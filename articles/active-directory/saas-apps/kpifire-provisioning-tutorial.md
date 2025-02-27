---
title: 'Tutoriel : Configurer kpifire pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner des comptes d’utilisateur à partir d’Azure AD dans kpifire.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 8c5dd093-20da-4ff6-a9b2-8071f44accd6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/23/2021
ms.author: thwimmer
ms.openlocfilehash: cb9d82afe4485cd76872b105bbae56616bad4eb0
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122207262"
---
# <a name="tutorial-configure-kpifire-for-automatic-user-provisioning"></a>Tutoriel : Configurer kpifire pour le provisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes à suivre à la fois dans kpifire et dans Azure Active Directory (Azure AD) pour configurer le provisionnement automatique d’utilisateurs. Une fois la configuration effectuée, Azure AD provisionne et déprovisionne automatiquement les utilisateurs et les groupes dans [kpifire](https://www.kpifire.com/) à l’aide du service de provisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans kpifire
> * Supprimer des utilisateurs dans kpifire quand ils n’ont plus besoin d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et kpifire
> * Provisionner des groupes et des appartenances aux groupes dans kpifire
> * [Authentification unique](kpifire-tutorial.md) auprès de kpifire (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un [locataire kpifire](https://www.kpifire.com/).
* Un compte d’utilisateur dans kpifire avec des autorisations d’administrateur.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement

1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et kpifire](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-kpifire-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer kpifire pour prendre en charge le provisionnement avec Azure AD
1. Connectez-vous à https://app.kpifire.com avec des droits d’administrateur.
1. Accédez à **Settings->API Settings->Add New Token** (Paramètres->Paramètres d’API->Ajouter un nouveau jeton) pour générer le jeton SCIM.

    [ ![Génération de jetons kpifire](media/kpifire-provisioning-tutorial/kpifire-token-generation.png) ](media/kpifire-provisioning-tutorial/kpifire-token-generation.png#lightbox)

1. Copiez et enregistrez le jeton SCIM. Vous devrez entrer cette valeur dans le champ **Jeton secret** sous l’onglet Provisionnement de votre application kpifire sur le portail Azure. 


## <a name="step-3-add-kpifire-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter kpifire à partir de la galerie d’applications Azure AD

Ajoutez kpifire à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement dans kpifire. Si vous avez déjà configuré kpifire pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quand vous attribuez des utilisateurs et des groupes à kpifire, vous devez sélectionner un autre rôle que **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible sur l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste d’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter plus de rôles. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-kpifire"></a>Étape 5. Configurer l’attribution automatique d’utilisateurs à kpifire 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans l’application kpifire, selon les assignations d’utilisateurs et de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-kpifire-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour kpifire dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **kpifire**.

    ![Lien kpifire dans la liste des applications](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

1.  Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

1. Dans la section **Informations d’identification de l’administrateur**, entrez vos informations **URL du locataire** et **Jeton secret** issues de kpifire. Sélectionnez **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à kpifire. Si la connexion échoue, vérifiez que votre compte kpifire dispose d’autorisations d’administrateur, puis réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

1. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement. Cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec kpifire**.

1. Passez en revue les attributs utilisateur synchronisés depuis Azure AD dans kpifire à la section **Mappage d’attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** servent à faire correspondre les comptes d’utilisateur dans kpifire pour les opérations de mise à jour. Si vous modifiez l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API kpifire prend en charge le filtrage des utilisateurs en fonction de cet attribut. Sélectionnez **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|


1. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec kpifire**.

1. Dans la section **Mappages des attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et kpifire. Les attributs sélectionnés comme propriétés de **Correspondance** servent à la mise en correspondance des groupes dans kpifire pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;
      |membres|Informations de référence|     

1. Pour configurer des filtres d’étendue, consultez les instructions fournies dans le [tutoriel sur les filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pour activer le service de provisionnement Azure AD pour kpifire, définissez **État du provisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs ou les groupes à provisionner dans kpifire en sélectionnant les valeurs souhaitées dans la section **Paramètres** sous **Étendue**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

1. Quand vous êtes prêt à effectuer l’approvisionnement, sélectionnez **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle initial prend plus de temps que les suivants, qui se produisent toutes les 40 minutes environ tant que le service d’approvisionnement d’Azure AD est en cours d’exécution.

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement

Après avoir configuré l’approvisionnement, utilisez les ressources suivantes pour superviser votre déploiement :

* Utilisez les [journaux de provisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer pour quels utilisateurs le provisionnement a réussi et pour quels utilisateurs il a échoué.
* Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et sa progression.
* Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour plus d’informations sur les états de quarantaine, consultez [Provisionnement d’application en état de quarantaine](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="more-resources"></a>Plus de ressources

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)