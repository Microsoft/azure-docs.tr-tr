---
title: Redis için Azure Önbelleği SSS
description: Redsıs için Azure önbelleği için sık sorulan soruların, desenlerin ve en iyi yöntemlerin yanıtlarını öğrenin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: yegu
ms.openlocfilehash: e497a5c54a80dbed2ea94f8251d198c1c8bc5043
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122790"
---
# <a name="azure-cache-for-redis-faq"></a>Redis için Azure Önbelleği SSS
Reda için Azure önbelleği için sık sorulan soruların, desenlerin ve en iyi yöntemlerin yanıtlarını öğrenin.

## <a name="what-if-my-question-isnt-answered-here"></a>Sorum burada yanıtlanmazsa ne olacak?
Sorunuz burada listelenmiyorsa, bize bilgi verin ve bir yanıt bulmanıza yardımcı olabiliriz.

* Bu SSS sonundaki açıklamalara soru gönderebilir ve bu makaleyle ilgili olarak Azure önbellek ekibine ve diğer topluluk üyelerine da katılın.
* Daha geniş bir hedef kitleye ulaşmak için [Azure önbellek MSDN forumuna](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) bir soru gönderebilir ve Azure önbellek ekibi ile topluluk diğer üyeleriyle iletişim sağlayabilirsiniz.
* Bir özellik isteği yapmak istiyorsanız, [Redsıs Kullanıcı seslerine yönelik](https://feedback.azure.com/forums/169382-cache)isteklerinizi ve fikirlerinizi Azure önbelleğine gönderebilirsiniz.
* Ayrıca, [Azure Cache dış geri bildirimlerinden](mailto:azurecache@microsoft.com)bize e-posta gönderebilirsiniz.

## <a name="azure-cache-for-redis-basics"></a>Redsıs temel kavramları için Azure önbelleği
Bu bölümdeki SSS, Reda için Azure Cache 'in bazı temellerini kapsar.

* [Redsıs için Azure önbelleği nedir?](#what-is-azure-cache-for-redis)
* [Redsıs için Azure önbelleği 'ni kullanmaya nasıl başlarım?](#how-can-i-get-started-with-azure-cache-for-redis)

Aşağıdaki SSS 'ler, redin için Azure önbelleği ile ilgili temel kavramları ve soruları kapsar ve diğer SSS bölümlerinden birinde yanıtlanmıştır.

* [Redne teklif teklifi ve boyutu için Azure önbelleği kullanmalıyım?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Redsıs istemcileri için hangi Azure önbelleğinde kullanabilirim?](#what-azure-cache-for-redis-clients-can-i-use)
* [Redsıs için Azure önbelleği için yerel bir öykünücü var mı?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Nasıl yaparım? önbelleğim sistem durumunu ve performansını izlemek mi?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>SSS planlaması
* [Redne teklif teklifi ve boyutu için Azure önbelleği kullanmalıyım?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Redsıs performansı için Azure önbelleği](#azure-cache-for-redis-performance)
* [Hangi bölgede önbelleğinizi bulmalıyım?](#in-what-region-should-i-locate-my-cache)
* [Redsıs için Azure önbelleği için nasıl faturalandırılırım?](#how-am-i-billed-for-azure-cache-for-redis)
* [Redsıs için Azure önbelleğini Azure Kamu bulutu, Azure Çin bulutu veya Microsoft Azure Almanya kullanabilir miyim?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Geliştirme SSS
* [StackExchange. Redsıs yapılandırma seçenekleri ne olur?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Redsıs istemcileri için hangi Azure önbelleğinde kullanabilirim?](#what-azure-cache-for-redis-clients-can-i-use)
* [Redsıs için Azure önbelleği için yerel bir öykünücü var mı?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Redsıs komutlarını nasıl çalıştırabilirim?](#how-can-i-run-redis-commands)
* [Redin için Azure Cache 'in diğer Azure hizmetleri gibi bir MSDN sınıf kitaplığı başvurusu neden yok?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Redsıs için Azure önbelleğini PHP oturum önbelleği olarak kullanabilir miyim?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Redsıs veritabanları nelerdir?](#what-are-redis-databases)

## <a name="security-faqs"></a>Güvenlik SSS
* [Redsıs 'e bağlanmak için SSL olmayan bağlantı noktasını etkinleştirmem gerekir mi?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Üretim SSS
* [Üretim en iyi uygulamaları nelerdir?](#what-are-some-production-best-practices)
* [Ortak Redi komutları kullanılırken dikkat edilecek noktalar nelerdir?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Önbelleğim performansını nasıl kıyaslarım ve test edebilirim?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [İş parçacığı büyümesi hakkında önemli ayrıntılar](#important-details-about-threadpool-growth)
* [StackExchange. Redsıs kullanırken istemci üzerinde daha fazla aktarım sağlamak için sunucu GC 'yi etkinleştirin](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Bağlantılar etrafında performans konuları](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>İzleme ve sorun giderme SSS
Bu bölümdeki SSS 'ler ortak izleme ve sorun giderme sorularını kapsar. Redsıs örnekleri için Azure önbelleğinizi izleme ve sorun giderme hakkında daha fazla bilgi için bkz. [Reda Için Azure önbelleğini izleme](cache-how-to-monitor.md) ve çeşitli sorun giderme kılavuzu.

* [Nasıl yaparım? önbelleğim sistem durumunu ve performansını izlemek mi?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Neden zaman aşımlarını görüyorum?](#why-am-i-seeing-timeouts)
* [İstemcimin neden önbellekten bağlantısı kesildi?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Önceki önbellek SSS sunumu
* [Hangi Azure önbellek teklifi bana uygun?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Redsıs için Azure önbelleği nedir?
Redsıs için Azure önbelleği, popüler açık kaynaklı yazılım [redin](https://redis.io/)tabanlıdır. Microsoft tarafından yönetilen ve Azure 'daki herhangi bir uygulamadan erişilebilen, redin için güvenli ve adanmış bir Azure önbelleği erişimi sağlar. Daha ayrıntılı bir genel bakış için Azure.com adresindeki [redsıs ürün Için Azure önbelleği](https://azure.microsoft.com/services/cache/) sayfasına bakın.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Redsıs için Azure önbelleği 'ni kullanmaya nasıl başlarım?
Redsıs için Azure önbelleği 'ni kullanmaya başlamanın birkaç yolu vardır.

* [.Net](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.net](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node. js](cache-nodejs-get-started.md)ve [Python](cache-python-get-started.md)için kullanılabilen öğreticilerimizden birine bakabilirsiniz.
* [Redin için Microsoft Azure cache kullanarak yüksek performanslı uygulamalar oluşturmayı](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/)izleyebilirsiniz.
* Redin 'in nasıl kullanılacağını görmek için, projenizin geliştirme diliyle eşleşen istemciler için istemci belgelerini kullanıma alabilirsiniz. Redsıs için Azure Cache ile kullanılabilen birçok Red, istemcisi vardır. Redsıs istemcilerinin bir listesi için bkz. [https://redis.io/clients](https://redis.io/clients).

Henüz bir Azure hesabınız yoksa şunları yapabilirsiniz:

* [Ücretsiz bir Azure hesabı açın](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Ücretli Azure hizmetlerini denemek için kullanabileceğiniz krediler alırsınız. Krediler bitmiş olsa bile hesabı sürdürebilir ve ücretsiz Azure hizmet ve özelliklerinden faydalanabilirsiniz.
* [Visual Studio abone avantajları etkinleştirin](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). MSDN aboneliğiniz, ücretli Azure hizmetlerinizi kullanabildiğiniz her ay size kredi verir.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Redne teklif teklifi ve boyutu için Azure önbelleği kullanmalıyım?
Redin teklifi için her Azure önbelleği, farklı düzeylerde **Boyut**, **bant genişliği**, **yüksek kullanılabilirlik**ve **SLA** seçenekleri sağlar.

Aşağıda bir önbellek sunumu seçme konuları verilmiştir.

* **Bellek**: temel ve standart katmanlar 250 MB – 53 GB sunar. Premium katman en fazla 1,2 TB (küme olarak) veya 120 GB (kümelenmemiş). Daha fazla bilgi için bkz. [Redsıs fiyatlandırması Için Azure önbelleği](https://azure.microsoft.com/pricing/details/cache/).
* **Ağ performansı**: yüksek aktarım hızı gerektiren bir iş yükünüz varsa, Premium katmanı standart veya temel ile karşılaştırıldığında daha fazla bant genişliği sunar. Ayrıca, her katmanda daha büyük boyutlu önbellekler, önbelleği barındıran temel VM nedeniyle daha fazla bant genişliğine sahiptir. Daha fazla bilgi için [aşağıdaki tabloya](#cache-performance)bakın.
* **Aktarım hızı**: Premium katmanı, kullanılabilir en fazla üretilen işi sunar. Önbellek sunucusu veya istemcisi bant genişliği sınırlarına ulaşırsa, istemci tarafında zaman aşımları alabilirsiniz. Daha fazla bilgi için aşağıdaki tabloya bakın.
* **Yüksek kullanılabilirlik/SLA**: Reda Için Azure önbelleği, Standart/Premium önbelleğin en az% 99,9 kullanılabilir olmasını güvence altına alır. SLA 'umuz hakkında daha fazla bilgi edinmek için bkz. [Redsıs fiyatlandırması Için Azure önbelleği](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). SLA yalnızca önbellek uç noktalarına olan bağlantıyı içerir. SLA, veri kaybından korumayı kapsamaz. Veri kaybına karşı dayanıklılığı artırmak için Premium katmanda Redsıs veri kalıcılığı özelliğini kullanmanızı öneririz.
* **Redsıs veri kalıcılığı**: Premium katmanı, önbellek verilerini bir Azure depolama hesabında kalıcı hale bırakmanıza olanak tanır. Temel/standart önbellekte, tüm veriler yalnızca bellekte depolanır. Temel altyapı sorunları, olası veri kaybına neden olabilir. Veri kaybına karşı dayanıklılığı artırmak için Premium katmanda Redsıs veri kalıcılığı özelliğini kullanmanızı öneririz. Redsıs için Azure önbelleği, Redsıs kalıcılığı içindeki RDB ve AOF (çok yakında) seçenekleri sunar. Daha fazla bilgi için bkz. [redsıs Için Premium Azure önbelleği için kalıcılığı yapılandırma](cache-how-to-premium-persistence.md).
* **Redsıs kümesi**: 120 GB 'den daha büyük önbellekler oluşturmak veya birden çok redin düğümüne veri eklemek için Premium katmanda bulunan reddo kümeleme kullanabilirsiniz. Her düğüm, yüksek kullanılabilirlik için birincil/çoğaltma önbellek çiftinin oluşur. Daha fazla bilgi için bkz. [redsıs Için Premium Azure önbelleği için kümeleme yapılandırma](cache-how-to-premium-clustering.md).
* **Gelişmiş güvenlik ve ağ yalıtımı**: Azure sanal ağ (VNet) dağıtımı, Azure önbelleğiniz için gelişmiş güvenlik ve yalıtımın yanı sıra alt ağlar, erişim denetim ilkeleri ve diğer özellikler için erişimi daha da kısıtlamak sağlar. Daha fazla bilgi için bkz. [bir Premium Azure önbelleği için bkz. redsıs Için sanal ağ desteğini yapılandırma](cache-how-to-premium-vnet.md).
* **Redsıs 'Yi yapılandırma**: hem standart hem de Premium katmanlarda, Redsıs 'Yi keyspace bildirimleri için yapılandırabilirsiniz.
* **İstemci bağlantısı sayısı üst sınırı**: Premium katmanı, daha büyük boyutlu önbellekler için daha fazla bağlantı ile redo 'ya bağlanabilecek maksimum istemci sayısını sağlar. Kümeleme, kümelenmiş bir önbellek için kullanılabilen bağlantı sayısını artırmaz. Daha fazla bilgi için bkz. [redsıs fiyatlandırması Için Azure önbelleği](https://azure.microsoft.com/pricing/details/cache/).
* **Redsıs sunucusu Için adanmış çekirdek**: Premium katmanda, redsıs için özel bir çekirdeğe sahiptir. Temel/standart katmanlarında C1 boyutu ve üzeri, Redsıs sunucusu için adanmış bir çekirdeğe sahiptir.
* **Redsıs tek iş parçacıklı olduğundan** , ikiden fazla çekirdeğe sahip olmak yalnızca iki çekirdeğe sahip olmak için ek avantaj sağlamaz, ancak büyük VM boyutları genellikle daha küçük boyutlarda daha fazla bant genişliğine sahiptir. Önbellek sunucusu veya istemcisi bant genişliği sınırlarına ulaşırsa, istemci tarafında zaman aşımları alırsınız.
* **Performans iyileştirmeleri**: Premium katmanda bulunan önbellekler, daha hızlı işlemciler içeren donanımda dağıtılır ve temel veya Standart katmana kıyasla daha iyi bir performans sağlar. Premium katman önbelleklerine daha yüksek aktarım hızı ve düşük gecikme süreleri vardır.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Redsıs performansı için Azure önbelleği
Aşağıdaki tabloda, bir IaaS VM 'sinden Redsıs uç noktası için Azure önbelleğine karşı `redis-benchmark.exe` kullanarak standart ve Premium önbellekleri farklı boyutlarda test edilirken gözlemlenen maksimum bant genişliği değerleri gösterilmektedir. SSL işleme için redsıs-kıyaslama, Redsıs uç noktası için Azure önbelleğine bağlanmak üzere Stunnel ile birlikte kullanılır.

>[!NOTE] 
>Bu değerler garanti edilmez ve bu numaralar için SLA yoktur, ancak tipik olmalıdır. Uygulamanız için doğru önbellek boyutunu tespit etmek üzere kendi uygulamanızı test etmeniz gerekir.
>Bu sayılar, düzenli aralıklarla yeni sonuçlar gönderdiğimiz için değişebilir.
>

Bu tablodan aşağıdaki ekibinizle çizebilirsiniz:

* Aynı boyutta olan önbellekler Standart katmana kıyasla Premium katmanda daha yüksektir. Örneğin, 6 GB önbellek ile P1 'nin performansı, C3 için 100.000 RPS ile karşılaştırıldığında saniyede 180.000 istektir (RPS).
* Redsıs kümelemesiyle, kümedeki parça sayısını (düğümler) artırdıkça üretilen iş miktarı artar. Örneğin, 10 parçalı bir P4 kümesi oluşturursanız, kullanılabilir verimlilik 400.000 * 10 = 4.000.000 RPS olur.
* Daha büyük anahtar boyutları için üretilen iş, Standart katmana kıyasla Premium katmanda daha yüksektir.

| Fiyatlandırma katmanı | Boyut | CPU çekirdekleri | Kullanılabilir bant genişliği | 1 KB değer boyutu | 1 KB değer boyutu |
| --- | --- | --- | --- | --- | --- |
| **Standart önbellek boyutları** | | |**Megabit/sn (MB/sn)/megabayt/sn (MB/s)** |**Saniye başına istek (RPS) SSL olmayan** |**İstek/saniye (RPS) SSL** |
| C0 | 250 MB | Paylaşımlı | 100 / 12.5  |  15.000 |   7\.500 |
| C1 |   1 GB | 1      | 500 / 62.5  |  38.000 |  20.720 |
| C2 | 2,5 GB | 2      | 500 / 62.5  |  41.000 |  37.000 |
| C3 |   6 GB | 4      | 1000/125  | 100.000 |  90,000 |
| C4 |  13 GB | 2      | 500 / 62.5  |  60,000 |  55.000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102.000 |  93.000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126.000 | 120,000 |
| **Premium önbellek boyutları** | |**Parça başına CPU çekirdekleri** | **Megabit/sn (MB/sn)/megabayt/sn (MB/s)** |**Saniyedeki istek sayısı (RPS) SSL olmayan, parça başına** |**Saniyedeki istek/saniye (RPS) SSL, parça başına** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172.000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350.000 | 341.000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350.000 | 341.000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400,000 | 373.000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400,000 | 373.000 |

Stunnel ayarlama veya `redis-benchmark.exe`gibi Redsıs araçlarını indirme hakkında yönergeler için bkz. [redsıs komutlarını nasıl çalıştırabilirim?](#cache-commands) bölümü.

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>Hangi bölgede önbelleğinizi bulmalıyım?
En iyi performans ve en düşük gecikme için, önbellek istemci uygulamanızla aynı bölgedeki redin için Azure önbelleğinizi bulun.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Redsıs için Azure önbelleği için nasıl faturalandırılırım?
Redsıs fiyatlandırması için Azure önbelleği [burada](https://azure.microsoft.com/pricing/details/cache/)yer alır. Fiyatlandırma sayfası, fiyatlandırmayı saatlik fiyat olarak listeler. Önbellekler, önbelleğin silindiği zamana kadar önbelleğin oluşturulduğu zamandan itibaren dakika başına faturalandırılır. Bir önbelleğin faturalandırmasını durdurma veya duraklatma seçeneği yoktur.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Redsıs için Azure önbelleğini Azure Kamu bulutu, Azure Çin bulutu veya Microsoft Azure Almanya kullanabilir miyim?
Evet, Redu için Azure önbelleği, Azure Kamu bulutu, Azure Çin 21Vianet bulutu ve Microsoft Azure Almanya kullanılabilir. Redo için Azure önbelleğine erişme ve bunları yönetmeye yönelik URL 'Ler, Azure genel bulutu ile karşılaştırıldığında bu bulutlarda farklılık açmaktadır.

| Bulut   | Redsıs için DNS son eki            |
|---------|---------------------------------|
| Genel  | *.redis.cache.windows.net       |
| ABD Devleti  | *.redis.cache.usgovcloudapi.net |
| Almanya | *.redis.cache.cloudapi.de       |
| Çin   | *.redis.cache.chinacloudapi.cn  |

Reda için Azure önbelleğini diğer bulutlarla birlikte kullanma hakkında daha fazla bilgi için aşağıdaki bağlantılara bakın.

- [Azure Kamu veritabanları-Redsıs için Azure önbelleği](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure Çin 21Vianet bulutu-Redsıs için Azure önbelleği](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Almanya](https://azure.microsoft.com/overview/clouds/germany/)

Azure için Azure önbelleğini Azure Kamu bulutu, Azure Çin 21Vianet bulutu ve Microsoft Azure Almanya PowerShell ile birlikte kullanma hakkında bilgi için bkz. [diğer bulutlara bağlanma-Redsıs PowerShell Için Azure önbelleği](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>StackExchange. Redsıs yapılandırma seçenekleri ne olur?
StackExchange. redin birçok seçeneği vardır. Bu bölüm bazı yaygın ayarlardan bazılarıdır. StackExchange. Redu seçenekleri hakkında daha ayrıntılı bilgi için bkz. [StackExchange. redsıs yapılandırması](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Açıklama | Öneri |
| --- | --- | --- |
| AbortOnConnectFail |Doğru olarak ayarlandığında, ağ hatasından sonra bağlantı yeniden bağlanmaz. |False olarak ayarlayın ve StackExchange. redin otomatik olarak yeniden bağlanmasına izin verin. |
| ConnectRetry |İlk bağlantı sırasında bağlantı denemelerinin yinelenme sayısı. |Rehberlik için aşağıdaki notlara bakın. |
| ConnectTimeout |Connect işlemlerinde MS olarak zaman aşımı. |Rehberlik için aşağıdaki notlara bakın. |

Genellikle istemcinin varsayılan değerleri yeterlidir. İş yükünüze göre seçeneklerde ince ayar yapabilirsiniz.

* **Dene**
  * ConnectRetry ve ConnectTimeout için genel rehberlik hızlı bir şekilde başarısız olur ve yeniden dener. Bu kılavuz, iş yükünüze ve istemcinizin bir Redi komutu vermesi ve yanıt alması için ne kadar süre harcandığından elde edilir.
  * StackExchange. redin bağlantı durumunu denetlemek ve kendiniz yeniden bağlanmak yerine otomatik olarak yeniden bağlanmasına izin verin. **Connectionçoğullayıcı. IsConnected özelliğini kullanmaktan kaçının**.
  * Kar Balling-bazen yeniden denemekte olduğunuz ve yeniden deneme karından ve hiçbir zaman kurtarmadığı bir sorunla karşılaşabilirsiniz. Kar baletini oluşursa, Microsoft düzenleri & Yöntemler grubu tarafından yayımlanan, [yeniden deneme genel Kılavuzu](../best-practices-retry-general.md) ' nda açıklandığı gibi bir üstel geri alma algoritması kullanmayı göz önünde bulundurmanız gerekir.
  
* **Zaman aşımı değerleri**
  * İş yükünüzü değerlendirin ve değerleri uygun şekilde ayarlayın. Büyük değerleri depoluyorsanız, zaman aşımını daha yüksek bir değere ayarlayın.
  * `AbortOnConnectFail` false olarak ayarlayın ve StackExchange. redin sizin için yeniden bağlanmasına izin verin.
  * Uygulama için tek bir Connectionçoğullayıcı örneği kullanın. Bir bağlantı özelliği tarafından döndürülen tek bir örnek oluşturmak için bir LazyConnection kullanabilirsiniz, bu, [Connectionçoğullayıcı sınıfı kullanılarak önbelleğe bağlanma](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)bölümünde gösterilmiştir.
  * Tanılama amaçları için `ConnectionMultiplexer.ClientName` özelliğini bir uygulama örneği için benzersiz bir ad olarak ayarlayın.
  * Özel iş yükleri için birden çok `ConnectionMultiplexer` örneği kullanın.
      * Uygulamanızda farklı yükleme yaptıysanız bu modeli takip edebilirsiniz. Örneğin:
      * Büyük anahtarlarla ilgilenirken bir Çoğullayıcı olabilir.
      * Küçük anahtarlarla ilgilenirken bir Çoğullayıcı olabilir.
      * Bağlantı zaman aşımları için farklı değerler ayarlayabilir ve kullandığınız her bir Connectionçoğullayıcı için yeniden deneme mantığı yapabilirsiniz.
      * Tanılamalarda yardımcı olması için her bir Çoğullayıcı `ClientName` özelliğini ayarlayın.
      * Bu kılavuz, `ConnectionMultiplexer`başına daha kolay gecikme süresine neden olabilir.

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Redsıs istemcileri için hangi Azure önbelleğinde kullanabilirim?
Redin hakkında harika şeyler birçok farklı geliştirme dilini destekleyen çok sayıda istemci vardır. İstemcilerin geçerli listesi için bkz. [redsıs istemcileri](https://redis.io/clients). Birçok farklı dili ve istemciyi kapsayan öğreticiler için bkz. [Azure Cache 'ı redsıs için kullanma](cache-dotnet-how-to-use-azure-redis-cache.md) ve içerik tablosundaki eşdüzey makaleler.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Redsıs için Azure önbelleği için yerel bir öykünücü var mı?
Redsıs için Azure önbelleği için yerel bir öykünücü yoktur, ancak Redis-Server. exe ' nin MSOpenTech sürümünü yerel makinenizde [redsıs komut satırı araçlarından](https://github.com/MSOpenTech/redis/releases/) çalıştırabilir ve aşağıdaki örnekte gösterildiği gibi yerel bir önbellek öykünücüsünde benzer bir deneyim almak için buna bağlanabilirsiniz:

    private static Lazy<ConnectionMultiplexer>
          lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


İsterseniz redo [. conf](https://redis.io/topics/config) dosyasını redsıs Için çevrimiçi Azure önbelleğiniz için [varsayılan önbellek ayarlarıyla](cache-configure.md#default-redis-server-configuration) daha yakından eşleşecek şekilde yapılandırabilirsiniz.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Redsıs komutlarını nasıl çalıştırabilirim?
Redsıs [komutlarında listelenen](https://redis.io/commands#) komutların herhangi birini, Redsıs [için Azure önbelleğinde desteklenmeyen](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)redsıs komutlarında listelenen komutlardan birini kullanabilirsiniz. Redsıs komutlarını çalıştırmak için birkaç seçeneğiniz vardır.

* Standart veya Premium önbelleğiniz varsa, redsıs [konsolunu](cache-configure.md#redis-console)kullanarak redsıs komutlarını çalıştırabilirsiniz. Redin konsolu, Azure portal Redsıs komutlarını çalıştırmak için güvenli bir yol sağlar.
* Redsıs komut satırı araçlarını da kullanabilirsiniz. Bunları kullanmak için aşağıdaki adımları gerçekleştirin:
* [Redsıs komut satırı araçlarını](https://github.com/MSOpenTech/redis/releases/)indirin.
* `redis-cli.exe`kullanarak önbelleğe bağlanın. Aşağıdaki örnekte gösterildiği gibi-a anahtarını ve-a anahtarını kullanarak önbellek uç noktasını geçirin:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Redsıs komut satırı araçları SSL bağlantı noktasıyla çalışmaz, ancak `stunnel` gibi bir yardımcı programı kullanarak, reddo [komut satırı aracının reddo Için Azure Cache Ile nasıl kullanılacağı ile](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool) ilgili yönergeleri IZLEYEREK araçları SSL bağlantı noktasına güvenle bağlayabilirsiniz.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Redin için Azure Cache 'in diğer Azure hizmetleri gibi bir MSDN sınıf kitaplığı başvurusu neden yok?
Redsıs için Microsoft Azure önbelleği, redin için popüler açık kaynaklı Azure önbelleğini temel alır. Birçok programlama dili için çok çeşitli [redin istemcileri](https://redis.io/clients) erişebilir. Her istemcinin [redsıs komutları](https://redis.io/commands)kullanılarak redsıs örneği Için Azure önbelleğine çağrı yapan kendi API 'si vardır.

Her istemci farklı olduğundan, MSDN 'de tek bir merkezi sınıf başvurusu yoktur ve her istemci kendi başvuru belgelerini korur. Başvuru belgelerine ek olarak, farklı diller ve önbellek istemcileri kullanarak redin için Azure önbelleği ile çalışmaya nasıl başladığının gösterildiği çeşitli öğreticiler vardır. Bu öğreticilere erişmek için bkz. [Azure Cache 'ı redsıs için kullanma](cache-dotnet-how-to-use-azure-redis-cache.md) ve içerik tablosundaki eşdüzey makaleler.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Redsıs için Azure önbelleğini PHP oturum önbelleği olarak kullanabilir miyim?
Evet, Redsıs için Azure önbelleğini PHP oturum önbelleği olarak kullanmak için, `session.save_path`içindeki Redsıs örneği için Azure önbelleğiniz için bağlantı dizesini belirtin.

> [!IMPORTANT]
> Redsıs için Azure önbellek 'yi bir PHP oturum önbelleği olarak kullanırken, aşağıdaki örnekte gösterildiği gibi, önbelleğe bağlanmak için kullanılan güvenlik anahtarını URL 'ye kodlamanız gerekir:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Anahtar URL kodlanmışsa, şöyle bir ileti içeren bir özel durum alabilirsiniz: `Failed to parse session.save_path`
>
>

Redin için Azure önbelleğini Phpredıs istemcisiyle PHP oturum önbelleği olarak kullanma hakkında daha fazla bilgi için bkz. [php oturum işleyicisi](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Redsıs veritabanları nelerdir?

Redsıs veritabanları, aynı redin örneği içindeki verilerin yalnızca mantıksal bir ayrımı olur. Önbellek belleği, belirli bir veritabanının tüm veritabanları ve gerçek bellek tüketimi arasında paylaşılır ve bu veritabanında depolanan anahtarlara/değerlere göre değişir. Örneğin, bir C6 önbelleğinde 53 GB bellek ve bir P5 120 GB vardır. Tüm 53 GB/120 GB 'yi tek bir veritabanına koyabilirsiniz veya birden çok veritabanı arasında ayırabilirsiniz. 

> [!NOTE]
> Redsıs için, kümeleme etkinken Premium bir Azure önbelleği kullanılırken yalnızca 0 veritabanı kullanılabilir. Bu sınırlama, iç Reddir kısıtlamasıdır ve redin için Azure önbelleğine özgü değildir. Daha fazla bilgi için bkz. [kümelendirmeyi kullanmak için istemci Uygulamam üzerinde herhangi bir değişiklik yapmam gerekir mi?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Redsıs 'e bağlanmak için SSL olmayan bağlantı noktasını etkinleştirmem gerekir mi?
Redsıs sunucusu, SSL 'yi yerel olarak desteklemez, ancak Redsıs için Azure önbelleği olur. Redsıs için Azure önbelleğine bağlanıyorsanız ve istemciniz, StackExchange. redin gibi SSL 'yi destekliyorsa, SSL kullanmanız gerekir.

>[!NOTE]
>SSL olmayan bağlantı noktası, Redsıs örnekleri için yeni Azure önbelleği için varsayılan olarak devre dışıdır. İstemciniz SSL 'yi desteklemiyorsa, [Redu Için Azure önbelleğinde önbellek yapılandırma](cache-configure.md) makalesinin [erişim bağlantı noktaları](cache-configure.md#access-ports) bölümündeki yönergeleri izleyerek SSL olmayan bağlantı noktasını etkinleştirmeniz gerekir.
>
>

`redis-cli` gibi redsıs araçları, SSL bağlantı noktasıyla birlikte çalışmaz, ancak ASP.NET gibi bir yardımcı `stunnel` programı kullanarak, [Redsıs önizleme sürümü blog gönderisi Için duyuruldu oturum durumu sağlayıcısının](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) yönergelerini IZLEYEREK araçları SSL bağlantı noktasına güvenle bağlayabilirsiniz.

Redsıs araçlarını indirme yönergeleri için bkz. [redsıs komutlarını nasıl çalıştırabilirim?](#cache-commands) bölümü.

### <a name="what-are-some-production-best-practices"></a>Üretim en iyi uygulamaları nelerdir?
* [StackExchange. Redsıs en iyi uygulamaları](#stackexchangeredis-best-practices)
* [Yapılandırma ve kavramlar](#configuration-and-concepts)
* [Performansı test etme](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange. Redsıs en iyi uygulamaları
* `AbortConnect` false olarak ayarlayın, ardından Connectionçoğullayıcı 'nın otomatik olarak yeniden bağlanmasına izin verin. [Ayrıntılar için buraya bakın](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Connectionçoğullayıcısı yeniden kullanma-her istek için yeni bir tane oluşturmayın. [Burada gösterilen](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) `Lazy<ConnectionMultiplexer>` deseninin kullanılması önerilir.
* Red, daha küçük değerlerle çalışır, bu nedenle daha büyük verileri birden fazla anahtara chopping değerlendirin. [Bu redin tartışmasında](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)100 KB büyük olarak değerlendirilir. Büyük değerlerden kaynaklanmış olabilecek örnek bir sorun için [Bu makaleyi](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) okuyun.
* Zaman aşımlarını önlemek için iş [parçacığı ayarlarınızı](#important-details-about-threadpool-growth) yapılandırın.
* En azından 5 saniyelik varsayılan connectTimeout değerini kullanın. Bu Aralık, StackExchange 'e, bir ağ BLIP durumunda bağlantıyı yeniden kurmak için yeterli zaman sağlar.
* Çalıştırdığınız farklı işlemlerle ilişkili performans maliyetlerinden haberdar olun. Örneğin, `KEYS` komutu bir O (n) işlemidir ve kaçınılması gerekir. [Redis.io sitesinde](https://redis.io/commands/) , desteklediği her bir işlem için zaman karmaşıklığı etrafında ayrıntılar bulunur. Her bir işlemin karmaşıklığını görmek için her bir komuta tıklayın.

#### <a name="configuration-and-concepts"></a>Yapılandırma ve kavramlar
* Üretim sistemleri için standart veya Premium katmanını kullanın. Temel katman, veri çoğaltması olmayan ve SLA olmayan tek düğümlü bir sistemdir. Ayrıca, en az bir C1 önbelleği kullanın. C0 önbellekleri genellikle basit geliştirme ve test senaryoları için kullanılır.
* Redthe 'ın **bellek içi** veri deposu olduğunu unutmayın. Veri kaybının gerçekleşebileceği senaryolara dikkat etmeniz için [Bu makaleyi](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) okuyun.
* [Düzeltme eki uygulama ve yük devretme nedeniyle](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md), sisteminizi, bağlantı bliıt 'leri işleyebilecek şekilde geliştirin.

#### <a name="performance-testing"></a>Performansı test etme
* Kendi performans testlerinizi yazmadan önce olası üretilen iş hızına ulaşmak için `redis-benchmark.exe` kullanarak başlayın. `redis-benchmark` SSL 'yi desteklemediğinden, testi çalıştırmadan önce [Azure Portal aracılığıyla SSL olmayan bağlantı noktasını etkinleştirmeniz](cache-configure.md#access-ports) gerekir. Örnekler için bkz. [önbelleğim performansını nasıl ücretlendirilirim ve test edebilirim?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Test için kullanılan istemci sanal makinesi, Redsıs örneği için Azure önbelleğiniz ile aynı bölgede olmalıdır.
* Daha iyi donanımlar olduğundan ve en iyi sonuçları vermesi için istemciniz için dv2 VM serisini kullanmanızı öneririz.
* Seçtiğiniz önbellek için en az bilgi işlem ve bant genişliği özelliği olan seçtiğiniz istemci VM 'nizin olduğundan emin olun.
* Windows kullanıyorsanız, istemci makinesinde VRSS 'i etkinleştirin. [Ayrıntılar için buraya bakın](https://technet.microsoft.com/library/dn383582.aspx).
* Premium katman Redsıs örnekleri, hem CPU hem de ağ için daha iyi donanımlar üzerinde çalıştıkları için daha iyi ağ gecikmesi ve üretilen iş hızına sahiptir.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Ortak Redi komutları kullanılırken dikkat edilecek noktalar nelerdir?

* Bu komutların etkisini tam olarak anlamadığınız sürece, tamamlanması uzun süren bazı REDI komutlarının kullanmaktan kaçının. Örneğin, üretimde [anahtarlar](https://redis.io/commands/keys) komutunu çalıştırmayın. Anahtar sayısına bağlı olarak, döndürülmesi uzun sürebilir. Redsıs tek iş parçacıklı bir sunucusudur ve komutları tek seferde işler. Anahtarlar sonrasında verilen başka komutlarınız varsa, Reduntil anahtarlar komutunu işleyene kadar bunlar işlenmeyecektir. [Redis.io sitesinde](https://redis.io/commands/) , desteklediği her bir işlem için zaman karmaşıklığı etrafında ayrıntılar bulunur. Her bir işlemin karmaşıklığını görmek için her bir komuta tıklayın.
* Anahtar boyutları-küçük anahtar/değer veya büyük anahtar/değer kullanmalı mıyım? Senaryoya bağlıdır. Senaryonuz daha büyük anahtarlar gerektiriyorsa, ConnectionTimeout 'yi ayarlayabilir, sonra değerleri yeniden deneyebilir ve yeniden deneme mantığınızı ayarlayabilirsiniz. Redsıs sunucu perspektifinden, daha küçük değerler daha iyi performans sağlar.
* Bu noktalar, daha büyük değerleri redin içinde depolayamayacağınız anlamına gelmez; Aşağıdaki noktalara dikkat etmeniz gerekir. Gecikme süreleri daha yüksek olacaktır. Daha büyük bir veri kümesi ve daha küçük olan bir veri kümesine sahipseniz, her biri farklı bir zaman aşımı kümesiyle ve yeniden deneme değerleriyle yapılandırılmış ve bu, önceki [StackExchange. redsıs yapılandırma seçenekleri](#cache-configuration) bölümünde açıklandığı gibi, birden çok connectionçoğullayıcı örneği kullanabilirsiniz.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Önbelleğim performansını nasıl kıyaslarım ve test edebilirim?
* Önbelleğinizin sistem durumunu [izleyebilmeniz](cache-how-to-monitor.md#enable-cache-diagnostics) için [önbellek tanılamayı etkinleştirin](cache-how-to-monitor.md). Azure portalında ölçümleri görüntüleyebilir ve ayrıca istediğiniz araçları kullanarak bunları [indirebilir ve gözden geçirebilirsiniz](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring).
* Redsıs sunucunuza test yüklemek için Redis-benchmark. exe ' yi kullanabilirsiniz.
* Redin yük testi istemcisinin ve Azure önbelleğinin aynı bölgede bulunduğundan emin olun.
* Redis-cli. exe ' yi kullanın ve ıNFO komutunu kullanarak önbelleği izleyin.
* Yüklemeniz yüksek bellek parçalanmasına neden oluyorsa, daha büyük bir önbellek boyutuna kadar ölçeklendirmelisiniz.
* Redsıs araçlarını indirme yönergeleri için bkz. [redsıs komutlarını nasıl çalıştırabilirim?](#cache-commands) bölümü.

Aşağıdaki komutlar, Redis-benchmark. exe ' nin kullanılmasına bir örnek sağlar. Doğru sonuçlar için, bu komutları önbelleğiniz ile aynı bölgedeki bir VM 'den çalıştırın.

* Bir 1000 yükü kullanarak ardışık, ardışık iş kümesi isteklerini test etme

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Test ardışık düzen bir 1 k yük kullanarak istekleri al.
  NOTE: önbelleği doldurmak için önce yukarıda gösterilen küme testini çalıştırın

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>İş parçacığı büyümesi hakkında önemli ayrıntılar
CLR ThreadPool 'ın iki tür iş parçacığı vardır-"Worker" ve "g/ç tamamlama bağlantı noktası" (IOCP) iş parçacıkları.

* Çalışan iş parçacıkları `Task.Run(…)`veya `ThreadPool.QueueUserWorkItem(…)` yöntemlerini işleme gibi şeyler için kullanılır. Bu iş parçacıkları, bir arka plan iş parçacığında iş olması gerektiğinde CLR içindeki çeşitli bileşenler tarafından da kullanılır.
* ICP iş parçacıkları, ağdan okurken olduğu gibi zaman uyumsuz GÇ gerçekleştiğinde kullanılır.

İş parçacığı havuzu, her bir iş parçacığı türü için "En düşük" ayarına ulaşıncaya kadar isteğe bağlı olarak (herhangi bir daraltma olmadan) yeni çalışan iş parçacıkları veya g/ç Tamamlama iş parçacıkları sağlar. Varsayılan olarak, en az sayıda iş parçacığı bir sistemdeki işlemci sayısına ayarlanır.

Mevcut (meşgul) iş parçacıklarının sayısı "en az" iş parçacığı sayısına ulaştıktan sonra, ThreadPool, yeni iş parçacıklarını 500 milisaniye başına bir iş parçacığına çıkarma oranını ortadan caktır. Genellikle, sisteminiz bir ıOCP iş parçacığına ihtiyaç duyan bir iş patlaması alırsa, bu çalışmayı hızla işler. Ancak, iş patlaması yapılandırılmış "En düşük" ayarından daha büyükse, iş parçacığı, iki şeyin oluşmasını beklediği için bazı işlerin işlenmesinde bir gecikme olur.

1. Mevcut bir iş parçacığı çalışmayı işlemek için ücretsiz hale gelir.
2. 500 MS için mevcut bir iş parçacığı yok, bu nedenle yeni bir iş parçacığı oluşturulur.

Temel olarak, meşgul iş parçacıklarının sayısı en az iş parçacığından fazlaysa, büyük olasılıkla ağ trafiğinin uygulama tarafından işlenebilmesi için 500-ms gecikme süresi ödemelerinizin olması anlamına gelir. Ayrıca, var olan bir iş parçacığının 15 saniyeden uzun süre boşta kaldığı durumlarda (hatırlıyorum), temizlendiğini ve bu büyüme ve azalma döngüsünün yinelenebileceğini unutmayın.

StackExchange. Redsıs 'den bir örnek hata iletisi görüyoruz (derleme 1.0.450 veya sonrası), şimdi de iş parçacığı istatistiklerini (bkz. ıOCP ve çalışan ayrıntıları aşağıda verilmiştir) yazdıracaksınız.

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

Önceki örnekte, ıOCP iş parçacığında altı meşgul iş parçacığı olduğunu ve sistemin dört en düşük iş parçacığına izin verecek şekilde yapılandırıldığını görebilirsiniz. Bu durumda, 6 > 4 nedeniyle istemci muhtemelen 2 500 ms gecikme süresi görmüştür.

ICP veya çalışan iş parçacıklarının büyümesi kısıtlandığında, StackExchange. redin zaman aşımları ile karşılaşacağını unutmayın.

### <a name="recommendation"></a>Öneri

Bu bilgiler verildiğinde, müşterilerin IOCP ve çalışan iş parçacıkları için en düşük yapılandırma değerini varsayılan değerden daha büyük bir değere ayarlaması önemle önerilir. Bir uygulamanın doğru değeri başka bir uygulama için büyük olasılıkla çok yüksek veya düşük olacağı için, bu değerin ne olması gerektiğine ilişkin bir veya daha fazla uygun olmayan tek bir kılavuza izin vermiyoruz. Bu ayar ayrıca, karmaşık uygulamaların diğer bölümlerinin performansını etkileyebilir, böylece her müşterinin bu ayarı kendi özel gereksinimlerine göre hassas bir şekilde ayarlayabilmeleri gerekir. İyi bir başlangıç yeri 200 veya 300, sonra da test ve ince ayar gerekir.

Bu ayar nasıl yapılandırılır:

* `global.asax.cs`' de [ThreadPool. SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) metodunu kullanarak bu ayarı programlı bir şekilde değiştirmenizi öneririz. Örneğin:

```cs
private readonly int minThreads = 200;
void Application_Start(object sender, EventArgs e)
{
    // Code that runs on application startup
    AreaRegistration.RegisterAllAreas();
    RouteConfig.RegisterRoutes(RouteTable.Routes);
    BundleConfig.RegisterBundles(BundleTable.Bundles);
    ThreadPool.SetMinThreads(minThreads, minThreads);
}
```

  > [!NOTE]
  > Bu yöntem tarafından belirtilen değer genel bir ayardır ve tüm AppDomain etki alanı etkilendi. Örneğin, 4 çekirdekli bir makineniz varsa ve çalışma zamanı sırasında *MinWorkerThreads* ve *MINIOTHREADS* 'i CPU başına 50 olarak ayarlamak Istiyorsanız, **ThreadPool. SetMinThreads (200, 200)** kullanın.

* En düşük iş parçacığı ayarlarını, genellikle `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\`bulunan `Machine.config``<processModel>` yapılandırma öğesi altındaki [ *miniothreads* veya *MinWorkerThreads* yapılandırma ayarını](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) kullanarak belirtebilirsiniz. **Bu şekilde en düşük iş parçacığı sayısını bu şekilde ayarlamak, sistem genelinde bir ayar olduğundan genellikle önerilmez.**

  > [!NOTE]
  > Bu yapılandırma öğesinde belirtilen değer, *çekirdek başına* ayardır. Örneğin, 4 çekirdekli bir makineniz varsa ve *Miniothreads* ayarınızı çalışma zamanında 200 olarak istiyorsanız `<processModel minIoThreads="50"/>`kullanırsınız.
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>StackExchange. Redsıs kullanırken istemci üzerinde daha fazla aktarım sağlamak için sunucu GC 'yi etkinleştirin
Sunucu GC 'yi etkinleştirmek, istemcisini iyileştirebilirler ve StackExchange. Redsıs kullanırken daha iyi performans ve verimlilik sağlar. Sunucu GC ve nasıl etkinleştirileceği hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Sunucu GC 'yi etkinleştirmek için](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Atık Toplamanın Temelleri](/dotnet/standard/garbage-collection/fundamentals)
* [Çöp toplama ve performans](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Bağlantılar etrafında performans konuları

Her fiyatlandırma katmanı, istemci bağlantıları, bellek ve bant genişliği için farklı sınırlara sahiptir. Her önbellek boyutu belirli sayıda bağlantıya *izin verdiğinden,* redde her bağlantı ile ilişkili ek yük vardır. Bu ek yükün bir örneği, TLS/SSL şifreleme sonucu olarak CPU ve bellek kullanımı olabilir. Belirli bir önbellek boyutu için en fazla bağlantı sınırı, hafif bir şekilde yüklenmiş önbelleği varsayar. Bağlantı yüklerinden yükleme, istemci *işlemlerinden yükleme,* sistem kapasitesini aşarsa, geçerli önbellek boyutu için bağlantı sınırını aşmasanız bile önbellek, kapasite sorunları yaşayabilir.

Her katmana ait farklı bağlantı sınırları hakkında daha fazla bilgi için bkz. [redsıs fiyatlandırması Için Azure önbelleği](https://azure.microsoft.com/pricing/details/cache/). Bağlantılar ve diğer varsayılan yapılandırmalar hakkında daha fazla bilgi için bkz. [varsayılan redsıs sunucu yapılandırması](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Nasıl yaparım? önbelleğim sistem durumunu ve performansını izlemek mi?
Redsıs örnekleri için Microsoft Azure önbelleği [Azure Portal](https://portal.azure.com)izlenebilir. Ölçümleri görüntüleyebilir, ölçüm grafiklerini başlangıç panosuna sabitleyebilir, izleme grafiklerinin tarih ve saat aralığını özelleştirebilir, grafiklerden ölçümleri ekleyebilir ve kaldırabilir ve belirli koşullar karşılandığında uyarılar ayarlayabilirsiniz. Daha fazla bilgi için bkz. [redsıs Için Azure önbelleğini izleme](cache-how-to-monitor.md).

Redsıs **Kaynak menüsü** Için Azure önbelleği, önbelleğinizi izlemek ve sorunlarını gidermek için çeşitli araçlar içerir.

* **Sorunları tanılama ve** çözme, yaygın sorunlar ve bunları çözmeye yönelik stratejiler hakkında bilgi sağlar.
* **Kaynak sistem durumu** , kaynağınızı izler ve beklendiği gibi çalışıp çalışmadığını bildirir. Azure Kaynak sistem durumu hizmeti hakkında daha fazla bilgi için bkz. [Azure Kaynak durumu genel bakış](../resource-health/resource-health-overview.md).
* **Yeni destek isteği** önbelleğiniz için bir destek isteği açma seçenekleri sağlar.

Bu araçlar, Redsıs örnekleri için Azure önbelleğinizin durumunu izlemenize ve önbelleğe alma uygulamalarınızı yönetmenize yardımcı olur. Daha fazla bilgi için [Azure önbelleğini redsıs için yapılandırma](cache-configure.md)konusunun "destek & sorun giderme ayarları" bölümüne bakın.

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Neden zaman aşımlarını görüyorum?
Redsıs ile konuşmak için kullandığınız istemcide zaman aşımları meydana gelir. Redsıs sunucusuna bir komut gönderildiğinde, komut kuyruğa alınır ve Reddir sunucusu, sonunda komutu seçer ve yürütür. Ancak, bu işlem sırasında istemci zaman aşımına uğrar ve arama tarafında bir özel durum ortaya çıkar. Zaman aşımı sorunlarını giderme hakkında daha fazla bilgi için bkz. [istemci tarafı sorun giderme](cache-troubleshoot-client.md) ve [StackExchange. redsıs zaman aşımı özel durumları](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>İstemcimin neden önbellekten bağlantısı kesildi?
Önbellek bağlantısının kesilmesi için bazı yaygın nedenler aşağıda verilmiştir.

* İstemci tarafı nedenleri
  * İstemci uygulama yeniden dağıtıldı.
  * İstemci uygulaması bir ölçeklendirme işlemi gerçekleştirdi.
    * Cloud Services veya Web Apps durumunda bu, otomatik ölçeklendirme nedeniyle olabilir.
  * İstemci tarafındaki ağ katmanı değişti.
  * İstemcide veya istemci ile sunucu arasındaki ağ düğümlerinde geçici hatalar oluştu.
  * Bant genişliği eşik sınırlarına ulaşıldı.
  * CPU bağlantılı işlemlerin tamamlanması çok uzun sürdü.
* Sunucu tarafı nedenleri
  * Standart önbellek teklifiyle, Redsıs hizmeti için Azure önbelleği, birincil düğümden ikincil düğüme yük devretme işlemi başlattı.
  * Azure, önbelleğin dağıtıldığı örneğe düzeltme eki eklendi
    * Bu, Redsıs sunucu güncelleştirmeleri veya genel VM bakımı için olabilir.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Hangi Azure Önbellek teklifi bana uygundur?
> [!IMPORTANT]
> Azure Yönetilen Önbellek Hizmeti ve Azure Rol İçi Önbellek Hizmeti, geçen yılın [duyurusuna](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)göre 30 Kasım 2016 tarihinde **kullanımdan kaldırılmıştır** . Önerimiz, [redsıs Için Azure önbelleğini](https://azure.microsoft.com/services/cache/)kullanmaktır. Geçiş hakkında bilgi için bkz. [redsıs Için yönetilen önbellek hizmetinden Azure önbelleğine geçiş](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Redis için Azure Önbelleği
Redo için Azure Cache, 120 GB 'a kadar olan ve% 99,9 kullanılabilirlik SLA 'sı olan boyutlarda genel kullanıma sunulmuştur. Yeni [Premium katmanı](cache-premium-tier-intro.md) ,% 99,9 SLA Ile 1,2 TB 'a kadar, KÜMELEME, VNET ve kalıcılık desteği sunar.

Redsıs için Azure önbelleği, müşterilere Microsoft tarafından yönetilen, redin için güvenli ve adanmış bir Azure önbelleği kullanma olanağı sunar. Bu teklifle, redin tarafından sunulan zengin özellik kümesi ve ekosistemden ve Microsoft 'un güvenilir barındırma ve izleme özelliğinden faydalanabilirsiniz.

Yalnızca anahtar-değer çiftleri ile ilgilenen geleneksel önbellekler aksine redo, yüksek performanslı veri türleri için popüler. Redin Ayrıca, bir dizeye ekleme gibi bu türlerde atomik işlemleri çalıştırmayı destekler; bir Karmadaki değeri artırma; Bir listeye gönderiliyor; bilgi işlem kümesi kesişimi, birleşim ve fark; ya da sıralanmış bir küme içinde en yüksek sıralamaya sahip üyeyi alma. Diğer özellikler arasında işlemler, yayımlama/Sub, Lua komut dosyası, sınırlı yaşam süresi olan anahtarlar ve redin geleneksel bir önbellek gibi davranmasını sağlamak için yapılandırma ayarları bulunur.

Redsıs başarısına yönelik başka bir anahtar, onun etrafında yerleşik olarak bulunan sağlam ve canlı açık kaynaklı ekosistemdir. Bu, birden çok dilde kullanılabilir olan çeşitli redin istemcileri kümesine yansıtılır. Bu ekosistem ve çok sayıda istemci, Azure 'un içinde derlemenize olanak tanıyan neredeyse tüm iş yükleri için Azure önbelleğinin kullanılmasını sağlar.

Redin için Azure önbelleği kullanmaya başlama hakkında daha fazla bilgi için bkz. redsıs için [Azure önbelleğini](cache-dotnet-how-to-use-azure-redis-cache.md) ve [redsıs belgeleri Için Azure önbelleğini](index.md)kullanma.

### <a name="managed-cache-service"></a>Yönetilen Önbellek Hizmeti
[Yönetilen Önbellek Hizmeti 30 Kasım 2016 tarihinde kullanımdan kaldırıldı.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Arşivlenmiş belgeleri görüntülemek için bkz. [arşivlenmiş Yönetilen Önbellek hizmeti belgeleri](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>Rol İçi Önbellek
[Rol İçi Önbellek 30 Kasım 2016 ' de kullanımdan kaldırıldı.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Arşivlenmiş belgeleri görüntülemek için bkz. [arşivlenmiş rol içi önbellek belgeleri](/previous-versions/azure/azure-services/dn386103(v=azure.100)).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
