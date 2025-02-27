---
title: Incorporer les widgets Azure Video Analyzer for Media (anciennement Video Indexer) dans vos applications
description: Découvrez comment incorporer les widgets Azure Video Analyzer for Media (anciennement Video Indexer) dans vos applications.
ms.topic: how-to
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: 7ab396292a1fe7d44391f9f6ba75d69a57e57dd3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615944"
---
# <a name="embed-video-analyzer-for-media-widgets-in-your-apps"></a>Incorporer les widgets Video Analyzer for Media dans vos applications

Cet article vous explique comment incorporer les widgets Azure Video Analyzer for Media (anciennement Video Indexer) dans vos applications. Video Analyzer for Media prend en charge l’incorporation de trois types de widgets dans vos applications : *Insights cognitifs*, *Lecteur* et *Éditeur*.

À partir de la version 2, l’URL de base du widget inclut la région du compte spécifié. Par exemple, un compte situé dans la région USA Ouest génère : `https://www.videoindexer.ai/embed/insights/.../?location=westus2`.

## <a name="widget-types"></a>Types de widgets

### <a name="cognitive-insights-widget"></a>Widget Insight cognitifs

Un widget Cognitive Insights inclut tous les insights visuels extraits à partir du processus d’indexation de votre vidéo. Le widget Insight cognitifs prend en charge les paramètres d’URL facultatifs suivants :

|Nom|Définition|Description|
|---|---|---|
|`widgets` | Chaînes séparées par des virgules | Vous permet de contrôler les insights dont vous voulez faire le rendu.<br/>Exemple : `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` affiche uniquement les insights d’IU des personnes et des mots clés.<br/>Options disponibles : people, animatedCharacters,keywords, labels, sentiments, emotions, topics, keyframes, transcript, ocr, speakers, scenes et namedEntities.|
|`controls`|Chaînes séparées par des virgules|Vous permet de contrôler les contrôles que vous voulez restituer.<br/>Exemple : `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` affiche uniquement l’option de recherche et le bouton de téléchargement.<br/>Options disponibles : search, download, presets, language.|
|`language`|Code de langue court (nom de la langue)|Contrôle la langue des insights.<br/>Exemple : `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>ou `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Code de langue court | Contrôle la langue de l’interface utilisateur. La valeur par défaut est `en`. <br/>Exemple : `locale=de`.|
|`tab` | Onglet sélectionné par défaut | Contrôle l’onglet **Insights** affiché par défaut. <br/>Exemple : `tab=timeline` affiche les insights avec l’onglet **Timeline** (Chronologie) sélectionné.|
|`location` ||Le paramètre `location` doit être inclus dans les liens incorporés. Découvrez [comment obtenir le nom de votre région](regions.md). Si votre compte est en préversion, `trial` doit être utilisé pour la valeur d’emplacement. `trial` est la valeur par défaut pour le paramètre `location`.| 

### <a name="player-widget"></a>Widget Lecteur

Vous pouvez utiliser le widget Lecteur pour le streaming vidéo avec vitesse de transmission adaptative. Le widget Lecteur prend en charge les paramètres d’URL facultatifs suivants.

|Nom|Définition|Description|
|---|---|---|
|`t` | Secondes écoulées depuis le début | Fait démarrer le lecteur à partir d’un instant donné.<br/> Exemple : `t=60`. |
|`captions` | Code de langue | Extrait les sous-titres dans la langue spécifiée pendant le chargement du widget pour les rendre disponibles dans le menu **Captions** (Sous-titres).<br/> Exemple : `captions=en-US`. |
|`showCaptions` | Une valeur booléenne | Permet de charger le lecteur avec les sous-titres déjà activés.<br/> Exemple : `showCaptions=true`. |
|`type`| | Active une apparence du lecteur audio (la partie vidéo est supprimée).<br/> Exemple : `type=audio`. |
|`autoplay` | Une valeur booléenne | Indique si le lecteur doit commencer la lecture de la vidéo après le chargement. La valeur par défaut est `true`.<br/> Exemple : `autoplay=false`. |
|`language`/`locale` | Code de langue | Détermine la langue du lecteur. La valeur par défaut est `en-US`.<br/>Exemple : `language=de-DE`.|
|`location` ||Le paramètre `location` doit être inclus dans les liens incorporés. Découvrez [comment obtenir le nom de votre région](regions.md). Si votre compte est en préversion, `trial` doit être utilisé pour la valeur d’emplacement. `trial` est la valeur par défaut pour le paramètre `location`.| 

### <a name="editor-widget"></a>Widget de l’éditeur

Vous pouvez utiliser le widget Éditeur pour créer des projets et de gérer les insights d’une vidéo. Le widget Éditeur prend en charge les paramètres d’URL facultatifs suivants.

|Nom|Définition|Description|
|---|---|---|
|`accessToken`<sup>*</sup> | String | Fournit l’accès aux vidéos qui se trouvent uniquement dans le compte utilisé pour incorporer le widget.<br> Le widget Éditeur nécessite le paramètre `accessToken`. |
|`language` | Code de langue | Détermine la langue du lecteur. La valeur par défaut est `en-US`.<br/>Exemple : `language=de-DE`. |
|`locale` | Code de langue court | Contrôle la langue des insights. La valeur par défaut est `en`.<br/>Exemple : `language=de`. |
|`location` ||Le paramètre `location` doit être inclus dans les liens incorporés. Découvrez [comment obtenir le nom de votre région](regions.md). Si votre compte est en préversion, `trial` doit être utilisé pour la valeur d’emplacement. `trial` est la valeur par défaut pour le paramètre `location`.| 

<sup>*</sup>Le propriétaire doit fournir le `accessToken` avec prudence.

## <a name="embed-videos"></a>Incorporer une vidéo

Cette section traite de l’incorporation de vidéos avec [le portail](#the-portal-experience) ou grâce à [l’assemblage manuel de l’URL](#assemble-the-url-manually) dans les applications. 

Le paramètre `location` doit être inclus dans les liens incorporés. Découvrez [comment obtenir le nom de votre région](regions.md). Si votre compte est en préversion, `trial` doit être utilisé pour la valeur d’emplacement. `trial` est la valeur par défaut pour le paramètre `location`. Par exemple : `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

### <a name="the-portal-experience"></a>L’expérience du portail

Pour incorporer une vidéo, utilisez le portail comme indiqué ci-dessous :

1. Connectez-vous au site web [Azure Video Analyzer for Media](https://www.videoindexer.ai/).
1. Sélectionnez la vidéo que vous souhaitez utiliser et appuyez sur **Lire**.
1. Sélectionnez le type de widget souhaité (**Cognitive Insights**, **Lecteur** ou **Éditeur**).
1. Cliquez sur **&lt;/&gt; Incorporer**.
5. Copiez le code incorporé (qui s’affiche dans **Copier le code incorporer** dans la boîte de dialogue **Partager et incorporer**).
6. Ajoutez le code à votre application.

> [!NOTE]
> Le partage d’un lien pour le widget **Player** ou **Insights** inclut le jeton d’accès et accorde les autorisations en lecture seule à votre compte.

### <a name="assemble-the-url-manually"></a>Assembler l’URL manuellement

#### <a name="public-videos"></a>Vidéos publiques

Vous pouvez incorporer des vidéos publiques en assemblant l’URL comme suit :

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>`
  
  
#### <a name="private-videos"></a>Vidéos privées

Pour incorporer une vidéo privée, vous devez passer un jeton d’accès (utilisez [Obtenir un jeton d’accès de vidéo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) dans `src`l’attribut de l’iframe) :

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
  
### <a name="provide-editing-insights-capabilities"></a>Fournir des fonctionnalités de modification d’insights

Pour fournir des fonctionnalités de modification d’insights dans votre widget incorporé, vous devez transmettre un jeton d’accès contenant les autorisations de modification. Utilisez [Obtenir un jeton d’accès de vidéo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) avec `&allowEdit=true`.

## <a name="widgets-interaction"></a>Interaction de widgets

Le widget Insights cognitifs peut interagir avec une vidéo sur votre application. Cette section montre comment réaliser cette interaction.

![Widget Cognitive Insights Video Analyzer for Media](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>Présentation du flux

Lorsque vous modifiez les transcriptions, le flux suivant se produit :

1. Vous modifiez la transcription dans la chronologie.
1. Video Analyzer for Media obtient ces mises à jour et les enregistre dans le fichier [from transcript edits](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) dans le modèle linguistique.
1. Les sous-titres sont mis à jour :

    * Si vous utilisez le widget de lecteur de Video Analyzer for Media, il est automatiquement mis à jour.
    * Si vous utilisez un lecteur externe, vous recevez un nouveau fichier de sous-titres que l’utilisateur peut appeler avec la commande **Obtenir les sous-titres des vidéos**.

### <a name="cross-origin-communications"></a>Communications cross-origin

Pour que les widgets Video Analyzer for Media communiquent avec les autres composants, le service Video Analyzer for Media :

- Utilise la méthode de communication HTML5 cross-origin `postMessage`.
- Valide le message vers l’origine VideoIndexer.ai.

Si vous implémentez votre propre code de lecteur et l’intégrez aux widgets Cognitive Insights, il vous incombe de vérifier l’origine du message provenant de VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Incorporer des widgets dans votre application ou votre blog (recommandé)

Cette section montre comment faire interagir deux widgets Video Analyzer for Media de sorte que le lecteur aille directement au moment approprié quand un utilisateur sélectionne le contrôle d’insight de votre application.

1. Copiez le code incorporé du widget Lecteur.
2. Copiez le code incorporé Cognitive Insights.
3. Ajoutez le [fichier Médiateur](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) pour gérer la communication entre les deux widgets :<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Quand un utilisateur sélectionne le contrôle d’insight dans votre application, le lecteur va maintenant directement au moment approprié.

Pour plus d’informations, consultez la démo [Video Analyzer for Media - Incorporer les deux widgets](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Incorporer le widget Insight cognitifs et utiliser le lecteur multimédia Azure pour lire le contenu

Cette section montre comment obtenir une interaction entre un widget Cognitive Insights et une instance du lecteur multimédia Azure à l’aide du [plug-in AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Ajouter un plug-in Video Analyzer for Media pour le lecteur AMP :<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instanciez le lecteur multimédia Azure avec le plug-in Video Analyzer for Media.

    ```javascript
    // Init the source.
    function initSource() {
        var tracks = [{
        kind: 'captions',
        // To load vtt from VI, replace it with your vtt URL.
        src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
        srclang: 'en',
        label: 'English'
        }];
        myPlayer.src([
        {
            "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
            "type": "application/vnd.ms-sstr+xml"
        }
        ], tracks);
    }

    // Init your AMP instance.
    var myPlayer = amp('vid1', { /* Options */
        "nativeControlsForTouch": false,
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
        videobreakedown: {}
        }
    }, function () {
        // Activate the plug-in.
        this.videobreakdown({
        videoId: "c4c1ad4c9a",
        syncTranscript: true,
        syncLanguage: true,
        location: "trial" /* location option for paid accounts (default is trial) */
        });

        // Set the source dynamically.
        initSource.call(this);
    });
    ```

3. Copiez le code incorporé Cognitive Insights.

Vous pouvez désormais communiquer avec le Lecteur multimédia Azure.

Pour plus d’informations, consultez la démo [Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/rYONrO) (Lecteur multimédia Azure et Video Indexer Insights).

### <a name="embed-the-video-analyzer-for-media-cognitive-insights-widget-and-use-a-different-video-player"></a>Incorporer le widget Video Analyzer for Media Cognitive Insights et utiliser un autre lecteur vidéo

Si vous utilisez un lecteur vidéo autre que le lecteur multimédia Azure, vous devez le manipuler manuellement pour obtenir la communication.

1. Insérez votre lecteur vidéo.

    Par exemple, un lecteur HTML5 standard :

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. Incorporez le widget Insight cognitifs.
3. Implémenter une communication pour votre lecteur en écoutant l’événement « message ». Par exemple :

    ```javascript
    <script>
    
        (function(){
        // Reference your player instance.
        var playerInstance = document.getElementById('vid1');
        
        function jumpTo(evt) {
          var origin = evt.origin || evt.originalEvent.origin;
        
          // Validate that the event comes from the videoindexer domain.
          if ((origin === "https://www.videoindexer.ai") && evt.data.time !== undefined){
                
            // Call your player's "jumpTo" implementation.
            playerInstance.currentTime = evt.data.time;
               
            // Confirm the arrival to us.
            if ('postMessage' in window) {
              evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
            }
          }
        }
        
        // Listen to the message event.
        window.addEventListener("message", jumpTo, false);
          
        }())    
        
    </script>
    ```

Pour plus d’informations, consultez la démo [Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/YEyPLd) (Lecteur multimédia Azure et Video Indexer Insights).

## <a name="adding-subtitles"></a>Ajout de sous-titres

Si vous incorporez des insights Video Analyzer for Media avec votre propre [Lecteur multimédia Azure](https://aka.ms/azuremediaplayer), vous pouvez utiliser la méthode `GetVttUrl` pour obtenir des sous-titres. Vous pouvez également appeler une méthode JavaScript à partir du plug-in AMP Video Analyzer for Media `getSubtitlesUrl` (comme indiqué précédemment).

## <a name="customizing-embeddable-widgets"></a>Personnalisation des widgets intégrables

### <a name="cognitive-insights-widget"></a>Widget Insight cognitifs

Vous pouvez utiliser les types d’insights de votre choix. Pour cela, spécifiez-les sous forme de valeur dans le paramètre d’URL suivant qui est ajouté au code incorporé que vous obtenez (à partir de l’API ou de l’application web) : `&widgets=<list of wanted widgets>`.

Les valeurs possibles sont : `people`, `animatedCharacters`, `keywords`, `labels`, `sentiments`, `emotions`, `topics`, `keyframes`, `transcript`, `ocr`, `speakers`, `scenes` et `namedEntities`.

Par exemple, si vous souhaitez incorporer un widget contenant uniquement des insights de personnes et de mots clés, l’URL incorporée d’iframe se présentera comme suit :

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

Le titre de la fenêtre d'iframe peut également être personnalisé en ajoutant `&title=<YourTitle>` à l'URL d'iframe. (Cela personnalise la valeur HTML `<title>`).
   
Par exemple, si vous souhaitez donner à la fenêtre d'iframe le titre « MyInsights », l'URL se présentera comme suit :

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Notez que cette option n’est utile que les cas où vous avez besoin d’ouvrir les insights dans une nouvelle fenêtre.

### <a name="player-widget"></a>Widget Lecteur

Si vous incorporez le lecteur Video Analyzer for Media, vous pouvez choisir la taille du lecteur en spécifiant la taille de l’iframe.

Exemple :

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Par défaut, le lecteur Video Analyzer for Media propose des sous-titres générés automatiquement basés sur la transcription de la vidéo. La transcription est extraite de la vidéo dans la langue source sélectionnée lors du chargement de la vidéo.

Si vous souhaitez effectuer l’incorporation dans une autre langue, vous pouvez ajouter `&captions=<Language Code>` à l’URL de lecteur incorporée. Si vous voulez que les sous-titres soient affichés par défaut, vous pouvez passer &showCaptions=true.

L'URL incorporée se présentera comme suit :

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Lecture automatique

Par défaut, le lecteur démarre la lecture de la vidéo. Vous pouvez empêcher cette action en passant `&autoplay=false` à l’URL incorporée précédente.

## <a name="code-samples"></a>Exemples de code

Consultez le référentiel d’[exemples de code](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) qui contient des exemples pour l’API Video Analyzer for Media et des widgets :

| Fichier/Dossier                       | Description                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Chargez la vidéo Video Analyzer for Media dans un lecteur multimédia Azure personnalisé.                        |
| `azure-media-player-vi-insights`  | Incorporer VI Insights à un Lecteur multimédia Azure personnalisé.                             |
| `control-vi-embedded-player`      | Incorporer VI Player et le contrôler à partir de l’extérieur.                                    |
| `custom-index-location`           | Incorporer VI Insights à partir d’un emplacement externe personnalisé (peut être un blob de client).     |
| `embed-both-insights`             | Utilisation de base de VI Insights, à la fois lecteur et insights.                            |
| `embed-insights-with-AMP`         | Incorporer un widget VI Insights à un Lecteur multimédia Azure personnalisé.                      |
| `customize-the-widgets`           | Incorporer des widgets VI Insights avec des options personnalisées.                                     |
| `embed-both-widgets`              | Incorporer VI Player et VI Insights et établir une communication entre les deux.                      |
| `url-generator`                   | Génère des widgets avec une URL d’incorporation personnalisée en fonction des options spécifiées par l’utilisateur.             |
| `html5-player`                    | Incorporer VI Insights à un lecteur vidéo HTML5 par défaut.                           |

## <a name="supported-browsers"></a>Navigateurs pris en charge

Pour en savoir plus, consultez [Navigateurs pris en charge](video-indexer-overview.md#supported-browsers).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon d’afficher et de modifier les insights Video Analyzer for Media, consultez [Afficher et modifier les insights Video Analyzer for Media](video-indexer-view-edit.md).

En outre, consultez [Video Analyzer for Media CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
