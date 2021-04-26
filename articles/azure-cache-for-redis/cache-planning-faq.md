---
title: Red, planlama için Azure önbelleği SSS
description: Redsıs için Azure önbelleği planlaması yapmanıza yardımcı olacak yaygın soruların yanıtlarını öğrenin
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398b567e173691068e6dd932e075d6598dcfe601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92537414"
---
# <a name="azure-cache-for-redis-planning-faqs"></a>Red, planlama için Azure önbelleği SSS

Bu makalede, redin için Azure önbelleğinin nasıl planlanacağı hakkında yaygın soruların yanıtları sağlanmaktadır.

## <a name="common-questions-and-answers"></a>Yaygın sorular ve yanıtları
Bu bölümde aşağıdaki SSS ele alınmaktadır:

* [Redsıs performansı için Azure önbelleği](#azure-cache-for-redis-performance)
* [Hangi bölgede önbelleğinizi bulmalıyım?](#in-what-region-should-i-locate-my-cache)
* [Önbelleğe alınan veriler nerede bulunur?](#where-do-my-cached-data-reside)
* [Redsıs için Azure önbelleği için nasıl faturalandırılırım?](#how-am-i-billed-for-azure-cache-for-redis)
* [Redsıs için Azure önbelleğini Azure Kamu bulutu, Azure Çin 21Vianet bulutu veya Microsoft Azure Almanya kullanabilir miyim?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

### <a name="azure-cache-for-redis-performance"></a>Redsıs performansı için Azure önbelleği
Aşağıdaki tabloda, `redis-benchmark.exe` bir IaaS VM 'Sinden redsıs uç noktası Için Azure önbelleğine karşı kullanılan çeşitli standart ve Premium önbellekleri test edilirken gözlemlenen maksimum bant genişliği değerleri gösterilmektedir. TLS verimlilik için redsıs-kıyaslama, Redsıs uç noktası için Azure önbelleğine bağlanmak üzere Stunnel ile birlikte kullanılır.

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
| C0 | 250 MB | Paylaşılan | 100/12,5  |  15.000 |   7.500 |
| C1 |   1 GB | 1      | 500/62,5  |  38.000 |  20.720 |
| C2 | 2,5 GB | 2      | 500/62,5  |  41.000 |  37.000 |
| C3 |   6 GB | 4      | 1000/125  | 100.000 |  90,000 |
| C4 |  13 GB | 2      | 500/62,5  |  60.000 |  55.000 |
| C5 |  26 GB | 4      | 1.000/125 | 102.000 |  93.000 |
| C6 |  53 GB | 8      | 2.000/250 | 126.000 | 120.000 |
| **Premium önbellek boyutları** | |**Parça başına CPU çekirdekleri** | **Megabit/sn (MB/sn)/megabayt/sn (MB/s)** |**Saniyedeki istek sayısı (RPS) SSL olmayan, parça başına** |**Saniyedeki istek/saniye (RPS) SSL, parça başına** |
| P1 |   6 GB |  2 | 1.500/187,5 | 180.000 | 172.000 |
| P2 |  13 GB |  4 | 3.000/375   | 350.000 | 341.000 |
| P3 |  26 GB |  4 | 3.000/375   | 350.000 | 341.000 |
| P4 |  53 GB |  8 | 6.000/750   | 400,000 | 373.000 |
| P5 | 120 GB | 20 | 6.000/750   | 400,000 | 373.000 |

Stunnel ayarlama veya gibi Redsıs araçlarını indirme hakkında yönergeler için `redis-benchmark.exe` bkz. [redsıs komutlarını nasıl çalıştırabilirim?](cache-development-faq.md#how-can-i-run-redis-commands).

### <a name="in-what-region-should-i-locate-my-cache"></a>Hangi bölgede önbelleğinizi bulmalıyım?
En iyi performans ve en düşük gecikme için, önbellek istemci uygulamanızla aynı bölgedeki redin için Azure önbelleğinizi bulun.

### <a name="where-do-my-cached-data-reside"></a>Önbelleğe alınan veriler nerede bulunur?
Redsıs için Azure önbellek, önbelleğinizi barındıran katmana bağlı olarak, uygulama verilerinizi VM veya sanal makinelerin RAM 'ine depolar. Verileriniz, varsayılan olarak seçtiğiniz Azure bölgesinde kesin olarak bulunur. Verilerinizin bölge bırakabileceği iki durum vardır:
* Önbellekte kalıcılığı etkinleştirdiğinizde Redsıs için Azure önbelleği, verilerinizi sahip olduğunuz bir Azure depolama hesabına yedekedecektir. Sağladığınız depolama hesabı başka bir bölgede yer alıyorsa, verilerinizin bir kopyası orada sona alınacaktır.
* Coğrafi çoğaltmayı ayarlarsanız ve ikincil önbelleğiniz farklı bir bölgedeyse ve bu durum normal şekilde, bu durumda verileriniz bu bölgeye çoğaltılır.

Bu özellikleri kullanmak için Redsıs için Azure önbelleğini açık bir şekilde yapılandırmanız gerekir. Depolama hesabının veya ikincil önbelleğin bulunduğu bölge üzerinde de tamamen denetiminiz vardır.

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Redsıs için Azure önbelleği için nasıl faturalandırılırım?
Redsıs fiyatlandırması için Azure önbelleği [burada](https://azure.microsoft.com/pricing/details/cache/)yer alır. Fiyatlandırma sayfası, fiyatlandırmayı saatlik ve aylık bir ücret olarak listeler. Önbellekler, önbelleğin silindiği zamana kadar önbelleğin oluşturulduğu zamandan itibaren dakika başına faturalandırılır. Bir önbelleğin faturalandırmasını durdurma veya duraklatma seçeneği yoktur.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>Redsıs için Azure önbelleğini Azure Kamu bulutu, Azure Çin 21Vianet bulutu veya Microsoft Azure Almanya kullanabilir miyim?
Evet, Redu için Azure önbelleği, Azure Kamu bulutu, Azure Çin 21Vianet bulutu ve Microsoft Azure Almanya kullanılabilir. Redo için Azure önbelleğine erişme ve bunları yönetmeye yönelik URL 'Ler, Azure genel bulutu ile karşılaştırıldığında bu bulutlarda farklılık açmaktadır.

| Bulut   | Redsıs için DNS son eki            |
|---------|---------------------------------|
| Genel  | *. redis.cache.windows.net       |
| US Gov  | *. redis.cache.usgovcloudapi.net |
| Almanya | *. redis.cache.cloudapi.de       |
| Çin   | *. redis.cache.chinacloudapi.cn  |

Reda için Azure önbelleğini diğer bulutlarla birlikte kullanma hakkında daha fazla bilgi için aşağıdaki bağlantılara bakın.

- [Azure Kamu veritabanları-Redsıs için Azure önbelleği](../azure-government/compare-azure-government-global-azure.md)
- [Azure Çin 21Vianet bulutu-Redsıs için Azure önbelleği](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Almanya](https://azure.microsoft.com/overview/clouds/germany/)

Azure için Azure önbelleğini Azure Kamu bulutu, Azure Çin 21Vianet bulutu ve Microsoft Azure Almanya PowerShell ile birlikte kullanma hakkında bilgi için bkz. [diğer bulutlara bağlanma-Redsıs PowerShell Için Azure önbelleği](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

## <a name="next-steps"></a>Sonraki adımlar

[Redsıs SSS için diğer Azure önbelleği](cache-faq.md)hakkında bilgi edinin.