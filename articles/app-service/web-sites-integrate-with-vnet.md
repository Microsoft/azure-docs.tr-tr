---
title: Uygulamayı Azure sanal ağı ile tümleştirme
description: Uygulamayı Azure sanal ağlarla Azure App Service tümleştirme.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 42391a073d7cb1d7e6850e298c2be32d550bb813
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832077"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Uygulamanızı bir Azure sanal ağıyla tümleştirin

Bu makalede Azure App Service VNet tümleştirme özelliği ve [Azure App Service](./overview.md)uygulamalarla nasıl ayarlanacağı açıklanır. [Azure sanal ağ][VNETOverview] (VNet) Ile, Azure kaynaklarınızın çoğunu internet 'e yönlendirilemeyen bir ağa yerleştirebilirsiniz. VNet tümleştirme özelliği, uygulamalarınızın bir sanal ağ içindeki veya VNet aracılığıyla kaynaklara erişmesine olanak sağlar. VNet tümleştirmesi, uygulamalarınıza özel olarak erişilmesine izin vermez.

Azure App Service, VNet tümleştirme özelliği üzerinde iki çeşitle sahiptir:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNet tümleştirmesini etkinleştir

1. App Service portalında **ağ** Kullanıcı arabirimine gidin. **VNET tümleştirmesi** altında, **yapılandırmak Için buraya tıklayın ' ı** seçin.

1. **VNET Ekle**' yi seçin.

   ![VNet tümleştirmesini seçin][1]

1. Açılır liste, aboneliğinizdeki tüm Azure Resource Manager sanal ağlarını aynı bölgede içerir. Bunun altında, diğer tüm bölgelerdeki Kaynak Yöneticisi sanal ağların bir listesi bulunur. Tümleştirmek istediğiniz sanal ağı seçin.

   ![VNet 'i seçin][2]

   * VNet aynı bölgedeyse, yeni bir alt ağ oluşturun veya var olan bir boş alt ağ seçin.
   * Başka bir bölgedeki VNet seçmek için, site etkin noktadan bir sanal ağ geçidi sağlanmış olmalıdır.
   * Klasik VNet ile tümleştirme için, **sanal ağ** açılan listesini seçmek yerine, **bir klasik VNET 'e bağlanmak Için buraya tıklayın ' ı** seçin. İstediğiniz klasik sanal ağı seçin. Hedef VNet 'in Noktadan siteye etkin olarak sağlanmış bir sanal ağ geçidi zaten olmalıdır.

    ![Klasik VNet seçin][3]

Tümleştirme sırasında uygulamanız yeniden başlatılır. Tümleştirme bittiğinde, Tümleştirdiğiniz VNet hakkındaki ayrıntıları görürsünüz.

## <a name="regional-vnet-integration"></a>Bölgesel VNet tümleştirmesi

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Bölgesel VNet tümleştirmesinin çalışması

App Service uygulamalar çalışan rollerinde barındırılır. Temel ve daha yüksek fiyatlandırma planları, aynı çalışanlar üzerinde çalışan başka müşteri iş yükleri bulunmayan ayrılmış barındırma planlardır. Bölgesel VNet tümleştirmesi, yetkilendirilmiş alt ağdaki adreslerle sanal arabirimler bağlayarak işe yarar. Kimden adresi sanal ağınız içinde olduğundan, VNET 'iniz içindeki bir VM gibi, sanal ağınız içindeki veya bu nesnelerin çoğuna erişebilir. Ağ uygulama, VNet 'iniz üzerinde bir VM çalıştırtan farklı. Bu özellik için bazı ağ özellikleri henüz kullanılamıyor.

![Bölgesel VNet tümleştirmesinin çalışması][5]

Bölgesel VNet tümleştirmesi etkinleştirildiğinde, uygulamanız normal olarak aynı kanallar üzerinden İnternet 'e giden çağrılar yapar. Uygulama özellikleri portalında listelenen giden adresler, uygulamanız tarafından halen kullanılan adreslerdir. Uygulamanız için yapılan değişiklikler hizmet uç noktası güvenliği olan hizmetlere yapılan çağrılardır veya RFC 1918 adresleri sanal ağınıza gider. WEBSITE_VNET_ROUTE_ALL 1 olarak ayarlanırsa, tüm giden trafik sanal ağınıza gönderilebilir.

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` Şu anda Windows kapsayıcılarında desteklenmiyor.
> 

Özelliği, çalışan başına yalnızca bir sanal arabirimi destekler. Çalışan başına bir sanal arabirim, App Service plan başına bir bölgesel VNet tümleştirmesi anlamına gelir. Aynı App Service planındaki tüm uygulamalar aynı VNet tümleştirmesini kullanabilir. Ek bir sanal ağa bağlanmak için bir uygulamaya ihtiyacınız varsa, başka bir App Service planı oluşturmanız gerekir. Kullanılan sanal arabirim, müşterilerin doğrudan erişimine sahip olduğu bir kaynak değildir.

Bu teknolojinin nasıl çalıştığı doğası nedeniyle, VNet tümleştirmesiyle kullanılan trafik Azure ağ Izleyicisi veya NSG akış günlüklerinde gösterilmez.

## <a name="gateway-required-vnet-integration"></a>Ağ Geçidi-gerekli VNet tümleştirmesi

Ağ Geçidi gerekli VNet tümleştirmesi, başka bir bölgedeki VNet 'e veya klasik bir sanal ağa bağlanmayı destekler. Ağ Geçidi-gerekli VNet tümleştirmesi:

* Bir uygulamanın aynı anda yalnızca bir VNet 'e bağlanmasını sağlar.
* App Service bir plan dahilinde tümleştirilecek en fazla beş VNET sağlar.
* Aynı VNet 'in bir App Service planı tarafından kullanılabilecek toplam sayıyı etkilemeden bir App Service planında birden çok uygulama tarafından kullanılmasını sağlar. Aynı App Service planında aynı VNet 'i kullanan altı uygulamanız varsa, kullanılmakta olan bir VNet olarak sayılır.
* Ağ geçidinde SLA nedeniyle% 99,9 SLA 'yı destekler.
* Uygulamalarınızın VNet 'in ile yapılandırıldığı DNS 'i kullanmasına olanak sağlar.
* Bir uygulamaya bağlanmadan önce SSTP Noktadan siteye VPN ile yapılandırılmış bir sanal ağ yolu tabanlı ağ geçidi gerektirir.

Ağ Geçidi için gerekli VNet tümleştirmesini kullanamazsınız:

* Azure ExpressRoute ile bağlantılı bir VNet ile.
* Bir Linux uygulamasından.
* Bir [Windows kapsayıcısından](quickstart-custom-container.md).
* Hizmet uç noktası güvenliği sağlanmış kaynaklara erişmek için.
* ExpressRoute ve Noktadan siteye veya siteden siteye VPN 'Leri destekleyen bir birlikte bulunma ağ geçidi ile.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Azure sanal ağınızda bir ağ geçidi ayarlama ###

Bir ağ geçidi oluşturmak için:

1. Sanal ağınız için [bir ağ geçidi alt ağı oluşturun][creategatewaysubnet] .

1. [VPN ağ geçidini oluşturun][creategateway]. Rota tabanlı VPN türü seçin.

1. [Noktadan siteye adreslerini ayarlayın][setp2saddresses]. Ağ Geçidi temel SKU 'da değilse, ıKEV2 'nin Noktadan siteye yapılandırmasında devre dışı bırakılması ve SSTP 'nin seçili olması gerekir. Noktadan siteye adres alanı, RFC 1918 adres blokları 10.0.0.0/8, 172.16.0.0/12 ve 192.168.0.0/16 adres bloklarından olmalıdır.

Ağ geçidini App Service VNet tümleştirmesiyle kullanılmak üzere oluşturursanız, bir sertifikayı karşıya yüklemeniz gerekmez. Ağ geçidinin oluşturulması 30 dakika sürebilir. Ağ Geçidi sağlanana kadar uygulamanızı VNet ile tümleştiremezsiniz.

### <a name="how-gateway-required-vnet-integration-works"></a>Ağ geçidine gereken VNet tümleştirmesi nasıl çalışacaktır?

Ağ Geçidi gerekli VNet tümleştirmesi, Noktadan siteye VPN teknolojisinin üzerine kurulmuştur. Noktadan siteye VPN 'Ler, uygulamayı barındıran sanal makineye ağ erişimini sınırlar. Uygulamalar yalnızca Karma Bağlantılar veya VNet tümleştirmesi aracılığıyla internet 'e trafik gönderecek şekilde kısıtlanır. Uygulamanız, ağ geçidi için gerekli VNet tümleştirmesini kullanmak üzere portalla yapılandırıldığında, ağ geçidinde ve uygulama tarafında sertifika oluşturmak ve atamak için sizin adınıza karmaşık bir anlaşma yönetilir. Sonuç olarak, uygulamalarınızı barındırmak için kullanılan çalışanların seçilen VNet 'teki sanal ağ geçidine doğrudan bağlanabilmesi mümkün olur.

![Ağ geçidine gereken VNet tümleştirmesi nasıl çalışacaktır?][6]

### <a name="access-on-premises-resources"></a>Şirket içi kaynaklara erişme

Uygulamalar, siteden siteye bağlantıları olan VNET 'ler ile tümleştirerek şirket içi kaynaklara erişebilir. Ağ Geçidi gerekli VNet tümleştirmesi kullanıyorsanız, şirket içi VPN ağ geçidi yollarınızı Noktadan siteye adres bloklarıyla güncelleştirin. Siteden siteye VPN ilk kez ayarlandığında, yapılandırmak için kullanılan betikler yolları doğru şekilde ayarlayabilmelidir. Siteden siteye VPN 'nizi oluşturduktan sonra Noktadan siteye adresleri eklerseniz, rotaları el ile güncelleştirmeniz gerekir. Bu şekilde, ağ geçidi başına farklılık gösteren ve burada açıklanmayan Ayrıntılar açıklanmaktadır. Siteden siteye VPN bağlantısıyla BGP 'yi yapılandırmamış olabilirsiniz.

Bölgesel VNet tümleştirme özelliğinin VNet 'iniz aracılığıyla şirket içi kaynaklara ulaşması için ek yapılandırma gerekmez. Sanal ortamınızı ExpressRoute veya siteden siteye VPN kullanarak şirket içi kaynaklara bağlamanız yeterlidir.

> [!NOTE]
> Ağ Geçidi gerekli VNet tümleştirme özelliği, bir uygulamayı ExpressRoute ağ geçidi olan bir VNet ile tümleştirmez. ExpressRoute ağ geçidi, birlikte [bulunma modunda][VPNERCoex]yapılandırılmış olsa bile, VNET tümleştirmesi çalışmaz. Bir ExpressRoute bağlantısı aracılığıyla kaynaklara erişmeniz gerekiyorsa, bölgesel VNet tümleştirme özelliğini veya VNet 'iniz üzerinde çalışan bir [App Service ortamı][ASE]kullanın.
>
>

### <a name="peering"></a>Eşleme

Bölgesel VNet tümleştirmesiyle eşleme kullanırsanız, ek yapılandırma yapmanız gerekmez.

Ağ Geçidi için gerekli VNet tümleştirmesini eşleme ile kullanırsanız, birkaç ek öğe yapılandırmanız gerekir. Eşleme 'yi uygulamanızla çalışacak şekilde yapılandırmak için:

1. Uygulamanızın bağlandığı VNet 'e bir eşleme bağlantısı ekleyin. Eşleme bağlantısını eklediğinizde **sanal ağ erişimine Izin ver** ' i etkinleştirin ve **Iletilen trafiğe izin ver** ' i seçin ve **ağ geçidi aktarımına izin verin**.
1. Bağlı olduğunuz VNet 'e eşlenmekte olan VNet 'e bir eşleme bağlantısı ekleyin. Eşleme bağlantısını hedef VNet 'e eklediğinizde, **sanal ağ erişimine Izin ver** ' i etkinleştirin ve **Iletilen trafiğe izin ver** ' i seçin ve **uzak ağ geçitlerine izin** verin.
1.   >  Portalda **ağ**  >  **VNET tümleştirmesi** Kullanıcı arabirimine App Service plan bölümüne gidin. Uygulamanızın bağlandığı sanal ağı seçin. Yönlendirme bölümünde, uygulamanızın bağlı olduğu VNet ile ilişkilendirilen VNet 'in adres aralığını ekleyin.

## <a name="manage-vnet-integration"></a>Sanal Ağ Tümleştirmesini Yönetme

VNet ile bağlanma ve bağlantı kesilmesi uygulama düzeyindedir. Birden çok uygulama genelinde VNet tümleştirmesini etkileyebilecek işlemler App Service plan düzeyidir. App > **ağ**  >  **VNET tümleştirme** portalından, VNET 'iniz hakkında ayrıntılı bilgi edinebilirsiniz. **App Service plan**  >  **ağı**  >  **VNET tümleştirme** portalındaki App Service plan düzeyinde benzer bilgiler görebilirsiniz.

VNet tümleştirme örneğinizin uygulama görünümünde gerçekleştirebileceğiniz tek işlem, uygulamanızın o anda bağlı olduğu VNet 'ten bağlantısını kesmesidir. Uygulamanızın VNet bağlantısını kesmek için **bağlantıyı kes**' i seçin. VNet bağlantısı kesildiğinde uygulamanız yeniden başlatılır. Bağlantı kesilmesi VNet 'i değiştirmez. Alt ağ veya ağ geçidi kaldırılmaz. Daha sonra sanal ortamınızı silmek istiyorsanız, öncelikle uygulamanızın VNet bağlantısını kesin ve içindeki kaynakları (örneğin, ağ geçitleri) silin.

App Service planı VNet tümleştirmesi Kullanıcı arabirimi, App Service planınızdaki uygulamalar tarafından kullanılan tüm VNet tümleştirmelerini gösterir. Her VNet hakkındaki ayrıntıları görmek için ilgilendiğiniz sanal ağı seçin. Ağ Geçidi için gereken VNet tümleştirmesi için burada gerçekleştirebileceğiniz iki eylem vardır:

* **Ağ eşitleme**: ağ eşitleme işlemi yalnızca ağ geçidine bağımlı VNET tümleştirme özelliği için kullanılır. Bir eşitleme ağı işleminin gerçekleştirilmesi, sertifikalarınızın ve ağ bilgilerinizin eşitlenmiş olmasını sağlar. VNet 'nizin DNS 'sini ekler veya değiştirirseniz, bir eşitleme ağı işlemi gerçekleştirin. Bu işlem, bu VNet 'i kullanan tüm uygulamaları yeniden başlatır. Farklı aboneliklere ait bir uygulama ve VNET kullanıyorsanız bu işlem çalışmayacaktır.
* **Rotalar ekleme**: yollar ekleme giden trafiği sanal ağınıza yönlendirir.

Örneğe atanan özel IP, **WEBSITE_PRIVATE_IP** ortam değişkeni aracılığıyla sunulur. Kudu konsol Kullanıcı arabirimi, Web uygulaması için kullanılabilen ortam değişkenlerinin listesini de gösterir. Bu IP, tümleşik alt ağın adres aralığından atanır. Bölgesel VNet tümleştirmesi için, WEBSITE_PRIVATE_IP değeri, temsilcili alt ağın adres aralığından bir IP 'dir ve ağ geçidi gerektiren VNet tümleştirmesi için, bu değer sanal ağ geçidinde yapılandırılan Noktadan siteye adres havuzunun giriş aralığından bir IP olur. Bu, Web uygulaması tarafından, kaynaklara sanal ağ üzerinden bağlanmak için kullanılacak olan IP 'dir. 

> [!NOTE]
> WEBSITE_PRIVATE_IP değeri değişikliğe bağlanır. Ancak, tümleştirme alt ağının veya Noktadan siteye adres aralığının adres aralığında bir IP olacaktır, bu nedenle tüm adres aralığından erişime izin vermeniz gerekir.
>

### <a name="gateway-required-vnet-integration-routing"></a>Ağ Geçidi-gerekli VNet tümleştirme yönlendirmesi
VNet 'iniz üzerinde tanımlanan yollar uygulamanızdan sanal ağınıza giden trafiği yönlendirmek için kullanılır. VNet 'e ek giden trafik göndermek için bu adres bloklarını buraya ekleyin. Bu özellik yalnızca ağ geçidi için gerekli VNet tümleştirmesiyle birlikte kullanılabilir. Ağ Geçidi gerekli VNet tümleştirmesinin bölgesel VNet tümleştirmesiyle gerçekleştirdikleri şekilde kullanılması durumunda yol tabloları uygulama trafiğinizi etkilemez.

### <a name="gateway-required-vnet-integration-certificates"></a>Ağ Geçidi-gerekli VNet tümleştirme sertifikaları
Ağ Geçidi gerekli VNet tümleştirmesi etkinleştirildiğinde, bağlantının güvenliğini sağlamak için gerekli bir sertifika alışverişi vardır. Sertifikaların yanı sıra, DNS yapılandırması, rotalar ve ağı tanımlayan diğer benzer şeylerdir.

Sertifikalar veya ağ bilgileri değiştirilirse, **ağı Eşitle**' yi seçin. **Eşitleme ağı**' nı seçtiğinizde, uygulamanız ve sanal ağınız arasında bağlantı için kısa bir kesinti oluşmasına neden olursunuz. Uygulamanız yeniden başlatılana karşın bağlantı kaybı sitenizin düzgün şekilde çalışmamasına neden olabilir.

## <a name="pricing-details"></a>Fiyatlandırma ayrıntıları
Bölgesel VNet tümleştirme özelliğinin, App Service planı fiyatlandırma katmanı ücretlerinden daha fazla kullanım ücreti yoktur.

Ağ Geçidi gerekli VNet tümleştirme özelliğinin kullanımıyla ilgili üç ücret vardır:

* **App Service plan fiyatlandırma katmanı ücretleri**: uygulamalarınızın standart, Premium, PremiumV2 veya PremiumV3 App Service planlarında olması gerekir. Bu maliyetler hakkında daha fazla bilgi için bkz. [App Service fiyatlandırması][ASPricing].
* **Veri aktarımı maliyetleri**: VNET aynı veri merkezinde olsa bile veri çıkışı için ücretlendirilir. Bu ücretler [veri aktarımı fiyatlandırma ayrıntılarında][DataPricing]açıklanmaktadır.
* **VPN Gateway maliyetleri**: Noktadan siteye VPN için gereken sanal ağ geçidi için bir maliyet vardır. Daha fazla bilgi için bkz. [VPN Gateway fiyatlandırması][VNETPricing].

## <a name="troubleshooting"></a>Sorun giderme

> [!NOTE]
> VNET tümleştirmesi, App Service Docker Compose senaryolar için desteklenmez.
> Azure Işlevleri erişim kısıtlamaları özel bir uç nokta varsa yoksayılır.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Otomasyon

CLı desteği bölgesel VNet tümleştirmesi için kullanılabilir. Aşağıdaki komutlara erişmek için [Azure CLI 'yı yüklemelisiniz][installCLI].

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.

az appservice vnet-integration --help

Group
    az appservice vnet-integration : A method that lists the virtual network
    integrations used in an appservice plan.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    list : List the virtual network integrations used in an appservice plan.
```

Bölgesel VNet tümleştirmesi için PowerShell desteği çok fazla kullanılabilir, ancak alt ağ RESOURCEID özelliği dizisiyle genel kaynak oluşturmanız gerekir

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

#Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

#Creation of the VNet integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```


Ağ Geçidi için gerekli VNet tümleştirmesi için, PowerShell kullanarak App Service bir Azure sanal ağı ile tümleştirebilirsiniz. Bir çalıştırma için, bkz. bir [uygulamayı Azure sanal ağına bağlama Azure App Service](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli
[privateendpoints]: networking/private-endpoint.md
