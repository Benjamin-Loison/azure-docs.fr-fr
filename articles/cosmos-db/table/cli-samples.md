---
title: Exemples Azure CLI pour l’API Table Azure Cosmos DB
description: Exemples Azure CLI pour l’API Table Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4050e01e25a6f8f74370418bd1203b49c7f12a79
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785441"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-table-api"></a>Exemples Azure CLI pour l’API Table Azure Cosmos DB
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Le tableau suivant comprend des liens vers des exemples de scripts Azure CLI pour Azure Cosmos DB. Utilisez les liens sur la droite pour accéder aux exemples spécifiques aux API. Les exemples communs sont les mêmes pour toutes les API. Des pages d’informations de référence pour toutes les commandes Azure Cosmos DB CLI sont disponibles dans les [Informations de référence sur Azure CLI](/cli/azure/cosmosdb). Vous trouverez également les exemples de scripts CLI Azure Cosmos DB dans le [dépôt GitHub CLI Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Ces exemples nécessitent Azure CLI version 2.12.1 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="common-samples"></a>Exemples communs

Ces exemples s’appliquent à toutes les API Azure Cosmos DB.

|Tâche | Description |
|---|---|
| [Ajouter des régions de basculement](../scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Ajoutez une région, changez la priorité de basculement, déclenchez un basculement manuel.|
| [Clés de compte et chaînes de connexion](../scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Listez les clés de compte, les clés en lecture seule, regénérez les clés et listez les chaînes de connexion.|
| [Sécuriser avec le pare-feu IP](../scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte Cosmos avec le pare-feu IP configuré.|
| [Sécuriser un nouveau compte avec des points de terminaison de service](../scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte Cosmos et sécurisez-le avec des points de terminaison de service.|
| [Sécuriser un compte existant avec des points de terminaison de service](../scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Mettez à jour un compte Cosmos pour le sécuriser avec des points de terminaison de service quand le sous-réseau est finalement configuré.|
|||

## <a name="table-api-samples"></a>Exemples d’API Table

|Tâche | Description |
|---|---|
| [Créer un compte et une table Azure Cosmos](../scripts/cli/table/create.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte et une table Azure Cosmos DB pour l’API Table. |
| [Créer un compte et une table Azure Cosmos avec mise à l’échelle automatique](../scripts/cli/table/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Crée un compte et une table Azure Cosmos DB avec mise à l’échelle automatique pour l’API Table. |
| [Opérations de débit](../scripts/cli/table/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Opérations sur une table, comme la lecture du débit, la mise à jour du débit et la migration entre le débit standard et le débit avec mise à l’échelle automatique.|
| [Verrouiller des ressources contre la suppression](../scripts/cli/table/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources.|
|||
