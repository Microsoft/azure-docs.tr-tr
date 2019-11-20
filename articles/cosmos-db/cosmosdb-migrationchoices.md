---
title: Cosmos DB geçiş seçenekleri
description: Bu belge, şirket içi veya bulut verilerinizi Azure Cosmos DB 'e geçirmeye yönelik çeşitli seçenekleri açıklar.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 9111193bb441487b9e3c49bc9ee1a296d49f8a31
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882397"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Şirket içi veya bulut verilerinizi Azure Cosmos DB 'e geçirmeye yönelik seçenekler

Azure Cosmos DB için çeşitli veri kaynaklarından veri yükleyebilirsiniz. Ayrıca, Azure Cosmos DB birden çok API desteklediğinden, hedefler mevcut API 'lerden herhangi biri olabilir. Çeşitli kaynaklardan farklı Azure Cosmos DB API 'Lerine geçiş yollarını desteklemek için, her geçiş yolu için özel işleme sağlayan birden çok çözüm vardır. Bu belgede, kullanılabilir çözümler listelenmekte ve bunların avantajları ve sınırlamaları açıklanmaktadır.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Geçiş Aracı seçimini etkileyen faktörler

Aşağıdaki etmenler geçiş aracının seçimini belirlemektir:
* **Çevrimiçi ve çevrimdışı geçiş**: birçok geçiş aracı yalnızca bir kerelik geçiş yapmak için bir yol sağlar. Bu, veritabanına erişen uygulamaların bir süre kapalı kalma süresine neden olabileceği anlamına gelir. Bazı geçiş çözümleri, kaynak ve hedef arasında bir çoğaltma işlem hattının ayarlandığı dinamik geçiş yapmak için bir yol sağlar.

* **Veri kaynağı**: mevcut VERILER Oracle DB2, DataStax Cassanda, Azure SQL Server, PostgreSQL vb. gibi çeşitli veri kaynaklarında bulunabilir. Veriler de mevcut bir Azure Cosmos DB hesabında olabilir ve geçiş amacı veri modelini değiştirebilir veya bir kapsayıcıdaki verileri farklı bir bölüm anahtarıyla yeniden bölümleyebilir.

* **Azure Cosmos DB API**: Azure Cosmos db SQL API 'si için, farklı geçiş senaryolarına yardımcı olan Azure Cosmos DB ekibi tarafından geliştirilen çeşitli araçlar vardır. Diğer tüm API 'Lerde, topluluk tarafından geliştirilen ve tutulan kendi özelleştirilmiş araç kümesi vardır. Azure Cosmos DB, bu API 'Leri bir kablo protokol düzeyinde desteklediğinden, bu araçların verileri Azure Cosmos DB geçirirken olduğu gibi çalışması gerekir. Ancak, bu kavram Azure Cosmos DB özel olarak, bu kavram için özel işleme gerektirebilir.

* **Verilerin boyutu**: çoğu geçiş araçları daha küçük veri kümeleri için çok iyi çalışır. Veri kümesi birkaç yüz gigabayttan geçtiğinde, geçiş araçlarının seçimleri sınırlıdır. 

* **Geçiş süresi bekleniyordu**: geçişler, daha az üretilen iş veya hedef Azure Cosmos DB kapsayıcısında sağlanan tüm aktarım hızını tüketebilir ve geçişi daha az bir şekilde tamamlayabileceği yavaş, artımlı bir hızda gerçekleşecek şekilde yapılandırılabilir. ışınızda.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
|**Geçiş türü**|**Çözüm**|**Dikkat edilmesi gerekenler**|
|---------|---------|---------|
|Çevrimdışı|[Veri geçiş aracı](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; daha fazla kaynak kurmak ve desteklemek kolaydır <br/>&bull; büyük veri kümeleri için uygun değil|
|Çevrimdışı|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; daha fazla kaynak kurmak ve desteklemek kolaydır <br/>&bull; Azure Cosmos DB toplu yürütücü kitaplığını kullanır <br/>&bull; büyük veri kümeleri için uygun <br/>Denetim noktası eksikliğinden &bull;, geçiş işlemi sırasında bir sorun oluşursa, tüm geçiş sürecini yeniden başlatmanız gerekir.<br/>&bull; ' dan fazla olmayan bir sıranın olmaması-bu, birkaç hatalı dosyanın tüm geçiş sürecini durdurmasına yol açabilir.|
|Çevrimdışı|[Azure Cosmos DB Spark Bağlayıcısı](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull; Azure Cosmos DB toplu yürütücü kitaplığını kullanır <br/>&bull; büyük veri kümeleri için uygun <br/>&bull; özel bir Spark kurulumuna Ihtiyaç duyuyor <br/>&bull; Spark, şema tutarsızlıklarına duyarlıdır ve bu, geçiş sırasında bir sorun olabilir |
|Çevrimdışı|[Cosmos DB toplu yürütücü kitaplığı ile özel araç](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;, geçiş dayanıklılığını artıran denetim noktası, ölü Özellik özellikleri sağlar <br/>&bull; çok büyük veri kümeleri için uygun (10 TB +)  <br/>&bull;, bu aracın App Service olarak çalışan özel kurulumunu gerektirir |
|Çevrimiçi|[Cosmos DB Işlevleri + ChangeFeed API 'SI](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull; kolayca ayarlanabilir <br/>&bull; yalnızca kaynak bir Azure Cosmos DB kapsayıcısı ise kullanılabilir <br/>&bull; büyük veri kümeleri için uygun değil <br/>&bull;, kaynak kapsayıcıdan silme yakalamaz |
|Çevrimiçi|[Değişiklik akışını kullanarak özel geçiş hizmeti](https://aka.ms/CosmosDBMigrationSample)|&bull; ilerleme izleme sağlar <br/>&bull; yalnızca kaynak bir Azure Cosmos DB kapsayıcısı ise kullanılabilir <br/>&bull; daha büyük veri kümeleri için de geçerlidir <br/>&bull;, kullanıcının değişiklik akışı işlemcisini barındırmak için bir App Service kurulumunu gerektirir <br/>&bull;, kaynak kapsayıcıdan silme yakalamaz|
|Çevrimiçi|[Anlık ileti](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;, Oracle, DB2, SQL Server gibi çok çeşitli kaynaklarla çalışmaktadır <br/>&bull;, ETL işlem hatlarını derlemek ve izleme için bir pano sağlar <br/>&bull; daha büyük veri kümelerini destekler <br/>&bull; bu üçüncü taraf bir araç olduğundan Market 'ten satın alınması ve kullanıcının ortamına yüklenmesi gerekir|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API 'SI
|**Geçiş türü**|**Çözüm**|**Dikkat edilmesi gerekenler**|
|---------|---------|---------|
|Çevrimdışı|[Veri geçiş aracı](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; daha fazla kaynak kurmak ve desteklemek kolaydır <br/>&bull; büyük veri kümeleri için uygun değil|
|Çevrimdışı|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; daha fazla kaynak kurmak ve desteklemek kolaydır <br/>&bull; Azure Cosmos DB toplu yürütücü kitaplığını kullanır <br/>&bull; büyük veri kümeleri için uygun <br/>checksize &bull; olmaması, geçiş işlemi sırasında herhangi bir sorunun tüm geçiş sürecinin yeniden başlatılmasını gerektirmeyeceği anlamına gelir.<br/>&bull; geçersiz bir sıra olması, birkaç hatalı dosyanın tüm geçiş sürecini durduramaması anlamına gelir. <br/>&bull;, belirli veri kaynakları için okuma aktarım hızını artırmak için özel kod gerektirir|
|Çevrimdışı|[Mevcut Mongo araçları (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; kolay ayarlama ve Tümleştirme <br/>&bull; ' ın kısıtlar için özel işleme Ihtiyacı vardır|
|Çevrimiçi|[Azure Veritabanı Geçiş Hizmeti](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull; Azure Cosmos DB toplu yürütücü kitaplığını kullanır <br/>&bull;, büyük veri kümeleri için uygundur ve canlı değişiklikleri çoğaltmaya karşı sürer <br/>&bull; yalnızca diğer MongoDB kaynaklarıyla kullanılabilir|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API
|**Geçiş türü**|**Çözüm**|**Dikkat edilmesi gerekenler**|
|---------|---------|---------|
|Çevrimdışı|[csqlsh COPY komutu](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull; kolayca ayarlanabilir <br/>&bull; büyük veri kümeleri için uygun değil <br/>&bull; yalnızca kaynak bir Cassandra tablosu olduğunda geçerlidir|
|Çevrimdışı|[Spark ile tablo kopyalama](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;, paralel hale getirmek dönüştürme ve alma için Spark yeteneklerini kullanabilir <br/>&bull; ' ın, azaltıcı Les 'yi işlemek için özel bir yeniden deneme ilkesiyle yapılandırılması gerekir|
|Çevrimiçi|[Anlık ileti (Oracle DB/Apache Cassandra 'dan)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;, Oracle, DB2, SQL Server gibi çok çeşitli kaynaklarla çalışmaktadır <br/>&bull;, ETL işlem hatlarını derlemek ve izleme için bir pano sağlar <br/>&bull; daha büyük veri kümelerini destekler <br/>&bull; bu üçüncü taraf bir araç olduğundan Market 'ten satın alınması ve kullanıcının ortamına yüklenmesi gerekir|
|Çevrimiçi|[Blitzz (Oracle DB/Apache Cassandra 'dan)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull; daha büyük veri kümelerini destekler <br/>&bull; bu üçüncü taraf bir araç olduğundan Market 'ten satın alınması ve kullanıcının ortamına yüklenmesi gerekir|

## <a name="other-apis"></a>Diğer API 'Ler
SQL API, Mongo API ve Cassandra API dışındaki API 'Ler için, her API 'nin mevcut ekosistemlerinin her biri tarafından desteklenen çeşitli araçlar vardır. 

**Tablo API’si** 
* [Veri geçiş aracı](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin API 'SI**
* [Graph toplu yürütücü kitaplığı](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Sonraki adımlar

* [.Net](bulk-executor-dot-net.md) ve [Java](bulk-executor-java.md)'daki toplu yürütücü kitaplığını kullanan örnek uygulamaları deneyerek daha fazla bilgi edinin. 
* Toplu yürütücü kitaplığı, Cosmos DB Spark Bağlayıcısı ile tümleşiktir, daha fazla bilgi edinmek için bkz. [Azure Cosmos DB Spark Bağlayıcısı](spark-connector.md) makalesi.  
* Büyük ölçekli geçişlerle ilgili ek yardım için "genel Danışma belgesi" sorun türü ve "büyük (TB +) geçişleri" sorun alt türü altında bir destek bileti açarak Azure Cosmos DB ürün ekibine başvurun.
