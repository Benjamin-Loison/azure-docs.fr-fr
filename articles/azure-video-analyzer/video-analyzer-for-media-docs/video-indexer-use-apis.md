---
title: Utiliser l’API Azure Video Analyzer for Media (anciennement Video Indexer)
description: Cet article explique comment bien démarrer avec l’API Azure Video Analyzer for Media (anciennement Video Indexer).
ms.date: 01/07/2021
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 83aa673625ad2119adc35571a8aaa98d3a3736a0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658637"
---
# <a name="tutorial-use-the-video-analyzer-for-media-api"></a>Didacticiel : utiliser l’API Azure Video Analyzer for Media

Video Analyzer for Media (anciennement Video Indexer) consolide plusieurs technologies audio et vidéo d’intelligence artificielle (IA) proposées par Microsoft dans un seul service intégré, ce qui simplifie le développement. Les API sont conçues pour permettre aux développeurs de se concentrer sur l’utilisation des technologies d’intelligence artificielle multimédia sans vous soucier de la mise à l’échelle, de la portée mondiale, de la disponibilité ou de la fiabilité des plateformes cloud. Vous pouvez utiliser l’API, entre autres, pour charger vos fichiers, obtenir des aperçus détaillés des vidéos et obtenir les URL des widgets intégrables de lecteur et des informations.

Lorsque vous créez un compte Azure Video Analyzer for Media, vous pouvez choisir un compte d’essai gratuit (où vous obtenez un certain nombre de minutes d’indexation gratuites) ou une option payante (où vous n’êtes pas limité par le quota). Avec un essai gratuit, Azure Video Analyzer for Media fournit jusqu’à 600 heures d’indexation gratuite aux utilisateurs du site web et jusqu’à 2 400 heures d’indexation gratuite aux utilisateurs de l’API. Avec une option payante, vous créez un compte Azure Video Analyzer for Media [connecté à votre abonnement Azure et un compte Azure Media Services](connect-to-azure.md). Vous payez pour les minutes indexées ; pour plus d’informations, consultez [Tarification Media Services](https://azure.microsoft.com/pricing/details/media-services/).

Cet article explique comment les développeurs peuvent tirer parti de l’[API Video Analyzer for Media](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>S’abonner à l’API

1. Connectez-vous au [Portail des développeurs Video Analyzer for Media](https://api-portal.videoindexer.ai/).

    Consultez la note de publication concernant les [informations de connexion](release-notes.md#october-2020).
    
     ![Se connecter au Portail des développeurs Video Analyzer for Media](./media/video-indexer-use-apis/sign-in.png)

   > [!Important]
   > * Vous devez utiliser le même fournisseur que lorsque vous vous êtes inscrit à Video Analyzer for Media.
   > * Les comptes Google et Microsoft (Outlook/Live) personnels ne peuvent être utilisés que pour les comptes d’évaluation. Les comptes connectés à Azure nécessitent Azure AD.
   > * Il ne peut y avoir qu’un seul compte actif par e-mail. Si un utilisateur tente de se connecter avec user@gmail.com pour LinkedIn et après avec user@gmail.com pour Google, ce dernier affiche une page d’erreur, indiquant que l’utilisateur existe déjà.

2. Abonnez-vous.

   Sélectionnez l’onglet [Produits](https://api-portal.videoindexer.ai/products). Ensuite, sélectionnez Autorisation et abonnez-vous.
    
   ![Onglet Produits dans le Portail des développeurs Video Indexer](./media/video-indexer-use-apis/authorization.png)

   > [!NOTE]
   > Les nouveaux utilisateurs sont automatiquement abonnés à l’API Autorisation.
    
   Après vous être abonné, vous pouvez trouver votre abonnement sous **Produits** -> **Autorisation**. Dans la page d’abonnement, vous trouverez les clés primaires et secondaires. Les clés doivent être protégées. Les clés doivent uniquement être utilisées par votre code serveur. Elles ne doivent pas être disponibles côté client (.js, .html, etc.).

   ![Abonnement et clés dans le Portail des développeurs Video Indexer](./media/video-indexer-use-apis/subscriptions.png)

> [!TIP]
> L’utilisateur de Video Analyzer for Media peut utiliser une clé d’abonnement unique pour se connecter à plusieurs comptes Video Analyzer for Media. Vous pouvez ensuite lier ces comptes Video Analyzer for Media à différents comptes Media Services.

## <a name="obtain-access-token-using-the-authorization-api"></a>Obtenir le jeton d’accès à l’aide de l’API Autorisation

Une fois abonné à l’API Autorisation, vous pouvez obtenir des jetons d’accès. Ces jetons d’accès sont utilisés pour l’authentification auprès de l’API Opérations.

Chaque appel à l’API Opérations doit être associé à un jeton d’accès correspondant à l’étendue d’autorisation de l’appel.

- Niveau Utilisateur : les jetons d’accès de niveau utilisateur vous permettent d’effectuer des opérations au niveau de l’**utilisateur**. Par exemple, obtenir les comptes associés.
- Niveau Compte : les jetons d’accès de niveau compte vous permettent d’effectuer des opérations au niveau du **compte** ou de la **vidéo**. Par exemple, charger la vidéo, répertorier toutes les vidéos, obtenir des aperçus de la vidéo, etc.
- Niveau Vidéo : les jetons d’accès de niveau vidéo vous permettent d’effectuer des opérations au niveau d’une **vidéo** spécifique. Par exemple, obtenir des aperçus de la vidéo, télécharger les sous-titres, obtenir des widgets, etc.

Il existe deux façons de contrôler le niveau d’autorisation des jetons :

* Pour les jetons de **compte**, vous pouvez utiliser l’API **Obtenir le jeton d’accès au compte avec une autorisation** et spécifier le type d’autorisation (**Lecteur**/ **Contributeur**/**MyAccessManager**/**Propriétaire**).
* Pour tous les types de jetons (dont les jetons de **compte**), vous pouvez spécifier **allowEdit=true/false**. **false** est l’équivalent d’une autorisation de **Lecteur** (lecture seule) et **true** est l’équivalent d’une autorisation de **Contributeur** (lecture-écriture).

Pour la plupart des scénarios de serveur à serveur, vous utiliserez probablement le même jeton de **compte**, dans la mesure où il couvre à la fois les opérations liées au **compte** et celles liées à la **vidéo**. Toutefois, si vous envisagez d’effectuer des appels côté client à Video Analyzer for Media (par exemple, à partir de JavaScript), vous pouvez utiliser un jeton d’accès de **vidéo** pour empêcher les clients d’accéder à l’intégralité du compte. Pour la même raison, lors de l’incorporation de code client Video Analyzer for Media dans votre client (par exemple, via le **widget d’obtention d’aperçu** ou le **widget d’obtention de lecteur**) vous devez fournir un jeton d’accès de **vidéo**.

Pour faciliter les choses, vous pouvez utiliser l’API **Autorisation** > **GetAccounts** (Obtenir les comptes) pour obtenir vos comptes sans pour autant disposer d’un jeton utilisateur. Vous pouvez également demander à obtenir les comptes avec des jetons valides, ce qui vous évite un appel supplémentaire pour obtenir un jeton de compte.

Les jetons d’accès expirent au bout d’une heure. Assurez-vous que votre jeton d’accès est valide avant d’utiliser l’API Opérations. S’il a expiré, rappelez l’API Autorisation pour obtenir un nouveau jeton d’accès.

Vous êtes prêt à commencer l’intégration avec l’API. Rechercher [la description détaillée de chaque API REST Video Analyzer for Media](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>ID de compte

Le paramètre ID de compte est requis dans tous les appels d’API opérationnelles. L’ID de compte est un GUID qui peut être obtenu de l’une des manières suivantes :

* Utilisez le **site web Video Analyzer for Media** pour obtenir l’ID de compte :

    1. Accédez au site web [Video Analyzer for Media](https://www.videoindexer.ai/) et connectez-vous.
    2. Accédez à la page **Paramètres**.
    3. Copiez l’ID de compte.

        ![Paramètres et ID de compte Video Analyzer for Media](./media/video-indexer-use-apis/account-id.png)

* Utilisez le **Portail des développeurs Video Analyzer for Media** pour obtenir l’ID de compte par programmation.

    Utilisez l’API [Get account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account).

    > [!TIP]
    > Vous pouvez générer des jetons d’accès pour les comptes en définissant `generateAccessTokens=true`.

* Obtenez l’ID de compte à partir de l’URL d’une page de lecteur dans votre compte.

    Lorsque vous regardez une vidéo, l’ID s’affiche après la section `accounts` et avant la section `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Recommandations

Cette section répertorie quelques recommandations pour l’utilisation de l’API Video Analyzer for Media.

- Si vous envisagez de charger une vidéo, il est recommandé de placer le fichier dans un emplacement réseau public (par exemple, un compte Stockage Blob Azure). Obtenez le lien vers la vidéo et indiquez l’URL en tant que paramètre de fichier de chargement.

    L’URL fournie à Video Analyzer for Media doit pointer vers un fichier multimédia (audio ou vidéo). Pour vérifier simplement l’URL (ou l’URL SAP), collez-la dans un navigateur : si le téléchargement ou la lecture du fichier démarre, il s’agit probablement d’une URL valide. Si le navigateur affiche une visualisation, ce n’est probablement pas un lien vers un fichier, mais une page HTML.

- Lorsque vous appelez l’API qui obtient des aperçus pour la vidéo spécifiée, vous obtenez une sortie JSON détaillée en tant que contenu de réponse. [Obtenez des détails sur la sortie JSON renvoyée dans cette rubrique](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Exemple de code

L’extrait de code C# suivant illustre l’utilisation conjointe de toutes les API Video Analyzer for Media.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2"; // replace with the account's location, or with “trial” if this is a trial account
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous avez terminé ce tutoriel, supprimez les ressources que vous n’envisagez pas d’utiliser.

## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble de Video Analyzer for Media](video-indexer-overview.md)
- [Régions](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Étapes suivantes

- [Examiner les détails de la sortie JSON](video-indexer-output-json-v2.md)
- Consultez l’[exemple de code](https://github.com/Azure-Samples/media-services-video-indexer) qui illustre un aspect important du chargement et de l’indexation d’une vidéo. Ce code vous permettra de voir comment utiliser les fonctionnalités de base de notre API. Veillez à lire les commentaires et nos conseils de bonnes pratiques.

