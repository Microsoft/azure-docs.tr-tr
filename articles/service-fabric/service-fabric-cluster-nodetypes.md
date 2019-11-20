---
title: Azure Service Fabric düğüm türleri ve sanal makine ölçek kümeleri | Microsoft Docs
description: Azure Service Fabric düğüm türlerinin sanal makine ölçek kümeleriyle ilişkilerini ve bir ölçek kümesi örneğine veya küme düğümüne uzaktan nasıl bağlanacağınızı öğrenin.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: pepogors
ms.openlocfilehash: cec134f9e71f86cd0ed17912f1a3c76adc9a4164
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167316"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric düğüm türleri ve sanal makine ölçek kümeleri
[Sanal Makine Ölçek Kümeleri](/azure/virtual-machine-scale-sets) bir Azure işlem kaynağıdır. Ölçek kümelerini, bir küme olarak bir sanal makine koleksiyonunu dağıtmak ve yönetmek için kullanabilirsiniz. Azure Service Fabric kümesinde tanımladığınız her düğüm türü ayrı bir ölçek ayarlar.  Microsoft. Azure. ServiceFabric sanal makine uzantısı tarafından belirlenen ölçek kümesindeki her bir sanal makineye yüklü Service Fabric çalışma zamanı. Her bir düğüm türünü bağımsız olarak yukarı veya aşağı ölçeklendirebilirsiniz, her küme düğümünde çalışan işletim sistemi SKU 'sunu değiştirebilir, farklı bağlantı noktası kümelerine açık olabilir ve farklı kapasite ölçümleri kullanabilirsiniz.

Aşağıdaki şekilde, ön uç ve arka uç adında iki düğüm türüne sahip bir küme gösterilmektedir. Her düğüm türünün beş düğümü vardır.

![İki düğüm türü olan bir küme][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Sanal makine ölçek kümesi örneklerini düğümlere eşle
Yukarıdaki şekilde gösterildiği gibi, ölçek kümesi örnekleri 0 ' dan başlar ve sonra 1 ' i arttırır. Numaralandırma, düğüm adlarında yansıtılır. Örneğin, Node BackEnd_0, arka uç ölçek kümesinin 0 örneğidir. Bu belirli ölçek kümesinde BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 ve BackEnd_4 adlı beş örnek vardır.

Ölçek kümesi ölçeğini ölçeklendirirseniz, yeni bir örnek oluşturulur. Yeni ölçek kümesi örnek adı genellikle ölçek kümesi adı ve sonraki örnek sayısıdır. Örneğimizde, BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Harita ölçek kümesi yük dengeleyiciler için düğüm türleri ve ölçek kümeleri
Kümenizi Azure portal dağıttıysanız veya örnek Azure Resource Manager şablonunu kullandıysanız, bir kaynak grubundaki tüm kaynaklar listelenir. Her ölçek kümesi veya düğüm türü için yük dengeleyicileri görebilirsiniz. Yük dengeleyici adı şu biçimi kullanır: **lb-&lt;node tür adı @ no__t-2**. Aşağıdaki şekilde gösterildiği gibi LB-sfcluster4doc-0 bir örnektir:

![Kaynaklar][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric sanal makine uzantısı
Service Fabric sanal makine uzantısı, Azure sanal makinelerine Service Fabric önyüklemek ve düğüm güvenliğini yapılandırmak için kullanılır.

Aşağıda Service Fabric sanal makine uzantısının bir parçacığı verilmiştir:

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "dataPath": "D:\\\\SvcFab",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

Özellik açıklamaları aşağıda verilmiştir:

| **Adı** | **İzin verilen değerler** | ** --- ** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
| ad | string | --- | uzantı için benzersiz ad |
| type | "ServiceFabricLinuxNode" veya "ServiceFabricWindowsNode" | --- | Önyükleme Service Fabric işletim sistemini tanımlar |
| autoUpgradeMinorVersion | true veya false | --- | SF çalışma zamanı alt sürümlerinin otomatik yükseltmesini etkinleştir |
| 'ın | Microsoft. Azure. ServiceFabric | --- | Service Fabric uzantısı yayımcısının adı |
| clusterEndpont | string | --- | URI: yönetim uç noktası bağlantı noktası |
| nodeTypeRef | string | --- | nodeType adı |
| Dur, Itylevel | Bronz, gümüş, altın, Platinum | --- | Sabit Azure altyapısını duraklatmaya izin verilen süre |
| enableParallelJobs | true veya false | --- | Aynı ölçek kümesindeki sanal makineyi kaldır ve sanal makineyi yeniden Başlat gibi hesaplama ParallelJobs 'ı etkinleştir |
| nicPrefixOverride | string | --- | "10.0.0.0/24" gibi alt ağ öneki |
| Ortak adlar | String [] | --- | Yüklü küme sertifikalarının ortak adları |
| x509StoreName | string | --- | Yüklü küme sertifikasının bulunduğu deponun adı |
| typeHandlerVersion | 1.1 | --- | Uzantının sürümü. 1,0 için klasik uzantı sürümünün 1,1 sürümüne yükseltilmesi önerilir |
| Veri yolu | string | --- | Service Fabric sistem hizmetleri ve uygulama verileri için durumu kaydetmek için kullanılan sürücünün yolu. 

## <a name="next-steps"></a>Sonraki adımlar
* ["Her yerde dağıtma" özelliğine genel bakış ve Azure tarafından yönetilen kümelerle karşılaştırma](service-fabric-deploy-anywhere.md)konusuna bakın.
* [Küme güvenliği](service-fabric-cluster-security.md)hakkında bilgi edinin.
* Belirli bir ölçek kümesi örneğine [uzak bağlantı](service-fabric-cluster-remote-connect-to-azure-cluster-node.md)
* Dağıtımdan sonra küme VM 'lerinde [RDP bağlantı noktası aralığı değerlerini Güncelleştir](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* Küme VM 'Leri için [Yönetici Kullanıcı adını ve parolasını değiştirme](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
