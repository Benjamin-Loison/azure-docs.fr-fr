---
title: Azure VMware Solution by CloudSimple - Service
description: Découvrez le service CloudSimple avec sa présentation. En créant ce service, vous pouvez acheter, réserver des nœuds et créer des clouds privés.
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 940b9e67bb10c33a76f6b854d7cf03f3a7a6ee49
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315118"
---
# <a name="cloudsimple-service-overview"></a>Vue d’ensemble du service CloudSimple

Le service CloudSimple vous permet d’utiliser Azure VMware Solution by CloudSimple.  En créant ce service, vous pouvez acheter, réserver des nœuds et créer des clouds privés.  Vous créez le service de CloudSimple à chaque région Azure où ce service est disponible. Le service définit le réseau de périmètre d’Azure VMware Solution by CloudSimple. Le réseau de périmètre prend en charge les services comprenant une connectivité VPN, ExpressRoute et Internet vers vos clouds privés.

## <a name="gateway-subnet"></a>Sous-réseau de passerelle

Un sous-réseau de passerelle est requis par le service CloudSimple et est propre à la région dans laquelle il est créé. Le sous-réseau de passerelle est utilisé lors de la création du réseau de périmètre et requiert un bloc CIDR /28.  L’espace d’adressage du sous-réseau de passerelle doit être unique. Il ne doit chevaucher aucun réseau qui communique avec l’environnement CloudSimple. Les réseaux qui communiquent avec CloudSimple incluent les réseaux locaux et le Réseau virtuel Azure.  Un sous-réseau de passerelle ne peut pas être supprimé après sa création.  Le sous-réseau de passerelle est supprimé lorsque le service est supprimé.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un service CloudSimple sur Azure](quickstart-create-cloudsimple-service.md).
