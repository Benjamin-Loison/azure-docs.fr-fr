---
title: Réplicas secondaires Hyperscale
description: Cet article décrit les différents types de réplicas secondaires disponibles dans le niveau de service Hyperscale.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.topic: overview
author: yorek
ms.author: damauri
ms.reviewer: ''
ms.date: 9/24/2021
ms.openlocfilehash: b87c8dcfab561a010019a71f59307bdc8943761d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439516"
---
# <a name="hyperscale-secondary-replicas"></a>Réplicas secondaires Hyperscale
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Comme décrit dans [Architecture des fonctions distribuées](service-tier-hyperscale.md), Azure SQL Database Hyperscale a deux types différents de nœuds de calcul, également appelés « réplicas » :

- Principal : sert les opérations de lecture et d’écriture
- Secondaire : fournit l’[échelle horizontale en lecture](read-scale-out.md), la [haute disponibilité](high-availability-sla.md) et la [géoréplication](active-geo-replication-overview.md)

Les réplicas secondaires sont toujours en lecture seule et peuvent être de trois types différents :

- Réplica de haute disponibilité
- Réplica nommé (en [préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/))
- Géoréplica (en [préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/))

Chaque type a une architecture, un ensemble de fonctionnalités, un objectif et des coûts différents. En fonction des fonctionnalités dont vous avez besoin, vous pouvez utiliser un seul type comme l’ensemble des trois types.

## <a name="high-availability-replica"></a>Réplica de haute disponibilité

Un réplica de haute disponibilité (HA) utilise les mêmes serveurs de pages que le réplica principal. Ainsi, aucune copie de données n’est nécessaire pour ajouter un réplica de haute disponibilité. Les réplicas HA sont principalement utilisés pour augmenter la disponibilité de la base de données. Ils agissent comme ressources de secours à des fins de basculement. Si le réplica principal devient indisponible, le basculement vers l’un des réplicas de haute disponibilité existants est automatique et rapide. La chaîne de connexion n’a pas besoin d’être changée ; pendant le basculement, les applications peuvent connaître un temps d’arrêt minimal en raison de la suppression des connexions actives. Comme d’ordinaire dans ce scénario, une logique de nouvelle tentative appropriée est recommandée. Plusieurs pilotes fournissent déjà un certain degré de logique de nouvelle tentative automatique. Si vous utilisez .NET, la [dernière bibliothèque Microsoft.Data.SqlClient](https://devblogs.microsoft.com/azure-sql/configurable-retry-logic-for-microsoft-data-sqlclient/) offre une prise en charge native complète de la logique de nouvelle tentative automatique configurable.

Les réplicas de haute disponibilité utilisent les mêmes noms de serveur et de base de données que le réplica principal. Leur objectif de niveau de service est aussi toujours le même que pour le réplica principal. Les réplicas de haute disponibilité ne peuvent pas être vus ou gérés en tant que ressources autonomes à partir du portail ou de n’importe quelle API. 

Il peut y avoir de zéro à quatre réplicas de haute disponibilité. Leur nombre peut être changé lors de la création d’une base de données ou après la création de celle-ci, par le biais des outils et du point de terminaison de gestion communs (par exemple : PowerShell, AZ CLI, portail, API REST). La création ou la suppression de réplicas de haute disponibilité n’affecte pas les connexions actives sur le réplica principal.

### <a name="connecting-to-an-ha-replica"></a>Connexion à un réplica de haute disponibilité

Dans les bases de données Hyperscale, l’argument `ApplicationIntent` de la chaîne de connexion utilisée par le client détermine si la connexion est routée vers le réplica principal en écriture-lecture ou vers un réplica de haute disponibilité en lecture seule. Si l’option `ApplicationIntent` est définie sur `ReadOnly` et que la base de données ne dispose pas de réplica secondaire, la connexion est routée vers le réplica principal et le comportement adopté par défaut est `ReadWrite`.

```csharp
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Tous les réplicas à haute disponibilité sont identiques dans leur capacité de ressources. Si plusieurs réplicas à haute disponibilité sont présents, la charge de travail d’intention de lecture est distribuée arbitrairement sur tous les réplicas à haute disponibilité disponibles. Quand il existe plusieurs réplicas de haute disponibilité, gardez à l’esprit que chacun d’eux peut avoir une latence de données différente en ce qui concerne les modifications de données effectuées sur le réplica principal. Chaque réplica de haute disponibilité utilise les mêmes données que le réplica principal sur le même ensemble de serveurs de pages. Cependant, les caches de données locaux sur chaque réplica de haute disponibilité reflètent les modifications apportées sur le réplica principal par le biais du service de journal des transactions, qui transfère les enregistrements de journal du réplica principal vers les réplicas de haute disponibilité. En conséquence, en fonction de la charge de travail traitée par un réplica de haute disponibilité, l’application d’enregistrements de journal peut se produire à des vitesses différentes, si bien que différents réplicas peuvent avoir une latence de données différente par rapport au réplica principal.

## <a name="named-replica-in-preview"></a>Réplica nommé (en préversion)

Un réplica nommé, tout comme un réplica de haute disponibilité, utilise les mêmes serveurs de pages que le réplica principal. Comme pour les réplicas de haute disponibilité, aucune copie de données n’est nécessaire pour ajouter un réplica nommé. 

La différence par rapport aux réplicas de haute disponibilité réside dans le fait que les réplicas nommés : 

- Apparaissent en tant que bases de données Azure SQL standard (en lecture seule) dans le portail et dans les appels d’API (AZ CLI, PowerShell, T-SQL) ;
- Peuvent avoir un nom de base de données différent du réplica principal et éventuellement être situés sur un autre serveur logique (à condition qu’il se trouve dans la même région que le réplica principal) ;
- Ont leur propre objectif de niveau de service qui peut être défini et changé indépendamment du réplica principal ;
- Prennent en charge un maximum de 30 réplicas nommés (pour chaque réplica principal) ;
- Prennent en charge des processus d’authentification différents pour chaque réplica nommé en créant des connexions différentes sur les serveurs logiques hébergeant les réplicas nommés.

Par conséquent, les réplicas nommés offrent plusieurs avantages par rapport aux réplicas à haute disponibilité, pour ce qui est des charges de travail en lecture seule :

- Les utilisateurs connectés à un réplica nommé ne subissent aucune déconnexion si le réplica principal fait l’objet d’un scale-up ou d’un scale-down. De même, les utilisateurs connectés au réplica principal ne sont pas affectés par le scale-up ou le scale-down des réplicas nommés.
-   Les charges de travail exécutées sur un réplica, principal ou nommé, ne sont pas affectées par les requêtes longues exécutées sur d’autres réplicas.

L’objectif principal des réplicas nommés est d’autoriser un scénario d’[échelle horizontale en lecture](read-scale-out.md) OLTP massive et d’améliorer les charges de travail de traitement transactionnel et analytique hybride (HTAP, Hybrid Transactional and Analytical Processing). Des exemples de création de solutions de ce type sont disponibles ici :

- [Exemple de scale-out OLTP](https://github.com/Azure-Samples/azure-sql-db-named-replica-oltp-scaleout)
- [Exemple de scale-out HTAP](https://github.com/Azure-Samples/azure-sql-db-named-replica-htap)

Les réplicas nommés offrent flexibilité et élasticité pour répondre à de nombreux autres cas d’usage, en plus des principaux scénarios listés ci-dessus :
- [Isolation d’accès](hyperscale-named-replica-security-configure.md) : Vous pouvez accorder l’accès à un réplica nommé spécifique, mais pas au réplica principal ou d’autres réplicas nommés.
- Objectif de niveau de service dépendant de la charge de travail : un réplica nommé pouvant avoir son propre objectif de niveau de service, il est possible d’utiliser différents réplicas nommés pour différentes charges de travail et cas d’usage. Par exemple, un réplica nommé peut être utilisé pour traiter des demandes Power BI, tandis qu’un autre peut être utilisé pour fournir des données à Apache Spark dans le cadre de tâches de science des données. Chacun d’eux peut avoir un objectif de niveau de service indépendant et être mis à l’échelle indépendamment.
- Routage dépendant de la charge de travail : dans la limite de 30 réplicas nommés, il est possible d’utiliser des réplicas nommés dans des groupes afin qu’une application puisse être isolée d’une autre. Par exemple, un groupe de quatre réplicas nommés peut être utilisé pour traiter les demandes provenant des applications mobiles, tandis qu’un autre groupe de deux réplicas nommés peut être utilisé pour traiter les demandes provenant d’une application web. Cette approche permet un réglage affiné des performances et des coûts pour chaque groupe.

L’exemple suivant crée un réplica nommé `WideWorldImporters_NR` pour la base de données `WideWorldImporters`. Le réplica principal utilise l’objectif de niveau de service HS_Gen5_4, tandis que le réplica nommé utilise HS_Gen5_2. Les deux utilisent le même serveur logique `MyServer`. Si vous préférez utiliser l’API REST directement, cette option est également possible : [Bases de données - Créer une base de données en tant que réplica secondaire nommé](/rest/api/sql/2020-11-01-preview/databases/createorupdate#creates-a-database-as-named-replica-secondary).

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
ALTER DATABASE [WideWorldImporters]
ADD SECONDARY ON SERVER [MyServer] 
WITH (SERVICE_OBJECTIVE = 'HS_Gen5_2', SECONDARY_TYPE = Named, DATABASE_NAME = [WideWorldImporters_NR]);
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
New-AzSqlDatabaseSecondary -ResourceGroupName "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters" -PartnerResourceGroupName "MyResourceGroup" -PartnerServerName "MyServer" -PartnerDatabaseName "WideWorldImporters_NR" -SecondaryType Named -SecondaryServiceObjectiveName HS_Gen5_2
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az sql db replica create -g MyResourceGroup -n WideWorldImporters -s MyServer --secondary-type named --partner-database WideWorldImporters_NR --partner-server MyServer --service-objective HS_Gen5_2
```

---

Comme aucun déplacement de données n’est impliqué, dans la plupart des cas, un réplica nommé est créé en environ une minute. Une fois le réplica nommé disponible, il est visible à partir du portail ou de tout outil en ligne de commande tel que AZ CLI ou PowerShell. Un réplica nommé est utilisable comme base de données en lecture seule ordinaire. 

> [!NOTE]
> Pour obtenir les réponses aux questions fréquemment posées sur les réplicas nommés Hyperscale, consultez [Questions fréquentes (FAQ) sur les réplicas nommés Hyperscale Azure SQL Database](service-tier-hyperscale-named-replicas-faq.yml).

### <a name="connecting-to-a-named-replica"></a>Connexion à un réplica nommé

Pour vous connecter à un réplica nommé, vous devez utiliser la chaîne de connexion pour ce réplica nommé, en référençant les noms du serveur et de la base de données. Il n’est pas nécessaire de spécifier l’option « ApplicationIntent=ReadOnly », car les réplicas nommés sont toujours en lecture seule.

Comme pour les réplicas de haute disponibilité, même si les réplicas principaux, de haute disponibilité et nommés partagent les mêmes données sur le même ensemble de serveurs de pages, les caches de données sur chaque réplica nommé demeurent synchronisés avec le réplica principal par le biais du service de journal des transactions, qui transfère les enregistrements de journal du réplica principal vers les réplicas nommés. En conséquence, en fonction de la charge de travail traitée par un réplica de nommé, l’application des enregistrements de journal peut se produire à des vitesses différentes, si bien que différents réplicas peuvent avoir une latence de données différente par rapport au réplica principal.

### <a name="modifying-a-named-replica"></a>Modification d’un réplica nommé

Vous pouvez définir l’objectif de niveau de service d’un réplica nommé quand vous le créez, par le biais de la commande `ALTER DATABASE` ou d’autres méthodes prises en charge (AZ CLI, PowerShell, API REST). Si vous avez besoin de changer l’objectif de niveau de service après la création du réplica nommé, vous pouvez le faire avec la commande `ALTER DATABASE ... MODIFY` sur le réplica nommé lui-même. Par exemple, si `WideWorldImporters_NR` est le réplica nommé de la base de données `WideWorldImporters`, vous pouvez effectuer l’opération comme indiqué ci-dessous. 

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
ALTER DATABASE [WideWorldImporters_NR] MODIFY (SERVICE_OBJECTIVE = 'HS_Gen5_4')
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Set-AzSqlDatabase -ResourceGroup "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters_NR" -RequestedServiceObjectiveName "HS_Gen5_4"
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az sql db update -g MyResourceGroup -s MyServer -n WideWorldImporters_NR --service-objective HS_Gen5_4
```

---

### <a name="removing-a-named-replica"></a>Suppression d’un réplica nommé

Vous pouvez supprimer un réplica nommé comme une base de données normale. Assurez-vous que vous êtes connecté à la base de données `master` du serveur hébergeant le réplica nommé à supprimer, puis utilisez la commande suivante :

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
DROP DATABASE [WideWorldImporters_NR];
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Remove-AzSqlDatabase -ResourceGroupName "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters_NR"
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az sql db delete -g MyResourceGroup -s MyServer -n WideWorldImporters_NR
```
---

> [!IMPORTANT]
> Les réplicas nommés sont automatiquement supprimés lors de la suppression du réplica principal à partir duquel ils ont été créés.

### <a name="known-issues"></a>Problèmes connus

#### <a name="partially-incorrect-data-returned-from-sysdatabases"></a>Données partiellement incorrectes retournées par sys.databases
Pendant la préversion publique, les valeurs de ligne retournées à partir de `sys.databases`, pour les réplicas nommés, dans des colonnes autres que `name` et `database_id`, peuvent être incohérentes et incorrectes. Par exemple, la colonne `compatibility_level` d’un réplica nommé peut être signalée comme ayant pour valeur 140 même si la base de données primaire à partir de laquelle le réplica nommé a été créé est définie sur 150. Dans la mesure du possible, une solution de contournement consiste à obtenir les mêmes données en utilisant la fonction `DATABASEPROPERTYEX()`, qui retourne les données correctes.

## <a name="geo-replica-in-preview"></a>Géoréplica (préversion)

Avec la [géoréplication active](active-geo-replication-overview.md), vous pouvez créer un réplica secondaire accessible en lecture de la base de données Hyperscale primaire dans la même région ou dans une autre région Azure. Les géoréplicas doivent être créés sur un serveur logique différent. Le nom de la base de données d’un géoréplica correspond toujours au nom de la base de données du réplica primaire.

Lors de la création d’un géoréplica, toutes les données sont copiées du réplica primaire vers un autre ensemble de serveurs de pages. Un géoréplica ne partage pas les serveurs de pages avec le réplica primaire, même s’ils se trouvent dans la même région. Cette architecture fournit la redondance nécessaire pour les basculements géographiques.

Les géo-réplicas sont utilisés pour maintenir une copie transactionnelle de la base de données via une réplication asynchrone. Si un géo-réplica se trouve dans une autre région Azure, il peut être utilisé pour la récupération d’urgence en cas de sinistre ou de panne dans la région primaire. Les géoréplicas peuvent également être utilisés dans le cadre de scénarios de scale-out en lecture.

Dans Hyperscale, un basculement géographique doit être initié manuellement. Après le basculement, le nouveau réplica principal a un point de terminaison de connexion différent, référençant le nom du serveur logique qui héberge le nouveau réplica principal. Pour plus d’informations, consultez [Géoréplication active](active-geo-replication-overview.md).

La géoréplication pour les bases de données Hyperscale est en préversion, avec les limitations suivantes :
- Un seul géoréplica peut être créé (dans la même région ou dans une autre région).
- Les groupes de basculement ne sont pas pris en charge. 
- Le basculement planifié n’est pas pris en charge.
- La limite de restauration dans le temps du géoréplica n’est pas prise en charge.
- La création d’une copie de la base de données du géoréplica n’est pas prise en charge. 
- La définition d’un réplica secondaire d’un réplica secondaire (également appelée « chaînage de géoréplicas ») n’est pas prise en charge. 

## <a name="next-steps"></a>Étapes suivantes

- [Niveau de service Hyperscale](service-tier-hyperscale.md)
- [Géo-réplication active](active-geo-replication-overview.md)
- [Configurer la sécurité pour autoriser l’accès isolé aux réplicas nommés Hyperscale Azure SQL Database](hyperscale-named-replica-security-configure.md)
- [Questions fréquentes (FAQ) sur les réplicas nommés Hyperscale Azure SQL Database](service-tier-hyperscale-named-replicas-faq.yml)
