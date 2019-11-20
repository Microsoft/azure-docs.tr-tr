---
title: Service Fabric Azure Resource Manager dağıtım guardrayları | Microsoft Docs
description: Bu makalede, Azure Resource Manager aracılığıyla Service Fabric kümesi dağıtımında yapılan yaygın hatalara ve bunların nasıl önleneceğini gösteren bir genel bakış sunulmaktadır.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: gamonroy
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/30/2019
ms.author: pepogors
ms.openlocfilehash: 3ea6f850685a695644cfc3073fc939a58901658c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828636"
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

## <a name="next-steps"></a>Sonraki adımlar
* VM 'lerde veya Windows Server çalıştıran bilgisayarlarda küme oluşturma: [Windows Server için Service Fabric kümesi oluşturma](service-fabric-cluster-creation-for-windows-server.md)
* VM 'lerde veya Linux çalıştıran bilgisayarlarda küme oluşturma: [Linux kümesi oluşturma](service-fabric-cluster-creation-via-portal.md)
* Sorun giderme Service Fabric: [sorun giderme kılavuzu](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
