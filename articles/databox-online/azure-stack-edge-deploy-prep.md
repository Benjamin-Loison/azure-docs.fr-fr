---
title: Tutoriel de préparation au déploiement d'Azure Stack Edge Pro via le portail Azure dans votre centre de données
description: Le premier tutoriel consacré au déploiement d'Azure Stack Edge Pro FPGA porte sur la préparation du portail Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 07/23/2021
ms.author: alkohli
ms.custom: subject-rbac-steps
ms.openlocfilehash: d215c1cc8650eb23d1702f7ef0fd782278cfb6d8
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605203"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-fpga"></a>Tutoriel : Préparer le déploiement d'Azure Stack Edge Pro FPGA  

Ce tutoriel est le premier d'une série consacrée au déploiement complet d'Azure Stack Edge Pro FPGA. Il explique comment préparer le portail Azure pour déployer une ressource Azure Stack Edge. 

Vous avez besoin de privilèges d’administrateur pour terminer le processus d’installation et de configuration. La préparation du portail prend moins de 10 minutes.  

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Créer une nouvelle ressource
> * Obtenir la clé d'activation

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="get-started"></a>Bien démarrer

Pour déployer Azure Stack Edge Pro FPGA, consultez les tutoriels suivants dans l'ordre indiqué.

| **#** | **Dans cette étape** | **Utilisez ces documents** |
| --- | --- | --- | 
| 1. |**[Préparer le portail Azure pour Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-prep.md)** |Créez et configurez votre ressource Azure Stack Edge avant d’installer un appareil physique Azure Stack Edge. |
| 2. |**[Installer Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-install.md)**|Déballez, montez en rack et branchez l'appareil physique Azure Stack Edge Pro FPGA.  |
| 3. |**[Connecter, configurer et activer Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-connect-setup-activate.md)** |Connectez-vous à l’interface utilisateur web locale, terminez l’installation de l’appareil et activez-le. L’appareil est prêt à configurer des partages SMB ou NFS.  |
| 4. |**[Transférer des données avec Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-add-shares.md)** |Ajoutez des partages et connectez-vous à ces derniers via SMB ou NFS. |
| 5. |**[Transformer des données avec Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-configure-compute.md)** |Configurez des modules de calcul sur l’appareil pour transformer les données lors de leur déplacement vers Azure. |

Vous pouvez maintenant commencer à configurer le portail Azure.

## <a name="prerequisites"></a>Prérequis

Voici les prérequis relatifs à la configuration de votre ressource Azure Stack Edge, de votre appareil Azure Stack Edge Pro FPGA et du réseau du centre de données.

### <a name="for-the-azure-stack-edge-resource"></a>Pour la ressource Azure Stack Edge

Avant de commencer, assurez-vous que :

* Votre abonnement Microsoft Azure est activé pour une ressource Azure Stack Edge. Vérifiez que vous avez utilisé un abonnement pris en charge tel que [Contrat Entreprise (EA) Microsoft](https://azure.microsoft.com/overview/sales-number/), [Fournisseur de solutions cloud (CSP)](/partner-center/azure-plan-lp) ou [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Les abonnements de type Paiement à l’utilisation ne sont pas pris en charge.

* Rôles RBAC : vous avez les attributions de rôles suivantes dans le contrôle d’accès en fonction du rôle (RBAC) Azure :

  * Pour créer des ressources de stockage Azure Stack Edge, IoT Hub et Azure, un utilisateur doit avoir le rôle contributeur ou propriétaire au niveau de l’étendue du groupe de ressources.

  * Pour attribuer le rôle collaborateur à un utilisateur au niveau de l’étendue du groupe de ressources, vous devez avoir le rôle de propriétaire au niveau de l’étendue de l’abonnement.

  Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).

* Fournisseurs de ressources : les fournisseurs de ressources suivants sont inscrits : 

  * Pour créer une ressource Azure Stack Edge/Data Box Gateway, assurez-vous que le fournisseur `Microsoft.DataBoxEdge` est inscrit.

  * Pour créer une ressource IoT Hub, vérifiez que le fournisseur `Microsoft.Devices` est bien inscrit.

  * Pour créer une ressource stockage Azure, assurez-vous que stockage Azure est inscrit. Le fournisseur de ressources stockage Azure est par défaut un fournisseur de ressources inscrit, mais dans certains cas, l’inscription peut être nécessaire.

  **Pour inscrire un fournisseur de ressources, vous devez avoir reçu le rôle RBAC associé, ci-dessus.**

  Pour plus d’informations sur l’inscription, consultez [Inscrire un fournisseur de ressources](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).

* Vous avez un accès Administrateur ou Utilisateur à l’API Graph Azure Active Directory. Pour plus d’informations, consultez [API Graph Azure Active Directory](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).

* Vous disposez d’un compte de stockage Microsoft Azure doté d’informations d’identification d’accès.

* Vous n’êtes pas bloqué par une attribution Azure Policy configurée par votre administrateur système. Pour plus d’informations sur Azure Policy, consultez [Démarrage rapide : Créer une affectation de stratégie pour identifier les ressources non conformes](../governance/policy/assign-policy-portal.md).


### <a name="for-the-azure-stack-edge-pro-fpga-device"></a>Pour l'appareil Azure Stack Edge Pro FPGA

Avant de déployer un appareil physique, assurez-vous que :

* Vous avez consulté les informations relatives à la sécurité qui étaient incluses dans le colis d’expédition.
* Dans votre centre de données, un emplacement 1U est disponible dans un rack standard 19 pouces pour le montage en rack de l’appareil.
* Vous avez accès à une surface de travail plane, stable et de niveau sur laquelle poser l’appareil en sécurité.
* Le site où vous envisagez de configurer l’appareil dispose d’une alimentation secteur standard provenant d’une source indépendante ou d’une unité de distribution de l’alimentation (PDU) en rack avec un onduleur (UPS).
* Vous avez accès à un appareil physique.

### <a name="for-the-datacenter-network"></a>Pour le réseau du centre de données

Avant de commencer, assurez-vous que :

* Le réseau de votre centre de données est configuré conformément aux exigences de mise en réseau de votre appareil Azure Stack Edge Pro FPGA. Pour plus d'informations, consultez [Configuration système nécessaire pour Azure Stack Edge Pro FPGA](azure-stack-edge-system-requirements.md).

* Pour utiliser votre appareil Azure Stack Edge Pro FPGA dans des conditions normales, vous avez :

  * Une bande passante de chargement de minimum 10 Mbits/s pour s’assurer que l’appareil reste à jour.
  * Une bande passante de chargement et de téléchargement de minimum 20 Mbits/s dédiée pour transférer des fichiers.

## <a name="create-new-resource-for-existing-device"></a>Créer une ressource pour l’appareil existant

Si vous êtes client Azure Stack Edge Pro FPGA et que vous devez remplacer ou réinitialiser votre appareil existant, utilisez la procédure suivante pour créer une ressource.

Si vous êtes un nouveau client, nous vous recommandons d’explorer à l’aide des appareils Azure Stack Edge Pro avec GPU pour vos charges de travail. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-overview.md). Pour plus d’informations sur la commande d’un appareil Azure Stack Edge Pro avec GPU, accédez à [Créer une ressource pour Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

Pour créer une ressource Azure Stack Edge pour un appareil existant, suivez ces étapes sur le portail Azure.

1. Utilisez vos informations d’identification Microsoft Azure pour vous connecter :

    - Au portail Azure à cette URL : [https://portal.azure.com](https://portal.azure.com)
    - Ou bien, au portail Azure Government à cette URL : [https://portal.azure.us](https://portal.azure.us) Pour plus d’informations, accédez à [Se connecter à Azure Government à l’aide du portail](../azure-government/documentation-government-get-started-connect-with-portal.md).

1. Sélectionnez **+ Créer une ressource**. Recherchez et sélectionnez **Azure Stack Edge**. Sélectionnez ensuite **Créer**.

1. Sélectionnez l'abonnement associé à l'appareil Azure Stack Edge Pro FPGA et le pays vers lequel celui-ci doit être expédié dans **Expédier à**.

   ![Sélectionner l’abonnement et le pays de destination pour votre appareil](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-01.png)


1. Dans la liste des types d’appareils qui s’affiche, sélectionnez **Azure Stack Edge Pro - FPGA**. Choisissez ensuite **Sélectionner**. 

   Le type d’appareil **Azure Stack Edge Pro - FPGA** s’affiche uniquement si vous avez un appareil existant. Si vous devez commander un nouvel appareil, accédez à [Créer une ressource pour Azure Stack Edge Pro - GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

   ![Rechercher le service Azure Stack Edge](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-02.png)

1. Sous l’onglet **Général** :

   1. Entrez ou sélectionnez les **détails du projet** suivants.
    
       |Paramètre  |Valeur  |
       |---------|---------|
       |Abonnement    |Cette valeur est automatiquement renseignée en fonction de la sélection antérieure. L’abonnement est lié à votre compte de facturation. |
       |Resource group  |Sélectionnez un groupe existant ou créez-en un.<br>Obtenez plus d’informations sur les [groupes de ressources Azure](../azure-resource-manager/management/overview.md).     |

   1. Entrez ou sélectionnez les **détails de l’instance** suivants.

       |Paramètre  |Valeur  |
       |---------|---------|
       |Nom   | Entrez un nom reconnaissable pour identifier la ressource.<br>Le nom peut comporter entre 2 et 50 caractères, dont des lettres, des chiffres et des traits d’union.<br> Le nom doit commencer et se terminer par une lettre ou un chiffre.        |
       |Région     |Pour obtenir la liste complète des régions où la ressource Azure Stack Edge est disponible, consultez [Disponibilité des produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Si vous utilisez Azure Government, toutes les régions administratives sont disponibles, comme indiqué dans [Régions Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Choisissez l’emplacement le plus proche de la région géographique dans laquelle vous souhaitez déployer votre appareil.|

   1. Sélectionnez **Revoir + créer**.

    ![Détails du projet et de l’instance](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-03.png)

1. Sous l’onglet **Vérifier + créer**, passez en revue les **Conditions d’utilisation**, les **Détails de la tarification** et les détails de votre ressource. Sélectionnez ensuite **Créer**.

    ![Passer en revue les détails de la ressource Azure Stack Edge et les conditions de confidentialité](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-04.png)

1. La création de la ressource prend quelques minutes. Un message vous informe que la ressource a été créée et déployée. Sélectionnez **Accéder à la ressource**.

   ![Accéder à la ressource Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource-01.png)

Une fois la commande passée, Microsoft l’examine et vous communique (par e-mail) les détails de l’expédition.

![Notification relative à l'examen de la commande de l'appareil Azure Stack Edge Pro FPGA](media/azure-stack-edge-deploy-prep/data-box-edge-resource-02.png)


## <a name="get-the-activation-key"></a>Obtenir la clé d'activation

Une fois que la ressource Azure Stack Edge est active et en cours d’exécution, vous devez obtenir la clé d’activation. Cette clé sert à activer votre appareil Azure Stack Edge Pro FPGA et à le connecter à la ressource. Vous pouvez obtenir cette clé maintenant, lorsque vous vous trouvez dans le Portail Azure.

1. Accédez à la ressource que vous avez créée, puis sélectionnez **Vue d’ensemble**. Une notification s’affiche pour vous avertir que votre commande est en cours de traitement.

    ![Sélectionner Vue d’ensemble](media/azure-stack-edge-deploy-prep/data-box-edge-select-device-setup.png)

2. Une fois la commande traitée et l’appareil expédié, le contenu de la page **Vue d’ensemble** est mis à jour. Acceptez le **nom Azure Key Vault** par défaut ou entrez-en un nouveau. Sélectionnez **Générer la clé d’activation**. Sélectionnez l’icône de copie pour copier la clé et l’enregistrer pour une utilisation ultérieure.

    ![Obtenir une clé d’activation](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * La clé d’activation expire 3 jours après sa création.
> * Si la clé expire, générez une nouvelle clé. L’ancienne clé n'est plus valide.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez approfondi vos connaissances sur Azure Stack Edge Pro FPGA et avez notamment appris à :

> [!div class="checklist"]
>
> * Créer une nouvelle ressource
> * Obtenir la clé d'activation

Passez au tutoriel suivant pour apprendre à installer Azure Stack Edge Pro FPGA.

> [!div class="nextstepaction"]
> [Installer Azure Stack Edge Pro FPGA](./azure-stack-edge-deploy-install.md)