---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 07/02/2021
ms.author: eur
ms.openlocfilehash: c15f92a86aa9b9bf0da64f93ad5bb42bf8fff5ac
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510767"
---
Dans ce guide de démarrage rapide, vous découvrez les modèles de conception courants qui permettent d’utiliser la synthèse vocale au moyen du kit SDK Speech. Vous commencez par créer une configuration et une synthèse de base, puis passez à des exemples plus poussés en matière de développement d’applications personnalisées, notamment :

* Obtention de réponses en tant que flux en mémoire
* Personnalisation du taux d’échantillonnage de sortie et de la vitesse de transmission
* Envoi de demandes de synthèse à l’aide de SSML (Speech Synthesis Markup Language)
* Utilisation de voix neurales

## <a name="skip-to-samples-on-github"></a>Passer aux exemples sur GitHub

Si vous souhaitez passer directement à l’exemple de code, consultez les [exemples de démarrage rapide C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/text-to-speech) sur GitHub.

## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous disposez d’un compte Azure et d’un abonnement au service Speech. Si vous n’avez pas de compte et d’abonnement, [essayez le service Speech gratuitement](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installer le Kit de développement logiciel (SDK) Speech

Avant de pouvoir faire quoi que ce soit, vous devez installer le SDK Speech. Suivez les instructions ci-dessous, en fonction de votre plateforme :

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## <a name="import-dependencies"></a>Importer les dépendances

Pour exécuter les exemples de cet article, incluez les instructions d’importation et `using` suivantes en haut de votre script.

```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

## <a name="create-a-speech-configuration"></a>Créer une configuration Speech

Pour appeler le service Speech à l’aide du SDK Speech, vous devez créer une classe [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig). Celle-ci comprend des informations sur votre abonnement, telles que votre clé Speech et l’emplacement/la région, le point de terminaison, l’hôte ou le jeton d’autorisation associés.

> [!NOTE]
> Quand vous procédez à une reconnaissance vocale, une synthèse vocale, une traduction ou une reconnaissance intentionnelle, vous devez toujours créer une configuration.

Vous pouvez initialiser une [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) de plusieurs façons :

* Avec un abonnement : transmettez une clé et l’emplacement/la région qui lui correspond.
* Avec un point de terminaison : transmettez un point de terminaison de service Speech. Une clé ou un jeton d’autorisation est facultatif.
* Avec un hôte : transmettez une adresse d’hôte. Une clé ou un jeton d’autorisation est facultatif.
* Avec un jeton d’autorisation : transmettez un jeton d’autorisation et la localisation/la région qui lui correspond.

Dans cet exemple, vous allez créer un objet [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) à partir d’une clé d’abonnement et d’une région. Pour obtenir ces informations d’identification, suivez les étapes indiquées dans [Essayer le service Speech gratuitement](../../../overview.md#try-the-speech-service-for-free). Vous pouvez aussi créer un code réutilisable de base à utiliser pour le reste de cet article, que vous modifiez pour différentes personnalisations.

```cpp
int wmain()
{
    try
    {
        synthesizeSpeech();
    }
    catch (exception e)
    {
        cout << e.what();
    }
    return 0;
}

void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
}
```

## <a name="select-synthesis-language-and-voice"></a>Sélectionner la langue et la voix de synthèse

Le service Synthèse vocale Azure prend en charge plus de 250 voix ainsi que plus de 70 langues et variantes.
Vous pouvez en obtenir la [liste complète](../../../language-support.md#neural-voices) ou les tester dans la [version de démonstration de la synthèse vocale](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features).
Spécifiez la langue ou la voix de [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) en fonction de votre texte d’entrée, puis utilisez la voix souhaitée.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    // Note: if only language is set, the default voice of that language is chosen.
    config->SetSpeechSynthesisLanguage("<your-synthesis-language>"); // e.g. "de-DE"
    // The voice setting will overwrite language setting.
    // The voice setting will not overwrite the voice element in input SSML.
    config->SetSpeechSynthesisVoiceName("<your-wanted-voice>");
}
```

## <a name="synthesize-speech-to-a-file"></a>Synthétiser la voix dans un fichier

Ensuite, vous allez créer un objet [`SpeechSynthesizer`](/cpp/cognitive-services/speech/speechsynthesizer), qui exécute des conversions de synthèse vocale et des sorties vers des haut-parleurs, des fichiers ou d’autres flux de sortie. [`SpeechSynthesizer`](/cpp/cognitive-services/speech/speechsynthesizer) accepte en tant que paramètres l’objet [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) créé à l’étape précédente et un objet [`AudioConfig`](/cpp/cognitive-services/speech/audio-audioconfig) qui spécifie la façon dont les résultats de sortie doivent être gérés.

Pour commencer, créez un `AudioConfig` pour écrire automatiquement la sortie dans un fichier `.wav` à l’aide de la fonction `FromWavFileOutput()`.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
}
```

Ensuite, instanciez un `SpeechSynthesizer` en transmettant votre objet `config` et l’objet `audioConfig` en tant que paramètres. Dès lors, exécuter la synthèse vocale et écrire dans un fichier sont aussi simples que d’exécuter `SpeakTextAsync()` avec une chaîne de texte.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, audioConfig);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
}
```

Exécutez le programme ; un fichier `.wav` synthétisé est alors écrit à l’emplacement que vous avez spécifié. Il s’agit d’un bon exemple de l’utilisation la plus simple, mais vous allez maintenant vous pencher sur la personnalisation de la sortie et sur la gestion de la réponse de sortie sous forme de flux en mémoire pour travailler sur des scénarios personnalisés.

## <a name="synthesize-to-speaker-output"></a>Synthétiser vers la sortie du haut-parleur

Dans certains cas, vous pouvez souhaiter sortir directement la synthèse vocale vers un haut-parleur. Pour cela, il suffit d’omettre le paramètre `AudioConfig` au moment de créer `SpeechSynthesizer` dans l’exemple ci-dessus. La sortie est alors synthétisée vers l’appareil de sortie actuellement actif.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    auto synthesizer = SpeechSynthesizer::FromConfig(config);
    auto result = synthesizer->SpeakTextAsync("Synthesizing directly to speaker output.").get();
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Obtenir le résultat sous forme de flux en mémoire

Dans de nombreux scénarios de développement d’application vocale, vous aurez probablement besoin des données audio obtenues sous forme de flux en mémoire plutôt que directement écrites dans un fichier. Cela vous permettra de générer un comportement personnalisé, notamment :

* Résumer le tableau d’octets obtenu sous forme de flux pouvant faire l’objet de recherches pour des services en aval personnalisés.
* Intégrer le résultat à d’autres API ou services.
* Modifier les données audio, écrire des en-têtes `.wav` personnalisés, etc.

Il est facile d’apporter cette modification à partir de l’exemple précédent. Tout d’abord, supprimez `AudioConfig`, car vous allez gérer le comportement de sortie manuellement à partir de ce point pour un contrôle accru. Transmettez ensuite `NULL` pour `AudioConfig` dans le constructeur `SpeechSynthesizer`.

> [!NOTE]
> Le fait de transmettre `NULL` pour `AudioConfig`, au lieu de l’omettre comme dans l’exemple de sortie de haut-parleur précédent, n’aura pas pour effet de lire par défaut l’audio sur l’appareil de sortie actuellement actif.

Cette fois, vous allez enregistrer le résultat dans une variable [`SpeechSynthesisResult`](/cpp/cognitive-services/speech/speechsynthesisresult). Le getter `GetAudioData` retourne `byte []` des données de sortie. Vous pouvez utiliser ce `byte []` manuellement ou utiliser la classe [`AudioDataStream`](/cpp/cognitive-services/speech/audiodatastream) pour gérer le flux en mémoire. Dans cet exemple, vous allez utiliser la fonction statique `AudioDataStream.FromResult()` pour obtenir un flux à partir du résultat.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);

    auto result = synthesizer->SpeakTextAsync("Getting the response as an in-memory stream.").get();
    auto stream = AudioDataStream::FromResult(result);
}
```

À partir de là, vous pouvez implémenter un comportement personnalisé à partir de l’objet `stream` obtenu.

## <a name="customize-audio-format"></a>Personnaliser le format audio

La section suivante montre comment personnaliser les attributs de sortie audio, notamment :

* Le type de fichier audio
* Le taux d’échantillonnage
* La profondeur de bits

Pour changer de format audio, utilisez la fonction `SetSpeechSynthesisOutputFormat()` sur l’objet `SpeechConfig`. Cette fonction attend un `enum` de type [`SpeechSynthesisOutputFormat`](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat), dont vous allez vous servir pour sélectionner le format de sortie. Consultez les documents de référence pour obtenir la [liste des formats audio](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat) disponibles.

Il existe diverses options pour les différents types de fichiers, ce qui permet de répondre à vos besoins. Notez que par définition, les formats bruts comme `Raw24Khz16BitMonoPcm` n’incluent pas d’en-têtes audio. N’utilisez les formats bruts que si vous savez que votre implémentation en aval pourra décoder un flux binaire brut ou bien si vous envisagez de créer manuellement des en-têtes en fonction de la profondeur de bits, du taux d’échantillonnage, du nombre de canaux, etc.

Dans cet exemple, vous allez spécifier un format RIFF haute fidélité `Riff24Khz16BitMonoPcm` en définissant `SpeechSynthesisOutputFormat` sur l’objet `SpeechConfig`. Comme dans l’exemple de la section précédente, vous allez utiliser [`AudioDataStream`](/cpp/cognitive-services/speech/audiodatastream) pour obtenir un flux en mémoire du résultat, puis l’écrire dans un fichier.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    config->SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat::Riff24Khz16BitMonoPcm);

    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();

    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
}
```

Si vous exécutez à nouveau votre programme, un fichier `.wav` est écrit dans le chemin spécifié.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Utiliser SSML pour personnaliser les caractéristiques vocales

Le langage SSML (Speech Synthesis Markup Language) vous permet d’ajuster la tonalité, la prononciation, la vitesse d’élocution, le volume et d’autres éléments de sortie de la synthèse vocale en soumettant vos demandes à partir d’un schéma XML. Cette section présente un exemple de modification de la voix, mais pour obtenir pour des indications plus détaillées, consultez l’[article de guide pratique sur SSML](../../../speech-synthesis-markup.md).

Pour commencer à utiliser SSML pour la personnalisation, vous allez apporter une modification simple qui change la voix.
Tout d’abord, créez un fichier XML pour la configuration SSML dans le répertoire racine de votre projet, à savoir `ssml.xml`. L’élément racine est toujours `<speak>`, et le fait d’intégrer le texte dans un élément `<voice>` vous permet de changer la voix à l’aide du paramètre `name`. Consultez la [liste complète](../../../language-support.md#neural-voices) des voix **neuronales** prises en charge.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-ChristopherNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

Ensuite, vous devez changer la demande de synthèse vocale de sorte qu’elle fasse référence à votre fichier XML. La demande est presque totalement identique, sauf qu’au lieu d’utiliser la fonction `SpeakTextAsync()`, vous allez utiliser `SpeakSsmlAsync()`. Sachant que cette fonction attend une chaîne XML, vous commencez par charger votre configuration SSML sous forme de chaîne. À partir de là, l’objet obtenu est exactement le même que dans les exemples précédents.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);

    std::ifstream file("./ssml.xml");
    std::string ssml, line;
    while (std::getline(file, line))
    {
        ssml += line;
        ssml.push_back('\n');
    }
    auto result = synthesizer->SpeakSsmlAsync(ssml).get();

    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
}
```

> [!NOTE]
> Pour modifier la voix sans utiliser SSML, vous pouvez définir la propriété sur `SpeechConfig` à l’aide de `SpeechConfig.SetSpeechSynthesisVoiceName("en-US-ChristopherNeural")`

## <a name="get-facial-pose-events"></a>Obtenir des événements de pose faciale

La reconnaissance vocale peut être un bon moyen de piloter l’animation des expressions faciales.
Souvent, les [visèmes](../../../how-to-speech-synthesis-viseme.md) sont utilisées pour représenter les principaux éléments de parole observés, tels que la position des lèvres, de la mâchoire et de la langue lors de la production d’un phonème particulier.
Vous pouvez vous abonner à l’événement de visème dans le kit de développement logiciel (SDK) Speech.
Ensuite, vous pouvez appliquer des événements de visème pour animer le visage d’un personnage au fur et à mesure de la lecture audio.
Découvrez [comment obtenir des événements de visème](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
