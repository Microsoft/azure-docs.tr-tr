---
title: Azure IoT Hub ve Event Grid | Microsoft Docs
description: İşlemleri IoT Hub gerçekleşen eylemlere göre tetiklemek için Azure Event Grid kullanın.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 2969791204474a7d73493ce6397c52255f7eab4a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151304"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Eylemleri tetiklemek için Event Grid kullanarak IoT Hub olaylara tepki verme

Azure IoT Hub, diğer hizmetlere olay bildirimleri gönderebilmek ve aşağı akış süreçlerini tetikleyebilmeniz için Azure Event Grid tümleştirilir. Kritik olaylara güvenilir, ölçeklenebilir ve güvenli bir şekilde yanıt vermek için, iş uygulamalarınızı IoT Hub olaylarını dinleyecek şekilde yapılandırın. Örneğin, bir veritabanını güncelleştiren, bir iş bileti oluşturan ve IoT Hub 'ınıza yeni bir IoT cihazı her kaydedildiğinde bir e-posta bildirimi sunan bir uygulama oluşturun.

[Azure Event Grid](../event-grid/overview.md) , yayımlama-abonelik modeli kullanan tam olarak yönetilen bir olay yönlendirme hizmetidir. Event Grid Azure [işlevleri](../azure-functions/functions-overview.md) ve [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)gibi Azure hizmetleri için yerleşik desteğe sahiptir ve Web kancalarını kullanarak Azure olmayan hizmetlere olay uyarıları sunabilir. Event Grid desteklediği olay işleyicilerinin tüm listesi için bkz. [Azure Event Grid giriş](../event-grid/overview.md).

![Azure Event Grid mimarisi](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Bölgesel kullanılabilirlik

Event Grid tümleştirme, Event Grid desteklendiği bölgelerde bulunan IoT Hub 'lar için kullanılabilir. Bölgelerin en son listesi için bkz. [Azure Event Grid bir giriş](../event-grid/overview.md).

## <a name="event-types"></a>Olay türleri

IoT Hub aşağıdaki olay türlerini yayınlar:

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Bir cihaz IoT Hub 'ına kaydedildiğinde yayımlandı. |
| Microsoft.Devices.DeviceDeleted | IoT Hub 'ından bir cihaz silindiğinde yayımlandı. |
| Microsoft. Devices. DeviceConnected | Bir cihaz IoT Hub 'ına bağlandığında yayımlandı. |
| Microsoft. Devices. DeviceConnected bağlantısı kesildi | Bir cihazın IoT Hub 'ından bağlantısı kesildiğinde yayımlandı. |
| Microsoft. Devices. Devicetelemetri | Bir IoT Hub 'ına cihaz telemetri iletisi gönderildiğinde yayımlandı |

Her bir IoT Hub 'ından yayımlanacak olayları yapılandırmak için Azure portal ya da Azure CLı kullanın. Bir örnek için, [Logic Apps kullanarak öğreticiyi Azure IoT Hub olayları hakkında e-posta bildirimleri gönderme](../event-grid/publish-iot-hub-events-to-logic-apps.md)öğreticisini deneyin.

## <a name="event-schema"></a>Olay şeması

IoT Hub olaylar, cihaz yaşam döngünüzün değişikliklere yanıt vermek için gereken tüm bilgileri içerir. EventType özelliğinin **Microsoft. Devices**ile başlayacağını denetleyerek bir IoT Hub olayı tanımlayabilirsiniz. Event Grid olay özelliklerinin nasıl kullanılacağı hakkında daha fazla bilgi için, bkz. [Event Grid olay şeması](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Cihaza bağlı şema

Aşağıdaki örnekte, bir cihaz bağlantılı olayının şeması gösterilmektedir:

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>Cihaz telemetri şeması

Cihaz telemetri iletisi, kod [sistemi özelliklerinde](iot-hub-devguide-routing-query-syntax.md#system-properties) **UTF-8** olarak ayarlanan ContentType ve **Application/JSON** olarak ayarlanmış geçerli bir JSON biçiminde olmalıdır. Bu özelliklerin her ikisi de büyük/küçük harfe duyarlıdır. İçerik kodlaması ayarlanmamışsa, IoT Hub iletileri temel 64 kodlu biçimde yazar.

Event Grid, uç noktayı Event Grid olarak seçerek cihaz telemetri olaylarını zenginleştirebilirsiniz. Daha fazla bilgi için bkz. [Ileti zenginleştirme genel bakış](iot-hub-message-enrichments-overview.md).

Aşağıdaki örnekte bir cihaz telemetri olayının şeması gösterilmektedir:

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>Cihaz tarafından oluşturulan şema

Aşağıdaki örnekte, bir cihaz oluşturulan olayının şeması gösterilmektedir:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Her bir özelliğin ayrıntılı açıklaması için, bkz. [IoT Hub için olay şeması Azure Event Grid](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Olayları filtreleme

IoT Hub olay abonelikleri olayları, cihaz adı olan olay türüne, veri içeriğine ve konuya göre filtreleyebilir.

Event Grid olay türlerinde, konularla ve veri içeriklerinde [filtrelemeye](../event-grid/event-filtering.md) izin vermez. Event Grid aboneliğini oluştururken, seçili IoT olaylarına abone olmayı seçebilirsiniz. Event Grid iş (ön ek) **Ile başlayan** ve (son ek) **ile biten** konu filtreleri. Filtre bir `AND` işleci kullanır, bu nedenle hem önek hem de sonek ile eşleşen bir konuya sahip olaylar aboneye teslim edilir.

IoT olaylarının konusu şu biçimi kullanır:

```json
devices/{deviceId}
```

Event Grid Ayrıca, veri içeriği de dahil olmak üzere her bir olayın özniteliklerine filtrelemeye izin verir. Bu, telemetri iletisinin temel içeriğini hangi olayların dağıttığı seçmenizi sağlar. Örnekleri görüntülemek için lütfen [Gelişmiş filtreleme](../event-grid/event-filtering.md#advanced-filtering) bölümüne bakın. Telemetri ileti gövdesinde filtreleme için, kod [sistemi özelliklerinde](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)ContentType öğesini **Application/JSON** ve Contentenkodlamaya göre **UTF-8** olarak ayarlamanız gerekir. Bu özelliklerin her ikisi de büyük/küçük harfe duyarlıdır.

DeviceConnected, DeviceConnected, DeviceCreated ve DeviceDeleted gibi telemetri olmayan olaylar için, abonelik oluşturulurken Event Grid filtrelemesi kullanılabilir. Telemetri olayları için, Event Grid ' deki filtrelemeye ek olarak, kullanıcılar da ileti yönlendirme sorgusu üzerinden cihaz iksi, ileti özellikleri ve gövdesinde filtre uygulayabilir. 

Telemetri olaylarına Event Grid aracılığıyla abone olduğunuzda IoT Hub, veri kaynağı türü cihaz iletilerini Event Grid göndermek için varsayılan bir ileti yolu oluşturur. İleti yönlendirme hakkında daha fazla bilgi için bkz. [IoT Hub Message Routing](iot-hub-devguide-messages-d2c.md). Bu yol, portalda IoT Hub > Ileti yönlendirme altında görünür. Telemetri olayları için oluşturulan örnek aboneliklerin sayısından bağımsız olarak Event Grid yalnızca bir yol oluşturulur. Bu nedenle, farklı filtrelerle birkaç aboneliğe ihtiyacınız varsa, bu sorgularda veya işlecini aynı rotada kullanabilirsiniz. Yolun oluşturulması ve silinmesi, Event Grid aracılığıyla telemetri olaylarının abonelikle denetlenir. IoT Hub Ileti yönlendirmeyi kullanarak Event Grid için bir yol oluşturamaz veya silemezsiniz.

Telemetri verileri gönderilmeden önce iletileri filtrelemek için [yönlendirme sorgunuzu](iot-hub-devguide-routing-query-syntax.md)güncelleştirebilirsiniz. Yönlendirme sorgusunun ileti gövdesine yalnızca gövde JSON olduğunda uygulanabileceğini unutmayın. İleti [sistemi özelliklerinde](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)ContentType öğesini **Application/JSON** ve Contentenkodlamaya de **UTF-8** olarak ayarlamanız gerekir.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Cihaz bağlı ve cihaz bağlantısı kesilen olaylar için sınırlamalar

Cihaza bağlı ve cihaz bağlantısı kesilen olayları almak için, cihazınızın D2C bağlantısını veya C2D bağlantısını açmanız gerekir. Cihazınız MQTT protokolünü kullanıyorsa, IoT Hub C2D bağlantısını açık tutacaktır. AMQP için, [alma zaman uyumsuz API 'yi](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)çağırarak C2D bağlantısını açabilirsiniz.

Telemetri gönderiyorsanız D2C bağlantısı açıktır. 

Cihaz bağlantısı titreşiyorsa, bu, cihazın en sık bağlanıp bağlantısını kestiğinde, her bir bağlantı durumunu göndermeyecektir, ancak sonunda tutarlı olan *son* bağlantı durumunu yayımlayacağız. Örneğin, cihazınız başlangıçta bağlı durumdaysa, bağlantı birkaç saniye boyunca bağlantı titreşiyor ve sonra bağlı duruma geri gelir. İlk bağlantı durumuyla bu yana yeni cihaz bağlantısı durumu olayları yayınlanmayacak. 

IoT Hub kesintisi olması durumunda cihaz bağlantı durumunu kesinti durumunda olduğu anda yayımlayacağız. Bu kesinti sırasında cihazın bağlantısı kesilirse, cihaz bağlantısı kesilen olay 10 dakika içinde yayımlanır.

## <a name="tips-for-consuming-events"></a>Olayları tüketme ipuçları

IoT Hub olaylarını işleyen uygulamalar aşağıdaki önerilen yöntemleri izlemelidir:

* Birden çok abonelik olayları aynı olay işleyicisine yönlendirmek üzere yapılandırılabilir, bu nedenle olayların belirli bir kaynaktan olduğunu varsaymayın. Beklenen IoT Hub 'ından geldiğinden emin olmak için ileti konusunu her zaman denetleyin.

* Aldığınız tüm olayların, beklediği türler olduğunu varsaymayın. İletiyi işlemeden önce her zaman eventType ' i kontrol edin.

* İletiler bir gecikmeden veya bir gecikmeden sonra gelebilir. Nesneler hakkındaki bilgilerinizin, cihaz oluşturma veya cihaz silinen olayları için güncel olup olmadığını anlamak için ETag alanını kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [IoT Hub olaylar öğreticisini deneyin](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Cihazla bağlantılı ve bağlantısız olayları nasıl sıralayacağınızı öğrenin](iot-hub-how-to-order-connection-state-events.md)

* [Event Grid hakkında daha fazla bilgi edinin](../event-grid/overview.md)

* [Yönlendirme IoT Hub olayları ve iletileri arasındaki farkları karşılaştırın](iot-hub-event-grid-routing-comparison.md)

* [Azure haritalar kullanarak IoT uzamsal analizlerini uygulamak için IoT telemetri olaylarını nasıl kullanacağınızı öğrenin](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
