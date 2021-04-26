---
title: Azure IoT Central çözümünde konum verilerini kullanma
description: IoT Central uygulamanıza bağlı bir cihazdan gönderilen konum verilerini nasıl kullanacağınızı öğrenin. Bir harita üzerindeki konum verilerini çizdirme veya bölge sınırlaması kuralları oluşturma.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c909fd1438488e3625f3674dd26f959cf6fad79f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98128054"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>Azure IoT Central çözümünde konum verilerini kullanma

Bu makalede, IoT Central uygulamasında konum verilerinin nasıl kullanılacağı gösterilir. IoT Central bağlı bir cihaz konum verilerini telemetri akışı olarak gönderebilir veya konum verilerini raporlamak için bir cihaz özelliği kullanabilir.

Bir çözüm Oluşturucusu, konum verilerini kullanarak şunları yapabilir:

* Bildirilen konumu haritada çiz.
* Telemetri konum geçmişini OM bir eşlemeyi çiz.
* Bir cihaz belirli bir alana girdiğinde veya ayrıldığında bir işlece bildirimde bulunan bölge sınırlama kuralları oluşturun.

## <a name="add-location-capabilities-to-a-device-template"></a>Bir cihaz şablonuna konum yetenekleri ekleme

Aşağıdaki ekran görüntüsünde, bir cihaz özelliği ve konum verilerini kullanan telemetri türü örnekleri içeren bir cihaz şablonu gösterilmektedir. Tanımlar, **konum** anlam türünü ve **coğrafi konum** şeması türünü kullanır:

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="Cihaz şablonunda konum özelliği tanımını gösteren ekran görüntüsü":::

Başvuru için, bu yeteneklerin [dijital TWINS tanım dili (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) tanımları aşağıdaki kod parçacığına benzer şekilde görünür:

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> **Geopoint** şema türü [dtdl belirtiminin](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)bir parçası değil. IoT Central Şu anda **geopoint** şema türünü ve geriye doğru uyumluluk için **konum** anlam türünü desteklemektedir.

## <a name="send-location-data-from-a-device"></a>Bir cihazdan konum verileri gönderme

Bir cihaz, önceki bölümde gösterilen **Devicelocation** özelliği için veri gönderdiğinde, yük aşağıdaki JSON kod parçacığı gibi görünür:

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Bir cihaz, önceki bölümde gösterilen **izleme** telemetrisi için veri gönderdiğinde, yük aşağıdaki JSON kod parçacığı gibi görünür:

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## <a name="display-device-location"></a>Cihaz konumunu görüntüle

IoT Central uygulamanızda, konum verilerini birden çok yerde görüntüleyebilirsiniz. Örneğin, tek bir cihazla veya panolar ile ilişkili görünümlerde.

Bir cihaz için bir görünüm oluşturduğunuzda, konumu haritaya veya tek tek değerleri göstermeye seçebilirsiniz:

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="Konum verileri ile örnek görünümü gösteren ekran görüntüsü":::

Bir veya daha fazla cihazın konumunu çizmek için, bir panoya harita kutucukları ekleyebilirsiniz. Konum telemetrisini göstermek için bir harita kutucuğu eklediğinizde, konumu bir zaman aralığı üzerine çizebilirsiniz. Aşağıdaki ekran görüntüsünde, son 30 dakikalık bir sanal cihaz tarafından bildirilen konum gösterilmektedir:

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="Konum verileri içeren örnek panoyu gösteren ekran görüntüsü":::

## <a name="create-a-geofencing-rule"></a>Bölge sınırlaması kuralı oluşturma

Bir cihaz dikdörtgen alanı içine veya dışına taşınırsa uyarı üreten bir bölge sınırlaması kuralı oluşturmak için konum telemetrisini kullanabilirsiniz. Aşağıdaki ekran görüntüsünde, enlem ve boylam değerlerini kullanarak dikdörtgen bir alanı tanımlamak için dört koşul kullanan bir kural gösterilmektedir. Bu kural, cihaz dikdörtgen alana taşınmışken bir e-posta oluşturur:

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="Bölge sınırlaması kural tanımını gösteren ekran görüntüsü":::

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanızda özelliklerin nasıl kullanılacağını öğrendiğinize göre, bkz.:

* [Yükü](concepts-telemetry-properties-commands.md)
* [Bir istemci uygulamasını oluşturma ve Azure IoT Central uygulamanıza bağlama](tutorial-connect-device.md)