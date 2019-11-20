---
title: Yönetilen Önbellek Hizmeti uygulamalarını redin-Azure 'a geçirme
description: Yönetilen Önbellek hizmetini ve Rol İçi Önbellek uygulamalarını Redsıs için Azure önbelleğine geçirmeyi öğrenin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 9596b8cb771f114cb09c5d6c6ae33b4fc4a8cada
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122692"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Redsıs için yönetilen önbellek hizmetinden Azure önbelleğine geçiş
Azure Yönetilen Önbellek Hizmeti kullanan uygulamalarınızın redin için Azure önbelleğine geçirilmesi, önbelleğe alma uygulamanız tarafından kullanılan yönetilen önbellek hizmeti özelliklerine bağlı olarak uygulamanızda en az değişiklikle gerçekleştirilebilir. API 'Ler tamamen benzer değildir ve bir önbelleğe erişmek için Yönetilen Önbellek hizmetini kullanan mevcut kodunuzun çoğu, en az değişiklikle yeniden kullanılabilir. Bu makalede, yönetilen önbellek hizmeti uygulamalarınızı Redsıs için Azure önbelleği 'ni kullanmak üzere geçirmek için gerekli yapılandırma ve uygulama değişikliklerinin nasıl yapılacağı gösterilmektedir ve bu işlevselliği uygulamak için redin Azure Cache 'in bazı özelliklerinin nasıl kullanılabileceği gösterilmektedir. Yönetilen Önbellek Hizmeti önbelleği.

>[!NOTE]
>Yönetilen Önbellek Hizmeti ve Rol İçi Önbellek 30 Kasım 2016 ' de [kullanımdan](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) kaldırıldı. Redu için Azure önbelleğine geçirmek istediğiniz Rol İçi Önbellek dağıtımlarınız varsa, bu makaledeki adımları izleyebilirsiniz.

## <a name="migration-steps"></a>Geçiş adımları
Yönetilen Önbellek Hizmeti uygulamasını Redsıs için Azure önbelleği kullanmak üzere geçirmek için aşağıdaki adımlar gereklidir.

* Redsıs için Yönetilen Önbellek Hizmeti özelliklerini Azure önbelleğine eşleme
* Bir önbellek teklifi seçin
* Önbellek oluşturma
* Önbellek Istemcilerini yapılandırma
  * Yönetilen Önbellek hizmeti yapılandırmasını kaldırma
  * StackExchange. Redsıs NuGet paketini kullanarak bir önbellek istemcisi yapılandırma
* Yönetilen Önbellek Hizmeti kodunu geçir
  * Connectionçoğullayıcı sınıfını kullanarak önbelleğe bağlanma
  * Önbellekteki temel veri türlerine erişin
  * Önbellekte .NET nesneleriyle çalışma
* Redsıs için ASP.NET oturum durumunu ve çıktı önbelleğini Azure önbelleğine geçirme 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Redsıs için Yönetilen Önbellek Hizmeti özelliklerini Azure önbelleğine eşleme
Azure Yönetilen Önbellek Hizmeti ve Redsıs için Azure önbelleği benzerdir ancak bazı özelliklerini farklı yollarla uygular. Bu bölümde bazı farklılıklar açıklanmakta ve Reda için Azure önbelleğinde Yönetilen Önbellek hizmeti özelliklerinin uygulanması için yönergeler sağlanmaktadır.

| Yönetilen Önbellek Hizmeti özelliği | Yönetilen Önbellek hizmeti desteği | Redsıs desteği için Azure önbelleği |
| --- | --- | --- |
| Adlandırılmış önbellekler |Varsayılan bir önbellek yapılandırılır ve standart ve Premium önbellek tekliflerinde, isterseniz en fazla dokuz ek adlandırılmış önbellek yapılandırılabilir. |Redsıs için Azure önbelleğinde, adlandırılmış önbelleklere benzer bir işlevsellik uygulamak için kullanılabilecek yapılandırılabilir sayıda veritabanı (varsayılan değer 16) vardır. Daha fazla bilgi için bkz. [Redis veritabanı nedir?](cache-faq.md#what-are-redis-databases) ve [Varsayılan Redis sunucu yapılandırması](cache-configure.md#default-redis-server-configuration). |
| Yüksek Kullanılabilirlik |Standart ve Premium önbellek tekliflerindeki Önbellekteki öğeler için yüksek kullanılabilirlik sağlar. Bir hata nedeniyle öğeler kaybolursa, önbellekteki öğelerin yedek kopyaları hala kullanılabilir. İkincil önbelleğe yazma işlemleri zaman uyumlu olarak yapılır. |Yüksek kullanılabilirlik, iki düğümlü birincil/çoğaltma yapılandırmasına (bir Premium önbellekteki her parça birincil/çoğaltma çiftine sahiptir) sahip olan standart ve Premium önbellek teklifleriyle kullanılabilir. Çoğaltmaya yazma işlemleri zaman uyumsuz olarak yapılır. Daha fazla bilgi için bkz. [redsıs fiyatlandırması Için Azure önbelleği](https://azure.microsoft.com/pricing/details/cache/). |
| Bildirimler |Adlandırılmış bir önbellekte çeşitli önbellek işlemleri gerçekleşince istemcilerin zaman uyumsuz bildirimler almasına izin verir. |İstemci uygulamaları, bildirimlere benzer bir işlevsellik elde etmek için Redsıs pub/Sub veya [keyspace bildirimlerini](cache-configure.md#keyspace-notifications-advanced-settings) kullanabilir. |
| Yerel önbellek |Daha hızlı erişim için, önbelleğe alınmış nesnelerin bir kopyasını istemcide yerel olarak depolar. |İstemci uygulamalarının bu işlevselliği bir sözlük veya benzer bir veri yapısı kullanarak uygulaması gerekir. |
| Çıkarma Ilkesi |Hiçbiri veya LRU. Varsayılan ilke LRU ' dır. |Redsıs için Azure Cache şu çıkarma ilkelerini destekler: geçici-LRU, AllKeys-LRU, volatile-Random, AllKeys-Random, volatile-TTL, noçıkarma. Varsayılan ilke geçici-LRU ' dır. Daha fazla bilgi için bkz. [varsayılan redsıs sunucu yapılandırması](cache-configure.md#default-redis-server-configuration). |
| Süre sonu Ilkesi |Varsayılan süre sonu ilkesi mutlak ve varsayılan süre sonu aralığı 10 dakikadır. Kaydırma ve hiçbir zaman ilke de kullanılabilir değildir. |Varsayılan olarak önbellekteki öğelerin süresi dolmaz, ancak önbellek kümesi aşırı yüklemeleri kullanılarak yazma esasına göre bir süre sonu yapılandırılabilir. |
| Bölgeler ve etiketleme |Bölgeler, önbelleğe alınmış öğeler için alt gruplar. Bölgeler Ayrıca, Etiketler adlı ek tanımlayıcı dizelerle önbelleğe alınmış öğelerin ek açıklamasını destekler. Bölgeler, bu bölgedeki herhangi bir etiketli öğe üzerinde arama işlemleri gerçekleştirme yeteneğini destekler. Bir bölgedeki tüm öğeler önbellek kümesinin tek bir düğümü içinde bulunur. |Redin için bir Azure önbelleği, tek bir düğümden oluşur (Redsıs kümesi etkin değilse), Yönetilen Önbellek Hizmeti bölgeleri kavramı uygulanmaz. Redsıs anahtarları alırken arama ve joker karakter işlemlerini destekler, böylece açıklayıcı Etiketler anahtar adlarına katıştırılabildiğinden ve öğeleri daha sonra almak için kullanılabilir. Red, kullanarak etiketleme çözümü uygulama örneği için bkz. [redsıs ile önbellek etiketleme uygulama](https://stackify.com/implementing-cache-tagging-redis/). |
| Getir |Yönetilen Önbellek NetDataContractSerializer, BinaryFormatter ve özel serileştiricilerin kullanımını destekler. Varsayılan değer NetDataContractSerializer ' dir. |Bu, istemci uygulamanın, seri hale getirme seçeneği ile istemci uygulama geliştiricisine kadar olan, önbelleğe alınmadan önce .NET nesnelerini seri hale getirme sorumluluğundadır. Daha fazla bilgi ve örnek kod için bkz. [önbellekte .NET nesneleriyle çalışma](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Önbellek öykünücüsü |Yönetilen Önbellek, yerel bir önbellek öykünücüsü sağlar. |Redin için Azure önbelleğinde öykünücü yoktur, ancak öykünücü deneyimi sağlamak için [Redis-Server. exe ' nin MSOpenTech derlemesini yerel olarak çalıştırabilirsiniz](cache-faq.md#cache-emulator) . |

## <a name="choose-a-cache-offering"></a>Bir önbellek teklifi seçin
Redo için Microsoft Azure Cache aşağıdaki katmanlarda kullanılabilir:

* **Temel** – Tek düğümlü. 53 GB'a kadar birden çok boyut.
* **Standart** – İki düğümlü Birincil/Çoğaltma. 53 GB'a kadar birden çok boyut. %99,9 SLA.
* **Premium** – İki düğümlü Birincil/Çoğaltma, En fazla 10 parça. 6 GB ila 1,2 TB arasında birden çok boyut. [Redis kümesi](cache-how-to-premium-clustering.md), [Redis kalıcılığı](cache-how-to-premium-persistence.md), and [Azure Virtual Network](cache-how-to-premium-vnet.md) dahil tüm Standart katman özellikleri ve fazlası. %99,9 SLA.

Her katman özellikler ve fiyatlandırma açısından farklıdır. Özellikler bu kılavuzun ilerleyen kısımlarında ele alınmıştır ve fiyatlandırma hakkında daha fazla bilgi için bkz. [önbellek fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/cache/).

Geçiş için bir başlangıç noktası, önceki Yönetilen Önbellek Hizmeti önbelleğinizin boyutuyla eşleşen boyutu seçip uygulamanızın gereksinimlerine bağlı olarak ölçeği büyütme veya küçültme olur. Redin teklifi için doğru Azure önbelleğini seçme hakkında daha fazla bilgi için bkz. [redne Için Azure önbelleği ve boyutu kullanmalıyım](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Önbellek oluşturma
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Önbellek Istemcilerini yapılandırma
Önbellek oluşturulup yapılandırıldıktan sonra, bir sonraki adım Yönetilen Önbellek hizmeti yapılandırmasını kaldırmak ve önbellek istemcilerinin önbelleğe erişebilmesi için Redsıs yapılandırması ve başvuruları için Azure önbelleğini eklemektir.

* Yönetilen Önbellek hizmeti yapılandırmasını kaldırma
* StackExchange. Redsıs NuGet paketini kullanarak bir önbellek istemcisi yapılandırma

### <a name="remove-the-managed-cache-service-configuration"></a>Yönetilen Önbellek hizmeti yapılandırmasını kaldırma
İstemci uygulamaları Redsıs için Azure önbelleği için yapılandırılabilmesi için, Yönetilen Önbellek Hizmeti NuGet paketi kaldırılarak var olan Yönetilen Önbellek Hizmeti Yapılandırması ve derleme başvurularının kaldırılması gerekir.

Yönetilen Önbellek Hizmeti NuGet paketini kaldırmak için **Çözüm Gezgini** ' de istemci projesine sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin. **Yüklü paketler** düğümünü seçin ve W**ındowsazure. Caching** yazarak yüklü paketleri ara kutusuna yazın. **Windows** **Azure önbelleğini** (veya NuGet paketinin sürümüne bağlı olarak **Windows** **Azure** önbelleği) seçin, **Kaldır**' a ve ardından **Kapat**' a tıklayın.

![Azure Yönetilen Önbellek Hizmeti NuGet paketini kaldır](./media/cache-migrate-to-redis/IC757666.jpg)

Yönetilen Önbellek Hizmeti NuGet paketini kaldırmak, Yönetilen Önbellek Hizmeti derlemelerini ve Yönetilen Önbellek Hizmeti girdilerini, istemci uygulamasının App. config veya Web. config dosyasında kaldırır. NuGet paketi kaldırılırken bazı özelleştirilmiş ayarlar kaldırılabileceğinden, Web. config veya App. config dosyasını açın ve aşağıdaki öğelerin kaldırıldığından emin olun.

`dataCacheClients` girişinin `configSections` öğesinden kaldırıldığından emin olun. Tüm `configSections` öğesini kaldırmayın; varsa `dataCacheClients` girdisini kaldırmanız yeterlidir.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

`dataCacheClients` bölümünün kaldırıldığından emin olun. `dataCacheClients` bölümü aşağıdaki örneğe benzer olacaktır.

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

Yönetilen Önbellek Hizmeti Yapılandırması kaldırıldıktan sonra, aşağıdaki bölümde açıklandığı gibi Önbellek istemcisini yapılandırabilirsiniz.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>StackExchange. Redsıs NuGet paketini kullanarak bir önbellek istemcisi yapılandırma
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Yönetilen Önbellek Hizmeti kodunu geçir
Redsıs istemcisi için StackExchange. Azure önbelleği için API, yönetilen önbellek hizmetine benzerdir. Bu bölüm, farklılıklara genel bakış sunar.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Connectionçoğullayıcı sınıfını kullanarak önbelleğe bağlanma
Yönetilen Önbellek hizmetinde, önbelleğe yönelik bağlantılar `DataCacheFactory` ve `DataCache` sınıfları tarafından işlenir. Redsıs için Azure önbelleğinde bu bağlantılar `ConnectionMultiplexer` sınıfı tarafından yönetilir.

Aşağıdaki using ifadesini, önbelleğe erişmek istediğiniz herhangi bir dosyanın en üstüne ekleyin.

```csharp
using StackExchange.Redis
```

Bu ad alanı çözümlenmezse, StackExchange. Redsıs NuGet paketini [hızlı başlangıç: redsıs için bir .NET uygulaması ile birlikte kullanın](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> StackExchange. Redsıs istemcisinin .NET Framework 4 veya üzeri bir değer gerektirdiğini unutmayın.
> 
> 

Redsıs örneği için bir Azure önbelleğine bağlanmak üzere statik `ConnectionMultiplexer.Connect` yöntemini çağırın ve bitiş noktasını ve anahtarı geçirin. Uygulamanızda bir `ConnectionMultiplexer` örneği paylaşmaya ilişkin bir yaklaşım, aşağıdaki örneğe benzer bir bağlı örnek döndüren statik özelliğe sahip olmaktır. Bu yaklaşım, tek bir bağlı `ConnectionMultiplexer` örneğini başlatmak için iş parçacığı güvenli bir yol sağlar. Bu örnekte `abortConnect` false olarak ayarlanır; bu da, önbellek bağlantısı kurulamazsa bile çağrının başarılı olacağı anlamına gelir. `ConnectionMultiplexer` temel özelliklerinden biri ağ sorunu ya da diğer nedenler çözümlendiğinde önbellek bağlantısını otomatik olarak geri yüklemesidir.

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Önbellek örneğiniz için önbellek uç noktası, anahtarlar ve bağlantı noktaları, **redsıs** dikey penceresinde elde edilebilir. Daha fazla bilgi için bkz. [redsıs özellikleri Için Azure önbelleği](cache-configure.md#properties).

Bağlantı kurulduktan sonra, `ConnectionMultiplexer.GetDatabase` yöntemini çağırarak Redsıs veritabanı için Azure önbelleğine bir başvuru döndürün. `GetDatabase` yönteminden döndürülen nesne küçük, geçişli bir nesnedir ve depolanması gerekmez.

```csharp
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

StackExchange. Redo istemcisi, önbellekteki öğelere erişmek ve bunları depolamak için `RedisKey` ve `RedisValue` türlerini kullanır. Bu türler, dize dahil olmak üzere en temel dil türlerine eşlenir ve genellikle doğrudan kullanılmaz. Redsıs dizeleri en temel redin değeri türüdür ve serileştirilmiş ikili akışlar dahil olmak üzere pek çok veri türü içerebilir ve doğrudan türü kullanmıyorsanız, adında `String` içeren yöntemleri kullanacaksınız. Çoğu temel veri türü için, koleksiyonları veya diğer redo veri türlerini önbellekte saklamadığınız müddetçe `StringSet` ve `StringGet` yöntemlerini kullanarak önbellekten öğeleri depolar ve alırsınız. 

`StringSet` ve `StringGet`, Yönetilen Önbellek Hizmeti `Put` ve `Get` yöntemlerine benzerdir ve bir .NET nesnesini, önbelleğe almadan önce serileştirmelisiniz. 

`StringGet`çağrılırken, nesne varsa, döndürülür ve null döndürülür. Bu durumda, istenen veri kaynağından değeri alabilir ve sonraki kullanım için önbellekte saklayabilirsiniz. Bu model, önbellek stili olarak bilinir.

Bir öğenin önbellekte sona erme tarihini belirtmek için, `TimeSpan` dizesine ait `StringSet` parametresini kullanın.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Redsıs için Azure Cache, .NET nesneleriyle ve ilkel veri türleriyle birlikte çalışabilir, ancak bir .NET nesnesi önbelleğe alınmadan önce serileştirilmelidir. Bu seri hale getirme, uygulama geliştiricisinin sorumluluğundadır ve bu sayede, serileştirici seçimi için geliştirici esnekliği sağlar. Daha fazla bilgi ve örnek kod için bkz. [önbellekte .NET nesneleriyle çalışma](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Redsıs için ASP.NET oturum durumunu ve çıktı önbelleğini Azure önbelleğine geçirme
Redin için Azure önbelleğinde hem ASP.NET oturum durumu hem de sayfa çıktısı önbelleği için sağlayıcılar vardır. Uygulamanızı bu sağlayıcıların Yönetilen Önbellek Hizmeti sürümlerini kullanarak geçirmek için, önce mevcut bölümleri Web. config 'nizden kaldırın ve ardından sağlayıcıların Redl sürümleri için Azure önbelleğini yapılandırın. Redsıs ASP.NET sağlayıcıları için Azure önbelleğini kullanma hakkında yönergeler için bkz. [ASP.NET oturum durumu sağlayıcısı](cache-aspnet-session-state-provider.md) Redsıs için Azure önbelleği ve [Redsıs için azure önbelleği Için ASP.net çıkış önbelleği sağlayıcısı](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Sonraki adımlar
Öğreticiler, örnekler, videolar ve daha fazlası için [redsıs belgelerinin Azure önbelleğini](https://azure.microsoft.com/documentation/services/cache/) inceleyin.

