---
title: Exporter vers SQL à partir d’Application Insights | Microsoft Docs
description: Exportez de façon continue les données Application Insights vers SQL à l’aide de Stream Analytics.
ms.topic: conceptual
ms.date: 09/11/2017
ms.service: stream-analytics
ms.openlocfilehash: bcf3bc5995a6d9c8c37ab92e303b7d39b0b132fe
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602534"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Procédure pas à pas : exporter vers SQL à partir d’Application Insights à l’aide de Stream Analytics
Cet article explique comment déplacer vos données de télémétrie d’[Azure Application Insights][start] vers Azure SQL Database à l’aide de l’[Exportation continue][export] et d’[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

L’exportation continue déplace vos données de télémétrie vers le stockage Azure au format JSON. Nous analyserons les objets JSON à l’aide d’Azure Stream Analytics et créerons des lignes dans une table de base de données.

(Plus généralement, l’exportation continue est la méthode qui vous permet de réaliser votre propre analyse des données de télémétrie que vos applications transmettent à Application Insights. Vous pourriez adapter cet exemple de code pour effectuer d’autres actions à l’aide des données de télémétrie exportées, comme agréger les données.)

Nous allons partir du principe que vous disposez déjà de l’application que vous voulez analyser.

Dans cet exemple, nous allons utiliser les données d’affichage de page. Toutefois, le même modèle peut facilement être étendu à d’autres types de données, tels que des événements et des exceptions personnalisés. 

> [!IMPORTANT]
> L’exportation continue est déconseillée. Elle n’est prise en charge que pour les ressources Application Insights classiques. [Migrez vers une ressource Application Insights basée sur un espace de travail](../azure-monitor/app/convert-classic-resource.md) pour utiliser les [paramètres de diagnostic](../azure-monitor/app/export-telemetry.md#diagnostic-settings-based-export) pour l’exportation des données de télémétrie.

## <a name="add-application-insights-to-your-application"></a>Ajouter Application Insights à votre application 
Pour commencer :

1. [Configurer Application Insights pour vos pages web](../azure-monitor/app/javascript.md). 
   
    (Dans cet exemple, nous allons nous concentrer sur le traitement des données d’affichage de page dans les navigateurs clients, mais vous pouvez également configurer Application Insights pour le côté serveur de votre application [Java](../azure-monitor/app/java-in-process-agent.md) ou [ASP.NET](../azure-monitor/app/asp-net.md) et traiter la demande, les dépendances et d’autres données de télémétrie du serveur.)
2. Publiez votre application et surveillez les données de télémétrie apparaissant dans votre ressource Application Insights.

## <a name="create-storage-in-azure"></a>Création d’un stockage dans Azure
Comme l’exportation continue génère toujours des données vers un compte de stockage Azure, vous devez commencer par créer ce stockage.

1. Créez un compte de stockage dans votre abonnement sur le [portail Azure][portal].
   
    ![Capture d’écran du portail Azure, choix de Nouveau, Données, Stockage, puis sélection de Classique, Créer et fourniture d’un nom de Stockage.](./media/app-insights-export-sql-stream-analytics/040-store.png)
2. Créer un conteneur
   
    ![Capture d’écran : nouvel emplacement de stockage, sélection de Conteneurs, puis sélection de la vignette Conteneurs, puis Ajouter.](./media/app-insights-export-sql-stream-analytics/050-container.png)
3. Copiez la clé d’accès au stockage.
   
    Vous en aurez bientôt besoin pour configurer l’entrée dans le service Stream analytics.
   
    ![ Capture d’écran : espace de stockage, ouverture de Paramètres, Clés et copie de la clé d’accès principale.](./media/app-insights-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Démarrer l’exportation continue vers le stockage Azure
1. Sur le portail Azure, accédez à la ressource Application Insights que vous avez créée pour votre application.
   
    ![Capture d’écran : portail Azure, choix de Parcourir, Application Insights, votre Application.](./media/app-insights-export-sql-stream-analytics/060-browse.png)
2. Créez une exportation continue.
   
    ![Capture d’écran : ressource Application Insights, choix de Paramètres, Exportation continue, puis Ajouter.](./media/app-insights-export-sql-stream-analytics/070-export.png)

    Sélectionnez le compte de stockage que vous avez préalablement créé.

    ![Capture d’écran : exportation continue, puis sélection d’Ajouter.](./media/app-insights-export-sql-stream-analytics/080-add.png)

    Définissez les types d’événements que vous souhaitez afficher :

    ![Capture d’écran de l’exportation continue, puis choix des types d’événements.](./media/app-insights-export-sql-stream-analytics/085-types.png)


1. Laissez les données s'accumuler. Installez-vous confortablement et laissez les utilisateurs utiliser votre application pendant un certain temps. Les données de télémétrie vont vous être transmises et vous permettre d’afficher des graphiques statistiques dans [Metrics explorer](../azure-monitor/essentials/metrics-charts.md) et des événements dans [Recherche de diagnostic](../azure-monitor/app/diagnostic-search.md). 
   
    Les données seront également exportées vers votre stockage. 
2. Inspectez les données exportées, soit dans le portail (choisissez **Parcourir**, sélectionnez votre compte de stockage, puis **Conteneurs**), soit dans Visual Studio. Dans Visual Studio, sélectionnez **Afficher / Cloud Explorer**, puis ouvrez Azure / Stockage. (Si vous n’avez pas cette option de menu, vous devez installer le SDK Azure : ouvrez la boîte de dialogue Nouveau projet, puis ouvrez Visual C# / Cloud / Obtenir Microsoft Azure SDK pour .NET.)
   
    ![Capture d’écran de Visual Studio, ouverture de l’Explorateur de serveurs, Azure, Stockage.](./media/app-insights-export-sql-stream-analytics/087-explorer.png)
   
    Prenez note de la partie commune du nom du chemin d'accès, qui est dérivée du nom de l'application et de la clé d'instrumentation. 

Les événements sont écrits dans des fichiers blob au format JSON. Chaque fichier peut contenir un ou plusieurs événements. Donc, nous devons lire les données d’événement et filtrer les champs voulus. Nous pourrions effectuer toutes sortes d’opérations avec les données, mais notre objectif aujourd’hui est d’utiliser Stream Analytics pour déplacer les données vers SQL Database. Cette action va simplifier l’exécution d’un grand nombre de requêtes intéressantes.

## <a name="create-an-azure-sql-database"></a>Création d’une base de données Azure SQL
Là encore, à partir de votre abonnement dans le [portail Azure][portal], créez la base de données (et un serveur, à moins que vous n’en ayez déjà un) où vous allez écrire les données.

![Capture d’écran du portail Azure, Créer, Données + stockage et SQL Database.](./media/app-insights-export-sql-stream-analytics/090-sql.png)

Assurez-vous que le serveur autorise l'accès aux services Azure :

![Capture d’écran des serveurs SQL dans le portail Azure et paramètres de pare-feu.](./media/app-insights-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-database"></a>Créer une table dans Azure SQL Database
Connectez-vous à la base de données créée dans la section précédente à l’aide de votre outil de gestion préféré. Dans cette procédure pas à pas, nous utiliserons [Outils d’administration SQL Server](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

![Capture d’écran de connexion à Azure SQL Database](./media/app-insights-export-sql-stream-analytics/31-sql-table.png)

Créez une nouvelle requête et exécutez le T-SQL suivant :

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![Capture d’écran de création de PageViewsTable dans SQL Server Management Studio.](./media/app-insights-export-sql-stream-analytics/34-create-table.png)

Dans cet exemple, nous utilisons les données issues des affichages de pages. Pour voir les autres données disponibles, examinez la sortie JSON et consultez le [modèle d’exportation de données](../azure-monitor/app/export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Création d’une instance Azure Stream Analytics
À partir du [portail Azure](https://portal.azure.com/), sélectionnez le service Azure Stream Analytics et créez une nouvelle tâche Stream Analytics :

![Capture d’écran montrant la page du travail Stream Analytics avec le bouton Créer en surbrillance.](./media/app-insights-export-sql-stream-analytics/001.png)

![Capture d’écran d’une nouvelle tâche Stream Analytics](./media/app-insights-export-sql-stream-analytics/002.png)

Lors de la création de la tâche, sélectionnez **Accéder à la ressource**.

![Capture d’écran montrant le message Déploiement réussi et le bouton Accéder à la ressource.](./media/app-insights-export-sql-stream-analytics/003.png)

#### <a name="add-a-new-input"></a>Ajouter une nouvelle entrée

![Capture d’écran montrant la page Entrées avec le bouton Ajouter sélectionné.](./media/app-insights-export-sql-stream-analytics/004.png)

Définissez-le pour qu’il tienne compte des données de votre objet blob d’exportation continue :

![Capture d’écran montrant la fenêtre Nouvelle entrée avec les options du menu déroulant Alias d’entrée, Source et Compte de stockage sélectionnées.](./media/app-insights-export-sql-stream-analytics/0005.png)

Vous devez maintenant disposer de la clé d’accès principale issue de votre compte de stockage, que vous avez notée précédemment. Définissez-la comme clé de compte de stockage.

#### <a name="set-path-prefix-pattern"></a>Définition de la séquence d’octets préfixe du chemin d’accès

**Veillez à définir le format de date sur AAAA-MM-JJ (avec des tirets).**

La séquence d’octets préfixe du chemin d’accès spécifie la manière dont Stream Analytics recherche les fichiers d’entrée dans le stockage. Vous devez la configurer pour correspondre au mode de stockage des données de l'exportation continue. Définissez-la comme suit :

```sql
webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}
```

Dans cet exemple :

* `webapplication27` est le nom de la ressource Application Insights, **tout en minuscules**. 
* `1234...` est la clé d’instrumentation de la ressource Application Insights **avec les tirets supprimés**. 
* `PageViews` est le type de données que nous souhaitons analyser. Les types disponibles varient selon le filtre que vous définissez dans l’exportation continue. Examinez les données exportées pour voir les autres types disponibles et consultez le [modèle d’exportation de données](../azure-monitor/app/export-data-model.md).
* `/{date}/{time}` est une séquence écrite de manière littérale.

Pour obtenir le nom et l’iKey de votre ressource Application Insights, ouvrez Essentials sur sa page de présentation ou ouvrez Paramètres.

> [!TIP]
> Utilisez l’exemple de fonction pour vérifier que vous avez correctement défini le chemin d’accès d’entrée. En cas d’échec, vérifiez qu’il y a des données dans le stockage pour l’exemple de période que vous avez choisi. Modifiez la définition de l’entrée et vérifiez que vous avez correctement défini le compte de stockage, le préfixe de chemin d’accès et le format de date.

 
## <a name="set-query"></a>Définition d’une requête
Ouvrez la section de la requête :

Remplacez la requête par défaut par :

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Notez que les premières propriétés sont spécifiques aux données d’affichage de page. Les exportations d’autres types de télémétrie disposent de propriétés différentes. Consultez la [référence de modèle de données détaillé pour les valeurs et types de propriétés.](../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Configuration de la sortie vers la base de données
Sélectionnez SQL comme sortie.

![Capture d’écran de Stream Analytics avec les Sorties sélectionnées.](./media/app-insights-export-sql-stream-analytics/006.png)

Spécifiez la base de données.

![Capture d’écran de nouvelle sortie avec les détails de la base de données.](./media/app-insights-export-sql-stream-analytics/007.png)

Fermez l’assistant et attendez une notification indiquant que la sortie a été configurée.

## <a name="start-processing"></a>Démarrage du traitement
Démarrez la tâche à partir de la barre d’action :

![Capture d’écran de Stream Analytics avec l’option Démarrer sélectionnée.](./media/app-insights-export-sql-stream-analytics/008.png)

Vous pouvez choisir de démarrer le traitement à partir de données actuelles ou de données antérieures. La dernière possibilité est utile si l’exportation continue fonctionne déjà depuis un certain temps.

Après quelques minutes, revenez aux Outils d’administration SQL Server et observez les données qui y circulent. Utilisez, par exemple, le type de requête suivant :

```sql
SELECT TOP 100 *
FROM [dbo].[PageViewsTable]
```

## <a name="next-steps"></a>Étapes suivantes
* [Exporter vers Power BI à l’aide de Stream Analytics](../azure-monitor/app/export-power-bi.md)
* [Référence de modèle de données détaillé pour les valeurs et types de propriétés.](../azure-monitor/app/export-data-model.md)
* [Exportation continue dans Application Insights](../azure-monitor/app/export-telemetry.md)

<!--Link references-->

[diagnostic]: ../azure-monitor/app/diagnostic-search.md
[export]: ../azure-monitor/app/export-telemetry.md
[metrics]: ../azure-monitor/essentials/metrics-charts.md
[portal]: https://portal.azure.com/
[start]: ../azure-monitor/app/app-insights-overview.md

