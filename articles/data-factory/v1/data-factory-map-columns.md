---
title: Mappage de colonnes de jeux de données dans Azure Data Factory
description: Découvrez comment mapper des colonnes source sur des colonnes de destination.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
robots: noindex
ms.openlocfilehash: bd45059d22ba2ea3c5b5cc49e0b4193dfa751cbe
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218596"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mapper des colonnes d’un jeu de données source sur des colonnes d’un jeu de données de destination
> [!NOTE]
> Cet article s’applique à la version 1 de Data Factory. 

Le mappage de colonnes peut être utilisé pour définir la façon dont les colonnes spécifiées dans la « structure » de la table source sont mappées aux colonnes spécifiées dans la « structure » de la table du récepteur. La propriété **columnMapping** est disponible dans la section **typeProperties** de l’activité de copie.

Le mappage de colonnes prend en charge les scénarios suivants :

* Toutes les colonnes de la structure du jeu de données source sont mappées sur toutes les colonnes de la structure du jeu de données récepteur.
* Un sous-ensemble des colonnes de la structure du jeu de données source est mappé sur toutes les colonnes de la structure du jeu de données récepteur.

Voici une liste de conditions d’erreur qui entraînent la levée d’une exception :

* La « structure » de la table du récepteur contient un nombre de colonnes inférieur ou supérieur à celui spécifié par le mappage de colonnes.
* Mappage en double.
* Le résultat de la requête SQL ne comprend pas de nom de colonne qui soit spécifié dans le mappage.

> [!NOTE]
> Les exemples suivants concernent SQL Azure et les objets blob Azure, mais sont applicables à tout magasin de données prenant en charge les jeux de données rectangulaires. Ajustez les définitions du jeu de données et du service lié dans les exemples de sorte qu’elles pointent vers les données de la source de données appropriée.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Exemple 1 : mappage de colonnes depuis SQL Azure vers un objet blob Azure
Dans cet exemple, la table d’entrée possède une structure et pointe vers une table SQL comprise dans Azure SQL Database.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Dans cet exemple, la table de sortie possède une structure et pointe vers un objet blob compris dans un stockage d’objets blob Azure.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Le JSON suivant définit une activité de copie dans un pipeline. Les colonnes de la source sont mappées sur les colonnes du récepteur (**columnMappings**) à l’aide de la propriété **Translator**.

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Flux du mappage de colonnes :**

:::image type="content" source="./media/data-factory-map-columns/column-mapping-flow.png" alt-text="Flux du mappage de colonnes":::

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Exemple 2 : mappage de colonnes à l’aide d’une requête SQL depuis SQL Azure vers un objet blob Azure
Dans cet exemple, une requête SQL est utilisée pour extraire des données d’Azure SQL au lieu de simplement spécifier le nom de la table et le nom des colonnes dans la section « structure ». 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
Dans ce cas, les résultats de la requête sont d’abord mappés aux colonnes spécifiées dans la « structure » de la source. Ensuite, les colonnes de la « structure » de la source sont mappées aux colonnes de la « structure » du récepteur avec les règles spécifiées dans columnMappings.  Supposons que la requête retourne cinq colonnes, c’est-à-dire deux colonnes de plus que celles spécifiées dans la « structure » de la source.

**Flux du mappage de colonnes**

:::image type="content" source="./media/data-factory-map-columns/column-mapping-flow-2.png" alt-text="Flux du mappage de colonnes 2":::

## <a name="next-steps"></a>Étapes suivantes
Suivez le didacticiel sur l’activité de copie dans l’article suivant : 

- [Copie de données à partir du Stockage Blob vers une base de données SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
