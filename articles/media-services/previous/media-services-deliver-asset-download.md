---
title: Media Services varlıkları bilgisayarınıza indirme-Azure | Microsoft Docs
description: Varlıkları bilgisayarınıza indirme hakkında bilgi edinin. Kod örnekleri C# dilinde yazılır ve .NET için Media Services SDK kullanır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 60f91e97a9bce1427b4ed8d251fe297d9eb7d969
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016670"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Nasıl yapılır: indirme yoluyla varlık sunma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Bu makalede, Media Services yüklenen medya varlıklarını sunmaya yönelik seçenekler açıklanmaktadır. Birçok uygulama senaryosunda Media Services içeriği sunabilirsiniz. Kodlama sonrasında, üretilen medya varlıklarını indirin veya bir akış Bulucu kullanarak bunlara erişin. Daha iyi performans ve ölçeklenebilirlik sağlamak için, Content Delivery Network (CDN) kullanarak da içerik sağlayabilirsiniz.

Bu örnekte, Media Services ' den yerel bilgisayarınıza medya varlıklarının nasıl indirileceği gösterilmektedir. Kod, iş KIMLIĞINE göre Media Services hesabıyla ilişkili işleri sorgular ve **Outputmediavarlıklarının** koleksiyonuna erişir (bir işi çalıştırmanın sonucu olan bir veya daha fazla çıktı medya varlığı kümesidir). Bu örnek, bir işten çıkış medya varlıklarının nasıl indirileceği gösterir, ancak diğer varlıkları indirmek için aynı yaklaşımı uygulayabilir.

>[!NOTE]
>Farklı AMS ilkeleri için sınır 1.000.000 ilkedir (örneğin, Bulucu ilkesi veya ContentKeyAuthorizationPolicy için). Aynı gün/erişim izinlerini (örneğin, uzun bir süre (karşıya yükleme olmayan ilkeler) yerinde kalması amaçlanan konum belirleyicilerinin ilkeleri gibi her zaman kullandığınız ilke KIMLIĞINI kullanın. Daha fazla bilgi için [Bu](media-services-dotnet-manage-entities.md#limit-access-policies) makaleye bakın.

```csharp
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }
```


## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ayrıca Bkz.
[Akış içeriği sunma](media-services-deliver-streaming-content.md)

