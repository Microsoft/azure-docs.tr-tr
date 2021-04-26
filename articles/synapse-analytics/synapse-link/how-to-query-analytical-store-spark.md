---
title: Azure SYNAPSE link 'te Apache Spark kullanarak Azure Cosmos DB etkileşim kurma
description: Azure SYNAPSE link 'te Apache Spark kullanarak Azure Cosmos DB ile etkileşim kurma
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 4a8367ea41ea96d8a412af965346684737d190fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627583"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-in-azure-synapse-link"></a>Azure SYNAPSE link 'te Apache Spark kullanarak Azure Cosmos DB etkileşim kurma

Bu makalede, SYNAPSE Apache Spark kullanarak Azure Cosmos DB nasıl etkileşim kuracağınızı öğreneceksiniz. Scala, Python, Mini SQL ve C# için tam destek sayesinde SYNAPSE Apache Spark, [Azure Cosmos DB Için Azure SYNAPSE bağlantısı](../../cosmos-db/synapse-link.md)'ndaki analiz, veri Mühendisliği, veri bilimi ve veri araştırma senaryolarına yönelik olarak tasarlanmıştır.

Aşağıdaki yetenekler Azure Cosmos DB etkileşimde desteklenirken desteklenir:
* SYNAPSE Apache Spark, işlem iş yüklerinizin performansını etkilemeden neredeyse gerçek zamanlı olarak Azure SYNAPSE bağlantısı ile etkinleştirilen Azure Cosmos DB kapsayıcılarınızdaki verileri analiz etmenizi sağlar. Spark 'dan Azure Cosmos DB [analitik depoyu](../../cosmos-db/analytical-store-introduction.md) sorgulamak için aşağıdaki iki seçenek mevcuttur:
    + Spark DataFrame 'e yükle
    + Spark tablosu oluşturma
* SYNAPSE Apache Spark ayrıca, Azure Cosmos DB verileri almanıza olanak sağlar. Verilerin her zaman işlemsel mağaza aracılığıyla Azure Cosmos DB kapsayıcılara alınıp gönderildiğine dikkat edin. SYNAPSE bağlantısı etkinleştirildiğinde, tüm yeni ekler, güncelleştirmeler ve silmeler otomatik olarak analitik depola eşitlenir.
* SYNAPSE Apache Spark ayrıca, kaynak ve havuz olarak Azure Cosmos DB ile Spark yapısal akışını destekler. 

Aşağıdaki bölümler, yukarıdaki yetenekler söz diziminde size yol gösterir. Azure SYNAPSE Analytics çalışma alanındaki hareketler, kullanmaya başlamak için kullanımı kolay bir deneyim sunmak üzere tasarlanmıştır. SYNAPSE çalışma alanının **veri** sekmesindeki bir Azure Cosmos DB kapsayıcısına sağ tıkladığınızda hareketler görünür. Hareketler sayesinde hızlı bir şekilde kod oluşturabilir ve ihtiyaçlarınıza göre düzenleyebilirsiniz. Hareketler ayrıca tek tıklamayla verileri keşfetmek için de idealdir.

> [!IMPORTANT]
> Analitik Şemadaki bazı kısıtlamaların, veri yükleme işlemlerinde beklenmedik davranışa yol açabilecek şekilde farkında olmanız gerekir.
> Örnek olarak, analiz şemasında, işlemsel şemadan yalnızca ilk 1000 özellik kullanılabilir, boşluk içeren özellikler kullanılamaz, vb. Beklenmedik sonuçlarla karşılaşırsanız, daha fazla ayrıntı için [analitik depo şeması kısıtlamalarını](../../cosmos-db/analytical-store-introduction.md#schema-constraints) kontrol edin.

## <a name="query-azure-cosmos-db-analytical-store"></a>Analitik depoyu sorgula Azure Cosmos DB

Azure Cosmos DB analitik depoyu sorgulamak, Spark DataFrame 'e yüklemek ve Spark tablosu oluşturmak için kullanabileceğiniz iki olası seçenek hakkında bilgi edinmek için, bu deneyim deneyimdeki farklılıkları inceleyerek gereksinimlerinize göre çalışma seçeneğini seçebilmenizi sağlamaktır.

Deneyimdeki fark, Azure Cosmos DB kapsayıcısında temel alınan veri değişikliklerinin Spark içinde gerçekleştirilen Analize otomatik olarak yansıtılmalıdır. Bir Spark DataFrame veya bir kapsayıcının analitik deposunda bir Spark tablosu oluşturulduğunda, analitik depodaki verilerin geçerli anlık görüntüsünün etrafındaki meta veriler, sonraki çözümlemenin etkili bir şekilde kapatılması için Spark 'a getirilir. Spark DataFrame üzerinde bir eylem çağrılmadığı veya Spark tablosunda bir mini SQL sorgusunun yürütülmediği durumlar olduğundan, Spark 'un bir yavaş değerlendirme ilkesini izlediğine dikkat edin. gerçek veriler, temel alınan kapsayıcının analitik deposundan getirilmez.

**Spark DataFrame'e yükleme** durumunda, getirilen meta veriler Spark oturumunun kullanım ömrü boyunca önbelleğe alınır ve dolasıyla DataFrame'de bundan sonra çağrılan eylemler DataFrame'i oluşturma sırasındaki analiz deposu anlık görüntüsü üzerinde gerçekleştirilir.

Öte yandan **Spark tablosu oluşturma** durumunda, analiz deposu durumunun meta verileri Spark'ta önbelleğe alınmaz ve Spark tablosunda yürütülen her SparkSQL sorgusu için yeniden yüklenir.

Bu nedenle, Spark analizinizin analiz deposunun sabit bir anlık görüntüsü üzerinde mi yoksa analiz deposunun en son anlık görüntüsü üzerinde mi gerçekleştirilmesini istediğinize bağlı olarak, sırasıyla Spark DataFrame'i yüklemeyi veya Spark tablosu oluşturmayı seçebilirsiniz.

> [!NOTE]
> Mongo DB hesaplarının Azure Cosmos DB API 'sini sorgulamak için, analitik depodaki [tam uygunluk şeması gösterimi](../../cosmos-db/analytical-store-introduction.md#analytical-schema) ve kullanılacak genişletilmiş özellik adları hakkında daha fazla bilgi edinin.

### <a name="load-to-spark-dataframe"></a>Spark DataFrame 'e yükle

Bu örnekte, Azure Cosmos DB analitik deposuna işaret eden bir Spark DataFrame oluşturacaksınız. Daha sonra DataFrame 'e göre Spark eylemlerini çağırarak ek analiz gerçekleştirebilirsiniz. Bu işlem, işlem deposunu etkilemez.

**Python** 'daki sözdizimi aşağıdaki gibi olacaktır:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

**Scala** 'daki eşdeğer sözdizimi aşağıdaki gibi olacaktır:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>Spark tablosu oluşturma

Bu örnekte, Azure Cosmos DB analitik deposunu işaret eden bir Spark tablosu oluşturacaksınız. Daha sonra tabloya karşı mini SQL sorguları çağırarak ek analizler gerçekleştirebilirsiniz. Bu işlem, işlem mağazasını etkilemez veya hiçbir veri hareketine neden olmaz. Bu Spark tablosunu silmeye karar verirseniz, temel alınan Azure Cosmos DB kapsayıcısı ve ilgili analitik depo etkilenmeyecektir. 

Bu senaryo, Spark tablolarının üçüncü taraf araçlar aracılığıyla yeniden kullanılması ve çalışma zamanı için temel alınan verilere erişilebilirlik sağlaması açısından kullanışlıdır.

Spark tablosu oluşturma sözdizimi aşağıdaki gibidir:
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> Temel Azure Cosmos DB kapsayıcısının şemasının zaman içinde değiştiği senaryolarınız varsa ve güncelleştirilmiş şemanın Spark tablosunda çalıştırılan sorgulara otomatik olarak yansıtılmasını istiyorsanız, Spark tablo seçeneklerinde `spark.cosmos.autoSchemaMerge` seçeneğini `true` değerine ayarlayarak bunu başarabilirsiniz.


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>Spark veri çerçevesini Azure Cosmos DB kapsayıcısına yaz

Bu örnekte, bir Azure Cosmos DB kapsayıcısına Spark DataFrame yazacaksınız. Bu işlem, işlem iş yüklerinin performansını etkiler ve Azure Cosmos DB kapsayıcısına veya paylaşılan veritabanına sağlanan istek birimlerini tüketir.

**Python** 'daki sözdizimi aşağıdaki gibi olacaktır:
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

**Scala** 'daki eşdeğer sözdizimi aşağıdaki gibi olacaktır:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Kapsayıcıdan akış veri çerçevesini yükleme
Bu hareket halinde, bir kapsayıcıdan veri çerçevesine veri yüklemek için Spark akış özelliğini kullanacaksınız. Veriler, çalışma alanına bağladığınız birincil Data Lake hesabında (ve dosya sisteminde) depolanır. 
> [!NOTE]
> SYNAPSE Apache Spark dış kitaplıklara başvurmak istiyorsanız, [buradan](#external-library-management)daha fazla bilgi edinebilirsiniz. Örneğin, bir Spark veri çerçevesini Mongo DB için Cosmos DB API 'SI kapsayıcısına almak istiyorsanız Spark için Mongo DB bağlayıcısını [buradan](https://docs.mongodb.com/spark-connector/master/)kullanabilirsiniz.

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>Azure Cosmos DB kapsayıcısından akış veri çerçevesini yükleme
Bu örnekte, Azure Cosmos DB akışı Değiştir işlevini kullanarak bir Azure Cosmos DB kapsayıcısından bir Spark akış veri çerçevesine veri yüklemek için Spark 'ın yapılandırılmış akış özelliğini kullanacaksınız. Spark tarafından kullanılan denetim noktası verileri, çalışma alanına bağladığınız birincil Data Lake hesabında (ve dosya sisteminde) depolanır.

*/Localreadcheckpointfolder* klasörü oluşturulmadıysa (aşağıdaki örnekte) otomatik olarak oluşturulur. Bu işlem, işlem iş yüklerinin performansını etkiler ve Azure Cosmos DB kapsayıcısı veya paylaşılan veritabanında sağlanan Istek birimlerini kullanır.

**Python** 'daki sözdizimi aşağıdaki gibi olacaktır:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

**Scala** 'daki eşdeğer sözdizimi aşağıdaki gibi olacaktır:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>Akış veri çerçevesini Azure Cosmos DB kapsayıcısına yaz
Bu örnekte, bir Azure Cosmos DB kapsayıcısına bir akış veri çerçevesi yazacaksınız. Bu işlem, işlem iş yüklerinin performansını etkiler ve Azure Cosmos DB kapsayıcısı veya paylaşılan veritabanında sağlanan Istek birimlerini kullanır. */Localwritecheckpointfolder* klasörü oluşturulmadıysa (aşağıdaki örnekte) otomatik olarak oluşturulur. 

**Python** 'daki sözdizimi aşağıdaki gibi olacaktır:

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

# If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

**Scala** 'daki eşdeğer sözdizimi aşağıdaki gibi olacaktır:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

// If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```

## <a name="external-library-management"></a>Dış Kitaplık Yönetimi

Bu örnekte, Synpade Apache Spark çalışma alanlarında Spark Not defterleri kullanırken JAR dosyalarından dış kitaplıklara nasıl başvurulacağını öğreneceksiniz. JAR dosyalarını, çalışma alanına bağladığınız birincil Data Lake hesabındaki bir kapsayıcıya yerleştirebilir ve ardından `%configure` Spark Not defterinize aşağıdaki ifadeyi ekleyebilirsiniz:

```cmd
%%configure -f
{
    "jars": [
        "abfss://<storage container name>@<data lake account name>.dfs.core.windows.net/<path to jar>"
    ]
}
```
Uzak Spark iş tanımlarını sunucusuz bir Apache Spark havuzuna göndermek istiyorsanız, bu [öğreticiyi](../spark/apache-spark-job-definitions.md)izleyerek dış kitaplıklara nasıl başvurulacağını öğrenebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [GitHub 'da Azure SYNAPSE bağlantısı ile çalışmaya başlama örnekleri](https://aka.ms/cosmosdb-synapselink-samples)
* [Azure Cosmos DB için Azure SYNAPSE bağlantısı 'nda nelerin desteklendiğini öğrenin](./concept-synapse-link-cosmos-db-support.md)
* [Azure Cosmos DB için SYNAPSE bağlantısına Bağlan](../quickstart-connect-synapse-link-cosmos-db.md)
