---
title: Transformer des données à l’aide d’une activité Hadoop Pig
description: Découvrez comment utiliser l'activité Pig pour exécuter des requêtes Pig sur un cluster HDInsight à la demande/ou votre propre cluster avec Azure Data Factory ou Synapse Analytics.
titleSuffix: Azure Data Factory & Azure Synapse
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c38b4ec230ef6140500905e3dd1eea6f8c089047
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005692"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory-or-synapse-analytics"></a>Transformer des données à l’aide d’une activité Hadoop Pig dans Azure Data Factory ou Synapse Analytics

> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-pig-activity.md)
> * [Version actuelle](transform-data-using-hadoop-pig.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L’activité HDInsight Pig d’un [pipeline](concepts-pipelines-activities.md) Data Factory exécute des requêtes Pig sur [votre propre](compute-linked-services.md#azure-hdinsight-linked-service) cluster ou cluster [à la demande](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight. Cet article s'appuie sur l'article [Activités de transformation des données](transform-data.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge.

Pour en savoir plus, avant de lire cet article, consultez l’introduction à [Azure Data Factory](introduction.md) ou [Synapse Analytics](../synapse-analytics/overview-what-is.md), puis suivez le didacticiel [Transformer des données](tutorial-transform-data-spark-powershell.md). 

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }   
}
```
## <a name="syntax-details"></a>Détails de la syntaxe

| Propriété            | Description                              | Obligatoire |
| ------------------- | ---------------------------------------- | -------- |
| name                | Nom de l’activité                     | Oui      |
| description         | Texte décrivant la raison motivant l’activité. | Non       |
| type                | Pour l’activité Hive, le type d’activité est HDinsightPig. | Oui      |
| linkedServiceName   | Référence au cluster HDInsight enregistré en tant que service lié. Pour en savoir plus sur ce service lié, consultez l’article [Services liés de calcul](compute-linked-services.md). | Oui      |
| scriptLinkedService | Référence à un service lié de stockage Azure utilisé pour stocker le script Pig à exécuter. Seuls les services liés **[Stockage Blob Azure](./connector-azure-blob-storage.md)** et **[ADLS Gen2](./connector-azure-data-lake-storage.md)** sont pris en charge ici. Si vous ne spécifiez pas ce service lié, le service lié Stockage Azure défini dans le service lié HDInsight est utilisé. | Non       |
| scriptPath          | Indiquez le chemin du fichier de script stocké dans le stockage Azure référencé par scriptLinkedService. Le nom de fichier respecte la casse. | Non       |
| getDebugInfo        | Spécifie quand les fichiers journaux sont copiés vers le stockage Azure utilisé par le cluster HDInsight (ou) spécifié par scriptLinkedService. Valeurs autorisées : None, Always ou Failure. Valeur par défaut : Aucun. | Non       |
| arguments           | Spécifie un tableau d’arguments pour un travail Hadoop. Les arguments sont passés sous la forme d’arguments de ligne de commande à chaque tâche. | Non       |
| defines             | Spécifier les paramètres sous forme de paires clé/valeur pour le référencement au sein du script Pig. | Non       |

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants qui expliquent comment transformer des données par d’autres moyens : 

* [Activité U-SQL](transform-data-using-data-lake-analytics.md)
* [Activité Hive](transform-data-using-hadoop-hive.md)
* [Activité MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Activité de diffusion en continu Hadoop](transform-data-using-hadoop-streaming.md)
* [Activité Spark](transform-data-using-spark.md)
* [Activité personnalisée .NET](transform-data-using-dotnet-custom-activity.md)
* [Activité de procédure stockée](transform-data-using-stored-procedure.md)
