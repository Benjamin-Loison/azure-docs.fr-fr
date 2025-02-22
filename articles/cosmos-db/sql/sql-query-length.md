---
title: 'Langage de requête Azure Cosmos DB : LENGTH'
description: Découvrez la fonction système SQL LENGTH dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0603f9c7ff79f9aba1766819575e31dfb848bbb8
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525628"
---
# <a name="length-azure-cosmos-db"></a>LENGTH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Retourne le nombre de caractères de l’expression de chaîne spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr*  
   Est l’expression de chaîne à évaluer.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant retourne la longueur d’une chaîne.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
