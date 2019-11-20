---
title: Sanal ağdan sanal ağa bağlantılar & S2S VPN için IPSec/ıKE ilkesi
titleSuffix: Azure VPN Gateway
description: Azure Resource Manager ve PowerShell kullanarak Azure VPN ağ geçitleriyle S2S veya VNet-VNet bağlantıları için IPSec/ıKE ilkesi yapılandırın.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: b0dabf0ee3370abab3d0f9d6f1bf26dd622862cf
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151790"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>S2S VPN veya VNet-VNet bağlantıları için IPSec/ıKE ilkesini yapılandırma

Bu makale, Kaynak Yöneticisi dağıtım modelini ve PowerShell 'i kullanarak siteden siteye VPN veya VNet-VNet bağlantıları için IPSec/ıKE ilkesini yapılandırma adımlarında size yol gösterir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>Azure VPN ağ geçitleri için IPSec ve ıKE ilke parametreleri hakkında
IPSec ve ıKE protokol standardı çeşitli birleşimlerde çok sayıda şifreleme algoritmasını destekler. Bunun şirket içi ve VNet 'ten VNet 'e bağlantısının uyumluluk veya güvenlik gereksinimlerinizi karşılamasını sağlamaya nasıl yardımcı olduğunu görmek için [Şifreleme gereksinimleri ve Azure VPN ağ geçitleri hakkında](vpn-gateway-about-compliance-crypto.md) bölümüne bakın.

Bu makalede bir IPSec/ıKE İlkesi oluşturup yapılandırma ve yeni veya mevcut bir bağlantı için geçerli olan yönergeler sağlanmaktadır:

* [Bölüm 1-IPSec/ıKE ilkesi oluşturmak ve ayarlamak için Iş akışı](#workflow)
* [Bölüm 2-desteklenen şifreleme algoritmaları ve anahtar güçleri](#params)
* [3. kısım-IPSec/ıKE ilkesiyle yeni bir S2S VPN bağlantısı oluşturma](#crossprem)
* [Bölüm 4-IPSec/ıKE ilkesiyle yeni VNet-VNet bağlantısı oluşturma](#vnet2vnet)
* [5. bölüm-bir bağlantı için IPSec/ıKE İlkesi yönetme (oluşturma, ekleme, kaldırma)](#managepolicy)

> [!IMPORTANT]
> 1. IPSec/ıKE ilkesinin yalnızca aşağıdaki ağ geçidi SKU 'Larında çalışmadığını unutmayın:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (rota tabanlı)
>    * ***Standart*** ve ***HighPerformance*** (rota tabanlı)
> 2. Belirli bir bağlantı için yalnızca ***bir*** ilke birleşimi belirtebilirsiniz.
> 3. IKE (ana mod) ve IPSec (hızlı mod) için tüm algoritmaları ve parametreleri belirtmeniz gerekir. Kısmi ilke belirtimine izin verilmez.
> 4. İlkenin şirket içi VPN cihazlarınızda desteklendiğinden emin olmak için, VPN cihazınızın satıcı belirtimlerinize başvurun. İlkeler uyumsuz ise, S2S veya VNet-VNet bağlantıları kurulamaz.

## <a name ="workflow"></a>Bölüm 1-IPSec/ıKE ilkesi oluşturmak ve ayarlamak için Iş akışı
Bu bölümde, bir S2S VPN veya VNet-VNet bağlantısında IPSec/ıKE ilkesi oluşturma ve güncelleştirme için iş akışı özetlenmektedir:
1. Sanal ağ ve VPN ağ geçidi oluşturma
2. Şirket içi bağlantı için bir yerel ağ geçidi veya VNet-VNet bağlantısı için başka bir sanal ağ ve Ağ Geçidi oluşturun
3. Seçili algoritmalara ve parametrelere sahip bir IPSec/ıKE ilkesi oluşturma
4. IPSec/ıKE ilkesiyle bağlantı (IPSec veya VNet2VNet) oluşturma
5. Mevcut bir bağlantı için IPSec/ıKE ilkesi ekleme/güncelleştirme/kaldırma

Bu makaledeki yönergeler, diyagramda gösterildiği gibi IPSec/ıKE ilkelerini ayarlamanıza ve yapılandırmanıza yardımcı olur:

![IPSec-IKE-Policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>2. Bölüm-desteklenen şifreleme algoritmaları & anahtar güçlerinin

Aşağıdaki tabloda, müşteriler tarafından yapılandırılabilen desteklenen şifreleme algoritmaları ve anahtar güçleri listelenmektedir:

| **IPsec/IKEv2**  | **Seçenekler**    |
| ---  | --- 
| IKEv2 Şifrelemesi | AES256, AES192, AES128, DES3, DES  
| IKEv2 Bütünlüğü  | SHA384, SHA256, SHA1, MD5  |
| DH Grubu         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| IPsec Şifrelemesi | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, None    |
| IPsec Bütünlüğü  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS Grubu        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Hiçbiri 
| QM SA Yaşam Süresi   | (**Isteğe bağlı**: belirtilmemişse varsayılan değerler kullanılır)<br>Saniye (tamsayı; **en az 300**/varsayılan 27000 saniye)<br>Kilobayt (tamsayı; **en az 1024**/varsayılan 102400000 kilobayt)   |
| Trafik Seçicisi | UsePolicyBasedTrafficSelectors * * ($True/$False; **Isteğe bağlı**, varsayılan $false belirtilmemişse)    |
|  |  |

> [!IMPORTANT]
> 1. **Şirket içi VPN cihaz yapılandırmanızın Azure IPSec/ıKE ilkesinde belirttiğiniz aşağıdaki algoritmaların ve parametrelerin eşleşmesi veya içermesi gerekir:**
>    * IKE şifreleme algoritması (ana mod/Aşama 1)
>    * IKE bütünlük algoritması (ana mod/Aşama 1)
>    * DH grubu (ana mod/Aşama 1)
>    * IPSec şifreleme algoritması (hızlı mod/aşama 2)
>    * IPSec bütünlük algoritması (hızlı mod/aşama 2)
>    * PFS Grubu (hızlı mod/aşama 2)
>    * Trafik seçicisi (UsePolicyBasedTrafficSelectors kullanılıyorsa)
>    * SA yaşam süreleri yalnızca yerel belirtimlerdir ve bunların eşleşmesi gerekmez.
>
> 2. **IPSec şifreleme algoritması için GCMAES kullanılıyorsa, IPSec bütünlüğü için aynı GCMAES algoritmasını ve anahtar uzunluğunu seçmeniz gerekir; Örneğin, her ikisi için GCMAES128 kullanma**
> 3. Yukarıdaki tabloda:
>    * Ikev2 ana moda veya 1 aşamasına karşılık gelir
>    * IPSec, hızlı moda veya Aşama 2 ' ye karşılık gelir
>    * DH grubu ana modda veya 1. aşamada kullanılan Diffie-Hellmence grubunu belirtir
>    * PFS Grubu, Hızlı modda veya Aşama 2 ' de kullanılan Diffie-Hellmence grubunu belirtti
> 4. Azure VPN ağ geçitlerinde IKEv2 Ana Modu SA yaşam süresi 28.800 saniye olarak sabitlenmiştir
> 5. "UsePolicyBasedTrafficSelectors" bağlantısının bir bağlantıda $True olarak ayarlanması, Azure VPN ağ geçidini şirket içi ilke tabanlı VPN güvenlik duvarına bağlanacak şekilde yapılandırır. PolicyBasedTrafficSelectors ' ı etkinleştirirseniz, VPN cihazınızın, şirket içi ağ (yerel ağ geçidi) öneklerinizin tüm birleşimleri ile, Azure sanal ağ öneklerinden herhangi birine ve bunlardan herhangi birine kadar tanımlanmış olan eşleşen trafik seçicileri olduğundan emin olmanız gerekir. Örneğin, şirket içi ağınızın ön ekleri 10.1.0.0/16 ve 10.2.0.0/16; sanal ağınızın ön ekleriyse 192.168.0.0/16 ve 172.16.0.0/16 şeklindeyse, aşağıdaki trafik seçicileri belirtmeniz gerekir:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

İlke tabanlı trafik seçicileri hakkında daha fazla bilgi için bkz. [birden çok şirket içi ilke tabanlı VPN cihazlarını bağlama](vpn-gateway-connect-multiple-policybased-rm-ps.md).

Aşağıdaki tabloda özel ilke tarafından desteklenen karşılık gelen Diffie-Hellman grupları listelenmektedir:

| **Diffie-Hellman Grubu**  | **DHGroup**              | **PFSGroup** | **Anahtar uzunluğu** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768 bit MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 bit MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 bit MODP  |
| 19                        | ECP256                   | ECP256       | 256 bit ECP    |
| 20                        | ECP384                   | ECP384       | 384 bit ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 bit MODP  |

Diğer ayrıntılar için [RFC3526](https://tools.ietf.org/html/rfc3526)ve [RFC5114](https://tools.ietf.org/html/rfc5114)’e bakın.

## <a name ="crossprem"></a>3. kısım-IPSec/ıKE ilkesiyle yeni bir S2S VPN bağlantısı oluşturma

Bu bölüm, IPSec/ıKE ilkesiyle bir S2S VPN bağlantısı oluşturma adımlarında size yol gösterir. Aşağıdaki adımlar diyagramda gösterilen bağlantıyı oluşturur:

![S2S-ilke](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

S2S VPN bağlantısı oluşturmaya yönelik daha ayrıntılı adım adım yönergeler için bkz. [S2S VPN bağlantısı oluşturma](vpn-gateway-create-site-to-site-rm-powershell.md) .

### <a name="before"></a>Başlamadan önce

* Azure aboneliğiniz olduğunu doğrulayın. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.
* Azure Resource Manager PowerShell cmdlet 'lerini yükler. PowerShell cmdlet 'lerini yükleme hakkında daha fazla bilgi için bkz. [Azure PowerShell genel bakış](/powershell/azure/overview) .

### <a name="createvnet1"></a>1. adım-sanal ağ, VPN Gateway ve yerel ağ geçidi oluşturma

#### <a name="1-declare-your-variables"></a>1. değişkenlerinizi bildirin

Bu alıştırma için değişkenlerimizi bildirerek başlayacağız. Üretim için yapılandırma sırasında bu değerleri kendi değerlerinizle değiştirdiğinizden emin olun.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. aboneliğinize bağlanın ve yeni bir kaynak grubu oluşturun

Resource Manager cmdlet’lerini kullanmak için PowerShell moduna geçtiğinizden emin olun. Daha fazla bilgi için [Windows PowerShell’i Resource Manager ile kullanma](../powershell-azure-resource-manager.md) konusuna bakın.

PowerShell konsolunuzu açın ve hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. sanal ağı, VPN ağ geçidini ve yerel ağ geçidini oluşturun

Aşağıdaki örnek, TestVNet1, üç alt ağ ve VPN ağ geçidi ile sanal ağ oluşturur. Kendi değerlerinizi yerleştirirken ağ geçidi alt ağınızı özellikle GatewaySubnet olarak adlandırmanız önem taşır. Başka bir ad kullanırsanız ağ geçidi oluşturma işleminiz başarısız olur.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="s2sconnection"></a>2. adım-IPSec/ıKE ilkesiyle bir S2S VPN bağlantısı oluşturma

#### <a name="1-create-an-ipsecike-policy"></a>1. IPSec/ıKE ilkesi oluşturma

Aşağıdaki örnek betik, aşağıdaki algoritmalara ve parametrelere sahip bir IPSec/ıKE ilkesi oluşturur:

* Ikev2: AES256, SHA384, DHGroup24
* IPSec: AES256, SHA256, PFS yok, SA yaşam süresi 14400 saniye & 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

IPSec için GCMAES kullanıyorsanız, hem IPSec şifrelemesi hem de bütünlüğü için aynı GCMAES algoritmasını ve anahtar uzunluğunu kullanmanız gerekir. Örneğin, yukarıdaki parametreler, GCMAES256 kullanılırken "-ıpsecencryption GCMAES256-ıpsecıntegrity GCMAES256" olacaktır.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. IPSec/ıKE ilkesiyle S2S VPN bağlantısını oluşturma

Bir S2S VPN bağlantısı oluşturun ve daha önce oluşturulan IPSec/ıKE ilkesini uygulayın.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

İsteğe bağlı olarak, yukarıda açıklandığı gibi, Azure VPN ağ geçidinin şirket içi ilke tabanlı VPN cihazlarına bağlanmasını sağlamak için bağlantı oluştur cmdlet 'ine "-UsePolicyBasedTrafficSelectors $True" ekleyebilirsiniz.

> [!IMPORTANT]
> Bir bağlantı üzerinde IPSec/ıKE ilkesi belirtilmişse, Azure VPN ağ geçidi yalnızca belirtilen şifreleme algoritmalarıyla IPSec/ıKE teklifini ve söz konusu bağlantıda anahtar güçlerini gönderir veya kabul eder. Bağlantı için şirket içi VPN cihazınızın, tam ilke birleşimini kullandığından veya kabul ettiğinden emin olun, aksi takdirde S2S VPN tüneli kurmaz.


## <a name ="vnet2vnet"></a>Bölüm 4-IPSec/ıKE ilkesiyle yeni VNet-VNet bağlantısı oluşturma

IPSec/ıKE ilkesiyle VNet-VNet bağlantısı oluşturma adımları S2S VPN bağlantısıyla benzerdir. Aşağıdaki örnek betikler, diyagramda gösterildiği gibi bağlantıyı oluşturur:

![V2V-ilke](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

VNET-VNet bağlantısı oluşturmaya yönelik daha ayrıntılı adımlar için bkz. VNET- [VNet bağlantısı oluşturma](vpn-gateway-vnet-vnet-rm-ps.md) . TestVNet1 ve VPN Gateway oluşturmak ve yapılandırmak için [Bölüm 3 ' ü](#crossprem) tamamlamalısınız.

### <a name="createvnet2"></a>1. adım-ikinci sanal ağı ve VPN ağ geçidini oluşturma

#### <a name="1-declare-your-variables"></a>1. değişkenlerinizi bildirin

Değerleri, yapılandırma için kullanmak istediğiniz değerlerle değiştirdiğinizden emin olun.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. yeni kaynak grubunda ikinci sanal ağı ve VPN ağ geçidini oluşturun

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>2\. adım-IPSec/ıKE ilkesiyle VNet-toVNet bağlantısı oluşturma

S2S VPN bağlantısına benzer şekilde, bir IPSec/ıKE ilkesi oluşturun ve ardından yeni bağlantı için ilkeye uygulayın.

#### <a name="1-create-an-ipsecike-policy"></a>1. IPSec/ıKE ilkesi oluşturma

Aşağıdaki örnek betik, şu algoritmalar ve parametreler ile farklı bir IPsec/IKE ilkesi oluşturur:
* Ikev2: AES128, SHA1, DHGroup14
* IPSec: GCMAES128, GCMAES128, PFS14, SA yaşam 14400 saniye & 102400000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. IPSec/ıKE ilkesiyle VNet-VNet bağlantıları oluşturma

VNet-VNet bağlantısı oluşturun ve oluşturduğunuz IPSec/ıKE ilkesini uygulayın. Bu örnekte her iki ağ geçidi de aynı abonelikte bulunur. Bu nedenle, her iki bağlantıyı aynı PowerShell oturumunda aynı IPSec/ıKE ilkesiyle oluşturmak ve yapılandırmak mümkündür.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Bir bağlantı üzerinde IPSec/ıKE ilkesi belirtilmişse, Azure VPN ağ geçidi yalnızca belirtilen şifreleme algoritmalarıyla IPSec/ıKE teklifini ve söz konusu bağlantıda anahtar güçlerini gönderir veya kabul eder. Her iki bağlantı için de IPSec ilkelerinin aynı olduğundan emin olun, aksi takdirde VNET-VNet bağlantısı kurmayacak.

Bu adımları tamamladıktan sonra, bağlantı birkaç dakika içinde oluşturulur ve başlangıcında gösterildiği gibi aşağıdaki ağ topolojisine sahip olursunuz:

![IPSec-IKE-Policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>5. kısım-bağlantı için IPSec/ıKE ilkesini güncelleştirme

Son bölümde, mevcut bir S2S veya VNet-VNet bağlantısı için IPSec/ıKE ilkesini nasıl yöneteceğiniz gösterilmektedir. Aşağıdaki alıştırmada bir bağlantıda aşağıdaki işlemler gösterilmektedir:

1. Bir bağlantının IPSec/ıKE ilkesini göster
2. IPSec/ıKE ilkesini bir bağlantıya ekleme veya güncelleştirme
3. IPSec/ıKE ilkesini bir bağlantıdan kaldırma

Aynı adımlar hem S2S hem de VNet-VNet bağlantıları için geçerlidir.

> [!IMPORTANT]
> IPSec/ıKE ilkesi yalnızca *Standart* ve *HIGHPERFORMANCE* rota tabanlı VPN ağ geçitlerinde desteklenir. Temel ağ geçidi SKU 'sunda veya ilke tabanlı VPN Gateway 'de çalışmaz.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. bir bağlantının IPSec/ıKE ilkesini göster

Aşağıdaki örnek, bir bağlantıda yapılandırılan IPSec/ıKE ilkesinin nasıl alınacağını gösterir. Betikler Ayrıca yukarıdaki alýþtýrmalara de devam eder.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Son komut, varsa, bağlantıda yapılandırılan geçerli IPSec/ıKE ilkesini listeler. Bağlantı için örnek bir çıktı aşağıda verilmiştir:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

Yapılandırılmış bir IPSec/ıKE ilkesi yoksa, (PS > $connection 6. ilke) komutu boş bir dönüş alır. IPSec/ıKE 'nin bağlantıda yapılandırılmadığı, ancak özel IPSec/ıKE ilkesi olmadığı anlamına gelir. Gerçek bağlantı, şirket içi VPN cihazınız ile Azure VPN ağ geçidi arasında anlaştığınız varsayılan ilkeyi kullanır.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. bağlantı için IPSec/ıKE ilkesi ekleme veya güncelleştirme

Yeni bir ilke ekleme veya bir bağlantıda var olan bir ilkeyi güncelleştirme adımları aynıdır: yeni bir ilke oluşturun ve sonra yeni ilkeyi bağlantıya uygulayın.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Şirket içi ilke tabanlı bir VPN cihazına bağlanırken "UsePolicyBasedTrafficSelectors" özelliğini etkinleştirmek için cmdlet 'e "-UsePolicyBaseTrafficSelectors" parametresini ekleyin veya seçeneği devre dışı bırakmak için $False ayarlayın:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

İlkenin güncelleştirilip güncelleştirilmediğini denetlemek için bağlantıyı yeniden alabilirsiniz.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Aşağıdaki örnekte gösterildiği gibi son satırdan çıktıyı görmeniz gerekir:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. bir IPSec/ıKE ilkesini bir bağlantıdan kaldırma

Özel ilkeyi bir bağlantıdan kaldırdığınızda, Azure VPN ağ geçidi [varsayılan IPSec/IKE teklifleri listesine](vpn-gateway-about-vpn-devices.md) geri döner ve ŞIRKET içi VPN cihazınızdan yeniden anlaşmaya varılır.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

İlkenin bağlantıdan kaldırılıp kaldırılmadığını denetlemek için aynı betiği kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

İlke tabanlı trafik seçicileri hakkında daha fazla bilgi için bkz. [birden çok şirket içi ilke tabanlı VPN cihazını bağlama](vpn-gateway-connect-multiple-policybased-rm-ps.md) .

Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Adımlar için bkz. [Sanal Makine Oluşturma](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
