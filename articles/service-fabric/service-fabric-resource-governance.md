---
title: Kapsayıcılar ve hizmetler için Azure Service Fabric kaynak İdaresi | Microsoft Docs
description: Azure Service Fabric, kapsayıcılar içinde veya dışında çalışan hizmetler için kaynak sınırları belirtmenize olanak tanır.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: atsenthi
ms.openlocfilehash: 44abb297b9ce0eafadd3af9539d5b12751360319
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242901"
---
# <a name="resource-governance"></a>Kaynak idaresi

Aynı düğüm veya küme üzerinde birden fazla hizmet çalıştırırken, bir hizmetin daha fazla kaynak tüketmesi ve bu da işlemdeki diğer hizmetlerden kaynaklanabilir. Bu sorun, "gürültülü komşu" sorunu olarak adlandırılır. Azure Service Fabric, geliştiricilerin kaynakları garanti etmek ve kaynak kullanımını sınırlamak için hizmet başına ayırmaları ve sınırları belirtmesini sağlar.

> Bu makaleye devam etmeden önce [Service Fabric uygulama modeli](service-fabric-application-model.md) ve [Service Fabric barındırma modeli](service-fabric-hosting-model.md)hakkında bilgi sahibi olmanız önerilir.
>

## <a name="resource-governance-metrics"></a>Kaynak idare ölçümleri

Kaynak İdaresi, [hizmet paketine](service-fabric-application-model.md)uygun olarak Service Fabric desteklenir. Hizmet paketine atanan kaynaklar kod paketleri arasında daha da ayrılabilir. Belirtilen kaynak sınırları, kaynakların rezervasyonu da anlamına gelir. Service Fabric, iki yerleşik [ölçüm](service-fabric-cluster-resource-manager-metrics.md)ile hizmet PAKETI başına CPU ve bellek belirtmeyi destekler:

* *CPU* (ölçüm adı `servicefabric:/_CpuCores`): konak makinede bulunan mantıksal çekirdek. Tüm düğümlerdeki tüm çekirdekler aynı şekilde ağırlıklı olarak dağıtılır.

* *Bellek* (ölçüm adı `servicefabric:/_MemoryInMB`): bellek megabayt cinsinden ifade edilir ve makinede bulunan fiziksel bellekle eşlenir.

Bu iki ölçüm için [küme kaynak yöneticisi](service-fabric-cluster-resource-manager-cluster-description.md) toplam küme kapasitesini, kümedeki her düğümdeki yükü ve kümedeki kalan kaynakları izler. Bu iki ölçüm, diğer Kullanıcı veya özel ölçümle eşdeğerdir. Var olan tüm özellikler bunlarla birlikte kullanılabilir:

* Küme, bu iki ölçümlere (varsayılan davranış) göre [dengelenebilir](service-fabric-cluster-resource-manager-balancing.md) .
* Küme, bu iki ölçümlere göre [birleştirilebilirler](service-fabric-cluster-resource-manager-defragmentation-metrics.md) .
* [Bir kümeyi açıkladığında](service-fabric-cluster-resource-manager-cluster-description.md), bu iki ölçüm için arabelleğe alınan kapasite ayarlanabilir.

[Dinamik yük raporlama](service-fabric-cluster-resource-manager-metrics.md) bu ölçümler için desteklenmez ve bu ölçümler için Yüklemeler oluşturma sırasında tanımlanmıştır.

## <a name="resource-governance-mechanism"></a>Kaynak idare mekanizması

Service Fabric çalışma zamanı şu anda kaynaklar için ayırma sağlamıyor. Bir işlem veya kapsayıcı açıldığında, çalışma zamanı, kaynak sınırlarını oluşturulma zamanında tanımlanan yükler olarak ayarlar. Ayrıca, çalışma zamanı, kaynaklar aşıldığında kullanılabilir olan yeni hizmet paketlerinin açılmasını reddeder. İşlemin nasıl çalıştığını daha iyi anlamak için iki CPU çekirdeğine sahip bir düğüme örnek alalım (bellek İdaresi mekanizması eşdeğerdir):

1. İlk olarak, düğüme bir kapsayıcı yerleştirilir ve bir CPU çekirdeği ister. Çalışma zamanı kapsayıcıyı açar ve CPU sınırını bir çekirdekli olarak ayarlar. Kapsayıcı birden fazla çekirdek kullanamaz.

2. Daha sonra, bir hizmetin bir kopyası düğüme yerleştirilir ve karşılık gelen hizmet paketi bir CPU çekirdeği sınırı belirtir. Çalışma zamanı, kod paketini açar ve CPU sınırını bir çekirdekli olarak ayarlar.

Bu noktada, limitlerin toplamı, düğümün kapasitesine eşittir. Bir işlem ve kapsayıcı her biri çekirdekle çalışır ve birbirleriyle müdahale etmez. Service Fabric, CPU sınırını belirtirken daha fazla kapsayıcı veya çoğaltma yerleştirmez.

Ancak, diğer işlemlerin CPU için çekişme olabileceği iki durum vardır. Bu durumlarda, örneğimizde bir işlem ve bir kapsayıcı, gürültülü komşu sorunu yaşayabilirler:

* *Yönetilen ve yönetilmeyen Hizmetleri ve kapsayıcıları karıştırma*: bir Kullanıcı hiçbir kaynak İdaresi olmadan bir hizmet oluşturursa, çalışma zamanı bu dosyayı kaynak olmadan görür ve örneğimizdeki düğüme yerleştirebilir. Bu durumda, bu yeni işlem düğümde zaten çalışmakta olan hizmetlerin ücretine göre bazı CPU kullanımını etkili bir şekilde tüketir. Bu sorunun iki çözümü vardır. Aynı kümede yönetilen ve yönetilmeyen Hizmetleri karışmayın ya da bu iki hizmet türünün aynı düğüm kümesine bitmesi için [yerleştirme kısıtlamalarını](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) kullanın.

* *Düğüm üzerinde başka bir işlem başlatıldığında Service Fabric dışında (örneğin, bir işletim sistemi hizmeti)* : Bu durumda, Service Fabric dışındaki işlem, var olan hizmetlerle CPU için de devam ediyor. Bu sorunun çözümü, sonraki bölümde gösterildiği gibi, işletim sistemi ek yükünü hesaba eklemek için düğüm kapasitelerinin doğru şekilde ayarlanmadır.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Kaynak yönetimini etkinleştirmek için küme kurulumu

Bir düğüm başlatıldığında ve kümeye katıldığında, Service Fabric kullanılabilir bellek miktarını ve kullanılabilir çekirdek sayısını algılar ve bu iki kaynak için düğüm kapagruplarını ayarlar.

İşletim sistemi için arabellek alanını bırakmak ve diğer işlemlerin düğümde çalışıyor olması için, Service Fabric düğümde kullanılabilir kaynakların yalnızca %80 ' unu kullanır. Bu yüzde yapılandırılabilir ve küme bildiriminde değiştirilebilir.

Aşağıda, kullanılabilir CPU 'nun %50 ' i ve kullanılabilir belleğin %70 ' i kullanmak için Service Fabric nasıl talimat alınacağını gösteren bir örnek verilmiştir:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Düğüm kapasitelerinin tam el ile kurulumunu yapmanız gerekiyorsa, kümedeki düğümleri tanımlamak için düzenli mekanizmayı kullanabilirsiniz. Düğümün dört çekirdekli ve 2 GB bellek ile nasıl ayarlanacağı hakkında bir örnek aşağıda verilmiştir:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Kullanılabilir kaynakların otomatik algılanması etkin olduğunda ve düğüm kapasiteleri küme bildiriminde el ile tanımlandığında Service Fabric, düğümün kullanıcının tanımladığı kapasiteyi desteklemek için yeterli kaynağa sahip olup olmadığını denetler:

* Bildirimde tanımlanan düğüm kapasiteleri, düğümdeki kullanılabilir kaynaklardan azsa veya buna eşitse, Service Fabric bildirimde belirtilen kapasiteleri kullanır.

* Bildirimde tanımlanan düğüm kapasiteleri kullanılabilir kaynaklardan fazlaysa, Service Fabric kullanılabilir kaynakları düğüm kapasiteleri olarak kullanır.

Gerekli değilse, kullanılabilir kaynakların otomatik algılanması kapatılabilir. Devre dışı bırakmak için aşağıdaki ayarı değiştirin:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

En iyi performans için, küme bildiriminde aşağıdaki ayar de açılmalıdır:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Service Fabric sürüm 7,0 ' den başlayarak, düğüm kaynak kapasitelerinin Kullanıcı tarafından el ile değer sağladığı durumlarda düğüm kaynak kapasitelerinin nasıl hesaplandığını gösteren kuralı güncelleştirdik. Aşağıdaki senaryoyu ele alalım:
>
> * Düğümde 10 CPU çekirdeği toplamı vardır
> * SF, düğüm üzerinde çalışan diğer hizmetler için %20 ' nin (System Services Service Fabric dahil) bir arabelleğini atan Kullanıcı Hizmetleri (varsayılan ayar) için toplam kaynakların %80 ' sini kullanacak şekilde yapılandırılmıştır.
> * Kullanıcı, CPU çekirdeği ölçümü için düğüm kaynak kapasitesini el ile geçersiz kılmayı ve 5 çekirdeğe ayarlıyor
>
> Service Fabric Kullanıcı Hizmetleri için kullanılabilir kapasitenin aşağıdaki şekilde nasıl hesaplanacağını gösteren kuralı değiştirdik:
>
> * 7,0 Service Fabric önce, Kullanıcı Hizmetleri için kullanılabilir kapasite **5 çekirdeğe** hesaplanacak (kapasite arabelleği %20 ' si yoksayılır)
> * Service Fabric 7,0 ' den başlayarak, Kullanıcı Hizmetleri için kullanılabilir kapasite **4 çekirdeğe** hesaplanacak (kapasite arabelleği %20 ' si yok sayılır)

## <a name="specify-resource-governance"></a>Kaynak yönetimini belirtin

Kaynak idare limitleri, aşağıdaki örnekte gösterildiği gibi uygulama bildiriminde (Servicemanifestımport bölümünde) belirtilir:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```

Bu örnekte, **Servicepackagea** adlı hizmet paketi, yerleştirildiği düğümlerde bir çekirdek alır. Bu hizmet paketi iki kod paketi (**CodeA1** ve **CodeA2**) içerir ve her ikisi de `CpuShares` parametresini belirtir. CpuShares 512:256 oranı, çekirdeği iki kod paketine böler.

Bu nedenle, bu örnekte, CodeA1 'in iki katı DS 'yi alır ve CodeA2 bir çekirdekli (ve aynı şekilde bir yazılım garantisi rezervasyonu) alır. Kod paketleri için CpuShares belirtilmemişse, Service Fabric çekirdekleri aralarında eşit olarak böler.

Bellek sınırları mutlak olduğundan, her iki kod paketi de 1024 MB bellekle (ve aynı şekilde bir yazılım garantisi rezervasyonu) sınırlıdır. Kod paketleri (kapsayıcılar veya süreçler) Bu sınırdan daha fazla bellek ayıramıyor ve bu işlemi gerçekleştirmeye çalışırken bellek yetersiz özel durumu oluşur. Kaynak sınırı zorlamasının çalışması için, hizmet paketi içindeki tüm kod paketlerinin bellek sınırlarının belirtilmiş olması gerekir.

### <a name="using-application-parameters"></a>Uygulama parametrelerini kullanma

Kaynak idare ayarlarını belirtirken, birden çok uygulama yapılandırmasını yönetmek için [Uygulama parametrelerini](service-fabric-manage-multiple-environment-app-configuration.md) kullanmak mümkündür. Aşağıdaki örnek, uygulama parametrelerinin kullanımını gösterir:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

Bu örnekte, her bir hizmet paketinin 4 çekirdek ve 2 GB bellek aldığı, üretim ortamı için varsayılan parametre değerleri ayarlanır. Varsayılan değerleri uygulama parametre dosyalarıyla değiştirmek mümkündür. Bu örnekte, uygulamayı yerel olarak test etmek için bir parametre dosyası kullanılabilir; burada üretimden daha az kaynak alınır:

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> Uygulama parametreleriyle kaynak yönetimini belirtmek, Service Fabric sürüm 6,1 ' den başlayarak kullanılabilir.<br>
>
> Kaynak yönetimini belirtmek için uygulama parametreleri kullanıldığında, Service Fabric sürüm 6,1 ' den önceki bir sürüme düşürülemez.

## <a name="enforcing-the-resource-limits-for-user-services"></a>Kullanıcı Hizmetleri için kaynak sınırlarını zorlama

Kaynak İdaresi Service Fabric hizmetlerinize uygulanırken kaynak tarafından yönetilen hizmetlerin kaynak kotasını aşmayacağından emin olmaya devam ederken, pek çok kullanıcının bazı Service Fabric hizmetlerini yönetilmeyen modda çalıştırması gerekir. Yönetilmeyen Service Fabric Hizmetleri kullanılırken, "ard arda" yönetilmeyen hizmetlerin, Service Fabric düğümlerinde kullanılabilir tüm kaynakları tükettiği durumlarda, örneğin:

* Düğümlerde çalışan diğer hizmetlerin kaynakları (Service Fabric sistem hizmetleri dahil)
* Uygun olmayan bir durumda biten düğümler
* Service Fabric küme yönetimi API 'Leri yanıt vermiyor

Bu durumların oluşmasını önlemek için Service Fabric, *düğüm üzerinde çalışan tüm Service Fabric Kullanıcı Hizmetleri* (hem yönetilen hem de yönetilmeyen) için kaynak sınırlarını zorunlu kılabilir ve bu sayede Kullanıcı hizmetlerinin Belirtilen kaynak miktarı. Bu, ClusterManifest 'in Placementandloaddengeleme bölümündeki Enforceuserservicemetrickapasiteler yapılandırmasının değeri true olarak ayarlanarak elde edilir. Bu ayar varsayılan olarak kapalıdır.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Ek açıklamalar:

* Kaynak sınırı zorlaması yalnızca `servicefabric:/_CpuCores` ve `servicefabric:/_MemoryInMB` kaynak ölçümleri için geçerlidir
* Kaynak sınırı zorlaması yalnızca, kaynak ölçümlerine ait düğüm kapasiteleri otomatik algılama mekanizmasıyla veya Kullanıcı aracılığıyla düğüm kapasitelerinin el ile belirtilerek ( [etkinleştirme Için küme kurulumunda açıklandığı gibi) Service Fabric kullanılabilir olduğunda geçerlidir. Kaynak idare](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) bölümü). Düğüm kapasiteleri yapılandırılmamışsa, Kullanıcı Hizmetleri için ne kadar kaynak ayrılacağını bilemediğinden Service Fabric kaynak sınırı zorlama özelliği kullanılamaz. Service Fabric, "Enforceuserservicemetrickapasiteler" true ise ancak düğüm kapasiteleri yapılandırılmamışsa bir sistem durumu uyarısı verir.

## <a name="other-resources-for-containers"></a>Kapsayıcılar için diğer kaynaklar

CPU ve belleğin yanı sıra, kapsayıcılar için diğer kaynak sınırlarını belirtmek mümkündür. Bu sınırlar, kod paketi düzeyinde belirtilir ve kapsayıcı başlatıldığında uygulanır. CPU ve bellek ile farklı olarak, Cluster Kaynak Yöneticisi bu kaynakları bilmez ve hiçbir kapasite denetimi veya yük dengelemesi yapamayacaktır.

* *Memoryswapınmb*: bir kapsayıcının kullanabileceği takas belleği miktarı.
* *Memoryrezervationınmb*: yalnızca düğümde bellek çakışması algılandığında uygulanan bellek yönetimi için geçici sınır.
* *Cpupercent*: KAPSAYıCıNıN kullanabileceği CPU yüzdesi. Hizmet paketi için CPU sınırları belirtilmişse, bu parametre etkin bir şekilde yok sayılır.
* *Maximumıops*: bir kapsayıcının KULLANABILECEĞI Maksimum IOPS (okuma ve yazma).
* *Maximumiobytesps*: bir kapsayıcının kullanabileceği en yüksek GÇ (saniye başına bayt) (okuma ve yazma).
* *Blockioweight*: diğer kapsayıcılara göreli olarak blok GÇ ağırlığı.

Bu kaynaklar, CPU ve bellekle birleştirilebilir. Kapsayıcılar için ek kaynakların nasıl belirtilbir örneği aşağıda verilmiştir:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Sonraki adımlar

* Küme Kaynak Yöneticisi hakkında daha fazla bilgi edinmek için [Service Fabric Cluster Resource Manager 'A giriş](service-fabric-cluster-resource-manager-introduction.md)konusunu okuyun.
* Uygulama modeli, hizmet paketleri ve kod paketleri hakkında daha fazla bilgi edinmek ve çoğaltmaların bunlara nasıl eşlendiğini öğrenin-- [Service Fabric bir uygulamanın modelini](service-fabric-application-model.md)okuyun.
