---
title: Intégrité et sécurité de la plateforme Azure - Sécurité Azure
description: Vue d’ensemble technique de l’intégrité et de la sécurité de la plateforme Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: e0522d6dcb02571a1ed197d60734906ee8131ab4
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112893957"
---
# <a name="platform-integrity-and-security-overview"></a>Vue d’ensemble de l’intégrité et de la sécurité de la plateforme
Le parc Azure est composé de millions de serveurs (hôtes) avec des milliers d’ordinateurs supplémentaires ajoutés quotidiennement. Des milliers d’hôtes subissent également une maintenance quotidienne via des redémarrages, des actualisations du système d’exploitation ou des réparations. Avant qu’un hôte puisse rejoindre le parc et commencer à accepter les charges de travail de clients, Microsoft vérifie que l’hôte est dans un état sécurisé et fiable. Cette vérification garantit que des modifications malveillantes ou involontaires n’ont pas été effectuées sur les composants de la séquence de démarrage tout au long de la chaîne d’approvisionnement ou des workflows de maintenance.

## <a name="securing-azure-hardware-and-firmware"></a>Sécurisation du matériel et des microprogrammes Azure
Cette série d’articles décrit comment Microsoft garantit l’intégrité et la sécurité des hôtes à travers les différentes étapes de leur cycle de vie, de la fabrication à la mise hors service. Il aborde les sujets suivants :
 
- [Sécurité de microprogramme](firmware.md)
- [Intégrité du code de la plateforme](code-integrity.md)
- [Démarrage sécurisé UEFI](secure-boot.md)
- [Démarrage mesuré et attestation de l’hôte](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Chiffrement au repos](encryption-atrest.md)
- [Sécurité de l’hyperviseur](hypervisor.md)
 
## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment Microsoft travaille activement en partenariat dans l’écosystème matériel du cloud pour [améliorer la sécurité des microprogrammes](firmware.md) de façon continue.

- Comprendre votre [responsabilité partagée dans le cloud](shared-responsibility.md).