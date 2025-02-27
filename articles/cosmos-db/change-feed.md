---
title: Utilisation du support de flux de modification dans Azure Cosmos DB
description: Utiliser le support de flux de modification d'Azure Cosmos DB pour le suivi des modifications des documents, les opérations de traitement basées sur les événements comme les déclencheurs, et la mise à jour des systèmes de cache et d'analyse
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/07/2021
ms.reviewer: sngun
ms.custom: seodec18, "seo-nov-2020"
ms.openlocfilehash: 26c5ce853776c111122eb184d042a414eb5f5acc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532521"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Flux de modification dans Azure Cosmos DB
[!INCLUDE[appliesto-all-apis-except-table](includes/appliesto-all-apis-except-table.md)]

Le flux de modification dans Azure Cosmos DB est un enregistrement persistant des modifications apportées à un conteneur dans l’ordre dans lequel elles se produisent. Dans Azure Cosmos DB, le flux de modification écoute les modifications apportées à un conteneur Azure Cosmos. Il renvoie ensuite la liste chronologique de documents qui ont été modifiés, dans l’ordre dans lequel ils ont été modifiés. Les modifications conservées peuvent être traitées de manière asynchrone et incrémentielle, puis réparties sur un ou plusieurs consommateurs pour un traitement en parallèle.

Apprenez-en davantage sur les [modèles de conception de flux de modification](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>API et SDK clients pris en charge

Cette fonctionnalité est prise en charge par les API et les SDK clients Azure Cosmos DB suivants.

| **Pilotes clients** | **API SQL** | **API Azure Cosmos DB pour Cassandra** | **API pour MongoDB d’Azure Cosmos DB** | **API Gremlin**|**API de table** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Oui | Oui | Oui | Oui | Non |
|Java|Oui|Oui|Oui|Oui|Non|
|Python|Oui|Oui|Oui|Oui|Non|
|Node/JS|Oui|Oui|Oui|Oui|Non|

## <a name="change-feed-and-different-operations"></a>Flux de modification et différentes opérations

Aujourd’hui, vous voyez toutes les insertions et mises à jour dans le flux de modification. Vous ne pouvez pas filtrer le flux de modification pour un type d’opération spécifique. Une alternative consiste à ajouter un « marqueur conditionnel » de mise à jour à l’élément, et à filtrer les résultats d’après ce marqueur quand vous effectuez le traitement des éléments du flux de modification.

Pour le moment, le flux de modification ne consigne pas les suppressions. Comme dans l’exemple précédent, vous pouvez ajouter un marqueur conditionnel sur les éléments qui sont supprimés. Par exemple, vous pouvez ajouter un attribut dans l’élément appelé « deleted » et lui attribuer la valeur « true », puis définir une durée de vie pour l’élément afin qu’il puisse être supprimé automatiquement. Vous pouvez lire le flux de modification pour les éléments historiques, par exemple, pour les éléments qui ont été ajoutés il y a cinq ans (la modification la plus récente correspondant à l’élément, il n’inclut pas les modifications intermédiaires). Vous pouvez lire le flux de modification jusqu’à l’origine de votre conteneur, mais si un élément est supprimé, il est retiré du flux de modification.

### <a name="sort-order-of-items-in-change-feed"></a>Ordre de tri des éléments du flux de modification

Les éléments du flux de modification figurent dans l’ordre où ils ont été modifiés. Cet ordre de tri est garanti par clé de partition logique.

### <a name="consistency-level"></a>Niveau de cohérence

Lorsque vous utilisez le flux de modification dans un niveau de cohérence éventuelle, il peut y avoir des événements en double entre les opérations de lecture de flux de modification suivantes (le dernier événement d’une opération de lecture apparaît comme le premier événement de la prochaine opération).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Flux de modification dans les comptes Azure Cosmos multirégions

Dans un compte Azure Cosmos multirégion, si une région d’écriture bascule, le flux de modification continue de fonctionner après le basculement manuel de manière contiguë.

### <a name="change-feed-and-time-to-live-ttl"></a>Flux de modification et durée de vie (TTL)

Si une propriété de durée de vie (TTL) est définie sur -1 pour un élément, le flux de modification est conservé indéfiniment. Tant que les données ne sont pas supprimées, elles restent dans le flux de modification.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Flux de modification et formats _etag, _lsn ou _ts

Le format _etag est un format interne. Vous ne devez pas vous y fier, car il peut être modifié à tout moment. Le format _ts correspond à un horodatage de modification ou de création. Vous pouvez utiliser les données _ts à des fins de comparaison chronologique. Le format _lsn est un ID de lot qui est ajouté uniquement pour le flux de modification. Il représente l’ID de transaction. De nombreux éléments peuvent avoir un même _lsn. L’étiquette d’entité (ETag) de FeedResponse est différente du _etag que vous voyez dans l’élément. _etag est un identificateur interne utilisé pour le contrôle d’accès concurrentiel. La propriété _etag indique la version de l’élément, tandis que la propriété ETag est utilisée pour le séquençage du flux.

## <a name="working-with-change-feed"></a>Utilisation du flux de modification

Vous pouvez utiliser le flux de modification à l’aide des options suivantes :

* [Utilisation du flux de modification avec Azure Functions](change-feed-functions.md)
* [Utilisation du flux de modification avec le processeur de flux de modification](change-feed-processor.md) 

Le flux de modification est disponible pour chacune des clés de partition logique du conteneur, et il peut être distribué vers un ou plusieurs consommateurs en vue d’un traitement parallèle, comme indiqué dans l’image ci-dessous.

:::image type="content" source="./media/change-feed/changefeedvisual.png" alt-text="Traitement distribué du flux de modification d’Azure Cosmos DB" border="false":::

## <a name="features-of-change-feed"></a>Fonctionnalités du flux de modification

* Le flux de modification est activé par défaut pour tous les comptes Azure Cosmos.

* Vous pouvez utiliser votre [débit provisionné](request-units.md) pour lire le contenu du flux de modification, comme toute autre opération effectuée dans Azure Cosmos DB, dans l’une des régions associées à votre base de données Azure Cosmos.

* Le flux de modification inclut les opérations d’insertion et de mise à jour apportées aux éléments du conteneur. Vous pouvez capturer des suppressions en définissant un indicateur de « suppression réversible » dans vos éléments (par exemple, vos documents) au lieu d’effectuer des suppressions définitives. Vous pouvez également définir un délai d’expiration fixe pour vos éléments avec la [fonctionnalité de durée de vie](time-to-live.md). Par exemple, vous pouvez définir le délai sur 24 heures et utiliser la valeur de cette propriété pour capturer les suppressions. Avec cette solution, vous devez traiter les modifications dans un intervalle de temps inférieur à la période d’expiration de durée de vie.

* Seule la dernière modification d’un élément donné est incluse dans le journal des modifications. Les modifications intermédiaires peuvent ne pas être disponibles.

* Chaque modification incluse dans le journal des modifications n’apparaît qu’une seule fois dans le flux de modification. De plus, les clients doivent gérer la logique de points de contrôle. Si vous souhaitez éviter la gestion complexe des points de contrôle, le processeur du flux de modification fournit des points de contrôle automatiques ainsi qu’une sémantique de type « au moins une fois ». [Utilisation du flux de modification avec le processeur de flux de modification](change-feed-processor.md).

* Le flux de modification est trié par ordre de modification au sein de chaque valeur de clé de partition logique. Il n’existe aucun ordre garanti entre les valeurs de clé de partition.

* Les modifications peuvent être synchronisées à partir de n’importe quel point dans le temps. Autrement dit, il n’existe aucune période fixe de conservation des données pendant laquelle les modifications sont disponibles.

* Les modifications sont disponibles en parallèle pour toutes les clés de partition logique d’un conteneur Azure Cosmos. Cette fonctionnalité permet à plusieurs consommateurs de traiter en parallèle les modifications de grands conteneurs.

* Les applications peuvent demander plusieurs flux de modification simultanément pour un même conteneur. ChangeFeedOptions.StartTime peut être utilisé pour fournir un point de départ. Par exemple, pour rechercher le jeton de continuation correspondant à une heure donnée. S’il est spécifié, ContinuationToken est prioritaire par rapport aux valeurs StartTime et StartFromBeginning. La précision de ChangeFeedOptions.StartTime est ’environ 5 secondes.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Flux de modification dans les API pour Cassandra et MongoDB

La fonctionnalité de flux de modification apparaît comme un flux de modification dans l’API MongoDB et sous la forme Interroger avec un prédicat dans l’API Cassandra. Pour en savoir plus sur les détails d’implémentation de l’API MongoDB, consultez les [flux de modification dans l’API Azure Cosmos DB pour MongoDB](mongodb/change-streams.md).

Native Apache Cassandra propose la capture des changements de données (CDC), un mécanisme de marquage de tables spécifiques à des fins d’archivage, mais aussi de rejet des écritures dans ces tables une fois que la taille sur disque configurable du journal CDC est atteinte. La fonctionnalité de flux de modification dans l’API Azure Cosmos DB pour Cassandra améliore la capacité à interroger les modifications avec un prédicat par le biais du langage CQL. Pour en savoir plus sur les détails d’implémentation, consultez [Flux de modification dans l’API Azure Cosmos DB pour Cassandra](cassandra/cassandra-change-feed.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le flux de modification, consultez les articles suivants :

* [Options de lecture du flux de modification](read-change-feed.md)
* [Utilisation du flux de modification avec Azure Functions](change-feed-functions.md)
* [Utilisation du processeur de flux de modification](change-feed-processor.md)
