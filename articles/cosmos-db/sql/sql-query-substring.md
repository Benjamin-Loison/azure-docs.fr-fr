---
title: 'Langage de requête Azure Cosmos DB : SUBSTRING'
description: Découvrez la fonction système SQL SUBSTRING dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c4df749de8c80b4b81410693c848b8a3c669f77d
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533179"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Renvoie une partie d’une expression de chaîne commençant à la position de caractère spécifiée (avec base zéro) et se poursuit jusqu'à la longueur spécifiée ou à la fin de la chaîne.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr*  
   Est une expression de chaîne.
  
*num_expr1*  
   Est une expression numérique indiquant le caractère de début. La valeur 0 est le premier caractère de *str_expr*.
  
*num_expr2*  
   Est une expression numérique indiquant le nombre maximal de caractères de *str_expr* à retourner. La valeur 0 ou une valeur inférieure génère une chaîne vide.

## <a name="return-types"></a>Types de retour
  
  Retourne une expression de chaîne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant retourne la sous-chaîne de « abc » commençant à 1 pour une longueur de 1 caractère.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Notes

Cette fonction système bénéficiera d’un [index de plage](../index-policy.md#includeexclude-strategy) si sa position de départ est `0`.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
