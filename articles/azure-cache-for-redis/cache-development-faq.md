---
title: Redsıs geliştirme hakkında Azure önbelleği SSS
description: Redsıs için Azure önbelleği geliştirmeye yardımcı olacak yaygın soruların yanıtlarını öğrenin
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: bafd8a9752d2587ec52fe586e442e3bfc86d7537
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585777"
---
# <a name="azure-cache-for-redis-development-faqs"></a>Redsıs geliştirme hakkında Azure önbelleği SSS

Bu makalede, redin için Azure önbelleği için geliştirme hakkında sık sorulan soruların yanıtları sağlanır.

## <a name="common-questions-and-answers"></a>Yaygın sorular ve yanıtları
Bu bölümde aşağıdaki SSS ele alınmaktadır:

* [Redsıs için Azure önbelleği 'ni kullanmaya nasıl başlarım?](#how-can-i-get-started-with-azure-cache-for-redis)
* [StackExchange. Redsıs yapılandırma seçenekleri ne olur?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Redsıs istemcileri için hangi Azure önbelleğinde kullanabilirim?](#what-azure-cache-for-redis-clients-can-i-use)
* [Redsıs için Azure önbelleği için yerel bir öykünücü var mı?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Redsıs komutlarını nasıl çalıştırabilirim?](#how-can-i-run-redis-commands)
* [Redin için Azure Cache 'In bir MSDN sınıf kitaplığı başvurusu neden değil?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference)
* [Redsıs için Azure önbelleğini PHP oturum önbelleği olarak kullanabilir miyim?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Redsıs veritabanları nelerdir?](#what-are-redis-databases)

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Redsıs için Azure önbelleği 'ni kullanmaya nasıl başlarım?
Redsıs için Azure önbelleği 'ni kullanmaya başlamanın birkaç yolu vardır.

* [.Net](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.net](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md)ve [Python](cache-python-get-started.md)için kullanılabilen öğreticilerimizden birine bakabilirsiniz.
* [Redin için Microsoft Azure cache kullanarak High-Performance uygulamalarının nasıl](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/)oluşturulacağını izleyebilirsiniz.
* Redin 'in nasıl kullanılacağını görmek için, projenizin geliştirme diliyle eşleşen istemciler için istemci belgelerini kullanıma alabilirsiniz. Redsıs için Azure Cache ile kullanılabilen birçok Red, istemcisi vardır. Redsıs istemcilerinin bir listesi için bkz [https://redis.io/clients](https://redis.io/clients) ..

Henüz bir Azure hesabınız yoksa şunları yapabilirsiniz:

* [Ücretsiz bir Azure hesabı açın](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Ücretli Azure hizmetlerini denemek için kullanabileceğiniz krediler alırsınız. Krediler bitmiş olsa bile hesabı sürdürebilir ve ücretsiz Azure hizmet ve özelliklerinden faydalanabilirsiniz.
* [Visual Studio abone avantajları etkinleştirin](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). MSDN aboneliğiniz, ücretli Azure hizmetlerinizi kullanabildiğiniz her ay size kredi verir.

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>StackExchange. Redsıs yapılandırma seçenekleri ne olur?
StackExchange. redin birçok seçeneği vardır. Bu bölüm bazı yaygın ayarlardan bazılarıdır. StackExchange. Redu seçenekleri hakkında daha ayrıntılı bilgi için bkz. [StackExchange. redsıs yapılandırması](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Description | Öneri |
| --- | --- | --- |
| AbortOnConnectFail |Doğru olarak ayarlandığında, ağ hatasından sonra bağlantı yeniden bağlanmaz. |False olarak ayarlayın ve StackExchange. redin otomatik olarak yeniden bağlanmasına izin verin. |
| ConnectRetry |İlk bağlantı sırasında bağlantı denemelerinin yinelenme sayısı. |Rehberlik için aşağıdaki notlara bakın. |
| ConnectTimeout |Connect işlemlerinde MS olarak zaman aşımı. |Rehberlik için aşağıdaki notlara bakın. |

Genellikle istemcinin varsayılan değerleri yeterlidir. İş yükünüze göre seçeneklerde ince ayar yapabilirsiniz.

* **Yeniden deneme sayısı**
  * ConnectRetry ve ConnectTimeout için genel rehberlik hızlı bir şekilde başarısız olur ve yeniden dener. Bu kılavuz, iş yükünüze ve istemcinizin bir Redi komutu vermesi ve yanıt alması için ne kadar süre harcandığından elde edilir.
  * StackExchange. redin bağlantı durumunu denetlemek ve kendiniz yeniden bağlanmak yerine otomatik olarak yeniden bağlanmasına izin verin. **Connectionçoğullayıcı. IsConnected özelliğini kullanmaktan kaçının**.
  * Kar Balling-bazen yeniden denemekte olduğunuz ve yeniden deneme karından ve hiçbir zaman kurtarmadığı bir sorunla karşılaşabilirsiniz. Kar baletini oluşursa, Microsoft düzenleri & Yöntemler grubu tarafından yayımlanan, [yeniden deneme genel Kılavuzu](/azure/architecture/best-practices/transient-faults) ' nda açıklandığı gibi bir üstel geri alma algoritması kullanmayı göz önünde bulundurmanız gerekir.
  
* **Zaman aşımı değerleri**
  * İş yükünüzü değerlendirin ve değerleri uygun şekilde ayarlayın. Büyük değerleri depoluyorsanız, zaman aşımını daha yüksek bir değere ayarlayın.
  * `AbortOnConnectFail`False olarak ayarlayın ve StackExchange. redin sizin için yeniden bağlanmasına izin verin.
  * Uygulama için tek bir Connectionçoğullayıcı örneği kullanın. Bir bağlantı özelliği tarafından döndürülen tek bir örnek oluşturmak için bir LazyConnection kullanabilirsiniz, bu, [Connectionçoğullayıcı sınıfı kullanılarak önbelleğe bağlanma](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)bölümünde gösterilmiştir.
  * Özelliği, `ConnectionMultiplexer.ClientName` tanılama amacıyla bir uygulama örneği benzersiz adı olarak ayarlayın.
  * `ConnectionMultiplexer`Özel iş yükleri için birden çok örnek kullanın.
    * Uygulamanızda farklı yükleme yaptıysanız bu modeli takip edebilirsiniz. Örnek:
    * Büyük anahtarlarla ilgilenirken bir Çoğullayıcı olabilir.
    * Küçük anahtarlarla ilgilenirken bir Çoğullayıcı olabilir.
    * Bağlantı zaman aşımları için farklı değerler ayarlayabilir ve kullandığınız her bir Connectionçoğullayıcı için yeniden deneme mantığı yapabilirsiniz.
    * Tanılama konusunda `ClientName` yardımcı olmak için her bir Çoğullayıcı özelliğini ayarlayın.
    * Bu kılavuz, başına daha kolay gecikme süresine neden olabilir `ConnectionMultiplexer` .

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Redsıs istemcileri için hangi Azure önbelleğinde kullanabilirim?
Redin hakkında harika şeyler birçok farklı geliştirme dilini destekleyen çok sayıda istemci vardır. İstemcilerin geçerli listesi için bkz. [redsıs istemcileri](https://redis.io/clients). Birçok farklı dili ve istemciyi kapsayan öğreticiler için bkz. [Azure Cache 'ı redsıs için kullanma](cache-dotnet-how-to-use-azure-redis-cache.md) ve içerik tablosundaki eşdüzey makaleler.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Redsıs için Azure önbelleği için yerel bir öykünücü var mı?
[Redsıs](https://github.com/MSOpenTech/redis/releases/) Için Azure önbelleği için yerel bir öykünücü yoktur, ancak aşağıdaki örnekte gösterildiği gibi yerel bir önbellek öykünücüsünde benzer bir deneyim almak Için redis-server.exe MSOpenTech sürümünü yerel makinenizde çalıştırabilir ve buna bağlanabilirsiniz:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        // Connect to a locally running instance of Redis to simulate
        // a local cache emulator experience.
        return ConnectionMultiplexer.Connect("127.0.0.1:6379");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

İsterseniz redo [. conf](https://redis.io/topics/config) dosyasını redsıs Için çevrimiçi Azure önbelleğiniz için [varsayılan önbellek ayarlarıyla](cache-configure.md#default-redis-server-configuration) daha yakından eşleşecek şekilde yapılandırabilirsiniz.

### <a name="how-can-i-run-redis-commands"></a>Redsıs komutlarını nasıl çalıştırabilirim?
Redsıs [komutlarında listelenen](https://redis.io/commands#) komutların herhangi birini, Redsıs [için Azure önbelleğinde desteklenmeyen](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)redsıs komutlarında listelenen komutlardan birini kullanabilirsiniz. Redsıs komutlarını çalıştırmak için birkaç seçeneğiniz vardır.

* Standart veya Premium önbelleğiniz varsa, redsıs [konsolunu](cache-configure.md#redis-console)kullanarak redsıs komutlarını çalıştırabilirsiniz. Redin konsolu, Azure portal Redsıs komutlarını çalıştırmak için güvenli bir yol sağlar.
* Redsıs komut satırı araçlarını da kullanabilirsiniz. Bunları kullanmak için aşağıdaki adımları gerçekleştirin:
* [Redsıs komut satırı araçlarını](https://github.com/MSOpenTech/redis/releases/)indirin.
* Kullanarak önbelleğe bağlanın `redis-cli.exe` . Aşağıdaki örnekte gösterildiği gibi-a anahtarını ve-a anahtarını kullanarak önbellek uç noktasını geçirin:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Redsıs komut satırı araçları TLS bağlantı noktası ile çalışmaz, ancak reddo `stunnel` [komut satırı aracının reddo Için Azure Cache Ile nasıl kullanılacağına ilişkin](./cache-how-to-redis-cli-tool.md) yönergeleri IZLEYEREK, araçları TLS bağlantı noktasına güvenli bir şekilde bağlamak için gibi bir yardımcı programı kullanabilirsiniz.
>
>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference"></a>Redin için Azure Cache 'In bir MSDN sınıf kitaplığı başvurusu neden değil?
Redo için Microsoft Azure önbelleği, popüler açık kaynaklı bellek içi veri deposuna ve redin 'a göre belirlenir. Birçok programlama dili için çok çeşitli [redin istemcileri](https://redis.io/clients) erişebilir. Her istemcinin [redsıs komutları](https://redis.io/commands)kullanılarak redsıs örneği Için Azure önbelleğine çağrı yapan kendi API 'si vardır.

Her istemci farklı olduğundan, MSDN 'de tek bir merkezi sınıf başvurusu yoktur ve her istemci kendi başvuru belgelerini korur. Başvuru belgelerine ek olarak, farklı diller ve önbellek istemcileri kullanarak redin için Azure önbelleği ile çalışmaya nasıl başladığının gösterildiği çeşitli öğreticiler vardır. Bu öğreticilere erişmek için bkz. [Azure Cache 'ı redsıs için kullanma](cache-dotnet-how-to-use-azure-redis-cache.md) ve içerik tablosundaki eşdüzey makaleler.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Redsıs için Azure önbelleğini PHP oturum önbelleği olarak kullanabilir miyim?
Evet, Redsıs için Azure önbelleğini PHP oturum önbelleği olarak kullanmak için, içindeki redin örneği için Azure önbelleğiniz için bağlantı dizesini belirtin `session.save_path` .

> [!IMPORTANT]
> Redsıs için Azure önbellek 'yi bir PHP oturum önbelleği olarak kullanırken, aşağıdaki örnekte gösterildiği gibi, önbelleğe bağlanmak için kullanılan güvenlik anahtarını URL 'ye kodlamanız gerekir:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Anahtar URL kodlanmışsa, şöyle bir ileti içeren bir özel durum alabilirsiniz: `Failed to parse session.save_path`
>

Redin için Azure önbelleğini Phpredıs istemcisiyle PHP oturum önbelleği olarak kullanma hakkında daha fazla bilgi için bkz. [php oturum işleyicisi](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Redsıs veritabanları nelerdir?

Redsıs veritabanları, aynı redin örneği içindeki verilerin yalnızca mantıksal bir ayrımı olur. Önbellek belleği, belirli bir veritabanının tüm veritabanları ve gerçek bellek tüketimi arasında paylaşılır ve bu veritabanında depolanan anahtarlara/değerlere göre değişir. Örneğin, bir C6 önbelleğinde 53 GB bellek ve bir P5 120 GB vardır. Tüm 53 GB/120 GB 'yi tek bir veritabanına koyabilirsiniz veya birden çok veritabanı arasında ayırabilirsiniz. 

> [!NOTE]
> Redsıs için, kümeleme etkinken Premium bir Azure önbelleği kullanılırken yalnızca 0 veritabanı kullanılabilir. Bu sınırlama, iç Reddir kısıtlamasıdır ve redin için Azure önbelleğine özgü değildir. Daha fazla bilgi için bkz. [Kümeleme kullanmak için istemci Uygulamam üzerinde herhangi bir değişiklik yapmam gerekir mi?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering).
> 
> 

## <a name="next-steps"></a>Sonraki adımlar

[Redsıs SSS için diğer Azure önbelleği](cache-faq.md)hakkında bilgi edinin.