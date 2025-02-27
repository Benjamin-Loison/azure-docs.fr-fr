---
title: 'Démarrage rapide : API REST Reconnaissance optique de caractères'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez vous familiariser avec l'API REST Reconnaissance optique de caractères.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ceeb1804c9332d9e0d3e11336ff92e8aacc8516c
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585101"
---
Utilisez l'API REST Reconnaissance optique de caractères pour lire du texte imprimé et manuscrit.

> [!NOTE]
> Ce guide de démarrage rapide utilise des commandes cURL pour appeler l’API REST. Vous pouvez également appeler l’API REST à l’aide d’un langage de programmation. Vous pouvez consulter des exemples [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST) et [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST) sur GitHub.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/) 
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="créez une ressource Vision par ordinateur"  target="_blank">créer une ressource Vision par ordinateur </a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
  * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application au service Vision par ordinateur. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
  * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
* Installation de [cURL](https://curl.haxx.se/)


## <a name="extract-printed-and-handwritten-text"></a>Extraire du texte imprimé et manuscrit

Le service OCR peut extraire le texte visible dans une image ou un document et le convertir en flux de caractères. Pour plus d'informations sur l’extraction de texte, consultez la [Présentation de la reconnaissance optique de caractères (OCR)](../overview-ocr.md).


### <a name="call-the-read-api"></a>Appeler l’API Lire

Pour créer et exécuter l’exemple, effectuez les étapes suivantes :

1. Copiez la commande ci-après dans un éditeur de texte.
1. Modifiez la commande comme ci-dessous :
    1. Remplacez la valeur de `<subscriptionKey>` par votre clé d’abonnement.
    1. Remplacez la première partie de l’URL de la demande (`westcentralus`) par le texte de votre propre URL de point de terminaison.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Éventuellement, changez l’URL d’image dans le corps de la requête (`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`) par l’URL d’une autre image à analyser.
1. Ouvrir une fenêtre d’invite de commandes.
1. Collez la commande à partir de l’éditeur de texte dans la fenêtre d’invite de commandes, puis exécutez la commande.

```bash
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

La réponse inclut un en-tête `Operation-Location`, dont la valeur est une URL unique. Utilisez cette URL pour interroger les résultats de l’opération de lecture. L’URL expire au bout de 48 heures.

### <a name="how-to-use-preview-features"></a>Comment utiliser les fonctionnalités en préversion
Pour les langues et les fonctionnalités en préversion, consultez [Comment spécifier la version du modèle](../Vision-API-How-to-Topics/call-read-api.md#determine-how-to-process-the-data-optional) pour utiliser la dernière préversion. Le modèle en préversion comprend toutes les améliorations apportées aux langues et fonctionnalités actuellement en disponibilité générale.

### <a name="get-read-results"></a>Obtenir les résultats de la lecture

1. Copiez la commande ci-après dans votre éditeur de texte.
1. Remplacez l’URL par la valeur `Operation-Location` que vous avez copiée à l’étape précédente.
1. Modifiez la commande comme ci-dessous :
    1. Remplacez la valeur de `<subscriptionKey>` par votre clé d’abonnement.
1. Ouvrir une fenêtre d’invite de commandes.
1. Collez la commande à partir de l’éditeur de texte dans la fenêtre d’invite de commandes, puis exécutez la commande.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>Examiner la réponse

Une réponse correcte est retournée au format JSON. L’exemple d’application analyse et affiche une réponse réussie dans la fenêtre d’invite de commandes, comme dans l’exemple suivant :

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-04-08T21:56:17.6819115+00:00",
  "lastUpdatedDateTime": "2021-04-08T21:56:18.4161316+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 338,
        "height": 479,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              25,
              14,
              318,
              14,
              318,
              59,
              25,
              59
            ],
            "text": "NOTHING",
            "appearance": {
              "style": {
                "name": "other",
                "confidence": 0.971
              }
            },
            "words": [
              {
                "boundingBox": [
                  27,
                  15,
                  294,
                  15,
                  294,
                  60,
                  27,
                  60
                ],
                "text": "NOTHING",
                "confidence": 0.994
              }
            ]
          }
        ]
      }
    ]
  }
}

```



## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à appeler l’API REST Read. À présent, découvrez-en plus sur les fonctionnalités de l’API Lire.

> [!div class="nextstepaction"]
>[Appeler l’API Lire](../Vision-API-How-to-Topics/call-read-api.md)

* [Vue d’ensemble de la reconnaissance OCR](../overview-ocr.md)
