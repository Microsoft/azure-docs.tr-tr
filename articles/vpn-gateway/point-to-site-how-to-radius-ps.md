---
title: 'Noktadan siteye ve RADIUS kimlik doğrulaması kullanarak bir bilgisayarı sanal ağa bağlama: PowerShell | Mavisi'
description: P2S ve RADIUS kimlik doğrulaması kullanarak Windows ve OS X istemcilerini bir sanal ağa güvenli bir şekilde bağlayın.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: cherylmc
ms.openlocfilehash: 9d962d3a4757b4c7b2d217f91aaf73d6ad4164d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94964856"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>RADIUS kimlik doğrulaması kullanarak bir sanal ağa Noktadan siteye bağlantı yapılandırma: PowerShell

Bu makalede, RADIUS kimlik doğrulaması kullanan Noktadan siteye bağlantı ile sanal ağ oluşturma gösterilmektedir. Bu yapılandırma yalnızca Kaynak Yöneticisi dağıtım modelinde kullanılabilir.

Noktadan Siteye (P2S) VPN ağ geçidi, ayrı bir istemci bilgisayardan sanal ağınıza güvenli bir bağlantı oluşturmanıza olanak sağlar. Noktadan siteye VPN bağlantıları, bir evden veya bir konferanstan çalışırken olduğu gibi uzak bir konumdan sanal ağınıza bağlanmak istediğinizde faydalıdır. P2S VPN ayrıca, bir sanal ağa bağlanması gereken yalnızca birkaç istemciniz olduğunda Siteden Siteye VPN yerine kullanabileceğiniz yararlı bir çözümüdür.

Windows ve Mac cihazlardan bir P2S VPN bağlantısı başlatılır. Bağlanma istemcileri aşağıdaki kimlik doğrulama yöntemlerini kullanabilir: 

* RADIUS sunucusu
* Yerel sertifika kimlik doğrulamasını VPN Gateway
* Yerel Azure Active Directory kimlik doğrulaması (yalnızca Windows 10)

Bu makale, RADIUS sunucusu kullanarak kimlik doğrulamasıyla bir P2S yapılandırması yapılandırmanıza yardımcı olur. Bunun yerine, üretilen sertifikaları ve VPN Gateway yerel sertifika kimlik doğrulamasını kullanarak kimlik doğrulaması yapmak istiyorsanız, bkz. [VPN Gateway yerel sertifika kimlik doğrulaması kullanarak VNET 'e Noktadan siteye bağlantı yapılandırma](vpn-gateway-howto-point-to-site-rm-ps.md) veya Azure Active Directory kimlik doğrulaması Için [P2S OpenVPN protokolü bağlantıları Için Azure Active Directory kiracı oluşturma](openvpn-azure-ad-tenant.md) .

![Bir RADIUS sunucusu kullanarak kimlik doğrulaması ile P2S yapılandırmasını gösteren diyagram.](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Noktadan Siteye bağlantılar için bir VPN cihazına veya genel kullanıma yönelik bir IP adresine gerek yoktur. P2S, VPN bağlantısını SSTP (Güvenli Yuva Tünel Protokolü), OpenVPN veya Ikev2 üzerinden oluşturur.

* SSTP yalnızca Windows istemci platformlarında desteklenen bir TLS tabanlı VPN tünelidir. Güvenlik duvarlarından geçebildiği için, herhangi bir yerden Azure’a bağlanmak için ideal bir seçenektir. Sunucu tarafında 1.0, 1.1 ve 1.2 SSTP sürümlerini destekliyoruz. Kullanılacak sürüm, istemci tarafından belirlenir. Windows 8.1 ve sonraki sürümlerinde, SSTP'de varsayılan olarak 1.2 kullanılır.

* Bir SSL/TLS tabanlı VPN protokolü olan OpenVPN® protokolü. Çoğu güvenlik duvarı, TLS tarafından kullanılan TCP bağlantı noktası 443 giden ' ü açık olduğundan, bir TLS VPN çözümü, güvenlik duvarlarını sızma edebilir. OpenVPN, Android, iOS (sürüm 11,0 ve üzeri), Windows, Linux ve Mac cihazları (OSX sürümleri 10,13 ve üzeri) ile bağlantı kurmak için kullanılabilir.

* IKEv2 VPN, standart tabanlı bir IPsec VPN çözümüdür. IKEv2 VPN, Mac cihazlardan (OSX sürüm 10.11 ve üzeri) bağlantı kurmak için kullanılabilir.

P2S bağlantıları aşağıdakileri gerektirir:

* RouteBased VPN ağ geçidi. 
* Kullanıcı kimlik doğrulamasını işleyecek bir RADIUS sunucusu. RADIUS sunucusu şirket içinde veya Azure VNet 'te dağıtılabilir. Ayrıca, yüksek kullanılabilirlik için iki RADIUS sunucusu da yapılandırabilirsiniz.
* Sanal ağa bağlanacak Windows cihazları için bir VPN istemcisi yapılandırma paketi. VPN istemcisi yapılandırma paketi, bir VPN istemcisinin P2S üzerinden bağlanması için gereken ayarları sağlar.

## <a name="about-active-directory-ad-domain-authentication-for-p2s-vpns"></a><a name="aboutad"></a>P2S VPN 'Ler için Active Directory (AD) etki alanı kimlik doğrulaması hakkında

AD etki alanı kimlik doğrulaması, kullanıcıların kuruluş etki alanı kimlik bilgilerini kullanarak Azure 'da oturum açmasına olanak tanır. AD sunucusu ile tümleşen bir RADIUS sunucusu gerektirir. Kuruluşlar, var olan RADIUS dağıtımlarından da faydalanabilir.
 
RADIUS sunucusu şirket içinde veya Azure VNet 'te bulunabilir. Kimlik doğrulama sırasında, VPN ağ geçidi bir geçişli olarak davranır ve kimlik doğrulama iletilerini RADIUS sunucusu ile bağlanan cihaz arasında ileri ve geri iletir. VPN ağ geçidinin RADIUS sunucusuna ulaşabilmesi önemlidir. RADIUS sunucusu şirket içinde bulunuyorsa, Azure 'dan şirket içi siteye bir VPN siteden siteye bağlantı gerekir.

Active Directory dışında, bir RADIUS sunucusu diğer dış kimlik sistemleriyle de tümleştirilebilir. Bu, MFA seçenekleri de dahil olmak üzere noktadan siteye VPN 'Ler için çok sayıda kimlik doğrulama seçeneği açar. İle tümleştiği kimlik sistemlerinin listesini almak için RADIUS sunucusu satıcı belgelerinize bakın.

![Bağlantı diyagramı-RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Yalnızca bir VPN siteden siteye bağlantı, şirket içi bir RADIUS sunucusuna bağlanmak için kullanılabilir. ExpressRoute bağlantısı kullanılamaz.
>
>

## <a name="before-beginning"></a><a name="before"></a>Başlamadan önce

Azure aboneliğiniz olduğunu doğrulayın. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial) için kaydolabilirsiniz.

### <a name="working-with-azure-powershell"></a>Azure PowerShell çalışma

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Örnek değerler

Örnek değerleri kullanarak bir test ortamı oluşturabilir veya bu makaledeki örnekleri daha iyi anlamak için bu değerlere bakabilirsiniz. İzlenecek yol olarak adımları kullanıp değerleri değiştirmeden uygulayabilir veya ortamınızı yansıtacak şekilde değiştirebilirsiniz.

* **Ad: VNet1**
* **Adres alanı: 192.168.0.0/16** ve **10.254.0.0/16**<br>Bu örnekte, bu yapılandırmanın birden çok adres alanıyla çalıştığını göstermek için birden fazla adres alanı kullanıyoruz. Ancak, bu yapılandırma için birden çok adres alanı gerekli değildir.
* **Alt ağ adı: FrontEnd**
  * **Alt ağ adres aralığı: 192.168.1.0/24**
* **Alt ağ adı: BackEnd**
  * **Alt ağ adres aralığı: 10.254.1.0/24**
* **Alt ağ adı: GatewaySubnet**<br>VPN ağ geçidinin çalışması için Alt Ağ adı olarak *GatewaySubnet*'in kullanılması zorunludur.
  * **Ağ Geçidi Alt Ağ adres aralığı: 192.168.200.0/24** 
* **VPN istemcisi adres havuzu: 172.16.201.0/24**<br>Sanal ağa, bu Noktadan Siteye bağlantıyı kullanarak bağlanan VPN istemcileri, VPN istemci adresi havuzundan bir IP adresi alır.
* **Abonelik:** Birden fazla aboneliğiniz varsa doğru aboneliği kullandığınızdan emin olun.
* **Kaynak Grubu: TestRG**
* **Konum: Doğu ABD**
* **DNS sunucusu:** sanal ağınız için ad çözümlemesi için kullanmak istediğiniz DNS sunucusunun IP adresi. (isteğe bağlı)
* **Ağ Geçidi Adı: Vnet1GW**
* **Ortak IP adı: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="1-set-the-variables"></a><a name="signin"></a>1. değişkenleri ayarlayın

Kullanmak istediğiniz değişkenleri bildirin. Aşağıdaki örneği kullanın ve gerektiğinde, değerleri kendi değerlerinizle değiştirin. Alıştırma sırasında herhangi bir noktada PowerShell/Cloud Shell oturumunuzu kapatırsanız, değişkenleri yeniden bildirmek için değerleri kopyalamanız ve yeniden yapıştırmanız yeterlidir.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="2-create-the-resource-group-vnet-and-public-ip-address"></a>2. <a name="vnet"></a> kaynak grubu, VNET ve genel IP adresi oluşturma

Aşağıdaki adımlar kaynak grubunda üç alt ağa sahip bir kaynak grubu ve bir sanal ağ oluşturur. Değerleri değiştirirken, ağ geçidi alt ağını özel olarak "GatewaySubnet" olarak yazmanız önemlidir. Başka bir ad belirtirseniz, ağ geçidi oluşturma işlemi başarısız olur;

1. Bir kaynak grubu oluşturun.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name "TestRG" -Location "East US"
   ```
2. Sanal ağ için alt ağ yapılandırmalarını oluşturup *FrontEnd*, *BackEnd* ve *GatewaySubnet* olarak adlandırın. Bu ön ekler bildirdiğiniz sanal adres alanının parçası olmalıdır.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
   $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
   ```
3. Sanal ağı oluşturun.

   Bu örnekte, -DnsServer sunucu parametresi isteğe bağlıdır. Bir değer belirtildiğinde yeni bir DNS sunucusu oluşturulmaz. Belirttiğiniz DNS sunucusu IP adresi, sanal ağınızdan bağlandığınız kaynakların adlarını çözümleyebilen bir DNS sunucusu olmalıdır. Bu örnek için özel bir IP adresi kullandık, ancak DNS sunucunuzun IP adresi muhtemelen bu değildir. Kendi değerlerinizi kullandığınızdan emin olun. Belirttiğiniz değer, P2S bağlantısı tarafından değil, VNet 'e dağıttığınız kaynaklar tarafından kullanılır.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Bir VPN ağ geçidinin genel bir IP adresi olmalıdır. İlk olarak IP adresi kaynağını istemeniz, sonra sanal ağ geçidinizi oluştururken bu kaynağa başvurmanız gerekir. VPN ağ geçidi oluşturulurken, IP adresi kaynağa dinamik olarak atanır. VPN Gateway hizmeti şu anda yalnızca *Dinamik* Genel IP adresi ayırmayı desteklemektedir. Statik bir Genel IP adresi ataması isteğinde bulunamazsınız. Ancak, bu durum IP adresinin VPN ağ geçidinize atandıktan sonra değiştiği anlamına gelmez. Genel IP adresi, yalnızca ağ geçidi silinip yeniden oluşturulduğunda değişir. VPN ağ geçidiniz üzerinde gerçekleştirilen yeniden boyutlandırma, sıfırlama veya diğer iç bakım/yükseltme işlemleri sırasında değişmez.

   Dinamik olarak atanan bir genel IP adresi istemek için değişkenleri belirtin.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-set-up-your-radius-server"></a>3. <a name="radius"></a> RADIUS sunucunuzu ayarlama

Sanal ağ geçidini oluşturmadan ve yapılandırmadan önce, RADIUS sunucunuz kimlik doğrulaması için doğru şekilde yapılandırılmalıdır.

1. Dağıtılan bir RADIUS sunucunuz yoksa, bir tane dağıtın. Dağıtım adımları için, RADIUS satıcınız tarafından sunulan kurulum kılavuzuna bakın.  
2. VPN ağ geçidini YARıÇAP üzerinde bir RADIUS istemcisi olarak yapılandırın. Bu RADIUS istemcisini eklerken, oluşturduğunuz sanal ağ GatewaySubnet 'i belirtin. 
3. RADIUS sunucusu kurulduktan sonra, RADIUS sunucusunun IP adresini ve RADIUS istemcilerinin RADIUS sunucusuyla konuşmak için kullanması gereken paylaşılan gizliliği alın. RADIUS sunucusu Azure sanal ağı 'nda ise, RADIUS sunucusu sanal makinesinin CA IP 'sini kullanın.

[Ağ Ilkesi sunucusu (NPS)](/windows-server/networking/technologies/nps/nps-top) MAKALESI, ad etki alanı kimlik doğrulaması Için BIR Windows RADIUS sunucusu (NPS) yapılandırma hakkında rehberlik sağlar.

## <a name="4-create-the-vpn-gateway"></a>4. <a name="creategw"></a> VPN Gateway oluşturma

Sanal ağınız için VPN Gateway 'i yapılandırın ve oluşturun.

* -GatewayType ' VPN ' olmalıdır ve-VpnType değeri ' RouteBased ' olmalıdır.
* Bir VPN ağ geçidinin tamamlanması, seçtiğiniz [ağ geçidi SKU 'suna](vpn-gateway-about-vpn-gateway-settings.md#gwsku)bağlı olarak 45 dakika sürebilir   .

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## <a name="5-add-the-radius-server-and-client-address-pool"></a>5. <a name="addradius"></a> RADIUS sunucusunu ve istemci adres havuzunu ekleme
 
* -RadiusServer adı veya IP adresi ile belirtilebilir. Adı belirtirseniz ve sunucu şirket içinde yer alıyorsa, VPN Gateway adı çözemeyebilir. Bu durumda, sunucunun IP adresini belirtmek daha iyidir. 
* -RadiusSecret, RADIUS sunucunuzda yapılandırılanla eşleşmelidir.
* -VpnClientAddressPool, bağlanan VPN istemcilerinin bir IP adresi aldığı aralıktır. Bağlantıyı kuracağınız şirket içi konum veya bağlanmak istediğiniz sanal ağ ile çakışmayan özel bir IP adresi aralığı kullanın. Yapılandırılmış büyük bir adres havuzuna sahip olduğunuzdan emin olun.  

1. RADIUS parolası için güvenli bir dize oluşturun.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. RADIUS parolasını girmeniz istenir. Girdiğiniz karakterler görüntülenmeyecek ve bunun yerine "*" karakteri ile değiştirilmeyecektir.

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. VPN istemcisi adres havuzunu ve RADIUS sunucusu bilgilerini ekleyin.

   SSTP yapılandırması için:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   OpenVPN® yapılandırmalarına yönelik:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "OpenVPN" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```


   Ikev2 yapılandırması için:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   SSTP + Ikev2 için

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   **İki** RADIUS sunucusu belirtmek için aşağıdaki sözdizimini kullanın. **-VpnClientProtocol** değerini gereken şekilde değiştirin

    ```azurepowershell-interactive
    $radiusServer1 = New-AzRadiusServer -RadiusServerAddress 10.1.0.15 -RadiusServerSecret $radiuspd -RadiusServerScore 30
    $radiusServer2 = New-AzRadiusServer -RadiusServerAddress 10.1.0.16 -RadiusServerSecret $radiuspd -RadiusServerScore 1

    $radiusServers = @( $radiusServer1, $radiusServer2 )

    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $actual -VpnClientAddressPool 201.169.0.0/16 -VpnClientProtocol "IkeV2" -RadiusServerList $radiusServers
    ```

## <a name="6-download-the-vpn-client-configuration-package-and-set-up-the-vpn-client"></a>6. <a name="vpnclient"></a> VPN istemcisi yapılandırma paketini indirme ve VPN istemcisini ayarlama

VPN istemci yapılandırması, cihazların bir P2S bağlantısı üzerinden bir sanal ağa bağlanmasına olanak sağlar. VPN istemcisi yapılandırma paketi oluşturmak ve VPN istemcisini ayarlamak için bkz. [RADIUS kimlik doğrulaması IÇIN VPN Istemci yapılandırması oluşturma](point-to-site-vpn-client-configuration-radius.md).

## <a name="7-connect-to-azure"></a><a name="connect"></a>7. Azure 'a bağlanın

### <a name="to-connect-from-a-windows-vpn-client"></a>Windows VPN istemcisinden bağlanmak için

1. İstemci bilgisayarda sanal ağınıza bağlanmak için VPN bağlantılarında gezinin ve oluşturduğunuz VPN bağlantısını bulun. Bu VPN bağlantısı sanal ağınızla aynı ada sahiptir. Etki alanı kimlik bilgilerinizi girin ve ' Bağlan 'a tıklayın. Yükseltilmiş haklar isteyen bir açılır ileti görüntülenir. Kabul edin ve kimlik bilgilerini girin.

   ![VPN istemcisinin Azure’a bağlanması](./media/point-to-site-how-to-radius-ps/client.png)
2. Bağlantınız kurulur.

   ![Bağlantı kuruldu](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Mac VPN istemcisinden Bağlan

Ağ iletişim kutusunda kullanmak istediğiniz istemci profilini bulup **Bağlan**’a tıklayın.

  ![Mac bağlantısı](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>Bağlantınızı doğrulamak için

1. VPN bağlantınızın etkin olduğunu doğrulamak için, yükseltilmiş bir komut istemi açın ve *ipconfig/all* komutunu çalıştırın.
2. Sonuçlara bakın. Aldığınız IP adresinin, yapılandırmanızda belirttiğiniz Noktadan Siteye VPN İstemcisi Adres Havuzu'ndaki adreslerden biri olduğuna dikkat edin. Sonuçları şu örneğe benzer:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

P2S bağlantısının sorunlarını gidermek için bkz. [Azure Noktadan siteye bağlantıları sorunlarını giderme](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Sanal makineye bağlanma

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Sanal ağ için DNS sunucusu IP adresleri belirtildikten sonra VPN istemci yapılandırma paketinin oluşturulduğunu doğrulayın. DNS sunucusu IP adreslerini güncelleştirdiyseniz, yeni bir VPN istemci yapılandırma paketi oluşturup yükleyin.

* 'ipconfig' seçeneğini kullanarak bağlantıyı kurduğunuz bilgisayardaki Ethernet bağdaştırıcısına atanmış IPv4 adresini denetleyin. IP adresi bağlanacağınız sanal ağın adres aralığında veya VPNClientAddressPool adres aralığında ise, bu durum çakışan bir adres alanı olarak adlandırılır. Adres alanınız bu şekilde çakıştığında, ağ trafiği Azure’a ulaşmaz ve yerel ağda kalır.

## <a name="faq"></a><a name="faq"></a>SSS

Bu SSS, RADIUS kimlik doğrulaması kullanılarak P2S için geçerlidir

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Daha fazla bilgi için bkz. [Sanal Makineler](../index.yml). Ağ ve sanal makineler hakkında daha fazla bilgi edinmek için, bkz. [Azure ve Linux VM ağına genel bakış](../virtual-machines/network-overview.md).
