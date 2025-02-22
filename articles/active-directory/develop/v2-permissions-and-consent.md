---
title: Étendues, autorisations et consentement de la plateforme d’identités Microsoft
description: Découvrez l’autorisation dans le point de terminaison de la plateforme d’identités Microsoft, ce qui comprend notamment les étendues, les autorisations et les consentements.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40, has-adal-ref
ms.openlocfilehash: 292bca70ae9ebb8b864e95d9f5eda125a90a597d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632972"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Autorisations et consentement dans la plateforme d’identités Microsoft

Les applications qui s’intègrent à la plateforme d’identités Microsoft suivent un modèle d’autorisation permettant aux utilisateurs et aux administrateurs de contrôler l’accès aux données. L’implémentation de ce modèle d’autorisation a été mise à jour sur la plateforme d’identités Microsoft. Elle modifie la manière dont une application doit interagir avec la plateforme d’identités Microsoft. Cet article aborde les concepts de base de ce modèle d’autorisation, notamment les étendues, les autorisations et le consentement.

## <a name="scopes-and-permissions"></a>Étendues et autorisations

La plateforme d’identité Microsoft implémente le protocole d’autorisation [OAuth 2.0](active-directory-v2-protocols.md). OAuth 2.0 est une méthode par le biais de laquelle une application tierce peut accéder aux ressources hébergées sur le web au nom d’un utilisateur. Les ressources hébergées sur le web qui s’intègrent à la plateforme d’identités Microsoft présentent un identificateur de ressource, également appelé *URI d’ID d’application*. 

Voici quelques exemples de ressources Microsoft  hébergées sur le web :

* Microsoft Graph : `https://graph.microsoft.com`
* API de messagerie Microsoft 365 : `https://outlook.office.com`
* Azure Key Vault : `https://vault.azure.net`

Cela s’applique également aux ressources tierces intégrées à la plateforme d’identité Microsoft. Ces ressources peuvent également définir un ensemble d’autorisations à utiliser pour diviser la fonctionnalité de cette ressource en fragments plus réduits. Par exemple, [Microsoft Graph](https://graph.microsoft.com) a défini des autorisations pour effectuer les tâches suivantes, entre autres :

* Lire le calendrier d’un utilisateur
* Écrire dans le calendrier d’un utilisateur
* Envoi de messages en tant qu’utilisateur

Grâce à ces types de définitions d’autorisations, la ressource dispose d’un contrôle précis sur ses données et sur la façon dont les fonctionnalités de l’API sont exposées. Une application tierce peut demander ces autorisations à des utilisateurs et des administrateurs. Ces derniers doivent approuver la requête avant que l’application puisse accéder aux données ou agir pour le compte d’un utilisateur. 

Lorsque les fonctionnalités d’une ressource sont regroupées en petits ensembles d’autorisations, des applications tierces peuvent être créées pour ne demander que les autorisations nécessaires à leur fonctionnement. Les utilisateurs et les administrateurs peuvent connaître les données auxquelles l’application peut accéder. Ils peuvent ainsi être plus confiants dans le fait que l’application ne se comporte pas de manière malveillante. Les développeurs doivent toujours respecter le principe de moindre privilège et demander uniquement les autorisations dont ils ont besoin pour faire fonctionner leurs applications.

Dans OAuth 2.0, ces types de jeux d’autorisations sont appelés des *étendues*. Ils sont également souvent appelés *autorisations*. Dans la plateforme d’identités Microsoft, une autorisation est représentée sous la forme d’une valeur de chaîne. Une application demande les autorisations dont elle a besoin en spécifiant l’autorisation dans le paramètre de requête `scope`. La plateforme d’identité prend en charge plusieurs [étendues OpenID Connect](#openid-connect-scopes) bien définies, ainsi que des autorisations basées sur les ressources (pour chaque autorisation, la valeur de celle-ci est ajoutée à l’URI de l’identificateur de la ressource ou de l’ID de l’application). Par exemple, la chaîne d’autorisation `https://graph.microsoft.com/Calendars.Read` est utilisée pour demander l’autorisation de lire les calendriers des utilisateurs dans Microsoft Graph.

Généralement, une application peut demander ces autorisations en spécifiant les étendues dans les demandes dirigées vers le point de terminaison d’autorisation de la plateforme d’identités Microsoft. Toutefois, certaines autorisations à privilèges élevés peuvent être accordées uniquement par le biais du consentement de l’administrateur. Elles peuvent être demandées ou accordées en utilisant le [point de terminaison de consentement de l’administrateur](#admin-restricted-permissions). Poursuivez votre lecture pour en savoir plus.

Dans les requêtes adressées aux points de terminaison d’autorisation, de jeton ou de consentement pour la plateforme d’identités Microsoft, si l’identificateur de ressource est omis dans le paramètre d’étendue, la ressource est censée être Microsoft Graph. Par exemple, `scope=User.Read` équivaut à `https://graph.microsoft.com/User.Read`.

## <a name="permission-types"></a>Types d'autorisations

La plateforme d’identités Microsoft prend en charge deux types d’autorisations : les *permissions déléguées* et les *permissions d’application*.

* Les **autorisations déléguées** sont utilisées par les applications pour lesquelles un utilisateur est connecté et présent. Pour ces applications, l’utilisateur ou un administrateur consent aux autorisations demandées par l’application. L’application se voit déléguer la permission d’agir en tant qu’utilisateur connecté lorsqu’elle effectue des appels vers la ressource cible. 

    Certaines permissions déléguées peuvent être consenties par des non-administrateurs. Toutefois, certaines autorisations à privilèges élevés requièrent le [consentement de l’administrateur](#admin-restricted-permissions). Pour connaître les rôles Administrateur habilités à donner leur consentement pour les permissions déléguées, consultez [Autorisations du rôle Administrateur dans Azure Active Directory (Azure AD)](../roles/permissions-reference.md).

* Les **permissions d’application** sont utilisées par les applications qui s’exécutent sans qu’un utilisateur soit connecté et présent, par exemple, les applications qui s’exécutent en tant que services ou démons en arrière-plan. Seul [un administrateur peut consentir](#requesting-consent-for-an-entire-tenant) aux permissions d’application.

Les _autorisations effectives_ correspondent aux autorisations dont dispose votre application lorsqu’elle envoie des requêtes à une ressource cible. Vous devez comprendre la différence entre les permissions déléguées et les permissions d’application accordées à votre application et les autorisations effectives accordées à votre application lorsqu’elle effectue des appels vers la ressource cible.

- Pour les permissions déléguées, les _autorisations effectives_ de votre application correspondent au niveau de privilège le moins élevé entre les permissions déléguées que l’application a reçues (par le biais d’un consentement) et les privilèges de l’utilisateur actuellement connecté. Votre application ne peut jamais avoir plus de privilèges que l’utilisateur connecté. 

   Au sein des organisations, les privilèges de l’utilisateur connecté peuvent être déterminés par la stratégie ou l’appartenance à un ou plusieurs rôles Administrateur. Pour connaître les rôles administrateur habilités à donner leur consentement pour les autorisations déléguées, consultez [Autorisations du rôle administrateur dans Azure AD](../roles/permissions-reference.md).

   Supposons que votre application ait reçu l’autorisation déléguée _User.ReadWrite.All_. Cette autorisation permet nominalement à votre application de lire et mettre à jour le profil de chaque utilisateur dans une organisation. Si l’utilisateur connecté est un administrateur général, votre application peut mettre à jour le profil de chaque utilisateur de l’organisation. Toutefois, si l’utilisateur connecté n’a pas de rôle Administrateur, votre application peut uniquement mettre à jour le profil de l’utilisateur connecté. Elle ne peut pas mettre à jour les profils des autres utilisateurs de l’organisation, car l’utilisateur pour lequel elle est autorisée à agir n’a pas ces privilèges.

- Pour les permissions d’application, les _autorisations effectives_ de votre application correspondent au niveau complet des privilèges impliqués par l’autorisation. Par exemple, une application disposant de l’autorisation d’application _User.ReadWrite.All_ peut mettre à jour le profil de chaque utilisateur de l’organisation.

## <a name="openid-connect-scopes"></a>Étendues OpenId Connect

L’implémentation d’OpenID Connect sur la plateforme d’identités Microsoft comprend quelques étendues bien définies qui sont également hébergées sur Microsoft Graph : `openid`, `email`, `profile` et `offline_access`. Les étendues OpenID Connect `address` et `phone` ne sont pas prises en charge.

Si vous demandez les étendues OpenID Connect et un jeton, vous obtenez un jeton pour appeler le [point de terminaison UserInfo](userinfo.md).

### <a name="openid"></a>openid

Si une application se connecte à l’aide [d’OpenID Connect](active-directory-v2-protocols.md), elle doit solliciter l’étendue `openid`. L’étendue `openid` s’affiche sur la page de consentement du compte professionnel en tant qu’**autorisation de connexion**. Sur la page de consentement du compte Microsoft personnel, elle s’affiche en tant qu’autorisation **Afficher votre profil et se connecter aux applications et aux services à l’aide de votre compte Microsoft**. 

En utilisant cette autorisation, une application peut recevoir un identificateur unique pour l’utilisateur sous la forme de la revendication `sub`. L’autorisation permet également à l’application d’accéder au point de terminaison UserInfo. L’étendue `openid` peut être utilisée sur le point de terminaison des jetons de la plateforme d’identités Microsoft pour acquérir des jetons d’ID. L’application peut utiliser ces derniers pour l’authentification.

### <a name="email"></a>email

L’étendue `email` peut être utilisée avec l’étendue `openid` et toute autre étendue. Elle permet à l’application d’accéder à l’adresse de messagerie principale de l’utilisateur sous la forme de la revendication `email`. 

La revendication `email` est incluse dans un jeton uniquement si une adresse e-mail est associée au compte d’utilisateur, ce qui n’est pas toujours le cas. Si votre application utilise l’étendue `email`, l’application doit être en mesure de traiter un cas dans lequel aucune revendication `email` n’existe dans le jeton.

### <a name="profile"></a>profile

L’étendue `profile` peut être utilisée avec l’étendue `openid` et toute autre étendue. Elle permet à l’application d’accéder à une grande quantité d’informations sur l’utilisateur, notamment le prénom de l’utilisateur, son nom de famille, son nom d’utilisateur privilégié et l’ID d’objet. 

Pour obtenir la liste complète des revendications `profile` disponibles dans le paramètre `id_tokens` pour un utilisateur donné, consultez la [référence `id_tokens`](id-tokens.md).

### <a name="offline_access"></a>offline_access

[L’étendue `offline_access`](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) permet à votre application d’accéder aux ressources pour le compte de l’utilisateur pendant une période prolongée. Dans la page de consentement, cette étendue apparaît comme l’autorisation **Conserver l’accès aux données auxquelles vous lui avez donné l’accès**. 

Lorsqu’un utilisateur approuve l’étendue `offline_access`, votre application peut recevoir les jetons d’actualisation du point de terminaison des jetons de la plateforme d’identités Microsoft. Les jetons d’actualisation sont de longue durée. Votre application peut obtenir de nouveaux jetons d’accès lorsque les plus anciens arrivent à expiration.

> [!NOTE]
> Cette autorisation apparaît actuellement sur toutes les pages de consentement, même pour les flux qui ne fournissent pas de jeton d’actualisation (par exemple, le [flux implicite](v2-oauth2-implicit-grant-flow.md)). Cette configuration concerne les scénarios dans lesquels un client peut commencer dans le cadre du flux implicite, puis passer au flux de code où un jeton d’actualisation est attendu.

Sur la plateforme d’identités Microsoft (requêtes adressées au point de terminaison v2.0), votre application doit demander explicitement l’étendue `offline_access` pour recevoir les jetons d’actualisation. Ainsi, lorsque vous acceptez un code d’autorisation dans le [flux de code d’autorisation OAuth 2.0](active-directory-v2-protocols.md), vous recevez uniquement un jeton d’accès du point de terminaison `/token`. 

Le jeton d’accès est valide pendant une courte durée : il arrive généralement à expiration en une heure. À ce stade, votre application doit rediriger l’utilisateur vers le point de terminaison `/authorize` afin de récupérer un nouveau code d’autorisation. Pendant ce réacheminement, en fonction du type d’application, l’utilisateur peut devoir entrer à nouveau ses informations d’identification ou accepter une nouvelle fois les autorisations.

Pour en savoir plus sur la récupération et l’utilisation des jetons d’actualisation, consultez la page [Référence sur le protocole de la plateforme d’identités Microsoft](active-directory-v2-protocols.md).

## <a name="consent-types"></a>Types de consentement

Les applications de la plateforme d’identités Microsoft s’appuient sur le consentement pour accéder aux ressources ou aux API nécessaires. Il existe plusieurs types de consentement dont votre application peut avoir besoin pour fonctionner correctement. Si vous définissez des autorisations, vous devez également comprendre la façon dont vos utilisateurs vont accéder à votre application ou API.

### <a name="static-user-consent"></a>Consentement de l’utilisateur statique 

Dans le scénario du consentement de l’utilisateur statique, vous devez spécifier toutes les autorisations nécessaires dans la configuration de l’application, au sein du portail Azure. Si l’utilisateur (ou l’administrateur, selon le cas) n’a pas octroyé son consentement pour cette application, la plateforme d’identités Microsoft invite l’utilisateur à donner son consentement à ce stade.

Les autorisations statiques permettent également aux administrateurs de [donner leur consentement au nom de tous les utilisateurs](#requesting-consent-for-an-entire-tenant) de l’organisation.

Bien que les autorisations statiques de l’application définies dans le portail Azure préservent la simplicité du code, elles présentent quelques problèmes potentiels pour les développeurs :

- L’application doit demander toutes les autorisations dont elle est susceptible d’avoir besoin dès la première connexion de l’utilisateur. Ceci peut résulter en une longue liste d’autorisations qui décourage les utilisateurs finaux d’approuver l’accès de l’application lors de la connexion initiale.

- L’application doit connaître au préalable l’ensemble des ressources auxquelles elle est susceptible d’accéder. Il est difficile de créer des applications pouvant accéder à un nombre arbitraire de ressources.

### <a name="incremental-and-dynamic-user-consent"></a>Consentement de l’utilisateur incrémentiel et dynamique

Avec le point de terminaison de la Plateforme d’identités Microsoft, vous pouvez ignorer les autorisations statiques définies dans les informations d’inscription de l’application du portail Azure et demander à la place des autorisations de façon incrémentielle.  Vous pouvez demander dès le départ un ensemble minimal d’autorisations, puis en demander plus ultérieurement quand le client utilise des fonctionnalités d’application supplémentaires. Pour cela, vous pouvez spécifier les étendues dont votre application a besoin à tout moment en incluant les nouvelles étendues dans le paramètre `scope` quand vous [demandez un jeton d’accès](#requesting-individual-user-consent), sans qu’il soit nécessaire de les définir au préalable dans les informations d’inscription de l’application. Si l’utilisateur n’a pas encore consenti aux nouvelles étendues ajoutées à la demande, il est invité à donner son consentement seulement aux nouvelles autorisations. Consentement incrémentiel ou dynamique, s’applique uniquement aux autorisations déléguées et pas aux permissions d’application.

En permettant à l’application de demander des autorisations de façon dynamique grâce au paramètre `scope`, les développeurs maîtrisent totalement l’expérience de vos utilisateurs. Vous pouvez aussi anticiper l’expérience de consentement et demander toutes les autorisations dans une même demande d’autorisation initiale. Si votre application nécessite un grand nombre d’autorisations, vous pouvez les demander à l’utilisateur de façon incrémentielle quand il essaie d’utiliser certaines fonctionnalités de votre application au fil du temps.

> [!IMPORTANT]
> Le consentement dynamique peut s’avérer pratique, mais il représente un véritable défi pour les autorisations qui nécessitent un consentement de l’administrateur, dans la mesure où l’expérience de consentement de l’administrateur n’a pas connaissance de ces autorisations au moment du consentement. Si vous avez besoin d’autorisations à privilège administratif ou si votre application utilise le consentement dynamique, vous devez inscrire toutes les autorisations dans le portail Azure (pas seulement le sous-ensemble d’autorisations qui nécessite le consentement de l’administrateur). Ceci permet aux administrateurs de locataires de donner leur consentement au nom de tous leurs utilisateurs.

### <a name="admin-consent"></a>Consentement de l’administrateur

[Consentement administrateur](#using-the-admin-consent-endpoint) : nécessaire quand votre application a besoin d’accéder à certaines autorisations dotées de privilèges élevés. Le consentement de l’administrateur garantit que les administrateurs disposent de contrôles supplémentaires avant d’autoriser des applications ou des utilisateurs à accéder aux données à privilèges élevés de l’organisation.

Le [consentement administrateur accordé au nom d’une organisation](#requesting-consent-for-an-entire-tenant) nécessite toujours l’inscription des autorisations statiques pour l’application. Définissez ces autorisations pour les applications dans le portail d’inscription d’applications, si vous avez besoin qu’un administrateur donne son consentement au nom de l’ensemble de l’organisation. Ainsi, les cycles nécessaires à l’administrateur de l’organisation pour configurer l’application sont réduits.

## <a name="requesting-individual-user-consent"></a>Demande de consentement d’utilisateur individuel

Dans une demande d’autorisation [OpenID Connect ou OAuth 2.0](active-directory-v2-protocols.md), une application peut demander les autorisations nécessaires à l’aide du paramètre de requête `scope`. Par exemple, lorsqu’un utilisateur se connecte à une application, cette dernière envoie une requête semblable à l’exemple ci-dessous. (Des sauts de ligne sont ajoutés pour une meilleure lisibilité.)

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

Le paramètre `scope` correspond à une liste d’autorisations déléguées séparées par des espaces, demandées par l’application. Chaque autorisation est indiquée par l’ajout de la valeur correspondante à l’identificateur de la ressource (URI d’ID d’application). Dans l’exemple de requête, l’application nécessite l’autorisation de lecture du calendrier de l’utilisateur et d’envoi de messages au nom de l’utilisateur.

Une fois que l’utilisateur a entré ses informations d’identification, la plateforme d’identités Microsoft recherche un enregistrement correspondant du *consentement de l’utilisateur*. Si, par le passé, l’utilisateur n’a consenti à aucune des autorisations demandées et que l’administrateur n’a pas consenti à ces autorisations pour le compte de toute l’organisation, la plateforme d’identités Microsoft demande à l’utilisateur d’accorder les autorisations demandées.

À ce stade, les autorisations `offline_access` (« Conserver l’accès aux données auxquelles vous lui avez donné l’accès ») et `user.read` (« Vous connecter et lire votre profil ») sont automatiquement incluses dans le consentement initial pour une application.  Ces autorisations sont généralement requises pour le bon fonctionnement de l’application. L’autorisation `offline_access` donne à l’application l’accès à des jetons d’actualisation qui sont essentiels pour les applications natives et les applications web. L’autorisation `user.read` donne accès à la revendication `sub`. Elle permet au client ou à l’application d’identifier correctement l’utilisateur au fil du temps et d’accéder à des informations rudimentaires sur l’utilisateur.

![Capture d’écran montrant le consentement sur le compte professionnel.](./media/v2-permissions-and-consent/work_account_consent.png)

Lorsque l’utilisateur approuve la demande d’autorisation, le consentement est enregistré. L’utilisateur n’a plus à donner son consentement lorsqu’il se connecte ultérieurement à l’application.

## <a name="requesting-consent-for-an-entire-tenant"></a>Demande de consentement d’un client entier

Lorsqu’une organisation achète une licence ou un abonnement pour une application, elle souhaite souvent configurer l’application de manière proactive afin que tous les membres de l’organisation puissent l’utiliser. Dans le cadre de cette procédure, un administrateur peut autoriser l’application à agir au nom de n’importe quel utilisateur au sein du locataire. Si l’administrateur donne son consentement pour l’intégralité du locataire, les utilisateurs de l’organisation ne voient pas de page de consentement pour l’application.

Le consentement administrateur accordé au nom d’une organisation nécessite toujours l’inscription des autorisations statiques pour l’application. Définissez ces autorisations pour les applications dans le portail d’inscription d’applications, si vous avez besoin qu’un administrateur donne son consentement au nom de l’ensemble de l’organisation.

Si vous souhaitez demander le consentement relatif aux autorisations déléguées pour tous les utilisateurs d’un locataire, votre application peut utiliser le [point de terminaison de consentement administrateur](#using-the-admin-consent-endpoint).

En outre, les applications doivent utiliser le point de terminaison du consentement administrateur pour demander des permissions d’application.

## <a name="admin-restricted-permissions"></a>Autorisations restreintes aux administrateurs

Certaines autorisations à privilèges élevés dans les ressources Microsoft peuvent être définies sur *restreintes aux administrateurs*. Voici quelques exemples de ces types d’autorisations :

* Lire les profils complets de tous les utilisateurs à l’aide de `User.Read.All`
* Écrire des données dans le répertoire d’une organisation à l’aide de `Directory.ReadWrite.All`
* Lire tous les groupes dans le répertoire d’une organisation à l’aide de `Groups.Read.All`

> [!NOTE]
>Dans les requêtes adressées aux points de terminaison d’autorisation, de jeton ou de consentement pour la plateforme d’identités Microsoft, si l’identificateur de ressource est omis dans le paramètre d’étendue, la ressource est censée être Microsoft Graph. Par exemple, `scope=User.Read` équivaut à `https://graph.microsoft.com/User.Read`.

Si un utilisateur consommateur peut accorder à une application l’accès à ce type de données, les utilisateurs de l’organisation ne peuvent pas accorder l’accès au même jeu de données d’entreprise sensibles. Si votre application requiert l’accès à l’une de ces autorisations d’un utilisateur de l’organisation, ce dernier recevra un message d’erreur indiquant qu’il n’est pas autorisé à donner son consentement pour les permissions de votre application.

Si votre application requiert des étendues pour les autorisations restreintes aux administrateurs, l’administrateur d’une organisation doit donner son consentement à ces étendues pour le compte des utilisateurs de l’organisation. Pour éviter d’afficher des invites aux utilisateurs qui demandent un consentement pour les autorisations qu’ils ne peuvent pas accorder, votre application peut utiliser le point de terminaison du consentement administrateur. Le point de terminaison du consentement administrateur est abordé dans la section suivante.

Si l’application demande des permissions déléguées à privilèges élevés et qu’un administrateur accorde ces permissions via le point de terminaison du consentement administrateur, le consentement est accordé à tous les utilisateurs du locataire.

Si l’application demande des permissions d’application et qu’un administrateur accorde ces permissions via le point de terminaison du consentement administrateur, cette attribution n’est pas effectuée pour le compte d’un utilisateur spécifique. L’application cliente reçoit les autorisations *directement*. Ces types d’autorisations sont utilisés uniquement par les services démon et d’autres applications non interactives qui s’exécutent en arrière-plan.

## <a name="using-the-admin-consent-endpoint"></a>Utilisation du point de terminaison de consentement administrateur

Une fois que vous avez utilisé le point de terminaison du consentement administrateur pour accorder le consentement administrateur, vous avez terminé. Les utilisateurs n’ont pas besoin d’effectuer d’autres actions. Une fois le consentement administrateur accordé, les utilisateurs peuvent obtenir un jeton d’accès par le biais d’un flux d’authentification standard. Le jeton d’accès obtenu possède les autorisations accordées.

Lorsqu’un administrateur général utilise votre application et est dirigé vers le point de terminaison d’autorisation, la plateforme d’identités Microsoft détecte le rôle de l’utilisateur. Elle demande si l’administrateur général souhaite donner son consentement pour le compte de l’ensemble du locataire aux autorisations que vous avez demandées. Vous pouvez utiliser à la place un point de terminaison du consentement administrateur dédié pour demander de manière proactive à un administrateur d’accorder l’autorisation pour le compte de l’ensemble du locataire. Ce point de terminaison est également nécessaire pour demander des permissions d’application. Les permissions d’application ne peuvent pas être demandées à l’aide du point de terminaison d’autorisation.

Si vous suivez ces étapes, votre application peut demander des autorisations pour tous les utilisateurs d’un locataire, notamment les étendues restreintes aux administrateurs. Cette opération demande des privilèges élevés. Utilisez l’opération uniquement si elle est nécessaire pour votre scénario.

Pour voir un exemple de code qui implémente les étapes, consultez [l’exemple d’étendues restreintes aux administrateurs](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) dans GitHub.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Demander les autorisations dans le portail d’inscription de l’application

Dans le portail d’inscription des applications, les applications peuvent répertorier les autorisations dont elles ont besoin, y compris les permissions déléguées et les permissions d’application. Cette configuration permet d’utiliser l’étendue `/.default` et l’option **Accorder un consentement administrateur** du portail Azure.  

En général, les autorisations doivent être définies de manière statique pour une application donnée. Elles doivent être un sur-ensemble des autorisations demandées par l’application de manière dynamique ou incrémentielle.

> [!NOTE]
>Les permissions d’application ne peuvent être demandées qu’à l’aide de [`/.default`](#the-default-scope). Dès lors, si votre application requiert des permissions d’application, vérifiez qu’elles sont répertoriées dans le portail d’inscription des applications.

Pour configurer la liste des autorisations demandées de manière statique pour une application :

1. Accédez à votre application dans l’expérience de démarrage rapide <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Portail Azure – Inscriptions d’applications</a>.
1. Sélectionnez une application ou [créez une application](quickstart-register-app.md) si vous ne l’avez pas déjà fait.
1. Dans la page **Vue d’ensemble** de l’application, sous **Gérer**, sélectionnez **Autorisations de l’API** > **Ajouter une autorisation**.
1. Sélectionnez **Microsoft Graph** dans la liste des API disponibles. Ajoutez ensuite les autorisations dont votre application a besoin.
1. Sélectionnez **Ajouter des autorisations**.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Recommandé : connectez l’utilisateur à votre application

En général, lorsque vous créez une application qui utilise le point de terminaison de consentement de l’administrateur, l’application doit disposer d’une page ou vue dans laquelle l’administrateur peut approuver ses autorisations. Cette page peut être :

* Une partie du flux d’inscription de l’application.
* Une partie des paramètres de l’application.
* Un flux de « connexion » dédié. 

Dans de nombreux cas, il est judicieux pour l’application d’afficher la vue de « connexion » uniquement après qu’un utilisateur se soit connecté avec un compte Microsoft professionnel ou un compte Microsoft scolaire.

Lorsque vous connectez l’utilisateur à votre application, vous pouvez identifier l’organisation à laquelle l’administrateur appartient, avant de lui demander d’approuver les autorisations nécessaires. Même si cette étape n’est pas strictement nécessaire, elle peut vous aider à créer une expérience plus intuitive pour les utilisateurs de votre organisation. 

Pour connecter l’utilisateur, suivez les [tutoriels sur le protocole de la plateforme d’identités Microsoft](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Demander les autorisations à un administrateur d’annuaire

Lorsque vous êtes prêt à demander les autorisations à l’administrateur de votre organisation, vous pouvez rediriger l’utilisateur vers le point de terminaison de consentement administrateur de la plateforme d’identités Microsoft.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Paramètre        | Condition        | Description                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Obligatoire | Le client d’annuaire auquel vous souhaitez demander l’autorisation. Il peut être fourni sous forme de GUID ou de nom convivial. Il peut aussi être référencé de manière générique auprès des organisations, comme illustré dans l’exemple. N’utilisez pas « common », car les comptes personnels ne peuvent pas fournir de consentement administrateur, sauf dans le contexte d’un locataire. Pour garantir la meilleure compatibilité avec les comptes personnels qui gèrent les locataires, utilisez l’ID de locataire, dans la mesure du possible. |
| `client_id` | Obligatoire | L’ID (client) d’application attribué à votre application par l’expérience [Portail Azure – Inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908). |
| `redirect_uri` | Obligatoire |L'URI de redirection où vous souhaitez que la réponse soit envoyée pour être gérée par votre application. Il doit correspondre exactement à l’un des URI de redirection que vous avez inscrits dans le portail d’inscription des applications. |
| `state` | Recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Utilisez l’état pour encoder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou la vue sur laquelle ou laquelle il était positionné. |
|`scope`        | Obligatoire        | Définit l’ensemble des autorisations demandées par l’application. Les étendues peuvent être statiques (utilisant [`/.default`](#the-default-scope)) ou dynamiques.  Cet ensemble peut inclure les étendues OpenID Connect (`openid`, `profile`, `email`). S'il vous faut des autorisations d’application, vous devez utiliser `/.default` pour demander la liste d’autorisations configurée statiquement.  |


À ce stade, Azure AD nécessite qu’un administrateur client se connecte pour terminer la demande. L’administrateur est invité à approuver toutes les autorisations que vous avez demandées dans le paramètre `scope`.  Si vous avez utilisé une valeur statique (`/.default`), celle-ci fonctionne comme le point de terminaison du consentement administrateur v1.0 et demande un consentement pour toutes les étendues trouvées dans les autorisations requises pour l’application.

#### <a name="successful-response"></a>Réponse correcte

Si l’administrateur approuve les autorisations pour votre application, la réponse correcte ressemble à ce qui suit :

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paramètre | Description |
| --- | --- |
| `tenant` | Client d’annuaire ayant accordé à votre application les autorisations demandées au format GUID. |
| `state` | Une valeur incluse dans la requête qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. La valeur d’état est utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| `admin_consent` | Sera défini sur `True`. |

#### <a name="error-response"></a>Réponse d’erreur

Si l’administrateur n’approuve pas les autorisations pour votre application, la réponse d’échec ressemble à ce qui suit :

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paramètre | Description |
| --- | --- |
| `error` | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent. Elle peut également être utilisée pour réagir aux erreurs. |
| `error_description` | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur. |

Une fois que vous avez reçu une réponse correcte du point de terminaison de consentement de l’administrateur, votre application a acquis les autorisations qu’elle avait demandées. Vous pouvez alors demander un jeton pour la ressource souhaitée.

## <a name="using-permissions"></a>Utilisation des autorisations

Une fois que l’utilisateur consent aux autorisations pour votre application, cette dernière peut acquérir des jetons d’accès représentant l’autorisation de l’application à accéder, dans une certaine capacité, à une ressource. Un jeton d’accès ne peut être utilisé que pour une seule ressource. Toutefois, l’encodage de ce jeton comporte les informations relatives à toutes les autorisations accordées à votre application pour cette ressource. Pour acquérir un jeton d’accès, votre application peut transmettre une demande au point de terminaison des jetons de la plateforme d’identités Microsoft :

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Vous pouvez utiliser le jeton d’accès obtenu dans les requêtes HTTP transmises à la ressource. Il indique de façon fiable à la ressource que votre application dispose de l’autorisation appropriée pour effectuer une tâche spécifique.

Pour en savoir plus sur le protocole OAuth 2.0 et sur la méthode pour obtenir des jetons d’accès, consultez la page de [Référence sur le protocole du point de terminaison de la plateforme d’identités Microsoft](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Étendue /.default

Vous pouvez utiliser l’étendue `/.default` pour faciliter la migration de vos applications à partir du point de terminaison v1.0 vers le point de terminaison de la plateforme d’identités Microsoft. L’étendue `/.default` est intégrée pour chaque application qui fait référence à la liste statique des autorisations configurées sur l’inscription d’application. 

Une valeur `scope` de `https://graph.microsoft.com/.default` est fonctionnellement identique à celle de `resource=https://graph.microsoft.com` sur le point de terminaison v1.0. En spécifiant l’étendue `https://graph.microsoft.com/.default` dans sa demande, votre application demande un jeton d’accès qui comprend des étendues pour chaque autorisation Microsoft Graph que vous avez sélectionnée pour l’application dans le portail d’inscription des applications. L’étendue est construite à l’aide de l’URI de ressource et de `/.default`. Par conséquent, si l’URI de la ressource est `https://contosoApp.com`, l’étendue demandée est `https://contosoApp.com/.default`.  Pour savoir quand vous devez inclure une deuxième barre oblique afin de correctement demander le jeton, consultez la [section relative aux barres obliques de fin](#trailing-slash-and-default).

L’étendue `/.default` peut être utilisée dans n’importe quel flux OAuth 2.0. Toutefois, il est nécessaire dans le [flux On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) et le [flux d’informations d’identification du client](v2-oauth2-client-creds-grant-flow.md). Vous en avez également besoin lorsque vous utilisez le point de terminaison du consentement administrateur v2 pour demander des permissions d’application.

Les clients ne peuvent pas combiner le consentement statique (`/.default`) et le consentement dynamique dans une seule demande. Ainsi, `scope=https://graph.microsoft.com/.default+mail.read` génère une erreur, car il combine des types d’étendue différents.

### <a name="default-and-consent"></a>/.default et consentement

L’étendue `/.default` déclenche le comportement de point de terminaison v1.0 également pour `prompt=consent`. Elle demande le consentement pour toutes les autorisations inscrites par l’application, quelle que soit la ressource. Si elle est incluse dans le cadre de la demande, l’étendue `/.default` retourne un jeton qui contient les étendues pour la ressource demandée.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default quand l’utilisateur a déjà donné son consentement

L’étendue `/.default` est fonctionnellement identique au comportement du point de terminaison v1.0 centré sur `resource`. Elle porte également le comportement de consentement du point de terminaison v1.0. Autrement dit, `/.default` déclenche une invite de consentement uniquement si l’utilisateur n’a accordé aucune autorisation entre le client et la ressource. 

Si un tel consentement existe, le jeton retourné contient toutes les étendues que l’utilisateur a accordées pour cette ressource. Toutefois, si aucune autorisation n’a été accordée ou si le paramètre `prompt=consent` a été fourni, une invite de consentement s’affiche pour toutes les étendues inscrites par l’application cliente.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Exemple 1 : L’utilisateur, ou l’administrateur de locataire, a accordé des autorisations

Dans cet exemple, l’utilisateur ou un administrateur de locataire a accordé au client les autorisations Microsoft Graph `mail.read` et `user.read`. 

Si le client demande `scope=https://graph.microsoft.com/.default`, aucune invite de consentement ne s’affiche, quel que soit le contenu des autorisations inscrites par l’application cliente pour Microsoft Graph. Le jeton retourné contient les étendues `mail.read` et `user.read`.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Exemple 2 : L’utilisateur n’a pas accordé d’autorisations entre le client et la ressource

Dans cet exemple, l’utilisateur n’a pas donné son consentement entre le client et Microsoft Graph. Le client s’est inscrit pour les autorisations `user.read` et `contacts.read`. Il s’est également inscrit pour l’étendue d’Azure Key Vault `https://vault.azure.net/user_impersonation`. 

Quand le client demande un jeton pour `scope=https://graph.microsoft.com/.default`, l’utilisateur voit une page de consentement pour l’étendue `user.read`, l’étendue `contacts.read` et les étendues Key Vault `user_impersonation`. Le jeton retourné contient uniquement les étendues `user.read` et `contacts.read`. Il peut uniquement être utilisé auprès de Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>Exemple 3 : L’utilisateur a donné son consentement, et le client demande des étendues supplémentaires

Dans cet exemple, l’utilisateur a déjà donné son consentement à `mail.read` pour le client. Le client s’est inscrit pour l’étendue `contacts.read`. 

Lorsque le client demande un jeton à l’aide de `scope=https://graph.microsoft.com/.default` et demande un consentement via `prompt=consent`, l’utilisateur voit une page de consentement pour toutes (et uniquement) les autorisations inscrites par l’application. L’étendue `contacts.read` se trouve sur la page de consentement, mais pas `mail.read`. Le jeton retourné est destiné à Microsoft Graph. Il contient `mail.read` et `contacts.read`.

### <a name="using-the-default-scope-with-the-client"></a>Utilisation de l’étendue /.default avec le client

Dans certains cas, un client peut demander sa propre étendue `/.default`. L’exemple suivant illustre ce cas de figure.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Cet exemple de code produit une page de consentement pour toutes les autorisations inscrites si les descriptions précédentes du consentement et `/.default` s’appliquent au scénario. Ensuite, le code retourne un `id_token`, plutôt qu’un jeton d’accès.  

Ce comportement prend en charge certains clients hérités qui passent de Bibliothèque d’authentification Azure AD (ADAL) à Bibliothèque d’authentification Microsoft (MSAL). Cette configuration *ne doit pas* être utilisée par les nouveaux clients qui ciblent la plateforme d’identités Microsoft.

### <a name="client-credentials-grant-flow-and-default"></a>Flux d’octroi des informations d’identification du client et /.default  

Une autre utilisation de `/.default` consiste à demander des permissions d’application (ou des *rôles*) dans une application non interactive telle qu’une application démon qui utilise le flux d’octroi des [informations d’identification du client](v2-oauth2-client-creds-grant-flow.md) pour appeler une API web.

Pour créer des permissions d’application (rôles) pour une API web, consultez [Ajouter des rôles d’application dans votre application](howto-add-app-roles-in-azure-ad-apps.md).

Les demandes d’informations d’identification du client dans votre application cliente *doivent* inclure `scope={resource}/.default`. Ici, `{resource}` est l’API web que votre application a l’intention d’appeler. L’émission d’une demande d’informations d’identification du client à l’aide de permissions d’application individuelles (rôles) n’est *pas* prise en charge. Toutes les permissions d’application (rôles) qui ont été accordées pour cette API web sont incluses dans le jeton d’accès retourné.

Pour accorder l’accès aux permissions d’application que vous définissez, y compris l’octroi du consentement administrateur pour l’application, consultez [Configurer une application cliente pour accéder à une API web](quickstart-configure-app-access-web-apis.md).

### <a name="trailing-slash-and-default"></a>Barre oblique de fin et /.default

Certains URI de ressource ont une barre oblique de fin, par exemple, `https://contoso.com/` par opposition à `https://contoso.com`. La barre oblique de fin peut entraîner des problèmes de validation des jetons. Les problèmes se produisent principalement lorsqu’un jeton est demandé pour Azure Resource Manager (`https://management.azure.com/`). Dans ce cas, une barre oblique à la fin de l’URI de ressource signifie que la barre oblique doit être présente lorsque le jeton est demandé.  Ainsi, lorsque vous demandez un jeton pour `https://management.azure.com/` et que vous utilisez `/.default`, vous devez demander `https://management.azure.com//.default` (prenez note de la double barre oblique). En général, si vous vérifiez que le jeton est émis et que celui-ci est rejeté par l’API qui doit l’accepter, ajoutez une deuxième barre oblique, puis réessayez. 

## <a name="troubleshooting-permissions-and-consent"></a>Résolution des problèmes d’autorisations et de consentement

Pour connaître les étapes de résolution des problèmes, consultez [Erreur inattendue lors de l’exécution du consentement sur une application](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

## <a name="next-steps"></a>Étapes suivantes

* [Jetons d’ID de la plateforme d’identités Microsoft](id-tokens.md)
* [Jetons d’accès de la plateforme d’identités Microsoft](access-tokens.md)
