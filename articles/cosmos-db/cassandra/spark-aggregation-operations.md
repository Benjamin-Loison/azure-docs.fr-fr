---
title: Opérations d’agrégation sur des tables de l’API Cassandra Azure Cosmos DB à partir de Spark
description: Cet article traite des opérations d’agrégation de base sur les tables de l’API Cassandra Azure Cosmos DB à partir de Spark
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 23ec7563c192ae8457e3d888dc4b0982b361afe2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525245"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Opérations d’agrégation sur des tables de l’API Cassandra Azure Cosmos DB à partir de Spark 
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Cet article décrit les opérations d’agrégation de base sur les tables de l’API Cassandra Azure Cosmos DB à partir de Spark. 

> [!NOTE]
> Le filtrage côté serveur et l’agrégation côté serveur ne sont pas pris en charge dans l’API Cassandra Azure Cosmos DB.

## <a name="cassandra-api-configuration"></a>Configuration de l’API Cassandra

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector
import org.apache.spark.sql.functions._

//if using Spark 2.x, CosmosDB library for multiple retry
//import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
// if using Spark 2.x
// spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
//spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10") // Spark 2.x
spark.conf.set("spark.cassandra.connection.remoteConnectionsPerExecutor", "10") // Spark 3.x
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

> [!NOTE]
> Si vous utilisez Spark 3.0 ou une version ultérieure, il n’est pas nécessaire d’installer l’assistance Cosmos DB ni la fabrique de connexion. Vous devez également utiliser `remoteConnectionsPerExecutor` à la place de `connections_per_executor_max` pour le connecteur Spark 3 (voir ci-dessus). Vous verrez que les propriétés liées à la connexion sont définies dans le notebook ci-dessus. À l’aide de la syntaxe ci-dessous, les propriétés de connexion peuvent être définies de cette manière, sans qu’il soit nécessaire de les définir au niveau du cluster (initialisation du contexte Spark). Toutefois, quand vous utilisez des opérations qui requièrent un contexte Spark (par exemple `CassandraConnector(sc)` pour certaines des opérations indiquées ci-dessous), les propriétés de connexion doivent être définies au niveau du cluster.

## <a name="sample-data-generator"></a>Générateur d’exemple de données

```scala
// Generate a simple dataset containing five values
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="count-operation"></a>Opération count


### <a name="rdd-api"></a>API pour le jeu de donnée distribué résilient

```scala
sc.cassandraTable("books_ks", "books").count
```

**Output:**
```bash
count: Long = 5
```

### <a name="dataframe-api"></a>API Dataframe

L’opération de comptage sur les dataframes n’est pas prise en charge.  En guise de solution de contournement, l’exemple ci-dessous montre comment exécuter une opération de comptage sur un dataframe après avoir conservé celui-ci en mémoire.

Choisissez une [option de stockage]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose) parmi les options disponibles suivantes, pour éviter de rencontrer des problèmes de « mémoire insuffisante » :

* MEMORY_ONLY : option de stockage par défaut. Stocke le jeu de donnée distribué résilient en tant qu’objets Java désérialisés dans la machine virtuelle Java. Si le jeu de donnée distribué résilient ne tient pas dans la mémoire, certaines partitions ne sont pas mises en cache et elles sont recalculées à la volée chaque fois qu’elles sont nécessaires.

* MEMORY_AND_DISK : stocke le jeu de donnée distribué résilient en tant qu’objets Java désérialisés dans la machine virtuelle Java. Si le jeu de donnée distribué résilient ne tient pas dans la mémoire, stocke les partitions qui ne tiennent pas sur le disque et, chaque fois que nécessaire, les lire à partir de l’emplacement où elles sont stockées.

* MEMORY_ONLY_SER (Java/Scala) : stocke le jeu de donnée distribué résilient en tant qu’objets Java sérialisés, à raison d’un tableau d’octets par partition. Cette option est moins gourmande en espace par rapport à des objets désérialisés, surtout quand vous utilisez un sérialiseur rapide, mais les opérations de lecture sont plus gourmandes en UC.

* MEMORY_AND_DISK_SER (Java/Scala) : cette option de stockage ressemble à MEMORY_ONLY_SER, la seule différence étant qu’elle déverse les partitions qui ne tiennent pas dans la mémoire du disque au lieu de les recalculer quand elles sont nécessaires.

* DISK_ONLY : stocke les partitions du jeu de donnée distribué résilient sur le disque uniquement.

* MEMORY_ONLY_2, MEMORY_AND_DISK_2... : identique aux niveaux ci-dessus, mais réplique chaque partition sur deux nœuds de cluster.

* OFF_HEAP (option expérimentale) : similaire à MEMORY_ONLY_SER, mais elle stocke les données dans la mémoire hors tas, et nécessite que celle-ci soit activée à l’avance. 

```scala
//Workaround
import org.apache.spark.storage.StorageLevel

//Read from source
val readBooksDF = spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()

//Explain plan
readBooksDF.explain

//Materialize the dataframe
readBooksDF.persist(StorageLevel.MEMORY_ONLY)

//Subsequent execution against this DF hits the cache 
readBooksDF.count

//Persist as temporary view
readBooksDF.createOrReplaceTempView("books_vw")
```

### <a name="sql"></a>SQL

```sql
%sql
select * from books_vw;
select count(*) from books_vw where book_pub_year > 1900;
select count(book_id) from books_vw;
select book_author, count(*) as count from books_vw group by book_author;
select count(*) from books_vw;
```

## <a name="average-operation"></a>Opération mean

### <a name="rdd-api"></a>API pour le jeu de donnée distribué résilient

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**Output:**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>API Dataframe

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**Output:**
```
+------------------+
|   avg(book_price)|
+------------------+
|16.016000175476073|
+------------------+
```

### <a name="sql"></a>SQL

```sql
select avg(book_price) from books_vw;
```
**Output:**
```
16.016000175476073
```

## <a name="min-operation"></a>Opération min

### <a name="rdd-api"></a>API pour le jeu de donnée distribué résilient

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**Output:**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>API Dataframe

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**Output:**
```
+---------------+
|min(book_price)|
+---------------+
|          11.33|
+---------------+
```

### <a name="sql"></a>SQL

```sql
%sql
select avg(book_price) from books_vw;
```

**Output:**
```
11.33
```

## <a name="max-operation"></a>Opération max

### <a name="rdd-api"></a>API pour le jeu de donnée distribué résilient

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>API Dataframe

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**Output:**
```
+---------------+
|max(book_price)|
+---------------+
|          22.45|
+---------------+
```

### <a name="sql"></a>SQL

```sql
%sql
select max(book_price) from books_vw;
```
**Output:**
```
22.45
```

## <a name="sum-operation"></a>Opération sum

### <a name="rdd-api"></a>API pour le jeu de donnée distribué résilient

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**Output:**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>API Dataframe

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**Output:**
```
+-----------------+
|  sum(book_price)|
+-----------------+
|80.08000087738037|
+-----------------+
```

### <a name="sql"></a>SQL

```sql
select sum(book_price) from books_vw;
```

**Output:**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>Opération top ou comparable

### <a name="rdd-api"></a>API pour le jeu de donnée distribué résilient

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**Output:**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>API Dataframe

```scala
import org.apache.spark.sql.functions._

val readBooksDF = spark.read.format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_price")
  .orderBy(desc("book_price"))
  .limit(3)

//Explain plan
readBooksDF.explain

//Top
readBooksDF.show
```

**Output:**
```
== Physical Plan ==
TakeOrderedAndProject(limit=3, orderBy=[book_price#1840 DESC NULLS LAST], output=[book_name#1839,book_price#1840])
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@29cd5f58 [book_name#1839,book_price#1840] PushedFilters: [], ReadSchema: struct<book_name:string,book_price:float>
+--------------------+----------+
|           book_name|book_price|
+--------------------+----------+
|      A sign of four|     22.45|
|The adventures of...|     19.83|
|The memoirs of Sh...|     14.22|
+--------------------+----------+

import org.apache.spark.sql.functions._
readBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_name: string, book_price: float]
```

### <a name="sql"></a>SQL

```sql
select book_name,book_price from books_vw order by book_price desc limit 3;
```

## <a name="next-steps"></a>Étapes suivantes

Pour effectuer des opérations de copie de table, consultez :

* [Opérations de copie de table](spark-table-copy-operations.md)
