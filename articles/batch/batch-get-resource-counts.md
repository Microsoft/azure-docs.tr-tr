---
title: Görevler ve düğümler için durum sayısı-Azure Batch | Microsoft Docs
description: Toplu Iş çözümlerini yönetmeye ve izlemeye yardımcı olmak için Azure Batch görevlerinin ve işlem düğümlerinin durumunu say.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 09/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 7b41be8c325cd238592f33369499348885de1778
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323536"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Görevleri ve düğümleri duruma göre sayarak Batch çözümlerini izleme

Büyük ölçekli Azure Batch çözümlerini izlemek ve yönetmek için çeşitli durumlarda kaynakların doğru sayımlarını yapmanız gerekir. Azure Batch, Batch *görevleri* ve *işlem düğümleri*için bu sayıları almak üzere etkili işlemler sağlar. Büyük görev veya düğüm koleksiyonlarıyla ilgili ayrıntılı bilgileri döndüren zaman alıcı liste sorguları yerine bu işlemleri kullanın.

* [Get görev sayıları][rest_get_task_counts] bir işte etkin, çalışan ve tamamlanmış görevlerin toplam sayısını ve başarılı veya başarısız olan görevleri alır. 

  Her durumda görevleri sayarak, bir kullanıcıya iş ilerlemesini daha kolay bir şekilde görüntüleyebilir veya işi etkileyebilecek beklenmedik gecikmeleri veya sorunları tespit edebilirsiniz. Toplu Iş Hizmetleri API sürümü 2017 -06-01.5.1 ve ilgili SDK 'lar ve araçlar olarak görev sayısını Al kullanılabilir.

* [Liste havuzu düğüm sayıları][rest_get_node_counts] , çeşitli durumlarda bulunan her havuzda adanmış ve düşük öncelikli işlem düğümleri sayısını alır: oluşturma, boşta, çevrimdışı, ön işleme, yeniden başlatma, yeniden görüntüleme, başlangıç ve diğer durumlar. 

  Her durumda düğümleri sayarak, işlerinizi çalıştırmak için yeterli işlem kaynağına sahip olduğunuz ve havuzlarınızla ilgili olası sorunları tespit edebilirsiniz. Liste havuzu düğüm sayıları Batch hizmeti API sürümü 2018 -03-01.6.1 ve ilgili SDK 'lar ve araçlar olarak kullanılabilir.

Hizmetin görev sayısını veya düğüm sayısı işlemlerini desteklemeyen bir sürümünü kullanıyorsanız, bu kaynakları saymak için bir liste sorgusu kullanın. Ayrıca, uygulamalar, dosyalar ve işler gibi diğer Batch kaynakları hakkında bilgi almak için bir liste sorgusu kullanın. Liste sorgularına filtre uygulama hakkında daha fazla bilgi için bkz. [Batch kaynaklarını etkili bir şekilde listelemek için sorgular oluşturma](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Görev durumu sayıları

Görev sayılarını al işlemi görevleri aşağıdaki durumlara göre sayar:

- **Etkin** -sıraya alınmış ve çalıştırılabilen, ancak şu anda bir işlem düğümüne atanmamış bir görevdir. Bir görev, henüz `active` tamamlanmamış [bir üst göreve bağımlıysa](batch-task-dependencies.md) de bir görevdir. 
- **Çalışıyor** -bir işlem düğümüne atanan ancak henüz tamamlanmamış bir görev. Bir görev, [bir görev hakkında bilgi al][rest_get_task] işlemiyle gösterildiği `preparing` gibi `running`, durumu veya olduğu `running` zaman sayılır.
- **Tamamlandı** -başarıyla bittiği veya başarısız olduğu ve ayrıca yeniden deneme sınırını tükettiğinden, artık çalıştırılmasına uygun olmayan bir görev. 
- **Başarılı** -görev yürütme `success`sonucu olan bir görev. Batch, `TaskExecutionResult` [ExecutionInfo][rest_get_exec_info] özelliğinin özelliğini denetleyerek bir görevin başarılı veya başarısız olup olmadığını belirler.
- **Başarısız oldu** Görev yürütme `failure`sonucu olan bir görev.

Aşağıdaki .NET kod örneği, görev sayılarının duruma göre nasıl alınacağını gösterir: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Bir iş için görev sayısını almak üzere REST ve diğer desteklenen diller için de benzer bir model kullanabilirsiniz. 

> [!NOTE]
> Batch hizmeti API sürümleri 2018 -08-01.7.0 Ayrıca görev sayısını Al `validationStatus` yanıtı içinde bir özellik döndürüyor. Bu özellik, toplu Işlemin durumunun liste görevleri API 'sinde raporlanan durumlarıyla tutarlı olup olmadığını kontrol edilip edilmeyeceğini belirtir. Değeri, yalnızca `validated` iş için en az bir kez tutarlılık denetimi yapıldığını gösterir. `validationStatus` Özelliğin değeri, görev saylarının döndürdüğü sayımların Şu anda güncel olup olmadığını göstermez.
>

## <a name="node-state-counts"></a>Düğüm durumu sayıları

Liste havuzu düğüm sayısı işlemi, her havuzda aşağıdaki durumlara göre işlem düğümlerini sayar. Ayrı toplama sayıları, her havuzdaki adanmış düğümler ve düşük öncelikli düğümler için sağlanır.

- **Oluşturma** -bir havuza katılmayı henüz başlatılmamış, Azure ile ayrılmış bir VM.
- **Boşta** -Şu anda bir görevi çalıştırmayan kullanılabilir bir işlem düğümü.
- **Leavingpool** -Kullanıcı açıkça kaldırıldığı veya havuz yeniden boyutlandırılırken ya da otomatik olarak ölçeklendirildiği için havuzdan çıkarılan bir düğüm.
- **Çevrimdışı** -Batch 'in yeni görevleri zamanlamak Için kullandıı düğüm.
- **Ön,** Azure VM 'yi geri karşılayan için havuzdan kaldırılan düşük öncelikli bir düğüm. Düşük `preempted` öncelikli VM kapasitesini değiştirme kullanılabilir olduğunda bir düğüm yeniden başlatılır.
- Yeniden **başlatılıyor** -yeniden başlatılan bir düğüm.
- **Yeniden görüntüleme** -işletim sisteminin yeniden yüklendiği düğüm.
- **Çalışıyor** -bir veya daha fazla görev (başlangıç görevi dışında) çalıştıran bir düğüm.
- **Başlangıç** -Batch hizmetinin başladığı bir düğüm. 
- **Starttaskfailed** - [Başlangıç görevinin][rest_start_task] başarısız olduğu ve tüm yeniden denemeler `waitForSuccess` tükendi ve başlangıç görevinde ayarlanan bir düğüm. Düğüm, görevleri çalıştırmak için kullanılamaz.
- **Bilinmiyor** -Batch hizmetiyle iletişim kurmayan ve durumu bilinmeyen bir düğüm.
- **Kullanılamaz** -hatalar nedeniyle görev yürütmesi için kullanılamayan düğüm.
- **Waitingforstarttask** -başlangıç görevinin çalışmaya başladığı, ancak `waitForSuccess` ayarlandığı ve başlangıç görevinin tamamlanmadığında oluşan bir düğümdür.

Aşağıdaki C# kod parçacığında, geçerli hesaptaki tüm havuzlar için düğüm sayımlarını listeleme gösterilmektedir:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Aşağıdaki C# kod parçacığında, geçerli hesaptaki belirli bir havuzun düğüm sayımlarını listeleme gösterilmektedir.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Havuzlar için düğüm sayılarını almak üzere REST ve diğer desteklenen diller için de benzer bir model kullanabilirsiniz.
 
## <a name="next-steps"></a>Sonraki adımlar

* Batch hizmeti kavramları ve özellikler hakkında daha fazla bilgi edinmek için bkz. [Batch özelliklerine genel bakışı](batch-api-basics.md). Makale havuzlar, işlem düğümleri, işler ve görevler gibi birincil Batch kaynaklarını ele alır ve hizmetin özelliklerine genel bir bakış sağlar.

* Batch kaynaklarını listeleyerek sorgulara filtre uygulama hakkında daha fazla bilgi için bkz. [Batch kaynaklarını etkili bir şekilde listelemek için sorgular oluşturma](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

