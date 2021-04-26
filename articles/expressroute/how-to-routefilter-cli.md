---
title: 'Öğretici: Microsoft eşlemesi için rota filtrelerini Yapılandırma-Azure CLı'
description: Bu öğreticide, Azure CLı kullanılarak Microsoft eşlemesi için yol filtrelerinin nasıl yapılandırılacağı açıklanmaktadır.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: ac7fc5af21f11699331d41a074e88ae757170664
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91976003"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-azure-cli"></a>Öğretici: Microsoft eşlemesi için rota filtrelerini yapılandırma: Azure CLı

> [!div class="op_single_selector"]
> * [Azure Portalı](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Rota filtreleri, desteklenen servislerin bir alt kümesini Microsoft eşlemesi aracılığıyla kullanmanın bir yoludur. Bu makaledeki adımlar, ExpressRoute devreleri için yol filtrelerini yapılandırmanıza ve yönetmenize yardımcı olur.

Exchange Online, SharePoint Online ve Skype Kurumsal gibi Microsoft 365 hizmetlere Microsoft eşlemesi aracılığıyla erişilebilir. ExpressRoute bağlantı hattındaki Microsoft eşlemesi yapılandırıldığında, bu hizmetlerle ilgili tüm önekler, oluşturulan BGP oturumları aracılığıyla tanıtıldığında. Ön ek aracılığıyla sunulan hizmeti tanımlamak için her ön eke BGP topluluk değeri eklenir. BGP topluluk değerlerinin ve eşlendikleri hizmetlerin listesi için bkz. [BGP toplulukları](expressroute-routing.md#bgp).

Tüm Azure ve Microsoft 365 hizmetlerine yönelik bağlantı, BGP aracılığıyla çok sayıda önek tanıtılmasına neden olur. Büyük sayıda önek, ağınızdaki yönlendiriciler tarafından tutulan yol tablolarının boyutunu önemli ölçüde artırır. Yalnızca Microsoft eşlemesi üzerinden sunulan hizmetlerin bir alt kümesini kullanmak istiyorsanız, yol tablolarınızın boyutunu iki şekilde azaltabilirsiniz. Seçenekleriniz şunlardır:

* BGP topluluklarına rota filtreleri uygulayarak istenmeyen önekleri filtreleyin. Yol filtreleme standart bir ağ uygulamasıdır ve çoğu ağ içinde yaygın olarak kullanılır.

* Rota filtrelerini tanımlayın ve bunları ExpressRoute devrenizi ile uygulayın. Yol filtresi, Microsoft eşlemesi aracılığıyla tüketmek üzere planladığınız hizmetlerin listesini seçmenize olanak sağlayan yeni bir kaynaktır. ExpressRoute yönlendiricileri yalnızca yol filtresinde tanımlanan hizmetlere ait olan ön eklerin listesini gönderir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - BGP topluluk değerlerini al.
> - Rota filtresi ve filtre kuralı oluşturma.
> - Rota filtresini bir ExpressRoute devresine ilişkilendirin.

### <a name="about-route-filters"></a><a name="about"></a>Yol filtreleri hakkında

ExpressRoute devreniz için Microsoft eşlemesi yapılandırıldığında, Microsoft Edge yönlendiricileri, bağlantı sağlayıcınız aracılığıyla Edge yönlendiricileriniz ile bir dizi BGP oturumu oluşturur. Ağınıza rota tanıtılmaz. Ağınızda rota tanıtılmasını sağlamak için bir rota filtresiyle ilişkilendirmeniz gerekir.

Rota filtresi, ExpressRoute bağlantı hattınızın Microsoft eşlemesi üzerinden kullanmak istediğiniz hizmetleri tanımlamanızı sağlar. Bu aslında tüm BGP topluluk değerlerinin izin verilen bir listesidir. Bir yol filtresi kaynağı tanımlandıktan ve bir ExpressRoute devresine eklendikten sonra, BGP topluluk değerleriyle eşlenen tüm önekler ağınıza tanıtılabilir.

Yönlendirme filtrelerini Microsoft 365 hizmetleriyle birlikte eklemek için, ExpressRoute aracılığıyla Microsoft 365 hizmetleri kullanma yetkisiyle sahip olmanız gerekir. ExpressRoute aracılığıyla Microsoft 365 hizmetlerini kullanma yetkiniz yoksa, yol filtrelerini iliştirme işlemi başarısız olur. Yetkilendirme işlemi hakkında daha fazla bilgi için bkz. [Azure ExpressRoute for Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> 1 Ağustos 2017 ' den önce yapılandırılmış ExpressRoute bağlantı hattı Microsoft eşlemesi, yol filtreleri tanımlanmasa bile Microsoft eşlemesi aracılığıyla tanıtılan tüm hizmet öneklerini alacak. 1 Ağustos 2017 ' de veya sonrasında yapılandırılan ExpressRoute devrelerinin Microsoft eşlemesi, bir yol filtresi devresine iliştirilene kadar tanıtılan öneklere sahip olmayacaktır.
> 

## <a name="prerequisites"></a>Önkoşullar

Microsoft eşlemesi aracılığıyla hizmetlere başarıyla bağlanmak için aşağıdaki yapılandırma adımlarını gerçekleştirmeniz gerekir:

* Microsoft eşlemesi sağlanmış etkin bir ExpressRoute devresine sahip olmanız gerekir. Aşağıdaki yönergeleri kullanarak şu görevleri gerçekleştirebilirsiniz:
  * Devam etmeden önce [bir ExpressRoute devresi oluşturun](howto-circuit-cli.md) ve bağlantı sağlayıcınız tarafından devre dışı bırakıldı. ExpressRoute bağlantı hattı sağlanmış ve etkinleştirilmiş durumda olmalıdır.
  * BGP oturumunu doğrudan yönetiyorsanız, [Microsoft eşlemesi oluşturun](howto-routing-cli.md) . Ya da bağlantı sağlayıcınızın, devreniz için Microsoft eşlemesi sağlamasını sağlayın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu hızlı başlangıç, Azure CLı sürüm 2.0.28 veya üstünü gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure hesabınızda oturum açın ve aboneliğinizi seçin

Yapılandırmanızı başlatmak için Azure hesabınızda oturum açın. "Deneyin" i kullanıyorsanız, otomatik olarak oturum açtınız ve oturum açma adımını atlayabilirsiniz. Bağlanmanıza yardımcı olması için aşağıdaki örnekleri kullanın:

```azurecli
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

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Ön eklerin ve BGP topluluk değerlerinin bir listesini alın

1. Microsoft eşlemesi aracılığıyla erişilebilen hizmet ile ilişkili BGP topluluk değerlerinin ve öneklerinin listesini almak için aşağıdaki cmdlet 'i kullanın:

    ```azurecli-interactive
    az network route-filter rule list-service-communities
    ```

1. Yol filtresinde kullanmak istediğiniz BGP topluluk değerlerinin bir listesini oluşturun.

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Yol filtresi ve filtre kuralı oluşturma

Yol filtresi yalnızca bir kurala sahip olabilir ve kuralın ' Allow ' türünde olması gerekir. Bu kural, kendisiyle ilişkili BGP topluluk değerlerinin bir listesine sahip olabilir. Komut `az network route-filter create` yalnızca bir yol filtre kaynağı oluşturur. Kaynağı oluşturduktan sonra bir kural oluşturmanız ve bunu yol filtresi nesnesine bağlamanız gerekir.

1. Bir yol filtre kaynağı oluşturmak için aşağıdaki komutu çalıştırın:

    ```azurecli-interactive
    az network route-filter create -n MyRouteFilter -g MyResourceGroup
    ```

1. Bir yol filtre kuralı oluşturmak için aşağıdaki komutu çalıştırın:
 
    ```azurecli-interactive
    az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Yol filtresini bir ExpressRoute devresine iliştirme

Yol filtresini ExpressRoute devresine eklemek için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Genel görevler

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Bir yol filtresinin özelliklerini almak için

Bir yol filtresinin özelliklerini almak için aşağıdaki komutu kullanın:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Bir yol filtresinin özelliklerini güncelleştirmek için

Yol filtresi zaten bir bağlantı hattına eklenmişse, BGP topluluk listesi güncelleştirmeleri otomatik olarak, belirlenen BGP oturumunda önek tanıtım değişikliklerini yayar. Aşağıdaki komutu kullanarak yol filtrenizin BGP topluluk listesini güncelleştirebilirsiniz:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Bir ExpressRoute bağlantı hattından yol filtresini ayırmak için

Yol filtresi ExpressRoute bağlantı hattı 'ndan ayrıldıktan sonra BGP oturumu aracılığıyla hiçbir önek tanıtılmaz. Aşağıdaki komutu kullanarak bir ExpressRoute bağlantı hattından bir yol filtresini ayırabilirsiniz:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir yol filtresini yalnızca herhangi bir devreye iliştirilmemişse silebilirsiniz. Yol filtresinin silmeyi denemeden önce herhangi bir devreye bağlı olmadığından emin olun. Aşağıdaki komutu kullanarak bir yol filtresini silebilirsiniz:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Sonraki Adımlar

Yönlendirici yapılandırma örnekleri hakkında daha fazla bilgi için bkz.:

> [!div class="nextstepaction"]
> [Yönlendirmeyi ayarlamak ve yönetmek için yönlendirici yapılandırma örnekleri](expressroute-config-samples-routing.md)
