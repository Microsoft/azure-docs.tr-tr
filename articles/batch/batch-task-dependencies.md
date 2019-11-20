---
title: Görevleri diğer görevlerin tamamlanmasına göre çalıştırmak için görev bağımlılıklarını kullanın-Azure Batch | Microsoft Docs
description: Azure Batch, MapReduce stilini ve benzer büyük veri iş yüklerini işlemeye yönelik diğer görevlerin tamamlanmasına bağlı görevler oluşturun.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a1378a5c00acbbce5e7ec73a75902ec55140575
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094614"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Diğer görevlere bağımlı görevleri çalıştırmak için görev bağımlılıkları oluşturma

Bir görevi veya görev kümesini yalnızca bir üst görev tamamlandıktan sonra çalıştırmak için görev bağımlılıklarını tanımlayabilirsiniz. Görev bağımlılıklarının yararlı olduğu bazı senaryolar şunlardır:

* Buluttaki MapReduce stili iş yükleri.
* Veri işleme görevleri, yönlendirilmiş bir çevrimsiz grafik (DAG) olarak ifade edilebilir işler.
* Sonraki görev başlamadan önce her görevin tamamlaması gereken, önceden işleme ve işleme sonrası süreçler.
* Aşağı akış görevlerinin yukarı akış görevlerinin çıktısına bağlı olduğu diğer işler.

Batch görevi bağımlılıklarıyla, bir veya daha fazla üst görevin tamamlanmasından sonra işlem düğümlerinde yürütmeye zamanlanan görevler oluşturabilirsiniz. Örneğin, her bir 3B filmin çerçevesini ayrı ve paralel görevlerle işleyen bir iş oluşturabilirsiniz. Son görev--"birleştirme görevi"--tüm çerçeveler başarıyla işlendikten sonra, işlenen kareleri tam filmle birleştirir.

Varsayılan olarak, bağımlı görevler yalnızca üst görev başarıyla tamamlandıktan sonra yürütme için zamanlanır. Varsayılan davranışı geçersiz kılmak için bir bağımlılık eylemi belirtebilir ve üst görev başarısız olduğunda görevleri çalıştırabilirsiniz. Ayrıntılar için [bağımlılık eylemleri](#dependency-actions) bölümüne bakın.  

Bire bir veya bire çok ilişkisinde diğer görevlere bağımlı görevler oluşturabilirsiniz. Ayrıca, bir görevin belirli bir görev kimliği aralığı içindeki bir görev grubunun tamamlanmasına bağlı olduğu bir Aralık bağımlılığı da oluşturabilirsiniz. Çoktan çoğa ilişkiler oluşturmak için bu üç temel senaryoyu birleştirebilirsiniz.

## <a name="task-dependencies-with-batch-net"></a>Batch .NET ile görev bağımlılıkları
Bu makalede, [Batch .net][net_msdn] kitaplığı 'nı kullanarak görev bağımlılıklarını yapılandırmayı tartıştık. Önce, işlerinizde [görev bağımlılığını](#enable-task-dependencies) nasıl etkinleştireceğinizi ve ardından [bağımlılıklara sahip bir görevin nasıl yapılandırılacağını](#create-dependent-tasks)gösterir. Ayrıca, üst öğe başarısız olursa bağımlı görevleri çalıştırmak için bir bağımlılık eyleminin nasıl belirtildüğüne de açıklıyoruz. Son olarak, Batch 'nin desteklediği [bağımlılık senaryolarını](#dependency-scenarios) tartıştık.

## <a name="enable-task-dependencies"></a>Görev bağımlılıklarını etkinleştir
Batch uygulamanızda görev bağımlılıklarını kullanmak için, önce görevi görev bağımlılıklarını kullanacak şekilde yapılandırmanız gerekir. Batch .NET sürümünde, [Usestaskdependencies][net_usestaskdependencies] özelliğini olarak `true`ayarlayarak [cloudişiniz][net_cloudjob] üzerinde etkinleştirin:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Yukarıdaki kod parçacığında, "batchClient" [batchclient][net_batchclient] sınıfının bir örneğidir.

## <a name="create-dependent-tasks"></a>Bağımlı görevler oluşturma
Bir veya daha fazla üst görevin tamamlanmasına bağlı bir görev oluşturmak için, görevin diğer görevlere "bağlı" olduğunu belirtebilirsiniz. Batch .NET sürümünde [Cloudtask][net_cloudtask]öğesini yapılandırın. [Task Dependencies][net_taskdependencies] sınıfının bir örneğiyle [bağımlıdson][net_dependson] özelliği:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Bu kod parçacığı, görev KIMLIĞI "çiçekler" olan bağımlı bir görev oluşturur. "Çiçekler" görevi "yağmur" ve "Sun" görevlerine bağlıdır. Görev "çiçekler", yalnızca "yağmur" ve "Sun" görevleri başarıyla tamamlandıktan sonra bir işlem düğümünde çalışacak şekilde zamanlanır.

> [!NOTE]
> Varsayılan olarak, bir görevin **tamamlandı** durumunda olduğu ve **Çıkış kodu** olduğu `0`zaman başarıyla tamamlandı olarak kabul edilir. Batch .NET sürümünde bu, [Cloudtask][net_cloudtask]anlamına gelir. [Durum][net_taskstate] özelliği değeri `Completed` ve cloudtask 'ın [taskexecutionınformation][net_taskexecutioninformation].[ ExitCode][net_exitcode] Özellik değeri `0`. Bunun nasıl değiştirileceği için [bağımlılık eylemleri](#dependency-actions) bölümüne bakın.
> 
> 

## <a name="dependency-scenarios"></a>Bağımlılık senaryoları
Azure Batch kullanabileceğiniz üç temel görev bağımlılığı senaryosu vardır: bire bir, bire çok ve görev KIMLIĞI aralığı bağımlılığı. Bunlar, çok-çok dördüncü bir senaryoyu sağlamak için birleştirilebilir.

| Senaryon&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Örnek |  |
|:---:| --- | --- |
|  [Bire bir](#one-to-one) |*Taskb* , *Taska* 'a bağımlıdır <p/> *Taskb* , tasü başarıyla tamamlanana kadar yürütülmek üzere zamanlanmayacak |![Diyagram: bire bir görev bağımlılığı][1] |
|  [Bire çok](#one-to-many) |*görevC* hem *görevA* hem de *görevB*’ye bağlıdır <p/> *Taskc* , hem *Taska* hem de *taskb* başarıyla tamamlanana kadar yürütülmek üzere zamanlanmayacak |![Diyagram: bire çok görev bağımlılığı][2] |
|  [Görev KIMLIĞI aralığı](#task-id-range) |*Taskd* , bir dizi göreve bağlıdır <p/> *1* ile *10* arasındaki görevler başarıyla tamamlanana kadar *taskd* yürütme için zamanlanmayacak |![Çizimindeki Görev kimliği aralığı bağımlılığı][3] |

> [!TIP]
> C, D, E ve F görevlerinin A ve B görevlerine bağlı olduğu durumlar gibi **çoktan çoğa** ilişkiler de oluşturabilirsiniz. Bu, örneğin, aşağı akış görevlerinizin birden çok yukarı akış görevinin çıktısına bağlı olduğu paralel ön işleme senaryolarında yararlıdır.
> 
> Bu bölümdeki örneklerde, bağımlı bir görev yalnızca üst görevler başarıyla tamamlandıktan sonra çalışır. Bu davranış, bağımlı bir görev için varsayılan davranıştır. Bir üst görev başarısız olduktan sonra, varsayılan davranışı geçersiz kılmak için bir bağımlılık eylemi belirterek, bağımlı bir görevi çalıştırabilirsiniz. Ayrıntılar için [bağımlılık eylemleri](#dependency-actions) bölümüne bakın.

### <a name="one-to-one"></a>Bire bir
Bire bir ilişkide, bir görev bir üst görevin başarıyla tamamlanmasına bağlıdır. Bağımlılığı oluşturmak için, [Taskdependencies][net_taskdependencies]'e tek BIR görev kimliği sağlayın. [Cloudtask][net_cloudtask]'ın [bağımlıdson][net_dependson] özelliğini doldurduğunuzda [onıd][net_onid] static yöntemi.

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Bire çok
Bire çok ilişkisinde, bir görev birden çok üst görevin tamamlanmasına bağlıdır. Bağımlılığı oluşturmak için, [Taskdependencies][net_taskdependencies]görev kimliklerinin bir koleksiyonunu sağlayın. [Cloudtask][net_cloudtask]'ın [bağımlıdson][net_dependson] özelliğini doldurduğunuzda [onıds][net_onids] statik yöntemi.

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
``` 

### <a name="task-id-range"></a>Görev KIMLIĞI aralığı
Bir üst görev aralığına bir bağımlılık içinde, bir görev, kimlikleri bir Aralık içinde olan görevlerin tamamlanmasına bağlıdır.
Bağımlılığı oluşturmak için, [Taskdependencies][net_taskdependencies]aralığındaki ilk ve son görev kimliklerini sağlayın. [Cloudtask][net_cloudtask]'ın [bağımlıdson][net_dependson] özelliğini doldurduğunuzda [onıdrange][net_onidrange] statik yöntemi.

> [!IMPORTANT]
> Bağımlılıklarınız için görev KIMLIĞI aralıklarını kullandığınızda, yalnızca tamsayı değerlerini temsil eden kimlikleri olan görevler Aralık tarafından seçilir. Bu nedenle, `1..10` Aralık `3` `7`görevleri seçer, ancak değil `5flamingoes`. 
> 
> Aralık bağımlılıkları değerlendirilirken öndeki sıfırlar önemli değildir, bu nedenle `4`dize tanımlayıcıları `04` olan görevler, ve `004` hepsi Aralık *dahilinde* olur ve hepsi görev `4`olarak değerlendirilir ve bu nedenle ilki tamamlamak için bağımlılığı karşılar.
> 
> Aralıktaki her görevin, başarıyla tamamlanarak ya da **karşılamak**üzere ayarlanmış bir bağımlılık eylemine eşlenmiş bir hata ile tamamlayarak bağımlılığı karşılaması gerekir. Ayrıntılar için [bağımlılık eylemleri](#dependency-actions) bölümüne bakın.
>
>

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Bağımlılık eylemleri

Varsayılan olarak, bağımlı bir görev veya görev kümesi yalnızca bir üst görev başarıyla tamamlandıktan sonra çalışır. Bazı senaryolarda, üst görev başarısız olsa bile bağımlı görevleri çalıştırmak isteyebilirsiniz. Bağımlılık eylemi belirterek varsayılan davranışı geçersiz kılabilirsiniz. Bağımlılık eylemi, ana görevin başarısı veya başarısızlığı temelinde bağımlı bir görevin çalıştırılmasına uygun olup olmadığını belirtir. 

Örneğin, bağımlı bir görevin, yukarı akış görevinin tamamlanmasında veri beklediğini varsayalım. Yukarı akış görevi başarısız olursa, bağımlı görev hala eski verileri kullanarak çalıştırılabilir. Bu durumda, bir bağımlılık eylemi bağımlı görevin, ana görevin başarısız olmasına rağmen çalışmaya uygun olduğunu belirtebilir.

Bağımlılık eylemi, üst görevin çıkış koşulunu temel alır. Aşağıdaki çıkış koşullarından herhangi biri için bir bağımlılık eylemi belirtebilirsiniz; .NET için Ayrıntılar için bkz. [Exitconditions][net_exitconditions] Sınıfı:

- Bir ön işleme hatası oluştuğunda.
- Bir dosya karşıya yükleme hatası oluştuğunda. Görev, **ExitCodes** veya **Exitcoderanges**ile belirtilen bir çıkış koduyla karşılaşıyorsa ve karşıya dosya yükleme hatasıyla karşılaşırsa, çıkış kodu tarafından belirtilen eylem önceliklidir.
- Görev, **ExitCodes** özelliği tarafından tanımlanan bir çıkış kodu ile çıktığında.
- Görev, **Exitcoderanges** özelliği tarafından belirtilen bir aralık dahilinde çıkış kodu ile çıktığında.
- Varsayılan durum, görev **ExitCodes** veya **Exitcoderanges**tarafından tanımlanmayan bir çıkış kodu ile çıkış yaptığında veya görev bir ön Işleme hatasıyla çıkarsa ve **PreProcessingError** özelliği ayarlanmamışsa veya görev bir dosya yükleme hatasıyla başarısız olursa ve **Fileuploaderror** özelliği ayarlanmadı. 

.NET ' te bir bağımlılık eylemi belirtmek için [Exitoptions][net_exitoptions]' ı ayarlayın. Çıkış koşulunun [DependencyAction][net_dependencyaction] özelliği. **DependencyAction** özelliği iki değerden birini alır:

- **DependencyAction** özelliğinin uygun olarak ayarlanması , üst görev belirtilen bir hatayla sonlandırılmadığı takdirde bağımlı görevlerin çalışmaya uygun olduğunu gösterir.
- **DependencyAction** özelliğinin **blok** olarak ayarlanması bağımlı görevlerin çalıştırmaya uygun olmadığını gösterir.

**DependencyAction** özelliği için varsayılan ayar, çıkış kodu 0 ve diğer tüm çıkış koşulları için **engellenir** .

Aşağıdaki kod parçacığı, bir üst görev için **DependencyAction** özelliğini ayarlar. Üst görev bir ön işleme hatasıyla veya belirtilen hata kodlarıyla çıkış içeriyorsa, bağımlı görev engellenir. Üst görev sıfır olmayan başka bir hatayla sonlandıysanız, bağımlı görev çalışmaya uygundur.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Kod örneği
[Taskdependencies][github_taskdependencies] örnek projesi, GitHub 'daki [Azure Batch kod örneklerinden][github_samples] biridir. Bu Visual Studio çözümü şunları gösterir:

- Bir işte görev bağımlılığını etkinleştirme
- Diğer görevlere bağımlı görevler oluşturma
- Bu görevleri işlem düğümleri havuzunda yürütme.

## <a name="next-steps"></a>Sonraki adımlar
### <a name="application-deployment"></a>Uygulama dağıtımı
Batch 'in [uygulama paketleri](batch-application-packages.md) özelliği, görevlerinizin işlem düğümlerinde yürüteceği uygulamaların dağıtılması ve sürümü için kolay bir yol sağlar.

### <a name="installing-applications-and-staging-data"></a>Uygulamaları yükleme ve verileri hazırlama
Düğümlerinizi görevleri çalıştıracak şekilde hazırlama yöntemlerine genel bakış için, bkz. Azure Batch forumundaki [Batch işlem düğümlerinde uygulamalar yükleme ve veri hazırlama][forum_post] . Azure Batch takım üyelerinden biri tarafından yazılan bu gönderi, uygulamaları, görev girişi verilerini ve diğer dosyaları işlem düğümleriniz üzerine kopyalamanın farklı yollarına bir örnektir.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_exitconditions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diyagram: bire bir bağımlılık"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diyagram: bire çok bağımlılığı"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diyagram: görev kimliği aralığı bağımlılığı"
