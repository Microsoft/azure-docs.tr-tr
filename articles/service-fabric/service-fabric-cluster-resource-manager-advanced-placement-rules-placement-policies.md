---
title: Service Fabric kümesi Kaynak Yöneticisi-yerleştirme Ilkeleri
description: Service Fabric Hizmetleri için ek yerleştirme ilkelerine ve kurallarına genel bakış
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 263e45928642aa74d682fc490e424a24deeb8076
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790690"
---
# <a name="placement-policies-for-service-fabric-services"></a>Service Fabric Hizmetleri için yerleştirme ilkeleri
Yerleştirme ilkeleri, hizmet yerleşimini bazı belirli, daha az yaygın senaryolarda yönetmek için kullanılabilecek ek kurallardır. Bu senaryolara örnek olarak şunlar verilebilir:

- Service Fabric kümeniz, birden çok şirket içi veri merkezi veya Azure bölgesi gibi coğrafi uzaklıkları kapsar
- Ortamınız, çok sayıda coğrafi veya yasal denetim ya da uygulamanız gereken ilke sınırlarına sahip olduğunuz başka bir durum için birden fazla alanı kapsar
- Büyük uzakların veya daha az güvenilir ağ bağlantılarının kullanımı nedeniyle iletişim performansı veya gecikme sorunları vardır
- Belirli iş yüklerini, diğer iş yükleriyle veya müşterilere yakınlık duyacak şekilde en iyi çaba olarak tutmanız gerekir
- Tek bir düğümde bir bölümün birden çok durum bilgisiz örneğine ihtiyacınız var

Bu gereksinimlerin çoğu kümenin hata etki alanları olarak temsil edilen fiziksel düzenine göre hizalanır. 

Bu senaryolara yönelik yardım eden gelişmiş yerleştirme ilkeleri şunlardır:

1. Geçersiz etki alanları
2. Gerekli etki alanları
3. Tercih edilen etki alanları
4. Çoğaltma paketlemeye izin vermeme
5. Düğümde birden çok durum bilgisiz örneğine izin ver

Aşağıdaki denetimlerin çoğu düğüm özellikleri ve yerleştirme kısıtlamaları aracılığıyla yapılandırılabilir, ancak bazıları daha karmaşıktır. Şeyleri daha kolay hale getirmek için Service Fabric kümesi Kaynak Yöneticisi bu ek yerleştirme ilkeleri sağlar. Yerleştirme ilkeleri, adlandırılmış hizmet örneği temelinde yapılandırılır. Ayrıca, dinamik olarak da güncelleştirilemeyebilir.

## <a name="specifying-invalid-domains"></a>Geçersiz etki alanlarını belirtme
**Invaliddomain** yerleştirme ilkesi belirli bir hizmet için belirli bir hata etki alanının geçersiz olduğunu belirtmenizi sağlar. Bu ilke, belirli bir hizmetin belirli bir alanda hiçbir şekilde çalışmamasını sağlar; örneğin, coğrafi veya kurumsal ilke nedenleriyle. Ayrı ilkeler aracılığıyla birden çok geçersiz etki alanı belirtilebilir.

<center>

![Geçersiz etki alanı örneği][Image1]
</center>

Kod:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Gerekli etki alanlarını belirtme
Gerekli etki alanı yerleştirme ilkesi, hizmetin yalnızca belirtilen etki alanında mevcut olmasını gerektirir. Birden çok gerekli etki alanı ayrı ilkeler aracılığıyla belirtilebilir.

<center>

![Gerekli etki alanı örneği][Image2]
</center>

Kod:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Durum bilgisi olan bir hizmetin birincil çoğaltmaları için tercih edilen bir etki alanı belirtme
Tercih edilen birincil etki alanı, birincili yerleştirilecek hata etki alanını belirtir. Her şey sağlıklı olduğunda, birincil bu etki alanında sona erer. Etki alanı veya birincil çoğaltma başarısız olursa veya kapatılırsa, birincil, aynı etki alanında ideal olarak başka bir konuma gider. Bu yeni konum tercih edilen etki alanında değilse, Küme Kaynak Yöneticisi en kısa sürede tercih edilen etki alanına geri döner. Doğal olarak bu ayar yalnızca durum bilgisi olan hizmetler için anlamlı hale gelir. Bu ilke, Azure bölgelerine veya birden çok veri merkezine yayılmış, ancak belirli bir konuma yerleştirmeyi tercih eden hizmetlere sahip olan kümeler için en yararlı seçenektir. Kendi kullanıcılarına veya diğer hizmetlere yönelik olarak yakın bir şekilde tutulması, özellikle okuma için, varsayılan olarak alt değerler tarafından işlenen daha düşük gecikme süresi sağlamaya yardımcı olur.

<center>

![Tercih edilen birincil etki alanları ve yük devretme][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Çoğaltma dağıtımı gerektirme ve paketlemeye izin vermeme
Çoğaltmalar _normalde_ , küme sağlıklı olduğunda hata ve yükseltme etki alanlarına dağıtılır. Ancak, belirli bir bölüm için birden fazla çoğaltmanın, geçici olarak tek bir etki alanına paketlenebileceği durumlar vardır. Örneğin, kümenin üç hata etki alanında dokuz düğüm olduğunu, FD:/0, FD:/1 ve FD:/2 olduğunu varsayalım. Ayrıca, hizmetinizin üç çoğaltma olduğunu da söylüm. FD:/1 ve FD:/2 ' deki çoğaltmalar için kullanılan düğümlerin çalışmıyor olduğunu varsayalım. Normalde Küme Kaynak Yöneticisi, aynı hata etki alanlarındaki diğer düğümleri tercih edebilir. Bu durumda, bu etki alanındaki diğer düğümlerin hiçbiri geçerli olmadığı için kapasite sorunlarından dolayı diyelim. Küme Kaynak Yöneticisi bu çoğaltmalar için değişiklik oluşturduğunda, FD:/0 ' da düğümleri seçmesi gerekir. Ancak _bunun yapılması,_ hata etki alanı kısıtlamasının ihlal edildiği bir durum oluşturur. Paket çoğaltmaları, çoğaltma kümesinin tamamının nasıl gidebilmesinden veya kaybedilme olasılığını artırır. 

> [!NOTE]
> Kısıtlamalar ve kısıtlama öncelikleri hakkında daha fazla bilgi için [Bu konuya](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities)bakın.
>

"" Gibi bir sistem durumu iletisi görüyorsanız `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain` , bu koşula veya bunun gibi bir duruma ulaşmış olursunuz. Genellikle yalnızca bir veya iki çoğaltma geçici olarak birlikte paketlenmiştir. Bu nedenle, belirli bir etki alanında çoğaltmalardan daha az bir çekirdek olduğu sürece güvenli olursunuz. Paketleme nadir bir durumdur, ancak bu durum genellikle düğümler geri geldiğinden bu durumlar geçicidir. Düğümler kapalıysa ve Küme Kaynak Yöneticisi değişiklik oluşturmaya ihtiyaç duyuyorsa, genellikle ideal hata etki alanlarında bulunan başka düğümler vardır.

Bazı iş yükleri, daha az etki alanına paketlenmiş olsalar bile her zaman hedef yineleme sayısına sahip olmayı tercih eder. Bu iş yükleri toplam eşzamanlı kalıcı etki alanı arızalarına karşı işlenir ve genellikle yerel durumu kurtarabilir. Diğer iş yükleri, risk doğruluğu veya veri kaybı dışında kalma süresini ortadan kaldırmaktı. Çoğu üretim iş yükleri üçten fazla çoğaltma, üç hata etki alanı ve hata etki alanı başına çok sayıda geçerli düğüm ile çalışır. Bu nedenle, varsayılan davranış varsayılan olarak etki alanı paketlemeye izin verir. Varsayılan davranış, geçici bir etki alanı paketleme anlamına gelir.

Belirli bir iş yükü için bu tür bir paket devre dışı bırakmak istiyorsanız, `RequireDomainDistribution` ilkeyi hizmette belirtebilirsiniz. Bu ilke ayarlandığında, Küme Kaynak Yöneticisi aynı bölümden iki çoğaltmanın aynı hata veya yükseltme etki alanında çalıştırılmasını sağlar.

Kod:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Şimdi, coğrafi olarak yayılmayan bir kümedeki hizmetler için bu yapılandırmaların kullanılması mümkün midir? Ancak harika bir neden yoktur. Senaryolar gerektirmedikleri takdirde gerekli, geçersiz ve tercih edilen etki alanı yapılandırmalarının önlenebilir olması gerekir. Belirli bir iş yükünü tek bir raf içinde çalışacak şekilde zorlamaya ya da yerel kümenizin bazı segmentini başka bir şekilde tercih etmeye çalışmak herhangi bir anlamlı yapmaz. Farklı donanım yapılandırmalarının hata etki alanlarına yayılması ve normal yerleştirme kısıtlamaları ve düğüm özellikleri aracılığıyla işlenmesi gerekir.

## <a name="placement-of-multiple-stateless-instances-of-a-partition-on-single-node"></a>Tek düğümdeki bir bölümün birden çok durum bilgisi olmayan örneğinin yerleştirilmesi
**Allowmultiplestatelessınstancesonnode** yerleştirme ilkesi, tek bir düğümdeki bir bölümün birden çok durum bilgisi olmayan örneğinin yerleştirilmesine izin verir. Varsayılan olarak, tek bir bölümün birden çok örneği bir düğüme yerleştirilemez. Bir-1 hizmeti olsa da, belirli bir adlandırılmış hizmet için kümedeki düğüm sayısının ötesinde örnek sayısını ölçeklendirmek mümkün değildir. Bu yerleştirme ilkesi, bu kısıtlamayı kaldırır ve InstanceCount 'un düğüm sayısından daha yüksek olarak belirtilmesini sağlar.

"" Gibi bir sistem durumu iletisi görüyorsanız `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: ReplicaExclusion` , bu koşula veya bunun gibi bir duruma ulaşmış olursunuz. 

`AllowMultipleStatelessInstancesOnNode`Hizmette ilke belirtilerek, InstanceCount, kümedeki düğümlerin sayısından daha fazla ayarlanabilir.

Kod:

```csharp
ServicePlacementAllowMultipleStatelessInstancesOnNodePolicyDescription allowMultipleInstances = new ServicePlacementAllowMultipleStatelessInstancesOnNodePolicyDescription();
serviceDescription.PlacementPolicies.Add(allowMultipleInstances);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless –PartitionSchemeSingleton –PlacementPolicy @(“AllowMultipleStatelessInstancesOnNode”) -InstanceCount 10 -ServicePackageActivationMode ExclusiveProcess 
```

> [!NOTE]
> Yerleştirme ilkesi Şu anda önizleme aşamasındadır ve `EnableUnsupportedPreviewFeatures` küme ayarlarının arkasında. Bu, şimdilik bir önizleme özelliği olduğundan, Preview config 'in ayarlanması kümenin sürümüne/sürümünden alınmasını engeller. Diğer bir deyişle, özelliği denemek için yeni bir küme oluşturmanız gerekir.
>

> [!NOTE]
> Şu anda ilke yalnızca ExclusiveProcess [hizmet paketi etkinleştirme modu](/dotnet/api/system.fabric.description.servicepackageactivationmode)Ile durum bilgisi olmayan hizmetler için desteklenir.
>

> [!WARNING]
> İlke, statik bağlantı noktası uç noktaları ile kullanıldığında desteklenmez. Aynı düğümdeki birden çok örnek aynı bağlantı noktasına bağlamaya çalıştığı ve gelmediğinden, birlikte her ikisi de birlikte kullanılması sağlıksız bir kümeye yol açabilir. 
>

> [!NOTE]
> Bu yerleştirme ilkesiyle yüksek bir [Minınstancecount](/dotnet/api/system.fabric.description.statelessservicedescription.mininstancecount) değeri kullanılması, takılı uygulama yükseltmelerine yol açabilir. Örneğin, beş düğümlü bir kümeniz varsa ve InstanceCount = 10 ' u ayarladıysanız, her düğümde iki örneğe sahip olursunuz. Minınstancecount = 9 ' u ayarlarsanız, denenen bir uygulama yükseltmesi takılmasına sahip olabilir; Minınstancecount = 8 ile bu kaçınılabilir.
>

## <a name="next-steps"></a>Sonraki adımlar
- Hizmetleri yapılandırma hakkında daha fazla bilgi için [Hizmetleri yapılandırma hakkında bilgi edinin](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
