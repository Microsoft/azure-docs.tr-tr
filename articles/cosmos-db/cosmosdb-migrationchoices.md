---
title: Cosmos DB geçiş seçenekleri
description: Bu belge, şirket içi veya bulut verilerinizi Azure Cosmos DB 'e geçirmeye yönelik çeşitli seçenekleri açıklar.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 3325960793a5a0d7bc48ca8030c675d7ebf0c026
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106442601"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Şirket içi veya bulut verilerinizi Azure Cosmos DB 'e geçirmeye yönelik seçenekler
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB için çeşitli veri kaynaklarından veri yükleyebilirsiniz. Azure Cosmos DB birden çok API 'yi desteklediğinden, hedefler mevcut API 'lerden herhangi biri olabilir. Aşağıda Azure Cosmos DB verileri geçirebileceğiniz bazı senaryolar verilmiştir:

* Verileri bir Azure Cosmos kapsayıcısından aynı veritabanındaki başka bir kapsayıcıya veya farklı veritabanlarına taşıyın.
* Ayrılmış kapsayıcılar arasında verileri paylaşılan veritabanı kapsayıcılarına taşıma.
* Region1 içinde bulunan bir Azure Cosmos hesabındaki verileri, aynı veya farklı bir bölgedeki başka bir Azure Cosmos hesabına taşıyın.
* Azure Blob depolama, bir JSON dosyası, Oracle Database, Couşbase, DynamoDB gibi bir kaynaktan verileri Azure Cosmos DB taşıyın.

Çeşitli kaynaklardan farklı Azure Cosmos DB API 'Lerine geçiş yollarını desteklemek için, her geçiş yolu için özel işleme sağlayan birden çok çözüm vardır. Bu belgede, kullanılabilir çözümler listelenmekte ve bunların avantajları ve sınırlamaları açıklanmaktadır.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Geçiş aracı seçimini etkileyen faktörler

Aşağıdaki faktörler geçiş aracı seçiminde belirleyici rol oynar:

* **Çevrimiçi veya çevrimdışı geçiş**: Birçok geçiş aracı yalnızca bir kerelik geçiş yapmak için yol sağlar. Diğer bir deyişle veritabanına erişen uygulamalar belirli bir kapalı kalma süresiyle karşılaşabilir. Bazı geçiş çözümleri, kaynak ile hedef arasında bir çoğaltma işlem hattının ayarlandığı dinamik bir geçiş yapmanın yolunu sağlar.

* **Veri kaynağı**: mevcut VERILER Oracle DB2, DataStax Cassanda, Azure SQL veritabanı, PostgreSQL vb. gibi çeşitli veri kaynaklarında bulunabilir. Veriler de mevcut bir Azure Cosmos DB hesabında olabilir ve geçiş amacı veri modelini değiştirebilir veya bir kapsayıcıdaki verileri farklı bir bölüm anahtarıyla yeniden bölümleyebilir.

* **Azure Cosmos DB API**: Azure Cosmos DB'deki SQL API için, Azure Cosmos DB ekibinin geliştirdiği ve farklı geçiş senaryolarına yardımcı olacak çeşitli araçlar vardır. Diğer tüm API'lerin topluluk tarafından geliştirilen özelleştirilmiş kendi araç kümesi bulunur. Azure Cosmos DB bu API'leri kablo protokolü düzeyinde desteklediğinden, verileri Azure Cosmos DB'ye geçirirken de bu araçlar oldukları gibi çalışmalıdır. Öte yandan kısıtlamalar için özel işlem gerektirebilir çünkü bu kavram Azure Cosmos DB'ye özgüdür.

* **Verilerin boyutu**: Geçiş araçlarının çoğu daha küçük veri kümelerinde çok iyi çalışır. Veri kümesi birkaç yüz gigabaytı aştığında geçiş aracı seçenekleri sınırlıdır. 

* **Beklenen geçiş süresi**: Geçişler az aktarım hızı kullanacak şeklide yavaş, küçük artışlarla yapılacak şekilde yapılandırılabilir veya hedef Azure Cosmos DB kapsayıcısında sağlanan aktarım hızının tamamını kullanarak daha kısa sürede tamamlanabilir.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API

|Geçiş türü|Çözüm|Desteklenen kaynaklar|Desteklenen hedefler|Dikkat edilmesi gerekenler|
|---------|---------|---------|---------|---------|
|Çevrimdışı|[Veri Geçişi Aracı](import-data.md)| &bull;JSON/CSV dosyaları<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Tablo Depolama<br/>&bull;AWS DynamoDB<br/>&bull;Azure Blob depolama|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB tabloları API 'SI<br/>&bull;JSON dosyaları |&bull; Daha kolay bir şekilde ayarlanır ve birden çok kaynağı destekler. <br/>&bull; Büyük veri kümeleri için uygun değildir.|
|Çevrimdışı|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON/CSV dosyaları<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB için Azure Cosmos DB API<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;Tablo Depolama<br/>&bull;Azure Blob depolama <br/> <br/>Desteklenen diğer kaynaklar için [Azure Data Factory](../data-factory/connector-overview.md) makalesine bakın.|&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB için Azure Cosmos DB API<br/>&bull;JSON dosyaları <br/><br/> Desteklenen diğer hedefler için [Azure Data Factory](../data-factory/connector-overview.md) makalesine bakın. |&bull; Daha kolay bir şekilde ayarlanır ve birden çok kaynağı destekler.<br/>&bull; Azure Cosmos DB toplu yürütücü Kitaplığı ' nı kullanır. <br/>&bull; Büyük veri kümeleri için uygundur. <br/>&bull; Denetimsiz işaret olmaması-geçiş işlemi sırasında bir sorun oluşursa, tüm geçiş sürecini yeniden başlatmanız gerekir.<br/>&bull; Geçerliliği kalmamış bir sıra yok-bu, birkaç hatalı dosyanın tüm geçiş sürecini durduramaması anlamına gelir.|
|Çevrimdışı|[Azure Cosmos DB Spark Bağlayıcısı](spark-connector.md)|SQL API Azure Cosmos DB. <br/><br/>Diğer kaynakları Spark ekosistemindeki ek bağlayıcılarla birlikte kullanabilirsiniz.| SQL API Azure Cosmos DB. <br/><br/>Diğer hedefleri Spark ekosistemindeki ek bağlayıcılarla birlikte kullanabilirsiniz.| &bull; Azure Cosmos DB toplu yürütücü Kitaplığı ' nı kullanır. <br/>&bull; Büyük veri kümeleri için uygundur. <br/>&bull; Özel bir Spark kurulumuna ihtiyaç duyuyor. <br/>&bull; Spark, şema tutarsızlıklarına duyarlıdır ve bu, geçiş sırasında bir sorun olabilir. |
|Çevrimdışı|[Cosmos DB toplu yürütücü kitaplığı ile özel araç](migrate-cosmosdb-data.md)| Kaynak, özel kodunuza bağlıdır | Azure Cosmos DB SQL API| &bull; Geçiş dayanıklılığını artıran denetim noktası, ölü Özellik özellikleri sağlar. <br/>&bull; Çok büyük veri kümeleri (10 TB +) için uygundur.  <br/>&bull; App Service olarak çalışan bu aracın özel kurulumunu gerektirir. |
|Çevrimiçi|[Cosmos DB Işlevleri + ChangeFeed API 'SI](change-feed-functions.md)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; Kolayca ayarlanabilir. <br/>&bull; Yalnızca kaynak bir Azure Cosmos DB kapsayıcısı ise geçerlidir. <br/>&bull; Büyük veri kümeleri için uygun değildir. <br/>&bull; Kaynak kapsayıcıdan silmeleri yakalamaz. |
|Çevrimiçi|[Değişiklik akışını kullanarak özel geçiş hizmeti](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; İlerleme durumunu izleme sağlar. <br/>&bull; Yalnızca kaynak bir Azure Cosmos DB kapsayıcısı ise geçerlidir. <br/>&bull; Daha büyük veri kümeleri için de geçerlidir.<br/>&bull; Kullanıcının değişiklik akışı işlemcisini barındırmak için bir App Service ayarlaması gerekir. <br/>&bull; Kaynak kapsayıcıdan silmeleri yakalamaz.|
|Çevrimiçi|[Anlık ileti](cosmosdb-sql-api-migrate-data-striim.md)| &bull;Oracle <br/>&bull;Apache Cassandra<br/><br/> Desteklenen diğer kaynaklar için bkz. [anlık ileti Web sitesi](https://www.striim.com/sources-and-targets/) . |&bull;Azure Cosmos DB SQL API <br/>&bull; Azure Cosmos DB Cassandra API<br/><br/> Desteklenen diğer hedefler için bkz. [anlık ileti Web sitesi](https://www.striim.com/sources-and-targets/) . | &bull; Oracle, DB2, SQL Server gibi çok çeşitli kaynaklarla birlikte çalışarak.<br/>&bull; ETL işlem hatlarını kolayca oluşturun ve izleme için bir pano sağlar. <br/>&bull; Daha büyük veri kümelerini destekler. <br/>&bull; Bu bir üçüncü taraf araç olduğundan Market 'ten satın alınması ve kullanıcının ortamına yüklenmesi gerekir.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API 'SI

|Geçiş türü|Çözüm|Desteklenen kaynaklar|Desteklenen hedefler|Dikkat edilmesi gerekenler|
|---------|---------|---------|---------|---------|
|Çevrimiçi|[Azure Veritabanı Geçiş Hizmeti](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|MongoDB için Azure Cosmos DB API |&bull; Azure Cosmos DB toplu yürütücü Kitaplığı ' nı kullanır. <br/>&bull; Büyük veri kümeleri için uygundur ve canlı değişiklikleri çoğaltmaya özen gösterin. <br/>&bull; Yalnızca diğer MongoDB kaynaklarıyla birlikte geçerlidir.|
|Çevrimdışı|[Azure Veritabanı Geçiş Hizmeti](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| MongoDB için Azure Cosmos DB API| &bull; Azure Cosmos DB toplu yürütücü Kitaplığı ' nı kullanır. <br/>&bull; Büyük veri kümeleri için uygundur ve canlı değişiklikleri çoğaltmaya özen gösterin. <br/>&bull; Yalnızca diğer MongoDB kaynaklarıyla birlikte geçerlidir.|
|Çevrimdışı|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON/CSV dosyaları<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB için Azure Cosmos DB API <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Tablo Depolama<br/>&bull;Azure Blob depolama <br/><br/> Desteklenen diğer kaynaklar için [Azure Data Factory](../data-factory/connector-overview.md) makalesine bakın. | &bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB için Azure Cosmos DB API <br/>&bull; JSON dosyaları <br/><br/> Desteklenen diğer hedefler için [Azure Data Factory](../data-factory/connector-overview.md) makalesine bakın.| &bull; Daha kolay bir şekilde ayarlanır ve birden çok kaynağı destekler. <br/>&bull; Azure Cosmos DB toplu yürütücü Kitaplığı ' nı kullanır. <br/>&bull; Büyük veri kümeleri için uygundur. <br/>&bull; CheckIn olmaması, geçiş işlemi sırasında herhangi bir sorunun tüm geçiş sürecinin yeniden başlatılmasını gerektirmeyeceği anlamına gelir.<br/>&bull; Sahipsiz bir sıra olmaması, birkaç hatalı dosyanın tüm geçiş sürecini durduramaması anlamına gelir. <br/>&bull; Belirli veri kaynakları için okuma aktarım hızını artırmak için özel koda ihtiyaç duyuyor.|
|Çevrimdışı|[Mevcut Mongo araçları (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|MongoDB | MongoDB için Azure Cosmos DB API| &bull; Kolayca ayarlama ve tümleştirme. <br/>&bull; Kısıtlar için özel işlemeye ihtiyaç duyuyor.|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API'si

|Geçiş türü|Çözüm|Desteklenen kaynaklar|Desteklenen hedefler|Dikkat edilmesi gerekenler|
|---------|---------|---------|---------|---------|
|Çevrimdışı|[csqlsh COPY komutu](cassandra-import-data.md#migrate-data-by-using-the-cqlsh-copy-command)|CSV dosyaları | Azure Cosmos DB Cassandra API'si| &bull; Kolayca ayarlanabilir. <br/>&bull; Büyük veri kümeleri için uygun değildir. <br/>&bull; Yalnızca kaynak bir Cassandra tablosu olduğunda geçerlidir.|
|Çevrimdışı|[Spark ile tablo kopyalama](cassandra-import-data.md#migrate-data-by-using-spark) | &bull;Apache Cassandra<br/>&bull;Azure Cosmos DB Cassandra API'si| Azure Cosmos DB Cassandra API'si | &bull; , Paralel hale getirmek dönüşüm ve alımı için Spark yeteneklerini kullanabilir. <br/>&bull; Azaltıcı lıkları işlemek için özel bir yeniden deneme ilkesiyle yapılandırma gerekir.|
|Çevrimiçi|[Anlık ileti (Oracle DB/Apache Cassandra 'dan)](cosmosdb-cassandra-api-migrate-data-striim.md)| &bull;Oracle<br/>&bull;Apache Cassandra<br/><br/> Desteklenen diğer kaynaklar için bkz. [anlık ileti Web sitesi](https://www.striim.com/sources-and-targets/) .|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB Cassandra API'si <br/><br/> Desteklenen diğer hedefler için bkz. [anlık ileti Web sitesi](https://www.striim.com/sources-and-targets/) .| &bull; Oracle, DB2, SQL Server gibi çok çeşitli kaynaklarla birlikte çalışarak. <br/>&bull; ETL işlem hatlarını kolayca oluşturun ve izleme için bir pano sağlar. <br/>&bull; Daha büyük veri kümelerini destekler. <br/>&bull; Bu bir üçüncü taraf araç olduğundan Market 'ten satın alınması ve kullanıcının ortamına yüklenmesi gerekir.|
|Çevrimiçi|[Blitzz (Oracle DB/Apache Cassandra 'dan)](oracle-migrate-cosmos-db-blitzz.md)|&bull;Oracle<br/>&bull;Apache Cassandra<br/><br/>Desteklenen diğer kaynaklar için [Blitzz Web sitesine](https://www.blitzz.io/) bakın. |Azure Cosmos DB Cassandra API. <br/><br/>Desteklenen diğer hedefler için [Blitzz Web sitesine](https://www.blitzz.io/) bakın. | &bull; Daha büyük veri kümelerini destekler. <br/>&bull; Bu bir üçüncü taraf araç olduğundan Market 'ten satın alınması ve kullanıcının ortamına yüklenmesi gerekir.|

## <a name="other-apis"></a>Diğer API'ler

SQL API, Mongo API ve Cassandra API dışındaki API 'Ler için, her API 'nin mevcut ekosistemlerinin her biri tarafından desteklenen çeşitli araçlar vardır. 

**Tablo API’si** 

* [Veri Geçişi Aracı](table-import.md#data-migration-tool)
* [AzCopy](table-import.md#migrate-data-by-using-azcopy)

**Gremlin API**

* [Graph toplu yürütücü kitaplığı](bulk-executor-graph-dotnet.md)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Sonraki adımlar

* [.Net](bulk-executor-dot-net.md) ve [Java](bulk-executor-java.md)'daki toplu yürütücü kitaplığını kullanan örnek uygulamaları deneyerek daha fazla bilgi edinin. 
* Toplu yürütücü kitaplığı, Cosmos DB Spark Bağlayıcısı ile tümleşiktir, daha fazla bilgi edinmek için bkz. [Azure Cosmos DB Spark Bağlayıcısı](spark-connector.md) makalesi.  
* Büyük ölçekli geçişlerle ilgili ek yardım için "genel Danışma belgesi" sorun türü ve "büyük (TB +) geçişleri" sorun alt türü altında bir destek bileti açarak Azure Cosmos DB ürün ekibine başvurun.
