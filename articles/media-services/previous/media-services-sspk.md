---
title: Licence du kit de portage du client Microsoft&reg; Smooth Streaming
description: En savoir plus sur la licence du kit de portage du client Microsoft&reg; Smooth Streaming.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: xpouyat
ms.openlocfilehash: 0311948d679cee38dcc7113e4c0246c757409d95
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233444"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>Licence du kit de portage du client Microsoft&reg; Smooth Streaming

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
## <a name="overview"></a>Vue d’ensemble
Le kit de portage client Smooth Streaming Microsoft (**SSPK** en abrégé) est une implémentation du client Smooth Streaming optimisée pour aider les fabricants de périphériques intégrés, les opérateurs mobiles et du câble, les fournisseurs de services de contenu, les fabricants de combinés, les éditeurs de logiciels indépendants (ISV) et les fournisseurs de solutions à créer des produits et des services pour diffuser du contenu adaptatif dans un format Smooth Streaming. SSPK est une implémentation Smooth Streaming cliente indépendante de l’appareil et de la plateforme pouvant être transférée par le titulaire de la licence vers n’importe quel appareil ou plateforme. 

Vous trouverez ci-après une architecture de haut niveau, et le package du kit de portage Smooth Streaming IIS est l’implémentation de client Smooth Streaming fournie par Microsoft. Il inclut la logique de base pour une lecture de contenu Smooth Streaming. Ce contenu est ensuite transféré par des partenaires pour un appareil ou une plateforme spécifique, grâce à l’implémentation des interfaces appropriées. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Description
SSPK est concédé sous licence selon des conditions représentant une excellente valeur pour l’entreprise. La licence SSPK offre au secteur :

* Source de kit de portage Smooth Streaming en C++ 
  * implémente la fonctionnalité Client Smooth Streaming
  * ajoute une analyse de format, heuristique, une logique de mise en mémoire tampon, etc.
* API d’application joueur 
  * interfaces de programmation pour une interaction avec une application de lecteur multimédia
* Interface de couche d’abstraction de plateforme (PAL) 
  * interfaces de programmation pour l‘interaction avec le système d‘exploitation (threads, sockets)
* Interface de couche d’abstraction matérielle 
  * interfaces de programmation pour l‘interaction avec les décodeurs A/V matériels (décodage, rendu)
* Interface de gestion de droits numériques (Digital Rights Management - DRM) 
  * interfaces de programmation pour la gestion des DRM via la couche Abstraction DRM (DAL)
  * Le kit de portage PlayReady Microsoft est fourni séparément, mais s’intègre via cette interface. [Découvrez des informations supplémentaires sur les licences pour appareil Microsoft PlayReady](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Échantillons de l’implémentation 
  * exemple d‘implémentation PAL pour Linux
  * exemple d‘implémentation HAL pour GStreamer

## <a name="licensing-options"></a>Options de licence
Le kit de portage Smooth Streaming client est accessible aux détenteurs de licences sous forme de deux contrats de licence distincts : l’un correspondant au développement des produits intermédiaires Smooth Streaming client, et l’autre pour la distribution de produits Smooth Streaming finaux pour les clients finaux.

* Pour les fabricants de puces, les intégrateurs système ou les fournisseurs de logiciels indépendants (ISV) qui ont besoin d‘un kit de portage de code source pour développer des produits intermédiaires, un kit de portage client Smooth Streaming Microsoft, **Licence produit intermédiaire** doit être exécutée.
* Pour les fabricants de périphériques ou les éditeurs de logiciels qui exigent des droits de distribution pour les produits de Smooth Streaming pour client final, c’est le kit de portage client Smooth Streaming **licence du produit Final** qui doit être exécuté.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Licence de produit intermédiaire pour le kit Microsoft Smooth Streaming Client Porting
Sous cette licence, Microsoft propose un kit de portage Smooth Streaming client et les droits de propriété intellectuelle nécessaires pour développer et distribuer les produits intermédiaires de client de Smooth Streaming pour les autres titulaires de licences de kit de portage Smooth Streaming client les produits finaux client de Smooth Streaming.

#### <a name="fee-structure"></a>Structure des frais
Des frais de licence de 50 000 dollars US payables en une fois offrent un accès au kit de portage Smooth Streaming client. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Licence de produit final pour le kit de portage Smooth Streaming client
Avec cette licence, Microsoft offre les droits de propriété intellectuelle nécessaires pour recevoir les produits intermédiaires de client de Smooth Streaming de la part d’autres titulaires de licences de kit de portage Smooth Streaming client et distribuer des produits Smooth Streaming à la marque de la société en continu lisse pour les clients finaux.

#### <a name="fee-structure"></a>Structure des frais
Le produit client final de Smooth Streaming est proposé selon un modèle soumis à redevance :

* 0,10 $ par implémentation de périphérique expédiés
* La redevance est limitée à 50 000 dollars chaque année
* Pas de redevance pour les 10 000 premières implémentations de périphérique chaque année 

## <a name="licensing-procedure-and-sspk-access"></a>Procédure d’achat de licence et accès à SSPK
Veuillez envoyer un e-mail à [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) pour toute requête relative aux licences.

Le portail de distribution SSPK Distribution est accessible aux détenteurs de licences intermédiaires enregistrés.

Les titulaires de licence SSPK intermédiaire ou finale peuvent soumettre des questions techniques à [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Les titulaires de licence d’accord de produit intermédiaire de client de diffusion lisse Microsoft
Consultez la liste actuelle des titulaires de licence ici : https://go.microsoft.com/fwlink/?linkid=301271.

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Titulaires de licence d’accord de produit final de client de diffusion lisse Microsoft
Consultez la liste actuelle des titulaires de licence ici : https://go.microsoft.com/fwlink/?linkid=301271.

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

