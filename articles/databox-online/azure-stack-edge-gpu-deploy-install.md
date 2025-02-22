---
title: 'Tutoriel d’installation : Déballer, monter en rack et brancher l’appareil physique Azure Stack Edge Pro avec GPU | Microsoft Docs'
description: Le deuxième tutoriel sur l’installation d’Azure Stack Edge Pro avec GPU fournit des explications sur le déballage, le montage en rack et le câblage de l’appareil physique.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 07/07/2021
ms.author: alkohli
ms.openlocfilehash: 488b6d791afe477bb1aecacd0ecb15d54dcb43da
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114221315"
---
# <a name="tutorial-install-azure-stack-edge-pro-with-gpu"></a>Tutoriel : Installer Azure Stack Edge Pro avec GPU

Ce tutoriel explique comment installer un appareil physique Azure Stack Edge Pro avec un GPU. La procédure d’installation comprend le déballage, le montage en rack et le câblage de l’appareil. 

L’installation peut prendre environ deux heures.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Déballez l’appareil
> * Monter l’appareil en rack
> * Câblage de l’appareil

## <a name="prerequisites"></a>Prérequis

Voici les prérequis pour l’installation d’un appareil physique :

### <a name="for-the-azure-stack-edge-resource"></a>Pour la ressource Azure Stack Edge

Avant de commencer, assurez-vous que :

* Vous avez effectué toutes les étapes décrites dans [Préparer le déploiement d’Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-deploy-prep.md).
    * Vous avez créé une ressource Azure Stack Edge pour déployer votre appareil.
    * Vous avez créé la clé d’activation pour activer votre appareil auprès de la ressource Azure Stack Edge.

 
### <a name="for-the-azure-stack-edge-pro-physical-device"></a>Pour l’appareil physique Azure Stack Edge Pro

Avant de déployer un appareil :

- Vérifiez que l’appareil est posé en toute sécurité sur une surface de travail plate, stable et de niveau.
- Vérifiez que le lieu prévu pour l’installation dispose des éléments suivants :
    - Alimentation secteur standard provenant d’une source indépendante.

        OU
    - Une unité de distribution d’énergie (PDU) en rack équipée d’un onduleur (UPS).
    - Un emplacement de 1U disponible sur le rack sur lequel vous avez l’intention de monter l’appareil.

### <a name="for-the-network-in-the-datacenter"></a>Pour le réseau dans le centre de données

Avant de commencer :

- Passez en revue la configuration réseau nécessaire pour le déploiement d’Azure Stack Edge Pro, et configurez le réseau du centre de données conformément aux exigences. Pour plus d’informations, consultez [Configuration réseau pour Azure Stack Edge Pro](azure-stack-edge-system-requirements.md#networking-port-requirements).

- Assurez-vous que la bande passante Internet minimale est de 20 Mbits/s pour un fonctionnement optimal de l’appareil.


## <a name="unpack-the-device"></a>Déballez l’appareil

Cet appareil est livré dans un seul carton. Procédez comme suit pour déballer votre appareil. 

1. Placez le carton sur une surface plane et droite.
2. Vérifiez que le carton et le polystyrène ne comportent pas de trace d’impacts, de coupures, d’infiltrations d’eau ou tout autre type de dégâts. Si le carton ou le reste de l’emballage vous semble trop endommagé, ne l’ouvrez pas. Contactez le Support Microsoft pour savoir si l’appareil est en état de marche.
3. Déballez le carton. Une fois le carton déballé, vous devez disposer des éléments suivants :
    - 1 appareil Azure Stack Edge Pro dans son boîtier
    - 2 câbles d’alimentation
    - 1 assemblage du kit de rails
    - 1 livret relatif à la sécurité, l’environnement et les réglementations

Si vous n’avez pas reçu tous les éléments énumérés ici, contactez le [support technique Microsoft](azure-stack-edge-contact-microsoft-support.md). L’étape suivante consiste à monter votre appareil en rack.


## <a name="rack-the-device"></a>Montage de l’appareil en rack

L’appareil doit être installé sur un rack standard de 19 pouces. Effectuez les étapes suivantes pour monter votre appareil sur un rack standard de 19 pouces.

> [!IMPORTANT]
> Les appareils Azure Stack Edge Pro doivent être montés en rack pour fonctionner correctement.


### <a name="prerequisites"></a>Prérequis

- Avant de commencer, lisez les instructions du livret relatif à la sécurité, l’environnement et les réglementations. Ce livret a été fourni avec l’appareil.
- Commencez par installer les rails dans l’espace alloué le plus proche de la partie inférieure du châssis.
- Pour la configuration de montage des rails avec outils :
    -  Vous devez fournir huit vis : n° 10-32, n° 12-24, n° M5 ou n° M6. Le diamètre des têtes de vis doit être inférieur à 10 mm (0,4 po).
    -  Vous avez besoin d’un tournevis à tête plate.

### <a name="identify-the-rail-kit-contents"></a>Identifier le contenu du kit de rails

Identifiez les éléments d’installation de l’assemblage du kit de rails :
- Deux assemblages de rails coulissants A7 Dell ReadyRails II
- Deux sangles à boucle et crochet

    ![Identifier le contenu du kit de rails](./media/azure-stack-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Installer et retirer les rails sans outil (racks à trous ronds ou carrés)

> [!TIP]
> Cette option ne propose aucun outil, car l’installation et le retrait des rails dans les trous carrés ou ronds non filetés des racks ne nécessitent pas d’outils.

1. Placez les extrémités des rails gauche et droit portant la mention **FRONT** vers l’intérieur, et orientez chaque extrémité pour qu’elle vienne s’encastrer dans les trous de la partie avant des brides verticales du rack.
2. Alignez chaque extrémité pour qu’elle s’encastre dans les trous inférieurs et supérieurs des unités souhaitées.
3. Engagez l’arrière du rail jusqu’à son enclenchement complet dans la collerette verticale du rack et jusqu’à l’enclenchement du loquet. Répétez ces étapes pour positionner et enclencher l’extrémité avant sur la bride verticale du rack.
4. Pour retirer les rails, tirez sur le bouton de dégagement du loquet situé au milieu de l’extrémité et dégagez chaque rail.

    ![Installer et retirer les rails sans outil](./media/azure-stack-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Installer et retirer les rails avec outils (racks à trous filetés)

> [!TIP]
> Cette option est équipée d’outils,car l’installation et le retrait des rails dans les trous ronds filetés des racks nécessitent un outil (_un tournevis à tête plate_).

1. Retirez les broches des supports de fixation avant et arrière à l’aide d’un tournevis plat.
2. Tirez et faites pivoter les sous-ensembles de loquets des rails afin de les retirer des supports de fixation.
3. Fixez les rails droit et gauche aux brides verticales avant du rail à l’aide de deux paires de vis.
4. Faites glisser les montants arrière gauche et droit vers l’avant jusqu’à ce qu’ils soient placés contre les brides verticales arrière du rail et fixez-les avec deux paires de vis.

    ![Installer et retirer les rails avec outils 2](./media/azure-stack-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Installer le système dans un rack

1. Dégagez les rails coulissants internes en les faisant glisser jusqu’à ce qu’ils s’enclenchent.
2. Repérez les picots du rail arrière de chaque côté du système et abaissez-les pour les emboîter dans les fentes en J des rails coulissants. Faites pivoter le système vers le bas jusqu’à ce que tous les picots du rail se trouvent dans les fentes en J.
3. Repoussez le système vers l’intérieur jusqu’à ce que les leviers de verrouillage s’enclenchent.
4. Appuyez sur les deux boutons de dégagement des deux rails et poussez le système dans le rack.

    ![Installer le système dans un rack](./media/azure-stack-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Retirer le système installé dans le rack

1. Localisez les leviers de verrouillage situés sur les côtés des rails internes.
2. Déverrouillez chaque levier en le faisant pivoter jusqu’à ce qu’il soit en position de dégagement.
3. Tenez le système par ses côtés et tirez-le vers l’avant jusqu’à aligner les picots du rail avec les fentes en J. Soulevez le système, dégagez-le et posez-le sur une surface plane.

    ![Retirer le système installé dans le rack](./media/azure-stack-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Ouvrir et fermer les rabats

> [!NOTE]
> Pour les systèmes non équipés de rabats, sécurisez le système à l’aide de vis, comme décrit à l’étape 3 de cette procédure.

1. À partir de l’avant, identifiez le rabat de chaque côté du système.
2. Les rabats s’enclenchent automatiquement quand vous poussez le système dans le rack et s’ouvrent automatiquement quand vous tirez dessus.
3. Si vous devez fixer le système pour l’expédier ou si celui-ci repose sur une surface instable, repérez les vis fixes situées sous chaque rabat et vissez-les à l’aide d’un tournevis cruciforme n° 2.

    ![Ouvrir et fermer les rabats](./media/azure-stack-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Câblage de l’appareil

Acheminez les câbles, puis branchez votre appareil. Les procédures suivantes expliquent comment effectuer le câblage d’alimentation et de réseau de votre appareil Azure Stack Edge Pro.

Pour brancher votre appareil, vous avez besoin des éléments suivants :

- Votre appareil physique Azure Stack Edge Pro, déballé et monté en rack.
- 2 câbles d’alimentation.
- Au moins un câble réseau 1-GbE RJ-45 à brancher à l’interface de gestion. Il existe deux interfaces de réseau 1-GbE, une interface de gestion, et une interface de données, sur l’appareil.
- Un câble en cuivre 25/10 GbE SFP+ pour chaque interface réseau de données à configurer. Au moins une interface de réseau de données, au choix PORT 2, PORT 3, PORT 4, PORT 5 ou PORT 6, doit être connectée à Internet (avec connectivité à Azure).  
- Accès à deux unités de distribution d’énergie (recommandé).
- Commutateur réseau 1-GbE au minimum pour connecter une interface réseau 1 GbE à Internet pour les données. L’interface utilisateur web locale n’est pas accessible si le commutateur connecté n’offre pas un débit d’au moins 1 GbE. Si vous utilisez l’interface 25/10 GbE pour les données, vous aurez besoin d’un commutateur 25 GbE ou 10 GbE.

> [!NOTE]
> - Si vous ne connectez qu’une seule interface de réseau, nous vous recommandons d’utiliser une interface réseau 25/10-GbE telle que PORT 3, PORT 4, PORT 5 ou PORT 6 pour envoyer des données à Azure. 
> - Pour de meilleures performances et pour gérer d’importants volumes de données, pensez à connecter tous les ports de données.
> - L’appareil Azure Stack Edge Pro doit être connecté au réseau du centre de données pour pouvoir ingérer les informations provenant des serveurs des sources de données.

Sur votre appareil Azure Stack Edge Pro :

- Le panneau avant comprend des lecteurs de disque et un bouton d’alimentation.

    - 10 emplacements de disque se trouvent à l’avant de votre appareil.
    - L’emplacement 0 comporte un lecteur SATA de 240 Go qui est utilisé comme un disque de système d’exploitation. L’emplacement 1 est vide, et les emplacements 2 à 6 sont des disques SSD NVMe utilisés comme des disques de données. Les emplacements 7 à 9 sont également vides.
- Le fond de panier de l’appareil comprend des unités d’alimentation (PSU) redondantes à l’arrière.
- Le fond de panier comprend six interfaces réseau :

    - Deux interfaces de 1 Gbit/s
    - Quatre interfaces de 25 Gbits/s qui peuvent également faire office d’interfaces de 10 Gbits/s.
    - Un contrôleur de gestion de la carte de base (BMC).

- Le fond de panier comprend deux cartes réseau correspondant aux six ports :

    - **Carte Microsoft `Qlogic` Cavium 25G NDC personnalisée** - Ports 1 à 4.
    - **Carte réseau Mellanox Dual Port 25G ConnectX - 4 canaux** : Ports 5 et 6.

Pour obtenir la liste complète des câbles, commutateurs et transmetteurs pour ces cartes réseau, consultez :

- [Matrice d’interopérabilité de la carte `Qlogic` Cavium 25G NDC](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
- Câbles et modules 25 GbE et 10 GbE dans [Produits compatibles avec la carte réseau Mellanox Dual Port 25G ConnectX-4](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).  

 
Effectuez les étapes suivantes pour brancher l’alimentation et le réseau de votre appareil.

1. Identifiez les différents ports sur le fond de panier de votre appareil. Vous pouvez avoir reçu un des appareils suivants de l’usine en fonction du nombre de GPU de votre appareil.

    - Appareil avec deux emplacements d’interconnexion de composants périphériques (PCI) et un GPU

        ![Fond de panier d’un appareil câblé](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    - Appareil avec trois emplacements PCI et un GPU

        ![Fond de panier d’un appareil câblé 2](./media/azure-stack-edge-gpu-deploy-install/ase-three-pci-slots-one-gpu.png)

    - Appareil avec trois emplacements PCI et deux GPU

        ![Fond de panier d’un appareil câblé 3](./media/azure-stack-edge-gpu-deploy-install/ase-three-pci-slots-two-gpu.png)

2. Localisez les emplacements de disque et le bouton d’alimentation à l’avant de l’appareil.

    ![Plan avant d’un appareil](./media/azure-stack-edge-gpu-deploy-install/ase-gpu-device-front-plane-labeled.png)

3. Branchez les câbles d’alimentation à chacune des unités d’alimentation dans le boîtier. Pour assurer une haute disponibilité, installez et connectez les deux unités d’alimentation à des sources d’alimentation différentes.
4. Fixez les câbles d’alimentation aux unités de distribution d’alimentation (PDU) du rack. Assurez-vous que les deux unités d’alimentation utilisent des sources d’alimentation distinctes.
5. Appuyez sur le bouton d’alimentation pour allumer l’appareil.
6. Connectez l’interface réseau 1-GbE PORT 1 à l’ordinateur utilisé pour configurer l’appareil physique. Le PORT 1 sert d’interface de gestion.
    
    > [!NOTE]
    > Si vous connectez l’ordinateur directement à votre appareil (sans passer par un commutateur), utilisez un câble croisé ou une carte Ethernet USB.

7. Connectez un ou plusieurs interfaces PORT 2, PORT 3, PORT 4, PORT 5 ou PORT 6 au réseau du centre de données/à Internet.

    - Si vous connectez le PORT 2, utilisez le câble réseau 1-GbE RJ-45.
    - Pour les interfaces réseau de 10/25-GbE, utilisez des câbles en cuivre SFP+ ou la fibre. Si vous utilisez la fibre, utilisez un adaptateur optique vers SFP.
    - Pour les déploiements de Network Function Manager, assurez-vous que les ports 5 et 6 sont connectés. Pour plus d’informations, consultez [Tutoriel : Déployer des fonctions réseau sur Azure Stack Edge (préversion)](../network-function-manager/deploy-functions.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez approfondi vos connaissances sur Azure Stack Edge Pro et avez notamment appris à :

> [!div class="checklist"]
> * Déballez l’appareil
> * Montage de l’appareil en rack
> * Câblage de l’appareil

Passez au tutoriel suivant pour savoir comment vous connecter à votre appareil.

> [!div class="nextstepaction"]
> [Connecter Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-connect.md)
