---
title: Exemples PowerShell pour proxy d’application Azure Active Directory
description: Utilisez ces exemples PowerShell pour proxy d’application Azure Active Directory afin d’obtenir des informations sur les applications et connecteurs de proxy d’application dans votre annuaire, d’affecter des utilisateurs et des groupes à des applications et d’obtenir des informations sur les certificats.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: sample
ms.date: 04/29/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: cd37d281ce3c6506bb09a650d215240d5208dacc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988940"
---
# <a name="azure-active-directory-application-proxy-powershell-examples"></a>Exemples PowerShell pour proxy d’application Azure Active Directory

Le tableau suivant contient des liens vers des exemples de scripts PowerShell pour le proxy d’application Azure AD. Ces exemples requièrent [AzureAD v2 PowerShell pour le module Graph](/powershell/azure/active-directory/install-adv2) ou [AzureAD v2 PowerShell pour la version préliminaire du module Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), sauf indication contraire.


Pour plus d’informations sur les applets de commande utilisées dans ces exemples, consultez [Gestion des applications de proxy d’application](/powershell/module/azuread/#application_proxy_application_management) et [Gestion des connecteurs de proxy d’application](/powershell/module/azuread/#application_proxy_connector_management).

| Lien | Description |
|---|---|
|**Applications de proxy d’application**||
| [Répertorier les informations de base pour toutes les applications de proxy d’application](scripts/powershell-get-all-app-proxy-apps-basic.md) | Liste les informations de base (AppId, DisplayName, ObjId) de toutes les applications de proxy d’application de votre répertoire. |
| [Répertorier les informations étendues pour toutes les applications de proxy d’application](scripts/powershell-get-all-app-proxy-apps-extended.md) | Répertorie les informations étendues (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) relatives à toutes les applications de proxy d’application de votre répertoire.  |
| [Répertorier toutes les applications de proxy d’application par groupe de connecteurs](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Répertorie des informations sur toutes les applications de proxy d’application de votre répertoire et les groupes de connecteurs auxquels les applications sont affectées. |
| [Obtenir toutes les applications de proxy d’application avec une stratégie de durée de vie de jeton](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Répertorie toutes les applications de proxy d’application de votre répertoire avec une stratégie de durée de vie des jetons et ses détails. Cet exemple nécessite la [version en préversion du module AzureAD v2 PowerShell pour Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true). |
|**Groupes de connecteurs**||
| [Récupérer tous les connecteurs et groupes de connecteurs dans le répertoire](scripts/powershell-get-all-connectors.md) | Répertorie tous les connecteurs et groupes de connecteurs dans votre répertoire. |
| [Déplacer toutes les applications affectées à un groupe de connecteurs vers un autre groupe de connecteurs](scripts/powershell-move-all-apps-to-connector-group.md) | Déplace toutes les applications actuellement affectées à un groupe de connecteurs vers un autre groupe de connecteurs. |
|**Utilisateurs et groupe affectés**||
| [Afficher les utilisateurs et les groupes affectés à une application de proxy d’application](scripts/powershell-display-users-group-of-app.md) | Répertorie les utilisateurs et les groupes affectés à une application de proxy d’application spécifique. |
| [Affecter un utilisateur à une application](scripts/powershell-assign-user-to-app.md) | Affecte un utilisateur spécifique à une application. |
| [Affecter un groupe à une application](scripts/powershell-assign-group-to-app.md) | Affecte un groupe spécifique à une application. |
|**Configuration de l’URL externe**||
| [Récupérer toutes les applications de proxy d’application à l’aide de domaines par défaut (. msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Répertorie toutes les applications de proxy d’application à l’aide de domaines par défaut (. msappproxy.net). |
| [Récupérer toutes les applications de proxy d’application à l’aide de la publication générique](scripts/powershell-get-all-wildcard-apps.md) | Répertorie toutes les applications de proxy d’application à l’aide de la publication de caractères génériques. |
|**Configuration de domaine personnalisée**||
| [Répertorier toutes les applications de proxy d’application utilisant des domaines personnalisés et informations de certificat](scripts/powershell-get-all-custom-domains-and-certs.md) | Répertorie toutes les applications de proxy d’application qui utilisent des domaines personnalisés et des informations de certificat associées aux domaines personnalisés. |
| [Répertorier toutes les applications d’application proxy Azure AD publiées sans certificat chargé](scripts/powershell-get-all-custom-domain-no-cert.md) | Liste toutes les applications de proxy d’application qui utilisent des domaines personnalisés, mais n’ont pas de certificat TLS/SSL valide chargé. |
| [Récupérer toutes les applications d’application Azure AD proxy publiées avec le même certificat](scripts/powershell-get-custom-domain-identical-cert.md) | Répertorie toutes les applications d’application proxy Azure AD publiées avec le certificat identique. |
| [Récupérer toutes les applications d’application Azure AD proxy publiées avec le certificat identique et remplacement](scripts/powershell-get-custom-domain-replace-cert.md) | Pour les applications Azure AD proxy qui sont publiées avec un certificat identique, vous permet de remplacer le certificat en bloc. |
