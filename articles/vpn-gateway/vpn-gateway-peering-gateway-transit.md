---
title: Sanal ağ eşlemesi için VPN ağ geçidi aktarımını yapılandırma
description: İki Azure sanal ağını bağlantı amaçlarıyla sorunsuz bir şekilde bağlamak için sanal ağ eşlemesi için ağ geçidi geçişi yapılandırın.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/30/2020
ms.author: cherylmc
ms.openlocfilehash: 73a7d76de34d29b2d51c54569b234cd8221b08f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98872188"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Sanal ağ eşlemesi için VPN ağ geçidi aktarımını yapılandırma

Bu makale, sanal ağ eşlemesi için ağ geçidi aktarımını yapılandırmanıza yardımcı olur. [Sanal ağ eşlemesi](../virtual-network/virtual-network-peering-overview.md), iki Azure sanal ağını sorunsuzca bağlar ve bağlantı amacıyla iki sanal ağı tek bir ağda birleştirir. [Ağ geçidi geçişi](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) , bir sanal ağın, şirket Içi veya VNet-VNet bağlantısı için eşlenen sanal ağda VPN ağ geçidini kullanmasına imkan tanıyan bir eşleme özelliğidir. Aşağıdaki diyagramda sanal ağ eşlemesi ile ağ geçidi aktarımının nasıl çalıştığı gösterilmiştir.

![Ağ Geçidi aktarım diyagramı](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

Diyagramda, ağ geçidi aktarımı eşlenmiş sanal ağların Hub-RM içinde Azure VPN ağ geçidi kullanmasına olanak tanır. S2S, P2S ve sanal ağlar arası bağlantılar dahil VPN ağ geçidinde kullanılabilen bağlantılar, üç sanal ağ için de geçerlidir. Aktarım seçeneği, aynı veya farklı dağıtım modelleri arasında eşleme için kullanılabilir. Farklı dağıtım modelleri arasında geçiş yapılandırıyorsanız, hub sanal ağı ve sanal ağ geçidi, klasik dağıtım modelinde değil, Kaynak Yöneticisi dağıtım modelinde olmalıdır.
>

Merkez-uç ağ mimarisinde ağ geçidi aktarımı, uç sanal ağların her uç sanal ağdaki VPN ağ geçitlerini dağıtmak yerine merkezdeki VPN ağ geçidini paylaşmasına olanak tanır. Ağ geçidine bağlı sanal ağların veya şirket içi ağların yolları, ağ geçidi aktarımı kullanılarak eşlenmiş sanal ağların yönlendirme tablolarına yayılır. VPN ağ geçidinden otomatik yol yaymayı devre dışı bırakabilirsiniz. "**BGP yol yaymayı devre dışı bırak**" seçeneği ile bir yönlendirme tablosu oluşturun ve yönlendirme tablosunu alt ağlarla ilişkilendirerek bu alt ağlara yol dağıtımını engelleyin. Daha fazla bilgi için bkz. [Sanal ağ yönlendirme tablosu](../virtual-network/manage-route-table.md).

Bu makalede iki senaryo vardır:

* **Aynı dağıtım modeli**: Kaynak Yöneticisi dağıtım modelinde her iki sanal ağ de oluşturulur.
* **Farklı dağıtım modelleri**: bağlı bileşen sanal ağı klasik dağıtım modelinde oluşturulur ve hub sanal ağı ve ağ geçidi Kaynak Yöneticisi dağıtım modelidir.

>[!NOTE]
> Ağınızın topolojisine bir değişiklik yaparsanız ve Windows VPN istemcileriniz varsa, değişikliklerin istemciye uygulanması için Windows istemcileri için VPN istemci paketinin indirilip yeniden yüklenmesi gerekir.
>

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdaki sanal ağlara ve izinlere sahip olduğunuzu doğrulayın:

### <a name="virtual-networks"></a><a name="vnet"></a>Sanal ağlar

|Sanal Ağ|Dağıtım modeli| Sanal ağ geçidi|
|---|---|---|---|
| Merkez-RM| [Resource Manager](./tutorial-site-to-site-portal.md)| [Evet](tutorial-create-gateway-portal.md)|
| Bağlı bileşen-RM | [Resource Manager](./tutorial-site-to-site-portal.md)| No |
| Uç-Klasik | [Klasik](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | No |

### <a name="permissions"></a><a name="permissions"></a>İzinler

Sanal ağ eşlemesi için kullandığınız hesaplar gerekli rol veya izinlere sahip olmalıdır. Aşağıdaki örnekte, **hub-RM** ve **bağlı bileşen-klasik** adlı iki sanal ağı eşkullandıysanız, hesabınız her bir sanal ağ için aşağıdaki rollere veya izinlere sahip olmalıdır:

|Sanal Ağ|Dağıtım modeli|Rol|İzinler|
|---|---|---|---|
|Merkez-RM|Resource Manager|[Ağ Katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klasik|[Klasik Ağ Katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Yok|
|Uç-Klasik|Resource Manager|[Ağ Katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klasik|[Klasik Ağ Katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

[Yerleşik roller](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ve [özel rollere](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (yalnızca Resource Manager) belirli izinlerin atanması hakkında daha fazla bilgi edinin.

## <a name="same-deployment-model"></a><a name="same"></a>Aynı dağıtım modeli

Bu senaryoda, sanal ağların her ikisi de Kaynak Yöneticisi dağıtım modelidir. Ağ Geçidi aktarımı 'nı etkinleştirmek için sanal ağ eşayarlarını oluşturmak veya güncelleştirmek üzere aşağıdaki adımları kullanın.

### <a name="to-add-a-peering-and-enable-transit"></a>Eşleme eklemek ve geçişi etkinleştirmek için

1. [Azure Portal](https://portal.azure.com), hub-RM ' den sanal ağ eşlemesi oluşturun veya güncelleştirin. **Hub-RM** sanal ağına gidin. **Eşleme Ekle**' ye tıklayın ve ardından **Ekle** **' ye tıklayın**.
1. **Eşleme Ekle** sayfasında, **Bu sanal ağın** değerlerini yapılandırın.

   * Eşleme bağlantı adı: bağlantıyı adlandırın. Örnek: **Hubrmtospokerd**
   * Uzak sanal ağa giden trafik: **Izin ver**
   * Uzak sanal ağdan iletilen trafik: **Izin ver**
   * Sanal ağ geçidi: **Bu sanal ağın ağ geçidini kullan**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="Ekran görüntüsünde eşleme Ekle gösterilir.":::

1. Aynı sayfada, **uzak sanal ağın** değerlerini yapılandırmak için devam edin.

   * Eşleme bağlantı adı: bağlantıyı adlandırın. Örnek: **Spokermtohubrm**
   * Dağıtım modeli: **Kaynak Yöneticisi**
   * Sanal ağ: **bağlı bileşen-RM**
   * Uzak sanal ağa giden trafik: **Izin ver**
   * Uzak sanal ağdan iletilen trafik: **Izin ver**
   * Sanal ağ geçidi: **uzak sanal ağın ağ geçidini kullanma**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="Ekran görüntüsü uzak sanal ağ için değerleri gösterir.":::

1. Eşlemeyi oluşturmak için **Ekle** ' yi seçin.
1. Eşleme durumunu her iki sanal ağda da **bağlı** olarak doğrulayın.

### <a name="to-modify-an-existing-peering-for-transit"></a>Aktarım için varolan bir eşlemeyi değiştirme

Eşleme zaten oluşturulmuşsa, transit eşlemesini değiştirebilirsiniz.

1. Sanal ağa gidin. **Peerler** ' i seçin ve değiştirmek istediğiniz eşlemeyi seçin.

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="Ekran görüntüsü, select peerusing ' i gösterir.":::

1. VNet eşlemesini güncelleştirin.

   * Uzak sanal ağa giden trafik: **Izin ver**
   * Sanal ağa iletilen trafik; **Izin ver**
   * Sanal ağ geçidi: **uzak sanal ağın ağ geçidini kullan**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="Ekran görüntüsünde eşleme ağ geçidini değiştirme gösterilmektedir.":::

1. Eşleme ayarlarını **kaydedin** .

### <a name="powershell-sample"></a><a name="ps-same"></a>PowerShell örneği

Yukarıdaki örnekle eşleme oluşturmak veya güncelleştirmek için PowerShell’i de kullanabilirsiniz. Değişkenleri, sanal ağlarınızın ve kaynak gruplarınızın adlarıyla değiştirin.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="different-deployment-models"></a><a name="different"></a>Farklı dağıtım modelleri

Bu yapılandırmada, bağlı **olan VNet sanal ağı klasik dağıtım** modelinde ve hub VNET **hub 'ı-RM** Kaynak Yöneticisi dağıtım modelinde. Dağıtım modelleri arasında geçiş yapılandırılırken, sanal ağ geçidinin klasik VNet değil Kaynak Yöneticisi VNet için yapılandırılması gerekir.

Bu yapılandırma için yalnızca **hub-RM** sanal ağını yapılandırmanız gerekir. **Bağlı olan klasik** VNET üzerinde herhangi bir şeyi yapılandırmanız gerekmez.

1. Azure portal **hub-RM** sanal ağına gidin, eşlemeler **' i seçin** ve **+ Ekle**' yi seçin.
1. **Eşleme Ekle** sayfasında, aşağıdaki değerleri yapılandırın:

   * Eşleme bağlantı adı: bağlantıyı adlandırın. Örnek: **Hubrmtoclassic**
   * Uzak sanal ağa giden trafik: **Izin ver**
   * Uzak sanal ağdan iletilen trafik: **Izin ver**
   * Sanal ağ geçidi: **Bu sanal ağın ağ geçidini kullan**
   * Uzak sanal ağ: **Klasik**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="Bağlı bileşen için eşleme sayfası ekleme-klasik":::

1. Aboneliğin doğru olduğundan emin olun, sonra açılan menüden sanal ağı seçin.
1. Eşlemeyi eklemek için **Ekle** ' yi seçin.
1. Hub-RM sanal ağında **bağlı** olarak eşleme durumunu doğrulayın. 

Bu yapılandırma için, **bağlı olan-klasik** sanal ağ üzerinde herhangi bir şeyi yapılandırmanız gerekmez. Durum **bağlı**' ı gösterdiğinde, bağlı olan sanal ağ, hub sanal ağındaki VPN ağ geçidi üzerinden bağlantıyı kullanabilir.

### <a name="powershell-sample"></a><a name="ps-different"></a>PowerShell örneği

Yukarıdaki örnekle eşleme oluşturmak veya güncelleştirmek için PowerShell’i de kullanabilirsiniz. Değişkenleri ve abonelik kimliğini sanal ağınızın, kaynak grubunuzun ve aboneliğinizin değerleri ile değiştirin. Yalnızca merkez sanal ağı üzerinde sanal ağ eşlemesi oluşturmanız gerekir.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Sonraki adımlar

* Üretimde kullanım için sanal ağ eşlemesi oluşturmadan önce [sanal ağ eşleme kısıtlamaları ve davranışları](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) ile [sanal ağ eşleme ayarları](../virtual-network/virtual-network-manage-peering.md#create-a-peering) hakkında daha fazla bilgi edinin.
* Sanal ağ eşleme ve ağ geçidi aktarımı ile [merkez ve uç ağ topolojisi oluşturma](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) hakkında bilgi edinin.
* [Aynı dağıtım modeliyle sanal ağ eşlemesi oluşturun](../virtual-network/tutorial-connect-virtual-networks-portal.md).
* [Farklı dağıtım modelleriyle sanal ağ eşlemesi oluşturun](../virtual-network/create-peering-different-deployment-models.md).