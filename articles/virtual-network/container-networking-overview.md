---
title: Azure Sanal Ağ ile kapsayıcı ağı oluşturma | Microsoft Docs
description: Azure sanal ağ kapsayıcısı ağ arabirimi (CNı) eklentisi ve kapsayıcıların bir Azure sanal ağı kullanmak için nasıl etkinleştirileceği hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 727e79cede9319cb7aae701082d5842ad895a61d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223525"
---
# <a name="enable-containers-to-use-azure-virtual-network-capabilities"></a>Kapsayıcıların Azure Sanal Ağ özelliklerini kullanmasını sağlama

Sanal makinelerin kullandığı yazılımla tanımlanan ağ yığınını kullanarak gelişmiş Azure ağ özelliklerini kapsayıcılara ekleyin. Azure Sanal Ağ kapsayıcı ağı arabirimi (CNI) eklentisi bir Azure Sanal Makinesine yüklenir. Eklenti, sanal makinede oluşturulan kapsayıcılara bir sanal ağdan IP adresi atayarak onları sanal ağa ekler ve doğrudan diğer kapsayıcılar ile sanal ağ kaynaklarına bağlar. Eklenti, bağlantı için ağ katmanlarını veya yolları kullanmaz ve sanal makinelerle aynı performansı sunar. Eklentinin sunduğu özelliklerden en önemli olanları aşağıda verilmiştir:

- Her poda bir sanal ağ IP adresi atanır ve bir podda bir veya daha fazla kapsayıcı bulunabilir.
- Podlar eşlenen sanal ağlara veya ExpressRoute ya da siteden siteye VPN bağlantısı üzerinden şirket içi ağlara bağlanabilir. Eşlenen veya şirket içi ağlardan da podlara erişim sağlanabilir.
- Podlar sanal ağ hizmet uç noktalarıyla korunan Azure Depolama ve SQL Veritabanı gibi hizmetlere erişebilir.
- Ağ güvenlik grupları ve yollar doğrudan podlara uygulanabilir.
- Podlar sanal makineler gibi doğrudan bir Azure iç veya genel Yük Dengeleyici arkasına yerleştirilebilir.
- Podlara genel IP adresi atanarak doğrudan internet üzerinden erişim sağlanabilir. Podlar da internete erişebilir.
- Hizmetler, Giriş denetleyicileri ve Kube DNS gibi Kubernetes kaynaklarıyla sorunsuz bir şekilde çalışır. Bir Kubernetes Hizmeti, Azure Load Balancer aracılığıyla iç veya dış kullanıma sunulabilir.

Aşağıdaki resimde eklentinin podlara Azure Sanal Makinesi özelliklerini nasıl sağladığı gösterilmiştir:

![Kapsayıcı ağına genel bakış](./media/container-networking/container-networking-overview.png)

Eklenti hem Linux hem de Windows platformlarını destekler.

## <a name="connecting-pods-to-a-virtual-network"></a>Podları sanal ağa bağlama

Podlar sanal ağın parçası olan bir sanal makinede oluşturulur. Sanal makinenin ağ arabiriminde ikinci adres olarak podlar için bir IP adresi havuzu yapılandırılır. Azure CNI podlar için temel ağ bağlantısı ayarlarını yapar ve havuzdaki IP adreslerinin kullanımını yönetir. Sanal makinede bir pod oluşturulduğunda Azure CNI havuzdan kullanılabilir IP adresi atar ve podu sanal makinedeki yazılım köprüsüne bağlar. Pod sonlandırıldığında IP adresi havuza geri gönderilir. Aşağıdaki resimde podların sanal ağa nasıl bağlandığı gösterilmiştir:

![Kapsayıcı ağı ayrıntıları](./media/container-networking/container-networking-detail.png)

## <a name="internet-access"></a>İnternet erişimi

Podların internete erişmesini sağlamak için eklenti *iptables* kurallarını yapılandırarak podlardan internete giden trafikte ağ adresi çevirisi (NAT) gerçekleştirir. Paketin kaynak IP adresi sanal makine ağ arabiriminin birincil IP adresine çevrilir. Windows sanal makineleri otomatik olarak sanal makinenin bulunduğu alt ağın dışında bir IP adresine gönderilen NAT (SNAT) trafiği için kaynak oluşturur. Genellikle sanal ağın IP aralığının dışındaki bir IP adresine gönderilen tüm trafik çevrilir.

## <a name="limits"></a>Sınırlar

Eklenti sanal makine başına 250 pod ve bir sanal ağda 16.000 pod için destek sunar. Bu sınırlar [Azure Kubernetes Service](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-kubernetes-service-limits) için farklıdır.

## <a name="using-the-plug-in"></a>Eklentiyi kullanma

Eklenti, podlar veya Docker kapsayıcılarına temel sanal ağ bağlantısı sağlamak üzere şu şekillerde kullanılabilir:

- **Azure Kubernetes Service**: Eklenti Azure Kubernetes Service (AKS) ile tümleşiktir ve *Gelişmiş Ağ* seçeneği ile kullanılabilir. Gelişmiş Ağ, Kubernetes kümesini var olan veya yeni bir sanal ağa dağıtmanızı sağlar. Gelişmiş Ağ ve ayarlama adımları hakkında daha fazla bilgi için bkz. [AKS'de ağ yapılandırması](../aks/configure-azure-cni.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- **Aks-Engine**: AKS-Engine, Azure 'Da bir Kubernetes kümesinin dağıtımı için Azure Resource Manager şablonu oluşturan bir araçtır. Ayrıntılı yönergeler için bkz. [AKS-Engine Kubernetes kümeleri için eklentiyi dağıtma](deploy-container-networking.md#deploy-the-azure-virtual-network-container-network-interface-plug-in).
- **Azure 'da kendi Kubernetes kümenizi oluşturma**: eklenti, aks 'leri veya aks-Engine gibi araçları kullanarak kendi dağıttığınız Kubernetes kümelerinde pods için temel ağ sağlamak üzere kullanılabilir. Bu durumda eklentinin kümedeki tüm sanal makinelere yüklenmesi ve etkinleştirilmesi gerekir. Ayrıntılı yönergeler için bkz. [Eklentiyi kendi dağıttığınız Kubernetes kümesi için dağıtma](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).
- **Azure'daki Docker kapsayıcılarına sanal ağ ekleme**: Eklenti bir Kubernetes kümesi kullanmak istemediğiniz ve sanal makinelerde sanal ağa eklenmiş Docker kapsayıcısı oluşturmak istediğiniz durumlarda kullanılabilir. Ayrıntılı yönergeler için bkz. [Eklentiyi Docker için dağıtma](deploy-container-networking.md#deploy-plug-in-for-docker-containers).

## <a name="next-steps"></a>Sonraki adımlar

Kubernetes kümeleri veya Docker kapsayıcıları için [eklentiyi dağıtma](deploy-container-networking.md)