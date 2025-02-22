---
title: Démarrage rapide – Création d’un registre – PowerShell
description: Apprenez rapidement à créer un registre de conteneurs Docker privé dans Azure Container Registry avec PowerShell.
ms.date: 06/03/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, mvc, mode-api
ms.openlocfilehash: 448b692adb603c884f1034bf5d7234e459ce5f66
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043487"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Démarrage rapide : Créer un registre de conteneurs privé avec Azure PowerShell

Azure Container Registry est un service de registre privé permettant de créer, de stocker et de gérer des images conteneurs et des artefacts connexes. Dans ce démarrage rapide, vous créez une instance de registre de conteneurs Azure avec Azure PowerShell. Vous allez ensuite utiliser des commandes Docker pour envoyer (push) une image conteneur dans le registre, puis tirer (pull) et exécuter l’image à partir de votre registre.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ce guide de démarrage rapide nécessite le module Azure PowerShell. Exécutez `Get-Module -ListAvailable Az` pour déterminer la version installée. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps).

Docker doit également être installé en local. Docker fournit des packages pour [macOS][docker-mac], [Windows][docker-windows] et [Linux][docker-linux].

Étant donné qu’Azure Cloud Shell n’inclut pas tous les composants Docker requis (par exemple, le démon `dockerd`), vous ne pouvez pas l’utiliser pour ce démarrage rapide.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande [Connect-AzAccount][Connect-AzAccount] et suivez les instructions indiquées à l’écran.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Une fois votre authentification auprès d’Azure effectuée, créez un groupe de ressources avec [New-AzResourceGroup][New-AzResourceGroup]. Un groupe de ressources est un conteneur logique dans lequel vous déployez et gérez vos ressources Azure.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Créer un registre de conteneurs

Ensuite, créez un registre de conteneurs dans votre nouveau groupe de ressources avec la commande [New-AzContainerRegistry][New-AzContainerRegistry].

Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques. L’exemple suivant crée un registre appelé « myContainerRegistry007 ». Remplacez *myContainerRegistry007* dans la commande suivante, puis exécutez-la pour créer le registre :

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

[!INCLUDE [container-registry-quickstart-sku](../../includes/container-registry-quickstart-sku.md)]

## <a name="log-in-to-registry"></a>Se connecter au registre

Avant d’envoyer (push) et de tirer (pull) des images conteneurs, vous devez vous connecter à votre registre avec la cmdlet [Connect-AzContainerRegistry][connect-azcontainerregistry]. L’exemple suivant utilise les informations d’identification que vous avez utilisées pour vous authentifier auprès d’Azure avec la cmdlet `Connect-AzAccount`.

> [!NOTE]
> Dans l’exemple suivant, la valeur de `$registry.Name` correspond au nom de la ressource, et non au nom de registre complet.

```powershell
Connect-AzContainerRegistry -Name $registry.Name
```

Une fois l’opération terminée, la commande renvoie `Login Succeeded`.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous avez terminé d’utiliser les ressources créées dans ce guide de démarrage rapide, utilisez la commande [Remove-AzResourceGroup][Remove-AzResourceGroup] pour supprimer le groupe de ressources, le registre de conteneurs et les images conteneur stockées à cet endroit :

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un registre de conteneurs Azure avec Azure PowerShell, envoyé (push) une image conteneur, puis tiré (pull) et exécuté l’image à partir du registre. Passez à présent au tutoriel sur Azure Container Registry (ACR) pour approfondir vos connaissances.

> [!div class="nextstepaction"]
> [Tutoriels sur Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Tutoriels Azure Container Registry Tasks][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[connect-azcontainerregistry]: /powershell/module/az.containerregistry/connect-azcontainerregistry
