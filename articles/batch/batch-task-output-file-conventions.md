---
title: .NET için dosya kuralları kitaplığı ile Azure depolama 'da iş ve görev çıkışını kalıcı hale getirme-Azure Batch | Microsoft Docs
description: Batch görevi ve iş çıktısını Azure depolama 'ya kalıcı hale getirmek için Azure Batch dosya kuralları kitaplığı 'nı kullanmayı ve Azure portal kalıcı çıktıyı görüntülemeyi öğrenin.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a2970c46c7cbc978bf6d7491c9258dcccc5404bd
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302681"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>.NET için batch dosya kuralları kitaplığı ile iş ve görev verilerini Azure depolama 'ya kalıcı hale getirme

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Görev verilerini kalıcı hale getirmenin bir yolu, [.NET için Azure Batch dosya kuralları kitaplığını][nuget_package]kullanmaktır. Dosya kuralları kitaplığı, Görev çıkış verilerini Azure depolama 'ya depolama ve alma sürecini basitleştirir. Dosya kuralları kitaplığını hem görev hem de istemci kodundaki &mdash; ' ı kalıcı dosyalar için görev kodunda ve istemci kodunda listelemek ve almak için kullanabilirsiniz. Görev kodunuz Ayrıca, bir [görev bağımlılıkları](batch-task-dependencies.md) senaryosunda olduğu gibi yukarı akış görevlerinin çıkışını almak için kitaplığı da kullanabilir.

Dosya kuralları kitaplığı ile çıkış dosyalarını almak için, belirli bir iş veya görevin dosyalarını KIMLIĞE ve amaca göre listeleyerek bulabilirsiniz. Dosyaların adlarını veya konumlarını bilmeniz gerekmez. Örneğin, belirli bir görevin tüm ara dosyalarını listelemek veya belirli bir iş için bir önizleme dosyası almak üzere dosya kuralları kitaplığını kullanabilirsiniz.

> [!TIP]
> Sürüm 2017-05-01 ' den başlayarak Batch hizmeti API 'SI, sanal makine yapılandırmasıyla oluşturulan havuzlarda çalışan görevler ve iş Yöneticisi görevleri için Azure depolama 'ya kalıcı çıkış verilerini destekler. Batch hizmeti API 'SI, bir görevi oluşturan kod içinden çıktıyı kalıcı hale getirmenin ve dosya kuralları kitaplığına alternatif olarak hizmet veren basit bir yol sunar. Toplu Iş istemci uygulamalarınızı, görevin çalıştığı uygulamayı güncelleştirmeye gerek kalmadan çıktıyı kalıcı hale getirmek için değiştirebilirsiniz. Daha fazla bilgi için bkz. [Batch hizmeti API 'si Ile Azure depolama 'ya görev verilerini kalıcı hale](batch-task-output-files.md)getirme.

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Görev çıktısını kalıcı hale getirmek için dosya kuralları kitaplığını ne zaman kullanabilirim?

Azure Batch, görev çıkışını kalıcı hale getirmek için birden çok yol sağlar. Dosya kuralları bu senaryolara en uygun seçenektir:

- Dosya kuralları kitaplığını kullanarak dosyaları kalıcı hale getirmek için görevin çalıştırıldığı uygulamanın kodunu kolayca değiştirebilirsiniz.
- Görev çalışmaya devam ederken Azure Storage 'a veri akışı yapmak istiyorsunuz.
- Bulut hizmeti yapılandırması veya sanal makine yapılandırmasıyla oluşturulan havuzlardaki verileri kalıcı hale getirmek istiyorsunuz.
- İstemci uygulamanızın veya işteki diğer görevlerin, Görev çıkış dosyalarını KIMLIĞE veya amaca göre bulması ve indirmesi gerekir.
- Azure portal görev çıktısını görüntülemek istiyorsunuz.

Senaryolarınız yukarıda listelenenlerden farklıysa, farklı bir yaklaşım düşünmeniz gerekebilir. Kalıcı görev çıktısına yönelik diğer seçenekler hakkında daha fazla bilgi için bkz. [Azure Storage 'da iş ve görev çıkışını kalıcı hale](batch-task-output.md)getirme.

## <a name="what-is-the-batch-file-conventions-standard"></a>Toplu Iş dosyası kuralları standart nedir?

[Toplu Iş dosyası kuralları standardı](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) , çıkış dosyalarınızın yazıldığı hedef kapsayıcılar ve BLOB yolları için bir adlandırma şeması sağlar. Dosya kuralları standardına bağlı olan Azure depolama 'da kalıcı olan dosyalar, Azure portal görüntülenmek üzere otomatik olarak kullanılabilir. Portal, adlandırma kuralını biliyor ve bu nedenle kendisine bağlı dosyaları görüntüleyebilir.

.NET için dosya kuralları kitaplığı, depolama Kapsayıcılarınızı ve Görev çıkış dosyalarınızı dosya kuralları standardına göre otomatik olarak adlandırır. Dosya kuralları kitaplığı ayrıca iş KIMLIĞI, görev KIMLIĞI veya amaca göre Azure Storage 'daki çıktı dosyalarını sorgulamak için yöntemler sağlar.

.NET dışında bir dille geliştiriyorsanız, dosya kuralları standardını uygulamanızda kendiniz uygulayabilirsiniz. Daha fazla bilgi için bkz. [batch dosya kuralları standardını uygulama](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Bir Azure Depolama hesabını Batch hesabınıza bağlama

Dosya kuralları kitaplığını kullanarak çıkış verilerini Azure depolama 'ya kalıcı hale getirmek için, önce Batch hesabınıza bir Azure depolama hesabı bağlamanız gerekir. Daha önce yapmadıysanız, [Azure Portal](https://portal.azure.com)kullanarak Batch hesabınıza bir depolama hesabı bağlayın:

1. Azure portalında Batch hesabınıza gidin.
1. **Ayarlar**altında **depolama hesabı**' nı seçin.
1. Batch hesabınızla ilişkili bir depolama hesabınız yoksa **depolama hesabı (hiçbiri)** öğesine tıklayın.
1. Aboneliğiniz için listeden bir depolama hesabı seçin. En iyi performans için, görevlerinizin çalıştığı Batch hesabıyla aynı bölgede olan bir Azure depolama hesabı kullanın.

## <a name="persist-output-data"></a>Çıktı verilerini kalıcı yap

Dosya kuralları kitaplığı ile iş ve Görev çıkış verilerini kalıcı hale getirmek için Azure depolama 'da bir kapsayıcı oluşturun ve sonra çıktıyı kapsayıcıya kaydedin. Görev çıktısını kapsayıcıya yüklemek için görev kodunuzda [.net Için Azure Storage istemci kitaplığı](https://www.nuget.org/packages/WindowsAzure.Storage) 'nı kullanın.

Azure depolama 'da kapsayıcılar ve BLOB 'larla çalışma hakkında daha fazla bilgi için bkz. [.NET kullanarak Azure Blob depolamayı kullanmaya başlama](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Dosya kuralları kitaplığıyla kalıcı olan tüm iş ve görev çıkışları aynı kapsayıcıda depolanır. Çok sayıda görev dosyaları aynı anda kalıcı olarak denemeye çalışıyorsa, Azure depolama azaltma sınırları zorlanabilir. Azaltma limitleri hakkında daha fazla bilgi için bkz. [BLOB depolama Için performans ve ölçeklenebilirlik denetim listesi](../storage/blobs/storage-performance-checklist.md).

### <a name="create-storage-container"></a>Depolama kapsayıcısı oluşturma

Görev çıktısını Azure depolama 'ya kalıcı hale getirmek için, önce [Cloudjob][net_cloudjob]'u çağırarak bir kapsayıcı oluşturun. [Prepareoutputstorageasync][net_prepareoutputasync]. Bu genişletme yöntemi bir [Cloudstorageaccount][net_cloudstorageaccount] nesnesini parametre olarak alır. Dosya kuralları standardına göre adlı bir kapsayıcı oluşturur, böylece içerikleri Azure portal ve makalenin ilerleyen kısımlarında ele alınan alma yöntemleri tarafından bulunabilir.

Genellikle, istemci uygulamanızda bir kapsayıcı oluşturmak için kodu yerleştirebilirsiniz @no__t-havuzlarınızı, işlerinizi ve görevlerinizi oluşturan uygulama.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Mağaza görev çıkışları

Artık Azure depolama 'da bir kapsayıcı hazırladığınıza göre, görevler dosya kuralları kitaplığında bulunan [Taskoutputstorage][net_taskoutputstorage] sınıfını kullanarak çıktıyı kapsayıcıya kaydedebilir.

Görev kodunuzda, önce bir [taskoutputstorage][net_taskoutputstorage] nesnesi oluşturun, ardından görev işini tamamladığında, [taskoutputstorage][net_taskoutputstorage]' ı çağırın. Çıktıyı Azure depolama 'ya kaydetmek için [Saveasync][net_saveasync] yöntemi.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

[Taskoutputstorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage)'ın `kind` parametresi. [Saveasync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) yöntemi kalıcı dosyaları kategorilere ayırır. Önceden tanımlanmış dört [Taskoutputkind][net_taskoutputkind] türü vardır: `TaskOutput`, `TaskPreview`, `TaskLog` ve `TaskIntermediate.` özel çıkış kategorileri de tanımlayabilirsiniz.

Bu çıktı türleri, belirli bir görevin kalıcı çıkışları için toplu Işi daha sonra Sorgulayabileceğiniz zaman hangi tür çıkışları listebir belirtmenizi sağlar. Diğer bir deyişle, bir görevin çıkışlarını listelemeniz durumunda, listeyi çıkış türlerinden birindeki filtreleyebilirsiniz. Örneğin, "bana görev *109*için *Önizleme* çıkışı ver." Çıkışları listeleme ve alma hakkında daha fazla bilgi, makalenin sonraki kısımlarında çıktıyı alma ' da görünür.

> [!TIP]
> Çıktı türü Ayrıca, Azure portal belirli bir dosyanın nerede göründüğünü belirler: *Taskoutput*-kategorilere ayrılmış dosyalar **görev çıktı dosyaları**altında görünür ve **görev günlükleri**altında *tasklog* dosyaları görünür.

### <a name="store-job-outputs"></a>İş çıktılarını depola

Görev çıktılarını depolamanın yanı sıra, tüm işle ilişkili çıktıları da saklayabilirsiniz. Örneğin, bir film işleme işinin birleştirme görevinde, tam olarak işlenen filmi iş çıkışı olarak kalıcı hale getirebilmeniz gerekir. İşiniz tamamlandığında, istemci uygulamanız iş için çıktıları listeleyebilir ve alabilir ve ayrı görevleri sorgulamasına gerek kalmaz.

[Joi Putstorage][net_joboutputstorage]' ı çağırarak iş çıktısını saklayın. [Saveasync][net_joboutputstorage_saveasync] yöntemi ve [Joi putkind][net_joboutputkind] ve filename ' i belirtin:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Görev çıkışları için **Taskoutputkind** türünde olduğu gibi, bir işin kalıcı dosyalarını kategorilere ayırmak Için [Joi putkind][net_joboutputkind] türünü kullanırsınız. Bu parametre, belirli bir çıktı türünü daha sonra sorgulamanızı (listelemek) sağlar. **Joi Putkind** türü hem çıktı hem de önizleme kategorilerini içerir ve özel kategori oluşturmayı destekler.

### <a name="store-task-logs"></a>Görev günlüklerini depola

Bir görev veya iş tamamlandığında, bir dosyayı dayanıklı depolamaya kalıcı hale getirmek için, &mdash; günlük dosyaları veya `stdout.txt` ve `stderr.txt` gibi bir görevin yürütülmesi sırasında güncellenen dosyaları kalıcı hale getirmeniz gerekebilir. Bu amaçla, Azure Batch dosya kuralları Kitaplığı [Taskoutputstorage][net_taskoutputstorage]sağlar. [Savetrackedadsync][net_savetrackedasync] yöntemi. [Savetrackedadsync][net_savetrackedasync]sayesinde, düğümdeki bir dosyadaki güncelleştirmeleri izleyebilir (belirttiğiniz aralıkta) ve bu güncelleştirmeleri Azure Storage 'da kalıcı hale getirebilirsiniz.

Aşağıdaki kod parçacığında, görevi yürütme sırasında her 15 saniyede bir `stdout.txt` ' i güncelleştirmek için [Savetrackedadsync][net_savetrackedasync] kullanırız:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

Açıklamalı bölüm `Code to process data and produce output file(s)`, görevin normalde gerçekleştirdiği kod için bir yer tutucudur. Örneğin, Azure depolama 'dan verileri indiren ve üzerinde dönüştürme veya hesaplama gerçekleştiren bir kodunuz olabilir. Bu kod parçacığının önemli bölümü, bir dosyayı [Savetrackedadsync][net_savetrackedasync]ile düzenli aralıklarla güncelleştirmek üzere `using` bloğunda bu tür kodu nasıl kaydırabileceğinizi gösterir.

Düğüm Aracısı, havuzdaki her düğüm üzerinde çalışan ve düğüm ile Batch hizmeti arasında komut ve denetim arabirimini sağlayan bir programdır. Düğüm aracısının düğüm üzerindeki stdout. txt dosyasına standart içeriğini temizleme zamanına sahip olduğundan emin olmak için, bu `using` bloğunun sonunda `Task.Delay` çağrısı gereklidir. Bu gecikme olmadan çıktının son birkaç saniyesini kaçırmak mümkündür. Bu gecikme tüm dosyalar için gerekli olmayabilir.

> [!NOTE]
> Dosya izlemeyi **Savetrackedadsync**ile etkinleştirdiğinizde, yalnızca izlenen dosyaya *ekler* Azure Storage 'da kalıcı hale getirilir. Bu yöntemi yalnızca, geri dönüş olmayan günlük dosyalarını veya dosyanın sonuna ekleme işlemleri ile öğesine yazılan diğer dosyaları izlemek için kullanın.

## <a name="retrieve-output-data"></a>Çıkış verilerini al

Kalıcı çıktınızı Azure Batch dosya kuralları kitaplığını kullanarak aldığınızda, bu işlemi görev ve iş merkezli bir şekilde yapabilirsiniz. Belirli bir görev veya iş için çıktıyı Azure Storage 'daki yolunu veya hatta dosya adını bilmeniz gerekmeden isteyebilirsiniz. Bunun yerine, görev veya iş KIMLIĞINE göre çıkış dosyaları isteyebilirsiniz.

Aşağıdaki kod parçacığı bir iş görevleri boyunca yinelenir, görevin çıkış dosyaları hakkında bazı bilgileri yazdırır ve sonra dosyalarını depolamadan indirir.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Azure portal çıktı dosyalarını görüntüleme

Azure portal, [toplu Işlem dosya kuralları standardı](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)kullanılarak bağlı bir Azure depolama hesabına kalıcı olan görev çıktı dosyalarını ve günlüklerini görüntüler. Bu kuralları dilediğiniz dilde kendiniz uygulayabilirsiniz veya .NET uygulamalarınızda dosya kuralları kitaplığını kullanabilirsiniz.

Çıkış dosyalarınızın portalda görüntülenmesini etkinleştirmek için aşağıdaki gereksinimleri karşılamanız gerekir:

1. Bir Azure Depolama hesabını Batch hesabınıza bağlayın.
1. Kalıcı çıkışları kalıcı hale getiren depolama kapsayıcıları ve dosyaları için önceden tanımlanmış adlandırma kurallarına uyar. Bu kuralların tanımını dosya kuralları Kitaplığı [Benioku][github_file_conventions_readme]dosyasında bulabilirsiniz. Çıktlarınızı sürdürmek için [Azure Batch dosya kuralları][nuget_package] kitaplığını kullanırsanız dosyalarınız dosya kuralları standardına göre kalıcı hale getirilir.

Azure portal görev çıktı dosyalarını ve günlüklerini görüntülemek için, çıktısını ilgilendiğiniz göreve gidin ve **kaydedilmiş çıkış dosyaları** veya **kayıtlı Günlükler**' e tıklayın. Bu görüntüde "007" KIMLIKLI görev için **Kaydedilen çıkış dosyaları** gösterilmektedir:

![Azure portal görev çıkışları dikey penceresi][2]

## <a name="code-sample"></a>Kod örneği

[Persistoutkoyar][github_persistoutputs] örnek proje, GitHub 'daki [Azure Batch kod örneklerinden][github_samples] biridir. Bu Visual Studio çözümü, görev çıkışını dayanıklı depolamaya kalıcı hale getirmek için Azure Batch dosya kuralları kitaplığının nasıl kullanılacağını gösterir. Örneği çalıştırmak için aşağıdaki adımları izleyin:

1. Projeyi **Visual Studio 2019**' de açın.
2. Batch ve Storage **hesabı kimlik bilgilerinizi** Microsoft. Azure. Batch. Samples. Common projesindeki **accountsettings. Settings** öğesine ekleyin.
3. Çözümü **oluşturun** (ancak çalıştırmayın). İstenirse tüm NuGet paketlerini geri yükleyin.
4. **PersistOutputsTask**için bir [uygulama paketini](batch-application-packages.md) karşıya yüklemek üzere Azure Portal kullanın. . Zip paketine `PersistOutputsTask.exe` ve bağımlı derlemelerini ekleyin, uygulama KIMLIĞINI "PersistOutputsTask" olarak ve uygulama paketi sürümünü "1,0" olarak ayarlayın.
5. **Persistoutkoyar** projesini **başlatın** (çalıştırın).
6. Örneği çalıştırmak için kullanılacak Kalıcılık teknolojisini seçmeniz istendiğinde, örnek için dosya kuralları kitaplığını kullanarak görev çıkışını kalıcı hale getirmek için **1** girin. 

## <a name="next-steps"></a>Sonraki adımlar

### <a name="get-the-batch-file-conventions-library-for-net"></a>.NET için toplu Işlem dosya kuralları kitaplığını alın

.NET için batch dosya kuralları kitaplığı, [NuGet][nuget_package]üzerinde kullanılabilir. Kitaplık [Cloudjob][net_cloudjob] ve [cloudtask][net_cloudtask] sınıflarını yeni yöntemlerle genişletir. Ayrıca, dosya kuralları kitaplığı için [başvuru belgelerine](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) bakın.

Dosya kuralları Kitaplığı [kaynak kodu][github_file_conventions] , .NET için Microsoft Azure SDK deposundaki GitHub 'da kullanılabilir. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Kalıcı çıkış verileri için diğer yaklaşımları keşfet

- Kalıcı görev ve iş verilerine genel bir bakış için bkz. [Azure depolama 'ya sürekli iş ve görev çıktısı](batch-task-output.md) .
- Çıktı verilerini kalıcı hale getirmek için Batch hizmeti API 'sini nasıl kullanacağınızı öğrenmek için bkz. [Batch hizmeti API 'si Ile Azure depolama 'da görev verilerini kalıcı hale](batch-task-output-files.md) getirme.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage._cloud_storage_account
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Portalda kaydedilen çıktı dosyaları ve kaydedilmiş Günlükler seçicileri"
[2]: ./media/batch-task-output/task-output-02.png "Azure portal görev çıkışları dikey penceresi"
