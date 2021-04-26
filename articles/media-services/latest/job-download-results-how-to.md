---
title: Bir işin sonuçlarını indir-Azure Media Services
description: Bu makalede bir işin sonuçlarının nasıl indirileceği gösterilmektedir.
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
ms.openlocfilehash: 5c59ef009a2f71e09d9c02dc51017518f8b99da5
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275643"
---
# <a name="download-the-results-of-a-job"></a>Bir işin sonuçlarını indir

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services, videolarınızı işlerken (örneğin, kodlama veya çözümleme), [işinizin](transform-jobs-concept.md)sonucunu depolamak için bir çıktı [varlığı](assets-concept.md) oluşturmanız gerekir. Daha sonra bu sonuçları medya hizmeti ve depolama API 'Leri kullanarak yerel bir klasöre indirebilirsiniz. 

Bu makalede, Java ve .NET SDK 'Ları kullanılarak sonuçların nasıl indirileceği gösterilmektedir.

## <a name="java"></a>Java

```java
/**
    * Use Media Service and Storage APIs to download the output files to a local folder
    * @param manager               The entry point of Azure Media resource management
    * @param resourceGroup         The name of the resource group within the Azure subscription
    * @param accountName           The Media Services account name
    * @param assetName             The asset name
    * @param outputFolder          The output folder for downloaded files.
    * @throws StorageException
    * @throws URISyntaxException
    * @throws IOException
    */
private static void downloadResults(MediaManager manager, String resourceGroup, String accountName,
        String assetName, File outputFolder) throws StorageException, URISyntaxException, IOException {
    ListContainerSasInput parameters = new ListContainerSasInput()
        .withPermissions(AssetContainerPermission.READ)
        .withExpiryTime(DateTime.now().plusHours(1));
    AssetContainerSas assetContainerSas = manager.assets()
        .listContainerSasAsync(resourceGroup, accountName, assetName, parameters).toBlocking().first();
    
    String strSas = assetContainerSas.assetContainerSasUrls().get(0);
    CloudBlobContainer container = new CloudBlobContainer(new URI(strSas));

    File directory = new File(outputFolder, assetName);
    directory.mkdir();

    ArrayList<ListBlobItem>  blobs = container.listBlobsSegmented(null, true, EnumSet.noneOf(BlobListingDetails.class), 200, null, null, null).getResults();

    for (ListBlobItem blobItem: blobs) {
        if (blobItem instanceof CloudBlockBlob) {
            CloudBlockBlob blob = (CloudBlockBlob)blobItem;
            File downloadTo = new File(directory, blob.getName());

            blob.downloadToFile(downloadTo.getPath());
        }
    }

    System.out.println("Download complete.");
}
```

Tam kod örneğine bakın: [EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESPredefinedPreset/src/main/java/sample/EncodingWithMESPredefinedPreset.java)

## <a name="net"></a>.NET

```csharp
/// <summary>
/// Use Media Service and Storage APIs to download the output files to a local folder
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName">The Media Services account name.</param>
/// <param name="assetName">The asset name.</param>
/// <param name="resultsFolder">The output folder name for downloaded files.</param>
/// <returns>A task.</returns>
private async static Task DownloadResults(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName, string resultsFolder)
{
    AssetContainerSas assetContainerSas = client.Assets.ListContainerSas(
                    resourceGroupName, 
                    accountName, 
                    assetName,
                    permissions: AssetContainerPermission.Read, 
                    expiryTime: DateTime.UtcNow.AddHours(1).ToUniversalTime()
                    );

    Uri containerSasUrl = new Uri(assetContainerSas.AssetContainerSasUrls.FirstOrDefault());
    CloudBlobContainer container = new CloudBlobContainer(containerSasUrl);

    string directory = Path.Combine(resultsFolder, assetName);
    Directory.CreateDirectory(directory);

    Console.WriteLine("Downloading results to {0}.", directory);
    
    var blobs = container.ListBlobsSegmentedAsync(null,true, BlobListingDetails.None,200,null,null,null).Result;
    
    foreach (var blobItem in blobs.Results)
    {
        if (blobItem is CloudBlockBlob)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            string filename = Path.Combine(directory, blob.Name);

            await blob.DownloadToFileAsync(filename, FileMode.Create);
        }
    }

    Console.WriteLine("Download complete.");
}
```

Tam kod örneğine bakın: [EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/VideoEncoding/EncodingWithMESPredefinedPreset/Program.cs)

## <a name="next-steps"></a>Sonraki adımlar

[BIR https URL 'sinden iş girişi oluşturun](job-input-from-http-how-to.md).
