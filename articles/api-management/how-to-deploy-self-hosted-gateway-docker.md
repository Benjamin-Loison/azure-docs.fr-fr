---
title: Déployer une passerelle auto-hébergée sur Docker
description: Découvrez comment déployer la passerelle auto-hébergée de la Gestion des API Azure sur Docker.
author: dlepow
manager: gwallace
ms.service: api-management
ms.topic: article
ms.date: 04/19/2021
ms.author: danlep
ms.openlocfilehash: 3ef8e0316b6df0b95f2163b6df8ae139ebb8fe6b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580943"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Déployer une passerelle auto-hébergée de gestion des API Azure sur Docker

Cet article explique comment déployer la passerelle auto-hébergée de la Gestion des API Azure dans un environnement Docker.

> [!NOTE]
> L’hébergement d’une passerelle auto-hébergée dans Docker convient pour l’évaluation, ainsi que pour les cas d’utilisation de développement. Il est recommandé d’utiliser Kubernetes dans un environnement de production. Pour savoir comment déployer une passerelle auto-hébergée sur Kubernetes, consultez [ce document](how-to-deploy-self-hosted-gateway-kubernetes.md).

## <a name="prerequisites"></a>Prérequis

- Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
- Créez un environnement Docker. [Docker pour le bureau](https://www.docker.com/products/docker-desktop) est une bonne option en matière de développement et d’évaluation. Pour plus d’informations sur toutes les éditions de Docker, leurs fonctionnalités et une documentation complète sur Docker, voir la [Documentation Docker](https://docs.docker.com).
- [Approvisionnez une ressource de passerelle dans votre instance Gestion des API](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> La passerelle auto-hébergée se présente sous la forme d’un conteneur Docker basé sur Linux x86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Déployer une passerelle auto-hébergée sur Docker

1. Sélectionnez **Gateways** (Passerelles) sous **Deployment and infrastructure** (Déploiement et infrastructure).
2. Sélectionnez la ressource de passerelle que vous souhaitez déployer.
3. Sélectionnez **Déploiement**.
4. Remarquez qu’un jeton d’accès a été généré automatiquement dans la zone de texte **Token** (Jeton), avec les valeurs par défaut pour l’expiration (**Expiry**) et la clé secrète (**Secret key**). Si nécessaire, sélectionnez les valeurs souhaitées dans l’un des contrôles ou dans les deux pour générer un jeton.
5. Assurez-vous que **Docker** est sélectionné sous **Scripts de déploiement**.
6. Sélectionnez le lien de fichier **env. conf** en regard de l’**Environnement** pour télécharger le fichier.
7. Sélectionnez l’icône **Copier** située à l’extrémité droite de la zone de texte **Exécuter** pour copier la commande Docker dans le Presse-papiers.
8. Collez la commande dans la fenêtre (ou la commande) du terminal. Ajustez les mappages de port et le nom de conteneur si nécessaire. Notez que la commande s’attend à ce que le fichier d’environnement téléchargé soit présent dans le répertoire actif.

   ```
   docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
   ```

9. Exécutez la commande. La commande demande à votre environnement Docker d’exécuter le conteneur à l’aide de l’[image conteneur](https://aka.ms/apim/sputnik/dhub) téléchargée à partir de Microsoft Container Registry, et de mapper les ports HTTP (8080) et HTTPS (8081) du conteneur aux ports 80 et 443 de l’hôte.
10. Exécutez la commande ci-dessous pour vérifier que le conteneur de la passerelle est en cours d’exécution :
    ```console
    docker ps
    CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
    895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
    ```
10. Revenez au portail Azure, cliquez sur **Vue d’ensemble**, puis vérifiez que le conteneur de la passerelle auto-hébergée que vous venez de déployer indique un état sain.

    ![état de la passerelle](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> Utilisez la commande `console docker container logs <gateway-name>` pour afficher un instantané du journal de la passerelle auto-hébergée.
>
> Utilisez la commande `docker container logs --help` pour voir toutes les options d’affichage des journaux.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la passerelle auto-hébergée, consultez [Vue d’ensemble de la passerelle auto-hébergée Gestion des API](self-hosted-gateway-overview.md).
* [Configurer un nom de domaine personnalisé pour la passerelle auto-hébergée](api-management-howto-configure-custom-domain-gateway.md).
