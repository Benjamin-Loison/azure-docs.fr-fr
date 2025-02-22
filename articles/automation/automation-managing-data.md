---
title: Sécurité des données Azure Automation
description: Cet article vous aide à découvrir comment Azure Automation protège votre confidentialité et sécurise vos données.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2d78b83ac7725f07060b3b61b01e57837fb053b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114465899"
---
# <a name="management-of-azure-automation-data"></a>Gestion des données Azure Automation

Cet article contient plusieurs rubriques expliquant comment les données sont protégées et sécurisées dans un environnement Azure Automation.

## <a name="tls-12-for-azure-automation"></a>TLS 1.2 pour Azure Automation

Pour garantir la sécurité des données en transit vers Azure Automation, nous vous encourageons vivement à configurer l’utilisation du protocole TLS (Transport Layer Security) 1.2. Voici une liste de méthodes ou de clients qui communiquent via HTTPS avec le service Automation :

* Appels de Webhook

* Runbook Workers hybrides comprenant des machines gérées par Update Management et Suivi des modifications et inventaire.

* Nœuds DSC

Les versions antérieures de TLS/SSL (Secure Sockets Layer) se sont avérées vulnérables et bien qu’elles fonctionnent encore pour assurer la compatibilité descendante, elles sont **déconseillées**. Nous ne recommandons pas de configurer explicitement votre agent de façon à ce qu’il utilise uniquement TLS 1.2, sauf en cas de nécessité, car cela peut annuler les fonctionnalités de sécurité au niveau de la plateforme qui vous permettent de détecter automatiquement et de tirer parti des protocoles plus sécurisés et plus récents dès qu’ils sont disponibles, tels que TLS 1.3.

Pour plus d’informations sur la prise en charge du protocole TLS 1.2 avec l’agent Log Analytics pour Windows et Linux, qui est une dépendance pour le rôle Runbook Worker hybride, consultez [Vue d’ensemble de l’agent Log Analytics – TLS 1.2](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol).

### <a name="platform-specific-guidance"></a>Instructions spécifiques à la plateforme

|Plateforme/Langage | Support | Informations complémentaires |
| --- | --- | --- |
|Linux | Les distributions de Linux s’appuient généralement sur [OpenSSL](https://www.openssl.org) pour la prise en charge de TLS 1.2.  | Vérifiez [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) pour vous assurer que votre version d’OpenSSL est prise en charge.|
| Windows 8.0 - 10 | Pris en charge, activé par défaut. | Pour confirmer que vous utilisez toujours les [paramètres par défaut](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 - 2016 | Pris en charge, activé par défaut. | Pour confirmer que vous utilisez toujours les [paramètres par défaut](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 et Windows Server 2008 R2 SP1 | Pris en charge, mais non activé par défaut. | Consultez la page [Paramètres de Registre de TLS](/windows-server/security/tls/tls-registry-settings) pour plus d’informations sur l’activation.  |

## <a name="data-retention"></a>Conservation des données

Quand vous supprimez une ressource dans Azure Automation, elle est conservée pendant de nombreux jours à des fins d’audit avant d’être supprimée définitivement. Vous ne pouvez ni voir ni utiliser la ressource pendant cette période. Cette stratégie vaut aussi pour les ressources qui appartiennent à un compte Automation supprimé. La stratégie de rétention s’applique à tous les utilisateurs et ne peut actuellement pas être personnalisée. Cependant, si vous souhaitez conserver les données sur une plus longue période, vous pouvez [transférer les données de tâches Azure Automation dans des journaux Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

Le tableau suivant récapitule la stratégie de rétention pour les différentes ressources.

| Données | Stratégie |
|:--- |:--- |
| Comptes |Un compte est définitivement supprimé 30 jours après avoir été supprimé par un utilisateur. |
| Éléments multimédias |Une ressource est définitivement supprimée 30 jours après avoir été supprimée par un utilisateur ou 30 jours après qu’un utilisateur a supprimé un compte qui contenait la ressource. Les ressources incluent des variables, des planifications, des informations d’identification, des certificats, des packages Python 2 et des connexions. |
| Nœuds DSC |Un nœud DSC est définitivement supprimé 30 jours après avoir été désinscrit d’un compte Automation via le portail Azure ou l’applet de commande [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) dans Windows PowerShell. De même, un nœud peut être supprimé définitivement 30 jours après qu’un utilisateur a supprimé le compte qui contenait le nœud. |
| travaux |Une tâche est supprimée et définitivement retirée 30 jours après avoir été modifiée (par exemple, suite à la fin, à l’arrêt ou à l’interruption de la tâche). |
| Modules |Un module est définitivement supprimé 30 jours après avoir été supprimé par un utilisateur ou 30 jours après qu’un utilisateur a supprimé le compte qui contenait le module. |
| Configurations de nœud/fichiers MOF |Une ancienne configuration de nœud est définitivement supprimée 30 jours après la génération d’une nouvelle configuration de nœud. |
| Rapports sur le nœud |Le rapport sur un nœud est définitivement supprimé définitivement 90 jours après la génération d’un nouveau rapport pour ce même nœud. |
| Runbooks |Un runbook est définitivement supprimé 30 jours après qu’un utilisateur a supprimé la ressource ou 30 jours après qu’un utilisateur a supprimé le compte qui contenait la ressource<sup>1</sup>. |

<sup>1</sup>Le runbook peuvent être récupéré dans la fenêtre de 30 jours en ouvrant un incident de support Azure auprès du support Microsoft Azure. Accédez au [site de support Azure](https://azure.microsoft.com/support/options/) et sélectionnez **Soumettre une demande de support**.

## <a name="data-backup"></a>Sauvegarde de données

Quand vous supprimez un compte Automation dans Azure, tous les objets du compte sont supprimés. Ces objets peuvent notamment consister en des runbooks, des modules, des configurations, des paramètres, des tâches ou des ressources. Ces objets ne peuvent pas être récupérés après que le compte a été supprimé. Vous pouvez utiliser les informations suivantes pour sauvegarder le contenu de votre compte Automation avant de le supprimer.

### <a name="runbooks"></a>Runbooks

Vous pouvez exporter vos Runbooks vers vos fichiers de script en utilisant soit le portail Azure, soit l’applet de commande [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) dans Windows PowerShell. Vous pouvez importer ces fichiers de script dans un autre compte Automation, comme expliqué dans [Gérer les runbooks dans Azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Modules d'intégration

Vous ne pouvez pas exporter les modules d’intégration à partir d’Azure Automation, ils doivent être mis à disposition en dehors du compte Automation.

### <a name="assets"></a>Éléments multimédias

Vous ne pouvez pas exporter de ressources Azure Automation : certificats, connexions, informations d’identification, planifications et variables. En revanche, vous pouvez utiliser le portail Azure et des applets de commande Azure pour noter les détails de ces ressources. Ces détails vous serviront par la suite à créer les ressources qui seront utilisées par les runbooks que vous importerez dans un autre compte Automation.

Vous ne pouvez pas récupérer la valeur des variables chiffrées ou des champs de mot de passe des informations d’identification en utilisant des applets de commande. Si vous ne connaissez pas ces valeurs, vous pouvez les récupérer dans un runbook. Pour récupérer des valeurs de variables, consultez [Ressources de variable dans Azure Automation](shared-resources/variables.md). Pour en savoir plus sur la récupération des valeurs d’informations d’identification, consultez [Ressources d’informations d’identification dans Azure Automation](shared-resources/credentials.md).

### <a name="dsc-configurations"></a>Configurations DSC

Vous pouvez exporter vos configurations DSC dans des fichiers de script en utilisant soit le portail Azure, soit l'applet de commande [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) dans Windows PowerShell. Vous pouvez importer et utiliser ces configurations dans un autre compte Automation.

## <a name="geo-replication-in-azure-automation"></a>Géo-réplication dans Azure Automation

La géoréplication est une fonctionnalité standard des comptes Azure Automation. Au moment de configurer votre compte, vous choisissez une région primaire. Le service de géoréplication interne d’Automation affecte alors automatiquement une région secondaire au compte. Les données de compte de la région primaire sont sauvegardées en continu dans la région secondaire. La liste complète des régions primaires et secondaires se trouve dans [Continuité et reprise d’activité : régions jumelées d’Azure](../best-practices-availability-paired-regions.md).

La sauvegarde créée par le service de géoréplication Automation est une copie complète de ressources, configurations et autre objets analogues Automation. Cette sauvegarde peut être utilisée si la région primaire perd des données à la suite d’une défaillance. Dans le cas peu probable d’une perte des données d’une région primaire, Microsoft tente de les récupérer.

> [!NOTE]
> Azure Automation stocke les données client dans la région sélectionnée par le client. À des fins de continuité d’activité et reprise d’activité, pour toutes les régions, à l’exception de Brésil Sud et Asie Sud-Est, les données Azure Automation sont stockées dans une région différente (région appariée Azure). Dans le cas de la région Brésil Sud (État de Sao Paulo) de la géographie Brésil et de la région Asie Sud-Est (Singapour) de la géographie Asie-Pacifique, nous stockons les données Azure Automation dans la même région afin de répondre aux exigences de résidence des données pour ces régions.

Le service de géoréplication Automation n’est pas directement accessible aux clients externes en cas de défaillance régionale. Si vous voulez conserver les runbooks et la configuration Automation à l’occasion de défaillances régionales :

1. Sélectionnez la région secondaire à appairer avec la région géographique de votre compte Automation principal.

2. Créez un compte Automation dans la région secondaire.

3. Dans le compte principal, exporter vos runbooks sous forme de fichiers de script.

4. Importez les runbooks dans votre compte Automation de la région secondaire.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les ressources sécurisées dans Azure Automation, consultez [Chiffrement des ressources sécurisées dans Azure Automation](automation-secure-asset-encryption.md).

* Pour découvrir plus en détail la géoréplication, consultez [Création et utilisation de la géoréplication active](../azure-sql/database/active-geo-replication-overview.md).
