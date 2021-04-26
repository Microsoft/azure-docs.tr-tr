---
title: Media Services .NET SDK ile varlıkları ve Ilgili varlıkları yönetme
description: .NET için Media Services SDK ile varlıkları ve ilgili varlıkları yönetmeyi öğrenin.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b859ff49edb495be15cdf76719c3aaaf1ac72a6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013780"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Media Services .NET SDK ile varlıkları ve Ilgili varlıkları yönetme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Bu konuda .NET ile Azure Media Services varlıkların nasıl yönetileceği gösterilmektedir.

1 Nisan 2017’den itibaren, hesabınızdaki 90 günden eski olan tüm İş kayıtları, toplam kayıt sayısı üst kota sınırının altında olsa bile ilişkili Görev kayıtlarıyla birlikte otomatik olarak silinecektir. Örneğin, 1 Nisan 2017 ' de hesabınızda 31 Aralık 2016 ' den eski olan Iş kayıtları otomatik olarak silinir. İş/görev bilgilerini arşivlemek gerekirse, bu konuda açıklanan kodu kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Geliştirme ortamınızı kurun ve app.config dosyanızı [.NET ile Media Services geliştirme](media-services-dotnet-how-to-use.md) bölümünde açıklandığı gibi bağlantı bilgileriyle doldurun. 

## <a name="get-an-asset-reference"></a>Varlık başvurusu al
Sık kullanılan bir görev, Media Services var olan bir varlığa başvuru almak için kullanılır. Aşağıdaki kod örneğinde, bir varlık kimliğine bağlı olarak sunucu bağlamı nesnesindeki varlıklar koleksiyonundan nasıl varlık başvurusu alabileceğiniz gösterilmektedir. Aşağıdaki kod örneği, varolan bir ıvarlık nesnesine başvuru almak için bir LINQ sorgusu kullanır.

```csharp
    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }
```

## <a name="list-all-assets"></a>Tüm varlıkları Listele
Depolamadaki varlıkların sayısı büyüdükçe, varlıklarınızı listelemek yararlı olur. Aşağıdaki kod örneği, sunucu bağlamı nesnesi üzerindeki varlıklar koleksiyonunun nasıl yineleneceğini göstermektedir. Her varlık ile, kod örneği Ayrıca bazı özellik değerlerini konsola yazar. Örneğin, her varlık birçok medya dosyası içerebilir. Kod örneği her varlıkla ilişkili tüm dosyaları yazar.

```csharp
    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="get-a-job-reference"></a>Iş başvurusu al

Media Services koddaki işleme görevleriyle çalışırken, genellikle bir kimliğe göre var olan bir işe başvuru almanız gerekir. Aşağıdaki kod örneği, Işler koleksiyonundan bir ıjob nesnesine nasıl başvuru alınacağını göstermektedir.

Uzun süre çalışan bir kodlama işi başlatırken bir iş başvurusu almanız ve iş parçacığı üzerinde iş durumunu denetlemeniz gerekir. Bu gibi durumlarda, yöntemi bir iş parçacığından döndüğünde, bir işe yenilenmiş bir başvuru almanız gerekir.

```csharp
    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }
```

## <a name="list-jobs-and-assets"></a>Işleri ve varlıkları listeleme
Önemli bir ilgili görev, varlıkları Media Services ilişkili işleri ile listeme. Aşağıdaki kod örneği her bir ıjob nesnesini nasıl listeleneceğini gösterir ve ardından her iş için iş, tüm ilgili görevler, tüm giriş varlıkları ve tüm çıkış varlıkları hakkında özellikleri görüntüler. Bu örnekteki kod, diğer birçok görev için yararlı olabilir. Örneğin, daha önce çalıştırdığınız bir veya daha fazla kodlama öğesinden çıkış varlıklarını listelemek istiyorsanız, bu kod çıkış varlıklarına nasıl erişegösterdiğini gösterir. Bir çıkış varlığına başvurunuz varsa, bu içeriği indirerek veya URL 'Ler sağlayarak diğer kullanıcılara veya uygulamalara gönderebilirsiniz. 

Varlık sunma seçenekleri hakkında daha fazla bilgi için bkz. [.NET için MEDIA SERVICES SDK Ile varlık sunma](media-services-deliver-streaming-content.md).

```csharp
    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="list-all-access-policies"></a>Tüm erişim Ilkelerini Listele
Media Services, bir varlık veya dosya üzerinde bir erişim ilkesi tanımlayabilirsiniz. Erişim ilkesi, bir dosya veya varlık için izinleri (erişim türü ve süre) tanımlar. Media Services kodunuzda, genellikle bir IAccessPolicy nesnesi oluşturup mevcut bir varlıkla ilişkilendirerek bir erişim ilkesi tanımlarsınız. Daha sonra, Media Services varlıklar için doğrudan erişim sağlamanıza olanak tanıyan bir ıdıcator nesnesi oluşturun. Bu belge serisine eşlik eden Visual Studio projesi, varlıklara erişim ilkeleri ve Konumlandırıcı oluşturma ve atamayı gösteren çeşitli kod örnekleri içerir.

Aşağıdaki kod örneği, sunucusunda tüm erişim ilkelerinin nasıl ekleneceğini ve her biriyle ilişkili izinlerin türünü gösterir. Erişim ilkelerini görüntülemenin diğer bir yolu da, sunucudaki tüm ıvcator nesnelerini listeleyerek her bir bulucu için, AccessPolicy özelliğini kullanarak ilişkili erişim ilkesini listeleyebilirsiniz.

```csharp
    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
```
    
## <a name="limit-access-policies"></a>Erişim Ilkelerini sınırlandırma 

>[!NOTE]
> Farklı AMS ilkeleri için sınır 1.000.000 ilkedir (örneğin, Bulucu ilkesi veya ContentKeyAuthorizationPolicy için). Uzun süre boyunca kullanılmak için oluşturulan bulucu ilkeleri gibi aynı günleri / erişim izinlerini sürekli olarak kullanıyorsanız, aynı ilke kimliğini kullanmalısınız (karşıya yükleme olmayan ilkeler için). 

Örneğin, uygulamanızda yalnızca bir kez çalıştırılacak olan aşağıdaki kodla genel bir ilke kümesi oluşturabilirsiniz. Daha sonra kullanmak üzere kimlikleri günlük dosyasına kaydedebilirsiniz:

```csharp
    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);
```

Daha sonra, kodunuzda şu şekilde mevcut kimlikleri kullanabilirsiniz:

```csharp
    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());
```

## <a name="list-all-locators"></a>Tüm Konumlandırıcı listesini Listele
Bulucu, bir varlığa erişmek için doğrudan yol sağlayan bir URL 'dir ve bu varlık, bulucunun ilişkili erişim ilkesi tarafından tanımlanan şekilde varlık izinleridir. Her varlık, onun Konumlandırıcı özelliğinde onunla ilişkili bir ıvcator nesneleri koleksiyonuna sahip olabilir. Sunucu bağlamı da tüm Konumlandırıcı 'yı içeren bir Konum Belirleyicisi koleksiyonuna sahiptir.

Aşağıdaki kod örneği, sunucudaki tüm konum belirleyicilerinin listesini görüntüler. Her bulucu için, ilgili varlık ve erişim ilkesinin kimliğini gösterir. Ayrıca, izin türünü, sona erme tarihini ve varlığın tam yolunu görüntüler.

Bir varlığa yönelik bir bulucu yolunun yalnızca varlığın temel URL olduğunu unutmayın. Bir kullanıcının veya uygulamanın gözatmasına yönelik tek tek dosyaların doğrudan yolunu oluşturmak için, kodunuzun konum belirleyici yoluna belirli dosya yolunu eklemesi gerekir. Bunun nasıl yapılacağı hakkında daha fazla bilgi için bkz. [.NET için MEDIA SERVICES SDK Ile varlıkları sunma](media-services-deliver-streaming-content.md)konusu.

```csharp
    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }
```

## <a name="enumerating-through-large-collections-of-entities"></a>Büyük varlık koleksiyonlarını sıralama
Varlıkları sorgularken, genel REST v2 sorgu sonuçlarını 1000 sonuçla sınırladığından, tek seferde döndürülen 1000 varlıkların bir sınırı vardır. Büyük varlık koleksiyonlarında sıralama yaparken atla ve Al ' ı kullanmanız gerekir. 

Aşağıdaki işlev, belirtilen Media Services hesabındaki tüm işler için döngü yapılır. Media Services Işler koleksiyonunda 1000 iş döndürür. İşlevi, tüm işlerin numaralandırılmasını sağlamak için atla ve Al ' ın kullanımını sağlar (hesabınızda 1000 taneden fazla iş olması durumunda).

```csharp
    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
```

## <a name="delete-an-asset"></a>Bir varlığı silme
Aşağıdaki örnekte bir varlık silinir.

```csharp
    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }
```

## <a name="delete-a-job"></a>Işi silme
Bir işi silmek için, durum özelliğinde belirtilen şekilde işin durumunu denetlemeniz gerekir. Tamamlanmış veya iptal edilen işler silinebilir, sıraya alınmış, zamanlanmış veya işleme gibi bazı diğer durumlarda gerçekleştirilen işler önce iptal edilmeli, sonra da silinebilirler.

Aşağıdaki kod örneği, iş durumlarını denetleyerek ve sonra durum tamamlandığında veya iptal edildiğinde silerek bir işi silmeye yönelik bir yöntemi gösterir. Bu kod, bir işe başvuru almak için bu konunun önceki bölümüne bağımlıdır: iş başvurusu alma.

```csharp
    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }
```


## <a name="delete-an-access-policy"></a>Erişim Ilkesini silme
Aşağıdaki kod örneği, ilke kimliğine bağlı olarak bir erişim ilkesi başvurusunun nasıl alınacağını gösterir ve ardından ilkeyi siler.

```csharp
    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }
```


## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
