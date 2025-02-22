---
title: Diagnostiquer et résoudre les problèmes liés au kit SDK Java v4 Azure Cosmos DB
description: Utilisez des fonctionnalités telles que la journalisation côté client et d’autres outils tiers pour identifier, diagnostiquer et résoudre des problèmes liés au kit SDK Java v4 Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.custom: devx-track-java
ms.openlocfilehash: 54f0796d52d150db272e00c5cb66aa0c68a2e51c
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129090986"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>Résoudre les problèmes liés à l’utilisation du kit SDK Java v4 Azure Cosmos DB avec des comptes d’API SQL
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Kit SDK Java v4](troubleshoot-java-sdk-v4-sql.md)
> * [Kit SDK Java asynchrone v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> Cet article traite uniquement de la résolution des problèmes liés au kit SDK Java asynchrone v4 Azure Cosmos DB. Pour plus d’informations, consultez les [Notes de publication](sql-api-sdk-java-v4.md), le [Référentiel Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) et les [Conseils sur les performances](performance-tips-java-sdk-v4-sql.md) en lien avec le kit SDK Java asynchrone v4 Azure Cosmos DB. Si vous utilisez actuellement une version antérieure à v4, consultez le guide [Migrer vers le kit SDK Java v4 Azure Cosmos DB](migrate-java-v4-sdk.md) pour être accompagné dans la mise à niveau vers v4.
>

Cet article traite des problèmes courants, solutions de contournement, étapes de diagnostic et outils associés à l’utilisation du kit SDK Java v4 Azure Cosmos DB avec des comptes d’API SQL Azure Cosmos DB.
Le kit SDK Java v4 Azure Cosmos DB fournit la représentation logique côté client pour accéder à l’API SQL Azure Cosmos DB. Cet article décrit les outils et les approches qui peuvent vous aider si vous rencontrez des problèmes.

Commencez par cette liste :

* Jetez un coup d’œil à la section [Problèmes courants et solutions de contournement] dans cet article.
* Consultez le kit SDK Java dans le dépôt centralisé Azure Cosmos DB, disponible en [open source sur GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos). Il contient une [section Problèmes](https://github.com/Azure/azure-sdk-for-java/issues) qui est activement tenue à jour. Vérifiez si un problème similaire au vôtre dispose déjà d’une solution de contournement. Une astuce intéressante consiste à filtrer les problèmes par l’étiquette *cosmos:v4-item*.
* Consultez les [conseils sur les performances](performance-tips-java-sdk-v4-sql.md) pour le kit SDK Java v4 Azure Cosmos DB et suivez les pratiques suggérées.
* Lisez le reste de cet article, si vous n’avez pas trouvé de solution. Ensuite, consignez un [problème GitHub](https://github.com/Azure/azure-sdk-for-java/issues). Si vous avez la possibilité d’ajouter des étiquettes à votre problème GitHub, ajoutez l’étiquette *cosmos:v4-item*.

### <a name="retry-logic"></a>Logique de nouvelle tentative <a id="retry-logics"></a>
Le kit de développement logiciel (SDK) Cosmos DB, en cas d’échec d’e/s, retente l’opération en cas de nouvelle tentative dans le SDK. Une nouvelle tentative de mise en place d’un échec est une bonne pratique, mais la gestion et la nouvelle tentative d’écriture échouent. Il est recommandé d’utiliser le dernier kit de développement logiciel (SDK), car la logique de nouvelle tentative est continuellement améliorée.

1. Les échecs d’e/s de lecture et d’interrogation sont retentés par le kit de développement logiciel sans les exposer à l’utilisateur final.
2. Les écritures (créer, upsert, remplacer, supprimer) ne sont pas des idempotent et, par conséquent, le kit de développement logiciel (SDK) ne peut pas toujours retenter les opérations d’écriture ayant échoué. Il est nécessaire que la logique de l’application de l’utilisateur gère l’échec et lance une nouvelle tentative.
3. [Résolution des problèmes de disponibilité du SDK](troubleshoot-sdk-availability.md) explique les nouvelles tentatives pour les comptes de Cosmos DB à plusieurs régions.

### <a name="retry-design"></a>Conception de nouvelle tentative

L’application doit être conçue pour procéder à de nouvelles tentatives en cas d’exceptions, sauf s’il s’agit d’un problème connu où les nouvelles tentatives ne sont pas utiles. Par exemple, l’application doit effectuer une nouvelle tentative sur les délais d’expiration de requête 408, car ces derniers peuvent être temporaires et une nouvelle tentative peut donc aboutir. L’application ne doit pas effectuer de nouvelle tentative sur les exceptions 400 qui signalent généralement un problème avec la requête qui doit d’abord être résolu. Une nouvelle tentative sur l’exception 400 ne corrigera pas le problème et échouera de la même manière. Le tableau ci-dessous indique les échecs connus et ceux pouvant être suivis d’une nouvelle tentative.

## <a name="common-error-status-codes"></a><a id="error-codes"></a> Codes d’état d’erreur courants

| Code d’état | Renouvelable | Description | 
|----------|-------------|-------------|
| 400 | Non | Requête incorrecte (par ex., JSON non valide, en-têtes incorrects, clé de partition incorrecte dans l’en-tête)| 
| 401 | Non | [Non autorisé](troubleshoot-unauthorized.md) | 
| 403 | Non | [Interdit](troubleshoot-forbidden.md) |
| 404 | Non | [Ressource introuvable](troubleshoot-not-found.md) |
| 408 | Oui | [La demande a expiré](troubleshoot-request-timeout-java-sdk-v4-sql.md) |
| 409 | Non | Une défaillance de conflit se produit quand L’ID fourni pour une ressource sur une opération PUT ou POST a été pris par une ressource existante. Pour résoudre ce problème, utilisez un autre ID pour la ressource, car l’ID doit être unique dans tous les documents ayant la même valeur de clé de partition. |
| 410 | Oui | Exceptions disparues (échec temporaire qui ne devrait pas violer le SLA) |
| 412 | Non | Un échec de condition préalable se produit quand l’opération a spécifié un eTag différent de la version disponible sur le serveur. Il s’agit d’une erreur d’accès concurrentiel optimiste. Effectuez une nouvelle tentative de requête après avoir lu la dernière version de la ressource et mis à jour l’eTag sur la requête.
| 413 | Non | [Entité de requête trop grande](../concepts-limits.md#per-item-limits) |
| 429 | Oui | Vous pouvez retenter sans problème après une erreur 429. Peut être évité en suivant le lien pour [Trop de requêtes](troubleshoot-request-rate-too-large.md).|
| 449 | Oui | Erreur temporaire qui se produit uniquement lors des opérations d’écriture, et qui peut faire suite à une nouvelle tentative sans problème. Cela peut pointer un problème de conception où un trop grand nombre d’opérations simultanées tentent de mettre à jour le même objet dans Cosmos DB. |
| 500 | Oui | L’opération a échoué en raison d’une erreur de service inattendue. Contactez le support en renseignant un [problème de support Azure](https://aka.ms/azure-support). |
| 503 | Oui | [Service indisponible](troubleshoot-service-unavailable-java-sdk-v4-sql.md). |

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problèmes courants et solutions de contournement

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problèmes réseau, échec du délai d’expiration de lecture Netty, débit faible, latence élevée

#### <a name="general-suggestions"></a>Recommandations d’ordre général
Pour un résultat optimal :
* Vérifiez que l’application s’exécute dans la même région que votre compte Azure Cosmos DB. 
* Vérifiez l’utilisation du processeur sur l’ordinateur hôte où l’application est en cours d’exécution. Si l’utilisation du processeur est supérieure ou égale à 50 %, exécutez votre application sur un hôte disposant d’une configuration plus élevée. Vous pouvez aussi distribuer la charge sur plusieurs ordinateurs.
    * Si vous exécutez votre application sur Azure Kubernetes Service, vous pouvez [vous servir d’Azure Monitor pour superviser l’utilisation du processeur](../../azure-monitor/containers/container-insights-analyze.md).

#### <a name="connection-throttling"></a>Limitation de la connexion
La limitation de la connexion peut se produire en raison d’une [Limite de connexion sur un ordinateur hôte] ou d’une [insuffisance de ports Azure SNAT (PAT)].

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Limite de connexion sur un ordinateur hôte
Certains systèmes Linux, tels que Red Hat, ont une limite supérieure quant au nombre total de fichiers ouverts. Les sockets dans Linux étant implémentés en tant que fichiers, ce nombre limite aussi le nombre total de connexions.
Exécutez la commande suivante :

```bash
ulimit -a
```
La quantité maximale de fichiers ouverts autorisée, qui sont identifiés comme « nofile », doit être au moins le double votre taille de pool de connexions. Pour plus d’informations, consultez les [conseils sur les performances](performance-tips-java-sdk-v4-sql.md) associés au kit SDK Java v4 Azure Cosmos DB.

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Insuffisance de ports Azure SNAT (PAT)

Si votre application est déployée sur Machine virtuelle Azure sans adresse IP publique, par défaut les [ports Azure SNAT](../../load-balancer/load-balancer-outbound-connections.md#preallocatedports) établissent des connexions avec n’importe quel point de terminaison en dehors de votre machine virtuelle. Le nombre de connexions autorisées de la machine virtuelle au point de terminaison Azure Cosmos DB est limité par la [configuration Azure SNAT](../../load-balancer/load-balancer-outbound-connections.md#preallocatedports).

 Les ports Azure SNAT sont utilisés uniquement quand votre machine virtuelle a une adresse IP privée et qu’un processus à partir de la machine virtuelle tente de se connecter avec une adresse IP publique. Il existe deux solutions de contournement pour éviter la limitation Azure SNAT :

* Ajoutez votre point de terminaison de service Azure Cosmos DB au sous-réseau de votre réseau virtuel Machines virtuelles Azure. Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](../../virtual-network/virtual-network-service-endpoints-overview.md). 

    Quand le point de terminaison de service est activé, les requêtes ne sont plus envoyées d’une adresse IP publique à Azure Cosmos DB. Au lieu de cela, les identités du réseau virtuel et du sous-réseau sont envoyées. Cette modification peut entraîner des problèmes de pare-feu si seules les adresses IP publiques sont autorisées. Si vous utilisez un pare-feu, quand vous activez le point de terminaison de service, ajoutez un sous-réseau au pare-feu à l’aide de [Listes de contrôle d’accès de réseau virtuel](/previous-versions/azure/virtual-network/virtual-networks-acl).
* Assignez une adresse IP publique à votre machine virtuelle Azure.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Impossible d’atteindre le service - pare-feu
``ConnectTimeoutException`` indique que le Kit de développement logiciel (SDK) ne peut pas atteindre le service.
Vous pouvez recevoir une erreur semblable à ce qui suit lorsque vous utilisez le mode direct :
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Si un pare-feu est en cours d’exécution sur l’ordinateur hébergeant l’application, ouvrez la plage de ports 10 000 à 20 000 (utilisée par le mode direct).
Respectez également la [limite de connexion sur un ordinateur hôte](#connection-limit-on-host).

#### <a name="http-proxy"></a>Serveur proxy HTTP

Si vous utilisez un proxy HTTP, vérifiez qu’il peut prendre en charge le nombre de connexions configuré dans SDK `ConnectionPolicy`.
Sinon, vous serez confronté à des problèmes de connexion.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Modèle de codage non valide : blocage du thread d’E/S Netty

Le SDK utilise la bibliothèque d’E/S [Netty](https://netty.io/) pour communiquer avec Azure Cosmos DB. Le kit SDK comprend une API asynchrone et utilise des API d’E/S non bloquantes fournies par Netty. Les tâches d’E/S du SDK sont effectuées sur les threads d’E/S de Netty. Le nombre de threads d’E/S de Netty est configuré pour être identique au nombre de cœurs de processeur de l’ordinateur de l’application. 

Les threads d’E/S de Netty sont destinés à être utilisés uniquement pour les tâches d’E/S non bloquantes de Netty. Le SDK retourne le résultat d’appel de l’API sur l’un des threads d’E/S de Netty au code de l’application. Si l’application effectue une opération de longue durée après avoir reçu les résultats sur le thread Netty, le SDK risque de ne pas avoir suffisamment de threads d’E/S pour effectuer ses tâches d’E/S internes. Ce type de codage d’application peut entraîner un débit faible, une latence élevée et des échecs `io.netty.handler.timeout.ReadTimeoutException`. La solution de contournement consiste à basculer le thread quand vous savez que l’opération prendra de temps.

Par exemple, examinez l’extrait de code suivant qui ajoute des éléments à un conteneur (regardez [ici](create-sql-api-java.md) pour obtenir des conseils sur la configuration de la base de données et du conteneur.) Il se peut que vous effectuiez un travail de longue durée qui prend plus de quelques millisecondes sur le thread Netty. Dans ce cas, vous risquez de passer dans un état où aucun thread d’E/S Netty n’est présent pour traiter les tâches d’E/S. Vous obtenez alors un échec ReadTimeoutException.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>API asynchrone du kit SDK Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootNeedsSchedulerAsync)]

La solution de contournement consiste à changer le thread sur lequel vous effectuez le travail qui prend du temps. Définissez une instance singleton du planificateur pour votre application.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>API asynchrone du kit SDK Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootCustomSchedulerAsync)]

Vous devrez peut-être effectuer des tâches qui prennent du temps, par exemple des E/S bloquantes ou des tâches qui nécessitent beaucoup de ressources de calcul. Dans ce cas, basculez vers un thread de travail fourni par votre `customScheduler` à l’aide de l’API `.publishOn(customScheduler)`.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>API asynchrone du kit SDK Java V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootPublishOnSchedulerAsync)]

En utilisant `publishOn(customScheduler)`, vous libérez le thread d’E/S Netty et basculez vers votre propre thread personnalisé fourni par le planificateur personnalisé. Cette modification résout le problème. Vous n’obtiendrez plus d’échec `io.netty.handler.timeout.ReadTimeoutException`.

### <a name="request-rate-too-large"></a>Taux de requêtes trop élevé
Cet échec est une erreur côté serveur. Il indique que vous avez consommé votre débit provisionné. Réessayez ultérieurement. Si vous obtenez souvent cet échec, augmentez le débit de la collection.

* **Implémentation de l’interruption à intervalles définis par getRetryAfterInMilliseconds**

    Lors du test de performances, vous devez augmenter la charge jusqu’à une limite d’un petit nombre de requêtes. En cas de limitation, l’application client doit s’interrompre pour l’intervalle de nouvelle tentative spécifié sur le serveur. Le respect de l’interruption garantit un temps d’attente minimal entre chaque tentative.

### <a name="error-handling-from-java-sdk-reactive-chain"></a>Gestion des erreurs d’une chaîne réactive du SDK Java

La gestion des erreurs du SDK Java Cosmos DB est importante quand il s’agit de la logique d’application du client. Il existe différents mécanismes de gestion des erreurs fournis par [framework reactor-core](https://projectreactor.io/docs/core/release/reference/#error.handling), qui peuvent être utilisés dans différents scénarios. Nous recommandons aux clients de comprendre ces opérateurs de gestion des erreurs en détail et d’utiliser ceux qui correspondent le mieux à leurs scénarios de logique de nouvelle tentative.

> [!IMPORTANT]
> Nous déconseillons d’utiliser l’opérateur [`onErrorContinue()`](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html#onErrorContinue-java.util.function.BiConsumer-), car celui-ci n’est pas pris en charge dans tous les scénarios.
> Notez que `onErrorContinue()` est un opérateur spécialisé susceptible de rendre peu clair le comportement de votre chaîne réactive. Il fonctionne sur les opérateurs en amont et non pas en aval, il nécessite une prise en charge spécifique de l’opérateur pour fonctionner, et l’étendue peut facilement se propager en amont dans le code de la bibliothèque qui ne l’anticipe pas (ce qui entraîne un comportement inattendu). Pour plus d’informations sur cet opérateur spécial, consultez la [documentation](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html#onErrorContinue-java.util.function.BiConsumer-) de `onErrorContinue()`.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Échec de connexion à l’émulateur Azure Cosmos DB

Le certificat HTTPS de l’émulateur Azure Cosmos DB est auto-signé. Pour que le SDK fonctionne avec l’émulateur, importez le certificat de l’émulateur dans Java TrustStore. Pour plus d’informations, consultez [Exporter les certificats de l’émulateur Azure Cosmos DB](../local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problèmes de conflits de dépendances

Le kit SDK Java Azure Cosmos DB extrait un certain nombre de dépendances ; en règle générale, si l’arborescence des dépendances de votre projet comprend une version antérieure d’un artefact dont le kit SDK Java Azure Cosmos DB dépend, des erreurs inattendues peuvent se produire lors de l’exécution de votre application. Si vous déboguez la raison pour laquelle votre application lève une exception de manière inattendue, il est judicieux de bien vérifier que votre arborescence des dépendances n’extrait pas accidentellement une version plus ancienne d’une ou de plusieurs dépendances du kit SDK Java Azure Cosmos DB.

La solution de contournement pour un tel problème consiste à identifier quelle dépendance de votre projet récupère l’ancienne version, à exclure la dépendance transitive sur cette version antérieure, puis à permettre au kit SDK Java Azure Cosmos DB d’intégrer la version plus récente.

Pour identifier la dépendance de votre projet qui récupère une version antérieure d’un élément dont le kit SDK Java Azure Cosmos DB dépend, exécutez la commande suivante sur le fichier pom.xml de votre projet :
```bash
mvn dependency:tree
```
Pour en savoir plus, consultez le [guide d’arborescence des dépendances maven](https://maven.apache.org/plugins-archives/maven-dependency-plugin-2.10/examples/resolving-conflicts-using-the-dependency-tree.html).

Une fois que vous savez quelle dépendance de votre projet dépend d’une version antérieure, vous pouvez modifier la dépendance sur cette bibliothèque dans votre fichier pom, et exclure la dépendance transitive en suivant l’exemple ci-dessous (qui suppose que *reactor-core* est la dépendance obsolète) :

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Pour en savoir plus, consultez le guide relatif à [l’exclusion de dépendances transitives](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Activer la journalisation de SDK client

Le kit SDK Java v4 Azure Cosmos DB utilise SLF4j comme façade de journalisation prenant en charge la journalisation dans les frameworks de journalisation populaires tels que log4j et logback.

Par exemple, si vous souhaitez utiliser log4j en tant que framework de journalisation, ajoutez les bibliothèques suivantes dans votre chemin de classe Java.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Ajoutez également une configuration log4j.
```
# this is a sample log4j configuration

# Set root logger level to INFO and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.azure.cosmos=INFO
#log4j.category.io.netty=OFF
#log4j.category.io.projectreactor=OFF
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Pour plus d’informations, consultez le [manuel de journalisation sfl4j](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Statistiques réseau du système d’exploitation
Exécutez la commande netstat pour avoir une idée du nombre de connexions à l’état `ESTABLISHED`, `CLOSE_WAIT`, et ainsi de suite.

Sur Linux, vous pouvez exécuter la commande suivante.
```bash
netstat -nap
```

Sur Windows, vous pouvez exécuter la même commande avec d’autres indicateurs d’argument :
```bash
netstat -abn
```

Filtrez le résultat uniquement sur les connexions au point de terminaison Azure Cosmos DB.

Le nombre de connexions au point de terminaison Azure Cosmos DB à l’état `ESTABLISHED` ne doit pas être supérieur à la taille du pool de connexions configuré.

De nombreuses connexions au point de terminaison Azure Cosmos DB peuvent se trouver à l’état `CLOSE_WAIT`. Il peut y en avoir plus de 1000. Un nombre si élevé indique que les connexions sont établies et détruites rapidement. Cette situation peut provoquer des problèmes. Pour plus d’informations, consultez la section [Problèmes courants et solutions de contournement].

 <!--Anchors-->
[Problèmes courants et solutions de contournement]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limite de connexion sur un ordinateur hôte]: #connection-limit-on-host
[Insuffisance de ports Azure SNAT (PAT)]: #snat
