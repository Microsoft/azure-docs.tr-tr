---
title: 'Öğretici: Azure portal kullanarak Azure Güvenlik duvarını karma ağda dağıtma ve yapılandırma'
description: Bu öğreticide, Azure portal kullanarak Azure Güvenlik Duvarı 'Nı dağıtmayı ve yapılandırmayı öğreneceksiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 86e27c190b269763d8dd2f562a207b3f2020da29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98051080"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak Azure Güvenlik duvarını karma ağda dağıtma ve yapılandırma

Karma ağ oluşturmak için şirket içi ağınızı bir Azure sanal ağına bağladığınızda, Azure ağ kaynaklarınıza erişimi denetleme özelliği, genel bir güvenlik planının önemli bir parçasıdır.

Azure Güvenlik Duvarı'nı kullanarak izin verilen ve reddedilen ağ trafiğini tanımlayan kurallar sayesinde hibrit ağdaki ağ erişimini denetleyebilirsiniz.

Bu öğretici için üç sanal ağ oluşturursunuz:

- **VNET-hub** -güvenlik duvarı bu sanal ağda.
- **VNET-kol** -bağlı bileşen sanal ağı, Azure üzerinde bulunan iş yükünü temsil eder.
- **VNET-Onpred** -şirket içi sanal ağ, bir şirket içi ağı temsil eder. Gerçek bir dağıtımda, bir VPN veya ExpressRoute bağlantısı ile bağlanabilir. Kolaylık olması için, bu öğretici bir VPN Ağ Geçidi bağlantısı kullanır ve şirket içi ağı temsil etmek için Azure 'da bulunan bir sanal ağ kullanılır.

![Hibrit ağda güvenlik duvarı](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Değişkenleri tanımlama
> * Güvenlik Duvarı hub 'ı sanal ağını oluşturma
> * Bağlı bileşen sanal ağını oluşturma
> * Şirket içi sanal ağı oluşturma
> * Güvenlik duvarını yapılandırma ve dağıtma
> * VPN ağ geçitlerini oluşturma ve bağlama
> * Hub ve bağlı bileşen sanal ağlarını eşler
> * Yolları oluşturma
> * Sanal makineleri oluşturma
> * Güvenlik duvarını test etme

Bu yordamı gerçekleştirmek için Azure PowerShell kullanmak istiyorsanız, bkz. [Azure PowerShell kullanarak Azure Güvenlik duvarını karma ağda dağıtma ve yapılandırma](tutorial-hybrid-ps.md).

## <a name="prerequisites"></a>Önkoşullar

Karma ağ, Azure sanal ağları ile şirket içi ağlar arasında trafiği yönlendirmek için hub ve bağlı bileşen mimarisi modelini kullanır. Hub ve bağlı bileşen mimarisi aşağıdaki gereksinimlere sahiptir:

- VNet 'e bağlı VNet-Hub eşleme yaparken **Allowgatewaytransit** ayarlayın. Hub ve bağlı bileşen ağ mimarisinde, bir ağ geçidi geçişi, bağlı olan sanal ağların her bağlı olan sanal ağda VPN ağ geçitleri dağıtmak yerine hub 'da VPN ağ geçidini paylaşmasına izin verir. 

   Ayrıca, ağ geçidi ile bağlantılı sanal ağlara veya şirket içi ağlara yapılan yollar, ağ geçidi geçişi kullanılarak eşlenen sanal ağlar için otomatik olarak yönlendirme tablolarına yayılır. Daha fazla bilgi için bkz. [sanal ağ eşlemesi IÇIN VPN ağ geçidi geçişi yapılandırma](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- VNet hub 'ına VNet-Spoke eşdüzey olduğunuzda **Useremotegateway** 'leri ayarlayın. **Useremotegateway** 'ler ayarlandıysa ve uzaktan eşleme üzerinde **allowgatewaytransit** da ayarlandıysa, bağlı olan sanal ağ, aktarım için uzak sanal ağın ağ geçitlerini kullanır.
- Alt ağ trafiğini hub güvenlik duvarıyla yönlendirmek için, **sanal ağ geçidi yol yayma** seçeneği devre dışı olan güvenlik duvarını işaret eden bir Kullanıcı tanımlı yol (UDR) kullanabilirsiniz. **Sanal ağ geçidi yol yayma** devre dışı seçeneği, bağlı olan alt ağlara yönlendirme dağıtımını önler. Bu, öğrenilen yolların UDR 'niz ile çakışmasını önler. **Sanal ağ geçidi yol yaymayı** devre dışı bırakmak ISTIYORSANıZ, BGP üzerinden şirket içinden yayımlananlar üzerine yazmak için güvenlik duvarına belirli yollar tanımladığınızdan emin olun.
- Hub ağ geçidi alt ağında, bağlı olan ağların sonraki atlaması olarak güvenlik duvarı IP adresini işaret eden bir UDR yapılandırın. Azure Güvenlik Duvarı alt ağında BGP 'deki yolları öğrendiğinden dolayı UDR gerekmez.

Bu yolların nasıl oluşturulduğunu görmek için [Yolları Oluşturma](#create-the-routes) bölümüne bakın.

>[!NOTE]
>Azure Güvenlik Duvarı internete bağlı olmalıdır. AzureFirewallSubnet, BGP aracılığıyla şirket içi ağınıza varsayılan bir yol öğrenirse, doğrudan Internet bağlantısını sürdürmek için **Nexthoptype** değeri **Internet** olarak ayarlanmış bir 0.0.0.0/0 UDR ile geçersiz kılmanız gerekir.
>
>Azure Güvenlik Duvarı, zorlamalı tüneli destekleyecek şekilde yapılandırılabilir. Daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Zorlamalı tünel](forced-tunneling.md).

>[!NOTE]
>Doğrudan eşlenmiş sanal ağlar arasındaki trafik, bir UDR varsayılan ağ geçidi olarak Azure Güvenlik Duvarı 'na işaret ediyorsa doğrudan yönlendirilir. Alt ağ trafiğine Bu senaryodaki güvenlik duvarının alt ağını göndermek için, her iki alt ağda de bir UDR 'nin hedef alt ağ önekini açıkça içermesi gerekir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-the-firewall-hub-virtual-network"></a>Güvenlik Duvarı hub 'ı sanal ağını oluşturma

İlk olarak, Bu öğreticinin kaynaklarını içerecek kaynak grubunu oluşturun:

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Azure Portal giriş sayfasında **kaynak grupları**  >  **Ekle**' yi seçin.
3. **Abonelik** bölümünde aboneliğinizi seçin.
1. **Kaynak grubu adı** Için, **FW-karma-test** yazın.
2. **Bölge** Için **(US) Doğu ABD** seçin. Daha sonra oluşturduğunuz tüm kaynakların aynı konumda olması gerekir.
3. **Gözden geçir + Oluştur**’u seçin.
4. **Oluştur**’u seçin.

Şimdi VNet 'i oluşturun:

> [!NOTE]
> AzureFirewallSubnet alt ağının boyutu/26 ' dır. Alt ağ boyutu hakkında daha fazla bilgi için bkz. [Azure Güvenlik DUVARı SSS](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. **Ağ** altında **sanal ağ**' ı seçin.
1. **Oluştur**’u seçin.
1. **Kaynak grubu** Için, **FW-karma-test**' i seçin.
1. **Ad** için **VNET-hub** yazın.
1. **İleri ' yi seçin: IP adresleri**.
1. **IPv4 adres alanı** için varsayılan adresi silin ve **10.5.0.0/16** yazın.
1. **Alt ağ adı** altında **alt ağ ekle**' yi seçin.
1. **Alt ağ adı** türü için **AzureFirewallSubnet**. Güvenlik duvarı bu alt ağda yer alacaktır ve alt ağ adının **mutlaka** AzureFirewallSubnet olması gerekir.
1. **Alt ağ adres aralığı** için **10.5.0.0/26** yazın. 
1. **Add (Ekle)** seçeneğini belirleyin.
1. **Gözden geçir ve oluştur**’u seçin.
1. **Oluştur**’u seçin.

## <a name="create-the-spoke-virtual-network"></a>Bağlı bileşen sanal ağını oluşturma

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. **Ağ** bölümünde **sanal ağ**' ı seçin.
7. **Kaynak grubu** Için, **FW-karma-test**' i seçin.
1. **Ad** için **VNET-kol** yazın.
2. **Bölge** Için **(US) Doğu ABD** seçin.
3. **İleri ' yi seçin: IP adresleri**.
4. **IPv4 adres alanı** için varsayılan adresi silin ve **10.6.0.0/16** yazın.
6. **Alt ağ adı** altında **alt ağ ekle**' yi seçin.
7. **Alt ağ adı** için **sn-iş yükü**.
8. **Alt ağ adres aralığı** için **10.6.0.0/24** yazın. 
9. **Add (Ekle)** seçeneğini belirleyin.
10. **Gözden geçir ve oluştur**’u seçin.
11. **Oluştur**’u seçin.

## <a name="create-the-on-premises-virtual-network"></a>Şirket içi sanal ağı oluşturma

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. **Ağ** bölümünde **sanal ağ**' ı seçin.
7. **Kaynak grubu** Için, **FW-karma-test**' i seçin.
1. **Ad** için **VNET-onpred** yazın.
2. **Bölge** Için **(US) Doğu ABD** seçin.
3. **İleri ' yi seçin: IP adresleri**
4. **IPv4 adres alanı** için varsayılan adresi silin ve **192.168.0.0/16** yazın.
5. **Alt ağ adı** altında **alt ağ ekle**' yi seçin.
7. **Alt ağ adı** için **sn-Corp**.
8. **Alt ağ adres aralığı** için **192.168.1.0/24** yazın. 
9. **Add (Ekle)** seçeneğini belirleyin.
10. **Gözden geçir ve oluştur**’u seçin.
11. **Oluştur**’u seçin.

Şimdi ağ geçidi için ikinci bir alt ağ oluşturun.

1. **VNET-Onpreb** sayfasında **alt ağlar**' ı seçin.
2. **+ Alt ağ**' ı seçin.
3. **Ad** Için **gatewaysubnet** yazın.
4. **Alt ağ adres aralığı** için **192.168.2.0/24** yazın.
5. **Tamam**’ı seçin.

## <a name="configure-and-deploy-the-firewall"></a>Güvenlik duvarını yapılandırma ve dağıtma

Şimdi güvenlik duvarını güvenlik duvarı hub 'ı sanal ağına dağıtın.

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. Sol sütunda **ağ iletişimi**' ni seçin ve sonra **güvenlik duvarı**' nı seçin.
4. **Güvenlik duvarı oluştur** sayfasında aşağıdaki ayarları kullanarak güvenlik duvarını yapılandırın:

   |Ayar  |Değer  |
   |---------|---------|
   |Abonelik     |\<your subscription\>|
   |Kaynak grubu     |**FW-karma-test** |
   |Name     |**AzFW01**|
   |Region     |**Doğu ABD**|
   |Bir sanal ağ seçin     |**Mevcut olanı kullan**:<br> **VNet-hub**|
   |Genel IP adresi     |Yeni ekle: <br>**FW-PIP**. |

5. **Gözden geçir ve oluştur**’u seçin.
6. Özeti gözden geçirin ve ardından güvenlik duvarını oluşturmak için **Oluştur** ' u seçin.

   Bu, dağıtılması birkaç dakika sürer.
7. Dağıtım tamamlandıktan sonra, **FW-karma-test** kaynak grubuna gidin ve **AzFW01** güvenlik duvarını seçin.
8. Özel IP adresini not edin. Varsayılan rotayı oluştururken bu adresi kullanacaksınız.

### <a name="configure-network-rules"></a>Ağ kurallarını yapılandırma

İlk olarak, Web trafiğine izin vermek için bir ağ kuralı ekleyin.

1. **AzFW01** sayfasında **kurallar**' ı seçin.
2. **Ağ kuralı koleksiyonu** sekmesini seçin.
3. **Ağ kuralı koleksiyonu Ekle**' yi seçin.
4. **Ad** için **RCNet01** yazın.
5. **Öncelik** için **100** yazın.
6. **Eylem** alanında **İzin ver**'i seçin.
6. **Kurallar**' ın altında, **ad** için **allowweb** yazın.
7. **Protokol** alanında **TCP**'yi seçin.
8. **Kaynak türü** için **IP adresi**' ni seçin.
9. **Kaynak** için **192.168.1.0/24** yazın.
10. **Hedef türü** için **IP adresi**' ni seçin.
11. **Hedef adres** için **10.6.0.0/16** yazın
12. **Hedef bağlantı noktaları** için **80** yazın.

Şimdi RDP trafiğine izin vermek için bir kural ekleyin.

İkinci kural satırına aşağıdaki bilgileri yazın:

1. **Ad**, **allowrdp** yazın.
2. **Protokol** alanında **TCP**'yi seçin.
3. **Kaynak türü** için **IP adresi**' ni seçin.
4. **Kaynak** için **192.168.1.0/24** yazın.
5. **Hedef türü** için **IP adresi**' ni seçin.
6. **Hedef adres** için **10.6.0.0/16** yazın
7. **Hedef bağlantı noktaları** için **3389** yazın.
8. **Add (Ekle)** seçeneğini belirleyin.

## <a name="create-and-connect-the-vpn-gateways"></a>VPN ağ geçitlerini oluşturma ve bağlama

Hub ve şirket içi sanal ağlar VPN ağ geçitleri aracılığıyla bağlanır.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Hub sanal ağı için bir VPN ağ geçidi oluşturma

Şimdi hub sanal ağı için VPN ağ geçidini oluşturun. Ağdan ağa yapılandırma, RouteBased bir VpnType gerektirir. VPN ağ geçidinin oluşturulması, seçili VPN ağ geçidi SKU’suna bağlı olarak 45 dakika veya daha uzun sürebilir.

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. Arama metin kutusuna **sanal ağ geçidi** yazın.
3. **Sanal ağ geçidi**' ni seçin ve **Oluştur**' u seçin.
4. **Ad** için **GW-hub** yazın.
5. **Bölge** için, daha önce kullandığınız bölgeyi seçin.
6. **Ağ geçidi türü** için **VPN**' yi seçin.
7. **VPN türü** için **Rota tabanlı**' ı seçin.
8. **SKU** için **temel**' yı seçin.
9. **Sanal ağ** için **VNET hub**' ı seçin.
10. **Genel IP adresi** Için **Yeni oluştur**' u seçin ve ad için **VNET-hub-GW-PI** yazın.
11. Kalan Varsayılanları kabul edin ve ardından **gözden geçir + oluştur**' u seçin.
12. Yapılandırmayı gözden geçirin ve **Oluştur**' u seçin.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Şirket içi sanal ağ için bir VPN ağ geçidi oluşturma

Şimdi, şirket içi sanal ağ için VPN ağ geçidini oluşturun. Ağdan ağa yapılandırma, RouteBased bir VpnType gerektirir. VPN ağ geçidinin oluşturulması, seçili VPN ağ geçidi SKU’suna bağlı olarak 45 dakika veya daha uzun sürebilir.

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. Arama metin kutusuna **sanal ağ geçidi** yazın ve **ENTER** tuşuna basın.
3. **Sanal ağ geçidi**' ni seçin ve **Oluştur**' u seçin.
4. **Ad** için **GW-onpred** yazın.
5. **Bölge** için, daha önce kullandığınız bölgeyi seçin.
6. **Ağ geçidi türü** için **VPN**' yi seçin.
7. **VPN türü** için **Rota tabanlı**' ı seçin.
8. **SKU** için **temel**' yı seçin.
9. **Sanal ağ** için **VNET-onpred** öğesini seçin.
10. **Genel IP adresi** Için **Yeni oluştur**' u seçin ve ad Için **VNET-onpren-GW-PI** yazın.
11. Kalan Varsayılanları kabul edin ve ardından **gözden geçir + oluştur**' u seçin.
12. Yapılandırmayı gözden geçirin ve **Oluştur**' u seçin.

### <a name="create-the-vpn-connections"></a>VPN bağlantılarını oluşturma

Artık hub ve şirket içi ağ geçitleri arasında VPN bağlantıları oluşturabilirsiniz.

Bu adımda, hub sanal ağından şirket içi sanal ağa bağlantı oluşturursunuz. Örneklerde paylaşılan bir anahtar göreceksiniz. Paylaşılan anahtar için kendi değerlerinizi kullanabilirsiniz. Paylaşılan anahtarın her iki bağlantıyla da eşleşiyor olması önemlidir. Bir bağlantı oluşturmak çok zaman almaz.

1. **İlt-hibrit-test** kaynak grubunu açın ve **GW-hub** ağ geçidini seçin.
2. Sol sütundaki **Bağlantılar** ' ı seçin.
3. **Add (Ekle)** seçeneğini belirleyin.
4. Bağlantı adı, **hub-Onpree** yazın.
5. **Bağlantı türü** için **VNET-VNET** ' i seçin.
6. **İkinci sanal ağ geçidi** için **GW-onpred** öğesini seçin.
7. **Paylaşılan anahtar (PSK)** için **AzureA1b2C3** yazın.
8. **Tamam**’ı seçin.

Şirket içinden hub 'a sanal ağ bağlantısını oluşturun. Bu adım öncekiyle benzerdir, çünkü VNet-Onprem sanal ağdan VNet hub 'ına bağlantı oluşturmanız gerekir. Paylaşılan anahtarların eşleştiğinden emin olun. Bağlantı birkaç dakika içerisinde kurulacaktır.

1. ILT- **hibrit-test** kaynak grubunu açın ve **GW-onprea** Gateway ' i seçin.
2. Sol sütundaki **Bağlantılar** ' ı seçin.
3. **Add (Ekle)** seçeneğini belirleyin.
4. Bağlantı adı için **Onpree-hub**' ı yazın.
5. **Bağlantı türü** için **VNET-VNET** ' i seçin.
6. **İkinci sanal ağ geçidi** için **GW-hub**' ı seçin.
7. **Paylaşılan anahtar (PSK)** için **AzureA1b2C3** yazın.
8. **Tamam**’ı seçin.


#### <a name="verify-the-connection"></a>Bağlantıyı doğrulama

Yaklaşık beş dakika sonra, her iki bağlantının da durumu **bağlanmalıdır**.

![Ağ geçidi bağlantıları](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Hub ve bağlı bileşen sanal ağlarını eşler

Artık hub ve bağlı bileşen sanal ağlarını eşler.

1. **İlt-hibrit-test** kaynak grubunu açın ve **VNET hub** sanal ağını seçin.
2. Sol sütunda, eşlemeler ' i **seçin.**
3. **Add (Ekle)** seçeneğini belirleyin.
4. **Bu sanal ağ** altında:
 
   
   |Ayar adı  |Değer  |
   |---------|---------|
   |Eşleme bağlantı adı| Hubtoteli|
   |Uzak sanal ağa trafik|   İzin ver (varsayılan)      |
   |Uzak sanal ağdan iletilen trafik    |   İzin ver (varsayılan)      |
   |Sanal ağ geçidi     |  Bu sanal ağın ağ geçidini kullan       |
    
5. **Uzak sanal ağ** altında:

   |Ayar adı  |Değer  |
   |---------|---------|
   |Eşleme bağlantı adı | SpoketoHub|
   |Sanal ağ dağıtım modeli| Resource Manager|
   |Abonelik|\<your subscription\>|
   |Sanal ağ| VNet-Spoke
   |Uzak sanal ağa trafik     |   İzin ver (varsayılan)      |
   |Uzak sanal ağdan iletilen trafik    |   İzin ver (varsayılan)      |
   |Sanal ağ geçidi     |  Uzak sanal ağın ağ geçidini kullan       |

5. **Add (Ekle)** seçeneğini belirleyin.

   :::image type="content" source="media/tutorial-hybrid-portal/firewall-peering.png" alt-text="VNET eşlemesi":::

## <a name="create-the-routes"></a>Yolları oluşturma

Şimdi birkaç yol oluşturun:

- Güvenlik duvarı IP adresi üzerinden hub ağ geçidi alt ağından uç alt ağına giden bir yol
- Güvenlik duvarı IP adresi üzerinden uç alt ağından gelen varsayılan yol

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. Arama metin kutusuna **yol tablosu** yazın ve **ENTER** tuşuna basın.
3. **Yol tablosu**' nu seçin.
4. **Oluştur**’u seçin.
6. Kaynak grubu için **FW-karma-test** ' i seçin.
8. **Bölge** için, daha önce kullandığınız konumu seçin.
1. Ad için **UDR-hub-kol** yazın.
9. **Gözden geçir + Oluştur**’u seçin.
10. **Oluştur**’u seçin.
11. Yol tablosu oluşturulduktan sonra, yol tablosu sayfasını açmak için seçin.
12. Sol sütundaki **rotalar** ' ı seçin.
13. **Add (Ekle)** seçeneğini belirleyin.
14. Yol adı için, uzak **bileşene** yazın.
15. Adres ön eki için **10.6.0.0/16** yazın.
16. Sonraki atlama türü için **Sanal Gereç**' ı seçin.
17. Sonraki atlama adresi için, daha önce not ettiğiniz güvenlik duvarının özel IP adresini yazın.
18. **Tamam**’ı seçin.

Şimdi rota alt ağıyla ilişkilendirin.

1. **UDR-hub-bağlı bileşen-yollar** sayfasında, **alt ağlar**' ı seçin.
2. **İlişkilendir**' i seçin.
3. **Sanal ağ** altında **VNET-hub**' ı seçin.
1. **Alt ağ** altında **gatewaysubnet**' i seçin.
2. **Tamam**’ı seçin.

Şimdi, bağlı olan alt ağdan varsayılan yolu oluşturun.

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. Arama metin kutusuna **yol tablosu** yazın ve **ENTER** tuşuna basın.
3. **Yol tablosu**' nu seçin.
5. **Oluştur**’u seçin.
7. Kaynak grubu için **FW-karma-test** ' i seçin.
8. **Bölge** için, daha önce kullandığınız konumu seçin.
1. Ad için **UDR-DG** yazın.
4. **Ağ geçidi yolunu yayma** için **Hayır**' ı seçin.
5. **Gözden geçir + Oluştur**’u seçin.
6. **Oluştur**’u seçin.
7. Yol tablosu oluşturulduktan sonra, yol tablosu sayfasını açmak için seçin.
8. Sol sütundaki **rotalar** ' ı seçin.
9. **Add (Ekle)** seçeneğini belirleyin.
10. Yol adı için, **Tohub** yazın.
11. Adres ön eki için **0.0.0.0/0** yazın.
12. Sonraki atlama türü için **Sanal Gereç**' ı seçin.
13. Sonraki atlama adresi için, daha önce not ettiğiniz güvenlik duvarının özel IP adresini yazın.
14. **Tamam**’ı seçin.

Şimdi rota alt ağıyla ilişkilendirin.

1. **UDR-DG-Routes** sayfasında **alt ağlar**' ı seçin.
2. **İlişkilendir**' i seçin.
3. **Sanal ağ** altında, **VNET-bağlı bileşen**' i seçin.
1. **Alt ağ** altında **sn-iş yükü** öğesini seçin.
2. **Tamam**’ı seçin.

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Artık bağlı olan iş yükünü ve şirket içi sanal makineleri oluşturun ve uygun alt ağlara yerleştirin.

### <a name="create-the-workload-virtual-machine"></a>İş yükü sanal makinesi oluşturma

Genel IP adresi olmadan IIS çalıştıran, bağlı olan sanal ağda bir sanal makine oluşturun.

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. **Popüler** bölümünde **Windows Server 2016 Datacenter**' u seçin.
3. Sanal makine için şu değerleri girin:
    - **Kaynak grubu** - **ILT-hibrit-test**' i seçin.
    - **Sanal makine adı**: *VM-ışınsal-01*.
    - **Bölge** -daha önce kullandığınız bölge.
    - **Kullanıcı adı**: \<type a user name\> .
    - **Parola**: \<type a password\>
4. **Ortak gelen bağlantı noktaları** için **Seçili bağlantı noktalarına izin ver**' i seçin ve ardından **http (80)** ve **RDP (3389)** öğesini seçin.
4. **İleri ' yi seçin: diskler**.
5. Varsayılanları kabul edin ve **İleri ' yi seçin: ağ**.
6. Sanal ağ için **VNET-ışınsal** ve alt ağ **sn-iş yükü**' ı seçin.
7. **Genel IP** için **hiçbiri**' ni seçin. 
9. Ileri 'yi seçin **: yönetim**.
10. **Önyükleme tanılaması** Için **devre dışı bırak**' ı seçin.
11. **Gözden geçir + oluştur**' u seçin, Özet sayfasında ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

### <a name="install-iis"></a>IIS yükleme

1. Azure portal, Cloud Shell açın ve **PowerShell** olarak ayarlandığından emin olun.
2. Sanal makineye IIS yüklemek ve gerekirse konumu değiştirmek için aşağıdaki komutu çalıştırın:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>Şirket içi sanal makine oluşturma

Bu, Uzak Masaüstü kullanarak genel IP adresine bağlanmak için kullandığınız bir sanal makinedir. Buradan, daha sonra güvenlik duvarı aracılığıyla şirket içi sunucuya bağlanırsınız.

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. **Popüler** bölümünde **Windows Server 2016 Datacenter**' u seçin.
3. Sanal makine için şu değerleri girin:
    - **Kaynak grubu** -mevcut ' ı seçin ve ardından **FW-karma-test**' i seçin.
    - **Sanal makine adı**  -  *VM-Onpred*.
    - **Bölge** -daha önce kullandığınız bölge.
    - **Kullanıcı adı**: \<type a user name\> .
    - **Parola**: \<type a user password\> .
7. **Ortak gelen bağlantı noktaları** için **Seçili bağlantı noktalarına izin ver**' i seçin ve ardından **RDP (3389)** öğesini seçin
4. **İleri ' yi seçin: diskler**.
5. Varsayılanları kabul edin ve **İleri ' yi seçin: ağ**.
6. Sanal ağ için **VNET-Onpred** ve alt ağ **sn-Corp**' ı seçin.
8. Ileri 'yi seçin **: yönetim**.
10. **Önyükleme tanılaması** Için **devre dışı bırak**' ı seçin.
10. **Gözden geçir + oluştur**' u seçin, Özet sayfasında ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

## <a name="test-the-firewall"></a>Güvenlik duvarını test etme

1. İlk olarak, **VM-ışınsal-01** sanal makinesi IÇIN özel IP adresi ' ni aklınızda yazın.

2. Azure portalından, **VM-Onprem** sanal makinesine bağlanın.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. **VM-Onpred** üzerinde bir Web tarayıcısı açın ve http://konumuna gidin \<VM-spoke-01 private IP\> .

   VM-ışınsal- **01** Web sayfasını görmeniz gerekir: ![ VM-ışınsal-01 Web sayfası](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. **VM-Onpree** sanal makinesinden, özel IP adresinde **VM-ışınsal-01 arası** uzak bir masaüstü açın.

   Bağlantınız başarılı olmalıdır ve oturum açabiliyor olmalısınız.

Artık Güvenlik Duvarı kurallarının çalıştığını doğruladınız:

<!---- You can ping the server on the spoke VNet.--->
- Web sunucusuna bağlı bileşen sanal ağı üzerinde gezinebilirsiniz.
- RDP kullanarak, bağlı olan sanal ağdaki sunucuya bağlanabilirsiniz.

Ardından, güvenlik duvarı kurallarının beklendiği gibi çalıştığını doğrulamak için güvenlik duvarı ağ kuralı koleksiyonu eylemini **Reddet** olarak değiştirin.

1. **AzFW01** güvenlik duvarını seçin.
2. **Kurallar**' ı seçin.
3. **Ağ kuralı koleksiyonu** sekmesini seçin ve **RCNet01** kural koleksiyonunu seçin.
4. **Eylem** için **Reddet**' i seçin.
5. **Kaydet**’i seçin.

Değişen kuralları test etmeden önce var olan tüm uzak masaüstlerini kapatın. Şimdi testleri yeniden çalıştırın. Bu kez tümü başarısız olmalıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Güvenlik duvarı kaynaklarını bir sonraki öğretici için tutabilirsiniz veya artık gerekli değilse **FW-Hybrid-Test** kaynak grubunu silerek güvenlik duvarıyla ilgili tüm kaynakları silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Şimdi Azure Güvenlik Duvarı günlüklerini izleyebilirsiniz.

> [!div class="nextstepaction"]
> [Öğretici: Azure Güvenlik Duvarı günlüklerini izleme](./firewall-diagnostics.md)