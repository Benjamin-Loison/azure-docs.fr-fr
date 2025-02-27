---
title: Modifier les paramètres de destination à l'occasion du déplacement de machines virtuelles Azure entre différentes régions avec Azure Resource Mover
description: Apprenez à modifier les paramètres de destination à l'occasion du déplacement de machines virtuelles Azure entre différentes régions avec Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 54c2594e823e2e78f7a3fd62afba1d0d023ff347
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048118"
---
# <a name="modify-destination-settings"></a>Modifier les paramètres de destination

Cet article explique comment modifier les paramètres de destination à l'occasion du déplacement de ressources entre différentes régions avec [Azure Resource Mover](overview.md).


## <a name="modify-vm-settings"></a>Modifier les paramètres de machine virtuelle

Vous pouvez modifier les paramètres de destination lorsque vous déplacez des machines virtuelles Azure et les ressources associées. Nous recommandons les actions suivantes :

- De ne modifier les paramètres de destination qu’une fois la collection de déplacement validée. Toutefois :
    - Si vous déplacez la ressource source, vous pouvez généralement modifier ces paramètres tant que vous n’avez pas lancé le processus de déplacement.
    - Si vous attribuez une ressource existante dans la région source, vous pouvez modifier les paramètres de destination tant que la validation du déplacement n'est pas terminée.
- De modifier les paramètres avant de préparer les ressources, car certaines propriétés de destination risquent de ne plus pouvoir être modifiées à l’issue de la préparation.

### <a name="settings-you-can-modify"></a>Paramètres que vous pouvez modifier

Les paramètres de configuration que vous pouvez modifier sont résumés dans le tableau.

**Ressource** | **Options** 
--- | --- 
**Nom de la machine virtuelle** | Options de destination :<br/><br/> - Créer une nouvelle machine virtuelle sous le même nom dans la région de destination<br/><br/> - Créer une nouvelle machine virtuelle sous un nom différent dans la région de destination<br/><br/> - Utiliser une machine virtuelle existante dans la région de destination<br/><br/> Si vous créez une nouvelle machine virtuelle, mis à part les paramètres que vous modifiez, la nouvelle machine virtuelle de destination hérite des mêmes paramètres que la source.
**Zone de disponibilité de la machine virtuelle** | Zone de disponibilité dans laquelle la machine virtuelle de destination sera placée. Sélectionnez **Non applicable** si vous ne voulez pas modifier les paramètres de la source ou si vous ne souhaitez pas placer la machine virtuelle dans une zone de disponibilité.
**Référence de la machine virtuelle** | [Type de machine virtuelle](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (disponible dans la région de destination) qui sera utilisé pour la machine virtuelle de destination.<br/><br/> La taille de la machine virtuelle de destination sélectionnée ne doit pas être inférieure à celle de la machine virtuelle source.
**Groupe à haute disponibilité de machines virtuelles** | Groupe à haute disponibilité dans lequel la machine virtuelle de destination sera placée. Sélectionnez **Non applicable** si vous ne voulez pas modifier les paramètres de la source ou si vous ne souhaitez pas placer la machine virtuelle dans un groupe à haute disponibilité.
**Coffre de clés de machine virtuelle** | Coffre de clés associé lorsque Azure Disk Encryption est activé sur une machine virtuelle.
**Jeu de chiffrement de disque** | Jeu de chiffrement de disque associé si la machine virtuelle utilise une clé gérée par le client pour le chiffrement côté serveur.
**Groupe de ressources** | Groupe de ressources dans lequel la machine virtuelle de destination sera placée.
**Ressources réseau** | Options pour les interfaces réseau, les réseaux virtuels et les groupes de sécurité/interfaces réseau :<br/><br/> - Créer une nouvelle ressource sous le même nom dans la région de destination<br/><br/> - Créer une nouvelle ressource sous un nom différent dans la région de destination<br/><br/> - Utiliser une ressource réseau existante dans la région de destination<br/><br/> Si vous créez une nouvelle ressource de destination, mis à part les paramètres que vous modifiez, elle hérite des mêmes paramètres que la ressource source.
**Nom, référence SKU et zone de l’adresse IP publique** | Nom, [référence SKU](../virtual-network/ip-services/public-ip-addresses.md#sku) et [zone](../virtual-network/ip-services/public-ip-addresses.md#standard) des adresses IP publiques standard.<br/><br/> Si vous voulez qu’il soit redondant interzone, définissez-le comme étant **Redondant interzone**.
**Nom, référence SKU et zone de l’équilibreur de charge** | Nom, référence SKU (De base ou Standard) et zone de l’équilibreur de charge.<br/><br/> Nous vous recommandons d’utiliser la référence SKU Standard.<br/><br/> Si vous voulez qu’il soit redondant interzone, spécifiez **Redondant interzone**.
**Dépendances des ressources** | Options pour chaque dépendance :<br/><br/>- La ressource utilise des ressources dépendantes de la source qui seront déplacées vers la région de destination.<br/><br/> - La ressource utilise d'autres ressources dépendantes situées dans la région de destination. Dans ce cas, vous pouvez effectuer un choix parmi des ressources similaires dans la région de destination.

### <a name="edit-vm-destination-settings"></a>Modifier les paramètres de destination des machines virtuelles

Si vous ne souhaitez déplacer des ressources dépendantes de la région source vers la destination, d’autres possibilités s’offrent à vous :

- Créez une nouvelle ressource dans la région de destination. À moins de spécifier des paramètres différents, la nouvelle ressource aura les mêmes paramètres que la ressource source.
- Utilisez une ressource existante dans la région de destination.

Le comportement exact dépend du type de ressource. [Apprenez-en davantage](modify-target-settings.md) sur la modification des paramètres de destination.

Pour modifier les paramètres de destination d'une ressource, vous devez utiliser l'entrée **Configuration de la destination** dans la collection de déplacement de la ressource. 

Pour modifier un paramètre : 

1. Sur la page **Entre les régions** > colonne **Configuration de la destination**, cliquez sur le lien correspondant à l'entrée de la ressource.
2. Sous **Paramètres de configuration**, vous pouvez créer une nouvelle machine virtuelle dans la région de destination.
3. Attribuez une nouvelle zone de disponibilité, un groupe à haute disponibilité ou une référence SKU à la machine virtuelle de destination. **Zone de disponibilité** et référence **SKU**.

Les modifications ne sont apportées qu’à la ressource que vous modifiez. Vous devez mettre à jour toute ressource dépendante séparément.


## <a name="modify-sql-settings"></a>Modifier les paramètres SQL

Au moment de déplacer des ressources Azure SQL Database, vous pouvez modifier les paramètres de destination pour le déplacement. 

- Pour la base de données SQL :
    - Nous vous recommandons de modifier les paramètres de configuration de la destination avant de préparer le déplacement.
    - Vous pouvez modifier les paramètres de la base de données de destination et la redondance de zone pour la base de données.
- Pour les pools élastiques :
    -  Vous pouvez modifier la configuration de la destination à tout moment avant de lancer le déplacement.
    - Vous pouvez modifier le pool élastique de destination et la redondance de zone pour le pool. 

### <a name="sql-settings-you-can-modify"></a>Paramètres SQL que vous pouvez modifier

**Paramètre** | **Base de données SQL** | **Pool élastique**
--- | --- | ---
**Nom** | Créer une nouvelle base de données sous le même nom dans la région de destination<br/><br/> Créer une nouvelle base de données sous un nom différent dans la région de destination<br/><br/> Utiliser une base de données existante dans la région de destination | Créer un nouveau pool élastique sous le même nom dans la région de destination<br/><br/> Créer un nouveau pool élastique sous un nom différent dans la région de destination<br/><br/> Utiliser un pool élastique existant dans la région de destination
**Redondance de zone** | Pour passer d’une région qui prend en charge la redondance de zone à une région qui ne la prend pas en charge, tapez **Désactiver** dans le paramètre de zone.<br/><br/> Pour passer d’une région qui ne prend pas en charge la redondance de zone à une région qui la prend en charge, tapez **Activer** dans le paramètre de zone. | Pour passer d’une région qui prend en charge la redondance de zone à une région qui ne la prend pas en charge, tapez **Désactiver** dans le paramètre de zone.<br/><br/> Pour passer d’une région qui ne prend pas en charge la redondance de zone à une région qui la prend en charge, tapez **Activer** dans le paramètre de zone.

### <a name="edit-sql-destination-settings"></a>Modifier les paramètres de destination SQL

Pour modifier les paramètres de destination d'une ressource Azure SQL Database, procédez comme suit : 

1. Dans **Entre les régions**, pour la ressource que vous souhaitez modifier, cliquez sur l'entrée **Configuration de la destination**.
2. Dans **Paramètres de configuration**, spécifiez les paramètres de destination résumés dans le tableau ci-dessus.


## <a name="modify-settings-in-powershell"></a>Modifier les paramètres dans PowerShell

Vous pouvez modifier les paramètres dans PowerShell.

1)  Récupérez la ressource de déplacement dont vous souhaitez modifier les propriétés. Par exemple, pour récupérer une machine virtuelle, exécutez :

    ```azurepowershell
    $moveResourceObj = Get-AzResourceMoverMoveResource -MoveCollectionName "PS-centralus-westcentralus-demoRMS1" -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PSDemoVM"
    ```
2)  Copiez le paramètre de ressource sur un objet de paramètre de ressource cible.

    ```azurepowershell
    $TargetResourceSettingObj = $moveResourceObj.ResourceSetting
    ```

3)  Définissez le paramètre dans l’objet de paramètre de ressource cible. Par exemple, pour modifier le nom de la machine virtuelle de destination :

    ```azurepowershell
    $TargetResourceSettingObj.TargetResourceName="PSDemoVM-target"
    ```

4)  Mettez à jour les paramètres de destination de la ressource de déplacement. Dans cet exemple, nous changeons le nom de la machine virtuelle *PSDemoVM* en *PSDemoVMTarget*.

    ```azurepowershell
    Update-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $TargetResourceSettingObj
    ```
    **Sortie**
    ![Sortie de texte après la modification des paramètres de destination](./media/modify-target-settings/update-settings.png)


## <a name="next-steps"></a>Étapes suivantes

[Déplacer une machine virtuelle Azure](tutorial-move-region-virtual-machines.md) dans une autre région.