---
title: Redis için Azure Cache'in en iyi yöntemleri
description: Bu en iyi yöntemleri izleyerek, redin için Azure önbelleğinizi etkin bir şekilde nasıl kullanacağınızı öğrenin.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: ae8d952a31a4f1b42cc4a653ffa81d88b3e813f8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311983"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Redis için Azure Cache'in en iyi yöntemleri 
Bu en iyi yöntemleri izleyerek Redsıs örneği için Azure önbelleğinizin performansını ve düşük maliyetli kullanımını en üst düzeye çıkarmanıza yardımcı olabilirsiniz.

## <a name="configuration-and-concepts"></a>Yapılandırma ve kavramlar
 * **Üretim sistemleri için standart veya Premium katmanını kullanın.**  Temel katman, veri çoğaltması olmayan ve SLA olmayan tek düğümlü bir sistemdir. Ayrıca en az C1 önbelleği kullanın.  C0 önbellekleri, paylaşılan bir CPU çekirdeği, küçük belleğe sahip olduklarından ve "gürültülü komşu" sorunları nedeniyle basit geliştirme ve test senaryolarına yöneliktir.

 * **Redthe 'ın bellek içi veri deposu olduğunu unutmayın.**  [Bu makalede](cache-troubleshoot-data-loss.md) , veri kaybının gerçekleşebileceği bazı senaryolar özetlenmektedir.

 * [Yazılım, düzeltme eki uygulama ve yük devretme nedeniyle](cache-failover.md) **bağlantı bliıt 'leri Işleyebileceğinden, sisteminizi geliştirin** .

 * Bellek baskısı koşullarına göre **sistem yanıt hızını artırmak için [MaxMemory ile ayrılmış ayarınızı](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) yapılandırın** .  Önemli bir ayırma ayarı, yazma ağır iş yükleri için özellikle önemlidir veya daha büyük değerler (100 KB veya daha fazla) redin içinde depoluyorsanız. Önbelleğinizin boyutunun %10 ' ü ile başlamalı ve yazma ağır yüklerinizin olması durumunda bu yüzdeyi artırmanız gerekir.

 * **Red, daha küçük değerlerle çalışır**, bu nedenle daha büyük verileri birden fazla anahtara chopping değerlendirin.  [Bu redin tartışmasında](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/), dikkatlice göz önünde bulundurmanız gereken bazı noktalar listelenmiştir.  Büyük değerlerden kaynaklanmış olabilecek örnek bir sorun için [Bu makaleyi](cache-troubleshoot-client.md#large-request-or-response-size) okuyun.

 * **Aynı bölgedeki önbellek örneğinizi ve uygulamanızı bulun.**  Farklı bir bölgedeki önbelleğe bağlanmak gecikme süresini önemli ölçüde artırabilir ve güvenilirliği azaltabilir.  Azure dışından bağlantı kurmak için, *özellikle redsıs 'in önbellek olarak kullanılması* önerilmez.  Redsıs 'i yalnızca anahtar/değer deposu olarak kullanıyorsanız, gecikme süresi birincil sorun olmayabilir. 

 * **Bağlantıları yeniden kullanın.**  Yeni bağlantılar oluşturmak pahalıdır ve gecikme süresini arttığı için, bağlantıları mümkün olduğunca yeniden kullanın. Yeni bağlantılar oluşturmayı seçerseniz, eski bağlantıları bırakmadan önce (.NET veya Java gibi yönetilen bellek dillerinde bile) kapatmayı unutmayın.

* **Ardışık düzen oluşturma kullanın.**  En iyi performansı elde etmek için ağınızı en verimli şekilde kullanmasını sağlamak üzere [redin ardışık düzen](https://redis.io/topics/pipelining) sıralamasını destekleyen bir redsıs istemcisi seçmeyi deneyin.

 * **İstemci kitaplığınızı en az 15 saniyelik bir *bağlantı zaman aşımı süresi* kullanacak şekilde yapılandırın**, böylece daha yüksek CPU koşullarında bile sunucuya bağlanma süresi vermiş olursunuz.  Küçük bir bağlantı zaman aşımı değeri, bağlantının bu zaman çerçevesinde kurulu olduğunu garanti etmez.  Bir sorun yanlış olursa (yüksek istemci CPU, yüksek sunucu CPU 'SU vb.), kısa bir bağlantı zaman aşımı değeri bağlantı girişiminin başarısız olmasına neden olur. Bu davranış genellikle kötü bir durumun daha kötüleşmesini sağlar.  Yardımcı olmak yerine, daha kısa zaman aşımları, sistemi yeniden bağlanmaya çalışmak için yeniden başlamaya zorlayarak, *Connect-> Fail-> yeniden deneme* döngüsüne neden olabilecek sorunu ortadan kaldırır. Genellikle bağlantı zaman aşımını 15 saniye veya daha yüksek bir düzeye bırakmamanız önerilir. Bağlantı denemesinin 15 veya 20 saniye sonra, yalnızca yeniden denemek için hızlı bir şekilde başarısız olmasına izin vermek daha iyidir. Bu tür bir yeniden deneme döngüsü, sistemin başlangıçta yalnızca daha uzun sürmesine izin verenden daha uzun sürmesine neden olabilir.  
     > [!NOTE]
     > Bu kılavuz, *bağlantı girişimine* ÖZELDIR ve Al veya Tamam gibi bir *işlemin* tamamlanmasını beklemek istediğiniz zamanla ilgili değildir.

 * **Pahalı Işlemlerden kaçının** - [anahtarlar](https://redis.io/commands/keys) komutu gibi bazı redin işlemleri *çok* pahalıdır ve kaçınılmalıdır.  Daha fazla bilgi için bkz. uzun süreli [komutlarla](cache-troubleshoot-server.md#long-running-commands) ilgili bazı konular

 * **TLS şifrelemesini kullanma** -redsıs Için Azure önbelleği varsayılan olarak TLS şifreli iletişim gerektirir.  TLS sürümleri 1,0, 1,1 ve 1,2 Şu anda desteklenmektedir.  Ancak, bu durumda TLS 1,0 ve 1,1, sektör genelinde kullanımdan kalkmaya yönelik bir yoldur. bu nedenle, tüm mümkünse TLS 1,2 kullanın.  İstemci kitaplığınız veya aracınız TLS desteklemiyorsa, şifrelenmemiş bağlantıları etkinleştirmek Azure portal veya [Yönetim API 'leri](/rest/api/redis/redis/update) [aracılığıyla](cache-configure.md#access-ports) yapılabilir.  Şifrelenmiş bağlantıların mümkün olmadığı durumlarda, önbelleğinizi ve istemci uygulamanızı bir sanal ağa yerleştirmek önerilir.  Sanal ağ önbelleği senaryosunda hangi bağlantı noktalarının kullanıldığı hakkında daha fazla bilgi için bu [tabloya](cache-how-to-premium-vnet.md#outbound-port-requirements)bakın.

 * **Boşta kalma zaman aşımı** -Azure Redu 'nin bağlantı için 10 dakikalık boş zaman aşımı süresi vardır; bu nedenle bu, 10 dakikadan kısa bir süre içinde ayarlanmalıdır.

## <a name="memory-management"></a>Bellek yönetimi
Göz önünde bulundurmanız isteyebileceğiniz Redsıs sunucu örneğiniz dahilinde bellek kullanımıyla ilgili birkaç şey vardır.  İşte birkaç:

 * **Uygulamanız için uygun bir [çıkarma ilkesi](https://redis.io/topics/lru-cache) seçin.**  Azure Redl için varsayılan ilke *geçici LRU*' dır. Bu, yalnızca TTL değeri ayarlanmış olan anahtarların çıkarma için uygun olacağı anlamına gelir.  Anahtarın TTL değeri yoksa, sistem hiçbir anahtarı çıkaramaz.  Sistemin bellek baskısı altında herhangi bir anahtarın çıkarılmayı izin vermek istiyorsanız, *AllKeys-LRU* ilkesini göz önünde bulundurmanız gerekebilir.

 * **Anahtarlarınız üzerinde bir süre sonu değeri ayarlayın.**  Süre sonu, bellek baskısı kadar beklemek yerine anahtarları etkin bir şekilde kaldırır.  Çıkarma, bellek baskısı nedeniyle başlatıldığında sunucunuzda ek yüke neden olabilir.  Daha fazla bilgi için, [süre sonu](https://redis.io/commands/expire) ve [expireat](https://redis.io/commands/expireat) komutlarına yönelik belgelere bakın.

## <a name="client-library-specific-guidance"></a>İstemci kitaplığına özgü kılavuz
 * [StackExchange. Reddir (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java-hangi istemciyi kullanmalıyım?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Lettuce (Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
 * [Jedsıs (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [HiRedisCluster](https://github.com/Azure/AzureCacheForRedis/blob/main/HiRedisCluster%20Best%20Practices.md)
 * [ASP.NET oturum durumu sağlayıcısı](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Yeniden denenme ne zaman güvenlidir?
Ne yazık ki kolay bir yanıt yok.  Her uygulamanın hangi işlemlerin yeniden deneneceği ve ne yapabileceğine karar sağlaması gerekir.  Her işlem, farklı gereksinimlere ve anahtar arası bağımlılıklara sahiptir.  Göz önünde bulundurmanız gereken bazı şeyler şunlardır:

 * Red, çalıştırmak istediğiniz komutu başarılı bir şekilde çalıştırsa bile, istemci tarafı hataları alabilirsiniz.  Örnek:
    - Zaman aşımları, istemci tarafı kavramıdır.  İşlem sunucuya ulaştıysa, istemci beklemeye alsa bile sunucu komutu çalıştırır.  
    - Yuva bağlantısında bir hata oluştuğunda, işlemin sunucuda gerçekten çalışır durumda olup olmadığını bilmeleri mümkün değildir.  Örneğin, sunucu isteği işledikten sonra ancak istemci yanıtı almadan önce bağlantı hatası oluşabilir.
 * Yanlışlıkla aynı işlemi iki kez çalıştırdığımda Uygulamam nasıl tepki veriyor?  Örneğin, bir tamsayıyı bir kez yerine iki kez artırdım.  Uygulamam birden çok konumdan aynı anahtara yazıyor mu?  Yeniden deneme mantığı uygulamamın başka bir bölümü tarafından ayarlanan bir değerin üzerine yazıyor mu?

Kodunuzun hata koşulları altında nasıl çalıştığını test etmek isterseniz, [yeniden başlatma özelliğini](cache-administration.md#reboot)kullanmayı düşünün. Yeniden başlatma, bağlantı sinyalleri 'nin uygulamanızı nasıl etkilediğini görmenizi sağlar.

## <a name="performance-testing"></a>Performansı test etme
 * **Kullanarak `redis-benchmark.exe` başlayın** kendi performans testlerinizi yazmadan önce olası işleme/gecikme süresi hakkında fikir almak için.  Redin-kıyaslama belgeleri [burada bulunabilir](https://redis.io/topics/benchmarks).  Redin-kıyaslama TLS ' i desteklemediğine, bu nedenle testi çalıştırmadan önce [Portal ÜZERINDEN TLS olmayan bağlantı noktasını etkinleştirmeniz](cache-configure.md#access-ports) gerekir.  [redis-benchmark.exe Windows ile uyumlu bir sürümü burada bulunabilir](https://github.com/MSOpenTech/redis/releases)
 * Test için kullanılan istemci sanal makinesi, Redsıs Cache örneğiniz ile **aynı bölgede** olmalıdır.
 * Daha iyi donanımlar olduğundan ve en iyi sonuçları sunduklarında, istemciniz için **dv2 VM serisini kullanmanızı öneririz** .
 * Kullandığınız istemci VM 'sinin, test edilmekte olan önbelleğin *en az işlem ve bant genişliğine* sahip olduğundan emin olun. 
 * Önbelleğinizin **Yük devretme koşulları altında test** edin. Önbelleğinizi yalnızca sabit durum koşulları altında performans testi olmamasını sağlamak önemlidir. Ayrıca yük devretme koşulları altında test edin ve bu süre boyunca önbelleğinizin CPU/sunucu yükünü ölçer. [Birincil düğümü](cache-administration.md#reboot)yeniden başlatarak bir yük devretme işlemi başlatabilirsiniz. Bu, uygulamanızın yük devretme koşulları sırasında işleme ve gecikme süresi boyunca nasıl davranacağını görmenizi sağlar (Güncelleştirmeler sırasında gerçekleşir ve planlanmamış bir olay sırasında gerçekleşebilir). İdeal olarak CPU/sunucu yükünü bir yük devretme sırasında %80 ' den fazlasına, performansı etkileyebilecek şekilde görmek istemezsiniz.
 * **Bazı önbellek boyutları** 4 veya daha fazla çekirdeğe sahip VM 'lerde barındırılır. Bu, önbellek VM 'lerinde genel CPU kullanımını getirmek için TLS şifreleme/şifre çözme ve birden çok çekirdekte TLS bağlantısı/bağlantı kesilmesi iş yükleri dağıtmak için yararlıdır.  [VM boyutları ve çekirdekleri hakkında daha fazla bilgi için buraya bakın](cache-planning-faq.md#azure-cache-for-redis-performance)
 * Windows kullanıyorsanız, istemci makinesinde **vRSS 'ı etkinleştirin** .  [Ayrıntılar için buraya bakın](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).  Örnek PowerShell betiği:
     >PowerShell-ExecutionPolicy Kısıtlamasız Enable-NetAdapterRSS-adı (Get-NetAdapter). Ada 

 * **Premium katman redsıs örnekleri kullanmayı düşünün**.  Bu önbellek boyutları, hem CPU hem de ağ için daha iyi donanımlar üzerinde çalıştıkları için daha iyi ağ gecikme süresi ve aktarım hızı olacaktır.

     > [!NOTE]
     > Gözlemlenen performans sonuçlarımız, başvurunuz için [burada yayımlanır](cache-planning-faq.md#azure-cache-for-redis-performance) .   Ayrıca, SSL/TLS 'nin bazı ek yük eklediğinden emin olun. bu nedenle, aktarım şifrelemesini kullanıyorsanız farklı gecikme süreleri ve/veya aktarım hızı sağlayabilirsiniz.

### <a name="redis-benchmark-examples"></a>Redis-Benchmark örnekleri
**Ön test kurulumu**: önbellek örneğini aşağıda listelenen gecikme süresi ve işleme testi komutları için gereken verilerle hazırlayın.
> redsıs-kıyaslama-h yourcache.redis.cache.windows.net-a yourAccesskey-t SET-n 10-d 1024 

**Test gecikmesi**: 1 k yük kullanarak test get istekleri.
> reddo-kıyaslama-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-d 1024-P 50-c 4

**Aktarım hızını test etmek için:** Ardışık düzen, 1000 yükü ile istekleri al.
> reddo-kıyaslama-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-n 1000000-d 1024-P 50-c 50
