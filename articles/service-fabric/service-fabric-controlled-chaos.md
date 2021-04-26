---
title: Service Fabric kümelerinde endüce Chaos
description: Kümedeki Chaos 'yi yönetmek için hata ekleme ve küme analizi hizmeti API 'Lerini kullanma.
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 759e2d1c8d2a326583625fbbbcadb4f4fa950510
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732440"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Service Fabric kümelerinde ı, kontrollü Chaos
Bulut altyapıları gibi büyük ölçekli dağıtılmış sistemler, doğal olarak güvenilir değildir. Azure Service Fabric, geliştiricilerin güvenilir olmayan bir altyapının üzerine güvenilir dağıtılmış hizmetler yazmasını sağlar. Güvenilir olmayan bir altyapının üzerine sağlam dağıtılmış hizmetler yazmak için, geliştiricilerin hizmetin kararlılığını test edebilmeleri gerekir, çünkü temeldeki güvenilir olmayan altyapı hatalar nedeniyle karmaşık durum geçişleri ile devam edebilir.

[Hata ekleme ve küme analizi hizmeti](./service-fabric-testability-overview.md) (hata analizi hizmeti olarak da bilinir), geliştiricilere hizmetlerini test etmek için hata alma olanağı sağlar. [Bir bölümü yeniden başlatma](/powershell/module/servicefabric/start-servicefabricpartitionrestart)gibi hedeflenmiş bu sanal hatalar, en yaygın durum geçişlerini çalıştırmaya yardımcı olabilir. Ancak hedeflenen benzetimli hatalar tanımına göre gezinilmiştir ve bu nedenle yalnızca donanımdan tahmin edilen, uzun ve karmaşık durum geçişleri sırasını gösteren hataları kaçırmayabilir. Taraflı olmayan bir test için Chaos kullanabilirsiniz.

Chaos, küme boyunca uzun süre boyunca düzenli ve araya eklemeli hataların (hem düzgün kapanma hem de düzgün şekilde değil) benzetimini yapar. Hatalı bir hata, bir Service Fabric API çağrısı kümesinden oluşur; Örneğin, yeniden başlatma çoğaltma hatası, bu bir yakın ve bir çoğaltma üzerinde açık olan bir kapatma olduğundan, kapanma hatası. Çoğaltmayı kaldırın, birincil çoğaltmayı taşıyın, ikincil çoğaltmayı taşıyın ve taşıma örneği, Chaos tarafından uygulanan diğer düzgün çalışan hatalardır. Yeniden başlatma düğümü ve kod paketini yeniden Başlat gibi düzgün olmayan hatalar işlemden çıkılıyor.

Chaos 'ı hız ve hata türüyle yapılandırdıktan sonra, kümede ve hizmetinizdeki hataları oluşturmaya başlamak için C#, Powershell veya REST API aracılığıyla Chaos 'i başlatabilirsiniz. Chaos 'i belirli bir süre için (örneğin, bir saat) çalışacak şekilde yapılandırabilir, sonra da her bir Chaos otomatik olarak durur veya StopChaos API 'yi (C#, Powershell veya REST) dilediğiniz zaman durdurabilirsiniz.

> [!NOTE]
> Şu anki biçimde, Chaos yalnızca güvenli hatalara sahiptir; bu, dış hataların bir çekirdek kaybına karşı yokluğunda olduğunu veya veri kaybını hiçbir şekilde gerçekleşmemesi anlamına gelir.
>

Chaos çalışırken, çalışma durumunu şu anda yakalayan farklı olaylar oluşturur. Örneğin, bir ExecutingFaultsEvent, Chaos 'in bu yinelemede yürütmeye karar verdiği tüm hataları içerir. Bir ValidationFailedEvent, kümenin doğrulanması sırasında bulunan doğrulama hatasının (sistem durumu veya kararlılık sorunları) ayrıntılarını içerir. Chaos çalıştırmaları raporunu almak için GetChaosReport API 'sini (C#, Powershell veya REST) çağırabilirsiniz. Bu olaylar, iki yapılandırma tarafından dikte edilen bir kesme ilkesine sahip [güvenilir bir sözlükte](./service-fabric-reliable-services-reliable-collections.md)kalıcı hale gelir: **MaxStoredChaosEventCount** (varsayılan değer 25000) ve **Storedadctioncleanupıntervalınseconds** (varsayılan değer 3600 ' dir). Her *Storedadctioncleanupıntervalınseconds* Chaos denetimleri ve hepsi ancak en son *MaxStoredChaosEventCount* olayları, güvenilir sözlükten temizlenir.

## <a name="faults-induced-in-chaos"></a>Chaos 'de bulunan hatalar
Chaos, tüm Service Fabric kümesi genelinde hata oluşturur ve ayda veya yılda görülen hataları birkaç saat içinde sıkıştırır. Yüksek hata oranı ile araya eklemeli hataların birleşimi, aksi takdirde Kaçırılabilecek köşe durumlarını bulur. Bu Chaos, hizmetin kod kalitesindeki önemli bir Gelişe yol açar.

Aşağıdaki kategorilerden oluşan Chaos hatası:

* Bir düğümü yeniden başlatma
* Dağıtılan bir kod paketini yeniden başlatma
* Bir çoğaltmayı kaldırma
* Çoğaltmayı yeniden başlatma
* Birincil çoğaltmayı taşıma (yapılandırılabilir)
* İkincil çoğaltmayı taşıma (yapılandırılabilir)
* Örnek taşıma

Chaos birden çok yinelemede çalışır. Her yineleme, belirtilen dönem için hata ve küme doğrulamasından oluşur. Kümenin sabitlenemez ve doğrulamanın başarılı olması için harcanan süreyi yapılandırabilirsiniz. Küme doğrulamasında bir hata bulunursa, Chaos UTC zaman damgasına ve hata ayrıntılarına sahip bir ValidationFailedEvent üretir ve devam ettirir. Örneğin, en fazla üç eş zamanlı hata içeren bir saat için çalışacak şekilde ayarlanmış bir Chaos örneğini düşünün. Chaos üç hata ile aynıdır ve sonra küme durumunu doğrular. Bu, StopChaosAsync API 'SI veya bir saatlik geçişlerinde açıkça durduruluncaya kadar önceki adımda yinelenir. Küme herhangi bir yinelemede sağlıksız hale gelirse (yani, sabitlemez veya geçilen Maxclusterilemintimeout süresi içinde sağlıklı olmaz), Chaos bir ValidationFailedEvent üretir. Bu olay bir şeyin yanlış geçmiş olduğunu ve daha fazla araştırma gerektirebileceğini gösterir.

Hangi hataların kurulu olduğunu almak için GetChaosReport API 'yi (PowerShell, C# veya REST) kullanabilirsiniz. API, geçirilen devamlılık belirtecine veya geçirilen zaman aralığına göre Chaos raporunun sonraki segmentini alır. Chaos raporunun bir sonraki segmentini almak için ContinuationToken ' ı belirtebilir veya StartTimeUtc ve EndTimeUtc aracılığıyla zaman aralığını belirtebilirsiniz ancak aynı çağrıda hem ContinuationToken hem de zaman aralığını belirtemezsiniz. 100 ' den fazla Chaos olayı olduğunda, bir segmentin en fazla 100 Chaos olayı içerdiği kesimlerde Chaos raporu döndürülür.

## <a name="important-configuration-options"></a>Önemli yapılandırma seçenekleri
* **Timetorun**: başarılı ile tamamlanmadan önce Chaos 'nin çalıştığı toplam süre. StopChaos API 'si aracılığıyla TimeToRun dönemi için çalıştırılmadan önce Chaos 'yi durdurabilirsiniz.

* **Maxclusterileizationtimeout**: bir ValidationFailedEvent oluşturmadan önce kümenin sağlıklı olması için beklenecek en uzun süre. Bu bekleme, kurtarma sırasında kümedeki yükü azaltmaktır. Gerçekleştirilen denetimler şunlardır:
  * Küme durumu tamam ise
  * Hizmet durumu tamam ise
  * Hizmet bölümü için hedef çoğaltma kümesi boyutu elde edilir
  * Hiç bir InBuild çoğaltması yok
* **Maxconcurrentfaults**: her yinelemede alınmış en fazla eşzamanlı hata sayısı. Sayı arttıkça, daha Agresif Chaos ve yük devretme işlemleri ve kümenin gittiği durum geçiş birleşimleri de daha karmaşıktır. 

> [!NOTE]
> Bir değer *Maxconcurrenthatalarının* ne kadar yüksek olduğuna bakılmaksızın, Chaos garantisi-dış hataların yokluğunda, çekirdek kaybı veya veri kaybı yoktur.
>

* **Enablemovereperepfaults**: birincil, ikincil çoğaltmaların veya örneklerin taşınmasına neden olan hataları sağlar veya devre dışı bırakır. Bu hatalar varsayılan olarak etkindir.
* **Waittimebetweenyinelemelerde**: yinelemeler arasında beklenecek süre. Diğer bir deyişle, bir hata yuvarlama gerçekleştirdikten ve kümenin sistem durumunun ilgili doğrulanmasını tamamladıktan sonra Chaos 'in duraklayabileceği zaman miktarı. Değer arttıkça, daha düşük ortalama hata ekleme hızıdır.
* **Waittimebetweenfaults**: tek bir yinelemede art arda iki hata arasında beklenecek süre. Değer arttıkça, (veya arasındaki örtüşme) arızalarını azaltır.
* **Clusterhealthpolicy**: küme durumu Ilkesi, Chaos yinelemeleri arasındaki kümenin sistem durumunu doğrulamak için kullanılır. Küme sistem durumu hata durumunda veya hata yürütmesi sırasında beklenmeyen bir özel durum oluşursa, Chaos bir sonraki sistem durumu denetiminin kümeye bir süre sonra sağlanması için önce 30 dakika bekler.
* **Bağlam**: (dize, dize) türünde anahtar-değer çiftleri koleksiyonu. Eşleme, Chaos çalıştırması hakkında bilgi kaydetmek için kullanılabilir. Bu tür çiftler üzerinde 100 daha fazla olamaz ve her dize (anahtar veya değer) en fazla 4095 karakter uzunluğunda olabilir. Bu harita, yönetim paketi tarafından, isteğe bağlı olarak belirli bir çalıştırma hakkındaki bağlamı depolamak için bir başlangıç tarafından ayarlanır.
* **Chaostargetfilter**: Bu filtre, yalnızca belirli düğüm türlerine veya yalnızca belirli uygulama örneklerine olan Chaos hatalarını hedeflemek için kullanılabilir. ChaosTargetFilter kullanılmazsa, Chaos tüm küme varlıklarını hatalara neden olur. ChaosTargetFilter kullanılırsa, Chaos yalnızca ChaosTargetFilter belirtimini karşılayan varlıkları hatalara neden olur. Nodetypeınary ıonlist ve Applicationınary ıonlist yalnızca birleşim semantiğine izin veriyor. Diğer bir deyişle, NodeType, ıonlist ve Applicationınary ıonlist 'in bir kesişimini belirtmek mümkün değildir. Örneğin, "Bu uygulama yalnızca bu düğüm türünde olduğunda hata." belirtmek mümkün değildir. Bir varlık Nodetypeary ıonlist veya Applicationary ıonlist öğesine eklendikten sonra, bu varlık ChaosTargetFilter kullanılarak dışlanamaz. ApplicationX, Applicationlarionlist içinde görünmese de, bazı Chaos yineleme applicationX içinde, Nodetypelarionlist içinde yer alan nodeTypeY düğümünde yer aldığı için hata oluşabilir. Hem Nodetypeary ıonlist hem de Applicationary ıonlist null ya da boşsa, bir ArgumentException atılır.
    * **Nodetypeınary ıonlist**: Chaos hatalarına dahil edilecek düğüm türlerinin bir listesi. Tüm hata türleri (düğümü yeniden Başlat, CodePackage 'i yeniden Başlat, çoğaltmayı kaldır, çoğaltmayı yeniden Başlat, birincil taşıma, ikincil taşıma ve taşıma örneği) bu düğüm türlerinin düğümleri için etkinleştirilir. Bir NodeType (bir NodeTypeX) Nodetypeary ıonlist içinde görünmezse, ardından, düğüm düzeyi hataları (NodeRestart gibi) NodeTypeX düğümleri için hiçbir şekilde etkinleştirilmez, ancak Applicationlarionlist içindeki bir uygulama NodeTypeX düğümünde yer alıyorsa, kod paketi ve çoğaltma hataları NodeTypeX için de etkinleştirilebilir. En çok 100 düğüm türü adı bu listeye dahil edilebilir, bu sayıyı artırmak için Maxnumberofnodetypesınchaostargetfilter yapılandırması için bir yapılandırma yükseltmesi gerekir.
    * **Applicationınary ıonlist**: Chaos hatalarına dahil edilecek uygulama URI 'lerinin listesi. Bu uygulamaların hizmetlerine ait tüm çoğaltmalar çoğaltma hatalarını (çoğaltmayı yeniden Başlat, çoğaltmayı kaldırma, birincil taşıma, ikincil taşıma ve örnek taşıma) Chaos tarafından düzeltilir. Chaos yalnızca kod paketi yalnızca bu uygulamaların çoğaltmalarını barındırıyorsa bir kod paketini yeniden başlatabilir. Bir uygulama bu listede görünmezse, uygulama Nodetypeary ıonlist içine dahil edilen düğüm türünün bir düğümünde sonlanıyorsa, bazı Chaos yinelemeyle yine de hatalı olabilir. Ancak, applicationX, yerleştirme kısıtlamaları aracılığıyla nodeTypeY 'e bağlıysa ve applicationX 'in Applicationary ıonlist öğesinden ve nodeTypeY 'in Nodetypeary ıonlist öğesinden Ikisi de yoksa, applicationX hiçbir şekilde hata olmayacaktır. En fazla 1000 uygulama adı bu listeye eklenebilir, bu sayıyı artırmak için MaxNumberOfApplicationsInChaosTargetFilter yapılandırması için bir yapılandırma yükseltmesi gerekir.

## <a name="how-to-run-chaos"></a>Chaos nasıl çalıştırılır?

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster
            // entities. 
            // During validation if a cluster entity is not stable and healthy within
            // MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex
            // series of states to uncover bugs.
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be
            // used to record information about the Chaos run. There cannot be more than 100 such pairs and
            // each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the
            // lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration.
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of
            // state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary
            // replica, move secondary replica, and move instance will happen for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList,
            // restart code package, restart replica, move primary replica, move secondary replica,
            //  and move instance faults will happen for 'fabric:/TestApp2' even if a replica or code
            // package from 'fabric:/TestApp2' is residing on a node which is not of type included
            // in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy.
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities.
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds,
# Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration.
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of
# states to uncover bugs.
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the
# fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration.
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state
# transitions that the cluster goes through.
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record
# information about the Chaos run.
# There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move
# secondary replica will happen for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for
# 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is
# not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
