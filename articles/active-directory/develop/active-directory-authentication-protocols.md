---
title: Protocoles d’authentification de la plateforme Microsoft Identity
description: Vue d’ensemble des protocoles d’authentification pris en charge par la plateforme d’identités Microsoft
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: d489dbad512af238601636e9f0c4f144f9b2216d
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129153935"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Protocoles d’authentification de la plateforme Microsoft Identity

La plateforme d’identités Microsoft prend en charge plusieurs des protocoles d’authentification et d’autorisation les plus couramment utilisés. Les rubriques de cette section décrivent les protocoles pris en charge et leur implémentation dans la plateforme Microsoft Identity. Les rubriques comprennent une revue des types de revendications pris en charge, une présentation de l’utilisation des métadonnées de fédération, de la documentation de référence détaillée sur les protocoles OAuth 2.0. et SAML 2.0 et une section de dépannage.

## <a name="authentication-protocols-articles-and-reference"></a>Articles et références sur les protocoles d’authentification

* [Informations importantes sur la substitution des clés de signature dans la plateforme Microsoft Identity](active-directory-signing-key-rollover.md) : découvrez le rythme de substitution des clés de signature de la plateforme Microsoft Identity, les modifications que vous pouvez effectuer pour mettre la clé à jour automatiquement et une description de la mise à jour des scénarios d’application les plus courants.
* [Types de jeton et de revendication pris en charge](id-tokens.md) : découvrez les revendications des jetons émis par la plateforme d’identités Microsoft.
* [OAuth 2.0 dans la plateforme Microsoft Identity](v2-oauth2-auth-code-flow.md) - Apprenez-en davantage sur l’implémentation d’OAuth 2.0 dans la plateforme Microsoft Identity.
* [OpenID Connect 1.0](v2-protocols-oidc.md) : découvrez comment utiliser OAuth 2.0, un protocole d’autorisation, pour l’authentification.
* [Appels de service à service avec les informations d’identification du client](v2-oauth2-client-creds-grant-flow.md) : découvrez comment utiliser les flux d’octroi d’informations d’identification du client OAuth 2.0 pour les appels de service à service.
* [Appels de service à service avec le flux Pour le compte de](v2-oauth2-on-behalf-of-flow.md) : découvrez comment utiliser les flux Pour le compte de OAuth 2.0 pour les appels de service à service.
* [Informations de référence sur le protocole SAML](active-directory-saml-protocol-reference.md) : découvrez les profils SAML d’authentification unique et de déconnexion unique de la plateforme d’identité Microsoft.

## <a name="see-also"></a>Voir aussi

* [Présentation de la plateforme d’identités Microsoft](v2-overview.md)
* [Exemples de code Azure Active Directory](sample-v2-code.md)
