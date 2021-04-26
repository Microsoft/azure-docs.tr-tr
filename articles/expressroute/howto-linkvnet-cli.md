---
title: 'Öğretici: bir sanal ağı ExpressRoute devresine bağlama-Azure CLı'
description: Bu öğreticide, Kaynak Yöneticisi dağıtım modeli ve Azure CLı kullanarak sanal ağların (VNet) ExpressRoute devrelerine nasıl bağlayabileceğiniz gösterilmektedir.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: 90676b134d06af7ef088b2041116b17022da2700
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110232"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Öğretici: CLı kullanarak bir ExpressRoute devresine sanal ağ bağlama

Bu öğreticide, Azure CLı kullanarak sanal ağların (VNet) Azure ExpressRoute devrelerine nasıl bağlayabileceğiniz gösterilmektedir. Azure CLı kullanarak bağlantı için, sanal ağların Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulması gerekir. Aynı abonelikte ya da başka bir aboneliğin parçası olabilir. VNet 'nizi bir ExpressRoute devresine bağlamak için farklı bir yöntem kullanmak istiyorsanız, aşağıdaki listeden bir makale seçebilirsiniz:

> [!div class="op_single_selector"]
> * [Azure portalı](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video-Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-linkvnet-classic.md)
> 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - Aynı abonelikteki bir sanal ağı bir devreye bağlama
> - Farklı abonelikteki bir sanal ağı devreye bağlama
> - Bir sanal ağ bağlantısını değiştirme
> - ExpressRoute FastPath yapılandırma

## <a name="prerequisites"></a>Önkoşullar

* En son komut satırı arabirimi (CLı) sürümüne ihtiyacınız vardır. Daha fazla bilgi için bkz. [Azure CLI 'Yı yüklerken](/cli/azure/install-azure-cli).
* Yapılandırmaya başlamadan önce [önkoşulları](expressroute-prerequisites.md), [yönlendirme gereksinimlerini](expressroute-routing.md)ve [iş akışlarını](expressroute-workflows.md) gözden geçirin.
* Etkin bir ExpressRoute bağlantı hattınızın olması gerekir. 
  * [ExpressRoute](howto-circuit-cli.md) bağlantı hattı oluşturmak için yönergeleri izleyin ve bağlantı sağlayıcınız tarafından devre dışı bırakıldı. 
  * Devreniz için Azure özel eşlemesi 'nin yapılandırıldığından emin olun. Yönlendirme yönergeleri için [yönlendirmeyi yapılandırma](howto-routing-cli.md) makalesine bakın. 
  * Azure özel eşlemesinin yapılandırıldığından emin olun. Uçtan uca bağlantıyı etkinleştirebilmeniz için ağınız ve Microsoft arasındaki BGP eşlemesi oluşturulmalıdır.
  * Oluşturulmuş ve tam olarak sağlanmış bir sanal ağa ve sanal ağ geçidine sahip olduğunuzdan emin olun. [ExpressRoute için sanal ağ geçidi yapılandırma](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)yönergelerini izleyin. ' İ kullandığınızdan emin olun `--gateway-type ExpressRoute` .
* 10 adede kadar sanal ağı standart bir ExpressRoute devresine bağlayabilirsiniz. Standart bir ExpressRoute bağlantı hattı kullanılırken tüm sanal ağların aynı geopolitik bölgede olması gerekir. 
* Tek bir sanal ağ, en fazla 16 ExpressRoute devresine bağlanabilir. Bağlanmakta olduğunuz her ExpressRoute devresi için yeni bir bağlantı nesnesi oluşturmak üzere aşağıdaki işlemi kullanın. ExpressRoute devreleri aynı abonelikte, farklı aboneliklerde veya her ikisinin karışımı olabilir.
* ExpressRoute Premium eklentisini etkinleştirirseniz, ExpressRoute devresinin coğrafi bölgesinin dışındaki sanal ağları bağlayabilirsiniz. Premium eklenti Ayrıca, seçilen bant genişliğine bağlı olarak ExpressRoute bağlantı hattına 10 ' dan fazla sanal ağ bağlamanıza olanak tanır. Premium eklenti hakkında daha fazla bilgi için [SSS bölümüne](expressroute-faqs.md) bakın.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Aynı abonelikteki bir sanal ağı bir devreye bağlama

Örneği kullanarak bir ExpressRoute devresine bir sanal ağ geçidini bağlayabilirsiniz. Komutu çalıştırmadan önce, sanal ağ geçidinin oluşturulduğundan ve bağlamaya hazırlandığınızdan emin olun.

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Farklı abonelikteki bir sanal ağı devreye bağlama

Bir ExpressRoute devresini birden çok abonelik arasında paylaşabilirsiniz. Aşağıdaki şekilde, birden çok aboneliğin ExpressRoute devreleri için paylaşımın nasıl çalıştığına ilişkin basit bir şematik gösterilmektedir.

Büyük buluttaki küçük bulutların her biri, bir kuruluştaki farklı departmanlara ait olan abonelikleri temsil etmek için kullanılır. Kuruluştaki bölümlerin her biri, hizmetlerini dağıtmak için kendi aboneliğini kullanır, ancak şirket içi ağınıza geri bağlanmak için tek bir ExpressRoute bağlantı hattı paylaşabilir. Tek bir departman (Bu örnekte:) ExpressRoute devresine sahip olabilir. Kuruluştaki diğer abonelikler ExpressRoute devresini kullanabilir.

> [!NOTE]
> Adanmış devre için bağlantı ve bant genişliği ücretleri ExpressRoute bağlantı hattı sahibine uygulanır. Tüm sanal ağlar aynı bant genişliğini paylaşır.
> 
> 

![Çapraz abonelik bağlantısı](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Yönetim-devre sahipleri ve devre kullanıcıları

' Devowner ', ExpressRoute bağlantı hattı kaynağının yetkili bir uzman kullanıcısı. Devre sahibi, ' devre kullanıcıları ' tarafından kullanılabilecek yetkilendirmeler oluşturabilir. Devre kullanıcıları ExpressRoute bağlantı hattı ile aynı abonelikte olmayan sanal ağ geçitlerinin sahiplerinden oluşur. Devre kullanıcıları yetkilendirmeleri (sanal ağ başına bir yetkilendirme) kullanabilir.

Devre sahibinin, her zaman yetkilendirmeleri değiştirme ve iptal etme gücü vardır. Yetkilendirme iptal edildiğinde, tüm bağlantı bağlantıları, erişimi iptal edilen abonelikten silinir.

### <a name="circuit-owner-operations"></a>Devre sahibi işlemleri

**Yetkilendirme oluşturmak için**

Devre sahibi, sanal ağ geçitlerini ExpressRoute devresine bağlamak için bir devre kullanıcısı tarafından kullanılacak bir yetkilendirme anahtarı oluşturan bir yetkilendirme oluşturur. Yetkilendirme yalnızca bir bağlantı için geçerlidir.

Aşağıdaki örnek nasıl bir yetkilendirme oluşturulacağını göstermektedir:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Yanıt, yetkilendirme anahtarını ve durumunu içerir:

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Yetkilendirmeleri gözden geçirmek için**

Devre sahibi, aşağıdaki örneği çalıştırarak belirli bir devreye göre verilen tüm yetkilendirmeleri gözden geçirebilir:

```azurecli-interactive
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Yetkilendirmeler eklemek için**

Devre sahibi aşağıdaki örneği kullanarak yetkilendirmeler ekleyebilir:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Yetkilendirmeleri silmek için**

Devre sahibi, aşağıdaki örneği çalıştırarak kullanıcıya yetkilendirmeleri iptal edebilir/silebilir:

```azurecli-interactive
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Devre Kullanıcı işlemleri

Devre kullanıcısının, devre sahibinden eş KIMLIĞI ve yetkilendirme anahtarı olması gerekir. Yetkilendirme anahtarı bir GUID 'dir.

```azurecli-interactive
az network express-route show -n MyCircuit -g ExpressRouteResourceGroup
```

**Bir bağlantı yetkilendirmesini kullanma**

Devre kullanıcısı bir bağlantı yetkilendirmesi kullanmak için aşağıdaki örneği çalıştırabilir:

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Bir bağlantı yetkilendirmesini serbest bırakmak için**

ExpressRoute bağlantı hattını sanal ağa bağlayan bağlantıyı silerek bir yetkilendirmeyi serbest bırakabilirsiniz.

## <a name="modify-a-virtual-network-connection"></a>Bir sanal ağ bağlantısını değiştirme
Bir sanal ağ bağlantısının belirli özelliklerini güncelleştirebilirsiniz. 

**Bağlantı ağırlığını güncelleştirmek için**

Sanal ağınız, birden fazla ExpressRoute devresine bağlanabilir. Birden fazla ExpressRoute devreninden aynı öneki alabilirsiniz. Bu önek için hangi trafiğin gönderileceğini seçmek üzere bir bağlantının *Routingweight* değerini değiştirebilirsiniz. Trafik en yüksek *Routingweight* bağlantısı üzerinden gönderilir.

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

*Routingweight* aralığı 0 ile 32000 arasındadır. Varsayılan değer 0’dır.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath yapılandırma 
Sanal ağ geçidiniz Ultra Performance veya ErGw3AZ ise [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) 'i etkinleştirebilirsiniz. FastPath, şirket içi ağınız ve sanal ağınız arasında saniye başına paket ve bağlantı gibi veri yolu performansını geliştirir. 

**Yeni bir bağlantıda FastPath yapılandırma**

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**FastPath 'i etkinleştirmek için mevcut bir bağlantı güncelleştiriliyor**

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```
## <a name="clean-up-resources"></a>Kaynakları temizleme

ExpressRoute bağlantısına artık ihtiyacınız yoksa, ağ geçidinin bulunduğu abonelikte `az network vpn-connection delete` ağ geçidi ve bağlantı hattı arasındaki bağlantıyı kaldırmak için komutunu kullanın.

```azurecli-interactive
az network vpn-connection delete --name ERConnection --resource-group ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, sanal bir ağı aynı abonelikte ve farklı bir abonelikte bir devreye bağlamayı öğrendiniz. ExpressRoute ağ geçidi hakkında daha fazla bilgi için bkz.: 

> [!div class="nextstepaction"]
> [ExpressRoute sanal ağ geçitleri hakkında](expressroute-about-virtual-network-gateways.md)
