---
title: Azure Service Fabric için Chaos ve yük devretme testleri oluşturma | Microsoft Docs
description: Service Fabric Chaos test ve yük devretme testi senaryolarını, bu hatalara ve hizmetlerinizin güvenilirliğini doğrulamaya yönelik olarak kullanma.
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 8eee7e89-404a-4605-8f00-7e4d4fb17553
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/1/2019
ms.author: motanv
ms.openlocfilehash: 2ea30b59e3195a0229c2584212e2897aaff4ee31
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718224"
---
# <a name="testability-scenarios"></a>Test edilebilirlik senaryoları
Bulut altyapıları gibi büyük dağıtılmış sistemler, doğal olarak güvenilir değildir. Azure Service Fabric, geliştiricilere güvenilir olmayan altyapıların üstünde çalışacak hizmetler yazma olanağı sağlar. Yüksek kaliteli hizmetleri yazmak için geliştiricilerin, hizmetlerinin kararlılığını test etmek üzere bu güvenilir olmayan altyapıya gelebilmesi gerekir.

Hata Analizi hizmeti, geliştiricilere hata durumunda test hizmetleri için hata eylemleri getirme yeteneği sağlar. Ancak, hedeflenen benzetimli hatalar size yalnızca şu ana kadar bir tane olur. Testi daha fazla yapmak için, Service Fabric: bir Chaos testi ve bir yük devretme testi olan test senaryolarını kullanabilirsiniz. Bu senaryolar, sürekli olarak araya eklemeli hataların, küme genelinde uzun süre boyunca düzgün şekilde ve düzgün bir şekilde benzetiminin benzetimini yapar. Bir test, hata oranı ve türüyle yapılandırıldıktan sonra, kümede ve hizmetinizde hata oluşturmak için C# API 'Ler veya PowerShell aracılığıyla başlatılabilir.

> [!WARNING]
> ChaosTestScenario, daha esnek, hizmet tabanlı bir Chaos tarafından değiştiriliyor. Daha fazla ayrıntı için lütfen bkz. [denetlenen Chaos](service-fabric-controlled-chaos.md) makalesine başvurun.
> 
> 

## <a name="chaos-test"></a>Chaos testi
Chaos senaryosu tüm Service Fabric kümesi genelinde hata üretir. Senaryo, genellikle aylar veya yıllar içinde görülen hataları birkaç saat sıkıştırır. Yüksek hata oranı ile araya eklemeli hataların birleşimi, aksi takdirde eksik olan köşe durumlarını bulur. Bu, hizmetin kod kalitesindeki önemli bir Gelişe yol açar.

### <a name="faults-simulated-in-the-chaos-test"></a>Chaos testinde sanal hatalar
* Bir düğümü yeniden başlatma
* Dağıtılan bir kod paketini yeniden başlatma
* Bir çoğaltmayı kaldırma
* Çoğaltmayı yeniden başlatma
* Birincil çoğaltmayı taşıma (isteğe bağlı)
* İkincil çoğaltmayı taşıma (isteğe bağlı)

Chaos testi, belirli bir süre boyunca hata ve küme doğrulamaları için birden çok yineleme çalıştırır. Kümenin sabitlenemez ve doğrulamanın başarılı olması için harcanan süre de yapılandırılabilir. Küme doğrulamasında tek bir hata oluştuğunda senaryo başarısız olur.

Örneğin, en fazla üç eşzamanlı hata ile bir saat boyunca çalışacak bir test kümesi düşünün. Test üç hatayı alacak ve ardından Küme durumunu doğrulayacaktır. Test, küme sağlıksız veya bir saat geçtiğinde önceki adımda yinelenir. Küme herhangi bir yinelemede sağlıksız hale gelirse, yani yapılandırılmış bir süre içinde sabitlemez, test bir özel durumla başarısız olur. Bu özel durum bir şeyin yanlış geçmiş olduğunu ve daha fazla araştırma gerektiğini gösterir.

Geçerli formunda, Chaos testinde hata oluşturma altyapısı yalnızca güvenli hatalara sahiptir. Bu, dış hataların yokluğu, bir çekirdeğin veya veri kaybının hiçbir şekilde gerçekleşmeyeceği anlamına gelir.

### <a name="important-configuration-options"></a>Önemli yapılandırma seçenekleri
* **Timetorun**: testin başarılı olmadan bitmeden önce çalışacağı toplam süre. Sınama, doğrulama hatası yerine daha önce bitebilirler.
* **Maxclustersabitlemesi zaman aşımı**: test başarısız olmadan önce kümenin sağlıklı olması için beklenecek en uzun süre. Gerçekleştirilen denetimler, küme durumunun Tamam, hizmet durumu Tamam, hedef çoğaltma kümesi boyutu hizmet bölümü için sağlanır ve hiçbir InBuild çoğaltması yok.
* **Maxconcurrentfaults**: her yinelemede en fazla sayıda eş zamanlı hata oluşturuldu. Sayı arttıkça, test daha karmatı, bu nedenle daha karmaşık yük devretme ve geçiş birleşimleri elde edilir. Sınama, bu yapılandırmanın ne kadar yüksek olduğunu fark etmeksizin, dış hataların yokluğunda bir çekirdek veya veri kaybı olmayacaktır.
* **Enablemovereperepfaults**: birincil veya ikincil çoğaltmaların taşınmasına neden olan hataları etkinleştirilir veya devre dışı bırakır. Bu hatalar varsayılan olarak devre dışıdır.
* **Waittimebetweenyinelemelerde**: bir hata ve karşılık gelen doğrulama sonrasında yinelemeler arasında beklenecek süre.

### <a name="how-to-run-the-chaos-test"></a>Chaos testini çalıştırma
C# örneği

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

Service Fabric PowerShell modülü, bir Chaos senaryosunu başlatmak için iki yol içerir. `Invoke-ServiceFabricChaosTestScenario`, istemci tabanlıdır ve istemci makinenin testi üzerinden kapatması durumunda başka bir hata ortaya alınmaz. Alternatif olarak, makineyi makinenin kapatılmasını durumunda çalıştırmaya devam eden bir komut kümesi vardır. `Start-ServiceFabricChaos`, FaultAnalysisService adlı bir durum bilgisi olan ve güvenilir bir sistem hizmeti kullanır ve bu işlem, TimeToRun tamamlanana kadar hataların ortaya çıkmamasını sağlar. `Stop-ServiceFabricChaos`, senaryoyu el ile durdurmak için kullanılabilir ve `Get-ServiceFabricChaosReport` bir rapor elde eder. Daha fazla bilgi için bkz. [Azure Service Fabric PowerShell Başvurusu](https://docs.microsoft.com/powershell/module/servicefabric/?view=azureservicefabricps) ve [Service Fabric kümelerinde denetlenen Chaos](service-fabric-controlled-chaos.md)'ı alma.

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Yük devretme testi
Yük devretme testi senaryosu, belirli bir hizmet bölümünü hedefleyen bir Chaos test senaryosu sürümüdür. Diğer hizmetleri etkilemeden, belirli bir hizmet bölümünde yük devretme etkisini sınar. Hedef bölüm bilgileri ve diğer parametrelerle yapılandırıldıktan sonra, hizmet bölümü için hata oluşturmak üzere C# API 'Leri veya PowerShell kullanan bir istemci tarafı araç olarak çalışır. Senaryo, iş mantığınızın bir iş yükü sağlamak üzere tarafında çalıştığı bir dizi sanal hata ve hizmet doğrulaması boyunca yinelenir. Hizmet doğrulamasında bir hata, daha fazla araştırma gerektiren bir sorunu gösterir.

### <a name="faults-simulated-in-the-failover-test"></a>Yük devretme testinde benzetilmiş hata
* Bölümün barındırıldığı dağıtılan bir kod paketini yeniden başlatın
* Birincil/ikincil çoğaltma veya durum bilgisi olmayan örneği kaldırma
* Birincil ikincil çoğaltmayı yeniden başlatın (kalıcı bir hizmet varsa)
* Birincil çoğaltmayı taşıma
* İkincil bir çoğaltmayı taşıma
* Bölümü yeniden başlatın

Yük devretme testi seçili bir hata alır ve daha sonra kararlılığını sağlamak için hizmette doğrulama çalıştırır. Yük devretme testi, tek seferde yalnızca bir hata olduğunu ve Chaos testinde olası birden çok hata olduğunu belirtir. Hizmet bölümü her bir hata sonrasında yapılandırılan zaman aşımı süresi içinde sabitlemezse, test başarısız olur. Test yalnızca güvenli hatalara sahiptir. Bu, dış hataların yokluğu, bir çekirdeğin veya veri kaybının gerçekleşmeyeceği anlamına gelir.

### <a name="important-configuration-options"></a>Önemli yapılandırma seçenekleri
* **Partitionselector**: hedeflenmesi gereken bölümü belirten Seçici nesne.
* **Timetorun**: testin bitmeden önce çalışacağı toplam süre.
* **Maxservicestabilizationtimeout**: test başarısız olmadan önce kümenin sağlıklı olması için beklenecek en uzun süre. Gerçekleştirilen denetimler, hizmet sistem durumunun tamam olup olmadığı, tüm bölümler için hedef çoğaltma kümesi boyutunun elde edilip edilmeyeceğini ve hiçbir InBuild çoğaltması yok.
* **Waittimebetweenfaults**: her hata ve doğrulama çevrimi arasında beklenecek süre.

### <a name="how-to-run-the-failover-test"></a>Yük devretme testini çalıştırma
**C#**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
