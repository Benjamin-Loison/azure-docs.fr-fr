---
title: Découvrir l’adresse IP de point de terminaison de gestion
titleSuffix: Azure SQL Managed Instance
description: Découvrir comment obtenir l’adresse IP publique du point de terminaison de gestion Azure SQL Managed Instance et vérifier la protection de son pare-feu intégré
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma
ms.date: 12/04/2018
ms.openlocfilehash: 8603f026d396316edf9d83e52ef33973ddc8edd7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525309"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>Déterminer l’adresse IP du point de terminaison de gestion - Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Le cluster virtuel Azure SQL Managed Instance contient un point de terminaison de gestion qu'Azure utilise pour les opérations de gestion. Le point de terminaison de gestion est protégé par un pare-feu intégré au niveau du réseau et par la vérification de certificat mutuelle au niveau de l’application. Vous pouvez déterminer l’adresse IP du point de terminaison de gestion, mais vous ne pouvez pas accéder à ce dernier.

Pour déterminer l’adresse IP de gestion, effectuez une [recherche DNS](/windows-server/administration/windows-commands/nslookup) sur le nom de domaine complet de votre instance SQL Managed Instance : `mi-name.zone_id.database.windows.net`. Cette opération retourne une entrée DNS similaire à `trx.region-a.worker.vnet.database.windows.net`. Vous pouvez ensuite effectuer une recherche DNS sur ce nom de domaine complet avec la partie « . vnet » en moins. L’adresse IP de gestion est alors retournée. 

Ce code PowerShell le fera pour vous si vous remplacez \<MI FQDN\> par l’entrée DNS de SQL Managed Instance : `mi-name.zone_id.database.windows.net` :
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Pour plus d’informations sur SQL Managed Instance et la connectivité, consultez [Architecture de la connectivité Azure SQL Managed Instance](connectivity-architecture-overview.md).
