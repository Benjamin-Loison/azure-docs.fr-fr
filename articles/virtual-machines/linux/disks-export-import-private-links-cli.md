---
title: Azure CLI – Restreindre l’accès en importation/exportation à des disques managés avec des liaisons privées
description: Activez des liaisons privées pour vos disques managés avec Azure CLI. Exportez et importez des disques de manière sécurisée, uniquement au sein de votre réseau virtuel.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 07/15/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: fa4bdcd3e9eccb3c8911dda758de431e7c459ce2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236748"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links"></a>Azure CLI – Restreindre l’accès en importation/exportation pour des disques managés avec des liaisons privées

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques flexibles 

Vous pouvez utiliser des [points de terminaison privés](../../private-link/private-endpoint-overview.md) pour restreindre l’exportation et l’importation de disques managés, et accéder de manière sécurisée aux données via une [liaison privée](../../private-link/private-link-overview.md) à partir de clients sur votre réseau virtuel Azure. Le point de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour votre service de disques managés. Le trafic réseau entre clients sur leurs réseau virtuel et disques managés transite uniquement sur le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, éliminant ainsi toute exposition à partir de l’Internet public.

Pour utiliser des liaisons privées pour exporter/importer des disques managés, commencez par créer une ressource d’accès au disque et par la lier à un réseau virtuel dans le même abonnement en créant un point de terminaison privé. Associez ensuite un disque ou un instantané à une instance d’accès au disque. Enfin, définissez la propriété NetworkAccessPolicy du disque ou de l’instantané avec `AllowPrivate`. De cette façon, l’accès est limité au réseau virtuel. 

Vous pouvez définir la propriété NetworkAccessPolicy avec `DenyAll` pour empêcher toute personne d’exporter les données d’un disque ou d’un instantané. La valeur par défaut de la propriété NetworkAccessPolicy est `AllowAll`.

## <a name="limitations"></a>Limites

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Se connecter à votre abonnement et définir vos variables

```azurecli-interactive
subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Créer un accès au disque à l’aide d’Azure CLI
```azurecli
az disk-access create -n $diskAccessName -g $resourceGroupName -l $region

diskAccessId=$(az disk-access show -n $diskAccessName -g $resourceGroupName --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Création d'un réseau virtuel

Les stratégies réseau, comme celles des groupes de sécurité réseau (NSG), ne sont pas applicables aux points de terminaison privés. Pour déployer un point de terminaison privé sur un sous-réseau donné, vous devez configurer un paramètre de désactivation explicite dans ce sous-réseau. 

```azurecli
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Désactiver les stratégies Private Endpoint du sous-réseau

Azure déploie les ressources sur un sous-réseau au sein d’un réseau virtuel. Vous devez donc mettre à jour le sous-réseau pour désactiver les stratégies réseau de point de terminaison privé. 

```azurecli
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Créer un point de terminaison privé pour l’objet d’accès au disque

```azurecli
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>Configurer la zone DNS privée

Créez une zone DNS privée pour le domaine Stockage Blob, créez un lien d’association avec le réseau virtuel et créez un groupe de zones DNS pour associer le point de terminaison privé à la zone DNS privée. 

```azurecli
az network private-dns zone create --resource-group $resourceGroupName \
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```

## <a name="create-a-disk-protected-with-private-links"></a>Créer un disque protégé avec des liens privés
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
diskName=yourDiskName
diskSkuName=Standard_LRS
diskSizeGB=128

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az disk create -n $diskName \
-g $resourceGroupName \
-l $region \
--size-gb $diskSizeGB \
--sku $diskSkuName \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Créer un instantané d’un disque protégé par des liaisons privées
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
sourceDiskName=yourSourceDiskForSnapshot
snapshotNameSecuredWithPL=yourSnapshotName

diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az snapshot create -n $snapshotNameSecuredWithPL \
-g $resourceGroupName \
-l $region \
--source $diskId \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="next-steps"></a>Étapes suivantes

- Charger un disque dur virtuel dans Azure ou copier un disque managé dans une autre région - Module [Azure CLI](disks-upload-vhd-to-managed-disk-cli.md) ou [Azure PowerShell](../windows/disks-upload-vhd-to-managed-disk-powershell.md)
- Télécharger un disque dur virtuel - [Windows](../windows/download-vhd.md) ou [Linux](download-vhd.md)
- [Questions fréquentes (FAQ) sur les liaisons privées](../faq-for-disks.yml)
- [Exporter/copier des instantanés managée en tant que disque dur virtuel vers un compte de stockage dans une région différente avec CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd)
