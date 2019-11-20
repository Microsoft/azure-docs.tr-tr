---
title: "Azure ExpressRoute: Microsoft eşlemesi üzerinden S2S VPN 'i yapılandırma"
description: IPSec/IKE siteden siteye VPN ağ geçidi kullanarak bir ExpressRoute Microsoft eşleme bağlantı hattı üzerinden azure'a bağlantısı yapılandırın.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 89c7e398e24fb48c1829dbaa2811f440c5485a60
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038101"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>ExpressRoute Microsoft eşlemesi üzerinde siteden siteye VPN yapılandırma

Bu makalede, bir ExpressRoute özel bağlantı üzerinden şirket içi ağınız ile Azure, sanal ağlar (Vnet'ler) arasında güvenli şifreli bağlantı yapılandırmanıza yardımcı olur. Microsoft eşlemesini seçilen şirket içi ağlarınız ve Azure sanal ağları arasında siteden siteye IPSec/IKE VPN tüneli oluşturmak için kullanabilirsiniz. ExpressRoute üzerinden güvenli bir tünel yapılandırma, gizlilik, yürütmeyi, kimlik doğrulaması ve bütünlük ile veri değişimi için sağlar.

>[!NOTE]
>Siteden siteye VPN ayarlamaya, eşleme Microsoft ayarladığınızda VPN ağ geçidi ve VPN çıkışı için ücretlendirilir. Daha fazla bilgi için [VPN Gateway fiyatlandırması](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a>Mimarisi


  ![bağlantıya genel bakış](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Yüksek kullanılabilirlik ve yedeklilik için bir ExpressRoute bağlantı hattı iki MSEE PE çiftleri birden fazla tünel yapılandırabilir ve tüneller arasında yük dengelemeyi etkinleştirmek.

  ![yüksek kullanılabilirlik seçenekleri](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

Microsoft eşlemesi üzerinden VPN tünelleri, VPN ağ geçidi kullanarak veya Azure Marketi'nden bir uygun ağ sanal Gereci (NVA) kullanılabilir kullanarak sonlandırılabilir. Statik veya dinamik olarak şifrelenmiş bir tünel temel alınan Microsoft eşlemesi için rota exchange sokmadan yolları. Bu makaledeki örneklerde, dinamik olarak şifrelenmiş bir tünel önekleri değişimi için BGP (Microsoft eşlemesi oluşturmak için kullanılan BGP oturumu farklı) kullanılır.

>[!IMPORTANT]
>Şirket içi tarafı için genellikle Microsoft eşlemesi üzerinde DMZ sonlandırılır ve özel eşdüzey hizmet sağlama Çekirdek Ağ bölgenin sonlandırılır. Güvenlik duvarları kullanarak iki bölgeleri ayrılacaktır. Microsoft yalnızca ExpressRoute üzerinden güvenli tüneli etkinleştirmek için eşleme yapılandırıyorsanız, yalnızca Microsoft eşlemesi aracılığıyla tanıtılan ilgi genel IP'ler aracılığıyla filtre unutmayın.
>
>

## <a name="workflow"></a>İş akışı

1. Microsoft, ExpressRoute bağlantı hattı için eşleme yapılandırın.
2. Seçili Azure bölgesel genel Microsoft eşlemesi aracılığıyla şirket içi ağınıza öneklerini.
3. Bir VPN ağ geçidi yapılandırma ve IPSec tünelleri
4. Şirket içi VPN cihazı yapılandırma.
5. Siteden siteye IPSec/IKE bağlantı oluşturun.
6. (İsteğe bağlı) Güvenlik duvarları ve filtreleme, şirket içi VPN cihazında yapılandırın.
7. Test ve IPSec iletişimi bir ExpressRoute devresi üzerinden doğrulayın.

## <a name="peering"></a>1. Microsoft eşlemesini yapılandırma

ExpressRoute üzerinden bir siteden siteye VPN bağlantısı yapılandırmak için ExpressRoute Microsoft eşlemesi yararlanarak gerekir.

* Yeni bir ExpressRoute bağlantı hattını yapılandırın ile başlayın [ExpressRoute önkoşulları](expressroute-prerequisites.md) makalesi ve ardından [oluşturun ve bir ExpressRoute bağlantı hattını değiştirme](expressroute-howto-circuit-arm.md).

* Kullanarak Microsoft eşlemesi zaten bir ExpressRoute devresi ancak Microsoft eşlemesi için yapılandırılmış olmayan, yapılandırma [oluşturun ve bir ExpressRoute bağlantı hattı için eşleme değiştirme](expressroute-howto-routing-arm.md#msft) makalesi.

Bağlantı hattı ve Microsoft eşleme yapılandırıldıktan sonra kolayca kullanarak görüntüleyebileceğiniz **genel bakış** Azure portalında sayfası.

![bağlantı hattı](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="routefilter"></a>2. yol filtrelerini yapılandırma

Rota filtresi, ExpressRoute bağlantı hattınızın Microsoft eşlemesi üzerinden kullanmak istediğiniz hizmetleri tanımlamanızı sağlar. Bu aslında tüm BGP topluluk değerlerinin izin verilenler listesidir. 

![Rota filtresi](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

Bu örnekte, yalnızca içinde dağıtımıdır *Azure Batı ABD 2* bölge. BGP topluluk değeri olan yalnızca tanıtım Azure Batı ABD 2, bölgesel öneklerinin izin vermek için bir rota filtresi kuralının eklenen *12076:51026*. Seçerek izin vermek istediğiniz bölgesel ön ekleri belirttiğiniz **Yönet kural**.

Rota filtresi içinde de rota filtresi uygulandığı ExpressRoute bağlantı hatları seçmeniz gerekir. ExpressRoute bağlantı hatları seçerek seçebileceğiniz **devreler**. Önceki resimde, rota filtresi ExpressRoute bağlantı hattına örnekle ilişkilidir.

### <a name="configfilter"></a>2.1 rota filtresi yapılandırma

Bir rota filtresinde yapılandırın. Adımlar için bkz: [Microsoft eşlemesi için rota filtreleri yapılandırma](how-to-routefilter-portal.md).

### <a name="verifybgp"></a>2.2 BGP yolları doğrulayın

Başarılı bir şekilde Microsoft eşleme ExpressRoute bağlantı hattı oluşturduğunuz ve bir rota filtresinde devre ile ilişkili sonra BGP yolları Msee'ler ile eşleme PE cihazlarda Msee alınan doğrulayabilirsiniz. Doğrulama komutu, PE cihazlarınızın işletim sistemine bağlı olarak değişir.

#### <a name="cisco-examples"></a>Cisco örnekleri

Bu örnek, bir Cisco IOS-XE komutunu kullanır. Örnekte, bir sanal Yönlendirme ve (VRF) örneği iletme eşleme trafiğini yalıtmak için kullanılabilir.

```
show ip bgp vpnv4 vrf 10 summary
```

Aşağıdaki kısmi çıktıda 68 ön eklerin \*. 243.229.34, ASN 12076 (MSEE) ile birlikte alındığını gösterir:

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Komşu tarafından alınan önekleri listesini görmek için aşağıdaki örneği kullanın:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Ön ekleri doğru kümesini aldığını doğrulamak için çapraz-doğrulayabilirsiniz. Aşağıdaki Azure PowerShell komut çıktısı, Microsoft hizmetlerinin her biri için ve her bir Azure bölgesi için eşleme aracılığıyla tanıtılan ön listeler:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="vpngateway"></a>3. VPN ağ geçidini ve IPSec tünellerini yapılandırma

Bu bölümde, IPSec VPN tünelleri, Azure VPN ağ geçidi ile şirket içi VPN cihazınız arasında oluşturulur. Cisco bulut hizmeti yönlendirici (CSR1000) VPN cihazlarının örnekler kullanır.

Aşağıdaki diyagramda, IPSec VPN tünelleri şirket içi VPN cihazı 1 ve Azure VPN ağ geçidi örneği çifti arasında kurulan gösterilmektedir. İki IPSec VPN tüneli 2 şirket içi VPN cihazınız arasında kurulan ve Azure VPN ağ geçidi örneği çifti diyagramda gösterildiği değildir ve yapılandırma ayrıntılarını listelenmez. Ancak, ek VPN tünelleri sahip yüksek kullanılabilirliği artırır.

  ![VPN tünelleri](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

IPSec tünel çifti özel ağ yollarını gönderip almak bir eBGP oturumu kurulur. EBGP oturum IPSec tünel çifti oluşturulduğunda aşağıdaki diyagramda gösterilmiştir:

  ![Tünel çifti üzerinden eBGP oturumları](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Örnek ağ bulunabilen genel bakış Aşağıdaki diyagramda gösterilmiştir:

  ![Örnek ağ](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Azure Resource Manager şablonu örnekleri hakkında

Örneklerde, VPN ağ geçidi ve IPSec tüneli sonlandırmalar bir Azure Resource Manager şablonu kullanarak yapılandırılır. Resource Manager şablonlarını kullanarak yeni ya da Resource Manager şablonu temel anlamak için bkz: [yapısını ve Azure Resource Manager şablonları söz dizimini anlamak](../azure-resource-manager/resource-group-authoring-templates.md). Bu bölümde şablonda bir sıfırdan oluşturur Azure ortamı (VNet). Bununla birlikte, mevcut bir sanal ağ varsa, bu şablonda başvurabilirsiniz. VPN ağ geçidi IPSec/IKE siteden siteye yapılandırmalarla ilgili bilgi sahibi değilseniz bkz [bir siteden siteye bağlantı oluşturma](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Bu yapılandırmayı oluşturmak için Azure Resource Manager şablonlarını kullanma gerekmez. Azure portalı veya PowerShell kullanarak bu yapılandırmayı oluşturabilirsiniz.
>
>

### <a name="variables3"></a>3.1 değişkenleri bildirin.

Bu örnekte, değişken bildirimlerini örnek ağa karşılık gelir. Değişkenleri bildirirken Bu bölümde, ortamınızı yansıtacak şekilde değiştirin.

* Değişken **localAddressPrefix** IPSec tünelleri sonlandırmak için şirket içi IP adreslerinden oluşan bir dizidir.
* **GatewaySku** VPN aktarım hızını belirler. GatewaySku ve vpnType hakkında daha fazla bilgi için bkz. [VPN Gateway yapılandırma ayarları](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Fiyatlandırma için bkz [VPN Gateway fiyatlandırması](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Ayarlama **vpnType** için **RouteBased**.

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premises configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="vnet"></a>3.2 sanal ağ (VNet) oluşturma

Mevcut bir Vnet'i VPN tünellerinin ile ilişkilendirirseniz, bu adımı atlayabilirsiniz.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="ip"></a>3.3 VPN ağ geçidi örneklerine genel IP adresleri atayın.
 
Her bir VPN ağ geçidi örneği için genel bir IP adresi atayın.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="termination"></a>3.4 şirket içi VPN tünel Sonlandırması (yerel ağ geçidi) belirtin

Şirket içi VPN cihazları olarak ifade edilir **yerel ağ geçidi**. Aşağıdaki json kod parçacığında, ayrıca uzak BGP eş ayrıntılarını belirtir:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="creategw"></a>3.5 VPN ağ geçidi oluşturma

Şablonu'nun bu bölümünde, VPN ağ geçidi bir aktif-aktif yapılandırma için gerekli ayarlarla yapılandırır. Aşağıdaki gereksinimleri göz önünde bulundurun:

* VPN ağ geçidi ile oluşturma bir **"RouteBased"** vpntype değeri. VPN ağ geçidi ve şirket VPN arasında BGP yönlendirme etkinleştirmek istiyorsanız, bu ayar zorunludur.
* Etkin-etkin modda iki VPN ağ geçidi örneklerini ve belirli şirket içi cihaz arasında VPN tünelleri oluşturmak için **"activeActive"** parametrenin ayarlanmış **true** Resource Manager şablonunda . Yüksek oranda kullanılabilir bir VPN ağ geçitleri hakkında daha fazla bilgi için bkz: [yüksek oranda kullanılabilir bir VPN ağ geçidi bağlantısı](../vpn-gateway/vpn-gateway-highlyavailable.md).
* EBGP oturumları arasında VPN tünellerinin yapılandırmak için iki farklı Asn'ler iki tarafında belirtmeniz gerekir. Özel bir ASN numaraları belirtmek için daha iyidir. Daha fazla bilgi için [genel bakış, BGP ve Azure VPN ağ geçitleri](../vpn-gateway/vpn-gateway-bgp-overview.md).

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="ipsectunnel"></a>3.6 IPSec tünelleri

Son eylem komut, Azure VPN ağ geçidi ile şirket içi VPN cihazınız arasında IPSec tünelleri oluşturur.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="device"></a>4. Şirket içi VPN cihazını yapılandırma

Azure VPN ağ geçidi, farklı satıcılardan birçok VPN cihazları ile uyumludur. Yapılandırma bilgilerini ve VPN ağ geçidi ile çalışacak şekilde doğrulanmış cihazlar için bkz. [VPN cihazları hakkında](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

VPN Cihazınızı yapılandırırken aşağıdaki öğeler gerekir:

* Paylaşılan bir anahtar. Bu, siteden siteye VPN bağlantınızı oluştururken belirttiğiniz paylaşılan anahtarın aynısıdır. Örneklerde temel bir paylaşılan anahtar kullanılır. Kullanmak için daha karmaşık bir anahtar oluşturmanız önerilir.
* VPN ağ geçidinizin genel IP adresi. Azure Portal, PowerShell veya CLI kullanarak genel IP adresini görüntüleyebilirsiniz. Azure portalını kullanarak VPN ağ geçidinizin genel IP adresini bulmak için sanal ağ geçitleri için gidin ve ağ geçidinizin adına tıklayın.

Genellikle eBGP eşleri (genellikle bir WAN bağlantısı üzerinden) doğrudan bağlanır. Ancak, ExpressRoute Microsoft eşlemesi aracılığıyla IPSec VPN tüneli üzerinden eBGP yapılandırmakta olduğunuz olduğunda birden fazla Yönlendirme etki alanları eBGP eşleri arasında. Kullanım **ebgp multihop** değil ikisi arasındaki eBGP komşu ilişki kurmak için komut-eşleri'doğrudan bağlanan. Ebgp-multihop komut izleyen tamsayıyı BGP paketlerinde TTL değeri belirtir. Komut **maksimum yolları eibgp 2** yük iki BGP yolları arasındaki trafiği Dengeleme etkinleştirir.

### <a name="cisco1"></a>Cisco CSR1000 örneği

Aşağıdaki örnek, bir Hyper-V sanal makinesinde şirket içi VPN cihazı olarak Cisco CSR1000 yapılandırmasını gösterir:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="firewalls"></a>5. VPN cihaz filtrelemeyi ve güvenlik duvarlarını yapılandırma (isteğe bağlı)

Güvenlik duvarını ve gereksinimlerinize göre filtrelemeyi yapılandırın.

## <a name="testipsec"></a>6. IPSec tüneli test edin ve doğrulayın

IPSec tünel durumu Azure VPN ağ geçidi Powershell komutlarıyla doğrulanabilir:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Örnek çıktı:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Bağımsız olarak Azure VPN ağ geçidi örneklerinde tüneller durumunu denetlemek için aşağıdaki örneği kullanın:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Örnek çıktı:

```azurepowershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

Ayrıca, şirket içi VPN cihazınızın tünel durumu kontrol edebilirsiniz.

Cisco CSR1000 örnek:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Örnek çıktı:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

Satır Protokolü sanal Tunnel arabirimi (VTI) üzerinde "IKE Aşama 2 tamamlanana kadar yukarı" değiştirmez. Aşağıdaki komut, güvenlik ilişkisi doğrular:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verifye2e"></a>İç arasında uçtan uca bağlantısını kontrol şirket içi ve Azure sanal ağı

IPSec tünelleri ayarlama ve statik yollar doğru ayarlandığından, uzak BGP eş IP adresine ping atmayı alabiliyor olmanız gerekir:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verifybgp"></a>BGP oturumlarını IPSec üzerinden doğrulayın

Azure VPN ağ geçidi, BGP eşi durumunu doğrulayın:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Örnek çıktı:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

VPN yoğunlaştırıcı şirket eBGP aracılığıyla alınan ağ ön ekleri listesi doğrulamak için "Kaynak" özniteliği tarafından filtreleyebilirsiniz:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

Örnek çıktıda, ASN 65010 VPN şirket içi BGP Otonom sistem numarası ' dir.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Tanıtılan rotaları listesini görmek için:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Örnek çıktı:

```azurepowershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Örneğin, şirket içi Cisco CSR1000:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

Azure VPN ağ geçidini şirket içi Cisco CSR1000 tanıtılan ağların listesini aşağıdaki komutu kullanarak listelenir:

```
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>Sonraki adımlar

* [ExpressRoute için Ağ Performansı İzleyicisini Yapılandırma](how-to-npm.md)

* [Bir sanal ağa bir VPN ağ geçidi bağlantısı var olan bir siteden siteye bağlantı ekleme](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
