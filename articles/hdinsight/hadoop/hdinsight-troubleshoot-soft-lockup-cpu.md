---
title: 'Erreur « watchdog: BUG: soft lockup - CPU » de cluster Azure HDInsight'
description: 'L’erreur « watchdog: BUG: soft lockup - CPU » apparaît dans les journaux syslogs de noyau du cluster Azure HDInsight'
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: d2deb421dce440c1c8f949c858269fed038ac3d5
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299750"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scénario : erreur « watchdog : BUG : blocage logiciel - CPU » à partir d’un cluster Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Les journaux syslogs du noyau contiennent le message d’erreur : `watchdog: BUG: soft lockup - CPU`.

## <a name="cause"></a>Cause

Un [bogue](https://bugzilla.kernel.org/show_bug.cgi?id=199437) dans le noyau Linux est à l’origine de verrouillages logiciels du processeur.

## <a name="resolution"></a>Résolution

Appliquer le correctif du noyau. Le script ci-dessous met à niveau le noyau Linux et redémarre les machines à différents moments au cours des 24 heures. Exécutez l’action de script dans deux lots. Le premier lot se trouve sur tous les nœuds, à l’exception du nœud principal. Le deuxième lot est sur le nœud principal. Ne s’exécute pas sur le nœud principal et d’autres nœuds en même temps.

1. Accédez à votre cluster HDInsight à partir du portail Azure.

1. Accédez aux actions de script.

1. Sélectionnez **Envoyer**, puis entrez ce qui suit

    | Propriété | Valeur |
    | --- | --- |
    | Type de script | -Personnalisé |
    | Nom |Correctif pour le problème de verrouillage logiciel du noyau |
    | URI de script bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Type(s) de nœud |Worker, Zookeeper |
    | Paramètres |NON APPLICABLE |

    Sélectionnez **Conservez cette action de script...** si vous souhaitez exécuter le script lorsque des nœuds sont ajoutés.

1. Sélectionnez **Create** (Créer).

1. Attendez que l’exécution aboutisse.

1. Exécutez l’action de script sur le nœud principal en suivant les mêmes étapes que l’étape 3, mais cette fois-ci, avec les types de nœud : Principal.

1. Attendez que l’exécution aboutisse.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]