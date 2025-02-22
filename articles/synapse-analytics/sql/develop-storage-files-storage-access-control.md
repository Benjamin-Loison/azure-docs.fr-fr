---
title: Contrôler l’accès au compte de stockage pour le pool SQL serverless
description: Décrit comment le pool SQL serverless accède à Stockage Azure et comment vous pouvez contrôler l’accès au stockage pour le pool SQL serverless dans Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9e610e7ec02ec16d077087dab4742721c4209bfa
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234848"
---
# <a name="control-storage-account-access-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Contrôler l’accès au compte de stockage pour le pool SQL serverless dans Azure Synapse Analytics

Une requête de pool SQL serverless lit les fichiers directement dans Stockage Azure. Les autorisations d’accès aux fichiers sur le stockage Azure sont contrôlées à deux niveaux :
- **Niveau de stockage** : l’utilisateur doit avoir l’autorisation d’accéder aux fichiers de stockage sous-jacents. Votre administrateur de stockage doit autoriser le principal Azure AD à lire/écrire des fichiers, ou à générer une clé SAP qui sera utilisée pour accéder au stockage.
- **Niveau de service SQL** - L’utilisateur doit se voir attribuer l’autorisation de lire les données en utilisant une [table externe](develop-tables-external-tables.md) ou d’exécuter la fonction `OPENROWSET`. Explorez plus en détail [les autorisations requises dans cette section](develop-storage-files-overview.md#permissions).

Cet article décrit les types d’informations d’identification que vous pouvez utiliser et la façon dont la recherche des informations d’identification est appliquée pour les utilisateurs SQL et Azure AD.

## <a name="storage-permissions"></a>Autorisations de stockage

Un pool SQL serverless dans l’espace de travail Synapse Analytics peut lire le contenu des fichiers stockés dans Azure Data Lake Storage. Vous devez configurer des autorisations sur le stockage pour permettre à un utilisateur qui exécute une requête SQL de lire les fichiers. Il existe trois méthodes pour activer l’accès aux fichiers :
- Le **[contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/overview.md)** vous permet d’attribuer un rôle à un utilisateur Azure AD dans le locataire où votre stockage est placé. Un lecteur doit avoir le rôle RBAC `Storage Blob Data Reader`, `Storage Blob Data Contributor` ou `Storage Blob Data Owner` sur un compte de stockage. Un utilisateur qui écrit des données dans le stockage Azure doit disposer du rôle `Storage Blob Data Writer` ou `Storage Blob Data Owner`. Notez que le rôle `Storage Owner` ne signifie pas qu’un utilisateur est également `Storage Data Owner`.
- **Les listes de contrôle d’accès (ACL, Access Control Lists)** vous permettent de définir des [autorisations Lecture (R), Écriture (W) et Exécution (X)](../../storage/blobs/data-lake-storage-access-control.md#levels-of-permission) affinées sur les fichiers et les répertoires du stockage Azure. Une liste ACL peut être attribuée aux utilisateurs Azure AD. Si les lecteurs veulent lire un fichier sur un chemin dans le Stockage Azure, ils doivent avoir l’autorisation Exécuter (X) la liste de contrôle d’accès (ACL) sur chaque dossier présent dans le chemin du fichier, et Lire (R) la liste ACL sur le fichier. [Découvrez-en plus sur la définition d’autorisations de liste de contrôle d’accès (ACL) dans la couche de stockage](../../storage/blobs/data-lake-storage-access-control.md#how-to-set-acls).
- La **signature d’accès partagé (SAS)** permet à un lecteur d’accéder aux fichiers sur le stockage Azure Data Lake à l’aide du jeton à durée limitée. Le lecteur n’a même pas besoin d’être authentifié en tant qu’utilisateur Azure AD. Le jeton SAS contient les autorisations accordées au lecteur, ainsi que la durée pendant laquelle le jeton est valide. Le jeton SAS représente un bon choix pour un accès à durée restreinte d’un utilisateur qui n’est même pas obligé de se trouver dans le même locataire Azure AD. Le jeton SAS peut être défini sur le compte de stockage ou sur des annuaires spécifiques. Apprenez-en davantage sur l’[octroi d’un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé](../../storage/common/storage-sas-overview.md).

Vous pouvez également rendre vos fichiers disponibles publiquement en autorisant l’accès anonyme. Cette approche NE doit PAS être utilisée si vous avez des données non publiques. 

## <a name="supported-storage-authorization-types"></a>Types d’autorisations de stockage pris en charge

Un utilisateur qui s’est connecté à un pool SQL serverless doit être autorisé à accéder aux fichiers présents dans Stockage Azure et à les interroger si les fichiers ne sont pas publiquement disponibles. Vous pouvez utiliser quatre types d’autorisation pour accéder au stockage non public : [Identité de l’utilisateur](?tabs=user-identity), [Signature d’accès partagé](?tabs=shared-access-signature), [Principal de service](?tab/service-principal) et [Identité managée](?tabs=managed-identity).

> [!NOTE]
> Le **pass-through Azure AD** est le comportement qui est utilisé par défaut quand vous créez un espace de travail.

### <a name="user-identity"></a>[Identité de l’utilisateur](#tab/user-identity)

L’**identité de l’utilisateur** (également appelée « pass-through Azure AD ») est un type d’autorisation où l’identité de l’utilisateur Azure AD qui s’est connecté au pool SQL serverless est utilisée pour autoriser l’accès aux données. Avant d’accéder aux données, l’administrateur du stockage Azure doit accorder des autorisations à l’utilisateur Azure AD. Comme indiqué dans le tableau ci-dessous, elle n’est pas prise en charge pour le type d’utilisateur SQL.

> [!IMPORTANT]
> Le jeton d'authentification AAD peut être mis en cache par les applications clientes. Par exemple, PowerBI met en cache le jeton AAD et réutilise le même jeton pendant une heure. Les requêtes durables peuvent échouer si le jeton expire pendant leur exécution. Si des requêtes échouent parce que le jeton d’accès AAD expire au milieu de celles-ci, envisagez de passer au [principal de service](develop-storage-files-storage-access-control.md?tabs=service-principal#supported-storage-authorization-types) ,à l’[identité managée](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types) ou à la [signature d’accès partagé](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#supported-storage-authorization-types).

Pour utiliser votre identité afin d’accéder aux données, vous devez disposer d’un rôle de propriétaire, de contributeur ou de lecteur pour les données blob de stockage. Vous pouvez également spécifier des règles ACL affinées pour accéder aux fichiers et aux dossiers. Même si vous êtes propriétaire d’un compte de stockage, vous devez vous ajouter à l’un des rôles de données blob de stockage.
Pour plus d’informations sur le contrôle d’accès dans Azure Data Lake Store Gen2, consultez l’article [Contrôle d’accès dans Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md).


### <a name="shared-access-signature"></a>[Signature d’accès partagé](#tab/shared-access-signature)

Une **signature d’accès partagé (SAS)** fournit un accès délégué aux ressources du compte de stockage. Avec une signature d’accès partagé, un utilisateur peut accorder aux clients l’accès aux ressources d’un compte de stockage sans partager les clés du compte. Une signature d’accès partagé vous fournit un contrôle précis sur le type d’accès que vous accordez aux clients qui disposent de la signature SAS, comme l’intervalle de validité, les autorisations accordées, la plage d’adresses IP acceptée ou le protocole accepté (https/http).

Vous pouvez obtenir un jeton SAS en accédant au **portail Azure -> Compte de stockage -> Signature d’accès partagé -> Configurer les autorisations -> Générer la chaîne de connexion et SAP**.

> [!IMPORTANT]
> Lorsqu’un jeton SAS est généré, son nom commence par un point d’interrogation (« ? »). Pour utiliser le jeton dans un pool SQL serverless, vous devez supprimer le point d’interrogation (« ? ») lors de la création des informations d’identification. Par exemple :
>
> Jeton SAS : ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

Pour autoriser l’accès à l’aide du jeton SAP, vous devez créer des informations d’identification incluses dans l’étendue de la base de données ou du serveur 


> [!IMPORTANT]
> Vous ne pouvez pas accéder aux comptes de stockage privés avec le jeton SAS. Envisagez de passer à l'[identité managée](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types) ou à l'[authentification directe Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) pour accéder au stockage protégé.


### <a name="service-principal"></a>[Principal du service](#tab/service-principal)
Un **principal de service** est la représentation locale d’un objet d’application global dans un locataire Azure AD particulier. Cette méthode d’authentification est appropriée quand l’accès au stockage doit être autorisé pour une application utilisateur, un service ou un outil d’automatisation. 

L’application doit être inscrite auprès d’Azure Active Directory. Pour le processus d’inscription, vous pouvez consulter [Démarrage rapide : Inscrire une application avec la plateforme d’identités Microsoft](../../active-directory/develop/quickstart-register-app.md). Une fois l’application inscrite, son principal de service peut être utilisé pour l’autorisation. 

Pour que l’application puisse accéder aux données, le principal de service doit avoir le rôle Contributeur, Lecteur ou Propriétaire des données blob du stockage. Même si le principal de service est propriétaire d’un compte de stockage, il doit toujours disposer d’un rôle approprié sur les données blob du stockage. Une autre façon d’accorder l’accès aux fichiers et aux dossiers de stockage consiste à définir des règles ACL affinées pour le principal de service. Pour plus d’informations sur le contrôle d’accès dans Azure Data Lake Store Gen2, consultez l’article [Contrôle d’accès dans Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md).

### <a name="managed-identity"></a>[Identité gérée](#tab/managed-identity)

L’**identité managée** est également connue sous le nom de MSI. Il s’agit d’une fonctionnalité Azure Active Directory (Azure AD) qui fournit des services Azure pour un pool SQL serverless. En outre, elle déploie automatiquement une identité managée dans Azure AD. Cette identité peut être utilisée pour autoriser les demandes d’accès aux données dans le stockage Azure.

Avant d’accéder aux données, l’administrateur du stockage Azure doit accorder des autorisations à l’identité managée pour accéder aux données. L’octroi d’autorisations à une identité managée s’effectue de la même façon que l’octroi d’une autorisation à un autre utilisateur Azure AD.

### <a name="anonymous-access"></a>[Accès anonyme](#tab/public-access)

Vous pouvez accéder aux fichiers publiquement disponibles placés sur des comptes de stockage Azure qui [autorisent l’accès anonyme](../../storage/blobs/anonymous-read-access-configure.md).

---

#### <a name="cross-tenant-scenarios"></a>Scénarios inter-locataires
Si le stockage Azure se trouve dans un locataire différent du pool SQL serverless Synapse, l’autorisation par le biais du **principal de service** est la méthode recommandée. L’autorisation **SAS** est également possible, mais l’**identité managée** n’est pas prise en charge. 

### <a name="supported-authorization-types-for-databases-users"></a>Types d’autorisation pris en charge pour les utilisateurs de bases de données

Dans le tableau ci-dessous, vous trouverez les types d’autorisation disponibles pour différentes méthodes de connexion au point de terminaison SQL Serverless Synapse :

| Type d’autorisation                    | *Utilisateur SQL*    | *Utilisateur Azure AD*     | *Principal du service* |
| ------------------------------------- | ------------- | -----------    | -------- |
| [Identité de l’utilisateur](?tabs=user-identity#supported-storage-authorization-types)       |  Non pris en charge | Prise en charge      | Prise en charge|
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Prise en charge     | Pris en charge      | Pris en charge|
| [Principal du service](?tabs=service-principal#supported-storage-authorization-types) | Pris en charge | Pris en charge      | Prise en charge|
| [Identité gérée](?tabs=managed-identity#supported-storage-authorization-types) | Prise en charge | Prise en charge      | Prise en charge|

### <a name="supported-storages-and-authorization-types"></a>Types d’autorisations et de stockage pris en charge

Vous pouvez utiliser les combinaisons de types d’autorisations et de stockage Azure ci-dessous :

| Type d’autorisation  | Stockage Blob   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)    | Prise en charge      | Non pris en charge   | Prise en charge     |
| [Principal du service](?tabs=managed-identity#supported-storage-authorization-types) | Pris en charge   | Prise en charge      | Prise en charge  |
| [Identité gérée](?tabs=managed-identity#supported-storage-authorization-types) | Prise en charge      | Pris en charge        | Prise en charge     |
| [Identité de l’utilisateur](?tabs=user-identity#supported-storage-authorization-types)    | Pris en charge      | Pris en charge        | Pris en charge     |

## <a name="firewall-protected-storage"></a>Stockage protégé par un pare-feu

Vous pouvez configurer des comptes de stockage pour autoriser l’accès à un pool SQL serverless spécifique en créant une [règle d’instance de ressource](../../storage/common/storage-network-security.md?tabs=azure-portal#grant-access-from-azure-resource-instances-preview).
Lors de l’accès à un stockage protégé par le pare-feu, vous pouvez utiliser une **identité utilisateur** ou une **identité managée**.

> [!NOTE]
> La fonctionnalité de pare-feu de Stockage est en préversion publique et est disponible dans toutes les régions de cloud public. 


Dans le tableau ci-dessous, vous trouverez les types d’autorisation disponibles pour différentes méthodes de connexion au point de terminaison SQL Serverless Synapse :

| Type d’autorisation                    | *Utilisateur SQL*    | *Utilisateur Azure AD*     | *Principal du service* |
| ------------------------------------- | ------------- | -----------    | -------- |
| [Identité de l’utilisateur](?tabs=user-identity#supported-storage-authorization-types)       |  Non pris en charge | Prise en charge      | Prise en charge|
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Non pris en charge     | Non pris en charge      | Non pris en charge|
| [Principal du service](?tabs=service-principal#supported-storage-authorization-types) | Non pris en charge | Non pris en charge      | Non pris en charge|
| [Identité gérée](?tabs=managed-identity#supported-storage-authorization-types) | Prise en charge | Pris en charge      | Prise en charge|

### <a name="user-identity"></a>[Identité de l’utilisateur](#tab/user-identity)

Pour accéder au stockage protégé par le pare-feu via une identité utilisateur, vous pouvez vous servir de l’interface utilisateur du portail Azure ou du module PowerShell Az.Storage.
### <a name="configuration-via-azure-portal"></a>Configuration via le portail Azure

1. Recherchez votre compte de stockage sur le portail Azure.
1. Accédez à Mise en réseau sous la section Paramètres.
1. Dans la section « Instances de ressources », ajoutez une exception pour votre espace de travail Synapse.
1. Sélectionnez le type de ressource Microsoft.Synapse/workspaces.
1. Sélectionnez le nom de votre espace de travail sous forme de nom d'instance.
1. Cliquez sur Enregistrer.

### <a name="configuration-via-powershell"></a>Configuration via PowerShell

Suivez ces étapes pour configurer le pare-feu de votre compte de stockage et ajouter une exception pour l’espace de travail synapse.

1. Ouvrez PowerShell ou [installez PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows)
2. Installez le module Az.Storage 3.4.0 et Az.Synapse 0.7.0 : 
    ```powershell
    Install-Module -Name Az.Storage -RequiredVersion 3.4.0
    Install-Module -Name Az.Synapse -RequiredVersion 0.7.0
    ```
    > [!IMPORTANT]
    > Veillez à utiliser la **version 3.4.0**. Vous pouvez vérifier votre version Az.Storage en exécutant cette commande :  
    > ```powershell 
    > Get-Module -ListAvailable -Name  Az.Storage | select Version
    > ```
    > 

3. Connectez-vous à votre locataire Azure : 
    ```powershell
    Connect-AzAccount
    ```
4. Définissez les variables dans PowerShell : 
    - Nom du groupe de ressources : vous pouvez le trouver dans le portail Azure dans la vue d’ensemble du compte de stockage.
    - Nom du compte - nom du compte de stockage protégé par les règles de pare-feu.
    - ID de locataire - vous pouvez le trouver dans le portail Azure dans Azure Active Directory dans les informations du locataire.
    - Nom de l’espace de travail – Nom de l’espace de travail Synapse.

    ```powershell
        $resourceGroupName = "<resource group name>"
        $accountName = "<storage account name>"
        $tenantId = "<tenant id>"
        $workspaceName = "<synapse workspace name>"
        
        $workspace = Get-AzSynapseWorkspace -Name $workspaceName
        $resourceId = $workspace.Id
        $index = $resourceId.IndexOf("/resourceGroups/", 0)
        # Replace G with g - /resourceGroups/ to /resourcegroups/
        $resourceId = $resourceId.Substring(0,$index) + "/resourcegroups/" + $resourceId.Substring($index + "/resourceGroups/".Length)
        $resourceId
    ```
    > [!IMPORTANT]
    > Vérifiez que l’ID de ressource correspond à ce modèle dans l’impression de la variable resourceId.
    >
    > Il est important d’écrire **resourcegroups** en minuscules.
    > Exemple d’un ID de ressource : 
    > ```
    > /subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Synapse/workspaces/{name-of-workspace}
    > ```
    > 
5. Ajoutez une règle de réseau de stockage : 
    ```powershell
        Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId
    ```
6. Vérifiez que la règle a été appliquée dans votre compte de stockage : 
    ```powershell
        $rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
        $rule.ResourceAccessRules | ForEach-Object { 
            if ($_.ResourceId -cmatch "\/subscriptions\/(\w\-*)+\/resourcegroups\/(.)+") { 
                Write-Host "Storage account network rule is successfully configured." -ForegroundColor Green
                $rule.ResourceAccessRules
            } else {
                Write-Host "Storage account network rule is not configured correctly. Remove this rule and follow the steps in detail." -ForegroundColor Red
                $rule.ResourceAccessRules
            }
        }
    ```

### <a name="shared-access-signature"></a>[Signature d’accès partagé](#tab/shared-access-signature)

Les signatures d’accès partagé ne peuvent pas être utilisées pour accéder au stockage protégé par un pare-feu.

### <a name="service-principal"></a>[Principal du service](#tab/service-principal)

Le principal de service ne peut pas être utilisé pour accéder au stockage protégé par un pare-feu. Utilisez l’identité managée à la place.

### <a name="managed-identity"></a>[Identité gérée](#tab/managed-identity)

Vous devez activer l’option [Autoriser les services Microsoft approuvés...](../../storage/common/storage-network-security.md#trusted-microsoft-services) et [Attribuer un rôle Azure](../../storage/blobs/authorize-access-azure-active-directory.md#assign-azure-roles-for-access-rights) de façon explicite à l’[identité managée attribuée par le système](../../active-directory/managed-identities-azure-resources/overview.md) pour cette instance de ressource. Dans ce cas, l’étendue de l’accès pour l’instance correspond au rôle Azure affecté à l’identité managée.

### <a name="anonymous-access"></a>[Accès anonyme](#tab/public-access)

Vous ne pouvez pas accéder au stockage protégé par un pare-feu à l’aide d’un accès anonyme.

---

## <a name="credentials"></a>Informations d'identification

Pour interroger un fichier situé dans Stockage Azure, votre point de terminaison de pool SQL serverless a besoin d’informations d’identification qui contiennent les informations d’authentification. Deux types d’informations d’identification sont utilisés :
- Les INFORMATIONS D’IDENTIFICATION au niveau du serveur sont utilisées pour des requêtes ad hoc exécutées à l’aide de la fonction `OPENROWSET`. Le nom des informations d’identification doit correspondre à l’URL de stockage.
- Les INFORMATIONS D’IDENTIFICATION INCLUSES DANS l’étendue de la base de données sont utilisées pour des tables externes. Une table externe fait référence au paramètre `DATA SOURCE` avec les informations d’identification à utiliser pour accéder au stockage.

Pour permettre à un utilisateur de créer ou de supprimer des informations d’identification, l’administrateur peut accorder (GRANT) ou refuser (DENY) l’autorisation Modifier des informations d’identification (ALTER ANY CREDENTIAL) à un utilisateur :

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Les utilisateurs de base de données qui accèdent à un stockage externe doivent avoir l’autorisation d’utiliser des informations d’identification.

### <a name="grant-permissions-to-use-credential"></a>Accorder des autorisations pour l’utilisation d’informations d’identification

Pour utiliser des informations d’identification, un utilisateur doit disposer de l’autorisation `REFERENCES` sur des informations d’identification spécifiques. Si vous souhaitez accorder une autorisation `REFERENCES` SUR un storage_credential à un specific_user, exécutez la commande suivante :

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

## <a name="server-scoped-credential"></a>Informations d’identification incluses dans l’étendue du serveur

Les informations d’identification incluses dans l’étendue du serveur sont utilisées lorsque la connexion SQL appelle la fonction `OPENROWSET` sans le paramètre `DATA_SOURCE` pour lire des fichiers sur un compte de stockage. Le nom des informations d’identification incluses dans l’étendue du serveur **doit** correspondre à l’URL du stockage Azure (éventuellement suivi par un nom de conteneur). Les informations d’identification sont ajoutées par l’exécution de [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true). Vous devez fournir un argument CREDENTIAL NAME.

> [!NOTE]
> L’argument `FOR CRYPTOGRAPHIC PROVIDER` n’est pas pris en charge.

Le nom des INFORMATIONS D’IDENTIFICATION au niveau du serveur doit correspondre au chemin d’accès complet au compte de stockage (et éventuellement au conteneur) dans le format suivant : `<prefix>://<storage_account_path>[/<container_name>]`. Les chemins d’accès de compte de stockage sont décrits dans le tableau suivant :

| Source de données externe       | Préfixe | Chemin de compte de stockage                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Stockage Blob Azure         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

Les informations d’identification informations d’identification incluses dans l’étendue du serveur permettent d’accéder au stockage Azure à l’aide des types d’authentification suivants :

### <a name="user-identity"></a>[Identité de l’utilisateur](#tab/user-identity)

Les utilisateurs Azure AD peuvent accéder à n’importe quel fichier sur le stockage Azure s’ils disposent du rôle `Storage Blob Data Owner`, `Storage Blob Data Contributor` ou `Storage Blob Data Reader`. Les utilisateurs Azure AD n’ont pas besoin d’informations d’identification pour accéder au stockage. 

Les utilisateurs SQL ne peuvent pas utiliser l’authentification Azure AD pour accéder au stockage.

### <a name="shared-access-signature"></a>[Signature d’accès partagé](#tab/shared-access-signature)

Le script suivant crée des informations d’identification au niveau du serveur que la fonction `OPENROWSET` peut utiliser pour accéder à n’importe quel fichier sur un stockage Azure à l’aide d’un jeton SAP. Créez ces informations d’identification pour permettre au principal SQL qui exécute la fonction `OPENROWSET` de lire des fichiers protégés par la clé SAP sur le stockage Azure qui correspond à l’URL dans le nom des informations d’identification.

Remplacez <*mystorageaccountname*> par le nom de votre compte de stockage, et <*mystorageaccountcontainername* par le nom du conteneur :

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

Si vous le souhaitez, vous pouvez utiliser uniquement l’URL de base du compte de stockage, sans nom de conteneur.

### <a name="service-principal"></a>[Principal du service](#tab/service-principal)

Le script suivant crée des informations d’identification au niveau du serveur qui permettent d’accéder aux fichiers dans un stockage en utilisant le principal de service pour l’authentification et l’autorisation. Vous pouvez obtenir l’**AppID** en visitant Inscriptions d’applications dans le portail Azure et en sélectionnant l’application qui demande l’accès au stockage. Le **Secret** est obtenu durant l’inscription de l’application. **AuthorityUrl** est l’URL de l’autorité AAD OAUTH 2.0.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY = '<AppID>@<AuthorityUrl>' 
, SECRET = '<Secret>'
```

### <a name="managed-identity"></a>[Identité gérée](#tab/managed-identity)
 
Le script suivant crée des informations d’identification au niveau du serveur que la fonction `OPENROWSET` peut utiliser pour accéder à n’importe quel fichier sur un stockage Azure à l’aide d’une identité managée d’espace de travail.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

Si vous le souhaitez, vous pouvez utiliser uniquement l’URL de base du compte de stockage, sans nom de conteneur.

### <a name="public-access"></a>[Accès public](#tab/public-access)

Les informations d’identification incluses dans l’étendue de la base de données ne sont pas requises pour autoriser l’accès aux fichiers publiquement disponibles. Créez une [source de données sans informations d’identification incluses dans l’étendue de la base de données](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) pour accéder aux fichiers publiquement disponibles sur le stockage Azure.

---

## <a name="database-scoped-credential"></a>Informations d’identification incluses dans l’étendue de la base de données

Les informations d’identification incluses dans l’étendue de la base de données sont utilisées quand un principal appelle la fonction `OPENROWSET` avec le paramètre `DATA_SOURCE` ou sélectionne des données d’une [table externe](develop-tables-external-tables.md) qui n’accèdent pas aux fichiers publics. Les informations d’identification incluses dans l’étendue de la base de données ne doivent pas nécessairement correspondre au nom du compte de stockage. Elles seront utilisées de manière explicite dans la SOURCE DE DONNÉES qui définit l’emplacement du stockage.

Les informations d’identification informations d’identification incluses dans l’étendue de la base de données permettent d’accéder au stockage Azure à l’aide des types d’authentification suivants :

### <a name="azure-ad-identity"></a>[Identité Azure AD](#tab/user-identity)

Les utilisateurs Azure AD peuvent accéder à n’importe quel fichier sur le stockage Azure s’ils disposent au moins du rôle `Storage Blob Data Owner`, `Storage Blob Data Contributor` ou `Storage Blob Data Reader`. Les utilisateurs Azure AD n’ont pas besoin d’informations d’identification pour accéder au stockage.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

Les utilisateurs SQL ne peuvent pas utiliser l’authentification Azure AD pour accéder au stockage.

### <a name="shared-access-signature"></a>[Signature d’accès partagé](#tab/shared-access-signature)

Le script suivant crée des informations d’identification qui sont utilisées pour accéder aux fichiers sur le stockage en utilisant le jeton SAP spécifié dans les informations d’identification. Le script crée un exemple de source de données externe qui utilise ce jeton SAP pour accéder au stockage.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```


### <a name="service-principal"></a>[Principal du service](#tab/service-principal)
Le script suivant crée des informations d’identification délimitées à la base de données qui permettent d’accéder aux fichiers dans un stockage en utilisant le principal de service pour l’authentification et l’autorisation. Vous pouvez obtenir l’**AppID** en visitant Inscriptions d’applications dans le portail Azure et en sélectionnant l’application qui demande l’accès au stockage. Le **Secret** est obtenu durant l’inscription de l’application. **AuthorityUrl** est l’URL de l’autorité AAD OAUTH 2.0.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>

CREATE DATABASE SCOPED CREDENTIAL [<CredentialName>] WITH
IDENTITY = '<AppID>@<AuthorityUrl>' 
, SECRET = '<Secret>'
GO
CREATE EXTERNAL DATA SOURCE MyDataSource
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = CredentialName
)
```

### <a name="managed-identity"></a>[Identité gérée](#tab/managed-identity)

Le script suivant crée des informations d’identification incluses dans l’étendue de la base de données, qui peuvent être utilisées pour emprunter l’identité de l’utilisateur Azure AD actuel en tant qu’identité managée du service. Le script crée un exemple de source de données externe qui utilise l’identité de l’espace de travail pour accéder au stockage.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

Les informations d’identification incluses dans l’étendue de la base de données ne doivent pas nécessairement correspondre au nom du compte de stockage, car elles seront utilisées de manière explicite dans la SOURCE DE DONNÉES qui définit l’emplacement de stockage.

### <a name="public-access"></a>[Accès public](#tab/public-access)

Les informations d’identification incluses dans l’étendue de la base de données ne sont pas requises pour autoriser l’accès aux fichiers publiquement disponibles. Créez une [source de données sans informations d’identification incluses dans l’étendue de la base de données](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) pour accéder aux fichiers publiquement disponibles sur le stockage Azure.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

Les informations d’identification incluses dans l’étendue de la base de données sont utilisées dans des sources de données externes pour spécifier la méthode d’authentification qui sera utilisée pour accéder à ce stockage :

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Exemples

### <a name="access-a-publicly-available-data-source"></a>**Accéder à une source de données disponible publiquement**

Utilisez le script suivant pour créer une table qui accède à la source de données publiquement disponible.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

L’utilisateur de base de données peut lire le contenu des fichiers à partir de la source de données à l’aide d’une table externe ou de la fonction [OPENROWSET](develop-openrowset.md) qui fait référence à la source de données :

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = 'mysample',
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**Accéder à une source de données en utilisant des informations d’identification**

Modifiez le script suivant pour créer une table externe qui accède au stockage Azure à l’aide d’un jeton SAP, d’une identité d’utilisateur Azure AD ou d’une identité managée d’espace de travail.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity, SAS token or Service Principal. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'
GO
CREATE DATABASE SCOPED CREDENTIAL SPNCredential WITH
IDENTITY = '**44e*****8f6-ag44-1890-34u4-22r23r771098@https://login.microsoftonline.com/**do99dd-87f3-33da-33gf-3d3rh133ee33/oauth2/token' 
, SECRET = '.7OaaU_454azar9WWzLL.Ea9ePPZWzQee~'
GO
-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
--,CREDENTIAL = SPNCredential
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

L’utilisateur de base de données peut lire le contenu des fichiers à partir de la source de données à l’aide d’une [table externe](develop-tables-external-tables.md) ou de la fonction [OPENROWSET](develop-openrowset.md) qui fait référence à la source de données :

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = 'mysample', FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>Étapes suivantes

Les articles listés ci-dessous vous expliqueront comment interroger les différents types de dossiers et de fichiers, et comment créer et utiliser des vues :

- [Interroger un fichier CSV](query-single-csv-file.md)
- [Interroger des dossiers et plusieurs fichiers CSV](query-folders-multiple-csv-files.md)
- [Interroger des fichiers spécifiques](query-specific-files.md)
- [Interroger des fichiers Parquet](query-parquet-files.md)
- [Créer et utiliser des vues](create-use-views.md)
- [Interroger des fichiers JSON](query-json-files.md)
- [Interroger des types imbriqués Parquet](query-parquet-nested-types.md)
