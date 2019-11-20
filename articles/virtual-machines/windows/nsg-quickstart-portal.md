---
title: Azure portal kullanarak bir VM 'ye bağlantı noktalarını açma
description: Azure portalındaki Resource Manager dağıtım modelini kullanarak bir bağlantı noktasını açmayı veya Windows VM 'nize bir uç nokta oluşturmayı öğrenin
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: fe9b0f590dbb728a54ac66fcaa49bf3d00417e41
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033101"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Azure portal bir sanal makineye bağlantı noktaları açma
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Azure'da oturum açma
https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

1. VM için kaynak grubunu arayıp seçin, **Ekle**' yi seçin ve **ağ güvenlik grubu**' nu arayıp seçin.

2. **Oluştur**'u seçin.

    **Ağ güvenlik grubu oluştur** penceresi açılır.

    ![Ağ güvenlik grubu oluşturma](./media/nsg-quickstart-portal/create-nsg.png)

2. Ağ güvenlik grubunuz için bir ad girin. 

3. Bir kaynak grubu seçin veya oluşturun ve ardından bir konum seçin.

4. Ağ güvenlik grubunu oluşturmak için **Oluştur** ' u seçin.

## <a name="create-an-inbound-security-rule"></a>Gelen güvenlik kuralı oluşturma

1. Yeni ağ güvenlik grubunuzu seçin. 

2. **Gelen güvenlik kuralları**' nı seçin ve ardından **Ekle**' yi seçin.

    ![Gelen kuralı ekle](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. **Gelişmiş**'i seçin. 

4. **Http**gibi açılan menüden ortak bir **hizmet** seçin. Ayrıca, kullanmak üzere belirli bir bağlantı noktası sağlamak istiyorsanız **özel** ' i de seçebilirsiniz. 

5. İsteğe bağlı olarak, **Öncelik** veya **adı**değiştirin. Öncelik kuralların uygulanma sırasını etkiler: sayısal değer ne kadar düşükse, kural daha önce uygulanır.

6. Kuralı oluşturmak için **Ekle** ' yi seçin.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Ağ güvenlik grubunuzu bir alt ağ ile ilişkilendirin

Gerçekleştirmeniz gereken son adım, ağ güvenlik grubunuzu bir alt ağ ile veya belirli bir ağ arabirimiyle ilişkilendirmektir. Bu örnekte ağ güvenlik grubunu bir alt ağ ile ilişkilendireceğiz. 

1. **Alt ağları**seçip **ilişkilendir**' i seçin.

    ![Ağ güvenlik grubunu bir alt ağ ile ilişkilendirme](./media/nsg-quickstart-portal/associate-subnet.png)

2. Sanal ağınızı ve ardından uygun alt ağı seçin.

    ![Ağ güvenlik grubunu sanal ağ ile ilişkilendirme](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Bu alt ağa bağlandığınız tüm VM 'Ler artık 80 numaralı bağlantı noktasında erişilebilir.

## <a name="additional-information"></a>Ek bilgiler

[Bu makaledeki adımları Azure PowerShell kullanarak da gerçekleştirebilirsiniz](nsg-quickstart-powershell.md).

Bu makalede açıklanan komutlar, sanal makinenize hızlı bir şekilde trafik akışını almanızı sağlar. Ağ güvenlik grupları, kaynaklarınıza erişimi denetlemek için çok sayıda harika özellik ve ayrıntı düzeyi sağlar. Daha fazla bilgi için bkz. ağ [güvenlik grubu ile ağ trafiğini filtreleme](../../virtual-network/tutorial-filter-network-traffic.md).

Yüksek oranda kullanılabilir Web uygulamaları için VM 'lerinizi bir Azure Yük dengeleyicisinin arkasına yerleştirmeyi göz önünde bulundurun. Yük dengeleyici, trafiği, trafik filtrelemesi sağlayan bir ağ güvenlik grubuyla sanal makinelere dağıtır. Daha fazla bilgi için bkz. [Azure 'Da Windows sanal makinelerinin yük dengelemesi, yüksek oranda kullanılabilir bir uygulama oluşturmak için](tutorial-load-balancer.md).

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, 80 numaralı bağlantı noktasında HTTP trafiğine izin veren bir gelen kuralı oluşturmuş ve sonra bu kuralı bir alt ağla ilişkilendirdikten sonra bir ağ güvenlik grubu oluşturdunuz. 

Aşağıdaki makalelerde daha ayrıntılı ortamlar oluşturma hakkında bilgi edinebilirsiniz:
- [Azure Resource Manager’a genel bakış](../../azure-resource-manager/resource-group-overview.md)
- [Güvenlik grupları](../../virtual-network/security-overview.md)