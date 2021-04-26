---
title: Zaman serisi KIMLIĞI seçmek için en iyi uygulamalar-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Gen2 içinde zaman serisi KIMLIĞI seçerken en iyi yöntemler hakkında bilgi edinin.
author: shipramishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: seodec18
ms.openlocfilehash: 8aefa6efa5f10dae8a28e7126b91b7b5f6ac77a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950748"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Zaman Serisi Kimliği seçmek için en iyi uygulamalar

Bu makale, Azure Time Series Insights Gen2 ortamınız için zaman serisi KIMLIĞININ önemini ve bir tane seçmek için en iyi yöntemleri özetler.

## <a name="choose-a-time-series-id"></a>Zaman Serisi Kimliği

Uygun bir zaman serisi KIMLIĞINI seçmek kritik öneme sahiptir. Bir zaman serisi KIMLIĞI seçilmesi, bir veritabanı için bölüm anahtarı seçmeye benzer. Bir Azure Time Series Insights Gen2 ortamı oluşturduğunuzda bu gereklidir.

Zaman serisi KIMLIĞININ ayrıntılı bir açıklaması için ortam sağlama öğreticisini izleyin. İki farklı JSON telemetri yük örneği ve her biri için doğru zaman serisi KIMLIĞI seçimi görüntülenir.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]


> [!IMPORTANT]
> Zaman serisi kimlikleri şunlardır:
>
> * *Büyük/küçük harfe duyarlı* bir özellik: harf ve karakter casler, aramalarda, karşılaştırmalar, güncelleştirmelerde ve bölümleme sırasında kullanılır.
> * *Sabit* bir özellik: oluşturulduktan sonra değiştirilemez.

> [!TIP]
> Olay kaynağınız bir IoT Hub ise, zaman serisi KIMLIĞINIZ büyük olasılıkla ***ıothub-Connection-Device-ID*** olacaktır.

İzlenecek en iyi anahtar uygulamalar şunlardır:

* Birçok farklı değere sahip bir bölüm anahtarı seçin (örneğin, yüzlerce veya binlerce). Çoğu durumda bu, JSON 'unuzdaki cihaz KIMLIĞI, algılayıcı KIMLIĞI veya etiket KIMLIĞI olabilir.
* Zaman serisi KIMLIĞI, [zaman serisi modelinizin](./concepts-model-overview.md)yaprak düğüm düzeyinde benzersiz olmalıdır.
* Zaman serisi KIMLIĞININ Özellik adı dizesinin karakter sınırı 128 ' dir. Zaman serisi KIMLIĞININ Özellik değeri için, karakter sınırı 1.024 ' dir.
* Zaman serisi KIMLIĞI için benzersiz bir özellik değeri eksikse, null değer olarak değerlendirilir ve Benzersizlik kısıtlamasının aynı kuralına uyar.
* Zaman serisi KIMLIĞINIZ karmaşık bir JSON nesnesi içinde iç içe ise, özellik adınızı sağlarken giriş [düzleştirme kurallarını](./concepts-json-flattening-escaping-rules.md) izlediğinizden emin olun. Örnek [B](concepts-json-flattening-escaping-rules.md#example-b)'ye göz atın.
* Zaman serisi KIMLIĞINIZ olarak en fazla *üç* anahtar özelliği de seçebilirsiniz. Kombinasyonu, zaman serisi KIMLIĞINI temsil eden bir bileşik anahtar olacaktır.  
  > [!NOTE]
  > Üç anahtar özelliği dize olmalıdır.
  > Tek seferde bir özellik yerine bu bileşik anahtarla sorgu yapmanız gerekir.

## <a name="select-more-than-one-key-property"></a>Birden fazla anahtar özelliği seçin

Aşağıdaki senaryolar, zaman serisi KIMLIĞINIZ olarak birden fazla anahtar özelliği seçmeyi anlatmaktadır.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Örnek 1: benzersiz bir anahtarla zaman serisi KIMLIĞI

* Varlıkların eski varlıkları var. Her birinin benzersiz bir anahtarı vardır.
* Bir filo özelliği **DeviceID** tarafından benzersiz şekilde tanımlanır. Başka bir Fleet için, Unique özelliği **ObjectID**' dir. Ne filo, diğer filo 'in benzersiz özelliğini içermez. Bu örnekte, benzersiz anahtarlar olarak iki anahtar, **DeviceID** ve **ObjectID** seçersiniz.
* Null değerleri kabul ediyoruz ve bir özelliğin olay yükünde varlık olmaması, null değer olarak sayılır. Bu aynı zamanda, her olay kaynağındaki verilerin benzersiz bir zaman serisi KIMLIĞI olduğu iki olay kaynağına veri göndermeyi işlemenin uygun yoludur.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Örnek 2: bileşik bir anahtarla zaman serisi KIMLIĞI

* Birden çok özelliği, varlıkların aynı yakıt kapsamında benzersiz olmasını gerektirir.
* Her odada akıllı binalar ve sensörler dağıtımı sunuyoruz. Her odada, genellikle **Sensorıd** için aynı değerlere sahip olursunuz. Örnekler şunlardır **sensor1**, **Sensor2** ve **SENSOR3**.
* Binasında, **Flrrm** özelliğindeki sitelerde zemin ve oda numaraları çakışıyor. Bu sayıların **1a**, **2B** ve **3A** gibi değerleri vardır.
* **Redmond**, **Barselona** ve **Tokyo** gibi değerleri içeren bir özellik, **konum** vardır. Benzersizlik oluşturmak için aşağıdaki üç özelliği zaman serisi KIMLIK anahtarlarınız olarak belirlersiniz: **Sensorıd**, **flrrm** ve **Location**.

Örnek Ham olay:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Azure portal, ardından bileşik anahtarı aşağıdaki gibi girebilirsiniz:

[![Ortam için zaman serisi KIMLIĞINI yapılandırın.](media/v2-how-to-tsid/configure-environment-key.png)](media/v2-how-to-tsid/configure-environment-key.png#lightbox)

  > [!NOTE]
  > Azure portal, tek bir texbox içindeki özelliklerin virgülle ayrılmış adlarını girmeyin, aksi takdirde virgüller içeren tek bir özellik adı olarak kabul edilir.
  > Her özellik adını kendi texbox olarak girin.

## <a name="next-steps"></a>Sonraki adımlar

* Olayların nasıl depolanacağını anlamak için [JSON düzleştirme ve kaçış kurallarını](./concepts-json-flattening-escaping-rules.md) okuyun.

* [Azure Time Series Insights Gen2 ortamınızı](./how-to-plan-your-environment.md)planlayın.