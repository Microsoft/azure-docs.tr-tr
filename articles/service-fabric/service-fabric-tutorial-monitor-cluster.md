---
title: Azure 'da bir Service Fabric kümesini izleme | Microsoft Docs
description: Bu öğreticide, Service Fabric olaylarını görüntüleyerek, EventStore API 'Lerini sorgulayarak, performans sayaçlarını izleyerek ve sistem durumu raporlarını görüntüleyerek bir kümeyi izlemeyi öğreneceksiniz.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: srrengar
ms.custom: mvc
ms.openlocfilehash: 63069dcdebf19c64d7bcde298fa234622a6d9a2b
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385283"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Öğretici: Azure 'da bir Service Fabric kümesini izleme

İzleme ve tanılama, iş yüklerini herhangi bir bulut ortamında geliştirmek, test etmek ve dağıtmak için önemlidir. Bu öğretici, bir serinin ikinci bölümüdür ve olaylar, performans sayaçları ve sistem durumu raporları kullanarak bir Service Fabric kümesini nasıl izleyip tanıleyeceğinizi gösterir.   Daha fazla bilgi için [küme izleme](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) ve [altyapı izleme](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring)hakkında genel bakış konusunu okuyun.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Service Fabric olaylarını görüntüle
> * Küme olayları için EventStore API 'Lerini sorgulama
> * Altyapıyı izleme/performans sayaçlarını toplama
> * Küme durumu raporlarını görüntüleme

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Şablon kullanarak Azure 'da güvenli bir [Windows kümesi](service-fabric-tutorial-create-vnet-and-windows-cluster.md) oluşturma
> * Bir kümeyi izleme
> * [Bir kümenin ölçeğini daraltma veya genişletme](service-fabric-tutorial-scale-cluster.md)
> * [Bir kümenin çalışma zamanını yükseltme](service-fabric-tutorial-upgrade-cluster.md)
> * [Küme silme](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) veya [Azure CLI](/cli/azure/install-azure-cli)'yı yükler.
* Güvenli bir [Windows kümesi](service-fabric-tutorial-create-vnet-and-windows-cluster.md) oluşturma 
* Küme için [Tanılama toplamayı](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) ayarla
* Kümede [Eventstore hizmetini](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) etkinleştirin
* Küme için [Azure izleyici günlüklerini ve Log Analytics aracısını](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) yapılandırma

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Azure Izleyici günlüklerini kullanarak Service Fabric olaylarını görüntüleme

Azure Izleyici günlükleri, bulutta barındırılan uygulama ve hizmetlerden Telemetriyi toplayıp analiz eder ve kullanılabilirlik ve performansını en üst düzeye çıkarmanıza yardımcı olacak analiz araçları sağlar. Azure Izleyici günlüklerinde sorguları çalıştırarak, öngörülere ilişkin Öngörüler edinebilir ve sorun gidermeye devam edebilirsiniz.

Service Fabric Analytics çözümüne erişmek için, [Azure Portal](https://portal.azure.com) gidin ve Service Fabric Analytics çözümünü oluşturduğunuz kaynak grubunu seçin.

Kaynak **Servicefabric (hayal fomsworkspace)** öğesini seçin.

**Genel bakış** bölümünde, Service Fabric için de dahil olmak üzere her bir çözümün etkin olduğu bir grafik biçiminde kutucukları görürsünüz. Service Fabric Analytics çözümüne devam etmek için **Service Fabric** grafiğine tıklayın.

![Service Fabric çözümü](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

Aşağıdaki görüntüde Service Fabric Analytics çözümünün giriş sayfası gösterilmektedir. Bu giriş sayfası, kümenizde neler olduğunu bir anlık görüntü görünümü sağlar.

![Service Fabric çözümü](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Küme oluşturma sonrasında tanılamayı etkinleştirdiyseniz, için olayları görebilirsiniz 

* [Küme olaylarını Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors programlama modeli olayları](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services programlama modeli olayları](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Service Fabric olaylarını kutudan sonuna ek olarak, [Tanılama uzantınızın yapılandırması güncelleştirilerek](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)daha ayrıntılı sistem olayları toplanabilir.

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Düğümlerde eylemler dahil Service Fabric olaylarını görüntüleme

Service Fabric Analytics sayfasında, **küme olayları**grafiğine tıklayın.  Toplanan tüm sistem olaylarının günlükleri görünür. Bu, Azure depolama hesabındaki **Wadservicefabricsystemeventstable** ve benzer şekilde daha sonra gördüğünüz güvenilir hizmetler ve aktör olayları bu ilgili tablolardan alınan bir başvurudur.
    
![Işlem kanalını sorgula](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

Sorgu, aradığınızı iyileştirmek için değiştirebileceğiniz kusto sorgu dilini kullanır. Örneğin, kümedeki düğümlerde gerçekleştirilen tüm eylemleri bulmak için aşağıdaki sorguyu kullanabilirsiniz. Aşağıda kullanılan olay kimlikleri [İşlemsel kanal olayları başvurusunda](service-fabric-diagnostics-event-generation-operational.md)bulunur.

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Kusto sorgu dili güçlü. Diğer bazı faydalı sorgular aşağıda verilmiştir.

Sorguyu ServiceFabricEvent diğer adı ile bir işlev olarak kaydederek Kullanıcı tanımlı işlev olarak bir *servicefabricevent* arama tablosu oluşturun:

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

Son bir saat içinde kaydedilen işlem olaylarını Döndür:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

EventID = = 18604 ve EventName = = ' Nodedownopersel ' ile işlemsel olaylar döndürün:
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

EventID = = 18604 ve EventName = = ' Nodeupopersel ' ile işlemsel olaylar döndürün:
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
HealthState = = 3 (hata) ile sistem durumu raporlarını döndürür ve EventMessage alanından ek özellikleri ayıklar:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

EventID! = 17523 ile olayların zaman grafiğini döndürün:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Belirli hizmet ve düğümle toplanan Service Fabric işletimsel olayları alın:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Bir çapraz kaynak sorgusu kullanarak Service Fabric olaylarının sayısını EventID/EventName ile işleme:

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>Service Fabric uygulama olaylarını görüntüle

Kümede dağıtılan güvenilir hizmetler ve güvenilir aktör uygulamalarının olaylarını görüntüleyebilirsiniz.  Service Fabric Analytics sayfasında, **uygulama olayları**grafiğine tıklayın.

Güvenilir hizmetler uygulamalarınızdan olayları görüntülemek için aşağıdaki sorguyu çalıştırın:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

RunAsync hizmeti başlatıldığında ve tamamlandığında, genellikle dağıtımlar ve yükseltmelerde gerçekleşen farklı olayları görebilirsiniz.

![Service Fabric çözümü Reliable Services](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

Ayrıca, ServiceName = = "Fabric:/Izleyici/WatchdogService" ile güvenilir hizmete yönelik olayları bulabilirsiniz:

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Güvenilir aktör olayları benzer bir biçimde görüntülenebilir:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Güvenilir aktörler için daha ayrıntılı olaylar yapılandırmak üzere, küme şablonundaki tanılama uzantısının `scheduledTransferKeywordFilter` yapılandırmasında öğesini değiştirebilirsiniz. Bunların değerlerinin ayrıntıları, [güvenilir aktör olayları başvurusunda](service-fabric-reliable-actors-diagnostics.md#keywords)bulunur.

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Azure Izleyici günlükleri ile performans sayaçlarını görüntüleme
Performans sayaçlarını görüntülemek için, Service Fabric Analytics çözümünü oluşturduğunuz [Azure Portal](https://portal.azure.com) ve kaynak grubuna gidin. 

Kaynak **Servicefabric (Kapsamım)** , ardından **Log Analytics çalışma alanı**ve **Gelişmiş ayarlar**' ı seçin.

**Veriler**' e ve ardından **Windows performans sayaçları**' na tıklayın. Etkinleştirmek için seçebileceğiniz varsayılan sayaçların bir listesi vardır ve koleksiyon aralığını da ayarlayabilirsiniz. Ayrıca, toplanacak [ek performans sayaçları](service-fabric-diagnostics-event-generation-perf.md) ekleyebilirsiniz. Bu [makalede](/windows/desktop/PerfCtrs/specifying-a-counter-path)doğru biçime başvurulur. **Kaydet**' e ve ardından **Tamam**' a tıklayın.

Gelişmiş ayarlar dikey penceresini kapatın ve **genel** başlık altında **çalışma alanı Özeti** ' ni seçin. Etkin çözümlerin her biri için, Service Fabric gibi bir grafik kutucuğu vardır. Service Fabric Analytics çözümüne devam etmek için **Service Fabric** grafiğine tıklayın.

İşlemsel kanal ve güvenilir hizmet olayları için grafik kutucukları vardır. Seçtiğiniz sayaçlar için akan verilerin grafik gösterimi, **düğüm ölçümleri**altında görünür. 

Ek ayrıntıları görmek için **kapsayıcı ölçümü** grafiğini seçin. Ayrıca, küme olaylarına benzer şekilde performans sayacı verilerini sorgulayabilir ve düğümler, performans sayacı adı ve değerler üzerinde kusto sorgu dilini kullanarak filtre uygulayabilirsiniz.

## <a name="query-the-eventstore-service"></a>EventStore hizmetini sorgulama
[Eventstore hizmeti](service-fabric-diagnostics-eventstore.md) , belirli bir noktadaki kümenizin veya iş yüklerinizin durumunu anlamak için bir yol sağlar. EventStore, kümeden olayları tutan, durum bilgisi olan bir Service Fabric hizmetidir. Olaylar [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), REST ve API 'ler aracılığıyla sunulur. EventStore, olayınızda bulunan tüm olayların tam listesini görmek Için kümedeki herhangi bir varlıkta tanılama verilerini almak üzere kümeyi doğrudan sorgular. [Service Fabric olayları](service-fabric-diagnostics-event-generation-operational.md)' na bakın.

EventStore API 'Leri, [Service Fabric istemci kitaplığı](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library)kullanılarak programlı bir şekilde sorgulanabilir.

GetClusterEventListAsync işlevi aracılığıyla 2018-04-03T18:00:00Z ve 2018-04-04T18:00:00Z arasındaki tüm küme olayları için örnek bir istek aşağıda verilmiştir.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

İşte, küme durumunu ve 2018 Eylül 'deki tüm düğüm olaylarını sorgulayan ve bunları yazdıran bir örnek.

```csharp
const int timeoutSecs = 60;
var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());


Console.WriteLine("Querying for node events...");
var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
    "2018-09-01T00:00:00Z",
    "2018-09-30T23:59:59Z",
    timeoutSecs,
    "NodeDown,NodeUp")
    .GetAwaiter()
    .GetResult()
    .ToList();
Console.WriteLine("Result Count: {0}", nodesEvents.Count());

foreach (var nodeEvent in nodesEvents)
{
    Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
    if (nodeEvent is NodeDownEvent)
    {
        var nodeDownEvent = nodeEvent as NodeDownEvent;
        Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
    }
    else if (nodeEvent is NodeUpEvent)
    {
        var nodeUpEvent = nodeEvent as NodeUpEvent;
        Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
    }
}
```


## <a name="monitor-cluster-health"></a>Küme durumunu izleme
Service Fabric, sistem bileşenlerinin ve Watchdogs izlemedikleri yerel koşulları bildirebileceği sistem durumu varlıklarını içeren bir [sistem durumu modeli](service-fabric-health-introduction.md) sunar. [Sistem durumu deposu](service-fabric-health-introduction.md#health-store) , varlıkların sağlıklı olup olmadığını öğrenmek için tüm sistem durumu verilerini toplar.

Küme, sistem bileşenleri tarafından gönderilen sistem durumu raporlarıyla otomatik olarak doldurulur. [Sorun gidermek için sistem durumu raporlarını kullanma](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)hakkında daha fazla bilgi edinin.

Service Fabric, desteklenen [varlık türlerinin](service-fabric-health-introduction.md#health-entities-and-hierarchy)her biri için sistem durumu sorgularını kullanıma sunar. [FabricClient. HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), PowerShell cmdlet 'LERI ve REST üzerindeki yöntemler kullanılarak API aracılığıyla erişilebilir. Bu sorgular varlıkla ilgili tüm sistem durumu bilgilerini döndürür: toplu sistem durumu, varlık sistem durumu olayları, alt sistem durumu durumları (varsa), sağlıksız değerlendirmeler (varlık sağlıklı olmadığında) ve alt sistem durumu istatistikleri (ne zaman uygulanabilir).

### <a name="get-cluster-health"></a>Küme durumunu al
[Get-ServiceFabricClusterHealth cmdlet 'i](/powershell/module/servicefabric/get-servicefabricclusterhealth) , küme varlığının sistem durumunu döndürür ve uygulamaların ve düğümlerin (kümenin alt öğeleri) sistem durumu durumlarını içerir.  İlk olarak, [Connect-ServiceFabricCluster cmdlet 'ini](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)kullanarak kümeye bağlanın.

Kümenin durumu 11 düğüm, sistem uygulaması ve doku:/oylama açıklandığı şekilde yapılandırılmıştır.

Aşağıdaki örnek, varsayılan sistem durumu ilkelerini kullanarak küme durumunu alır. 11 düğüm sağlıklı ancak yapı:/oylama uygulaması hatalı olduğundan küme toplu sistem durumu hatası. Sağlıksız değerlendirmeler, toplanan sistem durumunu tetikleyen koşullara ilişkin ayrıntıları nasıl sunyacağını unutmayın.

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

Aşağıdaki örnek, özel bir uygulama ilkesi kullanarak kümenin sistem durumunu alır. Yalnızca hata veya uyarı durumunda uygulamaları ve düğümleri almak için sonuçlara filtre uygular. Bu örnekte, tüm sağlıklı olduklarından hiçbir düğüm döndürülmez. Yalnızca doku:/oylama uygulaması uygulamalar filtresini uyar. Özel ilke, uyarıları doku:/oylama uygulaması için hata olarak kabul etmeyi belirttiğinden, uygulama hatalı olarak değerlendirilir ve küme bu şekilde belirlenir.

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>Düğüm durumunu al
[Get-ServiceFabricNodeHealth cmdlet 'i](/powershell/module/servicefabric/get-servicefabricnodehealth) bir düğüm varlığının sistem durumunu döndürür ve düğümde bildirilen sistem durumu olaylarını içerir. İlk olarak, [Connect-ServiceFabricCluster cmdlet 'ini](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)kullanarak kümeye bağlanın. Aşağıdaki örnek, varsayılan sistem durumu ilkelerini kullanarak belirli bir düğümün sistem durumunu alır:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

Aşağıdaki örnek kümedeki tüm düğümlerin sistem durumunu alır:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Sistem hizmeti sistem durumunu al 

Sistem hizmetlerinin toplu sistem durumunu alın:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Service Fabric olaylarını görüntüle
> * Küme olayları için EventStore API 'Lerini sorgulama
> * Altyapıyı izleme/performans sayaçlarını toplama
> * Küme durumu raporlarını görüntüleme

Daha sonra, bir kümeyi ölçeklendirmeyi öğrenmek için aşağıdaki öğreticiye ilerleyin.
> [!div class="nextstepaction"]
> [Küme ölçeklendirme](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
