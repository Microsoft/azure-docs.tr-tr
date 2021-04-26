---
title: Azure ExpressRoute için maliyetleri yönetmeyi planlayın
description: Azure portal maliyet analizini kullanarak Azure ExpressRoute için maliyetleri nasıl planlayacağınızı ve yöneteceğinizi öğrenin.
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: de8405477611d62b8a46e8b6b645887cc4d30099
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784250"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>Azure ExpressRoute için maliyetleri planlayın ve yönetin

Bu makalede, ExpressRoute için maliyetleri nasıl planlayabileceğinizi ve yönetebileceğiniz açıklanır. İlk olarak, maliyetleri tahmin etmek üzere hizmet için herhangi bir kaynak eklemeden önce ExpressRoute maliyetlerini planlamaya yardımcı olmak üzere Azure fiyatlandırma hesaplayıcısını kullanın. Azure kaynaklarını eklerken Tahmini maliyetleri gözden geçirin. 

ExpressRoute kaynaklarını kullanmayı başlattıktan sonra, maliyet yönetimi özelliklerini kullanarak bütçeleri ayarlayın ve maliyetleri izleyin. Ayrıca Tahmini maliyetleri gözden geçirebilir ve hareket etmek isteyebileceğiniz yerleri belirlemek için harcama eğilimlerini belirleyebilirsiniz. 

ExpressRoute maliyetlerinin yalnızca Azure faturanızda bulunan Aylık maliyetlerin yalnızca bir kısmı olduğunu aklınızda bulundurun. Bu makalede, ExpressRoute maliyetlerinin nasıl planlanacağı ve yönetileceği açıklanmakta olsa da, üçüncü taraf hizmetler de dahil olmak üzere Azure aboneliğinizde kullanılan tüm Azure hizmetleri ve kaynakları için faturalandırılırsınız.

## <a name="prerequisites"></a>Önkoşullar

Maliyet yönetimi 'nde maliyet analizi, çoğu Azure hesap türünü destekler, ancak bunların tümünü kullanmaz. Desteklenen hesap türlerinin tam listesini görüntülemek için, bkz. [Maliyet Yönetimi verilerini anlama](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Maliyet verilerini görüntülemek için bir Azure hesabı için en azından okuma erişiminizin olması gerekir. 

Azure Maliyet Yönetimi verilerine erişim atama hakkında daha fazla bilgi için bkz. [Verilere erişim atama](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="local-vs-standard-vs-premium"></a>Yerel ve standart ve Premium karşılaştırması

ExpressRoute üç farklı devre SKU 'SU içerir: [*Yerel*](./expressroute-faqs.md#expressroute-local), *Standart* ve [*Premium*](./expressroute-faqs.md#expressroute-premium). ExpressRoute kullanımınız için ücretlendirildiğiniz yol, bu üç SKU türü arasında farklılık gösterir. Yerel SKU ile, sınırsız bir veri planıyla otomatik olarak ücretlendirilirsiniz. Standart ve Premium SKU ile tarifeli veya sınırsız bir veri planı arasından seçim yapabilirsiniz. Global Reach eklentisinin kullanılması dışında tüm giriş verileri ücretsizdir. Maliyet ve bütçeyi en iyi şekilde iyileştirmek için iş yükünüz için hangi SKU türlerinin ve veri planının en iyi şekilde çalıştığını anlamak önemlidir.

## <a name="estimate-costs"></a>Maliyetleri tahmin etme

ExpressRoute bağlantı hattı oluşturmadan önce maliyetleri tahmin etmek için [Azure fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/) kullanın. 

1. Sol tarafta **ağ**' ı seçin ve ardından başlamak Için **Azure ExpressRoute** ' u seçin. 

1. Eşleme konumunuza bağlı olarak uygun *bölgeyi* seçin. Açılan kutudan uygun *bölgeyi* seçmek Için [ExpressRoute bağlantı sağlayıcıları](./expressroute-locations-providers.md#partners) ' na bakın. 

1. Ardından, bir tahmin istediğiniz *SKU*, *devre hızı* ve *veri planını* seçin. 

1. *Ek giden veri aktarımı*' nda, bir ay boyunca ne kadar giden veriler KULLANABILECEĞINIZI gösteren GB cinsinden bir tahmin girin. 

1. Son olarak, tahmine *Global Reach eklentisini* ekleyebilirsiniz.

Aşağıdaki ekran, hesaplayıcı kullanılarak maliyet tahmini gösterir:

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure hesaplayıcıda ExpressRoute maliyet tahmini":::

Daha fazla bilgi için bkz. [Azure ExpressRoute fiyatlandırması](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="expressroute-gateway-estimated-cost"></a>ExpressRoute Gateway tahmini maliyeti

Bir sanal ağı ExpressRoute devresine bağlamak için bir ExpressRoute ağ geçidi kullanıyorsanız, ağ geçidi kullanımının maliyetini tahmin etmek için aşağıdaki adımları kullanın.

1. Sol tarafta **ağ**' ı seçin ve başlamak için **VPN Gateway** seçin. 

1. Ağ geçidinin *bölgesini* seçin ve ardından *türü* **ExpressRoute ağ geçitleri** olarak değiştirin.

1. Açılan listeden *ağ geçidi türünü* seçin.

1. *Ağ geçidi saatlerini* girin. (720 saat = 30 gün)

## <a name="understand-the-full-billing-model-for-expressroute"></a>ExpressRoute için tam faturalandırma modelini anlayın

ExpressRoute, yeni kaynağı dağıtırken ExpressRoute ile birlikte maliyetleri tahakkuk eden Azure altyapısında çalışır. Ek altyapının maliyeti tahakkuk edebileceğini anlamak önemlidir. Dağıtılan kaynaklarda değişiklik yaptığınızda bu maliyeti yönetmeniz gerekir. 

### <a name="costs-that-typically-accrue-with-expressroute"></a>Genellikle ExpressRoute ile tahakkuk eden maliyetler

Bir ExpressRoute bağlantı hattı oluşturduğunuzda Sanal ağınızı devresine bağlamak için bir ExpressRoute Ağ Geçidi oluşturmayı tercih edebilirsiniz. Ağ geçitleri saatlik bir ücret ve bir ExpressRoute bağlantı hattının maliyeti üzerinden ücretlendirilir. Farklı ağ geçidi SKU 'Larının fiyatlarını görmek için bkz. [ExpressRoute fiyatlandırması](https://azure.microsoft.com/en-us/pricing/details/expressroute) ve ExpressRoute ağ geçitleri seçin.
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Kaynak silme işleminin ardından maliyetler tahakkuk edebilir

ExpressRoute bağlantı hattını sildikten sonra bir ExpressRoute ağ geçidinseniz, siz silene kadar ücret ödersiniz.

### <a name="using-azure-prepayment-credit"></a>Azure ön ödeme kredisi kullanma

ExpressRoute ücretleri için Azure ön ödemenize (daha önce parasal taahhüt adı verilir) kredi ile ödeme yapabilirsiniz. Ancak Azure ön ödeme kredisi, Azure Marketi 'nden farklı üçüncü taraf ürün ve hizmetlerine yönelik ücretler için ödeme yapmak üzere kullanamazsınız.

## <a name="monitor-costs"></a>Maliyetleri izleme

ExpressRoute ile Azure kaynaklarını kullanırken maliyetleriniz vardır. Azure Kaynak kullanımı birim maliyetleri zaman aralıklarına (saniye, dakika, saat ve gün) veya birim kullanımı (bayt, megabayt vb.) göre farklılık gösterir. ExpressRoute kullanımı başladıktan hemen sonra maliyetler ücretlendirilir ve [Maliyet analizinde](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)maliyetleri görebilirsiniz.

Maliyet analizini kullandığınızda, ExpressRoute devresi maliyetlerini farklı zaman aralıkları için grafikler ve tablolarda görüntüleyebilirsiniz. Bazı örnekler güne, geçerli ve önceki aya ve yıla göre yapılır. Ayrıca bütçeleri ve tahmin edilen maliyetleri de görüntüleyebilirsiniz. Zamana göre daha uzun görünümlere geçiş yapmak harcama eğilimlerini belirlemenize yardımcı olabilir. Ve fazla harcama oluşmuş olabileceğini görürsünüz. Bütçeleri oluşturduysanız, nerede aşıldığınızı da kolayca görebilirsiniz.

ExpressRoute maliyetlerini maliyet analizi 'nde görüntülemek için:

1. Azure portalında oturum açın.

1. **Abonelikler**' e gidin, listeden bir abonelik seçin ve ardından menüde **Maliyet Analizi** ' ni seçin. Maliyet analizinde farklı bir kapsama geçiş yapmak için **kapsam** ' ı seçin. Varsayılan olarak, hizmetlerin maliyeti ilk halka grafiğinde gösterilir.

    Maliyet analizini ilk kez açtığınızda gerçek aylık maliyetler gösterilir. İşte tüm aylık kullanım maliyetlerini gösteren bir örnek.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="Bir aboneliğin birikmiş maliyetlerini gösteren örnek":::
    

1.  ExpressRoute gibi tek bir hizmetin maliyetlerini daraltmak için **Filtre Ekle** ' yi ve ardından **hizmet adı**' nı seçin. Ardından, **ExpressRoute**' ı seçin.

    Yalnızca ExpressRoute için maliyetleri gösteren bir örnek aşağıda verilmiştir.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="ExpressRoute için birikmiş maliyetlerin gösterildiği örnek":::

Önceki örnekte, hizmet için geçerli maliyeti görürsünüz. Azure bölgelerine göre maliyetler (konumlar) ve ExpressRoute maliyetleri kaynak grubuna göre de gösterilir. Buradan, maliyetlerinizi kendiniz inceleyebilirsiniz.

## <a name="create-budgets-and-alerts"></a>Bütçeler ve uyarılar oluşturma

Maliyetleri yönetmek için [bütçeler](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilir ve paydaşları harcama anormallikleri ve fazla harcama riskleri ile ilgili otomatik olarak bilgilendiren [uyarılar](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilirsiniz. Uyarılar, bütçe ve maliyet eşiklerine kıyasla harcamaya göre belirlenir. Bütçeler ve uyarılar Azure abonelikleri ve kaynak grupları için oluşturulur, bu nedenle genel maliyet izleme stratejisinin bir parçası olarak faydalıdır. 

İzlemekte daha fazla ayrıntı düzeyi istiyorsanız, Azure 'daki belirli kaynaklara veya hizmetlere yönelik filtrelerle bütçeler oluşturulabilir. Filtreler, yanlışlıkla ek para maliyeti sağlayan yeni kaynaklar oluşturmamasını sağlamaya yardımcı olur. Bir bütçe oluşturduğunuzda filtre seçenekleri hakkında daha fazla bilgi için bkz. [Grup ve filtre seçenekleri](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Maliyet verilerini dışarı aktarma

Ayrıca, maliyet verilerinizi bir depolama hesabına [dışarı aktarabilirsiniz](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Bu, maliyetler için ek veri analizi yapmanız gerektiğinde veya başkalarının yararlı olması durumunda faydalıdır. Örneğin, bir Finans ekibi, Excel veya Power BI kullanarak verileri analiz edebilir. Maliyetlerinizi günlük, haftalık veya aylık bir zamanlamaya göre dışa aktarabilir ve özel bir tarih aralığı ayarlayabilirsiniz. Maliyet verilerini dışarı aktarmak, maliyet veri kümelerini almak için önerilen yoldur.

## <a name="next-steps"></a>Sonraki adımlar

- Fiyatlandırma ile Azure ExpressRoute ile nasıl çalıştığı hakkında daha fazla bilgi edinin. Bkz. [Azure ExpressRoute genel bakış fiyatlandırması](https://azure.microsoft.com/en-us/pricing/details/expressroute/).
- [Azure maliyet yönetimi ile bulut yatırımınızın nasıl iyileştirileceği hakkında](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)bilgi edinin.
- [Maliyet Analizi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)ile maliyetleri yönetme hakkında daha fazla bilgi edinin.
- [Beklenmeyen maliyetlerin nasıl önleneceği](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)hakkında bilgi edinin.
- [Maliyet yönetimi](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) destekli öğrenme kursuna katılın.