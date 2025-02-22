---
title: Transformation avec Azure Databricks
description: Découvrez comment utiliser un modèle de solution pour transformer des données à l’aide d’un notebook Databricks dans Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: dc01ba10922901e5c3558e26b0f2ab2718ea34e3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743593"
---
# <a name="transformation-with-azure-databricks"></a>Transformation avec Azure Databricks

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dans ce tutoriel, vous allez créer un pipeline de bout en bout contenant des activités **Validation**, **Copie des données** et **Notebook** dans Azure Data Factory.

- **Validation** garantit que le jeu de données source est prêt pour la consommation en aval avant que vous ne déclenchiez le travail de copie et d’analytique.

- **Copie des données** duplique le jeu de données source dans le stockage récepteur, qui est monté en tant que DBFS dans le notebook Azure Databricks. De cette façon, le jeu de données peut directement être consommé par Spark.

- **Notebook** déclenche le notebook Databricks qui transforme le jeu de données. Le jeu de données est aussi ajouté à un dossier traité ou à Azure Synapse Analytics.

Par souci de simplicité, le modèle de ce didacticiel ne crée pas de déclencheur planifié. Vous pouvez en ajouter un si nécessaire.

:::image type="content" source="media/solution-template-Databricks-notebook/pipeline-example.png" alt-text="Diagramme du pipeline":::

## <a name="prerequisites"></a>Prérequis

- Un compte de stockage Blob Azure avec un conteneur appelé `sinkdata` servant de récepteur.

  Notez le nom du compte de stockage, du conteneur et de la clé d’accès. Vous en aurez besoin plus loin dans le modèle.

- Un espace de travail Azure Databricks.

## <a name="import-a-notebook-for-transformation"></a>Importer un notebook pour Transformation

Pour importer un notebook **Transformation** dans votre espace de travail Databricks :

1. Connectez-vous à votre espace de travail Azure Databricks, puis sélectionnez **Importer**.
       :::image type="content" source="media/solution-template-Databricks-notebook/import-notebook.png" alt-text="Commande de menu pour l’importation d’un espace de travail":::
   Le chemin d’accès à votre espace de travail peut être différent de celui qui est affiché, mais souvenez-vous en pour plus tard.
1. Sélectionnez **Importer depuis : URL**. Dans la zone de texte, entrez `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`.

   :::image type="content" source="media/solution-template-Databricks-notebook/import-from-url.png" alt-text="Sélections pour l’importation d’un notebook":::

1. Nous allons maintenant mettre à jour le notebook **Transformation** avec vos informations de connexion au stockage.

   Dans le notebook importé, accédez à la **commande 5** comme indiqué dans l’extrait de code suivant.

   - Remplacez `<storage name>` et `<access key>` par vos propres informations de connexion au stockage.
   - Utilisez le compte de stockage muni du conteneur `sinkdata`.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Générez un **jeton d’accès Databricks** pour permettre à Data Factory d’accéder à Databricks.
   1. Dans votre espace de travail Databricks, sélectionnez votre icône de profil utilisateur dans le coin supérieur droit.
   1. Sélectionner **Paramètres utilisateur**.
    :::image type="content" source="media/solution-template-Databricks-notebook/user-setting.png" alt-text="Menu command for user settings":::
   1. Sélectionnez **Générer un nouveau jeton** sous l’onglet **Jetons d’accès**.
   1. Sélectionnez **Générer**.

    :::image type="content" source="media/solution-template-Databricks-notebook/generate-new-token.png" alt-text="Bouton &quot;Générer&quot;":::

   *Enregistrez le jeton d’accès* pour une utilisation ultérieure lors de la création d’un service lié à Databricks. Le jeton d’accès ressemble à `dapi32db32cbb4w6eee18b7d87e45exxxxxx`.

## <a name="how-to-use-this-template"></a>Comment utiliser ce modèle

1. Accédez au modèle **Transformation avec Azure Databricks** et créez des services liés pour les connexions suivantes.

   :::image type="content" source="media/solution-template-Databricks-notebook/connections-preview.png" alt-text="Configuration des connexions":::

    - **Connexion aux blobs sources** : pour accéder aux données sources.

       Pour cet exemple, vous pouvez utiliser le stockage de blobs public contenant les fichiers sources. Reportez-vous à la capture d’écran suivante pour la configuration. Utilisez l’**URL SAS** suivante pour vous connecter au stockage source (accès en lecture seule) :

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        :::image type="content" source="media/solution-template-Databricks-notebook/source-blob-connection.png" alt-text="Sélections pour la méthode d’authentification et l’URL SAS":::

    - **Connexion aux blobs de destination** : pour stocker les données copiées.

       Dans la fenêtre **Nouveau service lié**, sélectionnez votre blob de stockage du récepteur.

       :::image type="content" source="media/solution-template-Databricks-notebook/destination-blob-connection.png" alt-text="Blob de stockage de récepteur comme nouveau service lié":::

    - **Azure Databricks** : pour se connecter au cluster Databricks.

        Créez un service lié à Databricks à l’aide de la clé d’accès que vous avez générée précédemment. Vous pouvez choisir de sélectionner un *cluster interactif* si vous en avez un. Cet exemple utilise l’option **Nouveau cluster de travail**.

        :::image type="content" source="media/solution-template-Databricks-notebook/databricks-connection.png" alt-text="Sélections pour la connexion au cluster":::

1. Sélectionnez **Utiliser ce modèle**. Vous verrez la création d’un pipeline.

    :::image type="content" source="media/solution-template-Databricks-notebook/new-pipeline.png" alt-text="Créer un pipeline":::

## <a name="pipeline-introduction-and-configuration"></a>Présentation et configuration du pipeline

Dans le nouveau pipeline, la plupart des paramètres sont configurés automatiquement avec les valeurs par défaut. Passez en revue les configurations de votre pipeline et apportez les modifications nécessaires.

1. Dans l’**indicateur de disponibilité** de l’activité **Validation**, vérifiez que la valeur **Jeu de données** source est définie sur `SourceAvailabilityDataset` que vous avez créée précédemment.

   :::image type="content" source="media/solution-template-Databricks-notebook/validation-settings.png" alt-text="Valeur du jeu de données source":::

1. Dans le transfert **fichier à blob** de l’activité **Copie des données**, vérifiez les onglets **Source** et **Récepteur**. Modifiez les paramètres si nécessaire.

   - Onglet **Source** :::image type="content" source="media/solution-template-Databricks-notebook/copy-source-settings.png" alt-text="Source tab":::

   - Onglet **Récepteur** :::image type="content" source="media/solution-template-Databricks-notebook/copy-sink-settings.png" alt-text="Sink tab":::

1. Dans **Transformation** de l’activité **Notebook**, passez en revue et mettez à jour les chemins d’accès et les paramètres selon vos besoins.

   Le **service lié à Databricks** doit être pré-rempli avec la valeur d’une étape précédente, comme indiqué ci-dessous : :::image type="content" source="media/solution-template-Databricks-notebook/notebook-activity.png" alt-text="Populated value for the Databricks linked service":::

   Pour vérifier les paramètres de **Notebook** :
  
    1. Sélectionnez l’onglet **Paramètres**. Pour **Chemin d’accès du notebook**, vérifiez que le chemin d’accès par défaut est correct. Vous devrez peut-être parcourir et sélectionner le chemin d’accès approprié du notebook.

       :::image type="content" source="media/solution-template-Databricks-notebook/notebook-settings.png" alt-text="Chemin d’accès du notebook":::

    1. Développez le sélecteur **Paramètres de base** et vérifiez que les paramètres correspondent à ce qui est illustré dans la capture d’écran suivante. Ces paramètres sont transmis au notebook Databricks à partir de Data Factory.

       :::image type="content" source="media/solution-template-Databricks-notebook/base-parameters.png" alt-text="Paramètres de base":::

1. Vérifiez que les **paramètres du pipeline** correspondent à ce qui est illustré dans la capture d’écran suivante : :::image type="content" source="media/solution-template-Databricks-notebook/pipeline-parameters.png" alt-text="Pipeline parameters":::

1. Connectez-vous à vos jeux de données.

    >[!NOTE]
    >Dans les jeux de données ci-dessous, le chemin d’accès du fichier a été automatiquement spécifié dans le modèle. Si des modifications sont nécessaires, veillez à spécifier le chemin d’accès pour **conteneur** et **répertoire** en cas d’erreur de connexion.

   - **SourceAvailabilityDataset** : pour vérifier si les données sources sont disponibles.

     :::image type="content" source="media/solution-template-Databricks-notebook/source-availability-dataset.png" alt-text="Sélections pour le chemin d’accès du fichier et du service lié pour SourceAvailabilityDataset":::

   - **SourceFilesDataset** : pour accéder aux données sources.

       :::image type="content" source="media/solution-template-Databricks-notebook/source-file-dataset.png" alt-text="Sélections pour le chemin d’accès du fichier et du service lié pour SourceFilesDataset":::

   - **DestinationFilesDataset** : pour copier les données dans l’emplacement de destination du récepteur. Utilisez les valeurs suivantes :

     - **Service lié**  -  `sinkBlob_LS`, créé au cours d’une étape précédente.

     - **Chemin d’accès du fichier**  -  `sinkdata/staged_sink`.

       :::image type="content" source="media/solution-template-Databricks-notebook/destination-dataset.png" alt-text="Sélections pour le chemin d’accès du fichier et du service lié pour DestinationFilesDataset":::

1. Sélectionnez **Déboguer** pour exécuter le pipeline. Vous trouverez le lien vers les journaux Databricks permettant d’obtenir des journaux Spark plus détaillés.

    :::image type="content" source="media/solution-template-Databricks-notebook/pipeline-run-output.png" alt-text="Lien vers les journaux Databricks de la sortie":::

    Vous pouvez également vérifier le fichier de données à l’aide d’Explorateur Stockage Azure.

    > [!NOTE]
    > Pour la corrélation avec les exécutions de pipeline Data Factory, cet exemple ajoute l’ID d’exécution de pipeline de la fabrique de données au dossier de sortie. Cela permet le suivi des fichiers générés par chaque exécution.
    > :::image type="content" source="media/solution-template-Databricks-notebook/verify-data-files.png" alt-text="Appended pipeline run ID":::

## <a name="next-steps"></a>Étapes suivantes

- [Présentation du service Azure Data Factory](introduction.md)
