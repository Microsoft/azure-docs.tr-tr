---
title: Zaman serisi KIMLIĞI seçmek için en iyi uygulamalar-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights önizlemede zaman serisi KIMLIĞI seçerken en iyi yöntemler hakkında bilgi edinin.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: cf826c47c61e3714a05dd81fe3eea4e6ee0b03f4
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012504"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Zaman serisi kimliği seçmeye yönelik en iyi uygulamalar

Bu makale, Azure Time Series Insights önizleme ortamınız için zaman serisi KIMLIĞININ önemini ve bir tane seçmek için en iyi yöntemleri özetler.

## <a name="choose-a-time-series-id"></a>Zaman Serisi Kimliği

Zaman serisi kimliği seçerek bir veritabanı için bir bölüm anahtarı seçme gibi olur. Time Series Insights önizleme ortamı oluştururken seçilmesi gerekir. Bu, *sabit* bir özelliktir. Diğer bir deyişle, zaman serisi KIMLIĞIYLE bir Time Series Insights önizleme ortamı oluşturduktan sonra bu ortam için değiştiremezsiniz. 

> [!IMPORTANT]
> Zaman serisi KIMLIĞI büyük/küçük harfe duyarlıdır.

Uygun bir zaman serisi KIMLIĞINI seçmek kritik öneme sahiptir. İzleyebileceğiniz en iyi uygulamalardan bazıları şunlardır:

* Birçok farklı değere sahip bir bölüm anahtarı seçin (örneğin, yüzlerce veya binlerce). Çoğu durumda bu, JSON 'unuzdaki cihaz KIMLIĞI, algılayıcı KIMLIĞI veya etiket KIMLIĞI olabilir.
* Zaman serisi kimliği yaprak düğümü düzeyinde benzersiz olmalıdır, [zaman serisi modeli](./time-series-insights-update-tsm.md).
* Olay kaynağınız bir IoT Hub ise, zaman serisi KIMLIĞINIZ büyük olasılıkla *ıothub-Connection-Device-ID*olacaktır.
* Zaman serisi KIMLIĞININ Özellik adı dizesinin karakter sınırı 128 ' dir. Zaman serisi KIMLIĞININ Özellik değeri için, karakter sınırı 1.024 ' dir.
* Zaman serisi KIMLIĞI için benzersiz bir özellik değeri eksikse, null değer olarak değerlendirilir ve Benzersizlik kısıtlamasının aynı kuralına uyar.
* Zaman serisi KIMLIĞINIZ olarak en fazla *üç* anahtar özelliği de seçebilirsiniz. Kombinasyonu, zaman serisi KIMLIĞINI temsil eden bir bileşik anahtar olacaktır.  

  > [!NOTE]
  > Üç anahtar özelliği dize olmalıdır.
  > Tek seferde bir özellik yerine bu bileşik anahtarla sorgu yapmanız gerekir.

Aşağıdaki senaryolar, zaman serisi KIMLIĞINIZ olarak birden fazla anahtar özelliği seçmeyi anlatmaktadır.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Örnek 1: benzersiz bir anahtarla zaman serisi KIMLIĞI

* Varlıkların eski varlıkları var. Her birinin benzersiz bir anahtarı vardır.
* Bir filo özelliği **DeviceID**tarafından benzersiz şekilde tanımlanır. Başka bir Fleet için, Unique özelliği **ObjectID**' dir. Ne filo, diğer filo 'in benzersiz özelliğini içermez. Bu örnekte, benzersiz anahtarlar olarak iki anahtar, **DeviceID** ve **ObjectID**seçersiniz.
* Null değerleri kabul ediyoruz ve bir özelliğin olay yükünde varlık olmaması, null değer olarak sayılır. Bu aynı zamanda, her olay kaynağındaki verilerin benzersiz bir zaman serisi KIMLIĞI olduğu iki olay kaynağına veri göndermeyi işlemenin uygun yoludur.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Örnek 2: bileşik bir anahtarla zaman serisi KIMLIĞI

* Birden çok özellik varlıklar aynı filosundan içinde benzersiz olması gerekir. 
* Her odada akıllı binalar ve sensörler dağıtımı sunuyoruz. Her odada, genellikle **Sensorıd**için aynı değerlere sahip olursunuz. Örnekler şunlardır **sensor1**, **Sensor2**ve **SENSOR3**.
* Binasında, **Flrrm**özelliğindeki sitelerde zemin ve oda numaraları çakışıyor. Bu sayıların **1a**, **2B**ve **3A**gibi değerleri vardır.
* **Redmond**, **Barselona**ve **Tokyo**gibi değerleri içeren bir özellik, **konum**vardır. Benzersizlik oluşturmak için aşağıdaki üç özelliği zaman serisi KIMLIK anahtarlarınız olarak belirlersiniz: **Sensorıd**, **flrrm**ve **Location**.

Örnek Ham olay:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Azure portal, bu bileşik anahtarı şöyle girebilirsiniz: 

`[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]`

## <a name="next-steps"></a>Sonraki adımlar

* [Veri modelleme](./time-series-insights-update-tsm.md)hakkında daha fazla bilgi edinin.

* [Azure Time Series Insights Preview ortamınızı](./time-series-insights-update-plan.md)planlayın.