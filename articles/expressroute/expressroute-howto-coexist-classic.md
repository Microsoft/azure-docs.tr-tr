---
title: 'ExpressRoute ve S2S VPN birlikte var olan bağlantıları yapılandırma: klasik'
description: Bu makalede klasik dağıtım modeli için bir arada varolabilen ExpressRoute ve bir Siteden Siteye VPN bağlantısını nasıl yapılandıracağınız anlatılmaktadır.
documentationcenter: na
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 0643ce99ce4ba9328abc3f7a8c8e7061026611b9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031787"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-classic"></a>Birlikte bulunan ExpressRoute bağlantıları ile Siteden Siteye bağlantıları yapılandırma (klasik)
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - Klasik](expressroute-howto-coexist-classic.md)
> 
> 

Bu makalede bir arada ExpressRoute ve siteden siteye VPN bağlantıları yapılandırmanıza yardımcı olur. Siteden Siteye VPN ve ExpressRoute yapılandırma yeteneğine sahip olmanın çeşitli avantajları vardır. ExpressRoute için güvenli bir yük devretme yolu olarak siteden siteye VPN yapılandırabilir veya ExpressRoute aracılığıyla bağlanmayan sitelere bağlanmak için siteden siteye VPN'ler kullanabilirsiniz. Bu makalede iki senaryo için de yapılandırma adımları verilmektedir. Bu makale klasik dağıtım modeli için geçerlidir. Bu yapılandırma portalda kullanılabilir değildir.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure dağıtım modelleri hakkında**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> Aşağıdaki yönergeleri izlemeden önce ExpressRoute bağlantı hatlarının önceden yapılandırılmış olması gerekir. Aşağıdaki adımları izlemeden önce [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-classic.md) ve [yönlendirmeyi yapılandırma](expressroute-howto-routing-classic.md) kılavuzlarını izlediğinizden emin olun.
> 
> 

## <a name="limits-and-limitations"></a>Sınırlar ve sınırlamalar
* **Geçiş yönlendirmesi desteklenmez.** Siteden Siteye VPN aracılığıyla bağlanan yerel ağınız ve ExpressRoute aracılığıyla bağlanan yerel ağınız arasında (Azure aracılığıyla) yönlendirme yapamazsınız.
* **Noktadan siteye desteklenmez.** ExpressRoute’e bağlı aynı VNet için noktadan siteye VPN bağlantılarını etkinleştiremezsiniz. Noktadan siteye VPN ve ExpressRoute aynı VNet’te bir arada varolamaz.
* **Zorlamalı tünel, Siteden Siteye VPN ağ geçidinde etkinleştirilemez.** ExpressRoute aracılığıyla İnternet’e bağlı tüm trafiği yalnızca şirket içi ağınıza geri “zorlayabilirsiniz”.
* **Temel SKU ağ geçidi desteklenmez.** Hem [ExpressRoute ağ geçidi](expressroute-about-virtual-network-gateways.md) hem de [VPN ağ geçidi](../vpn-gateway/vpn-gateway-about-vpngateways.md) için Temel SKU olmayan bir ağ geçidi kullanmanız gerekir.
* **Yalnızca rota tabanlı VPN ağ geçidi desteklenir.** Rota tabanlı [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) kullanmanız gerekir.
* **VPN ağ geçidiniz için statik rota yapılandırılmalıdır.** Yerel ağınız hem ExpressRoute hem de Siteden Siteye VPN’e bağlıysa Siteden Siteye VPN bağlantısını genel İnternet’e yönlendirebilmeniz için yerel ağınızda statik bir rotanın yapılandırılmış olması gerekir.
* **İlk olarak ExpressRoute ağ geçidi yapılandırılmalıdır.** Siteden Siteye VPN ağ geçidini ekleyebilmek için önce ExpressRoute ağ geçidini oluşturmanız gerekir.

## <a name="configuration-designs"></a>Yapılandırma tasarımları
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Siteden siteye VPN’i ExpressRoute için bir yük devretme yolu olarak yapılandırma
Siteden siteye bir VPN bağlantısını ExpressRoute için yedek olarak yapılandırabilirsiniz. Bu yalnızca Azure özel eşleme yoluna bağlı sanal ağlar için geçerlidir. Azure ortak ve Microsoft eşlemeleri aracılığıyla erişilebilen hizmetler için VPN tabanlı yük devretme çözümü yoktur. ExpressRoute bağlantı hattı her zaman birincil bağlantıdır. Veriler yalnızca ExpressRoute bağlantı hattı başarısız olursa, Siteden Siteye VPN üzerinden akar. 

> [!NOTE]
> Her iki yol da aynı olduğunda ExpressRoute bağlantı hattı Siteden Siteye VPN’ye tercih edilse de Azure paketin hedefine yönelik yolu seçmek için en uzun ön ek eşleşmesini kullanır.
> 
> 

![Bir arada var olma](media/expressroute-howto-coexist-classic/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>ExpressRoute aracılığıyla bağlanılmayan sitelere bağlanmak için Siteden Siteye VPN yapılandırma
Ağınızı bazı sitelerin Azure’a Siteden Siteye VPN üzerinden doğrudan ve bazı sitelerin ExpressRoute üzerinden bağlanması için yapılandırabilirsiniz. 

![Bir arada var olma](media/expressroute-howto-coexist-classic/scenario2.jpg)

> [!NOTE]
> Bir sanal ağı geçiş yönlendiricisi olarak yapılandıramazsınız.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Kullanılacak adımları seçme
Bir arada var olabilen bağlantılar yapılandırmak için seçebileceğiniz iki farklı yordam kümesi vardır. Seçtiğiniz yapılandırma yordamı, bağlanmak istediğiniz mevcut bir sanal ağ olup olmadığına veya yeni bir sanal ağ oluşturmak isteyip istememenize bağlıdır.

* Bir VNet’im yok ve bir tane oluşturmam gerekiyor.
  
    Zaten bir sanal ağınız yoksa, bu yordam klasik dağıtım modelini kullanarak yeni bir sanal ağ oluşturmak ve yeni ExpressRoute ve Siteden Siteye VPN bağlantıları oluşturmak için size yol gösterir. Yapılandırmak için, makalenin [Yeni bir sanal ağ ve bir arada var olabilen bağlantılar oluşturmak için](#new) bölümündeki adımları izleyin.
* Zaten bir klasik dağıtım modeli VNet’im var.
  
    Mevcut bir Siteden Siteye VPN bağlantısı veya ExpressRoute bağlantısına sahip bir sanal ağınız zaten olabilir. Makale bölümünde [zaten var olan bir VNet için bir arada var olabilen bağlantılar yapılandırmak için](#add) ağ geçidini silme ve ardından yeni ExpressRoute ve siteden siteye VPN bağlantıları oluşturma size yol gösterir. Yeni bir bağlantı oluşturulurken adımların belirli bir sırayla tamamlanması gerektiğine dikkat edin. Ağ geçitleriniz ve bağlantılarınızı oluşturmak için diğer makalelerdeki yönergeleri kullanmayın.
  
    Bu yordamda, bir arada var olabilen bağlantılar oluşturmak, ağ geçidinizi silmenizi ve ardından yeni ağ geçitlerini yapılandırmanızı gerektirir. Bu, ağ geçidiniz ve bağlantıları silip yeniden oluştururken şirket içi ve dışı bağlantılarınız için kapalı kalma süresi yaşayacağınız ancak VM’leriniz veya hizmetlerinizi yeni bir sanal ağa geçirmeniz gerekmeyeceği anlamına gelir. VM'leriniz ve hizmetleriniz bunu yapmak için yapılandırılmışsa, ağ geçidi yapılandırması sırasında yük dengeleyici üzerinden iletişim kurmaya devam eder.

## <a name="new"></a>Yeni bir sanal ağ ve bir arada var olabilen bağlantılar oluşturmak için
Bu yordamda, bir VNet oluşturma ve bir arada var olabilen Siteden Siteye ve ExpressRoute bağlantıları oluşturma adım adım açıklanmıştır.

1. Azure PowerShell cmdlet’lerinin en yeni sürümünü yüklemeniz gerekir. PowerShell cmdlet'lerini yükleme hakkında daha fazla bilgi için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/overview). Bu yapılandırma için kullanacağınız cmdlet'lerin tanıdıklarınızdan biraz farklı olabileceğini unutmayın. Bu yönergelerde belirtilen cmdlet'leri kullandığınızdan emin olun. 
2. Sanal ağınız için bir şema oluşturun. Yapılandırma şeması hakkında daha fazla bilgi için bkz. [Azure Virtual Network yapılandırma şeması](https://msdn.microsoft.com/library/azure/jj157100.aspx).
   
    Şemanızı oluşturduğunuzda, aşağıdaki değerleri kullandığınızdan emin olun:
   
   * Sanal ağın ağ geçidi alt ağı /27 veya daha kısa bir önek (örneğin /26 veya /25) olmalıdır.
   * Ağ geçidi bağlantı türü "Ayrılmış" olmalıdır.
     
             <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
               <AddressSpace>
                 <AddressPrefix>10.17.159.192/26</AddressPrefix>
               </AddressSpace>
               <Subnets>
                 <Subnet name="Subnet-1">
                   <AddressPrefix>10.17.159.192/27</AddressPrefix>
                 </Subnet>
                 <Subnet name="GatewaySubnet">
                   <AddressPrefix>10.17.159.224/27</AddressPrefix>
                 </Subnet>
               </Subnets>
               <Gateway>
                 <ConnectionsToLocalNetwork>
                   <LocalNetworkSiteRef name="MyLocalNetwork">
                     <Connection type="Dedicated" />
                   </LocalNetworkSiteRef>
                 </ConnectionsToLocalNetwork>
               </Gateway>
             </VirtualNetworkSite>
3. Xml şeması dosyanızı oluşturduktan ve yapılandırdıktan sonra, dosyayı karşıya yükleyin. Bu, sanal ağınızı oluşturur.
   
    Dosyanızı karşıya yüklemek için aşağıdaki cmdlet'i değeri kendi değerinizle değiştirerek kullanın.
   
        Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'
4. <a name="gw"></a>ExpressRoute ağ geçidi oluşturun. GatewaySKU’yu *Standard*, *HighPerformance* veya *UltraPerformance*, GatewayType’ı ise *DynamicRouting* olarak belirttiğinizden emin olun.
   
    Aşağıdaki örneği değerleri kendi değerlerinizle değiştirerek kullanın.
   
        New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance
5. ExpressRoute ağ geçidini ExpressRoute bağlantı hattına bağlayın. Bu adım tamamlandıktan sonra, ExpressRoute aracılığıyla şirket içi ağınız ve Azure arasında bağlantı kurulur.
   
        New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET
6. <a name="vpngw"></a>Ardından, Siteden Siteye VPN ağ geçidinizi oluşturun. GatewaySKU *Standard*, *HighPerformance* veya *UltraPerformance*, GatewayType ise *DynamicRouting* olmalıdır.
   
        New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance
   
    Ağ geçidi kimliği ve ortak IP dahil sanal ağ ağ geçidi ayarlarını almak için `Get-AzureVirtualNetworkGateway` cmdlet'ini kullanın.
   
        Get-AzureVirtualNetworkGateway
   
        GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
        GatewayName          : S2SVPN
        LastEventData        :
        GatewayType          : DynamicRouting
        LastEventTimeStamp   : 5/29/2015 4:41:41 PM
        LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
        LastEventID          : 23002
        State                : Provisioned
        VIPAddress           : 104.43.x.y
        DefaultSite          :
        GatewaySKU           : HighPerformance
        Location             :
        VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
        SubnetId             :
        EnableBgp            : False
        OperationDescription : Get-AzureVirtualNetworkGateway
        OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
        OperationStatus      : Succeeded
7. Bir yerel site VPN ağ geçidi varlığı oluşturun. Bu komut, şirket içi VPN ağ geçidinizi yapılandırmaz. Azure VPN ağ geçidinin bağlanabilmesi için ortak IP ve şirket içi adres alanı gibi yerel ağ geçidi ayarlarını paylaşmanıza olanak tanır.
   
   > [!IMPORTANT]
   > Siteden Siteye VPN için yerel site netcfg içinde tanımlı değildir. Bunun yerine, yerel site parametrelerini belirtmek için bu cmdlet'i kullanmanız gerekir. Bunları portalı veya netcfg dosyasını kullanarak tanımlayamazsınız.
   > 
   > 
   
    Aşağıdaki örneği değerleri kendi değerlerinizle değiştirerek kullanın.
   
        New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>
   
   > [!NOTE]
   > Yerel ağınızda birden çok yol varsa, hepsini bir dizi olarak geçirebilirsiniz.  $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")  
   > 
   > 

    Ağ geçidi kimliği ve ortak IP dahil sanal ağ ağ geçidi ayarlarını almak için `Get-AzureVirtualNetworkGateway` cmdlet'ini kullanın. Aşağıdaki örneğe bakın.

        Get-AzureLocalNetworkGateway

        GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
        GatewayName          : MyLocalNetwork
        IpAddress            : 23.39.x.y
        AddressSpace         : {10.1.2.0/24}
        OperationDescription : Get-AzureLocalNetworkGateway
        OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
        OperationStatus      : Succeeded


1. Yerel VPN cihazınızı yeni ağ geçidine bağlanmak için yapılandırın. VPN cihazınızı yapılandırırken 6. adımda alınan bilgileri kullanın. VPN cihazını yapılandırma hakkında daha fazla bilgi için bkz. [VPN Cihazı Yapılandırma](../vpn-gateway/vpn-gateway-about-vpn-devices.md).
2. Azure’da Siteden Siteye ağ geçidini yerel ağ geçidine bağlayın.
   
    Bu örnekte, `Get-AzureLocalNetworkGateway` çalıştırarak bulabileceğiniz connectedEntityId yerel ağ geçididir. virtualNetworkGatewayId’yi `Get-AzureVirtualNetworkGateway` cmdlet’ini çalıştırarak bulabilirsiniz. Bu adımdan sonra, Siteden Siteye VPN bağlantısı aracılığıyla yerel ağınız ve Azure arasında bağlantı kurulur.

        New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>

## <a name="add"></a>Zaten mevcut bir VNet için bir arada var olabilen bağlantılar yapılandırma
Zaten bir sanal ağınız varsa, ağ geçidi alt ağ boyutunu kontrol edin. Ağ geçidi alt ağı /28 veya /29 ise, önce sanal ağ geçidi silmeniz ve ağ geçidi alt ağı boyutunu artırmanız gerekir. Bu bölümdeki adımlar bunu nasıl yapacağınızı gösterir.

Ağ geçidi alt ağı /27 veya daha büyükse ve sanal ağ ExpressRoute üzerinden bağlanıyorsa, aşağıdaki adımları atlayarak önceki bölümdeki ["6. Adım - Siteden Siteye VPN ağ geçidi oluşturma"](#vpngw) bölümüne geçebilirsiniz.

> [!NOTE]
> Mevcut ağ geçidini sildiğinizde, bu yapılandırma üzerinde çalışırken, yerel şirket sanal ağ bağlantısını kaybeder.
> 
> 

1. Azure Resource Manager PowerShell cmdlet’lerinin en yeni sürümünü yüklemeniz gerekir. PowerShell cmdlet'lerini yükleme hakkında daha fazla bilgi için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/overview). Bu yapılandırma için kullanacağınız cmdlet'lerin tanıdıklarınızdan biraz farklı olabileceğini unutmayın. Bu yönergelerde belirtilen cmdlet'leri kullandığınızdan emin olun. 
2. Mevcut ExpressRoute veya Siteden Siteye VPN ağ geçidini silin. Aşağıdaki cmdlet’i değerleri kendi değerlerinizle değiştirerek kullanın.
   
        Remove-AzureVNetGateway –VnetName MyAzureVNET
3. Sanal ağ şemasını dışarı aktarın. Aşağıdaki PowerShell cmdlet’ini değerleri kendi değerlerinizle değiştirerek kullanın.
   
        Get-AzureVNetConfig –ExportToFile "C:\NetworkConfig.xml"
4. Ağ yapılandırma dosyası şemasını ağ geçidi alt ağı /27 veya daha kısa bir önek (örneğin /26 veya /25) olacak şekilde düzenleyin. Aşağıdaki örneğe bakın. 
   
   > [!NOTE]
   > Sanal ağınızda ağ geçidi alt ağı boyutunu artırmak için yeterli IP adresi kalmadıysa, daha fazla IP adresi alanı eklemeniz gerekir. Yapılandırma şeması hakkında daha fazla bilgi için bkz. [Azure Virtual Network yapılandırma şeması](https://msdn.microsoft.com/library/azure/jj157100.aspx).
   > 
   > 
   
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>
5. Önceki ağ geçidiniz Siteden Siteye VPN ise, ayrıca bağlantı türünü **Ayrılmış** olarak değiştirmeniz gerekir.
   
                 <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
6. Bu noktada, hiçbir ağ geçidi olmayan bir VNet’e sahip olursunuz. Yeni ağ geçitleri oluşturmak ve bağlantılarınızı tamamlamak için, önceki adım kümesinde bulabileceğiniz [4. Adım - Bir ExpressRoute ağ geçidi oluşturma](#gw) bölümüyle devam edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
ExpressRoute hakkında daha fazla bilgi için bkz. [ExpressRoute hakkında SSS](expressroute-faqs.md).

