---
title: Dépannage du déploiement de modèle local
titleSuffix: Azure Machine Learning
description: Essayez un déploiement de modèle local comme première étape de dépannage des erreurs de déploiement de modèle.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.reviewer: luquinta
ms.date: 10/21/2021
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 6db00dfa109bad8acf0f9a56b9dd2559e5999d3e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560717"
---
# <a name="troubleshooting-with-a-local-model-deployment"></a>Dépannage avec un déploiement de modèle local

Essayez un déploiement de modèle local comme première étape de dépannage d’un déploiement sur Azure Container Instances (ACI) ou Azure Kubernetes service (AKS).  L’utilisation d’un service web local facilite la localisation et la correction des erreurs courantes de déploiement Docker liées au service web Azure Machine Learning.

## <a name="prerequisites"></a>Prérequis

* Un **abonnement Azure**. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/).
* Option A (**recommandée**) : déboguer localement sur une instance de calcul Azure Machine Learning
   * Un espace de travail Azure Machine Learning avec une [instance de calcul](how-to-deploy-local-container-notebook-vm.md) en cours d’exécution
* Option B : déboguer localement sur votre calcul
   * Le [Kit de développement logiciel (SDK) Azure Machine Learning](/python/api/overview/azure/ml/install).
   * [Interface de ligne de commande Azure](/cli/azure/install-azure-cli).
   * [Extension CLI pour Azure Machine Learning](reference-azure-machine-learning-cli.md).
   * Installation de Docker opérationnelle sur votre système local. 
   * Pour vérifier votre installation de Docker, utilisez la commande `docker run hello-world` à partir d’un terminal ou d’une invite de commandes. Pour obtenir des informations sur l’installation de Docker ou sur la résolution des erreurs Docker, consultez la [documentation Docker](https://docs.docker.com/).
* Option C : Activer le débogage local avec le serveur HTTP d’inférence Azure Machine Learning.
    * Le serveur HTTP d’inférence Azure Machine Learning [(préversion)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) est un package Python qui vous permet de valider facilement votre script d’entrée (`score.py`) dans un environnement de développement local. En cas de problème avec le script de scoring, le serveur retourne une erreur. Il retourne également l’emplacement où l’erreur s’est produite.
    * Le serveur peut également être utilisé lors de la création de portes de validation dans un pipeline d’intégration et de déploiement continus. Par exemple, démarrez le serveur avec le script candidat et exécutez la suite de tests sur le point de terminaison local.

## <a name="azure-machine-learning-inference-http-server"></a>Serveur HTTP d’inférence Azure Machine Learning

Le serveur d’inférence local vous permet de déboguer rapidement votre script d’entrée (`score.py`). Si un bogue affecte le script de scoring sous-jacent, le serveur ne pourra pas initialiser ou traiter le modèle. Au lieu de cela, il lèvera une exception là où les problèmes se sont produits. [Apprenez-en davantage sur le serveur HTTP d’inférence Azure Machine Learning](how-to-inference-server-http.md)

1. Installez le package `azureml-inference-server-http` à partir du flux [pypi](https://pypi.org/) :

    ```bash
    python -m pip install azureml-inference-server-http
    ```

2. Démarrez le serveur et définissez `score.py` comme script d’entrée :

    ```bash
    azmlinfsrv --entry_script score.py
    ```

3. Envoyez une requête de scoring au serveur à l’aide de `curl` :

    ```bash
    curl -p 127.0.0.1:5001/score
    ```

## <a name="debug-locally"></a>Déboguer en local

Vous trouverez un exemple exécutable de [notebook de déploiement local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) dans le référentiel [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks).

> [!WARNING]
> Les déploiements de service web locaux ne sont pas pris en charge pour les scénarios de production.

Pour déployer en local, modifiez votre code pour utiliser `LocalWebservice.deploy_configuration()` afin de créer une configuration de déploiement. Puis, utilisez `Model.deploy()` pour déployer le service. L’exemple suivant déploie un modèle (contenu dans la variable model) en tant que service web local :

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Si vous définissez votre propre YAML de spécification Conda, faites figurer une version supérieure ou égale à la version 1.0.45 d'azureml-defaults comme dépendance pip. Ce package est nécessaire pour héberger le modèle en tant que service web.

À ce stade, vous pouvez travailler avec le service comme d’habitude. Le code suivant illustre l'envoi des données au service :

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Pour plus d’informations sur la personnalisation de votre environnement Python, consultez [Créer et gérer des environnements pour l’entraînement et le déploiement](how-to-use-environments.md). 

### <a name="update-the-service"></a>Mettre à jour le service

Pendant le test local, vous devrez peut-être mettre à jour le fichier `score.py` pour ajouter la journalisation ou tenter de résoudre les problèmes détectés. Pour recharger les modifications apportées au fichier `score.py`, utilisez `reload()`. Par exemple, le code suivant recharge le script pour le service et lui envoie des données. Les données sont calculées à l’aide du fichier `score.py` mis à jour :

> [!IMPORTANT]
> La méthode `reload` est disponible uniquement pour les déploiements locaux. Pour en savoir plus sur la mise à jour d’un déploiement vers une autre cible de calcul, consultez la [procédure de mise à jour de vos services web](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Le script est rechargé à partir de l’emplacement spécifié par l’objet `InferenceConfig` utilisé par le service.

Pour modifier le modèle, les dépendances Conda ou la configuration de déploiement, utilisez [update()](/python/api/azureml-core/azureml.core.webservice%28class%29#update--args-). L’exemple suivant met à jour le modèle utilisé par le service :

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Supprimer le service

Pour supprimer le service, utilisez [delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Examiner le journal Docker

Vous pouvez afficher les messages détaillés du journal du moteur Docker à partir de l’objet de service. Vous pouvez afficher le journal pour les déploiements ACI, AKS et locaux. L’exemple suivant illustre l’impression des journaux.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

Si la ligne `Booting worker with pid: <pid>` s’affiche plusieurs fois dans les journaux, cela signifie qu’il n’y a pas assez de mémoire pour démarrer le Worker.
Vous pouvez résoudre l’erreur en augmentant la valeur de `memory_gb` dans `deployment_config`.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement :

* [Comment résoudre les problèmes liés aux déploiements distants](how-to-troubleshoot-deployment.md)
* [Serveur HTTP d’inférence Azure Machine Learning](how-to-inference-server-http.md)
* [Où et comment effectuer un déploiement](how-to-deploy-and-where.md)
* [Tutoriel : Effectuer l'apprentissage de modèles et les déployer](tutorial-train-models-with-aml.md)
* [Comment exécuter et déboguer des expériences localement](./how-to-debug-visual-studio-code.md)