---
title: "Birden çok kimlik doğrulama türü & P2S VPN kullanarak VNet 'e bağlanma: Portal"
titleSuffix: Azure VPN Gateway
description: Azure portal birden çok kimlik doğrulama türünü kullanarak P2S aracılığıyla VNet 'e bağlanın.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: d405f4b10808b7d39c0d116f2c9006c85532b4f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746704"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-multiple-authentication-types-azure-portal"></a>Birden çok kimlik doğrulama türü kullanarak VNet 'e Noktadan siteye VPN bağlantısı yapılandırma: Azure portal

Bu makale Windows, Linux veya macOS çalıştıran ayrı istemcileri bir Azure VNet 'e güvenli bir şekilde bağlamanıza yardımcı olur. Noktadan Siteye VPN bağlantıları, ev veya bir konferans gibi uzak bir noktadan Vnet'inize bağlanmak istediğinizde faydalıdır. Bir sanal ağa bağlanması gereken yalnızca birkaç istemciniz olduğunda Siteden Siteye VPN yerine P2S’yi de kullanabilirsiniz. Noktadan Siteye bağlantılar için bir VPN cihazına veya genel kullanıma yönelik bir IP adresine gerek yoktur. P2S, VPN bağlantısını SSTP (Güvenli Yuva Tünel Protokolü) veya IKEv2 üzerinden oluşturur. Noktadan Siteye VPN hakkında daha fazla bilgi edinmek için bkz. [Noktadan Siteye VPN hakkında](point-to-site-about.md).

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Bir bilgisayardan Azure VNet 'e bağlanma-Noktadan siteye bağlantı diyagramı":::

Noktadan siteye VPN hakkında daha fazla bilgi için bkz. [noktadan sıteye VPN hakkında](point-to-site-about.md). Azure PowerShell kullanarak bu yapılandırmayı oluşturmak için, bkz. [Azure PowerShell kullanarak noktadan sıteye VPN yapılandırma](vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz olduğunu doğrulayın. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial) için kaydolabilirsiniz.

Aynı VPN ağ geçidinde birden çok kimlik doğrulama türü yalnızca OpenVPN tünel türüyle desteklenir.

### <a name="example-values"></a><a name="example"></a>Örnek değerler

Aşağıdaki değerleri kullanarak bir test ortamı oluşturabilir veya bu makaledeki örnekleri daha iyi anlamak için bu değerlere bakabilirsiniz:

* **VNet Adı:** VNet1
* **Adres alanı:** 10.1.0.0/16<br>Bu örnekte yalnızca bir adres alanı kullanılmaktadır. Sanal ağınıza ait birden fazla adres alanı olabilir.
* **Alt ağ adı:** Uçta
* **Alt ağ adres aralığı:** 10.1.0.0/24
* **Abonelik:** Birden fazla aboneliğiniz varsa doğru aboneliği kullandığınızdan emin olun.
* **Kaynak Grubu:** TestRG1
* **Konum:** Doğu ABD
* **Gatewaysubnet:** 10.1.255.0/27<br>
* **Sanal ağ geçidi adı:** VNet1GW
* **Ağ geçidi türü:** VPN
* **VPN türü:** Rota tabanlı
* **Genel IP adresi adı:** VNet1GWpip
* **Bağlantı türü:** Noktadan siteye
* **İstemci adres havuzu:** 172.16.201.0/24<br>Sanal ağa bu Noktadan Siteye bağlantıyı kullanarak bağlanan VPN istemcileri, istemci adres havuzdan bir IP adresi alır.

## <a name="create-a-virtual-network"></a><a name="createvnet"></a>Sanal ağ oluşturma

Başlamadan önce, bir Azure aboneliğiniz olduğunu doğrulayın. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial) için kaydolabilirsiniz.

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Sanal ağ geçidi

Bu adımda sanal ağınız için sanal ağ geçidi oluşturacaksınız. Bir ağ geçidinin oluşturulması, seçili ağ geçidi SKU’suna bağlı olarak 45 dakika veya daha uzun sürebilir.

>[!NOTE]
>Temel ağ geçidi SKU 'SU, OpenVPN tünel türünü desteklemez.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>İstemci adres havuzu

İstemci adres havuzu, belirttiğiniz özel IP adresleri aralığıdır. Noktadan Siteye VPN üzerinden bağlanan istemciler dinamik olarak bu aralıktaki bir IP adresini alır. Bağlantı kurduğunuz şirket içi konum veya bağlanmak istediğiniz sanal ağ ile çakışmayan özel bir IP adresi aralığı kullanın. Birden çok protokol yapılandırırsanız ve SSTP protokollerden biri ise, yapılandırılmış adres havuzu yapılandırılan protokoller arasında eşit olarak bölünür.

1. Sanal ağ geçidi oluşturulduktan sonra sanal ağ geçidi sayfasının **Ayarlar** bölümüne gidin. **Ayarlar**' da, **Noktadan siteye yapılandırma**' yı seçin. Yapılandırma sayfasını açmak için **Şimdi Yapılandır** ' ı seçin.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Noktadan siteye yapılandırma sayfasının ekran görüntüsü." lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. **Noktadan siteye yapılandırma** sayfasında, çeşitli ayarları yapılandırabilirsiniz. **Adres havuzu** kutusuna, kullanmak ISTEDIĞINIZ özel IP adresi aralığını ekleyin. VPN istemcileri, belirttiğiniz aralıktan dinamik olarak bir IP adresi alır. Etkin/etkin yapılandırma için en düşük alt ağ maskesi, etkin/Pasif ve 28 bit için 29 bittir.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/address.jpg" alt-text="Adres havuzunun ekran görüntüsü.":::

1. Kimlik doğrulama ve tünel türlerini yapılandırmak için sonraki bölüme geçin.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Kimlik doğrulama ve tünel türleri

Bu bölümde, kimlik doğrulama türünü ve tünel türünü yapılandırırsınız. **Noktadan siteye yapılandırma** sayfasında, **tünel türü** veya **kimlik doğrulama türü** görmüyorsanız, ağ geçidiniz temel SKU 'yu kullanıyor demektir. Temel SKU, IKEv2 veya RADIUS kimlik doğrulamasını desteklemez. Bu ayarları kullanmak istiyorsanız, farklı bir ağ geçidi SKU 'SU kullanarak ağ geçidini silip yeniden oluşturmanız gerekir.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/multiauth.jpg" alt-text="Kimlik doğrulama türünün ekran görüntüsü.":::

### <a name="tunnel-type"></a><a name="tunneltype"></a>Tünel türü

**Noktadan siteye yapılandırma** sayfasında, tünel türü olarak **OpenVPN (SSL)** öğesini seçin.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Kimlik doğrulaması türü

**Kimlik doğrulama türü** için istenen türleri seçin. Seçenekler şunlardır:

* Azure sertifikası
* RADIUS
* Azure Active Directory

Seçili kimlik doğrulama türlerine bağlı olarak, doldurulması gereken farklı yapılandırma ayarı alanları görürsünüz. Gerekli bilgileri girin ve tüm yapılandırma ayarlarını kaydetmek için sayfanın en üstünde **Kaydet** ' i seçin.

Kimlik doğrulama türü hakkında daha fazla bilgi için bkz.:

* [Azure sertifikası](vpn-gateway-howto-point-to-site-resource-manager-portal.md#type)
* [RADIUS](point-to-site-how-to-radius-ps.md)
* [Azure Active Directory](openvpn-azure-ad-tenant.md)

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>VPN istemcisi yapılandırma paketi

VPN istemcileri istemci yapılandırma ayarları ile yapılandırılmalıdır. VPN istemcisi yapılandırma paketi, bir P2S bağlantısı üzerinden sanal ağa bağlanmak için VPN istemcilerini yapılandırma ayarlarına sahip dosyaları içerir.

VPN istemcisi yapılandırma dosyalarını oluşturma ve yüklemeye ilişkin yönergeler için, yapılandırmanızla ilgili olan makaleyi kullanın:

* [Yerel Azure sertifikası kimlik doğrulaması P2S yapılandırmaları IÇIN VPN istemcisi yapılandırma dosyaları oluşturun ve bunları yükler](point-to-site-vpn-client-configuration-azure-cert.md).
* [Azure Active Directory kimlik doğrulaması: P2S OpenVPN Protokolü bağlantıları için BIR VPN Istemcisi yapılandırın](openvpn-azure-ad-client.md).

## <a name="point-to-site-faq"></a><a name="faq"></a>Noktadan siteye hakkında SSS

Bu bölüm, Noktadan siteye yapılandırmalara yönelik SSS bilgilerini içerir. Ayrıca, VPN Gateway hakkında daha fazla bilgi için [VPN Gateway SSS](vpn-gateway-vpn-faq.md) 'yi de görüntüleyebilirsiniz.

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Daha fazla bilgi için bkz. [Sanal Makineler](../index.yml). Ağ ve sanal makineler hakkında daha fazla bilgi edinmek için, bkz. [Azure ve Linux VM ağına genel bakış](../virtual-machines/network-overview.md).

P2S sorun giderme bilgileri için [Azure noktadan siteye bağlantıları sorununu giderme](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
