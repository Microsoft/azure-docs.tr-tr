---
title: "VPN Gateway için BGP 'yi yapılandırın: Portal"
titleSuffix: Azure VPN Gateway
description: Bu makalede, PowerShell kullanarak Azure VPN Gateway BGP 'yi yapılandırma adımlarında adım adım açıklanmaktadır.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/22/2021
ms.author: yushwang
ms.openlocfilehash: 479a8fac111be6e5b1ae2c6ea21fff801ba26f83
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863590"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>Azure VPN ağ geçitleri üzerinde BGP 'yi yapılandırma

Bu makalede, Azure portal kullanarak bir şirket içi siteden siteye (S2S) VPN bağlantısı ve VNet-VNet bağlantısı üzerinde BGP 'yi etkinleştirme adımları gösterilmektedir.

## <a name="about-bgp"></a><a name="about"></a>BGP hakkında

BGP iki veya daha fazla ağ arasında yönlendirme ve ulaşılabilirlik bilgilerini takas etmek üzere İnternet’te yaygın olarak kullanılan standart yönlendirme protokolüdür. BGP, Azure VPN ağ geçitlerini ve BGP eşleri veya komşuları olarak adlandırılan şirket içi VPN cihazlarınızı, bu öneklerde yer alan ağ geçitlerinden veya yönlendiricilerle iletişim sağlamak için her iki ağ geçidini kullanılabilirlik ve ulaşılabilirlik üzerinde bilgilendirir. BGP ayrıca bir BGP ağ geçidinin öğrendiği yolları bir BGP eşliğinden diğer tüm BGP eşliklerine yayarak birden fazla ağ arasında geçiş yönlendirmesi sağlayabilir.

BGP 'nin avantajları hakkında daha fazla bilgi edinmek ve BGP kullanma hakkında teknik gereksinimleri ve konuları anlamak için bkz. [Azure VPN ağ geçitleri Ile BGP 'ye genel bakış](vpn-gateway-bgp-overview.md).

## <a name="getting-started"></a>Kullanmaya başlama

Bu makalenin her bir bölümü, ağ bağlantınızda BGP 'yi etkinleştirmek için temel bir yapı taşı ayarlamanıza yardımcı olur. Üç parçayı da tamamladıktan sonra, şema 1 ' de gösterildiği gibi topolojiyi derleyebilirsiniz.

**Diyagram 1**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Ağ mimarisini ve ayarlarını gösteren diyagram" border="false":::

Gereksinimlerinizi karşılayan daha karmaşık, çok atlamalı, transit ağı oluşturmak için parçaları birlikte birleştirebilirsiniz.

### <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz olduğunu doğrulayın. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>1. kısım: sanal ağ geçidinde BGP 'yi yapılandırma

Bu bölümde, bir sanal ağ oluşturur ve yapılandırır, BGP parametreleriyle bir sanal ağ geçidi oluşturup yapılandırırsınız ve Azure BGP eş IP adresini elde edersiniz. Diyagram 2, bu bölümdeki adımlarla çalışırken kullanılacak yapılandırma ayarlarını gösterir.

**Diyagram 2**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="Sanal ağ geçidi ayarlarını gösteren diyagram" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1. TestVNet1 oluşturma ve yapılandırma

Bu adımda, TestVNet1 oluşturup yapılandırırsınız. Azure Sanal ağınızı ve VPN ağ geçidinizi oluşturmak ve yapılandırmak için [ağ geçidi oluşturma öğreticisinde](./tutorial-create-gateway-portal.md) bulunan adımları kullanın. Aşağıdaki ekran görüntülerinde başvuru ayarlarını kullanın.

* Sanal ağ:

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="İlgili adres ön ekleri ile TestVNet1":::

* Alt ağlar:

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="TestVNet1 alt ağları":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. BGP parametreleriyle TestVNet1 için VPN Gateway oluşturun

Bu adımda, karşılık gelen BGP parametreleriyle bir VPN ağ geçidi oluşturursunuz.

1. Azure portal Market 'ten **sanal ağ geçidi** kaynağına gidin ve **Oluştur**' u seçin.

1. Parametreleri aşağıda gösterildiği gibi girin:

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="VNG1 oluştur":::

1. Sayfanın vurgulanan **BGP yapılandırma** bölümünde aşağıdaki ayarları yapılandırın:

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="BGP 'yi yapılandırma":::

   *   -  BGP yapılandırma bölümünü göstermek için BGP 'yi **etkin** Yapılandır ' ı seçin.

   * ASN 'nizi (otonom sistem numarası) girin.

   * **Azure APIPA BGP IP adresi** alanı isteğe bağlıdır. Şirket içi VPN cihazlarınız BGP için APIPA adresini kullanıyorsa, VPN için Azure ayrılmış APIPA adres aralığından, **169.254.21.0** ile **169.254.22.255** arasında bir adres seçmeniz gerekir. Bu örnek, 169.254.21.11 kullanır.

   * Etkin-etkin bir VPN ağ geçidi oluşturuyorsanız BGP bölümünde **ikinci bir Ikinci özel Azure APIPA BGP IP adresi** gösterilir. İzin verilen APIPA aralığından (**169.254.21.0** to **169.254.22.255**), başka bir IP adresi seçin. İkinci IP adresi ilk adresten farklı olmalıdır.

   > [!IMPORTANT]
   >
   > * Azure, varsayılan olarak, Azure VPN ağ geçidinde Azure BGP IP adresi olarak otomatik olarak GatewaySubnet önek aralığından özel bir IP adresi atar. Şirket içi VPN cihazlarınız, BGP IP 'si olarak bir APIPA adresi (169.254.0.1-169.254.255.254) kullanırken özel Azure APIPA BGP adresi gereklidir. Karşılık gelen yerel ağ geçidi kaynağı (Şirket içi ağ) BGP eşi IP 'si olarak bir APIPA adresine sahipse Azure VPN Gateway özel APIPA adresini seçer. Yerel ağ geçidi, normal bir IP adresi (APIPA değil) kullanıyorsa Azure VPN Gateway, GatewaySubnet aralığından özel IP adresine döndürülür.
   >
   > * APIPA BGP adresleri, şirket içi VPN cihazları ve tüm bağlı Azure VPN ağ geçitleri arasında çakışmamalıdır.
   >
   > * Azure VPN ağ geçitlerinde APIPA adresleri kullanıldığında, ağ geçitleri, APIPA kaynak IP adresleriyle BGP eşleme oturumları başlatmaz. Şirket içi VPN cihazı BGP eşleme bağlantıları 'nı başlatmalıdır.
   >

1. Doğrulamayı çalıştırmak için **gözden geçir + oluştur** ' u seçin. Doğrulama başarılı olduktan sonra, VPN ağ geçidini dağıtmak için **Oluştur** ' u seçin. Bir ağ geçidinin, tam olarak oluşturulması ve dağıtılması 45 dakika sürebilir. Dağıtım durumunu ağ geçidinizin Genel Bakış sayfasında görebilirsiniz.

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3. Azure BGP eş IP adreslerini alın

Ağ Geçidi oluşturulduktan sonra BGP eşi IP adreslerini Azure VPN Gateway 'de edinebilirsiniz. Bu adresler, şirket içi VPN cihazlarınızı Azure VPN ağ geçidi ile BGP oturumları kuracak şekilde yapılandırmak için gereklidir.

1. Sanal ağ geçidi kaynağına gidin ve aşağıdaki ekran görüntüsünde gösterildiği gibi BGP yapılandırma bilgilerini görmek için **yapılandırma** sayfasını seçin. Bu sayfada, Azure VPN ağ geçidinizdeki tüm BGP yapılandırma bilgilerini, Azure tarafındaki ASN, genel IP adresi ve karşılık gelen BGP eşi IP adresleri (varsayılan ve APIPA) görebilirsiniz.

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="BGP ağ geçidi":::

1. **Yapılandırma** sayfasında, aşağıdaki yapılandırma değişikliklerini yapabilirsiniz:

   * Gerekirse, ASN veya APIPA BGP IP adresini güncelleyebilirsiniz.
   * Etkin-etkin bir VPN ağ geçidiniz varsa, bu sayfada ikinci Azure VPN ağ geçidi örneğinin genel IP adresi, varsayılan ve APIPA BGP IP adresleri gösterilir.

1. Herhangi bir değişiklik yaptıysanız Azure VPN ağ geçidinizdeki değişiklikleri uygulamak için **Kaydet** ' i seçin.

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>Bölüm 2: şirketler arası S2S bağlantılarında BGP 'yi yapılandırma

Şirketler arası bağlantı kurmak için, şirket içi VPN cihazınızı temsil eden bir *yerel ağ geçidi* OLUŞTURMANıZ ve VPN ağ geçidini, [siteden siteye bağlantı oluşturma](./tutorial-site-to-site-portal.md)bölümünde açıklandığı gibi yerel ağ geçidine bağlamak için bir *bağlantı* oluşturmanız gerekir. Bu makale, BGP yapılandırma parametrelerini belirtmek için gereken ek özellikleri içerir.

**Diyagram 3**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="IPSec 'i gösteren diyagram" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1. yerel ağ geçidinde BGP 'yi yapılandırma

Bu adımda, yerel ağ geçidinde BGP 'yi yapılandırırsınız. Örnek olarak aşağıdaki ekran görüntüsünü kullanın. Ekran görüntüsü, 1. diyagramda belirtilen parametrelerle yerel ağ geçidi (site5) gösterir.

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="Yerel ağ geçidi için BGP 'yi yapılandırma":::

#### <a name="important-configuration-considerations"></a>Önemli yapılandırma konuları

* ASN ve BGP eşi IP adresi, şirket içi VPN yönlendirici yapılandırmanızla aynı olmalıdır.
* **Adres alanını** yalnızca BGP kullanıyorsanız bu ağa bağlanmak için boş bırakabilirsiniz. Azure VPN ağ geçidi, BGP eşi IP adresinizin bir yolunu ilgili IPSec tünelinde dahili olarak ekler. Azure VPN ağ geçidi ve bu belirli ağ arasında **BGP kullanmıyorsanız,** **Adres alanı** için geçerli adres ön eklerinin bir listesini sağlamanız **gerekir** .
* İsteğe bağlı olarak, gerekirse şirket içi BGP eş IP 'si olarak bir **APIPA IP adresi** (169.254. x. x) kullanabilirsiniz. Ancak, Azure VPN ağ geçidiniz için bu makalenin önceki kısımlarında açıklandığı gibi bir APIPA IP adresi belirtmeniz gerekecektir, aksi takdirde BGP oturumu bu bağlantı için kurulamaz.
* Yerel ağ geçidinin oluşturulması sırasında BGP yapılandırma bilgilerini girebilir veya yerel ağ geçidi kaynağının **yapılandırma** sayfasından BGP yapılandırması ekleyebilir veya değiştirebilirsiniz.

**Örnek**

Bu örnek, şirket içi BGP eşi IP adresi olarak bir APIPA adresi (169.254.100.1) kullanır:

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="Yerel ağ geçidi APIPA ve BGP":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2. BGP etkin olan bir S2S bağlantısı yapılandırma

Bu adımda, BGP etkin olan yeni bir bağlantı oluşturursunuz. Zaten bir bağlantınız varsa ve üzerinde BGP 'yi etkinleştirmek istiyorsanız, [var olan bir bağlantıyı güncelleştirebilirsiniz](#update).

#### <a name="to-create-a-connection-with-bgp-enabled"></a>BGP özellikli bir bağlantı oluşturmak için

BGP etkinken yeni bir bağlantı oluşturmak için **bağlantı ekle** sayfasında değerleri girin ve BGP 'yi **Etkinleştir** SEÇENEĞINI denetleyip bu bağlantıda BGP 'yi etkinleştirin. Bağlantıyı oluşturmak için **Tamam**'ı seçin.

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="BGP ile IPSec çapraz şirket içi bağlantısı":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>Var olan bir bağlantıyı güncelleştirmek için

Bir bağlantıda BGP seçeneğini değiştirmek istiyorsanız, bağlantı kaynağının **yapılandırma** sayfasına gidin ve ardından aşağıdaki örnekte gösterildiği gibi **BGP** seçeneğini değiştirin. Değişiklikleri kaydetmek için **Kaydet** ' i seçin.

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="Bağlantı için BGP 'yi Güncelleştir":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>3. kısım: VNet 'ten VNet 'e bağlantılar üzerinde BGP 'yi yapılandırma

VNet-VNet bağlantısında BGP 'yi etkinleştirme veya devre dışı bırakma adımları, [Bölüm 2](#crosspremises)' deki S2S adımlarıyla aynıdır. Bağlantıyı oluştururken BGP 'yi etkinleştirebilir veya var olan VNet 'ten VNet 'e bağlantı üzerinde yapılandırmayı güncelleştirebilirsiniz.

>[!NOTE] 
>BGP olmayan VNet-VNet bağlantısı, iletişimi yalnızca iki bağlı sanal ağ ile sınırlandırır. Bu iki sanal ağın diğer S2S veya VNet-VNet bağlantılarına geçiş yönlendirme yeteneği sağlamak için BGP 'yi etkinleştirin.
>

Bağlama **için, TestVNet2** ve TESTVNET1 arasında BGP devre dışı bırakılması durumunda, TestVNet2, şirket içi ağ için yolları öğrenmez ve bu nedenle site 5 ile iletişim kuramazlar. BGP 'yi etkinleştirdikten sonra, diyagram 4 ' te gösterildiği gibi, üç ağ, IPSec ve VNet 'ten VNet 'e bağlantılar üzerinden iletişim kurabiliyor.

**Diyagram 4**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Tam ağı gösteren diyagram" border="false":::

## <a name="next-steps"></a>Sonraki adımlar

Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Adımlar için bkz. [Sanal Makine Oluşturma](../virtual-machines/windows/quick-create-portal.md).