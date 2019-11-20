---
title: .NET için Azure Cosmos DB performans ipuçları
description: Azure Cosmos veritabanı performansını geliştirmek için istemci yapılandırma seçeneklerini öğrenin
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sngun
ms.openlocfilehash: 27f39af480db8c0a044489a2efe6d2e4447b6db1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "71261320"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Azure Cosmos DB ve .NET için performans ipuçları

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB, garantili gecikme ve verimlilik ile sorunsuz bir şekilde ölçeklenen hızlı ve esnek bir dağıtılmış veritabanıdır. Veritabanınızı Azure Cosmos DB ölçeklendirmek için önemli mimari değişiklikler yapmanız veya karmaşık kod yazmanız gerekmez. Ölçeği artırma ve azaltma, tek bir API çağrısı yapmak kadar kolaydır. Daha fazla bilgi edinmek için bkz. [kapsayıcı verimini sağlama](how-to-provision-container-throughput.md) veya [veritabanı verimini sağlama](how-to-provision-database-throughput.md). Ancak, Azure Cosmos DB ağ çağrılarıyla erişildiği için, [SQL .NET SDK](sql-api-sdk-dotnet-standard.md)kullanırken en yüksek performans elde etmek için, istemci tarafı iyileştirmeler vardır.

Bu nedenle "veritabanı performanmy nasıl iyileştirebilirim?" diye soruyoruz Aşağıdaki seçenekleri göz önünde bulundurun:

## <a name="networking"></a>Networking (Ağ İletişimi)
<a id="direct-connection"></a>

1. **Bağlantı ilkesi: doğrudan bağlantı modunu kullan**

    İstemcinin Azure Cosmos DB 'e bağlanması, özellikle de gözlemlenen istemci tarafı gecikme süresi bakımından performans açısından önemli etkileri vardır. İstemci bağlantı Ilkesini yapılandırmak için kullanılabilecek iki temel yapılandırma ayarı vardır: bağlantı *modu* ve bağlantı *Protokolü*.  Kullanılabilir iki mod şunlardır:

   * Ağ Geçidi modu
      
     Ağ Geçidi modu tüm SDK platformlarında desteklenir ve [Microsoft. Azure. DOCUMENTDB SDK](sql-api-sdk-dotnet.md)için yapılandırılmış varsayılandır. Uygulamanız, katı güvenlik duvarı kısıtlamalarına sahip bir kurumsal ağda çalışıyorsa, standart HTTPS bağlantı noktasını ve tek bir uç noktayı kullandığından, ağ geçidi modu en iyi seçimdir. Ancak performans zorunluluğunu getirir, ağ geçidi modunun, verilerin her okunduğu veya Azure Cosmos DB yazıldığı her seferinde ek bir ağ atlaması içerir. Bu nedenle, daha az ağ atlaması nedeniyle doğrudan mod daha iyi performans sunar. Uygulamaları sınırlı sayıda soket bağlantısı olan ortamlarda çalıştırdığınızda, ağ geçidi bağlantı modu da önerilir. 

     Azure Işlevlerinde SDK kullanılırken, özellikle [Tüketim planında](../azure-functions/functions-scale.md#consumption-plan), [bağlantılardaki geçerli limitlerin](../azure-functions/manage-connections.md)en az olması gerekir. Bu durumda, Azure Işlevleri uygulamanızdaki diğer HTTP tabanlı istemcilerle de çalışıyorsanız ağ geçidi modu önerilir.

   * Doğrudan mod

     Doğrudan mod, TCP ve HTTPS protokolleriyle bağlantıyı destekler ve [Microsoft. Azure. Cosmos/.net v3 SDK](sql-api-sdk-dotnet-standard.md)kullanıyorsanız varsayılan bağlantı modudur.

     Cosmos DB, ağ geçidi modu kullanırken, MongoDB için Azure Cosmos DB API 'SI kullanılırken bağlantı noktası 443 ve 10250, 10255 ve 10256 bağlantı noktalarını kullanır. 10250 bağlantı noktası, coğrafi çoğaltma işlevselliği ile MongoDB örneğine eşlenen coğrafi çoğaltma ve 10255/10256 bağlantı noktaları olmadan bir varsayılan MongoDB örneğine eşlenir. Doğrudan modda TCP kullanırken, ağ geçidi bağlantı noktalarına ek olarak, Azure Cosmos DB dinamik TCP bağlantı noktalarını kullandığından 10000 ve 20000 arasındaki bağlantı noktası aralığının açık olduğundan emin olmanız gerekir. Bu bağlantı noktaları açık değilse ve TCP kullanmaya çalışırsanız, 503 hizmetinde kullanılamayan bir hata alırsınız. Aşağıdaki tabloda, her API için farklı API 'Ler ve hizmet bağlantı noktaları kullanıcısı için kullanılabilen bağlantı modları gösterilmektedir:

     |Bağlantı modu  |Desteklenen Protokol  |Desteklenen SDK 'lar  |API/hizmet bağlantı noktası  |
     |---------|---------|---------|---------|
     |Ağ Geçidi  |   HTTPS    |  Tüm SDK 'lar    |   SQL (443), Mongo (10250, 10255, 10256), Table (443), Cassandra (10350), Graph (443)    |
     |Doğrudan    |     TCP    |  .NET SDK    | 10000-20000 aralığındaki bağlantı noktaları |

     Azure Cosmos DB, HTTPS üzerinden basit ve açık bir yeniden programlama modeli sunar. Ayrıca, bu, iletişim modelinde da daha da fazla olan ve .NET istemci SDK 'Sı aracılığıyla kullanılabilen etkin bir TCP protokolünü sunmaktadır. Hem doğrudan TCP hem de HTTPS, ilk kimlik doğrulaması ve trafiği şifrelemek için SSL kullanır. En iyi performans için, mümkün olduğunda TCP protokolünü kullanın.

     SDK V3 için, CosmosClientOptions 'ın bir parçası olarak CosmosClient örneği oluşturulurken bağlantı modu yapılandırılır.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
     new CosmosClientOptions
     {
        ConnectionMode = ConnectionMode.Direct
     });
     ```

     Microsoft. Azure. DocumentDB SDK 'Sı için bağlantı modu, DocumentClient örneğinin oluşturulması sırasında ConnectionPolicy parametresiyle yapılandırılır. Doğrudan mod kullanılıyorsa, protokol ConnectionPolicy parametresi içinde de ayarlanabilir.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
     new ConnectionPolicy
     {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
     });
     ```

     TCP yalnızca doğrudan modda desteklendiğinden, ağ geçidi modu kullanılıyorsa, HTTPS protokolü her zaman ağ geçidiyle iletişim kurmak için kullanılır ve ConnectionPolicy içindeki protokol değeri yok sayılır.

     ![Azure Cosmos DB bağlantı ilkesinin çizimi](./media/performance-tips/connection-policy.png)

2. **İlk istekte başlangıç gecikmesini önlemek için OpenAsync çağrısı yapın**

    Varsayılan olarak, ilk isteğin bir gecikme süresi daha yüksektir çünkü adres yönlendirme tablosunu getirmek zorunda olur. [SDK v2](sql-api-sdk-dotnet.md)'yi kullanırken, ilk istekte bu başlangıç gecikmesini önlemek için, başlatma sırasında aşağıdaki gibi bir kez openAsync () çağrısı yapmanız gerekir.

        await client.OpenAsync();

    > [!NOTE] 
    > OpenAsync yöntemi, hesaptaki tüm kapsayıcılar için adres yönlendirme tablosunu elde etmek üzere istekleri oluşturacak. Birçok kapsayıcı içeren hesaplar, ancak uygulamaları bunların bir alt kümesine eriştiğinde, başlatmanın yavaş olmasını sağlayan gereksiz bir trafik miktarı oluşturur. Bu nedenle, OpenAsync yönteminin kullanılması, uygulama başlangıcını yavaşlattığı için bu senaryoda yararlı olmayabilir.

   <a id="same-region"></a>
3. **Aynı Azure bölgesindeki istemcileri performans için birlikte bulun**

    Mümkün olduğunda Azure Cosmos veritabanıyla aynı bölgedeki Azure Cosmos DB çağıran tüm uygulamaları yerleştirin. Yaklaşık bir karşılaştırma için, 1-2 ms içinde aynı bölgedeki Azure Cosmos DB çağrıları tamamlanır, ancak ABD 'nin batı ve Doğu yakası arasındaki gecikme 50 MS >. Bu gecikme süresi büyük olasılıkla istemciden Azure veri merkezi sınırına geçerken istek tarafından alınan yola bağlı olarak istek üzerine farklılık gösterebilir. Olası en düşük gecikme süresi, çağıran uygulamanın sağlanan Azure Cosmos DB uç noktası ile aynı Azure bölgesinde bulunduğundan emin olarak elde edilir. Kullanılabilir bölgelerin listesi için bkz. [Azure bölgeleri](https://azure.microsoft.com/regions/#services).

    Azure Cosmos DB bağlantı ilkesinin ![çizimi](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **İş parçacığı sayısını/görevleri artır**

    Azure Cosmos DB çağrıları ağ üzerinden yapıldığından, istemci uygulamanın istekler arasında çok az zaman harcamasını sağlamak için isteklerinizin paralellik derecesini değiştirmeniz gerekebilir. Örneğin, kullanıyorsanız. NET ' in [görevi paralel kitaplığı](https://msdn.microsoft.com//library/dd460717.aspx), okuma veya Azure Cosmos DB yazma görevlerinin 100s ' da oluşturun.

5. **Hızlandırılmış ağı etkinleştir**

   Gecikme süresini ve CPU azalmasını azaltmak için, istemci sanal makinelerinin hızlandırılmış ağ etkin olmasını öneririz. Hızlandırılmış ağı etkinleştirmek için [hızlandırılmış ağ Ile Windows sanal makinesi oluşturma](../virtual-network/create-vm-accelerated-networking-powershell.md) veya [hızlandırılmış ağ makalelerine bir Linux sanal makinesi oluşturma](../virtual-network/create-vm-accelerated-networking-cli.md) bölümüne bakın.


## <a name="sdk-usage"></a>SDK kullanımı
1. **En son SDK 'Yı yükler**

    Azure Cosmos DB SDK 'Ları, en iyi performansı sağlamak için sürekli geliştirilmiştir. En son SDK 'Yı öğrenmek ve geliştirmeleri gözden geçirmek için [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) sayfalarına bakın.

2. **Stream API 'Lerini kullanma**

    [.NET SDK V3](sql-api-sdk-dotnet-standard.md) , Serileştirmeden veri alabilen ve döndüresağlayan Stream API 'leri içerir. 

    Doğrudan SDK 'dan yanıtları tüketmeyen ancak diğer uygulama katmanlarına geçiş yapan orta katmanlı uygulamalar, Stream API 'Lerinden yararlanabilir. Akış işleme örnekleri için [öğe yönetimi](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) örneklerine bakın.

3. **Uygulamanızın ömrü boyunca tek bir Azure Cosmos DB istemcisi kullanın**

    Her DocumentClient ve CosmosClient örneği iş parçacığı açısından güvenlidir ve doğrudan modda çalışırken verimli bağlantı yönetimi ve adres önbelleği gerçekleştirir. Etkin bağlantı yönetimine ve SDK istemcisi tarafından daha iyi performansa izin vermek için uygulama ömrü boyunca AppDomain başına tek bir örnek kullanılması önerilir.

   <a id="max-connection"></a>
4. **Ağ Geçidi modu kullanırken konak başına System.Net MaxConnections 'yi artırma**

    Azure Cosmos DB istekleri, ağ geçidi modu kullanılırken HTTPS/REST üzerinden yapılır ve ana bilgisayar adı veya IP adresi başına varsayılan bağlantı sınırına tabi. İstemci kitaplığının Azure Cosmos DB birden çok eş zamanlı bağlantıyı kullanabilmesi için, MaxConnections 'yi daha yüksek bir değere (100-1000) ayarlamanız gerekebilir. .NET SDK 1.8.0 ve üzeri sürümlerde, [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) için varsayılan değer 50 ' dir ve değeri değiştirmek için [belgeler. Client. Connectionpolicy. maxconnectionlimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) değerini daha yüksek bir değere ayarlayabilirsiniz.   
5. **Bölümlenmiş koleksiyonlar için Paralel sorguları ayarlama**

     SQL .NET SDK sürüm 1.9.0 ve üzeri, bölümlenmiş bir koleksiyonu paralel olarak sorgulamanızı sağlayan paralel sorguları destekler. Daha fazla bilgi için bkz. SDK 'lar ile çalışma ile ilgili [kod örnekleri](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) . Paralel sorgular, kendi seri karşılarındaki sorgu gecikmesini ve aktarım hızını artırmak için tasarlanmıştır. Paralel sorgular, kullanıcıların gereksinimlerine göre özel olarak ayarlayabilen iki parametre sağlar: (a) Maxdegreeofparalellik: en fazla bölüm sayısını denetlemek için paralel olarak sorgulanabilir ve (b) MaxBufferedItemCount: sayısını denetlemek için önceden getirilen sonuçlar.

    (a) paralel sorgu ***\:paralellik ayarlama derecesi*** paralel olarak birden çok bölümü sorgulayarak, paralel sorgu kullanılır. Ancak, tek bir bölümden alınan veriler sorguya göre işlem içine alınır. SDK [v2](sql-api-sdk-dotnet.md) 'de `MaxDegreeOfParallelism` veya [sdk v3](sql-api-sdk-dotnet-standard.md) 'teki `MaxConcurrency`, bölüm sayısının en yüksek performanslı bir sorgu elde etmemesi durumunda, diğer tüm sistem koşullarının aynı kalması şartıyla, bu sorgunun en fazla olması ihtimaline sahip olur. Bölüm sayısını bilmiyorsanız, paralellik derecesini yüksek bir sayı olarak ayarlayabilirsiniz ve sistem paralellik derecesi olarak en az (bölüm sayısı, Kullanıcı tarafından girilen giriş) seçer.

    Verilerin sorguya göre tüm bölümler arasında eşit bir şekilde dağıtılması halinde paralel sorguların en iyi avantajları ürettiğine dikkat edin. Bölümlenmiş koleksiyon, bir sorgu tarafından döndürülen verilerin tümünün veya çoğunluğunun birkaç bölümde (en kötü durumda bir bölüm) yoğunlaşarak bir şekilde bölümlenmişse, sorgunun performansı bu bölümler tarafından bottlenecked olacaktır.

    (b) ***MaxBufferedItemCount\:ayarlama*** paralel sorgu, geçerli sonuç toplu işi istemci tarafından işlendiği sırada sonuçları önceden getirmek üzere tasarlanmıştır. Önceden getirme, bir sorgunun genel gecikme artışında yardımcı olur. MaxBufferedItemCount, önceden getirilen sonuçların sayısını sınırlayan parametredir. MaxBufferedItemCount değeri döndürülen beklenen sonuç sayısına (veya daha yüksek bir sayıya) ayarlandığında sorgunun ön alma işleminden en fazla avantaj almasına izin verir.

    Önceden getirme, paralellik derecesi ne olursa olsun aynı şekilde çalışıyor ve tüm bölümlerdeki veriler için tek bir arabellek vardır.  
6. **Sunucu tarafı GC 'yi aç**

    Çöp toplamanın sıklığını azaltmak bazı durumlarda yardımcı olabilir. .NET sürümünde [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) değerini true olarak ayarlayın.
7. **RetryAfter aralıklarında geri alma Uygula**

    Performans testi sırasında, küçük bir istek hızı kısıtlanana kadar yükü artırmanız gerekir. Kısıtlanmamışsa, sunucu tarafından belirtilen yeniden deneme aralığı için istemci uygulamanın azaltılmasından geri dönüş olması gerekir. Geri alma işleminin en düşük olması, yeniden denemeler arasında bekleyen minimum süreyi harcamanızı sağlar. Yeniden deneme ilkesi desteği, [Node. js](sql-api-sdk-node.md) ve [Python](sql-api-sdk-python.md)sürümlerinin yanı sıra [.NET Core](sql-api-sdk-dotnet-core.md) SDK 'larının desteklenen tüm sürümlerini içeren SQL [.net](sql-api-sdk-dotnet.md) ve [Java](sql-api-sdk-java.md), sürüm 1.9.0 ve üzeri sürüm 1.8.0 ve üzeri sürümlerde bulunur. Daha fazla bilgi için, [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    .NET SDK 'nın 1,19 ve üzeri sürümleri ile ek tanılama bilgilerini günlüğe kaydetmek ve aşağıdaki örnekte gösterildiği gibi gecikme sorunlarını gidermek için bir mekanizma vardır. Daha yüksek okuma gecikmesi olan istekler için tanılama dizesini günlüğe kaydedebilirsiniz. Yakalanan tanılama dizesi, belirli bir istek için 429s 'yi gözlemlediğiniz zamanların sayısını anlamanıza yardımcı olur.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
8. **İstemcinizi genişletme-iş yükü**

    Yüksek aktarım hızı düzeylerinde (> 50000 RU/sn) test ediyorsanız, istemci uygulaması makine CPU veya ağ kullanımında kullanıma hazır hale geldiği için performans sorunlarına neden olabilir. Bu noktaya ulaştığınızda, istemci uygulamalarınızı birden çok sunucu arasında ölçeklendirerek Azure Cosmos DB hesabını daha fazla göndermeye devam edebilirsiniz.
9. **Daha düşük okuma gecikmesi için önbellek belgesi URI 'Leri**

    En iyi okuma performansı için mümkün olduğunda önbellek belgesi URI 'Leri. Kaynağı oluştururken RESOURCEID 'yi önbelleğe almak için mantığı tanımlamanız gerekir. RESOURCEID tabanlı aramalar, ad tabanlı aramalardan daha hızlıdır, bu nedenle bu değerlerin önbelleğe alınması performansı geliştirir. 

   <a id="tune-page-size"></a>
10. **Daha iyi performans için, sorguların/okunan akışların sayfa boyutunu ayarlayın**

   Okuma akışı işlevselliğini (örneğin, ReadDocumentFeedAsync) kullanarak veya bir SQL sorgusu verirken belgelerin toplu olarak okunmasını gerçekleştirirken, sonuç kümesi çok büyükse sonuçlar bölümlenmiş bir biçimde döndürülür. Varsayılan olarak, sonuçlar 100 öğe veya 1 MB Öbekle döndürülür, bu sınır ilk önce dönüştürülür.

   Tüm geçerli sonuçları almak için gereken ağ gidiş dönüşlerin sayısını azaltmak için, [x-MS-Max-item-Count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) istek üst bilgisini 1000 'e kadar kullanarak sayfa boyutunu artırabilirsiniz. Örneğin, Kullanıcı arabiriminiz veya uygulama API 'niz bir kez yalnızca 10 sonuç döndürürse, okuma ve sorgular için tüketilen aktarım hızını azaltmak için sayfa boyutunu 10 ' a da azaltabilirsiniz.

   > [!NOTE] 
   > Maxıtemcount özelliği yalnızca sayfalandırma amacıyla kullanılmamalıdır. Tek bir sayfada döndürülen en fazla öğe sayısını azaltarak sorguların performansını artırmak için bu asıl kullanım önemlidir.  

   Sayfa boyutunu kullanılabilir Azure Cosmos DB SDK 'Ları kullanarak da ayarlayabilirsiniz. [Feedosay 'Daki Maxıtemcount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) özelliği, sabit listesi işleminde döndürülecek en fazla öğe sayısını ayarlamanıza olanak sağlar. `maxItemCount`-1 olarak ayarlandığında, SDK, belge boyutuna bağlı olarak en uygun değeri otomatik olarak bulur. Örnek:
    
   ```csharp
    IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
   ```
    
   Bir sorgu yürütüldüğünde, sonuçta elde edilen veriler bir TCP paketi içinde gönderilir. `maxItemCount`için çok düşük değer belirtirseniz, verileri TCP paketi içinde göndermek için gereken gidiş sayısı yüksektir ve bu da performansı etkiler. `maxItemCount` özelliği için hangi değerin ayarlanacağını bilmiyorsanız, bunu-1 olarak ayarlamak ve SDK 'nın varsayılan değeri seçmesini sağlamak en iyisidir. 

11. **İş parçacığı sayısını/görevleri artır**

    Bkz. Ağ bölümünde [iş parçacığı sayısı/görev sayısını artırma](#increase-threads) .

12. **64 bit ana bilgisayar işlemeyi kullanma**

    SQL SDK, SQL .NET SDK sürümü 1.11.4 ve üstünü kullanırken 32 bitlik bir ana bilgisayar işleminde çalışmaktadır. Ancak, çapraz bölüm sorguları kullanıyorsanız, gelişmiş performans için 64 bitlik ana bilgisayar işleme önerilir. Aşağıdaki uygulama türleri varsayılan olarak 32 bitlik ana bilgisayar işlemine sahiptir ve bu nedenle, bunu 64 bit olarak değiştirmek için uygulamanızın türüne göre şu adımları izleyin:

    - Yürütülebilir uygulamalar için bu işlem, **derleme** sekmesindeki **proje özellikleri** penceresinde **32 bit tercih et** seçeneğinin işaretlenmesi yoluyla yapılabilir.

    - VSTest **tabanlı test projeleri** Için, **Visual Studio test** menü seçeneğinden **X64 olarak varsayılan Işlemci mimarisi**->test->**Test ayarları** ' nı seçerek bu işlemi gerçekleştirebilirsiniz.

    - Yerel olarak dağıtılan ASP.NET Web uygulamaları için, bu işlem, **Web siteleri ve projeleri için IIS Express 64 bitlik sürümünü kullan**, **araçlar**->**Seçenekler**->**Projeler ve çözümler** altında-> **Web projeleri**.

    - Azure 'da dağıtılan ASP.NET Web uygulamaları için, Azure portal **uygulama ayarlarında** **Platform 64-bit olarak** seçilerek bu yapılabilir.

## <a name="indexing-policy"></a>Dizin Oluşturma İlkesi
 
1. **Daha hızlı yazma işlemleri için, kullanılmayan yolları dizin oluşturmanın dışında bırakma**

    Cosmos DB Dizin oluşturma ilkesi, dizin oluşturma yollarından (ındexingpolicy. ıncludedpaths ve ındexingpolicy. ExcludedPaths) yararlanarak dizin oluşturma işleminden hangi belge yollarının ekleneceğini veya dışlanacağını belirtmenize olanak tanır. Dizin oluşturma yollarının kullanımı, sorgu desenlerinin önceden bildiği senaryolar için gelişmiş yazma performansı ve daha düşük dizin depolaması sunabilir, dizin oluşturma maliyetleri doğrudan dizinli benzersiz yolların sayısıyla bağıntılı olur.  Örneğin, aşağıdaki kod, "*" joker karakterini kullanarak, belgelerin tamamını (bir alt ağacı) dizin oluşturma işleminden nasıl dışlayacak gösterir.

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Daha fazla bilgi için bkz. [Azure Cosmos DB Dizin oluşturma ilkeleri](index-policy.md).

## <a name="throughput"></a>İşleme
<a id="measure-rus"></a>

1. **Düşük istek birimleri/ikinci kullanım için ölçme ve ayarlama**

    Azure Cosmos DB, UDF 'ler, saklı yordamlar ve tetikleyicilerle ilişkisel ve hiyerarşik sorgular dahil olmak üzere zengin bir veritabanı işlemleri kümesi sunar ve bunların tümü bir veritabanı koleksiyonu içindeki belgeler üzerinde çalışıyor. Bu işlemlerden her biriyle ilişkilendirilmiş maliyet, işlemi gerçekleştirmek için gereken CPU, GÇ ve belleğe göre değişir. Donanım kaynaklarını düşünmek ve yönetmek yerine, çeşitli veritabanı işlemlerini gerçekleştirmek ve bir uygulama isteğine hizmet vermek için gereken kaynaklar için bir istek birimi (RU) tek bir ölçü olarak düşünebilirsiniz.

    Aktarım hızı, her bir kapsayıcı için ayarlanan [istek birimi](request-units.md) sayısına göre sağlanır. İstek birimi tüketimi, saniye başına bir hız olarak değerlendirilir. Kapsayıcı için sağlanan istek birimi oranını aşan uygulamalar, oran için sağlanan düzeyin altına düşene kadar sınırlandırılır. Uygulamanız daha yüksek bir işleme düzeyi gerektiriyorsa, ek istek birimleri sağlayarak aktarım hızını artırabilirsiniz. 

    Bir sorgunun karmaşıklığı, bir işlem için kaç Istek biriminin tüketildiğini etkiler. Koşulların sayısı, koşulların doğası, UDF sayısı ve kaynak veri kümesinin boyutu, sorgu işlemlerinin maliyetini etkiler.

    Herhangi bir işlemin ek yükünü ölçmek için (oluşturma, güncelleştirme veya silme), [x-MS-Request-ücret](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) üst bilgisini (veya .NET SDK 'Da bulunan Resourceres,\<t > veya feedresponse\<t > ile) inceleyin. Bu işlemler tarafından tüketilen istek birimi sayısı.

    ```csharp
    // Measure the performance (request units) of writes
    ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
    Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
    // Measure the performance (request units) of queries
    IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
    while (queryable.HasMoreResults)
         {
              FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
              Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
         }
    ```             

    Bu üst bilgide döndürülen istek ücreti, sağlanan aktarım hızını (ör. 2000 ru/saniye) bir kesri. Örneğin, yukarıdaki sorgu 1000 kb-belge döndürürse, işlemin maliyeti 1000 ' dir. Bu nedenle, bir saniye içinde sunucu, sonraki istekleri sınırlayan orandan önce yalnızca iki istek için geçerlidir. Daha fazla bilgi için bkz. [İstek birimleri](request-units.md) ve [istek birimi Hesaplayıcısı](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Tanıtıcı hız sınırlandırma/istek hızı çok büyük**

    Bir istemci bir hesap için ayrılan aktarım hızını aşmaya çalıştığında, sunucuda bir performans düşüşü olmaz ve ayrılan düzeyin ötesinde üretilen iş kapasitesi yoktur. Sunucu, isteği RequestRateTooLarge (HTTP durum kodu 429) ile sona erpreemptively ve kullanıcının isteği yeniden denemeden önce beklemesi gereken süreyi milisaniye olarak belirten [x-MS-retry-After-MS](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) üst bilgisini döndürür.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK 'lar, sunucu tarafından belirtilen yeniden deneme üst bilgisine göre bu yanıtı dolaylı olarak yakalar ve isteği yeniden dener. Hesabınız birden çok istemci tarafından aynı anda erişilmediği takdirde, sonraki yeniden deneme başarılı olur.

    İstek hızının sürekli olarak birden fazla istemciniz varsa, bu, şu anda istemci tarafından dahili olarak 9 olarak ayarlanmış olan varsayılan yeniden deneme sayısı çalışmayabilir; Bu durumda, istemci uygulamaya 429 durum kodu ile bir DocumentClientException oluşturur. Varsayılan yeniden deneme sayısı, ConnectionPolicy örneğindeki RetryOptions ayarlanarak değiştirilebilir. Varsayılan olarak, durum kodu 429 olan DocumentClientException, istek istek hızının üzerinde çalışmaya devam ederse, 30 saniyelik birikimli bir bekleme süresi dolduktan sonra döndürülür. Bu durum, geçerli yeniden deneme sayısı en fazla yeniden deneme sayısından az olduğunda bile, varsayılan olarak 9 veya Kullanıcı tanımlı bir değer olmalıdır.

    Otomatik yeniden deneme davranışı, çoğu uygulama için esneklik ve kullanılabilirliği artırmaya yardımcı olmakla birlikte, özellikle gecikmeyi ölçirken performans kıyaslamaları yapılırken gürültü 'ye gelebilir.  Deneme sunucu tarafından azaldıysanız, istemci SDK 'sının sessizce yeniden denenmesine neden olursa istemci gözlenen gecikme süresi izlenir. Performans denemeleri sırasında gecikme sürelerini önlemek için, her bir işlem tarafından döndürülen ücreti ölçün ve isteklerin ayrılan istek oranının altında çalıştığından emin olun. Daha fazla bilgi için bkz. [İstek birimleri](request-units.md).
3. **Daha yüksek aktarım hızı için daha küçük belgeler tasarlama**

    Belirli bir işlemin istek ücreti (örn. istek işleme maliyeti) doğrudan belgenin boyutuyla bağıntılı olur. Büyük belgelerle ilgili işlemler, küçük belgeler için işlemlerden daha fazla maliyetlidir.

## <a name="next-steps"></a>Sonraki adımlar
Birkaç istemci makinede yüksek performanslı senaryolar için Azure Cosmos DB değerlendirmek üzere kullanılan örnek bir uygulama için bkz. [Azure Cosmos DB Ile performans ve ölçek testi](performance-testing.md).

Ayrıca, uygulamanızı ölçek ve yüksek performans için tasarlama hakkında daha fazla bilgi edinmek için bkz. [Azure Cosmos DB bölümleme ve ölçeklendirme](partition-data.md).
