---
title: 'ExpressRoute: yönlendirme filtreleri-Microsoft eşlemesi: Azure PowerShell'
description: Bu makalede PowerShell kullanarak Microsoft Peering için rota filtreleri yapılandırma
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: cade33e77eb0d3ddd818a6ce3dbd7c6cf72811d4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037417"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Microsoft eşlemesi için rota filtreleri yapılandırma: PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Rota filtreleri, desteklenen servislerin bir alt kümesini Microsoft eşlemesi aracılığıyla kullanmanın bir yoludur. Bu makaledeki adımları yapılandırmak ve ExpressRoute devreleri için rota filtreleri yönetmenize yardımcı olur.

Exchange Online, SharePoint Online ve Skype Kurumsal gibi Office 365 hizmetleri ve depolama ve SQL DB gibi Azure genel hizmetlerine Microsoft eşlemesi aracılığıyla erişilebilir. Azure genel Hizmetleri tek başına bölge temelinde seçilebilir ve kamu hizmeti tanımlanamaz.

Microsoft eşlemesi bir ExpressRoute bağlantı hattı üzerinde yapılandırılmış ve bir rota filtresinde bağlı olduğunda, bu hizmetler için seçilen tüm ön eklerin oluşturulmuş BGP oturumları bildirilir. Ön ek aracılığıyla sunulan hizmeti tanımlamak için her ön eke BGP topluluk değeri eklenir. BGP topluluk değerlerini ve eşlemek için Hizmetler listesi için bkz: [BGP toplulukları](expressroute-routing.md#bgp).

Tüm hizmetlerine ihtiyacınız varsa, ön ekleri çok fazla sayıda BGP üzerinden bildirilir. Bu, ağınızdaki yönlendiricileri tarafından korunan rota tabloları boyutunu önemli ölçüde artırır. Yalnızca Microsoft eşlemesi sunulan hizmetlerin bir alt kullanmayı planlıyorsanız, iki yolla yol tablolarınıza boyutunu azaltabilirsiniz. Şunları yapabilirsiniz:

- BGP toplulukları üzerinde rota filtreleri uygulayarak istenmeyen önekleri filtreleyin. Bu standart bir ağ uygulaması ve birçok ağ içinde yaygın olarak kullanılır.

- Rota filtreleri tanımlayın ve bunları ExpressRoute devreniz uygulayın. Bir rota filtresinde Microsoft eşlemesi üzerinden kullanmayı planladığınız hizmet listesini seçmenize izin veren yeni bir kaynaktır. ExpressRoute yönlendiriciler yalnızca rota filtresinde tanımlanan hizmetlerine ait bir ön ekleri listesi gönderir.

### <a name="about"></a>Rota filtreleri hakkında

ExpressRoute bağlantı hattınızda Microsoft eşlemesi yapılandırıldığında, Microsoft ağ uç yönlendiricileri, uç yönlendiricilerle (sizinki veya bağlantı sağlayıcınız) bir çift BGP oturumu oluşturur. Ağınıza rota tanıtılmaz. Ağınızda rota tanıtılmasını sağlamak için bir rota filtresiyle ilişkilendirmeniz gerekir.

Rota filtresi, ExpressRoute bağlantı hattınızın Microsoft eşlemesi üzerinden kullanmak istediğiniz hizmetleri tanımlamanızı sağlar. Bu aslında tüm BGP topluluk değerlerinin izin verilenler listesidir. Rota filtresi kaynağı tanımlandıktan ve bir ExpressRoute bağlantı hattına eklendikten sonra BGP topluluk değerleriyle eşleşen tüm ön ekler ağınızda tanıtılır.

Rota filtreleri bunlar üzerinde Office 365 Hizmetleri ile iliştirme mümkün olması için ExpressRoute aracılığıyla Office 365 hizmetlerini kullanma yetkisi olmalıdır. ExpressRoute aracılığıyla Office 365 hizmetlerini kullanma yetkisi olmayan rota filtreleri ekleme işlemi başarısız olur. Yetkilendirme işlemi hakkında daha fazla bilgi için bkz: [Office 365 için Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> 1 Ağustos 2017'den önce yapılandırılmış olan ExpressRoute devrelerinin Microsoft eşdüzey hizmet sağlama, tüm hizmet ön eklerin rota filtreleri tanımlanmamış olsa bile, Microsoft eşlemesi tanıtılan sahip olur. 1 Ağustos 2017 veya sonrasında yapılandırılmış ExpressRoute devrelerinin Microsoft eşlemesi tüm ön ekleri olmaz bağlantı hattına bir rota filtresinde bağlanana kadar tanıtılan.
> 
> 

### <a name="workflow"></a>İş akışı

Başarılı bir şekilde Microsoft eşlemesi üzerinden hizmetlere bağlanabilmesi için aşağıdaki yapılandırma adımlarını tamamlamanız gerekir:

- Microsoft eşleme sağlanmış olan etkin ExpressRoute bağlantı hattına sahip olmalıdır. Bu görevleri gerçekleştirmek için aşağıdaki yönergeleri kullanabilirsiniz:
  - [ExpressRoute devresi oluşturma](expressroute-howto-circuit-arm.md) ve devam etmeden önce bağlantı sağlayıcınız tarafından etkinleştirilen devreniz olduğunu. ExpressRoute bağlantı hattının sağlanmış ve etkin durumda olması gerekir.
  - [Microsoft eşlemesi oluşturma](expressroute-circuit-peerings.md) BGP oturumu doğrudan yönetiyorsanız. Veya bağlantı sağlayıcınızdan eşleme bağlantı hattınız için Microsoft sağlayın.

-  Oluşturma ve bir rota filtresinde yapılandırmanız gerekir.
    - Hizmetleri tanımlamak, Microsoft eşlemesi üzerinden kullanmak için
    - BGP topluluk değerlerini hizmetlerle ilgili listesini tanımlayın
    - BGP topluluk değerlerini eşleşen ön ek listesini izin verecek bir kural oluşturun

-  ExpressRoute bağlantı hattı için rota filtresi eklemeniz gerekir.

## <a name="before-you-begin"></a>Başlamadan önce

Yapılandırmaya başlamadan önce aşağıdaki ölçütleri karşıladığından emin olun:

 - Gözden geçirme [önkoşulları](expressroute-prerequisites.md) ve [iş akışları](expressroute-workflows.md) yapılandırmaya başlamadan önce.

 - Etkin bir ExpressRoute bağlantı hattınızın olması gerekir. Devam etmeden önce [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-arm.md) yönergelerini izleyin ve bağlantı sağlayıcınızın bağlantı hattını etkinleştirmesini isteyin. ExpressRoute bağlantı hattının sağlanmış ve etkin durumda olması gerekir.

 - Etkin bir Microsoft eşlemesi olması gerekir. Bölümündeki yönergeleri [oluştur ve eşleme yapılandırmasını değiştirme](expressroute-circuit-peerings.md) makalesi.


### <a name="working-with-azure-powershell"></a>Azure PowerShell ile çalışma

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Azure hesabınızda oturum açma

Bu yapılandırmaya başlamadan önce Azure hesabınızda oturum açmalısınız. Bu cmdlet, Azure hesabınıza ilişkin oturum açma kimlik bilgilerinizi girmenizi ister. Oturum açtıktan sonra, Azure PowerShell'de kullanabilmeniz için hesap ayarlarınızı indirir.

PowerShell konsolunuzu yükseltilmiş ayrıcalıklarla açın ve hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın. Azure Cloud Shell kullanıyorsanız, otomatik olarak oturumunuz açılır gibi Bu cmdlet çalıştırmanız gerekmez.

```azurepowershell
Connect-AzAccount
```

Birden çok Azure aboneliğiniz varsa, hesabın aboneliklerini denetleyin.

```azurepowershell-interactive
Get-AzSubscription
```

Kullanmak istediğiniz aboneliği belirtin.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="prefixes"></a>1. adım: bir ön ek listesini ve BGP topluluk değerlerini alma

### <a name="1-get-a-list-of-bgp-community-values"></a>1. BGP topluluk değerlerinin bir listesini alın

Microsoft eşlemesi erişilebilen hizmetler ile ilişkili BGP topluluk değerlerini listesi ve ilişkili önekleri listesini almak için aşağıdaki cmdlet'i kullanın:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. kullanmak istediğiniz değerlerin bir listesini oluşturun

Bir rota filtresinde kullanmak istediğiniz BGP topluluk değerlerini listesini hazırlayın.

## <a name="filter"></a>2. adım: bir yol filtresi ve bir filtre kuralı oluşturma

Bir rota filtresinde yalnızca bir kuralınız olabilir ve kural 'İzin ver' türünde olmalıdır. Bu kural, kendisiyle ilişkili BGP topluluk değerlerini listesi olabilir.

### <a name="1-create-a-route-filter"></a>1. yol filtresi oluşturma

İlk olarak rota filtresini oluşturun. ' New-AzRouteFilter ' komutu yalnızca bir yol filtre kaynağı oluşturuyor. Kaynak oluşturduktan sonra ardından bir kural oluşturmak ve rota filtresi nesnesine ekleme gerekir. Rota filtresi kaynak oluşturmak için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. filtre kuralı oluşturma

Örnekte gösterildiği gibi bir dizi BGP toplulukları virgülle ayrılmış bir liste belirtebilirsiniz. Yeni bir kural oluşturmak için aşağıdaki komutu çalıştırın:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. kuralı rota filtresine ekleyin

Filtre kuralı için rota filtresine eklemek için aşağıdaki komutu çalıştırın:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="attach"></a>3. adım: bir ExpressRoute bağlantı hattı için rota filtresi ekleme

Rota filtresi, yalnızca Microsoft eşlemesi olduğunu varsayarsak ExpressRoute devresine eklemek için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="tasks"></a>Genel görevler

### <a name="getproperties"></a>Bir rota filtresinde özelliklerini almak için

Bir rota filtresinde özelliklerini almak için aşağıdaki adımları kullanın:

1. Rota filtresi kaynak almak için aşağıdaki komutu çalıştırın:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Yol, aşağıdaki komutu çalıştırarak, rota filtresi kaynak filtre kuralları alma:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="updateproperties"></a>Bir rota filtresinde özelliklerini güncelleştirmek için

Rota filtresi zaten bir bağlantı hattına bağlıysa, BGP topluluk listesine güncelleştirmeleri otomatik olarak belirlenen BGP oturumları aracılığıyla uygun önekle tanıtım değişiklikleri yaymak. Aşağıdaki komutu kullanarak, rota filtresi BGP topluluk listesini güncelleştirebilirsiniz:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="detach"></a>Bir ExpressRoute bağlantı hattı yol filtresinden ayırmak için

Bir rota filtresinde ExpressRoute bağlantı ayrılmış sonra hiçbir ön ekleri BGP oturumu aracılığıyla bildirilir. Bunu yapmak için aşağıdaki komutu kullanarak ExpressRoute devresi yol filtresinden ayırabilirsiniz:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="delete"></a>Rota filtresi silinemedi

Tüm işlem hattına bağlı olmayan bir rota filtresinde yalnızca silebilirsiniz. Rota filtresi için herhangi bir bağlantı hattı silmeye çalışmadan önce bağlı olmadığından emin olun. Aşağıdaki komutu kullanarak bir rota filtresinde silebilirsiniz:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Sonraki Adımlar

ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).
