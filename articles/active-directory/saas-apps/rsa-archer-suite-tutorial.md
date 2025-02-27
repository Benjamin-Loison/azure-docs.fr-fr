---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à RSA Archer Suite'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et RSA Archer Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2021
ms.author: jeedes
ms.openlocfilehash: aac6f381e81dbc1d7465527623f0c1a29272a05b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279663"
---
# <a name="tutorial-azure-ad-sso-integration-with-rsa-archer-suite"></a>Tutoriel : Intégration de l’authentification unique Azure AD à RSA Archer Suite

Dans ce tutoriel, vous allez apprendre à intégrer RSA Archer Suite à Azure Active Directory (Azure AD). Quand vous intégrez RSA Archer Suite à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à RSA Archer Suite.
* Permettre à vos utilisateurs de se connecter automatiquement à RSA Archer Suite avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement RSA Archer Suite pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* RSA Archer Suite prend en charge l’authentification unique lancée par le **fournisseur de services**.
* RSA Archer Suite prend en charge l’attribution des utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-rsa-archer-suite-from-the-gallery"></a>Ajouter RSA Archer Suite à partir de la galerie

Pour configurer l’intégration de RSA Archer Suite à Azure AD, vous devez ajouter RSA Archer Suite à votre liste d’applications SaaS managées, à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **RSA Archer Suite** dans la zone de recherche.
1. Sélectionnez **RSA Archer Suite** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-rsa-archer-suite"></a>Configurer et tester l’authentification unique (SSO) Azure AD pour RSA Archer Suite

Configurez et testez l’authentification unique Azure AD avec RSA Archer Suite à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur RSA Archer Suite associé.

Pour configurer et tester l’authentification unique Azure AD avec RSA Archer Suite, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique RSA Archer Suite](#configure-rsa-archer-suite-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test RSA Archer Suite](#create-rsa-archer-suite-test-user)** pour avoir un équivalent de B.Simon dans RSA Archer Suite qui soit lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **RSA Archer Suite**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur (ID d’entité)**, tapez la valeur : `RSAArcherSuite_TENANT_STRING`

    b. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<BASE_URL>/default.aspx?IDP=<REALM_NAME>`

    > [!NOTE]
    > La valeur d’URL de connexion n’est pas réelle. Remplacez cette valeur par l’URL de connexion réelle. Contactez l’[équipe du support technique RSA Archer Suite](mailto:archersupport@rsa.com) pour obtenir la valeur. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application RSA Archer Suite attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application RSA Archer Suite s’attend à ce que quelques attributs supplémentaires, indiqués ci-après, soient passés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom |  Attribut source|
    | -------------- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | PhoneNumber | user.telephonenumber |
    | City | user.city |
    | Code postal | user.postalcode |
    | State | user.state |
    | Rue | user.streetaddress |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer RSA Archer Suite**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à RSA Archer Suite.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **RSA Archer Suite**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-rsa-archer-suite-sso"></a>Configurer l’authentification unique RSA Archer Suite

1. Connectez-vous au site web RSA Archer Suite en tant qu’administrateur dans un autre navigateur.

1. Effectuez les étapes ci-après dans la page suivante.

    ![Configurez l’authentification unique pour RSA Archer Suite.](./media/rsa-archer-suite-tutorial/configuration.png)

    a. Accédez à l’onglet **Single Sign-On** (Authentification unique), puis sélectionnez **SAML** pour **Single Sign-On Mode** (Mode d’authentification unique) dans la liste déroulante.

    b. Cochez la case **Allow manual bypass** (Autoriser le contournement manuel).

    c. Fournissez un nom valide dans la zone de texte **Instance Entity ID** (ID de l’entité de l’instance).

    d. Collez la **valeur de l’empreinte** dans la zone de texte **Certificate Thumbprint** (Empreinte numérique de certificat).

    e. Cliquez sur le bouton **Sélectionner**, puis chargez le fichier **XML de métadonnées de fédération** téléchargé à partir du portail Azure.

    f. Cliquez sur **Enregistrer** pour enregistrer les paramètres d’authentification unique. 

### <a name="create-rsa-archer-suite-test-user"></a>Créer un utilisateur de test RSA Archer Suite

Dans cette section, un utilisateur appelé B.Simon est créé dans RSA Archer Suite. RSA Archer Suite prend en charge l’attribution d’utilisateurs juste-à-temps, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans RSA Archer Suite, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion RSA Archer Suite où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL d’authentification unique de RSA Archer Suite pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette RSA Archer Suite dans Mes applications, vous êtes redirigé vers l’URL de connexion à RSA Archer Suite. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré RSA Archer Suite, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
