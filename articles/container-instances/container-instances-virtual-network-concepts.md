---
title: Scénarios d’utilisation d’un réseau virtuel
description: Scénarios, ressources et limitations relatifs au déploiement de groupes de conteneurs sur un réseau virtuel Azure.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 7f0e2719d6949037e2268f66bf1dce8904530992
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247491"
---
# <a name="virtual-network-scenarios-and-resources"></a>Ressources et scénarios relatifs aux réseaux virtuels

Un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) fournit un accès réseau privé et sécurisé à vos ressources Azure et locales. En déployant des groupes de conteneurs dans un réseau virtuel Azure, vos conteneurs peuvent communiquer en toute sécurité avec d’autres ressources dans le réseau virtuel. 

Cet article fournit des informations générales sur les scénarios, limitations et ressources relatifs aux réseaux virtuels. Pour obtenir des exemples de déploiement à l’aide d’Azure CLI, consultez [Déployer des instance de conteneur dans un réseau virtuel Azure](container-instances-vnet.md).

> [!IMPORTANT]
> Le déploiement d’un groupe de conteneurs sur un réseau virtuel est généralement disponible pour les conteneurs Linux, dans la plupart des régions où Azure Container Instances est disponible. Pour plus d’informations, consultez [Régions et disponibilité des ressources](container-instances-region-availability.md). 

## <a name="scenarios"></a>Scénarios

Les groupes de conteneurs déployés dans un réseau virtuel Azure autorisent les scénarios suivants :

* Communication directe entre les groupes de conteneurs dans le même sous-réseau
* Envoyer la sortie d’une charge de travail [basée sur des tâches](container-instances-restart-policy.md) à partir d’instances de conteneur à une base de données dans le réseau virtuel
* Récupérer le contenu des instances de conteneur à partir d’un [point de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) dans le réseau virtuel
* Activer la communication de conteneurs avec les ressources locales par le biais d’une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoute](../expressroute/expressroute-introduction.md)
* Intégrer avec [Pare-feu Azure](../firewall/overview.md) pour identifier le trafic sortant en provenance du conteneur 
* Résoudre les noms par le biais du système Azure DNS interne pour la communication avec les ressources Azure sur le réseau virtuel, telles que les machines virtuelles
* Utiliser des règles de groupe de sécurité réseau pour contrôler l’accès du conteneur aux sous-réseaux ou autres ressources réseau

## <a name="unsupported-networking-scenarios"></a>Scénarios de mise en réseau non pris en charge 

* **Azure Load Balancer** - Le placement d’un équilibreur de charge Azure devant des instances de conteneurs dans un groupe de conteneurs en réseau n’est pas pris en charge.
* **Peering mondial de réseaux virtuels** - Le peering mondial (connexion de réseaux virtuels entre des régions Azure) n’est pas pris en charge
* **Étiquette DNS ou adresse IP publique** - Les groupes de conteneurs déployés sur un réseau virtuel ne prennent actuellement pas en charge l’exposition de conteneurs directement sur Internet avec une adresse IP publique ou un nom de domaine complet.
* **NAT de réseau virtuel** - Les groupes de conteneurs déployés sur un réseau virtuel ne prennent actuellement pas en charge l’utilisation d’une ressource de passerelle NAT pour la connectivité Internet sortante.

## <a name="other-limitations"></a>Autres limitations

* Actuellement, seuls les conteneurs Linux sont pris en charge dans un groupe de conteneurs déployé sur un réseau virtuel.
* Pour que vous puissiez déployer des groupes de conteneurs sur un sous-réseau, celui-ci ne doit pas contenir d’autres types de ressources. Supprimez toutes les ressources d’un sous-réseau avant de déployer des groupes de conteneurs dans celui-ci, ou créez un sous-réseau.
* Vous ne pouvez pas utiliser une [identité managée](container-instances-managed-identity.md) dans un groupe de conteneurs déployé sur un réseau virtuel.
* Vous ne pouvez pas activer de [probe liveness](container-instances-liveness-probe.md) ni de [probe readiness](container-instances-readiness-probe.md) dans un groupe de conteneurs déployé sur un réseau virtuel.
* En raison des ressources réseau supplémentaires impliquées, les déploiements sur un réseau virtuel sont généralement plus lents que les déploiements d’une instance de conteneur standard.
* La connexion sortante vers le port 25 n’est pas prise en charge pour l’instant.
* Si vous connectez votre groupe de conteneurs à un compte Stockage Azure, vous devez ajouter un [point de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) à cette ressource.
* Les [adresses IPv6](../virtual-network/ip-services/ipv6-overview.md) ne sont pas prises en charge pour l’instant. 

## <a name="required-network-resources"></a>Ressources réseau requises

Trois ressources de réseau virtuel Azure sont requises pour déployer des groupes de conteneurs dans un réseau virtuel : le [réseau virtuel](#virtual-network) lui-même, un [sous-réseau délégué](#subnet-delegated) dans le réseau virtuel et un [profil réseau](#network-profile). 

### <a name="virtual-network"></a>Réseau virtuel

Un réseau virtuel définit l’espace d’adressage dans lequel vous créez un ou plusieurs sous-réseaux. Ensuite, vous déployez des ressources Azure (par exemple, des groupes de conteneurs) dans les sous-réseaux de votre réseau virtuel.

### <a name="subnet-delegated"></a>Sous-réseau (délégué)

Les sous-réseaux segmentent le réseau virtuel en espaces d’adressage distincts utilisables par les ressources Azure que vous placez dedans. Vous créez un ou plusieurs sous-réseaux dans un réseau virtuel.

Le sous-réseau que vous utilisez pour les groupes de conteneurs ne peut contenir que des groupes de conteneurs. Lorsque vous déployez d’abord un groupe de conteneurs dans un sous-réseau, Azure délègue ce sous-réseau à Azure Container Instances. Une fois délégué, le sous-réseau ne peut être utilisé que pour les groupes de conteneur. Si vous tentez de déployer des ressources autres que des groupes de conteneurs dans un sous-réseau délégué, l’opération échoue.

### <a name="network-profile"></a>Profil réseau

Un profil réseau est un modèle de configuration de réseau pour les ressources Azure. Il spécifie certaines propriétés réseau de la ressource, par exemple le sous-réseau dans lequel il doit être déployé. Lorsque vous utilisez pour la première fois la commande [az container create][az-container-create] pour déployer un groupe de conteneurs sur un sous-réseau (et donc un réseau virtuel), Azure crée un profil réseau pour vous. Vous pouvez ensuite utiliser ce profil réseau pour les déploiements futurs dans le sous-réseau. 

Pour utiliser un modèle Resource Manager, un fichier YAML ou une méthode de programmation pour déployer un groupe de conteneurs dans un sous-réseau, vous devez fournir l’ID de ressource Resource Manager complet d’un profil réseau. Vous pouvez utiliser un profil précédemment créé à l’aide de [az container create][az-container-create] ou créer un profil à l’aide d’un modèle Resource Manager (voir l’[exemple de modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.containerinstance/aci-vnet) et la [référence](/azure/templates/microsoft.network/networkprofiles)). Pour obtenir l’ID d’un profil précédemment créé, utilisez la commande [az network profile list][az-network-profile-list]. 

Dans le diagramme suivant, plusieurs groupes de conteneurs ont été déployés dans un sous-réseau délégué à Azure Container Instances. Une fois que vous avez déployé un groupe de conteneurs dans un sous-réseau, vous pouvez déployer d’autres groupes de conteneurs dans ce dernier en spécifiant le même profil réseau.

![Groupes de conteneurs dans un réseau virtuel][aci-vnet-01]

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir des exemples de déploiement avec Azure CLI, consultez [Déployer des instance de conteneur dans un réseau virtuel Azure](container-instances-vnet.md).
* Pour déployer un réseau virtuel, un sous-réseau, un profil réseau et un groupe de conteneurs nouveaux à l’aide d’un modèle Resource Manager, consultez [Créer un groupe de conteneurs Azure avec un réseau virtuel](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.containerinstance/aci-vnet
).
* Lorsque vous utilisez le [portail Azure](container-instances-quickstart-portal.md) pour créer une instance de conteneur, vous pouvez également définir les paramètres d'un réseau virtuel nouveau ou existant dans l'onglet **Mise en réseau**.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list