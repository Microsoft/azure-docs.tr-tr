---
title: Toplu işlem düğümlerinin kullanımını en üst düzeye çıkarmak için görevleri eşzamanlı olarak çalıştırın
description: Azure Batch havuzundaki her düğümde daha az işlem düğümü kullanarak ve görevleri paralel olarak çalıştırarak verimliliği ve daha düşük maliyetleri artırın
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 81648f30a7a02f702dcb189aa7df27e5a82e2b07
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389307"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Toplu işlem düğümlerinin kullanımını en üst düzeye çıkarmak için görevleri eşzamanlı olarak çalıştırın

Her düğümde aynı anda birden fazla görev çalıştırarak, havuzunuzdaki daha az sayıda işlem düğümünde kaynak kullanımını en üst düzeye çıkarabilirsiniz.

Bazı senaryolar tek bir göreve adanmış tüm düğüm kaynaklarıyla en iyi şekilde çalıştıkları sürece, bazı iş yükleri bu kaynakları paylaşan birden çok görev olduğunda daha kısa iş süresi ve daha düşük maliyetler görebilirler. Aşağıdaki senaryoları göz önünde bulundurun:

- Veri paylaşamayacak görevler için **veri aktarımını en aza indirin** . Paylaşılan verileri daha az sayıda düğüme kopyalayarak ve ardından her düğümde paralel olarak görevleri yürüterek veri aktarımı ücretlerini ciddi ölçüde azaltabilirsiniz. Bu özellikle, her düğüme kopyalanabilecek verilerin coğrafi bölgeler arasında aktarılması gerekiyorsa geçerlidir.
- Büyük miktarda bellek gerektiren görevler için **bellek kullanımını en üst düzeye çıkarın** , ancak yalnızca kısa süreler boyunca ve yürütme sırasında değişken saatlerinde. Bu tür artışları verimli bir şekilde işlemek için daha fazla belleğe sahip daha az, ancak daha büyük işlem düğümleri kullanabilirsiniz. Bu düğümlerde her düğüm üzerinde paralel olarak çalışan birden çok görev olacaktır, ancak her görev farklı zamanlarda düğümlerin plentiful belleğinden faydalanabilir.
- Bir havuz içinde düğümler arası iletişim gerektiğinde **düğüm sayısı sınırlarını azaltır** . Şu anda, düğümler arası iletişim için yapılandırılan havuzlar 50 işlem düğümüyle sınırlıdır. Bu tür bir havuzdaki her düğüm görevleri paralel olarak yürütebiliyor ise, daha fazla sayıda görev aynı anda yürütülebilir.
- Bir işlem ortamını Azure 'a ilk kez taşırken olduğu gibi, **Şirket içi bir bilgi işlem kümesini çoğaltın**. Mevcut şirket içi çözümünüz işlem düğümü başına birden çok görev çalıştırırsa, bu yapılandırmayı daha yakından yansıtmak için en fazla düğüm görevi sayısını artırabilirsiniz.

## <a name="example-scenario"></a>Örnek senaryo

Örnek olarak, [Standart \_ D1](../cloud-services/cloud-services-sizes-specs.md#d-series) düğümlerinin yeterlı olması için CPU ve bellek gereksinimleriyle bir görev uygulaması düşünün. Ancak, işi gerekli zamanda tamamlaması için bu düğümlerin 1.000 olması gerekir.

\_1 CPU çekirdeği olan standart D1 düğümlerini kullanmak yerine, her biri 16 çekirdeğe sahip [Standart \_ D14](../cloud-services/cloud-services-sizes-specs.md#d-series) düğümlerini kullanabilir ve paralel görev yürütmeyi etkinleştirebilirsiniz. Bu, 1.000 düğüm yerine, 16 kat daha az düğüm kullanılabileceği anlamına gelir, ancak yalnızca 63 gereklidir. Her düğüm için büyük uygulama dosyaları veya başvuru verileri gerekliyse, veriler yalnızca 63 düğümlere kopyalandıklarından iş süresi ve verimlilik iyileştirilir.

## <a name="enable-parallel-task-execution"></a>Paralel görev yürütmeyi etkinleştir

Paralel görev yürütme için işlem düğümlerini havuz düzeyinde yapılandırırsınız. Batch .NET kitaplığı ile bir havuz oluştururken [Cloudpool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) özelliğini ayarlayın. Batch REST API kullanıyorsanız, havuz oluşturma sırasında istek gövdesinde [Taskslotspernode](/rest/api/batchservice/pool/add) öğesini ayarlayın.

> [!NOTE]
> `taskSlotsPerNode`Element ve [Taskslotspernode](/dotnet/api/microsoft.azure.batch.cloudpool) özelliğini yalnızca havuz oluşturma zamanında ayarlayabilirsiniz. Havuz zaten oluşturulduktan sonra değiştirilemez.

Azure Batch, düğüm başına görev yuvalarını, düğüm çekirdekleri sayısına kadar ayarlamanıza olanak sağlar. Örneğin, havuz "büyük" (dört çekirdek) boyutundaki düğümlerle yapılandırıldıysa, `taskSlotsPerNode` 16 olarak ayarlanabilir. Ancak, düğümde kaç çekirdeğin olduğuna bakılmaksızın, düğüm başına 256 taneden fazla görev yuvası kullanamazsınız. Düğüm boyutlarının her biri için çekirdek sayısı hakkında daha fazla bilgi için bkz. [Cloud Services boyutları](../cloud-services/cloud-services-sizes-specs.md). Hizmet limitleri hakkında daha fazla bilgi için bkz. [Azure Batch hizmet Için kotalar ve sınırlar](batch-quota-limit.md).

> [!TIP]
> `taskSlotsPerNode`Havuzunuz için bir [Otomatik ölçeklendirme formülü](/rest/api/batchservice/pool/enableautoscale) oluştururken değeri hesaba aldığınızdan emin olun. Örneğin, değerlendirilen bir formül, `$RunningTasks` düğüm başına görevlerin artışına göre önemli ölçüde etkilenebilir. Daha fazla bilgi için, bkz. [bir Azure Batch havuzundaki işlem düğümlerini otomatik olarak ölçeklendirme](batch-automatic-scaling.md).

## <a name="specify-task-distribution"></a>Görev dağıtımını belirtin

Eşzamanlı görevleri etkinleştirirken, bu görevlerin havuzdaki düğümlerde nasıl dağıtılmasını istediğinizi belirtmek önemlidir.

[Cloudpool. TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) özelliğini kullanarak, bu görevlerin havuzdaki tüm düğümlerde eşit olarak atanmasını belirtebilirsiniz ("yayma"). Ya da görevler havuzdaki başka bir düğüme atanmadan önce her düğüme olabildiğince fazla görevin atanması gerektiğini belirtebilirsiniz ("paketleme").

Örnek olarak, bir [Cloudpool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) değeri 16 Ile yapılandırılan [Standart \_ D14](../cloud-services/cloud-services-sizes-specs.md#d-series) düğümlerinin havuzunu (Yukarıdaki örnekte) göz önünde bulundurun. [Cloudpool. TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) , bir [Computenodefilltype](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) *paketi* ile yapılandırıldıysa, her düğümün 16 çekirdeğin tüm kullanımlarını en üst düzeye çıkarabilir ve bir [Otomatik ölçeklendirme havuzunun](batch-automatic-scaling.md) , havuzdan kullanılmayan düğümleri (atanmış herhangi bir görev olmadan düğümler) kaldırmasına izin verir. Bu, kaynak kullanımını en aza indirir ve para tasarrufu sağlar.

## <a name="define-variable-slots-per-task"></a>Görev başına değişken yuvaları tanımlama

Bir görev, bir işlem düğümünde çalışması gereken yuva sayısını belirterek [Cloudtask. Requiredslotlar](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) özelliği ile tanımlanabilir. Varsayılan değer 1’dir. Görevlerinizin, işlem düğümündeki kaynak kullanımı ile ilgili farklı ağırlıkları varsa değişken görev yuvaları ayarlayabilirsiniz. Bu, her bir işlem düğümünün CPU veya bellek gibi sistem kaynaklarını çok fazla sayıda eşzamanlı çalışan göreve sahip olmasını sağlar.

Örneğin, özelliği olan bir havuz için `taskSlotsPerNode = 8` , `requiredSlots = 8` diğer görevlerin olarak ayarlanmaları halinde, ile çok ÇEKIRDEKLI gerekli CPU yoğun görevleri gönderebilirsiniz `requiredSlots = 1` . Bu karma iş yükü zamanlandığında, CPU yoğun görevler özel olarak kendi işlem düğümlerinde çalışır, diğer görevler ise diğer düğümlerde aynı anda (her seferinde sekiz göreve kadar) çalışabilir. Bu, iş yükünüzü işlem düğümleri genelinde dengelemenize ve kaynak kullanımı verimliliğini artırmaya yardımcı olur.

Bir görevin `requiredSlots` havuzdan daha büyük olmasını belirtmemenizi unutmayın `taskSlotsPerNode` . Bu, görevin hiçbir şekilde çalışmasına neden olur. Bir iş, gönderme zamanında bir havuza bağlı olmadığından veya devre dışı bırakarak/yeniden etkinleştirilerek farklı bir havuza değiştirilebildiğinden Batch hizmeti şu anda bu çakışmayı doğrulamaz.

> [!TIP]
> Değişken görev yuvaları kullanılırken, bazı düğümlerde hala boş yuvalara sahip olsa bile, herhangi bir işlem düğümünde yeterli sayıda yuva olmadığı için, daha gerekli yuvalara sahip büyük görevler geçici olarak zamanlanamaz. Düğümlerdeki kullanılabilir yuvalara rekabet şansını artırmak için bu görevler için iş önceliğini yükseltebilirsiniz.
>
> Batch hizmeti [Taskschedulefailevent](batch-task-schedule-fail-event.md) 'i bir görevi çalıştırmak için zamanlamazsa ve gerekli yuvalar kullanılabilir olana kadar zamanlamayı yeniden denemeye devam eder. Potansiyel görev zamanlama sorunlarını tespit etmek ve uygun şekilde azaltmak için bu olayı dinleyebilirsiniz.

## <a name="batch-net-example"></a>Batch .NET örneği

Aşağıdaki [Batch .net](/dotnet/api/microsoft.azure.batch) API kod parçacıkları, düğüm başına birden çok görev yuvası ile bir havuzun nasıl oluşturulacağını ve gerekli yuvalarla bir görevin nasıl gönderileceği gösterir.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Düğüm başına birden çok görev yuvası içeren bir havuz oluşturma

Bu kod parçacığı, düğüm başına dört adet görev yuvası ile dört düğüm içeren bir havuz oluşturma isteğini gösterir. Bu işlem, havuzdaki başka bir düğüme görev atamadan önce her bir düğümü görevlerle dolduracak bir görev zamanlama İlkesi belirtir.

Batch .NET API 'sini kullanarak havuz ekleme hakkında daha fazla bilgi için bkz. [Batchclient. PoolOperations. CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
            imageReference: new ImageReference(
                                publisher: "MicrosoftWindowsServer",
                                offer: "WindowsServer",
                                sku: "2019-datacenter-core",
                                version: "latest"),
            nodeAgentSkuId: "batch.node.windows amd64");

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>Gerekli yuvalarla bir görev oluşturun

Bu kod parçacığı, varsayılan olmayan bir görev oluşturur `requiredSlots` . Bu görev yalnızca bir işlem düğümünde yeterli kullanılabilir boş yuva olduğunda çalışır.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Görevleri ve yuvaları çalıştırmak için, işlem düğümlerini sayılarıyla listeleyin

Bu kod parçacığı, havuzdaki tüm işlem düğümlerini listeler ve her düğüm için çalışan görevler ve görev yuvaları sayısını yazdırır.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>İş için görev sayılarını listeleyin

Bu kod parçacığı, her görev durumu için hem görevler hem de görev Yuvaları sayısı dahil olmak üzere iş için görev sayısını alır.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Toplu REST örneği

Aşağıdaki [toplu Rest](/rest/api/batchservice/) API kodu parçacıkları, düğüm başına birden çok görev yuvası içeren bir havuzun nasıl oluşturulacağını ve gerekli yuvalarla bir görevin nasıl gönderileceği gösterir.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Düğüm başına birden çok görev yuvası içeren bir havuz oluşturma

Bu kod parçacığında düğüm başına en fazla dört görevi olan iki büyük düğüm içeren bir havuz oluşturma isteği gösterilmektedir.

REST API kullanarak havuzlar ekleme hakkında daha fazla bilgi için bkz. bir [hesaba havuz ekleme](/rest/api/batchservice/pool/add).

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "canonical",
      "offer": "ubuntuserver",
      "sku": "18.04-lts"
    },
    "nodeAgentSKUId": "batch.node.ubuntu 16.04"
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>Gerekli yuvalarla bir görev oluşturun

Bu kod parçacığında varsayılan olmayan bir görev ekleme isteği gösterilmektedir `requiredSlots` . Bu görev yalnızca işlem düğümünde yeterli kullanılabilir boş yuva olduğunda çalışır.

```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample-on-github"></a>GitHub 'da kod örneği

GitHub 'daki [Paralleltasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) projesi [Cloudpool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) özelliğinin kullanımını gösterir.

Bu C# konsol uygulaması, bir veya daha fazla işlem düğümü içeren bir havuz oluşturmak için [Batch .net](/dotnet/api/microsoft.azure.batch) kitaplığını kullanır. Bir değişken yükünün benzetimini yapmak için bu düğümlerde yapılandırılabilir sayıda görevi yürütür. Uygulamanın çıktısı, her bir görevi yürüten düğümleri gösterir. Uygulama, iş parametrelerinin ve sürenin bir özetini de sağlar.

Örnek olarak, aşağıda ParallelTasks örnek uygulamasının iki farklı çalıştırmasından çıktının Özet kısmı verilmiştir. Burada gösterilen iş süreleri, her iş, işlem düğümleri gönderme zamanında *Boşta* durumunda olan daha önce oluşturulmuş bir havuza gönderildiği için havuz oluşturma süresini içermez.

Örnek uygulamanın ilk yürütülmesi, havuzda tek bir düğüm ve düğüm başına bir görev için varsayılan ayar olan iş süresinin 30 dakikadan fazla olduğunu gösterir.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Örneğin ikinci çalıştırmasında iş süresinde önemli bir düşüş gösterilmektedir. Bunun nedeni, havuzun her düğüm için dört görev ile yapılandırılmakta olması ve paralel görev yürütmenin her zaman bir çeyrekte işin tamamlanmasını sağlar.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>Sonraki adımlar

- [Batch Explorer](https://azure.github.io/BatchExplorer/) ısı haritasını deneyin. Batch Explorer, Azure Batch uygulamaları oluşturmaya, hata ayıklamanıza ve izlemenize yardımcı olan ücretsiz, zengin özellikli, tek başına bir istemci aracıdır. [Paralleltasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) örnek uygulamasını yürütürken, Batch Explorer ısı haritası özelliği, her düğümde paralel görevlerin yürütülmesini kolayca görselleştirmenize olanak tanır.
- [GitHub 'da Azure Batch örnekleri](https://github.com/Azure/azure-batch-samples)keşfet.
- [Batch görev bağımlılıkları](batch-task-dependencies.md)hakkında daha fazla bilgi edinin.
