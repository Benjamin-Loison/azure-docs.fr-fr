---
title: Obtenir les points de terminaison pour une inscription d’application Azure AD
titleSuffix: Microsoft identity platform
description: Guide pratique pour trouver les points de terminaison d’authentification pour une application personnalisée que vous développez ou que vous inscrivez auprès d’Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 6d557a35c9e98a8941a94d5871f578101d935580
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129153745"
---
# <a name="how-to-discover-endpoints"></a>Comment découvrir des points de terminaison

Vous pouvez trouver les points de terminaison d’authentification pour votre application dans le [portail Azure](https://portal.azure.com).

1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</a>.
1. Sélectionnez **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Inscriptions d’applications**, puis sélectionnez **Points de terminaison** dans le menu supérieur.

    La page **Points de terminaison** s’affiche et présente tous les points de terminaison d’authentification pour votre locataire.
    
    Utilisez le point de terminaison qui correspond au protocole d’authentification que vous utilisez en combinaison avec l’**ID d’application (client)** pour créer la demande d’authentification spécifique à votre application.

Les **clouds nationaux** (comme Azure AD Chine, Allemagne ou US Government) disposent de leur propre portail d’inscription d’application, ainsi que de leurs propres points de terminaison d’authentification Azure AD. Pour plus d’informations, consultez la [présentation des clouds nationaux](authentication-national-cloud.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les points de terminaison dans les différents environnements Azure, consultez la [Vue d’ensemble des clouds nationaux](authentication-national-cloud.md).
