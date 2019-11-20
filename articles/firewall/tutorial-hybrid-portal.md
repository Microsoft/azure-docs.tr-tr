---
title: 'Öğretici: Azure portal kullanarak Azure Güvenlik duvarını karma ağda dağıtma ve yapılandırma'
description: Bu öğreticide, Azure portal kullanarak Azure Güvenlik Duvarı 'Nı dağıtmayı ve yapılandırmayı öğreneceksiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/02/2019
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 4a4fd2f89bc662f394b59aa6295c3a909cb8552b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468473"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak Azure Güvenlik duvarını karma ağda dağıtma ve yapılandırma

Karma ağ oluşturmak için şirket içi ağınızı bir Azure sanal ağına bağladığınızda, Azure ağ kaynaklarınıza erişimi denetleme özelliği, genel bir güvenlik planının önemli bir parçasıdır.

Azure Güvenlik Duvarı 'nı, izin verilen ve reddedilen ağ trafiğini tanımlayan kuralları kullanarak bir karma ağdaki ağ erişimini denetlemek için kullanabilirsiniz.

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

Bu senaryonun doğru çalışması için üç önemli gereksinimi vardır:

- Alt ağda, varsayılan ağ geçidi olarak Azure Güvenlik Duvarı IP adresini işaret eden bir Kullanıcı tanımlı yol (UDR). Bu yol tablosunda BGP yol yaymanın **Devre Dışı** olması gerekir.
- Hub ağ geçidi alt ağındaki UDR, bağlı olan ağların sonraki atlaması olarak güvenlik duvarı IP adresini göstermelidir.

   Azure Güvenlik Duvarı alt ağında BGP 'deki yolları öğrendiğinden dolayı UDR gerekmez.
- VNet-Hub'ı VNet-Spoke'a eşlerken **AllowGatewayTransit** ayarladığınızdan ve VNet-Spoke'u VNet-Hub'a eşlerken de **UseRemoteGateways** ayarladığınızdan emin olun.

Bu yolların nasıl oluşturulduğunu görmek için [Yolları Oluşturma](#create-the-routes) bölümüne bakın.

>[!NOTE]
>Azure Güvenlik duvarının doğrudan Internet bağlantısı olmalıdır. AzureFirewallSubnet, BGP aracılığıyla şirket içi ağınıza varsayılan bir yol öğrenirse, doğrudan Internet bağlantısını sürdürmek için **Nexthoptype** değeri **Internet** olarak ayarlanmış bir 0.0.0.0/0 UDR ile geçersiz kılmanız gerekir.
>
>Azure Güvenlik Duvarı şu anda zorlamalı tüneli desteklemiyor. Yapılandırmanız şirket içi bir ağa Zorlamalı tünel gerektiriyorsa ve Internet hedefleriniz için hedef IP öneklerini belirleyebiliyorsanız, bu aralıkları şirket içi ağ ile bir Kullanıcı tanımlı yol aracılığıyla sonraki atlama olarak yapılandırabilirsiniz. AzureFirewallSubnet. Ya da bu yolları tanımlamak için BGP kullanabilirsiniz.

>[!NOTE]
>Doğrudan eşlenmiş sanal ağlar arasındaki trafik, bir UDR varsayılan ağ geçidi olarak Azure Güvenlik Duvarı 'na işaret ediyorsa doğrudan yönlendirilir. Alt ağ trafiğine Bu senaryodaki güvenlik duvarının alt ağını göndermek için, her iki alt ağda de bir UDR 'nin hedef alt ağ önekini açıkça içermesi gerekir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-the-firewall-hub-virtual-network"></a>Güvenlik Duvarı hub 'ı sanal ağını oluşturma

İlk olarak, Bu öğreticinin kaynaklarını içerecek kaynak grubunu oluşturun:

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Azure portal giriş sayfasında, **ekle** > **kaynak grupları** ' nı seçin.
3. **Kaynak grubu adı**Için, **FW-karma-test**yazın.
4. **Abonelik** bölümünde aboneliğinizi seçin.
5. **Bölge**için **Doğu ABD**' yi seçin. Daha sonra oluşturduğunuz tüm kaynakların aynı konumda olması gerekir.
6. **Gözden geçir + oluştur**' u seçin.
7. **Oluştur**'u seçin.

Şimdi VNet 'i oluşturun:

> [!NOTE]
> AzureFirewallSubnet alt ağının boyutu/26 ' dır. Alt ağ boyutu hakkında daha fazla bilgi için bkz. [Azure Güvenlik DUVARı SSS](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. **Ağ**altında **sanal ağ**' ı seçin.
4. **Ad**için **VNET-hub**yazın.
5. **Adres alanı**için **10.5.0.0/16**yazın.
6. **Abonelik** bölümünde aboneliğinizi seçin.
7. **Kaynak grubu**Için, **FW-karma-test**' i seçin.
8. **Konum**için **Doğu ABD**' yi seçin.
9. **Alt ağ** bölümünde **Ad** alanına **AzureFirewallSubnet** yazın. Güvenlik duvarı bu alt ağda yer alacaktır ve alt ağ adının **mutlaka** AzureFirewallSubnet olması gerekir.
10. **Adres aralığı**için **10.5.0.0/26**yazın. 
11. Diğer varsayılan ayarları kabul edin ve **Oluştur**' u seçin.

## <a name="create-the-spoke-virtual-network"></a>Bağlı bileşen sanal ağını oluşturma

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. **Ağ**altında **sanal ağ**' ı seçin.
4. **Ad**için **VNET-kol**yazın.
5. **Adres alanı**için **10.6.0.0/16**yazın.
6. **Abonelik** bölümünde aboneliğinizi seçin.
7. **Kaynak grubu**Için, **FW-karma-test**' i seçin.
8. **Konum** alanında önceden kullandığınız konumu seçin.
9. **Alt ağ** altında, **Ad** için **SN-Workload** yazın.
10. **Adres aralığı**için **10.6.0.0/24**yazın.
11. Diğer varsayılan ayarları kabul edin ve **Oluştur**' u seçin.

## <a name="create-the-on-premises-virtual-network"></a>Şirket içi sanal ağı oluşturma

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. **Ağ**altında **sanal ağ**' ı seçin.
4. **Ad**için **VNET-onpred**yazın.
5. **Adres alanı** için **192.168.0.0/16** yazın.
6. **Abonelik** bölümünde aboneliğinizi seçin.
7. **Kaynak grubu**Için, **FW-karma-test**' i seçin.
8. **Konum** alanında önceden kullandığınız konumu seçin.
9. **Alt ağ**altında, **ad** türü **sn-Corp**için.
10. **Adres aralığı** için **192.168.1.0/24** yazın.
11. Diğer varsayılan ayarları kabul edin ve **Oluştur**' u seçin.

Şimdi ağ geçidi için ikinci bir alt ağ oluşturun.

1. **VNET-Onpreb** sayfasında **alt ağlar**' ı seçin.
2. **+ Alt ağ**' ı seçin.
3. **Ad**Için **gatewaysubnet**yazın.
4. **Adres aralığı (CIDR bloğu)** için **192.168.2.0/24**yazın.
5. **Tamam**’ı seçin.

### <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma

Bu, şirket içi ağ geçidi için kullanılan genel IP adresidir.

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. Arama metin kutusuna **genel IP adresi** yazın ve **ENTER**tuşuna basın.
3. **Genel IP adresi** ' ni seçin ve ardından **Oluştur**' u seçin.
4. Ad için **VNET-Onprea-GW-PI**yazın.
5. Kaynak grubu için, **FW-karma-test**yazın.
6. **Konum** alanında önceden kullandığınız konumu seçin.
7. Diğer varsayılanları kabul edin ve **Oluştur**' u seçin.

## <a name="configure-and-deploy-the-firewall"></a>Güvenlik duvarını yapılandırma ve dağıtma

Şimdi güvenlik duvarını güvenlik duvarı hub 'ı sanal ağına dağıtın.

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. Sol sütunda **ağ iletişimi**' ni seçin ve ardından **güvenlik duvarı**' nı seçin.
4. **Güvenlik duvarı oluştur** sayfasında aşağıdaki ayarları kullanarak güvenlik duvarını yapılandırın:

   |Ayar  |Değer  |
   |---------|---------|
   |Abonelik     |\<aboneliğiniz\>|
   |Kaynak grubu     |**FW-karma-test** |
   |Ad     |**AzFW01**|
   |Konum     |Önceden kullandığınız konumu seçin|
   |Bir sanal ağ seçin     |**Mevcut olanı kullan**:<br> **VNet-hub**|
   |Genel IP adresi     |Yeni oluştur: <br> - **FW-PIP** **adı** . |

5. **İncele ve oluştur**’u seçin.
6. Özeti gözden geçirin ve ardından güvenlik duvarını oluşturmak için **Oluştur** ' u seçin.

   Bu, dağıtılması birkaç dakika sürer.
7. Dağıtım tamamlandıktan sonra, **FW-karma-test** kaynak grubuna gidin ve **AzFW01** güvenlik duvarını seçin.
8. Özel IP adresini not edin. Varsayılan rotayı oluştururken bu adresi kullanacaksınız.

### <a name="configure-network-rules"></a>Ağ kurallarını yapılandırma

İlk olarak, Web trafiğine izin vermek için bir ağ kuralı ekleyin.

1. **AzFW01** sayfasında **kurallar**' ı seçin.
2. **Ağ kuralı koleksiyonu** sekmesini seçin.
3. **Ağ kuralı koleksiyonu Ekle**' yi seçin.
4. **Ad**için **RCNet01**yazın.
5. **Öncelik**için **100**yazın.
6. **Eylem** alanında **İzin ver**'i seçin.
6. **Kurallar**' ın altında, **ad**için **allowweb**yazın.
7. **Protokol** alanında **TCP**'yi seçin.
8. **Kaynak adresleri**için **192.168.1.0/24**yazın.
9. Hedef adres için **10.6.0.0/16** yazın
10. **Hedef bağlantı noktaları**için **80**yazın.

Şimdi RDP trafiğine izin vermek için bir kural ekleyin.

İkinci kural satırına aşağıdaki bilgileri yazın:

1. **Ad**, **allowrdp**yazın.
2. **Protokol** alanında **TCP**'yi seçin.
3. **Kaynak adresleri**için **192.168.1.0/24**yazın.
4. Hedef adres için **10.6.0.0/16** yazın
5. **Hedef bağlantı noktaları**için **3389**yazın.
6. **Add (Ekle)** seçeneğini belirleyin.

## <a name="create-and-connect-the-vpn-gateways"></a>VPN ağ geçitlerini oluşturma ve bağlama

Hub ve şirket içi sanal ağlar VPN ağ geçitleri aracılığıyla bağlanır.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Hub sanal ağı için bir VPN ağ geçidi oluşturma

Şimdi hub sanal ağı için VPN ağ geçidini oluşturun. Ağdan ağa yapılandırma, RouteBased bir VpnType gerektirir. VPN ağ geçidinin oluşturulması, seçili VPN ağ geçidi SKU’suna bağlı olarak 45 dakika veya daha uzun sürebilir.

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. Arama metin kutusuna **sanal ağ geçidi** yazın ve **ENTER**tuşuna basın.
3. **Sanal ağ geçidi**' ni seçin ve **Oluştur**' u seçin.
4. **Ad**için **GW-hub**yazın.
5. **Bölge**için, daha önce kullandığınız bölgeyi seçin.
6. **Ağ geçidi türü**için **VPN**' yi seçin.
7. **VPN türü**için **Rota tabanlı**' ı seçin.
8. **SKU**için **temel**' yı seçin.
9. **Sanal ağ**için **VNET hub**' ı seçin.
10. **Genel IP adresi**Için **Yeni oluştur**' u seçin ve ad için **VNET-hub-GW-PI** yazın.
11. Kalan Varsayılanları kabul edin ve ardından **gözden geçir + oluştur**' u seçin.
12. Yapılandırmayı gözden geçirin ve **Oluştur**' u seçin.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Şirket içi sanal ağ için bir VPN ağ geçidi oluşturma

Şimdi, şirket içi sanal ağ için VPN ağ geçidini oluşturun. Ağdan ağa yapılandırma, RouteBased bir VpnType gerektirir. VPN ağ geçidinin oluşturulması, seçili VPN ağ geçidi SKU’suna bağlı olarak 45 dakika veya daha uzun sürebilir.

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. Arama metin kutusuna **sanal ağ geçidi** yazın ve **ENTER**tuşuna basın.
3. **Sanal ağ geçidi**' ni seçin ve **Oluştur**' u seçin.
4. **Ad**için **GW-onpred**yazın.
5. **Bölge**için, daha önce kullandığınız bölgeyi seçin.
6. **Ağ geçidi türü**için **VPN**' yi seçin.
7. **VPN türü**için **Rota tabanlı**' ı seçin.
8. **SKU**için **temel**' yı seçin.
9. **Sanal ağ**için **VNET-onpred**öğesini seçin.
10. **Genel IP adresi**Için **Yeni oluştur**' u seçin ve ad Için **VNET-onpren-GW-PI** yazın.
11. Kalan Varsayılanları kabul edin ve ardından **gözden geçir + oluştur**' u seçin.
12. Yapılandırmayı gözden geçirin ve **Oluştur**' u seçin.

### <a name="create-the-vpn-connections"></a>VPN bağlantılarını oluşturma

Artık hub ve şirket içi ağ geçitleri arasında VPN bağlantıları oluşturabilirsiniz.

Bu adımda, hub sanal ağından şirket içi sanal ağa bağlantı oluşturursunuz. Örneklerde paylaşılan bir anahtar göreceksiniz. Paylaşılan anahtar için kendi değerlerinizi kullanabilirsiniz. Paylaşılan anahtarın her iki bağlantıyla da eşleşiyor olması önemlidir. Bir bağlantı oluşturmak çok zaman almaz.

1. **İlt-hibrit-test** kaynak grubunu açın ve **GW-hub** ağ geçidini seçin.
2. Sol sütundaki **Bağlantılar** ' ı seçin.
3. **Add (Ekle)** seçeneğini belirleyin.
4. Bağlantı adı, **hub-Onpree**yazın.
5. **Bağlantı türü**için **VNET-VNET** ' i seçin.
6. **İkinci sanal ağ geçidi**için **GW-onpred**öğesini seçin.
7. **Paylaşılan anahtar (PSK)** için **AzureA1b2C3**yazın.
8. **Tamam**’ı seçin.

Şirket içinden hub 'a sanal ağ bağlantısını oluşturun. Bu adım, VNet-Onpree ile VNet hub 'ına bağlantı oluşturmanız dışında öncekiyle benzerdir. Paylaşılan anahtarların eşleştiğinden emin olun. Bağlantı birkaç dakika içerisinde kurulacaktır.

1. ILT- **hibrit-test** kaynak grubunu açın ve **GW-onprea** Gateway ' i seçin.
2. Sol sütundaki **Bağlantılar** ' ı seçin.
3. **Add (Ekle)** seçeneğini belirleyin.
4. Bağlantı adı, **Onpree-hub**' ı yazın.
5. **Bağlantı türü**için **VNET-VNET** ' i seçin.
6. **İkinci sanal ağ geçidi**için **GW-hub**' ı seçin.
7. **Paylaşılan anahtar (PSK)** için **AzureA1b2C3**yazın.
8. **Tamam**’ı seçin.


#### <a name="verify-the-connection"></a>Bağlantıyı doğrulama

Yaklaşık beş dakika sonra, her iki bağlantının da durumu **bağlanmalıdır**.

![Ağ Geçidi bağlantıları](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Hub ve bağlı bileşen sanal ağlarını eşler

Artık hub ve bağlı bileşen sanal ağlarını eşler.

1. **İlt-hibrit-test** kaynak grubunu açın ve **VNET hub** sanal ağını seçin.
2. Sol sütunda, eşlemeler ' i **seçin.**
3. **Add (Ekle)** seçeneğini belirleyin.
4. **Ad**Için **hubtokol**yazın.
5. **Sanal ağ**için **VNET-bağlı bileşen** ' i seçin
6. Sanal ağ hub 'ından VNet hub 'ına eşleme adı için **Spoketohub**yazın.
7. **Ağ geçidi aktarımına Izin ver**' i seçin.
8. **Tamam**’ı seçin.

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>SpoketoHub eşlemesi için ek ayarlar yapılandırma

SpoketoHub eşlemesi için **iletilen trafiğe Izin ver** ' i etkinleştirmeniz gerekir.

1. **İlt-hibrit-test** kaynak grubunu açın ve **VNET-bağlı** sanal ağını seçin.
2. Sol sütunda, eşlemeler ' i **seçin.**
3. **Spoketohub** eşlemesini seçin.
4. **VNET hub 'ından sanal ağa yönlendirilen trafiğe Izin ver**altında, **etkin**' i seçin.
5. **Kaydet**’i seçin.

## <a name="create-the-routes"></a>Yolları oluşturma

Şimdi birkaç yol oluşturun:

- Güvenlik duvarı IP adresi üzerinden hub ağ geçidi alt ağından uç alt ağına giden bir yol
- Güvenlik duvarı IP adresi üzerinden uç alt ağından gelen varsayılan yol

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. Arama metin kutusuna **yol tablosu** yazın ve **ENTER**tuşuna basın.
3. **Yol tablosu**' nu seçin.
4. **Oluştur**'u seçin.
5. Ad için **UDR-hub-kol**yazın.
6. Kaynak grubu için **FW-karma-test** ' i seçin.
8. **Konum** alanında önceden kullandığınız konumu seçin.
9. **Oluştur**'u seçin.
10. Yol tablosu oluşturulduktan sonra, yol tablosu sayfasını açmak için seçin.
11. Sol sütundaki **rotalar** ' ı seçin.
12. **Add (Ekle)** seçeneğini belirleyin.
13. Yol adı için, uzak **bileşene**yazın.
14. Adres ön eki için **10.6.0.0/16**yazın.
15. Sonraki atlama türü için **Sanal Gereç**' ı seçin.
16. Sonraki atlama adresi için, daha önce not ettiğiniz güvenlik duvarının özel IP adresini yazın.
17. **Tamam**’ı seçin.

Şimdi rota alt ağıyla ilişkilendirin.

1. **UDR-hub-bağlı bileşen-yollar** sayfasında, **alt ağlar**' ı seçin.
2. **İlişkilendir**' i seçin.
3. **Bir sanal ağ seçin**' i seçin.
4. **VNET hub 'ı**seçin.
5. **Gatewaysubnet**öğesini seçin.
6. **Tamam**’ı seçin.

Şimdi, bağlı olan alt ağdan varsayılan yolu oluşturun.

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. Arama metin kutusuna **yol tablosu** yazın ve **ENTER**tuşuna basın.
3. **Yol tablosu**' nu seçin.
5. **Oluştur**'u seçin.
6. Ad için **UDR-DG**yazın.
7. Kaynak grubu için **FW-karma-test** ' i seçin.
8. **Konum** alanında önceden kullandığınız konumu seçin.
4. **Sanal ağ geçidi yol yayma**Için **devre dışı**' yı seçin.
1. **Oluştur**'u seçin.
2. Yol tablosu oluşturulduktan sonra, yol tablosu sayfasını açmak için seçin.
3. Sol sütundaki **rotalar** ' ı seçin.
4. **Add (Ekle)** seçeneğini belirleyin.
5. Yol adı için, **Tohub**yazın.
6. Adres ön eki için **0.0.0.0/0**yazın.
7. Sonraki atlama türü için **Sanal Gereç**' ı seçin.
8. Sonraki atlama adresi için, daha önce not ettiğiniz güvenlik duvarının özel IP adresini yazın.
9. **Tamam**’ı seçin.

Şimdi rota alt ağıyla ilişkilendirin.

1. **UDR-DG-Routes** sayfasında **alt ağlar**' ı seçin.
2. **İlişkilendir**' i seçin.
3. **Bir sanal ağ seçin**' i seçin.
4. **VNET-bağlı bileşen**' i seçin.
5. **Sn-Iş yükü**seçin.
6. **Tamam**’ı seçin.

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Artık bağlı olan iş yükünü ve şirket içi sanal makineleri oluşturun ve uygun alt ağlara yerleştirin.

### <a name="create-the-workload-virtual-machine"></a>İş yükü sanal makinesi oluşturma

Genel IP adresi olmadan IIS çalıştıran, bağlı olan sanal ağda bir sanal makine oluşturun.

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. **Popüler**bölümünde **Windows Server 2016 Datacenter**' u seçin.
3. Sanal makine için şu değerleri girin:
    - **Kaynak grubu** - **ILT-hibrit-test**' i seçin.
    - **Sanal makine adı**: *VM-ışınsal-01*.
    - **Bölge** -daha önce kullandığınız bölge.
    - **Kullanıcı adı**: *azureuser*.
    - **Parola**: *Azure123456!*
4. **İleri ' yi seçin: diskler**.
5. Varsayılanları kabul edin ve **İleri ' yi seçin: ağ**.
6. Sanal ağ için **VNET-ışınsal** ve alt ağ **sn-iş yükü**' ı seçin.
7. **Genel IP**için **hiçbiri**' ni seçin.
8. **Ortak gelen bağlantı noktaları**için **Seçili bağlantı noktalarına izin ver**' i seçin ve ardından **http (80)** ve **RDP (3389)** öğesini seçin.
9. Ileri 'yi seçin **: yönetim**.
10. **Önyükleme tanılaması**için **kapalı**' yı seçin.
11. **Gözden geçir + oluştur**' u seçin, Özet sayfasında ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

### <a name="install-iis"></a>IIS yükleme

1. Azure portal, Cloud Shell açın ve **PowerShell**olarak ayarlandığından emin olun.
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
2. **Popüler**bölümünde **Windows Server 2016 Datacenter**' u seçin.
3. Sanal makine için şu değerleri girin:
    - **Kaynak grubu** -mevcut ' ı seçin ve ardından **FW-karma-test**' i seçin.
    - **Sanal makine adı** - *VM-onpred*.
    - **Bölge** -daha önce kullandığınız bölge.
    - **Kullanıcı adı**: *azureuser*.
    - **Parola**: *Azure123456!* .
4. **İleri ' yi seçin: diskler**.
5. Varsayılanları kabul edin ve **İleri ' yi seçin: ağ**.
6. Sanal ağ için **VNET-Onpred** ve alt ağ **sn-Corp**' ı seçin.
7. **Ortak gelen bağlantı noktaları**için **Seçili bağlantı noktalarına izin ver**' i seçin ve ardından **RDP (3389)** öğesini seçin
8. Ileri 'yi seçin **: yönetim**.
9. **Önyükleme tanılaması**için **kapalı**' yı seçin.
10. **Gözden geçir + oluştur**' u seçin, Özet sayfasında ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

## <a name="test-the-firewall"></a>Güvenlik duvarını test etme

1. İlk olarak, **VM-ışınsal-01** sanal makinesi IÇIN özel IP adresi ' ni aklınızda yazın.

2. Azure portalından, **VM-Onprem** sanal makinesine bağlanın.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. **VM-Onpred**üzerinde bir Web tarayıcısı açın ve http://\<VM-ışınsal-01 özel IP\>gidin.

   VM-kol- **01** Web sayfasını görmeniz gerekir: ![VM-ışınsal-01 web sayfası](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

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
4. **Eylem**için **Reddet**' i seçin.
5. **Kaydet**’i seçin.

Değişen kuralları test etmeden önce var olan tüm uzak masaüstlerini kapatın. Şimdi testleri yeniden çalıştırın. Bu kez tümü başarısız olmalıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Güvenlik duvarı kaynaklarını bir sonraki öğretici için tutabilirsiniz veya artık gerekli değilse **FW-Hybrid-Test** kaynak grubunu silerek güvenlik duvarıyla ilgili tüm kaynakları silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Şimdi Azure Güvenlik Duvarı günlüklerini izleyebilirsiniz.

> [!div class="nextstepaction"]
> [Öğretici: Azure Güvenlik Duvarı günlüklerini izleme](./tutorial-diagnostics.md)
