---
title: Créer et explorer des jeux de données avec des étiquettes
titleSuffix: Azure Machine Learning
description: Apprenez à exporter des étiquettes de données à partir de vos projets d’étiquetage Azure Machine Learning, ainsi qu’à les utiliser pour des tâches de Machine Learning.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.custom: data4ml
ms.date: 10/21/2021
ms.openlocfilehash: 53abb99aef3a04f3c975cd0fbde4d1097e01269b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560314"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Créer et explorer un jeu de données Azure Machine Learning avec des étiquettes

Cet article explique comment exporter les étiquettes de données d’un projet d’étiquetage de données Azure Machine Learning, et les charger dans des formats populaires tels qu’une tramedonnées pandas pour l’exploration de données ou un jeu de données Torchvision pour la transformation d’image. 

## <a name="what-are-datasets-with-labels"></a>Que sont les jeux de données avec étiquettes 

Nous faisons référence aux jeux de données Azure Machine Learning avec des étiquettes sous la forme de jeux de données étiquetés. Ces types de jeux de données étiquetés spécifiques sont créés uniquement en tant que sortie de projets d’étiquetage de données Azure Machine Learning. Créez un projet d’étiquetage des données [pour l’étiquetage d’image](how-to-create-image-labeling-projects.md) ou [l’étiquetage de texte](how-to-create-text-labeling-projects.md). Machine Learning prend en charge les projets d’étiquetage de données pour la classification d’images (comprenant plusieurs étiquettes ou classes), ainsi que l’identification d’objets à l’aide de zones délimitées.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Le [Kit de développement logiciel (SDK) Azure Machine Learning pour Python](/python/api/overview/azure/ml/intro) ou l’accès à [Azure Machine Learning studio](https://ml.azure.com/).
    * Installer le package [azure-contrib-dataset](/python/api/azureml-contrib-dataset/)
* Un espace de travail Machine Learning. Consultez [Créer un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md).
* Accédez à un projet d’étiquetage de données Azure Machine Learning. Si vous n’avez pas de projet d’étiquetage, commencez par en créer un pour l’[étiquetage d’image](how-to-create-image-labeling-projects.md) ou l’[étiquetage de texte](how-to-create-text-labeling-projects.md).

## <a name="export-data-labels"></a>Exporter des étiquettes de données 

Lorsque vous terminez un projet d’étiquetage de données, vous pouvez en exporter les données d’étiquette. Cela vous permet de capturer la référence aux données et ses étiquettes, puis de les exporter au [format COCO](http://cocodataset.org/#format-data) ou en tant que jeu de données Azure Machine Learning. Utilisez le bouton **Exporter** de la page **Détails du projet** de votre projet d’étiquetage.

### <a name="coco"></a>COCO 

 Le fichier COCO est créé dans le magasin d’objets blob par défaut de l’espace de travail Azure Machine Learning, dans un dossier situé au sein de *export/coco*. 
 
>[!NOTE]
>Dans les projets de détection d’objets, les valeurs exportées « bbox : [x, y, largeur, hauteur] » dans le fichier COCO sont normalisées. Elles sont mises à l’échelle à 1. Exemple : un cadre englobant à l’emplacement (10, 10), avec une largeur de 30 pixels, une hauteur de 60 pixels, dans une image de 640x480 pixels, est annoté (0.015625. 0.02083, 0.046875, 0.125). Étant donné que les coordonnées sont normalisées, la valeur sera « 0.0 » pour la « largeur » et la « hauteur » pour toutes les images. La largeur et la hauteur réelles peuvent être obtenues à l’aide de l’une bibliothèque Python comme OpenCV ou Pillow (PIL).

### <a name="azure-machine-learning-dataset"></a>Jeu de données Azure Machine Learning

Vous pouvez accéder au jeu de données Azure Machine Learning exporté dans la section **Jeux de données** de votre studio Azure Machine Learning. La page **Détails** du jeu de données fournit également un exemple de code pour accéder à vos étiquettes à partir de Python.

![Jeu de données exporté](./media/how-to-create-labeling-projects/exported-dataset.png)

Une fois que vous avez exporté vos données étiquetées vers un jeu de données Azure Machine Learning, vous pouvez utiliser AutoML pour créer des modèles de vision par ordinateur formés sur vos données étiquetées. En savoir plus : [Configurer AutoML pour effectuer l’apprentissage de modèles de vision par ordinateur avec Python (préversion)](how-to-auto-train-image-models.md)

## <a name="explore-labeled-datasets"></a>Explorer les jeux de données étiquetés

Chargez vos jeux de données étiquetés dans une tramedonnées pandas ou un jeu de données torchvision pour tirer parti de bibliothèques open source populaires pour l’exploration de données, ainsi que de bibliothèques fournies par PyTorch pour la transformation et la formation d’images.

### <a name="pandas-dataframe"></a>Tramedonnées Pandas

Vous pouvez charger des jeux de données étiquetés dans une tramedonnées pandas avec la méthode [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) de la classe `azureml-contrib-dataset`. Installez la classe avec la commande d’interpréteur de commandes suivante : 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>L’espace de noms azureml.contrib change fréquemment car nous travaillons à améliorer le service. Par conséquent, tout ce qui est compris dans cet espace de noms doit être considéré comme une préversion et n’est pas entièrement pris en charge par Microsoft.

Azure Machine Learning propose les options de gestion de fichiers suivantes pour les flux de fichiers lors de la conversion en dataframes pandas.
* Télécharger : Téléchargez vos fichiers de données vers un chemin d’accès local.
* Monter : Montez vos fichiers de données sur un point de montage. Le montage fonctionne uniquement pour le calcul basé sur Linux, incluant les machines virtuelles de notebooks Azure Machine Learning et la capacité de calcul Azure Machine Learning.

Dans le code suivant, le jeu de données `animal_labels` est la sortie d’un projet d’étiquetage enregistré dans l’espace de travail.

```Python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Jeux de données Torchvision

Vous pouvez charger des jeux de données étiquetés dans un jeu de données Torchvision avec la méthode [to_torchvision()](/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset#to-torchvision--) également à partir de la classe `azureml-contrib-dataset`. Pour pouvoir utiliser cette méthode, [PyTorch](https://pytorch.org/) doit être installé. 

Dans le code suivant, le jeu de données `animal_labels` est la sortie d’un projet d’étiquetage enregistré dans l’espace de travail.

```python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

from torchvision.transforms import functional as F

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Étapes suivantes
