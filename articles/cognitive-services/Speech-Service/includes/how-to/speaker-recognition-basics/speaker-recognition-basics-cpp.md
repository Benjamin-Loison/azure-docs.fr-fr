---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 68e088b088c461b29311a1289e1867c4df9e5b1a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069127"
---
Dans ce guide de démarrage rapide, vous allez découvrir les modèles de conception de base pour la reconnaissance de l’orateur à l’aide du SDK de reconnaissance vocale, notamment :

* Vérification dépendante ou indépendante du texte
* Identification de l’orateur pour identifier un échantillon vocal parmi un groupe de voix
* Suppression de profils vocaux

Pour obtenir une vue d’ensemble des concepts de reconnaissance vocale, consultez l’article de [présentation](../../../speaker-recognition-overview.md). Voir le nœud de référence dans le menu de gauche pour une liste des plateformes prises en charge.

## <a name="skip-to-samples-on-github"></a>Passer aux exemples sur GitHub

Si vous souhaitez passer directement à l’exemple de code, consultez les [exemples de démarrage rapide C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) sur GitHub.

## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous disposez d’un compte Azure et d’un abonnement au service Speech. Si vous n’avez pas de compte et d’abonnement, [essayez le service Speech gratuitement](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Microsoft limite l’accès à la Reconnaissance de l’orateur. Demandez à l'utiliser par le biais de l'examen d'accès limité d'[Azure Cognitive Services Speaker Recognition](https://aka.ms/azure-speaker-recognition). Après approbation, vous pouvez accéder aux API de reconnaissance des locuteurs. 

## <a name="install-the-speech-sdk"></a>Installer le Kit de développement logiciel (SDK) Speech

Avant de pouvoir faire quoi que ce soit, vous devez installer le SDK Speech. Suivez les instructions ci-dessous, en fonction de votre plateforme :

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## <a name="import-dependencies"></a>Importer les dépendances

Pour exécuter les exemples de cet article, ajoutez les instructions suivantes en haut de votre fichier .cpp.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="dependencies":::

## <a name="create-a-speech-configuration"></a>Créer une configuration Speech

Pour appeler le service Speech à l’aide du SDK Speech, vous devez créer une classe [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig). Celle-ci comprend des informations sur votre abonnement, telles que votre clé et la région, le point de terminaison, l’hôte ou le jeton d’autorisation associés.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="get_speech_config":::

## <a name="text-dependent-verification"></a>Vérification dépendante du texte

La vérification de l’orateur est l’acte qui consiste à confirmer qu’un orateur correspond à une voix **inscrite** (connue). La première étape consiste à **inscrire** un profil vocal qui permet ensuite au service de comparer les futurs exemples de voix. Dans cet exemple, vous inscrivez le profil à l’aide d’une stratégie **dépendante du texte**, qui nécessite une phrase secrète spécifique à utiliser à la fois pour l’inscription et la vérification. Pour obtenir la liste des phrases secrètes prises en charge, consultez la [documentation de référence](/rest/api/speakerrecognition/).

### <a name="textdependentverification-function"></a>Fonction TextDependentVerification

Commencez par créer la fonction `TextDependentVerification`.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_dependent_verification":::

Cette fonction crée un objet [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) à l’aide de la méthode [CreateProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#createprofileasync). Notez qu’il existe trois [types](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#enum-voiceprofiletype) d’objets `VoiceProfile` :

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

En l’occurrence, vous transmettez `VoiceProfileType::TextDependentVerification` à `CreateProfileAsync`.

Vous appelez ensuite deux fonctions d’assistance que vous allez définir ci-après, `AddEnrollmentsToTextDependentProfile` et `SpeakerVerify`. Enfin, appelez [DeleteProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#deleteprofileasync) pour nettoyer le profil.

### <a name="addenrollmentstotextdependentprofile-function"></a>Fonction AddEnrollmentsToTextDependentProfile

Définissez la fonction suivante pour inscrire un profil vocal.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_dependent":::

Dans cette fonction, inscrivez des échantillons audio dans une boucle `while` qui effectue le suivi du nombre d’échantillons restants, et requis, pour l’inscription. Dans chaque itération, [EnrollProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#enrollprofileasync) vous invite à prononcer la phrase secrète dans votre microphone, puis ajoute l’échantillon au profil vocal.

### <a name="speakerverify-function"></a>Fonction SpeakerVerify

Définissez `SpeakerVerify` comme suit.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_verify":::

Dans cette fonction, vous créez un objet [SpeakerVerificationModel](/cpp/cognitive-services/speech/speaker-speakerverificationmodel) avec la méthode [SpeakerVerificationModel::FromProfile](/cpp/cognitive-services/speech/speaker-speakerverificationmodel#fromprofile), en passant l’objet [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) que vous avez créé.

Ensuite, [SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) vous invite à prononcer à nouveau la phrase secrète, cette fois-ci en la validant par rapport à votre profil vocal, puis retourne un score de similarité compris entre 0,0 et 1,0. L’objet [SpeakerRecognitionResult](/cpp/cognitive-services/speech/speaker-speakerrecognitionresult) retourne également `Accept` ou `Reject`, selon que la phrase secrète correspond ou non.

## <a name="text-independent-verification"></a>Vérification indépendante du texte

Contrairement à la vérification **dépendante du texte**, la vérification **indépendante du texte** ne nécessite pas trois échantillons audio, mais elle *nécessite* 20 secondes de l’audio total.

### <a name="textindependentverification-function"></a>Fonction TextIndependentVerification

Commencez par créer la fonction `TextIndependentVerification`.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_verification":::

À l’instar de la fonction `TextDependentVerification`, cette fonction crée un objet [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) avec la méthode [CreateProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#createprofileasync).

En l’occurrence, vous transmettez `VoiceProfileType::TextIndependentVerification` à `CreateProfileAsync`.

Vous appelez ensuite deux fonctions d’assistance : `AddEnrollmentsToTextIndependentProfile`, que vous allez définir ci-après et `SpeakerVerify`, que vous avez déjà définie. Enfin, appelez [DeleteProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#deleteprofileasync) pour nettoyer le profil.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Définissez la fonction suivante pour inscrire un profil vocal.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_independent":::

Dans cette fonction, inscrivez des échantillons audio dans une boucle `while` qui effectue le suivi du nombre de secondes d’audio restantes, et requises, pour l’inscription. Dans chaque itération, [EnrollProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#enrollprofileasync) vous invite à parler dans votre microphone, puis ajoute l’échantillon au profil vocal.

## <a name="speaker-identification"></a>Identification de l’orateur

L’identification de l’orateur est utilisée pour déterminer **qui** parle dans un groupe donné de voix inscrites. Le processus est très similaire à la **vérification indépendante du texte**. La principale différence est la possibilité de vérifier plusieurs profils vocaux à la fois, au lieu d’un seul profil.

### <a name="textindependentidentification-function"></a>Fonction TextIndependentIdentification

Commencez par créer la fonction `TextIndependentIdentification`.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_indentification":::

À l’instar de la fonction `TextDependentVerification` et `TextIndependentVerification`, cette fonction crée un objet [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) avec la méthode [CreateProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#createprofileasync).

En l’occurrence, vous transmettez `VoiceProfileType::TextIndependentIdentification` à `CreateProfileAsync`.

Vous appelez ensuite deux fonctions d’assistance : `AddEnrollmentsToTextIndependentProfile`, que vous avez déjà définie, et `SpeakerIdentify`, que vous allez définir ci-après. Enfin, appelez [DeleteProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#deleteprofileasync) pour nettoyer le profil.

### <a name="speakeridentify-function"></a>Fonction SpeakerIdentify

Définissez la fonction `SpeakerIdentify` comme suit.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_identify":::

Dans cette fonction, vous créez un objet [SpeakerIdentificationModel](/cpp/cognitive-services/speech/speaker-speakeridentificationmodel) avec la méthode [SpeakerIdentificationModel::FromProfiles](/cpp/cognitive-services/speech/speaker-speakeridentificationmodel#fromprofiles). `SpeakerIdentificationModel::FromProfiles` accepte une liste d’objets [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile). En l’occurrence, vous transmettez simplement l’objet `VoiceProfile` que vous avez créé. Toutefois, si vous le souhaitez, vous pouvez transmettre plusieurs objets `VoiceProfile`, chacun étant inscrit avec des échantillons audio à partir d’une voix différente.

Ensuite, [SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) vous réinvite à parler. Cette fois-ci, elle compare votre voix aux profils vocaux inscrits et retourne le profil vocal le plus similaire.

## <a name="main-function"></a>Fonction main

Enfin, définissez la fonction `main` comme suit.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="main":::

Cette fonction appelle simplement les fonctions que vous avez définies. Toutefois, elle crée d’abord un objet [VoiceProfileClient](/cpp/cognitive-services/speech/speaker-voiceprofileclient) et un objet [SpeakerRecognizer](/cpp/cognitive-services/speech/speaker-speakerrecognizer).

```
auto speech_config = GetSpeechConfig();
auto client = VoiceProfileClient::FromConfig(speech_config);
auto recognizer = SpeakerRecognizer::FromConfig(speech_config, audio_config);
```

L’objet `VoiceProfileClient` permet de créer, d’inscrire et de supprimer des profils vocaux. L’objet `SpeakerRecognizer` est utilisé pour valider des échantillons vocaux sur un ou plusieurs profils vocaux inscrits.

## <a name="changing-audio-input-type"></a>Modification du type d’entrée audio

Les exemples de cet article utilisent le microphone d’appareil par défaut comme entrée pour les exemples audio. Toutefois, dans les scénarios où vous devez utiliser des fichiers audio à la place d’une entrée de microphone, changez simplement la ligne suivante :

```
auto audio_config = Audio::AudioConfig::FromDefaultMicrophoneInput();
```

à :

```
auto audio_config = Audio::AudioConfig::FromWavFileInput(path/to/your/file.wav);
```

Ou remplacez toute utilisation de `audio_config` par [Audio::AudioConfig::FromWavFileInput](/cpp/cognitive-services/speech/audio-audioconfig#fromwavfileinput). Vous pouvez également avoir des entrées mixtes, à l’aide d’un microphone pour l’inscription et de fichiers pour la vérification par exemple.
