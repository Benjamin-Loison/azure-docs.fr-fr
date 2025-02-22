---
title: API Supprimer des requêtes de rapport
description: Utilisez cette API afin de supprimer les requêtes définies par l’utilisateur pour l’analyse de la place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 927534b11bc00b3ec21db2ab5efef2eb09104976
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563176"
---
# <a name="delete-report-queries-api"></a>API Supprimer des requêtes de rapport

Cette API supprime les requêtes définies par l’utilisateur.

**Syntaxe de la requête**

| **Méthode** | **URI de demande** |
| --- | --- |
| Suppression | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

**En-tête de requête**

| **En-tête** | **Type** | **Description** |
| --- | --- | --- |
| Autorisation | string | Obligatoire. Le jeton d’accès Azure Active Directory (Azure AD) au format `Bearer <token>` |
| Content-Type | string | `Application/JSON` |

**Paramètre de chemin**

| **Nom du paramètre** | **Type** | **Description** |
| --- | --- | --- |
| `queryId` | string | Filtre permettant d’obtenir les détails des seules requêtes avec l’ID fourni dans cet argument |

**Paramètre de requête**

Aucun

**Charge utile de la requête**

Aucun

**Glossaire**

Aucune

**Réponse**

La charge utile de réponse est structurée comme suit au format JSON.

Code de réponse : 200, 400, 401, 403, 404, 500

Charge utile de réponse :

```json
{
  "Value": [
    {
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Glossaire**

Ce tableau liste les définitions clés des éléments dans la réponse.

| **Paramètre** | **Description** |
| --- | --- |
| `QueryId` | UUID unique de la requête supprimée. |
| `Name` | Nom de la requête supprimée |
| `Description` | Description de la requête supprimée |
| `Query` | Chaîne de requête de rapport de la requête supprimée |
| `Type` | userDefined |
| `User` | ID de l’utilisateur qui a créé la requête |
| `CreatedTime` | Heure de création de la requête |
| `ModifiedTime` | Null |
| `TotalCount` | Nombre de jeux de données dans le tableau de valeur |
| `StatusCode` | Code de résultat. Les valeurs possibles sont 200, 400, 401, 403, 500 |
