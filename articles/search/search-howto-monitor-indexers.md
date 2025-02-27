---
title: Superviser l’état et les résultats des indexeurs
titleSuffix: Azure Cognitive Search
description: Surveillez l’état, la progression et les résultats des indexeurs Recherche cognitive Azure dans le portail Azure (en utilisant l’API REST) ou le SDK .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: bfcbdeb2755f76d5391de4a3c64a934b43e8f0f2
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982939"
---
# <a name="monitor-indexer-status-and-results-in-azure-cognitive-search"></a>Surveiller l’état et les résultats d’un indexeur Recherche cognitive Azure

Vous pouvez superviser le traitement de l’indexeur dans le portail Azure ou programmatiquement par le biais d’appels REST ou d’un kit SDK Azure. Outre l’état de l’indexeur lui-même, vous pouvez consulter les heures de début et de fin, ainsi que les erreurs et avertissements détaillés d’une exécution spécifique.

## <a name="monitor-using-azure-portal"></a>Superviser à l’aide du portail Azure

L’état actuel de tous vos indexeurs se trouve dans la page Vue d’ensemble de votre service de recherche. Les pages du portail sont actualisées au bout de quelques minutes. Vous ne verrez donc pas immédiatement une nouvelle exécution de l’indexeur.

   ![Liste des indexeurs](media/search-monitor-indexers/indexers-list.png "Liste des indexeurs")

| Statut | Description |
|--------|-------------|
| **En cours** | Indique une exécution active. Le portail donne des informations partielles. À mesure que l’indexation progresse, vous pouvez observer que la valeur **Documents ayant réussi** augmente en conséquence. L’exécution des indexeurs qui traitent de grands volumes de données peut être longue. Par exemple, les indexeurs qui traitent des millions de documents sources peuvent fonctionner pendant 24 heures, puis redémarrer presque immédiatement. Les indexeurs traitant de grands volumes de données peuvent se trouver encore à l’état **En cours** dans le portail. Même lorsqu’un indexeur est en cours d’exécution, des informations détaillées sont disponibles sur la progression en cours et les exécutions précédentes. |
| **Success** | Indique que l’exécution a réussi. Une exécution de l’indexeur peut réussir même si des documents individuels contiennent des erreurs, car le nombre d’erreurs est inférieur à la valeur du paramètre **Nombre maximal d’éléments en échec** de l’indexeur. |
| **Échec** | Le nombre d’erreurs était supérieur à **Nombre maximal d’éléments ayant échoué** et l’indexation s’est arrêtée. |
| **Réinitialiser** | L’état de suivi des modifications interne de l’indexeur a été réinitialisé. L’indexeur s’exécute entièrement, en actualisant tous les documents, et pas seulement ceux avec des horodatages plus récents. |

Vous pouvez cliquer sur un indexeur dans la liste pour afficher plus de détails sur les exécutions actuelles et récentes de l’indexeur.

   ![Résumé de l’indexeur et historique d’exécution](media/search-monitor-indexers/indexer-summary.png "Résumé de l’indexeur et historique d’exécution")

Le graphique **Résumé de l’indexeur** indique le nombre de documents traités lors des exécutions les plus récentes de l’indexeur.

La liste **Détails de l’exécution** affiche jusqu’à 50 des résultats d’exécution les plus récents. Cliquez sur un résultat d’exécution dans la liste pour afficher les détails correspondants. Cela inclut les heures de début et de fin, ainsi que les erreurs et les avertissements qui se sont produits.

   ![Détails de l’exécution de l’indexeur](media/search-monitor-indexers/indexer-execution.png "Détails de l’exécution de l’indexeur")

Si vous rencontrez des problèmes spécifiques au document pendant l’exécution, ils seront signalés dans les champs Erreurs et Avertissements.

   ![Détails de l’indexeur avec des erreurs](media/search-monitor-indexers/indexer-execution-error.png "Détails de l’indexeur avec des erreurs")

Les avertissements sont courants avec certains types d’indexeurs et n’indiquent pas toujours un problème. Par exemple, les indexeurs qui utilisent des services cognitifs peuvent signaler des avertissements lorsque les fichiers image ou PDF ne contiennent pas de texte à traiter. 

Pour plus d’informations sur l’investigation des erreurs et des avertissements liés à l’indexeur, consultez les [conseils d’aide sur la résolution des problèmes de l’indexeur](search-indexer-troubleshooting.md).

## <a name="monitor-using-get-indexer-status-rest-api"></a>Superviser avec Get Indexer Status (API REST)

Vous pouvez récupérer l’état et l’historique d’exécution d’un indexeur à l’aide de la [commande Get Indexer Status](/rest/api/searchservice/get-indexer-status) :

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

La réponse contient l'état d'intégrité global de l'indexeur, le dernier appel de l'indexeur (ou celui en cours), ainsi que l'historique des appels récents de l'indexeur.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

L’historique d’exécution contient les 50 exécutions les plus récentes, classées par ordre chronologique inverse (la plus récente en premier).

Notez qu’il existe deux valeurs d’état différentes. L’état de niveau supérieur s’applique à l’indexeur lui-même. Un état d’indexeur **En cours** signifie que l’indexeur est correctement configuré et disponible pour être exécuté mais qu’il n’est pas actuellement en cours d’exécution.

Chaque exécution de l’indexeur possède également un état qui indique si cette exécution spécifique est en cours d’exécution (**running**) ou déjà terminée avec l’état **success**, **transientFailure** ou **persistentFailure**. 

Lorsque l’on réinitialise un indexeur pour actualiser son état de suivi des modifications, une entrée distincte indiquant l’état **Réinitialiser** est ajoutée à l’historique des exécutions.

Pour plus d’informations sur les codes d’état et les données de supervision de l’indexeur, consultez [Get Indexer Status](/rest/api/searchservice/get-indexer-status).

## <a name="monitor-using-net"></a>Superviser avec .NET

En utilisant le Kit de développement logiciel (SDK) .NET Recherche cognitive Azure, l’exemple C# suivant écrit des informations sur l’état d’un indexeur et les résultats de son exécution la plus récente (ou en cours) sur la console.

```csharp
static void CheckIndexerStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        string indexerName = "hotels-sql-idxr";
        SearchIndexerStatus execInfo = indexerClient.GetIndexerStatus(indexerName);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("Run Status: {0}", result.Status.ToString());
        Console.WriteLine("Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("ErrorMessage: {0}", errorMsg);
        Console.WriteLine(" Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

Sur la console, vous obtenez un résultat semblable à ceci :

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 11:29:31 PM, EndTime: 11:29:31 PM, Elapsed: 00:00:00.2560000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

Notez qu’il existe deux valeurs d’état différentes. L’état de niveau supérieur est l’état de l’indexeur lui-même. Un état d’indexeur **En cours** signifie que l’indexeur est correctement configuré et disponible pour être exécuté mais qu’il n’est pas actuellement en cours d’exécution.

Chaque exécution de l’indexeur possède également un état qui indique si cette exécution spécifique est en cours d’exécution (**En cours**) ou déjà terminée avec l’état **Réussite** ou **TransientError**. 

Lorsque l’on réinitialise un indexeur pour actualiser son état de suivi des modifications, une entrée distincte indiquant l’état **Réinitialiser** est ajoutée à l’historique.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les codes d’état et les données de surveillance de l’indexeur, consultez la référence API suivante :

* [GetIndexerStatus (API REST)](/rest/api/searchservice/get-indexer-status)
* [IndexerStatus](/dotnet/api/azure.search.documents.indexes.models.indexerstatus)
* [IndexerExecutionStatus](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionstatus)
* [IndexerExecutionResult](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionresult)