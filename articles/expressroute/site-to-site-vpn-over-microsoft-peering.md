---
title: "Azure ExpressRoute: Microsoft eşlemesi üzerinden S2S VPN 'i yapılandırma"
description: Siteden siteye VPN ağ geçidi kullanarak bir ExpressRoute Microsoft eşlemesi devresi üzerinden Azure 'a IPSec/ıKE bağlantısı yapılandırın.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 366f27a0e2a22e9aa10dda20e105bf644255bdd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89393148"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>ExpressRoute Microsoft eşlemesi üzerinden siteden siteye VPN yapılandırma

Bu makale, bir ExpressRoute özel bağlantısı üzerinden şirket içi ağınız ile Azure sanal ağlarınız (sanal ağlar) arasında güvenli şifreli bağlantı yapılandırmanıza yardımcı olur. Seçtiğiniz şirket içi ağlar ve Azure sanal ağları arasında siteden siteye IPSec/ıKE VPN tüneli oluşturmak için Microsoft eşlemesini kullanabilirsiniz. ExpressRoute üzerinden güvenli bir tünel yapılandırmak, gizlilik, yeniden yürütme, özgünlük ve bütünlük ile veri değişimine izin verir.

>[!NOTE]
>Microsoft eşlemesi üzerinden siteden siteye VPN 'yi ayarlarken VPN ağ geçidi ve VPN çıkışı için ücretlendirilirsiniz. Daha fazla bilgi için bkz. [VPN Gateway fiyatlandırması](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a><a name="architecture"></a>Mimari


  ![bağlantıya genel bakış](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Yüksek kullanılabilirlik ve artıklık için, bir ExpressRoute bağlantı hattının iki MSEE-PE çifti üzerinde birden fazla tünel yapılandırabilir ve tüneller arasında yük dengelemeyi etkinleştirebilirsiniz.

  ![yüksek kullanılabilirlik seçenekleri](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

Microsoft eşlemesi üzerinden VPN tünelleri, VPN Gateway ya da Azure Marketi aracılığıyla kullanılabilen uygun bir ağ sanal gereci (NVA) kullanılarak sonlandırılabilir. Route Exchange 'i temel alınan Microsoft eşlemesine yönlendirmeksizin, şifrelenmiş tünellere statik veya dinamik olarak yollar gönderebilirsiniz. Bu makaledeki örneklerde, BGP (Microsoft eşlemesi oluşturmak için kullanılan BGP oturumundan farklı), şifreli tüneller üzerinden dinamik olarak ön ekleri değiş tokuş etmek için kullanılır.

>[!IMPORTANT]
>Şirket içi tarafında, genellikle Microsoft eşlemesi DMZ tarihinde sonlandırılır ve özel eşleme çekirdek ağ bölgesinde sonlandırılır. İki bölge güvenlik duvarları kullanılarak dilimlenebilir. ExpressRoute üzerinden güvenli tünel oluşturmayı etkinleştirmek için özel olarak Microsoft eşlemesini yapılandırıyorsanız, yalnızca Microsoft eşlemesi aracılığıyla tanıtılan genel IP 'Lerin yalnızca genel IP 'Leri ile filtreleneceğini unutmayın.
>
>

## <a name="workflow"></a><a name="workflow"></a>İş akışı

1. ExpressRoute devreniz için Microsoft eşlemesini yapılandırın.
2. Seçili Azure bölgesel ortak öneklerini, Microsoft eşlemesi aracılığıyla şirket içi ağınıza duyurur.
3. VPN ağ geçidi yapılandırma ve IPSec tünelleri oluşturma
4. Şirket içi VPN cihazını yapılandırın.
5. Siteden siteye IPSec/ıKE bağlantısı oluşturun.
6. Seçim Şirket içi VPN cihazında güvenlik duvarlarını/filtrelemeyi yapılandırın.
7. ExpressRoute bağlantı hattı üzerinden IPSec iletişimini test edin ve doğrulayın.

## <a name="1-configure-microsoft-peering"></a><a name="peering"></a>1. Microsoft eşlemesini yapılandırma

ExpressRoute üzerinden siteden siteye VPN bağlantısı yapılandırmak için ExpressRoute Microsoft eşlemesi ' ne sahip olmanız gerekir.

* Yeni bir ExpressRoute devresini yapılandırmak için [ExpressRoute önkoşulları](expressroute-prerequisites.md) makalesiyle başlayın ve ardından [bir ExpressRoute bağlantı hattı oluşturup değiştirin](expressroute-howto-circuit-arm.md).

* Zaten bir ExpressRoute devreniz varsa, ancak Microsoft eşlemesi yapılandırılmadıysa, [bir ExpressRoute bağlantı hattı Için oluşturma ve değiştirme eşlemesini](expressroute-howto-routing-arm.md#msft) kullanarak Microsoft eşlemesini yapılandırın.

Devre ve Microsoft eşlemeyi yapılandırdıktan sonra, Azure portal **genel bakış** sayfasını kullanarak kolayca görüntüleyebilirsiniz.

![bilgisayarına](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="2-configure-route-filters"></a><a name="routefilter"></a>2. yol filtrelerini yapılandırma

Rota filtresi, ExpressRoute bağlantı hattınızın Microsoft eşlemesi üzerinden kullanmak istediğiniz hizmetleri tanımlamanızı sağlar. Bu aslında tüm BGP topluluk değerlerinin izin verilenler listesidir. 

![yol filtresi](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

Bu örnekte, dağıtım yalnızca *Azure Batı ABD 2* bölgesidir. Yalnızca Azure Batı ABD 2 bölgesel ön eklerin tanıtımına izin vermek için bir yol filtresi kuralı eklenmiştir. Bu, BGP topluluk değeri *12076:51026*' dir. **Kural Yönet**' i seçerek izin vermek istediğiniz bölgesel önekleri belirtirsiniz.

Yol filtresi içinde, yol filtresinin uygulandığı ExpressRoute devrelerini de seçmeniz gerekir. **Devre Ekle**' yi seçerek ExpressRoute devreleri ' nı seçebilirsiniz. Önceki şekilde, yol filtresi örnek ExpressRoute bağlantı hattı ile ilişkilendirilir.

### <a name="21-configure-the-route-filter"></a><a name="configfilter"></a>2,1 yol filtresini yapılandırma

Bir rota filtresi yapılandırın. Adımlar için bkz. [Microsoft eşlemesi için yol filtrelerini yapılandırma](how-to-routefilter-portal.md).

### <a name="22-verify-bgp-routes"></a><a name="verifybgp"></a>2,2 BGP yollarını doğrulama

ExpressRoute bağlantı hattı üzerinden Microsoft eşlemesini başarıyla oluşturduktan ve devre ile bir yol filtresi ilişkilendirdikten sonra, MSEE ile eşleme olan PE cihazlarında MSEE 'tan alınan BGP yollarını doğrulayabilirsiniz. Doğrulama komutu, PE cihazlarınızın işletim sistemine bağlı olarak değişir.

#### <a name="cisco-examples"></a>Cisco örnekleri

Bu örnek, Cisco IOS-XE komutunu kullanır. Örnekte, eşleme trafiğini yalıtmak için bir sanal Yönlendirme ve iletme (VRF) örneği kullanılır.

```
show ip bgp vpnv4 vrf 10 summary
```

Aşağıdaki kısmi çıktı, 68 ön eklerin komşuyla alındığını gösterir \* . 243.229.34, ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Komşudan alınan ön eklerin listesini görmek için aşağıdaki örneği kullanın:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Doğru önek kümesini aldığınızı onaylamak için çapraz doğrulama yapabilirsiniz. Aşağıdaki Azure PowerShell komut çıktısı, her bir hizmet ve Azure bölgesinin her biri için Microsoft eşlemesi aracılığıyla tanıtılan ön ekleri listeler:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="3-configure-the-vpn-gateway-and-ipsec-tunnels"></a><a name="vpngateway"></a>3. VPN ağ geçidini ve IPSec tünellerini yapılandırma

Bu bölümde, Azure VPN ağ geçidi ve şirket içi VPN cihazı arasında IPSec VPN tünelleri oluşturulur. Örnekler Cisco Cloud Service yönlendirici (CSR1000) VPN cihazları kullanır.

Aşağıdaki diyagramda, şirket içi VPN cihazı 1 ile Azure VPN ağ geçidi örnek çifti arasında kurulu IPSec VPN tünelleri gösterilmektedir. Şirket içi VPN cihazı 2 ile Azure VPN ağ geçidi örnek çifti arasında oluşturulan iki IPSec VPN tüneli diyagramda gösterilmiyor ve yapılandırma ayrıntıları listelenmez. Ancak, ek VPN tünellerinin olması yüksek kullanılabilirliği artırır.

  ![VPN tünelleri](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

IPSec tünel çiftinin üzerinde, Exchange özel ağ yollarına bir eBGP oturumu oluşturulur. Aşağıdaki diyagramda, IPSec tünel çiftinin üzerinde sağlanan eBGP oturumu gösterilmektedir:

  ![Tünel çifti üzerinden eBGP oturumları](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Aşağıdaki diyagramda örnek ağa soyut genel bakış gösterilmektedir:

  ![örnek ağ](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Azure Resource Manager şablonu örnekleri hakkında

Örneklerde VPN Gateway ve IPSec tünel sonlandırmaları Azure Resource Manager şablonu kullanılarak yapılandırılır. Kaynak Yöneticisi şablonlarını kullanmaya yeni bir veya Kaynak Yöneticisi şablonu temel bilgilerini anlamak için, bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](../azure-resource-manager/templates/template-syntax.md). Bu bölümdeki şablon bir doğa alanı Azure ortamı (VNet) oluşturur. Ancak, mevcut bir VNet 'iniz varsa, bu sanal ağa başvurabilirsiniz. VPN Gateway IPSec/ıKE siteden siteye yapılandırmalarına alışkın değilseniz, bkz. siteden [siteye bağlantı oluşturma](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Bu yapılandırmayı oluşturmak için Azure Resource Manager şablonlarını kullanmanız gerekmez. Bu yapılandırmayı Azure portal veya PowerShell kullanarak oluşturabilirsiniz.
>
>

### <a name="31-declare-the-variables"></a><a name="variables3"></a>3,1 değişkenleri bildirin

Bu örnekte, değişken bildirimleri örnek ağa karşılık gelir. Değişkenleri bildirirken, bu bölümü ortamınızı yansıtacak şekilde değiştirin.

* **Localaddresspredüzeltmesini** değişkeni, IPSec tünellerini sonlandırmak için ŞIRKET içi IP adreslerinden oluşan bir dizidir.
* **Gatewaysku** , VPN aktarım hızını belirler. GatewaySku ve vpnType hakkında daha fazla bilgi için bkz. [VPN Gateway Configuration Settings](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Fiyatlandırma için bkz. [VPN Gateway fiyatlandırması](https://azure.microsoft.com/pricing/details/vpn-gateway).
* **Vpntype** 'ı **routebased** olarak ayarlayın.

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

### <a name="32-create-virtual-network-vnet"></a><a name="vnet"></a>3,2 sanal ağ oluşturma (VNet)

Mevcut bir VNet 'i VPN tünellerinden ilişkilendirirken, bu adımı atlayabilirsiniz.

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

### <a name="33-assign-public-ip-addresses-to-vpn-gateway-instances"></a><a name="ip"></a>3,3 VPN Gateway örneklerine genel IP adresleri atama
 
Bir VPN ağ geçidinin her örneği için genel bir IP adresi atayın.

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

### <a name="34-specify-the-on-premises-vpn-tunnel-termination-local-network-gateway"></a><a name="termination"></a>3,4 Şirket içi VPN tüneli sonlandırmasını belirtin (yerel ağ geçidi)

Şirket içi VPN cihazları **yerel ağ geçidi** olarak adlandırılır. Aşağıdaki JSON kod parçacığı uzak BGP eş bilgilerini de belirtir:

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

### <a name="35-create-the-vpn-gateway"></a><a name="creategw"></a>3,5 VPN Gateway oluşturma

Şablonun bu bölümü, VPN ağ geçidini etkin-etkin bir yapılandırma için gerekli ayarlarla yapılandırır. Aşağıdaki gereksinimleri göz önünde bulundurun:

* VPN ağ geçidini **"Routebased"** vpntype ile oluşturun. VPN ağ geçidi ve şirket içi VPN arasında BGP yönlendirmeyi etkinleştirmek istiyorsanız bu ayar zorunludur.
* VPN ağ geçidinin iki örneği ve belirli bir şirket içi cihaz arasında etkin-etkin modda VPN tünelleri oluşturmak için, Kaynak Yöneticisi şablonunda **"Activeactive"** parametresi **true** olarak ayarlanır. Yüksek oranda kullanılabilir VPN ağ geçitleri hakkında daha fazla bilgi edinmek için bkz. [yüksek oranda KULLANILABILIR VPN Gateway bağlantısı](../vpn-gateway/vpn-gateway-highlyavailable.md).
* VPN tünellerini arasında eBGP oturumlarını yapılandırmak için iki tarafta iki farklı ASNs belirtmeniz gerekir. Özel ASN numaralarını belirtmek tercih edilir. Daha fazla bilgi için bkz. [BGP ve Azure VPN ağ geçitlerine genel bakış](../vpn-gateway/vpn-gateway-bgp-overview.md).

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

### <a name="36-establish-the-ipsec-tunnels"></a><a name="ipsectunnel"></a>3,6 IPSec tünellerini oluşturma

Betiğin son eylemi, Azure VPN ağ geçidi ve şirket içi VPN cihazı arasında IPSec tünelleri oluşturur.

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

## <a name="4-configure-the-on-premises-vpn-device"></a><a name="device"></a>4. Şirket içi VPN cihazını yapılandırma

Azure VPN Gateway, farklı satıcılardan gelen birçok VPN cihazlarıyla uyumludur. VPN Gateway ile çalışmak üzere doğrulanan yapılandırma bilgileri ve cihazlar için bkz. [VPN cihazları hakkında](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

VPN cihazınızı yapılandırırken aşağıdaki öğeler gereklidir:

* Paylaşılan bir anahtar. Bu, siteden siteye VPN bağlantınızı oluştururken belirttiğiniz aynı paylaşılan anahtardır. Örnekler temel bir paylaşılan anahtar kullanır. Kullanmak için daha karmaşık bir anahtar oluşturmanız önerilir.
* VPN ağ geçidinizin genel IP adresi. Azure Portal, PowerShell veya CLI kullanarak genel IP adresini görüntüleyebilirsiniz. Azure portalını kullanarak VPN ağ geçidinizin Genel IP adresini bulmak için Sanal ağ geçitleri’ne gidin ve ağ geçidinizin adına tıklayın.

Genellikle eBGP eşleri doğrudan bağlanır (genellikle WAN bağlantısı üzerinden). Ancak, ExpressRoute Microsoft eşlemesi aracılığıyla IPSec VPN tünelleri üzerinden eBGP yapılandırırken, eBGP eşleri arasında birden çok yönlendirme etki alanı vardır. Doğrudan bağlantılı olmayan iki eş arasında eBGP komşusu ilişkisi oluşturmak için **ebgp-multihop** komutunu kullanın. Ebgp-multihop komutunu izleyen tamsayı BGP paketlerindeki TTL değerini belirtir. **En fazla Path, eibgp 2** , iki BGP yolu arasında trafiğin yük dengelenmesini mümkün bir şekilde sunar.

### <a name="cisco-csr1000-example"></a><a name="cisco1"></a>Cisco CSR1000 örneği

Aşağıdaki örnekte, şirket içi VPN cihazı olarak bir Hyper-V sanal makinesinde Cisco CSR1000 yapılandırması gösterilmektedir:

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

## <a name="5-configure-vpn-device-filtering-and-firewalls-optional"></a><a name="firewalls"></a>5. VPN cihaz filtrelemeyi ve güvenlik duvarlarını yapılandırma (isteğe bağlı)

Güvenlik duvarınızı ve filtrelemesini gereksinimlerinize göre yapılandırın.

## <a name="6-test-and-validate-the-ipsec-tunnel"></a><a name="testipsec"></a>6. IPSec tüneli test edin ve doğrulayın

IPSec tünellerinin durumu, Azure VPN Gateway 'den PowerShell komutlarına göre doğrulanabilir:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Örnek çıktı:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Azure VPN ağ geçidi örneklerinde tünellerin durumunu bağımsız olarak denetlemek için aşağıdaki örneği kullanın:

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

Ayrıca, şirket içi VPN cihazınızda tünel durumunu da denetleyebilirsiniz.

Cisco CSR1000 örneği:

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

Sanal tünel arabirimindeki (VTı) hat protokolü, ıKE aşama 2 tamamlanana kadar "yukarı" olarak değişmez. Aşağıdaki komut güvenlik ilişkilendirmesini doğrular:

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

### <a name="verify-end-to-end-connectivity-between-the-inside-network-on-premises-and-the-azure-vnet"></a><a name="verifye2e"></a>Şirket içi ve Azure sanal ağı arasındaki uçtan uca bağlantıyı doğrulama

IPSec tünelleri varsa ve statik yollar doğru ayarlandıysa, uzak BGP eşinin IP adresine ping atabiliyor olmanız gerekir:

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

### <a name="verify-the-bgp-sessions-over-ipsec"></a><a name="verifybgp"></a>IPSec üzerinden BGP oturumlarını doğrulama

Azure VPN ağ geçidinde, BGP eşinin durumunu doğrulayın:

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

Şirket içi VPN yoğunlaştırıcısında eBGP aracılığıyla alınan ağ öneklerinin listesini doğrulamak için, özniteliğe "Origin" olarak filtre uygulayabilirsiniz:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

Örnek çıktıda ASN 65010, şirket içi VPN 'deki BGP otonom sistem numarasıdır.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Tanıtılan yolların listesini görmek için:

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

Şirket içi Cisco CSR1000 örneği:

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

Şirket içi Cisco CSR1000 'dan Azure VPN Gateway 'e tanıtılan ağların listesi aşağıdaki komut kullanılarak listelenebilir:

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

* [ExpressRoute için Ağ Performansı İzleyicisi’ni Yapılandırma](how-to-npm.md)

* [Mevcut bir VPN Ağ Geçidi bağlantısı ile bir VNet 'e siteden siteye bağlantı ekleme](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
