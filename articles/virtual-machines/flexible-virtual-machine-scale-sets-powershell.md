---
title: Créer des machines virtuelles dans un groupe identique Flexible à l’aide d’un modèle Azure PowerShell
description: Découvrez comment créer un groupe de machines virtuelles identiques en mode d’orchestration Flexible à l’aide de PowerShell.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 45f998dbba1ffac99fc8d491cb90694a76c11f98
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165739"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-powershell"></a>Créer des machines virtuelles dans un groupe identique Flexible à l’aide de PowerShell

**S’applique à :** :heavy_check_mark: Groupes identiques flexibles


Cet article décrit l’utilisation d’un modèle PowerShell pour créer un groupe identique de machines virtuelles en mode d’orchestration Flexible. Pour plus d’informations sur les groupes identiques Flexibles, consultez le [mode d’orchestration Flexible pour les groupes identiques de machines virtuelles](flexible-virtual-machine-scale-sets.md). 

> [!CAUTION]
> Le mode d’orchestration est défini lorsque vous créez le groupe identique et ne peut pas être modifié ou mis à jour ultérieurement.


## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.


## <a name="get-started-with-flexible-scale-sets"></a>Prise en main des groupes identiques Flexibles

Créez un groupe de machines virtuelles identiques Flexible à l’aide d’Azure PowerShell.

### <a name="add-multiple-vms-to-a-scale-set"></a>Ajouter plusieurs machines virtuelles à un groupe identique 

Dans l’exemple suivant, nous spécifions un profil de machine virtuelle (type de machine virtuelle, configuration de mise en réseau, type de stockage, etc.) et le nombre d’instances à créer (capacité de référence = 2). 

1. Créer des configurations d’adresse IP :

    ```azurepowershell-interactive
    $ipConfig = New-AzVmssIpConfig -Name "myIPConfig"
    -SubnetId "${vnetid}/subnets/default" `
    -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id
    ```

1. Création d’un fichier de configuration :

    L’objet de configuration stocke les informations de base pour la création d’un groupe identique.

    ```azurepowershell-interactive
    $vmssConfig = New-AzVmssConfig -Location $loc
    -SkuCapacity 2 -SkuName "Standard_DS1_v2"
    -OrchestrationMode 'Flexible' `
    -PlatformFaultDomainCount 1
    ```

1. Référencer une image de machine virtuelle à partir de la galerie :

    ```azurepowershell-interactive
    Set-AzVmssStorageProfile $vmssConfig -OsDiskCreateOption "FromImage"
    -ImageReferencePublisher "Canonical" -ImageReferenceOffer "UbuntuServer"
    -ImageReferenceSku "18.04-LTS" `
    -ImageReferenceVersion "latest"
    ```

1. Configurez les informations pour l’authentification auprès de la machine virtuelle :

    ```azurepowershell-interactive
    Set-AzVmssOsProfile $vmssConfig -AdminUsername $cred.UserName
    -AdminPassword $cred.Password -ComputerNamePrefix $vmname
    ```

1. Attachez le réseau virtuel à l’objet de configuration :

    ```azurepowershell-interactive
    Add-AzVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig
    -Name "network-config" -Primary $true
    -IPConfiguration $ipConfig `
    -NetworkApiVersion '2020-11-01'
    ```

1. Créez le groupe identique avec l’objet de configuration :

    Cette étape peut prendre quelques minutes. 

    ```azurepowershell-interactive
    New-AzVmss -ResourceGroupName $rgname
    -Name $vmssName `
    -VirtualMachineScaleSet $vmssConfig
    ```

### <a name="add-a-single-vm-to-a-scale-set"></a>Ajouter une machine virtuelle unique à un groupe identique

L’exemple suivant illustre la création d’un groupe identique Flexible sans profil de machine virtuelle, où le nombre de domaines par défaut est défini sur 1. Une machine virtuelle est créée, puis ajoutée au groupe identique Flexible.

1. Connectez-vous à Azure PowerShell et spécifiez l’abonnement et les variables pour le déploiement. 

    ```azurepowershell-interactive
    Connect-AzAccount
    Set-AzContext `
        -Subscription "00000000-0000-0000-0000-000000000" 
    
    $loc = "eastus" 
    $rgname = "myResourceGroupFlexible" 
    $vmssName = "myFlexibleVMSS" 
    $vmname = "myFlexibleVM"
    ```

1. Ne spécifiez pas de paramètres de profil de machine virtuelle comme les références de réseau ou de machine virtuelle.

    ```azurepowershell-interactive
    $VmssConfigWithoutVmProfile = new-azvmssconfig -location $loc -platformfaultdomain 1 `
    $VmssFlex = new-azvmss -resourcegroupname $rgname -vmscalesetname $vmssName -virtualmachinescaleset $VmssConfigWithoutVmProfile 
    ```
 
1. Ajouter une machine virtuelle au groupe identique Flexible.

    ```azurepowershell-interactive
    $vm = new-azvm -resourcegroupname $rgname -location $loc -name $vmname -credential $cred -domainnamelabel $domainName -vmssid $VmssFlex.id 
    ```


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Découvrez comment créer un groupe identique Flexible dans le portail Azure.](flexible-virtual-machine-scale-sets-portal.md)
