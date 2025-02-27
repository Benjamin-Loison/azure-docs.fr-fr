---
title: Envoyer des événements personnalisés à un point de terminaison web - Event Grid, Portail Azure
description: 'Démarrage rapide : Utilisez Azure Event Grid et le portail Azure pour publier une rubrique personnalisée et pour vous abonner aux événements de cette rubrique. Les événements sont gérés par une application web.'
ms.date: 07/01/2021
ms.topic: quickstart
ms.openlocfilehash: 461d43e2a0210dfd5844beb844c7ae2a48e936f3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310069"
---
# <a name="route-custom-events-to-web-endpoint-with-the-azure-portal-and-event-grid"></a>Router des événements personnalisés vers un point de terminaison web avec le portail Azure et Event Grid
Event Grid est un service entièrement géré qui vous permet de facilement gérer des événements dans de nombreux services et applications Azure. Il simplifie la création d’applications pilotées par les événements et serverless. Pour une présentation du service, consultez [Vue d’ensemble d’Event Grid](overview.md).

Dans cet article, vous utilisez le portail Azure pour effectuer les tâches suivantes : 

1. Créez une rubrique personnalisée.
1. Abonnez-vous à la rubrique personnalisée.
1. Déclenchez l'événement.
1. Affichez le résultat. En règle générale, vous envoyez des événements à un point de terminaison qui traite les données d’événement et entreprend des actions. Toutefois, pour simplifier cet article, vous envoyez les événements à une application web qui collecte et affiche les messages.


## <a name="prerequisites"></a>Prérequis
[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Créer une rubrique personnalisée
Une rubrique de grille d’événement fournit un point de terminaison défini par l’utilisateur vers lequel vous envoyez vos événements. 

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans la barre de recherche de la rubrique, tapez **Rubriques Event Grid**, puis sélectionnez **Rubriques Event Grid** dans la liste déroulante. 

    Rechercher et sélectionner :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Rubriques Event Grid":::
3. Dans la page **Rubriques Event Grid**, sélectionnez **+ Créer** dans la barre d’outils. 
4. Dans la page **Créer une rubrique**, procédez comme suit :
    1. Sélectionnez votre **abonnement** Azure.
    2. Sélectionnez un groupe de ressources existant ou sélectionnez **Créer**, puis entrez un **nom** pour le **groupe de ressources**.
    3. Donnez un **nom** unique à la rubrique personnalisée. Le nom de la rubrique doit être unique, car elle est représentée par une entrée DNS. N’utilisez pas le nom indiqué dans l’image. Au lieu de cela, créez votre propre nom : il doit comprendre entre 3 et 50 caractères, et contenir uniquement des valeurs a-z, A-Z, 0-9 et « - ».
    4. Sélectionnez un **emplacement** pour la rubrique Event Grid.
    5. Au bas de la page, sélectionnez **Examiner et créer**. 

        :::image type="content" source="./media/custom-event-quickstart-portal/create-custom-topic.png" alt-text="Page Créer une rubrique":::
    6. Sous l’onglet **Vérifier + créer** de la page **Créer une rubrique**, sélectionnez **Créer**. 
    
        :::image type="content" source="./media/custom-event-quickstart-portal/review-create-page.png" alt-text="Vérifier les paramètres et créer":::
5. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource** pour voir la page **Rubrique système Event Grid** pour votre rubrique. Ne fermez pas cette page. Vous l’utiliserez plus loin dans le guide de démarrage rapide. 

    :::image type="content" source="./media/custom-event-quickstart-portal/event-grid-topic-home-page.png" alt-text="Capture d’écran montrant la page d’hébergement de la rubrique Event Grid":::

## <a name="create-a-message-endpoint"></a>Créer un point de terminaison de message
Avant de créer un abonnement pour la rubrique personnalisée, créez un point de terminaison pour le message d’événement. En règle générale, le point de terminaison entreprend des actions en fonction des données d’événement. Pour simplifier ce guide de démarrage rapide, déployez une [application web prédéfinie](https://github.com/Azure-Samples/azure-event-grid-viewer) qui affiche les messages d’événement. La solution déployée comprend un plan App Service, une offre App Service Web Apps et du code source en provenance de GitHub.

1. Dans la page de l’article, sélectionnez **Déployer sur Azure** pour déployer la solution sur votre abonnement. Dans le portail Azure, indiquez des valeurs pour les paramètres.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to deploy to Azure."></a>
2. Dans la page **Déploiement personnalisé**, procédez comme suit : 
    1. Pour **Groupe de ressources**, sélectionnez le groupe de ressources que vous avez créé lors de la création du compte de stockage. Il sera plus facile pour vous de nettoyer une fois que vous aurez terminé le tutoriel en supprimant le groupe de ressources.  
    2. Pour **Nom du site**, entrez un nom pour l’application web.
    3. Pour **Nom du plan d’hébergement**, entrez un nom pour le plan App Service à utiliser pour l’hébergement de l’application web.
    5. Sélectionnez **Revoir + créer**. 

        :::image type="content" source="./media/blob-event-quickstart-portal/template-deploy-parameters.png" alt-text="Capture d’écran montrant la page Déploiement personnalisé.":::
1. Dans la page **Vérifier + créer**, sélectionnez **Créer**. 
1. Le déploiement peut prendre quelques minutes. Sélectionnez Alertes (icône représentant une cloche) dans le portail, puis **Accéder au groupe de ressources**. 

    ![Alerte - Accédez au groupe de ressources.](./media/blob-event-quickstart-portal/navigate-resource-group.png)
4. Dans la page **Groupe de ressources**, dans la liste des ressources, sélectionnez l’application web que vous avez créée. Vous pouvez également voir le plan App Service et le compte de stockage dans cette liste. 

    ![Sélectionnez un site web.](./media/blob-event-quickstart-portal/resource-group-resources.png)
5. Dans la page **App Service** de votre application web, sélectionnez l’URL pour accéder au site web. L’URL doit être au format suivant : `https://<your-site-name>.azurewebsites.net`.
    
    ![Accédez au site web.](./media/blob-event-quickstart-portal/web-site.png)

6. Confirmez que vous voyez le site, mais qu’aucun événement n’a encore été posté sur celui-ci.

   ![Affichez le nouveau site.](./media/blob-event-quickstart-portal/view-site.png)

## <a name="subscribe-to-custom-topic"></a>S’abonner à une rubrique personnalisée

Vous vous abonnez à une rubrique Event Grid pour indiquer à Event Grid les événements qui vous intéressent, et où les envoyer.

1. Maintenant, dans la page **Rubrique Event Grid** de votre rubrique personnalisée, sélectionnez **+ Abonnement aux événements** sur la barre d’outils.

    :::image type="content" source="./media/custom-event-quickstart-portal/new-event-subscription.png" alt-text="Bouton d’ajout d’abonnement aux événements":::
2. Dans la page **Créer un abonnement aux événements**, effectuez les étapes suivantes :
    1. Entrez un **nom** pour l’abonnement aux événements.
    3. Sélectionnez **Webhook** pour **Type de point de terminaison**. 
    4. Choisissez **Sélectionner un point de terminaison**. 

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-subscription-values.png" alt-text="Fournir des valeurs d’abonnement à un événement":::
    5. Pour le point de terminaison du webhook, indiquez l’URL de votre application web et ajoutez `api/updates` à l’URL de la page d’accueil. Sélectionnez **Confirmer la sélection**.

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-endpoint.png" alt-text="Fournir une URL du point de terminaison":::
    6. Dans la page **Créer un abonnement aux événements**, sélectionnez **Créer**.

3. Affichez à nouveau votre application web, et notez qu’un événement de validation d’abonnement lui a été envoyé. Sélectionnez l’icône en forme d’œil pour développer les données d’événements. Event Grid envoie l’événement de validation pour que le point de terminaison puisse vérifier qu’il souhaite recevoir des données d’événement. L’application web inclut du code pour valider l’abonnement.

    ![Afficher l’événement d’abonnement](./media/custom-event-quickstart-portal/view-subscription-event.png)

## <a name="send-an-event-to-your-topic"></a>Envoyer un événement à votre rubrique

Nous allons maintenant déclencher un événement pour voir comment Event Grid distribue le message à votre point de terminaison. Utilisez Azure CLI ou PowerShell pour envoyer un événement de test à votre rubrique personnalisée. En règle générale, une application ou un service Azure envoie les données d’événements.

Le premier exemple utilise Azure CLI. Il obtient l’URL et la clé de la rubrique personnalisée, ainsi que les exemples de données d’événements. Utilisez le nom de votre rubrique personnalisée pour `<topic name>`. Des exemples de données d’événement sont créés. L’élément `data` du fichier JSON est la charge utile de l’événement. N’importe quel fichier JSON bien construit peut être placé dans ce champ. Vous pouvez aussi utiliser le champ objet pour un routage et un filtrage avancés. CURL est un utilitaire qui envoie des requêtes HTTP.


### <a name="azure-cli"></a>Azure CLI
1. Dans le portail Azure, sélectionnez **Cloud Shell**. Cloud Shell s’ouvre dans le volet inférieur du navigateur web. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="Icône Sélectionner Cloud Shell":::
1. Sélectionnez **Bash** dans l’angle supérieur gauche de la fenêtre Cloud Shell. 

    ![Cloud Shell - Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Exécutez la commande suivante afin d’obtenir le **point de terminaison** pour la rubrique : Après avoir copié et collé la commande, et avant de l’exécuter, mettez à jour le **nom de la rubrique** et le **nom du groupe de ressources**. Vous allez publier des exemples d’événements sur ce point de terminaison de rubrique. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Exécutez la commande suivante afin d’obtenir la **clé** pour la rubrique personnalisée  : Après avoir copié et collé la commande, et avant de l’exécuter, mettez à jour le **nom de la rubrique** et le nom du **groupe de ressources**. Il s’agit de la clé primaire de la rubrique Event Grid. Pour récupérer cette clé à partir du portail Azure, basculez vers l’onglet **Clés d’accès** de la page **Rubrique Event Grid**. Pour être en mesure de poster un événement dans une rubrique personnalisée, vous avez besoin de la clé d’accès. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Copiez l’instruction suivante avec la définition de l’événement, puis appuyez sur **Entrée**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Exécutez la commande **Curl** suivante pour publier l’événement : Dans la commande, l’en-tête `aeg-sas-key` est défini sur la clé d’accès que vous avez obtenue précédemment. 

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
Le deuxième exemple utilise PowerShell pour effectuer des étapes similaires.

1. Dans le portail Azure, sélectionnez **Cloud Shell** (ou accédez à `https://shell.azure.com/`). Cloud Shell s’ouvre dans le volet inférieur du navigateur web. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="Icône Sélectionner Cloud Shell":::
1. Dans **Cloud Shell**, sélectionnez **PowerShell** en haut à gauche de la fenêtre Cloud Shell. Consultez l’exemple d’image de fenêtre **Cloud Shell** dans la section Azure CLI.
2. Définissez les variables suivantes. Après avoir copié et collé la commande, et avant de l’exécuter, mettez à jour le **nom de la rubrique** et le **nom du groupe de ressources** :

    **Groupe de ressources** :
    ```powershell
    $resourceGroupName = "<resource group name>"
    ```

    **Nom de la rubrique Event Grid** :    
    ```powershell
    $topicName = "<topic name>"
    ```
3. Exécutez les commandes suivantes afin d’obtenir le **point de terminaison** et les **clés** pour la rubrique :

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. Préparez l’événement. Copiez et exécutez les instructions dans la fenêtre Cloud Shell. 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. Utilisez l’applet de commande **Invoke-WebRequest** pour envoyer l’événement. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Vérifier dans la visionneuse Event Grid
Vous avez déclenché l’événement, et Event Grid a envoyé le message au point de terminaison configuré lors de l’abonnement. Affichez votre application web pour voir l’événement que vous venez d’envoyer.

:::image type="content" source="./media/custom-event-quickstart-portal/event-grid-viewer-end.png" alt-text="Visionneuse Event Grid":::

## <a name="clean-up-resources"></a>Nettoyer les ressources
Si vous envisagez de continuer à utiliser cet événement, ne supprimez pas les ressources créées dans cet article. Dans le cas contraire, supprimez les ressources créées avec cet article.

1. Sélectionnez **Groupes de ressources** dans le menu de gauche. Si vous ne voyez pas cette option dans le menu de gauche, sélectionnez **Tous les services** dans ce menu, puis sélectionnez **Groupes de ressources**. 

    ![Groupes de ressources](./media/custom-event-quickstart-portal/delete-resource-groups.png)
1. Sélectionnez le groupe de ressources pour lancer la page **Groupe de ressources**. 
1. Sélectionnez **Supprimer le groupe de ressources** sur la barre d’outils. 
1. Confirmez la suppression en entrant le nom du groupe de ressources, puis sélectionnez **Supprimer**. 

    L’autre groupe de ressources que vous voyez dans l’image a été créé et utilisé par la fenêtre Cloud Shell. Supprimez-le si vous n’envisagez pas d’utiliser la fenêtre Cloud Shell. 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez créer des rubriques et des abonnements d’événements personnalisés, vous pouvez en apprendre davantage sur Event Grid et ce qu’il peut vous offrir :

- [À propos d’Event Grid](overview.md)
- [Acheminer des événements de stockage Blob Azure vers un point de terminaison Web personnalisé ](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Surveiller les modifications d’une machine virtuelle avec Azure Event Grid et Azure Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Diffuser en continu des Big Data dans un entrepôt de données](event-grid-event-hubs-integration.md)

Consultez les exemples suivants pour en savoir plus sur la publication d’événements sur Event Grid et leur consommation avec différents langages de programmation. 

- [Exemples Azure Event Grid pour .NET](/samples/azure/azure-sdk-for-net/azure-event-grid-sdk-samples/)
- [Exemples Azure Event Grid pour Java](/samples/azure/azure-sdk-for-java/eventgrid-samples/)
- [Exemples Azure Event Grid pour Python](/samples/azure/azure-sdk-for-python/eventgrid-samples/)
- [Exemples Azure Event Grid pour JavaScript](/samples/azure/azure-sdk-for-js/eventgrid-javascript/)
- [Exemples Azure Event Grid pour TypeScript](/samples/azure/azure-sdk-for-js/eventgrid-typescript/)
