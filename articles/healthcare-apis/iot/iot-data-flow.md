---
title: Data Flow dans IoT Connector-API Azure Healthcare
description: Comprendre le workflow des données du connecteur IoT. Le connecteur IoT ingère, normalise, regroupe, transforme et conserve les données IoMT dans le service FHIR.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 090c051195559458314e89b47f3a0e4fcb6a838c
ms.sourcegitcommit: 1aeff9f012cfd868104ef0159c5204e402d75696
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133030898"
---
# <a name="iot-connector-data-flow"></a>Workflow de données du connecteur IoT

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Cet article fournit une vue d’ensemble du processus de données du connecteur IoT. Vous en apprendrez davantage sur les différentes étapes de traitement des données dans IoT Connector qui transforment les données des appareils en ressources d' [observation](https://www.hl7.org/fhir/observation.html) FHIR&#174;) rapides.

Les données des appareils liés à l’intégrité ou des appareils médicaux circulent dans un chemin d’accès dans lequel le connecteur IoT transforme les données en FHIR, puis les données sont stockées et accessibles depuis le service FHIR. Le chemin d’accès aux données d’intégrité suit ces étapes dans l’ordre suivant : ingérer, normaliser, grouper, transformer et conserver. Dans ce workflow, les données d’intégrité sont récupérées à partir de l’appareil lors de la première étape de l’ingestion. Une fois les données reçues, elles sont traitées ou normalisées selon les modèles de schéma sélectionnés par l’utilisateur ou par l’utilisateur, afin que les données d’intégrité soient plus simples à traiter et puissent être regroupées. Les données d’intégrité sont regroupées en trois paramètres de fonctionnement. Une fois les données d’intégrité normalisées et regroupées, elles peuvent être traitées ou transformées par le biais de mappages de destination FHIR, puis enregistrées ou rendues persistantes sur le service FHIR.

Cet article présente plus en détail chaque étape du Workflow. Les étapes suivantes [expliquent comment déployer un connecteur IOT](deploy-iot-connector-in-azure.md) à l’aide de mappages d’appareils (étape de normalisation) et de mappages de destination FHIR (étape de transformation).

Les sections suivantes décrivent les étapes que les données IoMT (Internet of Medical Things) passent une fois reçues d’un Event Hub et d’un connecteur IoT.

:::image type="content" source="media/iot-data-flow/iot-data-flow.png" alt-text="IoMT les flux de données des appareils IoT dans un Event Hub. Les données IoMT sont ingérées par le connecteur IoT, car elles sont normalisées, regroupées, transformées et conservées dans le service FHIR." lightbox="media/iot-data-flow/iot-data-flow.png":::

## <a name="ingest"></a>Ingérer
La réception est la première étape où les données de l’appareil sont reçues dans le connecteur IoT. Le point de terminaison d’ingestion pour les données de l’appareil est hébergé sur un [Event hubs Azure](../../event-hubs/index.yml). La plateforme Azure Event Hubs prend en charge une grande échelle et un débit élevé, avec la possibilité de recevoir et de traiter des millions de messages par seconde. Il permet également au connecteur IoT de consommer les messages de manière asynchrone, ce qui évite d’avoir à attendre que les données des appareils soient traitées.

> [!NOTE]
> JSON est le seul format actuellement pris en charge pour les données d’appareils.

## <a name="normalize"></a>Normaliser
Normalize est l’étape suivante dans laquelle les données de l’appareil sont récupérées de la Event Hub ci-dessus et traitées à l’aide des mappages d’appareils. Ce processus de mappage entraîne une transformation des données d’appareils en un schéma normalisé. 

Le processus de normalisation simplifie non seulement le traitement des données à des étapes ultérieures, mais il offre également la possibilité de projeter un message d’entrée en plusieurs messages normalisés. Par exemple, un appareil pourrait envoyer plusieurs signes vitaux pour la température du corps, la fréquence cardiaque, la pression artérielle et la fréquence respiratoire dans un message unique. Ce message d’entrée crée quatre ressources FHIR distinctes. Chaque ressource représente un signe vital différent, avec le message d’entrée projeté dans quatre messages normalisés différents.

## <a name="group"></a>Grouper
Le groupe est l’étape suivante dans laquelle les messages normalisés disponibles à partir de l’étape précédente sont regroupés à l’aide de trois paramètres différents :

* Identité d’appareil
* Type de mesure 
* Période

Lee groupements par identité de l’appareil et types de mesure permettent d’utiliser le type de mesure [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData). Ce type fournit un moyen concis de représenter une série de mesures basées sur le temps d’un appareil dans FHIR. Et la période de temps contrôle la latence à laquelle les ressources d’observation générées par le connecteur IoT sont écrites dans le service FHIR.

> [!NOTE]
> La valeur de la période est définie par défaut sur 15 minutes et ne peut pas être configurée pour la préversion.

## <a name="transform"></a>Transformer
Dans l’étape de transformation, les messages normalisés groupés sont traités par le biais de modèles de mappage de destination FHIR. Les messages correspondant à un type de modèle sont transformés en ressources d’observation basées sur FHIR, comme spécifié via le mappage.

À ce stade, la ressource de l' [appareil](https://www.hl7.org/fhir/device.html) , ainsi que la ressource du [patient](https://www.hl7.org/fhir/patient.html) qui lui est associée, sont également récupérées à partir du service FHIR à l’aide de l’identificateur d’appareil présent dans le message. Ces ressources sont ajoutées en tant que référence à la ressource Observation en cours de création.

> [!NOTE]
> Toutes les recherches d’identité sont mises en cache une fois résolues pour réduire la charge sur le service FHIR. Si vous prévoyez de réutiliser des appareils avec plusieurs patients, il est recommandé de créer une ressource appareil virtuel spécifique du patient et d’envoyer l’identificateur d’appareil virtuel dans la charge utile du message. L’appareil virtuel peut être lié à la ressource appareil réelle en tant que parent.

S’il n’existe aucune ressource d’appareil pour un identificateur d’appareil donné dans le service FHIR, le résultat dépend de la valeur de `Resolution Type` définie au moment de la création. Lorsque la valeur est définie sur `Lookup`, le message spécifique est ignoré et le pipeline continue de traiter d’autres messages entrants. Si la valeur `Create` est, le connecteur IOT crée un appareil nu et des ressources pour les patients sur le service FHIR.  

## <a name="persist"></a>Conserver
Une fois la ressource FHIR d’observation générée à l’étape de transformation, la ressource est enregistrée dans le service FHIR. Si la ressource FHIR d’observation est nouvelle, elle sera créée sur le service FHIR. Si la ressource FHIR d’observation existe déjà, elle sera mise à jour.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer des mappages de destination appareil et FHIR.

> [!div class="nextstepaction"]
> [Mappages d’appareils](how-to-use-device-mappings.md)

> [!div class="nextstepaction"]
> [Mappages de destination FHIR](how-to-use-fhir-mappings.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
