---
title: 'Tutoriel : Configurer Apple School Manager pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateur entre Azure AD et Apple School Manager.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: f006c177-7b35-4af1-84f2-db4a4e2bf96a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: thwimmer
ms.openlocfilehash: 5031da3751d596228a40a354b527b718920b86e5
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113764945"
---
# <a name="tutorial-configure-apple-school-manager-for-automatic-user-provisioning"></a>Tutoriel : Configurer Apple School Manager pour le provisionnement automatique d’utilisateurs



Ce tutoriel décrit les étapes à suivre dans Apple School Manager et Azure Active Directory (Azure AD) pour configurer le provisionnement automatique d’utilisateurs. Une fois configuré, Azure AD provisionne et déprovisionne automatiquement les utilisateurs pour [Apple School Manager](https://school.apple.com/) à l’aide du service de provisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Apple School Manager
> * Supprimer des utilisateurs dans Apple School Manager lorsqu’ils ne nécessitent plus d’accès
> * Conserver des attributs utilisateur spécifiques synchronisés entre Azure AD et Apple School Manager

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer le provisionnement (par exemple Administrateur d’application, Administrateur d’application cloud, Propriétaire d’application ou Administrateur général) 
* Un compte Apple School Manager avec le rôle Administrator, Site Manager ou People Manager

> [!NOTE]
> Le transfert de jetons vers Azure AD et l’établissement d’une connexion doivent être effectués dans les quatre jours civils, sinon le processus doit être redémarré.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et Apple School Manager](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-apple-school-manager-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Apple School Manager pour prendre en charge le provisionnement avec Azure AD

1. Dans Apple School Manager, connectez-vous avec un compte qui a le rôle Administrator, Site Manager ou People Manager.
2. Cliquez sur Settings en bas de l’encadré, cliquez sur Data Source sous Organization Settings, puis sur Connect to Data Source.
3. Cliquez sur Connect en regard de SCIM et lisez attentivement l’avertissement. Cliquez sur Copy, puis sur Close.
[La fenêtre Connect to SCIM, qui fournit un jeton et un bouton de copie en dessous.] Laissez cette fenêtre ouverte pour copier l’URL du locataire d’Apple Business Manager vers Azure AD, à savoir : « https://federation.apple.com/feeds/school/scim  »

    ![Apple School Manager](media/appleschoolmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> Le jeton secret ne doit pas être partagé avec une personne autre que l’administrateur Azure AD.

## <a name="step-3-add-apple-school-manager-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Apple School Manager à partir de la galerie d’applications Azure AD

Ajoutez Apple School Manager à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement dans Apple School Manager. Si vous avez déjà configuré Apple School Manager pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quand vous attribuez des utilisateurs à Apple School Manager, vous devez sélectionner un rôle différent du rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-school-manager"></a>Étape 5. Configurer le provisionnement automatique d’utilisateurs dans Apple School Manager

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Apple School Manager**.

    ![Apple School Manager dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, entrez les valeurs d’**URL de base SCIM 2.0 et de jeton d’accès** récupérées à partir d’Apple School Manager respectivement dans **URL de locataire** et **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Apple School Manager. Si la connexion échoue, vérifiez que votre compte Apple School Manager dispose des autorisations d’administrateur, puis réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Si la connexion réussit, Apple School Manager indique que la connexion SCIM est active. Ce processus peut prendre jusqu’à 60 secondes pour qu’Apple School Manager reflète l’état de la connexion la plus récente.

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Apple School Manager**.

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Apple School Manager. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour la mise en correspondance des comptes d’utilisateur dans Apple School Manager dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|
   |---|---|
   |active|Boolean|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.givenName|String|
   |externalId|String|
   |locale|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service de provisionnement Azure AD pour Apple School Manager, définissez le paramètre **État du provisionnement** sur **Activé** dans la section Paramètres.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs et/ou groupes que vous souhaitez provisionner sur Apple School Manager en choisissant les valeurs souhaitées dans le champ **Étendue** de la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

13. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution.

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement

Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

1. Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
2. Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et quand il se termine
3. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
* [Examiner la configuration requise de SCIM pour Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdd88331cd6)
* [Utilisation d’un ID de personne dans Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd69e1e48e9)
* [Utiliser SCIM pour importer des utilisateurs dans Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd3ec7b95ad)
* [Résoudre les conflits de compte d’utilisateur SCIM dans Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd313013d12)
* [Supprimer des comptes Azure AD visibles dans Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdaa5798fbe)
* [Afficher l’activité SCIM dans Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd1bfd8dfde)
* [Gérer les connexions et les jetons SCIM existants dans Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdc9a823611)
* [Rompre la connexion SCIM dans Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd609be3a61)
* [Résolution des problèmes de connexion SCIM dans Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd403a0f3bd)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)