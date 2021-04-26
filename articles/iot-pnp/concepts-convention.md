---
title: IoT Tak ve Kullan kuralları | Microsoft Docs
description: IoT Tak ve Kullan kurallarının açıklaması, cihazların telemetri ve özellik gönderirken kullanmasını bekler ve komutları ve özellik güncelleştirmelerini işler.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a8dfb3cacf798559273361ec75cab4570a8a5228
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582689"
---
# <a name="iot-plug-and-play-conventions"></a>IoT Tak Çalıştır kuralları

IoT Tak ve Kullan cihazları, bir IoT Hub ile ileti alışverişi yaparken bir dizi kural izlemelidir. IoT Tak ve Kullan cihazları, IoT Hub iletişim kurmak için MQTT protokolünü kullanır.

Cihazlar [modüller](../iot-hub/iot-hub-devguide-module-twins.md)içerebilir veya IoT Edge çalışma zamanı tarafından barındırılan bir [IoT Edge modüle](../iot-edge/about-iot-edge.md) uygulanabilir.

IoT Tak ve Kullan cihazının [dijital bir TWINS tanım dili v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) modeliyle uyguladığı telemetri, özellik ve komutları _betimleyebilirsiniz_. Bu makalede başvurulan iki tür model vardır:

- **Bileşen yok** -bileşeni olmayan bir model. Model, ana arabirimin içerikler bölümünde telemetri, özellik ve komutları en üst düzey özellikler olarak bildirir. Azure IoT gezgin aracında, bu model tek bir _varsayılan bileşen_ olarak görünür.
- **Birden çok bileşen** -iki veya daha fazla arabirimden oluşan bir model. Telemetri, Özellikler ve komutlarla _varsayılan bileşen_ olarak görünen ana arabirim. Ek telemetri, özellik ve komutlarla bileşen olarak belirtilen bir veya daha fazla arabirim.

Daha fazla bilgi için bkz. [ıot Tak ve kullan modelleme Kılavuzu](concepts-modeling-guide.md).

## <a name="identify-the-model"></a>Modeli tanımla

Uyguladığı modeli duyurmak için, bir IoT Tak ve Kullan cihazı veya modülü, alanına ekleyerek MQTT bağlantı paketindeki model KIMLIĞINI içerir `model-id` `USERNAME` .

Bir cihaz ya da modülün uyguladığı modeli belirlemek için, bir hizmet şu kaynaktan model KIMLIĞINI alabilir:

- Device ikizi `modelId` alanı.
- Digital ikizi `$metadata.$model` alanı.
- Dijital ikizi değişiklik bildirimi.

## <a name="telemetry"></a>Telemetri

Bileşen olmayan bir cihazdan gönderilen telemetri hiçbir ek meta veri gerektirmez. Sistem, özelliği ekler `dt-dataschema` .

Birden çok bileşen cihazından gönderilen telemetri `$.sub` bir ileti özelliği olarak eklememelidir. Sistem, `dt-subject` ve özelliklerini ekler `dt-dataschema` .

## <a name="read-only-properties"></a>Salt okunur özellikler

### <a name="sample-no-component-read-only-property"></a>Bileşen olmayan salt okuma özelliği örneği

Bir cihaz veya modül, DTDL v2 kurallarını izleyen geçerli bir JSON gönderebilir.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Örnek bildirilen özellik yükü:

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>Örnek birden çok bileşen salt okunurdur özelliği

Cihazın veya modülün, `{"__t": "c"}` öğenin bir bileşene başvurduğunu göstermek için işaretçiyi eklemesi gerekir.

Bir bileşene başvuran DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

Bileşeni tanımlayan DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Örnek bildirilen özellik yükü:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>Yazılabilir Özellikler

Cihaz veya modül bildirilen bir özellik göndererek özelliği aldığını onaylamasını sağlamalıdır. Bildirilen özellik şunları içermelidir:

- `value` -özelliğin gerçek değeri (genellikle alınan değer, ancak cihaz farklı bir değer raporlama kararı verebilir).
- `ac` -bir HTTP durum kodu kullanan bir bildirim kodu.
- `av` -istenen özelliğin öğesine başvuran bir bildirim sürümü `$version` . Bu değeri, istenen JSON yükünde, istenen özellikte bulabilirsiniz.
- `ad` -isteğe bağlı bir bildirim açıklaması.

Bir cihaz başlatıldığında cihaz ikizi istemeli ve yazılabilir Özellik güncelleştirmelerini denetlemelidir. Cihaz çevrimdışıyken yazılabilir bir özelliğin sürümü artmışsa, cihazın güncelleştirmeyi aldığını onaylamak için bildirilen bir özellik yanıtı göndermelidir.

Bir cihaz ilk kez başlatıldığında, bundan sonra bildirilen bir özellik için bir başlangıç değeri gönderebilir ve bu, hub 'dan istenen bir özelliği almaz. Bu durumda, cihaz `av` olarak ayarlanmalıdır `1` . Örnek:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

Bir cihaz, hub 'a diğer bilgileri sağlamak için bildirilen özelliği kullanabilir. Örneğin, cihaz şu şekilde bir dizi devam eden ileti ile yanıt verebilir:

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

Cihaz hedef sıcaklığa ulaştığında şu iletiyi gönderir:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

Bir cihaz, şöyle bir hata bildirebilir:

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="sample-no-component-writable-property"></a>Örnek bileşen yazılabilir özelliği yok

Bir cihaz tek bir yükte birden çok bildirilen özellikleri aldığında, bildirilen özellik yanıtlarını birden çok yük arasında gönderebilir.

Bir cihaz veya modül, DTDL v2 kurallarını izleyen geçerli bir JSON gönderebilir:

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Örnek istenen özellik yükü:

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80
},
"$version" : 3
```

Örnek bildirilen özelliğin ilk yükü:

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

Örnek bildirilen özellik ikinci yük:

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

### <a name="sample-multiple-components-writable-property"></a>Örnek birden çok bileşen yazılabilir özelliği

Cihazın veya modülün, `{"__t": "c"}` öğenin bir bileşene başvurduğunu göstermek için işaretçiyi eklemesi gerekir.

İşaretleyici yalnızca bir bileşende tanımlanan özelliklere yönelik güncelleştirmeler için gönderilir. Varsayılan bileşende tanımlanan özelliklere yapılan güncelleştirmeler işaretleyici içermez, bkz. [örnek hiçbir bileşen yazılabilir özelliği](#sample-no-component-writable-property) .

Bir cihaz tek bir yükte birden çok bildirilen özellikleri aldığında, bildirilen özellik yanıtlarını birden çok yük arasında gönderebilir.

Cihaz veya modül bildirilen özellikleri göndererek özellikleri aldığını onaylamasını sağlamalıdır:

Bir bileşene başvuran DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

Bileşeni tanımlayan DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Örnek istenen özellik yükü:

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80
  }
},
"$version" : 3
```

Örnek bildirilen özelliğin ilk yükü:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

Örnek bildirilen özellik ikinci yük:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

## <a name="commands"></a>Komutlar

Hiçbir bileşen arabirimi, önek olmadan komut adını kullanmaz.

Bir cihazda veya modülde, birden çok bileşen arabirimi komut adlarını aşağıdaki biçimde kullanır: `componentName*commandName` .

## <a name="next-steps"></a>Sonraki adımlar

IoT Tak ve Kullan kuralları hakkında bilgi edindiğinize göre artık bazı ek kaynaklar verilmiştir:

- [Dijital İkiz Tanımlama Dili (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C cihaz SDK’sı](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [IoT Tak ve Kullan modelleme Kılavuzu](concepts-modeling-guide.md)