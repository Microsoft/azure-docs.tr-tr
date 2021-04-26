---
title: Redsıs için Azure önbelleği Event Grid kaynak olarak
description: Azure Event Grid ile Redsıs olayları için Azure önbelleği için sunulan özellikleri açıklar
ms.topic: conceptual
ms.date: 02/11/2021
author: curib
ms.author: cauribeg
ms.openlocfilehash: 1a2995bc9ef40cd4eab320ce1bb4c5faf61e0e6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371302"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Reda için Azure önbelleği Event Grid kaynak olarak

Bu makalede redin olayları için Azure önbelleği özellikleri ve şeması sağlanmaktadır. Olay şemalarına giriş için bkz. [Azure Event Grid olay şeması](event-schema.md). 

## <a name="available-event-types"></a>Kullanılabilir olay türleri
Bu olaylar, istemci Redsıs REST API 'Leri için Azure önbelleği çağırarak dışarı aktardığınızda, içeri aktardığında veya ölçekleyerek tetiklenir. Düzeltme eki uygulama Redsıs Update tarafından tetiklenir.

 |Olay adı |Description|
 |----------|-----------|
 |**Microsoft. cache. ExportRDBCompleted** |Önbellek verileri verildiğinde tetiklenir. |
 |**Microsoft. cache. ımportrdbcompleted** |Önbellek verileri içeri aktarıldığında tetiklenir. |
 |**Microsoft. cache. Patchıncompleted** |Düzeltme Eki tamamlandığında tetiklenir. |
 |**Microsoft. cache. ScalingCompleted** |Ölçeklendirme tamamlandığında tetiklenir. |

## <a name="example-event"></a>Örnek olay
Bir olay tetiklendiğinde, Event Grid hizmeti bu olayla ilgili verileri bu olay ile abone olacak şekilde gönderir. Bu bölüm, redin olayı için her bir Azure önbelleği için verilerin nasıl görüneceğine ilişkin bir örnek içerir.

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

### <a name="microsoftcachepatchingcompleted-event"></a>Microsoft. cache. PatchingCompleted olayı

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Microsoft. cache. ımportrdbcompleted olayı

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Microsoft. cache. ExportRDBCompleted olayı

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft. cache. ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)


### <a name="microsoftcachepatchingcompleted-event"></a>Microsoft. cache. PatchingCompleted olayı

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.PatchingCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "PatchingCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "PatchingCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Microsoft. cache. ımportrdbcompleted olayı

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ImportRDBCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ImportRDBCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ImportRDBCompleted",
    "eventTime": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Microsoft. cache. ExportRDBCompleted olayı

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ExportRDBCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ExportRDBCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ExportRDBCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft. cache. ScalingCompleted

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ScalingCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ScalingCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ScalingCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>Olay özellikleri

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `topic` | dize | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| `subject` | string | Olay konusunun yayımcı tarafından tanımlanan yolu. |
| `eventType` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| `eventTime` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `id` | string | Etkinliğin benzersiz tanımlayıcısı. |
| `data` | object | Redsıs olay verileri için Azure önbelleği. |
| `dataVersion` | string | Veri nesnesinin şema sürümü. Şema sürümünü yayımcı tanımlar. |
| `metadataVersion` | string | Olay meta verilerinin şema sürümü. Event Grid en üst düzey özelliklerin şemasını tanımlar. Event Grid bu değeri sağlar. |


# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)


Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `source` | dize | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| `subject` | string | Olay konusunun yayımcı tarafından tanımlanan yolu. |
| `type` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| `time` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `id` | string | Etkinliğin benzersiz tanımlayıcısı. |
| `data` | object | Redsıs olay verileri için Azure önbelleği. |
| `specversion` | string | CloudEvents şema belirtimi sürümü. |

---


Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `timestamp` | dize | Olayın gerçekleştiği zaman. |
| `name` | string | Olayın adı. |
| `status` | string | Etkinliğin durumu. Başarısız veya başarılı oldu. |

## <a name="quickstarts"></a>Hızlı Başlangıçlar

Redsıs olayları için Azure önbelleği 'ni denemek istiyorsanız, bu hızlı başlangıç makalelerinden herhangi birine bakın:

|Bu aracı kullanmak istiyorsanız:    |Şu makaleye bakın: |
|--|-|
|Azure portalı    |[Hızlı başlangıç: Azure portal ile Redsıs olayları için Azure önbelleğini Web uç noktasına yönlendirme](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Hızlı başlangıç: PowerShell ile Redsıs olayları için Azure önbelleğini Web uç noktasına yönlendirme](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|Azure CLI’si    |[Hızlı başlangıç: Azure CLı ile Redsıs olayları için Azure önbelleğini Web uç noktasına yönlendirme](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>Sonraki adımlar

* Azure Event Grid giriş için bkz. [Event Grid nedir?](overview.md)
* Azure Event Grid aboneliği oluşturma hakkında daha fazla bilgi için bkz. [Event Grid abonelik şeması](subscription-creation-schema.md).

