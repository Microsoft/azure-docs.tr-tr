---
title: 'Öğretici-şirket içi ağı sanal ağa bağlama: Azure portal'
description: Şirket içi ağınızdan, portalı kullanarak genel İnternet üzerinden bir Azure sanal ağı arasında bir siteden siteye VPN Gateway IPSec bağlantısı oluşturun.
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/04/2020
ms.openlocfilehash: ccb43c3e7efb9289450ad9a71c003f54e5362b66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945200"
---
# <a name="tutorial-create-a-site-to-site-connection-in-the-azure-portal"></a>Öğretici: Azure portal siteden siteye bağlantı oluşturma

Azure VPN ağ geçitleri, müşterinin iş yeri ile Azure arasında konumlar arası bağlantı sağlar. Bu öğretici, şirket içi ağınızdan VNet 'e siteden siteye VPN Ağ Geçidi bağlantısı oluşturmak için Azure portal nasıl kullanacağınızı gösterir. Ayrıca, bu yapılandırmayı [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) veya [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)kullanarak da oluşturabilirsiniz.

:::image type="content" source="./media/tutorial-site-to-site-portal/diagram.png" alt-text="Siteden Siteye şirket içi ve dışı karışık VPN Gateway bağlantısı diyagramı":::

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal ağ oluşturma
> * VPN ağ geçidi oluşturma
> * Yerel ağ geçidi oluşturma
> * VPN bağlantısı oluşturma
> * Bağlantıyı doğrulama
> * Sanal makineye bağlanma

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. Yoksa, [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Uyumlu bir VPN cihazı ve bu cihazı yapılandırabilecek birinin bulunduğundan emin olun. Uyumlu VPN cihazları ve cihaz yapılandırması hakkında daha fazla bilgi için bkz.[VPN Cihazları Hakkında](vpn-gateway-about-vpn-devices.md).
* VPN cihazınız için dışarıya dönük genel bir IPv4 adresi olduğunu doğrulayın.
* Şirket içi ağ yapılandırmanızda bulunan IP adresi aralıklarıyla ilgili fazla bilginiz yoksa size bu ayrıntıları sağlayabilecek biriyle çalışmanız gerekir. Bu yapılandırmayı oluşturduğunuzda, Azure’un şirket içi konumunuza yönlendireceği IP adres aralığı ön eklerini oluşturmanız gerekir. Şirket içi ağınızın alt ağlarından hiçbiri, bağlanmak istediğiniz sanal ağ alt ağlarıyla çakışamaz.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Sanal ağ oluşturma

Aşağıdaki değerleri kullanarak bir sanal ağ (VNet) oluşturun:

* **Kaynak grubu:** TestRG1
* **Ad:** VNet1
* **Bölge:** (US) Doğu ABD
* **IPv4 adres alanı:** 10.1.0.0/16
* **Alt ağ adı:** Uçta
* **Alt ağ adres alanı:** 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>VPN ağ geçidi oluşturma

Bu adımda sanal ağınız için sanal ağ geçidi oluşturacaksınız. Bir ağ geçidinin oluşturulması, seçili ağ geçidi SKU’suna bağlı olarak 45 dakika veya daha uzun sürebilir.

### <a name="about-the-gateway-subnet"></a>Ağ geçidi alt ağı hakkında

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>Ağ geçidini oluşturma

Aşağıdaki değerleri kullanarak bir VPN Ağ Geçidi oluşturun:

* **Ad:** VNet1GW
* **Bölge:** Doğu ABD
* **Ağ geçidi türü:** VPN
* **VPN türü:** Rota tabanlı
* **SKU:** VpnGw1
* **Oluşturma:** Generation1
* **Sanal ağ:** VNet1
* **Ağ geçidi alt ağ adres aralığı:** 10.1.255.0/27
* **Genel IP adresi:** Yeni oluştur
* **Genel IP adresi adı:** VNet1GWpip
* **Etkin-etkin modunu etkinleştir:** Devre dışı
* **BGP 'Yi yapılandırma:** Devre dışı

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="view-the-public-ip-address"></a><a name="view"></a>Genel IP adresini görüntüle

Ağ Geçidi genel IP adresini ağ geçidinizin **genel bakış** sayfasında görüntüleyebilirsiniz.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Genel bakış sayfası":::

Genel IP adresi nesnesiyle ilgili ek bilgileri görmek için **genel IP adresi**' nin yanındaki ad/IP adresi bağlantısına tıklayın.

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>Yerel ağ geçidi oluşturma

Yerel ağ geçidi, yönlendirme amacıyla Şirket içi konumunuzu (site) temsil eden belirli bir nesnedir. Siteye Azure’un başvuruda bulunmak için kullanabileceği bir ad verir, ardından bağlantı oluşturacağınız şirket içi VPN cihazının IP adresini belirtirsiniz. Ayrıca, VPN ağ geçidi üzerinden VPN cihazına yönlendirilecek IP adresi ön eklerini de belirtirsiniz. Belirttiğiniz adres ön ekleri, şirket içi adresinizde yer alan ön eklerdir. Şirket içi ağınız değişirse veya VPN cihazının genel IP adresini değiştirmeniz gerekirse değerleri daha sonra kolayca güncelleştirebilirsiniz.

Aşağıdaki değerleri kullanarak bir yerel ağ geçidi oluşturun:

* **Ad:** Site1
* **Kaynak Grubu:** TestRG1
* **Konum:** Doğu ABD

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>VPN cihazınızı yapılandırma

Bir şirket içi ağı ile Siteden Siteye bağlantılar için VPN cihazı gerekir. Bu adımda VPN cihazınızı yapılandıracaksınız. VPN cihazınızı yapılandırırken aşağıdaki değerlere sahip olmanız gerekir:

* Paylaşılan bir anahtar. Siteden Siteye VPN bağlantınızı oluştururken belirttiğiniz paylaşılan anahtarın aynısıdır. Bu örneklerde temel bir paylaşılan anahtar kullanılır. Kullanmak için daha karmaşık bir anahtar oluşturmanız önerilir.
* Sanal ağ geçidinizin Genel IP adresi. Azure Portal, PowerShell veya CLI kullanarak genel IP adresini görüntüleyebilirsiniz. Azure portal kullanarak VPN ağ geçidinizin genel IP adresini bulmak için **sanal ağ geçitleri**' ne gidin ve ağ geçidinizin adını seçin.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>VPN bağlantısı oluşturma

Sanal ağ geçidiniz ile şirket içi VPN cihazınız arasında Siteden Siteye VPN bağlantısı oluşturun.

Aşağıdaki değerleri kullanarak bir bağlantı oluşturun:

* **Yerel ağ geçidi adı:** Site1
* **Bağlantı adı:** VNet1toSite1
* **Ortak anahtar:** Bu örnekte abc123 kullanılır. Ancak, VPN donanımınızla uyumlu herhangi bir seçeneği kullanabilirsiniz. Önemli olan, değerin bağlantının her iki tarafında eşleşmesidir.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>VPN bağlantısını doğrulama

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connect-to-a-virtual-machine"></a><a name="connectVM"></a>Sanal makineye bağlanma

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="optional-steps"></a>İsteğe bağlı adımlar

### <a name="add-additional-connections-to-the-gateway"></a><a name="addconnect"></a>Ağ geçidine ek bağlantı ekleme

Hiçbir adres alanının bağlantılar arasında çakışmaması şartıyla başka bağlantılar ekleyebilirsiniz.

1. Ek bir bağlantı eklemek için VPN ağ geçidine gidin ve bağlantılar sayfasını açmak için **Bağlantılar** ' ı seçin.
1. Bağlantınızı eklemek için **+ Ekle** ' yi seçin. Bağlantı türünü VNet-VNet (başka bir VNet ağ geçidine bağlanılıyorsa) veya Site-site ayarını yansıtacak şekilde ayarlayın.
1. Site-site ile bağlantı kuruyorsanız ve bağlanmak istediğiniz site için henüz bir yerel ağ geçidi oluşturmadıysanız yeni bir tane oluşturabilirsiniz.
1. Kullanmak istediğiniz paylaşılan anahtarı belirtin, ardından bağlantıyı oluşturmak için **Tamam** ' ı seçin.

### <a name="resize-a-gateway-sku"></a><a name="resize"></a>Ağ Geçidi SKU 'sunu yeniden boyutlandırma

Yeniden boyutlandırma ile ilgili belirli kurallar vardır. bir ağ geçidi SKU 'SU değiştiriliyor. Bu bölümde, SKU 'YU yeniden boyutlandıracağız. Daha fazla bilgi için bkz. [Ağ Geçidi ayarları-SKU 'ları yeniden boyutlandırma ve değiştirme](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

### <a name="reset-a-gateway"></a><a name="reset"></a>Ağ geçidini sıfırlama

Bir veya daha fazla Siteden Siteye VPN tünelinde şirketler arası VPN bağlantısını kaybederseniz bir Azure VPN ağ geçidinin sıfırlanması yararlıdır. Bu durumda şirket içi VPN cihazlarınızın tümü düzgün çalışır, ancak Azure VPN ağ geçitleriyle IPsec tünelleri kuramaz.

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="additional-configuration-considerations"></a><a name="additional"></a>Ek yapılandırma konuları

S2S yapılandırması çeşitli yollarla özelleştirilebilir. Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

* BGP hakkında bilgi edinmek için [BGP’ye Genel Bakış](vpn-gateway-bgp-overview.md) ve [BGP’yi yapılandırma](vpn-gateway-bgp-resource-manager-ps.md) makalelerine bakın.
* Zorlamalı tünel oluşturma hakkında bilgi için bkz. [Zorlamalı tünel oluşturma hakkında](vpn-gateway-forced-tunneling-rm.md).
* Yüksek Oranda Kullanılabilir Etkin-Etkin bağlantılar hakkında bilgi için bkz. [Yüksek Oranda Kullanılabilir Şirket İçi ve Dışı ile Sanal Ağdan Sanal Ağa Bağlantı](vpn-gateway-highlyavailable.md).
* Ağ trafiğini bir sanal ağ içindeki kaynaklarla sınırlama hakkında bilgi için bkz. [Ağ Güvenliği](../virtual-network/network-security-groups-overview.md).
* Azure, şirket içi ve İnternet kaynakları arasındaki trafiğin Azure tarafından nasıl yönlendirdiği hakkında bilgi için bkz. [Sanal ağ trafiği yönlendirme](../virtual-network/virtual-networks-udr-overview.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekti veya sonraki öğreticiye gitmek için aşağıdaki adımları kullanarak bu kaynakları silin:

1. Portalın üst kısmındaki **arama** kutusuna kaynak grubunuzun adını girip arama sonuçlarından seçin.

1. **Kaynak grubunu sil**'i seçin.

1. Kaynak **grubunun adını yazıp** **Sil**' i seçerek kaynak grubunuzu girin.

## <a name="next-steps"></a>Sonraki adımlar

Bir S2S bağlantısı yapılandırdıktan sonra, aynı ağ geçidine bir P2S bağlantısı ekleyebilirsiniz.

> [!div class="nextstepaction"]
> [Noktadan siteye VPN bağlantıları](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
