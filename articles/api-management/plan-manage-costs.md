---
title: Azure API Management için maliyetleri planlayın ve yönetin
description: Azure portal maliyet analizini kullanarak Azure API Management maliyetlerini nasıl planlayacağınızı ve yöneteceğinizi öğrenin.
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 1ebb89ae318e57f1d4e0708a08019515ca43158d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581338"
---
# <a name="plan-and-manage-costs-for-api-management"></a>API Management maliyetlerini planlayın ve yönetin

Bu makalede, Azure API Management maliyetlerini nasıl planlayabileceğinizi ve yönetebileceğiniz açıklanır. İlk olarak, maliyetleri tahmin etmek üzere hizmet için herhangi bir kaynak eklemeden önce API Management maliyetlerini planlamaya yardımcı olması için Azure Fiyatlandırma hesaplayıcısı ' nı kullanırsınız. API Management kaynaklarını kullanmaya başladıktan sonra, bütçe ayarlamak ve maliyetleri izlemek için maliyet yönetimi özelliklerini kullanın. Ayrıca Tahmini maliyetleri gözden geçirebilir ve hareket etmek isteyebileceğiniz yerleri belirlemek için harcama eğilimlerini belirleyebilirsiniz. 

API Management maliyetleri, Azure faturanızda bulunan Aylık maliyetlerin yalnızca bir parçasıdır. Bu makalede API Management maliyetlerinin nasıl planlanacağı ve yönetileceği açıklanmakta olsa da, üçüncü taraf hizmetler de dahil olmak üzere Azure aboneliğinizde kullanılan tüm Azure hizmetleri ve kaynakları için faturalandırılırsınız.

## <a name="prerequisites"></a>Önkoşullar

Maliyet yönetimi 'nde maliyet analizi, çoğu Azure hesap türünü destekler, ancak bunların tümünü kullanmaz. Desteklenen hesap türlerinin tam listesini görüntülemek için, bkz. [Maliyet Yönetimi verilerini anlama](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Maliyet verilerini görüntülemek için bir Azure hesabı için en azından okuma erişiminizin olması gerekir. Azure Maliyet Yönetimi verilerine erişim atama hakkında daha fazla bilgi için bkz. [Verilere erişim atama](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-api-management"></a>API Management kullanmadan önce maliyetleri tahmin etme

API Management eklemeden önce maliyetleri tahmin etmek için [Azure Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/) ' nı kullanın. 

1. *API Management* arayın veya **tümleştirme**  >  **API Management** seçin.
1. Bir API Management hizmet örneği için varsayılan maliyet tahmini eklemek üzere **Görünüm** ' ü seçin.

> [!NOTE]
> Bu örnekte gösterilen maliyetler yalnızca tanıtım amaçlıdır. En son fiyatlandırma bilgileri için bkz. [API Management fiyatlandırması](https://azure.microsoft.com/pricing/details/api-management/) .

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="Geliştirici katmanı için tahmin maliyetleri":::

* Varsayılan maliyet tahmini, 1 [Kapasite birimi](api-management-capacity.md)ile **Geliştirici** [hizmeti katmanındaki](api-management-features.md) bir API Management hizmet örneğini temel alır. Geliştirici katmanı, üretim dışı kullanım örnekleri ve değerlendirmeleri içindir. Hizmet düzeyi sözleşmesi tarafından yedeklenmez.

* Ek Kapasite birimleri veya farklı bir hizmet katmanının maliyetlerini tahmin etmek için, **birimler** ve **Katman** açılır listesinden diğer seçenekleri belirleyin.

* Özellik kullanılabilirliği ve hizmet katmanına bağlı olarak, şirket içinde [barındırılan ağ geçitlerinin](self-hosted-gateway-overview.md)kullanımı için ek ücretler uygulanabilir.

Ek fiyatlandırma ve özellik ayrıntıları için bkz.

* [API Management fiyatlandırması](https://azure.microsoft.com/pricing/details/api-management/)
* [Azure API Management katmanlarının Özellik tabanlı karşılaştırması](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>API Management ile parasal krediyi kullanma

Azure ön ödemenizin (daha önce parasal taahhüt olarak adlandırılır) API Management ücretleri için ödeme yapabilirsiniz. Ancak Azure ön ödeme kredisi, Azure Marketi 'nden farklı üçüncü taraf ürün ve hizmetlerine yönelik ücretler için ödeme yapmak üzere kullanamazsınız.

## <a name="monitor-costs"></a>Maliyetleri izleme

API Management ile Azure kaynaklarını kullanırken maliyetleriniz vardır. Azure Kaynak kullanımı birim maliyetleri zaman aralıklarına (saniye, dakika, saat ve gün) veya birim kullanımı (bayt, megabayt vb.) göre değişir. API Management kullanımı başladığı anda maliyetler ücretlendirilir ve [Maliyet analizinde](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)maliyetleri görebilirsiniz.

Maliyet analizini kullandığınızda, grafikler ve tablolardaki API Management maliyetlerini farklı zaman aralıkları için görüntüleyebilirsiniz. Bazı örnekler güne, geçerli ve önceki aya ve yıla göre yapılır. Ayrıca bütçeleri ve tahmin edilen maliyetleri de görüntüleyebilirsiniz. Zamana göre daha uzun görünümlere geçiş yapmak harcama eğilimlerini belirlemenize yardımcı olabilir. Ve fazla harcama oluşmuş olabileceğini görürsünüz. Bütçeleri oluşturduysanız, nerede aşıldığınızı da kolayca görebilirsiniz.

> [!NOTE]
> Bu örnekte gösterilen maliyetler yalnızca tanıtım amaçlıdır. Maliyetleriniz, kaynak kullanımına ve geçerli fiyatlandırmaya göre değişir.

Maliyet analizinde API Management maliyetlerini görüntülemek için:

1. [Azure portalında](https://azure.microsoft.com) oturum açın.
1. **Maliyet yönetimi + faturalandırma** penceresini açın, menüden **maliyet yönetimi** ' ni seçin ve ardından bir **faturalandırma kapsamı** seçin. Örneğin, listeden bir abonelik seçin.
1. Menüden **maliyet yönetimi** ' ni seçin ve ardından **Maliyet Analizi**' ni seçin.
1. Varsayılan olarak, tüm hizmetler için aylık maliyetler ilk halka grafiğinde gösterilir. 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="Abonelik için aylık maliyetler":::

1. API Management gibi tek bir hizmetin maliyetlerini daraltmak için, **Filtre Ekle** ' yi ve ardından **hizmet adı**' nı seçin. Ardından **API Management**' yi seçin.

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="API Management için birikmiş maliyetleri gösteren örnek":::

Önceki örnekte, hizmet için geçerli maliyeti görürsünüz. Azure bölgelerine (konumlar) göre maliyetler ve kaynak grubuna göre API Management maliyetleri de gösterilir. Buradan, maliyetlerinizi kendiniz inceleyebilirsiniz.

## <a name="create-budgets"></a>Bütçe oluşturma

Maliyetleri yönetmek için [bütçeler](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilir ve paydaşları harcama anormallikleri ve fazla harcama riskleri ile ilgili otomatik olarak bilgilendiren [uyarılar](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilirsiniz. Uyarılar, bütçe ve maliyet eşiklerine kıyasla harcamaya göre belirlenir. Bütçeler ve uyarılar Azure abonelikleri ve kaynak grupları için oluşturulur, bu nedenle genel maliyet izleme stratejisinin bir parçası olarak faydalıdır. 

İzlemekte daha fazla ayrıntı düzeyi istiyorsanız, Azure 'daki belirli kaynaklara veya hizmetlere yönelik filtrelerle bütçeler oluşturulabilir. Filtreler, yanlışlıkla ek para maliyeti sağlayan yeni kaynaklar oluşturmamasını sağlamaya yardımcı olur. Bir bütçe oluştururken filtre seçenekleri hakkında daha fazla bilgi için bkz. [Grup ve filtre seçenekleri](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Maliyet verilerini dışarı aktarma

Ayrıca, maliyet verilerinizi bir depolama hesabına [dışarı aktarabilirsiniz](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Bu, diğer kullanıcıların maliyetler için ek veri analizi yapması gerektiğinde faydalıdır. Örneğin, bir Finans ekibi, Excel veya Power BI kullanarak verileri analiz edebilir. Maliyetlerinizi günlük, haftalık veya aylık bir zamanlamaya göre dışa aktarabilir ve özel bir tarih aralığı ayarlayabilirsiniz. Maliyet verilerini dışarı aktarmak, maliyet veri kümelerini almak için önerilen yoldur.

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>API Management maliyetlerini yönetmenin ve azaltmanın diğer yolları

### <a name="choose-tier"></a>Katman seçin

Senaryolarınız için hangi hizmet katmanının uygun olabileceğini belirlemenize yardımcı olmak üzere [Azure API Management katmanlarının Özellik tabanlı karşılaştırmasını](api-management-features.md) gözden geçirin. Farklı hizmet katmanları, farklı maliyetler ile çeşitli kullanım durumları için tasarlanan özellik ve yeteneklerin birleşimlerini destekler. 

* **Tüketim** hizmeti katmanı, sabit maliyet içermeyen hafif ve sunucusuz bir seçenek sağlar. Belirli bir eşiğin üzerinde hizmete yönelik API çağrısı sayısına göre faturalandırılırsınız. Kapasite Ayrıca hizmetin yüküne göre otomatik olarak ölçeklendirilir.
* **Geliştirici**, **temel**, **Standart** ve **Premium** API Management katmanları aylık maliyetlere sahiptir ve değerlendirme ve üretim iş yükleri için daha fazla verimlilik ve daha zengin özellik kümeleri sağlar. Dilediğiniz zaman farklı bir hizmet katmanına [yükseltin](upgrade-and-scale.md) .

### <a name="scale-using-capacity-units"></a>Kapasite birimleri kullanarak ölçeklendirme

Tüketim hizmeti katmanı haricinde, [*Kapasite birimleri*](api-management-capacity.md)ekleyerek veya kaldırarak ölçeklendirmeyi destekler API Management. Yük bir API Management örneğinde arttıkça, kapasite birimleri ekleme daha yüksek bir hizmet katmanına yükseltilenden daha ekonomik olabilir. En fazla birim sayısı hizmet katmanına bağlıdır.

Her kapasite birimi, hizmetin katmanına bağlı belirli bir istek işleme özelliğine sahiptir. Örneğin, temel bir katmanın bir birimi saniyede yaklaşık 1.000 istek için tahmini en yüksek aktarım hızına sahiptir. 

Birimleri, kapasiteyi ve maliyet ölçeğini orantılı olarak ekler veya kaldırırsanız. Örneğin, standart katmanın iki birimi saniyede yaklaşık 2.000 istekten oluşan tahmini bir verimlilik sağlar. İsteklerin veya yanıtların boyutu, bağlantı desenleri, istek yapan istemci sayısı ve diğer faktörlere göre gerçek aktarım hızı farklı olabilir.

Daha fazla yüklemeye uyum sağlamak için bir API Management örneğini ölçeklendirmeye veya yükseltmeye karar vermenize yardımcı olmak üzere API Management örneğiniz için kapasite ölçümünü [izleyin](api-management-howto-use-azure-monitor.md) .

## <a name="next-steps"></a>Sonraki adımlar

- [Azure maliyet yönetimi ile bulut yatırımınızın nasıl iyileştirileceği hakkında](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)bilgi edinin.
- [Maliyet Analizi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)ile maliyetleri yönetme hakkında daha fazla bilgi edinin.
- [Beklenmeyen maliyetlerin nasıl önleneceği](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)hakkında bilgi edinin.
- [Maliyet yönetimi](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) destekli öğrenme kursuna katılın.
- API Management [kapasitesi](api-management-capacity.md)hakkında bilgi edinin.
- [Azure Portal](upgrade-and-scale.md)kullanarak API Management ölçeklendirmek ve yükseltmek için adımlara bakın ve [Otomatik ölçeklendirme](api-management-howto-autoscale.md)hakkında bilgi edinin.
