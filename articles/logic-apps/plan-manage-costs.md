---
title: Azure Logic Apps maliyetlerini yönetmeyi planlayın
description: Azure portal maliyet analizini kullanarak Azure Logic Apps maliyetlerini nasıl planlayacağınızı ve yöneteceğinizi öğrenin
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 03/24/2021
ms.openlocfilehash: ec2e1098df4c21704ee7c17852b893630cd3fd27
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761826"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Azure Logic Apps maliyetlerini planlayın ve yönetin

Bu makale, Azure Logic Apps maliyetlerini planlayıp yönetmenize yardımcı olur. Bu hizmeti kullanarak herhangi bir kaynak oluşturmadan veya eklemeden önce, Azure fiyatlandırma hesaplayıcısını kullanarak maliyetlerinizi tahmin edin. Logic Apps kaynaklarını kullanmaya başladıktan sonra, [Azure maliyet yönetimi](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)'ni kullanarak bütçeleri ayarlayabilir ve maliyetleri izleyebilirsiniz. Çalışmak isteyebileceğiniz yerleri belirlemek için tahmin edilen maliyetleri gözden geçirebilir ve harcama eğilimlerini izleyebilirsiniz.

Logic Apps maliyetlerinin yalnızca Azure faturanızda bulunan Aylık maliyetlerin bir parçası olduğunu aklınızda bulundurun. Bu makalede Logic Apps maliyetlerinin nasıl tahmin edilmesi ve yönetileceği açıklanmakta olsa da, tüm üçüncü taraf hizmetler de dahil olmak üzere Azure aboneliğinizde kullanılan tüm Azure hizmetleri ve kaynakları için faturalandırılırsınız. Logic Apps maliyetlerini yönetme hakkında bilgi sahibi olduktan sonra, aboneliğinizde kullanılan tüm Azure hizmetlerinin maliyetlerini yönetmek için benzer yöntemler uygulayabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure maliyet yönetimi](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) , çoğu Azure hesap türünü destekler. Desteklenen tüm hesap türlerini görüntülemek için bkz. [maliyet yönetimi verilerini anlama](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Maliyet verilerini görüntülemek için Azure hesabınızda en azından okuma yetkisine sahip olmanız gerekir.

Azure Maliyet Yönetimi verilerine erişim atama hakkında daha fazla bilgi için bkz. [Verilere erişim atama](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>Faturalandırma modelini anlama

Azure Logic Apps, yeni kaynakları dağıtırken [maliyeti tahakkuk](https://azure.microsoft.com/pricing/details/logic-apps/) eden Azure altyapısında çalışır. [Logic Apps hizmeti için faturalandırma modelini Ilgili Azure kaynaklarıyla](logic-apps-pricing.md)anladığınızdan emin olun ve dağıtılan kaynaklarda değişiklik yaparken bu bağımlılıklar nedeniyle maliyetleri yönetin.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Genellikle Azure Logic Apps ile tahakkuk eden maliyetler

Logic Apps hizmeti, oluşturduğunuz ve kullandığınız kaynaklara göre farklı fiyatlandırma modelleri uygular:

* Çok kiracılı Logic Apps hizmetinde oluşturup çalıştırdığınız mantıksal uygulama kaynakları bir [Tüketim fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md#consumption-pricing)kullanır.

* Bir [tümleştirme hizmeti ortamında (ıSE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) oluşturduğunuz ve çalıştırdığınız mantıksal uygulama kaynakları [sabit bir fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md#fixed-pricing)kullanır.

Mantıksal uygulamalarla kullanım için oluşturduğunuz sırada maliyetleri karşılayan diğer kaynaklar şunlardır:

* [Tümleştirme hesabı](../logic-apps/logic-apps-pricing.md#integration-accounts) , B2B tümleştirmelerini oluşturmak için oluşturduğunuz ve Logic Apps 'e bağlanan ayrı bir kaynaktır. Tümleştirme hesapları, hızının, kullandığınız tümleştirme hesabı türü veya *katmanını* temel alan [sabit bir fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md#integration-accounts) kullanır.

* [Ise](../logic-apps/logic-apps-pricing.md#fixed-pricing) , bir sanal ağdaki kaynaklara doğrudan erişmesi gereken Logic Apps için dağıtım konumu olarak oluşturduğunuz ayrı bir kaynaktır. Sesleri, oluşturduğunuz ıSE SKU 'suna ve diğer ayarları temel alan [sabit bir fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md#fixed-pricing) kullanır.

* [Özel bağlayıcı](../logic-apps/logic-apps-pricing.md#consumption-pricing) , mantıksal uygulamalarınızda kullanabilmeniz için önceden oluşturulmuş bağlayıcı bulunmayan REST API için oluşturduğunuz ayrı bir kaynaktır. Özel bağlayıcı yürütmeleri, bunları bir ıSE içinde kullandığınız durumlar haricinde bir [Tüketim fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md#consumption-pricing) kullanır.

* Çok kiracılı Logic Apps hizmetinde [veri saklama ve depolama tüketimi](../logic-apps/logic-apps-pricing.md#data-retention) , maliyetleri [sabit bir fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md#fixed-pricing)kullanarak tahakkuk eder. Örneğin, çalıştırma geçmişinden girişler ve çıkışlar, mantıksal uygulamanızdan bağımsız olarak oluşturduğunuz, yönettiğiniz ve erişebileceğiniz depolama kaynaklarından farklı olarak, arka planda tutulur.

  Bir ıSE 'de veri saklama ve depolama tüketimi ücret ödemezsiniz.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>Kaynak silme işleminin ardından maliyetler tahakkuk edebilir

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

Bir mantıksal uygulamayı sildikten sonra, Logic Apps hizmeti yeni iş akışı örnekleri oluşturmaz veya çalıştırmaz. Ancak, tüm devam eden ve bekleyen çalıştırmalar tamamlanana kadar devam eder. Bu çalıştırmaların sayısına bağlı olarak, bu işlem biraz zaman alabilir. Daha fazla bilgi için bkz. [mantıksal uygulamaları yönetme](manage-logic-apps-with-azure-portal.md#delete-logic-apps).

Bir mantıksal uygulamayı sildikten sonra bu kaynaklara sahipseniz, bu kaynaklar mevcut olmaya devam eder ve siz silene kadar maliyetleri tahakkuk ettirmeye devam eder:

* Bu kaynaklara bağlanan mantıksal uygulamadan bağımsız olarak oluşturduğunuz ve yönettiğiniz Azure kaynakları (örneğin, Azure işlev uygulamaları, Olay Hub 'ları, olay grupları vb.)

* Tümleştirme hesapları

* Tümleştirme hizmeti ortamları (sesleri)

  [BIR ıSE silerseniz](ise-manage-integration-service-environment.md#delete-ise), ilişkili Azure sanal ağı, alt ağları ve diğer ilgili kaynaklar mevcut olmaya devam eder. ISE 'yi sildikten sonra, sanal ağ veya alt ağları silmeyi deneyebilmeniz için belirli bir saate kadar beklemeniz gerekebilir.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Azure Logic Apps ile parasal krediyi kullanma

EA parasal taahhüt krediniz ile Azure Logic Apps ücretleri için ödeme yapabilirsiniz. Ancak, Azure Marketi 'nden dahil olmak üzere üçüncü taraf ürün ve hizmetlerine yönelik ücretler için ödeme yapmak üzere EA parasal taahhüt kredisi kullanamazsınız.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>Maliyetleri tahmin etme

Azure Logic Apps olan kaynakları oluşturmadan önce [Azure fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/)kullanarak maliyetlerinizi tahmin edin. Daha fazla bilgi için [Azure Logic Apps Için fiyatlandırma modelini](../logic-apps/logic-apps-pricing.md)inceleyin.

1. [Azure Fiyatlandırma Hesaplayıcı sayfasında](https://azure.microsoft.com/pricing/calculator/), sol menüden **tümleştirme**  >  **Azure Logic Apps**' yi seçin.

   !["Azure Logic Apps" seçiliyken Azure fiyatlandırma hesaplayıcısını gösteren ekran görüntüsü.](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Azure Logic Apps fiyatlandırma hesaplayıcısını görüntüleyebilmek için sayfayı aşağı kaydırın. Azure Logic Apps doğrudan ilişkili Azure kaynakları için çeşitli bölümlerde, kullanmayı planladığınız kaynak sayısını ve bu kaynakları kullanabileceğiniz Aralık sayısını girin.

   Bu ekran görüntüsünde, hesaplayıcı kullanılarak örnek maliyet tahmini gösterilmektedir:

   ![Azure Fiyatlandırma Hesaplayıcı 'da tahmini maliyeti gösteren örnek](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Yeni ilgili kaynakları oluştururken ve kullanırken maliyet tahminlerinizi güncelleştirmek için bu Hesaplayıcı 'a dönün ve bu kaynakları burada güncelleştirin.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Bütçeler ve uyarılar oluşturma

Azure hesabınız veya aboneliğiniz için maliyetleri proaktif bir şekilde yönetmenize yardımcı olması için [Azure maliyet yönetimi ve faturalandırma](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) hizmeti ve yeteneklerini kullanarak [bütçeler](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ve [Uyarılar](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilirsiniz.  Bütçeler ve uyarılar Azure abonelikleri ve kaynak grupları için oluşturulur, bu nedenle genel maliyet izleme stratejisinin bir parçası olarak faydalıdır.

Bütçe ve maliyet eşiklerine kıyasla, uyarılar, harcama ve fazla harcama riskleri konusunda otomatik olarak katılımcıları bilgilendirir. İzlemekte daha fazla ayrıntı düzeyi istiyorsanız, Azure 'da belirli kaynaklar veya hizmetler için filtre kullanan bütçeler da oluşturabilirsiniz. Filtreler, yanlışlıkla daha fazla ücret ödediğinizden emin olmanıza yardımcı olur. Filtre seçenekleri hakkında daha fazla bilgi için bkz. [Grup ve filtre seçenekleri](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Maliyetleri izleme

Kaynak kullanım birimi maliyetleri, saniye, dakika, saat ve gün gibi zaman aralıklarına veya bayt, megabayt vb. gibi birim kullanımına göre değişir. Bazı örnekler güne, geçerli ve önceki aya ve yıla göre yapılır. Zamana göre daha uzun görünümlere geçiş yapmak harcama eğilimlerini belirlemenize yardımcı olabilir. Maliyet Analizi özelliklerini kullandığınızda, maliyetleri çeşitli zaman aralıklarında grafikler ve tablolar olarak görüntüleyebilirsiniz. Bütçeler ve maliyet tahminleri oluşturduysanız, bütçelerin nerede aşıldığını ve fazla harcama oluşmuş olabileceğini de kolayca bulabilirsiniz.

Azure 'da kullanarak oluşturan veya başlatılan kaynakların maliyetlerini başlattıktan sonra, bu maliyetleri şu yollarla gözden geçirebilir ve izleyebilirsiniz:

* Azure Izleyici ['yi kullanarak mantıksal uygulama yürütmelerini ve depolama tüketimini izleme](#monitor-billing-metrics)

* [Azure maliyet yönetimi ve faturalandırma](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) kullanarak [maliyet analizini](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) çalıştırma

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Mantıksal uygulama yürütmelerini ve depolama tüketimini izleme

Azure Izleyici 'yi kullanarak, belirli bir mantıksal uygulama için bu ölçümleri görüntüleyebilirsiniz:

* Faturalanabilir eylem yürütmeleri
* Faturalanabilir tetikleyici yürütmeleri
* Yerel işlem yürütmeleri için faturalandırma kullanımı
* Standart bağlayıcı yürütmeleri için faturalandırma kullanımı
* Depolama alanı tüketimi için faturalama kullanımı
* Toplam faturalandırılabilir yürütmeler

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Yürütme ve depolama alanı tüketim ölçümlerini görüntüleme

1. Azure portal, mantıksal uygulamanızı bulun ve açın. Mantıksal uygulamanızın menüsünde, **izleme** altında **ölçümler**' i seçin.

1. Sağ taraftaki bölmede, **grafik başlığı** altında, ölçüm çubuğunda **ölçüm** listesini açın ve istediğiniz ölçümü seçin.

   > [!NOTE]
   > Depolama alanı tüketimi, mantıksal uygulamanızın kullandığı ve faturalandırılan depolama birimi (GB) sayısı olarak ölçülür. Depolama alanında 500 MB 'tan az kullanım kullanan çalıştırmalar izleme görünümünde görünmeyebilir, ancak hala faturalandırılır.

   ![Açık "ölçüm" listesiyle ölçümler bölmesini gösteren ekran görüntüsü.](./media/logic-apps-pricing/select-metric.png)

1. Bölmenin sağ üst köşesinde istediğiniz zaman dilimini seçin.

1. Diğer depolama tüketimi verilerini görüntülemek için, mantıksal uygulamanızın çalıştırma geçmişinde özellikle eylem giriş ve çıkış boyutlarını görmek için [aşağıdaki adımları izleyin](#view-input-output-sizes).

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>Çalışma geçmişinde eylem giriş ve çıkış boyutlarını görüntüleme

1. Azure portal, mantıksal uygulamanızı bulun ve açın.

1. Mantıksal uygulamanızın menüsünde **genel bakış**' ı seçin.

1. Sağ taraftaki bölmede, çalışma **geçmişi** altında, görüntülemek istediğiniz girişleri ve çıkışları içeren çalıştırmayı seçin.

1. **Mantıksal uygulama çalıştırma** altında, **Çalıştır ayrıntıları**' nı seçin.

1. **Mantıksal uygulama çalıştırma ayrıntıları** bölmesinde, her eylemin durumunu ve süresini listeleyen eylemler tablosunda, görüntülemek istediğiniz eylemi seçin.

1. **Mantıksal uygulama eylemi** bölmesinde, bu eylemin giriş ve çıkışları için boyutları bulun. **Girişler bağlantı** ve **çıkış bağlantısı** altında, bu giriş ve çıkışların bağlantılarını bulun.

   > [!NOTE]
   > Döngüler için, yalnızca üst düzey eylemler, giriş ve çıkışları için boyutları gösterir. İç içe döngüler, girişler ve çıkışlar içindeki eylemler için sıfır boyut ve bağlantı yok gösterilmektedir.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Azure maliyet yönetimi ve faturalandırma kullanarak maliyet analizini çalıştırma

Azure aboneliği gibi belirli bir kapsama dayalı Logic Apps hizmetinin maliyetlerini gözden geçirmek için [Azure maliyet yönetimi ve faturalandırma](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)' de [Maliyet Analizi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) özelliklerini kullanabilirsiniz.

1. Azure portal, Azure aboneliğiniz gibi istediğiniz kapsamı açın. Sol taraftaki menüden **maliyet yönetimi** altında **Maliyet Analizi**' ni seçin.

   Maliyet Analizi bölmesini ilk açtığınızda, en üstteki grafik geçerli ay için abonelikteki tüm hizmetlerde gerçek ve tahmini kullanım maliyetlerini gösterir.

   ![Bir abonelikteki gerçek ve tahmini maliyetler için örnek içeren Azure portal ve maliyet analizi bölmesini gösteren ekran görüntüsü.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > Kapsamları değiştirmek için, **Maliyet Analizi** bölmesinde, filtreler çubuğundan **kapsam** filtresini seçin. **Kapsam seçin** bölmesinde istediğiniz kapsama geçin.

   Aşağıda, halka grafiklerde Azure Hizmetleri, Azure bölgesine (konum) ve kaynak grubuna göre geçerli maliyetler gösterilmektedir.

   ![Hizmetler, bölgeler ve kaynak grupları için örnek halka grafiklerle birlikte Azure portal ve maliyet analizi bölmesini gösteren ekran görüntüsü.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Grafiği bir hizmet veya kaynak gibi belirli bir alanla filtrelemek için filtreler çubuğunda **Filtre Ekle**' yi seçin.

1. Sol taraftaki listeden, filtre türünü (örneğin, **hizmet adı**) seçin. Sağ taraftaki listeden filtre ' yi, örneğin **Logic Apps**' i seçin. İşiniz bittiğinde yeşil onay işaretini seçin.

   ![Filtre seçimleriyle Azure portal ve maliyet analizi bölmesini gösteren ekran görüntüsü.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Örneğin, Logic Apps hizmeti için sonuç aşağıda verilmiştir:

   !["Logic Apps" ile filtrelenmiş sonuçlarla Azure portal ve maliyet analizi bölmesini gösteren ekran görüntüsü.](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Maliyet verilerini dışarı aktarma

Maliyetlerde daha fazla veri analizi yapmanız gerektiğinde, maliyet verilerini bir depolama hesabına [dışarı aktarabilirsiniz](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Örneğin, bir Finans ekibi Excel veya Power BI kullanarak bu verileri çözümleyebilir. Maliyetlerinizi günlük, haftalık veya aylık bir zamanlamaya göre dışa aktarabilir ve özel bir tarih aralığı ayarlayabilirsiniz. Maliyet verilerini dışarı aktarmak, maliyet veri kümelerini almak için önerilen yoldur.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Maliyetleri yönetmenin ve azaltmanın diğer yolları

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

Logic APS ve ilgili kaynaklarınızın maliyetlerini düşürmenize yardımcı olmak için şu seçenekleri deneyin:

* Mümkünse, [yerleşik Tetikleyiciler ve eylemler](../connectors/built-in.md)kullanın; Bu işlem, [yönetilen bağlayıcı tetikleyicilerine ve eylemlerine](../connectors/managed.md)göre yürütme başına çalıştırılması gereken maliyeti azaltır.

  Örneğin, [http eylemini](../connectors/connectors-native-http.md) kullanarak veya [Azure işlevleri hizmetini](../azure-functions/functions-overview.md) kullanarak oluşturduğunuz bir Işlevi çağırarak ve [yerleşik Azure işlevleri eylemini](../logic-apps/logic-apps-azure-functions.md)kullanarak diğer kaynaklara erişirken maliyetleri azaltabilirsiniz. Ancak Azure Işlevleri 'nin kullanılması de maliyet doğurur, bu nedenle seçeneklerinizi karşılaştırdığınızdan emin olun.

* Bir iş akışını çalıştırmak için [kesin tetikleyici koşulları belirtin](logic-apps-workflow-actions-triggers.md#trigger-conditions) .

  Örneğin, bir tetikleyicinin yalnızca hedef Web sitesi bir iç sunucu hatası döndürdüğünü tetikleyebelirtebilirsiniz. Tetikleyicinin JSON tanımında, `conditions` tetikleyicisinin durum koduna başvuran bir koşul belirtmek için özelliğini kullanın.

* Bir tetikleyicide bir yoklama sürümü ve Web kancası sürümü varsa, olayı düzenli olarak denetlemek yerine, belirtilen olayın tetiklenmesi için bekleyen Web kancası sürümünü deneyin.

* Farklı bir hizmet aracılığıyla mantıksal uygulamanızı çağırın, böylece tetikleyici yalnızca iş akışı çalıştığında tetiklenir.

  Örneğin, Azure Işlevleri hizmetini kullanarak oluşturduğunuz ve çalıştırdığınız bir işlevden mantıksal uygulamanızı çağırabilirsiniz. Örneğin, bkz. [Azure işlevleri ve Azure Service Bus kullanarak Logic Apps 'ı çağırma veya tetikleme](logic-apps-scenario-function-sb-trigger.md).

* Sürekli çalıştırmak zorunda olmayan [Logic Apps 'ı devre dışı bırakın](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) veya artık ihtiyacınız olmayan [mantıksal uygulamaları silin](manage-logic-apps-with-azure-portal.md#delete-logic-apps) . Mümkünse, sürekli olarak etkin olması gerekmeyen diğer kaynakları devre dışı bırakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Maliyet Yönetimi ile bulut yatırımınızı iyileştirme](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Maliyet analizini kullanarak maliyetleri yönetme](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Beklenmeyen maliyetleri engelleme](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Maliyet yönetimi](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) destekli öğrenme kursuna katılın