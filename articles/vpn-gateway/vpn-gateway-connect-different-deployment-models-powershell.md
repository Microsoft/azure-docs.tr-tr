---
title: 'Klasik sanal ağları Azure Resource Manager sanal ağlara bağlama: PowerShell'
description: Klasik sanal ağlar ve Kaynak Yöneticisi sanal ağlar arasında VPN Gateway ve PowerShell kullanarak bir VPN bağlantısı oluşturun.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 7012a696684a4e7bc1d3b52943b11ba413c43037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376497"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>PowerShell kullanarak farklı dağıtım modellerindeki sanal ağları birbirine bağlama

Bu makale, farklı dağıtım modellerinde bulunan kaynakların birbirleriyle iletişim kurmasına izin vermek için klasik sanal ağları Kaynak Yöneticisi sanal ağlara bağlamanıza yardımcı olur. Bu makaledeki adımlar PowerShell kullanır, ancak bu yapılandırmayı bu listeden seçerek Azure portal kullanarak da oluşturabilirsiniz.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Klasik sanal ağı bir Kaynak Yöneticisi VNet 'e bağlamak, VNet 'i şirket içi site konumuna bağlamaya benzer. Her iki bağlantı türü de IPsec/IKE kullanarak güvenli bir tünel sunmak üzere bir VPN ağ geçidi kullanır. Farklı aboneliklerde ve farklı bölgelerde bulunan sanal ağlar arasında bir bağlantı oluşturabilirsiniz. Ayrıca, yapılandırılmış oldukları ağ geçidi dinamik veya rota tabanlı olduğu sürece, zaten şirket içi ağlarda bağlantıları olan VNET 'leri de bağlayabilirsiniz. Sanal ağlar arası bağlantılar hakkında daha fazla bilgi için bu makalenin sonunda yer alan [Sanal ağlar arası bağlantılar hakkında SSS](#faq) bölümünü inceleyin. 

Zaten bir sanal ağ geçidinizin yoksa ve bir tane oluşturmak istemiyorsanız VNet eşleme kullanarak VNet 'iniz bağlamayı göz önünde bulundurmanız gerekebilir. VNet eşlemesi VPN ağ geçidini kullanmaz. Daha fazla bilgi için bkz. [VNet eşlemesi](../virtual-network/virtual-network-peering-overview.md).

## <a name="before-you-begin"></a><a name="before"></a>Başlamadan önce

Aşağıdaki adımlarda, her VNet için dinamik veya yol tabanlı bir ağ geçidi yapılandırmak ve ağ geçitleri arasında bir VPN bağlantısı oluşturmak için gereken ayarlar açıklanmaktadır. Bu yapılandırma statik veya ilke tabanlı ağ geçitlerini desteklemez.

### <a name="prerequisites"></a><a name="pre"></a>Önkoşullar

* Her iki sanal ağ zaten oluşturulmuş. Resource Manager sanal ağı oluşturmanız gerekiyorsa bkz. [kaynak grubu ve sanal ağ oluşturma](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network). Klasik bir sanal ağ oluşturmak için bkz. [Klasik VNET oluşturma](/previous-versions/azure/virtual-network/create-virtual-network-classic).
* VNET 'lerin adres aralıkları birbirleriyle örtüşmez veya ağ geçitlerinin bağlı olabileceği diğer bağlantılar için aralıklardan herhangi biriyle çakışmaz.
* En son PowerShell cmdlet 'lerini yüklediniz. Daha fazla bilgi için bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/) . Hem hizmet yönetimi (SM) hem de Kaynak Yöneticisi (RM) cmdlet 'lerini yüklediğinizden emin olun. 

### <a name="example-settings"></a><a name="exampleref"></a>Örnek ayarlar

Bu değerleri kullanarak bir test ortamı oluşturabilir veya bu makaledeki örnekleri daha iyi anlamak için bunlara bakabilirsiniz.

**Klasik VNet ayarları**

VNet adı = ClassicVNet <br>
Konum = Batı ABD <br>
Sanal ağ adres alanları = 10.0.0.0/24 <br>
Alt ağ-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Yerel ağ adı = RMVNetLocal <br>
GatewayType = Dynamıuting

**Kaynak Yöneticisi VNet ayarları**

VNet adı = RMVNet <br>
Kaynak grubu = RG1 <br>
Sanal ağ IP adresi alanları = 192.168.0.0/16 <br>
Alt ağ-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Konum = Doğu ABD <br>
Ağ Geçidi genel IP adı = gwpıp <br>
Yerel ağ geçidi = ClassicVNetLocal <br>
Sanal ağ geçidi adı = RMGateway <br>
Ağ geçidi IP adresleme yapılandırması = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>Bölüm 1-klasik VNet 'i yapılandırma
### <a name="1-download-your-network-configuration-file"></a>1. ağ yapılandırma dosyanızı indirin
1. PowerShell konsolundaki Azure hesabınızda, yükseltilmiş haklarla oturum açın. Aşağıdaki cmdlet, Azure hesabınız için oturum açma kimlik bilgilerini ister. Oturum açtıktan sonra, Azure PowerShell'de kullanabilmeniz için hesap ayarlarınızı indirir. Klasik hizmet yönetimi (SM) Azure PowerShell cmdlet 'leri bu bölümde kullanılır.

   ```azurepowershell
   Add-AzureAccount
   ```

   Azure aboneliğinizi alın.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Birden fazla aboneliğiniz varsa, kullanmak istediğiniz aboneliği seçin.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Aşağıdaki komutu çalıştırarak Azure ağ yapılandırma dosyanızı dışarı aktarın. Dosyanın konumunu, gerekirse farklı bir konuma dışarı aktarılacak şekilde değiştirebilirsiniz.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Düzenlemek için indirdiğiniz. xml dosyasını açın. Ağ yapılandırma dosyasına bir örnek için, bkz. [ağ yapılandırması şeması](/previous-versions/azure/reference/jj157100(v=azure.100)).

### <a name="2-verify-the-gateway-subnet"></a>2. ağ geçidi alt ağını doğrulayın
**Virtualnetworksites** öğesinde, zaten oluşturulmadıysa sanal ağınıza bir ağ geçidi alt ağı ekleyin. Ağ yapılandırma dosyası ile çalışırken, ağ geçidi alt ağının "GatewaySubnet" olarak adlandırılması ve Azure tarafından tanınıp bir ağ geçidi alt ağı olarak kullanılması gerekır.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Örnek:**

```xml
<VirtualNetworkSites>
  <VirtualNetworkSite name="ClassicVNet" Location="West US">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/24</AddressPrefix>
    </AddressSpace>
    <Subnets>
      <Subnet name="Subnet-1">
        <AddressPrefix>10.0.0.0/27</AddressPrefix>
      </Subnet>
      <Subnet name="GatewaySubnet">
        <AddressPrefix>10.0.0.32/29</AddressPrefix>
      </Subnet>
    </Subnets>
  </VirtualNetworkSite>
</VirtualNetworkSites>
```

### <a name="3-add-the-local-network-site"></a>3. yerel ağ sitesini ekleme
Eklediğiniz yerel ağ sitesi, bağlanmak istediğiniz RM sanal ağını temsil eder. Dosya yoksa bir **Localnetworksites** öğesi ekleyin. Yapılandırmanın bu noktasında, Kaynak Yöneticisi VNet için ağ geçidini henüz oluşturmadığımızda, VPNGatewayAddress geçerli bir genel IP adresi olabilir. Ağ geçidini oluşturduktan sonra, bu yer tutucu IP adresini RM ağ geçidine atanmış doğru genel IP adresiyle değiştirirsiniz.

```xml
<LocalNetworkSites>
  <LocalNetworkSite name="RMVNetLocal">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  </LocalNetworkSite>
</LocalNetworkSites>
```

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. VNet 'i yerel ağ sitesiyle ilişkilendirin
Bu bölümde, VNet 'i bağlamak istediğiniz yerel ağ sitesini belirttik. Bu durumda, daha önce başvurduğunuz Kaynak Yöneticisi sanal ağı budur. Adların eşleştiğinden emin olun. Bu adım bir ağ geçidi oluşturmaz. Ağ geçidinin bağlanacağı yerel ağı belirtir.

```xml
<Gateway>
  <ConnectionsToLocalNetwork>
    <LocalNetworkSiteRef name="RMVNetLocal">
      <Connection type="IPsec" />
    </LocalNetworkSiteRef>
  </ConnectionsToLocalNetwork>
</Gateway>
```

### <a name="5-save-the-file-and-upload"></a>5. dosyayı kaydedin ve karşıya yükleyin
Dosyayı kaydedin ve ardından aşağıdaki komutu çalıştırarak Azure 'a aktarın. Dosya yolunu ortamınız için gerektiği gibi değiştirdiğinizden emin olun.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

İçeri aktarmanın başarılı olduğunu gösteren benzer bir sonuç görürsünüz.

```output
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                                
Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 
```

### <a name="6-create-the-gateway"></a>6. ağ geçidini oluşturun

Bu örneği çalıştırmadan önce, Azure 'un görmeyi beklediği tam adlar için indirdiğiniz ağ yapılandırma dosyasına bakın. Ağ yapılandırma dosyası, klasik sanal ağlarınızın değerlerini içerir. Bazen, dağıtım modellerindeki farklılıklar nedeniyle Azure portal klasik VNet ayarları oluşturulurken, klasik sanal ağlar için adlar ağ yapılandırma dosyasında değiştirilir. Örneğin, ' klasik VNet ' adlı bir klasik VNet oluşturmak için Azure portal kullandıysanız ve bunu ' ClassicRG ' adlı bir kaynak grubunda oluşturduysanız, ağ yapılandırma dosyasında yer alan ad ' Group ClassicRG Classic VNet ' olarak dönüştürülür. Boşluk içeren bir sanal ağın adını belirtirken, değer etrafında tırnak işaretleri kullanın.


Dinamik yönlendirme ağ geçidi oluşturmak için aşağıdaki örneği kullanın:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

**Get-AzureVNetGateway** cmdlet 'ini kullanarak ağ geçidinin durumunu kontrol edebilirsiniz.

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>Bölüm 2-RM VNet ağ geçidini yapılandırma



Önkoşullar zaten bir RM VNet oluşturmuş olduğunuz varsayılmaktadır. Bu adımda, RM VNet için bir VPN ağ geçidi oluşturursunuz. Bu adımları, klasik VNet 'in ağ geçidi için genel IP adresi alınana kadar kullanmayın. 

1. PowerShell konsolunda Azure hesabınızda oturum açın. Aşağıdaki cmdlet, Azure hesabınız için oturum açma kimlik bilgilerini ister. Oturum açtıktan sonra, Azure PowerShell için kullanılabilir olmaları için hesap ayarlarınız indirilir. İsteğe bağlı olarak, tarayıcıda Azure Cloud Shell başlatmak için "TRY It" özelliğini kullanabilirsiniz.

   Azure Cloud Shell kullanıyorsanız, aşağıdaki cmdlet 'i atlayın:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Doğru aboneliği kullandığınızı doğrulamak için aşağıdaki cmdlet 'i çalıştırın:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Birden fazla aboneliğiniz varsa, kullanmak istediğiniz aboneliği belirtin.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Yerel ağ geçidi oluşturma. Sanal bir ağda, yerel ağ geçidi genellikle şirket içi konumunuz anlamına gelir. Bu durumda, yerel ağ geçidi, klasik VNet 'iniz anlamına gelir. Bu bileşene Azure 'un başvurabileceği bir ad verin ve ayrıca adres alanı ön ekini belirtin. Azure, belirttiğiniz IP adresi ön ekini kullanarak hangi trafiğin şirket içi konumunuza gönderileceğini belirler. Daha sonra bu bilgileri daha sonra ayarlamanız gerekirse, ağ geçidinizi oluşturmadan önce değerleri değiştirebilir ve örneği yeniden çalıştırabilirsiniz.
   
   **-Ad** , yerel ağ geçidine başvurmak için atamak istediğiniz addır.<br>
   **-Addresspredüzeltmesini** , klasik VNET 'Iniz Için adres alanıdır.<br>
   **-Gatewayıpaddress** , klasik VNET 'in ağ GEÇIDININ genel IP adresidir. Doğru IP adresini yansıtmak için aşağıdaki örnek metni "n. n. n. n" değiştirdiğinizden emin olun.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Kaynak Yöneticisi VNet için sanal ağ geçidine ayrılacak genel IP adresi isteyin. Kullanmak istediğiniz IP adresini belirtemezsiniz. IP adresi, sanal ağ geçidine dinamik olarak ayrılır. Ancak bu, IP adresinin değiştiği anlamına gelmez. Sanal ağ geçidi IP adresi yalnızca ağ geçidinin silindiği ve yeniden oluşturulması durumunda olduğu zaman değişir. Bu, ağ geçidinin yeniden boyutlandırılması, sıfırlanması veya diğer iç Bakımı/yükseltmeleri üzerinde değişmez.

   Bu adımda, daha sonraki bir adımda kullanılan bir değişken de ayarlayacağız.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Sanal ağınızın bir ağ geçidi alt ağına sahip olduğunu doğrulayın. Ağ geçidi alt ağı yoksa, bir tane ekleyin. Ağ geçidi alt ağının *gatewaysubnet* olarak adlandırıldığından emin olun.
5. Aşağıdaki komutu çalıştırarak ağ geçidi için kullanılan alt ağı alın. Bu adımda, bir sonraki adımda kullanılacak bir değişken de ayarlayacağız.
   
   **-Ad** Kaynak Yöneticisi sanal ağınızın adıdır.<br>
   **-Resourcegroupname** , VNET 'in ilişkilendirildiği kaynak grubudur. Ağ geçidi alt ağının bu VNet için zaten mevcut olması gerekir ve düzgün çalışması için *gatewaysubnet* adlı bir ad verilmelidir.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Ağ geçidi IP adresleme yapılandırmasını oluşturun. Ağ geçidi yapılandırması, kullanılacak alt ağı ve genel IP adresini tanımlar. Ağ geçidi yapılandırmanızı oluşturmak için aşağıdaki örneği kullanın.

   Bu adımda **-SubnetID** ve **-publicıpaddressıd** parametreleri SıRASıYLA alt ağdan ve IP adresi nesnelerinden ID özelliğini geçirmelidir. Basit bir dize kullanamazsınız. Bu değişkenler, genel bir IP istemek için adımında ve alt ağı alma adımını olarak ayarlanır.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Aşağıdaki komutu çalıştırarak Kaynak Yöneticisi sanal ağ geçidini oluşturun. , `-VpnType` *Routebased* olmalıdır. Ağ geçidinin oluşturulması 45 dakika veya daha uzun sürebilir.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. VPN ağ geçidi oluşturulduktan sonra genel IP adresini kopyalayın. Bu ayarı, klasik VNet 'iniz için yerel ağ ayarlarını yapılandırırken kullanırsınız. Genel IP adresini almak için aşağıdaki cmdlet 'i kullanabilirsiniz. Genel IP adresi *, IP 'nin* geri dönüş bölümünde listelenir.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>Bölüm 3-klasik VNet yerel site ayarlarını değiştirme

Bu bölümde, klasik VNet ile çalışırsınız. Kaynak Yöneticisi VNet ağ geçidine bağlanmak için kullanılacak yerel site ayarlarını belirtirken kullandığınız yer tutucu IP adresini değiştirirsiniz. Klasik VNet ile çalıştığınızdan, bilgisayarınızda Azure Cloud Shell Tryıt değil yerel olarak yüklü PowerShell kullanın.

1. Ağ yapılandırma dosyasını dışarı aktarın.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Bir metin düzenleyicisi kullanarak VPNGatewayAddress için değeri değiştirin. Yer tutucu IP adresini Kaynak Yöneticisi ağ geçidinin genel IP adresiyle değiştirin ve değişiklikleri kaydedin.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Değiştirilen ağ yapılandırma dosyasını Azure 'a aktarın.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>Bölüm 4-ağ geçitleri arasında bağlantı oluşturma
Ağ geçitleri arasında bağlantı oluşturmak PowerShell gerektirir. PowerShell cmdlet 'lerinin klasik sürümünü kullanmak için Azure hesabınızı eklemeniz gerekebilir. Bunu yapmak için **Add-AzureAccount** kullanın.

1. PowerShell konsolunda, paylaşılan anahtarınızı ayarlayın. Cmdlet 'leri çalıştırmadan önce, Azure 'un görmeyi beklediği tam adlar için indirdiğiniz ağ yapılandırma dosyasına bakın. Boşluk içeren bir sanal ağın adını belirtirken, değerin etrafında tek tırnak işaretleri kullanın.<br><br>Aşağıdaki örnekte **-vağ** adı klasik VNET 'in adı ve **-localnetworksitename** , yerel ağ sitesi için belirttiğiniz addır. **-Sharedkey** , oluşturduğunuz ve belirlediğiniz bir değerdir. Örnekte, ' abc123 ' kullandık, ancak daha karmaşık bir şeyler oluşturup kullanabilirsiniz. Önemli şey, burada belirttiğiniz değerin, bağlantınızı oluştururken bir sonraki adımda belirttiğiniz değer olması gerekir. Döndürülen **durum: başarılı**' i göstermelidir.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Aşağıdaki komutları çalıştırarak VPN bağlantısını oluşturun:
   
   Değişkenleri ayarlayın.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Bağlantıyı oluşturun. **-ConnectionType** Vnet2Vnet değil IPSec olduğunu unutmayın.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>5. Bölüm-bağlantılarınızı doğrulayın

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Klasik sanal ağınızdan Kaynak Yöneticisi VNet 'e bağlantıyı doğrulamak için

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure portalı

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Kaynak Yöneticisi VNet 'ten klasik VNet 'e bağlantıyı doğrulamak için

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure portalı

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Sanal ağlar arası bağlantılar hakkında SSS

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
