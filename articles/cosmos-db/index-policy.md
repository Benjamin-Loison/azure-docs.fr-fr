---
title: Stratégies d’indexation d’Azure Cosmos DB
description: Découvrez comment configurer et modifier la stratégie d’indexation par défaut pour bénéficier d’une indexation automatique et de meilleures performances dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/13/2021
ms.author: tisande
ms.openlocfilehash: c2f380e92693e6ef2d16e74001b2d22d76e6d941
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122563666"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Stratégies d’indexation dans Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dans Azure Cosmos DB, chaque conteneur est doté d’une stratégie d’indexation qui détermine la façon dont les éléments du conteneur doivent être indexés. La stratégie d’indexation par défaut pour les conteneurs nouvellement créés indexe chaque propriété de chaque élément et applique des index de plage pour les chaînes ou les nombres. Vous obtenez ainsi un bon niveau de performance des requêtes sans avoir à réfléchir dès le départ à l’indexation ni à la gestion des index.

Dans certaines situations, vous souhaiterez peut-être remplacer ce comportement automatique pour mieux répondre à vos besoins. Vous pouvez personnaliser la stratégie d’indexation d’un conteneur en définissant son *mode d’indexation* et inclure ou exclure des *chemins de la propriété*.

> [!NOTE]
> La méthode de mise à jour des stratégies d’indexation décrite dans cet article s’applique uniquement à l’API SQL (principale) de la base de données SQL Azure Cosmos. Découvrez l’indexation dans[API Azure Cosmos DB pour MongoDB](mongodb/mongodb-indexing.md).

## <a name="indexing-mode"></a>Mode d'indexation

Azure Cosmos DB prend en charge deux modes d’indexation :

- **Cohérent** : L’index est mis à jour de manière synchrone quand vous créez, mettez à jour ou supprimez des éléments. Cela signifie que la cohérence de vos requêtes de lecture sera la [cohérence configurée pour le compte](consistency-levels.md).
- **Aucun** : L’indexation est désactivée sur le conteneur. C’est courant lorsqu’un conteneur est exclusivement utilisé comme magasin clé-valeur sans que des index secondaires soient nécessaires. Vous pouvez également l’utiliser pour améliorer les performances des opérations en bloc. Une fois les opérations en bloc effectuées, vous pouvez définir le mode d’indexation Consistent (Cohérent), puis le superviser à l’aide de [IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk) jusqu’à la fin du processus.

> [!NOTE]
> Azure Cosmos DB prend également en charge un mode d’indexation différée. L’indexation différée effectue des mises à jour de l’index à un niveau de priorité nettement inférieur quand le moteur ne fait aucun autre travail. Cela peut entraîner des résultats de requête **incohérents ou incomplets**. Si vous prévoyez d’interroger un conteneur Cosmos, vous ne devez pas sélectionner l’indexation différée. Les nouveaux conteneurs ne peuvent pas sélectionner l’indexation différée. Vous pouvez demander une exemption en contactant le cosmoslazyindexing@microsoft.com (sauf si vous utilisez un compte Azure Cosmos en mode [serverless](serverless.md), qui ne prend pas en charge l’indexation différée).

Par défaut, la stratégie d’indexation a la valeur `automatic`. Ce résultat est obtenu en affectant à la propriété `automatic` de la stratégie d’indexation la valeur `true`. L’affectation de `true` à cette propriété permet à Azure CosmosDB d’indexer automatiquement les documents au fur et à mesure de leur rédaction.

## <a name="index-size"></a><a id="index-size"></a>Taille d’index

Dans Azure Cosmos DB, la consommation totale du stockage correspond à la somme de la taille des données et de la taille de l’index. Voici quelques-unes des fonctionnalités de la taille d’index :

* La taille d’index dépend de la stratégie d’indexation. Si toutes les propriétés sont indexées, la taille d’index peut être supérieure à la taille des données.
* Lorsque des données sont supprimées, les index sont compactés de manière quasi continue. Toutefois, pour les petites suppressions de données, vous risquez de ne pas observer de diminution immédiate de la taille d’index.
* La taille de l’index peut croître temporairement quand les partitions physiques sont divisées. L’espace d’index est libéré après la division de la partition.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Inclusion et exclusion de chemins de propriété

Une stratégie d’indexation personnalisée peut spécifier des chemins de propriétés qui sont explicitement inclus dans l’indexation ou en sont exclus. En optimisant le nombre de chemins d’accès qui sont indexés, vous pouvez réduire considérablement la latence et les frais de chargement des opérations d’écriture. Ces chemins sont définis à l’aide de [la méthode décrite dans la section de vue d’ensemble de l’indexation](index-overview.md#from-trees-to-property-paths) avec les ajouts suivants :

- un chemin menant à une valeur scalaire (chaîne ou nombre) se termine par `/?`
- les éléments tirés d’un tableau sont traités ensemble par le biais de la notation `/[]` (au lieu de `/0`, `/1` etc.)
- le générique `/*` peut être utilisé pour correspondre à tous les éléments situés sous le nœud

En reprenant le même exemple :

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- le chemin du `employees` de `headquarters` est `/headquarters/employees/?`

- le chemin `country` de `locations` est `/locations/[]/country/?`

- le chemin de tout ce qui se trouve sous `headquarters` est `/headquarters/*`

Par exemple, nous pourrions inclure le chemin d’accès `/headquarters/employees/?`. Ce chemin d’accès permet de s’assurer que la propriété employees est indexée, mais sans indexer de JSON imbriqué supplémentaire au sein de cette propriété.

## <a name="includeexclude-strategy"></a>Stratégie inclure/exclure

Toute stratégie d’indexation doit inclure le chemin racine `/*` comme chemin inclus ou exclu.

- Inclure le chemin racine pour exclure sélectivement des chemins qui n’ont pas besoin d’être indexés. C’est l’approche recommandée, car elle permet à Azure Cosmos DB d’indexer proactivement toute nouvelle propriété qui peut être ajoutée à votre modèle.
- Exclure le chemin racine pour inclure sélectivement des chemins devant être indexés.

- Pour les chemins avec des caractères normaux qui incluent : des caractères alphanumériques et _ (caractère de soulignement), vous n’êtes pas obligé d’échapper la chaîne de chemin entourée de guillemets doubles (par exemple, "/path/?"). Pour les chemins avec d’autres caractères spéciaux, vous devez avoir les caractères d’échappement que sont les guillemets doubles autour de la chaîne de chemin (par exemple, "/\"path\"/ ?"). Si vous prévoyez des caractères spéciaux dans votre chemin, vous pouvez échapper chaque chemin pour des raisons de sécurité. Du point de vue fonctionnel, que vous échappiez tous les chemins ou seulement ceux qui comportent des caractères spéciaux ne fait aucune différence.

- La propriété système `_etag` est exclue de l’indexation par défaut, sauf si l’etag est ajouté au chemin inclus pour l’indexation.

- Si le mode d’indexation est défini sur **cohérent**, les propriétés système `id` et `_ts` sont automatiquement indexées.

Lorsque vous incluez et excluez des chemins d’accès, vous pouvez rencontrer les attributs suivants :

- `kind` peut être `range` ou `hash`. La prise en charge des index de hachage est limitée aux filtres d’égalité. La fonctionnalité d’index de plage fournit toutes les fonctionnalités des index de hachage, ainsi que le tri efficace, les filtres de plage et les fonctions système. Nous recommandons toujours d’utiliser un index de plage.

- `precision` est un nombre défini au niveau de l’index pour les chemins inclus. La valeur `-1` indique la précision maximale. Nous vous recommandons de toujours définir cette valeur sur `-1`.

- `dataType` peut être `String` ou `Number`. Cela indique les types de propriétés JSON qui seront indexées.

Il n’est plus nécessaire de définir ces propriétés. Lorsqu’elles ne sont pas spécifiées, ces propriétés ont les valeurs par défaut suivantes :

| **Nom de la propriété**     | **Valeur par défaut** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` et `Number` |

Consultez [cette section](how-to-manage-indexing-policy.md#indexing-policy-examples) pour obtenir des exemples de stratégie d’indexation pour l’inclusion et l’exclusion de chemins d’accès.

## <a name="includeexclude-precedence"></a>Priorité de l’inclusion ou de l’exclusion

Si les chemins inclus et les chemins exclus présentent un conflit, le chemin plus précis est prioritaire.

Voici un exemple :

**Chemin inclus** : `/food/ingredients/nutrition/*`

**Chemin exclu** : `/food/ingredients/*`

Dans ce cas, le chemin inclus est prioritaire sur le chemin exclu, car il est plus précis. Sur la base de ces chemins, toutes les données du chemin `food/ingredients` ou imbriquées dans celui-ci sont exclues de l’index. Il en va différemment des données du chemin inclus `/food/ingredients/nutrition/*`, qui est indexé.

Voici quelques règles qui déterminent la priorité des chemins inclus et exclus dans Azure Cosmos DB :

- Les chemins plus profonds sont plus précis que les chemins plus étroits. Par exemple, `/a/b/?` est plus précis que `/a/?`.

- `/?` est plus précis que `/*`. Par exemple, `/a/?` étant plus précis que `/a/*`, `/a/?` est prioritaire.

- Le chemin `/*` doit être un chemin inclus ou un chemin exclu.

## <a name="spatial-indexes"></a>Index spatiaux

Lorsque vous définissez un chemin d’accès spatial dans la stratégie d’indexation, vous devez définir l’index ```type``` à appliquer à ce chemin d’accès. Les types possibles pour les index spatiaux sont les suivants :

* Point

* Polygone

* MultiPolygon

* LineString

Azure Cosmos DB, par défaut, ne crée pas d’index spatial. Si vous souhaitez utiliser des fonctions intégrées SQL spatiales, vous devez créer un index spatial sur les propriétés requises. Consultez [cette section](sql-query-geospatial-index.md) pour des exemples de stratégies d’indexation afin d’ajouter des index spatiaux.

## <a name="composite-indexes"></a>Index composites

Les requêtes qui ont une clause `ORDER BY` avec deux ou plusieurs propriétés nécessitent un index composite. Vous pouvez également définir un index composite pour améliorer les performances de nombreuses requêtes d’égalité et de plage. Par défaut, aucun index composite n’est défini. Vous devez donc [ajouter des index composites](how-to-manage-indexing-policy.md#composite-index) en fonction des besoins.

Contrairement aux chemins inclus ou exclus, vous ne pouvez pas créer un chemin avec le caractère générique `/*`. Chaque chemin composite se termine par un `/?` implicite que vous n’avez pas besoin de spécifier. Les chemins composites aboutissent à une valeur scalaire, qui est la seule valeur incluse dans l’index composite.

Lorsque vous définissez un index composite, vous spécifiez :

- deux ou plusieurs chemins de propriété ; la séquence où les chemins de propriété sont des aspects définis ;

- l’ordre (croissant ou décroissant).

> [!NOTE]
> Quand vous ajoutez un index composite, la requête utilise les index de plage existants jusqu’à ce que ce que l’ajout de ce nouvel index soit terminé. Ainsi, quand vous ajoutez un index composite, il se peut que vous ne perceviez pas immédiatement les améliorations de performances. Il est possible de suivre la progression de la transformation d’index [avec un des kits de développement logiciel (SDK)](how-to-manage-indexing-policy.md).

### <a name="order-by-queries-on-multiple-properties"></a>Requêtes ORDER BY sur plusieurs propriétés :

Les considérations suivantes sont utilisées lors de l’utilisation d’index composites pour les requêtes avec une clause `ORDER BY` comptant au moins deux propriétés :

- Si les chemins des index composites ne correspondent pas à la séquence des propriétés dans la clause `ORDER BY`, l’index composite ne peut pas prendre en charge la requête.

- L’ordre des chemins des index composites (croissant ou décroissant) doit également correspondre à `order` dans la clause `ORDER BY`.

- L’index composite prend également en charge une clause `ORDER BY` dont l’ordre est inverse sur tous les chemins.

Prenons l’exemple suivant, où un index composite est défini sur des propriétés name, age et _ts :

| **Index composite**     | **Exemple `ORDER BY` de requête**      | **Pris en charge par l’index composite ?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Vous devez personnaliser votre stratégie d’indexation afin de pouvoir servir toutes les requêtes `ORDER BY` nécessaires.

### <a name="queries-with-filters-on-multiple-properties"></a>Requêtes avec des filtres sur plusieurs propriétés

Si une requête a des filtres sur deux propriétés ou plus, il peut être utile de créer un index composite pour ces propriétés.

Par exemple, considérez la requête suivante qui a un filtre d’égalité et un filtre de plage :

```sql
SELECT *
FROM c
WHERE c.name = "John" AND c.age > 18
```

Cette requête serait plus efficace, prendrait moins de temps et consommerait moins de RU, si elle était en mesure de tirer parti d’un index composite sur `(name ASC, age ASC)`.

Les requêtes avec plusieurs filtres de plage peuvent également être optimisées à l’aide d’un index composite. Toutefois, chaque index composite individuel ne peut optimiser qu’un seul filtre de plage. Les filtres de plage incluent `>`, `<`, `<=`, `>=` et `!=`. Le filtre de plage doit être défini en dernier dans l’index composite.

Considérez la requête suivante, qui comprend un filtre d’égalité et deux filtres de plage :

```sql
SELECT *
FROM c
WHERE c.name = "John" AND c.age > 18 AND c._ts > 1612212188
```

Cette requête serait plus efficace avec un index composite sur `(name ASC, age ASC)` et `(name ASC, _ts ASC)`. Toutefois, la requête n’utiliserait pas d’index composite sur `(age ASC, name ASC)`, car les propriétés avec des filtres d’égalité doivent être définies en premier dans l’index composite. Deux index composites distincts sont requis au lieu d’un index composite unique sur `(name ASC, age ASC, _ts ASC)`, car chaque index composite peut uniquement optimiser un seul filtre de plage.

Les considérations suivantes sont utilisées lors de la création d’index composites pour les requêtes avec des filtres sur plusieurs propriétés

- Les expressions de filtre peuvent utiliser plusieurs index composites.
- Les propriétés du filtre de la requête doivent correspondre à celles de l’index composite. Si une propriété se trouve dans l’index composite, mais qu’elle n’est pas incluse dans la requête en tant que filtre, la requête n’utilise pas l’index composite.
- Si une requête a des propriétés supplémentaires dans le filtre qui n’ont pas été définies dans un index composite, une combinaison d’index composites et de plage sera utilisée pour évaluer la requête. Cela nécessite moins de RU que l’utilisation exclusive d’index de plage.
- Si une propriété a un filtre de plage (`>`, `<`, `<=`, `>=` ou `!=`), cette propriété doit être définie en dernier dans l’index composite. Si une requête a plusieurs filtres de plage, elle peut tirer parti de plusieurs index composites.
- Lors de la création d’un index composite pour optimiser des requêtes avec plusieurs filtres, `ORDER` de l’index composite n’aura aucun impact sur les résultats. Cette propriété est facultative.

Prenons les exemples suivants, où un index composite est défini sur des propriétés name, age et timestamp :

| **Index composite**     | **Exemple de requête**      | **Pris en charge par l’index composite ?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name ASC, age ASC)```   | ```SELECT COUNT(1) FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |
| ```(name ASC, age ASC) and (name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp > 123049923``` | ```Yes```            |

### <a name="queries-with-a-filter-and-order-by"></a>Requêtes avec un filtre et la clause ORDER BY

Si une requête filtre sur une ou plusieurs propriétés et possède des propriétés différentes dans la clause ORDER BY, il peut être utile d’ajouter les propriétés du filtre à la clause `ORDER BY`.

Par exemple, en ajoutant les propriétés du filtre à la clause `ORDER BY`, la requête suivante peut être réécrite pour tirer parti d’un index composite :

Requête utilisant un index de plage :

```sql
SELECT *
FROM c 
WHERE c.name = "John" 
ORDER BY c.timestamp
```

Requête utilisant un index composite :

```sql
SELECT * 
FROM c 
WHERE c.name = "John"
ORDER BY c.name, c.timestamp
```

Vous pouvez généraliser les mêmes optimisations de requête pour toutes les requêtes `ORDER BY` avec des filtres, en gardant à l’esprit que les index composites individuels ne peuvent prendre en charge qu’un seul filtre de plage.

Requête utilisant un index de plage :

```sql
SELECT * 
FROM c 
WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 1611947901 
ORDER BY c.timestamp
```

Requête utilisant un index composite :

```sql
SELECT * 
FROM c 
WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 1611947901 
ORDER BY c.name, c.age, c.timestamp
```

En outre, vous pouvez utiliser des index composites pour optimiser les requêtes comportant des fonctions système et la clause ORDER BY :

Requête utilisant un index de plage :

```sql
SELECT * 
FROM c 
WHERE c.firstName = "John" AND Contains(c.lastName, "Smith", true) 
ORDER BY c.lastName
```

Requête utilisant un index composite :

```sql
SELECT * 
FROM c 
WHERE c.firstName = "John" AND Contains(c.lastName, "Smith", true) 
ORDER BY c.firstName, c.lastName
```

Les considérations suivantes s’appliquent lors de la création d’index composites pour optimiser une requête avec un filtre et une clause `ORDER BY` :

* Si vous ne définissez pas d’index composite sur une requête avec un filtre sur une propriété et une clause `ORDER BY` distincte à l’aide d’une autre propriété, la requête réussit quand même. Toutefois, le coût RU de la requête peut être réduit à l’aide d’un index composite, en particulier si la propriété de la clause `ORDER BY` a une cardinalité élevée.
* Si la requête filtre sur les propriétés, celles-ci doivent être incluses en premier dans la clause `ORDER BY`.
* Si la requête filtre plusieurs propriétés, les filtres d’égalité doivent être les premières propriétés de la clause `ORDER BY`.
* Si la requête filtre sur plusieurs propriétés, vous pouvez utiliser au maximum un filtre de plage ou une fonction système par index composite. La propriété utilisée dans le filtre de plage ou la fonction système doit être définie en dernier dans l’index composite.
* Toutes les considérations relatives à la création d’index composites pour les requêtes `ORDER BY` avec plusieurs propriétés, ainsi que les requêtes avec des filtres sur plusieurs propriétés, s’appliquent toujours.


| **Index composite**                      | **Exemple `ORDER BY` de requête**                                  | **Pris en charge par l’index composite ?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" AND c.timestamp > 1589840355 ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT * FROM c WHERE c.timestamp > 1589840355 AND c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

### <a name="queries-with-a-filter-and-an-aggregate"></a>Requêtes avec un filtre et un agrégat 

Si une requête filtre une ou plusieurs propriétés et possède une fonction système d’agrégation, il peut être utile de créer un index composite pour les propriétés dans la fonction système de filtrage et d’agrégation. Cette optimisation s’applique aux fonctions système [SUM](sql-query-aggregate-sum.md) et [AVG](sql-query-aggregate-avg.md).

Les considérations suivantes sont utilisées lors de la création d’index composites pour optimiser une fonction système de filtrage et d’agrégation.

* Les index composites sont facultatifs lors de l’exécution de requêtes avec des agrégats. Toutefois, le coût RU de la requête peut souvent être réduit de manière significative à l’aide d’un index composite.
* Si la requête filtre plusieurs propriétés, les filtres d’égalité doivent être les premières propriétés de l’index composite.
* Vous pouvez avoir au maximum un filtre de plage par index composite, et il doit se trouver sur la propriété dans la fonction système d’agrégation.
* La propriété dans la fonction système d’agrégation doit être définie en dernier dans l’index composite.
* La propriété `order` (`ASC` ou `DESC`) n’a pas d’importance.

| **Index composite**                      | **Exemple de requête**                                  | **Pris en charge par l’index composite ?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John"``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John"``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name > "John"``` | `No` |
| ```(name ASC, age ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John" AND c.age = 25``` | `Yes` |
| ```(age ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John" AND c.age > 25``` | `No` |

## <a name="modifying-the-indexing-policy"></a><a id=index-transformation></a>Modification de la stratégie d’indexation

La stratégie d’indexation d’un conteneur peut être mise à jour à tout moment [à l’aide du portail Azure ou de l’un des kit de développement logiciel (SDK) pris en charge](how-to-manage-indexing-policy.md). Une mise à jour de la stratégie d’indexation déclenche une transformation de l’ancien index vers le nouveau, qui est effectuée en ligne et localement (aucun espace de stockage supplémentaire n’est consommé pendant l’opération). L’ancienne stratégie d’indexation est transformée efficacement en nouvelle stratégie, sans incidence sur la disponibilité d’écriture ni de lecture, ni sur le débit approvisionné sur le conteneur. La transformation d’index est une opération asynchrone. Le temps nécessaire pour l’effectuer dépend du débit approvisionné, du nombre d’éléments et de leur taille.

> [!IMPORTANT]
> La transformation d’index est une opération qui consomme des [unités de requête](request-units.md). Les unités de requête consommées par une transformation d’index ne sont pas facturées si vous utilisez des conteneurs [serverless](serverless.md). Ces unités de requête seront facturées quand les conteneurs serverless seront généralement disponibles.

> [!NOTE]
> Vous pouvez suivre la progression de la transformation d'index sur le portail Azure ou [à l'aide de l'un des kits de développement logiciel (SDK)](how-to-manage-indexing-policy.md).

Il n’y a aucun impact sur la disponibilité des écritures lors des transformations d’index. La transformation d’index utilise vos unités de requête approvisionnées, mais à une priorité inférieure à celles de vos opérations CRUD ou de vos requêtes.

Il n’y a aucun impact sur la disponibilité de lecture lors de l’ajout de nouveaux chemins indexés. Les requêtes utilisent uniquement les nouveaux chemins indexés une fois la transformation d’un index terminée. En d’autres termes, lors de l’ajout de nouveaux chemins indexés, les requêtes qui tirent parti de ce chemin indexé présenteront les mêmes performances avant et pendant la transformation d’index. Une fois la transformation d’index terminée, le moteur de requête commence à utiliser les nouveaux chemins indexés.

Lorsque vous supprimez des chemins indexés, vous devez regrouper toutes vos modifications dans une seule transformation de stratégie d’indexation. Si vous supprimez plusieurs index et que vous le faites dans une seule modification de stratégie d’indexation, le moteur de requête fournit des résultats cohérents et complets tout au long de la transformation d’index. Toutefois, si vous supprimez des index par le biais de plusieurs modifications de stratégie d’indexation, le moteur de requête ne fournit pas de résultats cohérents ou complets tant que toutes les transformations d’index ne sont pas terminées. La plupart des développeurs ne suppriment pas les index, puis essaient immédiatement de les interroger. Or, en pratique, cette situation est peu probable.

Lorsque vous supprimez un chemin indexé, le moteur de requête cesse immédiatement de l’utiliser et effectue une analyse complète.

> [!NOTE]
> Dans la mesure du possible, vous devez toujours essayer de regrouper plusieurs modifications d’indexation dans une seule modification de stratégie d’indexation.

## <a name="indexing-policies-and-ttl"></a>Stratégies d’indexation et TTL

L’utilisation de la [fonctionnalité de durée de vie (TTL, Time-to-Live) ](time-to-live.md) nécessite l’indexation. Cela signifie que :

- il n’est pas possible d’activer la fonctionnalité TTL sur un conteneur dans lequel le mode d’indexation est défini sur `none` ;
- il n’est pas possible de définir le mode d’indexation sur Aucun sur un conteneur dans lequel TLL est activée.

Dans les scénarios où aucun chemin de propriété ne doit être indexé, mais où la fonctionnalité TTL est nécessaire, vous pouvez utiliser une stratégie d’indexation avec le mode d’indexation `consistent`, aucun chemin inclus et un seul chemin exclu (`/*`).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’indexation, consultez les articles suivants :

- [Vue d’ensemble de l’indexation](index-overview.md)
- [Guide pratique pour gérer la stratégie d’indexation](how-to-manage-indexing-policy.md)
