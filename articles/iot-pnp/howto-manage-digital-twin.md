---
title: IoT Tak ve Kullan dijital TWINS 'i yönetme
description: Digital ikizi API 'Lerini kullanarak IoT Tak ve Kullan cihazını yönetme
author: prashmo
ms.author: prashmo
ms.date: 12/17/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e68003878dc0e9275461100a59e0f45486c2978f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739881"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>IoT Tak ve Kullan dijital TWINS 'i yönetme

IoT Tak ve Kullan, dijital TWINS 'yi yönetmek için dijital **Ikizi al** ve **dijital ikizi işlemlerini Güncelleştir** ' i destekler. [REST API 'leri](/rest/api/iothub/service/digitaltwin) ya da [hizmet SDK 'lardan](libraries-sdks.md)birini kullanabilirsiniz.

Yazma sırasında Digital ikizi API sürümü `2020-09-30` .

## <a name="update-a-digital-twin"></a>Dijital ikizi güncelleştirme

IoT Tak ve Kullan cihazı, [dijital TWINS tanım dili v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)tarafından tanımlanan bir modeli uygular. Çözüm geliştiricileri, bileşen durumunu ve Digital ikizi özelliklerini güncelleştirmek için **Digital IKIZI API 'Sini Güncelleştir** ' i kullanabilir.

Bu makalede bir örnek olarak kullanılan IoT Tak ve Kullan cihazı, [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) bileşenleriyle [sıcaklık denetleyicisi modelini](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) uygular.

Aşağıdaki kod parçacığında, bir JSON nesnesi olarak biçimlendirilen bir **Get Digital ikizi** isteğine yanıt gösterilmektedir. Dijital ikizi biçimi hakkında daha fazla bilgi edinmek için bkz. [ıot Tak ve kullan dijital TWINS 'ı anlama](./concepts-digital-twin.md#digital-twin-example):

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
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

Dijital TWINS, bir [JSON yaması](http://jsonpatch.com/)kullanarak bir bileşen veya özelliğin tamamını güncelleştirmenize olanak tanır.

Örneğin, `targetTemperature` özelliğini aşağıdaki gibi güncelleştirebilirsiniz:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

Önceki güncelleştirme, `$metadata` Aşağıdaki kod parçacığında gösterildiği gibi karşılık gelen bileşen düzeyindeki bir özelliğin istenen değerini ayarlar. IoT Hub, özelliğin istenen sürümünü güncelleştirir:

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>Bileşen ekleme, değiştirme veya kaldırma

Bileşen düzeyi işlemleri değer içinde boş bir nesne `$metadata` işaretleyicisi gerektirir.

Bileşen ekleme veya değiştirme işlemi, belirtilen tüm özelliklerin istenen değerlerini ayarlar. Ayrıca, güncelleştirme ile sağlanmayan yazılabilir özellikler için istenen değerleri de temizler.

Bir bileşeni kaldırmak, mevcut tüm yazılabilir özelliklerin istenen değerlerini temizler. Bir cihaz sonunda bu kaldırma eşitlenir ve tek tek özellikleri raporlamayı durduruyor. Daha sonra bileşen dijital ikizi kaldırılır.

Aşağıdaki JSON Patch örneği, bir bileşenin nasıl ekleneceğini, değiştirileceğini veya kaldırılacağını gösterir:

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>Özellik ekleme, değiştirme veya kaldırma

Bir ekleme veya değiştirme işlemi, bir özelliğin istenen değerini ayarlar. Cihaz durumu eşitleyebilir ve bir `ack` kod, sürüm ve açıklama ile birlikte değerin bir güncelleştirmesini rapor edebilir.

Bir özelliğin kaldırılması, ayarlandıysa, istenen özellik değerini temizler. Cihaz daha sonra bu özelliği raporlamayı durdurabilir ve bileşen bileşeninden kaldırılır. Bu özellik bileşende son ise, bileşen da kaldırılır.

Aşağıdaki JSON Patch örneği bir bileşen içindeki bir özelliğin nasıl ekleneceğini, değiştirileceğini veya kaldırılacağını gösterir:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Bir Digital ikizi özelliğinin istenen değerini ayarlamaya yönelik kurallar

**Ad**

Bir bileşenin veya özelliğin adı geçerli bir DTDL V2 adı olmalıdır.

İzin verilen karakterler a-z, A-Z, 0-9 (ilk karakter olarak değil) ve alt çizgi (ilk veya son karakter olarak değil).

Ad 1-64 karakter uzunluğunda olabilir.

**Özellik değeri**

Değer geçerli bir [Dtdl v2 özelliği](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)olmalıdır.

Tüm ilkel türler desteklenir. Karmaşık türler içinde, numaralandırmalar, haritalar ve nesneler desteklenir. Daha fazla bilgi için bkz. [Dtdl v2 şemaları](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas).

Özellikler diziyi veya bir dizi ile karmaşık şemayı desteklemez.

Karmaşık bir nesne için en fazla beş düzeyin derinliği desteklenir.

Karmaşık nesne içindeki tüm alan adları geçerli DTDL v2 adları olmalıdır.

Tüm harita anahtarları geçerli DTDL v2 adları olmalıdır.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>Digital ikizi API hatalarını güncelleştirme sorunlarını giderme

Digital ikizi API 'SI aşağıdaki genel hata iletisini oluşturur:

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Bu hatayı görürseniz, güncelleştirme düzeltme ekinin [bir Digital ikizi özelliğinin istenen değerini ayarlamaya yönelik kurallara](#rules-for-setting-the-desired-value-of-a-digital-twin-property) uyduğundan emin olun

Bir bileşeni güncelleştirdiğinizde [boş nesne $Metadata işaretleyicinin](#add-replace-or-remove-a-component) ayarlandığından emin olun.

Bir cihazın bildirilen değerleri [IoT Tak ve Çalıştır kurallarıyla](./concepts-convention.md#writable-properties)uyumlu değilse güncelleştirmeler başarısız olabilir.

## <a name="next-steps"></a>Sonraki adımlar

Artık dijital TWINS hakkında bilgi edindiğinize göre, bazı ek kaynaklar aşağıda verilmiştir:

- [Çözümünüzden bir cihazla etkileşim kurma](quickstart-service.md)
- [IoT dijital Ikizi REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Gezgini](howto-use-iot-explorer.md)
