---
title: Optimisation pour le développement et le test dans Azure Cosmos DB
description: Cet article explique comment Azure Cosmos DB propose plusieurs options pour développer et tester gratuitement le service.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 60f586e3f2ead0bde743f75d7810bf2d43c737af
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123028599"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optimiser les coûts de développement et de test dans Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Cet article décrit les différentes options qui permettent d’utiliser gratuitement Azure Cosmos DB pour le développement et le test, ainsi que des techniques pour optimiser les coûts des comptes de développement ou de test.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Émulateur Azure Cosmos DB (version téléchargeable localement)

L’[émulateur Azure Cosmos DB](local-emulator.md) est une version téléchargeable en local qui simule le service cloud Azure Cosmos DB. Vous pouvez écrire et tester du code qui utilise les API Azure Cosmos DB même si vous ne disposez d’aucune connexion réseau, et sans frais. L’émulateur Azure Cosmos DB fournit un environnement local qui reproduit de façon fidèle le service Azure Cosmos DB à des fins de développement. Vous pouvez développer et tester votre application localement, sans créer d’abonnement Azure et sans frais. Lorsque vous êtes prêt à déployer votre application dans le cloud, mettez à jour la chaîne de connexion pour vous connecter au point de terminaison Azure Cosmos DB dans le cloud. Aucune autre modification n’est nécessaire. Vous pouvez aussi [configurer un pipeline CI/CD avec la tâche de génération d’émulateur Azure Cosmos DB](tutorial-setup-ci-cd.md) dans Azure DevOps pour effectuer des tests. Pour vous lancer, consultez l’article [Émulateur Azure Cosmos DB](local-emulator.md).

## <a name="azure-cosmos-db-free-tier"></a>Niveau gratuit d’Azure Cosmos DB

Le niveau gratuit d’Azure Cosmos DB facilite le démarrage, le développement et le test de vos applications, voire l’exécution de petites charges de travail de production, gratuitement. Quand le niveau gratuit est activé sur un compte, vous obtenez gratuitement 1 000 RU/s et 25 Go de stockage dans le compte.

Le niveau gratuit est valable pendant toute la durée de vie du compte et comprend tous les [avantages et fonctionnalités](introduction.md#key-benefits) d’un compte Azure Cosmos DB standard, y compris un stockage et un débit (RU/s) illimités, des contrats SLA, une haute disponibilité, une distribution mondiale clé en main dans toutes les régions Azure et plus encore. Vous pouvez créer un compte de niveau gratuit en utilisant le portail Azure, CLI, PowerShell ou un modèle Resource Manager. Pour plus d’informations, consultez l’article [Créer un compte de niveau gratuit](free-tier.md) et la [page des tarifs](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="try-azure-cosmos-db-for-free"></a>Essayez gratuitement Azure Cosmos DB

[Essayez gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) est une expérience gratuite qui vous permet de tester Azure Cosmos DB dans le cloud sans créer de compte Azure ni utiliser votre carte de crédit. Les comptes d’essai Azure Cosmos DB sont disponibles pour une durée limitée de 30 jours, actuellement. Vous pouvez les renouveler à tout moment. Les comptes d’essai Azure Cosmos DB permettent de facilement évaluer Azure Cosmos DB, générer et tester une application, ou utiliser les démarrages rapides ou les tutoriels. Vous pouvez également créer une démonstration, effectuer des tests unitaires ou même créer un compte multirégion et y exécuter une application sans frais. Dans un compte d’essai Azure Cosmos DB, vous pouvez avoir une base de données de débit partagé avec un maximum de 25 conteneurs et 20 000 RU/s de débit, ou un conteneur avec un maximum de 5 000 RU/s. Pour vous lancer, consultez la page [Essayer Azure Cosmos DB gratuitement](https://azure.microsoft.com/try/cosmosdb/).

## <a name="azure-free-account"></a>Compte gratuit Azure

Azure Cosmos DB est inclus dans le [compte gratuit Azure](https://azure.microsoft.com/free), qui offre gratuitement des crédits et des ressources Azure pendant un certain temps. Spécialement pour Azure Cosmos DB, ce compte gratuit offre 25 Go de stockage et 400 RU/s de débit approvisionné pour toute l’année. Cette expérience permet aux développeurs de facilement tester les fonctionnalités d’Azure Cosmos DB ou de les intégrer gratuitement à d’autres services Azure. Avec un compte gratuit Azure, vous bénéficiez d’un crédit de 200 $ à dépenser dans les 30 premiers jours. Rien ne vous sera facturé, même si vous commencez à utiliser les services, tant que vous n’avez pas procédé à une mise à niveau. Pour commencer, consultez la page [Compte gratuit Azure](https://azure.microsoft.com/free).

## <a name="azure-cosmos-db-serverless"></a>Azure Cosmos DB serverless

[Azure Cosmos DB serverless](serverless.md) vous permet d’utiliser votre compte Azure Cosmos sur la base de la consommation. Vous êtes facturé uniquement pour les unités de requête que consomment vos opérations de base de données, et le stockage que consomment vos données. L’utilisation du service Azure Cosmos DB en mode serverless n’implique pas de frais minimum. Étant donné qu’il élimine le concept de capacité approvisionnée, il constitue une solution idéale pour les activités de développement ou de test, en particulier lorsque votre base de données est inactive la plupart du temps.

## <a name="use-shared-throughput-databases"></a>Utiliser des bases de données de débit partagé

Dans une [base de données de débit partagé](set-throughput.md#set-throughput-on-a-database), tous les conteneurs à l’intérieur de la base de données partagent le débit provisionné (RU/s) de la base de données. Par exemple, si vous provisionnez une base de données avec 400 RU/s et que vous avez quatre conteneurs, les quatre conteneurs partagent les 400 RU/s. Dans un environnement de développement ou de test, où chaque conteneur peut être consulté moins souvent et nécessite donc moins que les 400 RU/s minimales, le fait de placer des conteneurs dans une base de données de débit partagé peut permettre d’optimiser les coûts.

Par exemple, supposons que votre compte de développement ou de test a quatre conteneurs. Si vous créez quatre conteneurs avec un débit dédié (400 RU/s minimum), le nombre total de RU/s est 1 600 RU/s. En revanche, si vous créez une base de données de débit partagé (400 RU/s minimum) et que vous y placez vos conteneurs, le nombre total de RU/s est seulement 400 RU/s. En général, les bases de données de débit partagé conviennent parfaitement aux scénarios où vous n’avez pas besoin d’un débit garanti sur un conteneur individuel.  Découvrez plus d’informations sur les [bases de données de débit partagé.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez commencer à utiliser l’émulateur ou les comptes Azure Cosmos DB gratuits avec les articles suivants :

* En savoir plus sur les [factures Azure Cosmos DB](understand-your-bill.md)
* En savoir plus sur le [Azure Cosmos DB serverless](serverless.md).
* En savoir plus sur l’[optimisation du coût du débit](optimize-cost-throughput.md)
* En savoir plus sur l’[optimisation du coût de stockage](optimize-cost-storage.md)
* En savoir plus sur l’[optimisation du coût des lectures et écritures](optimize-cost-reads-writes.md)
* En savoir plus sur l’[optimisation du coût des requêtes](./optimize-cost-reads-writes.md)
* En savoir plus sur l’[optimisation du coût des comptes Azure Cosmos sur plusieurs régions](optimize-cost-regions.md)
* Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existantes pour la planification de la capacité.
    * Si vous ne connaissez que le nombre de vCore et de serveurs présents dans votre cluster de bases de données existant, lisez l’article sur l’[estimation des unités de requête à l’aide de vCore ou de processeurs virtuels](convert-vcore-to-request-unit.md) 
    * Si vous connaissez les taux de requêtes types de votre charge de travail de base de données actuelle, lisez la section concernant l’[estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-with-capacity-planner.md)