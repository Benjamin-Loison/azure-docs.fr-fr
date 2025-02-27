---
title: Copier des données à partir d’Amazon RDS for Oracle
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment copier des données à partir d’Amazon RDS for Oracle vers des banques de données réceptrices prises en charge à l’aide de pipelines Azure Data Factory ou Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: jianleishen
ms.openlocfilehash: c6f14b2bb5129907aa5efac042f23217791d84d3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602497"
---
# <a name="copy-data-from-amazon-rds-for-oracle-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Copier des données à partir d’Amazon RDS for Oracle à l’aide d’Azure Data Factory ou d’Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir d’une base de données Amazon RDS for Oracle. Il s’appuie sur la [vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Amazon RDS for Oracle est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données d’une base de données Amazon RDS for Oracle vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Amazon RDS for Oracle prend en charge :

- Les versions suivantes d’une base de données Amazon RDS for Oracle :
    - Amazon RDS for Oracle 19c R1 (19.1) et versions ultérieures
    - Amazon RDS for Oracle 18c R1 (18.1) et versions ultérieures
    - Amazon RDS for Oracle 12c R1 (12.1) et versions ultérieures
    - Amazon RDS for Oracle 11g R1 (11.1) et versions ultérieures
- La copie parallèle à partir d’une source Amazon RDS for Oracle Pour plus d’informations, consultez la section [Copie en parallèle à partir d’Amazon RDS for Oracle](#parallel-copy-from-amazon-rds-for-oracle).

> [!Note]
> Le serveur proxy Amazon RDS for Oracle n’est pas pris en charge.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)] 

Le runtime d’intégration intègre un pilote Amazon RDS for Oracle. Par conséquent, vous n’avez pas besoin d’installer manuellement un pilote pour copier des données à partir d’Amazon RDS for Oracle.

## <a name="get-started"></a>Bien démarrer

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-amazon-rds-for-oracle-using-ui"></a>Créer un service lié à Amazon RDS for Oracle à l’aide de l’interface utilisateur

Utilisez les étapes suivantes pour créer un service lié à Amazon RDS for Oracle dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse, sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Synapse.":::

1. Recherchez Amazon RDS for Oracle et sélectionnez le connecteur Amazon RDS for Oracle.

    :::image type="content" source="media/connector-amazon-rds-for-oracle/amazon-rds-for-oracle-connector.png" alt-text="Capture d’écran du connecteur Amazon RDS for Oracle.":::    

1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

    :::image type="content" source="media/connector-amazon-rds-for-oracle/configure-amazon-rds-for-oracle-linked-service.png" alt-text="Capture d’écran de la configuration du service lié pour Amazon RDS for Oracle.":::

## <a name="connector-configuration-details"></a>Détails de configuration du connecteur

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités spécifiques du connecteur Amazon RDS for Oracle.

## <a name="linked-service-properties"></a>Propriétés du service lié

Le service lié à Amazon RDS for Oracle prend en charge les propriétés suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **AmazonRdsForOracle**. | Oui |
| connectionString | Spécifie les informations requises pour se connecter à l’instance de base de données Amazon RDS for Oracle. <br/>Vous pouvez également définir un mot de passe dans Azure Key Vault et extraire la configuration `password` de la chaîne de connexion. Pour plus d’informations, reportez-vous aux exemples suivants et à [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). <br><br>**Type de connexion pris en charge** : Vous pouvez utiliser l’**identificateur de sécurité Amazon RDS for Oracle** ou le **nom du service Amazon RDS for Oracle** pour identifier votre base de données :<br>- Si vous utilisez le SID : `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Si vous utilisez le nom du service : `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Comme options avancées de connexion native Amazon RDS for Oracle, vous pouvez choisir d’ajouter une entrée dans le fichier [TNSNAMES.ORA](http://www.orafaq.com/wiki/Tnsnames.ora) sur le serveur Amazon RDS for Oracle et, dans le service lié à Amazon RDS for Oracle, choisissez d’utiliser le type de connexion avec nom du service Amazon RDS for Oracle et configurez le nom du service correspondant. | Oui |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Pour plus d’informations, consultez la section [Conditions préalables](#prerequisites). À défaut de spécification, l’Azure Integration Runtime par défaut est utilisé. |Non |

Si vous avez plusieurs instances Amazon RDS for Oracle pour le scénario de basculement, vous pouvez créer un service lié à Amazon RDS for Oracle, renseigner l’hôte principal, le port, le nom d’utilisateur, le mot de passe, etc., et ajouter une nouvelle propriété « **Propriétés de connexion supplémentaires** » avec le nom `AlternateServers` et la valeur `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)`. N’oubliez pas les crochets et faites attention aux signes deux-points (`:`) utilisés comme séparateur. À titre d’exemple, la valeur suivante de serveurs secondaires définit deux serveurs de base de données de remplacement pour le basculement de connexion : `(HostName=AccountingAmazonRdsForOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)`.

Autres propriétés de connexion que vous pouvez définir dans la chaîne de connexion selon votre cas :

| Propriété | Description | Valeurs autorisées |
|:--- |:--- |:--- |
| ArraySize |Nombre d’octets que le connecteur peut extraire dans un aller-retour réseau. Par exemple, `ArraySize=‭10485760‬`.<br/><br/>Des valeurs plus élevées augmentent le débit en réduisant le nombre de fois que des données sont extraites sur le réseau. Des valeurs réduites augmentent le temps de réponse, car il y a moins de retard à attendre que le serveur transmette les données. | Entier compris entre 1 et 4294967296 (4 Go). La valeur par défaut est `60000`. La valeur 1 ne définit pas le nombre d’octets, mais indique une allocation d’espace pour une seule ligne de données. |

Pour activer le chiffrement sur la connexion Amazon RDS for Oracle, deux options s’offrent à vous :

-   Pour utiliser le **chiffrement 3DES (Triple-DES) et AES (Advanced Encryption Standard)** , du côté du serveur Amazon RDS for Oracle, accédez à Oracle Advanced Security (OAS) et configurez les paramètres de chiffrement. Pour plus d’informations, consultez cette [documentation Oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Le connecteur Amazon RDS for Oracle Application Development Framework (ADF) négocie automatiquement la méthode de chiffrement pour utiliser celle que vous configurez dans OAS lors de l’établissement d’une connexion à Amazon RDS for Oracle.

-   Pour utiliser **TLS** :

    1.  Obtenez les informations de certificat TLS/SSL. Obtenez les informations de certificat encodé DER (Distinguished Encoding Rules) de votre certificat TLS/SSL et enregistrez la sortie (-----Begin Certificate … End Certificate---) sous forme de fichier texte.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Exemple :** Extrayez les informations de certificat de DERcert.cer, puis enregistrez la sortie dans cert.txt.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Générez le `keystore` ou le `truststore`. La commande suivante crée le fichier `truststore`, avec ou sans mot de passe, au format PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Exemple :** Créez un fichier `truststore` PKCS12 nommé MyTrustStoreFile, avec un mot de passe.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Placez le fichier `truststore` sur l’ordinateur IR auto-hébergé. Par exemple, placez le fichier sur C:\MyTrustStoreFile.
    4.  Dans le service, configurez la chaîne de connexion Amazon RDS for Oracle avec `EncryptionMethod=1` et la valeur `TrustStore`/`TrustStorePassword` correspondante. Par exemple : `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Exemple :**

```json
{
    "name": "AmazonRdsForOracleLinkedService",
    "properties": {
        "type": "AmazonRdsForOracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : stockage du mot de passe dans Azure Key Vault**

```json
{
    "name": "AmazonRdsForOracleLinkedService",
    "properties": {
        "type": "AmazonRdsForOracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
## <a name="dataset-properties"></a>Propriétés du jeu de données

Cette section fournit la liste des propriétés prises en charge par le jeu de données Amazon RDS for Oracle. Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). 

Pour copier des données à partir d’Amazon RDS for Oracle, définissez la propriété type du jeu de données sur `AmazonRdsForOracleTable`. Les propriétés suivantes sont prises en charge.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur `AmazonRdsForOracleTable`. | Oui |
| schéma | Nom du schéma. | Non |
| table | Nom de la table/vue. | Non |
| tableName | Nom de la table/vue avec schéma. Cette propriété est prise en charge pour la compatibilité descendante. Pour les nouvelles charges de travail, utilisez `schema` et `table`. | Non |

**Exemple :**

```json
{
    "name": "AmazonRdsForOracleDataset",
    "properties":
    {
        "type": "AmazonRdsForOracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Amazon RDS for Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Cette section fournit la liste des propriétés prises en charge par la source Amazon RDS for Oracle. Pour obtenir la liste complète des sections et propriétés disponibles pour la définition des activités, consultez [Pipelines](concepts-pipelines-activities.md). 

### <a name="amazon-rds-for-oracle-as-source"></a>Amazon RDS for Oracle en tant que source

>[!TIP]
>Pour charger efficacement les données d’Amazon RDS for Oracle en utilisant le partitionnement des données, apprenez-en plus sur la [copie parallèle à partir d’Amazon RDS for Oracle](#parallel-copy-from-amazon-rds-for-oracle).

Pour copier des données à partir d’Amazon RDS for Oracle, définissez le type de source dans l’activité de copie sur `AmazonRdsForOracleSource`. Les propriétés suivantes sont prises en charge dans la section **source** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source de l’activité de copie doit être définie sur `AmazonRdsForOracleSource`. | Oui |
| oracleReaderQuery | Utiliser la requête SQL personnalisée pour lire les données. par exemple `"SELECT * FROM MyTable"`.<br>Lorsque vous activez la charge partitionnée, vous devez utiliser les paramètres de partition intégrés correspondants dans votre requête. Pour voir des exemples, consultez la section [Copie en parallèle à partir d’Amazon RDS for Oracle](#parallel-copy-from-amazon-rds-for-oracle). | Non |
| partitionOptions | Spécifie les options de partitionnement des données utilisées pour charger des données à partir d’Amazon RDS for Oracle. <br>Les valeurs autorisées sont les suivantes : **None** (valeur par défaut), **PhysicalPartitionsOfTable** et **DynamicRange**.<br>Lorsqu’une option de partition est activée (autrement dit, pas `None`), le degré de parallélisme pour charger simultanément des données à partir d’une base de données Amazon RDS for Oracle est contrôlé par le paramètre [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) de l’activité de copie. | Non |
| partitionSettings | Spécifiez le groupe de paramètres pour le partitionnement des données. <br>S’applique lorsque l’option de partitionnement n’est pas `None`. | Non |
| partitionNames | La liste des partitions physiques qui doivent être copiées. <br>S’applique lorsque l’option de partitionnement est `PhysicalPartitionsOfTable`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfTabularPartitionName` dans la clause WHERE. Pour voir un exemple, consultez la section [Copie en parallèle à partir d’Amazon RDS for Oracle](#parallel-copy-from-amazon-rds-for-oracle). | Non |
| partitionColumnName | Spécifiez le nom de la colonne source **dans type entier** qu’utilisera le partitionnement par plages de valeurs pour la copie en parallèle. S’il n’est pas spécifié, la clé primaire de la table sera automatiquement détectée et utilisée en tant que colonne de partition. <br>S’applique lorsque l’option de partitionnement est `DynamicRange`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfRangePartitionColumnName` dans la clause WHERE. Pour voir un exemple, consultez la section [Copie en parallèle à partir d’Amazon RDS for Oracle](#parallel-copy-from-amazon-rds-for-oracle). | Non |
| partitionUpperBound | Valeur maximale de la colonne de partition à partir de laquelle copier des données. <br>S’applique lorsque l’option de partitionnement est `DynamicRange`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfRangePartitionUpbound` dans la clause WHERE. Pour voir un exemple, consultez la section [Copie en parallèle à partir d’Amazon RDS for Oracle](#parallel-copy-from-amazon-rds-for-oracle). | Non |
| partitionLowerBound | Valeur minimale de la colonne de partition à partir de laquelle copier des données. <br>S’applique lorsque l’option de partitionnement est `DynamicRange`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfRangePartitionLowbound` dans la clause WHERE. Pour voir un exemple, consultez la section [Copie en parallèle à partir d’Amazon RDS for Oracle](#parallel-copy-from-amazon-rds-for-oracle). | Non |

**Exemple : copie de données à l’aide de la requête de base sans partition**

```json
"activities":[
    {
        "name": "CopyFromAmazonRdsForOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon RDS for Oracle input dataset name>",
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
                "type": "AmazonRdsForOracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-amazon-rds-for-oracle"></a>Copie en parallèle à partir d’Amazon RDS for Oracle

Le connecteur Amazon RDS for Oracle propose un partitionnement de données intégré pour copier des données à partir d’Amazon RDS for Oracle en parallèle. Vous trouverez des options de partitionnement de données dans l’onglet **Source** de l’activité de copie.

:::image type="content" source="./media/connector-amazon-rds-for-oracle/connector-amazon-rds-for-oracle-partition-options.png" alt-text="Capture d’écran représentant les options de partition.":::

Lorsque vous activez la copie partitionnée, le service exécute des requêtes en parallèle sur votre source Amazon RDS for Oracle pour charger des données par partitions. Le degré de parallélisme est contrôlé via le paramètre [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) sur l’activité de copie. Par exemple, si vous définissez `parallelCopies` sur quatre, le service génère et exécute simultanément quatre requêtes basées sur l’option de partition et les paramètres que vous avez spécifiés, chacune récupérant une partie des données à partir de votre base de données Amazon RDS for Oracle.

Il vous est recommandé d’activer la copie en parallèle avec partitionnement des données notamment lorsque vous chargez une grande quantité de données à partir de votre base de données Amazon RDS for Oracle. Voici quelques suggestions de configurations pour différents scénarios. Lors de la copie de données dans un magasin de données basé sur des fichiers, il est recommandé de les écrire dans un dossier sous la forme de plusieurs fichiers (spécifiez uniquement le nom du dossier). Les performances seront meilleures qu’avec l’écriture dans un seul fichier.

| Scénario                                                     | Paramètres suggérés                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Chargement complet à partir d’une table volumineuse, avec des partitions physiques.          | **Option de partition** : Partitions physiques de la table. <br><br/>Pendant l’exécution, le service détecte automatiquement les partitions physiques et copie les données par partition. |
| Chargement complet d’une table volumineuse, sans partitions physiques, avec une colonne entière pour le partitionnement des données. | **Options de partition** : Partition dynamique par spécification de plages de valeurs.<br>**Colonne de partition** : Spécifiez la colonne utilisée pour partitionner les données. Si la valeur n’est pas spécifiée, la colonne de la clé primaire est utilisée. |
| Chargement d’une grande quantité de données à l’aide d’une requête personnalisée, avec des partitions physiques. | **Option de partition** : Partitions physiques de la table.<br>**Requête**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Nom de la partition** : Spécifiez le(s) nom(s) de partition à copier. Si ce n’est pas spécifié, le service détecte automatiquement les partitions physiques de la table que vous avez spécifiée dans le jeu de données Amazon RDS for Oracle.<br><br>Pendant l’exécution, le service remplace `?AdfTabularPartitionName` par le nom réel de la partition et l’envoie à Amazon RDS for Oracle. |
| Chargement d’une grande quantité de données à l’aide d’une requête personnalisée, sans partitions physiques, et avec une colonne entière pour le partitionnement des données. | **Options de partition** : Partition dynamique par spécification de plages de valeurs.<br>**Requête**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Colonne de partition** : Spécifiez la colonne utilisée pour partitionner les données. Vous pouvez procéder au partitionnement par rapport à la colonne avec le type de données entier.<br>**Limite supérieure de partition** et **limite inférieure de partition** : Indiquez si vous souhaitez filtrer le contenu par rapport à la colonne de partition pour récupérer uniquement les données entre les plages inférieure et supérieure.<br><br>Lors de l’exécution, le service remplace `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound` et `?AdfRangePartitionLowbound` par le nom de colonne et les plages de valeurs réels de chaque partition et les envoie à Amazon RDS for Oracle. <br>Par exemple, si votre colonne de partition « ID » est définie sur une limite inférieure de 1 et une limite supérieure de 80, avec une copie en parallèle définie sur 4, le service récupère les données via 4 partitions. Les ID sont inclus entre [1,20], [21, 40], [41, 60] et [61, 80], respectivement. |

> [!TIP]
> Lorsque vous copiez des données à partir d’une table non partitionnée, vous pouvez utiliser l’option de partition « Plage dynamique » afin de partitionner par rapport à une colonne d’entiers. Si vos données sources n’incluent pas un tel type de colonne, vous pouvez tirer parti de la fonction [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm) dans une requête source pour générer une colonne et l’utiliser comme colonne de partition.

**Exemple : requête avec partition physique**

```json
"source": {
    "type": "AmazonRdsForOracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Exemple : requête avec partition dynamique par spécification de plages de valeurs**

```json
"source": {
    "type": "AmazonRdsForOracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes
Consultez les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats) pour obtenir la liste des sources et magasins de données pris en charge en tant que récepteurs par l’activité de copie.
