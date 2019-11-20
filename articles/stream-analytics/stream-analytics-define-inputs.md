---
title: Azure Stream Analytics giriş olarak veri akışı
description: Azure Stream Analytics ' de bir veri bağlantısı kurma hakkında bilgi edinin. Girişler, olaylardan gelen bir veri akışını ve ayrıca veri başvurusunu içerir.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: df111d605b7c05bcb934771b6063f2be04770ea9
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606472"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Stream Analytics giriş olarak veri akışı

Stream Analytics, Azure veri akışları ile birinci sınıf tümleştirmeyle üç türden kaynaktan giriş olarak sahiptir:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Depolama](https://azure.microsoft.com/services/storage/blobs/) 

Bu giriş kaynakları, Stream Analytics işiniz veya farklı bir abonelikle aynı Azure aboneliğinde bulunabilir.

### <a name="compression"></a>Sıkıştırma

Stream Analytics tüm veri akışı giriş kaynaklarında sıkıştırmayı destekler. Desteklenen sıkıştırma türleri şunlardır: None, GZip ve söndür sıkıştırma. Başvuru verileri için sıkıştırma desteği kullanılamaz. Giriş biçimi sıkıştırılmış veriler avro, saydam olarak işlenir. Avro serileştirme ile sıkıştırma türü belirtmeniz gerekmez. 

## <a name="create-edit-or-test-inputs"></a>Girişleri oluşturma, düzenleme veya test etme

[Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)ve [Visual Studio Code](quick-create-vs-code.md) kullanarak akış işinizdeki mevcut girişleri ekleyebilir ve bunları görüntüleyebilir veya düzenleyebilirsiniz. Ayrıca, Azure portal, [Visual Studio](stream-analytics-vs-tools-local-run.md)ve [Visual Studio Code](vscode-local-run.md)örnek verilerden giriş bağlantılarını ve [Test sorgularını](stream-analytics-manage-job.md#test-your-query) test edebilirsiniz. Bir sorgu yazdığınızda FROM yan tümcesindeki girişi listeleyin. Portalda **sorgu** sayfasından kullanılabilir girişlerin listesini alabilirsiniz. Birden çok giriş kullanmak istiyorsanız, bunları `JOIN` veya birden çok `SELECT` sorgu yazabilirsiniz.


## <a name="stream-data-from-event-hubs"></a>Event Hubs’dan veri akışı sağlama

Azure Event Hubs, yüksek düzeyde ölçeklenebilir yayımla-abone ol olay alımı sağlar. Bir olay hub 'ı saniyede milyonlarca olay toplayabilir, böylece bağlı cihazlarınız ve uygulamalarınız tarafından üretilen büyük miktarda veriyi işleyebilir ve analiz edebilirsiniz. Birlikte, Event Hubs ve Stream Analytics gerçek zamanlı analizler için uçtan uca bir çözüm sunar. Event Hubs, olayları gerçek zamanlı olarak Azure 'a aktarmanıza olanak tanır ve Stream Analytics işler bu olayları gerçek zamanlı olarak işleyebilir. Örneğin, Event Hubs için Web tıklamalarını, algılayıcı ayarlarını veya çevrimiçi günlük olaylarını gönderebilirsiniz. Daha sonra gerçek zamanlı filtreleme, toplama ve bağıntı için giriş veri akışları olarak Event Hubs kullanmak üzere Stream Analytics işleri oluşturabilirsiniz.

`EventEnqueuedUtcTime` bir olay hub 'ında bir olayın varış zamanıdır ve Event Hubs gelen olayların zaman damgasıdır Stream Analytics. Verileri olay yükünde zaman damgası kullanarak bir akış olarak işlemek için anahtar sözcük [Ile zaman damgasını](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) kullanmanız gerekir.

### <a name="event-hubs-consumer-groups"></a>Event Hubs tüketici grupları

Her bir Stream Analytics Olay Hub girişini kendi tüketici grubuna sahip olacak şekilde yapılandırmanız gerekir. Bir iş kendi kendine JOIN içerdiğinde veya birden fazla girişe sahip olduğunda, bazı girişler birden fazla okuyucu aşağı akış tarafından okunabilir. Bu durum, tek bir tüketici grubundaki okuyucu sayısını etkiler. Bölüm başına her bir tüketici grubu için beş okuyucu başına Event Hubs sınırının aşılmaması için, her bir Stream Analytics işi için bir tüketici grubu belirlemek en iyi uygulamadır. Ayrıca Standart katman Olay Hub 'ı için 20 Tüketici grubu sınırı vardır. Daha fazla bilgi için bkz. [Azure Stream Analytics girdileri sorun giderme](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Event Hubs bir giriş oluşturun

Aşağıdaki tabloda, bir olay hub 'ından veri girişi akışı için Azure portal **yeni giriş** sayfasında her bir özellik açıklanmaktadır:

| Özellik | Açıklama |
| --- | --- |
| **Giriş diğer adı** |Bu girişe başvurmak için iş sorgusunda kullandığınız kolay bir ad. |
| **Abonelik** | Olay Hub 'ı kaynağının bulunduğu aboneliği seçin. | 
| **Olay Hub 'ı ad alanı** | Olay Hub 'ı ad alanı, bir mesajlaşma varlıkları kümesi için bir kapsayıcıdır. Yeni bir olay hub 'ı oluşturduğunuzda ad alanını da oluşturursunuz. |
| **Olay Hub 'ı adı** | Giriş olarak kullanılacak Olay Hub 'ının adı. |
| **Olay Hub 'ı ilke adı** | Olay Hub 'ına erişim sağlayan paylaşılan erişim ilkesi. Her paylaşılan erişim ilkesinin adı, sizin ayarladığınız izinler ve anahtarlara erişim vardır. Bu seçenek, Olay Hub 'ı ayarlarını el ile sağlama seçeneğini seçmediğiniz takdirde otomatik olarak doldurulur.|
| **Olay Hub 'ı Tüketici grubu** (önerilir) | Her Stream Analytics işi için ayrı bir tüketici grubu kullanmanız önemle önerilir. Bu dize, Olay Hub 'ından veri almak için kullanılacak tüketici grubunu tanımlar. Hiçbir tüketici grubu belirtilmemişse, Stream Analytics işi $Default tüketici grubunu kullanır.  |
| **Olay serileştirme biçimi** | Gelen veri akışının serileştirme biçimi (JSON, CSV, Avro veya [diğer (prototip, XML, özel...)](custom-deserializer.md)).  JSON biçiminin belirtile hizalandığından ve ondalık sayılar için öndeki 0 içermediğinden emin olun. |
| **Kodlama** | UTF-8 şu anda desteklenen tek kodlama biçimidir. |
| **Olay sıkıştırma türü** | Hiçbiri (varsayılan), GZip veya söndür gibi gelen veri akışını okumak için kullanılan sıkıştırma türü. |

Verileriniz bir olay hub 'ı akış girişinden geliyorsa, Stream Analytics sorgunuzda aşağıdaki meta veri alanlarına erişebilirsiniz:

| Özellik | Açıklama |
| --- | --- |
| **EventProcessedUtcTime** |Stream Analytics tarafından etkinliğin işlendiği tarih ve saat. |
| **EventEnqueuedUtcTime** |Olayın Event Hubs tarafından alındığı tarih ve saat. |
| **PartitionID** |Giriş bağdaştırıcısının sıfır tabanlı bölüm KIMLIĞI. |

Örneğin, bu alanları kullanarak, aşağıdaki örneğe benzer bir sorgu yazabilirsiniz:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Olay Hub 'ını IoT Hub yollar için uç nokta olarak kullanırken, [Getmetadatapropertyvalue işlevini](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue)kullanarak IoT Hub meta verilerine erişebilirsiniz.
> 

## <a name="stream-data-from-iot-hub"></a>IoT Hub veri akışı

Azure IoT Hub, IoT senaryoları için iyileştirilmiş, yüksek düzeyde ölçeklenebilir bir yayınla-abone ol olay alma olayıdır.

Stream Analytics bir IoT Hub gelen olayların varsayılan zaman damgası, `EventEnqueuedUtcTime`olan IoT Hub olayın ulaştığı zaman damgasıdır. Verileri olay yükünde zaman damgası kullanarak bir akış olarak işlemek için anahtar sözcük [Ile zaman damgasını](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) kullanmanız gerekir.

### <a name="iot-hub-consumer-groups"></a>IoT Hub 'ı tüketici grupları

Her Stream Analytics IoT Hub girişini kendi tüketici grubuna sahip olacak şekilde yapılandırmanız gerekir. Bir iş kendi kendine JOIN içerdiğinde veya birden çok girişe sahip olduğunda, bazı girişler birden fazla okuyucu tarafından aşağı akış tarafından okunabilir. Bu durum, tek bir tüketici grubundaki okuyucu sayısını etkiler. Her bölüm için tüketici grubu başına beş okuyucu için Azure IoT Hub sınırının aşılmaması için, her bir Stream Analytics işi için bir tüketici grubu belirlemek en iyi uygulamadır.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>IoT Hub veri akışı girişi olarak yapılandırma

Aşağıdaki tabloda, bir IoT Hub akış girişi olarak yapılandırdığınızda Azure portal **yeni giriş** sayfasında her bir özellik açıklanmaktadır.

| Özellik | Açıklama |
| --- | --- |
| **Giriş diğer adı** | Bu girişe başvurmak için iş sorgusunda kullandığınız kolay bir ad.|
| **Abonelik** | IoT Hub kaynağının bulunduğu aboneliği seçin. | 
| **IoT Hub’ı** | Giriş olarak kullanılacak IoT Hub adı. |
| **Bkz** | IoT Hub uç noktası.|
| **Paylaşılan erişim ilkesi adı** | IoT Hub erişim sağlayan paylaşılan erişim ilkesi. Her paylaşılan erişim ilkesinin adı, sizin ayarladığınız izinler ve anahtarlara erişim vardır. |
| **Paylaşılan erişim ilkesi anahtarı** | IoT Hub erişimi yetkilendirmek için kullanılan paylaşılan erişim anahtarı.  IoT Hub ayarlarını el ile sağlama seçeneğini seçmediğiniz takdirde bu seçenek otomatik olarak doldurulur. |
| **Tüketici grubu** | Her Stream Analytics işi için farklı bir tüketici grubu kullanmanız önemle tavsiye edilir. Tüketici grubu IoT Hub verileri almak için kullanılır. Stream Analytics, aksi belirtilmedikçe $Default tüketicisi grubunu kullanır.  |
| **Olay serileştirme biçimi** | Gelen veri akışının serileştirme biçimi (JSON, CSV, Avro veya [diğer (prototip, XML, özel...)](custom-deserializer.md)).  JSON biçiminin belirtile hizalandığından ve ondalık sayılar için öndeki 0 içermediğinden emin olun. |
| **Kodlama** | UTF-8 şu anda desteklenen tek kodlama biçimidir. |
| **Olay sıkıştırma türü** | Hiçbiri (varsayılan), GZip veya söndür gibi gelen veri akışını okumak için kullanılan sıkıştırma türü. |


Akış verilerini bir IoT Hub kullandığınızda, Stream Analytics sorgunuzda aşağıdaki meta veri alanlarına erişebilirsiniz:

| Özellik | Açıklama |
| --- | --- |
| **EventProcessedUtcTime** | Olayın işlendiği tarih ve saat. |
| **EventEnqueuedUtcTime** | Olayın IoT Hub aldığı tarih ve saat. |
| **PartitionID** | Giriş bağdaştırıcısının sıfır tabanlı bölüm KIMLIĞI. |
| **Iothub. MessageId** | IoT Hub ' de iki yönlü iletişimi ilişkilendirmek için kullanılan bir KIMLIK. |
| **Iothub. CorrelationId** | IoT Hub İleti yanıtlarında ve geri bildirimde kullanılan bir KIMLIK. |
| **Iothub. Connectiondeviceıd** | Bu iletiyi göndermek için kullanılan kimlik doğrulaması KIMLIĞI. Bu değer, servicebağlı iletilerde IoT Hub tarafından damgalı olur. |
| **Iothub. Connectiondevicegenerationıd** | Bu iletiyi göndermek için kullanılan kimliği doğrulanmış cihazın oluşturma KIMLIĞI. Bu değer, servicebağlı iletilerde IoT Hub tarafından damgalı olur. |
| **Iothub. EnqueuedTime** | İleti IoT Hub tarafından alındığı zaman. |


## <a name="stream-data-from-blob-storage"></a>Blob depolamadan veri akışı
Azure Blob depolama, bulutta depolanacak büyük miktarda yapılandırılmamış veri ile senaryolar için uygun maliyetli ve ölçeklenebilir bir çözüm sunar. Blob depolamada bulunan veriler genellikle bekleyen veriler olarak değerlendirilir; Ancak, blob verileri Stream Analytics tarafından veri akışı olarak işlenebilir. 

Günlük işleme, Stream Analytics ile BLOB depolama girişlerini kullanmak için yaygın olarak kullanılan bir senaryodur. Bu senaryoda telemetri veri dosyaları bir sistemden yakalanmıştır ve anlamlı verilerin ayıklanmasının ayrıştırılması ve işlenmesi gerekir.

Stream Analytics blob Storage olaylarının varsayılan zaman damgası, blob 'un en son değiştirildiği zaman damgasıdır ve bu `BlobLastModifiedUtcTime`. Blob, 13:00 konumundaki bir depolama hesabına yüklenirse ve Azure Stream Analytics işi *Şu* anda 13:01 ' de seçeneği kullanılarak başlatılırsa, değiştirilen süre iş çalışma döneminin dışında kaldığında blob alınmaz.

Bir blob, 13:00 konumundaki bir depolama hesabı kapsayıcısına yüklenirse ve Azure Stream Analytics işi 13:00 veya daha önceki bir tarihte *özel süre* kullanılarak başlatılırsa, blob, değiştirilen süre iş çalışma döneminin içinde yer aldığından alınır.

Bir Azure Stream Analytics işi *Şu* anda 13:00 ' de kullanılarak başlatılırsa ve bir BLOB depolama hesabı kapsayıcısına 13:01 ' de yüklenirse, Azure Stream Analytics blobu seçer.

Verileri olay yükünde zaman damgası kullanarak bir akış olarak işlemek için anahtar sözcük [Ile zaman damgasını](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) kullanmanız gerekir. Blob dosyası kullanılabiliyorsa bir Stream Analytics işi her saniye Azure Blob depolama girişinden verileri çeker. Blob dosyası kullanılamıyorsa, 90 saniyelik en fazla gecikme süresine sahip bir üstel geri alma işlemi vardır.

CSV biçimli girişler, veri kümesi alanlarını tanımlamak için bir başlık satırı gerektirir ve tüm üstbilgi satırı alanları benzersiz olmalıdır.

> [!NOTE]
> Stream Analytics var olan bir blob dosyasına içerik eklenmesini desteklemez. Stream Analytics her dosyayı yalnızca bir kez görüntüler ve işin ardından dosyada gerçekleşen değişiklikler verileri okuduktan sonra işlenmez. En iyi yöntem bir blob dosyası için tüm verileri bir kerede karşıya yüklemek ve sonra farklı, yeni bir blob dosyasına daha yeni olaylar eklemektir.

Tek seferde çok fazla sayıda blob yüklemek Stream Analytics nadir durumlarda birkaç blob 'un okunmasını atlamasına neden olabilir. BLOB depolama alanına blob 'ları en az 2 saniye karşıya yüklemeniz önerilir. Bu seçenek uygun değilse, büyük hacimde olayları akışa almak için Event Hubs kullanabilirsiniz. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Blob depolamayı bir akış girişi olarak yapılandırma 

Aşağıdaki tabloda, blob depolamayı bir akış girişi olarak yapılandırdığınızda Azure portal **yeni giriş** sayfasında her bir özellik açıklanmaktadır.

| Özellik | Açıklama |
| --- | --- |
| **Giriş diğer adı** | Bu girişe başvurmak için iş sorgusunda kullandığınız kolay bir ad. |
| **Abonelik** | IoT Hub kaynağının bulunduğu aboneliği seçin. | 
| **Depolama hesabı** | Blob dosyalarının bulunduğu depolama hesabının adı. |
| **Depolama hesabı anahtarı** | Depolama hesabıyla ilişkili gizli anahtar. BLOB depolama ayarlarını el ile sağlama seçeneğini seçmediğiniz takdirde bu seçenek otomatik olarak doldurulur. |
| **Kapsayıcı** | Blob girişi için kapsayıcı. Kapsayıcılar Microsoft Azure Blob hizmetinde depolanan Bloblar için mantıksal bir gruplama sağlar. Azure Blob depolama hizmetine bir blob yüklediğinizde, o blob için bir kapsayıcı belirtmeniz gerekir. Yeni bir kapsayıcının oluşturulmasını sağlamak için **Mevcut kapsayıcıyı kullan** veya **Yeni oluştur** seçeneklerinden birini belirleyebilirsiniz.|
| **Yol kalıbı** (isteğe bağlı) | Belirtilen kapsayıcı içindeki Blobları bulmak için kullanılan dosya yolu. Kapsayıcının kökünden blob 'ları okumak istiyorsanız, bir yol kalıbı ayarlamayın. Yol içinde, aşağıdaki üç değişkenin bir veya daha fazla örneğini belirtebilirsiniz: `{date}`, `{time}`veya `{partition}`<br/><br/>Örnek 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Örnek 2: `cluster1/logs/{date}`<br/><br/>`*` karakteri yol ön eki için izin verilen bir değer değil. Yalnızca geçerli <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure Blob karakterlerine</a> izin verilir. Kapsayıcı adları veya dosya adları dahil değildir. |
| **Tarih biçimi** (isteğe bağlı) | Yol içinde tarih değişkenini kullanıyorsanız, dosyaların düzenlenme tarih biçimi. Örnek: `YYYY/MM/DD` |
| **Saat biçimi** (isteğe bağlı) |  Yoldaki zaman değişkenini, dosyaların düzenlenme zaman biçimini kullanırsanız. Şu anda desteklenen tek değer saat `HH`. |
| **Olay serileştirme biçimi** | Gelen veri akışının serileştirme biçimi (JSON, CSV, Avro veya [diğer (prototip, XML, özel...)](custom-deserializer.md)).  JSON biçiminin belirtile hizalandığından ve ondalık sayılar için öndeki 0 içermediğinden emin olun. |
| **Kodlama** | CSV ve JSON için, UTF-8 şu anda desteklenen tek kodlama biçimidir. |
| **Masıyla** | Hiçbiri (varsayılan), GZip veya söndür gibi gelen veri akışını okumak için kullanılan sıkıştırma türü. |

Verileriniz bir BLOB depolama kaynağından geliyorsa, Stream Analytics sorgunuzda aşağıdaki meta veri alanlarına erişebilirsiniz:

| Özellik | Açıklama |
| --- | --- |
| **BlobName** |Olayın geldiği giriş blobunun adı. |
| **EventProcessedUtcTime** |Stream Analytics tarafından etkinliğin işlendiği tarih ve saat. |
| **BlobLastModifiedUtcTime** |Blobun en son değiştirildiği tarih ve saat. |
| **PartitionID** |Giriş bağdaştırıcısının sıfır tabanlı bölüm KIMLIĞI. |

Örneğin, bu alanları kullanarak, aşağıdaki örneğe benzer bir sorgu yazabilirsiniz:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Hızlı başlangıç: Azure portal kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
