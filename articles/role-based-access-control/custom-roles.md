---
title: Rôles personnalisés Azure - Contrôle d’accès en fonction du rôle (RBAC) Azure
description: Découvrez comment créer des rôles personnalisés Azure avec le contrôle d’accès en fonction du rôle (RBAC) Azure pour une gestion affinée des accès aux ressources Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 08/27/2021
ms.author: rolyon
ms.openlocfilehash: 31da46a7eb6a021a14641d13f9c76ab7406f9b94
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363413"
---
# <a name="azure-custom-roles"></a>Rôles personnalisés Azure

> [!IMPORTANT]
> L’ajout d’un groupe d’administration à `AssignableScopes` est actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si les [rôles intégrés Azure](built-in-roles.md) ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres rôles personnalisés. Tout comme les rôles intégrés, vous pouvez attribuer des rôles personnalisés à des utilisateurs, à des groupes et à des principaux de service dans des étendues de groupe d’administration (en préversion uniquement), d’abonnement et de groupe de ressources.

Les rôles personnalisés peuvent être partagés entre abonnements qui font confiance au même annuaire Azure AD. Il existe une limite de **5 000** rôles personnalisés par annuaire. (Pour Azure Allemagne et Azure Chine 21Vianet, la limite est de 2 000 rôles personnalisés). Vous pouvez créer des rôles personnalisés à l’aide du portail Azure, d’Azure PowerShell, d’Azure CLI ou de l’API REST.

## <a name="steps-to-create-a-custom-role"></a>Procédure de création d’un rôle personnalisé

Voici la procédure de base pour créer un rôle personnalisé.

1. Déterminez les autorisations nécessaires.

    Lorsque vous créez un rôle personnalisé, vous devez connaître les actions qui sont disponibles pour définir vos autorisations. En règle générale, vous démarrez avec un rôle intégré existant, puis vous le modifiez selon vos besoins. Vous ajouterez les actions aux propriétés `Actions` ou `NotActions` de la [définition de rôle](role-definitions.md). Si vous avez des actions sur les données, vous les ajouterez aux propriétés `DataActions` ou `NotDataActions`.

    Pour plus d’informations, consultez la section suivante [Déterminer les autorisations nécessaires](#how-to-determine-the-permissions-you-need).

1. Choisissez la méthode de création de votre choix pour le rôle personnalisé.

    Vous pouvez créer des rôles personnalisés à l’aide du [portail Azure](custom-roles-portal.md), d’[Azure PowerShell](custom-roles-powershell.md), d’[Azure CLI](custom-roles-cli.md) ou de l’[API REST](custom-roles-rest.md).

1. Créez le rôle personnalisé.

    Le moyen le plus simple consiste à utiliser le Portail Azure. Pour connaître les étapes de création d’un rôle personnalisé à l’aide du Portail Azure, consultez [Créer ou mettre à jour des rôles personnalisés Azure à l’aide du portail Azure](custom-roles-portal.md).

1. Testez le rôle personnalisé.

    Une fois que vous avez votre rôle personnalisé, vous devez le tester pour vérifier qu’il fonctionne comme prévu. Si vous avez besoin d’effectuer des ajustements ultérieurement, vous pouvez mettre à jour le rôle personnalisé.

## <a name="how-to-determine-the-permissions-you-need"></a>Déterminer les autorisations nécessaires

Azure dispose de plusieurs milliers d’autorisations que vous pouvez inclure dans votre rôle personnalisé. Voici quelques moyens de déterminer les autorisations à ajouter à un rôle personnalisé :

- Examinez les [rôles intégrés](built-in-roles.md) existants.

    Vous souhaiterez peut-être modifier un rôle existant ou combiner des autorisations utilisées dans plusieurs rôles.

- Dressez la liste des services Azure auxquels vous souhaitez accorder l’accès.

- Déterminez les [fournisseurs de ressources correspondant aux services Azure](../azure-resource-manager/management/azure-services-resource-providers.md).

    Les services Azure exposent leurs fonctionnalités et leurs autorisations par le biais de [fournisseurs de ressources](../azure-resource-manager/management/overview.md). Par exemple, le fournisseur de ressources Microsoft.Compute fournit des ressources de machines virtuelles, tandis que le fournisseur de ressources Microsoft.Billing fournit des ressources d’abonnement et de facturation. Connaître les fournisseurs de ressources peut vous aider à limiter et à déterminer les autorisations dont vous avez besoin pour votre rôle personnalisé.

    Lorsque vous créez un rôle personnalisé à l’aide du portail Azure, vous pouvez également déterminer les fournisseurs de ressources en recherchant des mots clés. Cette fonctionnalité de recherche est décrite dans la section [Créer ou mettre à jour des rôles personnalisés Azure à l’aide du portail Azure](custom-roles-portal.md#step-4-permissions).

    ![Volet Ajouter des autorisations avec fournisseur de ressources](./media/shared/add-permissions-provider.png)

- Recherchez dans [Autorisations disponibles](resource-provider-operations.md) les autorisations que vous souhaitez inclure.

    Lorsque vous créez un rôle personnalisé à l’aide du portail Azure, vous pouvez rechercher des autorisations par mot clé. Par exemple, vous pouvez rechercher des autorisations d’*ordinateur virtuel* ou de *facturation*. Vous pouvez également télécharger toutes les autorisations sous la forme d’un fichier CSV, puis lancer une recherche dans ce fichier. Cette fonctionnalité de recherche est décrite dans la section [Créer ou mettre à jour des rôles personnalisés Azure à l’aide du portail Azure](custom-roles-portal.md#step-4-permissions).

    ![Liste Ajouter des autorisations](./media/shared/add-permissions-list.png)

## <a name="custom-role-example"></a>Exemple de rôle personnalisé

Voici ce à quoi ressemble un rôle personnalisé tel qu’il apparaît au format JSON dans Azure PowerShell. Ce rôle personnalisé peut être utilisé pour surveiller et redémarrer des machines virtuelles.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

L’exemple suivant montre le même rôle personnalisé que celui qui est affiché à l’aide d’Azure CLI.

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="custom-role-properties"></a>Propriétés du rôle personnalisé

Le tableau suivant décrit ce que signifient les propriétés de rôle personnalisées.

| Propriété | Obligatoire | Type | Description |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Oui | String | Nom complet du rôle personnalisé. Si une définition de rôle est une ressource de niveau groupe d'administration ou abonnement, elle peut cependant être utilisée dans plusieurs abonnements partageant le même annuaire Azure AD. Ce nom d’affichage doit être unique dans l’étendue de l’annuaire Azure AD. Peut inclure des lettres, des chiffres, des espaces et des caractères spéciaux. Nombre maximal de caractères : 128. |
| `Id`</br>`name` | Oui | String | ID unique du rôle personnalisé. Pour Azure PowerShell et Azure CLI, cet ID est généré automatiquement lorsque vous créez un nouveau rôle. |
| `IsCustom`</br>`roleType` | Oui | String | Indique s’il s’agit d’un rôle personnalisé. À définir sur `true` ou `CustomRole` pour les rôles personnalisés. À définir sur `false` ou `BuiltInRole` pour les rôles intégrés. |
| `Description`</br>`description` | Oui | String | Description du rôle personnalisé. Peut inclure des lettres, des chiffres, des espaces et des caractères spéciaux. Nombre maximal de caractères : 1 024. |
| `Actions`</br>`actions` | Oui | String[] | Tableau de chaînes qui spécifie les actions du plan de contrôle que le rôle autorise. Pour plus d’informations, voir [Actions](role-definitions.md#actions). |
| `NotActions`</br>`notActions` | Non | String[] | Tableau de chaînes qui spécifie les actions du plan de contrôle exclues des `Actions` autorisées. Pour plus d’informations, voir [NotActions](role-definitions.md#notactions). |
| `DataActions`</br>`dataActions` | Non | String[] | Tableau de chaînes qui spécifie les actions du plan de données que le rôle autorise sur vos données au sein de cet objet. Si vous créez un rôle personnalisé avec `DataActions`, ce rôle ne peut pas être affecté au niveau de l’étendue du groupe d’administration. Pour plus d’informations, consultez [DataActions](role-definitions.md#dataactions). |
| `NotDataActions`</br>`notDataActions` | Non | String[] | Tableau de chaînes qui spécifie les actions du plan de données exclues des `DataActions` autorisées. Pour plus d’informations, consultez [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes`</br>`assignableScopes` | Oui | String[] | Tableau de chaînes qui spécifie les étendues pour lesquelles le rôle personnalisé est disponible à des fins d’attribution. Vous ne pouvez définir qu’un seul groupe d’administration dans `AssignableScopes` d’un rôle personnalisé. L’ajout d’un groupe d’administration à `AssignableScopes` est actuellement en préversion. Pour plus d’informations, voir [AssignableScopes](role-definitions.md#assignablescopes). |

Les chaînes d’autorisation ne respectent pas la casse. Lorsque vous créez vos rôles personnalisés, la convention est de faire correspondre la casse que vous voyez pour les permissions dans [Opérations de fournisseur de ressources Azure](resource-provider-operations.md).

## <a name="wildcard-permissions"></a>Autorisations de caractères génériques

`Actions`, `NotActions`, `DataActions` et `NotDataActions` prennent en charge les caractères génériques (`*`) pour définir des autorisations. Un caractère générique (`*`) étend une autorisation à tout ce qui correspond à la chaîne d’action que vous fournissez. Par exemple, supposons que vous souhaitiez ajouter toutes les autorisations relatives à Azure Cost Management et aux exportations. Vous pouvez ajouter toutes ces chaînes d’action :

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Au lieu d’ajouter toutes ces chaînes, vous pouvez simplement ajouter une chaîne de caractères génériques. Par exemple, la chaîne de caractères génériques suivante équivaut aux cinq chaînes précédentes. Cela inclut également toutes les futures autorisations d’exportation qui pourraient être ajoutées.

```
Microsoft.CostManagement/exports/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Qui peut créer, supprimer, mettre à jour ou afficher un rôle personnalisé

Tout comme pour les rôles intégrés, la propriété `AssignableScopes` spécifie les étendues pour lesquelles le rôle est disponible à des fins d’attribution. La propriété `AssignableScopes` pour un rôle personnalisé contrôle également qui peut créer, supprimer, mettre à jour ou afficher le rôle personnalisé.

| Tâche | Action | Description |
| --- | --- | --- |
| Créer/supprimer un rôle personnalisé | `Microsoft.Authorization/ roleDefinitions/write` | Les utilisateurs ayant accès à cette action sur toutes les étendues `AssignableScopes` du rôle personnalisé peuvent créer (ou supprimer) des rôles personnalisés utilisables dans ces étendues. Il s’agit, par exemple, des [Propriétaires](built-in-roles.md#owner) et [Administrateurs de l’accès utilisateur](built-in-roles.md#user-access-administrator) des groupe d’administration, des abonnements et des groupes de ressources. |
| Mettre à jour un rôle personnalisé | `Microsoft.Authorization/ roleDefinitions/write` | Les utilisateurs ayant accès à cette action sur toutes les étendues `AssignableScopes` du rôle personnalisé peuvent mettre à jour des rôles personnalisés dans ces étendues. Il s’agit, par exemple, des [Propriétaires](built-in-roles.md#owner) et [Administrateurs de l’accès utilisateur](built-in-roles.md#user-access-administrator) des groupe d’administration, des abonnements et des groupes de ressources. |
| Afficher un rôle personnalisé | `Microsoft.Authorization/ roleDefinitions/read` | Les utilisateurs ayant accès à cette action dans une étendue peuvent afficher les rôles personnalisés disponibles pour attribution dans cette étendue. Tous les rôles intégrés permettent que les rôles personnalisés soient disponibles pour attribution. |

## <a name="custom-role-limits"></a>Limites des rôles personnalisés

La liste suivante décrit les limites des rôles personnalisés.

- Chaque annuaire peut avoir jusqu’à **5 000** rôles personnalisés.
- Azure Allemagne et Azure Chine 21Vianet peuvent avoir jusqu’à 2 000 rôles personnalisés pour chaque annuaire.
- Vous ne pouvez pas définir `AssignableScopes` à l’étendue racine (`"/"`).
- Vous ne pouvez pas utiliser de caractères génériques (`*`) dans `AssignableScopes`. Cette restriction de caractère générique permet de garantir qu’un utilisateur ne peut pas obtenir l’accès à une étendue en mettant à jour la définition de rôle.
- Vous ne pouvez définir qu’un seul groupe d’administration dans `AssignableScopes` d’un rôle personnalisé. L’ajout d’un groupe d’administration à `AssignableScopes` est actuellement en préversion.
- Vous ne pouvez avoir qu’un seul caractère générique dans une chaîne d’action.
- Les rôles personnalisés avec `DataActions` ne peuvent pas être attribués dans l’étendue du groupe d’administration.
- Azure Resource Manager ne valide pas le groupe d’administration existant dans l’étendue attribuable de la définition de rôle.

Pour plus d’informations sur les rôles personnalisés et les groupes d’administration, consultez [Présentation des groupes d’administration Azure](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment).

## <a name="input-and-output-formats"></a>Formats d’entrée et de sortie

Pour créer un rôle personnalisé à l’aide de la ligne de commande, vous utilisez généralement JSON afin de spécifier les propriétés souhaitées pour le rôle personnalisé. Selon les outils que vous utilisez, les formats d’entrée et de sortie semblent légèrement différents. Cette section répertorie les formats d’entrée et de sortie en fonction de l’outil.

### <a name="azure-powershell"></a>Azure PowerShell

Pour créer un rôle personnalisé à l’aide d’Azure PowerShell, vous devez fournir l’entrée suivante.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Pour mettre à jour un rôle personnalisé à l’aide d’Azure PowerShell, vous devez fournir l’entrée suivante. Notez que la propriété `Id` a été ajoutée. 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

L’exemple suivant illustre la sortie lorsque vous répertoriez un rôle personnalisé à l’aide d’Azure PowerShell et de la commande [ConvertTo-Json](/powershell/module/microsoft.powershell.utility/convertto-json). 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>Azure CLI

Pour créer ou mettre à jour un rôle personnalisé à l’aide de l’interface de ligne de commande Azure, vous devez fournir l’entrée suivante. Ce format est le même que lorsque vous créez un rôle personnalisé à l’aide d’Azure PowerShell.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

L’exemple suivant illustre la sortie lorsque vous répertoriez un rôle personnalisé à l’aide d’Azure CLI.

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>API REST

Pour créer ou mettre à jour un rôle personnalisé à l’aide de l’API REST, vous devez fournir l’entrée suivante. Ce format généré est le même que lorsque vous créez un rôle personnalisé à l’aide du Portail Azure.

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

L’exemple suivant illustre la sortie lorsque vous répertoriez un rôle personnalisé à l’aide de l’API REST.

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer un rôle personnalisé Azure à l’aide d’Azure PowerShell](tutorial-custom-role-powershell.md)
- [Tutoriel : Créer un rôle personnalisé Azure à l’aide d’Azure CLI](tutorial-custom-role-cli.md)
- [Comprendre les définitions de rôles Azure](role-definitions.md)
- [Résoudre les problèmes liés à Azure RBAC](troubleshooting.md)
