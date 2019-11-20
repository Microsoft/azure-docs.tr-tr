---
title: Büyük miktarda rastgele verileri paralel şekilde Azure Depolama’ya yükleme | Microsoft Docs
description: Azure depolama istemci kitaplığı 'nı kullanarak büyük miktarlarda rastgele verileri bir Azure depolama hesabına paralel olarak yükleme hakkında bilgi edinin
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/08/2019
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 5b20686399db9537e5db8622a433b5e506939d19
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302988"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Büyük miktarda rastgele verileri paralel şekilde Azure Depolama’ya yükleme

Bu öğretici, bir dizinin ikinci bölümüdür. Bu öğretici, büyük miktarda rastgele verileri Azure depolama hesabına yükleyen bir uygulamayı nasıl dağıtacağınızı gösterir.

Serinin ikinci bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Bağlantı dizesini yapılandırma
> * Uygulama oluşturma
> * Uygulamayı çalıştırma
> * Bağlantı sayısını doğrulama

Azure blob depolama, verilerinizi depolamak için ölçeklenebilir bir hizmet sağlar. Uygulamanızın mümkün olduğunca yüksek performanslı olmasını sağlamak için, blob depolamanın nasıl çalıştığının anlaşılması önerilir. Azure bloblarının sınırlarının bilinmesi önemlidir. Bu sınırlar hakkında daha fazla bilgi edinmek için bkz. [blob depolama ölçeklenebilirlik hedefleri](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).

Blob kullanan yüksek performanslı bir uygulama tasarlarken [bölüm adlandırma](../blobs/storage-performance-checklist.md#partitioning) , potansiyel olarak önemli bir faktördür. 4 MIB 'den büyük veya buna eşit blok boyutları için [yüksek aktarım hızı blok Blobları](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) kullanılır ve bölüm adlandırma performansı etkilemez. 4 MiB 'den küçük blok boyutları için Azure depolama, ölçek ve yük dengelemek için Aralık tabanlı bölümleme şeması kullanır. Bu yapılandırma, benzer adlandırma kurallarına veya ön eklere sahip dosyaların aynı bölüme gideceği anlamına gelir. Bu mantık, dosyaların yüklendiği kapsayıcının adını içerir. Bu öğreticide, rastgele oluşturulan içerik ve adlar için GUID’e sahip olan dosyaları kullanırsınız. Bunlar daha sonra rastgele adlarla beş farklı kapsayıcıya yüklenir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için, önceki depolama öğreticisini tamamlamış olmanız gerekir: [ölçeklenebilir bir uygulama için sanal makine ve depolama hesabı oluşturma][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Sanal makinenize uzaktan bağlanma

Sanal makine ile bir uzak masaüstü oturumu oluşturmak için yerel makinenizde aşağıdaki komutu kullanın. IP adresini, sanal makinenizin publicIPAddress değeriyle değiştirin. İstendiğinde, sanal makineyi oluştururken kullandığınız kimlik bilgilerini girin.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Bağlantı dizesini yapılandırma

Azure portalında depolama hesabınıza gidin. Depolama hesabınızdaki **Ayarlar** bölümünde **Erişim anahtarları**’nı seçin. Birincil veya ikincil anahtardaki **bağlantı dizesini** kopyalayın. Önceki öğreticide oluşturduğunuz sanal makinede oturum açın. Bir **Komut İstemini** yönetici olarak açın ve `/m` anahtarıyla `setx` komutunu çalıştırın. Bu komut bir makine ayarı ortam değişkenini kaydeder. Bu ortam değişkeni, **Komut İstemi** yeniden yükleninceye kadar kullanılamaz. Aşağıdaki örnekte **\<storageConnectionString\>** ’i değiştirin:

```
setx storageconnectionstring "<storageConnectionString>" /m
```

Tamamlandığında, başka bir **Komut İstemi** açın, `D:\git\storage-dotnet-perf-scale-app` sayfasına gidin ve `dotnet build` yazarak uygulamayı oluşturun.

## <a name="run-the-application"></a>Uygulamayı çalıştırma

`D:\git\storage-dotnet-perf-scale-app` sayfasına gidin.

Uygulamayı çalıştırmak için `dotnet run` yazın. `dotnet` ilk çalıştırıldığında, geri yükleme hızını artırmak ve çevrimdışı erişimi etkinleştirmek için yerel paket önbelleğinizi doldurur. Bu komutun tamamlanması bir dakika süre ve yalnızca bir kez gerçekleşir.

```
dotnet run
```

Uygulama, beş adet rastgele adlandırılmış kapsayıcı oluşturur ve hazırlama dizinindeki dosyaları depolama hesabına yüklemeye başlar. Uygulama, çalışması sırasında çok sayıda eş zamanlı bağlantıya izin verilmesini sağlamak için en az iş parçacığı sayısını 100 olarak ve [DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) değerini de 100 olarak ayarlar.

İş parçacığı sayısı ve bağlantı sınırı ayarlarının belirlenmesine ek olarak, [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) yöntemi için [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) yapılandırılarak paralellik kullanılır ve MD5 karma doğrulaması devre dışı bırakılır. Dosyalar 100 mb’lık bloklar halinde karşıya yüklenir, bu yapılandırma daha iyi performans sağlar ancak düşük performanslı bir ağ kullanıldığında bir hata varmış gibi 100 mb’lık bloğun tamamı yeniden denendiğinden bu maliyetli olabilir.

|Özellik|Değer|Açıklama|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| Ayar, karşıya yükleme sırasında blobu bloklar halinde böler. En yüksek performans için bu değer, çekirdek sayısının sekiz katı olmalıdır. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| doğru| Bu özellik, karşıya yüklenen içeriğin MD5 karmasının denetimini devre dışı bırakır. MD5 doğrulaması devre dışı bırakıldığında daha hızlı bir aktarım üretilir. Ancak aktarılan dosyaların geçerliliği veya bütünlüğü onaylanmaz.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| yanlış| Bu özellik, bir MD5 karmasının hesaplanıp dosyayla birlikte depolanıp depolanmayacağını belirler.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| En fazla 10 yeniden deneme ile 2 saniyelik geri alma |İsteklerin yeniden deneme ilkesini belirler. Bağlantı hataları yeniden denenir. Bu örnekte 2 saniyelik geri alma ve en fazla 10 yeniden deneme sayısı ile bir [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry) ilkesi yapılandırılmaktadır. Uygulamanız, [blob depolama ölçeklenebilirlik hedeflerine](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets) yaklaştığında bu ayar önemlidir.  |

Aşağıdaki örnekte `UploadFilesAsync` görevi gösterilmektedir:

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

Aşağıdaki örnek, Windows sisteminde çalışan kesilmiş bir uygulama çıktısı örneğidir.

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>Bağlantıları doğrulama

Dosyalar karşıya yüklenirken, depolama hesabınıza yönelik eş zamanlı bağlantı sayısını doğrulayabilirsiniz. Bir **Komut İstemi** açın ve `netstat -a | find /c "blob:https"` yazın. Bu komut şu anda `netstat` kullanılarak açılan bağlantı sayısını gösterir. Aşağıdaki örnek, öğreticiyi kendiniz çalıştırırken gördüğünüze benzer bir çıktıyı gösterir. Örnekte görebileceğiniz gibi, rastgele dosyalar depolama hesabına yüklenirken 800 bağlantı açıktı. Karşıya yükleme çalıştırılırken bu değer değişir. Blok yığınlar paralel şekilde karşıya yüklenerek, içerikleri aktarmak için gereken süre büyük ölçüde azalır.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Sonraki adımlar

Serinin ikinci kısmında, büyük miktarlarda rastgele verileri paralel şekilde bir depolama hesabına yüklemeyle ilgili aşağıda örnekleri verilen işlemleri öğrendiniz:

> [!div class="checklist"]
> * Bağlantı dizesini yapılandırma
> * Uygulama oluşturma
> * Uygulamayı çalıştırma
> * Bağlantı sayısını doğrulama

Bir depolama hesabından büyük miktarlarda verileri indirmek için serinin üçüncü kısmına ilerleyin.

> [!div class="nextstepaction"]
> [Azure Depolama’dan büyük miktarda rastgele verileri indirme](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
