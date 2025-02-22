---
title: Script CLI - Créer un serveur - Azure Database pour MySQL
description: Cet exemple de script CLI crée un serveur Azure Database pour MySQL et configure une règle de pare-feu au niveau du serveur.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 12/02/2019
ms.openlocfilehash: 94a7e19b2fd668145a3748cd23f7ac640ea444d5
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084675"
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Créer un serveur MySQL et configurer une règle de pare-feu à l’aide de l’interface de ligne de commande Azure

[[!INCLUDE[applies-to-mysql-single-flexible-server](../includes/applies-to-mysql-single-flexible-server.md)]

Cet exemple de script CLI crée un serveur Azure Database pour MySQL et configure une règle de pare-feu au niveau du serveur. Une fois que le script s’exécute correctement, le serveur MySQL est accessible par tous les services Azure et l’adresse IP configurée.

[!INCLUDE[azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. 

## <a name="sample-script"></a>Exemple de script

Dans cet exemple de script, modifiez les lignes en surbrillance pour mettre à jour le nom d’utilisateur et le mot de passe d’administrateur et utiliser les vôtres.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/create-mysql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for MySQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées après l’exécution du script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/delete-mysql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes décrites dans le tableau suivant :

| **Commande** | **Remarques** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | Crée un serveur MySQL qui héberge les bases de données. |
| [az mysql server firewall create](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) | Crée une règle de pare-feu pour autoriser l’accès au serveur et aux bases de données qui s’y trouvent à partir de la plage d’adresses IP entrée. |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur Azure CLI : [Documentation d’Azure CLI](/cli/azure)
- Essayez d’autres scripts : [Exemples de CLI Azure pour Azure Database pour MySQL](../sample-scripts-azure-cli.md)
