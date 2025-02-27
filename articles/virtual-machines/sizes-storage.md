---
title: Tailles des machines virtuelles Azure – Stockage | Microsoft Docs
description: Répertorie les différentes tailles optimisées pour le stockage disponibles pour les machines virtuelles dans Azure. Liste des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
ms.subservice: vm-sizes-storage
documentationcenter: ''
author: sasha-melamed
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: a6210583dc450fa31cd0e76c158cdab40e1c578b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695739"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Tailles de machine virtuelle à stockage optimisé

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

> [!TIP]
> Essayez l’ **[outil de sélection des machines virtuelles](https://aka.ms/vm-selector)** pour trouver d’autres tailles mieux adaptées à votre charge de travail.

Les tailles de machines virtuelles à stockage optimisé offrent un débit de disque et d’E/S élevé. Elles sont idéales pour les bases de données Big Data, SQL, NoSQL ainsi que pour l’entreposage de données et les bases de données transactionnelles volumineuses.  Cassandra, MongoDB, Cloudera et Redis en sont des exemples. Cet article fournit des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur le débit de stockage local et la bande passante réseau pour chaque taille optimisée.

La [série Lsv2](lsv2-series.md) offre un stockage NVMe local directement mappé, à haut débit et faible latence. Elle est dotée du [processeur AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) disposant d’une augmentation de la fréquence de tous les cœurs de 2,55 GHz et d’une augmentation maximale de 3 GHz. Les machines virtuelles de la série Lsv2 comprennent entre 8 et 80 processeurs virtuels dans une configuration de multithreading simultané.  Il existe 8 Gio de mémoire par processeur virtuel et un périphérique NVMe SSD M.2 de 1,92 To pour 8 processeurs virtuels, avec une limite maximale de 19,2 To (10 x 1,92 To) sur le L80s v2.

## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.

Découvrez comment optimiser les performances sur les machines virtuelles de la série Lsv2 pour [Windows](windows/storage-performance.md) ou [Linux](linux/storage-performance.md).

Pour plus d’informations sur la façon dont Azure nomme ses machines virtuelles, consultez [Conventions de nommage des tailles de machines virtuelles Azure](./vm-naming-conventions.md).