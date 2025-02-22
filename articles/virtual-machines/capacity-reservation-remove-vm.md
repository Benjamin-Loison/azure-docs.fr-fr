---
title: Supprimer une association de machines virtuelles d’un groupe de réservations de capacité (préversion)
description: Découvrez comment supprimer une machine virtuelle d’un groupe de réservations de capacité.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: f0c84e4b44218aa4f7659376251d1931ffc9516b
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063618"
---
# <a name="remove-a-vm-association-from-a-capacity-reservation-group-preview"></a>Supprimer une association de machines virtuelles d’un groupe de réservations de capacité (préversion)

Cet article vous guide tout au long des étapes de suppression d’une association de machines virtuelles à un groupe de réservations de capacité. Pour en savoir plus sur les réservations de capacité, consultez l'[article vue d’ensemble](capacity-reservation-overview.md). 

Étant donné que la machine virtuelle et la réservation de capacité sous-jacente occupent la capacité logiquement, Azure impose certaines contraintes sur ce processus afin d’éviter des états d’allocation ambigus et des erreurs inattendues.  

Vous pouvez modifier l’association de deux façons : 
- Option 1 : libérer la machine virtuelle, modifier la propriété de groupe de réservation de capacité et éventuellement redémarrer la machine virtuelle
- Option 2 : mettre à jour la quantité réservée à zéro, puis modifier la propriété du groupe de réservations de capacité

> [!IMPORTANT]
> La réservation de capacité est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deallocate-the-vm"></a>Libérer la machine virtuelle

La première option est de libérer la machine virtuelle, modifier la propriété de groupe de réservation de capacité et éventuellement redémarrer la machine virtuelle. 

### <a name="api"></a>[API](#tab/api1)

1. Libérer la machine virtuelle

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/deallocate?api-version=2021-04-01
    ```

1. Mettre à jour la machine virtuelle pour supprimer l’association avec le groupe de réservations de capacité
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/update?api-version=2021-04-01
    ```
    Dans le corps de la demande, définissez la propriété `capacityReservationGroup` sur zéro pour supprimer l’association de la machine virtuelle au groupe :

    ```json
     {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id":null
            }
        }
    }
    }
    ```

### <a name="portal"></a>[Portail](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Allez vers votre machine virtuelle, puis choisissez **Vue d’ensemble**
1. Sélectionnez **Arrêter** 
    1. Vous savez que votre machine virtuelle est libérée lorsque l’état passe à *Arrêté (libéré)*
    1. À ce stade du processus, la machine virtuelle est toujours associée au groupe de réservations de capacité, qui est reflétée dans la propriété `virtualMachinesAssociated` de la réservation de capacité 
1. Sélectionnez **Configuration**.
1. Définissiez la valeur **Groupe de réservation de capacité** sur *Aucun*
    - La machine virtuelle n’est plus associée au groupe de réservations de capacité 

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli1)

1. Libérez la machine virtuelle

    ```azurecli-interactive
    az vm deallocate 
    -g myResourceGroup 
    -n myVM
    ```

    Vous savez que votre machine virtuelle est libérée lorsque l’état passe à **Arrêté (libéré)** .

1. Mettez à jour la machine virtuelle afin de supprimer l’association avec le groupe de réservations de capacité en définissant la propriété `capacity-reservation-group` sur None :

    ```azurecli-interactive
    az vm update 
    -g myresourcegroup 
    -n myVM 
    --capacity-reservation-group None
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. Libérez la machine virtuelle

    ```powershell-interactive
    Stop-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    ```

    Vous savez que votre machine virtuelle est libérée lorsque l’état passe à **Arrêté (libéré)** .

1. Mettez à jour la machine virtuelle afin de supprimer l’association avec le groupe de réservations de capacité en définissant la propriété `CapacityReservationGroupId` sur zéro :

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId $null
    ```

Pour en savoir plus, allez voir les commandes Azure PowerShell [Stop-AzVM](/powershell/module/az.compute/stop-azvm), [Get-AzVM](/powershell/module/az.compute/get-azvm), et [Update-AzVM](/powershell/module/az.compute/update-azvm).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="update-the-reserved-quantity-to-zero"></a>Mettez à jour la quantité réservée à zéro 

La deuxième option consiste à mettre à jour la quantité réservée à zéro, puis à modifier la propriété du groupe de réservations de capacité.

Cette option fonctionne bien lorsque la machine virtuelle ne peut pas être libérée et lorsqu’une réservation n’est plus nécessaire. Par exemple, vous pouvez créer une réservation de capacité pour garantir temporairement la capacité pendant un déploiement à grande échelle. Une fois l’opération terminée, la réservation n’est plus nécessaire. 

### <a name="api"></a>[API](#tab/api2)

1. Mettez à jour la quantité réservée à zéro 

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/CapacityReservations/{CapacityReservationName}?api-version=2021-04-01
    ```

    Dans le corps de la demande, ajoutez les éléments suivants :
    
    ```json
    {
    "sku":
        {
        "capacity": 0
        }
    }
    ```
    
    Notez également que la propriété `capacity` est définie sur 0 et plus.

1. Mettez à jour la machine virtuelle pour supprimer l’association avec le groupe de réservations de capacité

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}/update?api-version=2021-04-01
    ```

    Dans le corps de la demande, définissez la propriété `capacityReservationGroup` sur zéro pour supprimer l’association :
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id":null
            }
        }
    }
    } 
    ```

### <a name="portal"></a>[Portail](#tab/portal2)

<!-- no images necessary if steps are straightforward --> 

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Allez sur votre groupe de réservation de capacité et choisissez **Vue d’ensemble**
1. Choisissez **Réservations** 
1. En haut de la page, sélectionnez **Gérer les réservations** 
1. Sur le panneau *Gérer les réservations* :
    1. Entrez `0` dans le champs **Instances**
    1. Sélectionnez **Enregistrer**. 
1. Allez vers votre machine virtuelle, puis choisissez **Configuration**
1. Définissiez la valeur **Groupe de réservation de capacité** sur *Aucun*
    - La machine virtuelle n’est plus associée au groupe de réservations de capacité

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli2)

1. Mettez à jour la quantité réservée à zéro

   ```azurecli-interactive
   az capacity reservation update 
   -g myResourceGroup
   -c myCapacityReservationGroup 
   -n myCapacityReservation 
   --capacity 0
   ```

1. Mettez à jour la machine virtuelle afin de supprimer l’association avec le groupe de réservations de capacité en définissant la propriété `capacity-reservation-group` sur None :

    ```azurecli-interactive
    az vm update 
    -g myresourcegroup 
    -n myVM 
    --capacity-reservation-group None
    ```


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. Mettez à jour la quantité réservée à zéro

    ```powershell-interactive
    Update-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -CapacityToReserve 0
    ```

1. Mettez à jour la machine virtuelle afin de supprimer l’association avec le groupe de réservations de capacité en définissant la propriété `CapacityReservationGroupId` sur zéro :

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId $null
    ```

Pour en savoir plus, allez voir les commandes Azure PowerShell [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation), [Get-AzVM](/powershell/module/az.compute/get-azvm), et [Update-AzVM](/powershell/module/az.compute/update-azvm).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment associer un groupe identique à un groupe de réservations de capacité](capacity-reservation-associate-virtual-machine-scale-set.md)
