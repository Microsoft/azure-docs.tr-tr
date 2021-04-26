---
title: Azure Stream Analytics ile gerçek zamanlı IoT veri akışlarını işleme
description: Akış analizi ve gerçek zamanlı veri işleme ile birlikte IoT algılayıcı etiketleri ve veri akışları
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/26/2019
ms.openlocfilehash: a438401ff93c20d8759e6128936c3626bd3de484
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012691"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Azure Stream Analytics ile gerçek zamanlı IoT veri akışlarını işleme

Bu makalede, Nesnelerin İnterneti (IoT) cihazlarından veri toplamak üzere akış işleme mantığı oluşturmayı öğreneceksiniz. Çözümünüzü hızlı ve ekonomik bir şekilde nasıl oluşturacağınızı göstermek için gerçek dünyada Nesnelerin İnterneti (IoT) kullanım örneği kullanırsınız.

## <a name="prerequisites"></a>Önkoşullar

* Ücretsiz bir [Azure aboneliği](https://azure.microsoft.com/pricing/free-trial/)oluşturun.
* [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)'dan örnek sorgu ve veri dosyalarını indirin.

## <a name="scenario"></a>Senaryo

Endüstriyel otomasyon alanında faaliyet gösteren Contoso şirketi, üretim süreçlerini tamamen otomatik hale getirmiştir. Bu fabrikada bulunan makineler, gerçek zamanlı olarak veri akışları yayabilen algılayıcılara sahiptir. Bu senaryoda bir üretim katı yöneticisi, algılayıcı verilerinden gerçek zamanlı bilgiler alarak belirli kalıpları aramak ve bunlara yönelik işlemler yapmak istiyor. Gelen veri akışından ilgi çekici desenler bulmak için algılayıcı verileri üzerinde Stream Analytics sorgu dili (SAQL) kullanabilirsiniz.

Bu örnekte, veriler bir Texas Instruments algılayıcı etiketi aygıtından oluşturulmuştur. Verilerin yükü JSON biçimindedir ve aşağıdaki gibi görünür:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

Gerçek hayattaki bir senaryoda, olayları bir akış şeklinde oluşturan bunun gibi yüzlerce algılayıcınız olabilir. Bir ağ geçidi cihazının bu olayları [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)'a veya [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)'larına göndermek için kod çalıştırması idealdir. Akış Analizi işiniz, bu olayları Event Hubs'dan alır ve akışlara yönelik gerçek zamanlı analiz sorguları çalıştırır. Ardından, sonuçları [desteklenen çıktılardan](stream-analytics-define-outputs.md)birine gönderebilirsiniz.

Kullanım kolaylığı için, bu başlangıç kılavuzunda gerçek algılayıcı etiket cihazlarından yakalanan bir örnek veri dosyası sunulmaktadır. Örnek veriler üzerinde sorgu çalıştırabilir ve sonuçları görebilirsiniz. Daha sonraki öğreticilerde, işinizi girişlere ve çıkışlara nasıl bağlayacağınızı ve bunları Azure hizmetine nasıl dağıtacağınızı öğreneceksiniz.

## <a name="create-a-stream-analytics-job"></a>Akış Analizi işi oluşturma

1. [Azure Portal](https://portal.azure.com)sol gezinti menüsünden **+ kaynak oluştur** ' u seçin. Ardından analizden **Stream Analytics iş** öğesini **seçin.**
   
    ![Yeni bir Akış Analizi işi oluşturma](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Benzersiz bir iş adı girin ve aboneliğin işiniz için doğru olduğundan emin olun. Yeni bir kaynak grubu oluşturun veya aboneliğinizden mevcut bir kaynağı seçin.

1. İşiniz için bir konum seçin. İşlem hızını artırmak ve maliyetlerin düşürülmesi için kaynak grubunuz ve tüm kaynaklar için aynı konumu kullanın. Yapılandırma yaptıktan sonra **Oluştur**' u seçin.
   
    ![Yeni bir Akış Analizi işi oluşturma ayrıntıları](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Azure Akış Analizi sorgusu oluşturma
İşiniz oluşturulduktan sonraki adım bir sorgu yazmaktır. Bir giriş veya çıktıyı işinize bağlamadan örnek verilere karşı sorgular test edebilirsiniz.

[HelloWorldASA-InputStream.js](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) GitHub 'dan indirin. Sonra, Azure portal Azure Stream Analytics işinize gidin.

Sol menüden **iş topolojisi** altında **sorgu** ' yı seçin. Ardından **örnek girişi yükle**' yi seçin. Dosyayı karşıya yükleyin `HelloWorldASA-InputStream.json` ve **Tamam**' ı seçin.

![Pano sorgu kutucuğunu Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Verilerin önizlemesinin, **giriş önizleme** tablosunda otomatik olarak doldurulduğuna dikkat edin.

![Örnek giriş verilerinin önizlemesi](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Sorgu: Ham verilerinizi arşivleme

En basit sorgu biçimi, tüm giriş verilerini belirlenmiş çıkışlarına arşivleyen bir doğrudan sorgudur. Bu sorgu, yeni bir Azure Stream Analytics işinde doldurulan varsayılan sorgudur.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

**Test sorgusu** ' nu seçin ve sonuçları **test sonuçları** tablosunda görüntüleyin.

![Stream Analytics sorgusunun test sonuçları](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Sorgu: Verileri bir koşula göre filtreleme

Bir koşula göre sonuçları filtrelemeye deneyelim. Yalnızca "sensorA" dan gelen olaylar için sonuçları göstermek istiyoruz.

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Sorguyu düzenleyiciye yapıştırın ve sonuçları gözden geçirmek için **Test sorgusu** ' nu seçin.

![Bir veri akışını filtreleme](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Sorgu: İş akışı tetiklemesi için uyarı

Şimdi sorgumuzu daha ayrıntılı hale getirelim. Her algılayıcı türü için ortalama sıcaklığı 30 saniyelik aralıklarda izlemek ve yalnızca ortalama sıcaklık 100 derecenin üzerinde olduğu zaman sonuçları görüntülemek istiyoruz.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![30 saniyelik filtre sorgusu](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Ortalama geçicinin 100 ' den büyük olduğu yalnızca 245 satır ve algılayıcı adı içeren sonuçları görmeniz gerekir. Bu sorguda olay akışı, 30 saniyelik bir **Atlayan Pencere** üzerinden algılayıcı adı olan **dspl**'ye göre gruplanır. Zamana bağlı sorgular, saatin nasıl ilerlemesini istediğinizi belirtmelidir. **Zaman DAMGASı by** yan tümcesini kullanarak, saatleri tüm zamana bağlı hesaplamalarla Ilişkilendirmek Için **outputtime** sütununu belirttiniz. Ayrıntılı bilgi için [zaman yönetimi](/stream-analytics-query/time-management-azure-stream-analytics) ve [Pencereleme işlevleri](/stream-analytics-query/windowing-azure-stream-analytics)hakkında bilgi edinin.

### <a name="query-detect-absence-of-events"></a>Sorgu: Var olmayan olayları algılama

Eksik giriş olaylarını bulmak için nasıl sorgu yazabiliriz? Bir sensörin verileri gönderdiği son zamanı bulalım ve sonraki 5 saniye boyunca olay göndermediği.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Var olmayan olayları algılama](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Burada, **LEFT OUTER** deyimini aynı veri akışı üzerinde kullanırız (kendi kendine birleşme). Bir **INNER** birleşimde, yalnızca bir eşleşme bulunduğu zaman bir sonuç döndürülür.  Bir **LEFT OUTER** birleşimde, birleştirmenin sol tarafındaki bir olay eşleşmemişse sağ taraftaki tüm sütunlar için NULL değerine sahip bir satır döndürülür. Bu teknik, var olmayan olayların bulunması için oldukça kullanışlıdır. Daha fazla bilgi için bkz. [JOIN](/stream-analytics-query/join-azure-stream-analytics).

## <a name="conclusion"></a>Sonuç

Bu makalenin amacı, farklı Stream Analytics sorgu dili sorgularının nasıl yazılacağını ve sonuçları tarayıcıda görmektir. Ancak, bu yalnızca başlamanıza olanak sağlamak için kullanılır. Akış Analizi birçok giriş ve çıkışı desteklemenin yanı sıra Azure Machine Learning'deki işlevlerden de faydalanır. Bu da onu veri akışlarının analizi için sağlam bir araç haline getirir. Sorgu yazma hakkında daha fazla bilgi için [ortak sorgu desenleri](stream-analytics-stream-analytics-query-patterns.md) hakkındaki makaleyi okuyun.