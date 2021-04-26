---
title: Azure depolama 'dan büyük miktarda rastgele verileri indirin | Microsoft Docs
description: Azure Depolama hesabından büyük miktarda rastgele verileri indirmek için Azure SDK’nın nasıl kullanılacağını öğrenin
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c029abd87e1b819cc4d96e906be8824c019f433
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584982"
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Azure Depolama’dan büyük miktarda rastgele verileri indirme

Bu öğretici, bir serinin üçüncü bölümüdür. Bu öğretici, Azure Depolama’dan büyük miktarda verileri nasıl indireceğinizi gösterir.

Serinin üçüncü bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Uygulamayı güncelleştirme
> * Uygulamayı çalıştırma
> * Bağlantı sayısını doğrulama

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için önceki şu Depolama öğreticisini tamamlamış olmanız gerekir: [Büyük miktarda rastgele verileri paralel şekilde Azure Depolama’ya yükleme][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Sanal makinenize uzaktan bağlanma

 Sanal makine ile bir uzak masaüstü oturumu oluşturmak için yerel makinenizde aşağıdaki komutu kullanın. IP adresini, sanal makinenizin publicIPAddress değeriyle değiştirin. İstendiğinde, sanal makine oluşturulurken kullanılan kimlik bilgilerini girin.

```console
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Uygulamayı güncelleştirme

Önceki öğreticide, yalnızca depolama hesabına dosyaları yüklediniz. `D:\git\storage-dotnet-perf-scale-app\Program.cs` dosyasını bir metin düzenleyicisinde açın. `Main` yöntemini aşağıdaki örnekle değiştirin. Bu örnek, karşıya yükleme görevini açıklama satırı yapar ve tamamlandığında indirme görevinin ve depolama hesabındaki içeriği silme görevinin açıklamasını kaldırır.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to 
    // ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage 
    // client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        // await UploadFilesAsync();

        // Uncomment the following line to enable downloading of files from the storage account.
        // This is commented out initially to support the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        await DownloadFilesAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.
        // This is commented out initially as the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        // has you upload only for one tutorial and download for the other.
        if (!exception)
        {
            // await DeleteExistingContainersAsync();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

Uygulama güncelleştirildikten sonra uygulamayı tekrar derlemeniz gerekir. Bir `Command Prompt` açın ve `D:\git\storage-dotnet-perf-scale-app` dizinine gidin. Aşağıdaki örnekte görüldüğü gibi `dotnet build` çalıştırarak uygulamayı yeniden derleyin:

```console
dotnet build
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Şimdi uygulama yeniden derlendiğine göre güncelleştirilmiş kod ile uygulamayı çalıştırmaya sıra gelir. Açık değilse bir `Command Prompt` açın ve `D:\git\storage-dotnet-perf-scale-app` dizinine gidin.

Uygulamayı çalıştırmak için `dotnet run` yazın.

```console
dotnet run
```

Aşağıdaki örnekte `DownloadFilesAsync` görevi gösterilmektedir:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Uygulama, **storageconnectionstring** içinde belirtilen depolama hesabında bulunan kapsayıcıları okur. [Getbloblar](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) metodunu kullanarak bloblarda dolaşır ve [downloadtoasync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) yöntemini kullanarak bunları yerel makineye indirir.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_DownloadFilesAsync":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Uygulama, **storageconnectionstring** içinde belirtilen depolama hesabında bulunan kapsayıcıları okur. Kapsayıcılarda [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync) yöntemini kullanarak Blobları 10 ' da yineler ve [Downloadtofileasync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) yöntemini kullanarak yerel makineye indirir.

Aşağıdaki tabloda, indirilen her blob için tanımlanan [Blobrequestoptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) gösterilmektedir.

|Özellik|Değer|Açıklama|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Bu özellik, karşıya yüklenen içeriğin MD5 karmasının denetimini devre dışı bırakır. MD5 doğrulaması devre dışı bırakıldığında daha hızlı bir aktarım üretilir. Ancak aktarılan dosyaların geçerliliği veya bütünlüğü onaylanmaz. |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| yanlış| Bu özellik, bir MD5 karmasının hesaplanıp hesaplanmayacağını ve depolanıp depolanmayacağını belirler.   |

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestOptions on the download, including disabling MD5 
    // hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.
    // Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container, 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

---

### <a name="validate-the-connections"></a>Bağlantıları doğrulama

Dosyalar indirilirken, depolama hesabınıza yönelik eş zamanlı bağlantı sayısını doğrulayabilirsiniz. Bir konsol penceresi açın ve yazın `netstat -a | find /c "blob:https"` . Bu komut şu anda açık olan bağlantı sayısını gösterir. Aşağıdaki örnekte görebileceğiniz gibi, depolama hesabından dosya indirilirken 280 ' den fazla bağlantı açıktır.

```console
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Sonraki adımlar

Serinin üçüncü kısmında, bir depolama hesabından büyük miktarlarda veri indirmeyi öğrendiniz ve aşağıdakiler de dahil olmak üzere:

> [!div class="checklist"]
> * Uygulamayı çalıştırma
> * Bağlantı sayısını doğrulama

Portalda üretilen iş ve gecikme süresi ölçümlerini doğrulamak için serinin dördüncü kısmına gidin.

> [!div class="nextstepaction"]
> [Portalda aktarım hızı ve gecikme süresi ölçümlerini doğrulama](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md
