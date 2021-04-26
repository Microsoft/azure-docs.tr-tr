---
title: "Azure VPN Gateway BGP 'yi yapılandırma: CLı"
description: Bu makalede, Azure Resource Manager ve CLı kullanarak Azure VPN ağ geçidi ile BGP 'yi yapılandırma işlemi adım adım açıklanmaktadır.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: a69ce0592b79be0868dd7c15ac054910eee75fc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89393607"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>CLı kullanarak bir Azure VPN ağ geçidinde BGP 'yi yapılandırma

Bu makale, şirket içi siteden siteye (S2S) VPN bağlantısı ve VNet-VNet bağlantısı (diğer bir deyişle, sanal ağlar arasında bağlantı) Azure Resource Manager dağıtım modeli ve Azure CLı kullanarak BGP 'yi etkinleştirmenize yardımcı olur.

## <a name="about-bgp"></a>BGP hakkında

BGP, iki veya daha fazla ağ arasında yönlendirme ve ulaşılabilirlik bilgilerini değiş tokuş etmek için genelde Internet 'te kullanılan standart yönlendirme protokolüdür. BGP, VPN ağ geçitlerini ve BGP eşleri veya komşuları olarak adlandırılan şirket içi VPN cihazlarınızı, yolları Exchange 'e sağlar. Yollar, her iki ağ geçidini, öneklere yönelik kullanılabilirlik ve erişilebilirlik bilgilerini, söz konusu ağ geçitlerinden veya yönlendiricilerle ilgili olarak bilgilendirir. BGP, bir BGP ağ geçidinin bir BGP eşinden öğrendiğini tüm diğer BGP eşlerine yayarak birden çok ağ arasında geçiş yönlendirmeyi de etkinleştirebilir.

BGP 'nin avantajları hakkında daha fazla bilgi için, bkz. [Azure VPN ağ geçitleri Ile BGP 'ye genel bakış](vpn-gateway-bgp-overview.md).

Bu makale aşağıdaki görevlerde size yardımcı olur:

* [VPN ağ geçidiniz IÇIN BGP 'Yi etkinleştirme](#enablebgp) (gerekli)

  Daha sonra aşağıdaki bölümlerden birini veya her ikisini de tamamlayabilirsiniz:

* [BGP ile şirketler arası bağlantı kurma](#crossprembgp)
* [BGP ile VNet-VNet bağlantısı oluşturma](#v2vbgp)

Bu üç bölümden her biri, ağ bağlantınızda BGP 'yi etkinleştirmek için temel bir yapı bloğu oluşturur. Üç bölümü de tamamladıktan sonra, aşağıdaki diyagramda gösterildiği gibi topolojiyi oluşturun:

![BGP topolojisi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Gereksinimlerinizi karşılayan daha karmaşık bir çok duraklı transit ağı oluşturmak için bu bölümleri birleştirebilirsiniz.

## <a name="enable-bgp-for-your-vpn-gateway"></a><a name ="enablebgp"></a>VPN ağ geçidiniz için BGP 'yi etkinleştirme

Bu bölüm, diğer iki yapılandırma bölümündeki adımlardan herhangi birini gerçekleştirmeden önce gereklidir. Aşağıdaki yapılandırma adımları, aşağıdaki diyagramda gösterildiği gibi Azure VPN Gateway 'in BGP parametrelerini ayarlar:

![BGP ağ geçidi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Başlamadan önce

CLı komutlarının en son sürümünü (2,0 veya üzeri) yükler. CLI komutlarını yükleme hakkında bilgi için bkz. [Azure CLI’yi yükleme](/cli/azure/install-azure-cli) ve [Azure CLI’yi Kullanmaya Başlama](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>1. Adım: TestVNet1 oluşturma ve yapılandırma

#### <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. aboneliğinize bağlanın

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. kaynak grubu oluşturma

Aşağıdaki örnek, "eastus" konumunda TestRG1 adlı bir kaynak grubu oluşturur. Sanal ağınızı oluşturmak istediğiniz bölgede zaten bir kaynak grubunuz varsa, bunun yerine bunu kullanabilirsiniz.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 oluşturma

Aşağıdaki örnek, TestVNet1 adlı bir sanal ağ ve üç alt ağ oluşturur: GatewaySubnet, ön uç ve arka uç. Değerleri değiştirirken, ağ geçidi alt ağınızın özel GatewaySubnet 'i her zaman yazmanız önemlidir. Başka bir ad kullanırsanız ağ geçidi oluşturma işleminiz başarısız olur.

İlk komut ön uç adres alanını ve ön uç alt ağını oluşturur. İkinci komut, arka uç alt ağı için ek bir adres alanı oluşturur. Üçüncü ve dördüncü komutlar arka uç alt ağını ve GatewaySubnet 'i oluşturur.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. Adım: BGP parametreleriyle TestVNet1 için VPN Gateway oluşturma

#### <a name="1-create-the-public-ip-address"></a>1. genel IP adresini oluşturma

Genel bir IP adresi isteyin. Genel IP adresi, sanal ağınız için oluşturduğunuz VPN ağ geçidine tahsis edilir.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. AS numarasıyla VPN Gateway oluşturun

TestVNet1 için sanal ağ geçidini oluşturun. BGP Route-Based bir VPN Ağ Geçidi gerektirir. Ayrıca, `-Asn` TestVNet1 için otonom sistem numarası 'nı (ASN) ayarlamak üzere ek parametreye ihtiyacınız vardır. Ağ geçidi oluşturma işleminin tamamlanması biraz zaman alabilir (45 dakika veya daha fazla). 

Bu komutu parametresini kullanarak çalıştırırsanız `--no-wait` , hiçbir geri bildirim veya çıkış görmezsiniz. `--no-wait`Parametresi, ağ geçidinin arka planda oluşturulmasına olanak tanır. VPN ağ geçidinin hemen oluşturulduğu anlamına gelmez.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Azure BGP eş IP adresini edinin

Ağ Geçidi oluşturulduktan sonra, Azure VPN ağ geçidinde BGP eşi IP adresini edinmeniz gerekir. Bu adres, VPN ağ geçidini şirket içi VPN cihazlarınız için BGP eşi olarak yapılandırmak için gereklidir.

Aşağıdaki komutu çalıştırın ve `bgpSettings` çıktının en üstündeki bölümünü kontrol edin:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Ağ Geçidi oluşturulduktan sonra, BGP ile bir şirket içi bağlantı veya VNet-VNet bağlantısı kurmak için bu ağ geçidini kullanabilirsiniz.

## <a name="establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembgp"></a>BGP ile şirketler arası bağlantı kurma

Şirketler arası bağlantı kurmak için şirket içi VPN cihazınızı temsil eden bir yerel ağ geçidi oluşturmanız gerekir. Ardından, Azure VPN ağ geçidini yerel ağ geçidi ile bağlanırsınız. Bu adımlar diğer bağlantıları oluşturmaya benzer olsa da, BGP yapılandırma parametrelerini belirtmek için gereken ek özellikleri içerirler.

![Şirketler arası için BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>1. Adım: yerel ağ geçidini oluşturma ve yapılandırma

Bu alıştırma diyagramda gösterilen yapılandırmayı oluşturmaya devam eder. Değerleri, yapılandırma için kullanmak istediğiniz değerlerle değiştirdiğinizden emin olun. Yerel ağ geçitleri ile çalışırken şunları göz önünde bulundurun:

* Yerel ağ geçidi, VPN ağ geçidiyle aynı konum ve kaynak grubunda olabilir veya farklı bir konum ve kaynak grubunda olabilir. Bu örnekte, farklı konumlardaki farklı kaynak gruplarındaki ağ geçitleri gösterilmektedir.
* Yerel ağ geçidi için bildirmeniz gereken en küçük ön ek, VPN cihazınızdaki BGP eşi IP adresinizin ana bilgisayar adresidir. Bu durumda, 10.51.255.254/32 ' nin bir/32 ön eki olur.
* Bir anımsatıcı olarak, şirket içi ağlarınız ve Azure sanal ağı arasında farklı BGP 'ler kullanmanız gerekir. Aynı ise, şirket içi VPN cihazlarınız diğer BGP komşuları ile ASN 'yi zaten kullanıyorsa VNet ASN 'nizi değiştirmeniz gerekir.

Devam etmeden önce, bu alıştırmanın [VPN Gateway IÇIN BGP 'Yi etkinleştir](#enablebgp) bölümünü tamamladığınızdan ve yine de 1. aboneliğe bağlı olduğunuzdan emin olun. Bu örnekte, yeni bir kaynak grubu oluşturduğunuz hakkında dikkat edin. Ayrıca, yerel ağ geçidi için iki ek parametreye de dikkat edin: `Asn` ve `BgpPeerAddress` .

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>2. Adım: VNet ağ geçidini ve yerel ağ geçidini bağlama

Bu adımda, TestVNet1 ile site5 arasındaki bağlantıyı oluşturacaksınız. `--enable-bgp`Bu bağlantı IÇIN BGP 'yi etkinleştirmek üzere parametresini belirtmeniz gerekir. 

Bu örnekte, sanal ağ geçidi ve yerel ağ geçidi farklı kaynak gruplarıdır. Ağ geçitleri farklı kaynak gruplarında olduğunda, sanal ağlar arasında bir bağlantı kurmak için iki ağ geçidinin tüm kaynak KIMLIĞINI belirtmeniz gerekir.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. VNet1GW kaynak KIMLIĞINI alın

VNet1GW kaynak KIMLIĞINI almak için aşağıdaki komuttan gelen çıktıyı kullanın:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Çıktıda `"id":` satırı bulun. Sonraki bölümde bağlantıyı oluşturmak için tırnak işaretleri içindeki değerlere ihtiyacınız vardır.

Örnek çıktı:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

`"id":`Bağlantıyı oluştururken kolayca yapıştırabilmeniz Için Not Defteri gibi bir metin düzenleyicisinde değerleri kopyalayın. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. site5 kaynak KIMLIĞINI alın

Site5 kaynak KIMLIĞINI çıktıdan almak için aşağıdaki komutu kullanın:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. TestVNet1-to-site5 bağlantısı oluşturma

Bu adımda, TestVNet1 ile site5 arasındaki bağlantıyı oluşturacaksınız. Daha önce anlatıldığı gibi, aynı Azure VPN ağ geçidi için BGP ve BGP olmayan bağlantılara sahip olmak mümkündür. Bağlantı özelliğinde BGP etkinleştirilmemişse, Azure Bu bağlantı için BGP 'yi etkinleştirmez, ancak her iki ağ geçidi üzerinde BGP parametreleri zaten yapılandırılmış olsa bile. Abonelik kimliklerini kendi ile değiştirin.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Bu alıştırma için aşağıdaki örnekte, şirket içi VPN cihazınızın BGP yapılandırması bölümüne girilecek parametreler listelenmektedir:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Bağlantı birkaç dakika içerisinde kurulacaktır. BGP eşleme oturumu IPSec bağlantısı kurulduktan sonra başlar.

## <a name="establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>BGP ile VNet-VNet bağlantısı oluşturma

Bu bölüm, aşağıdaki diyagramda gösterildiği gibi BGP ile VNet-VNet bağlantısı ekler: 

![VNet-VNet için BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Aşağıdaki yönergeler önceki bölümlerdeki adımlardan devam eder. BGP ile TestVNet1 ve VPN Gateway oluşturmak ve yapılandırmak için [VPN ağ geçidiniz IÇIN BGP 'Yi etkinleştirme](#enablebgp) bölümüne tamamlamalısınız.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>1. Adım: TestVNet2 ve VPN Gateway oluşturma

Yeni sanal ağın IP adresi alanının, TestVNet2, VNet aralıklarından herhangi biriyle çakışmadığından emin olmak önemlidir.

Bu örnekte, sanal ağlar aynı aboneliğe aittir. Farklı abonelikler arasında VNet-VNet bağlantıları ayarlayabilirsiniz. Daha fazla bilgi için bkz. [VNET 'Ten VNET 'e bağlantı yapılandırma](vpn-gateway-howto-vnet-vnet-cli.md). `-EnableBgp $True`BGP 'yi etkinleştirmek için bağlantıları oluştururken eklediğinizden emin olun.

#### <a name="1-create-a-new-resource-group"></a>1. yeni bir kaynak grubu oluşturun

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. yeni kaynak grubunda TestVNet2 oluşturun

İlk komut ön uç adres alanını ve ön uç alt ağını oluşturur. İkinci komut, arka uç alt ağı için ek bir adres alanı oluşturur. Üçüncü ve dördüncü komutlar arka uç alt ağını ve GatewaySubnet 'i oluşturur.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. genel IP adresini oluşturma

Genel bir IP adresi isteyin. Genel IP adresi, sanal ağınız için oluşturduğunuz VPN ağ geçidine tahsis edilir.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. AS numarasıyla VPN Gateway oluşturun

TestVNet2 için sanal ağ geçidini oluşturun. Azure VPN ağ geçitleriniz üzerindeki varsayılan ASN 'yi geçersiz kılmanız gerekir. Bağlı sanal ağların ASNs 'leri BGP ve transit yönlendirmeyi etkinleştirmek için farklı olmalıdır.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>2. Adım: TestVNet1 ve TestVNet2 ağ geçitlerini bağlama

Bu adımda, TestVNet1 ile site5 arasındaki bağlantıyı oluşturacaksınız. Bu bağlantı için BGP 'yi etkinleştirmek üzere parametresini belirtmeniz gerekir `--enable-bgp` .

Aşağıdaki örnekte, sanal ağ geçidi ve yerel ağ geçidi farklı kaynak gruplarıdır. Ağ geçitleri farklı kaynak gruplarında olduğunda, sanal ağlar arasında bir bağlantı kurmak için iki ağ geçidinin tüm kaynak KIMLIĞINI belirtmeniz gerekir. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. VNet1GW kaynak KIMLIĞINI alın 

Aşağıdaki komutun çıktısından VNet1GW kaynak KIMLIĞINI alın:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. VNet2GW kaynak KIMLIĞINI alın

Aşağıdaki komutun çıktısından VNet2GW kaynak KIMLIĞINI alın:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. bağlantıları oluşturma

TestVNet1 ile TestVNet2 arasındaki bağlantıyı ve TestVNet2 'den TestVNet1 'e bağlantıyı oluşturun. Abonelik kimliklerini kendi ile değiştirin.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> *Her iki* bağlantı için BGP 'yi etkinleştirin.
> 
> 

Bu adımları tamamladıktan sonra bağlantı birkaç dakika içinde kurulacaktır. BGP eşleme oturumu, VNet 'ten VNet 'e bağlantı tamamlandıktan sonra çalışır.

## <a name="next-steps"></a>Sonraki adımlar

Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Adımlar için bkz. [sanal makine oluşturma](../virtual-machines/windows/quick-create-portal.md).
