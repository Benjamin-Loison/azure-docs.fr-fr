---
title: 'Démarrage rapide : Configuration de la plateforme du kit SDK Speech pour C++ (macOS) – Service Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour configurer votre plateforme pour C++ sur macOS avec le kit SDK du service Speech.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: eur
ms.openlocfilehash: 73a6c99b3f35fa43852981edb3343cf9644e9662
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252500"
---
Ce guide explique comment installer le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour C++ sur macOS 10.14 et versions ultérieures.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Configuration système requise

macOS 10.14 et versions ultérieures

## <a name="install-speech-sdk"></a>Installer le kit SDK Speech

1. Choisissez un répertoire dans lequel extraire les fichiers du kit SDK Speech, et définissez la variable d’environnement `SPEECHSDK_ROOT` pour qu’elle pointe vers ce répertoire. Cette variable facilite ensuite la référence au répertoire dans les futures commandes. Par exemple, si vous souhaitez utiliser le répertoire `speechsdk` dans votre répertoire de base, utilisez une commande semblable à la suivante :

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. S’il n’existe pas encore, créez ce répertoire.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Téléchargez et extrayez l’archive `.zip` contenant le framework SDK Speech :

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Validez le contenu du répertoire de niveau supérieur du package extrait :

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   La liste des répertoires doit contenir les fichiers de notification et de licence de tiers, ainsi qu’un répertoire `MicrosoftCognitiveServicesSpeech.xcframework`.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [windows](../quickstart-list.md)]
