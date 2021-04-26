---
title: 'Azure ExpressRoute: ExpressRoute Global Reach yapılandırma: CLı'
description: Azure CLı kullanarak, şirket içi ağlarınızla özel bir ağ oluşturmak ve Global Reach etkinleştirmek için ExpressRoute bağlantı hatlarını birbirine bağlamayı öğrenin.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 55f1e4c030dc0e7c8d0011dcff0cc1c9f8fec06e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99260240"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Azure CLı kullanarak ExpressRoute Global Reach yapılandırma

Bu makale, Azure CLı kullanarak Azure ExpressRoute Global Reach yapılandırmanıza yardımcı olur. Daha fazla bilgi için bkz. [ExpressRoute Global Reach](expressroute-global-reach.md).
 
Yapılandırmaya başlamadan önce, aşağıdaki gereksinimleri doldurun:

* Azure CLı 'nın en son sürümünü yükler. Bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli) ve [Azure CLI kullanmaya başlama](/cli/azure/get-started-with-azure-cli).
* ExpressRoute devre sağlama [iş akışlarını](expressroute-workflows.md)anlayın.
* ExpressRoute Devrelerinizin sağlanan durumda olduğundan emin olun.
* ExpressRoute devrelerinizi Azure özel eşlemeden yapılandırıldığından emin olun.  

### <a name="sign-in-to-your-azure-account"></a>Azure hesabınızda oturum açma

Yapılandırmayı başlatmak için Azure hesabınızda oturum açın. Aşağıdaki komut, varsayılan tarayıcınızı açar ve Azure hesabınız için oturum açma kimlik bilgilerini ister:  

```azurecli
az login
```

Birden çok Azure aboneliğiniz varsa, hesap için abonelikleri kontrol edin:

```azurecli
az account list
```

Kullanmak istediğiniz aboneliği belirtin:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Yapılandırma için ExpressRoute devrelerinizi tanımla

İki ExpressRoute bağlantı hattı arasında ExpressRoute Global Reach etkinleştirebilirsiniz. Devrelerin desteklenen ülkelerde/bölgelerde olması ve farklı eşleme konumlarında oluşturulması gerekir. Aboneliğiniz her iki devrede sahipse, yapılandırmayı çalıştırmak için devre öğesini seçebilirsiniz. Ancak, iki devre farklı Azure aboneliklerdeyse, devrelerden birinden bir yetkilendirme anahtarı oluşturmanız gerekir. İlk devreye göre oluşturulan yetkilendirme anahtarını kullanarak ikinci devre üzerinde Global Reach etkinleştirebilirsiniz.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Şirket içi ağlarınız arasında bağlantıyı etkinleştirin

Bağlantıyı etkinleştirmek için komutunu çalıştırırken parametre değerleri için aşağıdaki gereksinimleri dikkate alın:

* *eş devre* , tam kaynak kimliği olmalıdır. Örnek:

  > /Subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering

* *Adres ön eki* bir "/29" IPv4 alt ağı olmalıdır (örneğin, "10.0.0.0/29"). İki ExpressRoute devreleri arasında bağlantı kurmak için bu alt ağdaki IP adreslerini kullanıyoruz. Bu alt ağdaki adresleri Azure sanal ağlarınızda veya şirket içi ağlarınızda kullanamazsınız.

İki ExpressRoute devresine bağlanmak için aşağıdaki CLı komutunu çalıştırın:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

CLı çıkışı şuna benzer:

```output
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Bu işlem tamamlandığında, iki ExpressRoute devreniz aracılığıyla şirket içi ağlarınızla her iki tarafa da bağlantınız olur.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Farklı Azure aboneliklerinde ExpressRoute devreleri arasında bağlantıyı etkinleştirme

İki devre aynı Azure aboneliğinde değilse, yetkilendirme gerekir. Aşağıdaki yapılandırmada, devre 2 ' nin aboneliğinde yetkilendirme oluşturursunuz. Ardından, yetkilendirme anahtarını devre 1 ' e geçirirsiniz.

1. Yetkilendirme anahtarı oluştur:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   CLı çıkışı şuna benzer:

   ```output
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. Devre 2 için hem kaynak KIMLIĞI hem de yetkilendirme anahtarı ' nı bir yere göz önünde yapın.

1. Devre 1 ' de, devre 2 ' nin kaynak KIMLIĞI ve yetkilendirme anahtarını geçirerek aşağıdaki komutu çalıştırın:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Bu işlem tamamlandığında, iki ExpressRoute devreniz aracılığıyla şirket içi ağlarınızla her iki tarafa da bağlantınız olur.

## <a name="get-and-verify-the-configuration"></a>Yapılandırmayı edinme ve doğrulama

Yapılandırmanın yapıldığı devre üzerindeki yapılandırmayı doğrulamak için aşağıdaki komutu kullanın (önceki örnekte devre 1):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

CLı çıktısında, *devre bağlantı durumu*' nu görürsünüz. Bu, iki bağlantı ("bağlı") veya kurulmadı ("bağlantısı kesildi") arasındaki bağlantının yapılıp yapılmayacağını belirtir. 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Şirket içi ağlarınızla bağlantıyı devre dışı bırakma

Bağlantıyı devre dışı bırakmak için, yapılandırmanın yapıldığı devreye karşı aşağıdaki komutu çalıştırın (önceki örnekte devre 1).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

```show```Durumu doğrulamak için komutunu kullanın.

Bu işlem tamamlandığında, ExpressRoute bağlantı hatlarınız aracılığıyla şirket içi ağlarınızla bağlantınız olmayacaktır.

## <a name="next-steps"></a>Sonraki adımlar

* [ExpressRoute Global Reach hakkında daha fazla bilgi edinin](expressroute-global-reach.md)
* [ExpressRoute bağlantısını doğrulama](expressroute-troubleshooting-expressroute-overview.md)
* [Bir ExpressRoute bağlantı hattını bir sanal ağa bağlama](expressroute-howto-linkvnet-arm.md)
