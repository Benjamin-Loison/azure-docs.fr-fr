---
title: Trusona et Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Découvrez comment ajouter Trusona comme fournisseur d’identité dans Azure AD B2C pour activer l’authentification sans mot de passe.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: a1f898861de55776e30227ac17d4a628c7481e00
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130035882"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>Intégrer Trusona à Azure Active Directory B2C

Trusona est un éditeur de logiciels indépendant (ISV) qui fournit des solutions conçues pour vous aider à sécuriser vos connexions par le biais de l’authentification sans mot de passe, de l’authentification multifacteur et de l’analyse de licence numérique. Dans cet article, découvrez comment ajouter Trusona comme fournisseur d’identité dans Azure AD B2C pour activer l’authentification sans mot de passe.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un [locataire Azure AD B2C](tutorial-create-tenant.md) qui est lié à votre abonnement Azure.
* Un [compte d’essai](https://www.trusona.com/) sur Trusona.

## <a name="scenario-description"></a>Description du scénario

Dans ce scénario, Trusona joue le rôle de fournisseur d’identité pour Azure AD B2C afin d’activer l’authentification par mot de passe. La solution se compose des éléments suivants :

* Une stratégie Azure AD B2C combinée pour la connexion et l’inscription
* Trusona ajouté à Azure AD B2C comme fournisseur d’identité
* L’application Trusona téléchargeable

![Diagramme de l’architecture Trusona](media/partner-trusona/trusona-architecture-diagram.png)

| Étape | Description |
|------|------|
|1     | Un utilisateur tente de se connecter à l’application ou de s’y inscrire. L’utilisateur est authentifié au moyen de la stratégie Azure AD B2C d’inscription et de connexion. Lors de l’inscription, l’adresse e-mail préalablement vérifiée de l’utilisateur à partir de l’application Trusona est utilisée.     |
|2     | Azure B2C redirige l’utilisateur vers le fournisseur d’identité Trusona OpenID Connect (OIDC) en utilisant le flux implicite.     |
|3     | Pour les connexions sur des PC de bureau, Trusona affiche un code QR unique, sans état, animé et dynamique qui sera analysé avec l’application Trusona. Pour les connexions sur des appareils mobiles, Trusona utilise un « lien profond » pour ouvrir l’application Trusona. Ces deux méthodes sont utilisées pour la découverte de l’appareil, puis de l’utilisateur final.     |
|4     | L’utilisateur analyse le code QR affiché avec l’application Trusona.     |
|5     | Le compte de l’utilisateur est trouvé dans le service cloud Trusona, et l’authentification est préparée.     |
|6     | Le service cloud Trusona envoie une demande d’authentification à l’utilisateur au moyen d’une notification Push envoyée à l’application Trusona :<br>a. L’utilisateur reçoit la demande d’authentification. <br> b. L’utilisateur choisit d’accepter ou de refuser la demande. <br> c. L’utilisateur est invité à utiliser la sécurité du système d’exploitation (par exemple, biométrique, code secret, code PIN ou modèle) pour valider et signer la demande avec une clé privée dans l’environnement Exécution approuvée/Enclave sécurisée. <br> d. L’application Trusona génère une charge utile dynamique d’anti-relecture basée sur les paramètres de l’authentification en temps réel. <br> e. La réponse entière est signée (une deuxième fois) par une clé privée dans l’environnement Exécution approuvée/Enclave sécurisée, puis elle est renvoyée au service cloud Trusona pour vérification.      |
|7     |  Le service cloud Trusona redirige l’utilisateur vers l’application initiale en joignant un jeton id_token. Azure AD B2C vérifie le jeton id_token d’après la configuration OpenID publiée de Trusona, telle qu’elle a été définie lors de la configuration du fournisseur d’identité.    |
|  |  |

## <a name="onboard-with-trusona"></a>Intégration de Trusona

1. Remplissez le [formulaire](https://www.trusona.com/) pour créer un compte Trusona et démarrer.

1. Téléchargez l’application mobile Trusona à partir de l’App Store. Installez l’application et enregistrez votre adresse e-mail.

1. Vérifiez votre adresse e-mail via le « lien magique » sécurisé envoyé par le logiciel.  

1. Accédez au [tableau de bord des développeurs Trusona](https://dashboard.trusona.com) en libre-service.

1. Sélectionnez **I’m Ready** (Je suis prêt) et authentifiez-vous auprès de votre application Trusona.

1. Dans le volet de navigation de gauche, choisissez **OIDC Integrations** (Intégrations OIDC).

1. Sélectionnez **Create OpenID Connect Integration** (Créer une intégration OpenID Connect).

1. Entrez un **nom** de votre choix et utilisez les informations de domaine fournies précédemment (par exemple, Contoso) dans le champ **Client Redirect Host** (Hôte de redirection du client).  

   > [!NOTE]
   > Le nom de domaine initial d’Azure Active Directory est utilisé comme hôte de redirection du client.

1. Suivez les instructions données dans le [guide d’intégration de Trusona](https://docs.trusona.com/integrations/aad-b2c-integration/). Lorsque vous y êtes invité, entrez le nom de domaine initial (par exemple, Contoso) mentionné à l’étape précédente.  

## <a name="integrate-with-azure-ad-b2c"></a>Intégrer avec Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Ajouter un nouveau fournisseur d’identité

> [!NOTE]
> Si vous n’en avez pas, [créez un locataire Azure AD B2C](tutorial-create-tenant.md) qui est lié à votre abonnement Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Accédez à **Tableau de bord** > **Azure Active Directory B2C** > **Fournisseurs d’identité**.
1. Sélectionnez **Fournisseurs d’identité**.
1. Sélectionnez **Ajouter**.

### <a name="configure-an-identity-provider"></a>Configurer un fournisseur d’identité  

1. Sélectionnez **Type de fournisseur d’identité** > **OpenID Connect (préversion)** .

1. Remplissez le formulaire pour configurer le fournisseur d’identité :  

   | Propriété | Valeur  |
   | :--- | :--- |
   | URL de métadonnées | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | ID client | Vous sera envoyé par e-mail par Trusona |
   | Étendue | E-mail du profil OpenID |
   | Type de réponse | Id_token |
   | Mode de réponse  | Form_post |

1. Sélectionnez **OK**.  

1. Sélectionnez **Mapper les revendications de ce fournisseur d’identité**.  

1. Remplissez le formulaire pour mapper le fournisseur d’identité :

   | Propriété | Valeur  |
   | :--- | :--- |
   | UserID | Sub  |
   | Nom complet | nickname |
   | Prénom | given_name |
   | Surname | Family_name |
   | Mode de réponse | email |

1. Sélectionnez **OK** pour terminer la configuration de votre nouveau fournisseur d’identité OIDC.

### <a name="create-a-user-flow-policy"></a>Créer une stratégie de flux utilisateur

Vous devez maintenant voir Trusona listé comme **nouveau fournisseur d’identité OpenID Connect** parmi vos fournisseurs d’identité B2C.

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateurs** sous **Stratégies**.

1. Sélectionnez **Nouveau flux d’utilisateurs**.

1. Sélectionnez successivement **Inscription et connexion**, une version, puis **Créer**.

1. Sous **Nom**, spécifiez un nom pour votre stratégie.

1. Dans la section **Fournisseurs d’identité**, sélectionnez le **fournisseur d’identité Trusona** que vous venez de créer.

   > [!NOTE]
   > Étant donné que Trusona est intrinsèquement multifacteur, il est préférable de conserver l’authentification multifacteur désactivée.

1. Sélectionnez **Create** (Créer).

1. Sous **Attributs utilisateur et revendications**, choisissez **Afficher plus**. Dans le formulaire, sélectionnez au moins un attribut que vous avez spécifié lors de la configuration de votre fournisseur d’identité dans la section précédente.

1. Sélectionnez **OK**.  

### <a name="test-the-policy"></a>Tester la stratégie

1. Sélectionnez la stratégie créée.

1. Sélectionnez **Exécuter le flux d’utilisateurs**, puis sélectionnez les paramètres :

   1. **Application** : sélectionnez l’application enregistrée.
 
   1. **URL de réponse** : sélectionnez l’URL de redirection.
   
1. Sélectionnez **Exécuter le flux utilisateur**. Vous êtes normalement redirigé vers la passerelle OIDC Trusona. Sur la passerelle Trusona, analysez le code QR sécurisé affiché avec l’application Trusona ou avec une application personnalisée basée sur le SDK Mobile Trusona.

1. Une fois l’analyse du code QR sécurisé terminée, vous êtes redirigé vers l’URL de réponse que vous avez définie.

## <a name="next-steps"></a>Étapes suivantes  

Pour plus d’informations, consultez les articles suivants :

- [Stratégies personnalisées dans Azure AD B2C](custom-policy-overview.md)

- [Bien démarrer avec les stratégies personnalisées dans AAD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
