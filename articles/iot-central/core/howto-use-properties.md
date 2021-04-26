---
title: Azure IoT Central çözümünde özellikleri kullanma
description: Azure IoT Central çözümünde salt okuma ve yazılabilir özellikleri nasıl kullanacağınızı öğrenin.
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 39bab52a564439d34b8702de11edabe7f0d6dfbc
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492265"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Azure IoT Central çözümünde özellikleri kullanma

Bu nasıl yapılır Kılavuzu, bir cihaz geliştiricisi olarak, Azure IoT Central uygulamanızdaki bir cihaz şablonunda tanımlanan cihaz özelliklerini nasıl kullanacağınızı gösterir.

Özellikler, zaman içinde nokta değerlerini temsil eder. Örneğin, bir cihaz, ulaşmaya çalıştığı hedef sıcaklığın raporlanabilmesi için bir özelliği kullanabilir. Varsayılan olarak, cihaz özellikleri IoT Central salt okunurdur. Yazılabilir özellikler, cihazınız ile Azure IoT Central uygulamanız arasında durum eşitlemesini sağlar.

Ayrıca, bulut özelliklerini bir Azure IoT Central uygulamasında da tanımlayabilirsiniz. Bulut özelliği değerleri hiçbir şekilde bir cihazla değiş tokuş edilir ve bu makale için kapsam dışındadır.

## <a name="define-your-properties"></a>Özelliklerinizi tanımlama

Özellikler, cihazınızın durumunu temsil eden veri alanlarıdır. Cihazın, cihazın açık/kapalı durumu gibi dayanıklı durumunu göstermek için özellikleri kullanın. Özellikler Ayrıca, cihazın yazılım sürümü gibi temel cihaz özelliklerini de temsil edebilir. Özellikleri salt okunurdur veya yazılabilir olarak bildirirsiniz.

Aşağıdaki ekran görüntüsünde bir Azure IoT Central uygulamasındaki özellik tanımı gösterilmektedir.

:::image type="content" source="media/howto-use-properties/property-definition.png" alt-text="Azure IoT Central uygulamasında bir özellik tanımı gösteren ekran görüntüsü.":::

Aşağıdaki tabloda bir özellik yeteneği için yapılandırma ayarları gösterilmektedir.

| Alan           | Açıklama                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Görünen ad    | Panolar ve formlarda kullanılan özellik değeri için görünen ad.                                                                                                                                                              |
| Name            | Özelliğin adı. Azure IoT Central, görünen adından Bu alan için bir değer oluşturur, ancak gerekirse kendi değerini seçebilirsiniz. Bu alan alfasayısal olmalıdır.  Cihaz kodu bu **ad** değerini kullanır.           |
| Yetenek türü | Özelliði.                                                                                                                                                                                                                          |
| Anlam türü   | Özelliğin sıcaklık, durum veya olay gibi anlam türü. Anlamsal tür seçimi aşağıdaki alanlardan hangisinin kullanılabildiğini belirler.                                                                       |
| Şema          | Double, String veya Vector gibi özellik veri türü. Kullanılabilir seçimler anlamsal tür tarafından belirlenir. Şema, olay ve durum anlam türleri için kullanılamaz.                                               |
| Yazılabilir       | Özellik yazılabilir değilse, cihaz özellik değerlerini Azure IoT Central rapor edebilir. Özellik yazılabilir ise, cihaz özellik değerlerini Azure IoT Central rapor edebilir. Ardından Azure IoT Central, cihaza özellik güncelleştirmeleri gönderebilir. |
| Önem derecesi        | Yalnızca olay anlam türü için kullanılabilir. Önem derecesi **hata**, **bilgi** veya **uyarılardır**.                                                                                                                         |
| Durum değerleri    | Yalnızca durum anlam türü için kullanılabilir. Her birinin görünen adı, adı, sabit listesi türü ve değeri olan olası durum değerlerini tanımlayın.                                                                                   |
| Birim            | **Mph**, **%** veya **&deg; C** gibi özellik değeri için bir birim.                                                                                                                                                              |
| Görüntüleme birimi    | Panolar ve formlarda kullanılacak bir görüntüleme birimi.                                                                                                                                                                                    |
| Yorum         | Özellik yeteneği hakkında herhangi bir açıklama.                                                                                                                                                                                        |
| Açıklama     | Özellik yeteneğinin açıklaması.                                                                                                                                                                                          |

Özellikler, burada gösterildiği gibi bir cihaz şablonundaki bir arabirim içinde de tanımlanabilir:

``` json
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "targetTemperature",
  "schema": "double",
  "displayName": "Target Temperature",
  "description": "Allows to remotely specify the desired target temperature.",
  "unit" : "degreeCelsius",
  "writable": true
},
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "unit" : "degreeCelsius",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

Bu örnekte iki özellik gösterilmektedir. Bu özellikler, Kullanıcı arabirimindeki özellik tanımıyla ilgilidir:

* `@type` özelliğin türünü belirtir: `Property` . Yukarıdaki örnekte `Temperature` her iki özellik için de anlamsal tür gösterilmektedir.
* `name` özelliği için.
* `schema` özelliğin veri türünü belirtir. Bu değer, Double, Integer, Boolean veya String gibi bir temel tür olabilir. Karmaşık nesne türleri ve eşlemeler de desteklenir.
* `writable` Varsayılan olarak, özellikler salt okunurdur. Bu alanı kullanarak, bir özelliği yazılabilir olarak işaretleyebilirsiniz.

Görünen ad ve açıklama gibi isteğe bağlı alanlar, arabirime ve yeteneklere daha fazla ayrıntı eklemenizi sağlar.

Bir özellik oluşturduğunuzda, **nesne** ve **sabit listesi** gibi karmaşık şema türlerini belirtebilirsiniz.

![Nasıl özellik ekleneceğini gösteren ekran görüntüsü.](./media/howto-use-properties/property.png)

**Nesne** gibi karmaşık **Şemayı** seçtiğinizde nesneyi de tanımlamanız gerekir.

:::image type="content" source="media/howto-use-properties/object.png" alt-text="Bir nesnenin nasıl tanımlanacağını gösteren ekran görüntüsü":::

Aşağıdaki kod bir nesne özelliği türünün tanımını gösterir. Bu nesnenin dize ve tamsayı türünde iki alanı vardır.

``` json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>Salt okunurdur özellikleri uygulama

Varsayılan olarak, özellikler salt okunurdur. Salt okuma özellikleri, bir cihaz raporu Özellik değerinin Azure IoT Central uygulamanızda güncellamasına olanak sağlar. Azure IoT Central uygulamanız salt okunurdur bir özelliğin değerini ayarlayamadı.

Azure IoT Central, cihaz ve Azure IoT Central uygulaması arasındaki özellik değerlerini senkronize etmek için cihaz ikizlerini kullanır. Cihaz özelliği değerleri cihaz ikizi bildirilen özelliklerini kullanır. Daha fazla bilgi için bkz. [cihaz TWINS](../../iot-hub/tutorial-device-twins.md).

Bir cihaz modelinden aşağıdaki kod parçacığında, salt okunurdur özellik türünün tanımı gösterilmektedir:

``` json
{
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

Özellik güncelleştirmeleri bir cihaz tarafından JSON yükü olarak gönderilir. Daha fazla bilgi için bkz. [Yük](./concepts-telemetry-properties-commands.md).

Azure IoT Central uygulamanıza bir özellik güncelleştirmesi göndermek için Azure IoT cihaz SDK 'sını kullanabilirsiniz.

Aşağıdaki işlev kullanılarak Device ikizi özellikleri Azure IoT Central uygulamanıza gönderilebilir:

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

Bu makale basitlik için Node.js kullanır. Diğer dil örnekleri için bkz. [Azure IoT Central uygulamanıza istemci uygulaması oluşturma ve bağlama](tutorial-connect-device.md) öğreticisine bakın.

Azure IoT Central uygulamasındaki aşağıdaki görünüm, görebileceğiniz özellikleri gösterir. Görünüm, **cihaz modeli** özelliğini otomatik olarak bir _salt okuma cihaz özelliği_ yapar.

:::image type="content" source="media/howto-use-properties/read-only.png" alt-text="Salt okunurdur özelliğinin görünümünü gösteren ekran görüntüsü":::

## <a name="implement-writable-properties"></a>Yazılabilir özellikleri Uygula

Yazılabilir özellikler, bir formdaki Azure IoT Central uygulamasındaki bir operatör tarafından ayarlanır. Azure IoT Central, özelliği cihaza gönderir. Azure IoT Central cihazdan bir bildirim bekliyor.

Bir cihaz modelinden aşağıdaki kod parçacığında yazılabilir özellik türünün tanımı gösterilmektedir:

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

Cihazınızın yanıt verdiği yazılabilir özellikleri tanımlamak ve işlemek için aşağıdaki kodu kullanabilirsiniz:

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

Yanıt iletisi `ac` ve `av` alanlarını içermelidir. `ad` alanı isteğe bağlıdır. Örnekler için aşağıdaki kod parçacıklarına bakın:

* `ac` , aşağıdaki tabloda yer alan değerleri kullanan sayısal bir alandır.
* `av` cihaza gönderilen sürüm numarasıdır.
* `ad` , bir seçenek dize açıklamasıdır.

| Değer | Etiketle | Açıklama |
| ----- | ----- | ----------- |
| `'ac': 200` | Tamamlandı | Özellik değiştirme işlemi başarıyla tamamlandı. |
| `'ac': 202` veya `'ac': 201` | Beklemede | Özellik değiştirme işlemi bekliyor veya devam ediyor. |
| `'ac': 4xx` | Hata | İstenen özellik değişikliği geçerli değil veya bir hata oluştu. |
| `'ac': 5xx` | Hata | Cihaz, istenen değişikliği işlerken beklenmeyen bir hatayla karşılaştı. |

Cihaz ikgörüti hakkında daha fazla bilgi için bkz. [cihazları arka uç hizmetinden yapılandırma](../../iot-hub/tutorial-device-twins.md).

Operatör, Azure IoT Central uygulamasında yazılabilir bir özellik ayarladığında, uygulama değeri cihaza göndermek için bir Device ikizi istenen özelliğini kullanır. Cihaz daha sonra bir Device ikizi bildirilen özelliği kullanarak yanıt verir. Azure IoT Central bildirilen özellik değerini aldığında, özellik görünümünü **kabul edildi** durumuyla güncelleştirir.

Aşağıdaki görünümde yazılabilir özellikler gösterilmektedir. Değeri girip **Kaydet**' i seçtiğinizde, Ilk durum **bekliyor**' dır. Cihaz değişikliği kabul ettiğinde, durum **kabul edilir** olarak değişir.

![Bekleyen durumu gösteren ekran görüntüsü.](./media/howto-use-properties/status-pending.png)

![Kabul edilen özelliği gösteren ekran görüntüsü.](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanızda özelliklerin nasıl kullanılacağını öğrendiğinize göre, bkz.:

* [Yükü](concepts-telemetry-properties-commands.md)
* [Bir istemci uygulamasını oluşturma ve Azure IoT Central uygulamanıza bağlama](tutorial-connect-device.md)