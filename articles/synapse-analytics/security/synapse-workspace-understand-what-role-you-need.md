---
title: Comprendre les rôles requis pour effectuer des tâches courantes dans Azure Synapse
description: Cet article décrit le(s) rôle(s) RBAC Synapse intégré(s) requis pour accomplir des tâches spécifiques
author: meenalsri
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/02/2021
ms.author: mesrivas
ms.reviewer: wiassaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 376c91c93c61f3070c5b81a24fd8ae145bdee6ad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018828"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-azure-synapse"></a>Comprendre les rôles requis pour effectuer des tâches courantes dans Azure Synapse

Cet article vous aidera à comprendre les différents rôles de type de contrôle d’accès en fonction du rôle (RBAC) Azure nécessaires pour effectuer vos tâches dans Synapse Studio.  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>Résumé du contrôle d’accès et du flux de travail Synapse Studio 

### <a name="access-synapse-studio"></a>Accéder à Synapse Studio

Vous pouvez ouvrir Synapse Studio et afficher les détails de l’espace de travail, et répertorier l’une de ses ressources Azure (pools SQL, pools Spark ou runtimes d’intégration) si un rôle RBAC Synapse vous a été attribué ou si vous disposez du rôle Propriétaire, Contributeur ou Lecteur Azure sur l’espace de travail.

### <a name="resource-management"></a>Gestion des ressources

Vous pouvez créer des pools SQL, des pools Data Explorer et des runtimes d’intégration, si vous avez le statut de Propriétaire ou de Contributeur Azure sur l’espace de travail. Lorsque vous utilisez des modèles ARM pour un déploiement automatisé, vous devez être un contributeur Azure sur le groupe de ressources.

Vous pouvez suspendre ou mettre à l’échelle un pool SQL dédié, configurer un pool Spark ou un runtime d’intégration si vous êtes un Propriétaire ou un Contributeur Azure sur l’espace de travail ou cette ressource.

### <a name="view-and-edit-code-artifacts"></a>Afficher et modifier les artefacts de code

L’accès à Synapse Studio vous permet de créer des artefacts de code, par exemple des scripts SQL, des scripts KQL, des notebooks, des travaux Spark, des services liés, des pipelines, des flux de données, des déclencheurs et des informations d’identification.  (Ces artefacts peuvent être publiés ou enregistrés avec des autorisations supplémentaires.)  

Si vous êtes un Utilisateur d’artefact Synapse, un Éditeur d’artefact Synapse, un Contributeur Synapse ou un Administrateur Synapse, vous pouvez répertorier, ouvrir et modifier des artefacts de code déjà publiés.

### <a name="execute-your-code"></a>Exécuter votre code

Vous pouvez exécuter des scripts SQL sur des pools SQL si vous avez défini les autorisations SQL nécessaires dans les pools SQL. Vous pouvez exécuter des scripts KQL sur des pools Data Explorer si vous disposez des autorisations nécessaires.  

Vous pouvez exécuter des notebooks et des travaux Spark si vous disposez d’autorisations de type Opérateur de capacité de calcul Synapse sur l’espace de travail ou sur des pools Apache Spark spécifiques.  

Avec les autorisations Opérateur de capacité de calcul sur l’espace de travail ou des runtimes d’intégration spécifiques, ainsi que les autorisations d’informations d’identification appropriées, vous pouvez exécuter des pipelines.

### <a name="monitor-and-manage-execution"></a>Surveiller et gérer l’exécution

Vous pouvez consulter l’état des notebooks et des travaux en cours d’exécution dans les pools Apache Spark si vous êtes un utilisateur Synapse.

Vous pouvez consulter les journaux et annuler les travaux et pipelines en cours d’exécution si vous êtes un opérateur de capacité de calcul Synapse sur l’espace de travail, ou pour un pool ou un pipeline Spark spécifique.  

### <a name="publish-and-save-your-code"></a>Publier et enregistrer votre code

Vous pouvez publier des artefacts de code nouveaux ou mis à jour sur le service si vous êtes un Éditeur d’artefact Synapse, un Contributeur Synapse ou un Administrateur Synapse. 

Vous pouvez valider des artefacts de code dans une branche de travail d’un référentiel Git si l’espace de travail est compatible Git et que vous disposez d’autorisations Git. Si Git est activé, la publication est uniquement autorisée à partir de la branche de collaboration.

Si vous fermez Synapse Studio sans publier ni valider les modifications apportées aux artefacts de code, ces modifications seront perdues.


## <a name="tasks-and-required-roles"></a>Tâches et rôles requis

Le tableau ci-dessous répertorie les tâches courantes et, pour chaque tâche, le RBAC Synapse ou les rôles RBAC Azure requis.  

>[!Note]
> L’administrateur Synapse n’est pas indiqué pour chaque tâche, sauf s’il s’agit du seul rôle qui fournit l’autorisation nécessaire. Un administrateur Synapse peut effectuer toutes les tâches activées par d’autres rôles RBAC Synapse.</br>

Le rôle RBAC Synapse minimal requis est indiqué. 

Tous les rôles RBAC Synapse dans toutes les étendues fournissent des autorisations de type Utilisateur Synapse pour l’espace de travail.

Toutes les autorisations/actions RBAC Synapse présentées dans le tableau sont préfixées avec `Microsoft/Synapse/workspaces/...`.


Tâche (je souhaite...) |Rôle (je dois être...)|Autorisation/action RBAC Synapse
--|--|--
|Ouvrir Synapse Studio sur un espace de travail|Utilisateur Synapse, ou|lire
| |Propriétaire, Contributeur ou Lecteur Azure sur l’espace de travail|aucun
|Répertorier des pools SQL, des pools Data Explorer, des runtimes d’intégration et accéder à leurs détails de configuration|Utilisateur Synapse, ou|lire|
||Propriétaire, Contributeur ou Lecteur Azure sur l’espace de travail|aucun
|Répertorier les services liés, les informations d’identification, les points de terminaison privés managés|Utilisateur Synapse|lire
POOLS SQL|
Créer un pool SQL dédié ou un pool SQL serverless|Propriétaire ou Contributeur Azure sur l’espace de travail|aucun
Gérer (suspendre, mettre à l’échelle ou supprimer) un pool SQL dédié|Propriétaire ou Contributeur Azure sur le pool SQL ou l’espace de travail|aucun
Créer un script SQL</br>|Utilisateur Synapse, ou </br>Propriétaire ou Contributeur Azure sur l’espace de travail, </br>*Des autorisations SQL supplémentaires sont requises pour exécuter un script SQL ou pour publier ou valider des modifications*|
Répertorier et ouvrir tout script SQL publié| Utilisateur d'artefact Synapse, Éditeur d’artefacts, Contributor Synapse|artifacts/read
Exécuter un script SQL sur un pool SQL serverless|Autorisations SQL sur le pool (accordées automatiquement à un Administrateur Synapse)|aucun
Exécuter un script SQL sur un pool SQL dédié|Autorisations SQL sur le pool (accordées automatiquement à un Administrateur Synapse)|aucun
Publier un script SQL nouveau, mis à jour ou supprimé|Éditeur d’artefacts Synapse, Contributor Synapse|sqlScripts/write, delete
Valider les modifications apportées à un script SQL dans le référentiel Git|Nécessite des autorisations Git sur le référentiel|
Attribuer des droits Administrateur Active Directory sur l’espace de travail (via les propriétés de l’espace de travail dans le portail Azure)|Propriétaire ou Contributeur Azure sur l’espace de travail |
POOLS DATA EXPLORER|
Créer un pool Data Explorer |Propriétaire ou Contributeur Azure sur l’espace de travail|aucun
Gérer (suspendre, mettre à l’échelle ou supprimer) un pool Data Explorer|Propriétaire ou Contributeur Azure sur le pool ou l’espace de travail Data Explorer|aucun
Créer un script KQL</br>|Utilisateur Synapse, </br>*Des autorisations Data Explorer supplémentaires sont requises pour exécuter un script ou pour publier ou valider des modifications*.|
Répertorier et ouvrir tout script KQL publié| Utilisateur d'artefact Synapse, Éditeur d’artefacts, Contributor Synapse|artifacts/read
Exécuter un script KQL sur un pool Data Explorer| Autorisations Data Explorer sur le pool (accordées automatiquement à un Administrateur Synapse)|aucun
Publier, mettre à jour ou supprimer un script KQL|Éditeur d’artefacts Synapse, Contributor Synapse|kqlScripts/write, delete
Valider les modifications apportées à un script KQL dans le référentiel Git|Nécessite des autorisations Git sur le référentiel|
POOLS APACHE SPARK|
Créer un pool Apache Spark|Propriétaire ou Contributeur Azure sur l’espace de travail|
Surveiller les applications Apache Spark| Utilisateur Synapse|lire
Afficher les journaux pour l’exécution des notebooks et des travaux |Opérateur de capacité de calcul Synapse|
Annuler un notebook ou un travail Spark en cours d’exécution sur un pool Apache Spark|Opérateur de capacité de calcul Synapse sur le pool Apache Spark.|bigDataPools/useCompute
Créer une définition de notebook ou de travail|Utilisateur Synapse, ou </br>Propriétaire, Contributeur ou Lecteur Azure sur l’espace de travail</br> *Des autorisations supplémentaires sont requises pour exécuter, publier ou valider des modifications*|lire</br></br></br></br></br> 
Répertorier et ouvrir une définition de notebook ou de travail publiée, y compris la révision des sorties enregistrées|Utilisateur d'artefact Synapse, Éditeur d’artefacts Synapse, Contributor Synapse sur l’espace de travail|artifacts/read
Exécuter un notebook et examiner sa sortie, ou envoyer un travail Spark|Administrateur Apache Spark Synapse, Opérateur de capacité de calcul Synapse sur le pool Apache Spark sélectionné|bigDataPools/useCompute 
Publier ou supprimer une définition de notebook ou de travail (y compris la sortie) dans le service|Éditeur d’artefacts sur l’espace de travail, Administrateur Apache Spark Synapse|notebooks/write, delete
Valider les modifications apportées à une définition de notebook ou de tâche dans le référentiel Git|Autorisations Git|aucun
PIPELINES, RUNTIMES D’INTÉGRATION, DATAFLOWS, JEUX DE DONNÉES ET DÉCLENCHEURS|
Créer, mettre à jour ou supprimer un runtime d’intégration|Propriétaire ou Contributeur Azure sur l’espace de travail|
Superviser l’état du runtime d’intégration|Opérateur de capacité de calcul Synapse|read, integrationRuntimes/viewLogs
Passer en revue les exécutions de pipeline|Éditeur d’artefacts Synapse/Contributor Synapse|read, pipelines/viewOutputs 
Créer un pipeline |Utilisateur Synapse</br>*Des autorisations Synapse supplémentaires sont requises pour déboguer, ajouter des déclencheurs ou publier ou valider des modifications*|lire
Créer un dataflow ou un jeu de données |Utilisateur Synapse</br>*Des autorisations Synapse supplémentaires sont requises pour publier ou valider des modifications*|lire
Répertorier et ouvrir un pipeline publié |Utilisateur d'artefact Synapse | artifacts/read
Afficher un aperçu des données d’un jeu de données|Utilisateur Synapse + Utilisateur d’informations d’identification Synapse sur WorkspaceSystemIdentity| 
Déboguer un pipeline à l’aide du runtime d’intégration par défaut|Utilisateur Synapse + Utilisateur d’informations d’identification Synapse sur WorkspaceSystemIdentity|read, </br>credentials/useSecret
Créer un déclencheur, y compris un déclencheur immédiat (nécessite l’autorisation d’exécuter le pipeline)|Utilisateur Synapse + Utilisateur d’informations d’identification Synapse sur WorkspaceSystemIdentity|read, credentials/useSecret/action
Exécuter un pipeline|Utilisateur Synapse + Utilisateur d’informations d’identification Synapse sur WorkspaceSystemIdentity|read, credentials/useSecret/action
Copier des données à l’aide de l’outil Copier des données|Utilisateur Synapse + Utilisateur d’informations d’identification Synapse sur l’identité du système de l’espace de travail|read, credentials/useSecret/action
Ingérer des données (à l’aide d’une planification)|Auteur Synapse + Utilisateur d’informations d’identification Synapse sur l’identité du système de l’espace de travail|read, credentials/useSecret/action
Publier un pipeline, un dataflow ou un déclencheur nouveau, mis à jour ou supprimé dans le service|Éditeur d’artefact Synapse sur l’espace de travail|pipelines/write, delete</br>dataflows/write, delete</br>triggers/write, delete
Valider les modifications apportées aux pipelines, dataflows, jeux de données ou déclencheurs du référentiel Git |Autorisations Git|aucun 
SERVICES LIÉS|
Créer un service lié (y compris l’attribution d’informations d’identification)|Utilisateur Synapse</br>*Des autorisations supplémentaires sont requises pour utiliser un service lié avec des informations d’identification ou pour publier ou valider des modifications*|lire
Répertorier et ouvrir un service lié publié|Utilisateur d'artefact Synapse|linkedServices/write, delete  
Tester la connexion sur un service lié sécurisé par des informations d’identification|Utilisateur Synapse + Utilisateur d’informations d’identification Synapse|credentials/useSecret/action|
Publier un service lié|Éditeur d’artefact Synapse, Gestionnaire de données lié Synapse|linkedServices/write, delete
Valider les définitions de service lié dans le référentiel Git|Autorisations Git|aucun
GESTION DES ACCÈS|
Passer en revue les attributions de rôles RBAC Synapse dans n’importe quelle étendue|Utilisateur Synapse|lire
Attribuer et supprimer des rôles RBAC Synapse pour des utilisateurs, groupes et principaux de service| Administrateur Synapse dans l’espace de travail ou au niveau d’une étendue d’élément d’espace de travail spécifique|roleAssignments/write, delete 

> [!Note]
> Les utilisateurs invités d’un autre locataire peuvent aussi consulter, ajouter ou modifier les attributions de rôles une fois que l’administrateur Synapse leur a attribué un rôle. 

## <a name="next-steps"></a>Étapes suivantes

 - [Passer en revue les attributions de rôles RBAC Synapse](./how-to-review-synapse-rbac-role-assignments.md)
 - [Gérer les attributions de rôles RBAC Synapse](./how-to-manage-synapse-rbac-role-assignments.md)
