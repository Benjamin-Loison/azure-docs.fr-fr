---
title: 'Tutoriel : Prise en main d’Azure Synapse Analytics - Surveiller votre espace de travail Synapse'
description: Dans ce tutoriel, vous allez apprendre à surveiller les activités dans votre espace de travail Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 08/25/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: cab153403d41a7eca026fb2350d8f94238df6ec2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054785"
---
# <a name="monitor-your-synapse-workspace"></a>Surveiller votre espace de travail Synapse

Dans ce tutoriel, vous allez apprendre à surveiller les activités dans votre espace de travail Synapse. Vous pouvez superviser les activités actuelles et précédentes en relation avec SQL, Apache Spark et Pipelines. 

## <a name="introduction-to-the-monitor-hub"></a>Présentation du hub Monitor

Ouvrez Synapse Studio et accédez au hub **Monitor**. À cet emplacement, vous pouvez voir un historique de toutes les activités qui se produisent dans l’espace de travail et celles qui sont actuellement actives. 

* Sous **Intégration**, vous pouvez superviser les pipelines, les déclencheurs et les runtimes d’intégration.
* Sous **Activités**, vous pouvez superviser les activités Spark et SQL. 

## <a name="integration"></a>Intégration

1. Accédez à **Intégration > Exécutions de pipeline**. Dans cette vue, vous pouvez voir chaque exécution d’un pipeline dans votre espace de travail. 
1. Recherchez le pipeline que vous avez exécuté à l’étape précédente, et cliquez sur son **nom** pour afficher les détails.
1. Cliquez sur **Barre de navigation** vers le haut de Synapse Studio, cliquez sur **Toutes les exécutions de pipelines** pour revenir à la vue précédente.

## <a name="data-explorer-activities"></a>Activités Data Explorer

1. Accédez à **Activités > Requêtes KQL**.
1. Dans cette vue, vous pouvez voir les requêtes KQL.
1. Sélectionnez un **Pool** à superviser dans le filtre **Pool**. Vous pouvez maintenant voir toutes les requêtes KQL en cours d’exécution ou exécutées dans votre espace de travail dans ce pool.
1. Recherchez une requête KQL spécifique, puis cliquez sur le lien **Plus** pour voir le texte complet de la requête KQL.

## <a name="apache-spark-activities"></a>Activités Apache Spark

1. Accédez à **Activités > Applications Apache Spark**. Vous pouvez maintenant voir toutes les applications Spark en cours d’exécution ou exécutées dans votre espace de travail.
1. Recherchez une application qui n’est plus en cours d’exécution et cliquez sur son **nom**. Vous pouvez maintenant voir les détails de l’application Spark.
1. Si vous êtes familiarisé avec Apache Spark, vous pouvez trouver l’interface utilisateur du serveur d’historique Apache Spark standard en cliquant sur **Serveur d’historique Spark**.

## <a name="sql-activities"></a>Activités SQL

1. Accédez à **Activités > Requêtes SQL**.
1. Dans cette vue, vous pouvez voir les requêtes SQL.
1. Sélectionnez un **Pool** à superviser dans le filtre **Pool**. Vous pouvez maintenant voir toutes les requêtes SQL en cours d’exécution ou exécutées dans votre espace de travail dans ce pool.
1. Recherchez une requête SQL spécifique, puis cliquez sur le lien **Plus** pour voir le texte complet de la requête SQL.

    > [!NOTE] 
    > Les requêtes SQL soumises par le biais de Synapse Studio dans un pool SQL dédié (anciennement SQL DW) activé sur l’espace de travail peuvent être consultées dans le hub Monitor. Pour toutes les autres activités de supervision, vous pouvez accéder à la supervision des pools SQL dédiés (anciennement SQL DW) sur le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer le Centre des connaissances](get-started-knowledge-center.md)
