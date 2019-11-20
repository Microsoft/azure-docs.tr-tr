---
title: Azure Kubernetes ağ ilkeleri | Microsoft Docs
description: Kubernetes kümenizi güvenli hale getirmek için Kubernetes ağ ilkeleri hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 5a6da7e65a9a3e962a2df37b062792fbb990d04d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159685"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Azure Kubernetes ağ ilkelerine genel bakış

Ağ Ilkeleri, ağ güvenlik grupları (NSG 'Ler) gibi sanal makineler için mikro segmentler sağlar. Azure ağ Ilkesi uygulama, standart Kubernetes ağ Ilkesi belirtimini destekler. Etiketler grubunu seçmek ve bu yığından ve bu yığınlardan izin verilen trafik türünü belirten giriş ve çıkış kurallarının bir listesini tanımlamak için Etiketler ' i kullanabilirsiniz. Kubernetes [belgelerindeki](https://kubernetes.io/docs/concepts/services-networking/network-policies/)Kubernetes ağ ilkeleri hakkında daha fazla bilgi edinin.

![Kubernetes ağ ilkelerine genel bakış](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure ağ ilkeleri, kapsayıcılar için VNet tümleştirmesi sağlayan Azure CNı ile birlikte çalışır. Günümüzde yalnızca Linux düğümlerinde desteklenir. Uygulamalar, trafik filtrelemeyi zorlamak için tanımlanan ilkeleri temel alan Linux IP tablosu kurallarını yapılandırır.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Kubernetes kümeniz için güvenliği planlama
Kümeniz için güvenlik uygularken, Kuzey-Güney trafiğini filtrelemek için ağ güvenlik grupları (NSG 'ler) kullanın, diğer bir deyişle, küme alt ağınızı girip bırakarak trafik ve Doğu Batı trafiği için Kubernetes ağ ilkelerini kullanın, diğer bir deyişle, içindeki IP 'ler arasındaki trafik kümeniz.

## <a name="using-azure-kubernetes-network-policies"></a>Azure Kubernetes ağ ilkelerini kullanma
Azure ağ Ilkeleri, pods için mikro segmentasyon sağlamak üzere aşağıdaki yollarla kullanılabilir.

### <a name="acs-engine"></a>ACS-motor
ACS-Engine, Azure 'da bir Kubernetes kümesinin dağıtımı için Azure Resource Manager şablonu oluşturan bir araçtır. Küme yapılandırması, şablon oluşturma sırasında araca geçirilen bir JSON dosyasında belirtilir. Desteklenen küme ayarlarının tamamı ve açıklamaları hakkında daha fazla bilgi edinmek için bkz. Microsoft Azure Container Service Engine-Cluster Definition.

ACS-Engine kullanılarak dağıtılan kümelerdeki ilkeleri etkinleştirmek için, küme tanımı dosyasındaki networkPolicy ayarının değerini "Azure" olarak belirtin.

#### <a name="example-configuration"></a>Örnek yapılandırma

Aşağıdaki JSON örnek yapılandırması yeni bir sanal ağ ve alt ağ oluşturur ve Azure CNı ile buna bir Kubernetes kümesi dağıtır. JSON dosyasını düzenlemek için "Notepad" kullanmanızı öneririz. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Azure 'da kendi Kubernetes kümenizi oluşturma
Uygulama, ACS motoru gibi araçlara bağlı kalmadan, kendi dağıttığınız Kubernetes kümelerinde pods 'Ler için ağ Ilkeleri sağlamak üzere kullanılabilir. Bu durumda, ilk olarak CNı eklentisini yüklersiniz ve bir kümedeki her sanal makinede etkinleştirebilirsiniz. Ayrıntılı yönergeler için bkz. [Eklentiyi kendi dağıttığınız Kubernetes kümesi için dağıtma](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Küme dağıtıldıktan sonra, *Daemonset* Azure ağ ilkesi 'ni indirmek ve kümeye uygulamak için aşağıdaki `kubectl` komutunu çalıştırın.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Çözüm Ayrıca açık kaynak olur ve kod [Azure Container Networking deposunda](https://github.com/Azure/azure-container-networking/tree/master/npm)kullanılabilir.



## <a name="next-steps"></a>Sonraki adımlar
- [Azure Kubernetes hizmeti](../aks/intro-kubernetes.md)hakkında bilgi edinin.
-  [Kapsayıcı ağı](container-networking-overview.md)hakkında bilgi edinin.
- Kubernetes kümeleri veya Docker kapsayıcıları için [eklentiyi dağıtın](deploy-container-networking.md) .
