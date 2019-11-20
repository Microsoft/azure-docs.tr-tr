---
title: Azure Stream analytics'te anomali algılama
description: Bu makalede, Azure Stream Analytics ve Azure Machine Learning birlikte anomalileri algılamak için nasıl kullanılacağını açıklar.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: e2fd226f1c605821f0fd595832b2cbe26d994fb4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612330"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure Stream analytics'te anomali algılama

Kullanılabilir hem bulut hem de Azure IOT Edge, Azure Stream Analytics yerleşik makine öğrenimi tabanlı anomali algılama iki en yaygın olarak karşılaşılan anomalileri izlemek için kullanılan bir özellikleri sunar: geçici ve kalıcı. İle **AnomalyDetection_SpikeAndDip** ve **AnomalyDetection_ChangePoint** İşlevler, doğrudan Stream Analytics işinizi anomali algılama gerçekleştirebilirsiniz.

Makine öğrenimi modelleri birörnek örneklenen zaman serisi varsayılır. Zaman serisi Tekdüzen değilse, anomali algılama çağırmadan önce bir atlayan pencere ile bir toplama adım ekleyebilirsiniz.

Makine öğrenimi işlemleri mevsimsellik eğilimleri veya çok değişkenli bağıntılar şu anda desteklemez.

## <a name="model-behavior"></a>Model davranışı

Genellikle, daha fazla veri kayan pencere modelin doğruluğunu artırır. Belirtilen kayan pencere verileri, o zaman çerçevesi için değerler normal kendi aralığının bir parçası olarak kabul edilir. Model yalnızca geçerli olay anormal olup olmadığını denetlemek için kayan pencere üzerinde olay geçmişi dikkate alır. Kayan pencere taşınırken, eski değerleri modelin eğitimleri çıkarılan.

İşlevler, ne oldukları kadar gördünüz üzerinde dayalı belirli bir normal oluşturarak çalışır. Aykırı değerleri güven düzeyi içinde kurulu normal karşı karşılaştırma tarafından tanımlanır. Pencere boyutu bir anomali ortaya çıktığında, bunu tanımasına olacaktır, böylece normal davranış modeli eğitmek için gereken en düşük olayları bağlı olmalıdır.

Geçmiş olaylar daha yüksek bir sayı karşı Karşılaştırılacak gerektiğinden modelin yanıt süresi geçmiş boyutunu artırır. Olayları daha iyi performans için gereken sayıda yalnızca dahil etmek için önerilir.

Zaman serisi boşlukları belirli noktalarda olaylar sürede almıyor modelinin bir sonucu olabilir. Bu durum imputation mantığı kullanarak Stream Analytics tarafından işlenir. Aynı kayan pencere için bir süre yanı sıra, geçmiş boyutu, olayları gelmesi beklenen ortalaması hesaplamak için kullanılır.

Kullanılabilir bir anomali Oluşturucu [burada](https://aka.ms/asaanomalygenerator) farklı anomali desenlerine sahip veriler ile IOT hub'ı akış için kullanılabilir. Bu IOT Hub'ından okumak ve anomalileri algılamak için anomali algılama sahip bu işlevlerin bir ASA işini ayarlanabilir.

## <a name="spike-and-dip"></a>Depo ve DIP

Zaman serisi olay akışı geçici anomalileri ani artışlar ve düşüşler olarak bilinir. Ani artışlar ve düşüşler makine öğrenimi tabanlı işlecini kullanarak izlenebilir [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Depo ve DIP anomali örneği](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

İkinci bir depo ilki küçükse, aynı kayan pencere daha küçük depo için hesaplanan puanı yeterli güven düzeyi içinde ilk depo için puana göre belirlenen önemli değildir büyük olasılıkla. Modelin güvenilirlik düzeyi gibi anomalileri algılamak için azalan deneyebilirsiniz. Ancak, çok sayıda uyarı almak başlatırsanız, daha yüksek bir olasılık aralığı kullanabilirsiniz.

Aşağıdaki örnek sorgu 2 dakikalık kayan pencere saniyede bir olayda Tekdüzen giriş fiyatı, 120 olayların geçmişini ile varsayar. Son SELECT deyimi ayıklar ve puan ve anomali çıkaran durumu % 95 güven düzeyine sahip.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Noktasını Değiştir

Zaman serisi olay akışı kalıcı anomalileri değerlerinin dağıtımındaki değişiklikler olay, düzey değişiklikleri ve eğilimleri gibi akışında ' dir. Stream Analytics'te dayalı Machine Learning'i kullanma gibi anomalileri algılanan [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) işleci.

Kalıcı değişiklikler çok süreden daha uzun ani artışlar ve düşüşler en son ve yıkıcı olaylar olduğunu gösteriyor olabilir. Kalıcı değişiklikler çıplak gözle genellikle görünür değildir, ancak ile algılanabilir **AnomalyDetection_ChangePoint** işleci.

Aşağıdaki resimde, bir düzey değişikliği örneğidir:

![Düzeyi değiştirme anomali örneği](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

Aşağıdaki resimde eğilim değişiklik örneğidir:

![Eğilim değişiklik anomali örneği](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

Aşağıdaki örnek sorgu Giriş bir olayda 20 dakikalık kayan pencere Saniyedeki oranı Tekdüzen 1200 olayların bir geçmiş boyutu ile varsayar. Son SELECT deyimi ayıklar ve puan ve anomali çıkaran durumu % 80'e güven düzeyine sahip.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>Performans özellikleri

Performansı bu modellerin geçmişi boyutu, pencere süresi, olay yükü bağlıdır ve işlev düzeyi bölümleme olup kullanılır. Bu bölümde, bu yapılandırmalar ele alınmaktadır ve 1 K, 5 K ve saniyede 10 bin olay alımı ücretlerine sürdüren öğrenmek için örnekleri sağlar.

* **Geçmiş boyutu** -Bu modeller ile doğrusal olarak gerçekleştirmek **geçmiş boyutu**. Artık yeni bir olay puanlamak için modeller geçmiş boyutu, o kadar uzun yararlanın. Yeni olay geçmişi arabellekteki geçmiş olayların her biri ile modeli karşılaştırmak olmasıdır.
* **Pencere süresi** - **pencere süresi** geçmiş boyutu tarafından belirtilen sayıda olaylarını almak için ne kadar sürer yansıtmalıdır. Penceresinde, birçok olay, Azure Stream Analytics, eksik değerleri impute. Bu nedenle, CPU tüketimi geçmişi boyutunun bir işlevdir.
* **Olay yükü** - büyük **olay yükü**, daha fazla iş, modelleriyle, CPU tüketimini etkiler gerçekleştirilir. İş utandırıcı derecede paralel olarak daha fazla giriş bölüm kullanmak iş mantığı için anlamlı varsayılarak kolaylaştırarak genişletilebilir.
* **İşlev düzeyi bölümleme** - **işlev düzeyi bölümleme** kullanılarak yapılır ```PARTITION BY``` anomali algılama işlev çağrısı içinde. Durum birden çok modeli için aynı anda korunmasının gerektiği durumlar gibi bölümleme bu tür bir ek ekler. İşlev düzeyi bölümleme, cihaz düzeyinde bölümleme gibi senaryolarda kullanılır.

### <a name="relationship"></a>İlişki
Aşağıdaki şekilde, geçmiş boyutu, pencere süresi ve toplam olay yükü ilgilidir:

windowDuration (ms cinsinden) = 1000 * historySize / (toplam giriş olayları / sn / giriş bölüm sayısı)

İşlev tarafından DeviceID bölümlenirken "DeviceID tarafından anomali algılama işlevi çağrısı bölüm" ekleyin.

### <a name="observations"></a>Gözlemler
Aşağıdaki tabloda bölümlenmemiş bir örneği için tek bir düğüm (6 SU) için aktarım hızı gözlemleri içerir:

| Geçmiş boyutu (olaylar) | Pencere süresi (ms) | Toplam giriş olayı / sn |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6,000 | 10,910 | 1,100 |

Aşağıdaki tablo, bölümlenmiş çalışması için tek bir düğüm (6 SU) için aktarım hızı gözlemleri içerir:

| Geçmiş boyutu (olaylar) | Pencere süresi (ms) | Toplam giriş olayı / sn | Cihaz sayısı |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6,000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6,000 | 2,181,819 | <550 | 100 |

Bölümlenmemiş yapılandırmaları yukarıdaki çalıştırmak için örnek kod bulunan [ölçek, akış depo](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) Azure örnekleri. Bu kod, girdi ve çıktı olarak olay hub'ı kullanan hiçbir işlev düzeyi bölümleme ile bir stream analytics işi oluşturur. Giriş yük test istemcilerinin kullanılarak oluşturulur. Her giriş olayı, bir 1 KB json belgesidir. Olaylar (en fazla 1 K cihazlar için) JSON veri gönderen bir IOT cihazının simülasyonunu gerçekleştirme. Geçmiş boyutu, pencere süresi ve toplam olay yük 2 giriş bölüm farklılık gösterir.

> [!Note]
> Daha doğru bir tahmin için örnekleri kendi senaryonuza uyacak şekilde özelleştirin.

### <a name="identifying-bottlenecks"></a>Performans sorunlarını tanımlama
Ölçümleri bölmesinde Azure Stream Analytics işinizi işlem hattınızı performans sorunlarını tanımlamak için kullanın. Gözden geçirme **giriş/çıkış olaylarını** için aktarım hızı ve ["Eşik gecikmesi"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) veya **biriktirme listesine alınan olaylar** iş giriş oranı ile tutmaktan olmadığını görmek için. Olay hub'ı ölçümler için aranacak **istekler daraltıldı** ve eşik birimleri uygun şekilde ayarlayın. Cosmos DB ölçümleri için gözden geçirin **bölüm anahtar aralığı başına tüketilen RU/sn en fazla** , bölüm anahtar aralığı emin olmak için aktarım hızı altında aynı şekilde kullanılır. Azure SQL DB için izleme **günlük GÇ** ve **CPU**.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Azure Stream Analytics Machine learning ile anomali algılama

Aşağıdaki video Azure Stream Analytics machine learning işlevleri kullanarak gerçek zamanlı bir anomali Algılama işlemini gösterir. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)

