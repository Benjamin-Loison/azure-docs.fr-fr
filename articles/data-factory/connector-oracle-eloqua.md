---
title: Copier des données à partir d’Oracle Eloqua (préversion)
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics pour copier des données d’Oracle Eloqua vers des banques de données réceptrices prises en charge.
titleSuffix: Azure Data Factory & Azure Synapse
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: e987244624939a5582523317b732527d99d5a2e0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831681"
---
# <a name="copy-data-from-oracle-eloqua-using-azure-data-factory-or-synapse-analytics-preview"></a>Copier des données d’Oracle Eloqua à l’aide d’Azure Data Factory ou de Synapse Analytics (préversion)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory ou Synapse Analytics pour copier des données d’Oracle Eloqua. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!IMPORTANT]
> Ce connecteur est actuellement en préversion. Essayez-le et envoyez-nous vos commentaires. Si vous souhaitez établir une dépendance sur les connecteurs en préversion dans votre solution, veuillez contacter le [support Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Oracle Eloqua est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier les données depuis Oracle Eloqua vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

La service fournit un pilote intégré qui permet la connexion. Vous n’avez donc pas besoin d’installer manuellement un pilote à l’aide de ce connecteur.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-oracle-eloqua-using-ui"></a>Créer un service lié à Oracle Eloqua à l’aide de l’interface utilisateur

Utilisez les étapes suivantes pour créer un service lié à Oracle Eloqua dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse et sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Capture d’écran de la création d’un nouveau service lié avec l’interface utilisateur d’Azure Synapse.":::

2. Recherchez Oracle et sélectionnez le connecteur Oracle Eloqua.

   :::image type="content" source="media/connector-oracle-eloqua/oracle-eloqua-connector.png" alt-text="Capture d’écran du connecteur Oracle Eloqua.":::    


1. Configurez les détails du service, testez la connexion et créez le nouveau service lié.

   :::image type="content" source="media/connector-oracle-eloqua/configure-oracle-eloqua-linked-service.png" alt-text="Capture d’écran de la configuration du service lié pour Oracle Eloqua.":::

## <a name="connector-configuration-details"></a>Détails de configuration des connecteurs

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Oracle Eloqua.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Oracle Eloqua sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **Eloqua** | Oui |
| endpoint | Le point de terminaison du serveur Eloqua. Eloqua prend en charge plusieurs centres de données. Pour déterminer votre point de terminaison, connectez-vous à https://login.eloqua.com avec vos informations d’identification, puis copiez la partie **URL de base** à partir de l’URL redirigée avec le modèle `xxx.xxx.eloqua.com`. | Oui |
| username | Nom du site et nom d’utilisateur de votre compte Eloqua au format : `SiteName\Username`, par exemple `Eloqua\Alice`.  | Oui |
| mot de passe | Mot de passe correspondant au nom d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| useEncryptedEndpoints | Indique si les points de terminaison de la source de données sont chiffrés suivant le protocole HTTPS. La valeur par défaut est true.  | Non |
| useHostVerification | Indique si le nom d’hôte du certificat du serveur doit correspondre à celui du serveur en cas de connexion TLS. La valeur par défaut est true.  | Non |
| usePeerVerification | Indique s’il faut vérifier l’identité du serveur en cas de connexion TLS. La valeur par défaut est true.  | Non |

**Exemple :**

```json
{
    "name": "EloquaLinkedService",
    "properties": {
        "type": "Eloqua",
        "typeProperties": {
            "endpoint" : "<base URL e.g. xxx.xxx.eloqua.com>",
            "username" : "<site name>\\<user name e.g. Eloqua\\Alice>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Oracle Eloqua.

Pour copier des données depuis Oracle Eloqua, affectez la valeur **EloquaObject** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **EloquaObject** | Oui |
| tableName | Nom de la table. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "EloquaDataset",
    "properties": {
        "type": "EloquaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Eloqua linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Oracle Eloqua.

### <a name="eloqua-as-source"></a>Eloqua en tant que source

Pour copier des données d’Oracle Eloqua, définissez le type de source dans l’activité de copie sur **EloquaSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **EloquaSource** | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM Accounts"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromEloqua",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Eloqua input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "EloquaSource",
                "query": "SELECT * FROM Accounts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Étapes suivantes
Consultez les [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats) pour obtenir la liste des banques de données prises en charge dans le service.
