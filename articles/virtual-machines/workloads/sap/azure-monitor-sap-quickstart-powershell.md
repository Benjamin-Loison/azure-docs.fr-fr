---
title: Déployer Azure Monitor pour SAP Solutions avec Azure PowerShell
description: Déployer Azure Monitor pour SAP Solutions avec Azure PowerShell
author: sameeksha91
ms.author: sakhare
ms.topic: quickstart
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.devlang: azurepowershell
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: e86f29291e52909eb1a531079a008c6ebefc47f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008405"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>Démarrage rapide : Déployer Azure Monitor pour SAP Solutions avec Azure PowerShell

Cet article explique comment créer des ressources Azure Monitor pour SAP Solutions à l’aide du module PowerShell [Az.HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure).

> [!CAUTION]
> Azure Monitor pour SAP Solutions est actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service. Il n’est pas recommandé pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Spécifications

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

Si vous choisissez d’utiliser PowerShell en local, vous devez installer le module Azure PowerShell. Vous devez également vous connecter à votre compte Azure à l’aide de la cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Pour en savoir plus sur l’installation du module Az PowerShell, consultez [Installer Azure PowerShell](/powershell/azure/install-az-ps). Vous pouvez également choisir d’utiliser Cloud Shell. Pour plus d’informations sur Cloud Shell, consultez [Vue d’ensemble d’Azure Cloud Shell](../../../cloud-shell/overview.md).

> [!IMPORTANT]
> Tant que le module PowerShell **Az.HanaOnAzure** est en préversion, vous devez l’installer séparément à l’aide de l’applet de commande `Install-Module`. Quand ce module PowerShell sera en disponibilité générale, il fera partie intégrante des versions futures du module PowerShell Az et sera disponible en mode natif dans Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Si vous avez plusieurs abonnements Azure, sélectionnez l’abonnement approprié dans lequel les ressources doivent être facturées. Sélectionnez un abonnement spécifique avec l’applet de commande [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources Azure](../../../azure-resource-manager/management/overview.md) avec le cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe.

L’exemple suivant crée un groupe de ressources portant le nom spécifié à l’emplacement indiqué.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>Moniteur SAP

Pour créer un moniteur SAP, utilisez l’applet de commande [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor). L’exemple suivant crée un moniteur SAP pour l’abonnement, le groupe de ressources et le nom de ressource spécifiés.

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

Pour récupérer les propriétés d’un moniteur SAP, utilisez l’applet de commande [Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor). L’exemple suivant obtient les propriétés d’un moniteur SAP pour l’abonnement, le groupe de ressources et le nom de ressource spécifiés.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Instance du fournisseur

Pour créer une instance de fournisseur, utilisez l’applet de commande [New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance). L’exemple suivant crée une instance de fournisseur pour l’abonnement, le groupe de ressources et le nom de ressource spécifiés.

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

Pour récupérer les propriétés d’une instance de fournisseur, utilisez l’applet de commande [Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance). L’exemple suivant obtient les propriétés de : 
- Instance de fournisseur pour l’abonnement spécifié
- Groupe de ressources
- Nom de SapMonitor
- Nom de la ressource

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez pas besoin des ressources que vous avez créées dans cet article, vous pouvez les supprimer en exécutant l’exemple suivant.

### <a name="delete-the-provider-instance"></a>Supprimer l’instance de fournisseur

Pour supprimer une instance de fournisseur, vous utilisez l’applet de commande [Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance). L’exemple suivant supprime une instance de fournisseur pour l’abonnement, le groupe de ressources, le nom SapMonitor et le nom de ressource spécifiés.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>Supprimer le moniteur SAP

Pour supprimer un moniteur SAP, utilisez l’applet de commande [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor). L’exemple suivant supprime un moniteur SAP pour l’abonnement, le groupe de ressources et le nom de moniteur spécifiés.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>Supprimer le groupe de ressources

> [!CAUTION]
> L’exemple suivant supprime le groupe de ressources spécifié et toutes les ressources qu’il contient.
> Si des ressources en dehors du cadre de cet article existent dans le groupe de ressources spécifié, elles seront également supprimées.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail Azure Monitor pour SAP Solutions.

> [!div class="nextstepaction"]
> [Superviser SAP sur Azure](monitor-sap-on-azure.md)
