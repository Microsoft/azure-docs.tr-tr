---
title: Media Services veri başvurusunu izleme
description: Media Services izlerken gereken önemli başvuru malzemeleri
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 04/21/2021
ms.openlocfilehash: 3fd7b8013ec67d718f308ccd1b72a6f90012e02e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873064"
---
# <a name="monitoring-media-services-data-reference"></a>Media Services veri başvurusunu izleme

Bu makale Media Services izlemek için yararlı olan verileri içerir. Azure Izleyici 'de desteklenen tüm platform ölçümleri hakkında daha fazla bilgi için [Azure izleyici Ile desteklenen ölçümleri](../../../azure-monitor/essentials/metrics-supported.md)gözden geçirin.

## <a name="metrics"></a>Ölçümler

Ölçümler, değerin değiştirilip değişmediğini düzenli aralıklarla toplanır. Bunlar genelde örneklenebilir ve bir uyarı görece basit mantık ile hızlı bir şekilde tetiklenebilir.


Media Services aşağıdaki kaynaklar için izleme ölçümlerini destekler:

|Ölçüm türü | Kaynak sağlayıcısı/tür ad alanı<br/> ve bireysel ölçümlere bağlantı |
|-------|-----|
| Media Services genel | [Genel](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservices) |
| Canlı Etkinlikler | [Microsoft. Media/mediaservices/liveEvents](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservicesliveevents) 
| Akış Uç Noktaları | [Akış uç noktaları](/rest/api/media/streamingendpoints)ile Ilgili olan [Microsoft. Media/mediaservices/streamingEndpoints](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservicesstreamingendpoints)REST API. 


[Hesap kotalarını ve sınırlarını](../limits-quotas-constraints-reference.md)de gözden geçirmeniz gerekir.


## <a name="metric-dimensions"></a>Ölçüm boyutları

Ölçüm boyutları hakkında daha fazla bilgi için bkz. [çok boyutlu ölçümler](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Media Services 'in ölçüm boyutları şunlardır.  Bunlar, destekledikleri ölçümlere göre kendi kendine explantory.  Daha fazla bilgi için bkz. [ölçüm bağlantıları](#metrics) .   
- OutputFormat
- HttpStatusCode 
- ErrorCode 
- TrackName 

## <a name="resource-logs"></a>Kaynak günlükleri

Kaynak günlükleri bir Azure kaynağının çalışması hakkında zengin ve sık veriler sağlar. Daha fazla bilgi için bkz. [Azure kaynaklarınızdan günlük verilerini toplama ve kullanma](../../../azure-monitor/essentials/platform-logs-overview.md).

Media Services şu kaynak günlüklerini destekler: [Microsoft. Media/mediaservices](/azure/azure-monitor/essentials/resource-logs-categories#microsoftmediamediaservices)

## <a name="schemas"></a>Şemalar

Üst düzey tanılama günlükleri şemasının ayrıntılı açıklaması için bkz. [Azure tanılama günlükleri Için desteklenen hizmetler, şemalar ve Kategoriler](../../../azure-monitor/essentials/resource-logs-schema.md).

## <a name="key-delivery-log-schema-properties"></a>Anahtar teslim günlüğü şema özellikleri

Bu özellikler, anahtar teslim günlüğü şemasına özeldir.

|Ad|Açıklama|
|---|---|
|keyId|İstenen anahtarın KIMLIĞI.|
|Anahtar|Şu değerlerden biri olabilir: "Clear" (şifreleme yok), "FairPlay", "PlayReady" veya "Widevine".|
|policyName|İlkenin Azure Resource Manager adı.|
|Belirteç|Belirteç türü.|
|statusMessage|Durum iletisi.|

### <a name="example"></a>Örnek

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

>[!NOTE]
> Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
