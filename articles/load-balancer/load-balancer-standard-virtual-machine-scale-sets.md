---
title: Azure Standart Load Balancer ve sanal makine ölçek kümeleri için kurallar ekleme
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: Bu öğrenme yoluyla Azure Standart Load Balancer ve sanal makine ölçek kümelerini kullanmaya başlayın.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 7a2e0531427343a2ec267de54cee05b5eb25889f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592288"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>Sanal makine ölçek kümeleriyle Azure Load Balancer kurallar ekleme

Sanal Makine Ölçek Kümeleri ve Azure Load Balancer çalışırken aşağıdaki yönergeleri göz önünde bulundurun.

## <a name="port-forwarding-and-inbound-nat-rules"></a>Bağlantı noktası iletme ve gelen NAT kuralları

Ölçek kümesi oluşturulduktan sonra, yük dengeleyicinin bir sistem durumu araştırması tarafından kullanılan bir yük dengeleme kuralı için arka uç bağlantı noktası değiştirilemez. Bağlantı noktasını değiştirmek için, sanal makine ölçek kümesini güncelleştirerek ve bağlantı noktasını güncelleştirerek sistem durumu araştırmasını kaldırın. Sonra sistem durumu araştırmasını yeniden yapılandırın.

Yük dengeleyicinin arka uç havuzunda sanal makine ölçek kümesini kullandığınızda, varsayılan gelen NAT kuralları otomatik olarak oluşturulur.
  
## <a name="inbound-nat-pool"></a>Gelen NAT havuzu

Her sanal makine ölçek kümesinin en az bir gelen NAT havuzu olmalıdır. Gelen NAT havuzu, gelen NAT kurallarından oluşan bir koleksiyondur. Bir gelen NAT havuzu birden çok sanal makine ölçek kümesini destekleyemez.

## <a name="load-balancing-rules"></a>Yük Dengeleme kuralları

Yük dengeleyicinin arka uç havuzunda sanal makine ölçek kümesini kullandığınızda, varsayılan Yük Dengeleme kuralı otomatik olarak oluşturulur.
  
## <a name="outbound-rules"></a>Giden kuralları

Bir yük dengeleme kuralı tarafından zaten başvurulan bir arka uç havuzu için bir giden kuralı oluşturmak için, gelen yük dengeleme kuralı oluşturulduğunda Azure portal **örtük giden kuralları oluştur** altında **Hayır** ' ı seçin.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Yük Dengeleme kuralı oluşturmayı gösteren ekran görüntüsü." border="true":::

Mevcut bir Load Balancer örneğiyle bir sanal makine ölçek kümesi dağıtmak için aşağıdaki yöntemleri kullanın:

* [Azure portal kullanarak bir sanal makine ölçek kümesini mevcut bir Azure Load Balancer örneğiyle yapılandırın](./configure-vm-scale-set-portal.md)
* [Azure PowerShell kullanarak var olan bir Azure Load Balancer örneği ile sanal makine ölçek kümesi yapılandırma](./configure-vm-scale-set-powershell.md)
* [Azure CLı kullanarak bir sanal makine ölçek kümesini mevcut bir Azure Load Balancer örneğiyle yapılandırma](./configure-vm-scale-set-cli.md)
* [Bir sanal makine ölçek kümesi tarafından kullanılan Azure Load Balancer var olan bir örneğini güncelleştirme veya silme](./update-load-balancer-with-vm-scale-set.md)
