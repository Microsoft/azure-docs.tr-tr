---
title: Azure Service Fabric için kapasite planlama ve ölçeklendirme
description: Service Fabric kümelerini ve uygulamaları planlamak ve ölçeklendirmek için en iyi uygulamalar.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.custom: devx-track-csharp
ms.openlocfilehash: 32a9c26bb9e89cf4057cc753b02ad3c006d0bae6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595077"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Azure Service Fabric için kapasite planlama ve ölçeklendirme

Herhangi bir Azure Service Fabric kümesi oluşturmadan veya kümenizi barındıran işlem kaynaklarını ölçeklendirmeye başlamadan önce kapasiteyi planlamanız önemlidir. Kapasiteyi planlama hakkında daha fazla bilgi için bkz. [Service Fabric kümesi kapasitesini planlama](./service-fabric-cluster-capacity.md). Küme ölçeklenebilirliği hakkında daha iyi uygulama kılavuzu için bkz. [Service Fabric ölçeklenebilirlik konuları](/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Düğüm türü ve küme özelliklerini düşünmenin yanı sıra, bir üretim ortamı için bir saatten daha uzun sürer. Bu durum, eklediğiniz VM sayısının ne olursa olsun doğru bir konudur.

## <a name="autoscaling"></a>Otomatik ölçeklendirme
[Kaynak yapılandırmalarının kod olarak](./service-fabric-best-practices-infrastructure-as-code.md)değerlendirmek için en iyi yöntem olduğundan, Azure Resource Manager şablonları aracılığıyla ölçeklendirme işlemleri gerçekleştirmeniz gerekir. 

Sanal Makine Ölçek Kümeleri aracılığıyla otomatik ölçeklendirmeyi kullanmak, sürümlü Kaynak Yöneticisi şablonunun sanal makine ölçek kümeleri için örnek sayılarınızı doğru şekilde tanımlamasını sağlayacak. Yanlış tanım, gelecekteki dağıtımların istenmeden ölçeklendirme işlemlerine neden olacağı riski artırır. Genel olarak, şu durumlarda otomatik ölçeklendirmeyi kullanmanız gerekir:

* Kaynak Yöneticisi şablonlarınızı, belirtilen uygun kapasiteye dağıtmak, kullanım durumunu desteklememektedir.
     
   El ile Ölçeklendirmeye ek olarak, [Azure Kaynak grubu dağıtım projelerini kullanarak Azure DevOps Services bir sürekli tümleştirme ve teslim işlem hattı](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)yapılandırabilirsiniz. Bu işlem hattı genellikle [Azure izleyici REST API](../azure-monitor/essentials/rest-api-walkthrough.md)sorgulanan sanal makine performans ölçümlerini kullanan bir mantıksal uygulama tarafından tetiklenir. İşlem hattı, istediğiniz ölçümlere göre etkili bir şekilde otomatik olarak ölçeklendiriyor, Kaynak Yöneticisi şablonları için iyileştiriliyor.
* Aynı anda yalnızca bir sanal makine ölçek kümesi düğümünü yatay olarak ölçeklendirmeniz gerekir.
   
   Aynı anda üç veya daha fazla düğüm ölçeğini genişletmek için, bir [sanal makine ölçek kümesi ekleyerek bir Service Fabric kümesini ölçeklendirmelisiniz](virtual-machine-scale-set-scale-node-type-scale-out.md). Sanal makine ölçek kümelerinin tek seferde bir düğüm olarak ölçeklendirilmesi ve ölçeğini genişletmek en güvenli hale gelir.
* Service Fabric kümeniz için gümüş güvenilirlik veya daha yükseği, otomatik ölçeklendirme kurallarını yapılandırdığınız herhangi bir ölçekte daha fazla dayanıklılık veya daha yükseği vardır.
  
   Otomatik ölçeklendirme kuralları için en düşük kapasite, beş sanal makine örneğine eşit veya ondan büyük olmalıdır. Ayrıca, birincil düğüm türü için en düşük güvenilirlik katmanınıza eşit veya ondan büyük olmalıdır.

> [!NOTE]
> Service Fabric durum bilgisi olan Service Fabric:/System/ınfastructureservice/<NODE_TYPE_NAME>, gümüş veya daha yüksek dayanıklılık düzeyine sahip her düğüm türü üzerinde çalışır. Bu, küme düğüm türlerinizin hiçbirinde Azure 'da çalışmak için desteklenen tek sistem hizmetidir.

> [!IMPORTANT]
> Service Fabric otomatik ölçeklendirme `Default` , `NewestVM` sanal makine ölçek kümesi [Ölçek](../virtual-machine-scale-sets/virtual-machine-scale-sets-scale-in-policy.md)kümesini destekler.

## <a name="vertical-scaling-considerations"></a>Dikey ölçeklendirme konuları

Azure Service Fabric bir düğüm türünün [Dikey ölçeklendirilmesi](./virtual-machine-scale-set-scale-node-type-scale-out.md) için birkaç adım ve dikkat edilmesi gerekir. Örnek:

* Ölçeklendirmeden önce kümenin sağlıklı olması gerekir. Aksi takdirde, kümeyi daha fazla sabitleyebileceksiniz.
* Durum bilgisi olmayan hizmetleri barındıran tüm Service Fabric küme düğümü türleri için gümüş dayanıklılık düzeyi veya daha yüksek bir değer gereklidir.

> [!NOTE]
> Durum bilgisi olmayan Service Fabric sistem hizmetlerini barındıran birincil düğüm türü gümüş dayanıklılık düzeyi veya daha büyük olmalıdır. Gümüş dayanıklılığı etkinleştirdikten sonra, sistem işlemleri hızlarında veri güvenliğini iyileştirdiği için yükseltme, düğüm ekleme veya kaldırma gibi küme işlemleri daha yavaş olur.

Bir sanal makine ölçek kümesini yalnızca kaynak SKU 'SU değiştirilerek bir bozucu işlem olduğundan, bir sanal makine ölçek kümesinin dikey ölçeklendirilmesi bir bozucu işlemdir. bu nedenle, konaklarınızı yeniden görüntüleyerek yerel olarak kalıcı Bunun yerine, istediğiniz SKU 'ya sahip yeni bir ölçek kümesi ekleyerek ve ardından güvenli bir dikey ölçeklendirme işlemini gerçekleştirmek için hizmetlerinizi yeni ölçek kümesine geçirerek kümenizi yatay olarak ölçeklendirirsiniz.

Kümeniz, uygulamanızın hizmetlerini barındırmaya karar vermek için Service Fabric [node özelliklerini ve yerleştirme kısıtlamalarını](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) kullanır. Birincil düğüm türünü dikey olarak ölçeklendirirken, ikinci bir birincil düğüm türü dağıtırsınız ve sonra `"isPrimary": false` özgün birincil düğüm türünde () öğesini ve ardından düğümlerini devre dışı bırakmayı ve ölçek kümesini ve ilgili kaynaklarını kaldırmayı isteyeceksiniz. Ayrıntılar için bkz. [Service Fabric kümesi ölçeğini ölçekleme birincil düğüm türü](service-fabric-scale-up-primary-node-type.md).

> [!NOTE]
> Üretim ortamında değişikliklere kalkışmadan önce test ortamlarındaki işlemleri her zaman doğrulayın. Service Fabric küme sistemi hizmetleri, varsayılan olarak yalnızca hedef birincil düğüm türüne bir yerleştirme kısıtlamasına sahiptir.

Düğüm özellikleri ve yerleştirme kısıtlamaları ile birlikte, aşağıdaki adımları tek seferde bir VM örneği yapın. Bu, başka bir yerde oluşturulan yeni çoğaltmalar olduğundan, sistem hizmetlerinin (ve durum bilgisi olan hizmetlerin) kaldırdığınız sanal makine örneğinde düzgün şekilde kapatılmasını sağlar.

1. PowerShell 'den, `Disable-ServiceFabricNode` `RemoveNode` kaldırılacak düğümü devre dışı bırakmak için amacını çalıştırın. En yüksek sayı olan düğüm türünü kaldırın. Örneğin, altı düğümlü bir kümeniz varsa, "MyNodeType_5" sanal makine örneğini kaldırın.
2. `Get-ServiceFabricNode`Düğümün devre dışı öğesine geçirildiğinden emin olmak için ' i çalıştırın. Aksi takdirde, düğüm devre dışı olana kadar bekleyin. Bu işlem her düğüm için birkaç saat sürebilir. Düğüm devre dışı olana kadar devam etmeyin.
3. VM sayısını bu düğüm türünde bir değer ile azaltın. En yüksek VM örneği artık kaldırılacak.
4. Gerektiğinde 1 ile 3 arasındaki adımları yineleyin, ancak birincil düğüm türlerindeki örnek sayısında güvenilirlik katmanının ne kadar düşük olduğuna ilişkin hiçbir şekilde ölçeklendirin. Önerilen örneklerin listesi için [Service Fabric kümesi kapasitesini planlama](./service-fabric-cluster-capacity.md) konusuna bakın.
5. Tüm VM 'Ler kaybolduktan sonra ("aşağı" olarak temsil edilir) doku:/System/InfrastructureService/[Node Name] bir hata durumu gösterecektir. Daha sonra, düğüm türünü kaldırmak için küme kaynağını güncelleştirebilirsiniz. ARM şablon dağıtımını kullanabilir ya da [Azure Resource Manager](https://resources.azure.com)aracılığıyla küme kaynağını düzenleyebilirsiniz. Bu, hata durumunda olan Fabric:/System/InfrastructureService/[Node Type] hizmetini kaldıracak bir küme yükseltmesi başlatır.
 6. VMScaleSet 'yi isteğe bağlı olarak silerseniz, yine de düğümleri Service Fabric Explorer görünümünde "aşağı" olarak görürsünüz. Son adım, komutu komutuyla temizleyelim `Remove-ServiceFabricNodeState` .

## <a name="horizontal-scaling"></a>Yatay ölçeklendirme

Yatay ölçeklendirmeyi [el ile](./service-fabric-cluster-scale-in-out.md) veya [programlama yoluyla](./service-fabric-cluster-programmatic-scaling.md)yapabilirsiniz.

> [!NOTE]
> Gümüş veya altın dayanıklılık içeren bir düğüm türünü ölçeklendirirken ölçeklendirme yavaş olur.

### <a name="scaling-out"></a>Ölçeği genişletme

Belirli bir sanal makine ölçek kümesinin örnek sayısını artırarak bir Service Fabric kümesini ölçeklendirin. `AzureClient`Kapasiteyi artırmak için, kullanarak ve istenen ölçek KÜMESININ kimliğini kullanarak programlama yoluyla ölçeklendirebilirsiniz.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

El ile ölçeklendirmek için, istenen [sanal makine ölçek kümesi](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) kaynağının SKU özelliğindeki kapasiteyi güncelleştirin.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Ölçeklendirme

' De ölçekleme, ölçeklendirmeden daha fazla değerlendirme gerektirir. Örneğin:

* Service Fabric sistem hizmetleri kümenizdeki birincil düğüm türünde çalışır. Güvenilirlik katmanının ne kadar örnek olduğuna göre daha az örneğe sahip olmanız için, bu düğüm türü için örnek sayısında hiçbir şekilde kapanmayın veya ölçeklendirin. 
* Durum bilgisi olan bir hizmet için, kullanılabilirliği sürdürmek ve hizmetinizin durumunu korumak için her zaman bir kaç düğüm olması gerekir. En azından, bölüm veya hizmetin hedef çoğaltma kümesi sayısına eşit sayıda düğüm gerekir.

El ile ölçeklendirmek için aşağıdaki adımları izleyin:

1. PowerShell 'den, `Disable-ServiceFabricNode` `RemoveNode` kaldırılacak düğümü devre dışı bırakmak için amacını çalıştırın. En yüksek sayı olan düğüm türünü kaldırın. Örneğin, altı düğümlü bir kümeniz varsa, "MyNodeType_5" sanal makine örneğini kaldırın.
2. `Get-ServiceFabricNode`Düğümün devre dışı öğesine geçirildiğinden emin olmak için ' i çalıştırın. Aksi takdirde, düğüm devre dışı olana kadar bekleyin. Bu işlem her düğüm için birkaç saat sürebilir. Düğüm devre dışı olana kadar devam etmeyin.
3. VM sayısını bu düğüm türünde bir değer ile azaltın. En yüksek VM örneği artık kaldırılacak.
4. İstediğiniz kapasiteyi sağlamadan önce gerekirse 1 ile 3 arasındaki adımları yineleyin. Birincil düğüm türlerindeki örneklerin sayısını güvenilirlik katmanının ne kadar düşük olduğuna göre ölçeklendirmeyin. Önerilen örneklerin listesi için [Service Fabric kümesi kapasitesini planlama](./service-fabric-cluster-capacity.md) konusuna bakın.

El ile ölçeklendirmek için, istenen [sanal makine ölçek kümesi](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) kaynağının SKU özelliğindeki kapasiteyi güncelleştirin.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Programlı olarak ölçeklendirmek için düğümü kapanmaya hazırlamanız gerekir. Kaldırılacak düğümü (en yüksek örnekli düğüm) bulun. Örnek:

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

`FabricClient` `client` Önceki kodda kullandığınız aynı örneği (Bu durumda) ve düğüm örneğini (Bu örnekte) kullanarak düğümü devre dışı bırakın ve kaldırın `instanceIdString` :

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Bir kümede ölçeklendirirseniz, kaldırılan düğümü/VM örneğini Service Fabric Explorer uygun olmayan bir durumda görürsünüz. Bu davranışın açıklaması için, [Service Fabric Explorer gözlemleyebileceğiniz davranışlar](./service-fabric-cluster-scale-in-out.md#behaviors-you-may-observe-in-service-fabric-explorer)bölümüne bakın. Seçenekleriniz şunlardır:
> * [Remove-ServiceFabricNodeState komutunu](/powershell/module/servicefabric/remove-servicefabricnodestate) uygun düğüm adıyla çağırın.
> * [Service Fabric otomatik ölçeklendirme yardımcısı uygulamasını](https://github.com/Azure/service-fabric-autoscale-helper/) kümenize dağıtın. Bu uygulama, ölçeklenmiş düğümlerin Service Fabric Explorer temizlenmesini sağlar.

## <a name="reliability-levels"></a>Güvenilirlik düzeyleri

[Güvenilirlik düzeyi](./service-fabric-cluster-capacity.md) Service Fabric kümesi kaynağınızın bir özelliğidir. Tek tek düğüm türleri için farklı şekilde yapılandırılamaz. Küme için sistem hizmetlerinin çoğaltma faktörünü denetler ve küme kaynak düzeyinde bir ayardır. 

Güvenilirlik düzeyi, birincil düğüm türünde olması gereken en az düğüm sayısını saptacaktır. Güvenilirlik katmanı aşağıdaki değerleri alabilir:

* Platin: bir hedef çoğaltma kümesi yedi ve dokuz çekirdek düğümü olan sistem hizmetlerini çalıştırır.
* Altın: bir hedef çoğaltma kümesi yedi ve yedi çekirdek düğümü olan sistem hizmetlerini çalıştırır.
* Gümüş: bir hedef çoğaltma kümesi sayısı beş ve beş çekirdek düğümü olan sistem hizmetlerini çalıştırır.
* Bronz: bir hedef çoğaltma kümesi, üç ve üç çekirdek düğüm sayısıyla sistem hizmetlerini çalıştırır.

Önerilen en düşük güvenilirlik düzeyi gümüş.

Güvenilirlik düzeyi, [Microsoft. ServiceFabric/kümeler kaynağının](/azure/templates/microsoft.servicefabric/2018-02-01/clusters)Özellikler bölümünde ayarlanır; örneğin:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Dayanıklılık düzeyleri

> [!WARNING]
> Bronz dayanıklılık ile çalışan düğüm türleri _hiçbir ayrıcalık_ alamadı. Durum bilgisiz iş yüklerinizi etkileyen altyapı işleri durdurulmaz veya gecikmez ve bu da iş yüklerinizi etkileyebilir. 
>
> Yalnızca durum bilgisi olmayan iş yüklerini çalıştıran düğüm türleri için bronz dayanıklılık kullanın. Üretim iş yükleri için, durum tutarlılığı sağlamak üzere gümüş veya daha yüksek bir sürümü çalıştırın. [Kapasite planlama belgelerindeki](./service-fabric-cluster-capacity.md)kılavuza göre doğru güvenilirliği seçin.

Dayanıklılık düzeyi iki kaynak olarak ayarlanmalıdır. Bunlardan biri, [sanal makine ölçek kümesi kaynağının](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)uzantı profilidir:

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

Diğer kaynak, `nodeTypes` [Microsoft. servicefabric/kümeler kaynağında](/azure/templates/microsoft.servicefabric/2018-02-01/clusters)bulunur: 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Sonraki adımlar

* Windows Server çalıştıran VM 'lerde veya bilgisayarlarda küme oluşturma: [Windows Server için Service Fabric kümesi oluşturma](service-fabric-cluster-creation-for-windows-server.md).
* VM 'lerde veya Linux çalıştıran bilgisayarlarda küme oluşturma: [Linux kümesi oluşturma](service-fabric-cluster-creation-via-portal.md).
* [Service Fabric destek seçenekleri](service-fabric-support.md)hakkında bilgi edinin.

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
