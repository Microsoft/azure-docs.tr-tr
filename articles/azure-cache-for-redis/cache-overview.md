---
title: Redsıs için Azure önbelleği nedir?
description: Redin için Azure önbelleğinin ne olduğunu ve nasıl yaygın olarak kullanıldığını öğrenin.
author: yegu-ms
ms.service: cache
ms.topic: overview
ms.date: 03/26/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 87e7505bddfce431b5e859fbbeee79f75867cfc9
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122644"
---
# <a name="azure-cache-for-redis-description"></a>Redis açıklaması için Azure önbelleği

Redsıs için Azure önbelleği, popüler yazılım [redin](https://redis.io/)tabanlıdır. Genellikle, arka uç veri depolarını yoğun bir şekilde bağımlı olan sistemlerin performans ve ölçeklenebilirliğini artırmak için önbellek olarak kullanılır. Performans, sık erişilen veriler uygulamaya yakın bir konumdaki hızlı depolama alanına geçici olarak kopyalanarak iyileştirilir. [Redsıs Için Azure önbelleği](https://redis.io/)sayesinde, bu hızlı depolama, bir veritabanı tarafından diskten yüklenmesi yerine redin Için Azure önbelleği ile bellek içinde bulunur.

Redde için Azure Cache, bellek içi veri yapısı deposu, dağıtılmış ilişkisel olmayan bir veritabanı ve bir ileti Aracısı olarak da kullanılabilir. Redis altyapısının düşük gecikmeli, yüksek aktarım hızına sahip performansından yararlanılarak uygulama performansı iyileştirilir.

Redsıs için Azure önbelleği, güvenli ve adanmış bir Redsıs önbelleğine erişmenizi sağlar. Redsıs için Azure önbelleği, Azure 'da barındırılan ve Azure içindeki veya dışındaki tüm uygulamalar tarafından erişilebilen Microsoft tarafından yönetilir.

## <a name="using-azure-cache-for-redis"></a>Redsıs için Azure önbelleğini kullanma

Redis için Azure önbelleğinin uygulama mimarisini desteklemek veya uygulama performansını geliştirmek için kullanıldığı birçok yaygın desen vardır. En yaygın olanlarından bazıları şunlardır:

| Desen      | Açıklama                                        |
| ------------ | -------------------------------------------------- |
| [Edilgen Önbellek](cache-web-app-cache-aside-leaderboard.md) | Bir veritabanı büyük olabileceği için, tüm veritabanının bir önbelleğe yüklenmesi önerilen bir yaklaşım değildir. Veri öğelerini önbelleğe yalnızca gerekli olduğunda yüklemek için [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) düzeninin kullanılması yaygındır. Sistem arka uç verilerinde değişiklikler yaptığında da önbelleği güncelleyebilir ve diğer istemcilerle dağıtabilir. Ek olarak, sistem veri öğeleri için bir süre sonu ayarlayabilir ya da veri güncelleştirmelerinin önbelleğe yeniden yüklenmesine neden olacak bir çıkarma ilkesi kullanabilir.|
| [İçeriği Önbelleğe Alma](cache-aspnet-output-cache-provider.md) | Birçok Web sayfası, üst bilgiler, alt bilgiler, araç çubukları, menüler vb. şablonlardan oluşturulur. Bunlar aslında sık değişmez ve dinamik olarak oluşturulmamalıdır. Redde için Azure önbelleği gibi bellek içi önbellek kullanımı, web sunucularınız için arka uç veri depolarına kıyasla bu tür statik içerik için hızlı erişim sağlayacak. Bu düzen, içeriği dinamik olarak oluşturmak için gerekecek işleme süresini ve sunucu yükünü azaltır. Bunun yapılması, web sunucularının daha iyi yanıt vermesini sağlar ve yükleri işlemek için gereken sunucu sayısını azaltmanızı sağlayabilir. Redsıs için Azure Cache, ASP.NET ile bu kalıbı desteklemeye yardımcı olmak için Redsıs çıkış önbelleği sağlayıcısı sağlar.|
| [Kullanıcı oturumunu önbelleğe alma](cache-aspnet-session-state-provider.md) | Bu düzen yaygın olarak alışveriş sepetleri ve bir web uygulamasının kullanıcı tanımlama bilgileriyle ilişkilendirmek isteyebileceği diğer kullanıcı geçmişi türündeki bilgilerle birlikte kullanılır. Bir tanımlama bilgisinde çok fazla bilgi depolamak, tanımlama bilgisi boyutu arttıkça ve tanımlama bilgisi her istek ile birlikte geçirilip doğrulandıkça performansı olumsuz yönde etkileyebilir. Tipik bir çözüm, bir arka uç veritabanında verileri sorgulamak üzere tanımlama bilgisinin anahtar olarak kullanılmasıdır. Redde için Azure önbelleği gibi bellek içi önbellek kullanmak, bilgilerin bir kullanıcıyla ilişkilendirilmesi, tam ilişkisel veritabanıyla etkileşime kıyasla çok daha hızlıdır. |
| İş ve ileti sıraya alma | Uygulamalar istek aldığında genellikle istekle ilişkili işlemlerin yürütülmesi ek zaman alır. Daha uzun süre çalışan işlemleri bir sıraya ekleyerek ertelemek ve daha sonra, muhtemelen başka bir sunucu ile işleme almak yaygın bir düzendir. Bu iş erteleme yöntemine görevi sıraya alma adı verilir. Görev sıralarını desteklemek için tasarlanmış birçok yazılım bileşeni mevcuttur. Redde için Azure Cache Ayrıca bu amaca dağıtılmış bir sıra olarak hizmet verir.|
| Dağıtılmış işlemler | Uygulamaların tek bir işlem (atomik) olarak bir arka uç veri deposuna karşı bir dizi komut yürütebilmesi genel bir gereksinimdir. Tüm komutlar başarılı olmalı veya tümü ilk durumuna geri döndürülmelidir. Redsıs için Azure cache [, işlem biçiminde](https://redis.io/topics/transactions)tek bir işlem olarak bir dizi komutun yürütülmesini destekler. |

## <a name="azure-cache-for-redis-offerings"></a>Redsıs teklifleri için Azure önbelleği

Redo için Azure Cache aşağıdaki katmanlarda kullanılabilir:

| Katman | Açıklama |
|---|---|
Temel | Tek düğümlü bir önbellek. Bu katman birden fazla bellek boyutunu (250 MB - 53 GB) destekler. Geliştirme/test ve kritik olmayan iş yükleri için ideal bir katmandır. Temel katmanda hizmet düzeyi sözleşmesi (SLA) yoktur |
| Standart | Microsoft tarafından yönetilen iki düğümlü bir birincil/ikincil yapılandırmada çoğaltılan ve yüksek kullanılabilirlik SLA’sı (%99,9) ile sunulan önbellek. |
| Premium | Premium katman, kurumsal olmaya yönelik katmandır. Premium katmanı Önbellekler daha fazla özelliği destekler ve düşük gecikmeyle daha yüksek aktarım hızına sahiptir. Premium katmanındaki önbellekler, Temel veya Standart Katmanına kıyasla daha iyi performans sağlayan daha güçlü donanımlara dağıtılır. Bu avantaj, aynı boyuttaki bir önbellek için üretilen iş hızının Standart katmana kıyasla Premium 'da daha yüksek olacağı anlamına gelir. |

> [!TIP]
> Premium önbellekler ile boyut, aktarım hızı ve bant genişliği hakkında daha fazla bilgi için bkz. [REDSıS hakkında Azure önbelleği](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Önbelleğiniz oluşturulduktan sonra ölçeğini daha yüksek bir katmana artırabilirsiniz. Ölçeğin daha düşük bir katmana indirilmesi desteklenmez. Adım adım ölçeklendirme yönergeleri için bkz. [Azure önbelleğini redsıs Için ölçeklendirme](cache-how-to-scale.md) ve [bir ölçeklendirme işlemini otomatikleştirme](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Özellik karşılaştırması

[Redsıs fiyatlandırma sayfası Için Azure önbelleği](https://azure.microsoft.com/pricing/details/cache/) , her katman için ayrıntılı bir karşılaştırma sağlar. Aşağıdaki tablo, katmana göre desteklenen özelliklerden bazılarını açıklamaya yardımcı olur:

| Özellik Açıklaması | Premium | Standart | Temel |
| ------------------- | :-----: | :------: | :---: |
| [Hizmet Düzeyi Sözleşmesi (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis veri kalıcılığı](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis kümesi](cache-how-to-premium-clustering.md) |✔|-|-|
| [Güvenlik duvarı kuralları ile güvenlik](cache-configure.md#firewall) |✔|✔|✔|
| Aktarım sırasında şifreleme |✔|✔|✔|
| [VNet ile geliştirilmiş güvenlik ve yalıtım](cache-how-to-premium-vnet.md) |✔|-|-|
| [İçeri/Dışarı Aktarma](cache-how-to-import-export-data.md) |✔|-|-|
| [Zamanlanan güncelleştirmeler](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Coğrafi çoğaltma](cache-how-to-geo-replication.md) |✔|-|-|
| [Yeniden başlatma](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Sonraki adımlar

* [ASP.NET Web uygulaması hızlı başlangıç](cache-web-app-howto.md) Redsıs için Azure önbelleği kullanan basit bir ASP.NET Web uygulaması oluşturun.
* [.Net hızlı başlangıç](cache-dotnet-how-to-use-azure-redis-cache.md) Redsıs için Azure önbelleği kullanan bir .NET uygulaması oluşturun.
* [.NET Core hızlı başlangıç](cache-dotnet-core-quickstart.md) Redsıs için Azure önbelleği kullanan bir .NET Core uygulaması oluşturun.
* [Node. js hızlı başlangıç](cache-nodejs-get-started.md) Redsıs için Azure önbelleği kullanan basit bir Node. js uygulaması oluşturun.
* [Java hızlı başlangıç](cache-java-get-started.md) Redsıs için Azure önbelleği kullanan basit bir Java uygulaması oluşturun.
* [Python hızlı başlangıç](cache-python-get-started.md) Redsıs için Azure önbelleği kullanan bir Python uygulaması oluşturun.
