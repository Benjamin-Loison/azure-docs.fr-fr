---
title: Exemple de script Azure CLI - Créer un compte Batch - abonnement utilisateur | Documents Microsoft
description: Découvrez comment créer un compte Azure Batch en mode abonnement utilisateur. Ce compte alloue des nœuds de calcul dans votre abonnement.
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: traitement, lot, exemples azure cli, exemples de code azure cli
ms.openlocfilehash: 087ba199787e36dccba58f37dd1ebb83c715d729
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658656"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>Exemple CLI : créer un compte Batch en mode abonnement utilisateur

Ce script crée un compte Azure Batch en mode abonnement utilisateur. Un compte qui alloue des nœuds de calcul dans votre abonnement doit être authentifié via un jeton Azure Active Directory. Les nœuds de calcul alloués sont pris en compte dans le quota de processeurs virtuels (cœur) de votre abonnement.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ce tutoriel nécessite Azure CLI version 2.0.20 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.  

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Exécutez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qui lui sont associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az role assignment create](/cli/azure/role) | Crée une nouvelle attribution de rôle pour un utilisateur, un groupe ou un principal de service. |
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az keyvault create](/cli/azure/keyvault#az_keyvault_create) | Crée un coffre de clés. |
| [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) | Met à jour la stratégie de sécurité du coffre de clés spécifié. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Crée le compte Batch.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Effectue l’authentification par rapport au compte Batch spécifié pour renforcer les interactions avec la CLI.  |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).
