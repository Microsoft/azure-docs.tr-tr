---
title: Düğüm türleri ve sanal makine ölçek kümeleri
description: Azure Service Fabric düğüm türlerinin sanal makine ölçek kümeleriyle ilişkilerini ve bir ölçek kümesi örneğine veya küme düğümüne uzaktan nasıl bağlanacağınızı öğrenin.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.openlocfilehash: 9e30c02de54806006a1881448bcb9f788a57310c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97095262"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric düğüm türleri ve sanal makine ölçek kümeleri

[Sanal Makine Ölçek Kümeleri](../virtual-machine-scale-sets/index.yml) bir Azure işlem kaynağıdır. Ölçek kümelerini, bir küme olarak bir sanal makine koleksiyonunu dağıtmak ve yönetmek için kullanabilirsiniz. Bir Azure Service Fabric kümesinde tanımladığınız her düğüm türü, tam olarak bir ölçek kümesi ayarlar: birden çok düğüm türü aynı ölçek kümesi tarafından yedeklenmez ve bir düğüm türü birden çok ölçek kümesi tarafından yedeklenmemelidir.

Service Fabric çalışma zamanı, ölçek kümesindeki her bir sanal makineye *Microsoft. Azure. ServiceFabric* sanal makine uzantısı tarafından yüklenir. Her bir düğüm türünü bağımsız olarak yukarı veya aşağı ölçeklendirebilirsiniz, her küme düğümünde çalışan işletim sistemi SKU 'sunu değiştirebilir, farklı bağlantı noktası kümelerine açık olabilir ve farklı kapasite ölçümleri kullanabilirsiniz.

Aşağıdaki şekilde, *ön uç* ve *arka uç* adında iki düğüm türüne sahip bir küme gösterilmektedir. Her düğüm türünün beş düğümü vardır.

![İki düğüm türü olan bir küme][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Sanal makine ölçek kümesi örneklerini düğümlere eşle

Yukarıdaki şekilde gösterildiği gibi, ölçek kümesi örnekleri 0 ' dan başlar ve sonra 1 ' i arttırır. Numaralandırma, düğüm adlarında yansıtılır. Örneğin, düğüm BackEnd_0 arka uç ölçek kümesinin 0 örneğidir. Bu ölçek kümesinde BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 ve BackEnd_4 adlı beş örnek vardır.

Ölçek kümesi ölçeğini ölçeklendirirseniz, yeni bir örnek oluşturulur. Yeni ölçek kümesi örnek adı genellikle ölçek kümesi adı ve sonraki örnek sayısıdır. Örneğimizde, BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Harita ölçek kümesi yük dengeleyiciler için düğüm türleri ve ölçek kümeleri

Kümenizi Azure portal dağıttıysanız veya örnek Azure Resource Manager şablonunu kullandıysanız, bir kaynak grubundaki tüm kaynaklar listelenir. Her ölçek kümesi veya düğüm türü için yük dengeleyicileri görebilirsiniz. Yük dengeleyici adı şu biçimi kullanır: **lb- &lt; node tür adı &gt;**. Aşağıdaki şekilde gösterildiği gibi LB-sfcluster4doc-0 bir örnektir:

![Ekran görüntüsü, iki yük dengeleyicileri vurgulanmış bir kaynak grubu gösterir.][Resources]

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

| **Ad** | **İzin Verilen Değerler** | **Kılavuz veya kısa açıklama** |
| --- | --- | --- | --- |
| name | string | Uzantı için benzersiz ad |
| tür | "ServiceFabricLinuxNode" veya "ServiceFabricWindowsNode" | Önyükleme Service Fabric işletim sistemini tanımlar |
| autoUpgradeMinorVersion | true veya false | SF çalışma zamanı alt sürümlerinin otomatik yükseltmesini etkinleştir |
| yayımcı | Microsoft. Azure. ServiceFabric | Service Fabric uzantısı yayımcısının adı |
| clusterEndpont | string | URI: yönetim uç noktası bağlantı noktası |
| nodeTypeRef | string | NodeType adı |
| Dur, Itylevel | Bronz, gümüş, altın, Platinum | Sabit Azure altyapısını duraklatmaya izin verilen süre |
| enableParallelJobs | true veya false | Aynı ölçek kümesindeki sanal makineyi kaldır ve sanal makineyi yeniden Başlat gibi hesaplama ParallelJobs 'ı etkinleştir |
| nicPrefixOverride | string | "10.0.0.0/24" gibi alt ağ öneki |
| Ortak adlar | String [] | Yüklü küme sertifikalarının ortak adları |
| x509StoreName | string | Yüklü küme sertifikasının bulunduğu deponun adı |
| typeHandlerVersion | 1.1 | Uzantının sürümü. 1,0 için klasik uzantı sürümünün 1,1 sürümüne yükseltilmesi önerilir |
| Veri yolu | string | Service Fabric sistem hizmetleri ve uygulama verileri için durumu kaydetmek için kullanılan sürücünün yolu.

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
