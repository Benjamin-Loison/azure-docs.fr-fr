---
title: 'Démarrage rapide : Créer une requête partagée avec Azure PowerShell'
description: Dans ce guide de démarrage rapide, vous allez suivre les étapes indiquées pour créer une requête partagée Resource Graph avec Azure PowerShell.
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: 8281e2bb2ff53fea28ff43a69e8b16cf9d1f7ddf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016123"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>Démarrage rapide : Créer une requête partagée Resource Graph avec Azure PowerShell

Cet article explique comment créer une requête partagée Azure Resource Graph à l’aide du module PowerShell [Az.ResourceGraph](/powershell/module/az.resourcegraph).

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Tant que le module PowerShell **Az.ResourceGraph** est en préversion, vous devez l’installer séparément à l’aide de l’applet de commande `Install-Module`.

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- Si vous avez plusieurs abonnements Azure, sélectionnez l’abonnement approprié dans lequel les ressources doivent être facturées. Sélectionnez un abonnement spécifique avec l’applet de commande [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Créer une requête partagée Resource Graph

Une fois le module PowerShell **Az.ResourceGraph** ajouté à l’environnement de votre choix, vous pouvez créer une requête partagée Resource Graph. La requête partagée est un objet Azure Resource Manager auquel vous pouvez accorder des autorisations ou que vous pouvez exécuter dans l’Explorateur Azure Resource Graph. La requête récapitule le nombre total de ressources regroupées par _emplacement_.

1. Créez un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) pour stocker la requête partagée Azure Resource Graph. Ce groupe de ressources est nommé `resource-graph-queries` et son emplacement est `westus2`.

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. Créez la requête partagée Azure Resource Graph à l’aide du module PowerShell **Az.ResourceGraph** et de la cmdlet [New-AzResourceGraphQuery](/powershell/module/az.resourcegraph/new-azresourcegraphquery) :

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. Listez les requêtes partagées dans le nouveau groupe de ressources. L’applet de commande [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) retourne un tableau de valeurs.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. Pour obtenir un seul résultat de requête partagée, utilisez `Get-AzResourceGraphQuery` avec son paramètre `Name`.

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez supprimer la requête partagée et le groupe de ressources Resource Graph de votre environnement Azure, vous pouvez le faire à l’aide des commandes suivantes :

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une requête partagée Resource Graph avec Azure PowerShell. Pour plus d’informations sur le langage Resource Graph, passez à la page des détails du langage de requête.

> [!div class="nextstepaction"]
> [Obtenir plus d’informations sur le langage de requête](./concepts/query-language.md)
