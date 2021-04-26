---
title: IoT Tak ve Kullan cihaz modellerini anlama | Microsoft Docs
description: IoT Tak ve Kullan cihazları için dijital TWINS tanım dili (DTDL) modelleme dilini anlayın. Makalesinde basit ve karmaşık veri türleri açıklanmakta, bileşenleri ve devralmayı kullanan desenler ve anlamsal türler açıklanır. Makale, model yazma için cihaz ikizi model tanımlayıcısı ve araç desteği seçeneklerine kılavuzluk sağlar.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 85888370106f34c723be4e47738114f7df33dcf4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057482"
---
# <a name="iot-plug-and-play-modeling-guide"></a>IoT Tak ve Kullan modelleme Kılavuzu

IoT Tak ve Kullan 'ın temel tarafında, IoT Tak ve Kullan özellikli bir uygulama için bir cihazın yeteneklerini açıklayan bir cihaz _modelidir_ . Bu model, şunları tanımlayan bir arabirimler kümesi olarak yapılandırılmıştır:

- Bir cihazın veya diğer varlıkların salt okunurdur veya yazılabilir durumunu temsil eden _özelliklerdir_ . Örneğin, bir cihaz seri numarası salt okunurdur ve bir termostat üzerinde hedef sıcaklık yazılabilir bir özellik olabilir.
- Bir cihaz tarafından yayılan verileri tanımlayan _telemetri_ alanları, verilerin düzenli algılayıcı okumalar, zaman zaman hatası veya bilgi iletisi olup olmadığı.
- Bir cihazda yapılabilen bir işlevi veya işlemi tanımlayan _Komutlar_ . Örneğin, bir komut bir ağ geçidini yeniden başlatır veya uzak bir kamerayı kullanarak bir resim alabilir.

IoT Tak ve Kullan cihaz modellerini kullanma hakkında daha fazla bilgi edinmek için bkz. [ıot Tak ve kullan cihaz Geliştirici Kılavuzu](concepts-developer-guide-device.md) ve [IoT Tak ve Kullan hizmeti Geliştirici Kılavuzu](concepts-developer-guide-service.md).

Bir model tanımlamak için dijital TWINS tanım dilini (DTDL) kullanırsınız. DTDL, [JSON-ld](https://json-ld.org/)ADLı bir JSON değişkeni kullanır. Aşağıdaki kod parçacığında, bir termostat cihazının modeli gösterilmektedir:

- Benzersiz bir model KIMLIĞIYLE sahiptir: `dtmi:com:example:Thermostat;1` .
- Sıcaklık telemetrisini gönderir.
- Hedef sıcaklığını ayarlamak için yazılabilir bir özelliğe sahiptir.
- Son yeniden başlatmadan bu yana en fazla sıcaklığın raporlanmasından salt okunurdur.
- Bir zaman dilimi boyunca maksimum, en düşük ve ortalama sıcaklık isteyen bir komuta yanıt verir.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

Termostat modelinin tek bir arabirimi vardır. Bu makaledeki daha sonraki örneklerde, bileşenleri ve devralmayı kullanan daha karmaşık modeller gösterilmektedir.

Bu makalede kendi modellerinizi tasarlamak ve yazmak ve veri türleri, model yapısı ve araçlar gibi konuları ele almak açıklanmaktadır.

Daha fazla bilgi için bkz. [Digital TWINS tanım dili v2](https://github.com/Azure/opendigitaltwins-dtdl) belirtimi.

## <a name="model-structure"></a>Model yapısı

Özellikler, telemetri ve komutlar arabirimler halinde gruplandırılır. Bu bölümde, bileşenleri ve devralmayı kullanarak basit ve karmaşık modelleri açıklayan arabirimlerin nasıl kullanılacağı açıklanmaktadır.

### <a name="model-ids"></a>Model kimlikleri

Her arabirimin benzersiz bir Digital ikizi model tanımlayıcısı (DTMı) vardır. Karmaşık modeller, bileşenleri tanımlamak için Dtmıs kullanır. Uygulamalar, bir depodaki model tanımlarını bulmak için cihazların gönderdikleri Dtmıs ' u kullanabilir.

Dtmıs, [ıot Tak ve kullan model deposu](https://github.com/Azure/iot-plugandplay-models)için gereken adlandırma kuralını izlemelidir:

- DTMı ön eki `dtmi:` .
- DTMı soneki, gibi model için sürüm numarasıdır `;2` .
- DTMı gövdesi, modelin depolandığı model deposundaki klasör ve dosya ile eşlenir. Sürüm numarası dosya adının bir parçasıdır.

Örneğin, DTMı tarafından tanımlanan model `dtmi:com:Example:Thermostat;2` dosyada *dtmı/com/example/thermostat-2.js* depolanır.

Aşağıdaki kod parçacığında, benzersiz DTMı ile bir arabirim tanımının ana hattı gösterilmektedir:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>Bileşen yok

Daha önce gösterilen termostat gibi basit bir model, gömülü veya basamaklı bileşenleri kullanmaz. Telemetri, Özellikler ve komutlar `contents` arabirimin düğümünde tanımlanmıştır.

Aşağıdaki örnek, bileşenleri kullanmayan basit bir modelin bir parçasını gösterir:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Azure IoT Explorer ve IoT Central cihaz şablonu Tasarımcısı gibi araçlar, _varsayılan bir bileşen_ olarak termostat gibi tek başına bir arabirimi etiketleyebilir.

Aşağıdaki ekran görüntüsünde, modelin Azure IoT gezgin aracında nasıl görüntülendiği gösterilmektedir:

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Azure IoT Gezgini 'nde varsayılan bileşen":::

Aşağıdaki ekran görüntüsünde, modelin IoT Central cihaz şablonu tasarımcısında varsayılan bileşen olarak nasıl görüntüleyeceği gösterilmektedir. Modelin DTMı 'sini görmek için **kimliği görüntüle** ' yi seçin:

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="IoT Central tasarımcısında termostat modelini gösteren ekran görüntüsü":::

Model KIMLIĞI, aşağıdaki ekran görüntüsünde gösterildiği gibi bir Device ikizi özelliğinde depolanır:

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="Digital ikizi özelliğindeki model KIMLIĞI":::

Bileşenleri olmayan bir DTDL modeli, tek bir telemetri, özellik ve komut kümesine sahip bir cihaz veya IoT Edge modülü için kullanışlı bir basitleştirmesi olur. Bileşenleri kullanmayan bir model, var olan bir cihazı veya modülü IoT Tak ve Kullan cihazı veya modülü olarak geçirmeyi kolaylaştırır. herhangi bir bileşen tanımlamaya gerek kalmadan gerçek cihazınızı veya modülünüzü açıklayan bir DTDL modeli oluşturursunuz.

> [!TIP]
> Modül bir cihaz [modülü](../iot-hub/iot-hub-devguide-module-twins.md) veya [IoT Edge modülü](../iot-edge/about-iot-edge.md)olabilir.

### <a name="reuse"></a>Yeniden kullanma

Arabirim tanımlarını yeniden kullanmanın iki yolu vardır. Diğer arabirim tanımlarına başvurmak için bir modelde birden çok bileşen kullanın. Mevcut arabirim tanımlarını genişletmek için devralmayı kullanın.

### <a name="multiple-components"></a>Birden çok bileşen

Bileşenler, model arabirimini diğer arabirimlerin derlemesi olarak oluşturmanıza imkan tanır.

Örneğin, [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) arabirimi bir model olarak tanımlanır. [Isı denetleyicisi modelini](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)tanımlarken bu arabirimi bir veya daha fazla bileşen olarak ekleyebilirsiniz. Aşağıdaki örnekte, bu bileşenler ve olarak adlandırılır `thermostat1` `thermostat2` .

Birden çok bileşeni olan bir DTDL modeli için, iki veya daha fazla bileşen bölümü vardır. Her bölüm `@type` `Component` , aşağıdaki kod parçacığında gösterildiği gibi bir şemaya ayarlanmış ve açıkça başvurur:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Bu modelde, içerik bölümünde iki bileşen ve bir bileşen tanımlanmış üç bileşen vardır `Thermostat` `DeviceInformation` . İçerik bölümü özelliği, telemetri ve komut tanımlarını da içerir.

Aşağıdaki ekran görüntüleri, bu modelin IoT Central nasıl göründüğünü gösterir. Sıcaklık denetleyicisindeki özellik, telemetri ve komut tanımları en üst düzey **varsayılan bileşende** görüntülenir. Her bir termostat için özellik, telemetri ve komut tanımları bileşen tanımlarında görünür:

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="IoT Central ' deki sıcaklık denetleyicisi cihaz şablonunu gösteren ekran görüntüsü.":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="IoT Central ' deki sıcaklık denetleyicisi cihaz şablonundaki termostat bileşenlerini gösteren ekran görüntüsü.":::

Bileşenlerle etkileşim kuran cihaz kodunun nasıl yazılacağını öğrenmek için bkz. [ıot Tak ve kullan cihaz Geliştirici Kılavuzu](concepts-developer-guide-device.md).

Bir cihazdaki bileşenlerle etkileşen hizmet kodu yazma hakkında bilgi edinmek için bkz. [ıot Tak ve Kullan hizmeti Geliştirici Kılavuzu](concepts-developer-guide-service.md).

### <a name="inheritance"></a>Devralma

Devralma, bir arabirimin yeteneklerini genişletmek için temel arabirimlerde özellikleri yeniden kullanmanıza olanak sağlar. Örneğin, birkaç cihaz modeli seri numarası gibi yaygın özellikleri paylaşabilir:

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="Bir cihaz modelinde, bir temel arabirimden yetenekleri paylaşan bir akış denetleyicisi gösteren devralma örneği." border="false":::

Aşağıdaki kod parçacığında, `extends` önceki diyagramda gösterilen devralma ilişkisini tanımlamak için anahtar sözcüğünü kullanan BIR DTML modeli gösterilmektedir:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

Aşağıdaki ekran görüntüsünde bu model IoT Central cihaz şablonu ortamında gösterilmektedir:

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="IoT Central içinde arabirim devralmayı gösteren ekran görüntüsü":::

Cihaz veya hizmet tarafı kodu yazdığınızda, kodunuzun devralınan arabirimleri işlemek için özel bir şey yapması gerekmez. Bu bölümde gösterilen örnekte, cihaz kodunuz, seri numarasını termostat arabiriminin bir parçası gibi raporlar.

### <a name="tips"></a>İpuçları

Bir model oluşturduğunuzda bileşenleri ve devralmayı birleştirebilirsiniz. Aşağıdaki diyagramda `thermostat` bir arabirimden devralan bir model gösterilmektedir `baseDevice` . `baseDevice`Arabirimin bir bileşeni vardır ve kendisi başka bir arabirimden devralır:

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="Hem bileşen hem de devralma kullanan bir modeli gösteren diyagram." border="false":::

Aşağıdaki kod parçacığında, `extends` `component` önceki diyagramda gösterilen devralma ilişkisini ve bileşen kullanımını tanımlamak için ve anahtar sözcüklerini kullanan bir DTML modeli gösterilmektedir:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>Veri türleri

Telemetri, özellik ve komut parametrelerini tanımlamak için veri türlerini kullanın. Veri türleri basit veya karmaşık olabilir. Karmaşık veri türleri basit veya diğer karmaşık türleri kullanır. Karmaşık türlerin derinlik üst sınırı beş düzeydir.

### <a name="primitive-types"></a>İlkel türler

Aşağıdaki tabloda kullanabileceğiniz temel türler kümesi gösterilmektedir:

| İlkel tür | Description |
| --- | --- |
| `boolean` | Bir boole değeri |
| `date` | [RFC 3339 ' nin 5,6 bölümünde](https://tools.ietf.org/html/rfc3339#section-5.6) tanımlanan tam tarih |
| `dateTime` | [RFC 3339](https://tools.ietf.org/html/rfc3339) ' de tanımlanan tarih-saat |
| `double` | IEEE 8 baytlık kayan nokta |
| `duration` | ISO 8601 biçiminde bir süre |
| `float` | Bir IEEE 4 baytlık kayan nokta |
| `integer` | İşaretli 4 baytlık tamsayı |
| `long` | İşaretli 8 baytlık tamsayı |
| `string` | Bir UTF8 dizesi |
| `time` | [RFC 3339 ' de 5,6 bölümünde](https://tools.ietf.org/html/rfc3339#section-5.6) tanımlanan tam süre |

Aşağıdaki kod parçacığında, alanında türü kullanan örnek bir telemetri tanımı gösterilmektedir `double` `schema` :

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>Karmaşık veri türleri

Karmaşık veri türleri *dizi*, *numaralandırma*, *eşleme*, *nesne* veya jeo-uzamsal türlerden biridir.

#### <a name="arrays"></a>Diziler

Dizi, tüm öğelerin aynı türde olduğu dizine eklenebilir bir veri türüdür. Öğe türü, basit veya karmaşık bir tür olabilir.

Aşağıdaki kod parçacığında, alanında türü kullanan örnek bir telemetri tanımı gösterilmektedir `Array` `schema` . Dizinin öğeleri Boole değerleri:

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>Listelemeler

Sabit listesi, değerlerle eşlenen adlandırılmış Etiketler kümesini içeren bir türü açıklar. Değerler tamsayı veya dizeler olabilir, ancak Etiketler her zaman dizelerdir.

Aşağıdaki kod parçacığında, alanında türü kullanan örnek bir telemetri tanımı gösterilmektedir `Enum` `schema` . Numaralandırmadaki değerler tamsayılardır:

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Haritalar

Eşleme, değerlerinin hepsi aynı türde olan anahtar-değer çiftleri içeren bir türdür. Eşlemedeki anahtar bir dize olmalıdır. Bir haritadaki değerler, başka bir karmaşık tür da dahil olmak üzere herhangi bir tür olabilir.

Aşağıdaki kod parçacığında, alanında türü kullanan örnek bir özellik tanımı gösterilmektedir `Map` `schema` . Haritadaki değerler dizelerdir:

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>Nesneler

Bir nesne türü, adlandırılmış alanlardan oluşur. Bir nesne eşlemesindeki alanların türleri basit veya karmaşık türler olabilir.

Aşağıdaki kod parçacığında, alanında türü kullanan örnek bir telemetri tanımı gösterilmektedir `Object` `schema` . Nesnesindeki alanlar `dateTime` , `duration` ve `string` türleridir:

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>Jeo-uzamsal türler

Dtdl, coğrafi veri yapılarını modellemeye yönelik [geojson](https://geojson.org/)tabanlı bir jeo-uzamsal türler kümesi sağlar: `point` , `multiPoint` , `lineString` , `multiLineString` , `polygon` ve `multiPolygon` . Bu türler, diziler, nesneler ve Numaralandırmaların önceden tanımlanmış iç yapılarıdır.

Aşağıdaki kod parçacığında, alanında türü kullanan örnek bir telemetri tanımı gösterilmektedir `point` `schema` :

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

Jeo-uzamsal türler dizi tabanlı olduğundan, bunlar şu anda Özellik tanımlarında kullanılamaz.

## <a name="semantic-types"></a>Anlamsal türler

Bir özelliğin veya telemetri tanımının veri türü, bir cihazın bir hizmetle birlikte alışverişi için kullandığı verilerin biçimini belirtir. Anlamsal tür, bir uygulamanın bir değerin nasıl işlenmesi veya görüntüleneceği tespit etmek için kullanabileceği telemetri ve özellikler hakkında bilgiler sağlar. Her semantik türün bir veya daha fazla ilişkili birimi vardır. Örneğin, santigrat ve Fahrenhayt, sıcaklık anlam türü için birimdir. IoT Central panolar ve analiz, telemetri veya özellik değerlerinin ve görüntüleme birimlerinin nasıl çizalınacağını anlamak için anlamsal tür bilgilerini kullanabilir. Anlam türlerini okumak için model ayrıştırıcısının nasıl kullanılabileceğini öğrenmek için bkz. [dijital TWINS modeli ayrıştırıcısını anlama](concepts-model-parser.md).

Aşağıdaki kod parçacığında, semantik tür bilgilerini içeren örnek bir telemetri tanımı gösterilmektedir. Anlamsal tür `Temperature` `@type` diziye eklenir ve `unit` değeri `degreeCelsius` anlam türü için geçerli birimlerden biridir:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>Yerelleştirme

IoT Central gibi uygulamalar, IoT Tak ve Kullan cihazıyla değiştirilen verilerin etrafında dinamik olarak bir kullanıcı arabirimi oluşturmak için modeldeki bilgileri kullanır. Örneğin, bir panodaki kutucuklar telemetri, özellik ve komutların adlarını ve açıklamalarını görüntüleyebilir.

Modeldeki isteğe bağlı `description` ve `displayName` alanlar, BIR kullanıcı arabiriminde kullanılması amaçlanan dizeleri tutar. Bu alanlar, bir uygulamanın yerelleştirilmiş bir kullanıcı arabirimini işlemek için kullanabileceği yerelleştirilmiş dizeleri tutabilir.

Aşağıdaki kod parçacığında, yerelleştirilmiş dizeleri içeren örnek bir sıcaklık telemetri tanımı gösterilmektedir:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

Yerelleştirilmiş dizeler eklemek isteğe bağlıdır. Aşağıdaki örnekte yalnızca bir tek, varsayılan dil vardır:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>Yaşam döngüsü ve araçlar

Bir cihaz modeli için dört yaşam döngüsü aşaması yazma, yayınlama, kullanım ve sürüm oluşturma aşamalardır:

### <a name="author"></a>Yazma

DTML cihaz modelleri, bir metin düzenleyicisinde oluşturabileceğiniz JSON belgelerdir. Ancak IoT Central, bir DTML modeli oluşturmak için cihaz şablonu GUI ortamını kullanabilirsiniz. IoT Central, şunları yapabilirsiniz:

- Özellikleri, telemetri ve komutları tanımlayan arabirimler oluşturun.
- Birden çok arabirimi birleştirmek için bileşenleri kullanın.
- Arabirimler arasındaki devralma ilişkilerini tanımlayın.
- DTML model dosyalarını içeri ve dışarı aktarın.

Daha fazla bilgi edinmek için bkz. [Azure IoT Central uygulamanızda yeni bir IoT cihaz türü tanımlama](../iot-central/core/howto-set-up-template.md).

[Visual Studio Code Için Dtdl Düzenleyicisi](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) , sözdizimi doğrulaması ve model yazma deneyimi üzerinde daha ayrıntılı denetim için otomatik tamamlama içeren metin tabanlı bir düzenleme ortamı sağlar.

### <a name="publish"></a>Yayımlama

DTML modellerinizi paylaşılabilir ve bulunabilir hale getirmek için bunları bir cihaz modelleri deposunda yayımlayabilirsiniz.

Ortak depoda bir modeli yayımlamadan önce, `dmr-client` modelinizi doğrulamak için araçları kullanabilirsiniz.

Daha fazla bilgi için bkz. [cihaz modelleri deposu](concepts-model-repository.md).

### <a name="use"></a>Kullanın

IoT Central gibi uygulamalar cihaz modellerini kullanır. IoT Central, bir model cihazın yeteneklerini açıklayan cihaz şablonunun bir parçasıdır. IoT Central, cihaz için panolar ve çözümlemeler dahil olmak üzere dinamik olarak bir kullanıcı arabirimi oluşturmak üzere cihaz şablonunu kullanır.

Özel bir çözüm, modeli uygulayan bir cihazın yeteneklerini anlamak için [dijital TWINS modeli ayrıştırıcısında](concepts-model-parser.md) kullanılabilir. Daha fazla bilgi edinmek için bkz. [IoT çözümünde ıot Tak ve kullan modellerini kullanma](concepts-model-discovery.md).

### <a name="version"></a>Sürüm

Modelleri kullanan cihazların ve sunucu tarafı çözümlerin çalışmaya devam etmesini sağlamak için, yayımlanan modeller sabittir.

DTMı, bir modelin birden çok sürümünü oluşturmak için kullanabileceğiniz bir sürüm numarası içerir. Cihazlar ve sunucu tarafı çözümleri, kullanmak üzere tasarlandıkları belirli sürümü kullanabilir.

IoT Central, cihaz modelleri için daha fazla sürüm kuralı uygular. Bir cihaz şablonunu ve modelini IoT Central sürümüne yüklerseniz, cihazları önceki sürümlerden sonraki sürümlere geçirebilirsiniz. Ancak, geçirilen cihazlar bellenim yükseltmesi olmadan yeni özellikleri kullanamaz. Daha fazla bilgi için bkz. [yeni cihaz şablonu sürümü oluşturma](../iot-central/core/howto-version-device-template.md).

## <a name="limits-and-constraints"></a>Sınırlar ve kısıtlamalar

Aşağıdaki listede, modellerdeki bazı önemli kısıtlamalar ve sınırlar özetlenmektedir:

- Şu anda diziler, haritalar ve nesneler için en yüksek derinlik, beş derinlik düzeydir.
- Özellik tanımlarında dizileri kullanamazsınız.
- Arabirimleri 10 düzey derinliğine genişletebilirsiniz.
- Bir arabirim, en fazla iki diğer arabirimi genişletebilir.
- Bir bileşen başka bir bileşen içeremez.

## <a name="next-steps"></a>Sonraki adımlar

Artık cihaz modelleme hakkında bilgi edindiğinize göre, bazı ek kaynaklar aşağıda verilmiştir:

- [DTDL yazma araçlarını yükle ve kullan](howto-use-dtdl-authoring-tools.md)
- [Dijital TWINS tanım dili v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Model depoları](./concepts-model-repository.md)
