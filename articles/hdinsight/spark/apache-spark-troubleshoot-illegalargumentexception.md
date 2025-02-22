---
title: Erreur IllegalArgumentException pour Apache Spark – Azure HDInsight
description: IllegalArgumentException pour l’activité Apache Spark dans Azure HDInsight pour Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 2b892e6419045d362ac8675a6e8976600650851a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112288916"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Scénario : IllegalArgumentException pour l’activité Apache Spark dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Apache Spark dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Vous recevez l’exception suivante lors de la tentative d’exécution d’une activité Spark dans un pipeline Azure Data Factory :

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Cause

Un travail Spark échoue si le fichier jar de l’application ne se trouve pas dans le stockage par défaut/principal du cluster Spark.

Il s’agit d’un problème connu avec l’infrastructure open source Spark suivi dans ce bogue : [Échec du travail Spark si fs.defaultFS et le fichier jar d’application sont des URL différentes](https://issues.apache.org/jira/browse/SPARK-22587).

Ce problème a été résolu dans Spark 2.3.0.

## <a name="resolution"></a>Résolution

Assurez-vous que le fichier jar de l’application est stocké sur le stockage par défaut/principal pour le cluster HDInsight. Dans le cas d’Azure Data Factory, assurez-vous que le service lié ADF est pointé vers le conteneur HDInsight par défaut plutôt qu’un conteneur secondaire.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]