---
title: Azure IoT Hub veri başvurusunu izleme
description: Azure IoT Hub izlerken gereken önemli başvuru malzemeleri
author: robinsh
ms.author: robinsh
ms.topic: reference
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 10/22/2020
ms.openlocfilehash: 6c7fd0a310663b664d33027951ade885b83d458a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570967"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>Azure IoT Hub veri başvurusunu izleme

Azure IoT Hub izleme verilerinin toplanması ve çözümlenmesi hakkındaki ayrıntılar için bkz. [azure IoT Hub 'Yi izleme](monitor-iot-hub.md) .

## <a name="metrics"></a>Ölçümler

Bu bölümde, Azure IoT Hub için otomatik olarak toplanan tüm platform ölçümleri listelenmektedir. IoT Hub ölçümler için kaynak sağlayıcısı ad alanı **Microsoft. Devices** ve tür ad alanı **Iothubs**'dir.

Aşağıdaki alt bölümlerde, genel kategoriye göre IoT Hub platform ölçümleri kesilir ve ile Azure portal göründükleri görünen ada göre listelenir. Ayrıca, her bir alt bölümde görünen ölçümlerle ilgili bilgiler de sağlanır.

Ayrıca, Azure Izleyici belgelerindeki [Microsoft. Devices/IotHubs](../azure-monitor/essentials/metrics-supported.md#microsoftdevicesiothubs) altında ölçüm adına göre IoT Hub platform ölçümlerini listeleyen tek bir tablo bulabilirsiniz. Bu tabloda, bu makalede sunulan bazı ölçümler için [desteklenen toplamalar](#supported-aggregations) gibi bazı bilgiler sağlamadığının farkında olun.

Diğer Azure hizmetleri tarafından desteklenen ölçümler hakkında bilgi edinmek için bkz. [Azure izleyici Ile desteklenen ölçümler](../azure-monitor/essentials/metrics-supported.md).

**Bu bölümdeki konular**

- [Azure IoT Hub veri başvurusunu izleme](#monitoring-azure-iot-hub-data-reference)
  - [Ölçümler](#metrics)
    - [Desteklenen toplamalar](#supported-aggregations)
    - [Buluttan cihaza komut ölçümleri](#cloud-to-device-command-metrics)
    - [Buluttan cihaza doğrudan Yöntemler ölçümleri](#cloud-to-device-direct-methods-metrics)
    - [Buluttan cihaza ikizi işlemleri ölçümleri](#cloud-to-device-twin-operations-metrics)
    - [Yapılandırma ölçümleri](#configurations-metrics)
    - [Günlük kota ölçümleri](#daily-quota-metrics)
    - [Cihaz ölçümleri](#device-metrics)
    - [Cihaz telemetri ölçümleri](#device-telemetry-metrics)
    - [Cihazdan buluta ikizi işlemler ölçümleri](#device-to-cloud-twin-operations-metrics)
    - [Olay Kılavuzu ölçümleri](#event-grid-metrics)
    - [İş ölçümleri](#jobs-metrics)
    - [Yönlendirme ölçümleri](#routing-metrics)
    - [İkizi sorgu ölçümleri](#twin-query-metrics)
  - [Ölçüm boyutları](#metric-dimensions)
  - [Kaynak günlükleri](#resource-logs)
    - [Bağlantılar](#connections)
    - [Cihaz telemetrisi](#device-telemetry)
    - [Buluttan cihaza komutlar](#cloud-to-device-commands)
    - [Cihaz kimliği işlemleri](#device-identity-operations)
    - [Dosya karşıya yükleme işlemleri](#file-upload-operations)
    - [Rotalar](#routes)
    - [Cihazdan buluta ikizi işlemleri](#device-to-cloud-twin-operations)
    - [Buluttan cihaza ikizi işlemleri](#cloud-to-device-twin-operations)
    - [İkizi sorguları](#twin-queries)
    - [İş işlemleri](#jobs-operations)
    - [Doğrudan Yöntemler](#direct-methods)
    - [Dağıtılmış Izleme (Önizleme)](#distributed-tracing-preview)
      - [IoT Hub D2C (cihazdan buluta) günlükleri](#iot-hub-d2c-device-to-cloud-logs)
      - [Giriş günlüklerini IoT Hub](#iot-hub-ingress-logs)
      - [Çıkış günlüklerini IoT Hub](#iot-hub-egress-logs)
    - [Yapılandırmalar](#configurations)
    - [Cihaz akışları (Önizleme)](#device-streams-preview)
  - [Azure Izleyici günlük tabloları](#azure-monitor-logs-tables)
  - [Ayrıca bkz.](#see-also)

### <a name="supported-aggregations"></a>Desteklenen toplamalar

Her tablodaki **toplama türü** sütunu, bir grafik veya uyarı için ölçüm seçildiğinde kullanılan varsayılan toplamaya karşılık gelir.

   ![Ölçümler için toplamayı gösteren ekran görüntüsü](./media/monitor-iot-hub-reference/aggregation-type.png)

Çoğu ölçüm için tüm toplama türleri geçerlidir; Ancak, sayı ölçümleri için, **birim** sütun değeri **sayısı**, yalnızca bazı toplamalar geçerlidir. Count ölçümleri iki türden biri olabilir:

* **Tek noktalı** sayı ölçümleri için IoT Hub, ölçülen işlem her gerçekleştiğinde tek bir veri noktası kaydettirir (aslında 1--). Azure Izleyici daha sonra bu veri noktalarını belirtilen ayrıntı düzeyine göre toplar. **Tek nokta** ölçümlerine örnek olarak *gönderilen telemetri iletileri* ve *C2D ileti teslimleri tamamlanmıştır*. Bu ölçümler için, yalnızca ilgili toplama türü toplamdır (Toplam). Portal en düşük, en yüksek ve ortalama seçeneklerini seçmenizi sağlar; Ancak, bu değerler her zaman 1 olur.

* **Snapshot** Count ölçümleri için, ölçülen işlem gerçekleştiğinde IoT Hub toplam bir sayı kaydeder. Şu anda IoT Hub tarafından yayılan üç **anlık görüntü** ölçümü vardır: *Toplam kullanılan ileti sayısı*, *Toplam cihaz (Önizleme)* ve *bağlı cihazlar (Önizleme)*. Bu ölçümler her yayıldıklarında bir "Toplam" miktarı sağladığından, bunları belirtilen ayrıntı düzeyi üzerinde toplamak hiçbir fikir vermez. Azure Izleyici, bu ölçümler için toplama türü için Ortalama, en düşük ve en yüksek değerleri seçmenizi sağlar.

### <a name="cloud-to-device-command-metrics"></a>Buluttan cihaza komut ölçümleri

|Ölçüm görünen adı|Metric|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|
|C2D Iletilerinin süre dolma (Önizleme)|C2DMessagesExpired|Count|Toplam|Zaman aşımına uğradı buluttan cihaza ileti sayısı|Yok|
|C2D ileti teslimleri tamamlandı|C2D. Commands. çıkış. tamamlandı. başarılı|Count|Toplam|Cihaz tarafından başarıyla tamamlanan buluttan cihaza ileti teslimleri sayısı|Yok|
|C2D iletileri bırakıldı|C2D. Commands. çıkış. Abandon. Success|Count|Toplam|Cihaz tarafından bırakılan buluttan cihaza yönelik ileti sayısı|Yok|
|C2D iletileri reddedildi|C2D. Commands. çıkış. Red. Success|Count|Toplam|Cihaz tarafından reddedilen buluttan cihaza yönelik ileti sayısı|Yok|

**Birim** değeri **sayısı** olan ölçümler için, yalnızca toplam (Sum) toplama geçerlidir. En düşük, en yüksek ve ortalama toplamalar her zaman 1 döndürür. Daha fazla bilgi için bkz. [desteklenen toplamalar](#supported-aggregations).

### <a name="cloud-to-device-direct-methods-metrics"></a>Buluttan cihaza doğrudan Yöntemler ölçümleri

|Ölçüm görünen adı|Metric|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|
|Başarısız doğrudan yöntem etkinleştirmeleri|C2D. Methods. Failure|Count|Toplam|Tüm başarısız doğrudan yöntem çağrılarının sayısı.|Yok|
|Doğrudan yöntem etkinleştirmeleri istek boyutu|C2D. Methods. requestSize|Bayt|Ortalama|Tüm başarılı doğrudan yöntem isteklerinin sayısı.|Yok|
|Doğrudan yöntem etkinleştirmeleri yanıt boyutu|C2D. Methods. responseSize|Bayt|Ortalama|Tüm başarılı doğrudan yöntem yanıtlarının sayısı.|Yok|
|Başarılı doğrudan yöntem etkinleştirmeleri|C2D. Methods. Success|Count|Toplam|Tüm başarılı doğrudan yöntem çağrılarının sayısı.|Yok|

**Birim** değeri **sayı** olan ölçümler için yalnızca toplam (Toplam) toplama geçerlidir. En düşük, en yüksek ve ortalama toplamalar her zaman 1 döndürür. Daha fazla bilgi için bkz. [desteklenen toplamalar](#supported-aggregations).

### <a name="cloud-to-device-twin-operations-metrics"></a>Buluttan cihaza ikizi işlemleri ölçümleri

|Ölçüm görünen adı|Metric|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|
|Arka uçtan başarısız ikizi okumaları|C2D. ikizi. Read. Failure|Count|Toplam|Tüm başarısız arka uç tarafından başlatılan ikizi okumaların sayısı.|Yok|
|Arka uçtan başarısız ikizi güncelleştirmeleri|C2D. ikizi. Update. Failure|Count|Toplam|Tüm başarısız arka uç başlatılan ikizi güncelleştirmelerinin sayısı.|Yok|
|Arka uçtan gelen ikizi okumaların yanıt boyutu|C2D. ikizi. Read. size|Bayt|Ortalama|Tüm başarılı arka uç tarafından başlatılan ikizi okumaların sayısı.|Yok|
|Arka uçtan ikizi güncelleştirmelerinin boyutu|C2D. ikizi. Update. size|Bayt|Ortalama|Tüm başarılı arka uç ile başlatılan ikizi güncelleştirmelerinin toplam boyutu.|Yok|
|Arka uçtan başarılı ikizi okumaları|C2D. ikizi. Read. Success|Count|Toplam|Tüm başarılı arka uç tarafından başlatılan ikizi okumaların sayısı.|Yok|
|Arka uçtan başarılı ikizi güncelleştirmeleri|C2D. ikizi. Update. Success|Count|Toplam|Tüm başarılı arka uç ile başlatılan ikizi güncelleştirmelerinin sayısı.|Yok|

**Birim** değeri **sayısı** olan ölçümler için, yalnızca toplam (Sum) toplama geçerlidir. En düşük, en yüksek ve ortalama toplamalar her zaman 1 döndürür. Daha fazla bilgi için bkz. [desteklenen toplamalar](#supported-aggregations).

### <a name="configurations-metrics"></a>Yapılandırma ölçümleri

|Ölçüm görünen adı|Metric|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|
|Yapılandırma ölçümleri|konfigürasyonları|Count|Toplam|Bir hedef cihaz kümesinde cihaz yapılandırması ve IoT Edge dağıtımı için gerçekleştirilen toplam CRUD işlemlerinin sayısı. Bu Ayrıca, bu yapılandırmalardan dolayı cihaz ikizi veya modül ikizi ' i değiştiren işlem sayısını da içerir.|Yok|

**Birim** değeri **sayısı** olan ölçümler için, yalnızca toplam (Sum) toplama geçerlidir. En düşük, en yüksek ve ortalama toplamalar her zaman 1 döndürür. Daha fazla bilgi için bkz. [desteklenen toplamalar](#supported-aggregations).

### <a name="daily-quota-metrics"></a>Günlük kota ölçümleri

|Ölçüm görünen adı|Metric|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|
|Toplam cihaz verisi kullanımı|deviceDataUsage|Bayt|Toplam|Iothub 'e bağlı tüm cihazlardan ve bunlardan aktarılan baytlar|Yok|
|Toplam cihaz verisi kullanımı (Önizleme)|deviceDataUsageV2|Bayt|Toplam|Iothub 'e bağlı tüm cihazlardan ve bunlardan aktarılan baytlar|Yok|
|Kullanılan toplam ileti sayısı|Günlükmessagequotakullanıldı|Count|Ortalama|Bugün kullanılan toplam ileti sayısı. Bu, her gün 00:00 UTC tarihinde sıfıra sıfırlanan toplu bir değerdir.|Yok|

*Kullanılan toplam ileti sayısı* için yalnızca minimum, maksimum ve ortalama toplamalar desteklenir. Daha fazla bilgi için bkz. [desteklenen toplamalar](#supported-aggregations).

### <a name="device-metrics"></a>Cihaz ölçümleri

|Ölçüm görünen adı|Metric|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|
|Toplam cihaz (kullanım dışı)|Devices. totalDevices|Count|Toplam|IoT Hub 'ınıza kayıtlı cihazların sayısı|Yok|
|Bağlı cihazlar (kullanım dışı) |Devices. connectedDevices. allProtocol|Count|Toplam|IoT Hub 'ınıza bağlı cihazların sayısı|Yok|
|Toplam cihaz (Önizleme)|totalDeviceCount|Count|Ortalama|IoT Hub 'ınıza kayıtlı cihazların sayısı|Yok|
|Bağlı cihazlar (Önizleme)|connectedDeviceCount|Count|Ortalama|IoT Hub 'ınıza bağlı cihazların sayısı|Yok|

*Toplam cihazlar (kullanım dışı)* ve *bağlı cihazlar (kullanım dışı)* için, yalnızca toplam (Sum) toplama geçerlidir. En düşük, en yüksek ve ortalama toplamalar her zaman 1 döndürür. Daha fazla bilgi için bkz. [desteklenen toplamalar](#supported-aggregations).

*Toplam cihazlar (Önizleme)* ve *bağlı cihazlar (Önizleme)* için yalnızca minimum, maksimum ve ortalama toplamalar geçerlidir. Daha fazla bilgi için bkz. [desteklenen toplamalar](#supported-aggregations).

*Bağlı cihazlar (Önizleme)* ve *Toplam cihaz (Önizleme)* , Tanılama ayarları aracılığıyla dışarı aktarılabilir değildir.

### <a name="device-telemetry-metrics"></a>Cihaz telemetri ölçümleri

|Ölçüm görünen adı|Metric|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|
|Daraltma hatası sayısı|D2C. telemetri. ınress. Sendvaliz|Count|Toplam|Cihaz performansı azaltmasından kaynaklanan azaltma hatası sayısı|Yok|
|Telemetri iletisi gönderme denemeleri|D2C. telemetri. ınress. allProtocol|Count|Toplam|IoT Hub 'ınıza gönderilmeye çalışılan cihazdan buluta telemetri iletisi sayısı|Yok|
|Gönderilen telemetri iletileri|D2C. telemetri. giriş. başarılı|Count|Toplam|IoT Hub 'ınıza başarıyla gönderilen cihazdan buluta telemetri iletisi sayısı|Yok|

**Birim** değeri **sayısı** olan ölçümler için, yalnızca toplam (Sum) toplama geçerlidir. En düşük, en yüksek ve ortalama toplamalar her zaman 1 döndürür. Daha fazla bilgi için bkz. [desteklenen toplamalar](#supported-aggregations).

### <a name="device-to-cloud-twin-operations-metrics"></a>Cihazdan buluta ikizi işlemler ölçümleri

|Ölçüm görünen adı|Metric|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|
|Cihazlardan başarısız ikizi okumaları|D2C. ikizi. Read. Failure|Count|Toplam|Tüm başarısız cihaz tarafından başlatılan ikizi okumaların sayısı.|Yok|
|Cihazlardan ikizi güncelleştirmeleri başarısız oldu|D2C. ikizi. Update. Failure|Count|Toplam|Tüm başarısız cihaz tarafından başlatılan ikizi güncelleştirmelerinin sayısı.|Yok|
|Cihazlardan gelen ikizi okumaların yanıt boyutu|D2C. ikizi. Read. size|Bayt|Ortalama|Tüm başarılı cihaz tarafından başlatılan ikizi okuma sayısı.|Yok|
|Cihazlardan ikizi güncelleştirmelerinin boyutu|D2C. ikizi. Update. size|Bayt|Ortalama|Tüm başarılı cihaz tarafından başlatılan ikizi güncelleştirmelerinin toplam boyutu.|Yok|
|Cihazlardan başarılı ikizi okumaları|D2C. ikizi. Read. Success|Count|Toplam|Tüm başarılı cihaz tarafından başlatılan ikizi okuma sayısı.|Yok|
|Cihazlardan başarılı ikizi güncelleştirmeleri|D2C. ikizi. Update. Success|Count|Toplam|Tüm başarılı cihaz tarafından başlatılan ikizi güncelleştirmelerinin sayısı.|Yok|

**Birim** değeri **sayısı** olan ölçümler için, yalnızca toplam (Sum) toplama geçerlidir. En düşük, en yüksek ve ortalama toplamalar her zaman 1 döndürür. Daha fazla bilgi için bkz. [desteklenen toplamalar](#supported-aggregations).

### <a name="event-grid-metrics"></a>Olay Kılavuzu ölçümleri

|Ölçüm görünen adı|Metric|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|
|Event Grid teslimler (Önizleme)|Eventgridteslimler|Count|Toplam|Event Grid yayımlanan IoT Hub olaylarının sayısı. Başarılı ve başarısız isteklerin sayısı için sonuç boyutunu kullanın. EventType boyutu olay türünü gösterir ( https://aka.ms/ioteventgrid) .|Sonuç,<br/>Olay türü<br>*Daha fazla bilgi için bkz. [ölçüm boyutları](#metric-dimensions)*.|
|Event Grid gecikme süresi (Önizleme)|EventGridLatency|Mayacak|Ortalama|Olay Event Grid yayımlandığında IoT Hub olayının oluşturulduğu sürenin ortalama gecikme süresi (milisaniye). Bu sayı, tüm olay türleri arasındaki bir ortalama değer. Belirli bir olay türünün gecikmesini görmek için EventType boyutunu kullanın.|Olay türü<br>*Daha fazla bilgi için bkz. [ölçüm boyutları](#metric-dimensions)*.|

**Birim** değeri **sayısı** olan ölçümler için, yalnızca toplam (Sum) toplama geçerlidir. En düşük, en yüksek ve ortalama toplamalar her zaman 1 döndürür. Daha fazla bilgi için bkz. [desteklenen toplamalar](#supported-aggregations).

### <a name="jobs-metrics"></a>İş ölçümleri

|Ölçüm görünen adı|Metric|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|
|Tamamlanan İşler|işler. tamamlandı|Count|Toplam|Tamamlanan tüm işlerin sayısı.|Yok|
|İşleri listelemek için başarısız çağrılar|Jobs. listJobs. hata|Count|Toplam|Listeleme işleri için başarısız olan tüm çağrıların sayısı.|Yok|
|Yöntem çağırma işlerinin oluşturma işlemi başarısız oldu|Jobs. createDirectMethodJob. Failure|Count|Toplam|Doğrudan yöntem çağırma işlerinin tüm başarısız oluşturma sayısı.|Yok|
|İkizi Update işlerinin başarısız oluşturmaları|Jobs. createTwinUpdateJob. Failure|Count|Toplam|İkizi Update işlerinin başarısız olan tüm oluşturma sayısı.|Yok|
|Başarısız iş iptalleri|Jobs. cancelJob. Failure|Count|Toplam|İşi iptal etmek için başarısız olan tüm çağrıların sayısı.|Yok|
|Başarısız iş sorguları|Jobs. queryJobs. hata|Count|Toplam|Sorgu işlerine yapılan tüm başarısız çağrıların sayısı.|Yok|
|Başarısız işler|işler. başarısız|Count|Toplam|Başarısız olan tüm işlerin sayısı.|Yok|
|İşleri listelemek için başarılı çağrılar|işler. listJobs. başarılı|Count|Toplam|İşleri listelemek için başarılı olan tüm çağrıların sayısı.|Yok|
|Yöntem çağırma işlerinin başarılı oluşturmaları|Jobs. createDirectMethodJob. Success|Count|Toplam|Tüm başarılı doğrudan yöntem çağırma işlerinin oluşturulma sayısı.|Yok|
|İkizi Update işlerinin başarılı oluşturmaları|Jobs. createTwinUpdateJob.<br>başarılı|Count|Toplam|Tüm başarılı ikizi Update işlerinin oluşturma sayısı.|Yok|
|Başarılı iş iptalleri|Jobs. cancelJob. Success|Count|Toplam|İşi iptal etmek için tüm başarılı çağrı sayısı.|Yok|
|Başarılı iş sorguları|işler. queryJobs. başarılı|Count|Toplam|Sorgu işlerine yapılan tüm başarılı çağrıların sayısı.|Yok|

**Birim** değeri **sayısı** olan ölçümler için, yalnızca toplam (Sum) toplama geçerlidir. En düşük, en yüksek ve ortalama toplamalar her zaman 1 döndürür. Daha fazla bilgi için bkz. [desteklenen toplamalar](#supported-aggregations).

### <a name="routing-metrics"></a>Yönlendirme ölçümleri

|Ölçüm görünen adı|Metric|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|
| Yönlendirme teslim denemeleri (Önizleme) |Routingteslimler | Count | Toplam |Bu, yönlendirme teslimi ölçümdür. Belirli bir uç noktanın teslim durumunu veya belirli bir yönlendirme kaynağını belirlemek için boyutları kullanın.| Sonuç,<br>RoutingSource,<br>EndpointType,<br>FailureReasonCategory,<br>Uçnoktaadı<br>*Daha fazla bilgi için bkz. [ölçüm boyutları](#metric-dimensions)*. |
| Yönlendirme teslim verilerinin bayt cinsinden boyutu (Önizleme)|RoutingDataSizeInBytesDelivered| Bayt | Toplam |IoT Hub tarafından özel uç noktaya ve yerleşik uç noktaya yönlendirilen toplam bayt sayısı. Belirli bir uç noktaya veya belirli bir yönlendirme kaynağına yönlendirilen veri boyutunu tanımlamak için boyutları kullanın.| RoutingSource,<br>EndpointType<br>Uçnoktaadı<br>*Daha fazla bilgi için bkz. [ölçüm boyutları](#metric-dimensions)*.|
| Yönlendirme gecikmesi (Önizleme) |Routingdeliverygecikme süresi| Mayacak | Ortalama |Bu, yönlendirme teslimi gecikme ölçümdür. Belirli bir uç noktanın gecikmesini veya belirli bir yönlendirme kaynağını belirlemek için boyutları kullanın.| RoutingSource,<br>EndpointType,<br>Uçnoktaadı<br>*Daha fazla bilgi için bkz. [ölçüm boyutları](#metric-dimensions)*.|
|Yönlendirme: depolamaya teslim edilen Bloblar|D2C. endpoints. çıkış. Storage. blob 'ları|Count|Toplam|IoT Hub blob 'ları depolama uç noktalarına yönlendirmenin kaç kez yönlendirtığı sayısıdır.|Yok|
|Yönlendirme: depolamaya teslim edilen veriler|D2C. endpoints. çıkış. Storage. Bytes|Bayt|Toplam|Depolama uç noktalarına teslim edilen veri miktarı (bayt) IoT Hub.|Yok|
|Yönlendirme: Olay Hub 'ı için ileti gecikmesi|D2C. endpoints. Latency. eventHubs|Mayacak|Ortalama|Olay Hub 'ının özel uç noktalarına IoT Hub ileti girişi ve ileti girişi arasındaki ortalama gecikme süresi (milisaniye). Bu, yerleşik uç noktaya yönelik ileti yollarını içermez (olaylar).|Yok|
|Yönlendirme: Service Bus kuyruğu için ileti gecikmesi|D2C. endpoints. Latency. serviceBusQueues|Mayacak|Ortalama|IoT Hub ileti girişi ile Service Bus kuyruğu uç noktasına giriş arasındaki ortalama gecikme süresi (milisaniye).|Yok|
|Yönlendirme: Service Bus konusu için ileti gecikmesi|D2C. endpoints. Latency. Servicebuskonular|Mayacak|Ortalama|IoT Hub ve ileti girişi arasındaki ortalama gecikme süresi (milisaniye) Service Bus konu uç noktasına giriş.|Yok|
|Yönlendirme: iletiler/olaylar için ileti gecikmesi|D2C. endpoints. Latency. yerleik. Events|Mayacak|Ortalama|Yerleşik uç noktaya (iletiler/olaylar) ve geri dönüş rotasında ileti girişi IoT Hub ve ileti girişi arasındaki ortalama gecikme süresi (milisaniye).|Yok|
|Yönlendirme: depolama için ileti gecikmesi|D2C. endpoints. Latency. Storage|Mayacak|Ortalama|Bir depolama uç noktasında ileti girişi IoT Hub ve ileti girişi arasındaki ortalama gecikme süresi (milisaniye).|Yok|
|Yönlendirme: Olay Hub 'ına teslim edilen iletiler|D2C. endpoints. çıkış. eventHubs|Count|Toplam|IoT Hub yönlendirmenin, Olay Hub 'ı türünde özel uç noktalara başarıyla ileti teslim dağıttığı sayı. Bu, yerleşik uç noktaya yönelik ileti yollarını içermez (olaylar).|Yok|
|Yönlendirme: Service Bus kuyruğuna teslim edilen iletiler|D2C. endpoints. çıkış. serviceBusQueues|Count|Toplam|IoT Hub yönlendirmenin iletileri Service Bus sıra uç noktalarına başarıyla teslim dağıttığı zaman sayısı.|Yok|
|Yönlendirme: Service Bus konuya teslim edilen iletiler|D2C. endpoints. çıkış. Servicebuskonular|Count|Toplam|IoT Hub yönlendirmenin Service Bus konu uç noktalarına başarıyla ileti teslim eden zaman sayısı.|Yok|
|Yönlendirme: geri dönüşe teslim edilen iletiler|D2C. telemetri. çıkış. geri dönüş|Count|Toplam|Geri dönüş rotası ile ilişkili uç noktaya gönderilen iletileri yönlendirmenin IoT Hub sayısı.|Yok|
|Yönlendirme: iletilere/olaylara teslim edilen iletiler|D2C. endpoints. çıkış. builtIn. Events|Count|Toplam|IoT Hub yönlendirmenin, yerleşik uç noktaya (iletiler/olaylar) ve geri dönüş rotasında iletileri başarıyla teslim eden zaman sayısı.|Yok|
|Yönlendirme: depolamaya teslim edilen iletiler|D2C. endpoints. çıkış. Storage|Count|Toplam|IoT Hub yönlendirmenin depolama uç noktalarına başarıyla ileti teslim eden zaman sayısı.|Yok|
|Yönlendirme: teslim edilen telemetri iletileri|D2C. telemetri. çıkış. başarılı|Count|Toplam|IoT Hub yönlendirme kullanılarak iletilerin tüm uç noktalara başarıyla ulaştırılan ileti sayısı. Bir ileti birden çok uç noktaya yönlendirilmemişse, her başarılı teslimat için bu değer bir artar. Bir ileti aynı uç noktaya birden çok kez teslim edildiğinde, her başarılı teslimat için bu değer bir artar.|Yok|
|Yönlendirme: telemetri iletileri bırakıldı |D2C. telemetri. çıkış. bırakıldı|Count|Toplam|İleti, ölü uç noktalar nedeniyle IoT Hub yönlendirmenin kaç kez bırakılmakta olduğunu. Bu değer, bırakılan iletiler buraya teslim edimediğinden geri dönüş rotasına teslim edilen iletileri saymaz.|Yok|
|Yönlendirme: telemetri iletileri uyumsuz|D2C. telemetri. çıkış. geçersiz|Count|Toplam|Uç nokta ile uyumsuzluk nedeniyle IoT Hub yönlendirmenin iletileri teslim etme başarısız olduğu zaman sayısı. IoT Hub, iletiyi bir uç noktaya teslim etmeye çalıştığında ve geçici olmayan bir hata ile başarısız olduğunda bir ileti bir uç nokta ile uyumsuzdur. Geçersiz iletiler yeniden denenmez. Bu değer yeniden denemeler içermez.|Yok|
|Yönlendirme: telemetri iletileri yalnız bırakılmış |D2C. telemetri. çıkış. yalnız bırakılmış|Count|Toplam|Geri dönüş rotası devre dışı bırakıldığında hiçbir yönlendirme sorgusuyla eşleşmediğinden iletilerin IoT Hub yönlendirme tarafından yalnız bırakılmış olduğu zaman sayısı.|Yok|

**Birim** değeri **sayısı** olan ölçümler için, yalnızca toplam (Sum) toplama geçerlidir. En düşük, en yüksek ve ortalama toplamalar her zaman 1 döndürür. Daha fazla bilgi için bkz. [desteklenen toplamalar](#supported-aggregations).

### <a name="twin-query-metrics"></a>İkizi sorgu ölçümleri

|Ölçüm görünen adı|Metric|Birim|Toplama Türü|Description|Boyutlar|
|---|---|---|---|---|---|
|Başarısız ikizi sorguları|twinQueries. Failure|Count|Toplam|Tüm başarısız ikizi sorgularının sayısı.|Yok|
|Başarılı ikizi sorguları|twinQueries. Success|Count|Toplam|Tüm başarılı ikizi sorgularının sayısı.|Yok|
|İkizi sorguları sonuç boyutu|twinQueries. resultSize|Bayt|Ortalama|Tüm başarılı ikizi sorgularının sonuç boyutunun toplamı.|Yok|

**Birim** değeri **sayısı** olan ölçümler için, yalnızca toplam (Sum) toplama geçerlidir. En düşük, en yüksek ve ortalama toplamalar her zaman 1 döndürür. Daha fazla bilgi için bkz. [desteklenen toplamalar](#supported-aggregations).

## <a name="metric-dimensions"></a>Ölçüm boyutları

Azure IoT Hub, bazı yönlendirme ve olay Kılavuzu ölçümleriyle ilişkili aşağıdaki boyutlara sahiptir.

|Boyut adı | Description|
|---|---|
||
|**Uçnoktaadı**| Uç nokta adı.|
|**EndpointType**|Aşağıdakilerden biri: **Eventhubs**, **servicebusqueues**, **cosmosdb**, **servicebuskonular**. **yerleşik** veya **blobstorage**.|
|**Türü**| Aşağıdaki Event Grid olay türlerinden biri: **Microsoft. Devices. DeviceCreated**. **Microsoft. Devices. DeviceDeleted**, **Microsoft. Devices. DeviceConnected**, **Microsoft. Devices. DeviceConnected** veya **Microsoft. Devices. devicetelemetrisi**. Daha fazla bilgi için bkz. [olay türleri](iot-hub-event-grid.md#event-types).|
|**FailureReasonCategory**| Aşağıdakilerden biri: **geçersiz**, **bırakılmış**, **yalnız bırakılmış** veya **null**.|
|**Sonuç**| **Başarılı** veya **başarısız**.|
|**Yönlendirme kaynağı**| Cihaz Iletileri<br>İkizi değişiklik olayları<br>Cihaz yaşam döngüsü olayları|

Ölçüm boyutları hakkında daha fazla bilgi için bkz. [çok boyutlu ölçümler](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

## <a name="resource-logs"></a>Kaynak günlükleri

Bu bölümde, Azure IoT Hub için toplanan tüm kaynak günlüğü kategori türleri ve şemaları listelenmektedir. Tüm IoT Hub günlüklerin kaynak sağlayıcısı ve türü [Microsoft. Devices/IotHubs](../azure-monitor/essentials/resource-logs-categories.md#microsoftdevicesiothubs).

**Bu bölümdeki konular**

- [Azure IoT Hub veri başvurusunu izleme](#monitoring-azure-iot-hub-data-reference)
  - [Ölçümler](#metrics)
    - [Desteklenen toplamalar](#supported-aggregations)
    - [Buluttan cihaza komut ölçümleri](#cloud-to-device-command-metrics)
    - [Buluttan cihaza doğrudan Yöntemler ölçümleri](#cloud-to-device-direct-methods-metrics)
    - [Buluttan cihaza ikizi işlemleri ölçümleri](#cloud-to-device-twin-operations-metrics)
    - [Yapılandırma ölçümleri](#configurations-metrics)
    - [Günlük kota ölçümleri](#daily-quota-metrics)
    - [Cihaz ölçümleri](#device-metrics)
    - [Cihaz telemetri ölçümleri](#device-telemetry-metrics)
    - [Cihazdan buluta ikizi işlemler ölçümleri](#device-to-cloud-twin-operations-metrics)
    - [Olay Kılavuzu ölçümleri](#event-grid-metrics)
    - [İş ölçümleri](#jobs-metrics)
    - [Yönlendirme ölçümleri](#routing-metrics)
    - [İkizi sorgu ölçümleri](#twin-query-metrics)
  - [Ölçüm boyutları](#metric-dimensions)
  - [Kaynak günlükleri](#resource-logs)
    - [Bağlantılar](#connections)
    - [Cihaz telemetrisi](#device-telemetry)
    - [Buluttan cihaza komutlar](#cloud-to-device-commands)
    - [Cihaz kimliği işlemleri](#device-identity-operations)
    - [Dosya karşıya yükleme işlemleri](#file-upload-operations)
    - [Rotalar](#routes)
    - [Cihazdan buluta ikizi işlemleri](#device-to-cloud-twin-operations)
    - [Buluttan cihaza ikizi işlemleri](#cloud-to-device-twin-operations)
    - [İkizi sorguları](#twin-queries)
    - [İş işlemleri](#jobs-operations)
    - [Doğrudan Yöntemler](#direct-methods)
    - [Dağıtılmış Izleme (Önizleme)](#distributed-tracing-preview)
      - [IoT Hub D2C (cihazdan buluta) günlükleri](#iot-hub-d2c-device-to-cloud-logs)
      - [Giriş günlüklerini IoT Hub](#iot-hub-ingress-logs)
      - [Çıkış günlüklerini IoT Hub](#iot-hub-egress-logs)
    - [Yapılandırmalar](#configurations)
    - [Cihaz akışları (Önizleme)](#device-streams-preview)
  - [Azure Izleyici günlük tabloları](#azure-monitor-logs-tables)
  - [Ayrıca bkz.](#see-also)

### <a name="connections"></a>Bağlantılar

Bağlantılar kategorisi, cihaz bağlantısını ve bir IoT Hub 'ından olayları ve bağlantı kesmeyi izler. Bu kategori, cihazların bağlantısını kaybettiyseniz yetkisiz bağlantı girişimlerini belirlemek ve veya uyarma için faydalıdır.

> [!NOTE]
> Cihazların güvenilir bağlantı durumu için [cihaz sinyalini](iot-hub-devguide-identity-registry.md#device-heartbeat)denetleyin.

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>Cihaz telemetrisi

Cihaz telemetri kategorisi, IoT Hub 'ında oluşan hataları izler ve telemetri işlem hattı ile ilgilidir. Bu kategori telemetri olayları gönderilirken (daraltma gibi) ve telemetri olaylarının (yetkisiz okuyucu gibi) alınması sırasında oluşan hataları içerir. Bu kategori, cihazın kendisinde çalışan kodun neden olduğu hataları yakalayamaz.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>Buluttan cihaza komutlar

Buluttan cihaza komutlar kategorisi, IoT Hub 'ında oluşan ve buluttan cihaza ileti işlem hattı ile ilgili hataları izler. Bu kategori, şuradan oluşan hataları içerir:

* Buluttan cihaza iletileri gönderme (yetkisiz gönderen hatalar gibi),
* Buluttan cihaza iletileri alma (örneğin, teslim sayısı aşıldı hataları) ve
* Buluttan cihaza ileti geri bildirimi alma (geri bildirim süre sonu hataları gibi).

Bu kategori, buluttan cihaza ileti başarıyla teslim edildiğinde, ancak cihaz tarafından yanlış işlenirse hataları yakalamaz.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>Cihaz kimliği işlemleri

Cihaz kimliği işlemler kategorisi, IoT Hub 'ının kimlik kayıt defterinde bir girişi oluşturmaya, güncelleştirmeye veya silmeye çalıştığınızda oluşan hataları izler. Bu kategoriyi izlemek, senaryoları sağlamak için faydalıdır.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>Dosya karşıya yükleme işlemleri

Karşıya dosya yükleme kategorisi, IoT Hub 'ında oluşan ve karşıya dosya yükleme işlevselliğiyle ilgili hataları izler. Bu kategori şunları içerir:

* Bir cihazın, tamamlanmış bir karşıya yükleme merkezine bildirim göndermeden önce ne zaman dolacağını belirten SAS URI 'SI ile oluşan hatalar.

* Cihaz tarafından raporlanan karşıya yüklemeler başarısız oldu.

* IoT Hub bildirim iletisi oluşturma sırasında depolamada bir dosya bulunamadığında oluşan hatalar.

Bu kategori, cihaz bir dosyayı depoya yüklerken doğrudan oluşan hataları yakalayamaz.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>Yollar

[İleti yönlendirme](./iot-hub-devguide-messages-d2c.md) kategorisi, IoT Hub tarafından algılanan ileti yolu değerlendirmesi ve uç nokta durumu sırasında oluşan hataları izler. Bu kategori, şunlar gibi olayları içerir:

* Bir kural "tanımsız" olarak değerlendirilir,
* IoT Hub bir uç noktayı ölü olarak işaretler veya
* Bir uç noktadan alınan hatalar.

Bu kategori, "cihaz telemetrisi" kategorisi altında bildirilen iletiler hakkındaki belirli hataları (cihaz azaltma hataları gibi) içermez.

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

Yönlendirme kaynak günlükleri hakkında daha fazla ayrıntı aşağıda verilmiştir:

* [Yönlendirme kaynak günlüğü hata kodlarının listesi](troubleshoot-message-routing.md#diagnostics-error-codes)
* [Yönlendirme kaynağı günlüklerinin operationNames listesi](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>Cihazdan buluta ikizi işlemleri

Cihazdan buluta ikizi Operations kategorisi cihaz ikizlerinin cihaz tarafından başlatılan olaylarını izler. Bu işlemler Get ikizi, bildirilen özellikleri güncelleştirebilir ve istenen özelliklere abone olabilir.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>Buluttan cihaza ikizi işlemleri

Buluttan cihaza ikizi Operations kategorisi, cihaz ikizlerinin hizmet tarafından başlatılan olaylarını izler. Bu işlemler, Get ikizi, etiketleri güncelleştirebilir veya değiştirebilir ve istenen özellikleri güncelleştirebilir veya değiştirebilir.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>İkizi sorguları

İkizi, bulutta başlatılan cihaz TWINS 'e yönelik sorgu istekleri hakkında rapor raporlar.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>İş işlemleri

İş istekleri kategorisi, cihaz iklerini güncelleştirmek veya birden çok cihazda doğrudan yöntemleri çağırmak için iş istekleri hakkında rapor sağlar. Bu istekler bulutta başlatılır.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>Doğrudan Yöntemler

Doğrudan Yöntemler kategorisi, bireysel cihazlara gönderilen istek-yanıt etkileşimlerini izler. Bu istekler bulutta başlatılır.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>Dağıtılmış Izleme (Önizleme)

Dağıtılmış izleme kategorisi, izleme bağlam üstbilgisini taşıyan iletiler için bağıntı kimliklerini izler. Bu günlükleri tam olarak etkinleştirmek için, istemci tarafı kodu, [IoT Hub dağıtılmış izleme (Önizleme) ile uçtan uca olan IoT uygulamalarını çözümleyin ve tanılayın](iot-hub-distributed-tracing.md).

`correlationId` [W3C Trace bağlam](https://github.com/w3c/trace-context) teklifine, ve ' a ve ' a sahip olduğunu unutmayın `trace-id` `span-id` .

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C (cihazdan buluta) günlükleri

IoT Hub, geçerli izleme özellikleri içeren bir ileti IoT Hub geldiğinde bu günlüğü kaydeder.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Burada, `durationMs` IoT Hub saatinin cihaz saatiyle eşitlenmiş olmaması ve bu nedenle bir süre hesaplamasının yanıltıcı olması için hesaplanmaz. `properties`Cihazdan buluta gecikme süresini yakalamak için bölümündeki zaman damgalarını kullanarak mantık yazma öneririz.

| Özellik | Tür | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Tamsayı | Cihazdan buluta iletinin bayt cinsinden boyutu |
| **deviceId** | ASCII 7 bit alfasayısal karakter dizesi | Cihazın kimliği |
| **callerLocalTimeUtc** | UTC zaman damgası | Cihazın yerel saat tarafından raporlanan Oluşturulma saati |
| **calleeLocalTimeUtc** | UTC zaman damgası | IoT Hub ağ geçidinde ileti alma zamanı IoT Hub hizmet tarafı saatine göre bildirildi |

#### <a name="iot-hub-ingress-logs"></a>Giriş günlüklerini IoT Hub

IoT Hub, geçerli izleme özellikleri içeren ileti iç veya yerleşik Olay Hub 'ına yazdığında bu günlüğü kaydeder.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

`properties`Bölümünde, bu günlük ileti girişi hakkında ek bilgiler içerir.

| Özellik | Tür | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | Dize | True veya false, IoT Hub ileti yönlendirmenin etkin olup olmadığını gösterir |
| **Parentspanıd** | Dize | Bu durumda D2C ileti izlemesi olacak üst iletinin [yayılma kimliği](https://w3c.github.io/trace-context/#parent-id) |

#### <a name="iot-hub-egress-logs"></a>Çıkış günlüklerini IoT Hub

IoT Hub [yönlendirme](iot-hub-devguide-messages-d2c.md) etkinken ve ileti bir [uç noktaya](iot-hub-devguide-endpoints.md)yazıldığında bu günlüğü kaydeder. Yönlendirme etkinleştirilmemişse IoT Hub bu günlüğü kaydetmez.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

`properties`Bölümünde, bu günlük ileti girişi hakkında ek bilgiler içerir.

| Özellik | Tür | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **Uçnoktaadı** | Dize | Yönlendirme uç noktasının adı |
| **endpointType** | Dize | Yönlendirme uç noktasının türü |
| **Parentspanıd** | Dize | Bu durumda IoT Hub giriş iletisi izlemesi olacak üst iletinin [yayılma kimliği](https://w3c.github.io/trace-context/#parent-id) |

### <a name="configurations"></a>Yapılandırmalar

IoT Hub yapılandırma günlükleri otomatik cihaz yönetimi özellik kümesi için olayları ve hataları izler.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Cihaz akışları (Önizleme)

Cihaz akışları kategorisi, bireysel cihazlara gönderilen istek-yanıt etkileşimlerini izler.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Azure Izleyici günlük tabloları
<!-- REQUIRED. Please keep heading in this order -->

Bu bölüm, Azure IoT Hub ilgili olan ve Log Analytics tarafından sorgu için kullanılabilen tüm Azure Izleyici günlüklerini gösterir. Bu tabloların listesi ve IoT Hub kaynak türü için daha fazla bilgi bağlantıları için bkz. Azure Izleyici Günlükler tablosu başvurusu [IoT Hub](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub) .

Tüm Azure Izleyici günlükleri/Log Analytics tablolarının bir başvurusu için bkz. [Azure Izleyici günlük tablosu başvurusu](/azure/azure-monitor/reference/tables/tables-resourcetype).

## <a name="see-also"></a>Ayrıca Bkz.

* Bkz. Azure IoT Hub izleme açıklaması için bkz. [azure IoT Hub](monitor-iot-hub.md) izleme.
* Azure kaynaklarını izleme hakkında ayrıntılı bilgi için bkz. Azure [izleyici ile Azure kaynaklarını izleme](../azure-monitor/essentials/monitor-azure-resource.md) .
