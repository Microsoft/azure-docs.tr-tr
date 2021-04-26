---
title: Olay bildirimleri
titleSuffix: Azure Digital Twins
description: Farklı olay türlerini ve bunların farklı bildirim iletilerini yorumlama bölümüne bakın.
author: baanders
ms.author: baanders
ms.date: 4/8/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q4
ms.openlocfilehash: 42842b00120b7e918ca5b790cce92a74ab1b99d5
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260015"
---
# <a name="event-notifications"></a>Olay bildirimleri

Azure Digital TWINS 'deki farklı olaylar, farklı eylemler meydana geldiğinde çözüm arka ucunun farkında olmasını sağlayan **Bildirimler** üretir. Bunlar daha sonra bu bilgileri işlem yapmak için kullanabileceğiniz Azure dijital TWINS içindeki ve dışındaki farklı konumlara [yönlendirilir](concepts-route-events.md) .

Oluşturulabilecek birçok bildirim türü vardır ve bildirim iletileri, hangi olay türüne bağlı olarak farklı görünebilir. Bu makale, farklı ileti türleri ve nasıl görünebilecekleri hakkında ayrıntılı bilgi sağlar.

Bu grafik farklı bildirim türlerini gösterir:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="notification-structure"></a>Bildirim yapısı

Genel olarak, bildirimler iki bölümden oluşur: başlık ve gövde. 

### <a name="event-notification-headers"></a>Olay bildirim üstbilgileri

Bildirim iletisi üstbilgileri, anahtar-değer çiftleri ile temsil edilir. Kullanılan protokole (MQTT, AMQP veya HTTP) bağlı olarak, ileti üstbilgileri farklı şekilde serileştirilir. Bu bölümde, seçilen protokole ve Serileştirmeden bağımsız olarak bildirim iletilerine ilişkin genel üst bilgi bilgileri anlatılmaktadır.

Bazı bildirimler [Cloudevents](https://cloudevents.io/) standardına uygundur. CloudEvents uygunluğu aşağıdaki gibidir.
* Cihazlardan alınan bildirimler, bildirim için mevcut belirtimleri izlemeye devam eder
* IoT Hub tarafından işlenen ve yayılan bildirimler, IoT Hub, Event Grid gibi CloudEvents desteğini desteklemeyi tercih ettiği durumlar haricinde bildirim için mevcut belirtimleri izlemeye devam eder.
* Bir [model](concepts-models.md) ile [dijital TWINS](concepts-twins-graph.md) 'Ten alınan bildirimler, cloudevents ile uyumlu
* Azure dijital TWINS tarafından işlenen ve yayılan bildirimler, CloudEvents ile uyumlu

Hizmetlerin, sıralarını belirtmek için tüm bildirimlere bir sıra numarası eklemesi veya kendi sıralamasını başka bir şekilde korumanız gerekir. 

Azure dijital TWINS tarafından Event Grid 'e yapılan bildirimler, olay Kılavuzu konusunda tanımlanan şema türüne bağlı olarak, CloudEvents şeması veya EventGridEvent şemasına otomatik olarak biçimlendirilir. 

Üst bilgilerdeki uzantı öznitelikleri, yük içindeki Event Grid şemasına özellikler olarak eklenecektir. 

### <a name="event-notification-bodies"></a>Olay bildirim gövdeleri

Bildirim iletilerinin gövdeleri burada JSON 'da açıklanmıştır. İleti gövdesi (örneğin, JSON, CBOR, Protoda vb.) için istenen seri hale getirme öğesine bağlı olarak, ileti gövdesi farklı şekilde seri hale getirilebilir.

Gövdenin içerdiği alan kümesi, farklı bildirim türleriyle farklılık gösterir.

Aşağıdaki bölümler IoT Hub ve Azure dijital TWINS (veya diğer Azure IoT Hizmetleri) tarafından yayınlanan farklı bildirim türleri hakkında daha ayrıntılı bilgi vermektedir. Her bildirim türünü tetikleyen şeyleri ve her bir bildirim gövdesi türüyle birlikte bulunan alan kümesini okuyacaksınız.

## <a name="digital-twin-change-notifications"></a>Dijital ikizi değişiklik bildirimleri

Dijital **ikizi değişiklik bildirimleri** , bir dijital ikizi güncelleştirilirken tetiklenir, örneğin:
* Özellik değerleri veya meta veriler değiştiğinde.
* Dijital ikizi veya bileşen meta verileri değiştiğinde. Bu senaryoya bir örnek, dijital bir ikizi modelini değiştiriyor.

### <a name="properties"></a>Özellikler

Dijital ikizi değişiklik bildiriminin gövdesinde yer alan alanlar aşağıda verilmiştir.

| Name    | Değer |
| --- | --- |
| `id` | Bir UUID veya hizmet tarafından tutulan bir sayaç gibi bildirimin tanımlayıcısı. `source` + `id` her farklı olay için benzersizdir |
| `source` | *Myhub.Azure-Devices.net* veya *mydigitaltwins.westus2.azuredigitaltwins.net* gibi IoT Hub veya Azure dijital TWINS örneğinin adı |
| `data` | İkizi üzerinde yapılan güncelleştirmeyi açıklayan bir JSON yama belgesi. Ayrıntılar için aşağıdaki [gövde ayrıntılarına](#body-details) bakın. |
| `specversion` | *1.0*<br>İleti, [Cloudevents belirtiminin](https://github.com/cloudevents/spec)bu sürümüne uyar. |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | Dijital ikizi KIMLIĞI |
| `time` | İşlem dijital ikizi üzerinde gerçekleştiyse zaman damgası |
| `traceparent` | Etkinlik için bir W3C Trace bağlamı |

### <a name="body-details"></a>Gövde ayrıntıları

İleti içinde, alan, `data` dijital ikizi güncelleştirmesini içeren BIR JSON yama belgesi içerir.

Örneğin, bir dijital ikizi, aşağıdaki düzeltme eki kullanılarak güncelleştirildiğini varsayalım.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component-2.json":::

Karşılık gelen bildirimde bulunan veriler (Azure dijital TWINS 'i güncelleştiren bir dijital ikizi gibi), şöyle bir gövdeye sahip olur:

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

Bu, `data` yaşam döngüsü bildirim iletisi alanına gidecek olan bilgiler olacaktır.

## <a name="digital-twin-lifecycle-notifications"></a>Digital ikizi yaşam döngüsü bildirimleri

Tüm [dijital TWINS](concepts-twins-graph.md) , [Azure dijital twıns 'de IoT Hub cihazları](how-to-ingest-iot-hub-data.md) temsil etmeksizin bağımsız olarak bildirimleri yayar. Bunun nedeni, dijital ikizi kendisi ile ilgili olan **yaşam döngüsü bildirimlerinin** bir kendisidir.

Yaşam döngüsü bildirimleri şu durumlarda tetiklenir:
* Dijital bir ikizi oluşturulur
* Dijital ikizi silindi

### <a name="properties"></a>Özellikler

Yaşam döngüsü bildiriminin gövdesinde yer alan alanlar aşağıda verilmiştir.

| Name | Değer |
| --- | --- |
| `id` | Bir UUID veya hizmet tarafından tutulan bir sayaç gibi bildirimin tanımlayıcısı. `source` + `id` her farklı olay için benzersizdir. |
| `source` | *Myhub.Azure-Devices.net* veya *mydigitaltwins.westus2.azuredigitaltwins.net* gibi IoT Hub veya Azure dijital TWINS örneğinin adı |
| `data` | Yaşam döngüsü olayını yaşayan ikizi verileri. Ayrıntılar için aşağıdaki [gövde ayrıntılarına](#body-details-1) bakın. |
| `specversion` | *1.0*<br>İleti, [Cloudevents belirtiminin](https://github.com/cloudevents/spec)bu sürümüne uyar. |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | Dijital ikizi KIMLIĞI |
| `time` | İkizi üzerinde işlem gerçekleştiği zaman damgası |
| `traceparent` | Etkinlik için bir W3C Trace bağlamı |

### <a name="body-details"></a>Gövde ayrıntıları

Yaşam döngüsü bildirim iletisine bir örnek aşağıda verilmiştir: 

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

İleti içinde, alan, `data` JSON biçiminde gösterilen etkilenen dijital ikizi verilerini içerir. Bu, *dijital TWINS kaynağı 7,1*' dir.

Oluşturma olayları için yük, `data` kaynak oluşturulduktan sonra ikizi durumunu yansıtır, bu nedenle tüm sistem tarafından oluşturulan öğeleri bir çağrıda olduğu gibi içermelidir `GET` .

Aşağıda, bileşenler ve en üst düzey özellikler olmadan [ıot Tak ve kullan (PnP)](../iot-pnp/overview-iot-plug-and-play.md) cihazının verilerine bir örnek verilmiştir. Cihazlar için anlamlı olmayan Özellikler (bildirilen özellikler gibi) atlanmalıdır. Bu, `data` yaşam döngüsü bildirim iletisi alanına gidecek olan bilgiler olacaktır.

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

Dijital ikizi verilerine yönelik başka bir örnek aşağıda verilmiştir. Bu, bir [modeli](concepts-models.md)temel alır ve bileşenleri desteklemez:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

## <a name="digital-twin-relationship-change-notifications"></a>Digital ikizi ilişki değişikliği bildirimleri

**İlişki değişikliği bildirimleri** , dijital ikizi herhangi bir ilişkisi oluşturulduğunda, güncelleştirilirken veya silindiğinde tetiklenir. 

### <a name="properties"></a>Özellikler

İlişki değişikliği bildiriminin gövdesinde yer alan alanlar aşağıda verilmiştir.

| Name    | Değer |
| --- | --- |
| `id` | Bir UUID veya hizmet tarafından tutulan bir sayaç gibi bildirimin tanımlayıcısı. `source` + `id` her farklı olay için benzersizdir |
| `source` | *Mydigitaltwins.westus2.azuredigitaltwins.net* gibi Azure dijital TWINS örneğinin adı |
| `data` | Değiştirilen ilişkinin yükü. Ayrıntılar için aşağıdaki [gövde ayrıntılarına](#body-details-2) bakın. |
| `specversion` | *1.0*<br>İleti, [Cloudevents belirtiminin](https://github.com/cloudevents/spec)bu sürümüne uyar. |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | İlişkinin KIMLIĞI, örneğin `<twinID>/relationships/<relationshipID>` |
| `time` | İlişki üzerinde işlem gerçekleştiği zaman damgası |
| `traceparent` | Etkinlik için bir W3C Trace bağlamı |

### <a name="body-details"></a>Gövde ayrıntıları

İleti içinde, `data` alan JSON biçiminde bir ilişkinin yükünü içerir. Bu, `GET` [Digitaltwins API 'si](/rest/api/digital-twins/dataplane/twins)aracılığıyla bir ilişki isteğiyle aynı biçimi kullanır. 

Bir güncelleştirme ilişkisi bildirimine yönelik verilerin bir örneği aşağıda verilmiştir. "İlişki güncelleştirme", ilişkinin özelliklerinin değiştiği anlamına gelir, bu nedenle veriler güncelleştirilmiş özelliği ve yeni değerini gösterir. Bu, `data` Digital ikizi ilişki bildirim iletisi alanının alanına gidecek olan bilgiler olacaktır.

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

Bir oluşturma veya silme ilişkisi bildirimi için bir veri örneği aşağıda verilmiştir. İçin `Relationship.Delete` , gövde `GET` istekle aynıdır ve silinmeden önce en son durumu alır.

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

## <a name="digital-twin-telemetry-messages"></a>Dijital ikizi telemetri iletileri

**Telemetri iletileri** , ölçümleri toplayıp gönderen bağlı cihazlardan Azure dijital TWINS 'de alınır.

### <a name="properties"></a>Özellikler

Telemetri iletisi gövdesindeki alanlar aşağıda verilmiştir.

| Name    | Değer |
| --- | --- |
| `id` | Telemetri API 'SI çağrılırken müşteri tarafından belirtilen bildirimin tanımlayıcısı. |
| `source` | Telemetri olayının gönderildiği ikizi tam adı. Şu biçimi kullanır: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>` . |
| `specversion` | *1.0*<br>İleti, [Cloudevents belirtiminin](https://github.com/cloudevents/spec)bu sürümüne uyar. |
| `type` | `microsoft.iot.telemetry` |
| `data` | TWINS 'e gönderilen telemetri iletisi. Yük değişmez ve telemetri gönderilen ikizi şeması ile hizalanmayabilir. |
| `dataschema` | Veri şeması, ikizi veya Telemetriyi gösteren bileşenin model KIMLIĞIDIR. Örneğin, `dtmi:example:com:floor4;2`. |
| `datacontenttype` | `application/json` |
| `traceparent` | Olay için bir W3C Trace bağlamı. |

### <a name="body-details"></a>Gövde ayrıntıları

Gövde, cihaz hakkında bazı bağlamsal bilgilerle birlikte telemetri ölçümünü içerir.

Örnek bir telemetri iletisi gövdesi aşağıda verilmiştir: 

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

## <a name="next-steps"></a>Sonraki adımlar

Uç noktaların ve yolların kullanıldığı farklı hedeflere olay sunma hakkında bilgi edinin:
* [*Kavramlar: olay yolları*](concepts-route-events.md)
