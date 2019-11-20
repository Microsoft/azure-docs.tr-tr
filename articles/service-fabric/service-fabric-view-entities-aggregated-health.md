---
title: Azure Service Fabric varlıkların toplu sistem durumunu görüntüleme | Microsoft Docs
description: Sistem durumu sorguları ve genel sorgular aracılığıyla Azure Service Fabric varlıkların toplu sistem durumunu sorgulama, görüntüleme ve değerlendirme işlemlerinin nasıl yapılacağını açıklar.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: c4a312654fb54660a229c334071d33a5d6bc172f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496364"
---
# <a name="view-service-fabric-health-reports"></a>Service Fabric sistem durumu raporlarını görüntüleme
Azure Service Fabric, sistem bileşenlerinin ve Watchdogs izlemedikleri yerel koşulları bildirebileceği sistem durumu varlıklarını içeren bir [sistem durumu modeli](service-fabric-health-introduction.md) sunar. [Sistem durumu deposu](service-fabric-health-introduction.md#health-store) , varlıkların sağlıklı olup olmadığını öğrenmek için tüm sistem durumu verilerini toplar.

Küme, sistem bileşenleri tarafından gönderilen sistem durumu raporlarıyla otomatik olarak doldurulur. [Sorun gidermek için sistem durumu raporlarını kullanma](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)hakkında daha fazla bilgi edinin.

Service Fabric varlıkların toplu sistem durumunu almak için birden çok yol sağlar:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) veya diğer görselleştirme araçları
* Durum sorguları (PowerShell, API veya REST aracılığıyla)
* Özelliklerden biri (PowerShell, API veya REST aracılığıyla) durumuna sahip varlıkların bir listesini döndüren genel sorgular

Bu seçenekleri göstermek için beş düğüm ve [doku:/WordCount uygulaması](https://github.com/Azure-Samples/service-fabric-wordcount/raw/master/WordCountV1.sfpkg)olan yerel bir küme kullanalım. **Fabric:/WordCount** uygulaması iki varsayılan hizmet, `WordCountServiceType`türünde bir durum bilgisi olan hizmet ve `WordCountWebServiceType`türünde durum bilgisi olmayan bir hizmet içerir. `ApplicationManifest.xml`, durum bilgisi olan hizmet ve bir bölüm için yedi hedef çoğaltma gerektirecek şekilde değiştirdim. Kümede yalnızca beş düğüm olduğundan, sistem bileşenleri, hedef sayısının altında olduğundan hizmet bölümünde bir uyarı bildirir.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Service Fabric Explorer sistem durumu
Service Fabric Explorer kümenin görsel görünümünü sağlar. Aşağıdaki görüntüde şunları görebilirsiniz:

* Uygulama **dokusu:/WordCount** , özellik **kullanılabilirliği**için **mykıst** tarafından bildirilen bir hata olayı içerdiğinden kırmızı (hatalı).
* Hizmetlerinden biri olan **Fabric:/WordCount/WordCountService** sarı (uyarı olarak). Hizmet yedi çoğaltma ile yapılandırılmıştır ve kümede beş düğüm bulunur, bu nedenle iki çoğaltma yerleştirilemez. Burada gösterilmese de, `Partition is below target replica or instance count`söyleyen `System.FM` bir sistem raporu nedeniyle hizmet bölümü sarı. Sarı bölüm, sarı hizmeti tetikler.
* Kırmızı uygulama nedeniyle küme kırmızıdır.

Değerlendirme, küme bildiriminde ve uygulama bildiriminde varsayılan ilkeleri kullanır. Bunlar katı ilkeleridir ve herhangi bir hatayı kabul etmez.

Service Fabric Explorer ile küme görünümü:

![Service Fabric Explorer ile küme görünümü.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)hakkında daha fazla bilgi edinin.
>
>

## <a name="health-queries"></a>Sistem durumu sorguları
Service Fabric, desteklenen [varlık türlerinin](service-fabric-health-introduction.md#health-entities-and-hierarchy)her biri için sistem durumu sorgularını kullanıma sunar. [FabricClient. HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), PowerShell cmdlet 'LERI ve REST üzerindeki yöntemler kullanılarak API aracılığıyla erişilebilir. Bu sorgular varlıkla ilgili tüm sistem durumu bilgilerini döndürür: toplu sistem durumu, varlık sistem durumu olayları, alt sistem durumu durumları (varsa), sağlıksız değerlendirmeler (varlık sağlıklı olmadığında) ve alt sistem durumu istatistikleri (ne zaman uygulanabilir).

> [!NOTE]
> Sistem durumu deposunda tamamen doldurulduğu zaman bir sistem durumu varlığı döndürülür. Varlık etkin (silinmemelidir) ve bir sistem raporuna sahip olmalıdır. Hiyerarşi zincirindeki üst varlıklarının de sistem raporları olmalıdır. Bu koşullardan herhangi biri karşılanmıyorsa, sistem durumu sorguları, varlığın neden döndürülmediğini gösteren [Fabricerrorcode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` Ile bir [fabricexception](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) döndürür.
>
>

Durum sorgularının, varlık türüne bağlı olan varlık tanımlayıcısı içinde geçmesi gerekir. Sorgular isteğe bağlı sistem durumu ilkesi parametrelerini kabul eder. Sistem durumu ilkesi belirtilmemişse, küme veya uygulama bildirimindeki [sistem durumu ilkeleri](service-fabric-health-introduction.md#health-policies) değerlendirme için kullanılır. Bildirimler sistem durumu ilkeleri için bir tanım içermiyorsa, değerlendirme için varsayılan sistem durumu ilkeleri kullanılır. Varsayılan sistem durumu ilkeleri hatalara karşı kabul etmez. Sorgular, belirtilen filtrelere göre yalnızca kısmi alt öğe veya olay döndürmek için filtreleri de kabul eder. Diğer bir filtre alt istatistiklerin dışlanmasını sağlar.

> [!NOTE]
> Çıkış filtreleri sunucu tarafında uygulanır, bu nedenle ileti yanıt boyutu azaltılır. İstemci tarafında filtre uygulamak yerine döndürülen verileri sınırlandırmak için çıkış filtrelerini kullanmanızı öneririz.
>
>

Bir varlığın sistem durumu şunları içerir:

* Varlığın toplu sistem durumu. Durum depolama tarafından, varlık sistem durumu raporlarına, alt sistem sağlığı durumlarına (uygun olduğunda) ve sistem durumu ilkelerine göre hesaplanır. [Varlık durumu değerlendirmesi](service-fabric-health-introduction.md#health-evaluation)hakkında daha fazla bilgi edinin.  
* Varlıktaki sistem durumu olayları.
* Alt öğeleri olan varlıkların tüm alt öğelerinin sistem durumu koleksiyonu. Sistem durumları, varlık tanımlayıcılarını ve toplanmış sistem durumunu içerir. Bir alt öğenin tüm sistem durumunu almak için, alt varlık türü için sorgu durumunu çağırın ve alt tanımlayıcıyı geçirin.
* Varlık sağlıklı değilse, varlığın durumunu tetikleyen raporu işaret eden sağlıksız değerlendirmeler. Değerlendirmeler, geçerli sistem durumunu tetikleyen alt sistem durumu değerlendirmelerinin bulunduğu özyinelemeli bir yinelemedir. Örneğin, bir izleyici bir çoğaltmaya karşı bir hata bildirdi. Uygulama durumu, sağlıksız bir hizmet nedeniyle sağlıksız bir değerlendirme gösterir; Hatalı bir bölüm nedeniyle hizmet uygun değil; Hatalı bir çoğaltma nedeniyle bölüm kötü durumda. İzleyici hata sistem durumu raporu nedeniyle çoğaltma sağlıksız durumda.
* Alt öğeleri olan varlıkların tüm alt öğe türleri için sistem durumu istatistikleri. Örneğin, küme sağlığı, kümedeki Toplam uygulama, hizmet, bölüm, çoğaltma ve dağıtılan varlık sayısını gösterir. Hizmet durumu, belirtilen hizmet kapsamındaki toplam bölüm ve çoğaltma sayısını gösterir.

## <a name="get-cluster-health"></a>Küme durumunu al
Küme varlığının sistem durumunu döndürür ve uygulamaların ve düğümlerin (kümenin alt öğeleri) sistem durumu durumlarını içerir. Giriş:

* Seçim Düğümleri ve küme olaylarını değerlendirmek için kullanılan küme sistem durumu ilkesi.
* Seçim Uygulama durumu ilkesi eşlemesi, uygulama bildirim ilkelerini geçersiz kılmak için kullanılan sistem durumu ilkeleriyle birlikte.
* Seçim Hangi girişlerin ilgilenilmek gerektiğini belirten olaylar, düğümler ve uygulamalar için filtreler (örneğin, yalnızca hatalar veya her iki uyarı ve hata). Tüm olaylar, düğümler ve uygulamalar, filtreden bağımsız olarak, varlığın toplam durumunu değerlendirmek için kullanılır.
* Seçim Sistem durumu istatistiklerini hariç tutmak için filtreleyin.
* Seçim Durum istatistiklerine doku:/sistem durumu istatistiklerini dahil etmek için filtreleyin. Yalnızca sistem durumu istatistikleri dışlanmadığı zaman geçerlidir. Varsayılan olarak, sistem durumu istatistikleri, sistem uygulaması değil yalnızca kullanıcı uygulamaları için istatistikleri içerir.

### <a name="api"></a>API
Küme durumunu almak için bir `FabricClient` oluşturun ve **Healthmanager**'da [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) yöntemini çağırın.

Aşağıdaki çağrı, küme durumunu alır:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Aşağıdaki kod, özel bir küme sistem durumu ilkesi ve düğüm ve uygulama filtrelerini kullanarak küme durumunu alır. Durum istatistiklerinin doku:/sistem istatistiklerini dahil edileceğini belirtir. Giriş bilgilerini içeren [Clusterhealthquerydescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription)oluşturur.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Küme durumunu almak için cmdlet [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth)' dır. İlk olarak, [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet 'ini kullanarak kümeye bağlanın.

Kümenin durumu beş düğüm, sistem uygulaması ve doku:/WordCount açıklandığı şekilde yapılandırılır.

Aşağıdaki cmdlet, varsayılan sistem durumu ilkelerini kullanarak küme durumunu alır. Fabric:/WordCount uygulaması uyarı içinde olduğundan, toplanmış sistem durumu Warning. Sağlıksız değerlendirmeler, toplanan sistem durumunu tetikleyen koşullara ilişkin ayrıntıları nasıl sunyacağını unutmayın.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.


NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

Aşağıdaki PowerShell cmdlet 'i özel bir uygulama ilkesi kullanarak kümenin sistem durumunu alır. Yalnızca hata veya uyarı durumunda uygulamaları ve düğümleri almak için sonuçlara filtre uygular. Sonuç olarak, tüm sağlıklı olduklarından hiçbir düğüm döndürülmez. Yalnızca Fabric:/WordCount uygulaması uygulamalar filtresine uyar. Özel ilke yapı:/WordCount uygulaması için uyarıları hata olarak kabul etmeyi belirttiğinden, uygulama hata olarak değerlendirilir ve bu nedenle küme.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None
```

### <a name="rest"></a>REST
Gövdede açıklanan sistem durumu ilkelerini içeren bir [get isteği](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) veya [POST isteği](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) ile küme durumunu alabilirsiniz.

## <a name="get-node-health"></a>Düğüm durumunu al
Düğüm varlığının sistem durumunu döndürür ve düğümde bildirilen sistem durumu olaylarını içerir. Giriş:

* Istenir Düğümü tanımlayan düğüm adı.
* Seçim Sistem durumunu değerlendirmek için kullanılan küme durumu ilkesi ayarları.
* Seçim Hangi girişlerin ilgilenilmek gerektiğini belirten olaylar için filtreler (örneğin, yalnızca hatalar veya her iki uyarı ve hata). Tüm olaylar, filtreden bağımsız olarak, varlığın toplam durumunu değerlendirmek için kullanılır.

### <a name="api"></a>API
API aracılığıyla düğüm durumunu almak için bir `FabricClient` oluşturun ve HealthManager 'da [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) yöntemini çağırın.

Aşağıdaki kod, belirtilen düğüm adı için düğüm durumunu alır:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Aşağıdaki kod, belirtilen düğüm adı için düğüm durumunu alır ve [Nodehealthquerydescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription)aracılığıyla olaylar filtrelemesine ve özel ilkeye geçirilir:

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Düğüm durumunun alınacağı cmdlet [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth)' dir. İlk olarak, [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet 'ini kullanarak kümeye bağlanın.
Aşağıdaki cmdlet, varsayılan sistem durumu ilkelerini kullanarak düğüm durumunu alır:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Aşağıdaki cmdlet kümedeki tüm düğümlerin sistem durumunu alır:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
Gövdede açıklanan sistem durumu ilkelerini içeren bir [get isteği](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) veya [Post isteğiyle](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) düğüm durumu alabilirsiniz.

## <a name="get-application-health"></a>Uygulama durumunu al
Bir uygulama varlığının sistem durumunu döndürür. Dağıtılan uygulama ve hizmet alt öğelerinin sistem durumlarını içerir. Giriş:

* Istenir Uygulamayı tanımlayan uygulama adı (URI).
* Seçim Uygulama bildirim ilkelerini geçersiz kılmak için kullanılan uygulama sistem durumu ilkesi.
* Seçim Hangi girişlerin ilgilenilmek gerektiğini belirten olaylar, hizmetler ve dağıtılan uygulamalar için filtreler (örneğin, yalnızca hatalar veya her iki uyarı ve hata). Tüm olaylar, hizmetler ve dağıtılan uygulamalar, filtreden bağımsız olarak, varlığın toplam durumunu değerlendirmek için kullanılır.
* Seçim Sistem durumu istatistiklerini hariç tutmak için filtreleyin. Belirtilmezse, sistem durumu istatistikleri tüm uygulama alt öğeleri için Tamam, uyarı ve hata sayısını içerir: hizmetler, bölümler, çoğaltmalar, dağıtılan uygulamalar ve dağıtılan hizmet paketleri.

### <a name="api"></a>API
Uygulama sistem durumunu almak için bir `FabricClient` oluşturun ve HealthManager 'da [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) yöntemini çağırın.

Aşağıdaki kod, belirtilen uygulama adı (URI) için uygulama durumunu alır:

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Aşağıdaki kod, [Applicationhealthquerydescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription)aracılığıyla belirtilen filtre ve özel ilkelerle belirtilen uygulama adı (URI) için uygulama durumunu alır.

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Uygulamanın sistem durumunu almak için cmdlet 'i [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). İlk olarak, [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet 'ini kullanarak kümeye bağlanın.

Aşağıdaki cmdlet, **dokunun:/WordCount** uygulamasının sistem durumunu döndürür:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

Aşağıdaki PowerShell cmdlet 'i özel ilkelerde geçirilir. Ayrıca alt öğeleri ve olayları filtreler.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Bir [get isteği](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) veya gövdede açıklanan sistem durumu ilkelerini Içeren bir [POST isteği](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) ile uygulama durumu alabilirsiniz.

## <a name="get-service-health"></a>Hizmet durumunu al
Bir hizmet varlığının sistem durumunu döndürür. Bölüm sistem durumlarını içerir. Giriş:

* Istenir Hizmeti tanımlayan hizmet adı (URI).
* Seçim Uygulama bildirim ilkesini geçersiz kılmak için kullanılan uygulama sistem durumu ilkesi.
* Seçim Hangi girişlerin ilgilenilmek gerektiğini belirten olaylar ve bölümler için filtreler (örneğin, yalnızca hatalar veya her iki uyarı ve hata). Tüm olaylar ve bölümler, filtreden bağımsız olarak, varlığın toplam durumunu değerlendirmek için kullanılır.
* Seçim Sistem durumu istatistiklerini hariç tutmak için filtreleyin. Belirtilmemişse, sistem durumu istatistikleri hizmetin tüm bölümleri ve çoğaltmaları için Tamam, uyarı ve hata sayısını gösterir.

### <a name="api"></a>API
API aracılığıyla hizmet durumunu almak için bir `FabricClient` oluşturun ve HealthManager 'da [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) yöntemini çağırın.

Aşağıdaki örnek, belirtilen hizmet adına (URI) sahip bir hizmetin sistem durumunu alır:

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Aşağıdaki kod, [Servicehealthquerydescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription)aracılığıyla filtre ve özel ilke belirterek belirtilen hizmet adı (URI) için hizmet durumunu alır:

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Hizmet durumunu almak için cmdlet, [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth)' dır. İlk olarak, [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet 'ini kullanarak kümeye bağlanın.

Aşağıdaki cmdlet, varsayılan sistem durumu ilkelerini kullanarak hizmet durumunu alır:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning

HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
Bir [get isteği](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) veya gövdede açıklanan sistem durumu ilkelerini Içeren bir [POST isteği](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) ile hizmet durumunu alabilirsiniz.

## <a name="get-partition-health"></a>Bölüm durumunu al
Bölüm varlığının sistem durumunu döndürür. Çoğaltma sistem durumlarını içerir. Giriş:

* Istenir Bölümü tanımlayan bölüm KIMLIĞI (GUID).
* Seçim Uygulama bildirim ilkesini geçersiz kılmak için kullanılan uygulama sistem durumu ilkesi.
* Seçim Hangi girişlerin ilgilenilmek gerektiğini belirten olaylar ve çoğaltmalar için filtreler (örneğin, yalnızca hatalar veya her iki uyarı ve hata). Tüm olaylar ve çoğaltmalar, filtreden bağımsız olarak, varlığın toplam durumunu değerlendirmek için kullanılır.
* Seçim Sistem durumu istatistiklerini hariç tutmak için filtreleyin. Belirtilmemişse, sistem durumu istatistikleri, kaç yinelemenin Tamam, uyarı ve hata durumları olduğunu gösterir.

### <a name="api"></a>API
Bölüm durumunu API aracılığıyla almak için bir `FabricClient` oluşturun ve HealthManager 'da [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) yöntemini çağırın. İsteğe bağlı parametreleri belirtmek için [Partitionhealthquerydescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription)oluşturun.

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
Bölüm sistem durumunu almak için cmdlet [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth)' dır. İlk olarak, [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet 'ini kullanarak kümeye bağlanın.

Aşağıdaki cmdlet, dokunun tüm bölümlerinin sistem durumunu alır **:/WordCount/WordCountService** hizmeti ve çoğaltma sistem durumu durumlarını filtreler:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)

                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A

                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None

                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Bir [get isteği](/rest/api/servicefabric/sfclient-api-getpartitionhealth) veya gövdede açıklanan sistem durumu ilkelerini Içeren bir [POST isteği](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) ile bölüm durumunu alabilirsiniz.

## <a name="get-replica-health"></a>Çoğaltma durumunu al
Durum bilgisi olan bir hizmet çoğaltmasının veya durum bilgisi olmayan bir hizmet örneğinin sistem durumunu döndürür. Giriş:

* Istenir Çoğaltmayı tanımlayan bölüm KIMLIĞI (GUID) ve çoğaltma KIMLIĞI.
* Seçim Uygulama bildirim ilkelerini geçersiz kılmak için kullanılan uygulama durumu ilkesi parametreleri.
* Seçim Hangi girişlerin ilgilenilmek gerektiğini belirten olaylar için filtreler (örneğin, yalnızca hatalar veya her iki uyarı ve hata). Tüm olaylar, filtreden bağımsız olarak, varlığın toplam durumunu değerlendirmek için kullanılır.

### <a name="api"></a>API
API aracılığıyla çoğaltma sistem durumunu almak için bir `FabricClient` oluşturun ve HealthManager 'da [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) yöntemini çağırın. Gelişmiş parametreleri belirtmek için, [Replicahealthquerydescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription)kullanın.

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
Çoğaltma durumunu alma cmdlet 'i [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth)' dır. İlk olarak, [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet 'ini kullanarak kümeye bağlanın.

Aşağıdaki cmdlet, hizmetin tüm bölümleri için birincil çoğaltmanın sistem durumunu alır:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Gövdesinde açıklanan sistem durumu ilkelerini içeren bir [get isteği](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) veya [POST isteği](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) ile çoğaltma durumu alabilirsiniz.

## <a name="get-deployed-application-health"></a>Dağıtılan uygulama durumunu al
Düğüm varlığına dağıtılan bir uygulamanın sistem durumunu döndürür. Dağıtılmış hizmet paketi sistem durumlarını içerir. Giriş:

* Istenir Dağıtılan uygulamayı tanımlayan uygulama adı (URI) ve düğüm adı (dize).
* Seçim Uygulama bildirim ilkelerini geçersiz kılmak için kullanılan uygulama sistem durumu ilkesi.
* Seçim Hangi girişlerin ilgilenilmek gerektiğini belirten olaylar ve dağıtılan hizmet paketleri için filtreler (örneğin, yalnızca hatalar veya her iki uyarı ve hata). Tüm olaylar ve dağıtılan hizmet paketleri, filtreden bağımsız olarak, varlığın toplam durumunu değerlendirmek için kullanılır.
* Seçim Sistem durumu istatistiklerini hariç tutmak için filtreleyin. Belirtilmemişse, sistem durumu istatistikleri, dağıtılan hizmet paketlerinin sayısını Tamam, uyarı ve hata durumu durumları bölümünde gösterir.

### <a name="api"></a>API
API aracılığıyla bir düğüme dağıtılan bir uygulamanın sistem durumunu almak için bir `FabricClient` oluşturun ve HealthManager 'da [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) yöntemini çağırın. İsteğe bağlı parametreleri belirtmek için [Deployedadpplicationhealthquerydescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription)kullanın.

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
Dağıtılan uygulamanın sistem durumunu almak için cmdlet 'i [Get-Servicefabricdeployedadpplicationhealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). İlk olarak, [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet 'ini kullanarak kümeye bağlanın. Uygulamanın nereye dağıtıldığını öğrenmek için [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) komutunu çalıştırın ve dağıtılan uygulama alt öğelerine bakın.

Aşağıdaki cmdlet, **_Node_2**üzerinde dağıtılan **Fabric:/WORDCOUNT** uygulamasının sistem durumunu alır.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Bir [Get isteğiyle](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) veya gövdede açıklanan sistem durumu ilkelerini Içeren bir [Post isteğiyle](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) dağıtılan uygulama durumu alabilirsiniz.

## <a name="get-deployed-service-package-health"></a>Dağıtılan hizmet paketi durumunu al
Dağıtılan bir hizmet paketi varlığının sistem durumunu döndürür. Giriş:

* Istenir Dağıtılan hizmet paketini tanımlayan uygulama adı (URI), düğüm adı (dize) ve hizmet bildirimi adı (dize).
* Seçim Uygulama bildirim ilkesini geçersiz kılmak için kullanılan uygulama sistem durumu ilkesi.
* Seçim Hangi girişlerin ilgilenilmek gerektiğini belirten olaylar için filtreler (örneğin, yalnızca hatalar veya her iki uyarı ve hata). Tüm olaylar, filtreden bağımsız olarak, varlığın toplam durumunu değerlendirmek için kullanılır.

### <a name="api"></a>API
API aracılığıyla dağıtılan bir hizmet paketinin sistem durumunu almak için bir `FabricClient` oluşturun ve HealthManager 'da [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) yöntemini çağırın. İsteğe bağlı parametreleri belirtmek için [Deployedservicepackagehealthquerydescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription)kullanın.

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Dağıtılan hizmet paketi sistem durumunu almak için cmdlet, [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth)' dır. İlk olarak, [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet 'ini kullanarak kümeye bağlanın. Uygulamanın nerede dağıtıldığını görmek için [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) komutunu çalıştırın ve dağıtılan uygulamalara bakın. Hangi hizmet paketlerinin bir uygulamada olduğunu görmek için [Get-Servicefabricdeployedadpplicationhealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) çıktısında dağıtılan hizmet paketi alt öğelerine bakın.

Aşağıdaki cmdlet, **_Node_2**üzerinde dağıtılan **Fabric:/WORDCOUNT** uygulamasının **wordcountservicepkg** hizmet paketinin sistem durumunu alır. Varlık, başarılı hizmet paketi ve giriş noktası etkinleştirme ve başarılı hizmet türü kayıt için **System. Hosting** raporları içerir.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Bir [get isteği](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) veya gövdede açıklanan sistem durumu ilkelerini Içeren bir [POST isteği](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) ile dağıtılmış hizmet paketi sistem durumunu alabilirsiniz.

## <a name="health-chunk-queries"></a>Durum öbek sorguları
Durum öbek sorguları, giriş filtreleri başına çok düzeyli küme alt öğelerini (yinelemeli) döndürebilir. Döndürülecek alt öğeleri seçerken çok fazla esneklik sağlayan gelişmiş filtreleri destekler. Filtreler, alt öğeleri benzersiz tanımlayıcı veya diğer grup tanımlayıcıları ve/veya sistem durumları ile belirtebilir. Varsayılan olarak, her zaman ilk düzey alt öğeleri içeren sistem durumu komutlarının aksine alt öğe dahil değildir.

[Sistem durumu sorguları](service-fabric-view-entities-aggregated-health.md#health-queries) , gerekli filtreler başına belirtilen varlığın yalnızca ilk düzey alt öğelerini döndürür. Alt öğelerin alt öğelerini almak için, ilgilendiğiniz her varlık için ek sağlık API 'Lerini çağırmanız gerekir. Benzer şekilde, belirli varlıkların sistem durumunu almak için, istenen her varlık için bir sistem durumu API 'SI çağırmanız gerekir. Öbek sorgusu Gelişmiş filtreleme, tek bir sorguda birden çok öğe ilgilenmenizi, ileti boyutunu ve ileti sayısını en aza indirmenizi sağlar.

Öbek sorgusunun değeri, daha fazla küme varlığı (potansiyel olarak, gerekli kökden başlayan tüm küme varlıkları) için tek bir çağrıda sistem durumu alabilir. Karmaşık durum sorgusunu aşağıda gösterildiği gibi ifade edebilirsiniz:

* Yalnızca hatalı uygulamaları geri döndür ve bu uygulamalar için uyarı veya hata bölümünde tüm hizmetler bulunur. Döndürülen hizmetler için tüm bölümleri dahil edin.
* Yalnızca, adlarına göre belirtilen dört uygulamanın sistem durumunu döndürür.
* Yalnızca istenen uygulama türündeki uygulamaların sistem durumunu döndürür.
* Bir düğümdeki tüm dağıtılan varlıkları döndürün. Belirtilen düğümdeki tüm dağıtılan uygulamaları ve bu düğümdeki tüm dağıtılan hizmet paketlerini döndürür.
* Tüm çoğaltmaları hata halinde döndürür. Tüm uygulamaları, Hizmetleri, bölümleri ve yalnızca hatalı çoğaltmaları döndürür.
* Tüm uygulamaları döndürün. Belirtilen bir hizmet için tüm bölümleri dahil edin.

Şu anda, sistem durumu öbek sorgusu yalnızca küme varlığı için kullanıma sunulur. Şunu içeren bir küme durumu öbeği döndürür:

* Kümenin toplam sistem durumu.
* Giriş filtrelerine yönelik düğümlerin sistem durumu öbek listesi.
* Giriş filtrelerine yönelik uygulamaların sistem durumu öbek listesi. Her bir uygulama sistem durumu öbeği, giriş filtrelerine ve tüm dağıtılan uygulamalarla ilgili filtreleri karşılayan bir öbek listesini karşılayan tüm hizmetleri içeren bir öbek listesi içerir. Hizmetlerin ve dağıtılan uygulamaların alt öğeleri için de aynıdır. Bu şekilde, küme içindeki tüm varlıklar, istenirse, hiyerarşik bir biçimde döndürülür.

### <a name="cluster-health-chunk-query"></a>Küme durumu öbek sorgusu
Küme varlığının sistem durumunu döndürür ve gerekli alt öğelerin hiyerarşik sistem durumu öbeklerini içerir. Giriş:

* Seçim Düğümleri ve küme olaylarını değerlendirmek için kullanılan küme sistem durumu ilkesi.
* Seçim Uygulama durumu ilkesi eşlemesi, uygulama bildirim ilkelerini geçersiz kılmak için kullanılan sistem durumu ilkeleriyle birlikte.
* Seçim Hangi girişlerin ilgilenilmek gerektiğini belirten ve sonuçta döndürülecek olan düğüm ve uygulamalar için filtreler. Filtreler bir varlığa/varlık grubuna özeldir veya bu düzeydeki tüm varlıklar için geçerlidir. Filtre listesi, bir genel filtre ve/veya belirli tanımlayıcıların sorgu tarafından döndürülen varlıkları ince noktalı virgülle filtreler içerebilir. Boşsa, alt öğeler varsayılan olarak döndürülmez.
  [Nodehealthstatefilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) ve [applicationhealthstatefilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter)konumundaki filtreler hakkında daha fazla bilgi edinin. Uygulama filtreleri yinelemeli olarak alt öğeler için gelişmiş filtreler belirtebilir.

Öbek sonucu, filtrelere yönelik alt öğeleri içerir.

Şu anda, öbek sorgusu sağlıksız değerlendirmeler veya varlık olayları döndürmez. Bu ek bilgiler, mevcut küme durumu sorgusu kullanılarak elde edilebilir.

### <a name="api"></a>API
Küme durumu öbeğini almak için bir `FabricClient` oluşturun ve **Healthmanager**'Da [Getclusterhealthchunkasync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) yöntemini çağırın. Sistem durumu ilkelerini ve gelişmiş filtreleri anlatmak için [Clusterhealthquerydescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) ' a geçiş yapabilirsiniz.

Aşağıdaki kod, gelişmiş filtrelerle küme durumu öbeğini alır.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Küme durumunu almak için cmdlet, [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk)' dır. İlk olarak, [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet 'ini kullanarak kümeye bağlanın.

Aşağıdaki kod, yalnızca belirli bir düğüm hariç hata durumunda olmaları durumunda düğümleri alır ve her zaman döndürülür.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

Aşağıdaki cmdlet, uygulama filtreleriyle küme öbeği alır.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1

                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1

                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5

                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

Aşağıdaki cmdlet, bir düğümdeki tüm dağıtılan varlıkları döndürür.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
Bir [get isteği](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) veya bir [POST isteği](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) ile, gövdede açıklanan sistem durumu ilkelerini ve gelişmiş filtreleri içeren küme durumu öbeğini alabilirsiniz.

## <a name="general-queries"></a>Genel sorgular
Genel sorgular, belirtilen türde Service Fabric varlıkların bir listesini döndürür. Bunlar API aracılığıyla sunulur ( **FabricClient. QueryManager**yöntemleri aracılığıyla), PowerShell cmdlet 'LERI ve Rest. Bu sorgular, birden çok bileşenden gelen alt sorguları toplar. Bunlardan biri, her bir sorgu sonucunun toplanmış sistem durumunu dolduran [sistem durumu deposudur](service-fabric-health-introduction.md#health-store).  

> [!NOTE]
> Genel sorgular varlığın toplu sistem durumunu döndürür ve zengin sağlık verileri içermez. Bir varlık sağlıklı değilse, olaylar, alt sistem durumu durumları ve sağlıksız değerlendirmeler dahil olmak üzere tüm sistem durumu bilgilerini almak için sistem durumu sorgularıyla izleyebilirsiniz.
>
>

Genel sorgular bir varlık için bilinmeyen bir sistem durumu döndürmediğinde, sistem durumu deposu varlık hakkında tüm verileri almamalıdır. Ayrıca, sistem durumu deposu için bir alt sorgu başarılı olmazsa (örneğin, bir iletişim hatası vardı veya sistem durumu deposu daraltıldı). Varlık için bir sistem durumu sorgusu ile izleyin. Alt sorgu ağ sorunları gibi geçici hatalarla karşılaşılırsa, bu izleme sorgusu başarılı olabilir. Ayrıca, varlığın neden gösterilmediği hakkında daha fazla ayrıntı için sistem durumu deposundan daha fazla ayrıntı verebilir.

Varlıklar için **HealthState** içeren sorgular şunlardır:

* Düğüm listesi: kümedeki liste düğümlerini döndürür (Sayfalanmış).
  * API: [FabricClient. QueryClient. GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Uygulama listesi: kümedeki uygulamaların (Sayfalanmış) listesini döndürür.
  * API: [FabricClient. QueryClient. GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Hizmet listesi: bir uygulamadaki (Sayfalanmış) hizmetlerin listesini döndürür.
  * API: [FabricClient. QueryClient. GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Bölüm listesi: bir hizmetin (Sayfalanmış) bölümlerinin listesini döndürür.
  * API: [FabricClient. QueryClient. GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Çoğaltma Listesi: bir bölümdeki kopyaların (Sayfalanmış) listesini döndürür.
  * API: [FabricClient. QueryClient. GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Dağıtılan uygulama listesi: bir düğümdeki dağıtılan uygulamaların listesini döndürür.
  * API: [FabricClient. QueryClient. Getdeployedavınpplicationlistasync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-Servicefabricdeployedadpplication
* Dağıtılan hizmet paketi listesi: dağıtılan bir uygulamadaki hizmet paketlerinin listesini döndürür.
  * API: [FabricClient. QueryClient. GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-Servicefabricdeployedadpplication

> [!NOTE]
> Bazı sorgular disk belleğine alınmış sonuçlar döndürüyor. Bu sorguların geri dönmesi [Pagedlist\<t >](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1)öğesinden türetilmiş bir listesidir. Sonuçlar bir iletiye uymuyorsa, yalnızca bir sayfa döndürülür ve numaralandırmanın nerede durdurulduğunu izleyen bir ContinuationToken olur. Sonraki sonuçları almak için aynı sorguyu çağırmaya ve önceki sorgudan devamlılık belirtecini geçirmeye devam edin.

### <a name="examples"></a>Örnekler
Aşağıdaki kod kümedeki sağlıksız uygulamaları alır:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Aşağıdaki cmdlet, Fabric:/WordCount uygulaması için uygulama ayrıntılarını alır. Sistem durumunun uyarı durumunda olduğunu unutmayın.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

Aşağıdaki cmdlet, sistem durumu hatası olan hizmetleri alır:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Küme ve uygulama yükseltmeleri
Küme ve uygulamanın izlenen yükseltmesi sırasında, her şeyin sağlıklı kalmasını sağlamak için sistem durumunu denetler Service Fabric. Bir varlık, yapılandırılmış sistem durumu ilkeleri kullanılarak değerlendirildikleri şekilde sağlıksız ise, yükseltme, sonraki eylemi belirlemede yükseltmeye özgü ilkeler uygular. Yükseltme, kullanıcı etkileşimine (hata koşullarını düzeltme veya ilkeleri değiştirme gibi) izin vermek için duraklatılıp otomatik olarak önceki iyi sürüme geri dönebilir.

*Küme* yükseltmesi sırasında, küme yükseltme durumunu alabilirsiniz. Yükseltme durumu sağlıksız değerlendirmeler içerir ve kümede sağlıksız olduğunu işaret eder. Yükseltme sistem durumu sorunları nedeniyle geri alınırsa, yükseltme durumu en son sağlıksız nedenleri anımsar. Bu bilgiler, yöneticilerin yükseltme geri alındıktan veya durdurulduktan sonra neyin yanlış olduğunu araştırmaya yardımcı olabilir.

Benzer şekilde, bir *uygulama* yükseltmesi sırasında, sağlıksız değerlendirmeler uygulama yükseltme durumunda yer alır.

Aşağıda, değiştirilmiş bir doku:/WordCount uygulaması için uygulama yükseltme durumu gösterilmektedir. Bir izleme Çoğaltmalarından birinde bir hata bildirdi. Durum denetimleri dikkate alınmadığından yükseltme geri alınıyor.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

[Service Fabric uygulaması yükseltmesi](service-fabric-application-upgrade.md)hakkında daha fazla bilgi edinin.

## <a name="use-health-evaluations-to-troubleshoot"></a>Sorun gidermek için sistem durumu değerlendirmesi kullanın
Küme veya uygulama ile ilgili bir sorun olduğunda, neyin yanlış olduğunu saptamak için kümeye veya uygulama durumuna bakın. Sağlıksız değerlendirmeler, geçerli sağlıksız durum tetiklendiği hakkında ayrıntılı bilgi sağlar. Gerekirse, kök nedenini belirlemek için sağlıksız alt varlıklarda ayrıntıya gidebilirsiniz.

Örneğin, Çoğaltmalarından birinde bir hata raporu olduğundan, bir uygulamayı sağlıksız olarak düşünün. Aşağıdaki PowerShell cmdlet 'i sağlıksız değerlendirmeleri gösterir:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.

                                            Error event: SourceId='MyWatchdog', Property='Memory'.

ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

Daha fazla bilgi edinmek için çoğaltmaya bakabilirsiniz:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.

HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> Sağlıksız değerlendirmeler, varlığın geçerli sistem durumuna değerlendirildiği ilk neden gösterir. Bu durumu tetikleyen birden fazla başka olay olabilir, ancak değerlendirmelere yansıtılmazlar. Daha fazla bilgi edinmek için, kümedeki tüm sağlıksız raporları anlamak için sistem durumu varlıklarının ayrıntılarına gidin.
>
>

## <a name="next-steps"></a>Sonraki adımlar
[Sorun gidermek için sistem durumu raporlarını kullanma](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Özel Service Fabric sistem durumu raporları ekleme](service-fabric-report-health.md)

[Hizmet durumunu raporlama ve denetleme](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Hizmetleri yerel olarak izleme ve tanılama](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uygulama yükseltmesini Service Fabric](service-fabric-application-upgrade.md)
