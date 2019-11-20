---
title: Tanılama günlükleri şemaları Azure Media Services-Azure
description: Bu makalede Azure Media Services tanılama günlüğü şemaları gösterilmektedir.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: f95258368664aabeb89426afb83854378c0e4429
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261092"
---
# <a name="diagnostic-logs-schemas"></a>Tanılama günlükleri şemaları

[Azure izleyici](../../azure-monitor/overview.md) , uygulamalarınızın nasıl çalıştığını anlamanıza yardımcı olan ölçümleri ve tanılama günlüklerini izlemenize olanak sağlar. Media Services tanılama günlüklerini izleyebilir, toplanan ölçümler ve Günlükler için uyarılar ve bildirimler oluşturabilirsiniz. [Azure depolama](https://azure.microsoft.com/services/storage/)'ya Günlükler gönderebilir, bunları [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)akışa alabilir ve [Log Analytics](https://azure.microsoft.com/services/log-analytics/)ya da üçüncü taraf hizmetleri kullanabilirsiniz.

Ayrıntılı bilgi için bkz. [Azure Izleyici ölçümleri](../../azure-monitor/platform/data-platform.md) ve [Azure izleyici tanılama günlükleri](../../azure-monitor/platform/resource-logs-overview.md).

Bu makalede tanılama günlükleri şemaları Media Services açıklanmaktadır.

## <a name="top-level-diagnostic-logs-schema"></a>Üst düzey tanılama günlükleri şeması

Üst düzey tanılama günlükleri şemasının ayrıntılı açıklaması için bkz. [Azure tanılama günlükleri Için desteklenen hizmetler, şemalar ve Kategoriler](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Anahtar teslim günlüğü şeması

### <a name="properties"></a>properties

Bu özellikler, anahtar teslim günlüğü şemasına özeldir.

|Name|Açıklama|
|---|---|
|keyId|İstenen anahtarın KIMLIĞI.|
|Anahtar|Aşağıdaki değerlerden biri olabilir: "Clear" (şifreleme yok), "FairPlay", "PlayReady" veya "Widevine".|
|PolicyName|İlkenin Azure Resource Manager adı.|
|Belirteç|Belirteç türü.|
|statusMessage|Durum iletisi.|

### <a name="examples"></a>Örnekler

Anahtar teslim istekleri şemasının özellikleri.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="next-steps"></a>Sonraki adımlar

[Media Services ölçümleri ve tanılama günlüklerini izleme](media-services-metrics-diagnostic-logs.md)
