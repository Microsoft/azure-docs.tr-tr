---
title: 'Azure ExpressRoute: sanal ağı devre dışı bağlantı: CLı'
description: Bu makalede, CLI ve Resource Manager dağıtım modeli kullanarak ExpressRoute bağlantı hatları için sanal ağlar (Vnet'ler) bağlantı işlemini göstermektedir.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: a8814030e6c4345227ec05ea1554104e0b21efbc
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076552"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>CLI kullanarak bir ExpressRoute bağlantı hattına bir sanal ağı bağlama

Bu makalede, sanal ağlar (Vnet'ler) bağlantı CLI kullanarak Azure ExpressRoute bağlantı hatları için yardımcı olur. Azure CLI kullanarak bağlamak için sanal ağları Resource Manager dağıtım modeli kullanılarak oluşturulmuş olması gerekir. Bunlar aynı abonelik veya başka bir abonelik parçası olabilir. Bir ExpressRoute bağlantı hattı için sanal ağınıza bağlanmak için farklı bir yöntem kullanmak istiyorsanız, aşağıdaki listeden bir makale seçebilirsiniz:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure portalı](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Yapılandırma önkoşulları

* Komut satırı arabirimi (CLI) en son sürümü gerekir. Daha fazla bilgi için [Azure CLI'yı yükleme](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Gözden geçirmeniz gereken [önkoşulları](expressroute-prerequisites.md), [yönlendirme gereksinimleri](expressroute-routing.md), ve [iş akışları](expressroute-workflows.md) yapılandırmaya başlamadan önce.

* Etkin bir ExpressRoute bağlantı hattınızın olması gerekir. 
  * Yönergelerini izleyin [ExpressRoute devresi oluşturma](howto-circuit-cli.md) ve bağlantı hattı, bağlantı sağlayıcı tarafından etkin. 
  * Bağlantı hattınız için yapılandırılmış Azure özel eşleme olduğundan emin olun. Bkz: [yönlendirmeyi yapılandırma](howto-routing-cli.md) makale için yönlendirme yönergeleri. 
  * Azure özel eşdüzey hizmet sağlama yapılandırıldığından emin olun. Böylece uçtan uca bağlantıyı etkinleştirmek BGP ağınız ile Microsoft arasında eşleme ayarlama olması gerekir.
  * Bir sanal ağ ve oluşturulan ve tam olarak sağlanan sanal ağ geçidi olduğundan emin olun. Yönergelerini izleyin [ExpressRoute için sanal ağ geçidi yapılandırma](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Kullandığınızdan emin olun `--gateway-type ExpressRoute`.

* Standart bir ExpressRoute bağlantı hattı için en fazla 10 sanal ağlara bağlayabilirsiniz. Tüm sanal ağları, standart bir ExpressRoute bağlantı hattını kullanırken aynı jeopolitik bölgede olması gerekir. 

* En fazla dört ExpressRoute bağlantı hatları için tek bir sanal ağa bağlanabilir. Bağlanmakta olduğunuz her bir ExpressRoute bağlantı hattı için yeni bir bağlantı nesnesi oluşturmak için aşağıdaki işlemi kullanın. ExpressRoute bağlantı hatları, aynı abonelik, farklı Aboneliklerde veya her ikisinin bir karışımı olabilir.

* ExpressRoute premium eklentisi etkinleştirirseniz, bir sanal ağ ExpressRoute bağlantı hattının coğrafi bölge dışında bağlama veya çok sayıda sanal ağları ExpressRoute devreniz bağlanın. Premium eklenti hakkında daha fazla bilgi için bkz: [SSS](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Bir sanal ağ ile aynı abonelikte devreye bağlama

Örnek kullanarak ExpressRoute bağlantı hattına bir sanal ağ geçidine bağlanabilir. Sanal ağ geçidi oluşturulur ve komutu çalıştırmadan önce bağlama için hazır olduğundan emin olun.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Farklı abonelikteki bir sanal ağı devreye bağlama

Bir ExpressRoute bağlantı hattı birden çok farklı abonelikler arasında paylaşabilirsiniz. Aşağıdaki şekilde basit bir ExpressRoute bağlantı hatları için nasıl paylaşım Works şematik, birden fazla aboneliği analiz gösterilmektedir.

Her küçük bulutların büyük bulut içinde bir kuruluştaki farklı departmanlara ait abonelikleri temsil etmek için kullanılır. Her kuruluş içindeki bölümlerin dağıtmak için kendi hizmetlerini--ancak kendi aboneliğini kullanabilirsiniz, şirket içi ağınıza bağlanmak için tek bir ExpressRoute bağlantı hattı paylaşabilirsiniz. Tek bir bölüm (Bu örnekte: BT) ExpressRoute bağlantı hattına sahip olabilir. Kuruluştaki diğer abonelikler, ExpressRoute bağlantı hattı kullanabilirsiniz.

> [!NOTE]
> ExpressRoute bağlantı hattı sahibinden için adanmış bir bağlantı hattı için bağlantı ve bant genişliği ücretleri uygulanır. Tüm sanal ağları, aynı bant genişliğini paylaşır.
> 
> 

![Çapraz abonelik bağlantısı](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Yönetim - bağlantı hattı sahibi ve bağlantı hattı kullanıcıları

'Bağlantı hattı sahibinden' ExpressRoute bağlantı hattı kaynak yetkili bir güç kullanıcıdır. Bağlantı hattı sahibinden 'bağlantı hattı kullanıcılar tarafından ödenebilecek yetkilendirmeleri oluşturabilirsiniz. ExpressRoute bağlantı hattı aynı abonelik içinde olmayan sanal ağ geçitleri sahipleri bağlantı hattını kullanıcılardır. Devre kullanıcılarının, yetkilendirmeleri (sanal ağ başına bir yetkilendirme) kullanmak.

Bağlantı hattı sahibinden yetkilendirme dilediğiniz zaman iptal et ve değiştirmek için gücüne sahiptir. Bir yetkilendirme iptal edildiğinde, tüm bağlantı erişimini iptal edildi abonelikten silinir.

### <a name="circuit-owner-operations"></a>Bağlantı hattı sahibi işlemleri

**Bir yetkilendirme oluşturmak için**

Bağlantı hattı sahibinden bağlantı hattı kullanıcı tarafından ExpressRoute bağlantı hattına kendi sanal ağ geçitlerine bağlanmak için kullanılan bir yetkilendirme anahtarı oluşturur bir yetkilendirme oluşturur. Bir yetkilendirme yalnızca bir bağlantı için geçerli değil.

Aşağıdaki örnek, bir yetkilendirme oluşturma işlemi gösterilmektedir:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Yanıt, durum ve yetkilendirme anahtarını içerir:

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Yetkilendirmeleri gözden geçirmek için**

Bağlantı hattı sahibinden belirli bir bağlantı hattı üzerinde aşağıdaki örneği çalıştırmadan tarafından verilen tüm yetkilendirmeleri gözden geçirebilirsiniz:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Yetkilendirmeleri eklemek için**

Bağlantı hattı sahibinden yetkilendirme, aşağıdaki örneği kullanarak ekleyebilirsiniz:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Yetkilendirmeleri silmek için**

Bağlantı hattı sahibinden iptal etme/yetkilendirmeleri kullanıcıya aşağıdaki örnekte çalıştırarak ya da silebilir:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Bağlantı hattı kullanıcı işlemleri

Bağlantı hattı kullanıcısı eş kimliği ve bağlantı hattı sahibinden yetkilendirme anahtarı gerekir. Yetkilendirme anahtarı bir GUID'dir.

```azurecli
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Bağlantı Yetkilendirme kullanmak için**

Bağlantı hattı kullanıcısı bağlantı yetkilendirme kullanmak için aşağıdaki örnek çalıştırabilirsiniz:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Bağlantı Yetkilendirme serbest bırakmak için**

Sanal ağı ExpressRoute bağlantı hattına bağlayan bağlantı silerek bir yetkilendirme serbest bırakabilirsiniz.

## <a name="modify-a-virtual-network-connection"></a>Sanal ağ bağlantısı değiştirme
Belirli bir sanal ağ bağlantısı özellikleri güncelleştirebilirsiniz. 

**Bağlantı ağırlığına güncelleştirmek için**

Sanal ağınız için birden çok ExpressRoute bağlantı hattına bağlı olabilir. Aynı öneke birden fazla ExpressRoute devresinden alabilirsiniz. Bu ön eki hedefleyen trafiği göndermek için hangi bağlantı seçmek için değiştirebileceğiniz *RoutingWeight* bağlantısı. Trafik, en yüksek bağlantı gönderilecek *RoutingWeight*.

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Aralığı *RoutingWeight* 0-32000. Varsayılan değer 0’dır.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath yapılandırma 
ExpressRoute bağlantı hattınızı [ExpressRoute Direct](expressroute-erdirect-about.md) üzerinde ise ve sanal ağ seçin ağ geçidiniz Ultra Performance veya ErGw3AZ ise [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) 'i etkinleştirebilirsiniz. FastPath, şirket içi ağınız ve sanal ağınız arasında saniyedeki paket sayısı ve saniye başına bağlantı gibi veri yolu preformance geliştirir. 

> [!NOTE] 
> Zaten bir sanal ağ bağlantınız varsa ancak FastPath 'i etkinleştirmediyseniz, sanal ağ bağlantısını silmeniz ve yeni bir bağlantı oluşturmanız gerekir. 
> 
>  

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```


## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).
