---
title: Problèmes rencontrés lors de la création d'une application Proxy d'application Azure Active Directory
description: Résoudre les problèmes de création d'applications Proxy d'application sur le portail d'administration Azure Active Directory
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 513d75a0f5b2a0b0faf283000909e239f706d702
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990403"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problèmes lors de la création d’une application de proxy d’application 

Cet article aborde certains problèmes courants auxquels sont confrontés les utilisateurs lors de la création d’une application de proxy d’application.

## <a name="recommended-documents"></a>Documents recommandés 

Pour en savoir plus sur la création d’une application de proxy d’application par le biais du portail d’administration, consultez [Publier des applications avec le Proxy d’application Azure AD](application-proxy-add-on-premises-application.md).

Si vous suivez les étapes décrites dans ce document et si vous obtenez une erreur lors de création de l’application, consultez les détails de l’erreur pour en savoir plus et découvrir des suggestions afin de corriger l’application. La plupart des messages d’erreur incluent la suggestion d’un correctif. 

## <a name="specific-things-to-check"></a>Éléments spécifiques à vérifier

Pour éviter les erreurs courantes, vérifiez les points suivants :

-   Vous êtes un administrateur autorisé à créer une application de proxy d’application

-   L’URL interne est unique

-   L’URL externe est unique

-   Les URL commencent par http ou https et se terminent par un signe « / »

-   L’URL doit être un nom de domaine et non une adresse IP

Le message d’erreur s’affiche en haut à droite lorsque vous créez l’application. Vous pouvez également sélectionner l’icône de notification pour afficher les messages d’erreur.

   ![Invite de notification](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Étapes suivantes
[Activer le proxy d’application dans le portail Azure](application-proxy-add-on-premises-application.md)
