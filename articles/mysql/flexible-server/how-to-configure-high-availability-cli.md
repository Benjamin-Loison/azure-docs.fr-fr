---
title: Gérer la haute disponibilité redondante interzone - Azure CLI - Serveur flexible Azure Database pour MySQL
description: Cet article explique comment configurer la haute disponibilité redondante interzone dans le serveur flexible Azure Database pour MySQL avec l’interface Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3ed931d0f972caa2e4a49012ad09afe9df9d3233
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422764"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Gérer la haute disponibilité redondante interzone dans le serveur flexible Azure Database pour MySQL avec Azure CLI

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


Cet article explique comment vous pouvez activer ou désactiver la configuration de la haute disponibilité avec redondance interzone au moment de la création du serveur dans votre serveur flexible. Vous pouvez également désactiver la haute disponibilité redondante interzone après la création du serveur. L’activation de la haute disponibilité redondante interzone après la création du serveur n’est pas prise en charge.

La fonctionnalité de haute disponibilité provisionne physiquement des réplicas principaux et secondaires séparés dans différentes zones. Pour plus d’informations, consultez la [documentation sur les concepts de haute disponibilité](./concepts/../concepts-high-availability.md). L’activation ou la désactivation de la haute disponibilité ne change pas vos autres paramètres, notamment la configuration du réseau virtuel, les paramètres du pare-feu et la conservation des sauvegardes. La désactivation de la haute disponibilité n’a pas d’impact sur la connectivité et le fonctionnement de votre application.

> [!IMPORTANT]
> La haute disponibilité redondante interzone est disponible dans un ensemble limité de régions. Consultez [ici](./overview.md#azure-regions) les régions prises en charge.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. 

    [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
- Installez ou mettez à niveau Azure CLI vers la dernière version. Consultez [Installer Azure CLI](/cli/azure/install-azure-cli).
- Connectez-vous au compte Azure à l’aide de la commande [az login](/cli/azure/reference-index#az_login). Notez la propriété **id**, qui fait référence à l’**ID d’abonnement** pour votre compte Azure.

    ```azurecli-interactive
    az login
    ````

- Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel vous souhaitez créer le serveur à l’aide de la commande ```az account set```.

    ```azurecli
    az account set --subscription <subscription id>
    ```

## <a name="enable-high-availability-during-server-creation"></a>Activer la haute disponibilité lors de la création du serveur

Vous pouvez uniquement créer des serveurs en utilisant les niveaux tarifaires Usage général ou Mémoire optimisée avec une haute disponibilité. Vous ne pouvez activer la haute disponibilité redondante interzone pour un serveur qu’au moment de sa création.

**Utilisation :**

   ```azurecli
    az mysql flexible-server create [--high-availability {Disabled, SameZone, ZoneRedundant}]
                                    [--sku-name]
                                    [--tier]
                                    [--resource-group]
                                    [--location]
                                    [--name]
   ```

**Exemple :**

   ```azurecli
    az mysql flexible-server create --name myservername --sku-name Standard_D2ds_v4 --tier Genaralpurpose --resource-group myresourcegroup --high-availability ZoneRedundant --location eastus
   ```

## <a name="disable-high-availability"></a>Désactiver la haute disponibilité

Vous pouvez désactiver la haute disponibilité à l’aide de la commande [az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update). Notez que la désactivation de la haute disponibilité est prise en charge seulement si le serveur a été créé avec une haute disponibilité.

```azurecli
az mysql flexible-server update [--high-availability {Disabled, SameZone, ZoneRedundant}]
                                [--resource-group]
                                [--name]
```
>[!Note]
>Si vous souhaitez passer de ZoneRedundant à SameZone, vous devez d’abord désactiver la haute disponibilité, puis activer la même zone.

**Exemple :**

   ```azurecli
    az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
   ```

## <a name="next-steps"></a>Étapes suivantes

- Découvrir la [continuité de l’activité](./concepts-business-continuity.md)
- Découvrir la [haute disponibilité avec redondance interzone](./concepts-high-availability.md)