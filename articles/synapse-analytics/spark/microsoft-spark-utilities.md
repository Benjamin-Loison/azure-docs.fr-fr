---
title: Présentation des utilitaires Microsoft Spark
description: 'Tutoriel : MSSparkUtils dans les notebooks Azure Synapse Analytics'
author: ruixinxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.custom: subject-rbac-steps
ms.openlocfilehash: f5dba6b81befd569523111b997c29e54b3e82881
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774611"
---
# <a name="introduction-to-microsoft-spark-utilities"></a>Présentation des utilitaires Microsoft Spark

Microsoft Spark Utilities (MSSparkUtils) est un package intégré qui vous permet d’effectuer aisément des tâches courantes. Vous pouvez utiliser MSSparkUtils pour travailler efficacement avec des systèmes de fichiers, pour récupérer des variables d’environnement, chaîner des notebooks et utiliser des secrets. MSSparkUtils est disponible dans les notebooks `PySpark (Python)`, `Scala` et `.NET Spark (C#)` et les pipelines Synapse.

## <a name="pre-requisites"></a>Conditions préalables

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Configurer l’accès à Azure Data Lake Storage Gen2 

Les notebooks Synapse utilisent le Pass-through Azure Active Directory (Azure AD) pour accéder aux comptes ADLS Gen2. Vous devez être un **Contributeur aux données Blob de stockage** pour accéder au compte (ou au dossier) ADLS Gen2. 

Les pipelines Synapse utilisent l’identité MSI (Managed Service Identity) de l’espace de travail pour accéder aux comptes de stockage. Pour utiliser MSSparkUtils dans vos activités de pipeline, l'identité de votre espace de travail doit être un **Contributeur aux données Blob de stockage** pour accéder au compte (ou au dossier) ADLS Gen2.

Procédez comme suit pour vous assurer que votre compte Azure AD et votre espace de travail MSI ont accès au compte ADLS Gen2 :
1. Ouvrez le [portail Azure](https://portal.azure.com/) et le compte de stockage auquel vous souhaitez accéder. Vous pouvez accéder au conteneur spécifique auquel vous souhaitez accéder.
1. Dans le volet de gauche, sélectionnez **Contrôle d’accès (IAM)** .
1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.
1. Attribuez le rôle suivant. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).
    
    | Paramètre | Valeur |
    | --- | --- |
    | Role | Contributeur aux données Blob du stockage |
    | Attribuer l’accès à | USER et MANAGEDIDENTITY |
    | Membres | Votre compte Azure AD et votre identité d’espace de travail |

    > [!NOTE]
    > Le nom de l’identité managée correspond également au nom de l’espace de travail.

    ![Page Ajouter une attribution de rôle dans le portail Azure.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)
 
1. Sélectionnez **Enregistrer**.

Vous pouvez accéder aux données sur ADLS Gen2 à l’aide de Synapse Spark via l’URL suivante :

`abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path>`

### <a name="configure-access-to-azure-blob-storage"></a>Configurer l’accès à Stockage Blob Azure  

Synapse utilise la [**signature d’accès partagé (SAP)**](../../storage/common/storage-sas-overview.md) pour accéder à Stockage Blob Azure. Pour éviter d’exposer des clés SAP dans le code, nous vous recommandons de créer un nouveau service lié dans l’espace de travail Synapse sur le compte Stockage Blob Azure auquel vous souhaitez accéder.

Procédez comme suit pour ajouter un nouveau service lié pour un compte Stockage Blob Azure :

1. Ouvrez [Azure Synapse Studio](https://web.azuresynapse.net/).
2. Sélectionnez **Gérer** dans le volet gauche, puis **Services liés** sous **Connexions externes**.
3. Recherchez **Stockage Blob Azure** dans le panneau **Nouveau service lié** à droite.
4. Sélectionnez **Continuer**.
5. Sélectionnez le compte Stockage Blob Azure pour y accéder et configurer le nom du service lié. Suggérer l’utilisation d’une **clé de compte** pour la **méthode d’authentification**.
6. Sélectionnez **Tester la connexion** pour vérifier que les paramètres sont corrects.
7. Sélectionnez **Créer**, puis sur **Publier tout** pour enregistrer vos modifications. 

Vous pouvez accéder aux données sur Stockage Blob Azure à l’aide de Synapse Spark via l’URL suivante :

`wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path>`

Voici un exemple de code :

:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val blob_account_name = "" // replace with your blob name
val blob_container_name = "" //replace with your container name
val blob_relative_path = "/" //replace with your relative folder path
val linked_service_name = "" //replace with your linked service name


val blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

val wasbs_path = f"wasbs://$blob_container_name@$blob_account_name.blob.core.windows.net/$blob_relative_path"
spark.conf.set(f"fs.azure.sas.$blob_container_name.$blob_account_name.blob.core.windows.net",blob_sas_token)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var blob_account_name = "";  // replace with your blob name
var blob_container_name = "";     // replace with your container name
var blob_relative_path = "";  // replace with your relative folder path
var linked_service_name = "";    // replace with your linked service name
var blob_sas_token = Credentials.GetConnectionStringOrCreds(linked_service_name);

spark.SparkContext.GetConf().Set($"fs.azure.sas.{blob_container_name}.{blob_account_name}.blob.core.windows.net", blob_sas_token);

var wasbs_path = $"wasbs://{blob_container_name}@{blob_account_name}.blob.core.windows.net/{blob_relative_path}";

Console.WriteLine(wasbs_path);

```

::: zone-end 
 
###  <a name="configure-access-to-azure-key-vault"></a>Configurer l’accès à Azure Key Vault

Vous pouvez ajouter un compte Azure Key Vault en tant que service lié pour gérer vos informations d’identification dans Synapse. Pour ajouter un compte Azure Key Vault en tant que service lié Synapse, procédez comme suit :
1. Ouvrez [Azure Synapse Studio](https://web.azuresynapse.net/).
2. Sélectionnez **Gérer** dans le volet gauche, puis **Services liés** sous **Connexions externes**.
3. Recherchez **Azure Key Vault** dans le panneau **Nouveau service lié** à droite.
4. Sélectionnez le compte Azure Key Vault pour y accéder et configurer le nom du service lié.
5. Sélectionnez **Tester la connexion** pour vérifier que les paramètres sont corrects.
6. Sélectionnez **Créer**, puis sur **Publier tout** pour enregistrer vos modifications. 

Les notebooks Synapse utilisent le Pass-through Azure Active Directory (Azure AD) pour accéder à Azure Key Vault. Les pipelines Synapse utilisent l’identité de l’espace de travail (MSI) pour accéder à Azure Key Vault. Pour vous assurer que votre code fonctionne à la fois dans le notebook et dans le pipeline Synapse, nous vous recommandons d’accorder une autorisation d’accès secret à la fois à votre compte Azure AD et à votre identité d’espace de travail.

Procédez comme suit pour accorder un accès secret à votre identité d’espace de travail :
1. Ouvrez le [portail Azure](https://portal.azure.com/) et le compte Azure Key Vault auquel vous souhaitez accéder. 
2. Dans le panneau gauche, sélectionnez **Stratégies d’accès**.
3. Sélectionnez **Ajouter une stratégie d’accès** : 
    - Choisissez **Gestion des clés, des secrets et des certificats** en tant que modèle de configuration.
    - Sélectionnez **votre compte Azure AD** et **votre identité d’espace de travail** (identique au nom de votre espace de travail) dans le principal sélectionné ou assurez-vous qu’il est déjà attribué. 
4. Choisissez **Sélectionner** et **Ajouter**.
5. Cliquez sur le bouton **Enregistrer** pour valider les modifications.  

## <a name="file-system-utilities"></a>Utilitaires du système de fichiers

`mssparkutils.fs` fournit des utilitaires pour travailler avec différents systèmes de fichiers, notamment Azure Data Lake Storage Gen2 (ADLS Gen2) et Stockage Blob Azure. Veillez à configurer l’accès à [Azure Data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) et [Stockage Blob Azure](#configure-access-to-azure-blob-storage) de manière appropriée.

Exécutez les commandes suivantes pour obtenir une vue d’ensemble des méthodes disponibles :

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

Retourne comme résultat :
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>Énumérer des fichiers
Répertorie le contenu d’un répertoire.


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>Affichez les propriétés de fichier
Retourne les propriétés du fichier, notamment le nom du fichier, son chemin d’accès, sa taille, s’il s’agit d’un répertoire et s’il s’agit d’un fichier.

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>Créer un répertoire

Crée le répertoire donné s’il n’existe pas, ainsi que les répertoires parents nécessaires.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>Copier le fichier

Copie un fichier ou un répertoire. Prend en charge la copie sur plusieurs systèmes de fichiers.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>Afficher un aperçu du contenu du fichier

Renvoie jusqu’aux premiers octets « maxBytes » du fichier donné sous la forme d’une chaîne encodée au format UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>Déplacer le fichier

Déplace un fichier ou un répertoire. Prend en charge le déplacement sur plusieurs systèmes de fichiers.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>Écrire dans un fichier

Écrit la chaîne donnée dans un fichier encodé au format UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>Ajouter du contenu à un fichier

Ajoute la chaîne donnée à un fichier, encodé au format UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append("file path", "content to append", True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path", "content to append", true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>Supprimer un fichier ou un répertoire

Supprime un fichier ou un répertoire.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end



## <a name="notebook-utilities"></a>Utilitaires de notebook 

:::zone pivot = "programming-language-csharp"

Non pris en charge.

::: zone-end

:::zone pivot = "programming-language-python"

Vous pouvez utiliser les utilitaires notebook MSSparkUtils pour exécuter un notebook ou quitter un notebook avec une valeur. Exécutez la commande suivante pour obtenir une vue d’ensemble des méthodes disponibles :

```python
mssparkutils.notebook.help()
```

Obtenir les résultats :
```
The notebook module.

exit(value: String): void -> This method lets you exit a notebook with a value.
run(path: String, timeoutSeconds: int, arguments: Map): String -> This method runs a notebook and returns its exit value.

```

### <a name="reference-a-notebook"></a>Référencer un notebook
Référence un notebook et renvoie sa valeur de sortie. Vous pouvez exécuter des appels de fonction d’imbrication dans un notebook de manière interactive ou dans un pipeline. Le notebook référencé s’exécutera sur le pool Spark duquel le notebook appelle cette fonction.  

```python

mssparkutils.notebook.run("notebook path", <timeoutSeconds>, <parameterMap>)

```

Par exemple :

```python
mssparkutils.notebook.run("folder/Sample1", 90, {"input": 20 })
```

### <a name="exit-a-notebook"></a>Quitter un notebook
Quitte un notebook avec une valeur. Vous pouvez exécuter des appels de fonction d’imbrication dans un notebook de manière interactive ou dans un pipeline. 

- Lorsque vous appelez une fonction `exit()` de notebook de manière interactive, Azure Synapse lève une exception, ignore l’exécution des cellules de la sous-séquence et maintient la session Spark active.

- Lorsque vous orchestrez un notebook qui appelle une fonciton `exit()` dans un pipeline Synapse, Azure Synapse renvoie une valeur de sortie, termine l’exécution du pipeline et arrête la session Spark.  

- Lorsque vous appelez une fonction `exit()` dans un notebook référencé, Azure Synapse arrête l’exécution supplémentaire dans le notebook référencé et continue à exécuter les cellules suivantes du notebook qui appellent la fonction `run()`. Par exemple : Notebook1 possède trois cellules et appelle une fonction `exit()` dans la deuxième cellule. Notebook2 possède cinq cellules et appelle `run(notebook1)` dans la troisième cellule. Lorsque vous exécutez Notebook2, Notebook1 est arrêté à la deuxième cellule lorsque vous atteignez la fonction `exit()`. Notebook2 continue à exécuter sa quatrième cellule et sa cinquième cellule. 


```python
mssparkutils.notebook.exit("value string")
```

Par exemple :

Le notebook **Sample1** se trouve sous **folder/** avec les deux cellules : 
- La cellule 1 définit un paramètre **d’entrée** dont la valeur par défaut est définie sur 10.
- La cellule 2 quitte le notebook avec l'**entrée** comme valeur de sortie. 

![Capture d’écran d’un exemple de notebook](./media/microsoft-spark-utilities/spark-utilities-run-notebook-sample.png)

Vous pouvez exécuter **Sample1** dans un autre bloc-notes avec les valeurs par défaut :

```python

exitVal = mssparkutils.notebook.run("folder/Sample1")
print (exitVal)

```
Retourne comme résultat :

```
Sample1 run success with input is 10
```

Vous pouvez exécuter **Sample1** dans un autre notebook et définir la valeur d’**entrée** sur 20 :

```python
exitVal = mssparkutils.notebook.run("mssparkutils/folder/Sample1", 90, {"input": 20 })
print (exitVal)
```

Retourne comme résultat :

```
Sample1 run success with input is 20
```
::: zone-end

:::zone pivot = "programming-language-scala"

Vous pouvez utiliser les utilitaires notebook MSSparkUtils pour exécuter un notebook ou quitter un notebook avec une valeur. Exécutez la commande suivante pour obtenir une vue d’ensemble des méthodes disponibles :

```scala
mssparkutils.notebook.help()
```

Obtenir les résultats :
```
The notebook module.

exit(value: String): void -> This method lets you exit a notebook with a value.
run(path: String, timeoutSeconds: int, arguments: Map): String -> This method runs a notebook and returns its exit value.

```

### <a name="reference-a-notebook"></a>Référencer un notebook
Référence un notebook et renvoie sa valeur de sortie. Vous pouvez exécuter des appels de fonction d’imbrication dans un notebook de manière interactive ou dans un pipeline. Le notebook référencé s’exécutera sur le pool Spark duquel le notebook appelle cette fonction.  

```scala

mssparkutils.notebook.run("notebook path", <timeoutSeconds>, <parameterMap>)

```

Par exemple :

```scala
mssparkutils.notebook.run("folder/Sample1", 90, {"input": 20 })
```

### <a name="exit-a-notebook"></a>Quitter un notebook
Quitte un notebook avec une valeur. Vous pouvez exécuter des appels de fonction d’imbrication dans un notebook de manière interactive ou dans un pipeline. 

- Lorsque vous appelez une fonction `exit()` de notebook de manière interactive, Azure Synapse lève une exception, ignore l’exécution des cellules de la sous-séquence et maintient la session Spark active.

- Lorsque vous orchestrez un notebook qui appelle une fonciton `exit()` dans un pipeline Synapse, Azure Synapse renvoie une valeur de sortie, termine l’exécution du pipeline et arrête la session Spark.  

- Lorsque vous appelez une fonction `exit()` dans un notebook référencé, Azure Synapse arrête l’exécution supplémentaire dans le notebook référencé et continue à exécuter les cellules suivantes du notebook qui appellent la fonction `run()`. Par exemple : Notebook1 possède trois cellules et appelle une fonction `exit()` dans la deuxième cellule. Notebook2 possède cinq cellules et appelle `run(notebook1)` dans la troisième cellule. Lorsque vous exécutez Notebook2, Notebook1 est arrêté à la deuxième cellule lorsque vous atteignez la fonction `exit()`. Notebook2 continue à exécuter sa quatrième cellule et sa cinquième cellule. 


```python
mssparkutils.notebook.exit("value string")
```

Par exemple :

Le notebook **Sample1** est situé sous **mssparkutils/dossier/** avec les deux cellules suivantes : 
- La cellule 1 définit un paramètre **d’entrée** dont la valeur par défaut est définie sur 10.
- La cellule 2 quitte le notebook avec l'**entrée** comme valeur de sortie. 

![Capture d’écran d’un exemple de notebook](./media/microsoft-spark-utilities/spark-utilities-run-notebook-sample.png)

Vous pouvez exécuter **Sample1** dans un autre bloc-notes avec les valeurs par défaut :

```scala

val exitVal = mssparkutils.notebook.run("mssparkutils/folder/Sample1")
print(exitVal)

```
Retourne comme résultat :

```
exitVal: String = Sample1 run success with input is 10
Sample1 run success with input is 10
```


Vous pouvez exécuter **Sample1** dans un autre notebook et définir la valeur d’**entrée** sur 20 :

```scala
val exitVal = mssparkutils.notebook.run("mssparkutils/folder/Sample1", 90, {"input": 20 })
print(exitVal)
```

Retourne comme résultat :

```
exitVal: String = Sample1 run success with input is 20
Sample1 run success with input is 20
```
::: zone-end


## <a name="credentials-utilities"></a>Utilitaires d’informations d’identification

Vous pouvez utiliser les utilitaires d’informations d’identification MSSparkUtils pour récupérer les jetons d’accès des services liés et gérer les secrets dans Azure Key Vault. 

Exécutez la commande suivante pour obtenir une vue d’ensemble des méthodes disponibles :

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.Help()
```

::: zone-end

Vous obtenez le résultat suivant :

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>Obtenir un jeton

Renvoie un jeton Azure AD pour un public donné, (nom facultatif). Le tableau ci-dessous répertorie tous les types d’audience disponibles : 

|Type d’audience|Clé d’audience|
|--|--|
|Type de résolution de l’audience|« Audience »|
|Ressource d’audience de stockage|« Storage »|
|Pools SQL dédiés (Data Warehouse)|« DW »|
|Ressource d’audience de lac de données|« AzureManagement »|
|Ressource d’audience de coffre|« DataLakeStore »|
|Ressource d’audience Azure OSSDB|« AzureOSSDB »|
|Ressource Azure Synapse|« Synapse »|
|Ressource Azure Data Factory|« ADF »|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetToken("audience Key")
```

::: zone-end


### <a name="validate-token"></a>Valider un jeton

Renvoie la valeur true si le jeton n’a pas expiré.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.IsValidToken("your token")
```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>Obtenir la chaîne de connexion ou les informations d’identification du service lié

Renvoie la chaîne de connexion ou les informations d’identification du service lié. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetConnectionStringOrCreds("linked service name")
```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>Obtenir le secret à l’aide de l’identité de l’espace de travail

Renvoie le secret Azure Key Vault pour un nom Azure Key Vault donné, un nom de secret et un nom de service lié à l’aide de l’identité de l’espace de travail. Veillez à configurer l’accès à [Azure Key Vault](#configure-access-to-azure-key-vault) de manière appropriée.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name","linked service name")
```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>Obtenir le secret à l’aide des informations d’identification de l’utilisateur

Renvoie le secret Azure Key Vault pour un nom Azure Key Vault donné, un nom de secret et un nom de service lié à l’aide des informations d’identification de l’utilisateur. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name")
```

::: zone-end

<!-- ### Put secret using workspace identity

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using workspace identity. Make sure you configure the access to [Azure Key Vault](#configure-access-to-azure-key-vault) appropriately. -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-workspace-identity"></a>Placer le secret à l’aide de l’identité de l’espace de travail

Place le secret Azure Key Vault pour un nom Azure Key Vault donné, un nom de secret et un nom de service lié à l’aide de l’identité de l’espace de travail. Veillez à configurer l’accès à [Azure Key Vault](#configure-access-to-azure-key-vault) de manière appropriée.

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-workspace-identity"></a>Placer le secret à l’aide de l’identité de l’espace de travail

Place le secret Azure Key Vault pour un nom Azure Key Vault donné, un nom de secret et un nom de service lié à l’aide de l’identité de l’espace de travail. Veillez à configurer l’accès à [Azure Key Vault](#configure-access-to-azure-key-vault) de manière appropriée.

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


<!-- ### Put secret using user credentials

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using user credentials.  -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-user-credentials"></a>Placer le secret à l’aide des informations d’identification de l’utilisateur

Place le secret Azure Key Vault pour un nom Azure Key Vault donné, un nom de secret et un nom de service lié à l’aide des informations d’identification de l’utilisateur. 

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-user-credentials"></a>Placer le secret à l’aide des informations d’identification de l’utilisateur

Place le secret Azure Key Vault pour un nom Azure Key Vault donné, un nom de secret et un nom de service lié à l’aide des informations d’identification de l’utilisateur. 

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


## <a name="environment-utilities"></a>Utilitaires d’environnement 

Exécutez la commande suivante pour obtenir une vue d’ensemble des méthodes disponibles :

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.Help()
```

::: zone-end

Vous obtenez le résultat suivant :
```
GetUserName(): returns user name
GetUserId(): returns unique user id
GetJobId(): returns job id
GetWorkspaceName(): returns workspace name
GetPoolName(): returns Spark pool name
GetClusterId(): returns cluster id
```

### <a name="get-user-name"></a>Obtient le nom d’utilisateur

Renvoie le nom de l’utilisateur actuel.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserName()
```

::: zone-end

### <a name="get-user-id"></a>Obtenir l’identifiant utilisateur

Renvoie l’identifiant utilisateur actuel.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserId()
```

::: zone-end

### <a name="get-job-id"></a>Obtenir l’ID de tâche

Renvoie l’ID de tâche.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetJobId()
```

::: zone-end

### <a name="get-workspace-name"></a>Obtenir le nom de l’espace de travail

Renvoie le nom de l’espace de travail.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetWorkspaceName()
```

::: zone-end

### <a name="get-pool-name"></a>Obtenir le nom du pool

Renvoie le nom du pool Spark.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetPoolName()
```

::: zone-end

### <a name="get-cluster-id"></a>Obtenir l’ID de cluster

Renvoie l’ID de cluster actuel.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetClusterId()
```

::: zone-end


## <a name="runtime-context"></a>Contexte d’exécution

Les utilitaires d’exécution Mssparkutils ont exposé trois propriétés d’exécution. Vous pouvez utiliser le contexte d’exécution mssparkutils pour récupérer les propriétés répertoriées comme ci-dessous :
- **Notebookname** : nom du notebook actuel, renvoie toujours la valeur pour le mode interactif et le mode pipeline.
- **Pipelinejobid** : ID d’exécution du pipeline, renvoie la valeur en mode pipeline et renvoie une chaîne vide en mode interactif.
- **Activityrunid** : ID d’exécution de l’activité de notebook, renvoie la valeur en mode pipeline et renvoie une chaîne vide en mode interactif.

Actuellement, le contexte d’exécution prend en charge Python et Scala.

:::zone pivot = "programming-language-python"

```python
mssparkutils.runtime.context
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
%%spark
mssparkutils.runtime.context
```
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

- [Consultez les exemples de notebooks Synapse](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Démarrage rapide : Créer un pool Apache Spark dans Azure Synapse Analytics avec des outils web](../quickstart-apache-spark-notebook.md)
- [Présentation d’Apache Spark dans Azure Synapse Analytics](apache-spark-overview.md)
- [Azure Synapse Analytics](../index.yml)
