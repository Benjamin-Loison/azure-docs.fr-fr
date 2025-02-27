---
title: Fonctions Bicep - tableaux
description: Décrit les fonctions à utiliser dans un fichier Bicep pour travailler avec les tableaux.
author: mumian
ms.topic: conceptual
ms.author: jgao
ms.date: 10/18/2021
ms.openlocfilehash: f0f9132818a8708ad5ef2a3205eb2f3a497006d6
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130160809"
---
# <a name="array-functions-for-bicep"></a>Tableaux logiques pour Bicep

Cet article décrit les fonctions Bicep pour l’utilisation de tableaux.

## <a name="array"></a>tableau

`array(convertToArray)`

Convertit la valeur en tableau.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| convertToArray |Oui |int, string, array ou object |Valeur à convertir en tableau. |

### <a name="return-value"></a>Valeur retournée

Tableau.

### <a name="example"></a> Exemple

L’exemple suivant montre comment utiliser la fonction de tableau avec des types différents.

```bicep
param intToConvert int = 1
param stringToConvert string = 'efgh'
param objectToConvert object = {
  'a': 'b'
  'c': 'd'
}

output intOutput array = array(intToConvert)
output stringOutput array = array(stringToConvert)
output objectOutput array = array(objectToConvert)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| intOutput | Array | [1] |
| stringOutput | Array | ["efgh"] |
| objectOutput | Array | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

Combine plusieurs tableaux et renvoie le tableau concaténé.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau |Le premier tableau pour la concaténation. |
| arguments supplémentaires |Non |tableau |Tableaux supplémentaires en ordre séquentiel pour la concaténation. |

Cette fonction prend un nombre quelconque de tableaux et les combine.

### <a name="return-value"></a>Valeur retournée

Un tableau de valeurs concaténées.

### <a name="example"></a> Exemple

L’exemple suivant montre comment combiner deux tableaux.

```bicep
param firstArray array = [
  '1-1'
  '1-2'
  '1-3'
]
param secondArray array = [
  '2-1'
  '2-2'
  '2-3'
]

output return array = concat(firstArray, secondArray)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| return | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>contains

`contains(container, itemToFind)`

Vérifie si un tableau contient une valeur, un objet contient une clé ou une chaîne contient une sous-chaîne. La comparaison de chaînes est sensible à la casse. Cependant, quand vous testez si un objet contient une clé, la comparaison n’est pas sensible à la casse.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| conteneur |Oui |tableau, objet ou chaîne |La valeur qui contient la valeur à rechercher. |
| itemToFind |Oui |chaîne ou entier |La valeur à trouver. |

### <a name="return-value"></a>Valeur retournée

**True** si l’élément est trouvé ; sinon, **False**.

### <a name="example"></a>Exemple

L’exemple suivant montre comment utiliser contains avec différents types :

```bicep
param stringToTest string = 'OneTwoThree'
param objectToTest object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output stringTrue bool = contains(stringToTest, 'e')
output stringFalse bool = contains(stringToTest, 'z')
output objectTrue bool = contains(objectToTest, 'one')
output objectFalse bool = contains(objectToTest, 'a')
output arrayTrue bool = contains(arrayToTest, 'three')
output arrayFalse bool = contains(arrayToTest, 'four')
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="empty"></a>empty

`empty(itemToTest)`

Détermine si un tableau, un objet ou une chaîne est vide.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| itemToTest |Oui |tableau, objet ou chaîne |Valeur à vérifier pour voir si elle est vide. |

### <a name="return-value"></a>Valeur retournée

Retourne **True** si la valeur est vide ; sinon, **False**.

### <a name="example"></a>Exemple

L’exemple suivant vérifie si un tableau, un objet et une chaîne sont vides.

```bicep
param testArray array = []
param testObject object = {}
param testString string = ''

output arrayEmpty bool = empty(testArray)
output objectEmpty bool = empty(testObject)
output stringEmpty bool = empty(testString)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="first"></a>first

`first(arg1)`

Retourne le premier élément du tableau ou le premier caractère de la chaîne.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau ou chaîne |La valeur permettant de récupérer le premier élément ou caractère. |

### <a name="return-value"></a>Valeur retournée

Type (chaîne, entier, tableau ou objet) du premier élément d’un tableau ou premier caractère d’une chaîne.

### <a name="example"></a> Exemple

L’exemple suivant montre comment utiliser la première fonction avec un tableau et une chaîne.

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = first(arrayToTest)
output stringOutput string = first('One Two Three')
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="intersection"></a>intersection

`intersection(arg1, arg2, arg3, ...)`

Retourne un tableau ou un objet unique avec les éléments communs à partir des paramètres.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |objet ou tableau |La première valeur à utiliser pour rechercher des éléments communs. |
| arg2 |Oui |objet ou tableau |La seconde valeur à utiliser pour rechercher des éléments communs. |
| arguments supplémentaires |Non |objet ou tableau |Valeurs supplémentaires à utiliser pour rechercher des éléments communs. |

### <a name="return-value"></a>Valeur de retour

Tableau ou objet avec les éléments communs. L’ordre des éléments est déterminé par le premier paramètre de tableau.

### <a name="example"></a>Exemple

L’exemple suivant indique comment utiliser l’intersection avec les tableaux et les objets :

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}

param secondObject object = {
  'one': 'a'
  'two': 'z'
  'three': 'c'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'two'
  'three'
]

output objectOutput object = intersection(firstObject, secondObject)
output arrayOutput array = intersection(firstArray, secondArray)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

Le premier paramètre de tableau détermine l’ordre des éléments croisés. L’exemple suivant montre comment l’ordre des éléments retournés est basé sur le tableau qui figure en première position.

```bicep
var array1 = [
  1
  2
  3
  4
]

var array2 = [
  3
  2
  1
]

var array3 = [
  4
  1
  3
  2
]

output commonUp array = intersection(array1, array2, array3)
output commonDown array = intersection(array2, array3, array1)
```

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| commonUp | tableau | [1, 2, 3] |
| commonDown | tableau | [3, 2, 1] |

## <a name="items"></a>items

`items(object)`

Convertit un objet dictionnaire en un tableau.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| object |Oui |object |Objet dictionnaire à convertir en tableau. |

### <a name="return-value"></a>Valeur retournée

Tableau d’objets pour le dictionnaire converti. Chaque objet du tableau a une propriété `key` qui contient la valeur de clé du dictionnaire. Chaque objet possède également une propriété `value` qui contient les propriétés de l’objet.

### <a name="example"></a>Exemple

L’exemple suivant convertit un objet dictionnaire en tableau. Pour chaque objet du tableau, il crée un objet avec les valeurs modifiées.

```bicep
var entities = {
  item001: {
    enabled: true
    displayName: 'Example item 1'
    number: 300
  }
  item002: {
    enabled: false
    displayName: 'Example item 2'
    number: 200
  }
}

var modifiedListOfEntities = [for entity in items(entities): {
  key: entity.key
  fullName: entity.value.displayName
  itemEnabled: entity.value.enabled
}]

output modifiedResult array = modifiedListOfEntities
```

L’exemple précédent renvoie :

```json
"modifiedResult": {
  "type": "Array",
  "value": [
    {
      "fullName": "Example item 1",
      "itemEnabled": true,
      "key": "item001"
    },
    {
      "fullName": "Example item 2",
      "itemEnabled": false,
      "key": "item002"
    }
  ]
}
```

L’exemple suivant montre le tableau renvoyé par la fonction items.

```bicep
var entities = {
  item001: {
    enabled: true
    displayName: 'Example item 1'
    number: 300
  }
  item002: {
    enabled: false
    displayName: 'Example item 2'
    number: 200
  }
}

var entitiesArray = items(entities)

output itemsResult array = entitiesArray
```

L’exemple renvoie :

```json
"itemsResult": {
  "type": "Array",
  "value": [
    {
      "key": "item001",
      "value": {
        "displayName": "Example item 1",
        "enabled": true,
        "number": 300
      }
    },
    {
      "key": "item002",
      "value": {
        "displayName": "Example item 2",
        "enabled": false,
        "number": 200
      }
    }
  ]
}
```

## <a name="last"></a>last

`last(arg1)`

Retourne le dernier élément du tableau ou le dernier caractère de la chaîne.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau ou chaîne |La valeur permettant de récupérer le dernier élément ou caractère. |

### <a name="return-value"></a>Valeur retournée

Type (chaîne, entier, tableau ou objet) du dernier élément d’un tableau ou dernier caractère d’une chaîne.

### <a name="example"></a> Exemple

L’exemple suivant indique comment utiliser la dernière fonction avec un tableau et une chaîne.

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = last(arrayToTest)
output stringOutput string = last('One Two three')
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="length"></a>length

`length(arg1)`

Retourne le nombre d’éléments d’un tableau, les caractères d’une chaîne ou les propriétés au niveau de la racine d’un objet.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau, chaîne ou objet |Tableau à utiliser pour l’obtention du nombre d’éléments, ou chaîne à utiliser pour l’obtention du nombre de caractères, ou l’objet à utiliser pour l’obtention du nombre de propriétés au niveau de la racine. |

### <a name="return-value"></a>Valeur retournée

Un entier.

### <a name="example"></a>Exemple

L’exemple suivant montre comment utiliser la longueur avec un tableau et une chaîne :

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]
param stringToTest string = 'One Two Three'
param objectToTest object = {
  'propA': 'one'
  'propB': 'two'
  'propC': 'three'
  'propD': {
    'propD-1': 'sub'
    'propD-2': 'sub'
  }
}

output arrayLength int = length(arrayToTest)
output stringLength int = length(stringToTest)
output objectLength int = length(objectToTest)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="max"></a>max

`max(arg1)`

Retourne la valeur minimale à partir d’un tableau d’entiers ou une liste séparée par des virgules d’entiers.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau d’entiers ou liste séparée par des virgules d’entiers |Collection permettant d’obtenir la valeur maximale. |

### <a name="return-value"></a>Valeur retournée

Entier représentant la valeur maximale.

### <a name="example"></a> Exemple

L’exemple suivant montre comment utiliser max avec un tableau et une liste d’entiers :

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutput int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>min

`min(arg1)`

Retourne la valeur minimale à partir d’un tableau d’entiers ou une liste séparée par des virgules d’entiers.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau d’entiers ou liste séparée par des virgules d’entiers |Collection permettant d’obtenir la valeur minimale. |

### <a name="return-value"></a>Valeur retournée

Entier représentant la valeur minimale.

### <a name="example"></a> Exemple

L’exemple suivant indique comment utiliser la fonction min avec un tableau et une liste d’entiers :

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutput int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="range"></a>range

`range(startIndex, count)`

Crée un tableau d’entiers à partir d’un entier de départ et contenant le nombre d’éléments.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| index_début |Oui |int |Premier entier du tableau. La somme de startIndex et count ne doit pas être supérieure à 2147483647. |
| count |Oui |int |Nombre d’entiers dans le tableau. Il doit s'agir d'un entier non négatif jusqu'à 10000. |

### <a name="return-value"></a>Valeur retournée

Tableau d’entiers.

### <a name="example"></a> Exemple

L’exemple suivant montre comment utiliser la fonction range :

```bicep
param startingInt int = 5
param numberOfElements int = 3

output rangeOutput array = range(startingInt, numberOfElements)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| rangeOutput | Array | [5, 6, 7] |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Retourne un tableau avec tous les éléments après le nombre spécifié dans le tableau, ou retourne une chaîne avec tous les caractères après le nombre spécifié dans la chaîne.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Oui |tableau ou chaîne |Tableau ou chaîne à utiliser pour ignorer les caractères. |
| numberToSkip |Oui |int |Nombre d’éléments ou de caractères à ignorer. Si cette valeur est inférieure ou égale à 0, tous les éléments ou caractères de la valeur sont renvoyés. Si elle est supérieure à la longueur du tableau ou de la chaîne, un tableau ou une chaîne vide est retourné. |

### <a name="return-value"></a>Valeur retournée

Tableau ou chaîne.

### <a name="example"></a> Exemple

L’exemple suivant ignore le nombre spécifié d’éléments dans le tableau et le nombre spécifié de caractères dans une chaîne.

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 4

output arrayOutput array = skip(testArray, elementsToSkip)
output stringOutput string = skip(testString, charactersToSkip)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Retourne un tableau avec le nombre spécifié d’éléments à partir du début du tableau, ou une chaîne avec le nombre spécifié de caractères à partir du début de la chaîne.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Oui |tableau ou chaîne |Tableau ou chaîne à partir duquel les éléments sont tirés. |
| numberToTake |Oui |int |Nombre d’éléments ou de caractères à prendre. Si cette valeur est inférieure ou égale à 0, une chaîne ou un tableau vide est renvoyé. Si elle est supérieure à la longueur du tableau ou de la chaîne, tous les éléments du tableau ou de la chaîne sont retournés. |

### <a name="return-value"></a>Valeur retournée

Tableau ou chaîne.

### <a name="example"></a> Exemple

L’exemple suivant prend le nombre spécifié d’éléments du tableau, et les caractères d’une chaîne.

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToTake int = 2
param testString string = 'one two three'
param charactersToTake int = 2

output arrayOutput array = take(testArray, elementsToTake)
output stringOutput string = take(testString, charactersToTake)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | sur |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

Retourne un tableau ou un objet unique avec tous les éléments communs à partir des paramètres. Les valeurs ou les clés en double sont uniquement incluses une seule fois.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |objet ou tableau |La première valeur à utiliser pour joindre des éléments. |
| arg2 |Oui |objet ou tableau |La seconde valeur à utiliser pour joindre des éléments. |
| arguments supplémentaires |Non |objet ou tableau |Valeurs supplémentaires à utiliser pour joindre des éléments. |

### <a name="return-value"></a>Valeur de retour

Objet ou tableau.

### <a name="example"></a>Exemple

L’exemple suivant indique comment utiliser l’intersection avec les tableaux et les objets :

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c1'
}

param secondObject object = {
  'three': 'c2'
  'four': 'd'
  'five': 'e'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'three'
  'four'
]

output objectOutput object = union(firstObject, secondObject)
output arrayOutput array = union(firstArray, secondArray)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir un tableau de valeurs de chaîne délimitée par une valeur, consultez [split](./bicep-functions-string.md#split).
