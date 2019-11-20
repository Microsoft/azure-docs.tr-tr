---
title: Azure Service Fabric kümesine düğüm türü ekleme | Microsoft Docs
description: Bir sanal makine ölçek kümesi ekleyerek Service Fabric kümesi ölçeklendirmeyi öğrenin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: atsenthi
ms.openlocfilehash: 1414e656a358af1e258c823cc7ec747fefa986ba
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598698"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Bir sanal makine ölçek kümesi ekleyerek Service Fabric kümeyi ölçeklendirin
Bu makalede, var olan bir kümeye yeni bir düğüm türü ekleyerek bir Azure Service Fabric kümesinin ölçeklendirilmesi açıklanır. Service Fabric küme, mikro hizmetlerinizin dağıtıldığı ve yönetildiği, ağa bağlı bir sanal veya fiziksel makine kümesidir. Bir kümenin parçası olan makine veya VM, düğüm olarak adlandırılır. Sanal Makine Ölçek Kümeleri, bir sanal makine koleksiyonunu bir küme olarak dağıtmak ve yönetmek için kullandığınız bir Azure işlem kaynağıdır. Bir Azure kümesinde tanımlanan her düğüm türü [ayrı bir ölçek kümesi olarak ayarlanır](service-fabric-cluster-nodetypes.md). Her düğüm türü ayrıca yönetilebilir. Service Fabric kümesi oluşturduktan sonra, var olan bir kümeye yeni bir düğüm türü (sanal makine ölçek kümesi) ekleyerek bir kümeyi yatay olarak ölçeklendirebilirsiniz.  Küme üzerinde iş yükleri çalışırken bile kümeyi istediğiniz zaman ölçeklendirebilirsiniz.  Küme ölçeklenirken uygulamalarınız da otomatik olarak ölçeklendirilir.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Var olan bir kümeye ek ölçek kümesi ekleme
Mevcut bir kümeye yeni bir düğüm türü (bir sanal makine ölçek kümesi tarafından desteklenir) eklemek, [birincil düğüm türünün yükseltilmesine](service-fabric-scale-up-node-type.md)benzerdir; ancak aynı nodetyperef kullanamazsınız. , etkin olarak kullanılan sanal makine ölçek kümelerini devre dışı bırakmayacaktır ve birincil düğüm türünü güncelleştirmemeniz durumunda küme kullanılabilirliğini kaybetmezsiniz. 

NodeTypeRef özelliği, sanal makine ölçek kümesi Service Fabric uzantı özellikleri içinde bildiriliyor:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Ayrıca, bu yeni düğüm türünü Service Fabric kümesi kaynağına eklemeniz gerekir:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Sonraki adımlar
* [Birincil düğüm türünü](service-fabric-scale-up-node-type.md) nasıl ölçeklendireceğinizi öğrenin
* [Uygulama ölçeklenebilirliği](service-fabric-concepts-scalability.md)hakkında bilgi edinin.
* [Bir Azure kümesini içinde veya dışarı ölçeklendirin](service-fabric-tutorial-scale-cluster.md).
* Akıcı Azure işlem SDK 'sını kullanarak [bir Azure kümesini programlı bir şekilde ölçeklendirin](service-fabric-cluster-programmatic-scaling.md) .
* [Tek başına kümeyi içinde veya dışarı ölçeklendirin](service-fabric-cluster-windows-server-add-remove-nodes.md).

