---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Greenlight Enterprise Business Controls Platform'
description: Apprenez à configurer l'authentification unique entre Azure Active Directory et Greenlight Enterprise Business Controls Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/18/2021
ms.author: jeedes
ms.openlocfilehash: 3b80c7c8c0a3fa0a61270146d7d3ec79a1d88774
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132311486"
---
# <a name="tutorial-azure-ad-sso-integration-with-greenlight-enterprise-business-controls-platform"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Greenlight Enterprise Business Controls Platform

Dans ce tutoriel, vous allez apprendre à intégrer Greenlight Enterprise Business Controls Platform à Azure Active Directory (Azure AD). Lorsque vous intégrez Greenlight Enterprise Business Controls Platform à Azure AD, vous pouvez :

* Dans Azure AD, contrôler l'accès des utilisateurs à Greenlight Enterprise Business Controls Platform.
* Permettre à vos utilisateurs de se connecter automatiquement à Greenlight Enterprise Business Controls Platform avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Greenlight Enterprise Business Controls Platform pour lequel l'authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Greenlight Enterprise Business Controls Platform prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.

## <a name="add-greenlight-enterprise-business-controls-platform-from-the-gallery"></a>Ajouter Greenlight Enterprise Business Controls Platform à partir de la galerie

Pour configurer l'intégration de Greenlight Enterprise Business Controls Platform à Azure AD, vous devez ajouter Greenlight Enterprise Business Controls Platform à votre liste d'applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Greenlight Enterprise Business Controls Platform** dans la zone de recherche.
1. Sélectionnez **Greenlight Enterprise Business Controls Platform** dans le volet de résultats, puis ajoutez l'application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-greenlight-enterprise-business-controls-platform"></a>Configurer et tester l'authentification unique Azure AD pour Greenlight Enterprise Business Controls Platform

Configurez et testez l'authentification unique Azure AD avec Greenlight Enterprise Business Controls Platform à l'aide d'un utilisateur de test appelé **B.Simon**. Pour que l'authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l'utilisateur Greenlight Enterprise Business Controls Platform associé.

Pour configurer et tester l’authentification unique Azure AD avec Greenlight Enterprise Business Controls Platform, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l'authentification unique Greenlight Enterprise Business Controls Platform](#configure-greenlight-enterprise-business-controls-platform-sso)** - pour configurer les paramètres de l'authentification unique côté application.
    1. **[Créer un utilisateur de test Greenlight Enterprise Business Controls Platform](#create-greenlight-enterprise-business-controls-platform-test-user)** - pour avoir dans Greenlight Enterprise Business Controls Platform un équivalent de B.Simon lié à la représentation Azure AD de l'utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Greenlight Enterprise Business Controls Platform**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<CUSTOMER_NAME>.gltcloud.com/ebcpplatform/saml`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<CUSTOMER_NAME>.gltcloud.com/ebcpplatform/saml`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<CUSTOMER_NAME>.gltcloud.com/ebcpplatform/saml`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l'[équipe de support technique de Greenlight Enterprise Business Controls Platform](mailto:support@greenlightcorp.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Greenlight Enterprise Business Controls Platform**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l'authentification unique Azure en lui accordant l'accès à Greenlight Enterprise Business Controls Platform.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Greenlight Enterprise Business Controls Platform**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-greenlight-enterprise-business-controls-platform-sso"></a>Configurer l'authentification unique Greenlight Enterprise Business Controls Platform

Pour configurer l'authentification unique côté **Greenlight Enterprise Business Controls Platform**, vous devez envoyer le **XML des métadonnées de fédération** téléchargé et les URL correspondantes copiées sur le portail Azure à l'[équipe du support technique Greenlight Enterprise Business Controls Platform](mailto:support@greenlightcorp.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-greenlight-enterprise-business-controls-platform-test-user"></a>Créer un utilisateur de test Greenlight Enterprise Business Controls Platform

Dans cette section, vous allez créer un utilisateur appelé B.Simon dans Greenlight Enterprise Business Controls Platform. Collaborez avec l’[équipe du support technique Greenlight Enterprise Business Controls Platform](mailto:support@greenlightcorp.com) pour ajouter les utilisateurs à la plateforme Greenlight Enterprise Business Controls Platform. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion de Greenlight Enterprise Business Controls Platform, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion de Greenlight Enterprise Business Controls Platform pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors automatiquement connecté automatiquement à l’instance de Greenlight Enterprise Business Controls Platform pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Greenlight Enterprise Business Controls Platform dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour initier le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Greenlight Enterprise Business Controls Platform pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Greenlight Enterprise Business Controls Platform, vous pouvez appliquer un contrôle de session qui protège l'exfiltration et l'infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
