---
title: Fichier include
description: Fichier include
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 8b09c744cb531dd8834f68a1d8fcdacfa48c2504
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287660"
---
> [!div class="op_single_selector"]
> * [Azure portal](../articles/virtual-network/ip-services/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/ip-services/virtual-network-multiple-ip-addresses-powershell.md)
> * [Azure CLI](../articles/virtual-network/ip-services/virtual-network-multiple-ip-addresses-cli.md)
>

Une ou plusieurs cartes réseau sont attachées à une machine virtuelle Azure. Une ou plusieurs adresses IP publiques ou privées, statiques ou dynamiques, peuvent être affectées à chaque carte réseau. L’affectation de plusieurs adresses IP à une machine virtuelle permet :

* D’héberger plusieurs sites web ou services avec des adresses IP différentes et des certificats SSL sur un serveur unique.
* de jouer le rôle d’une appliance virtuelle réseau, telle qu’un pare-feu ou un équilibreur de charge.
* La possibilité d’ajouter l’une des adresses IP privées pour toutes les cartes réseau à un pool principal Azure Load Balancer. Auparavant, seule l’adresse IP principale de la carte réseau principale pouvait être ajoutée à un pool principal. Pour plus d’informations sur la façon d’équilibrer la charge entre plusieurs configurations IP, consultez l’article [Équilibrage de la charge entre plusieurs configurations IP](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Une ou plusieurs configurations IP sont associées à chaque carte réseau attachée à une machine virtuelle. Une adresse IP privée statique ou dynamique est affectée à chaque configuration. Une ressource d’adresse IP publique peut également être associée à chaque configuration. Une adresse IP publique statique ou dynamique est affectée à une ressource d’adresse IP publique. Pour plus d’informations sur les adresses IP dans Azure, consultez l’article [Adresses IP dans Azure](../articles/virtual-network/ip-services/public-ip-addresses.md). 

Le nombre d’adresses IP privées pouvant être affectées à une carte réseau est limité. Le nombre d’adresses IP publiques pouvant être utilisées dans un abonnement Azure est également limité. Consultez l’article sur les [limites Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).