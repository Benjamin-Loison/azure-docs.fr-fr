---
title: Activité de fonction Azure
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment utiliser l’activité Azure Function pour exécuter Azure Functions dans un pipeline Azure Data Factory ou Azure Synapse Analytics
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 4f3b928ef4cbb070afac7a127fcd893669724d3c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124749482"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Activité de fonction Azure dans Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
L’activité Azure Function vous permet d’exécuter [Azure Functions](../azure-functions/functions-overview.md) dans un pipeline Azure Data Factory ou Azure Synapse Analytics. Pour exécuter une fonction Azure, vous devez créer une connexion de service lié et une activité qui spécifie la fonction Azure que vous envisagez d’exécuter.

Pour une présentation de huit minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Service lié de fonction Azure


Le type de retour de la fonction Azure doit être un `JObject` valide (n’oubliez pas que [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) est *pas* un `JObject`). Tout type de retour autre que `JObject` échoue et génère l’erreur utilisateur *Le contenu de la réponse n’est pas un JObject valide*.

La clé de fonction permet un accès sécurisé aux noms de fonctions, chacune d’entre elles disposant de clés uniques distinctes ou d’une clé principale au sein d’une application de fonction. L’identité managée fournit un accès sécurisé à l’ensemble de l’application de fonction. L’utilisateur doit fournir une clé pour accéder au nom de la fonction. Pour plus d’informations sur la [clé d’accès aux fonctions](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#configuration), consultez la documentation sur les fonctions


| **Propriété** | **Description** | **Obligatoire** |
| --- | --- | --- |
| type   | La propriété type doit être définie sur : **AzureFunction** | Oui |
| URL de l’application de fonction | URL de l’application de fonction Azure. Son format est `https://<accountname>.azurewebsites.net`. Cette URL correspond à la valeur indiquée dans la section **URL** quand vous affichez votre application de fonction dans le portail Azure.  | Oui |
| clé de fonction | Clé d’accès de la fonction Azure. Cliquez sur la section **Gérer** de la fonction correspondante, puis copiez la **clé de fonction** ou la **clé d’hôte**. Découvrez-en plus ici : [Déclencheurs et liaisons HTTP Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | Oui |
|   |   |   |

## <a name="azure-function-activity"></a>Activité de fonction Azure

| **Propriété**  | **Description** | **Valeurs autorisées** | **Obligatoire** |
| --- | --- | --- | --- |
| name  | Nom de l’activité dans le pipeline  | String | Oui |
| type  | Le type d’activité est « AzureFunctionActivity ». | String | Oui |
| service lié | Service lié de fonction Azure de l’application de fonction Azure correspondante  | référence de service lié | Oui |
| nom de fonction  | Nom de la fonction dans l’application de fonction Azure que cette activité appelle. | String | Oui |
| method  | Méthode API REST de l’appel de fonction. | Types de chaîne pris en charge : « GET », « POST », « PUT »   | Oui |
| en-tête  | En-têtes envoyés à la demande. Par exemple, pour définir la langue et le type sur une demande : "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Chaîne (ou expression avec resultType de chaîne) | Non |
| body  | Corps envoyé avec la demande à la méthode d’api de la fonction  | Chaîne (ou expression avec resultType de chaîne) ou objet.   | Obligatoire pour les méthodes PUT/POST |
|   |   |   | |

Voir le schéma de la charge utile de demande dans la section [Schéma de la charge utile](control-flow-web-activity.md#request-payload-schema).

## <a name="routing-and-queries"></a>Routage et requêtes

L’activité de fonction Azure prend en charge le **routage**. Par exemple, si votre fonction Azure a un point de terminaison `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, le `functionName` à utiliser dans l’activité de fonction Azure est `<functionName>/<value>`. Vous pouvez paramétrer cette fonction pour fournir le `functionName` souhaité lors de l’exécution.

L’activité de fonction Azure prend également en charge les **requêtes**. Une requête doit être incluse dans le cadre de `functionName`. Par exemple, lorsque le nom de la fonction est `HttpTriggerCSharp` et que la requête que vous souhaitez inclure est `name=hello`, vous pouvez construire la `functionName` dans l’activité de fonction Azure en tant que `HttpTriggerCSharp?name=hello`. Cette fonction peut être paramétrée afin que la valeur soit déterminée au moment de l’exécution.

## <a name="timeout-and-long-running-functions"></a>Délai d’expiration et fonctions durables

Azure Functions expire après 230 secondes, quel que soit le paramètre `functionTimeout` que vous ayez configuré dans les paramètres. Pour plus d’informations, consultez [cet article](../azure-functions/functions-versions.md#timeout). Pour contourner ce comportement, appliquez un modèle asynchrone ou utilisez Durable Functions. L’avantage de Durable Functions que cette option utilise son propre mécanisme de suivi de l’état, donc vous n’aurez pas à implémenter le vôtre.

Plus d’informations sur Durable Functions dans [cet article](../azure-functions/durable/durable-functions-overview.md). Vous pouvez configurer une activité de fonction Azure pour appeler la fonction Durable Functions, qui renvoie une réponse avec un autre URI, comme dans [cet exemple](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Étant donné que `statusQueryGetUri` retourne l’état HTTP 202 pendant que la fonction est en cours d’exécution, vous pouvez interroger l’état de la fonction avec une activité web. Définissez simplement une activité web avec la valeur `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri` dans le champ `url`. Lorsque la fonction Durable Functions est terminée, la sortie de la fonction sera la sortie de l’activité web.


## <a name="sample"></a>Exemple

Un exemple où Azure Function est utilisé pour extraire le contenu d’un fichier tar est disponible [ici](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les activités prises en charge, consultez la page [Pipelines et activités](concepts-pipelines-activities.md).
