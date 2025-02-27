---
title: API Go Live – Place de marché Azure
description: L’API Go Live lance le processus de création en direct de la liste des offres.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: ec036878cf9412bcc612917fa7a104db3a2e8dd0
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112240311"
---
# <a name="go-live-api"></a>API Go Live

> [!NOTE]
> Les API de Portail Cloud Partner sont intégrées à Espace partenaires et continueront d’y fonctionner. La transition introduit de légères modifications. Passez en revue les changements répertoriés dans les [informations de référence relatives aux API de Portail Cloud Partner](./cloud-partner-portal-api-overview.md) pour vous assurer que votre code continue de fonctionner après la transition vers Espace partenaires. Les API de Portail Cloud Partner doivent uniquement être utilisées pour les produits existants intégrés avant la transition vers Espace partenaires. Les nouveaux produits doivent utiliser les API de soumission d’Espace partenaires.

Cette API démarre le processus de transmission de type push d’une application en production. Cette opération dure généralement longtemps. Cet appel utilise la liste d’e-mails de notification de l’opération de l’API [Publish](./cloud-partner-portal-api-publish-offer.md).

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>Paramètres URI
--------------

|  **Nom**      |   **Description**                                                           | **Type de données** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identificateur du serveur de publication de l’offre à récupérer, par exemple `contoso`       |  String       |
| offerId        | Identificateur de l’offre à récupérer                                   |  String       |
| api-version    | Version la plus récente de l’API                                                   |  Date         |
|  |  |  |

## <a name="header"></a>En-tête
------

|  **Nom**       |     **Valeur**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autorisation   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Exemple de corps

### <a name="response"></a>response

#### <a name="migrated-offers"></a>Offres migrées

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Offres non migrées

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>En-tête de réponse

|  **Nom**             |      **Valeur**                                                            |
|  --------             |      ----------                                                           |
| Emplacement    |  Chemin relatif permettant de récupérer l’état de cette opération            |
|  |  |

### <a name="response-status-codes"></a>Codes d’état de réponse

| **Code** |  **Description**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` : la requête a été acceptée. La réponse contient un emplacement permettant de suivre l’état de l’opération. |
|  400     | `Bad/Malformed request` : le corps de réponse contient des informations d’erreur supplémentaires. |
|  404     |  `Not found` : l’entité spécifiée n’existe pas.                                       |
|  |  |
