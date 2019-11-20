---
title: IOT hub'ın Azure Event Grid şeması | Microsoft Docs
description: Olay şeması biçimi ve IOT Hub'ının özellikler için başvuru sayfası
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: kgremban
ms.openlocfilehash: 4e96276a862844cea1d0800eafb952d4a0df97ab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076348"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>IOT hub'ın Azure Event Grid olay şeması

Bu makalede, Azure IOT Hub olaylarını şema ve özellikleri sağlar. Olay şemaları için bir giriş için bkz [Azure Event Grid olay şeması](event-schema.md). 

Örnek betikler ve öğreticiler listesi için bkz: [IOT Hub olay kaynağı](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Kullanılabilir olay türleri

Azure IOT Hub aşağıdaki olay türlerini gösterir:

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | IOT hub'a bir cihaz kaydedildiğinde yayımladı. |
| Microsoft.Devices.DeviceDeleted | Bir cihaz IOT hub'ından silindiğinde yayımladı. | 
| Microsoft.Devices.DeviceConnected | Bir cihaz IOT hub'a bağlandığında yayımladı. |
| Microsoft.Devices.DeviceDisconnected | Bir cihaz IOT hub'ından kesildiğinde yayımladı. | 
| Microsoft.Devices.DeviceTelemetry | Bir IOT hub'ına telemetri ileti gönderildiğinde yayımladı. |

Cihazın telemetri olayları hariç tüm cihaz olayları Event Grid tarafından desteklenen tüm bölgelerde genel kullanıma sunulmuştur. Cihazın telemetri olayı genel Önizleme aşamasındadır ve Doğu ABD, Batı ABD, Batı Avrupa dışındaki tüm bölgelerde kullanılabilir [Azure kamu](../azure-government/documentation-government-welcome.md), [Azure Çin 21Vianet](/azure/china/china-welcome), ve [Azure Almanya](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>Örnek olay

Şema DeviceConnected ve DeviceDisconnected olayları için aynı yapıya sahip. Bu örnek olay, bir cihaz IOT hub'a bağlı olduğunda gerçekleşen bir olay şeması gösterir:

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
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

Bir IOT Hub'ına telemetri olayı gönderildiğinde DeviceTelemetry olay tetiklenir. Bu olay için bir örnek şema aşağıda gösterilmiştir.

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

Şema DeviceCreated ve DeviceDeleted olayları için aynı yapıya sahip. Bu örnek olay, bir IOT hub'a bir cihaz kaydedildiğinde gerçekleşen bir olay şeması gösterir:

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
      "deviceEtag": "null",
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

### <a name="event-properties"></a>Olay Özellikleri

Tüm olaylar aynı üst düzey veri içerir: 

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| id | string | Olayın benzersiz tanımlayıcısı. |
| topic | string | Olay kaynağı tam kaynak yolu. Bu alan, yazılabilir değil. Event Grid, bu değeri sağlar. |
| subject | string | Yayımcı tarafından tanımlanan olay konu yolu. |
| eventType | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| eventTime | string | Olayın oluşturulduğu zamandan, sağlayıcının UTC saatini temel alan. |
| data | object | IOT Hub olay verileri.  |
| dataVersion | string | Veri nesnesinin şema sürümü. Yayımcı, şema sürümü tanımlar. |
| metadataVersion | string | Olay meta verilerinin şema sürümü. Event Grid, şemanın en üst düzey özellikleri tanımlar. Event Grid, bu değeri sağlar. |

Tüm IOT Hub olaylarını veri nesnesinin aşağıdaki özellikleri içerir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| hubName | string | IOT cihaz nerede oluşturulduğu veya silindiği Hub adı. |
| deviceId | string | Cihazın benzersiz tanımlayıcısı. Bu büyük küçük harfe duyarlı dize en fazla 128 karakter uzunluğunda olabilir ve ASCII 7 bit alfasayısal karakterlerin yanı sıra şu özel karakterleri destekler: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

Veri nesnesi, içeriğini, her olay yayımcısı için farklıdır. 

İçin **cihaz bağlı** ve **cihazın bağlantısı** IOT Hub olayları, veri nesnesi, aşağıdaki özellikleri içerir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| moduleId | string | Modülün benzersiz tanımlayıcısı. Çıkış Modülü cihazlar için yalnızca alandır. Bu büyük küçük harfe duyarlı dize en fazla 128 karakter uzunluğunda olabilir ve ASCII 7 bit alfasayısal karakterlerin yanı sıra şu özel karakterleri destekler: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | object | Cihaz bağlantı durumu olay bilgilerini
| sequenceNumber | string | Yardımcı olan bağlı cihaz veya cihaz sırasını belirten bir sayı olayları bağlantısı kesildi. En son olay, bir sıra numarası olacaktır, önceki olay daha yüksek. Bu sayı tarafından 1'den fazla değiştirebilir, ancak kesin olarak artmaktadır. Bkz: [sıra numarası kullanmayı](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

İçin **cihaz Telemetrisi** IOT Hub olay veri nesnesini içeren, CİHAZDAN buluta ileti [IOT hub ileti biçimi](../iot-hub/iot-hub-devguide-messages-construct.md) ve aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| body | string | Bir CİHAZDAN ileti içeriği. |
| properties | string | Uygulama özellikleri iletiye eklenen kullanıcı tanımlı dizelerdir. Bu alanlar isteğe bağlıdır. |
| system properties | string | [Sistem Özellikleri](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) içeriği ve iletilerin kaynak tanımlamanıza yardımcı olacaktır. Cihazın telemetri iletileriyle, JSON ve UTF-8'e ayarlayın ileti sistemi özelliklerinde contentEncoding contentType ile geçerli bir JSON biçiminde olmalıdır. Bu ayarlanmazsa, IOT Hub iletilerini taban 64 kodlanmış biçimde yazılacaktır.  |

İçin **cihaz oluşturulan** ve **cihaz silinmiş** IOT Hub olayları, veri nesnesi, aşağıdaki özellikleri içerir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| twin | object | Uygulama cihaz meta verilerini bir bulut gösterimiyse cihaz ikizinde hakkında bilgi sağlar. | 
| deviceId | string | Cihaz ikizi benzersiz tanımlayıcısı. | 
| etag | string | Cihaz ikizi güncelleştirmeleri tutarlılık sağlamaya yönelik bir doğrulayıcı. Her bir etag cihaz ikizi benzersiz olması garanti edilir. |  
| deviceEtag| string | Bir cihaz kayıt defterine güncelleştirmelerin tutarlılık sağlamaya yönelik bir doğrulayıcı. Her deviceEtag cihaz kayıt defteri başına benzersiz olması garanti edilir. |
| status | string | Olup cihaz ikizinde etkin veya devre dışı. | 
| statusUpdateTime | string | Son cihaz ikizi durumu ISO8601 zaman damgası güncelleştirin. |
| connectionState | string | Olup cihaz bağlı bağlantısı kesildi veya. | 
| lastActivityTime | string | Son Etkinlik ISO8601 zaman damgası. | 
| cloudToDeviceMessageCount | integer | Bu cihaza gönderilen cihaz iletileri buluta sayısı. | 
| authenticationType | string | Bu cihaz için kullanılan kimlik doğrulaması türü: ya da `SAS`, `SelfSigned`, veya `CertificateAuthority`. |
| X509Thumbprint | string | Parmak izi, x509 için benzersiz bir değer belirli bir sertifika bir sertifika deposunda bulmak için kullanılan sertifika. Parmak izi SHA1 algoritması kullanılarak dinamik olarak oluşturulur ve fiziksel olarak sertifika mevcut değil. | 
| primaryThumbprint | string | Birincil parmak izi x509 için sertifika. |
| secondaryThumbprint | string | İkincil parmak izi x509 için sertifika. | 
| version | integer | Bir her artırılır tamsayı saat cihaz ikizi güncelleştirilir. |
| desired | object | Yalnızca uygulama arka ucu tarafından yazılmış ve cihaz tarafından okunur özellikleri kısmı. | 
| reported | object | Yalnızca cihaz tarafından yazılmış ve uygulama arka ucu tarafından okunur özellikleri kısmı. |
| lastUpdated | string | ISO8601 zaman damgası son cihaz ikizi özelliğinin güncelleştirin. | 

## <a name="next-steps"></a>Sonraki adımlar

* Azure Event grid'e giriş için bkz [Event Grid nedir?](overview.md)
* IOT Hub ve Event Grid birlikte nasıl çalıştığı hakkında bilgi edinmek için [tetikleyici eylemlere Event Grid kullanarak IOT Hub olaylarına tepki](../iot-hub/iot-hub-event-grid.md).