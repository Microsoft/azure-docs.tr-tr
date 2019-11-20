---
title: 'ExpressRoute: yönlendirme filtreleri-Microsoft eşlemesi: Azure CLı'
description: Bu makalede Azure CLI kullanarak Microsoft Peering için rota filtreleri yapılandırma
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: c3c50a005e119890fb17fcf7b3114a747bbe34bf
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033408"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Microsoft eşlemesi için rota filtreleri yapılandırma: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Rota filtreleri, desteklenen servislerin bir alt kümesini Microsoft eşlemesi aracılığıyla kullanmanın bir yoludur. Bu makaledeki adımları yapılandırmak ve ExpressRoute devreleri için rota filtreleri yönetmenize yardımcı olur.

Exchange Online, SharePoint Online ve Skype Kurumsal gibi Office 365 hizmetlerine Microsoft eşlemesi aracılığıyla erişilebilir. Microsoft eşlemesi ExpressRoute devresi yapılandırıldığında, bu hizmetleri ile ilgili tüm ön eklerin oluşturulmuş BGP oturumları bildirilir. Ön ek aracılığıyla sunulan hizmeti tanımlamak için her ön eke BGP topluluk değeri eklenir. BGP topluluk değerlerini ve eşlemek için Hizmetler listesi için bkz: [BGP toplulukları](expressroute-routing.md#bgp).

Tüm hizmetlerine ihtiyacınız varsa, ön ekleri çok fazla sayıda BGP üzerinden bildirilir. Bu, ağınızdaki yönlendiricileri tarafından korunan rota tabloları boyutunu önemli ölçüde artırır. Yalnızca Microsoft eşlemesi sunulan hizmetlerin bir alt kullanmayı planlıyorsanız, iki yolla yol tablolarınıza boyutunu azaltabilirsiniz. Şunları yapabilirsiniz:

* BGP toplulukları üzerinde rota filtreleri uygulayarak istenmeyen önekleri filtreleyin. Bu standart bir ağ uygulaması ve birçok ağ içinde yaygın olarak kullanılır.

* Rota filtreleri tanımlayın ve bunları ExpressRoute devreniz uygulayın. Bir rota filtresinde Microsoft eşlemesi üzerinden kullanmayı planladığınız hizmet listesini seçmenize izin veren yeni bir kaynaktır. ExpressRoute yönlendiriciler yalnızca rota filtresinde tanımlanan hizmetlerine ait bir ön ekleri listesi gönderir.

### <a name="about"></a>Rota filtreleri hakkında

Microsoft eşlemesi ExpressRoute devreniz yapılandırıldığında, Microsoft uç yönlendiricileri kenar yönlendiricilerine (sizin ya da bağlantı sağlayıcınızın) BGP oturumları çifti oluşturun. Ağınıza rota tanıtılmaz. Ağınızda rota tanıtılmasını sağlamak için bir rota filtresiyle ilişkilendirmeniz gerekir.

Rota filtresi, ExpressRoute bağlantı hattınızın Microsoft eşlemesi üzerinden kullanmak istediğiniz hizmetleri tanımlamanızı sağlar. Bu genelde tüm BGP topluluk değerlerinden oluşan bir izin verilenler listesidir. Rota filtresi kaynağı tanımlandıktan ve bir ExpressRoute bağlantı hattına eklendikten sonra BGP topluluk değerleriyle eşleşen tüm ön ekler ağınızda tanıtılır.

Rota filtreleri bunlar üzerinde Office 365 Hizmetleri ile iliştirme mümkün olması için ExpressRoute aracılığıyla Office 365 hizmetlerini kullanma yetkisi olmalıdır. ExpressRoute aracılığıyla Office 365 hizmetlerini kullanma yetkisi olmayan rota filtreleri ekleme işlemi başarısız olur. Yetkilendirme işlemi hakkında daha fazla bilgi için bkz: [Office 365 için Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> 1 Ağustos 2017'den önce yapılandırılmış olan ExpressRoute devrelerinin Microsoft eşdüzey hizmet sağlama, tüm hizmet ön eklerin rota filtreleri tanımlanmamış olsa bile, Microsoft eşlemesi tanıtılan sahip olur. 1 Ağustos 2017 veya sonrasında yapılandırılmış ExpressRoute devrelerinin Microsoft eşlemesi tüm ön ekleri olmaz bağlantı hattına bir rota filtresinde bağlanana kadar tanıtılan.
> 
> 

### <a name="workflow"></a>İş akışı

Başarılı bir şekilde Microsoft eşlemesi üzerinden hizmetlere bağlanabilmesi için aşağıdaki yapılandırma adımlarını tamamlamanız gerekir:

* Microsoft eşleme sağlanmış olan etkin ExpressRoute bağlantı hattına sahip olmalıdır. Bu görevleri gerçekleştirmek için aşağıdaki yönergeleri kullanabilirsiniz:
  * [ExpressRoute devresi oluşturma](howto-circuit-cli.md) ve devam etmeden önce bağlantı sağlayıcınız tarafından etkinleştirilen devreniz olduğunu. ExpressRoute bağlantı hattının sağlanmış ve etkin durumda olması gerekir.
  * [Microsoft eşlemesi oluşturma](howto-routing-cli.md) BGP oturumu doğrudan yönetiyorsanız. Veya bağlantı sağlayıcınızdan eşleme bağlantı hattınız için Microsoft sağlayın.

* Oluşturma ve bir rota filtresinde yapılandırmanız gerekir.
  * Hizmetleri tanımlamak, Microsoft eşlemesi üzerinden kullanmak için
  * BGP topluluk değerlerini hizmetlerle ilgili listesini tanımlayın
  * BGP topluluk değerlerini eşleşen ön ek listesini izin verecek bir kural oluşturun

* ExpressRoute bağlantı hattı için rota filtresi eklemeniz gerekir.

## <a name="before-you-begin"></a>Başlamadan önce

Başlamadan önce, CLI komutlarının en son sürümünü (2.0 veya üzeri) yükleyin. CLI komutlarını yükleme hakkında bilgi için bkz. [Azure CLI’yi yükleme](/cli/azure/install-azure-cli) ve [Azure CLI’yi Kullanmaya Başlama](/cli/azure/get-started-with-azure-cli).

* Gözden geçirme [önkoşulları](expressroute-prerequisites.md) ve [iş akışları](expressroute-workflows.md) yapılandırmaya başlamadan önce.

* Etkin bir ExpressRoute bağlantı hattınızın olması gerekir. Devam etmeden önce [ExpressRoute bağlantı hattı oluşturma](howto-circuit-cli.md) yönergelerini izleyin ve bağlantı sağlayıcınızın bağlantı hattını etkinleştirmesini isteyin. ExpressRoute bağlantı hattının sağlanmış ve etkin durumda olması gerekir.

* Etkin bir Microsoft eşlemesi olması gerekir. Konumundaki yönergeleri [oluştur ve eşleme yapılandırmasını değiştirme](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure hesabınızda oturum açın ve aboneliğinizi seçin

Yapılandırmanızı başlamak için Azure hesabınızda oturum açın. "Try IT" kullanıyorsanız, otomatik olarak oturum açtınız ve oturum açma adımı atlayabilirsiniz. Bağlanmanıza yardımcı olması için aşağıdaki örnekleri kullanın:

```azurecli
az login
```

Hesapla ilişkili abonelikleri kontrol edin.

```azurecli-interactive
az account list
```

Bir ExpressRoute bağlantı hattı oluşturmak istediğiniz aboneliği seçin.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="prefixes"></a>1. adım: bir ön ek listesini ve BGP topluluk değerlerini alma

### <a name="1-get-a-list-of-bgp-community-values"></a>1. BGP topluluk değerlerinin bir listesini alın

Microsoft eşlemesi erişilebilen hizmetler ile ilişkili BGP topluluk değerlerini listesi ve ilişkili önekleri listesini almak için aşağıdaki cmdlet'i kullanın:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. kullanmak istediğiniz değerlerin bir listesini oluşturun

Bir rota filtresinde kullanmak istediğiniz BGP topluluk değerlerini listesini hazırlayın.

## <a name="filter"></a>2. adım: bir yol filtresi ve bir filtre kuralı oluşturma

Bir rota filtresinde yalnızca bir kuralınız olabilir ve kural 'İzin ver' türünde olmalıdır. Bu kural, kendisiyle ilişkili BGP topluluk değerlerini listesi olabilir.

### <a name="1-create-a-route-filter"></a>1. yol filtresi oluşturma

İlk olarak rota filtresini oluşturun. Komut `az network route-filter create` yalnızca bir rota filtresi kaynağı oluşturur. Kaynak oluşturduktan sonra ardından bir kural oluşturmak ve rota filtresi nesnesine ekleme gerekir. Rota filtresi kaynak oluşturmak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. filtre kuralı oluşturma

Yeni bir kural oluşturmak için aşağıdaki komutu çalıştırın:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>3. adım: bir ExpressRoute bağlantı hattı için rota filtresi ekleme

ExpressRoute bağlantı hattı için rota filtresine eklemek için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>Genel görevler

### <a name="getproperties"></a>Bir rota filtresinde özelliklerini almak için

Bir rota filtresinde özelliklerini almak için aşağıdaki komutu kullanın:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>Bir rota filtresinde özelliklerini güncelleştirmek için

Rota filtresi zaten bir bağlantı hattına bağlıysa, BGP topluluk listesine güncelleştirmeleri otomatik olarak belirlenen BGP oturumları aracılığıyla uygun önekle tanıtım değişiklikleri yaymak. Aşağıdaki komutu kullanarak, rota filtresi BGP topluluk listesini güncelleştirebilirsiniz:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>Bir ExpressRoute bağlantı hattı yol filtresinden ayırmak için

Bir rota filtresinde ExpressRoute bağlantı ayrılmış sonra hiçbir ön ekleri BGP oturumu aracılığıyla bildirilir. Bunu yapmak için aşağıdaki komutu kullanarak ExpressRoute devresi yol filtresinden ayırabilirsiniz:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>Rota filtresi silinemedi

Tüm işlem hattına bağlı olmayan bir rota filtresinde yalnızca silebilirsiniz. Rota filtresi için herhangi bir bağlantı hattı silmeye çalışmadan önce bağlı olmadığından emin olun. Aşağıdaki komutu kullanarak bir rota filtresinde silebilirsiniz:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Sonraki Adımlar

ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).
