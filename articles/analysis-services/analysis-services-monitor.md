---
title: Azure Analysis Services Server ölçümlerini izleme | Microsoft Docs
description: Sunucularınızın performansını ve sistem durumunu izlemenize yardımcı olması için portaldaki ücretsiz bir araç olan Azure Ölçüm Gezgini Analysis Services nasıl kullanacağınızı öğrenin.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3991eb421f42ec6645e3321d3a624e226fd12c67
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314261"
---
# <a name="monitor-server-metrics"></a>Sunucu ölçümlerini izleme

Analysis Services, portaldaki ücretsiz bir araç olan Azure Ölçüm Gezgini ve sunucularınızın performansını ve sistem durumunu izlemenize yardımcı olmak için ölçümler sağlar. Örneğin, bellek ve CPU kullanımını, istemci bağlantı sayısını ve sorgu kaynak tüketimini izleyin. Analysis Services, diğer Azure hizmetleriyle aynı izleme çerçevesini kullanır. Daha fazla bilgi için bkz. [Azure Ölçüm Gezgini](../azure-monitor/essentials/metrics-getting-started.md)kullanmaya başlama.

Daha ayrıntılı tanılama gerçekleştirmek, performansı izlemek ve bir kaynak grubunda veya abonelikte birden çok hizmet kaynağı arasındaki eğilimleri belirlemek için [Azure izleyici](../azure-monitor/overview.md)'yi kullanın. Azure Izleyici (hizmet), faturalandırılabilir bir hizmetle sonuçlanabilir.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Analysis Services sunucuya yönelik ölçümleri izlemek için

1. Azure portal, **ölçümler**' i seçin.

    ![Azure portalında izleme](./media/analysis-services-monitor/aas-monitor-portal.png)

2. **Ölçüm** bölümünde, grafiğinizde içerilecek ölçümleri seçin. 

    ![Grafiği izle](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Sunucu ölçümleri

İzleme senaryolarınız için hangi ölçümlerin en iyi olduğunu anlamak için bu tabloyu kullanın. Aynı grafikte yalnızca aynı birimin ölçümleri görüntülenebilir.

|Metric|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Komut havuzu Iş kuyruğu uzunluğu|Count|Ortalama|Komut iş parçacığı havuzu sırasındaki iş sayısı.|
|CurrentConnections|Bağlantı: geçerli bağlantılar|Count|Ortalama|Kurulan istemci bağlantılarının geçerli sayısı.|
|CurrentUserSessions|Geçerli Kullanıcı oturumları|Count|Ortalama|Geçerli kullanıcı oturumlarının sayısı.|
|mashup_engine_memory_metric|D motoru belleği|Bayt|Ortalama|Karma altyapı işlemlerine göre bellek kullanımı|
|mashup_engine_qpu_metric|A motoru QPU|Count|Ortalama|Karma altyapı işlemlerine göre QPU kullanımı|
|memory_metric|Bellek|Bayt|Ortalama|Bellek. S1 için 0-25 GB, S2 için 0-50 GB ve S4 için 0-100 GB|
|memory_thrashing_metric|Bellek Temizleme|Yüzde|Ortalama|Ortalama bellek miktarı.|
|CleanerCurrentPrice|Bellek: temizleyici geçerli fiyatı|Count|Ortalama|Geçerli bellek fiyatı, $/Byte/Time, 1000 olarak normalleştirilemez.|
|Cleanermemorynonınkable|Bellek: temizleyici bellek daraltılamaz|Bayt|Ortalama|Arka plan temizleyici tarafından temizlenme konusu değil, bayt cinsinden bellek miktarı.|
|Cleanermemoryshrınkable|Bellek: temizleyici bellek shrınılabilir|Bayt|Ortalama|Arka plan temizleyici tarafından temizlenme konusu bayt cinsinden bellek miktarı.|
|MemoryLimitHard|Bellek: bellek sınırı sabit|Bayt|Ortalama|Yapılandırma dosyasından sabit bellek sınırı.|
|Memoryhighlimit|Bellek: bellek sınırı yüksek|Bayt|Ortalama|Yapılandırma dosyasından yüksek bellek sınırı.|
|MemoryLimitLow|Bellek: bellek sınırı düşük|Bayt|Ortalama|Yapılandırma dosyasından düşük bellek sınırı.|
|MemoryLimitVertiPaq|Bellek: bellek sınırı VertiPaq|Bayt|Ortalama|Yapılandırma dosyasından bellek içi sınır.|
|MemoryUsage|Bellek: bellek kullanımı|Bayt|Ortalama|Sunucu işleminin temizleyici bellek fiyatını hesaplamada kullanılan bellek kullanımı. Altyapı bellek sınırının fazla olması halinde bellek içi analiz altyapısı (VertiPaq) tarafından eşlenen veya ayrılan belleği yok sayan, bellek eşlemeli verilerin boyutuna eşit.|
|private_bytes_metric|Özel baytlar |Bayt|Ortalama|Analysis Services altyapısı işleminin ve karma kapsayıcı işlemlerinin ayırdığı, diğer işlemlerle paylaşılan bellek dahil değil toplam bellek miktarı.|
|virtual_bytes_metric|Sanal Bayt Sayısı |Bayt|Ortalama|Analysis Services altyapısı işleminin ve mashup kapsayıcı işlemlerinin kullandığı sanal adres alanının geçerli boyutu.|
|mashup_engine_private_bytes_metric|D motoru özel baytlar |Bayt|Ortalama|Diğer işlemlerle paylaşılan bellek dahil değil, ayrılan bellek karma kapsayıcı işlemlerinin toplam miktarı.|
|mashup_engine_virtual_bytes_metric|D motoru sanal bayt sayısı |Bayt|Ortalama|Sanal adres alanı karma kapsayıcı işlemlerinin geçerli boyutu kullanıyor.|
|Kota|Bellek: kota|Bayt|Ortalama|Bayt cinsinden geçerli bellek kotası. Bellek kotası, bellek verme veya bellek ayırma olarak da bilinir.|
|Quotabkilitli|Bellek: kota engellendi|Count|Ortalama|Diğer bellek kotaları boşaltılana kadar engellenen kota isteklerinin geçerli sayısı.|
|Vertipaqdisk belleksiz|Bellek: VertiPaq disk belleksiz|Bayt|Ortalama|Bellek içi altyapı tarafından kullanılmak üzere çalışma kümesinde kilitlenen bellek baytları.|
|Vertipaqdisk|Bellek: VertiPaq disk belleğine|Bayt|Ortalama|Bellek içi veriler için kullanılan disk belleğine alınan bellek miktarı.|
|ProcessingPoolJobQueueLength|İşleme havuzu Iş kuyruğu uzunluğu|Count|Ortalama|İşleme iş parçacığı havuzu kuyruğundaki g/ç dışı iş sayısı.|
|RowsConvertedPerSec|İşleme: dönüştürülen satır sayısı/saniye|Sayaçpersaniye|Ortalama|İşlem sırasında dönüştürülen satır oranı.|
|RowsReadPerSec|İşleme: okunan satır sayısı/saniye|Sayaçpersaniye|Ortalama|Tüm ilişkisel veritabanlarından okunan satır oranı.|
|RowsWrittenPerSec|İşleme: yazılan satır sayısı/saniye|Sayaçpersaniye|Ortalama|İşlem sırasında yazılan satır oranı.|
|qpu_metric|QPU|Count|Ortalama|QPU. S1 için 0-100 aralığı, S2 için 0-200 ve S4 için 0-400|
|Querypoolbusyıthreads|Sorgu havuzu meşgul Iş parçacıkları|Count|Ortalama|Sorgu iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|
|Başarılı bir bağlantı Spersec|Saniyedeki başarılı bağlantı sayısı|Sayaçpersaniye|Ortalama|Başarılı bağlantı tamamlama oranı.|
|Commandpoolbusyıthreads|İş parçacıkları: komut havuzu meşgul iş parçacıkları|Count|Ortalama|Komut iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|
|Commandpokaydlithreads|İş parçacıkları: komut havuzu boşta iş parçacıkları|Count|Ortalama|Komut iş parçacığı havuzundaki boşta iş parçacığı sayısı.|
|Longparsingbusyıthreads|İş parçacıkları: uzun ayrıştırma meşgul iş parçacıkları|Count|Ortalama|Uzun ayrıştırma iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|
|LongParsingIdleThreads|İş parçacıkları: uzun ayrıştırma boşta iş parçacıkları|Count|Ortalama|Uzun ayrıştırma iş parçacığı havuzundaki boşta iş parçacığı sayısı.|
|LongParsingJobQueueLength|İş parçacıkları: uzun ayrıştırma işi sıra uzunluğu|Count|Ortalama|Uzun ayrıştırma iş parçacığı havuzu kuyruğundaki iş sayısı.|
|Processingpokayojobqueuelength|İş parçacıkları: Işleme havuzu g/ç iş kuyruğu uzunluğu|Count|Ortalama|İşleme iş parçacığı havuzu kuyruğundaki g/ç işlerinin sayısı.|
|Processingpoolbusyıiojobthreads|İş parçacıkları: Işleme havuzu meşgul g/ç işi iş parçacıkları|Count|Ortalama|İşleme iş parçacığı havuzunda g/ç işleri çalıştıran iş parçacığı sayısı.|
|Processingpoolbusınonıothreads|İş parçacıkları: Işleme havuzu meşgul olmayan g/ç iş parçacıkları|Count|Ortalama|İşleme iş parçacığı havuzunda g/ç olmayan işler çalıştıran iş parçacığı sayısı.|
|Processingpokaydliiojobthreads|İş parçacıkları: Işleme havuzu boşta g/ç işi iş parçacıkları|Count|Ortalama|İşleme iş parçacığı havuzundaki g/ç işleri için boşta iş parçacığı sayısı.|
|Processingpokaydlenoniothreads|İş parçacıkları: Işleme havuzu boşta g/ç olmayan iş parçacıkları|Count|Ortalama|İşleme iş parçacığı havuzunda g/ç dışı işlere adanmış boşta iş parçacığı sayısı.|
|Querypokaydlithreads|İş parçacıkları: sorgu havuzu boşta iş parçacıkları|Count|Ortalama|İşleme iş parçacığı havuzundaki g/ç işleri için boşta iş parçacığı sayısı.|
|QueryPoolJobQueueLength|İş parçacıkları: sorgu havuzu iş kuyruğu uzunluğu|Count|Ortalama|Sorgu iş parçacığı havuzu kuyruğundaki iş sayısı.|
|Shortparsingbusyıthreads|İş parçacıkları: kısa ayrıştırma meşgul iş parçacıkları|Count|Ortalama|Kısa ayrıştırma iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|
|ShortParsingIdleThreads|İş parçacıkları: kısa ayrıştırma boşta iş parçacıkları|Count|Ortalama|Kısa ayrıştırma iş parçacığı havuzundaki boşta iş parçacığı sayısı.|
|ShortParsingJobQueueLength|İş parçacıkları: kısa ayrıştırma iş kuyruğu uzunluğu|Count|Ortalama|Kısa ayrıştırma iş parçacığı havuzu sırasındaki iş sayısı.|
|Totalconnectionarızaları|Toplam bağlantı başarısızlığı sayısı|Count|Ortalama|Toplam başarısız bağlantı denemesi.|
|TotalConnectionRequests|Toplam bağlantı Isteği sayısı|Count|Ortalama|Toplam bağlantı isteği sayısı. |

## <a name="next-steps"></a>Sonraki adımlar
[Azure Izleyicisine genel bakış](../azure-monitor/overview.md)      
[Azure Ölçüm Gezgini kullanmaya başlama](../azure-monitor/essentials/metrics-getting-started.md)      
[Azure Izleyici 'de ölçümler REST API](/rest/api/monitor/metrics)
