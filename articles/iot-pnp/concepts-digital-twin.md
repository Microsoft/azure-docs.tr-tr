---
title: IoT Tak Çalıştır dijital ikizlerini anlama
description: IoT Tak ve Kullan dijital TWINS 'i nasıl kullandığını anlayın
author: prashmo
ms.author: prashmo
ms.date: 12/14/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 99c957e5bf6ffe69c94e109796590f5ab975c3cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656895"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>IoT Tak Çalıştır dijital ikizlerini anlama

IoT Tak ve Kullan cihazı, [dijital TWINS tanım dili (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) şeması tarafından tanımlanan bir modeli uygular. Model, belirli bir cihazın sahip olduğu bileşenler, özellikler, komutlar ve telemetri iletileri kümesini açıklar.

IoT Tak ve Kullan DTDL sürüm 2 kullanır. Bu sürüm hakkında daha fazla bilgi için bkz. GitHub 'da [dijital TWINS tanım dili (DTDL)-sürüm 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) belirtimi.

> [!NOTE]
> DTDL, IoT Tak ve Kullan özel değildir. [Azure dijital TWINS](../digital-twins/overview.md)gibi diğer IoT Hizmetleri, binalar ve enerji ağları gibi tüm ortamları temsil etmek için bunu kullanır.

Azure IoT hizmeti SDK 'Ları, bir hizmetin bir cihazın dijital ikizi etkileşime girmesine izin veren API 'Leri içerir. Örneğin, bir hizmet, dijital ikizi cihaz özelliklerini okuyabilir veya bir cihazdaki komutu çağırmak için dijital ikizi kullanabilir. Daha fazla bilgi için bkz. [IoT Hub Digital ikizi örnekleri](concepts-developer-guide-service.md#iot-hub-digital-twin-examples).

Bu makaledeki örnek IoT Tak ve Kullan cihazı, [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) bileşenlerine sahip bir [sıcaklık denetleyicisi modeli](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) uygular.

## <a name="device-twins-and-digital-twins"></a>Cihaz ikları ve dijital TWINS

Ayrıca, dijital bir ikizi de Azure IoT Hub her bağlı cihaz için bir *cihaz ikizi* bulundurur. Bir cihaz ikizi, bir cihazın özelliklerinin temsili olan dijital bir ikizi benzerdir. Azure IoT hizmeti SDK 'Ları, cihaz ikikiyle etkileşime yönelik API 'Leri içerir.

IoT Hub 'ı, bir IoT Tak ve Kullan cihazının ilk bağlanışında bir Digital ikizi ve bir cihaz ikizi başlatır.

Cihaz TWINS, meta veriler, konfigürasyonlar ve koşullar dahil olmak üzere cihaz durum bilgilerini depolayan JSON belgelerdir. Daha fazla bilgi için bkz. [IoT Hub hizmeti istemci örnekleri](concepts-developer-guide-service.md#iot-hub-service-client-examples). Hem cihaz hem de çözüm oluşturucular, IoT Tak ve Kullan kurallarını kullanarak cihaz ve çözümleri uygulamak için aynı cihaz Ikizi API 'Leri ve SDK 'ları kullanmaya devam edebilir.

Digital ikizi API 'Leri, bileşenler, Özellikler ve komutlar gibi üst düzey DTDL yapıları üzerinde çalışır. Dijital ikizi API 'Leri, çözüm oluşturucuların IoT Tak ve Kullan çözümleri oluşturmalarına daha kolay hale getirir.

Bir cihaz ikizi, yazılabilir bir özelliğin durumu *istenen özellikler* ve *bildirilen özellikler* bölümlerine bölünür. Tüm salt okunurdur özellikler, bildirilen Özellikler bölümünde bulunur.

Dijital bir ikizi, özelliğin geçerli ve istenen durumunun birleştirilmiş bir görünümü vardır. Belirli bir özelliğin eşitleme durumu karşılık gelen varsayılan bileşen `$metadata` bölümünde saklanır.

### <a name="device-twin-json-example"></a>Device ikizi JSON örneği

Aşağıdaki kod parçacığında, bir ikizi, JSON nesnesi olarak biçimlendirilen bir IoT Tak ve Kullan cihazı gösterilmektedir:

```json
{
  "deviceId": "sample-device",
  "modelId": "dtmi:com:example:TemperatureController;1",
  "version": 15,
  "properties": {
    "desired": {
      "thermostat1": {
        "__t": "c",
        "targetTemperature": 21.8
      },
      "$metadata": {...},
      "$version": 4
    },
    "reported": {
      "serialNumber": "alwinexlepaho8329",
      "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "__t": "c",
        "targetTemperature": {
          "value": 21.8,
          "ac": 200,
          "ad": "Successfully executed patch",
        }
      },
      "$metadata": {...},
      "$version": 11
    }
  }
}
```

### <a name="digital-twin-example"></a>Digital ikizi örneği

Aşağıdaki kod parçacığında, JSON nesnesi olarak biçimlendirilen dijital ikizi gösterilmektedir:

```json
{
  "$dtId": "sample-device",
  "serialNumber": "alwinexlepaho8329",
  "thermostat1": {
    "maxTempSinceLastReboot": 25.3,
    "targetTemperature": 21.8,
    "$metadata": {
      "targetTemperature": {
        "desiredValue": 21.8,
        "desiredVersion": 4,
        "ackVersion": 4,
        "ackCode": 200,
        "ackDescription": "Successfully executed patch",
        "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
      },
      "maxTempSinceLastReboot": {
         "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:example:TemperatureController;1",
    "serialNumber": {
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

Aşağıdaki tablo, Digital ikizi JSON nesnesindeki alanları açıklar:

| Alan adı | Description |
| --- | --- |
| `$dtId` | Cihazın dijital ikizi KIMLIĞINI temsil eden kullanıcı tarafından sağlanmış dize |
| `{propertyName}` | JSON içindeki bir özelliğin değeri |
| `$metadata.$model` | Seçim Bu dijital ikizi karakterleştirir model arabiriminin KIMLIĞI |
| `$metadata.{propertyName}.desiredValue` | [Yalnızca yazılabilir özellikler için] Belirtilen özelliğin istenen değeri |
| `$metadata.{propertyName}.desiredVersion` | [Yalnızca yazılabilir özellikler için] IoT Hub tarafından tutulan istenen değerin sürümü|
| `$metadata.{propertyName}.ackVersion` | [Gerekli, yalnızca yazılabilir özellikler için] Dijital ikizi uygulayan cihaz tarafından kabul edilen sürüm, istenen sürüme eşit veya ondan daha büyük olmalıdır |
| `$metadata.{propertyName}.ackCode` | [Gerekli, yalnızca yazılabilir özellikler için] `ack` Dijital ikizi uygulayan cihaz uygulaması tarafından döndürülen kod |
| `$metadata.{propertyName}.ackDescription` | [İsteğe bağlı, yalnızca yazılabilir özellikler için] `ack` Dijital ikizi uygulayan cihaz uygulaması tarafından döndürülen açıklama |
| `$metadata.{propertyName}.lastUpdateTime` | IoT Hub, özelliğin son güncelleştirme zaman damgasını cihaz tarafından korur. Zaman damgaları UTC biçimindedir ve ıSO8601 biçiminde kodlanır YYYY-MM-DDTHH: MM: SS. mmmZ |
| `{componentName}` | Bileşenin özellik değerlerini ve meta verilerini içeren bir JSON nesnesi. |
| `{componentName}.{propertyName}` | JSON 'daki bileşen özelliğinin değeri |
| `{componentName}.$metadata` | Bileşen için meta veri bilgileri. |

### <a name="properties"></a>Özellikler

Özellikler, bir varlığın durumunu temsil eden veri alanlarıdır (birçok nesne odaklı programlama dilinde özellikler gibi).

#### <a name="read-only-property"></a>Salt okunurdur özelliği

DTDL şeması:

```json
{
    "@type": "Property",
    "name": "serialNumber",
    "displayName": "Serial Number",
    "description": "Serial number of the device.",
    "schema": "string"
}
```

Bu örnekte, `alwinexlepaho8329` `serialNumber` cihaz tarafından bildirilen salt okunurdur özelliğinin geçerli değeridir.
Aşağıdaki kod parçacıkları, özelliğinin yan yana JSON gösterimini gösterir `serialNumber` :

:::row:::
   :::column span="":::
      **Cihaz çifti**

```json
"properties": {
  "reported": {
    "serialNumber": "alwinexlepaho8329"
  }
}
```

   :::column-end:::
   :::column span="":::
      **Dijital ikizi**

```json
"serialNumber": "alwinexlepaho8329"
```

   :::column-end:::
:::row-end:::

#### <a name="writable-property"></a>Yazılabilir Özellik

Aşağıdaki örneklerde varsayılan bileşende yazılabilir bir özellik gösterilmektedir.

DTDL:

```json
{
  "@type": "Property",
  "name": "fanSpeed",
  "displayName": "Fan Speed",
  "writable": true,
  "schema": "double"
}
```

:::row:::
   :::column span="":::
      **Cihaz çifti**

```json
{
  "properties": {
    "desired": {
      "fanSpeed": 2.0,
    },
    "reported": {
      "fanSpeed": {
        "value": 3.0,
        "ac": 200,
        "av": 1,
        "ad": "Successfully executed patch version 1"
      }
    }
  },
}
```

   :::column-end:::
   :::column span="":::
      **Dijital ikizi**

```json
{
  "fanSpeed": 3.0,
  "$metadata": {
    "fanSpeed": {
      "desiredValue": 2.0,
      "desiredVersion": 2,
      "ackVersion": 1,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch version 1",
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

Bu örnekte, `3.0` `fanSpeed` cihaz tarafından bildirilen özelliğin geçerli değeridir. `2.0` , çözüm tarafından ayarlanan istenen değerdir. Kök düzeyindeki bir özelliğin istenen değeri ve eşitleme durumu, `$metadata` dijital ikizi için kök düzeyinde ayarlanır. Cihaz çevrimiçi olduğunda, bu güncelleştirmeyi uygulayabilir ve güncelleştirilmiş değeri geri bildirebilirsiniz.

### <a name="components"></a>Bileşenler

Bileşenler, model arabiriminin diğer arabirimlerin derlemesi olarak oluşturulmasına izin verir.
Örneğin, [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) arabirimi, bileşenler olarak `thermostat1` ve  `thermostat2` [sıcaklık denetleyicisi model](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) modelinde eklenebilir.

Bir cihaz ikizi, bir bileşen işaret tarafından tanımlanır `{ "__t": "c"}` . Dijital bir ikizi, `$metadata` bir bileşeni işaret ediyor.

Bu örnekte, `thermostat1` iki özelliği olan bir bileşendir:

- `maxTempSinceLastReboot` salt okunurdur.
- `targetTemperature` , cihaz tarafından başarıyla eşitlenen yazılabilir bir özelliktir. Bu özelliklerin istenen değeri ve eşitleme durumu bileşen ' de bulunur `$metadata` .

Aşağıdaki kod parçacıkları, bileşenin yan yana JSON gösterimini gösterir `thermostat1` :

:::row:::
   :::column span="":::
      **Cihaz çifti**

```json
"properties": {
  "desired": {
    "thermostat1": {
      "__t": "c",
      "targetTemperature": 21.8
    },
    "$metadata": {
    },
    "$version": 4
  },
  "reported": {
    "thermostat1": {
      "maxTempSinceLastReboot": 25.3,
      "__t": "c",
      "targetTemperature": {
        "value": 21.8,
        "ac": 200,
        "ad": "Successfully executed patch",
        "av": 4
      }
    },
    "$metadata": {
    },
    "$version": 11
  }
}
```

   :::column-end:::
   :::column span="":::
      **Dijital ikizi**

```json
"thermostat1": {
  "maxTempSinceLastReboot": 25.3,
  "targetTemperature": 21.8,
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 21.8,
      "desiredVersion": 4,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch",
      "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
    },
    "maxTempSinceLastReboot": {
       "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

## <a name="digital-twin-apis"></a>Dijital ikizi API 'Leri

Dijital ikizi API 'Leri, dijital **Ikizi al**, **dijital Ikizi güncelleştirme**, **bileşen çağırma** ve **komut işlemlerini çağırma** , dijital bir ikizi yönetme içerir. [REST API 'leri](/rest/api/iothub/service/digitaltwin) doğrudan ya da bir [hizmet SDK 'sı](../iot-pnp/libraries-sdks.md)aracılığıyla kullanabilirsiniz.

## <a name="digital-twin-change-events"></a>Dijital ikiz değişiklik olayları

Dijital ikiz değişiklik olayları etkinleştirilirse, bileşenin veya özelliğin geçerli veya istenen değeri her değiştiğinde bir olay tetiklenir. Dijital ikizi değişiklik olayları [JSON Patch](http://jsonpatch.com/) biçiminde oluşturulur. İkizi değişiklik olayları etkinse, ilgili olaylar Device ikizi biçiminde oluşturulur.

Cihaz ve dijital ikizi olayları için yönlendirmeyi nasıl etkinleştireceğinizi öğrenmek için bkz. [farklı uç noktalara cihazdan buluta iletiler göndermek için IoT Hub ileti yönlendirmeyi kullanma](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). İleti biçimini anlamak için bkz. [IoT Hub Iletileri oluşturma ve okuma](../iot-hub/iot-hub-devguide-messages-construct.md).

Örneğin, aşağıdaki Digital ikizi Change olayı `targetTemperature` çözüm tarafından ayarlandığında tetiklenir:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:04 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d463fa034
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature",
    "value": {
      "desiredValue": 21.8,
      "desiredVersion": 4
    }
  }
]
```

Aşağıdaki dijital ikizi değişiklik olayı, cihaz yukarıdaki istenen değişikliğin uygulandığını bildirdiğinde tetiklenir:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:05 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d464a2c80
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackCode",
    "value": 200
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackDescription",
    "value": "Successfully executed patch"
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackVersion",
    "value": 4
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/lastUpdateTime",
    "value": "2020-07-17T06:11:04.9309159Z"
  },
  {
    "op": "add",
    "path": "/thermostat1/targetTemperature",
    "value": 21.8
  }
]
```

> [!NOTE]
> İkizi değişiklik bildirimi iletileri hem cihaz hem de dijital ikizi değişiklik bildiriminde açıldığında iki katına çıkar.

## <a name="next-steps"></a>Sonraki adımlar

Artık dijital TWINS hakkında bilgi edindiğinize göre, bazı ek kaynaklar aşağıda verilmiştir:

- [IoT Tak ve Kullan Digital ikizi API 'Lerini kullanma](howto-manage-digital-twin.md)
- [Çözümünüzden bir cihazla etkileşim kurma](quickstart-service.md)
- [IoT dijital Ikizi REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Gezgini](howto-use-iot-explorer.md)