---
title: .NET SDK V3 için Azure Cosmos DB performans ipuçları
description: Azure Cosmos DB .NET v3 SDK performansını artırmaya yardımcı olmak için istemci yapılandırma seçeneklerini öğrenin.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: jawilley
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: 1a6439cfa64257e80d113f01f4ed31d56d850ea3
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226065"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Azure Cosmos DB ve .NET için performans ipuçları
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK’sı v4](performance-tips-java-sdk-v4-sql.md)
> * [Zaman uyumsuz Java SDK v2](performance-tips-async-java.md)
> * [Zaman uyumlu Java SDK v2](performance-tips-java.md)

Azure Cosmos DB, garantili gecikme ve verimlilik düzeyiyle sorunsuz bir şekilde ölçeklendirilebilen hızlı ve esnek bir dağıtılmış veritabanıdır. Veritabanınızı Azure Cosmos DB ölçeklendirmek için önemli mimari değişiklikler yapmanız veya karmaşık kod yazmanız gerekmez. Ölçeği artırma ve azaltma, tek bir API çağrısı yapmak kadar kolaydır. Daha fazla bilgi edinmek için bkz. [kapsayıcı işleme sağlama](how-to-provision-container-throughput.md) veya [veritabanı üretimini sağlama](how-to-provision-database-throughput.md). 

Azure Cosmos DB ağ çağrıları üzerinden erişildiği için, [SQL .NET SDK](sql-api-sdk-dotnet-standard.md)kullandığınızda en yüksek performansa ulaşmak üzere istemci tarafı iyileştirmeler yapabilirsiniz.

Veritabanı performanslarını artırmaya çalışıyorsanız, aşağıdaki bölümlerde sunulan seçenekleri göz önünde bulundurun.

## <a name="hosting-recommendations"></a>Barındırma önerileri

**Sorgu yoğunluğu yoğun iş yükleri için Linux veya Windows 32 bit ana bilgisayar işlemesi yerine Windows 64-bit kullanın**

Gelişmiş performans için Windows 64 bit ana bilgisayar işlemesini öneririz. SQL SDK, sorguları yerel olarak ayrıştırmak ve iyileştirmek için yerel bir ServiceInterop.dll içerir. ServiceInterop.dll yalnızca Windows x64 platformunda desteklenir. 

ServiceInterop.dll kullanılamadığı Linux ve diğer desteklenmeyen platformlar için, iyileştirilmiş sorguyu almak üzere ağ geçidine ek bir ağ çağrısı yapılır. 

Burada listelenen dört uygulama türü, varsayılan olarak 32 bitlik ana bilgisayar işlemini kullanır. Uygulama türü için ana bilgisayar işlemesini 64 bitlik işleme değiştirmek için aşağıdakileri yapın:

- **Yürütülebilir uygulamalar için**: **Proje özellikleri** penceresinde, **Yapı** bölmesinde, [Platform hedefini](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) **x64** olarak ayarlayın.

- **VSTest tabanlı test projeleri için**: Visual Studio **Test** menüsünde **Test**  >  **Test ayarları**' nı seçin ve ardından **Varsayılan işlemci mimarisini** **x64** olarak ayarlayın.

- **Yerel olarak dağıtılan ASP.NET Web uygulamaları için**: **Araçlar**  >  **Seçenekler**  >  **Projeler ve çözümler**  >  **Web projeleri**' ni seçin ve ardından **Web siteleri ve projeler için IIS Express 64 bitlik sürümünü kullan**' ı seçin.

- **Azure 'da dağıtılan ASP.NET Web uygulamaları için**: Azure Portal, **uygulama ayarları**' nda **64 bitlik** platformu seçin.

> [!NOTE] 
> Varsayılan olarak, yeni Visual Studio projeleri **herhangi BIR CPU**'ya ayarlanır. Projenizi **x86**'ya geçiş yapmak için **x64** olarak ayarlamanızı öneririz. **Herhangi BIR CPU** 'ya ayarlanmış bir proje yalnızca x86 bağımlılığı eklendiyse, kolayca **x86** 'ya geçiş yapabilir.<br/>
> ServiceInterop.dll dosyanın, SDK DLL 'sinin yürütüldüğü klasörde olması gerekir. Bu, yalnızca dll 'Leri el ile kopyalarsanız veya özel derleme veya dağıtım sistemlerine sahipseniz sorun olması gerekir.
    
**Sunucu tarafı atık toplamayı aç**

Çöp toplamanın sıklığını azaltmak bazı durumlarda yardımcı olabilir. .NET sürümünde [gcServer](/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection) olarak ayarlayın `true` .

**İstemci iş yükünüzü ölçeklendirin**

Yüksek aktarım hızı düzeylerinde veya saniyede 50.000 Istek birimi (RU/sn) daha büyük fiyatlar halinde test ediyorsanız, istemci uygulama bir iş yükü performans sorunu olabilir. Bunun nedeni, makinenin CPU veya ağ kullanımında büyük olabileceğini gösterebilir. Bu noktaya ulaştığınızda, istemci uygulamalarınızı birden çok sunucu arasında ölçeklendirerek Azure Cosmos DB hesabını daha fazla göndermeye devam edebilirsiniz.

> [!NOTE] 
> Yüksek CPU kullanımı, artan gecikme süresine ve istek zaman aşımı özel durumlarına neden olabilir.

## <a name="networking"></a>Ağ
<a id="direct-connection"></a>

**Bağlantı ilkesi: doğrudan bağlantı modunu kullan**

.NET v3 SDK varsayılan bağlantı modu, TCP protokolüyle doğrudan yapılır. ' De örneği oluştururken bağlantı modunu yapılandırırsınız `CosmosClient` `CosmosClientOptions` .  Farklı bağlantı seçenekleri hakkında daha fazla bilgi edinmek için [bağlantı modları](sql-sdk-connection-modes.md) makalesine bakın.

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

**Kısa ömürlü bağlantı noktası tükenmesi**

Örneklerinizi üzerinde yüksek bir bağlantı birimi veya yüksek bağlantı noktası kullanımı görürseniz, önce istemci örneklerinizin tekton olduğunu doğrulayın. Diğer bir deyişle, istemci örneklerinin uygulamanın ömrü için benzersiz olması gerekir.

TCP protokolünde çalışırken, istemci uzun süreli bağlantıları kullanarak gecikme süresini iyileştirir. Bu, iki dakikadan kısa bir süre sonra bağlantıları sonlandıran HTTPS protokolüne karşılık gelir.

Seyrek erişiminizin olduğu senaryolarda ve ağ geçidi modu erişimiyle karşılaştırıldığında daha yüksek bir bağlantı sayısı fark ederseniz şunları yapabilirsiniz:

* [Cosmosclientoptions. PortReuseMode](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode) özelliğini olarak yapılandırın `PrivatePortPool` (Framework sürümleri 4.6.1 ve üzeri ve .net Core sürümleri 2,0 ve üzeri ile geçerlidir). Bu özellik, SDK 'nın çeşitli Azure Cosmos DB hedef uç noktaları için kısa ömürlü bağlantı noktası havuzu kullanmasına izin verir.
* [Cosmosclientoptions. ıdbu](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout) özelliğini 10 dakikadan büyük veya buna eşit olacak şekilde yapılandırın. Önerilen değerler 20 dakikadan 24 saate kadar sürer.

<a id="same-region"></a>

**Aynı Azure bölgesindeki istemcileri performans için birlikte bulun**

Mümkün olduğunda, Azure Cosmos DB veritabanıyla aynı bölgedeki Azure Cosmos DB çağıran tüm uygulamaları yerleştirin. İşte yaklaşık bir karşılaştırma: aynı bölgedeki Azure Cosmos DB için çağrılar 1 milisaniyelik (MS) ile 2 ms arasında tamamlanır, ancak ABD 'nin batı ve Doğu yakası arasındaki gecikme 50 MS 'den fazla olur. Bu gecikme, istemciden Azure veri merkezi sınırına geçerken istek tarafından alınan yola bağlı olarak istek üzerine değişiklik gösterebilir. 

Çağıran uygulamanın sağlanan Azure Cosmos DB uç noktası ile aynı Azure bölgesinde bulunduğundan emin olarak olası en düşük gecikme süresini alabilirsiniz. Kullanılabilir bölgelerin listesi için bkz. [Azure bölgeleri](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Aynı bölgedeki istemcileri birlikte bulun." border="false":::

   <a id="increase-threads"></a>

**İş parçacığı/görev sayısını artırma**

Azure Cosmos DB çağrıları ağ üzerinden yapıldığından, istemci uygulamanın istekler arasında en az zaman harcamasını sağlamak için isteklerinizin eşzamanlılık derecesini değiştirmeniz gerekebilir. Örneğin, .NET [görev paralel kitaplığı](/dotnet/standard/parallel-programming/task-parallel-library-tpl)kullanıyorsanız, Azure Cosmos DB okuyan veya üzerine yazılan yüzlerce görev sırasını oluşturun.

**Hızlandırılmış ağı etkinleştir**
 
Gecikme süresi ve CPU değişimi azaltmak için istemci sanal makinelerinizde hızlandırılmış ağı etkinleştirmenizi öneririz. Daha fazla bilgi için bkz. [hızlandırılmış ağ Ile Windows sanal makinesi oluşturma](../virtual-network/create-vm-accelerated-networking-powershell.md) veya [hızlandırılmış ağ ile Linux sanal makinesi oluşturma](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>SDK kullanımı

**En son SDK 'Yı yükler**

Azure Cosmos DB SDK 'Ları, en iyi performansı sağlamak için sürekli geliştirilmiştir. En son SDK 'Yı ve geliştirmeleri gözden geçirmek için bkz. [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md).

**Stream API 'Lerini kullanma**

[.NET SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) , verileri seri hale getirmeksizin alıp döndürebilirler. 

Yanıtları doğrudan SDK 'dan tüketmeyen, ancak bunları diğer uygulama katmanlarına geçirmeyen orta katmanlı uygulamalar, Stream API 'Lerinden faydalanabilir. Akış işleme örnekleri için bkz. [öğe yönetimi](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) örnekleri.

**Uygulamanızın ömrü boyunca tek bir Azure Cosmos DB istemcisi kullanın**

Her `CosmosClient` örnek iş parçacığı açısından güvenlidir ve doğrudan modda çalışırken verimli bağlantı yönetimi ve adres önbelleği gerçekleştirir. Etkili bağlantı yönetimine ve daha iyi SDK istemci performansına izin vermek için, `AppDomain` uygulamanın kullanım ömrü boyunca tek bir örnek kullanmanızı öneririz.

Azure Işlevleri üzerinde çalışırken, örneklerin de mevcut [yönergeleri](../azure-functions/manage-connections.md#static-clients) izlemesi ve tek bir örneği koruması gerekir.

<a id="max-connection"></a>

**Yazma işlemlerinde içerik yanıtını devre dışı bırak**

Ağır oluşturma yükleri olan iş yükleri için, `EnableContentResponseOnWrite` istek seçeneğini olarak ayarlayın `false` . Hizmet artık oluşturulan veya güncellenen kaynağı SDK 'ya döndürmez. Normalde, uygulama oluşturulmakta olan nesneye sahip olduğundan, hizmet tarafından döndürülmesi gerekmez. Üst bilgi değerlerine, istek ücreti gibi hala erişilebilir. İçerik yanıtının devre dışı bırakılması, SDK 'nın artık bellek ayırması veya yanıtın gövdesini serileştirilmesi gerekmediği için performansı artırmaya yardımcı olabilir. Ayrıca, performansı daha fazla kolaylaştırmak için ağ bant genişliği kullanımını azaltır.  

```csharp
ItemRequestOptions requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

**Gecikme süresi yerine üretilen işi iyileştirmek için toplu etkinleştirme**

İş yükünün büyük miktarda işleme gerektirdiği ve gecikme süresinin önemli olmadığı senaryolarda *toplu* olarak etkinleştirin. Toplu özelliği etkinleştirme hakkında daha fazla bilgi edinmek ve hangi senaryoları için kullanılması gerektiğini öğrenmek için bkz. [toplu desteğe giriş](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk).

**Ağ Geçidi modunu kullandığınızda konak başına System.Net MaxConnections 'yi artırma**

Ağ Geçidi modunu kullandığınızda Azure Cosmos DB istekleri HTTPS/REST üzerinden yapılır. Ana bilgisayar adı veya IP adresi başına varsayılan bağlantı sınırına tabidir. `MaxConnections`İstemci kitaplığının Azure Cosmos DB için birden çok eş zamanlı bağlantı kullanabilmesi için daha yüksek bir değere (100 ile 1.000 arasında) ayarlamanız gerekebilir. .NET SDK 1.8.0 ve üzeri sürümlerde, [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) için varsayılan değer 50 ' dir. Değeri değiştirmek için [`Documents.Client.ConnectionPolicy.MaxConnectionLimit`](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) daha yüksek bir değere ayarlayabilirsiniz.

**Bölümlenmiş koleksiyonlar için Paralel sorguları ayarlama**

SQL .NET SDK Paralel sorguları destekler, bu, bölümlenmiş bir kapsayıcıyı paralel olarak sorgulamanızı sağlar. Daha fazla bilgi için bkz. SDK 'lar ile çalışma ile ilgili [kod örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs) . Paralel sorgular, seri karşılığından daha iyi sorgu gecikmesi ve verimlilik sağlamak üzere tasarlanmıştır. 

Paralel sorgular, gereksinimlerinize uyacak şekilde ayarlayabilmeniz için iki parametre sağlar: 

- **MaxConcurrency**: paralel olarak sorgulanabilecek en fazla bölüm sayısını denetler.

   Paralel sorgu birden çok bölümü paralel olarak sorgulayarak işe yarar. Ancak tek bir bölümden alınan veriler sorguya göre işlem içine alınır. `MaxConcurrency` [SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) 'nin bölüm sayısına göre ayarlanması en iyi performansı elde etmek için en iyi performansa sahiptir, diğer tüm sistem koşulları aynı kalır. Bölüm sayısını bilmiyorsanız paralellik derecesini yüksek bir sayı olarak ayarlayabilirsiniz. Sistem paralellik derecesi olarak en az (bölüm sayısını, Kullanıcı tarafından girilen girişi) seçer.

    Paralel sorgular, verilerin sorguya göre tüm bölümler arasında eşit bir şekilde dağıtılması halinde en avantaja sahip olur. Bölümlenmiş koleksiyon, bir sorgu tarafından döndürülen verilerin tümünün veya çoğu birkaç bölümde yoğunlaşarak (bir bölüm en kötü durumdur), bu bölümlerin sorgunun performansını performans sorunlarına neden olur.
   
- **Maxbuffereditemcount**: önceden getirilen sonuçların sayısını denetler.

   Paralel sorgu, geçerli sonuç toplu işi istemci tarafından işlendiği sırada sonuçları önceden getirmek üzere tasarlanmıştır. Bu önceden getirme, bir sorgunun genel gecikmesinin artırılmasına yardımcı olur. `MaxBufferedItemCount`Parametresi, önceden getirilen sonuçların sayısını sınırlar. `MaxBufferedItemCount`Sorgunun ön getirmeyi en fazla avantaj almasına izin vermek için beklenen sonuç sayısı (veya daha yüksek bir sayı) olarak ayarlayın.

   Önceden getirme, paralellik derecesi ne olursa olsun aynı şekilde çalışacaktır ve tüm bölümlerdeki veriler için tek bir arabellek vardır.  

**RetryAfter aralıklarında geri alma Uygula**

Performans testi sırasında, küçük bir istek hızı kısıtlanana kadar yükü artırmanız gerekir. İstekler kısıtlandığı zaman, istemci uygulamanın, sunucu tarafından belirtilen yeniden deneme aralığı için azaltma kapatması gerekir. Geri alma işleminin ne kadar düşük olması, yeniden denemeler arasında bekleyen en az miktarda süre harcamanızı sağlar. 

Daha fazla bilgi için bkz. [RetryAfter](/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter#Microsoft_Azure_Cosmos_CosmosException_RetryAfter).
    
Aşağıdaki örnekte gösterildiği gibi, ek tanılama bilgileri ve sorun giderme gecikme sorunlarını günlüğe kaydetme mekanizması vardır. Daha yüksek okuma gecikmesi olan istekler için tanılama dizesini günlüğe kaydedebilirsiniz. Yakalanan tanılama dizesi, belirli bir istek için kaç kez *429* hatası aldığınızı anlamanıza yardımcı olur.

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**İş parçacığı/görev sayısını artırma**

Bkz. Bu makalenin ağ bölümünde [iş parçacığı sayısı/görevler sayısını artırın](#increase-threads) .

## <a name="indexing-policy"></a>Dizin oluşturma ilkesi
 
**Daha hızlı yazma işlemleri için, kullanılmayan yolları dizin oluşturmanın dışında bırakma**

Azure Cosmos DB Dizin oluşturma ilkesi, dizin oluşturma yollarını (ındexingpolicy. ıncludedpaths ve ındexingpolicy. ExcludedPaths) kullanarak hangi belge yollarının dizine eklenmesini veya hangilerinin dışlanacağını belirtmenize de olanak tanır. 

Yalnızca ihtiyacınız olan yolların dizinlemesi, yazma performansını iyileştirebilir, yazma işlemlerinde RU ücretlerini azaltabilir ve sorgu desenlerinin önceden bilinen senaryolarında Dizin depolamayı azaltabilirsiniz. Bunun nedeni, dizin oluşturma maliyetlerinin doğrudan dizin oluşturulan benzersiz yolların sayısıyla ilişkilendirilmesi. Örneğin, aşağıdaki kod, "*" joker karakterini kullanarak, belgelerin tamamını (bir alt ağacı) dizin oluşturma işleminden nasıl dışlayacak göstermektedir:

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

Daha fazla bilgi için bkz. [Azure Cosmos DB Dizin oluşturma ilkeleri](index-policy.md).

## <a name="throughput"></a>Aktarım hızı
<a id="measure-rus"></a>

**Düşük RU/s kullanımı için ölçüm ve ayarlama**

Azure Cosmos DB zengin bir veritabanı işlemleri kümesi sunar. Bu işlemler, bir veritabanı koleksiyonu içindeki belgeler üzerinde çalışan Evrensel Disk Biçimi (UDF) dosyaları, saklı yordamlar ve tetikleyicilerle ilişkisel ve hiyerarşik sorgular içerir. 

Bu işlemlerden her biriyle ilişkili maliyetler, işlemi gerçekleştirmek için gereken CPU, GÇ ve belleğe göre değişir. Donanım kaynaklarını düşünmek ve yönetmek yerine, çeşitli veritabanı işlemlerini gerçekleştirmek ve bir uygulama isteğine hizmet vermek için gereken kaynaklar için bir Istek birimini tek bir ölçü olarak düşünebilirsiniz.

Aktarım hızı, her bir kapsayıcı için ayarlanan [Istek birimi](request-units.md) sayısına göre sağlanır. İstek birimi tüketimi, saniye başına bir birim olarak değerlendirilir. Kapsayıcı için sağlanan Istek birimi oranını aşan uygulamalar, oran için sağlanan düzeyin altına düşene kadar sınırlandırılır. Uygulamanız daha yüksek bir işleme düzeyi gerektiriyorsa, ek Istek birimleri sağlayarak aktarım hızını artırabilirsiniz.

Bir sorgunun karmaşıklık düzeyi, işlem için kullanılan istek birimi sayısını etkiler. Koşulların sayısı, koşulların doğası, UDF dosyalarının sayısı ve kaynak veri kümesinin boyutu, sorgu işlemlerinin maliyetini etkiler.

Herhangi bir işlemin (oluşturma, güncelleştirme veya silme) yükünü ölçmek için, [](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) `RequestCharge` `ResourceResponse<T>` `FeedResponse<T>` işlemler tarafından tüketilen istek birimlerinin sayısını ölçmek üzere x-MS-Request-ücret üst bilgisini (veya .NET SDK içindeki veya içinde eşdeğer özelliği) inceleyin:

```csharp
// Measure the performance (Request Units) of writes
ItemResponse<Book> response = await container.CreateItemAsync<Book>(myBook, new PartitionKey(myBook.PkValue));
Console.WriteLine("Insert of item consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
FeedIterator<Book> queryable = container.GetItemQueryIterator<ToDoActivity>(queryString);
while (queryable.HasMoreResults)
    {
        FeedResponse<Book> queryResponse = await queryable.ExecuteNextAsync<Book>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

Bu üst bilgide döndürülen istek ücreti, sağlanan aktarım hızının (yani, 2.000 RU/sn) bir bölümü olur. Örneğin, yukarıdaki sorgu 1.000 1 KB 'lik belgeler döndürürse, işlemin maliyeti 1.000 ' dir. Bu nedenle, bir saniye içinde, daha sonraki istekleri derecelendirmek için sunucu yalnızca iki tür istek olarak kabul edilir. Daha fazla bilgi için bkz. [Istek birimleri](request-units.md) ve [istek birimi Hesaplayıcısı](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Tanıtıcı hız sınırlandırma/istek hızı çok büyük**

Bir istemci, bir hesap için ayrılan aktarım hızını aşmaya çalıştığında, sunucuda bir performans düşüşü olmaz ve ayrılan düzeyin ötesinde üretilen iş kapasitesi kullanılamaz. Sunucu preemptively, isteği RequestRateTooLarge (HTTP durum kodu 429) ile sonlandırır. Kullanıcının isteği yeniden denemeden önce beklemesi gereken süre miktarını milisaniye cinsinden belirten bir [x-MS-retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) üst bilgisi döndürür.

```xml
    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100
```

SDK 'lar, sunucu tarafından belirtilen yeniden deneme üst bilgisine göre bu yanıtı dolaylı olarak yakalar ve isteği yeniden dener. Hesabınız birden çok istemci tarafından aynı anda erişilmediği takdirde, sonraki yeniden deneme başarılı olur.

İstek hızının sürekli olarak birden fazla istemciniz varsa, bu, şu anda istemci tarafından dahili olarak 9 ' a ayarlanmış olan varsayılan yeniden deneme sayısı yeterli olmayabilir. Bu durumda, istemci, uygulama 429 durum kodu ile CosmosException oluşturur. 

Örneği üzerinde ayarını yaparak varsayılan yeniden deneme sayısını değiştirebilirsiniz `RetryOptions` `CosmosClientOptions` . Varsayılan olarak, istek hızı üzerinde çalışmaya devam ederse, 429 durum kodu ile CosmosException, 30 saniyelik birikimli bir bekleme süresi sonra döndürülür. Bu hata, geçerli yeniden deneme sayısı en fazla yeniden deneme sayısından az olduğunda, geçerli değerin varsayılan olarak 9 veya Kullanıcı tanımlı bir değer olup olmadığı durumlarda da döndürülür.

Otomatik yeniden deneme davranışı çoğu uygulama için dayanıklılığı ve kullanılabilirliği artırmaya yardımcı olur. Ancak, özellikle gecikmeyi ölçmeniz durumunda performans kıyaslamaları yaparken en iyi davranış olmayabilir. Deneme sunucu tarafından azaldıysanız, istemci SDK 'sının sessizce yeniden denenmesine neden olursa istemci gözlenen gecikme süresi izlenir. Performans denemeleri sırasında gecikme sürelerini önlemek için, her bir işlemin döndürdüğü ücreti ölçün ve isteklerin ayrılan istek oranının altında çalıştığından emin olun. 

Daha fazla bilgi için bkz. [Istek birimleri](request-units.md).

**Daha yüksek aktarım hızı için, daha küçük belgeler için tasarım**

Belirtilen bir işlemin istek ücreti (yani, istek işleme maliyeti) doğrudan belgenin boyutuyla ilişkili olur. Büyük belgelerle ilgili işlemler küçük belgelerde işlemlerden daha fazla maliyetlidir.

## <a name="next-steps"></a>Sonraki adımlar
Birkaç istemci makinede yüksek performanslı senaryolar için Azure Cosmos DB değerlendirmek üzere kullanılan örnek bir uygulama için bkz. [Azure Cosmos DB Ile performans ve ölçek testi](performance-testing.md).

Uygulamanızı ölçek ve yüksek performans için tasarlama hakkında daha fazla bilgi için, bkz. [Azure Cosmos DB bölümleme ve ölçeklendirme](partitioning-overview.md).
