---
title: Zaman uyumsuz Java için performans ipuçları Azure Cosmos DB
description: Azure Cosmos veritabanı performansını geliştirmek için istemci yapılandırma seçeneklerini öğrenin
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 7a470193110fda0bb675e56e17a8f5647ebda5ab
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614975"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Azure Cosmos DB ve zaman uyumsuz Java için performans ipuçları

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB, garantili gecikme ve verimlilik ile sorunsuz bir şekilde ölçeklenen hızlı ve esnek bir dağıtılmış veritabanıdır. Veritabanınızı Azure Cosmos DB ölçeklendirmek için önemli mimari değişiklikler yapmanız veya karmaşık kod yazmanız gerekmez. Ölçeği artırma ve azaltma, tek bir API çağrısı veya SDK Yöntem çağrısı yapmak kadar kolaydır. Ancak, Azure Cosmos DB ağ çağrılarıyla erişildiği için, [SQL zaman uyumsuz Java SDK 'sını](sql-api-sdk-async-java.md)kullanırken en yüksek performans elde etmek için, istemci tarafı iyileştirmeler vardır.

Bu nedenle "veritabanı performanmy nasıl iyileştirebilirim?" diye soruyoruz Aşağıdaki seçenekleri göz önünde bulundurun:

## <a name="networking"></a>Ağ
   <a id="same-region"></a>
1. **Aynı Azure bölgesindeki istemcileri performans için birlikte bulun**

    Mümkün olduğunda Azure Cosmos veritabanıyla aynı bölgedeki Azure Cosmos DB çağıran tüm uygulamaları yerleştirin. Yaklaşık bir karşılaştırma için, 1-2 ms içinde aynı bölgedeki Azure Cosmos DB çağrıları tamamlanır, ancak ABD 'nin batı ve Doğu yakası arasındaki gecikme 50 MS >. Bu gecikme süresi büyük olasılıkla istemciden Azure veri merkezi sınırına geçerken istek tarafından alınan yola bağlı olarak istek üzerine farklılık gösterebilir. Olası en düşük gecikme süresi, çağıran uygulamanın sağlanan Azure Cosmos DB uç noktası ile aynı Azure bölgesinde bulunduğundan emin olarak elde edilir. Kullanılabilir bölgelerin listesi için bkz. [Azure bölgeleri](https://azure.microsoft.com/regions/#services).

    ![Azure Cosmos DB bağlantı ilkesinin çizimi](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>SDK kullanımı
1. **En son SDK 'Yı yükler**

    Azure Cosmos DB SDK 'Ları, en iyi performansı sağlamak için sürekli geliştirilmiştir. En son SDK 'Yı öğrenmek ve geliştirmeleri gözden geçirmek için [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) sayfalarına bakın.
2. **Uygulamanızın ömrü boyunca tek bir Azure Cosmos DB istemcisi kullanın**

    Her AsyncDocumentClient örneği iş parçacığı açısından güvenlidir ve verimli bağlantı yönetimi ve adres önbelleği gerçekleştirir. AsyncDocumentClient 'ın etkili bağlantı yönetimine ve daha iyi performansa izin vermek için uygulama ömrü boyunca AppDomain başına AsyncDocumentClient ' ın tek bir örneğini kullanmanız önerilir.

   <a id="max-connection"></a>

3. **ConnectionPolicy ayarlama**

    Azure Cosmos DB istekler, zaman uyumsuz Java SDK 'Sı kullanılırken HTTPS/REST üzerinden yapılır ve varsayılan en yüksek bağlantı havuzu boyutuna tabi (1000). Bu varsayılan değer kullanım örneklerinin çoğunluğu için ideal olmalıdır. Ancak, çok sayıda bölüm içeren büyük bir koleksiyonunuz varsa, setMaxPoolSize kullanarak en fazla bağlantı havuzu boyutunu daha büyük bir sayıya (deyin, 1500) ayarlayabilirsiniz.

4. **Bölümlenmiş koleksiyonlar için Paralel sorguları ayarlama**

    Azure Cosmos DB SQL zaman uyumsuz Java SDK 'Sı, bölümlenmiş bir koleksiyonu paralel olarak sorgulamanızı sağlayan paralel sorguları destekler. Daha fazla bilgi için bkz. SDK 'lar ile çalışma ile ilgili [kod örnekleri](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) . Paralel sorgular, kendi seri karşılarındaki sorgu gecikmesini ve aktarım hızını artırmak için tasarlanmıştır.

    (a) ***setmaxdegreeofparalellik\:***  Paralel sorguları ayarlama, birden çok bölümü paralel olarak sorgulayarak çalışır. Ancak, tek bir bölümlenmiş koleksiyondaki veriler, sorguya göre işlem içine alınır. Bu nedenle, en iyi performansı elde etmek için en yüksek performansa sahip bölüm sayısını ayarlamak için Setmaxdegreeofparalellik kullanın, diğer tüm sistem koşulları aynı kalır. Bölüm sayısını bilmiyorsanız, yüksek bir sayı ayarlamak için Setmaxdegreeofparalellik kullanabilirsiniz ve sistem en az paralellik derecesi olarak en düşük (bölüm sayısı, Kullanıcı tarafından girilen giriş) değerini seçer.

    Verilerin sorguya göre tüm bölümler arasında eşit bir şekilde dağıtılması halinde paralel sorguların en iyi avantajları ürettiğine dikkat edin. Bölümlenmiş koleksiyon, bir sorgu tarafından döndürülen verilerin tümünün veya çoğunluğunun birkaç bölümde (en kötü durumda bir bölüm) yoğunlaşarak bir şekilde bölümlenmişse, sorgunun performansı bu bölümler tarafından bottlenecked olacaktır.

    (b) ***ayarlama setmaxbuffereditemcount\:***  paralel sorgusu, geçerli sonuç toplu işi istemci tarafından işlenirken sonuçları önceden getirmek üzere tasarlanmıştır. Önceden getirme, bir sorgunun genel gecikme artışında yardımcı olur. setMaxBufferedItemCount, önceden getirilen sonuçların sayısını sınırlar. SetMaxBufferedItemCount değeri döndürülen beklenen sonuç sayısına (veya daha yüksek bir sayıya) ayarlandığında sorgunun ön alma işleminden en fazla avantaj almasına olanak sağlar.

    Önceden getirme, Maxdegreeofparalelliği ne olursa olsun aynı şekilde çalışıyor ve tüm bölümlerdeki veriler için tek bir arabellek vardır.

5. **GetRetryAfterInMilliseconds aralıklarında geri alma Uygula**

    Performans testi sırasında, küçük bir istek hızı kısıtlanana kadar yükü artırmanız gerekir. Kısıtlanmamışsa, istemci uygulamasının sunucu tarafından belirtilen yeniden deneme aralığı için geri kapatması gerekir. Geri alma işleminin en düşük olması, yeniden denemeler arasında bekleyen minimum süreyi harcamanızı sağlar.
6. **İstemcinizi genişletme-iş yükü**

    Yüksek aktarım hızı düzeylerinde (> 50000 RU/sn) test ediyorsanız, istemci uygulaması makine CPU veya ağ kullanımında kullanıma hazır hale geldiği için performans sorunlarına neden olabilir. Bu noktaya ulaştığınızda, istemci uygulamalarınızı birden çok sunucu arasında ölçeklendirerek Azure Cosmos DB hesabını daha fazla göndermeye devam edebilirsiniz.

7. **Ad tabanlı adresleme kullan**

    Bağlantıların `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, bağlantıyı oluşturmak için kullanılan tüm kaynakların RESOURCEID 'lerini almaktan kaçınmak için biçimi `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` olan\_, kendi kendini bağlayan, (self) değil, ad tabanlı adresleme kullanın. Ayrıca, bu kaynaklar yeniden oluşturulur (muhtemelen aynı ada sahip olabilir), önbelleğe alma işlemi yardımcı olmayabilir.

   <a id="tune-page-size"></a>
8. **Daha iyi performans için, sorguların/okunan akışların sayfa boyutunu ayarlayın**

    Belgeleri okuma akışı işlevselliğini (örneğin, readDocuments) kullanarak veya bir SQL sorgusu verirken belge okuma işlemi gerçekleştirirken, sonuç kümesi çok büyükse sonuçlar parçalı olarak döndürülür. Varsayılan olarak, sonuçlar 100 öğe veya 1 MB Öbekle döndürülür, bu sınır ilk önce dönüştürülür.

    Tüm geçerli sonuçları almak için gereken ağ gidiş dönüşlerin sayısını azaltmak için, [x-MS-Max-item-Count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) istek üst bilgisini 1000 'e kadar kullanarak sayfa boyutunu artırabilirsiniz. Örneğin, Kullanıcı arabiriminiz veya uygulama API 'niz bir kez yalnızca 10 sonuç döndürürse, okuma ve sorgular için tüketilen aktarım hızını azaltmak için sayfa boyutunu 10 ' a da azaltabilirsiniz.

    Ayrıca, Setmaxıtemcount metodunu kullanarak sayfa boyutunu da ayarlayabilirsiniz.

9. **Uygun zamanlayıcıyı kullan (olay döngüsü GÇ ağ parçacıklarının çalınmasını önleyin)**

    Zaman uyumsuz Java SDK 'Sı, engellenmeyen GÇ için [Netty](https://netty.io/) kullanır. SDK, GÇ işlemlerini yürütmek için sabit sayıda GÇ Netty olay döngüsü iş parçacığını (makinenizin sahip olduğu CPU çekirdekleri) kullanır. API tarafından döndürülen observable, paylaşılan GÇ olay döngüsü Netty iş parçacıklarından birindeki sonucu yayar. Bu nedenle, paylaşılan GÇ olay döngüsü Netty iş parçacıklarının engellenmemesi önemlidir. GÇ olay döngüsü Netty iş parçacığı üzerinde CPU yoğun iş veya engelleme işlemi yapıldığında kilitlenme olabilir veya SDK verimlilik önemli ölçüde azalabilir.

    Örneğin, aşağıdaki kod olay döngüsü GÇ Netty iş parçacığı üzerinde yoğun bir CPU kullanımı işi yürütür:

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

    Çalışmanızın türüne göre, çalışmanız için mevcut olan RxJava Scheduler 'ı kullanmanız gerekir. Buradan [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html)okuyun.

    Daha fazla bilgi için lütfen zaman uyumsuz Java SDK 'Sı için [GitHub sayfasına](https://github.com/Azure/azure-cosmosdb-java) bakın.

10. **Netty 'ın günlüğünü devre dışı bırak** Netty kitaplığı günlüğü geveze ve ek CPU maliyetlerinden kaçınmak için kapalı olması gerekir (yapılandırmada oturum açmayı gizleme yeterli olmayabilir). Hata ayıklama modunda değilseniz, netty 'ın günlüğünü tamamen devre dışı bırakın. Bu nedenle, netty 'den kaynaklanan ``org.apache.log4j.Category.callAppenders()`` ek CPU maliyetlerini kaldırmak için Log4J kullanıyorsanız, kod tabanınıza aşağıdaki satırı ekleyin:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **Işletim sistemi açık dosyaları kaynak sınırı** Bazı Linux sistemleri (Red hat gibi), açık dosya sayısı için üst sınıra ve bu nedenle toplam bağlantı sayısına sahiptir. Geçerli sınırları görüntülemek için aşağıdakileri çalıştırın:

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

12. **Netty için yerel SSL uygulamasını kullanma** Netty, daha iyi performans elde etmek için doğrudan SSL uygulama yığını için OpenSSL kullanabilir. Bu yapılandırma yokluğunda, Java 'nın varsayılan SSL uygulamasına geri dönecektir.

    Ubuntu 'da:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    ve projenizin Maven bağımlılıklara aşağıdaki bağımlılığı ekleyin:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Diğer platformlar (Red hat, Windows, Mac vb.) için bu yönergelere bakın https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Dizin Oluşturma İlkesi
 
1. **Daha hızlı yazma işlemleri için, kullanılmayan yolları dizin oluşturmanın dışında bırakma**

    Azure Cosmos DB Dizin oluşturma ilkesi, dizin oluşturma yollarını (Setıncludedpaths ve setExcludedPaths) kullanarak hangi belge yollarının dizine eklenmesini veya dışlanacağını belirtmenize olanak tanır. Dizin oluşturma yollarının kullanımı, sorgu desenlerinin önceden bildiği senaryolar için gelişmiş yazma performansı ve daha düşük dizin depolaması sunabilir, dizin oluşturma maliyetleri doğrudan dizinli benzersiz yolların sayısıyla bağıntılı olur. Örneğin, aşağıdaki kod belgelerin tamamını nasıl dışlayacak gösterir (deyişle "*" joker karakterini kullanarak dizin oluşturma işleminden bir alt ağacı.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Daha fazla bilgi için bkz. [Azure Cosmos DB Dizin oluşturma ilkeleri](indexing-policies.md).

## <a name="throughput"></a>Aktarım hızı
<a id="measure-rus"></a>

1. **Düşük istek birimleri/ikinci kullanım için ölçme ve ayarlama**

    Azure Cosmos DB, UDF 'ler, saklı yordamlar ve tetikleyicilerle ilişkisel ve hiyerarşik sorgular dahil olmak üzere zengin bir veritabanı işlemleri kümesi sunar ve bunların tümü bir veritabanı koleksiyonu içindeki belgeler üzerinde çalışıyor. Bu işlemlerden her biriyle ilişkilendirilmiş maliyet, işlemi gerçekleştirmek için gereken CPU, GÇ ve belleğe göre değişir. Donanım kaynaklarını düşünmek ve yönetmek yerine, çeşitli veritabanı işlemlerini gerçekleştirmek ve bir uygulama isteğine hizmet vermek için gereken kaynaklar için bir istek birimi (RU) tek bir ölçü olarak düşünebilirsiniz.

    Aktarım hızı, her bir kapsayıcı için ayarlanan [istek birimi](request-units.md) sayısına göre sağlanır. İstek birimi tüketimi, saniye başına bir hız olarak değerlendirilir. Kapsayıcı için sağlanan istek birimi oranını aşan uygulamalar, oran için sağlanan düzeyin altına düşene kadar sınırlandırılır. Uygulamanız daha yüksek bir işleme düzeyi gerektiriyorsa, ek istek birimleri sağlayarak aktarım hızını artırabilirsiniz.

    Bir sorgunun karmaşıklığı, bir işlem için kaç istek biriminin tüketildiğini etkiler. Koşulların sayısı, koşulların doğası, UDF sayısı ve kaynak veri kümesinin boyutu, sorgu işlemlerinin maliyetini etkiler.

    Herhangi bir işlemin (oluşturma, güncelleştirme veya silme) yükünü ölçmek için, bu işlemler tarafından tüketilen istek birimi sayısını ölçmek üzere [x-MS-Request-şarj](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) üst bilgisini inceleyin. Ayrıca,\<resourceresıset > veya feedresponse\<T > ' de eşdeğer requestücretözelliğine bakabilirsiniz.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Bu üst bilgide döndürülen istek ücreti, sağlanan aktarım hızının bir bölümü. Örneğin, sağlanan 2000 RU/sn varsa ve önceki sorgu 1000 1KB-belgeler döndürürse, işlem maliyeti 1000 ' dir. Bu nedenle, bir saniye içinde sunucu, sonraki istekleri sınırlayan orandan önce yalnızca iki istek için geçerlidir. Daha fazla bilgi için bkz. [İstek birimleri](request-units.md) ve [istek birimi Hesaplayıcısı](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Tanıtıcı hız sınırlandırma/istek hızı çok büyük**

    Bir istemci bir hesap için ayrılan aktarım hızını aşmaya çalıştığında, sunucuda bir performans düşüşü olmaz ve ayrılan düzeyin ötesinde üretilen iş kapasitesi yoktur. Sunucu, isteği RequestRateTooLarge (HTTP durum kodu 429) ile sona erpreemptively ve kullanıcının isteği yeniden denemeden önce beklemesi gereken süreyi milisaniye olarak belirten [x-MS-retry-After-MS](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) üst bilgisini döndürür.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK 'lar, sunucu tarafından belirtilen yeniden deneme üst bilgisine göre bu yanıtı dolaylı olarak yakalar ve isteği yeniden dener. Hesabınız birden çok istemci tarafından aynı anda erişilmediği takdirde, sonraki yeniden deneme başarılı olur.

    İstek hızının sürekli olarak birden fazla istemciniz varsa, bu, şu anda istemci tarafından dahili olarak 9 olarak ayarlanmış olan varsayılan yeniden deneme sayısı çalışmayabilir; Bu durumda, istemci uygulamaya 429 durum kodu ile bir DocumentClientException oluşturur. Varsayılan yeniden deneme sayısı, ConnectionPolicy örneğinde setRetryOptions kullanılarak değiştirilebilir. Varsayılan olarak, durum kodu 429 olan DocumentClientException, istek istek hızının üzerinde çalışmaya devam ederse, 30 saniyelik birikimli bir bekleme süresi dolduktan sonra döndürülür. Bu durum, geçerli yeniden deneme sayısı en fazla yeniden deneme sayısından az olduğunda bile, varsayılan olarak 9 veya Kullanıcı tanımlı bir değer olmalıdır.

    Otomatik yeniden deneme davranışı, çoğu uygulama için esneklik ve kullanılabilirliği artırmaya yardımcı olmakla birlikte, özellikle gecikmeyi ölçirken performans kıyaslamaları yapılırken gürültü 'ye gelebilir. Deneme sunucu tarafından azaldıysanız, istemci SDK 'sının sessizce yeniden denenmesine neden olursa istemci gözlenen gecikme süresi izlenir. Performans denemeleri sırasında gecikme sürelerini önlemek için, her bir işlem tarafından döndürülen ücreti ölçün ve isteklerin ayrılan istek oranının altında çalıştığından emin olun. Daha fazla bilgi için bkz. [İstek birimleri](request-units.md).
3. **Daha yüksek aktarım hızı için daha küçük belgeler tasarlama**

    Belirli bir işlemin istek ücreti (istek işleme maliyeti), belgenin boyutuyla doğrudan bağıntılı olur. Büyük belgelerle ilgili işlemler, küçük belgeler için işlemlerden daha fazla maliyetlidir.

## <a name="next-steps"></a>Sonraki adımlar
Uygulamanızı ölçek ve yüksek performans için tasarlama hakkında daha fazla bilgi için, bkz. [Azure Cosmos DB bölümleme ve ölçeklendirme](partition-data.md).
