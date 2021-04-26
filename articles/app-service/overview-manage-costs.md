---
title: App Service maliyetlerini yönetmeyi planlayın
description: Azure portal maliyet analizini kullanarak Azure App Service maliyetlerini nasıl planlayacağınızı ve yöneteceğinizi öğrenin.
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: ada4c1991a57c8252247c9617e097dc82cb3b4a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593993"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>Azure App Service maliyetlerini planlama ve yönetme

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

Bu makalede, Azure App Service maliyetlerini nasıl planlayabileceğinizi ve yönetebileceğiniz açıklanır. İlk olarak, maliyetleri tahmin etmek üzere hizmet için herhangi bir kaynak eklemeden önce App Service maliyetlerini planlamaya yardımcı olması için Azure Fiyatlandırma hesaplayıcısı ' nı kullanırsınız. Daha sonra, Azure kaynaklarını eklerken Tahmini maliyetleri gözden geçirin. App Service kaynaklarını kullanmaya başladıktan sonra, bütçe ayarlamak ve maliyetleri izlemek için [maliyet yönetimi](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) özelliklerini kullanın. Ayrıca Tahmini maliyetleri gözden geçirebilir ve hareket etmek isteyebileceğiniz yerleri belirlemek için harcama eğilimlerini belirleyebilirsiniz. Azure App Service maliyetleri, Azure faturanızda bulunan Aylık maliyetlerin yalnızca bir parçasıdır. Bu makalede App Service maliyetlerinin nasıl planlanacağı ve yönetileceği açıklanmakta olsa da, üçüncü taraf hizmetler de dahil olmak üzere Azure aboneliğinizde kullanılan tüm Azure hizmetleri ve kaynakları için faturalandırılırsınız.

## <a name="relevant-costs-for-app-service"></a>App Service ilgili maliyetler

App Service maliyeti tahakkuk eden Azure altyapısında çalışır. Ek altyapının maliyeti tahakkuk edebileceğini anlamak önemlidir. Dağıtılan kaynaklarda değişiklik yaptığınızda bu maliyeti yönetmeniz gerekir.

### <a name="costs-that-accrue-with-azure-app-service"></a>Azure App Service ile tahakkuk eden maliyetler

App Service kullandığınız özelliğe bağlı olarak, aşağıdaki Maliyet tahakkuku kaynakları oluşturulabilir:

- **App Service planı**  App Service uygulamasını barındırmak için gereklidir.
- **Yalıtılmış katman**  App Service ortamı için bir [sanal ağ](../virtual-network/index.yml) gereklidir.
- **Yedekleme**  Yedeklemeleri yapmak için bir [depolama hesabı](../storage/index.yml) gereklidir.
- **Tanılama günlükleri**  [Depolama hesabı](../storage/index.yml) ' nı günlüğe kaydetme seçeneği olarak seçebilir veya [Azure Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)ile tümleştirebilirsiniz.
- **App Service sertifikaları**  Azure 'da satın aldığınız sertifikaların [Azure Key Vault](../key-vault/index.yml)içinde tutulması gerekir.

App Service yönelik diğer maliyet kaynakları şunlardır: (Ayrıntılar için [App Service fiyatlandırmasına](https://azure.microsoft.com/pricing/details/app-service/) bakın):

- [App Service etki alanları](manage-custom-dns-buy-domain.md)  Otomatik yenilemeyi etkinleştirirseniz aboneliğiniz yıllık olarak etki alanı kaydına göre ücretlendirilir.
- [App Service sertifikaları](configure-ssl-certificate.md#import-an-app-service-certificate)  Satın alma sırasında tek seferlik ücret. Güvenli hale getirmek için birden fazla alt etki alanı varsa, birden çok standart sertifika yerine bir joker sertifika satın alarak maliyeti azaltabilirsiniz.
- [IP tabanlı sertifika bağlamaları](configure-ssl-bindings.md#create-binding)  Bağlama, uygulama düzeyindeki bir sertifikada yapılandırılır. Her bağlamada maliyetler tahakkuk ettirilir. **Standart** katman ve yukarıdaki, ilk IP tabanlı bağlama ücretlendirilmez.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>Kaynak silme işleminden sonra tahakkuk olabilecek maliyetler

Bir App Service planındaki tüm uygulamaları sildiğinizde, plan, yapılandırılmış fiyatlandırma katmanına ve örnek sayısına göre ücretleri tahakkuk etmeye devam eder. İstenmeyen ücretlerden kaçınmak için planı silin veya **ücretsiz** katmana ölçeklendirin.

Azure App Service kaynaklarını sildikten sonra, ilgili Azure hizmetlerinden kaynaklar mevcut olmaya devam edebilir. Bunlar silinene kadar maliyetleri tahakkuk ettirmeye devam eder. Örnek:

- **Yalıtılmış** bir katman App Service planı Için oluşturduğunuz sanal ağ
- Yedeklemeleri veya tanılama günlüklerini depolamak için oluşturduğunuz depolama hesapları
- App Service sertifikalarını depolamak için oluşturduğunuz Key Vault
- Tanılama günlüklerini göndermek için oluşturduğunuz günlük analitik ad alanları
- Henüz geçmemiş App Service için [örnek veya damga ayırmaları](#azure-reservations)

### <a name="using-monetary-credit-with-azure-app-service"></a>Azure App Service ile parasal krediyi kullanma

Azure ön ödeyiniz (daha önce parasal taahhüt olarak adlandırılır) krediyle Azure App Service ücretleri için ödeme yapabilirsiniz. Ancak Azure ön ödeme kredisi, Azure Marketi 'nden farklı olan üçüncü taraf ürün ve hizmetlerine yönelik ücretlerle ödeme yapmak için kullanamazsınız.

## <a name="estimate-costs"></a>Maliyetleri tahmin etme

[Azure fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/)kullanarak App Service maliyetinizi tahmin etmenin ve iyileştirmenin kolay bir yolu.

Fiyatlandırma hesaplayıcısını kullanmak için, **Ürünler** sekmesinden **App Service** ' a tıklayın. Ardından, hesap makinesiyle çalışmak için aşağı kaydırın. Aşağıdaki ekran görüntüsü bir örnektir ve geçerli fiyatlandırmayı yansıtmaz.

![Azure Fiyatlandırma Hesaplayıcı 'da tahmini maliyeti gösteren örnek](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>Azure portalında tahmini maliyetleri gözden geçirme

Bir App Service uygulaması veya bir App Service planı oluşturduğunuzda, tahmini maliyetleri görebilirsiniz.

Bir uygulama oluşturmak ve tahmini fiyatı görüntülemek için:

1. Oluştur sayfasında, **App Service plan**' a kaydırın ve **Yeni oluştur**' a tıklayın.
1. Bir ad belirtip **Tamam**' a tıklayın.
1. **SKU ve boyut**' un yanında **boyutu Değiştir**' e tıklayın.
1. Özette gösterilen tahmini fiyatı gözden geçirin. Aşağıdaki ekran görüntüsü bir örnektir ve geçerli fiyatlandırmayı yansıtmaz.

    ![Portalda her fiyatlandırma katmanının tahmini maliyetini gözden geçirin](media/overview-manage-costs/pricing-estimates.png)

Azure aboneliğinizin harcama limiti varsa, Azure kredi miktarınızdan harcamanıza engel olur. Azure kaynaklarını oluştururken ve kullanırken kredileriniz kullanılır. Kredi sınırınıza ulaştığınızda, dağıttığınız kaynaklar bu fatura döneminin geri kalanı için devre dışı bırakılır. Kredi sınırınızı değiştiremezsiniz, ancak kaldırabilirsiniz. Harcama limitleri hakkında daha fazla bilgi için bkz. [Azure harcama limiti](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="optimize-costs"></a>Maliyetleri iyileştirin

Temel düzeyde App Service uygulamalar, bunları barındıran App Service planına göre ücretlendirilir. App Service dağıtımınız ile ilişkili maliyetler birkaç ana etkene bağlıdır:

- **Fiyatlandırma katmanı**  App Service planının SKU 'SU olarak da bilinir. Daha yüksek katmanlar, daha fazla CPU çekirdeği, bellek, depolama alanı veya özellik veya bileşim sağlar.
- **Örnek sayısı**  adanmış katmanları (temel ve üstü) ölçeklendirilebilir ve her bir örnek tahakkuk eden örnek ücretlerine göre genişletilebilir.
- **Damga ücreti**  Yalıtılmış katmanda, uygulama veya çalışan örneklerinin kaç tane barındırıldığından bağımsız olarak App Service ortamınızda sabit bir ücret tahakkuk edilir.

App Service planı, birden fazla uygulamayı barındırabilir. Dağıtımınıza bağlı olarak, bir App Service planına daha fazla uygulama barındıran maliyetleri kaydedebilirsiniz (yani uygulamalarınızı daha az App Service planlarda barınıyor).

Ayrıntılar için bkz. [App Service planına genel bakış](overview-hosting-plans.md)

### <a name="non-production-workloads"></a>Üretim dışı iş yükleri

Düşük veya minimum maliyet tahakkuk ederken App Service veya çözümünüzü test etmek için, paylaşılan örneklerde barındırılan, **ücretsiz** ve **paylaşılan** iki giriş düzeyi fiyatlandırma katmanını kullanarak başlayabilirsiniz. Uygulamanızı daha iyi performansa sahip adanmış örneklerde test etmek için hem Windows hem de Linux uygulamalarını destekleyen **temel** katmana yükseltebilirsiniz. 

> [!NOTE]
> **Azure geliştirme ve test fiyatlandırması**  Daha yüksek Katmanlar ( **yalıtılmış** hariç tüm katmanlar) gerektiren ön üretim iş yüklerini test etmek Için, Visual Studio aboneleri Ayrıca önemli ıskontolar sunan [Azure geliştirme ve test fiyatlandırmasının](https://azure.microsoft.com/pricing/dev-test/)avantajlarından yararlanabilir.
>
> Hem **ücretsiz** hem de **paylaşılan** katman ve Azure dev/test fiyatlandırma indirimleri, malı olarak desteklenen bir SLA 'yı taşımaz.

### <a name="production-workloads"></a>Üretim iş yükleri

Üretim iş yükleri, adanmış **Standart** fiyatlandırma katmanının veya yukarıdaki öneriyle gelir. Fiyat daha yüksek katmanlarla aynı zamanda daha fazla bellek ve depolama ve daha yüksek performanslı donanımlar sunarak işlem örneği başına daha yüksek uygulama yoğunluğu sağlar. Bu, aynı uygulama sayısı için daha düşük örnek sayısına ve bu nedenle daha düşük maliyetli bir değer sağlar. Aslında **Premium v3** (**yalıtılmış** olmayan en yüksek katman), uygulamanızı ölçekli olarak sunmanın en uygun maliyetli yoludur. Tasarruflara eklemek için [Premium v3 ayırmaları](#azure-reservations)hakkında derin indirimler elde edebilirsiniz.

> [!NOTE]
> **Premium v3** hem Windows kapsayıcıları hem de Linux kapsayıcıları destekler. 

İstediğiniz fiyatlandırma katmanını seçtikten sonra, boşta kalan örnekleri en aza indirmelisiniz. Bir genişleme dağıtımında, az kullanılan işlem örnekleri üzerinde paraya yatırım yapabilirsiniz. **Standart** katmanda ve yukarıda bulunan [Otomatik ölçeklendirmeyi yapılandırmanız](../azure-monitor/autoscale/autoscale-get-started.md)gerekir. Ölçek Genişletme zamanlamalarının yanı sıra ölçüm tabanlı ölçek genişletme kuralları oluşturarak yalnızca, belirli bir zamanda gerçekten gereken örneklere ödeme yaparsınız.

### <a name="azure-reservations"></a>Azure Rezervasyonlar

Bir yıl veya daha fazla süreyle bilinen en az sayıda işlem örneğini kullanmayı planlıyorsanız, **Premium v3** katmanından faydalanır ve bu örnekleri 1 yıl veya 3 yıllık artışlarla ayırarak örnek maliyetini büyük ölçüde azaltabilirsiniz. Aylık maliyet tasarrufları, örnek başına %55 kadar olabilir. İki tür ayırma mümkündür:

- **Windows (veya platform belirsiz)**  , Aboneliğinizdeki Windows veya Linux örneklerine uygulanabilir.
- **Linux 'a özgü**  Yalnızca aboneliğinizdeki Linux örnekleri için geçerlidir.

Ayrılmış örnek fiyatlandırması, ayırmış olduğunuz örnek sayısına kadar aboneliğinizdeki ilgili örneklere uygulanır. Ayrılmış örnekler, bir faturalandırma sorunudur ve belirli işlem örneklerine bağlı değildir. Rezervasyon süresi boyunca herhangi bir noktada ayırdığınız sayıdan daha az örnek çalıştırırsanız, ayrılmış örnekler için yine de ödeme yaparsınız. Rezervasyon süresi boyunca herhangi bir noktada ayırmaktan daha fazla örnek çalıştırırsanız, ek örnekler için normal tahakkuk eden maliyeti ödeyin.

**Yalıtılmış** katman (App Service ortamı), daha düşük fiyatlandırmayla 1 yıllık ve 3 yıllık rezervasyonları da destekler. Daha fazla bilgi için bkz. [rezervasyon iskontolarının Azure App Service Için nasıl uygulanacağı](../cost-management-billing/reservations/reservation-discount-app-service.md).

## <a name="monitor-costs"></a>Maliyetleri izleme

App Service ile Azure kaynaklarını kullanırken maliyetleriniz vardır. Azure Kaynak kullanımı birim maliyetleri zaman aralıklarına göre farklılık gösterir (saniye, dakika, saat ve gün). App Service kullanımı başladığı anda maliyetler ücretlendirilir ve [Maliyet analizinde](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)maliyetleri görebilirsiniz.

Maliyet analizini kullandığınızda, grafikler ve tablolardaki App Service maliyetlerini farklı zaman aralıkları için görüntüleyebilirsiniz. Bazı örnekler güne, geçerli ve önceki aya ve yıla göre yapılır. Ayrıca bütçeleri ve tahmin edilen maliyetleri de görüntüleyebilirsiniz. Zamana göre daha uzun görünümlere geçiş yapmak harcama eğilimlerini belirlemenize yardımcı olabilir. Ve fazla harcama oluşmuş olabileceğini görürsünüz. Bütçeleri oluşturduysanız, nerede aşıldığınızı da kolayca görebilirsiniz.
    
Maliyet analizinde App Service maliyetlerini görüntülemek için:

1. Azure portalında oturum açın.
2. Azure portal kapsamı açın ve menüden **Maliyet Analizi** ' ni seçin. Örneğin, **abonelikler**' e gidin, listeden bir abonelik seçin ve ardından menüde  **Maliyet Analizi** ' ni seçin. Maliyet analizinde farklı bir kapsama geçiş yapmak için **kapsam** ' ı seçin.
3. Varsayılan olarak, hizmetlerin maliyeti ilk halka grafiğinde gösterilir. App Service etiketli grafikteki alanı seçin.

Maliyet analizini ilk kez açtığınızda gerçek aylık maliyetler gösterilir. İşte tüm aylık kullanım maliyetlerini gösteren bir örnek.

![Bir aboneliğin birikmiş maliyetlerini gösteren örnek](media/overview-manage-costs/all-costs.png)

App Service gibi tek bir hizmetin maliyetlerini daraltmak için, **Filtre Ekle** ' yi ve ardından **hizmet adı**' nı seçin. Ardından **App Service**' yi seçin.

Yalnızca App Service maliyetlerini gösteren bir örnek aşağıda verilmiştir.

![ServiceName için birikmiş maliyetleri gösteren örnek](media/overview-manage-costs/service-specific-costs.png)

Önceki örnekte, hizmet için geçerli maliyeti görürsünüz. Azure bölgelerine (konumlar) göre maliyetler ve kaynak grubuna göre App Service maliyetleri de gösterilir. Buradan, maliyetlerinizi kendiniz inceleyebilirsiniz.

## <a name="create-budgets"></a>Bütçe oluşturma

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

Maliyetleri yönetmek için [bütçeler](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilir ve paydaşları harcama anormallikleri ve fazla harcama riskleri ile ilgili otomatik olarak bilgilendiren [uyarılar](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilirsiniz. Uyarılar, bütçe ve maliyet eşiklerine kıyasla harcamaya göre belirlenir. Bütçeler ve uyarılar Azure abonelikleri ve kaynak grupları için oluşturulur, bu nedenle genel maliyet izleme stratejisinin bir parçası olarak faydalıdır. 

İzlemekte daha fazla ayrıntı düzeyi istiyorsanız, Azure 'daki belirli kaynaklara veya hizmetlere yönelik filtrelerle bütçeler oluşturulabilir. Filtreler, yanlışlıkla ek para maliyeti sağlayan yeni kaynaklar oluşturmamasını sağlamaya yardımcı olur. Bir bütçe oluşturduğunuzda kullanılabilecek filtre seçenekleri hakkında daha fazla bilgi için bkz. [Grup ve filtre seçenekleri](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Maliyet verilerini dışarı aktarma

Ayrıca, maliyet verilerinizi bir depolama hesabına [dışarı aktarabilirsiniz](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Bu, daha fazla maliyet için daha fazla veri analizi yapmanıza gerek duyduğunuzda veya başkaları için yararlıdır. Örneğin, bir Finans ekibi, Excel veya Power BI kullanarak verileri analiz edebilir. Maliyetlerinizi günlük, haftalık veya aylık bir zamanlamaya göre dışa aktarabilir ve özel bir tarih aralığı ayarlayabilirsiniz. Maliyet verilerini dışarı aktarmak, maliyet veri kümelerini almak için önerilen yoldur.

## <a name="next-steps"></a>Sonraki adımlar

- Fiyatlandırma ile Azure depolama ile nasıl çalıştığı hakkında daha fazla bilgi edinin. [App Service fiyatlandırmasına](https://azure.microsoft.com/pricing/details/app-service/)bakın.
- [Azure maliyet yönetimi ile bulut yatırımınızın nasıl iyileştirileceği hakkında](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)bilgi edinin.
- [Maliyet Analizi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)ile maliyetleri yönetme hakkında daha fazla bilgi edinin.
- [Beklenmeyen maliyetlerin nasıl önleneceği](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)hakkında bilgi edinin.
- [Maliyet yönetimi](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) destekli öğrenme kursuna katılın.

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->