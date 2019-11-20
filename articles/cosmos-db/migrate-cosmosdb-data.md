---
title: Azure Cosmos DB’ye yüzlerce terabaytlık verileri geçirme
description: Bu belge, Cosmos DB terabaytlık verileri nasıl geçirebileceğinizi açıklar
author: bharathsreenivas
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 69b400eb7838c986ac6f275da58c7457179ebea6
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880214"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Azure Cosmos DB’ye yüzlerce terabaytlık verileri geçirme 

Azure Cosmos DB terabaytlarca veriyi depolayabilir. Üretim iş yükünüzü Azure Cosmos DB’ye taşımak için büyük ölçekli bir veri geçişi yapabilirsiniz. Bu makalede büyük ölçekli verileri Azure Cosmos DB taşımaya ve sorunları gidermenize yardımcı olan ve Azure Cosmos DB verileri geçirmede size yardımcı olan araç hakkında sorunlar açıklanmaktadır. Bu durumda, müşteri SQL API Cosmos DB kullandı.  

İş yükünün tamamını Azure Cosmos DB geçirmeden önce bölüm anahtarı seçimi, sorgu performansı ve veri modelleme gibi bazı yönleri doğrulamak üzere bir veri alt kümesini geçirebilirsiniz. Kavram kanıtı 'nı doğruladıktan sonra, tüm iş yükünü Azure Cosmos DB taşıyabilirsiniz.  

## <a name="tools-for-data-migration"></a>Veri geçiş araçları 

Azure Cosmos DB geçiş stratejileri Şu anda API seçimine ve verilerin boyutuna göre farklılık gösterir. Daha küçük veri kümelerini geçirmek için – veri modelleme, sorgu performansı, bölüm anahtarı seçimi vb. için [veri geçiş aracını](import-data.md) veya [Azure Data Factory Azure Cosmos DB bağlayıcısını](../data-factory/connector-azure-cosmos-db.md)seçebilirsiniz. Spark hakkında bilginiz varsa, verileri geçirmek için [Azure Cosmos DB Spark bağlayıcısını](spark-connector.md) kullanmayı da tercih edebilirsiniz.

## <a name="challenges-for-large-scale-migrations"></a>Büyük ölçekli geçişlere yönelik sorunlar 

Azure Cosmos DB veri taşımaya yönelik mevcut araçlar, özellikle büyük ölçeklerde görünen bazı sınırlamalara sahiptir:

 * **Sınırlı ölçek genişletme özellikleri**: terabaytlarca veri Azure Cosmos DB, mümkün olduğunca hızlı bir şekilde geçiş yapmak ve sağlanan tüm üretilen iş verimini etkin bir şekilde kullanmak için, geçiş istemcilerinin sonsuza kadar ölçeklendirme yeteneği olmalıdır.  

* **İlerleme izlemenin ve onay durumunun olmaması**: geçiş işleminin ilerlemesini izlemek ve büyük veri kümelerini geçirirken denetim noktası sağlamak önemlidir. Aksi takdirde, geçiş sırasında oluşan herhangi bir hata geçişi durdurur ve işlemi sıfırdan başlatmanız gerekir. %99 zaten tamamlandığında geçiş işleminin tamamını yeniden başlatmak üretken değildir.  

* **Sahipsiz sıra olmaması**durumunda: büyük veri kümeleri içinde, bazı durumlarda kaynak verilerin bölümleriyle ilgili sorunlar olabilir. Ayrıca, istemci veya ağla ilgili geçici sorunlar da olabilir. Bu durumların herhangi biri, geçişin tamamının başarısız olmasına neden olmamalıdır. Çoğu geçiş aracının aralıklı sorunlara karşı koruma sağlayan güçlü yeniden deneme özellikleri olsa da, her zaman yeterince olmaz. Örneğin, kaynak veri belgelerinin% 0,01 ' sinden azı boyutu 2 MB 'tan fazlaysa, Azure Cosmos DB belge yazma işlemi başarısız olur. En ideal olarak, geçiş aracının bu ' başarısız ' belgelerini başka bir atılacak ileti kuyruğuna kalıcı hale getirmek için, geçiş sonrası işlenebilen yararlı bir seçenektir. 

Azure Data Factory, Azure Data Migration hizmetleri gibi araçlar için bu sınırlamaların birçoğu düzeltilmekte. 

## <a name="custom-tool-with-bulk-executor-library"></a>Toplu yürütücü kitaplığı ile özel araç 

Yukarıdaki bölümde açıklanan zorluklar, birden çok örneğe kolayca ölçeklenebilen ve geçici hatalara dayanıklı olan özel bir araç kullanılarak çözülebilir. Ayrıca, özel araç çeşitli denetim noktalarında geçişi duraklatabilir ve devam edebilir. Azure Cosmos DB, bu özelliklerden bazılarını içeren [toplu yürütücü kitaplığını](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) zaten sağlıyor. Örneğin, toplu yürütücü kitaplığı geçici hataları işlemeye yönelik işlevlere zaten sahiptir ve düğüm başına 500 K ru 'yi kullanmak için tek bir düğümdeki iş parçacıklarını ölçeklendirebilirler. Toplu yürütücü kitaplığı ayrıca kaynak veri kümesini, bir denetim noktası olarak bağımsız olarak çalıştırılan mikro toplu işlemlere da bölümler.  

Özel araç, toplu yürütücü kitaplığını kullanır ve birden çok istemcide ölçeklendirmeyi destekler ve alma işlemi sırasında hataları izler. Bu aracı kullanmak için, kaynak verilerin Azure Data Lake Storage (ADLS) içindeki farklı dosyalara bölümlenmesi gerekir, böylece farklı geçiş çalışanları her dosyayı seçip Azure Cosmos DB içine alabilir. Özel araç, ADLS içindeki her bir kaynak dosya için geçiş ilerlemesiyle ilgili meta verileri depolayan ayrı bir koleksiyon kullanır ve bunlarla ilişkili tüm hataları izler.  

Aşağıdaki görüntüde, bu özel aracı kullanan geçiş işlemi açıklanmaktadır. Araç bir sanal makine kümesi üzerinde çalışıyor ve her sanal makine, kaynak veri bölümlerinden birinde kira almak için Azure Cosmos DB 'de izleme koleksiyonunu sorgular. Bu işlem yapıldıktan sonra, kaynak veri bölümü araç tarafından okunarak toplu yürütücü kitaplığı kullanılarak Azure Cosmos DB alınır. Daha sonra, izleme koleksiyonu veri alımı ilerlemesini ve karşılaşılan hatalarla karşılaştıracak şekilde güncelleştirilir. Bir veri bölümü işlendikten sonra araç, bir sonraki kullanılabilir kaynak bölümü sorgulamaya çalışır. Tüm veriler geçirilene kadar sonraki kaynak bölümü işlemeye devam eder. Araç için kaynak kodu [buradan](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion)edinebilirsiniz.  

 
![Geçiş Aracı Kurulumu](./media/migrate-cosmosdb-data/migrationsetup.png)
 

 

İzleme koleksiyonu, aşağıdaki örnekte gösterildiği gibi belgeler içerir. Bu tür belgeleri kaynak verilerdeki her bölüm için bir tane görürsünüz.  Her belge, kaynak veri bölümünün konumu, geçiş durumu ve hataları (varsa) gibi meta verileri içerir:  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>Veri taşıma önkoşulları 

Veri geçişi başlamadan önce göz önünde bulundurmanız gereken birkaç önkoşul vardır:  

#### <a name="estimate-the-data-size"></a>Veri boyutunu tahmin edin:  

Kaynak veri boyutu, Azure Cosmos DB veri boyutuyla tam olarak eşlenmeyebilir. Verilerin boyutunu Azure Cosmos DB denetlemek için kaynaktan birkaç örnek belge eklenebilir. Örnek belge boyutuna bağlı olarak, geçiş sonrası Azure Cosmos DB toplam veri boyutu tahmin edilebilir. 

Örneğin, Azure Cosmos DB geçiş sonrasında her belge 1 KB 'ın etrafında ve kaynak veri kümesinde 60.000.000.000 belge etrafında varsa, Azure Cosmos DB içindeki tahmini boyutun 60 TB 'a yakın olması anlamına gelir. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Yeterli ru ile kapsayıcıları önceden oluşturun: 

Azure Cosmos DB depolamayı otomatik olarak ölçeklendirse de en küçük kapsayıcı boyutundan başlamak önerilmez. Daha küçük kapsayıcılar düşük verimlilik kullanılabilirliğine sahiptir, bu da geçişin tamamlanması çok daha uzun sürer. Bunun yerine, kapsayıcıları son veri boyutuyla (önceki adımda tahmini olarak) oluşturmak ve geçiş iş yükünün sağlanan aktarım hızını tam olarak kullandığından emin olmak yararlıdır.  

Bir önceki adımda. veri boyutunun 60 TB 'lık bir yerinde olması tahmin edildiğinden, tüm veri kümesinin tamamına uyum sağlaması için en az 2,4 e ru kapsayıcısının bir kapsayıcısı gerekir.  

 

#### <a name="estimate-the-migration-speed"></a>Geçiş hızını tahmin edin: 

Geçiş iş yükünün sağlanan tüm üretilen işi tükettiği kabul edildiğinde, sağlanan genelinde geçiş hızının bir tahmini sağlanır. Önceki örneğe devam edildiğinde, 1 KB 'lık bir belgeyi Azure Cosmos DB SQL API hesabına yazmak için 5 ru gerekir.  2.400.000 ru, saniye başına 480.000 belge aktarımına (veya 480 MB/sn) izin verebilir. Bu, 60 TB 'ın tüm geçişinin 125.000 saniye veya yaklaşık 34 saat süreleceği anlamına gelir.  

Geçişin bir gün içinde tamamlanmasını istemeniz durumunda, sağlanan aktarım hızını 5.000.000 ru olarak artırmanız gerekir. 

 

#### <a name="turn-off-the-indexing"></a>Dizinlemeyi kapat:  

Geçişin mümkün olan en kısa sürede tamamlanması gerektiğinden, alınan her belge için dizin oluştururken harcanan zamanı ve ru 'yi en aza indirmek önerilir.  Tüm özellikleri otomatik olarak dizine Azure Cosmos DB, seçilen birkaç terim için dizin oluşturmayı en aza indirmek veya geçiş işlemi için tamamen devre dışı bırakmak çok önemlidir. Dizin oluşturma ilkesini aşağıda gösterildiği gibi ındexingmode değerini None olarak değiştirerek devre dışı bırakabilirsiniz:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

Geçiş işlemi tamamlandıktan sonra, dizin oluşturmayı güncelleştirebilirsiniz.  

## <a name="migration-process"></a>Geçiş süreci 

Önkoşullar tamamlandıktan sonra, aşağıdaki adımlarla veri geçirebilirsiniz:  

1. İlk olarak verileri kaynaktan Azure Blob depolamaya aktarın. Geçişin hızını artırmak için, farklı kaynak bölümleri genelinde paralel hale getirmek yararlı olur. Geçişe başlamadan önce, kaynak veri kümesinin 200 MB boyutunda boyuttaki dosyalara bölümlenmesi gerekir.   

2. Toplu yürütücü kitaplığı, tek bir istemci sanal makinesinde 500.000 ru 'yi kullanmak için ölçeklenebilir. Kullanılabilir verimlilik 5.000.000 ru olduğundan, Azure Cosmos veritabanınızın bulunduğu bölgede 10 Ubuntu 16,04 VM (Standard_D32_v3) sağlanmalıdır. Bu VM 'Leri geçiş aracı ve ayarlar dosyası ile hazırlamanız gerekir.  

3. İstemci sanal makinelerinden birinde sıra adımını çalıştırın. Bu adım, ADLS kapsayıcısını tarayan ve kaynak veri kümesinin bölüm dosyalarının her biri için bir ilerleme izleme belgesi oluşturan izleme koleksiyonunu oluşturur.  

4. Sonra, tüm istemci VM 'lerinde içeri aktarma adımını çalıştırın. İstemcilerin her biri, kaynak bölümde sahipliği alabilir ve verilerini Azure Cosmos DB içine alabilir. Tamamlandıktan sonra ve durumu izleme koleksiyonunda güncelleştirildikten sonra, istemciler daha sonra izleme koleksiyonundaki bir sonraki kullanılabilir kaynak bölümünü sorgulayabilir.  

5. Bu işlem, tüm kaynak bölüm kümesine alınana kadar devam eder. Tüm kaynak bölümler işlendikten sonra, araç aynı izleme koleksiyonundaki hata düzeltme modunda yeniden çalıştırmanız gerekir. Bu adım, hatalar nedeniyle yeniden işlenmesi gereken kaynak bölümleri belirlemek için gereklidir.  

6. Bu hatalardan bazıları, kaynak verilerdeki hatalı belgelerden kaynaklanıyor olabilir. Bunlar tanımlanmalıdır ve düzeltilir. Ardından, başarısız olan bölümlerde içeri aktarma adımını yeniden çalıştırmanız gerekir. 

Geçiş tamamlandıktan sonra, Azure Cosmos DB içindeki belge sayısı, kaynak veritabanındaki belge sayısıyla aynı olduğunu doğrulayabilirsiniz. Bu örnekte, Azure Cosmos DB toplam boyut 65 terabayt olarak etkinleştirilir. Geçiş sonrası, dizin oluşturma seçime bağlı olarak açılabilir ve RUs iş yükünün işlemleri için gereken düzeye düşürülemez.

## <a name="contact-the-azure-cosmos-db-team"></a>Azure Cosmos DB ekibine başvurun
Büyük veri kümelerini Azure Cosmos DB başarılı bir şekilde geçirmek için bu kılavuzu takip edebilir, ancak büyük ölçekli geçişler için, veri modellemeyi ve genel mimari incelemesini doğrulamak üzere Azure Cosmos DB ürün ekibine ulaşmanıza önerilir. Veri kümeniz ve iş yükünüze göre, ürün ekibi sizin için geçerli olabilecek diğer performans ve maliyet iyileştirmeleri de önerebilir. Büyük ölçekli geçişlerle yardım için Azure Cosmos DB ekibine başvurmak için, aşağıda gösterildiği gibi "genel danışmanlık" sorun türü ve "büyük (TB +) geçişleri" sorun alt türü altında bir destek bileti açabilirsiniz.

![Geçiş desteği konusu](./media/migrate-cosmosdb-data/supporttopic.png)


## <a name="next-steps"></a>Sonraki adımlar

* [.Net](bulk-executor-dot-net.md) ve [Java](bulk-executor-java.md)'daki toplu yürütücü kitaplığını kullanan örnek uygulamaları deneyerek daha fazla bilgi edinin. 
* Toplu yürütücü kitaplığı, Cosmos DB Spark Bağlayıcısı ile tümleşiktir, daha fazla bilgi edinmek için bkz. [Azure Cosmos DB Spark Bağlayıcısı](spark-connector.md) makalesi.  
* Büyük ölçekli geçişlerle ilgili ek yardım için "genel Danışma belgesi" sorun türü ve "büyük (TB +) geçişleri" sorun alt türü altında bir destek bileti açarak Azure Cosmos DB ürün ekibine başvurun. 
