---
title: Fichier include
description: Fichier include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: fc2dd8938f627be669519b843a97b87ddf1e3203
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288383"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Pour créer une stratégie d'accès partagé qui accorde des autorisations de **connexion de service** et de **lecture du registre**, et obtenir une chaîne de connexion pour cette stratégie, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Groupes de ressources**. Sélectionnez le groupe de ressources dans lequel se trouve votre hub, puis sélectionnez votre hub dans la liste des ressources.

1. Dans le volet de gauche de votre hub, sélectionnez **Stratégies d’accès partagé**.

1. Dans le menu supérieur au-dessus de la liste des stratégies, sélectionnez **Ajouter**.

1. Sous **Ajouter une stratégie d’accès partagé**, entrez un nom descriptif pour votre stratégie, par exemple *serviceAndRegistryRead*. Sous **Autorisations**, sélectionnez **Lecture du registre** et **Connexion du service**, puis sélectionnez **Ajouter**.

    :::image type="content" source="./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png" alt-text="Capture d’écran qui montre comment ajouter une nouvelle stratégie d’accès partagé" border="true":::

1. Sélectionnez votre nouvelle stratégie dans la liste des stratégies.

1. Sélectionnez l’icône de copie pour la **Chaîne de connexion principale** et enregistrez la valeur.

    :::image type="content" source="./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png" alt-text="Capture d’écran qui montre comment récupérer la chaîne de connexion" border="true":::

Pour plus d’informations sur les autorisations et les stratégies d’accès partagé IoT Hub, consultez [Contrôle d’accès et autorisations](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions).
