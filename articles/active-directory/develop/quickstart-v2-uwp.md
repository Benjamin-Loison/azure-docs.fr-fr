---
title: 'Démarrage rapide : Connecter des utilisateurs et appeler Microsoft Graph dans une application UWP | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, découvrez comment une application de plateforme Windows universelle (UWP) peut obtenir un jeton d’accès et appeler une API protégée par la plateforme d’identités Microsoft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/07/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 72f19c7f722900f46df07265407b28583412196d
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235095"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Démarrage rapide : Appeler l’API Microsoft Graph à partir d’une application de plateforme Windows universelle (UWP)

Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application de plateforme Windows universelle (UWP) peut connecter des utilisateurs et obtenir un jeton d’accès pour appeler l’API Microsoft Graph. 

Consultez [Fonctionnement de l’exemple](#how-the-sample-works) pour obtenir une illustration.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Prérequis
>
> * Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Inscrire et télécharger votre application de démarrage rapide
> [!div renderon="docs" class="sxs-lookup"]
> Vous disposez de deux options pour démarrer votre application de démarrage rapide :
> * [Express] [Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuel] [Option 2 : Inscrire et configurer manuellement vos application et exemple de code](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code
>
> 1. Accédez à l’expérience de démarrage rapide <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs" target="_blank">Portail Azure - Inscriptions d’applications</a>.
> 1. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
> 1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application pour vous en un seul clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option n°2 : Inscrire et configurer manuellement vos application et exemple de code
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
> Pour inscrire votre application et ajouter les informations d’inscription de l’application à votre solution, procédez comme suit :
> 1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</a>.
> 1. Si vous avez accès à plusieurs locataires, utilisez le filtre **Répertoires + abonnements** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: dans le menu du haut pour basculer vers le locataire dans lequel vous voulez inscrire l’application.
> 1. Recherchez et sélectionnez **Azure Active Directory**.
> 1. Sous **Gérer**, sélectionnez **Inscriptions d’applications** > **Nouvelle inscription**.
> 1. Entrez un **nom** pour votre application (par exemple, `UWP-App-calling-MsGraph`). Les utilisateurs de votre application peuvent voir ce nom, et vous pouvez le changer ultérieurement.
> 1. Dans la section **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft (par exemple, Skype, Xbox, Outlook.com)** .
> 1. Sélectionnez **Inscrire** pour créer l’application, puis consignez l’**ID d’application (client)** en vue de son utilisation dans une étape ultérieure.
> 1. Sous **Gérer**, sélectionnez **Authentification**.
> 1. Sélectionnez **Ajouter une plateforme** > **Applications de bureau et mobiles**.
> 1. Sous **URI de redirection**, sélectionnez `https://login.microsoftonline.com/common/oauth2/nativeclient`.
> 1. Sélectionnez **Configurer**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-the-application"></a>Étape 1 : Configurer l’application
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, ajoutez l’**URI de redirection** `https://login.microsoftonline.com/common/oauth2/nativeclient`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter cette modification pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-uwp/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-the-visual-studio-project"></a>Étape 2 : Télécharger le projet Visual Studio

> [!div renderon="docs"]
> [Télécharger le projet Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Exécutez le projet à l’aide de Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Étape 3 : Votre application est configurée et prête à être exécutée
> Nous avons configuré votre projet avec les valeurs des propriétés de votre application et il est prêt à être exécuté.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-visual-studio-project"></a>Étape 3 : Configurer le projet Visual Studio
>
> 1. Extrayez l’archive .zip dans un dossier local proche de la racine de votre lecteur. Par exemple, dans **C:\Azure-Samples**.
> 1. Ouvrez le projet dans Visual Studio. Si vous y êtes invité, installez la charge de travail **Développement pour la plateforme Windows universelle** et éventuellement des composants individuels du kit SDK.
> 1. Dans *MainPage.Xaml.cs*, remplacez la valeur de la variable `ClientId` par l’**ID d’application (client)**  de l’application que vous avez inscrite précédemment.
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
>
>    Vous pouvez trouver l’**ID d’application(client)** dans le volet **Vue d’ensemble** de l’application sur le portail Azure (**Azure Active Directory** > **Inscriptions des applications** >  *{Inscription de votre application}* ).
> 1. Créez un certificat auto-signé de test pour le package et sélectionnez-le :
>     1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier *Package.appxmanifest*.
>     1. Sélectionnez **Empaquetage** > **Choisir un certificat...**  > **Créer...** .
>     1. Entrez un mot de passe, puis sélectionnez **OK**. Un certificat nommé *Native_UWP_V2_TemporaryKey.pfx* est créé. 
>     1. Sélectionnez **OK** pour fermer la boîte de dialogue **Choisir un certificat**, puis vérifiez que *Native_UWP_V2_TemporaryKey.pfx* s’affiche dans l’Explorateur de solutions.
>     1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Native_UWP_V2**, puis sélectionnez **Propriétés**.
>     1. Choisissez **Signature**, puis sélectionnez le fichier .pfx que vous avez créé dans la liste déroulante **Choisir un fichier de clé de nom fort**.

#### <a name="step-4-run-the-application"></a>Étape 4 : Exécuter l’application

Pour exécuter l’exemple d’application sur votre ordinateur local :

1. Dans la barre d’outils Visual Studio, choisissez la bonne plateforme (probablement **x64** ou **x86**, mais pas ARM). L’appareil cible doit passer de *Appareil* à *Machine locale*.
1. Sélectionnez **Déboguer** > **Démarrer sans débogage**
    
    Si vous êtes invité à le faire, vous devrez peut-être d’abord activer **Mode développeur**, puis à nouveau **Démarrer sans débogage** pour lancer l’application.

Quand la fenêtre de l’application s’affiche, vous pouvez sélectionner le bouton **Appeler l’API Microsoft Graph**, entrer vos informations d’identification, puis donner votre consentement aux autorisations demandées par l’application. En cas de réussite, l’application affiche des informations de jeton ainsi que les données obtenues de l’appel à l’API Microsoft Graph.

## <a name="how-the-sample-works"></a>Fonctionnement de l’exemple

![Fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons de sécurité. Les jetons de sécurité sont utilisés pour accéder à une API protégée par la plateforme d’identités Microsoft. Vous pouvez installer MSAL en exécutant la commande suivante dans la *console du gestionnaire de package* de Visual Studio :

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Initialisation MSAL

Vous pouvez ajouter la référence de MSAL en ajoutant le code suivant :

```csharp
using Microsoft.Identity.Client;
```

MSAL est ensuite initialisé à l’aide du code suivant :

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

La valeur de `ClientId` est l’**ID d’application (client)** de l’application que vous avez inscrite sur le portail Azure. Vous pouvez retrouver cette valeur dans la page **Vue d’ensemble** de l’application dans le portail Azure.

### <a name="requesting-tokens"></a>Demande de jetons

MSAL utilise deux méthodes d’acquisition de jetons dans une application UWP : `AcquireTokenInteractive` et `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Obtenir un jeton d’utilisateur de manière interactive

Certaines situations obligent les utilisateurs à interagir avec la plateforme d’identités Microsoft via une fenêtre contextuelle pour qu’ils valident leurs informations d’identification ou qu’ils donnent leur consentement. Voici quelques exemples :

- Lorsque des utilisateurs se connectent pour la première fois à l’application
- Quand les utilisateurs doivent de nouveau entrer leurs informations d’identification, car le mot de passe a expiré
- Lorsque votre application demande l’accès à une ressource pour laquelle l’utilisateur doit donner son consentement
- Lorsqu’une authentification à 2 facteurs est requise

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

Le paramètre `scopes` contient les étendues demandées, comme `{ "user.read" }` pour Microsoft Graph ou `{ "api://<Application ID>/access_as_user" }` pour les API web personnalisées.

#### <a name="get-a-user-token-silently"></a>Obtenir un jeton d’utilisateur en mode silencieux

Utilisez la méthode `AcquireTokenSilent` pour obtenir des jetons d’accès aux ressources protégées après la méthode `AcquireTokenInteractive` initiale. Vous ne voulez pas obliger l’utilisateur à valider ses informations d’identification chaque fois qu’il doit accéder à une ressource. La plupart du temps, vous voulez que les acquisitions et renouvellements de jetons se fassent sans aucune interaction de l’utilisateur.

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

* `scopes` contient les étendues demandées, comme `{ "user.read" }` pour Microsoft Graph ou `{ "api://<Application ID>/access_as_user" }` pour les API web personnalisées.
* `firstAccount` spécifie le compte du premier utilisateur dans le cache (MSAL prend en charge plusieurs utilisateurs dans une même application).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Essayez le didacticiel de bureau Windows pour apprendre à créer, étape par étape, des applications et des fonctionnalités, y compris une explication complète de ce démarrage rapide.

> [!div class="nextstepaction"]
> [Didacticiel UWP : Appeler l’API Graph](tutorial-v2-windows-uwp.md)
