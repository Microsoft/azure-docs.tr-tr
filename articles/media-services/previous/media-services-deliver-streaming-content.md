---
title: .NET kullanarak Azure Media Services içeriğini yayımlayın | Microsoft Docs
description: Akış URL 'SI oluşturmak için kullanılan bir bulucu oluşturmayı öğrenin. Kod örnekleri C# dilinde yazılır ve .NET için Media Services SDK kullanır.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a1a06c9a5fdac1082a57bee71cccad271a6aae81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015803"
---
# <a name="publish-media-services-content-using-net"></a>.NET kullanarak Media Services içeriği yayımlama

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Genel Bakış
Bir Ondemandstreaming Locator ve akış URL 'SI oluşturarak bir uyarlamalı bit hızı MP4 kümesini akışla aktarabilirsiniz. [Bir varlık kodlama](media-services-encode-asset.md) konusu, uyarlamalı bit hızı bir MP4 kümesinin nasıl kodlanacağını göstermektedir. 

> [!NOTE]
> İçeriğiniz şifrelendiyse, bir bulucu oluşturmadan önce varlık teslim ilkesini ( [Bu](media-services-dotnet-configure-asset-delivery-policy.md) konuda açıklandığı gibi) yapılandırın. 
> 
> 

Ayrıca, aşamalı olarak indirilebilen MP4 dosyalarını işaret eden URL 'Ler oluşturmak için bir OnDemand akış Bulucu de kullanabilirsiniz.  

Bu konuda, varlığınızı yayımlamak ve sorunsuz, MPEG DASH ve HLS akış URL 'Leri oluşturmak için bir Ondemandstreaming Locator oluşturma gösterilmektedir. Ayrıca, aşamalı indirme URL 'Lerinin oluşturulması için sık kullanılan öğesini gösterir. 

## <a name="create-an-ondemand-streaming-locator"></a>Bir Ondemandstreaming Locator oluşturma
OnDemand akış bulucusu oluşturmak ve URL 'Leri almak için aşağıdaki işlemleri yapmanız gerekir:

1. İçerik şifrelendiyse, bir erişim ilkesi tanımlayın.
2. Bir Ondemandstreaming Bulucu oluşturun.
3. Akışı planlıyorsanız, varlığın akış bildirim dosyasını (. ISM) alın. 
   
   Aşamalı olarak indirmeyi planlıyorsanız, varlık içindeki MP4 dosyalarının adlarını alın.  
4. Bildirim dosyasının veya MP4 dosyalarının URL 'Lerini oluşturun. 


>[!NOTE]
>Farklı AMS ilkeleri için sınır 1.000.000 ilkedir (örneğin, Bulucu ilkesi veya ContentKeyAuthorizationPolicy için). Aynı gün/erişim izinlerini her zaman kullanıyorsanız aynı ilke KIMLIĞINI kullanın. Örneğin, bir uzun süredir (karşıya yükleme olmayan ilkeler) yerinde kalması amaçlanan konum belirleyicilerinin ilkeleri. Daha fazla bilgi için [bu](media-services-dotnet-manage-entities.md#limit-access-policies) konu başlığına bakın.

### <a name="use-media-services-net-sdk"></a>.NET SDK Media Services kullanma
Akış URL 'Leri oluşturun 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

Çıktılar:

- Kesintisiz Akış Protokolü kullanılarak istemci akışı için bildirim URL 'SI: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`
- HLS protokolünü kullanarak istemci akışı bildiriminin URL 'SI: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`
- MPEG DASH protokolünü kullanarak istemci akışı bildiriminin URL 'SI: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`

> [!NOTE]
> Ayrıca içeriğinizi bir TLS bağlantısı üzerinden de akışla aktarabilirsiniz. Bu yaklaşımda, akış URL 'Lerinin HTTPS ile başlayıp başlamadığınızdan emin olun. Şu anda, AMS özel etki alanları ile TLS 'yi desteklemez.
> 
> 

Aşamalı indirme URL 'Leri oluşturma 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
Çıktılar:

- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4`

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Media Services .NET SDK uzantıları kullanın
Aşağıdaki kod, bir bulucu oluşturan ve uyarlamalı akış için Kesintisiz Akış, HLS ve MPEG-DASH URL 'Lerini oluşturan .NET SDK uzantıları yöntemlerini çağırır.
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
* [Varlıkları indir](media-services-deliver-asset-download.md)
* [Varlık teslim ilkesini yapılandırma](media-services-dotnet-configure-asset-delivery-policy.md)

