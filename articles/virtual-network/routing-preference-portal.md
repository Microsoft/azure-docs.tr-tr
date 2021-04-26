---
title: Genel IP adresi için yönlendirme tercihini Yapılandırma-Azure portal
description: Internet trafiği yönlendirme tercihi ile genel IP oluşturmayı öğrenin
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: f445eab65e8d2448e57bad19c52a4b72732016bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672906"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Azure portal kullanarak genel IP adresi için yönlendirme tercihini yapılandırın

Bu makalede genel bir IP adresi için ISS ağı (**Internet** seçeneği) aracılığıyla [yönlendirme tercihini](./routing-preference-overview.md) yapılandırma gösterilmektedir. Genel IP adresini oluşturduktan sonra, internet 'e gelen ve giden trafik için aşağıdaki Azure kaynaklarıyla ilişkilendirebilirsiniz:

* Sanal makine
* Sanal makine ölçek kümesi
* Azure Kubernetes Service (AKS)
* Internet 'e yönelik yük dengeleyici
* Application Gateway
* Azure Güvenlik Duvarı

Varsayılan olarak, genel IP adresi için yönlendirme tercihi tüm Azure hizmetleri için Microsoft Global Network 'e ayarlanır ve herhangi bir Azure hizmetiyle ilişkilendirilebilir.

Azure aboneliğiniz yoksa şimdi [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Yönlendirme tercihi ile genel IP adresi oluşturma
1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. **Kaynak oluştur**’u seçin.
3. Arama kutusuna *genel IP adresi* yazın.
3. Arama sonuçlarında **genel IP adresi**' ni seçin. Sonra, **genel IP adresi** sayfasında **Oluştur**' u seçin.
1. SKU için **Standart**' ı seçin.
1. **Yönlendirme tercihi** için **Internet**' i seçin.

      ![Genel IP adresi oluşturma](./media/routing-preference-portal/public-ip-new.png)
1. **IPV4 IP adresi yapılandırması** bölümünde, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Abonelik | Aboneliğinizi seçin.|
    | Kaynak grubu | **Yeni oluştur**' u seçin, *RoutingPreferenceResourceGroup* girin ve **Tamam**' ı seçin. |
    | Konum | **Doğu ABD**’yi seçin.|
    | Kullanılabilirlik alanı | Varsayılan değer olan- **bölge-yedekli** değerini koruyun. |
1. **Oluştur**’u seçin.

    > [!NOTE]
    > Genel IP adresleri IPv4 veya IPv6 adresiyle oluşturulur. Ancak, yönlendirme tercihi şu anda yalnızca ıPV4 'Ü destekler.

Yukarıdaki oluşturulmuş genel IP adresini bir [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makinesiyle ilişkilendirebilirsiniz. Öğretici sayfasındaki CLı bölümünü kullanın: genel IP adresini VM 'niz ile ilişkilendirmek için bir [sanal makineyle genel IP adresi ilişkilendirin](associate-public-ip-address-vm.md#azure-cli) . Ayrıca, yük dengeleyici **ön uç** yapılandırmasına atayarak yukarıda oluşturulan genel IP adresini bir [Azure Load Balancer](../load-balancer/load-balancer-overview.md)ilişkilendirebilirsiniz. Genel IP adresi yükü dengelenmiş bir sanal IP adresi (VIP) olarak işlev görür.

## <a name="next-steps"></a>Sonraki adımlar
- [Yönlendirme tercihi ile genel IP](routing-preference-overview.md)hakkında daha fazla bilgi edinin.
- [BIR VM için yönlendirme tercihini yapılandırın](tutorial-routing-preference-virtual-machine-portal.md).
- [PowerShell kullanarak genel IP adresi için yönlendirme tercihini yapılandırın](routing-preference-powershell.md).
- Azure 'da [genel IP adresleri](./public-ip-addresses.md#public-ip-addresses) hakkında daha fazla bilgi edinin.
- Tüm [genel IP adresi ayarları](virtual-network-public-ip-address.md#create-a-public-ip-address)hakkında daha fazla bilgi edinin.