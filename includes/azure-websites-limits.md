---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: 1f08067768f2d4a0a0c2ab31b3db1c9f2c9e1521
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476957"
---
| Kaynak | Ücretsiz | Paylaşımlı | Temel | Standart | Premium (v2) | Yalıtılmış </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Azure App Service plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> başına [Web, mobil veya API Apps](https://azure.microsoft.com/services/app-service/) |10 |100 |Sınırsız<sup>2</sup> |Sınırsız<sup>2</sup> |Sınırsız<sup>2</sup> |Sınırsız<sup>2</sup>|
| [App Service planı](../articles/app-service/overview-hosting-plans.md) |Bölge başına 10 |kaynak grubu başına 10 |kaynak grubu başına 100 |kaynak grubu başına 100 |kaynak grubu başına 100 |kaynak grubu başına 100|
| İşlem örneği türü |Paylaşımlı |Paylaşımlı |Adanmış<sup>3</sup> |Adanmış<sup>3</sup> |Adanmış<sup>3</sup></p> |Adanmış<sup>3</sup>|
| [Ölçeği](../articles/app-service/manage-scale-up.md) genişletme (en fazla örnek) |1 paylaşılan |1 paylaşılan |3 ayrılmış<sup>3</sup> |10 adanmış<sup>3</sup> |20 ayrılmış<sup>3</sup>|100 adanmış<sup>4</sup>|
| Depolama<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU süresi (5 dakika)<sup>6</sup> |3 dakika |3 dakika |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a> |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a> |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a> |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a>|
| CPU süresi (gün)<sup>6</sup> |60 dakika |240 dakika |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a> |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a> |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a> |Sınırsız, standart [oranlarda](https://azure.microsoft.com/pricing/details/app-service/) öde</a> |
| Bellek (1 saat) |App Service plan başına 1.024 MB |Uygulama başına 1.024 MB |Yok |Yok |Yok |Yok |
| Bant Genişliği |165 MB |Sınırsız, [veri aktarımı ücretleri](https://azure.microsoft.com/pricing/details/data-transfers/) uygulanır |Sınırsız, [veri aktarımı ücretleri](https://azure.microsoft.com/pricing/details/data-transfers/) uygulanır |Sınırsız, [veri aktarımı ücretleri](https://azure.microsoft.com/pricing/details/data-transfers/) uygulanır |Sınırsız, [veri aktarımı ücretleri](https://azure.microsoft.com/pricing/details/data-transfers/) uygulanır |Sınırsız, [veri aktarımı ücretleri](https://azure.microsoft.com/pricing/details/data-transfers/) uygulanır |
| Uygulama mimarisi |32 bit |32 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |
| Örnek başına Web Yuvaları<sup>7</sup> |5 |35 |350 |Sınırsız |Sınırsız |Sınırsız |
| IP bağlantıları | 600 | 600 | Örnek boyutuna bağlıdır<sup>8</sup> | Örnek boyutuna bağlıdır<sup>8</sup> | Örnek boyutuna bağlıdır<sup>8</sup> | 64.000 |
| Uygulama başına eşzamanlı [hata ayıklayıcı bağlantıları](../articles/app-service/troubleshoot-dotnet-visual-studio.md) |1 |1 |1 |5 |5 |5 |
| Abonelik başına App Service sertifikaları<sup>9</sup>| Desteklenmiyor | Desteklenmiyor |10 |10 |10 |10 |
| Uygulama başına özel etki alanları</a> |0 (yalnızca azurewebsites.net alt etki alanı)|500 |500 |500 |500 |500 |
| Özel etki alanı [SSL desteği](../articles/app-service/configure-ssl-certificate.md) |Desteklenmiyor, *. azurewebsites.net için joker karakter sertifikası varsayılan olarak kullanılabilir|Desteklenmiyor, *. azurewebsites.net için joker karakter sertifikası varsayılan olarak kullanılabilir|Sınırsız SNI SSL bağlantı |Sınırsız SNI SSL ve 1 IP SSL bağlantı dahil değildir |Sınırsız SNI SSL ve 1 IP SSL bağlantı dahil değildir | Sınırsız SNI SSL ve 1 IP SSL bağlantı dahil değildir|
| Plan başına karma bağlantı | | | 5 | 25 | 200 | 200 |
| Tümleşik yük dengeleyici | |X |X |X |X |X<sup>10</sup> |
| [Her zaman açık](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Zamanlanan yedeklemeler](../articles/app-service/manage-backup.md) | | | | Her 2 saatte bir zamanlanan yedeklemeler, günde en fazla 12 yedekleme (el ile + zamanlanmış) | Her saat zamanlanan yedeklemeler, günde en fazla 50 yedekleme (el ile + zamanlanmış) | Her saat zamanlanan yedeklemeler, günde en fazla 50 yedekleme (el ile + zamanlanmış) |
| [Otomatik Ölçeklendirme](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [Web işleri](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Azure Zamanlayıcı](https://azure.microsoft.com/services/scheduler/) desteği | |X |X |X |X |X |
| [Uç Nokta izleme](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Hazırlama Yuvaları](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |%99,95|%99,95|%99,95|%99,95|  

<sup>1</sup> Uygulamalar ve depolama kotaları, aksi belirtilmediği takdirde App Service plan başına yapılır.  
<sup>2</sup> Bu makinelerde barındırabilmeniz gereken uygulamaların gerçek sayısı, uygulamaların etkinliğine, makine örneklerinin boyutuna ve karşılık gelen kaynak kullanımına göre değişir.  
<sup>3</sup> Ayrılmış örnekler farklı boyutlarda olabilir. Daha fazla bilgi için bkz. [App Service fiyatlandırması](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> İstek üzerine daha fazlasına izin verilir.  
<sup>5</sup> Depolama sınırı, aynı App Service planındaki tüm uygulamalarda bulunan toplam içerik boyutudur. Tek bir kaynak grubundaki ve bölgedeki tüm App Service planları genelinde tüm uygulamaların toplam içerik boyutu 500GB ' i aşamaz.  
<sup>6</sup> Bu kaynaklar, adanmış örneklerde (örnek boyutu ve örnek sayısı) fiziksel kaynaklar tarafından sınırlandırılır.  
<sup>7</sup> Temel katmandaki bir uygulamayı iki örneğe ölçeklendirirseniz, iki örnek için 350 eş zamanlı bağlantınız vardır. Standart katman ve yukarıdaki sürümler için Web soketlerine teorik sınır yoktur, ancak diğer faktörler Web Yuvaları sayısını sınırlayabilir. Örneğin, izin verilen en fazla eşzamanlı istek (`maxConcurrentRequestsPerCpu`tarafından tanımlanır), her bir küçük VM başına 7.500, orta sanal makine başına 15.000 (7.500 x 2 çekirdek) ve büyük VM başına 75.000 (18.750 x 4 çekirdek).  
<sup>8</sup> En fazla IP bağlantısı, örnek başına yapılır ve örnek boyutuna bağlıdır: B1/S1/P1V2 örneği başına 1.920, B2/S2/P2V2 örneği başına 3.968, B3/S3/P3V2 örneği başına 8.064.  
<sup>9</sup> Abonelik başına App Service Sertifikası kota sınırı, en fazla 200 sınırına yönelik destek isteği aracılığıyla artırılabilir.  
<sup>10</sup> App Service Yalıtılmış SKU 'Lar Azure Load Balancer ile dahili olarak yük dengeli (ıLB) olabilir, bu nedenle internet 'ten genel bağlantı yoktur. Bunun sonucunda, ILB Yalıtılmış App Service’in bazı özelliklerin, ILB ağ uç noktasına doğrudan erişimi olan makinelerden kullanılması gerekir.  
<sup>11</sup> App Service örneğiniz içinde isteğe bağlı, zamanlamaya göre veya sürekli bir arka plan görevi olarak özel yürütülebilir dosyalar ve/veya betikler çalıştırın. WebJob'ların sürekli yürütülebilmesi için Her Zaman Açık özelliği gereklidir. Zamanlanan WebJob'lar için Azure Zamanlayıcı Ücretsiz veya Standart gereklidir. App Service örneğinde çalışabilecek WebJobs sayısında önceden tanımlanmış bir sınır yoktur. Uygulama kodunun ne işe çalışmadığına bağlı olarak pratik sınırlar vardır.  