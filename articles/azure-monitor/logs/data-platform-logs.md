---
title: Azure İzleyici Günlükleri
description: İzleme verilerinin gelişmiş analizine yönelik olarak kullanılan Azure Izleyici günlüklerini açıklar.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 9794c5f048b8795652e4b31e0134b36a77715abe
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873388"
---
# <a name="azure-monitor-logs-overview"></a>Azure İzleyici Günlükleri'ne genel bakış
Azure Izleyici günlükleri, [izlenen kaynaklardan](../monitor-reference.md)gelen günlük ve performans verilerini toplayan ve düzenleyen bir Azure izleyici özelliğidir. Azure hizmetlerinden gelen [Platform günlükleri](../essentials/platform-logs-overview.md) , [sanal makine aracılarından](../agents/agents-overview.md)gelen günlük ve performans verileri ve [uygulamalardan](../app/app-insights-overview.md) kullanım ve performans verileri gibi farklı kaynaklardaki veriler tek bir çalışma alanında birleştirilebilir ve bu sayede milyonlarca kaydı hızlı bir şekilde analiz etme yeteneğine sahip olan gelişmiş bir sorgu dili kullanılarak analiz edilebilir. Yalnızca belirli bir kayıt kümesini alan basit bir sorgu gerçekleştirebilir veya izleme verilerinizde kritik desenleri belirlemek için gelişmiş veri analizi gerçekleştirebilirsiniz. Log Analytics kullanarak günlük sorgularıyla ve sonuçlarıyla etkileşimli olarak çalışın, sorunları önceden bilgilendirmeye yönelik bir uyarı kurallarında kullanın veya sonuçları bir çalışma kitabı veya panoda görselleştirin.

> [!NOTE]
> Azure Izleyici günlükleri, Azure Izleyicisini destekleyen veri platformunun bir yarısıdır. Diğer bir deyişle, sayısal verileri bir zaman serisi veritabanında depolayan [Azure Izleyici ölçümleri](../essentials/data-platform-metrics.md) vardır. Bu, bu verilerin Azure Izleyici günlüklerinde verileri daha basit hale getirir ve neredeyse gerçek zamanlı senaryoları destekleyerek, sorunların uyarı ve hızlı bir şekilde algılanması için oldukça yararlı hale gelir. Yalnızca belirli bir yapıda sayısal veriler depolayabilse de, Günlükler her biri kendi yapısıyla çeşitli farklı veri türlerini depolayabilse de ölçümler. Ayrıca, ölçüm verilerinin çözümlenmesi için kullanılamayan günlük sorgularını kullanarak Günlükler verilerinde karmaşık analizler gerçekleştirebilirsiniz.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Azure Izleyici günlükleri ile neler yapabilirsiniz?
Aşağıdaki tabloda Azure Izleyici 'de günlükleri kullanmanın farklı yolları açıklanmaktadır:

|  | Description |
|:---|:---|
| **Analiz** | [Günlük sorgularını](./log-query-overview.md) yazmak ve güçlü bir analiz altyapısı kullanarak günlük verilerini etkileşimli olarak çözümlemek için Azure Portal [Log Analytics](./log-analytics-tutorial.md) kullanın |
| **Uyarı** | Sorgu sonuçları belirli bir sonuçla eşleşiyorsa bildirim gönderen veya [otomatik eylem](../alerts/action-groups.md) alan bir [günlük uyarı kuralı](../alerts/alerts-log.md) yapılandırın. |
| **Görselleştirme** | Bir [Azure panosuna](../../azure-portal/azure-portal-dashboards.md)tablo veya grafik olarak işlenen sorgu sonuçlarını sabitleme.<br>Etkileşimli bir raporda birden fazla veri kümesiyle birleştirilecek bir [çalışma kitabı](../visualize/workbooks-overview.md) oluşturun. <br>Farklı görselleştirmeler kullanmak ve Azure dışındaki kullanıcılarla paylaşmak için bir sorgunun sonuçlarını [Power BI](../visualize/powerbi.md) dışarı aktarın.<br>Bir sorgunun sonuçlarını [Grafana](../visualize/grafana-plugin.md) olarak dışa aktarın ve diğer veri kaynaklarıyla birleştirin.|
| **İçgörüler** | Belirli uygulamalar ve hizmetler için özelleştirilmiş bir izleme deneyimi sağlayan [öngörüleri](../monitor-reference.md#insights-and-core-solutions) destekleme.  |
| **Almanın** | [Azure CLI](/cli/azure/monitor/log-analytics)kullanarak bir komut satırından günlük sorgusu sonuçlarına erişin.<br>[PowerShell cmdlet 'lerini](/powershell/module/az.operationalinsights)kullanarak bir komut satırından günlük sorgusu sonuçlarına erişin.<br>[REST API](https://dev.loganalytics.io/)kullanarak özel bir uygulamadan günlük sorgusu sonuçlarına erişin. |
| **Dışarı Aktarma** | Azure depolama hesabına veya Azure Event Hubs [otomatik olarak günlük verilerinin dışarı aktarılmasını](./logs-data-export.md) yapılandırın.<br>Günlük verilerini almak ve [Logic Apps](./logicapp-flow-connector.md)kullanarak bir dış konuma kopyalamak için bir iş akışı oluşturun. |

![Günlüklere genel bakış](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Veri toplama
Bir Log Analytics çalışma alanı oluşturduktan sonra, verilerini göndermek için farklı kaynaklar yapılandırmanız gerekir. Hiçbir veri otomatik olarak toplanmaz. Bu yapılandırma, veri kaynağına bağlı olarak farklı olacaktır. Örneğin, Azure kaynaklarından kaynak günlüklerini çalışma alanına göndermek için [Tanılama ayarları oluşturun](../essentials/diagnostic-settings.md) . Sanal makinelerden veri toplamak için [VM öngörülerini etkinleştirin](../vm/vminsights-enable-overview.md) . Ek olayları ve performans verilerini toplamak için [çalışma alanındaki veri kaynaklarını](../agents/data-sources.md) yapılandırın.

- Azure izleyici günlüklerine veri göndermek için yapılandırabileceğiniz veri kaynaklarının tüm listesi için bkz. [Azure izleyici tarafından Izlenen nedir?](../monitor-reference.md) .


## <a name="log-analytics-workspaces"></a>Log Analytics çalışma alanları
Azure Izleyici günlükleri tarafından toplanan veriler bir veya daha fazla [Log Analytics çalışma](./design-logs-deployment.md)alanında depolanır. Çalışma alanı verilerin coğrafi konumunu, hangi kullanıcıların verilere erişebileceğini tanımlayan erişim haklarını ve fiyatlandırma katmanı ve veri saklama gibi yapılandırma ayarlarını tanımlar.  

Azure Izleyici günlüklerini kullanmak için en az bir çalışma alanı oluşturmanız gerekir. Tek bir çalışma alanı tüm izleme verileriniz için yeterli olabilir veya gereksinimlerinize bağlı olarak birden çok çalışma alanı oluşturmayı tercih edebilir. Örneğin, üretim verileriniz ve test için bir çalışma alanınız olabilir. 

- Yeni bir çalışma alanı oluşturmak için [Azure portal Log Analytics çalışma alanı oluşturma](./quick-create-workspace.md) bölümüne bakın.
- Bkz. birden çok çalışma alanı oluşturma konusunda dikkat edilmesi gereken [Azure Izleyici günlükleri dağıtımınızı tasarlama](design-logs-deployment.md) .

## <a name="data-structure"></a>Veri yapısı
Günlük sorguları Log Analytics çalışma alanından verilerini alır. Her çalışma alanı birden fazla veri satırı içeren ayrı sütunlarda düzenlenmiş birden çok tablo içerir. Her tablo, veri kaynağı tarafından belirtilen veri satırları tarafından paylaşılan benzersiz bir sütun kümesi tarafından tanımlanır. 

[![Azure Izleyici günlükleri yapısı](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Application Insights günlük verileri Azure Izleyici günlüklerinde de depolanır, ancak uygulamanızın nasıl yapılandırıldığına bağlı olarak farklı bir şekilde depolanır. Çalışma alanı tabanlı bir uygulama için veriler, uygulama istekleri, özel durumlar ve sayfa görünümleri gibi verileri tutmak üzere standart bir tablo kümesindeki Log Analytics çalışma alanında depolanır. Birden çok uygulama aynı çalışma alanını kullanabilir. Klasik bir uygulama için, veriler Log Analytics çalışma alanında depolanmaz. Aynı sorgu dilini kullanır ve Azure portal aynı Log Analytics aracını kullanarak sorgular oluşturup çalıştırırsınız. Klasik uygulamalar için veriler birbirinden ayrı olarak depolanır. Tablo ve sütun adları farklı olsa da, genel yapısı çalışma alanı tabanlı uygulamalarla aynıdır. Çalışma alanı tabanlı ve klasik uygulamalar için şemanın ayrıntılı bir karşılaştırması için [çalışma alanı tabanlı kaynak değişikliklerine](../app/apm-tables.md) bakın.


> [!NOTE]
> Application Insights deneyiminde Application Insights klasik kaynak sorguları, çalışma kitapları ve günlük tabanlı uyarılarınız için de tam geriye doğru uyumluluk sağlıyoruz. [Yeni çalışma alanı tabanlı tablo yapısına/şemasına](../app/apm-tables.md) karşı sorgu/görünüm için öncelikle Log Analytics çalışma alanınıza gitmeniz gerekir. Önizleme sırasında, Application Insights bölmeleri içinden **günlüklerin** seçilmesi, klasik Application Insights sorgu deneyimine erişmenizi sağlayacaktır. Daha fazla ayrıntı için bkz. [sorgu kapsamı](./scope.md) .


[![Application Insights için Azure Izleyici günlük yapısı](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Günlük sorguları
Veriler, verileri işlemek ve sonuçları döndürmek için salt okunurdur bir istek olan günlük sorgusu kullanılarak bir Log Analytics çalışma alanından alınır. Günlük sorguları, Azure Veri Gezgini tarafından kullanılan sorgu dili olan [kusto sorgu dilinde (KQL)](/azure/data-explorer/kusto/query/)yazılır. Sonuçları etkileşimli olarak analiz etmek, uyarı kurallarında kullanmak veya sonuçları çalışma kitaplarına veya panolara eklemek için Log Analytics günlük sorguları yazabilirsiniz. Öngörüler, görünümlerini ve çalışma kitaplarını desteklemek için önceden oluşturulmuş sorgular içerir.

- Günlük sorgularının kullanıldığı bir liste için bkz. [Azure izleyici 'de günlük sorguları](./log-query-overview.md) ve başlamanıza başlamak için öğreticilere ve diğer belgelere başvurular.

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Günlük sorgularını düzenlemek ve çalıştırmak ve sonuçlarını etkileşimli olarak çözümlemek için Azure portal bir araç olan Log Analytics kullanın. Daha sonra, Azure Izleyici 'de günlük sorgu uyarıları ve çalışma kitapları gibi diğer özellikleri desteklemek için oluşturduğunuz sorguları kullanabilirsiniz. Azure Izleyici menüsündeki **Günlükler** seçeneğinden veya Azure Portal diğer hizmetlerden Log Analytics erişin.

- Log Analytics açıklaması için bkz. [Azure izleyici 'de Log Analytics genel bakış](./log-analytics-overview.md) . 
- Basit bir günlük sorgusu oluşturmak ve sonuçlarını çözümlemek için Log Analytics özelliklerini kullanma hakkında bilgi almak için [Log Analytics öğreticiye](./log-analytics-tutorial.md) bakın.



## <a name="relationship-to-azure-data-explorer"></a>Azure Veri Gezgini ile ilişki
Azure Izleyici günlükleri Azure Veri Gezgini tabanlıdır. Log Analytics çalışma alanı kabaca Azure Veri Gezgini 'deki bir veritabanının eşdeğeridir, tablolar aynı şekilde yapılandırılmıştır ve her ikisi de aynı kusto sorgu dilini (KQL) kullanır. Azure portal Azure Izleyici sorgularıyla çalışmak için Log Analytics kullanma deneyimi, Azure Veri Gezgini Web Kullanıcı arabirimini kullanarak deneyimle benzerdir. [Azure Veri Gezgini sorgusunda bir Log Analytics çalışma alanından da veri](/azure/data-explorer/query-monitor-data)ekleyebilirsiniz. 


## <a name="next-steps"></a>Sonraki adımlar

- Log Analytics çalışma alanından verileri almak ve analiz etmek için [günlük sorguları](./log-query-overview.md) hakkında bilgi edinin.
- [Azure izleyici 'de ölçümler](../essentials/data-platform-metrics.md)hakkında bilgi edinin.
- Azure 'daki farklı kaynaklar için [kullanılabilen izleme verileri](../agents/data-sources.md) hakkında bilgi edinin.