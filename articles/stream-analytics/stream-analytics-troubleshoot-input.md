---
title: Azure Stream Analytics için sorun giderme girişler
description: Bu makalede, giriş bağlantılarınızı Azure Stream Analytics işlerinde sorun giderme teknikleri açıklar.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 8357a53ee065812922b5df53fbdef7c14e5f0ff7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621036"
---
# <a name="troubleshoot-input-connections"></a>Giriş bağlantı sorunlarını giderme

Bu sayfa, giriş bağlantıları ve bunları nasıl giderebileceğinizden ile ilgili yaygın sorunları açıklar.

## <a name="input-events-not-received-by-job"></a>İş tarafından alınmayan giriş olayları 
1.  Bağlantınızı test edin. Kullanarak giriş ve çıkış bağlantısını doğrulayın **Test Bağlantısı** her giriş ve çıkış düğmesi.

2.  Girişinizi inceleyin.

    Giriş verileri olay Hub'ına geçiyor doğrulamak için [hizmet veri yolu Gezgini](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) (olay hub'ı girişi kullanılıyorsa), Azure olay Hub'ına bağlanmak için.
        
    Kullanım [ **örnek verileri** ](stream-analytics-sample-data-input.md) düğme için her bir giriş ve örnek giriş verilerini indirin.
        
    Veri şekli anlamak için örnek verileri İnceleme: şema ve [veri türleri](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="malformed-input-events-causes-deserialization-errors"></a>Giriş yanlış biçimlendirilmiş olaylar neden serileştirme kaldırma hataları 
Stream Analytics işinizin Giriş akışı yanlış biçimlendirilmiş iletiler içerdiğinde seri durumundan çıkarma sorunları nedeniyle oluşur. Örneğin, hatalı bir ileti parantezin eksik olması veya bir JSON nesnesinde bir küme ayracı veya hatalı zaman damgası biçimi zaman alanı kaynaklanabilir. 
 
Bir Stream Analytics işi girdi hatalı bir ileti aldığında, iletiyi bırakır ve bir uyarı ile bildirir. Bir uyarı sembolü gösterilir **girişleri** Stream Analytics işinizin kutucuk. İşi çalışır durumda olduğu sürece, bu uyarı işareti vardır:

![Azure Stream Analytics girişler kutucuğunda](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Uyarı ayrıntılarını görüntülemek tanılama günlüklerini etkinleştirin. Giriş yanlış biçimlendirilmiş olaylar için yürütme günlüklerini şuna benzer bir ileti ile bir giriş içerir: 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>Seri durumundan çıkarma hatası neyin
Giriş olayları seri durumdan çıkarma hataya açık anlamak için ayrıntılı analiz etmek için aşağıdaki adımları uygulayabilirsiniz. Ardından, bu sorunu yeniden ulaşmaktan önlemek için doğru biçimde olaylar oluşturmak için olay kaynağı düzeltebilirsiniz.

1. Giriş kutucuğuna gidin ve sorunları listesini görmek için uyarı simgeleri tıklatın.

2. Giriş Ayrıntıları kutucuğu, her bir sorunun ayrıntılarını içeren uyarıların bir listesini görüntüler. Aşağıdaki örnek uyarı iletisi bölümü, uzaklığı ve seri numaraları içeren JSON verileri hatalı biçimlendirilmiş olduğu. 

   ![Uzaklığı olan Stream Analytics uyarı iletisi](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. Hatalı biçimdeki JSON verilerini bulmak için kullanılabilir CheckMalformedEvents.cs kod çalıştırma [GitHub örnekleri depomuzdan](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Bu kod okuma bölüm kimliği, uzaklığı ve bu uzaklık içinde bulunan veri yazdırır. 

4. Verileri okuduktan sonra, serileştirme biçimini analiz edebilir ve düzeltebilirsiniz.

5. Ayrıca [olayları bir Service Bus Explorer ile IOT Hub'ından okumak](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>En fazla olay hub'ı alıcıları iş aşıyor
Event Hubs'ı kullanmaya yönelik en iyi yöntem, işi ölçeklenebilirlik sağlamak için birden fazla tüketici grupları kullanmaktır. Belirli bir giriş için Stream Analytics işinde okuyucu sayısını tek bir tüketici grubundaki okuyucu sayısını etkiler. Alıcılar kesin sayısını iç uygulama ayrıntıları genişleme topolojisi mantığı temel alır ve harici olarak gösterilmez. Bir iş başlatıldığında veya iş yükseltmeleri sırasında okuyucu sayısını değiştirebilirsiniz.

Alıcı sayısı üst sınırı aştığında gösterilen hata oluşur: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Okuyucu sayısıyla proje yükseltme sırasında değiştiğinde, geçici uyarıları denetim günlükleri yazılır. Stream Analytics işleri, bu geçici sorunları otomatik olarak kurtarma.

### <a name="add-a-consumer-group-in-event-hubs"></a>Event hubs'ı tüketici grubu Ekle
Event Hubs Örneğinizde yeni bir tüketici grubu eklemek için aşağıdaki adımları izleyin:

1. Azure Portal’da oturum açın.

2. Event hubs'ı bulun.

3. Seçin **Event Hubs** altında **varlıkları** başlığı.

4. Ada göre olay hub'ı seçin.

5. Üzerinde **olay hub'ı örneği** sayfasındaki **varlıkları** başlığı seçin **tüketici grupları**. Ada sahip bir tüketici grubu **$Default** listelenir.

6. Seçin **+ tüketici grubu** yeni bir tüketici grubu eklemek için. 

   ![Event hubs'ı tüketici grubu Ekle](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Olay Hub'ına işaret edecek şekilde Stream Analytics işinde giriş oluşturduğunuzda, tüketici grubu belirtildi. Belirtilmediğinde $Default kullanılır. Yeni bir tüketici grubu oluşturduktan sonra Stream Analytics işi olay hub'ı giriş düzenleyin ve yeni tüketici grubu adını belirtin.


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Bölüm başına okuyucular, Event Hubs sınırı aşıyor

Akış sorgu sözdiziminizin birden çok kez aynı giriş olay hub'ı kaynağa başvuruyorsa, proje altyapısı sorgudan aynı tüketici grubu başına birden fazla okuyucuyu kapsayacak kullanabilirsiniz. Aynı tüketici grubu için çok fazla başvuru olduğunda, iş beş sınırını aşabilir ve bir hata oluşturulur. Bu durumlarda, aşağıdaki bölümde açıklanan çözümünü kullanarak birden fazla tüketici grupları arasında birden fazla giriş kullanarak daha da ayırabilirsiniz. 

Bölüm başına okuyucu sayısıyla beş Event Hubs sınırını aşıyor senaryolar aşağıdakileri içerir:

* Birden çok SELECT deyimine: Başvuran birden çok SELECT deyimine kullanırsanız **aynı** olay hub'ı giriş, her SELECT deyiminin oluşturulacak yeni bir alıcı neden olur.
* BİRLEŞİM: Bir birleşim kullandığınızda, başvuruda bulunan birden çok giriş olası **aynı** olay hub'ı ve tüketici grubu.
* KENDİ KENDİNE BİRLEŞME: Bir kendi KENDİNE JOIN işlemi kullandığınızda başvurmak olası **aynı** olay hub'ı birden çok kez.

Aşağıdaki en iyi, bölüm başına okuyucu sayısıyla beş Event Hubs sınırını aşıyor senaryoları azaltılmasına yardımcı olur.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>WITH yan tümcesini kullanarak sorgunuzu içine birden çok adımı Böl

WITH yan tümcesiyle sorgusunda FROM yan tümcesi tarafından başvurulan bir geçici adlandırılmış sonuç kümesi belirtir. WITH yan tümcesi tek bir SELECT deyimi yürütme kapsamında tanımlarsınız.

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

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Girişler için farklı bir tüketici grubu bağlama emin olun.

Aynı Event hubs'ı tüketici grubu için üç veya daha fazla giriş bağlı sorgular için ayrı bir tüketici grubu oluşturun. Bu ek Stream Analytics'e giriş oluşturulmasını gerektirir.

## <a name="get-help"></a>Yardım alın

Daha fazla yardım için deneyin bizim [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)
