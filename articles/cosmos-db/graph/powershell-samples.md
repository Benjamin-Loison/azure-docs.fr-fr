---
title: Exemples Azure PowerShell pour Azure Cosmos DB - API Gremlin
description: Obtenir les exemples Azure PowerShell pour effectuer des tâches courantes dans l’API Gremlin Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: 8132d3f22b19f42e0aa6aac9fc33b95ef3e23939
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785346"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-gremlin-api"></a>Exemples Azure PowerShell pour Azure Cosmos DB - API Gremlin
[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Le tableau suivant comprend des liens vers des scripts Azure PowerShell couramment utilisés pour Azure Cosmos DB. Utilisez les liens sur la droite pour accéder aux exemples spécifiques aux API. Les exemples communs sont les mêmes pour toutes les API. Des pages d’informations de référence pour toutes les applets de commande PowerShell Azure Cosmos DB sont disponibles dans les [Informations de référence sur Azure PowerShell](/powershell/module/az.cosmosdb). Le module `Az.CosmosDB` fait maintenant partie du module `Az`. [Téléchargez et installez](/powershell/azure/install-az-ps) la dernière version du module Az pour accéder aux applets de commande Azure Cosmos DB. Vous pouvez également récupérer la version la plus récente à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/Az/5.4.0). Vous pouvez également dupliquer (fork) ces exemples PowerShell pour Cosmos DB à partir de notre dépôt GitHub : [Exemples PowerShell pour Cosmos DB sur GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Exemples communs

|Tâche | Description |
|---|---|
|[Mettre à jour un compte](../scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mettez à jour le niveau de cohérence par défaut d’un compte Cosmos DB. |
|[Mettre à jour les régions d’un compte](../scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mettez à jour les régions d’un compte Cosmos DB. |
|[Modifier la priorité de basculement ou déclencher un basculement](../scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Modifiez la priorité de basculement régional d’un compte Azure Cosmos ou déclenchez un basculement manuel. |
|[Clés de compte ou chaînes de connexion](../scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenez des clés primaires et secondaires, des chaînes de connexion, ou regénérez une clé de compte d’un compte Azure Cosmos DB. |
|[Créer un compte Cosmos avec un pare-feu IP](../scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créez un compte Azure Cosmos DB avec le pare-feu IP activé. |
|||

## <a name="gremlin-api-samples"></a>Exemples d’API Gremlin

|Tâche | Description |
|---|---|
|[Créer un compte, une base de données et un graphe](../scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créez un compte, une base de données et un graphe Azure Cosmos. |
|[Créer un compte, une base de données et un graphe avec mise à l’échelle automatique](../scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée un compte, une base de données et un graphe Azure Cosmos avec mise à l’échelle automatique. |
|[Lister ou obtenir des bases de données et des graphes](../scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Listez ou obtenez des bases de données et des graphes. |
|[Opérations de débit](../scripts/powershell/gremlin/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Opérations de débit sur une base de données ou un graphe, comme l’obtention du débit, la mise à jour du débit et la migration entre le débit standard et le débit avec mise à l’échelle automatique. |
|[Verrouiller des ressources contre la suppression](../scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources. |
|||
