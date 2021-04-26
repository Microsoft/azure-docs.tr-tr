---
title: 'VNet-VNet VPN Gateway bağlantısını yapılandırma: Azure portal'
description: Resource Manager ve Azure portalı kullanarak sanal ağlar arasında VPN ağ geçidi bağlantısı oluşturun.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/19/2020
ms.author: cherylmc
ms.openlocfilehash: 465d877da48e0d7027dbba6615302af32c6bb154
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98872409"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Azure portal kullanarak VNet-VNet VPN Gateway bağlantısı yapılandırma

Bu makale, VNet-VNet bağlantı türünü kullanarak sanal ağları (VNet 'ler) bağlamanıza yardımcı olur. Sanal ağlar farklı bölgelerde ve farklı aboneliklerden olabilir. Farklı aboneliklerden VNET 'leri bağladığınızda, aboneliklerin aynı Active Directory kiracısıyla ilişkilendirilmesi gerekmez. 

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet-vnet-diagram.png" alt-text="VNet 'ten VNet 'e diyagramı":::

Bu makaledeki adımlar Azure Resource Manager dağıtım modeli için geçerlidir ve Azure portal kullanır. Aşağıdaki makalelerde açıklanan seçenekleri kullanarak, bu yapılandırmayı farklı bir dağıtım aracı veya modeliyle oluşturabilirsiniz:

> [!div class="op_single_selector"]
> * [Azure portalı](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure portal (klasik)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Farklı dağıtım modellerini bağlama - Azure portalı](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Farklı dağıtım modellerini bağlama - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Sanal ağları bağlama hakkında

Aşağıdaki bölümlerde sanal ağları bağlanmanın farklı yolları açıklanır.

### <a name="vnet-to-vnet"></a>Sanal Ağdan Sanal Ağa

VNet-VNet bağlantısını yapılandırmak, sanal ağları bağlamak için basit bir yoldur. Sanal bir ağı VNet-VNet bağlantı türü (VNet2VNet) ile başka bir sanal ağa bağladığınızda, şirket içi bir konuma siteden siteye IPSec bağlantısı oluşturmaya benzer. Her iki bağlantı türü de IPSec/ıKE ile güvenli bir tünel sağlamak için bir VPN ağ geçidi kullanır ve iletişim kurarken aynı şekilde çalışır. Ancak, yerel ağ geçidinin yapılandırıldığı şekilde farklılık gösterir. 

VNet 'ten VNet 'e bağlantı oluşturduğunuzda, yerel ağ geçidi adres alanı otomatik olarak oluşturulur ve doldurulur. Bir sanal ağın adres alanını güncelleştirirseniz, diğer VNet otomatik olarak güncelleştirilmiş adres alanına yönlendirir. Siteden siteye bağlantısından VNet-VNet bağlantısı oluşturmak genellikle daha hızlı ve kolaydır.

### <a name="site-to-site-ipsec"></a>Siteden Siteye (IPsec)

Karmaşık bir ağ yapılandırmasıyla çalışıyorsanız, sanal ağlarınızı [siteden siteye bağlantı](./tutorial-site-to-site-portal.md) kullanarak bağlamayı tercih edebilirsiniz. Siteden siteye IPSec adımlarını izlediğinizde, yerel ağ geçitlerini kendiniz oluşturup yapılandırırsınız. Her sanal ağa ait yerel ağ geçidi, diğer sanal ağa yerel bir site gibi davranır. Bu adımlar, trafiği yönlendirmek üzere yerel ağ geçidi için ek adres alanları belirtmenize olanak tanır. VNet için adres alanı değişirse, karşılık gelen yerel ağ geçidini el ile güncelleştirmeniz gerekir.

### <a name="vnet-peering"></a>VNet eşlemesi

Sanal ağlarınızı VNet eşlemesi kullanarak da bağlayabilirsiniz. VNet eşlemesi bir VPN ağ geçidi kullanmaz ve farklı kısıtlamalara sahiptir. Ayrıca, [sanal ağ eşleme fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-network), [Sanal Ağlar Arası VPN Gateway fiyatlandırmasından](https://azure.microsoft.com/pricing/details/vpn-gateway) farklı olarak hesaplanır. Daha fazla bilgi için bkz. [VNet eşlemesi](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Neden sanal ağdan sanal ağa bağlantı oluşturmalısınız?

Sanal ağları aşağıdaki nedenlerden dolayı VNet-VNet bağlantısı kullanarak bağlamak isteyebilirsiniz:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Çapraz bölgede coğrafi yedeklilik ve iletişim durumu

* İnternet 'e yönelik uç noktaların üstünden geçmeden, güvenli bağlantı ile kendi Coğrafi çoğaltmayı veya eşitleme 'yi ayarlayabilirsiniz.
* Azure Traffic Manager ve Azure Load Balancer ile, birden fazla Azure bölgesinde coğrafi yedeklilik ile yüksek oranda kullanılabilir iş yükü ayarlayabilirsiniz. Örneğin, birden çok Azure bölgesinde her zaman kullanılabilirlik grupları SQL Server ayarlayabilirsiniz.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Yalıtım veya yönetim sınırlarındaki bölgesel çok katmanlı uygulamalar

* Aynı bölge içinde, yalıtım veya yönetim gereksinimleri nedeniyle birbirine bağlı birden çok sanal ağ içeren çok katmanlı uygulamalar ayarlayabilirsiniz.

Hatta Sanal Ağdan Sanal Ağa iletişim çok siteli yapılandırmalarla bile birleştirilebilir. Bu yapılandırma, aşağıdaki diyagramda gösterildiği gibi şirket içi ağ bağlantısıyla şirketler arası bağlantıyı birleştiren ağ topolojileri ayarlamanıza olanak sağlar:

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections-diagram.png" alt-text="VNet bağlantıları diyagramı":::

Bu makalede VNet-VNet bağlantı türünü kullanarak sanal ağların nasıl bağlanacağı gösterilmektedir. Bu adımları bir alıştırma olarak izlediğinizde, aşağıdaki örnek ayarlar değerlerini kullanabilirsiniz. Örnekte, sanal ağlar aynı abonelikte ancak farklı kaynak gruplarındadır. Sanal ağlarınız farklı aboneliklerdeyse, portalda bağlantı oluşturamazsınız. Bunun yerine [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) veya [CLI](vpn-gateway-howto-vnet-vnet-cli.md) kullanın. VNet-VNet bağlantıları hakkında daha fazla bilgi için bkz. [VNET-VNET hakkında SSS](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Örnek ayarlar

**VNet1 için değerler:**

* **Sanal ağ ayarları**
  * **Ad**: VNet1
  * **Adres alanı**: 10.1.0.0/16
  * **Abonelik**: Kullanmak istediğiniz aboneliği seçin.
  * **Kaynak grubu**: TestRG1
  * **Konum**: Doğu ABD
  * **Alt ağ**
    * **Ad**: ön uç
    * **Adres aralığı**: 10.1.0.0/24

* **Sanal ağ geçidi ayarları**
  * **Ad**: VNet1GW
  * **Kaynak grubu**: Doğu ABD
  * **Oluşturma**: 1. kuşak
  * **Ağ geçidi türü**: **VPN**’i seçin.
  * **VPN türü**: **route * based** öğesini seçin.
  * **SKU**: VpnGw1
  * **Sanal ağ**: VNet1
  * **Ağ geçidi alt ağ adres aralığı**: 10.1.255.0/27
  * **Genel IP adresi**: Yeni oluştur
  * **Genel IP adresi adı**: VNet1GWpip

* **Bağlantı**
  * **Ad**: VNet1toVNet4
  * **Paylaşılan anahtar**: paylaşılan anahtarı kendiniz oluşturabilirsiniz. VNET 'ler arasında bağlantı oluşturduğunuzda değerler eşleşmelidir. Bu alıştırma için abc123 kullanın.

**Ile vnet4 arasında için değerler:**

* **Sanal ağ ayarları**
  * **Ad**: ile vnet4 arasında
  * **Adres alanı**: 10.41.0.0/16
  * **Abonelik**: Kullanmak istediğiniz aboneliği seçin.
  * **Kaynak grubu**: TestRG4
  * **Konum**: Batı ABD
  * **Alt ağ**
  * **Ad**: ön uç
  * **Adres aralığı**: 10.41.0.0/24

* **Sanal ağ geçidi ayarları**
  * **Ad**: VNet4GW
  * **Kaynak grubu**: Batı ABD
  * **Oluşturma**: 1. kuşak
  * **Ağ geçidi türü**: **VPN**’i seçin.
  * **VPN türü**: **Rota tabanlı**' ı seçin.
  * **SKU**: VpnGw1
  * **Sanal ağ**: ile vnet4 arasında
  * **Ağ geçidi alt ağ adres aralığı**: 10.41.255.0/27
  * **Genel IP adresi**: Yeni oluştur
  * **Genel IP adresi adı**: VNet4GWpip

* **Bağlantı**
  * **Ad**: VNet4toVNet1
  * **Paylaşılan anahtar**: paylaşılan anahtarı kendiniz oluşturabilirsiniz. VNET 'ler arasında bağlantı oluşturduğunuzda değerler eşleşmelidir. Bu alıştırma için abc123 kullanın.

## <a name="create-and-configure-vnet1"></a>VNet1 oluşturma ve yapılandırma

Zaten bir sanal ağınız varsa, ayarların VPN ağ geçidi tasarımınızla uyumlu olduğunu doğrulayın. Diğer ağlarla çakışabilecek herhangi bir alt ağ olup olmadığına özellikle dikkat edin. Çakışan alt ağlarınız varsa bağlantınız düzgün çalışmaz.

### <a name="to-create-a-virtual-network"></a>Sanal ağ oluşturmak için

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>VNet1 ağ geçidini oluşturma

Bu adımda sanal ağınız için sanal ağ geçidi oluşturacaksınız. Bir ağ geçidinin oluşturulması, seçili ağ geçidi SKU’suna bağlı olarak 45 dakika veya daha uzun sürebilir. Bu yapılandırmayı bir alıştırma olarak oluşturuyorsanız, bkz. [örnek ayarlar](#example-settings).

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Bir sanal ağ geçidi oluşturmak için

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Ile vnet4 arasında oluşturma ve yapılandırma

VNet1 yapılandırdıktan sonra, önceki adımları tekrarlayarak ve değerleri Ile vnet4 arasında değerleriyle değiştirerek Ile vnet4 arasında ve Ile vnet4 arasında Gateway oluşturun. Ile vnet4 arasında yapılandırmadan önce VNet1 için sanal ağ geçidi oluşturmayı bitirene kadar beklemeniz gerekmez. Kendi değerlerinizi kullanıyorsanız, adres alanlarının bağlanmak istediğiniz VNET 'ler ile çakışmadığından emin olun.

## <a name="configure-the-vnet1-gateway-connection"></a>VNet1 ağ geçidi bağlantısını yapılandırma

Hem VNet1 hem de Ile vnet4 arasında için sanal ağ geçitleri tamamlandığında, sanal ağ geçidi bağlantılarınızı oluşturabilirsiniz. Bu bölümde VNet1 ile VNet4 arasında bir bağlantı oluşturursunuz. Bu adımlar yalnızca aynı abonelikteki sanal ağlar için geçerlidir. Sanal ağlarınız farklı aboneliklerdeyse, bağlantıyı kurmak için [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) kullanmanız gerekir. Ancak sanal ağlarınız aynı abonelikte farklı kaynak gruplarıdır, portalı kullanarak bunları bağlayabilirsiniz.

1. Azure portal, **tüm kaynaklar**' ı seçin, arama kutusuna *sanal ağ geçidi* girin ve ardından VNET 'iniz için sanal ağ geçidine gidin. Örneğin, **VNet1GW**. **Sanal ağ geçidi** sayfasını açmak için ağ geçidini seçin.
1. Ağ Geçidi sayfasında, **Ayarlar->bağlantılar**' a gidin. Ardından **+ Ekle**' yi seçin.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png" alt-text="Bağlantılar sayfası":::
1. **Bağlantı ekle** sayfası açılır.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4.png" alt-text="Bağlantı ekleme":::

   **Bağlantı ekle** sayfasında, bağlantınızın değerlerini girin:

   * **Ad**: bağlantınız için bir ad girin. Örneğin, *VNet1toVNet4*.

   * **Bağlantı türü**: açılan listeden VNet **-VNET** ' i seçin.

   * **İlk sanal ağ geçidi**: Bu bağlantıyı belirtilen sanal ağ geçidinden oluşturduğunuz için, bu alan değeri otomatik olarak doldurulur.

   * **İkinci sanal ağ geçidi**: Bu alan, bağlantı oluşturmak istediğiniz VNET 'in sanal ağ geçidindir. **Sanal ağ geçidi Seç** sayfasını açmak için **başka bir sanal ağ geçidi seçin öğesini** seçin.

      :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/choose.png" alt-text="Ağ geçidi seçme":::

     * Bu sayfada listelenen sanal ağ geçitlerini görüntüleyin. Yalnızca aboneliğinizdeki sanal ağ geçitleri listelenir. Aboneliğinizde olmayan bir sanal ağ geçidine bağlanmak istiyorsanız, [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)kullanın.

     * Bağlanmak istediğiniz sanal ağ geçidini seçin.

   * **Paylaşılan anahtar (PSK)**: Bu alana, bağlantınız için paylaşılan bir anahtar girin. Bu anahtarı kendiniz üretebilir veya oluşturabilirsiniz. Siteden siteye bağlantı içinde, kullandığınız anahtar şirket içi cihazınız ve sanal ağ geçidi bağlantınız için aynıdır. Bu kavram, bir VPN cihazına bağlanmak yerine, başka bir sanal ağ geçidine bağlanmakta olduğunuz durumlar dışında benzerdir.
1. Değişikliklerinizi kaydetmek için **Tamam**’ı seçin.

## <a name="configure-the-vnet4-gateway-connection"></a>Ile vnet4 arasında ağ geçidi bağlantısını yapılandırma

Sonra, Ile vnet4 arasında ile VNet1 arasında bir bağlantı oluşturun. Portalda, Ile vnet4 arasında ile ilişkili sanal ağ geçidini bulun. Ile vnet4 arasında ile VNet1 arasında bağlantı oluşturmak için değerleri değiştirerek önceki bölümdeki adımları izleyin. Aynı paylaşılan anahtarı kullandığınızdan emin olun.

## <a name="verify-your-connections"></a>Bağlantılarınızı doğrulayın

1. Azure portal sanal ağ geçidini bulun. 
1. Sanal **ağ geçidi** sayfasında, sanal ağ geçidinin **Bağlantılar** sayfasını görüntülemek için **Bağlantılar** ' ı seçin. Bağlantı kurulduktan sonra **durum** değerlerinin **bağlı** olarak değiştirilmesini görürsünüz.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png" alt-text="Bağlantıları doğrula":::
1. **Ad** sütununda, daha fazla bilgi görüntülemek için bağlantılardan birini seçin. Veriler akışa başladığında, **Içindeki verilerin** değerlerini ve **dışarı veri** akışını görürsünüz.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png" alt-text="Ekran görüntüsü, içindeki verilerin ve veri çıkış değerlerinin bulunduğu bir kaynak grubunu gösterir":::

## <a name="add-additional-connections"></a>Ek bağlantı ekleme

Ek bağlantılar eklemek istiyorsanız, bağlantıyı oluşturmak istediğiniz sanal ağ geçidine gidin ve **Bağlantılar**' ı seçin. Başka bir VNet-VNet bağlantısı oluşturabilir veya bir şirket içi konum ile IPSec Siteden Siteye bağlantısı oluşturabilirsiniz. **Bağlantı türünü**, oluşturmak istediğiniz bağlantı türüyle eşleşecek şekilde ayarladığınızdan emin olun. Ek bağlantılar oluşturmadan önce, sanal ağınızın adres alanının, bağlanmak istediğiniz adres alanları ile çakışmadığından emin olun. Siteden Siteye bağlantı oluşturma adımları için bkz. [Siteden Siteye bağlantı oluşturma](./tutorial-site-to-site-portal.md).

## <a name="vnet-to-vnet-faq"></a>Sanal Ağdan Sanal Ağa - SSS

Sanal ağlar arası bağlantılar hakkında ek bilgi için SSS sayfasını görüntüleyin.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

* Ağ trafiğini bir sanal ağ içindeki kaynaklarla nasıl sınırlayabilmeniz hakkında bilgi için bkz. [ağ güvenliği](../virtual-network/network-security-groups-overview.md).

* Azure, şirket içi ve İnternet kaynakları arasındaki trafiğin Azure tarafından nasıl yönlendirdiği hakkında bilgi için bkz. [Sanal ağ trafiği yönlendirme](../virtual-network/virtual-networks-udr-overview.md).