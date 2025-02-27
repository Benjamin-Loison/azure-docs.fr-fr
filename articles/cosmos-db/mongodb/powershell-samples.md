---
title: Exemples Azure PowerShell destinés à l’API Azure Cosmos DB pour MongoDB
description: Obtenir les exemples Azure PowerShell pour effectuer des tâches courantes dans l’API Azure Cosmos DB pour MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 08/26/2021
ms.author: mjbrown
ms.openlocfilehash: 4f30f265a3e4865ea68782088661f9153662d1e5
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039710"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-api-for-mongodb"></a>Exemples Azure PowerShell destinés à l’API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

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

## <a name="mongo-db-api-samples"></a>Exemples d’API Mongo DB

|Tâche | Description |
|---|---|
|[Créer un compte, une base de données et une collection](../scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créez un compte, une base de données et une collection Azure Cosmos. |
|[Créer un compte, une base de données et une collection avec mise à l’échelle automatique](../scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée un compte, une base de données et une collection Azure Cosmos avec mise à l’échelle automatique. |
|[Lister ou obtenir des bases de données et des collections](../scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Listez ou obtenez des bases de données et des collections. |
|[Opérations de débit](../scripts/powershell/mongodb/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Opérations de débit sur une base de données ou une collection, comme l’obtention du débit, la mise à jour du débit et la migration entre le débit standard et le débit avec mise à l’échelle automatique. |
|[Verrouiller des ressources contre la suppression](../scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources. |
|||

## <a name="next-steps"></a>Étapes suivantes

Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existant pour la planification de la capacité.
* Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existantes, lisez l’article sur l’[estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](../convert-vcore-to-request-unit.md) 
* Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez la section concernant l’[estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-capacity-planner.md)