---
title: Confidentialité des utilisateurs et authentification unique fluide Azure AD | Microsoft Docs
description: Cet article traite de l’authentification unique fluide Azure Active Directory (Azure AD) et de la conformité RGPD.
services: active-directory
keywords: Qu’est-ce qu’Azure AD Connect, RGPD, Composants requis pour Azure AD, SSO, Authentification unique
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21d4253da1d3e4b190cfad92349ee885541766c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067982"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Confidentialité des utilisateurs et authentification unique fluide Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Vue d’ensemble


L’authentification unique transparente Azure AD crée le type de journal suivant, pouvant contenir des Données personnelles : 

- Fichiers journaux des traces Azure AD Connect

Améliore la confidentialité de l'utilisateur pour l’authentification unique transparente de deux façons :

1.  Sur demande, en extrayant les données d’une personne, puis en supprimant ces données des installations
2.  En garantissant qu’aucune donnée n’est conservée plus de 48 heures

Nous recommandons vivement la deuxième option, car elle est plus facile à implémenter et à gérer. Voici les instructions pour chaque type de journal :

### <a name="delete-azure-ad-connect-trace-log-files"></a>Supprimer les fichiers journaux des traces Azure AD Connect

Vérifiez le contenu du dossier **%ProgramData%\AADConnect** et supprimez le contenu des journaux de traces (fichiers **trace -\*.log**) de ce dossier dans les 48 heures suivant l’installation ou la mise à niveau d’Azure AD Connect ou la modification de la configuration de l’authentification unique fluide, car cette action peut créer des données couvertes par la réglementation RGPD.

>[!IMPORTANT]
>Ne supprimez pas le fichier **PersistedState.xml** de ce dossier, car il est sert à conserver l’état de l’installation précédente d’Azure AD Connect et est utilisé quand une mise à niveau est effectuée. Ce fichier ne contiendra jamais de données relatives à un individu et ne doit jamais être supprimé.

Vous pouvez passer en revue et supprimer ces fichiers journaux des traces à l’aide de l’Explorateur Windows, ou vous pouvez utiliser le script PowerShell suivant pour effectuer les actions nécessaires :

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Enregistrez le script dans un fichier avec l’extension « .PS1 ». Exécutez ce script selon les besoins.

Pour en savoir plus sur les exigences RGPD relatives à Azure AD Connect, consultez [cet article](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Remarque concernant les journaux d’activité des contrôleurs de domaine

Si l’enregistrement d’audit est activé, ce produit peut générer des journaux d’activité de sécurité pour vos contrôleurs de domaine. Pour en savoir plus sur la configuration des stratégies d’audit, consultez cet [article](/previous-versions/tn-archive/dd277403(v=technet.10)).

## <a name="next-steps"></a>Étapes suivantes

* [Lire la politique de confidentialité Microsoft sur le Centre de confidentialité](https://www.microsoft.com/trustcenter)
  - [**Résolution des problèmes**](tshoot-connect-sso.md) : découvrez comment résoudre les problèmes courants susceptibles de survenir avec cette fonctionnalité.
  - [**UserVoice**](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789) : pour le dépôt de nouvelles demandes de fonctionnalités.