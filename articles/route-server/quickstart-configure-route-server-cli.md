---
title: 'Hızlı başlangıç: Azure CLı kullanarak rota sunucusu oluşturma ve yapılandırma'
description: Bu hızlı başlangıçta, Azure CLı kullanarak bir rota sunucusu oluşturma ve yapılandırma hakkında bilgi edineceksiniz.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: e9c583db7493afc04b2c66553801f62d364b0a80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419617"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak rota sunucusu oluşturma ve yapılandırma 

Bu makale, Azure CLı kullanarak sanal ağınızdaki ağ sanal gereçlerine (NVA) sahip olarak Azure Route Server 'ı yapılandırmanıza yardımcı olur. Azure yol sunucusu, NVA 'dan yolları öğrenmekte ve sanal ağdaki sanal makineler için bunları programalacak. Ayrıca, sanal ağ yollarını NVA 'ya duyuracaktır. Daha fazla bilgi için [Azure Route sunucusu](overview.md)' nu okuyun.

> [!IMPORTANT]
> Azure yol sunucusu (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

##  <a name="prerequisites"></a>Önkoşullar 

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* En son Azure CLı sürümüne sahip olduğunuzdan emin olun veya portalda Azure Cloud Shell kullanabilirsiniz. 
* [Azure Route sunucusu için hizmet sınırlarını](route-server-faq.md#limitations)gözden geçirin. 

##  <a name="create-a-route-server"></a>Rota sunucusu oluşturma 

###  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure hesabınızda oturum açın ve aboneliğinizi seçin. 

Yapılandırmanızı başlatmak için Azure hesabınızda oturum açın. "TRY It" Cloud Shell kullanırsanız, otomatik olarak oturum açtınız. Bağlanmanıza yardımcı olması için aşağıdaki örnekleri kullanın:

```azurecli-interactive
az login
```

Hesapla ilişkili abonelikleri kontrol edin.

```azurecli-interactive
az account list
```

ExpressRoute bağlantı hattı oluşturmak istediğiniz aboneliği seçin.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="create-a-resource-group-and-virtual-network"></a>Kaynak grubu ve sanal ağ oluşturma 

Bir Azure rota sunucusu oluşturabilmeniz için önce dağıtımı barındırmak üzere bir sanal ağ gerekir. Bir kaynak grubu ve sanal ağ oluşturmak için izle komutunu kullanın. Zaten bir sanal ağınız varsa, sonraki bölüme atlayabilirsiniz.

```azurecli-interactive
az group create -n "RouteServerRG" -l "westus" 
az network vnet create -g "RouteServerRG" -n "myVirtualNetwork" --address-prefix "10.0.0.0/16" 
``` 

### <a name="add-a-subnet"></a>Alt ağ ekleme 

1. Azure yol sunucusunu dağıtmak için *Routeserversubnet* adlı bir alt ağ ekleyin. Bu alt ağ, yalnızca Azure Route sunucusu için ayrılmış bir alt ağıdır. RouteServerSubnet/27 veya daha kısa bir ön ek olmalıdır (örneğin/26,/25) veya Azure yol sunucusunu eklediğinizde bir hata iletisi alırsınız.

    ```azurecli-interactive 
    az network vnet subnet create -g "RouteServerRG" --vnet-name "myVirtualNetwork" --name "RouteServerSubnet" --address-prefix "10.0.0.0/24"  
    ``` 

1. RouteServerSubnet KIMLIĞINI alın. Sanal ağdaki tüm alt ağların kaynak KIMLIĞINI görüntülemek için şu komutu kullanın: 

    ```azurecli-interactive 
    $subnet_id = $(az network vnet subnet show -n "RouteServerSubnet" --vnet-name "myVirtualNetwork" -g "RouteServerRG" --query id -o tsv) 
    ``` 

RouteServerSubnet KIMLIĞI aşağıdakine benzer: 

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Rota sunucusu oluşturma 

Bu komutla rota sunucusu oluşturun: 

```azurecli-interactive
az network routeserver create -n "myRouteServer" -g "RouteServerRG" --hosted-subnet $subnet_id  
``` 

Konumun, sanal ağınızın konumuyla eşleşmesi gerekir. HostedSubnet, önceki bölümde edindiğiniz RouteServerSubnet KIMLIĞIDIR. 

## <a name="create-peering-with-an-nva"></a>NVA ile eşleme oluşturma 

Yol sunucusundan NVA 'ya eşleme oluşturmak için aşağıdaki komutu kullanın: 

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA1_name" 

``` 

"nva_ip", NVA 'ya atanan sanal ağ IP 'dir. "nva_asn", NVA 'da yapılandırılan otonom sistem sayısıdır (ASN). ASN, 65515-65520 aralığından farklı bir 16 bit sayı olabilir. Bu ASNs aralığı Microsoft tarafından ayrılmıştır. 

Artıklığı için farklı NVA veya aynı NVA örneği ile eşleme ayarlamak için şu komutu kullanın:

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA2_name" 
``` 

## <a name="complete-the-configuration-on-the-nva"></a>NVA üzerinde yapılandırmayı doldurun 

NVA üzerindeki yapılandırmayı tamamlayıp BGP oturumlarını etkinleştirmek için, Azure Route sunucusunun IP ve ASN 'sine ihtiyacınız vardır. Şu komutu kullanarak bu bilgileri alabilirsiniz: 

```azurecli-interactive 
az network routeserver show -g "RouteServerRG" -n "myRouteServer" 
``` 

Çıktıda aşağıdaki bilgiler bulunur. 

```azurecli-interactive 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>Rota değişimini yapılandırma 

Aynı VNet 'te bir ExpressRoute Gateway ve bir Azure VPN ağ geçidiniz varsa ve yollar alışverişi yapmak istiyorsanız, Azure Route sunucusunda Yönlendirme değişimi 'ni etkinleştirebilirsiniz.

> [!IMPORTANT]
> Doğa alanı dağıtımları için Azure Route sunucusu oluşturmadan önce Azure VPN ağ geçidini oluşturmayı unutmayın; Aksi takdirde Azure VPN Gateway dağıtımı başarısız olur.
> 

1. Azure yol sunucusu ve ağ geçidi (ler) arasında yönlendirme değişimini etkinleştirmek için şu komutu kullanın:

```azurecli-interactive 
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic true 

``` 

2. Azure yol sunucusu ve ağ geçidi (lar) arasında yönlendirme değişimini devre dışı bırakmak için şu komutu kullanın:

```azurecli-interactive
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic false 
``` 

## <a name="troubleshooting"></a>Sorun giderme 

Azure Route sunucusu tarafından tanıtılan ve alınan yolları bu komutla görüntüleyebilirsiniz:

```azurecli-interactive 
az network routeserver peering list-advertised-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
az network routeserver peering list-learned-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
``` 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure Route sunucusuna artık ihtiyacınız yoksa, BGP eşlemesini kaldırmak için bu komutları kullanın ve ardından rota sunucusunu kaldırın. 

1. Bu komutla Azure Route sunucusu ve NVA arasındaki BGP eşlemesini kaldırın:

```azurecli-interactive
az network routeserver peering delete --routeserver-name "myRouteServer" -g "RouteServerRG" -n "NVA2_name" 
``` 

2. Azure yol sunucusunu şu komutla kaldırın: 

```azurecli-interactive 
az network routeserver delete -n "myRouteServer" -g "RouteServerRG" 
``` 

## <a name="next-steps"></a>Sonraki adımlar

Azure Route sunucusu oluşturduktan sonra Azure Route sunucusunun ExpressRoute ve VPN ağ geçitleri ile nasıl etkileşime gireceğini öğrenin: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute ve Azure VPN desteği](expressroute-vpn-support.md)
 
