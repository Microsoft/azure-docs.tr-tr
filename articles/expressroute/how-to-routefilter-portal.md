---
title: 'ExpressRoute: yönlendirme filtreleri-Microsoft eşlemesi: Azure portal'
description: Bu makalede, Azure portalını kullanarak Microsoft eşlemesi için rota filtreleri yapılandırma açıklanır.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 07/01/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 0b8e06ad5688374e5ab4aaa72d8485e6da797afe
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037450"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Microsoft eşlemesi için rota filtreleri yapılandırma: Azure portalı
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Rota filtreleri, desteklenen servislerin bir alt kümesini Microsoft eşlemesi aracılığıyla kullanmanın bir yoludur. Bu makaledeki adımları yapılandırmak ve ExpressRoute devreleri için rota filtreleri yönetmenize yardımcı olur.

Exchange Online, SharePoint Online ve Skype Kurumsal gibi Office 365 hizmetlerine ve depolama ve SQL DB gibi Azure hizmetlerine Microsoft eşlemesi aracılığıyla erişilebilir. Microsoft eşlemesi ExpressRoute devresi yapılandırıldığında, bu hizmetleri ile ilgili tüm ön eklerin oluşturulmuş BGP oturumları bildirilir. Ön ek aracılığıyla sunulan hizmeti tanımlamak için her ön eke BGP topluluk değeri eklenir. BGP topluluk değerlerini ve eşlemek için Hizmetler listesi için bkz: [BGP toplulukları](expressroute-routing.md#bgp).

Tüm hizmetlerine ihtiyacınız varsa, ön ekleri çok fazla sayıda BGP üzerinden bildirilir. Bu, ağınızdaki yönlendiricileri tarafından korunan rota tabloları boyutunu önemli ölçüde artırır. Yalnızca Microsoft eşlemesi sunulan hizmetlerin bir alt kullanmayı planlıyorsanız, iki yolla yol tablolarınıza boyutunu azaltabilirsiniz. Şunları yapabilirsiniz:

- BGP toplulukları üzerinde rota filtreleri uygulayarak istenmeyen önekleri filtreleyin. Bu standart bir ağ uygulaması ve birçok ağ içinde yaygın olarak kullanılır.

- Rota filtreleri tanımlayın ve bunları ExpressRoute devreniz uygulayın. Bir rota filtresinde Microsoft eşlemesi üzerinden kullanmayı planladığınız hizmet listesini seçmenize izin veren yeni bir kaynaktır. ExpressRoute yönlendiriciler yalnızca rota filtresinde tanımlanan hizmetlerine ait bir ön ekleri listesi gönderir.

### <a name="about"></a>Rota filtreleri hakkında

Microsoft eşlemesi ExpressRoute devreniz yapılandırıldığında, Microsoft uç yönlendiricileri kenar yönlendiricilerine (sizin ya da bağlantı sağlayıcınızın) BGP oturumları çifti oluşturun. Ağınıza rota tanıtılmaz. Ağınızda rota tanıtılmasını sağlamak için bir rota filtresiyle ilişkilendirmeniz gerekir.

Rota filtresi, ExpressRoute bağlantı hattınızın Microsoft eşlemesi üzerinden kullanmak istediğiniz hizmetleri tanımlamanızı sağlar. Bu aslında, izin vermek istediğiniz tüm BGP topluluk değerlerinin bir listesidir. Rota filtresi kaynağı tanımlandıktan ve bir ExpressRoute bağlantı hattına eklendikten sonra BGP topluluk değerleriyle eşleşen tüm ön ekler ağınızda tanıtılır.

Rota filtreleri bunlar üzerinde Office 365 Hizmetleri ile iliştirme mümkün olması için ExpressRoute aracılığıyla Office 365 hizmetlerini kullanma yetkisi olmalıdır. ExpressRoute aracılığıyla Office 365 hizmetlerini kullanma yetkisi olmayan rota filtreleri ekleme işlemi başarısız olur. Yetkilendirme işlemi hakkında daha fazla bilgi için bkz: [Office 365 için Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> 1 Ağustos 2017'den önce yapılandırılmış olan ExpressRoute devrelerinin Microsoft eşdüzey hizmet sağlama, tüm hizmet ön eklerin rota filtreleri tanımlanmamış olsa bile, Microsoft eşlemesi tanıtılan sahip olur. 1 Ağustos 2017 veya sonrasında yapılandırılmış ExpressRoute devrelerinin Microsoft eşlemesi tüm ön ekleri olmaz bağlantı hattına bir rota filtresinde bağlanana kadar tanıtılan.
> 
> 

### <a name="workflow"></a>İş akışı

Başarılı bir şekilde Microsoft eşlemesi üzerinden hizmetlere bağlanabilmesi için aşağıdaki yapılandırma adımlarını tamamlamanız gerekir:

- Microsoft eşleme sağlanmış olan etkin ExpressRoute bağlantı hattına sahip olmalıdır. Bu görevleri gerçekleştirmek için aşağıdaki yönergeleri kullanabilirsiniz:
  - [ExpressRoute devresi oluşturma](expressroute-howto-circuit-portal-resource-manager.md) ve devam etmeden önce bağlantı sağlayıcınız tarafından etkinleştirilen devreniz olduğunu. ExpressRoute bağlantı hattının sağlanmış ve etkin durumda olması gerekir.
  - [Microsoft eşlemesi oluşturma](expressroute-howto-routing-portal-resource-manager.md) BGP oturumu doğrudan yönetiyorsanız. Veya bağlantı sağlayıcınızdan eşleme bağlantı hattınız için Microsoft sağlayın.

-  Oluşturma ve bir rota filtresinde yapılandırmanız gerekir.
    - Hizmetleri tanımlamak, Microsoft eşlemesi üzerinden kullanmak için
    - BGP topluluk değerlerini hizmetlerle ilgili listesini tanımlayın
    - BGP topluluk değerlerini eşleşen ön ek listesini izin verecek bir kural oluşturun

-  ExpressRoute bağlantı hattı için rota filtresi eklemeniz gerekir.

## <a name="before-you-begin"></a>Başlamadan önce

Yapılandırmaya başlamadan önce aşağıdaki ölçütleri karşıladığından emin olun:

 - Gözden geçirme [önkoşulları](expressroute-prerequisites.md) ve [iş akışları](expressroute-workflows.md) yapılandırmaya başlamadan önce.

 - Etkin bir ExpressRoute bağlantı hattınızın olması gerekir. Devam etmeden önce [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-portal-resource-manager.md) yönergelerini izleyin ve bağlantı sağlayıcınızın bağlantı hattını etkinleştirmesini isteyin. ExpressRoute bağlantı hattının sağlanmış ve etkin durumda olması gerekir.

 - Etkin bir Microsoft eşlemesi olması gerekir. Konumundaki yönergeleri [oluştur ve eşleme yapılandırmasını değiştirme](expressroute-howto-routing-portal-resource-manager.md)


## <a name="prefixes"></a>1. adım: bir ön ek listesini ve BGP topluluk değerlerini alma

### <a name="1-get-a-list-of-bgp-community-values"></a>1. BGP topluluk değerlerinin bir listesini alın

Microsoft eşlemesi aracılığıyla erişilebilen hizmetler ile ilişkili BGP topluluk değerlerini kullanılabilir [ExpressRoute yönlendirme gereksinimleri](expressroute-routing.md) sayfası.

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. kullanmak istediğiniz değerlerin bir listesini oluşturun

Yol filtresinde kullanmak istediğiniz [BGP topluluk değerlerinin](expressroute-routing.md#bgp) bir listesini oluşturun. 

## <a name="filter"></a>2. adım: bir yol filtresi ve bir filtre kuralı oluşturma

Bir rota filtresinde yalnızca bir kuralınız olabilir ve kural 'İzin ver' türünde olmalıdır. Bu kural, kendisiyle ilişkili BGP topluluk değerlerini listesi olabilir.

### <a name="1-create-a-route-filter"></a>1. yol filtresi oluşturma
Yeni bir kaynak oluşturma seçeneğini belirleyerek bir rota filtresi oluşturabilirsiniz. Tıklayın **kaynak Oluştur** > **ağ** > **RouteFilter**, aşağıdaki görüntüde gösterildiği gibi:

![Rota filtresi oluşturma](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Rota filtresi bir kaynak grubunda yerleştirmeniz gerekir. 

![Rota filtresi oluşturma](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. filtre kuralı oluşturma

Ekleyebilir ve kuralları, rota filtresi Yönet kural sekmesini seçerek güncelleştirin.

![Rota filtresi oluşturma](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Açılan listeden bağlanmak istediğiniz hizmetleri seçebilir ve tamamlandığında kuralı kaydedebilirsiniz.

![Rota filtresi oluşturma](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="attach"></a>3. adım: bir ExpressRoute bağlantı hattı için rota filtresi ekleme

"Devre Ekle" düğmesini seçerek ve açılan listeden ExpressRoute bağlantı hattını seçerek yol filtresini bir devreye ekleyebilirsiniz.

![Rota filtresi oluşturma](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Bağlantı sağlayıcısı, "Bağlantı hattı Ekle" düğmesi seçmeden önce ExpressRoute bağlantı hattı yenileme için ExpressRoute bağlantı hattı dikey penceresinden devre eşlemesi yapılandırırsa.

![Rota filtresi oluşturma](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>Genel görevler

### <a name="getproperties"></a>Bir rota filtresinde özelliklerini almak için

Portalda kaynak açtığınızda, bir rota filtresinde özelliklerini görüntüleyebilirsiniz.

![Rota filtresi oluşturma](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="updateproperties"></a>Bir rota filtresinde özelliklerini güncelleştirmek için

BGP topluluk değerini "Manage kuralı" düğmesini seçerek bir bağlantı hattına bağlı listesini güncelleştirebilirsiniz.


![Rota filtresi oluşturma](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Rota filtresi oluşturma](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="detach"></a>Bir ExpressRoute bağlantı hattı yol filtresinden ayırmak için

Bir devreyi yol filtresinden ayırmak için, devreye sağ tıklayın ve "ilişkiyi kaldır" a tıklayın.

![Rota filtresi oluşturma](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="delete"></a>Rota filtresi silinemedi

Bir rota filtresinde Sil düğmesini seçerek silebilirsiniz. 

![Rota filtresi oluşturma](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Sonraki Adımlar

* ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).

* Yönlendirici yapılandırma örnekleri hakkında daha fazla bilgi için bkz. [yönlendirme ayarlamak ve yönetmek Için yönlendirici yapılandırma örnekleri](expressroute-config-samples-routing.md). 
