---
title: Utilisez les étiquettes pour fournir des valeurs de configuration par environnement.
titleSuffix: Azure App Configuration
description: Cet article décrit l’utilisation d’étiquettes pour récupérer des valeurs de configuration d’application pour l’environnement dans lequel l’application est en cours d’exécution.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: alkemper
ms.openlocfilehash: e6d9aadff5fba66aef260c674f5a01904b289da2
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111592355"
---
# <a name="use-labels-to-provide-per-environment-configuration-values"></a>Utilisez les étiquettes pour fournir des valeurs de configuration par environnement.

De nombreuses applications ont besoin de différentes configurations selon les environnements. Supposons qu’une application possède une valeur de configuration qui définit la chaîne de connexion à utiliser pour sa base de données principale. Les développeurs de l’application utilisent une autre base de données que celle utilisée en production. La chaîne de connexion de base de données utilisée par l’application doit changer lorsque l’application passe du développement à la production.

Dans Azure App Configuration, il est possible d’utiliser des *étiquettes* afin de définir des valeurs différentes pour la même clé. Par exemple, vous pouvez définir une clé unique avec des valeurs différentes pour le développement et la production, et spécifier la ou les étiquettes à charger lors de la connexion à App Configuration.

Pour illustrer cette fonctionnalité, vous allez modifier l’application web créée dans [Démarrage rapide : Création d’une application ASP.NET Core avec Azure App Configuration](./quickstart-aspnet-core-app.md) en utilisant des paramètres de configuration différents pour le développement et la production. Suivez le guide de démarrage rapide avant de continuer.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Spécification d’une étiquette lors de l’ajout d’une valeur de configuration

Sur le Portail Azure, accédez à **Explorateur de configuration** et recherchez la clé *TestApp:Settings:FontColor* que vous avez créée dans le guide de démarrage rapide. Sélectionnez son menu contextuel, puis sélectionnez **Ajouter une valeur**.

> [!div class="mx-imgBorder"]
> ![Élément de menu Ajouter une valeur](media/labels-add-value.png)

Sur l’écran **Ajouter une valeur**, entrez **Rouge** comme **Valeur** et **Développement** comme **Étiquette**. Laissez **Type de contenu** vide. Sélectionnez **Appliquer**.

## <a name="load-configuration-values-with-a-specified-label"></a>Charger les valeurs de configuration avec une étiquette spécifiée

Par défaut, Azure App Configuration charge uniquement les valeurs de configuration sans étiquette. Si vous avez défini des étiquettes pour vos valeurs de configuration, spécifiez les étiquettes à utiliser lors de la connexion à App Configuration.

Dans la section précédente, vous avez créé une valeur de configuration différente pour l’environnement de développement. Vous utilisez la variable `HostingEnvironment.EnvironmentName` pour déterminer dynamiquement dans quel environnement d’exécution l’application s’exécute. Pour plus d’informations, consultez [Utilisation de plusieurs environnements dans ASP.NET Core](/aspnet/core/fundamentals/environments).

Ajoutez une référence à l’espace de noms [Microsoft.Extensions.Configfiguration.AzureAppConfiguration](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration) pour accéder aux classes [KeyFilter](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.keyfilter) et [LabelFilter](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.labelfilter).

```csharp
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
``` 

Chargez les valeurs de configuration avec l’étiquette correspondant à l’environnement actuel en passant le nom de l’environnement dans la méthode `Select` :

### <a name="net-core-5x"></a>[.NET Core 5.x](#tab/core5x)

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options =>
                options
                    .Connect(settings.GetConnectionString("AppConfig"))
                    // Load configuration values with no label
                    .Select(KeyFilter.Any, LabelFilter.Null)
                    // Override with any configuration values specific to current hosting env
                    .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
            );
        })
        .UseStartup<Startup>());
```

### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options =>
                options
                    .Connect(settings.GetConnectionString("AppConfig"))
                    // Load configuration values with no label
                    .Select(KeyFilter.Any, LabelFilter.Null)
                    // Override with any configuration values specific to current hosting env
                    .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
            );
        })
        .UseStartup<Startup>());
```

### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options =>
                options
                    .Connect(settings.GetConnectionString("AppConfig"))
                    // Load configuration values with no label
                    .Select(KeyFilter.Any, LabelFilter.Null)
                    // Override with any configuration values specific to current hosting env
                    .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
            );
        })
        .UseStartup<Startup>();
```
---


> [!IMPORTANT]
> L’extrait de code précédent utilise l’outil Secret Manager pour charger la chaîne de connexion d’App Configuration. Pour plus d’informations sur le stockage de la chaîne de connexion à l’aide de Secret Manager, consultez [Démarrage rapide pour Azure App Configuration avec ASP.NET Core](quickstart-aspnet-core-app.md).

La méthode `Select` est appelée deux fois. La première fois, elle charge les valeurs de configuration sans étiquette. Ensuite, elle les charge à nouveau avec l’étiquette correspondant à l’environnement actuel. Ces valeurs propres à l’environnement remplacent toutes les valeurs correspondantes sans étiquette. Il n’est pas nécessaire de définir des valeurs propres à l’environnement pour chacune des clés. Si une clé ne comporte pas de valeur dotée d’une étiquette correspondant à l’environnement actuel, elle utilise la valeur sans étiquette.

## <a name="test-in-different-environments"></a>Test dans des environnements différents

Ouvrez le fichier `launchSettings.json` qui se trouve dans le répertoire `Properties`. Recherchez l’entrée `config` sous `profiles`. Dans la section `environmentVariables`, définissez la variable `ASPNETCORE_ENVIRONMENT` sur `Production`.

Une fois les nouvelles valeurs définies, générez et exécutez votre application.

```dotnetcli
dotnet build
dotnet run
```

Utilisez un navigateur web pour accéder à `http://localhost:5000`. Vous remarquerez que la police est de couleur noire.

![Application web exécutée avec la configuration de production](media/labels-website-prod.png)

Mettez à jour `launchSettings.json` pour définir la variable `ASPNETCORE_ENVIRONMENT` sur `Development`. Exécutez de nouveau `dotnet run`. 

Vous remarquerez que la police est maintenant de couleur rouge. En effet, l’application utilise désormais la valeur de `TestApp:Settings:FontColor` comportant l’étiquette `Development`. Toutes les autres valeurs de configuration restent les mêmes que leurs valeurs de production.

![Application web exécutée avec la configuration de développement](media/labels-website-dev.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration dans ASP.NET Core](/aspnet/core/fundamentals/configuration/)
