---
title: 'Didacticiel : Intégration d’Azure Active Directory à Zoho | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 439dd4adf30633d167c72d071c9f5152021abc1c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319849"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Didacticiel : Intégration d’Azure Active Directory à Zoho

Dans ce tutoriel, vous allez voir comment intégrer Zoho à Azure Active Directory (Azure AD). Quand vous intégrez Zoho à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à Zoho.
* Permettre à vos utilisateurs de se connecter automatiquement à Zoho avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Zoho One, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Zoho pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Zoho prend en charge l’authentification unique (SSO) initiée par le **fournisseur de services**

## <a name="add-zoho-from-the-gallery"></a>Ajouter Zoho à partir de la galerie

Pour configurer l’intégration de Zoho avec Azure AD, vous devez ajouter Zoho, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Zoho** dans la zone de recherche.
1. Sélectionnez **Zoho** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-zoho"></a>Configurer et tester l’authentification unique (SSO) Azure AD pour Zoho

Configurez et testez l’authentification unique Azure AD avec Zoho pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Zoho associé.

Pour configurer et tester l’authentification unique Azure AD avec Zoho, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Zoho](#configure-zoho-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Zoho](#create-zoho-test-user)** pour avoir dans Zoho un équivalent de B.Simon qui soit lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Zoho**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.zohomail.com`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe du support Zoho](https://www.zoho.com/mail/contact.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Zoho**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zoho.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Zoho**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="configure-zoho-sso"></a>Configurer l’authentification unique pour Zoho

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zoho Mail en tant qu’administrateur.

2. Accédez à **Control panel**.
   
    ![Panneau de configuration](./media/zoho-mail-tutorial/control-panel.png "Control panel")

3. Cliquez sur l’onglet **SAML Authentication** .
   
    ![Authentification SAML](./media/zoho-mail-tutorial/saml-authentication.png "Authentification SAML")

4. Dans la section **SAML Authentication Details** , procédez comme suit :
   
    ![Détails de l’authentification SAML](./media/zoho-mail-tutorial/details.png "SAML Authentication Details")
   
    a. Dans la zone de texte **URL de connexion**, collez l’**URL de connexion** que vous avez copiée dans le portail Azure.
   
    b. Dans la zone de texte **URL de déconnexion**, collez l’**URL de déconnexion** que vous avez copiée dans le portail Azure.
   
    c. Dans la zone de texte **Change Password Link**, collez l’**URL de modification de mot de passe** que vous avez copié à partir du portail Azure.
       
    d. Ouvrez dans le Bloc-notes votre certificat codé en base 64 téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **PublicKey**.
   
    e. Comme **Algorithme**, sélectionnez **RSA**.
   
    f. Cliquez sur **OK**.

### <a name="create-zoho-test-user"></a>Créer un utilisateur de test Zoho

Pour que les utilisateurs d’Azure AD puissent se connecter à Zoho Mail, ils doivent être approvisionnés dans Zoho Mail. Dans le cas de Zoho Mail, l’approvisionnement est une tâche manuelle.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Zoho Mail pour provisionner des comptes d’utilisateurs Azure AD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous à votre site d’entreprise **Zoho Mail** en tant qu’administrateur.

1. Accédez à **Control Panel \> Mail & Docs**.

1. Accédez à **User Details \> Add User**.
   
    ![Capture d’écran montrant le site Zoho Mail avec les éléments User Details et Add User sélectionnés.](./media/zoho-mail-tutorial/add-user-1.png "Ajouter un utilisateur")

1. Dans la boîte de dialogue **Add users** , procédez comme suit :
   
    ![Capture d’écran montrant la boîte de dialogue Add users, dans laquelle vous pouvez entrer les valeurs décrites.](./media/zoho-mail-tutorial/add-user-2.png "Ajouter un utilisateur")
   
    a. Dans la zone de texte **First Name**, tapez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last Name**, tapez le nom de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **ID d’e-mail**, tapez l’ID d’e-mail de l’utilisateur, par exemple **brittasimon\@contoso.com**.

    d. Dans la zone de texte **Password**, tapez le mot de passe de l’utilisateur.
   
    e. Cliquez sur **OK**.  
      
    > [!NOTE]
    > Le titulaire du compte Azure AD reçoit un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.

### <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Zoho, d’où vous pouvez lancer le processus de connexion. 

* Accédez directement à l’URL de connexion Zoho pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Zoho dans Mes applications, vous devez être connecté automatiquement à l’application ADP pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré Zoho, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
