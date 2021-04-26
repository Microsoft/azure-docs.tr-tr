---
title: MPı uygulamalarını çalıştırmak için çok örnekli görevleri kullanma
description: Ileti geçirme arabirimi (MPı) uygulamalarını Azure Batch ' deki çok örnekli görev türünü kullanarak yürütmeyi öğrenin.
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: e96cfb89b186d69f6ad969949b8df609956114d2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389409"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Batch 'de Ileti geçirme arabirimi (MPı) uygulamalarını çalıştırmak için çok örnekli görevleri kullanma

Çok örnekli görevler birden çok işlem düğümünde aynı anda bir Azure Batch görevi çalıştırmanızı sağlar. Bu görevler, toplu Işteki Ileti geçirme arabirimi (MPı) uygulamaları gibi yüksek performanslı bilgi işlem senaryolarına olanak tanır. Bu makalede [Batch .net](/dotnet/api/microsoft.azure.batch) kitaplığını kullanarak çok örnekli görevleri yürütmeyi öğreneceksiniz.

> [!NOTE]
> Bu makaledeki örneklerde Batch .NET, MS-MPI ve Windows işlem düğümlerine odaklanılmış olsa da, burada ele alınan çok örnekli görev kavramları diğer platformlar ve teknolojiler için geçerlidir (örneğin, Python ve Intel MPı Linux düğümlerinde).

## <a name="multi-instance-task-overview"></a>Çok örnekli göreve genel bakış

Batch 'de, her görev normalde tek bir işlem düğümünde yürütülür. bir işe birden çok görev gönderirseniz ve Batch hizmeti her görevi bir düğümde yürütmeye göre zamanlar. Ancak, bir görevin **Çoklu örnek ayarlarını** yapılandırarak, toplu olarak tek bir birincil görev ve daha sonra birden çok düğümde yürütülen birkaç alt görev oluşturmayı öğreneceksiniz.

:::image type="content" source="media/batch-mpi/batch-mpi-01.png" alt-text="Çok örnekli ayarların genel bakışını gösteren diyagram.":::

Bir iş için çok örnekli ayarlarla bir görev gönderdiğinizde toplu Işlem, çok örnekli görevlere özgü birkaç adımı gerçekleştirir:

1. Batch hizmeti, çoklu örnek ayarlarına bağlı olarak bir **birincil** ve birkaç **alt görev** oluşturur. Toplam görev sayısı (birincil ve tüm alt görevler), çok örnekli ayarlarda belirttiğiniz **örnek** sayısıyla (işlem düğümleri) eşleşir.
2. Batch, işlem düğümlerinden birini **ana öğe** olarak belirler ve birincil görevi ana bilgisayarda yürütülecek şekilde zamanlar. Alt görevleri, çok örnekli göreve ayrılan işlem düğümlerinin geri kalanı üzerinde yürütülecek şekilde zamanlar, düğüm başına bir alt görev.
3. Birincil ve tüm alt görevler, çok örnekli ayarlarda belirttiğiniz **ortak kaynak dosyalarını** indirir.
4. Ortak kaynak dosyaları indirildikten sonra, birincil ve alt görevler, çoklu örnek ayarlarında belirttiğiniz **koordinasyon komutunu** yürütür. Düzenleme komutu genellikle, görevi yürütmek için düğüm hazırlamak üzere kullanılır. Bu, arka plan hizmetlerinin (örneğin, [Microsoft MPI](/message-passing-interface/microsoft-mpi) `smpd.exe` ) başlamasını ve düğümlerin düğümler arası iletileri işlemeye hazırlandığının doğrulanması olabilir.
5. Birincil görev, düzenleme komutu birincil ve tüm alt görevler tarafından başarıyla tamamlandıktan *sonra* ana düğümde **uygulama komutunu** yürütür. Uygulama komutu, çok örnekli görevin kendisi için komut satırı olur ve yalnızca birincil görev tarafından yürütülür. [MS-MPı](/message-passing-interface/microsoft-mpi) tabanlı bir çözümde, bu, kullanarak MPI özellikli uygulamanızı yürütebileceğiniz yerdir `mpiexec.exe` .

> [!NOTE]
> İşlevsel farklı olsa da, "Çoklu örnek görevi" [startTask](/dotnet/api/microsoft.azure.batch.starttask) veya [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask)gibi benzersiz bir görev türü değildir. Çok örnekli görev, çok örnekli ayarları yapılandırılmış standart bir Batch görevi (Batch .NET 'te[cloudtask](/dotnet/api/microsoft.azure.batch.cloudtask) ) olur. Bu makalede, buna **Çoklu örnek görevi** olarak değineceğiz.

## <a name="requirements-for-multi-instance-tasks"></a>Çok örnekli görevler için gereksinimler

Çok örnekli görevler, **düğüm içi iletişimin etkin** olduğu bir havuz gerektirir ve **eşzamanlı görev yürütme devre dışı bırakılır**. Eşzamanlı görev yürütmeyi devre dışı bırakmak için [Cloudpool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) özelliğini 1 olarak ayarlayın.

> [!NOTE]
> Batch, düğüm içi iletişim etkin olan bir havuzun boyutunu [sınırlar](batch-quota-limit.md#pool-size-limits) .

Bu kod parçacığı, Batch .NET kitaplığı kullanılarak çok örnekli görevler için bir havuzun nasıl oluşturulacağını gösterir.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
        imageReference: new ImageReference(
                        publisher: "MicrosoftWindowsServer",
                        offer: "WindowsServer",
                        sku: "2019-datacenter-core",
                        version: "latest"),
        nodeAgentSkuId: "batch.node.windows amd64");

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.TaskSlotsPerNode = 1;
```

> [!NOTE]
> Internode iletişimi devre dışı bırakılmış bir havuzda çok örnekli bir görevi çalıştırmayı denerseniz veya 1 ' den büyük bir *Taskslotspernode* değeri varsa, görev hiçbir şekilde zamanlanmadı; "etkin" durumunda süresiz olarak kalır.

### <a name="use-a-starttask-to-install-mpi"></a>MPı 'yi yüklemek için bir StartTask kullanın

MPı uygulamalarını çok örnekli bir görevle çalıştırmak için, önce havuzdaki işlem düğümlerine bir MPı uygulaması (örneğin, MS-MPı veya Intel MPı) yüklemeniz gerekir. Bu, bir düğüm bir havuza katıldığında veya yeniden başlatıldığında yürütülen bir [startTask](/dotnet/api/microsoft.azure.batch.starttask)kullanmak için iyi bir zamandır. Bu kod parçacığı, bir [kaynak dosyası](/dotnet/api/microsoft.azure.batch.resourcefile)olarak MS-MPI kurulum paketini belirten bir startTask oluşturur. Başlangıç görevinin komut satırı, kaynak dosyası düğüme indirildikten sonra yürütülür. Bu durumda, komut satırı MS-MPı 'nin katılımsız bir yüklemesini gerçekleştirir.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Doğrudan uzak bellek erişimi (RDMA)

Batch havuzunuzdaki işlem düğümleri için A9 gibi bir [RDMA özellikli bir boyut](../virtual-machines/sizes-hpc.md?toc=/azure/virtual-machines/windows/toc.json) seçtiğinizde, MPI uygulamanız Azure 'un yüksek performanslı, düşük gecikmeli uzak doğrudan bellek ERIŞIMI (RDMA) ağından faydalanabilir.

[Azure 'daki sanal makinelerin](../virtual-machines/sizes.md) (virtualmakineyapılandırma havuzları için) veya [Cloud Services boyutlarının](../cloud-services/cloud-services-sizes-specs.md) (cloudservicesconfiguration havuzları için) Boyutlar cinsinden "RDMA özellikli" olarak belirtilen boyutları arayın.

> [!NOTE]
> [Linux işlem DÜĞÜMLERINDE](batch-linux-nodes.md)RDMA 'nin avantajlarından yararlanmak Için düğümlerde **Intel MPI** kullanmanız gerekir.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Batch .NET ile çok örnekli bir görev oluşturma

Artık havuz gereksinimlerini ve MPı paketi yüklemesini kapsadığımıza göre, çok örnekli görevi oluşturalım. Bu kod parçacığında, bir standart [Cloudtask](/dotnet/api/microsoft.azure.batch.cloudtask)oluşturup [multiınstancesettings](/dotnet/api/microsoft.azure.batch.cloudtask) özelliğini yapılandıracağız. Daha önce belirtildiği gibi, çok örnekli görev ayrı bir görev türü değildir, ancak çok örnekli ayarlarla yapılandırılmış standart bir Batch görevi.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Birincil görev ve alt görevler

Bir görev için çoklu örnek ayarlarını oluştururken, görevi yürütecek olan işlem düğümlerinin sayısını belirtirsiniz. Görevi bir işe gönderdiğinizde, Batch hizmeti bir **birincil** görev ve belirttiğiniz düğüm sayısıyla eşleşen çok sayıda **alt** iş oluşturur.

Bu görevlere 0- *Numberofınstances* -1 aralığında BIR tamsayı kimliği atanır. KIMLIĞI 0 olan görev birincil görevdir ve diğer tüm kimlikler alt görevlerdir. Örneğin, bir görev için aşağıdaki çok örnekli ayarları oluşturursanız, birincil görevin KIMLIĞI 0 olur ve alt görevler 1 ile 9 arasında kimlik içermelidir.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Ana düğüm

Çok örnekli bir görevi gönderdiğinizde, Batch hizmeti işlem düğümlerinden birini "ana" düğüm olarak belirler ve birincil görevi ana düğümde yürütülecek şekilde zamanlar. Alt görevler, çok örnekli göreve ayrılan düğümlerin geri kalanı üzerinde yürütülecek şekilde zamanlanır.

## <a name="coordination-command"></a>Koordinasyon komutu

**Koordinasyon komutu** hem birincil hem de alt görevler tarafından yürütülür.

Koordinasyon komutunun çağrılması,--düzenleme komutu tüm alt görevler için başarılı bir şekilde döndürülünceye kadar uygulama komutunu çalıştırmaz. Koordinasyon komutu bu nedenle gerekli arka plan hizmetlerini başlatıp, kullanıma hazırdıklarından emin olun ve ardından çıkış yapın. Örneğin, MS-MPI sürüm 7 kullanan bir çözüm için bu düzenleme komutu, düğümdeki SMPD hizmetini başlatır ve çıkar:

`cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d`

Bu koordinasyon komutunda kullanılması gerektiğini aklınızda edin `start` . `smpd.exe`Uygulama yürütmeden hemen sonra dönmediğinden bu gereklidir. Start komutunun kullanımı olmadan, bu koordinasyon komutu dönmez ve bu nedenle uygulama komutunun çalışmasını engeller.

## <a name="application-command"></a>Uygulama komutu

Birincil görev ve tüm alt görevler koordinasyon komutunu yürütmeyi tamamladığında, çok örnekli görevin komut satırı *yalnızca* birincil görev tarafından yürütülür. Bu **uygulama komutunu** , koordinasyon komutundan ayırt etmek için çağırıyoruz.

MS-MPı uygulamalarında, MPı özellikli uygulamanızı ile yürütmek için uygulama komutunu kullanın `mpiexec.exe` . Örneğin, MS-MPı sürüm 7 kullanan bir çözüm için uygulama komutu aşağıda verilmiştir:

`cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe`

> [!NOTE]
> MS-MPı `mpiexec.exe` `CCP_NODES` Varsayılan olarak değişkenini kullandığından (bkz. [ortam değişkenleri](#environment-variables)), yukarıdaki örnek uygulama komut satırı bunu dışlar.

## <a name="environment-variables"></a>Ortam değişkenleri

Batch, çok örnekli bir göreve ayrılan işlem düğümlerinde çok örnekli görevlere özgü birkaç [ortam değişkeni](batch-compute-node-environment-variables.md) oluşturur. Koordinasyon ve uygulama komut hatlarınız, yürüttikleri betikler ve programlar gibi bu ortam değişkenlerine başvurabilir.

Aşağıdaki ortam değişkenleri, Batch hizmeti tarafından çok örnekli görevler tarafından kullanılmak üzere oluşturulur:

- `CCP_NODES`
- `AZ_BATCH_NODE_LIST`
- `AZ_BATCH_HOST_LIST`
- `AZ_BATCH_MASTER_NODE`
- `AZ_BATCH_TASK_SHARED_DIR`
- `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Bunlarla ilgili tüm ayrıntılar ve bunların içerikleri ve görünürlüğü dahil diğer toplu işlem düğümü ortam değişkenleri için bkz. [işlem düğümü ortam değişkenleri](batch-compute-node-environment-variables.md).

> [!TIP]
> [Batch Linux MPI kodu örneği](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch/article_samples/mpi) , bu ortam değişkenlerinin kaç tane kullanıldığına ilişkin bir örnek içerir.

## <a name="resource-files"></a>Kaynak dosyalar

Çok örnekli görevler için göz önünde bulundurmanız gereken iki kaynak dosyası kümesi vardır: *Tüm* görevlerin indirdiği **ortak kaynak dosyaları** (birincil ve alt görevler) ve *yalnızca birincil* görevin indirileceği çok örnekli görevin kendisi için belirtilen **kaynak dosyaları** .

Bir görev için çoklu örnek ayarlarında bir veya daha fazla **ortak kaynak dosyası** belirtebilirsiniz. Bu ortak kaynak dosyaları, [Azure depolama](../storage/common/storage-introduction.md) 'dan her bir düğümün **görev paylaşılan dizinine** birincil ve tüm alt görevler tarafından indirilir. Görev paylaşılan dizinine, ortam değişkenini kullanarak, uygulama ve düzenleme komut satırlarından erişebilirsiniz `AZ_BATCH_TASK_SHARED_DIR` . `AZ_BATCH_TASK_SHARED_DIR`Yol, çok örnekli göreve ayrılan her düğümde aynıdır, bu sayede birincil ve tüm alt görevler arasında tek bir düzenleme komutu paylaşabilirsiniz. Batch, dizini bir uzaktan erişim açısından "paylaşmaz", ancak ortam değişkenlerinde ipucu içinde daha önce bahsedildiği gibi bir bağlama veya paylaşma noktası olarak kullanabilirsiniz.

Çoklu örnek görevi için belirttiğiniz kaynak dosyaları, varsayılan olarak görevin çalışma dizinine indirilir `AZ_BATCH_TASK_WORKING_DIR` . Belirtildiği gibi, yaygın kaynak dosyalarının aksine yalnızca birincil görev, çok örnekli görevin kendisi için belirtilen kaynak dosyalarını indirir.

> [!IMPORTANT]
> Her zaman ortam değişkenlerini kullanın `AZ_BATCH_TASK_SHARED_DIR` ve `AZ_BATCH_TASK_WORKING_DIR` komut satırlarında bu dizinlere başvurun. Yolları el ile oluşturmaya çalışmayın.

## <a name="task-lifetime"></a>Görev ömrü

Birincil görevin ömrü, tüm çok örnekli görevin ömrünü denetler. Birincil çıkış yapıldığında tüm alt görevler sonlandırılır. Birincil çıkış kodu, görevin çıkış kodudur ve bu nedenle, yeniden deneme amacıyla görevin başarısını veya başarısızlığını belirlemede kullanılır.

Alt görevlerden herhangi biri başarısız olursa, sıfır olmayan bir dönüş koduyla çıkılıyor, örneğin, tüm çok örnekli görev başarısız olur. Daha sonra, yeniden deneme sınırına kadar çok örnekli görev sonlandırılır ve yeniden denenir.

Çok örnekli bir görevi sildiğinizde, birincil ve tüm alt görevler de Batch hizmeti tarafından silinir. Tüm alt görev dizinleri ve dosyaları, standart bir görevde olduğu gibi işlem düğümlerinden silinir.

[Maxtaskretrycount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount), [maxduvar Clocktime](/dotnet/api/microsoft.azure.batch.taskconstraints.maxwallclocktime)ve [RetentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) özellikleri gibi çok örnekli bir görevin [taskkýsýtlamalarından](/dotnet/api/microsoft.azure.batch.taskconstraints) bazıları standart bir görevde olduğu gibi kabul edilir ve birincil ve tüm alt görevlere uygulanır. Ancak, iş için çok örnekli görevi ekledikten sonra, bu değişiklik yalnızca birincil göreve uygulanır ve tüm alt görevler özgün RetentionTime kullanmaya devam eder.

İşlem düğümünün son görev listesi, son görev çok örnekli bir görevin parçasıysa bir alt görevin KIMLIĞINI yansıtır.

## <a name="obtain-information-about-subtasks"></a>Alt görevler hakkında bilgi edinin

Batch .NET kitaplığı 'nı kullanarak alt görevler hakkında bilgi edinmek için [Cloudtask. Listalt görevler](/dotnet/api/microsoft.azure.batch.cloudtask.listsubtasks) yöntemini çağırın. Bu yöntem, tüm alt görevlerle ilgili bilgileri ve görevleri yürüten işlem düğümü hakkındaki bilgileri döndürür. Bu bilgilerden, her alt görevin kök dizinini, havuz KIMLIĞINI, geçerli durumunu, çıkış kodunu ve daha fazlasını belirleyebilirsiniz. Bu bilgileri, alt görevin dosyalarını almak için [Pooloperations. GetNodeFile](/dotnet/api/microsoft.azure.batch.pooloperations.getnodefile) yöntemiyle birlikte kullanabilirsiniz. Bu yöntemin birincil görev (KIMLIK 0) için bilgi döndürmediğini unutmayın.

> [!NOTE]
> Aksi belirtilmediği takdirde, çok örnekli [Cloudtask](/dotnet/api/microsoft.azure.batch.cloudtask) üzerinde çalışan Batch .net metotları *yalnızca* birincil görev için geçerlidir. Örneğin, [cloudtask. ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask.listnodefiles) yöntemini çok örnekli bir görevde çağırdığınızda yalnızca birincil görevin dosyaları döndürülür.

Aşağıdaki kod parçacığı, alt görev bilgilerinin nasıl alınacağını ve yürütüldüğü düğümlerin dosya içeriğini nasıl isteyeceğini gösterir.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Kod örneği

GitHub 'daki [Multiınstancetasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks) kod örneği, toplu işlem düğümlerinde bir [MS-MPI](/message-passing-interface/microsoft-mpi) uygulamasını çalıştırmak için çok örnekli bir görevin nasıl kullanılacağını gösterir. Örneği çalıştırmak için aşağıdaki adımları izleyin.

### <a name="preparation"></a>Hazırlık

1. [MS-MPı SDK ve yeniden dağıtım yükleyicilerini](/message-passing-interface/microsoft-mpi) indirin ve yükleyin. Yüklemeden sonra MS-MPI ortam değişkenlerinin ayarlandığını doğrulayabilirsiniz.
1. [Mpihelloworld](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld) örnek MPI programının *yayın* sürümünü oluşturun. Bu, işlem düğümlerinde çoklu örnek görevi tarafından çalıştırılacak olan programdır.
1. `MPIHelloWorld.exe`(Adım 2 ' de oluşturduğunuz) ve `MSMpiSetup.exe` (adım 1 ' de indirdiğiniz) içeren bir zip dosyası oluşturun. Sonraki adımda bu zip dosyasını bir uygulama paketi olarak karşıya yüklersiniz.
1. "MPIHelloWorld" adlı bir Batch [uygulaması](batch-application-packages.md) oluşturmak için [Azure Portal](https://portal.azure.com) kullanın ve önceki adımda oluşturduğunuz zip dosyasını uygulama paketinin "1,0" sürümü olarak belirtin. Daha fazla bilgi için bkz. [uygulamaları karşıya yükleme ve yönetme](batch-application-packages.md#upload-and-manage-applications) .

> [!TIP]
> Uygulamasının *yayın* sürümünü oluşturmak, `MPIHelloWorld.exe` uygulama paketinize ek bağımlılıklar (örneğin, veya) dahil etmek zorunda olmamasını sağlar `msvcp140d.dll` `vcruntime140d.dll` .

### <a name="execution"></a>Yürütme

1. GitHub 'dan [Azure-Batch-Samples. zip dosyasını](https://github.com/Azure/azure-batch-samples/archive/master.zip) indirin.
1. Visual Studio 2019 ' de Multiınstancetasks **çözümünü** açın. `MultiInstanceTasks.sln`Çözüm dosyası şu konumda bulunur:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
1. `AccountSettings.settings` **Microsoft.Azure.Batch. Samples. Common** projesinde toplu işlem ve depolama hesabı kimlik bilgilerinizi girin.
1. Bir Batch havuzundaki işlem düğümlerinde MPı örnek uygulamasını yürütmek için Multiınstancetasks çözümünü **derleyin ve çalıştırın** .
1. *Isteğe bağlı*: kaynakları silmeden önce örnek havuzunu, işi ve görevi ("Multiınstancesamplepool", "Multiınstancesamplejob", "Multiınstancesampletask") incelemek için [Azure Portal](https://portal.azure.com) veya [Batch Explorer](https://azure.github.io/BatchExplorer/) kullanın.

> [!TIP]
> Visual [Studio Community](https://visualstudio.microsoft.com/vs/community/) 'yi ücretsiz olarak indirebilirsiniz.

Çıkış `MultiInstanceTasks.exe` , aşağıdakine benzer:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Batch Linux Için MPI desteği](https://docs.microsoft.com/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch)hakkında daha fazla bilgi edinin.
- Azure Batch MPı çözümlerinizde kullanılmak üzere [Linux işlem düğümü havuzları oluşturmayı](batch-linux-nodes.md) öğrenin.
