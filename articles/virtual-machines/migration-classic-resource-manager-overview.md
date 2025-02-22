---
title: Vue d’ensemble de la migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager
description: Découvrez la migration de ressources prise en charge par la plateforme de l’environnement Classic vers Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 3909c87a256b6ae6afe230f9bad91d3fccdc62a7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346645"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows

> [!IMPORTANT]
> Aujourd’hui, environ 90 % des machines virtuelles IaaS utilisent [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Depuis le 28 février 2020, les machines virtuelles classiques sont dépréciées. Elles seront entièrement mises hors service le 1er mars 2023. [Apprenez-en davantage]( https://aka.ms/classicvmretirement) sur cette désapprobation et [son impact sur vous](classic-vm-deprecation.md#how-does-this-affect-me).



Cet article fournit une vue d’ensemble de l’outil de migration pris en charge par la plateforme, explique comment migrer les ressources d’ASM (Azure Service Manager), également appelé modèle de déploiement classique, vers des modèles de déploiement ARM (Azure Resource Manager). Il décrit en détail comment connecter les ressources des deux modèles de déploiement qui coexistent dans votre abonnement à l’aide de passerelles de réseau virtuel de site à site. Pour en savoir plus, voir [Fonctionnalités et avantages d’Azure Resource Manager](../azure-resource-manager/management/overview.md). 

ASM prend en charge deux produits de calcul différents, Machines virtuelles Azure (modèle classique), également appelé Machines virtuelles IaaS, et [Azure Cloud Services (modèle classique)](../cloud-services/index.yml) également appelé Machines virtuelles PaaS ou Rôles web/Rôles de travail. Ce document traite uniquement de la migration de machines virtuelles Azure (classiques).

## <a name="goal-for-migration"></a>Objectif de la migration
Resource Manager autorise le déploiement d’applications complexes à l’aide de modèles, configure les machines virtuelles au moyen d’extensions de machines virtuelles et intègre la gestion des accès et le balisage. Azure Resource Manager inclut un déploiement extensible, en parallèle, de machines virtuelles dans des groupes à haute disponibilité. Le nouveau modèle de déploiement assure également la gestion de façon indépendante du cycle de vie des services de calcul, de réseau et de stockage. Enfin, il applique la sécurité par défaut grâce à la mise en œuvre de machines virtuelles dans un réseau virtuel.

Pratiquement toutes les fonctionnalités du modèle de déploiement Classic sont prises en charge pour le calcul, le réseau et le stockage dans Azure Resource Manager. Pour bénéficier des nouvelles fonctionnalités d’Azure Resource Manager, vous pouvez migrer des déploiements existants à partir du modèle de déploiement classique.

## <a name="supported-resources--configurations-for-migration"></a>Ressources et configurations prises en charge pour la migration

### <a name="supported-resources-for-migration"></a>Ressources prises en charge pour la migration
* Machines virtuelles (service cloud avec machines virtuelles)
* [Cloud Services (avec rôles web/rôles de travail)](../cloud-services-extended-support/in-place-migration-overview.md)
* Groupes à haute disponibilité
* Comptes de stockage
* Virtual Network
* Passerelles VPN
* [Passerelles Express Route](../expressroute/expressroute-howto-move-arm.md) _(dans le même abonnement que Réseau virtuel uniquement)_
* Network Security Group
* Tables de routage
* IP réservées

## <a name="supported-configurations-for-migration"></a>Configurations prises en charge pour la migration
Ces ressources IaaS classiques sont prises en charge lors de la migration

| Service | Configuration |
| --- | --- |
| Services de domaine Azure AD | [Réseaux virtuels contenant des services de domaine Azure AD](../active-directory-domain-services/migrate-from-classic-vnet.md) |

## <a name="supported-scopes-of-migration"></a>Étendues de migration prises en charge
Il existe quatre façons différentes de migrer les ressources de calcul, de réseau et de stockage :

* [Migration de machines virtuelles (ne figurant PAS dans un réseau virtuel)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migration de machines virtuelles (dans un réseau virtuel)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migration de comptes de stockage](#migration-of-storage-accounts)
* [Migration des ressources non attachées](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migration de machines virtuelles (ne figurant PAS dans un réseau virtuel)
Dans le modèle de déploiement Resource Manager, nous appliquons par défaut la sécurité de vos applications. Toutes les machines virtuelles doivent donc figurer dans un réseau virtuel du modèle Resource Manager. La plateforme Azure redémarre (`Stop`, `Deallocate`, et `Start`) les machines virtuelles dans le cadre de la migration. Vous avez deux options pour les réseaux virtuels vers lesquels les machines virtuelles seront migrées :

* Vous pouvez demander à la plateforme de créer un réseau virtuel et de procéder à la migration de la machine virtuelle vers ce réseau.
* Vous pouvez effectuer la migration de la machine virtuelle vers un réseau virtuel existant dans Resource Manager.

> [!NOTE]
> Dans cette étendue de migration, les opérations plan de gestion et du plan de données peuvent ne pas être autorisées pendant un certain laps de temps durant la migration.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migration de machines virtuelles (dans un réseau virtuel)
Pour la plupart des configurations de machines virtuelles, seules les métadonnées sont migrées entre le modèle de déploiement classique et le modèle de déploiement Resource Manager. Les machines virtuelles sous-jacentes s’exécutent sur le même matériel, dans le même réseau et avec le même stockage. Il se peut que les opérations du plan de gestion ne soient pas autorisées pendant un certain laps de temps durant la migration. Toutefois, le plan de données continue à fonctionner. Cela signifie que vos applications s’exécutant par dessus les machines virtuelles (Classic) ne subissent aucun temps d’arrêt lors de la migration.

Les configurations non prises en charge actuellement sont les suivantes. En cas de prise en charge à l’avenir, certaines machines virtuelles de cette configuration connaîtront peut-être un temps d’arrêt (avec exécution des opérations d’arrêt, de désallocation et de redémarrage des machines virtuelles).

* Vous disposez de plus d’un groupe à haute disponibilité dans un seul service cloud.
* Vous disposez d’un ou de plusieurs groupes à haute disponibilité et de machines virtuelles ne figurant pas dans un groupe à haute disponibilité dans un seul service cloud.

> [!NOTE]
> Dans cette étendue de migration, le plan de gestion peut ne pas être utilisable pendant un certain laps de temps lors de la migration. Certaines configurations décrites ci-dessus entraînent un temps d’arrêt du forfait de données.
>

### <a name="migration-of-storage-accounts"></a>Migration de comptes de stockage
Pour permettre une migration fluide, vous pouvez déployer des machines virtuelles Resource Manager dans un compte de stockage classique. Cette fonctionnalité permet d’effectuer la migration des ressources de calcul et de réseau indépendamment des comptes de stockage. Après avoir migré vos machines virtuelles et votre réseau virtuel, vous devrez migrer vos comptes de stockage pour achever le processus de migration.

Si votre compte de stockage ne dispose d’aucun disque associé ni de données de machines virtuelles et ne possède que des objets blob, des fichiers, des tables et des files d’attente, la migration vers Azure Resource Manager peut être réalisée comme migration autonome sans dépendances.

> [!NOTE]
> Le modèle de déploiement Resource Manager n’intègre pas le concept d’images et de disques classiques. Une fois le compte de stockage migré, les images et les disques du modèle classique ne sont plus visibles dans le portail Azure. Toutefois, les VHD associés restent dans le compte de stockage.

Les captures d’écran suivantes montrent comment mettre à niveau un compte de stockage classique vers un compte de stockage Azure Resource Manager à l’aide du portail Azure :
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez à votre compte de stockage.
3. Dans la section **Paramètres**, cliquez sur **Migrer vers Azure Resource Manager**.
4. Cliquez sur **Valider** pour déterminer la faisabilité de la migration.
5. Si la validation réussit, cliquez sur **Préparer** pour créer un compte de stockage migré.
6. Tapez **oui** pour confirmer la migration et cliquez sur **Valider** pour terminer la migration.

    ![Valider le compte de stockage](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Préparer le compte de stockage](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Finaliser la migration du compte de stockage](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migration des ressources non attachées
Les comptes de stockage sans disque associé ou données de machines virtuelles peuvent être migrés indépendamment.

Les groupes de sécurité réseau, tables de routage et adresses IP réservées qui ne sont pas rattachés à des machines virtuelles et des réseaux virtuels peuvent aussi être migrés indépendamment.

<br>

## <a name="unsupported-features-and-configurations"></a>Fonctionnalités et configurations non prises en charge
Certaines fonctionnalités et configurations ne sont actuellement pas prises en charge. La section suivante détaille nos recommandations les concernant.

### <a name="unsupported-features"></a>Fonctionnalités non prises en charge
Les fonctionnalités non prises en charge actuellement sont les suivantes. Si vous le souhaitez, vous pouvez supprimer ces paramètres, effectuer la migration des machines virtuelles, puis réactiver les paramètres dans le modèle de déploiement Resource Manager.

| Fournisseur de ressources | Fonctionnalité | Recommandation |
| --- | --- | --- |
| Calcul | Disques de machine virtuelle non associés. | Les objets BLOB VHD derrière ces disques seront migrés lors de la migration du compte de stockage |
| Calcul | Images de machine virtuelle. | Les objets BLOB VHD derrière ces disques seront migrés lors de la migration du compte de stockage |
| Réseau | Listes de contrôle d’accès de points de terminaison. | Supprimez les listes de contrôle d’accès des points de terminaison et réessayez la migration. |
| Réseau | Application Gateway | Supprimez l’Application Gateway avant de commencer la migration, puis recréez l’Application Gateway une fois la migration terminée. |
| Réseau | Réseaux virtuels à l’aide de l’homologation de réseaux virtuels (VNet Peering). | Migrez le réseau virtuel vers Resource Manager, puis homologuez-le. Apprenez-en plus sur le [Peering de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Configurations non prises en charge
Les configurations non prises en charge actuellement sont les suivantes.

| Service | Configuration | Recommandation |
| --- | --- | --- |
| Gestionnaire de ressources |Contrôle d’accès en fonction du rôle (RBAC) pour les ressources classiques |L’URI des ressources étant modifié après la migration, il est recommandé de planifier les mises à jour de stratégie RBAC à exécuter après la migration. |
| Calcul |Plusieurs sous-réseaux associés à une machine virtuelle |Mettez à jour la configuration de sous-réseau afin de ne référencer qu’un seul sous-réseau. Pour cette opération, vous devrez peut-être supprimer de la machine virtuelle une carte réseau secondaire (se rapportant à un autre sous-réseau), puis la rattacher une fois la migration effectuée. |
| Calcul |Machines virtuelles appartenant à un réseau virtuel, mais auxquelles aucun sous-réseau n’est affecté de manière explicite |Si vous le souhaitez, vous pouvez supprimer la machine virtuelle. |
| Calcul |Machines virtuelles dotées d’alertes et de stratégies de mise à l’échelle automatique |La migration a lieu et ces paramètres sont ignorés. Il est vivement recommandé d’évaluer votre environnement avant de procéder à la migration. Vous pouvez également choisir de reconfigurer les paramètres d’alerte une fois la migration terminée. |
| Calcul |Extensions XML de machines virtuelles (BGInfo 1.*, débogueur Visual Studio, Web Deploy et débogage à distance) |Cela n'est pas pris en charge. Il est recommandé de supprimer ces extensions de la machine virtuelle pour continuer la migration ; sinon, elles seront automatiquement supprimées pendant le processus de migration. |
| Calcul |Diagnostics de démarrage avec le stockage Premium |Désactivez la fonctionnalité Diagnostics de démarrage pour les machines virtuelles avant de poursuivre la migration. Vous pouvez réactiver les Diagnostics de démarrage dans la pile Resource Manager une fois la migration terminée. En outre, les objets Blob utilisés pour la capture d’écran et les journaux d’activité de série doivent être supprimés afin que vous ne soyez plus facturé pour ces objets Blob. |
| Calcul | Services cloud qui contiennent plus d’un groupe à haute disponibilité ou des groupes à haute disponibilité multiples. |Non pris en charge actuellement. Placez les machines virtuelles dans le même groupe à haute disponibilité avant la migration. |
| Calcul | Machine virtuelle avec l’extension Microsoft Defender pour le cloud | Microsoft Defender pour le cloud installe automatiquement les extensions sur vos machines virtuelles pour contrôler leur sécurité et déclencher des alertes. Ces extensions sont généralement installées automatiquement si la stratégie de Microsoft Defender pour le cloud est activée sur l’abonnement. Pour migrer les machines virtuelles, désactivez la stratégie de Defender pour le cloud sur l’abonnement, ce qui supprimera l’extension de surveillance Defender pour le cloud des machines virtuelles. |
| Calcul | Machine virtuelle avec une extension de sauvegarde ou de capture instantanée | Ces extensions sont installées sur une machine virtuelle configurée avec le service Sauvegarde Azure. Bien que la migration de ces machines virtuelles ne soit pas prise en charge, suivez les conseils d’aide fournis dans les [Questions fréquentes (FAQ) sur la migration du modèle classique vers le modèle Azure Resource Manager](./migration-classic-resource-manager-faq.yml) pour conserver les sauvegardes effectuées avant la migration.  |
| Calcul | Machine virtuelle avec extension d’Azure Site Recovery | Ces extensions sont installées sur une machine virtuelle configurée avec le service Azure Site Recovery. Même si la migration du stockage utilisé avec Site Recovery fonctionnera, il y aura des conséquences sur la réplication actuelle. Vous devez désactiver et activer la réplication de machine virtuelle après la migration du stockage. |
| Réseau |Réseaux virtuels contenant des machines virtuelles et des rôles Web/de travail |Non pris en charge actuellement. Placez les rôles Web/de travail dans leur propre réseau virtuel avant la migration. Une fois que le réseau virtuel classique a migré, le réseau virtuel Azure Resource Manager qui a migré peut être appairé avec le réseau virtuel classique pour obtenir une configuration similaire à celle d’avant.|
| Réseau | Circuits Express Route classiques |Non pris en charge actuellement. Ces circuits doivent migrer vers Azure Resource Manager avant le début de la migration IaaS. Pour en savoir plus, consultez [Migration de circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](../expressroute/expressroute-move.md).|
| Azure App Service |Réseaux virtuels contenant des environnements App Service |Non pris en charge actuellement. |
| Azure HDInsight |Réseaux virtuels contenant des services HDInsight |Non pris en charge actuellement. |
| Services de cycle de vie Microsoft Dynamics |Réseaux virtuel contenant des machines virtuelles gérées par Dynamics Lifecycle Services |Non pris en charge actuellement. |
| Gestion des API Azure |Réseaux virtuels contenant des déploiements Gestion des API Azure |Non pris en charge actuellement. Pour migrer le réseau virtuel IaaS, modifiez le réseau virtuel du déploiement Gestion des API. Il s’agit d’une opération sans temps d’arrêt. |

## <a name="next-steps"></a>Étapes suivantes

* [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planification de la migration des ressources IaaS d’Azure Classic vers Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](migration-classic-resource-manager-ps.md)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure](migration-classic-resource-manager-cli.md)
* [Outils de la communauté pour aider à la migration de ressources IaaS de Classic vers Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Passer en revue les erreurs courantes de migration](migration-classic-resource-manager-errors.md)
* [Passez en revue les questions fréquemment posées sur la migration des ressources IaaS de Classic vers Azure Resource Manager](migration-classic-resource-manager-faq.yml)
