---
title: Supervision - Azure Database for MariaDB
description: Cet article décrit les métriques de surveillance et d’alerte pour Azure Database pour MariaDB, notamment les statistiques sur l’UC, le stockage et la connexion.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/21/2020
ms.openlocfilehash: 36b3c29872c73122a0dc7913499f8a5a10df94e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065168"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Supervision dans Azure Database for MariaDB
La surveillance des données relatives à vos serveurs vous aide à résoudre les problèmes et à optimiser votre charge de travail. Azure Database pour MariaDB propose diverses métriques qui fournissent des insights sur le comportement de votre serveur.

## <a name="metrics"></a>Mesures
Toutes les métriques Azure présentent une fréquence d’une minute et chaque métrique fournit 30 jours d’historique. Vous pouvez configurer des alertes basées sur les métriques. Les autres tâches incluent la configuration d’actions automatisées, l’exécution d’analyses avancées et l’archivage de l’historique. Pour plus d’informations, consultez [Vue d’ensemble des mesures dans Microsoft Azure](../azure-monitor/data-platform.md).

Pour des instructions étape par étape, consultez [Configurer des alertes](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Liste des métriques
Ces métriques sont disponibles pour Azure Database pour MariaDB :

|Métrique|Nom d’affichage de la métrique|Unité|Description|
|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Pourcentage d’UC en cours d’utilisation.|
|memory_percent|Pourcentage de mémoire|Pourcentage|Pourcentage de mémoire en cours d’utilisation.|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Pourcentage d’E/S en cours d’utilisation. (Non applicable pour les serveurs de niveau De base)|
|storage_percent|Pourcentage de stockage|Pourcentage|Pourcentage de stockage utilisé par rapport à la limite maximale du serveur.|
|storage_used|Stockage utilisé|Octets|Quantité de stockage en cours d’utilisation. Le stockage utilisé par le service peut inclure les fichiers de base de données, les journaux d’activité des transactions et les journaux d’activité du serveur.|
|serverlog_storage_percent|Pourcentage de stockage du journal du serveur|Pourcentage|Pourcentage de stockage du journal du serveur utilisé par rapport au stockage du journal du serveur maximal défini pour le serveur.|
|serverlog_storage_usage|Stockage du journal du serveur utilisé|Octets|Quantité de stockage du journal du serveur en cours d’utilisation.|
|serverlog_storage_limit|Limite de stockage du journal du serveur|Octets|Stockage du journal du serveur maximal défini pour ce serveur.|
|storage_limit|Limite de stockage|Octets|Stockage maximal pour ce serveur.|
|active_connections|Connexions actives|Count|Nombre de connexions actives sur le serveur.|
|connections_failed|Connexions ayant échoué|Count|Nombre de connexions au serveur ayant échoué.|
|seconds_behind_master|Décalage de la réplication en secondes|Count|Nombre de secondes de latence du serveur réplica par rapport au serveur source. (Non applicable pour les serveurs de niveau De base)|
|network_bytes_egress|Network Out|Octets|Sortie réseau entre connexions actives.|
|network_bytes_ingress|Network In|Octets|Entrée réseau entre connexions actives.|
|backup_storage_used|Stockage de sauvegarde utilisé|Octets|Quantité de stockage de sauvegarde utilisée. La métrique représente le total du stockage consommé par l’ensemble des sauvegardes de base de données complètes, sauvegardes différentielles et sauvegardes de journaux conservées en fonction de la période de rétention de sauvegarde définie pour le serveur. La fréquence des sauvegardes est gérée par le service et expliquée dans l’[article sur les concepts](concepts-backup.md). Pour le stockage géo-redondant, l’utilisation du stockage de sauvegarde est le double de celle du stockage localement redondant.|

## <a name="server-logs"></a>Journaux d’activité du serveur

Vous pouvez activer la journalisation des requêtes lentes sur votre serveur. Ces journaux sont également disponibles par le biais des journaux de diagnostic Azure dans les journaux Azure Monitor, les hubs d’événements et le compte de stockage. Pour en savoir plus sur la journalisation, consultez la page [journaux d’activité serveur](concepts-server-logs.md).

## <a name="query-store"></a>Magasin des requêtes

[Magasin des requêtes](concepts-query-store.md) effectue le suivi des performances des requêtes au fil du temps, y compris les statistiques d’exécution des requêtes et les événements d’attente. La fonctionnalité conserve les informations de performances du runtime de requête dans le schéma **mysql**. Vous pouvez contrôler la collecte et le stockage des données au moyen de différents boutons de configuration.

## <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](concepts-query-performance-insight.md) fonctionne conjointement avec le Magasin des requêtes pour fournir des visualisations accessibles à partir du portail Azure. Ces graphiques vous permettent d’identifier les principales requêtes qui ont un impact sur les performances. Query Performance Insight est accessible dans la section **Performances intelligentes** de la page du portail de votre serveur Azure Database for MariaDB.

## <a name="planned-maintenance-notification"></a>Notification de maintenance planifiée

Les [notifications de maintenance planifiée](./concepts-planned-maintenance-notification.md) vous permettent de recevoir des alertes concernant la maintenance planifiée à venir sur votre Azure Database for MariaDB. Ces notifications sont intégrées à la maintenance planifiée de [Service Health](../service-health/overview.md) et vous permettent d’afficher toutes les tâches de maintenance planifiée de vos abonnements dans un même emplacement. Cela permet également d’adapter la notification au public approprié pour divers groupes de ressources, car des contacts différents peuvent être responsables de différentes ressources. Vous recevez la notification concernant la maintenance à venir 72 heures avant l’événement.

En savoir plus sur la configuration des notifications dans le document relatif aux [notifications de maintenance planifiée](./concepts-planned-maintenance-notification.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la façon d’accéder aux métriques et de les exporter à l’aide du portail Azure, de l’API REST ou de CLI, consultez [Vue d’ensemble des métriques Azure](../azure-monitor/data-platform.md).
- Consultez le [guide pratique pour configurer des alertes](howto-alert-metric.md) pour savoir comment créer une alerte sur une métrique.
- En savoir plus sur les [notifications de maintenance planifiée](./concepts-planned-maintenance-notification.md) dans Azure Database for MariaDB.