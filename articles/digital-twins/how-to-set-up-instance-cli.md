---
title: Configurer une instance et l’authentification (CLI)
titleSuffix: Azure Digital Twins
description: Découvrez comment configurer une instance du service Azure Digital Twins à l’aide de l’interface CLI
author: baanders
ms.author: baanders
ms.date: 10/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q2
ms.openlocfilehash: 615680552bc1854c650477f314c4ab14815dbc01
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000211"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Configurer une instance Azure Digital Twins et l’authentification (interface CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Cet article explique comment **configurer une nouvelle instance Azure Digital Twins**, notamment la création de l’instance et la configuration de l’authentification. À l’issue de cet article, vous aurez une instance Azure Digital Twins prête pour la programmation.

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Configurer une session Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Créer l’instance Azure Digital Twins

Dans cette section, vous allez **créer une nouvelle instance d’Azure Digital Twins** à l’aide de la commande Cloud Shell. Vous devrez fournir les éléments suivants :
* Un groupe de ressources où l’instance sera déployée. Si vous n’avez pas encore choisi un groupe de ressources existant, vous pouvez en créer un maintenant avec cette commande :
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Une région pour le déploiement. Pour connaître les régions qui prennent en charge Azure Digital Twins, visitez [Disponibilité des produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Un nom pour votre instance. Si votre abonnement a une autre instance Azure Digital Twins dans la région qui utilise déjà le nom spécifié, vous êtes invité à choisir un autre nom.

Utilisez ces valeurs dans la [commande az dt](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) suivante pour créer l’instance :

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> --resource-group <your-resource-group> --location <region>
```

### <a name="verify-success-and-collect-important-values"></a>Vérifier la réussite de l’exécution et collecter les valeurs importantes

Si l’instance a été créée avec succès, le résultat dans Cloud Shell ressemble à ceci, générant des informations sur la ressource que vous avez créée :

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Capture d’écran de la fenêtre Cloud Shell avec création réussie d’un groupe de ressources et d’une instance Azure Digital Twins dans le portail Azure":::

Notez les valeurs **hostName**, **name** et **resourceGroup** de l’instance Azure Digital Twins fournies dans la sortie. Ces valeurs sont toutes importantes et vous pouvez en avoir besoin quand vous continuez à travailler avec votre instance Azure Digital Twins pour configurer l’authentification et les ressources Azure associées. Si d’autres utilisateurs doivent programmer pour l’instance, vous devez partager ces valeurs avec eux.

> [!TIP]
> Vous pouvez afficher ces propriétés, ainsi que toutes les propriétés de votre instance, à tout moment en exécutant `az dt show --dt-name <your-Azure-Digital-Twins-instance>`.

Vous disposez maintenant d’une instance Azure Digital Twins opérationnelle. Ensuite, vous allez accorder les autorisations utilisateur Azure appropriées pour la gérer.

## <a name="set-up-user-access-permissions"></a>Configurer les autorisations d’accès utilisateur

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

### <a name="prerequisites-permission-requirements"></a>Configuration requise : Spécifications relatives aux autorisations

[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

### <a name="assign-the-role"></a>Attribuer le rôle

Pour accorder à un utilisateur les autorisations nécessaires pour gérer une instance Azure Digital Twins, vous devez lui attribuer le rôle **Propriétaire de données Azure Digital Twins** au sein de l’instance.

Utilisez la commande suivante pour attribuer le rôle (doit être exécutée par un utilisateur avec les [autorisations suffisantes](#prerequisites-permission-requirements) dans l’abonnement Azure). La commande nécessite de passer le *nom d’utilisateur principal* sur le compte Azure AD de l’utilisateur auquel le rôle doit être attribué. Dans la plupart des cas, cette valeur correspond à l’adresse e-mail de l’utilisateur sur le compte Azure AD.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

Les résultats de cette commande décrivent l’attribution de rôle que vous avez créée.

> [!NOTE]
> Si cette commande renvoie une erreur indiquant que l’interface CLI **ne trouve pas l’utilisateur ou le principal du service dans la base de données de graphes** :
>
> Attribuez le rôle en utilisant à la place l’*ID d’objet* de l’utilisateur. Cela peut se produire pour les utilisateurs disposant de [comptes Microsoft (MSA)](https://account.microsoft.com/account) personnels. 
>
> Utilisez la [page du portail Azure des utilisateurs Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) pour sélectionner le compte d’utilisateur et afficher ses détails. Copiez la valeur *ObjectID* de l’utilisateur :
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Capture d’écran de la page utilisateur dans le portail Azure mettant en évidence le GUID dans le champ « ID d’objet »" lightbox="media/includes/user-id.png":::
>
> Ensuite, répétez la commande 'role assignment list' en utilisant l’*ID d’objet* de l’utilisateur pour le paramètre `assignee` ci-dessus.

### <a name="verify-success"></a>Vérifier la réussite de l’exécution

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Vous disposez maintenant d’une instance Azure Digital Twins prête à l’emploi et des autorisations pour la gérer.

## <a name="next-steps"></a>Étapes suivantes

Testez les appels d’API REST individuels sur votre instance à l’aide des commandes CLI d’Azure Digital Twins : 
* [az dt reference](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)
* [Ensemble de commandes CLI Azure Digital Twins](concepts-cli.md)

Vous pouvez également découvrir comment connecter une application cliente à votre instance avec un code d’authentification :
* [Écrire le code d’authentification de l’application](how-to-authenticate-client.md)