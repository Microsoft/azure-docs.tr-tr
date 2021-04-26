---
title: 'Öğretici: Microsoft eşlemesi için yol filtrelerini Yapılandırma-Azure PowerShell'
description: Bu öğreticide, PowerShell kullanarak Microsoft eşlemesi için yol filtrelerinin nasıl yapılandırılacağı açıklanmaktadır.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 90d4def5a1c08e305b9315f299e83e2187b6be2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91969951"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-powershell"></a>Öğretici: PowerShell kullanarak Microsoft eşlemesi için yol filtrelerini yapılandırma

> [!div class="op_single_selector"]
> * [Azure Portalı](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Rota filtreleri, desteklenen servislerin bir alt kümesini Microsoft eşlemesi aracılığıyla kullanmanın bir yoludur. Bu makaledeki adımlar, ExpressRoute devreleri için yol filtrelerini yapılandırmanıza ve yönetmenize yardımcı olur.

Exchange Online, SharePoint Online ve Skype Kurumsal gibi Microsoft 365 hizmetlere ve depolama ve SQL DB gibi Azure ortak hizmetlerine Microsoft eşlemesi aracılığıyla erişilebilir. Azure genel hizmetleri bölge başına seçilebilir ve genel hizmet başına tanımlanamaz.

ExpressRoute bağlantı hattındaki Microsoft eşlemesi yapılandırıldığında, bu hizmetlerle ilgili tüm önekler, oluşturulan BGP oturumları aracılığıyla tanıtıldığında. Ön ek aracılığıyla sunulan hizmeti tanımlamak için her ön eke BGP topluluk değeri eklenir. BGP topluluk değerlerinin ve eşlendikleri hizmetlerin listesi için bkz. [BGP toplulukları](expressroute-routing.md#bgp).

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

- Yapılandırmaya başlamadan önce [önkoşulları](expressroute-prerequisites.md) ve [iş akışlarını](expressroute-workflows.md) gözden geçirin.

- Microsoft eşlemesi sağlanmış etkin bir ExpressRoute devresine sahip olmanız gerekir. Aşağıdaki yönergeleri kullanarak şu görevleri gerçekleştirebilirsiniz:
  - Devam etmeden önce [bir ExpressRoute devresi oluşturun](expressroute-howto-circuit-arm.md) ve bağlantı sağlayıcınız tarafından devre dışı bırakıldı. ExpressRoute bağlantı hattı sağlanmış ve etkinleştirilmiş durumda olmalıdır.
  - BGP oturumunu doğrudan yönetiyorsanız, [Microsoft eşlemesi oluşturun](expressroute-circuit-peerings.md) . Ya da bağlantı sağlayıcınızın, devreniz için Microsoft eşlemesi sağlamasını sağlayın.
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure hesabınızda oturum açın ve aboneliğinizi seçin

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a> Ön eklerin ve BGP topluluk değerlerinin bir listesini alın

1. Microsoft eşlemesi aracılığıyla erişilebilen hizmet ile ilişkili BGP topluluk değerlerinin ve öneklerinin listesini almak için aşağıdaki cmdlet 'i kullanın:

    ```azurepowershell-interactive
    Get-AzBgpServiceCommunity
    ```

1. Yol filtresinde kullanmak istediğiniz BGP topluluk değerlerinin bir listesini oluşturun.

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Yol filtresi ve filtre kuralı oluşturma

Yol filtresi yalnızca bir kurala sahip olabilir ve kuralın ' Allow ' türünde olması gerekir. Bu kural, kendisiyle ilişkili BGP topluluk değerlerinin bir listesine sahip olabilir. Komut `az network route-filter create` yalnızca bir yol filtre kaynağı oluşturur. Kaynağı oluşturduktan sonra bir kural oluşturmanız ve bunu yol filtresi nesnesine bağlamanız gerekir.

1. Bir yol filtre kaynağı oluşturmak için aşağıdaki komutu çalıştırın:

    ```azurepowershell-interactive
    New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
    ```

1. Bir yol filtre kuralı oluşturmak için aşağıdaki komutu çalıştırın:
 
    ```azurepowershell-interactive
    $rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
    ```

1. Filtre kuralını rota filtresine eklemek için aşağıdaki komutu çalıştırın:
 
    ```azurepowershell-interactive
    $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
    $routefilter.Rules.Add($rule)
    Set-AzRouteFilter -RouteFilter $routefilter
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Yol filtresini bir ExpressRoute devresine iliştirme

Yönlendirme filtresini ExpressRoute bağlantı hattına eklemek için aşağıdaki komutu çalıştırın ve yalnızca Microsoft 'un eşlemesini kullanabilirsiniz:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Genel görevler

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Bir yol filtresinin özelliklerini almak için

Bir yol filtresinin özelliklerini almak için aşağıdaki adımları kullanın:

1. Yol filtresi kaynağını almak için aşağıdaki komutu çalıştırın:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Aşağıdaki komutu çalıştırarak yol filtresi kaynağı için yol filtresi kurallarını alın:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Bir yol filtresinin özelliklerini güncelleştirmek için

Yol filtresi zaten bir bağlantı hattına eklenmişse, BGP topluluk listesi güncelleştirmeleri otomatik olarak, belirlenen BGP oturumunda önek tanıtım değişikliklerini yayar. Aşağıdaki komutu kullanarak yol filtrenizin BGP topluluk listesini güncelleştirebilirsiniz:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Bir ExpressRoute bağlantı hattından yol filtresini ayırmak için

Yol filtresi ExpressRoute bağlantı hattı 'ndan ayrıldıktan sonra BGP oturumu aracılığıyla hiçbir önek tanıtılmaz. Aşağıdaki komutu kullanarak bir ExpressRoute bağlantı hattından bir yol filtresini ayırabilirsiniz:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir yol filtresini yalnızca herhangi bir devreye iliştirilmemişse silebilirsiniz. Yol filtresinin silmeyi denemeden önce herhangi bir devreye bağlı olmadığından emin olun. Aşağıdaki komutu kullanarak bir yol filtresini silebilirsiniz:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Sonraki Adımlar

Yönlendirici yapılandırma örnekleri hakkında daha fazla bilgi için bkz.:

> [!div class="nextstepaction"]
> [Yönlendirmeyi ayarlamak ve yönetmek için yönlendirici yapılandırma örnekleri](expressroute-config-samples-routing.md)
