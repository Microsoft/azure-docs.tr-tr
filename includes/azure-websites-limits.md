---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/17/2020
ms.author: msangapu
ms.openlocfilehash: dad7799cb5a7579b28847e3968b6b38f1f98298a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327542"
---
| Kaynak | Ücretsiz | Paylaşılan | Temel | Standart | Premium (v1-v3) | Yalıtılmış </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Azure App Service plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> başına [Web, mobil veya API Apps](https://azure.microsoft.com/services/app-service/) |10 |100 |Sınırsız<sup>2</sup> |Sınırsız<sup>2</sup> |Sınırsız<sup>2</sup> |Sınırsız<sup>2</sup>|
| [App Service planı](../articles/app-service/overview-hosting-plans.md) |Bölge başına 10 |kaynak grubu başına 10 |kaynak grubu başına 100 |kaynak grubu başına 100 |kaynak grubu başına 100 |kaynak grubu başına 100|
| İşlem örneği türü |Paylaşılan |Paylaşılan |Adanmış<sup>3</sup> |Adanmış<sup>3</sup> |Adanmış<sup>3</sup></p> |Adanmış<sup>3</sup>|
| [Ölçeği](../articles/app-service/manage-scale-up.md) genişletme (en fazla örnek) |1 paylaşılan |1 paylaşılan |3 ayrılmış<sup>3</sup> |10 adanmış<sup>3</sup> | V1 ve v2 için ayrılan 20; 30 ila v3 için ayrılmış. <sup>3</sup>|100 adanmış<sup>4</sup>|
| Depolama<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> |1 TB<sup>5</sup> <br/><br/> Kullanılabilir depolama kotası 999 GB 'dir. |
| CPU süresi (5 dakika)<sup>6</sup> |3 dakika |3 dakika |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a> |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a> |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a> |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a>|
| CPU süresi (gün)<sup>6</sup> |60 dakika |240 dakika |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a> |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a> |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a> |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a> |
| Bellek (1 saat) |App Service plan başına 1.024 MB |Uygulama başına 1.024 MB |Yok |Yok |Yok |Yok |
| Bant genişliği |165 MB |Sınırsız, [veri aktarımı ücretleri](https://azure.microsoft.com/pricing/details/data-transfers/) uygulanır |Sınırsız, [veri aktarımı ücretleri](https://azure.microsoft.com/pricing/details/data-transfers/) uygulanır |Sınırsız, [veri aktarımı ücretleri](https://azure.microsoft.com/pricing/details/data-transfers/) uygulanır |Sınırsız, [veri aktarımı ücretleri](https://azure.microsoft.com/pricing/details/data-transfers/) uygulanır |Sınırsız, [veri aktarımı ücretleri](https://azure.microsoft.com/pricing/details/data-transfers/) uygulanır |
| Uygulama mimarisi |32 bit |32 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |
| Örnek başına Web Yuvaları<sup>7</sup> |5 |35 |350 |Sınırsız |Sınırsız |Sınırsız |
| Örnek başına giden IP bağlantısı sayısı | 600 | 600 | Örnek boyutuna bağlıdır<sup>8</sup> | Örnek boyutuna bağlıdır<sup>8</sup> | Örnek boyutuna bağlıdır<sup>8</sup> | 16.000 |
| Uygulama başına eşzamanlı [hata ayıklayıcı bağlantıları](../articles/app-service/troubleshoot-dotnet-visual-studio.md) |1 |1 |1 |5 |5 |5 |
| Abonelik başına App Service sertifikaları<sup>9</sup>| Desteklenmez | Desteklenmez |10 |10 |10 |10 |
| Uygulama başına özel etki alanları</a> |0 (yalnızca azurewebsites.net alt etki alanı)|500 |500 |500 |500 |500 |
| Özel etki alanı [SSL desteği](../articles/app-service/configure-ssl-certificate.md) |Desteklenmiyor, \* Varsayılan olarak. azurewebsites.NET için joker sertifika kullanılabilir|Desteklenmiyor, \* Varsayılan olarak. azurewebsites.NET için joker sertifika kullanılabilir|Sınırsız SNI SSL bağlantı |Sınırsız SNI SSL ve 1 IP SSL bağlantı dahil değildir |Sınırsız SNI SSL ve 1 IP SSL bağlantı dahil değildir | Sınırsız SNI SSL ve 1 IP SSL bağlantı dahil değildir|
| Karma bağlantılar | | | plan başına 5 | plan başına 25 | Uygulama başına 200 | Uygulama başına 200 |
| [Sanal Ağ Tümleştirmesi](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| [Özel uç noktalar](../articles/app-service/networking/private-endpoint.md) | | |   |   |  Uygulama başına 100  |    |
| Tümleşik yük dengeleyici | |X |X |X |X |X<sup>10</sup> |
| [Erişim kısıtlamaları](../articles/app-service/networking-features.md#access-restrictions) | Uygulama başına 512 kural | Uygulama başına 512 kural | Uygulama başına 512 kural | Uygulama başına 512 kural | Uygulama başına 512 kural | Uygulama başına 512 kural |
| [Her zaman açık](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Zamanlanan yedeklemeler](../articles/app-service/manage-backup.md) | | | | Her 2 saatte bir zamanlanan yedeklemeler, günde en fazla 12 yedekleme (el ile + zamanlanmış) | Her saat zamanlanan yedeklemeler, günde en fazla 50 yedekleme (el ile + zamanlanmış) | Her saat zamanlanan yedeklemeler, günde en fazla 50 yedekleme (el ile + zamanlanmış) |
| [Otomatik Ölçeklendirme](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [Web işleri](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Uç nokta izleme](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| Uygulama başına [hazırlama yuvaları](../articles/app-service/deploy-staging-slots.md)| | | |5 |20 |20 |
| [Üretimde test etme](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [Tanılama Günlükleri](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [Kimlik doğrulama ve yetkilendirme](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [App Service yönetilen Sertifikalar (Genel Önizleme)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| SLA | |  |%99,95|%99,95|%99,95|%99,95|

<sup>1</sup> uygulama ve depolama kotaları, aksi belirtilmediği takdirde App Service plan başına yapılır.

<sup>2</sup> bu makinelerde barındırabilmeniz gereken gerçek uygulama sayısı, uygulamaların etkinliğine, makine örneklerinin boyutuna ve karşılık gelen kaynak kullanımına göre değişir.

<sup>3</sup> ayrılmış örnek farklı boyutlarda olabilir. Daha fazla bilgi için bkz. [App Service fiyatlandırması](https://azure.microsoft.com/pricing/details/app-service/).

istek üzerine <sup>4</sup> daha fazlasına izin verilir.

<sup>5</sup> depolama sınırı, aynı App Service planındaki tüm uygulamalarda bulunan toplam içerik boyutudur. Tek bir kaynak grubundaki ve bölgedeki tüm App Service planları genelinde tüm uygulamaların toplam içerik boyutu 500 GB 'ı aşamaz. App Service barındırılan uygulamalar için dosya sistemi kotası, bir bölgede ve kaynak grubunda oluşturulan App Service planlarının toplamı tarafından belirlenir.

<sup>6</sup> bu kaynaklar ayrılmış örneklerde (örnek boyutu ve örnek sayısı) fiziksel kaynaklar tarafından sınırlandırılır.

<sup>7</sup> temel katmandaki bir uygulamayı iki örneğe ölçeklendirirseniz, iki örnek için 350 eş zamanlı bağlantınız vardır. Standart katman ve yukarıdaki sürümler için Web soketlerine teorik sınır yoktur, ancak diğer faktörler Web Yuvaları sayısını sınırlayabilir. Örneğin, izin verilen en fazla eşzamanlı istek (tarafından tanımlanır), her bir `maxConcurrentRequestsPerCpu` küçük VM başına 7.500, orta düzey VM başına 15.000 (7.500 x 2 çekirdek) ve büyük VM başına 75.000 (18.750 x 4 çekirdek).

<sup>8</sup> her örnek için en fazla IP bağlantısı vardır ve örnek boyutuna bağlıdır: B1/S1/P1V3 örneği başına 1.920, B2/S2/P2V3 örneği başına 3.968, B3/S3/P3V3 örneği başına 8.064.

<sup>9</sup> abonelik başına App Service sertifikası kota sınırı, 200 üst sınırına yönelik destek isteği aracılığıyla artırılabilir.

<sup>10</sup> App Service yalıtılmış sku 'ları Azure Load Balancer ile dahili olarak yük dengeli (ILB) olabilir, bu nedenle internet 'ten genel bağlantı yoktur. Sonuç olarak, ıLB yalıtılmış bir App Service bazı özelliklerinin ıLB ağ uç noktasına doğrudan erişimi olan makinelerden kullanılması gerekir.

<sup>11</sup> App Service örneğiniz içinde isteğe bağlı, zamanlamaya göre veya sürekli bir arka plan görevi olarak özel yürütülebilir dosyalar ve/veya betikler çalıştırın. Always on sürekli Web Işleri yürütmesi için gereklidir. App Service örneğinde çalışabilecek WebJobs sayısında önceden tanımlanmış bir sınır yoktur. Uygulama kodunun ne işe çalışmadığına bağlı olarak pratik sınırlar vardır.

<sup>12</sup> Naked etki alanı desteklenmez. Yalnızca standart sertifikaları verme (joker sertifikaları kullanılamaz). Özel etki alanı başına yalnızca bir adet ücretsiz sertifikayla sınırlıdır.
