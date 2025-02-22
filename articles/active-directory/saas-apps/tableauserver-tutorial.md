---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Tableau Server | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Tableau Server.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 60b8374134c80c35c2d5bd2bd4f8178e9ab9c50e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333178"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Tableau Server

Dans ce tutoriel, vous allez apprendre à intégrer Tableau Server à Azure Active Directory (Azure AD). Quand vous intégrez Tableau Server à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Tableau Server.
* Permettre à vos utilisateurs de se connecter automatiquement à Tableau Server avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.


## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Tableau Server pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Tableau Server prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="add-tableau-server-from-the-gallery"></a>Ajouter Tableau Server à partir de la galerie

Pour configurer l’intégration de Tableau Server à Azure AD, vous devez ajouter Tableau Server, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Tableau Server** dans la zone de recherche.
1. Sélectionnez **Tableau Server** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-tableau-server"></a>Configurer et tester l’authentification unique Azure AD pour Tableau Server

Configurez et testez l’authentification unique Azure AD avec Tableau Server à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Tableau Server associé.

Pour configurer et tester l’authentification unique Azure AD avec Tableau Server, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Tableau Server](#configure-tableau-server-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Tableau Server](#create-tableau-server-test-user)** pour avoir un équivalent de B.Simon dans Tableau Server lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Tableau Server**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://azure.<domain name>.link`.

    b. Dans la zone de texte **Identificateur**, tapez une URL en utilisant le format suivant : `https://azure.<domain name>.link`

    c. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Les valeurs ci-dessus ne sont pas des valeurs réelles. Mettez à jour les valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels à partir de la page de configuration de Tableau Server, conformément aux explications données plus loin dans le tutoriel.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Tableau Server**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à Tableau Server.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Tableau Server**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-tableau-server-sso"></a>Configurer l’authentification unique Tableau Server

1. Pour que l’authentification unique soit configurée pour votre application, vous devez vous connecter à votre locataire Tableau Server comme administrateur.

2. Sur l’onglet **CONFIGURATION**, sélectionnez **User Identity & Access** (Identité utilisateur et accès), puis l’onglet de la méthode d’**authentification**.

    ![Capture d’écran montrant l’élément Authentication sélectionné dans User Identity & Access.](./media/tableauserver-tutorial/auth.png)

3. Dans la page **CONFIGURATION**, effectuez les étapes suivantes :

    ![Capture d’écran montrant la page Configuration dans laquelle vous pouvez indiquer les valeurs décrites.](./media/tableauserver-tutorial/config.png)

    a. Pour **Méthode d’authentification**, sélectionnez SAML.

    b. Cochez la case **Enable SAML Authentication for the server** (Activer l’authentification SAML pour le serveur).

    c. Tableau Server return URL (URL de retour Tableau Server) : URL à laquelle accèdent les utilisateurs Tableau Server, telle que `http://tableau_server`. L’utilisation de `http://localhost` n’est pas recommandée. L’utilisation d’une URL avec une barre oblique finale (par exemple, `http://tableau_server/`) n’est pas prise en charge. Copiez l’**URL de retour de Tableau Server** et collez-la dans la zone de texte **URL de connexion** située dans la section **Configuration SAML de base** dans le portail Azure.

    d. SAML entity ID (ID d’entité SAML) : l’ID d’entité identifie de façon unique votre installation Tableau Server auprès du fournisseur d’identité. Vous pouvez à nouveau entrer l’URL Tableau Server ici, si vous le souhaitez, mais ce n’est pas obligatoire. Copiez l’**ID d’entité SAML** et collez-la dans la zone de texte **Identificateur** située dans la section **Configuration SAML de base** dans le portail Azure.

    e. Cliquez sur **Download XML Metadata File** (Télécharger le fichier de métadonnées XML) et ouvrez-le dans l’application de l’éditeur de texte. Recherchez l’URL Assertion Consumer Service avec HTTP POST et Index 0, puis copiez l’URL. Maintenant, collez-la dans la zone de texte **URL de réponse** située dans la section **Configuration SAML de base** dans le portail Azure.

    f. Localisez votre fichier de métadonnées de fédération téléchargé à partir du portail Azure, puis chargez-le dans le **fichier de métadonnées du fournisseur d’identité SAML**.

    g. Entrez les noms des attributs que le fournisseur d’identité utilise pour accueillir les noms d’utilisateur, noms d’affichage et adresses e-mail.

    h. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Le client doit charger un fichier de certificat x509 encodé au format PEM avec une extension .crt et un fichier de clé privée RSA ou DSA avec l’extension .key, en tant que fichier de clé de certificat. Pour plus d’informations sur le fichier de certificat et le fichier de clé de certificat, consultez [ce document](https://help.tableau.com/current/server/en-us/saml_requ.htm). Si vous avez besoin d’aide pour la configuration de SAML dans Tableau Server, consultez l’article [Configurer SAML à l’échelle du serveur](https://help.tableau.com/current/server/en-us/config_saml.htm).

### <a name="create-tableau-server-test-user"></a>Créer un utilisateur de test Tableau Server

L’objectif de cette section est de créer un utilisateur appelé B.Simon dans Tableau Server. Vous devez approvisionner tous les utilisateurs dans Tableau Server.

Le nom de l’utilisateur doit correspondre à la valeur que vous avez configurée dans l’attribut personnalisé Azure AD **username**. Avec le mappage correct, l’intégration doit prendre en charge Configuration de l’authentification unique Azure AD.

> [!NOTE]
> Si vous avez besoin de créer un utilisateur manuellement, vous devez contacter l’administrateur Tableau Server de votre organisation.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion Tableau Server, à partir de laquelle vous pouvez démarrer le flux de connexion. 

* Accédez directement à l’URL de connexion Tableau Server pour lancer le processus de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette Tableau Server dans Mes applications, vous êtes redirigé vers l’URL de connexion Tableau Server. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Tableau Server, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
