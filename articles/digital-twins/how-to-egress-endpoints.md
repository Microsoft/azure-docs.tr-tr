---
title: Çıkış ve uç noktalar-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS 'de olay uç noktaları oluşturmayı ve çıkış yapmayı öğrenin.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 33e8a6a281fbc6620a4608c7b0821b196043423e
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010064"
---
# <a name="egress-and-endpoints-in-azure-digital-twins"></a>Azure dijital TWINS 'teki çıkış ve uç noktalar

Azure dijital TWINS *uç noktaları* , bir kullanıcının Azure aboneliğindeki bir iletiyi veya olay aracısını temsil eder. Olaylar ve iletiler Azure Event Hubs, Azure Event Grid ve Azure Service Bus konularına gönderilebilir.

Olaylar, önceden tanımlanmış yönlendirme tercihlerine göre uç noktalara yönlendirilir. Kullanıcılar, her uç noktanın alabileceği *olay türlerini* belirtir.

Olaylar, Yönlendirme ve olay türleri hakkında daha fazla bilgi edinmek için bkz. [Azure dijital TWINS 'de yönlendirme olayları ve iletileri](./concepts-events-routing.md).

## <a name="events"></a>Olaylar

Olaylar, Azure ileti ve olay aracıları tarafından işlenmek üzere IoT nesneleri (cihazlar ve algılayıcılar gibi) tarafından gönderilir. Olaylar aşağıdaki [Azure Event Grid olay şeması başvurusu](../event-grid/event-schema.md)tarafından tanımlanır.

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Öznitelik | Tür | Açıklama |
| --- | --- | --- |
| id | string | Etkinliğin benzersiz tanımlayıcısı. |
| subject | string | Olay konusunun yayımcı tanımlı yolu. |
| data | object | Kaynak sağlayıcısına özel olay verileri. |
| eventType | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| eventTime | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| dataVersion | string | Veri nesnesinin şema sürümü. Yayımcı, şema sürümünü tanımlar. |
| metadataVersion | string | Olay meta verilerinin şema sürümü. Event Grid üst düzey özelliklerin şemasını tanımlar. Event Grid bu değeri sağlar. |
| konu başlığı | string | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |

Event Grid olay şeması hakkında daha fazla bilgi için:

- [Azure Event Grid olay şeması başvurusunu](../event-grid/event-schema.md)gözden geçirin.
- [Azure EventGrid Node. js SDK EventGridEvent başvurusunu](https://docs.microsoft.com/javascript/api/@azure/eventgrid/eventgridevent?view=azure-node-latest)okuyun.

## <a name="event-types"></a>Olay türleri

Olay türleri olayın yapısını sınıflandırır ve **EventType** alanında ayarlanır. Kullanılabilir olay türleri aşağıdaki liste tarafından verilmiştir:

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Her olay türü için olay biçimleri aşağıdaki alt bölümlerde daha ayrıntılı olarak açıklanmıştır.

### <a name="topologyoperation"></a>TopologyOperation

**Topologyoperation** , grafik değişiklikleri için geçerlidir. **Subject** özelliği, etkilenen nesne türünü belirtir. Aşağıdaki nesne türleri bu olayı tetikleyebilir:

- Cihaz
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- Anahtar deposu
- Rapor
- RoleDefinition
- Algılayıcısı
- SensorBlobMetadata
- SensorExtendedProperty
- Uzay
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- Spaceroleatama
- Sistem
- Kullanıcı
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Örnek

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Değer | Şununla değiştir |
| --- | --- |
| YOUR_TOPIC_NAME | Özelleştirilmiş konunun adı |

### <a name="udfcustom"></a>UdfCustom

**Udfcustom** , Kullanıcı tanımlı bir Işlev (UDF) tarafından gönderilen bir olaydır.
  
> [!IMPORTANT]  
> Bu olay UDF 'den açıkça gönderilmesi gerekir.

#### <a name="example"></a>Örnek

```JSON
{
  "id": "568fd394-380b-46fa-925a-ebb96f658cce",
  "subject": "UdfCustom",
  "data": {
    "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "ResourceType": "Space",
    "Payload": "\"Room is not available or air quality is poor\"",
    "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
  },
  "eventType": "UdfCustom",
  "eventTime": "2018-10-02T06:50:15.198Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Değer | Şununla değiştir |
| --- | --- |
| YOUR_TOPIC_NAME | Özelleştirilmiş konunun adı |

### <a name="sensorchange"></a>SensorChange

**Sensorchange** , telemetri değişikliklerine bağlı olarak bir sensörin durumunun güncelleştirmesidir.

#### <a name="example"></a>Örnek

```JSON
{
  "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
  "subject": "SensorChange",
  "data": {
    "Type": "Classic",
    "DataType": "Motion",
    "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "Value": "False",
    "PreviousValue": "True",
    "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
    "MessageType": "sensor",
    "Properties": {
      "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
      "ms-activity-id": "ct22YwXEGJ5u.605.0"
    }
  },
  "eventType": "SensorChange",
  "eventTime": "2018-04-17T17:46:18.5452993Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Değer | Şununla değiştir |
| --- | --- |
| YOUR_TOPIC_NAME | Özelleştirilmiş konunun adı |

### <a name="spacechange"></a>SpaceChange

**Spacechange** , telemetri değişikliklerine göre bir alanın durumunun bir güncelleştirmesidir.

#### <a name="example"></a>Örnek

```JSON
{
  "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
  "subject": "SpaceChange",
  "data": {
    "Type": null,
    "DataType": "AvailableAndFresh",
    "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "Value": "Room is not available or air quality is poor",
    "PreviousValue": null,
    "RawData": null,
    "transactionId": null,
    "EventTimestamp": null,
    "MessageType": null,
    "Properties": null,
    "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
  },
  "eventType": "SpaceChange",
  "eventTime": "2018-10-02T06:50:20.128Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Değer | Şununla değiştir |
| --- | --- |
| YOUR_TOPIC_NAME | Özelleştirilmiş konunun adı |

### <a name="devicemessage"></a>DeviceMessage

**Devicemessage**kullanarak, ham telemetri olaylarının Azure dijital TWINS 'ten de yönlendirilebilmesi Için bir **EventHub** bağlantısı belirtebilirsiniz.

> [!NOTE]
> - **Devicemessage** yalnızca **EventHub**ile combinable. **Devicemessage** ' i diğer olay türlerinden herhangi biriyle birleştiremezsiniz.
> - **EventHub** veya **devicemessage**türü birleşiminin yalnızca bir uç noktasını belirtebilirsiniz.

## <a name="configure-endpoints"></a>Uç noktaları yapılandırma

Uç nokta yönetimi uç noktalar API 'SI aracılığıyla yapılır.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Aşağıdaki örneklerde desteklenen uç noktaların nasıl yapılandırılacağı gösterilmektedir.

>[!IMPORTANT]
> **EventTypes** özniteliğinde dikkatli bir dikkat ödeyin. Hangi olay türlerinin bitiş noktası tarafından işleneceğini tanımlar ve bu nedenle yönlendirmeyi belirler.

Kimliği doğrulanmış bir HTTP POST isteği şu şekilde yapılır:

```plaintext
YOUR_MANAGEMENT_API_URL/endpoints
```

- Service Bus olay türlerine yönlendirme **Sensorchange**, **Spacechange**ve **topologyoperation**:

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Değer | Şununla değiştir |
    | --- | --- |
    | YOUR_NAMESPACE | Uç noktanızın ad alanı |
    | YOUR_PRIMARY_KEY | Kimlik doğrulaması için kullanılan birincil bağlantı dizesi |
    | YOUR_SECONDARY_KEY | Kimlik doğrulaması için kullanılan ikincil bağlantı dizesi |
    | YOUR_TOPIC_NAME | Özelleştirilmiş konunun adı |

- Event Grid olay türlerine yönlendirme **Sensorchange**, **Spacechange**ve **topologyoperation**:

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Değer | Şununla değiştir |
    | --- | --- |
    | YOUR_PRIMARY_KEY | Kimlik doğrulaması için kullanılan birincil bağlantı dizesi|
    | YOUR_SECONDARY_KEY | Kimlik doğrulaması için kullanılan ikincil bağlantı dizesi |
    | YOUR_TOPIC_NAME | Özelleştirilmiş konunun adı |

- Event Hubs olay türlerine yönlendirme **Sensorchange**, **Spacechange**ve **topologyoperation**:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Değer | Şununla değiştir |
    | --- | --- |
    | YOUR_NAMESPACE | Uç noktanızın ad alanı |
    | YOUR_PRIMARY_KEY | Kimlik doğrulaması için kullanılan birincil bağlantı dizesi |
    | YOUR_SECONDARY_KEY | Kimlik doğrulaması için kullanılan ikincil bağlantı dizesi |
    | YOUR_EVENT_HUB_NAME | Olay Hub 'ınızın adı |

- Event Hubs olay türü **Devicemessage**'ye yönlendir. **ConnectionString** 'e `EntityPath` eklenmesi zorunludur:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Değer | Şununla değiştir |
    | --- | --- |
    | YOUR_NAMESPACE | Uç noktanızın ad alanı |
    | YOUR_PRIMARY_KEY | Kimlik doğrulaması için kullanılan birincil bağlantı dizesi |
    | YOUR_SECONDARY_KEY | Kimlik doğrulaması için kullanılan ikincil bağlantı dizesi |
    | YOUR_EVENT_HUB_NAME | Olay Hub 'ınızın adı |

> [!NOTE]  
> Yeni bir uç nokta oluşturulduktan sonra, uç noktada olayların alınmasına başlaması 5 ila 10 dakika kadar sürebilir.

## <a name="primary-and-secondary-connection-keys"></a>Birincil ve ikincil bağlantı anahtarları

Birincil bağlantı anahtarı yetkisiz hale geldiğinde, sistem otomatik olarak ikincil bağlantı anahtarını dener. Bu, bir yedekleme sağlar ve uç noktalar API 'SI aracılığıyla birincil anahtarı düzgün şekilde kimlik doğrulaması ve güncelleştirme olasılığının olmasını sağlar.

Birincil ve ikincil bağlantı anahtarlarının her ikisi de yetkisiz olursa, sistem en fazla 30 dakikalık bir üstel geri dönme bekleme süresi girer. Olaylar, tetiklenen her geri dönme saatinde bırakılır.

Sistem bir geri alma bekleme durumunda olduğunda, bağlantı anahtarlarının uç noktalar API 'SI aracılığıyla güncelleştirilmesi 30 dakika kadar sürebilir.

## <a name="unreachable-endpoints"></a>Erişilemeyen uç noktalar

Bir uç nokta ulaşılamaz hale geldiğinde, sistem en fazla 30 dakikalık bir üstel geri dönme bekleme süresi girer. Olaylar, tetiklenen her geri dönme saatinde bırakılır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure dijital TWINS Swagger kullanmayı](how-to-use-swagger.md)öğrenin.

- Azure dijital TWINS 'de [olayları ve iletileri yönlendirme](concepts-events-routing.md) hakkında daha fazla bilgi edinin.
