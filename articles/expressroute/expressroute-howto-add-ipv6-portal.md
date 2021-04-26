---
title: 'Azure ExpressRoute: Azure portal kullanarak IPv6 desteği ekleme'
description: Azure portal kullanarak Azure dağıtımlarına bağlanmak için IPv6 desteğinin nasıl ekleneceğini öğrenin.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 7f5afc05a8d03d33366a2f76318bcf5e039d4d30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561670"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Azure portal kullanarak özel eşleme için IPv6 desteği ekleme (Önizleme)

Bu makalede, Azure portal kullanarak Azure 'daki kaynaklarınıza ExpressRoute aracılığıyla bağlanmak üzere IPv6 desteğinin nasıl ekleneceği açıklanmaktadır. 

> [!Note]
> Bu özellik şu anda [kullanılabilirlik alanları olan Azure bölgelerinde](../availability-zones/az-region.md#azure-regions-with-availability-zones)önizleme için kullanılabilir. Bu nedenle, ExpressRoute bağlantı hattı herhangi bir eşleme konumu kullanılarak oluşturulabilir, ancak bağlandığı IPv6 tabanlı dağıtımlar Kullanılabilirlik Alanları bir bölgede olmalıdır.

## <a name="register-for-public-preview"></a>Genel önizlemeye kaydolun
IPv6 desteği eklemeden önce aboneliğinizi kaydetmeniz gerekir. Kaydolmak için Azure PowerShell aracılığıyla aşağıdaki komutları çalıştırın:

1.  Azure 'da oturum açın ve aboneliği seçin. ExpressRoute bağlantı hattınızı içeren abonelik için bu komutları ve Azure dağıtımlarınızı içeren aboneliği (farklıysa) çalıştırın.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Aşağıdaki komutu kullanarak aboneliğinizi genel önizleme için kaydedin:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

İsteğiniz daha sonra ExpressRoute ekibi tarafından 2-3 iş günü içinde onaylanır.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Bir tarayıcıdan [Azure Portal](https://portal.azure.com)gidin ve Azure hesabınızla oturum açın.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>ExpressRoute devrenizin IPv6 özel eşlemesi ekleme

1. [Bir ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) bağlantı hattı oluşturun veya değiştirmek istediğiniz mevcut bağlantı hattına gidin.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="ExpressRoute devre listesinin ekran görüntüsü.":::

1. **Azure özel** eşleme yapılandırmasını seçin.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="ExpressRoute Genel Bakış sayfasının ekran görüntüsü.":::

1. **Alt ağlar** Için "her ikisi de" i seçerek mevcut IPv4 özel eşleme yapılandırmanıza IPv6 özel eşlemesi ekleyin veya "IPv6" öğesini seçerek yeni bağlantı hattınızı yalnızca IPv6 özel eşlemesini etkinleştirin. Birincil bağlantınız ve ikincil bağlantılarınız için sahip olduğunuz bir çift/126 IPv6 alt ağı sağlayın. Bu alt ağların her birinde, Microsoft 'un yönlendiricisi için kullanılabilen ikinci IP 'yi kullandığından, ilk kullanılabilir IP adresini yönlendiricinize atayacaksınız. Tüm parametreleri belirttikten sonra eşleme yapılandırmanızı **kaydedin** .

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Özel eşleme sayfasına IPv6 ekleme ekranının ekran görüntüsü.":::

1. Yapılandırma başarıyla kabul edildikten sonra, aşağıdaki örneğe benzer bir şey görmeniz gerekir.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Özel eşleme için yapılandırılmış IPv6 ekran görüntüsü.":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Mevcut bir sanal ağla bağlantınızı güncelleştirme

IPv6 özel eşliğini kullanmak istediğiniz Kullanılabilirlik Alanları bir bölgede Azure kaynakları 'nın mevcut bir ortamınıza sahipseniz aşağıdaki adımları izleyin.

1. ExpressRoute devrenizin bağlı olduğu sanal ağa gidin.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Sanal ağ listesinin ekran görüntüsü.":::

1. **Adres alanı** sekmesine gidin ve sanal ağınıza bir IPv6 adres alanı ekleyin. Adres alanınızı **kaydedin** .

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Sanal ağa IPv6 adres alanı ekleme ekranının ekran görüntüsü.":::

1. **Alt ağlar** sekmesine gidin ve **gatewaysubnet** öğesini seçin. **IPv6 adres alanı Ekle** ' ye başvurun ve alt ağınız Için bir IPv6 adres alanı sağlayın. Ağ Geçidi IPv6 alt ağı/64 veya daha büyük olmalıdır. Tüm parametreleri belirttikten sonra yapılandırmanızı **kaydedin** .

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Alt ağa IPv6 adres alanı ekleme ekranının ekran görüntüsü.":::

1. Bölgesel olarak yedekli bir ağ geçidiniz varsa, ExpressRoute ağ geçidinize gidin ve IPv6 bağlantısını etkinleştirmek için kaynağı yenileyin. Aksi takdirde, bölgesel olarak yedekli bir SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) kullanarak [sanal ağ geçidini oluşturun](expressroute-howto-add-gateway-portal-resource-manager.md) . FastPath kullanmayı planlıyorsanız, ErGw3AZ kullanın.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="Ağ geçidini yenileme ekran görüntüsü.":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Yeni bir sanal ağa bağlantı oluştur

IPv6 özel eşlemesini kullanarak Kullanılabilirlik Alanları bir bölgedeki yeni bir Azure kaynakları kümesine bağlanmayı planlıyorsanız aşağıdaki adımları izleyin.

1. Hem IPv4 hem de IPv6 adres alanı ile çift yığın sanal ağı oluşturun. Daha fazla bilgi için bkz. [sanal ağ oluşturma](../virtual-network/quick-create-portal.md#create-a-virtual-network).

1. [Çift Stack ağ geçidi alt ağını oluşturun](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. Bölgesel olarak yedekli bir SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) kullanarak [sanal ağ geçidini oluşturun](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) . FastPath kullanmayı planlıyorsanız, ErGw3AZ kullanın (Bu seçeneğin yalnızca ExpressRoute Direct kullanan devreler için kullanılabilir olduğunu unutmayın).

1. [Sanal ağınızı ExpressRoute devrenizi Ile ilişkilendirin](expressroute-howto-linkvnet-portal-resource-manager.md).

## <a name="limitations"></a>Sınırlamalar
IPv6 desteği, Kullanılabilirlik Alanları bölgelerdeki dağıtımlara bağlantılar için kullanılabilir olsa da, aşağıdaki kullanım durumlarını desteklemez:

* AZ ExpressRoute ağ geçidi SKU 'SU aracılığıyla Azure 'daki dağıtımlara bağlantılar
* AZ olmayan bölgelerde dağıtımlara bağlantılar
* ExpressRoute devreleri arasında bağlantı Global Reach
* Sanal WAN ile ExpressRoute kullanımı
* ExpressRoute doğrudan devrelerine sahip FastPath
* Şu eşleme konumlarında devrelere sahip FastPath: Dubai
* VPN Gateway birlikte bulunma

## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute sorunlarını gidermek için aşağıdaki makalelere bakın:

* [ExpressRoute bağlantısını doğrulama](expressroute-troubleshooting-expressroute-overview.md)
* [Ağ performansı sorunlarını giderme](expressroute-troubleshooting-network-performance.md)