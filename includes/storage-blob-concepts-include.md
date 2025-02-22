---
title: Fichier include
description: Fichier include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/19/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0914cf9515930e23e4134181ffe8332e36eacffe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612944"
---
Le stockage Blob Azure est la solution de stockage d’objet de Microsoft pour le cloud. Stockage Blob est optimisé pour le stockage d’immenses quantités de données non structurées. Les données non structurées sont des données qui n’obéissent pas à un modèle ou une définition de données en particulier, comme des données texte ou binaires.

## <a name="about-blob-storage"></a>À propos du stockage Blob

Le stockage Blob est conçu pour :

* Mise à disposition d’images ou de documents directement dans un navigateur.
* Stockage de fichiers pour un accès distribué.
* Diffusion en continu de vidéo et d’audio.
* Écriture dans les fichiers journaux.
* Stockage de données pour la sauvegarde et la restauration, la récupération d’urgence et l’archivage.
* Stockage des données pour l’analyse par un service local ou hébergé par Azure.

Les utilisateurs ou les applications clientes peuvent accéder aux objets du stockage Blob via HTTP/HTTPS, où qu’ils se trouvent dans le monde. Les objets du stockage Blob sont accessibles via l’[API REST Stockage Azure](/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](/powershell/module/az.storage), [Azure CLI](/cli/azure/storage) ou une bibliothèque de client Stockage Azure. Les bibliothèques clientes sont disponibles pour différents langages, notamment :

* [.NET](/dotnet/api/overview/azure/storage)
* [Java](/java/api/overview/azure/storage)
* [Node.JS](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](../articles/storage/blobs/storage-quickstart-blobs-python.md)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>À propos d’Azure Data Lake Storage Gen2

Le stockage Blob prend en charge Azure Data Lake Storage Gen2, solution d’analytique de Big Data d’entreprise de Microsoft pour le cloud. Azure Data Lake Storage Gen2 offre un système de fichiers hiérarchique ainsi que les avantages du stockage Blob, notamment :

* Stockage hiérarchisé à faible coût
* Haute disponibilité
* Cohérence forte
* Fonctionnalités de reprise d’activité

Pour plus d’informations sur Data Lake Storage Gen2, consultez [Présentation d’Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md).