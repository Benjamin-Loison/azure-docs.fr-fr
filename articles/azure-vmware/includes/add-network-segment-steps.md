---
title: Ajouter un segment réseau NSX-T
description: Étapes à suivre pour ajouter un segment réseau NSX-T pour Azure VMware Solution dans NSX-T Manager.
ms.topic: include
ms.date: 03/13/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 1e6d50e1ca05ea15c4d6690965c112565dae76f5
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638778"
---
<!-- Used in configure-dhcp-azure-vmware-solution.md and tutorial-nsx-t-network-segment.md -->

1. Dans NSX-T Manager, sélectionnez **Networking** (Réseaux)  > **Segments**, puis sélectionnez **Add Segment** (Ajouter un segment). 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Capture d’écran montrant comment ajouter un nouveau segment dans NSX-T Manager.":::

1. Entrez un nom pour le segment.

1. Sélectionnez la passerelle de niveau 1 (TNTxx-T1) comme valeur pour **Connected Gateway** (Passerelle connectée) et laissez Flexible comme **Type**.

1. Sélectionnez la zone de transport (**Transport Zone**) de superposition préconfigurée (TNTxx-OVERLAY-TZ), puis sélectionnez **Set Subnets** (Définir des sous-réseaux). 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Capture d’écran montrant les détails des segments pour l’ajout d’un nouveau segment de réseau NSX-T.":::

1. Entrez l’adresse IP de la passerelle, puis sélectionnez **Ajouter**. 

   >[!IMPORTANT]
   >L’adresse IP doit se trouver dans un bloc d’adresses RFC1918 sans chevauchement, ce qui garantit la connexion aux machines virtuelles sur le nouveau segment.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Capture d’écran montrant l’adresse IP de la passerelle pour le nouveau segment.":::

1. Sélectionnez **Apply** (Appliquer), puis **Save** (Enregistrer).

1. Sélectionnez **No** (Non) pour refuser l’option qui permet de continuer la configuration du segment. 


