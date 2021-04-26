---
title: .NET kullanarak Media Services bir hesaba dosya yükleme | Microsoft Docs
description: .NET kullanarak varlık oluşturup karşıya yükleyerek Media Services medya içeriğini nasıl alabileceğinizi öğrenin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ee9bcc6861245ce40c43570126445a3613cd2744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013740"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>.NET kullanarak Media Services hesabına dosya yükleme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Media Services’de, dijital dosyalar bir varlığa yüklenir (veya alınır). Varlık **varlığı video** , ses, görüntüler, küçük resim koleksiyonları, metin parçaları ve kapalı açıklamalı alt yazı dosyaları (ve bu dosyalar hakkındaki meta veriler) içerebilir.  Dosyalar karşıya yüklendikten sonra, içeriğiniz daha fazla işlem ve akış için bulutta güvenli bir şekilde depolanır.

Varlık içindeki dosyalara **Varlık Dosyaları** adı verilir. **Assetfile** örneği ve gerçek medya dosyası iki ayrı nesne. Maldosya örneği medya dosyası hakkındaki meta verileri içerir, ancak medya dosyası gerçek medya içeriğini içerir.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Aşağıdaki noktalara dikkat edilmelidir:
 
 * Media Services, akış içeriği için URL 'Ler oluştururken IAssetFile.Name özelliğinin değerini kullanır (örneğin, http://{AMSAccount}. Origin. mediaservices. Windows. net/{GUID}/{ıassetfile. Name}/streamingParameters.) Bu nedenle, yüzde kodlamalı izin verilmez. **Name** özelliğinin değeri, Şu sayıda [kodlamaya ayrılan karakterlerden](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)herhangi birini içeremez:! * ' ();: @ &= + $,/?% # [] ". Ayrıca, dosya adı uzantısı için yalnızca bir '. ' olabilir.
* Adın uzunluğu 260 karakterden büyük olmamalıdır.
* Media Services ile işleme için desteklenen dosya boyutlarına yönelik üst sınır uygulanır. Dosya boyutu sınırlaması hakkında ayrıntılı bilgi için [bu](media-services-quotas-and-limitations.md) makaleye bakın.
* Farklı AMS ilkeleri için sınır 1.000.000 ilkedir (örneğin, Bulucu ilkesi veya ContentKeyAuthorizationPolicy için). Uzun süre boyunca kullanılmak için oluşturulan bulucu ilkeleri gibi aynı günleri / erişim izinlerini sürekli olarak kullanıyorsanız, aynı ilke kimliğini kullanmalısınız (karşıya yükleme olmayan ilkeler için). Daha fazla bilgi için [Bu](media-services-dotnet-manage-entities.md#limit-access-policies) makaleye bakın.

Varlıklar oluşturduğunuzda, aşağıdaki şifreleme seçeneklerini belirtebilirsiniz:

* **Hiçbiri**: Şifreleme kullanılmaz. Varsayılan değer budur. Bu seçeneği kullandığınızda, içeriğiniz aktarım sırasında veya depolamadaki bekleyen sırada korunmaz.
  Aşamalı indirme kullanarak bir MP4 teslim etmeyi planlıyorsanız, bu seçeneği kullanın: 
* **Commonencryption** -Common Encryption veya PlayReady DRM ile zaten şifrelenmiş ve korunan içeriği (örneğin, PlayReady DRM ile korunan kesintisiz akış) karşıya yüklüyorsanız bu seçeneği kullanın.
* **EnvelopeEncrypted** – HLS 'leri AES ile şifreli olarak karşıya yüklüyorsanız bu seçeneği kullanın. Dosyaların Transform Manager tarafından kodlanmış ve şifrelenmiş olması gerektiğini unutmayın.
* **Storageencryptıon** -açık içeriğinizi AES-256 bit şifrelemeyi kullanarak yerel olarak şifreler ve sonra geri kalanı şifreli olarak depolandığı Azure depolama 'ya yükler. Depolama Şifrelemesi ile korunan varlıklar, kodlamadan önce otomatik olarak şifrelenerek şifrelenmiş bir dosya sistemine yerleştirilir ve yeni bir çıktı varlığı şeklinde geri yüklenmeden önce isteğe bağlı olarak yeniden şifrelenir. Depolama Şifrelemesinin birincil kullanım nedeni, yüksek kaliteli girdi medya dosyalarınızın güvenliğini güçlü şifrelemeyle diskte bekleyen konumda sağlamak istediğiniz durumdur.
  
    Media Services, dijital hak Yöneticisi (DRM) gibi değil, varlıklarınız için disk üzerinde depolama şifrelemesi sağlar.
  
    Varlığınıza depolama şifrelemesi uygulanmışsa varlık teslim ilkesini yapılandırmanız gerekir. Daha fazla bilgi için bkz. [varlık teslim Ilkesini yapılandırma](media-services-dotnet-configure-asset-delivery-policy.md).

Varlığınızın **Commonşifrelenen** bir seçenekle veya bir **EnvelopeEncrypted** seçeneğiyle şifrelenmesini belirtirseniz, varlığınızı bir **contentkey** ile ilişkilendirmeniz gerekir. Daha fazla bilgi için bkz. [ContentKey oluşturma](media-services-dotnet-create-contentkey.md). 

Varlığınızın **Storageşifrelenen** bir seçenekle şifrelenmesini belirtirseniz, .net IÇIN Media Services SDK, varlığınız Için **storageşifrelenen** bir **contentkey** oluşturur.

Bu makalede, bir Media Services varlığına dosya yüklemek için .NET SDK 'sının yanı sıra Media Services .NET SDK uzantıları 'nın Media Services nasıl kullanılacağı gösterilmektedir.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Media Services .NET SDK ile tek bir dosyayı karşıya yükleme

Aşağıdaki kod, tek bir dosyayı karşıya yüklemek için .NET kullanır. AccessPolicy ve Locator, yükleme işlevi tarafından oluşturulur ve yok edilir. 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Media Services .NET SDK ile birden çok dosya yükleme
Aşağıdaki kod, bir varlık oluşturmayı ve birden çok dosyayı yüklemeyi gösterir.

Kod şunları yapar:

* Önceki adımda tanımlanan CreateEmptyAsset metodunu kullanarak boş bir varlık oluşturur.
* Varlığa erişim iznini ve erişim süresini tanımlayan bir **AccessPolicy** örneği oluşturur.
* Varlığa erişim sağlayan bir **Bulucu** örneği oluşturur.
* **Blobtransferclient** örneği oluşturur. Bu tür, Azure Blobları üzerinde çalışan bir istemciyi temsil eder. Bu örnekte, istemci karşıya yükleme ilerlemesini izler. 
* Belirtilen dizindeki dosyaları sıralar ve her dosya için bir **Assetfile** örneği oluşturur.
* **Uploadasync** yöntemini kullanarak dosyaları Media Services karşıya yükler. 

> [!NOTE]
> Çağrıların engellenmediğinden ve dosyaların paralel olarak karşıya yüklendiğinden emin olmak için UploadAsync yöntemini kullanın.
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AssetFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


Çok sayıda varlığı karşıya yüklerken aşağıdakileri göz önünde bulundurun:

* İş parçacığı başına yeni bir **Cloudmediacontext** nesnesi oluşturun. **Cloudmediacontext** sınıfı iş parçacığı açısından güvenli değil.
* 2 varsayılan değerinden Numberofconcurrentaktarımlarını 5 gibi daha yüksek bir değere yükseltin. Bu özellik ayarlandığında **Cloudmediacontext**'in tüm örnekleri etkilenir. 
* Varsayılan değer olan 10 ' da ParallelTransferThreadCount değerini saklayın.

## <a name="ingesting-assets-in-bulk-using-media-services-net-sdk"></a><a id="ingest_in_bulk"></a>Media Services .NET SDK kullanarak varlıkları toplu olarak kullanma
Büyük varlık dosyalarını karşıya yüklemek varlık oluşturma sırasında bir performans sorunu olabilir. Varlıkları toplu veya "toplu olarak almak" bölümünde, varlık oluşturma işleminin karşıya yükleme işleminden ayrılması gerekir. Toplu bir yaklaşımı kullanmak için, varlığı ve ilişkili dosyalarını açıklayan bir bildirim (IngestManifest) oluşturun. Ardından, ilişkili dosyaları bildirimin blob kapsayıcısına yüklemek için istediğiniz karşıya yükleme yöntemini kullanın. Microsoft Azure Media Services bildirimle ilişkili blob kapsayıcısını izler. Blob kapsayıcısına bir dosya yüklendikten sonra, Microsoft Azure Media Services bildirimdeki varlığın yapılandırmasına göre varlık oluşturmayı tamamlar (IngestManifestAsset).

Yeni bir IngestManifest oluşturmak için, CloudMediaContext üzerinde IngestManifests koleksiyonu tarafından kullanıma sunulan oluşturma yöntemini çağırın. Bu yöntem, sağladığınız bildirim adı ile yeni bir IngestManifest oluşturur.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Toplu IngestManifest ilişkili varlıkları oluşturun. Toplu ödeme için varlık üzerinde istenen şifreleme seçeneklerini yapılandırın.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

Bir IngestManifestAsset, toplu bir giriş için bir varlığı toplu IngestManifest ile ilişkilendirir. Ayrıca, her bir varlığı oluşturan Assetdosyalarını da ilişkilendirir. Bir IngestManifestAsset oluşturmak için sunucu bağlamında oluştur yöntemini kullanın.

Aşağıdaki örnek, daha önce oluşturulan iki varlığı toplu alma bildirimine ilişkilendiren iki yeni IngestManifestAssets eklemeyi gösterir. Her IngestManifestAsset, toplu giriş sırasında her bir varlık için karşıya yüklenen bir dosya kümesini de ilişkilendirir.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Varlık dosyalarını, IngestManifest 'ın **IIngestManifest. BlobStorageUriForUpload** özelliği tarafından belirtilen BLOB depolama kapsayıcısı URI 'sine karşıya yükleyebilen herhangi bir yüksek hızlı istemci uygulamasını kullanabilirsiniz. 

Aşağıdaki kod, .NET SDK 'nın varlık dosyalarını karşıya yüklemek için nasıl kullanılacağını gösterir.

```csharp
    static void UploadBlobFile(string containerName, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

Bu makalede kullanılan örnek için varlık dosyalarını karşıya yükleme kodu aşağıdaki kod örneğinde gösterilmektedir:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

**IngestManifest** istatistik özelliğini yoklayarak, bir **IngestManifest** ile ilişkili tüm varlıklar için toplu geri ödeme sürecini saptayabilirsiniz. İlerleme bilgilerini güncelleştirmek için, Istatistikler özelliğini her yoklamada yeni bir **Cloudmediacontext** kullanmanız gerekir.

Aşağıdaki örnek, bir IngestManifest tarafından **kimliğini** yoklayarak gösterir.

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>.NET SDK uzantılarını kullanarak karşıya dosya yükleme
Aşağıdaki örnekte, .NET SDK uzantıları kullanılarak tek bir dosyanın nasıl karşıya yükleneceği gösterilmektedir. Bu durumda **CreateFromFile** yöntemi kullanılır, ancak zaman uyumsuz sürüm de kullanılabilir (**Createfromfileasync**). **CreateFromFile** yöntemi, dosyanın karşıya yükleme ilerlemesini raporlamak için dosya adı, şifreleme seçeneği ve geri çağırma belirtmenize olanak tanır.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

Aşağıdaki örnek, UploadFile işlevini çağırır ve varlık oluşturma seçeneği olarak depolama şifrelemesini belirtir.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>Sonraki adımlar

Karşıya yüklenen varlıklarınızı artık kodlayabilirsiniz. Daha fazla bilgi için bkz. [Varlıkları kodlama](media-services-portal-encode.md).

Yapılandırılmış kapsayıcıya gelen dosyaya göre bir kodlama işi tetiklemek için Azure İşlevleri’ni de kullanabilirsiniz. Daha fazla bilgi için [bu örneğe](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ) bakın.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Sonraki adım
Artık Media Services bir varlık yüklediğinize göre, [medya Işlemcisi alma][How to Get a Media Processor] makalesine gidin.

[How to Get a Media Processor]: media-services-get-media-processor.md
