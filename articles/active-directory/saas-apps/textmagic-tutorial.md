---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à TextMagic | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et TextMagic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/14/2021
ms.author: jeedes
ms.openlocfilehash: 09f00aaa669ca89e3b6d3b8c580f7088bf4a3e83
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316832"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-textmagic"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à TextMagic

Ce tutoriel explique comment intégrer TextMagic avec Azure Active Directory (Azure AD). Quand vous intégrez TextMagic à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à TextMagic.
* Permettre à vos utilisateurs de se connecter automatiquement à TextMagic avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement TextMagic pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* TextMagic prend en charge l’authentification unique lancée par le **fournisseur d’identité**.

* TextMagic prend en charge l’attribution d’utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-textmagic-from-the-gallery"></a>Ajouter TextMagic à partir de la galerie

Pour configurer l’intégration de TextMagic avec Azure AD, vous devez ajouter TextMagic à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **TextMagic** dans la zone de recherche.
1. Sélectionnez **TextMagic** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-textmagic"></a>Configurer et tester l’authentification unique Azure AD pour TextMagic

Configurez et testez l’authentification unique Azure AD avec TextMagic pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur TextMagic associé.

Pour configurer et tester l’authentification unique Azure AD auprès de TextMagic, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique TextMagic](#configure-textmagic-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test TextMagic](#create-textmagic-test-user)** pour avoir dans TextMagic un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le Portail Azure, accédez à la page d’intégration de l’application **TextMagic**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez l’étape suivante :

    Dans la zone de texte **Identificateur**, tapez l’URL : `https://my.textmagic.com/saml/metadata`

5. L’application TextMagic attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**. L’application TextMagic s’attend à ce que **nameidentifier** soit mappé avec **user.mail**. Vous devez donc modifier le mappage d’attribut en cliquant sur l’icône **Modifier**.

    ![image](common/edit-attribute.png)

1. En plus de ce qui précède, l’application TextMagic s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom |   Attribut source| Espace de noms  |
    | --------------- | --------------- | --------------- |
    | société | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | firstName | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastName | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | phone | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer TextMagic**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TextMagic.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **TextMagic**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-textmagic-sso"></a>Configurer l’authentification unique TextMagic

1. Pour automatiser la configuration dans TextMagic, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer TextMagic** pour être redirigé vers l’application TextMagic. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à TextMagic. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 5.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement TextMagic, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise TextMagic en tant qu’administrateur et effectuez les étapes suivantes :

4. Sélectionnez **Paramètres du compte** sous le nom d’utilisateur.

    ![Capture d’écran montrant l’élément Paramètres du compte sélectionné par l’utilisateur](./media/textmagic-tutorial/account.png)

5. Cliquez sur l’onglet **Authentification unique (SSO)** , puis complétez les champs suivants :  

    ![Capture d’écran montrant l’onglet Authentification unique dans lequel vous pouvez entrer les valeurs décrites](./media/textmagic-tutorial/settings.png)

    a. Dans la zone de texte **Identity provider Entity ID:** (ID d’entité du fournisseur d’identité), collez la valeur de l’**identificateur Azure AD**, que vous avez copiée à partir du portail Azure.

    b. Dans la zone de texte **Identity provider SSO URL:** (URL d’authentification unique du fournisseur d’identité), collez la valeur de l’**URL de connexion** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **Identity provider SLO URL:** (URL SLO du fournisseur d’identité), collez la valeur d’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    d. Ouvrez dans le Bloc-notes votre **certificat codé en base 64** téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat x509 public**.

    e. Cliquez sur **Enregistrer**.

### <a name="create-textmagic-test-user"></a>Créer un utilisateur de test TextMagic

Dans cette section, un utilisateur appelé B.Simon est créé dans TextMagic. TextMagic prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans TextMagic, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Cliquez sur Tester cette application dans le Portail Azure : vous devez être connecté automatiquement à l’instance de TextMagic pour laquelle vous avez configuré l’authentification unique

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette TextMagic dans Mes applications, vous devez être connecté automatiquement à l’application TextMagic pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré TextMagic, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
