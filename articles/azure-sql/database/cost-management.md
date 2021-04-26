---
title: Azure SQL veritabanı için maliyetleri planlayın ve yönetin
description: Azure portal maliyet analizini kullanarak Azure SQL veritabanı için maliyetleri nasıl planlayacağınızı ve yöneteceğinizi öğrenin.
author: stevestein
ms.author: sstein
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 56cf30d89460df8ac50d258bd8b29cf4e7236690
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734639"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Azure SQL veritabanı için maliyetleri planlayın ve yönetin

Bu makalede, Azure SQL veritabanı için maliyetleri nasıl planlayabileceğinizi ve yönetebileceğiniz açıklanır. İlk olarak, Azure fiyatlarını eklemek ve tahmini maliyetleri gözden geçirmek için Azure Fiyatlandırma hesaplayıcısı ' nı kullanın. Azure SQL veritabanı kaynaklarını kullanmaya başladıktan sonra, bütçe ayarlamak ve maliyetleri izlemek için maliyet yönetimi özelliklerini kullanın. Ayrıca Tahmini maliyetleri gözden geçirebilir ve hareket etmek isteyebileceğiniz yerleri belirlemek için harcama eğilimlerini belirleyebilirsiniz. Azure SQL veritabanı için maliyetler yalnızca Azure faturanızda bulunan Aylık maliyetlerin bir parçasıdır. Bu makalede, Azure SQL veritabanı maliyetlerinin nasıl planlanacağı ve yönetileceği açıklanmakta olsa da, herhangi bir üçüncü taraf hizmeti de dahil olmak üzere Azure aboneliğinizde kullanılan tüm Azure hizmetleri ve kaynakları için faturalandırılırsınız.


## <a name="prerequisites"></a>Önkoşullar

Maliyet Analizi çoğu Azure hesap türünü destekler, ancak bunların tümünü kullanmaz. Desteklenen hesap türlerinin tam listesini görüntülemek için, bkz. [Maliyet Yönetimi verilerini anlama](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Maliyet verilerini görüntülemek için bir Azure hesabı için en azından okuma erişiminizin olması gerekir. 

Azure Maliyet Yönetimi verilerine erişim atama hakkında daha fazla bilgi için bkz. [Verilere erişim atama](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).


## <a name="sql-database-initial-cost-considerations"></a>SQL veritabanı ilk maliyet konuları

Azure SQL veritabanı ile çalışırken göz önünde bulundurmanız gereken birkaç maliyet tasarrufu özelliği vardır:


### <a name="vcore-or-dtu-purchasing-models"></a>Sanal çekirdek veya DTU satın alma modelleri 

Azure SQL veritabanı iki satın alma modelini destekler: vCore ve DTU. Ücretlendirmenin nasıl alınacağı, satın alma modelleri arasında farklılık gösterir. bu sayede, maliyetleri planlarken ve göz önünde bulundurarak iş yükünüz için en iyi şekilde çalışacak modeli anlamanız önemlidir. VCore ve DTU satın alma modelleri hakkında daha fazla bilgi için bkz. [vCore ve DTU satın alma modelleri arasında seçim yapın](purchasing-models.md).


### <a name="provisioned-or-serverless"></a>Sağlanmış veya sunucusuz

Sanal çekirdek satın alma modelinde, Azure SQL veritabanı aynı zamanda iki tür işlem katmanını da destekler: sağlanan aktarım hızı ve sunucusuz. Her bir işlem katmanının ücretlendirildiği şekilde, maliyetleri planlarken ve göz önünde bulundurarak iş yükünüz için en iyi neyin çalıştığını anlamak önemlidir. Ayrıntılar için bkz. [Vcore modeline genel bakış-işlem katmanları](service-tiers-vcore.md#compute-tiers).

Sanal çekirdek tabanlı satın alma modelinin sağlanan işlem katmanında, mevcut lisanslarınızı indirimli ücretler için Exchange 'e dönüştürebilirsiniz. Ayrıntılar için bkz. [Azure hibrit avantajı (AHB)](../azure-hybrid-benefit.md).

### <a name="elastic-pools"></a>Esnek havuzlar

Farklı ve öngörülemeyen kullanım taleplerine sahip birden çok veritabanına sahip ortamlar için elastik havuzlar aynı miktarda tek veritabanı sağlamaya kıyasla maliyet tasarrufu sağlayabilir. Ayrıntılar için bkz. [elastik havuzlar](elastic-pool-overview.md).

## <a name="estimate-azure-sql-database-costs"></a>Azure SQL veritabanı maliyetlerini tahmin etme

Farklı Azure SQL veritabanı yapılandırmalarının maliyetlerini tahmin etmek için [Azure Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/) ' nı kullanın. Aşağıdaki resimdeki bilgiler ve fiyatlandırma yalnızca örnek amaçlıdır:

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Azure SQL Veritabanı Fiyatlandırma Hesaplayıcı örneği":::

Ayrıca, farklı bekletme Ilkesi seçeneklerinin maliyeti nasıl etkilediğini de tahmin edebilirsiniz. Aşağıdaki resimdeki bilgiler ve fiyatlandırma yalnızca örnek amaçlıdır:

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="Depolama için Azure SQL Veritabanı Fiyatlandırma Hesaplayıcı örneği":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Azure SQL veritabanı için tam faturalandırma modelini anlayın

Azure SQL veritabanı, yeni kaynağı dağıtırken Azure SQL veritabanı ile birlikte maliyetleri tahakkuk eden Azure altyapısında çalışır. Ek altyapının maliyeti tahakkuk edebileceğini anlamak önemlidir. Dağıtılan kaynaklarda değişiklik yaptığınızda bu maliyeti yönetmeniz gerekir. 


Azure SQL veritabanı (sunucusuz dışında), öngörülebilir, saatlik bir ücret üzerinden faturalandırılır. SQL veritabanı bir saatten daha az etkin ise, kullanımdan veya veritabanının bir saatten az etkin olup olmamasına bakılmaksızın, seçilen en yüksek hizmet katmanını, sağlanan depolama alanını ve o saat içinde uygulanan GÇ 'yi kullanarak veritabanının mevcut olduğu her saat için faturalandırılırsınız.


### <a name="using-monetary-credit-with-azure-sql-database"></a>Azure SQL veritabanı ile parasal krediyi kullanma

Azure ön ödemeli (daha önce parasal taahhüt olarak adlandırılır) krediyle Azure SQL veritabanı ücretleri için ödeme yapabilirsiniz. Ancak Azure ön ödeme kredisi, Azure Marketi 'nden dahil olan üçüncü taraf ürün ve hizmetlerine yönelik ücretler için ödeme yapmak üzere kullanamazsınız.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Azure portalında tahmini maliyetleri gözden geçirme

Azure SQL veritabanı oluşturma işlemi sırasında, işlem katmanının yapılandırması sırasında tahmini maliyetleri görebilirsiniz. 

Bu ekrana erişmek için **SQL veritabanı oluştur** sayfasının **temel bilgiler** sekmesinde **Veritabanını yapılandır** ' ı seçin. Aşağıdaki resimdeki bilgiler ve fiyatlandırma yalnızca örnek amaçlıdır:

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Azure portal Maliyet tahminini gösteren örnek":::



Azure aboneliğinizin harcama limiti varsa, Azure kredi miktarınızdan harcamanıza engel olur. Azure kaynaklarını oluştururken ve kullanırken kredileriniz kullanılır. Kredi sınırınıza ulaştığınızda, dağıttığınız kaynaklar bu fatura döneminin geri kalanı için devre dışı bırakılır. Kredi sınırınızı değiştiremezsiniz, ancak kaldırabilirsiniz. Harcama limitleri hakkında daha fazla bilgi için bkz. [Azure harcama limiti](../../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Maliyetleri izleme

Azure SQL veritabanı 'nı kullanmaya başladığınızda, portalda Tahmini maliyetleri görebilirsiniz. Maliyet tahminini gözden geçirmek için aşağıdaki adımları kullanın:

1. Azure portal oturum açın ve Azure SQL veritabanınızın kaynak grubuna gidin. Veritabanınıza gidip **genel bakış** bölümünde **kaynak grubu** ' nu seçerek kaynak grubunu bulabilirsiniz.
1. Menüsünde **Maliyet Analizi**' ni seçin.
1. **Birikmiş maliyetleri** görüntüleyin ve alt kısımdaki grafik ' i **hizmet adı** olarak ayarlayın. Bu grafik, geçerli SQL veritabanı maliyetlerinizin bir tahminini gösterir. Tüm sayfanın Azure SQL veritabanı ile maliyetlerini daraltmak için, **Filtre Ekle** ' yi seçin ve ardından **Azure SQL veritabanı**' nı seçin. Aşağıdaki resimdeki bilgiler ve fiyatlandırma yalnızca örnek amaçlıdır:

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Azure portal birikmiş maliyetleri gösteren örnek":::

Buradan, maliyetlerinizi kendiniz inceleyebilirsiniz. Farklı maliyet analizi ayarları hakkında daha fazla bilgi için bkz. [maliyetleri çözümlemeye başlama](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="create-budgets"></a>Bütçe oluşturma

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

Maliyetleri yönetmek için [bütçeler](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilir ve paydaşları harcama anormallikleri ve fazla harcama riskleri ile ilgili otomatik olarak bilgilendiren [uyarılar](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilirsiniz. Uyarılar, bütçe ve maliyet eşiklerine kıyasla harcamaya göre belirlenir. Bütçeler ve uyarılar Azure abonelikleri ve kaynak grupları için oluşturulur, bu nedenle genel maliyet izleme stratejisinin bir parçası olarak faydalıdır. 

İzlemekte daha fazla ayrıntı düzeyi istiyorsanız, Azure 'daki belirli kaynaklara veya hizmetlere yönelik filtrelerle bütçeler oluşturulabilir. Filtreler, yanlışlıkla ek para maliyeti sağlayan yeni kaynaklar oluşturmamasını sağlamaya yardımcı olur. Bir bütçe oluştururken filtre seçenekleri hakkında daha fazla bilgi için bkz. [Grup ve filtre seçenekleri](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Maliyet verilerini dışarı aktarma

Ayrıca, maliyet verilerinizi bir depolama hesabına [dışarı aktarabilirsiniz](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Bu, maliyetler için ek veri analizi yapmanız gerektiğinde veya başkalarının yararlı olması durumunda faydalıdır. Örneğin, bir finans ekipleri, Excel veya Power BI kullanarak verileri analiz edebilir. Maliyetlerinizi günlük, haftalık veya aylık bir zamanlamaya göre dışa aktarabilir ve özel bir tarih aralığı ayarlayabilirsiniz. Maliyet verilerini dışarı aktarmak, maliyet veri kümelerini almak için önerilen yoldur.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Azure SQL veritabanı için maliyetleri yönetmenin ve azaltmanın diğer yolları

Azure SQL veritabanı, uygulama gereksinimlerinize göre maliyetleri denetlemek için kaynakları yukarı veya aşağı ölçeklendirmenize de olanak sağlar. Ayrıntılar için bkz. [dinamik olarak veritabanı kaynaklarını ölçeklendirme](scale-resources.md).

Bir ile üç yıla kadar işlem kaynakları için bir ayırmaya ayırarak paradan tasarruf edin. Ayrıntılar için bkz. [ayrılmış kapasiteye sahip kaynaklar için maliyetleri kaydetme](reserved-capacity-overview.md).


## <a name="next-steps"></a>Sonraki adımlar

- [Azure maliyet yönetimi ile bulut yatırımınızın nasıl iyileştirileceği hakkında](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)bilgi edinin.
- [Maliyet Analizi](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)ile maliyetleri yönetme hakkında daha fazla bilgi edinin.
- [Beklenmeyen maliyetlerin nasıl önleneceği](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)hakkında bilgi edinin.
- [Maliyet yönetimi](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) destekli öğrenme kursuna katılın.