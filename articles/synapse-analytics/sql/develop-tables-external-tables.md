---
title: Utiliser des tables externes avec Synapse SQL
description: Lecture ou écriture de fichiers de données avec des tables externes dans Synapse SQL
services: synapse-analytics
author: ma77b
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 07/23/2021
ms.author: maburd
ms.reviewer: wiassaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 14341d2c623ab465e054c83b7b47a100a52f8ece
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054625"
---
# <a name="use-external-tables-with-synapse-sql"></a>Utiliser des tables externes avec Synapse SQL

Une table externe pointe vers des données situées dans Hadoop, Stockage Blob Azure ou Azure Data Lake Storage. Les tables externes sont utilisées pour lire des données à partir de fichiers ou écrire des données dans des fichiers dans Stockage Azure. Avec Synapse SQL, vous pouvez utiliser des tables externes pour lire des données externes en utilisant le pool SQL dédié ou le pool SQL serverless.

Selon le type de la source de données externe, vous pouvez utiliser deux types de tables externes :
- Les tables externes Hadoop que vous pouvez utiliser pour lire et exporter des données dans différents formats de données tels que CSV, Parquet et ORC. Les tables externes Hadoop sont disponibles dans les pools SQL dédiés, mais pas dans les pools SQL serverless.
- Les tables externes natives que vous pouvez utiliser pour lire et exporter des données dans différents formats de données tels que CSV et Parquet. Des tables externes natives sont disponibles dans des pools SQL serverless. Elles sont en **préversion publique** dans des pools SQL dédiés.

Les principales différences entre les tables externes Hadoop et natives sont présentées dans le tableau suivant :

| Type de table externe | Hadoop | Natif |
| --- | --- | --- |
| Pool SQL dédié | Disponible | Des tables Parquet sont disponibles en **préversion publique**. |
| Pool SQL serverless | Non disponible | Disponible |
| Formats pris en charge | Délimité/CSV, Parquet, ORC, Hive RC et RC | Pool SQL serverless : Délimité/CSV, Parquet, et [Delta Lake (préversion)](query-delta-lake-format.md)<br/>Pool SQL dédié : Parquet |
| Élimination des partitions de dossier | Non | Uniquement pour les tables partitionnées synchronisées à partir de pools Apache Spark dans l’espace de travail Synapse vers des pools SQL serverless |
| Format personnalisé pour l’emplacement | Yes | Oui, avec des caractères génériques comme `/year=*/month=*/day=*` |
| Analyse récursive des dossiers | No | Uniquement dans les pools SQL serverless quand `/**` est spécifié à la fin du chemin d’accès d’emplacement |
| Pushdown du filtre de stockage | No | Oui, dans un pool SQL serverless. Pour le pushdown de chaîne, vous devez utiliser `Latin1_General_100_BIN2_UTF8` le classement sur les `VARCHAR` colonnes. |
| Authentification du stockage | Clé d’accès de stockage, authentification directe AAD, identité managée, identité Azure AD d’application personnalisée | Signature d’accès partagé (SAS), authentification directe AAD, identité managée |

> [!NOTE]
> Les tables externes natives au format Delta Lake sont en préversion publique. Pour plus d’informations, consultez [Interroger des fichiers Delta Lake (préversion)](query-delta-lake-format.md). [CETAS](develop-tables-cetas.md) ne prend pas en charge l’exportation de contenu au format Delta Lake.

## <a name="external-tables-in-dedicated-sql-pool-and-serverless-sql-pool"></a>Tables externes dans un pool SQL dédié et un pool SQL serverless

Vous pouvez utiliser des tables externes pour :

- Interroger Stockage Blob Azure et Azure Data Lake Gen2 avec des instructions Transact-SQL.
- Stocker les résultats de requête dans des fichiers dans le Stockage Blob Azure ou Azure Data Lake Storage à l’aide de [CETAS](develop-tables-cetas.md).
- Importer des données à partir du Stockage Blob Azure et d’Azure Data Lake Storage et les stocker dans un pool SQL dédié (tables Hadoop uniquement dans un pool dédié).

> [!NOTE]
> Utilisée conjointement avec l’instruction [CREATE TABLE AS SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context), la sélection à partir d’une table externe permet d’importer des données dans une table du pool SQL **dédié**. Outre [l’instruction COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true), les tables externes sont utiles pour le chargement de données. 
> 
> Pour obtenir un tutoriel sur le chargement, consultez [Utiliser PolyBase pour charger des données du Stockage Blob Azure](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

Vous pouvez créer des tables externes dans des pools Synapse SQL en effectuant les étapes suivantes :

1. [CRÉEZ UNE SOURCE DE DONNÉES EXTERNE](#create-external-data-source) pour référencer un stockage Azure externe, puis spécifiez les informations d’identification à utiliser pour accéder au stockage.
2. [CRÉEZ UN FORMAT DE FICHIER EXTERNE](#create-external-file-format) pour décrire le format des fichiers CSV ou Parquet.
3. [CRÉEZ UNE TABLE EXTERNE](#create-external-table) en plus des fichiers placés dans la source de données avec le même format de fichier.

### <a name="security"></a>Sécurité

L’utilisateur doit disposer de l’autorisation `SELECT` sur une table externe pour lire les données.
Les tables externes accèdent au stockage Azure sous-jacent à l’aide des informations d’identification étendues à la base de données définies dans la source de données selon les règles suivantes :
- La source de données sans informations d’identification permet aux tables externes d’accéder aux fichiers en disponibilité publique sur le stockage Azure.
- La source de données peut comporter des informations d’identification permettant aux tables externes d’accéder uniquement aux fichiers sur le stockage Azure à l’aide d’un jeton SAP ou de l’identité managée de l’espace de travail. Pour voir des exemples, consultez l’article [Développement du contrôle d’accès au stockage des fichiers de stockage](develop-storage-files-storage-access-control.md#examples).

## <a name="create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE

Les sources de données externes sont utilisées pour se connecter aux comptes de stockage. La documentation complète est présentée [ici](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true).

### <a name="syntax-for-create-external-data-source"></a>Syntaxe de CREATE EXTERNAL DATA SOURCE

#### <a name="hadoop"></a>[Hadoop](#tab/hadoop)

Les sources de données externes avec `TYPE=HADOOP` sont disponibles uniquement dans les pools SQL dédiés.

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
     , TYPE = HADOOP
)
[;]
```

#### <a name="native"></a>[Natif](#tab/native)

Les sources de données externes sans `TYPE=HADOOP` sont généralement disponibles dans les pools SQL serverless et en préversion publique dans les pools dédiés.

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
)
[;]
```

---

### <a name="arguments-for-create-external-data-source"></a>Arguments de CREATE EXTERNAL DATA SOURCE

#### <a name="data_source_name"></a>data_source_name

Spécifie le nom défini par l’utilisateur de la source de données. Le nom doit être unique au sein de la base de données.

#### <a name="location"></a>Emplacement
LOCATION = `'<prefix>://<path>'`   - Fournit le protocole de connectivité et le chemin de la source de données externe. Les modèles suivants peuvent être utilisés à cet emplacement :

| Source de données externe        | Préfixe de l’emplacement | Chemin d’emplacement                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Stockage Blob Azure          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Stockage Blob Azure          | `http[s]`       | `<storage_account>.blob.core.windows.net/<container>/subfolders` |
| Azure Data Lake Store Gén. 1 | `http[s]`       | `<storage_account>.azuredatalakestore.net/webhdfs/v1` |
| Azure Data Lake Store Gén. 2 | `http[s]`       | `<storage_account>.dfs.core.windows.net/<container>/subfolders`  |

Le préfixe `https:` vous permet d’utiliser un sous-dossier dans le chemin.

#### <a name="credential"></a>Informations d'identification
CREDENTIAL = `<database scoped credential>` est une information d’identification facultative qui sera utilisée pour l’authentification sur le stockage Azure. Une source de données externe sans informations d’identification peut accéder à un compte de stockage public ou utiliser l’identité Azure AD de l’appelant pour accéder aux fichiers sur le stockage. 
- Dans un pool SQL dédié, les informations d’identification limitées à la base de données peuvent spécifier une identité d’application personnalisée, une identité managée d’espace de travail ou une clé SAS. 
- Dans un pool SQL serverless, les informations d’identification limitées à la base de données peuvent spécifier une identité managée de l’espace de travail ou une clé SAS. 

#### <a name="type"></a>TYPE
TYPE = `HADOOP` est l’option qui spécifie que la technologie Java doit être utilisée pour l’accès aux fichiers sous-jacents. Ce paramètre ne peut pas être utilisé dans un pool SQL serverless qui utilise le lecteur natif intégré.

### <a name="example-for-create-external-data-source"></a>Exemple pour CREATE EXTERNAL DATA SOURCE

#### <a name="hadoop"></a>[Hadoop](#tab/hadoop)

L’exemple suivant crée une source de données externe Hadoop dans un pool SQL dédié pour Azure Data Lake Gen2 pointant vers le jeu de données New York :

```sql
CREATE DATABASE SCOPED CREDENTIAL [ADLS_credential]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

L’exemple suivant crée une source de données externe pour Azure Data Lake Gen2 pointant vers le jeu de données New York en disponibilité publique :

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/',
       TYPE = HADOOP)
```

#### <a name="native"></a>[Natif](#tab/native)

L’exemple suivant crée une source de données externe dans un pool SQL serverless ou dédié pour Azure Data Lake Gen2, accessible à l’aide des informations d’identification SAS :

```sql
CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

L’exemple suivant crée une source de données externe pour Azure Data Lake Gen2 pointant vers le jeu de données New York en disponibilité publique :

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Crée un objet de format de fichier externe qui définit des données externes stockées dans Stockage Blob Azure ou Azure Data Lake Storage. La création d’un format de fichier externe est un prérequis à la création d’une table externe. La documentation complète se trouve [ici](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true).

En créant un format de fichier externe, vous spécifiez la disposition des données référencées par une table externe.

### <a name="syntax-for-create-external-file-format"></a>Syntaxe de CREATE EXTERNAL FILE FORMAT

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
    | PARSER_VERSION = {'parser_version'}
}
```

### <a name="arguments-for-create-external-file-format"></a>Arguments de CREATE EXTERNAL FILE FORMAT

file_format_name - Spécifie un nom pour le format de fichier externe.

FORMAT_TYPE = [ PARQUET | DELIMITEDTEXT] - Spécifie le format des données externes.

- PARQUET - Spécifie un format Parquet.
- DELIMITEDTEXT - Spécifie un format de texte avec des délimiteurs de colonne, également appelés « marques de fin de champ ».

FIELD_TERMINATOR = *field_terminator* - S’applique uniquement aux fichiers texte délimité. La marque de fin de champ spécifie un ou plusieurs caractères qui indiquent la fin de chaque champ (colonne) dans le fichier texte délimité. La valeur par défaut est la barre verticale (ꞌ|ꞌ).

Exemples :

- FIELD_TERMINATOR = '|'
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR = ꞌ\tꞌ

STRING_DELIMITER = *string_delimiter* - Spécifie la marque de fin de champ pour les données de type chaîne du fichier texte délimité. Le délimiteur de chaîne est constitué d’un ou plusieurs caractères, et est entouré de guillemets simples. La valeur par défaut est la chaîne vide ("").

Exemples :

- STRING_DELIMITER = '"'
- STRING_DELIMITER = '*'
- STRING_DELIMITER = ꞌ,ꞌ

FIRST_ROW = *First_row_int* - Spécifie le numéro de ligne qui est lu en premier et s’applique à tous les fichiers. Si vous spécifiez la valeur 2, la première ligne de chaque fichier (ligne d’en-tête) est ignorée quand les données sont chargées. Les lignes sont ignorées en présence de marques de fin de ligne (/r/n, /r, /n).

USE_TYPE_DEFAULT = { TRUE | **FALSE** } - Spécifie comment gérer les valeurs manquantes dans les fichiers texte délimité lors de la récupération des données à partir du fichier texte.

TRUE - En cas de récupération de données à partir du fichier texte, stocker chaque valeur manquante à l’aide du type de données de la valeur par défaut pour la colonne correspondante dans la définition de la table externe. Par exemple, remplacez une valeur manquante par :

- 0 si la colonne est définie comme une colonne numérique. Les colonnes décimales ne sont pas prises en charge et provoquent une erreur.
- Une chaîne vide ("") si la colonne est une colonne de chaîne.
- 1900-01-01 si la colonne est une colonne de date.

FALSE - Stocker toutes les valeurs manquantes en tant que valeurs NULL. Toutes les valeurs NULL qui sont stockées à l’aide du mot NULL dans le fichier texte délimité sont importées en tant que chaînes NULL.

Encoding = {'UTF8' | 'UTF16'} - Un pool SQL serverless peut lire des fichiers texte délimités encodés aux formats UTF8 et UTF16.

DATA_COMPRESSION = *méthode_de_compression_de_données* - Cet argument spécifie la méthode de compression appliquée aux données externes. 

Le type de format PARQUET prend en charge les méthodes de compression suivantes :

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

Lors de la lecture de tables externes Parquet, cet argument est ignoré, mais il est utilisé lors de l’écriture dans des tables externes à l’aide de [CETAS](develop-tables-cetas.md).

Le type de format de fichier DELIMITEDTEXT prend en charge la méthode de compression suivante :

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'

PARSER_VERSION = 'parser_version' : spécifie la version d’analyseur à utiliser lors de la lecture de fichiers CSV. Les versions d’analyseur disponibles sont `1.0` et `2.0`. Cette option est disponible uniquement dans les pools SQL serverless.

### <a name="example-for-create-external-file-format"></a>Exemple pour CREATE EXTERNAL FILE FORMAT

L’exemple suivant crée un format de fichier externe pour des fichiers de recensement :

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>CREATE EXTERNAL TABLE

La commande CREATE EXTERNAL TABLE crée une table externe pour Synapse SQL afin d’accéder aux données stockées dans Stockage Blob Azure ou Azure Data Lake Storage. 

### <a name="syntax-for-create-external-table"></a>Syntaxe de CREATE EXTERNAL TABLE

```syntaxsql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
        [, TABLE_OPTIONS = N'{"READ_OPTIONS":["ALLOW_INCONSISTENT_READS"]}' ]
        [, <reject_options> [ ,...n ] ] 
    )
[;] 

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]

<reject_options> ::=  
{  
    | REJECT_TYPE = value,  
    | REJECT_VALUE = reject_value,  
    | REJECT_SAMPLE_VALUE = reject_sample_value,
    | REJECTED_ROW_LOCATION = '/REJECT_Directory'
}   
```

### <a name="arguments-create-external-table"></a>Arguments de CREATE EXTERNAL TABLE

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }*

Nom (composé d’une à trois parties) de la table à créer. Pour une table externe, un pool Synapse SQL stocke seulement les métadonnées de la table. Aucune donnée n’est déplacée ni stockée dans une base de données Synapse SQL.

<column_definition>, ...*n* ]

CREATE EXTERNAL TABLE prend en charge la possibilité de configurer le nom de colonne, le type de données et le classement. Vous ne pouvez pas utiliser DEFAULT CONSTRAINT sur des tables externes.

>[!IMPORTANT]
>Les définitions de colonne, notamment les types de données et le nombre de colonnes, doivent correspondre aux données des fichiers externes. En cas de non-correspondance, les lignes du fichier sont rejetées lors de l’interrogation des données réelles. Consultez les options REJECT pour contrôler le comportement des lignes rejetées.

Lors de la lecture à partir de fichiers Parquet, vous pouvez spécifier uniquement les colonnes que vous souhaitez lire et ignorer le reste.


LOCATION = '*folder_or_filepath*'

Spécifie le dossier, ou le chemin et le nom du fichier, où se trouvent les données dans Stockage Blob Azure. L’emplacement commence au dossier racine. Le dossier racine est l’emplacement de données qui est spécifié dans la source de données externe.

![Données récursives pour les tables externes](./media/develop-tables-external-tables/folder-traversal.png)

Contrairement aux tables externes Hadoop, les tables externes natives ne retournent pas de sous-dossiers, sauf si vous spécifiez /** à la fin du chemin. Dans cet exemple, si LOCATION='/webdata/', une requête de pool SQL serverless retourne des lignes de mydata.txt. Il ne retourne pas mydata2.txt et mydata3.txt, car ces fichiers se trouvent dans un sous-dossier. Les tables Hadoop retournent tous les fichiers de tous les sous-dossiers.

Les tables externes Hadoop et natives ignorent les fichiers dont le nom commence par un trait de soulignement (_) ou un point (.).


DATA_SOURCE = *external_data_source_name*

Spécifie le nom de la source de données externe contenant l’emplacement des données externes. Pour créer une source de données externe, utilisez [CREATE EXTERNAL DATA SOURCE](#create-external-data-source).


FILE_FORMAT = *external_file_format_name*

Spécifie le nom de l’objet de format de fichier externe qui stocke le type de fichier et la méthode de compression pour les données externes. Pour créer un format de fichier externe, utilisez [CREATE EXTERNAL FILE FORMAT](#create-external-file-format).

Options REJECT 

> [!NOTE]
> La fonctionnalité de lignes rejetées est en préversion publique.
> Notez que la fonctionnalité de lignes rejetées marche avec des fichiers texte délimités et PARSER_VERSION 1.0.

Vous pouvez spécifier les paramètres REJECT qui déterminent la façon dont le service traite les enregistrements *incorrects* qu’il récupère de la source de données externe. Un enregistrement de données est considéré comme « incorrect » si les types de données actuels ne correspondent pas aux définitions de colonne de la table externe.

Si vous ne spécifiez pas ou ne changez pas les options REJECT, le service utilise les valeurs par défaut. Ces informations sur les paramètres REJECT sont stockées en tant que métadonnées supplémentaires lorsque vous créez une table externe avec l’instruction CREATE EXTERNAL TABLE. Quand une prochaine instruction SELECT ou SELECT INTO SELECT sélectionne des données dans la table externe, le service utilise les options REJECT pour déterminer le nombre de lignes pouvant être rejetées avant de provoquer l’échec de la requête. La requête retourne des résultats (partiels) jusqu’à ce que le seuil de rejet soit dépassé. Ensuite, elle échoue avec le message d’erreur correspondant.


REJECT_TYPE = **value** 

Il s’agit de la seule valeur prise en charge pour le moment. Clarifie que l’option REJECT_VALUE est spécifiée en tant que valeur littérale.

value 

REJECT_VALUE est une valeur littérale. La requête échoue lorsque le nombre de lignes rejetées dépasse la valeur *reject_value*.

Par exemple, si REJECT_VALUE = 5 et REJECT_TYPE = value, la requête SELECT échoue après le rejet de cinq lignes.


REJECT_VALUE = *reject_value* 

Spécifie le nombre de lignes pouvant être rejetées avant l’échec de la requête.

Pour REJECT_TYPE = value, *reject_value* doit être un entier compris entre 0 et 2 147 483 647.


REJECTED_ROW_LOCATION = *Emplacement de répertoire*

Spécifie le répertoire dans la Source de données externe dans lequel les lignes rejetées et le fichier d’erreur correspondant doivent être écrits. Si le chemin spécifié n’existe pas, le service en crée un en votre nom. Un répertoire enfant est créé sous le nom « _rejectedrows ». Le caractère «_   » garantit que le répertoire est placé dans une séquence d’échappement pour le traitement d’autres données, sauf s’il est explicitement nommé dans le paramètre d’emplacement. Dans ce répertoire figure un dossier qui est créé d’après l’heure de soumission du chargement au format AnnéeMoisJour - HeureMinuteSeconde_IDInstruction (par exemple, 20180330-173205-559EE7D2-196D-400A-806D-3BF5D007F891). Vous pouvez utiliser l’ID d’instruction pour corréler le dossier avec la requête qui l’a généré. Dans ce dossier, deux fichiers sont écrits : le fichier error.json et le fichier de données. 

Le fichier error.json contient un tableau json avec des erreurs rencontrées qui sont liées aux lignes rejetées. Chaque élément représentant une erreur contient les attributs suivants :

| Attribut | Description                                                  |
| --------- | ------------------------------------------------------------ |
| Error     | Raison pour laquelle la ligne est rejetée.                                  |
| Ligne       | Nombre ordinal de lignes rejetées dans le fichier.                         |
| Colonne    | Nombre ordinal de colonnes rejetées.                              |
| Value     | Valeur des colonnes rejetées. Si la valeur est supérieure à 100 caractères, seuls les 100 premiers caractères seront affichés. |
| Fichier      | Chemin du fichier auquel la ligne appartient.                            |

#### <a name="table_options"></a>TABLE_OPTIONS

TABLE_OPTIONS = *options json* : spécifie l’ensemble d’options qui décrivent comment lire les fichiers sous-jacents. La seule option disponible est `"READ_OPTIONS":["ALLOW_INCONSISTENT_READS"]`. Celle-ci indique à la table externe d’ignorer les mises à jour appliquées aux fichiers sous-jacents, même si cela peut entraîner des opérations de lecture incohérentes. Utilisez cette option uniquement lorsque vous avez des fichiers auxquels sont fréquemment ajoutées des données. Cette option est disponible dans le pool SQL serverless pour le format CSV.

### <a name="permissions-create-external-table"></a>Autorisations pour CREATE EXTERNAL TABLE

Pour effectuer une sélection à partir d’une table externe, vous devez disposer des informations d’identification appropriées avec les autorisations de liste et de lecture.

### <a name="example-create-external-table"></a>Exemple pour CREATE EXTERNAL TABLE

L’exemple suivant crée une table externe : Il retourne la première ligne :

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Créer et interroger des tables externes à partir d’un fichier dans Azure Data Lake

Avec les fonctionnalités d’exploration de Data Lake offertes par Synapse Studio, vous pouvez désormais créer et interroger une table externe à l’aide d’un pool Synapse SQL avec un simple clic droit sur le fichier. Le clic simple qui permet de créer des tables externes à partir d’un compte de stockage ADLS Gen2 est possible uniquement pour les fichiers Parquet. 

### <a name="prerequisites"></a>Prérequis

- Vous devez avoir accès à l’espace de travail en ayant au moins le rôle d’accès `Storage Blob Data Contributor` au compte ADLS Gen2 ou aux listes du service Access Control qui vous permettent d’interroger les fichiers.

- Vous devez disposer au moins d’[autorisations de créer](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#permissions-2&preserve-view=true) et d’interroger des tables externes sur le pool Synapse SQL (dédié ou serverless).

Dans le panneau Données, sélectionnez le fichier à partir duquel vous souhaitez créer la table externe :
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

Une fenêtre de dialogue s’ouvre. Sélectionnez un pool SQL dédié ou un pool SQL serverless, donnez un nom à la table et sélectionnez Ouvrir le script :

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

Le script SQL est généré automatiquement en déduisant le schéma à partir du fichier :
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

Exécutez le script. Le script exécutera automatiquement un Select Top 100 *. :
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

La table externe est maintenant créée. Pour toute exploration ultérieure du contenu de cette table externe, l’utilisateur peut l’interroger directement à partir du volet Données :
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez l’article [CETAS](develop-tables-cetas.md) pour découvrir comment enregistrer des résultats de requête dans une table externe dans Stockage Azure. Vous pouvez sinon commencer à interroger des [Tables externes Apache Spark pour Azure Synapse](develop-storage-files-spark-tables.md).
