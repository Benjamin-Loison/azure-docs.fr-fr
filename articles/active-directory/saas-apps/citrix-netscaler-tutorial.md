---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory au connecteur Citrix ADC SAML pour Azure AD (authentification Kerberos) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique (SSO) entre Azure Active Directory et le connecteur Citrix ADC SAML pour Azure AD à l’aide d’une authentification Kerberos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2021
ms.author: jeedes
ms.openlocfilehash: 3e5c7e0634696badff8121651faffb88b2a57db5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324132"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-adc-saml-connector-for-azure-ad-kerberos-based-authentication"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory au connecteur Citrix ADC SAML pour Azure AD (authentification Kerberos)

Ce tutoriel explique comment intégrer le connecteur Citrix ADC SAML pour Azure AD à Azure Active Directory (Azure AD). Lorsque vous intégrez le connecteur Citrix ADC SAML pour Azure AD à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès au connecteur Citrix ADC SAML pour Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement au connecteur Citrix ADC SAML pour Azure AD avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement activé pour l’authentification unique du connecteur Citrix ADC SAML pour Azure AD.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. Ce tutoriel inclut les scénarios suivants :

* Authentification unique **initiée par le fournisseur de service** pour le connecteur Citrix ADC SAML pour Azure AD.

* Approvisionnement d’utilisateur **juste-à-temps** pour le connecteur Citrix ADC SAML pour Azure AD.

* [Authentification Kerberos pour le connecteur SAML ADC pour Azure AD](#publish-the-web-server).

* [Authentification basée sur l’en-tête pour le connecteur SAML ADC pour Azure AD](header-citrix-netscaler-tutorial.md#publish-the-web-server).


## <a name="add-citrix-adc-saml-connector-for-azure-ad-from-the-gallery"></a>Ajouter le connecteur Citrix ADC SAML pour Azure AD à partir de la galerie

Pour intégrer le connecteur Citrix ADC SAML pour Azure AD à Azure AD, commencez par ajouter le connecteur Citrix ADC SAML pour Azure AD à votre liste d’applications SaaS gérées à partir de la galerie :

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.

1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.

1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.

1. Dans la section **Ajouter à partir de la galerie**, entrez **Connecteur Citrix ADC SAML pour Azure AD** dans la zone de recherche.

1. Dans les résultats, sélectionnez **Connecteur Citrix ADC SAML pour Azure AD**, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-citrix-adc-saml-connector-for-azure-ad"></a>Configurer et tester l’authentification unique Azure AD pour le connecteur Citrix ADC SAML pour Azure AD

Configurez et testez l’authentification unique Azure AD avec le connecteur Citrix ADC SAML pour Azure AD à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur du connecteur Citrix ADC SAML pour Azure AD associé.

Pour configurer et tester l’authentification unique Azure AD avec le connecteur Citrix ADC SAML pour Azure AD, effectuez les étapes suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-sso) : pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.

    1. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) : pour tester l’authentification unique Azure AD avec B.Simon.

    1. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) : pour permettre à B.Simon d’utiliser l’authentification unique Azure AD.

1. [Configurer l’authentification unique pour le connecteur Citrix ADC SAML pour Azure AD](#configure-citrix-adc-saml-connector-for-azure-ad-sso) pour configurer les paramètres d’authentification unique côté application.

    1. [Créer un utilisateur de test pour le connecteur Citrix ADC SAML pour Azure AD](#create-citrix-adc-saml-connector-for-azure-ad-test-user). Pour avoir un équivalent de B.Simon dans le connecteur Citrix ADC SAML pour Azure AD lié à la représentation de l’utilisateur dans Azure AD.

1. [Tester l’authentification unique](#test-sso) : pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Pour activer l’authentification unique Azure AD à l’aide du portail Azure, effectuez les étapes suivantes :

1. Dans le portail Azure, dans la page d’intégration de l’application **Connecteur Citrix ADC SAML pour Azure AD**, sous **Gérer**, sélectionnez **Authentification unique**.

1. Dans le volet **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.

1. Dans le volet **Configurer l’authentification unique avec SAML**, sélectionnez l’icône de crayon pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, pour configurer l’application en mode **lancé par le fournisseur d’identité**, effectuez les étapes suivantes :

    1. Dans la zone de texte **Identificateur**, entrez une URL au format suivant : `https://<YOUR_FQDN>`

    1. Dans la zone de texte **URL de réponse**, entrez une URL au format suivant : `http(s)://<YOUR_FQDN>.of.vserver/cgi/samlauth`

1. Pour configurer l’application en mode **lancé par le fournisseur de services**, sélectionnez **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    * Dans la zone **URL de connexion**, entrez une URL au format suivant : `https://<YOUR_FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * Les URL utilisées dans cette section ne sont pas des valeurs réelles. Mettez à jour ces valeurs avec les valeurs réelles de l’identificateur, de l’URL de réponse et de l’URL de connexion. Pour obtenir ces valeurs, contactez [l’équipe de support technique du connecteur Citrix ADC SAML pour Azure AD](https://www.citrix.com/contact/technical-support.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.
    > * Pour configurer l’authentification unique, les URL doivent être accessibles à partir de sites web publics. Vous devez activer le pare-feu ou d’autres paramètres de sécurité côté connecteur Citrix ADC SAML pour Azure AD pour permettre à Azure AD de poster le jeton sur l’URL configurée.

1. Dans le volet **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **URL des métadonnées de fédération d’application**, copiez cette URL et enregistrez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer le connecteur Citrix ADC SAML pour Azure AD**, copiez la ou les URL pertinentes en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon sur le portail Azure.

1. Dans le menu gauche du portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.

1. Sélectionnez **Nouvel utilisateur** en haut du volet.

1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :

   1. Pour **Nom**, entrez `B.Simon`.  

   1. Entrez _username@companydomain.extension_ comme **Nom d’utilisateur**. Par exemple : `B.Simon@contoso.com`.

   1. Cochez la case **Afficher le mot de passe**, puis notez ou copiez la valeur affichée dans le champ **Mot de passe**.

   1. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser l’utilisateur B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès au connecteur Citrix ADC SAML pour Azure AD.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

1. Dans la liste d’applications, sélectionnez **Connecteur Citrix ADC SAML pour Azure AD**.

1. Dans la vue d’ensemble de l’application, sous **Gérer**, sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B.Simon** dans la liste **Utilisateurs**. Choisissez **Select**.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.

## <a name="configure-citrix-adc-saml-connector-for-azure-ad-sso"></a>Configurer l’authentification unique pour le connecteur Citrix ADC SAML pour Azure AD

Sélectionnez un lien pour connaître les étapes relatives au type d’authentification que vous voulez configurer :

- [Configurer Citrix ADC SAML Connector pour Azure AD SSO pour l’authentification Kerberos](#publish-the-web-server)

- [Configurer le connecteur Citrix SAML ADC pour Azure AD SSO pour l’authentification basée sur l’en-tête](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Publier le serveur web 

Pour créer un serveur virtuel :

1. Sélectionnez **Gestion du trafic** > **Équilibrage de charge** > **Services**.
    
1. Sélectionnez **Ajouter**.

    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Services](./media/citrix-netscaler-tutorial/web01.png)

1. Définissez les valeurs suivantes pour le serveur web qui exécute les applications :

   * **Nom du service** :
   * **Adresse IP du serveur/ Serveur existant**
   * **Protocole**
   * **Port**

### <a name="configure-the-load-balancer"></a>Configurer l’équilibrage de charge

Pour configurer l’équilibrage de charge :

1. Accédez à **Gestion de trafic** > **Équilibrage de charge** > **Serveurs virtuels**.

1. Sélectionnez **Ajouter**.

1. Définissez les valeurs suivantes comme décrit dans la capture d’écran ci-après :

    * **Nom**
    * **Protocole**
    * **Adresse IP**
    * **Port**

1. Sélectionnez **OK**.

    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Paramètres de base](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Lier le serveur virtuel

Pour lier l’équilibreur de charge au serveur virtuel :

1. Dans le volet **Services et groupes de services**, sélectionnez **Aucune liaison de service de serveur virtuel d’équilibrage de charge**.

   ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Liaison de service de serveur virtuel d’équilibrage de charge](./media/citrix-netscaler-tutorial/bind01.png)

1. Vérifiez les paramètres comme indiqué dans la capture d’écran suivante, puis sélectionnez **Fermer**.

   ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Vérifier la liaison des services de serveur virtuel](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Lier le certificat

Pour publier ce service en tant que TLS, liez le certificat serveur, puis testez votre application :

1. Sous **Certificat**, sélectionnez **Aucun certificat de serveur**.

   ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Certificat de serveur](./media/citrix-netscaler-tutorial/bind03.png)

1. Vérifiez les paramètres comme indiqué dans la capture d’écran suivante, puis sélectionnez **Fermer**.

   ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Vérifier le certificat](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-connector-for-azure-ad-saml-profile"></a>Profil SAML du connecteur Citrix ADC SAML pour Azure AD

Pour configurer le profil SAML du connecteur Citrix ADC SAML pour Azure AD, effectuez les étapes des sections suivantes.

### <a name="create-an-authentication-policy"></a>Créer une stratégie d’authentification

Pour créer une stratégie d’authentification :

1. Accédez à **Sécurité** > **AAA - Trafic d’application** > **Stratégies** > **Authentification** > **Stratégies d’authentification**.

1. Sélectionnez **Ajouter**.

1. Dans le volet **Créer une stratégie d’authentification**, entrez ou sélectionnez les valeurs suivantes :

    * **Name** : Entrez un nom pour votre stratégie d’authentification.
    * **Action** : Entrez **SAML**, puis sélectionnez **Ajouter**.
    * **Expression** :  Entrez **true**.     
    
    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Créer une politique d’authentification](./media/citrix-netscaler-tutorial/policy01.png)

1. Sélectionnez **Create** (Créer).

### <a name="create-an-authentication-saml-server"></a>Créer un serveur SAML d’authentification

Pour créer un serveur SAML d’authentification, accédez au volet **Créer un serveur SAML d’authentification**, puis effectuez les étapes suivantes :

1. Dans **Nom**, entrez le nom du serveur SAML d’authentification.

1. Sous **Exporter les métadonnées SAML** :

   1. Cochez la case **Importer les métadonnées**.

   1. Entrez l’URL des métadonnées de fédération à partir de l’interface utilisateur SAML Azure que vous avez copiée précédemment.
    
1. Dans **Nom de l’émetteur**, entrez l’URL pertinente.

1. Sélectionnez **Create** (Créer).

![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Créer un serveur d’authentification SAML](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Créer un serveur virtuel d’authentification

Pour créer un serveur virtuel d’authentification :

1.  Accédez à **Sécurité** > **AAA - Trafic d’application** > **Stratégies** > **Authentification** > **Serveurs virtuels d’authentification**.

1.  Sélectionnez **Ajouter**, puis effectuez les étapes suivantes :

    1. Dans **Nom**, entrez le nom du serveur virtuel d’authentification.

    1. Cochez la case **Non adressable**.

    1. Dans **Protocole**, sélectionnez **SSL**.

    1. Sélectionnez **OK**.
    
1. Sélectionnez **Continuer**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Configurer le serveur virtuel d’authentification pour utiliser Azure AD

Modifiez deux sections pour le serveur virtuel d’authentification :

1.  Dans le volet **Stratégies d’authentification avancées**, sélectionnez **Aucune stratégie d’authentification**.

    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Stratégies d’authentification avancées](./media/citrix-netscaler-tutorial/virtual01.png)

1. Dans le volet **Liaison des stratégies**, sélectionnez la stratégie d’authentification, puis sélectionnez **Lier**.

    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Liaison de la stratégie](./media/citrix-netscaler-tutorial/virtual02.png)

1. Dans le volet **Serveurs virtuels basés sur un formulaire**, sélectionnez **Aucun serveur virtuel d’équilibrage de charge**.

    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Serveurs virtuels basés sur des formulaires](./media/citrix-netscaler-tutorial/virtual03.png)

1. Dans **Nom de domaine complet d’authentification**, entrez un nom de domaine complet (FQDN) (obligatoire).

1. Sélectionnez le serveur virtuel d’équilibrage de charge à protéger avec l’authentification Azure AD.

1. Sélectionnez **Lier**.

    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Liaison de serveur virtuel d’équilibrage de charge](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Veillez à sélectionner **Terminé** dans le volet **Configuration du serveur virtuel d’authentification**.

1. Pour vérifier vos modifications, dans un navigateur, accédez à l’URL de l’application. Votre page de connexion de locataire doit s’afficher au lieu de l’accès non authentifié que vous auriez pu voir précédemment.

    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Une page de connexion dans un navigateur web](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-adc-saml-connector-for-azure-ad-sso-for-kerberos-based-authentication"></a>Configurer Citrix ADC SAML Connector pour Azure AD SSO pour l’authentification Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc-saml-connector-for-azure-ad"></a>Créer un compte de délégation Kerberos pour le connecteur Citrix ADC SAML pour Azure AD

1. Créez un compte d’utilisateur (dans cet exemple, nous utilisons _AppDelegation_).

    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Propriétés](./media/citrix-netscaler-tutorial/kerberos01.png)

1. Configurez un nom de principal du service (SPN) hôte pour ce compte. 

    Exemple : `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    Dans cet exemple :

    * `IDENTT.WORK` est le nom de domaine complet du domaine.
    * `identt` est le nom NetBIOS du domaine.
    * `appdelegation` est le nom du compte d’utilisateur de délégation.

1. Configurez la délégation pour le serveur web comme indiqué dans la capture d’écran suivante :
 
    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Délégation dans le volet Propriétés](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > Dans l’exemple de capture d’écran, le nom du serveur w&eb interne exécutant le site d’authentification intégrée Windows est _CWEB2_.

### <a name="citrix-adc-saml-connector-for-azure-ad-aaa-kcd-kerberos-delegation-accounts"></a>AAA KCD (comptes de délégation Kerberos) du connecteur Citrix ADC SAML pour Azure AD

Pour configurer les comptes AAA KCD du connecteur Citrix ADC SAML pour Azure AD :

1.  Accédez à **Passerelle Citrix** > **Comptes AAA KCD (délégation contrainte Kerberos)** .

1.  Sélectionnez **Ajouter**, puis entrez ou sélectionnez les valeurs suivantes :

    * **Name** : Entrez un nom pour le compte KCD.

    * **Realm** (Domaine) : Entrez le domaine et l’extension en majuscules.

    * **Service SPN** (Nom de principal du service) : `http/<host/fqdn>@<DOMAIN.COM>`.
    
        > [!NOTE]
        > `@DOMAIN.COM` est obligatoire et doit être en majuscules. Exemple : `http/cweb2@IDENTT.WORK`.

    * **Utilisateur délégué** : Entrez le nom d’utilisateur délégué.

    * Cochez la case **Mot de passe de l’utilisateur délégué**, puis entrez et confirmez un mot de passe.

1. Sélectionnez **OK**.
 
    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Configurer les comptes KCD](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Stratégie de trafic Citrix et profil de trafic

Pour configurer la stratégie de trafic Citrix et le profil de trafic :

1.  Accédez à **Securité** > **AAA - Trafic d’application** > **Stratégies** > **Stratégies de trafic, profils de trafic et profils d’authentification unique par formulaire**.

1.  Sélectionnez **Traffic Profiles** (Profils de trafic).

1.  Sélectionnez **Ajouter**.

1.  Pour configurer un profil de trafic, entrez ou sélectionnez les valeurs suivantes.

    * **Name** : Entrez un nom pour le profil de trafic.

    * **Authentification unique** : Sélectionnez **ACTIVÉ**.

    * **Compte KCD** : Sélectionnez le compte KCD que vous avez créée dans la section précédente.

1. Sélectionnez **OK**.

    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Configurer le profil de trafic](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  Sélectionnez **Traffic Policy** (Stratégie de trafic).

1.  Sélectionnez **Ajouter**.

1.  Pour configurer une stratégie de trafic, entrez ou sélectionnez les valeurs suivantes :

    * **Name** : Entrez un nom pour la stratégie de trafic.

    * **Profil** : Sélectionnez le profil de trafic que vous avez créé dans la section précédente.

    * **Expression** : Entrez **true**.

1. Sélectionnez **OK**.

    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Configurer la stratégie de trafic](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Lier une stratégie de trafic à un serveur virtuel dans Citrix

Pour lier une stratégie de trafic à un serveur virtuel à l’aide de l’interface graphique utilisateur :

1. Accédez à **Gestion de trafic** > **Équilibrage de charge** > **Serveurs virtuels**.

1. Dans la liste des serveurs virtuels, sélectionnez le serveur virtuel auquel lier la stratégie de réécriture, puis sélectionnez **Ouvrir**.

1. Dans le volet **Serveur virtuel d’équilibrage de charge**, sous **Paramètres avancés**, sélectionnez **Stratégies**. Toutes les stratégies configurées pour votre instance de NetScaler figurent dans la liste.
 
    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Serveur virtuel d’équilibrage de charge](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Boîte de dialogue Stratégies](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Cochez la case située en regard du nom de la stratégie que vous voulez lier à ce serveur virtuel.
 
    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Liaison de stratégie de trafic de serveur virtuel d’équilibrage de charge](./media/citrix-netscaler-tutorial/kerberos09.png)

1. Dans la boîte de dialogue **Choisir un type** :

    1. Pour **Choisir une stratégie**, sélectionnez **Trafic**.

    1. Pour **Choisir un type**, sélectionnez **Requête**.

    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Choisir un type](./media/citrix-netscaler-tutorial/kerberos08.png)

1. Quand la stratégie est liée, sélectionnez **Terminé**.
 
    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Volet Stratégies](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Testez la liaison à l’aide du site web d’authentification intégrée Windows.

    ![Configuration du connecteur Citrix ADC SAML pour Azure AD - Une page de test dans un navigateur web](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-citrix-adc-saml-connector-for-azure-ad-test-user"></a>Créer un utilisateur de test pour le connecteur Citrix ADC SAML pour Azure AD

Dans cette section, un utilisateur appelé B.Simon est créé dans le connecteur Citrix ADC SAML pour Azure AD. Le connecteur Citrix ADC SAML pour Azure AD prend en charge le provisionnement d’utilisateurs juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans le connecteur Citrix ADC SAML pour Azure AD, un utilisateur est créé après l’authentification.

> [!NOTE]
> Si vous avez besoin de créer un utilisateur manuellement, contactez l’[équipe du support technique du connecteur Citrix ADC SAML pour Azure AD](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion au connecteur Citrix ADC SAML pour Azure AD à partir de laquelle vous pouvez lancer le flux de connexion. 

* Accédez directement à l’URL de connexion au connecteur Citrix ADC SAML pour Azure AD pour initier le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Lorsque vous cliquez sur la vignette du connecteur Citrix ADC SAML pour Azure AD dans Mes applications, cette opération redirige vers l’URL de connexion du connecteur Citrix ADC SAML pour Azure AD. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré le connecteur Citrix ADC SAML pour Azure AD, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
