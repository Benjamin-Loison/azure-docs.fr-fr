---
title: Résolution des problèmes Azure Spring Cloud dans un réseau virtuel
description: Guide de dépannage pour un réseau virtuel Azure Spring Cloud.
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 95b288181180e2409bcb18f4f8233af568bd311c
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563424"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>Résolution des problèmes Azure Spring Cloud dans des réseaux virtuels

Ce document vous aidera à résoudre divers problèmes qui peuvent survenir lors de l’utilisation d’Azure Spring Cloud dans des réseaux virtuels.

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>J’ai rencontré un problème lors de la création d’une instance du service Azure Spring Cloud

Pour créer une instance Azure Spring Cloud, vous devez disposer des autorisations suffisantes pour déployer l’instance sur le réseau virtuel.  L’instance du service Spring Cloud doit elle-même [accorder l’autorisation du service Azure Spring Cloud au réseau virtuel](./how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network).

Si vous utilisez le Portail Azure pour configurer l’instance de service Azure Spring Cloud, le Portail Azure validera les autorisations.

Pour configurer l’instance de service Azure Spring Cloud à l’aide de l’[interface de ligne de commande Azure](/cli/azure/get-started-with-azure-cli), vérifiez les points suivants :

- L’abonnement est actif.
- L’emplacement est pris en charge par Azure Spring Cloud.
- Le groupe de ressources pour l’instance est créé.
- Le nom de la ressource est conforme à la règle de nommage. Il ne doit contenir que des lettres minuscules, des chiffres et des traits d’union. Le premier caractère doit être une lettre. Le dernier caractère doit être une lettre ou un chiffre. La valeur doit comprendre entre 2 et 32 caractères.

Pour configurer l’instance de service Azure Spring Cloud à l’aide du modèle Resource Manager, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](../azure-resource-manager/templates/syntax.md).

### <a name="common-creation-issues"></a>Problèmes de création courants

| Message d’erreur | Procédure de résolution |
|------|------|
| Les ressources créées par Azure Spring Cloud n’ont pas été autorisées par la stratégie. | Les ressources réseau sont créées lors du déploiement d’Azure Spring Cloud dans votre propre réseau virtuel. Vérifiez si vous disposez d’[Azure Policy](../governance/policy/overview.md) définie pour bloquer ces créations. Les ressources qui n’ont pas pu être créées sont indiquées dans le message d’erreur. |
| Le trafic requis n’est pas inclus dans la liste d’autorisation. | Pour faire en sorte que le trafic requis soit inclus dans la liste d’autorisation, consultez [Responsabilités du client pour l’exécution d’Azure Spring Cloud dans un réseau virtuel](./vnet-customer-responsibilities.md). |

## <a name="my-application-cant-be-registered"></a>Impossible d’inscrire mon application

Ce problème se produit si votre réseau virtuel est configuré avec des paramètres DNS personnalisés. Dans ce cas, la zone DNS privée utilisée par Azure Spring Cloud est inefficace. Ajoutez l’adresse IP Azure DNS 168.63.129.16 en tant que serveur DNS en amont dans le serveur DNS personnalisé.

## <a name="other-issues"></a>Autres problèmes

[Résoudre les problèmes courants liés à Azure Spring Cloud](./troubleshoot.md)
