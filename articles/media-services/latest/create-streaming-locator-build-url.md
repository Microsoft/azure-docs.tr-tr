---
title: Akış bulucusu ve derleme URL’leri oluşturma
description: Bu makalede, akış bulucu ve derleme URL 'Lerinin nasıl oluşturulduğu gösterilir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 8a192c3c3accf115b1a1db8e32a56d6d936078be
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277668"
---
# <a name="create-a-streaming-locator-and-build-urls"></a>Akış bulucusu ve derleme URL’leri oluşturma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services, akış URL 'SI oluşturmak için önce bir [akış Bulucu](stream-streaming-locators-concept.md)oluşturmanız gerekir. Daha sonra [akış uç noktası](/rest/api/media/streamingendpoints) ana bilgisayar adını ve **akış Bulucu** yolunu birleştirebilirsiniz. Bu örnekte, *varsayılan* **akış uç noktası** kullanılır. İlk olarak bir Media Service hesabı oluşturduğunuzda, bu *varsayılan* **akış uç noktası** durdurulmuş durumda olur, bu nedenle akışı başlatmak için **Start** 'ı çağırmanız gerekir.

Bu makalede, bir akış Bulucu oluşturma ve Java ve .NET SDK 'Ları kullanarak akış URL 'SI oluşturma işlemlerinin nasıl yapılacağı gösterilir.

## <a name="prerequisite"></a>Önkoşul

[Dinamik paketleme](encode-dynamic-packaging-concept.md) önizlemesi

## <a name="java"></a>Java

```java
/**
* Creates a StreamingLocator for the specified asset and with the specified streaming policy name.
* Once the StreamingLocator is created the output asset is available to clients for playback.
* @param manager       The entry point of Azure Media resource management
* @param resourceGroup The name of the resource group within the Azure subscription
* @param accountName   The Media Services account name
* @param assetName     The name of the output asset
* @param locatorName   The StreamingLocator name (unique in this case)
* @return              The locator created
*/
private static StreamingLocator getStreamingLocator(MediaManager manager, String resourceGroup, String accountName,
    String assetName, String locatorName) {
    // Note that we are using one of the PredefinedStreamingPolicies which tell the Origin component
    // of Azure Media Services how to publish the content for streaming.
    System.out.println("Creating a streaming locator...");
    StreamingLocator locator = manager
        .streamingLocators().define(locatorName)
        .withExistingMediaservice(resourceGroup, accountName)
        .withAssetName(assetName)
        .withStreamingPolicyName("Predefined_ClearStreamingOnly")
        .create();

    return locator;
}

/**
* Checks if the streaming endpoint is in the running state, if not, starts it.
* @param manager       The entry point of Azure Media resource management
* @param resourceGroup The name of the resource group within the Azure subscription
* @param accountName   The Media Services account name
* @param locatorName   The name of the StreamingLocator that was created
* @param streamingEndpoint     The streaming endpoint.
* @return              List of streaming urls
*/
private static List<String> getStreamingUrls(MediaManager manager, String resourceGroup, String accountName,
    String locatorName, StreamingEndpoint streamingEndpoint) {
    List<String> streamingUrls = new ArrayList<>();

    ListPathsResponse paths = manager.streamingLocators().listPathsAsync(resourceGroup, accountName, locatorName)
        .toBlocking().first();
    
    for (StreamingPath path: paths.streamingPaths()) {
        StringBuilder uriBuilder = new StringBuilder();
        uriBuilder.append("https://")
            .append(streamingEndpoint.hostName())
            .append("/")
            .append(path.paths().get(0));

        streamingUrls.add(uriBuilder.toString());
    }
    return streamingUrls;
}
```

Tam kod örneğine bakın: [EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESPredefinedPreset/src/main/java/sample/EncodingWithMESPredefinedPreset.java)

## <a name="net"></a>.NET

```csharp
/// <summary>
/// Creates a StreamingLocator for the specified asset and with the specified streaming policy name.
/// Once the StreamingLocator is created the output asset is available to clients for playback.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="assetName">The name of the output asset.</param>
/// <param name="locatorName">The StreamingLocator name (unique in this case).</param>
/// <returns>A task.</returns>
private static async Task<StreamingLocator> CreateStreamingLocatorAsync(
    IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string assetName,
    string locatorName)
{
    Console.WriteLine("Creating a streaming locator...");
    StreamingLocator locator = await client.StreamingLocators.CreateAsync(
        resourceGroup,
        accountName,
        locatorName,
        new StreamingLocator
        {
            AssetName = assetName,
            StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
        });

    return locator;
}

/// <summary>
/// Checks if the streaming endpoint is in the running state,
/// if not, starts it. Then, builds the streaming URLs.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="locatorName">The name of the StreamingLocator that was created.</param>
/// <param name="streamingEndpoint">The streaming endpoint.</param>
/// <returns>A task.</returns>
private static async Task<IList<string>> GetStreamingUrlsAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    String locatorName,
    StreamingEndpoint streamingEndpoint)
{
    IList<string> streamingUrls = new List<string>();

    ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

    foreach (StreamingPath path in paths.StreamingPaths)
    {
        UriBuilder uriBuilder = new UriBuilder
        {
            Scheme = "https",
            Host = streamingEndpoint.HostName,

            Path = path.Paths[0]
        };
        streamingUrls.Add(uriBuilder.ToString());
    }

    return streamingUrls;
}
```

Tam kod örneğine bakın: [EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/VideoEncoding/EncodingWithMESPredefinedPreset/Program.cs)

## <a name="see-also"></a>Ayrıca bkz.

* [.NET ile filtre oluşturma](filters-dynamic-manifest-dotnet-how-to.md)
* [REST ile filtre oluşturma](filters-dynamic-manifest-rest-howto.md)
* [CLı ile filtre oluşturma](filters-dynamic-manifest-cli-how-to.md)

## <a name="next-steps"></a>Sonraki adımlar

[DRM ile Içeriğinizi koruyun](drm-protect-with-drm-tutorial.md).
