---
title: Autorisations du plan Azure DDoS Protection
description: Découvrez comment gérer les autorisations dans un plan de protection.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 30a081ab25e5c7918e7253649983ddea54ddf2b4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439305"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>Gérer les plans de protection DDoS : autorisations et restrictions

Un plan de protection DDoS fonctionne dans les régions et les abonnements. Le même plan peut être associé à des réseaux virtuels d’autres abonnements dans différentes régions, dans votre locataire. L’abonnement auquel le plan est associé subit la facturation mensuelle périodique du plan, ainsi que les frais de dépassement, si le nombre d’adresses IP publiques protégées est supérieur à 100. Pour en savoir plus sur la tarification du service de protection DDoS, consultez la rubrique relative aux [détails de tarification](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="prerequisites"></a>Prérequis

- Avant de pouvoir effectuer les étapes de ce tutoriel, vous devez d’abord créer un [plan de protection Azure DDoS Standard](manage-ddos-protection.md).

## <a name="permissions"></a>Autorisations

Pour que vous puissiez travailler avec des plans de protection DDoS, il est nécessaire que votre compte ait le rôle [contributeur réseau](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou un rôle [personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) disposant des autorisations appropriées, listées dans le tableau suivant :

| Action                                            | Nom                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Lire un plan de protection DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Créer ou mettre à jour un plan de protection DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Supprimer un plan de protection DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Joindre un plan de protection DDoS              |

Pour que vous puissiez activer la protection DDoS sur un réseau virtuel, il est nécessaire que votre compte dispose des [autorisations appropriées pour les réseaux virtuels](../virtual-network/manage-virtual-network.md#permissions).

> [!IMPORTANT]
> Une fois qu’un plan de protection DDoS a été activé sur un réseau virtuel, les opérations suivantes sur ce réseau virtuel nécessitent toujours l'`Microsoft.Network/ddosProtectionPlans/join/action` autorisation action.

## <a name="azure-policy"></a>Azure Policy

La création de plusieurs plans n’est pas requise pour la plupart des organisations. Un plan ne peut pas être déplacé d’un abonnement vers un autre. Si vous souhaitez changer l’abonnement auquel un plan est rattaché, vous devez supprimer le plan existant et en créer un.

Pour les clients qui disposent de plusieurs abonnements différents et qui souhaitent déployer un seul et même plan sur l’ensemble de leur locataire pour maîtriser les coûts, vous pouvez utiliser Azure Policy afin de [restreindre la création de plans Azure DDoS Protection Standard](https://aka.ms/ddosrestrictplan). Cette stratégie bloque la création de plans DDoS, sauf si l’abonnement a été marqué comme une exception. Cette stratégie affiche également la liste de tous les abonnements pour lesquels un plan DDoS est déployé alors qu’il ne devrait pas l’être, en les signalant comme non conformes.


## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment consulter et configurer la télémétrie pour votre plan de protection DDoS, passez aux tutoriels.

> [!div class="nextstepaction"]
> [Consulter et configurer la télémétrie de la protection DDoS](telemetry.md)
