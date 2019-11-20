---
title: Azure Izleyici kaynak türüne göre desteklenen ölçümler
description: Azure Izleyici ile her kaynak türü için kullanılabilen ölçümlerin listesi.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 58c0db1f8200a757c95d7f66f9d821968e7e1839
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121113"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Izleyici ile desteklenen ölçümler

Azure Izleyici, ölçümlerle etkileşimde bulunmak için, portalda grafik oluşturma, REST API aracılığıyla erişme veya PowerShell veya CLı kullanarak sorgulama gibi çeşitli yollar sağlar. Aşağıda, Azure Izleyici 'nin ölçüm işlem hattı ile Şu anda kullanılabilir olan tüm ölçümlerin tamamı listelenmiştir. Diğer ölçümler portalda veya eski API 'Ler kullanılarak kullanılabilir. Aşağıdaki listede yalnızca birleştirilmiş Azure Izleyici ölçüm işlem hattı kullanılarak kullanılabilen ölçümler bulunur. Bu ölçümleri sorgulamak ve bunlara erişmek için lütfen [2018-01-01 API-sürümünü](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) kullanın

> [!NOTE]
> Çok boyutlu ölçümlerin tanılama ayarları aracılığıyla gönderilmesi şu anda desteklenmemektedir. Boyutlu ölçümler, boyut değerlerinin toplamı alınarak düzleştirilmiş tek yönlü ölçümler olarak dışarı aktarılır.
>
> *Örneğin*: Bir Olay Hub'ındaki 'Gelen İletiler' ölçümü, kuyruk düzeyi temelinde araştırılıp grafiği oluşturulabilir. Ancak, tanılama ayarları aracılığıyla dışarı aktarılan ölçüm, Olay Hub’ındaki tüm kuyruklarda tüm gelen iletiler halinde ifade edilir.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/sunucuları

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|qpu_metric|QPU|Sayı|Ortalama|QPU. S1 için 0-100 aralığı, S2 için 0-200 ve S4 için 0-400|ServerResourceType|
|memory_metric|Bellek|Bayt|Ortalama|Bellek. S1 için 0-25 GB, S2 için 0-50 GB ve S4 için 0-100 GB|ServerResourceType|
|private_bytes_metric|Özel baytlar |Bayt|Ortalama|Analysis Services altyapısı işleminin ve karma kapsayıcı işlemlerinin ayırdığı, diğer işlemlerle paylaşılan bellek dahil değil toplam bellek miktarı.|ServerResourceType|
|virtual_bytes_metric|Sanal baytlar |Bayt|Ortalama|Analysis Services altyapısı işleminin ve mashup kapsayıcı işlemlerinin kullandığı sanal adres alanının geçerli boyutu.|ServerResourceType|
|TotalConnectionRequests|Toplam bağlantı Isteği sayısı|Sayı|Ortalama|Toplam bağlantı isteği sayısı. Bunlar, varışlardır.|ServerResourceType|
|Başarılı bir bağlantı Spersec|Saniyedeki başarılı bağlantı sayısı|Sayaçpersaniye|Ortalama|Başarılı bağlantı tamamlama oranı.|ServerResourceType|
|Totalconnectionarızaları|Toplam bağlantı başarısızlığı sayısı|Sayı|Ortalama|Toplam başarısız bağlantı denemesi.|ServerResourceType|
|CurrentUserSessions|Geçerli Kullanıcı oturumları|Sayı|Ortalama|Geçerli kullanıcı oturumlarının sayısı.|ServerResourceType|
|Querypoolbusyıthreads|Sorgu havuzu meşgul Iş parçacıkları|Sayı|Ortalama|Sorgu iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|ServerResourceType|
|CommandPoolJobQueueLength|Komut havuzu Iş kuyruğu uzunluğu|Sayı|Ortalama|Komut iş parçacığı havuzu sırasındaki iş sayısı.|ServerResourceType|
|ProcessingPoolJobQueueLength|İşleme havuzu Iş kuyruğu uzunluğu|Sayı|Ortalama|İşleme iş parçacığı havuzu kuyruğundaki g/ç dışı iş sayısı.|ServerResourceType|
|CurrentConnections|Bağlantı: geçerli bağlantılar|Sayı|Ortalama|Kurulan istemci bağlantılarının geçerli sayısı.|ServerResourceType|
|CleanerCurrentPrice|Bellek: temizleyici geçerli fiyatı|Sayı|Ortalama|Geçerli bellek fiyatı, $/Byte/Time, 1000 olarak normalleştirilemez.|ServerResourceType|
|Cleanermemoryshrınkable|Bellek: temizleyici bellek shrınılabilir|Bayt|Ortalama|Arka plan temizleyici tarafından temizlenme konusu bayt cinsinden bellek miktarı.|ServerResourceType|
|Cleanermemorynonınkable|Bellek: temizleyici bellek daraltılamaz|Bayt|Ortalama|Arka plan temizleyici tarafından temizlenme konusu değil, bayt cinsinden bellek miktarı.|ServerResourceType|
|MemoryUsage|Bellek: bellek kullanımı|Bayt|Ortalama|Sunucu işleminin temizleyici bellek fiyatını hesaplamada kullanılan bellek kullanımı. XVelocity altyapısının bellek sınırının fazla olması halinde, xVelocity bellek içi analiz altyapısı (VertiPaq) tarafından eşlenen veya ayrılan belleği yoksayarak, işlem \ bayt ve bellek eşlemeli verilerin boyutuna eşit.|ServerResourceType|
|MemoryLimitHard|Bellek: bellek sınırı sabit|Bayt|Ortalama|Yapılandırma dosyasından sabit bellek sınırı.|ServerResourceType|
|Memoryhighlimit|Bellek: bellek sınırı yüksek|Bayt|Ortalama|Yapılandırma dosyasından yüksek bellek sınırı.|ServerResourceType|
|MemoryLimitLow|Bellek: bellek sınırı düşük|Bayt|Ortalama|Yapılandırma dosyasından düşük bellek sınırı.|ServerResourceType|
|MemoryLimitVertiPaq|Bellek: bellek sınırı VertiPaq|Bayt|Ortalama|Yapılandırma dosyasından bellek içi sınır.|ServerResourceType|
|Kota|Bellek: kota|Bayt|Ortalama|Bayt cinsinden geçerli bellek kotası. Bellek kotası, bellek verme veya bellek ayırma olarak da bilinir.|ServerResourceType|
|Quotabkilitli|Bellek: kota engellendi|Sayı|Ortalama|Diğer bellek kotaları boşaltılana kadar engellenen kota isteklerinin geçerli sayısı.|ServerResourceType|
|Vertipaqdisk belleksiz|Bellek: VertiPaq disk belleksiz|Bayt|Ortalama|Bellek içi altyapı tarafından kullanılmak üzere çalışma kümesinde kilitlenen bellek baytları.|ServerResourceType|
|Vertipaqdisk|Bellek: VertiPaq disk belleğine|Bayt|Ortalama|Bellek içi veriler için kullanılan disk belleğine alınan bellek miktarı.|ServerResourceType|
|RowsReadPerSec|İşleme: okunan satır sayısı/saniye|Sayaçpersaniye|Ortalama|Tüm ilişkisel veritabanlarından okunan satır oranı.|ServerResourceType|
|RowsConvertedPerSec|İşleme: dönüştürülen satır sayısı/saniye|Sayaçpersaniye|Ortalama|İşlem sırasında dönüştürülen satır oranı.|ServerResourceType|
|RowsWrittenPerSec|İşleme: yazılan satır sayısı/saniye|Sayaçpersaniye|Ortalama|İşlem sırasında yazılan satır oranı.|ServerResourceType|
|Commandpoolbusyıthreads|İş parçacıkları: komut havuzu meşgul iş parçacıkları|Sayı|Ortalama|Komut iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|ServerResourceType|
|CommandPoolIdleThreads|İş parçacıkları: komut havuzu boşta iş parçacıkları|Sayı|Ortalama|Komut iş parçacığı havuzundaki boşta iş parçacığı sayısı.|ServerResourceType|
|Longparsingbusyıthreads|İş parçacıkları: uzun ayrıştırma meşgul iş parçacıkları|Sayı|Ortalama|Uzun ayrıştırma iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|ServerResourceType|
|LongParsingIdleThreads|İş parçacıkları: uzun ayrıştırma boşta iş parçacıkları|Sayı|Ortalama|Uzun ayrıştırma iş parçacığı havuzundaki boşta iş parçacığı sayısı.|ServerResourceType|
|LongParsingJobQueueLength|İş parçacıkları: uzun ayrıştırma işi sıra uzunluğu|Sayı|Ortalama|Uzun ayrıştırma iş parçacığı havuzu kuyruğundaki iş sayısı.|ServerResourceType|
|Processingpoolbusyıiojobthreads|İş parçacıkları: Işleme havuzu meşgul g/ç işi iş parçacıkları|Sayı|Ortalama|İşleme iş parçacığı havuzunda g/ç işleri çalıştıran iş parçacığı sayısı.|ServerResourceType|
|Processingpoolbusınonıothreads|İş parçacıkları: Işleme havuzu meşgul olmayan g/ç iş parçacıkları|Sayı|Ortalama|İşleme iş parçacığı havuzunda g/ç olmayan işler çalıştıran iş parçacığı sayısı.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|İş parçacıkları: Işleme havuzu g/ç iş kuyruğu uzunluğu|Sayı|Ortalama|İşleme iş parçacığı havuzu kuyruğundaki g/ç işlerinin sayısı.|ServerResourceType|
|Processingpokaydliiojobthreads|İş parçacıkları: Işleme havuzu boşta g/ç işi iş parçacıkları|Sayı|Ortalama|İşleme iş parçacığı havuzundaki g/ç işleri için boşta iş parçacığı sayısı.|ServerResourceType|
|Processingpokaydlenoniothreads|İş parçacıkları: Işleme havuzu boşta g/ç olmayan iş parçacıkları|Sayı|Ortalama|İşleme iş parçacığı havuzunda g/ç dışı işlere adanmış boşta iş parçacığı sayısı.|ServerResourceType|
|QueryPoolIdleThreads|İş parçacıkları: sorgu havuzu boşta iş parçacıkları|Sayı|Ortalama|İşleme iş parçacığı havuzundaki g/ç işleri için boşta iş parçacığı sayısı.|ServerResourceType|
|QueryPoolJobQueueLength|İş parçacıkları: sorgu havuzu iş kuyruğu uzunluğu|Sayı|Ortalama|Sorgu iş parçacığı havuzu kuyruğundaki iş sayısı.|ServerResourceType|
|Shortparsingbusyıthreads|İş parçacıkları: kısa ayrıştırma meşgul iş parçacıkları|Sayı|Ortalama|Kısa ayrıştırma iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|ServerResourceType|
|ShortParsingIdleThreads|İş parçacıkları: kısa ayrıştırma boşta iş parçacıkları|Sayı|Ortalama|Kısa ayrıştırma iş parçacığı havuzundaki boşta iş parçacığı sayısı.|ServerResourceType|
|ShortParsingJobQueueLength|İş parçacıkları: kısa ayrıştırma iş kuyruğu uzunluğu|Sayı|Ortalama|Kısa ayrıştırma iş parçacığı havuzu sırasındaki iş sayısı.|ServerResourceType|
|memory_thrashing_metric|Bellek dağılması|Yüzde|Ortalama|Ortalama bellek miktarı.|ServerResourceType|
|mashup_engine_qpu_metric|A motoru QPU|Sayı|Ortalama|Karma altyapı işlemlerine göre QPU kullanımı|ServerResourceType|
|mashup_engine_memory_metric|D motoru belleği|Bayt|Ortalama|Karma altyapı işlemlerine göre bellek kullanımı|ServerResourceType|
|mashup_engine_private_bytes_metric|D motoru özel baytlar |Bayt|Ortalama|Diğer işlemlerle paylaşılan bellek dahil değil, ayrılan bellek karma kapsayıcı işlemlerinin toplam miktarı.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|D motoru sanal bayt sayısı |Bayt|Ortalama|Sanal adres alanı karma kapsayıcı işlemlerinin geçerli boyutu kullanıyor.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|İstekler|İstekler|Sayı|Toplam|Belirli bir dönemdeki ağ geçidi isteklerinin toplam sayısı. Sorunları tanılamanıza yardımcı olması için çeşitli boyutlara göre dilimlenebilir. |Konum, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|Toplam Istek sayısı|Toplam ağ geçidi Isteği sayısı|Sayı|Toplam|Belirli bir dönemdeki ağ geçidi isteklerinin toplam sayısı. Bu ölçüm kullanım dışı bırakılmıştır, yeni `Requests` ölçüsünü kullanmanızı öneririz. |Konum, ana bilgisayar adı|
|SuccessfulRequests|Başarılı ağ geçidi Istekleri|Sayı|Toplam|Belirli bir dönemdeki başarılı ağ geçidi isteklerinin toplam sayısı. Bu ölçüm kullanım dışı bırakılmıştır, yeni `Requests` ölçüsünü kullanmanızı öneririz.|Konum, ana bilgisayar adı|
|UnauthorizedRequests|Yetkisiz ağ geçidi Istekleri|Sayı|Toplam| Belirli bir dönemdeki yetkisiz ağ geçidi isteklerinin toplam sayısı. Bu ölçüm kullanım dışı bırakılmıştır, yeni `Requests` ölçüsünü kullanmanızı öneririz.|Konum, ana bilgisayar adı|
|FailedRequests|Başarısız ağ geçidi Istekleri|Sayı|Toplam|Belirli bir dönemdeki başarısız ağ geçidi isteklerinin toplam sayısı. Bu ölçüm kullanım dışı bırakılmıştır, yeni `Requests` ölçüsünü kullanmanızı öneririz.|Konum, ana bilgisayar adı|
|Diğer Istekler|Diğer ağ geçidi Istekleri|Sayı|Toplam|Belirli bir dönemdeki, başarılı, yetkisiz veya başarısız kategorilere ayrılan ağ geçidi isteklerinin toplam sayısı. Bu ölçüm kullanım dışı bırakılmıştır, yeni `Requests` ölçüsünü kullanmanızı öneririz. |Konum, ana bilgisayar adı|
|Süre|Ağ Geçidi Isteklerinin genel süresi|Milisaniye|Ortalama|API Management bir istemciden bir istek aldığında ve istemciye yanıt döndürdüğünde geçen süre.|Konum, ana bilgisayar adı|
|Kapasite|Kapasite|Yüzde|Ortalama|Örneği daha fazla yüklemeye uyum sağlayacak şekilde ölçeklendirip ölçeklendirmeyeceğine dair bilinçli kararlar vermek için bir API Management örneğindeki yükün göstergesi.|Konum|
|EventHubTotalEvents|Toplam EventHub olayları|Sayı|Toplam|Belirli bir dönemdeki API Management EventHub 'e gönderilen toplam olay sayısıdır.|Konum|
|Eventhubbaşarılı Futavents|Başarılı EventHub olayları|Sayı|Toplam|Belirli bir dönemdeki başarılı EventHub olaylarının toplam sayısı.|Konum|
|EventHubTotalFailedEvents|Başarısız EventHub olayları|Sayı|Toplam|Belirli bir dönemdeki başarısız olan EventHub olaylarının toplam sayısı.|Konum|
|EventHubRejectedEvents|Reddedilen EventHub olayları|Sayı|Toplam|Belirli bir dönemde reddedilen EventHub olaylarının toplam sayısı (yanlış yapılandırma veya yetkisiz).|Konum|
|EventHubThrottledEvents|Kısıtlanmış EventHub olayları|Sayı|Toplam|Belirli bir dönemdeki kısıtlanmış EventHub olaylarının toplam sayısı.|Konum|
|EventHubTimedoutEvents|Zaman aşımına uğrayan EventHub olayları|Sayı|Toplam|Belirli bir dönemdeki zaman aşımına uğrayan toplam EventHub olay sayısı.|Konum|
|EventHubDroppedEvents|Bırakılan EventHub olayları|Sayı|Toplam|Belirli bir dönemde sıra boyutu sınırına ulaşıldığından atlanan toplam olay sayısı.|Konum|
|EventHubTotalBytesSent|EventHub olaylarının boyutu|Bayt|Toplam|Belirli bir dönemdeki toplam EventHub olay boyutu (bayt).|Konum|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|TotalJob|Toplam Iş sayısı|Sayı|Toplam|Toplam iş sayısı|Runbook, durum|
|TotalUpdateDeploymentRuns|Toplam güncelleştirme dağıtımı çalıştırmaları|Sayı|Toplam|Toplam yazılım güncelleştirme dağıtımı çalıştırmaları|SoftwareUpdateConfigurationName, durum|
|Totalupdatedeploymentmachinerçalıştırır|Toplam güncelleştirme dağıtım makinesi çalıştırması|Sayı|Toplam|Yazılım güncelleştirme dağıtımı çalıştırmasında toplam yazılım güncelleştirme dağıtımı makinesi|SoftwareUpdateConfigurationName, Status, TargetComputer, Softwareupdateconfigurationrunıd|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|CoreCount|Adanmış çekirdek sayısı|Sayı|Toplam|Batch hesabındaki toplam adanmış çekirdek sayısı|Boyut yok|
|TotalNodeCount|Adanmış düğüm sayısı|Sayı|Toplam|Batch hesabındaki toplam ayrılmış düğüm sayısı|Boyut yok|
|LowPriorityCoreCount|LowPriority çekirdek sayısı|Sayı|Toplam|Batch hesabındaki toplam düşük öncelikli çekirdek sayısı|Boyut yok|
|TotalLowPriorityNodeCount|Düşük öncelikli düğüm sayısı|Sayı|Toplam|Batch hesabındaki toplam düşük öncelikli düğüm sayısı|Boyut yok|
|CreatingNodeCount|Düğüm sayısı oluşturuluyor|Sayı|Toplam|Oluşturulmakta olan düğüm sayısı|Boyut yok|
|StartingNodeCount|Başlangıç düğüm sayısı|Sayı|Toplam|Başlayan düğüm sayısı|Boyut yok|
|WaitingForStartTaskNodeCount|Başlangıç görevi düğüm sayısı bekleniyor|Sayı|Toplam|Başlangıç görevinin tamamlanması için bekleyen düğüm sayısı|Boyut yok|
|StartTaskFailedNodeCount|Başlangıç görevi başarısız düğüm sayısı|Sayı|Toplam|Başlangıç görevinin başarısız olduğu düğüm sayısı|Boyut yok|
|IdleNodeCount|Boştaki düğüm sayısı|Sayı|Toplam|Boştaki düğüm sayısı|Boyut yok|
|OfflineNodeCount|Çevrimdışı düğüm sayısı|Sayı|Toplam|Çevrimdışı düğüm sayısı|Boyut yok|
|RebootingNodeCount|Düğüm sayısı yeniden başlatılıyor|Sayı|Toplam|Yeniden başlatma düğümlerinin sayısı|Boyut yok|
|ReimagingNodeCount|Yeniden Imaging düğüm sayısı|Sayı|Toplam|Yeniden görüntüleme düğümlerinin sayısı|Boyut yok|
|RunningNodeCount|Çalışan düğüm sayısı|Sayı|Toplam|Çalışan düğümlerin sayısı|Boyut yok|
|LeavingPoolNodeCount|Havuz düğüm sayısından çıkılıyor|Sayı|Toplam|Havuzdan ayrılma düğüm sayısı|Boyut yok|
|UnusableNodeCount|Kullanılamayan düğüm sayısı|Sayı|Toplam|Kullanılamayan düğüm sayısı|Boyut yok|
|PreemptedNodeCount|Önden düğüm sayısı|Sayı|Toplam|Yok eden düğüm sayısı|Boyut yok|
|TaskStartEvent|Görev başlatma olayları|Sayı|Toplam|Başlatılan toplam görev sayısı|Boyut yok|
|TaskCompleteEvent|Görev tamamlanma olayları|Sayı|Toplam|Tamamlanan toplam görev sayısı|Boyut yok|
|TaskFailEvent|Görev başarısız olayları|Sayı|Toplam|Hatalı durumda tamamlanan toplam görev sayısı|Boyut yok|
|PoolCreateEvent|Havuz oluşturma olayları|Sayı|Toplam|Oluşturulan Toplam Havuz sayısı|Boyut yok|
|PoolResizeStartEvent|Havuz yeniden boyutlandırma başlangıç olayları|Sayı|Toplam|Başlatılan toplam havuz yeniden boyutlandırmaları sayısı|Boyut yok|
|PoolResizeCompleteEvent|Havuz yeniden boyutlandırma Tamam olayları|Sayı|Toplam|Tamamlanan toplam havuz yeniden boyutlandırmaları sayısı|Boyut yok|
|PoolDeleteStartEvent|Havuz silme başlangıç olayları|Sayı|Toplam|Başlatılmış olan toplam havuz silme sayısı|Boyut yok|
|PoolDeleteCompleteEvent|Havuz silme Tamam olayları|Sayı|Toplam|Tamamlanan toplam havuz silme sayısı|Boyut yok|
|JobDeleteCompleteEvent|İş silme Tamam olayları|Sayı|Toplam|Başarıyla silinen işlerin toplam sayısı.|Boyut yok|
|JobDeleteStartEvent|İş silme başlangıç olayları|Sayı|Toplam|Silinmesi istenen toplam iş sayısı.|Boyut yok|
|JobDisableCompleteEvent|İş tüm olayları devre dışı bırak|Sayı|Toplam|Başarıyla devre dışı bırakılmış toplam iş sayısı.|Boyut yok|
|JobDisableStartEvent|İş devre dışı başlatma olayları|Sayı|Toplam|Devre dışı bırakılması istenen toplam iş sayısı.|Boyut yok|
|JobStartEvent|İş başlatma olayları|Sayı|Toplam|Başarıyla başlatılmış toplam iş sayısı.|Boyut yok|
|JobTerminateCompleteEvent|İş sonlandırma tamamlanma olayları|Sayı|Toplam|Başarıyla sonlandırılan toplam iş sayısı.|Boyut yok|
|JobTerminateStartEvent|İş sonlandırma başlangıç olayları|Sayı|Toplam|Sonlandırılması istenen toplam iş sayısı.|Boyut yok|

## <a name="microsoftcacheredis"></a>Microsoft. Cache/redsıs

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|connectedistemcileri|Bağlı Istemciler|Sayı|Maksimum||Parça|
|totalcommandsişlendi|Toplam Işlem sayısı|Sayı|Toplam||Parça|
|cachehits|Önbellek ısabetleri|Sayı|Toplam||Parça|
|cacheisabetsizlik|Önbellek Isabetsizliği|Sayı|Toplam||Parça|
|GetCommands|İyorsa|Sayı|Toplam||Parça|
|SetCommands|Yapar|Sayı|Toplam||Parça|
|operationsPerSecond|İşlem/saniye|Sayı|Maksimum||Parça|
|çıkarılan anahtarlar|Çıkarılan anahtarlar|Sayı|Toplam||Parça|
|totalkeys|Toplam anahtar sayısı|Sayı|Maksimum||Parça|
|expiredkeys|Süre dolma anahtarları|Sayı|Toplam||Parça|
|usedmemory|Kullanılan bellek|Bayt|Maksimum||Parça|
|usedmemorypercentage|Kullanılan bellek yüzdesi|Yüzde|Maksimum||Parça|
|usedmemoryRss|Kullanılan bellek RSS|Bayt|Maksimum||Parça|
|Sunucuyükü|Sunucu yükü|Yüzde|Maksimum||Parça|
|cacheWrite|Önbellek yazma|BytesPerSecond|Maksimum||Parça|
|cacheRead|Önbellek Okuma|BytesPerSecond|Maksimum||Parça|
|percentProcessorTime|CPU|Yüzde|Maksimum||Parça|
|cacheLatency|Önbellek gecikmesi mikrosaniye (Önizleme)|Sayı|Ortalama||Shardıd, SampleType|
|hatalar|Hatalar|Sayı|Maksimum||Shardıd, ErrorType|
|connectedclients0|Bağlı Istemciler (parça 0)|Sayı|Maksimum||Boyut yok|
|totalcommandsprocessed0|Toplam Işlem (parça 0)|Sayı|Toplam||Boyut yok|
|cachehits0|Önbellek ısabetleri (parça 0)|Sayı|Toplam||Boyut yok|
|cachemisses0|Önbellekte bulunamayanlar (parça 0)|Sayı|Toplam||Boyut yok|
|getcommands0|Alır (parça 0)|Sayı|Toplam||Boyut yok|
|setcommands0|Kümeler (parça 0)|Sayı|Toplam||Boyut yok|
|operationsPerSecond0|Saniyedeki işlem (parça 0)|Sayı|Maksimum||Boyut yok|
|evictedkeys0|Çıkarılan anahtarlar (parça 0)|Sayı|Toplam||Boyut yok|
|totalkeys0|Toplam anahtar (parça 0)|Sayı|Maksimum||Boyut yok|
|expiredkeys0|Süre dolma anahtarları (parça 0)|Sayı|Toplam||Boyut yok|
|usedmemory0|Kullanılan bellek (parça 0)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss0|Kullanılan bellek RSS (parça 0)|Bayt|Maksimum||Boyut yok|
|serverLoad0|Sunucu yükü (parça 0)|Yüzde|Maksimum||Boyut yok|
|cacheWrite0|Önbellek yazma (parça 0)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead0|Önbellek Okuma (parça 0)|BytesPerSecond|Maksimum||Boyut yok|
|percentProcessorTime0|CPU (parça 0)|Yüzde|Maksimum||Boyut yok|
|connectedclients1|Bağlı Istemciler (parça 1)|Sayı|Maksimum||Boyut yok|
|totalcommandsprocessed1|Toplam Işlem (parça 1)|Sayı|Toplam||Boyut yok|
|cachehits1|Önbellek ısabetleri (parça 1)|Sayı|Toplam||Boyut yok|
|cachemisses1|Önbellekte bulunamayanlar (parça 1)|Sayı|Toplam||Boyut yok|
|getcommands1|Alır (parça 1)|Sayı|Toplam||Boyut yok|
|setcommands1|Kümeler (parça 1)|Sayı|Toplam||Boyut yok|
|operationsPerSecond1|Saniyedeki işlem (parça 1)|Sayı|Maksimum||Boyut yok|
|evictedkeys1|Çıkarılan anahtarlar (parça 1)|Sayı|Toplam||Boyut yok|
|totalkeys1|Toplam anahtar (parça 1)|Sayı|Maksimum||Boyut yok|
|expiredkeys1|Süre dolma anahtarları (parça 1)|Sayı|Toplam||Boyut yok|
|usedmemory1|Kullanılan bellek (parça 1)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss1|Kullanılan bellek RSS (parça 1)|Bayt|Maksimum||Boyut yok|
|serverLoad1|Sunucu yükü (parça 1)|Yüzde|Maksimum||Boyut yok|
|cacheWrite1|Önbellek yazma (parça 1)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead1|Önbellek Okuma (parça 1)|BytesPerSecond|Maksimum||Boyut yok|
|percentProcessorTime1|CPU (parça 1)|Yüzde|Maksimum||Boyut yok|
|connectedclients2|Bağlı Istemciler (parça 2)|Sayı|Maksimum||Boyut yok|
|totalcommandsprocessed2|Toplam Işlem (parça 2)|Sayı|Toplam||Boyut yok|
|cachehits2|Önbellek ısabetleri (parça 2)|Sayı|Toplam||Boyut yok|
|cachemisses2|Önbellekte bulunamayanlar (parça 2)|Sayı|Toplam||Boyut yok|
|getcommands2|Alır (parça 2)|Sayı|Toplam||Boyut yok|
|setcommands2|Kümeler (parça 2)|Sayı|Toplam||Boyut yok|
|operationsPerSecond2|Saniyedeki işlem (parça 2)|Sayı|Maksimum||Boyut yok|
|evictedkeys2|Çıkarılan anahtarlar (parça 2)|Sayı|Toplam||Boyut yok|
|totalkeys2|Toplam anahtar (parça 2)|Sayı|Maksimum||Boyut yok|
|expiredkeys2|Süre dolma anahtarları (parça 2)|Sayı|Toplam||Boyut yok|
|usedmemory2|Kullanılan bellek (parça 2)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss2|Kullanılan bellek RSS (parça 2)|Bayt|Maksimum||Boyut yok|
|serverLoad2|Sunucu yükü (parça 2)|Yüzde|Maksimum||Boyut yok|
|cacheWrite2|Önbellek yazma (parça 2)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead2|Önbellek Okuma (parça 2)|BytesPerSecond|Maksimum||Boyut yok|
|percentProcessorTime2|CPU (parça 2)|Yüzde|Maksimum||Boyut yok|
|connectedclients3|Bağlı Istemciler (parça 3)|Sayı|Maksimum||Boyut yok|
|totalcommandsprocessed3|Toplam Işlem (parça 3)|Sayı|Toplam||Boyut yok|
|cachehits3|Önbellek ısabetleri (parça 3)|Sayı|Toplam||Boyut yok|
|cachemisses3|Önbellekte bulunamayanlar (parça 3)|Sayı|Toplam||Boyut yok|
|getcommands3|Alır (parça 3)|Sayı|Toplam||Boyut yok|
|setcommands3|Kümeler (parça 3)|Sayı|Toplam||Boyut yok|
|operationsPerSecond3|Saniyedeki işlem (parça 3)|Sayı|Maksimum||Boyut yok|
|evictedkeys3|Çıkarılan anahtarlar (parça 3)|Sayı|Toplam||Boyut yok|
|totalkeys3|Toplam anahtar (parça 3)|Sayı|Maksimum||Boyut yok|
|expiredkeys3|Süre dolma anahtarları (parça 3)|Sayı|Toplam||Boyut yok|
|usedmemory3|Kullanılan bellek (parça 3)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss3|Kullanılan bellek RSS (parça 3)|Bayt|Maksimum||Boyut yok|
|serverLoad3|Sunucu yükü (parça 3)|Yüzde|Maksimum||Boyut yok|
|cacheWrite3|Önbellek yazma (parça 3)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead3|Önbellek Okuma (parça 3)|BytesPerSecond|Maksimum||Boyut yok|
|percentProcessorTime3|CPU (parça 3)|Yüzde|Maksimum||Boyut yok|
|connectedclients4|Bağlı Istemciler (parça 4)|Sayı|Maksimum||Boyut yok|
|totalcommandsprocessed4|Toplam Işlem (parça 4)|Sayı|Toplam||Boyut yok|
|cachehits4|Önbellek ısabetleri (parça 4)|Sayı|Toplam||Boyut yok|
|cachemisses4|Önbellekte bulunamayanlar (parça 4)|Sayı|Toplam||Boyut yok|
|getcommands4|Alır (parça 4)|Sayı|Toplam||Boyut yok|
|setcommands4|Kümeler (parça 4)|Sayı|Toplam||Boyut yok|
|operationsPerSecond4|Saniyedeki işlem (parça 4)|Sayı|Maksimum||Boyut yok|
|evictedkeys4|Çıkarılan anahtarlar (parça 4)|Sayı|Toplam||Boyut yok|
|totalkeys4|Toplam anahtar (parça 4)|Sayı|Maksimum||Boyut yok|
|expiredkeys4|Süre dolma anahtarları (parça 4)|Sayı|Toplam||Boyut yok|
|usedmemory4|Kullanılan bellek (parça 4)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss4|Kullanılan bellek RSS (parça 4)|Bayt|Maksimum||Boyut yok|
|serverLoad4|Sunucu yükü (parça 4)|Yüzde|Maksimum||Boyut yok|
|cacheWrite4|Önbellek yazma (parça 4)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead4|Önbellek Okuma (parça 4)|BytesPerSecond|Maksimum||Boyut yok|
|percentProcessorTime4|CPU (parça 4)|Yüzde|Maksimum||Boyut yok|
|connectedclients5|Bağlı Istemciler (parça 5)|Sayı|Maksimum||Boyut yok|
|totalcommandsprocessed5|Toplam Işlem (parça 5)|Sayı|Toplam||Boyut yok|
|cachehits5|Önbellek ısabetleri (parça 5)|Sayı|Toplam||Boyut yok|
|cachemisses5|Önbellekte bulunamayanlar (parça 5)|Sayı|Toplam||Boyut yok|
|getcommands5|Alır (parça 5)|Sayı|Toplam||Boyut yok|
|setcommands5|Kümeler (parça 5)|Sayı|Toplam||Boyut yok|
|operationsPerSecond5|Saniyedeki işlem (parça 5)|Sayı|Maksimum||Boyut yok|
|evictedkeys5|Çıkarılan anahtarlar (parça 5)|Sayı|Toplam||Boyut yok|
|totalkeys5|Toplam anahtar (parça 5)|Sayı|Maksimum||Boyut yok|
|expiredkeys5|Süre dolma anahtarları (parça 5)|Sayı|Toplam||Boyut yok|
|usedmemory5|Kullanılan bellek (parça 5)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss5|Kullanılan bellek RSS (parça 5)|Bayt|Maksimum||Boyut yok|
|serverLoad5|Sunucu yükü (parça 5)|Yüzde|Maksimum||Boyut yok|
|cacheWrite5|Önbellek yazma (parça 5)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead5|Önbellek Okuma (parça 5)|BytesPerSecond|Maksimum||Boyut yok|
|percentProcessorTime5|CPU (parça 5)|Yüzde|Maksimum||Boyut yok|
|connectedclients6|Bağlı Istemciler (parça 6)|Sayı|Maksimum||Boyut yok|
|totalcommandsprocessed6|Toplam Işlem (parça 6)|Sayı|Toplam||Boyut yok|
|cachehits6|Önbellek ısabetleri (parça 6)|Sayı|Toplam||Boyut yok|
|cachemisses6|Önbellekte bulunamayanlar (parça 6)|Sayı|Toplam||Boyut yok|
|getcommands6|Alır (parça 6)|Sayı|Toplam||Boyut yok|
|setcommands6|Kümeler (parça 6)|Sayı|Toplam||Boyut yok|
|operationsPerSecond6|Saniyedeki işlem (parça 6)|Sayı|Maksimum||Boyut yok|
|evictedkeys6|Çıkarılan anahtarlar (parça 6)|Sayı|Toplam||Boyut yok|
|totalkeys6|Toplam anahtar (parça 6)|Sayı|Maksimum||Boyut yok|
|expiredkeys6|Süre dolma anahtarları (parça 6)|Sayı|Toplam||Boyut yok|
|usedmemory6|Kullanılan bellek (parça 6)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss6|Kullanılan bellek RSS (parça 6)|Bayt|Maksimum||Boyut yok|
|serverLoad6|Sunucu yükü (parça 6)|Yüzde|Maksimum||Boyut yok|
|cacheWrite6|Önbellek yazma (parça 6)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead6|Önbellek Okuma (parça 6)|BytesPerSecond|Maksimum||Boyut yok|
|percentProcessorTime6|CPU (parça 6)|Yüzde|Maksimum||Boyut yok|
|connectedclients7|Bağlı Istemciler (parça 7)|Sayı|Maksimum||Boyut yok|
|totalcommandsprocessed7|Toplam Işlem (parça 7)|Sayı|Toplam||Boyut yok|
|cachehits7|Önbellek ısabetleri (parça 7)|Sayı|Toplam||Boyut yok|
|cachemisses7|Önbellekte bulunamayanlar (parça 7)|Sayı|Toplam||Boyut yok|
|getcommands7|Alır (parça 7)|Sayı|Toplam||Boyut yok|
|setcommands7|Kümeler (parça 7)|Sayı|Toplam||Boyut yok|
|operationsPerSecond7|Saniyedeki işlem (parça 7)|Sayı|Maksimum||Boyut yok|
|evictedkeys7|Çıkarılan anahtarlar (parça 7)|Sayı|Toplam||Boyut yok|
|totalkeys7|Toplam anahtar (parça 7)|Sayı|Maksimum||Boyut yok|
|expiredkeys7|Süre dolma anahtarları (parça 7)|Sayı|Toplam||Boyut yok|
|usedmemory7|Kullanılan bellek (parça 7)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss7|Kullanılan bellek RSS (parça 7)|Bayt|Maksimum||Boyut yok|
|serverLoad7|Sunucu yükü (parça 7)|Yüzde|Maksimum||Boyut yok|
|cacheWrite7|Önbellek yazma (parça 7)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead7|Önbellek Okuma (parça 7)|BytesPerSecond|Maksimum||Boyut yok|
|percentProcessorTime7|CPU (parça 7)|Yüzde|Maksimum||Boyut yok|
|connectedclients8|Bağlı Istemciler (parça 8)|Sayı|Maksimum||Boyut yok|
|totalcommandsprocessed8|Toplam Işlem (parça 8)|Sayı|Toplam||Boyut yok|
|cachehits8|Önbellek ısabetleri (parça 8)|Sayı|Toplam||Boyut yok|
|cachemisses8|Önbellekte bulunamayanlar (parça 8)|Sayı|Toplam||Boyut yok|
|getcommands8|Al (Shard 8)|Sayı|Toplam||Boyut yok|
|setcommands8|Kümeler (Shard 8)|Sayı|Toplam||Boyut yok|
|operationsPerSecond8|Saniyedeki işlem (parça 8)|Sayı|Maksimum||Boyut yok|
|evictedkeys8|Çıkarılan anahtarlar (parça 8)|Sayı|Toplam||Boyut yok|
|totalkeys8|Toplam anahtar (Shard 8)|Sayı|Maksimum||Boyut yok|
|expiredkeys8|Süre dolma anahtarları (parça 8)|Sayı|Toplam||Boyut yok|
|usedmemory8|Kullanılan bellek (parça 8)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss8|Kullanılan bellek RSS (parça 8)|Bayt|Maksimum||Boyut yok|
|serverLoad8|Sunucu yükü (parça 8)|Yüzde|Maksimum||Boyut yok|
|cacheWrite8|Önbellek yazma (parça 8)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead8|Önbellek Okuma (parça 8)|BytesPerSecond|Maksimum||Boyut yok|
|percentProcessorTime8|CPU (parça 8)|Yüzde|Maksimum||Boyut yok|
|connectedclients9|Bağlı Istemciler (parça 9)|Sayı|Maksimum||Boyut yok|
|totalcommandsprocessed9|Toplam Işlem (parça 9)|Sayı|Toplam||Boyut yok|
|cachehits9|Önbellek ısabetleri (parça 9)|Sayı|Toplam||Boyut yok|
|cachemisses9|Önbellekte bulunamayanlar (parça 9)|Sayı|Toplam||Boyut yok|
|getcommands9|Alır (parça 9)|Sayı|Toplam||Boyut yok|
|setcommands9|Kümeler (parça 9)|Sayı|Toplam||Boyut yok|
|operationsPerSecond9|Saniyedeki işlem (parça 9)|Sayı|Maksimum||Boyut yok|
|evictedkeys9|Çıkarılan anahtarlar (parça 9)|Sayı|Toplam||Boyut yok|
|totalkeys9|Toplam anahtar (parça 9)|Sayı|Maksimum||Boyut yok|
|expiredkeys9|Süre dolma anahtarları (parça 9)|Sayı|Toplam||Boyut yok|
|usedmemory9|Kullanılan bellek (parça 9)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss9|Kullanılan bellek RSS (parça 9)|Bayt|Maksimum||Boyut yok|
|serverLoad9|Sunucu yükü (parça 9)|Yüzde|Maksimum||Boyut yok|
|cacheWrite9|Önbellek yazma (parça 9)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead9|Önbellek Okuma (parça 9)|BytesPerSecond|Maksimum||Boyut yok|
|percentProcessorTime9|CPU (parça 9)|Yüzde|Maksimum||Boyut yok|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|CPU yüzdesi|CPU yüzdesi|Yüzde|Ortalama|Şu anda sanal makineler tarafından kullanılan ayrılmış işlem birimlerinin yüzdesi.|Boyut yok|
|Ağ Girişi|Ağ Girişi|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde alınan bayt sayısı (gelen trafik).|Boyut yok|
|Ağ Çıkışı|Ağ Çıkışı|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde giden bayt sayısı (giden trafik).|Boyut yok|
|Disk okuma bayt/sn|Disk okuma|BytesPerSecond|Ortalama|İzleme dönemi boyunca diskten okunan ortalama bayt.|Boyut yok|
|Disk yazma bayt/sn|Disk yazma|BytesPerSecond|Ortalama|İzleme dönemi boyunca diske yazılan ortalama bayt.|Boyut yok|
|Disk okuma Işlemi/sn|Disk okuma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk okuma ıOPS 'si.|Boyut yok|
|Disk yazma Işlemi/sn|Disk yazma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk yazma ıOPS 'si.|Boyut yok|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domainNames/yuvalar/roller

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|CPU yüzdesi|CPU yüzdesi|Yüzde|Ortalama|Şu anda sanal makineler tarafından kullanılan ayrılmış işlem birimlerinin yüzdesi.|Roleınstanceıd|
|Ağ Girişi|Ağ Girişi|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde alınan bayt sayısı (gelen trafik).|Roleınstanceıd|
|Ağ Çıkışı|Ağ Çıkışı|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde giden bayt sayısı (giden trafik).|Roleınstanceıd|
|Disk okuma bayt/sn|Disk okuma|BytesPerSecond|Ortalama|İzleme dönemi boyunca diskten okunan ortalama bayt.|Roleınstanceıd|
|Disk yazma bayt/sn|Disk yazma|BytesPerSecond|Ortalama|İzleme dönemi boyunca diske yazılan ortalama bayt.|Roleınstanceıd|
|Disk okuma Işlemi/sn|Disk okuma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk okuma ıOPS 'si.|Roleınstanceıd|
|Disk yazma Işlemi/sn|Disk yazma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk yazma ıOPS 'si.|Roleınstanceıd|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Toplam çağrılar|Toplam çağrı sayısı|Sayı|Toplam|Toplam çağrı sayısı.|ApiName, OperationName, bölge|
|Başarılı çağrılar|Başarılı çağrılar|Sayı|Toplam|Başarılı çağrı sayısı.|ApiName, OperationName, bölge|
|Toplam hata sayısı|Toplam hata sayısı|Sayı|Toplam|Hata yanıtıyla Toplam çağrı sayısı (HTTP yanıt kodu 4xx veya 5xx).|ApiName, OperationName, bölge|
|Blockedçağrılarında|Engellenen çağrılar|Sayı|Toplam|Oran veya kota sınırını aşan çağrı sayısı.|ApiName, OperationName, bölge|
|ServerErrors|Sunucu hataları|Sayı|Toplam|Hizmet iç hatası olan çağrı sayısı (HTTP yanıt kodu 5xx).|ApiName, OperationName, bölge|
|ClientErrors|İstemci hataları|Sayı|Toplam|İstemci tarafı hatası olan çağrı sayısı (HTTP yanıt kodu 4xx).|ApiName, OperationName, bölge|
|Dataın|Içindeki veriler|Bayt|Toplam|Gelen verilerin bayt cinsinden boyutu.|ApiName, OperationName, bölge|
|Veri çıkışı|Giden veriler|Bayt|Toplam|Giden verilerin bayt cinsinden boyutu.|ApiName, OperationName, bölge|
|Gecikme süresi|Gecikme süresi|Mayacak|Ortalama|Milisaniye cinsinden gecikme süresi.|ApiName, OperationName, bölge|
|Karakter Yabanslamuş|Çevrilen karakterler|Sayı|Toplam|Gelen metin isteğindeki toplam karakter sayısı.|ApiName, OperationName, bölge|
|Karakterçeli|Eğitilen karakterler|Sayı|Toplam|Eğitilen toplam karakter sayısı.|ApiName, OperationName, bölge|
|SpeechSessionDuration|Konuşma oturumu süresi|Saniye|Toplam|Konuşma oturumunun saniye cinsinden toplam süresi.|ApiName, OperationName, bölge|
|TotalTransactions|Toplam Işlem sayısı|Sayı|Toplam|Toplam işlem sayısı.|Boyut yok|
|TotalTokenCalls|Toplam belirteç çağrısı sayısı|Sayı|Toplam|Toplam belirteç çağrısı sayısı.|ApiName, OperationName, bölge|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|CPU yüzdesi|CPU yüzdesi|Yüzde|Ortalama|Sanal Makineler tarafından kullanılan ayrılmış işlem birimlerinin geçerli yüzdesi|Boyut yok|
|Ağ Girişi|Faturalanabilir ağ|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde alınan faturalandırılabilir bayt sayısı (gelen trafik)|Boyut yok|
|Ağ Çıkışı|Ağ üzerinden faturalandırılabilir|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde faturalandırılabilir bayt sayısı (giden trafik)|Boyut yok|
|Disk okuma bayt sayısı|Disk okuma bayt sayısı|Bayt|Toplam|İzleme dönemi boyunca diskten okunan bayt sayısı|Boyut yok|
|Disk yazma baytları|Disk yazma baytları|Bayt|Toplam|İzleme dönemi boyunca diske yazılan bayt sayısı|Boyut yok|
|Disk okuma Işlemi/sn|Disk okuma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk okuma ıOPS 'si|Boyut yok|
|Disk yazma Işlemi/sn|Disk yazma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk yazma ıOPS 'si|Boyut yok|
|Kalan CPU kredileri|Kalan CPU kredileri|Sayı|Ortalama|Patlama için kullanılabilen toplam kredi sayısı|Boyut yok|
|Tüketilen CPU kredileri|Tüketilen CPU kredileri|Sayı|Ortalama|Sanal makine tarafından tüketilen toplam kredi sayısı|Boyut yok|
|Disk başına okunan bayt/sn|Veri diski okuma bayt/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diskten okunan bayt/sn|SlotID|
|Disk başına yazma bayt/sn|Veri diski yazma bayt/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diske yazılan bayt/sn|SlotID|
|Disk başına okuma Işlemi/sn|Veri diski okuma Işlemi/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diskten ıOPS 'yi okuma|SlotID|
|Disk başına yazma Işlemi/sn|Veri diski yazma Işlemi/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İzleme dönemi boyunca tek bir diskten ıOPS yazma|SlotID|
|Disk başına QD|Veri diski QD (kullanım dışı)|Sayı|Ortalama|Veri diski sıra derinliği (veya sıra uzunluğu)|SlotID|
|İşletim sistemi disk başına okuma bayt/sn|İşletim sistemi diski okuma bayt/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten okunan bayt/sn|Boyut yok|
|İşletim sistemi disk başına yazma bayt/sn|İşletim sistemi diski yazma bayt/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diske yazılan bayt/sn|Boyut yok|
|İşletim sistemi diski başına okuma Işlemi/sn|İşletim sistemi diski okuma Işlemi/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten ıOPS 'yi okuyun|Boyut yok|
|İşletim sistemi diski başına yazma Işlemi/sn|İşletim sistemi diski yazma Işlemi/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten ıOPS yaz|Boyut yok|
|Disk başına işletim sistemi QD|İşletim sistemi diski QD (kullanım dışı)|Sayı|Ortalama|İşletim sistemi diski sıra derinliği (veya sıra uzunluğu)|Boyut yok|
|Veri diski okuma bayt/sn|Veri diski okuma bayt/sn (Önizleme)|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diskten okunan bayt/sn|LUN|
|Veri diski yazma bayt/sn|Veri diski yazma bayt/sn (Önizleme)|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diske yazılan bayt/sn|LUN|
|Veri diski okuma Işlemi/sn|Veri diski okuma Işlemi/sn (Önizleme)|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diskten ıOPS 'yi okuma|LUN|
|Veri diski yazma Işlemi/sn|Veri diski yazma Işlemi/sn (Önizleme)|Sayaçpersaniye|Ortalama|İzleme dönemi boyunca tek bir diskten ıOPS yazma|LUN|
|Veri diski sıra derinliği|Veri diski sıra derinliği (Önizleme)|Sayı|Ortalama|Veri diski sıra derinliği (veya sıra uzunluğu)|LUN|
|İşletim sistemi diski okuma bayt/sn|İşletim sistemi diski okuma bayt/sn (Önizleme)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten okunan bayt/sn|Boyut yok|
|İşletim sistemi diski yazma bayt/sn|İşletim sistemi diski yazma bayt/sn (Önizleme)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diske yazılan bayt/sn|Boyut yok|
|İşletim sistemi diski okuma Işlemi/sn|İşletim sistemi diski okuma Işlemi/sn (Önizleme)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten ıOPS 'yi okuyun|Boyut yok|
|İşletim sistemi diski yazma Işlemi/sn|İşletim sistemi diski yazma Işlemi/sn (Önizleme)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten ıOPS yaz|Boyut yok|
|İşletim sistemi diski sıra derinliği|İşletim sistemi diski sıra derinliği (Önizleme)|Sayı|Ortalama|İşletim sistemi diski sıra derinliği (veya sıra uzunluğu)|Boyut yok|
|Gelen akışlar|Gelen akışlar (Önizleme)|Sayı|Ortalama|Gelen akışlar, gelen yöndeki geçerli akışların sayısıdır (VM 'ye giden trafik)|Boyut yok|
|Giden akışlar|Giden akışlar (Önizleme)|Sayı|Ortalama|Giden akışlar giden yöndeki geçerli akış sayısıdır (VM 'den giden trafik)|Boyut yok|
|Gelen akışlar en yüksek oluşturma oranı|Gelen akışlar en yüksek oluşturma oranı (Önizleme)|Sayaçpersaniye|Ortalama|Gelen akışların en yüksek oluşturma oranı (VM 'ye giden trafik)|Boyut yok|
|Giden akış maksimum oluşturma oranı|Giden akışlar maksimum oluşturma oranı (Önizleme)|Sayaçpersaniye|Ortalama|Giden akışların en yüksek oluşturma oranı (VM 'den giden trafik)|Boyut yok|
|Premium veri diski önbelleği okuma Isabeti|Premium veri diski önbelleği okuma Isabeti (Önizleme)|Yüzde|Ortalama|Premium veri diski önbelleği okuma Isabeti|LUN|
|Premium veri diski önbelleği okuma Isabetsizliği|Premium veri diski önbelleği okuma Isabetsizlik (Önizleme)|Yüzde|Ortalama|Premium veri diski önbelleği okuma Isabetsizliği|LUN|
|Premium işletim sistemi disk önbelleği okuma Isabeti|Premium işletim sistemi disk önbelleği okuma Isabeti (Önizleme)|Yüzde|Ortalama|Premium işletim sistemi disk önbelleği okuma Isabeti|Boyut yok|
|Premium işletim sistemi disk önbelleği okuma Isabetsizlik|Premium işletim sistemi disk önbelleği okuma Isabetsizlik (Önizleme)|Yüzde|Ortalama|Premium işletim sistemi disk önbelleği okuma Isabetsizlik|Boyut yok|
|Toplam ağ|Toplam ağ|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde alınan bayt sayısı (gelen trafik)|Boyut yok|
|Toplam ağ çıkışı|Toplam ağ çıkışı|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde giden bayt sayısı (giden trafik)|Boyut yok|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|CPU yüzdesi|CPU yüzdesi|Yüzde|Ortalama|Sanal Makineler tarafından kullanılan ayrılmış işlem birimlerinin geçerli yüzdesi|VMName|
|Ağ Girişi|Faturalanabilir ağ|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde alınan faturalandırılabilir bayt sayısı (gelen trafik)|VMName|
|Ağ Çıkışı|Ağ üzerinden faturalandırılabilir|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde faturalandırılabilir bayt sayısı (giden trafik)|VMName|
|Disk okuma bayt sayısı|Disk okuma bayt sayısı|Bayt|Toplam|İzleme dönemi boyunca diskten okunan bayt sayısı|VMName|
|Disk yazma baytları|Disk yazma baytları|Bayt|Toplam|İzleme dönemi boyunca diske yazılan bayt sayısı|VMName|
|Disk okuma Işlemi/sn|Disk okuma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk okuma ıOPS 'si|VMName|
|Disk yazma Işlemi/sn|Disk yazma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk yazma ıOPS 'si|VMName|
|Kalan CPU kredileri|Kalan CPU kredileri|Sayı|Ortalama|Patlama için kullanılabilen toplam kredi sayısı|Boyut yok|
|Tüketilen CPU kredileri|Tüketilen CPU kredileri|Sayı|Ortalama|Sanal makine tarafından tüketilen toplam kredi sayısı|Boyut yok|
|Disk başına okunan bayt/sn|Veri diski okuma bayt/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diskten okunan bayt/sn|SlotID|
|Disk başına yazma bayt/sn|Veri diski yazma bayt/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diske yazılan bayt/sn|SlotID|
|Disk başına okuma Işlemi/sn|Veri diski okuma Işlemi/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diskten ıOPS 'yi okuma|SlotID|
|Disk başına yazma Işlemi/sn|Veri diski yazma Işlemi/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İzleme dönemi boyunca tek bir diskten ıOPS yazma|SlotID|
|Disk başına QD|Veri diski QD (kullanım dışı)|Sayı|Ortalama|Veri diski sıra derinliği (veya sıra uzunluğu)|SlotID|
|İşletim sistemi disk başına okuma bayt/sn|İşletim sistemi diski okuma bayt/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten okunan bayt/sn|Boyut yok|
|İşletim sistemi disk başına yazma bayt/sn|İşletim sistemi diski yazma bayt/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diske yazılan bayt/sn|Boyut yok|
|İşletim sistemi diski başına okuma Işlemi/sn|İşletim sistemi diski okuma Işlemi/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten ıOPS 'yi okuyun|Boyut yok|
|İşletim sistemi diski başına yazma Işlemi/sn|İşletim sistemi diski yazma Işlemi/sn (kullanım dışı)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten ıOPS yaz|Boyut yok|
|Disk başına işletim sistemi QD|İşletim sistemi diski QD (kullanım dışı)|Sayı|Ortalama|İşletim sistemi diski sıra derinliği (veya sıra uzunluğu)|Boyut yok|
|Veri diski okuma bayt/sn|Veri diski okuma bayt/sn (Önizleme)|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diskten okunan bayt/sn|LUN, VMName|
|Veri diski yazma bayt/sn|Veri diski yazma bayt/sn (Önizleme)|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diske yazılan bayt/sn|LUN, VMName|
|Veri diski okuma Işlemi/sn|Veri diski okuma Işlemi/sn (Önizleme)|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diskten ıOPS 'yi okuma|LUN, VMName|
|Veri diski yazma Işlemi/sn|Veri diski yazma Işlemi/sn (Önizleme)|Sayaçpersaniye|Ortalama|İzleme dönemi boyunca tek bir diskten ıOPS yazma|LUN, VMName|
|Veri diski sıra derinliği|Veri diski sıra derinliği (Önizleme)|Sayı|Ortalama|Veri diski sıra derinliği (veya sıra uzunluğu)|LUN, VMName|
|İşletim sistemi diski okuma bayt/sn|İşletim sistemi diski okuma bayt/sn (Önizleme)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten okunan bayt/sn|VMName|
|İşletim sistemi diski yazma bayt/sn|İşletim sistemi diski yazma bayt/sn (Önizleme)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diske yazılan bayt/sn|VMName|
|İşletim sistemi diski okuma Işlemi/sn|İşletim sistemi diski okuma Işlemi/sn (Önizleme)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten ıOPS 'yi okuyun|VMName|
|İşletim sistemi diski yazma Işlemi/sn|İşletim sistemi diski yazma Işlemi/sn (Önizleme)|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten ıOPS yaz|VMName|
|İşletim sistemi diski sıra derinliği|İşletim sistemi diski sıra derinliği (Önizleme)|Sayı|Ortalama|İşletim sistemi diski sıra derinliği (veya sıra uzunluğu)|VMName|
|Gelen akışlar|Gelen akışlar (Önizleme)|Sayı|Ortalama|Gelen akışlar, gelen yöndeki geçerli akışların sayısıdır (VM 'ye giden trafik)|VMName|
|Giden akışlar|Giden akışlar (Önizleme)|Sayı|Ortalama|Giden akışlar giden yöndeki geçerli akış sayısıdır (VM 'den giden trafik)|VMName|
|Gelen akışlar en yüksek oluşturma oranı|Gelen akışlar en yüksek oluşturma oranı (Önizleme)|Sayaçpersaniye|Ortalama|Gelen akışların en yüksek oluşturma oranı (VM 'ye giden trafik)|VMName|
|Giden akış maksimum oluşturma oranı|Giden akışlar maksimum oluşturma oranı (Önizleme)|Sayaçpersaniye|Ortalama|Giden akışların en yüksek oluşturma oranı (VM 'den giden trafik)|VMName|
|Premium veri diski önbelleği okuma Isabeti|Premium veri diski önbelleği okuma Isabeti (Önizleme)|Yüzde|Ortalama|Premium veri diski önbelleği okuma Isabeti|LUN, VMName|
|Premium veri diski önbelleği okuma Isabetsizliği|Premium veri diski önbelleği okuma Isabetsizlik (Önizleme)|Yüzde|Ortalama|Premium veri diski önbelleği okuma Isabetsizliği|LUN, VMName|
|Premium işletim sistemi disk önbelleği okuma Isabeti|Premium işletim sistemi disk önbelleği okuma Isabeti (Önizleme)|Yüzde|Ortalama|Premium işletim sistemi disk önbelleği okuma Isabeti|VMName|
|Premium işletim sistemi disk önbelleği okuma Isabetsizlik|Premium işletim sistemi disk önbelleği okuma Isabetsizlik (Önizleme)|Yüzde|Ortalama|Premium işletim sistemi disk önbelleği okuma Isabetsizlik|VMName|
|Toplam ağ|Toplam ağ|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde alınan bayt sayısı (gelen trafik)|VMName|
|Toplam ağ çıkışı|Toplam ağ çıkışı|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde giden bayt sayısı (giden trafik)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. Containerınstance/containerGroups

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|CpuUsage|CPU kullanımı|Sayı|Ortalama|Miliçekirdekte tüm çekirdekler üzerinde CPU kullanımı.|Adı|
|MemoryUsage|Bellek kullanımı|Bayt|Ortalama|Bayt cinsinden toplam bellek kullanımı.|Adı|
|NetworkBytesReceivedPerSecond|Saniye başına alınan ağ bayt sayısı|Bayt|Ortalama|Saniye başına alınan ağ bayt sayısı.|Boyut yok|
|NetworkBytesTransmittedPerSecond|Saniye başına aktarılan ağ baytları|Bayt|Ortalama|Saniye başına aktarılan ağ baytları.|Boyut yok|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/kayıt defterleri

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|TotalPullCount|Toplam çekme sayısı|Sayı|Ortalama|Toplam görüntü sayısı|Boyut yok|
|Başarılı sayı|Başarılı çekme sayısı|Sayı|Ortalama|Başarılı görüntü sayısı|Boyut yok|
|Toplam Pushcount|Toplam gönderme sayısı|Sayı|Ortalama|Toplam resim gönderim sayısı|Boyut yok|
|Başarılı Pushcount|Başarılı gönderme sayısı|Sayı|Ortalama|Başarılı görüntü gönderme sayısı|Boyut yok|
|RunDuration|Çalışma süresi|Milisaniye|Toplam|Çalışma süresi (milisaniye)|Boyut yok|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/Managedkümeler

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Yönetilen kümede toplam kullanılabilir CPU çekirdeği sayısı|Sayı|Toplam|Yönetilen kümede toplam kullanılabilir CPU çekirdeği sayısı|Boyut yok|
|kube_node_status_allocatable_memory_bytes|Yönetilen kümede toplam kullanılabilir bellek miktarı|Bayt|Toplam|Yönetilen kümede toplam kullanılabilir bellek miktarı|Boyut yok|
|kube_pod_status_ready|Hazırlık durumundaki Pod sayısı|Sayı|Toplam|Hazırlık durumundaki Pod sayısı|ad alanı, Pod|
|kube_node_status_condition|Çeşitli düğüm koşullarına yönelik durumlar|Sayı|Toplam|Çeşitli düğüm koşullarına yönelik durumlar|koşul, durum, status2, düğüm|
|kube_pod_status_phase|Aşamasına göre Pod sayısı|Sayı|Toplam|Aşamasına göre Pod sayısı|aşama, ad alanı, Pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft. Customerınsights/hub 'ları

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Dcıapıalls|Customer Insights API çağrıları|Sayı|Toplam||Boyut yok|
|Dcimappıngimportoperationbaşarılı satırlar|Içeri aktarma Işlemi başarılı satırları eşleme|Sayı|Toplam||Boyut yok|
|Dcımappıngimportoperationfailedlines|Eşleme Içeri aktarma Işlemi başarısız satırlar|Sayı|Toplam||Boyut yok|
|Dcımappıngimportoperationtotallines|Eşleme Içeri aktarma Işlemi toplam satırlar|Sayı|Toplam||Boyut yok|
|Dcımappıngimportoperationruntimeınseconds|Içeri aktarma Işlemi çalışma zamanını saniyeler Içinde eşleme|Saniye|Toplam||Boyut yok|
|DCIOutboundProfileExportSucceeded|Giden profili dışarı aktarma başarılı|Sayı|Toplam||Boyut yok|
|DCIOutboundProfileExportFailed|Giden profili dışarı aktarma başarısız|Sayı|Toplam||Boyut yok|
|DCIOutboundProfileExportDuration|Giden profili dışarı aktarma süresi|Saniye|Toplam||Boyut yok|
|Dcıoutboundkpiexportsucceeded|Giden KPI dışarı aktarma başarılı|Sayı|Toplam||Boyut yok|
|Dcıoutboundkpiexportfailed|Giden KPI dışarı aktarılamadı|Sayı|Toplam||Boyut yok|
|Dcıoutboundkpiexportduration|Giden KPI dışarı aktarma süresi|Saniye|Toplam||Boyut yok|
|Dcıoutboundkpiexportstarted|Giden KPI dışarı aktarma başlatıldı|Saniye|Toplam||Boyut yok|
|DCIOutboundKpiRecordCount|Giden KPI kayıt sayısı|Saniye|Toplam||Boyut yok|
|DCIOutboundProfileExportCount|Giden profili dışarı aktarma sayısı|Saniye|Toplam||Boyut yok|
|DCIOutboundInitialProfileExportFailed|Giden Ilk profili dışarı aktarma başarısız|Saniye|Toplam||Boyut yok|
|DCIOutboundInitialProfileExportSucceeded|Giden Ilk profili dışarı aktarma başarılı|Saniye|Toplam||Boyut yok|
|Dcıoutbounınvoitialkpiexportbaşarısız oldu|Giden başlangıç KPI 'si dışarı aktarılamadı|Saniye|Toplam||Boyut yok|
|Dcıoutboundınitialkpiexportsucceeded|Giden Ilk KPI dışarı aktarma başarılı|Saniye|Toplam||Boyut yok|
|DCIOutboundInitialProfileExportDurationInSeconds|Giden Ilk profil dışa aktarma süresi (saniye)|Saniye|Toplam||Boyut yok|
|AdlaJobForStandardKpiFailed|Standart KPI Için adla Işi saniye cinsinden başarısız oldu|Saniye|Toplam||Boyut yok|
|AdlaJobForStandardKpiTimeOut|Standart KPI zaman aşımı Için saniye cinsinden adla Işi|Saniye|Toplam||Boyut yok|
|AdlaJobForStandardKpiCompleted|Standart KPI Için adla Işi saniye cinsinden tamamlandı|Saniye|Toplam||Boyut yok|
|Importasavaluesfailed|ASA değerlerini içeri aktarma başarısız sayısı|Sayı|Toplam||Boyut yok|
|Importasavaluessucceeded|ASA değerlerini içeri aktarma başarılı sayısı|Sayı|Toplam||Boyut yok|
|Dcıprofilscount|Profil örnek sayısı|Sayı|Son||Boyut yok|
|Dciınteractionspermonthcount|Aylık etkileşimler sayısı|Sayı|Son||Boyut yok|
|DCIKpisCount|KPI sayısı|Sayı|Son||Boyut yok|
|DCISegmentsCount|Segment sayısı|Sayı|Son||Boyut yok|
|Dcıpredictivematchpoliciescount|Tahmine dayalı eşleşme sayısı|Sayı|Son||Boyut yok|
|DCIPredictionsCount|Tahmin sayısı|Sayı|Son||Boyut yok|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Nicreadüretilen Iş|Okuma performansı (ağ)|BytesPerSecond|Ortalama|Cihazdaki ağ arabiriminin, ağ geçidinde bulunan tüm birimler için raporlama döneminde okuma performansı.|InstanceName|
|Nicwriteüretilen Iş|Yazma Işleme (ağ)|BytesPerSecond|Ortalama|Cihazdaki ağ arabiriminin, ağ geçidinde bulunan tüm birimler için raporlama döneminde yazma aktarım hızı.|InstanceName|
|CloudReadThroughputPerShare|Bulut Indirme verimlilik (paylaşma)|BytesPerSecond|Ortalama|Raporlama dönemi boyunca bir paylaşımdan Azure 'a aktarım hızını indirin.|Paylaş|
|CloudUploadThroughputPerShare|Bulut karşıya yükleme üretilen Işi (paylaşma)|BytesPerSecond|Ortalama|Raporlama dönemi boyunca bir paylaşımdan Azure 'a yük aktarımını yükleme.|Paylaş|
|BytesUploadedToCloudPerShare|Karşıya yüklenen bulut baytları (paylaşma)|Bayt|Ortalama|Raporlama dönemi boyunca bir paylaşımdan Azure 'a yüklenen toplam bayt sayısı.|Paylaş|
|TotalCapacity|Toplam kapasite|Bayt|Ortalama|Toplam kapasite|Boyut yok|
|AvailableCapacity|Kullanılabilir kapasite|Bayt|Ortalama|Raporlama dönemi boyunca kullanılabilir kapasite (bayt cinsinden).|Boyut yok|
|Clouduploadüretilen Iş|Bulut karşıya yükleme performansı|BytesPerSecond|Ortalama|Raporlama dönemi boyunca Azure 'a bulut yükleme performansı.|Boyut yok|
|Cloudreadüretilen Iş|Bulut Indirme performansı|BytesPerSecond|Ortalama|Raporlama döneminde Azure 'a bulut indirme performansı.|Boyut yok|
|BytesUploadedToCloud|Karşıya yüklenen bulut baytları (cihaz)|Bayt|Ortalama|Raporlama dönemi boyunca bir cihazdan Azure 'a yüklenen toplam bayt sayısı.|Boyut yok|
|Hypervvirtualprocessorkullanım|Edge hesaplama-CPU yüzdesi|Yüzde|Ortalama|CPU kullanımı yüzdesi|InstanceName|
|Hipervmemoryutilileştirme|Edge Işlem-bellek kullanımı|Yüzde|Ortalama|Kullanımdaki RAM miktarı|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/DataFactory

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Failedçalıştırmaları|Başarısız çalıştırmalar|Sayı|Toplam||Ardışık düzen ınename, activityName|
|Başarılı çalıştırmalar|Başarılı çalıştırmalar|Sayı|Toplam||Ardışık düzen ınename, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/Factory

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Pipelinefailedçalıştırmaları|Başarısız işlem hattı çalıştırmaları ölçümleri|Sayı|Toplam||Failuıyeniden yazma, ad|
|Ardışık düzen Inesucceededçalıştırmaları|Başarılı işlem hattı çalışma ölçümleri|Sayı|Toplam||Failuıyeniden yazma, ad|
|Activityfailedçalıştırmaları|Başarısız etkinlik çalıştırma ölçümleri|Sayı|Toplam||ActivityType, ardışık düzen adı, Failuyeniden yazma, ad|
|ActivitySucceededRuns|Başarılı etkinlik ölçümleri çalıştırıyor|Sayı|Toplam||ActivityType, ardışık düzen adı, Failuyeniden yazma, ad|
|Triggerfailedçalıştırmaları|Başarısız tetikleyici çalıştırma ölçümleri|Sayı|Toplam||Ad, Failuyeniden yazma|
|TriggerSucceededRuns|Başarılı tetikleyici çalışan ölçümleri|Sayı|Toplam||Ad, Failuyeniden yazma|
|Integrationruntimecpuyüzdesi|Tümleştirme çalışma zamanı CPU kullanımı|Yüzde|Ortalama||Integrationruntimename, düğ,|
|Integrationruntimekullanılabilirliği Blememory|Tümleştirme çalışma zamanı kullanılabilir belleği|Bayt|Ortalama||Integrationruntimename, düğ,|
|MaxAllowedResourceCount|İzin verilen en fazla varlık sayısı|Sayı|Maksimum||Boyut yok|
|Maxallowedfactorysizeingbits|İzin verilen en yüksek fabrika boyutu (GB birimi)|Sayı|Maksimum||Boyut yok|
|ResourceCount|Toplam varlık sayısı|Sayı|Maksimum||Boyut yok|
|Factorysizeingbits|Toplam fabrika boyutu (GB birimi)|Sayı|Maksimum||Boyut yok|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|JobEndedSuccess|Başarılı Işler|Sayı|Toplam|Başarılı iş sayısı.|Boyut yok|
|JobEndedFailure|Başarısız Işler|Sayı|Toplam|Başarısız iş sayısı.|Boyut yok|
|Jobendediptal edildi|İptal edilen Işler|Sayı|Toplam|İptal edilen işlerin sayısı.|Boyut yok|
|JobAUEndedSuccess|Başarılı AU Saati|Saniye|Toplam|Başarılı işler için toplam AU süresi.|Boyut yok|
|JobAUEndedFailure|Başarısız AU Saati|Saniye|Toplam|Başarısız işler için toplam AU süresi.|Boyut yok|
|Jobauendediptal edildi|İptal edilen AU Saati|Saniye|Toplam|İptal edilen işler için toplam AU süresi.|Boyut yok|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|TotalStorage|Toplam Depolama Alanı|Bayt|Maksimum|Hesapta depolanan toplam veri miktarı.|Boyut yok|
|Veri yazıldı|Yazılan veriler|Bayt|Toplam|Hesaba yazılan toplam veri miktarı.|Boyut yok|
|DataRead|Okunan veriler|Bayt|Toplam|Hesaptan okunan toplam veri miktarı.|Boyut yok|
|WriteRequests|Yazma Istekleri|Sayı|Toplam|Hesaba veri yazma isteği sayısı.|Boyut yok|
|ReadRequests|Okuma Istekleri|Sayı|Toplam|Hesaba yönelik veri okuma isteklerinin sayısı.|Boyut yok|

## <a name="microsoftdbformariadbservers"></a>Microsoft. Dbformarıdb/sunucular

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|cpu_percent|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|Boyut yok|
|memory_percent|Bellek yüzdesi|Yüzde|Ortalama|Bellek yüzdesi|Boyut yok|
|io_consumption_percent|GÇ yüzdesi|Yüzde|Ortalama|GÇ yüzdesi|Boyut yok|
|storage_percent|Depolama alanı yüzdesi|Yüzde|Ortalama|Depolama alanı yüzdesi|Boyut yok|
|storage_used|Kullanılan depolama|Bayt|Ortalama|Kullanılan depolama|Boyut yok|
|storage_limit|Depolama sınırı|Bayt|Ortalama|Depolama sınırı|Boyut yok|
|serverlog_storage_percent|Sunucu günlüğü depolama yüzdesi|Yüzde|Ortalama|Sunucu günlüğü depolama yüzdesi|Boyut yok|
|serverlog_storage_usage|Kullanılan sunucu günlüğü depolaması|Bayt|Ortalama|Kullanılan sunucu günlüğü depolaması|Boyut yok|
|serverlog_storage_limit|Sunucu günlüğü depolama sınırı|Bayt|Ortalama|Sunucu günlüğü depolama sınırı|Boyut yok|
|active_connections|Etkin bağlantılar|Sayı|Ortalama|Etkin bağlantılar|Boyut yok|
|connections_failed|Başarısız Bağlantılar|Sayı|Toplam|Başarısız Bağlantılar|Boyut yok|
|seconds_behind_master|Saniye cinsinden çoğaltma gecikmesi|Sayı|Ortalama|Saniye cinsinden çoğaltma gecikmesi|Boyut yok|
|backup_storage_used|Kullanılan yedekleme depolama alanı|Bayt|Ortalama|Kullanılan yedekleme depolama alanı|Boyut yok|
|network_bytes_egress|Ağ Çıkışı|Bayt|Toplam|Etkin bağlantılar arasında ağ çıkışı|Boyut yok|
|network_bytes_ingress|Ağ Girişi|Bayt|Toplam|Etkin bağlantılar genelinde ağ|Boyut yok|

## <a name="microsoftdbformysqlservers"></a>Microsoft. Dbformyısql/sunucuları

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|cpu_percent|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|Boyut yok|
|memory_percent|Bellek yüzdesi|Yüzde|Ortalama|Bellek yüzdesi|Boyut yok|
|io_consumption_percent|GÇ yüzdesi|Yüzde|Ortalama|GÇ yüzdesi|Boyut yok|
|storage_percent|Depolama alanı yüzdesi|Yüzde|Ortalama|Depolama alanı yüzdesi|Boyut yok|
|storage_used|Kullanılan depolama|Bayt|Ortalama|Kullanılan depolama|Boyut yok|
|storage_limit|Depolama sınırı|Bayt|Ortalama|Depolama sınırı|Boyut yok|
|serverlog_storage_percent|Sunucu günlüğü depolama yüzdesi|Yüzde|Ortalama|Sunucu günlüğü depolama yüzdesi|Boyut yok|
|serverlog_storage_usage|Kullanılan sunucu günlüğü depolaması|Bayt|Ortalama|Kullanılan sunucu günlüğü depolaması|Boyut yok|
|serverlog_storage_limit|Sunucu günlüğü depolama sınırı|Bayt|Ortalama|Sunucu günlüğü depolama sınırı|Boyut yok|
|active_connections|Etkin bağlantılar|Sayı|Ortalama|Etkin bağlantılar|Boyut yok|
|connections_failed|Başarısız Bağlantılar|Sayı|Toplam|Başarısız Bağlantılar|Boyut yok|
|seconds_behind_master|Saniye cinsinden çoğaltma gecikmesi|Sayı|Ortalama|Saniye cinsinden çoğaltma gecikmesi|Boyut yok|
|backup_storage_used|Kullanılan yedekleme depolama alanı|Bayt|Ortalama|Kullanılan yedekleme depolama alanı|Boyut yok|
|network_bytes_egress|Ağ Çıkışı|Bayt|Toplam|Etkin bağlantılar arasında ağ çıkışı|Boyut yok|
|network_bytes_ingress|Ağ Girişi|Bayt|Toplam|Etkin bağlantılar genelinde ağ|Boyut yok|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/sunucuları

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|cpu_percent|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|Boyut yok|
|memory_percent|Bellek yüzdesi|Yüzde|Ortalama|Bellek yüzdesi|Boyut yok|
|io_consumption_percent|GÇ yüzdesi|Yüzde|Ortalama|GÇ yüzdesi|Boyut yok|
|storage_percent|Depolama alanı yüzdesi|Yüzde|Ortalama|Depolama alanı yüzdesi|Boyut yok|
|storage_used|Kullanılan depolama|Bayt|Ortalama|Kullanılan depolama|Boyut yok|
|storage_limit|Depolama sınırı|Bayt|Ortalama|Depolama sınırı|Boyut yok|
|serverlog_storage_percent|Sunucu günlüğü depolama yüzdesi|Yüzde|Ortalama|Sunucu günlüğü depolama yüzdesi|Boyut yok|
|serverlog_storage_usage|Kullanılan sunucu günlüğü depolaması|Bayt|Ortalama|Kullanılan sunucu günlüğü depolaması|Boyut yok|
|serverlog_storage_limit|Sunucu günlüğü depolama sınırı|Bayt|Ortalama|Sunucu günlüğü depolama sınırı|Boyut yok|
|active_connections|Etkin bağlantılar|Sayı|Ortalama|Etkin bağlantılar|Boyut yok|
|connections_failed|Başarısız Bağlantılar|Sayı|Toplam|Başarısız Bağlantılar|Boyut yok|
|backup_storage_used|Kullanılan yedekleme depolama alanı|Bayt|Ortalama|Kullanılan yedekleme depolama alanı|Boyut yok|
|network_bytes_egress|Ağ Çıkışı|Bayt|Toplam|Etkin bağlantılar arasında ağ çıkışı|Boyut yok|
|network_bytes_ingress|Ağ Girişi|Bayt|Toplam|Etkin bağlantılar genelinde ağ|Boyut yok|
|pg_replica_log_delay_in_seconds|Çoğaltma gecikmesi|Saniye|Maksimum|Saniye cinsinden çoğaltma gecikmesi|Boyut yok|
|pg_replica_log_delay_in_bytes|Çoğaltmalar genelinde en fazla gecikme|Bayt|Maksimum|En fazla çoğaltma çoğaltmasının bayt cinsinden gecikmesi|Boyut yok|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|cpu_percent|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|Boyut yok|
|memory_percent|Bellek yüzdesi|Yüzde|Ortalama|Bellek yüzdesi|Boyut yok|
|'ye|IOPS|Sayı|Ortalama|GÇ Işlemi/saniye|Boyut yok|
|storage_percent|Depolama alanı yüzdesi|Yüzde|Ortalama|Depolama alanı yüzdesi|Boyut yok|
|storage_used|Kullanılan depolama|Bayt|Ortalama|Kullanılan depolama|Boyut yok|
|active_connections|Etkin bağlantılar|Sayı|Ortalama|Etkin bağlantılar|Boyut yok|
|network_bytes_egress|Ağ Çıkışı|Bayt|Toplam|Etkin bağlantılar arasında ağ çıkışı|Boyut yok|
|network_bytes_ingress|Ağ Girişi|Bayt|Toplam|Etkin bağlantılar genelinde ağ|Boyut yok|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/ıothubs

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetri iletisi gönderme denemeleri|Sayı|Toplam|IoT Hub 'ınıza gönderilmeye çalışılan cihazdan buluta telemetri iletisi sayısı|Boyut yok|
|D2C. telemetri. giriş. başarılı|Gönderilen telemetri iletileri|Sayı|Toplam|IoT Hub 'ınıza başarıyla gönderilen cihazdan buluta telemetri iletisi sayısı|Boyut yok|
|c2d.commands.egress.complete.success|Komutlar tamamlandı|Sayı|Toplam|Cihaz tarafından başarıyla tamamlanan buluttan cihaza yönelik komutların sayısı|Boyut yok|
|c2d.commands.egress.abandon.success|Komutlar bırakıldı|Sayı|Toplam|Cihaz tarafından bırakılan buluttan cihaza yönelik komutların sayısı|Boyut yok|
|c2d.commands.egress.reject.success|Reddedilen komutlar|Sayı|Toplam|Cihaz tarafından reddedilen buluttan cihaza yönelik komutların sayısı|Boyut yok|
|Devices. totalDevices|Toplam cihaz (kullanım dışı)|Sayı|Toplam|IoT Hub 'ınıza kayıtlı cihazların sayısı|Boyut yok|
|devices.connectedDevices.allProtocol|Bağlı cihazlar (kullanım dışı) |Sayı|Toplam|IoT Hub 'ınıza bağlı cihazların sayısı|Boyut yok|
|D2C. telemetri. çıkış. başarılı|Yönlendirme: teslim edilen telemetri iletileri|Sayı|Toplam|IoT Hub yönlendirme kullanılarak iletilerin tüm uç noktalara başarıyla ulaştırılan ileti sayısı. Bir ileti birden çok uç noktaya yönlendirilmemişse, her başarılı teslimat için bu değer bir artar. Bir ileti aynı uç noktaya birden çok kez teslim edildiğinde, her başarılı teslimat için bu değer bir artar.|Boyut yok|
|D2C. telemetri. çıkış. bırakıldı|Yönlendirme: telemetri iletileri bırakıldı |Sayı|Toplam|İleti, ölü uç noktalar nedeniyle IoT Hub yönlendirmenin kaç kez bırakılmakta olduğunu. Bu değer, bırakılan iletiler buraya teslim edimediğinden geri dönüş rotasına teslim edilen iletileri saymaz.|Boyut yok|
|d2c.telemetry.egress.orphaned|Yönlendirme: telemetri iletileri yalnız bırakılmış |Sayı|Toplam|Herhangi bir yönlendirme kuralıyla eşleşmediğinden (geri dönüş kuralı dahil) iletilerin IoT Hub yönlendirme tarafından yalnız bırakılmış olduğu zaman sayısı. |Boyut yok|
|D2C. telemetri. çıkış. geçersiz|Yönlendirme: telemetri iletileri uyumsuz|Sayı|Toplam|Uç nokta ile uyumsuzluk nedeniyle IoT Hub yönlendirmenin iletileri teslim etme başarısız olduğu zaman sayısı. Bu değer yeniden denemeler içermez.|Boyut yok|
|d2c.telemetry.egress.fallback|Yönlendirme: geri dönüşe teslim edilen iletiler|Sayı|Toplam|Geri dönüş rotası ile ilişkili uç noktaya gönderilen iletileri yönlendirmenin IoT Hub sayısı.|Boyut yok|
|D2C. endpoints. çıkış. eventHubs|Yönlendirme: Olay Hub 'ına teslim edilen iletiler|Sayı|Toplam|IoT Hub yönlendirmenin Olay Hub 'ı uç noktalarına başarıyla ileti teslim eden zaman sayısı.|Boyut yok|
|D2C. endpoints. Latency. eventHubs|Yönlendirme: Olay Hub 'ı için ileti gecikmesi|Milisaniye|Ortalama|İleti girişi ile bir olay hub 'ı uç noktasına giriş IoT Hub ve ileti girişi arasındaki ortalama gecikme (milisaniye).|Boyut yok|
|d2c.endpoints.egress.serviceBusQueues|Yönlendirme: Service Bus kuyruğuna teslim edilen iletiler|Sayı|Toplam|IoT Hub yönlendirmenin iletileri Service Bus sıra uç noktalarına başarıyla teslim dağıttığı zaman sayısı.|Boyut yok|
|d2c.endpoints.latency.serviceBusQueues|Yönlendirme: Service Bus kuyruğu için ileti gecikmesi|Milisaniye|Ortalama|İleti girişi IoT Hub ve telemetri iletisi arasındaki ortalama gecikme süresi (milisaniye) Service Bus kuyruğu uç noktasına giriş.|Boyut yok|
|d2c.endpoints.egress.serviceBusTopics|Yönlendirme: Service Bus konuya teslim edilen iletiler|Sayı|Toplam|IoT Hub yönlendirmenin Service Bus konu uç noktalarına başarıyla ileti teslim eden zaman sayısı.|Boyut yok|
|d2c.endpoints.latency.serviceBusTopics|Yönlendirme: Service Bus konusu için ileti gecikmesi|Milisaniye|Ortalama|İleti girişi IoT Hub ve telemetri iletisi arasındaki ortalama gecikme süresi (milisaniye) Service Bus konu uç noktasına giriş.|Boyut yok|
|D2C. endpoints. çıkış. builtIn. Events|Yönlendirme: iletilere/olaylara teslim edilen iletiler|Sayı|Toplam|Yönlendirmenin, yerleşik uç noktaya (iletiler/olaylar) başarıyla ileti teslim IoT Hub sayısı. Bu ölçüm yalnızca yönlendirme etkinleştirildiğinde (https://aka.ms/iotrouting) IoT Hub 'ı için) çalışmaya başlar.|Boyut yok|
|D2C. endpoints. Latency. yerleik. Events|Yönlendirme: iletiler/olaylar için ileti gecikmesi|Milisaniye|Ortalama|Yerleşik uç noktaya (iletiler/olaylar) gelen ileti girişi IoT Hub ve telemetri iletisi arasındaki ortalama gecikme süresi (milisaniye). Bu ölçüm yalnızca yönlendirme etkinleştirildiğinde (https://aka.ms/iotrouting) IoT Hub 'ı için) çalışmaya başlar.|Boyut yok|
|D2C. endpoints. çıkış. Storage|Yönlendirme: depolamaya teslim edilen iletiler|Sayı|Toplam|IoT Hub yönlendirmenin depolama uç noktalarına başarıyla ileti teslim eden zaman sayısı.|Boyut yok|
|D2C. endpoints. Latency. Storage|Yönlendirme: depolama için ileti gecikmesi|Milisaniye|Ortalama|İleti girişi IoT Hub ve telemetri iletisine bir depolama uç noktasına giriş arasındaki ortalama gecikme süresi (milisaniye).|Boyut yok|
|D2C. endpoints. çıkış. Storage. Bytes|Yönlendirme: depolamaya teslim edilen veriler|Bayt|Toplam|Depolama uç noktalarına teslim edilen veri miktarı (bayt) IoT Hub.|Boyut yok|
|D2C. endpoints. çıkış. Storage. blob 'ları|Yönlendirme: depolamaya teslim edilen Bloblar|Sayı|Toplam|IoT Hub blob 'ları depolama uç noktalarına yönlendirmenin kaç kez yönlendirtığı sayısıdır.|Boyut yok|
|Eventgridteslimler|Event Grid teslimler (Önizleme)|Sayı|Toplam|Event Grid yayımlanan IoT Hub olaylarının sayısı. Başarılı ve başarısız isteklerin sayısı için sonuç boyutunu kullanın. EventType boyutu olay türünü gösterir (https://aka.ms/ioteventgrid).|Sonuç, EventType|
|EventGridLatency|Olay Event Grid yayımlandığında IoT Hub olayının oluşturulduğu sürenin ortalama gecikme süresi (milisaniye). Bu sayı, tüm olay türleri arasındaki bir ortalama değer. Belirli bir olay türünün gecikmesini görmek için EventType boyutunu kullanın.|Türü|
|d2c.twin.read.success|Cihazlardan başarılı ikizi okumaları|Sayı|Toplam|Tüm başarılı cihaz tarafından başlatılan ikizi okuma sayısı.|Boyut yok|
|d2c.twin.read.failure|Cihazlardan başarısız ikizi okumaları|Sayı|Toplam|Tüm başarısız cihaz tarafından başlatılan ikizi okumaların sayısı.|Boyut yok|
|D2C. ikizi. Read. size|Cihazlardan gelen ikizi okumaların yanıt boyutu|Bayt|Ortalama|Tüm başarılı cihaz tarafından başlatılan ikizi okumalarının ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|D2C. ikizi. Update. Success|Cihazlardan başarılı ikizi güncelleştirmeleri|Sayı|Toplam|Tüm başarılı cihaz tarafından başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|d2c.twin.update.failure|Cihazlardan ikizi güncelleştirmeleri başarısız oldu|Sayı|Toplam|Tüm başarısız cihaz tarafından başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|d2c.twin.update.size|Cihazlardan ikizi güncelleştirmelerinin boyutu|Bayt|Ortalama|Tüm başarılı cihaz tarafından başlatılan ikizi güncelleştirmelerinin ortalama, en düşük ve en büyük boyutu.|Boyut yok|
|c2d.methods.success|Başarılı doğrudan yöntem etkinleştirmeleri|Sayı|Toplam|Tüm başarılı doğrudan yöntem çağrılarının sayısı.|Boyut yok|
|C2D. Methods. Failure|Başarısız doğrudan yöntem etkinleştirmeleri|Sayı|Toplam|Tüm başarısız doğrudan yöntem çağrılarının sayısı.|Boyut yok|
|c2d.methods.requestSize|Doğrudan yöntem etkinleştirmeleri istek boyutu|Bayt|Ortalama|Tüm başarılı doğrudan yöntem isteklerinin ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|c2d.methods.responseSize|Doğrudan yöntem etkinleştirmeleri yanıt boyutu|Bayt|Ortalama|Tüm başarılı doğrudan yöntem yanıtlarının ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|c2d.twin.read.success|Arka uçtan başarılı ikizi okumaları|Sayı|Toplam|Tüm başarılı arka uç tarafından başlatılan ikizi okumaların sayısı.|Boyut yok|
|C2D. ikizi. Read. Failure|Arka uçtan başarısız ikizi okumaları|Sayı|Toplam|Tüm başarısız arka uç tarafından başlatılan ikizi okumaların sayısı.|Boyut yok|
|C2D. ikizi. Read. size|Arka uçtan gelen ikizi okumaların yanıt boyutu|Bayt|Ortalama|Tüm başarılı arka uç başlatılan ikizi okumalarının ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|C2D. ikizi. Update. Success|Arka uçtan başarılı ikizi güncelleştirmeleri|Sayı|Toplam|Tüm başarılı arka uç ile başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|C2D. ikizi. Update. Failure|Arka uçtan başarısız ikizi güncelleştirmeleri|Sayı|Toplam|Tüm başarısız arka uç başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|c2d.twin.update.size|Arka uçtan ikizi güncelleştirmelerinin boyutu|Bayt|Ortalama|Tüm başarılı arka uç ile başlatılan ikizi güncelleştirmelerinin ortalama, en düşük ve en büyük boyutu.|Boyut yok|
|twinQueries. Success|Başarılı ikizi sorguları|Sayı|Toplam|Tüm başarılı ikizi sorgularının sayısı.|Boyut yok|
|twinQueries. Failure|Başarısız ikizi sorguları|Sayı|Toplam|Tüm başarısız ikizi sorgularının sayısı.|Boyut yok|
|twinQueries.resultSize|İkizi sorguları sonuç boyutu|Bayt|Ortalama|Tüm başarılı ikizi sorgularının sonuç boyutunun ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|jobs.createTwinUpdateJob.success|İkizi Update işlerinin başarılı oluşturmaları|Sayı|Toplam|Tüm başarılı ikizi Update işlerinin oluşturma sayısı.|Boyut yok|
|jobs.createTwinUpdateJob.failure|İkizi Update işlerinin başarısız oluşturmaları|Sayı|Toplam|İkizi Update işlerinin başarısız olan tüm oluşturma sayısı.|Boyut yok|
|Jobs. createDirectMethodJob. Success|Yöntem çağırma işlerinin başarılı oluşturmaları|Sayı|Toplam|Tüm başarılı doğrudan yöntem çağırma işlerinin oluşturulma sayısı.|Boyut yok|
|Jobs. createDirectMethodJob. Failure|Yöntem çağırma işlerinin oluşturma işlemi başarısız oldu|Sayı|Toplam|Doğrudan yöntem çağırma işlerinin tüm başarısız oluşturma sayısı.|Boyut yok|
|işler. listJobs. başarılı|İşleri listelemek için başarılı çağrılar|Sayı|Toplam|İşleri listelemek için başarılı olan tüm çağrıların sayısı.|Boyut yok|
|Jobs. listJobs. hata|İşleri listelemek için başarısız çağrılar|Sayı|Toplam|Listeleme işleri için başarısız olan tüm çağrıların sayısı.|Boyut yok|
|Jobs. cancelJob. Success|Başarılı iş iptalleri|Sayı|Toplam|İşi iptal etmek için tüm başarılı çağrı sayısı.|Boyut yok|
|Jobs. cancelJob. Failure|Başarısız iş iptalleri|Sayı|Toplam|İşi iptal etmek için başarısız olan tüm çağrıların sayısı.|Boyut yok|
|işler. queryJobs. başarılı|Başarılı iş sorguları|Sayı|Toplam|Sorgu işlerine yapılan tüm başarılı çağrıların sayısı.|Boyut yok|
|Jobs. queryJobs. hata|Başarısız iş sorguları|Sayı|Toplam|Sorgu işlerine yapılan tüm başarısız çağrıların sayısı.|Boyut yok|
|işler. tamamlandı|Tamamlanan İşler|Sayı|Toplam|Tamamlanan tüm işlerin sayısı.|Boyut yok|
|işler. başarısız|Başarısız işler|Sayı|Toplam|Başarısız olan tüm işlerin sayısı.|Boyut yok|
|d2c.telemetry.ingress.sendThrottle|Daraltma hatası sayısı|Sayı|Toplam|Cihaz performansı azaltmasından kaynaklanan azaltma hatası sayısı|Boyut yok|
|Günlükmessagequotakullanıldı|Kullanılan toplam ileti sayısı|Sayı|Ortalama|Bugün kullanılan toplam ileti sayısı. Bu, her gün 00:00 UTC tarihinde sıfıra sıfırlanan toplu bir değerdir.|Boyut yok|
|deviceDataUsage|Toplam cihaz verisi kullanımı|Bayt|Toplam|Iothub 'e bağlı tüm cihazlardan ve bunlardan aktarılan baytlar|Boyut yok|
|totalDeviceCount|Toplam cihaz (Önizleme)|Sayı|Ortalama|IoT Hub 'ınıza kayıtlı cihazların sayısı|Boyut yok|
|connectedDeviceCount|Bağlı cihazlar (Önizleme)|Sayı|Ortalama|IoT Hub 'ınıza bağlı cihazların sayısı|Boyut yok|
|konfigürasyonları|Yapılandırma ölçümleri|Sayı|Toplam|Yapılandırma Işlemleri için ölçümler|Boyut yok|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Registrationdenemeleri|Kayıt denemeleri|Sayı|Toplam|Denenen cihaz kaydı sayısı|ProvisioningServiceName, IotHubName, durum|
|Deviceasyleri|Atanan cihazlar|Sayı|Toplam|IoT Hub 'ına atanan cihazların sayısı|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Kanıtlama denemeleri|Sayı|Toplam|Denenen cihaz belirlediğimizi karşıladığımızı sayısı|ProvisioningServiceName, durum, protokol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|AvailableStorage|Kullanılabilir depolama alanı|Bayt|Toplam|5 dakika ayrıntı düzeyinde raporlanan toplam kullanılabilir depolama alanı|CollectionName, DatabaseName, bölgesi|
|Cassandraconnectionkapanışları|Cassandra bağlantı kapanışları|Sayı|Toplam|1 dakikalık ayrıntı düzeyinde raporlanan, kapatılan Cassandra bağlantısı sayısı|Bölge, ClosureReason|
|Cassandrarequestücretleri|Cassandra Istek ücretleri|Sayı|Toplam|Cassandra istekleri için tüketilen RUs|DatabaseName, CollectionName, bölge, OperationType, ResourceType|
|CassandraRequests|Cassandra Istekleri|Sayı|Sayı|Yapılan Cassandra isteği sayısı|DatabaseName, CollectionName, bölge, OperationType, ResourceType, ErrorCode|
|Veri kullanımı|Veri Kullanımı|Bayt|Toplam|5 dakika ayrıntı düzeyinde raporlanan toplam veri kullanımı|CollectionName, DatabaseName, bölgesi|
|DocumentCount|Belge sayısı|Sayı|Toplam|5 dakika ayrıntı düzeyinde raporlanan toplam belge sayısı|CollectionName, DatabaseName, bölgesi|
|DocumentQuota|Belge kotası|Bayt|Toplam|5 dakika ayrıntı düzeyinde raporlanan toplam depolama kotası|CollectionName, DatabaseName, bölgesi|
|Indexusage|Dizin kullanımı|Bayt|Toplam|5 dakika ayrıntı düzeyi olarak raporlanan toplam dizin kullanımı|CollectionName, DatabaseName, bölgesi|
|Meta veri datarequests|Meta veri Istekleri|Sayı|Sayı|Meta veri isteklerinin sayısı. Cosmos DB, her hesap için sistem meta veri toplamayı korur, bu da koleksiyonları, veritabanlarını, vb. ve bunların yapılandırmalarının ücretsiz olarak numaralandırılmasını sağlar.|DatabaseName, CollectionName, bölge, StatusCode, |
|MongoRequestCharge|Mongo Istek ücreti|Sayı|Toplam|Tüketilen Mongo Istek birimleri|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Mongo Istekleri|Sayı|Sayı|Yapılan Mongo Isteği sayısı|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|Provisionedüretilen Iş|Sağlanan İşleme Hızı|Sayı|Maksimum|Sağlanan İşleme Hızı|DatabaseName, CollectionName|
|ReplicationLatency|P99 çoğaltma gecikmesi|Mayacak|Ortalama|Coğrafi olarak etkinleştirilen hesap için kaynak ve hedef bölgelerde P99 çoğaltma gecikme süresi|SourceRegion, TargetRegion|
|ServiceAvailability|Hizmet kullanılabilirliği|Yüzde|Ortalama|Hesap istekleri bir saat, gün veya ay ayrıntı düzeyinde kullanılabilirlik|Boyut yok|
|TotalRequestUnits|Toplam Istek birimleri|Sayı|Toplam|Tüketilen istek birimleri|DatabaseName, CollectionName, bölge, StatusCode, OperationType|
|Toplam Istek sayısı|Toplam İstek Sayısı|Sayı|Sayı|Yapılan istek sayısı|DatabaseName, CollectionName, bölge, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/konuları

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Publishbaşarılı sayısı|Yayımlanan olaylar|Sayı|Toplam|Bu konuda yayınlanan toplam olay sayısı|Boyut yok|
|PublishFailCount|Başarısız olayları Yayımla|Sayı|Toplam|Bu konuda yayımlanamadı toplam olay sayısı|ErrorType, hata|
|UnmatchedEventCount|Eşleşmeyen olaylar|Sayı|Toplam|Bu konu için olay abonelikleriyle eşleşmeyen toplam olay sayısı|Boyut yok|
|Publishbaşarıyla Latencınms|Yayımlama başarı gecikmesi|Sayı|Toplam|Milisaniye cinsinden yayımlama başarısı gecikmesi|Boyut yok|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/Eventabonelikleri

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|MatchedEventCount|Eşleşen olaylar|Sayı|Toplam|Bu olay aboneliğiyle eşleşen toplam olay sayısı|Boyut yok|
|Ityattemptfailcount|Teslim başarısız olaylar|Sayı|Toplam|Bu olay aboneliğine teslim edilemedi toplam olay sayısı|Hata, ErrorType|
|Deliverybaşarılı sayısı|Teslim edilen olaylar|Sayı|Toplam|Bu olay aboneliğine teslim edilen toplam olay sayısı|Boyut yok|
|DestinationProcessingDurationInMs|Hedef Işleme süresi|Milisaniye|Ortalama|Milisaniye cinsinden hedef işleme süresi|Boyut yok|
|DroppedEventCount|Bırakılan olaylar|Sayı|Toplam|Bu olay aboneliğiyle eşleşen toplam bırakılan olay sayısı|DropReason|
|DeadLetteredCount|Kullanılmayan olaylar|Sayı|Toplam|Bu olay aboneliğiyle eşleşen toplam atılacak olay sayısı|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/Extensionkonuları

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Publishbaşarılı sayısı|Yayımlanan olaylar|Sayı|Toplam|Bu konuda yayınlanan toplam olay sayısı|Boyut yok|
|PublishFailCount|Başarısız olaylar|Sayı|Toplam|Bu konuda yayımlanamadı toplam olay sayısı|ErrorType, hata|
|UnmatchedEventCount|Eşleşmeyen olaylar|Sayı|Toplam|Bu konu için olay abonelikleriyle eşleşmeyen toplam olay sayısı|Boyut yok|
|Publishbaşarıyla Latencınms|Yayımlama başarı gecikmesi|Sayı|Toplam|Milisaniye cinsinden yayımlama başarısı gecikmesi|Boyut yok|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|SuccessfulRequests|Başarılı Istekler|Sayı|Toplam|Microsoft. EventHub için başarılı Istekler.|EntityName |
|ServerErrors|Sunucu hataları.|Sayı|Toplam|Microsoft. EventHub için sunucu hataları.|EntityName |
|UserErrors|Kullanıcı hataları.|Sayı|Toplam|Microsoft. EventHub için Kullanıcı hataları.|EntityName |
|QuotaExceededErrors|Kota, hataları aştı.|Sayı|Toplam|Microsoft. EventHub için Kota aşıldı.|EntityName |
|OperationName|Kısıtlanmış Istekler.|Sayı|Toplam|Microsoft. EventHub için kısıtlanan Istekler.|EntityName |
|Incomingrequests|Gelen Istekler|Sayı|Toplam|Microsoft. EventHub için gelen Istekler.|EntityName|
|Incomingmessages|Gelen Iletiler|Sayı|Toplam|Microsoft. EventHub için gelen Iletiler.|EntityName|
|OutgoingMessages|Giden Iletiler|Sayı|Toplam|Microsoft. EventHub için giden Iletiler.|EntityName|
|Incomingbytes|Gelen bayt sayısı.|Bayt|Toplam|Microsoft. EventHub için gelen baytlar.|EntityName|
|OutgoingBytes|Giden bayt sayısı.|Bayt|Toplam|Microsoft. EventHub için giden baytlar.|EntityName|
|ActiveConnection sayısı|ActiveConnection sayısı|Sayı|Ortalama|Microsoft. EventHub için toplam etkin bağlantı sayısı.|Boyut yok|
|ConnectionsOpened|Bağlantılar açıldı.|Sayı|Ortalama|Microsoft. EventHub için açılan bağlantılar.|EntityName|
|ConnectionsClosed|Bağlantı kapatıldı.|Sayı|Ortalama|Microsoft. EventHub için kapatılan bağlantılar.|EntityName|
|CaptureBacklog|Biriktirme listesini yakala.|Sayı|Toplam|Microsoft. EventHub için yakalama biriktirme listesi.|EntityName|
|CapturedMessages|Yakalanan Iletiler.|Sayı|Toplam|Microsoft. EventHub için yakalanan Iletiler.|EntityName|
|CapturedBytes|Yakalanan baytlar.|Bayt|Toplam|Microsoft. EventHub için yakalanan baytlar.|EntityName|
|Boyut|Boyut|Bayt|Ortalama|Bir EventHub 'ın bayt cinsinden boyutu.|EntityName|
|INREQS|Gelen Istekler (kullanım dışı)|Sayı|Toplam|Bir ad alanı için toplam gelen gönderme isteği sayısı (kullanım dışı)|Boyut yok|
|SUCCREQ|Başarılı Istekler (kullanım dışı)|Sayı|Toplam|Bir ad alanı için toplam başarılı istek (kullanım dışı)|Boyut yok|
|FAILREQ|Başarısız Istekler (kullanım dışı)|Sayı|Toplam|Bir ad alanı için toplam başarısız istek (kullanım dışı)|Boyut yok|
|SVRBSY|Sunucu meşgul hataları (kullanım dışı)|Sayı|Toplam|Bir ad alanı için toplam sunucu meşgul hatası (kullanım dışı)|Boyut yok|
|INTERR|İç sunucu hataları (kullanım dışı)|Sayı|Toplam|Bir ad alanı için toplam iç sunucu hatası (kullanım dışı)|Boyut yok|
|Hatalı cerr|Diğer hatalar (kullanım dışı)|Sayı|Toplam|Bir ad alanı için toplam başarısız istek (kullanım dışı)|Boyut yok|
|Giriş iletileri|Gelen Iletiler (kullanım dışı) (kullanım dışı)|Sayı|Toplam|Bir ad alanı için gelen toplam ileti sayısı. Bu ölçüm kullanım dışıdır. Lütfen bunun yerine gelen Iletiler ölçümünü kullanın (kullanım dışı)|Boyut yok|
|EHINILETISI|Gelen Iletiler (kullanım dışı)|Sayı|Toplam|Bir ad alanı için toplam gelen ileti (kullanım dışı)|Boyut yok|
|Dış mesaj|Giden Iletiler (kullanım dışı) (kullanım dışı)|Sayı|Toplam|Bir ad alanı için toplam giden ileti. Bu ölçüm kullanım dışıdır. Lütfen bunun yerine giden Iletiler ölçümünü kullanın (kullanım dışı)|Boyut yok|
|EHODıKILETISI|Giden Iletiler (kullanım dışı)|Sayı|Toplam|Bir ad alanı için toplam giden ileti (kullanım dışı)|Boyut yok|
|EHINMB|Gelen bayt (kullanım dışı) (kullanım dışı)|Bayt|Toplam|Bir ad alanı için Olay Hub 'ı gelen ileti işleme. Bu ölçüm kullanım dışıdır. Lütfen bunun yerine gelen bayt ölçümünü kullanın (kullanım dışı)|Boyut yok|
|EHINBYTES|Gelen baytlar (kullanım dışı)|Bayt|Toplam|Ad alanı için Olay Hub 'ı gelen ileti işleme (kullanım dışı)|Boyut yok|
|EHOUTMB|Giden baytlar (kullanım dışı) (kullanım dışı)|Bayt|Toplam|Bir ad alanı için Olay Hub 'ı giden ileti işleme. Bu ölçüm kullanım dışıdır. Lütfen bunun yerine giden bayt ölçümünü kullanın (kullanım dışı)|Boyut yok|
|EHOUTBYTES|Giden baytlar (kullanım dışı)|Bayt|Toplam|Bir ad alanı için Olay Hub 'ı giden ileti işleme (kullanım dışı)|Boyut yok|
|EHABL|Biriktirme listesi iletilerini Arşivle (kullanım dışı)|Sayı|Toplam|Ad alanı için biriktirme listesindeki Olay Hub 'ı arşiv iletileri (kullanım dışı)|Boyut yok|
|EHAMSGS|İletileri Arşivle (kullanım dışı)|Sayı|Toplam|Bir ad alanında olay hub 'ı arşivlenmiş iletiler (kullanım dışı)|Boyut yok|
|EHAMB|Arşiv iletisi verimlilik (kullanım dışı)|Bayt|Toplam|Bir ad alanında olay hub 'ı arşivlenmiş ileti işleme (kullanım dışı)|Boyut yok|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/kümeler

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|SuccessfulRequests|Başarılı Istekler (Önizleme)|Sayı|Toplam|Microsoft. EventHub için başarılı Istekler. (Önizleme)|Boyut yok|
|ServerErrors|Sunucu hataları. (Önizleme)|Sayı|Toplam|Microsoft. EventHub için sunucu hataları. (Önizleme)|Boyut yok|
|UserErrors|Kullanıcı hataları. (Önizleme)|Sayı|Toplam|Microsoft. EventHub için Kullanıcı hataları. (Önizleme)|Boyut yok|
|QuotaExceededErrors|Kota, hataları aştı. (Önizleme)|Sayı|Toplam|Microsoft. EventHub için Kota aşıldı. (Önizleme)|Boyut yok|
|OperationName|Kısıtlanmış Istekler. (Önizleme)|Sayı|Toplam|Microsoft. EventHub için kısıtlanan Istekler. (Önizleme)|Boyut yok|
|Incomingrequests|Gelen Istekler (Önizleme)|Sayı|Toplam|Microsoft. EventHub için gelen Istekler. (Önizleme)|Boyut yok|
|Incomingmessages|Gelen Iletiler (Önizleme)|Sayı|Toplam|Microsoft. EventHub için gelen Iletiler. (Önizleme)|Boyut yok|
|OutgoingMessages|Giden Iletiler (Önizleme)|Sayı|Toplam|Microsoft. EventHub için giden Iletiler. (Önizleme)|Boyut yok|
|Incomingbytes|Gelen bayt sayısı. (Önizleme)|Bayt|Toplam|Microsoft. EventHub için gelen baytlar. (Önizleme)|Boyut yok|
|OutgoingBytes|Giden bayt sayısı. (Önizleme)|Bayt|Toplam|Microsoft. EventHub için giden baytlar. (Önizleme)|Boyut yok|
|ActiveConnection sayısı|ActiveConnections (Önizleme)|Sayı|Ortalama|Microsoft. EventHub için toplam etkin bağlantı sayısı. (Önizleme)|Boyut yok|
|ConnectionsOpened|Bağlantılar açıldı. (Önizleme)|Sayı|Ortalama|Microsoft. EventHub için açılan bağlantılar. (Önizleme)|Boyut yok|
|ConnectionsClosed|Bağlantı kapatıldı. (Önizleme)|Sayı|Ortalama|Microsoft. EventHub için kapatılan bağlantılar. (Önizleme)|Boyut yok|
|CaptureBacklog|Biriktirme listesini yakala. (Önizleme)|Sayı|Toplam|Microsoft. EventHub için yakalama biriktirme listesi. (Önizleme)|Boyut yok|
|CapturedMessages|Yakalanan Iletiler. (Önizleme)|Sayı|Toplam|Microsoft. EventHub için yakalanan Iletiler. (Önizleme)|Boyut yok|
|CapturedBytes|Yakalanan baytlar. (Önizleme)|Bayt|Toplam|Microsoft. EventHub için yakalanan baytlar. (Önizleme)|Boyut yok|
|CPU|CPU (Önizleme)|Yüzde|Maksimum|Bir yüzde olarak olay hub 'ı kümesine yönelik CPU kullanımı|Rol|
|Kullanılabilirlik Blememory|Kullanılabilir bellek (Önizleme)|Sayı|Maksimum|Event hub kümesi için bayt cinsinden kullanılabilir bellek|Rol|

## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/kümeler

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|GatewayRequests|Ağ Geçidi Istekleri|Sayı|Toplam|Ağ Geçidi isteklerinin sayısı|ClusterDnsName, HttpStatus|
|Kategorizedgatewayrequests|Kategorilere ayrılmış ağ geçidi Istekleri|Sayı|Toplam|Kategorilere göre ağ geçidi isteklerinin sayısı (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|Numactiveçalışanları|Etkin çalışan sayısı|Sayı|Maksimum|Etkin çalışan sayısı|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/oto Scalesettings

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|ObservedMetricValue|Gözlemlenen ölçüm değeri|Sayı|Ortalama|Yürütüldüğünde otomatik ölçeklendirme tarafından hesaplanan değer|MetricTriggerSource|
|MetricThreshold|Ölçüm eşiği|Sayı|Ortalama|Otomatik ölçeklendirme çalıştırıldığında yapılandırılan otomatik ölçeklendirme eşiği.|MetricTriggerRule|
|ObservedCapacity|Gözlenen kapasite|Sayı|Ortalama|Yürütüldüğünde otomatik ölçeklendirme olarak bildirilen kapasite.|Boyut yok|
|Scaleactionsınıated|Ölçek eylemleri başlatıldı|Sayı|Toplam|Ölçek işleminin yönü.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/bileşenler

(Genel Önizleme)

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Kullanılabilirlik sonuçları/kullanılabilirliği yüzdesi|Kullanılabilirlik|Yüzde|Ortalama|Başarıyla tamamlanan kullanılabilirlik testlerinin yüzdesi|Kullanılabilirlik sonucu/adı, kullanılabilirlik sonucu/konumu|
|Kullanılabilirlik sonuçları/sayısı|Kullanılabilirlik testleri|Sayı|Sayı|Kullanılabilirlik testlerinin sayısı|Kullanılabilirlik sonucu/adı, kullanılabilirlik sonucu/konumu, kullanılabilirlik sonucu/başarısı|
|Kullanılabilirlik sonuçları/süresi|Kullanılabilirlik testi süresi|Mayacak|Ortalama|Kullanılabilirlik testi süresi|Kullanılabilirlik sonucu/adı, kullanılabilirlik sonucu/konumu, kullanılabilirlik sonucu/başarısı|
|Browserzamanlamalar/networkDuration|Sayfa yükleme ağ bağlantı süresi|Mayacak|Ortalama|Kullanıcı isteği ile ağ bağlantısı arasındaki süre. DNS arama ve aktarım bağlantısı içerir.|Boyut yok|
|Browserzamanlamalar/processingDuration|İstemci işlem süresi|Mayacak|Ortalama|DOM yükleninceye kadar belgenin son baytını alma arasındaki süre. Zaman uyumsuz istekler hala işlenmeye devam edebilir.|Boyut yok|
|Browserzamanlamalar/receiveDuration|Yanıt süresini alma|Mayacak|Ortalama|İlk ve son bayt veya bağlantının kesilmesi arasındaki süre.|Boyut yok|
|Browserzamanlamalar/sendDuration|İstek gönderme süresi|Mayacak|Ortalama|Ağ bağlantısı ve ilk baytın alınması arasındaki süre.|Boyut yok|
|Browserzamanlamalar/totalDuration|Tarayıcı sayfa yükleme süresi|Mayacak|Ortalama|DOM, stil sayfaları, betikler ve görüntüler yükleninceye kadar Kullanıcı isteğinden geçen süre.|Boyut yok|
|Bağımlılıklar/sayı|Bağımlılık çağrıları|Sayı|Sayı|Uygulama tarafından dış kaynaklara yapılan çağrıların sayısı.|bağımlılık/tür, bağımlılık/performanceBucket, bağımlılık/başarı, işlem/yapay, Cloud/Roleınstance, Cloud/roleName|
|Bağımlılıklar/süre|Bağımlılık süresi|Mayacak|Ortalama|Uygulama tarafından dış kaynaklara yapılan çağrıların süresi.|bağımlılık/tür, bağımlılık/performanceBucket, bağımlılık/başarı, işlem/yapay, Cloud/Roleınstance, Cloud/roleName|
|Bağımlılıklar/başarısız|Bağımlılık çağrısı sorunları|Sayı|Sayı|Uygulama tarafından dış kaynaklara yapılan başarısız bağımlılık çağrılarının sayısı.|bağımlılık/tür, bağımlılık/performanceBucket, işlem/yapay, Cloud/Roleınstance, Cloud/roleName|
|pageViews/Count|Sayfa görünümleri|Sayı|Sayı|Sayfa görüntüleme sayısı.|işlem/yapay|
|pageViews/Duration|Sayfa görünümü yükleme süresi|Mayacak|Ortalama|Sayfa görünümü yükleme süresi|işlem/yapay|
|performanceCounters/requestExecutionTime|HTTP isteği yürütme süresi|Mayacak|Ortalama|En son isteğin yürütme süresi.|Cloud/Roleınstance|
|performanceCounters/Requestsınqueue|Uygulama kuyruğundaki HTTP istekleri|Sayı|Ortalama|Uygulama isteği sırasının uzunluğu.|Cloud/Roleınstance|
|performanceCounters/requestsPerSecond|HTTP istek hızı|Sayaçpersaniye|Ortalama|ASP.NET ' den saniyede uygulamaya yapılan tüm isteklerin oranı.|Cloud/Roleınstance|
|performanceCounters/exceptionsPerSecond|Özel durum oranı|Sayaçpersaniye|Ortalama|.NET özel durumları ve .NET özel durumlarına dönüştürülmüş yönetilmeyen özel durumlar da dahil olmak üzere, Windows 'a bildirilen işlenmiş ve işlenmemiş özel durumların sayısı.|Cloud/Roleınstance|
|performanceCounters/Processıobitespersecond|İşlem GÇ oranı|BytesPerSecond|Ortalama|Dakikada, ağda ve cihazlarda okunan ve yazılan toplam bayt/saniye.|Cloud/Roleınstance|
|performanceCounters/processCpuPercentage|İşlem CPU 'SU|Yüzde|Ortalama|Tüm işlem iş parçacıklarının yönergeleri yürütmek için işlemciyi kullandığı geçen sürenin yüzdesi. Bu, 0 ila 100 arasında farklılık gösterebilir. Bu ölçüm, W3wp sürecinin performansını tek başına gösterir.|Cloud/Roleınstance|
|performanceCounters/Processorcpuyüzdesi|İşlemci zamanı|Yüzde|Ortalama|İşlemcinin boş olmayan iş parçacıklarında harcadığı sürenin yüzdesi.|Cloud/Roleınstance|
|performanceCounters/memoryAvailableBytes|Available memory|Bayt|Ortalama|Bir işleme veya sistem kullanımına ayırmak için hemen kullanılabilir fiziksel bellek.|Cloud/Roleınstance|
|performanceCounters/processPrivateBytes|İşlem özel baytları|Bayt|Ortalama|İzlenen uygulamanın işlemlerine özel olarak atanan bellek.|Cloud/Roleınstance|
|istek/süre|Sunucu yanıt süresi|Mayacak|Ortalama|Bir HTTP isteği alma ve yanıtın gönderilmesini tamamlama arasındaki süre.|istek/performanceBucket, istek/resultCode, işlem/yapay, Cloud/Roleınstance, istek/başarı, bulut/roleName|
|istek/sayı|Sunucu istekleri|Sayı|Sayı|Tamamlanan HTTP isteği sayısı.|istek/performanceBucket, istek/resultCode, işlem/yapay, Cloud/Roleınstance, istek/başarı, bulut/roleName|
|istek/başarısız|Başarısız istekler|Sayı|Sayı|Başarısız olarak işaretlenen HTTP isteklerinin sayısı. Çoğu durumda bunlar > = 400 yanıt koduna sahip isteklerdir ve 401 ' e eşit değildir.|istek/performanceBucket, istek/resultCode, işlem/yapay, Cloud/Roleınstance, Cloud/roleName|
|istek/hız|Sunucu isteği hızı|Sayaçpersaniye|Ortalama|Saniye başına sunucu isteği oranı|istek/performanceBucket, istek/resultCode, işlem/yapay, Cloud/Roleınstance, istek/başarı, bulut/roleName|
|özel durumlar/say|Özel durumlar|Sayı|Sayı|Tüm yakalanamayan özel durumların Birleşik sayısı.|bulut/rol adı, bulut/Roleınstance, istemci/tür|
|özel durumlar/tarayıcı|Tarayıcı özel durumları|Sayı|Sayı|Tarayıcıda oluşturulan yakalanamayan özel durumların sayısı.|Boyut yok|
|özel durumlar/sunucu|Sunucu özel durumları|Sayı|Sayı|Sunucu uygulamasında oluşturulan yakalanamayan özel durum sayısı.|Cloud/roleName, Cloud/Roleınstance|
|izlemeler/say|Lerin|Sayı|Sayı|İzleme belgesi sayısı|izleme/severityLevel, işlem/yapay, Cloud/roleName, Cloud/Roleınstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|ServiceApiHit|Toplam hizmet API 'Si Isabet sayısı|Sayı|Sayı|Toplam hizmet API 'si isabet sayısı|ActivityType, ActivityName|
|Hizmet Apilatesi|Genel hizmet API 'Si gecikmesi|Milisaniye|Ortalama|Hizmet API 'si isteklerinin genel gecikmesi|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Toplam hizmet API 'Si sonuçları|Sayı|Sayı|Toplam hizmet API 'si sonuç sayısı|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft. kusto/kümeler

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Cachekullanım|Önbellek kullanımı|Yüzde|Ortalama|Küme kapsamındaki kullanım düzeyi|None|
|QueryDuration|Sorgu süresi|Milisaniye|Ortalama|Sorgu süresi (saniye)|Sorgu durumu|
|Alım kullanımı|Alım kullanımı|Yüzde|Ortalama|Kümedeki kullanılan alma yuvaları oranı|None|
|Alı|Canlı tut|Sayı|Ortalama|Sanity denetimi, kümenin sorgulara yanıt verdiğini belirtir|None|
|Inestionvolumeınmb|Alım birimi (MB)|Sayı|Toplam|Kümeye alınan verilerin toplam hacmi (MB)|Veritabanı|
|Inestionlatencınseconds|Alma gecikmesi (saniye)|Saniye|Ortalama|Kaynaktan (örn. ileti EventHub ' de bulunur) kümeye saniye cinsinden alma süresi|None|
|EventProcessedForEventHubs|Işlenen Olaylar (Event Hubs için)|Sayı|Toplam|Olay Hub 'ından geri ödeme yaparken küme tarafından işlenen olay sayısı|None|
|Inestionresult|Alım sonucu|Sayı|Sayı|Alma işlemlerinin sayısı|Durum|
|CPU|CPU|Yüzde|Ortalama|CPU kullanım düzeyi|None|
| Continuousexportnumofrecordsexports | Sürekli dışarı aktarmada dışarı aktarılmış kayıt sayısı | Sayı | Toplam | Dışarı aktarma işlemi sırasında yazılan her depolama yapıtı için dışarı aktarılmış kayıt sayısı  | None |
| Dışarı Aktaramstilileştirme | Kullanım verme | Yüzde | Maksimum | Kullanım verme | None |
| ContinuousExportPendingCount | Sürekli dışarı aktarma bekleyen sayısı | Sayı | Maksimum | Yürütmeye hazırlamış bekleyen sürekli dışarı aktarma işlerinin sayısı | None |
| ContinuousExportMaxLatenessMinutes | Sürekli dışarı aktarma en fazla dakika | Sayı | Maksimum | Bekleyen ve yürütme için hazırlanma olan tüm sürekli dışarı aktarmalar için dakika cinsinden en uzun süre | None |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft. LocationBasedServices/accounts

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Kullanım|Kullanım|Sayı|Sayı|API çağrısı sayısı|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|RunsStarted|Çalışma başlatıldı|Sayı|Toplam|Başlatılan iş akışı çalıştırma sayısı.|Boyut yok|
|RunsCompleted|Tamamlanan çalıştırmalar|Sayı|Toplam|Tamamlanan iş akışı çalıştırma sayısı.|Boyut yok|
|RunsSucceeded|Çalışma başarılı|Sayı|Toplam|İş akışı çalıştırma sayısı başarılı oldu.|Boyut yok|
|RunsFailed|Çalıştırma başarısız|Sayı|Toplam|Başarısız iş akışı çalıştırma sayısı.|Boyut yok|
|RunsCancelled|Çalıştırma Iptal edildi|Sayı|Toplam|İptal edilen iş akışı çalıştırma sayısı.|Boyut yok|
|RunLatency|Çalıştırma gecikmesi|Saniye|Ortalama|Tamamlanan iş akışı çalıştırmalarının gecikmesi.|Boyut yok|
|Runbaşarılı gecikme süresi|Çalıştırma başarılı gecikme süresi|Saniye|Ortalama|Başarılı iş akışı çalıştırmalarının gecikmesi.|Boyut yok|
|RunThrottledEvents|Kısıtlanmış olayları Çalıştır|Sayı|Toplam|İş akışı eylemi veya tetikleyici kısıtlanan olay sayısı.|Boyut yok|
|RunFailurePercentage yüzdesi|Çalıştırma hatası yüzdesi|Yüzde|Toplam|İş akışı çalıştırmalarının yüzdesi başarısız oldu.|Boyut yok|
|ActionsStarted|Başlatılan eylemler |Sayı|Toplam|Başlatılan iş akışı eylemi sayısı.|Boyut yok|
|ActionsCompleted|Tamamlanan eylemler |Sayı|Toplam|Tamamlanan iş akışı eylemlerinin sayısı.|Boyut yok|
|ActionsSucceeded|İşlemler başarılı oldu |Sayı|Toplam|Başarılı iş akışı eylemi sayısı.|Boyut yok|
|ActionsFailed|Başarısız eylemler|Sayı|Toplam|Başarısız iş akışı eylemi sayısı.|Boyut yok|
|ActionsSkipped|Atlanan eylemler |Sayı|Toplam|Atlanan iş akışı eylemi sayısı.|Boyut yok|
|ActionLatency|Eylem gecikmesi |Saniye|Ortalama|Tamamlanan iş akışı eylemlerinin gecikme süresi.|Boyut yok|
|Actionbaşarılı gecikme süresi|Eylem başarı gecikmesi |Saniye|Ortalama|Başarılı iş akışı eylemlerinin gecikme süresi.|Boyut yok|
|ActionThrottledEvents|Eylem kısıtlanmış olaylar|Sayı|Toplam|İş akışı eylemi kısıtlanmış olay sayısı..|Boyut yok|
|TriggersStarted|Başlatılan Tetikleyiciler |Sayı|Toplam|Başlatılan iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersCompleted|Tamamlanan Tetikleyiciler |Sayı|Toplam|Tamamlanan iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersSucceeded|Başarılı Tetikleyiciler |Sayı|Toplam|Başarılı iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersFailed|Başarısız Tetikleyiciler |Sayı|Toplam|Başarısız iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersSkipped|Atlanan Tetikleyiciler|Sayı|Toplam|Atlanan iş akışı tetikleyicisi sayısı.|Boyut yok|
|Triggersharekete geçirildi|Tetiklenen Tetikleyiciler |Sayı|Toplam|Tetiklenen iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggerLatency|Tetikleme gecikmesi |Saniye|Ortalama|Tamamlanan iş akışı tetikleyicilerinin gecikmesi.|Boyut yok|
|Triggerfirelatlik|Tetikleyici Tetikleme gecikmesi |Saniye|Ortalama|Tetiklenen iş akışı Tetikleyicileri gecikmesi.|Boyut yok|
|Triggerbaşarılı gecikme|Tetikleyici başarı gecikme süresi |Saniye|Ortalama|Başarılı iş akışı tetikleyicilerinin gecikmesi.|Boyut yok|
|TriggerThrottledEvents|Kısıtlanmış olayları tetikleyin|Sayı|Toplam|İş akışı tetikleyicisi kısıtlanmış olay sayısı.|Boyut yok|
|Billableactionyürütmeler|Faturalanabilir eylem yürütmeleri|Sayı|Toplam|Faturalandırılan iş akışı eylemi yürütmelerinin sayısı.|Boyut yok|
|Billabletriggeryürütmeler|Faturalanabilir tetikleyici yürütmeleri|Sayı|Toplam|Faturalandırılan iş akışı tetikleyici yürütmelerinin sayısı.|Boyut yok|
|Totalbillableyürütmeleri|Toplam faturalandırılabilir yürütmeler|Sayı|Toplam|Faturalandırılan iş akışı yürütmelerinin sayısı.|Boyut yok|
|BillingUsageNativeOperation|Yerel Işlem yürütmeleri için faturalandırma kullanımı|Sayı|Toplam|Faturalandırılan yerel işlem yürütmelerinin sayısı.|Boyut yok|
|BillingUsageStandardConnector|Standart bağlayıcı yürütmeleri için faturalandırma kullanımı|Sayı|Toplam|Faturalandırılan standart bağlayıcı yürütmelerinin sayısı.|Boyut yok|
|Billingusagestorampatüketim|Depolama tüketimi yürütmeleri için faturalama kullanımı|Sayı|Toplam|Faturalandırılan depolama tüketimi yürütmelerinin sayısı.|Boyut yok|
|BillingUsageNativeOperation|Yerel Işlem yürütmeleri için faturalandırma kullanımı|Sayı|Toplam|Faturalandırılan yerel işlem yürütmelerinin sayısı.|Boyut yok|
|BillingUsageStandardConnector|Standart bağlayıcı yürütmeleri için faturalandırma kullanımı|Sayı|Toplam|Faturalandırılan standart bağlayıcı yürütmelerinin sayısı.|Boyut yok|
|Billingusagestorampatüketim|Depolama tüketimi yürütmeleri için faturalama kullanımı|Sayı|Toplam|Faturalandırılan depolama tüketimi yürütmelerinin sayısı.|Boyut yok|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/ıntegrationserviceortamortamları

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|RunsStarted|Çalışma başlatıldı|Sayı|Toplam|Başlatılan iş akışı çalıştırma sayısı.|Boyut yok|
|RunsCompleted|Tamamlanan çalıştırmalar|Sayı|Toplam|Tamamlanan iş akışı çalıştırma sayısı.|Boyut yok|
|RunsSucceeded|Çalışma başarılı|Sayı|Toplam|İş akışı çalıştırma sayısı başarılı oldu.|Boyut yok|
|RunsFailed|Çalıştırma başarısız|Sayı|Toplam|Başarısız iş akışı çalıştırma sayısı.|Boyut yok|
|RunsCancelled|Çalıştırma Iptal edildi|Sayı|Toplam|İptal edilen iş akışı çalıştırma sayısı.|Boyut yok|
|RunLatency|Çalıştırma gecikmesi|Saniye|Ortalama|Tamamlanan iş akışı çalıştırmalarının gecikmesi.|Boyut yok|
|Runbaşarılı gecikme süresi|Çalıştırma başarılı gecikme süresi|Saniye|Ortalama|Başarılı iş akışı çalıştırmalarının gecikmesi.|Boyut yok|
|RunThrottledEvents|Kısıtlanmış olayları Çalıştır|Sayı|Toplam|İş akışı eylemi veya tetikleyici kısıtlanan olay sayısı.|Boyut yok|
|RunStartThrottledEvents|Kısıtlanmış başlangıç olaylarını Çalıştır|Sayı|Toplam|İş akışı çalıştırma, kısıtlanmış olay sayısı.|Boyut yok|
|RunFailurePercentage yüzdesi|Çalıştırma hatası yüzdesi|Yüzde|Toplam|İş akışı çalıştırmalarının yüzdesi başarısız oldu.|Boyut yok|
|ActionsStarted|Başlatılan eylemler |Sayı|Toplam|Başlatılan iş akışı eylemi sayısı.|Boyut yok|
|ActionsCompleted|Tamamlanan eylemler |Sayı|Toplam|Tamamlanan iş akışı eylemlerinin sayısı.|Boyut yok|
|ActionsSucceeded|İşlemler başarılı oldu |Sayı|Toplam|Başarılı iş akışı eylemi sayısı.|Boyut yok|
|ActionsFailed|Başarısız eylemler |Sayı|Toplam|Başarısız iş akışı eylemi sayısı.|Boyut yok|
|ActionsSkipped|Atlanan eylemler |Sayı|Toplam|Atlanan iş akışı eylemi sayısı.|Boyut yok|
|ActionLatency|Eylem gecikmesi |Saniye|Ortalama|Tamamlanan iş akışı eylemlerinin gecikme süresi.|Boyut yok|
|Actionbaşarılı gecikme süresi|Eylem başarı gecikmesi |Saniye|Ortalama|Başarılı iş akışı eylemlerinin gecikme süresi.|Boyut yok|
|ActionThrottledEvents|Eylem kısıtlanmış olaylar|Sayı|Toplam|İş akışı eylemi kısıtlanmış olay sayısı..|Boyut yok|
|TriggersStarted|Başlatılan Tetikleyiciler |Sayı|Toplam|Başlatılan iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersCompleted|Tamamlanan Tetikleyiciler |Sayı|Toplam|Tamamlanan iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersSucceeded|Başarılı Tetikleyiciler |Sayı|Toplam|Başarılı iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersFailed|Başarısız Tetikleyiciler |Sayı|Toplam|Başarısız iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersSkipped|Atlanan Tetikleyiciler|Sayı|Toplam|Atlanan iş akışı tetikleyicisi sayısı.|Boyut yok|
|Triggersharekete geçirildi|Tetiklenen Tetikleyiciler |Sayı|Toplam|Tetiklenen iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggerLatency|Tetikleme gecikmesi |Saniye|Ortalama|Tamamlanan iş akışı tetikleyicilerinin gecikmesi.|Boyut yok|
|Triggerfirelatlik|Tetikleyici Tetikleme gecikmesi |Saniye|Ortalama|Tetiklenen iş akışı Tetikleyicileri gecikmesi.|Boyut yok|
|Triggerbaşarılı gecikme|Tetikleyici başarı gecikme süresi |Saniye|Ortalama|Başarılı iş akışı tetikleyicilerinin gecikmesi.|Boyut yok|
|TriggerThrottledEvents|Kısıtlanmış olayları tetikleyin|Sayı|Toplam|İş akışı tetikleyicisi kısıtlanmış olay sayısı.|Boyut yok|
|Integrationserviceenvironmentworkflowprocessorusage|Tümleştirme Hizmeti Ortamı için iş akışı Işlemcisi kullanımı|Yüzde|Ortalama|Tümleştirme hizmeti ortamı için iş akışı işlemcisi kullanımı.|Boyut yok|
|Integrationserviceenvironmentworkflowmemoryusage|Tümleştirme Hizmeti Ortamı için iş akışı bellek kullanımı|Yüzde|Ortalama|Tümleştirme hizmeti ortamı için iş akışı bellek kullanımı.|Boyut yok|
|Integrationserviceenvironmentconnectorprocessorusage|Tümleştirme Hizmeti Ortamı için bağlayıcı Işlemci kullanımı|Yüzde|Ortalama|Tümleştirme hizmeti ortamı için bağlayıcı işlemci kullanımı.|Boyut yok|
|Integrationserviceenvironmentconnectormemoryusage|Tümleştirme Hizmeti Ortamı için bağlayıcı bellek kullanımı|Yüzde|Ortalama|Tümleştirme hizmeti ortamı için bağlayıcı bellek kullanımı.|Boyut yok|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/çalışma alanları

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Tamamlanan çalıştırmalar|Tamamlanan çalıştırmalar|Sayı|Toplam|Bu çalışma alanı için başarıyla tamamlanan çalıştırma sayısı|Senaryo|
|Başlatılan çalıştırmalar|Başlatılan çalıştırmalar|Sayı|Toplam|Bu çalışma alanı için başlatılan çalıştırma sayısı|Senaryo|
|Başarısız çalıştırmalar|Başarısız çalıştırmalar|Sayı|Toplam|Bu çalışma alanı için başarısız olan çalıştırma sayısı|Senaryo|

## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/hesaplar

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Kullanım|Kullanım|Sayı|Sayı|API çağrısı sayısı|ApiCategory, ApiName, ResultType, ResponseCode|
|Kullanılabilirlik|Kullanılabilirlik|Yüzde|Ortalama|API 'lerin kullanılabilirliği|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/Capacityhavuzları/birimleri

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|AverageOtherLatency|Diğer ortalama gecikme süresi|MS/op|Ortalama|İşlem başına milisaniye cinsinden ortalama diğer gecikme süresi (okuma veya yazma)|Boyut yok|
|AverageReadLatency|Ortalama okuma gecikmesi|MS/op|Ortalama|İşlem başına milisaniye cinsinden ortalama okuma gecikmesi|Boyut yok|
|AverageTotalLatency|Ortalama toplam gecikme|MS/op|Ortalama|İşlem başına milisaniye cinsinden ortalama toplam gecikme|Boyut yok|
|AverageWriteLatency|Ortalama yazma gecikmesi|MS/op|Ortalama|İşlem başına milisaniye cinsinden ortalama yazma gecikmesi|Boyut yok|
|FilesystemOtherOps|Dosya sistemi diğer Ops|işlemleri|Ortalama|Dosya sistemi diğer işlem sayısı (okuma veya yazma olmayan)|Boyut yok|
|FilesystemReadOps|Dosya sistemi okuma Ops|işlemleri|Ortalama|Dosya sistemi okuma işlemi sayısı|Boyut yok|
|FilesystemTotalOps|Dosya sistemi toplam Ops|işlemleri|Ortalama|Tüm dosya sistemi işlemlerinin toplamı|Boyut yok|
|FilesystemWriteOps|Dosya sistemi yazma Ops|işlemleri|Ortalama|Dosya sistemi yazma işlemi sayısı|Boyut yok|
|Iobi Tesperotherops|Diğer Ops başına GÇ bayt sayısı|bayt/op|Ortalama|Diğer işlemler başına gelen/giden bayt sayısı (okuma veya yazma olmayan)|Boyut yok|
|Iobi Tesperreadops|Okuma Ops başına GÇ bayt sayısı|bayt/op|Ortalama|Okuma işlemi başına gelen/giden bayt sayısı|Boyut yok|
|Iobi Tespertotalops|Tüm işlemler genelinde OP başına GÇ bayt sayısı|bayt/op|Ortalama|Tüm gelen/giden bayt işlemleri toplamı|Boyut yok|
|Iobi Tesperwriteops|Yazma Ops başına GÇ bayt sayısı|bayt/op|Ortalama|Yazma işlemi başına gelen/giden bayt sayısı|Boyut yok|
|Diğer IOPS|Diğer IOPS|işlem/saniye|Ortalama|Saniye başına diğer gelen/giden işlem|Boyut yok|
|Diğer aktarım hızı|Diğer aktarım hızı|Kadar|Ortalama|Saniye başına megabayt cinsinden diğer üretilen iş (okuma veya yazma)|Boyut yok|
|ReadIops|IOPS 'yi oku|işlem/saniye|Ortalama|Saniye başına okuma/kapatma işlemi|Boyut yok|
|Readüretilen Iş|Aktarım hızını oku|Kadar|Ortalama|Saniye başına megabayt cinsinden okuma performansı|Boyut yok|
|Toplam IOPS|Toplam IOPS|işlem/saniye|Ortalama|Saniye başına tüm gelen/giden işlemlerin toplamı|Boyut yok|
|Toplam aktarım hızı|Toplam Verimlilik|Kadar|Ortalama|Saniye başına megabayt cinsinden tüm aktarım hızı toplamı|Boyut yok|
|Birimayrıtedboyut|Birim ayırma boyutu|bayt|Ortalama|Birimin ayrılan boyutu (gerçek kullanılan baytlar değil)|Boyut yok|
|Birimlogicalsize|Birim mantıksal boyutu|bayt|Ortalama|Birimin mantıksal boyutu (kullanılan baytlar)|Boyut yok|
|VolumeSnapshotSize|Birim anlık görüntü boyutu|bayt|Ortalama|Birimdeki tüm anlık görüntülerin boyutu|Boyut yok|
|Writeıops|IOPS yaz|işlem/saniye|Ortalama|Saniyede yazma/çıkış işlemi sayısı|Boyut yok|
|Writeüretilen Iş|Yazma performansı|Kadar|Ortalama|Saniye başına megabayt cinsinden yazma hızı|Boyut yok|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/Capacityhavuzları

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Birim havuzu ayrılan boyut|bayt|Ortalama|Havuzun ayrılan boyutu (gerçek kullanılan baytlar değil)|Boyut yok|
|VolumePoolAllocatedUsed|Ayrılan birim havuzu|bayt|Ortalama|Havuzun ayrılan boyutu ayrılmış|Boyut yok|
|VolumePoolTotalLogicalSize|Birim havuzu toplam mantıksal boyut|bayt|Ortalama|Havuza ait tüm birimlerin mantıksal boyutunun toplamı|Boyut yok|
|VolumePoolTotalSnapshotSize|Birim havuzu toplam anlık görüntü boyutu|bayt|Ortalama|Havuzdaki tüm anlık görüntülerin toplamı|Boyut yok|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/NetworkInterfaces

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|BytesSentRate|Gönderilen bayt|Sayı|Toplam|Ağ arabiriminin gönderdiği bayt sayısı|Boyut yok|
|BytesReceivedRate|Alınan bayt|Sayı|Toplam|Ağ arabiriminin aldığı bayt sayısı|Boyut yok|
|PacketsSentRate|Gönderilen paketler|Sayı|Toplam|Ağ arabiriminin gönderdiği paket sayısı|Boyut yok|
|PacketsReceivedRate|Alınan paketler|Sayı|Toplam|Ağ arabiriminin aldığı paket sayısı|Boyut yok|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|VipAvailability|Veri yolu kullanılabilirliği|Sayı|Ortalama|Süre başına ortalama Load Balancer veri yolu kullanılabilirliği|Frontendıaddress, FrontendPort|
|DipAvailability|Durum araştırma durumu|Sayı|Ortalama|Süre başına ortalama Load Balancer sistem durumu araştırma durumu|ProtocolType, BackendPort, Frontendıbaddress, FrontendPort, Backendıdıaddress|
|ByteCount|Bayt sayısı|Sayı|Toplam|Zaman aralığı içinde aktarılan toplam bayt sayısı|Frontendıaaddress, FrontendPort, Yön|
|PacketCount|Paket sayısı|Sayı|Toplam|Zaman aralığı içinde aktarılan toplam paket sayısı|Frontendıaaddress, FrontendPort, Yön|
|SYNCount|SYN sayısı|Sayı|Toplam|Zaman aralığı içinde aktarılan toplam SYN paketi sayısı|Frontendıaaddress, FrontendPort, Yön|
|SnatConnectionCount|SNAT bağlantı sayısı|Sayı|Toplam|Zaman aralığı içinde oluşturulan toplam yeni SNAT bağlantısı sayısı|Frontendıbaddress, Backendıo adresi, ConnectionState|
|AllocatedSnatPorts|Ayrılan SNAT bağlantı noktaları (Önizleme)|Sayı|Toplam|Zaman aralığı içinde ayrılan toplam SNAT bağlantı noktası sayısı|Frontendıbaddress, Backendıaddress, ProtocolType|
|UsedSnatPorts|Kullanılan SNAT bağlantı noktaları (Önizleme)|Sayı|Toplam|Zaman aralığı içinde kullanılan SNAT bağlantı noktalarının toplam sayısı|Frontendıbaddress, Backendıaddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|QueryVolume|Sorgu hacmi|Sayı|Toplam|Bir DNS bölgesi için sunulan sorgu sayısı|Boyut yok|
|Kayıt kümesi sayısı|Kayıt kümesi sayısı|Sayı|Maksimum|Bir DNS bölgesindeki kayıt kümesi sayısı|Boyut yok|
|Recordsetkapaıyutilileştirme|Kayıt kümesi kapasite kullanımı|Yüzde|Maksimum|Bir DNS bölgesi tarafından kullanılan kayıt kümesi kapasitesinin yüzdesi|Boyut yok|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|PacketsInDDoS|Gelen paketler DDoS|Sayaçpersaniye|Maksimum|Gelen paketler DDoS|Boyut yok|
|PacketsDroppedDDoS|DDoS bırakılan gelen paketler|Sayaçpersaniye|Maksimum|DDoS bırakılan gelen paketler|Boyut yok|
|PacketsForwardedDDoS|DDoS iletilen gelen paketler|Sayaçpersaniye|Maksimum|DDoS iletilen gelen paketler|Boyut yok|
|TCPPacketsInDDoS|Gelen TCP paketleri DDoS|Sayaçpersaniye|Maksimum|Gelen TCP paketleri DDoS|Boyut yok|
|TCPPacketsDroppedDDoS|Gelen TCP paketleri bırakıldı DDoS|Sayaçpersaniye|Maksimum|Gelen TCP paketleri bırakıldı DDoS|Boyut yok|
|TCPPacketsForwardedDDoS|DDoS iletilen gelen TCP paketleri|Sayaçpersaniye|Maksimum|DDoS iletilen gelen TCP paketleri|Boyut yok|
|UDPPacketsInDDoS|Gelen UDP paketleri DDoS|Sayaçpersaniye|Maksimum|Gelen UDP paketleri DDoS|Boyut yok|
|UDPPacketsDroppedDDoS|Gelen UDP paketleri bırakıldı|Sayaçpersaniye|Maksimum|Gelen UDP paketleri bırakıldı|Boyut yok|
|UDPPacketsForwardedDDoS|DDoS iletilen gelen UDP paketleri|Sayaçpersaniye|Maksimum|DDoS iletilen gelen UDP paketleri|Boyut yok|
|BytesInDDoS|Gelen bayt sayısı DDoS|BytesPerSecond|Maksimum|Gelen bayt sayısı DDoS|Boyut yok|
|BytesDroppedDDoS|Gelen bayt bırakıldı DDoS|BytesPerSecond|Maksimum|Gelen bayt bırakıldı DDoS|Boyut yok|
|BytesForwardedDDoS|İletilen gelen bayt sayısı|BytesPerSecond|Maksimum|İletilen gelen bayt sayısı|Boyut yok|
|TCPBytesInDDoS|Gelen TCP bayt DDoS|BytesPerSecond|Maksimum|Gelen TCP bayt DDoS|Boyut yok|
|TCPBytesDroppedDDoS|Gelen TCP bayt bırakılan DDoS|BytesPerSecond|Maksimum|Gelen TCP bayt bırakılan DDoS|Boyut yok|
|TCPBytesForwardedDDoS|İletilen gelen TCP baytları|BytesPerSecond|Maksimum|İletilen gelen TCP baytları|Boyut yok|
|UDPBytesInDDoS|Gelen UDP bayt sayısı DDoS|BytesPerSecond|Maksimum|Gelen UDP bayt sayısı DDoS|Boyut yok|
|UDPBytesDroppedDDoS|Gelen UDP bayt bırakılan DDoS|BytesPerSecond|Maksimum|Gelen UDP bayt bırakılan DDoS|Boyut yok|
|UDPBytesForwardedDDoS|İletilen gelen UDP baytları|BytesPerSecond|Maksimum|İletilen gelen UDP baytları|Boyut yok|
|IfUnderDDoSAttack|DDoS saldırısı altında|Sayı|Maksimum|DDoS saldırısı altında|Boyut yok|
|DDO, Ggertcppackets|DDoS azaltma tetiklemeye yönelik gelen TCP paketleri|Sayaçpersaniye|Maksimum|DDoS azaltma tetiklemeye yönelik gelen TCP paketleri|Boyut yok|
|DDO, Ggerudppaketleri|DDoS risk azaltma tetiklenecek gelen UDP paketleri|Sayaçpersaniye|Maksimum|DDoS risk azaltma tetiklenecek gelen UDP paketleri|Boyut yok|
|Ddobir Ggersynpackets|DDoS azaltma tetiklenmesi için gelen SYN paketleri|Sayaçpersaniye|Maksimum|DDoS azaltma tetiklenmesi için gelen SYN paketleri|Boyut yok|
|VipAvailability|Veri yolu kullanılabilirliği|Sayı|Ortalama|Süre başına ortalama IP adresi kullanılabilirliği|Bağlantı noktası|
|ByteCount|Bayt sayısı|Sayı|Toplam|Zaman aralığı içinde aktarılan toplam bayt sayısı|Bağlantı noktası, Yön|
|PacketCount|Paket sayısı|Sayı|Toplam|Zaman aralığı içinde aktarılan toplam paket sayısı|Bağlantı noktası, Yön|
|SynCount|SYN sayısı|Sayı|Toplam|Zaman aralığı içinde aktarılan toplam SYN paketi sayısı|Bağlantı noktası, Yön|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|ApplicationRuleHit|Uygulama kuralları isabet sayısı|Sayı|Toplam|Uygulama kurallarının isabet sayısı|Durum, neden, protokol|
|NetworkRuleHit|Ağ kuralları isabet sayısı|Sayı|Toplam|Ağ kurallarının isabet sayısı|Durum, neden, protokol|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Aktarım hızı|Aktarım hızı|BytesPerSecond|Toplam|Application Gateway saniyede hizmet verilen bayt sayısı|Boyut yok|
|Unhealthyıhostcount|Sağlıksız konak sayısı|Sayı|Ortalama|Sağlıklı olmayan arka uç Konakları sayısı|BackendSettingsPool|
|Healthyıhostcount|Sağlıklı konak sayısı|Sayı|Ortalama|Sağlıklı arka uç Konakları sayısı|BackendSettingsPool|
|Toplam Istek sayısı|Toplam İstek Sayısı|Sayı|Toplam|Application Gateway tarafından sunulan başarılı istek sayısı|BackendSettingsPool|
|FailedRequests|Başarısız İstekler|Sayı|Toplam|Application Gateway tarafından sunulan başarısız istek sayısı|BackendSettingsPool|
|ResponseStatus|Yanıt durumu|Sayı|Toplam|Application Gateway tarafından döndürülen http yanıt durumu|HttpStatusGroup|
|CurrentConnections|Geçerli bağlantılar|Sayı|Toplam|Application Gateway ile kurulan geçerli bağlantı sayısı|Boyut yok|
|CapacityUnits|Geçerli kapasite birimleri|Sayı|Ortalama|Tüketilen Kapasite birimleri|Boyut yok|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/Virtualnetworkgateway 'ler

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|AverageBandwidth|Ağ Geçidi S2S bant genişliği|BytesPerSecond|Ortalama|Bayt/saniye cinsinden bir ağ geçidinin ortalama siteden siteye bant genişliği|Boyut yok|
|P2SBandwidth|Ağ Geçidi P2S bant genişliği|BytesPerSecond|Ortalama|Bayt/saniye cinsinden bir ağ geçidinin ortalama Noktadan siteye bant genişliği|Boyut yok|
|P2SConnectionCount|P2S bağlantı sayısı|Sayı|Maksimum|Bir ağ geçidinin Noktadan siteye bağlantı sayısı|Protokol|
|TunnelAverageBandwidth|Tünel bant genişliği|BytesPerSecond|Ortalama|Bir tünelin bayt/saniye cinsinden ortalama bant genişliği|ConnectionName, Remoteıp|
|TunnelEgressBytes|Tünel çıkış baytları|Bayt|Toplam|Bir tünelin giden baytları|ConnectionName, Remoteıp|
|TunnelIngressBytes|Tünel giriş baytları|Bayt|Toplam|Bir tünelin gelen baytları|ConnectionName, Remoteıp|
|TunnelEgressPackets|Tünel çıkış paketleri|Sayı|Toplam|Bir tünelin giden paket sayısı|ConnectionName, Remoteıp|
|TunnelIngressPackets|Tünel giriş paketleri|Sayı|Toplam|Bir tünelin gelen paket sayısı|ConnectionName, Remoteıp|
|TunnelEgressPacketDropTSMismatch|Tünel çıkış uyumsuzluğu paket bırakma|Sayı|Toplam|Bir tünelin trafik seçiciyle eşleşmeyen giden paket bırakma sayısı|ConnectionName, Remoteıp|
|TunnelIngressPacketDropTSMismatch|Tünel girişi uyumsuz paket bırakma|Sayı|Toplam|Trafik seçicideki gelen paket bırakma sayısı bir tünelin eşleşmemesi|ConnectionName, Remoteıp|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/Expressroutedevreleri

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Sayaçpersaniye|Ortalama|Saniye başına Azure 'da BITS giriş|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|Sayaçpersaniye|Ortalama|Saniye başına Azure 'da BITS yumursan|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/Expressroutedevreleri/eşayarları

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Sayaçpersaniye|Ortalama|Saniye başına Azure 'da BITS giriş|Boyut yok|
|BitsOutPerSecond|BitsOutPerSecond|Sayaçpersaniye|Ortalama|Saniye başına Azure 'da BITS yumursan|Boyut yok|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Sayaçpersaniye|Ortalama|Saniye başına Azure 'da BITS giriş|Boyut yok|
|BitsOutPerSecond|BitsOutPerSecond|Sayaçpersaniye|Ortalama|Saniye başına Azure 'da BITS yumursan|Boyut yok|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|QpsByEndpoint|Bitiş noktasına göre sorgular döndürüldü|Sayı|Toplam|Verilen zaman çerçevesinde Traffic Manager uç noktasının döndürüldüğü sayı|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Uç nokta tarafından uç nokta durumu|Sayı|Maksimum|1 bir uç noktanın araştırma durumu "etkin" ise, 0 yoksa.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/Connectionmonitörleri

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Yoklama başarısız oldu|Yüzde|Ortalama|Bağlantı İzleme Araştırmaları yüzdesi başarısız oldu|Boyut yok|
|AverageRoundtripMs|Ort. gidiş dönüş süresi (MS)|Mayacak|Ortalama|Kaynak ve hedef arasında gönderilen bağlantı izleme araştırmaları için Ortalama ağ gidiş dönüş süresi (MS)|Boyut yok|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontkapaklı

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|RequestCount|İstek Sayısı|Sayı|Toplam|HTTP/S proxy tarafından hizmet verilen istemci isteklerinin sayısı|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|İstek boyutu|Bayt|Toplam|İstemcilerden HTTP/S proxy 'sine istek olarak gönderilen bayt sayısı|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Yanıt boyutu|Yanıt boyutu|Bayt|Toplam|HTTP/S proxy 'sinden istemcilere yanıt olarak gönderilen baytların sayısı|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Arka uç Istek sayısı|Sayı|Toplam|HTTP/S proxy 'sinden arka uçlara gönderilen isteklerin sayısı|HttpStatus, HttpStatusGroup, arka uç|
|BackendRequestLatency|Arka uç Isteği gecikmesi|Mayacak|Ortalama|HTTP/s proxy, arka ucun son yanıt baytını almayana kadar isteğin HTTP/S proxy tarafından arka uca gönderildiği süre|Sunucusundan|
|TotalLatency|Toplam gecikme süresi|Mayacak|Ortalama|İstemci, http/s proxy 'sinden gelen son yanıt baytını kabul edene kadar, istemci isteğinin HTTP/S proxy tarafından alındığı zamana göre hesaplanır|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Arka uç sistem durumu yüzdesi|Yüzde|Ortalama|HTTP/S proxy 'sinden arka uçlara başarılı sistem durumu araştırmalarının yüzdesi|Arka uç, BackendPool|
|WebApplicationFirewallRequestCount|Web uygulaması güvenlik duvarı Istek sayısı|Sayı|Toplam|Web uygulaması güvenlik duvarı tarafından işlenen istemci isteklerinin sayısı|PolicyName, RuleName, eylem|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub 'Lar

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|kayıt. tümü|Kayıt Işlemleri|Sayı|Toplam|Tüm başarılı kayıt işlemlerinin sayısı (güncelleştirme sorguları ve silmeleri oluşturma). |Boyut yok|
|kayıt. oluştur|Kayıt oluşturma Işlemleri|Sayı|Toplam|Tüm başarılı kayıt oluşturma işlemlerinin sayısı.|Boyut yok|
|kayıt. güncelleştirme|Kayıt güncelleştirme Işlemleri|Sayı|Toplam|Tüm başarılı kayıt güncelleştirmelerinin sayısı.|Boyut yok|
|kayıt. Get|Kayıt okuma Işlemleri|Sayı|Toplam|Tüm başarılı kayıt sorgularının sayısı.|Boyut yok|
|kayıt. Sil|Kayıt silme Işlemleri|Sayı|Toplam|Tüm başarılı kayıt silme işlemlerinin sayısı.|Boyut yok|
|gelen|Gelen Iletiler|Sayı|Toplam|Başarılı bir gönderme API çağrısı sayısı. |Boyut yok|
|gelen. zamanlandı|Zamanlanan anında Iletme bildirimleri gönderildi|Sayı|Toplam|Zamanlanan anında Iletme bildirimleri Iptal edildi|Boyut yok|
|gelen. zamanlandı. iptal|Zamanlanan anında Iletme bildirimleri Iptal edildi|Sayı|Toplam|Zamanlanan anında Iletme bildirimleri Iptal edildi|Boyut yok|
|scheduled.pending|Bekleyen zamanlanmış bildirimler|Sayı|Toplam|Bekleyen zamanlanmış bildirimler|Boyut yok|
|yükleme. tümü|Yükleme yönetimi Işlemleri|Sayı|Toplam|Yükleme yönetimi Işlemleri|Boyut yok|
|yükleme. Get|Yükleme Işlemlerini al|Sayı|Toplam|Yükleme Işlemlerini al|Boyut yok|
|yükleme. upsert|Yükleme Işlemlerini oluşturma veya güncelleştirme|Sayı|Toplam|Yükleme Işlemlerini oluşturma veya güncelleştirme|Boyut yok|
|yükleme. Patch|Düzeltme eki yükleme Işlemleri|Sayı|Toplam|Düzeltme eki yükleme Işlemleri|Boyut yok|
|yükleme. Delete|Yükleme Işlemlerini silme|Sayı|Toplam|Yükleme Işlemlerini silme|Boyut yok|
|giden. allpns. Success|Başarılı bildirimler|Sayı|Toplam|Tüm başarılı bildirimlerin sayısı.|Boyut yok|
|giden. allpns. ınvalidpayload|Yük hataları|Sayı|Toplam|PNS hatalı yük hatası döndürdüğünden başarısız olan gönderim sayısı.|Boyut yok|
|outgoing.allpns.pnserror|Dış bildirim sistemi hataları|Sayı|Toplam|PNS ile iletişim kurulurken bir sorun oluştuğundan başarısız olan gönderim sayısı (kimlik doğrulama sorunları hariç).|Boyut yok|
|outgoing.allpns.channelerror|Kanal hataları|Sayı|Toplam|Kanal geçersiz olduğu için doğru uygulamayla ilişkili olmayan veya geçerliliği aşıldığı için başarısız olan gönderim sayısı.|Boyut yok|
|outgoing.allpns.badorexpiredchannel|Hatalı veya süre dolma kanalı hataları|Sayı|Toplam|Kayıttaki kanal/belirteç/RegistrationId 'nin geçerliliği aşıldığı veya geçersiz olduğu için başarısız olan gönderim sayısı.|Boyut yok|
|giden. WNS. Success|WNS başarılı bildirimleri|Sayı|Toplam|Tüm başarılı bildirimlerin sayısı.|Boyut yok|
|giden. WNS. ınvalidcredentials|WNS yetkilendirme hataları (geçersiz kimlik bilgileri)|Sayı|Toplam|PNS belirtilen kimlik bilgilerini kabul etmediğinden veya kimlik bilgileri engellendiğinden başarısız olan gönderim sayısı. (Windows Live kimlik bilgilerini tanımaz).|Boyut yok|
|giden. WNS. badchannel|WNS hatalı kanal hatası|Sayı|Toplam|Kayıttaki Channelurı tanınmadığı için başarısız olan gönderim sayısı (WNS durumu: 404 bulunamadı).|Boyut yok|
|outgoing.wns.expiredchannel|WNS süre dolduğunda kanal hatası|Sayı|Toplam|ChannelURI 'nin geçerliliği aşıldığı için başarısız olan gönderim sayısı (WNS durumu: 410 gitti).|Boyut yok|
|giden. WNS. kısıtlanıyor|WNS kısıtlanıyor bildirimleri|Sayı|Toplam|WNS bu uygulamayı azaltdığı için başarısız olan gönderim sayısı (WNS durumu: 406 kabul edilemez).|Boyut yok|
|giden. WNS. tokenproviderunreachable|WNS yetkilendirme hataları (ulaşılamıyor)|Sayı|Toplam|Windows Live ulaşılamıyor.|Boyut yok|
|giden. WNS. ınvalidtoken|WNS yetkilendirme hataları (geçersiz belirteç)|Sayı|Toplam|WNS öğesine sunulan belirteç geçerli değil (WNS Status: 401 Yetkisiz).|Boyut yok|
|giden. WNS. yanlışlıkla gtoken|WNS yetkilendirme hataları (yanlış belirteç)|Sayı|Toplam|WNS öğesine sunulan belirteç geçerli, ancak başka bir uygulama için (WNS durumu: 403 Yasak). Kayıttaki Channelurı başka bir uygulamayla ilişkiliyse bu durum oluşabilir. İstemci uygulamasının kimlik bilgileri Bildirim Hub 'ında olan aynı uygulamayla ilişkilendirildiğinden emin olun.|Boyut yok|
|giden. WNS. ınvalidnotificationformat|WNS geçersiz bildirim biçimi|Sayı|Toplam|Bildirimin biçimi geçersiz (WNS durumu: 400). WNS 'nin tüm geçersiz yükleri reddetmediğini unutmayın.|Boyut yok|
|giden. WNS. ınvalidnotificationsize|WNS geçersiz bildirim boyutu hatası|Sayı|Toplam|Bildirim yükü çok büyük (WNS durumu: 413).|Boyut yok|
|giden. WNS. channelkısıtlanıyor|WNS kanal kısıtlandı|Sayı|Toplam|Kayıttaki Channelurı kısıtlandığı için bildirim bırakıldı (WNS yanıt üst bilgisi: X-WNS-NotificationStatus: Channelkısıtlanıyor).|Boyut yok|
|outgoing.wns.channeldisconnected|WNS kanal bağlantısı kesildi|Sayı|Toplam|Kayıttaki Channelurı kısıtlandığı için bildirim bırakıldı (WNS yanıt üst bilgisi: X-WNS-DeviceConnectionStatus: bağlantısı kesildi).|Boyut yok|
|giden. WNS. bırakıldı|WNS bırakılan bildirimler|Sayı|Toplam|Kayıttaki Channelurı kısıtlandığı için bildirim bırakıldı (X-WNS-NotificationStatus: bırakıldı ancak X-WNS-DeviceConnectionStatus: bağlantısı kesildi).|Boyut yok|
|outgoing.wns.pnserror|WNS hataları|Sayı|Toplam|Bildirim, WNS ile iletişim kuran hatalar nedeniyle teslim edilemiyor.|Boyut yok|
|outgoing.wns.authenticationerror|WNS kimlik doğrulama hataları|Sayı|Toplam|Windows Live geçersiz kimlik bilgileri veya yanlış belirteçle iletişim kurarken oluşan hatalar nedeniyle bildirim teslim edilemiyor.|Boyut yok|
|giden. APNs. Success|APNS başarılı bildirimler|Sayı|Toplam|Tüm başarılı bildirimlerin sayısı.|Boyut yok|
|giden. APNs. ınvalidcredentials|APNS yetkilendirme hataları|Sayı|Toplam|PNS belirtilen kimlik bilgilerini kabul etmediğinden veya kimlik bilgileri engellendiğinden başarısız olan gönderim sayısı.|Boyut yok|
|giden. APNs. badchannel|APNS geçersiz kanal hatası|Sayı|Toplam|Belirteç geçersiz olduğu için başarısız olan gönderim sayısı (APNS ikili protokol durum kodu: 8. APNS HTTP protokolü durum kodu: "BadDeviceToken" ile 400).|Boyut yok|
|outgoing.apns.expiredchannel|APNS süre dolma kanalı hatası|Sayı|Toplam|APNS geri bildirim kanalı tarafından geçersiz kılınan belirteç sayısı.|Boyut yok|
|giden. APNs. ınvalidnotificationsize|APNS geçersiz bildirim boyutu hatası|Sayı|Toplam|Yük çok büyük olduğu için başarısız olan gönderim sayısı (APNS ikili protokol durum kodu: 7).|Boyut yok|
|giden. APNs. pnserror|APNS hataları|Sayı|Toplam|APNS ile iletişim kurulurken oluşan hatalardan dolayı başarısız olan gönderim sayısı.|Boyut yok|
|giden. gcm. Success|GCM başarılı bildirimleri|Sayı|Toplam|Tüm başarılı bildirimlerin sayısı.|Boyut yok|
|giden. gcm. ınvalidcredentials|GCM yetkilendirme hataları (geçersiz kimlik bilgileri)|Sayı|Toplam|PNS belirtilen kimlik bilgilerini kabul etmediğinden veya kimlik bilgileri engellendiğinden başarısız olan gönderim sayısı.|Boyut yok|
|giden. gcm. badchannel|GCM hatalı kanal hatası|Sayı|Toplam|Kayıttaki RegistrationId tanınmadığı için başarısız olan gönderim sayısı (GCM sonucu: geçersiz kayıt).|Boyut yok|
|giden. gcm. expiredchannel|GCM süre aşımına uğradı kanal hatası|Sayı|Toplam|Kayıttaki RegistrationId 'nin zaman aşımına uğradığından başarısız olan gönderim sayısı (GCM sonucu: NotRegistered).|Boyut yok|
|giden. gcm. kısıtlanıyor|GCM kısıtlanan bildirimler|Sayı|Toplam|GCM tarafından kısıtlanan bu uygulamayı (GCM durum kodu: 501-599 veya sonuç: kullanılamıyor) nedeniyle başarısız olan gönderim sayısı.|Boyut yok|
|giden. gcm. ınvalidnotificationformat|GCM geçersiz bildirim biçimi|Sayı|Toplam|Yük doğru biçimlendirilmediği için başarısız olan gönderim sayısı (GCM sonucu: ınvaliddatakey veya ınvalidttl).|Boyut yok|
|giden. gcm. ınvalidnotificationsize|GCM geçersiz bildirim boyutu hatası|Sayı|Toplam|Yük çok büyük olduğu için başarısız olan gönderim sayısı (GCM sonucu: Messagetoobıg).|Boyut yok|
|outgoing.gcm.wrongchannel|GCM yanlış kanal hatası|Sayı|Toplam|Kayıttaki RegistrationId geçerli uygulamayla ilişkili olmadığından başarısız olan gönderim sayısı (GCM sonucu: ınvalidpackagename).|Boyut yok|
|outgoing.gcm.pnserror|GCM hataları|Sayı|Toplam|GCM ile iletişim kurulurken oluşan hatalardan dolayı başarısız olan gönderim sayısı.|Boyut yok|
|giden. gcm. authenticationerror|GCM kimlik doğrulama hataları|Sayı|Toplam|PNS belirtilen kimlik bilgilerini kabul etmediğinden başarısız olan gönderim sayısı, kimlik bilgilerinin engellenmesi veya SenderId uygulamada doğru yapılandırılmamış (GCM sonucu: Mismatchedsenderıd).|Boyut yok|
|giden. MPNS. Success|MPNS başarılı bildirimleri|Sayı|Toplam|Tüm başarılı bildirimlerin sayısı.|Boyut yok|
|giden. MPNS. ınvalidcredentials|MPNS geçersiz kimlik bilgileri|Sayı|Toplam|PNS belirtilen kimlik bilgilerini kabul etmediğinden veya kimlik bilgileri engellendiğinden başarısız olan gönderim sayısı.|Boyut yok|
|giden. MPNS. badchannel|MPNS hatalı kanal hatası|Sayı|Toplam|Kayıttaki Channelurı tanınmadığı için başarısız olan gönderim sayısı (MPNS durumu: 404 bulunamadı).|Boyut yok|
|giden. MPNS. kısıtlanıyor|MPNS kısıtlanan bildirimler|Sayı|Toplam|MPNS bu uygulamayı azaltdığı için başarısız olan gönderim sayısı (WNS MPNS: 406 kabul edilemez).|Boyut yok|
|giden. MPNS. ınvalidnotificationformat|MPNS geçersiz bildirim biçimi|Sayı|Toplam|Bildirimin yükü çok büyük olduğu için başarısız olan gönderim sayısı.|Boyut yok|
|outgoing.mpns.channeldisconnected|MPNS kanalının bağlantısı kesildi|Sayı|Toplam|Kayıttaki Channelurı bağlantısı kesildiğinden başarısız olan gönderim sayısı (MPNS durumu: 412 bulunamadı).|Boyut yok|
|giden. MPNS. bırakıldı|MPNS bırakılan bildirimler|Sayı|Toplam|MPNS tarafından bırakılan gönderim sayısı (MPNS yanıt üst bilgisi: X-NotificationStatus: QueueFull veya gizlendi).|Boyut yok|
|outgoing.mpns.pnserror|MPNS hataları|Sayı|Toplam|MPNS ile iletişim kurulurken oluşan hatalardan dolayı başarısız olan gönderim sayısı.|Boyut yok|
|outgoing.mpns.authenticationerror|MPNS kimlik doğrulama hataları|Sayı|Toplam|PNS belirtilen kimlik bilgilerini kabul etmediğinden veya kimlik bilgileri engellendiğinden başarısız olan gönderim sayısı.|Boyut yok|
|notificationhub. gönderimleri|Tüm giden bildirimler|Sayı|Toplam|Bildirim Hub 'ının tüm giden bildirimleri|Boyut yok|
|gelen. ALL. Requests|Tüm gelen Istekler|Sayı|Toplam|Bir Bildirim Hub 'ı için toplam gelen istek sayısı|Boyut yok|
|gelen. tüm. failedistekleri|Tüm gelen başarısız Istekler|Sayı|Toplam|Bir Bildirim Hub 'ı için toplam gelen başarısız istek sayısı|Boyut yok|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. Operationalınsights/çalışma alanları

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Average_% boş ınomdes|% Boş ınomdes|Sayı|Ortalama|Average_% boş ınomdes|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|% Average_ boş alan|% Boş alan|Sayı|Ortalama|% Average_ boş alan|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% kullanılan ınomdes|% Kullanılan ınomdes|Sayı|Ortalama|Average_% kullanılan ınomdes|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Kullanılan Average_ alan yüzdesi|% Kullanılan alan|Sayı|Ortalama|Kullanılan Average_ alan yüzdesi|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Disk okunan bayt/sn|Disk Okuma Bayt/sn|Sayı|Ortalama|Average_Disk okunan bayt/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Okuma/sn Average_Disk|Disk Okuma/sn|Sayı|Ortalama|Okuma/sn Average_Disk|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Disk aktarım sayısı/sn|Disk aktarımı/sn|Sayı|Ortalama|Average_Disk aktarım sayısı/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Disk yazma bayt/sn|Disk Yazma Bayt/sn|Sayı|Ortalama|Average_Disk yazma bayt/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Disk yazma/sn|Disk Yazma/sn|Sayı|Ortalama|Average_Disk yazma/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Free megabayt|Boş megabayt|Sayı|Ortalama|Average_Free megabayt|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Logical disk bayt/sn|Mantıksal Disk Bayt/sn|Sayı|Ortalama|Average_Logical disk bayt/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Kullanılabilir% Average_ bellek|% Kullanılabilir bellek|Sayı|Ortalama|Kullanılabilir% Average_ bellek|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% kullanılabilir takas alanı|% Kullanılabilir takas alanı|Sayı|Ortalama|Average_% kullanılabilir takas alanı|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Kullanılan% Average_ bellek|% Kullanılan bellek|Sayı|Ortalama|Kullanılan% Average_ bellek|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% kullanılan takas alanı|% Kullanılan takas alanı|Sayı|Ortalama|Average_% kullanılan takas alanı|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Available MBayt bellek|Kullanılabilir MBayt belleği|Sayı|Ortalama|Average_Available MBayt bellek|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Available MBayt takas|Kullanılabilir MBayt takas|Sayı|Ortalama|Average_Available MBayt takas|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Okuma/sn Average_Page|Sayfa Okuma/sn|Sayı|Ortalama|Okuma/sn Average_Page|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Page yazma/sn|Sayfa yazma/sn|Sayı|Ortalama|Average_Page yazma/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Pages/sn|Sayfa/sn|Sayı|Ortalama|Average_Pages/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Used MBayt alanı değiştirme|Kullanılan MBayt değiştirme alanı|Sayı|Ortalama|Average_Used MBayt alanı değiştirme|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Used belleği MBayt|Kullanılan bellek MBayt|Sayı|Ortalama|Average_Used belleği MBayt|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Total aktarılan baytlar|Aktarılan toplam bayt|Sayı|Ortalama|Average_Total aktarılan baytlar|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Total alınan bayt|Alınan toplam bayt sayısı|Sayı|Ortalama|Average_Total alınan bayt|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Total bayt|Toplam bayt sayısı|Sayı|Ortalama|Average_Total bayt|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Iletilen Average_Total paketleri|Aktarılan toplam paket sayısı|Sayı|Ortalama|Iletilen Average_Total paketleri|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Alınan Average_Total paketleri|Alınan toplam paket sayısı|Sayı|Ortalama|Alınan Average_Total paketleri|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Total RX hataları|Toplam RX hatası sayısı|Sayı|Ortalama|Average_Total RX hataları|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Total TX hataları|Toplam TX hatası sayısı|Sayı|Ortalama|Average_Total TX hataları|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Total çakışmaları|Toplam çakışma sayısı|Sayı|Ortalama|Average_Total çakışmaları|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Avg. Disk sn/Okuma|Ortalama Disk sn/okuma|Sayı|Ortalama|Average_Avg. Disk sn/Okuma|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Avg. Disk sn/Aktarım|Ortalama Disk sn/Aktarım|Sayı|Ortalama|Average_Avg. Disk sn/Aktarım|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Avg. Disk sn/yazma|Ortalama Disk sn/yazma|Sayı|Ortalama|Average_Avg. Disk sn/yazma|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Physical disk bayt/sn|Fiziksel disk bayt/sn|Sayı|Ortalama|Average_Physical disk bayt/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Pct ayrıcalıklı saat|Ayrıcalıklı zaman yüzdesi|Sayı|Ortalama|Average_Pct ayrıcalıklı saat|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Pct Kullanıcı saati|Kullanım süresi yüzdesi|Sayı|Ortalama|Average_Pct Kullanıcı saati|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Used belleği Kbayt|Kullanılan bellek Kbayt|Sayı|Ortalama|Average_Used belleği Kbayt|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Virtual paylaşılan bellek|Sanal paylaşılan bellek|Sayı|Ortalama|Average_Virtual paylaşılan bellek|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% DPC Zamanı|% DPC Zamanı|Sayı|Ortalama|Average_% DPC Zamanı|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% boşta kalma süresi|% Boşta kalma süresi|Sayı|Ortalama|Average_% boşta kalma süresi|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Kesme zamanı Average_%|Kesme zamanı yüzdesi|Sayı|Ortalama|Kesme zamanı Average_%|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% GÇ bekleme süresi|% GÇ bekleme süresi|Sayı|Ortalama|Average_% GÇ bekleme süresi|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% Iyi zaman|% İyi zaman|Sayı|Ortalama|Average_% Iyi zaman|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% ayrıcalıklı zaman|% Ayrıcalıklı zaman|Sayı|Ortalama|Average_% ayrıcalıklı zaman|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% Işlemci zamanı|% İşlemci zamanı|Sayı|Ortalama|Average_% Işlemci zamanı|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% Kullanıcı Zamanı|% Kullanıcı Zamanı|Sayı|Ortalama|Average_% Kullanıcı Zamanı|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Fiziksel bellek Average_Free|Boş fiziksel bellek|Sayı|Ortalama|Fiziksel bellek Average_Free|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Disk belleği dosyalarında Average_Free alanı|Disk belleği dosyalarındaki boş alan|Sayı|Ortalama|Disk belleği dosyalarında Average_Free alanı|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Sanal bellek Average_Free|Boş sanal bellek|Sayı|Ortalama|Sanal bellek Average_Free|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Processes|İşlemler|Sayı|Ortalama|Average_Processes|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Disk belleği dosyalarında depolanan Average_Size|Disk belleği dosyalarında depolanan boyut|Sayı|Ortalama|Disk belleği dosyalarında depolanan Average_Size|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Uptime|Çalışma Süresi|Sayı|Ortalama|Average_Uptime|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Users|Kullanıcılar|Sayı|Ortalama|Average_Users|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Avg. Disk sn/Okuma|Ortalama Disk sn/okuma|Sayı|Ortalama|Average_Avg. Disk sn/Okuma|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Avg. Disk sn/yazma|Ortalama Disk sn/yazma|Sayı|Ortalama|Average_Avg. Disk sn/yazma|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Current disk kuyruğu uzunluğu|Geçerli disk sırası uzunluğu|Sayı|Ortalama|Average_Current disk kuyruğu uzunluğu|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Okuma/sn Average_Disk|Disk Okuma/sn|Sayı|Ortalama|Okuma/sn Average_Disk|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Disk aktarım sayısı/sn|Disk aktarımı/sn|Sayı|Ortalama|Average_Disk aktarım sayısı/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Disk yazma/sn|Disk Yazma/sn|Sayı|Ortalama|Average_Disk yazma/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Free megabayt|Boş megabayt|Sayı|Ortalama|Average_Free megabayt|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|% Average_ boş alan|% Boş alan|Sayı|Ortalama|% Average_ boş alan|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Available MBayt|Kullanılabilir MBayt|Sayı|Ortalama|Average_Available MBayt|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|% Average_ kaydedilmiş bayt kullanımda|Kullanılan kaydedilmiş bayt yüzdesi|Sayı|Ortalama|% Average_ kaydedilmiş bayt kullanımda|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Alınan Average_Bytes/sn|Alınan Bayt/sn|Sayı|Ortalama|Alınan Average_Bytes/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Gönderilen Average_Bytes/sn|Gönderilen bayt/sn|Sayı|Ortalama|Gönderilen Average_Bytes/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Toplam Average_Bytes/sn|Toplam bayt/sn|Sayı|Ortalama|Toplam Average_Bytes/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% Işlemci zamanı|% İşlemci zamanı|Sayı|Ortalama|Average_% Işlemci zamanı|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Processor kuyruğu uzunluğu|İşlemci kuyruğu uzunluğu|Sayı|Ortalama|Average_Processor kuyruğu uzunluğu|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Sinyal|Sinyal|Sayı|Toplam|Sinyal|Computer, OSType, Version, SourceComputerId|
|Güncelleştirme|Güncelleştirme|Sayı|Ortalama|Güncelleştirme|Bilgisayar, ürün, sınıflandırma, UpdateState, Isteğe bağlı, onaylanan|
|Olay|Olay|Sayı|Ortalama|Olay|Kaynak, olay günlüğü, bilgisayar, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. Powerbiadanmış/kapasiteler

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|QueryDuration|Sorgu süresi|Milisaniye|Ortalama|Son aralıktaki DAX sorgu süresi|Boyut yok|
|QueryPoolJobQueueLength|İş parçacıkları: sorgu havuzu iş kuyruğu uzunluğu|Sayı|Ortalama|Sorgu iş parçacığı havuzu kuyruğundaki iş sayısı.|Boyut yok|
|qpu_high_utilization_metric|QPU yüksek kullanım|Sayı|Toplam|Son dakikada QPU yüksek kullanımı, yüksek QPU kullanımı Için 1, aksi durumda 0|Boyut yok|
|memory_metric|Bellek|Bayt|Ortalama|Bellek. A1 için 0-3 GB, a2 için 0-5 GB, a3 için 0-10 GB, A4 için 0-25 GB, A6 için 0-50 GB ve A6 için 0-100 GB|Boyut yok|
|memory_thrashing_metric|Bellek dağılması|Yüzde|Ortalama|Ortalama bellek miktarı.|Boyut yok|

## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/Namespace

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|ListenerConnections-başarılı|ListenerConnections-başarılı|Sayı|Toplam|Microsoft. Relay için başarılı bir ListenerConnections.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Sayı|Toplam|Microsoft. Relay için ListenerConnections 'da ClientError.|EntityName|
|ListenerConnections-Sunucuhatası|ListenerConnections-Sunucuhatası|Sayı|Toplam|Microsoft. Relay için ListenerConnections 'da ServerError.|EntityName|
|SenderConnections-başarılı|SenderConnections-başarılı|Sayı|Toplam|Microsoft. Relay için başarılı SenderConnections.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Sayı|Toplam|Microsoft. Relay için SenderConnections 'ta ClientError.|EntityName|
|SenderConnections-Sunucuhatası|SenderConnections-Sunucuhatası|Sayı|Toplam|Microsoft. Relay için SenderConnections 'ta ServerError.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Sayı|Toplam|Microsoft. Relay için toplam ListenerConnections.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Sayı|Toplam|Microsoft. Relay için toplam SenderConnections isteği.|EntityName|
|ActiveConnection sayısı|ActiveConnection sayısı|Sayı|Toplam|Microsoft. Relay için toplam ActiveConnections.|EntityName|
|ActiveListeners|ActiveListeners|Sayı|Toplam|Microsoft. Relay için toplam ActiveListeners.|EntityName|
|BytesTransferred|BytesTransferred|Sayı|Toplam|Microsoft. Relay için toplam BytesTransferred.|EntityName|
|Listenerdisconnect|Listenerdisconnect|Sayı|Toplam|Microsoft. Relay için toplam Listenerdisconnect.|EntityName|
|Gönderenin bağlantısı kesiliyor|Gönderenin bağlantısı kesiliyor|Sayı|Toplam|Microsoft. Relay için toplam Senderdisconnect.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|SearchLatency|Arama gecikmesi|Saniye|Ortalama|Arama hizmeti için Ortalama arama gecikmesi|Boyut yok|
|SearchQueriesPerSecond|Sorgu arama/saniye|Sayaçpersaniye|Ortalama|Arama hizmeti için saniye başına arama sorgusu|Boyut yok|
|Tüm Searchqueriespercentage 'ı kısıtlar|Kısıtlanmış arama sorguları yüzdesi|Yüzde|Ortalama|Arama hizmeti için kısıtlanan arama sorgularının yüzdesi|Boyut yok|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|SuccessfulRequests|Başarılı Istekler (Önizleme)|Sayı|Toplam|Bir ad alanı için toplam başarılı istek (Önizleme)|EntityName|
|ServerErrors|Sunucu hataları. (Önizleme)|Sayı|Toplam|Microsoft. ServiceBus için sunucu hataları. (Önizleme)|EntityName|
|UserErrors|Kullanıcı hataları. (Önizleme)|Sayı|Toplam|Microsoft. ServiceBus için Kullanıcı hataları. (Önizleme)|EntityName|
|OperationName|Kısıtlanmış Istekler. (Önizleme)|Sayı|Toplam|Microsoft. ServiceBus için kısıtlanan Istekler. (Önizleme)|EntityName|
|Incomingrequests|Gelen Istekler (Önizleme)|Sayı|Toplam|Microsoft. ServiceBus için gelen Istekler. (Önizleme)|EntityName|
|Incomingmessages|Gelen Iletiler (Önizleme)|Sayı|Toplam|Microsoft. ServiceBus için gelen Iletiler. (Önizleme)|EntityName|
|OutgoingMessages|Giden Iletiler (Önizleme)|Sayı|Toplam|Microsoft. ServiceBus için giden Iletiler. (Önizleme)|EntityName|
|ActiveConnection sayısı|ActiveConnections (Önizleme)|Sayı|Toplam|Microsoft. ServiceBus için toplam etkin bağlantı sayısı. (Önizleme)|Boyut yok|
|Boyut|Boyut (Önizleme)|Bayt|Ortalama|Bir kuyruğun/konunun bayt cinsinden boyutu. (Önizleme)|EntityName|
|İletiler|Kuyruktaki/konudaki iletilerin sayısı. (Önizleme)|Sayı|Ortalama|Kuyruktaki/konudaki iletilerin sayısı. (Önizleme)|EntityName|
|ActiveMessages|Bir kuyruktaki/konudaki etkin ileti sayısı. (Önizleme)|Sayı|Ortalama|Bir kuyruktaki/konudaki etkin ileti sayısı. (Önizleme)|EntityName|
|DeadletteredMessages|Bir kuyruktaki/konudaki kullanılmayan iletilerin sayısı. (Önizleme)|Sayı|Ortalama|Bir kuyruktaki/konudaki kullanılmayan iletilerin sayısı. (Önizleme)|EntityName|
|ScheduledMessages|Bir kuyruktaki/konudaki zamanlanan ileti sayısı. (Önizleme)|Sayı|Ortalama|Bir kuyruktaki/konudaki zamanlanan ileti sayısı. (Önizleme)|EntityName|
|CPUXNS|Ad alanı başına CPU kullanımı|Yüzde|Maksimum|Service Bus Premium ad alanı CPU kullanım ölçümü|Boyut yok|
|WSXNS|Ad alanı başına bellek boyutu kullanımı|Yüzde|Maksimum|Service Bus Premium ad alanı bellek kullanım ölçümü|Boyut yok|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. Servicefabrickafes/uygulamaları

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Ayrılatedcpu|Ayrılatedcpu|Sayı|Ortalama|Miliçekirdekte bu kapsayıcıya ayrılan CPU|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|Ayrılan bellek|Ayrılan bellek|Bayt|Ortalama|Bu kapsayıcıya MB cinsinden ayrılan bellek|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Sayı|Ortalama|Miliçekirdekte gerçek CPU kullanımı|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bayt|Ortalama|MB cinsinden gerçek bellek kullanımı|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|Cpukullanımı|Cpukullanımı|Yüzde|Ortalama|Ayrılan CPU yüzdesi olarak bu kapsayıcı için CPU kullanımı|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|Memoryutilileştirme|Memoryutilileştirme|Yüzde|Ortalama|Ayrılan CPU yüzdesi olarak bu kapsayıcı için CPU kullanımı|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Sayı|Ortalama|Service Fabric kafes uygulamasının durumu|ApplicationName, durum|
|ServiceStatus|ServiceStatus|Sayı|Ortalama|Service Fabric kafes uygulamasındaki bir hizmetin sistem durumu|ApplicationName, durum, HizmetAdı|
|Servicereperepstatus|Servicereperepstatus|Sayı|Ortalama|Service Fabric kafes uygulamasındaki bir hizmet çoğaltmasının sistem durumu|ApplicationName, Status, HizmetAdı, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Sayı|Ortalama|Service Fabric kafes uygulamasındaki kapsayıcının durumu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, durum|
|RestartCount|RestartCount|Sayı|Ortalama|Service Fabric kafes uygulamasındaki bir kapsayıcının yeniden başlatma sayısı|ApplicationName, Status, HizmetAdı, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/SignalR

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|ConnectionCount|Bağlantı sayısı|Sayı|Maksimum|Kullanıcı bağlantısı miktarı.|Uç Nokta|
|MessageCount|İleti sayısı|Sayı|Toplam|Toplam ileti miktarı.|Boyut yok|
|Inboundtraffic|Gelen Trafik|Bayt|Toplam|Hizmetin gelen trafiği|Boyut yok|
|OutboundTraffic|Giden Trafik|Bayt|Toplam|Hizmetin giden trafiği|Boyut yok|
|UserErrors|Kullanıcı hataları|Yüzde|Maksimum|Kullanıcı hatalarının yüzdesi|Boyut yok|
|SystemErrors|Sistem hataları|Yüzde|Maksimum|Sistem hatalarının yüzdesi|Boyut yok|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|allocated_data_storage|Ayrılan veri alanı|Bayt|Ortalama|Ayrılan veri alanı. Veri ambarlarında geçerli değildir.|Boyut yok|
|app_cpu_billed|Faturalandırılan uygulama CPU 'SU|Sayı|Toplam|Uygulama CPU 'SU faturalandırılır. Sunucusuz veritabanları için geçerlidir.|Boyut yok|
|app_cpu_percent|Uygulama CPU yüzdesi|Yüzde|Ortalama|Uygulama CPU yüzdesi. Sunucusuz veritabanları için geçerlidir.|Boyut yok|
|app_memory_percent|Kullanılan uygulama belleği yüzdesi|Yüzde|Ortalama|Kullanılan uygulama belleği yüzdesi. Sunucusuz veritabanları için geçerlidir.|Boyut yok|
|blocked_by_firewall|Güvenlik duvarı tarafından engellendi|Sayı|Toplam|Güvenlik duvarı tarafından engellendi|Boyut yok|
|cache_hit_percent|İsabetli önbellek okuması yüzdesi|Yüzde|Maksimum|İsabetli önbellek okuması yüzdesi. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|cache_used_percent|Önbellek kullanılan yüzde|Yüzde|Maksimum|Önbellek kullanım yüzdesi. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|connection_failed|Başarısız Bağlantılar|Sayı|Toplam|Başarısız Bağlantılar|Boyut yok|
|connection_successful|Başarılı bağlantılar|Sayı|Toplam|Başarılı bağlantılar|Boyut yok|
|cpu_percent|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|Boyut yok|
|cpu_limit|CPU sınırı|Sayı|Ortalama|CPU sınırı. Sanal çekirdek tabanlı veritabanları için geçerlidir.|Boyut yok|
|cpu_used|Kullanılan CPU|Sayı|Ortalama|Kullanılan CPU. Sanal çekirdek tabanlı veritabanları için geçerlidir.|Boyut yok|
|Çözül|Çık|Sayı|Toplam|Çık. Veri ambarlarında geçerli değildir.|Boyut yok|
|diff_backup_size_bytes|Değişiklik yedekleme depolama boyutu|Bayt|Maksimum|Toplu fark yedekleme depolama boyutu. Sanal çekirdek tabanlı veritabanları için geçerlidir. Hiper ölçekli veritabanları için geçerli değildir.|Boyut yok|
|dtu_limit|DTU sınırı|Sayı|Ortalama|DTU sınırı. DTU tabanlı veritabanları için geçerlidir.|Boyut yok|
|dtu_consumption_percent|DTU yüzdesi|Yüzde|Ortalama|DTU yüzdesi. DTU tabanlı veritabanları için geçerlidir.|Boyut yok|
|dtu_used|Kullanılan DTU|Sayı|Ortalama|DTU kullanıldı. DTU tabanlı veritabanları için geçerlidir.|Boyut yok|
|dw_cpu_percent|DW düğüm düzeyi CPU yüzdesi|Yüzde|Ortalama|DW düğüm düzeyi CPU yüzdesi|DwLogicalNodeId|
|dw_physical_data_read_percent|DW düğüm düzeyi veri GÇ yüzdesi|Yüzde|Ortalama|DW düğüm düzeyi veri GÇ yüzdesi|DwLogicalNodeId|
|dwu_consumption_percent|DWU yüzdesi|Yüzde|Maksimum|DWU yüzdesi. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|dwu_limit|DWU sınırı|Sayı|Maksimum|DWU sınırı. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|dwu_used|Kullanılan DWU|Sayı|Maksimum|DWU kullanıldı. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|full_backup_size_bytes|Tam yedekleme depolama boyutu|Bayt|Maksimum|Toplu tam yedekleme depolama boyutu. Sanal çekirdek tabanlı veritabanları için geçerlidir. Hiper ölçekli veritabanları için geçerli değildir.|Boyut yok|
|local_tempdb_usage_percent|Yerel tempdb yüzdesi|Yüzde|Ortalama|Yerel tempdb yüzdesi. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|log_backup_size_bytes|Günlük yedekleme depolama boyutu|Bayt|Maksimum|Toplu günlük yedekleme depolama boyutu. Sanal çekirdek tabanlı veritabanları için geçerlidir. Hiper ölçekli veritabanları için geçerli değildir.|Boyut yok|
|log_write_percent|Günlük GÇ yüzdesi|Yüzde|Ortalama|Günlük GÇ yüzdesi. Veri ambarlarında geçerli değildir.|Boyut yok|
|physical_data_read_percent|Veri G/Ç yüzdesi|Yüzde|Ortalama|Veri G/Ç yüzdesi|Boyut yok|
|sessions_percent|Oturum yüzdesi|Yüzde|Ortalama|Oturum yüzdesi. Veri ambarlarında geçerli değildir.|Boyut yok|
|sqlserver_process_core_percent|SQL Server işlem çekirdeği yüzdesi|Yüzde|Maksimum|Bu ölçüm bir yer tutucudur ve şu anda doldurulmamış.|Boyut yok|
|sqlserver_process_memory_percent|SQL Server işlem belleği yüzdesi|Yüzde|Maksimum|Bu ölçüm bir yer tutucudur ve şu anda doldurulmamış.|Boyut yok|
|depolama|Kullanılan veri alanı|Bayt|Maksimum|Toplam veritabanı boyutu. Veri ambarlarında geçerli değildir.|Boyut yok|
|storage_percent|Kullanılan veri alanı yüzdesi|Yüzde|Maksimum|Veritabanı boyutu yüzdesi. Veri ambarları veya hiper ölçekli veritabanları için geçerli değildir.|Boyut yok|
|tempdb_data_size|Tempdb veri dosyası boyutu kilobayt|Sayı|Maksimum|Tempdb veri dosyası boyutu kilobayt. Veri ambarlarında geçerli değildir. Bu ölçüm, DTU tabanlı satın alma modelleri için vCore satın alma modeli veya 100 DTU ve üzeri kullanan veritabanlarında kullanılabilir.|Boyut yok|
|tempdb_log_size|Tempdb günlük dosyası boyutu kilobayt|Sayı|Maksimum|Tempdb günlük dosyası boyutu kilobayt. Veri ambarlarında geçerli değildir. Bu ölçüm, DTU tabanlı satın alma modelleri için vCore satın alma modeli veya 100 DTU ve üzeri kullanan veritabanlarında kullanılabilir.|Boyut yok|
|tempdb_log_used_percent|Kullanılan tempdb günlüğü yüzdesi|Yüzde|Maksimum|Kullanılan tempdb günlüğü yüzdesi. Veri ambarlarında geçerli değildir. Bu ölçüm, DTU tabanlı satın alma modelleri için vCore satın alma modeli veya 100 DTU ve üzeri kullanan veritabanlarında kullanılabilir.|Boyut yok|
|workers_percent|Çalışan yüzdesi|Yüzde|Ortalama|Çalışan yüzdesi. Veri ambarlarında geçerli değildir.|Boyut yok|
|xtp_storage_percent|Bellek içi OLTP depolama yüzdesi|Yüzde|Ortalama|Bellek içi OLTP depolaması yüzdesi. Veri ambarlarında geçerli değildir.|Boyut yok|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|allocated_data_storage|Ayrılan veri alanı|Bayt|Ortalama|Ayrılan veri alanı|Boyut yok|
|allocated_data_storage_percent|Ayrılan veri alanı yüzdesi|Yüzde|Maksimum|Ayrılan veri alanı yüzdesi|Boyut yok|
|cpu_limit|CPU sınırı|Sayı|Ortalama|CPU sınırı. Sanal çekirdek tabanlı elastik havuzlar için geçerlidir.|Boyut yok|
|cpu_percent|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|Boyut yok|
|cpu_used|Kullanılan CPU|Sayı|Ortalama|Kullanılan CPU. Sanal çekirdek tabanlı elastik havuzlar için geçerlidir.|Boyut yok|
|dtu_consumption_percent|DTU yüzdesi|Yüzde|Ortalama|DTU yüzdesi. DTU tabanlı elastik havuzlar için geçerlidir.|Boyut yok|
|eDTU_limit|eDTU sınırı|Sayı|Ortalama|eDTU sınırı. DTU tabanlı elastik havuzlar için geçerlidir.|Boyut yok|
|eDTU_used|eDTU kullanıldı|Sayı|Ortalama|eDTU kullanıldı. DTU tabanlı elastik havuzlar için geçerlidir.|Boyut yok|
|log_write_percent|Günlük GÇ yüzdesi|Yüzde|Ortalama|Günlük GÇ yüzdesi|Boyut yok|
|physical_data_read_percent|Veri G/Ç yüzdesi|Yüzde|Ortalama|Veri G/Ç yüzdesi|Boyut yok|
|sessions_percent|Oturum yüzdesi|Yüzde|Ortalama|Oturum yüzdesi|Boyut yok|
|storage_limit|Veri boyutu üst sınırı|Bayt|Ortalama|Depolama sınırı|Boyut yok|
|storage_percent|Kullanılan veri alanı yüzdesi||Yüzde|Ortalama|Depolama yüzdesi|Boyut yok|
|storage_used|Kullanılan veri alanı|Bayt|Ortalama|Kullanılan depolama|Boyut yok|
|sqlserver_process_core_percent|SQL Server işlem çekirdeği yüzdesi|Yüzde|Maksimum|Bu ölçüm bir yer tutucudur ve şu anda doldurulmamış.|Boyut yok|
|sqlserver_process_memory_percent|SQL Server işlem belleği yüzdesi|Yüzde|Maksimum|Bu ölçüm bir yer tutucudur ve şu anda doldurulmamış.|Boyut yok|
|tempdb_data_size|Tempdb veri dosyası boyutu kilobayt|Sayı|Maksimum|Tempdb veri dosyası boyutu kilobayt. Veri ambarlarında geçerli değildir. Bu ölçüm, DTU tabanlı satın alma modelleri için vCore satın alma modeli veya 100 DTU ve üzeri kullanan veritabanlarında kullanılabilir.|Boyut yok|
|tempdb_log_size|Tempdb günlük dosyası boyutu kilobayt|Sayı|Maksimum|Tempdb günlük dosyası boyutu kilobayt. Veri ambarlarında geçerli değildir. Bu ölçüm, DTU tabanlı satın alma modelleri için vCore satın alma modeli veya 100 DTU ve üzeri kullanan veritabanlarında kullanılabilir.|Boyut yok|
|tempdb_log_used_percent|Kullanılan tempdb günlüğü yüzdesi|Yüzde|Maksimum|Kullanılan tempdb günlüğü yüzdesi. Veri ambarlarında geçerli değildir. Bu ölçüm, DTU tabanlı satın alma modelleri için vCore satın alma modeli veya 100 DTU ve üzeri kullanan veritabanlarında kullanılabilir.|Boyut yok|
|workers_percent|Çalışan yüzdesi|Yüzde|Ortalama|Çalışan yüzdesi|Boyut yok|
|xtp_storage_percent|Bellek içi OLTP depolama yüzdesi|Yüzde|Ortalama|Bellek içi OLTP depolama yüzdesi|Boyut yok|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/ManagedInstances

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|avg_cpu_percent|Ortalama CPU yüzdesi|Yüzde|Ortalama|Ortalama CPU yüzdesi|Boyut yok|
|io_bytes_read|Okunan GÇ baytları|Bayt|Ortalama|Okunan GÇ baytları|Boyut yok|
|io_requests|GÇ istek sayısı|Sayı|Ortalama|GÇ istek sayısı|Boyut yok|
|io_bytes_written|Yazılan GÇ baytları|Bayt|Ortalama|Yazılan GÇ baytları|Boyut yok|
|reserved_storage_mb|Ayrılan depolama alanı|Sayı|Ortalama|Ayrılan depolama alanı|Boyut yok|
|storage_space_used_mb|Kullanılan depolama alanı|Sayı|Ortalama|Kullanılan depolama alanı|Boyut yok|
|virtual_core_count|Sanal çekirdek sayısı|Sayı|Ortalama|Sanal çekirdek sayısı|Boyut yok|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|UsedCapacity|Kullanılan kapasite|Bayt|Ortalama|Hesabın kullanılan kapasitesi|Boyut yok|
|İşlemler|İşlemler|Sayı|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı tür yanıtların sayısı için ResponseType boyutunu kullanın.|Yanıt türü, coğrafi tür, ApiName, kimlik doğrulama|
|Giriş|Giriş|Bayt|Toplam|Bayt olarak giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Çıkış|Çıkış|Bayt|Toplam|Bayt cinsinden çıkış verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya çıkan ve Azure içinde çıkan verileri içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulaması|
|SuccessServerLatency|Başarı Sunucu Gecikme Süresi|Milisaniye|Ortalama|Milisaniye cinsinden başarılı bir isteği işlemek için Azure Depolama tarafından kullanılan ortalama gecikme süresi. Bu değer, AverageE2ELatency öğesinde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Başarı E2e|Başarı E2E Gecikme Süresi|Milisaniye|Ortalama|Bir depolama hizmetine veya belirtilen API işlemi, milisaniye cinsinden gönderilen başarılı isteklerin ortalama uçtan uca gecikme. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Kullanılabilirlik|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmetinin veya belirtilen API işleminin kullanılabilirlik yüzdesi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulaması|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|BlobCapacity|Blob Kapasitesi|Bayt|Ortalama|Bayt olarak depolama hesabının Blob hizmeti tarafından kullanılan depolama miktarı.|BlobType, katman|
|BLOB sayısı|Blob Sayısı|Sayı|Toplam|Depolama hesabının Blob hizmetindeki Blob sayısı.|BlobType|       |BLOB sayısı|Blob Sayısı|Sayı|Ortalama|Depolama hesabının Blob hizmetindeki Blob sayısı.|BlobType, katman|
|ContainerCount|Blob Kapsayıcı Sayısı|Sayı|Ortalama|Depolama hesabının Blob hizmetindeki kapsayıcı sayısı.|Boyut yok|
|Dizin kapasitesi|Dizin kapasitesi|Bayt|Ortalama|Bayt cinsinden ADLS 2. (hiyerarşik) dizin tarafından kullanılan depolama miktarı.|Boyut yok|
|İşlemler|İşlemler|Sayı|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı tür yanıtların sayısı için ResponseType boyutunu kullanın.|Yanıt türü, coğrafi tür, ApiName, kimlik doğrulama|
|Giriş|Giriş|Bayt|Toplam|Bayt olarak giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Çıkış|Çıkış|Bayt|Toplam|Bayt cinsinden çıkış verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya çıkan ve Azure içinde çıkan verileri içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulaması|
|SuccessServerLatency|Başarı Sunucu Gecikme Süresi|Milisaniye|Ortalama|Milisaniye cinsinden başarılı bir isteği işlemek için Azure Depolama tarafından kullanılan ortalama gecikme süresi. Bu değer, AverageE2ELatency öğesinde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Başarı E2e|Başarı E2E Gecikme Süresi|Milisaniye|Ortalama|Bir depolama hizmetine veya belirtilen API işlemi, milisaniye cinsinden gönderilen başarılı isteklerin ortalama uçtan uca gecikme. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Kullanılabilirlik|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmetinin veya belirtilen API işleminin kullanılabilirlik yüzdesi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulaması|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Dosya kapasitesi|Dosya kapasitesi|Bayt|Ortalama|Bayt olarak depolama hesabının dosya hizmeti tarafından kullanılan depolama miktarı.|Boyut yok|
|FileCount|Dosya sayısı|Sayı|Ortalama|Depolama hesabının dosya hizmetindeki dosya sayısı.|Boyut yok|
|Dosya ShareCount|Dosya paylaşma sayısı|Sayı|Ortalama|Depolama hesabının dosya hizmetindeki dosya paylaşımlarının sayısı.|Boyut yok|
|İşlemler|İşlemler|Sayı|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı tür yanıtların sayısı için ResponseType boyutunu kullanın.|Yanıt türü, coğrafi tür, ApiName, kimlik doğrulama|
|Giriş|Giriş|Bayt|Toplam|Bayt olarak giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Çıkış|Çıkış|Bayt|Toplam|Bayt cinsinden çıkış verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya çıkan ve Azure içinde çıkan verileri içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulaması|
|SuccessServerLatency|Başarı Sunucu Gecikme Süresi|Milisaniye|Ortalama|Milisaniye cinsinden başarılı bir isteği işlemek için Azure Depolama tarafından kullanılan ortalama gecikme süresi. Bu değer, AverageE2ELatency öğesinde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Başarı E2e|Başarı E2E Gecikme Süresi|Milisaniye|Ortalama|Bir depolama hizmetine veya belirtilen API işlemi, milisaniye cinsinden gönderilen başarılı isteklerin ortalama uçtan uca gecikme. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Kullanılabilirlik|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmetinin veya belirtilen API işleminin kullanılabilirlik yüzdesi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulaması|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|QueueCapacity|Kuyruk Kapasitesi|Bayt|Ortalama|Bayt olarak depolama hesabının Kuyruk hizmeti tarafından kullanılan depolama miktarı.|Boyut yok|
|QueueCount|Kuyruk Sayısı|Sayı|Ortalama|Depolama hesabının Kuyruk hizmetindeki sıra sayısı.|Boyut yok|
|QueueMessageCount|Kuyruk İleti Sayısı|Sayı|Ortalama|Depolama hesabının Kuyruk hizmetindeki sıra iletilerinin yaklaşık sayısı.|Boyut yok|
|İşlemler|İşlemler|Sayı|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı tür yanıtların sayısı için ResponseType boyutunu kullanın.|Yanıt türü, coğrafi tür, ApiName, kimlik doğrulama|
|Giriş|Giriş|Bayt|Toplam|Bayt olarak giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Çıkış|Çıkış|Bayt|Toplam|Bayt cinsinden çıkış verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya çıkan ve Azure içinde çıkan verileri içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulaması|
|SuccessServerLatency|Başarı Sunucu Gecikme Süresi|Milisaniye|Ortalama|Milisaniye cinsinden başarılı bir isteği işlemek için Azure Depolama tarafından kullanılan ortalama gecikme süresi. Bu değer, AverageE2ELatency öğesinde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Başarı E2e|Başarı E2E Gecikme Süresi|Milisaniye|Ortalama|Bir depolama hizmetine veya belirtilen API işlemi, milisaniye cinsinden gönderilen başarılı isteklerin ortalama uçtan uca gecikme. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Kullanılabilirlik|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmetinin veya belirtilen API işleminin kullanılabilirlik yüzdesi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulaması|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|TableCapacity|Tablo Kapasitesi|Bayt|Ortalama|Bayt olarak depolama hesabının Tablo hizmeti tarafından kullanılan depolama miktarı.|Boyut yok|
|TableCount|Tablo Sayısı|Sayı|Ortalama|Depolama hesabının Tablo hizmetindeki tablo sayısı.|Boyut yok|
|TableEntityCount|Tablo Varlık Sayısı|Sayı|Ortalama|Depolama hesabının Tablo hizmetindeki tablo varlıklarının sayısı.|Boyut yok|
|İşlemler|İşlemler|Sayı|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı tür yanıtların sayısı için ResponseType boyutunu kullanın.|Yanıt türü, coğrafi tür, ApiName, kimlik doğrulama|
|Giriş|Giriş|Bayt|Toplam|Bayt olarak giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Çıkış|Çıkış|Bayt|Toplam|Bayt cinsinden çıkış verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya çıkan ve Azure içinde çıkan verileri içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulaması|
|SuccessServerLatency|Başarı Sunucu Gecikme Süresi|Milisaniye|Ortalama|Milisaniye cinsinden başarılı bir isteği işlemek için Azure Depolama tarafından kullanılan ortalama gecikme süresi. Bu değer, AverageE2ELatency öğesinde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Başarı E2e|Başarı E2E Gecikme Süresi|Milisaniye|Ortalama|Bir depolama hizmetine veya belirtilen API işlemi, milisaniye cinsinden gönderilen başarılı isteklerin ortalama uçtan uca gecikme. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Kullanılabilirlik|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmetinin veya belirtilen API işleminin kullanılabilirlik yüzdesi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulaması|

## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagessync/storageSyncServices

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Eşitleme oturumu sonucu|Sayı|Ortalama|Sunucu uç noktası, bulut uç noktası ile eşitleme oturumunu başarıyla tamamladığında 1 değerini günlüğe kaydeden ölçüm|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Eşitlenen dosyalar|Sayı|Toplam|Eşitlenen dosya sayısı|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Dosyalar eşitlenmiyor|Sayı|Toplam|Eşitleme başarısız olan dosya sayısı|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Eşitlenen baytlar|Bayt|Toplam|Eşitleme oturumları için aktarılan toplam dosya boyutu|SyncGroupName, ServerEndpointName, SyncDirection|
|Storagesyncserversinyal|Sunucu çevrimiçi durumu|Sayı|Maksimum|Kayıtlı sunucu, bulut uç noktasıyla bir sinyal başarıyla kaydetilişinde 1 değerini günlüğe kaydeden ölçüm|ServerName|
|Storagesyncyeniden hesaplama Liototalsizebytes|Bulut katmanlama geri çağırma|Bayt|Toplam|Sunucu tarafından geri çekilen toplam veri boyutu|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Resourcekullanım|SU Kullanım Yüzdesi|Yüzde|Maksimum|SU Kullanım Yüzdesi|LogicalName, PartitionID|
|Inputevents|Giriş Olayları|Sayı|Toplam|Giriş Olayları|LogicalName, PartitionID|
|Inputeventbytes|Giriş Olayı Bayt Sayısı|Bayt|Toplam|Giriş Olayı Bayt Sayısı|LogicalName, PartitionID|
|LateInputEvents|Geç Giriş Olayları|Sayı|Toplam|Geç Giriş Olayları|LogicalName, PartitionID|
|OutputEvents|Çıkış Olayları|Sayı|Toplam|Çıkış Olayları|LogicalName, PartitionID|
|Dönüştürme hataları|Veri Dönüştürme Hataları|Sayı|Toplam|Veri Dönüştürme Hataları|LogicalName, PartitionID|
|Hatalar|Çalışma Zamanı Hataları|Sayı|Toplam|Çalışma Zamanı Hataları|LogicalName, PartitionID|
|Droppedorayarlantedevents|Sıra dışı olaylar|Sayı|Toplam|Sıra dışı olaylar|LogicalName, PartitionID|
|AMLCalloutRequests|İşlev İstekleri|Sayı|Toplam|İşlev İstekleri|LogicalName, PartitionID|
|AMLCalloutFailedRequests|Başarısız İşlev İstekleri|Sayı|Toplam|Başarısız İşlev İstekleri|LogicalName, PartitionID|
|AMLCalloutInputEvents|İşlev Olayları|Sayı|Toplam|İşlev Olayları|LogicalName, PartitionID|
|DeserializationError|Giriş Serileştirme Kaldırma Hataları|Sayı|Toplam|Giriş Serileştirme Kaldırma Hataları|LogicalName, PartitionID|
|EarlyInputEvents|Erken Giriş Olayları|Sayı|Toplam|Erken Giriş Olayları|LogicalName, PartitionID|
|Outputsulu Markdelayseconds|Eşik Gecikmesi|Saniye|Maksimum|Eşik Gecikmesi|LogicalName, PartitionID|
|Inputeventssourcesbackgünlüğe kaydediliyor|Biriktirme Listesindeki Giriş Olayları|Sayı|Maksimum|Biriktirme Listesindeki Giriş Olayları|LogicalName, PartitionID|
|Inputeventssourcespersecond|Alınan Giriş Kaynakları|Sayı|Toplam|Alınan Giriş Kaynakları|LogicalName, PartitionID|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. Timeseriesınsights/ortamlar

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Inressreceived Iletileri|Giriş alınan Iletiler|Sayı|Toplam|Tüm Olay Hub 'ından veya IoT Hub olay kaynaklarından okunan ileti sayısı|Boyut yok|
|Inressreceiveınvalidmessages|Giriş geçersiz Iletiler aldı|Sayı|Toplam|Tüm Olay Hub 'ından veya IoT Hub olay kaynaklarından okunan geçersiz ileti sayısı|Boyut yok|
|Alınan baytlar|Alınan bayt sayısı|Bayt|Toplam|Tüm olay kaynaklarından okunan bayt sayısı|Boyut yok|
|IngressStoredBytes|Giriş depolanan baytlar|Bayt|Toplam|Başarılı bir şekilde işlenen ve sorgu için kullanılabilir olayların toplam boyutu|Boyut yok|
|Inressstoo Vents|Giriş saklı olayları|Sayı|Toplam|Sorgu için başarıyla işlenen ve kullanılabilir düzleştirilmiş olay sayısı|Boyut yok|
|Inressreceived Iletizamangecikmesi|Alınan Ileti zaman gecikmesi|Saniye|Maksimum|İleti olay kaynağında sıraya alındığı zaman ve giriş sırasında işlendiği zaman arasındaki fark|Boyut yok|
|Inressreceived Iletisayaçgecikmesi|Alınan Ileti sayısı gecikmesi|Sayı|Ortalama|Olay kaynak bölümünde en son sıraya alınan iletinin sıra numarası ve giriş dosyasında işlenen ileti sırası arasındaki fark|Boyut yok|
|WarmStorageMaxProperties|Isınma depolama en yüksek özellikleri|Sayı|Maksimum|S1/S2 SKU 'SU için ortam tarafından izin verilen en fazla özellik sayısı ve PAYG SKU 'SU için ısınma tarafından izin verilen en fazla özellik sayısı|Boyut yok|
|WarmStorageUsedProperties|Sıcak depolama kullanılan özellikler |Sayı|Maksimum|S1/S2 SKU 'SU için ortam tarafından kullanılan özellik sayısı ve PAYG SKU 'SU için sıcak mağaza tarafından kullanılan özellik sayısı|Boyut yok|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. Timeseriesınsights/ortamlar/EventSources

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Inressreceived Iletileri|Giriş alınan Iletiler|Sayı|Toplam|Olay kaynağından okunan ileti sayısı|Boyut yok|
|Inressreceiveınvalidmessages|Giriş geçersiz Iletiler aldı|Sayı|Toplam|Olay kaynağından okunan geçersiz ileti sayısı|Boyut yok|
|Alınan baytlar|Alınan bayt sayısı|Bayt|Toplam|Olay kaynağından okunan bayt sayısı|Boyut yok|
|IngressStoredBytes|Giriş depolanan baytlar|Bayt|Toplam|Başarılı bir şekilde işlenen ve sorgu için kullanılabilir olayların toplam boyutu|Boyut yok|
|Inressstoo Vents|Giriş saklı olayları|Sayı|Toplam|Sorgu için başarıyla işlenen ve kullanılabilir düzleştirilmiş olay sayısı|Boyut yok|
|Inressreceived Iletizamangecikmesi|Alınan Ileti zaman gecikmesi|Saniye|Maksimum|İleti olay kaynağında sıraya alındığı zaman ve giriş sırasında işlendiği zaman arasındaki fark|Boyut yok|
|Inressreceived Iletisayaçgecikmesi|Alınan Ileti sayısı gecikmesi|Sayı|Ortalama|Olay kaynak bölümünde en son sıraya alınan iletinin sıra numarası ve giriş dosyasında işlenen ileti sırası arasındaki fark|Boyut yok|
|WarmStorageMaxProperties|Isınma depolama en yüksek özellikleri|Sayı|Maksimum|S1/S2 SKU 'SU için ortam tarafından izin verilen en fazla özellik sayısı ve PAYG SKU 'SU için ısınma tarafından izin verilen en fazla özellik sayısı|Boyut yok|
|WarmStorageUsedProperties|Sıcak depolama kullanılan özellikler |Sayı|Maksimum|S1/S2 SKU 'SU için ortam tarafından kullanılan özellik sayısı ve PAYG SKU 'SU için sıcak mağaza tarafından kullanılan özellik sayısı|Boyut yok|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. Vmwarechoparlör basit/virtualMachines

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk okuma bayt/sn|BytesPerSecond|Ortalama|Örnek süre içinde okuma işlemleri nedeniyle ortalama disk aktarım hızı.|Boyut yok|
|DiskWriteBytesPerSecond|Disk yazma bayt/sn|BytesPerSecond|Ortalama|Örnek süre içinde yazma işlemleri nedeniyle ortalama disk aktarım hızı.|Boyut yok|
|Disk okuma bayt sayısı|Disk okuma bayt sayısı|Bayt|Toplam|Örnek süre boyunca okuma işlemleri nedeniyle toplam disk aktarım hızı.|Boyut yok|
|Disk yazma baytları|Disk yazma baytları|Bayt|Toplam|Örnek süre içinde yazma işlemleri nedeniyle toplam disk aktarım hızı.|Boyut yok|
|DiskReadOperations|Disk okuma Işlemleri|Sayı|Toplam|Önceki örnek dönemdeki GÇ okuma işlemlerinin sayısı. Bu işlemlerin değişken boyutlandırılabilir olabileceğini unutmayın.|Boyut yok|
|DiskWriteOperations|Disk yazma Işlemleri|Sayı|Toplam|Önceki örnek dönemdeki GÇ Yazma işlemlerinin sayısı. Bu işlemlerin değişken boyutlandırılabilir olabileceğini unutmayın.|Boyut yok|
|Disk okuma Işlemi/sn|Disk okuma Işlemi/sn|Sayaçpersaniye|Ortalama|Önceki örnek dönemdeki ortalama GÇ okuma işlemi sayısı. Bu işlemlerin değişken boyutlandırılabilir olabileceğini unutmayın.|Boyut yok|
|Disk yazma Işlemi/sn|Disk yazma Işlemi/sn|Sayaçpersaniye|Ortalama|Önceki örnek dönemdeki ortalama GÇ yazma işlemi sayısı. Bu işlemlerin değişken boyutlandırılabilir olabileceğini unutmayın.|Boyut yok|
|DiskReadLatency|Disk okuma gecikmesi|Milisaniye|Ortalama|Toplam okuma gecikmesi. Cihaz ve çekirdek okuma gecikme sürelerinin toplamı.|Boyut yok|
|DiskWriteLatency|Disk yazma gecikmesi|Milisaniye|Ortalama|Toplam yazma gecikmesi. Cihaz ve çekirdek yazma gecikme sürelerinin toplamı.|Boyut yok|
|Networkınbytespersecond|Bayt/sn cinsinden ağ|BytesPerSecond|Ortalama|Alınan trafik için Ortalama ağ performansı.|Boyut yok|
|NetworkOutBytesPerSecond|Ağ çıkış bayt/sn|BytesPerSecond|Ortalama|Aktarılan trafik için Ortalama ağ aktarım hızı.|Boyut yok|
|Ağ Girişi|Ağ Girişi|Bayt|Toplam|Alınan trafik için toplam ağ aktarım hızı.|Boyut yok|
|Ağ Çıkışı|Ağ Çıkışı|Bayt|Toplam|Aktarılan trafik için toplam ağ aktarım hızı.|Boyut yok|
|MemoryUsed|Kullanılan bellek|Bayt|Ortalama|VM tarafından kullanılan makine belleği miktarı.|Boyut yok|
|Memoryverildi|Verilen bellek|Bayt|Ortalama|VM 'ye konak tarafından verilen bellek miktarı. Bir kez dokunulmadan ve VMkernel belleğe ihtiyaç duyuncaya kadar bellek verilebilene kadar bu konağa bellek verilmez.|Boyut yok|
|MemoryActive|Bellek etkin|Bayt|Ortalama|Son küçük zaman penceresinde VM tarafından kullanılan bellek miktarı. Bu, VM 'nin şu anda ihtiyacı olan bellek miktarını "doğru" sayısıdır. Ek olarak kullanılmayan bellek, konuğun performansına hiçbir etkilenmeden dışarı veya ballooned olabilir.|Boyut yok|
|CPU yüzdesi|CPU yüzdesi|Yüzde|Ortalama|CPU kullanımı. Bu değer, sistemdeki tüm işlemci çekirdeğini temsil eden %100 ile raporlanır. Örnek olarak, dört çekirdekli bir sistemin %50 ' ünü kullanan 2 yönlü bir VM tamamen iki çekirdek kullanmaktır.|Boyut yok|
|Yüztagecpuready|CPU için hazırlanma yüzdesi|Milisaniye|Toplam|Hazır süre, CPU (ler) in geçmiş güncelleştirme aralığında kullanılabilir olmasını bekleyen zamanın harcadığı süredir.|Boyut yok|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|Cpuyüzdesi|CPU Yüzdesi|Yüzde|Ortalama|CPU Yüzdesi|Örnek|
|MemoryPercentage|Bellek yüzdesi|Yüzde|Ortalama|Bellek yüzdesi|Örnek|
|DiskQueueLength|Disk kuyruğu uzunluğu|Sayı|Ortalama|Disk kuyruğu uzunluğu|Örnek|
|HttpQueueLength|Http kuyruğu uzunluğu|Sayı|Ortalama|Http kuyruğu uzunluğu|Örnek|
|BytesReceived|Içindeki veriler|Bayt|Toplam|Içindeki veriler|Örnek|
|BytesSent|Giden veriler|Bayt|Toplam|Giden veriler|Örnek|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (işlevler hariç)

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|CpuTime|CPU süresi|Saniye|Toplam|CPU süresi|Örnek|
|İstekler|İstekler|Sayı|Toplam|İstekler|Örnek|
|BytesReceived|Içindeki veriler|Bayt|Toplam|Içindeki veriler|Örnek|
|BytesSent|Giden veriler|Bayt|Toplam|Giden veriler|Örnek|
|Http101|Http 101|Sayı|Toplam|Http 101|Örnek|
|Http2xx|Http 2xx|Sayı|Toplam|Http 2xx|Örnek|
|Http3xx|Http 3xx|Sayı|Toplam|Http 3xx|Örnek|
|Http401|Http 401|Sayı|Toplam|Http 401|Örnek|
|Http403|Http 403|Sayı|Toplam|Http 403|Örnek|
|Http404|HTTP 404|Sayı|Toplam|HTTP 404|Örnek|
|Http406|Http 406|Sayı|Toplam|Http 406|Örnek|
|Http4xx|Http 4xx|Sayı|Toplam|Http 4xx|Örnek|
|Http5xx|Http sunucu hataları|Sayı|Toplam|Http sunucu hataları|Örnek|
|MemoryWorkingSet|Bellek çalışma kümesi|Bayt|Ortalama|Bellek çalışma kümesi|Örnek|
|AverageMemoryWorkingSet|Ortalama bellek çalışma kümesi|Bayt|Ortalama|Ortalama bellek çalışma kümesi|Örnek|
|AverageResponseTime|Ortalama yanıt süresi|Saniye|Ortalama|Ortalama yanıt süresi|Örnek|
|AppConnections|Bağlantılar|Sayı|Ortalama|Bağlantılar|Örnek|
|İşlendiğini|Tanıtıcı sayısı|Sayı|Ortalama|Tanıtıcı sayısı|Örnek|
|İş Parçacıkları|İş parçacığı sayısı|Sayı|Ortalama|İş parçacığı sayısı|Örnek|
|PrivateBytes|Özel baytlar|Bayt|Ortalama|Özel baytlar|Örnek|
|Ioreadbytespersecond|GÇ okuma bayt/saniye|BytesPerSecond|Toplam|GÇ okuma bayt/saniye|Örnek|
|Iowritebytespersecond|GÇ yazma bayt/saniye|BytesPerSecond|Toplam|GÇ yazma bayt/saniye|Örnek|
|Iootherbytespersecond|GÇ diğer bayt/saniye|BytesPerSecond|Toplam|GÇ diğer bayt/saniye|Örnek|
|IoReadOperationsPerSecond|GÇ okuma Işlemi/saniye|BytesPerSecond|Toplam|GÇ okuma Işlemi/saniye|Örnek|
|Iowriteoperationspersecond|GÇ Yazma Işlemi/saniye|BytesPerSecond|Toplam|GÇ Yazma Işlemi/saniye|Örnek|
|Iootheroperationspersecond|GÇ diğer Işlem/saniye|BytesPerSecond|Toplam|GÇ diğer Işlem/saniye|Örnek|
|RequestsInApplicationQueue|Uygulama kuyruğundaki istekler|Sayı|Ortalama|Uygulama kuyruğundaki istekler|Örnek|
|CurrentAssemblies|Geçerli derlemeler|Sayı|Ortalama|Geçerli derlemeler|Örnek|
|TotalAppDomains|Toplam uygulama etki alanları|Sayı|Ortalama|Toplam uygulama etki alanları|Örnek|
|TotalAppDomainsUnloaded|Toplam yüklenmeyen uygulama etki alanları|Sayı|Ortalama|Toplam yüklenmeyen uygulama etki alanları|Örnek|
|Gen0Collections|Gen 0 çöp koleksiyonları|Sayı|Toplam|Gen 0 çöp koleksiyonları|Örnek|
|Gen1Collections|Gen 1 çöp koleksiyonları|Sayı|Toplam|Gen 1 çöp koleksiyonları|Örnek|
|Gen2Collections|Gen 2 çöp koleksiyonları|Sayı|Toplam|Gen 2 çöp koleksiyonları|Örnek|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (işlevler)

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|BytesReceived|Içindeki veriler|Bayt|Toplam|Içindeki veriler|Örnek|
|BytesSent|Giden veriler|Bayt|Toplam|Giden veriler|Örnek|
|Http5xx|Http sunucu hataları|Sayı|Toplam|Http sunucu hataları|Örnek|
|MemoryWorkingSet|Bellek çalışma kümesi|Bayt|Ortalama|Bellek çalışma kümesi|Örnek|
|AverageMemoryWorkingSet|Ortalama bellek çalışma kümesi|Bayt|Ortalama|Ortalama bellek çalışma kümesi|Örnek|
|FunctionExecutionUnits|İşlev yürütme birimleri|MB/milisaniye|Toplam|[İşlev yürütme birimleri](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Örnek|
|Işlev yürütme sayısı|İşlev yürütme sayısı|Sayı|Toplam|İşlev yürütme sayısı|Örnek|
|PrivateBytes|Özel baytlar|Bayt|Ortalama|Özel baytlar|Örnek|
|Ioreadbytespersecond|GÇ okuma bayt/saniye|BytesPerSecond|Toplam|GÇ okuma bayt/saniye|Örnek|
|Iowritebytespersecond|GÇ yazma bayt/saniye|BytesPerSecond|Toplam|GÇ yazma bayt/saniye|Örnek|
|Iootherbytespersecond|GÇ diğer bayt/saniye|BytesPerSecond|Toplam|GÇ diğer bayt/saniye|Örnek|
|IoReadOperationsPerSecond|GÇ okuma Işlemi/saniye|BytesPerSecond|Toplam|GÇ okuma Işlemi/saniye|Örnek|
|Iowriteoperationspersecond|GÇ Yazma Işlemi/saniye|BytesPerSecond|Toplam|GÇ Yazma Işlemi/saniye|Örnek|
|Iootheroperationspersecond|GÇ diğer Işlem/saniye|BytesPerSecond|Toplam|GÇ diğer Işlem/saniye|Örnek|
|RequestsInApplicationQueue|Uygulama kuyruğundaki istekler|Sayı|Ortalama|Uygulama kuyruğundaki istekler|Örnek|
|CurrentAssemblies|Geçerli derlemeler|Sayı|Ortalama|Geçerli derlemeler|Örnek|
|TotalAppDomains|Toplam uygulama etki alanları|Sayı|Ortalama|Toplam uygulama etki alanları|Örnek|
|TotalAppDomainsUnloaded|Toplam yüklenmeyen uygulama etki alanları|Sayı|Ortalama|Toplam yüklenmeyen uygulama etki alanları|Örnek|
|Gen0Collections|Gen 0 çöp koleksiyonları|Sayı|Toplam|Gen 0 çöp koleksiyonları|Örnek|
|Gen1Collections|Gen 1 çöp koleksiyonları|Sayı|Toplam|Gen 1 çöp koleksiyonları|Örnek|
|Gen2Collections|Gen 2 çöp koleksiyonları|Sayı|Toplam|Gen 2 çöp koleksiyonları|Örnek|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|CpuTime|CPU süresi|Saniye|Toplam|CPU süresi|Örnek|
|İstekler|İstekler|Sayı|Toplam|İstekler|Örnek|
|BytesReceived|Içindeki veriler|Bayt|Toplam|Içindeki veriler|Örnek|
|BytesSent|Giden veriler|Bayt|Toplam|Giden veriler|Örnek|
|Http101|Http 101|Sayı|Toplam|Http 101|Örnek|
|Http2xx|Http 2xx|Sayı|Toplam|Http 2xx|Örnek|
|Http3xx|Http 3xx|Sayı|Toplam|Http 3xx|Örnek|
|Http401|Http 401|Sayı|Toplam|Http 401|Örnek|
|Http403|Http 403|Sayı|Toplam|Http 403|Örnek|
|Http404|HTTP 404|Sayı|Toplam|HTTP 404|Örnek|
|Http406|Http 406|Sayı|Toplam|Http 406|Örnek|
|Http4xx|Http 4xx|Sayı|Toplam|Http 4xx|Örnek|
|Http5xx|Http sunucu hataları|Sayı|Toplam|Http sunucu hataları|Örnek|
|MemoryWorkingSet|Bellek çalışma kümesi|Bayt|Ortalama|Bellek çalışma kümesi|Örnek|
|AverageMemoryWorkingSet|Ortalama bellek çalışma kümesi|Bayt|Ortalama|Ortalama bellek çalışma kümesi|Örnek|
|AverageResponseTime|Ortalama yanıt süresi|Saniye|Ortalama|Ortalama yanıt süresi|Örnek|
|FunctionExecutionUnits|İşlev yürütme birimleri|Sayı|Toplam|İşlev yürütme birimleri|Örnek|
|Işlev yürütme sayısı|İşlev yürütme sayısı|Sayı|Toplam|İşlev yürütme sayısı|Örnek|
|AppConnections|Bağlantılar|Sayı|Ortalama|Bağlantılar|Örnek|
|İşlendiğini|Tanıtıcı sayısı|Sayı|Ortalama|Tanıtıcı sayısı|Örnek|
|İş Parçacıkları|İş parçacığı sayısı|Sayı|Ortalama|İş parçacığı sayısı|Örnek|
|PrivateBytes|Özel baytlar|Bayt|Ortalama|Özel baytlar|Örnek|
|Ioreadbytespersecond|GÇ okuma bayt/saniye|BytesPerSecond|Toplam|GÇ okuma bayt/saniye|Örnek|
|Iowritebytespersecond|GÇ yazma bayt/saniye|BytesPerSecond|Toplam|GÇ yazma bayt/saniye|Örnek|
|Iootherbytespersecond|GÇ diğer bayt/saniye|BytesPerSecond|Toplam|GÇ diğer bayt/saniye|Örnek|
|IoReadOperationsPerSecond|GÇ okuma Işlemi/saniye|BytesPerSecond|Toplam|GÇ okuma Işlemi/saniye|Örnek|
|Iowriteoperationspersecond|GÇ Yazma Işlemi/saniye|BytesPerSecond|Toplam|GÇ Yazma Işlemi/saniye|Örnek|
|Iootheroperationspersecond|GÇ diğer Işlem/saniye|BytesPerSecond|Toplam|GÇ diğer Işlem/saniye|Örnek|
|RequestsInApplicationQueue|Uygulama kuyruğundaki istekler|Sayı|Ortalama|Uygulama kuyruğundaki istekler|Örnek|
|CurrentAssemblies|Geçerli derlemeler|Sayı|Ortalama|Geçerli derlemeler|Örnek|
|TotalAppDomains|Toplam uygulama etki alanları|Sayı|Ortalama|Toplam uygulama etki alanları|Örnek|
|TotalAppDomainsUnloaded|Toplam yüklenmeyen uygulama etki alanları|Sayı|Ortalama|Toplam yüklenmeyen uygulama etki alanları|Örnek|
|Gen0Collections|Gen 0 çöp koleksiyonları|Sayı|Toplam|Gen 0 çöp koleksiyonları|Örnek|
|Gen1Collections|Gen 1 çöp koleksiyonları|Sayı|Toplam|Gen 1 çöp koleksiyonları|Örnek|
|Gen2Collections|Gen 2 çöp koleksiyonları|Sayı|Toplam|Gen 2 çöp koleksiyonları|Örnek|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|İstekler|İstekler|Sayı|Toplam|İstekler|Örnek|
|BytesReceived|Içindeki veriler|Bayt|Toplam|Içindeki veriler|Örnek|
|BytesSent|Giden veriler|Bayt|Toplam|Giden veriler|Örnek|
|Http101|Http 101|Sayı|Toplam|Http 101|Örnek|
|Http2xx|Http 2xx|Sayı|Toplam|Http 2xx|Örnek|
|Http3xx|Http 3xx|Sayı|Toplam|Http 3xx|Örnek|
|Http401|Http 401|Sayı|Toplam|Http 401|Örnek|
|Http403|Http 403|Sayı|Toplam|Http 403|Örnek|
|Http404|HTTP 404|Sayı|Toplam|HTTP 404|Örnek|
|Http406|Http 406|Sayı|Toplam|Http 406|Örnek|
|Http4xx|Http 4xx|Sayı|Toplam|Http 4xx|Örnek|
|Http5xx|Http sunucu hataları|Sayı|Toplam|Http sunucu hataları|Örnek|
|AverageResponseTime|Ortalama yanıt süresi|Saniye|Ortalama|Ortalama yanıt süresi|Örnek|
|Cpuyüzdesi|CPU Yüzdesi|Yüzde|Ortalama|CPU Yüzdesi|Örnek|
|MemoryPercentage|Bellek yüzdesi|Yüzde|Ortalama|Bellek yüzdesi|Örnek|
|DiskQueueLength|Disk kuyruğu uzunluğu|Sayı|Ortalama|Disk kuyruğu uzunluğu|Örnek|
|HttpQueueLength|Http kuyruğu uzunluğu|Sayı|Ortalama|Http kuyruğu uzunluğu|Örnek|
|ActiveRequests|Etkin Istekler|Sayı|Toplam|Etkin Istekler|Örnek|
|Toplam ön uçlar|Toplam ön uçlar|Sayı|Ortalama|Toplam ön uçlar|Boyut yok|
|Smallappserviceplanınstances|Küçük App Service çalışanları planlıyor|Sayı|Ortalama|Küçük App Service çalışanları planlıyor|Boyut yok|
|Mediumappserviceplanınstances|Orta App Service çalışanları planlıyor|Sayı|Ortalama|Orta App Service çalışanları planlıyor|Boyut yok|
|Largeappserviceplanınstances|Büyük App Service çalışanları planlıyor|Sayı|Ortalama|Büyük App Service çalışanları planlıyor|Boyut yok|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Ölçüm|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|Boyutlar|
|---|---|---|---|---|---|
|WorkersTotal|Toplam çalışan sayısı|Sayı|Ortalama|Toplam çalışan sayısı|Boyut yok|
|WorkersAvailable|Kullanılabilir çalışanlar|Sayı|Ortalama|Kullanılabilir çalışanlar|Boyut yok|
|WorkersUsed|Kullanılan çalışanlar|Sayı|Ortalama|Kullanılan çalışanlar|Boyut yok|
|Cpuyüzdesi|CPU Yüzdesi|Yüzde|Ortalama|CPU Yüzdesi|Örnek|
|MemoryPercentage|Bellek yüzdesi|Yüzde|Ortalama|Bellek yüzdesi|Örnek|

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Izleyici 'de ölçümler hakkında bilgi edinin](data-platform.md)
* [Ölçümler üzerinde uyarı oluşturma](alerts-overview.md)
* [Ölçümleri depolama, Olay Hub 'ına veya Log Analytics Dışa aktarma](resource-logs-overview.md)
