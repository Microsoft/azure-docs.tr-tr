---
title: 'Öğretici: Microsoft eşlemesi için yol filtrelerini Yapılandırma-Azure portal'
description: Bu öğreticide, Azure portal kullanılarak Microsoft eşlemesi için yol filtrelerinin nasıl yapılandırılacağı açıklanmaktadır.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: d142028d197f9e279b5f1e05757946dc40d2c153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109145"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak Microsoft eşlemesi için yol filtrelerini yapılandırma

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

- Yapılandırmaya başlamadan önce [önkoşulları](expressroute-prerequisites.md) ve [iş akışlarını](expressroute-workflows.md) gözden geçirin.

- Microsoft eşlemesi sağlanmış etkin bir ExpressRoute devresine sahip olmanız gerekir. Aşağıdaki yönergeleri kullanarak şu görevleri gerçekleştirebilirsiniz:
  - Devam etmeden önce [bir ExpressRoute devresi oluşturun](expressroute-howto-circuit-portal-resource-manager.md) ve bağlantı sağlayıcınız tarafından devre dışı bırakıldı. ExpressRoute bağlantı hattı sağlanmış ve etkinleştirilmiş durumda olmalıdır.
  - BGP oturumunu doğrudan yönetiyorsanız, [Microsoft eşlemesi oluşturun](expressroute-howto-routing-portal-resource-manager.md) . Ya da bağlantı sağlayıcınızın, devreniz için Microsoft eşlemesi sağlamasını sağlayın.

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Ön eklerin ve BGP topluluk değerlerinin bir listesini alın

### <a name="get-a-list-of-bgp-community-values"></a>BGP topluluk değerlerinin listesini al

Microsoft eşlemesi aracılığıyla erişilebilen hizmetlerle ilişkili BGP topluluk değerleri, [ExpressRoute yönlendirme gereksinimleri](expressroute-routing.md) sayfasında bulunabilir.

### <a name="make-a-list-of-the-values-that-you-want-to-use"></a>Kullanmak istediğiniz değerlerin listesini oluşturma

Yol filtresinde kullanmak istediğiniz [BGP topluluk değerlerinin](expressroute-routing.md#bgp) bir listesini oluşturun. 

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Yol filtresi ve filtre kuralı oluşturma

Yol filtresi yalnızca bir kurala sahip olabilir ve kuralın ' Allow ' türünde olması gerekir. Bu kural, kendisiyle ilişkili BGP topluluk değerlerinin bir listesine sahip olabilir.

1. **Kaynak oluştur** ' u seçin ve aşağıdaki görüntüde gösterildiği gibi *Rota filtresi* araması yapın:

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter.png" alt-text="Yol filtresi sayfasını gösteren ekran görüntüsü":::

1. Yol filtresini bir kaynak grubuna yerleştirin. Konumun ExpressRoute devresiyle aynı olduğundan emin olun. **Gözden geçir + oluştur** ve sonra **Oluştur**' u seçin.

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter-basic.png" alt-text="Örnek değerler girilen rota Filtresi Oluştur sayfasını gösteren ekran görüntüsü":::

### <a name="create-a-filter-rule"></a>Filtre kuralı oluşturma

1. Kuralları eklemek ve güncelleştirmek için rota filtreniz için kuralı Yönet sekmesini seçin.

    :::image type="content" source="./media/how-to-routefilter-portal/manage-route-filter.png" alt-text="Kuralı Yönet eyleminin vurgulandığı genel bakış sayfasını gösteren ekran görüntüsü":::

1. Açılan listeden bağlanmak istediğiniz hizmetleri seçin ve tamamlandığında kuralı kaydedin.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Izin verilen hizmet toplulukları açılan listesinde seçilen hizmetlerle kural yönetme penceresini gösteren ekran görüntüsü":::

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Yol filtresini bir ExpressRoute devresine iliştirme

**+ Devre Ekle** düğmesini seçerek ve açılan listeden ExpressRoute bağlantı hattını seçerek yol filtresini bir devreye ekleyin.

:::image type="content" source="./media/how-to-routefilter-portal/add-circuit-to-route-filter.png" alt-text="Devre Ekle eylemi seçiliyken genel bakış sayfasını gösteren ekran görüntüsü":::

Bağlantı sağlayıcısı ExpressRoute devreniz için eşlemeyi yapılandırıyorsa, **+ devre Ekle** düğmesini seçmeden önce ExpressRoute bağlantı hattı sayfasından devreyi yenileyin.

:::image type="content" source="./media/how-to-routefilter-portal/refresh-express-route-circuit.png" alt-text="Yenileme eylemi seçili olan genel bakış sayfasını gösteren ekran görüntüsü.":::

## <a name="common-tasks"></a><a name="tasks"></a>Genel görevler

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Bir yol filtresinin özelliklerini almak için

Kaynağı portalda açtığınızda yol filtresinin özelliklerini görüntüleyebilirsiniz.

:::image type="content" source="./media/how-to-routefilter-portal/view-route-filter.png" alt-text="Genel bakış sayfasını gösteren ekran görüntüsü":::

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Bir yol filtresinin özelliklerini güncelleştirmek için

1. Bir devreye eklenen BGP topluluk değerlerinin listesini, **kuralı Yönet** düğmesini seçerek güncelleştirebilirsiniz.

    :::image type="content" source="./media/how-to-routefilter-portal/update-route-filter.png" alt-text="Kural yönetme eylemiyle rota filtrelerini güncelleştirme":::

1. İstediğiniz hizmet topluluklarını seçin ve ardından **Kaydet**' i seçin.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Hizmetler seçiliyken kuralı Yönet penceresini gösteren ekran görüntüsü":::

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Bir ExpressRoute bağlantı hattından yol filtresini ayırmak için

Bir devreyi yol filtresinden ayırmak için, devreye sağ tıklayıp **Ilişkiyi kaldır**' ı seçin.

:::image type="content" source="./media/how-to-routefilter-portal/detach-route-filter.png" alt-text="Özet eylemi vurgulanmış şekilde genel bakış sayfasını gösteren ekran görüntüsü":::


## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir rota filtresini **Sil düğmesini seçerek silebilirsiniz.** Yol filtresinin, bunu yapmadan önce herhangi bir devreden ilişkilendirilmediğinden emin olun.

:::image type="content" source="./media/how-to-routefilter-portal/delete-route-filter.png" alt-text="Yol filtresini silme":::

## <a name="next-steps"></a>Sonraki Adımlar

Yönlendirici yapılandırma örnekleri hakkında daha fazla bilgi için bkz.:

> [!div class="nextstepaction"]
> [Yönlendirmeyi ayarlamak ve yönetmek için yönlendirici yapılandırma örnekleri](expressroute-config-samples-routing.md)
