---
title: Redsıs için Azure önbelleği için en iyi uygulamalar
description: Bu en iyi yöntemleri izleyerek, redin için Azure önbelleğinizi etkin bir şekilde nasıl kullanacağınızı öğrenin.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: joncole
ms.openlocfilehash: 136c29245c63b2f2feed79a10a09fb57a379736f
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122387"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Redsıs için Azure önbelleği için en iyi uygulamalar 
Bu en iyi yöntemleri izleyerek Redsıs örneği için Azure önbelleğinizin performansını ve düşük maliyetli kullanımını en üst düzeye çıkarmanıza yardımcı olabilirsiniz.

## <a name="configuration-and-concepts"></a>Yapılandırma ve kavramlar
 * **Üretim sistemleri için standart veya Premium katmanını kullanın.**  Temel katman, veri çoğaltması olmayan ve SLA olmayan tek düğümlü bir sistemdir. Ayrıca, en az bir C1 önbelleği kullanın.  C0 önbellekleri, paylaşılan bir CPU çekirdeği, küçük belleğe sahip olduklarından ve "gürültülü komşu" sorunları nedeniyle basit geliştirme ve test senaryolarına yöneliktir.

 * **Redthe 'ın bellek içi veri deposu olduğunu unutmayın.**  [Bu makalede](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) , veri kaybının gerçekleşebileceği bazı senaryolar özetlenmektedir.

 * [Yazılım, düzeltme eki uygulama ve yük devretme nedeniyle](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md) **bağlantı bliıt 'leri Işleyebileceğinden, sisteminizi geliştirin** .

 * Bellek baskısı koşullarına göre **sistem yanıt hızını artırmak için [MaxMemory ile ayrılmış ayarınızı](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) yapılandırın** .  Bu ayar, yazma ağır iş yükleri için özellikle önemlidir veya daha büyük değerler (100 KB veya daha fazla) redin içinde depoluyorsunuz. Önbelleğinizin boyutunun %10 ' u ile başlamanız önerilir ve sonradan yazma ağır yüklerinizi artırmanız gerekir.

 * **Red, daha küçük değerlerle çalışır**, bu nedenle daha büyük verileri birden fazla anahtara chopping değerlendirin.  [Bu redin tartışmasında](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/), dikkatlice göz önünde bulundurmanız gereken bazı noktalar listelenmiştir.  Büyük değerlerden kaynaklanmış olabilecek örnek bir sorun için [Bu makaleyi](cache-troubleshoot-client.md#large-request-or-response-size) okuyun.

 * **Aynı bölgedeki önbellek örneğinizi ve uygulamanızı bulun.**  Farklı bir bölgedeki bir önbelleğe bağlanmak, gecikmeyi önemli ölçüde artırabilir ve güvenilirliği azaltabilir.  Azure dışından bağlantı kurmak için, *özellikle redsıs 'in önbellek olarak kullanılması*önerilmez.  Redsıs 'i yalnızca anahtar/değer deposu olarak kullanıyorsanız, gecikme süresi birincil sorun olmayabilir. 

 * **Bağlantıları yeniden kullan** -yeni bağlantı oluşturma pahalıdır ve gecikme süresini arttığı için bağlantıları mümkün olduğunca yeniden kullanın. Yeni bağlantılar oluşturmayı seçerseniz, eski bağlantıları bırakmadan önce (.NET veya Java gibi yönetilen bellek dillerinde bile) kapatmayı unutmayın.

 * **İstemci kitaplığınızı en az 15 saniyelik bir *bağlantı zaman aşımı süresi* kullanacak şekilde yapılandırın**, böylece daha yüksek CPU koşullarında bile sunucuya bağlanma süresi vermiş olursunuz.  Küçük bir bağlantı zaman aşımı değeri, bağlantının bu zaman çerçevesinde kurulu olduğunu garanti etmez.  Bir sorun yanlış olursa (yüksek istemci CPU, yüksek sunucu CPU 'SU vb.), kısa bir bağlantı zaman aşımı değeri bağlantı girişiminin başarısız olmasına neden olur. Bu davranış genellikle kötü bir durumun daha kötüleşmesini sağlar.  Yardımcı olmak yerine, daha kısa zaman aşımları, sistemi yeniden bağlanmaya çalışmak için yeniden başlamaya zorlayarak, *Connect-> Fail-> yeniden deneme* döngüsüne neden olabilecek sorunu ortadan kaldırır. Genellikle bağlantı zaman aşımını 15 saniye veya daha yüksek bir düzeye bırakmamanız önerilir. Bağlantı denemesinin 15 veya 20 saniye sonra, yalnızca yeniden denendiğinden hızlı bir şekilde başarısız olmasına izin vermek daha iyidir. Bu tür bir yeniden deneme döngüsü, sistemin başlangıçta yalnızca daha uzun sürmesine izin verenden daha uzun sürmesine neden olabilir.  
     > [!NOTE]
     > Bu kılavuz, *bağlantı girişimine* ÖZELDIR ve Al veya Tamam gibi bir *işlemin* tamamlanmasını beklemek istediğiniz zamanla ilgili değildir.
 
 * **Pahalı Işlemlerden kaçının** - [anahtarlar](https://redis.io/commands/keys) komutu gibi bazı redin işlemleri *çok* pahalıdır ve kaçınılmalıdır.  Daha fazla bilgi için bkz. uzun süreli [komutlarla](cache-troubleshoot-server.md#long-running-commands) ilgili bazı konular

 * **TLS şifrelemesini kullanma** -redsıs Için Azure önbelleği varsayılan olarak TLS şifreli iletişim gerektirir.  TLS sürümleri 1,0, 1,1 ve 1,2 Şu anda desteklenmektedir.  Ancak, bu durumda TLS 1,0 ve 1,1, sektör genelinde kullanımdan kalkmaya yönelik bir yoldur. bu nedenle, tüm mümkünse TLS 1,2 kullanın.  İstemci kitaplığınız veya aracınız TLS desteklemiyorsa, şifrelenmemiş bağlantıları etkinleştirmek Azure portal veya [Yönetim API 'leri](https://docs.microsoft.com/rest/api/redis/redis/update) [aracılığıyla](cache-configure.md#access-ports) yapılabilir.  Şifrelenmiş bağlantıların mümkün olmadığı durumlarda, önbelleğinizi ve istemci uygulamanızı bir sanal ağa yerleştirmek önerilir.  İçin kullanılan bağlantı noktaları hakkında ayrıntılı bilgi için 
 
## <a name="memory-management"></a>Bellek yönetimi
Göz önünde bulundurmanız isteyebileceğiniz Redsıs sunucu örneğiniz dahilinde bellek kullanımıyla ilgili birkaç şey vardır.  İşte birkaç:

 * **Uygulamanız için uygun bir [çıkarma ilkesi](https://redis.io/topics/lru-cache) seçin.**  Azure Redl için varsayılan ilke *geçici LRU*' dır. Bu, yalnızca TTL değeri ayarlanmış olan anahtarların çıkarma için uygun olacağı anlamına gelir.  Anahtarın TTL değeri yoksa, sistem hiçbir anahtarı çıkaramaz.  Sistemin bellek baskısı altında herhangi bir anahtarın çıkarılmayı izin vermek istiyorsanız, *AllKeys-LRU* ilkesini göz önünde bulundurmanız gerekebilir.

 * **Anahtarlarınız üzerinde bir süre sonu değeri ayarlayın.**  Bu, bellek baskısı kadar beklemek yerine anahtarları kullanılamaz olarak kaldırır.  Çıkarma, bellek baskısı nedeniyle başlatıldığında sunucunuzda ek yüke neden olabilir.  Daha fazla bilgi için, [süre sonu](https://redis.io/commands/expire) ve [expireat](https://redis.io/commands/expireat) komutlarına yönelik belgelere bakın.
 
## <a name="client-library-specific-guidance"></a>İstemci kitaplığına özgü kılavuz
 * [StackExchange. Reddir (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java-hangi istemciyi kullanmalıyım?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Lettuce (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedsıs (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net oturum durumu sağlayıcısı](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Yeniden denenme ne zaman güvenlidir?
Ne yazık ki kolay bir yanıt yok.  Her uygulamanın hangi işlemlerin yeniden deneneceği ve ne yapabileceğine karar sağlaması gerekir.  Her işlem, farklı gereksinimlere ve anahtar arası bağımlılıklara sahiptir.  Göz önünde bulundurmanız gereken bazı şeyler şunlardır:

 * Red, çalıştırmak istediğiniz komutu başarılı bir şekilde çalıştırsa bile, istemci tarafı hataları alabilirsiniz.  Örneğin:
     - Zaman aşımları, istemci tarafı kavramıdır.  İşlem sunucuya ulaştıysa, istemci beklemeye alsa bile sunucu komutu çalıştırır.  
     - Yuva bağlantısında bir hata oluştuğunda, işlemin sunucuda gerçekten çalışır durumda olup olmadığını bilmeleri mümkün değildir.  Örneğin, istek sunucu tarafından işlendikten sonra, ancak yanıt istemci tarafından alınmadan önce bağlantı hatası oluşabilir.
 *  Yanlışlıkla aynı işlemi iki kez çalıştırdığımda Uygulamam nasıl tepki veriyor?  Örneğin, bir tamsayıyı yalnızca bir kez yerine iki kez artırdım?  Uygulamam birden çok konumdan aynı anahtara yazıyor mu?  Yeniden deneme mantığı uygulamamın başka bir bölümü tarafından ayarlanan bir değerin üzerine yazıyor mu?

Kodunuzun hata koşulları altında nasıl çalıştığını test etmek isterseniz, [yeniden başlatma özelliğini](cache-administration.md#reboot)kullanmayı düşünün. Bu, bağlantı sinyalleri 'nin uygulamanızı nasıl etkilediğini görmenizi sağlar.

## <a name="performance-testing"></a>Performansı test etme
 * Kendi performans testlerinizi yazmadan önce olası işleme/gecikme süresi hakkında fikir almak için **`redis-benchmark.exe`kullanarak başlayın** .  Redin-kıyaslama belgeleri [burada bulunabilir](https://redis.io/topics/benchmarks).  Redin-kıyaslama SSL 'yi desteklemediğine, bu nedenle testi çalıştırmadan önce [Portal ÜZERINDEN SSL olmayan bağlantı noktasını etkinleştirmeniz](cache-configure.md#access-ports) gerekir.  [Redis-benchmark. exe ' nin Windows uyumlu bir sürümü burada bulunabilir](https://github.com/MSOpenTech/redis/releases)
 * Test için kullanılan istemci sanal makinesi, Redsıs Cache örneğiniz ile **aynı bölgede** olmalıdır.
 * Daha iyi donanımlar olduğundan ve en iyi sonuçları sunduklarında, istemciniz için **dv2 VM serisini kullanmanızı öneririz** .
 * Kullandığınız istemci VM 'sinin, test edilmekte olan önbelleğin*en az işlem ve bant genişliğine* sahip olduğundan emin olun. 
 * Windows kullanıyorsanız, istemci makinesinde **vRSS 'ı etkinleştirin** .  [Ayrıntılar için buraya bakın](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Örnek PowerShell betiği:
     >PowerShell-ExecutionPolicy Kısıtlanmamış Enable-NetAdapterRSS-Name (Get-NetAdapter). Ada 
     
 * **Premium katman redsıs örnekleri kullanmayı düşünün**.  Bu önbellek boyutları, hem CPU hem de ağ için daha iyi donanımlar üzerinde çalıştıkları için daha iyi ağ gecikme süresi ve aktarım hızı olacaktır.
 
     > [!NOTE]
     > Gözlemlenen performans sonuçlarımız, başvurunuz için [burada yayımlanır](cache-faq.md#azure-cache-for-redis-performance) .   Ayrıca, SSL/TLS 'nin bazı ek yük eklediğinden emin olun. bu nedenle, aktarım şifrelemesini kullanıyorsanız farklı gecikme süreleri ve/veya aktarım hızı sağlayabilirsiniz.
 
### <a name="redis-benchmark-examples"></a>Redsıs-kıyaslama örnekleri
**Ön test kurulumu**: Bu işlem, önbellek örneğini aşağıda listelenen gecikme süresi ve üretilen iş testi komutları için gereken verilerle hazırlar.
> Redis-benchmark. exe-h yourcache.redis.cache.windows.net-a yourAccesskey-t SET-n 10-d 1024 

**Sınama gecikmesi**: Bu, bir 1000 yükü kullanarak Get isteklerini test edecektir.
> Redis-benchmark. exe-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-d 1024-P 50-c 4

**Aktarım hızını test etmek için:** Bu, 1000 yüküne sahip ardışık düzen al isteklerini kullanır.
> Redis-benchmark. exe-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-n 1000000-d 1024-P 50-c 50
