---
title: İşlem kaynaklarını verimli bir şekilde kullanmak için görevleri paralel olarak çalıştırın-Azure Batch | Microsoft Docs
description: Daha az işlem düğümü kullanarak ve bir Azure Batch havuzundaki her düğümde eşzamanlı görevleri çalıştırarak verimliliği ve daha düşük maliyetleri artırın
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f45c35e6d9fb611ebf73c4eab8b517d8575b8e82
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094940"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Toplu işlem düğümlerinin kullanımını en üst düzeye çıkarmak için görevleri eşzamanlı olarak çalıştırın 

Azure Batch havuzunuzdaki her bir işlem düğümünde birden fazla görev aynı anda çalıştırılarak, havuzdaki daha az sayıda düğüm üzerinde kaynak kullanımını en üst düzeye çıkarabilirsiniz. Bazı iş yükleri için bu, daha kısa iş sürelerine ve maliyeti azaltmaya neden olabilir.

Bazı senaryolar, tüm düğümün kaynaklarını tek bir göreve eklemek avantajına karşın, birkaç durum birden fazla görevin bu kaynakları paylaşmasına izin vermenin avantajlarından yararlanır:

* Görevler veri paylaştığında **veri aktarımını en aza indirir** . Bu senaryoda, paylaşılan verileri daha az sayıda düğüme kopyalayarak ve görevleri her düğümde paralel olarak yürüterek veri aktarımı ücretlerini ciddi ölçüde azaltabilirsiniz. Bu özellikle, her düğüme kopyalanabilecek verilerin coğrafi bölgeler arasında aktarılması gerekiyorsa geçerlidir.
* Görevler büyük miktarda bellek gerektirdiğinde, ancak yalnızca kısa süreler boyunca ve yürütme sırasında değişken sürelerinde **bellek kullanımının** en üst düzeye çıkarın. Bu tür artışları verimli bir şekilde işlemek için daha fazla belleğe sahip daha az, ancak daha büyük işlem düğümleri kullanabilirsiniz. Bu düğümlerde her düğüm üzerinde paralel olarak çalışan birden çok görev olur, ancak her görev farklı zamanlarda düğümlerin plentiful belleğinden faydalanır.
* Bir havuz içinde düğümler arası iletişim gerektiğinde **düğüm sayısı sınırlarını** azaltma. Şu anda, düğümler arası iletişim için yapılandırılan havuzlar 50 işlem düğümüyle sınırlıdır. Bu tür bir havuzdaki her düğüm görevleri paralel olarak yürütebiliyor ise, daha fazla sayıda görev aynı anda yürütülebilir.
* Bir bilgi işlem ortamını Azure 'a taşırken olduğu gibi, **Şirket içi bir hesaplama kümesini çoğaltma**. Mevcut şirket içi çözümünüz işlem düğümü başına birden çok görev çalıştırırsa, bu yapılandırmayı daha yakından yansıtmak için en fazla düğüm görevi sayısını artırabilirsiniz.

## <a name="example-scenario"></a>Örnek senaryo
Paralel görev yürütmesinin avantajlarını gösteren bir örnek olarak, görev uygulamanızın [Standart\_D1](../cloud-services/cloud-services-sizes-specs.md) düğümlerinin yeterli olması gibi CPU ve bellek gereksinimleri olduğunu varsayalım. Ancak, işin gerekli zamanda tamamlanabilmesi için, bu düğümlerin 1.000 ' i gereklidir.

1 CPU çekirdeği olan\_standart D1 düğümlerini kullanmak yerine, her biri 16 çekirdeğe sahip [Standart\_D14](../cloud-services/cloud-services-sizes-specs.md) düğümlerini kullanabilir ve paralel görev yürütmeyi etkinleştirebilirsiniz. Bu nedenle, 1.000 düğüm yerine *16 kez daha az düğüm* kullanılabilir, ancak yalnızca 63 gereklidir. Ayrıca, her düğüm için büyük uygulama dosyaları veya başvuru verileri gerekliyse, veriler yalnızca 63 düğümlere kopyalandığından iş süresi ve verimlilik yeniden geliştirilmiştir.

## <a name="enable-parallel-task-execution"></a>Paralel görev yürütmeyi etkinleştir
Paralel görev yürütme için işlem düğümlerini havuz düzeyinde yapılandırırsınız. Batch .NET kitaplığı ile, bir havuz oluştururken [Cloudpool. MaxTasksPerComputeNode][maxtasks_net] özelliğini ayarlayın. Batch REST API kullanıyorsanız, havuz oluşturma sırasında istek gövdesinde [Maxtaskspernode][rest_addpool] öğesini ayarlayın.

Azure Batch, düğüm başına görevleri (4X) çekirdek düğüm sayısına kadar ayarlamanıza olanak sağlar. Örneğin, havuz "büyük" (dört çekirdek) boyutundaki düğümlerle yapılandırıldıysa, `maxTasksPerNode` 16 olarak ayarlanabilir. Ancak, düğümde kaç çekirdeğin olduğuna bakılmaksızın, düğüm başına 256 ' den fazla görev kullanamazsınız. Düğüm boyutlarının her biri için çekirdek sayısı hakkında daha fazla bilgi için bkz. [Cloud Services boyutları](../cloud-services/cloud-services-sizes-specs.md). Hizmet limitleri hakkında daha fazla bilgi için bkz. [Azure Batch hizmet Için kotalar ve sınırlar](batch-quota-limit.md).

> [!TIP]
> Havuzunuz için bir `maxTasksPerNode` [Otomatik ölçeklendirme formülü][enable_autoscaling] oluştururken değeri hesaba aldığınızdan emin olun. Örneğin, değerlendirilen `$RunningTasks` bir formül, düğüm başına görevlerin artışına göre önemli ölçüde etkilenebilir. Daha fazla bilgi için bkz. [bir Azure Batch havuzundaki işlem düğümlerini otomatik olarak ölçeklendirme](batch-automatic-scaling.md) .
>
>

## <a name="distribution-of-tasks"></a>Görevlerin dağıtılması
Bir havuzdaki işlem düğümleri görevleri eşzamanlı olarak yürütemediğinde, bu görevlerin havuzdaki düğümlerde nasıl dağıtılmasını istediğinizi belirtmek önemlidir.

[Cloudpool. TaskSchedulingPolicy][task_schedule] özelliğini kullanarak, bu görevlerin havuzdaki tüm düğümlerde eşit olarak atanmasını belirtebilirsiniz ("yayma"). Ya da görevler havuzdaki başka bir düğüme atanmadan önce her düğüme olabildiğince fazla görevin atanması gerektiğini belirtebilirsiniz ("paketleme").

Bu özelliğin nasıl değerli olduğunu gösteren bir örnek olarak, [\_standart D14](../cloud-services/cloud-services-sizes-specs.md) düğümlerinin havuzunu (Yukarıdaki örnekte) bir [cloudpool. MaxTasksPerComputeNode][maxtasks_net] değeri 16 ile yapılandırılmış şekilde düşünün. [Cloudpool. TaskSchedulingPolicy][task_schedule] , bir [Computenodefilltype][fill_type] *paketi*ile yapılandırıldıysa, her düğümün 16 çekirdeğin tüm kullanımlarını en üst düzeye çıkarabilir ve bir [Otomatik ölçeklendirme havuzunun](batch-automatic-scaling.md) kullanılmayan düğümleri havuzdan (düğümler olmadan) ayıklayabilmesini sağlar. atanan tüm görevler). Bu, kaynak kullanımını en aza indirir ve para tasarrufu sağlar.

## <a name="batch-net-example"></a>Batch .NET örneği
Bu [Batch .net][api_net] API kod parçacığı, düğüm başına en fazla dört adet görev içeren dört düğüm içeren bir havuz oluşturma isteğini gösterir. Bu işlem, havuzdaki başka bir düğüme görev atamadan önce her bir düğümü görevlerle dolduracak bir görev zamanlama İlkesi belirtir. Batch .NET API 'sini kullanarak havuz ekleme hakkında daha fazla bilgi için bkz. [Batchclient. PoolOperations. CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Toplu REST örneği
Bu [toplu Iş Rest][api_rest] API parçacığı, düğüm başına en fazla dört görevi olan iki büyük düğüm içeren bir havuz oluşturma isteğini gösterir. REST API kullanarak havuzlar ekleme hakkında daha fazla bilgi için bkz. bir [hesaba havuz ekleme][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicatedComputeNodes":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [!NOTE]
> `maxTasksPerNode` Öğesi ve [MaxTasksPerComputeNode][maxtasks_net] özelliğini yalnızca havuz oluşturma zamanında ayarlayabilirsiniz. Havuz zaten oluşturulduktan sonra değiştirilemez.
>
>

## <a name="code-sample"></a>Kod örneği
GitHub 'daki [Parallelnodetasks][parallel_tasks_sample] projesi [Cloudpool. MaxTasksPerComputeNode][maxtasks_net] özelliğinin kullanımını gösterir.

Bu C# konsol uygulaması, bir veya daha fazla işlem düğümü içeren bir havuz oluşturmak için [Batch .net][api_net] kitaplığını kullanır. Değişken yükünün benzetimini yapmak için bu düğümlerde yapılandırılabilir sayıda görevi yürütür. Uygulamanın çıktısı, her bir görevi yürüten düğümleri belirtir. Uygulama, iş parametrelerinin ve sürenin bir özetini de sağlar. Örnek uygulamanın iki farklı çalıştırmasından çıktının Özet bölümü aşağıda görüntülenir.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Örnek uygulamanın ilk yürütülmesi, havuzda tek bir düğüm ve düğüm başına bir görev için varsayılan ayar olan iş süresinin 30 dakikadan fazla olduğunu gösterir.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Örneğin ikinci çalıştırmasında iş süresinde önemli bir düşüş gösterilmektedir. Bunun nedeni, havuzun her düğüm için dört görev ile yapılandırılmıştı. Bu, paralel görev yürütmenin iş zamanının neredeyse bir çeyrekte tamamlanmasını sağlar.

> [!NOTE]
> Yukarıdaki özetlerdeki iş süreleri havuz oluşturma süresini içermez. Yukarıdaki işlerin her biri, işlem düğümleri gönderme zamanında *Boşta* durumunda olan önceden oluşturulmuş havuzlara gönderilmiştir.
>
>

## <a name="next-steps"></a>Sonraki adımlar
### <a name="batch-explorer-heat-map"></a>Batch Explorer ısı haritası
[Batch Explorer][batch_labs] , Azure Batch uygulamaları oluşturmaya, hata ayıklamanıza ve izlemenize yardımcı olan ücretsiz, zengin özellikli, tek başına bir istemci aracıdır. Batch Explorer, görev yürütme görselleştirmesini sağlayan bir *ısı haritası* özelliği içerir. [Paralleltasks][parallel_tasks_sample] örnek uygulamasını yürütürken, her bir düğümdeki paralel görevlerin yürütülmesini kolayca görselleştirebilmeniz Için ısı haritası özelliğini kullanabilirsiniz.


[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

