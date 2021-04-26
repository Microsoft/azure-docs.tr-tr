---
title: Azure Izleyici ile Azure kaynaklarını izleme | Microsoft Docs
description: Azure Izleyici kullanarak Azure 'daki kaynaklardan izleme verilerinin nasıl toplanacağını ve analiz edileceğini açıklar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: cb778d826ef094d71fd27f3c10bc1f2c292baa47
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862408"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Azure İzleyici ile Azure kaynaklarını izleme
Azure kaynaklarına bağlı kritik Uygulamalarınız ve iş süreçleriniz olduğunda, bu kaynakları kullanılabilirlik, performans ve işlem için izlemek istersiniz. Bu makalede, Azure kaynakları tarafından oluşturulan izleme verileri ve bu verileri çözümlemek ve uyarmak için Azure Izleyici 'nin özelliklerini nasıl kullanabileceğiniz açıklanır.

> [!IMPORTANT]
> Bu makale Azure Izleyici kullanan tüm Azure hizmetleri için geçerlidir. VM 'Ler ve App Service dahil işlem kaynakları, burada açıklanan izleme verilerinin aynısını oluşturur ve ayrıca Günlükler ve ölçümler oluşturabilen bir konuk işletim sistemine sahiptir. Bu verilerin toplanması ve çözümlenmesi hakkında ayrıntılı bilgi edinmek için bu hizmetlere ilişkin izleme belgelerine bakın.

## <a name="what-is-azure-monitor"></a>Azure İzleyici nedir?
Azure Izleyici, Azure 'da, diğer bulutlardaki ve Şirket içindeki kaynaklara ek olarak Azure kaynaklarınızı izlemeye yönelik eksiksiz bir özellik kümesi sunan tam bir yığın izleme hizmetidir. [Azure izleyici veri platformu](../data-platform.md) , tüm izleme araçları kümesi kullanılarak analiz edilebilecekleri [günlüklere](../logs/data-platform-logs.md) ve [ölçümlere](../essentials/data-platform-metrics.md) veri toplar. Azure izleyici tarafından [izlenen](../monitor-reference.md)Azure izleyici tarafından izlenebilecekleri uygulamaların ve hizmetlerin tüm listesini görün.

Bir Azure kaynağı oluşturandan itibaren Azure Izleyici etkinleştirilir ve [Azure Portal görüntüleyip çözümleyebilmeniz](#monitoring-in-the-azure-portal)gereken ölçümleri ve etkinlik günlüklerini toplamaya başlar. Bazı yapılandırmalar ile ek izleme verileri toplayabilir ve ek özellikleri etkinleştirebilirsiniz. Yapılandırma gereksinimleriyle ilgili ayrıntılı bilgi için aşağıdaki [Izleme verilerini](#monitoring-data) inceleyin.


## <a name="costs-associated-with-monitoring"></a>İzleme ile ilişkili maliyetler
Varsayılan olarak toplanan izleme verilerinin çözümlenmesi için bir maliyet yoktur. Bu, aşağıdakileri içerir:

- Platform ölçümleri toplanıyor ve bunları Ölçüm Gezgini ile analiz etme.
- Etkinlik günlüğü toplanıyor ve Azure portal analiz ediliyor.
- Etkinlik günlüğü uyarı kuralı oluşturuluyor.

Günlükleri ve ölçümleri toplamak ve dışarı aktarmak için Azure Izleyici maliyeti yoktur, ancak hedefle ilişkili ilişkili maliyetler olabilir:

- Log Analytics çalışma alanında Günlükler ve ölçümler toplanırken veri alımı ve bekletme ile ilişkili maliyetler. [Log Analytics için bkz. Azure izleyici fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/).
- Azure depolama hesabına Günlükler ve ölçümler toplanırken veri depolama ile ilişkili maliyetler. Bkz. [BLOB depolama Için Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Günlükler ve ölçümler Azure Event Hubs iletilirken, Olay Hub 'ı akışıyla ilişkili maliyetler. Bkz. [Azure Event Hubs fiyatlandırması](https://azure.microsoft.com/pricing/details/event-hubs/).

Aşağıdakiler ile ilişkili Azure Izleyici maliyeti olabilir. Bkz. [Azure izleyici fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/):

- Günlük sorgusu çalıştırılıyor.
- Ölçüm veya günlük sorgusu uyarı kuralı oluşturma.
- Herhangi bir uyarı kuralından bildirim gönderiliyor.
- API aracılığıyla ölçümlere erişme.

## <a name="monitoring-data"></a>Verileri izleme
Azure 'daki kaynaklar, aşağıdaki diyagramda gösterilen [Günlükler](../logs/data-platform-logs.md) ve [ölçümler](../essentials/data-platform-metrics.md) oluşturur. Oluşturdukları belirli veriler ve sağladıkları tüm ek çözümler veya içgörüler için her bir Azure hizmeti için belgelere bakın.

![Genel Bakış](media/monitor-azure-resource/logs-metrics.png)



- [Platform ölçümleri](../essentials/data-platform-metrics.md) -düzenli aralıklarla otomatik olarak toplanan ve belirli bir zamanda kaynağın bazı yönlerini tanımlayan sayısal değerlerdir. 
- [Kaynak günlükleri](./platform-logs-overview.md) -bir Azure kaynağı içinde (veri düzlemi) gerçekleştirilen işlemlere ilişkin öngörüler sağlar. Örneğin, bir Key Vault gizli anahtar alma veya bir veritabanına istek yapma. Kaynak günlüklerinin içeriği ve yapısı, Azure hizmeti ve kaynak türüne göre farklılık gösterir.
- [Etkinlik günlüğü](./platform-logs-overview.md) -abonelik içindeki her bir Azure kaynağında (Yönetim düzlemi) gerçekleştirilen işlemlere ilişkin öngörüler sağlar (örneğin, yeni bir kaynak oluşturma veya bir sanal makine başlatma). Bu, aboneliğinizdeki kaynaklar üzerinde herhangi bir yazma işlemi (PUT, POST, SILME) için ne zaman, kim ve ne zaman alındığını öğrenin.


## <a name="configuration-requirements"></a>Yapılandırma gereksinimleri

### <a name="configure-monitoring"></a>İzlemeyi yapılandırma
Bazı izleme verileri otomatik olarak toplanır, ancak gereksinimlerinize bağlı olarak bazı yapılandırmalar yapmanız gerekebilir. Her bir izleme verileri türü için belirli bilgiler için aşağıdaki bilgilere bakın.

- [Platform ölçümleri](../essentials/data-platform-metrics.md) -platform ölçümleri, hiçbir yapılandırma gerekmeden [Azure izleyici ölçümlerine](../essentials/data-platform-metrics.md) otomatik olarak toplanır. Azure Izleyici günlüklerine giriş göndermek veya Azure 'un dışına iletmek için bir tanılama ayarı oluşturun.
- [Kaynak günlükleri](./platform-logs-overview.md) -kaynak günlükleri Azure kaynakları tarafından otomatik olarak oluşturulur ancak bir tanılama ayarı olmadan toplanmaz.  Azure Izleyici günlüklerine giriş göndermek veya Azure 'un dışına iletmek için bir tanılama ayarı oluşturun.
- [Etkinlik günlüğü](./platform-logs-overview.md) -etkinlik günlüğü, yapılandırma gerekmeden otomatik olarak toplanır ve Azure Portal görüntülenebilir. Azure Izleyici günlüklerine kopyalamak veya bunları Azure 'un dışına iletmek için bir tanılama ayarı oluşturun.

### <a name="log-analytics-workspace"></a>Log Analytics çalışma alanı
Azure Izleyici günlüklerine veri toplanması Log Analytics çalışma alanı gerektirir. Yeni bir çalışma alanı oluşturarak hizmetinizi hızlı bir şekilde izlemeye başlayabilirsiniz, ancak diğer hizmetlerden veri toplayan bir çalışma alanı kullanmanın bir değeri olabilir. Gereksinimleriniz için en iyi çalışma alanı tasarımını belirlemenize yardımcı olmak üzere bir çalışma alanı oluşturma ve [Azure Izleyici günlükleri dağıtımınızı tasarlama](../logs/design-logs-deployment.md) hakkında bilgi için bkz. [Azure Portal Log Analytics çalışma alanı oluşturma](../logs/quick-create-workspace.md) . Kuruluşunuzda var olan bir çalışma alanını kullanıyorsanız, [Azure izleyici 'de günlük verilerine ve çalışma alanlarına erişimi yönetme](../logs/manage-access.md)bölümünde açıklandığı gibi uygun izinlere ihtiyacınız olacaktır. 





## <a name="diagnostic-settings"></a>Tanılama ayarları
Tanılama ayarları, belirli bir kaynağın kaynak günlüklerinin ve ölçümlerinin nereye gönderileceğini tanımlar. Olası hedefler şunlardır:

- Güçlü günlük sorguları ve ayrıca günlük uyarıları ve görselleştirmeler gibi diğer Azure Izleyici özelliklerinden yararlanmak için, Azure Izleyici tarafından toplanan diğer izleme verileriyle verileri analiz etmenizi sağlayan [Log Analytics çalışma alanı](./resource-logs.md#send-to-log-analytics-workspace) . 
- Üçüncü taraf Sıems ve diğer Log Analytics çözümleri gibi dış sistemlere veri akışı için [Olay Hub 'ları](./resource-logs.md#send-to-azure-event-hubs) . 
- Denetim, statik analiz veya yedekleme için yararlı olan [Azure depolama hesabı](./resource-logs.md#send-to-azure-storage) .

Azure portal aracılığıyla tanılama ayarlarını oluşturmak ve yönetmek için [Azure 'da platform günlükleri ve ölçümleri toplamak üzere tanılama oluştur](../essentials/diagnostic-settings.md) bölümündeki yordamı izleyin. Bir şablonda tanımlamak ve bir kaynağın oluşturulduğu sırada tüm izlemeyi etkinleştirmek için [Kaynak Yöneticisi şablonu kullanarak Azure 'da tanılama ayarı oluşturma](./resource-manager-diagnostic-settings.md) konusuna bakın.


## <a name="monitoring-in-the-azure-portal"></a>Azure portal izleme
 Çoğu Azure kaynağının izleme verilerine, Azure portal kaynağın menüsünden erişebilirsiniz. Bu, standart Azure Izleyici araçlarını kullanarak tek bir kaynağın verilerine erişmenizi sağlar. Bazı Azure Hizmetleri farklı seçenekler sağlayacak, bu nedenle ek bilgi için bu hizmetin belgelerine başvurmanız gerekir. İzlenen tüm kaynaklardaki verileri çözümlemek için **Azure izleyici** menüsünü kullanın. 

### <a name="overview"></a>Genel Bakış
Birçok hizmet, **genel bakış** sayfasına, işlem için hızlı bir bakış olarak izleme verileri içerir. Bu, genellikle Azure Izleyici ölçümlerinde depolanan platform ölçümlerinin bir alt kümesini temel alır. Diğer izleme seçenekleri, genellikle hizmetin menüsünün **izleme** bölümünde kullanılabilir.

![Genel bakış sayfası](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Öngörüler ve çözümler 
Bazı hizmetler, Azure Izleyici 'nin standart özelliklerinden daha fazla araç sağlar. [Öngörüler](../monitor-reference.md) , Azure izleyici veri platformu ve standart özellikleri üzerinde oluşturulmuş özelleştirilmiş bir izleme deneyimi sağlar. [Çözümler](../insights/solutions.md) , Azure izleyici günlükleri üzerinde oluşturulmuş, önceden tanımlanmış izleme mantığı sağlar. 

Bir hizmette Azure Izleyici Insight varsa, bu kaynağa her bir kaynağın menüsündeki **izleme** 'den erişebilirsiniz. **Azure izleyici** menüsünden tüm Öngörüler ve çözümlere erişin.

![Azure portal içgörüler](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Ölçümler
Ölçüm [Gezgini](./metrics-getting-started.md) 'ni kullanarak Azure Portal ölçümleri analiz ederek, çoğu hizmet için **ölçümler** menü öğesinden kullanılabilir. Bu araç, bağıntıları ve eğilimleri belirlemek için bireysel ölçümlerle çalışmanıza veya birden çok birleştirme yapmanıza olanak sağlar. 

- Ölçüm Gezgini 'ni kullanmanın temelleri için bkz. [Azure Ölçüm Gezgini](./metrics-getting-started.md) kullanmaya başlama.
- Birden çok ölçüm kullanma ve filtre uygulama ve bölme gibi Ölçüm Gezgini 'nin gelişmiş özellikleri için [Azure Ölçüm Gezgini gelişmiş özelliklerine](../essentials/metrics-charts.md) bakın.

![Azure portal Ölçüm Gezgini](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Etkinlik günlüğü 
Geçerli kaynağa ilk filtresi ayarlanmış Azure portal etkinlik günlüğündeki girdileri görüntüleyin. Etkinlik günlüğünü, oturum sorguları ve çalışma kitaplarında kullanmak üzere bir Log Analytics çalışma alanına kopyalayın. 

- Etkinlik günlüğünü görüntüleme ve çeşitli yöntemler kullanılarak girişleri alma hakkında ayrıntılı bilgi için bkz. [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](../essentials/activity-log.md#view-the-activity-log) .
- Günlüğe kaydedilen belirli olaylar için Azure hizmetinize yönelik belgelere bakın.

![Etkinlik Günlüğü](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure İzleyici Günlükleri
Azure Izleyici günlükleri, güçlü bir sorgu aracıyla analizler için birden çok hizmetten ve diğer veri kaynaklarından günlükleri ve ölçümleri birleştirir. Yukarıda açıklandığı gibi, Azure Izleyici 'deki bir Log Analytics çalışma alanında platform ölçümleri, etkinlik günlüğü ve kaynak günlüklerini toplamak için bir tanılama ayarı oluşturun.

[Log Analytics](../logs/log-analytics-tutorial.md) , tam özellikli bir sorgu dili kullanarak günlük verilerinin gelişmiş analizini gerçekleştirmenize olanak tanıyan, Azure izleyici 'nin güçlü bir özelliği olan [günlük sorgularıyla](../logs/log-query-overview.md)çalışmanıza olanak sağlar. [Sorgu kapsamı](../logs/scope.md#query-scope)olarak kaynağı kullanarak günlük sorgularıyla çalışmak Için bir Azure kaynağının **izleme** menüsündeki **günlüklerden** Log Analytics açın. Bu, yalnızca söz konusu kaynak için birden çok tablo genelinde verileri analiz etmenizi sağlar. Tüm kaynaklar için günlüklere erişmek üzere Azure Izleyici menüsünden **günlükleri** kullanın. 

- Günlük sorgularını yazmak için kullanılan sorgu dilini kullanma hakkında bir öğretici için bkz. [Azure izleyici 'de günlük sorgularıyla çalışmaya başlama](../logs/get-started-queries.md) .
- Azure Izleyici günlüklerinde kaynak günlüklerinin nasıl toplandığını ve bir sorgudaki bunlara nasıl erişecekleri hakkında bilgi için bkz. Azure [izleyici 'de Log Analytics çalışma alanında Azure Kaynak günlüklerini toplama](./resource-logs.md#send-to-log-analytics-workspace) .
- Kaynak günlük verilerinin Azure Izleyici günlüklerinde nasıl yapılandırıldığı hakkında bir açıklama için bkz. [koleksiyon modu](./resource-logs.md#send-to-log-analytics-workspace) .
- Azure Izleyici günlüklerinde tablosu hakkındaki ayrıntılar için her bir Azure hizmetine yönelik belgelere bakın.

![Azure portal Log Analytics](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Komut satırından izleme
Bir komut satırından kaynağından toplanan izleme verilerine erişebilir veya [Azure PowerShell](/powershell/azure/) veya [Azure komut satırı arabirimini](/cli/azure/)kullanarak bir komut dosyasına dahil edebilirsiniz. 

- CLı 'dan ölçüm verilerine erişmek için bkz. [CLI ölçümleri başvurusu](/cli/azure/monitor/metrics) .
- CLı 'dan günlük sorgusu kullanarak Azure Izleyici günlük verilerine erişmek için bkz. [clı Log Analytics başvurusu](/cli/azure/monitor/log-analytics) .
- Azure PowerShell ölçüm verilerine erişmek için [Azure PowerShell ölçüm başvurusuna](/powershell/module/azurerm.insights/get-azurermmetric) bakın.
- Azure PowerShell bir günlük sorgusu kullanarak Azure Izleyici günlük verilerine erişmek için [Azure PowerShell günlük sorgu başvurusu](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) ' na bakın.

## <a name="monitoring-from-rest-api"></a>REST API izleme
REST API kullanarak özel bir uygulamaya kaynağından toplanan izleme verilerini dahil edin.

- Azure Izleyici REST API ölçümlere erişme hakkında daha fazla bilgi için bkz. [Azure izleme REST API](./rest-api-walkthrough.md) Kılavuzu.
- Azure PowerShell günlük sorgusu kullanarak Azure Izleyici günlük verilerine erişme hakkında bilgi için bkz. [azure Log Analytics REST API](https://dev.loganalytics.io/) .

## <a name="alerts"></a>Uyarılar
[Uyarılar](../alerts/alerts-overview.md) , izleme verilerinizde önemli koşullar bulunduğunda sizi etkili bir şekilde bilgilendirir ve işlem yapabilirsiniz. Uyarı için bir hedef tanımlayan bir uyarı kuralı, bir uyarı oluşturulup oluşturulmayacağını belirten koşullar ve yanıt almak için herhangi bir eylemin oluşturulması gerekir.

Farklı türlerde izleme verileri farklı tür uyarı kuralları için kullanılır.

- [Etkinlik günlüğü uyarısı](../alerts/alerts-activity-log.md) -etkinlik günlüğünde belirli ölçütlerle eşleşen bir girdi oluşturulduğunda bir uyarı oluşturun. Bu, örneğin belirli bir kaynak türü oluşturulduğunda veya bir yapılandırma değişikliği başarısız olduğunda bildirim almanıza olanak sağlar.
- [Ölçüm uyarısı](../alerts/alerts-metric.md) -ölçüm değeri belirli bir eşiği aştığında uyarı oluştur. Ölçüm uyarıları diğer uyarılardan daha fazla yanıt verebilir ve sorun düzeltildiğinde otomatik olarak çözülebilir.
- [Sorgu uyarısını günlüğe](../alerts/alerts-log.md) kaydet-belirli bir koşul bulunursa, düzenli aralıklarla bir günlük sorgusu çalıştırın ve uyarı oluşturun. Bu, birden çok veri kümesi ve üzerinde karmaşık analiz gerçekleştirmenize olanak sağlar.

Uyarıları görüntülemek ve bu kaynakla ilgili uyarı kurallarını yönetmek için bir kaynağın menüsündeki **uyarıları** kullanın. Yalnızca etkinlik günlüğü uyarıları ve ölçüm uyarıları tek tek Azure kaynaklarını hedef olarak kullanır. Günlük sorgusu uyarıları bir Log Analytics çalışma alanını hedef olarak kullanır ve bu çalışma alanında depolanan tüm günlüklere erişebilen bir sorguyu temel alır. Tüm kaynaklarla ilgili uyarıları görüntülemek ve yönetmek için Azure Izleyici menüsünü ve günlük sorgusu uyarı kurallarını yönet ' i kullanın.

- Uyarı kuralları oluşturma hakkında ayrıntılı bilgi için yukarıdaki farklı Uyarı türleri makalelerine bakın.
- Uyarılara yanıtları yönetmenizi sağlayan bir eylem grubu oluşturma hakkında ayrıntılı bilgi için [Azure Portal eylem grupları oluşturma ve yönetme](../alerts/action-groups.md) bölümüne bakın.



## <a name="next-steps"></a>Sonraki adımlar

* Farklı Azure hizmetlerine yönelik kaynak günlüklerinin ayrıntıları için bkz. [Azure Kaynak günlükleri Için desteklenen hizmetler, şemalar ve Kategoriler](./resource-logs-schema.md) .
