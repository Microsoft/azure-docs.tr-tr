---
title: Azure Izleyici kaynak türüne göre desteklenen ölçümler
description: Azure Izleyici ile her kaynak türü için kullanılabilen ölçümlerin listesi.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/15/2021
ms.author: robb
ms.openlocfilehash: 1091d103428315a065dd1ff9800ce2ad16632df0
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600021"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Izleyici ile desteklenen ölçümler

> [!NOTE]
> Bu liste büyük ölçüde otomatik olarak oluşturulmuştur. Bu listede GitHub aracılığıyla yapılan herhangi bir değişiklik, uyarı olmadan üzerine yazılabilir. Kalıcı güncelleştirmeler yapma hakkında daha fazla bilgi için bu makalenin yazarına başvurun.

Azure Izleyici, ölçümlerle etkileşimde bulunmak için, portalda grafik oluşturma, REST API aracılığıyla erişme veya PowerShell veya CLı kullanarak sorgulama gibi çeşitli yollar sağlar. 

Bu makale, Azure Izleyicisinin birleştirilmiş ölçüm işlem hattı ile Şu anda kullanılabilir olan tüm platform (yani otomatik olarak toplanan) ölçümlerinin tümüyle bir listesidir. Bu makalenin en üstündeki tarihten sonra değiştirilen veya eklenen ölçümler henüz aşağıda görünmeyebilir. Program aracılığıyla ölçüm listesini sorgulamak ve erişmek için lütfen [2018-01-01 api sürümünü](/rest/api/monitor/metricdefinitions)kullanın. Bu listede bulunmayan diğer ölçümler portalda veya eski API 'Ler kullanılarak kullanılabilir olabilir.

Ölçümler, kaynak sağlayıcılarına ve kaynak türüne göre düzenlenir. Hizmetlerin ve kaynak sağlayıcılarının ve bunlara ait türlerin listesi için bkz. [Azure hizmetleri Için kaynak sağlayıcıları](../../azure-resource-manager/management/azure-services-resource-providers.md).  

## <a name="exporting-platform-metrics-to-other-locations"></a>Platform ölçümlerini diğer konumlara dışarı aktarma

Azure izleyici ardışık düzeninde bulunan Platform ölçümlerini iki şekilde başka konumlara dışarı aktarabilirsiniz.
1. Ölçümleri kullanın [REST API](/rest/api/monitor/metrics/list)
2. Platform ölçümlerini yönlendirmek için [tanılama ayarlarını](../essentials/diagnostic-settings.md) kullanın 
    - Azure Depolama
    - Azure Izleyici günlükleri (ve bu nedenle Log Analytics)
    - Onları Microsoft dışı sistemlere alma yöntemi olan olay hub 'ları 

Tanılama ayarlarını kullanmak ölçümleri yönlendirmenin en kolay yoludur, ancak bazı sınırlamalar vardır: 

- **Bazı dışarı aktarılabilir** olmayan-tüm ölçümler REST API kullanılarak dışarı aktarılabilir, ancak bazıları Azure izleyici arka ucunun içindeki yanlışlıklar nedeniyle Tanılama ayarları kullanılarak dışarı aktarılamaz. Aşağıdaki tablolarda bulunan *Tanılama ayarları aracılığıyla dışarı aktarılabilir* sütun, hangi ölçümlerin bu şekilde dışarı aktarılabileceği listelenmiştir.  

- **Çok boyutlu ölçümler** -çok boyutlu ölçümleri Tanılama ayarları aracılığıyla diğer konumlara gönderme Şu anda desteklenmemektedir. Boyutlu ölçümler, boyut değerlerinin toplamı alınarak düzleştirilmiş tek yönlü ölçümler olarak dışarı aktarılır. *Örneğin*: Bir Olay Hub'ındaki 'Gelen İletiler' ölçümü, kuyruk düzeyi temelinde araştırılıp grafiği oluşturulabilir. Ancak, tanılama ayarları aracılığıyla dışarı aktarılan ölçüm, Olay Hub’ındaki tüm kuyruklarda tüm gelen iletiler halinde ifade edilir.

## <a name="guest-os-and-host-os-metrics"></a>Konuk işletim sistemi ve konak işletim sistemi ölçümleri

> [!WARNING]
> Azure sanal makineler, Service Fabric ve Cloud Services üzerinde çalışan konuk işletim sistemi (konuk OS) ölçümleri **burada listelenmez.** Konuk işletim sistemi ölçümleri, Konuk işletim sisteminin bir parçası olarak veya üzerinde çalışan bir veya daha fazla aracı aracılığıyla toplanmalıdır.  Konuk işletim sistemi ölçümleri, her ikisi de otomatik ölçekleme veya uyarı için sık kullanılan Konuk CPU yüzdesini veya bellek kullanımını izleyen performans sayaçlarını içerir. 
>
> **Konak işletim sistemi ölçümleri kullanılabilir ve aşağıda listelenmiştir.** Bunlar aynı değildir. Konak işletim sistemi ölçümleri, Konuk işletim sistemi oturumunuzu barındıran Hyper-V oturumu ile ilgilidir. 

> [!TIP]
> En iyi yöntem, Konuk işletim sistemi performans ölçümlerini platform ölçümlerinin depolandığı Azure Izleyici ölçüm veritabanına göndermek için [Azure tanılama uzantısını](../agents/diagnostics-extension-overview.md) kullanmak ve yapılandırmaktır. Uzantı, Konuk işletim sistemi ölçümlerini [özel ölçüm](../essentials/metrics-custom-overview.md) API 'si aracılığıyla yönlendirir. Daha sonra, bir grafik oluşturabilir, uyarır ve başka bir deyişle platform ölçümleri gibi Konuk işletim sistemi ölçümlerini kullanabilirsiniz. Alternatif olarak veya Ayrıca, Azure Izleyici günlüklerine/Log Analytics Konuk işletim sistemi ölçümleri göndermek için Log Analytics aracısını kullanabilirsiniz. Ölçüm olmayan verilerle birlikte bu ölçümler üzerinde sorgulama yapabilirsiniz. 

Önemli ek bilgiler için bkz. [Izleme aracılarına genel bakış](../agents/agents-overview.md).

## <a name="table-formatting"></a>Tablo biçimlendirme

> [!IMPORTANT] 
> Bu en son güncelleştirme yeni bir sütun ekler ve ölçümleri alfabetik olarak yeniden sıralanabilir. Ek bilgiler, tarayıcı pencerenizin genişliğine bağlı olarak, aşağıdaki tabloda alt kısımdaki bir yatay kaydırma çubuğuna sahip olabileceği anlamına gelir. Bilgilerin eksik olduğunu düşünüyorsanız, tablonun tamamını görmek için kaydırma çubuğunu kullanın.
## <a name="microsoftaadiamazureadmetrics"></a>Microsoft. aadihar/Azureadölçümlerini

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|OperationName|No|OperationName|Count|Ortalama|Azureadölçüm türü ölçümü|Boyut yok|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/sunucuları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Yes|Bellek: temizleyici geçerli fiyatı|Count|Ortalama|Geçerli bellek fiyatı, $/Byte/Time, 1000 olarak normalleştirilemez.|Sunucuresourcetype|
|Cleanermemorynonınkable|Yes|Bellek: temizleyici bellek daraltılamaz|Bayt|Ortalama|Arka plan temizleyici tarafından temizlenme konusu değil, bayt cinsinden bellek miktarı.|Sunucuresourcetype|
|Cleanermemoryshrınkable|Yes|Bellek: temizleyici bellek shrınılabilir|Bayt|Ortalama|Arka plan temizleyici tarafından temizlenme konusu bayt cinsinden bellek miktarı.|Sunucuresourcetype|
|Commandpoolbusyıthreads|Yes|İş parçacıkları: komut havuzu meşgul iş parçacıkları|Count|Ortalama|Komut iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|Sunucuresourcetype|
|Commandpokaydlithreads|Yes|İş parçacıkları: komut havuzu boşta iş parçacıkları|Count|Ortalama|Komut iş parçacığı havuzundaki boşta iş parçacığı sayısı.|Sunucuresourcetype|
|CommandPoolJobQueueLength|Yes|Komut havuzu Iş kuyruğu uzunluğu|Count|Ortalama|Komut iş parçacığı havuzu sırasındaki iş sayısı.|Sunucuresourcetype|
|CurrentConnections|Yes|Bağlantı: geçerli bağlantılar|Count|Ortalama|Kurulan istemci bağlantılarının geçerli sayısı.|Sunucuresourcetype|
|CurrentUserSessions|Yes|Geçerli Kullanıcı oturumları|Count|Ortalama|Geçerli kullanıcı oturumlarının sayısı.|Sunucuresourcetype|
|Longparsingbusyıthreads|Yes|İş parçacıkları: uzun ayrıştırma meşgul iş parçacıkları|Count|Ortalama|Uzun ayrıştırma iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|Sunucuresourcetype|
|LongParsingIdleThreads|Yes|İş parçacıkları: uzun ayrıştırma boşta iş parçacıkları|Count|Ortalama|Uzun ayrıştırma iş parçacığı havuzundaki boşta iş parçacığı sayısı.|Sunucuresourcetype|
|LongParsingJobQueueLength|Yes|İş parçacıkları: uzun ayrıştırma işi sıra uzunluğu|Count|Ortalama|Uzun ayrıştırma iş parçacığı havuzu kuyruğundaki iş sayısı.|Sunucuresourcetype|
|mashup_engine_memory_metric|Yes|D motoru belleği|Bayt|Ortalama|Karma altyapı işlemlerine göre bellek kullanımı|Sunucuresourcetype|
|mashup_engine_private_bytes_metric|Yes|D motoru özel baytlar|Bayt|Ortalama|Karma altyapı işlemlerine göre özel bayt kullanımı.|Sunucuresourcetype|
|mashup_engine_qpu_metric|Yes|A motoru QPU|Count|Ortalama|Karma altyapı işlemlerine göre QPU kullanımı|Sunucuresourcetype|
|mashup_engine_virtual_bytes_metric|Yes|D motoru sanal bayt sayısı|Bayt|Ortalama|Karma altyapı işlemlerine göre sanal bayt kullanımı.|Sunucuresourcetype|
|memory_metric|Yes|Bellek|Bayt|Ortalama|Bellek. S1 için 0-25 GB, S2 için 0-50 GB ve S4 için 0-100 GB|Sunucuresourcetype|
|memory_thrashing_metric|Yes|Bellek Temizleme|Yüzde|Ortalama|Ortalama bellek miktarı.|Sunucuresourcetype|
|MemoryLimitHard|Yes|Bellek: bellek sınırı sabit|Bayt|Ortalama|Yapılandırma dosyasından sabit bellek sınırı.|Sunucuresourcetype|
|Memoryhighlimit|Yes|Bellek: bellek sınırı yüksek|Bayt|Ortalama|Yapılandırma dosyasından yüksek bellek sınırı.|Sunucuresourcetype|
|MemoryLimitLow|Yes|Bellek: bellek sınırı düşük|Bayt|Ortalama|Yapılandırma dosyasından düşük bellek sınırı.|Sunucuresourcetype|
|MemoryLimitVertiPaq|Yes|Bellek: bellek sınırı VertiPaq|Bayt|Ortalama|Yapılandırma dosyasından bellek içi sınır.|Sunucuresourcetype|
|MemoryUsage|Yes|Bellek: bellek kullanımı|Bayt|Ortalama|Sunucu işleminin temizleyici bellek fiyatını hesaplamada kullanılan bellek kullanımı. XVelocity altyapısının bellek sınırının fazla olması halinde, xVelocity bellek içi analiz altyapısı (VertiPaq) tarafından eşlenen veya ayrılan belleği yoksayarak, işlem \ bayt ve bellek eşlemeli verilerin boyutuna eşit.|Sunucuresourcetype|
|private_bytes_metric|Yes|Özel baytlar|Bayt|Ortalama|Özel baytlar.|Sunucuresourcetype|
|Processingpoolbusyıiojobthreads|Yes|İş parçacıkları: Işleme havuzu meşgul g/ç işi iş parçacıkları|Count|Ortalama|İşleme iş parçacığı havuzunda g/ç işleri çalıştıran iş parçacığı sayısı.|Sunucuresourcetype|
|Processingpoolbusınonıothreads|Yes|İş parçacıkları: Işleme havuzu meşgul olmayan g/ç iş parçacıkları|Count|Ortalama|İşleme iş parçacığı havuzunda g/ç olmayan işler çalıştıran iş parçacığı sayısı.|Sunucuresourcetype|
|Processingpokaydliiojobthreads|Yes|İş parçacıkları: Işleme havuzu boşta g/ç işi iş parçacıkları|Count|Ortalama|İşleme iş parçacığı havuzundaki g/ç işleri için boşta iş parçacığı sayısı.|Sunucuresourcetype|
|Processingpokaydlenoniothreads|Yes|İş parçacıkları: Işleme havuzu boşta g/ç olmayan iş parçacıkları|Count|Ortalama|İşleme iş parçacığı havuzunda g/ç dışı işlere adanmış boşta iş parçacığı sayısı.|Sunucuresourcetype|
|Processingpokayojobqueuelength|Yes|İş parçacıkları: Işleme havuzu g/ç iş kuyruğu uzunluğu|Count|Ortalama|İşleme iş parçacığı havuzu kuyruğundaki g/ç işlerinin sayısı.|Sunucuresourcetype|
|ProcessingPoolJobQueueLength|Yes|İşleme havuzu Iş kuyruğu uzunluğu|Count|Ortalama|İşleme iş parçacığı havuzu kuyruğundaki g/ç dışı iş sayısı.|Sunucuresourcetype|
|qpu_metric|Yes|QPU|Count|Ortalama|QPU. S1 için 0-100 aralığı, S2 için 0-200 ve S4 için 0-400|Sunucuresourcetype|
|Querypoolbusyıthreads|Yes|Sorgu havuzu meşgul Iş parçacıkları|Count|Ortalama|Sorgu iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|Sunucuresourcetype|
|Querypokaydlithreads|Yes|İş parçacıkları: sorgu havuzu boşta iş parçacıkları|Count|Ortalama|İşleme iş parçacığı havuzundaki g/ç işleri için boşta iş parçacığı sayısı.|Sunucuresourcetype|
|QueryPoolJobQueueLength|Yes|İş parçacıkları: sorgu havuzu iş kuyruğu uzunluğu|Count|Ortalama|Sorgu iş parçacığı havuzu kuyruğundaki iş sayısı.|Sunucuresourcetype|
|Kota|Yes|Bellek: kota|Bayt|Ortalama|Bayt cinsinden geçerli bellek kotası. Bellek kotası, bellek verme veya bellek ayırma olarak da bilinir.|Sunucuresourcetype|
|Quotabkilitli|Yes|Bellek: kota engellendi|Count|Ortalama|Diğer bellek kotaları boşaltılana kadar engellenen kota isteklerinin geçerli sayısı.|Sunucuresourcetype|
|RowsConvertedPerSec|Yes|İşleme: dönüştürülen satır sayısı/saniye|Sayaçpersaniye|Ortalama|İşlem sırasında dönüştürülen satır oranı.|Sunucuresourcetype|
|RowsReadPerSec|Yes|İşleme: okunan satır sayısı/saniye|Sayaçpersaniye|Ortalama|Tüm ilişkisel veritabanlarından okunan satır oranı.|Sunucuresourcetype|
|RowsWrittenPerSec|Yes|İşleme: yazılan satır sayısı/saniye|Sayaçpersaniye|Ortalama|İşlem sırasında yazılan satır oranı.|Sunucuresourcetype|
|Shortparsingbusyıthreads|Yes|İş parçacıkları: kısa ayrıştırma meşgul iş parçacıkları|Count|Ortalama|Kısa ayrıştırma iş parçacığı havuzundaki meşgul iş parçacığı sayısı.|Sunucuresourcetype|
|ShortParsingIdleThreads|Yes|İş parçacıkları: kısa ayrıştırma boşta iş parçacıkları|Count|Ortalama|Kısa ayrıştırma iş parçacığı havuzundaki boşta iş parçacığı sayısı.|Sunucuresourcetype|
|ShortParsingJobQueueLength|Yes|İş parçacıkları: kısa ayrıştırma iş kuyruğu uzunluğu|Count|Ortalama|Kısa ayrıştırma iş parçacığı havuzu sırasındaki iş sayısı.|Sunucuresourcetype|
|Başarılı bir bağlantı Spersec|Yes|Saniyedeki başarılı bağlantı sayısı|Sayaçpersaniye|Ortalama|Başarılı bağlantı tamamlama oranı.|Sunucuresourcetype|
|Totalconnectionarızaları|Yes|Toplam bağlantı başarısızlığı sayısı|Count|Ortalama|Toplam başarısız bağlantı denemesi.|Sunucuresourcetype|
|TotalConnectionRequests|Yes|Toplam bağlantı Isteği sayısı|Count|Ortalama|Toplam bağlantı isteği sayısı. Bunlar, varışlardır.|Sunucuresourcetype|
|Vertipaqdisk belleksiz|Yes|Bellek: VertiPaq disk belleksiz|Bayt|Ortalama|Bellek içi altyapı tarafından kullanılmak üzere çalışma kümesinde kilitlenen bellek baytları.|Sunucuresourcetype|
|Vertipaqdisk|Yes|Bellek: VertiPaq disk belleğine|Bayt|Ortalama|Bellek içi veriler için kullanılan disk belleğine alınan bellek miktarı.|Sunucuresourcetype|
|virtual_bytes_metric|Yes|Sanal Bayt Sayısı|Bayt|Ortalama|Sanal bayt sayısı.|Sunucuresourcetype|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|BackendDuration|Yes|Arka uç Isteklerinin süresi|Mayacak|Ortalama|Milisaniye cinsinden arka uç Isteklerinin süresi|Konum, ana bilgisayar adı|
|Kapasite|Yes|Kapasite|Yüzde|Ortalama|Apimanayönetimi hizmeti için kullanım ölçümü|Konum|
|Süre|Yes|Ağ Geçidi Isteklerinin genel süresi|Mayacak|Ortalama|Milisaniye olarak ağ geçidi Isteklerinin genel süresi|Konum, ana bilgisayar adı|
|EventHubDroppedEvents|Yes|Bırakılan EventHub olayları|Count|Toplam|Sıra boyutu sınırına ulaşıldığından atlanan olay sayısı|Konum|
|EventHubRejectedEvents|Yes|Reddedilen EventHub olayları|Count|Toplam|Reddedilen EventHub olaylarının sayısı (yanlış yapılandırma veya yetkisiz)|Konum|
|Eventhubbaşarılı Futavents|Yes|Başarılı EventHub olayları|Count|Toplam|Başarılı EventHub olaylarının sayısı|Konum|
|EventHubThrottledEvents|Yes|Kısıtlanmış EventHub olayları|Count|Toplam|Kısıtlanmış EventHub olay sayısı|Konum|
|EventHubTimedoutEvents|Yes|Zaman aşımına uğrayan EventHub olayları|Count|Toplam|Zaman aşımına uğrayan EventHub olaylarının sayısı|Konum|
|EventHubTotalBytesSent|Yes|EventHub olaylarının boyutu|Bayt|Toplam|Toplam EventHub olay boyutu (bayt)|Konum|
|EventHubTotalEvents|Yes|Toplam EventHub olayları|Count|Toplam|EventHub 'e gönderilen olay sayısı|Konum|
|EventHubTotalFailedEvents|Yes|Başarısız EventHub olayları|Count|Toplam|Başarısız EventHub olaylarının sayısı|Konum|
|FailedRequests|Yes|Başarısız ağ geçidi Istekleri (kullanım dışı)|Count|Toplam|Ağ Geçidi isteklerindeki başarısızlık sayısı-çok boyutlu istek ölçüsünü GatewayResponseCodeCategory boyutuyla kullanın|Konum, ana bilgisayar adı|
|NetworkConnectivity|Yes|Kaynakların ağ bağlantısı durumu (Önizleme)|Count|Ortalama|API Management hizmetinden bağımlı kaynak türlerinin ağ bağlantısı durumu|Konum, ResourceType|
|Diğer Istekler|Yes|Diğer ağ geçidi Istekleri (kullanım dışı)|Count|Toplam|Diğer ağ geçidi isteklerinin sayısı-çok boyutlu istek ölçüsünü GatewayResponseCodeCategory boyutuyla kullanın|Konum, ana bilgisayar adı|
|İstekler|Yes|İstekler|Count|Toplam|Birden çok boyut içeren ağ geçidi isteği ölçümleri|Konum, ana bilgisayar adı, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Yes|Başarılı ağ geçidi Istekleri (kullanım dışı)|Count|Toplam|Başarılı ağ geçidi isteklerinin sayısı-çok boyutlu istek ölçüsünü GatewayResponseCodeCategory boyutuyla kullanın|Konum, ana bilgisayar adı|
|TotalRequests|Yes|Toplam ağ geçidi Isteği sayısı (kullanım dışı)|Count|Toplam|Ağ Geçidi isteklerinin sayısı-çok boyutlu istek ölçüsünü GatewayResponseCodeCategory boyutuyla kullanın|Konum, ana bilgisayar adı|
|UnauthorizedRequests|Yes|Yetkisiz ağ geçidi Istekleri (kullanım dışı)|Count|Toplam|Yetkisiz ağ geçidi isteklerinin sayısı-çok boyutlu istek ölçüsünü GatewayResponseCodeCategory boyutuyla kullanın|Konum, ana bilgisayar adı|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/Configurationmağazaları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Httpıncomingrequestcount|Yes|Httpıncomingrequestcount|Count|Count|Gelen http isteklerinin toplam sayısı.|StatusCode, kimlik doğrulaması|
|Httpıncomingrequestduration|Yes|Httpıncomingrequestduration|Count|Ortalama|Http isteğinde gecikme süresi.|StatusCode, kimlik doğrulaması|
|Çalışan Httprequestcount|Yes|Çalışan Httprequestcount|Count|Count|Kısıtlanmış http istekleri.|Boyut yok|

## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/yay

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|etkin-zamanlayıcı-sayısı|Yes|etkin-zamanlayıcı-sayısı|Count|Ortalama|Şu anda etkin olan zamanlayıcılar sayısı|Dağıtım, AppName, Pod|
|ayırma oranı|Yes|ayırma oranı|Bayt|Ortalama|Yönetilen yığında ayrılan bayt sayısı|Dağıtım, AppName, Pod|
|AppCpuUsage|Yes|Uygulama CPU Kullanımı |Yüzde|Ortalama|Uygulama için son CPU kullanımı|Dağıtım, AppName, Pod|
|derleme sayısı|Yes|derleme sayısı|Count|Ortalama|Yüklenen derlemelerin sayısı|Dağıtım, AppName, Pod|
|CPU kullanımı|Yes|CPU kullanımı|Yüzde|Ortalama|işlemin CPU 'YU kullanışında%|Dağıtım, AppName, Pod|
|geçerli istekler|Yes|geçerli istekler|Count|Ortalama|İşlemin ömrü boyunca işlenen isteklerin toplam sayısı|Dağıtım, AppName, Pod|
|özel durum-sayı|Yes|özel durum-sayı|Count|Toplam|Özel durum sayısı|Dağıtım, AppName, Pod|
|başarısız-istekler|Yes|başarısız-istekler|Count|Ortalama|İşlemin ömrü boyunca başarısız isteklerin toplam sayısı|Dağıtım, AppName, Pod|
|GC-yığın boyutu|Yes|GC-yığın boyutu|Count|Ortalama|GC tarafından bildirilen toplam yığın boyutu (MB)|Dağıtım, AppName, Pod|
|Gen-0-GC-Count|Yes|Gen-0-GC-Count|Count|Ortalama|Gen 0 GCs sayısı|Dağıtım, AppName, Pod|
|Gen-0-boyut|Yes|Gen-0-boyut|Bayt|Ortalama|Gen 0 yığın boyutu|Dağıtım, AppName, Pod|
|Gen-1-GC-sayısı|Yes|Gen-1-GC-sayısı|Count|Ortalama|Gen 1 GB sayısı|Dağıtım, AppName, Pod|
|Gen-1-boyut|Yes|Gen-1-boyut|Bayt|Ortalama|Gen 1 yığın boyutu|Dağıtım, AppName, Pod|
|Gen-2-GC-sayım|Yes|Gen-2-GC-sayım|Count|Ortalama|Gen 2 GB sayısı|Dağıtım, AppName, Pod|
|Gen-2 boyutu|Yes|Gen-2 boyutu|Bayt|Ortalama|Gen 2 yığın boyutu|Dağıtım, AppName, Pod|
|JVM. GC. Live. Data. size|Yes|JVM. GC. Live. Data. size|Bayt|Ortalama|Tam GC sonrasında eski nesil bellek havuzunun boyutu|Dağıtım, AppName, Pod|
|JVM. GC. Max. Data. size|Yes|JVM. GC. Max. Data. size|Bayt|Ortalama|Eski nesil bellek havuzunun en büyük boyutu|Dağıtım, AppName, Pod|
|JVM. GC. Memory. ayrılmış|Yes|JVM. GC. Memory. ayrılmış|Bayt|Maksimum|Bir GC 'nin bir sonraki öncesine ait olması durumunda küçük kuşak bellek havuzunun boyutunun artması için artırılır|Dağıtım, AppName, Pod|
|JVM. GC. Memory. yükseltilen|Yes|JVM. GC. Memory. yükseltilen|Bayt|Maksimum|GC 'den sonra GC 'den önce eski nesil bellek havuzunun boyutundaki pozitif artış sayısı|Dağıtım, AppName, Pod|
|JVM. GC. Pause. Total. Count|Yes|JVM. GC. Pause. Total. Count|Count|Toplam|GC duraklatma sayısı|Dağıtım, AppName, Pod|
|JVM. GC. Pause. Total. Time|Yes|JVM. GC. Pause. Total. Time|Mayacak|Toplam|GC duraklatma toplam süre|Dağıtım, AppName, Pod|
|JVM. Memory. taahhüt|Yes|JVM. Memory. taahhüt|Bayt|Ortalama|JVM 'ye bayt olarak atanan bellek|Dağıtım, AppName, Pod|
|JVM. Memory. Max|Yes|JVM. Memory. Max|Bayt|Maksimum|Bellek yönetimi için kullanılabilecek bayt cinsinden maksimum bellek miktarı|Dağıtım, AppName, Pod|
|JVM. Memory. kullanıldı|Yes|JVM. Memory. kullanıldı|Bayt|Ortalama|Bayt cinsinden kullanılan uygulama belleği|Dağıtım, AppName, Pod|
|Loh-boyut|Yes|Loh-boyut|Bayt|Ortalama|LOH yığın boyutu|Dağıtım, AppName, Pod|
|Monitor-Lock-çekişme-Count|Yes|Monitor-Lock-çekişme-Count|Count|Ortalama|İzleyici kilidi alınmaya çalışılırken çekişme sayısı|Dağıtım, AppName, Pod|
|Process. CPU. Usage|Yes|Process. CPU. Usage|Yüzde|Ortalama|JVM işlemi için son CPU kullanımı|Dağıtım, AppName, Pod|
|saniye başına istek sayısı|Yes|istek hızı|Count|Ortalama|İstek hızı|Dağıtım, AppName, Pod|
|System. CPU. Usage|Yes|System. CPU. Usage|Yüzde|Ortalama|Tüm sistem için en son CPU kullanımı|Dağıtım, AppName, Pod|
|ThreadPool-tamamlanan-öğe sayısı|Yes|ThreadPool-tamamlanan-öğe sayısı|Count|Ortalama|ThreadPool tamamlanan Iş öğesi sayısı|Dağıtım, AppName, Pod|
|ThreadPool-kuyruk uzunluğu|Yes|ThreadPool-kuyruk uzunluğu|Count|Ortalama|Iş parçacığı Iş öğeleri sıra uzunluğu|Dağıtım, AppName, Pod|
|ThreadPool-thread-Count|Yes|ThreadPool-thread-Count|Count|Ortalama|ThreadPool Iş parçacığı sayısı|Dağıtım, AppName, Pod|
|GC zamanı|Yes|GC zamanı|Yüzde|Ortalama|Son GC 'den beri GC 'de geçen süre|Dağıtım, AppName, Pod|
|Tomcat. Global. Error|Yes|Tomcat. Global. Error|Count|Toplam|Tomcat genel hatası|Dağıtım, AppName, Pod|
|Tomcat. Global. alındı|Yes|Tomcat. Global. alındı|Bayt|Toplam|Tomcat alınan toplam bayt sayısı|Dağıtım, AppName, Pod|
|Tomcat. Global. Request. ort. saat|Yes|Tomcat. Global. Request. ort. saat|Mayacak|Ortalama|Tomcat Isteği ortalama zamanı|Dağıtım, AppName, Pod|
|Tomcat. Global. Request. Max|Yes|Tomcat. Global. Request. Max|Mayacak|Maksimum|Tomcat Isteği maksimum zamanı|Dağıtım, AppName, Pod|
|Tomcat. Global. Request. Total. Count|Yes|Tomcat. Global. Request. Total. Count|Count|Toplam|Tomcat Isteği toplam sayısı|Dağıtım, AppName, Pod|
|Tomcat. Global. Request. Total. Time|Yes|Tomcat. Global. Request. Total. Time|Mayacak|Toplam|Tomcat Isteği toplam süre|Dağıtım, AppName, Pod|
|Tomcat. Global. gönderildi|Yes|Tomcat. Global. gönderildi|Bayt|Toplam|Tomcat gönderilen toplam bayt sayısı|Dağıtım, AppName, Pod|
|Tomcat. Sessions. ACTIVE. Current|Yes|Tomcat. Sessions. ACTIVE. Current|Count|Toplam|Tomcat oturumu etkin sayısı|Dağıtım, AppName, Pod|
|Tomcat. Sessions. Active. Max|Yes|Tomcat. Sessions. Active. Max|Count|Toplam|Tomcat oturumu en fazla etkin sayısı|Dağıtım, AppName, Pod|
|Tomcat. Sessions. canlı. Max|Yes|Tomcat. Sessions. canlı. Max|Mayacak|Maksimum|Tomcat oturumu maksimum etkin süresi|Dağıtım, AppName, Pod|
|Tomcat. Sessions. oluşturuldu|Yes|Tomcat. Sessions. oluşturuldu|Count|Toplam|Tomcat oturum oluşturma sayısı|Dağıtım, AppName, Pod|
|Tomcat. Sessions. süre geçildi|Yes|Tomcat. Sessions. süre geçildi|Count|Toplam|Tomcat oturumunun süre dolma sayısı|Dağıtım, AppName, Pod|
|Tomcat. Sessions. reddedildi|Yes|Tomcat. Sessions. reddedildi|Count|Toplam|Tomcat oturumu reddedildi sayısı|Dağıtım, AppName, Pod|
|tomcat.threads.config. Max|Yes|tomcat.threads.config. Max|Count|Toplam|Tomcat yapılandırması en fazla Iş parçacığı sayısı|Dağıtım, AppName, Pod|
|Tomcat. Threads. Current|Yes|Tomcat. Threads. Current|Count|Toplam|Tomcat geçerli Iş parçacığı sayısı|Dağıtım, AppName, Pod|
|toplam istek sayısı|Yes|toplam istek sayısı|Count|Ortalama|İşlemin ömrü boyunca toplam istek sayısı|Dağıtım, AppName, Pod|
|çalışma kümesi|Yes|çalışma kümesi|Count|Ortalama|İşlem tarafından kullanılan çalışma kümesi miktarı (MB)|Dağıtım, AppName, Pod|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|TotalJob|Yes|Toplam Iş sayısı|Count|Toplam|Toplam iş sayısı|Runbook, durum|
|Totalupdatedeploymentmachinerçalıştırır|Yes|Toplam güncelleştirme dağıtım makinesi çalıştırması|Count|Toplam|Yazılım güncelleştirme dağıtımı çalıştırmasında toplam yazılım güncelleştirme dağıtımı makinesi|SoftwareUpdateConfigurationName, Status, TargetComputer, Softwareupdateconfigurationrunıd|
|TotalUpdateDeploymentRuns|Yes|Toplam güncelleştirme dağıtımı çalıştırmaları|Count|Toplam|Toplam yazılım güncelleştirme dağıtımı çalıştırmaları|SoftwareUpdateConfigurationName, durum|


## <a name="microsoftavsprivateclouds"></a>Microsoft. AVS/Privatebulutlar

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|CapacityLatest|Yes|Veri deposu diski toplam kapasitesi|Bayt|Ortalama|Veri deposundaki toplam disk kapasitesi|dsname|
|DiskUsedPercentage|Yes| Kullanılan veri deposu disk yüzdesi|Yüzde|Ortalama|Veri deposunda kullanılan kullanılabilir disk yüzdesi|dsname|
|EffectiveCpuAverage|Yes|CPU yüzdesi|Yüzde|Ortalama|Kümedeki kullanılan CPU kaynaklarının yüzdesi|:/|
|Efekt|Yes|Ortalama etkin bellek|Bayt|Ortalama|Kümedeki toplam kullanılabilir makine belleği miktarı|:/|
|Denizhemi|Yes|Ortalama bellek ek yükü|Bayt|Ortalama|Sanallaştırma altyapısı tarafından tüketilen fiziksel belleği barındırma|:/|
|Toplam Mbaiçecek|Yes|Ortalama toplam bellek|Bayt|Ortalama|Kümedeki toplam bellek|:/|
|UsageAverage|Yes|Ortalama bellek kullanımı|Yüzde|Ortalama|Toplam yapılandırılmış veya kullanılabilir belleğin yüzdesi olarak bellek kullanımı|:/|
|UsedLatest|Yes|Kullanılan veri deposu diski|Bayt|Ortalama|Veri deposunda kullanılan toplam disk miktarı|dsname|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|CoreCount|No|Adanmış çekirdek sayısı|Count|Toplam|Batch hesabındaki toplam adanmış çekirdek sayısı|Boyut yok|
|CreatingNodeCount|No|Düğüm sayısı oluşturuluyor|Count|Toplam|Oluşturulmakta olan düğüm sayısı|Boyut yok|
|Idıdnodecount|No|Boştaki düğüm sayısı|Count|Toplam|Boştaki düğüm sayısı|Boyut yok|
|JobDeleteCompleteEvent|Yes|İş silme Tamam olayları|Count|Toplam|Başarıyla silinen işlerin toplam sayısı.|No|
|JobDeleteStartEvent|Yes|İş silme başlangıç olayları|Count|Toplam|Silinmesi istenen toplam iş sayısı.|No|
|JobDisableCompleteEvent|Yes|İş tüm olayları devre dışı bırak|Count|Toplam|Başarıyla devre dışı bırakılmış toplam iş sayısı.|No|
|JobDisableStartEvent|Yes|İş devre dışı başlatma olayları|Count|Toplam|Devre dışı bırakılması istenen toplam iş sayısı.|No|
|JobStartEvent|Yes|İş başlatma olayları|Count|Toplam|Başarıyla başlatılmış toplam iş sayısı.|No|
|JobTerminateCompleteEvent|Yes|İş sonlandırma tamamlanma olayları|Count|Toplam|Başarıyla sonlandırılan toplam iş sayısı.|No|
|JobTerminateStartEvent|Yes|İş sonlandırma başlangıç olayları|Count|Toplam|Sonlandırılması istenen toplam iş sayısı.|No|
|LeavingPoolNodeCount|No|Havuz düğüm sayısından çıkılıyor|Count|Toplam|Havuzdan ayrılma düğüm sayısı|Boyut yok|
|LowPriorityCoreCount|No|LowPriority çekirdek sayısı|Count|Toplam|Batch hesabındaki toplam düşük öncelikli çekirdek sayısı|Boyut yok|
|OfflineNodeCount|No|Çevrimdışı düğüm sayısı|Count|Toplam|Çevrimdışı düğüm sayısı|Boyut yok|
|PoolCreateEvent|Yes|Havuz oluşturma olayları|Count|Toplam|Oluşturulan Toplam Havuz sayısı|poolId|
|PoolDeleteCompleteEvent|Yes|Havuz silme Tamam olayları|Count|Toplam|Tamamlanan toplam havuz silme sayısı|poolId|
|PoolDeleteStartEvent|Yes|Havuz silme başlangıç olayları|Count|Toplam|Başlatılmış olan toplam havuz silme sayısı|poolId|
|PoolResizeCompleteEvent|Yes|Havuz yeniden boyutlandırma Tamam olayları|Count|Toplam|Tamamlanan toplam havuz yeniden boyutlandırmaları sayısı|poolId|
|PoolResizeStartEvent|Yes|Havuz yeniden boyutlandırma başlangıç olayları|Count|Toplam|Başlatılan toplam havuz yeniden boyutlandırmaları sayısı|poolId|
|PreemptedNodeCount|No|Önden düğüm sayısı|Count|Toplam|Yok eden düğüm sayısı|Boyut yok|
|RebootingNodeCount|No|Düğüm sayısı yeniden başlatılıyor|Count|Toplam|Yeniden başlatma düğümlerinin sayısı|Boyut yok|
|ReimagingNodeCount|No|Yeniden Imaging düğüm sayısı|Count|Toplam|Yeniden görüntüleme düğümlerinin sayısı|Boyut yok|
|RunningNodeCount|No|Çalışan düğüm sayısı|Count|Toplam|Çalışan düğümlerin sayısı|Boyut yok|
|StartingNodeCount|No|Başlangıç düğüm sayısı|Count|Toplam|Başlayan düğüm sayısı|Boyut yok|
|StartTaskFailedNodeCount|No|Başlangıç görevi başarısız düğüm sayısı|Count|Toplam|Başlangıç görevinin başarısız olduğu düğüm sayısı|Boyut yok|
|TaskCompleteEvent|Yes|Görev tamamlanma olayları|Count|Toplam|Tamamlanan toplam görev sayısı|poolId, iş kimliği|
|TaskFailEvent|Yes|Görev başarısız olayları|Count|Toplam|Hatalı durumda tamamlanan toplam görev sayısı|poolId, iş kimliği|
|TaskStartEvent|Yes|Görev başlatma olayları|Count|Toplam|Başlatılan toplam görev sayısı|poolId, iş kimliği|
|TotalLowPriorityNodeCount|No|Low-Priority düğüm sayısı|Count|Toplam|Batch hesabındaki toplam düşük öncelikli düğüm sayısı|Boyut yok|
|TotalNodeCount|No|Adanmış düğüm sayısı|Count|Toplam|Batch hesabındaki toplam ayrılmış düğüm sayısı|Boyut yok|
|UnusableNodeCount|No|Kullanılamayan düğüm sayısı|Count|Toplam|Kullanılamayan düğüm sayısı|Boyut yok|
|Waitingforstarttasnot Decount|No|Başlangıç görevi düğüm sayısı bekleniyor|Count|Toplam|Başlangıç görevinin tamamlanması için bekleyen düğüm sayısı|Boyut yok|


## <a name="microsoftbatchaiworkspaces"></a>ChAI/çalışma alanlarını Microsoft.Bat

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Etkin çekirdekler|Yes|Etkin çekirdekler|Count|Ortalama|Etkin çekirdek sayısı|Senaryo, ClusterName|
|Etkin düğümler|Yes|Etkin düğümler|Count|Ortalama|Çalışan düğümlerin sayısı|Senaryo, ClusterName|
|Boştaki çekirdekler|Yes|Boştaki çekirdekler|Count|Ortalama|Boşta çekirdek sayısı|Senaryo, ClusterName|
|Boştaki düğümler|Yes|Boştaki düğümler|Count|Ortalama|Boştaki düğüm sayısı|Senaryo, ClusterName|
|İş tamamlandı|Yes|İş tamamlandı|Count|Toplam|Tamamlanan iş sayısı|Senaryo, ClusterName, ResultType|
|İş gönderildi|Yes|İş gönderildi|Count|Toplam|Gönderilen iş sayısı|Senaryo, ClusterName|
|Çekirdekleri bırakma|Yes|Çekirdekleri bırakma|Count|Ortalama|Bırakma çekirdekleri sayısı|Senaryo, ClusterName|
|Düğümlerden çıkılıyor|Yes|Düğümlerden çıkılıyor|Count|Ortalama|Bırakma düğümü sayısı|Senaryo, ClusterName|
|Önden çekirdekler|Yes|Önden çekirdekler|Count|Ortalama|Yok edilecek çekirdek sayısı|Senaryo, ClusterName|
|Önden düğümler|Yes|Önden düğümler|Count|Ortalama|Yok eden düğüm sayısı|Senaryo, ClusterName|
|Kota kullanım yüzdesi|Yes|Kota kullanım yüzdesi|Count|Ortalama|Kullanılan Kota yüzdesi|Senaryo, ClusterName, VmFamilyName, VmPriority|
|Toplam çekirdek sayısı|Yes|Toplam çekirdek sayısı|Count|Ortalama|Toplam çekirdek sayısı|Senaryo, ClusterName|
|Toplam düğüm sayısı|Yes|Toplam düğüm sayısı|Count|Ortalama|Toplam düğüm sayısı|Senaryo, ClusterName|
|Kullanılamayan çekirdekler|Yes|Kullanılamayan çekirdekler|Count|Ortalama|Kullanılamayan çekirdek sayısı|Senaryo, ClusterName|
|Kullanılamayan düğümler|Yes|Kullanılamayan düğümler|Count|Ortalama|Kullanılamayan düğüm sayısı|Senaryo, ClusterName|

## <a name="microsoftbingaccounts"></a>Microsoft. Bing/hesaplar

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Blockedçağrılarında|Yes|Engellenen çağrılar|Count|Toplam|Oran veya kota sınırını aşan çağrı sayısı|ApiName, ServingRegion, StatusCode|
|ClientErrors|Yes|İstemci hataları|Count|Toplam|İstemci hatası olan çağrı sayısı (HTTP durum kodu 4xx)|ApiName, ServingRegion, StatusCode|
|Dataın|Yes|Içindeki veriler|Bayt|Toplam|Gelen istek Içeriği-bayt cinsinden uzunluk|ApiName, ServingRegion, StatusCode|
|Veri çıkışı|Yes|Giden veriler|Bayt|Toplam|Giden Yanıt Içeriği-bayt cinsinden uzunluk|ApiName, ServingRegion, StatusCode|
|Gecikme süresi|Yes|Gecikme süresi|Mayacak|Ortalama|Milisaniye cinsinden gecikme|ApiName, ServingRegion, StatusCode|
|ServerErrors|Yes|Sunucu hataları|Count|Toplam|Herhangi bir sunucu hatası olan çağrı sayısı (HTTP durum kodu 5xx)|ApiName, ServingRegion, StatusCode|
|Başarılı çağrılar|Yes|Başarılı çağrılar|Count|Toplam|Başarılı çağrı sayısı (HTTP durum kodu 2xx)|ApiName, ServingRegion, StatusCode|
|Toplam çağrılar|Yes|Toplam çağrı sayısı|Count|Toplam|Toplam çağrı sayısı|ApiName, ServingRegion, StatusCode|
|Toplam hata sayısı|Yes|Toplam Hata Sayısı|Count|Toplam|Herhangi bir hata içeren çağrı sayısı (HTTP durum kodu 4xx veya 5xx)|ApiName, ServingRegion, StatusCode|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. Blockzincirine/blockchainMembers

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Yayınsaysedcount|Yes|Yayınadı, yayınadı|Count|Ortalama|İşlenen işlem sayısı.|Düğüm, kanal, tür, durum|
|Chaincodeexecutezamanaşımları|Yes|ChaincodeExecuteTimeoutsDisplayName|Count|Ortalama|Zaman aşımına uğramış chaincode yürütmelerinin sayısı (Init veya Invoke).|Düğüm, chaincode|
|Chaincodelaunchhatalarıyla|Yes|ChaincodeLaunchFailuresDisplayName|Count|Ortalama|Başarısız olan chaincode tarafından başlatılan sayı.|Düğüm, chaincode|
|Chaincodelaunchzamanaşımları|Yes|ChaincodeLaunchTimeoutsDisplayName|Count|Ortalama|Zaman aşımına uğrayan chaincode tarafından başlatılan sayı.|Düğüm, chaincode|
|ChaincodeShimRequestsCompleted|Yes|ChaincodeShimRequestsCompletedDisplayName|Count|Ortalama|Yürütülen chaincode dolgusu isteklerinin sayısı.|Düğüm, tür, kanal, chaincode, başarı|
|ChaincodeShimRequestsReceived|Yes|ChaincodeShimRequestsReceivedDisplayName|Count|Ortalama|Alınan chaincode dolgusu isteklerinin sayısı.|Düğüm, tür, kanal, chaincode|
|ClusterCommEgressQueueCapacity|Yes|ClusterCommEgressQueueCapacityDisplayName|Count|Ortalama|Çıkış sırasının kapasitesi.|Düğüm, konak, msg_type, kanal|
|ClusterCommEgressQueueLength|Yes|ClusterCommEgressQueueLengthDisplayName|Count|Ortalama|Çıkış sırasının uzunluğu.|Düğüm, konak, msg_type, kanal|
|Clustercommegressqueueçalışanları|Yes|ClusterCommEgressQueueWorkersDisplayName|Count|Ortalama|Çıkış kuyruğu çalışanlarının sayısı.|Düğüm, kanal|
|ClusterCommEgressStreamCount|Yes|ClusterCommEgressStreamCountDisplayName|Count|Ortalama|Diğer düğümlere akış sayısı.|Düğüm, kanal|
|ClusterCommEgressTlsConnectionCount|Yes|ClusterCommEgressTlsConnectionCountDisplayName|Count|Ortalama|Diğer düğümlere yönelik TLS bağlantısı sayısı.|Düğüm|
|ClusterCommIngressStreamCount|Yes|ClusterCommIngressStreamCountDisplayName|Count|Ortalama|Diğer düğümlerden akış sayısı.|Düğüm|
|ClusterCommMsgDroppedCount|Yes|ClusterCommMsgDroppedCountDisplayName|Count|Ortalama|Bırakılan ileti sayısı.|Düğüm, ana bilgisayar, kanal|
|Connectionkabul edildi|Yes|Kabul edilen bağlantılar|Count|Toplam|Kabul edilen bağlantılar|Düğüm|
|ConnectionActive|Yes|Etkin Bağlantılar|Count|Ortalama|Etkin Bağlantılar|Düğüm|
|Connectionişlenmiş|Yes|İşlenmiş bağlantılar|Count|Toplam|İşlenmiş bağlantılar|Düğüm|
|ConsensusEtcdraftActiveNodes|Yes|ConsensusEtcdraftActiveNodesDisplayName|Count|Ortalama|Bu kanaldaki etkin düğümlerin sayısı.|Düğüm, kanal|
|ConsensusEtcdraftClusterSize|Yes|ConsensusEtcdraftClusterSizeDisplayName|Count|Ortalama|Bu kanaldaki düğüm sayısı.|Düğüm, kanal|
|ConsensusEtcdraftCommittedBlockNumber|Yes|ConsensusEtcdraftCommittedBlockNumberDisplayName|Count|Ortalama|Yürütülen en son bloğun blok numarası.|Düğüm, kanal|
|ConsensusEtcdraftConfigProposalsReceived|Yes|Consensusetcdraftconfigproposalsreceived DisplayName|Count|Ortalama|Yapılandırma türü işlemleri için alınan toplam teklif sayısı.|Düğüm, kanal|
|Consensusetcdraftisöncü|Yes|ConsensusEtcdraftIsLeaderDisplayName|Count|Ortalama|Geçerli düğümün liderlik durumu: 1 öncüsü ise 0.|Düğüm, kanal|
|ConsensusEtcdraftLeaderChanges|Yes|ConsensusEtcdraftLeaderChangesDisplayName|Count|Ortalama|İşlemin başlamasından itibaren öncü değişiklik sayısı.|Düğüm, kanal|
|ConsensusEtcdraftNormalProposalsReceived|Yes|Consensusetcdraftnormalproposalsreceived DisplayName|Count|Ortalama|Normal tür işlemleri için alınan tekliflerin toplam sayısı.|Düğüm, kanal|
|ConsensusEtcdraftProposalFailures|Yes|ConsensusEtcdraftProposalFailuresDisplayName|Count|Ortalama|Teklif hatalarının sayısı.|Düğüm, kanal|
|ConsensusEtcdraftSnapshotBlockNumber|Yes|ConsensusEtcdraftSnapshotBlockNumberDisplayName|Count|Ortalama|En son anlık görüntünün blok numarası.|Düğüm, kanal|
|ConsensusKafkaBatchSize|Yes|ConsensusKafkaBatchSizeDisplayName|Count|Ortalama|Konulara gönderilen bayt cinsinden ortalama toplu iş boyutu.|Düğüm, konu|
|ConsensusKafkaCompressionRatio|Yes|ConsensusKafkaCompressionRatioDisplayName|Count|Ortalama|Konular için Ortalama sıkıştırma oranı (yüzde olarak).|Düğüm, konu|
|ConsensusKafkaIncomingByteRate|Yes|ConsensusKafkaIncomingByteRateDisplayName|Count|Ortalama|Bayt/saniye okumayı kapatma aracıları.|Düğüm, broker_id|
|ConsensusKafkaLastOffsetPersisted|Yes|ConsensusKafkaLastOffsetPersistedDisplayName|Count|Ortalama|En son yürütülen bloğun blok meta verilerinde belirtilen fark.|Düğüm, kanal|
|ConsensusKafkaOutgoingByteRate|Yes|ConsensusKafkaOutgoingByteRateDisplayName|Count|Ortalama|Aracılar için yazılan bayt/saniye.|Düğüm, broker_id|
|ConsensusKafkaRecordSendRate|Yes|ConsensusKafkaRecordSendRateDisplayName|Count|Ortalama|Konuların bir saniyede gönderilmesi için kayıt sayısı.|Düğüm, konu|
|ConsensusKafkaRecordsPerRequest|Yes|ConsensusKafkaRecordsPerRequestDisplayName|Count|Ortalama|Konuya istek başına gönderilen ortalama kayıt sayısı.|Düğüm, konu|
|ConsensusKafkaRequestLatency|Yes|ConsensusKafkaRequestLatencyDisplayName|Count|Ortalama|MS ile aracılar arasındaki ortalama istek gecikmesi.|Düğüm, broker_id|
|ConsensusKafkaRequestRate|Yes|ConsensusKafkaRequestRateDisplayName|Count|Ortalama|Aracılar için istekler/saniye gönderilir.|Düğüm, broker_id|
|ConsensusKafkaRequestSize|Yes|ConsensusKafkaRequestSizeDisplayName|Count|Ortalama|Ortalama istek boyutu, aracılar için bayt cinsinden.|Düğüm, broker_id|
|ConsensusKafkaResponseRate|Yes|ConsensusKafkaResponseRateDisplayName|Count|Ortalama|Aracılar için istekler/saniye gönderilir.|Düğüm, broker_id|
|ConsensusKafkaResponseSize|Yes|ConsensusKafkaResponseSizeDisplayName|Count|Ortalama|Aracılardan bayt cinsinden ortalama yanıt boyutu.|Düğüm, broker_id|
|CpuUsagePercentageInDouble|Yes|CPU kullanım yüzdesi|Yüzde|Maksimum|CPU kullanım yüzdesi|Düğüm|
|Teslimat bloğu|Yes|DeliverBlocksSentDisplayName|Count|Ortalama|Teslim hizmeti tarafından gönderilen blokların sayısı.|Düğüm, kanal, filtrelenmiş, data_type|
|Teslimat Isteği tamamlandı|Yes|Teslimat Requestscompleteddisplayname|Count|Ortalama|Tamamlanan teslim isteği sayısı.|Düğüm, kanal, filtrelenmiş, data_type, başarılı|
|Teslimat Isteği alındı|Yes|Teslimat Requestsreceived DisplayName|Count|Ortalama|Alınan teslim isteği sayısı.|Düğüm, kanal, filtrelenmiş, data_type|
|Akışlı Msclosed|Yes|Teslimat Streamscloseddisplayname|Count|Ortalama|Teslim hizmeti için kapatılmış olan GRPC akışlarının sayısı.|Düğüm|
|Teslimsaçıldı|Yes|Akstreammsopeneddisplayname|Count|Ortalama|Teslim hizmeti için açılmış GRPC akışlarının sayısı.|Düğüm|
|EndorserChaincodeInstantiationFailures|Yes|EndorserChaincodeInstantiationFailuresDisplayName|Count|Ortalama|Başarısız olan chaincode örneklerinin veya yükseltmenin sayısı.|Düğüm, kanal, chaincode|
|Onaylama başarısız|Yes|Onaylama onaylaması|Count|Ortalama|Yinelenen işlem KIMLIĞI nedeniyle başarısız olan tekliflerin sayısı.|Düğüm, kanal, chaincode|
|Onaylama başarısız|Yes|Doğrulımauıredisplayname|Count|Ortalama|Başarısız olan onaylama sayısı.|Düğüm, kanal, chaincode, chaincodeerror|
|EndorserProposalAclFailures|Yes|EndorserProposalAclFailuresDisplayName|Count|Ortalama|ACL denetimlerinin başarısız olduğu tekliflerin sayısı.|Düğüm, kanal, chaincode|
|EndorserProposalSimulationFailures|Yes|EndorserProposalSimulationFailuresDisplayName|Count|Ortalama|Başarısız teklif benzetimleri sayısı.|Düğüm, kanal, chaincode|
|Doğrulayıcı alındı|Yes|Onaylama DisplayName|Count|Ortalama|Alınan tekliflerin sayısı.|Düğüm|
|Onaylama başarısızlıklarıyla doğrulama|Yes|Onaylama, doğrulama Failuresdisplayname|Count|Ortalama|İlk doğrulamanın başarısız olduğu tekliflerin sayısı.|Düğüm|
|Onaylama önerileri|Yes|Onaylama onayadı doğrulandı|Count|Ortalama|Başarılı tekliflerin sayısı.|Düğüm|
|FabricVersion|Yes|FabricVersionDisplayName|Count|Ortalama|Dokunun etkin sürümü.|Düğüm, sürüm|
|Gossıpcommiletileri alındı|Yes|Gossıpcommiletileri DisplayName|Count|Ortalama|Alınan ileti sayısı.|Düğüm|
|Gossıpcommiletileri gönderildi|Yes|GossipCommMessagesSentDisplayName|Count|Ortalama|Gönderilen ileti sayısı.|Düğüm|
|GossipCommOverflowCount|Yes|GossipCommOverflowCountDisplayName|Count|Ortalama|Giden kuyruk arabelleğinin taşma sayısı.|Düğüm|
|Gossıpleaderelectionöncü|Yes|GossipLeaderElectionLeaderDisplayName|Count|Ortalama|Eş, öncü (1) veya İzleyici (0).|Düğüm, kanal|
|Gossıpmembershiptotalpeersbilinen|Yes|Gossıpmembershiptotalpeersknowndisplayname|Count|Ortalama|Toplam bilinen eş.|Düğüm, kanal|
|GossipPayloadBufferSize|Yes|GossipPayloadBufferSizeDisplayName|Count|Ortalama|Yük arabelleğinin boyutu.|Düğüm, kanal|
|Gossıpstateheight|Yes|Gossıpstateheightdisplayname|Count|Ortalama|Geçerli defter yüksekliği.|Düğüm, kanal|
|GrpcCommConnClosed|Yes|GrpcCommConnClosedDisplayName|Count|Ortalama|gRPC bağlantıları kapatıldı. Açık eksi kapalı bağlantısı etkin bağlantı sayısıdır.|Düğüm|
|GrpcCommConnOpened|Yes|GrpcCommConnOpenedDisplayName|Count|Ortalama|gRPC bağlantıları açıldı. Açık eksi kapalı bağlantısı etkin bağlantı sayısıdır.|Düğüm|
|Grpcserverstreamileti, alındı|Yes|Grpcserverstreamiletireceived DisplayName|Count|Ortalama|Alınan akış iletilerinin sayısı.|Node, Service, yöntemi|
|Grpcserverstreamiletiler gönderildi|Yes|GrpcServerStreamMessagesSentDisplayName|Count|Ortalama|Gönderilen akış iletilerinin sayısı.|Node, Service, yöntemi|
|GrpcServerStreamRequestsCompleted|Yes|GrpcServerStreamRequestsCompletedDisplayName|Count|Ortalama|Tamamlanan akış isteği sayısı.|Düğüm, hizmet, yöntem, kod|
|GrpcServerUnaryRequestsReceived|Yes|Grpcserverunaryrequestsreceived DisplayName|Count|Ortalama|Alınan birli istek sayısı.|Node, Service, yöntemi|
|Ioreadbytes|Yes|GÇ okuma baytları|Bayt|Toplam|GÇ okuma baytları|Düğüm|
|IOWriteBytes|Yes|GÇ Yazma baytları|Bayt|Toplam|GÇ Yazma baytları|Düğüm|
|LedgerBlockchainHeight|Yes|LedgerBlockchainHeightDisplayName|Count|Ortalama|Bloklar içindeki zincirin yüksekliği.|Düğüm, kanal|
|LedgerTransactionCount|Yes|LedgerTransactionCountDisplayName|Count|Ortalama|İşlenen işlem sayısı.|Düğüm, kanal, transaction_type, chaincode, validation_code|
|LoggingEntriesChecked|Yes|LoggingEntriesCheckedDisplayName|Count|Ortalama|Etkin günlük kaydı düzeyine göre denetlenen günlük girdisi sayısı.|Düğüm, düzey|
|Loggingentriesyazıldı|Yes|LoggingEntriesWrittenDisplayName|Count|Ortalama|Yazılan günlük girdisi sayısı.|Düğüm, düzey|
|MemoryLimit|Yes|Bellek sınırı|Bayt|Ortalama|Bellek sınırı|Düğüm|
|MemoryUsage|Yes|Bellek Kullanımı|Bayt|Ortalama|Bellek Kullanımı|Düğüm|
|MemoryUsagePercentageInDouble|Yes|Bellek kullanım yüzdesi|Yüzde|Ortalama|Bellek kullanım yüzdesi|Düğüm|
|PendingTransactions|Yes|Bekleyen Işlemler|Count|Ortalama|Bekleyen Işlemler|Düğüm|
|Processedbkilitler|Yes|İşlenen bloklar|Count|Toplam|İşlenen bloklar|Düğüm|
|ProcessedTransactions|Yes|İşlenen Işlemler|Count|Toplam|İşlenen Işlemler|Düğüm|
|QueuedTransactions|Yes|Kuyruğa alınmış Işlemler|Count|Ortalama|Kuyruğa alınmış Işlemler|Düğüm|
|RequestHandled|Yes|İşlenmiş Istekler|Count|Toplam|İşlenmiş Istekler|Düğüm|
|StorageUsage|Yes|Depolama alanı kullanımı|Bayt|Ortalama|Depolama alanı kullanımı|Düğüm|


## <a name="microsoftbotservicebotservices"></a>Microsoft. botservice/botservices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|RequestLatency|Yes|İstek gecikme süresi|Mayacak|Toplam|İsteği işlemek için sunucu tarafından harcanan süre|İşlem, kimlik doğrulama, protokol, veri merkezi|
|RequestsTraffic|Yes|İstek trafiği|Yüzde|Count|Yapılan Istek sayısı|İşlem, kimlik doğrulama, protokol, StatusCode, StatusCodeClass, DataCenter|


## <a name="microsoftcacheredis"></a>Microsoft. Cache/redsıs

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|allcachehits|Yes|Önbellek ısabetleri (örnek tabanlı)|Count|Toplam||Parça, bağlantı noktası, birincil|
|allcacheisabetsizliği|Yes|Önbellekte bulunamayanlar (örnek tabanlı)|Count|Toplam||Parça, bağlantı noktası, birincil|
|allcacheRead|Yes|Önbellek Okuma (örnek tabanlı)|BytesPerSecond|Maksimum||Parça, bağlantı noktası, birincil|
|allcacheWrite|Yes|Önbellek yazma (örnek tabanlı)|BytesPerSecond|Maksimum||Parça, bağlantı noktası, birincil|
|allconnectedclients|Yes|Bağlı Istemciler (örnek tabanlı)|Count|Maksimum||Parça, bağlantı noktası, birincil|
|allevictedkeys|Yes|Çıkarılan anahtarlar (örnek tabanlı)|Count|Toplam||Parça, bağlantı noktası, birincil|
|allexpiredkeys|Yes|Süre dolma anahtarları (örnek tabanlı)|Count|Toplam||Parça, bağlantı noktası, birincil|
|allgetcommands|Yes|Alır (örnek tabanlı)|Count|Toplam||Parça, bağlantı noktası, birincil|
|Allowperationspersecond|Yes|Saniye başına işlem (örnek tabanlı)|Count|Maksimum||Parça, bağlantı noktası, birincil|
|allserverLoad|Yes|Sunucu yükü (örnek tabanlı)|Yüzde|Maksimum||Parça, bağlantı noktası, birincil|
|allsetcommands|Yes|Ayarlar (örnek tabanlı)|Count|Toplam||Parça, bağlantı noktası, birincil|
|alltotalcommandsişlendi|Yes|Toplam Işlem (örnek tabanlı)|Count|Toplam||Parça, bağlantı noktası, birincil|
|alltotalkeys|Yes|Toplam anahtar (örnek tabanlı)|Count|Maksimum||Parça, bağlantı noktası, birincil|
|allusedmemory|Yes|Kullanılan bellek (örnek tabanlı)|Bayt|Maksimum||Parça, bağlantı noktası, birincil|
|allusedmemorypercentage|Yes|Kullanılan bellek yüzdesi (örnek tabanlı)|Yüzde|Maksimum||Parça, bağlantı noktası, birincil|
|allusedmemoryRss|Yes|Kullanılan bellek RSS (örnek tabanlı)|Bayt|Maksimum||Parça, bağlantı noktası, birincil|
|cachehits|Yes|İsabetli Önbellek Okuma Sayısı|Count|Toplam||Parça|
|cachehits0|Yes|Önbellek ısabetleri (parça 0)|Count|Toplam||Boyut yok|
|cachehits1|Yes|Önbellek ısabetleri (parça 1)|Count|Toplam||Boyut yok|
|cachehits2|Yes|Önbellek ısabetleri (parça 2)|Count|Toplam||Boyut yok|
|cachehits3|Yes|Önbellek ısabetleri (parça 3)|Count|Toplam||Boyut yok|
|cachehits4|Yes|Önbellek ısabetleri (parça 4)|Count|Toplam||Boyut yok|
|cachehits5|Yes|Önbellek ısabetleri (parça 5)|Count|Toplam||Boyut yok|
|cachehits6|Yes|Önbellek ısabetleri (parça 6)|Count|Toplam||Boyut yok|
|cachehits7|Yes|Önbellek ısabetleri (parça 7)|Count|Toplam||Boyut yok|
|cachehits8|Yes|Önbellek ısabetleri (parça 8)|Count|Toplam||Boyut yok|
|cachehits9|Yes|Önbellek ısabetleri (parça 9)|Count|Toplam||Boyut yok|
|cacheLatency|Yes|Önbellek gecikmesi mikrosaniye (Önizleme)|Count|Ortalama||Parça|
|cacheisabetsizlik|Yes|İsabetsiz Önbellek Okuma Sayısı|Count|Toplam||Parça|
|cachemisses0|Yes|Önbellekte bulunamayanlar (parça 0)|Count|Toplam||Boyut yok|
|cachemisses1|Yes|Önbellekte bulunamayanlar (parça 1)|Count|Toplam||Boyut yok|
|cachemisses2|Yes|Önbellekte bulunamayanlar (parça 2)|Count|Toplam||Boyut yok|
|cachemisses3|Yes|Önbellekte bulunamayanlar (parça 3)|Count|Toplam||Boyut yok|
|cachemisses4|Yes|Önbellekte bulunamayanlar (parça 4)|Count|Toplam||Boyut yok|
|cachemisses5|Yes|Önbellekte bulunamayanlar (parça 5)|Count|Toplam||Boyut yok|
|cachemisses6|Yes|Önbellekte bulunamayanlar (parça 6)|Count|Toplam||Boyut yok|
|cachemisses7|Yes|Önbellekte bulunamayanlar (parça 7)|Count|Toplam||Boyut yok|
|cachemisses8|Yes|Önbellekte bulunamayanlar (parça 8)|Count|Toplam||Boyut yok|
|cachemisses9|Yes|Önbellekte bulunamayanlar (parça 9)|Count|Toplam||Boyut yok|
|cachemissrate|Yes|Önbellek Isabetsizlik oranı|Yüzde|cachemissrate||Parça|
|cacheRead|Yes|Önbellek Okuması|BytesPerSecond|Maksimum||Parça|
|cacheRead0|Yes|Önbellek Okuma (parça 0)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead1|Yes|Önbellek Okuma (parça 1)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead2|Yes|Önbellek Okuma (parça 2)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead3|Yes|Önbellek Okuma (parça 3)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead4|Yes|Önbellek Okuma (parça 4)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead5|Yes|Önbellek Okuma (parça 5)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead6|Yes|Önbellek Okuma (parça 6)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead7|Yes|Önbellek Okuma (parça 7)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead8|Yes|Önbellek Okuma (parça 8)|BytesPerSecond|Maksimum||Boyut yok|
|cacheRead9|Yes|Önbellek Okuma (parça 9)|BytesPerSecond|Maksimum||Boyut yok|
|cacheWrite|Yes|Önbellek Yazması|BytesPerSecond|Maksimum||Parça|
|cacheWrite0|Yes|Önbellek yazma (parça 0)|BytesPerSecond|Maksimum||Boyut yok|
|cacheWrite1|Yes|Önbellek yazma (parça 1)|BytesPerSecond|Maksimum||Boyut yok|
|cacheWrite2|Yes|Önbellek yazma (parça 2)|BytesPerSecond|Maksimum||Boyut yok|
|cacheWrite3|Yes|Önbellek yazma (parça 3)|BytesPerSecond|Maksimum||Boyut yok|
|cacheWrite4|Yes|Önbellek yazma (parça 4)|BytesPerSecond|Maksimum||Boyut yok|
|cacheWrite5|Yes|Önbellek yazma (parça 5)|BytesPerSecond|Maksimum||Boyut yok|
|cacheWrite6|Yes|Önbellek yazma (parça 6)|BytesPerSecond|Maksimum||Boyut yok|
|cacheWrite7|Yes|Önbellek yazma (parça 7)|BytesPerSecond|Maksimum||Boyut yok|
|cacheWrite8|Yes|Önbellek yazma (parça 8)|BytesPerSecond|Maksimum||Boyut yok|
|cacheWrite9|Yes|Önbellek yazma (parça 9)|BytesPerSecond|Maksimum||Boyut yok|
|connectedistemcileri|Yes|Bağlı İstemciler|Count|Maksimum||Parça|
|connectedclients0|Yes|Bağlı Istemciler (parça 0)|Count|Maksimum||Boyut yok|
|connectedclients1|Yes|Bağlı Istemciler (parça 1)|Count|Maksimum||Boyut yok|
|connectedclients2|Yes|Bağlı Istemciler (parça 2)|Count|Maksimum||Boyut yok|
|connectedclients3|Yes|Bağlı Istemciler (parça 3)|Count|Maksimum||Boyut yok|
|connectedclients4|Yes|Bağlı Istemciler (parça 4)|Count|Maksimum||Boyut yok|
|connectedclients5|Yes|Bağlı Istemciler (parça 5)|Count|Maksimum||Boyut yok|
|connectedclients6|Yes|Bağlı Istemciler (parça 6)|Count|Maksimum||Boyut yok|
|connectedclients7|Yes|Bağlı Istemciler (parça 7)|Count|Maksimum||Boyut yok|
|connectedclients8|Yes|Bağlı Istemciler (parça 8)|Count|Maksimum||Boyut yok|
|connectedclients9|Yes|Bağlı Istemciler (parça 9)|Count|Maksimum||Boyut yok|
|hatalar|Yes|Hatalar|Count|Maksimum||Shardıd, ErrorType|
|çıkarılan anahtarlar|Yes|Çıkarılan Anahtarlar|Count|Toplam||Parça|
|evictedkeys0|Yes|Çıkarılan anahtarlar (parça 0)|Count|Toplam||Boyut yok|
|evictedkeys1|Yes|Çıkarılan anahtarlar (parça 1)|Count|Toplam||Boyut yok|
|evictedkeys2|Yes|Çıkarılan anahtarlar (parça 2)|Count|Toplam||Boyut yok|
|evictedkeys3|Yes|Çıkarılan anahtarlar (parça 3)|Count|Toplam||Boyut yok|
|evictedkeys4|Yes|Çıkarılan anahtarlar (parça 4)|Count|Toplam||Boyut yok|
|evictedkeys5|Yes|Çıkarılan anahtarlar (parça 5)|Count|Toplam||Boyut yok|
|evictedkeys6|Yes|Çıkarılan anahtarlar (parça 6)|Count|Toplam||Boyut yok|
|evictedkeys7|Yes|Çıkarılan anahtarlar (parça 7)|Count|Toplam||Boyut yok|
|evictedkeys8|Yes|Çıkarılan anahtarlar (parça 8)|Count|Toplam||Boyut yok|
|evictedkeys9|Yes|Çıkarılan anahtarlar (parça 9)|Count|Toplam||Boyut yok|
|expiredkeys|Yes|Süresi Dolan Anahtarlar|Count|Toplam||Parça|
|expiredkeys0|Yes|Süre dolma anahtarları (parça 0)|Count|Toplam||Boyut yok|
|expiredkeys1|Yes|Süre dolma anahtarları (parça 1)|Count|Toplam||Boyut yok|
|expiredkeys2|Yes|Süre dolma anahtarları (parça 2)|Count|Toplam||Boyut yok|
|expiredkeys3|Yes|Süre dolma anahtarları (parça 3)|Count|Toplam||Boyut yok|
|expiredkeys4|Yes|Süre dolma anahtarları (parça 4)|Count|Toplam||Boyut yok|
|expiredkeys5|Yes|Süre dolma anahtarları (parça 5)|Count|Toplam||Boyut yok|
|expiredkeys6|Yes|Süre dolma anahtarları (parça 6)|Count|Toplam||Boyut yok|
|expiredkeys7|Yes|Süre dolma anahtarları (parça 7)|Count|Toplam||Boyut yok|
|expiredkeys8|Yes|Süre dolma anahtarları (parça 8)|Count|Toplam||Boyut yok|
|expiredkeys9|Yes|Süre dolma anahtarları (parça 9)|Count|Toplam||Boyut yok|
|GetCommands|Yes|Alınanlar|Count|Toplam||Parça|
|getcommands0|Yes|Alır (parça 0)|Count|Toplam||Boyut yok|
|getcommands1|Yes|Alır (parça 1)|Count|Toplam||Boyut yok|
|getcommands2|Yes|Alır (parça 2)|Count|Toplam||Boyut yok|
|getcommands3|Yes|Alır (parça 3)|Count|Toplam||Boyut yok|
|getcommands4|Yes|Alır (parça 4)|Count|Toplam||Boyut yok|
|getcommands5|Yes|Alır (parça 5)|Count|Toplam||Boyut yok|
|getcommands6|Yes|Alır (parça 6)|Count|Toplam||Boyut yok|
|getcommands7|Yes|Alır (parça 7)|Count|Toplam||Boyut yok|
|getcommands8|Yes|Al (Shard 8)|Count|Toplam||Boyut yok|
|getcommands9|Yes|Alır (parça 9)|Count|Toplam||Boyut yok|
|operationsPerSecond|Yes|Saniye Başına İşlem|Count|Maksimum||Parça|
|operationsPerSecond0|Yes|Saniyedeki işlem (parça 0)|Count|Maksimum||Boyut yok|
|operationsPerSecond1|Yes|Saniyedeki işlem (parça 1)|Count|Maksimum||Boyut yok|
|operationsPerSecond2|Yes|Saniyedeki işlem (parça 2)|Count|Maksimum||Boyut yok|
|operationsPerSecond3|Yes|Saniyedeki işlem (parça 3)|Count|Maksimum||Boyut yok|
|operationsPerSecond4|Yes|Saniyedeki işlem (parça 4)|Count|Maksimum||Boyut yok|
|operationsPerSecond5|Yes|Saniyedeki işlem (parça 5)|Count|Maksimum||Boyut yok|
|operationsPerSecond6|Yes|Saniyedeki işlem (parça 6)|Count|Maksimum||Boyut yok|
|operationsPerSecond7|Yes|Saniyedeki işlem (parça 7)|Count|Maksimum||Boyut yok|
|operationsPerSecond8|Yes|Saniyedeki işlem (parça 8)|Count|Maksimum||Boyut yok|
|operationsPerSecond9|Yes|Saniyedeki işlem (parça 9)|Count|Maksimum||Boyut yok|
|percentProcessorTime|Yes|CPU|Yüzde|Maksimum||Parça|
|percentProcessorTime0|Yes|CPU (parça 0)|Yüzde|Maksimum||Boyut yok|
|percentProcessorTime1|Yes|CPU (parça 1)|Yüzde|Maksimum||Boyut yok|
|percentProcessorTime2|Yes|CPU (parça 2)|Yüzde|Maksimum||Boyut yok|
|percentProcessorTime3|Yes|CPU (parça 3)|Yüzde|Maksimum||Boyut yok|
|percentProcessorTime4|Yes|CPU (parça 4)|Yüzde|Maksimum||Boyut yok|
|percentProcessorTime5|Yes|CPU (parça 5)|Yüzde|Maksimum||Boyut yok|
|percentProcessorTime6|Yes|CPU (parça 6)|Yüzde|Maksimum||Boyut yok|
|percentProcessorTime7|Yes|CPU (parça 7)|Yüzde|Maksimum||Boyut yok|
|percentProcessorTime8|Yes|CPU (parça 8)|Yüzde|Maksimum||Boyut yok|
|percentProcessorTime9|Yes|CPU (parça 9)|Yüzde|Maksimum||Boyut yok|
|Sunucuyükü|Yes|Sunucu Yükü|Yüzde|Maksimum||Parça|
|serverLoad0|Yes|Sunucu yükü (parça 0)|Yüzde|Maksimum||Boyut yok|
|serverLoad1|Yes|Sunucu yükü (parça 1)|Yüzde|Maksimum||Boyut yok|
|serverLoad2|Yes|Sunucu yükü (parça 2)|Yüzde|Maksimum||Boyut yok|
|serverLoad3|Yes|Sunucu yükü (parça 3)|Yüzde|Maksimum||Boyut yok|
|serverLoad4|Yes|Sunucu yükü (parça 4)|Yüzde|Maksimum||Boyut yok|
|serverLoad5|Yes|Sunucu yükü (parça 5)|Yüzde|Maksimum||Boyut yok|
|serverLoad6|Yes|Sunucu yükü (parça 6)|Yüzde|Maksimum||Boyut yok|
|serverLoad7|Yes|Sunucu yükü (parça 7)|Yüzde|Maksimum||Boyut yok|
|serverLoad8|Yes|Sunucu yükü (parça 8)|Yüzde|Maksimum||Boyut yok|
|serverLoad9|Yes|Sunucu yükü (parça 9)|Yüzde|Maksimum||Boyut yok|
|SetCommands|Yes|Ayarlar|Count|Toplam||Parça|
|setcommands0|Yes|Kümeler (parça 0)|Count|Toplam||Boyut yok|
|setcommands1|Yes|Kümeler (parça 1)|Count|Toplam||Boyut yok|
|setcommands2|Yes|Kümeler (parça 2)|Count|Toplam||Boyut yok|
|setcommands3|Yes|Kümeler (parça 3)|Count|Toplam||Boyut yok|
|setcommands4|Yes|Kümeler (parça 4)|Count|Toplam||Boyut yok|
|setcommands5|Yes|Kümeler (parça 5)|Count|Toplam||Boyut yok|
|setcommands6|Yes|Kümeler (parça 6)|Count|Toplam||Boyut yok|
|setcommands7|Yes|Kümeler (parça 7)|Count|Toplam||Boyut yok|
|setcommands8|Yes|Kümeler (Shard 8)|Count|Toplam||Boyut yok|
|setcommands9|Yes|Kümeler (parça 9)|Count|Toplam||Boyut yok|
|totalcommandsişlendi|Yes|Toplam İşlem Sayısı|Count|Toplam||Parça|
|totalcommandsprocessed0|Yes|Toplam Işlem (parça 0)|Count|Toplam||Boyut yok|
|totalcommandsprocessed1|Yes|Toplam Işlem (parça 1)|Count|Toplam||Boyut yok|
|totalcommandsprocessed2|Yes|Toplam Işlem (parça 2)|Count|Toplam||Boyut yok|
|totalcommandsprocessed3|Yes|Toplam Işlem (parça 3)|Count|Toplam||Boyut yok|
|totalcommandsprocessed4|Yes|Toplam Işlem (parça 4)|Count|Toplam||Boyut yok|
|totalcommandsprocessed5|Yes|Toplam Işlem (parça 5)|Count|Toplam||Boyut yok|
|totalcommandsprocessed6|Yes|Toplam Işlem (parça 6)|Count|Toplam||Boyut yok|
|totalcommandsprocessed7|Yes|Toplam Işlem (parça 7)|Count|Toplam||Boyut yok|
|totalcommandsprocessed8|Yes|Toplam Işlem (parça 8)|Count|Toplam||Boyut yok|
|totalcommandsprocessed9|Yes|Toplam Işlem (parça 9)|Count|Toplam||Boyut yok|
|totalkeys|Yes|Toplam anahtar sayısı|Count|Maksimum||Parça|
|totalkeys0|Yes|Toplam anahtar (parça 0)|Count|Maksimum||Boyut yok|
|totalkeys1|Yes|Toplam anahtar (parça 1)|Count|Maksimum||Boyut yok|
|totalkeys2|Yes|Toplam anahtar (parça 2)|Count|Maksimum||Boyut yok|
|totalkeys3|Yes|Toplam anahtar (parça 3)|Count|Maksimum||Boyut yok|
|totalkeys4|Yes|Toplam anahtar (parça 4)|Count|Maksimum||Boyut yok|
|totalkeys5|Yes|Toplam anahtar (parça 5)|Count|Maksimum||Boyut yok|
|totalkeys6|Yes|Toplam anahtar (parça 6)|Count|Maksimum||Boyut yok|
|totalkeys7|Yes|Toplam anahtar (parça 7)|Count|Maksimum||Boyut yok|
|totalkeys8|Yes|Toplam anahtar (Shard 8)|Count|Maksimum||Boyut yok|
|totalkeys9|Yes|Toplam anahtar (parça 9)|Count|Maksimum||Boyut yok|
|usedmemory|Yes|Kullanılan Bellek|Bayt|Maksimum||Parça|
|usedmemory0|Yes|Kullanılan bellek (parça 0)|Bayt|Maksimum||Boyut yok|
|usedmemory1|Yes|Kullanılan bellek (parça 1)|Bayt|Maksimum||Boyut yok|
|usedmemory2|Yes|Kullanılan bellek (parça 2)|Bayt|Maksimum||Boyut yok|
|usedmemory3|Yes|Kullanılan bellek (parça 3)|Bayt|Maksimum||Boyut yok|
|usedmemory4|Yes|Kullanılan bellek (parça 4)|Bayt|Maksimum||Boyut yok|
|usedmemory5|Yes|Kullanılan bellek (parça 5)|Bayt|Maksimum||Boyut yok|
|usedmemory6|Yes|Kullanılan bellek (parça 6)|Bayt|Maksimum||Boyut yok|
|usedmemory7|Yes|Kullanılan bellek (parça 7)|Bayt|Maksimum||Boyut yok|
|usedmemory8|Yes|Kullanılan bellek (parça 8)|Bayt|Maksimum||Boyut yok|
|usedmemory9|Yes|Kullanılan bellek (parça 9)|Bayt|Maksimum||Boyut yok|
|usedmemorypercentage|Yes|Kullanılan Bellek Yüzdesi|Yüzde|Maksimum||Parça|
|usedmemoryRss|Yes|Kullanılan bellek RSS|Bayt|Maksimum||Parça|
|usedmemoryRss0|Yes|Kullanılan bellek RSS (parça 0)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss1|Yes|Kullanılan bellek RSS (parça 1)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss2|Yes|Kullanılan bellek RSS (parça 2)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss3|Yes|Kullanılan bellek RSS (parça 3)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss4|Yes|Kullanılan bellek RSS (parça 4)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss5|Yes|Kullanılan bellek RSS (parça 5)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss6|Yes|Kullanılan bellek RSS (parça 6)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss7|Yes|Kullanılan bellek RSS (parça 7)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss8|Yes|Kullanılan bellek RSS (parça 8)|Bayt|Maksimum||Boyut yok|
|usedmemoryRss9|Yes|Kullanılan bellek RSS (parça 9)|Bayt|Maksimum||Boyut yok|


## <a name="microsoftcacheredisenterprise"></a>Microsoft. Cache/redisEnterprise

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|cachehits|Yes|İsabetli Önbellek Okuma Sayısı|Count|Toplam||Boyut yok|
|cacheLatency|Yes|Önbellek gecikmesi mikrosaniye (Önizleme)|Count|Ortalama||InstanceId|
|cacheisabetsizlik|Yes|İsabetsiz Önbellek Okuma Sayısı|Count|Toplam||InstanceId|
|cacheRead|Yes|Önbellek Okuması|BytesPerSecond|Maksimum||InstanceId|
|cacheWrite|Yes|Önbellek Yazması|BytesPerSecond|Maksimum||InstanceId|
|connectedistemcileri|Yes|Bağlı İstemciler|Count|Maksimum||InstanceId|
|hatalar|Yes|Hatalar|Count|Maksimum||InstanceId, ErrorType|
|çıkarılan anahtarlar|Yes|Çıkarılan Anahtarlar|Count|Toplam||Boyut yok|
|expiredkeys|Yes|Süresi Dolan Anahtarlar|Count|Toplam||Boyut yok|
|GetCommands|Yes|Alınanlar|Count|Toplam||Boyut yok|
|operationsPerSecond|Yes|Saniye Başına İşlem|Count|Maksimum||Boyut yok|
|percentProcessorTime|Yes|CPU|Yüzde|Maksimum||InstanceId|
|Sunucuyükü|Yes|Sunucu Yükü|Yüzde|Maksimum||Boyut yok|
|SetCommands|Yes|Ayarlar|Count|Toplam||Boyut yok|
|totalcommandsişlendi|Yes|Toplam İşlem Sayısı|Count|Toplam||Boyut yok|
|totalkeys|Yes|Toplam anahtar sayısı|Count|Maksimum||Boyut yok|
|usedmemory|Yes|Kullanılan Bellek|Bayt|Maksimum||Boyut yok|
|usedmemorypercentage|Yes|Kullanılan Bellek Yüzdesi|Yüzde|Maksimum||InstanceId|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Yes|Web uygulaması güvenlik duvarı Istek sayısı|Count|Toplam|Web uygulaması güvenlik duvarı tarafından işlenen istemci isteklerinin sayısı|PolicyName, RuleName, eylem|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profiller

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ByteHitRatio|Yes|Bayt Isabet oranı|Yüzde|Ortalama|Bu, toplam yanıt baytları ile karşılaştırıldığında önbellekten sunulan toplam baytların oranıdır|Uç Nokta|
|OriginHealthPercentage|Yes|Kaynak sistem durumu yüzdesi|Yüzde|Ortalama|AFDX 'ten arka uçlara yapılan başarılı sistem durumu araştırmalarının yüzdesi.|Kaynak, OriginGroup|
|OriginLatency|Yes|Kaynak gecikmesi|Mayacak|Ortalama|İsteğin AFDX Edge tarafından arka uca gönderildiği, AFDX, arka ucun son yanıt baytını almayana kadar hesaplanan süre.|Kaynak, uç nokta|
|OriginRequestCount|Yes|Kaynak Istek sayısı|Count|Toplam|AFDX 'den kaynağa gönderilen istek sayısı.|HttpStatus, HttpStatusGroup, Origin, Endpoint|
|Percentage4XX|Yes|4XX yüzdesi|Yüzde|Ortalama|Yanıt durum kodu 4XX olan tüm istemci isteklerinin yüzdesi|Uç nokta, ClientRegion, ClientCountry|
|Percentage5XX|Yes|5XX yüzdesi|Yüzde|Ortalama|Yanıt durum kodu 5 xx olan tüm istemci isteklerinin yüzdesi|Uç nokta, ClientRegion, ClientCountry|
|Istek sayısı|Yes|İstek Sayısı|Count|Toplam|HTTP/S proxy tarafından hizmet verilen istemci isteklerinin sayısı|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|RequestSize|Yes|İstek boyutu|Bayt|Toplam|İstemcilerden AFDX 'e istek olarak gönderilen bayt sayısı.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|Yanıt boyutu|Yes|Yanıt boyutu|Bayt|Toplam|HTTP/S proxy 'sinden istemcilere yanıt olarak gönderilen baytların sayısı|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|TotalLatency|Yes|Toplam gecikme süresi|Mayacak|Ortalama|İstemci, http/s proxy 'sinden gelen son yanıt baytını kabul edene kadar, istemci isteğinin HTTP/S proxy tarafından alındığı zamana göre hesaplanır|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|WebApplicationFirewallRequestCount|Yes|Web uygulaması güvenlik duvarı Istek sayısı|Count|Toplam|Web uygulaması güvenlik duvarı tarafından işlenen istemci isteklerinin sayısı|PolicyName, RuleName, eylem|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domainNames/yuvalar/roller

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Disk okuma bayt/sn|No|Disk okuma|BytesPerSecond|Ortalama|İzleme dönemi boyunca diskten okunan ortalama bayt.|Roleınstanceıd|
|Disk okuma Işlemi/sn|Yes|Disk okuma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk okuma ıOPS 'si.|Roleınstanceıd|
|Disk yazma bayt/sn|No|Disk yazma|BytesPerSecond|Ortalama|İzleme dönemi boyunca diske yazılan ortalama bayt.|Roleınstanceıd|
|Disk yazma Işlemi/sn|Yes|Disk yazma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk yazma ıOPS 'si.|Roleınstanceıd|
|Ağ Girişi|Yes|Ağ Girişi|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde alınan bayt sayısı (gelen trafik).|Roleınstanceıd|
|Ağ Çıkışı|Yes|Ağ Çıkışı|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde giden bayt sayısı (giden trafik).|Roleınstanceıd|
|CPU yüzdesi|Yes|CPU yüzdesi|Yüzde|Ortalama|Şu anda sanal makineler tarafından kullanılan ayrılmış işlem birimlerinin yüzdesi.|Roleınstanceıd|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Disk okuma bayt/sn|No|Disk okuma|BytesPerSecond|Ortalama|İzleme dönemi boyunca diskten okunan ortalama bayt.|Boyut yok|
|Disk okuma Işlemi/sn|Yes|Disk okuma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk okuma ıOPS 'si.|Boyut yok|
|Disk yazma bayt/sn|No|Disk yazma|BytesPerSecond|Ortalama|İzleme dönemi boyunca diske yazılan ortalama bayt.|Boyut yok|
|Disk yazma Işlemi/sn|Yes|Disk yazma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk yazma ıOPS 'si.|Boyut yok|
|Ağ Girişi|Yes|Ağ Girişi|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde alınan bayt sayısı (gelen trafik).|Boyut yok|
|Ağ Çıkışı|Yes|Ağ Çıkışı|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde giden bayt sayısı (giden trafik).|Boyut yok|
|CPU yüzdesi|Yes|CPU yüzdesi|Yüzde|Ortalama|Şu anda sanal makineler tarafından kullanılan ayrılmış işlem birimlerinin yüzdesi.|Boyut yok|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik|Yes|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmeti için kullanılabilirlik yüzdesi veya belirtilen API işlemi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Çıkış|Yes|Çıkış|Bayt|Toplam|Bayt cinsinden çıkış verisi miktarı. Bu sayı, dış istemciden Azure Depolama'ya çıkan ve Azure içinde çıkan verileri içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Giriş|Yes|Giriş|Bayt|Toplam|Bayt cinsinden giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|SuccessE2ELatency|Yes|Başarı E2E Gecikme Süresi|Mayacak|Ortalama|Bir depolama hizmetine veya belirtilen API işlemine yapılan başarılı isteklerin milisaniye cinsinden uçtan uca gecikmesi. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Başarılı Sunucugecikmesi|Yes|Başarı sunucusu gecikmesi|Mayacak|Ortalama|Azure depolama tarafından başarılı bir isteği işlemek için kullanılan gecikme süresi (milisaniye cinsinden). Bu değer, Başarı E2E Gecikme Süresi’nde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulaması|
|İşlemler|Yes|İşlemler|Count|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı yanıt türü sayısı için ResponseType boyutunu kullanın.|Yanıt türü, coğrafi tür, ApiName, kimlik doğrulama|
|UsedCapacity|No|Kullanılan kapasite|Bayt|Ortalama|Hesabın kullanılan kapasitesi|Boyut yok|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik|Yes|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmeti için kullanılabilirlik yüzdesi veya belirtilen API işlemi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulaması|
|BlobCapacity|No|Blob kapasitesi|Bayt|Ortalama|Depolama hesabının blob hizmeti tarafından bayt olarak kullanılan depolama miktarı.|BlobType, katman|
|BlobCount|No|BLOB sayısı|Count|Ortalama|Depolama hesabının blob hizmetindeki BLOB sayısı.|BlobType, katman|
|ContainerCount|Yes|Blob kapsayıcı sayısı|Count|Ortalama|Depolama hesabının blob hizmetindeki kapsayıcı sayısı.|Boyut yok|
|Çıkış|Yes|Çıkış|Bayt|Toplam|Bayt cinsinden çıkış verisi miktarı. Bu sayı, dış istemciden Azure Depolama'ya çıkan ve Azure içinde çıkan verileri içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Dizin kapasitesi|No|Dizin kapasitesi|Bayt|Ortalama|Bayt cinsinden ADLS 2. (hiyerarşik) dizin tarafından kullanılan depolama miktarı.|Boyut yok|
|Giriş|Yes|Giriş|Bayt|Toplam|Bayt cinsinden giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|SuccessE2ELatency|Yes|Başarı E2E Gecikme Süresi|Mayacak|Ortalama|Bir depolama hizmetine veya belirtilen API işlemine yapılan başarılı isteklerin milisaniye cinsinden uçtan uca gecikmesi. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Başarılı Sunucugecikmesi|Yes|Başarı sunucusu gecikmesi|Mayacak|Ortalama|Azure depolama tarafından başarılı bir isteği işlemek için kullanılan gecikme süresi (milisaniye cinsinden). Bu değer, Başarı E2E Gecikme Süresi’nde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulaması|
|İşlemler|Yes|İşlemler|Count|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı yanıt türü sayısı için ResponseType boyutunu kullanın.|Yanıt türü, coğrafi tür, ApiName, kimlik doğrulama|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik|Yes|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmeti için kullanılabilirlik yüzdesi veya belirtilen API işlemi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulama, FileShare|
|Çıkış|Yes|Çıkış|Bayt|Toplam|Bayt cinsinden çıkış verisi miktarı. Bu sayı, dış istemciden Azure Depolama'ya çıkan ve Azure içinde çıkan verileri içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulama, FileShare|
|Dosya kapasitesi|No|Dosya kapasitesi|Bayt|Ortalama|Bayt olarak depolama hesabının dosya hizmeti tarafından kullanılan depolama miktarı.|Dosya Paylaşımı|
|FileCount|No|Dosya sayısı|Count|Ortalama|Depolama hesabının dosya hizmetindeki dosya sayısı.|Dosya Paylaşımı|
|Dosya ShareCount|No|Dosya paylaşma sayısı|Count|Ortalama|Depolama hesabının dosya hizmetindeki dosya paylaşımlarının sayısı.|Boyut yok|
|Dosya Sharequota|No|Dosya paylaşımının kota boyutu|Bayt|Ortalama|Azure dosyaları hizmeti tarafından bayt olarak kullanılabilecek depolama miktarının üst sınırı.|Dosya Paylaşımı|
|FileShareSnapshotCount|No|Dosya paylaşımının anlık görüntü sayısı|Count|Ortalama|Depolama hesabının dosyalar hizmetindeki paylaşımda bulunan anlık görüntü sayısı.|Dosya Paylaşımı|
|FileShareSnapshotSize|No|Dosya paylaşımının anlık görüntü boyutu|Bayt|Ortalama|Bayt olarak depolama hesabının dosya hizmetindeki anlık görüntüler tarafından kullanılan depolama miktarı.|Dosya Paylaşımı|
|Giriş|Yes|Giriş|Bayt|Toplam|Bayt cinsinden giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulama, FileShare|
|SuccessE2ELatency|Yes|Başarı E2E Gecikme Süresi|Mayacak|Ortalama|Bir depolama hizmetine veya belirtilen API işlemine yapılan başarılı isteklerin milisaniye cinsinden uçtan uca gecikmesi. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulama, FileShare|
|Başarılı Sunucugecikmesi|Yes|Başarı sunucusu gecikmesi|Mayacak|Ortalama|Azure depolama tarafından başarılı bir isteği işlemek için kullanılan gecikme süresi (milisaniye cinsinden). Bu değer, Başarı E2E Gecikme Süresi’nde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulama, FileShare|
|İşlemler|Yes|İşlemler|Count|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı yanıt türü sayısı için ResponseType boyutunu kullanın.|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik|Yes|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmeti için kullanılabilirlik yüzdesi veya belirtilen API işlemi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Çıkış|Yes|Çıkış|Bayt|Toplam|Bayt cinsinden çıkış verisi miktarı. Bu sayı, dış istemciden Azure Depolama'ya çıkan ve Azure içinde çıkan verileri içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Giriş|Yes|Giriş|Bayt|Toplam|Bayt cinsinden giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|QueueCapacity|Yes|Kuyruk kapasitesi|Bayt|Ortalama|Depolama hesabının Kuyruk hizmeti bayt cinsinden kullandığı depolama miktarı.|Boyut yok|
|QueueCount|Yes|Sıra sayısı|Count|Ortalama|Depolama hesabının Kuyruk hizmeti sıra sayısı.|Boyut yok|
|QueueMessageCount|Yes|Kuyruk Iletisi sayısı|Count|Ortalama|Depolama hesabının Kuyruk hizmeti sıra iletilerinin yaklaşık sayısı.|Boyut yok|
|SuccessE2ELatency|Yes|Başarı E2E Gecikme Süresi|Mayacak|Ortalama|Bir depolama hizmetine veya belirtilen API işlemine yapılan başarılı isteklerin milisaniye cinsinden uçtan uca gecikmesi. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Başarılı Sunucugecikmesi|Yes|Başarı sunucusu gecikmesi|Mayacak|Ortalama|Azure depolama tarafından başarılı bir isteği işlemek için kullanılan gecikme süresi (milisaniye cinsinden). Bu değer, Başarı E2E Gecikme Süresi’nde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulaması|
|İşlemler|Yes|İşlemler|Count|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı yanıt türü sayısı için ResponseType boyutunu kullanın.|Yanıt türü, coğrafi tür, ApiName, kimlik doğrulama|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik|Yes|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmeti için kullanılabilirlik yüzdesi veya belirtilen API işlemi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Çıkış|Yes|Çıkış|Bayt|Toplam|Bayt cinsinden çıkış verisi miktarı. Bu sayı, dış istemciden Azure Depolama'ya çıkan ve Azure içinde çıkan verileri içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Giriş|Yes|Giriş|Bayt|Toplam|Bayt cinsinden giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|SuccessE2ELatency|Yes|Başarı E2E Gecikme Süresi|Mayacak|Ortalama|Bir depolama hizmetine veya belirtilen API işlemine yapılan başarılı isteklerin milisaniye cinsinden uçtan uca gecikmesi. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Başarılı Sunucugecikmesi|Yes|Başarı sunucusu gecikmesi|Mayacak|Ortalama|Azure depolama tarafından başarılı bir isteği işlemek için kullanılan gecikme süresi (milisaniye cinsinden). Bu değer, Başarı E2E Gecikme Süresi’nde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Tablokapasitesi|Yes|Tablo kapasitesi|Bayt|Ortalama|Bayt olarak depolama hesabının tablo hizmeti tarafından kullanılan depolama miktarı.|Boyut yok|
|Tablosayısı|Yes|Tablo sayısı|Count|Ortalama|Depolama hesabının tablo hizmetindeki tablo sayısı.|Boyut yok|
|TableEntityCount|Yes|Tablo varlık sayısı|Count|Ortalama|Depolama hesabının tablo hizmetindeki tablo varlıklarının sayısı.|Boyut yok|
|İşlemler|Yes|İşlemler|Count|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı yanıt türü sayısı için ResponseType boyutunu kullanın.|Yanıt türü, coğrafi tür, ApiName, kimlik doğrulama|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. Biliveservices/hesapları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Blockedçağrılarında|Yes|Engellenen çağrılar|Count|Toplam|Oran veya kota sınırını aşan çağrı sayısı.|ApiName, OperationName, bölge|
|Karakterçeli|Yes|Eğitilen karakterler|Count|Toplam|Eğitilen toplam karakter sayısı.|ApiName, OperationName, bölge|
|Karakter Yabanslamuş|Yes|Çevrilen karakterler|Count|Toplam|Gelen metin isteğindeki toplam karakter sayısı.|ApiName, OperationName, bölge|
|ClientErrors|Yes|İstemci hataları|Count|Toplam|İstemci tarafı hatası olan çağrı sayısı (HTTP yanıt kodu 4xx).|ApiName, OperationName, bölge|
|Dataın|Yes|Içindeki veriler|Bayt|Toplam|Gelen verilerin bayt cinsinden boyutu.|ApiName, OperationName, bölge|
|Veri çıkışı|Yes|Giden veriler|Bayt|Toplam|Giden verilerin bayt cinsinden boyutu.|ApiName, OperationName, bölge|
|Gecikme süresi|Yes|Gecikme süresi|Mayacak|Ortalama|Milisaniye cinsinden gecikme süresi.|ApiName, OperationName, bölge|
|Öğrenin|Yes|Öğrenilmiş olaylar|Count|Toplam|Öğrenilen olay sayısı.|Imatchbaseline, Mode, RunId|
|Matchedreödüller|Yes|Eşleşen Reliksürümleri|Count|Toplam| Eşleşen Reliksayısı.|Imatchbaseline, Mode, RunId|
|ObservedRewards|Yes|Gözlemlenen ödüller|Count|Toplam|Gözlemlenen ödüller sayısı.|Imatchbaseline, Mode, RunId|
|ProcessedCharacters|Yes|İşlenen karakterler|Count|Toplam|Karakter sayısı.|ApiName, FeatureName, UsageChannel, bölge|
|ProcessedTextRecords|Yes|İşlenen metin kayıtları|Count|Toplam|Metin kaydı sayısı.|ApiName, FeatureName, UsageChannel, bölge|
|ServerErrors|Yes|Sunucu hataları|Count|Toplam|Hizmet iç hatası olan çağrı sayısı (HTTP yanıt kodu 5xx).|ApiName, OperationName, bölge|
|SpeechSessionDuration|Yes|Konuşma oturumu süresi|Saniye|Toplam|Konuşma oturumunun saniye cinsinden toplam süresi.|ApiName, OperationName, bölge|
|Başarılı çağrılar|Yes|Başarılı çağrılar|Count|Toplam|Başarılı çağrı sayısı.|ApiName, OperationName, bölge|
|Toplam çağrılar|Yes|Toplam çağrı sayısı|Count|Toplam|Toplam çağrı sayısı.|ApiName, OperationName, bölge|
|Toplam hata sayısı|Yes|Toplam Hata Sayısı|Count|Toplam|Hata yanıtıyla Toplam çağrı sayısı (HTTP yanıt kodu 4xx veya 5xx).|ApiName, OperationName, bölge|
|TotalTokenCalls|Yes|Toplam belirteç çağrısı sayısı|Count|Toplam|Toplam belirteç çağrısı sayısı.|ApiName, OperationName, bölge|
|TotalTransactions|Yes|Toplam Işlem sayısı|Count|Toplam|Toplam işlem sayısı.|Boyut yok|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/CommunicationServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|APIRequestAuthentication|No|Kimlik doğrulama API 'SI Istekleri|Count|Count|Iletişim Hizmetleri kimlik doğrulama uç noktasındaki tüm isteklerin sayısı.|İşlem, StatusCode, StatusCodeClass|
|APIRequestChat|Yes|Sohbet API 'SI Istekleri|Count|Count|Iletişim Hizmetleri sohbet uç noktasındaki tüm isteklerin sayısı.|İşlem, StatusCode, StatusCodeClass|
|APIRequestSMS|Yes|SMS API Istekleri|Count|Count|Iletişim Hizmetleri SMS uç noktasındaki tüm isteklerin sayısı.|İşlem, StatusCode, StatusCodeClass|


## <a name="microsoftcomputecloudservices"></a>Microsoft. COMPUTE/cloudServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Disk okuma bayt sayısı|Yes|Disk okuma bayt sayısı|Bayt|Toplam|İzleme dönemi boyunca diskten okunan bayt sayısı|Roleınstanceıd, RoleID|
|Disk okuma Işlemi/sn|Yes|Disk okuma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk okuma ıOPS 'si|Roleınstanceıd, RoleID|
|Disk yazma baytları|Yes|Disk yazma baytları|Bayt|Toplam|İzleme dönemi boyunca diske yazılan bayt sayısı|Roleınstanceıd, RoleID|
|Disk yazma Işlemi/sn|Yes|Disk yazma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk yazma ıOPS 'si|Roleınstanceıd, RoleID|
|CPU yüzdesi|Yes|CPU yüzdesi|Yüzde|Ortalama|Şu anda sanal makineler tarafından kullanılan ayrılmış işlem birimlerinin yüzdesi|Roleınstanceıd, RoleID|


## <a name="microsoftcomputecloudservicesroles"></a>Microsoft. COMPUTE/cloudServices/rolleri

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Disk okuma bayt sayısı|Yes|Disk okuma bayt sayısı|Bayt|Toplam|İzleme dönemi boyunca diskten okunan bayt sayısı|Roleınstanceıd, RoleID|
|Disk okuma Işlemi/sn|Yes|Disk okuma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk okuma ıOPS 'si|Roleınstanceıd, RoleID|
|Disk yazma baytları|Yes|Disk yazma baytları|Bayt|Toplam|İzleme dönemi boyunca diske yazılan bayt sayısı|Roleınstanceıd, RoleID|
|Disk yazma Işlemi/sn|Yes|Disk yazma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk yazma ıOPS 'si|Roleınstanceıd, RoleID|
|CPU yüzdesi|Yes|CPU yüzdesi|Yüzde|Ortalama|Şu anda sanal makineler tarafından kullanılan ayrılmış işlem birimlerinin yüzdesi|Roleınstanceıd, RoleID|


## <a name="microsoftcomputedisks"></a>Microsoft. COMPUTE/diskler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Bileşik disk okuma bayt/sn|No|Disk okuma bayt/sn (Önizleme)|Bayt|Ortalama|İzleme dönemi boyunca diskten okunan bayt/sn, lütfen bu ölçümün önizlemededir ve genel kullanıma sunulmadan önce değişikliğe tabi olduğunu unutmayın||
|Bileşik disk okuma Işlemi/sn|No|Disk okuma Işlemi/sn (Önizleme)|Bayt|Ortalama|İzleme dönemi boyunca bir diskte gerçekleştirilen okuma IOs sayısı, lütfen bu ölçümün önizlemede olduğunu ve genel kullanıma sunulmadan önce değişikliğe tabi olduğunu unutmayın.||
|Bileşik disk yazma bayt/sn|No|Disk yazma bayt/sn (Önizleme)|Bayt|Ortalama|İzleme dönemi boyunca diske yazılan bayt/sn, lütfen bu ölçümün önizlemededir ve genel kullanıma sunulmadan önce değişikliğe tabi olduğunu unutmayın||
|Bileşik disk yazma Işlemi/sn|No|Disk yazma Işlemi/sn (Önizleme)|Bayt|Ortalama|İzleme dönemi boyunca bir diskte gerçekleştirilen yazma IOs sayısı, lütfen bu ölçümün önizlemededir ve genel kullanıma sunulmadan önce değişikliğe tabi olduğunu unutmayın||


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Tüketilen CPU kredileri|Yes|Tüketilen CPU kredileri|Count|Ortalama|Sanal makine tarafından tüketilen toplam kredi sayısı. Yalnızca B serisi yazılabilir VM 'lerde kullanılabilir|Boyut yok|
|Kalan CPU kredileri|Yes|Kalan CPU kredileri|Count|Ortalama|Patlama için kullanılabilen toplam kredi sayısı. Yalnızca B serisi yazılabilir VM 'lerde kullanılabilir|Boyut yok|
|Veri diski tüketilen bant genişliği yüzdesi|Yes|Veri diski tüketilen bant genişliği yüzdesi|Yüzde|Ortalama|Dakika başına tüketilen veri diski bant genişliği yüzdesi|'YI|
|Veri diski ıOPS tüketilen yüzde|Yes|Veri diski ıOPS tüketilen yüzde|Yüzde|Ortalama|Dakika başına tüketilen veri diski g/ç yüzdesi|'YI|
|Veri diski en fazla patlama bant genişliği|Yes|Veri diski en fazla patlama bant genişliği|Count|Ortalama|Saniyede en fazla aktarım hızı veri diski elde edebilir|'YI|
|Veri diski maksimum veri bloğu ıOPS|Yes|Veri diski maksimum veri bloğu ıOPS|Count|Ortalama|En fazla ıOPS veri diski, patlama ile elde edilebilir|'YI|
|Veri Diski Kuyruk Derinliği|Yes|Veri Diski Kuyruk Derinliği|Count|Ortalama|Veri diski sıra derinliği (veya sıra uzunluğu)|'YI|
|Veri diski okuma bayt/sn|Yes|Veri diski okuma bayt/sn|BytesPerSecond|Ortalama|İzleme döneminde tek bir diskten okunan bayt/sn|'YI|
|Veri diski okuma Işlemi/sn|Yes|Veri diski okuma Işlemi/sn|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diskten ıOPS 'yi okuma|'YI|
|Veri diski hedef bant genişliği|Yes|Veri diski hedef bant genişliği|Count|Ortalama|Saniyedeki temel bayt/saniye hızında veri diski elde etmeden elde edilebilir|'YI|
|Veri diski hedef ıOPS 'si|Yes|Veri diski hedef ıOPS 'si|Count|Ortalama|Temel ıOPS veri diski, tekrar elde etmeden elde edilebilir|'YI|
|Veri diski kullanılan patlama BPS kredileri yüzdesi|Yes|Veri diski kullanılan patlama BPS kredileri yüzdesi|Yüzde|Ortalama|Şimdiye kadar kullanılan veri diski veri bloğu bant genişliği kredilerinin yüzdesi|'YI|
|Veri diski kullanılan patlama GÇ kredileri yüzdesi|Yes|Veri diski kullanılan patlama GÇ kredileri yüzdesi|Yüzde|Ortalama|Şimdiye kadar kullanılan veri diski veri bloğu g/ç kredilerinin yüzdesi|'YI|
|Veri diski yazma bayt/sn|Yes|Veri diski yazma bayt/sn|BytesPerSecond|Ortalama|İzleme döneminde tek bir diske yazılan bayt/sn|'YI|
|Veri diski yazma Işlemi/sn|Yes|Veri diski yazma Işlemi/sn|Sayaçpersaniye|Ortalama|İzleme dönemi boyunca tek bir diskten ıOPS yazma|'YI|
|Disk okuma bayt sayısı|Yes|Disk okuma bayt sayısı|Bayt|Toplam|İzleme dönemi boyunca diskten okunan bayt sayısı|Boyut yok|
|Disk okuma Işlemi/sn|Yes|Disk okuma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk okuma ıOPS 'si|Boyut yok|
|Disk yazma baytları|Yes|Disk yazma baytları|Bayt|Toplam|İzleme dönemi boyunca diske yazılan bayt sayısı|Boyut yok|
|Disk yazma Işlemi/sn|Yes|Disk yazma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk yazma ıOPS 'si|Boyut yok|
|Gelen Akışlar|Yes|Gelen Akışlar|Count|Ortalama|Gelen akışlar, gelen yöndeki geçerli akışların sayısıdır (VM 'ye giden trafik)|Boyut yok|
|Gelen akışlar en yüksek oluşturma oranı|Yes|Gelen akışlar en yüksek oluşturma oranı|Sayaçpersaniye|Ortalama|Gelen akışların en yüksek oluşturma oranı (VM 'ye giden trafik)|Boyut yok|
|Ağ Girişi|Yes|Faturalanabilir (kullanım dışı) ağ|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde alınan faturalandırılabilir bayt sayısı (gelen trafik) (kullanım dışı)|Boyut yok|
|Toplam ağ|Yes|Toplam ağ|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde alınan bayt sayısı (gelen trafik)|Boyut yok|
|Ağ Çıkışı|Yes|Ağdan çıkış faturalandırılabilir (kullanım dışı)|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde faturalandırılabilir bayt sayısı (giden trafik) (kullanım dışı)|Boyut yok|
|Toplam ağ çıkışı|Yes|Toplam ağ çıkışı|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde giden bayt sayısı (giden trafik)|Boyut yok|
|İşletim sistemi diski kullanılan bant genişliği yüzdesi|Yes|İşletim sistemi diski kullanılan bant genişliği yüzdesi|Yüzde|Ortalama|Dakika başına tüketilen işletim sistemi disk bant genişliği yüzdesi|'YI|
|İşletim sistemi diski ıOPS tüketilen yüzde|Yes|İşletim sistemi diski ıOPS tüketilen yüzde|Yüzde|Ortalama|Dakikada tüketilen işletim sistemi diski g/ç yüzdesi|'YI|
|İşletim sistemi diski en fazla patlama bant genişliği|Yes|İşletim sistemi diski en fazla patlama bant genişliği|Count|Ortalama|Saniye başına en fazla aktarım hızı işletim sistemi diski, patlama ile elde edilebilir|'YI|
|İşletim sistemi diski maksimum patlama ıOPS|Yes|İşletim sistemi diski maksimum patlama ıOPS|Count|Ortalama|En fazla ıOPS işletim sistemi diski, patlama ile elde edilebilir|'YI|
|İşletim Sistemi Diski Kuyruk Derinliği|Yes|İşletim Sistemi Diski Kuyruk Derinliği|Count|Ortalama|İşletim sistemi diski sıra derinliği (veya sıra uzunluğu)|Boyut yok|
|İşletim sistemi diski okuma bayt/sn|Yes|İşletim sistemi diski okuma bayt/sn|BytesPerSecond|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten okunan bayt/sn|Boyut yok|
|İşletim sistemi diski okuma Işlemi/sn|Yes|İşletim sistemi diski okuma Işlemi/sn|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten ıOPS 'yi okuyun|Boyut yok|
|İşletim sistemi diski hedef bant genişliği|Yes|İşletim sistemi diski hedef bant genişliği|Count|Ortalama|Saniye başına üretilen iş işletim sistemi diski, ani işlem olmadan elde edilebilir|'YI|
|İşletim sistemi diski hedef ıOPS 'si|Yes|İşletim sistemi diski hedef ıOPS 'si|Count|Ortalama|Taban çizgisi ıOPS işletim sistemi diski, burtasız elde edilebilir|'YI|
|İşletim sistemi diski kullanılan patlama BPS kredileri yüzdesi|Yes|İşletim sistemi diski kullanılan patlama BPS kredileri yüzdesi|Yüzde|Ortalama|Şimdiye kadar kullanılan işletim sistemi disk veri bloğu bant genişliği kredilerinin yüzdesi|'YI|
|İşletim sistemi diski kullanılan patlama GÇ kredileri yüzdesi|Yes|İşletim sistemi diski kullanılan patlama GÇ kredileri yüzdesi|Yüzde|Ortalama|Şimdiye kadar kullanılan işletim sistemi disk veri bloğu g/ç kredilerinin yüzdesi|'YI|
|İşletim sistemi diski yazma bayt/sn|Yes|İşletim sistemi diski yazma bayt/sn|BytesPerSecond|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diske yazılan bayt/sn|Boyut yok|
|İşletim sistemi diski yazma Işlemi/sn|Yes|İşletim sistemi diski yazma Işlemi/sn|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten ıOPS yaz|Boyut yok|
|Giden akışlar|Yes|Giden akışlar|Count|Ortalama|Giden akışlar giden yöndeki geçerli akış sayısıdır (VM 'den giden trafik)|Boyut yok|
|Giden akış maksimum oluşturma oranı|Yes|Giden akış maksimum oluşturma oranı|Sayaçpersaniye|Ortalama|Giden akışların en yüksek oluşturma oranı (VM 'den giden trafik)|Boyut yok|
|CPU yüzdesi|Yes|CPU yüzdesi|Yüzde|Ortalama|Şu anda sanal makineler tarafından kullanılan ayrılmış işlem birimlerinin yüzdesi|Boyut yok|
|Premium veri diski önbelleği okuma Isabeti|Yes|Premium veri diski önbelleği okuma Isabeti|Yüzde|Ortalama|Premium veri diski önbelleği okuma Isabeti|'YI|
|Premium veri diski önbelleği okuma Isabetsizliği|Yes|Premium veri diski önbelleği okuma Isabetsizliği|Yüzde|Ortalama|Premium veri diski önbelleği okuma Isabetsizliği|'YI|
|Premium işletim sistemi disk önbelleği okuma Isabeti|Yes|Premium işletim sistemi disk önbelleği okuma Isabeti|Yüzde|Ortalama|Premium işletim sistemi disk önbelleği okuma Isabeti|Boyut yok|
|Premium işletim sistemi disk önbelleği okuma Isabetsizlik|Yes|Premium işletim sistemi disk önbelleği okuma Isabetsizlik|Yüzde|Ortalama|Premium işletim sistemi disk önbelleği okuma Isabetsizlik|Boyut yok|
|VM önbelleğe alınan bant genişliği yüzdesi|Yes|VM önbelleğe alınan bant genişliği yüzdesi|Yüzde|Ortalama|VM tarafından tüketilen önbelleğe alınmış disk bant genişliği yüzdesi|Boyut yok|
|VM önbelleğe alınan ıOPS yüzdesi|Yes|VM önbelleğe alınan ıOPS yüzdesi|Yüzde|Ortalama|VM tarafından tüketilen önbelleğe alınmış disk ıOPS yüzdesi|Boyut yok|
|VM önbelleğe alınmamış bant genişliği yüzdesi|Yes|VM önbelleğe alınmamış bant genişliği yüzdesi|Yüzde|Ortalama|VM tarafından tüketilen önbelleğe alınmamış disk bant genişliğinin yüzdesi|Boyut yok|
|VM önbelleğe alınmamış ıOPS yüzde yüzdesi|Yes|VM önbelleğe alınmamış ıOPS yüzde yüzdesi|Yüzde|Ortalama|VM tarafından tüketilen önbelleğe alınmamış disk ıOPS yüzdesi|Boyut yok|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Tüketilen CPU kredileri|Yes|Tüketilen CPU kredileri|Count|Ortalama|Sanal makine tarafından tüketilen toplam kredi sayısı. Yalnızca B serisi yazılabilir VM 'lerde kullanılabilir|Boyut yok|
|Kalan CPU kredileri|Yes|Kalan CPU kredileri|Count|Ortalama|Patlama için kullanılabilen toplam kredi sayısı. Yalnızca B serisi yazılabilir VM 'lerde kullanılabilir|Boyut yok|
|Veri diski tüketilen bant genişliği yüzdesi|Yes|Veri diski tüketilen bant genişliği yüzdesi|Yüzde|Ortalama|Dakika başına tüketilen veri diski bant genişliği yüzdesi|LUN, VMName|
|Veri diski ıOPS tüketilen yüzde|Yes|Veri diski ıOPS tüketilen yüzde|Yüzde|Ortalama|Dakika başına tüketilen veri diski g/ç yüzdesi|LUN, VMName|
|Veri diski en fazla patlama bant genişliği|Yes|Veri diski en fazla patlama bant genişliği|Count|Ortalama|Saniyede en fazla aktarım hızı veri diski elde edebilir|LUN, VMName|
|Veri diski maksimum veri bloğu ıOPS|Yes|Veri diski maksimum veri bloğu ıOPS|Count|Ortalama|En fazla ıOPS veri diski, patlama ile elde edilebilir|LUN, VMName|
|Veri Diski Kuyruk Derinliği|Yes|Veri Diski Kuyruk Derinliği|Count|Ortalama|Veri diski sıra derinliği (veya sıra uzunluğu)|LUN, VMName|
|Veri diski okuma bayt/sn|Yes|Veri diski okuma bayt/sn|BytesPerSecond|Ortalama|İzleme döneminde tek bir diskten okunan bayt/sn|LUN, VMName|
|Veri diski okuma Işlemi/sn|Yes|Veri diski okuma Işlemi/sn|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diskten ıOPS 'yi okuma|LUN, VMName|
|Veri diski hedef bant genişliği|Yes|Veri diski hedef bant genişliği|Count|Ortalama|Saniyedeki temel bayt/saniye hızında veri diski elde etmeden elde edilebilir|LUN, VMName|
|Veri diski hedef ıOPS 'si|Yes|Veri diski hedef ıOPS 'si|Count|Ortalama|Temel ıOPS veri diski, tekrar elde etmeden elde edilebilir|LUN, VMName|
|Veri diski kullanılan patlama BPS kredileri yüzdesi|Yes|Veri diski kullanılan patlama BPS kredileri yüzdesi|Yüzde|Ortalama|Şimdiye kadar kullanılan veri diski veri bloğu bant genişliği kredilerinin yüzdesi|LUN, VMName|
|Veri diski kullanılan patlama GÇ kredileri yüzdesi|Yes|Veri diski kullanılan patlama GÇ kredileri yüzdesi|Yüzde|Ortalama|Şimdiye kadar kullanılan veri diski veri bloğu g/ç kredilerinin yüzdesi|LUN, VMName|
|Veri diski yazma bayt/sn|Yes|Veri diski yazma bayt/sn|BytesPerSecond|Ortalama|İzleme döneminde tek bir diske yazılan bayt/sn|LUN, VMName|
|Veri diski yazma Işlemi/sn|Yes|Veri diski yazma Işlemi/sn|Sayaçpersaniye|Ortalama|İzleme dönemi boyunca tek bir diskten ıOPS yazma|LUN, VMName|
|Disk okuma bayt sayısı|Yes|Disk okuma bayt sayısı|Bayt|Toplam|İzleme dönemi boyunca diskten okunan bayt sayısı|VMName|
|Disk okuma Işlemi/sn|Yes|Disk okuma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk okuma ıOPS 'si|VMName|
|Disk yazma baytları|Yes|Disk yazma baytları|Bayt|Toplam|İzleme dönemi boyunca diske yazılan bayt sayısı|VMName|
|Disk yazma Işlemi/sn|Yes|Disk yazma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk yazma ıOPS 'si|VMName|
|Gelen Akışlar|Yes|Gelen Akışlar|Count|Ortalama|Gelen akışlar, gelen yöndeki geçerli akışların sayısıdır (VM 'ye giden trafik)|VMName|
|Gelen akışlar en yüksek oluşturma oranı|Yes|Gelen akışlar en yüksek oluşturma oranı|Sayaçpersaniye|Ortalama|Gelen akışların en yüksek oluşturma oranı (VM 'ye giden trafik)|VMName|
|Ağ Girişi|Yes|Faturalanabilir (kullanım dışı) ağ|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde alınan faturalandırılabilir bayt sayısı (gelen trafik) (kullanım dışı)|VMName|
|Toplam ağ|Yes|Toplam ağ|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde alınan bayt sayısı (gelen trafik)|VMName|
|Ağ Çıkışı|Yes|Ağdan çıkış faturalandırılabilir (kullanım dışı)|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde faturalandırılabilir bayt sayısı (giden trafik) (kullanım dışı)|VMName|
|Toplam ağ çıkışı|Yes|Toplam ağ çıkışı|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde giden bayt sayısı (giden trafik)|VMName|
|İşletim sistemi diski kullanılan bant genişliği yüzdesi|Yes|İşletim sistemi diski kullanılan bant genişliği yüzdesi|Yüzde|Ortalama|Dakika başına tüketilen işletim sistemi disk bant genişliği yüzdesi|LUN, VMName|
|İşletim sistemi diski ıOPS tüketilen yüzde|Yes|İşletim sistemi diski ıOPS tüketilen yüzde|Yüzde|Ortalama|Dakikada tüketilen işletim sistemi diski g/ç yüzdesi|LUN, VMName|
|İşletim sistemi diski en fazla patlama bant genişliği|Yes|İşletim sistemi diski en fazla patlama bant genişliği|Count|Ortalama|Saniye başına en fazla aktarım hızı işletim sistemi diski, patlama ile elde edilebilir|LUN, VMName|
|İşletim sistemi diski maksimum patlama ıOPS|Yes|İşletim sistemi diski maksimum patlama ıOPS|Count|Ortalama|En fazla ıOPS işletim sistemi diski, patlama ile elde edilebilir|LUN, VMName|
|İşletim Sistemi Diski Kuyruk Derinliği|Yes|İşletim Sistemi Diski Kuyruk Derinliği|Count|Ortalama|İşletim sistemi diski sıra derinliği (veya sıra uzunluğu)|VMName|
|İşletim sistemi diski okuma bayt/sn|Yes|İşletim sistemi diski okuma bayt/sn|BytesPerSecond|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten okunan bayt/sn|VMName|
|İşletim sistemi diski okuma Işlemi/sn|Yes|İşletim sistemi diski okuma Işlemi/sn|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten ıOPS 'yi okuyun|VMName|
|İşletim sistemi diski hedef bant genişliği|Yes|İşletim sistemi diski hedef bant genişliği|Count|Ortalama|Saniye başına üretilen iş işletim sistemi diski, ani işlem olmadan elde edilebilir|LUN, VMName|
|İşletim sistemi diski hedef ıOPS 'si|Yes|İşletim sistemi diski hedef ıOPS 'si|Count|Ortalama|Taban çizgisi ıOPS işletim sistemi diski, burtasız elde edilebilir|LUN, VMName|
|İşletim sistemi diski kullanılan patlama BPS kredileri yüzdesi|Yes|İşletim sistemi diski kullanılan patlama BPS kredileri yüzdesi|Yüzde|Ortalama|Şimdiye kadar kullanılan işletim sistemi disk veri bloğu bant genişliği kredilerinin yüzdesi|LUN, VMName|
|İşletim sistemi diski kullanılan patlama GÇ kredileri yüzdesi|Yes|İşletim sistemi diski kullanılan patlama GÇ kredileri yüzdesi|Yüzde|Ortalama|Şimdiye kadar kullanılan işletim sistemi disk veri bloğu g/ç kredilerinin yüzdesi|LUN, VMName|
|İşletim sistemi diski yazma bayt/sn|Yes|İşletim sistemi diski yazma bayt/sn|BytesPerSecond|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diske yazılan bayt/sn|VMName|
|İşletim sistemi diski yazma Işlemi/sn|Yes|İşletim sistemi diski yazma Işlemi/sn|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten ıOPS yaz|VMName|
|Giden akışlar|Yes|Giden akışlar|Count|Ortalama|Giden akışlar giden yöndeki geçerli akış sayısıdır (VM 'den giden trafik)|VMName|
|Giden akış maksimum oluşturma oranı|Yes|Giden akış maksimum oluşturma oranı|Sayaçpersaniye|Ortalama|Giden akışların en yüksek oluşturma oranı (VM 'den giden trafik)|VMName|
|CPU yüzdesi|Yes|CPU yüzdesi|Yüzde|Ortalama|Şu anda sanal makineler tarafından kullanılan ayrılmış işlem birimlerinin yüzdesi|VMName|
|Premium veri diski önbelleği okuma Isabeti|Yes|Premium veri diski önbelleği okuma Isabeti|Yüzde|Ortalama|Premium veri diski önbelleği okuma Isabeti|LUN, VMName|
|Premium veri diski önbelleği okuma Isabetsizliği|Yes|Premium veri diski önbelleği okuma Isabetsizliği|Yüzde|Ortalama|Premium veri diski önbelleği okuma Isabetsizliği|LUN, VMName|
|Premium işletim sistemi disk önbelleği okuma Isabeti|Yes|Premium işletim sistemi disk önbelleği okuma Isabeti|Yüzde|Ortalama|Premium işletim sistemi disk önbelleği okuma Isabeti|VMName|
|Premium işletim sistemi disk önbelleği okuma Isabetsizlik|Yes|Premium işletim sistemi disk önbelleği okuma Isabetsizlik|Yüzde|Ortalama|Premium işletim sistemi disk önbelleği okuma Isabetsizlik|VMName|
|VM önbelleğe alınan bant genişliği yüzdesi|Yes|VM önbelleğe alınan bant genişliği yüzdesi|Yüzde|Ortalama|VM tarafından tüketilen önbelleğe alınmış disk bant genişliği yüzdesi|VMName|
|VM önbelleğe alınan ıOPS yüzdesi|Yes|VM önbelleğe alınan ıOPS yüzdesi|Yüzde|Ortalama|VM tarafından tüketilen önbelleğe alınmış disk ıOPS yüzdesi|VMName|
|VM önbelleğe alınmamış bant genişliği yüzdesi|Yes|VM önbelleğe alınmamış bant genişliği yüzdesi|Yüzde|Ortalama|VM tarafından tüketilen önbelleğe alınmamış disk bant genişliğinin yüzdesi|VMName|
|VM önbelleğe alınmamış ıOPS yüzde yüzdesi|Yes|VM önbelleğe alınmamış ıOPS yüzde yüzdesi|Yüzde|Ortalama|VM tarafından tüketilen önbelleğe alınmamış disk ıOPS yüzdesi|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Tüketilen CPU kredileri|Yes|Tüketilen CPU kredileri|Count|Ortalama|Sanal makine tarafından tüketilen toplam kredi sayısı. Yalnızca B serisi yazılabilir VM 'lerde kullanılabilir|Boyut yok|
|Kalan CPU kredileri|Yes|Kalan CPU kredileri|Count|Ortalama|Patlama için kullanılabilen toplam kredi sayısı. Yalnızca B serisi yazılabilir VM 'lerde kullanılabilir|Boyut yok|
|Veri diski tüketilen bant genişliği yüzdesi|Yes|Veri diski tüketilen bant genişliği yüzdesi|Yüzde|Ortalama|Dakika başına tüketilen veri diski bant genişliği yüzdesi|'YI|
|Veri diski ıOPS tüketilen yüzde|Yes|Veri diski ıOPS tüketilen yüzde|Yüzde|Ortalama|Dakika başına tüketilen veri diski g/ç yüzdesi|'YI|
|Veri diski en fazla patlama bant genişliği|Yes|Veri diski en fazla patlama bant genişliği|Count|Ortalama|Saniyede en fazla aktarım hızı veri diski elde edebilir|'YI|
|Veri diski maksimum veri bloğu ıOPS|Yes|Veri diski maksimum veri bloğu ıOPS|Count|Ortalama|En fazla ıOPS veri diski, patlama ile elde edilebilir|'YI|
|Veri Diski Kuyruk Derinliği|Yes|Veri Diski Kuyruk Derinliği|Count|Ortalama|Veri diski sıra derinliği (veya sıra uzunluğu)|'YI|
|Veri diski okuma bayt/sn|Yes|Veri diski okuma bayt/sn|BytesPerSecond|Ortalama|İzleme döneminde tek bir diskten okunan bayt/sn|'YI|
|Veri diski okuma Işlemi/sn|Yes|Veri diski okuma Işlemi/sn|Sayaçpersaniye|Ortalama|İzleme döneminde tek bir diskten ıOPS 'yi okuma|'YI|
|Veri diski hedef bant genişliği|Yes|Veri diski hedef bant genişliği|Count|Ortalama|Saniyedeki temel bayt/saniye hızında veri diski elde etmeden elde edilebilir|'YI|
|Veri diski hedef ıOPS 'si|Yes|Veri diski hedef ıOPS 'si|Count|Ortalama|Temel ıOPS veri diski, tekrar elde etmeden elde edilebilir|'YI|
|Veri diski kullanılan patlama BPS kredileri yüzdesi|Yes|Veri diski kullanılan patlama BPS kredileri yüzdesi|Yüzde|Ortalama|Şimdiye kadar kullanılan veri diski veri bloğu bant genişliği kredilerinin yüzdesi|'YI|
|Veri diski kullanılan patlama GÇ kredileri yüzdesi|Yes|Veri diski kullanılan patlama GÇ kredileri yüzdesi|Yüzde|Ortalama|Şimdiye kadar kullanılan veri diski veri bloğu g/ç kredilerinin yüzdesi|'YI|
|Veri diski yazma bayt/sn|Yes|Veri diski yazma bayt/sn|BytesPerSecond|Ortalama|İzleme döneminde tek bir diske yazılan bayt/sn|'YI|
|Veri diski yazma Işlemi/sn|Yes|Veri diski yazma Işlemi/sn|Sayaçpersaniye|Ortalama|İzleme dönemi boyunca tek bir diskten ıOPS yazma|'YI|
|Disk okuma bayt sayısı|Yes|Disk okuma bayt sayısı|Bayt|Toplam|İzleme dönemi boyunca diskten okunan bayt sayısı|Boyut yok|
|Disk okuma Işlemi/sn|Yes|Disk okuma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk okuma ıOPS 'si|Boyut yok|
|Disk yazma baytları|Yes|Disk yazma baytları|Bayt|Toplam|İzleme dönemi boyunca diske yazılan bayt sayısı|Boyut yok|
|Disk yazma Işlemi/sn|Yes|Disk yazma Işlemi/sn|Sayaçpersaniye|Ortalama|Disk yazma ıOPS 'si|Boyut yok|
|Gelen Akışlar|Yes|Gelen Akışlar|Count|Ortalama|Gelen akışlar, gelen yöndeki geçerli akışların sayısıdır (VM 'ye giden trafik)|Boyut yok|
|Gelen akışlar en yüksek oluşturma oranı|Yes|Gelen akışlar en yüksek oluşturma oranı|Sayaçpersaniye|Ortalama|Gelen akışların en yüksek oluşturma oranı (VM 'ye giden trafik)|Boyut yok|
|Ağ Girişi|Yes|Faturalanabilir (kullanım dışı) ağ|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde alınan faturalandırılabilir bayt sayısı (gelen trafik) (kullanım dışı)|Boyut yok|
|Toplam ağ|Yes|Toplam ağ|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde alınan bayt sayısı (gelen trafik)|Boyut yok|
|Ağ Çıkışı|Yes|Ağdan çıkış faturalandırılabilir (kullanım dışı)|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde faturalandırılabilir bayt sayısı (giden trafik) (kullanım dışı)|Boyut yok|
|Toplam ağ çıkışı|Yes|Toplam ağ çıkışı|Bayt|Toplam|Sanal makineler tarafından tüm ağ arabirimlerinde giden bayt sayısı (giden trafik)|Boyut yok|
|İşletim sistemi diski kullanılan bant genişliği yüzdesi|Yes|İşletim sistemi diski kullanılan bant genişliği yüzdesi|Yüzde|Ortalama|Dakika başına tüketilen işletim sistemi disk bant genişliği yüzdesi|'YI|
|İşletim sistemi diski ıOPS tüketilen yüzde|Yes|İşletim sistemi diski ıOPS tüketilen yüzde|Yüzde|Ortalama|Dakikada tüketilen işletim sistemi diski g/ç yüzdesi|'YI|
|İşletim sistemi diski en fazla patlama bant genişliği|Yes|İşletim sistemi diski en fazla patlama bant genişliği|Count|Ortalama|Saniye başına en fazla aktarım hızı işletim sistemi diski, patlama ile elde edilebilir|'YI|
|İşletim sistemi diski maksimum patlama ıOPS|Yes|İşletim sistemi diski maksimum patlama ıOPS|Count|Ortalama|En fazla ıOPS işletim sistemi diski, patlama ile elde edilebilir|'YI|
|İşletim Sistemi Diski Kuyruk Derinliği|Yes|İşletim Sistemi Diski Kuyruk Derinliği|Count|Ortalama|İşletim sistemi diski sıra derinliği (veya sıra uzunluğu)|Boyut yok|
|İşletim sistemi diski okuma bayt/sn|Yes|İşletim sistemi diski okuma bayt/sn|BytesPerSecond|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten okunan bayt/sn|Boyut yok|
|İşletim sistemi diski okuma Işlemi/sn|Yes|İşletim sistemi diski okuma Işlemi/sn|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten ıOPS 'yi okuyun|Boyut yok|
|İşletim sistemi diski hedef bant genişliği|Yes|İşletim sistemi diski hedef bant genişliği|Count|Ortalama|Saniye başına üretilen iş işletim sistemi diski, ani işlem olmadan elde edilebilir|'YI|
|İşletim sistemi diski hedef ıOPS 'si|Yes|İşletim sistemi diski hedef ıOPS 'si|Count|Ortalama|Taban çizgisi ıOPS işletim sistemi diski, burtasız elde edilebilir|'YI|
|İşletim sistemi diski kullanılan patlama BPS kredileri yüzdesi|Yes|İşletim sistemi diski kullanılan patlama BPS kredileri yüzdesi|Yüzde|Ortalama|Şimdiye kadar kullanılan işletim sistemi disk veri bloğu bant genişliği kredilerinin yüzdesi|'YI|
|İşletim sistemi diski kullanılan patlama GÇ kredileri yüzdesi|Yes|İşletim sistemi diski kullanılan patlama GÇ kredileri yüzdesi|Yüzde|Ortalama|Şimdiye kadar kullanılan işletim sistemi disk veri bloğu g/ç kredilerinin yüzdesi|'YI|
|İşletim sistemi diski yazma bayt/sn|Yes|İşletim sistemi diski yazma bayt/sn|BytesPerSecond|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diske yazılan bayt/sn|Boyut yok|
|İşletim sistemi diski yazma Işlemi/sn|Yes|İşletim sistemi diski yazma Işlemi/sn|Sayaçpersaniye|Ortalama|İşletim sistemi diski için izleme döneminde tek bir diskten ıOPS yaz|Boyut yok|
|Giden akışlar|Yes|Giden akışlar|Count|Ortalama|Giden akışlar giden yöndeki geçerli akış sayısıdır (VM 'den giden trafik)|Boyut yok|
|Giden akış maksimum oluşturma oranı|Yes|Giden akış maksimum oluşturma oranı|Sayaçpersaniye|Ortalama|Giden akışların en yüksek oluşturma oranı (VM 'den giden trafik)|Boyut yok|
|CPU yüzdesi|Yes|CPU yüzdesi|Yüzde|Ortalama|Şu anda sanal makineler tarafından kullanılan ayrılmış işlem birimlerinin yüzdesi|Boyut yok|
|Premium veri diski önbelleği okuma Isabeti|Yes|Premium veri diski önbelleği okuma Isabeti|Yüzde|Ortalama|Premium veri diski önbelleği okuma Isabeti|'YI|
|Premium veri diski önbelleği okuma Isabetsizliği|Yes|Premium veri diski önbelleği okuma Isabetsizliği|Yüzde|Ortalama|Premium veri diski önbelleği okuma Isabetsizliği|'YI|
|Premium işletim sistemi disk önbelleği okuma Isabeti|Yes|Premium işletim sistemi disk önbelleği okuma Isabeti|Yüzde|Ortalama|Premium işletim sistemi disk önbelleği okuma Isabeti|Boyut yok|
|Premium işletim sistemi disk önbelleği okuma Isabetsizlik|Yes|Premium işletim sistemi disk önbelleği okuma Isabetsizlik|Yüzde|Ortalama|Premium işletim sistemi disk önbelleği okuma Isabetsizlik|Boyut yok|
|VM önbelleğe alınan bant genişliği yüzdesi|Yes|VM önbelleğe alınan bant genişliği yüzdesi|Yüzde|Ortalama|VM tarafından tüketilen önbelleğe alınmış disk bant genişliği yüzdesi|Boyut yok|
|VM önbelleğe alınan ıOPS yüzdesi|Yes|VM önbelleğe alınan ıOPS yüzdesi|Yüzde|Ortalama|VM tarafından tüketilen önbelleğe alınmış disk ıOPS yüzdesi|Boyut yok|
|VM önbelleğe alınmamış bant genişliği yüzdesi|Yes|VM önbelleğe alınmamış bant genişliği yüzdesi|Yüzde|Ortalama|VM tarafından tüketilen önbelleğe alınmamış disk bant genişliğinin yüzdesi|Boyut yok|
|VM önbelleğe alınmamış ıOPS yüzde yüzdesi|Yes|VM önbelleğe alınmamış ıOPS yüzde yüzdesi|Yüzde|Ortalama|VM tarafından tüketilen önbelleğe alınmamış disk ıOPS yüzdesi|Boyut yok|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. Containerınstance/containerGroups

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|CpuUsage|Yes|CPU Kullanımı|Count|Ortalama|Miliçekirdekte tüm çekirdekler üzerinde CPU kullanımı.|Adı|
|MemoryUsage|Yes|Bellek Kullanımı|Bayt|Ortalama|Bayt cinsinden toplam bellek kullanımı.|Adı|
|NetworkBytesReceivedPerSecond|Yes|Saniye başına alınan ağ bayt sayısı|Bayt|Ortalama|Saniye başına alınan ağ bayt sayısı.|Boyut yok|
|NetworkBytesTransmittedPerSecond|Yes|Saniye başına aktarılan ağ baytları|Bayt|Ortalama|Saniye başına aktarılan ağ baytları.|Boyut yok|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/kayıt defterleri

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Yes|AgentPool CPU süresi|Saniye|Toplam|Saniye cinsinden AgentPool CPU süresi|Boyut yok|
|RunDuration|Yes|Çalışma süresi|Mayacak|Toplam|Çalışma süresi (milisaniye)|Boyut yok|
|Başarılı sayı|Yes|Başarılı çekme sayısı|Count|Ortalama|Başarılı görüntü sayısı|Boyut yok|
|Başarılı Pushcount|Yes|Başarılı gönderme sayısı|Count|Ortalama|Başarılı görüntü gönderme sayısı|Boyut yok|
|TotalPullCount|Yes|Toplam çekme sayısı|Count|Ortalama|Toplam görüntü sayısı|Boyut yok|
|Toplam Pushcount|Yes|Toplam gönderme sayısı|Count|Ortalama|Toplam resim gönderim sayısı|Boyut yok|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/Managedkümeler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|apiserver_current_inflight_requests|No|Esnek Istekler|Count|Ortalama|Son saniye içinde istek başına apiserver istek türü üzerinde şu anda en fazla kullanılan esnek istek sayısı|Istek türü|
|cluster_autoscaler_cluster_safe_to_autoscale|No|Küme Durumu|Count|Ortalama|Küme otomatik Scaler 'ın kümede işlem yapıp olmayacağını belirler||
|cluster_autoscaler_scale_down_in_cooldown|No|Ölçeği aşağı genişlet|Count|Ortalama|Ölçeğin aşağı doğru olup olmadığını belirler. bu zaman çerçevesi sırasında hiçbir düğüm kaldırılmaz||
|cluster_autoscaler_unneeded_nodes_count|No|Gereksiz düğümler|Count|Ortalama|Küme auotscaler, bu düğümleri silinmek üzere aday olarak işaretler ve sonunda silinir||
|cluster_autoscaler_unschedulable_pods_count|No|Unschedulable pods|Count|Ortalama|Kümede Şu anda unschedulable olan Pod sayısı||
|kube_node_status_allocatable_cpu_cores|No|Yönetilen kümede toplam kullanılabilir CPU çekirdeği sayısı|Count|Ortalama|Yönetilen kümede toplam kullanılabilir CPU çekirdeği sayısı||
|kube_node_status_allocatable_memory_bytes|No|Yönetilen kümede toplam kullanılabilir bellek miktarı|Bayt|Ortalama|Yönetilen kümede toplam kullanılabilir bellek miktarı||
|kube_node_status_condition|No|Çeşitli düğüm koşullarına yönelik durumlar|Count|Ortalama|Çeşitli düğüm koşullarına yönelik durumlar|koşul, durum, status2, düğüm|
|kube_pod_status_phase|No|Aşamasına göre Pod sayısı|Count|Ortalama|Aşamasına göre Pod sayısı|aşama, ad alanı, Pod|
|kube_pod_status_ready|No|Hazırlık durumundaki Pod sayısı|Count|Ortalama|Hazırlık durumundaki Pod sayısı|ad alanı, Pod, koşul|
|node_cpu_usage_millicores|Yes|CPU kullanımı Miliçekirdekler|Milicore|Ortalama|Küme genelinde miliçekirdekte CPU kullanımının toplu ölçümü|Node, nodepool|
|node_cpu_usage_percentage|Yes|CPU kullanım yüzdesi|Yüzde|Ortalama|Küme genelinde yüzde cinsinden ölçülen ortalama CPU kullanımı|Node, nodepool|
|node_disk_usage_bytes|Yes|Kullanılan disk bayt sayısı|Bayt|Ortalama|Cihaza göre bayt cinsinden kullanılan disk alanı|Node, nodepool, cihaz|
|node_disk_usage_percentage|Yes|Kullanılan disk yüzdesi|Yüzde|Ortalama|Cihaza göre yüzde cinsinden kullanılan disk alanı|Node, nodepool, cihaz|
|node_memory_rss_bytes|Yes|Bellek RSS baytları|Bayt|Ortalama|Bayt cinsinden kullanılan kapsayıcı RSS belleği|Node, nodepool|
|node_memory_rss_percentage|Yes|Bellek RSS yüzdesi|Yüzde|Ortalama|Kullanılan kapsayıcı RSS belleği yüzdesi|Node, nodepool|
|node_memory_working_set_bytes|Yes|Bellek çalışma kümesi baytları|Bayt|Ortalama|Bayt cinsinden kullanılan kapsayıcı çalışma kümesi belleği|Node, nodepool|
|node_memory_working_set_percentage|Yes|Bellek çalışma kümesi yüzdesi|Yüzde|Ortalama|Kapsayıcıda kullanılan kapsayıcı çalışma kümesi belleği yüzdesi|Node, nodepool|
|node_network_in_bytes|Yes|Bayt cinsinden ağ|Bayt|Ortalama|Alınan ağ bayt sayısı|Node, nodepool|
|node_network_out_bytes|Yes|Ağ çıkış baytları|Bayt|Ortalama|Ağ tarafından iletilen baytlar|Node, nodepool|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|FailedRequests|Yes|Başarısız İstekler|Count|Toplam|Özel kaynak sağlayıcıları için kullanılabilir günlükleri alır|HttpMethod, CallPath, StatusCode|
|Başarılı Istek sayısı|Yes|Başarılı İstekler|Count|Toplam|Özel sağlayıcı tarafından yapılan başarılı istekler|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AvailableCapacity|Yes|Kullanılabilir kapasite|Bayt|Ortalama|Raporlama dönemi boyunca kullanılabilir kapasite (bayt cinsinden).|Boyut yok|
|BytesUploadedToCloud|Yes|Karşıya yüklenen bulut baytları (cihaz)|Bayt|Ortalama|Raporlama dönemi boyunca bir cihazdan Azure 'a yüklenen toplam bayt sayısı.|Boyut yok|
|BytesUploadedToCloudPerShare|Yes|Karşıya yüklenen bulut baytları (paylaşma)|Bayt|Ortalama|Raporlama dönemi boyunca bir paylaşımdan Azure 'a yüklenen toplam bayt sayısı.|Paylaş|
|Cloudreadüretilen Iş|Yes|Bulut Indirme performansı|BytesPerSecond|Ortalama|Raporlama döneminde Azure 'a bulut indirme performansı.|Boyut yok|
|CloudReadThroughputPerShare|Yes|Bulut Indirme verimlilik (paylaşma)|BytesPerSecond|Ortalama|Raporlama dönemi boyunca bir paylaşımdan Azure 'a aktarım hızını indirin.|Paylaş|
|Clouduploadüretilen Iş|Yes|Bulut karşıya yükleme performansı|BytesPerSecond|Ortalama|Raporlama dönemi boyunca Azure 'a bulut yükleme performansı.|Boyut yok|
|CloudUploadThroughputPerShare|Yes|Bulut karşıya yükleme üretilen Işi (paylaşma)|BytesPerSecond|Ortalama|Raporlama dönemi boyunca bir paylaşımdan Azure 'a yük aktarımını yükleme.|Paylaş|
|Hipervmemoryutilileştirme|Yes|Edge Işlem-bellek kullanımı|Yüzde|Ortalama|Kullanımdaki RAM miktarı|InstanceName|
|Hypervvirtualprocessorkullanım|Yes|Edge hesaplama-CPU yüzdesi|Yüzde|Ortalama|CPU kullanımı yüzdesi|InstanceName|
|Nicreadüretilen Iş|Yes|Okuma performansı (ağ)|BytesPerSecond|Ortalama|Cihazdaki ağ arabiriminin, ağ geçidinde bulunan tüm birimler için raporlama döneminde okuma performansı.|InstanceName|
|Nicwriteüretilen Iş|Yes|Yazma Işleme (ağ)|BytesPerSecond|Ortalama|Cihazdaki ağ arabiriminin, ağ geçidinde bulunan tüm birimler için raporlama döneminde yazma aktarım hızı.|InstanceName|
|TotalCapacity|Yes|Toplam kapasite|Bayt|Ortalama|Toplam kapasite|Boyut yok|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft. Dataişbirliği/çalışma alanları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|DataAssetCount|Yes|Oluşturulan veri varlıkları|Count|Maksimum|Oluşturulan veri varlıklarının sayısı|DataAssetName|
|PipelineCount|Yes|Oluşturulan işlem hatları|Count|Maksimum|Oluşturulan işlem hattı sayısı|Ardışık Düzen adı|
|ProposalCount|Yes|Oluşturulan teklifler|Count|Maksimum|Oluşturulan tekliflerin sayısı|ProposalName|
|ScriptCount|Yes|Oluşturulan betikler|Count|Maksimum|Oluşturulan betiklerin sayısı|Komut|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/DataFactory

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Failedçalıştırmaları|Yes|Başarısız çalıştırmalar|Count|Toplam||Ardışık düzen ınename, activityName|
|Başarılı çalıştırmalar|Yes|Başarılı çalıştırmalar|Count|Toplam||Ardışık düzen ınename, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/Factory

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Activitycancelledçalıştırmaları|Yes|İptal edilen etkinlik ölçümleri çalıştırıyor|Count|Toplam||ActivityType, ardışık düzen adı, Failuyeniden yazma, ad|
|Activityfailedçalıştırmaları|Yes|Başarısız etkinlik çalıştırma ölçümleri|Count|Toplam||ActivityType, ardışık düzen adı, Failuyeniden yazma, ad|
|ActivitySucceededRuns|Yes|Başarılı etkinlik ölçümleri çalıştırıyor|Count|Toplam||ActivityType, ardışık düzen adı, Failuyeniden yazma, ad|
|Factorysizeingbits|Yes|Toplam fabrika boyutu (GB birimi)|Count|Maksimum||Boyut yok|
|Integrationruntimekullanılabilirliği Blememory|Yes|Tümleştirme çalışma zamanı kullanılabilir belleği|Bayt|Ortalama||Integrationruntimename, düğ,|
|IntegrationRuntimeAvailableNodeNumber|Yes|Tümleştirme çalışma zamanı kullanılabilir düğüm sayısı|Count|Ortalama||Integrationruntimename|
|Integrationruntimeaveragetaskpickupdelay|Yes|Tümleştirme çalışma zamanı sıra süresi|Saniye|Ortalama||Integrationruntimename|
|Integrationruntimecpuyüzdesi|Yes|Tümleştirme çalışma zamanı CPU kullanımı|Yüzde|Ortalama||Integrationruntimename, düğ,|
|Integrationruntimequeuelength|Yes|Tümleştirme çalışma zamanı sıra uzunluğu|Count|Ortalama||Integrationruntimename|
|Maxallowedfactorysizeingbits|Yes|İzin verilen en yüksek fabrika boyutu (GB birimi)|Count|Maksimum||Boyut yok|
|MaxAllowedResourceCount|Yes|İzin verilen en fazla varlık sayısı|Count|Maksimum||Boyut yok|
|Ardışık düzen ınecancelledçalıştırmaları|Yes|İptal edilen işlem hattı çalışma ölçümleri|Count|Toplam||Failuıyeniden yazma, ad|
|PipelineElapsedTimeRuns|Yes|Geçen zaman ardışık düzeni ölçümleri çalıştırır|Count|Toplam||RunId, Name|
|Pipelinefailedçalıştırmaları|Yes|Başarısız işlem hattı çalıştırmaları ölçümleri|Count|Toplam||Failuıyeniden yazma, ad|
|Ardışık düzen Inesucceededçalıştırmaları|Yes|Başarılı işlem hattı çalışma ölçümleri|Count|Toplam||Failuıyeniden yazma, ad|
|ResourceCount|Yes|Toplam varlık sayısı|Count|Maksimum||Boyut yok|
|Ssisıntegrationruntimestartcancel|Yes|İptal edilen SSIS Integration Runtime başlangıç ölçümleri|Count|Toplam||Integrationruntimename|
|Ssisıntegrationruntimestartfailed|Yes|Başarısız SSIS Integration Runtime başlangıç ölçümleri|Count|Toplam||Integrationruntimename|
|Ssisıntegrationruntimestartsucceeded|Yes|Başarılı SSIS Integration Runtime başlangıç ölçümleri|Count|Toplam||Integrationruntimename|
|Ssisıntegrationruntimestoptakılmış|Yes|Takılmış SSIS tümleştirme çalışma zamanı ölçümleri durdur|Count|Toplam||Integrationruntimename|
|Ssisıntegrationruntimestopsucceeded|Yes|Başarılı SSIS tümleştirme çalışma zamanı ölçümleri durdur|Count|Toplam||Integrationruntimename|
|SSISPackageExecutionCancel|Yes|İptal edilen SSIS paketi yürütme ölçümleri|Count|Toplam||Integrationruntimename|
|Ssıspackageexecutionfailed|Yes|Başarısız SSIS paketi yürütme ölçümleri|Count|Toplam||Integrationruntimename|
|Ssıspackageexecutionsucceeded|Yes|Başarılı SSIS paketi yürütme ölçümleri|Count|Toplam||Integrationruntimename|
|Triggercancelledçalıştırmaları|Yes|İptal edilen tetikleyici çalışan ölçümleri|Count|Toplam||Ad, Failuyeniden yazma|
|Triggerfailedçalıştırmaları|Yes|Başarısız tetikleyici çalıştırma ölçümleri|Count|Toplam||Ad, Failuyeniden yazma|
|TriggerSucceededRuns|Yes|Başarılı tetikleyici çalışan ölçümleri|Count|Toplam||Ad, Failuyeniden yazma|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/hesapları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Jobauendediptal edildi|Yes|İptal edilen AU Saati|Saniye|Toplam|İptal edilen işler için toplam AU süresi.|Boyut yok|
|JobAUEndedFailure|Yes|Başarısız AU Saati|Saniye|Toplam|Başarısız işler için toplam AU süresi.|Boyut yok|
|JobAUEndedSuccess|Yes|Başarılı AU Saati|Saniye|Toplam|Başarılı işler için toplam AU süresi.|Boyut yok|
|Jobendediptal edildi|Yes|İptal edilen Işler|Count|Toplam|İptal edilen işlerin sayısı.|Boyut yok|
|JobEndedFailure|Yes|Başarısız Işler|Count|Toplam|Başarısız iş sayısı.|Boyut yok|
|JobEndedSuccess|Yes|Başarılı Işler|Count|Toplam|Başarılı iş sayısı.|Boyut yok|
|JobStage|Yes|Aşamadaki işler|Count|Toplam|Her aşamadaki iş sayısı.|Boyut yok|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/accounts

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|DataRead|Yes|Okunan veriler|Bayt|Toplam|Hesaptan okunan toplam veri miktarı.|Boyut yok|
|Veri yazıldı|Yes|Yazılan veriler|Bayt|Toplam|Hesaba yazılan toplam veri miktarı.|Boyut yok|
|ReadRequests|Yes|Okuma Istekleri|Count|Toplam|Hesaba yönelik veri okuma isteklerinin sayısı.|Boyut yok|
|TotalStorage|Yes|Toplam Depolama Alanı|Bayt|Maksimum|Hesapta depolanan toplam veri miktarı.|Boyut yok|
|WriteRequests|Yes|Yazma Istekleri|Count|Toplam|Hesaba veri yazma isteği sayısı.|Boyut yok|


## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/hesapları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Failedsharesubscriptioneşitlemeleri|Yes|Alınan paylaşma anlık görüntüleri alındı|Count|Count|Hesapta başarısız olan paylaşılan anlık görüntü sayısı|Boyut yok|
|Failedsharesyniyet|Yes|Gönderilen paylaşma başarısız anlık görüntüler|Count|Count|Hesaptaki gönderilen başarısız paylaşılan anlık görüntü sayısı|Boyut yok|
|ShareCount|Yes|Gönderilen paylaşımlar|Count|Maksimum|Hesaptaki gönderilen paylaşım sayısı|Madı|
|ShareSubscriptionCount|Yes|Alınan paylaşımlar|Count|Maksimum|Hesaptaki alınan paylaşım sayısı|ShareSubscriptionName|
|SucceededShareSubscriptionSynchronizations|Yes|Alınan paylaşma başarılı anlık görüntüleri|Count|Count|Hesaptaki alınan paylaşma başarılı anlık görüntülerinin sayısı|Boyut yok|
|SucceededShareSynchronizations|Yes|Gönderilen paylaşma başarılı anlık görüntüleri|Count|Count|Hesapta gönderilen başarılı paylaşılan anlık görüntü sayısı|Boyut yok|


## <a name="microsoftdbformariadbservers"></a>Microsoft. Dbformarıdb/sunucular

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|active_connections|Yes|Etkin Bağlantılar|Count|Ortalama|Etkin Bağlantılar|Boyut yok|
|backup_storage_used|Yes|Kullanılan yedekleme depolama alanı|Bayt|Ortalama|Kullanılan yedekleme depolama alanı|Boyut yok|
|connections_failed|Yes|Başarısız Bağlantılar|Count|Toplam|Başarısız Bağlantılar|Boyut yok|
|cpu_percent|Yes|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|Boyut yok|
|io_consumption_percent|Yes|GÇ yüzdesi|Yüzde|Ortalama|GÇ yüzdesi|Boyut yok|
|memory_percent|Yes|Bellek yüzdesi|Yüzde|Ortalama|Bellek yüzdesi|Boyut yok|
|network_bytes_egress|Yes|Ağ Çıkışı|Bayt|Toplam|Etkin bağlantılar arasında ağ çıkışı|Boyut yok|
|network_bytes_ingress|Yes|Ağ Girişi|Bayt|Toplam|Etkin bağlantılar genelinde ağ|Boyut yok|
|seconds_behind_master|Yes|Saniye cinsinden çoğaltma gecikmesi|Count|Maksimum|Saniye cinsinden çoğaltma gecikmesi|Boyut yok|
|serverlog_storage_limit|Yes|Sunucu günlüğü depolama sınırı|Bayt|Maksimum|Sunucu günlüğü depolama sınırı|Boyut yok|
|serverlog_storage_percent|Yes|Sunucu günlüğü depolama yüzdesi|Yüzde|Ortalama|Sunucu günlüğü depolama yüzdesi|Boyut yok|
|serverlog_storage_usage|Yes|Kullanılan sunucu günlüğü depolaması|Bayt|Ortalama|Kullanılan sunucu günlüğü depolaması|Boyut yok|
|storage_limit|Yes|Depolama sınırı|Bayt|Maksimum|Depolama sınırı|Boyut yok|
|storage_percent|Yes|Depolama alanı yüzdesi|Yüzde|Ortalama|Depolama alanı yüzdesi|Boyut yok|
|storage_used|Yes|Kullanılan depolama alanı|Bayt|Ortalama|Kullanılan depolama alanı|Boyut yok|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. Dbformyısql/Flexibelservers

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|aborted_connections|Yes|Durdurulan Bağlantılar|Count|Toplam|Durdurulan Bağlantılar|Boyut yok|
|active_connections|Yes|Etkin Bağlantılar|Count|Maksimum|Etkin Bağlantılar|Boyut yok|
|backup_storage_used|Yes|Kullanılan yedekleme depolama alanı|Bayt|Maksimum|Kullanılan yedekleme depolama alanı|Boyut yok|
|cpu_percent|Yes|Ana bilgisayar CPU 'SU yüzdesi|Yüzde|Maksimum|Ana bilgisayar CPU 'SU yüzdesi|Boyut yok|
|io_consumption_percent|Yes|GÇ yüzdesi|Yüzde|Maksimum|GÇ yüzdesi|Boyut yok|
|memory_percent|Yes|Ana bilgisayar belleği yüzdesi|Yüzde|Maksimum|Ana bilgisayar belleği yüzdesi|Boyut yok|
|network_bytes_egress|Yes|Ana bilgisayar ağı|Bayt|Toplam|Bayt cinsinden ana bilgisayar ağı çıkışı|Boyut yok|
|network_bytes_ingress|Yes|Konak ağı|Bayt|Toplam|Ana bilgisayar ağı girişi bayt|Boyut yok|
|Sorgular|Yes|Sorgular|Count|Toplam|Sorgular|Boyut yok|
|replication_lag|Yes|Saniye cinsinden çoğaltma gecikmesi|Saniye|Maksimum|Saniye cinsinden çoğaltma gecikmesi|Boyut yok|
|storage_limit|Yes|Depolama sınırı|Bayt|Maksimum|Depolama sınırı|Boyut yok|
|storage_percent|Yes|Depolama alanı yüzdesi|Yüzde|Maksimum|Depolama alanı yüzdesi|Boyut yok|
|storage_used|Yes|Kullanılan depolama alanı|Bayt|Maksimum|Kullanılan depolama alanı|Boyut yok|
|total_connections|Yes|Toplam bağlantı sayısı|Count|Toplam|Toplam bağlantı sayısı|Boyut yok|


## <a name="microsoftdbformysqlservers"></a>Microsoft. Dbformyısql/sunucuları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|active_connections|Yes|Etkin Bağlantılar|Count|Ortalama|Etkin Bağlantılar|Boyut yok|
|backup_storage_used|Yes|Kullanılan yedekleme depolama alanı|Bayt|Ortalama|Kullanılan yedekleme depolama alanı|Boyut yok|
|connections_failed|Yes|Başarısız Bağlantılar|Count|Toplam|Başarısız Bağlantılar|Boyut yok|
|cpu_percent|Yes|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|Boyut yok|
|io_consumption_percent|Yes|GÇ yüzdesi|Yüzde|Ortalama|GÇ yüzdesi|Boyut yok|
|memory_percent|Yes|Bellek yüzdesi|Yüzde|Ortalama|Bellek yüzdesi|Boyut yok|
|network_bytes_egress|Yes|Ağ Çıkışı|Bayt|Toplam|Etkin bağlantılar arasında ağ çıkışı|Boyut yok|
|network_bytes_ingress|Yes|Ağ Girişi|Bayt|Toplam|Etkin bağlantılar genelinde ağ|Boyut yok|
|seconds_behind_master|Yes|Saniye cinsinden çoğaltma gecikmesi|Count|Maksimum|Saniye cinsinden çoğaltma gecikmesi|Boyut yok|
|serverlog_storage_limit|Yes|Sunucu günlüğü depolama sınırı|Bayt|Maksimum|Sunucu günlüğü depolama sınırı|Boyut yok|
|serverlog_storage_percent|Yes|Sunucu günlüğü depolama yüzdesi|Yüzde|Ortalama|Sunucu günlüğü depolama yüzdesi|Boyut yok|
|serverlog_storage_usage|Yes|Kullanılan sunucu günlüğü depolaması|Bayt|Ortalama|Kullanılan sunucu günlüğü depolaması|Boyut yok|
|storage_limit|Yes|Depolama sınırı|Bayt|Maksimum|Depolama sınırı|Boyut yok|
|storage_percent|Yes|Depolama alanı yüzdesi|Yüzde|Ortalama|Depolama alanı yüzdesi|Boyut yok|
|storage_used|Yes|Kullanılan depolama alanı|Bayt|Ortalama|Kullanılan depolama alanı|Boyut yok|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/Flexibtaservers

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|active_connections|Yes|Etkin Bağlantılar|Count|Ortalama|Etkin Bağlantılar|Boyut yok|
|backup_storage_used|Yes|Kullanılan yedekleme depolama alanı|Bayt|Ortalama|Kullanılan yedekleme depolama alanı|Boyut yok|
|connections_failed|Yes|Başarısız Bağlantılar|Count|Toplam|Başarısız Bağlantılar|Boyut yok|
|connections_succeeded|Yes|Başarılı bağlantılar|Count|Toplam|Başarılı bağlantılar|Boyut yok|
|cpu_credits_consumed|Yes|Tüketilen CPU kredileri|Count|Ortalama|Veritabanı sunucusu tarafından tüketilen toplam kredi sayısı|Boyut yok|
|cpu_credits_remaining|Yes|Kalan CPU kredileri|Count|Ortalama|Patlama için kullanılabilen toplam kredi sayısı|Boyut yok|
|cpu_percent|Yes|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|Boyut yok|
|disk_queue_depth|Yes|Disk kuyruğu derinliği|Count|Ortalama|Veri diskine yönelik bekleyen g/ç işlemlerinin sayısı|Boyut yok|
|'ye|Yes|IOPS|Count|Ortalama|GÇ Işlemi/saniye|Boyut yok|
|maximum_used_transactionIDs|Yes|Kullanılan en fazla Işlem kimliği|Count|Ortalama|Kullanılan en fazla Işlem kimliği|Boyut yok|
|memory_percent|Yes|Bellek yüzdesi|Yüzde|Ortalama|Bellek yüzdesi|Boyut yok|
|network_bytes_egress|Yes|Ağ Çıkışı|Bayt|Toplam|Etkin bağlantılar arasında ağ çıkışı|Boyut yok|
|network_bytes_ingress|Yes|Ağ Girişi|Bayt|Toplam|Etkin bağlantılar genelinde ağ|Boyut yok|
|read_iops|Yes|IOPS 'yi oku|Count|Ortalama|Saniye başına veri disk g/ç okuma işlemi sayısı|Boyut yok|
|read_throughput|Yes|Okunan Işleme bayt/sn|Count|Ortalama|İzleme dönemi boyunca veri diskinden saniye başına okunan bayt sayısı|Boyut yok|
|storage_free|Yes|Depolama alanı boş|Bayt|Ortalama|Depolama alanı boş|Boyut yok|
|storage_percent|Yes|Depolama alanı yüzdesi|Yüzde|Ortalama|Depolama alanı yüzdesi|Boyut yok|
|storage_used|Yes|Kullanılan depolama alanı|Bayt|Ortalama|Kullanılan depolama alanı|Boyut yok|
|txlogs_storage_used|Yes|Kullanılan işlem günlüğü depolaması|Bayt|Ortalama|Kullanılan işlem günlüğü depolaması|Boyut yok|
|write_iops|Yes|IOPS yaz|Count|Ortalama|Saniye başına veri disk g/ç yazma işlemi sayısı|Boyut yok|
|write_throughput|Yes|Yazma aktarım hızı bayt/sn|Count|Ortalama|İzleme dönemi boyunca veri diskine saniye başına yazılan bayt sayısı|Boyut yok|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft. DBForPostgreSQL/serverGroupsv2

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|active_connections|Yes|Etkin Bağlantılar|Count|Ortalama|Etkin Bağlantılar|aboneliğinde ve|
|cpu_percent|Yes|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|aboneliğinde ve|
|'ye|Yes|IOPS|Count|Ortalama|GÇ işlemi/saniye|aboneliğinde ve|
|memory_percent|Yes|Bellek yüzdesi|Yüzde|Ortalama|Bellek yüzdesi|aboneliğinde ve|
|network_bytes_egress|Yes|Ağ Çıkışı|Bayt|Toplam|Etkin bağlantılar arasında ağ çıkışı|aboneliğinde ve|
|network_bytes_ingress|Yes|Ağ Girişi|Bayt|Toplam|Etkin bağlantılar genelinde ağ|aboneliğinde ve|
|storage_percent|Yes|Depolama alanı yüzdesi|Yüzde|Ortalama|Depolama alanı yüzdesi|aboneliğinde ve|
|storage_used|Yes|Kullanılan depolama alanı|Bayt|Ortalama|Kullanılan depolama alanı|aboneliğinde ve|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/sunucuları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|active_connections|Yes|Etkin Bağlantılar|Count|Ortalama|Etkin Bağlantılar|Boyut yok|
|backup_storage_used|Yes|Kullanılan yedekleme depolama alanı|Bayt|Ortalama|Kullanılan yedekleme depolama alanı|Boyut yok|
|connections_failed|Yes|Başarısız Bağlantılar|Count|Toplam|Başarısız Bağlantılar|Boyut yok|
|cpu_percent|Yes|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|Boyut yok|
|io_consumption_percent|Yes|GÇ yüzdesi|Yüzde|Ortalama|GÇ yüzdesi|Boyut yok|
|memory_percent|Yes|Bellek yüzdesi|Yüzde|Ortalama|Bellek yüzdesi|Boyut yok|
|network_bytes_egress|Yes|Ağ Çıkışı|Bayt|Toplam|Etkin bağlantılar arasında ağ çıkışı|Boyut yok|
|network_bytes_ingress|Yes|Ağ Girişi|Bayt|Toplam|Etkin bağlantılar genelinde ağ|Boyut yok|
|pg_replica_log_delay_in_bytes|Yes|Çoğaltmalar genelinde en fazla gecikme|Bayt|Maksimum|En fazla çoğaltma çoğaltmasının bayt cinsinden gecikmesi|Boyut yok|
|pg_replica_log_delay_in_seconds|Yes|Çoğaltma gecikmesi|Saniye|Maksimum|Saniye cinsinden çoğaltma gecikmesi|Boyut yok|
|serverlog_storage_limit|Yes|Sunucu günlüğü depolama sınırı|Bayt|Maksimum|Sunucu günlüğü depolama sınırı|Boyut yok|
|serverlog_storage_percent|Yes|Sunucu günlüğü depolama yüzdesi|Yüzde|Ortalama|Sunucu günlüğü depolama yüzdesi|Boyut yok|
|serverlog_storage_usage|Yes|Kullanılan sunucu günlüğü depolaması|Bayt|Ortalama|Kullanılan sunucu günlüğü depolaması|Boyut yok|
|storage_limit|Yes|Depolama sınırı|Bayt|Maksimum|Depolama sınırı|Boyut yok|
|storage_percent|Yes|Depolama alanı yüzdesi|Yüzde|Ortalama|Depolama alanı yüzdesi|Boyut yok|
|storage_used|Yes|Kullanılan depolama alanı|Bayt|Ortalama|Kullanılan depolama alanı|Boyut yok|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|active_connections|Yes|Etkin Bağlantılar|Count|Ortalama|Etkin Bağlantılar|Boyut yok|
|cpu_percent|Yes|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|Boyut yok|
|'ye|Yes|IOPS|Count|Ortalama|GÇ Işlemi/saniye|Boyut yok|
|memory_percent|Yes|Bellek yüzdesi|Yüzde|Ortalama|Bellek yüzdesi|Boyut yok|
|network_bytes_egress|Yes|Ağ Çıkışı|Bayt|Toplam|Etkin bağlantılar arasında ağ çıkışı|Boyut yok|
|network_bytes_ingress|Yes|Ağ Girişi|Bayt|Toplam|Etkin bağlantılar genelinde ağ|Boyut yok|
|storage_percent|Yes|Depolama alanı yüzdesi|Yüzde|Ortalama|Depolama alanı yüzdesi|Boyut yok|
|storage_used|Yes|Kullanılan depolama alanı|Bayt|Ortalama|Kullanılan depolama alanı|Boyut yok|


## <a name="microsoftdeviceselasticpools"></a>Microsoft. Devices/Elaun havuzları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Elaun Pool. requestedUsageRate|Yes|istenen kullanım oranı|Yüzde|Ortalama|istenen kullanım oranı|Boyut yok|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft. Devices/Elaun havuzları/Iothubkiracılar

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|C2D. Commands. çıkış. Abandon. Success|Yes|C2D iletileri bırakıldı|Count|Toplam|Cihaz tarafından bırakılan buluttan cihaza yönelik ileti sayısı|Boyut yok|
|C2D. Commands. çıkış. tamamlandı. başarılı|Yes|C2D ileti teslimleri tamamlandı|Count|Toplam|Cihaz tarafından başarıyla tamamlanan buluttan cihaza ileti teslimleri sayısı|Boyut yok|
|C2D. Commands. çıkış. Red. Success|Yes|C2D iletileri reddedildi|Count|Toplam|Cihaz tarafından reddedilen buluttan cihaza yönelik ileti sayısı|Boyut yok|
|C2D. Methods. Failure|Yes|Başarısız doğrudan yöntem etkinleştirmeleri|Count|Toplam|Tüm başarısız doğrudan yöntem çağrılarının sayısı.|Boyut yok|
|C2D. Methods. requestSize|Yes|Doğrudan yöntem etkinleştirmeleri istek boyutu|Bayt|Ortalama|Tüm başarılı doğrudan yöntem isteklerinin ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|C2D. Methods. responseSize|Yes|Doğrudan yöntem etkinleştirmeleri yanıt boyutu|Bayt|Ortalama|Tüm başarılı doğrudan yöntem yanıtlarının ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|C2D. Methods. Success|Yes|Başarılı doğrudan yöntem etkinleştirmeleri|Count|Toplam|Tüm başarılı doğrudan yöntem çağrılarının sayısı.|Boyut yok|
|C2D. ikizi. Read. Failure|Yes|Arka uçtan başarısız ikizi okumaları|Count|Toplam|Tüm başarısız arka uç tarafından başlatılan ikizi okumaların sayısı.|Boyut yok|
|C2D. ikizi. Read. size|Yes|Arka uçtan gelen ikizi okumaların yanıt boyutu|Bayt|Ortalama|Tüm başarılı arka uç başlatılan ikizi okumalarının ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|C2D. ikizi. Read. Success|Yes|Arka uçtan başarılı ikizi okumaları|Count|Toplam|Tüm başarılı arka uç tarafından başlatılan ikizi okumaların sayısı.|Boyut yok|
|C2D. ikizi. Update. Failure|Yes|Arka uçtan başarısız ikizi güncelleştirmeleri|Count|Toplam|Tüm başarısız arka uç başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|C2D. ikizi. Update. size|Yes|Arka uçtan ikizi güncelleştirmelerinin boyutu|Bayt|Ortalama|Tüm başarılı arka uç ile başlatılan ikizi güncelleştirmelerinin ortalama, en düşük ve en büyük boyutu.|Boyut yok|
|C2D. ikizi. Update. Success|Yes|Arka uçtan başarılı ikizi güncelleştirmeleri|Count|Toplam|Tüm başarılı arka uç ile başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|C2DMessagesExpired|Yes|C2D Iletilerinin son kullanma|Count|Toplam|Zaman aşımına uğradı buluttan cihaza ileti sayısı|Boyut yok|
|konfigürasyonları|Yes|Yapılandırma ölçümleri|Count|Toplam|Yapılandırma Işlemleri için ölçümler|Boyut yok|
|connectedDeviceCount|Yes|Bağlı cihazlar|Count|Ortalama|IoT Hub 'ınıza bağlı cihazların sayısı|Boyut yok|
|D2C. endpoints. çıkış. builtIn. Events|Yes|Yönlendirme: iletilere/olaylara teslim edilen iletiler|Count|Toplam|Yönlendirmenin, yerleşik uç noktaya (iletiler/olaylar) başarıyla ileti teslim IoT Hub sayısı.|Boyut yok|
|D2C. endpoints. çıkış. eventHubs|Yes|Yönlendirme: Olay Hub 'ına teslim edilen iletiler|Count|Toplam|IoT Hub yönlendirmenin Olay Hub 'ı uç noktalarına başarıyla ileti teslim eden zaman sayısı.|Boyut yok|
|D2C. endpoints. çıkış. serviceBusQueues|Yes|Yönlendirme: Service Bus kuyruğuna teslim edilen iletiler|Count|Toplam|IoT Hub yönlendirmenin iletileri Service Bus sıra uç noktalarına başarıyla teslim dağıttığı zaman sayısı.|Boyut yok|
|D2C. endpoints. çıkış. Servicebuskonular|Yes|Yönlendirme: Service Bus konuya teslim edilen iletiler|Count|Toplam|IoT Hub yönlendirmenin Service Bus konu uç noktalarına başarıyla ileti teslim eden zaman sayısı.|Boyut yok|
|D2C. endpoints. çıkış. Storage|Yes|Yönlendirme: depolamaya teslim edilen iletiler|Count|Toplam|IoT Hub yönlendirmenin depolama uç noktalarına başarıyla ileti teslim eden zaman sayısı.|Boyut yok|
|D2C. endpoints. çıkış. Storage. blob 'ları|Yes|Yönlendirme: depolamaya teslim edilen Bloblar|Count|Toplam|IoT Hub blob 'ları depolama uç noktalarına yönlendirmenin kaç kez yönlendirtığı sayısıdır.|Boyut yok|
|D2C. endpoints. çıkış. Storage. Bytes|Yes|Yönlendirme: depolamaya teslim edilen veriler|Bayt|Toplam|Depolama uç noktalarına teslim edilen veri miktarı (bayt) IoT Hub.|Boyut yok|
|D2C. endpoints. Latency. yerleik. Events|Yes|Yönlendirme: iletiler/olaylar için ileti gecikmesi|Mayacak|Ortalama|Yerleşik uç noktaya (iletiler/olaylar) gelen ileti girişi IoT Hub ve telemetri iletisi arasındaki ortalama gecikme süresi (milisaniye).|Boyut yok|
|D2C. endpoints. Latency. eventHubs|Yes|Yönlendirme: Olay Hub 'ı için ileti gecikmesi|Mayacak|Ortalama|İleti girişi ile bir olay hub 'ı uç noktasına giriş IoT Hub ve ileti girişi arasındaki ortalama gecikme (milisaniye).|Boyut yok|
|D2C. endpoints. Latency. serviceBusQueues|Yes|Yönlendirme: Service Bus kuyruğu için ileti gecikmesi|Mayacak|Ortalama|İleti girişi IoT Hub ve telemetri iletisi arasındaki ortalama gecikme süresi (milisaniye) Service Bus kuyruğu uç noktasına giriş.|Boyut yok|
|D2C. endpoints. Latency. Servicebuskonular|Yes|Yönlendirme: Service Bus konusu için ileti gecikmesi|Mayacak|Ortalama|İleti girişi IoT Hub ve telemetri iletisi arasındaki ortalama gecikme süresi (milisaniye) Service Bus konu uç noktasına giriş.|Boyut yok|
|D2C. endpoints. Latency. Storage|Yes|Yönlendirme: depolama için ileti gecikmesi|Mayacak|Ortalama|İleti girişi IoT Hub ve telemetri iletisine bir depolama uç noktasına giriş arasındaki ortalama gecikme süresi (milisaniye).|Boyut yok|
|D2C. telemetri. çıkış. bırakıldı|Yes|Yönlendirme: telemetri iletileri bırakıldı |Count|Toplam|İleti, ölü uç noktalar nedeniyle IoT Hub yönlendirmenin kaç kez bırakılmakta olduğunu. Bu değer, bırakılan iletiler buraya teslim edimediğinden geri dönüş rotasına teslim edilen iletileri saymaz.|Boyut yok|
|D2C. telemetri. çıkış. geri dönüş|Yes|Yönlendirme: geri dönüşe teslim edilen iletiler|Count|Toplam|Geri dönüş rotası ile ilişkili uç noktaya gönderilen iletileri yönlendirmenin IoT Hub sayısı.|Boyut yok|
|D2C. telemetri. çıkış. geçersiz|Yes|Yönlendirme: telemetri iletileri uyumsuz|Count|Toplam|Uç nokta ile uyumsuzluk nedeniyle IoT Hub yönlendirmenin iletileri teslim etme başarısız olduğu zaman sayısı. Bu değer yeniden denemeler içermez.|Boyut yok|
|D2C. telemetri. çıkış. yalnız bırakılmış|Yes|Yönlendirme: telemetri iletileri yalnız bırakılmış |Count|Toplam|Herhangi bir yönlendirme kuralıyla eşleşmediğinden (geri dönüş kuralı dahil) iletilerin IoT Hub yönlendirme tarafından yalnız bırakılmış olduğu zaman sayısı. |Boyut yok|
|D2C. telemetri. çıkış. başarılı|Yes|Yönlendirme: teslim edilen telemetri iletileri|Count|Toplam|IoT Hub yönlendirme kullanılarak iletilerin tüm uç noktalara başarıyla ulaştırılan ileti sayısı. Bir ileti birden çok uç noktaya yönlendirilmemişse, her başarılı teslimat için bu değer bir artar. Bir ileti aynı uç noktaya birden çok kez teslim edildiğinde, her başarılı teslimat için bu değer bir artar.|Boyut yok|
|D2C. telemetri. ınress. allProtocol|Yes|Telemetri iletisi gönderme denemeleri|Count|Toplam|IoT Hub 'ınıza gönderilmeye çalışılan cihazdan buluta telemetri iletisi sayısı|Boyut yok|
|D2C. telemetri. ınress. Sendvaliz|Yes|Daraltma hatası sayısı|Count|Toplam|Cihaz performansı azaltmasından kaynaklanan azaltma hatası sayısı|Boyut yok|
|D2C. telemetri. giriş. başarılı|Yes|Gönderilen telemetri iletileri|Count|Toplam|IoT Hub 'ınıza başarıyla gönderilen cihazdan buluta telemetri iletisi sayısı|Boyut yok|
|D2C. ikizi. Read. Failure|Yes|Cihazlardan başarısız ikizi okumaları|Count|Toplam|Tüm başarısız cihaz tarafından başlatılan ikizi okumaların sayısı.|Boyut yok|
|D2C. ikizi. Read. size|Yes|Cihazlardan gelen ikizi okumaların yanıt boyutu|Bayt|Ortalama|Tüm başarılı cihaz tarafından başlatılan ikizi okumalarının ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|D2C. ikizi. Read. Success|Yes|Cihazlardan başarılı ikizi okumaları|Count|Toplam|Tüm başarılı cihaz tarafından başlatılan ikizi okuma sayısı.|Boyut yok|
|D2C. ikizi. Update. Failure|Yes|Cihazlardan ikizi güncelleştirmeleri başarısız oldu|Count|Toplam|Tüm başarısız cihaz tarafından başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|D2C. ikizi. Update. size|Yes|Cihazlardan ikizi güncelleştirmelerinin boyutu|Bayt|Ortalama|Tüm başarılı cihaz tarafından başlatılan ikizi güncelleştirmelerinin ortalama, en düşük ve en büyük boyutu.|Boyut yok|
|D2C. ikizi. Update. Success|Yes|Cihazlardan başarılı ikizi güncelleştirmeleri|Count|Toplam|Tüm başarılı cihaz tarafından başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|Günlükmessagequotakullanıldı|Yes|Kullanılan toplam ileti sayısı|Count|Maksimum|Bugün kullanılan toplam ileti sayısı|Boyut yok|
|deviceDataUsage|Yes|Toplam cihaz verisi kullanımı|Bayt|Toplam|Iothub 'e bağlı tüm cihazlardan ve bunlardan aktarılan baytlar|Boyut yok|
|deviceDataUsageV2|Yes|Toplam cihaz verisi kullanımı (Önizleme)|Bayt|Toplam|Iothub 'e bağlı tüm cihazlardan ve bunlardan aktarılan baytlar|Boyut yok|
|Devices. connectedDevices. allProtocol|Yes|Bağlı cihazlar (kullanım dışı) |Count|Toplam|IoT Hub 'ınıza bağlı cihazların sayısı|Boyut yok|
|Devices. totalDevices|Yes|Toplam cihaz (kullanım dışı)|Count|Toplam|IoT Hub 'ınıza kayıtlı cihazların sayısı|Boyut yok|
|Eventgridteslimler|Yes|Event Grid teslimler|Count|Toplam|Event Grid yayımlanan IoT Hub olaylarının sayısı. Başarılı ve başarısız isteklerin sayısı için sonuç boyutunu kullanın. EventType boyutu olay türünü gösterir ( https://aka.ms/ioteventgrid) .|Sonuç, EventType|
|EventGridLatency|Yes|Event Grid gecikme süresi|Mayacak|Ortalama|Olay Event Grid yayımlandığında IoT Hub olayının oluşturulduğu sürenin ortalama gecikme süresi (milisaniye). Bu sayı, tüm olay türleri arasındaki bir ortalama değer. Belirli bir olay türünün gecikmesini görmek için EventType boyutunu kullanın.|Olay türü|
|Jobs. cancelJob. Failure|Yes|Başarısız iş iptalleri|Count|Toplam|İşi iptal etmek için başarısız olan tüm çağrıların sayısı.|Boyut yok|
|Jobs. cancelJob. Success|Yes|Başarılı iş iptalleri|Count|Toplam|İşi iptal etmek için tüm başarılı çağrı sayısı.|Boyut yok|
|işler. tamamlandı|Yes|Tamamlanan İşler|Count|Toplam|Tamamlanan tüm işlerin sayısı.|Boyut yok|
|Jobs. createDirectMethodJob. Failure|Yes|Yöntem çağırma işlerinin oluşturma işlemi başarısız oldu|Count|Toplam|Doğrudan yöntem çağırma işlerinin tüm başarısız oluşturma sayısı.|Boyut yok|
|Jobs. createDirectMethodJob. Success|Yes|Yöntem çağırma işlerinin başarılı oluşturmaları|Count|Toplam|Tüm başarılı doğrudan yöntem çağırma işlerinin oluşturulma sayısı.|Boyut yok|
|Jobs. createTwinUpdateJob. Failure|Yes|İkizi Update işlerinin başarısız oluşturmaları|Count|Toplam|İkizi Update işlerinin başarısız olan tüm oluşturma sayısı.|Boyut yok|
|Jobs. createTwinUpdateJob. Success|Yes|İkizi Update işlerinin başarılı oluşturmaları|Count|Toplam|Tüm başarılı ikizi Update işlerinin oluşturma sayısı.|Boyut yok|
|işler. başarısız|Yes|Başarısız işler|Count|Toplam|Başarısız olan tüm işlerin sayısı.|Boyut yok|
|Jobs. listJobs. hata|Yes|İşleri listelemek için başarısız çağrılar|Count|Toplam|Listeleme işleri için başarısız olan tüm çağrıların sayısı.|Boyut yok|
|işler. listJobs. başarılı|Yes|İşleri listelemek için başarılı çağrılar|Count|Toplam|İşleri listelemek için başarılı olan tüm çağrıların sayısı.|Boyut yok|
|Jobs. queryJobs. hata|Yes|Başarısız iş sorguları|Count|Toplam|Sorgu işlerine yapılan tüm başarısız çağrıların sayısı.|Boyut yok|
|işler. queryJobs. başarılı|Yes|Başarılı iş sorguları|Count|Toplam|Sorgu işlerine yapılan tüm başarılı çağrıların sayısı.|Boyut yok|
|RoutingDataSizeInBytesDelivered|Yes|Yönlendirme teslim Iletisi boyutunu bayt cinsinden (Önizleme)|Bayt|Toplam|IoT Hub tarafından bir uç noktaya gönderilen iletilerin bayt cinsinden toplam boyutu. Mesajların farklı uç noktalarınıza teslim edilen bayt boyutunu görüntülemek için EndpointName ve EndpointType boyutlarını kullanabilirsiniz. Ölçüm değeri, ileti birden çok uç noktaya teslim edildiğinde veya ileti aynı uç noktaya birden çok kez teslim edildiğinde de dahil olmak üzere, teslim edilen her ileti için artar.|EndpointType, EndpointName, RoutingSource|
|Routingteslimler|Yes|Yönlendirme teslimleri (Önizleme)|Count|Toplam|IoT Hub yönlendirme kullanan tüm uç noktalara ileti sunmaya kaç kez girişimde denendiğinde. Başarılı veya başarısız denemelerin sayısını görmek için sonuç boyutunu kullanın. Hatalı, bırakılmış veya yalnız bırakılmış gibi başarısızlık nedenini görmek için FailureReasonCategory boyutunu kullanın. Ayrıca, farklı uç noktalarınıza kaç tane ileti teslim edildiğini anlamak için EndpointName ve EndpointType boyutlarını da kullanabilirsiniz. Bir ileti birden çok uç noktaya teslim edildiğinde veya ileti aynı uç noktaya birden çok kez teslim edildiğinde, ölçüm değeri her bir teslim girişimi için bir artırılır.|EndpointType, EndpointName, FailureReasonCategory, result, RoutingSource|
|Routingdeliverygecikme süresi|Yes|Yönlendirme teslimi gecikmesi (Önizleme)|Mayacak|Ortalama|İleti girişi IoT Hub ve telemetri iletisi ile bir uç noktaya giriş arasındaki ortalama gecikme (milisaniye). Farklı uç noktalarınıza gecikme süresini anlamak için EndpointName ve EndpointType boyutlarını kullanabilirsiniz.|EndpointType, EndpointName, RoutingSource|
|tenantHub.requestedUsageRate|Yes|istenen kullanım oranı|Yüzde|Ortalama|istenen kullanım oranı|Boyut yok|
|totalDeviceCount|Yes|Toplam cihaz sayısı|Count|Ortalama|IoT Hub 'ınıza kayıtlı cihazların sayısı|Boyut yok|
|twinQueries. Failure|Yes|Başarısız ikizi sorguları|Count|Toplam|Tüm başarısız ikizi sorgularının sayısı.|Boyut yok|
|twinQueries. resultSize|Yes|İkizi sorguları sonuç boyutu|Bayt|Ortalama|Tüm başarılı ikizi sorgularının sonuç boyutunun ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|twinQueries. Success|Yes|Başarılı ikizi sorguları|Count|Toplam|Tüm başarılı ikizi sorgularının sayısı.|Boyut yok|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|C2D. Commands. çıkış. Abandon. Success|Yes|C2D iletileri bırakıldı|Count|Toplam|Cihaz tarafından bırakılan buluttan cihaza yönelik ileti sayısı|Boyut yok|
|C2D. Commands. çıkış. tamamlandı. başarılı|Yes|C2D ileti teslimleri tamamlandı|Count|Toplam|Cihaz tarafından başarıyla tamamlanan buluttan cihaza ileti teslimleri sayısı|Boyut yok|
|C2D. Commands. çıkış. Red. Success|Yes|C2D iletileri reddedildi|Count|Toplam|Cihaz tarafından reddedilen buluttan cihaza yönelik ileti sayısı|Boyut yok|
|C2D. Methods. Failure|Yes|Başarısız doğrudan yöntem etkinleştirmeleri|Count|Toplam|Tüm başarısız doğrudan yöntem çağrılarının sayısı.|Boyut yok|
|C2D. Methods. requestSize|Yes|Doğrudan yöntem etkinleştirmeleri istek boyutu|Bayt|Ortalama|Tüm başarılı doğrudan yöntem isteklerinin ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|C2D. Methods. responseSize|Yes|Doğrudan yöntem etkinleştirmeleri yanıt boyutu|Bayt|Ortalama|Tüm başarılı doğrudan yöntem yanıtlarının ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|C2D. Methods. Success|Yes|Başarılı doğrudan yöntem etkinleştirmeleri|Count|Toplam|Tüm başarılı doğrudan yöntem çağrılarının sayısı.|Boyut yok|
|C2D. ikizi. Read. Failure|Yes|Arka uçtan başarısız ikizi okumaları|Count|Toplam|Tüm başarısız arka uç tarafından başlatılan ikizi okumaların sayısı.|Boyut yok|
|C2D. ikizi. Read. size|Yes|Arka uçtan gelen ikizi okumaların yanıt boyutu|Bayt|Ortalama|Tüm başarılı arka uç başlatılan ikizi okumalarının ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|C2D. ikizi. Read. Success|Yes|Arka uçtan başarılı ikizi okumaları|Count|Toplam|Tüm başarılı arka uç tarafından başlatılan ikizi okumaların sayısı.|Boyut yok|
|C2D. ikizi. Update. Failure|Yes|Arka uçtan başarısız ikizi güncelleştirmeleri|Count|Toplam|Tüm başarısız arka uç başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|C2D. ikizi. Update. size|Yes|Arka uçtan ikizi güncelleştirmelerinin boyutu|Bayt|Ortalama|Tüm başarılı arka uç ile başlatılan ikizi güncelleştirmelerinin ortalama, en düşük ve en büyük boyutu.|Boyut yok|
|C2D. ikizi. Update. Success|Yes|Arka uçtan başarılı ikizi güncelleştirmeleri|Count|Toplam|Tüm başarılı arka uç ile başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|C2DMessagesExpired|Yes|C2D Iletilerinin son kullanma|Count|Toplam|Zaman aşımına uğradı buluttan cihaza ileti sayısı|Boyut yok|
|konfigürasyonları|Yes|Yapılandırma ölçümleri|Count|Toplam|Yapılandırma Işlemleri için ölçümler|Boyut yok|
|connectedDeviceCount|No|Bağlı cihazlar|Count|Ortalama|IoT Hub 'ınıza bağlı cihazların sayısı|Boyut yok|
|D2C. endpoints. çıkış. builtIn. Events|Yes|Yönlendirme: iletilere/olaylara teslim edilen iletiler|Count|Toplam|Yönlendirmenin, yerleşik uç noktaya (iletiler/olaylar) başarıyla ileti teslim IoT Hub sayısı.|Boyut yok|
|D2C. endpoints. çıkış. eventHubs|Yes|Yönlendirme: Olay Hub 'ına teslim edilen iletiler|Count|Toplam|IoT Hub yönlendirmenin Olay Hub 'ı uç noktalarına başarıyla ileti teslim eden zaman sayısı.|Boyut yok|
|D2C. endpoints. çıkış. serviceBusQueues|Yes|Yönlendirme: Service Bus kuyruğuna teslim edilen iletiler|Count|Toplam|IoT Hub yönlendirmenin iletileri Service Bus sıra uç noktalarına başarıyla teslim dağıttığı zaman sayısı.|Boyut yok|
|D2C. endpoints. çıkış. Servicebuskonular|Yes|Yönlendirme: Service Bus konuya teslim edilen iletiler|Count|Toplam|IoT Hub yönlendirmenin Service Bus konu uç noktalarına başarıyla ileti teslim eden zaman sayısı.|Boyut yok|
|D2C. endpoints. çıkış. Storage|Yes|Yönlendirme: depolamaya teslim edilen iletiler|Count|Toplam|IoT Hub yönlendirmenin depolama uç noktalarına başarıyla ileti teslim eden zaman sayısı.|Boyut yok|
|D2C. endpoints. çıkış. Storage. blob 'ları|Yes|Yönlendirme: depolamaya teslim edilen Bloblar|Count|Toplam|IoT Hub blob 'ları depolama uç noktalarına yönlendirmenin kaç kez yönlendirtığı sayısıdır.|Boyut yok|
|D2C. endpoints. çıkış. Storage. Bytes|Yes|Yönlendirme: depolamaya teslim edilen veriler|Bayt|Toplam|Depolama uç noktalarına teslim edilen veri miktarı (bayt) IoT Hub.|Boyut yok|
|D2C. endpoints. Latency. yerleik. Events|Yes|Yönlendirme: iletiler/olaylar için ileti gecikmesi|Mayacak|Ortalama|Yerleşik uç noktaya (iletiler/olaylar) gelen ileti girişi IoT Hub ve telemetri iletisi arasındaki ortalama gecikme süresi (milisaniye).|Boyut yok|
|D2C. endpoints. Latency. eventHubs|Yes|Yönlendirme: Olay Hub 'ı için ileti gecikmesi|Mayacak|Ortalama|İleti girişi ile bir olay hub 'ı uç noktasına giriş IoT Hub ve ileti girişi arasındaki ortalama gecikme (milisaniye).|Boyut yok|
|D2C. endpoints. Latency. serviceBusQueues|Yes|Yönlendirme: Service Bus kuyruğu için ileti gecikmesi|Mayacak|Ortalama|İleti girişi IoT Hub ve telemetri iletisi arasındaki ortalama gecikme süresi (milisaniye) Service Bus kuyruğu uç noktasına giriş.|Boyut yok|
|D2C. endpoints. Latency. Servicebuskonular|Yes|Yönlendirme: Service Bus konusu için ileti gecikmesi|Mayacak|Ortalama|İleti girişi IoT Hub ve telemetri iletisi arasındaki ortalama gecikme süresi (milisaniye) Service Bus konu uç noktasına giriş.|Boyut yok|
|D2C. endpoints. Latency. Storage|Yes|Yönlendirme: depolama için ileti gecikmesi|Mayacak|Ortalama|İleti girişi IoT Hub ve telemetri iletisine bir depolama uç noktasına giriş arasındaki ortalama gecikme süresi (milisaniye).|Boyut yok|
|D2C. telemetri. çıkış. bırakıldı|Yes|Yönlendirme: telemetri iletileri bırakıldı |Count|Toplam|İleti, ölü uç noktalar nedeniyle IoT Hub yönlendirmenin kaç kez bırakılmakta olduğunu. Bu değer, bırakılan iletiler buraya teslim edimediğinden geri dönüş rotasına teslim edilen iletileri saymaz.|Boyut yok|
|D2C. telemetri. çıkış. geri dönüş|Yes|Yönlendirme: geri dönüşe teslim edilen iletiler|Count|Toplam|Geri dönüş rotası ile ilişkili uç noktaya gönderilen iletileri yönlendirmenin IoT Hub sayısı.|Boyut yok|
|D2C. telemetri. çıkış. geçersiz|Yes|Yönlendirme: telemetri iletileri uyumsuz|Count|Toplam|Uç nokta ile uyumsuzluk nedeniyle IoT Hub yönlendirmenin iletileri teslim etme başarısız olduğu zaman sayısı. Bu değer yeniden denemeler içermez.|Boyut yok|
|D2C. telemetri. çıkış. yalnız bırakılmış|Yes|Yönlendirme: telemetri iletileri yalnız bırakılmış |Count|Toplam|Herhangi bir yönlendirme kuralıyla eşleşmediğinden (geri dönüş kuralı dahil) iletilerin IoT Hub yönlendirme tarafından yalnız bırakılmış olduğu zaman sayısı. |Boyut yok|
|D2C. telemetri. çıkış. başarılı|Yes|Yönlendirme: teslim edilen telemetri iletileri|Count|Toplam|IoT Hub yönlendirme kullanılarak iletilerin tüm uç noktalara başarıyla ulaştırılan ileti sayısı. Bir ileti birden çok uç noktaya yönlendirilmemişse, her başarılı teslimat için bu değer bir artar. Bir ileti aynı uç noktaya birden çok kez teslim edildiğinde, her başarılı teslimat için bu değer bir artar.|Boyut yok|
|D2C. telemetri. ınress. allProtocol|Yes|Telemetri iletisi gönderme denemeleri|Count|Toplam|IoT Hub 'ınıza gönderilmeye çalışılan cihazdan buluta telemetri iletisi sayısı|Boyut yok|
|D2C. telemetri. ınress. Sendvaliz|Yes|Daraltma hatası sayısı|Count|Toplam|Cihaz performansı azaltmasından kaynaklanan azaltma hatası sayısı|Boyut yok|
|D2C. telemetri. giriş. başarılı|Yes|Gönderilen telemetri iletileri|Count|Toplam|IoT Hub 'ınıza başarıyla gönderilen cihazdan buluta telemetri iletisi sayısı|Boyut yok|
|D2C. ikizi. Read. Failure|Yes|Cihazlardan başarısız ikizi okumaları|Count|Toplam|Tüm başarısız cihaz tarafından başlatılan ikizi okumaların sayısı.|Boyut yok|
|D2C. ikizi. Read. size|Yes|Cihazlardan gelen ikizi okumaların yanıt boyutu|Bayt|Ortalama|Tüm başarılı cihaz tarafından başlatılan ikizi okumalarının ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|D2C. ikizi. Read. Success|Yes|Cihazlardan başarılı ikizi okumaları|Count|Toplam|Tüm başarılı cihaz tarafından başlatılan ikizi okuma sayısı.|Boyut yok|
|D2C. ikizi. Update. Failure|Yes|Cihazlardan ikizi güncelleştirmeleri başarısız oldu|Count|Toplam|Tüm başarısız cihaz tarafından başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|D2C. ikizi. Update. size|Yes|Cihazlardan ikizi güncelleştirmelerinin boyutu|Bayt|Ortalama|Tüm başarılı cihaz tarafından başlatılan ikizi güncelleştirmelerinin ortalama, en düşük ve en büyük boyutu.|Boyut yok|
|D2C. ikizi. Update. Success|Yes|Cihazlardan başarılı ikizi güncelleştirmeleri|Count|Toplam|Tüm başarılı cihaz tarafından başlatılan ikizi güncelleştirmelerinin sayısı.|Boyut yok|
|Günlükmessagequotakullanıldı|Yes|Kullanılan toplam ileti sayısı|Count|Maksimum|Bugün kullanılan toplam ileti sayısı|Boyut yok|
|deviceDataUsage|Yes|Toplam cihaz verisi kullanımı|Bayt|Toplam|Iothub 'e bağlı tüm cihazlardan ve bunlardan aktarılan baytlar|Boyut yok|
|deviceDataUsageV2|Yes|Toplam cihaz verisi kullanımı (Önizleme)|Bayt|Toplam|Iothub 'e bağlı tüm cihazlardan ve bunlardan aktarılan baytlar|Boyut yok|
|Devices. connectedDevices. allProtocol|Yes|Bağlı cihazlar (kullanım dışı) |Count|Toplam|IoT Hub 'ınıza bağlı cihazların sayısı|Boyut yok|
|Devices. totalDevices|Yes|Toplam cihaz (kullanım dışı)|Count|Toplam|IoT Hub 'ınıza kayıtlı cihazların sayısı|Boyut yok|
|Eventgridteslimler|Yes|Event Grid teslimler|Count|Toplam|Event Grid yayımlanan IoT Hub olaylarının sayısı. Başarılı ve başarısız isteklerin sayısı için sonuç boyutunu kullanın. EventType boyutu olay türünü gösterir ( https://aka.ms/ioteventgrid) .|Sonuç, EventType|
|EventGridLatency|Yes|Event Grid gecikme süresi|Mayacak|Ortalama|Olay Event Grid yayımlandığında IoT Hub olayının oluşturulduğu sürenin ortalama gecikme süresi (milisaniye). Bu sayı, tüm olay türleri arasındaki bir ortalama değer. Belirli bir olay türünün gecikmesini görmek için EventType boyutunu kullanın.|Olay türü|
|Jobs. cancelJob. Failure|Yes|Başarısız iş iptalleri|Count|Toplam|İşi iptal etmek için başarısız olan tüm çağrıların sayısı.|Boyut yok|
|Jobs. cancelJob. Success|Yes|Başarılı iş iptalleri|Count|Toplam|İşi iptal etmek için tüm başarılı çağrı sayısı.|Boyut yok|
|işler. tamamlandı|Yes|Tamamlanan İşler|Count|Toplam|Tamamlanan tüm işlerin sayısı.|Boyut yok|
|Jobs. createDirectMethodJob. Failure|Yes|Yöntem çağırma işlerinin oluşturma işlemi başarısız oldu|Count|Toplam|Doğrudan yöntem çağırma işlerinin tüm başarısız oluşturma sayısı.|Boyut yok|
|Jobs. createDirectMethodJob. Success|Yes|Yöntem çağırma işlerinin başarılı oluşturmaları|Count|Toplam|Tüm başarılı doğrudan yöntem çağırma işlerinin oluşturulma sayısı.|Boyut yok|
|Jobs. createTwinUpdateJob. Failure|Yes|İkizi Update işlerinin başarısız oluşturmaları|Count|Toplam|İkizi Update işlerinin başarısız olan tüm oluşturma sayısı.|Boyut yok|
|Jobs. createTwinUpdateJob. Success|Yes|İkizi Update işlerinin başarılı oluşturmaları|Count|Toplam|Tüm başarılı ikizi Update işlerinin oluşturma sayısı.|Boyut yok|
|işler. başarısız|Yes|Başarısız işler|Count|Toplam|Başarısız olan tüm işlerin sayısı.|Boyut yok|
|Jobs. listJobs. hata|Yes|İşleri listelemek için başarısız çağrılar|Count|Toplam|Listeleme işleri için başarısız olan tüm çağrıların sayısı.|Boyut yok|
|işler. listJobs. başarılı|Yes|İşleri listelemek için başarılı çağrılar|Count|Toplam|İşleri listelemek için başarılı olan tüm çağrıların sayısı.|Boyut yok|
|Jobs. queryJobs. hata|Yes|Başarısız iş sorguları|Count|Toplam|Sorgu işlerine yapılan tüm başarısız çağrıların sayısı.|Boyut yok|
|işler. queryJobs. başarılı|Yes|Başarılı iş sorguları|Count|Toplam|Sorgu işlerine yapılan tüm başarılı çağrıların sayısı.|Boyut yok|
|RoutingDataSizeInBytesDelivered|Yes|Yönlendirme teslim Iletisi boyutunu bayt cinsinden (Önizleme)|Bayt|Toplam|IoT Hub tarafından bir uç noktaya gönderilen iletilerin bayt cinsinden toplam boyutu. Mesajların farklı uç noktalarınıza teslim edilen bayt boyutunu görüntülemek için EndpointName ve EndpointType boyutlarını kullanabilirsiniz. Ölçüm değeri, ileti birden çok uç noktaya teslim edildiğinde veya ileti aynı uç noktaya birden çok kez teslim edildiğinde de dahil olmak üzere, teslim edilen her ileti için artar.|EndpointType, EndpointName, RoutingSource|
|Routingteslimler|Yes|Yönlendirme teslimleri (Önizleme)|Count|Toplam|IoT Hub yönlendirme kullanan tüm uç noktalara ileti sunmaya kaç kez girişimde denendiğinde. Başarılı veya başarısız denemelerin sayısını görmek için sonuç boyutunu kullanın. Hatalı, bırakılmış veya yalnız bırakılmış gibi başarısızlık nedenini görmek için FailureReasonCategory boyutunu kullanın. Ayrıca, farklı uç noktalarınıza kaç tane ileti teslim edildiğini anlamak için EndpointName ve EndpointType boyutlarını da kullanabilirsiniz. Bir ileti birden çok uç noktaya teslim edildiğinde veya ileti aynı uç noktaya birden çok kez teslim edildiğinde, ölçüm değeri her bir teslim girişimi için bir artırılır.|EndpointType, EndpointName, FailureReasonCategory, result, RoutingSource|
|Routingdeliverygecikme süresi|Yes|Yönlendirme teslimi gecikmesi (Önizleme)|Mayacak|Ortalama|İleti girişi IoT Hub ve telemetri iletisi ile bir uç noktaya giriş arasındaki ortalama gecikme (milisaniye). Farklı uç noktalarınıza gecikme süresini anlamak için EndpointName ve EndpointType boyutlarını kullanabilirsiniz.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|No|Toplam cihaz sayısı|Count|Ortalama|IoT Hub 'ınıza kayıtlı cihazların sayısı|Boyut yok|
|twinQueries. Failure|Yes|Başarısız ikizi sorguları|Count|Toplam|Tüm başarısız ikizi sorgularının sayısı.|Boyut yok|
|twinQueries. resultSize|Yes|İkizi sorguları sonuç boyutu|Bayt|Ortalama|Tüm başarılı ikizi sorgularının sonuç boyutunun ortalama, en düşük ve en yüksek değeri.|Boyut yok|
|twinQueries. Success|Yes|Başarılı ikizi sorguları|Count|Toplam|Tüm başarılı ikizi sorgularının sayısı.|Boyut yok|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AttestationAttempts|Yes|Kanıtlama denemeleri|Count|Toplam|Denenen cihaz belirlediğimizi karşıladığımızı sayısı|ProvisioningServiceName, durum, protokol|
|Deviceasyleri|Yes|Atanan cihazlar|Count|Toplam|IoT Hub 'ına atanan cihazların sayısı|ProvisioningServiceName, IotHubName|
|Registrationdenemeleri|Yes|Kayıt denemeleri|Count|Toplam|Denenen cihaz kaydı sayısı|ProvisioningServiceName, IotHubName, durum|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/Digitaltwınsınstances

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ApiRequests|Yes|API İstekleri|Count|Toplam|Dijital TWINS okuma, yazma, silme ve sorgu işlemleri için yapılan API isteklerinin sayısı.|İşlem, kimlik doğrulama, protokol, StatusCode, StatusCodeClass, StatusText|
|ApiRequestsFailureRate|Yes|API Isteği başarısızlık oranı|Yüzde|Ortalama|Dijital TWINS okuma, yazma, silme ve sorgu işlemleri için bir iç hata (500) yanıt kodu döndüren örneğiniz için hizmetin aldığı API isteklerinin yüzdesi.|İşlem, kimlik doğrulama, protokol|
|Apirequestslatlik|Yes|API Istekleri gecikmesi|Mayacak|Ortalama|API istekleri için yanıt süresi, örneğin Azure dijital TWINS tarafından isteğin ne zaman alındığı, hizmetin dijital TWINS okuma, yazma, silme ve sorgu işlemleri için başarılı/başarısız sonucu göndermesi.|İşlem, kimlik doğrulama, protokol, StatusCode, StatusCodeClass, StatusText|
|BillingApiOperations|Yes|Faturalandırma API 'SI Işlemleri|Count|Toplam|Azure Digital TWINS hizmetinde yapılan tüm API isteklerinin sayısı için faturalandırma ölçümü.|MeterId|
|Billingiletide Işlendi|Yes|Işlenen faturalandırma Iletileri|Count|Toplam|Azure dijital TWINS 'den dış uç noktalara gönderilen ileti sayısı için faturalandırma ölçümü.|MeterId|
|BillingQueryUnits|Yes|Faturalama sorgu birimleri|Count|Toplam|Sorgu birimlerinin sayısı, sorgu yürütmek için tüketilen, hizmet kaynağı kullanımının dahili olarak hesaplanan bir ölçümüdür.|MeterId|
|Inressevents|Yes|Giriş olayları|Count|Toplam|Azure dijital TWINS 'e gelen telemetri olaylarının sayısı.|Sonuç|
|Inresyettsfailurerate|Yes|Giriş olayları başarısızlık oranı|Yüzde|Ortalama|Hizmetin bir iç hata (500) yanıt kodu döndürdüğü gelen telemetri olaylarının yüzdesi.|Boyut yok|
|Ingresseventsla|Yes|Giriş olayları gecikmesi|Mayacak|Ortalama|Bir olayın Azure dijital TWINS tarafından yumurtada ayrılmaya hazırlandığında, hizmetin bir başarı/başarısızlık sonucu gönderdiği zaman.|Sonuç|
|ModelCount|Yes|Model Sayısı|Count|Toplam|Azure dijital TWINS örneğindeki toplam model sayısı. Örnek başına izin verilen maksimum model sayısı için hizmet sınırına yaklaşıp yaklaşmadığını öğrenmek için bu ölçümü kullanın.|Boyut yok|
|Yönlendirme|Yes|Yönlendirilen iletiler|Count|Toplam|Olay Hub 'ı, Service Bus veya Event Grid gibi bir uç nokta Azure hizmetine yönlendirilen ileti sayısı.|EndpointType, sonuç|
|RoutingFailureRate|Yes|Yönlendirme başarısızlık oranı|Yüzde|Ortalama|Azure dijital TWINS 'den, Olay Hub 'ı, Service Bus veya Event Grid gibi bir uç nokta Azure hizmetine yönlendirildikleri için hataya neden olan olayların yüzdesi.|EndpointType|
|RoutingLatency|Yes|Yönlendirme gecikmesi|Mayacak|Ortalama|Azure dijital TWINS 'den, Olay Hub 'ı, Service Bus veya Event Grid gibi bir uç nokta Azure hizmetine gönderildiğinde bir olay arasında geçen süre.|EndpointType, sonuç|
|TwinCount|Yes|İkizi sayısı|Count|Toplam|Azure dijital TWINS örneğindeki toplam TWINS sayısı. Örnek başına izin verilen en fazla sayıda TWINS için hizmet sınırına yaklaşıp yaklaşmadığını öğrenmek için bu ölçümü kullanın.|Boyut yok|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>UmentDB/databaseAccounts Microsoft.Doc

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AddRegion|Yes|Eklenen bölge|Count|Count|Eklenen bölge|Region|
|Oto Scalemaxüretilen Iş|No|Otomatik ölçeklendirme en fazla aktarım hızı|Count|Maksimum|Otomatik ölçeklendirme en fazla aktarım hızı|DatabaseName, CollectionName|
|AvailableStorage|No|kullanım dışı Kullanılabilir depolama alanı|Bayt|Toplam|"Kullanılabilir depolama alanı", Eylül 2023 ' nin sonunda Azure Izleyici 'den kaldırılacak. Cosmos DB koleksiyon depolama boyutu artık sınırsız. Tek kısıtlama, her mantıksal bölüm anahtarı için depolama boyutunun 20GB olduğu bir kısıtlamadır. En üst bölüm anahtarlarına yönelik depolama tüketimini bilmeniz için tanılama günlüğünde PartitionKeyStatistics 'i etkinleştirebilirsiniz. Cosmos DB depolama kotası hakkında daha fazla bilgi için lütfen bu belgeyi kontrol edin https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Kullanımdan kaldırıldıktan sonra, kullanımdan kaldırılan ölçüm üzerinde hala tanımlanmış olan kalan uyarı kuralları otomatik olarak devre dışı bırakılır.|CollectionName, DatabaseName, bölgesi|
|Cassandraconnectionkapanışları|No|Cassandra bağlantı kapanışları|Count|Toplam|1 dakikalık ayrıntı düzeyinde raporlanan, kapatılan Cassandra bağlantısı sayısı|Bölge, ClosureReason|
|Cassandraconnectosgreplicationlatency|No|Cassandra Bağlayıcısı ortalama ReplicationLatency|Mayacak|Ortalama|Cassandra Bağlayıcısı ortalama ReplicationLatency|Boyut yok|
|CassandraConnectorReplicationHealthStatus|No|Cassandra bağlayıcısı çoğaltma sistem durumu|Count|Count|Cassandra bağlayıcısı çoğaltma sistem durumu|NotStarted, Replicationınprogress, hata|
|Cassandrakeyspacecoluştur|No|Cassandra keyspace oluşturuldu|Count|Count|Cassandra keyspace oluşturuldu|Kaynak |
|CassandraKeyspaceDelete|No|Cassandra keyspace silindi|Count|Count|Cassandra keyspace silindi|Kaynak |
|CassandraKeyspaceThroughputUpdate|No|Cassandra keyspace üretilen Işi güncelleştirildi|Count|Count|Cassandra keyspace üretilen Işi güncelleştirildi|Kaynak |
|CassandraKeyspaceUpdate|No|Cassandra keyspace güncelleştirildi|Count|Count|Cassandra keyspace güncelleştirildi|Kaynak |
|Cassandrarequestücretleri|No|Cassandra Istek ücretleri|Count|Toplam|Cassandra istekleri için tüketilen RUs|DatabaseName, CollectionName, bölge, OperationType, ResourceType|
|CassandraRequests|No|Cassandra Istekleri|Count|Count|Yapılan Cassandra isteği sayısı|DatabaseName, CollectionName, bölge, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|No|Cassandra tablosu oluşturuldu|Count|Count|Cassandra tablosu oluşturuldu|ResourceName, ChildResourceName, |
|CassandraTableDelete|No|Cassandra tablosu silindi|Count|Count|Cassandra tablosu silindi|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|No|Cassandra tablosu üretilen Işi güncelleştirildi|Count|Count|Cassandra tablosu üretilen Işi güncelleştirildi|ResourceName, ChildResourceName, |
|CassandraTableUpdate|No|Cassandra tablosu güncelleştirildi|Count|Count|Cassandra tablosu güncelleştirildi|ResourceName, ChildResourceName, |
|CreateAccount|Yes|Hesap oluşturuldu|Count|Count|Hesap oluşturuldu|Boyut yok|
|Veri kullanımı|No|Veri Kullanımı|Bayt|Toplam|5 dakika ayrıntı düzeyinde raporlanan toplam veri kullanımı|CollectionName, DatabaseName, bölgesi|
|Ayrılmış Gatewayrequests|Yes|Ayrılmış Gatewayrequests|Count|Count|Adanmış ağ geçidinde istekler|DatabaseName, CollectionName, Cachesed, OperationName, bölge|
|DeleteAccount|Yes|Hesap silindi|Count|Count|Hesap silindi|Boyut yok|
|DocumentCount|No|Belge sayısı|Count|Toplam|5 dakika ayrıntı düzeyinde raporlanan toplam belge sayısı|CollectionName, DatabaseName, bölgesi|
|DocumentQuota|No|Belge kotası|Bayt|Toplam|5 dakika ayrıntı düzeyinde raporlanan toplam depolama kotası|CollectionName, DatabaseName, bölgesi|
|GremlinDatabaseCreate|No|Gremlin veritabanı oluşturuldu|Count|Count|Gremlin veritabanı oluşturuldu|Kaynak |
|GremlinDatabaseDelete|No|Gremlin veritabanı silindi|Count|Count|Gremlin veritabanı silindi|Kaynak |
|GremlinDatabaseThroughputUpdate|No|Gremlin veritabanı performansı güncelleştirildi|Count|Count|Gremlin veritabanı performansı güncelleştirildi|Kaynak |
|GremlinDatabaseUpdate|No|Gremlin veritabanı güncelleştirildi|Count|Count|Gremlin veritabanı güncelleştirildi|Kaynak |
|GremlinGraphCreate|No|Gremlin grafiği oluşturuldu|Count|Count|Gremlin grafiği oluşturuldu|ResourceName, ChildResourceName, |
|GremlinGraphDelete|No|Gremlin grafiği silindi|Count|Count|Gremlin grafiği silindi|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|No|Gremlin Graf üretilen Işi güncelleştirildi|Count|Count|Gremlin Graf üretilen Işi güncelleştirildi|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|No|Gremlin grafiği güncelleştirildi|Count|Count|Gremlin grafiği güncelleştirildi|ResourceName, ChildResourceName, |
|Indexusage|No|Dizin Kullanımı|Bayt|Toplam|5 dakika ayrıntı düzeyi olarak raporlanan toplam dizin kullanımı|CollectionName, DatabaseName, bölgesi|
|Tümleştirencachetahliye Tedentriessize|No|Tümleştirencachetahliye Tedentriessize|Bayt|Ortalama|Tümleşik önbellekten çıkarılan girişlerin boyutu|CacheType, bölge|
|Integratedcachehitrate|No|Integratedcachehitrate|Yüzde|Ortalama|Tümleşik önbellekler için isabetli önbellek okuması oranı|CacheType, bölge|
|Integratedcachesize|No|Integratedcachesize|Bayt|Ortalama|Adanmış ağ geçidi istekleri için tümleşik önbelleklerin boyutu|CacheType, bölge|
|IntegratedCacheTTLExpirationCount|No|IntegratedCacheTTLExpirationCount|Count|Ortalama|TTL süre sonu nedeniyle tümleşik önbellekten kaldırılan giriş sayısı|CacheType, bölge|
|Meta veri datarequests|No|Meta veri Istekleri|Count|Count|Meta veri isteklerinin sayısı. Cosmos DB, her bir hesap için sistem meta veri toplamayı korur, bu da koleksiyonları, veritabanlarını, vb. ve bunların yapılandırmasını, ücretsiz olarak listeletmenize olanak tanır.|DatabaseName, CollectionName, bölge, StatusCode, |
|MongoCollectionCreate|No|Mongo koleksiyonu oluşturuldu|Count|Count|Mongo koleksiyonu oluşturuldu|ResourceName, ChildResourceName, |
|MongoCollectionDelete|No|Mongo koleksiyonu silindi|Count|Count|Mongo koleksiyonu silindi|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|No|Mongo koleksiyon üretilen Işi güncelleştirildi|Count|Count|Mongo koleksiyon üretilen Işi güncelleştirildi|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|No|Mongo koleksiyonu güncelleştirildi|Count|Count|Mongo koleksiyonu güncelleştirildi|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|No|Mongo veritabanı silindi|Count|Count|Mongo veritabanı silindi|Kaynak |
|MongoDatabaseThroughputUpdate|No|Mongo veritabanı üretilen Işi güncelleştirildi|Count|Count|Mongo veritabanı üretilen Işi güncelleştirildi|Kaynak |
|MongoDBDatabaseCreate|No|Mongo veritabanı oluşturuldu|Count|Count|Mongo veritabanı oluşturuldu|Kaynak |
|MongoDBDatabaseUpdate|No|Mongo veritabanı güncelleştirildi|Count|Count|Mongo veritabanı güncelleştirildi|Kaynak |
|Mongorequestşarj|Yes|Mongo Istek ücreti|Count|Toplam|Tüketilen Mongo Istek birimleri|DatabaseName, CollectionName, Region, CommandName, ErrorCode, durum|
|MongoRequests|Yes|Mongo Istekleri|Count|Count|Yapılan Mongo Isteği sayısı|DatabaseName, CollectionName, Region, CommandName, ErrorCode, durum|
|MongoRequestsCount|No|kullanım dışı Mongo Istek hızı|Sayaçpersaniye|Ortalama|Mongo istek sayısı/saniye|DatabaseName, CollectionName, bölge, hata kodu|
|MongoRequestsDelete|No|kullanım dışı Mongo silme Isteği hızı|Sayaçpersaniye|Ortalama|Mongo silme isteği/saniye|DatabaseName, CollectionName, bölge, hata kodu|
|Mongorequestsınsert|No|kullanım dışı Mongo ekleme Isteği oranı|Sayaçpersaniye|Ortalama|Mongo ekleme sayısı/saniye|DatabaseName, CollectionName, bölge, hata kodu|
|MongoRequestsQuery|No|kullanım dışı Mongo sorgu Isteği hızı|Sayaçpersaniye|Ortalama|Mongo sorgu isteği/saniye|DatabaseName, CollectionName, bölge, hata kodu|
|MongoRequestsUpdate|No|kullanım dışı Mongo güncelleştirme Isteği hızı|Sayaçpersaniye|Ortalama|Saniye başına Mongo güncelleştirme isteği|DatabaseName, CollectionName, bölge, hata kodu|
|Normalizedrutüketim|No|Normalleştirilmiş RU tüketimi|Yüzde|Maksimum|Dakikada maksimum RU tüketim yüzdesi|CollectionName, DatabaseName, Region, Partitionkeyrangeıd|
|Provisionedüretilen Iş|No|Sağlanan İşleme Hızı|Count|Maksimum|Sağlanan İşleme Hızı|DatabaseName, CollectionName|
|RegionFailover|Yes|Yük devredilen bölge|Count|Count|Yük devredilen bölge|Boyut yok|
|RemoveRegion|Yes|Bölge kaldırıldı|Count|Count|Bölge kaldırıldı|Region|
|ReplicationLatency|Yes|P99 çoğaltma gecikmesi|Mayacak|Ortalama|Coğrafi olarak etkinleştirilen hesap için kaynak ve hedef bölgelerde P99 çoğaltma gecikme süresi|SourceRegion, TargetRegion|
|ServerSideLatency|No|Sunucu tarafı gecikme süresi|Mayacak|Ortalama|Sunucu tarafı gecikme süresi|DatabaseName, CollectionName, bölge, ConnectionMode, OperationType, Publicapitürü|
|ServiceAvailability|No|Hizmet kullanılabilirliği|Yüzde|Ortalama|Hesap istekleri bir saat, gün veya ay ayrıntı düzeyinde kullanılabilirlik|Boyut yok|
|SqlContainerCreate|No|Oluşturulan SQL kapsayıcısı|Count|Count|Oluşturulan SQL kapsayıcısı|ResourceName, ChildResourceName, |
|SqlContainerDelete|No|SQL kapsayıcısı silindi|Count|Count|SQL kapsayıcısı silindi|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|No|SQL kapsayıcısı üretilen Işi güncelleştirildi|Count|Count|SQL kapsayıcısı üretilen Işi güncelleştirildi|ResourceName, ChildResourceName, |
|SqlContainerUpdate|No|SQL kapsayıcısı güncelleştirildi|Count|Count|SQL kapsayıcısı güncelleştirildi|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|No|SQL veritabanı oluşturuldu|Count|Count|SQL veritabanı oluşturuldu|Kaynak |
|SqlDatabaseDelete|No|SQL veritabanı silindi|Count|Count|SQL veritabanı silindi|Kaynak |
|SqlDatabaseThroughputUpdate|No|SQL veritabanı verimlilik güncelleştirildi|Count|Count|SQL veritabanı verimlilik güncelleştirildi|Kaynak |
|SqlDatabaseUpdate|No|SQL veritabanı güncelleştirildi|Count|Count|SQL veritabanı güncelleştirildi|Kaynak |
|Tablotablooluştur|No|AzureTable tablo oluşturuldu|Count|Count|AzureTable tablo oluşturuldu|Kaynak |
|TableTableDelete|No|AzureTable tablo silindi|Count|Count|AzureTable tablo silindi|Kaynak |
|TableTableThroughputUpdate|No|AzureTable tablo üretilen Işi güncelleştirildi|Count|Count|AzureTable tablo üretilen Işi güncelleştirildi|Kaynak |
|TableTableUpdate|No|AzureTable tablo güncelleştirildi|Count|Count|AzureTable tablo güncelleştirildi|Kaynak |
|TotalRequests|Yes|Toplam İstek Sayısı|Count|Count|Yapılan istek sayısı|DatabaseName, CollectionName, bölge, StatusCode, OperationType, durum|
|TotalRequestUnits|Yes|Toplam Istek birimleri|Count|Toplam|Tüketilen istek birimleri|DatabaseName, CollectionName, bölge, StatusCode, OperationType, durum|
|UpdateAccountKeys|Yes|Hesap anahtarları güncelleştirildi|Count|Count|Hesap anahtarları güncelleştirildi|Anahtar|
|UpdateAccountNetworkSettings|Yes|Hesap Ağ ayarları güncelleştirildi|Count|Count|Hesap Ağ ayarları güncelleştirildi|Boyut yok|
|UpdateAccountReplicationSettings|Yes|Hesap çoğaltma ayarları güncelleştirildi|Count|Count|Hesap çoğaltma ayarları güncelleştirildi|Boyut yok|
|UpdateDiagnosticsSettings|No|Hesap Tanılama ayarları güncelleştirildi|Count|Count|Hesap Tanılama ayarları güncelleştirildi|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/Domains

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Gelişmiş Filtre değerlendirmeleri|Count|Toplam|Bu konu için olay abonelikleri genelinde değerlendirilen gelişmiş filtrelerin toplam sayısı.|Konu başlığı, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Yes|Kullanılmayan olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam atılacak olay sayısı|Konu başlığı, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|Ityattemptfailcount|No|Teslim başarısız olaylar|Count|Toplam|Bu olay aboneliğine teslim edilemedi toplam olay sayısı|Konu başlığı, EventSubscriptionName, DomainEventSubscriptionName, hata, ErrorType|
|Deliverybaşarılı sayısı|Yes|Teslim edilen olaylar|Count|Toplam|Bu olay aboneliğine teslim edilen toplam olay sayısı|Konu başlığı, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Hedef Işleme süresi|Mayacak|Ortalama|Milisaniye cinsinden hedef işleme süresi|Konu başlığı, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Yes|Bırakılan olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam bırakılan olay sayısı|Konu başlığı, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Eşleşen olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam olay sayısı|Konu başlığı, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Yes|Başarısız olayları Yayımla|Count|Toplam|Bu konuda yayımlanamadı toplam olay sayısı|Konu, ErrorType, hata|
|Publishbaşarılı sayısı|Yes|Yayımlanan olaylar|Count|Toplam|Bu konuda yayınlanan toplam olay sayısı|Konu|
|Publishbaşarıyla Latencınms|Yes|Yayımlama başarı gecikmesi|Mayacak|Toplam|Milisaniye cinsinden yayımlama başarısı gecikmesi|Boyut yok|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/Eventabonelikleri

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Kullanılmayan olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam atılacak olay sayısı|DeadLetterReason|
|Ityattemptfailcount|No|Teslim başarısız olaylar|Count|Toplam|Bu olay aboneliğine teslim edilemedi toplam olay sayısı|Hata, ErrorType|
|Deliverybaşarılı sayısı|Yes|Teslim edilen olaylar|Count|Toplam|Bu olay aboneliğine teslim edilen toplam olay sayısı|Boyut yok|
|DestinationProcessingDurationInMs|No|Hedef Işleme süresi|Mayacak|Ortalama|Milisaniye cinsinden hedef işleme süresi|Boyut yok|
|DroppedEventCount|Yes|Bırakılan olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam bırakılan olay sayısı|DropReason|
|MatchedEventCount|Yes|Eşleşen olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam olay sayısı|Boyut yok|


## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/Extensionkonuları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|Başarısız olayları Yayımla|Count|Toplam|Bu konuda yayımlanamadı toplam olay sayısı|ErrorType, hata|
|Publishbaşarılı sayısı|Yes|Yayımlanan olaylar|Count|Toplam|Bu konuda yayınlanan toplam olay sayısı|Boyut yok|
|Publishbaşarıyla Latencınms|Yes|Yayımlama başarı gecikmesi|Mayacak|Toplam|Milisaniye cinsinden yayımlama başarısı gecikmesi|Boyut yok|
|UnmatchedEventCount|Yes|Eşleşmeyen olaylar|Count|Toplam|Bu konu için olay abonelikleriyle eşleşmeyen toplam olay sayısı|Boyut yok|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Kullanılmayan olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam atılacak olay sayısı|DeadLetterReason, EventSubscriptionName|
|Ityattemptfailcount|No|Teslim başarısız olaylar|Count|Toplam|Bu olay aboneliğine teslim edilemedi toplam olay sayısı|Hata, ErrorType, EventSubscriptionName|
|Deliverybaşarılı sayısı|Yes|Teslim edilen olaylar|Count|Toplam|Bu olay aboneliğine teslim edilen toplam olay sayısı|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Hedef Işleme süresi|Mayacak|Ortalama|Milisaniye cinsinden hedef işleme süresi|EventSubscriptionName|
|DroppedEventCount|Yes|Bırakılan olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam bırakılan olay sayısı|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Eşleşen olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam olay sayısı|EventSubscriptionName|
|PublishFailCount|Yes|Başarısız olayları Yayımla|Count|Toplam|Bu konuda yayımlanamadı toplam olay sayısı|ErrorType, hata|
|Publishbaşarılı sayısı|Yes|Yayımlanan olaylar|Count|Toplam|Bu konuda yayınlanan toplam olay sayısı|Boyut yok|
|Publishbaşarıyla Latencınms|Yes|Yayımlama başarı gecikmesi|Mayacak|Toplam|Milisaniye cinsinden yayımlama başarısı gecikmesi|Boyut yok|
|UnmatchedEventCount|Yes|Eşleşmeyen olaylar|Count|Toplam|Bu konu için olay abonelikleriyle eşleşmeyen toplam olay sayısı|Boyut yok|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/Partnerkonuları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Gelişmiş Filtre değerlendirmeleri|Count|Toplam|Bu konu için olay abonelikleri genelinde değerlendirilen gelişmiş filtrelerin toplam sayısı.|EventSubscriptionName|
|DeadLetteredCount|Yes|Kullanılmayan olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam atılacak olay sayısı|DeadLetterReason, EventSubscriptionName|
|Ityattemptfailcount|No|Teslim başarısız olaylar|Count|Toplam|Bu olay aboneliğine teslim edilemedi toplam olay sayısı|Hata, ErrorType, EventSubscriptionName|
|Deliverybaşarılı sayısı|Yes|Teslim edilen olaylar|Count|Toplam|Bu olay aboneliğine teslim edilen toplam olay sayısı|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Hedef Işleme süresi|Mayacak|Ortalama|Milisaniye cinsinden hedef işleme süresi|EventSubscriptionName|
|DroppedEventCount|Yes|Bırakılan olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam bırakılan olay sayısı|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Eşleşen olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam olay sayısı|EventSubscriptionName|
|PublishFailCount|Yes|Başarısız olayları Yayımla|Count|Toplam|Bu konuda yayımlanamadı toplam olay sayısı|ErrorType, hata|
|Publishbaşarılı sayısı|Yes|Yayımlanan olaylar|Count|Toplam|Bu konuda yayınlanan toplam olay sayısı|Boyut yok|
|UnmatchedEventCount|Yes|Eşleşmeyen olaylar|Count|Toplam|Bu konu için olay abonelikleriyle eşleşmeyen toplam olay sayısı|Boyut yok|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/Systemkonuları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Gelişmiş Filtre değerlendirmeleri|Count|Toplam|Bu konu için olay abonelikleri genelinde değerlendirilen gelişmiş filtrelerin toplam sayısı.|EventSubscriptionName|
|DeadLetteredCount|Yes|Kullanılmayan olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam atılacak olay sayısı|DeadLetterReason, EventSubscriptionName|
|Ityattemptfailcount|No|Teslim başarısız olaylar|Count|Toplam|Bu olay aboneliğine teslim edilemedi toplam olay sayısı|Hata, ErrorType, EventSubscriptionName|
|Deliverybaşarılı sayısı|Yes|Teslim edilen olaylar|Count|Toplam|Bu olay aboneliğine teslim edilen toplam olay sayısı|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Hedef Işleme süresi|Mayacak|Ortalama|Milisaniye cinsinden hedef işleme süresi|EventSubscriptionName|
|DroppedEventCount|Yes|Bırakılan olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam bırakılan olay sayısı|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Eşleşen olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam olay sayısı|EventSubscriptionName|
|PublishFailCount|Yes|Başarısız olayları Yayımla|Count|Toplam|Bu konuda yayımlanamadı toplam olay sayısı|ErrorType, hata|
|Publishbaşarılı sayısı|Yes|Yayımlanan olaylar|Count|Toplam|Bu konuda yayınlanan toplam olay sayısı|Boyut yok|
|Publishbaşarıyla Latencınms|Yes|Yayımlama başarı gecikmesi|Mayacak|Toplam|Milisaniye cinsinden yayımlama başarısı gecikmesi|Boyut yok|
|UnmatchedEventCount|Yes|Eşleşmeyen olaylar|Count|Toplam|Bu konu için olay abonelikleriyle eşleşmeyen toplam olay sayısı|Boyut yok|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/konuları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Gelişmiş Filtre değerlendirmeleri|Count|Toplam|Bu konu için olay abonelikleri genelinde değerlendirilen gelişmiş filtrelerin toplam sayısı.|EventSubscriptionName|
|DeadLetteredCount|Yes|Kullanılmayan olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam atılacak olay sayısı|DeadLetterReason, EventSubscriptionName|
|Ityattemptfailcount|No|Teslim başarısız olaylar|Count|Toplam|Bu olay aboneliğine teslim edilemedi toplam olay sayısı|Hata, ErrorType, EventSubscriptionName|
|Deliverybaşarılı sayısı|Yes|Teslim edilen olaylar|Count|Toplam|Bu olay aboneliğine teslim edilen toplam olay sayısı|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Hedef Işleme süresi|Mayacak|Ortalama|Milisaniye cinsinden hedef işleme süresi|EventSubscriptionName|
|DroppedEventCount|Yes|Bırakılan olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam bırakılan olay sayısı|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Eşleşen olaylar|Count|Toplam|Bu olay aboneliğiyle eşleşen toplam olay sayısı|EventSubscriptionName|
|PublishFailCount|Yes|Başarısız olayları Yayımla|Count|Toplam|Bu konuda yayımlanamadı toplam olay sayısı|ErrorType, hata|
|Publishbaşarılı sayısı|Yes|Yayımlanan olaylar|Count|Toplam|Bu konuda yayınlanan toplam olay sayısı|Boyut yok|
|Publishbaşarıyla Latencınms|Yes|Yayımlama başarı gecikmesi|Mayacak|Toplam|Milisaniye cinsinden yayımlama başarısı gecikmesi|Boyut yok|
|UnmatchedEventCount|Yes|Eşleşmeyen olaylar|Count|Toplam|Bu konu için olay abonelikleriyle eşleşmeyen toplam olay sayısı|Boyut yok|


## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/kümeler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Count|Ortalama|Microsoft. EventHub için toplam etkin bağlantı sayısı.||
|Kullanılabilirlik Blememory|No|Kullanılabilir Bellek|Yüzde|Maksimum|Toplam belleğin yüzdesi olarak olay hub 'ı kümesi için kullanılabilir bellek.|Rol|
|CaptureBacklog|No|Biriktirme listesini yakala.|Count|Toplam|Microsoft. EventHub için yakalama biriktirme listesi.||
|CapturedBytes|No|Yakalanan baytlar.|Bayt|Toplam|Microsoft. EventHub için yakalanan baytlar.||
|CapturedMessages|No|Yakalanan Iletiler.|Count|Toplam|Microsoft. EventHub için yakalanan Iletiler.||
|ConnectionsClosed|No|Kapatılan Bağlantılar.|Count|Ortalama|Microsoft. EventHub için kapatılan bağlantılar.||
|ConnectionsOpened|No|Açılan Bağlantılar.|Count|Ortalama|Microsoft. EventHub için açılan bağlantılar.||
|CPU|No|CPU|Yüzde|Maksimum|Bir yüzde olarak olay hub 'ı kümesine yönelik CPU kullanımı|Rol|
|Incomingbytes|Yes|Gelen Bayt Miktarı|Bayt|Toplam|Microsoft. EventHub için gelen baytlar.||
|Incomingmessages|Yes|Gelen İletiler|Count|Toplam|Microsoft. EventHub için gelen Iletiler.||
|Incomingrequests|Yes|Gelen İstekler|Count|Toplam|Microsoft. EventHub için gelen Istekler.||
|OutgoingBytes|Yes|Giden bayt sayısı.|Bayt|Toplam|Microsoft. EventHub için giden baytlar.||
|OutgoingMessages|Yes|Giden İletiler|Count|Toplam|Microsoft. EventHub için giden Iletiler.||
|QuotaExceededErrors|No|Kota Aşıldı Hataları.|Count|Toplam|Microsoft. EventHub için Kota aşıldı.|OperationResult|
|ServerErrors|No|Sunucu Hataları.|Count|Toplam|Microsoft. EventHub için sunucu hataları.|OperationResult|
|Boyut|No|Boyut|Bayt|Ortalama|Bir EventHub 'ın bayt cinsinden boyutu.|Rol|
|SuccessfulRequests|No|Başarılı İstekler|Count|Toplam|Microsoft. EventHub için başarılı Istekler.|OperationResult|
|OperationName|No|Kısıtlanan İstekler.|Count|Toplam|Microsoft. EventHub için kısıtlanan Istekler.|OperationResult|
|UserErrors|No|Kullanıcı Hataları.|Count|Toplam|Microsoft. EventHub için Kullanıcı hataları.|OperationResult|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/ad alanları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Count|Ortalama|Microsoft. EventHub için toplam etkin bağlantı sayısı.||
|CaptureBacklog|No|Biriktirme listesini yakala.|Count|Toplam|Microsoft. EventHub için yakalama biriktirme listesi.|EntityName|
|CapturedBytes|No|Yakalanan baytlar.|Bayt|Toplam|Microsoft. EventHub için yakalanan baytlar.|EntityName|
|CapturedMessages|No|Yakalanan Iletiler.|Count|Toplam|Microsoft. EventHub için yakalanan Iletiler.|EntityName|
|ConnectionsClosed|No|Kapatılan Bağlantılar.|Count|Ortalama|Microsoft. EventHub için kapatılan bağlantılar.|EntityName|
|ConnectionsOpened|No|Açılan Bağlantılar.|Count|Ortalama|Microsoft. EventHub için açılan bağlantılar.|EntityName|
|EHABL|Yes|Biriktirme listesi iletilerini Arşivle (kullanım dışı)|Count|Toplam|Ad alanı için biriktirme listesindeki Olay Hub 'ı arşiv iletileri (kullanım dışı)||
|EHAMB|Yes|Arşiv iletisi verimlilik (kullanım dışı)|Bayt|Toplam|Bir ad alanında olay hub 'ı arşivlenmiş ileti işleme (kullanım dışı)||
|EHAMESAJ|Yes|İletileri Arşivle (kullanım dışı)|Count|Toplam|Bir ad alanında olay hub 'ı arşivlenmiş iletiler (kullanım dışı)||
|EHINBYTES|Yes|Gelen baytlar (kullanım dışı)|Bayt|Toplam|Ad alanı için Olay Hub 'ı gelen ileti işleme (kullanım dışı)||
|EHINMB|Yes|Gelen bayt (geçersiz) (kullanım dışı)|Bayt|Toplam|Bir ad alanı için Olay Hub 'ı gelen ileti işleme. Bu ölçüm kullanım dışıdır. Lütfen bunun yerine gelen bayt ölçümünü kullanın (kullanım dışı)||
|EHINILETISI|Yes|Gelen Iletiler (kullanım dışı)|Count|Toplam|Bir ad alanı için toplam gelen ileti (kullanım dışı)||
|EHOUTBYTES|Yes|Giden baytlar (kullanım dışı)|Bayt|Toplam|Bir ad alanı için Olay Hub 'ı giden ileti işleme (kullanım dışı)||
|EHOUTMB|Yes|Giden baytlar (eski) (kullanım dışı)|Bayt|Toplam|Bir ad alanı için Olay Hub 'ı giden ileti işleme. Bu ölçüm kullanım dışıdır. Lütfen bunun yerine giden bayt ölçümünü kullanın (kullanım dışı)||
|EHODıKILETISI|Yes|Giden Iletiler (kullanım dışı)|Count|Toplam|Bir ad alanı için toplam giden ileti (kullanım dışı)||
|FAILREQ|Yes|Başarısız Istekler (kullanım dışı)|Count|Toplam|Bir ad alanı için toplam başarısız istek (kullanım dışı)||
|Incomingbytes|Yes|Gelen Bayt Miktarı|Bayt|Toplam|Microsoft. EventHub için gelen baytlar.|EntityName|
|Incomingmessages|Yes|Gelen İletiler|Count|Toplam|Microsoft. EventHub için gelen Iletiler.|EntityName|
|Incomingrequests|Yes|Gelen İstekler|Count|Toplam|Microsoft. EventHub için gelen Istekler.|EntityName|
|Giriş iletileri|Yes|Gelen Iletiler (eski) (kullanım dışı)|Count|Toplam|Bir ad alanı için gelen toplam ileti sayısı. Bu ölçüm kullanım dışıdır. Lütfen bunun yerine gelen Iletiler ölçümünü kullanın (kullanım dışı)||
|INREQS|Yes|Gelen Istekler (kullanım dışı)|Count|Toplam|Bir ad alanı için toplam gelen gönderme isteği sayısı (kullanım dışı)||
|INTERR|Yes|İç sunucu hataları (kullanım dışı)|Count|Toplam|Bir ad alanı için toplam iç sunucu hatası (kullanım dışı)||
|Hatalı cerr|Yes|Diğer hatalar (kullanım dışı)|Count|Toplam|Bir ad alanı için toplam başarısız istek (kullanım dışı)||
|OutgoingBytes|Yes|Giden bayt sayısı.|Bayt|Toplam|Microsoft. EventHub için giden baytlar.|EntityName|
|OutgoingMessages|Yes|Giden İletiler|Count|Toplam|Microsoft. EventHub için giden Iletiler.|EntityName|
|Dış mesaj|Yes|Giden Iletiler (eski) (kullanım dışı)|Count|Toplam|Bir ad alanı için toplam giden ileti. Bu ölçüm kullanım dışıdır. Lütfen bunun yerine giden Iletiler ölçümünü kullanın (kullanım dışı)||
|QuotaExceededErrors|No|Kota Aşıldı Hataları.|Count|Toplam|Microsoft. EventHub için Kota aşıldı.|EntityName, OperationResult|
|ServerErrors|No|Sunucu Hataları.|Count|Toplam|Microsoft. EventHub için sunucu hataları.|EntityName, OperationResult|
|Boyut|No|Boyut|Bayt|Ortalama|Bir EventHub 'ın bayt cinsinden boyutu.|EntityName|
|SuccessfulRequests|No|Başarılı İstekler|Count|Toplam|Microsoft. EventHub için başarılı Istekler.|EntityName, OperationResult|
|SUCCREQ|Yes|Başarılı Istekler (kullanım dışı)|Count|Toplam|Bir ad alanı için toplam başarılı istek (kullanım dışı)||
|SVRBSY|Yes|Sunucu meşgul hataları (kullanım dışı)|Count|Toplam|Bir ad alanı için toplam sunucu meşgul hatası (kullanım dışı)||
|OperationName|No|Kısıtlanan İstekler.|Count|Toplam|Microsoft. EventHub için kısıtlanan Istekler.|EntityName, OperationResult|
|UserErrors|No|Kullanıcı Hataları.|Count|Toplam|Microsoft. EventHub için Kullanıcı hataları.|EntityName, OperationResult|


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/kümeler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kategorizedgatewayrequests|Yes|Kategorilere ayrılmış ağ geçidi Istekleri|Count|Toplam|Kategorilere göre ağ geçidi isteklerinin sayısı (1xx/2xx/3xx/4xx/5xx)|Http durumu|
|GatewayRequests|Yes|Ağ Geçidi Istekleri|Count|Toplam|Ağ Geçidi isteklerinin sayısı|Http durumu|
|KafkaRestProxy.ConsumerRequest.m1_delta|Yes|REST proxy tüketicisi Istek Işleme|Sayaçpersaniye|Toplam|Kafka REST proxy 'sine yönelik tüketici isteği sayısı|Makine, konu|
|KafkaRestProxy.ConsumerRequestFail.m1_delta|Yes|REST proxy tüketicisi başarısız Istekleri|Sayaçpersaniye|Toplam|Tüketici isteği özel durumları|Makine, konu|
|KafkaRestProxy. ConsumerRequestTime. P95|Yes|REST proxy tüketicisi RequestLatency|Mayacak|Ortalama|Kafka REST proxy aracılığıyla bir tüketici isteğinde ileti gecikmesi|Makine, konu|
|KafkaRestProxy. Consumerrequestwaitingınqueuetime. P95|Yes|REST proxy tüketicisi Istek biriktirme listesi|Mayacak|Ortalama|Tüketici REST ara sunucu kuyruğu uzunluğu|Makine, konu|
|KafkaRestProxy.MessagesIn.m1_delta|Yes|REST proxy üreticisi Messageüretilen Işi|Sayaçpersaniye|Toplam|Kafka REST proxy aracılığıyla üretici iletilerinin sayısı|Makine, konu|
|KafkaRestProxy.MessagesOut.m1_delta|Yes|REST proxy tüketicisi Ileti Işleme|Sayaçpersaniye|Toplam|Kafka REST proxy aracılığıyla tüketici iletisi sayısı|Makine, konu|
|KafkaRestProxy. OpenConnections|Yes|REST proxy ConcurrentConnections|Count|Toplam|Kafka REST proxy aracılığıyla eşzamanlı bağlantı sayısı|Makine, konu|
|KafkaRestProxy.ProducerRequest.m1_delta|Yes|REST proxy üreticisi Requestverimlilik|Sayaçpersaniye|Toplam|Kafka REST proxy 'sine yönelik üretici isteği sayısı|Makine, konu|
|KafkaRestProxy.ProducerRequestFail.m1_delta|Yes|REST proxy üreticisi başarısız Istekleri|Sayaçpersaniye|Toplam|Üretici isteği özel durumları|Makine, konu|
|KafkaRestProxy.ProducerRequestTime.p95|Yes|REST proxy üreticisi RequestLatency|Mayacak|Ortalama|Kafka REST proxy aracılığıyla bir üretici isteğinde ileti gecikmesi|Makine, konu|
|KafkaRestProxy.ProducerRequestWaitingInQueueTime.p95|Yes|REST proxy üreticisi Istek biriktirme listesi|Mayacak|Ortalama|Üretici REST proxy sırası uzunluğu|Makine, konu|
|Numactiveçalışanları|Yes|Etkin çalışan sayısı|Count|Maksimum|Etkin çalışan sayısı|MetricName|


## <a name="microsofthealthcareapisservices"></a>Microsoft. Healthgelişme API 'leri/Hizmetleri

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik|Yes|Kullanılabilirlik|Yüzde|Ortalama|Hizmetin kullanılabilirlik oranı.|Boyut yok|
|CosmosDbCollectionSize|Yes|Cosmos DB koleksiyonu boyutu|Bayt|Toplam|Yedekleme Cosmos DB koleksiyonunun bayt cinsinden boyutu.|Boyut yok|
|CosmosDbIndexSize|Yes|Cosmos DB Dizin boyutu|Bayt|Toplam|Yedekleme Cosmos DB koleksiyonun dizininin bayt cinsinden boyutu.|Boyut yok|
|Cosmosdbrequestücret|Yes|Cosmos DB RU kullanımı|Count|Toplam|Hizmetin yedekleme Cosmos DB isteklerin RU kullanımı.|İşlem, ResourceType|
|CosmosDbRequests|Yes|Hizmet Cosmos DB istekleri|Count|Sum|Bir hizmetin yedekleme Cosmos DB yapılan isteklerin toplam sayısı.|İşlem, ResourceType|
|Cosmosdbkısıtlar Lerate|Yes|Hizmet Cosmos DB azaltma oranı|Count|Sum|Bir hizmetin yedekleme Cosmos DB Toplam 429 yanıt sayısıdır.|İşlem, ResourceType|
|Iotconnectordeviceevent|Yes|Gelen Ileti sayısı|Count|Sum|Tüm normalleştirmadan önce FHıR için Azure IoT Bağlayıcısı tarafından alınan toplam ileti sayısı.|İşlem, ConnectorName|
|Iotconnectordeviceeventprocessinglatnms|Yes|Ortalama Normalleştir aşaması gecikmesi|Mayacak|Ortalama|Bir olayın alma süresi ile olayın normalleştirilmesi için işlendiği süre arasındaki ortalama süre.|İşlem, ConnectorName|
|Iotconnectorölçüm|Yes|Ölçüm sayısı|Count|Sum|FHıR için Azure IoT bağlayıcısının FHıR dönüştürme aşamasına göre alınan normalleştirilmiş değer okumalar sayısı.|İşlem, ConnectorName|
|Iotconnectormeasurementgroup|Yes|Ileti grubu sayısı|Count|Sum|Tür, cihaz, hasta ve yapılandırılmış zaman dilimi genelinde toplam ölçüm Gruplandırmaların, FHıR dönüştürme aşamasına göre oluşturulan toplam sayısı.|İşlem, ConnectorName|
|Iotconnectormeasurementingestionlatlarms|Yes|Ortalama Grup aşaması gecikmesi|Mayacak|Ortalama|IoT bağlayıcısının cihaz verilerini aldığı ve verilerin FHıR dönüştürme aşamasına göre işlendiği zaman arasındaki süre.|İşlem, ConnectorName|
|Iotconnectornormalizedevent|Yes|Normalleştirilmiş Ileti sayısı|Count|Sum|FHıR için Azure IoT bağlayıcısının normalleştirme aşamasından alınan, eşlenen normalleştirilmiş değerlerin toplam sayısı.|İşlem, ConnectorName|
|Iotconnectortotalerrors|Yes|Toplam hata sayısı|Count|Sum|FHıR için Azure IoT Bağlayıcısı tarafından günlüğe kaydedilen toplam hata sayısı|Ad, Işlem, ErrorType, Errorönem derecesi, ConnectorName|
|ServiceApiErrors|Yes|Hizmet hataları|Count|Sum|Hizmet tarafından oluşturulan iç sunucu hatalarının toplam sayısı.|Protokol, kimlik doğrulama, Işlem, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|Hizmet Apilatesi|Yes|Hizmet gecikmesi|Mayacak|Ortalama|Hizmetin yanıt gecikmesi.|Protokol, kimlik doğrulama, Işlem, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|Hizmet Apirequests|Yes|Service Requests|Count|Sum|Hizmet tarafından alınan isteklerin toplam sayısı.|Protokol, kimlik doğrulama, Işlem, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|Toplam hata sayısı|Yes|Toplam Hata Sayısı|Count|Sum|Hizmet tarafından karşılaşılan iç sunucu hatalarının toplam sayısı.|Protokol, StatusCode, StatusCodeClass, StatusCodeText|
|TotalLatency|Yes|Toplam gecikme süresi|Mayacak|Ortalama|Hizmetin yanıt gecikmesi.|Protokol|
|TotalRequests|Yes|Toplam İstek Sayısı|Count|Sum|Hizmet tarafından alınan isteklerin toplam sayısı.|Protokol|


## <a name="microsofthybridnetworknetworkfunctions"></a>Microsoft. hybridnetwork/networkfunctions

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Hypervvirtualprocessorkullanım|Yes|Ortalama CPU Kullanımı|Yüzde|Ortalama|Tek bir dakika aralığında sanal CPU kullanımının toplam ortalama yüzdesi. Sanal CPU 'nun toplam sayısı, SKU tanımında Kullanıcı tarafından yapılandırılan değeri temel alır. Daha fazla filtre, SKU 'da tanımlanan RoleName temelinde uygulanabilir.|InstanceName|


## <a name="microsofthybridnetworkvirtualnetworkfunctions"></a>Microsoft. hybridnetwork/virtualnetworkfunctions

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Hypervvirtualprocessorkullanım|Yes|Ortalama CPU Kullanımı|Yüzde|Ortalama|Tek bir dakika aralığında sanal CPU kullanımının toplam ortalama yüzdesi. Sanal CPU 'nun toplam sayısı, SKU tanımında Kullanıcı tarafından yapılandırılan değeri temel alır. Daha fazla filtre, SKU 'da tanımlanan RoleName temelinde uygulanabilir.|InstanceName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/oto scalesettings

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|MetricThreshold|Yes|Ölçüm eşiği|Count|Ortalama|Otomatik ölçeklendirme çalıştırıldığında yapılandırılan otomatik ölçeklendirme eşiği.|MetricTriggerRule|
|ObservedCapacity|Yes|Gözlenen kapasite|Count|Ortalama|Yürütüldüğünde otomatik ölçeklendirme olarak bildirilen kapasite.||
|ObservedMetricValue|Yes|Gözlemlenen ölçüm değeri|Count|Ortalama|Yürütüldüğünde otomatik ölçeklendirme tarafından hesaplanan değer|MetricTriggerSource|
|Scaleactionsınıated|Yes|Ölçek eylemleri başlatıldı|Count|Toplam|Ölçek işleminin yönü.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/bileşenler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik sonuçları/kullanılabilirliği yüzdesi|Yes|Kullanılabilirlik|Yüzde|Ortalama|Başarıyla tamamlanan kullanılabilirlik testlerinin yüzdesi|Kullanılabilirlik sonucu/adı, kullanılabilirlik sonucu/konumu|
|Kullanılabilirlik sonuçları/sayısı|No|Kullanılabilirlik testleri|Count|Count|Kullanılabilirlik testlerinin sayısı|Kullanılabilirlik sonucu/adı, kullanılabilirlik sonucu/konumu, kullanılabilirlik sonucu/başarısı|
|Kullanılabilirlik sonuçları/süresi|Yes|Kullanılabilirlik testi süresi|Mayacak|Ortalama|Kullanılabilirlik testi süresi|Kullanılabilirlik sonucu/adı, kullanılabilirlik sonucu/konumu, kullanılabilirlik sonucu/başarısı|
|Browserzamanlamalar/networkDuration|Yes|Sayfa yükleme ağ bağlantı süresi|Mayacak|Ortalama|Kullanıcı isteği ile ağ bağlantısı arasındaki süre. DNS arama ve aktarım bağlantısı içerir.|Boyut yok|
|Browserzamanlamalar/processingDuration|Yes|İstemci işlem süresi|Mayacak|Ortalama|DOM yükleninceye kadar belgenin son baytını alma arasındaki süre. Zaman uyumsuz istekler hala işlenmeye devam edebilir.|Boyut yok|
|Browserzamanlamalar/receiveDuration|Yes|Yanıt süresini alma|Mayacak|Ortalama|İlk ve son bayt veya bağlantının kesilmesi arasındaki süre.|Boyut yok|
|Browserzamanlamalar/sendDuration|Yes|İstek gönderme süresi|Mayacak|Ortalama|Ağ bağlantısı ve ilk baytın alınması arasındaki süre.|Boyut yok|
|Browserzamanlamalar/totalDuration|Yes|Tarayıcı sayfa yükleme süresi|Mayacak|Ortalama|DOM, stil sayfaları, betikler ve görüntüler yükleninceye kadar Kullanıcı isteğinden geçen süre.|Boyut yok|
|Bağımlılıklar/sayı|No|Bağımlılık çağrıları|Count|Count|Uygulama tarafından dış kaynaklara yapılan çağrıların sayısı.|bağımlılık/tür, bağımlılık/performanceBucket, bağımlılık/başarı, bağımlılık/hedef, bağımlılık/resultCode, işlem/yapay, Cloud/Roleınstance, Cloud/roleName|
|Bağımlılıklar/süre|Yes|Bağımlılık süresi|Mayacak|Ortalama|Uygulama tarafından dış kaynaklara yapılan çağrıların süresi.|bağımlılık/tür, bağımlılık/performanceBucket, bağımlılık/başarı, bağımlılık/hedef, bağımlılık/resultCode, işlem/yapay, Cloud/Roleınstance, Cloud/roleName|
|Bağımlılıklar/başarısız|No|Bağımlılık çağrısı sorunları|Count|Count|Uygulama tarafından dış kaynaklara yapılan başarısız bağımlılık çağrılarının sayısı.|bağımlılık/tür, bağımlılık/performanceBucket, bağımlılık/hedef, bağımlılık/resultCode, işlem/yapay, Cloud/Roleınstance, Cloud/roleName|
|özel durumlar/tarayıcı|No|Tarayıcı özel durumları|Count|Count|Tarayıcıda oluşturulan yakalanamayan özel durumların sayısı.|bulut/roleName|
|özel durumlar/say|Yes|Özel durumlar|Count|Count|Tüm yakalanamayan özel durumların Birleşik sayısı.|bulut/rol adı, bulut/Roleınstance, istemci/tür|
|özel durumlar/sunucu|No|Sunucu özel durumları|Count|Count|Sunucu uygulamasında oluşturulan yakalanamayan özel durum sayısı.|Cloud/roleName, Cloud/Roleınstance|
|pageViews/Count|Yes|Sayfa görünümleri|Count|Count|Sayfa görüntüleme sayısı.|işlem/yapay, bulut/roleName|
|pageViews/Duration|Yes|Sayfa görünümü yükleme süresi|Mayacak|Ortalama|Sayfa görünümü yükleme süresi|işlem/yapay, bulut/roleName|
|performanceCounters/exceptionsPerSecond|Yes|Özel durum oranı|Sayaçpersaniye|Ortalama|.NET özel durumları ve .NET özel durumlarına dönüştürülmüş yönetilmeyen özel durumlar da dahil olmak üzere, Windows 'a bildirilen işlenmiş ve işlenmemiş özel durumların sayısı.|Cloud/Roleınstance|
|performanceCounters/memoryAvailableBytes|Yes|Kullanılabilir bellek|Bayt|Ortalama|Bir işleme veya sistem kullanımına ayırmak için hemen kullanılabilir fiziksel bellek.|Cloud/Roleınstance|
|performanceCounters/processCpuPercentage|Yes|İşlem CPU 'SU|Yüzde|Ortalama|Tüm işlem iş parçacıklarının yönergeleri yürütmek için işlemciyi kullandığı geçen sürenin yüzdesi. Bu, 0 ila 100 arasında farklılık gösterebilir. Bu ölçüm, W3wp sürecinin performansını tek başına gösterir.|Cloud/Roleınstance|
|performanceCounters/Processıobitespersecond|Yes|İşlem GÇ oranı|BytesPerSecond|Ortalama|Dakikada, ağda ve cihazlarda okunan ve yazılan toplam bayt/saniye.|Cloud/Roleınstance|
|performanceCounters/Processorcpuyüzdesi|Yes|İşlemci zamanı|Yüzde|Ortalama|İşlemcinin boş olmayan iş parçacıklarında harcadığı sürenin yüzdesi.|Cloud/Roleınstance|
|performanceCounters/processPrivateBytes|Yes|İşlem özel baytları|Bayt|Ortalama|İzlenen uygulamanın işlemlerine özel olarak atanan bellek.|Cloud/Roleınstance|
|performanceCounters/requestExecutionTime|Yes|HTTP isteği yürütme süresi|Mayacak|Ortalama|En son isteğin yürütme süresi.|Cloud/Roleınstance|
|performanceCounters/Requestsınqueue|Yes|Uygulama kuyruğundaki HTTP istekleri|Count|Ortalama|Uygulama isteği sırasının uzunluğu.|Cloud/Roleınstance|
|performanceCounters/requestsPerSecond|Yes|HTTP istek hızı|Sayaçpersaniye|Ortalama|ASP.NET ' den saniyede uygulamaya yapılan tüm isteklerin oranı.|Cloud/Roleınstance|
|istek/sayı|No|Sunucu istekleri|Count|Count|Tamamlanan HTTP isteği sayısı.|istek/performanceBucket, istek/resultCode, işlem/yapay, Cloud/Roleınstance, istek/başarı, bulut/roleName|
|istek/süre|Yes|Sunucu yanıt süresi|Mayacak|Ortalama|Bir HTTP isteği alma ve yanıtın gönderilmesini tamamlama arasındaki süre.|istek/performanceBucket, istek/resultCode, işlem/yapay, Cloud/Roleınstance, istek/başarı, bulut/roleName|
|istek/başarısız|No|Başarısız istekler|Count|Count|Başarısız olarak işaretlenen HTTP isteklerinin sayısı. Çoğu durumda bunlar >= 400 yanıt koduna sahip isteklerdir ve 401 ' e eşit değildir.|istek/performanceBucket, istek/resultCode, işlem/yapay, Cloud/Roleınstance, Cloud/roleName|
|istek/hız|No|Sunucu isteği hızı|Sayaçpersaniye|Ortalama|Saniye başına sunucu isteği oranı|istek/performanceBucket, istek/resultCode, işlem/yapay, Cloud/Roleınstance, istek/başarı, bulut/roleName|
|izlemeler/say|Yes|İzlemeler|Count|Count|İzleme belgesi sayısı|izleme/severityLevel, işlem/yapay, Cloud/roleName, Cloud/Roleınstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. ıotcentral/ıotapps

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|C2D. Commands. Failure|Yes|Başarısız komut çağırmaları|Count|Toplam|IoT Central başlatılan tüm başarısız komut isteklerinin sayısı|Boyut yok|
|C2D. Commands. requestSize|Yes|Komut etkinleştirmeleri istek boyutu|Bayt|Toplam|IoT Central başlatılan tüm komut isteklerinin istek boyutu|Boyut yok|
|C2D. Commands. responseSize|Yes|Komut etkinleştirmeleri yanıt boyutu|Bayt|Toplam|IoT Central başlatılan tüm komut yanıtlarının yanıt boyutu|Boyut yok|
|C2D. Commands. Success|Yes|Başarılı komut çağırmaları|Count|Toplam|IoT Central başlatılan tüm başarılı komut isteklerinin sayısı|Boyut yok|
|C2D. Property. Read. Failure|Yes|IoT Central 'den cihaz özelliği okumaları başarısız oldu|Count|Toplam|IoT Central başlatılan tüm başarısız Özellik okuma sayısı|Boyut yok|
|C2D. Property. Read. Success|Yes|IoT Central 'den başarılı cihaz özelliği okumaları|Count|Toplam|IoT Central başlatılan tüm başarılı Özellik okuma sayısı|Boyut yok|
|C2D. Property. Update. Failure|Yes|IoT Central cihaz özelliği güncelleştirmeleri başarısız oldu|Count|Toplam|IoT Central tarafından başlatılan tüm başarısız özellik güncelleştirmelerinin sayısı|Boyut yok|
|C2D. Property. Update. Success|Yes|IoT Central 'den başarılı cihaz özelliği güncelleştirmeleri|Count|Toplam|IoT Central başlatıldığı tüm başarılı özellik güncelleştirmeleri sayısı|Boyut yok|
|connectedDeviceCount|No|Toplam bağlı cihaz sayısı|Count|Ortalama|IoT Central bağlı cihazların sayısı|Boyut yok|
|D2C. Property. Read. Failure|Yes|Cihazlardan cihaz özelliği okumaları başarısız oldu|Count|Toplam|Cihazlardan başlatılan tüm başarısız Özellik okuma sayısı|Boyut yok|
|D2C. Property. Read. Success|Yes|Cihazlardan başarılı cihaz özelliği okumaları|Count|Toplam|Cihazlardan başlatılan tüm başarılı Özellik okuma sayısı|Boyut yok|
|D2C. Property. Update. Failure|Yes|Cihazlardan cihaz özelliği güncelleştirmeleri başarısız oldu|Count|Toplam|Cihazlardan başlatılan tüm başarısız özellik güncelleştirmelerinin sayısı|Boyut yok|
|D2C. Property. Update. Success|Yes|Cihazlardan başarılı cihaz özelliği güncelleştirmeleri|Count|Toplam|Cihazlardan başlatılan tüm başarılı özellik güncelleştirmelerinin sayısı|Boyut yok|
|D2C. telemetri. ınress. allProtocol|Yes|Toplam telemetri Iletisi gönderme denemeleri|Count|Toplam|IoT Central uygulamasına gönderilmesi denenen cihazdan buluta telemetri iletisi sayısı|Boyut yok|
|D2C. telemetri. giriş. başarılı|Yes|Gönderilen toplam telemetri Iletisi|Count|Toplam|IoT Central uygulamasına başarıyla gönderilen cihazdan buluta telemetri iletisi sayısı|Boyut yok|
|dataExport. Error|Yes|Veri dışarı aktarma hataları|Count|Toplam|Verilerin dışarı aktarılması için karşılaşılan hata sayısı|Dışarı Exportıd, exportDisplayName, Destinationıd, destinationDisplayName|
|dataExport. messages. filtrelenmiş|Yes|Filtrelenen veri dışarı aktarma Iletileri|Count|Toplam|Veri dışarı aktarma sırasında filtrelerle geçilen ileti sayısı|Dışarı Exportıd, exportDisplayName, Destinationıd, destinationDisplayName|
|dataExport. messages. alındı|Yes|Alınan veri dışarı aktarma Iletileri|Count|Toplam|Filtrelemeden ve zenginleştirme işlemeden önce, veri dışa aktarmaya gelen ileti sayısı|Dışarı Exportıd, exportDisplayName, Destinationıd, destinationDisplayName|
|dataExport. messages. yazıldı|Yes|Yazılan veri dışa aktarma Iletileri|Count|Toplam|Hedefe yazılan ileti sayısı|Dışarı Exportıd, exportDisplayName, Destinationıd, destinationDisplayName|
|dataExport. statusChange|Yes|Veri dışarı aktarma durum değişikliği|Count|Toplam|Durum değişikliği sayısı|Dışarı Exportıd, exportDisplayName, Destinationıd, destinationDisplayName, Status|
|deviceDataUsage|Yes|Toplam cihaz verisi kullanımı|Bayt|Toplam|IoT Central uygulamasına bağlı herhangi bir cihazdan ve bu bilgisayardan aktarılan baytlar|Boyut yok|
|provisionedDeviceCount|No|Sağlanan toplam cihaz sayısı|Count|Ortalama|IoT Central uygulamada sağlanan cihaz sayısı|Boyut yok|

## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. keykasası/managedhsms

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik|No|Genel hizmet kullanılabilirliği|Yüzde|Ortalama|Hizmet istekleri kullanılabilirliği|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiHit|Yes|Toplam hizmet API 'Si Isabet sayısı|Count|Count|Toplam hizmet API 'si isabet sayısı|ActivityType, ActivityName|
|Hizmet Apilatesi|No|Genel hizmet API 'Si gecikmesi|Mayacak|Ortalama|Hizmet API 'si isteklerinin genel gecikmesi|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Toplam hizmet API 'Si sonuçları|Count|Count|Toplam hizmet API 'si sonuç sayısı|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. Keykasası/kasa

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik|Yes|Genel kasa kullanılabilirliği|Yüzde|Ortalama|Kasa istekleri kullanılabilirliği|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|Cumartesi Ationshoe kutusu|No|Genel kasa doygunluğu|Yüzde|Ortalama|Kullanılan kasa kapasitesi|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Yes|Toplam hizmet API 'Si Isabet sayısı|Count|Count|Toplam hizmet API 'si isabet sayısı|ActivityType, ActivityName|
|Hizmet Apilatesi|Yes|Genel hizmet API 'Si gecikmesi|Mayacak|Ortalama|Hizmet API 'si isteklerinin genel gecikmesi|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Toplam hizmet API 'Si sonuçları|Count|Count|Toplam hizmet API 'si sonuç sayısı|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkubernetesconnectedclusters"></a>Microsoft. Kubernetes/Connectedkümeler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|capacity_cpu_cores|Yes|Bağlı bir kümedeki toplam CPU çekirdeği sayısı|Count|Toplam|Bağlı bir kümedeki toplam CPU çekirdeği sayısı||


## <a name="microsoftkustoclusters"></a>Microsoft. kusto/kümeler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|BatchBlobCount|Yes|Toplu iş blobu sayısı|Count|Ortalama|Alma için toplanmış toplu işteki veri kaynağı sayısı.|Veritabanı|
|BatchDuration|Yes|Toplu iş süresi|Saniye|Ortalama|Alma akışındaki toplama aşamasının süresi.|Veritabanı|
|Batchesişlendi|Yes|Işlenen toplu işler|Count|Toplam|Alma için toplanan toplu iş sayısı. Toplu işleme türü: toplu işlemin toplu işlem zamanına, veri boyutuna veya dosya sayısına göre ayarlanmış olup olmadığını, ilke toplu işleme tarafından ayarlanan|Veritabanı, SealReason|
|BatchSize|Yes|Toplu İş Boyutu|Bayt|Ortalama|Alma için toplanmış bir toplu işte beklenen veri boyutu sıkıştırılmamış.|Veritabanı|
|Blobsbırakılan|Yes|Bırakılan Bloblar|Count|Toplam|Bir bileşen tarafından kalıcı olarak reddedilen BLOB sayısı.|Veritabanı, ComponentType, ComponentName|
|Blobsişlendi|Yes|Işlenen Bloblar|Count|Toplam|Bir bileşen tarafından işlenen Blobların sayısı.|Veritabanı, ComponentType, ComponentName|
|BlobsReceived|Yes|Alınan Bloblar|Count|Toplam|Bir bileşen tarafından giriş akışından alınan Blobların sayısı.|Veritabanı, ComponentType, ComponentName|
|Cachekullanım|Yes|Önbellek kullanımı|Yüzde|Ortalama|Küme kapsamındaki kullanım düzeyi|Boyut yok|
|ContinuousExportMaxLatenessMinutes|Yes|Sürekli dışarı aktarma maksimum değeri|Count|Maksimum|Kümedeki sürekli dışarı aktarma işleri tarafından raporlanan süre (dakika cinsinden)|Boyut yok|
|Continuousexportnumofrecordsexports|Yes|Sürekli dışa aktarma – dışarı aktarılmış kayıtların sayısı|Count|Toplam|Dışarı aktarma işlemi sırasında yazılan her depolama yapıtı için tetiklenen, dışarı aktarılmış kayıt sayısı|Devamousexportname, veritabanı|
|ContinuousExportPendingCount|Yes|Sürekli dışarı aktarma bekleyen sayısı|Count|Maksimum|Yürütmeye hazırlamış bekleyen sürekli dışarı aktarma işlerinin sayısı|Boyut yok|
|ContinuousExportResult|Yes|Sürekli dışarı aktarma sonucu|Count|Count|Sürekli dışarı aktarmanın başarılı veya başarısız olup olmadığını belirtir|Devamousexportname, sonuç, veritabanı|
|CPU|Yes|CPU|Yüzde|Ortalama|CPU kullanım düzeyi|Boyut yok|
|DiscoveryLatency|Yes|Bulma gecikmesi|Saniye|Ortalama|Veri bağlantıları tarafından bildirilen (varsa). Veri bağlantısı tarafından bulunana kadar bir ileti sıraya alındığı veya olayın oluşturulduğu süre (saniye cinsinden). Bu zaman, Azure Veri Gezgini toplam alma süresine dahil değildir.|ComponentType, ComponentName|
|Bırakılan olayları|Yes|Bırakılan olaylar|Count|Toplam|Veri bağlantısı tarafından kalıcı olarak bırakılan olay sayısı. Hata nedeni olan bir alım sonucu ölçüsü gönderilir.|ComponentType, ComponentName|
|Işlenen olayları|Yes|İşlenen Olaylar|Count|Toplam|Küme tarafından işlenen olay sayısı|ComponentType, ComponentName|
|Processedforeventhubs olayları|Yes|Işlenen Olaylar (olay/IoT Hub 'Ları için)|Count|Toplam|Olay/IoT Hub geri ödeme yaparken küme tarafından işlenen olay sayısı|EventStatus|
|Alınan olayları|Yes|Alınan olaylar|Count|Toplam|Veri bağlantısı tarafından alınan olay sayısı.|ComponentType, ComponentName|
|Dışarı Aktaramstilileştirme|Yes|Dışarı Aktarma Kullanımı|Yüzde|Maksimum|Kullanım verme|Boyut yok|
|Inestionlatencınseconds|Yes|Veri Alımı Gecikmesi|Saniye|Ortalama|Verilerin kümede alındığı zamandan sorgu için hazırlanana kadar süren alınan veri gecikmesi. Veri alımı gecikmesi dönemi alma senaryosuna bağlıdır.|Boyut yok|
|Inestionresult|Yes|Alım sonucu|Count|Toplam|Alma işlemlerinin sayısı|Inestionresultdetails|
|Alım kullanımı|Yes|Alım kullanımı|Yüzde|Ortalama|Kümedeki kullanılan alma yuvaları oranı|Boyut yok|
|Inestionvolumeınmb|Yes|Veri Alım Hacmi|Bayt|Toplam|Kümeye alınan verilerin toplam hacmi|Veritabanı|
|Yükteki InstanceCount|Yes|Örnek Sayısı|Count|Ortalama|Toplam örnek sayısı|Boyut yok|
|Alı|Yes|Canlı tut|Count|Ortalama|Sanity denetimi, kümenin sorgulara yanıt verdiğini belirtir|Boyut yok|
|Materializedviewagedakikaları|Yes|Gerçekleştirilmiş görünüm yaşı|Count|Ortalama|Dakikalar içinde gerçekleştirilmiş görünüm yaşı|Veritabanı, MaterializedViewName|
|MaterializedViewDataLoss|Yes|Gerçekleştirilmiş görünüm veri kaybı|Count|Maksimum|Gerçekleştirilmiş görünümde olası veri kaybını belirtir|Veritabanı, MaterializedViewName, tür|
|MaterializedViewExtentsRebuild|Yes|Gerçekleştirilmiş görünüm kapsamları yeniden oluşturma|Count|Ortalama|Uzantı yeniden derleme sayısı|Veritabanı, MaterializedViewName|
|Materializedviewwhegizli|Yes|Gerçekleştirilmiş görünüm durumu|Count|Ortalama|Gerçekleştirilmiş görünümün sistem durumu (sağlıklı için 1, sağlıklı olmayan için 0)|Veritabanı, MaterializedViewName|
|Materializedviewmisinizındelta|Yes|Delta 'Da gerçekleştirilmiş görünüm kayıtları|Count|Ortalama|Görünümün gerçekleştirilmiş olmayan bölümünde kayıt sayısı|Veritabanı, MaterializedViewName|
|MaterializedViewResult|Yes|Gerçekleştirilmiş görünüm sonucu|Count|Ortalama|Gerçekleştirmesi işleminin sonucu|Veritabanı, MaterializedViewName, sonuç|
|QueryDuration|Yes|Sorgu süresi|Mayacak|Ortalama|Sorgu süresi (saniye)|QueryStatus|
|QueryResult|No|Sorgu Sonucu|Count|Count|Toplam sorgu sayısı.|QueryStatus|
|QueueLength|Yes|Sırası Uzunluğu|Count|Ortalama|Bileşen sırasındaki bekleyen ileti sayısı.|ComponentType|
|QueueOldestMessage|Yes|En eski kuyruk Iletisi|Count|Ortalama|Kuyruktaki en eski iletinin eklendiği saniye cinsinden süre.|ComponentType|
|Alınan Datasizebytes|Yes|Alınan veri boyutu baytları|Bayt|Ortalama|Veri bağlantısı tarafından alınan verilerin boyutu. Bu, veri akışının boyutudur veya sağlanmışsa ham veri boyutudur.|ComponentType, ComponentName|
|StageLatency|Yes|Aşama gecikmesi|Saniye|Ortalama|Raporlama bileşeni tarafından işlenmek üzere alınana kadar bir iletinin keşfedildiği, toplu süre (ileti alma kuyruğu için ileti sıraya alındığında veya veri bağlantısı tarafından bulunduğunda, bulma süresi ayarlanır).|Veritabanı, ComponentType|
|SteamingIngestRequestRate|Yes|Akış Alma İstek Oranı|Count|Kterequestspersecond|Akış alma isteği hızı (saniye başına istek)|Boyut yok|
|StreamingIngestDataRate|Yes|Akış Alma Veri Oranı|Count|Ortalama|Akış alma verileri oranı (MB/saniye)|Boyut yok|
|StreamingIngestDuration|Yes|Akış Alma Süresi|Mayacak|Ortalama|Milisaniye cinsinden akış alma süresi|Boyut yok|
|StreamingIngestResults|Yes|Akış Alma Sonucu|Count|Ortalama|Akış alma sonucu|Sonuç|
|TotalNumberOfConcurrentQueries|Yes|Toplam eşzamanlı sorgu sayısı|Count|Maksimum|Toplam eşzamanlı sorgu sayısı|Boyut yok|
|Totalnumberofkapsamları|Yes|Toplam kapsam sayısı|Count|Toplam|Toplam veri uzantısı sayısı|Boyut yok|
|Totalnumberofkısıtlar Ledcommands|Yes|Kısıtlanmış komutların toplam sayısı|Count|Toplam|Kısıtlanmış komutların toplam sayısı|CommandType|
|Toplam sorgu sayısı|Yes|Toplam kısıtlanmış sorgu sayısı|Count|Maksimum|Toplam kısıtlanmış sorgu sayısı|Boyut yok|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/ıntegrationserviceortamortamları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Eylem gecikmesi |Saniye|Ortalama|Tamamlanan iş akışı eylemlerinin gecikme süresi.|Boyut yok|
|ActionsCompleted|Yes|Tamamlanan eylemler |Count|Toplam|Tamamlanan iş akışı eylemlerinin sayısı.|Boyut yok|
|ActionsFailed|Yes|Başarısız eylemler |Count|Toplam|Başarısız iş akışı eylemi sayısı.|Boyut yok|
|ActionsSkipped|Yes|Atlanan eylemler |Count|Toplam|Atlanan iş akışı eylemi sayısı.|Boyut yok|
|ActionsStarted|Yes|Başlatılan eylemler |Count|Toplam|Başlatılan iş akışı eylemi sayısı.|Boyut yok|
|ActionsSucceeded|Yes|İşlemler başarılı oldu |Count|Toplam|Başarılı iş akışı eylemi sayısı.|Boyut yok|
|Actionbaşarılı gecikme süresi|Yes|Eylem başarı gecikmesi |Saniye|Ortalama|Başarılı iş akışı eylemlerinin gecikme süresi.|Boyut yok|
|ActionThrottledEvents|Yes|Eylem kısıtlanmış olaylar|Count|Toplam|İş akışı eylemi kısıtlanmış olay sayısı..|Boyut yok|
|Integrationserviceenvironmentconnectormemoryusage|Yes|Tümleştirme Hizmeti Ortamı için bağlayıcı bellek kullanımı|Yüzde|Ortalama|Tümleştirme hizmeti ortamı için bağlayıcı bellek kullanımı.|Boyut yok|
|Integrationserviceenvironmentconnectorprocessorusage|Yes|Tümleştirme Hizmeti Ortamı için bağlayıcı Işlemci kullanımı|Yüzde|Ortalama|Tümleştirme hizmeti ortamı için bağlayıcı işlemci kullanımı.|Boyut yok|
|Integrationserviceenvironmentworkflowmemoryusage|Yes|Tümleştirme Hizmeti Ortamı için iş akışı bellek kullanımı|Yüzde|Ortalama|Tümleştirme hizmeti ortamı için iş akışı bellek kullanımı.|Boyut yok|
|Integrationserviceenvironmentworkflowprocessorusage|Yes|Tümleştirme Hizmeti Ortamı için iş akışı Işlemcisi kullanımı|Yüzde|Ortalama|Tümleştirme hizmeti ortamı için iş akışı işlemcisi kullanımı.|Boyut yok|
|RunFailurePercentage yüzdesi|Yes|Çalıştırma hatası yüzdesi|Yüzde|Toplam|İş akışı çalıştırmalarının yüzdesi başarısız oldu.|Boyut yok|
|RunLatency|Yes|Çalıştırma gecikmesi|Saniye|Ortalama|Tamamlanan iş akışı çalıştırmalarının gecikmesi.|Boyut yok|
|RunsCancelled|Yes|Çalıştırma Iptal edildi|Count|Toplam|İptal edilen iş akışı çalıştırma sayısı.|Boyut yok|
|RunsCompleted|Yes|Tamamlanan çalıştırmalar|Count|Toplam|Tamamlanan iş akışı çalıştırma sayısı.|Boyut yok|
|RunsFailed|Yes|Çalıştırma başarısız|Count|Toplam|Başarısız iş akışı çalıştırma sayısı.|Boyut yok|
|RunsStarted|Yes|Çalışma başlatıldı|Count|Toplam|Başlatılan iş akışı çalıştırma sayısı.|Boyut yok|
|RunsSucceeded|Yes|Çalışma başarılı|Count|Toplam|İş akışı çalıştırma sayısı başarılı oldu.|Boyut yok|
|RunStartThrottledEvents|Yes|Kısıtlanmış başlangıç olaylarını Çalıştır|Count|Toplam|İş akışı çalıştırma, kısıtlanmış olay sayısı.|Boyut yok|
|Runbaşarılı gecikme süresi|Yes|Çalıştırma başarılı gecikme süresi|Saniye|Ortalama|Başarılı iş akışı çalıştırmalarının gecikmesi.|Boyut yok|
|RunThrottledEvents|Yes|Kısıtlanmış olayları Çalıştır|Count|Toplam|İş akışı eylemi veya tetikleyici kısıtlanan olay sayısı.|Boyut yok|
|Triggerfirelatlik|Yes|Tetikleyici Tetikleme gecikmesi |Saniye|Ortalama|Tetiklenen iş akışı Tetikleyicileri gecikmesi.|Boyut yok|
|TriggerLatency|Yes|Tetikleme gecikmesi |Saniye|Ortalama|Tamamlanan iş akışı tetikleyicilerinin gecikmesi.|Boyut yok|
|TriggersCompleted|Yes|Tamamlanan Tetikleyiciler |Count|Toplam|Tamamlanan iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersFailed|Yes|Başarısız Tetikleyiciler |Count|Toplam|Başarısız iş akışı tetikleyicisi sayısı.|Boyut yok|
|Triggersharekete geçirildi|Yes|Tetiklenen Tetikleyiciler |Count|Toplam|Tetiklenen iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersSkipped|Yes|Atlanan Tetikleyiciler|Count|Toplam|Atlanan iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersStarted|Yes|Başlatılan Tetikleyiciler |Count|Toplam|Başlatılan iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersSucceeded|Yes|Başarılı Tetikleyiciler |Count|Toplam|Başarılı iş akışı tetikleyicisi sayısı.|Boyut yok|
|Triggerbaşarılı gecikme|Yes|Tetikleyici başarı gecikme süresi |Saniye|Ortalama|Başarılı iş akışı tetikleyicilerinin gecikmesi.|Boyut yok|
|TriggerThrottledEvents|Yes|Kısıtlanmış olayları tetikleyin|Count|Toplam|İş akışı tetikleyicisi kısıtlanmış olay sayısı.|Boyut yok|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/iş akışları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Eylem gecikmesi |Saniye|Ortalama|Tamamlanan iş akışı eylemlerinin gecikme süresi.|Boyut yok|
|ActionsCompleted|Yes|Tamamlanan eylemler |Count|Toplam|Tamamlanan iş akışı eylemlerinin sayısı.|Boyut yok|
|ActionsFailed|Yes|Başarısız eylemler |Count|Toplam|Başarısız iş akışı eylemi sayısı.|Boyut yok|
|ActionsSkipped|Yes|Atlanan eylemler |Count|Toplam|Atlanan iş akışı eylemi sayısı.|Boyut yok|
|ActionsStarted|Yes|Başlatılan eylemler |Count|Toplam|Başlatılan iş akışı eylemi sayısı.|Boyut yok|
|ActionsSucceeded|Yes|İşlemler başarılı oldu |Count|Toplam|Başarılı iş akışı eylemi sayısı.|Boyut yok|
|Actionbaşarılı gecikme süresi|Yes|Eylem başarı gecikmesi |Saniye|Ortalama|Başarılı iş akışı eylemlerinin gecikme süresi.|Boyut yok|
|ActionThrottledEvents|Yes|Eylem kısıtlanmış olaylar|Count|Toplam|İş akışı eylemi kısıtlanmış olay sayısı..|Boyut yok|
|Billableactionyürütmeler|Yes|Faturalanabilir eylem yürütmeleri|Count|Toplam|Faturalandırılan iş akışı eylemi yürütmelerinin sayısı.|Boyut yok|
|Billabletriggeryürütmeler|Yes|Faturalanabilir tetikleyici yürütmeleri|Count|Toplam|Faturalandırılan iş akışı tetikleyici yürütmelerinin sayısı.|Boyut yok|
|BillingUsageNativeOperation|Yes|Yerel Işlem yürütmeleri için faturalandırma kullanımı|Count|Toplam|Faturalandırılan yerel işlem yürütmelerinin sayısı.|Boyut yok|
|BillingUsageStandardConnector|Yes|Standart bağlayıcı yürütmeleri için faturalandırma kullanımı|Count|Toplam|Faturalandırılan standart bağlayıcı yürütmelerinin sayısı.|Boyut yok|
|Billingusagestorampatüketim|Yes|Depolama tüketimi yürütmeleri için faturalama kullanımı|Count|Toplam|Faturalandırılan depolama tüketimi yürütmelerinin sayısı.|Boyut yok|
|RunFailurePercentage yüzdesi|Yes|Çalıştırma hatası yüzdesi|Yüzde|Toplam|İş akışı çalıştırmalarının yüzdesi başarısız oldu.|Boyut yok|
|RunLatency|Yes|Çalıştırma gecikmesi|Saniye|Ortalama|Tamamlanan iş akışı çalıştırmalarının gecikmesi.|Boyut yok|
|RunsCancelled|Yes|Çalıştırma Iptal edildi|Count|Toplam|İptal edilen iş akışı çalıştırma sayısı.|Boyut yok|
|RunsCompleted|Yes|Tamamlanan çalıştırmalar|Count|Toplam|Tamamlanan iş akışı çalıştırma sayısı.|Boyut yok|
|RunsFailed|Yes|Çalıştırma başarısız|Count|Toplam|Başarısız iş akışı çalıştırma sayısı.|Boyut yok|
|RunsStarted|Yes|Çalışma başlatıldı|Count|Toplam|Başlatılan iş akışı çalıştırma sayısı.|Boyut yok|
|RunsSucceeded|Yes|Çalışma başarılı|Count|Toplam|İş akışı çalıştırma sayısı başarılı oldu.|Boyut yok|
|RunStartThrottledEvents|Yes|Kısıtlanmış başlangıç olaylarını Çalıştır|Count|Toplam|İş akışı çalıştırma, kısıtlanmış olay sayısı.|Boyut yok|
|Runbaşarılı gecikme süresi|Yes|Çalıştırma başarılı gecikme süresi|Saniye|Ortalama|Başarılı iş akışı çalıştırmalarının gecikmesi.|Boyut yok|
|RunThrottledEvents|Yes|Kısıtlanmış olayları Çalıştır|Count|Toplam|İş akışı eylemi veya tetikleyici kısıtlanan olay sayısı.|Boyut yok|
|Totalbillableyürütmeleri|Yes|Toplam faturalandırılabilir yürütmeler|Count|Toplam|Faturalandırılan iş akışı yürütmelerinin sayısı.|Boyut yok|
|Triggerfirelatlik|Yes|Tetikleyici Tetikleme gecikmesi |Saniye|Ortalama|Tetiklenen iş akışı Tetikleyicileri gecikmesi.|Boyut yok|
|TriggerLatency|Yes|Tetikleme gecikmesi |Saniye|Ortalama|Tamamlanan iş akışı tetikleyicilerinin gecikmesi.|Boyut yok|
|TriggersCompleted|Yes|Tamamlanan Tetikleyiciler |Count|Toplam|Tamamlanan iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersFailed|Yes|Başarısız Tetikleyiciler |Count|Toplam|Başarısız iş akışı tetikleyicisi sayısı.|Boyut yok|
|Triggersharekete geçirildi|Yes|Tetiklenen Tetikleyiciler |Count|Toplam|Tetiklenen iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersSkipped|Yes|Atlanan Tetikleyiciler|Count|Toplam|Atlanan iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersStarted|Yes|Başlatılan Tetikleyiciler |Count|Toplam|Başlatılan iş akışı tetikleyicisi sayısı.|Boyut yok|
|TriggersSucceeded|Yes|Başarılı Tetikleyiciler |Count|Toplam|Başarılı iş akışı tetikleyicisi sayısı.|Boyut yok|
|Triggerbaşarılı gecikme|Yes|Tetikleyici başarı gecikme süresi |Saniye|Ortalama|Başarılı iş akışı tetikleyicilerinin gecikmesi.|Boyut yok|
|TriggerThrottledEvents|Yes|Kısıtlanmış olayları tetikleyin|Count|Toplam|İş akışı tetikleyicisi kısıtlanmış olay sayısı.|Boyut yok|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/çalışma alanları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Etkin çekirdekler|Yes|Etkin çekirdekler|Count|Ortalama|Etkin çekirdek sayısı|Senaryo, ClusterName|
|Etkin düğümler|Yes|Etkin düğümler|Count|Ortalama|Acitve düğümlerin sayısı. Bunlar, etkin olarak bir işi çalıştıran düğümlerdir.|Senaryo, ClusterName|
|Istenen çalıştırmaları iptal et|Yes|Istenen çalıştırmaları iptal et|Count|Toplam|Bu çalışma alanı için iptal edilen çalıştırma sayısı. Bir çalıştırma için iptal isteği alındığında sayı güncelleştirilir.|Senaryo, RunType, Publishedpipelineıd, ComputeType, PipelineStepType, ExperimentName|
|İptal edilen çalıştırmalar|Yes|İptal edilen çalıştırmalar|Count|Toplam|Bu çalışma alanı için iptal edilen çalıştırma sayısı. Bir çalıştırma başarıyla iptal edildiğinde sayı güncelleştirilir.|Senaryo, RunType, Publishedpipelineıd, ComputeType, PipelineStepType, ExperimentName|
|Tamamlanan çalıştırmalar|Yes|Tamamlanan çalıştırmalar|Count|Toplam|Bu çalışma alanı için çalıştırma sayısı başarıyla tamamlandı. Bir çalıştırma tamamlandığında ve çıkış toplandığında sayı güncelleştirilir.|Senaryo, RunType, Publishedpipelineıd, ComputeType, PipelineStepType, ExperimentName|
|Cpukullanımı|Yes|Cpukullanımı|Count|Ortalama|Bir CPU düğümündeki kullanım yüzdesi. Kullanım, bir dakikalık aralıklarla raporlanır.|Senaryo, RunId, NodeId, ClusterName|
|Hatalar|Yes|Hatalar|Count|Toplam|Bu çalışma alanındaki çalıştırma hatalarının sayısı. Çalışma bir hatayla karşılaştığında, sayı güncellenir.|Senaryo|
|Başarısız çalıştırmalar|Yes|Başarısız çalıştırmalar|Count|Toplam|Bu çalışma alanı için başarısız olan çalıştırma sayısı. Bir çalıştırma başarısız olduğunda sayı güncelleştirilir.|Senaryo, RunType, Publishedpipelineıd, ComputeType, PipelineStepType, ExperimentName|
|Çalıştırmalar sonlandırılıyor|Yes|Çalıştırmalar sonlandırılıyor|Count|Toplam|Bu çalışma alanı için sonlandırılıyor durum girilen çalıştırma sayısı. Bir çalıştırma tamamlandığında ancak çıkış koleksiyonu hala devam ederken sayı güncelleştirilir.|Senaryo, RunType, Publishedpipelineıd, ComputeType, PipelineStepType, ExperimentName|
|Gpumemoryutilileştirme|Yes|Gpumemoryutilileştirme|Count|Ortalama|Bir GPU düğümündeki bellek kullanımının yüzdesi. Kullanım, bir dakikalık aralıklarla raporlanır.|Senaryo, RunId, NodeId, DeviceID, ClusterName|
|Gpukullanım|Yes|Gpukullanım|Count|Ortalama|Bir GPU düğümündeki kullanım yüzdesi. Kullanım, bir dakikalık aralıklarla raporlanır.|Senaryo, RunId, NodeId, DeviceID, ClusterName|
|Boştaki çekirdekler|Yes|Boştaki çekirdekler|Count|Ortalama|Boşta çekirdek sayısı|Senaryo, ClusterName|
|Boştaki düğümler|Yes|Boştaki düğümler|Count|Ortalama|Boştaki düğüm sayısı. Boştaki düğümler, herhangi bir işi çalıştırmayan ancak varsa yeni işi kabul edebilecek düğümlerdir.|Senaryo, ClusterName|
|Çekirdekleri bırakma|Yes|Çekirdekleri bırakma|Count|Ortalama|Bırakma çekirdekleri sayısı|Senaryo, ClusterName|
|Düğümlerden çıkılıyor|Yes|Düğümlerden çıkılıyor|Count|Ortalama|Düğüm sayısı. Düğüm ayrıldığınızda, bir işi işlemeyi tamamlamış olan düğümler ve boşta durumuna gidecektir.|Senaryo, ClusterName|
|Model Dağıtımı başarısız oldu|Yes|Model Dağıtımı başarısız oldu|Count|Toplam|Bu çalışma alanında başarısız olan model dağıtımı sayısı|Senaryo, StatusCode|
|Model Dağıtımı başladı|Yes|Model Dağıtımı başladı|Count|Toplam|Bu çalışma alanında başlatılan model dağıtımı sayısı|Senaryo|
|Model Dağıtımı başarılı oldu|Yes|Model Dağıtımı başarılı oldu|Count|Toplam|Bu çalışma alanındaki başarılı olan model dağıtımı sayısı|Senaryo|
|Model kaydı başarısız oldu|Yes|Model kaydı başarısız oldu|Count|Toplam|Bu çalışma alanında başarısız olan model kayıtlarının sayısı|Senaryo, StatusCode|
|Model kaydı başarılı oldu|Yes|Model kaydı başarılı oldu|Count|Toplam|Bu çalışma alanında başarılı olan model kaydı sayısı|Senaryo|
|Yanıt vermiyor çalıştırmaları|Yes|Yanıt vermiyor çalıştırmaları|Count|Toplam|Bu çalışma alanı için yanıt vermeyen çalıştırma sayısı. Bir çalıştırma yanıt vermiyorsa, sayı güncelleştirilir.|Senaryo, RunType, Publishedpipelineıd, ComputeType, PipelineStepType, ExperimentName|
|Başlatılmamış çalıştırmalar|Yes|Başlatılmamış çalıştırmalar|Count|Toplam|Bu çalışma alanı için başlatılmamış durumdaki çalıştırma sayısı. Sayı, çalıştırma oluşturmak için bir istek alındığında ancak çalıştırma bilgisi henüz doldurulmadığında güncelleştirilir. |Senaryo, RunType, Publishedpipelineıd, ComputeType, PipelineStepType, ExperimentName|
|Önden çekirdekler|Yes|Önden çekirdekler|Count|Ortalama|Yok edilecek çekirdek sayısı|Senaryo, ClusterName|
|Önden düğümler|Yes|Önden düğümler|Count|Ortalama|Yok edilecek düğüm sayısı. Bu düğümler, kullanılabilir düğüm havuzundan alınan düşük öncelikli düğümlerdir.|Senaryo, ClusterName|
|Çalıştırmalar hazırlanıyor|Yes|Çalıştırmalar hazırlanıyor|Count|Toplam|Bu çalışma alanı için hazırlanma çalıştırmaların sayısı. Çalışma ortamı hazırlanırken bir çalıştırma duruma hazırlık girdiğinde sayı güncellenir.|Senaryo, RunType, Publishedpipelineıd, ComputeType, PipelineStepType, ExperimentName|
|Sağlama çalıştırmaları|Yes|Sağlama çalıştırmaları|Count|Toplam|Bu çalışma alanı için sağlama olan çalıştırma sayısı. Bir çalıştırma, işlem hedefi oluşturmayı veya sağlamayı beklerken, sayı güncelleştirilir.|Senaryo, RunType, Publishedpipelineıd, ComputeType, PipelineStepType, ExperimentName|
|Sıraya alınan çalıştırmalar|Yes|Sıraya alınan çalıştırmalar|Count|Toplam|Bu çalışma alanı için sıraya alınan çalıştırmaların sayısı. Bir çalıştırma işlem hedefinde sıraya alınmışsa, sayı güncelleştirilir. Gerekli işlem düğümlerinin hazırlanmasını beklerken ortaya kodan olabilir.|Senaryo, RunType, Publishedpipelineıd, ComputeType, PipelineStepType, ExperimentName|
|Kota kullanım yüzdesi|Yes|Kota kullanım yüzdesi|Count|Ortalama|Kullanılan Kota yüzdesi|Senaryo, ClusterName, VmFamilyName, VmPriority|
|Başlatılan çalıştırmalar|Yes|Başlatılan çalıştırmalar|Count|Toplam|Bu çalışma alanı için çalıştırılan çalıştırma sayısı. Çalıştırma, gerekli kaynaklar üzerinde çalışmaya başladığında, sayı güncelleştirilir.|Senaryo, RunType, Publishedpipelineıd, ComputeType, PipelineStepType, ExperimentName|
|Çalıştırmalar başlatılıyor|Yes|Çalıştırmalar başlatılıyor|Count|Toplam|Bu çalışma alanı için başlatılan çalıştırma sayısı. Sayı, çalıştırma kimliği oluşturma ve çalıştırma kimliği gibi çalışma bilgisi doldurulduktan sonra güncelleştirilir.|Senaryo, RunType, Publishedpipelineıd, ComputeType, PipelineStepType, ExperimentName|
|Toplam çekirdek sayısı|Yes|Toplam çekirdek sayısı|Count|Ortalama|Toplam çekirdek sayısı|Senaryo, ClusterName|
|Toplam düğüm sayısı|Yes|Toplam düğüm sayısı|Count|Ortalama|Toplam düğüm sayısı. Bu toplam etkin düğümlerin, boştaki düğümlerin, kullanılamayan düğümlerin, önceden ayrılmış düğümlerin bazılarını içerir, düğümleri bırakır|Senaryo, ClusterName|
|Kullanılamayan çekirdekler|Yes|Kullanılamayan çekirdekler|Count|Ortalama|Kullanılamayan çekirdek sayısı|Senaryo, ClusterName|
|Kullanılamayan düğümler|Yes|Kullanılamayan düğümler|Count|Ortalama|Kullanılamayan düğüm sayısı. Çözümlenebilen bazı sorunlar nedeniyle kullanılamaz düğümler çalışmıyor. Azure bu düğümleri geri dönüştürecek.|Senaryo, ClusterName|
|Uyarılar|Yes|Uyarılar|Count|Toplam|Bu çalışma alanındaki çalışma uyarısı sayısı. Bir çalıştırma bir uyarıyla karşılaştığında, sayı güncellenir.|Senaryo|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/hesaplar

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik|Yes|Kullanılabilirlik|Yüzde|Ortalama|API 'lerin kullanılabilirliği|ApiCategory, ApiName|
|Kullanım|No|Kullanım|Count|Count|API çağrısı sayısı|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/mediaservices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AssetCount|Yes|Varlık sayısı|Count|Ortalama|Geçerli medya hizmeti hesabında kaç varlık zaten oluşturuldu|Boyut yok|
|AssetQuota|Yes|Varlık kotası|Count|Ortalama|Geçerli medya hizmeti hesabı için kaç kıymete izin veriliyor?|Boyut yok|
|AssetQuotaUsedPercentage|Yes|Kullanılan varlık kotası yüzdesi|Yüzde|Ortalama|Geçerli medya hizmeti hesabındaki varlık kullanım yüzdesi|Boyut yok|
|ChannelsAndLiveEventsCount sayısı|Yes|Canlı etkinlik sayısı|Count|Ortalama|Geçerli Media Services hesabındaki toplam canlı olay sayısı|Boyut yok|
|ContentKeyPolicyCount|Yes|İçerik anahtarı Ilke sayısı|Count|Ortalama|Geçerli medya hizmeti hesabında kaç tane içerik anahtar ilkesi oluşturuldu|Boyut yok|
|ContentKeyPolicyQuota|Yes|İçerik anahtarı Ilke kotası|Count|Ortalama|Geçerli medya hizmeti hesabı için kaç tane içerik anahtar ilkelerine izin verilir?|Boyut yok|
|ContentKeyPolicyQuotaUsedPercentage|Yes|İçerik anahtarı Ilke kotası kullanılan yüzde|Yüzde|Ortalama|Geçerli medya hizmeti hesabında kullanılan içerik anahtarı Ilkesi yüzdesi|Boyut yok|
|MaxChannelsAndLiveEventsCount sayısı|Yes|En büyük canlı etkinlik kotası|Count|Maksimum|Geçerli Media Services hesabında izin verilen en fazla canlı olay sayısı|Boyut yok|
|MaxRunningChannelsAndLiveEventsCount sayısı|Yes|En fazla çalışan canlı etkinlik kotası|Count|Maksimum|Geçerli Media Services hesabında izin verilen en fazla çalışan etkin olay sayısı|Boyut yok|
|RunningChannelsAndLiveEventsCount sayısı|Yes|Çalışan canlı olay sayısı|Count|Ortalama|Geçerli Media Services hesabındaki toplam çalışan etkin olay sayısı|Boyut yok|
|Streammingpolicycount|Yes|Akış Ilkesi sayısı|Count|Ortalama|Geçerli medya hizmeti hesabında kaç tane akış ilkesi zaten oluşturuldu|Boyut yok|
|StreamingPolicyQuota|Yes|Akış Ilkesi kotası|Count|Ortalama|Geçerli medya hizmeti hesabı için kaç akış ilkesine izin verilir?|Boyut yok|
|StreamingPolicyQuotaUsedPercentage|Yes|Akış Ilkesi kotası kullanılan yüzde|Yüzde|Ortalama|Geçerli medya hizmeti hesabındaki akış Ilkesi kullanım yüzdesi|Boyut yok|


## <a name="microsoftmediamediaservicesliveevents"></a>Microsoft. Media/mediaservices/liveEvents

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|IngestBitrate|Yes|Canlı olay alma bit hızı|BitsPerSecond|Ortalama|Bit/saniye cinsinden canlı bir olay için alınan gelen bit hızı.|TrackName|
|IngestDriftValue|Yes|Canlı olay alma değeri|Saniye|Maksimum|Alınan içeriğin zaman damgası ve sistem saatinin, dakika başına saniye cinsinden ölçülen aralığı. Sıfır olmayan bir değer, alınan içeriğin sistem saati zamanından daha yavaş olduğunu gösterir.|TrackName|
|IngestLastTimestamp|Yes|Canlı olay alma son zaman damgası|Mayacak|Maksimum|Canlı bir olay için son zaman damgası.|TrackName|
|LiveOutputLastTimestamp|Yes|Son çıktı zaman damgası|Mayacak|Maksimum|Canlı bir olay çıkışı için depolamaya yüklenen son parçanın zaman damgası.|TrackName|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/mediaservices/streamingEndpoints

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|CPU|Yes|CPU kullanımı|Yüzde|Ortalama|Premium akış uç noktaları için CPU kullanımı. Bu veriler standart akış uç noktaları için kullanılamaz.|Boyut yok|
|Çıkış|Yes|Çıkış|Bayt|Toplam|Bayt cinsinden çıkış verisi miktarı.|OutputFormat|
|Yumurressbandwidth|No|Çıkış bant genişliği|BitsPerSecond|Ortalama|Bit/saniye cinsinden çıkış bant genişliği.|Boyut yok|
|İstekler|Yes|İstekler|Count|Toplam|Akış uç noktası istekleri.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Yes|Başarılı uçtan uca gecikme süresi|Mayacak|Ortalama|Başarılı istekler için milisaniye cinsinden ortalama gecikme süresi.|OutputFormat|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. MixedReality/remoteRenderingAccounts

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ActiveRenderingSessions|Yes|Etkin Işleme oturumları|Count|Ortalama|Toplam etkin işleme oturumu sayısı|SessionType, SDKVersion|
|Assetsdönüştürüldü|Yes|Dönüştürülen varlıklar|Count|Toplam|Dönüştürülen toplam varlık sayısı|SDK sürümü|


## <a name="microsoftmixedrealityspatialanchorsaccounts"></a>Microsoft. MixedReality/spatialAnchorsAccounts

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AnchorsCreated|Yes|Oluşturulan Çıpaları|Count|Toplam|Oluşturulan bağlayıcı sayısı|DeviceFamily, SDKVersion|
|AnchorsDeleted|Yes|Bağlantılar silindi|Count|Toplam|Silinen bağlayıcı sayısı|DeviceFamily, SDKVersion|
|Anchorssorgulanan|Yes|Sorgulanan bağlantılar|Count|Toplam|Sorgulanan uzamsal bağlayıcı sayısı|DeviceFamily, SDKVersion|
|AnchorsUpdated|Yes|Bağlantılar güncelleştirildi|Count|Toplam|Güncellenen bağlayıcı sayısı|DeviceFamily, SDKVersion|
|PosesFound|Yes|Bulunan pozlar|Count|Toplam|Döndürülen pozlar sayısı|DeviceFamily, SDKVersion|
|Totaldavilyanchors|Yes|Günlük toplam bağlayıcı sayısı|Count|Ortalama|Toplam bağlayıcı sayısı-günlük|DeviceFamily, SDKVersion|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/Capacityhavuzları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Yes|Havuza ayrılan boyut|Bayt|Ortalama|Bu havuzun boyutu sağlandı|Boyut yok|
|VolumePoolAllocatedToVolumeThroughput|Yes|Havuza ayrılan üretilen iş|BytesPerSecond|Ortalama|Havuza ait tüm birimlerin verimlilik toplamı|Boyut yok|
|VolumePoolAllocatedUsed|Yes|Birim boyutuna ayrılan havuz|Bayt|Ortalama|Havuzun ayrılan boyutu ayrılmış|Boyut yok|
|Birimhavuzprovisionedüretilen Iş|Yes|Havuz için sağlanan aktarım hızı|BytesPerSecond|Ortalama|Bu havuzun sağlanmış olarak üretilen işi|Boyut yok|
|VolumePoolTotalLogicalSize|Yes|Kullanılan havuz boyutu|Bayt|Ortalama|Havuza ait tüm birimlerin mantıksal boyutunun toplamı|Boyut yok|
|VolumePoolTotalSnapshotSize|Yes|Havuz için toplam anlık görüntü boyutu|Bayt|Ortalama|Bu havuzdaki tüm birimlerin anlık görüntü boyutu toplamı|Boyut yok|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/Capacityhavuzları/birimleri

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AverageReadLatency|Yes|Ortalama okuma gecikmesi|Mayacak|Ortalama|İşlem başına milisaniye cinsinden ortalama okuma gecikmesi|Boyut yok|
|AverageWriteLatency|Yes|Ortalama yazma gecikmesi|Mayacak|Ortalama|İşlem başına milisaniye cinsinden ortalama yazma gecikmesi|Boyut yok|
|Cbsvolumebackupactıve|Yes|Birim yedeklemesi askıya alındı|Count|Ortalama|Yedekleme ilkesi birim için askıya alındı mu? 0 ise, hayır ise 1.|Boyut yok|
|CbsVolumeLogicalBackupBytes|Yes|Birim yedekleme baytları|Bayt|Ortalama|Bu birim için yedeklenen toplam bayt sayısı.|Boyut yok|
|Cbsvolumeoperationtamamlanmıştır|Yes|Toplu yedekleme Işlemi tamamlanmıştır|Count|Ortalama|Son birim yedeklemesi veya geri yükleme işlemi başarıyla tamamlandı mı? 1 ise, hayır ise 0.|Boyut yok|
|CbsVolumeOperationTransferredBytes|Yes|Toplu yedekleme son aktarılan baytlar|Bayt|Ortalama|Son yedekleme veya geri yükleme işlemi için aktarılan toplam bayt sayısı.|Boyut yok|
|CbsVolumeProtected|Yes|Birim yedeklemesi etkin|Count|Ortalama|Birim için yedekleme etkinleştirildi mi? 1 ise, hayır ise 0.|Boyut yok|
|Diğer aktarım hızı|Yes|Diğer aktarım hızı|BytesPerSecond|Ortalama|Bayt/saniye cinsinden diğer üretilen iş (okuma veya yazma)|Boyut yok|
|ReadIops|Yes|IOPS 'yi oku|Sayaçpersaniye|Ortalama|Saniye başına okuma/kapatma işlemi|Boyut yok|
|Readüretilen Iş|Yes|Aktarım hızını oku|BytesPerSecond|Ortalama|Bayt/saniye cinsinden okuma performansı|Boyut yok|
|Toplam aktarım hızı|Yes|Toplam Verimlilik|BytesPerSecond|Ortalama|Bayt/saniye cinsinden tüm aktarım hızı toplamı|Boyut yok|
|Birimayrıtedboyut|Yes|Birim ayırma boyutu|Bayt|Ortalama|Bir birimin sağlanan boyutu|Boyut yok|
|Birimtüketimedsizepercentage|Yes|Tüketilen yüzde birimi boyutu|Yüzde|Ortalama|Anlık görüntüler dahil kullanılan birimin yüzdesi.|Boyut yok|
|Birimlogicalsize|Yes|Tüketilen birim boyutu|Bayt|Ortalama|Birimin mantıksal boyutu (kullanılan baytlar)|Boyut yok|
|VolumeSnapshotSize|Yes|Birim anlık görüntü boyutu|Bayt|Ortalama|Birimdeki tüm anlık görüntülerin boyutu|Boyut yok|
|Writeıops|Yes|IOPS yaz|Sayaçpersaniye|Ortalama|Saniyede yazma/çıkış işlemi sayısı|Boyut yok|
|Writeüretilen Iş|Yes|Yazma performansı|BytesPerSecond|Ortalama|Bayt/saniye cinsinden yazma hızı|Boyut yok|
|Xregionreplicationsağlıklı|Yes|Birim çoğaltma durumu sağlıklı|Count|Ortalama|İlişki koşulu, 1 veya 0.|Boyut yok|
|XregionReplicationLagTime|Yes|Birim çoğaltma gecikme süresi|Saniye|Ortalama|Yansıtmadaki verilerin kaynağın arkasında olduğu sürenin saniye cinsinden miktarı.|Boyut yok|
|XregionReplicationLastTransferDuration|Yes|Birim çoğaltma son aktarım süresi|Saniye|Ortalama|Son aktarımın tamamlanması için geçen saniye cinsinden süre.|Boyut yok|
|XregionReplicationLastTransferSize|Yes|Birim çoğaltma son aktarım boyutu|Bayt|Ortalama|Son aktarımın bir parçası olarak aktarılan toplam bayt sayısı.|Boyut yok|
|XregionReplicationRelationshipProgress|Yes|Birim çoğaltma ilerleme durumu|Bayt|Ortalama|Geçerli aktarım işlemi için aktarılan toplam veri miktarı.|Boyut yok|
|Xregionreplicationrelationshiptransfer|Yes|Birim çoğaltma aktarılıyor|Count|Ortalama|Birim çoğaltmasının durumunun ' aktarma ' olup olmadığı.|Boyut yok|
|XregionReplicationTotalTransferBytes|Yes|Birim çoğaltma toplam aktarımı|Bayt|Ortalama|İlişki için aktarılan birikmeli baytlar.|Boyut yok|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/Applicationgateway 'ler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|No|Application Gateway toplam süre|Mayacak|Ortalama|Bir isteğin işlenmesi için geçen ortalama süre ve gönderilecek yanıt. Bu, yanıt gönderme işlemi tamamlandığında Application Gateway bir HTTP isteğinin ilk baytını aldığında zaman aralığının ortalaması olarak hesaplanır. Bu, genellikle Application Gateway işleme süresini, istek ve Yanıt paketlerinin ağ üzerinden hareket etmekte olduğu süreyi ve arka uç sunucusunun yanıt vermesi gerektiğini unutmayın.|Dinleyici|
|Avgrequestcountperhealthyıhost|No|Sağlıklı ana bilgisayar başına dakika başına istek|Count|Ortalama|Bir havuzda sağlıklı arka uç Konağı başına dakika başına ortalama istek sayısı|BackendSettingsPool|
|BackendConnectTime|No|Arka uç bağlantı saati|Mayacak|Ortalama|Arka uç sunucusuyla bağlantı kurmaya harcanan süre|Dinleyici, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|No|Arka uç Ilk bayt yanıt süresi|Mayacak|Ortalama|Arka uç sunucusuna bağlantı kurma ve yanıt üst bilgisinin ilk baytını alma işlemi arasındaki zaman aralığı, arka uç sunucusunun işlem süresini yaklaştırmaya başladı|Dinleyici, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|No|Arka uç son bayt yanıt süresi|Mayacak|Ortalama|Arka uç sunucusuna bağlantı kurma ve yanıt gövdesinin son baytını alma arasındaki zaman aralığı|Dinleyici, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|Yes|Arka uç yanıt durumu|Count|Toplam|Arka uç üyeleri tarafından oluşturulan HTTP yanıt kodlarının sayısı. Bu, Application Gateway tarafından oluşturulan yanıt kodlarını içermez.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|Yes|Web uygulaması güvenlik duvarı engellenen Istek kuralı dağıtımı|Count|Toplam|Web uygulaması güvenlik duvarı engellenen istek kuralı dağıtımı|RuleGroup, RuleId|
|BlockedReqCount|Yes|Web uygulaması güvenlik duvarı engellenen Istek sayısı|Count|Toplam|Web uygulaması güvenlik duvarı engellenen istek sayısı|Boyut yok|
|BytesReceived|Yes|Alınan bayt|Bayt|Toplam|İstemcilerden Application Gateway tarafından alınan toplam bayt sayısı|Dinleyici|
|BytesSent|Yes|Gönderilen bayt|Bayt|Toplam|Application Gateway tarafından istemcilere gönderilen toplam bayt sayısı|Dinleyici|
|CapacityUnits|No|Geçerli kapasite birimleri|Count|Ortalama|Tüketilen Kapasite birimleri|Boyut yok|
|ClientRtt|No|İstemci RTT|Mayacak|Ortalama|İstemcilerle Application Gateway arasında ortalama gidiş dönüş süresi. Bu ölçüm, bağlantı kurmak ve bildirimlerin döndürülmesi için ne kadar sürdüğünü gösterir|Dinleyici|
|ComputeUnits|No|Geçerli Işlem birimleri|Count|Ortalama|Tüketilen işlem birimleri|Boyut yok|
|Cpukullanımı|No|CPU Kullanımı|Yüzde|Ortalama|Application Gateway geçerli CPU kullanımı|Boyut yok|
|CurrentConnections|Yes|Geçerli bağlantılar|Count|Toplam|Application Gateway ile kurulan geçerli bağlantı sayısı|Boyut yok|
|Estimatedbilledcapacitybirimleri|No|Tahmini faturalandırılan Kapasite birimleri|Count|Ortalama|Ücretlendirilebilecek tahmini Kapasite birimleri|Boyut yok|
|FailedRequests|Yes|Başarısız İstekler|Count|Toplam|Application Gateway tarafından sunulan başarısız istek sayısı|BackendSettingsPool|
|Fixedbillablecapacitybirimleri|No|Sabit Faturalanabilir Kapasite Birimleri|Count|Ortalama|Ücretlendirilebilecek minimum kapasite birimleri|Boyut yok|
|Healthyıhostcount|Yes|Sağlıklı konak sayısı|Count|Ortalama|İyi durumdaki arka uç konaklarının sayısı|BackendSettingsPool|
|MatchedCount|Yes|Web uygulaması güvenlik duvarı toplam kural dağıtımı|Count|Toplam|Gelen trafik için Web uygulaması güvenlik duvarı toplam kural dağıtımı|RuleGroup, RuleId|
|NewConnectionsPerSecond|No|Saniyedeki yeni bağlantı sayısı|Sayaçpersaniye|Ortalama|Saniye başına yeni bağlantı Application Gateway ile oluşturuldu|Boyut yok|
|ResponseStatus|Yes|Yanıt durumu|Count|Toplam|Application Gateway tarafından döndürülen http yanıt durumu|HttpStatusGroup|
|Aktarım hızı|No|Aktarım hızı|BytesPerSecond|Ortalama|Application Gateway saniyede hizmet verilen bayt sayısı|Boyut yok|
|TlsProtocol|Yes|İstemci TLS protokolü|Count|Toplam|Application Gateway ile bağlantı kurulan istemci tarafından başlatılan TLS ve TLS olmayan isteklerin sayısı. TLS protokol dağıtımını görüntülemek için Dimension TLS protokolüne göre filtreleyin.|Dinleyici, TlsProtocol|
|TotalRequests|Yes|Toplam İstek Sayısı|Count|Toplam|Application Gateway tarafından sunulan başarılı istek sayısı|BackendSettingsPool|
|Unhealthyıhostcount|Yes|Sağlıksız konak sayısı|Count|Ortalama|İyi durumda olmayan arka uç konaklarının sayısı|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Yes|Uygulama kuralları isabet sayısı|Count|Toplam|Uygulama kurallarının isabet sayısı|Durum, neden, protokol|
|Veri Işlendi|Yes|İşlenen veriler|Bayt|Toplam|Bu güvenlik duvarı tarafından işlenen toplam veri miktarı|Boyut yok|
|FirewallHealth|Yes|Güvenlik duvarı sistem durumu|Yüzde|Ortalama|Bu güvenlik duvarının genel durumunu gösterir|Durum, neden|
|NetworkRuleHit|Yes|Ağ kuralları isabet sayısı|Count|Toplam|Ağ kurallarının isabet sayısı|Durum, neden, protokol|
|SNATPortUtilization|Yes|SNAT bağlantı noktası kullanımı|Yüzde|Ortalama|Şu anda kullanımda olan giden SNAT bağlantı noktalarının yüzdesi|Protokol|
|Aktarım hızı|No|Aktarım hızı|BitsPerSecond|Ortalama|Bu güvenlik duvarı tarafından işlenen işleme|Boyut yok|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|pingkafes|No|Savunma Iletişim durumu|Count|Ortalama|İletişim durumu, tüm iletişim uygunsa 1, bozuksa 0 değerini gösterir.||
|bağlanabilecek|No|Oturum Sayısı|Count|Toplam|Savunma için oturum sayısı. Toplam ve örnek başına görüntüleyin.|konak|
|toplam|Yes|Toplam bellek|Count|Ortalama|Toplam bellek istatistiği.|konak|
|usage_user|No|Kullanılan CPU|Count|Ortalama|CPU kullanım istatistikleri.|CPU, ana bilgisayar|
|kullanılan|Yes|Kullanılan Bellek|Count|Ortalama|Bellek kullanım istatistikleri.|konak|


## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Ortalama|Saniye başına Azure’da bit girişi|Boyut yok|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Ortalama|Saniye başına Azure’da bit çıkışı|Boyut yok|


## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|QueryVolume|No|Sorgu hacmi|Count|Toplam|Bir DNS bölgesi için sunulan sorgu sayısı|Boyut yok|
|Recordsetkapaıyutilileştirme|No|Kayıt kümesi kapasite kullanımı|Yüzde|Maksimum|Bir DNS bölgesi tarafından kullanılan kayıt kümesi kapasitesinin yüzdesi|Boyut yok|
|Kayıt kümesi sayısı|No|Kayıt kümesi sayısı|Count|Maksimum|Bir DNS bölgesindeki kayıt kümesi sayısı|Boyut yok|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/Expressroutedevreleri

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ArpAvailability|Yes|ARP kullanılabilirliği|Yüzde|Ortalama|Tüm eşleri doğrultusunda MSEE 'tan ARP kullanılabilirliği.|PeeringType, eş|
|BgpAvailability|Yes|BGP kullanılabilirliği|Yüzde|Ortalama|Tüm Peers yönelik MSEE BGP kullanılabilirliği.|PeeringType, eş|
|BitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Ortalama|Saniye başına Azure’da bit girişi|PeeringType, DeviceRole|
|BitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Ortalama|Saniye başına Azure’da bit çıkışı|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|No|GlobalReachBitsInPerSecond|BitsPerSecond|Ortalama|Saniye başına Azure’da bit girişi|Peereddevresi Skey|
|GlobalReachBitsOutPerSecond|No|GlobalReachBitsOutPerSecond|BitsPerSecond|Ortalama|Saniye başına Azure’da bit çıkışı|Peereddevresi Skey|
|QosDropBitsInPerSecond|Yes|DroppedInBitsPerSecond|BitsPerSecond|Ortalama|Saniye başına bırakılan veri girişi bitleri|Boyut yok|
|QosDropBitsOutPerSecond|Yes|DroppedOutBitsPerSecond|BitsPerSecond|Ortalama|Saniye başına bırakılan veri çıkış bitleri|Boyut yok|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/Expressroutedevreleri/eşayarları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Ortalama|Saniye başına Azure’da bit girişi|Boyut yok|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Ortalama|Saniye başına Azure’da bit çıkışı|Boyut yok|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/Expressroutegateway 'ler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Ortalama|Saniye başına Azure’da bit girişi|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Ortalama|Saniye başına Azure’da bit çıkışı|ConnectionName|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Yes|Eşe tanıtılan yolların sayısı (Önizleme)|Count|Maksimum|ExpressRouteGateway tarafından eşe tanıtılan yolların sayısı|Roleınstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|Eşten öğrenilen yolların sayısı (Önizleme)|Count|Maksimum|ExpressRouteGateway tarafından eşten öğrenilen yolların sayısı|Roleınstance|
|Expressroutegatewaycpukullanımı|Yes|CPU kullanımı|Count|Ortalama|ExpressRoute ağ geçidinin CPU kullanımı|Roleınstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Yolların sıklığı değişikliği (Önizleme)|Count|Toplam|ExpressRoute Gateway 'de yolların sıklığı değişikliği|Roleınstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Sanal ağdaki VM sayısı (Önizleme)|Count|Maksimum|Sanal ağdaki sanal makinelerin sayısı|Boyut yok|
|ExpressRouteGatewayPacketsPerSecond|No|Paket/saniye|Sayaçpersaniye|Ortalama|ExpressRoute ağ geçidinin paket sayısı|Roleınstance|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AdminState|Yes|AdminState|Count|Ortalama|Bağlantı noktasının yönetici durumu|Bağlantı|
|LineProtocol|Yes|LineProtocol|Count|Ortalama|Bağlantı noktasının hat protokol durumu|Bağlantı|
|PortBitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Ortalama|Saniye başına Azure’da bit girişi|Bağlantı|
|PortBitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Ortalama|Saniye başına Azure’da bit çıkışı|Bağlantı|
|RxLightLevel|Yes|RxLightLevel|Count|Ortalama|DBm 'de RX hafif düzeyi|Bağlantı, kulvar|
|TxLightLevel|Yes|TxLightLevel|Count|Ortalama|DBm 'de TX hafif düzeyi|Bağlantı, kulvar|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontkapaklı

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Yes|Arka uç sistem durumu yüzdesi|Yüzde|Ortalama|HTTP/S proxy 'sinden arka uçlara başarılı sistem durumu araştırmalarının yüzdesi|Arka uç, BackendPool|
|BackendRequestCount|Yes|Arka uç Istek sayısı|Count|Toplam|HTTP/S proxy 'sinden arka uçlara gönderilen isteklerin sayısı|HttpStatus, HttpStatusGroup, arka uç|
|BackendRequestLatency|Yes|Arka uç Isteği gecikmesi|Mayacak|Ortalama|HTTP/s proxy, arka ucun son yanıt baytını almayana kadar isteğin HTTP/S proxy tarafından arka uca gönderildiği süre|Arka uç|
|Billilableresponsesize|Yes|Faturalanabilir yanıt boyutu|Bayt|Toplam|HTTP/S proxy 'sinden istemcilere yanıt olarak gönderilen faturalandırılabilir bayt sayısı (istek başına en az 2 KB).|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Istek sayısı|Yes|İstek Sayısı|Count|Toplam|HTTP/S proxy tarafından hizmet verilen istemci isteklerinin sayısı|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Yes|İstek boyutu|Bayt|Toplam|İstemcilerden HTTP/S proxy 'sine istek olarak gönderilen bayt sayısı|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Yanıt boyutu|Yes|Yanıt boyutu|Bayt|Toplam|HTTP/S proxy 'sinden istemcilere yanıt olarak gönderilen baytların sayısı|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Yes|Toplam gecikme süresi|Mayacak|Ortalama|İstemci, http/s proxy 'sinden gelen son yanıt baytını kabul edene kadar, istemci isteğinin HTTP/S proxy tarafından alındığı zamana göre hesaplanır|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Yes|Web uygulaması güvenlik duvarı Istek sayısı|Count|Toplam|Web uygulaması güvenlik duvarı tarafından işlenen istemci isteklerinin sayısı|PolicyName, RuleName, eylem|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|No|Ayrılan SNAT bağlantı noktaları|Count|Ortalama|Zaman aralığı içinde ayrılan toplam SNAT bağlantı noktası sayısı|Frontendıbaddress, Backendıaddress, ProtocolType, |
|ByteCount|Yes|Bayt sayısı|Bayt|Toplam|Zaman aralığı içinde aktarılan toplam bayt sayısı|Frontendıaaddress, FrontendPort, Yön|
|DipAvailability|Yes|Durum Yoklaması Durumu|Count|Ortalama|Süre başına ortalama Load Balancer sistem durumu araştırma durumu|ProtocolType, BackendPort, Frontendıbaddress, FrontendPort, Backendıdıaddress|
|PacketCount|Yes|Paket Sayısı|Count|Toplam|Zaman aralığı içinde aktarılan toplam paket sayısı|Frontendıaaddress, FrontendPort, Yön|
|SnatConnectionCount|Yes|SNAT bağlantı sayısı|Count|Toplam|Zaman aralığı içinde oluşturulan toplam yeni SNAT bağlantısı sayısı|Frontendıbaddress, Backendıo adresi, ConnectionState|
|SYNCount|Yes|SYN sayısı|Count|Toplam|Zaman aralığı içinde aktarılan toplam SYN paketi sayısı|Frontendıaaddress, FrontendPort, Yön|
|UsedSnatPorts|No|Kullanılan SNAT bağlantı noktaları|Count|Ortalama|Zaman aralığı içinde kullanılan SNAT bağlantı noktalarının toplam sayısı|Frontendıbaddress, Backendıaddress, ProtocolType, |
|VipAvailability|Yes|Veri Yolu Kullanılabilirliği|Count|Ortalama|Süre başına ortalama Load Balancer veri yolu kullanılabilirliği|Frontendıaddress, FrontendPort|


## <a name="microsoftnetworknatgateways"></a>Microsoft. Network/Natgateway 'ler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Bayt|Bayt|Toplam|Zaman aralığı içinde aktarılan toplam bayt sayısı|Protokol, Yön|
|Datapathavaılabılıty|Yes|Veri yolu kullanılabilirliği (Önizleme)|Count|Ortalama|NAT ağ geçidi veri yolu kullanılabilirliği|Boyut yok|
|PacketCount|Yes|Paketleri|Count|Toplam|Zaman aralığı içinde aktarılan toplam paket sayısı|Protokol, Yön|
|PacketDropCount|Yes|Bırakılan paketler|Count|Toplam|Bırakılan paket sayısı|Boyut yok|
|SNATConnectionCount|Yes|SNAT bağlantı sayısı|Count|Toplam|Toplam eşzamanlı etkin bağlantı sayısı|Protokol, ConnectionState|
|TotalConnectionCount|Yes|Toplam SNAT bağlantı sayısı|Count|Toplam|Toplam etkin SNAT bağlantısı sayısı|Protokol|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/NetworkInterfaces

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Yes|Alınan bayt|Bayt|Toplam|Ağ arabiriminin aldığı bayt sayısı|Boyut yok|
|BytesSentRate|Yes|Gönderilen bayt|Bayt|Toplam|Ağ arabiriminin gönderdiği bayt sayısı|Boyut yok|
|PacketsReceivedRate|Yes|Alınan paketler|Count|Toplam|Ağ arabiriminin aldığı paket sayısı|Boyut yok|
|PacketsSentRate|Yes|Gönderilen paketler|Count|Toplam|Ağ arabiriminin gönderdiği paket sayısı|Boyut yok|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/Connectionmonitörleri

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Yes|Ort. gidiş dönüş süresi (MS) (klasik)|Mayacak|Ortalama|Kaynak ve hedef arasında gönderilen bağlantı izleme araştırmaları için Ortalama ağ gidiş dönüş süresi (MS)|Boyut yok|
|ChecksFailedPercent|Yes|Denetim başarısız oldu yüzdesi|Yüzde|Ortalama|bağlantı izleme denetimlerinin yüzdesi başarısız oldu|SourceAddress, SourceName, Sourceresourceıd, SourceType, protokol, DestinationAddress, DestinationName, Destinationresourceıd, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, Destinationıp, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent|Yes|% Yoklama başarısız oldu (klasik)|Yüzde|Ortalama|Bağlantı İzleme Araştırmaları yüzdesi başarısız oldu|Boyut yok|
|Roundroundtimems|Yes|Round-Trip zamanı (MS)|Mayacak|Ortalama|Bağlantı izleme denetimleri için milisaniye cinsinden gidiş dönüş süresi|SourceAddress, SourceName, Sourceresourceıd, SourceType, protokol, DestinationAddress, DestinationName, Destinationresourceıd, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, Destinationıp, SourceSubnet, DestinationSubnet|
|TestResult|Yes|Test sonucu|Count|Ortalama|Bağlantı İzleyicisi test sonucu|SourceAddress, SourceName, Sourceresourceıd, SourceType, protokol, DestinationAddress, DestinationName, Destinationresourceıd, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, Testresultölçüt, SourceIP, Destinationıp, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|P2SBandwidth|Yes|Ağ Geçidi P2S bant genişliği|BytesPerSecond|Ortalama|Bayt/saniye cinsinden bir ağ geçidinin ortalama Noktadan siteye bant genişliği|Boyut yok|
|P2SConnectionCount|Yes|P2S Bağlantı Sayısı|Count|Toplam|Bir ağ geçidinin noktadan siteye bağlantı sayısı|Protokol|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/privateDnsZones

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|QueryVolume|Yes|Sorgu hacmi|Count|Toplam|Bir Özel DNS bölgesi için sunulan sorgu sayısı|Boyut yok|
|Recordsetkapaıyutilileştirme|No|Kayıt kümesi kapasite kullanımı|Yüzde|Maksimum|Özel DNS bölgesi tarafından kullanılan kayıt kümesi kapasitesinin yüzdesi|Boyut yok|
|Kayıt kümesi sayısı|Yes|Kayıt kümesi sayısı|Count|Maksimum|Özel DNS bölgesindeki kayıt kümesi sayısı|Boyut yok|
|Virtualnetworklinkkapaıyutilileştirme|No|Sanal ağ bağlantısı kapasitesi kullanımı|Yüzde|Maksimum|Bir Özel DNS bölgesi tarafından kullanılan sanal ağ bağlantısı kapasitesinin yüzdesi|Boyut yok|
|VirtualNetworkLinkCount|Yes|Sanal ağ bağlantı sayısı|Count|Maksimum|Bir Özel DNS bölgesine bağlı sanal ağların sayısı|Boyut yok|
|Virtualnetworkwithregistrationkapaıyutilileştirme|No|Sanal ağ kaydı bağlantı kapasitesi kullanımı|Yüzde|Maksimum|Özel DNS bölgesi tarafından kullanılan otomatik kayıt kapasitesine sahip sanal ağ bağlantısının yüzdesi|Boyut yok|
|VirtualNetworkWithRegistrationLinkCount|Yes|Sanal ağ kayıt bağlantı sayısı|Count|Maksimum|Otomatik kayıt özellikli bir Özel DNS bölgesine bağlı sanal ağların sayısı|Boyut yok|


## <a name="microsoftnetworkprivateendpoints"></a>Microsoft. Network/privateEndpoints

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|PEBytesIn|Yes|Içindeki baytlar|Count|Toplam|Toplam giden bayt sayısı|PrivateEndpointId|
|PEBytesOut|Yes|Giden bayt|Count|Toplam|Toplam giden bayt sayısı|PrivateEndpointId|


## <a name="microsoftnetworkprivatelinkservices"></a>Microsoft. Network/privateLinkServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|PLSBytesIn|Yes|Içindeki baytlar|Count|Toplam|Toplam giden bayt sayısı|Privatelinkserviceıd|
|PLSBytesOut|Yes|Giden bayt|Count|Toplam|Toplam giden bayt sayısı|Privatelinkserviceıd|
|PLSNatPortsUsage|Yes|NAT bağlantı noktaları kullanımı|Yüzde|Ortalama|NAT bağlantı noktaları kullanımı|Privatelinkserviceıd, Privatelinkserviceıpaddress|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/Publicıpaddresses

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Bayt sayısı|Bayt|Toplam|Zaman aralığı içinde aktarılan toplam bayt sayısı|Bağlantı noktası, Yön|
|BytesDroppedDDoS|Yes|Gelen bayt bırakıldı DDoS|BytesPerSecond|Maksimum|Gelen bayt bırakıldı DDoS|Boyut yok|
|BytesForwardedDDoS|Yes|İletilen gelen bayt sayısı|BytesPerSecond|Maksimum|İletilen gelen bayt sayısı|Boyut yok|
|BytesInDDoS|Yes|Gelen bayt sayısı DDoS|BytesPerSecond|Maksimum|Gelen bayt sayısı DDoS|Boyut yok|
|Ddobir Ggersynpackets|Yes|DDoS azaltmayı tetiklemek için gelen SYN paketleri|Sayaçpersaniye|Maksimum|DDoS azaltmayı tetiklemek için gelen SYN paketleri|Boyut yok|
|DDO, Ggertcppackets|Yes|DDoS azaltmayı tetiklemek için gelen TCP paketleri|Sayaçpersaniye|Maksimum|DDoS azaltmayı tetiklemek için gelen TCP paketleri|Boyut yok|
|DDO, Ggerudppaketleri|Yes|DDoS azaltmayı tetiklemek için gelen UDP paketleri|Sayaçpersaniye|Maksimum|DDoS azaltmayı tetiklemek için gelen UDP paketleri|Boyut yok|
|Iunderddosattack|Yes|DDoS saldırısı altında|Count|Maksimum|DDoS saldırısı altında|Boyut yok|
|PacketCount|Yes|Paket Sayısı|Count|Toplam|Zaman aralığı içinde aktarılan toplam paket sayısı|Bağlantı noktası, Yön|
|PacketsDroppedDDoS|Yes|DDoS bırakılan gelen paketler|Sayaçpersaniye|Maksimum|DDoS bırakılan gelen paketler|Boyut yok|
|PacketsForwardedDDoS|Yes|DDoS iletilen gelen paketler|Sayaçpersaniye|Maksimum|DDoS iletilen gelen paketler|Boyut yok|
|PacketsInDDoS|Yes|Gelen paketler DDoS|Sayaçpersaniye|Maksimum|Gelen paketler DDoS|Boyut yok|
|SynCount|Yes|SYN sayısı|Count|Toplam|Zaman aralığı içinde aktarılan toplam SYN paketi sayısı|Bağlantı noktası, Yön|
|TCPBytesDroppedDDoS|Yes|Gelen TCP bayt bırakılan DDoS|BytesPerSecond|Maksimum|Gelen TCP bayt bırakılan DDoS|Boyut yok|
|TCPBytesForwardedDDoS|Yes|İletilen gelen TCP baytları|BytesPerSecond|Maksimum|İletilen gelen TCP baytları|Boyut yok|
|TCPBytesInDDoS|Yes|Gelen TCP bayt DDoS|BytesPerSecond|Maksimum|Gelen TCP bayt DDoS|Boyut yok|
|TCPPacketsDroppedDDoS|Yes|Gelen TCP paketleri bırakıldı DDoS|Sayaçpersaniye|Maksimum|Gelen TCP paketleri bırakıldı DDoS|Boyut yok|
|TCPPacketsForwardedDDoS|Yes|DDoS iletilen gelen TCP paketleri|Sayaçpersaniye|Maksimum|DDoS iletilen gelen TCP paketleri|Boyut yok|
|TCPPacketsInDDoS|Yes|Gelen TCP paketleri DDoS|Sayaçpersaniye|Maksimum|Gelen TCP paketleri DDoS|Boyut yok|
|UDPBytesDroppedDDoS|Yes|Gelen UDP bayt bırakılan DDoS|BytesPerSecond|Maksimum|Gelen UDP bayt bırakılan DDoS|Boyut yok|
|UDPBytesForwardedDDoS|Yes|İletilen gelen UDP baytları|BytesPerSecond|Maksimum|İletilen gelen UDP baytları|Boyut yok|
|UDPBytesInDDoS|Yes|Gelen UDP bayt sayısı DDoS|BytesPerSecond|Maksimum|Gelen UDP bayt sayısı DDoS|Boyut yok|
|UDPPacketsDroppedDDoS|Yes|Gelen UDP paketleri bırakıldı|Sayaçpersaniye|Maksimum|Gelen UDP paketleri bırakıldı|Boyut yok|
|UDPPacketsForwardedDDoS|Yes|DDoS iletilen gelen UDP paketleri|Sayaçpersaniye|Maksimum|DDoS iletilen gelen UDP paketleri|Boyut yok|
|UDPPacketsInDDoS|Yes|Gelen UDP paketleri DDoS|Sayaçpersaniye|Maksimum|Gelen UDP paketleri DDoS|Boyut yok|
|VipAvailability|Yes|Veri Yolu Kullanılabilirliği|Count|Ortalama|Süre başına ortalama IP adresi kullanılabilirliği|Bağlantı noktası|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Probeagentcurrentendpointstatebyprofileresourceıd|Yes|Uç nokta tarafından uç nokta durumu|Count|Maksimum|1 bir uç noktanın araştırma durumu "etkin" ise, 0 yoksa.|Uçnoktaadı|
|QpsByEndpoint|Yes|Bitiş noktasına göre sorgular döndürüldü|Count|Toplam|Verilen zaman çerçevesinde Traffic Manager uç noktasının döndürüldüğü sayı|Uçnoktaadı|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/Virtualnetworkgateway 'ler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Ağ Geçidi S2S bant genişliği|BytesPerSecond|Ortalama|Bayt/saniye cinsinden bir ağ geçidinin ortalama siteden siteye bant genişliği|Boyut yok|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Yes|Eşe tanıtılan yolların sayısı (Önizleme)|Count|Maksimum|ExpressRouteGateway tarafından eşe tanıtılan yolların sayısı|Roleınstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|Eşten öğrenilen yolların sayısı (Önizleme)|Count|Maksimum|ExpressRouteGateway tarafından eşten öğrenilen yolların sayısı|Roleınstance|
|Expressroutegatewaycpukullanımı|Yes|CPU kullanımı|Count|Ortalama|ExpressRoute ağ geçidinin CPU kullanımı|Roleınstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Yolların sıklığı değişikliği (Önizleme)|Count|Toplam|ExpressRoute Gateway 'de yolların sıklığı değişikliği|Roleınstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Sanal ağdaki VM sayısı (Önizleme)|Count|Maksimum|Sanal ağdaki sanal makinelerin sayısı|Boyut yok|
|ExpressRouteGatewayPacketsPerSecond|No|Paket/saniye|Sayaçpersaniye|Ortalama|ExpressRoute ağ geçidinin paket sayısı|Roleınstance|
|P2SBandwidth|Yes|Ağ Geçidi P2S bant genişliği|BytesPerSecond|Ortalama|Bayt/saniye cinsinden bir ağ geçidinin ortalama Noktadan siteye bant genişliği|Boyut yok|
|P2SConnectionCount|Yes|P2S Bağlantı Sayısı|Count|Maksimum|Bir ağ geçidinin noktadan siteye bağlantı sayısı|Protokol|
|TunnelAverageBandwidth|Yes|Tünel Bant Genişliği|BytesPerSecond|Ortalama|Bir tünelin bayt/saniye cinsinden ortalama bant genişliği|ConnectionName, Remoteıp|
|TunnelEgressBytes|Yes|Tünel Çıkış Baytları|Bayt|Toplam|Bir tünelin giden bayt sayısı|ConnectionName, Remoteıp|
|TunnelEgressPacketDropTSMismatch|Yes|Tünel Çıkış TS Uyumsuzluğu Paket Bırakma|Count|Toplam|Bir tünelin trafik seçiciyle eşleşmeyen giden paket bırakma sayısı|ConnectionName, Remoteıp|
|TunnelEgressPackets|Yes|Tünel Çıkış Paketleri|Count|Toplam|Bir tünelin giden paket sayısı|ConnectionName, Remoteıp|
|TunnelIngressBytes|Yes|Tünel Giriş Baytları|Bayt|Toplam|Bir tünelin gelen bayt sayısı|ConnectionName, Remoteıp|
|TunnelIngressPacketDropTSMismatch|Yes|Tünel Giriş TS Uyumsuzluğu Paket Bırakma|Count|Toplam|Bir tünelin trafik seçiciyle eşleşmeyen gelen paket bırakma sayısı|ConnectionName, Remoteıp|
|TunnelIngressPackets|Yes|Tünel giriş paketleri|Count|Toplam|Bir tünelin gelen paket sayısı|ConnectionName, Remoteıp|
|Tünelnatallocations konumları|No|Tünel NAT ayırmaları|Count|Toplam|Bir tünelde NAT kuralı için ayırma sayısı|NatRule, ConnectionName, Remoteıp|
|Tünelde Tedbytes|No|Tünel Içindeki baytlar|Bayt|Toplam|Bir NAT kuralı tarafından tünele gelen bayt sayısı |NatRule, ConnectionName, Remoteıp|
|Tünelnatedpackets|No|Tünel tutulan paketler|Count|Toplam|Bir NAT kuralı tarafından tünele gelen paketlerin sayısı|NatRule, ConnectionName, Remoteıp|
|Tünelnatflowcount|No|NAT akışları tüneli|Count|Toplam|Bir tünelde akış türüne ve NAT kuralına göre NAT akışı sayısı|NatRule, ConnectionName, Remoteıp, FlowType|
|Tünelnatpacketdrop|No|Tünel NAT paketi düşü|Count|Toplam|Bırakma türü ve NAT kuralı tarafından bırakılan bir tünelde bulunan paketlerin sayısı|NatRule, ConnectionName, Remoteıp, DropType|
|Tünelde sente bayt|No|Tünel ters çevrilmiş baytlar|Bayt|Toplam|Bir NAT kuralı tarafından bir tünelde ters çevrilmiş bayt sayısı|NatRule, ConnectionName, Remoteıp|
|Tünel yankı paketleri|No|Ters çevrilmiş paketleri tünel|Count|Toplam|Bir NAT kuralı tarafından ters çevrilmiş bir Tüneldeki paket sayısı|NatRule, ConnectionName, Remoteıp|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Yes|Bir VM 'ye ping için gidiş dönüş süresi|Mayacak|Ortalama|Hedef VM 'ye gönderilen pingler için gidiş dönüş süresi|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Yes|VM 'ye ping işlemi başarısız oldu|Yüzde|Ortalama|Hedef VM 'nin gönderilen toplam ping Pinglerine yönelik başarısız Ping sayısı yüzdesi|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnetworkvirtualrouters"></a>Microsoft. Network/Virtualyönlendiriciler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Peeringavaılabılıty|Yes|BGP kullanılabilirliği|Yüzde|Ortalama|VirtualRouter ile uzak eşleri arasında BGP kullanılabilirliği|Eşdüzey hizmet sağlayıcı|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/Vpngateway 'ler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Ağ Geçidi S2S bant genişliği|BytesPerSecond|Ortalama|Bayt/saniye cinsinden bir ağ geçidinin ortalama siteden siteye bant genişliği|Boyut yok|
|TunnelAverageBandwidth|Yes|Tünel Bant Genişliği|BytesPerSecond|Ortalama|Bir tünelin bayt/saniye cinsinden ortalama bant genişliği|ConnectionName, Remoteıp|
|TunnelEgressBytes|Yes|Tünel Çıkış Baytları|Bayt|Toplam|Bir tünelin giden bayt sayısı|ConnectionName, Remoteıp|
|TunnelEgressPacketDropTSMismatch|Yes|Tünel Çıkış TS Uyumsuzluğu Paket Bırakma|Count|Toplam|Bir tünelin trafik seçiciyle eşleşmeyen giden paket bırakma sayısı|ConnectionName, Remoteıp|
|TunnelEgressPackets|Yes|Tünel Çıkış Paketleri|Count|Toplam|Bir tünelin giden paket sayısı|ConnectionName, Remoteıp|
|TunnelIngressBytes|Yes|Tünel Giriş Baytları|Bayt|Toplam|Bir tünelin gelen bayt sayısı|ConnectionName, Remoteıp|
|TunnelIngressPacketDropTSMismatch|Yes|Tünel Giriş TS Uyumsuzluğu Paket Bırakma|Count|Toplam|Bir tünelin trafik seçiciyle eşleşmeyen gelen paket bırakma sayısı|ConnectionName, Remoteıp|
|TunnelIngressPackets|Yes|Tünel giriş paketleri|Count|Toplam|Bir tünelin gelen paket sayısı|ConnectionName, Remoteıp|
|Tünelnatallocations konumları|No|Tünel NAT ayırmaları|Count|Toplam|Bir tünelde NAT kuralı için ayırma sayısı|NatRule, ConnectionName, Remoteıp|
|Tünelde Tedbytes|No|Tünel Içindeki baytlar|Bayt|Toplam|Bir NAT kuralı tarafından tünele gelen bayt sayısı |NatRule, ConnectionName, Remoteıp|
|Tünelnatedpackets|No|Tünel tutulan paketler|Count|Toplam|Bir NAT kuralı tarafından tünele gelen paketlerin sayısı|NatRule, ConnectionName, Remoteıp|
|Tünelnatflowcount|No|NAT akışları tüneli|Count|Toplam|Bir tünelde akış türüne ve NAT kuralına göre NAT akışı sayısı|NatRule, ConnectionName, Remoteıp, FlowType|
|Tünelnatpacketdrop|No|Tünel NAT paketi düşü|Count|Toplam|Bırakma türü ve NAT kuralı tarafından bırakılan bir tünelde bulunan paketlerin sayısı|NatRule, ConnectionName, Remoteıp, DropType|
|Tünelde sente bayt|No|Tünel ters çevrilmiş baytlar|Bayt|Toplam|Bir NAT kuralı tarafından bir tünelde ters çevrilmiş bayt sayısı|NatRule, ConnectionName, Remoteıp|
|Tünel yankı paketleri|No|Ters çevrilmiş paketleri tünel|Count|Toplam|Bir NAT kuralı tarafından ters çevrilmiş bir Tüneldeki paket sayısı|NatRule, ConnectionName, Remoteıp|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub 'Lar

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|gelen|Yes|Gelen İletiler|Count|Toplam|Başarılı bir gönderme API çağrısı sayısı. |Boyut yok|
|gelen. tüm. failedistekleri|Yes|Tüm gelen başarısız Istekler|Count|Toplam|Bir Bildirim Hub 'ı için toplam gelen başarısız istek sayısı|Boyut yok|
|gelen. ALL. Requests|Yes|Tüm gelen Istekler|Count|Toplam|Bir Bildirim Hub 'ı için toplam gelen istek sayısı|Boyut yok|
|gelen. zamanlandı|Yes|Zamanlanan anında Iletme bildirimleri gönderildi|Count|Toplam|Zamanlanan anında Iletme bildirimleri Iptal edildi|Boyut yok|
|gelen. zamanlandı. iptal|Yes|Zamanlanan anında Iletme bildirimleri Iptal edildi|Count|Toplam|Zamanlanan anında Iletme bildirimleri Iptal edildi|Boyut yok|
|yükleme. tümü|Yes|Yükleme yönetimi Işlemleri|Count|Toplam|Yükleme yönetimi Işlemleri|Boyut yok|
|yükleme. Delete|Yes|Yükleme Işlemlerini silme|Count|Toplam|Yükleme Işlemlerini silme|Boyut yok|
|yükleme. Get|Yes|Yükleme Işlemlerini al|Count|Toplam|Yükleme Işlemlerini al|Boyut yok|
|yükleme. Patch|Yes|Düzeltme eki yükleme Işlemleri|Count|Toplam|Düzeltme eki yükleme Işlemleri|Boyut yok|
|yükleme. upsert|Yes|Yükleme Işlemlerini oluşturma veya güncelleştirme|Count|Toplam|Yükleme Işlemlerini oluşturma veya güncelleştirme|Boyut yok|
|notificationhub. gönderimleri|Yes|Tüm giden bildirimler|Count|Toplam|Bildirim Hub 'ının tüm giden bildirimleri|Boyut yok|
|giden. allpns. badorexpiredchannel|Yes|Hatalı veya süre dolma kanalı hataları|Count|Toplam|Kayıttaki kanal/belirteç/RegistrationId 'nin geçerliliği aşıldığı veya geçersiz olduğu için başarısız olan gönderim sayısı.|Boyut yok|
|giden. allpns. channelerror|Yes|Kanal hataları|Count|Toplam|Kanal geçersiz olduğu için doğru uygulamayla ilişkili olmayan veya geçerliliği aşıldığı için başarısız olan gönderim sayısı.|Boyut yok|
|giden. allpns. ınvalidpayload|Yes|Yük hataları|Count|Toplam|PNS hatalı yük hatası döndürdüğünden başarısız olan gönderim sayısı.|Boyut yok|
|giden. allpns. pnserror|Yes|Dış bildirim sistemi hataları|Count|Toplam|PNS ile iletişim kurulurken bir sorun oluştuğundan başarısız olan gönderim sayısı (kimlik doğrulama sorunları hariç).|Boyut yok|
|giden. allpns. Success|Yes|Başarılı bildirimler|Count|Toplam|Tüm başarılı bildirimlerin sayısı.|Boyut yok|
|giden. APNs. badchannel|Yes|APNS geçersiz kanal hatası|Count|Toplam|Belirteç geçersiz olduğu için başarısız olan gönderim sayısı (APNS durum kodu: 8).|Boyut yok|
|giden. APNs. expiredchannel|Yes|APNS süre dolma kanalı hatası|Count|Toplam|APNS geri bildirim kanalı tarafından geçersiz kılınan belirteç sayısı.|Boyut yok|
|giden. APNs. ınvalidcredentials|Yes|APNS yetkilendirme hataları|Count|Toplam|PNS belirtilen kimlik bilgilerini kabul etmediğinden veya kimlik bilgileri engellendiğinden başarısız olan gönderim sayısı.|Boyut yok|
|giden. APNs. ınvalidnotificationsize|Yes|APNS geçersiz bildirim boyutu hatası|Count|Toplam|Yük çok büyük olduğu için başarısız olan gönderim sayısı (APNS durum kodu: 7).|Boyut yok|
|giden. APNs. pnserror|Yes|APNS hataları|Count|Toplam|APNS ile iletişim kurulurken oluşan hatalardan dolayı başarısız olan gönderim sayısı.|Boyut yok|
|giden. APNs. Success|Yes|APNS başarılı bildirimler|Count|Toplam|Tüm başarılı bildirimlerin sayısı.|Boyut yok|
|giden. gcm. authenticationerror|Yes|GCM kimlik doğrulama hataları|Count|Toplam|PNS belirtilen kimlik bilgilerini kabul etmediğinden başarısız olan gönderim sayısı, kimlik bilgilerinin engellenmesi veya SenderId uygulamada doğru yapılandırılmamış (GCM sonucu: Mismatchedsenderıd).|Boyut yok|
|giden. gcm. badchannel|Yes|GCM hatalı kanal hatası|Count|Toplam|Kayıttaki RegistrationId tanınmadığı için başarısız olan gönderim sayısı (GCM sonucu: geçersiz kayıt).|Boyut yok|
|giden. gcm. expiredchannel|Yes|GCM süre aşımına uğradı kanal hatası|Count|Toplam|Kayıttaki RegistrationId 'nin zaman aşımına uğradığından başarısız olan gönderim sayısı (GCM sonucu: NotRegistered).|Boyut yok|
|giden. gcm. ınvalidcredentials|Yes|GCM yetkilendirme hataları (geçersiz kimlik bilgileri)|Count|Toplam|PNS belirtilen kimlik bilgilerini kabul etmediğinden veya kimlik bilgileri engellendiğinden başarısız olan gönderim sayısı.|Boyut yok|
|giden. gcm. ınvalidnotificationformat|Yes|GCM geçersiz bildirim biçimi|Count|Toplam|Yük doğru biçimlendirilmediği için başarısız olan gönderim sayısı (GCM sonucu: ınvaliddatakey veya ınvalidttl).|Boyut yok|
|giden. gcm. ınvalidnotificationsize|Yes|GCM geçersiz bildirim boyutu hatası|Count|Toplam|Yük çok büyük olduğu için başarısız olan gönderim sayısı (GCM sonucu: Messagetoobıg).|Boyut yok|
|giden. gcm. pnserror|Yes|GCM hataları|Count|Toplam|GCM ile iletişim kurulurken oluşan hatalardan dolayı başarısız olan gönderim sayısı.|Boyut yok|
|giden. gcm. Success|Yes|GCM başarılı bildirimleri|Count|Toplam|Tüm başarılı bildirimlerin sayısı.|Boyut yok|
|giden. gcm. kısıtlanıyor|Yes|GCM kısıtlanan bildirimler|Count|Toplam|GCM tarafından kısıtlanan bu uygulamayı (GCM durum kodu: 501-599 veya sonuç: kullanılamıyor) nedeniyle başarısız olan gönderim sayısı.|Boyut yok|
|giden. gcm. yanlışlıkla gchannel|Yes|GCM yanlış kanal hatası|Count|Toplam|Kayıttaki RegistrationId geçerli uygulamayla ilişkili olmadığından başarısız olan gönderim sayısı (GCM sonucu: ınvalidpackagename).|Boyut yok|
|giden. MPNS. authenticationerror|Yes|MPNS kimlik doğrulama hataları|Count|Toplam|PNS belirtilen kimlik bilgilerini kabul etmediğinden veya kimlik bilgileri engellendiğinden başarısız olan gönderim sayısı.|Boyut yok|
|giden. MPNS. badchannel|Yes|MPNS hatalı kanal hatası|Count|Toplam|Kayıttaki Channelurı tanınmadığı için başarısız olan gönderim sayısı (MPNS durumu: 404 bulunamadı).|Boyut yok|
|giden. MPNS. channelconnected bağlantısı kesildi|Yes|MPNS kanalının bağlantısı kesildi|Count|Toplam|Kayıttaki Channelurı bağlantısı kesildiğinden başarısız olan gönderim sayısı (MPNS durumu: 412 bulunamadı).|Boyut yok|
|giden. MPNS. bırakıldı|Yes|MPNS bırakılan bildirimler|Count|Toplam|MPNS tarafından bırakılan gönderim sayısı (MPNS yanıt üst bilgisi: X-NotificationStatus: QueueFull veya gizlendi).|Boyut yok|
|giden. MPNS. ınvalidcredentials|Yes|MPNS geçersiz kimlik bilgileri|Count|Toplam|PNS belirtilen kimlik bilgilerini kabul etmediğinden veya kimlik bilgileri engellendiğinden başarısız olan gönderim sayısı.|Boyut yok|
|giden. MPNS. ınvalidnotificationformat|Yes|MPNS geçersiz bildirim biçimi|Count|Toplam|Bildirimin yükü çok büyük olduğu için başarısız olan gönderim sayısı.|Boyut yok|
|giden. MPNS. pnserror|Yes|MPNS hataları|Count|Toplam|MPNS ile iletişim kurulurken oluşan hatalardan dolayı başarısız olan gönderim sayısı.|Boyut yok|
|giden. MPNS. Success|Yes|MPNS başarılı bildirimleri|Count|Toplam|Tüm başarılı bildirimlerin sayısı.|Boyut yok|
|giden. MPNS. kısıtlanıyor|Yes|MPNS kısıtlanan bildirimler|Count|Toplam|MPNS bu uygulamayı azaltdığı için başarısız olan gönderim sayısı (WNS MPNS: 406 kabul edilemez).|Boyut yok|
|giden. WNS. authenticationerror|Yes|WNS kimlik doğrulama hataları|Count|Toplam|Windows Live geçersiz kimlik bilgileri veya yanlış belirteçle iletişim kurarken oluşan hatalar nedeniyle bildirim teslim edilemiyor.|Boyut yok|
|giden. WNS. badchannel|Yes|WNS hatalı kanal hatası|Count|Toplam|Kayıttaki Channelurı tanınmadığı için başarısız olan gönderim sayısı (WNS durumu: 404 bulunamadı).|Boyut yok|
|giden. WNS. channelconnected bağlantısı kesildi|Yes|WNS kanal bağlantısı kesildi|Count|Toplam|Kayıttaki Channelurı kısıtlandığı için bildirim bırakıldı (WNS yanıt üst bilgisi: X-WNS-DeviceConnectionStatus: bağlantısı kesildi).|Boyut yok|
|giden. WNS. channelkısıtlanıyor|Yes|WNS kanal kısıtlandı|Count|Toplam|Kayıttaki Channelurı kısıtlandığı için bildirim bırakıldı (WNS yanıt üst bilgisi: X-WNS-NotificationStatus: Channelkısıtlanıyor).|Boyut yok|
|giden. WNS. bırakıldı|Yes|WNS bırakılan bildirimler|Count|Toplam|Kayıttaki Channelurı kısıtlandığı için bildirim bırakıldı (X-WNS-NotificationStatus: bırakıldı ancak X-WNS-DeviceConnectionStatus: bağlantısı kesildi).|Boyut yok|
|giden. WNS. expiredchannel|Yes|WNS süre dolduğunda kanal hatası|Count|Toplam|ChannelURI 'nin geçerliliği aşıldığı için başarısız olan gönderim sayısı (WNS durumu: 410 gitti).|Boyut yok|
|giden. WNS. ınvalidcredentials|Yes|WNS yetkilendirme hataları (geçersiz kimlik bilgileri)|Count|Toplam|PNS belirtilen kimlik bilgilerini kabul etmediğinden veya kimlik bilgileri engellendiğinden başarısız olan gönderim sayısı. (Windows Live kimlik bilgilerini tanımaz).|Boyut yok|
|giden. WNS. ınvalidnotificationformat|Yes|WNS geçersiz bildirim biçimi|Count|Toplam|Bildirimin biçimi geçersiz (WNS durumu: 400). WNS 'nin tüm geçersiz yükleri reddetmediğini unutmayın.|Boyut yok|
|giden. WNS. ınvalidnotificationsize|Yes|WNS geçersiz bildirim boyutu hatası|Count|Toplam|Bildirim yükü çok büyük (WNS durumu: 413).|Boyut yok|
|giden. WNS. ınvalidtoken|Yes|WNS yetkilendirme hataları (geçersiz belirteç)|Count|Toplam|WNS öğesine sunulan belirteç geçerli değil (WNS Status: 401 Yetkisiz).|Boyut yok|
|giden. WNS. pnserror|Yes|WNS hataları|Count|Toplam|Bildirim, WNS ile iletişim kuran hatalar nedeniyle teslim edilemiyor.|Boyut yok|
|giden. WNS. Success|Yes|WNS başarılı bildirimleri|Count|Toplam|Tüm başarılı bildirimlerin sayısı.|Boyut yok|
|giden. WNS. kısıtlanıyor|Yes|WNS kısıtlanıyor bildirimleri|Count|Toplam|WNS bu uygulamayı azaltdığı için başarısız olan gönderim sayısı (WNS durumu: 406 kabul edilemez).|Boyut yok|
|giden. WNS. tokenproviderunreachable|Yes|WNS yetkilendirme hataları (ulaşılamıyor)|Count|Toplam|Windows Live ulaşılamıyor.|Boyut yok|
|giden. WNS. yanlışlıkla gtoken|Yes|WNS yetkilendirme hataları (yanlış belirteç)|Count|Toplam|WNS öğesine sunulan belirteç geçerli, ancak başka bir uygulama için (WNS durumu: 403 Yasak). Kayıttaki Channelurı başka bir uygulamayla ilişkiliyse bu durum oluşabilir. İstemci uygulamasının kimlik bilgileri Bildirim Hub 'ında olan aynı uygulamayla ilişkilendirildiğinden emin olun.|Boyut yok|
|kayıt. tümü|Yes|Kayıt Işlemleri|Count|Toplam|Tüm başarılı kayıt işlemlerinin sayısı (güncelleştirme sorguları ve silmeleri oluşturma). |Boyut yok|
|kayıt. oluştur|Yes|Kayıt oluşturma Işlemleri|Count|Toplam|Tüm başarılı kayıt oluşturma işlemlerinin sayısı.|Boyut yok|
|kayıt. Sil|Yes|Kayıt silme Işlemleri|Count|Toplam|Tüm başarılı kayıt silme işlemlerinin sayısı.|Boyut yok|
|kayıt. Get|Yes|Kayıt okuma Işlemleri|Count|Toplam|Tüm başarılı kayıt sorgularının sayısı.|Boyut yok|
|kayıt. güncelleştirme|Yes|Kayıt güncelleştirme Işlemleri|Count|Toplam|Tüm başarılı kayıt güncelleştirmelerinin sayısı.|Boyut yok|
|zamanlandı. bekliyor|Yes|Bekleyen zamanlanmış bildirimler|Count|Toplam|Bekleyen zamanlanmış bildirimler|Boyut yok|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. Operationalınsights/çalışma alanları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilir% Average_ bellek|Yes|% Kullanılabilir bellek|Count|Ortalama|Kullanılabilir% Average_ bellek|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% kullanılabilir takas alanı|Yes|% Kullanılabilir takas alanı|Count|Ortalama|Average_% kullanılabilir takas alanı|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|% Average_ kaydedilmiş bayt kullanımda|Yes|Kullanılan kaydedilmiş bayt yüzdesi|Count|Ortalama|% Average_ kaydedilmiş bayt kullanımda|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% DPC Zamanı|Yes|% DPC Zamanı|Count|Ortalama|Average_% DPC Zamanı|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% boş ınomdes|Yes|% Boş ınomdes|Count|Ortalama|Average_% boş ınomdes|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|% Average_ boş alan|Yes|% Boş alan|Count|Ortalama|% Average_ boş alan|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% boşta kalma süresi|Yes|% Boşta kalma süresi|Count|Ortalama|Average_% boşta kalma süresi|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Kesme zamanı Average_%|Yes|Kesme zamanı yüzdesi|Count|Ortalama|Kesme zamanı Average_%|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% GÇ bekleme süresi|Yes|% GÇ bekleme süresi|Count|Ortalama|Average_% GÇ bekleme süresi|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% Iyi zaman|Yes|% İyi zaman|Count|Ortalama|Average_% Iyi zaman|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% ayrıcalıklı zaman|Yes|% Ayrıcalıklı zaman|Count|Ortalama|Average_% ayrıcalıklı zaman|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% Işlemci zamanı|Yes|% İşlemci zamanı|Count|Ortalama|Average_% Işlemci zamanı|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% kullanılan ınomdes|Yes|% Kullanılan ınomdes|Count|Ortalama|Average_% kullanılan ınomdes|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Kullanılan% Average_ bellek|Yes|% Kullanılan bellek|Count|Ortalama|Kullanılan% Average_ bellek|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Kullanılan Average_ alan yüzdesi|Yes|% Kullanılan alan|Count|Ortalama|Kullanılan Average_ alan yüzdesi|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% kullanılan takas alanı|Yes|% Kullanılan takas alanı|Count|Ortalama|Average_% kullanılan takas alanı|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_% Kullanıcı Zamanı|Yes|% Kullanıcı Zamanı|Count|Ortalama|Average_% Kullanıcı Zamanı|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Available MBayt|Yes|Kullanılabilir MBayt|Count|Ortalama|Average_Available MBayt|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Available MBayt bellek|Yes|Kullanılabilir MBayt belleği|Count|Ortalama|Average_Available MBayt bellek|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Available MBayt takas|Yes|Kullanılabilir MBayt takas|Count|Ortalama|Average_Available MBayt takas|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Avg. Disk sn/Okuma|Yes|Ortalama Disk sn/okuma|Count|Ortalama|Average_Avg. Disk sn/Okuma|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Avg. Disk sn/Aktarım|Yes|Ortalama Disk sn/Aktarım|Count|Ortalama|Average_Avg. Disk sn/Aktarım|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Avg. Disk sn/Yazma|Yes|Ortalama Disk sn/yazma|Count|Ortalama|Average_Avg. Disk sn/Yazma|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Alınan Average_Bytes/sn|Yes|Alınan bayt/sn|Count|Ortalama|Alınan Average_Bytes/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Gönderilen Average_Bytes/sn|Yes|Gönderilen bayt/sn|Count|Ortalama|Gönderilen Average_Bytes/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Toplam Average_Bytes/sn|Yes|Toplam bayt/sn|Count|Ortalama|Toplam Average_Bytes/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Current disk kuyruğu uzunluğu|Yes|Geçerli disk sırası uzunluğu|Count|Ortalama|Average_Current disk kuyruğu uzunluğu|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Disk okunan bayt/sn|Yes|Disk okuma bayt/sn|Count|Ortalama|Average_Disk okunan bayt/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Okuma/sn Average_Disk|Yes|Disk Okuma/sn|Count|Ortalama|Okuma/sn Average_Disk|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Disk aktarım sayısı/sn|Yes|Disk aktarımı/sn|Count|Ortalama|Average_Disk aktarım sayısı/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Disk yazma bayt/sn|Yes|Disk yazma bayt/sn|Count|Ortalama|Average_Disk yazma bayt/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Disk yazma/sn|Yes|Disk yazma/sn|Count|Ortalama|Average_Disk yazma/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Free megabayt|Yes|Boş megabayt|Count|Ortalama|Average_Free megabayt|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Fiziksel bellek Average_Free|Yes|Boş fiziksel bellek|Count|Ortalama|Fiziksel bellek Average_Free|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Disk belleği dosyalarında Average_Free alanı|Yes|Disk belleği dosyalarındaki boş alan|Count|Ortalama|Disk belleği dosyalarında Average_Free alanı|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Sanal bellek Average_Free|Yes|Boş sanal bellek|Count|Ortalama|Sanal bellek Average_Free|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Logical disk bayt/sn|Yes|Mantıksal disk bayt/sn|Count|Ortalama|Average_Logical disk bayt/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Okuma/sn Average_Page|Yes|Sayfa Okuma/sn|Count|Ortalama|Okuma/sn Average_Page|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Page yazma/sn|Yes|Sayfa yazma/sn|Count|Ortalama|Average_Page yazma/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Pages/sn|Yes|Sayfa/sn|Count|Ortalama|Average_Pages/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Pct ayrıcalıklı saat|Yes|Ayrıcalıklı zaman yüzdesi|Count|Ortalama|Average_Pct ayrıcalıklı saat|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Pct Kullanıcı saati|Yes|Kullanım süresi yüzdesi|Count|Ortalama|Average_Pct Kullanıcı saati|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Physical disk bayt/sn|Yes|Fiziksel disk bayt/sn|Count|Ortalama|Average_Physical disk bayt/sn|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Processes|Yes|İşlemler|Count|Ortalama|Average_Processes|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Processor kuyruğu uzunluğu|Yes|İşlemci kuyruğu uzunluğu|Count|Ortalama|Average_Processor kuyruğu uzunluğu|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Disk belleği dosyalarında depolanan Average_Size|Yes|Disk belleği dosyalarında depolanan boyut|Count|Ortalama|Disk belleği dosyalarında depolanan Average_Size|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Total bayt|Yes|Toplam bayt sayısı|Count|Ortalama|Average_Total bayt|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Total alınan bayt|Yes|Alınan toplam bayt|Count|Ortalama|Average_Total alınan bayt|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Total aktarılan baytlar|Yes|Aktarılan toplam bayt sayısı|Count|Ortalama|Average_Total aktarılan baytlar|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Total çakışmaları|Yes|Toplam çakışma sayısı|Count|Ortalama|Average_Total çakışmaları|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Alınan Average_Total paketleri|Yes|Alınan toplam paket sayısı|Count|Ortalama|Alınan Average_Total paketleri|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Iletilen Average_Total paketleri|Yes|Aktarılan toplam paket sayısı|Count|Ortalama|Iletilen Average_Total paketleri|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Total RX hataları|Yes|Toplam RX hatası sayısı|Count|Ortalama|Average_Total RX hataları|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Total TX hataları|Yes|Toplam TX hatası sayısı|Count|Ortalama|Average_Total TX hataları|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Uptime|Yes|Çalışma süresi|Count|Ortalama|Average_Uptime|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Used MBayt alanı değiştirme|Yes|Kullanılan MBayt değiştirme alanı|Count|Ortalama|Average_Used MBayt alanı değiştirme|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Used belleği Kbayt|Yes|Kullanılan bellek Kbayt|Count|Ortalama|Average_Used belleği Kbayt|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Used belleği MBayt|Yes|Kullanılan bellek MBayt|Count|Ortalama|Average_Used belleği MBayt|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Users|Yes|Kullanıcılar|Count|Ortalama|Average_Users|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Average_Virtual paylaşılan bellek|Yes|Sanal paylaşılan bellek|Count|Ortalama|Average_Virtual paylaşılan bellek|Bilgisayar, ObjectName, InstanceName, CounterPath, dir|
|Olay|Yes|Olay|Count|Ortalama|Olay|Kaynak, olay günlüğü, bilgisayar, EventCategory, EventLevel, EventLevelName, EventID|
|Sinyal|Yes|Sinyal|Count|Toplam|Sinyal|Bilgisayar, OSType, sürüm, Sourcecomputerıd|
|Güncelleştir|Yes|Güncelleştir|Count|Ortalama|Güncelleştir|Bilgisayar, ürün, sınıflandırma, UpdateState, Isteğe bağlı, onaylanan|


## <a name="microsoftpeeringpeerings"></a>Microsoft. eşleme/eşlemeler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Yes|Çıkış trafik oranı|BitsPerSecond|Ortalama|Bit/saniye cinsinden çıkış trafiği oranı|ConnectionID, Sessionıp, TrafficClass|
|IngressTrafficRate|Yes|Giriş trafiği oranı|BitsPerSecond|Ortalama|Bit/saniye cinsinden giriş trafiği oranı|ConnectionID, Sessionıp, TrafficClass|
|Sessionavaılabılıty|Yes|Oturum kullanılabilirliği|Count|Ortalama|Eşleme oturumunun kullanılabilirliği|ConnectionID, Sessionıp|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft. eşleme/peeringServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|PrefixLatency|Yes|Ön ek gecikmesi|Mayacak|Ortalama|Ortanca ön ek gecikmesi|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. Powerbiadanmış/kapasiteler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|memory_metric|Yes|Bellek (Gen1)|Bayt|Ortalama|Bellek. A1 için 0-3 GB, a2 için 0-5 GB, a3 için 0-10 GB, A4 için 0-25 GB, A6 için GB ve A6 için 0-50 GB. Yalnızca Power BI Embedded nesil 1 kaynakları için desteklenir.|Boyut yok|
|memory_thrashing_metric|Yes|Bellek kullanımı (veri kümeleri) (Gen1)|Yüzde|Ortalama|Ortalama bellek miktarı. Yalnızca Power BI Embedded nesil 1 kaynakları için desteklenir.|Boyut yok|
|qpu_high_utilization_metric|Yes|QPU High kullanımı (Gen1)|Count|Toplam|Son dakikada QPU yüksek kullanımı, yüksek QPU kullanımı Için 1, aksi durumda 0. Yalnızca Power BI Embedded nesil 1 kaynakları için desteklenir.|Boyut yok|
|QueryDuration|Yes|Sorgu süresi (veri kümeleri) (Gen1)|Mayacak|Ortalama|Son aralıktaki DAX sorgu süresi. Yalnızca Power BI Embedded nesil 1 kaynakları için desteklenir.|Boyut yok|
|QueryPoolJobQueueLength|Yes|Sorgu havuzu Iş kuyruğu uzunluğu (veri kümeleri) (Gen1)|Count|Ortalama|Sorgu iş parçacığı havuzu kuyruğundaki iş sayısı. Yalnızca Power BI Embedded nesil 1 kaynakları için desteklenir.|Boyut yok|


## <a name="microsoftpurviewaccounts"></a>Microsoft. purview/hesapları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Scaniptal edildi|Yes|Tarama Iptal edildi|Count|Toplam|İptal edilen taramaların sayısını belirtir.||
|ScanCompleted|Yes|Tarama tamamlandı|Count|Toplam|Taramanın başarıyla tamamlandığını gösterir.||
|ScanFailed|Yes|Tarama başarısız oldu|Count|Toplam|Başarısız tarama sayısını belirtir.||
|ScanTimeTaken|Yes|Harcanan Tarama süresi|Saniye|Toplam|Saniye cinsinden toplam tarama süresini gösterir.||


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/Namespace

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Count|Toplam|Microsoft. Relay için toplam ActiveConnections.|EntityName|
|ActiveListeners|No|ActiveListeners|Count|Toplam|Microsoft. Relay için toplam ActiveListeners.|EntityName|
|BytesTransferred|Yes|BytesTransferred|Bayt|Toplam|Microsoft. Relay için toplam BytesTransferred.|EntityName|
|ListenerConnections-ClientError|No|ListenerConnections-ClientError|Count|Toplam|Microsoft. Relay için ListenerConnections 'da ClientError.|EntityName, OperationResult|
|ListenerConnections-ServerError|No|ListenerConnections-ServerError|Count|Toplam|Microsoft. Relay için ListenerConnections 'da ServerError.|EntityName, OperationResult|
|ListenerConnections-Success|No|ListenerConnections-Success|Count|Toplam|Microsoft. Relay için başarılı bir ListenerConnections.|EntityName, OperationResult|
|ListenerConnections-TotalRequests|No|ListenerConnections-TotalRequests|Count|Toplam|Microsoft. Relay için toplam ListenerConnections.|EntityName|
|Listenerdisconnect|No|Listenerdisconnect|Count|Toplam|Microsoft. Relay için toplam Listenerdisconnect.|EntityName|
|SenderConnections-ClientError|No|SenderConnections-ClientError|Count|Toplam|Microsoft. Relay için SenderConnections 'ta ClientError.|EntityName, OperationResult|
|SenderConnections-ServerError|No|SenderConnections-ServerError|Count|Toplam|Microsoft. Relay için SenderConnections 'ta ServerError.|EntityName, OperationResult|
|SenderConnections-Success|No|SenderConnections-Success|Count|Toplam|Microsoft. Relay için başarılı SenderConnections.|EntityName, OperationResult|
|SenderConnections-TotalRequests|No|SenderConnections-TotalRequests|Count|Toplam|Microsoft. Relay için toplam SenderConnections isteği.|EntityName|
|Gönderenin bağlantısı kesiliyor|No|Gönderenin bağlantısı kesiliyor|Count|Toplam|Microsoft. Relay için toplam Senderdisconnect.|EntityName|


## <a name="microsoftresourcessubscriptions"></a>Microsoft. resources/abonelikler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Gecikme süresi|Yes|Http gelen Istekleri gecikme verileri|Count|Ortalama|Http gelen Istekleri gecikme verileri|Yöntem, ad alanı, RequestRegion, ResourceType, Microsoft. SubscriptionID|
|Trafik|Yes|Trafik|Count|Ortalama|HTTP trafiği|RequestRegion, StatusCode, StatusCodeClass, ResourceType, Namespace, Microsoft. SubscriptionID|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|SearchLatency|Yes|Arama gecikmesi|Saniye|Ortalama|Arama hizmeti için Ortalama arama gecikmesi|Boyut yok|
|SearchQueriesPerSecond|Yes|Sorgu arama/saniye|Sayaçpersaniye|Ortalama|Arama hizmeti için saniye başına arama sorgusu|Boyut yok|
|Tüm Searchqueriespercentage 'ı kısıtlar|Yes|Kısıtlanmış arama sorguları yüzdesi|Yüzde|Ortalama|Arama hizmeti için kısıtlanan arama sorgularının yüzdesi|Boyut yok|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/ad alanları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Count|Toplam|Microsoft. ServiceBus için toplam etkin bağlantı sayısı.||
|ActiveMessages|No|Bir kuyruktaki/konudaki etkin ileti sayısı.|Count|Ortalama|Bir kuyruktaki/konudaki etkin ileti sayısı.|EntityName|
|ConnectionsClosed|No|Kapatılan Bağlantılar.|Count|Ortalama|Microsoft. ServiceBus için kapatılan bağlantılar.|EntityName|
|ConnectionsOpened|No|Açılan Bağlantılar.|Count|Ortalama|Microsoft. ServiceBus için açılan bağlantılar.|EntityName|
|CPUXNS|No|CPU (kullanım dışı)|Yüzde|Maksimum|Service Bus Premium ad alanı CPU kullanımı ölçümü. Bu ölçüm eşit olarak dağıtılır. Lütfen bunun yerine CPU ölçümünü kullanın (NamespaceCpuUsage).|Çoğaltma|
|DeadletteredMessages|No|Bir kuyruktaki/konudaki kullanılmayan iletilerin sayısı.|Count|Ortalama|Bir kuyruktaki/konudaki kullanılmayan iletilerin sayısı.|EntityName|
|Incomingmessages|Yes|Gelen İletiler|Count|Toplam|Microsoft. ServiceBus için gelen Iletiler.|EntityName|
|Incomingrequests|Yes|Gelen İstekler|Count|Toplam|Microsoft. ServiceBus için gelen Istekler.|EntityName|
|İletiler|No|Kuyruktaki/Konudaki iletilerin sayısı.|Count|Ortalama|Kuyruktaki/Konudaki iletilerin sayısı.|EntityName|
|NamespaceCpuUsage|No|CPU|Yüzde|Maksimum|Service Bus Premium ad alanı CPU kullanımı ölçümü.|Çoğaltma|
|NamespaceMemoryUsage|No|Bellek Kullanımı|Yüzde|Maksimum|Service Bus Premium ad alanı bellek kullanımı ölçümü.|Çoğaltma|
|OutgoingMessages|Yes|Giden İletiler|Count|Toplam|Microsoft. ServiceBus için giden Iletiler.|EntityName|
|ScheduledMessages|No|Bir kuyruktaki/konudaki zamanlanan ileti sayısı.|Count|Ortalama|Bir kuyruktaki/konudaki zamanlanan ileti sayısı.|EntityName|
|ServerErrors|No|Sunucu Hataları.|Count|Toplam|Microsoft. ServiceBus için sunucu hataları.|EntityName, OperationResult|
|Boyut|No|Boyut|Bayt|Ortalama|Bir kuyruğun/konunun bayt cinsinden boyutu.|EntityName|
|SuccessfulRequests|No|Başarılı İstekler|Count|Toplam|Bir ad alanı için toplam başarılı istek sayısı|EntityName, OperationResult|
|OperationName|No|Kısıtlanan İstekler.|Count|Toplam|Microsoft. ServiceBus için kısıtlanan Istekler.|EntityName, OperationResult|
|UserErrors|No|Kullanıcı Hataları.|Count|Toplam|Microsoft. ServiceBus için Kullanıcı hataları.|EntityName, OperationResult|
|WSXNS|No|Bellek kullanımı (kullanım dışı)|Yüzde|Maksimum|Service Bus Premium ad alanı bellek kullanımı ölçümü. Bu ölçüm kullanım dışıdır. Lütfen bunun yerine bellek kullanımı (NamespaceMemoryUsage) ölçüsünü kullanın.|Çoğaltma|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. Servicefabrickafes/uygulamaları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ActualCpu|No|ActualCpu|Count|Ortalama|Milli çekirdekte gerçek CPU kullanımı|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|No|ActualMemory|Bayt|Ortalama|MB cinsinden gerçek bellek kullanımı|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|Ayrılatedcpu|No|Ayrılatedcpu|Count|Ortalama|Milli çekirdekleri içinde bu kapsayıcıya ayrılan CPU|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|Ayrılan bellek|No|Ayrılan bellek|Bayt|Ortalama|Bu kapsayıcıya MB cinsinden ayrılan bellek|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|No|ApplicationStatus|Count|Ortalama|Service Fabric kafes uygulamasının durumu|ApplicationName, durum|
|ContainerStatus|No|ContainerStatus|Count|Ortalama|Service Fabric kafes uygulamasındaki kapsayıcının durumu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, durum|
|Cpukullanımı|No|Cpukullanımı|Yüzde|Ortalama|Ayrılan CPU yüzdesi olarak bu kapsayıcı için CPU kullanımı|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|Memoryutilileştirme|No|Memoryutilileştirme|Yüzde|Ortalama|Ayrılan CPU yüzdesi olarak bu kapsayıcı için CPU kullanımı|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|RestartCount|No|RestartCount|Count|Ortalama|Service Fabric kafes uygulamasındaki bir kapsayıcının yeniden başlatma sayısı|ApplicationName, Status, HizmetAdı, ServiceReplicaName, CodePackageName|
|Servicereperepstatus|No|Servicereperepstatus|Count|Ortalama|Service Fabric kafes uygulamasındaki bir hizmet çoğaltmasının sistem durumu|ApplicationName, Status, HizmetAdı, ServiceReplicaName|
|ServiceStatus|No|ServiceStatus|Count|Ortalama|Service Fabric kafes uygulamasındaki bir hizmetin sistem durumu|ApplicationName, durum, HizmetAdı|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/SignalR

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ConnectionCount|Yes|Bağlantı sayısı|Count|Maksimum|Kullanıcı bağlantısı miktarı.|Uç Nokta|
|Inboundtraffic|Yes|Gelen Trafik|Bayt|Toplam|Hizmetin gelen trafiği|Boyut yok|
|MessageCount|Yes|İleti Sayısı|Count|Toplam|Toplam ileti miktarı.|Boyut yok|
|OutboundTraffic|Yes|Giden Trafik|Bayt|Toplam|Hizmetin giden trafiği|Boyut yok|
|SystemErrors|Yes|Sistem hataları|Yüzde|Maksimum|Sistem hatalarının yüzdesi|Boyut yok|
|UserErrors|Yes|Kullanıcı hataları|Yüzde|Maksimum|Kullanıcı hatalarının yüzdesi|Boyut yok|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft. SignalRService/WebPubSub

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Inboundtraffic|Yes|Gelen Trafik|Bayt|Toplam|Hizmetin gelen trafiği|Boyut yok|
|OutboundTraffic|Yes|Giden Trafik|Bayt|Toplam|Hizmetin giden trafiği|Boyut yok|
|TotalConnectionCount|Yes|Bağlantı sayısı|Count|Maksimum|Kullanıcı bağlantısı miktarı.|Boyut yok|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/ManagedInstances

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Yes|Ortalama CPU yüzdesi|Yüzde|Ortalama|Ortalama CPU yüzdesi|Boyut yok|
|io_bytes_read|Yes|Okunan GÇ baytları|Bayt|Ortalama|Okunan GÇ baytları|Boyut yok|
|io_bytes_written|Yes|Yazılan GÇ baytları|Bayt|Ortalama|Yazılan GÇ baytları|Boyut yok|
|io_requests|Yes|GÇ istek sayısı|Count|Ortalama|GÇ istek sayısı|Boyut yok|
|reserved_storage_mb|Yes|Ayrılan depolama alanı|Count|Ortalama|Ayrılan depolama alanı|Boyut yok|
|storage_space_used_mb|Yes|Kullanılan depolama alanı|Count|Ortalama|Kullanılan depolama alanı|Boyut yok|
|virtual_core_count|Yes|Sanal çekirdek sayısı|Count|Ortalama|Sanal çekirdek sayısı|Boyut yok|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|active_queries|Yes|Etkin sorgular|Count|Toplam|Tüm iş yükü grupları genelinde etkin sorgular. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|allocated_data_storage|Yes|Ayrılan veri alanı|Bayt|Ortalama|Ayrılan veri depolama alanı. Veri ambarlarında geçerli değildir.|Boyut yok|
|app_cpu_billed|Yes|Faturalandırılan uygulama CPU 'SU|Count|Toplam|Uygulama CPU 'SU faturalandırılır. Sunucusuz veritabanları için geçerlidir.|Boyut yok|
|app_cpu_percent|Yes|Uygulama CPU yüzdesi|Yüzde|Ortalama|Uygulama CPU yüzdesi. Sunucusuz veritabanları için geçerlidir.|Boyut yok|
|app_memory_percent|Yes|Uygulama belleği yüzdesi|Yüzde|Ortalama|Uygulama belleği yüzdesi. Sunucusuz veritabanları için geçerlidir.|Boyut yok|
|base_blob_size_bytes|Yes|Temel BLOB depolama boyutu|Bayt|Maksimum|Temel BLOB depolama boyutu. Hiper ölçekli veritabanları için geçerlidir.|Boyut yok|
|blocked_by_firewall|Yes|Güvenlik Duvarı Tarafından Engellendi|Count|Toplam|Güvenlik Duvarı Tarafından Engellendi|Boyut yok|
|cache_hit_percent|Yes|İsabetli önbellek okuması yüzdesi|Yüzde|Maksimum|İsabetli önbellek okuması yüzdesi. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|cache_used_percent|Yes|Önbellek kullanılan yüzde|Yüzde|Maksimum|Önbellek kullanım yüzdesi. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|connection_failed|Yes|Başarısız Bağlantılar|Count|Toplam|Başarısız Bağlantılar|Boyut yok|
|connection_successful|Yes|Başarılı bağlantılar|Count|Toplam|Başarılı bağlantılar|Boyut yok|
|cpu_limit|Yes|CPU sınırı|Count|Ortalama|CPU sınırı. Sanal çekirdek tabanlı veritabanları için geçerlidir.|Boyut yok|
|cpu_percent|Yes|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|Boyut yok|
|cpu_used|Yes|Kullanılan CPU|Count|Ortalama|Kullanılan CPU. Sanal çekirdek tabanlı veritabanları için geçerlidir.|Boyut yok|
|Çözül|Yes|Kilitlenmeler|Count|Toplam|Çık. Veri ambarlarında geçerli değildir.|Boyut yok|
|diff_backup_size_bytes|Yes|Değişiklik yedekleme depolama boyutu|Bayt|Maksimum|Toplu fark yedekleme depolama boyutu. Sanal çekirdek tabanlı veritabanları için geçerlidir. Hiper ölçekli veritabanları için geçerli değildir.|Boyut yok|
|dtu_consumption_percent|Yes|DTU yüzdesi|Yüzde|Ortalama|DTU yüzdesi. DTU tabanlı veritabanları için geçerlidir.|Boyut yok|
|dtu_limit|Yes|DTU sınırı|Count|Ortalama|DTU sınırı. DTU tabanlı veritabanları için geçerlidir.|Boyut yok|
|dtu_used|Yes|Kullanılan DTU|Count|Ortalama|DTU kullanıldı. DTU tabanlı veritabanları için geçerlidir.|Boyut yok|
|dwu_consumption_percent|Yes|DWU yüzdesi|Yüzde|Maksimum|DWU yüzdesi. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|dwu_limit|Yes|DWU sınırı|Count|Maksimum|DWU sınırı. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|dwu_used|Yes|Kullanılan DWU|Count|Maksimum|DWU kullanıldı. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|full_backup_size_bytes|Yes|Tam yedekleme depolama boyutu|Bayt|Maksimum|Toplu tam yedekleme depolama boyutu. Sanal çekirdek tabanlı veritabanları için geçerlidir. Hiper ölçekli veritabanları için geçerli değildir.|Boyut yok|
|local_tempdb_usage_percent|Yes|Yerel tempdb yüzdesi|Yüzde|Ortalama|Yerel tempdb yüzdesi. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|log_backup_size_bytes|Yes|Günlük yedekleme depolama boyutu|Bayt|Maksimum|Toplu günlük yedekleme depolama boyutu. Sanal çekirdek tabanlı ve hiper ölçekli veritabanları için geçerlidir.|Boyut yok|
|log_write_percent|Yes|Günlük GÇ yüzdesi|Yüzde|Ortalama|Günlük GÇ yüzdesi. Veri ambarlarında geçerli değildir.|Boyut yok|
|memory_usage_percent|Yes|Bellek yüzdesi|Yüzde|Maksimum|Bellek yüzdesi. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|physical_data_read_percent|Yes|Veri G/Ç yüzdesi|Yüzde|Ortalama|Veri G/Ç yüzdesi|Boyut yok|
|queued_queries|Yes|Kuyruğa alınmış sorgular|Count|Toplam|Tüm iş yükü gruplarında sıraya alınmış sorgular. Yalnızca veri ambarlarında geçerlidir.|Boyut yok|
|sessions_percent|Yes|Oturum yüzdesi|Yüzde|Ortalama|Oturum yüzdesi. Veri ambarlarında geçerli değildir.|Boyut yok|
|snapshot_backup_size_bytes|Yes|Anlık görüntü yedekleme depolama boyutu|Bayt|Maksimum|Birikmeli anlık görüntü yedekleme depolama boyutu. Hiper ölçekli veritabanları için geçerlidir.|Boyut yok|
|sqlserver_process_core_percent|Yes|SQL Server işlem çekirdeği yüzdesi|Yüzde|Maksimum|SQL DB işleminin yüzdesi olarak CPU kullanımı. Veri ambarlarında geçerli değildir.|Boyut yok|
|sqlserver_process_memory_percent|Yes|SQL Server işlem belleği yüzdesi|Yüzde|Maksimum|SQL DB işleminin yüzdesi olarak bellek kullanımı. Veri ambarlarında geçerli değildir.|Boyut yok|
|depolama|Yes|Kullanılan veri alanı|Bayt|Maksimum|Kullanılan veri alanı. Veri ambarlarında geçerli değildir.|Boyut yok|
|storage_percent|Yes|Kullanılan veri alanı yüzdesi|Yüzde|Maksimum|Kullanılan veri alanı yüzdesi. Veri ambarları veya hiper ölçekli veritabanları için geçerli değildir.|Boyut yok|
|tempdb_data_size|Yes|Tempdb veri dosyası boyutu kilobayt|Count|Maksimum|Tempdb veri dosyalarında kilobayt cinsinden kullanılan alan. Veri ambarlarında geçerli değildir.|Boyut yok|
|tempdb_log_size|Yes|Tempdb günlük dosyası boyutu kilobayt|Count|Maksimum|Tempdb işlem günlük dosyasında kilobayt cinsinden kullanılan alan. Veri ambarlarında geçerli değildir.|Boyut yok|
|tempdb_log_used_percent|Yes|Kullanılan tempdb günlüğü yüzdesi|Yüzde|Maksimum|Tempdb işlem günlük dosyasında kullanılan alan yüzdesi. Veri ambarlarında geçerli değildir.|Boyut yok|
|wlg_active_queries|Yes|İş yükü grubu etkin sorguları|Count|Toplam|İş yükü grubu içindeki etkin sorgular. Yalnızca veri ambarlarında geçerlidir.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Yes|İş yükü grubu sorgu zaman aşımları|Count|Toplam|İş yükü grubu için zaman aşımına uğramış sorgular. Yalnızca veri ambarlarında geçerlidir.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Yes|Sistem iş yükü grubuna göre ayırma yüzdesi|Yüzde|Maksimum|Tüm iş yükü grubu başına sistemin tamamına göre ayrılan kaynak yüzdesi. Yalnızca veri ambarlarında geçerlidir.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Yes|Sınır kaynağına göre iş yükü grubu ayırma yüzdesi|Yüzde|Maksimum|Her iş yükü grubu için belirtilen sınır kaynağına göre ayrılan kaynak yüzdesi. Yalnızca veri ambarlarında geçerlidir.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Yes|Etkin uç kaynak yüzdesi|Yüzde|Maksimum|Diğer iş yükü grupları için ayrılan en düşük kaynak yüzdesine sahip olan iş yükü grubu için izin verilen kaynakların yüzdesine yönelik sabit sınır. Yalnızca veri ambarlarında geçerlidir.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Yes|Geçerli Min kaynak yüzdesi|Yüzde|Maksimum|Hizmet düzeyi en düşük hesaba katılarak iş yükü grubu için ayrılan ve yalıtılmış kaynakların minimum yüzdesi. Yalnızca veri ambarlarında geçerlidir.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Yes|İş yükü grubu sıraya alınmış sorgular|Count|Toplam|İş yükü grubu içindeki sıraya alınmış sorgular. Yalnızca veri ambarlarında geçerlidir.|WorkloadGroupName, IsUserDefined|
|workers_percent|Yes|Çalışan yüzdesi|Yüzde|Ortalama|Çalışan yüzdesi. Veri ambarlarında geçerli değildir.|Boyut yok|
|xtp_storage_percent|Yes|In-Memory OLTP depolama alanı yüzdesi|Yüzde|Ortalama|OLTP depolama yüzdesini In-Memory. Veri ambarlarında geçerli değildir.|Boyut yok|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|allocated_data_storage|Yes|Ayrılan veri alanı|Bayt|Ortalama|Ayrılan veri alanı|Boyut yok|
|allocated_data_storage_percent|Yes|Ayrılan veri alanı yüzdesi|Yüzde|Maksimum|Ayrılan veri alanı yüzdesi|Boyut yok|
|cpu_limit|Yes|CPU sınırı|Count|Ortalama|CPU sınırı. Sanal çekirdek tabanlı elastik havuzlar için geçerlidir.|Boyut yok|
|cpu_percent|Yes|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|Boyut yok|
|cpu_used|Yes|Kullanılan CPU|Count|Ortalama|Kullanılan CPU. Sanal çekirdek tabanlı elastik havuzlar için geçerlidir.|Boyut yok|
|database_allocated_data_storage|No|Ayrılan veri alanı|Bayt|Ortalama|Ayrılan veri alanı|DatabaseResourceId|
|database_cpu_limit|No|CPU sınırı|Count|Ortalama|CPU sınırı|DatabaseResourceId|
|database_cpu_percent|No|CPU yüzdesi|Yüzde|Ortalama|CPU yüzdesi|DatabaseResourceId|
|database_cpu_used|No|Kullanılan CPU|Count|Ortalama|Kullanılan CPU|DatabaseResourceId|
|database_dtu_consumption_percent|No|DTU yüzdesi|Yüzde|Ortalama|DTU yüzdesi|DatabaseResourceId|
|database_eDTU_used|No|eDTU kullanıldı|Count|Ortalama|eDTU kullanıldı|DatabaseResourceId|
|database_log_write_percent|No|Günlük GÇ yüzdesi|Yüzde|Ortalama|Günlük GÇ yüzdesi|DatabaseResourceId|
|database_physical_data_read_percent|No|Veri G/Ç yüzdesi|Yüzde|Ortalama|Veri G/Ç yüzdesi|DatabaseResourceId|
|database_sessions_percent|No|Oturum yüzdesi|Yüzde|Ortalama|Oturum yüzdesi|DatabaseResourceId|
|database_storage_used|No|Kullanılan veri alanı|Bayt|Ortalama|Kullanılan veri alanı|DatabaseResourceId|
|database_workers_percent|No|Çalışan yüzdesi|Yüzde|Ortalama|Çalışan yüzdesi|DatabaseResourceId|
|dtu_consumption_percent|Yes|DTU yüzdesi|Yüzde|Ortalama|DTU yüzdesi. DTU tabanlı elastik havuzlar için geçerlidir.|Boyut yok|
|eDTU_limit|Yes|eDTU sınırı|Count|Ortalama|eDTU sınırı. DTU tabanlı elastik havuzlar için geçerlidir.|Boyut yok|
|eDTU_used|Yes|eDTU kullanıldı|Count|Ortalama|eDTU kullanıldı. DTU tabanlı elastik havuzlar için geçerlidir.|Boyut yok|
|log_write_percent|Yes|Günlük GÇ yüzdesi|Yüzde|Ortalama|Günlük GÇ yüzdesi|Boyut yok|
|physical_data_read_percent|Yes|Veri G/Ç yüzdesi|Yüzde|Ortalama|Veri G/Ç yüzdesi|Boyut yok|
|sessions_percent|Yes|Oturum yüzdesi|Yüzde|Ortalama|Oturum yüzdesi|Boyut yok|
|sqlserver_process_core_percent|Yes|SQL Server işlem çekirdeği yüzdesi|Yüzde|Maksimum|SQL DB işleminin yüzdesi olarak CPU kullanımı. Elastik havuzlar için geçerlidir.|Boyut yok|
|sqlserver_process_memory_percent|Yes|SQL Server işlem belleği yüzdesi|Yüzde|Maksimum|SQL DB işleminin yüzdesi olarak bellek kullanımı. Elastik havuzlar için geçerlidir.|Boyut yok|
|storage_limit|Yes|Maksimum veri boyutu|Bayt|Ortalama|Maksimum veri boyutu|Boyut yok|
|storage_percent|Yes|Kullanılan veri alanı yüzdesi|Yüzde|Ortalama|Kullanılan veri alanı yüzdesi|Boyut yok|
|storage_used|Yes|Kullanılan veri alanı|Bayt|Ortalama|Kullanılan veri alanı|Boyut yok|
|tempdb_data_size|Yes|Tempdb veri dosyası boyutu kilobayt|Count|Maksimum|Tempdb veri dosyalarında kilobayt cinsinden kullanılan alan.|Boyut yok|
|tempdb_log_size|Yes|Tempdb günlük dosyası boyutu kilobayt|Count|Maksimum|Tempdb işlem günlük dosyasında kilobayt cinsinden kullanılan alan.|Boyut yok|
|tempdb_log_used_percent|Yes|Kullanılan tempdb günlüğü yüzdesi|Yüzde|Maksimum|Tempdb işlem günlük dosyasında kullanılan alan yüzdesi|Boyut yok|
|workers_percent|Yes|Çalışan yüzdesi|Yüzde|Ortalama|Çalışan yüzdesi|Boyut yok|
|xtp_storage_percent|Yes|In-Memory OLTP depolama alanı yüzdesi|Yüzde|Ortalama|In-Memory OLTP depolama alanı yüzdesi|Boyut yok|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik|Yes|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmeti için kullanılabilirlik yüzdesi veya belirtilen API işlemi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Çıkış|Yes|Çıkış|Bayt|Toplam|Çıkış verileri miktarı. Bu sayı, Azure Storage 'dan dış istemciye çıkış ve Azure 'da çıkış içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Giriş|Yes|Giriş|Bayt|Toplam|Bayt cinsinden giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|SuccessE2ELatency|Yes|Başarı E2E Gecikme Süresi|Mayacak|Ortalama|Bir depolama hizmetine veya belirtilen API işlemine yapılan başarılı isteklerin milisaniye cinsinden ortalama uçtan uca gecikme süresi. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Başarılı Sunucugecikmesi|Yes|Başarı sunucusu gecikmesi|Mayacak|Ortalama|Azure Depolama tarafından gerçekleştirilen başarılı bir isteği işlemek için kullanılan ortalama süre. Bu değer, Başarı E2E Gecikme Süresi’nde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulaması|
|İşlemler|Yes|İşlemler|Count|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı yanıt türü sayısı için ResponseType boyutunu kullanın.|Yanıt türü, coğrafi tür, ApiName, kimlik doğrulama|
|UsedCapacity|Yes|Kullanılan kapasite|Bayt|Ortalama|Depolama hesabı tarafından kullanılan depolama miktarı. Standart depolama hesapları için blob, tablo, dosya ve kuyruk tarafından kullanılan kapasitenin toplamıdır. Premium Depolama hesapları ve BLOB depolama hesapları için BlobCapacity veya FileCapacity ile aynıdır.|Boyut yok|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik|Yes|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmeti için kullanılabilirlik yüzdesi veya belirtilen API işlemi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulaması|
|BlobCapacity|No|Blob kapasitesi|Bayt|Ortalama|Depolama hesabının blob hizmeti tarafından bayt olarak kullanılan depolama miktarı.|BlobType, katman|
|BlobCount|No|BLOB sayısı|Count|Ortalama|Depolama hesabında depolanan BLOB nesnelerinin sayısı.|BlobType, katman|
|BlobProvisionedSize|No|Blob sağlanan boyut|Bayt|Ortalama|Depolama hesabının blob hizmetinde bayt olarak sağlanan depolama miktarı.|BlobType, katman|
|ContainerCount|Yes|Blob kapsayıcı sayısı|Count|Ortalama|Depolama hesabındaki kapsayıcıların sayısı.|Boyut yok|
|Çıkış|Yes|Çıkış|Bayt|Toplam|Çıkış verileri miktarı. Bu sayı, Azure Storage 'dan dış istemciye çıkış ve Azure 'da çıkış içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Dizin kapasitesi|No|Dizin kapasitesi|Bayt|Ortalama|Hiyerarşik Dizin Azure Data Lake Storage 2. tarafından kullanılan depolama miktarı.|Boyut yok|
|Giriş|Yes|Giriş|Bayt|Toplam|Bayt cinsinden giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|SuccessE2ELatency|Yes|Başarı E2E Gecikme Süresi|Mayacak|Ortalama|Bir depolama hizmetine veya belirtilen API işlemine yapılan başarılı isteklerin milisaniye cinsinden ortalama uçtan uca gecikme süresi. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Başarılı Sunucugecikmesi|Yes|Başarı sunucusu gecikmesi|Mayacak|Ortalama|Azure Depolama tarafından gerçekleştirilen başarılı bir isteği işlemek için kullanılan ortalama süre. Bu değer, Başarı E2E Gecikme Süresi’nde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulaması|
|İşlemler|Yes|İşlemler|Count|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı yanıt türü sayısı için ResponseType boyutunu kullanın.|Yanıt türü, coğrafi tür, ApiName, kimlik doğrulama|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik|Yes|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmeti için kullanılabilirlik yüzdesi veya belirtilen API işlemi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulama, FileShare|
|Çıkış|Yes|Çıkış|Bayt|Toplam|Çıkış verileri miktarı. Bu sayı, Azure Storage 'dan dış istemciye çıkış ve Azure 'da çıkış içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulama, FileShare|
|Dosya kapasitesi|No|Dosya kapasitesi|Bayt|Ortalama|Depolama hesabı tarafından kullanılan dosya depolama alanı miktarı.|Dosya Paylaşımı|
|FileCount|No|Dosya sayısı|Count|Ortalama|Depolama hesabındaki dosyaların sayısı.|Dosya Paylaşımı|
|Filesharecapacitykotası|No|Dosya paylaşma kapasitesi kotası|Bayt|Ortalama|Azure dosyaları hizmeti tarafından bayt olarak kullanılabilecek depolama miktarının üst sınırı.|Dosya Paylaşımı|
|Dosya ShareCount|No|Dosya paylaşma sayısı|Count|Ortalama|Depolama hesabındaki dosya paylaşımlarının sayısı.|Boyut yok|
|FileShareProvisionedIOPS|No|Dosya paylaşımının sağlandığı ıOPS|Bayt|Ortalama|Premium dosyalar depolama hesabındaki Premium dosya paylaşımında sağlanan ıOPS 'nin temel sayısı. Bu sayı, paylaşma kapasitesinin sağlanan boyutu (Kota) temel alınarak hesaplanır.|Dosya Paylaşımı|
|FileShareSnapshotCount|No|Dosya paylaşımının anlık görüntü sayısı|Count|Ortalama|Depolama hesabının dosyalar hizmetindeki paylaşımda bulunan anlık görüntü sayısı.|Dosya Paylaşımı|
|FileShareSnapshotSize|No|Dosya paylaşımının anlık görüntü boyutu|Bayt|Ortalama|Bayt olarak depolama hesabının dosya hizmetindeki anlık görüntüler tarafından kullanılan depolama miktarı.|Dosya Paylaşımı|
|Giriş|Yes|Giriş|Bayt|Toplam|Bayt cinsinden giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulama, FileShare|
|SuccessE2ELatency|Yes|Başarı E2E Gecikme Süresi|Mayacak|Ortalama|Bir depolama hizmetine veya belirtilen API işlemine yapılan başarılı isteklerin milisaniye cinsinden ortalama uçtan uca gecikme süresi. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulama, FileShare|
|Başarılı Sunucugecikmesi|Yes|Başarı sunucusu gecikmesi|Mayacak|Ortalama|Azure Depolama tarafından gerçekleştirilen başarılı bir isteği işlemek için kullanılan ortalama süre. Bu değer, Başarı E2E Gecikme Süresi’nde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulama, FileShare|
|İşlemler|Yes|İşlemler|Count|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı yanıt türü sayısı için ResponseType boyutunu kullanın.|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik|Yes|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmeti için kullanılabilirlik yüzdesi veya belirtilen API işlemi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Çıkış|Yes|Çıkış|Bayt|Toplam|Çıkış verileri miktarı. Bu sayı, Azure Storage 'dan dış istemciye çıkış ve Azure 'da çıkış içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Giriş|Yes|Giriş|Bayt|Toplam|Bayt cinsinden giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|QueueCapacity|Yes|Kuyruk kapasitesi|Bayt|Ortalama|Depolama hesabı tarafından kullanılan kuyruk depolama miktarı.|Boyut yok|
|QueueCount|Yes|Sıra sayısı|Count|Ortalama|Depolama hesabındaki sıraların sayısı.|Boyut yok|
|QueueMessageCount|Yes|Kuyruk Iletisi sayısı|Count|Ortalama|Depolama hesabındaki süre dolma edilmemiş sıra iletilerinin sayısı.|Boyut yok|
|SuccessE2ELatency|Yes|Başarı E2E Gecikme Süresi|Mayacak|Ortalama|Bir depolama hizmetine veya belirtilen API işlemine yapılan başarılı isteklerin milisaniye cinsinden ortalama uçtan uca gecikme süresi. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Başarılı Sunucugecikmesi|Yes|Başarı sunucusu gecikmesi|Mayacak|Ortalama|Azure Depolama tarafından gerçekleştirilen başarılı bir isteği işlemek için kullanılan ortalama süre. Bu değer, Başarı E2E Gecikme Süresi’nde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulaması|
|İşlemler|Yes|İşlemler|Count|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı yanıt türü sayısı için ResponseType boyutunu kullanın.|Yanıt türü, coğrafi tür, ApiName, kimlik doğrulama|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Kullanılabilirlik|Yes|Kullanılabilirlik|Yüzde|Ortalama|Depolama hizmeti için kullanılabilirlik yüzdesi veya belirtilen API işlemi. Kullanılabilirlik, TotalBillableRequests değerinin beklenmeyen hata üretenler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Beklenmeyen tüm hatalar, depolama hizmeti veya belirtilen API işlemi için kullanılabilirliğin azalmasıyla sonuçlanır.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Çıkış|Yes|Çıkış|Bayt|Toplam|Çıkış verileri miktarı. Bu sayı, Azure Storage 'dan dış istemciye çıkış ve Azure 'da çıkış içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Giriş|Yes|Giriş|Bayt|Toplam|Bayt cinsinden giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|SuccessE2ELatency|Yes|Başarı E2E Gecikme Süresi|Mayacak|Ortalama|Bir depolama hizmetine veya belirtilen API işlemine yapılan başarılı isteklerin milisaniye cinsinden ortalama uçtan uca gecikme süresi. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Başarılı Sunucugecikmesi|Yes|Başarı sunucusu gecikmesi|Mayacak|Ortalama|Azure Depolama tarafından gerçekleştirilen başarılı bir isteği işlemek için kullanılan ortalama süre. Bu değer, Başarı E2E Gecikme Süresi’nde belirtilen ağ gecikme süresini içermez.|Coğrafi tür, ApiName, kimlik doğrulaması|
|Tablokapasitesi|Yes|Tablo kapasitesi|Bayt|Ortalama|Depolama hesabı tarafından kullanılan tablo depolaması miktarı.|Boyut yok|
|Tablosayısı|Yes|Tablo sayısı|Count|Ortalama|Depolama hesabındaki tablo sayısı.|Boyut yok|
|TableEntityCount|Yes|Tablo varlık sayısı|Count|Ortalama|Depolama hesabındaki tablo varlıklarının sayısı.|Boyut yok|
|İşlemler|Yes|İşlemler|Count|Toplam|Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız isteklerin yanı sıra hata üreten istekleri içerir. Farklı yanıt türü sayısı için ResponseType boyutunu kullanın.|Yanıt türü, coğrafi tür, ApiName, kimlik doğrulama|


## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/önbellekler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Clienentiops|Yes|Toplam Istemci ıOPS 'si|Count|Ortalama|Önbellek tarafından işlenen istemci dosyası işlemlerinin oranı.|Boyut yok|
|ClientLatency|Yes|Ortalama Istemci gecikmesi|Mayacak|Ortalama|Önbelleğe istemci dosyası işlemlerinin ortalama gecikme süresi.|Boyut yok|
|Clientlockıops|Yes|İstemci kilidi ıOP|Sayaçpersaniye|Ortalama|Saniye başına istemci dosya kilitleme işlemleri.|Boyut yok|
|ClientMetadataReadIOPS|Yes|İstemci meta verileri okuma ıOPS 'si|Sayaçpersaniye|Ortalama|Kalıcı durumu değiştirmeyen veri okuma işlemleri hariç, önbelleğe gönderilen istemci dosyası işlemlerinin oranı.|Boyut yok|
|Clientmetadatawriteıops|Yes|İstemci meta verileri yazma ıOPS 'si|Sayaçpersaniye|Ortalama|Kalıcı durumu değiştiren veri yazmaları hariç önbelleğe gönderilen istemci dosyası işlemleri hızıdır.|Boyut yok|
|ClientReadIOPS|Yes|İstemci okuma ıOPS 'si|Sayaçpersaniye|Ortalama|Saniye başına istemci okuma işlemi.|Boyut yok|
|Clientreadüretilen Iş|Yes|Ortalama önbellek okuma performansı|BytesPerSecond|Ortalama|İstemci okuma veri aktarımı hızı.|Boyut yok|
|ClientWriteIOPS|Yes|İstemci yazma ıOPS 'si|Sayaçpersaniye|Ortalama|Saniye başına istemci yazma işlemi.|Boyut yok|
|ClientWriteThroughput|Yes|Ortalama önbellek yazma performansı|BytesPerSecond|Ortalama|İstemci yazma verisi aktarım hızı.|Boyut yok|
|Storagetargetasyncwriteüretilen Iş|Yes|StorageTarget zaman uyumsuz yazma üretilen Işi|BytesPerSecond|Ortalama|Önbelleğin verileri belirli bir StorageTarget 'e zaman uyumsuz olarak yazma oranı. Bunlar, istemcilerin engellenmesine neden olmayan fırsatçı yazmalar.|StorageTarget|
|Storagetargetfillüretilen Iş|Yes|StorageTarget dolgusu Işleme|BytesPerSecond|Ortalama|Önbelleğin, bir önbellek isabetsizliği işlemek için StorageTarget 'ten verileri okuduğu hız.|StorageTarget|
|Storagetargethegizli|Yes|Depolama hedefi durumu|Count|Ortalama|Önbellek ve depolama hedefleri arasındaki bağlantı testinin Boole sonuçları.|Boyut yok|
|Storagetargetıops|Yes|Toplam StorageTarget ıOPS|Count|Ortalama|Önbelleğin belirli bir StorageTarget 'e gönderdiği tüm dosya işlemlerinin oranı.|StorageTarget|
|StorageTargetLatency|Yes|StorageTarget gecikmesi|Mayacak|Ortalama|Önbelleğin bir ayrıştırısal StorageTarget 'e gönderdiği tüm dosya işlemlerinin ortalama gidiş dönüş gecikmesi.|StorageTarget|
|Storagetargetmetadatareadıops|Yes|StorageTarget meta verileri okuma ıOPS 'si|Sayaçpersaniye|Ortalama|Kalıcı durumu değiştirmeyen ve okuma işleminin dışında, önbelleğin belirli bir StorageTarget 'e gönderdiği dosya işlemlerinin oranı.|StorageTarget|
|Storagetargetmetadatawriteıops|Yes|StorageTarget meta verileri yazma ıOPS|Sayaçpersaniye|Ortalama|Kalıcı durumu değiştirme ve yazma işlemini hariç tutma, önbelleğin belirli bir StorageTarget 'e gönderdiği dosya işlemleri oranı.|StorageTarget|
|Storagetargetreadaheadüretilen Iş|Yes|StorageTarget Ileriye yönelik okuma performansı|BytesPerSecond|Ortalama|Opportunisticly, StorageTarget 'ten verileri okuduğu önbelleğin hızını derecelendirin.|StorageTarget|
|Storagetargetreadıops|Yes|Storagetaral okuma ıOPS|Sayaçpersaniye|Ortalama|Önbelleğin belirli bir StorageTarget 'e gönderdiği dosya okuma işlemlerinin oranı.|StorageTarget|
|Storagetargetsyncwriteüretilen Iş|Yes|StorageTarget zaman uyumlu yazma performansı|BytesPerSecond|Ortalama|Önbelleğin verileri belirli bir StorageTarget 'e zaman uyumlu olarak yazma hızı. Bunlar, istemcilerin engellenmesine neden olan yazlardır.|StorageTarget|
|Storagetargettotalreadüretilen Iş|Yes|StorageTarget toplam okuma performansı|BytesPerSecond|Ortalama|Önbelleğin belirli bir StorageTarget 'ten verileri okuduğu toplam oran.|StorageTarget|
|Storagetargettotalwriteverimini|Yes|StorageTarget Toplam yazma performansı|BytesPerSecond|Ortalama|Önbelleğin verileri belirli bir StorageTarget 'e yazdığı toplam oran.|StorageTarget|
|Storagetargetwriteıops|Yes|StorageTarget yazma ıOPS|Count|Ortalama|Önbellek belirli bir StorageTarget 'e gönderdiği dosya yazma işlemlerinin oranı.|StorageTarget|
|Çalışma süresi|Yes|Çalışma süresi|Count|Ortalama|Önbellek ve izleme sistemi arasındaki bağlantı testinin Boole sonuçları.|Boyut yok|


## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagessync/storageSyncServices

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Yes|Eşitleme oturumu sonucu|Count|Ortalama|Sunucu uç noktası, bulut uç noktası ile eşitleme oturumunu başarıyla tamamladığında 1 değerini günlüğe kaydeden ölçüm|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Yes|Eşitlenen baytlar|Bayt|Toplam|Eşitleme oturumları için aktarılan toplam dosya boyutu|SyncGroupName, ServerEndpointName, SyncDirection|
|Storagesyncrecall, Tedbaşarılı oran|Yes|Bulut katmanlama geri çekme başarı oranı|Yüzde|Ortalama|Başarılı olan tüm geri çekmeler yüzdesi|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Yes|Uygulamaya göre bulut katmanlama geri çağırma boyutu|Bayt|Toplam|Uygulama tarafından geri çekilen verilerin boyutu|SyncGroupName, ServerName, ApplicationName|
|Storagesyncyeniden hesaplama Ledtotalnetworkbytes|Yes|Bulut katmanlama geri çağırma boyutu|Bayt|Toplam|Geri çekilen verilerin boyutu|SyncGroupName, ServerName|
|Storagesyncyeniden hesaplama Liototalsizebytes|Yes|Bulut katmanlama geri çağırma|Bayt|Toplam|Sunucu tarafından geri çekilen toplam veri boyutu|aboneliğinde ve|
|StorageSyncRecallThroughputBytesPerSecond|Yes|Bulut katmanlama geri çekme performansı|BytesPerSecond|Ortalama|Veri geri çağırma işleme boyutu|SyncGroupName, ServerName|
|Storagesyncserversinyal|Yes|Sunucu çevrimiçi durumu|Count|Maksimum|Yeniden sigvbulunan sunucu, bulut uç noktasıyla bir sinyal başarıyla kaydetilişinde 1 değerini günlüğe kaydeden ölçüm|aboneliğinde ve|
|StorageSyncSyncSessionAppliedFilesCount|Yes|Eşitlenen dosyalar|Count|Toplam|Eşitlenen dosya sayısı|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Yes|Dosyalar eşitlenmiyor|Count|Toplam|Eşitleme başarısız olan dosya sayısı|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagessync/storageSyncServices/registeredServers

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Sunucusinyal|Yes|Sunucu çevrimiçi durumu|Count|Maksimum|Yeniden sigvbulunan sunucu, bulut uç noktasıyla bir sinyal başarıyla kaydetilişinde 1 değerini günlüğe kaydeden ölçüm|Sunucuresourceıd, ServerName|
|Sunucu yeniden hesaplama Liototalsizebytes|Yes|Bulut katmanlama geri çağırma|Bayt|Toplam|Sunucu tarafından geri çekilen toplam veri boyutu|Sunucuresourceıd, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagessync/storageSyncServices/syncGroups

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Yes|Eşitlenen baytlar|Bayt|Toplam|Eşitleme oturumları için aktarılan toplam dosya boyutu|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Yes|Eşitlenen dosyalar|Count|Toplam|Eşitlenen dosya sayısı|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Yes|Dosyalar eşitlenmiyor|Count|Toplam|Eşitleme başarısız olan dosya sayısı|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagessync/storageSyncServices/syncGroups/serverEndpoints

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Yes|Eşitlenen baytlar|Bayt|Toplam|Eşitleme oturumları için aktarılan toplam dosya boyutu|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Yes|Eşitlenen dosyalar|Count|Toplam|Eşitlenen dosya sayısı|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Yes|Dosyalar eşitlenmiyor|Count|Toplam|Eşitleme başarısız olan dosya sayısı|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Yes|Başarısız Işlev Istekleri|Count|Toplam|Başarısız Işlev Istekleri|LogicalName, PartitionID|
|AMLCalloutInputEvents|Yes|İşlev olayları|Count|Toplam|İşlev olayları|LogicalName, PartitionID|
|AMLCalloutRequests|Yes|İşlev Istekleri|Count|Toplam|İşlev Istekleri|LogicalName, PartitionID|
|Dönüştürme hataları|Yes|Veri dönüştürme hataları|Count|Toplam|Veri dönüştürme hataları|LogicalName, PartitionID|
|DeserializationError|Yes|Giriş seri kaldırma hataları|Count|Toplam|Giriş seri kaldırma hataları|LogicalName, PartitionID|
|Droppedorayarlantedevents|Yes|Sıra dışı olaylar|Count|Toplam|Sıra dışı olaylar|LogicalName, PartitionID|
|EarlyInputEvents|Yes|Erken giriş olayları|Count|Toplam|Erken giriş olayları|LogicalName, PartitionID|
|Hatalar|Yes|Çalışma zamanı hataları|Count|Toplam|Çalışma zamanı hataları|LogicalName, PartitionID|
|Inputeventbytes|Yes|Olay baytlarını gir|Bayt|Toplam|Olay baytlarını gir|LogicalName, PartitionID|
|Inputevents|Yes|Giriş olayları|Count|Toplam|Giriş olayları|LogicalName, PartitionID|
|Inputeventssourcesbackgünlüğe kaydediliyor|Yes|Biriktirme listesindeki giriş olayları|Count|Maksimum|Biriktirme listesindeki giriş olayları|LogicalName, PartitionID|
|Inputeventssourcespersecond|Yes|Alınan giriş kaynakları|Count|Toplam|Alınan giriş kaynakları|LogicalName, PartitionID|
|LateInputEvents|Yes|Geç giriş olayları|Count|Toplam|Geç giriş olayları|LogicalName, PartitionID|
|OutputEvents|Yes|Çıkış olayları|Count|Toplam|Çıkış olayları|LogicalName, PartitionID|
|Outputsulu Markdelayseconds|Yes|Filigran gecikmesi|Saniye|Maksimum|Filigran gecikmesi|LogicalName, PartitionID|
|ProcessCPUUsagePercentage|Yes|CPU% kullanımı (Önizleme)|Yüzde|Maksimum|CPU% kullanımı (Önizleme)|LogicalName, PartitionID|
|Resourcekullanım|Yes|SU kullanımı yüzdesi|Yüzde|Maksimum|SU kullanımı yüzdesi|LogicalName, PartitionID|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. SYNAPSE/çalışma alanları
|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Builsqlpooldataprocessedbytes|No|İşlenen veri (bayt)|Bayt|Toplam|Sorgular tarafından işlenen veri miktarı|Boyut yok|
|Builsqlpoollogindenemeler|No|Oturum açma girişimleri|Count|Toplam|Başarılı veya başarısız olan oturum açma denemesi sayısı|Sonuç|
|BuiltinSqlPoolRequestsEnded|No|Sonlandırılan istekler|Count|Toplam|Başarılı, başarısız veya iptal edilen Isteklerin sayısı|Sonuç|
|Integrationactivityrunsended|No|Etkinlik çalıştırmaları sona erdi|Count|Toplam|Başarılı, başarısız veya iptal edilen tümleştirme etkinliklerinin sayısı|Sonuç, Failuyeniden yazma, etkinlik, ActivityType, işlem hattı|
|Integrationbir ardışık düzen eylemsizlik|No|İşlem hattı çalıştırmaları sona erdi|Count|Toplam|Başarılı, başarısız veya iptal edilen tümleştirme işlem hattı çalıştırma sayısı|Sonuç, Failuyeniden yazma, işlem hattı|
|Integrationtriggerrunsended|No|Tetikleyici çalıştırmaları sona erdi|Count|Toplam|Başarılı, başarısız veya iptal edilen tümleştirme Tetikleyicileri sayısı|Sonuç, Failuyeniden yazma, tetikleyici|
|Sqlstreamingbackloggedınputeventsources|No|Biriktirme listesindeki giriş olayları (Önizleme)|Count|Toplam|Bu, Doğu ABD Batı Avrupa sunulan bir önizleme ölçümdür. Biriktirme listesindeki giriş olayı kaynaklarının sayısı.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionID, Processorınstance|
|SQLStreamingConversionErrors|No|Veri dönüştürme hataları (Önizleme)|Count|Toplam|Bu, Doğu ABD Batı Avrupa sunulan bir önizleme ölçümdür. Beklenen çıkış şemasına dönüştürülemeyen çıkış olaylarının sayısı. Hata ilkesi, bu senaryoya ilişkin olayları bırakmak için ' Drop ' olarak değiştirilebilir.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionID, Processorınstance|
|SQLStreamingDeserializationError|No|Giriş seri kaldırma hataları (Önizleme)|Count|Toplam|Bu, Doğu ABD Batı Avrupa sunulan bir önizleme ölçümdür. Seri durumdan çıkarılabilen giriş olaylarının sayısı.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionID, Processorınstance|
|SQLStreamingEarlyInputEvents|No|Erken giriş olayları (Önizleme)|Count|Toplam|Bu, Doğu ABD Batı Avrupa sunulan bir önizleme ölçümdür. İlk alma ilkesine göre, uygulama zamanının başlangıç süresine kıyasla erken kabul edildiği giriş olaylarının sayısı.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionID, Processorınstance|
|SQLStreamingInputEventBytes|No|Giriş olayı baytları (Önizleme)|Count|Toplam|Bu, Doğu ABD Batı Avrupa sunulan bir önizleme ölçümdür. Akış işi tarafından bayt cinsinden alınan veri miktarı. Bu, olayların giriş kaynağına gönderildiğini doğrulamak için kullanılabilir.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionID, Processorınstance|
|SQLStreamingInputEvents|No|Giriş olayları (Önizleme)|Count|Toplam|Bu, Doğu ABD Batı Avrupa sunulan bir önizleme ölçümdür. Giriş olaylarının sayısı.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionID, Processorınstance|
|SQLStreamingInputEventsSourcesPerSecond|No|Alınan giriş kaynakları (Önizleme)|Count|Toplam|Bu, Doğu ABD Batı Avrupa sunulan bir önizleme ölçümdür. Saniye başına giriş olayı kaynak sayısı.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionID, Processorınstance|
|SQLStreamingLateInputEvents|No|Geç giriş olayları (Önizleme)|Count|Toplam|Bu, Doğu ABD Batı Avrupa sunulan bir önizleme ölçümdür. Geç varış ilkesine göre, uygulama zamanının geç olarak kabul edildiği giriş olayları sayısı.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionID, Processorınstance|
|SQLStreamingOutOfOrderEvents|No|Sıra dışı olaylar (Önizleme)|Count|Toplam|Bu, Doğu ABD Batı Avrupa sunulan bir önizleme ölçümdür. Olay Hub 'ı giriş bağdaştırıcısı tarafından alınan olay hub 'ı olaylarının (seri hale getirilmiş iletiler) sayısı, olay sıralaması Ilkesine bağlı olarak, ayarlanmış bir zaman damgasına alınmış veya verilen zaman damgası.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionID, Processorınstance|
|SQLStreamingOutputEvents|No|Çıkış olayları (Önizleme)|Count|Toplam|Bu, Doğu ABD Batı Avrupa sunulan bir önizleme ölçümdür. Çıkış olaylarının sayısı.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionID, Processorınstance|
|Sqlstreamingoutputsulu Markdelayseconds|No|Filigran gecikmesi (Önizleme)|Count|Maksimum|Bu, Doğu ABD Batı Avrupa sunulan bir önizleme ölçümdür. Saniye cinsinden çıkış filigranı gecikmesi.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionID, Processorınstance|
|Sqlstreamingresourcekullanım|No|Kaynak% kullanımı (Önizleme)|Yüzde|Maksimum|Bu, Doğu ABD Batı Avrupa sunulan bir önizleme ölçümdür.
 Kaynak kullanımı yüzde olarak ifade edilir. Yüksek kullanım, işin en fazla ayrılmış kaynağa yakın kullandığını gösterir.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionID, Processorınstance|
|SQLStreamingRuntimeErrors|No|Çalışma zamanı hataları (Önizleme)|Count|Toplam|Bu, Doğu ABD Batı Avrupa sunulan bir önizleme ölçümdür. Sorgu işlemeyle ilgili toplam hata sayısı (olayları alırken veya sonuçları yazarken bulunan hatalar hariç).|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionID, Processorınstance|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. SYNAPSE/Workspaces/bigDataPools

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|No|ayrılan sanal çekirdekler|Count|Maksimum|Apache Spark havuzu için ayrılmış sanal çekirdekler|Submitterıd|
|BigDataPoolAllocatedMemory|No|Ayrılan bellek (GB)|Count|Maksimum|Apach Spark Havuzu (GB) için ayrılan bellek|Submitterıd|
|BigDataPoolApplicationsActive|No|Etkin Apache Spark uygulamalar|Count|Maksimum|Toplam etkin Apache Spark havuzu uygulamaları|JobState|
|BigDataPoolApplicationsEnded|No|Apache Spark uygulamalar sonlandırıldı|Count|Toplam|Apache Spark havuzu uygulamalarının sayısı sona erdi|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. SYNAPSE/Workspaces/Sqlhavuzlar

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ActiveQueries|No|Etkin sorgular|Count|Toplam|Etkin sorgular. Bu ölçüyü filtrelenmemiş ve Böl, sistemde çalışan tüm etkin sorguları görüntüler|IsUserDefined|
|Uyartivecachehitpercent|No|Uyarlamalı önbellek isabet yüzdesi|Yüzde|Maksimum|İş yüklerinin Uyarlamalı önbelleğin kullanılmasıyla ne kadar iyi olduğunu ölçer. Ek kapasite için ölçekleneceğini veya önbelleği doldurma için iş yüklerini yeniden çalıştırmayı öğrenmek üzere bu ölçümü önbellek isabet yüzdesi ölçümüyle birlikte kullanın|Boyut yok|
|Uyartivecacheusedpercent|No|Uyarlamalı önbellek kullanılan yüzde|Yüzde|Maksimum|İş yüklerinin Uyarlamalı önbelleğin kullanılmasıyla ne kadar iyi olduğunu ölçer. Bu ölçümü, ek kapasite için ölçekleneceğini veya önbelleğe almak üzere iş yüklerini yeniden çalıştırmayı belirtmek için kullanılan önbellek yüzdesi ölçümüyle kullanın|Boyut yok|
|Bağlantılar|Yes|Bağlantılar|Count|Toplam|SQL havuzunda Toplam oturum açma sayısı|Sonuç|
|ConnectionsBlockedByFirewall|No|Güvenlik duvarı tarafından engellenen bağlantılar|Count|Toplam|Güvenlik duvarı kuralları tarafından engellenen bağlantı sayısı. SQL havuzunuz için erişim denetim ilkelerini yeniden ziyaret edin ve sayı yüksekse bu bağlantıları izleyin|Boyut yok|
|Cpuyüzdesi|No|Kullanılan CPU yüzdesi|Yüzde|Maksimum|SQL havuzundaki tüm düğümlerde CPU kullanımı|Boyut yok|
|DWULimit|No|DWU sınırı|Count|Maksimum|SQL havuzunun hizmet düzeyi hedefi|Boyut yok|
|Kullanılan DWU|No|Kullanılan DWU|Count|Maksimum|SQL havuzu genelinde kullanım üst düzey gösterimini temsil eder. DWU sınırına göre ölçülür * DWU yüzdesi|Boyut yok|
|DWUUsedPercent|No|Kullanılan DWU yüzdesi|Yüzde|Maksimum|SQL havuzu genelinde kullanım üst düzey gösterimini temsil eder. CPU yüzdesi ile veri GÇ yüzdesi arasındaki en yüksek değer alınarak ölçülür|Boyut yok|
|LocalTempDBUsedPercent|No|Yerel tempdb kullanılan yüzde|Yüzde|Maksimum|Tüm işlem düğümlerinde yerel tempdb kullanımı-değerler her beş dakikada bir dağıtılır|Boyut yok|
|MemoryUsedPercent|No|Kullanılan bellek yüzdesi|Yüzde|Maksimum|SQL havuzundaki tüm düğümlerde bellek kullanımı|Boyut yok|
|QueuedQueries|No|Kuyruğa alınmış sorgular|Count|Toplam|En fazla eşzamanlılık sınırına ulaşıldıktan sonra sıraya alınan toplam istek sayısı|IsUserDefined|
|WLGActiveQueries|No|İş yükü grubu etkin sorguları|Count|Toplam|İş yükü grubu içindeki etkin sorgular. Bu ölçüyü filtrelenmemiş ve Böl, sistemde çalışan tüm etkin sorguları görüntüler|IsUserDefined, WorkloadGroup|
|Wlgactivequerieszamanaşımları|No|İş yükü grubu sorgu zaman aşımları|Count|Toplam|Zaman aşımına uğramış iş yükü grubu için sorgular. Bu ölçüm tarafından bildirilen sorgu zaman aşımları yalnızca sorgu yürütülmeye başladıktan sonra (kilitleme veya kaynak bekleme nedeniyle bekleme zamanı içermez)|IsUserDefined, WorkloadGroup|
|WLGAllocationByEffectiveCapResourcePercent|No|Maksimum kaynak yüzdesi bazında iş yükü grubu ayırması|Yüzde|Maksimum|Toplam iş yükü grubu başına düşen etkin sınır kaynağına göre kaynakların yüzde ayırmasını görüntüler. Bu ölçüm, iş yükü grubunun etkin kullanımını sağlar|IsUserDefined, WorkloadGroup|
|Wlgallocationbysystempersent|No|Sistem iş yükü grubuna göre ayırma yüzdesi|Yüzde|Maksimum|Tüm sisteme göre kaynakların yüzde tahsisi|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|No|Etkin uç kaynak yüzdesi|Yüzde|Maksimum|İş yükü grubu için geçerli uç kaynak yüzdesi. Min_percentage_resource > 0 olan başka iş yükü grupları varsa effective_cap_percentage_resource orantılı bir şekilde düşürülemez|IsUserDefined, WorkloadGroup|
|WLGEffectiveMinResourcePercent|No|Geçerli Min kaynak yüzdesi|Yüzde|Maksimum|Etkin Min kaynak yüzdesi ayarı, hizmet düzeyini ve iş yükü grubu ayarlarını göz önünde bulundur, izin verildi. Etkin min_percentage_resource daha düşük hizmet düzeylerinde ayarlanabilir|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|No|İş yükü grubu sıraya alınmış sorgular|Count|Toplam|En fazla eşzamanlılık sınırına ulaşıldıktan sonra sıraya alınan toplam istek sayısı|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. Timeseriesınsights/ortamlar

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Alınan baytlar|Yes|Alınan bayt sayısı|Bayt|Toplam|Tüm olay kaynaklarından okunan bayt sayısı|Boyut yok|
|Inressreceiveınvalidmessages|Yes|Giriş geçersiz Iletiler aldı|Count|Toplam|Tüm Olay Hub 'ından veya IoT Hub olay kaynaklarından okunan geçersiz ileti sayısı|Boyut yok|
|Inressreceived Iletileri|Yes|Giriş alınan Iletiler|Count|Toplam|Tüm Olay Hub 'ından veya IoT Hub olay kaynaklarından okunan ileti sayısı|Boyut yok|
|Inressreceived Iletisayaçgecikmesi|Yes|Alınan Ileti sayısı gecikmesi|Count|Ortalama|Olay kaynak bölümünde en son sıraya alınmış iletinin sıra numarası ve giriş bölümünde işlenmekte olan iletilerin sıra numarası arasındaki fark|Boyut yok|
|Inressreceived Iletizamangecikmesi|Yes|Alınan Ileti zaman gecikmesi|Saniye|Maksimum|İleti olay kaynağında sıraya alındığı zaman ve giriş sırasında işlendiği zaman arasındaki fark|Boyut yok|
|IngressStoredBytes|Yes|Giriş depolanan baytlar|Bayt|Toplam|Başarılı bir şekilde işlenen ve sorgu için kullanılabilir olayların toplam boyutu|Boyut yok|
|Inressstoo Vents|Yes|Giriş saklı olayları|Count|Toplam|Sorgu için başarıyla işlenen ve kullanılabilir düzleştirilmiş olay sayısı|Boyut yok|
|WarmStorageMaxProperties|Yes|Isınma depolama en yüksek özellikleri|Count|Maksimum|S1/S2 SKU 'SU için ortam tarafından izin verilen en fazla özellik sayısı ve PAYG SKU 'SU için ısınma tarafından izin verilen en fazla özellik sayısı|Boyut yok|
|WarmStorageUsedProperties|Yes|Sıcak depolama kullanılan özellikler |Count|Maksimum|S1/S2 SKU 'SU için ortam tarafından kullanılan özellik sayısı ve PAYG SKU 'SU için sıcak mağaza tarafından kullanılan özellik sayısı|Boyut yok|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. Timeseriesınsights/ortamlar/EventSources

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Alınan baytlar|Yes|Alınan bayt sayısı|Bayt|Toplam|Olay kaynağından okunan bayt sayısı|Boyut yok|
|Inressreceiveınvalidmessages|Yes|Giriş geçersiz Iletiler aldı|Count|Toplam|Olay kaynağından okunan geçersiz ileti sayısı|Boyut yok|
|Inressreceived Iletileri|Yes|Giriş alınan Iletiler|Count|Toplam|Olay kaynağından okunan ileti sayısı|Boyut yok|
|Inressreceived Iletisayaçgecikmesi|Yes|Alınan Ileti sayısı gecikmesi|Count|Ortalama|Olay kaynak bölümünde en son sıraya alınmış iletinin sıra numarası ve giriş bölümünde işlenmekte olan iletilerin sıra numarası arasındaki fark|Boyut yok|
|Inressreceived Iletizamangecikmesi|Yes|Alınan Ileti zaman gecikmesi|Saniye|Maksimum|İleti olay kaynağında sıraya alındığı zaman ve giriş sırasında işlendiği zaman arasındaki fark|Boyut yok|
|IngressStoredBytes|Yes|Giriş depolanan baytlar|Bayt|Toplam|Başarılı bir şekilde işlenen ve sorgu için kullanılabilir olayların toplam boyutu|Boyut yok|
|Inressstoo Vents|Yes|Giriş saklı olayları|Count|Toplam|Sorgu için başarıyla işlenen ve kullanılabilir düzleştirilmiş olay sayısı|Boyut yok|
|WarmStorageMaxProperties|Yes|Isınma depolama en yüksek özellikleri|Count|Maksimum|S1/S2 SKU 'SU için ortam tarafından izin verilen en fazla özellik sayısı ve PAYG SKU 'SU için ısınma tarafından izin verilen en fazla özellik sayısı|Boyut yok|
|WarmStorageUsedProperties|Yes|Sıcak depolama kullanılan özellikler |Count|Maksimum|S1/S2 SKU 'SU için ortam tarafından kullanılan özellik sayısı ve PAYG SKU 'SU için sıcak mağaza tarafından kullanılan özellik sayısı|Boyut yok|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. Vmwarechoparlör basit/virtualMachines

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Disk okuma bayt sayısı|Yes|Disk okuma bayt sayısı|Bayt|Toplam|Örnek süre boyunca okuma işlemleri nedeniyle toplam disk aktarım hızı.|Boyut yok|
|Disk okuma Işlemi/sn|Yes|Disk okuma Işlemi/sn|Sayaçpersaniye|Ortalama|Önceki örnek dönemdeki ortalama GÇ okuma işlemi sayısı. Bu işlemlerin değişken boyutlandırılabilir olabileceğini unutmayın.|Boyut yok|
|Disk yazma baytları|Yes|Disk yazma baytları|Bayt|Toplam|Örnek süre içinde yazma işlemleri nedeniyle toplam disk aktarım hızı.|Boyut yok|
|Disk yazma Işlemi/sn|Yes|Disk yazma Işlemi/sn|Sayaçpersaniye|Ortalama|Önceki örnek dönemdeki ortalama GÇ yazma işlemi sayısı. Bu işlemlerin değişken boyutlandırılabilir olabileceğini unutmayın.|Boyut yok|
|DiskReadBytesPerSecond|Yes|Disk okuma bayt/sn|BytesPerSecond|Ortalama|Örnek süre içinde okuma işlemleri nedeniyle ortalama disk aktarım hızı.|Boyut yok|
|DiskReadLatency|Yes|Disk okuma gecikmesi|Mayacak|Ortalama|Toplam okuma gecikmesi. Cihaz ve çekirdek okuma gecikme sürelerinin toplamı.|Boyut yok|
|DiskReadOperations|Yes|Disk okuma Işlemleri|Count|Toplam|Önceki örnek dönemdeki GÇ okuma işlemlerinin sayısı. Bu işlemlerin değişken boyutlandırılabilir olabileceğini unutmayın.|Boyut yok|
|DiskWriteBytesPerSecond|Yes|Disk yazma bayt/sn|BytesPerSecond|Ortalama|Örnek süre içinde yazma işlemleri nedeniyle ortalama disk aktarım hızı.|Boyut yok|
|DiskWriteLatency|Yes|Disk yazma gecikmesi|Mayacak|Ortalama|Toplam yazma gecikmesi. Cihaz ve çekirdek yazma gecikme sürelerinin toplamı.|Boyut yok|
|DiskWriteOperations|Yes|Disk yazma Işlemleri|Count|Toplam|Önceki örnek dönemdeki GÇ Yazma işlemlerinin sayısı. Bu işlemlerin değişken boyutlandırılabilir olabileceğini unutmayın.|Boyut yok|
|MemoryActive|Yes|Bellek etkin|Bayt|Ortalama|Son küçük zaman penceresinde VM tarafından kullanılan bellek miktarı. Bu, VM 'nin şu anda ihtiyacı olan bellek miktarını "doğru" sayısıdır. Ek olarak kullanılmayan bellek, konuğun performansına hiçbir etkilenmeden dışarı veya ballooned olabilir.|Boyut yok|
|Memoryverildi|Yes|Verilen bellek|Bayt|Ortalama|VM 'ye konak tarafından verilen bellek miktarı. Bir kez dokunulmadan ve VMkernel belleğe ihtiyaç duyuncaya kadar bellek verilebilene kadar bu konağa bellek verilmez.|Boyut yok|
|MemoryUsed|Yes|Kullanılan bellek|Bayt|Ortalama|VM tarafından kullanılan makine belleği miktarı.|Boyut yok|
|Ağ Girişi|Yes|Ağ Girişi|Bayt|Toplam|Alınan trafik için toplam ağ aktarım hızı.|Boyut yok|
|Ağ Çıkışı|Yes|Ağ Çıkışı|Bayt|Toplam|Aktarılan trafik için toplam ağ aktarım hızı.|Boyut yok|
|Networkınbytespersecond|Yes|Bayt/sn cinsinden ağ|BytesPerSecond|Ortalama|Alınan trafik için Ortalama ağ performansı.|Boyut yok|
|NetworkOutBytesPerSecond|Yes|Ağ çıkış bayt/sn|BytesPerSecond|Ortalama|Aktarılan trafik için Ortalama ağ aktarım hızı.|Boyut yok|
|CPU yüzdesi|Yes|CPU yüzdesi|Yüzde|Ortalama|CPU kullanımı. Bu değer, sistemdeki tüm işlemci çekirdeğini temsil eden %100 ile raporlanır. Örnek olarak, dört çekirdekli bir sistemin %50 ' ünü kullanan 2 yönlü bir VM tamamen iki çekirdek kullanmaktır.|Boyut yok|
|Yüztagecpuready|Yes|CPU için hazırlanma yüzdesi|Mayacak|Toplam|Hazır süre, CPU (ler) in geçmiş güncelleştirme aralığında kullanılabilir olmasını bekleyen zamanın harcadığı süredir.|Boyut yok|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|ActiveRequests|Yes|Etkin Istekler (kullanım dışı)|Count|Toplam|Etkin Istekler|Örnek|
|AverageResponseTime|Yes|Ortalama yanıt süresi (kullanım dışı)|Saniye|Ortalama|Ön uç için istekleri saniye cinsinden hizmeti için geçen ortalama süre.|Örnek|
|BytesReceived|Yes|Içindeki veriler|Bayt|Toplam|Tüm ön uçlar genelinde kullanılan, MIB içindeki ortalama gelen bant genişliği.|Örnek|
|BytesSent|Yes|Giden veriler|Bayt|Toplam|Tüm ön uçta kullanılan, MIB 'deki ortalama gelen bant genişliği.|Örnek|
|Cpuyüzdesi|Yes|CPU Yüzdesi|Yüzde|Ortalama|Tüm ön uç örnekleri genelinde kullanılan ortalama CPU.|Örnek|
|DiskQueueLength|Yes|Disk kuyruğu uzunluğu|Count|Ortalama|Depolama üzerinde sıraya alınan okuma ve yazma isteklerinin ortalama sayısı. Yüksek disk kuyruğu uzunluğu, aşırı disk g/ç nedeniyle yavaşlabilen bir uygulamanın göstergesidir.|Örnek|
|Http101|Yes|Http 101|Count|Toplam|HTTP durum kodu 101 ile sonuçlanan istek sayısı.|Örnek|
|Http2xx|Yes|Http 2xx|Count|Toplam|HTTP durum koduna neden olan istek sayısı = 200, ancak 300 <.|Örnek|
|Http3xx|Yes|Http 3xx|Count|Toplam|HTTP durum koduna neden olan istek sayısı = 300, ancak 400 <.|Örnek|
|Http401|Yes|Http 401|Count|Toplam|HTTP 401 durum koduna neden olan istek sayısı.|Örnek|
|Http403|Yes|Http 403|Count|Toplam|HTTP 403 durum koduna neden olan istek sayısı.|Örnek|
|Http404|Yes|HTTP 404|Count|Toplam|HTTP 404 durum koduna neden olan istek sayısı.|Örnek|
|Http406|Yes|Http 406|Count|Toplam|HTTP 406 durum koduna neden olan istek sayısı.|Örnek|
|Http4xx|Yes|Http 4xx|Count|Toplam|HTTP durum koduna neden olan istek sayısı = 400, ancak 500 <.|Örnek|
|Http5xx|Yes|Http sunucu hataları|Count|Toplam|HTTP durum koduna neden olan istek sayısı = 500, ancak 600 <.|Örnek|
|HttpQueueLength|Yes|Http kuyruğu uzunluğu|Count|Ortalama|Yerine getirilmeden önce kuyruğa oturmak zorunda olan HTTP isteklerinin ortalama sayısı. Yüksek veya artan bir HTTP kuyruğu uzunluğu, ağır yük altında bir planın belirtisidir.|Örnek|
|HttpResponseTime|Yes|Yanıt süresi|Saniye|Ortalama|Ön uç için istekleri saniye cinsinden hizmeti için geçen süre.|Örnek|
|Largeappserviceplanınstances|Yes|Büyük App Service çalışanları planlıyor|Count|Ortalama|Büyük App Service çalışanları planlıyor|Boyut yok|
|Mediumappserviceplanınstances|Yes|Orta App Service çalışanları planlıyor|Count|Ortalama|Orta App Service çalışanları planlıyor|Boyut yok|
|MemoryPercentage|Yes|Bellek yüzdesi|Yüzde|Ortalama|Tüm ön uç örnekleri genelinde kullanılan ortalama bellek.|Örnek|
|İstekler|Yes|İstekler|Count|Toplam|Elde edilen HTTP durum kodundan bağımsız olarak isteklerin toplam sayısı.|Örnek|
|Smallappserviceplanınstances|Yes|Küçük App Service çalışanları planlıyor|Count|Ortalama|Küçük App Service çalışanları planlıyor|Boyut yok|
|Toplam ön uçlar|Yes|Toplam ön uçlar|Count|Ortalama|Toplam ön uçlar|Boyut yok|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|Cpuyüzdesi|Yes|CPU Yüzdesi|Yüzde|Ortalama|Çalışan havuzunun tüm örnekleri genelinde kullanılan ortalama CPU.|Örnek|
|MemoryPercentage|Yes|Bellek yüzdesi|Yüzde|Ortalama|Çalışan havuzunun tüm örnekleri genelinde kullanılan ortalama bellek.|Örnek|
|WorkersAvailable|Yes|Kullanılabilir çalışanlar|Count|Ortalama|Kullanılabilir çalışanlar|Boyut yok|
|WorkersTotal|Yes|Toplam çalışan sayısı|Count|Ortalama|Toplam çalışan sayısı|Boyut yok|
|WorkersUsed|Yes|Kullanılan çalışanlar|Count|Ortalama|Kullanılan çalışanlar|Boyut yok|


## <a name="microsoftwebserverfarms"></a>Microsoft. Web/sunucugrupları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|BytesReceived|Yes|Içindeki veriler|Bayt|Toplam|Planın tüm örnekleri genelinde kullanılan ortalama gelen bant genişliği.|Örnek|
|BytesSent|Yes|Giden veriler|Bayt|Toplam|Planın tüm örneklerinde kullanılan ortalama giden bant genişliği.|Örnek|
|Cpuyüzdesi|Yes|CPU Yüzdesi|Yüzde|Ortalama|Planın tüm örnekleri genelinde kullanılan ortalama CPU.|Örnek|
|DiskQueueLength|Yes|Disk kuyruğu uzunluğu|Count|Ortalama|Depolama üzerinde sıraya alınan okuma ve yazma isteklerinin ortalama sayısı. Yüksek disk kuyruğu uzunluğu, aşırı disk g/ç nedeniyle yavaşlabilen bir uygulamanın göstergesidir.|Örnek|
|HttpQueueLength|Yes|Http kuyruğu uzunluğu|Count|Ortalama|Yerine getirilmeden önce kuyruğa oturmak zorunda olan HTTP isteklerinin ortalama sayısı. Yüksek veya artan bir HTTP kuyruğu uzunluğu, ağır yük altında bir planın belirtisidir.|Örnek|
|MemoryPercentage|Yes|Bellek yüzdesi|Yüzde|Ortalama|Planın tüm örnekleri genelinde kullanılan ortalama bellek.|Örnek|
|Socketınboundall|Yes|Socketınboundall|Count|Ortalama|Socketınboundall|Örnek|
|SocketLoopback|Yes|SocketLoopback|Count|Ortalama|SocketLoopback|Örnek|
|SocketOutboundAll|Yes|SocketOutboundAll|Count|Ortalama|SocketOutboundAll|Örnek|
|Socketoutsıçramaed|Yes|Socketoutsıçramaed|Count|Ortalama|Socketoutsıçramaed|Örnek|
|SocketOutboundTimeWait|Yes|SocketOutboundTimeWait|Count|Ortalama|SocketOutboundTimeWait|Örnek|
|TcpCloseWait|Yes|TCP kapatma bekleme|Count|Ortalama|TCP kapatma bekleme|Örnek|
|TcpClosing|Yes|TCP kapatma|Count|Ortalama|TCP kapatma|Örnek|
|Tcpkurdu|Yes|TCP oluşturuldu|Count|Ortalama|TCP oluşturuldu|Örnek|
|TcpFinWait1|Yes|TCP Fin bekleme 1|Count|Ortalama|TCP Fin bekleme 1|Örnek|
|TcpFinWait2|Yes|TCP Fin bekleme 2|Count|Ortalama|TCP Fin bekleme 2|Örnek|
|TcpLastAck|Yes|TCP son ACK|Count|Ortalama|TCP son ACK|Örnek|
|Tcpsynalındı|Yes|TCP SYN alındı|Count|Ortalama|TCP SYN alındı|Örnek|
|TcpSynSent|Yes|TCP SYN gönderilen|Count|Ortalama|TCP SYN gönderilen|Örnek|
|TcpTimeWait|Yes|TCP zaman bekleme|Count|Ortalama|TCP zaman bekleme|Örnek|


## <a name="microsoftwebsites"></a>Microsoft. Web/siteler

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Bağlantılar|Count|Ortalama|Korumalı alan içindeki ilişkili yuva sayısı (w3wp.exe ve alt işlemi). Bağlama yuvası, bind ()/Connect () API 'Leri çağırarak ve bir yuva CloseHandle ()/Closesocket () ile kapatılana kadar kalır.|Örnek|
|AverageMemoryWorkingSet|Yes|Ortalama bellek çalışma kümesi|Bayt|Ortalama|Uygulama tarafından, megabayt (MIB) cinsinden kullanılan ortalama bellek miktarı.|Örnek|
|AverageResponseTime|Yes|Ortalama yanıt süresi (kullanım dışı)|Saniye|Ortalama|Uygulama için geçen ortalama süre (saniye cinsinden).|Örnek|
|BytesReceived|Yes|Içindeki veriler|Bayt|Toplam|MIB içinde uygulama tarafından tüketilen gelen bant genişliği miktarı.|Örnek|
|BytesSent|Yes|Giden veriler|Bayt|Toplam|MIB içinde uygulama tarafından tüketilen giden bant genişliği miktarı.|Örnek|
|CpuTime|Yes|CPU süresi|Saniye|Toplam|Uygulama tarafından saniye cinsinden tüketilen CPU miktarı. Bu ölçüm hakkında daha fazla bilgi için. Azure Işlevleri için geçerli değildir. Lütfen bkz https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage . (CPU süresi vs CPU yüzdesi).|Örnek|
|CurrentAssemblies|Yes|Geçerli derlemeler|Count|Ortalama|Bu uygulamadaki tüm AppDomain 'ler genelinde yüklenen derlemelerin geçerli sayısı.|Örnek|
|Filesystemage|Yes|Dosya sistemi kullanımı|Bayt|Ortalama|Uygulama tarafından tüketilen dosya sistemi kotasının yüzdesi.|Boyut yok|
|Işlev yürütme sayısı|Yes|İşlev yürütme sayısı|Count|Toplam|İşlev yürütme sayısı. Yalnızca Azure Işlevleri için mevcut.|Örnek|
|FunctionExecutionUnits|Yes|İşlev yürütme birimleri|Count|Toplam|İşlev yürütme birimleri. Yalnızca Azure Işlevleri için mevcut.|Örnek|
|Gen0Collections|Yes|Gen 0 çöp koleksiyonları|Count|Toplam|Uygulama işleminin başlangıcından bu yana nesil 0 nesnelerinin atık olarak toplandığı sayı. Daha yüksek nesil GCs, tüm düşük nesil GCs 'leri içerir.|Örnek|
|Gen1Collections|Yes|Gen 1 çöp koleksiyonları|Count|Toplam|Uygulama işleminin başlangıcından bu yana 1. nesil nesnelerin atık olarak toplandığı sayı. Daha yüksek nesil GCs, tüm düşük nesil GCs 'leri içerir.|Örnek|
|Gen2Collections|Yes|Gen 2 çöp koleksiyonları|Count|Toplam|Oluşturma 2 nesnelerinin, uygulama işleminin başlangıcından bu yana atık olarak toplandığı sayı.|Örnek|
|Handles|Yes|Tanıtıcı Sayısı|Count|Ortalama|Uygulama işlemi tarafından şu anda açık olan tanıtıcı sayısının toplam sayısı.|Örnek|
|HealthCheckStatus|Yes|Durum denetimi durumu|Count|Ortalama|Durum denetimi durumu|Örnek|
|Http101|Yes|Http 101|Count|Toplam|HTTP durum kodu 101 ile sonuçlanan istek sayısı.|Örnek|
|Http2xx|Yes|Http 2xx|Count|Toplam|HTTP durum koduna neden olan istek sayısı = 200, ancak 300 <.|Örnek|
|Http3xx|Yes|Http 3xx|Count|Toplam|HTTP durum koduna neden olan istek sayısı = 300, ancak 400 <.|Örnek|
|Http401|Yes|Http 401|Count|Toplam|HTTP 401 durum koduna neden olan istek sayısı.|Örnek|
|Http403|Yes|Http 403|Count|Toplam|HTTP 403 durum koduna neden olan istek sayısı.|Örnek|
|Http404|Yes|HTTP 404|Count|Toplam|HTTP 404 durum koduna neden olan istek sayısı.|Örnek|
|Http406|Yes|Http 406|Count|Toplam|HTTP 406 durum koduna neden olan istek sayısı.|Örnek|
|Http4xx|Yes|Http 4xx|Count|Toplam|HTTP durum koduna neden olan istek sayısı = 400, ancak 500 <.|Örnek|
|Http5xx|Yes|Http sunucu hataları|Count|Toplam|HTTP durum koduna neden olan istek sayısı = 500, ancak 600 <.|Örnek|
|HttpResponseTime|Yes|Yanıt süresi|Saniye|Ortalama|Uygulamanın isteklere istek görmesi için geçen süre (saniye cinsinden).|Örnek|
|Iootherbytespersecond|Yes|GÇ diğer bayt/saniye|BytesPerSecond|Toplam|Uygulama işleminin, denetim işlemleri gibi verileri içermeyen g/ç işlemlerine bayt verme hızıdır.|Örnek|
|Iootheroperationspersecond|Yes|GÇ diğer Işlem/saniye|BytesPerSecond|Toplam|Uygulama işleminin okuma veya yazma işlemleri olmayan g/ç işlemlerini verme oranı.|Örnek|
|Ioreadbytespersecond|Yes|GÇ okuma bayt/saniye|BytesPerSecond|Toplam|Uygulama işleminin g/ç işlemlerinden bayt okuma hızıdır.|Örnek|
|IoReadOperationsPerSecond|Yes|GÇ okuma Işlemi/saniye|BytesPerSecond|Toplam|Uygulama işleminin okuma g/ç işlemlerini verme hızıdır.|Örnek|
|Iowritebytespersecond|Yes|GÇ yazma bayt/saniye|BytesPerSecond|Toplam|Uygulama işleminin g/ç işlemlerine bayt yazma hızıdır.|Örnek|
|Iowriteoperationspersecond|Yes|GÇ Yazma Işlemi/saniye|BytesPerSecond|Toplam|Uygulama işleminin yazma g/ç işlemlerini verme hızıdır.|Örnek|
|MemoryWorkingSet|Yes|Bellek çalışma kümesi|Bayt|Ortalama|Uygulama tarafından, MIB 'de kullanılan geçerli bellek miktarı.|Örnek|
|PrivateBytes|Yes|Özel baytlar|Bayt|Ortalama|Özel baytlar, uygulama işleminin ayırdığı ve diğer işlemlerle paylaşılamayan belleğin bayt cinsinden geçerli boyutudur.|Örnek|
|İstekler|Yes|İstekler|Count|Toplam|Elde edilen HTTP durum kodundan bağımsız olarak isteklerin toplam sayısı.|Örnek|
|RequestsInApplicationQueue|Yes|Uygulama kuyruğundaki istekler|Count|Ortalama|Uygulama istek sırasındaki isteklerin sayısı.|Örnek|
|İş Parçacıkları|Yes|İş parçacığı sayısı|Count|Ortalama|Uygulama işleminde Şu anda etkin olan iş parçacıklarının sayısı.|Örnek|
|TotalAppDomains|Yes|Toplam uygulama etki alanları|Count|Ortalama|Bu uygulamada yüklü olan AppDomain 'in geçerli sayısı.|Örnek|
|TotalAppDomainsUnloaded|Yes|Toplam yüklenmeyen uygulama etki alanları|Count|Ortalama|Uygulamanın başlangıcından bu yana bellekten kaldırılan toplam AppDomain sayısı.|Örnek|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/Yuvaları

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Bağlantılar|Count|Ortalama|Korumalı alan içindeki ilişkili yuva sayısı (w3wp.exe ve alt işlemi). Bağlama yuvası, bind ()/Connect () API 'Leri çağırarak ve bir yuva CloseHandle ()/Closesocket () ile kapatılana kadar kalır.|Örnek|
|AverageMemoryWorkingSet|Yes|Ortalama bellek çalışma kümesi|Bayt|Ortalama|Uygulama tarafından, megabayt (MIB) cinsinden kullanılan ortalama bellek miktarı.|Örnek|
|AverageResponseTime|Yes|Ortalama yanıt süresi (kullanım dışı)|Saniye|Ortalama|Uygulama için geçen ortalama süre (saniye cinsinden).|Örnek|
|BytesReceived|Yes|Içindeki veriler|Bayt|Toplam|MIB içinde uygulama tarafından tüketilen gelen bant genişliği miktarı.|Örnek|
|BytesSent|Yes|Giden veriler|Bayt|Toplam|MIB içinde uygulama tarafından tüketilen giden bant genişliği miktarı.|Örnek|
|CpuTime|Yes|CPU süresi|Saniye|Toplam|Uygulama tarafından saniye cinsinden tüketilen CPU miktarı. Bu ölçüm hakkında daha fazla bilgi için. Azure Işlevleri için geçerli değildir. Lütfen bkz https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage . (CPU süresi vs CPU yüzdesi).|Örnek|
|CurrentAssemblies|Yes|Geçerli derlemeler|Count|Ortalama|Bu uygulamadaki tüm AppDomain 'ler genelinde yüklenen derlemelerin geçerli sayısı.|Örnek|
|Filesystemage|Yes|Dosya sistemi kullanımı|Bayt|Ortalama|Uygulama tarafından tüketilen dosya sistemi kotasının yüzdesi.|Boyut yok|
|Işlev yürütme sayısı|Yes|İşlev yürütme sayısı|Count|Toplam|İşlev yürütme sayısı|Örnek|
|FunctionExecutionUnits|Yes|İşlev yürütme birimleri|Count|Toplam|İşlev yürütme birimleri|Örnek|
|Gen0Collections|Yes|Gen 0 çöp koleksiyonları|Count|Toplam|Uygulama işleminin başlangıcından bu yana nesil 0 nesnelerinin atık olarak toplandığı sayı. Daha yüksek nesil GCs, tüm düşük nesil GCs 'leri içerir.|Örnek|
|Gen1Collections|Yes|Gen 1 çöp koleksiyonları|Count|Toplam|Uygulama işleminin başlangıcından bu yana 1. nesil nesnelerin atık olarak toplandığı sayı. Daha yüksek nesil GCs, tüm düşük nesil GCs 'leri içerir.|Örnek|
|Gen2Collections|Yes|Gen 2 çöp koleksiyonları|Count|Toplam|Oluşturma 2 nesnelerinin, uygulama işleminin başlangıcından bu yana atık olarak toplandığı sayı.|Örnek|
|Handles|Yes|Tanıtıcı Sayısı|Count|Ortalama|Uygulama işlemi tarafından şu anda açık olan tanıtıcı sayısının toplam sayısı.|Örnek|
|HealthCheckStatus|Yes|Durum denetimi durumu|Count|Ortalama|Durum denetimi durumu|Örnek|
|Http101|Yes|Http 101|Count|Toplam|HTTP durum kodu 101 ile sonuçlanan istek sayısı.|Örnek|
|Http2xx|Yes|Http 2xx|Count|Toplam|HTTP durum koduna neden olan istek sayısı = 200, ancak 300 <.|Örnek|
|Http3xx|Yes|Http 3xx|Count|Toplam|HTTP durum koduna neden olan istek sayısı = 300, ancak 400 <.|Örnek|
|Http401|Yes|Http 401|Count|Toplam|HTTP 401 durum koduna neden olan istek sayısı.|Örnek|
|Http403|Yes|Http 403|Count|Toplam|HTTP 403 durum koduna neden olan istek sayısı.|Örnek|
|Http404|Yes|HTTP 404|Count|Toplam|HTTP 404 durum koduna neden olan istek sayısı.|Örnek|
|Http406|Yes|Http 406|Count|Toplam|HTTP 406 durum koduna neden olan istek sayısı.|Örnek|
|Http4xx|Yes|Http 4xx|Count|Toplam|HTTP durum koduna neden olan istek sayısı = 400, ancak 500 <.|Örnek|
|Http5xx|Yes|Http sunucu hataları|Count|Toplam|HTTP durum koduna neden olan istek sayısı = 500, ancak 600 <.|Örnek|
|HttpResponseTime|Yes|Yanıt süresi|Saniye|Ortalama|Uygulamanın isteklere istek görmesi için geçen süre (saniye cinsinden).|Örnek|
|Iootherbytespersecond|Yes|GÇ diğer bayt/saniye|BytesPerSecond|Toplam|Uygulama işleminin, denetim işlemleri gibi verileri içermeyen g/ç işlemlerine bayt verme hızıdır.|Örnek|
|Iootheroperationspersecond|Yes|GÇ diğer Işlem/saniye|BytesPerSecond|Toplam|Uygulama işleminin okuma veya yazma işlemleri olmayan g/ç işlemlerini verme oranı.|Örnek|
|Ioreadbytespersecond|Yes|GÇ okuma bayt/saniye|BytesPerSecond|Toplam|Uygulama işleminin g/ç işlemlerinden bayt okuma hızıdır.|Örnek|
|IoReadOperationsPerSecond|Yes|GÇ okuma Işlemi/saniye|BytesPerSecond|Toplam|Uygulama işleminin okuma g/ç işlemlerini verme hızıdır.|Örnek|
|Iowritebytespersecond|Yes|GÇ yazma bayt/saniye|BytesPerSecond|Toplam|Uygulama işleminin g/ç işlemlerine bayt yazma hızıdır.|Örnek|
|Iowriteoperationspersecond|Yes|GÇ Yazma Işlemi/saniye|BytesPerSecond|Toplam|Uygulama işleminin yazma g/ç işlemlerini verme hızıdır.|Örnek|
|MemoryWorkingSet|Yes|Bellek çalışma kümesi|Bayt|Ortalama|Uygulama tarafından, MIB 'de kullanılan geçerli bellek miktarı.|Örnek|
|PrivateBytes|Yes|Özel baytlar|Bayt|Ortalama|Özel baytlar, uygulama işleminin ayırdığı ve diğer işlemlerle paylaşılamayan belleğin bayt cinsinden geçerli boyutudur.|Örnek|
|İstekler|Yes|İstekler|Count|Toplam|Elde edilen HTTP durum kodundan bağımsız olarak isteklerin toplam sayısı.|Örnek|
|RequestsInApplicationQueue|Yes|Uygulama kuyruğundaki istekler|Count|Ortalama|Uygulama istek sırasındaki isteklerin sayısı.|Örnek|
|İş Parçacıkları|Yes|İş parçacığı sayısı|Count|Ortalama|Uygulama işleminde Şu anda etkin olan iş parçacıklarının sayısı.|Örnek|
|TotalAppDomains|Yes|Toplam uygulama etki alanları|Count|Ortalama|Bu uygulamada yüklü olan AppDomain 'in geçerli sayısı.|Örnek|
|TotalAppDomainsUnloaded|Yes|Toplam yüklenmeyen uygulama etki alanları|Count|Ortalama|Uygulamanın başlangıcından bu yana bellekten kaldırılan toplam AppDomain sayısı.|Örnek|


## <a name="microsoftwebstaticsites"></a>Microsoft. Web/staticSites

|Metric|Tanılama ayarları aracılığıyla dışarı aktarılabilir mi?|Ölçüm görünen adı|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|---|
|BytesSent|Yes|Giden veriler|Bayt|Toplam|BytesSent|Örnek|
|FunctionErrors|Yes|FunctionErrors|Count|Toplam|FunctionErrors|Örnek|
|Işlev ısabetleri|Yes|Işlev ısabetleri|Count|Toplam|Işlev ısabetleri|Örnek|
|SiteErrors|Yes|SiteErrors|Count|Toplam|SiteErrors|Örnek|
|SiteHits|Yes|SiteHits|Count|Toplam|SiteHits|Örnek|

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Izleyici 'de ölçümler hakkında bilgi edinin](../data-platform.md)
- [Ölçümler üzerinde uyarı oluşturma](../alerts/alerts-overview.md)
- [Ölçümleri depolama, Olay Hub 'ına veya Log Analytics Dışa aktarma](../essentials/platform-logs-overview.md)
