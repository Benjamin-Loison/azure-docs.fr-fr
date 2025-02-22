---
title: Transformation des données avec un flux de données de mappage de réseau virtuel managé Azure Data Factory
description: Ce tutoriel fournit des instructions détaillées sur l’utilisation d’Azure Data Factory pour transformer des données à l’aide de flux de données de mappage.
author: ssabat
ms.author: susabat
ms.reviewer: makromer
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/04/2021
ms.openlocfilehash: fb7dfc93048d888eb83088ca2a3f458219fdc393
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128617884"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Transformation sécurisée des données à l’aide de flux de données de mappage

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](./introduction.md).

Dans ce tutoriel, vous allez utiliser l’interface utilisateur Data Factory pour créer un pipeline permettant de copier et de transformer des données *d’une source Azure Data Lake Storage Gen2 à un récepteur Data Lake Storage Gen2 (tous deux n’autorisant l’accès qu’aux réseaux sélectionnés)* à l’aide d’un flux de données de mappage sur le [réseau virtuel managé Data Factory](managed-virtual-network-private-endpoint.md). Vous pouvez développer le modèle de configuration utilisé dans ce tutoriel lors de la transformation de données à l’aide du flux de données de mappage.

Dans ce tutoriel, vous effectuez les étapes suivantes :

> [!div class="checklist"]
>
> * Créer une fabrique de données.
> * Créer un pipeline avec une activité de flux de données.
> * Générer un flux de données de mappage avec quatre transformations.
> * Effectuer une série de tests sur le pipeline.
> * Superviser une activité de flux de données.

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Azure Storage**. Vous utilisez Data Lake Storage comme magasins de données *source* et *récepteur*. Si vous ne possédez pas de compte de stockage, consultez l’article [Créer un compte de stockage Azure](../storage/common/storage-account-create.md?tabs=azure-portal) pour découvrir comment en créer un. *Vérifiez que le compte de stockage autorise l’accès provenant des réseaux sélectionnés uniquement.* 

Le fichier que nous transformons dans ce tutoriel est moviesDB.csv, accessible sur ce [site de contenu GitHub](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Pour récupérer le fichier à partir de GitHub, copiez le contenu dans l’éditeur de texte de votre choix pour l’enregistrer localement sous la forme d’un fichier .csv. Pour charger le fichier dans votre compte de stockage, consultez [Chargement d’objets blob avec le Portail Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Les exemples feront référence à un conteneur nommé **sample-data**.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

Au cours de cette étape, vous allez créer une fabrique de données et ouvrir l’interface utilisateur de Data Factory afin de créer un pipeline dans la fabrique de données.

1. Ouvrez Microsoft Edge ou Google Chrome. Actuellement, seuls les navigateurs web Microsoft Edge et Google Chrome prennent en charge l’interface utilisateur de Data Factory.
1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Analytics** > **Data Factory**.
1. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**.

   Le nom de la fabrique de données doit être un *nom global unique*. Si vous recevez un message d’erreur concernant la valeur du nom, entrez un autre nom pour la fabrique de données (par exemple, votrenomADFTutorialDataFactory). Consultez l’article [Azure Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les règles Data Factory.

1. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données.
1. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :

    * Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.
    * Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources. 
         
    Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md). 
1. Sous **Version**, sélectionnez **V2**.
1. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge apparaissent dans la liste déroulante. Les magasins de données (comme Stockage Azure et Azure SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.

1. Sélectionnez **Create** (Créer).
1. Une fois la création terminée, la notification apparaît dans le centre de notifications. Sélectionnez **Accéder à la ressource** pour accéder à la page **Data Factory**.
1. Sélectionnez **Créer et surveiller** pour lancer l’interface utilisateur de Data Factory dans un onglet séparé.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Créer un runtime d’intégration Azure sur le réseau virtuel managé Data Factory

À cette étape, vous allez créer un runtime d’intégration Azure et activer le réseau virtuel managé Data Factory.

1. Sur le portail Data Factory, accédez à **Gérer** et sélectionnez **Nouveau** pour créer un runtime d’intégration Azure.

   :::image type="content" source="./media/tutorial-copy-data-portal-private/create-new-azure-ir.png" alt-text="Capture d’écran montrant la création d’un runtime d’intégration Azure.":::
1. Dans la page **Configuration du runtime d’intégration**, choisissez le runtime d’intégration à créer en fonction des fonctionnalités nécessaires. Dans ce tutoriel, sélectionnez **Azure, Auto-hébergé**, puis cliquez sur **Continuer**. 
1. Sélectionnez **Azure**, puis cliquez sur **Continuer** pour créer un runtime d’intégration Azure.

   :::image type="content" source="./media/tutorial-copy-data-portal-private/azure-ir.png" alt-text="Capture d’écran montrant un nouveau runtime d’intégration Azure.":::

1. Sous **Configuration de réseau virtuel (préversion)** , sélectionnez **Activer**.

   :::image type="content" source="./media/tutorial-copy-data-portal-private/enable-managed-vnet.png" alt-text="Capture d’écran montrant l’activation d’un nouveau runtime d’intégration Azure.":::

1. Sélectionnez **Create** (Créer).

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Créer un pipeline avec une activité de flux de données

Au cours de cette étape, vous allez créer un pipeline qui contient une activité de flux de données.

1. Sur la page d’accueil Azure Data Factory, sélectionnez **Orchestrer**.

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Capture d’écran montrant la création d’un pipeline":::

1. Dans le volet Propriétés du pipeline, entrez **TransformMovies** comme nom du pipeline.
1. Dans le volet **Activités**, développez **Déplacer et transformer**. Faites glisser l’activité **Flux de données** du volet jusqu’au canevas du pipeline.

1. Dans la fenêtre contextuelle **Ajout de flux de données**, sélectionnez **Créer un flux de données**, puis **Flux de données de mappage**. Lorsque vous avez terminé, sélectionnez **OK**.

    :::image type="content" source="media/tutorial-data-flow-private/mapping-dataflow.png" alt-text="Capture d’écran montrant le flux de données de mappage.":::

1. Nommez votre flux de données **TransformMovies** dans le volet Propriétés.
1. Dans la barre supérieure du canevas du pipeline, faites glisser le curseur **Débogage du flux de données** pour l’activer. Le mode de débogage permet un test interactif de la logique de transformation sur un cluster Spark activé. Le préchauffage des clusters de flux de données nécessite 5 à 7 minutes et il est recommandé aux utilisateurs d’activer d’abord le débogage s’ils envisagent d’effectuer un développement de flux de données. Pour plus d’informations, consultez [Mode de débogage](concepts-data-flow-debug-mode.md).

    :::image type="content" source="media/tutorial-data-flow-private/dataflow-debug.png" alt-text="Capture d’écran montrant le curseur de débogage du flux de données.":::

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Générer une logique de transformation dans le canevas de flux de données

Une fois que vous avez créé votre flux de données, vous êtes automatiquement envoyé vers le canevas de flux de données. Au cours de cette étape, vous allez créer un flux de données qui prend le fichier moviesDB.csv dans Data Lake Storage et agrège l’évaluation moyenne des comédies des années 1910 à 2000. Vous réécrirez ensuite ce fichier dans Data Lake Storage.

### <a name="add-the-source-transformation"></a>Ajout de la transformation de la source

À cette étape, vous allez configurer Data Lake Storage Gen2 comme source.

1. Dans le canevas de flux de données, ajoutez une source en sélectionnant la zone **Ajouter une source**.

1. Nommez votre source **MoviesDB**. Sélectionnez **Nouveau** pour créer un jeu de données source.

1. Sélectionnez **Azure Data Lake Storage Gen2**, puis sélectionnez **Continuer**.

1. Sélectionnez **DelimitedText**, puis sélectionnez **Continuer**.

1. Nommez votre jeu de données **MoviesDB**. Dans la liste déroulante du service lié, sélectionnez **Nouveau**.

1. Dans l’écran de création de service lié, nommez votre service lié Data Lake Storage Gen2 **ADLSGen2**, puis spécifiez votre méthode d’authentification. Entrez ensuite vos informations d’identification de connexion. Dans ce tutoriel, nous utilisons une **clé de compte** pour nous connecter à notre compte de stockage. 

1. Veillez à activer **Création interactive**. L’activation peut prendre une minute.

    :::image type="content" source="./media/tutorial-data-flow-private/interactive-authoring.png" alt-text="Capture d’écran montrant Création interactive":::

1. Sélectionnez **Tester la connexion**. Cela doit échouer, car le compte de stockage n’y autorise pas l’accès sans la création et l’approbation d’un point de terminaison privé. Le message d’erreur comporte un lien permettant de créer un point de terminaison privé. Vous pouvez le suivre pour créer un point de terminaison privé managé. Une autre possibilité pour le créer consiste à accéder directement à l’onglet **Gérer** et à suivre les instructions de [cette section](#create-a-managed-private-endpoint).

1. Laissez la boîte de dialogue ouverte, puis accédez à votre compte de stockage.

1. Suivez les instructions de [cette section](#approval-of-a-private-link-in-a-storage-account) pour approuver la liaison privée.

1. Revenez à la boîte de dialogue. Sélectionnez de nouveau **Tester la connexion**, puis sélectionnez **Créer** pour déployer le service lié.

1. Dans l’écran de création du jeu de données, entrez l’emplacement de votre fichier sous le champ **Chemin du fichier**. Dans ce tutoriel, le fichier moviesDB.csv se trouve dans le conteneur **sample-data**. Étant donné que le fichier contient des en-têtes, cochez la case **Première ligne comme en-tête**. Sélectionnez **À partir de la connexion/du magasin** pour importer le schéma d’en-tête directement à partir du fichier situé dans le stockage. Lorsque vous avez terminé, sélectionnez **OK**.

    :::image type="content" source="media/tutorial-data-flow-private/source-file-path.png" alt-text="Capture d’écran montrant le chemin source.":::

1. Si votre cluster de débogage a démarré, accédez à l’onglet **Aperçu des données**  de la transformation de la source, puis sélectionnez **Actualiser** pour obtenir un instantané des données. Vous pouvez utiliser l’aperçu des données pour vérifier que votre transformation est correctement configurée.

    :::image type="content" source="media/tutorial-data-flow-private/data-preview.png" alt-text="Capture d’écran montrant l’onglet Aperçu des données.":::

#### <a name="create-a-managed-private-endpoint"></a>Créer un point de terminaison privé managé

Si vous n’avez pas utilisé le lien hypertexte lorsque vous avez testé la connexion précédente, suivez le chemin. Vous devez maintenant créer un point de terminaison privé managé que vous connecterez au service lié créé.

1. Accédez à l’onglet **Gérer**.

   > [!NOTE]
   > L’onglet **Gérer** n’est pas nécessairement disponible pour toutes les instances Data Factory. Si vous ne le voyez pas, vous pouvez accéder aux points de terminaison privés en sélectionnant **Auteur** > **Connexions** > **Point de terminaison privé**.

1. Accédez à la section **Points de terminaison privés managés**.
1. Sélectionnez **+ Nouveau** sous **Points de terminaison privés managés**.

    :::image type="content" source="./media/tutorial-data-flow-private/new-managed-private-endpoint.png" alt-text="Capture d’écran montrant Points de terminaison privés managés &gt; bouton Nouveau"::: 

1. Sélectionnez la vignette **Azure Data Lake Storage Gen2** dans la liste, puis sélectionnez **Continuer**.
1. Entrez le nom du compte de stockage que vous avez créé.
1. Sélectionnez **Create** (Créer).
1. Au bout de quelques secondes, la liaison privée créée indique qu’une approbation est nécessaire.
1. Sélectionnez le point de terminaison privé que vous avez créé. Le lien hypertexte qui apparaît vous permet d’approuver le point de terminaison privé au niveau du compte de stockage.

    :::image type="content" source="./media/tutorial-data-flow-private/manage-private-endpoint.png" alt-text="Capture d’écran montrant le volet Gérer les points de terminaison privés."::: 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Approbation d’une liaison privée dans un compte de stockage

1. Dans la section **Paramètres** du compte de stockage, accédez à **Connexions des points de terminaison privés**.

1. Cochez la case du point de terminaison privé que vous avez créé, puis sélectionnez **Approuver**.

    :::image type="content" source="./media/tutorial-data-flow-private/approve-private-endpoint.png" alt-text="Capture d’écran montrant le bouton Approuver du point de terminaison privé.":::

1. Ajoutez une description, puis sélectionnez **Oui**.
1. Revenez à la section **Points de terminaison privés managés** de l’onglet **Gérer** dans Data Factory.
1. Après environ une minute, l’approbation doit s’afficher pour votre point de terminaison privé.

### <a name="add-the-filter-transformation"></a>Ajout de la transformation de filtre

1. À côté de votre nœud source dans le canevas de flux de données, sélectionnez l’icône plus (+) pour ajouter une nouvelle transformation. La première transformation que vous ajouterez est un **filtre**.

    :::image type="content" source="media/tutorial-data-flow-private/add-filter.png" alt-text="Capture d’écran illustrant l’ajout d’un filtre.":::
1. Nommez votre transformation de filtre **FilterYears**. Sélectionnez la zone d’expression à côté de **Filtrer sur** pour ouvrir le générateur d’expressions. Ici, vous allez spécifier votre condition de filtrage.

    :::image type="content" source="media/tutorial-data-flow-private/filter-years.png" alt-text="Capture d’écran montrant FilterYears.":::
1. Le générateur d’expressions de flux de données vous permet de générer de manière interactive des expressions à utiliser dans diverses transformations. Les expressions peuvent inclure des fonctions intégrées, des colonnes du schéma d’entrée et des paramètres définis par l’utilisateur. Pour plus d’informations sur la génération d’expressions, consultez [Générateur d’expressions de flux de données](./concepts-data-flow-expression-builder.md).

    * Dans ce tutoriel, vous voulez filtrer les films du genre comédie qui sont sortis entre les années 1910 et 2000. Comme l’année est actuellement une chaîne, vous devez la convertir en un entier à l’aide de la fonction `toInteger()`. Utilisez les opérateurs supérieur ou égal à (>=) et inférieur ou égal à (<=) pour effectuer une comparaison avec les valeurs d’années (year) littérales 1910 et 2000. Unissez ces expressions avec l’opérateur And (&&). L’expression se présente comme suit :

        `toInteger(year) >= 1910 && toInteger(year) <= 2000`

    * Pour rechercher les films qui sont des comédies, vous pouvez utiliser la fonction `rlike()` pour rechercher le modèle « Comedy » dans la colonne des genres. Ajoutez à l’expression `rlike` la comparaison d’année à obtenir :

        `toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')`

    * Si vous avez un cluster de débogage actif, vous pouvez vérifier votre logique en sélectionnant **Actualiser** pour afficher la sortie de l’expression par rapport aux entrées utilisées. Il y a plusieurs réponses appropriées sur la façon dont vous pouvez accomplir cette logique à l’aide du langage d’expression de flux de données.

        :::image type="content" source="media/tutorial-data-flow-private/filter-expression.png" alt-text="Capture d’écran montrant l’expression de filtre.":::

    * Sélectionnez **Enregistrer et terminer** une fois que vous en avez terminé avec votre expression.

1. Pour vérifier que le filtre fonctionne correctement, récupérez un **Aperçu des données**.

    :::image type="content" source="media/tutorial-data-flow-private/filter-data.png" alt-text="Capture d’écran montrant l’aperçu des données filtré.":::

### <a name="add-the-aggregate-transformation"></a>Ajout de la transformation d’agrégation

1. La transformation suivante que vous allez ajouter est une transformation **Agrégation** sous **Modificateur de schéma**.

    :::image type="content" source="media/tutorial-data-flow-private/add-aggregate.png" alt-text="Capture d’écran illustrant l’ajout de l’agrégat.":::
1. Nommez votre transformation d’agrégation **AggregateComedyRating**. Sous l’onglet **Grouper par**, sélectionnez **année** dans la liste déroulante pour regrouper les agrégations par année de sortie des films.

    :::image type="content" source="media/tutorial-data-flow-private/group-by-year.png" alt-text="Capture d’écran montrant le groupe d’agrégats.":::
1. Accédez à l’onglet **Agrégats**. Dans la zone de texte de gauche, nommez la colonne d’agrégation **AverageComedyRating**. Sélectionnez la zone d’expression de droite pour entrer l’expression d’agrégation par le biais du générateur d’expressions.

    :::image type="content" source="media/tutorial-data-flow-private/name-column.png" alt-text="Capture d’écran montrant le nom de la colonne d’agrégation.":::
1. Pour afficher la moyenne de la colonne **Évaluation**, utilisez la fonction d’agrégation ```avg()```. Comme **Évaluation** est une chaîne et que ```avg()``` prend une entrée numérique, nous devons convertir la valeur en nombre à l’aide de la fonction ```toInteger()```. Voici comment se présente l’expression :

    `avg(toInteger(Rating))`

1. Sélectionnez **Enregistrer et terminer** une fois que vous avez terminé.

    :::image type="content" source="media/tutorial-data-flow-private/save-aggregate.png" alt-text="Capture d’écran montrant l’enregistrement de l’agrégat.":::
1. Accédez à l’onglet **Aperçu des données** pour afficher la sortie de la transformation. Notez que seules deux colonnes sont affichées : **year** et **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>Ajout de la transformation du récepteur

1. Ensuite, vous voulez ajouter une transformation **Récepteur** sous **Destination**.

    :::image type="content" source="media/tutorial-data-flow-private/add-sink.png" alt-text="Capture d’écran illustrant l’ajout d’un récepteur.":::
1. Nommez votre récepteur **Sink**. Sélectionnez **Nouveau** pour créer votre jeu de données récepteur.

    :::image type="content" source="media/tutorial-data-flow-private/create-sink.png" alt-text="Capture d’écran montrant la création d’un récepteur.":::
1. Dans la page **Nouveau jeu de données**, sélectionnez **Azure Data Lake Storage Gen2**, puis **Continuer**.

1. Dans la page **Sélectionner le format**, sélectionnez **DelimitedText**, puis **Continuer**.

1. Nommez votre jeu de données récepteur **MoviesSink**. Pour Service lié, choisissez le service lié **ADLSGen2** que vous avez créé pour la transformation source. Entrez un dossier de sortie dans lequel écrire vos données. Dans ce tutoriel, nous écrivons dans le dossier **output** se trouvant dans le conteneur **sample-data**. Le dossier ne doit pas nécessairement exister au préalable et peut être créé de façon dynamique. Cochez la case **Première ligne comme en-tête**, puis sélectionnez **Aucun** pour **Importer un schéma**. Sélectionnez **OK**.

    :::image type="content" source="media/tutorial-data-flow-private/sink-file-path.png" alt-text="Capture d’écran montrant le chemin d’accès du récepteur.":::

Vous avez terminé la génération de votre flux de données. Vous êtes prêt à l’exécuter dans votre pipeline.

## <a name="run-and-monitor-the-data-flow"></a>Exécuter et surveiller le flux de données

Vous pouvez déboguer un pipeline avant de le publier. Au cours de cette étape, vous déclenchez une exécution de débogage du pipeline de flux de données. Alors que l’aperçu des données n’écrit pas de données, une exécution de débogage écrit des données dans votre récepteur de destination.

1. Accédez au canevas du pipeline. Sélectionnez **Déboguer** pour déclencher une exécution de débogage.

1. Le débogage de pipeline des activités de flux de données utilise le cluster de débogage actif, mais son initialisation prend toujours au moins une minute. Vous pouvez suivre la progression par le biais de l’onglet **Sortie**. Une fois l’exécution réussie, sélectionnez l’icône en forme de lunettes pour en afficher les détails.

1. La page Détails indique le nombre de lignes et le temps consacré à chaque étape de transformation.

    :::image type="content" source="media/tutorial-data-flow-private/run-details.png" alt-text="Capture d’écran montrant une exécution d’analyse.":::
1. Sélectionnez une transformation pour obtenir des informations détaillées sur les colonnes et le partitionnement des données.

Si vous avez suivi ce tutoriel correctement, vous devez avoir écrit 83 lignes et 2 colonnes dans votre dossier récepteur. Vous pouvez vérifier que les données sont correctes en vérifiant votre stockage d’objets blob.

## <a name="summary"></a>Résumé

Dans ce tutoriel, vous avez utiliser l’interface utilisateur Data Factory pour créer un pipeline permettant de copier et de transformer des données d’une source Data Lake Storage Gen2 à un récepteur Data Lake Storage Gen2 (tous deux n’autorisant l’accès qu’aux réseaux sélectionnés) à l’aide d’un flux de données de mappage sur le [réseau virtuel managé Data Factory](managed-virtual-network-private-endpoint.md).
