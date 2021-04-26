---
title: Azure Stream Analytics ilgili sorun giderme girdileri
description: Bu makalede Azure Stream Analytics işlerinde giriş bağlantılarınızın sorunlarını gidermeye yönelik teknikler açıklanmaktadır.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 05/01/2020
ms.custom: seodec18
ms.openlocfilehash: 2d7171c9ec1e60447fb3342caa72098fb2eb9337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019916"
---
# <a name="troubleshoot-input-connections"></a>Giriş bağlantısı sorunlarını giderme

Bu makalede Azure Stream Analytics giriş bağlantılarıyla ilgili yaygın sorunlar, giriş sorunlarının nasıl giderileceği ve sorunların nasıl giderileceği açıklanmaktadır. Birçok sorun giderme adımı Stream Analytics işiniz için kaynak günlüklerinin etkinleştirilmesini gerektirir. Kaynak günlüklerinizi etkinleştirmediyseniz, bkz. [Azure Stream Analytics sorun giderme kaynak günlüklerini kullanarak](stream-analytics-job-diagnostic-logs.md).

## <a name="input-events-not-received-by-job"></a>İş tarafından alınmayan giriş olayları 

1.  Giriş ve çıkış bağlantınızı test edin. Her giriş ve çıkış için **Bağlantıyı Sına** düğmesini kullanarak giriş ve çıkışlarla bağlantıyı doğrulayın.

2.  Giriş verilerinizi inceleyin.

    1. Her giriş için [**örnek veri**](./stream-analytics-test-query.md) düğmesini kullanın. Giriş örnek verilerini indirin.
        
    1. Şema ve [veri türlerini](/stream-analytics-query/data-types-azure-stream-analytics)anlamak için örnek verileri inceleyin.
    
    1. Olayların gönderildiğinden emin olmak için [Olay Hub 'ı ölçümlerini](../event-hubs/event-hubs-metrics-azure-monitor.md) denetleyin. Event Hubs ileti alıyorsanız, ileti ölçümleri sıfırdan büyük olmalıdır.

3.  Giriş önizlemede bir zaman aralığı seçtiğinizden emin olun. **Zaman aralığını Seç**' i seçin ve ardından sorgunuzu test etmeden önce bir örnek süre girin.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Yanlış biçimlendirilmiş giriş olayları seri durumundan çıkarma hatalarına neden oluyor 

Seri kaldırma sorunları, Stream Analytics işinizin giriş akışı hatalı biçimlendirilmiş iletiler içerdiğinde oluşur. Örneğin, hatalı biçimlendirilmiş bir ileti bir JSON nesnesinde eksik parantez veya küme ayracı ya da zaman alanında yanlış bir zaman damgası biçiminde olabilir. 
 
Bir Stream Analytics işi girişten hatalı biçimlendirilmiş bir ileti aldığında, iletiyi bırakır ve bir uyarı verir. Stream Analytics işinizin **giriş** kutucuğunda bir uyarı simgesi gösterilir. İş çalışır durumda olduğu sürece aşağıdaki uyarı simgesi mevcuttur:

![Azure Stream Analytics giriş kutucuğu](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Hatanın ayrıntılarını ve hataya neden olan iletiyi (yükü) görüntülemek için kaynak günlüklerini etkinleştirin. Seri durumdan çıkarma hatalarının gerçekleşebileceği birden çok neden vardır. Belirli seri kaldırma hatalarıyla ilgili daha fazla bilgi için bkz. [giriş verileri hataları](data-errors.md#input-data-errors). Kaynak günlükleri etkinleştirilmemişse, Azure portal kısa bir bildirim kullanıma sunulacaktır.

![Giriş ayrıntıları uyarı bildirimi](media/stream-analytics-malformed-events/warning-message-with-offset.png)

İleti yükünün 32 KB 'den büyük veya ikili biçimde olduğu durumlarda, [GitHub örnekleri deposunda](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)bulunan CheckMalformedEvents. cs kodunu çalıştırın. Bu kod, bölüm KIMLIĞINI, kaydırmayı okur ve bu uzaklığa ait olan verileri yazdırır. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>İş, en yüksek Olay Hub 'ı alıcılarını aşıyor

Event Hubs kullanmak için en iyi yöntem, iş ölçeklenebilirliği için birden çok tüketici grubu kullanmaktır. Belirli bir giriş için Stream Analytics işteki okuyucu sayısı, tek bir tüketici grubundaki okuyucu sayısını etkiler. Tam alıcı sayısı, genişleme topolojisi mantığının iç uygulama ayrıntılarına dayanır ve dışarıdan gösterilmez. Bir iş başlatıldığında veya iş yükseltmeleri sırasında okuyucu sayısı değişebilir.

Alıcı sayısı üst sınırı aştığında aşağıdaki hata iletileri gösterilir. Hata iletisi, bir tüketici grubu altında Olay Hub 'ına yapılan mevcut bağlantıların bir listesini içerir. Etiket, `AzureStreamAnalytics` bağlantıların Azure Streaming Service 'ten olduğunu gösterir.

```
The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.

The following information may be helpful in identifying the connected receivers: Exceeded the maximum number of allowed receivers per partition in a consumer group which is 5. List of connected receivers – 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1.
```

> [!NOTE]
> Bir iş yükseltmesi sırasında okuyucu sayısı değiştiğinde, denetim günlüklerine geçici uyarılar yazılır. Stream Analytics işleri bu geçici sorunlardan otomatik olarak kurtarılır.

### <a name="add-a-consumer-group-in-event-hubs"></a>Event Hubs bir tüketici grubu ekleyin

Event Hubs örneğinize yeni bir tüketici grubu eklemek için aşağıdaki adımları izleyin:

1. Azure portalında oturum açın.

2. Olay Hub 'ınızı bulun.

3. **Varlıklar** başlığı altında **Event Hubs** ' yi seçin.

4. Olay Hub 'ını ada göre seçin.

5. **Event Hubs örneği** sayfasında, **varlıklar** başlığı altında **tüketici grupları**' nı seçin. **$Default** adlı bir tüketici grubu listelenir.

6. Yeni bir tüketici grubu eklemek için **+ Tüketici grubu** ' nu seçin. 

   ![Event Hubs bir tüketici grubu ekleyin](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Olay Hub 'ını işaret etmek için Stream Analytics işinde girişi oluşturduğunuzda, burada tüketici grubunu belirttiniz. **$Default** , hiçbiri belirtilmediğinde kullanılır. Yeni bir tüketici grubu oluşturduktan sonra, Stream Analytics işinde Olay Hub 'ı girişini düzenleyin ve yeni tüketici grubunun adını belirtin.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Bölüm başına okuyucu Event Hubs sınırı aşıyor

Akış sorgusu sözdiziminiz aynı giriş olayı hub kaynağına birden çok kez başvuruyorsa, iş altyapısı aynı tüketici grubundan sorgu başına birden çok okuyucu kullanabilir. Aynı tüketici grubuna çok fazla başvuru olduğunda, iş, beş sınırını aşabilir ve bir hata oluşturdu. Bu durumlarda, aşağıdaki bölümde açıklanan çözümü kullanarak birden çok tüketici grubunda birden çok giriş kullanarak daha fazla giriş yapabilirsiniz. 

Bölüm başına okuyucu sayısının beş Event Hubs sınırını aşması gereken senaryolar şunlardır:

* Çoklu SELECT deyimleri: **aynı** Olay Hub 'ına başvuran bırden çok select deyimi kullanıyorsanız, her SELECT deyimi yeni bir alıcının oluşturulmasına neden olur.

* BIRLEŞIM: bir BIRLEŞIM kullandığınızda, **aynı** Olay Hub 'ına ve tüketici grubuna başvuran birden çok giriş olması mümkündür.

* Kendi kendine JOIN: bir kendi kendine JOIN işlemi kullandığınızda, **aynı** Olay Hub 'ına birden çok kez başvurmak mümkündür.

Aşağıdaki en iyi yöntemler, bölüm başına okuyucu sayısının beş Event Hubs sınırını aştığı senaryolara karşı azaltmaya yardımcı olabilir.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>WıTH yan tümcesini kullanarak sorgunuzu birden çok adıma bölme

WıTH yan tümcesi, sorgudaki FROM yan tümcesi tarafından başvurulabilen geçici bir adlandırılmış sonuç kümesini belirtir. WıTH yan tümcesini tek bir SELECT ifadesinin yürütme kapsamında tanımlarsınız.

Örneğin, bu sorgu yerine:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Bu sorguyu kullanın:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Girişlerin farklı tüketici gruplarına bağlı olduğundan emin olun

Üç veya daha fazla girişin aynı Event Hubs tüketici grubuna bağlandığı sorgular için, ayrı tüketici grupları oluşturun. Bu, ek Stream Analytics girişlerinin oluşturulmasını gerektirir.

### <a name="create-separate-inputs-with-different-consumer-groups"></a>Farklı tüketici gruplarıyla ayrı girişler oluşturun

Aynı Olay Hub 'ı için farklı tüketici gruplarıyla ayrı girişler oluşturabilirsiniz. Aşağıdaki BIRLEŞIM sorgusu, *ınputone* ve *ınputtwo* 'in aynı Olay Hub kaynağına başvurduğu bir örnektir. Herhangi bir sorgunun farklı tüketici gruplarıyla ayrı girdileri olabilir. BIRLEŞIM sorgusu yalnızca bir örnektir.

```sql
WITH 
DataOne AS 
(
SELECT * FROM InputOne 
),

DataTwo AS 
(
SELECT * FROM InputTwo 
),

SELECT foo FROM DataOne
UNION 
SELECT foo FROM DataTwo

```

## <a name="readers-per-partition-exceeds-iot-hub-limit"></a>Bölüm başına okuyucu IoT Hub sınırı aşıyor

Stream Analytics işler, IoT Hub olayları bağlamak ve okumak için IoT Hub yerleşik [Olay Hub 'ı ile uyumlu uç noktasını](../iot-hub/iot-hub-devguide-messages-read-builtin.md) kullanır. Bölüm başına okuma, IoT Hub sınırlarını aşarsa, bu sorunu çözmek için [Olay Hub 'ı çözümlerini](#readers-per-partition-exceeds-event-hubs-limit) kullanabilirsiniz. Yerleşik uç nokta için IoT Hub Portal uç noktası oturumunda veya [IoT Hub SDK](/rest/api/iothub/IotHubResource/CreateEventHubConsumerGroup)aracılığıyla bir tüketici grubu oluşturabilirsiniz.

## <a name="get-help"></a>Yardım alın

Daha fazla yardım için, [Azure Stream Analytics Için Microsoft Q&soru sayfasını](/answers/topics/azure-stream-analytics.html)deneyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](/rest/api/streamanalytics/)