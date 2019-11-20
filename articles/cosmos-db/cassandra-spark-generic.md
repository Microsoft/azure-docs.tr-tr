---
title: Spark 'tan Azure Cosmos DB Cassandra API çalışma
description: Bu makale, Spark Cassandra API Tümleştirmesi Cosmos DB ana sayfasıdır.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: cb34ea44c069f067d13a6480531a94a1a515f380
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241236"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Spark 'dan Azure Cosmos DB Cassandra API bağlanma

Bu makale, Spark 'tan Azure Cosmos DB Cassandra API tümleştirmede bir dizi makale arasından biridir. Makalelerdeki bağlantı, veri tanımlama dili (DDL) işlemleri, temel veri Işleme dili (DML) işlemleri ve gelişmiş Azure Cosmos DB Cassandra API Spark 'dan tümleştirme ele alınmaktadır. 

## <a name="prerequisites"></a>Önkoşullar
* [Azure Cosmos DB bir Cassandra API hesabı sağlayın.](create-cassandra-dotnet.md#create-a-database-account)

* Spark ortamınızı sağlama [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Diğerleri].

## <a name="dependencies-for-connectivity"></a>Bağlantı bağımlılıkları
* **Cassandra için Spark Bağlayıcısı:** Spark Connector, Azure Cosmos DB Cassandra API bağlanmak için kullanılır.  Spark ortamınızın Spark ve Scala sürümleriyle uyumlu olan [Maven Central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) 'da bulunan bağlayıcının sürümünü belirleyip kullanın.

* **Cassandra API için Azure Cosmos DB Yardımcısı kitaplığı:** Spark bağlayıcısının yanı sıra, Azure Cosmos DB [Azure-Cosmos-Cassandra-Spark-Helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) adlı başka bir kitaplığa ihtiyacınız vardır. Bu kitaplık özel bağlantı fabrikası ve yeniden deneme ilkesi sınıflarını içerir.

  Azure Cosmos DB 'de yeniden deneme ilkesi, HTTP durum kodu 429 ("Istek hızı büyük") özel durumlarını işleyecek şekilde yapılandırılmıştır. Azure Cosmos DB Cassandra API, bu özel durumları Cassandra Native protokolünde aşırı yüklenmiş hatalara dönüştürür ve geri dönme ile yeniden deneyebilirsiniz. Azure Cosmos DB sağlanan üretilen iş modelini kullandığından, giriş/çıkış oranları artdığında istek hızı sınırlandırma özel durumları oluşur. Yeniden deneme ilkesi, Spark işlerinizi, Kapsayıcınız için ayrılan üretilen işi büyük bir şekilde aşan veri artışlarına karşı korur.

  > [!NOTE] 
  > Yeniden deneme ilkesi, Spark işlerinizin yalnızca kopan ani artışlarla korunmasını sağlayabilir. İş yükünüzü çalıştırmak için yeterli RUs yapılandırmadıysanız, yeniden deneme ilkesi geçerli değildir ve yeniden deneme ilkesi sınıfı özel durumu yeniden oluşturur.

* **Azure Cosmos DB hesabı bağlantı ayrıntıları:** Azure Cassandra API hesabınızın adı, hesap uç noktası ve anahtarınız.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Spark Bağlayıcısı verimlilik yapılandırma parametreleri

Aşağıdaki tabloda bağlayıcı tarafından sunulan Cassandra API özgü işleme yapılandırma parametreleri listelenmektedir Azure Cosmos DB. Tüm yapılandırma parametrelerinin ayrıntılı bir listesi için bkz. Spark Cassandra Connector GitHub deposunun [yapılandırma başvurusu](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) sayfası.

| **Özellik adı** | **Varsayılan değer** | **Açıklama** |
|---------|---------|---------|
| spark. Cassandra. Output. Batch. size. Rows |  1\. |Tek toplu iş başına satır sayısı. Bu parametreyi 1 olarak ayarlayın. Bu parametre ağır iş yükleri için daha yüksek performans elde etmek için kullanılır. |
| spark. Cassandra. Connection. connections_per_executor_max  | Yok. | Yürütücü başına düğüm başına en fazla bağlantı sayısı. 10 * n, n düğümlü Cassandra kümesinde düğüm başına 10 bağlantıyla eşdeğerdir. Bu nedenle, 5 düğümlü Cassandra kümesi için her düğüm için düğüm başına 5 bağlantı gerekiyorsa, bu yapılandırmayı 25 olarak ayarlamanız gerekir. Bu değeri, paralellik derecesini veya Spark işlerinizin yapılandırıldığı yürütme sayısını temel alarak değiştirin.   |
| spark. Cassandra. Output. eşzamanlı. yazmaları  |  100 | Yürütücü başına gerçekleşebileceğini paralel yazma sayısını tanımlar. "Batch. size. Rows" değerini 1 olarak ayarladığınızdan, bu değeri uygun şekilde ölçeklendirdiğinizden emin olun. Bu değeri, paralellik derecesini veya iş yükünüz için elde etmek istediğiniz aktarım hızını temel alarak değiştirin. |
| spark. Cassandra. eşzamanlı. okumalar |  512 | Yürütücü başına gerçekleşebileceğini paralel okumaların sayısını tanımlar. Bu değeri, paralellik derecesini veya iş yükünüz için elde etmek istediğiniz aktarım hızını temel alarak değiştirin  |
| spark.cassandra.output.throughput_mb_per_sec  | Yok. | Yürütücü başına toplam yazma aktarım hızını tanımlar. Bu parametre, Spark iş aktarım hızı için üst sınır olarak kullanılabilir ve Cosmos kapsayıcılarınızın sağlanan iş hızına dayandırın.   |
| spark. Cassandra. Input. reads_per_sec| Yok.   | Yürütücü başına toplam okuma aktarım hızını tanımlar. Bu parametre, Spark iş aktarım hızı için üst sınır olarak kullanılabilir ve Cosmos kapsayıcılarınızın sağlanan iş hızına dayandırın.  |
| spark. Cassandra. Output. Batch. Grouping. buffer. size |  1000  | Cassandra API gönderilmeden önce bellekte depolanabilecek tek Spark görevi başına toplu iş sayısını tanımlar |
| spark. Cassandra. Connection. keep_alive_ms | 60000 | Kullanılmayan bağlantıların kullanılabilir olduğu süreyi tanımlar. | 

Spark işleriniz için bekleyen iş yüküne ve Cosmos DB hesabınız için sağladığınız aktarım hızını temel alarak bu parametrelerin paralelliğini ve derecesini ayarlayın.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Spark 'dan Azure Cosmos DB Cassandra API bağlanılıyor

### <a name="cqlsh"></a>csqlsh
Aşağıdaki komutlar, csqlsh 'ten Azure CosmosDB Cassandra API bağlanmayı ayrıntılandırır.  Spark içindeki örnekleri kullanarak çalıştırırken doğrulama için faydalıdır.<br>
**Linux/Unix/Mac 'ten:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.  Azure Databricks
Aşağıdaki makalede Azure Databricks küme sağlama, Azure Cosmos DB Cassandra API bağlantı için küme yapılandırma ve DDL işlemlerini, DML işlemlerini ve daha fazlasını kapsayan birkaç örnek Not defteri ele alınmaktadır.<BR>
[Azure databricks Azure Cosmos DB Cassandra API ile çalışma](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2.  Azure HDInsight-Spark
Aşağıdaki makale, HDInsight-Spark hizmeti, sağlama, Azure Cosmos DB Cassandra API bağlanmaya yönelik küme yapılandırması ve DDL işlemlerini, DML işlemlerini ve daha fazlasını kapsayan birkaç örnek Not defterini kapsar.<BR>
[Azure HDInsight 'tan Azure Cosmos DB Cassandra API çalışma-Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3.  Spark ortamı genel
Yukarıdaki bölümler Azure Spark tabanlı PaaS hizmetlerine özgü olduğundan, bu bölümde tüm genel Spark ortamları ele alınmaktadır.  Bağlayıcı bağımlılıkları, içeri aktarmalar ve Spark oturum yapılandırması aşağıda ayrıntılı olarak verilmiştir. "Sonraki adımlar" bölümü, DDL işlemlerine yönelik kod örneklerini, DML işlemlerini ve daha fazlasını içerir.  

#### <a name="connector-dependencies"></a>Bağlayıcı bağımlılıkları:

1. [Spark Için Cassandra bağlayıcısını](cassandra-spark-generic.md#dependencies-for-connectivity) almak üzere Maven koordinatlarını ekleyin
2. Cassandra API için [Azure Cosmos DB Yardımcısı kitaplığı](cassandra-spark-generic.md#dependencies-for-connectivity) için Maven koordinatlarını ekleyin

#### <a name="imports"></a>İşlemlerinin

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Spark oturum yapılandırması:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde Azure Cosmos DB Cassandra API Spark tümleştirmesi gösterilmektedir. 
 
* [DDL işlemleri](cassandra-spark-ddl-ops.md)
* [Oluşturma/ekleme işlemleri](cassandra-spark-create-ops.md)
* [Okuma işlemleri](cassandra-spark-read-ops.md)
* [Upsert işlem](cassandra-spark-upsert-ops.md)
* [Silme işlemleri](cassandra-spark-delete-ops.md)
* [Toplama işlemleri](cassandra-spark-aggregation-ops.md)
* [Tablo kopyalama işlemleri](cassandra-spark-table-copy-ops.md)
