---
title: Azure Analysis Services Server ölçümlerini izleme | Microsoft Docs
description: Sunucularınızın performansını ve sistem durumunu izlemenize yardımcı olması için portaldaki ücretsiz bir araç olan Azure Ölçüm Gezgini Analysis Services nasıl kullanacağınızı öğrenin.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9e6eab38e0f7bd55fa671aa8c1e99693eeb54c84
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572725"
---
# <a name="monitor-server-metrics"></a>Sunucu ölçümlerini izleme

Analysis Services, portaldaki ücretsiz bir araç olan Azure Ölçüm Gezgini ve sunucularınızın performansını ve sistem durumunu izlemenize yardımcı olmak için ölçümler sağlar. Örneğin, bellek ve CPU kullanımını, istemci bağlantı sayısını ve sorgu kaynak tüketimini izleyin. Analysis Services, diğer Azure hizmetleriyle aynı izleme çerçevesini kullanır. Daha fazla bilgi için bkz. [Azure Ölçüm Gezgini](../azure-monitor/platform/metrics-getting-started.md)kullanmaya başlama.

Daha ayrıntılı tanılama gerçekleştirmek, performansı izlemek ve bir kaynak grubunda veya abonelikte birden çok hizmet kaynağı arasındaki eğilimleri belirlemek için [Azure izleyici](../azure-monitor/overview.md)'yi kullanın. Azure Izleyici (hizmet), faturalandırılabilir bir hizmetle sonuçlanabilir.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Analysis Services sunucuya yönelik ölçümleri izlemek için

1. Azure portal, **ölçümler**' i seçin.

    ![Azure portalında izleme](./media/analysis-services-monitor/aas-monitor-portal.png)

2. **Ölçüm**bölümünde, grafiğinizde içerilecek ölçümleri seçin. 

    ![Grafiği izle](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Sunucu ölçümleri

İzleme senaryolarınız için hangi ölçümlerin en iyi olduğunu anlamak için bu tabloyu kullanın. Aynı grafikte yalnızca aynı birimin ölçümleri görüntülenebilir.

|Ölçüm|Ölçüm görünen adı|Birim|Toplama türü|Açıklama|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Komut havuzu Iş kuyruğu uzunluğu|Sayı|Ortalama|Komut iş parçacığı havuzu sırasındaki iş sayısı.|
|CurrentConnections|Bağlantı: geçerli bağlantılar|Sayı|Ortalama|Kurulan istemci bağlantılarının geçerli sayısı.|
|CurrentUserSessions|Geçerli Kullanıcı oturumları|Sayı|Ortalama|Geçerli kullanıcı oturumlarının sayısı.|
|mashup_engine_memory_metric|D motoru belleği|Sayacının|Ortalama|Karma altyapı işlemlerine göre bellek kullanımı|
|mashup_engine_qpu_metric|A motoru QPU|Sayı|Ortalama|Karma altyapı işlemlerine göre QPU kullanımı|
|memory_metric|Bellek|Sayacının|Ortalama|Bellek. S1 için 0-25 GB, S2 için 0-50 GB ve S4 için 0-100 GB|
|memory_thrashing_metric|Bellek dağılması|Yüzde|Ortalama|Ortalama bellek miktarı.|
|CleanerCurrentPrice|Bellek: temizleyici geçerli fiyatı|Sayı|Ortalama|Geçerli bellek fiyatı, $/Byte/Time, 1000 olarak normalleştirilemez.|
|Cleanermemorynonınkable|Bellek: temizleyici bellek daraltılamaz|Sayacının|Ortalama|Arka plan temizleyici tarafından temizlenme konusu değil, bayt cinsinden bellek miktarı.|
|Cleanermemoryshrınkable|Bellek: temizleyici bellek shrınılabilir|Sayacının|Ortalama|Arka plan temizleyici tarafından temizlenme konusu bayt cinsinden bellek miktarı.|
|MemoryLimitHard|Bellek: bellek sınırı sabit|Sayacının|Ortalama|Yapılandırma dosyasından sabit bellek sınırı.|
|Memoryhighlimit|Bellek: bellek sınırı yüksek|Sayacının|Ortalama|Yapılandırma dosyasından yüksek bellek sınırı.|
|MemoryLimitLow|Bellek: bellek sınırı düşük|Sayacının|Ortalama|Yapılandırma dosyasından düşük bellek sınırı.|
|MemoryLimitVertiPaq|Bellek: bellek sınırı VertiPaq|Sayacının|Ortalama|Yapılandırma dosyasından bellek içi sınır.|
|MemoryUsage|Bellek: bellek kullanımı|Sayacının|Ortalama|Sunucu işleminin temizleyici bellek fiyatını hesaplamada kullanılan bellek kullanımı. Altyapı bellek sınırının fazla olması halinde bellek içi analiz altyapısı (VertiPaq) tarafından eşlenen veya ayrılan belleği yok sayan, bellek eşlemeli verilerin boyutuna eşit.|
|private_bytes_metric|Özel baytlar |Sayacının|Ortalama|Analysis Services altyapısı işleminin ve karma kapsayıcı işlemlerinin ayırdığı, diğer işlemlerle paylaşılan bellek dahil değil toplam bellek miktarı.|
|virtual_bytes_metric|Sanal baytlar |Sayacının|Ortalama|Analysis Services altyapısı işleminin ve mashup kapsayıcı işlemlerinin kullandığı sanal adres alanının geçerli boyutu.|
|mashup_engine_private_bytes_metric|D motoru özel baytlar |Sayacının|Ortalama|Diğer işlemlerle paylaşılan bellek dahil değil, ayrılan bellek karma kapsayıcı işlemlerinin toplam miktarı.|
|mashup_engine_virtual_bytes_metric|D motoru sanal bayt sayısı |Sayacının|Ortalama|Sanal adres alanı karma kapsayıcı işlemlerinin geçerli boyutu kullanıyor.|
|Kota|Bellek: kota|Sayacının|Ortalama|Bayt cinsinden geçerli bellek kotası. Bellek kotası, bellek verme veya bellek ayırma olarak da bilinir.|
|Quotabkilitli|Bellek: kota engellendi|Sayı|Ortalama|Diğer bellek kotaları boşaltılana kadar engellenen kota isteklerinin geçerli sayısı.|
|Vertipaqdisk belleksiz|Bellek: VertiPaq disk belleksiz|Sayacının|Ortalama|Bellek içi altyapı tarafından kullanılmak üzere çalışma kümesinde kilitlenen bellek baytları.|
|Vertipaqdisk|Bellek: VertiPaq disk belleğine|Sayacının|Ortalama|Bellek içi veriler için kullanılan disk belleğine alınan bellek miktarı.|
|ProcessingPoolJobQueueLength|İşleme havuzu Iş kuyruğu uzunluğu|Sayı|Ortalama|İşleme iş parçacığı havuzu kuyruğundaki g/ç dışı iş sayısı.|
|RowsConvertedPerSec|İşleme: dönüştürülen satır sayısı/saniye|Sayaçpersaniye|Ortalama|İşlem sırasında dönüştürülen satır oranı.|
|RowsReadPerSec|İşleme: okunan satır sayısı/saniye|Sayaçpersaniye|Ortalama|Tüm ilişkisel veritabanlarından okunan satır oranı.|
|RowsWrittenPerSec|İşleme: yazılan satır sayısı/saniye|Sayaçpersaniye|Ortalama|İşlem sırasında yazılan satır oranı.|
|qpu_metric|QPU|Sayı|Ortalama|QPU. S1 için 0-100 aralığı, S2 için 0-200 ve S4 için 0-400|
|Querypoolbusyıthreads|Sorgu havuzu meşgul Iş parçacıkları|Sayı|Ortalama|Sorgu iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|
|Başarılı bir bağlantı Spersec|Saniyedeki başarılı bağlantı sayısı|Sayaçpersaniye|Ortalama|Başarılı bağlantı tamamlama oranı.|
|Commandpoolbusyıthreads|İş parçacıkları: komut havuzu meşgul iş parçacıkları|Sayı|Ortalama|Komut iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|
|Commandpokaydlithreads|İş parçacıkları: komut havuzu boşta iş parçacıkları|Sayı|Ortalama|Komut iş parçacığı havuzundaki boşta iş parçacığı sayısı.|
|Longparsingbusyıthreads|İş parçacıkları: uzun ayrıştırma meşgul iş parçacıkları|Sayı|Ortalama|Uzun ayrıştırma iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|
|LongParsingIdleThreads|İş parçacıkları: uzun ayrıştırma boşta iş parçacıkları|Sayı|Ortalama|Uzun ayrıştırma iş parçacığı havuzundaki boşta iş parçacığı sayısı.|
|LongParsingJobQueueLength|İş parçacıkları: uzun ayrıştırma işi sıra uzunluğu|Sayı|Ortalama|Uzun ayrıştırma iş parçacığı havuzu kuyruğundaki iş sayısı.|
|Processingpokayojobqueuelength|İş parçacıkları: Işleme havuzu g/ç iş kuyruğu uzunluğu|Sayı|Ortalama|İşleme iş parçacığı havuzu kuyruğundaki g/ç işlerinin sayısı.|
|Processingpoolbusyıiojobthreads|İş parçacıkları: Işleme havuzu meşgul g/ç işi iş parçacıkları|Sayı|Ortalama|İşleme iş parçacığı havuzunda g/ç işleri çalıştıran iş parçacığı sayısı.|
|Processingpoolbusınonıothreads|İş parçacıkları: Işleme havuzu meşgul olmayan g/ç iş parçacıkları|Sayı|Ortalama|İşleme iş parçacığı havuzunda g/ç olmayan işler çalıştıran iş parçacığı sayısı.|
|Processingpokaydliiojobthreads|İş parçacıkları: Işleme havuzu boşta g/ç işi iş parçacıkları|Sayı|Ortalama|İşleme iş parçacığı havuzundaki g/ç işleri için boşta iş parçacığı sayısı.|
|Processingpokaydlenoniothreads|İş parçacıkları: Işleme havuzu boşta g/ç olmayan iş parçacıkları|Sayı|Ortalama|İşleme iş parçacığı havuzunda g/ç dışı işlere adanmış boşta iş parçacığı sayısı.|
|Querypokaydlithreads|İş parçacıkları: sorgu havuzu boşta iş parçacıkları|Sayı|Ortalama|İşleme iş parçacığı havuzundaki g/ç işleri için boşta iş parçacığı sayısı.|
|QueryPoolJobQueueLength|İş parçacıkları: sorgu havuzu iş kuyruğu uzunluğu|Sayı|Ortalama|Sorgu iş parçacığı havuzu kuyruğundaki iş sayısı.|
|Shortparsingbusyıthreads|İş parçacıkları: kısa ayrıştırma meşgul iş parçacıkları|Sayı|Ortalama|Kısa ayrıştırma iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|
|ShortParsingIdleThreads|İş parçacıkları: kısa ayrıştırma boşta iş parçacıkları|Sayı|Ortalama|Kısa ayrıştırma iş parçacığı havuzundaki boşta iş parçacığı sayısı.|
|ShortParsingJobQueueLength|İş parçacıkları: kısa ayrıştırma iş kuyruğu uzunluğu|Sayı|Ortalama|Kısa ayrıştırma iş parçacığı havuzu sırasındaki iş sayısı.|
|Totalconnectionarızaları|Toplam bağlantı başarısızlığı sayısı|Sayı|Ortalama|Toplam başarısız bağlantı denemesi.|
|TotalConnectionRequests|Toplam bağlantı Isteği sayısı|Sayı|Ortalama|Toplam bağlantı isteği sayısı. |

## <a name="next-steps"></a>Sonraki adımlar
[Azure izlemeye genel bakış](../azure-monitor/overview.md)      
[Azure Ölçüm Gezgini ile çalışmaya](../azure-monitor/platform/metrics-getting-started.md) başlama      
[Azure Izleyici 'de ölçümler REST API](/rest/api/monitor/metrics)
