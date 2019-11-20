---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: 93a2554b5d3cc24e1b5fc1e3d0f18ed1bfe0579c
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71692053"
---
Azure 'da barındırılan VM 'lerin önemli büyümesi sayesinde, destekledikleri uygulamaları ve altyapı hizmetlerini etkileyen performans ve sistem durumu sorunlarını belirlemek önemlidir. Temel izleme, Azure ile varsayılan olarak, CPU kullanımı, disk kullanımı, bellek kullanımı ve konak Hiper Yöneticisi tarafından toplanan ağ trafiği ölçüm türlerine göre dağıtılır. Konuk işletim sisteminden sanal makinelerinizdeki tanılamayı yapılandırmak için ek ölçüm ve günlük verileri, [uzantıları](../articles/virtual-machines/windows/extensions-features.md) kullanılarak toplanabilir.

VM içinde çalışan konuk işletim sistemi, .NET tabanlı veya Java Web uygulaması bileşenlerinde performans ve sistem durumu sorunlarını algılamak ve tanılamak için, Azure Izleyici VM'ler için Azure İzleyici gibi kapsamlı özelliklerle merkezi bir izleme sağlar ve Application Insights.

## <a name="diagnostics-and-metrics"></a>Tanılama ve ölçümler 

Azure portal, Azure CLı, Azure PowerShell ve programlama uygulamaları programlama arabirimleri (API 'Ler) ' nde [ölçümleri](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) kullanarak [Tanılama verilerinin](https://docs.microsoft.com/cli/azure/vm/diagnostics) toplanmasını ayarlayabilir ve izleyebilirsiniz. Örneğin, şunları yapabilirsiniz:

- **VM için temel ölçümleri gözlemleyin.** Azure portal genel bakış ekranında, gösterilen temel ölçümler CPU kullanımı, ağ kullanımı, disk baytlarının toplamı ve saniye başına disk işlemleri içerir.

- **Önyükleme tanılaması koleksiyonunu etkinleştirin ve Azure portal kullanarak görüntüleyin.** Kendi görüntünüzü Azure 'a getirirken veya platform görüntülerinden birini önyüklerken, bir sanal makinenin önyüklenebilir olmayan bir duruma kaç nedenden kaynaklanabilir. Ayarlar ekranının Izleme bölümünde önyükleme tanılaması için **Etkinleştir** ' i TıKLATARAK bir VM oluşturduğunuzda, önyükleme tanılamayı kolayca etkinleştirebilirsiniz.

    VM 'Ler önyüklemesinde, önyükleme tanılama Aracısı önyükleme çıkışını yakalar ve Azure Storage 'da depolar. Bu veriler VM önyükleme sorunlarını gidermek için kullanılabilir. Komut satırı araçlarından bir VM oluşturduğunuzda önyükleme tanılaması otomatik olarak etkinleştirilmez. Önyükleme tanılamalarını etkinleştirmeden önce, önyükleme günlüklerini depolamak için bir depolama hesabı oluşturulmalıdır. Azure portal önyükleme tanılamayı etkinleştirirseniz, sizin için otomatik olarak bir depolama hesabı oluşturulur.

    VM oluşturulduğunda önyükleme tanılamayı etkinleştirmediyseniz, daha sonra [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)veya bir [Azure Resource Manager şablonu](../articles/virtual-machines/windows/extensions-diagnostics-template.md)kullanarak her zaman etkinleştirebilirsiniz.

- **Konuk işletim sistemi Tanılama verileri koleksiyonunu etkinleştirin.** Bir VM oluşturduğunuzda, Konuk işletim sistemi tanılamayı etkinleştirmek için Ayarlar ekranında fırsat alırsınız. Tanılama verileri koleksiyonunu etkinleştirdiğinizde, [Linux Için ıaasdiagnostics uzantısı](../articles/virtual-machines/linux/diagnostic-extension.md) veya [Windows Için ıaasdiagnostics](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) uzantısı, ek disk, CPU ve bellek VERILERI toplamanıza olanak sağlayan sanal makineye eklenir.

    Toplanan tanılama verilerini kullanarak, sanal makinelerinize yönelik otomatik ölçeklendirmeyi yapılandırabilirsiniz. Ayrıca, verileri depolamak için [Azure Izleyici günlüklerini](../articles/azure-monitor/platform/data-platform-logs.md) yapılandırabilir ve performansı doğru olmadığında size bildirmek üzere uyarılar ayarlayabilirsiniz.

## <a name="alerts"></a>Uyarılar

Belirli performans ölçümlerine göre [Uyarılar](../articles/azure-monitor/platform/alerts-overview.md) oluşturabilirsiniz. Ortalama CPU kullanımı belirli bir eşiği aştığında veya kullanılabilir boş disk alanı belirli bir miktarın altına düştüğünde, sorun hakkında uyarı alabilirsiniz. Uyarılar [Azure Portal](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal), [Azure Resource Manager ŞABLONLARı](../articles/azure-monitor/platform/alerts-metric-create-templates.md)veya [Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli)kullanılarak yapılandırılabilir.

## <a name="azure-service-health"></a>Azure Hizmet Durumu

Azure [hizmet durumu](../articles/service-health/service-health-overview.md) , Azure hizmetlerindeki sorunlardan etkilendiğiniz durumlarda kişiselleştirilmiş rehberlik ve destek sağlar ve yaklaşan planlı bakımın hazırlanmasına yardımcı olur. Azure hizmet durumu, hedeflenen ve esnek bildirimleri kullanarak sizi ve takımlarınızı uyarır.

## <a name="azure-resource-health"></a>Azure Kaynak Durumu

[Azure Kaynak durumu](../articles/service-health/resource-health-overview.md) , kaynaklarınızı etkileyen bir Azure sorunu tanılamanıza ve destek almanıza yardımcı olur. Kaynaklarınızın hem geçerli durumu hem de geçmişteki durumları hakkında bilgi verir ve sorunların etkisini azaltmanıza katkıda bulunur. Kaynak durumu, Azure hizmetleriyle ilgili sorunlarda yardıma ihtiyacınız olduğunda teknik destek sağlar.

## <a name="azure-activity-log"></a>Azure etkinlik günlüğü

[Azure etkinlik günlüğü](../articles/azure-monitor/platform/activity-logs-overview.md) , Azure 'da oluşan abonelik düzeyindeki olaylara ilişkin Öngörüler sağlayan bir abonelik günlüğü. Günlük, Azure Resource Manager işletimsel verilerden hizmet durumu olaylarında güncelleştirmelere kadar bir veri aralığı içerir. VM 'nizin günlüğünü görüntülemek için Azure portal etkinlik günlüğü ' ne tıklayabilirsiniz.

Etkinlik günlüğünde yapabileceğiniz bazı şeyler şunlardır:

- [Etkinlik günlüğü olayında uyarı](../articles/azure-monitor/platform/activity-logs-overview.md)oluşturma.
- Bir üçüncü taraf hizmeti veya Power BI gibi özel analizler tarafından alım için [bir olay hub 'ına akış](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) .
- [Power BI içerik paketini](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)kullanarak Power BI analiz edin.
- Arşiv veya el ile inceleme için [bir depolama hesabına kaydedin](../articles/azure-monitor/platform/archive-activity-log.md) . Günlük profilini kullanarak saklama süresini (gün olarak) belirtebilirsiniz.

Etkinlik günlüğü verilerine [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), [Azure CLı](https://docs.microsoft.com/cli/azure/monitor)veya [izleme REST API 'lerini](https://docs.microsoft.com/rest/api/monitor/)kullanarak da erişebilirsiniz.

[Azure Kaynak günlükleri](../articles/azure-monitor/platform/resource-logs-overview.md) , kendi işlemi hakkında zengin ve sık veriler sağlayan, VM 'niz tarafından yayılan günlüklerdir. Kaynak günlükleri, VM 'de gerçekleştirilen işlemler hakkında Öngörüler sunarak etkinlik günlüğünden farklıdır.

Tanılama günlükleri ile gerçekleştirebileceğiniz işlemlerden bazıları şunlardır:

- Denetim veya el ile inceleme için [bunları bir depolama hesabına kaydedin](../articles/azure-monitor/platform/archive-diagnostic-logs.md) . Kaynak tanılama ayarlarını kullanarak saklama süresini (gün olarak) belirtebilirsiniz.
- [Bunları](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) bir üçüncü taraf hizmeti veya Power BI gibi özel analizler tarafından alma için Event Hubs.
- [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md)ile çözümleyin.

## <a name="advanced-monitoring"></a>Gelişmiş izleme

Azure VM ve sanal makine ölçek kümeleri tarafından desteklenen uygulama veya hizmetin görünürlüğü için, Konuk işletim sistemi veya VM 'de çalışan iş yüküyle ilgili sorunların, uygulamanın kullanılabilirliğini veya performansını etkileyip etkilemediğini anlamak için tanımlanması veya bir uygulamayla ilgili sorun hem [VM'ler için Azure izleyici](../articles/azure-monitor/insights/vminsights-overview.md) hem de [Application Insights](../articles/azure-monitor/app/app-insights-overview.md)etkinleştirin.

VM'ler için Azure İzleyici, Windows ve Linux sanal makinelerinizin farklı süreçler ve diğer kaynaklardaki ve dış süreçlerdeki birbirine bağlı bağımlılıklar dahil olmak üzere, Windows ve Linux VM 'lerinizin performansını ve sistem durumunu analiz ederek ölçeklendirerek Azure sanal makinelerinizi (VM keşfede. Sorun araştırmada ve sanal makinelerinizin kapasitesini değerlendirmekte yardımcı olacak çeşitli eğilim performansı grafikleri içerir. Bağımlılık haritasında, izlenen ve izlenmeyen makineler, işlem ve bu makineler arasında etkin ağ bağlantıları gösterilir ve standart ağ bağlantısı ölçümlerine sahip eğilim grafikleri gösterilmektedir. Application Insights ile birlikte, uygulamanızı izler ve HTTP istekleri, özel durumlar vb. gibi Telemetriyi yakalayın. böylece, VM 'Ler ve uygulamanız arasındaki sorunları ilişkilendirebilmenizi sağlayabilirsiniz. VM'ler için Azure İzleyici tarafından toplanan izleme verilerinden algılanan önemli koşullar hakkında uyarı almak için [Azure izleyici uyarılarını](../articles/azure-monitor/platform/alerts-overview.md) yapılandırın.

## <a name="next-steps"></a>Sonraki adımlar

- Azure CLı ile [bir Windows sanal makinesini Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) veya [Linux sanal makinesini izleme](../articles/virtual-machines/linux/tutorial-monitoring.md)bölümündeki adımları izleyin.

- [İzleme ve tanılama](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)etrafında en iyi uygulamalar hakkında daha fazla bilgi edinin.
