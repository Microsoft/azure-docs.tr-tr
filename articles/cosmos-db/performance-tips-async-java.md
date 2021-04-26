---
title: Azure Cosmos DB zaman uyumsuz Java SDK v2 için performans ipuçları
description: Zaman uyumsuz Java SDK v2 için Azure Cosmos veritabanı performansını iyileştirecek istemci yapılandırma seçeneklerini öğrenin
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: bd009ae4909c8cb016a31323294df3a359eb7c51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033672"
---
# <a name="performance-tips-for-azure-cosmos-db-async-java-sdk-v2"></a>Azure Cosmos DB zaman uyumsuz Java SDK v2 için performans ipuçları
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK’sı v4](performance-tips-java-sdk-v4-sql.md)
> * [Zaman uyumsuz Java SDK v2](performance-tips-async-java.md)
> * [Zaman uyumlu Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)


> [!IMPORTANT]  
> Bu, Azure Cosmos DB için en son Java SDK 'Sı *değildir* ! Projenizi [Java SDK v4 Azure Cosmos DB](sql-api-sdk-java-v4.md) yükseltmeniz ve ardından Azure Cosmos DB Java SDK v4 [performansı ipuçları kılavuzunu](performance-tips-java-sdk-v4-sql.md)okumanız gerekir. Yükseltmek için [Azure Cosmos DB Java SDK 'sı v4](migrate-java-v4-sdk.md) Kılavuzu ve [reaktör vs rxjava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) kılavuzundaki yönergeleri izleyin. 
> 
> Bu makaledeki performans ipuçları yalnızca Azure Cosmos DB zaman uyumsuz Java SDK v2 içindir. Daha fazla bilgi için bkz. Azure Cosmos DB zaman uyumsuz Java SDK v2 [sürüm notları](sql-api-sdk-async-java.md), [Maven deposu](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)ve Azure Cosmos DB zaman uyumsuz Java SDK v2 [sorun giderme kılavuzu](troubleshoot-java-async-sdk.md) .
>

Azure Cosmos DB, garantili gecikme ve verimlilik ile sorunsuz bir şekilde ölçeklenen hızlı ve esnek bir dağıtılmış veritabanıdır. Veritabanınızı Azure Cosmos DB ölçeklendirmek için önemli mimari değişiklikler yapmanız veya karmaşık kod yazmanız gerekmez. Ölçeği artırma ve azaltma, tek bir API çağrısı veya SDK Yöntem çağrısı yapmak kadar kolaydır. Ancak, Azure Cosmos DB ağ çağrılarıyla erişildiği için, [Azure Cosmos DB zaman uyumsuz Java SDK v2](sql-api-sdk-async-java.md)'yi kullanırken en yüksek performans elde etmenizi sağlayabilirsiniz.

Bu nedenle "veritabanı performanmy nasıl iyileştirebilirim?" diye soruyoruz Aşağıdaki seçenekleri göz önünde bulundurun:

## <a name="networking"></a>Ağ

* **Bağlantı modu: doğrudan modu kullan**
    
  İstemcinin Azure Cosmos DB 'e bağlanması, özellikle de istemci tarafı gecikme süresi bakımından performans açısından önemli etkileri vardır. *Connectionmode* , Istemci *connectionpolicy*'yi yapılandırmak için kullanılabilen bir anahtar yapılandırma ayarıdır. Azure Cosmos DB zaman uyumsuz Java SDK v2 için, kullanılabilir iki Connectionmode şunlardır:  
      
  * [Ağ Geçidi (varsayılan)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
  * [Direct](/java/api/com.microsoft.azure.cosmosdb.connectionmode)
  
  Ağ Geçidi modu tüm SDK platformlarında desteklenir ve varsayılan olarak yapılandırılmış seçenektir. Uygulamalarınız, katı güvenlik duvarı kısıtlamalarına sahip bir kurumsal ağ içinde çalışıyorsa, standart HTTPS bağlantı noktasını ve tek bir uç noktayı kullandığından, ağ geçidi modu en iyi seçimdir.   Ancak performans zorunluluğunu getirir, ağ geçidi modunun, verilerin her okunduğu veya Azure Cosmos DB yazıldığı her seferinde ek bir ağ atlaması içerir. Bu nedenle, daha az ağ atlaması nedeniyle doğrudan mod daha iyi performans sunar.
  
  *Connectionmode* , *documentclient* örneğinin oluşturulması sırasında *connectionpolicy* parametresiyle yapılandırılır.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-connectionpolicy"></a>Async Java SDK v2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.Direct);
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
```

* **Performans için istemcileri aynı Azure bölgesinde birlikte bulundurun**

  Mümkün olduğunda Azure Cosmos veritabanıyla aynı bölgedeki Azure Cosmos DB çağıran tüm uygulamaları yerleştirin. Yaklaşık bir karşılaştırma için, 1-2 ms içinde aynı bölgedeki Azure Cosmos DB çağrıları tamamlanır, ancak ABD 'nin batı ve Doğu yakası arasındaki gecikme 50 MS >. Bu gecikme süresi büyük olasılıkla istemciden Azure veri merkezi sınırına geçerken istek tarafından alınan yola bağlı olarak istek üzerine farklılık gösterebilir. Olası en düşük gecikme süresi, çağıran uygulamanın sağlanan Azure Cosmos DB uç noktası ile aynı Azure bölgesinde bulunduğundan emin olarak elde edilir. Kullanılabilir bölgelerin listesi için bkz. [Azure bölgeleri](https://azure.microsoft.com/regions/#services).

  :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB bağlantı ilkesinin çizimi" border="false":::

## <a name="sdk-usage"></a>SDK kullanımı

* **En son SDK 'Yı yükler**

  Azure Cosmos DB SDK 'Ları, en iyi performansı sağlamak için sürekli geliştirilmiştir. En son SDK 'yı ve geliştirmeleri gözden geçirmeyi öğrenmek için Azure Cosmos DB zaman uyumsuz Java SDK v2 [sürüm notları](sql-api-sdk-async-java.md) sayfalarına bakın.

* **Uygulamanızın ömrü boyunca tek bir Azure Cosmos DB istemcisi kullanın**

  Her AsyncDocumentClient örneği iş parçacığı açısından güvenlidir ve verimli bağlantı yönetimi ve adres önbelleği gerçekleştirir. AsyncDocumentClient 'ın etkili bağlantı yönetimine ve daha iyi performansa izin vermek için uygulama ömrü boyunca AppDomain başına AsyncDocumentClient ' ın tek bir örneğini kullanmanız önerilir.

* **ConnectionPolicy ayarlama**

  Varsayılan olarak, Azure Cosmos DB zaman uyumsuz Java SDK v2 kullanılırken doğrudan mod Cosmos DB istekleri TCP üzerinden yapılır. Dahili olarak SDK, ağ kaynaklarını dinamik olarak yönetmek ve en iyi performansı elde etmek için özel bir doğrudan mod mimarisi kullanır.

  Azure Cosmos DB zaman uyumsuz Java SDK v2 'de, doğrudan mod, en iyi iş yükleriyle veritabanı performansını geliştirmek için en iyi seçenektir. 

  * ***Doğrudan moda genel bakış***

  :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="Doğrudan mod mimarisinin çizimi" border="false":::
  
  Doğrudan modda çalışan istemci tarafı mimarisi, tahmin edilebilir ağ kullanımı ve Azure Cosmos DB çoğaltmaya çoğullanmış erişim sağlar. Yukarıdaki diyagramda, doğrudan modunun Cosmos DB arka uçtaki çoğaltmalara istemci isteklerini nasıl yönlendirdiğini gösterir. Doğrudan mod mimarisi, VERITABANı çoğaltması başına istemci tarafında en fazla 10 **Kanal** ayırır. Kanal, 30 istekten oluşan bir istek arabelleğinin önünde yer aldığı bir TCP bağlantısıdır. Bir çoğaltmaya ait olan kanallar, çoğaltmanın **hizmet uç noktası** tarafından gerektiği şekilde dinamik olarak ayrılır. Kullanıcı doğrudan modda bir istek verdiği zaman, **Transportclient** , isteği bölüm anahtarına göre uygun hizmet uç noktasına yönlendirir. **Istek kuyruğu** , hizmet uç noktasından önceki istekleri arabelleğe alır.

  * ***Doğrudan mod için ConnectionPolicy yapılandırma seçenekleri***

    İlk adım olarak aşağıdaki önerilen yapılandırma ayarlarını kullanın. Bu konuda sorun yaşıyorsanız lütfen [Azure Cosmos DB ekibine](mailto:CosmosDBPerformanceSupport@service.microsoft.com) başvurun.

    Bir başvuru veritabanı olarak Azure Cosmos DB kullanıyorsanız (diğer bir deyişle, veritabanı birçok nokta okuma işlemi ve birkaç yazma işlemi için kullanılırsa), *ıdtimeout zaman aşımını* 0 (yani zaman aşımı yok) olarak ayarlamak kabul edilebilir.


    | Yapılandırma seçeneği       | Varsayılan    |
    | :------------------:       | :-----:    |
    | bufferPageSize             | 8192       |
    | connectionTimeout          | "PT1M"     |
    | ıdtımechanneltimeout         | "PT0S"     |
    | ıdtımeendpointtimeout        | "PT1M10S"  |
    | maxBufferCapacity          | 8388608    |
    | maxChannelsPerEndpoint     | 10         |
    | maxRequestsPerChannel      | 30         |
    | receiveHangDetectionTime   | "PT1M5S"   |
    | Requestexpiryıınterval      | "PT5S"     |
    | requestTimeout             | "PT1M"     |
    | requestTimerResolution     | "PT 0,5 S"   |
    | sendHangDetectionTime      | "PT10S"    |
    | shutdownTimeout            | "PT15S"    |

* ***Doğrudan mod için programlama ipuçları***

  SDK sorunlarını çözmek için temel olarak Azure Cosmos DB zaman uyumsuz Java SDK v2 [sorun giderme](troubleshoot-java-async-sdk.md) makalesini gözden geçirin.
  
  Doğrudan mod kullanırken bazı önemli programlama ipuçları:
  
  * **ETKIN TCP veri aktarımı için uygulamanızda çoklu iş parçacığı kullanımı** -bir istek yapıldıktan sonra uygulamanız başka bir iş parçacığında veri almak için abone olmalıdır. Bunu yapmamaya devam etmek istenmeyen "yarı çift yönlü" işlemi zorlar ve sonraki istekler önceki isteğin yanıtı beklenirken engellenir.
  
  * **Özel bir iş parçacığında yoğun işlem yoğunluğu olan iş yüklerini gerçekleştirin** ; önceki ipucunun benzer nedenlerle, karmaşık veri işleme gibi işlemler ayrı bir iş parçacığına en iyi şekilde yerleştirilir. Başka bir veri deposundan veri çeken bir istek (örneğin, iş parçacığı Azure Cosmos DB kullanır ve Spark veri depoları aynı anda), daha fazla gecikme yaşar ve diğer veri deposundan bir yanıtı bekleyen ek bir iş parçacığı üretme önerilir.
  
    * Azure Cosmos DB zaman uyumsuz Java SDK v2 'de temel alınan ağ GÇ, netty tarafından yönetilir, [NETTY GÇ iş parçacıklarını engelleyen kodlama desenlerini önlemeye yönelik bu ipuçlarına](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread)bakın.
  
  * **Veri modellemesi** -Azure Cosmos DB SLA 'sı belge boyutunu 1kb 'tan küçük olacak şekilde kabul eder. Veri modelinizi ve programlamanın daha küçük belge boyutunu tercih etmek için en iyi duruma getirilmesi genellikle azaltılmış gecikmeye neden olur. 1 KB 'den büyük belgeleri depolamaya ve almaya ihtiyaç duyecekseniz, belgelerin Azure Blob depolama alanındaki verilere bağlanması için önerilen yaklaşım önerilir.

* **Bölümlenmiş koleksiyonlar için Paralel sorguları ayarlama**

  Azure Cosmos DB Async Java SDK v2 Paralel sorguları destekler ve bu, bölümlenmiş bir koleksiyonu paralel olarak sorgulamanızı sağlar. Daha fazla bilgi için bkz. SDK 'lar ile çalışma ile ilgili [kod örnekleri](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) . Paralel sorgular, kendi seri karşılarındaki sorgu gecikmesini ve aktarım hızını artırmak için tasarlanmıştır.

  * ***Setmaxdegreeofparalellik ayarlama\:***
    
    Paralel sorgular birden çok bölümü paralel olarak sorgulayarak çalışır. Ancak, tek bir bölümlenmiş koleksiyondaki veriler, sorguya göre işlem içine alınır. Bu nedenle, en iyi performansı elde etmek için en yüksek performansa sahip bölüm sayısını ayarlamak için Setmaxdegreeofparalellik kullanın, diğer tüm sistem koşulları aynı kalır. Bölüm sayısını bilmiyorsanız, yüksek bir sayı ayarlamak için Setmaxdegreeofparalellik kullanabilirsiniz ve sistem en az paralellik derecesi olarak en düşük (bölüm sayısı, Kullanıcı tarafından girilen giriş) değerini seçer.

    Verilerin sorguya göre tüm bölümler arasında eşit bir şekilde dağıtılması halinde paralel sorguların en iyi avantajları ürettiğine dikkat edin. Bölümlenmiş koleksiyon, bir sorgu tarafından döndürülen verilerin tümünün veya çoğunluğunun birkaç bölümde (en kötü durumda bir bölüm) yoğunlaşarak bir şekilde bölümlenmişse, sorgunun performansı bu bölümler tarafından bottlenecked olacaktır.

  * ***SetMaxBufferedItemCount ayarlama\:***
    
    Paralel sorgu, geçerli sonuç toplu işi istemci tarafından işlendiği sırada sonuçları önceden getirmek üzere tasarlanmıştır. Önceden getirme, bir sorgunun genel gecikme artışında yardımcı olur. setMaxBufferedItemCount, önceden getirilen sonuçların sayısını sınırlar. SetMaxBufferedItemCount değeri döndürülen beklenen sonuç sayısına (veya daha yüksek bir sayıya) ayarlandığında sorgunun ön alma işleminden en fazla avantaj almasına olanak sağlar.

    Önceden getirme, Maxdegreeofparalelliği ne olursa olsun aynı şekilde çalışıyor ve tüm bölümlerdeki veriler için tek bir arabellek vardır.

* **GetRetryAfterInMilliseconds aralıklarında geri alma Uygula**

  Performans testi sırasında, küçük bir istek hızı kısıtlanana kadar yükü artırmanız gerekir. Kısıtlanmamışsa, istemci uygulamasının sunucu tarafından belirtilen yeniden deneme aralığı için geri kapatması gerekir. Geri alma işleminin en düşük olması, yeniden denemeler arasında bekleyen minimum süreyi harcamanızı sağlar.

* **İstemcinizi genişletme-iş yükü**

  Yüksek aktarım hızı düzeylerinde (>50.000 RU/sn) test ediyorsanız, istemci uygulaması makine CPU veya ağ kullanımında kullanıma hazır hale geldiği için performans sorunlarına neden olabilir. Bu noktaya ulaştığınızda, istemci uygulamalarınızı birden çok sunucu arasında ölçeklendirerek Azure Cosmos DB hesabını daha fazla göndermeye devam edebilirsiniz.

* **Ad tabanlı adresleme kullan**

  Bağlantıların, `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` \_ `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` bağlantıyı oluşturmak için kullanılan tüm kaynakların RESOURCEID 'lerini almaktan kaçınmak için biçimi olan, kendi kendini bağlayan, (self) değil, ad tabanlı adresleme kullanın. Ayrıca, bu kaynaklar yeniden oluşturulur (muhtemelen aynı ada sahip olabilir), önbelleğe alma işlemi yardımcı olmayabilir.

* **Daha iyi performans için, sorguların/okunan akışların sayfa boyutunu ayarlayın**

  Belgeleri okuma akışı işlevselliğini (örneğin, readDocuments) kullanarak veya bir SQL sorgusu verirken belge okuma işlemi gerçekleştirirken, sonuç kümesi çok büyükse sonuçlar parçalı olarak döndürülür. Varsayılan olarak, sonuçlar 100 öğe veya 1 MB Öbekle döndürülür, bu sınır ilk önce dönüştürülür.

  Tüm geçerli sonuçları almak için gereken ağ gidiş dönüşlerin sayısını azaltmak için, [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) istek üst bilgisini 1000 'e kadar kullanarak sayfa boyutunu artırabilirsiniz. Örneğin, Kullanıcı arabiriminiz veya uygulama API 'niz bir kez yalnızca 10 sonuç döndürürse, okuma ve sorgular için tüketilen aktarım hızını azaltmak için sayfa boyutunu 10 ' a da azaltabilirsiniz.

  Ayrıca, Setmaxıtemcount metodunu kullanarak sayfa boyutunu da ayarlayabilirsiniz.

* **Uygun zamanlayıcıyı kullan (olay döngüsü GÇ ağ parçacıklarının çalınmasını önleyin)**

  Azure Cosmos DB Async Java SDK v2, engellenmeyen GÇ için [Netty](https://netty.io/) kullanır. SDK, GÇ işlemlerini yürütmek için sabit sayıda GÇ Netty olay döngüsü iş parçacığını (makinenizin sahip olduğu CPU çekirdekleri) kullanır. API tarafından döndürülen observable, paylaşılan GÇ olay döngüsü Netty iş parçacıklarından birindeki sonucu yayar. Bu nedenle, paylaşılan GÇ olay döngüsü Netty iş parçacıklarının engellenmemesi önemlidir. GÇ olay döngüsü Netty iş parçacığı üzerinde CPU yoğun iş veya engelleme işlemi yapıldığında kilitlenme olabilir veya SDK verimlilik önemli ölçüde azalabilir.

  Örneğin, aşağıdaki kod olay döngüsü GÇ Netty iş parçacığı üzerinde yoğun bir CPU kullanımı işi yürütür:

  **Async Java SDK v2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)**

  ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
  ```

  Sonuç alındıktan sonra, CPU yoğun işi gerçekleştirmek istiyorsanız olay döngüsü GÇ ağ parçacığında bunu yapmaktan kaçınmalısınız. Bunun yerine, işinizi çalıştırmak için kendi iş parçacığını sağlamak üzere kendi Scheduler 'ı sağlayabilirsiniz.

  **Async Java SDK v2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)**

  ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
  ```

  Çalışmanızın türüne göre, çalışmanız için mevcut olan RxJava Scheduler 'ı kullanmanız gerekir. Buradan okuyun [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html) .

  Daha fazla bilgi için lütfen Azure Cosmos DB zaman uyumsuz Java SDK v2 için [GitHub sayfasına](https://github.com/Azure/azure-cosmosdb-java) bakın.

* **Netty 'ın günlüğünü devre dışı bırak**

    Netty kitaplığı günlüğü geveze ve ek CPU maliyetlerinden kaçınmak için kapalı olması gerekir (yapılandırmada oturum açmayı gizleme yeterli olmayabilir). Hata ayıklama modunda değilseniz, netty 'ın günlüğünü tamamen devre dışı bırakın. Bu nedenle, netty 'den kaynaklanan ek CPU maliyetlerini kaldırmak için Log4J kullanıyorsanız ``org.apache.log4j.Category.callAppenders()`` , kod tabanınıza aşağıdaki satırı ekleyin:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **İşletim sistemi açık dosyaları kaynak sınırı**
 
    Bazı Linux sistemleri (Red hat gibi), açık dosya sayısı için üst sınıra ve bu nedenle toplam bağlantı sayısına sahiptir. Geçerli sınırları görüntülemek için aşağıdakileri çalıştırın:

    ```bash
    ulimit -a
    ```

    Açık dosya (nofile) sayısı, yapılandırılmış bağlantı havuzu boyutunuz ve işletim sistemi tarafından diğer açık dosyalar için yeterli yere yetecek kadar büyük olmalıdır. Daha büyük bir bağlantı havuzu boyutu için izin verecek şekilde değiştirilebilir.

    Limit. conf dosyasını açın:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Aşağıdaki satırları ekleyin/değiştirin:

    ```
    * - nofile 100000
    ```

## <a name="indexing-policy"></a>Dizin Oluşturma İlkesi
 
* **Daha hızlı yazma işlemleri için, kullanılmayan yolları dizin oluşturmanın dışında bırakma**

    Azure Cosmos DB Dizin oluşturma ilkesi, dizin oluşturma yollarını (Setıncludedpaths ve setExcludedPaths) kullanarak hangi belge yollarının dizine eklenmesini veya dışlanacağını belirtmenize olanak tanır. Dizin oluşturma yollarının kullanımı, sorgu desenlerinin önceden bildiği senaryolar için gelişmiş yazma performansı ve daha düşük dizin depolaması sunabilir, dizin oluşturma maliyetleri doğrudan dizinli benzersiz yolların sayısıyla bağıntılı olur. Örneğin, aşağıdaki kod, "*" joker karakterini kullanarak, belgelerin tamamının (alt ağaç olarak da bilinir) bir bölümünün tamamını nasıl dışlayacak olduğunu gösterir.

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-indexing"></a>Async Java SDK v2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Daha fazla bilgi için bkz. [Azure Cosmos DB Dizin oluşturma ilkeleri](./index-policy.md).

## <a name="throughput"></a><a id="measure-rus"></a>Aktarım hızı

* **Düşük istek birimleri/ikinci kullanım için ölçme ve ayarlama**

    Azure Cosmos DB, UDF 'ler, saklı yordamlar ve tetikleyicilerle ilişkisel ve hiyerarşik sorgular dahil olmak üzere zengin bir veritabanı işlemleri kümesi sunar ve bunların tümü bir veritabanı koleksiyonu içindeki belgeler üzerinde çalışıyor. Bu işlemlerden her biriyle ilişkilendirilmiş maliyet, işlemi tamamlamak için gereken CPU, GÇ ve belleğe göre değişiklik gösterir. Donanım kaynaklarını düşünmek ve yönetmek yerine, çeşitli veritabanı işlemlerini gerçekleştirmek ve bir uygulama isteğine hizmet vermek için gereken kaynaklar için bir istek birimi (RU) tek bir ölçü olarak düşünebilirsiniz.

    Aktarım hızı, her bir kapsayıcı için ayarlanan [istek birimi](request-units.md) sayısına göre sağlanır. İstek birimi tüketimi, saniye başına bir hız olarak değerlendirilir. Kapsayıcı için sağlanan istek birimi oranını aşan uygulamalar, oran için sağlanan düzeyin altına düşene kadar sınırlandırılır. Uygulamanız daha yüksek bir işleme düzeyi gerektiriyorsa, ek istek birimleri sağlayarak aktarım hızını artırabilirsiniz.

    Bir sorgunun karmaşıklığı, bir işlem için kaç istek biriminin tüketildiğini etkiler. Koşulların sayısı, koşulların doğası, UDF sayısı ve kaynak veri kümesinin boyutu, sorgu işlemlerinin maliyetini etkiler.

    Herhangi bir işlemin (oluşturma, güncelleştirme veya silme) yükünü ölçmek için, bu işlemler tarafından tüketilen istek birimi sayısını ölçmek üzere [x-MS-Request-şarj](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) üst bilgisini inceleyin. Ayrıca, Resourceres, FeedResponse içindeki eşdeğer Requestücretözelliğine de bakabilirsiniz \<T> \<T> .

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-requestcharge"></a>Async Java SDK v2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Bu üst bilgide döndürülen istek ücreti, sağlanan aktarım hızının bir bölümü. Örneğin, sağlanan 2000 RU/sn varsa ve önceki sorgu 1000 1KB-belgeler döndürürse, işlem maliyeti 1000 ' dir. Bu nedenle, bir saniye içinde sunucu, sonraki istekleri sınırlayan orandan önce yalnızca iki istek için geçerlidir. Daha fazla bilgi için bkz. [İstek birimleri](request-units.md) ve [istek birimi Hesaplayıcısı](https://www.documentdb.com/capacityplanner).

* **Tanıtıcı hız sınırlandırma/istek hızı çok büyük**

    Bir istemci bir hesap için ayrılan aktarım hızını aşmaya çalıştığında, sunucuda bir performans düşüşü olmaz ve ayrılan düzeyin ötesinde üretilen iş kapasitesi yoktur. Sunucu, isteği RequestRateTooLarge (HTTP durum kodu 429) ile sona erpreemptively ve kullanıcının isteği yeniden denemeden önce beklemesi gereken süreyi milisaniye olarak belirten [x-MS-retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) üst bilgisini döndürür.

   ```xml
   HTTP Status 429,
   Status Line: RequestRateTooLarge
   x-ms-retry-after-ms :100
   ```

    SDK 'lar, sunucu tarafından belirtilen yeniden deneme üst bilgisine göre bu yanıtı dolaylı olarak yakalar ve isteği yeniden dener. Hesabınız birden çok istemci tarafından aynı anda erişilmediği takdirde, sonraki yeniden deneme başarılı olur.

    İstek hızının sürekli olarak birden fazla istemciniz varsa, bu, şu anda istemci tarafından dahili olarak 9 olarak ayarlanmış olan varsayılan yeniden deneme sayısı çalışmayabilir; Bu durumda, istemci uygulamaya 429 durum kodu ile bir DocumentClientException oluşturur. Varsayılan yeniden deneme sayısı, ConnectionPolicy örneğinde setRetryOptions kullanılarak değiştirilebilir. Varsayılan olarak, durum kodu 429 olan DocumentClientException, istek istek hızının üzerinde çalışmaya devam ederse, 30 saniyelik birikimli bir bekleme süresi dolduktan sonra döndürülür. Bu durum, geçerli yeniden deneme sayısı en fazla yeniden deneme sayısından az olduğunda bile, varsayılan olarak 9 veya Kullanıcı tanımlı bir değer olmalıdır.

    Otomatik yeniden deneme davranışı, çoğu uygulama için esneklik ve kullanılabilirliği artırmaya yardımcı olmakla birlikte, özellikle gecikmeyi ölçirken performans kıyaslamaları yapılırken gürültü 'ye gelebilir. Deneme sunucu tarafından azaldıysanız, istemci SDK 'sının sessizce yeniden denenmesine neden olursa istemci gözlenen gecikme süresi izlenir. Performans denemeleri sırasında gecikme sürelerini önlemek için, her bir işlem tarafından döndürülen ücreti ölçün ve isteklerin ayrılan istek oranının altında çalıştığından emin olun. Daha fazla bilgi için bkz. [İstek birimleri](request-units.md).

* **Daha yüksek aktarım hızı için daha küçük dosyalar tasarlayın**

    Belirli bir işlemin istek ücreti (istek işleme maliyeti), belgenin boyutuyla doğrudan bağıntılı olur. Büyük belgelerle ilgili işlemler, küçük belgeler için işlemlerden daha fazla maliyetlidir.

## <a name="next-steps"></a>Sonraki adımlar

Uygulamanızı ölçek ve yüksek performans için tasarlama hakkında daha fazla bilgi için, bkz. [Azure Cosmos DB bölümleme ve ölçeklendirme](partitioning-overview.md).