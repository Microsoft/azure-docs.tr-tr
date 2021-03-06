---
title: Service Fabric Azure Resource Manager dağıtım guardrayları
description: Bu makalede, Azure Resource Manager aracılığıyla Service Fabric kümesi dağıtımında yapılan yaygın hatalara ve bunların nasıl önleneceğini gösteren bir genel bakış sunulmaktadır.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: aa1499c57ead28bfcee90a2f224ef9c3bb1d7f58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86247833"
---
# <a name="service-fabric-guardrails"></a>Service Fabric guardrayları 
Bir Service Fabric kümesi dağıtımında, guardrayları yerinde konur, bu da geçersiz bir küme yapılandırması durumunda Azure Resource Manager dağıtımı başarısız olur. Aşağıdaki bölümler, yaygın küme yapılandırma sorunlarına ve bu sorunları azaltmak için gereken adımlara genel bir bakış sağlar. 

## <a name="durability-mismatch"></a>Dayanıklılık uyumsuzluğu
### <a name="overview"></a>Genel Bakış
Service Fabric düğüm türü için dayanıklılık değeri, Azure Resource Manager şablonunun iki farklı bölümünde tanımlanmıştır. Sanal makine ölçek kümesi kaynağının sanal makine ölçek kümesi uzantısı bölümü ve Service Fabric kümesi kaynağının düğüm türü bölümü. Bu bölümlerdeki dayanıklılık değerinin eşleşmesi bir gereksinimdir, aksi takdirde kaynak dağıtımı başarısız olur.

Aşağıdaki bölümde, sanal makine ölçek kümesi uzantısı dayanıklılığı ayarı ve Service Fabric düğüm türü dayanıklılığı ayarı arasında bir dayanıklılık uyumsuzluğu örneği verilmiştir:  

**Sanal makine ölçek kümesi dayanıklılık ayarı**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Service Fabric düğüm türü dayanıklılık ayarı** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Hata iletileri
* Sanal makine ölçek kümesi dayanıklılık uyumsuzluğu geçerli Service Fabric düğüm türü dayanıklılık düzeyiyle eşleşmiyor
* Sanal makine ölçek kümesi dayanıklılığı, hedef Service Fabric düğüm türü dayanıklılık düzeyiyle eşleşmiyor
* Sanal makine ölçek kümesi dayanıklılığı, geçerli Service Fabric dayanıklılık düzeyiyle veya hedef Service Fabric düğüm türü dayanıklılık düzeyiyle eşleşiyor 

### <a name="mitigation"></a>Risk azaltma
Yukarıdaki hata iletilerinin herhangi biri tarafından belirtilen bir dayanıklılık uyuşmazlığını onarmak için:
1. Değerlerin eşleştiğinden emin olmak için, Azure Resource Manager şablonunun sanal makine ölçek kümesi uzantısında veya Service Fabric düğüm türü bölümünde dayanıklılık düzeyini güncelleştirin.
2. Azure Resource Manager şablonunu güncelleştirilmiş değerlerle yeniden dağıtın.


## <a name="seed-node-deletion"></a>Çekirdek düğüm silme 
### <a name="overview"></a>Genel Bakış
Service Fabric kümenin, kümenin birincil düğüm türünde çalışan sistem hizmetleri çoğaltmaları sayısını belirlemede kullanılan bir [güvenilirlik katmanı](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) özelliği vardır. Gerekli çoğaltmaların sayısı, kümenin birincil düğüm türünde tutulması gereken düğüm sayısı alt sınırını belirleyecek. Birincil düğüm türündeki düğümlerin sayısı güvenilirlik katmanı için gerekli en düşük değerin altına gittiğinde, küme kararsız hale gelir.  

### <a name="error-messages"></a>Hata iletileri 
Çekirdek düğüm kaldırma işlemi algılandı ve reddedilecek. 
* Bu işlem yalnızca {0} olası çekirdek düğümlerinin kümede kalmasına neden olur, ancak {1} en az bir değer gerekir.
* {0}Çekirdek düğümlerin dışına kaldırılması {1} , çekirdek düğüm çekirdeği kaybı nedeniyle kümenin düşmesine neden olur. Tek seferde kaldırılabileceği en fazla çekirdek düğüm sayısı {2} .
 
### <a name="mitigation"></a>Risk azaltma 
Birincil düğüm türü, kümenizde belirtilen güvenilirlik için yeterli sanal makineye sahip olduğundan emin olun. Sanal makine ölçek kümesini, belirtilen güvenilirlik katmanı için en az sayıda düğüm altına getirecek şekilde bir sanal makineyi kaldıracaksınız.
* Güvenilirlik katmanı doğru şekilde belirtilmişse, güvenilirlik katmanı için gereken birincil düğüm türünde yeterli düğüme sahip olduğunuzdan emin olun. 
* Güvenilirlik katmanı yanlış ise, herhangi bir sanal makine ölçek kümesi işlemini başlatmadan önce güvenilirlik düzeyini düşürmek için Service Fabric kaynağında bir değişiklik başlatın ve bunun tamamlanmasını bekleyin.
* Güvenilirlik katmanı bronz ise, lütfen kümenizdeki ölçeği dikkatlice ölçeklendirmek için aşağıdaki [adımları](./service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) izleyin.

## <a name="next-steps"></a>Sonraki adımlar
* VM 'lerde veya Windows Server çalıştıran bilgisayarlarda küme oluşturma: [Windows Server için Service Fabric kümesi oluşturma](service-fabric-cluster-creation-for-windows-server.md)
* VM 'lerde veya Linux çalıştıran bilgisayarlarda küme oluşturma: [Linux kümesi oluşturma](service-fabric-cluster-creation-via-portal.md)
* Sorun giderme Service Fabric: [sorun giderme kılavuzu](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
