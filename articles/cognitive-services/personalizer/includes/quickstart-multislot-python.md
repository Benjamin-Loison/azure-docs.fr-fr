---
title: Fichier Include
description: Fichier include
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 03/23/2021
ms.openlocfilehash: da6a271275c0b3b4f8d412bf622e6171609b3128
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255767"
---
[Conceptuel sur plusieurs emplacements](..\concept-multi-slot-personalization.md) | [Échantillons](https://aka.ms/personalizer/ms-python)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="créez une ressource Personalizer"  target="_blank">créer une ressource Personalizer</a> sur le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Personalizer. Collez votre clé et votre point de terminaison dans le code ci-dessous, plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

[!INCLUDE [Upgrade Personalizer instance to Multi-Slot](upgrade-personalizer-multi-slot.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

[!INCLUDE [Change reward wait time](change-reward-wait-time.md)]

### <a name="create-a-new-python-application"></a>Créer une application Python

Créez un fichier Python et des variables pour le point de terminaison et la clé d’abonnement de votre ressource.

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```python
import json, uuid, requests

# The endpoint specific to your personalization service instance; 
# e.g. https://<your-resource-name>.cognitiveservices.azure.com
PERSONALIZATION_BASE_URL = "<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>"
# The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
RESOURCE_KEY = "<REPLACE-WITH-YOUR-PERSONALIZER-KEY>"
```

## <a name="object-model"></a>Modèle objet

Pour demander le seul meilleur élément du contenu pour chaque emplacement, créez un **rank_request**, puis envoyez une demande de publication au point de terminaison [multislot/rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Rank). La réponse est ensuite analysée dans un **rank_response**.

Pour envoyer un score de récompense à Personalizer, créez un **rewards**, puis envoyez une demande de publication à [multislot/events/{eventId}/reward](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Events_Reward).

Dans ce guide de démarrage rapide, il est facile de déterminer le score de récompense. Dans un système de production, déterminer les éléments ayant un impact sur le [score de récompense](../concept-rewards.md) (et dans quelle mesure) peut être un processus complexe, que vous pouvez décider de modifier au fil du temps. Cette décision de conception doit être l’une des principales décisions à prendre pour votre architecture Personalizer.

## <a name="code-examples"></a>Exemples de code

Ces extraits de code vous montrent comment effectuer les tâches suivantes en envoyant des requêtes HTTP pour Python :

* [Créer des URL de base](#create-base-urls)
* [API Rank](#request-the-best-action)
* [API Reward](#send-a-reward)

## <a name="create-base-urls"></a>Créer des URL de base

Dans cette section, vous allez construire les URL de classement/récompense à l’aide de l’URL de base et des en-têtes de demande à l’aide de la clé de ressource.

```python
MULTI_SLOT_RANK_URL = '{0}personalizer/v1.1-preview.1/multislot/rank'.format(PERSONALIZATION_BASE_URL)
MULTI_SLOT_REWARD_URL_BASE = '{0}personalizer/v1.1-preview.1/multislot/events/'.format(PERSONALIZATION_BASE_URL)
HEADERS = {
    'ocp-apim-subscription-key': RESOURCE_KEY,
    'Content-Type': 'application/json'
}
```

## <a name="get-content-choices-represented-as-actions"></a>Représenter les choix de contenu sous forme d’actions

Les actions représentent les choix de contenu parmi lesquels Personalizer doit sélectionner l’élément de contenu le mieux adapté. Ajoutez les méthodes suivantes au script pour représenter l’ensemble d’actions et leurs caractéristiques. 

```python
def get_actions():
    return [
        {
            "id": "Red-Polo-Shirt-432",
            "features": [
                {
                    "onSale": "true",
                    "price": 20,
                    "category": "Clothing"
                }
            ]
        },
        {
            "id": "Tennis-Racket-133",
            "features": [
                {
                    "onSale": "false",
                    "price": 70,
                    "category": "Sports"
                }
            ]
        },
        {
            "id": "31-Inch-Monitor-771",
            "features": [
                {
                    "onSale": "true",
                    "price": 200,
                    "category": "Electronics"
                }
            ]
        },
        {
            "id": "XBox-Series X-117",
            "features": [
                {
                    "onSale": "false",
                    "price": 499,
                    "category": "Electronics"
                }
            ]
        }
    ]

```

## <a name="get-user-preferences-for-context"></a>Obtenir les préférences utilisateur pour le contexte

Ajoutez les méthodes suivantes au script pour obtenir une entrée d’utilisateur à partir de la ligne de commande pour l’heure de la journée et le type d’appareil sur lequel se trouve l’utilisateur. Elles seront utilisées comme des caractéristiques de contexte.

```python
def get_context_features():
    time_features = ["morning", "afternoon", "evening", "night"]
    time_pref = input("What time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n")
    try:
        parsed_time = int(time_pref)
        if(parsed_time <=0 or parsed_time > len(time_features)):
            raise IndexError
        time_of_day = time_features[parsed_time-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", time_features[0] + ".")
        time_of_day = time_features[0]

    device_features = ['mobile', 'tablet', 'desktop']
    device_pref = input("What type of device is the user on (enter number)? 1. mobile 2. tablet 3. desktop\n")
    try:
        parsed_device = int(device_pref)
        if(parsed_device <=0 or parsed_device > len(device_features)):
            raise IndexError
        device = device_features[parsed_device-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", device_features[0]+ ".")
        device = device_features[0]

    return [
        {'time': time_of_day},
        {'device': device}
        ]
```

## <a name="get-slots"></a>Accéder aux emplacements

Les emplacements constituent la page avec laquelle l’utilisateur interagit. Personalizer décidera de l’action à afficher dans chacun des emplacements définis. Les actions peuvent être exclues d’emplacements spécifiques, comme `ExcludeActions`. `BaselineAction` est l’action par défaut pour l’emplacement qui aurait été affiché sans l’utilisation de Personalizer.

Ce guide de démarrage rapide offre des fonctionnalités d’emplacement simples. Dans les systèmes de production, il peut être important de déterminer et d’[évaluer](../concept-feature-evaluation.md) les [caractéristiques](../concepts-features.md).

```python
def get_slots():
    return [
        {
            "id": "BigHeroPosition",
            "features": [
                {
                    "size": "large",
                    "position": "left",
                }
            ],
            "excludedActions": ["31-Inch-Monitor-771"],
            "baselineAction": "Red-Polo-Shirt-432"
        },
        {
            "id": "SmallSidebar",
            "features": [
                {
                    "size": "small",
                    "position": "right",
                }
            ],
            "excludedActions": ["Tennis-Racket-133"],
            "baselineAction": "XBox-Series X-117"
        }
    ]
```

## <a name="make-http-requests"></a>Effectuer des requêtes HTTP

Ajoutez ces fonctions pour envoyer des requêtes de publication au point de terminaison Personalizer pour les appels de classement et de récompense à plusieurs emplacements.

```python
def send_multi_slot_rank(rank_request):
multi_slot_response = requests.post(MULTI_SLOT_RANK_URL, data=json.dumps(rank_request), headers=HEADERS)
if multi_slot_response.status_code != 201:
    raise Exception(multi_slot_response.text)
return json.loads(multi_slot_response.text)
```

```python
def send_multi_slot_reward(reward_request, event_id):
    reward_url = '{0}{1}/reward'.format(MULTI_SLOT_REWARD_URL_BASE, event_id)
    requests.post(reward_url, data=json.dumps(reward_request), headers=HEADERS)
```

## <a name="get-feedback-for-personalizer-decisions"></a>Obtenir des commentaires pour les décisions de Personalizer

Ajoutez la méthode suivante au script. L’invite de la ligne de commande vous indiquera si Personalizer a pris une bonne décision pour chaque emplacement.

```python
def get_reward_for_slot():
    answer = input('\nIs this correct? (y/n)\n').upper()
    if (answer == 'Y'):
        print('\nGreat! The application will send Personalizer a reward of 1 so it learns from this choice of action for this slot.\n')
        return 1
    elif (answer == 'N'):
        print('\nYou didn\'t like the recommended item.The application will send Personalizer a reward of 0 for this choice of action for this slot.\n')
        return 0
    print('\nEntered choice is invalid. Service assumes that you didn\'t like the recommended item.\n')
    return 0
```

## <a name="create-the-learning-loop"></a>Créer la boucle d’apprentissage

La boucle d’apprentissage Personalizer est un cycle d’appels aux API [Rank](#request-the-best-action) et [Reward](#send-a-reward). Dans ce guide de démarrage rapide, chaque appel Rank en vue de personnaliser le contenu est suivi d’un appel Reward qui indique à Personalizer l’efficacité du service.

Le code ci-après applique, en boucle, le cycle suivant : il demande à l’utilisateur d’indiquer ses préférences à partir de la ligne de commande, il envoie ces informations à Personalizer en vue de sélectionner le contenu le mieux adapté pour chaque emplacement, il présente la sélection au client qui peut faire son choix dans la liste, puis il envoie un score de récompense à Personalizer, en indiquant l’efficacité du service concernant le choix du contenu.

```python
run_loop = True

while run_loop:

    eventId = str(uuid.uuid4())
    context = get_context_features()
    actions = get_actions()
    slots = get_slots()

    rank_request = {
        "eventId": eventId,
        "contextFeatures": context,
        "actions": actions,
        "slots": slots,
        "deferActivation": False
      }

    #Rank the actions for each slot
    multi_slot_rank_response = send_multi_slot_rank(rank_request)
    multi_slot_rewards = {"reward": []}

    for i in range(len(multi_slot_rank_response['slots'])):
        print('\nPersonalizer service decided you should display: {0} in slot {1}\n'.format(multi_slot_rank_response['slots'][i]['rewardActionId'], multi_slot_rank_response['slots'][i]['id']))

        slot_reward = {'slotId': multi_slot_rank_response['slots'][i]['id']}
        # User agrees or disagrees with Personalizer decision for slot
        slot_reward['value'] = get_reward_for_slot()
        multi_slot_rewards['reward'].append(slot_reward)

    # Send the rewards for the event
    send_multi_slot_reward(multi_slot_rewards, multi_slot_rank_response['eventId'])

    answer = input('\nPress q to break, any other key to continue:\n').upper()
    if (answer == 'Q'):
        run_loop = False
```

Dans les sections suivantes, examinez de plus près les appels de classement et de récompense.

Ajoutez les méthodes suivantes, qui permettent d’[obtenir les choix de contenu](#get-content-choices-represented-as-actions), d’[obtenir les préférences de l’utilisateur pour le contexte](#get-user-preferences-for-context), d’[obtenir les emplacements](#get-slots), d’[exécuter des requêtes HTTP](#make-http-requests), d’[obtenir une récompense pour chaque emplacement](#get-feedback-for-personalizer-decisions) avant d’exécuter le fichier de code :

* get_actions
* get_context_features
* get_slots
* send_rank
* send_reward
* get_reward_for_dsot

## <a name="request-the-best-action"></a>Demander l’action la mieux adaptée

Pour traiter la requête Rank, le programme demande les préférences de l’utilisateur pour créer des choix de contenu. Le corps de la requête contient le contexte, les actions et les emplacements avec leurs caractéristiques respectives. La méthode `send_multi_slot_rank` prend un rankRequest et exécute la demande de classement sur plusieurs emplacements.

Ce guide de démarrage rapide utilise des caractéristiques de contexte simples basées sur l’heure de la journée et l’appareil de l’utilisateur. Dans les systèmes de production, il peut être important de déterminer et d’[évaluer](../concept-feature-evaluation.md) les [actions et caractéristiques](../concepts-features.md).

```python
eventId = str(uuid.uuid4())
context = get_context_features()
actions = get_actions()
slots = get_slots()

rank_request = {
    "eventId": eventId,
    "contextFeatures": context,
    "actions": actions,
    "slots": slots,
    "deferActivation": False
    }

#Rank the actions for each slot
multi_slot_rank_response = send_multi_slot_rank(rank_request)
```

## <a name="send-a-reward"></a>Envoyer une récompense

Pour obtenir le score de récompense pour la requête Reward, le programme récupère la sélection de l’utilisateur pour chaque emplacement à partir de la ligne de commande, attribue une valeur numérique (score de récompense) à la sélection, puis envoie à la méthode `send_multi_slot_reward` l’ID d’événement unique et le score de récompense pour chaque emplacement. Une récompense n’a pas besoin d’être définie pour chaque emplacement.

Dans le cadre de ce guide de démarrage rapide, un simple numéro est attribué en tant que score de récompense : 0 ou 1. Dans les systèmes de production, il peut être important de déterminer ce qui doit être envoyé à l’appel [Reward](../concept-rewards.md) (et à quel moment) selon vos besoins.

```python
multi_slot_rewards = {"reward": []}

for i in range(len(multi_slot_rank_response['slots'])):
    print('\nPersonalizer service decided you should display: {0} in slot {1}\n'.format(multi_slot_rank_response['slots'][i]['rewardActionId'], multi_slot_rank_response['slots'][i]['id']))

    slot_reward = {'slotId': multi_slot_rank_response['slots'][i]['id']}
    # User agrees or disagrees with Personalizer decision for slot
    slot_reward['value'] = get_reward_for_slot()
    multi_slot_rewards['reward'].append(slot_reward)

# Send the rewards for the event
send_multi_slot_reward(multi_slot_rewards, multi_slot_rank_response['eventId'])
```

## <a name="run-the-program"></a>Exécuter le programme

Exécutez l’application avec Python à partir de votre répertoire d’application.

```console
python sample.py
```

![Ce programme de démarrage rapide pose quelques questions pour recueillir les préférences de l’utilisateur, appelées « caractéristiques », puis fournit l’action classée en premier.](../media/csharp-quickstart-commandline-feedback-loop/multislot-quickstart-program-feedback-loop-example-1.png)


Le [code source associé à ce guide de démarrage rapide](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer/multislot-quickstart) est disponible.
