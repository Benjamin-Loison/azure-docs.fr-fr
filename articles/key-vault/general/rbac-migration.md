---
title: Migrer vers le contrôle d’accès en fonction du rôle | Microsoft Docs
description: Découvrez comment migrer des stratégies d’accès de coffre vers des rôles Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 5f92ecca8ccecda0f4ba6e7e87dc0287c75ed41b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124761616"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-permission-model"></a>Migrer d’une stratégie d’accès de coffre vers un modèle d’autorisation de type contrôle d’accès en fonction du rôle Azure

Le modèle de stratégie d’accès du coffre est un système d’autorisation existant intégré au Coffre de clés pour fournir l’accès aux clés, aux secrets et aux certificats. Vous pouvez contrôler l’accès en affectant des autorisations individuelles au principal de sécurité (utilisateur, groupe, principal de service, identité managée) à l’échelle du Coffre de clés. 

Le contrôle d’accès en fonction du rôle (RBAC Azure) est un système d’autorisation basé sur [Azure Resource Manager](../../azure-resource-manager/management/overview.md), qui permet une gestion précise des accès des ressources Azure. Avec Azure RBAC, vous contrôlez l’accès aux ressources en créant des attributions de rôles, qui se composent de trois éléments : un principal de sécurité, une définition de rôle (jeu d’autorisations prédéfini) et une étendue (groupe de ressources ou ressource individuelle). Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md)

Avant de migrer vers Azure RBAC, il est important de comprendre ses avantages et ses limitations.

Avantages principaux d’Azure RBAC par rapport aux stratégies d’accès du coffre :
- Fournit un modèle de contrôle d’accès unifié pour les ressources Azure, avec une même API à travers les services Azure
- Gestion centralisée des accès pour les administrateurs: gérez toutes les ressources Azure dans une seule vue
- Intégration à [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) pour le contrôle d’accès en fonction du temps
- Refus des affectations : possibilité d’exclure des principaux de sécurité à une étendue particulière. Pour plus d’informations, consultez [Comprendre les affectations de refus Azure](../../role-based-access-control/deny-assignments.md)

Inconvénients d’Azure RBAC :
- Latence pour les attributions de rôles : l’application de l’attribution des rôles peut prendre plusieurs minutes. Les stratégies d’accès au coffre sont immédiatement affectées.
- Nombre limité d’attributions de rôles : le RBAC Azure n’autorise que 2 000 attributions de rôles dans tous les services par abonnement, et 1 024 stratégies d’accès par Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Mappage des stratégies d’accès aux rôles Azure

Azure RBAC a plusieurs rôles intégrés Azure que vous pouvez affecter aux utilisateurs, groupes, principaux de service et identités managées. Si les rôles intégrés ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres [rôles personnalisés Azure](../../role-based-access-control/custom-roles.md).

Rôles intégrés de Key Vault pour la gestion de l’accès aux clés, certificats et secrets :
- Administrateur Key Vault
- Lecteur Key Vault
- Responsable de certificat de Key Vault
- Agent de chiffrement Key Vault
- Utilisateur de chiffrement Key Vault
- Utilisateur du service de chiffrement de Key Vault
- Agent des secrets Key Vault
- Utilisateur des secrets Key Vault

Pour plus d’informations sur les rôles intégrés existants, consultez [Rôles intégrés Azure](../../role-based-access-control/built-in-roles.md)

Les stratégies d’accès au coffre peuvent être attribuées avec des autorisations sélectionnées individuellement ou des modèles d’autorisation prédéfinis.

Modèles d’autorisation prédéfinis pour les stratégies d’accès :
- Gestion des clés, des secrets et des certificats
- Gestion des clés et des secrets
- Gestion des secrets et des certificats
- Gestion de clés
- Gestion des secrets
- Gestion de certificats
- connecteur SQL Server
- Azure Data Lake Storage ou Stockage Azure
- Sauvegarde Azure
- Clé client Exchange Online
- Clé client SharePoint Online
- Azure Information BYOK

### <a name="access-policy-templates-to-azure-roles-mapping"></a>Mappage des modèles de stratégie d’accès aux rôles Azure
| Modèle de stratégie d’accès | Operations | Rôle Azure |
| --- | --- | --- |
| Gestion des clés, des secrets et des certificats | Clés : toutes les opérations <br>Certificats : toutes les opérations<br>Secrets : toutes les opérations | Administrateur Key Vault |
| Gestion des clés et des secrets | Clés : toutes les opérations <br>Secrets : toutes les opérations| Agent de chiffrement Key Vault <br> Agent des secrets Key Vault |
| Gestion des secrets et des certificats | Certificats : toutes les opérations <br>Secrets : toutes les opérations| Agent des certificats Key Vault <br> Agent des secrets Key Vault|
| Gestion de clés | Clés : toutes les opérations| Agent de chiffrement Key Vault|
| Gestion des secrets | Secrets : toutes les opérations| Agent des secrets Key Vault|
| Gestion de certificats | Certificats : toutes les opérations | Agent des certificats Key Vault|
| connecteur SQL Server | Clés : obtenir, lister, inclure la clé, ne pas inclure la clé | Utilisateur du service de chiffrement de Key Vault|
| Azure Data Lake Storage ou Stockage Azure | Clés : obtenir, lister, désenvelopper la clé | N/A<br> Rôle personnalisé requis|
| Sauvegarde Azure | Clés : obtenir, lister, sauvegarder<br> Secrets : obtenir, lister, sauvegarder | N/A<br> Rôle personnalisé requis|
| Clé client Exchange Online | Clés : obtenir, lister, inclure la clé, ne pas inclure la clé | Utilisateur du service de chiffrement de Key Vault|
| Clé client Exchange Online | Clés : obtenir, lister, inclure la clé, ne pas inclure la clé | Utilisateur du service de chiffrement de Key Vault|
| Azure Information BYOK | Clés : obtenir, déchiffrer, signer | N/A<br>Rôle personnalisé requis|


## <a name="assignment-scopes-mapping"></a>Mappage des étendues d’affectation  

Azure RBAC pour Key Vault permet l’attribution de rôles aux étendues suivantes :
- Groupe d’administration
- Abonnement
- Resource group
- Ressource Key Vault
- Clé individuelle, secret et certificat

Le modèle d’autorisation de stratégie d’accès de coffre est limité à l’attribution de stratégies uniquement au niveau de la ressource du Coffre de clés.

En général, il est recommandé de disposer d’un coffre de clés par application et de gérer l’accès au niveau du coffre de clés. Dans certains scénarios, la gestion de l’accès sur d’autres étendues peut simplifier la gestion des accès.

- **Infrastructure, administrateurs et opérateurs de sécurité : la gestion du groupe de coffres de clés au niveau du groupe d’administration, de l’abonnement ou du groupe de ressources avec des stratégies d’accès au coffre requiert l’entretien des stratégies pour chaque coffre de clés. Azure RBAC permet de créer une attribution de rôle au niveau d’un groupe d’administration, d’un abonnement ou d’un groupe de ressources. Cette attribution s’appliquera à tous les nouveaux coffres de clés créés dans la même étendue. Dans ce scénario, il est recommandé d’utiliser Privileged Identity Management avec un accès juste-à-temps plutôt que la fourniture d’un accès permanent.
 
- **Applications : il existe des scénarios où l’application doit partager le secret avec une autre application. Avec les stratégies d’accès au coffre, un coffre de clés distinct doit être créé pour éviter d’avoir accès à tous les secrets. Azure RBAC permet d’attribuer un rôle à la clé secrète individuelle à la place à l’aide d’un coffre de clés unique.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Étapes de migration de stratégie d’accès du coffre vers Azure RBAC
Il existe de nombreuses différences entre le modèle d’autorisation Azure RBAC et la stratégie d’accès du coffre. Pour éviter les pannes au cours de la migration, il est recommandé de suivre les étapes ci-dessous.
 
1. **Identifiez et attribuez les rôles** : identifiez les rôles intégrés en fonction du tableau de mappage ci-dessus et créez des rôles personnalisés si nécessaire. Affectez des rôles aux étendues, en fonction des recommandations de mappage des étendues. Pour plus d’informations sur la façon d’attribuer des rôles à un coffre de clés, consultez [Fournir l’accès à Key Vault avec un contrôle d’accès en fonction du rôle Azure](rbac-guide.md)
1. **Validez l’attribution des rôles** : la propagation des attributions de rôles dans Azure RBAC peut prendre plusieurs minutes. Pour savoir comment vérifier les attributions de rôles, consultez [Répertorier les affectations de rôles dans l’étendue](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)
1. **Configurez la surveillance et les alertes sur le coffre de clés** : il est important d’activer la journalisation et les alertes de configuration pour les exceptions de refus d’accès. Pour plus d’informations, consultez [Surveillance et alertes Azure Key Vault](./alert.md)
1. **Définissez le modèle d’autorisation de contrôle d’accès en fonction du rôle Azure sur Key Vault** : l’activation du modèle d’autorisation Azure RBAC invalidera toutes les stratégies d’accès existantes. En cas d’erreur, le modèle d’autorisation peut être rétabli avec toutes les stratégies d’accès existantes inchangées.

> [!NOTE]
> La modification du modèle d’autorisation nécessite l’autorisation « Microsoft.Authorization/roleAssignments/write », qui fait partie des rôles [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) et [Administrateur de l'accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator). Les rôles Administrateur d’abonnement classiques tels que « Administrateur de service » et « Coadministrateur » ne sont pas pris en charge.

> [!NOTE]
> Lorsque le modèle d’autorisation RBAC Azure est activé, tous les scripts qui tentent de mettre à jour les stratégies d’accès échouent. Il est important de mettre à jour ces scripts pour utiliser Azure RBAC.

## <a name="troubleshooting"></a>Dépannage
-  Il est possible que l’attribution de rôle ne fonctionne pas au bout de quelques minutes ; il existe des situations où les attributions de rôles peuvent prendre plus de temps. Il est important d’écrire une logique de nouvelle tentative dans le code pour couvrir ces cas.
- Les attributions de rôles ont disparu lorsque Key Vault a été supprimé (suppression réversible) et récupéré. Il s’agit actuellement d’une limitation de la fonctionnalité de suppression réversible pour tous les services Azure. Il est nécessaire de recréer toutes les attributions de rôles après la récupération.    

## <a name="learn-more"></a>En savoir plus

- [Vue d’ensemble d’Azure RBAC](../../role-based-access-control/overview.md)
- [Tutoriel Rôles personnalisés](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
