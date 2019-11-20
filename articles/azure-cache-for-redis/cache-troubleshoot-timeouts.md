---
title: Redsıs zaman aşımları için Azure önbelleğinde sorun giderme
description: Redsıs için Azure önbelleğiyle ilgili genel zaman aşımı sorunlarını çözmeyi öğrenin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: yegu
ms.openlocfilehash: e58b305a43cc5ad339fb87b9b8a09af04c410839
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121374"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Redsıs zaman aşımları için Azure önbelleğinde sorun giderme

Bu bölümde, Redsıs için Azure önbelleğine bağlanılırken oluşan zaman aşımı sorunlarını giderme işlemi anlatılmaktadır.

- [Redsıs sunucusu düzeltme eki uygulama](#redis-server-patching)
- [StackExchange. Redsıs zaman aşımı özel durumları](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Bu kılavuzdaki sorun giderme adımlarından bazıları Redsıs komutları çalıştırma ve çeşitli performans ölçümlerini izleme yönergelerini içerir. Daha fazla bilgi ve yönergeler için [ek bilgi](#additional-information) bölümündeki makalelere bakın.
>

## <a name="redis-server-patching"></a>Redsıs sunucusu düzeltme eki uygulama

Redsıs için Azure önbelleği, sağladığı yönetilen hizmet işlevselliğinin bir parçası olarak sunucu yazılımını düzenli olarak güncelleştirir. Bu [düzeltme eki uygulama](cache-failover.md) , büyük ölçüde sahnenin arkasında yer alır. Redsıs sunucu düğümlerine düzeltme eki eklenen yük devretme işlemleri sırasında, bu düğümlere bağlı olan Redsıs istemcileri bu düğümler arasında bağlantı kurulmasını geçici zaman aşımları ile karşılaşabilir. Bkz. bir yük devretme, uygulamanızda hangi yan etkilere sahip olabilir ve düzeltme eki uygulama işlemlerini nasıl geliştirebileceğiniz hakkında daha fazla bilgi için [istemci uygulamamı nasıl etkiler](cache-failover.md#how-does-a-failover-affect-my-client-application) ?

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange. Redsıs zaman aşımı özel durumları

StackExchange. Redo, varsayılan 1000 ms değeri ile zaman uyumlu işlemler için `synctimeout` adlı bir yapılandırma ayarı kullanır. Zaman uyumlu bir çağrı bu süre içinde tamamlanmazsa, StackExchange. Redsıs istemcisi aşağıdaki örneğe benzer bir zaman aşımı hatası oluşturur:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Bu hata iletisi, sorunun nedenine ve olası çözümüne işaret etmenize yardımcı olabilecek ölçümler içerir. Aşağıdaki tabloda hata iletisi ölçümleriyle ilgili ayrıntılar yer almaktadır.

| Hata iletisi ölçümü | Ayrıntılar |
| --- | --- |
| inst |Son zaman dilimi: 0 komutları yayımlandığında |
| SETUPAPI |Yuva Yöneticisi `socket.select`yapıyor. Bu, işletim sisteminin yapması gereken bir yuva olduğunu belirtmesidir. Okuyucu herhangi bir şey olduğunu düşünmediği için ağ üzerinden etkin bir şekilde okunamaz |
| kuyruk |Devam eden 73 Toplam işlem var |
| Qu |işlemin 6 ' dan devam eden işlemler gönderilmemiş kuyrukta ve henüz giden ağa yazılmadı |
| QS |devam eden işlemlerin 67 sunucuya gönderilmesi ancak bir yanıt henüz kullanılamıyor. Yanıt `Not yet sent by the server` veya `sent by the server but not yet processed by the client.` olabilir |
| QC |sürmekte olan işlemlerin 0 ' a yanıt gördük ancak tamamlanma döngüsünde beklediği için henüz tamamlandı olarak işaretlenmedi |
| WR |Etkin bir yazıcı (yani, 6 gönderilmemiş isteğin yoksayılmaması anlamına gelir) bayt/activeyazarlar |
| 'Ndaki |Etkin okuyucu yok ve NIC baytları/activereaders üzerinde okunabilecek sıfır bayt var |

Olası temel nedenleri araştırmak için aşağıdaki adımları kullanabilirsiniz.

1. En iyi uygulama olarak, StackExchange. Redsıs istemcisini kullanırken bağlanmak için aşağıdaki kalıbı kullandığınızdan emin olun.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    Daha fazla bilgi için bkz. [StackExchange. redsıs kullanarak önbelleğe bağlanma](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Sunucunuzun ve istemci uygulamanızın Azure 'da aynı bölgede bulunduğundan emin olun. Örneğin, önbelleğiniz Doğu ABD, ancak istemci Batı ABD ve istek `synctimeout` aralığında tamamlanmadığında zaman aşımları alabilir veya yerel geliştirme makinenizden hata ayıklaması yaparken zaman aşımları elde edebilirsiniz. 

    Önbelleğin ve istemcisinin aynı Azure bölgesinde olması kesinlikle önerilir. Çapraz bölge çağrıları içeren bir senaryonuz varsa, bağlantı dizesine bir `synctimeout` özelliği ekleyerek `synctimeout` aralığını varsayılan 1000-ms aralığından daha yüksek bir değere ayarlamanız gerekir. Aşağıdaki örnek, StackExchange için bir bağlantı dizesinin kod parçacığını gösterir. redsıs için Azure önbelleği, 2000 MS `synctimeout`.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. [StackExchange. Redsıs NuGet paketinin](https://www.nuget.org/packages/StackExchange.Redis/)en son sürümünü kullandığınızdan emin olun. En son sürüme sahip olacak şekilde, zaman aşımlarını daha sağlam hale getirmek için kodda sürekli olarak düzeltilen hatalar vardır.
1. İstekleriniz sunucu veya istemcideki bant genişliği sınırlamalarına göre bağlanmışsa, tamamlanmaları daha uzun sürer ve zaman aşımları oluşmasına neden olabilir. Zaman aşımının sunucuda ağ bant genişliği olmasından dolayı olup olmadığını görmek için bkz. [sunucu tarafı bant genişliği sınırlaması](cache-troubleshoot-server.md#server-side-bandwidth-limitation). Zaman aşımının istemci ağ bant genişliği nedeniyle olup olmadığını görmek için bkz. [istemci tarafı bant genişliği sınırlaması](cache-troubleshoot-client.md#client-side-bandwidth-limitation).
1. Sunucuda veya istemcide CPU 'ya bağlı mı?

   - İstemcinizdeki CPU ile bağlı olup olmadığınızı denetleyin. Yüksek CPU, isteğin `synctimeout` aralığı içinde işlenmemesine ve isteğin zaman aşımına gelmesine neden olabilir. Daha büyük bir istemci boyutuna geçme veya yükü dağıtma bu sorunu denetlemeye yardımcı olabilir.
   - CPU [önbelleği performans ölçümünü](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)IZLEYEREK sunucuda CPU bağlı olup olmadığınızı denetleyin. Redin CPU sınırının geldiği sırada gelen istekler bu isteklerin zaman aşımına yol açabilir. Bu durumu gidermek için, yükü bir Premium önbellekte birden fazla parçaya dağıtabilir veya daha büyük bir boyuta veya fiyatlandırma katmanına yükseltebilirsiniz. Daha fazla bilgi için bkz. [sunucu tarafı bant genişliği sınırlaması](cache-troubleshoot-server.md#server-side-bandwidth-limitation).
1. Sunucuda işlenmesi uzun zaman alan komutlar var mı? Redo-Server üzerinde işlenmesi uzun süren uzun süre çalışan komutlar zaman aşımına neden olabilir. Uzun süredir çalışan komutlar hakkında daha fazla bilgi için bkz. [uzun süreli komutlar](cache-troubleshoot-server.md#long-running-commands). Redsıs-CLI istemcisini veya [Redsıs konsolunu](cache-configure.md#redis-console)kullanarak redsıs örneği Için Azure önbelleğinize bağlanabilirsiniz. Ardından, beklenden daha yavaş istekler olup olmadığını görmek için [yavaşlatma log](https://redis.io/commands/slowlog) komutunu çalıştırın. Redsıs Server ve StackExchange. Redsıs, daha az sayıda büyük istek yerine birçok küçük istek için iyileştirilmiştir. Verilerinizi daha küçük parçalara bölmek buradaki şeyleri iyileştirebilecek.

    Redsıs-CLI ve Stunnel kullanarak önbelleğinizi SSL uç noktasına bağlama hakkında daha fazla bilgi için bkz. The [Redsıs Preview sürümü için ASP.NET oturum durumu sağlayıcısı duyuruldu](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Yüksek Redsıs sunucu yüklemesi zaman aşımına neden olabilir. `Redis Server Load` [önbelleği performans ölçümünü](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)izleyerek sunucu yükünü izleyebilirsiniz. Sunucu yükü 100 (en yüksek değer), redsıs sunucusunun meşgul olduğunu, boşta kalma süresi olmadan ve işleme isteklerini belirtir. Belirli isteklerin tüm sunucu yeteneklerini mi aldığını görmek için, önceki paragrafta açıklandığı gibi Yavaşgünlüğü komutunu çalıştırın. Daha fazla bilgi için bkz. yüksek CPU kullanımı/sunucu yükü.
1. İstemci tarafında ağ Blip oluşmasına neden olabilecek başka bir olay vardı mi? Ortak olaylar şunlardır: istemci örneklerinin sayısını ölçeği artırma veya azaltma, istemcinin yeni bir sürümünü dağıtma veya otomatik ölçeklendirme özelliği. Testinizde, otomatik ölçeklendirmeyi veya ölçeklendirmeyi artırma/azaltma, giden ağ bağlantısının birkaç saniye süreyle kaybolmasına neden olabilir. StackExchange. Redsıs kodu, bu tür olaylara esnektir ve yeniden bağlanır. Yeniden bağlanıldığında kuyruktaki isteklerin zaman aşımına uğrar.
1. Birkaç küçük istekten daha önce zaman aşımına uğramış bir istek var mı? Hata iletisindeki parametre `qs`, istemciden sunucuya kaç istek gönderildiğini söyler, ancak bir yanıt işlenmedi. StackExchange. redin tek bir TCP bağlantısı kullandığından ve aynı anda yalnızca bir yanıt okuyabildiğinden bu değer büyümeye devam edebilir. İlk işlem zaman aşımına uğrasa da, sunucudan veya sunucudan daha fazla verinin gönderilmesini durdurmaz. Diğer istekler, büyük istek tamamlanana kadar engellenecek ve zaman aşımlarına neden olabilir. Tek bir çözüm, önbelleğinizin iş yükünüz için yeterince büyük olmasını ve büyük değerleri daha küçük parçalara bölünmesini sağlayarak zaman aşımları olasılığını en aza indirmektir. Başka bir olası çözüm, istemcinizdeki `ConnectionMultiplexer` nesnelerinin bir havuzunu kullanmaktır ve yeni bir istek gönderirken en az yüklenen `ConnectionMultiplexer` seçer. Birden çok bağlantı nesnesi arasında yükleme, tek bir zaman aşımının diğer isteklerin da zaman aşımına uğramasına neden olur.
1. `RedisSessionStateProvider`kullanıyorsanız, yeniden deneme zaman aşımını doğru ayarlamış olduğunuzdan emin olun. `retryTimeoutInMilliseconds` `operationTimeoutInMilliseconds`daha yüksek olmalıdır, aksi takdirde yeniden deneme gerçekleşmez. Aşağıdaki örnekte `retryTimeoutInMilliseconds` 3000 olarak ayarlanmıştır. Daha fazla bilgi için bkz. [ASP.NET oturum durumu sağlayıcısı, redsıs Için Azure önbelleği](cache-aspnet-session-state-provider.md) ve [oturum durumu sağlayıcısı ve çıkış önbelleği sağlayıcısı yapılandırma parametrelerinin nasıl kullanılacağı](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. `Used Memory RSS` ve `Used Memory`[Izleyerek](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) redo Server Için Azure önbelleğindeki bellek kullanımını kontrol edin. Çıkarma ilkesi mevcutsa, `Used_Memory` önbellek boyutuna ulaştığında redo, anahtarları çıkarma işlemi başlatır. İdeal olarak, `Used Memory RSS` `Used memory`göre yalnızca biraz daha yüksek olmalıdır. Büyük bir fark, bellek parçalanması (iç veya dış) anlamına gelir. `Used Memory RSS` `Used Memory`daha az olduğunda, önbellek belleğinin bir kısmının işletim sistemi tarafından değiştirilmiş olması anlamına gelir. Bu değiştirme gerçekleşirse bazı önemli gecikme süreleri bekleyebilir. Redsıs 'in, ayırmaların bellek sayfalarına eşlenme üzerinde denetimi olmadığından, yüksek `Used Memory RSS` genellikle bellek kullanımında ani bir ani sonuçlardır. Redsıs sunucusu belleği serbest bıraktığı zaman ayırıcı belleği alır, ancak bu bellek sisteme geri dönüş gösterebilir veya olmayabilir. `Used Memory` değeri ile işletim sistemi tarafından bildirilen bellek tüketimi arasında bir tutarsızlık olabilir. Bellek, Redsıs tarafından kullanılmış, ancak sisteme geri verilmeyebilir. Bellek sorunlarını azaltmaya yardımcı olmak için aşağıdaki adımları gerçekleştirebilirsiniz:

   - Sistemdeki bellek sınırlamalarıyla çalışmaya devam edebilmeniz için önbelleği daha büyük bir boyuta yükseltin.
   - Eski değerlerin önceden çıkarılabilmesi için anahtarların süre sonu zamanlarını ayarlayın.
   - `used_memory_rss` önbelleği ölçümünü izleyin. Bu değer, önbelleğinin boyutuna yaklaşırsa, büyük olasılıkla performans sorunlarını görmeye başlayabilirsiniz. Premium önbellek kullanıyorsanız veya daha büyük bir önbellek boyutuna yükseltirseniz, verileri birden çok parça genelinde dağıtın.

   Daha fazla bilgi için bkz. [redsıs sunucusu 'Nda bellek baskısı](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>Ek bilgiler

- [Redsıs istemci tarafı sorunları için Azure Cache sorunlarını giderme](cache-troubleshoot-client.md)
- [Redsıs sunucu tarafı sorunları için Azure Cache sorunlarını giderme](cache-troubleshoot-server.md)
- [Önbelleğim performansını nasıl kıyaslarım ve test edebilirim?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Redsıs için Azure önbelleğini izleme](cache-how-to-monitor.md)
