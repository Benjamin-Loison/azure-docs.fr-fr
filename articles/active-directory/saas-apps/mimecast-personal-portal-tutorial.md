---
title: 'Tutoriel : Intégration d’Azure Active Directory avec Mimecast Personal Portal | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Mimecast Personal Portal.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: f6cc85a437e455becb516859fb3bacae0086cb85
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325924"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-personal-portal"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory avec Mimecast Personal Portal

Dans ce tutoriel, vous allez découvrir comment intégrer Mimecast Personal Portal à Azure AD (Azure Active Directory). Quand vous intégrez Mimecast Personal Portal à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Mimecast Personal Portal.
* Permettre aux utilisateurs de se connecter automatiquement à Mimecast Personal Portal avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Mimecast Personal Portal pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Mimecast Personal Portal prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**
 
## <a name="add-mimecast-personal-portal-from-the-gallery"></a>Ajouter Mimecast Personal Portal à partir de la galerie

Pour configurer l’intégration de Mimecast Personal Portal à Azure AD, vous devez ajouter Mimecast Personal Portal à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Mimecast Personal Portal** dans la zone de recherche.
1. Sélectionnez **Mimecast Personal Portal** dans le panneau des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-mimecast-personal-portal"></a>Configurer et tester l’authentification unique Azure AD pour Mimecast Personal Portal

Configurez et testez l’authentification unique Azure AD avec Mimecast Personal Portal pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Mimecast Personal Portal associé.

Pour configurer et tester l’authentification unique Azure AD avec Mimecast Personal Portal, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Mimecast Personal Portal](#configure-mimecast-personal-portal-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Mimecast Personal Portal](#create-mimecast-personal-portal-test-user)** pour obtenir un équivalent de B.Simon dans Mimecast Personal Portal lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans la page d’intégration de l’application **Mimecast Personal Portal** du portail Azure, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le fournisseur d’identité, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez l’URL au format suivant : 

    | Région  |  Valeur | 
    | --------------- | --------------- |
    | Europe          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | États-Unis   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | Afrique du Sud    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australie       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > Vous trouverez la valeur `accountcode` dans le portail Mimecast Personal Portal sous **Account** > **Settings** > **Account Code** (Compte > Paramètres > Code de compte). Ajoutez le `accountcode` à l’identificateur.

    b. Dans la zone de texte **URL de réponse**, tapez l’URL : 

    | Région  |  Valeur |
    | --------------- | --------------- |
    | Europe          | `https://eu-api.mimecast.com/login/saml`|
    | États-Unis   | `https://us-api.mimecast.com/login/saml`|
    | Afrique du Sud    | `https://za-api.mimecast.com/login/saml`|
    | Australie       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

1. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service** :

    Dans la zone de texte **URL de connexion**, tapez l’URL : 

    | Région  |  Valeur |
    | --------------- | --------------- |
    | Europe          | `https://eu-api.mimecast.com/login/saml`|
    | États-Unis   | `https://us-api.mimecast.com/login/saml`|
    | Afrique du Sud    | `https://za-api.mimecast.com/login/saml`|
    | Australie       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

1. Cliquez sur **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Mimecast Personal Portal.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Mimecast Personal Portal**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-mimecast-personal-portal-sso"></a>Configurer l’authentification unique Mimecast Personal Portal

1. Dans une autre fenêtre du navigateur web, connectez-vous à la console d’administrateur Mimecast.

1. Accédez à **Administration** > **Services** > **Applications**.

    ![Capture d’écran montrant la fenêtre Mimecast avec l’élément Applications sélectionné.](./media/mimecast-personal-portal-tutorial/services.png)

1. Cliquez sur l’onglet **Authentication Profiles** (Profils d’authentification).
    
    ![Capture d’écran montrant l’onglet Application avec les profils d’authentification sélectionnés.](./media/mimecast-personal-portal-tutorial/authentication-profiles.png)

1. Cliquez sur l’onglet **New Authentication Profile** (Nouveau profil d’authentification).

    ![Capture d’écran montrant l’élément New Authentication Profile sélectionné.](./media/mimecast-personal-portal-tutorial/new-authenticatio-profile.png)

1. Entrez une description valide dans la zone de texte **Description** et cochez la case **Enforce SAML Authentication for Mimecast Personal Portal** (Appliquer l’authentification SAML pour Mimecast Personal Portal).

    ![Capture d’écran montrant l’élément New Authentication Profile sélectionné.](./media/mimecast-personal-portal-tutorial/selecting-personal-portal.png)

1. Dans la page **SAML Configuration for Mimecast Personal Portal** (Configuration SAML pour Mimecast Personal Portal), effectuez les étapes suivantes :

    ![Capture d’écran montrant l’emplacement où sélectionner Enforce SAML Authentication for Administration Console.](./media/mimecast-personal-portal-tutorial/sso-settings.png)

    a. Pour **Provider** (Fournisseur), sélectionnez **Azure Active Directory** dans la liste déroulante.

    b. Dans la zone de texte **Metadata URL** (URL de métadonnées), collez la valeur **URL des métadonnées de fédération d’application** que vous avez copiée à partir du portail Azure.

    c. Cliquez sur **Importer**. Une fois l’URL des métadonnées importée, les champs sont renseignés automatiquement et aucune action n’est nécessaire sur ces champs.

    d. Veillez à décocher les cases **Use Password protected Context** (Utiliser le contexte protégé par mot de passe) et **Use Integrated Authentication Context** (Utiliser le contexte d’authentification intégré).

    e. Cliquez sur **Save**.

### <a name="create-mimecast-personal-portal-test-user"></a>Créer un utilisateur de test Mimecast Personal Portal

1. Dans une autre fenêtre du navigateur web, connectez-vous à la console d’administrateur Mimecast.

1. Accédez à **Administration** > **Directories** > **Internal Directories** (Administration > Répertoires > Répertoires internes).

    ![Capture d’écran montrant la configuration SAML pour Mimecast Personal Portal dans laquelle vous pouvez indiquer les valeurs décrites.](./media/mimecast-personal-portal-tutorial/internal-directories.png)

1. Sélectionnez votre domaine, s’il est mentionné ci-dessous. Sinon, créez-en un en cliquant sur **New Domain** (Nouveau domaine).

    ![Capture d’écran montrant la fenêtre Mimecast avec l’élément Internal Directories sélectionné.](./media/mimecast-personal-portal-tutorial/domain-name.png)

1. Cliquez sur l’onglet **New Address** (Nouvelle adresse).

    ![Capture d’écran montrant le domaine sélectionné.](./media/mimecast-personal-portal-tutorial/new-address.png)

1. Fournissez les informations utilisateur requises dans la page suivante :

    ![Capture d’écran montrant la page dans laquelle vous pouvez indiquer les valeurs décrites.](./media/mimecast-personal-portal-tutorial/user-information.png)

    a. Dans la zone de texte **Email Address**, entrez l’adresse e-mail de l’utilisateur, par exemple `B.Simon@yourdomainname.com`.

    b. Dans la zone de texte **Global Name** (Nom global), entrez le **nom complet** de l’utilisateur.

    c. Dans les zones de texte **Password** et **Confirm Password**, entrez le mot de passe de l’utilisateur.

    d. Cochez la case **Force Change at Login** (Forcer la modification lors de la connexion).

    e. Cliquez sur **Enregistrer**.

    f. Pour attribuer des rôles à l’utilisateur, cliquez sur **Role Edit** (Modification du rôle) et attribuez le rôle requis à l’utilisateur conformément aux exigences de votre organisation.

    ![Capture d’écran montrant la zone Address Settings où vous pouvez sélectionner Role Edit.](./media/mimecast-personal-portal-tutorial/assign-role.png)


## <a name="test-sso"></a>Tester l’authentification unique (SSO) 
Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL d’authentification Mimecast Personal Portal, où vous pouvez lancer le processus de connexion.  

* Accédez directement à l’URL d’authentification Mimecast Personal Portal pour lancer le processus de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Dans le portail Azure, cliquez sur **Tester cette application**. Vous êtes alors automatiquement connecté à l’instance Mimecast Personal Portal pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Mimecast Personal Portal dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’application Mimecast Personal Portal pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Mimecast Personal Portal, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
