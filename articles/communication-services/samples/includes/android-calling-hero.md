---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: ddematheu2
manager: chpalm
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: 0c93d09618eca9b5f43b0b52c80607875b5c73cb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288111"
---
L'**exemple de bannière d'appel de groupe pour Android** d'Azure Communication Services illustre de quelle façon le SDK Android Communication Services Calling peut être utilisé pour créer une expérience d'appels vocaux et vidéo. Dans cet exemple de démarrage rapide, vous allez apprendre à configurer et à exécuter l’exemple. Une vue d’ensemble de l’exemple est fournie ci-après pour le contexte.

## <a name="download-code"></a>Télécharger le code

Recherchez le projet correspondant à cet exemple sur [GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero). Une version [Interopérabilité avec Teams](../../concepts/teams-interop.md) de l'exemple est disponible sur une [branche](https://github.com/Azure-Samples/communication-services-android-calling-hero/tree/feature/teams_interop) distincte.

## <a name="overview"></a>Vue d’ensemble

L'exemple est une application Android native qui utilise les SDK Android d'Azure Communication Services pour créer une expérience d'appels vocaux et vidéo. L'application se sert d'un composant côté serveur afin d'approvisionner des jetons d'accès qui sont ensuite utilisés pour initialiser le SDK d'Azure Communication Services. Pour configurer ce composant côté serveur, n’hésitez pas à suivre le tutoriel pour [créer un service d’authentification approuvé avec Azure Functions](../../tutorials/trusted-service-tutorial.md).

Voici à quoi ressemble l’exemple :

:::image type="content" source="../media/calling/landing-page-android.png" alt-text="Capture d’écran montrant la page d’arrivée de l’exemple d’application.":::

Lorsque vous appuyez sur le bouton « Lancer un nouvel appel », l'application Android crée un appel et le rejoint. L’application vous permet également de rejoindre un appel Azure Communication Services en cours en spécifiant l’ID de cet appel.

Après avoir rejoint un appel, vous êtes invité à autoriser l’application à accéder à votre caméra et votre microphone. Vous êtes également invité à fournir un nom d’affichage.

:::image type="content" source="../media/calling/pre-call-android.png" alt-text="Capture d’écran montrant l’écran de pré-appel de l’exemple d’application.":::

Une fois que vous avez configuré votre nom d’affichage et vos appareils, vous pouvez rejoindre l’appel. Vous voyez alors le canevas d’appel principal où réside l’expérience d’appel de base.

:::image type="content" source="../media/calling/main-app-android.png" alt-text="Capture d’écran montrant l’écran principal de l’exemple d’application.":::

Composants de l’écran principal d’appel :

- **Galerie multimédia** : scène principale montrant les participants. Si une caméra est activée pour un participant, son flux vidéo est affiché ici. Chaque participant a une vignette individuelle qui comporte son nom d’affichage et son flux vidéo (le cas échéant). La galerie prend en charge plusieurs participants et est mise à jour au fur et à mesure de l’ajout ou de la suppression de participants à l’appel.
- **Barre d’action** : elle contient les principales commandes d’appel. Ces commandes vous permettent d’activer et de désactiver votre caméra et votre microphone, de partager votre écran et de quitter l’appel.

Vous trouverez ci-dessous des informations supplémentaires sur les prérequis et les étapes à suivre pour configurer l’exemple.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Une instance d'[Android Studio](https://developer.android.com/studio) exécutée sur votre ordinateur
- Une ressource Azure Communication Services. Pour plus d’informations, consultez [Créer des ressources Azure Communication Services](../../quickstarts/create-communication-resource.md).
- Une instance d'Azure Function exécutant le [point de terminaison d'authentification](../../tutorials/trusted-service-tutorial.md) pour récupérer les jetons d'accès.

## <a name="running-sample-locally"></a>Exécution de l’exemple localement

L'exemple d'appel de groupe peut être exécuté localement en utilisant Android Studio. Les développeurs ont le choix d’utiliser leur appareil physique ou un émulateur pour tester l’application.

### <a name="before-running-the-sample-for-the-first-time"></a>Avant d’exécuter l’exemple pour la première fois

1. Ouvrez Android Studio et sélectionnez `Open an Existing Project`.
2. Ouvrez le dossier `AzureCalling` dans la version téléchargée de l'exemple.
3. Développez Applications/ressources pour mettre à jour `appSettings.properties`. Définissez la valeur de la clé `communicationTokenFetchUrl` sur l'URL du point de terminaison d'authentification configuré comme condition préalable.

### <a name="run-sample"></a>Exécuter l’exemple

Générez et exécutez l'exemple dans Android Studio.

## <a name="optional-securing-an-authentication-endpoint"></a>(Facultatif) Sécurisation d’un point de terminaison d’authentification

À des fins de démonstration, cet exemple utilise par défaut un point de terminaison accessible publiquement pour extraire un jeton Azure Communication Services. Dans les scénarios de production, nous vous recommandons d’utiliser votre propre point de terminaison sécurisé pour provisionner vos propres jetons.

Avec une configuration supplémentaire, cet exemple prend en charge la connexion à un point de terminaison **Azure Active Directory** (Azure AD) protégé, qui requiert une connexion de l’utilisateur pour que l’application puisse récupérer un jeton Azure Communication Services. Consultez les étapes ci-dessous :

1. Activez l’authentification Azure Active Directory dans votre application.  
   - [Inscrire votre application sous Azure Active Directory (en utilisant les paramètres de plateforme Android)](../../../active-directory/develop/tutorial-v2-android.md) 
    - [Configurer votre application App Service ou Azure Functions pour utiliser une connexion Azure AD](../../../app-service/configure-authentication-provider-aad.md)

2. Accédez à la page de vue d’ensemble de l’application inscrite sous Azure Active Directory > Inscriptions des applications. Notez les valeurs de `Package name`, `Signature hash`, `MSAL Configutaion`

:::image type="content" source="../media/calling/aad-overview-android.png" alt-text="Configuration d’Azure Active Directory dans le portail Azure.":::

3. Modifier `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` et définir `isAADAuthEnabled` pour activer Azure Active Directory
4. Modifiez `AndroidManifest.xml` et définissez `android:path` sur le hachage de signature du magasin de clés. (Facultatif, La valeur actuelle utilise le hachage du magasin de clés debug.keystore fourni. Si un autre magasin de clés est utilisé, il doit être mis à jour.)
   ```
   <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="com.azure.samples.communication.calling"
                    android:path="/Signature hash" <!-- do not remove /. The current hash in AndroidManifest.xml is for debug.keystore. -->
                    android:scheme="msauth" />
            </intent-filter>
        </activity>
   ```
5. Copiez la configuration de MSAL pour Android à partir du portail Azure et collez-la dans le fichier `AzureCalling/app/src/main/res/raw/auth_config_single_account.json`. Incluez "account_mode" : "SINGLE"
   ```
      {
         "client_id": "",
         "authorization_user_agent": "DEFAULT",
         "redirect_uri": "",
         "account_mode" : "SINGLE",
         "authorities": [
            {
               "type": "AAD",
               "audience": {
               "type": "AzureADMyOrg",
               "tenant_id": ""
               }
            }
         ]
      }
   ```

6. Modifiez `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` et définissez la valeur de la clé `communicationTokenFetchUrl` comme l'URL de votre point de terminaison d'authentification sécurisé.
7. Modifiez `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` et définissez la valeur de la clé `aadScopes` à partir des étendues `Azure Active Directory` `Expose an API`.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer un abonnement Communication Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées. Apprenez-en davantage sur le [nettoyage des ressources](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Étapes suivantes

>[!div class="nextstepaction"]
>[Télécharger l’exemple à partir de GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero)

Pour plus d’informations, consultez les articles suivants :

- Familiarisez-vous avec l’[utilisation du SDK Calling](../../quickstarts/voice-video-calling/getting-started-with-calling.md)
- Apprenez-en davantage sur le [fonctionnement des appels](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Documentation supplémentaire

- [Azure Communication GitHub](https://github.com/Azure/communication) : Autres exemples et informations dans la page GitHub officielle
- [Exemples](./../overview.md) : Recherchez d’autres exemples sur notre page Vue d’ensemble des exemples.
- [Fonctionnalités Azure Communication Calling](../../concepts/voice-video-calling/calling-sdk-features.md) : découvrez-en plus sur le SDK Android Calling - [Azure Communication - SDK Android Calling](https://search.maven.org/artifact/com.azure.android/azure-communication-calling)