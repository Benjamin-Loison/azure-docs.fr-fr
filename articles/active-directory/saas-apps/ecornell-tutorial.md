---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à eCornell | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et eCornell.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.openlocfilehash: 7fdacd88c4aa913c4e9d14a15f8ef663a59d9ff0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124813665"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ecornell"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à eCornell

Dans ce tutoriel, vous allez découvrir comment intégrer eCornell à Azure Active Directory (Azure AD). Quand vous intégrez eCornell à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à eCornell.
* Permettre à vos utilisateurs de se connecter automatiquement à eCornell avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement eCornell pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* eCornell prend en charge l’authentification unique lancée par le **fournisseur de services**
* eCornell prend en charge le provisionnement d’utilisateurs **juste-à-temps**

## <a name="adding-ecornell-from-the-gallery"></a>Ajout d’eCornell à partir de la galerie

Pour configurer l’intégration d’eCornell à Azure AD, vous devez ajouter eCornell à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **eCornell** dans la zone de recherche.
1. Sélectionnez **eCornell** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ecornell"></a>Configurer et tester l’authentification unique Azure AD pour eCornell

Configurez et testez l’authentification unique Azure AD avec eCornell pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur eCornell associé.

Pour configurer et tester l’authentification unique Azure AD avec eCornell, effectuez les module suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique eCornell](#configure-ecornell-sso)** pour configurer les paramètres de l’authentification unique côté application.
    * **[Créer un utilisateur de test eCornell](#create-ecornell-test-user)** pour avoir un équivalent de B.Simon dans eCornell lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [Portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **eCornell**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://admin.ecornell.com/sso/clp/<groupCode>`.

    b. Dans la zone de texte **Identificateur**, tapez une URL en utilisant le format suivant : `http://pingone.com/<eCornellCustomGUID>`

    c. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2?saasid=<CustomGUID>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support client d’eCornell](mailto:jschichor@ecornell.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Votre application eCornell s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jetons SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application eCornell s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom |  Attribut source|
    | -------------- | --------- |
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.mail |
    | SAML_SUBJECT | user.userprincipalname |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer eCornell**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à eCornell.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **eCornell**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-ecornell-sso"></a>Configurer l’authentification unique eCornell

Pour configurer l’authentification unique côté **eCornell**, vous devez envoyer le **XML des métadonnées de fédération** téléchargé et les URL copiées correspondantes à partir du portail Azure à l’[équipe du support eCornell](mailto:jschichor@ecornell.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-ecornell-test-user"></a>Créer un utilisateur de test eCornell

Dans cette section, un utilisateur appelé B.Simon est créé dans eCornell. eCornell prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans eCornell, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette eCornell dans le volet d’accès, vous devez être connecté automatiquement à l’application eCornell pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](./tutorial-list.md)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](../conditional-access/overview.md)

- [Essayer eCornell avec Azure AD](https://aad.portal.azure.com/)