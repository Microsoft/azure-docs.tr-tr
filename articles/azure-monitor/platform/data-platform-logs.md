---
title: Azure Izleyici 'de Günlükler | Microsoft Docs
description: İzleme verilerinin gelişmiş analizine yönelik olarak kullanılan Azure Izleyici günlüklerini açıklar.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 9aed19c88517868c2e8cb860dc01d01b7a7c3127
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262071"
---
# <a name="logs-in-azure-monitor"></a>Azure İzleyici'deki günlükler

> [!NOTE]
> Azure Izleyici tarafından toplanan tüm veriler, iki temel tür, ölçüm ve günlüklerden birine uyar. Bu makalede Günlükler açıklanır. Ölçüm hakkında ayrıntılı bir açıklama ve [Azure izleyici tarafından toplanan verileri](data-platform.md) iki karşılaştırma için Izlemek üzere [Azure izleyici 'de ölçümler](data-platform-metrics.md) bölümüne bakın.

Azure Izleyici 'deki Günlükler özellikle çeşitli kaynaklardaki veriler arasında karmaşık analizler gerçekleştirmek için yararlıdır. Bu makalede, günlüklerin Azure Izleyici 'de nasıl yapılandırıldığı, verilerle neler yapabilecekleri ve verileri günlüklerde depolayan farklı veri kaynakları tanımlandığı açıklanır.

> [!NOTE]
> Azure Izleyici günlüklerini ve Azure 'da günlük verilerinin kaynaklarını ayırt etmek önemlidir. Örneğin, Azure 'daki abonelik düzeyi olayları, Azure Izleyici menüsünden görüntüleyebileceğiniz bir [etkinlik günlüğüne](activity-logs-overview.md) yazılır. Çoğu kaynak, işletimsel bilgileri farklı konumlara iletebilmeniz için bir [Tanılama günlüğüne](resource-logs-overview.md) yazar. Azure Izleyici günlükleri, tüm kaynak kümesinde derin analiz sağlamak üzere diğer izleme verileriyle birlikte etkinlik günlüklerini ve tanılama günlüklerini toplayan bir günlük veri platformudur.

## <a name="what-are-azure-monitor-logs"></a>Azure Izleyici günlükleri nelerdir?

Azure Izleyici 'deki Günlükler, her tür için farklı özellik kümelerine sahip kayıtlarla düzenlenmiş farklı veri türleri içerir. Günlükler, Azure Izleyici ölçümleri gibi sayısal değerler içerebilir, ancak genellikle ayrıntılı açıklamalarla metin verileri içerir. Bunlar, kendi yapısında farklılık gösteren ve genellikle düzenli aralıklarla toplanmayan ölçüm verilerinden farklılık gösterir. Olaylar ve izlemeler gibi telemetri, tüm analiz için birleştirilebilmesi için performans verilerine ek olarak Azure Izleyici günlüklerini de depolardır.

Genel bir günlük girişi türü, sporadlı olarak toplanan bir olaydır. Olaylar bir uygulama veya hizmet tarafından oluşturulur ve genellikle kendi kendine tamamen bağlam sağlamak için yeterli bilgi içerir. Örneğin, bir olay belirli bir kaynağa oluşturulmuş veya değiştirilmiş, artan trafiğe yanıt olarak başlatılan yeni bir ana bilgisayar veya bir uygulamada bir hata algılandı olduğunu gösterebilir.

 Veri biçimi farklılık gösterebileceğinden, ihtiyaç duydukları yapısını kullanarak özel günlükleri uygulamalar oluşturabilirsiniz. Ölçüm verileri, daha fazla veri analizi için diğer izleme verileriyle birlikte birleştirilecek günlüklerde da depolanabilir.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Azure Izleyici günlükleri ile neler yapabilirsiniz?
Aşağıdaki tabloda, Azure Izleyici 'de günlükleri kullanmanın farklı yolları listelenmektedir.


|  |  |
|:---|:---|
| Çözümle | [Günlük sorgularını](../log-query/log-query-overview.md) yazmak ve güçlü veri Gezgini analiz altyapısını kullanarak günlük verilerini etkileşimli olarak çözümlemek için Azure Portal [Log Analytics](../log-query/get-started-portal.md) kullanın.<br>Günlük sorgularını yazmak ve Application Insights günlük verilerini etkileşimli olarak çözümlemek için Azure portal [Application Insights Analytics konsolunu](../app/analytics.md) kullanın. |
| Görselleştirin | Bir [Azure panosuna](../../azure-portal/azure-portal-dashboards.md)tablo veya grafik olarak işlenen sorgu sonuçlarını sabitleme.<br>Etkileşimli bir raporda birden fazla veri kümesiyle birleştirilecek bir [çalışma kitabı](../app/usage-workbooks.md) oluşturun. <br>Sorgu sonuçlarını dışarı aktarma [Power BI](powerbi.md) farklı görselleştirme kullanın ve Azure dışındaki kullanıcılarla paylaşmak için.<br>Bir sorgunun sonuçlarını [Grafana](grafana-plugin.md) olarak dışa aktarın ve diğer veri kaynaklarıyla birleştirin.|
| Uyarı | Yapılandırma bir [günlük uyarı kuralı](alerts-log.md) bildirim gönderen veya alan [eylemi otomatik](action-groups.md) zaman sorgunun sonuçlarını eşleşen belirli bir sonuç.<br>Ölçüm olarak ayıklanan belirli günlük veri günlüklerinde [ölçüm uyarı kuralı](alerts-metric-logs.md) yapılandırın. |
| Almanın | [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)kullanarak bir komut satırından günlük sorgusu sonuçlarına erişin.<br>[PowerShell cmdlet 'lerini](https://docs.microsoft.com/powershell/module/az.operationalinsights)kullanarak bir komut satırından günlük sorgusu sonuçlarına erişin.<br>[REST API](https://dev.loganalytics.io/)kullanarak özel bir uygulamadan günlük sorgusu sonuçlarına erişin. |
| Dışarı Aktarma | Günlük verilerini almak ve [Logic Apps](~/articles/logic-apps/index.yml)kullanarak bir dış konuma kopyalamak için bir iş akışı oluşturun. |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Azure Izleyici günlüklerindeki veriler nasıl yapılandırılır?
Azure Izleyici günlükleri tarafından toplanan veriler [Log Analytics çalışma alanında](../platform/design-logs-deployment.md)depolanır. Her çalışma alanı, her biri belirli bir kaynaktaki verilerin depolandığı birden çok tablo içerir. Tüm tablolar [bazı ortak özellikleri](log-standard-properties.md)paylaştığında, her birinin depoladığı veri türüne bağlı olarak benzersiz bir özellikler kümesi vardır. Yeni bir çalışma alanında Standart tablo kümesi olacaktır ve farklı izleme çözümleri ve çalışma alanına yazan diğer hizmetler tarafından daha fazla tablo eklenecektir.

Application Insights günlük verileri çalışma alanlarıyla aynı Log Analytics altyapısını kullanır, ancak izlenen her uygulama için ayrı olarak depolanır. Her uygulamanın, uygulama istekleri, özel durumlar ve sayfa görünümleri gibi verileri tutmak için standart bir tablo kümesi vardır.

Günlük sorguları, Log Analytics çalışma alanındaki veya Application Insights uygulamasındaki verileri kullanır. Uygulama verilerini diğer günlük verileriyle birlikte çözümlemek veya birden çok çalışma alanı veya uygulama dahil olmak üzere sorgular oluşturmak için bir [çapraz kaynak sorgu](../log-query/cross-workspace-query.md) kullanabilirsiniz.

![Çalışma Alanı](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Günlük sorguları
Azure Izleyici günlüklerindeki veriler, toplanan verileri hızlıca almanıza, birleştirmenize ve çözümlemenize olanak sağlayan [kusto sorgu diliyle](../log-query/get-started-queries.md)yazılmış bir [günlük sorgusu](../log-query/log-query-overview.md) kullanılarak alınır. Azure portal günlük sorgularını yazmak ve test etmek için [Log Analytics](../log-query/portals.md) kullanın. Bu, sonuçlarla etkileşimli olarak çalışmanıza veya bunları diğer görselleştirmelerle görüntülemek için bir panoya sabitlemeye olanak tanır.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Application Insights verilerini çözümlemek için [Application Insights Log Analytics](../app/analytics.md) açın.

![Application Insights Analizi](media/data-platform-logs/app-insights-analytics.png)

Günlük verilerini [log ANALYTICS API](https://dev.loganalytics.io/documentation/overview) ve [Application Insights REST API](https://dev.applicationinsights.io/documentation/overview)kullanarak da alabilirsiniz.


## <a name="sources-of-azure-monitor-logs"></a>Azure Izleyici günlüklerinin kaynakları
Azure Izleyici, Azure 'daki ve şirket içi kaynaklardan çeşitli kaynaklardan günlük verileri toplayabilir. Aşağıdaki tablolarda, Azure Izleyici günlüklerine veri yazan farklı kaynaklardan kullanılabilir farklı veri kaynakları listelenmektedir. Her birinin gerekli yapılandırma hakkında ayrıntıların bağlantısı vardır.

### <a name="azure-tenant-and-subscription"></a>Azure kiracısı ve aboneliği

| Data | Açıklama |
|:---|:---|
| Denetim günlüklerini Azure Active Directory | Her bir dizin için Tanılama ayarları aracılığıyla yapılandırılır. Bkz. Azure [ad günlüklerini Azure izleyici günlükleriyle tümleştirme](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Etkinlik günlükleri | Varsayılan olarak ayrı bir şekilde depolanır ve gerçek zamanlı uyarıların yakınında kullanılabilir. Log Analytics çalışma alanına yazmak için etkinlik günlüğü Analizi çözümünü yükler. Bkz. [Log Analytics Azure etkinlik günlüklerini toplayın ve çözümleyin](activity-log-collect.md). |

### <a name="azure-resources"></a>Azure kaynakları

| Data | Açıklama |
|:---|:---|
| Kaynak tanılama | Log Analytics çalışma alanına ölçümler de dahil olmak üzere tanılama verilerine yazmak için tanılama ayarlarını yapılandırın. [Log Analytics için bkz. Azure tanılama günlüklerini akışa almak](resource-logs-collect-storage.md). |
| İzleme çözümleri | İzleme çözümleri, Log Analytics çalışma alanına topladıkları verileri yazar. Çözümlerin listesi için bkz. [Azure 'daki yönetim çözümleri Için veri toplama ayrıntıları](../insights/solutions-inventory.md) . Çözümleri yükleme ve kullanma hakkında ayrıntılı bilgi için bkz. [Azure izleyici 'de çözümleri izleme](../insights/solutions.md) . |
| Ölçümler | Azure Izleyici kaynakları için platform ölçümlerini, daha uzun dönemler için günlük verilerini koruyacak ve [kusto sorgu dilini](/azure/kusto/query/)kullanarak diğer veri türleriyle karmaşık analizler gerçekleştirmek için bir Log Analytics çalışma alanına gönderin. [Log Analytics için bkz. Azure tanılama günlüklerini akışa almak](resource-logs-collect-storage.md). |
| Azure tablo depolaması | Azure depolama 'dan, bazı Azure kaynaklarının izleme verileri yazacağı verileri toplayın. Bkz. [Log Analytics Için Azure Blob Storage 'ı IIS ve Azure Tablo Depolaması Için kullanma](azure-storage-iis-table.md). |

### <a name="virtual-machines"></a>Virtual Machines

| Data | Açıklama |
|:---|:---|
|  Aracı veri kaynakları | [Windows](agent-windows.md) ve [Linux](../learn/quick-collect-linux-computer.md) aracılarından toplanan veri kaynakları, olayları, performans verilerini ve özel günlükleri içerir. Veri kaynaklarının bir listesi ve yapılandırma hakkında ayrıntılı bilgi için bkz. [Azure izleyici 'de aracı veri kaynakları](data-sources.md) . |
| İzleme çözümleri | İzleme çözümleri, aracılardan topladıkları verileri Log Analytics çalışma alanına yazar. Çözümlerin listesi için bkz. [Azure 'daki yönetim çözümleri Için veri toplama ayrıntıları](../insights/solutions-inventory.md) . Çözümleri yükleme ve kullanma hakkında ayrıntılı bilgi için bkz. [Azure izleyici 'de çözümleri izleme](../insights/solutions.md) . |
| System Center Operations Manager | Şirket içi aracılardan günlüklere olay ve performans verilerini toplamak için Operations Manager yönetim grubunu Azure Izleyici 'ye bağlayın. Bu yapılandırmayla ilgili ayrıntılar için bkz. [Connect Operations Manager Log Analytics](om-agents.md) . |


### <a name="applications"></a>Uygulamalar

| Data | Açıklama |
|:---|:---|
| İstekler ve özel durumlar | Uygulama istekleri ve özel durumlar hakkındaki ayrıntılı veriler, _isteklerde_, _PageViews_ve _Exceptions_ tablolarında bulunur. [Dış bileşenlere](../app/asp-net-dependencies.md) yapılan çağrılar _Bağımlılıklar_ tablosudur. |
| Kullanım ve performans | Uygulama performansı _istekler_, _Browserzamanlamalar_ ve _PerformanceCounters_ tablolarında kullanılabilir. [Özel ölçümler](../app/api-custom-events-metrics.md#trackevent) Için veriler _customölçümler_ tablosudur.|
| İzleme verileri | [Dağıtılmış izlemenin](../app/distributed-tracing.md) sonuçları _izlemeler_ tablosunda depolanır. |
| Kullanılabilirlik testleri | [Kullanılabilirlik testlerinden](../app/monitor-web-app-availability.md) özet veriler, kullanılabilirliği _sonuçlar_ tablosunda depolanır. Bu testlerin ayrıntılı verileri ayrı depolamada bulunur ve Azure portal Application Insights 'ten erişilir. |

### <a name="insights"></a>Insights

| Data | Açıklama |
|:---|:---|
| Kapsayıcılar için Azure İzleyici | [Kapsayıcılar Için Azure izleyici](../insights/container-insights-overview.md)tarafından toplanan envanter ve performans verileri. Tablo listesi için bkz. [kapsayıcı verileri-koleksiyon ayrıntıları](../insights/container-insights-log-search.md#container-records) . |
| VM'ler için Azure İzleyici | [VM'ler için Azure izleyici](../insights/vminsights-overview.md)tarafından toplanan harita ve performans verileri. Bu verileri sorgulama hakkındaki ayrıntılar için bkz. [VM'ler için Azure izleyici günlüklerini sorgulama](../insights/vminsights-log-search.md) . |

### <a name="custom"></a>Özel 

| Data | Açıklama |
|:---|:---|
| REST API | Herhangi bir REST istemcisinden Log Analytics çalışma alanına veri yazın. Ayrıntılar için bkz. [http veri toplayıcı API 'si ile günlük verilerini Azure izleyici 'ye gönderme](data-collector-api.md) .
| Mantıksal Uygulama | **Azure Log Analytics veri toplayıcısı** eylemiyle bir Logic App iş akışından bir Log Analytics çalışma alanına veri yazın. |

### <a name="security"></a>Güvenlik

| Data | Açıklama |
|:---|:---|
| Azure Güvenlik Merkezi | [Azure Güvenlik Merkezi](/azure/security-center/) , topladığı verileri, diğer günlük verileriyle çözümlenebileceği bir Log Analytics çalışma alanında depolar. Çalışma alanı yapılandırmasıyla ilgili ayrıntılar için bkz. [Azure Güvenlik Merkezi 'Nde veri toplama](../../security-center/security-center-enable-data-collection.md) . |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) , veri kaynaklarından verileri bir Log Analytics çalışma alanına depolar. Bkz. [veri kaynaklarını bağlama](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Sonraki adımlar

- [Azure izleyici veri platformu](data-platform.md)hakkında daha fazla bilgi edinin.
- [Azure izleyici 'de ölçümler](data-platform-metrics.md)hakkında bilgi edinin.
- Hakkında bilgi edinin [izleme verilerini kullanılabilir](data-sources.md) azure'daki farklı kaynakları.
