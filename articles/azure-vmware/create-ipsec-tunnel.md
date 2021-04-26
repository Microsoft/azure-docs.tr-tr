---
title: Azure VMware çözümüne bir IPSec tüneli oluşturma
description: Azure VMware çözümlerinde VPN (IPSec IKEv1 ve Ikev2) siteden siteye tünel oluşturmayı öğrenin.
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 280ffdd3fec77208d5b49c8e624b7b22bca1daaf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027050"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Azure VMware çözümüne bir IPSec tüneli oluşturma

Bu makalede, Microsoft Azure sanal WAN hub 'ında bir VPN (IPSec IKEv1 ve Ikev2) siteden siteye tünel oluşturma adımlarını inceleyeceğiz. Hub, Azure VMware çözümü ExpressRoute ağ geçidini ve siteden siteye VPN ağ geçidini içerir. Azure VMware Çözüm uç noktası ile şirket içi VPN cihazını bağlar.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="VPN siteden siteye tünel mimarisini gösteren diyagram." border="false":::

Bu şekilde, şunları yapmanız gerekir:
- Bir Azure sanal WAN hub 'ı ve kendisine bağlı bir genel IP adresi olan bir VPN Ağ Geçidi oluşturun. 
- Azure ExpressRoute Ağ Geçidi oluşturun ve bir Azure VMware Çözüm uç noktası kurun. 
- İlke tabanlı bir VPN şirket içi kurulumunu etkinleştirin. 

## <a name="prerequisites"></a>Önkoşullar
Şirket içi VPN cihazında, genel kullanıma yönelik bir IP adresi Sonlandırıcı olması gerekir.

## <a name="step-1-create-an-azure-virtual-wan"></a>Adım 1. Azure sanal WAN oluşturma

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="step-2-create-a-virtual-wan-hub-and-gateway"></a>Adım 2. Sanal WAN hub 'ı ve ağ geçidi oluşturma

>[!TIP]
>Ayrıca, [var olan bir hub 'da ağ geçidi oluşturabilirsiniz](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub).

1. Önceki adımda oluşturduğunuz sanal WAN 'ı seçin.

1. **Sanal hub oluştur**' u seçin, gerekli alanları girin ve ardından **İleri: siteden siteye**' yı seçin. 

   Alt ağı `/24` (minimum) kullanarak girin.

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Sanal hub oluştur sayfasını gösteren ekran görüntüsü.":::

4. **Siteden siteye** ağ geçidi ' ni seçin, **ağ geçidi ölçek birimleri** açılır listesinden toplam aktarım hızını ayarlayarak siteden siteye ağ geçidini tanımlayın. 

   >[!TIP]
   >Ölçek birimleri, her biri 500 Mbps (bir ölçek birimi = 500 Mbps) destekleyen artıklık için çiftler halinde bulunur. 
  
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="Siteden siteye ayrıntılarını gösteren ekran görüntüsü.":::

5. **ExpressRoute** sekmesini seçin, bir ExpressRoute Ağ Geçidi oluşturun. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-er-hub-include/hub2.png" alt-text="ExpressRoute ayarlarının ekran görüntüsü.":::

   >[!TIP]
   >Ölçek birimi değeri 2 GB/sn 'dir. 

    Her hub 'ın oluşturulması yaklaşık 30 dakika sürer. 

## <a name="step-3-create-a-site-to-site-vpn"></a>3. Adım Siteden siteye VPN oluşturma

1. Azure portal, daha önce oluşturduğunuz sanal WAN ' ı seçin.

2. Sanal hub 'a **genel bakış** bölümünde **bağlantı**  >  **VPN (siteden siteye)**  >  **yeni VPN sitesi oluştur**' u seçin.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Sanal hub 'ın VPN (siteden siteye) ile ilgili genel bakış sayfasının ekran görüntüsü ve yeni VPN sitesi oluştur seçilidir.":::  
 
3. **Temel bilgiler** sekmesinde gerekli alanları girin. 

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics2.png" alt-text="Yeni VPN sitesi için temel bilgiler sekmesinin ekran görüntüsü.":::  

   1. **Sınır Ağ Geçidi Protokolü** **etkinleştirilecek** şekilde ayarlayın.  Etkinleştirildiğinde, hem Azure VMware çözümünün hem de şirket içi sunucuların, yollarını tünelden tanıtmasını sağlar. Devre dışı bırakılırsa, tanıtılmak gereken alt ağların el ile saklanması gerekir. Alt ağlar kaçırıldığında, HCX hizmet kafesi oluşturmayacak. Daha fazla bilgi için bkz.  [Azure VPN Gateway Ile BGP hakkında](../vpn-gateway/vpn-gateway-bgp-overview.md).
   
   1. **Özel adres alanı** için ŞIRKET içi CIDR bloğunu girin. Şirket içindeki tüm trafiği tünelde tünele yönlendirmek için kullanılır. CıDR bloğu yalnızca BGP 'yi etkinleştirmezseniz gereklidir.

1. **İleri ' yi** seçin ve gerekli alanları doldurun. Bağlantının ve sağlayıcı adlarının belirtilmesi, hub 'ın bir parçası olarak, sonunda oluşturulabilen ağ geçitleri sayısını ayırt etmenize olanak tanır. BGP ve otonom sistem numarası (ASN), kuruluşunuz içinde benzersiz olmalıdır.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-links.png" alt-text="Bağlantı ayrıntılarını gösteren ekran görüntüsü.":::

1. **Gözden geçir ve oluştur**’u seçin. 

1. İstediğiniz sanal hub 'a gidin ve VPN sitenizi hub 'a bağlamak için **hub ilişkilendirmesi** seçimini kaldırın.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="Önceden paylaşılan anahtar ve ilişkili ayarlarla ilgili sanal HUB için bağlı siteler bölmesini gösteren ekran görüntüsü.":::   

## <a name="step-4-optional-create-policy-based-vpn-site-to-site-tunnels"></a>4. Adım: Seçim İlke tabanlı VPN siteden siteye tüneller oluşturma

>[!IMPORTANT]
>Bu, isteğe bağlı bir adımdır ve yalnızca ilke tabanlı VPN 'Ler için geçerlidir. 

İlke tabanlı VPN kurulumları, hub aralıkları dahil olmak üzere şirket içi ve Azure VMware Çözüm ağlarının belirtilmesini gerektirir.  Bu hub aralıkları, ilke tabanlı VPN tüneli şirket içi uç noktasının şifreleme etki alanını belirtir.  Azure VMware Çözüm tarafı yalnızca ilke tabanlı trafik Seçicisi göstergesinin etkinleştirilmesini gerektirir. 

1. Azure portal, sanal WAN hub sitenize gidin. **Bağlantı** altında **VPN (siteden siteye)** seçeneğini belirleyin.

2. VPN sitenizin adını, en sağdaki üç noktayı (...) seçin ve ardından **VPN bağlantısını bu hub 'a düzenleyin**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Sanal WAN hub sitesi için Azure 'daki sayfanın ekran görüntüsü, bu hub 'a yönelik VPN bağlantısını Düzenle ' ye erişmek için seçilen bir üç nokta gösterir." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. VPN sitesi ile hub arasındaki bağlantıyı düzenleyin ve ardından **Kaydet**' i seçin.
   - Internet Protokolü güvenliği (IPSec), **özel**' i seçin.
   - İlke tabanlı trafik seçiciyi kullanın, **Etkinleştir** ' i seçin.
   - **Ike Aşama 1** ve **Ike aşama 2 (IPSec)** ayrıntılarını belirtin. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="VPN bağlantısını Düzenle sayfasının ekran görüntüsü."::: 
 
   İlke tabanlı şifreleme etki alanının parçası olan trafik seçicileri veya alt ağlarınız şunlardır:
    
   - Sanal WAN hub 'ı `/24`
   - Azure VMware çözümü özel bulutu `/22`
   - Bağlı Azure sanal ağı (varsa)

## <a name="step-5-connect-your-vpn-site-to-the-hub"></a>5. Adım. VPN sitenizi hub 'a bağlama

1. VPN sitenizin adını seçin ve ardından **VPN sitelerini bağla**' yı seçin. 

1. **Önceden paylaşılan anahtar** alanına, daha önce şirket içi uç nokta için tanımlanan anahtarı girin. 

   >[!TIP]
   >Daha önce tanımlı bir anahtarınız yoksa, bu alanı boş bırakabilirsiniz. Sizin için otomatik olarak bir anahtar oluşturulur. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Önceden paylaşılan anahtar ve ilişkili ayarlarla ilgili sanal HUB için bağlı siteler bölmesini gösteren ekran görüntüsü. "::: 

1. Hub 'da bir güvenlik duvarı dağıtıyorsanız ve sonraki atlamada, **varsayılan yolu yay** seçeneğini **Etkinleştir** olarak ayarlayın. 

   Etkinleştirildiğinde, sanal WAN hub 'ı yalnızca Hub 'da bir güvenlik duvarı dağıtıldığında ya da başka bir bağlı sitede Zorlamalı tünel etkin olduğunda bir bağlantı olarak zaten öğrenildiği zaman bir bağlantıyı yayar. Varsayılan yol, sanal WAN hub 'ında değil.  

1. **Bağlan**’ı seçin. Birkaç dakika sonra, site bağlantı ve bağlantı durumunu gösterir.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="Siteden siteye bağlantı ve bağlantı durumunu gösteren ekran görüntüsü." lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

1. Şirket içi uç nokta için [VPN yapılandırma dosyasını indirin](../virtual-wan/virtual-wan-site-to-site-portal.md#device) .  

3. Sanal WAN hub 'ında Azure VMware çözümü ExpressRoute 'a yama yapın. 

   >[!IMPORTANT]
   >Platforma yama yapabilmeniz için önce bir özel bulutun oluşturulması gerekir. 

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Azure VMware çözümünü ve VPN ağ geçidini sanal WAN hub 'ında birbirine bağlayın. Önceki adımda yetkilendirme anahtarını ve ExpressRoute KIMLIĞINI (eş devre URI 'SI) kullanacaksınız.

   1. ExpressRoute ağ geçidinizi seçip kullan **Yetkilendirme anahtarı**' nı seçin.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Kullan yetkilendirme anahtarı seçiliyken özel bulutun ExpressRoute sayfasının ekran görüntüsü.":::

   1. Yetkilendirme anahtarını **Yetkilendirme anahtarı** alanına yapıştırın.
   1. ExpressRoute KIMLIĞINI **eş devre URI 'si** alanına yapıştırın. 
   1. **Bu ExpressRoute devresini otomatik olarak hub ile ilişkilendir** onay kutusunu seçin. 
   1. Bağlantıyı kurmak için **Ekle** ' yi seçin. 

5. [NSX-T segmenti oluşturup](./tutorial-nsx-t-network-segment.md) ağ ÜZERINDE bir VM sağlayarak bağlantınızı test edin. Hem şirket içi hem de Azure VMware Çözüm uç noktalarına ping gönderin.
