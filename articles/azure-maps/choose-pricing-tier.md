---
title: Choisir le bon niveau tarifaire pour Microsoft Azure Maps
description: En savoir plus sur les niveaux tarifaires de Azure Maps. Découvrez les fonctionnalités proposées aux niveaux et les principales considérations à prendre en compte pour le choix d’un niveau tarifaire.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/11/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: ef61b0c8a52e37b5eb73f38ab63cc97e66bf7e4e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398912"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Choix du bon niveau tarifaire dans Azure Maps

Azure Maps propose maintenant deux niveaux tarifaires : Gen1 et Gen2. Le niveau tarifaire Gen2 contient toutes les fonctionnalités d’Azure Maps avec augmentation des limites de requêtes par seconde par rapport à Gen1. Il vous permet de réaliser des économies en cas d’augmentation du volume de transactions Azure Maps. Cet article a pour objectif de vous aider à choisir le niveau tarifaire qui répond le mieux à vos besoins.

## <a name="pricing-tier-targeted-customers"></a>Niveau tarifaire et clients ciblés

Pour mieux comprendre les niveaux tarifaires Gen1 et Gen2, consultez le tableau des **clients ciblés par les niveaux tarifaires** ci-dessous.  Pour plus d’informations, consultez la page [Tarification Azure Maps](https://aka.ms/CreatorPricing). Si vous êtes actuellement client Azure Maps, découvrez comment passer du tarif Gen1 au tarif Gen2 dans l’article [Gérer le niveau tarifaire](how-to-manage-pricing-tier.md).

| Niveau tarifaire  | SKU | Clients ciblés|
|---------------|:---:| ------------------|
|**Gen1**|S0| Le niveau tarifaire S0 convient pout toutes les phases de production des applications : du développement « preuve de concept » à la phase de test initiale, en passant par la mise en production et le déploiement. Toutefois, ce niveau est conçu pour un développement à petite échelle, des clients avec peu d’utilisateurs simultanés, ou les deux. S0 a une restriction de 50 requêtes par seconde pour tous les services combinés.
|         |S1| Le niveau tarifaire S1 convient aux clients qui ont des applications d’entreprise à grande échelle, des applications stratégiques ou un grand nombre d’utilisateurs simultanés. Il convient également aux clients qui ont besoin de services géospatiaux avancés.
| **Gen2** | Maps/Location Insights | Le tarif Gen2 est destiné aux nouveaux clients et clients actuels Azure Maps. Il s’accompagne d’un niveau mensuel gratuit de transactions à utiliser pour tester et exploiter des cartes Azure Maps. Les références SKU Maps et Location Insights contiennent toutes les fonctionnalités d’Azure Maps. Cela vous permet de réaliser des économies au fur et à mesure que le volume des transactions Azure Maps augmente. En outre, il existe des limites de requêtes par seconde supérieures par rapport à Gen1. Le niveau tarifaire Gen 2 est requis lors de l’utilisation du [Créateur pour cartes d’intérieur](creator-indoor-maps.md).
|     |  |

Pour plus d’informations sur les limites de requêtes par seconde, reportez-vous à [Limites du taux de requêtes par seconde Azure Maps](azure-maps-qps-rate-limits.md).

Pour obtenir des informations supplémentaires sur la tarification du [Créateur pour cartes d’intérieur](creator-indoor-maps.md), consultez la section *Créateur* dans [Tarification d’Azure Maps](https://aka.ms/CreatorPricing).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’affichage et la modification des niveaux tarifaires :

> [!div class="nextstepaction"]
> [Gérer un niveau tarifaire](how-to-manage-pricing-tier.md)
