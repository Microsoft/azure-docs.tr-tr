---
title: Akış uç noktalarını yönetme
description: Bu makalede, Azure Media Services v3 ile akış uç noktalarının nasıl yönetileceği gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 2b442edc537ec64b12df215a18ab017ee47becff
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791742"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Media Services v3 ile akış uç noktalarını yönetme

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services hesabınız oluşturulduğunda hesabınıza **durdurulmuş** durumda bir **varsayılan** [akış uç noktası](stream-streaming-endpoint-concept.md) eklenir. İçeriğinizi akışa almak ve [dinamik paketleme](encode-dynamic-packaging-concept.md) ile [dinamik şifrelemeden](drm-content-protection-concept.md)yararlanmak için içerik akışı yapmak Istediğiniz akış uç noktasının **çalışıyor** durumda olması gerekir.

Bu makalede, farklı teknolojiler kullanarak akış uç noktanıza [Başlangıç](/rest/api/media/streamingendpoints/start) komutunun nasıl yürütüleceği gösterilmektedir. 
 
> [!NOTE]
> Yalnızca akış uç noktanız çalışır durumdaysa faturalandırılırsınız.
    
## <a name="prerequisites"></a>Önkoşullar

İncelemeyi 

* [Media Services kavramlar](concepts-overview.md)
* [Akış uç noktası kavramı](stream-streaming-endpoint-concept.md)
* [Dinamik paketleme](encode-dynamic-packaging-concept.md)

## <a name="use-rest"></a>REST kullanma

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Daha fazla bilgi için bkz. 

* [Streammingendpoint](/rest/api/media/streamingendpoints/start) başvuru belgelerini başlatın.
* Akış uç noktası başlatıldığında zaman uyumsuz bir işlemdir. 

    Uzun süre çalışan işlemlerin nasıl izleneceği hakkında bilgi için bkz. [uzun süreli işlemler](media-services-apis-overview.md).
* Bu [Postman koleksiyonu](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) , akış uç noktasının nasıl başlatılacağı hakkında daha fazla Rest işlemi örnekleri içerir.

## <a name="use-the-azure-portal"></a>Azure portalını kullanma 
 
1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Azure Media Services hesabınıza gidin.
1. Sol bölmede,  **akış uç noktaları**' nı seçin.
1. Başlatmak istediğiniz akış uç noktasını seçin ve ardından **Başlat**' ı seçin.

## <a name="use-the-azure-cli"></a>Azure CLI kullanma

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Daha fazla bilgi için bkz. [az AMS streaming-Endpoint start](/cli/azure/ams/streaming-endpoint#az_ams_streaming_endpointstart).

## <a name="use-sdks"></a>SDK’ları kullanma

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Bkz. [Java kod örneği](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

[.NET kod örneğine](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Streaming/StreamHLSAndDASH/Program.cs#L112)ilişkin tüm örnekleri inceleyin.

---

## <a name="next-steps"></a>Sonraki adımlar

* [Media Services v3 Openapı belirtimi (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Akış uç noktası işlemleri](/rest/api/media/streamingendpoints)
