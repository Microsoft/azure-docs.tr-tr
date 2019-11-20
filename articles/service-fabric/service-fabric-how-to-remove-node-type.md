---
title: Azure Service Fabric düğüm türünü kaldırma | Microsoft Docs
description: Azure 'da çalışan bir Service Fabric kümesinden düğüm türünü kaldırmayı öğrenin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: atsenthi
ms.openlocfilehash: 44f25adf4168f4339a31e9270c2b23a8466a8889
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599499"
---
# <a name="remove-a-service-fabric-node-type"></a>Service Fabric düğüm türünü kaldır
Bu makalede, var olan bir düğüm türünü kümeden kaldırarak bir Azure Service Fabric kümesinin nasıl ölçeklendiriyapılacağı açıklanır. Service Fabric küme, mikro hizmetlerinizin dağıtıldığı ve yönetildiği, ağa bağlı bir sanal veya fiziksel makine kümesidir. Bir kümenin parçası olan makine veya VM, düğüm olarak adlandırılır. Sanal Makine Ölçek Kümeleri, bir sanal makine koleksiyonunu bir küme olarak dağıtmak ve yönetmek için kullandığınız bir Azure işlem kaynağıdır. Bir Azure kümesinde tanımlanan her düğüm türü [ayrı bir ölçek kümesi olarak ayarlanır](service-fabric-cluster-nodetypes.md). Her düğüm türü ayrıca yönetilebilir. Bir Service Fabric kümesi oluşturduktan sonra, düğüm türünü (sanal makine ölçek kümesi) ve tüm düğümlerini kaldırarak bir kümeyi yatay olarak ölçeklendirebilirsiniz.  Küme üzerinde iş yükleri çalışırken bile kümeyi istediğiniz zaman ölçeklendirebilirsiniz.  Küme ölçeklenirken uygulamalarınız da otomatik olarak ölçeklendirilir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Service Fabric düğüm türünü kaldırmak için [Remove-AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) komutunu kullanın.

Remove-AzServiceFabricNodeType çağrıldığında gerçekleşen üç işlem şunlardır:
1.  Düğüm türünün arkasındaki sanal makine ölçek kümesi silinir.
2.  Düğüm türü kümeden kaldırılır.
3.  Bu düğüm türü içindeki her düğüm için, bu düğümün tüm durumu sistemden kaldırılır. Bu düğümde hizmetler varsa, hizmetler önce başka bir düğüme taşınır. Küme Yöneticisi çoğaltma/hizmet için bir düğüm bulamazsa, işlem gecikmeli/engellenir.

> [!WARNING]
> Bir üretim kümesinden düğüm türünü kaldırmak için Remove-AzServiceFabricNodeType kullanılması, sıklıkla kullanılması önerilmez. Düğüm türünün arkasındaki sanal makine ölçek kümesi kaynağını sildiği için bu, tehlikeli bir komuttur. 

## <a name="durability-characteristics"></a>Dayanıklılık özellikleri
Remove-AzServiceFabricNodeType kullanılırken güvenlik, hızlara göre önceliklendirilir. Düğüm türü gümüş veya altın [dayanıklılık düzeyi](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)olmalıdır, nedeni:
- Bronz durum bilgilerini kaydetme hakkında garanti vermez.
- Gümüş ve altın dayanıklılık, ölçek kümesinde yapılan değişiklikleri yakalar.
- Altın Ayrıca ölçek kümesi altında Azure güncelleştirmeleri üzerinde denetim elde etmenizi sağlar.

Verilerin kaybolmaması için temel değişiklikleri ve güncelleştirmeleri "düzenler" Service Fabric. Ancak, bronz dayanıklılığı olan bir düğümü kaldırdığınızda durum bilgileri kaybolabilir. Birincil düğüm türünü kaldırıyorsanız ve uygulamanız durum bilgisdeyse, bronz kabul edilebilir. Üretim için durum bilgisi olan iş yüklerini çalıştırdığınızda, en düşük yapılandırma gümüş olmalıdır. Benzer şekilde, üretim senaryolarında birincil düğüm türü her zaman gümüş veya altın olmalıdır.

### <a name="more-about-bronze-durability"></a>Bronz dayanıklılık hakkında daha fazla bilgi

Bronz olan bir düğüm türü kaldırılırken, düğüm türündeki tüm düğümler hemen aşağı gider. Service Fabric, hiçbir bronz düğüm ölçek kümesi güncelleştirmesini yakalamaz, bu nedenle tüm VM 'Ler hemen aşağı gider. Bu düğümlerde durum bilgisi olan herhangi bir şey varsa, veriler kaybolur. Artık durum bilgisiz olsa bile Service Fabric tüm düğümler halkaya katılır, bu nedenle tüm bir komşu kaybolabilir ve bu da kümenin kendisinin kararlılığını bozabilir.

## <a name="recommended-node-type-removal-process"></a>Önerilen düğüm türü kaldırma işlemi

Düğüm türünü kaldırmak için [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) cmdlet 'ini çalıştırın.  Cmdlet 'in tamamlanmak üzere bir süre sürer.  Tüm VM 'Ler kaybolduktan sonra ("aşağı" olarak gösterilir) doku:/System/InfrastructureService/[NodeType Name] bir hata durumu gösterecektir.

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

Daha sonra, düğüm türünü kaldırmak için küme kaynağını güncelleştirebilirsiniz. ARM şablon dağıtımını kullanabilir ya da [Azure Resource Manager](https://resources.azure.com)aracılığıyla küme kaynağını düzenleyebilirsiniz. Bu, hata durumunda olan Fabric:/System/InfrastructureService/[NodeType Name] hizmetini kaldıracak bir küme yükseltmesi başlatır.

Hala Service Fabric Explorer düğümlerin "aşağı" olduğunu görürsünüz. Kaldırılmasını istediğiniz düğümlerin her birinde [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) komutunu çalıştırın.


```powershell
$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Sonraki adımlar
- Küme [dayanıklılığı özellikleri](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)hakkında daha fazla bilgi edinin.
- [Düğüm türleri ve sanal makine ölçek kümeleri](service-fabric-cluster-nodetypes.md)hakkında daha fazla bilgi edinin.
- [Service Fabric kümesi ölçeklendirme](service-fabric-cluster-scaling.md)hakkında daha fazla bilgi edinin.
