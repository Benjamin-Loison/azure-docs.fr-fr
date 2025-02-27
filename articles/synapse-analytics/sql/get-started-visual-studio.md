---
title: Se connecter à Synapse SQL avec Visual Studio et SSDT, et l’interroger
description: Utilisez Visual Studio pour interroger le pool SQL dédié à l’aide d’Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 7d534794737f93cd71ee9820f72e6c5ead08ca2b
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532949"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Se connecter à Synapse SQL avec Visual Studio et SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Utilisez Visual Studio pour interroger le pool SQL dédié à l’aide d’Azure Synapse Analytics. Cette méthode utilise l’extension SQL Server Data Tools (SSDT) dans Visual Studio 2019. 

> [!NOTE]
> Le pool SQL serverless n’est pas pris en charge par SSDT.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce tutoriel, vous devez disposer des composants suivants :

- Un espace de travail Synapse existant, si vous devez en créer un, consultez [création d’un espace de travail Synapse](../get-started-create-workspace.md)
- Un pool SQL dédié. Si vous n’en possédez aucun, consultez [Créer un pool SQL dédié](../get-started-analyze-sql-pool.md#create-a-dedicated-sql-pool).
- SSDT pour Visual Studio. Si vous avez Visual Studio, vous disposez probablement déjà de ce composant. Pour obtenir des instructions et des options d’installation, consultez [Installation de Visual Studio et/ou SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?context=/azure/synapse-analytics/context/context).
- Le nom complet du serveur SQL. Pour rechercher le nom du serveur, consultez [Se connecter à un pool SQL dédié](connect-overview.md).

## <a name="1-connect-to-a-dedicated-sql-pool"></a>1. Se connecter à un pool SQL dédié
1. Ouvrez Visual Studio 2019.
2. Ouvrez l'Explorateur d’objets SQL Server en sélectionnant **Afficher** > **Explorateur d’objets SQL Server**.
   
    ![Explorateur d’objets SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Cliquez sur l’icône **Ajouter SQL Server** .
   
    ![Ajouter SQL Server](./media/get-started-visual-studio/add-server.png)
4. Renseignez les champs dans la fenêtre Se connecter au serveur.
   
    ![Se connecter au serveur](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Nom du serveur** : Saisissez le **nom du serveur** précédemment identifié.
   * **Authentification** : Sélectionnez **Authentification SQL Server** ou **Authentification intégrée Active Directory** :
   * **Nom d’utilisateur** et **Mot de passe** : Entrez votre nom d’utilisateur et votre mot de passe si l’authentification SQL Server a été sélectionnée plus haut.
   * Cliquez sur **Connecter**.
5. Pour voir plus d’informations, développez votre serveur SQL Azure. Vous pouvez afficher les bases de données associées au serveur. Développez AdventureWorksDW pour voir les tables de votre exemple de base de données.
   
    ![Explorer AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Exécuter un exemple de requête
Maintenant qu’une connexion à votre base de données a été établie, vous allez écrire une requête.

1. Cliquez avec le bouton droit sur votre base de données dans l’Explorateur d’objets SQL Server.
2. Sélectionnez **Nouvelle requête**. Une nouvelle fenêtre de requête s’ouvre.
   
    ![Nouvelle requête](./media/get-started-visual-studio/new-query2.png)
3. Copiez la requête T-SQL suivante dans la fenêtre de requête :
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Exécutez la requête en cliquant sur la flèche verte ou utilisez le raccourci : `CTRL`+`SHIFT`+`E`.
   
    ![Exécuter une requête](./media/get-started-visual-studio/run-query.png)
5. Passez en revue les résultats de la requête. Dans cet exemple, la table FactInternetSales a 60 398 lignes.
   
    ![Résultats de la requête](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous pouvez vous connecter et exécuter des requêtes, essayez de [visualiser les données avec Power BI](get-started-power-bi-professional.md).
Pour configurer votre environnement pour l’authentification Azure Active Directory, consultez [S’authentifier auprès d’un pool SQL dédié](sql-authentication.md?tabs=provisioned).
 