---
title: Notes de publication concernant la disponibilité générale d’Azure Stack Edge | Microsoft Docs
description: Décrit les problèmes majeurs existants et les résolutions pour Azure Stack Edge exécutant la version en disponibilité générale.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 6f08e9c33a0e5b97e27993519b1d0f471622cb9b
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705501"
---
# <a name="azure-stack-edge-pro-with-gpu-general-availability-ga-release-notes"></a>Notes de publication concernant la disponibilité générale d’Azure Stack Edge Pro avec GPU

[!INCLUDE [applies-to-Pro-GPU-sku](../../includes/azure-stack-edge-applies-to-gpu-sku.md)]

Les notes de publication suivantes identifient des problèmes critiques, résolus ou non, en lien avec la disponibilité générale pour vos appareils Azure Stack Edge Pro avec GPU.

Les notes de publication sont régulièrement mises à jour ; les problèmes critiques nécessitant une solution de contournement sont ajoutés au fur et à mesure de leur découverte. Avant de déployer votre appareil Azure Stack Edge Pro, lisez attentivement les informations contenues dans les notes de publication.

Cet article concerne la version **Azure Stack Edge Pro 2010** qui correspond au numéro de version logicielle **2.1.1377.2170**.

## <a name="whats-new"></a>Nouveautés

Les nouvelles fonctionnalités suivantes sont disponibles dans Azure Stack Edge 2010. 

- **Classes de stockage** : dans cette mise en production, des classes de stockage sont disponibles pour vous permettre d’approvisionner le stockage de façon dynamique. Pour plus d’informations, consultez [Gestion du stockage Kubernetes sur votre appareil Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning). 
- **Tableau de bord Kubernetes avec serveur de métriques** – Dans cette mise en production, un tableau de bord Kubernetes est ajouté avec un module complémentaire serveur de métriques. Vous pouvez utiliser le tableau de bord pour obtenir une vue d’ensemble des applications en cours d’exécution sur votre appareil Azure Stack Edge Pro, afficher l’état des ressources de cluster Kubernetes et voir les erreurs qui se sont produites sur l’appareil. Le serveur de métriques agrège l’utilisation de l’UC et de la mémoire par les ressources Kubernetes sur l’appareil. Pour plus d’informations, consultez [Utiliser le tableau de bord Kubernetes pour superviser votre appareil Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- **Kubernetes avec Azure Arc pour Azure Stack Edge Pro** : à partir de cette version, vous pouvez déployer des charges de travail d’application sur votre appareil Azure Stack Edge Pro via Kubernetes avec Azure Arc. Azure Arc est un outil de gestion hybride qui vous permet de déployer des applications sur vos clusters Kubernetes. Pour plus d’informations, consultez [Déployer des charges de travail via Azure Arc sur votre appareil Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).  

## <a name="known-issues"></a>Problèmes connus 

Le tableau suivant fournit un résumé de problèmes connus de l’appareil Azure Stack Edge Pro.

| Non. | Fonctionnalité | Problème | Solution de contournement/commentaires |
| --- | --- | --- | --- |
|**1.**|Fonctionnalités de préversion |Pour cette mise en production de la disponibilité générale, les fonctionnalités suivantes : Azure Resource Manager local, machines virtuelles, Kubernetes, Kubernetes avec Azure Arc, service multiprocessus (MPS) pour GPU : toutes ces fonctionnalités sont disponibles en préversion pour votre appareil Azure Stack Edge Pro.  |Ces fonctionnalités seront généralement disponibles dans une mise en production ultérieure. |
| **2.** |Azure Stack Edge Pro + Azure SQL | La création d’une base de données SQL nécessite un accès administrateur.   |Suivez les étapes suivantes au lieu des étapes 1-2 dans [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database). <ul><li>Dans l’interface utilisateur locale de votre appareil, activez l’interface de calcul. Sélectionnez **Compute > N° de port > Activer pour le calcul > Appliquer.**</li><li>Téléchargez [l’utilitaire sqlcmd](/sql/tools/sqlcmd-utility) sur la machine de votre client.</li><li>Connectez-vous à l’adresse IP de votre interface de calcul (port activé), en ajoutant un « 1401 » à la fin de l’adresse.</li><li>La commande finale ressemble à ceci : sqlcmd -S {Interface IP},1401 -U SA -P "Strong!Passw0rd".</li>Ensuite, les étapes 3-4 de la documentation actuelle devraient être identiques. </li></ul> |
| **3.** |Actualiser| Les modifications incrémentielles d’objets blob restaurés via la fonctionnalité **Actualiser** ne sont pas prises en charge. |Pour les points de terminaison d’objet blob, des mises à jour partielles d’objets blob après une actualisation peuvent empêcher le chargement des mises à jour dans le cloud. Prenons l’exemple de la séquence d’actions suivante :<ul><li>Créer un objet blob dans le cloud. Ou supprimer un objet blob précédemment chargé à partir de l’appareil.</li><li>Actualiser l’objet blob à partir du cloud dans l’appliance à l’aide de la fonctionnalité d’actualisation.</li><li>Mettre à jour uniquement une partie de l’objet blob à l’aide des API REST du Kit de développement logiciel (SDK) Azure.</li></ul>Ces actions peuvent avoir pour effet que des sections mises à jour de l’objet blob ne sont pas mises à jour dans le cloud. <br>**Solution de contournement** : servez-vous d’outils tels que Robocopy ou d’une copie de fichiers normale via l’Explorateur ou la ligne de commande pour remplacer des objets blob entiers.|
|**4.**|Limitation|Lors d’une limitation, si de nouvelles écritures dans l’appareil ne sont pas autorisées, les écritures effectuées par le client NFS échouent avec l’erreur « Autorisation refusée ».| L’erreur s’affiche comme ci-dessous :<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir : impossible de créer le répertoire ’test’ : Autorisation refusée|
|**5.**|Ingestion du Stockage Blob|Lors de l’utilisation d’AzCopy version 10 pour l’ingestion du stockage d’objets blob, exécutez AzCopy avec l’argument suivant : `Azcopy <other arguments> --cap-mbps 2000`.| Si ces limites ne sont pas fournies pour AzCopy, cela risque d’entraîner l’envoi d’un grand nombre de demandes à l’appareil et d’occasionner des problèmes avec le service.|
|**6.**|Comptes de stockage hiérarchisés|Voici ce qu’il se produit lors de l’utilisation de comptes de stockage hiérarchisés :<ul><li> Seuls les objets blob de blocs sont pris en charge. Les objets blob de pages ne sont pas pris en charge.</li><li>Il n’existe aucune prise en charge de l’API de copie ou de capture instantanée.</li><li> L’ingestion de charge de travail Hadoop via `distcp` n’est pas prise en charge, car elle sollicite abondamment l’opération de copie.</li></ul>||
|**7.**|Connexion de partage NFS|Si plusieurs processus copient vers le même partage et que l’attribut `nolock` n’est pas utilisé, des erreurs peuvent apparaître en cours de copie.|L’attribut `nolock` doit être passé à la commande mount pour copier des fichiers vers le partage NFS. Par exemple : `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**8.**|Cluster Kubernetes|Lorsque vous appliquez une mise à jour sur votre appareil exécutant un cluster Kubernetes, les machines virtuelles Kubernetes redémarrent. Dans ce cas, seuls les pods déployés avec des réplicas spécifiés sont automatiquement restaurés après une mise à jour.  |Si vous avez créé des pods individuels en dehors d’un contrôleur de réplication sans spécifier de jeu de réplicas, ces pods ne seront pas restaurés automatiquement après la mise à jour de l’appareil. Vous devrez les restaurer.<br>Un jeu de réplicas remplace des pods supprimés ou arrêtés pour une raison quelconque, par exemple un échec de nœud ou une mise à niveau de nœud perturbatrice. Pour cette raison, nous vous recommandons d’utiliser un jeu de réplicas, même si votre application ne requiert qu’un seul pod.|
|**9.**|Cluster Kubernetes|Kubernetes sur Azure Stack Edge Pro est pris en charge uniquement avec Helm v3 ou ultérieur. Pour plus d’informations, consultez le [Forum Aux Questions : Suppression de Tiller](https://v3.helm.sh/docs/faq/).|
|**10.**|Kubernetes avec Azure Arc |Pour la disponibilité générale, Kubernetes avec Azure Arc est mis à jour de la version 0.1.18 vers la version 0.2.9. Étant donné que la mise à jour de Kubernetes avec Azure Arc n’est pas prise en charge sur un appareil Azure Stack Edge, vous devez redéployer Kubernetes avec Azure Arc.|Effectuez les étapes suivantes :<ol><li>[Appliquez les mises à jour logicielles de l’appareil et de Kubernetes](azure-stack-edge-gpu-install-update.md).</li><li>Connectez-vous à l’[interface PowerShell de l’appareil](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Supprimez l’agent Azure Arc existant. Entrez : `Remove-HcsKubernetesAzureArcAgent`.</li><li>Déployez [Azure Arc sur une nouvelle ressource](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). N’utilisez pas de ressource Azure Arc existante.</li></ol>|
|**11.**|Kubernetes avec Azure Arc|Les déploiements d’Azure Arc ne sont pas pris en charge si un proxy web est configuré sur votre appareil Azure Stack Edge Pro.||
|**12.**|Kubernetes |Le port 31000 est réservé au tableau de bord Kubernetes. De même, dans la configuration par défaut, les adresses IP 172.28.0.1 et 172.28.0.10 sont réservées respectivement pour le service Kubernetes et le service DNS de base.|N’utilisez pas d’adresses IP réservées.|
|**13.**|Kubernetes |Kubernetes n’autorise pas actuellement les services LoadBalancer multiprotocoles. Par exemple, un service DNS devant écouter à la fois les protocoles TCP et UDP. |Pour contourner cette limitation de Kubernetes avec MetalLB, deux services (un pour TCP, un pour UDP) peuvent être créés sur le même sélecteur de pod. Ces services utilisent la même clé de partage et spec.loadBalancerIP pour partager la même adresse IP. Des adresses IP peuvent également être partagées si vous avez plus de services qu’il n’y d’adresses IP disponibles. <br> Pour plus d’informations, voir [Partage d’adresse IP](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**14.**|Cluster Kubernetes|Des modules existants de la Place de marché Azure IoT Edge peuvent nécessiter des modifications pour s’exécuter sur IoT Edge sur un appareil Azure Stack Edge.|Pour plus d’informations, consultez Modifier des modules Azure IoT Edge de la place de marché pour les exécuter sur des appareils Azure Stack Edge.<!-- insert link-->|
|**15.**|Kubernetes |Les montages de liaison basés sur des fichiers ne sont pas pris en charge avec Azure IoT Edge sur Kubernetes sur un appareil Azure Stack Edge.|IoT Edge utilise une couche de traduction pour convertir des options de `ContainerCreate` en constructions Kubernetes. La création de `Binds` mappe au répertoire `hostpath`. Par conséquent, des montages de liaison basés sur des fichiers ne peuvent pas être liés à des chemins d’accès dans des conteneurs IoT Edge. Si possible, mappez le répertoire parent.|
|**16.**|Kubernetes |Si vous apportez vos propres certificats pour IoT Edge et les ajoutez sur votre appareil Azure Stack Edge une fois le calcul configuré sur celui-ci, les nouveaux certificats ne sont pas sélectionnés.|Pour contourner ce problème, vous devez charger les certificats avant de configurer le calcul sur l’appareil. Si le calcul est déjà configuré, [connectez-vous à l’interface PowerShell de l’appareil et exécutez des commandes IoT Edge](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Redémarrez les pods `iotedged` et `edgehub`.|
|**17.**|Certificats |Dans certains cas, la mise à jour de l’état des certificats dans l’interface utilisateur locale peut prendre plusieurs secondes. |Les scénarios suivants dans l’interface utilisateur locale peuvent être affectés.<ul><li>Colonne **État** dans la page **Certificats**.</li><li>Vignette **Sécurité** dans la page **Démarrer**.</li><li>Vignette **Configuration** dans la page **Vue d’ensemble**.</li></ul>  |
|**17.**|IoT Edge |Les modules déployés via IoT Edge ne peuvent pas utiliser le réseau hôte. | |
|**18.**|Calcul + Kubernetes |Le calcul/Kubernetes ne prend pas en charge le proxy web NTLM. ||
|**19.**|Calcul + proxy Web + mise à jour |Si le calcul est configuré avec un proxy web, la mise à jour du calcul peut échouer. |Nous vous recommandons de désactiver le calcul avant la mise à jour. |

<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->




## <a name="next-steps"></a>Étapes suivantes

- [Préparer le déploiement d’un appareil Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-deploy-prep.md)
