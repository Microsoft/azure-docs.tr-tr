---
title: Azure SYNAPSE Analytics için maliyetleri yönetmeyi planlayın
description: Azure portal maliyet analizini kullanarak Azure SYNAPSE Analytics maliyetlerini nasıl planlayacağınızı ve yöneteceğinizi öğrenin.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: 15ac6ce6a1a49bbbd15849adec373dd0fcd42c10
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568531"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Azure SYNAPSE Analytics için maliyetleri planlayın ve yönetin

Bu makalede, Azure SYNAPSE Analytics maliyetlerinin nasıl planlanacağı ve yönetileceği açıklanmaktadır. İlk olarak, maliyetleri tahmin etmek üzere hizmet için herhangi bir kaynak eklemeden önce Azure SYNAPSE maliyetlerini planlamaya yardımcı olmak üzere Azure Fiyatlandırma hesaplayıcısı ' nı kullanırsınız. Daha sonra, Azure SYNAPSE kaynaklarını eklerken Tahmini maliyetleri gözden geçirin.

Azure SYNAPSE kaynaklarını kullanmaya başladıktan sonra, bütçe ayarlamak ve maliyetleri izlemek için maliyet yönetimi özelliklerini kullanın. Ayrıca Tahmini maliyetleri gözden geçirebilir ve hareket etmek isteyebileceğiniz yerleri belirlemek için harcama eğilimlerini belirleyebilirsiniz. Azure SYNAPSE için maliyetler yalnızca Azure faturanızda bulunan Aylık maliyetlerin bir parçasıdır. Bu makalede, Azure SYNAPSE maliyetlerinin nasıl planlanacağı ve yönetileceği açıklanmakta olsa da, üçüncü taraf hizmetler de dahil olmak üzere Azure aboneliğinizde kullanılan tüm Azure hizmetleri ve kaynakları için faturalandırılırsınız.

## <a name="prerequisites"></a>Önkoşullar

Maliyet yönetimi 'nde maliyet analizi, çoğu Azure hesap türünü destekler, ancak bunların tümünü kullanmaz. Desteklenen hesap türlerinin tam listesini görüntülemek için, bkz. [Maliyet Yönetimi verilerini anlama](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Maliyet verilerini görüntülemek için bir Azure hesabı için en azından okuma erişiminizin olması gerekir. Azure Maliyet Yönetimi verilerine erişim atama hakkında daha fazla bilgi için bkz. [Verilere erişim atama](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'i kullanmadan önce maliyetleri tahmin etme

Azure SYNAPSE Analytics 'i eklemeden önce maliyetleri tahmin etmek için [Azure Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/) ' nı kullanın.

Azure SYNAPSE, aşağıdaki maliyet tahmininde görüldüğü gibi farklı ücretlere sahip çeşitli kaynaklara sahiptir. 

![Azure Fiyatlandırma Hesaplayıcı 'da tahmini maliyeti gösteren örnek](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Azure SYNAPSE Analytics için tam faturalandırma modelini anlayın

Azure SYNAPSE, yeni kaynağı dağıtırken Azure SYNAPSE ile birlikte maliyetleri tahakkuk eden Azure altyapısında çalışır. Ek altyapının maliyeti tahakkuk edebileceğini anlamak önemlidir. Dağıtılan kaynaklarda değişiklik yaptığınızda bu maliyeti yönetmeniz gerekir. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Genellikle Azure SYNAPSE Analytics ile tahakkuk eden maliyetler

Azure SYNAPSE için kaynak oluşturduğunuzda, diğer Azure hizmetlerine yönelik kaynaklar da oluşturulur. Bunlara aşağıdakiler dahildir:

- Data Lake Storage 2. Nesil

 ### <a name="costs-might-accrue-after-resource-deletion"></a>Kaynak silme işleminin ardından maliyetler tahakkuk edebilir

Azure SYNAPSE kaynaklarını sildikten sonra, aşağıdaki kaynaklar mevcut olmaya devam edebilir. Bunlar silinene kadar maliyetleri tahakkuk ettirmeye devam eder.

- Data Lake Storage 2. Nesil

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Azure SYNAPSE ile Azure ön ödeme kredisi kullanma 

Azure ön ödemenizin (daha önce parasal taahhüt olarak adlandırılır) krediyle Azure SYNAPSE ücretleri için ödeme yapabilirsiniz. Ancak Azure ön ödeme kredisi, Azure Marketi 'nden dahil olan üçüncü taraf ürün ve hizmetlerine yönelik ücretler için ödeme yapmak üzere kullanamazsınız.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Azure portalında tahmini maliyetleri gözden geçirme

Azure SYNAPSE Analytics için kaynaklar oluştururken Tahmini maliyetleri görürsünüz. Çalışma alanında, çalışma alanıyla oluşturulmuş sunucusuz bir SQL havuzu bulunur. Sorgular çalıştırılıncaya kadar sunucusuz SQL havuzu ücret ödemeyecektir. Adanmış SQL havuzları ve sunucusuz Apache Spark havuzları gibi diğer kaynakların çalışma alanı içinde oluşturulması gerekir.

Bir oluşturmak <ResourceName> ve tahmini fiyatı görüntülemek için:

1. Azure portal hizmete gidin.
2. Kaynağı oluşturun.
3. Özette gösterilen tahmini fiyatı gözden geçirin.
4. Kaynağı oluşturmayı tamamlama.

![Kaynak oluştururken tahmini maliyetlerin gösterildiği örnek](./media/plan-manage-costs/create-workspace-cost.png)


Azure aboneliğinizin harcama limiti varsa, Azure kredi miktarınızdan harcamanıza engel olur. Azure kaynaklarını oluştururken ve kullanırken kredileriniz kullanılır. Kredi sınırınıza ulaştığınızda, dağıttığınız kaynaklar bu fatura döneminin geri kalanı için devre dışı bırakılır. Kredi sınırınızı değiştiremezsiniz, ancak kaldırabilirsiniz. Harcama limitleri hakkında daha fazla bilgi için bkz. [Azure harcama limiti](../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Maliyetleri izleme

Azure SYNAPSE kaynaklarını kullanırken ücretlendirilirsiniz. Azure Kaynak kullanımı birim maliyetleri zaman aralıklarına (saniye, dakika, saat ve gün) veya birim kullanımı (bayt, megabayt vb.) göre farklılık gösterir. Azure SYNAPSE 'te kaynak kullanmaya başladığınızda maliyetler ücretlendirilir ve [Maliyet analizinde](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)maliyetleri görebilirsiniz.

Maliyet analizini kullandığınızda, farklı zaman aralıkları için grafikler ve tablolardaki Azure SYNAPSE Analytics maliyetlerini görüntüleyebilirsiniz. Bazı örnekler güne, geçerli ve önceki aya ve yıla göre yapılır. Ayrıca bütçeleri ve tahmin edilen maliyetleri de görüntüleyebilirsiniz. Zamana göre daha uzun görünümlere geçiş yapmak harcama eğilimlerini belirlemenize yardımcı olabilir. Ve fazla harcama oluşmuş olabileceğini görürsünüz. Bütçeleri oluşturduysanız, nerede aşıldığınızı da kolayca görebilirsiniz.

Azure SYNAPSE maliyetlerini maliyet analizi 'nde görüntülemek için:

1. Azure portalında oturum açın.
2. Azure portal abonelik veya kaynak grubunu açın ve menüden **Maliyet Analizi** ' ni seçin. Örneğin, **abonelikler**' e gidin, listeden bir abonelik seçin ve ardından menüde  **Maliyet Analizi** ' ni seçin. Maliyet analizinde farklı bir kapsama geçiş yapmak için **kapsam** ' ı seçin.
3. Varsayılan olarak, hizmetlerin maliyeti ilk halka grafiğinde gösterilir. Azure SYNAPSE etiketli grafikte alanı seçin.

Maliyet analizini ilk kez açtığınızda gerçek aylık maliyetler gösterilir. İşte tüm aylık kullanım maliyetlerini gösteren bir örnek.

![Bir aboneliğin birikmiş maliyetlerini gösteren örnek](./media/plan-manage-costs/actual-monthly-costs.png)

- Azure SYNAPSE gibi tek bir hizmetin maliyetlerini daraltmak için **Filtre Ekle** ' yi ve ardından **hizmet adı**' nı seçin. Ardından **Azure SYNAPSE Analytics**' i seçin.

Yalnızca Azure SYNAPSE için maliyetleri gösteren bir örnek aşağıda verilmiştir.

![ServiceName için birikmiş maliyetleri gösteren örnek](./media/plan-manage-costs/filtered-monthly-costs.png)

Önceki örnekte, hizmet için geçerli maliyeti görürsünüz. Azure bölgeleri (konumlar) ve Azure SYNAPSE maliyetlerini kaynak grubuna göre maliyetler de gösterilir. Buradan, maliyetlerinizi kendiniz inceleyebilirsiniz.

## <a name="create-budgets"></a>Bütçe oluşturma

Maliyetleri yönetmek için [bütçeler](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilir ve paydaşları harcama anormallikleri ve fazla harcama riskleri ile ilgili otomatik olarak bilgilendiren [uyarılar](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilirsiniz. Uyarılar, bütçe ve maliyet eşiklerine kıyasla harcamaya göre belirlenir. Bütçeler ve uyarılar Azure abonelikleri ve kaynak grupları için oluşturulur, bu nedenle genel maliyet izleme stratejisinin bir parçası olarak faydalıdır. 

İzlemekte daha fazla ayrıntı düzeyi istiyorsanız, Azure 'daki belirli kaynaklara veya hizmetlere yönelik filtrelerle bütçeler oluşturulabilir. Filtreler, yanlışlıkla ek para maliyeti sağlayan yeni kaynaklar oluşturmamasını sağlamaya yardımcı olur. Bir bütçe oluşturduğunuzda filtre seçenekleri hakkında daha fazla bilgi için bkz. [Grup ve filtre seçenekleri](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Maliyet verilerini dışarı aktarma

Ayrıca, maliyet verilerinizi bir depolama hesabına [dışarı aktarabilirsiniz](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Bu, maliyetler için ek veri analizi yapmanız gerektiğinde veya başkalarının yararlı olması durumunda faydalıdır. Örneğin, bir finans ekipleri, Excel veya Power BI kullanarak verileri analiz edebilir. Maliyetlerinizi günlük, haftalık veya aylık bir zamanlamaya göre dışa aktarabilir ve özel bir tarih aralığı ayarlayabilirsiniz. Maliyet verilerini dışarı aktarmak, maliyet veri kümelerini almak için önerilen yoldur.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Azure SYNAPSE için maliyetleri yönetmenin ve azaltmanın diğer yolları 

### <a name="serverless-sql-pool"></a>Sunucusuz SQL havuzu

Sunucusuz SQL havuzunun maliyeti hakkında daha fazla bilgi edinmek için bkz. [Azure SYNAPSE Analytics 'te SUNUCUSUZ SQL havuzu Için maliyet yönetimi](./sql/data-processed.md)

### <a name="dedicated-sql-pool"></a>Ayrılmış SQL havuzu

Adanmış bir SQL havuzunun maliyetlerini, kullanımda olmadığında kaynak duraklayan şekilde denetleyebilirsiniz. Örneğin, gece ve hafta sonları sırasında veritabanını kullanmıyorsanız, bu zamanlarda duraklatıp gün içinde devam edebilirsiniz. Daha fazla bilgi için bkz [. Azure Portal aracılığıyla ADANMıŞ SQL havuzunda Işlem duraklatma ve devam ettirme](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="serverless-apache-spark-pool"></a>Sunucusuz Apache Spark havuzu

Sunucusuz Apache Spark havuzunuzun maliyetlerini denetlemek için sunucusuz Apache Spark otomatik duraklatma özelliğini etkinleştirin ve zaman aşımı değerini uygun şekilde ayarlayın.  Geliştirme için SYNAPSE Studio 'yu kullanırken, bu da yapılandırılabilir olan oturumu etkin tutmak üzere canlı tut iletisi gönderir, bu nedenle otomatik duraklama için kısa bir zaman aşımı değeri ayarlayın.  İşiniz bittiğinde oturumunuzu kapatın ve zaman aşımı değerine ulaşıldığında Apache Spark havuzu otomatik olarak duraklatılır.
 
Geliştirme sırasında, çeşitli boyutlarda birden çok Apache Spark havuz tanımı oluşturun.  Apache Spark havuz tanımlarının oluşturulması ücretsizdir ve yalnızca kullanım için ücretlendirilirsiniz.  Azure SYNAPSE ' de Apache Spark kullanımı, vCore saati başına ücretlendirilir ve dakikaya eşit olarak dağıtılır.  Örneğin, performans testi için daha büyük havuz boyutları kullanırken kod geliştirme ve doğrulama için küçük havuz boyutları kullanın.


### <a name="data-integration---pipelines-and-data-flows"></a>Veri tümleştirme-işlem hatları ve veri akışları 

Veri tümleştirme maliyeti hakkında daha fazla bilgi edinmek için bkz. [Azure Data Factory için maliyetleri planlayın ve yönetin](../data-factory/plan-manage-costs.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure maliyet yönetimi ile bulut yatırımınızın nasıl iyileştirileceği hakkında](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)bilgi edinin.
- [Maliyet Analizi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)ile maliyetleri yönetme hakkında daha fazla bilgi edinin.
- [Beklenmeyen maliyetlerin nasıl önleneceği](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)hakkında bilgi edinin.
- [Maliyet yönetimi](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) destekli öğrenme kursuna katılın.
- [Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md) maliyetlerini planlama ve yönetme hakkında bilgi edinin