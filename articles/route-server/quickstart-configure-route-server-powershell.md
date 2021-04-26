---
title: 'Hızlı başlangıç: Azure PowerShell kullanarak rota sunucusu oluşturma ve yapılandırma'
description: Bu hızlı başlangıçta Azure PowerShell kullanarak bir rota sunucusu oluşturma ve yapılandırma hakkında bilgi edineceksiniz.
services: route-server
author: duongau
ms.author: duau
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: route-server
ms.custom:
- mode-api
ms.openlocfilehash: 608ec3755fcd231d5cc89bbc28a01ce172978144
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538715"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak rota sunucusu oluşturma ve yapılandırma

Bu makale, Azure yol sunucusunu PowerShell kullanarak sanal ağınızdaki bir ağ sanal gereci (NVA) ile eş olarak yapılandırmanıza yardımcı olur. Azure yol sunucusu, NVA 'dan yolları öğrenmekte ve sanal ağdaki sanal makinelerde bunları programalacak. Azure yol sunucusu, NVA 'ya sanal ağ yollarını da duyuracaktır. Daha fazla bilgi için [Azure Route sunucusu](overview.md)' nu okuyun.

> [!IMPORTANT]
> Azure yol sunucusu (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En son PowerShell modüllerine sahip olduğunuzdan emin olun veya portalda Azure Cloud Shell kullanabilirsiniz.
* [Azure Route sunucusu için hizmet sınırlarını](route-server-faq.md#limitations)gözden geçirin.

## <a name="create-a-route-server"></a>Rota sunucusu oluşturma

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure hesabınızda oturum açın ve aboneliğinizi seçin.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>Kaynak grubu ve sanal ağ oluşturma

Bir Azure rota sunucusu oluşturabilmeniz için önce dağıtımı barındırmak üzere bir sanal ağ gerekir. Bir kaynak grubu ve sanal ağ oluşturmak için izle komutunu kullanın. Zaten bir sanal ağınız varsa, sonraki bölüme atlayabilirsiniz.

```azurepowershell-interactive
New-AzResourceGroup –Name "RouteServerRG” -Location “West US"
New-AzVirtualNetwork –ResourceGroupName "RouteServerRG" -Location "West US" -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Alt ağ ekleme

1. Azure yol sunucusunu dağıtmak için *Routeserversubnet* adlı bir alt ağ ekleyin. Bu alt ağ, yalnızca Azure Route sunucusu için ayrılmış bir alt ağıdır. RouteServerSubnet/27 veya daha kısa bir ön ek olmalıdır (örneğin/26,/25) veya Azure yol sunucusunu eklediğinizde bir hata iletisi alırsınız.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "myVirtualNetwork" - ResourceGroupName "RouteServerRG"
    Add-AzVirtualNetworkSubnetConfig –Name "RouteServerSubnet" -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. RouteServerSubnet KIMLIĞINI alın. Sanal ağdaki tüm alt ağların kaynak KIMLIĞINI görmek için şu komutu kullanın:

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "vnet_name" -ResourceGroupName "RouteServerRG"
    $vnet.Subnets
    ```

RouteServerSubnet KIMLIĞI aşağıdakine benzer:

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Rota sunucusu oluşturma

Bu komutla rota sunucusu oluşturun:

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US" -HostedSubnet "RouteServerSubnet_ID"
```

Konumun, sanal ağınızın konumuyla eşleşmesi gerekir. HostedSubnet, önceki bölümde edindiğiniz RouteServerSubnet KIMLIĞIDIR.

## <a name="create-peering-with-an-nva"></a>NVA ile eşleme oluşturma

Yol sunucusundan NVA 'ya BGP eşlemesi oluşturmak için aşağıdaki komutu kullanın:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

"nva_ip", NVA 'ya atanan sanal ağ IP 'dir. "nva_asn", NVA 'da yapılandırılan otonom sistem sayısıdır (ASN). ASN, 65515-65520 aralığından farklı bir 16 bit sayı olabilir. Bu ASNs aralığı Microsoft tarafından ayrılmıştır.

Artıklığı için farklı NVA veya aynı NVA örneği ile eşleme ayarlamak için şu komutu kullanın:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>NVA üzerinde yapılandırmayı doldurun

NVA üzerindeki yapılandırmayı tamamlayıp BGP oturumlarını etkinleştirmek için, Azure Route sunucusunun IP ve ASN 'sine ihtiyacınız vardır. Şu komutu kullanarak bu bilgileri alabilirsiniz:

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

Çıktıda aşağıdaki bilgiler bulunur:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Rota değişimini yapılandırma

Aynı VNet 'te bir ExpressRoute Gateway ve bir Azure VPN ağ geçidiniz varsa ve yollar alışverişi yapmak istiyorsanız, Azure Route sunucusunda Yönlendirme değişimi 'ni etkinleştirebilirsiniz.

1. Azure yol sunucusu ve ağ geçidi (ler) arasında yönlendirme değişimini etkinleştirmek için şu komutu kullanın:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Azure yol sunucusu ve ağ geçidi (ler) arasında yönlendirme değişimini devre dışı bırakmak için şu komutu kullanın:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>Sorun giderme

Azure Route sunucusu tarafından tanıtılan ve alınan yolları bu komutla görüntüleyebilirsiniz:

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure Route sunucusuna artık ihtiyacınız yoksa, BGP eşlemesini kaldırmak için bu komutları kullanın ve ardından rota sunucusunu kaldırın. 

1. Bu komutla Azure Route sunucusu ve NVA arasındaki BGP eşlemesini kaldırın:

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName "nva_name" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. Azure yol sunucusunu şu komutla kaldırın:

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Route sunucusu oluşturduktan sonra Azure Route sunucusunun ExpressRoute ve VPN ağ geçitleri ile nasıl etkileşime gireceğini öğrenin: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute ve Azure VPN desteği](expressroute-vpn-support.md)
