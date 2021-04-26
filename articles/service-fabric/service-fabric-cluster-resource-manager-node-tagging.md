---
title: Azure Service Fabric dinamik düğüm etiketleri
description: Azure Service Fabric, düğüm etiketlerini dinamik olarak eklemenize ve kaldırmanıza olanak tanır.
author: yu-supersonic
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: branim
ms.custom: devx-track-csharp
ms.openlocfilehash: 712e6422060619e5567a74d6335320eff9ed8e66
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741931"
---
# <a name="introduction-to-dynamic-node-tags"></a>Dinamik düğüm etiketlerine giriş
Düğüm etiketleri, hizmetlerin yerleşimini etkilemek için düğümlere dinamik olarak Etiketler eklemenize ve bunları kaldırmanıza olanak tanır. Düğüm etiketleme çok esnektir ve uygulama veya küme yükseltmeleri olmadan hizmet yerleştirmesi üzerinde değişiklik yapılmasına izin verir. Etiketler herhangi bir zamanda düğümlere eklenebilir veya kaldırılabilir, ancak hizmetler oluşturulduğunda belirli Etiketler için gereksinimleri belirtebilir. Ayrıca, bir hizmet çalışırken, etiket gereksinimlerinin dinamik olarak güncelleştirilmesini de sağlayabilir.

Düğüm etiketleme, [yerleştirme kısıtlamalarına](service-fabric-cluster-resource-manager-configure-services.md) benzerdir ve genellikle bir hizmetin üzerinde çalıştığı düğümleri denetlemek için kullanılır. Her bir Service Fabric hizmeti, etiketin yerleştirilmesini veya çalışmaya devam etmesini gerektirecek şekilde yapılandırılabilir.

Düğüm etiketleme, tüm Service Fabric barındırılan hizmet türleri (Reliable Services, Konuk yürütülebilir dosyalar ve kapsayıcılar) için desteklenir. Düğüm etiketlemesini kullanmak için, Service Fabric çalışma zamanının 8,0 veya sonraki bir sürümünü çalıştırıyor olmanız gerekir.

Bu makalenin geri kalanında, düğüm etiketlemesini etkinleştirme veya devre dışı bırakma yolları açıklanmakta ve bu özelliğin nasıl kullanılacağına ilişkin örnekler verilmektedir.


## <a name="describing-dynamic-node-tags"></a>Dinamik düğüm etiketlerini açıklama
Hizmetler, gereken etiketleri belirtebilir. İki tür etiket vardır:
* **Yerleştirme için gereken Etiketler** , yalnızca hizmet yerleştirmesi için gerekli olan bir etiket kümesini tanımlıyor. Çoğaltma yerleştirildiğinde, bu Etiketler hizmet kesintiye uğramadan kaldırılabilir. Bu etiketlerden herhangi biri düğümünden kaldırılırsa, hizmet çoğaltması çalışmaya devam eder ve Service Fabric hizmeti kaldırmaz

* **Çalıştırmak için gereken Etiketler** , hizmetin yerleştirilmesi ve çalıştırılması için gerekli olan bir etiket kümesini açıkla. Çalışan etiketlerin herhangi biri kaldırılırsa Service Fabric, hizmeti bu etiketleri belirtilen başka bir düğüme taşır.

Örnek: yerleştirme etiketleri için gerekli bir dizi kapsayıcı Etkinleştirici Hizmeti kullandığınızda ve kapsayıcının yerleştirilmesi için bu hizmetin olması ve kapsayıcının etkin hale geldiğinde, artık etkileşleştirmeniz gerekmez, ancak bununla ilişkili etiketi kaldırabilirsiniz, ancak kapsayıcı çalışmaya devam etmelidir.
Etiket çalıştırmak için gerekli olan bir faturalandırma hizmetiniz varsa, bu, kullanıcıya yönelik hizmet ile birlikte çalışmak için yararlıdır. Faturalandırma hizmeti düğümde başarısız olursa, onunla ilişkili etiketi kaldırır ve kullanıcıya yönelik hizmet, faturalandırma hizmeti ve etiketi bulunan başka bir düğüme taşınır.

C# API 'Leri, REST API 'Leri veya PowerShell komutları gibi standart Service Fabric arabirim mekanizmaları kullanılarak tek bir düğümden etiket veya etiket kümesi eklenebilir, güncelleştirilir veya kaldırılabilir.

> [!NOTE]
> Düğüm etiketleri kullanılırken Service Fabric, UD/FD dağıtımlarını korumaz. Lütfen düğüm etiketlerini uygun şekilde yönetin. bu nedenle, etki alanları arasında hatalı etiketlerin dağıtılması nedeniyle FD/UD ihlalleri alınamaz.

## <a name="enabling-dynamic-node-tags"></a>Dinamik düğüm etiketlerini etkinleştirme
Bu özelliğin çalışması için, XML veya JSON kullanarak küme bildiriminin Placementandloaddengeleme bölümünde NodeTaggingEnabled config ' i etkinleştirmeniz gerekir:

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="NodeTaggingEnabled" Value="true" />
</Section>
```

Tek başına dağıtımlar veya Azure 'da barındırılan kümeler için Template.jsClusterConfig.jsaracılığıyla:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": " NodeTaggingEnabled ",
          "value": "true"
      }
    ]
  }
]
```

## <a name="setting-dynamic-node-tags"></a>Dinamik düğüm etiketlerini ayarlama

### <a name="using-powershell"></a>PowerShell’i kullanma

Düğüme düğüm etiketleri ekleniyor:

```posh
Add-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Bu komut, "SampleTag1" ve "SampleTag2" etiketlerini DB. 1 düğümüne ekleyecek.

Düğüm etiketini düğümden kaldır:

```posh
Remove-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Bu komut, "SampleTag1" ve "SampleTag2" etiketlerini DB. 1 düğümünde kaldıracak.

### <a name="using-c-apis"></a>C# API 'Lerini kullanma

Düğüme düğüm etiketleri ekleniyor:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.AddNodeTagsAsync("DB.1", nodeTagsList);
```

Düğüm etiketini düğümden kaldır:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.RemoveNodeTagsAsync("DB.1", nodeTagsList);
```

## <a name="setting-required-tags-for-services"></a>Hizmetler için gerekli Etiketler ayarlanıyor

### <a name="using-powershell"></a>PowerShell’i kullanma

Yeni hizmet oluşturuluyor:

```posh
New-ServiceFabricService -ApplicationName fabric:/HelloWorld -ServiceName fabric:/HelloWorld/svc1 -ServiceTypeName HelloWorldStateful -Stateful -PartitionSchemeSingleton -TargetReplicaSetSize 5 -MinReplicaSetSize 3 -TagsRequiredToRun @("SampleTag1") - TagsRequiredToPlace @("SampleTag2")
```
Bu komut, hizmetin buraya yerleştirilmesi için bir düğümde "SampleTag2" olmasını gerektiren bir hizmet oluşturur ve hizmetin bu düğümde çalışmaya devam edebilmesi için "SampleTag1" mevcut olacak.

Mevcut hizmet güncelleştiriliyor:

```posh
Update-ServiceFabricService -Stateful -ServiceName fabric:/myapp/test -TagsRequiredToRun @("SampleTag1") -TagsRequiredToPlace @("SampleTag2")
```
Bu komut, hizmetin buraya yerleştirilmesi için bir düğümde "SampleTag2" olmasını gerektiren bir hizmeti güncelleştirir ve hizmetin söz konusu düğümde çalışmaya devam edebilmesi için "SampleTag1" mevcut olacaktır.

### <a name="using-c-apis"></a>C# API 'Lerini kullanma

Yeni hizmet oluşturuluyor:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceDescription.ServiceTags = serviceTags;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Mevcut hizmet güncelleştiriliyor:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceUpdate.ServiceTags = serviceTags;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```

Tüm bu komutlar, durum bilgisi olmayan hizmetlere eşit olarak uygulanır.

## <a name="next-steps"></a>Sonraki adımlar
[Yerleştirme kısıtlamaları](service-fabric-cluster-resource-manager-configure-services.md) hakkında daha fazla bilgi
