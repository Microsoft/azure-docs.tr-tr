---
title: 'Azure VPN Gateway: S2S VPN bağlantıları oluşturma ve yönetme: öğretici'
description: Öğretici - Azure PowerShell modülü ile S2S VPN bağlantıları Oluşturma ve Yönetme
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 64da55c3dba4b617458b68a8b54a509bab4264d2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152150"
---
# <a name="tutorial-create-and-manage-s2s-vpn-connections-using-powershell"></a>Öğretici: PowerShell kullanarak S2S VPN bağlantıları oluşturma ve yönetme

Azure S2S VPN bağlantıları müşterinin iş yeri ile Azure arasında güvenli, konumlar arası bağlantı sağlar. Bu öğretici, IPsec S2S VPN bağlantısı yaşam döngüleri için bir S2S VPN bağlantısı oluşturup yönetme gibi konularda rehberlik sağlar. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * S2S VPN bağlantısı oluşturma
> * Bağlantı özelliğini güncelleştirme: önceden paylaşılan anahtar, BGP, IPsec/IKE ilkesi
> * Daha fazla VPN bağlantısı ekleme
> * Bir VPN bağlantısını silme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki diyagramda bu öğreticinin topolojisi gösterilmektedir:

![Konumdan Konuma VPN bağlantısı diyagramı](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="requirements"></a>Gereksinimler

İlk öğreticiyi doldurun: aşağıdaki kaynakları oluşturmak için [Azure PowerShell Ile VPN ağ geçidi oluşturma](vpn-gateway-tutorial-create-gateway-powershell.md) :

1. Kaynak grubu (TestRG1), sanal ağ (VNet1) ve GatewaySubnet
2. VPN ağ geçidi (VNet1GW)

Sanal ağ parametre değerleri aşağıda listelenmiştir. Şirket içi ağınızı temsil eden yerel ağ geçidinin ek değerlerini göz önünde edin. Aşağıdaki değerleri ortamınıza ve ağ kuruluma göre değiştirin, ardından Bu öğreticinin değişkenlerini ayarlamak için kopyalayıp yapıştırın. Cloud Shell oturumunuz zaman aşımına uğrarsa veya farklı bir PowerShell penceresi kullanmanız gerekiyorsa, değişkenleri kopyalayıp yeni oturumunuza yapıştırın ve öğreticiye devam edin.

>[!NOTE]
> Bunu bir bağlantı yapmak için kullanıyorsanız, değerleri şirket içi ağınızla eşleşecek şekilde değiştirdiğinizden emin olun. Bu adımları bir öğretici olarak çalıştırıyorsanız, değişiklik yapmanız gerekmez, ancak bağlantı çalışmaz.
>

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network - LNGIP1 is the VPN device public IP address
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "5.4.3.2"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

S2S VPN bağlantısı oluşturmak için uygulanması gereken iş akışı basittir:

1. Şirket içi ağınızı temsil eden yerel bir ağ geçidi oluşturun
2. Azure VPN ağ geçidiniz ile yerel ağ geçidiniz arasında bağlantı oluşturma

## <a name="create-a-local-network-gateway"></a>Yerel ağ geçidi oluşturma

Şirket içi ağınızı yerel bir ağ geçidi temsil eder. Yerel ağ geçidinde aşağıdakiler dahil olmak üzere şirket içi ağınızın özelliklerini belirtebilirsiniz:

* VPN cihazınızın genel IP adresi
* Şirket içi adres alanı
* (İsteğe bağlı) BGP öznitelikleri (BGP eş IP adresi ve AS numarası)

[New-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azlocalnetworkgateway) komutuyla bir yerel ağ geçidi oluşturun.

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>S2S VPN bağlantısı oluşturma

Sonra, sanal ağ geçidiniz ile VPN cihazınız arasında [New-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection)Ile bir sıteden siteye VPN bağlantısı oluşturun. Siteden Siteye VPN bağlantısı için ‘-ConnectionType’ değerinin *IPsec* olduğunu unutmayın.

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3" -ConnectionProtocol IKEv2
```

BGP kullanıyorsanız isteğe bağlı " **-EnableBGP $True**" özelliğini ekleyerek bağlantı için BGP'yi etkinleştirebilirsiniz. Varsayılan olarak devre dışıdır. Varsayılan olarak Ikev2 ile '-ConnectionProtocol ' parametresi isteğe bağlıdır. **-ConnectionProtocol IKEv1**belirterek, IKEv1 protokolleriyle bağlantı oluşturabilirsiniz.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>VPN bağlantısı önceden paylaşılan anahtarını, BGP’yi ve IPsec/IKE ilkesini güncelleştirme

### <a name="view-and-update-your-pre-shared-key"></a>Önceden paylaşılan anahtarınızı görüntüleme ve güncelleştirme

Azure S2S VPN bağlantısı, şirket içi VPN cihazınız ile Azure VPN ağ geçidi arasında kimlik doğrulaması gerçekleştirmek için önceden paylaşılan bir anahtar (gizli dizi) kullanır. [Get-azvirtualnetworkgatewayconnectionsharedkey](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworkgatewayconnectionsharedkey) ve [set-azvirtualnetworkgatewayconnectionsharedkey](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnectionsharedkey)ile bir bağlantı için önceden paylaşılan anahtarı görüntüleyebilir ve güncelleştirebilirsiniz.

> [!IMPORTANT]
> Önceden paylaşılan anahtar, en fazla 128 **yazdırılabilir ASCII karakterden** oluşan bir dizedir.

Bu komut, bağlantının önceden paylaşılan anahtarını gösterir:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

Yukarıdaki örnekte takip eden çıkış "**Azure\@! b2C3**" olacaktır. Önceden paylaşılan anahtar değerini "**Azure\@! _b2 = C3**" olarak değiştirmek için aşağıdaki komutu kullanın:

```azurepowershell-interactive
Set-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>VPN bağlantısında BGP'yi etkinleştirme

Azure VPN ağ geçidi, BGP dinamik yönlendirme protokolünü destekler. Şirket içi ağlarınızda ve cihazlarınızda BGP’yi kullanıp kullanmadığınıza bağlı olarak her bağlantıda BGP’yi etkinleştirebilirsiniz. Bağlantıda BGP’yi etkinleştirmeden önce aşağıdaki BGP özelliklerini belirtin:

* Azure VPN ASN (Otonom Sistem Numarası)
* Şirket içi yerel ağ geçidi ASN’si
* Şirket içi yerel ağ geçidi BGP eş IP adresi

BGP özelliklerini yapılandırmadıysanız, aşağıdaki komutlar VPN ağ geçidize ve yerel ağ geçidine bu özellikleri ekler: [set-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgateway) ve [set-azlocalnetworkgateway](https://docs.microsoft.com/powershell/module/az.network/set-azlocalnetworkgateway).

BGP özelliklerini yapılandırmak için aşağıdaki örneği kullanın:

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

[Set-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnection)ile BGP 'yi etkinleştirin.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

"-EnableBGP" özellik değerini **$False** olacak şekilde değiştirerek BGP’yi devre dışı bırakabilirsiniz. Azure VPN ağ geçitlerinde BGP’ye ilişkin daha ayrıntılı açıklamalar için [Azure VPN ağ geçitlerinde BGP](vpn-gateway-bgp-resource-manager-ps.md) konusuna başvurun.

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Bağlantıda özel bir IPSec/IKE ilkesi uygulama

İsteğe bağlı bir IPSec/IKE ilkesi belirterek bağlantıda [varsayılan öneriler](vpn-gateway-about-vpn-devices.md#ipsec) yerine tam olarak hangi IPSec/IKE şifreleme algoritması ve anahtar gücü kombinasyonunun kullanılacağını belirtebilirsiniz. Aşağıdaki örnek betik, şu algoritmalar ve parametreler ile farklı bir IPsec/IKE ilkesi oluşturur:

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, SA Yaşam Süresi 14.400 saniye ve 102.400.000 KB

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Algoritmaların ve yönergelerin tam listesini görmek için bkz. [S2S veya Sanal Ağdan Sanal Ağa bağlantıları için IPSec/IKE İlkesi](vpn-gateway-ipsecikepolicy-rm-powershell.md).

## <a name="add-another-s2s-vpn-connection"></a>Başka bir S2S VPN bağlantısı ekleme

Aynı VPN ağ geçidine ek bir S2S VPN bağlantısı ekleyin, başka bir yerel ağ geçidi oluşturun ve yeni yerel ağ geçidi ile VPN ağ geçidi arasında yeni bir bağlantı oluşturun. Değişkenlerini kendi ağ yapılandırmanızı yansıtacak şekilde değiştirdiğinizden emin olmak için aşağıdaki örnekleri kullanın.

```azurepowershell-interactive
# On-premises network - LNGIP2 is the VPN device public IP address
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "4.3.2.1"
$Connection2 = "VNet1ToSite2"

New-AzLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

Azure VPN ağ geçidiniz ile iki S2S VPN bağlantısı kurmuş oldunuz.

![Çok siteli VPN bağlantıları](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Bir S2S VPN bağlantısını silme

[Remove-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetworkgatewayconnection)Ile BIR S2S VPN bağlantısını silin.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Yerel ağ geçidi artık gerekli değilse ağ geçidini silin. Kendisiyle ilişkili başka bağlantılar olan bir yerel ağ geçidini silemezsiniz.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu yapılandırma, prototip, test veya kavram kanıtı dağıtımı bir parçasıysa, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu, VPN ağ geçidini ve tüm ilgili kaynakları kaldırabilirsiniz.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, S2S VPN bağlantıları oluşturup yönetme konusunda aşağıdaki gibi işlemler yapmayı öğrendiniz:

> [!div class="checklist"]
> * S2S VPN bağlantısı oluşturma
> * Bağlantı özelliğini güncelleştirme: önceden paylaşılan anahtar, BGP, IPsec/IKE ilkesi
> * Daha fazla VPN bağlantısı ekleme
> * Bir VPN bağlantısını silme

S2S, Sanal Ağdan Sanal Ağa ve P2S bağlantıları hakkında bilgi edinmek için sonraki öğreticilere ilerleyin.

> [!div class="nextstepaction"]
> * [Sanal Ağdan Sanal Ağa bağlantılar oluşturma](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [P2S bağlantıları oluşturma](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
