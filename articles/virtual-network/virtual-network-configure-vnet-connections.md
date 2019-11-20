---
title: Sanal ağ veya VPN bağlantılarını yapılandırma ve doğrulama
description: Çeşitli Azure VPN ve sanal ağ dağıtımlarını yapılandırma ve doğrulamaya yönelik adım adım yönergeler
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71099069"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Sanal ağ veya VPN bağlantılarını yapılandırma ve doğrulama

Bu izlenecek yol, çeşitli Azure VPN ve sanal ağ dağıtımlarını yapılandırmak ve doğrulamak için adım adım yönergeler sağlar. Senaryolar arasında geçiş yönlendirmesi, ağdan ağa bağlantılar, Sınır Ağ Geçidi Protokolü (BGP), çok siteli bağlantılar ve Noktadan siteye bağlantılar bulunur.

Azure VPN ağ geçitleri, Azure 'da neredeyse her türlü bağlı sanal ağ topolojisini düzenleme esnekliği sağlar. Örneğin, sanal ağları bağlayabilirsiniz:

- Bölgeler arasında.
- Sanal ağ türleri arasında (Azure Resource Manager Klasik).
- Azure 'da veya şirket içi karma ortamda.
- Farklı aboneliklerde. 

## <a name="network-to-network-vpn-connection"></a>Ağdan ağa VPN bağlantısı

VPN aracılığıyla bir sanal ağı başka bir sanal ağa (ağdan ağa) bağlamak, sanal bir ağı şirket içi site konumuna bağlamaya benzer. Her iki bağlantı türü de IPSec ve ıKE aracılığıyla güvenli bir tünel sağlamak için bir VPN ağ geçidi kullanır. Sanal ağlar aynı ya da farklı bölgelerde ve aynı ya da farklı aboneliklerde bulunuyor olabilirler.

![IPSec ile ağdan ağa bağlantı](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Sanal ağlarınız aynı bölgedeyse, sanal ağ eşlemesi kullanarak bunları bağlamayı düşünmek isteyebilirsiniz. Sanal ağ eşlemesi bir VPN ağ geçidi kullanmaz. Aktarım hızını artırır ve gecikme süresini azaltır. Bir sanal ağ eşleme bağlantısını yapılandırmak için, **VNET eşlemesini Yapılandır ve Doğrula**' yı seçin.

Sanal ağlarınız Azure Resource Manager dağıtım modeli aracılığıyla oluşturulduysa, VPN bağlantısını yapılandırmak için bir **Kaynak Yöneticisi VNET 'i Kaynak Yöneticisi VNET bağlantısına Yapılandır ve Doğrula** ' yı seçin.

Azure klasik dağıtım modeli üzerinden sanal ağlardan biri oluşturulduysa ve diğeri Kaynak Yöneticisi aracılığıyla oluşturulduysa, bir VPN bağlantısı yapılandırmak için **Klasik VNET 'i Kaynak Yöneticisi VNET bağlantısına Yapılandır ve Doğrula** ' yı seçin.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Aynı bölgedeki iki sanal ağ için sanal ağ eşlemesi yapılandırma

Azure sanal ağ eşlemesi 'ni uygulamaya ve yapılandırmaya başlamadan önce, aşağıdaki önkoşulları karşıladığınızdan emin olun:

* Eşlenmiş sanal ağlar aynı Azure bölgesinde bulunmalıdır.
* Eşlenen sanal ağlarda çakışan IP adresi alanları olmalıdır.
* Sanal ağ eşlemesi iki sanal ağ arasında gerçekleşir. Eşlemeler arasında türetilmiş geçişli ilişki yoktur. Örneğin, VNetA, VNetB ile eşlenirse ve VNetB VNetC ile eşlenirse VNetA, VNetC ile *eşlenmez* .

Gereksinimleri karşıladığınızda öğreticiyi izleyebilirsiniz [: Eşlemeyi oluşturmak ve yapılandırmak için Azure Portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) kullanarak sanal ağ eşlemesi ile sanal ağları bağlayın.

Eşleme yapılandırmasını denetlemek için aşağıdaki yöntemi kullanın:

1. Gerekli [Roller ve izinlere](virtual-network-manage-peering.md#permissions)sahip bir hesap kullanarak [Azure Portal](https://portal.azure.com/) oturum açın.
2. Portalın üst kısmındaki metin **arama kaynaklarını** içeren kutuda, **sanal ağlar**yazın. Arama sonuçlarında **sanal ağlar** göründüğünde, bunu seçin.
3. Görüntülenen **sanal ağlar** dikey penceresinde, eşleme oluşturmak istediğiniz sanal ağı seçin.
4. Sanal ağ için görüntülenen bölmede, **Ayarlar** bölümünde **eşlemeler ' i seçin.**
5. Bir eşleme seçin ve yapılandırma sonuçlarını görüntüleyin.

![Sanal ağ eşleme yapılandırmasını denetleme seçimleri](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Azure PowerShell için, sanal ağ eşlemesini almak üzere [Get-Azurermvirtualnetworkeşleme](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) komutunu çalıştırın. Bir örneği aşağıda verilmiştir:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Kaynak Yöneticisi sanal ağını başka bir Kaynak Yöneticisi sanal ağa bağlama

Bir Kaynak Yöneticisi sanal ağdan, doğrudan başka bir Kaynak Yöneticisi sanal ağa bağlantı yapılandırabilirsiniz. Veya bağlantıyı IPSec kullanarak da yapılandırabilirsiniz.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Kaynak Yöneticisi sanal ağlar arasında VPN bağlantısı yapılandırma

Kaynak Yöneticisi sanal ağlar arasında IPSec olmadan bir bağlantı yapılandırmak için, bkz. [Azure Portal kullanarak ağdan ağa VPN Ağ Geçidi bağlantısı yapılandırma](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

İki Kaynak Yöneticisi sanal ağ arasında IPSec ile bir bağlantı yapılandırmak için, her bir sanal ağ için [Azure Portal bir siteden siteye bağlantı oluşturma](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) bölümünde 1 ile 5 arasındaki adımları uygulayın.

> [!Note]
> Bu adımlar yalnızca aynı abonelikteki sanal ağlarda çalışır. Sanal ağlarınız farklı aboneliklerdeyse, bağlantıyı kurmak için PowerShell kullanmanız gerekir. [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps) makalesine bakın.

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Kaynak Yöneticisi sanal ağlar arasındaki VPN bağlantısını doğrulama

![Azure Resource Manager sanal ağ ile klasik sanal ağ bağlantısı](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

VPN bağlantınızın doğru şekilde yapılandırıldığından emin olmak için bu yönergeleri izleyin.

> [!Note] 
> Bu adımdaki sanal ağ bileşenlerinden sonraki sayılar, önceki diyagramdaki sayılara karşılık gelir.

1. Bağlı sanal ağlarda çakışan adres alanları olmadığından emin olun.
2. Azure Resource Manager sanal ağı için adres aralığının (1) **bağlantı nesnesi** örneğinde (4) doğru şekilde tanımlandığından emin olun.
3. Azure Resource Manager sanal ağı (6) için adres aralığının **bağlantı nesnesi** örneğinde (3) doğru şekilde tanımlandığından emin olun.
4. Önceden paylaşılan anahtarların bağlantı nesnelerinde eşleştirdiğini doğrulayın.
5. Azure Resource Manager sanal ağ geçidi VIP (2) **bağlantı nesnesi** örneğinde (4) doğru bir şekilde tanımlandığından emin olun.
6. Azure Resource Manager sanal ağ geçidi VIP (5) ' in **bağlantı nesnesi** örneğinde (3) doğru şekilde tanımlandığından emin olun.

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Klasik sanal ağı Kaynak Yöneticisi sanal ağa bağlama

Farklı aboneliklerdeki ve farklı bölgelerdeki sanal ağlar arasında bir bağlantı oluşturabilirsiniz. Ağ Geçidi türünü rota tabanlı olarak yapılandırdığınız sürece, zaten şirket içi ağlarda bağlantıları olan sanal ağları da bağlayabilirsiniz.

Klasik bir sanal ağ ile Kaynak Yöneticisi sanal ağ arasında bağlantı yapılandırmak için, bkz. [Azure Portal kullanarak farklı dağıtım modellerindeki sanal ağları bağlama](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal).

![Azure Resource Manager sanal ağ ile klasik sanal ağ bağlantısı](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Klasik bir sanal ağı bir Azure Resource Manager sanal ağa bağladığınızda yapılandırmayı denetlemek için bu yönergeleri izleyin.

> [!Note] 
> Bu adımdaki sanal ağ bileşenlerinden sonraki sayılar, önceki diyagramdaki sayılara karşılık gelir. 

1. Bağlı sanal ağlarda çakışan adres alanları olmadığından emin olun.
2. Azure Resource Manager sanal ağı (6) için adres aralığının Klasik yerel ağ tanımında (3) doğru şekilde tanımlandığından emin olun.
3. Klasik sanal ağ (1) için adres aralığının Azure Resource Manager **bağlantı nesnesi** örneğinde (4) doğru şekilde tanımlandığından emin olun.
4. Klasik sanal ağ geçidi VIP 'nin (2) Azure Resource Manager **bağlantı nesnesi** örneğinde (4) doğru bir şekilde tanımlandığından emin olun.
5. Azure Resource Manager sanal ağ geçidinin (5) klasik **yerel ağ tanımı** örneğinde (3) doğru şekilde tanımlandığından emin olun.
6. Önceden paylaşılan anahtarların hem bağlı sanal ağlarda eşleştirdiğini doğrulayın:
   - Klasik sanal ağ: **Yerel ağ tanımı** 03
   - Sanal ağ Azure Resource Manager: **Bağlantı nesnesi** 4

## <a name="create-a-point-to-site-vpn-connection"></a>Noktadan siteye VPN bağlantısı oluşturma

Bir noktadan siteye (*P2S* ) yapılandırması, tek bir istemci bilgisayardan bir sanal ağa güvenli bir bağlantı oluşturmanıza olanak sağlar. Noktadan siteye bağlantılar, ana veya konferans gibi uzak bir konumdan sanal ağınıza bağlanmak istediğinizde faydalıdır. Ayrıca, bir sanal ağa bağlanması gereken yalnızca birkaç istemciniz olduğunda da yararlıdır. 

Noktadan siteye VPN bağlantısı, yerel Windows VPN istemcisi aracılığıyla istemci bilgisayardan başlatılır. Bağlanan istemciler kimlik doğrulamak için sertifika kullanır.

![Noktadan siteye bağlantılar](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Noktadan siteye bağlantılar için bir VPN cihazı gerekmez. VPN bağlantısını Güvenli Yuva Tünel Protokolü (SSTP) üzerinden oluşturur. Çeşitli dağıtım araçlarını ve dağıtım modellerini kullanarak bir sanal ağa Noktadan siteye bağlantı bağlayabilirsiniz:

* [Azure portal kullanarak bir sanal ağa Noktadan siteye bağlantı yapılandırma](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Azure portal (klasik) kullanarak bir sanal ağa Noktadan siteye bağlantı yapılandırma](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [PowerShell kullanarak bir sanal ağa Noktadan siteye bağlantı yapılandırma](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>Noktadan siteye bağlantınızı doğrulama

Sorun giderme [makalesi: Azure Noktadan siteye bağlantı sorunları](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) , Noktadan siteye bağlantılar ile ilgili yaygın sorunları açıklar.

## <a name="create-a-multisite-vpn-connection"></a>Çoklu site VPN bağlantısı oluşturma

Siteden siteye bağlantı, Noktadan siteye bağlantı veya ağdan ağa bağlantı içeren bir sanal ağa bağlantı sağlayan bir siteden siteye (Aşağıdaki diyagramda*S2S* ) ekleme yapabilirsiniz. Bu tür bir bağlantı genellikle *çok siteli* yapılandırma olarak adlandırılır. 

![Çoklu site bağlantısı](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure Şu anda iki dağıtım modeli ile birlikte çalışıyor: Kaynak Yöneticisi ve klasik. İki model birbirleriyle tamamen uyumlu değildir. Farklı modellerle bir çoklu site bağlantısı yapılandırmak için aşağıdaki makalelere bakın:

* [Mevcut bir VPN Ağ Geçidi bağlantısı ile bir sanal ağa siteden siteye bağlantı ekleme](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Mevcut bir VPN Ağ Geçidi bağlantısı ile bir sanal ağa siteden siteye bağlantı ekleme (klasik)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> Bu makalelerdeki adımlar Azure ExpressRoute ve siteden siteye birlikte var olan bağlantı yapılandırmalarına uygulanmaz. Daha fazla bilgi için bkz. [ExpressRoute ve siteden siteye birlikte var olan bağlantılar](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

## <a name="configure-transit-routing"></a>Geçiş yönlendirmeyi yapılandırma

Transit yönlendirme, zincirleme bir topolojide birden çok ağı bağladığınızda belirli bir yönlendirme senaryosudur. Bu yönlendirme, zincirin her birindeki sanal ağlardaki kaynakların, arasında sanal ağlar aracılığıyla birbirleriyle iletişim kurmasını sağlar. Geçiş yönlendirmesi olmadan, bir hub aracılığıyla eşlenen ağlar veya cihazlar birbiriyle iletişime geçemez.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Noktadan siteye bağlantıda geçiş yönlendirmeyi yapılandırma

VNetA ve VNetB arasında siteden siteye VPN bağlantısı yapılandırmak istediğiniz senaryoyu düşünün. Ayrıca, istemcinin VNetA ağ geçidine bağlanması için Noktadan siteye VPN yapılandırmak isteyebilirsiniz. Ardından, Noktadan siteye istemcilerin VNetA aracılığıyla geçen VNetB 'ye bağlanması için geçiş yönlendirmeyi etkinleştirmek istersiniz. 

Bu senaryo, VNetA ve VNetB arasındaki siteden siteye VPN üzerinde BGP etkinleştirildiğinde desteklenir. Daha fazla bilgi için bkz. [noktadan sıteye VPN yönlendirmesi hakkında](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing).

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>ExpressRoute bağlantısında geçiş yönlendirmeyi yapılandırma

Azure ExpressRoute, bağlantı sağlayıcı tarafından kolaylaştırılan adanmış özel bağlantı üzerinden şirket içi ağlarınızı Microsoft bulutuna genişletmenizi sağlar. ExpressRoute'u kullanarak Microsoft Azure, Office 365 ve Dynamics 365 gibi Microsoft bulut hizmetleriyle bağlantı kurabilirsiniz. Daha fazla bilgi için bkz. [ExpressRoute 'a genel bakış](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Azure sanal ağlarına ExpressRoute özel eşleme bağlantısı](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> VNetA ve VNetB 'nin aynı geopolitik bölgede olması durumunda, geçiş yönlendirmeyi yapılandırmak yerine [her iki sanal ağı ExpressRoute devresine bağlarsınız](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) . Sanal ağlarınız farklı coğrafi bölge bölgeslarlardayken, [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium)'a sahipseniz, bunları bağlantı hattına doğrudan da bağlayabilirsiniz. 

ExpressRoute ve siteden siteye birlikte bulunma varsa, geçiş yönlendirmesi desteklenmez. Daha fazla bilgi için bkz. [PowerShell kullanarak ExpressRoute ve siteden siteye yapılandırma](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Yerel ağlarınızı bir Azure sanal ağına bağlamak için ExpressRoute 'u etkinleştirdiyseniz, geçiş yönlendirmesine sahip olmak istediğiniz sanal ağlar arasında eşlemeyi etkinleştirebilirsiniz. Yerel ağlarınızın uzak sanal ağa bağlanmasına izin vermek için [sanal ağ eşlemesini](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity)yapılandırmanız gerekir. 

> [!Note]
> Sanal ağ eşlemesi yalnızca aynı bölgedeki sanal ağlarda kullanılabilir.

Sanal ağ eşlemesi için geçiş yönlendirmesi yapılandırılıp yapılandırılmadığını denetlemek için şu yönergeleri izleyin:

1. Gerekli [Roller ve izinlere](virtual-network-manage-peering.md#permissions)sahip bir hesap kullanarak [Azure Portal](https://portal.azure.com/) oturum açın.
2. Önceki diyagramda gösterildiği gibi [VNetA ve VNetB arasında bir eşleme oluşturun](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) . 
3. Sanal ağ için görüntülenen bölmede, **Ayarlar** bölümünde **eşlemeler ' i seçin.**
4. Görüntülemek istediğiniz eşlemeyi seçin. Ardından, ExpressRoute bağlantı hattına bağlı VNetA ağında **ağ geçidi aktarımına Izin ver** ' i etkinleştirdiğini doğrulamak için **yapılandırma** ' yı seçin ve ExpressRoute 'A bağlı olmayan uzak Vnetb ağı üzerinde **uzak ağ geçidini kullanın** bilgisayarına.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Sanal ağ eşleme bağlantısında geçiş yönlendirmeyi yapılandırma

Sanal ağlar eşlendiğinde, eşlenmiş sanal ağdaki ağ geçidini şirket içi bir ağa geçiş noktası olarak da yapılandırabilirsiniz. Sanal ağ eşağında bir geçiş yolu yapılandırmak için bkz. [ağdan ağa bağlantılar](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json).

> [!Note]
> Farklı dağıtım modelleriyle oluşturulan sanal ağlar arasındaki eşleme ilişkisinde ağ geçidi geçişi desteklenmez. Ağ Geçidi geçişinin çalışması için, eşleme ilişkisindeki her iki sanal ağın da Kaynak Yöneticisi kullanılarak oluşturulmuş olması gerekir.

Sanal ağ eşlemesi için bir geçiş yolu yapılandırılıp yapılandırılmadığını denetlemek için şu yönergeleri izleyin:

1. Gerekli [Roller ve izinlere](virtual-network-manage-peering.md#permissions)sahip bir hesap kullanarak [Azure Portal](https://portal.azure.com/) oturum açın.
2. Portalın üst kısmındaki metin **arama kaynaklarını** içeren kutuda, **sanal ağlar**yazın. Arama sonuçlarında **sanal ağlar** göründüğünde, bunu seçin.
3. Görüntülenen **sanal ağlar** dikey penceresinde, eşleme ayarını denetlemek istediğiniz sanal ağı seçin.
4. Seçtiğiniz sanal ağ için görüntülenen bölmede, **Ayarlar** bölümünde **eşlemeler ' i seçin.**
5. Görüntülemek istediğiniz eşlemeyi seçin. **Ağ geçidi aktarımına Izin ver** ' i etkinleştirdiğini ve **yapılandırma**altında **uzak ağ geçitlerini kullanacağınızı** doğrulayın.

![Sanal ağ eşlemesi için bir geçiş yolu yapılandırdığınıza yönelik seçimler](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Ağdan ağa bağlantıda geçiş yönlendirmeyi yapılandırma

Sanal ağlar arasında geçiş yönlendirmeyi yapılandırmak için, Kaynak Yöneticisi dağıtım modelini ve PowerShell 'i kullanarak tüm ara ağdan ağa bağlantılar üzerinde BGP 'yi etkinleştirmelisiniz. Yönergeler için bkz. [PowerShell kullanarak Azure VPN ağ geçitlerinde BGP yapılandırma](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Azure VPN ağ geçitleri aracılığıyla geçiş trafiği, klasik dağıtım modeli aracılığıyla yapılabilir, ancak ağ yapılandırma dosyasında statik olarak tanımlanan adres alanlarını kullanır. Azure sanal ağları ve VPN Gateway 'ler ile klasik dağıtım modeli üzerinden BGP henüz desteklenmiyor. BGP olmadan, transit adres alanlarını el ile tanımlamak hataya açıktır ve bunu önermiyoruz.

> [!Note]
> Klasik Azure portalını kullanarak veya klasik portalda bir ağ yapılandırma dosyası kullanarak, klasik ağdan ağa bağlantıları yapılandırırsınız. Azure Resource Manager dağıtım modeli veya Azure portal aracılığıyla klasik bir sanal ağ oluşturamaz veya değiştiremezsiniz. Klasik sanal ağlar için geçiş yönlendirme hakkında daha fazla bilgi için bkz. [Microsoft Developer blogu](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Siteden siteye bağlantıda geçiş yönlendirmeyi yapılandırma

Şirket içi ağınız ile siteden siteye bağlantısı olan bir sanal ağ arasında geçiş yönlendirmeyi yapılandırmak için, Kaynak Yöneticisi dağıtım modelini ve PowerShell 'i kullanarak tüm ara siteden siteye bağlantılar üzerinde BGP 'yi etkinleştirmelisiniz. Yönergeler için bkz. [PowerShell kullanarak Azure VPN ağ geçitlerinde BGP yapılandırma](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) .

Azure VPN ağ geçitleri aracılığıyla geçiş trafiği, klasik dağıtım modeli aracılığıyla yapılabilir, ancak ağ yapılandırma dosyasında statik olarak tanımlanan adres alanlarını kullanır. Azure sanal ağları ve VPN Gateway 'ler ile klasik dağıtım modeli üzerinden BGP henüz desteklenmiyor. BGP olmadan, transit adres alanlarını el ile tanımlamak hataya açıktır ve bunu önermiyoruz.

> [!Note]
> Klasik siteden siteye bağlantıları klasik Azure portalını kullanarak veya klasik portalda bir ağ yapılandırma dosyası kullanarak yapılandırırsınız. Azure Resource Manager dağıtım modeli veya Azure portal aracılığıyla klasik bir sanal ağ oluşturamaz veya değiştiremezsiniz. Klasik sanal ağlar için geçiş yönlendirme hakkında daha fazla bilgi için bkz. [Microsoft Developer blogu](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

## <a name="configure-bgp-for-a-vpn-gateway"></a>Bir VPN ağ geçidi için BGP yapılandırma

BGP, iki veya daha fazla ağ arasında yönlendirme ve ulaşılabilirlik bilgilerini değiş tokuş etmek için internet 'te kullanılan standart yönlendirme protokolüdür. BGP, Azure sanal ağları bağlamında kullanıldığında, Azure VPN ağ geçitlerini ve BGP eşleri veya komşuları olarak bilinen şirket içi VPN cihazlarınızı sunar. Her iki ağ geçidini, bu öneklerdeki ağ geçitleri veya yönlendiriciler arasında gidip ulaşılabilmesi için kullanılabilirlik ve ulaşılabilirlik konusunda bilgilendiren "rotalar" şeklinde değiş tokuş ederler. 

BGP, bir BGP ağ geçidinin bir BGP eşinden diğer tüm BGP eşlerine öğrenme yollarını yayarak birden çok ağ arasında geçiş yönlendirmeyi de etkinleştirebilir. Daha fazla bilgi için bkz. [Azure VPN Gateway Ile BGP 'ye genel bakış](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>VPN bağlantısı için BGP 'yi yapılandırma

BGP kullanan bir VPN bağlantısı yapılandırmak için bkz. [PowerShell kullanarak Azure VPN ağ geçitlerinde BGP yapılandırma](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Sanal ağ geçidinde BGP 'yi, için bir otonom sistem (AS) numarası oluşturarak etkinleştirin. Temel ağ geçitleri BGP 'yi desteklemez. Ağ geçidinin SKU 'sunu denetlemek için Azure portal **VPN Gateway** dikey penceresinin **genel bakış** bölümüne gidin. SKU 'nuzu **temel**Ise, SKU 'yu (bkz. [ağ geçidini yeniden boyutlandırma](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)) **VpnGw1**olarak değiştirmeniz gerekir. 

SKU 'nun denetlenmesi, 20 ila 30 dakika kapalı kalma süresine neden olur. Ağ geçidinin doğru SKU 'SU varsa, [set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell cmdlet 'INI kullanarak as numarasını ekleyebilirsiniz. AS numarasını yapılandırdıktan sonra, ağ geçidi için bir BGP eşi IP 'si otomatik olarak sunulacaktır.

Bir as numarasıyla ve `LocalNetworkGateway` BGP eş adresiyle el ile sağlamanız gerekir. `ASN` Ve`-BgpPeeringAddress` değerlerini [New-azurermlocalnetworkgateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) veya [set-azurermlocalnetworkgateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell komutlet kullanarak ayarlayabilirsiniz. Bazı AS numaraları Azure için ayrılmıştır ve [azure VPN Gateway Ile BGP hakkında](../vpn-gateway/vpn-gateway-bgp-overview.md#faq)bölümünde açıklandığı gibi bunları kullanamazsınız.

Bağlantı nesnesi BGP 'nin etkin olması gerekir. `-EnableBGP` Değeri [New-azurermvirtualnetworkgatewayconnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) veya [set-azurermvirtualnetworkgatewayconnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0)aracılığıyla olarak `$True` ayarlayabilirsiniz.

### <a name="validate-the-bgp-configuration"></a>BGP yapılandırmasını doğrulama

BGP 'nin doğru yapılandırılıp yapılandırılmadığını denetlemek için `get-AzureRmVirtualNetworkGateway` ve `get-AzureRmLocalNetworkGateway` cmdlet 'lerini çalıştırabilirsiniz. Daha sonra, `BgpSettingsText` bölümünde BGP ile ilgili çıkış olduğunu fark edeceksiniz. Örneğin:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Yüksek oranda kullanılabilir etkin/etkin VPN bağlantısı oluşturma

Etkin/etkin ve etkin/hazır ağ geçitleri arasındaki temel farklılıklar şunlardır:

* İki genel IP adresi ile iki ağ geçidi IP yapılandırması oluşturmanız gerekir.
* **Enableactiveactivefeature** bayrağını ayarlamanız gerekir.
* Ağ Geçidi SKU 'SU **VpnGw1**, **VpnGw2**veya **VpnGw3**olmalıdır.

Şirketler arası ve ağdan ağa bağlantı için yüksek kullanılabilirlik elde etmek için birden çok VPN ağ geçidi dağıtmanız ve ağlarınız ile Azure arasında birden çok paralel bağlantı oluşturmanız gerekir. Bağlantı seçeneklerine ve topolojisine genel bir bakış için bkz. [yüksek oranda kullanılabilir şirket içi ve ağdan ağa bağlantı](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable).

Etkin/etkin ormanlar arası ve ağdan ağa bağlantılar oluşturmak için, Azure [VPN ağ geçitleri ile etkin/ETKIN S2S VPN bağlantılarını yapılandırma](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) bölümündeki yönergeleri IZLEYEREK Azure VPN Gateway 'i etkin/etkin modda yapılandırın.

> [!Note]  
> * BGP özellikli etkin/etkin mod için yerel ağ geçidine adres eklediğinizde, *BGP eşlerinin yalnızca/32 adreslerini ekleyin*. Daha fazla adres eklerseniz, bu adresler statik yollar olarak değerlendirilir ve BGP rotalarıyla önceliklidir.
> * Azure 'a bağlanan şirket içi ağlarınız için farklı BGP numaraları kullanmanız gerekir. (Aynı ise, şirket içi VPN cihazınız zaten diğer BGP komşuları ile ASN 'yi kullanıyorsa, Sanal ağınızı sayı olarak değiştirmeniz gerekir.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Dağıtımdan sonra bir Azure VPN ağ geçidi türünü değiştirme

Bir Azure sanal ağ geçidi türünü, ilke temelinde doğrudan rota tabanlı ya da başka bir yöntemle değiştiremezsiniz. Önce ağ geçidini silmeniz gerekir. Bundan sonra, IP adresi ve önceden paylaşılan anahtar korunmaz. Daha sonra istediğiniz türde yeni bir ağ geçidi oluşturabilirsiniz. 

Bir ağ geçidini silmek ve oluşturmak için aşağıdaki adımları izleyin:

1. Özgün ağ geçidiyle ilişkili tüm bağlantıları silin.
2. Azure portal, PowerShell veya klasik PowerShell 'i kullanarak ağ geçidini silin: 
   * [Azure portal kullanarak bir sanal ağ geçidini silme](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [PowerShell kullanarak bir sanal ağ geçidini silme](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [PowerShell kullanarak bir sanal ağ geçidini silme (klasik)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. İstenen türde yeni ağ geçidini oluşturmak için [VPN ağ geçidini oluşturma](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway) bölümündeki adımları IZLEYIN ve VPN kurulumunu tamamlayın.

> [!Note]
> Bu işlem yaklaşık 60 dakika sürer.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure VM’leri arasında bağlantı sorunlarını giderme](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

