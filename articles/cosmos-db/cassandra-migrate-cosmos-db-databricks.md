---
title: Databricks (Spark) kullanarak Apache Cassandra 'dan Azure Cosmos DB Cassandra API geçirin
description: Azure Databricks ve Spark kullanarak Apache Cassandra veritabanından Azure Cosmos DB Cassandra API nasıl veri geçirebileceğinizi öğrenin.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: caedefbf3887205b68bcd5de5e7cd5f1f7d7f53c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801018"
---
# <a name="migrate-data-from-cassandra-to-an-azure-cosmos-db-cassandra-api-account-by-using-azure-databricks"></a>Cassandra 'dan verileri Azure Databricks kullanarak Azure Cosmos DB Cassandra API hesabına geçirin
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB Cassandra API çeşitli nedenlerle Apache Cassandra üzerinde çalışan kurumsal iş yükleri için harika bir seçenek haline geldi:

* **Yönetme ve izleme için ek yük yoktur:** İşletim sistemi, JVM ve YAML dosyaları ve bunların etkileşimleri arasında ayarları yönetme ve izleme yükünü ortadan kaldırır.

* **Önemli maliyet tasarrufları:** VM 'Lerin maliyeti, bant genişliği ve ilgili tüm lisanslar dahil olmak üzere Azure Cosmos DB maliyetleri kaydedebilirsiniz. Veri merkezlerini, sunucuları, SSD depolamayı, ağ iletişimini ve elektrik maliyetlerini yönetmeniz gerekmez.

* **Mevcut kod ve araçları kullanma özelliği:** Azure Cosmos DB, mevcut Cassandra SDK 'Ları ve araçlarıyla kablo protokol düzeyinde uyumluluk sağlar. Bu uyumluluk, mevcut kod tabanınızı Azure Cosmos DB Cassandra API önemsiz değişikliklerle kullanmanıza de sağlar.

Veritabanı iş yüklerini bir platformdan diğerine geçirmek için birçok yol vardır. [Azure Databricks](https://azure.microsoft.com/services/databricks/) , bir hizmet olarak platform (PaaS) teklifi, büyük ölçekte çevrimdışı geçişler gerçekleştirmeye yönelik bir yol sunan [Apache Spark](https://spark.apache.org/) . Bu makalede, verileri yerel Apache Cassandra keyspaces ve tablolarından Azure Databricks kullanarak Azure Cosmos DB Cassandra API geçirmek için gereken adımlar açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* [Azure Cosmos DB bir Cassandra API hesabı sağlayın](create-cassandra-dotnet.md#create-a-database-account).

* [Bir Azure Cosmos DB Cassandra API bağlanma temellerini inceleyin](cassandra-spark-generic.md).

* Uyumluluk sağlamak için [Azure Cosmos DB Cassandra API desteklenen özellikleri](cassandra-support.md) gözden geçirin.

* Hedef Azure Cosmos DB Cassandra API hesabınızda boş anahtar alanları ve tablolar oluşturduğunuzdan emin olun.

* [Doğrulama için csqlsh veya hosted Shell kullanın](cassandra-support.md#hosted-cql-shell-preview).

## <a name="provision-an-azure-databricks-cluster"></a>Azure Databricks kümesi sağlama

[Azure Databricks kümesi sağlamak](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)için talimatları izleyebilirsiniz. Spark 3,0 ' yi destekleyen Databricks çalışma zamanı sürüm 7,5 ' i seçmenizi öneririz.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks çalışma zamanı sürümünü bulmayı gösteren ekran görüntüsü.":::

## <a name="add-dependencies"></a>Bağımlılık Ekle

Hem yerel hem de Azure Cosmos DB Cassandra uç noktalarına bağlanmak için Apache Spark Cassandra bağlayıcı kitaplığını kümenize eklemeniz gerekir. Kümenizde, **Kitaplıklar**  >  **Yeni**  >  **Maven**'i yükleyip Maven koordinatları ' nı seçin `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` .

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks içinde Maven paketleri aramasını gösteren ekran görüntüsü.":::

Yükleme tamamlandığında **, yükleme ' yi seçin ve** ardından kümeyi yeniden başlatın.

> [!NOTE]
> Cassandra bağlayıcı kitaplığı yüklendikten sonra Databricks kümesini yeniden başlattığınızdan emin olun.

## <a name="create-scala-notebook-for-migration"></a>Geçiş için Scala Not defteri oluşturma

Databricks içinde bir Scala Not defteri oluşturun. Kaynak ve hedef Cassandra yapılandırmalarınızı ilgili kimlik bilgileriyle ve kaynak ve hedef anahtar alanları ve tablolar ile değiştirin. Ardından aşağıdaki kodu çalıştırın:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> `spark.cassandra.output.batch.size.rows`Ve `spark.cassandra.output.concurrent.writes` değerleri ve Spark kümenizdeki çalışanların sayısı, [hız sınırlandırmasını](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/)önlemek için ayarlanacak önemli yapılandırmalardan oluşur. Azure Cosmos DB istekleri sağlanan aktarım hızını veya [istek birimlerini](./request-units.md) (ru) aştığında hız sınırlaması gerçekleşir. Bu ayarları, Spark kümesindeki yürüticilerinin sayısına ve hedef tablolara yazılmakta olan her kaydın boyutunu (ve dolayısıyla RU maliyeti) bağlı olarak ayarlamanız gerekebilir.

## <a name="troubleshoot"></a>Sorun giderme

### <a name="rate-limiting-429-error"></a>Hız sınırlaması (429 hata)

Ayarları minimum değerlerine düşürüyor olsanız bile 429 hata kodu veya "istek oranı büyük" hata metni görebilirsiniz. Aşağıdaki senaryolar hız sınırlandırmasına neden olabilir:

* **Tabloya ayrılan üretilen iş, 6.000 [istek biriminden](./request-units.md)daha azdır**. Spark, minimum ayarlarda bile 6.000 istek birimi veya daha fazlası için bir hızda yazabilir. Paylaşılan verimlilik içeren bir anahtar alanı içinde bir tablo sağladıysanız, bu tabloda çalışma zamanında 6.000 ru 'dan az kullanılabilir.

    Geçişini yaparken, geçiş yaptığınız tablonun en az 6.000 ru kullanılabilir olduğundan emin olun. Gerekirse, bu tabloya adanmış istek birimleri ayırın.

* **Büyük veri hacimiyle aşırı veri eğriliği**. Belirli bir tabloya geçirilecek ancak veriler üzerinde önemli bir çarpıklık (yani, aynı bölüm anahtarı değeri için yazılmakta olan çok sayıda kayıt) varsa büyük miktarınız varsa, tablonuzda sağlanmış birkaç [istek birimi](./request-units.md) olsa bile ücret sınırlandırmaya devam edebilirsiniz. İstek birimleri fiziksel bölümler arasında eşit olarak bölünür ve ağır veri eğriliği, tek bir bölüme yönelik isteklerin performans sorunlarına neden olabilir.

    Bu senaryoda Spark 'ta en düşük işleme ayarlarına düşürün ve geçişi yavaş çalışmaya zorlayın. Bu senaryo, erişimin daha sık olduğu ve eğriliği yüksek olabilecek başvuru veya denetim tablolarını geçirdiğinizde daha yaygın olabilir. Ancak, başka bir tablo türünde önemli bir eğme varsa, sabit durum işlemleri sırasında iş yükünüz için sık erişimli bölüm sorunlarından kaçınmak üzere veri modelinizi gözden geçirmek isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Kapsayıcı ve veritabanlarına aktarım hızı sağlama](set-throughput.md)
* [Bölüm anahtarı en iyi uygulamaları](partitioning-overview.md#choose-partitionkey)
* [Azure Cosmos DB kapasite planlayıcısını kullanarak RU/s 'yi tahmin etme](estimate-ru-with-capacity-planner.md)
* [Azure Cosmos DB Cassandra API elastik ölçek](manage-scale-cassandra.md)
