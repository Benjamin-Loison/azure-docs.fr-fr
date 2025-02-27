---
title: Fichier Include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/16/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a2009a1898abd8390b4a747efb10db01964f55a7
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122534777"
---
L’activation de disques partagés est disponible uniquement pour un sous-ensemble de types de disques. Seuls les disques Ultra, les disques SSD Premium et les disques SSD standard peuvent activer des disques partagés. Chaque disque managé pour lequel la fonctionnalité Disques partagés est activée est soumis aux limitations suivantes, selon le type disque :

### <a name="ultra-disks"></a>Disques Ultra

Les disques Ultra ont leur propre liste de limitations sans lien avec les disques partagés. Pour connaître les limitations des disques Ultra, reportez-vous à [Utilisation de disques Ultra Azure](../articles/virtual-machines/disks-enable-ultra-ssd.md).

Quand vous partagez des disques Ultra, ils présentent les limitations supplémentaires suivantes :

- Seuls les disques de base peuvent être utilisés avec certaines versions de la fonctionnalité Cluster de basculement de Windows Server. Pour plus d’informations, consultez [Configuration matérielle requise pour le clustering de basculement et options de stockage](/windows-server/failover-clustering/clustering-requirements).
- Seul le [chiffrement côté serveur](../articles/virtual-machines/disk-encryption.md) est pris en charge. [Azure Disk Encryption](../articles/virtual-machines/windows/disk-encryption-overview.md) n’est pas pris en charge pour le moment.

Les disques Ultra partagés sont disponibles dans toutes les régions qui prennent en charge les disques Ultra par défaut, et vous n’avez pas besoin de vous inscrire pour pouvoir les utiliser.

### <a name="premium-ssds"></a>SSD Premium

- Activation possible uniquement sur des disques de données, et non sur des disques de système d’exploitation.
- La mise en cache de l’hôte **ReadOnly** n’est pas disponible pour les disques SSD Premium avec `maxShares>1`.
- Le bursting n’est pas disponible pour les disques SSD Premium avec `maxShares>1`.
- Lorsque vous utilisez des groupes à haute disponibilité et des groupes de machines virtuelles identiques avec des disques partagés Azure, [l’alignement du domaine d’erreur de stockage](../articles/virtual-machines/availability.md) sur un domaine d’erreur de machine virtuelle n’est pas appliqué pour le disque de données partagé.
- Lorsque des [groupes de placement de proximité](../articles/virtual-machines/windows/proximity-placement-groups.md) sont utilisés, toutes les machines virtuelles qui se partagent un disque doivent faire partie du même groupe de placement de proximité.
- Seuls les disques de base peuvent être utilisés avec certaines versions de la fonctionnalité Cluster de basculement de Windows Server. Pour plus d’informations, consultez [Configuration matérielle requise pour le clustering de basculement et options de stockage](/windows-server/failover-clustering/clustering-requirements).
- La prise en charge d’Azure Site Recovery n’est pas encore disponible.
- Le service Sauvegarde Azure est disponible via [Sauvegarde des disques Azure](../articles/backup/disk-backup-overview.md).
- Seul le [chiffrement côté serveur](../articles/virtual-machines/disk-encryption.md) est pris en charge. [Azure Disk Encryption](../articles/virtual-machines/windows/disk-encryption-overview.md) n’est pas pris en charge pour le moment.

#### <a name="premium-ssd-regional-availability"></a>Disponibilité régionale SSD Premium

Les disques partagés sur l’ensemble des tailles de disques SSD premium sont disponibles dans toutes les régions où les disques managés le sont également.


### <a name="standard-ssds"></a>SSD Standard

- Activation possible uniquement sur des disques de données, et non sur des disques de système d’exploitation.
- Lorsque vous utilisez des groupes à haute disponibilité et des groupes de machines virtuelles identiques avec des disques partagés Azure, [l’alignement du domaine d’erreur de stockage](../articles/virtual-machines/availability.md) sur un domaine d’erreur de machine virtuelle n’est pas appliqué pour le disque de données partagé.
- Lorsque des [groupes de placement de proximité](../articles/virtual-machines/windows/proximity-placement-groups.md) sont utilisés, toutes les machines virtuelles qui se partagent un disque doivent faire partie du même groupe de placement de proximité.
- Seuls les disques de base peuvent être utilisés avec certaines versions de la fonctionnalité Cluster de basculement de Windows Server. Pour plus d’informations, consultez [Configuration matérielle requise pour le clustering de basculement et options de stockage](/windows-server/failover-clustering/clustering-requirements).
- La prise en charge d’Azure Site Recovery n’est pas encore disponible.
- Le service Sauvegarde Azure est disponible via [Sauvegarde des disques Azure](../articles/backup/disk-backup-overview.md).
- Seul le [chiffrement côté serveur](../articles/virtual-machines/disk-encryption.md) est pris en charge. [Azure Disk Encryption](../articles/virtual-machines/windows/disk-encryption-overview.md) n’est pas pris en charge pour le moment.

#### <a name="standard-ssd-regional-availability"></a>Disponibilité régionale SSD Standard

Les disques partagés sur l’ensemble des tailles de disques SSD standard sont disponibles dans toutes les régions où les disques managés le sont également.
