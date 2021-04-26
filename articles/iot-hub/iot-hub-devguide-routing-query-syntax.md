---
title: Azure IoT Hub ileti yönlendirmesinde sorgula | Microsoft Docs
description: Sizin için önemli olan verileri almak üzere iletilere zengin sorgular uygulamak için kullanabileceğiniz IoT Hub ileti yönlendirme sorgu dili hakkında bilgi edinin.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: c4ba48377d868404ff130ec458e50e2b42fae977
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790526"
---
# <a name="iot-hub-message-routing-query-syntax"></a>IoT Hub ileti yönlendirme sorgusu söz dizimi

İleti yönlendirme, kullanıcıların farklı veri türlerini, cihaz telemetri iletilerini, cihaz yaşam döngüsü olaylarını ve cihaz ikizi değişiklik olaylarını çeşitli uç noktalara yönlendirmesine olanak sağlar. Bu verilere, sizin için önemli olan verileri almak üzere yönlendirmeden önce zengin sorgular da uygulayabilirsiniz. Bu makalede IoT Hub ileti yönlendirme sorgu dili açıklanmakta ve bazı yaygın sorgu desenleri sunulmaktadır.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

İleti yönlendirme, ileti özellikleri ve ileti gövdesinde, Device ikizi etiketleri ve Device ikizi özellikleri ile sorgulama yapmanıza olanak sağlar. İleti gövdesi JSON değilse, ileti yönlendirme iletiyi yine de yönlendirebilir, ancak sorgular ileti gövdesine uygulanamıyor.  Sorgular Boole ifadesi olarak tanımlanır ve bu, tüm gelen verileri yönlendiren sorgunun başarılı olduğunu ve Boole false ' ın sorgu başarısız olduğunu ve veri yönlendirilmesini sağlar. İfade null veya tanımsız olarak değerlendirilirse, yanlış olarak değerlendirilir ve bir hata durumunda [kaynak günlükleri](monitor-iot-hub-reference.md#routes) günlüklerinde IoT Hub yollar oluşturulur. Yolun kaydedilmesi ve değerlendirilmesi için sorgu söz dizimi doğru olmalıdır.  

## <a name="message-routing-query-based-on-message-properties"></a>İleti özelliklerine dayanan ileti yönlendirme sorgusu 

IoT Hub, protokollerde birlikte çalışabilirlik için tüm cihazdan buluta mesajlaşma için [ortak bir biçim](iot-hub-devguide-messages-construct.md) tanımlar. IoT Hub ileti, iletinin aşağıdaki JSON gösterimini varsayar. Sistem özellikleri tüm kullanıcılar için eklenir ve iletinin içeriğini belirler. Kullanıcılar, iletiye seçmeli olarak uygulama özellikleri ekleyebilir. Cihazdan buluta mesajlaşma, büyük/küçük harfe duyarlı olmayan IoT Hub için benzersiz özellik adları kullanmanızı öneririz. Örneğin, aynı ada sahip birden fazla özellik varsa IoT Hub yalnızca özelliklerden birini gönderir.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>Sistem özellikleri

Sistem Özellikleri, iletilerin içeriğini ve kaynağını belirlemesine yardımcı olur. 

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| contentType | string | Kullanıcı iletinin içerik türünü belirtir. İleti gövdesinde sorguya izin vermek için bu değer Application/JSON olarak ayarlanmalıdır. |
| Contentenkodlamaya | string | Kullanıcı iletinin kodlama türünü belirtir. ContentType Application/JSON olarak ayarlandıysa, izin verilen değerler UTF-8, UTF-16, UTF-32 olur. |
| ıothub-bağlantı-cihaz kimliği | string | Bu değer IoT Hub olarak ayarlanır ve cihazın KIMLIĞINI tanımlar. Sorgulamak için kullanın `$connectionDeviceId` . |
| ıothub-enqueuedtime | string | Bu değer, IoT Hub tarafından ayarlanır ve UTC 'de iletiyi sıraya alma gerçek süresini temsil eder. Sorgulamak için kullanın `enqueuedTime` . |
| DT-DataSchema | string |  Bu değer, cihazdan buluta iletilerde IoT Hub tarafından ayarlanır. Cihaz bağlantısında ayarlanan cihaz modeli KIMLIĞINI içerir. Sorgulamak için kullanın `$dt-dataschema` . |
| DT-konu | string | Cihazdan buluta iletileri gönderen bileşenin adı. Sorgulamak için kullanın `$dt-subject` . |

[IoT Hub iletilerinde](iot-hub-devguide-messages-construct.md)açıklandığı gibi, bir iletide ek sistem özellikleri vardır. Önceki tablodaki özelliklerin yanı sıra **Connectiondeviceıd**, **connectionmoduleıd**' yi de sorgulayabilirsiniz.

### <a name="application-properties"></a>Uygulama özellikleri

Uygulama özellikleri, iletiye eklenebilen Kullanıcı tanımlı dizelerdir. Bu alanlar isteğe bağlıdır.  

### <a name="query-expressions"></a>Sorgu ifadeleri

İleti sistemi özelliklerindeki bir sorgunun, simgeye ön eki eklenmiş olması gerekir `$` . Uygulama özelliklerindeki sorgulara adlarıyla erişilir ve sembol önüne kullanılmamalıdır `$` . Bir uygulama özelliği adı ile başlıyorsa `$` , IoT Hub Sistem özelliklerinde arama yapılır ve bu, uygulama özelliklerine bakar. Örnek: 

Sistem özelliği Çekiştenkodlamaya göre sorgulamak için 

```sql
$contentEncoding = 'UTF-8'
```

Uygulama özelliği Işleme yolunda sorgulamak için:

```sql
processingPath = 'hot'
```

Bu sorguları birleştirmek için, Boolean ifadeleri ve işlevleri kullanabilirsiniz:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Desteklenen işleçlerin ve işlevlerin tam listesi [ifade ve koşullarda](iot-hub-devguide-query-language.md#expressions-and-conditions)gösterilmiştir.

## <a name="message-routing-query-based-on-message-body"></a>İleti gövdesine dayalı ileti yönlendirme sorgusu

İleti gövdesinde sorgulamayı etkinleştirmek için ileti, UTF-8, UTF-16 veya UTF-32 ' de bir JSON kodlamalı olmalıdır. , `contentType` `application/JSON` `contentEncoding` SISTEM özelliğinde desteklenen UTF kodlamalarının birine ve olarak ayarlanmalıdır. Bu özellikler belirtilmemişse, IoT Hub ileti gövdesinde sorgu ifadesini değerlendirmeyecektir. 

Aşağıdaki örnek, düzgün biçimlendirilmiş ve kodlanmış bir JSON gövdesi ile bir iletinin nasıl oluşturulacağını gösterir: 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

> [!NOTE] 
> Bu, gövde kodlamasının JavaScript 'te nasıl işleneceğini gösterir. C# dilinde bir örnek görmek isterseniz, [Azure IoT C# örneklerini](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)indirin. master.zip dosyasını sıkıştırmayı açın. Visual Studio Solution *SimulatedDevice*'ın program. cs dosyası, bir IoT Hub iletilerin nasıl kodlanacağını ve gönderyapılacağını gösterir. İleti yönlendirme [öğreticisinde](tutorial-routing.md)açıklandığı gibi, ileti yönlendirmeyi test etmek için kullanılan aynı örnektir. Program. cs ' nin en altında, kodlanmış dosyalardan birinde okuma, kodunu çözme ve bunu okuyabilmeniz için ASCII olarak yeniden yazma yöntemi vardır. 


### <a name="query-expressions"></a>Sorgu ifadeleri

İleti gövdesinde bir sorgunun ön eki olarak kullanılması gerekir `$body` . Sorgu ifadesinde gövde başvurusunu, gövde dizisi başvurusunu veya birden çok gövde başvurusunu kullanabilirsiniz. Sorgu ifadeniz Ayrıca ileti sistemi özellikleriyle bir gövde başvurusunu ve ileti uygulama özellikleri başvurusunu birleştirebilir. Örneğin, aşağıdakiler geçerli sorgu ifadeleridir: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

> [!NOTE] 
> Sorguları ve işlevleri yalnızca gövde başvurusu içindeki özelliklerde çalıştırabilirsiniz. Tüm gövde başvurusunda sorgu veya işlev çalıştıramazsınız. Örneğin, aşağıdaki *sorgu desteklenmez ve döndürülür* `undefined` :
> 
> ```sql
> $body[0] = 'Feb'
> ```

## <a name="message-routing-query-based-on-device-twin"></a>Cihaz ikizi tabanlı ileti yönlendirme sorgusu 

İleti yönlendirme, JSON nesneleri olan [cihaz ikizi](iot-hub-devguide-device-twins.md) etiketleri ve özellikleri üzerinde sorgulama yapmanızı sağlar. Modül ikizi üzerinde sorgulama de desteklenir. Bir cihaz Ikizi etiketleri ve özellikleri aşağıda gösterilmiştir.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>Sorgu ifadeleri

İleti ikizi üzerinde bir sorgunun ön eki olması gerekir `$twin` . Sorgu ifadeniz Ayrıca bir ikizi etiketi veya özellik başvurusunu bir gövde başvurusuyla, ileti sistemi özellikleriyle ve ileti uygulama özellikleri başvurusuyla birleştirebilir. Sorgu büyük/küçük harfe duyarlı olmadığından, Etiketler ve özelliklerde benzersiz adlar kullanmanızı öneririz. Bu hem cihaz ikis, hem de modül TWINS için geçerlidir. Ayrıca `twin` , `$twin` `body` `$body` bir özellik adları olarak,, veya ' ı kullanmaktan kaçının. Örneğin, aşağıdakiler geçerli sorgu ifadeleridir: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

Bir veya daha fazla özellik adı içindeki bir nokta ile gövde veya cihaz ikizi yönlendirme sorgusu desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

* [İleti yönlendirme](iot-hub-devguide-messages-d2c.md)hakkında bilgi edinin.
* [İleti yönlendirme öğreticisini](tutorial-routing.md)deneyin.
