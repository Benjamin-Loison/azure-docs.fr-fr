---
title: Résolution des problèmes liés à l’erreur de l’authentification Azure Digital Twins Explorer
titleSuffix: Azure Digital Twins
description: Découvrez comment diagnostiquer et résoudre les erreurs d’authentification dans Azure Digital Twins Explorer.
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 10/7/2021
ms.openlocfilehash: f03c85f912271dbd5d1cb7904f159d89f4a09ba6
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132623"
---
# <a name="troubleshooting-azure-digital-twins-explorer-authentication-error"></a>Résolution des problèmes liés à l’erreur de l’authentification Azure Digital Twins Explorer

Cet article décrit les causes et les étapes de résolution en cas de réception de l’erreur « Échec de l’authentification » lors de l’exécution de l’exemple [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) sur votre ordinateur local. 

## <a name="symptoms"></a>Symptômes

Lors de la configuration et de l’exécution de l’application Azure Digital Twins Explorer, les tentatives d’authentification auprès de l’application se heurtent au message d’erreur suivant :

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Capture d’écran d’un message d’erreur d’échec d’authentification dans Azure Digital Twins Explorer":::

## <a name="causes"></a>Causes

### <a name="cause-1"></a>Cause no 1

Cette erreur peut se produire si votre compte Azure ne dispose pas des autorisations de contrôle d’accès en fonction du rôle Azure (Azure RBAC) requises définies sur votre instance Azure Digital Twins. Pour accéder aux données de votre instance, vous devez avoir le rôle **Lecteur des données Azure Digital Twins** ou **Propriétaire des données Azure Digital Twins** sur l’instance que vous essayez de lire ou de gérer, respectivement. 

Pour plus d’informations sur la sécurité et les rôles dans Azure Digital Twins, consultez [Sécurité pour les solutions Azure Digital Twins](concepts-security.md).

## <a name="solutions"></a>Solutions

### <a name="solution-1"></a>Solution no 1

Vérifiez que votre utilisateur Azure dispose du rôle **Lecteur des données Azure Digital Twins** sur l’instance Azure Digital Twins si vous tentez simplement de lire ses données ou du rôle **Propriétaire des données Azure Digital Twins** sur l’instance si vous essayez de gérer ses données.

Notez que ce rôle est différent…
* L’ancien nom de ce rôle pendant la préversion, *Propriétaire Azure Digital Twins (préversion)* (le rôle est le même, mais le nom a été modifié)
* du rôle *Propriétaire* sur l’ensemble de l’abonnement Azure. *Propriétaire de données Azure Digital Twins* est un rôle au sein d’Azure Digital Twins qui est étendu à cette instance individuelle d’Azure Digital Twins.
* du rôle *Propriétaire* dans Azure Digital Twins. Il s’agit de deux rôles de gestion distincts pour Azure Digital Twins et *Propriétaire de données Azure Digital Twins* est le rôle qui doit être utilisé pour la gestion.

 Si vous n’avez pas ce rôle, configurez-le pour résoudre le problème.

#### <a name="check-current-setup"></a>Vérifier la configuration actuelle

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Corriger les problèmes 

Si ce rôle ne vous est pas attribué, une personne disposant d’un rôle Propriétaire dans votre **abonnement Azure** doit exécuter la commande suivante pour donner à votre utilisateur Azure le rôle approprié sur l’**instance Azure Digital Twins**. 

Si vous êtes propriétaire de l’abonnement, vous pouvez exécuter cette commande vous-même. Dans le cas contraire, contactez un propriétaire pour qu’il l’exécute à votre place. Le nom du rôle est soit **Propriétaire des données Azure Digital Twins** pour un accès en modification, soit **Lecteur des données Azure Digital Twins** pour un accès en lecture.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

Pour plus d’informations sur cette exigence de rôle et le processus d’attribution, consultez la section [Configurer les autorisations d’accès de votre utilisateur](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) de l’article *Guide pratique : Configurer une instance et l’authentification (CLI ou portail)* .

## <a name="next-steps"></a>Étapes suivantes

Lisez les étapes de configuration relatives à la création et à l’authentification d’une nouvelle instance Azure Digital Twins :
* [Configurer une instance et l’authentification (CLI)](how-to-set-up-instance-cli.md)

En savoir plus sur la sécurité et les autorisations sur Azure Digital Twins :
* [Sécurité pour les solutions Azure Digital Twins](concepts-security.md)
