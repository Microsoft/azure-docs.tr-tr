---
title: Azure IoT Central telemetri, özellik ve komut yükleri | Microsoft Docs
description: Azure IoT Central cihaz şablonları, bir cihazın telemetri, Özellikler ve komutlarının uygulanması gerektiğini belirtmenize olanak tanır. Bir cihazın IoT Central ile değişimi için veri biçimini anlayın.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 995f4670b17d55fe04d5c30a834ea4be576a8348
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106489987"
---
# <a name="telemetry-property-and-command-payloads"></a>Telemetri, özellik ve komut yükleri

_Bu makale, cihaz geliştiricileri için geçerlidir._

Azure IoT Central 'deki bir cihaz şablonu şunları tanımlayan bir şema ' dir:

* Bir cihazın IoT Central gönderdiği telemetri.
* Bir cihazın IoT Central ile eşitlendiği Özellikler.
* Bir cihazda IoT Central çağıran komutlar.

Bu makalede, cihaz geliştiricileri için cihazların bir cihaz şablonunda tanımlanan telemetri, Özellikler ve komutlar için gönderdikleri ve alabileceği JSON yükleri açıklanmaktadır.

Makale, olası telemetri, özellik ve komut yükünün her türünü tanımlamaz, ancak örnekler tüm anahtar türlerini gösterir.

Her örnek, cihazın IoT Central uygulamayla nasıl etkileşime gireceğini göstermek için türü ve örnek JSON yüklerini tanımlayan cihaz modelinden bir kod parçacığı gösterir.

> [!NOTE]
> IoT Central geçerli bir JSON kabul eder, ancak yalnızca cihaz modelindeki bir tanıma eşleşiyorsa görselleştirmeler için kullanılabilir. Tanımıyla eşleşmeyen verileri dışarı aktarabilirsiniz, bkz. [Azure 'Da IoT verilerini hedeflere aktarma](howto-export-data.md).

Cihaz modelini tanımlayan JSON dosyası [Digital Ikizi tanım dili (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)kullanır.

Kullanımda olan bu yüklerin bazılarını gösteren örnek cihaz kodu için bkz. [Azure IoT Central uygulama öğreticisine istemci uygulaması oluşturma ve bağlama](tutorial-connect-device.md) öğreticisine bakın.

## <a name="view-raw-data"></a>Ham verileri görüntüleme

IoT Central, bir cihazın bir uygulamaya gönderdiği ham verileri görüntülemenize olanak sağlar. Bu görünüm, bir cihazdan gönderilen yükle ilgili sorunları gidermek için kullanışlıdır. Bir cihazın gönderdiği ham verileri görüntülemek için:

1. **Cihazlar** sayfasından cihaza gidin.

1. **Ham veri** sekmesini seçin:

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="Ham veri görünümü":::

    Bu görünümde, görüntülenecek sütunları seçebilir ve görüntülenecek zaman aralığını ayarlayabilirsiniz. **Modellenmemiş veriler** sütunu, cihazdaki herhangi bir özellik veya telemetri tanımından hiçbiriyle eşleşmeyen verileri gösterir.

## <a name="telemetry"></a>Telemetri

### <a name="telemetry-in-components"></a>Bileşenlerdeki telemetri

Telemetri bir bileşende tanımlanmışsa, `$.sub` cihaz modelinde tanımlandığı şekilde bileşenin adıyla adlı bir özel ileti özelliği ekleyin. Daha fazla bilgi edinmek için bkz. [öğretici: Azure IoT Central uygulamanıza istemci uygulaması oluşturma ve bağlama](tutorial-connect-device.md).

### <a name="primitive-types"></a>İlkel türler

Bu bölümde, bir cihazın IoT Central uygulamasına akışı yapılan temel telemetri türleri örnekleri gösterilmektedir.

Bir cihaz modelinden aşağıdaki kod parçacığında `boolean` telemetri türünün tanımı gösterilmektedir:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

Bir cihaz istemcisi, aşağıdaki örnekte gösterildiği gibi Telemetriyi JSON olarak göndermelidir:

```json
{ "BooleanTelemetry": true }
```

Bir cihaz modelinden aşağıdaki kod parçacığında `string` telemetri türünün tanımı gösterilmektedir:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

Bir cihaz istemcisi, aşağıdaki örnekte gösterildiği gibi Telemetriyi JSON olarak göndermelidir:

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

Bir cihaz modelinden aşağıdaki kod parçacığında `integer` telemetri türünün tanımı gösterilmektedir:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

Bir cihaz istemcisi, aşağıdaki örnekte gösterildiği gibi Telemetriyi JSON olarak göndermelidir:

```json
{ "IntegerTelemetry": 23 }
```

Bir cihaz modelinden aşağıdaki kod parçacığında `double` telemetri türünün tanımı gösterilmektedir:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

Bir cihaz istemcisi, aşağıdaki örnekte gösterildiği gibi Telemetriyi JSON olarak göndermelidir:

```json
{ "DoubleTelemetry": 56.78 }
```

Bir cihaz modelinden aşağıdaki kod parçacığında `dateTime` telemetri türünün tanımı gösterilmektedir:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

Bir cihaz istemcisi Telemetriyi aşağıdaki örnek `DateTime` türler ıso 8061 biçiminde olması gereken JSON olarak göndermelidir:

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

Bir cihaz modelinden aşağıdaki kod parçacığında `duration` telemetri türünün tanımı gösterilmektedir:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

Bir cihaz istemcisi, aşağıdaki örnek süreler ISO 8601 biçiminde olmalıdır:

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>Karmaşık türler

Bu bölümde, bir cihazın IoT Central uygulamasına akışını sağlayan karmaşık telemetri türleri örnekleri gösterilmektedir.

Bir cihaz modelinden aşağıdaki kod parçacığında `geopoint` telemetri türünün tanımı gösterilmektedir:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

> [!NOTE]
> **Geopoint** şema türü, [dijital TWINS tanım dili belirtiminin](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)bir parçası değil. IoT Central Şu anda **geopoint** şema türünü ve geriye doğru uyumluluk için **konum** anlam türünü desteklemektedir.

Bir cihaz istemcisi, aşağıdaki örneğe benzer şekilde Telemetriyi JSON olarak göndermelidir. IoT Central, değeri bir haritada PIN olarak görüntüler:

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Bir cihaz modelinden aşağıdaki kod parçacığında `Enum` telemetri türünün tanımı gösterilmektedir:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Bir cihaz istemcisi, aşağıdaki örneğe benzer şekilde Telemetriyi JSON olarak göndermelidir. Olası değerler,, ve `0` `1` `2` IoT Central olarak, ve olarak görüntülenir `Item1` `Item2` `Item3` :

```json
{ "EnumTelemetry": 1 }
```

Bir cihaz modelinden aşağıdaki kod parçacığında `Object` telemetri türünün tanımı gösterilmektedir. Bu nesne, ve türlerine sahip üç alana sahiptir `dateTime` `integer` `Enum` :

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

Bir cihaz istemcisi, aşağıdaki örneğe benzer şekilde Telemetriyi JSON olarak göndermelidir. `DateTime` türler ISO 8061 uyumlu olmalıdır. İçin olası değerler,, ve `Property3` `0` `1` IoT Central olarak, ve olarak görüntülenir `Item1` `Item2` `Item3` :

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

Bir cihaz modelinden aşağıdaki kod parçacığında `vector` telemetri türünün tanımı gösterilmektedir:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

Bir cihaz istemcisi, aşağıdaki örnekte gösterildiği gibi Telemetriyi JSON olarak göndermelidir:

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>Olay ve durum türleri

Bu bölümde, telemetri olaylarının örnekleri ve bir cihazın IoT Central uygulamasına gönderdiği durumlar gösterilmektedir.

Bir cihaz modelinden aşağıdaki kod parçacığında bir `integer` olay türü tanımı gösterilmektedir:

```json
{
  "@type": [
    "Telemetry",
    "Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

Bir cihaz istemcisi, olay verilerini aşağıdaki örnekteki gibi görünen JSON olarak göndermelidir:

```json
{ "IntegerEvent": 74 }
```

Bir cihaz modelinden aşağıdaki kod parçacığında bir `integer` durum türünün tanımı gösterilmektedir:

```json
{
  "@type": [
    "Telemetry",
    "State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

Bir cihaz istemcisi, durumu aşağıdaki örnekte gösterildiği gibi JSON olarak göndermelidir. Olası tamsayı durum değerleri,, veya ' dir `1` `2` `3` :

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>Özellikler

> [!NOTE]
> Özellikler için yük biçimleri 07/14/2020 tarihinde veya sonrasında oluşturulan uygulamalar için geçerlidir.

### <a name="properties-in-components"></a>Bileşenlerdeki Özellikler

Özellik bir bileşende tanımlanmışsa, özelliği bileşen adı ' nda sarın. Aşağıdaki örnek, `maxTempSinceLastReboot` bileşeninde ' i ayarlar `thermostat2` . İşaretleyici, `__t` Bu bileşenin şu şekilde olduğunu gösterir:

```json
{
  "thermostat2" : {  
    "__t" : "c",  
    "maxTempSinceLastReboot" : 38.7
    } 
}
```

Daha fazla bilgi edinmek için bkz. [öğretici: Azure IoT Central uygulamanıza istemci uygulaması oluşturma ve bağlama](tutorial-connect-device.md).

### <a name="primitive-types"></a>İlkel türler

Bu bölümde, bir cihazın IoT Central uygulamasına gönderdiği temel özellik türlerinin örnekleri gösterilmektedir.

Bir cihaz modelinden aşağıdaki kod parçacığında bir `boolean` özellik türünün tanımı gösterilmektedir:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean",
  "writable": false
}
```

Bir cihaz istemcisi, cihaz ikizi bildirilen bir özellik gibi aşağıdaki örnek gibi görünen bir JSON yükü göndermelidir:

```json
{ "BooleanProperty": false }
```

Bir cihaz modelinden aşağıdaki kod parçacığında bir `boolean` özellik türünün tanımı gösterilmektedir:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long",
  "writable": false
}
```

Bir cihaz istemcisi, cihaz ikizi bildirilen bir özellik gibi aşağıdaki örnek gibi görünen bir JSON yükü göndermelidir:

```json
{ "LongProperty": 439 }
```

Bir cihaz modelinden aşağıdaki kod parçacığında bir `date` özellik türünün tanımı gösterilmektedir:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date",
  "writable": false
}
```

Bir cihaz istemcisi, cihaz ikizi bildirilen bir özellik gibi aşağıdaki örnek gibi görünen bir JSON yükü göndermelidir. `Date` türler ISO 8061 uyumlu olmalıdır:

```json
{ "DateProperty": "2020-05-17" }
```

Bir cihaz modelinden aşağıdaki kod parçacığında bir `duration` özellik türünün tanımı gösterilmektedir:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration",
  "writable": false
}
```

Bir cihaz istemcisi, cihazda bildirilen bir özellik olarak aşağıdaki örnekte gösterildiği gibi bir JSON yükü göndermelidir ikizi-süreler ISO 8601 süresi uyumlu olmalıdır:

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

Bir cihaz modelinden aşağıdaki kod parçacığında bir `float` özellik türünün tanımı gösterilmektedir:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float",
  "writable": false
}
```

Bir cihaz istemcisi, cihaz ikizi bildirilen bir özellik gibi aşağıdaki örnek gibi görünen bir JSON yükü göndermelidir:

```json
{ "FloatProperty": 1.9 }
```

Bir cihaz modelinden aşağıdaki kod parçacığında bir `string` özellik türünün tanımı gösterilmektedir:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string",
  "writable": false
}
```

Bir cihaz istemcisi, cihaz ikizi bildirilen bir özellik gibi aşağıdaki örnek gibi görünen bir JSON yükü göndermelidir:

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>Karmaşık türler

Bu bölümde, bir cihazın IoT Central uygulamasına gönderdiği karmaşık özellik türleri örnekleri gösterilmektedir.

Bir cihaz modelinden aşağıdaki kod parçacığında bir `geopoint` özellik türünün tanımı gösterilmektedir:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint",
  "writable": false
}
```

> [!NOTE]
> **Geopoint** şema türü, [dijital TWINS tanım dili belirtiminin](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)bir parçası değil. IoT Central Şu anda **geopoint** şema türünü ve geriye doğru uyumluluk için **konum** anlam türünü desteklemektedir.

Bir cihaz istemcisi, cihaz ikizi bildirilen bir özellik gibi aşağıdaki örnek gibi görünen bir JSON yükü göndermelidir:

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Bir cihaz modelinden aşağıdaki kod parçacığında bir `Enum` özellik türünün tanımı gösterilmektedir:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "writable": false,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Bir cihaz istemcisi, cihaz ikizi bildirilen bir özellik gibi aşağıdaki örnek gibi görünen bir JSON yükü göndermelidir. Olası değerler,, ve `0` `1` IoT Central olarak, ve olarak görüntülenir `Item1` `Item2` `Item3` :

```json
{ "EnumProperty": 1 }
```

Bir cihaz modelinden aşağıdaki kod parçacığında bir `Object` özellik türünün tanımı gösterilmektedir. Bu nesne, türlerine sahip iki alana sahiptir `string` ve `integer` :

```json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "writable": false,
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

Bir cihaz istemcisi, cihaz ikizi bildirilen bir özellik gibi aşağıdaki örnek gibi görünen bir JSON yükü göndermelidir:

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

Bir cihaz modelinden aşağıdaki kod parçacığında bir `vector` özellik türünün tanımı gösterilmektedir:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector",
  "writable": false
}
```

Bir cihaz istemcisi, cihaz ikizi bildirilen bir özellik gibi aşağıdaki örnek gibi görünen bir JSON yükü göndermelidir:

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writable-property-types"></a>Yazılabilir Özellik türleri

Bu bölümde, bir cihazın IoT Central uygulamasından aldığı yazılabilir Özellik türlerinin örnekleri gösterilmektedir.

Yazılabilir özelliği bir bileşende tanımlanmışsa, istenen özellik iletisi bileşen adını içerir. Aşağıdaki örnek, cihazdaki öğesini güncelleştirmek için cihazı isteyen iletiyi gösterir `targetTemperature` `thermostat2` . İşaretleyici, `__t` Bu bileşenin şu şekilde olduğunu gösterir:

```json
{
  "thermostat2": {
    "targetTemperature": {
      "value": 57
    },
    "__t": "c"
  },
  "$version": 3
}
```

Daha fazla bilgi edinmek için bkz. [öğretici: Azure IoT Central uygulamanıza istemci uygulaması oluşturma ve bağlama](tutorial-connect-device.md).

IoT Central cihazdan yazılabilir Özellik güncelleştirmelerine yanıt bekliyor. Yanıt iletisi `ac` ve `av` alanlarını içermelidir. `ad` alanı isteğe bağlıdır. Örnekler için aşağıdaki kod parçacıklarına bakın.

`ac` , aşağıdaki tablodaki değerleri kullanan sayısal bir alandır:

| Değer | Etiketle | Açıklama |
| ----- | ----- | ----------- |
| `'ac': 200` | Tamamlandı | Özellik değiştirme işlemi başarıyla tamamlandı. |
| `'ac': 202`  veya `'ac': 201` | Beklemede | Özellik değiştirme işlemi bekliyor veya devam ediyor |
| `'ac': 4xx` | Hata | İstenen özellik değişikliği geçerli değil veya bir hata oluştu |
| `'ac': 5xx` | Hata | Cihaz, istenen değişikliği işlerken beklenmeyen bir hatayla karşılaştı. |

`av` cihaza gönderilen sürüm numarasıdır.

`ad` , bir seçenek dize açıklamasıdır.

Bir cihaz modelinden aşağıdaki kod parçacığında yazılabilir `string` özellik türünün tanımı gösterilmektedir:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

Cihaz IoT Central aşağıdaki yükü alır:

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

Cihaz, güncelleştirmeyi tamamladıktan sonra IoT Central aşağıdaki JSON yükünü göndermelidir. Bu ileti, IoT Central alınan özgün güncelleştirmenin sürüm numarasını içerir. IoT Central bu iletiyi aldığında, özelliği Kullanıcı arabiriminde **eşitlenmiş** olarak işaretler:

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

Bir cihaz modelinden aşağıdaki kod parçacığında yazılabilir `Enum` özellik türünün tanımı gösterilmektedir:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Cihaz IoT Central aşağıdaki yükü alır:

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

Cihaz, güncelleştirmeyi tamamladıktan sonra IoT Central aşağıdaki JSON yükünü göndermelidir. Bu ileti, IoT Central alınan özgün güncelleştirmenin sürüm numarasını içerir. IoT Central bu iletiyi aldığında, özelliği Kullanıcı arabiriminde **eşitlenmiş** olarak işaretler:

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>Komutlar

Komut bir bileşende tanımlanmışsa, cihazın aldığı komutun adı bileşen adını içerir. Örneğin, komut çağrılırsa `getMaxMinReport` ve bileşen çağrılırsa `thermostat2` , cihaz adlı bir komutu yürütmek için bir istek alır `thermostat2*getMaxMinReport` .

Bir cihaz modelinden aşağıdaki kod parçacığında, parametresi olmayan ve cihazın herhangi bir şeyi döndürmesini beklemediği bir komutun tanımı gösterilmektedir:

```json
{
  "@type": "Command",
  "displayName": {
    "en": "CommandBasic"
  },
  "name": "CommandBasic"
}
```

Cihaz, istekte boş bir yük alır ve `200` başarıyı göstermek için http yanıt kodu ile yanıtta boş bir yük döndürmelidir.

Bir cihaz modelinden aşağıdaki kod parçacığında, bir tamsayı parametresi olan ve cihazın bir tamsayı değeri döndürmesini bekleyen bir komutun tanımı gösterilmektedir:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "CommandSimple"
  },
  "name": "CommandSimple"
}
```

Cihaz, istek yükü olarak bir tamsayı değeri alır. Bu cihaz, `200` başarıyı göstermek için bir http yanıt kodu ile yanıt yükü olarak bir tamsayı değeri döndürmelidir.

Bir cihaz modelinden aşağıdaki kod parçacığında, bir nesne parametresi olan ve cihazın bir nesne döndürmesini bekleyen bir komutun tanımı gösterilmektedir. Bu örnekte, her iki nesnenin de Integer ve String alanları vardır:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
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
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
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
  },
  "displayName": {
    "en": "CommandComplex"
  },
  "name": "CommandComplex"
}
```

Aşağıdaki kod parçacığında cihaza gönderilen örnek bir istek yükü gösterilmektedir:

```json
{ "Field1": 56, "Field2": "A string value" }
```

Aşağıdaki kod parçacığında cihazdan gönderilen örnek bir yanıt yükü gösterilmektedir. `200`Başarıyı göstermek için bir http yanıt kodu kullanın:

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="long-running-commands"></a>Uzun süre çalışan komutlar

Bir cihaz modelinden aşağıdaki kod parçacığında bir komutun tanımı gösterilmektedir. Komutun bir tamsayı parametresi vardır ve cihazın bir tamsayı değeri döndürmesini bekler:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "LongRunningCommandSimple"
  },
  "name": "LongRunningCommandSimple"
}
```

Cihaz, istek yükü olarak bir tamsayı değeri alır. Cihazın bu komutu işlemesi için zaman ihtiyacı varsa, `202` cihazın işleme isteğini kabul ettiğini göstermek için bir http yanıt koduyla boş bir yanıt yükü döndürmelidir.

Cihaz isteği işlemeyi tamamladığında, aşağıdaki örnek gibi görünen IoT Central bir özellik göndermelidir. Özellik adı, komut adıyla aynı olmalıdır:

```json
{
  "LongRunningCommandSimple": {
    "value": 87
  }
}
```

### <a name="offline-commands"></a>Çevrimdışı komutlar

IoT Central Web Kullanıcı arabiriminde, bir komut için **çevrimdışı** seçeneğini belirleyebilirsiniz. Çevrimdışı komutlar, bir cihazın bağlanması halinde teslim edilen çözümünüzde cihaza tek yönlü bildirimlerdir. Çevrimdışı komutların istek parametreleri olabilir ancak yanıt döndürmez.

Cihaz şablonundan bir modeli veya arabirimi dışa aktardığınızda, **çevrimdışıyken sıra** ayarı dahil değildir. Bir komutun çevrimdışı bir komut olduğu, dışarıya aktarılmış bir modele veya Interface JSON 'a bakarak bilgi alamazsınız.

Çevrimdışı komutlar, cihaza komut ve yük göndermek için [buluttan cihaza iletileri IoT Hub](../../iot-hub/iot-hub-devguide-messages-c2d.md) kullanır.

Bir cihaz modelinden aşağıdaki kod parçacığında bir komutun tanımı gösterilmektedir. Komutun bir tarih saat alanı ve sabit listesi içeren bir nesne parametresi vardır:

```json
{
  "@type": "Command",
  "displayName": {
    "en": "Generate Diagnostics"
  },
  "name": "GenerateDiagnostics",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "Payload"
    },
    "name": "Payload",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "StartTime"
          },
          "name": "StartTime",
          "schema": "dateTime"
        },
        {
          "displayName": {
            "en": "Bank"
          },
          "name": "Bank",
          "schema": {
            "@type": "Enum",
            "displayName": {
              "en": "Enum"
            },
            "enumValues": [
              {
                "displayName": {
                  "en": "Bank 1"
                },
                "enumValue": 1,
                "name": "Bank1"
              },
              {
                "displayName": {
                  "en": "Bank2"
                },
                "enumValue": 2,
                "name": "Bank2"
              },
              {
                "displayName": {
                  "en": "Bank3"
                },
                "enumValue": 3,
                "name": "Bank3"
              }
            ],
            "valueSchema": "integer"
          }
        }
      ]
    }
  }
}
```

Önceki kod parçacığında komut için cihaz şablonu Kullanıcı arabiriminde bu **sırayı** etkinleştirirseniz, cihazın aldığı ileti aşağıdaki özellikleri içerir:

| Özellik adı | Örnek değer |
| ---------- | ----- |
| `custom_properties` | `{'method-name': 'GenerateDiagnostics'}` |
| `data` | `{"StartTime":"2021-01-05T08:00:00.000Z","Bank":2}` |

## <a name="next-steps"></a>Sonraki adımlar

Cihaz geliştiricisi olarak cihaz şablonları hakkında bilgi edindiğinize göre, cihazların IoT Central nasıl kaydedileceği ve cihaz bağlantılarını IoT Central nasıl güvenlik altına aldığı hakkında daha fazla bilgi edinmek için, önerilen bir sonraki adım [Azure IoT Central 'a](./concepts-get-connected.md) bağlanın.
