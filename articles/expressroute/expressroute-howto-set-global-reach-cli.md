---
title: 'Azure ExpressRoute: ExpressRoute Global Reach yapılandırma: CLı'
description: Bu makalede, yardımcı birlikte özel ağ arasında şirket içi ağlarınız ve Global erişim etkinleştirme yapmak için ExpressRoute bağlantı hattına bağlayın.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: jaredro
ms.openlocfilehash: eda0011ea4d259d0e60cb894c2b42325ddfc2eb7
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076626"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Azure CLı kullanarak ExpressRoute Global Reach yapılandırma

Bu makale, Azure CLı kullanarak Azure ExpressRoute Global Reach yapılandırmanıza yardımcı olur. Daha fazla bilgi için bkz. [ExpressRoute Global Reach](expressroute-global-reach.md).
 
Yapılandırmaya başlamadan önce, aşağıdaki gereksinimleri doldurun:

* Azure CLı 'nın en son sürümünü yükler. Bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli) ve [Azure CLI kullanmaya başlama](/cli/azure/get-started-with-azure-cli).
* ExpressRoute devre sağlama [iş akışlarını](expressroute-workflows.md)anlayın.
* ExpressRoute Devrelerinizin sağlanan durumda olduğundan emin olun.
* Azure özel eşdüzey hizmet sağlama, ExpressRoute bağlantı hatları üzerinde yapılandırıldığından emin olun.  

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

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Yapılandırma için ExpressRoute devreleri tanımlayın

Desteklenen ülkelerde/bölgelerde bulundukları ve farklı eşleme konumlarında oluşturulan her iki ExpressRoute bağlantı hattı arasında ExpressRoute Global Reach etkinleştirebilirsiniz. Aboneliğiniz her iki devrede sahipse, bu makalenin ilerleyen kısımlarında açıklandığı gibi yapılandırmayı çalıştırmaya yönelik her iki devreyi de seçebilirsiniz. İki devre farklı Azure aboneliklerdeyse, bir Azure aboneliğinden yetkilendirmeniz ve diğer Azure aboneliğinde yapılandırma komutunu çalıştırdığınızda yetkilendirme anahtarını geçmesi gerekir.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Şirket içi ağlarınızı arasındaki bağlantıyı etkinleştir

Bağlantıyı etkinleştirmek için komutunu çalıştırırken parametre değerleri için aşağıdaki gereksinimleri dikkate alın:

* *eş devre* , tam kaynak kimliği olmalıdır. Örneğin:

  > /Subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *Adres ön eki* bir "/29" IPv4 alt ağı olmalıdır (örneğin, "10.0.0.0/29"). İki ExpressRoute devreleri arasında bağlantı kurmak için bu alt ağdaki IP adreslerini kullanıyoruz. Azure sanal ağlarınızda veya şirket içi ağlarınızda bu alt ağda yer alan adresleri kullanmanız gerekir.

İki ExpressRoute devresine bağlanmak için aşağıdaki CLı komutunu çalıştırın:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

CLı çıkışı şuna benzer:

```azurecli
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

İki devre aynı Azure aboneliğinde değilse, yetkilendirme gerekir. Aşağıdaki yapılandırmada, devre 2 aboneliğindeki yetkilendirmeyi oluşturur ve yetkilendirme anahtarını devre 1 ' e geçitirsiniz.

1. Yetkilendirme anahtarı oluştur:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   CLı çıkışı şuna benzer:

   ```azurecli
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

## <a name="get-and-verify-the-configuration"></a>Alma ve yapılandırmayı doğrulama

Yapılandırmanın yapıldığı devre üzerindeki yapılandırmayı doğrulamak için aşağıdaki komutu kullanın (önceki örnekte devre 1):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

CLı çıktısında, *devre bağlantı durumu*' nu görürsünüz. Bu, iki bağlantı ("bağlı") veya kurulmadı ("bağlantısı kesildi") arasındaki bağlantının yapılıp yapılmayacağını belirtir. 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Şirket içi ağlarınız arasında bağlantı devre dışı bırak

Bağlantıyı devre dışı bırakmak için, yapılandırmanın yapıldığı devreye karşı aşağıdaki komutu çalıştırın (önceki örnekte devre 1).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Durumu doğrulamak için ```show``` komutunu kullanın.

Bu işlem tamamlandığında, ExpressRoute bağlantı hatlarınız aracılığıyla şirket içi ağlarınızla bağlantınız olmayacaktır.

## <a name="next-steps"></a>Sonraki adımlar

* [ExpressRoute Global erişim hakkında daha fazla bilgi edinin](expressroute-global-reach.md)
* [ExpressRoute bağlantısını doğrulama](expressroute-troubleshooting-expressroute-overview.md)
* [Bir ExpressRoute bağlantı hattını bir sanal ağa bağlama](expressroute-howto-linkvnet-arm.md)
