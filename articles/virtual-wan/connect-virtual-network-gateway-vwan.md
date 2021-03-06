---
title: Bir sanal ağ geçidini Azure sanal WAN 'a bağlama
description: Bu makale bir Azure sanal ağ geçidini Azure sanal WAN VPN Gateway 'e bağlamanıza yardımcı olur
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 469d7ba9e86751312ebf6a6c82b35f065ee6cb50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880381"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>VPN Gateway (sanal ağ geçidi) sanal WAN 'a bağlama

Bu makale, bir Azure VPN Gateway (sanal ağ geçidi) ile bir Azure sanal WAN (VPN Gateway) arasında bağlantı kurmanıza yardımcı olur. Bir VPN Gateway (sanal ağ geçidi) ile bir sanal WAN (VPN Gateway) arasında bağlantı oluşturmak, şube VPN sitelerinden sanal bir WAN 'a bağlantı ayarlamaya benzer.

İki özellik arasındaki olası karışıklığı en aza indirmek için, bu ağ geçidini, başvurduğumuz özelliğin adıyla önliyoruz. Örneğin, sanal ağ geçidi VPN Gateway ve sanal WAN VPN Gateway.

## <a name="before-you-begin"></a>Başlamadan önce

Başlamadan önce aşağıdaki kaynakları oluşturun:

Azure Sanal WAN

* [Sanal WAN oluşturun](virtual-wan-site-to-site-portal.md#openvwan).
* [Bir hub oluşturun](virtual-wan-site-to-site-portal.md#hub). Sanal hub, sanal WAN VPN Gateway 'i içerir.

Azure Sanal Ağ

* Sanal ağ geçitleri olmadan bir sanal ağ oluşturun. Şirket içi ağlarınızın alt ağlarının hiçbirinin, bağlanmak istediğiniz sanal ağlarla çakışmadığından emin olun. Azure portal bir sanal ağ oluşturmak için [hızlı](../virtual-network/quick-create-portal.md)başlangıca bakın.

## <a name="1-create-a-vpn-gateway-virtual-network-gateway"></a><a name="vnetgw"></a>1. VPN Gateway sanal ağ geçidi oluşturun

Sanal ağınız için etkin-etkin modda **VPN Gateway** bir sanal ağ geçidi oluşturun. Ağ geçidini oluştururken, ağ geçidinin iki örneği için mevcut genel IP adreslerini kullanabilir veya yeni genel IP 'Ler oluşturabilirsiniz. Sanal WAN sitelerini ayarlarken bu genel IP 'Leri kullanacaksınız. Etkin-etkin VPN ağ geçitleri ve yapılandırma adımları hakkında daha fazla bilgi için bkz. [Active-ACTIVE VPN ağ geçitlerini yapılandırma](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Etkin-etkin mod ayarı

Sanal ağ geçidi **yapılandırması** sayfasında etkin-etkin modunu etkinleştirin.

![etkin-etkin](./media/connect-virtual-network-gateway-vwan/active.png "aktif/aktif")

### <a name="bgp-setting"></a><a name="BGP"></a>BGP ayarı

Sanal ağ geçidi **yapılandırması** SAYFASıNDA **BGP ASN**'yi yapılandırabilirsiniz. BGP ASN 'yi değiştirin. BGP ASN 65515 olamaz. 65515, Azure sanal WAN tarafından kullanılacak.

![Ekran görüntüsü BGP ASN 'yi Yapılandır seçiliyken bir sanal ağ geçidi yapılandırma sayfasını gösterir.](./media/connect-virtual-network-gateway-vwan/bgp.png "b")

### <a name="public-ip-addresses"></a><a name="pip"></a>Genel IP adresleri

Ağ Geçidi oluşturulduğunda, **Özellikler** sayfasına gidin. Özellikler ve yapılandırma ayarları aşağıdaki örneğe benzer olacaktır. Ağ Geçidi için kullanılan iki genel IP adresine dikkat edin.

![özelliklerinin](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. sanal WAN VPN siteleri oluşturma

Sanal WAN VPN siteleri oluşturmak için sanal WAN 'ınızla gezinmek ve **bağlantı** altında **VPN siteleri**' ni seçin. Bu bölümde, önceki bölümde oluşturduğunuz sanal ağ geçitlerine karşılık gelen iki sanal WAN VPN sitesi oluşturacaksınız.

1. **+ Site oluştur**' u seçin.
2. **VPN siteleri oluştur** sayfasında, aşağıdaki değerleri yazın:

   * **Bölge** -Azure VPN Gateway sanal ağ geçidi ile aynı bölge.
   * **Cihaz satıcısı** -cihaz satıcısına (herhangi bir ad) girin.
   * **Özel adres alanı** -BGP etkinleştirildiğinde bir değer girin veya boş bırakın.
   * **Sınır Ağ Geçidi Protokolü** -Azure VPN Gateway sanal ağ geçidinde BGP etkinse **etkinleştirilecek** şekilde ayarlanır.
   * **Hub 'Lara bağlanma** -açılan listeden ön koşullarda oluşturduğunuz hub 'ı seçin. Bir hub görmüyorsanız, hub 'ınız için siteden siteye VPN ağ geçidi oluşturdunuz.
3. **Bağlantılar**' ın altında, aşağıdaki değerleri girin:

   * **Sağlayıcı adı** -bir bağlantı adı ve sağlayıcı adı (herhangi bir ad) girin.
   * **Hız** -hız (herhangi bir sayı).
   * **IP adresi** -IP adresi girin ((VPN Gateway) sanal ağ geçidi özellikleri altında gösterilen Ilk genel IP adresi ile aynı).
   * **BGP adresi** ve **ASN** -BGP adresi ve ASN. Bunlar, BGP eşi IP adreslerinden biri ile aynı olmalıdır ve [1. adımda](#vnetgw)yapılandırdığınız VPN Gateway sanal ağ geçidinden ASN.
4. Siteyi oluşturmak için gözden geçirin ve **Onayla** ' yı seçin.
5. VPN Gateway sanal ağ geçidinin ikinci örneğiyle eşleşecek ikinci siteyi oluşturmak için önceki adımları tekrarlayın. Aynı ayarları, ikinci genel IP adresi ve VPN Gateway yapılandırmasından ikinci BGP eşi IP adresi kullanılması dışında tutacaksınız.
6. Artık iki siteniz başarıyla sağlandı ve yapılandırma dosyalarını indirmek için sonraki bölüme geçebilirler.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. VPN yapılandırma dosyalarını indirin

Bu bölümde, önceki bölümde oluşturduğunuz her bir sitenin VPN yapılandırma dosyasını indirirler.

1. Sanal WAN **VPN siteleri** sayfasının en üstünde, **siteyi** SEÇIN ve **siteden siteye VPN yapılandırmasını indir**' i seçin. Azure, ayarlarla bir yapılandırma dosyası oluşturur.

   !["Siteden siteye VPN yapılandırmasını Indir" eyleminin seçildiği "VPN siteleri" sayfasını gösteren ekran görüntüsü.](./media/connect-virtual-network-gateway-vwan/download.png "indirin")
2. Yapılandırma dosyasını indirip açın.
3. İkinci site için bu adımları tekrarlayın. Yapılandırma dosyalarının her ikisini de açtıktan sonra bir sonraki bölüme geçebilirsiniz.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. yerel ağ geçitleri oluşturun

Bu bölümde, iki Azure VPN Gateway yerel ağ geçidi oluşturursunuz. Önceki adımdaki yapılandırma dosyaları, ağ geçidi yapılandırma ayarlarını içerir. Azure VPN Gateway yerel ağ geçitleri oluşturmak ve yapılandırmak için bu ayarları kullanın.

1. Bu ayarları kullanarak yerel ağ geçidini oluşturun. VPN Gateway yerel ağ geçidi oluşturma hakkında daha fazla bilgi için bkz. [yerel ağ geçidi oluşturma](../vpn-gateway/tutorial-site-to-site-portal.md#LocalNetworkGateway)VPN Gateway makalesi.

   * **IP adresi** -yapılandırma dosyasından *gatewayconfiguration* Için gösterilen örnek0 IP adresini kullanın.
   * **BGP** -bağlantı BGP ÜZERINDEN ise **BGP ayarlarını yapılandır** ' ı seçin ve ' 65515 ' ASN 'yi girin. BGP eşi IP adresini girin. Yapılandırma dosyasından *gatewayconfiguration* Için ' Örnek0 BgpPeeringAddresses ' kullanın.
   * **Abonelik, kaynak grubu ve konum** , sanal WAN hub 'ı ile aynıdır.
2. Yerel ağ geçidini gözden geçirin ve oluşturun. Yerel ağ geçidinizin bu örneğe benzer görünmelidir.

   ![Bir IP adresi vurgulanmış ve "BGP ayarlarını yapılandır" seçiliyken "yapılandırma" sayfasını gösteren ekran görüntüsü.](./media/connect-virtual-network-gateway-vwan/lng1.png "örnek0")
3. Başka bir yerel ağ geçidi oluşturmak için bu adımları yineleyin, ancak bu kez yapılandırma dosyasından ' Örnek0 ' değerleri yerine ' Instance1 ' değerlerini kullanın.

   ![yapılandırma dosyasını indir](./media/connect-virtual-network-gateway-vwan/lng2.png "Instance1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. bağlantı oluştur

Bu bölümde, VPN Gateway yerel ağ geçitleri ve sanal ağ geçidi arasında bir bağlantı oluşturursunuz. VPN Gateway bağlantısı oluşturma adımları için bkz. [bağlantı yapılandırma](../vpn-gateway/tutorial-site-to-site-portal.md#CreateConnection).

1. Portalda, sanal ağ geçidinize gidin ve **Bağlantılar**' a tıklayın. Bağlantılar sayfasının üstündeki **+Ekle**’ye tıklayarak **Bağlantı ekle** sayfasını açın.
2. **Bağlantı ekle** sayfasında, bağlantınız için aşağıdaki değerleri yapılandırın:

   * **Ad:** Bağlantınızı adlandırın.
   * **Bağlantı türü:** **Siteden siteye (IPSec)** seçme
   * **Sanal ağ geçidi:** Bu ağ geçidinden bağlandığınız için değer sabittir.
   * **Yerel ağ geçidi:** Bu bağlantı, sanal ağ geçidini yerel ağ geçidine bağlayacaktır. Daha önce oluşturduğunuz yerel ağ geçitleri arasından birini seçin.
   * **Paylaşılan anahtar:** Paylaşılan bir anahtar girin.
   * **IKE protokolü:** IKE protokolünü seçin.
3. Bağlantınızı oluşturmak için **Tamam**’a tıklayın.
4. Bağlantıyı sanal ağ geçidinin **Bağlantılar** sayfasında görüntüleyebilirsiniz.

   ![Bağlantı](./media/connect-virtual-network-gateway-vwan/connect.png "bağlantı")
5. İkinci bir bağlantı oluşturmak için önceki adımları tekrarlayın. İkinci bağlantı için, oluşturduğunuz diğer yerel ağ geçidini seçin.
6. Bağlantılar BGP üzerinden olursa, bağlantılarınızı oluşturduktan sonra bir bağlantıya gidip **yapılandırma**' yı seçin. **Yapılandırma** sayfasında **BGP** için **etkin**' i seçin. Ardından **Kaydet**' e tıklayın. İkinci bağlantı için tekrarlayın.

## <a name="6-test-connections"></a><a name="test"></a>6. test bağlantıları

VPN Gateway sanal ağ geçidinin yanı sıra bir sanal WAN için bir sanal ağda olmak üzere iki sanal makine oluşturarak bağlantıyı test edebilir ve ardından iki sanal makineye ping yapılır.

1. Azure VPN Gateway (test1-VNG) için sanal ağda (test1-VNet) sanal makine oluşturun. Sanal makineyi GatewaySubnet içinde oluşturmayın.
2. Sanal WAN 'a bağlanmak için başka bir sanal ağ oluşturun. Bu sanal ağın alt ağında bir sanal makine oluşturun. Bu sanal ağ, sanal ağ geçitleri içeremez. [Siteden siteye bağlantı](virtual-wan-site-to-site-portal.md#vnet) makalesindeki PowerShell adımlarını kullanarak hızlı bir şekilde sanal ağ oluşturabilirsiniz. Cmdlet 'leri çalıştırmadan önce değerleri değiştirdiğinizden emin olun.
3. VNet 'i sanal WAN hub 'ına bağlayın. Sanal WAN 'nizin sayfasında, **sanal ağ bağlantıları**' nı ve **+ bağlantı ekle**' yi seçin. **Bağlantı ekle** sayfasında aşağıdaki alanları doldurun:

    * **Bağlantı adı**: Bağlantınıza bir ad verin.
    * **Hub'lar**: Bu bağlantıyla ilişkilendirmek istediğiniz hub'ı seçin.
    * **Abonelik**: Aboneliği doğrulayın.
    * **Sanal ağ**: Bu hub'a bağlamak istediğiniz sanal ağı seçin. Sanal ağda önceden var olan bir sanal ağ geçidi bulunamaz.
4. Sanal ağ bağlantısını oluşturmak için **Tamam** ' ı tıklatın.
5. Bağlantı artık VM 'Ler arasında ayarlanır. Herhangi bir güvenlik duvarı veya iletişimi engelleyen başka ilkeler yoksa, bir VM 'den ping işlemi yapabiliyor olmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Özel bir IPSec ilkesi yapılandırma adımları için bkz. [sanal WAN için özel IPSec Ilkesi yapılandırma](virtual-wan-custom-ipsec-portal.md).
Sanal WAN hakkında daha fazla bilgi için bkz. [Azure sanal WAN](virtual-wan-about.md) ve [Azure sanal WAN hakkında SSS](virtual-wan-faq.md).