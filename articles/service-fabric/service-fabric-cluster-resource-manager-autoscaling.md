---
title: Azure Service Fabric Hizmetleri ve kapsayıcıları otomatik ölçeklendirme
description: Azure Service Fabric, hizmetler ve kapsayıcılar için otomatik ölçeklendirme ilkeleri ayarlamanıza olanak sağlar.
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.custom: devx-track-csharp
ms.openlocfilehash: 060bb9dcdd504846c76ab4c782b2857fdddfa394
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91354811"
---
# <a name="introduction-to-auto-scaling"></a>Otomatik ölçeklendirmeye giriş
Otomatik ölçeklendirme, hizmetlerin raporlanması gereken yükün veya kaynakların kullanımlarına göre dinamik olarak ölçeklendirilmesine yönelik Service Fabric ek bir özelliktir. Otomatik ölçeklendirme, büyük ölçüde esneklik sağlar ve isteğe bağlı olarak hizmetinizin ek örneklerinin veya bölümlerinin sağlanmasına olanak tanır. Otomatik ölçeklendirme işleminin tamamı otomatik ve şeffaftır ve ilkeleri bir hizmette ayarladıktan sonra hizmet düzeyinde el ile ölçeklendirme işlemlerine gerek yoktur. Otomatik Ölçeklendirme hizmeti oluşturma sırasında veya hizmeti güncelleştirilerek herhangi bir zamanda açılabilir.

Belirli bir hizmet üzerindeki yükün zamana göre farklılık gösterdiği, otomatik ölçeklendirmenin yararlı olduğu yaygın bir senaryo. Örneğin, bir ağ geçidi gibi bir hizmet, gelen istekleri işlemek için gereken kaynak miktarına göre ölçeklendirebilirler. Bu ölçeklendirme kurallarının nasıl görünebileceklerini bir örneğe bakalım:
* Ağ geçidimin tüm örnekleri ortalama üzerinde ikiden fazla çekirdek kullanıyorsa, daha sonra bir örnek ekleyerek Ağ Geçidi hizmetini ölçeklendirin. Bu saati her saat yapın, ancak toplamda yedi taneden fazla örneğe sahip olmaz.
* Ağ geçidimin tüm örnekleri ortalama 0,5 çekirdek kullanıyorsa, bir örneği kaldırarak hizmeti ' de ölçeklendirin. Bu saati her saat yapın, ancak toplamda üçten az örnek yoktur.

Otomatik ölçeklendirme, hem kapsayıcılar hem de normal Service Fabric Hizmetleri için desteklenir. Otomatik ölçeklendirmeyi kullanabilmeniz için, Service Fabric çalışma zamanının 6,2 veya sonraki bir sürümü üzerinde çalışıyor olmanız gerekir. 

Bu makalenin geri kalanında ölçekleme ilkeleri, otomatik ölçeklendirmeyi etkinleştirme veya devre dışı bırakma yolları açıklanmakta ve bu özelliğin nasıl kullanılacağına ilişkin örnekler verilmektedir.

## <a name="describing-auto-scaling"></a>Otomatik ölçeklendirmeyi açıklama
Otomatik ölçeklendirme ilkeleri, bir Service Fabric kümesindeki her bir hizmet için tanımlanabilir. Her ölçeklendirme ilkesi iki bölümden oluşur:
* **Ölçeklendirme tetikleyicisi** , hizmetin ölçeklendirilmesine ne zaman gerçekleştirileceğini açıklar. Tetikleyicide tanımlanan koşullar, bir hizmetin ölçeklendirilmesi gerekip gerekmediğini belirlemede düzenli aralıklarla denetlenir.

* **Ölçeklendirme mekanizması** , tetiklendikten sonra ölçeklendirmeyi nasıl gerçekleştirileceğini açıklar. Mekanizma yalnızca tetikleyiciden gelen koşullar karşılandığında uygulanır.

Şu anda desteklenen tüm tetikleyiciler, [mantıksal yük ölçümleriyle](service-fabric-cluster-resource-manager-metrics.md)veya CPU ya da bellek kullanımı gibi fiziksel ölçülerle çalışır. Her iki durumda da Service Fabric, ölçüm için bildirilen yükü izler ve ölçeklendirmeyi, ölçeklendirmenin gerekip gerekmediğini belirlemede düzenli aralıklarla değerlendirir.

Otomatik ölçeklendirme için şu anda desteklenen iki mekanizma vardır. Birincisi, durum bilgisi olmayan hizmetler veya [örnek](service-fabric-concepts-replica-lifecycle.md)ekleyerek veya kaldırarak otomatik ölçeklendirmenin gerçekleştirildiği kapsayıcılar için tasarlanmıştır. Durum bilgisiz ve durum bilgisi olmayan hizmetler için, otomatik ölçeklendirme, hizmetin adlandırılmış [bölümlerini](service-fabric-concepts-partitioning.md) ekleyerek veya kaldırarak da gerçekleştirilebilir.

> [!NOTE]
> Şu anda hizmet başına yalnızca bir ölçeklendirme ilkesi desteği vardır ve ölçek ilkesi başına yalnızca bir ölçeklendirme tetikleyicisi vardır.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Örnek tabanlı ölçeklendirmeyle ortalama bölüm yükleme tetikleyicisi
İlk tetikleyici türü, durum bilgisi olmayan bir hizmet bölümündeki örneklerin yükünü temel alır. Ölçüm yüklemeleri, bir bölümün her örneğinin yükünü elde etmek için ilk kez düzgünleştirilir ve bu değerler bölümün tüm örneklerinde ortalaması alınır. Hizmetin ne zaman ölçekleneceğini tespit eden üç etken vardır:

* _Alt yük eşiği_ , hizmetin ne zaman **ölçeklendirileceğini** belirleyen bir değerdir. Bölümlerin tüm örneklerinin ortalama yükü bu değerden düşükse, hizmet içinde ölçeklendirilecektir.
* _Büyük yük eşiği_ , hizmetin ne zaman gösterileceğini belirleyen bir **değerdir.** Bölümün tüm örneklerinin ortalama yükü bu değerden yüksekse, hizmet dışa ayarlanır.
* _Ölçeklendirme aralığı_ , tetikleyicinin ne sıklıkta denetleneceğini belirler. Tetikleyici denetlendikten sonra, ölçeklendirilmesi gerekiyorsa mekanizmanın uygulanması gerekir. Ölçeklendirme gerekmiyorsa, hiçbir eylem yapılmaz. Her iki durumda da, ölçeklendirmenin zaman aşımı süresi dolmadan önce tetikleyici yeniden denetlenmeyecektir.

Bu tetikleyici yalnızca durum bilgisi olmayan hizmetler (durum bilgisi olmayan kapsayıcılar veya Service Fabric Hizmetleri) ile kullanılabilir. Bir hizmette birden çok bölüm varsa, tetikleyici her bölüm için ayrı olarak değerlendirilir ve her bölüm, belirtilen mekanizmanın bağımsız olarak uygulanmasını sağlar. Bu nedenle, bu durumda hizmetin bazı bölümlerinin ölçeği ölçeklenmez, bazıları içinde ölçeklenmez ve bazıları, yüküne bağlı olarak aynı anda ölçeklendirilmez.

Bu tetikleyici ile kullanılabilen tek mekanizma Partitionınstancecountscalemechanısm ' dir. Bu mekanizmanın nasıl uygulanacağını tespit eden üç etken vardır:
* _Ölçek artışı_ , mekanizma tetiklendiğinde kaç örnek ekleneceğini veya kaldırılacağını belirler.
* _En fazla örnek sayısı_ ölçekleme için üst sınırı tanımlar. Bölüm örneklerinin sayısı bu sınıra ulaşırsa, yük ne olursa olsun, hizmet ölçeklenmez. -1 değerini belirterek bu sınırı atlayabilirsiniz ve bu durumda hizmet mümkün olduğunca (sınır kümede kullanılabilir olan düğüm sayısıdır), bu sınırı atlamak mümkündür.
* _Minimum örnek sayısı_ ölçekleme için alt sınırı tanımlar. Bölüm örneklerinin sayısı bu sınıra ulaşırsa, yük ne olursa olsun hizmet bu sınıra göre ölçeklenmez.

## <a name="setting-auto-scaling-policy-for-instance-based-scaling"></a>Örnek tabanlı ölçeklendirme için otomatik ölçeklendirme ilkesi ayarlanıyor

### <a name="using-application-manifest"></a>Uygulama bildirimini kullanma
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C# API 'Lerini kullanma
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>PowerShell 'i kullanma
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Bölüm tabanlı ölçeklendirmeyle ortalama hizmet yükleme tetikleyicisi
İkinci tetikleyici, bir hizmetin tüm bölümlerinin yükünü temel alır. Ölçüm yüklemeleri, bir bölümün her çoğaltması veya örneği için yük almak üzere ilk olarak düzgünleştirilir. Durum bilgisi olan hizmetler için, Bölüm yükü birincil çoğaltmanın yükü olarak kabul edilir, ancak durum bilgisiz Hizmetleri için bölüm yükü bölümün tüm örneklerinin ortalama yüklerdir. Bu değerler, hizmetin tüm bölümlerinde ortalaması alınır ve bu değer otomatik ölçeklendirmeyi tetiklemek için kullanılır. Önceki mekanizmasıyla aynı şekilde, hizmetin ne zaman ölçekleneceğini tespit eden üç etken vardır:

* _Alt yük eşiği_ , hizmetin ne zaman **ölçeklendirileceğini** belirleyen bir değerdir. Hizmetin tüm bölümlerinin ortalama yükü bu değerden düşükse, hizmet içinde ölçeklendirilir.
* _Büyük yük eşiği_ , hizmetin ne zaman gösterileceğini belirleyen bir **değerdir.** Hizmetin tüm bölümlerinin ortalama yükü bu değerden yüksekse, hizmet dışa ayarlanır.
* _Ölçeklendirme aralığı_ , tetikleyicinin ne sıklıkta denetleneceğini belirler. Tetikleyici denetlendikten sonra, ölçeklendirilmesi gerekiyorsa mekanizmanın uygulanması gerekir. Ölçeklendirme gerekmiyorsa, hiçbir eylem yapılmaz. Her iki durumda da, ölçeklendirmenin zaman aşımı süresi dolmadan önce tetikleyici yeniden denetlenmeyecektir.

Bu tetikleyici her ikisi de durum bilgisi olan ve durum bilgisi olmayan hizmetler ile kullanılabilir. Bu tetikleyici ile kullanılabilen tek mekanizma AddRemoveIncrementalNamedPartitionScalingMechanism ' dir. Hizmet ölçeklendirildiğinde yeni bir bölüm eklenir ve hizmet mevcut bölümlerden birinde ölçeklendirilirse kaldırılır. Hizmet oluşturulduğunda veya güncelleştirilirken denetlenecek kısıtlamalar vardır ve bu koşullar karşılanmazsa hizmet oluşturma/güncelleştirme başarısız olur:
* Hizmet için adlandırılmış bölüm şeması kullanılmalıdır.
* Bölüm adları, "0", "1",... gibi ardışık tamsayı sayılar olmalıdır.
* İlk bölüm adı "0" olmalıdır.

Örneğin, bir hizmet başlangıçta üç bölümden oluşturulmuşsa, Bölüm adı için geçerli tek olasılık "0", "1" ve "2" dir.

Gerçekleştirilen gerçek otomatik ölçeklendirme işlemi, bu adlandırma düzenine de uyum sağlayacak:
* Hizmetin geçerli bölümleri "0", "1" ve "2" olarak adlandırılmışsa, ölçek genişletme için eklenecek Bölüm "3" olarak adlandıralınacaktır.
* Hizmetin geçerli bölümleri "0", "1" ve "2" olarak adlandırılmışsa, içinde ölçeklendirilmesi için kaldırılacak Bölüm "2" adlı bölümdür.

Örnekleri ekleyerek veya kaldırarak ölçeklendirmeyi kullanan mekanizmasıyla aynı şekilde, bu mekanizmanın nasıl uygulanacağını belirten üç parametre vardır:
* _Ölçek artışı_ , mekanizma tetiklendiğinde kaç bölümden ekleneceğini veya kaldırılacağını belirler.
* _En fazla bölüm sayısı_ ölçekleme için üst sınırı tanımlar. Hizmetin bölüm sayısı bu sınıra ulaşırsa, yük ne olursa olsun, hizmet ölçeklenmez. -1 değerini belirterek bu sınırı atlamak mümkündür ve bu durumda hizmet mümkün olduğunca (sınır kümenin gerçek kapasitesidir) ayarlanır.
* _Minimum örnek sayısı_ ölçekleme için alt sınırı tanımlar. Hizmetin bölüm sayısı bu sınıra ulaşırsa, yük ne olursa olsun hizmet bu sınıra göre ölçeklenmez.

> [!WARNING] 
> AddRemoveIncrementalNamedPartitionScalingMechanism, durum bilgisi olmayan hizmetlerle kullanıldığında, Service Fabric **bildirim veya uyarı olmaksızın** bölüm ekler veya kaldırır. Ölçeklendirme mekanizması tetiklendiğinde verilerin yeniden bölümlenmesi gerçekleştirilmeyecektir. Ölçeği genişletme işlemi durumunda, yeni bölümler boş olur ve çalışma sırasında ölçekleme durumunda, **bölüm içerdiği tüm verilerle birlikte silinir**.

## <a name="setting-auto-scaling-policy-for-partition-based-scaling"></a>Bölüm tabanlı ölçeklendirme için otomatik ölçeklendirme ilkesi ayarlanıyor

### <a name="using-application-manifest"></a>Uygulama bildirimini kullanma
``` xml
<NamedPartition>
    <Partition Name="0" />
</NamedPartition>
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C# API 'Lerini kullanma
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>PowerShell 'i kullanma
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>Kaynaklara göre otomatik ölçeklendirme

Kaynak İzleyicisi hizmetini gerçek kaynaklara göre ölçeklendirmeye olanak tanımak için

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Gerçek fiziksel kaynakları temsil eden iki ölçüm vardır. Bunlardan biri, gerçek CPU kullanımını temsil eden servicefabric:/_CpuCores (dolayısıyla 0,5, yarı bir çekirdeği temsil eder) ve diğeri, MB olarak bellek kullanımını temsil eden servicefabric:/_MemoryInMB olur.
ResourceMonitorService, Kullanıcı hizmetlerinin CPU ve bellek kullanımını izlemekten sorumludur. Bu hizmet, olası kısa süreli artışlar için hesaba göre ağırlıklı hareketli ortalama uygular. Kaynak izleme, Windows üzerinde Kapsayıcılı ve kapsayıcısız uygulamalar ve Linux üzerinde Kapsayıcılı uygulamalar için desteklenir. Kaynaklarda otomatik ölçekleme yalnızca [özel işlem modelinde](service-fabric-hosting-model.md#exclusive-process-model)etkinleştirilen hizmetler için etkinleştirilmiştir.

## <a name="next-steps"></a>Sonraki adımlar
[Uygulama ölçeklenebilirliği](service-fabric-concepts-scalability.md)hakkında daha fazla bilgi edinin.
