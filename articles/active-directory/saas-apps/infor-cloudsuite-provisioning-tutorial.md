---
title: 'Tutoriel : Configurer Infor CloudSuite pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et déprovisionner automatiquement des comptes d’utilisateur sur Infor CloudSuite.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: thwimmer
ms.openlocfilehash: 9c488a04962216df2748891c71bad32cb9dffac6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477475"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Tutoriel : Configurer Infor CloudSuite pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans Infor CloudSuite et Azure Active Directory (Azure AD) afin de configurer Azure AD pour approvisionner et déprovisionner automatiquement des utilisateurs et/ou des groupes sur Infor CloudSuite.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* [un locataire Infor CloudSuite](https://www.infor.com/products) ;
* Un compte d’utilisateur dans Infor CloudSuite avec des privilèges Administrateur.

## <a name="assigning-users-to-infor-cloudsuite"></a>Attribution d’utilisateurs à Infor CloudSuite

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Infor CloudSuite. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs et/ou groupes à Infor CloudSuite en suivant les instructions fournies ici :
* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Conseils importants pour l’attribution d’utilisateurs à Infor CloudSuite

* Il est recommandé de n’attribuer qu’un seul utilisateur Azure AD à Infor CloudSuite afin de tester la configuration de l’approvisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous assignez un utilisateur à Infor CloudSuite, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’assignation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Configurer Infor CloudSuite pour l’approvisionnement

1. Connectez-vous à votre [console d’administration Infor CloudSuite](https://www.infor.com/customer-center). Cliquez sur l’icône utilisateur, puis accédez à **Gestion des utilisateurs**.

    ![Console d’administration Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Cliquez sur l’icône de menu dans le coin supérieur gauche de l’écran. Cliquez sur **Gérer**.

    ![Infor CloudSuite – Ajouter SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Accédez à **Comptes SCIM**.

    ![Infor CloudSuite – Compte SCIM](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Ajoutez un utilisateur administrateur en cliquant sur l’icône plus. Fournissez un **mot de passe SCIM** et entrez le même mot de passe sous **Confirmer le mot de passe**. Cliquez sur l’icône de dossier pour enregistrer le mot de passe. Vous verrez alors un **Identificateur d’utilisateur** généré pour l’utilisateur administrateur.

    ![Infor CloudSuite – Utilisateur administrateur](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite – Mot de passe](media/infor-cloudsuite-provisioning-tutorial/password.png)

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/identifier.png" alt-text="Capture d’écran de la console d’administration Infor CloudSuite montrant une ligne de table mise en évidence. Cette ligne contient un identificateur d’utilisateur, des mots de passe et un horodatage." border="false":::

5. Pour générer le jeton du porteur, copiez l’**identificateur d’utilisateur** et le **mot de passe SCIM**. Collez-les dans Notepad++ séparés par un deux-points. Encodez la valeur de chaîne en accédant à **Plug-ins > Outils MIME > Basic64 Encode**. 

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/token.png" alt-text="Capture d’écran d’un document Notepad++. Dans le menu Plugins, les outils MIME sont mis en évidence. Dans le menu d’outils MIME, l’encodage Base64 est mis en évidence." border="false":::

3.  Copiez le jeton du porteur. Vous devrez entrer cette valeur dans le champ Jeton secret dans l’onglet Approvisionnement de votre application CloudSuite dans le Portail Azure.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Ajouter Infor CloudSuite à partir de la galerie

Avant de configurer Infor CloudSuite pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Infor CloudSuite à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Infor CloudSuite à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Infor CloudSuite**, sélectionnez **Infor CloudSuite** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Infor CloudSuite dans la liste des résultats](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Configuration de l’approvisionnement automatique d’utilisateurs sur Infor CloudSuite 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Infor CloudSuite en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez aussi choisir d’activer l’authentification unique basée sur SAML pour Infor CloudSuite en suivant les instructions fournies dans le [tutoriel sur l’authentification unique pour Infor CloudSuite](./infor-cloud-suite-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

> [!NOTE]
> Pour en savoir plus sur le point de terminaison SCIM d’Infor CloudSuite, reportez-vous à ce [document](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Infor CloudSuite dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Infor CloudSuite**.

    ![Lien Infor CloudSuite dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Capture d’écran des options Gérer avec l’option Provisionnement en évidence.](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Capture d’écran de la liste déroulante Mode de provisionnement avec l’option Automatique en évidence.](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` dans **URL de locataire**. Saisissez le jeton du porteur récupéré précédemment dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Infor CloudSuite. Si la connexion échoue, vérifiez que votre compte Infor CloudSuite dispose de privilèges Administrateur et réessayez.

    ![URL de locataire + Jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Infor CloudSuite**.

    ![Infor CloudSuite – Mappages d’utilisateurs](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Infor CloudSuite. Les attributs sélectionnés en tant que propriétés de **Correspondance** servent à faire correspondre les comptes d’utilisateur dans Infor CloudSuite, en vue d’opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Infor CloudSuite – Attributs d’utilisateur](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory sur Infor CloudSuite**.

    ![Infor CloudSuite – Mappages de groupes](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et Infor CloudSuite. Les attributs sélectionnés en tant que propriétés de **Correspondance** servent à faire correspondre les groupes dans Infor CloudSuite, en vue d’opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Infor CloudSuite – Attributs de groupe](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service d’approvisionnement Azure AD pour Infor CloudSuite, définissez l’**État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou les groupes que vous souhaitez approvisionner sur Infor CloudSuite en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour superviser la progression et suivre les liens vers le rapport d’activité d’approvisionnement, qui décrit toutes les actions effectuées par le service d’approvisionnement Azure AD sur Infor CloudSuite.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)
