---
title: Toplu olarak içeri aktarma ve güncelleştirme işlemlerinin Azure Cosmos DB'de toplu Yürütücü Java kitaplığı kullanma
description: Toplu içeri aktarma ve Azure Cosmos DB belgeleri toplu Yürütücü Java kitaplığını kullanarak güncelleştirin.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: ef006e94ee22886f1129c7c9ca31e20503312fe3
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616924"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Azure Cosmos DB veriler üzerinde toplu işlemler gerçekleştirmek için toplu Yürütücü Java kitaplığı kullanma

Bu öğretici, Azure Cosmos DB'nin toplu Yürütücü Java kitaplığı kullanarak içeri aktarma ve Azure Cosmos DB belgeleri güncelleştirmek için yönergeler sağlar. Toplu Yürütücü kitaplığı ve yüksek düzeyde işleme ve depolama yararlanmanıza nasıl yardımcı olduğunu öğrenmek için bkz. [Yürütücü kitaplığına genel bakış toplu](bulk-executor-overview.md) makalesi. Bu öğreticide, rastgele belgeler üreten ve bunlar bir Azure Cosmos kapsayıcısına toplu olarak içeri aktarılan bir Java uygulaması oluşturacaksınız. İçeri aktardıktan sonra toplu bir belge bazı özelliklerini güncelleştirir. 

Şu anda, toplu yürütücü kitaplığı yalnızca Azure Cosmos DB SQL API ve Gremlin API hesapları tarafından desteklenir. Bu makalede, SQL API hesaplarıyla toplu yürütücü Java kitaplığı 'nın nasıl kullanılacağı açıklanır. Toplu Yürütücü .NET kitaplığı ile Gremlin API kullanımı hakkında bilgi edinmek için [Azure Cosmos DB Gremlin API'SİNDE toplu işlemler gerçekleştirme](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.  

* Azure aboneliği olmadan [ücretsiz Azure Cosmos DB deneyebilir](https://azure.microsoft.com/try/cosmosdb/) , ücretsiz ve taahhütlere sahip olabilirsiniz. Ya da [Azure Cosmos DB öykünücüsünü](https://docs.microsoft.com/azure/cosmos-db/local-emulator) `https://localhost:8081` uç noktayla kullanabilirsiniz. Birincil Anahtar, [Kimlik doğrulama istekleri](local-emulator.md#authenticating-requests) bölümünde sağlanır.  

* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)  
  - Ubuntu’da JDK’yi yüklemek için `apt-get install default-jdk` komutunu çalıştırın.  

  - JAVA_HOME ortam değişkenini JDK’nin yüklü olduğu klasöre işaret edecek şekilde ayarladığınızdan emin olun.

* Bir [Maven](https://maven.apache.org/) ikili arşivi [indirin](https://maven.apache.org/download.cgi) ve [yükleyin](https://maven.apache.org/install.html)  
  
  - Ubuntu’da Maven’i yüklemek için `apt-get install maven` komutunu çalıştırabilirsiniz.

* Java hızlı başlangıç makalesinin [veritabanı hesabı oluştur](create-sql-api-java.md#create-a-database-account) bölümünde açıklanan adımları kullanarak Azure Cosmos DB BIR SQL API hesabı oluşturun.

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Şimdi Github'dan bir örnek Java uygulamasından indirerek kod ile çalışmaya şimdi geçiş. Bu uygulama, Azure Cosmos DB veriler üzerinde toplu işlem gerçekleştirir. Uygulama kopyalamak için bir komut istemi açın, uygulama kopyalayın ve aşağıdaki komutu çalıştırarak istediğiniz dizine gidin:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Kopyalanan deponun iki örnekleri "BulkImport" ve "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor" göreli "bulkupdate" içerir. "BulkImport" uygulama rastgele belgeler oluşturur ve bunları Azure Cosmos DB'ye içeri aktarır. Azure Cosmos DB'de bazı belgeler "bulkupdate" uygulama güncelleştirir. Sonraki bölümlerde, bu örnek uygulamaları kod şimdi gözden uygulayacaksınız. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Azure Cosmos DB için toplu verileri içeri aktar

1. Azure Cosmos DB bağlantı dizelerini bağımsız değişken olarak okuma ve CmdLineConfiguration.java dosyasında tanımlanan değişkenler atanmış.  

2. Sonraki DocumentClient nesnesinin aşağıdaki ifadeleri kullanarak başlatılır:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. DocumentBulkExecutor nesne bekleme süresi ile bir yüksek yeniden deneme değerlerini başlatılır ve istek kısıtlanmış. Ve Tıkanıklık denetimi için yaşam süresi için DocumentBulkExecutor geçirmek için 0 olarak ardından ayarlanırlar.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
   ```

4. Bir Azure Cosmos kapsayıcısına toplu içeri aktarmaya yönelik rastgele belgeler üreten ImportAll API 'sini çağırın. Komut satırı yapılandırmaları CmdLineConfiguration.java dosyası içinde yapılandırabilirsiniz.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   Toplu içeri aktarma API'si belgeleri JSON olarak serileştirilen bir koleksiyonunu kabul eder ve daha fazla ayrıntı için aşağıdaki söz dizimine sahiptir, bkz: [API belgeleri](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   İmportAll yöntemi, aşağıdaki parametreleri kabul eder:
 
   |**Parametre**  |**Açıklama**  |
   |---------|---------|
   |isUpsert    |   Upsert belgelerin etkinleştirmek için bir bayrak. Bir belge ile verilen kimliği zaten var, bunu güncelleştirilir.  |
   |disableAutomaticIdGeneration     |   Kimliği otomatik olarak oluşturulmasını devre dışı bırakmak için bayrak Varsayılan olarak ayarlanmış true.   |
   |maxConcurrencyPerPartitionRange    |  En büyük bölüm anahtar aralığı başına eşzamanlılık derecesini. Varsayılan değer 20'dir.  |

   **Toplu yanıt nesnesi tanımını içeri aktarma** aşağıdaki get yöntemleri toplu içeri aktarma API çağrısının sonucunu içerir:

   |**Parametre**  |**Açıklama**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Toplu olarak sağlanan belgeleri dışında başarıyla içeri aktarıldı belgelerin toplam sayısı, API çağrısı içeri aktarın.      |
   |çift getTotalRequestUnitsConsumed()   |  Toplu tarafından tüketilen toplam istek birimi (RU) API çağrısı içeri aktarın.       |
   |Süre getTotalTimeTaken()   |    Toplu olarak içeri aktarma tarafından API çağrısı, yürütme tamamlamak için geçen toplam süre.     |
   |>\<GetErrors () özel durumunu listeleyin |  Eklenen başarısız oldu. API çağrısı bazı belgelerin toplu olarak sağlanan Toplu içe aktarırsanız, hataların listesini alır.       |
   |Getbadınputdocuments () > nesne listesi\<  |    API çağrısı başarıyla toplu olarak içeri aktarılamadı, bozuk biçimli belgelerin listesini içeri aktarın. Kullanıcı, döndürülen belgelerin düzeltin ve içeri aktarmayı yeniden deneyin. Hatalı biçimlendirilmiş belgeleri kimliği değeri (null ya da başka herhangi bir veri türü geçersiz olarak kabul edilir) bir dize değil belgeleri içerir.     |

5. Uygulama hazır alma toplu sonra kaynak komut satırı aracını 'mvn temiz paket' komutunu kullanarak oluşturun. Bu komut, hedef klasörde bir jar dosyasını oluşturur:  

   ```java
   mvn clean package
   ```

6. Hedef bağımlılıklar oluşturulduktan sonra aşağıdaki komutu kullanarak toplu içeri Aktarıcı uygulama çağırabilirsiniz:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Toplu içeri Aktarıcı, veritabanı adı, koleksiyon adı ve aktarım hızı değerleri App.config dosyasında belirtilen ile yeni bir veritabanı ve koleksiyonu oluşturur. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Azure Cosmos DB'de toplu güncelleştirme verileri

Varolan belgeleri BulkUpdateAsync API'sini kullanarak güncelleştirebilirsiniz. Bu örnekte, ad alanına yeni bir değere ayarlayın ve açıklama alanı mevcut kaldırma. Desteklenen alan kümesini güncelleştirme işlemleri, bkz: [API belgeleri](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Karşılık gelen alan güncelleştirme işlemlerinin yanı sıra güncelleştirme öğeleri tanımlar. Bu örnekte ad alanı ve açıklama alanı tüm belgelerden kaldırma UnsetUpdateOperation güncelleştirilecek SetUpdateOperation kullanır. Diğer artış gibi bir belge alan belirli bir değere göre işlemleri, belirli değerleri bir dizi alanındaki anında iletme veya belirli bir değer bir dizi alanından kaldırın. Toplu güncelleştirme API'sı tarafından sağlanan farklı yöntemleri hakkında bilgi edinmek için bkz. [API belgeleri](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                      
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Daha sonra bir Azure Cosmos kapsayıcısına toplu olarak içeri aktarılacak rastgele belgeler üreten updateAll API 'yi çağırın. CmdLineConfiguration.java dosyasında geçirilecek komut satırı yapılandırmaları yapılandırabilirsiniz.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   Toplu güncelleştirme API'sı, güncelleştirilecek öğe koleksiyonu kabul eder. Her güncelleştirme öğesi kimliği ve bölüm anahtarı değeri tarafından tanımlanan belge üzerinde gerçekleştirilecek alan güncelleştirme işlemlerin listesini belirtir. Daha fazla ayrıntı için [API belgeleri](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   UpdateAll yöntemi, aşağıdaki parametreleri kabul eder:

   |**Parametre** |**Açıklama** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  En büyük bölüm anahtar aralığı başına eşzamanlılık derecesini. Varsayılan değer 20'dir.  |
 
   **Toplu yanıt nesnesi tanımını içeri aktarma** aşağıdaki get yöntemleri toplu içeri aktarma API çağrısının sonucunu içerir:

   |**Parametre** |**Açıklama**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Toplu güncelleştirme API çağrısına sağlanan belgeleri dışında başarıyla güncelleştirildi belge toplam sayısı.      |
   |çift getTotalRequestUnitsConsumed() |  Toplu güncelleştirme API çağrısı tarafından tüketilen toplam istek birimi (RU).       |
   |Süre getTotalTimeTaken()  |   Yürütme tamamlanması API çağrısı tarafından toplu geçen toplam süreyi güncelleştirin.      |
   |>\<GetErrors () özel durumunu listeleyin   |    Bazı belgeleri toplu iş dışında toplu güncelleştirme API çağrısı eklenen edilemedi sağlandıysa hataların listesini alır.      |

3. Uygulama hazır güncelleştirme toplu sonra kaynak komut satırı aracını 'mvn temiz paket' komutunu kullanarak oluşturun. Bu komut, hedef klasörde bir jar dosyasını oluşturur:  

   ```
   mvn clean package
   ```

4. Hedef bağımlılıklar oluşturulduktan sonra aşağıdaki komutu kullanarak uygulamayı toplu güncelleştirme çağırabilirsiniz:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Performans ipuçları 

Toplu Yürütücü Kitaplığı kullanıldığında daha iyi performans için aşağıdaki noktaları göz önünde bulundurun:

* En iyi performans için Cosmos DB hesabı yazma bölgenizi aynı bölgede bir Azure VM'den uygulamanızı çalıştırın.  
* Daha yüksek performans elde etmek için:  

   * JVM'ın yığın boyutu fazla sayıda belge işleme bir bellek sorunu önlemek için yeterince büyük bir sayıya ayarlayın. Yığın boyutu önerilen: en fazla (3GB, 3 * sizeof (toplu olarak geçirilen tüm belgeleri Al API'si tek bir toplu)).  
   * Hangi nedeniyle daha yüksek aktarım hızı ile çok sayıda belgeleri toplu işlemleri gerçekleştirirken erişmenizi sağlayacak bir ön işleme süresi yoktur. 10.000.000 belge almak istiyorsanız, bu nedenle, 10 kez toplu olarak içeri aktarma üzerinde belgelerin 10 toplu her boyutta 1.000.000 çalışan toplu olarak içeri 100 kez belgelerin 100 toplu üzerinde her boyutu 100.000 belgelerin çalışan daha tercih edilir.  

* Belirli bir Azure Cosmos kapsayıcısına karşılık gelen tek bir sanal makine içinde uygulamanın tamamı için tek bir Documentbulkyürütücü nesnesi örneği oluşturmanız önerilir.  

* Bu yana tek bir toplu işlem API yürütme istemcinin CPU ve ağ GÇ büyük bir yığın kullanır. Birden çok görev tarafından dahili olarak UNICODE böyle, yürütülen her toplu işlem API çağrıları, uygulama işlemi içinde birden çok eş zamanlı görevleri UNICODE özen göstermektir. Tek bir sanal makine üzerinde çalışan tek bir toplu işlem API çağrısı tüketen tüm kapsayıcının aktarım hızını kaydedemediği (varsa, kapsayıcının aktarım hızını > 1 milyon RU/sn), eş zamanlı olarak toplu yürütmek için ayrı sanal makineler oluşturmak için tercih edilir API işlem çağrıları.

    
## <a name="next-steps"></a>Sonraki adımlar
* Maven paket ayrıntıları hakkında bilgi edinin ve sürüm notları toplu Yürütücü Java kitaplığı için bkz:[toplu Yürütücü SDK ayrıntıları](sql-api-sdk-bulk-executor-java.md).


