---
ms.author: cherylmc
author: cherylmc
ms.date: 07/29/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: d13072a03dce5aa3c7fd84ccf458b4f4fad112f1
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778487"
---
1. Dans la page de votre **WAN virtuel**, sélectionnez **Configurations de VPN utilisateur**.
1. Dans la page **Configurations de VPN utilisateur**, sélectionnez une configuration, puis sélectionnez **Télécharger le profil VPN utilisateur du WAN virtuel**.

   :::image type="content" source="./media/virtual-wan-p2s-download-profile/download.png" alt-text="Capture d’écran de téléchargement du profil VPN utilisateur du WAN virtuel.":::

   * Quand vous téléchargez la configuration de niveau WAN, vous obtenez un profil VPN utilisateur basé sur Traffic Manager intégré. 
   
   * Pour plus d’informations sur les profils globaux et les profils basés sur un hub, consultez [Profils basés sur un hub](../articles/virtual-wan/global-hub-profile.md). Les scénarios de basculement sont simplifiés avec le profil global.

   * Si un hub n’est pas disponible pour une raison ou une autre, la gestion du trafic intégré fourni par le service garantit la connectivité (via un hub différent) aux ressources Azure pour les utilisateurs point à site. Vous pouvez toujours télécharger une configuration VPN propre à un hub en accédant au hub. Sous **VPN utilisateur (point à site)** , téléchargez le profil **VPN utilisateur** du hub virtuel.
1. Dans la page **Télécharger le profil VPN utilisateur du WAN virtuel**, sélectionnez le **type d’authentification** que vous souhaitez, puis cliquez sur **Générer et télécharger un profil**. 

   Un package de profil (fichier zip) contenant les paramètres de configuration du client est généré et téléchargé sur votre ordinateur.

   :::image type="content" source="./media/virtual-wan-p2s-download-profile/generate.png" alt-text="Capture d’écran de génération et de téléchargement du profil.":::

