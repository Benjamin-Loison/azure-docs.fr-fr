---
title: Présentation d'Azure Data Lake Storage Gen2
description: Lisez une présentation d’Azure Data Lake Storage Gen2. Découvrez les fonctionnalités clés. Passez en revue les fonctionnalités de Stockage Blob, les intégrations de service Azure et les plateformes prises en charge.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 34331436c0e683aefa8ea7cddd4396e4a21546ea
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619328"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Présentation d'Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 est un ensemble de fonctionnalités dédiées à l’analytique du Big Data, s’appuyant sur le service [Stockage Blob Azure](storage-blobs-introduction.md).

Data Lake Storage Gen2 fait converger les fonctionnalités d’[Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) avec le service Stockage Blob Azure. Par exemple, Data Lake Storage Gen2 fournit une sémantique du système de fichiers, une sécurité au niveau des fichiers et la mise à l’échelle. Comme ces fonctionnalités sont basées sur le stockage Blob, vous bénéficiez également d’un stockage hiérarchisé à faible coût avec des fonctionnalités à haute disponibilité et de reprise d’activité.

## <a name="designed-for-enterprise-big-data-analytics"></a>Conçu pour l’analytique du Big Data d’entreprise

Data Lake Storage Gen2 fait du stockage Azure la base pour créer des dépôts Data Lake d’entreprise sur Azure. Conçu dès le départ pour traiter plusieurs téraoctets d’informations tout en assurant des centaines de gigaoctets de débit, Data Lake Storage Gen2 vous permet de facilement gérer d'importants volumes de données.

Une caractéristique fondamentale de Data Lake Storage Gen2 est l’ajout d’un [espace de noms hiérarchique](data-lake-storage-namespace.md) au stockage Blob. L’espace de noms hiérarchique organise les objets/fichiers dans une hiérarchie de répertoires pour offrir un accès efficace aux données. Une convention de nommage de magasin d’objets commune utilise des barres obliques dans le nom pour reproduire une structure hiérarchique de dossiers. Cette structure devient réelle avec Data Lake Storage Gen2. Des opérations telles que le renommage ou la suppression d’un répertoire deviennent des opérations individuelles de métadonnées atomiques sur le répertoire. Il n’est pas nécessaire d’énumérer et de traiter tous les objets qui partagent le préfixe du nom du répertoire.

Data Lake Storage Gen2 s’appuie sur le Stockage Blob. Il améliore les performances, la gestion et la sécurité de diverses façons :

- Les **performances** sont optimisées, car vous n’avez pas besoin de copier ou transformer les données avant l’analyse. Par rapport à l’espace de noms plat du Stockage Blob, l’espace de noms hiérarchique améliore considérablement les performances des opérations de gestion de répertoires et donc les performances générales du travail.

- La **gestion** est plus facile, car vous pouvez organiser et manipuler les fichiers dans des répertoires et des sous-répertoires.

- La **sécurité** est applicable, car vous pouvez définir des autorisations POSIX sur des répertoires ou des fichiers spécifiques.

Par ailleurs, Data Lake Storage Gen2 est extrêmement rentable dans la mesure où il repose sur le service [Stockage Blob Azure](storage-blobs-introduction.md), dont le coût est faible. Les fonctionnalités supplémentaires réduisent encore le coût total de possession de l’analytique du Big Data sur Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Principales fonctionnalités de Data Lake Storage Gen2

- **Accès compatible Hadoop**: Data Lake Storage Gen2 vous permet de gérer les données et d’y accéder comme avec un [système de fichiers DFS Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Le nouveau [pilote ABFS](data-lake-storage-abfs-driver.md) (utilisé pour accéder aux données) est disponible dans tous les environnements Apache Hadoop. Ces environnements incluent [Azure HDInsight](../../hdinsight/index.yml) *,* [Azure Databricks](/azure/databricks/) et [Azure Synapse Analytics](../../synapse-analytics/index.yml).

- **Surensemble d’autorisations POSIX :** Le modèle de sécurité pour Data Lake Gen2 prend en charge les autorisations ACL et POSIX, ainsi que certaines granularités supplémentaires spécifiques à Data Lake Storage Gen2. Les paramètres peuvent être configurés via l’Explorateur Stockage ou des infrastructures telles que Hive et Spark.

- **Économique** : Data Lake Storage Gen2 offre une capacité de stockage et des transactions à faible coût. Des fonctionnalités telles que le [cycle de vie du service Stockage Blob Azure](./lifecycle-management-overview.md) optimisent les coûts tout au long du cycle de vie des données.

- **Pilote optimisé :** Le pilote ABFS est [optimisé spécifiquement](data-lake-storage-abfs-driver.md) pour l’analytique du Big Data. Les API REST correspondantes sont exposées par le biais du point de terminaison `dfs.core.windows.net`.

### <a name="scalability"></a>Extensibilité

Le Stockage Azure est nativement scalable, que vous y accédiez via les interfaces Data Lake Storage Gen2 ou de stockage Blob. Il peut stocker et traiter *plusieurs exaoctets de données*. Ce volume de stockage est disponible avec un débit mesuré en gigabits par seconde (Gbits/s) et des niveaux élevés d’opérations d’entrée/sortie par seconde (IOPS). Le traitement est exécuté à des latences par demande quasi constantes, qui sont mesurées au niveau du service, du compte et du fichier.

### <a name="cost-effectiveness"></a>Rentabilité

Data Lake Storage Gen2 reposant sur le service Stockage Blob Azure, la capacité de stockage et les coûts des transactions sont inférieurs. Contrairement à d’autres services de stockage cloud, vous n’avez pas besoin de déplacer ni de transformer vos données pour pouvoir les analyser. Pour plus d’informations sur la tarification, consultez la page [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage).

Par ailleurs, des fonctionnalités comme [l’espace de noms hiérarchique](data-lake-storage-namespace.md) améliorent considérablement les performances générales de nombreuses tâches analytiques. Cette amélioration des performances signifie que vous avez besoin d’une puissance de calcul moindre pour traiter la même quantité de données, ce qui contribue à la baisse du coût total de possession (TCO) pour le travail analytique de bout en bout.

### <a name="one-service-multiple-concepts"></a>Un seul service, plusieurs concepts

Data Lake Storage Gen2 reposant sur le service Stockage Blob Azure, plusieurs concepts peuvent décrire les mêmes éléments partagés.

Les éléments suivants sont des entités équivalentes, décrites par différents concepts. Sauf indication contraire, ces entités sont directement synonymes :

| Concept                                | Organisation de niveau supérieur | Organisation de niveau inférieur                                            | Conteneur de données |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Objets blob – Stockage d’objets à usage général | Conteneur              | Répertoire virtuel (kit SDK uniquement – ne fournit aucune manipulation atomique) | Objet blob           |
| Azure Data Lake Storage Gen2 – Stockage de l’analytique          | Conteneur            | Répertoire                                                           | Fichier           |

## <a name="supported-blob-storage-features"></a>Fonctionnalités Stockage Blob prises en charge

Les fonctionnalités de Stockage Blob, telles que la [journalisation des diagnostics](../common/storage-analytics-logging.md), les [niveaux d’accès](access-tiers-overview.md) et les [stratégies de gestion du cycle de vie du stockage Blob](./lifecycle-management-overview.md) sont disponibles pour votre compte. La plupart des fonctionnalités de Stockage Blob sont entièrement prises en charge, mais certaines sont prises en charge uniquement au niveau de la préversion ou ne sont pas encore prises en charge.

Pour voir comment chaque fonctionnalité Stockage Blob est prise en charge avec Data Lake Storage Gen2, consultez [Prise en charge des fonctionnalités de Stockage Blob dans les comptes Stockage Azure](storage-feature-support-in-storage-accounts.md).

## <a name="supported-azure-service-integrations"></a>Intégrations de service Azure prises en charge

Data Lake Storage Gen2 prend en charge plusieurs services Azure. Vous pouvez les utiliser pour ingérer des données, effectuer une analytique et créer des représentations visuelles. Pour obtenir la liste des services Azure pris en charge, consultez [Services Azure prenant en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plateformes open source prises en charge

Plusieurs plateformes open source prennent en charge le stockage Data Lake Gen2. Pour obtenir une liste complète, consultez [Plateformes open source prenant en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Voir aussi

- [Bonnes pratiques d’utilisation d’Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)
- [Problèmes connus avec Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Accès multiprotocole pour Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
