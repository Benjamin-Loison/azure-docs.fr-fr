---
title: 'Didacticiel : Intégration d’Azure Active Directory à PerformanceCentre | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et PerformanceCentre.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 88c7141d446d03a5e7b2926826af109768ec6409
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124826149"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Didacticiel : Intégration d’Azure Active Directory à PerformanceCentre

Dans ce didacticiel, vous apprenez à intégrer PerformanceCentre à Azure Active Directory (Azure AD).
L’intégration de PerformanceCentre dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à PerformanceCentre.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à PerformanceCentre (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour configurer l’intégration d’Azure AD avec PerformanceCentre, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement PerformanceCentre pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* PerformanceCentre prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="adding-performancecentre-from-the-gallery"></a>Ajout de PerformanceCentre à partir de la galerie

Pour configurer l’intégration de PerformanceCentre avec Azure AD, vous devez ajouter PerformanceCentre à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter PerformanceCentre à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **PerformanceCentre**, sélectionnez **PerformanceCentre** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![PerformanceCentre dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec PerformanceCentre au moyen d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur PerformanceCentre associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec PerformanceCentre, vous avez besoin de suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique PerformanceCentre](#configure-performancecentre-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test PerformanceCentre](#create-performancecentre-test-user)** pour avoir dans PerformanceCentre un équivalent de Britta Simon lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec PerformanceCentre, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **PerformanceCentre**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL PerformanceCentre](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `http://<companyname>.performancecentre.com/saml/SSO`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `http://<companyname>.performancecentre.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique de PerformanceCentre](https://www.performio.co/contact-us). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer PerformanceCentre**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-performancecentre-single-sign-on"></a>Configurer l’authentification unique PerformanceCentre

1. Connectez-vous à votre site d’entreprise **PerformanceCentre** en tant qu’administrateur.

2. Dans l’onglet sur le côté gauche, cliquez sur **Configure**.
   
    ![Capture d’écran montrant le menu « PerformanceCenter » avec l’élément « Configure » sélectionné.][10]

3. Dans l’onglet sur le côté gauche, cliquez sur **Miscellaneous**, puis cliquez sur **Single Sign On**.
   
    ![Capture d’écran montrant l’onglet « Configure » avec l’élément « Single Sign-On » sélectionné dans le menu « Miscellaneous ».][11]

4. Pour **Protocol**, sélectionnez **SAML**.
   
    ![Capture d’écran montrant la section « Single Sign-On Configuration » avec l’élément « SAML » sélectionné dans le menu « Protocol ».][12]

5. Ouvrez votre fichier de métadonnées téléchargé dans le Bloc-notes, copiez son contenu, collez-le dans la zone de texte **Identity Provider Metadata**, puis cliquez sur **Save**.
   
    ![Capture d’écran montrant la zone de texte « Identity Provider Metadata ».][13]

6. Vérifiez que les valeurs **Entity Base URL** et **Entity ID URL** sont correctes.
    
     ![Authentification unique Azure AD][14]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple : BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à PerformanceCentre.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **PerformanceCentre**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **PerformanceCentre**.

    ![Lien PerformanceCentre dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-performancecentre-test-user"></a>Créer un utilisateur de test PerformanceCentre

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans PerformanceCentre.

**Pour créer un utilisateur appelé Britta Simon dans PerformanceCentre, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise PerformanceCentre en tant qu’administrateur.

2. Dans le menu de gauche, cliquez sur **Interrelate**, puis cliquez sur **Create Participant**.
   
    ![Capture d’écran montrant la page « Interrelate - Participants » du site d’entreprise « PerformanceCenter » avec le bouton « Create Participant » sélectionné.][400]

3. Dans la boîte de dialogue **Interrelate - Create Participant** , effectuez les opérations suivantes :
   
    ![Create User][401]
    
    a. Entrez les attributs requis pour Britta Simon dans les zones de texte correspondantes.
    
    >[!IMPORTANT]
    >L’attribut de nom d’utilisateur de Britta dans PerformanceCentre doit être le même que le nom d’utilisateur dans Azure AD.
    
    b. Sélectionnez **Client Administrator** pour **Choose Role**.
    
    c. Cliquez sur **Enregistrer**. 

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette PerformanceCentre dans le volet d’accès doit vous connecter automatiquement à l’application PerformanceCentre pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](./tutorial-list.md)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](../conditional-access/overview.md)

<!--Image references-->

[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png