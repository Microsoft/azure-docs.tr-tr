---
title: Büyük miktarlarda rastgele verileri Azure depolama 'ya paralel olarak yükleme
description: Azure depolama istemci kitaplığı 'nı kullanarak büyük miktarlarda rastgele verileri bir Azure depolama hesabına paralel olarak yükleme hakkında bilgi edinin
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: ed7020a58f3f15403108934bcc3fab644bd1b627
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584473"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Büyük miktarda rastgele verileri paralel şekilde Azure Depolama’ya yükleme

Bu öğretici, bir dizinin ikinci bölümüdür. Bu öğretici, büyük miktarda rastgele verileri Azure depolama hesabına yükleyen bir uygulamayı nasıl dağıtacağınızı gösterir.

Serinin ikinci bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Bağlantı dizesini yapılandırma
> * Uygulama oluşturma
> * Uygulamayı çalıştırma
> * Bağlantı sayısını doğrulama

Microsoft Azure Blob depolama, verilerinizi depolamak için ölçeklenebilir bir hizmet sağlar. Uygulamanızın mümkün olduğunca yüksek performanslı olmasını sağlamak için, blob depolamanın nasıl çalıştığının anlaşılması önerilir. Azure Blob sınırları hakkında daha fazla bilgi edinmek için bu sınırlar hakkında daha fazla bilgi için bkz. [BLOB depolama Için ölçeklenebilirlik ve performans hedefleri](../blobs/scalability-targets.md).

Blob kullanan yüksek performanslı bir uygulama tasarlarken [bölüm adlandırma](../blobs/storage-performance-checklist.md#partitioning) , potansiyel olarak önemli bir faktördür. 4 MIB 'den büyük veya buna eşit blok boyutları için [yüksek aktarım hızı blok Blobları](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) kullanılır ve bölüm adlandırma performansı etkilemez. 4 MiB 'den küçük blok boyutları için Azure depolama, ölçek ve yük dengelemek için Aralık tabanlı bölümleme şeması kullanır. Bu yapılandırma, benzer adlandırma kurallarına veya ön eklere sahip dosyaların aynı bölüme gideceği anlamına gelir. Bu mantık, dosyaların yüklendiği kapsayıcının adını içerir. Bu öğreticide, rastgele oluşturulan içerik ve adlar için GUID’e sahip olan dosyaları kullanırsınız. Bunlar daha sonra rastgele adlarla beş farklı kapsayıcıya yüklenir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için önceki şu Depolama öğreticisini tamamlamış olmanız gerekir: [Ölçeklenebilir uygulama için sanal makine ve depolama hesabı oluşturma][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Sanal makinenize uzaktan bağlanma

Sanal makine ile bir uzak masaüstü oturumu oluşturmak için yerel makinenizde aşağıdaki komutu kullanın. IP adresini, sanal makinenizin publicIPAddress değeriyle değiştirin. İstendiğinde, sanal makineyi oluştururken kullandığınız kimlik bilgilerini girin.

```console
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Bağlantı dizesini yapılandırma

Azure portalında depolama hesabınıza gidin. Depolama hesabınızdaki **Ayarlar** bölümünde **Erişim anahtarları**’nı seçin. Birincil veya ikincil anahtardaki **bağlantı dizesini** kopyalayın. Önceki öğreticide oluşturduğunuz sanal makinede oturum açın. Bir **Komut İstemini** yönetici olarak açın ve `/m` anahtarıyla `setx` komutunu çalıştırın. Bu komut bir makine ayarı ortam değişkenini kaydeder. Bu ortam değişkeni, **Komut İstemi** yeniden yükleninceye kadar kullanılamaz. **\<storageConnectionString\>** Aşağıdaki örnekte değiştirin:

```console
setx storageconnectionstring "<storageConnectionString>" /m
```

Tamamlandığında, başka bir **Komut İstemi** açın, `D:\git\storage-dotnet-perf-scale-app` sayfasına gidin ve `dotnet build` yazarak uygulamayı oluşturun.

## <a name="run-the-application"></a>Uygulamayı çalıştırma

`D:\git\storage-dotnet-perf-scale-app` sayfasına gidin.

Uygulamayı çalıştırmak için `dotnet run` yazın. `dotnet` ilk çalıştırıldığında, geri yükleme hızını artırmak ve çevrimdışı erişimi etkinleştirmek için yerel paket önbelleğinizi doldurur. Bu komutun tamamlanması bir dakika süre ve yalnızca bir kez gerçekleşir.

```console
dotnet run
```

Uygulama, beş adet rastgele adlandırılmış kapsayıcı oluşturur ve hazırlama dizinindeki dosyaları depolama hesabına yüklemeye başlar.

`UploadFilesAsync`Yöntemi aşağıdaki örnekte gösterilmiştir:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_UploadFilesAsync":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Çok sayıda eş zamanlı bağlantıya izin verildiğinden emin olmak için en düşük ve en fazla iş parçacığı sayısı 100 olarak ayarlanır.

```csharp
private static async Task UploadFilesAsync()
{
    // Create five randomly named containers to store the uploaded files.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();

    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // Path to the directory to upload
    string uploadPath = currentdir + "\\upload";

    // Start a timer to measure how long it takes to upload all the files.
    Stopwatch time = Stopwatch.StartNew();

    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);

        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections
        // are retried with a back off policy. As multiple large files are being uploaded using
        // large block sizes, this can cause an issue if an exponential retry policy is not defined.
        // Additionally, parallel operations are enabled with a thread count of 8.
        // This should be a multiple of the number of processor cores in the machine.
        // Lastly, MD5 hash validation is disabled for this example, improving the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };

        // Create a new instance of the SemaphoreSlim class to 
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set the block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;

            await sem.WaitAsync();

            // Create a task for each file to upload. The tasks are
            // added to a collection and all run asynchronously.
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));

            count++;
        }

        // Run all the tasks asynchronously.
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
İş parçacığı sayısı ve bağlantı sınırı ayarlarının belirlenmesine ek olarak, [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) yöntemi için [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) yapılandırılarak paralellik kullanılır ve MD5 karma doğrulaması devre dışı bırakılır. Dosyalar 100 mb’lık bloklar halinde karşıya yüklenir, bu yapılandırma daha iyi performans sağlar ancak düşük performanslı bir ağ kullanıldığında bir hata varmış gibi 100 mb’lık bloğun tamamı yeniden denendiğinden bu maliyetli olabilir.

|Özellik|Değer|Açıklama|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| Ayar, karşıya yükleme sırasında blobu bloklar halinde böler. En yüksek performans için bu değer, çekirdek sayısının sekiz katı olmalıdır. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Bu özellik, karşıya yüklenen içeriğin MD5 karmasının denetimini devre dışı bırakır. MD5 doğrulaması devre dışı bırakıldığında daha hızlı bir aktarım üretilir. Ancak aktarılan dosyaların geçerliliği veya bütünlüğü onaylanmaz.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| yanlış| Bu özellik, bir MD5 karmasının hesaplanıp dosyayla birlikte depolanıp depolanmayacağını belirler.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| En fazla 10 yeniden deneme ile 2 saniyelik geri alma |İsteklerin yeniden deneme ilkesini belirler. Bağlantı hataları yeniden denenir. Bu örnekte 2 saniyelik geri alma ve en fazla 10 yeniden deneme sayısı ile bir [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry) ilkesi yapılandırılmaktadır. Bu ayar, uygulamanız blob depolaması için ölçeklenebilirlik hedeflerine dönmek için yakın olduğunda önemlidir. Daha fazla bilgi için bkz. [BLOB depolama Için ölçeklenebilirlik ve performans hedefleri](../blobs/scalability-targets.md).  |

---

Aşağıdaki örnek, Windows sisteminde çalışan kesilmiş bir uygulama çıktısı örneğidir.

```console
Created container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Created container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Created container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Created container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Created container 92480da9-f695-4a42-abe8-fb35e71eb887
Iterating in directory: C:\git\myapp\upload
Found 5 file(s)
Uploading 1d596d16-f6de-4c4c-8058-50ebd8141e4d.pdf to container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Uploading 242ff392-78be-41fb-b9d4-aee8152a6279.pdf to container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Uploading 38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.pdf to container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Uploading 45930d63-b0d0-425f-a766-cda27ff00d32.pdf to container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Uploading 5129b385-5781-43be-8bac-e2fbb7d2bd82.pdf to container 92480da9-f695-4a42-abe8-fb35e71eb887
Uploaded 5 files in 16.9552163 seconds
```

### <a name="validate-the-connections"></a>Bağlantıları doğrulama

Dosyalar karşıya yüklenirken, depolama hesabınıza yönelik eş zamanlı bağlantı sayısını doğrulayabilirsiniz. Bir konsol penceresi açın ve yazın `netstat -a | find /c "blob:https"` . Bu komut şu anda açık olan bağlantı sayısını gösterir. Aşağıdaki örnekte görebileceğiniz gibi, depolama hesabına rastgele dosyalar yüklenirken 800 bağlantı açıktır. Karşıya yükleme çalıştırılırken bu değer değişir. Blok yığınlar paralel şekilde karşıya yüklenerek, içerikleri aktarmak için gereken süre büyük ölçüde azalır.

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
