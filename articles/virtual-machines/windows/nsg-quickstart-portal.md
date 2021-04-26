---
title: Azure portal kullanarak bir VM 'ye bağlantı noktalarını açma
description: Azure portal kullanarak sanal makinenize bir bağlantı noktası açmayı/bir uç nokta oluşturmayı öğrenin
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/27/2020
ms.author: cynthn
ms.openlocfilehash: 28b3ee98d44e1567d9e25cf9237015396975ab01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550459"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Azure portal bir sanal makineye bağlantı noktaları açma
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Azure'da oturum açma
https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

1. VM için kaynak grubunu arayıp seçin, **Ekle**' yi seçin ve **ağ güvenlik grubu**' nu arayıp seçin.

1. **Oluştur**’u seçin.

    **Ağ güvenlik grubu oluştur** penceresi açılır.

    ![Ağ güvenlik grubu oluşturma](./media/nsg-quickstart-portal/create-nsg.png)

1. Ağ güvenlik grubunuz için bir ad girin. 

1. Bir kaynak grubu seçin veya oluşturun ve ardından bir konum seçin.

1. Ağ güvenlik grubunu oluşturmak için **Oluştur** ' u seçin.

## <a name="create-an-inbound-security-rule"></a>Gelen güvenlik kuralı oluşturma

1. Yeni ağ güvenlik grubunuzu seçin. 

1. Sol menüden **gelen güvenlik kuralları** ' nı seçin ve ardından **Ekle**' yi seçin.

    ![Gelişmiş sayfaya geç](./media/nsg-quickstart-portal/advanced.png)

1. **Gelen güvenlik kuralı ekle** sayfasında, sayfanın üst kısmındaki **temel** 'den **Gelişmiş** ' e geçiş yapın. 

1. **Http** gibi açılan menüden ortak bir **hizmet** seçin. Ayrıca, kullanmak üzere belirli bir bağlantı noktası sağlamak istiyorsanız **özel** ' i de seçebilirsiniz. 

1. İsteğe bağlı olarak, **Öncelik** veya **adı** değiştirin. Öncelik kuralların uygulanma sırasını etkiler: sayısal değer ne kadar düşükse, kural daha önce uygulanır.

1. Kuralı oluşturmak için **Ekle** ' yi seçin.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Ağ güvenlik grubunuzu bir alt ağ ile ilişkilendirin

Gerçekleştirmeniz gereken son adım, ağ güvenlik grubunuzu bir alt ağ ile veya belirli bir ağ arabirimiyle ilişkilendirmektir. Bu örnekte ağ güvenlik grubunu bir alt ağ ile ilişkilendireceğiz. 

1. Sol menüden **alt ağlar** ' ı seçin ve ardından **ilişkilendir**' i seçin.

1. Sanal ağınızı ve ardından uygun alt ağı seçin.

    ![Ağ güvenlik grubunu sanal ağ ile ilişkilendirme](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. İşiniz bittiğinde **Tamam**’ı seçin.

## <a name="additional-information"></a>Ek bilgiler

[Bu makaledeki adımları Azure PowerShell kullanarak da gerçekleştirebilirsiniz](nsg-quickstart-powershell.md).

Bu makalede açıklanan komutlar, sanal makinenize hızlı bir şekilde trafik akışını almanızı sağlar. Ağ güvenlik grupları, kaynaklarınıza erişimi denetlemek için çok sayıda harika özellik ve ayrıntı düzeyi sağlar. Daha fazla bilgi için bkz. ağ [güvenlik grubu ile ağ trafiğini filtreleme](../../virtual-network/tutorial-filter-network-traffic.md).

Yüksek oranda kullanılabilir Web uygulamaları için VM 'lerinizi bir Azure Yük dengeleyicisinin arkasına yerleştirmeyi göz önünde bulundurun. Yük dengeleyici, trafiği, trafik filtrelemesi sağlayan bir ağ güvenlik grubuyla sanal makinelere dağıtır. Daha fazla bilgi için bkz. [Azure 'Da Windows sanal makinelerinin yük dengelemesi, yüksek oranda kullanılabilir bir uygulama oluşturmak için](tutorial-load-balancer.md).

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, 80 numaralı bağlantı noktasında HTTP trafiğine izin veren bir gelen kuralı oluşturmuş ve sonra bu kuralı bir alt ağla ilişkilendirdikten sonra bir ağ güvenlik grubu oluşturdunuz. 

Aşağıdaki makalelerde daha ayrıntılı ortamlar oluşturma hakkında bilgi edinebilirsiniz:
- [Azure Resource Manager genel bakış](../../azure-resource-manager/management/overview.md)
- [Güvenlik grupları](../../virtual-network/network-security-groups-overview.md)
