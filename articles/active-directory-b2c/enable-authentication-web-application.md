---
title: Activer l’authentification dans une application web à l’aide des blocs de construction Azure Active Directory B2C
description: Cet article explique comment utiliser les blocs de construction Azure Active Directory B2C pour inscrire et connecter des utilisateurs dans une application web ASP.NET.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/11/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 66b37e5464d548559b8d4e08922f1970e0c8bb7f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130040813"
---
# <a name="enable-authentication-in-your-own-web-app-by-using-azure-ad-b2c"></a>Activation de l’authentification dans votre application web avec Azure AD B2C

Cet article explique comment ajouter l’authentification Azure Active Directory B2C (Azure AD B2C) à votre propre application Web ASP.NET. Découvrez comment créer une application Web ASP.NET Core avec l’intergiciel ASP.NET Core qui utilise le protocole [OpenID Connect](openid-connect.md). 

Utilisez cet article conjointement à [Configuration de l’authentification dans un exemple d’application web](configure-authentication-sample-web-app.md), en remplaçant l’exemple d’application Web par votre propre application web.

## <a name="prerequisites"></a>Prérequis

Pour passer en revue les conditions préalables et les instructions d’intégration, consultez [Configurer l’authentification dans un exemple d’application web](configure-authentication-sample-web-app.md).

## <a name="step-1-create-a-web-app-project"></a>Étape 1 : Créer un projet d’application web

Vous pouvez utiliser un projet d’application web modèle vue-contrôleur (MVC) ASP.NET existant ou en créer un. Pour créer un nouveau projet, ouvrez une interpréteur de commandes, puis entrez la commande suivante :

```dotnetcli
dotnet new mvc -o mywebapp
```

La commande précédente effectue les opérations suivantes :

* Elle crée une application web MVC.  
* Le paramètre `-o mywebapp` crée un répertoire nommé *myebapp* avec les fichiers sources de l’application.

## <a name="step-2-add-the-authentication-libraries"></a>Étape 2 : Ajouter les bibliothèques d’authentification

Ajoutez la bibliothèque web Microsoft Identity, qui est un ensemble de bibliothèques ASP.NET Core qui simplifient l’ajout de la prise en charge de l’authentification et de l’autorisation Azure AD B2C à votre application web. La bibliothèque Microsoft Identity Web définit le pipeline d’authentification avec une authentification basée sur les cookies. Il s’occupe de l’envoi et de la réception des messages d’authentification HTTP, de la validation des jetons, de l’extraction des revendications et bien plus encore.

Pour ajouter la bibliothèque Microsoft Identity Web, installez les packages en exécutant les commandes suivantes : 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```dotnetcli
dotnet add package Microsoft.Identity.Web
dotnet add package Microsoft.Identity.Web.UI
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
Install-Package Microsoft.Identity.Web
Install-Package Microsoft.Identity.Web.UI 
```

---


## <a name="step-3-initiate-the-authentication-libraries"></a>Étape 3 : Lancer les bibliothèques d’authentification

L’intergiciel Microsoft Identity Web utilise une classe de démarrage qui s’exécute lors du démarrage du processus d’hébergement. Au cours de cette étape, vous allez ajouter le code nécessaire pour initier les bibliothèques d’authentification.

Ouvrez *Startup.cs*, puis ajoutez les déclarations `using` suivantes au début de la classe :

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.Identity.Web;
using Microsoft.Identity.Web.UI;
```

Étant donné que Microsoft Identity Web utilise l’authentification basée sur les cookies pour protéger votre application Web, le code suivant définit les paramètres de cookie *SameSite*. Il lit ensuite les paramètres d’application `AzureAdB2C` et lance le contrôleur d’intergiciel avec son affichage. 

Remplacez la fonction `ConfigureServices(IServiceCollection services)` par l’extrait de code suivant : 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        // This lambda determines whether user consent for non-essential cookies is needed for a given request.
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.Unspecified;
        // Handling SameSite cookie according to https://docs.microsoft.com/en-us/aspnet/core/security/samesite?view=aspnetcore-3.1
        options.HandleSameSiteCookieCompatibility();
    });

    // Configuration to sign-in users with Azure AD B2C
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

    services.AddControllersWithViews()
        .AddMicrosoftIdentityUI();

    services.AddRazorPages();

    //Configuring appsettings section AzureAdB2C, into IOptions
    services.AddOptions();
    services.Configure<OpenIdConnectOptions>(Configuration.GetSection("AzureAdB2C"));
}
```

Le code suivant ajoute la stratégie de cookie et utilise le modèle d’authentification. Remplacez la fonction `Configure` par l’extrait de code suivant : 

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();

    // Add the Microsoft Identity Web cookie policy
    app.UseCookiePolicy();
    app.UseRouting();
    // Add the ASP.NET Core authentication service
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
        
        // Add endpoints for Razor pages
        endpoints.MapRazorPages();
    });
};
```

## <a name="step-4-add-the-ui-elements"></a>Étape 4 : Ajouter des éléments d’interface utilisateur

Pour ajouter des éléments d’interface utilisateur, utilisez un affichage partiel qui contient la logique permettant de vérifier si un utilisateur est connecté ou non. Si l’utilisateur n’est pas connecté, la vue partielle affiche le bouton de connexion. Si l’utilisateur est connecté, cela affiche son nom d’affichage et le bouton de déconnexion.
  
Créez un nouveau fichier, *\_ LoginPartial. cshtml*, à l’intérieur du dossier */Views/Shared* avec l’extrait de code suivant :

```razor
@using System.Security.Principal
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.Identity.Name</li>
        <!-- The Account controller is not defined in this project. Instead, it is part of Microsoft.Identity.Web.UI nuget package and
            it defines some well known actions such as SignUp/In, SignOut and EditProfile-->
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="EditProfile">
                <button type="submit" class="btn btn-primary" style="margin-right:5px">Edit Profile</button>
            </form>
        </li>
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">
                <button type="submit" class="btn btn-primary">Sign Out</button>
            </form>
        </li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">
                <button type="submit" class="btn btn-primary">Sign Up/In</button>
            </form>
        </li>
    </ul>
}
```

Modifiez votre fichier */Views/Shared_Layout.cshtml* pour inclure le fichier *_LoginPartial.cshtml* que vous avez ajouté. Le fichier *_Layout.cshtml* est une disposition commune qui fournit à l’utilisateur une expérience cohérente lorsqu’il navigue d’une page à l’autre. La disposition inclut des éléments d’IU courants comme l’en-tête et le pied de page de l’application.

> [!NOTE]
> Selon la version de .NET Core que vous exécutez et selon si vous ajoutez la connexion à une application existante, les éléments de l’interface utilisateur peuvent paraître différents. Dans ce cas, veillez à inclure *_LoginPartial* à l’emplacement approprié dans la mise en page.

Ouvrez le fichier */Views/Shared/_Layout.cshtml*, puis ajoutez l’élément `div` suivant.

```razor
<div class="navbar-collapse collapse">
...
</div>
```

Remplacez cet élément par le code Razor suivant :

```razor
<div class="navbar-collapse collapse">
  <ul class="nav navbar-nav">
    <li><a asp-area="" asp-controller="Home" asp-action="Index">Home</a></li>
    <li><a asp-area="" asp-controller="Home" asp-action="Claims">Claims</a></li>
  </ul>
  <partial name="_LoginPartial" />
</div>
```

Le code Razor précédent comprend un lien vers l'action `Claims` que vous allez créer lors de l’étape suivante.

## <a name="step-5-add-the-claims-view"></a>Étape 5 : Ajouter la vue des revendications

Pour afficher les revendications de jeton d’ID sous le dossier */Views/Home*, ajoutez l’affichage *Claims.cshtml*.

```razor
@using System.Security.Claims

@{
  ViewData["Title"] = "Claims";
}
<h2>@ViewData["Title"].</h2>

<table class="table-hover table-condensed table-striped">
  <tr>
    <th>Claim Type</th>
    <th>Claim Value</th>
  </tr>

  @foreach (Claim claim in User.Claims)
  {
    <tr>
      <td>@claim.Type</td>
      <td>@claim.Value</td>
    </tr>
  }
</table>
```

Au cours de cette étape, vous allez ajouter l’action `Claims` qui lie l’affichage *claims.cshtml* au contrôleur *d’hébergement*. L’action `Claims` utilise l’attribut `Authorize`, qui limite l’accès à l’action aux utilisateurs authentifiés.  

Dans le contrôleur */Controllers/HomeController.cs*, ajoutez l’action suivante :

```csharp
[Authorize]
public IActionResult Claims()
{
    return View();
}
```

Ajoutez la déclaration `using` suivante au début de la classe :

```csharp
using Microsoft.AspNetCore.Authorization;
```

## <a name="step-6-add-the-app-settings"></a>Étape 6 : Ajouter les paramètres de l’application

Les paramètres du fournisseur d’identité Azure AD B2C sont stockés dans le fichier *appsettings.json*. Ouvrez *appsettings.json*, puis ajoutez les paramètres suivants :

```JSon
"AzureAdB2C": {
  "Instance": "https://<your-tenant-name>.b2clogin.com",
  "ClientId": "<web-app-application-id>",
  "Domain": "<your-b2c-domain>",
  "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
  "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
}
```

Les informations requises sont décrites dans l’article [Configurer l’authentification dans un exemple d’application web](configure-authentication-sample-web-app.md). Utilisez les paramètres suivants :

* **Instance** : Remplacez `<your-tenant-name>` par la première partie du [nom de votre locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C (par exemple, `https://contoso.b2clogin.com`).
* **Domain** : Remplacez `<your-b2c-domain>` par le [nom complet de votre locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C (par exemple, `contoso.onmicrosoft.com`).
* **Client ID** : Remplacez `<web-app-application-id>` par l’ID de l’application de [l'étape 2](configure-authentication-sample-web-app.md#step-2-register-a-web-application).
* **Policy name** : Remplacez `<your-sign-up-in-policy>` par les flux d’utilisateur que vous avez créés lors de [l'étape 1](configure-authentication-sample-web-app.md#step-1-configure-your-user-flow).

## <a name="step-7-run-your-application"></a>Étape 7 : Exécuter votre application

1. Générez et exécutez le projet.
1. Atteindre `https://localhost:5001`. 
1. Sélectionnez **S’inscrire/Se connecter**.
1. Terminez le processus d’inscription ou de connexion.

Une fois que vous vous êtes correctement authentifié, vous verrez votre nom d’affichage dans la barre de navigation. Pour afficher les revendications que le jeton Azure AD B2C retourne à votre application, sélectionnez **Revendications**.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [personnaliser et améliorer l’expérience d’authentification Azure AD B2C pour votre application web](enable-authentication-web-application-options.md).
