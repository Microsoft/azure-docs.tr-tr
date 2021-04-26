---
title: Redis için Azure Cache nedir?
description: Önbellek, içerik önbelleğe alma, Kullanıcı oturumu önbelleğe alma, iş ve İleti Kuyruklama ve dağıtılmış işlemlerin etkinleştirilmesi için Redsıs için Azure önbelleği hakkında bilgi edinin.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 02/08/2021
ms.openlocfilehash: 49a697e6c3a6a6c931f2bb9c545647e2d6f1322d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056666"
---
# <a name="about-azure-cache-for-redis"></a>Redis için Azure Önbelleği hakkında
Redsıs için Azure önbelleği, [redin](https://redis.io/) yazılımını temel alan bellek içi veri deposu sağlar. Redsıs, arka uç veri depolarında yoğun bir şekilde kullanılan bir uygulamanın performansını ve ölçeklenebilirliğini geliştirir. Sık erişilen verileri hızlı bir şekilde yazılabilen ve hızla okunabilen sunucu belleğinde tutarak, büyük hacimde uygulama isteğini işleyebilir. Redin, modern uygulamalara kritik düşük gecikmeli ve yüksek performanslı veri depolama çözümü getirir.

Redin için Azure Cache, hem Redsıs açık kaynaklı (OSS redin) hem de Redsıs Labs 'den (Redsıs Enterprise) bir ticari ürünün yönetilen bir hizmet olarak sunulur. Güvenli ve adanmış Redsıs sunucu örnekleri ve tam Redsıs API uyumluluğu sağlar. Hizmet, Azure 'da barındırılan ve Azure 'un içindeki veya dışındaki herhangi bir uygulamayla erişilebilen Microsoft tarafından çalıştırılır.

Redsıs için Azure önbelleği, dağıtılmış bir veri veya içerik önbelleği, bir oturum deposu, bir ileti Aracısı ve daha fazlası olarak kullanılabilir. Azure SQL veya Cosmos DB gibi diğer Azure veritabanı hizmetleriyle tek başına veya yan yana dağıtılabilir.

## <a name="key-scenarios"></a>Önemli senaryolar
Redis için Azure önbelleği, genel uygulama mimarisi desenlerini destekleyerek uygulama performansını geliştirir. En yaygın olanlarından bazıları şunlardır:

| Desen      | Description                                        |
| ------------ | -------------------------------------------------- |
| [Veri önbelleği](cache-web-app-cache-aside-leaderboard.md) | Veritabanları genellikle bir önbelleğe doğrudan yüklenemeyecek kadar büyük olur. Yalnızca gerektiğinde önbelleğe veri yüklemek için [önbelleğe alma](/azure/architecture/patterns/cache-aside) deseninin kullanılması yaygındır. Sistem verilerde değişiklik yaptığında sistem, daha sonra diğer istemcilere dağıtılan önbelleği de güncelleştirebilir. Ayrıca, sistem veri üzerinde bir süre sonu ayarlayabilir veya veri güncelleştirmelerini önbelleğe tetikleyebilmesi için bir çıkarma ilkesi kullanabilir.|
| [İçerik önbelleği](cache-aspnet-output-cache-provider.md) | Üst bilgiler, alt bilgiler ve başlık gibi statik içerik kullanan şablonlardan birçok Web sayfası oluşturulur. Bu statik öğeler sık değişmemelidir. Bellek içi önbellek kullanmak, arka uç veri depolarına kıyasla statik içeriğe hızlı erişim sağlar. Bu model, işlem süresini ve sunucu yükünü azaltarak Web sunucularının daha hızlı yanıt vermesine olanak tanır. Yükü işlemek için gereken sunucu sayısını azaltmanıza izin verebilir. Redsıs için Azure Cache, ASP.NET ile bu kalıbı desteklemek için Redsıs çıkış önbelleği sağlayıcısı sağlar.|
| [Oturum deposu](cache-aspnet-session-state-provider.md) | Bu model genellikle alışveriş sepetlerini ve bir Web uygulamasının Kullanıcı tanımlama bilgileriyle ilişkilendirmek isteyebileceğiniz diğer Kullanıcı Geçmişi verileri ile birlikte kullanılır. Bir tanımlama bilgisinde çok fazla bilgi depolamak, tanımlama bilgisi boyutu arttıkça ve tanımlama bilgisi her istek ile birlikte geçirilip doğrulandıkça performansı olumsuz yönde etkileyebilir. Tipik bir çözüm, bir veritabanındaki verileri sorgulamak için anahtar olarak tanımlama bilgisini kullanır. Redde için Azure önbelleği gibi bellek içi önbellek kullanmak, bilgilerin bir kullanıcıyla ilişkilendirilmesi, tam ilişkisel veritabanıyla etkileşime kıyasla çok daha hızlıdır. |
| İş ve ileti sıraya alma | Uygulamalar genellikle istekle ilişkili işlemler yürütme süresi geldiğinde bir kuyruğa görev ekler. Daha uzun çalışan işlemler, genellikle başka bir sunucu tarafından sırada işlenmek üzere sıraya alınır.  Bu iş erteleme yöntemine görevi sıraya alma adı verilir. Redsıs için Azure önbelleği, uygulamanızda bu düzenin etkinleştirilmesi için dağıtılmış bir sıra sağlar.|
| Dağıtılmış işlemler | Uygulamalar bazen bir arka uç veri deposunda tek bir atomik işlem olarak yürütülecek bir dizi komut gerektirir. Tüm komutlar başarılı olmalı veya tümü ilk durumuna geri döndürülmelidir. Redsıs için Azure Cache, tek bir [işlem](https://redis.io/topics/transactions)olarak toplu bir komut yürütmeyi destekler. |

## <a name="redis-versions"></a>Redsıs sürümleri

Redsıs için Azure Cache, OSS redin sürüm 4. x ve önizleme olarak 6,0 ' i destekler. En son sürümü size getirmek için redin 5,0 ' i atlama kararı yaptık. Daha önce, Reda için Azure önbelleği yalnızca tek bir Redsıs sürümü ile korunur. Daha yeni bir ana sürüm yükseltmesi ve ileride en az bir eski kararlı sürüm sağlayacak. Hangi sürümün uygulamanız için en uygun olanı [seçebileceğinizi seçebilirsiniz](cache-how-to-version.md) .


## <a name="service-tiers"></a>Hizmet katmanları
Redo için Azure Cache aşağıdaki katmanlarda kullanılabilir:

| Katman | Description |
|---|---|
| Temel | Tek bir VM üzerinde çalışan bir OSS Redsıs önbelleği. Bu katmanda hizmet düzeyi sözleşmesi (SLA) yoktur ve geliştirme/test ve kritik olmayan iş yükleri için idealdir. |
| Standart | Çoğaltılan bir yapılandırmadaki iki VM üzerinde çalışan bir OSS Redsıs önbelleği. |
| Premium | Yüksek performanslı OSS Redsıs önbellekler. Bu katman daha yüksek aktarım hızı, daha düşük gecikme süresi, daha iyi kullanılabilirlik ve daha fazla özellik sunar. Premium önbellekler, temel veya standart önbellekler ile karşılaştırıldığında daha güçlü sanal makinelere dağıtılır. |
| Kurumsal | Redsıs Labs ' Redsıs Enterprise Software tarafından desteklenen yüksek performanslı önbellekler. Bu katman RediSearch, RedisBloom ve RedisTimeSeries gibi Redsıs modüllerini destekler. Ayrıca, Premium katmandan daha yüksek kullanılabilirlik sağlar. |
| Enterprise Flash | Redsıs Labs ' Redsıs Enterprise Software tarafından desteklenen uygun maliyetli büyük önbellekler. Bu katman Redsıs veri depolamayı bir VM 'de DRAM 'dan farklı olan geçici olmayan belleğe genişletir. Toplam GB başına bellek maliyetini azaltır. |

### <a name="feature-comparison"></a>Özellik karşılaştırması
[Redin fiyatlandırması Için Azure önbelleği](https://azure.microsoft.com/pricing/details/cache/) , her katman için ayrıntılı bir karşılaştırma sağlar. Aşağıdaki tablo, katmana göre desteklenen özelliklerden bazılarını açıklamaya yardımcı olur:

| Özellik Açıklaması | Temel | Standart | Premium | Kurumsal | Enterprise Flash |
| ------------------- | :-----: | :------: | :---: | :---: | :---: |
| [Hizmet Düzeyi Sözleşmesi (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|✔|✔|✔|
| Veri şifrelemesi |✔|✔|✔|✔|✔|
| [Ağ yalıtımı](cache-how-to-premium-vnet.md) |✔|✔|✔|✔|✔|
| [Ölçeklendirme](cache-how-to-scale.md) |✔|✔|✔|✔|✔|
| [OSS kümesi](cache-how-to-premium-clustering.md) |-|-|✔|✔|✔|
| [Veri kalıcılığı](cache-how-to-premium-persistence.md) |-|-|✔|Önizleme|Önizleme|
| [Bölge yedekliliği](cache-how-to-zone-redundancy.md) |-|-|Önizleme|✔|✔|
| [Coğrafi çoğaltma](cache-how-to-geo-replication.md) |-|-|✔|Önizleme|Önizleme|
| [Modül](https://redis.io/modules) |-|-|-|✔|✔|
| [İçeri/Dışarı Aktarma](cache-how-to-import-export-data.md) |-|-|✔|✔|✔|
| [Zamanlanan güncelleştirmeler](cache-administration.md#schedule-updates) |✔|✔|✔|-|-|

### <a name="choosing-the-right-tier"></a>Doğru katmanı seçme
Redsıs katmanı için bir Azure önbelleği seçerken aşağıdakileri göz önünde bulundurmanız gerekir:

* **Bellek**: temel ve standart katmanlar 250 MB – 53 GB; Premium katman 6 GB-1,2 TB; Kurumsal katmanlar 12 GB-14 TB.  120 GB 'den büyük bir Premium katman önbelleği oluşturmak için Redsıs OSS kümeleme kullanabilirsiniz. Daha fazla bilgi için bkz. [Redsıs fiyatlandırması Için Azure önbelleği](https://azure.microsoft.com/pricing/details/cache/). Daha fazla bilgi için bkz. [redsıs Için Premium Azure önbelleği için kümeleme yapılandırma](cache-how-to-premium-clustering.md).
* **Performans**: Premium ve kurumsal katmanlardaki önbellekler, daha hızlı işlemciler içeren donanımda dağıtılır ve temel veya Standart katmana kıyasla daha iyi bir performans sağlar. Premium katman önbelleklerine daha yüksek aktarım hızı ve düşük gecikme süreleri vardır. Daha fazla bilgi için bkz. [redsıs performansı Için Azure önbelleği](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Redsıs Server Için adanmış çekirdek**: C0 dışında tüm ÖNBELLEKLER adanmış VM çekirdeğini çalıştırır. Redsıs, tasarıma göre, komut işleme için yalnızca bir iş parçacığı kullanır. Redsıs için Azure önbelleği, g/ç işleme için ek çekirdekler kullanır. Daha fazla çekirdeğe sahip olmak, üretilen iş performansını artırır ve bu da doğrusal ölçekleme üretmeyebilir. Ayrıca, daha büyük VM boyutları genellikle daha küçük olanlara göre daha yüksek bant genişliği limitleriyle gelir. Bu, uygulamanızda zaman aşımları oluşmasına neden olacak ağ doygunluğunun önlenmesine yardımcı olur.
* **Ağ performansı**: yüksek aktarım hızı gerektiren bir iş yükünüz varsa, Premium veya Enterprise katmanı temel veya standart ile karşılaştırıldığında daha fazla bant genişliği sunar. Ayrıca, her katmanda daha büyük boyutlu önbellekler, önbelleği barındıran temel VM nedeniyle daha fazla bant genişliğine sahiptir. Daha fazla bilgi için bkz. [redsıs performansı Için Azure önbelleği](cache-planning-faq.md#azure-cache-for-redis-performance).
* **İstemci bağlantısı sayısı üst sınırı**: Premium katmanı, daha büyük boyutlu önbellekler için daha fazla bağlantı ile redo 'ya bağlanabilecek maksimum istemci sayısını sağlar. Kümeleme, kümelenmiş bir önbellek için kullanılabilen bağlantı sayısını artırmaz. Daha fazla bilgi için bkz. [redsıs fiyatlandırması Için Azure önbelleği](https://azure.microsoft.com/pricing/details/cache/).
* **Yüksek kullanılabilirlik**: Reda Için Azure önbelleği birden çok [yüksek kullanılabilirlik](cache-high-availability.md) seçeneği sağlar. Standart, Premium veya kurumsal önbelleğin [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)'imize göre kullanılabilir olmasını güvence altına alır. SLA yalnızca önbellek uç noktalarına olan bağlantıyı içerir. SLA, veri kaybından korumayı kapsamaz. Veri kaybına karşı dayanıklılığı artırmak için Premium ve Enterprise katmanlarında Redsıs veri kalıcılığı özelliğinin kullanılması önerilir.
* **Veri kalıcılığı**: Premium ve kurumsal katmanlar, önbellek verilerini sırasıyla bir Azure depolama hesabına ve yönetilen diske kalıcı hale getirebilmeniz için izin verir. Temel altyapı sorunları, olası veri kaybına neden olabilir. Veri kaybına karşı dayanıklılığı artırmak için bu katmanlardaki Redsıs veri kalıcılığı özelliğini kullanmanızı öneririz. Redin için Azure önbelleği hem RDB hem de AOF (Önizleme) seçeneklerini sunar. Veri kalıcılığı Azure portal ve CLı aracılığıyla etkinleştirilebilir. Premium katmanı için bkz. [redsıs Için Premium Azure önbelleği için kalıcılığı yapılandırma](cache-how-to-premium-persistence.md).
* **Ağ yalıtımı**: Azure özel bağlantısı ve sanal ağ (VNet) dağıtımları, Reda Için Azure önbelleğiniz için gelişmiş güvenlik ve trafik yalıtımı sağlar. VNET, ağ erişim denetimi ilkeleriyle erişimi daha fazla kısıtlayabilmeniz için izin verir. Daha fazla bilgi için bkz. [Azure özel bağlantısı Ile redsıs Için Azure önbelleği](cache-private-link.md) ve [Redsıs Için Premium bir Azure önbelleği için sanal ağ desteğini yapılandırma](cache-how-to-premium-vnet.md).
* **Genişletilebilirlik**: Kurumsal Katmanlar destek [RediSearch](https://docs.redislabs.com/latest/modules/redisearch/), [Redisbloom](https://docs.redislabs.com/latest/modules/redisbloom/) ve [redistimeseries](https://docs.redislabs.com/latest/modules/redistimeseries/). Bu modüller Redsıs 'e yeni veri türleri ve işlevler ekler.

Oluşturulduktan sonra, temel katmandaki önbelleğinizi Premium 'a ölçeklendirebilirsiniz. Daha düşük bir katmana ölçek azaltma Şu anda desteklenmiyor. Adım adım ölçeklendirme yönergeleri için bkz. [Azure önbelleğini redsıs Için ölçeklendirme](cache-how-to-scale.md) ve [bir ölçeklendirme işlemini otomatikleştirme](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="special-considerations-for-enterprise-tiers"></a>Kurumsal katmanlara yönelik özel konular

Kurumsal katmanlar redin Labs 'e ait ticari bir çeşit olan redsıs Enterprise 'ı kullanır. Müşteriler bir Azure Market teklifi aracılığıyla bu yazılıma yönelik bir lisans edinecektir ve ödeyebilir. Redsıs için Azure önbelleği, lisans alımı kolaylaştırmaya yardımcı olur, böylece ayrı olarak bunu yapmanız gerekmez. Azure Marketi 'nde satın almak için aşağıdaki önkoşullara sahip olmanız gerekir:
* Azure aboneliğinizin geçerli bir ödeme aracı vardır. Azure kredileri veya ücretsiz MSDN Abonelikleri desteklenmez.
* Kuruluşunuz [Azure Marketi satın alımlara](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)izin veriyor.
* Özel bir market kullanıyorsanız, redin Labs kurumsal teklifini içermesi gerekir.

> [!IMPORTANT]
> Redsıs Enterprise Cache için Azure önbelleği, önbellek örneklerinden ayrı olarak ücretlendirilen Standart Ağ Yük dengeleyicileri gerektirir. Daha fazla ayrıntı için [Load Balancer fiyatlandırmasına](https://azure.microsoft.com/pricing/details/load-balancer/) bakın. Bir kuruluş önbelleği birden çok Kullanılabilirlik Alanları için yapılandırılmışsa, veri aktarımı 1 Temmuz 2021 ' den başlayarak [Standart ağ bant genişliği ücretlerine](https://azure.microsoft.com/pricing/details/bandwidth/) göre faturalandırılır.
>
> Ayrıca, veri kalıcılığı yönetilen diskler ekler. Bu kaynakların kullanımı, kurumsal veri kalıcılığının genel önizlemesi sırasında ücretsiz olacaktır. Bu özellik genel kullanıma sunulduğunda değişebilir.
>
>

## <a name="next-steps"></a>Sonraki adımlar
* [Açık kaynaklı Redsıs önbelleği oluşturma](quickstart-create-redis.md)
* [Redsıs kurumsal önbelleği oluşturma](quickstart-create-redis-enterprise.md)
* [ASP.NET Web uygulamasında Redsıs için Azure önbelleğini kullanma](cache-web-app-howto.md)
* [.NET Core 'da Redsıs için Azure önbelleğini kullanma](cache-dotnet-core-quickstart.md)
* [Redsıs için Azure önbelleğini kullanın .NET Framework](cache-dotnet-how-to-use-azure-redis-cache.md)
* [Redsıs için Azure önbelleğini kullanın Node.js](cache-nodejs-get-started.md)
* [Java 'da Redsıs için Azure önbelleğini kullanma](cache-java-get-started.md)
* [Python 'da Redsıs için Azure önbelleğini kullanma](cache-python-get-started.md)