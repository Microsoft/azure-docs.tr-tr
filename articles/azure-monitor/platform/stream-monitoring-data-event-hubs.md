---
title: Azure izleme verilerini Olay Hub 'ına akış
description: Verileri bir iş ortağı SıEM veya analiz aracına almak için Azure izleme verilerinizi bir olay hub 'ına nasıl akıtirecağınızı öğrenin.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 84cb2b465735532ff44e35ab7a2fe4e9bb224e61
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150008"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Azure izleme verilerini bir olay hub 'ına akış
Azure Izleyici, Azure 'da, diğer bulutlarda ve şirket içinde bulunan uygulamalar ve hizmetler için eksiksiz bir tam yığın izleme çözümü sağlar. Verileri analiz etmek ve farklı izleme senaryolarında kullanmak için Azure Izleyici kullanmanın yanı sıra, bu dosyayı ortamınızdaki diğer izleme araçlarına göndermeniz gerekebilir. Çoğu durumda, izleme verilerinin dış araçlara akışını sağlamak için en etkili yöntem [Azure Event Hubs](/azure/event-hubs/)kullanmaktır. Bu makalede, farklı kaynaklardan bir olay hub 'ına izleme verileri akışını ve ayrıntılı kılavuza bağlantıları nasıl alabileceğiniz hakkında kısa bir açıklama sunulmaktadır.


## <a name="create-an-event-hubs-namespace"></a>Event Hubs ad alanı oluşturma

Herhangi bir veri kaynağı için akışı yapılandırmadan önce, [bir Event Hubs ad alanı ve Olay Hub 'ı oluşturmanız](../../event-hubs/event-hubs-create.md)gerekir. Bu ad alanı ve Olay Hub 'ı tüm izleme verilerinizin hedefdir. Event Hubs ad alanı, aynı erişim ilkesini paylaşan bir olay hub 'larının mantıksal gruplandırmasıdır. Bu, depolama hesabının bu depolama hesabı içinde ayrı blob 'lara sahip olduğu gibidir. İzleme verileri akışı için kullandığınız Olay Hub 'ları ad alanı ve Olay Hub 'ları hakkında aşağıdaki ayrıntıları göz önünde bulundurun:

* Üretilen iş birimi sayısı, Olay Hub 'larınız için üretilen iş ölçeğini artırmanıza olanak tanır. Genellikle yalnızca bir üretilen iş birimi gereklidir. Günlük kullanımınız arttıkça ölçeği büyütmeniz gerekiyorsa, ad alanı için üretilen iş birimi sayısını el ile artırabilir veya otomatik enflasyon sağlayabilirsiniz.
* Bölüm sayısı, birçok tüketici genelinde tüketim paralel hale getirmek sağlar. Tek bir bölüm, saniyede en fazla 20 Mbps veya yaklaşık 20.000 ileti destekleyebilir. Verileri kullanan araca bağlı olarak, birden çok bölümden kullanmayı desteklemiyor olabilir veya desteklemeyebilir. Ayarlanacak bölüm sayısı hakkında emin değilseniz, bunun için dört bölüm başlamak mantıklı değildir.
* Olay Hub 'ınızdaki ileti bekletmesini en az 7 güne ayarlayın. Tüketim aracınız bir günden daha uzun bir süre kapanıyorsa bu, aracın 7 güne kadar eski olaylar için kaldığınız yerden devam edebilmesini sağlar.
* Olay Hub 'ınız için varsayılan tüketici grubunu kullanmanız gerekir. Aynı Olay Hub 'ından aynı verileri tüketmek üzere iki farklı araca sahip olmak için başka tüketici grupları oluşturmanız veya ayrı bir tüketici grubu kullanmanız gerekmez.
* Azure etkinlik günlüğü için bir Event Hubs ad alanı seçmelisiniz ve Azure Izleyici, bu ad alanı içinde _Öngörüler-logs-işletimsel-logs_adlı bir olay hub 'ı oluşturur. Diğer günlük türleri için, mevcut bir olay hub 'ını seçebilir veya Azure Izleyici 'nin günlük kategorisi başına bir olay hub 'ı oluşturmasını sağlayabilirsiniz.
* Giden bağlantı noktası 5671 ve 5672, genellikle olay hub 'ından veri kullanan bilgisayar veya VNET üzerinde açılmalıdır.


## <a name="monitoring-data-available"></a>İzleme verileri kullanılabilir
[Azure izleyici için izleme verileri kaynakları](data-sources.md) , Azure uygulamaları için farklı veri katmanlarını ve her biri için kullanılabilen izleme verileri türlerini açıklar. Aşağıdaki tabloda bu katmanların her biri ve bu verilerin bir olay hub 'ına nasıl akışa alınacağını gösteren bir açıklama listelenmektedir. Daha ayrıntılı bilgi için sağlanan bağlantıları izleyin.

| Katman | Veriler | Yöntem |
|:---|:---|:---|
| [Azure kiracısı](data-sources.md#azure-tenant) | Denetim günlüklerini Azure Active Directory | AAD kiracınızda kiracı tanılama ayarını yapılandırın. Ayrıntılar için bkz. [öğretici: Azure Olay Hub 'ına akış Azure Active Directory günlükleri](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) . |
| [Azure aboneliği](data-sources.md#azure-subscription) | Azure Etkinlik Günlüğü | Etkinlik günlüğü olaylarını Event Hubs dışarı aktarmak için bir günlük profili oluşturun.  Ayrıntılar için bkz. [Azure etkinlik günlüğünü depolama alanına veya azure Event Hubs dışa aktarma](activity-log-export.md) . |
| [Azure kaynakları](data-sources.md#azure-resources) | Platform ölçümleri<br>Tanılama günlükleri |Her iki tür veri, bir kaynak tanılama ayarı kullanılarak bir olay hub 'ına gönderilir. Ayrıntılar için bkz. [Azure tanılama günlüklerini bir olay hub 'ına akış](resource-logs-stream-event-hubs.md) . |
| [İşletim sistemi (konuk)](data-sources.md#operating-system-guest) | Azure sanal makineleri | Azure 'daki Windows ve Linux sanal makinelerine [Azure tanılama uzantısını](diagnostics-extension-overview.md) yükler. Windows VM 'Lerle ilgili ayrıntılar için [Event Hubs kullanarak bkz. Azure Tanılama verileri etkin yolda](diagnostics-extension-stream-event-hubs.md) ve Linux VM 'lerinde Ayrıntılar için [ölçümleri ve günlükleri Izlemek üzere Linux Tanılama uzantısı 'nı kullanın](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) . |
| [Uygulama kodu](data-sources.md#application-code) | Application Insights | Application Insights, Olay Hub 'larına veri akışı için doğrudan bir yöntem sağlamaz. Application Insights verilerinin sürekli olarak bir depolama hesabına [dışarı aktarılmasını ayarlayabilir](../../azure-monitor/app/export-telemetry.md) ve ardından mantıksal uygulama [ile el ile akışta](#manual-streaming-with-logic-app)açıklanan şekilde verileri bir olay hub 'ına göndermek için bir mantıksal uygulama kullanabilirsiniz. |

## <a name="manual-streaming-with-logic-app"></a>Mantıksal uygulamayla el ile akış
Bir olay hub 'ına doğrudan akış yapamazsınız, Azure depolama 'ya yazabilir ve ardından [BLOB depolamadan veri alıp](../../connectors/connectors-create-api-azureblobstorage.md#add-action) [Olay Hub 'ına ileti olarak](../../connectors/connectors-create-api-azure-event-hubs.md#add-action)gönderen bir zaman tetiklenen mantıksal uygulama kullanabilirsiniz. 


## <a name="tools-with-azure-monitor-integration"></a>Azure Izleyici tümleştirmesi ile araçlar

İzleme verilerinizi Azure Izleyici ile bir olay hub 'ına yönlendirme, dış SıEM ve izleme araçlarıyla kolayca tümleştirmenize olanak sağlar. Azure Izleyici tümleştirmesi ile araçlara örnek olarak şunlar verilebilir:

| Araç | Açıklama |
|:---|:---|
|  IBM QRadar | Microsoft Azure DSM ve Microsoft Azure Olay Hub 'ı Protokolü [IBM Support Web sitesinden](https://www.ibm.com/support)indirilebilir. Azure ile tümleştirme hakkında daha fazla bilgi için bkz. [QRadar DSM yapılandırması](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | [Splunk Için Azure Izleyici eklentisi](https://splunkbase.splunk.com/app/3534/) , Splunkbase 'de kullanılabilen açık kaynaklı bir projem. Belgeler, [Azure Izleyici eklentisi Ile splunk için](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk)kullanılabilir.<br><br> Splunk örneğiniz için bir eklenti yükleyemezseniz, örneğin bir ara sunucu kullanıyorsanız veya splunk bulutu üzerinde çalıştırıyorsanız, bu olayları splunk [Için Azure işlevini](https://github.com/Microsoft/AzureFunctionforSplunkVS)kullanarak splunk http olay toplayıcısına iletebilir. Bu, Olay Hub 'ında yeni iletiler tarafından tetiklenir. |
| SumoLogic | Olay Hub 'ından veri tüketmek üzere SumoLogic ayarlamaya yönelik yönergeler [, Olay Hub 'ından Azure denetim uygulamasının günlüklerini toplar](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | Arcgörüş Azure Olay Hub 'ı akıllı Bağlayıcısı, [arcgözetimi akıllı bağlayıcı koleksiyonunun](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852)bir parçası olarak kullanılabilir. |
| Syslog sunucusu | Azure Izleyici verilerini doğrudan bir Syslog sunucusuna akışını istiyorsanız, bir [Azure işlevine dayalı bir çözüm](https://github.com/miguelangelopereira/azuremonitor2syslog/)kullanabilirsiniz.
| Logrhythd | Bir olay hub 'ından günlükleri toplamak için Logrhythd 'yi ayarlama yönergeleri [burada](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)bulunabilir. 


## <a name="next-steps"></a>Sonraki Adımlar
* [Etkinlik günlüğünü bir depolama hesabına arşivleme](../../azure-monitor/platform/archive-activity-log.md)
* [Azure etkinlik günlüğü 'ne genel bakış konusunu okuyun](../../azure-monitor/platform/activity-logs-overview.md)
* [Etkinlik günlüğü olayına göre uyarı ayarlama](../../azure-monitor/platform/alerts-log-webhook.md)


