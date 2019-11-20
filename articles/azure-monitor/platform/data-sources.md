---
title: Azure Izleyici 'de veri kaynakları | Microsoft Docs
description: Azure kaynaklarınızın ve üzerinde çalışan uygulamaların sistem durumunu ve performansını izlemek için kullanılabilecek verileri açıklar.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/23/2019
ms.openlocfilehash: b93bf4f67c2581b7cac476b83fc130bf344cfa83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476731"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Azure Izleyici için izleme verileri kaynakları
Azure Izleyici, [günlükleri](data-platform-logs.md) ve [ölçümleri](data-platform-metrics.md)içeren [ortak bir izleme verileri platformunu](data-platform.md) temel alır. Bu platforma verilerin toplanması, birden fazla kaynaktan gelen verilerin Azure Izleyici 'de ortak bir araç kümesi kullanılarak analiz edilmesini sağlar. İzleme verileri belirli senaryoları desteklemek için başka konumlara de gönderilebilir ve bazı kaynaklar günlüklere veya ölçümlere toplanmadan önce diğer konumlara yazılabilir.

Bu makalede, Azure tarafından oluşturulan izleme verilerine ek olarak Azure Izleyici tarafından toplanan izleme verilerinin farklı kaynakları açıklanmaktadır. Bu verilerin farklı konumlara toplanması için gereken yapılandırma hakkında ayrıntılı bilgilere bağlantılar sağlanır.

## <a name="application-tiers"></a>Uygulama katmanları

Azure uygulamalarından izlenen veri izleme kaynakları, uygulamanızın kendisi ve alt katmanların Azure platformunun bileşenleri haline gelen en yüksek katmanlarından oluşan katmanlar halinde organize edilebilir. Her katmandaki verilere erişim yöntemi farklılık gösterir. Uygulama katmanları aşağıdaki tabloda özetlenmiştir ve her katmandaki izleme verileri kaynakları aşağıdaki bölümlerde sunulmaktadır. Her bir veri konumunun açıklaması ve verilerine nasıl erişebileceğiniz hakkında bilgi için bkz. [Azure 'da veri konumlarını izleme](data-locations.md) .


![İzleme katmanları](../media/overview/overview.png)


### <a name="azure"></a>Azure
Aşağıdaki tabloda, Azure 'a özgü uygulama katmanları kısaca açıklanmaktadır. Aşağıdaki bölümlerde yer alan her biri hakkında daha fazla ayrıntı için bağlantıyı takip edin.

| Katman | Açıklama | Collection yöntemi |
|:---|:---|:---|
| [Azure kiracısı](#azure-tenant) | Azure Active Directory gibi kiracı düzeyinde Azure hizmetlerinin çalışması hakkındaki veriler. | Portalda AAD verilerini görüntüleyin veya bir kiracı tanılama ayarı kullanarak koleksiyonu Azure Izleyici 'ye yapılandırın. |
| [Azure aboneliği](#azure-subscription) | Azure aboneliğinizdeki Kaynak Yöneticisi ve hizmet durumu gibi çapraz kaynak hizmetlerinin sistem durumu ve yönetimiyle ilgili veriler. | Portalda görüntüleyin veya bir günlük profili kullanarak Azure Izleyici 'de koleksiyonu yapılandırın. |
| [Azure kaynakları](#azure-resources) |  Her bir Azure kaynağının işlemi ve performansı hakkındaki veriler. | Ölçümler otomatik olarak toplanır, Ölçüm Gezgini içinde görüntüleyin.<br>Azure Izleyici 'de günlükleri toplamak için tanılama ayarlarını yapılandırın.<br>Belirli kaynak türlerine yönelik daha ayrıntılı izleme için çözümleri ve öngörüleri izleme. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, diğer bulut veya şirket içi 
Aşağıdaki tabloda, Azure 'da, başka bir bulutta veya şirket içinde olabilecek uygulama katmanları kısaca açıklanmaktadır. Aşağıdaki bölümlerde yer alan her biri hakkında daha fazla ayrıntı için bağlantıyı takip edin.

| Katman | Açıklama | Collection yöntemi |
|:---|:---|:---|
| [İşletim sistemi (konuk)](#operating-system-guest) | İşlem kaynaklarında işletim sistemiyle ilgili veriler. | VM'ler için Azure İzleyici destekleme bağımlılıklarını toplamak için istemci veri kaynaklarını Azure Izleyici 'ye ve bağımlılık aracısına toplamak üzere Log Analytics Aracısı 'nı yükler.<br>Azure sanal makineler için Azure Izleyici 'de günlükleri ve ölçümleri toplamak üzere Azure tanılama uzantısı 'nı yüklersiniz. |
| [Uygulama kodu](#application-code) | Performans izlemeleri, uygulama günlükleri ve Kullanıcı telemetrisi dahil gerçek uygulamanın ve kodun performansı ve işlevleri hakkındaki veriler. | Kodunuzu Application Insights veri toplamak için işaretleyin. |
| [Özel kaynaklar](#custom-sources) | Dış hizmetlerden veya diğer bileşenlerden veya cihazlardan gelen veriler. | Günlük veya ölçüm verilerini Azure Izleyici 'de herhangi bir REST istemcisinden toplayın. |

## <a name="azure-tenant"></a>Azure kiracısı
Azure kiracınızla ilgili telemetri, Azure Active Directory gibi kiracı genelindeki hizmetlerden toplanır.

![Azure kiracı koleksiyonu](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Denetim günlüklerini Azure Active Directory
[Azure Active Directory raporlama](../../active-directory/reports-monitoring/overview-reports.md) , oturum açma etkinliğinin geçmişini ve belirli bir kiracı içinde yapılan değişikliklerin denetim izini içerir. 

| Hedef | Açıklama | Başvuru |
|:---|:---|:---|
| Azure İzleyici Günlükleri | Azure AD günlüklerini diğer izleme verileriyle çözümlemek için Azure Izleyici 'de toplanacak şekilde yapılandırın. | [Azure AD günlüklerini Azure Izleyici günlükleri ile tümleştirme (Önizleme)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Depolama | Azure AD günlüklerini arşivleme için Azure depolama 'ya aktarın. | [Öğretici: Azure AD günlüklerini bir Azure depolama hesabına arşivleme (Önizleme)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Olay Hub'ı | Event Hubs kullanarak Azure AD günlüklerini diğer konumlara akışı yapın. | [Öğretici: Azure Olay Hub 'ına (Önizleme) günlük Azure Active Directory akışı](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Azure aboneliği
Azure aboneliğinizin sistem durumu ve işlemiyle ilgili telemetri.

![Azure aboneliği](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure etkinlik günlüğü 
[Azure etkinlik günlüğü](activity-logs-overview.md) , Azure aboneliğinizdeki kaynaklarda yapılan tüm yapılandırma değişiklikleri kayıtlarıyla birlikte hizmet durumu kayıtlarını içerir. Etkinlik günlüğü tüm Azure kaynakları için kullanılabilir ve _dış_ görünümünü temsil eder.

| Hedef | Açıklama | Başvuru |
|:---|:---|
| Etkinlik günlüğü | Etkinlik günlüğü, Azure Izleyici menüsünden görüntüleyebileceğiniz veya etkinlik günlüğü uyarıları oluşturmak için kullanabileceğiniz kendi veri deposunda toplanır. | [Azure portal etkinlik günlüğünü sorgulama](activity-log-view.md#azure-portal) |
| Azure İzleyici Günlükleri | Azure Izleyici günlüklerini, diğer izleme verileriyle çözümlemek için etkinlik günlüğünü toplayacak şekilde yapılandırın. | [Azure Izleyici 'de Log Analytics çalışma alanında Azure etkinlik günlüklerini toplayın ve çözümleyin](activity-log-collect.md) |
| Azure Depolama | Arşivleme için etkinlik günlüğünü Azure Storage 'a aktarın. | [Etkinlik günlüğünü Arşivle](activity-log-export.md#archive-activity-log)  |
| Event Hubs | Event Hubs kullanarak etkinlik günlüğünü diğer konumlara akış | [Etkinlik günlüğünü Olay Hub 'ına akış](activity-log-export.md#stream-activity-log-to-event-hub). |

### <a name="azure-service-health"></a>Azure Hizmet Durumu
[Azure hizmet durumu](../../service-health/service-health-overview.md) , aboneliğinizde uygulamanızın ve kaynaklarınızın bağlı olduğu Azure hizmetlerinin sistem durumu hakkında bilgi sağlar.

| Hedef | Açıklama | Başvuru |
|:---|:---|:---|
| Etkinlik günlüğü<br>Azure İzleyici Günlükleri | Hizmet durumu kayıtları, Azure etkinlik günlüğünde depolanır, böylece bunları Azure portal görüntüleyebilir veya etkinlik günlüğü ile gerçekleştirebileceğiniz diğer etkinlikleri gerçekleştirebilirsiniz. | [Azure portalını kullanarak hizmet durumu bildirimlerini görüntüleme](service-notifications.md) |


## <a name="azure-resources"></a>Azure kaynakları
Ölçümler ve kaynak günlükleri, Azure kaynaklarının _iç_ işlemi hakkında bilgiler sağlar. Bunlar pek çok Azure hizmeti için kullanılabilir ve çözüm ve Öngörüler izlemek belirli hizmetler için ek veriler toplar.

![Azure Kaynak koleksiyonu](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Platform ölçümleri 
Çoğu Azure hizmeti, performans ve işlemlerini yansıtan [Platform ölçümlerini](data-platform-metrics.md) doğrudan ölçüm veritabanına gönderir. Belirli [ölçümler her kaynak türü için farklılık gösterecektir](metrics-supported.md). 

| Hedef | Açıklama | Başvuru |
|:---|:---|:---|
| Azure Izleyici ölçümleri | Platform ölçümleri, Azure Izleyici ölçümleri veritabanına hiçbir yapılandırma olmadan yazar. Ölçüm Gezgini platform ölçümlerine erişin.  | [Azure Ölçüm Gezgini'ni kullanmaya başlama](metrics-getting-started.md)<br>[Azure Izleyici ile desteklenen ölçümler](metrics-supported.md) |
| Azure İzleyici Günlükleri | Log Analytics kullanarak, popüler ve diğer analizler için platform ölçümlerini günlüklere kopyalayın. | [Azure tanılama doğrudan Log Analytics](resource-logs-collect-workspace.md) |
| Event Hubs | Event Hubs kullanarak ölçümleri diğer konumlara akış. |[Dış bir araçla tüketim için Azure izleme verilerini bir olay hub 'ına akış](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Kaynak günlükleri
[Kaynak günlükleri](resource-logs-overview.md) bir Azure kaynağının _iç_ işlemine ilişkin öngörüler sağlar.  Kaynak günlükleri otomatik olarak oluşturulur, ancak her kaynak için toplanmaları için bir hedef belirtmek üzere bir tanılama ayarı oluşturmanız gerekir.

Kaynak günlüklerinin yapılandırma gereksinimleri ve içerikleri kaynak türüne göre farklılık gösterir ve tüm hizmetler henüz bunları oluşturmaz. Her hizmet hakkındaki ayrıntılar ve ayrıntılı yapılandırma yordamlarına bağlantılar için bkz. [Azure Kaynak günlükleri Için desteklenen hizmetler, şemalar ve Kategoriler](diagnostic-logs-schema.md) . Hizmet Bu makalede listelenmiyorsa, bu hizmet şu anda kaynak günlükleri oluşturmaz.

| Hedef | Açıklama | Başvuru |
|:---|:---|:---|
| Azure İzleyici Günlükleri | Kaynak günlüklerini, toplanan diğer günlük verileriyle analiz edilmek üzere Azure Izleyici günlüklerine gönderin. | [Azure Izleyici 'de Log Analytics çalışma alanında Azure Kaynak günlüklerini toplayın](resource-logs-collect-storage.md) |
| Depolama | Arşivleme için kaynak günlüklerini Azure Storage 'a gönderin. | [Azure Kaynak günlüklerini arşivleme](resource-logs-collect-workspace.md) |
| Event Hubs | Event Hubs kullanarak kaynak günlüklerini diğer konumlara akış. |[Azure Kaynak günlüklerini bir olay hub 'ına akış](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>İşletim sistemi (konuk)
Azure 'da, diğer bulutlarda ve Şirket içindeki işlem kaynaklarını izlemek için bir konuk işletim sistemi vardır. Bir veya daha fazla aracı yüklemesiyle, Azure hizmetleri ile aynı izleme araçlarıyla analiz etmek için konuktaki Telemetriyi Azure Izleyici 'ye toplayabilirsiniz.

![Azure işlem kaynağı koleksiyonu](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure tanılama uzantısı
Azure sanal makineleri için Azure Tanılama uzantısının etkinleştirilmesi, Azure bulut hizmeti (klasik) Web ve çalışan rolleri, sanal makineler, sanal makine dahil olmak üzere Azure işlem kaynaklarının Konuk işletim sisteminden Günlükler ve ölçümler toplamanıza olanak tanır. Ölçek Kümeleri ve Service Fabric.

| Hedef | Açıklama | Başvuru |
|:---|:---|:---|
| Depolama | Tanılama uzantısını etkinleştirdiğinizde, varsayılan olarak bir depolama hesabına yazar. | [Azure Depolama’daki tanılama verilerini depolama ve görüntüleme](diagnostics-extension-to-storage.md) |
| Azure Izleyici ölçümleri | Tanılama uzantısını performans sayaçlarını toplayacak şekilde yapılandırdığınızda, bunlar Azure Izleyici ölçümleri veritabanına yazılır. | [Windows sanal makinesi için Kaynak Yöneticisi şablonu kullanarak Azure Izleyici ölçüm deposuna Konuk işletim sistemi ölçümleri gönderme](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Application Insights günlükleri | Uygulamanızı destekleyen işlem kaynağından, diğer uygulama verileriyle analiz edilecek Günlükler ve performans sayaçlarını toplayın. | [Bulut hizmeti, sanal makine veya Service Fabric tanılama verilerini Application Insights gönderin](diagnostics-extension-to-application-insights.md) |
| Event Hubs | Tanılama uzantısını, Event Hubs kullanarak verileri diğer konumlara akışa almak için yapılandırın.  | [Azure Tanılama Event Hubs kullanarak etkin yoldaki verileri akışa alma](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Log Analytics Aracısı 
Windows veya Linux sanal makinelerinizin kapsamlı izleme ve yönetimi için Log Analytics aracısını yükler. Sanal makine Azure 'da, başka bir bulutta veya şirket içinde çalışıyor olabilir.

| Hedef | Açıklama | Başvuru |
|:---|:---|:---|
| Azure İzleyici Günlükleri | Log Analytics Aracısı doğrudan veya System Center Operations Manager aracılığıyla Azure Izleyici 'ye bağlanır ve yapılandırdığınız veri kaynaklarından veya uygulamalar için ek Öngörüler sağlayan izleme çözümleriyle veri toplamanıza olanak tanır sanal makinede çalışıyor. | [Azure Izleyici 'de aracı veri kaynakları](agent-data-sources.md)<br>[Operations Manager Azure Izleyici 'ye bağlama](om-agents.md) |


### <a name="azure-monitor-for-vms"></a>VM'ler için Azure İzleyici 
[VM'ler için Azure izleyici](../insights/vminsights-overview.md) , sanal makineler için, hizmet durumu ve VM sistem durumu da dahil olmak üzere temel Azure izleyici işlevlerinin ötesinde özellikler sağlayan özelleştirilmiş bir izleme deneyimi sağlar. Bu, sanal makine ve dış işlem bağımlılıkları üzerinde çalışan işlemler hakkında bulunan verileri toplamak üzere Log Analytics aracısıyla tümleştirilen Windows ve Linux sanal makinelerinde Dependency Agent gerektirir.

| Hedef | Açıklama | Başvuru |
|:---|:---|:---|
| Azure İzleyici Günlükleri | Aracıdaki işlem ve bağımlılıklarla ilgili verileri depolar. | [Uygulama bileşenlerini anlamak için VM'ler için Azure İzleyici (Önizleme) eşlemesini kullanma](../insights/vminsights-maps.md) |
| VM depolaması | VM'ler için Azure İzleyici, özel bir konumda durum bilgilerini depolar. Bu, yalnızca [Azure Kaynak sistem durumu REST API](/rest/api/resourcehealth/)ek olarak Azure Portal VM'ler için Azure izleyici kullanılabilir. | [Azure sanal makinelerinizin sistem durumunu anlayın](../insights/vminsights-health.md)<br>[Azure Kaynak durumu REST API](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>Uygulama kodu
Azure Izleyici 'de ayrıntılı uygulama izleme, çeşitli platformlarda çalışan uygulamalardan veri toplayan [Application Insights](https://docs.microsoft.com/azure/application-insights/) ile yapılır. Uygulama Azure 'da, başka bir bulutta veya şirket içinde çalışıyor olabilir.

![Uygulama verileri toplama](media/data-sources/applications.png)


### <a name="application-data"></a>Uygulama verileri
Bir izleme paketi yükleyerek bir uygulama için Application Insights etkinleştirdiğinizde, uygulamanın performansına ve operasyonla ilgili ölçümleri ve günlükleri toplar. Application Insights, topladığı verileri diğer veri kaynakları tarafından kullanılan aynı Azure Izleyici veri platformunda depolar. Bu verileri çözümlemek için kapsamlı araçlar içerir, ancak Ölçüm Gezgini ve Log Analytics gibi araçları kullanarak diğer kaynaklardaki verilerle de analiz edebilirsiniz.

| Hedef | Açıklama | Başvuru |
|:---|:---|:---|
| Azure İzleyici Günlükleri | Sayfa görünümleri, uygulama istekleri, özel durumlar ve izlemeler dahil olmak üzere uygulamanız hakkındaki işletimsel veriler. | [Azure Izleyici 'de günlük verilerini çözümleme](../log-query/log-query-overview.md) |
|                    | Uygulama Haritası ve telemetri bağıntısını desteklemek için uygulama bileşenleri arasındaki bağımlılık bilgileri. | [Application Insights telemetri bağıntısı](../app/correlation.md) <br> [Uygulama Eşlemesi](../app/app-map.md) |
|            | Uygulamanızın genel Internet 'teki farklı konumlardan kullanılabilirliğini ve yanıt hızını test eden kullanılabilirlik testlerinin sonuçları. | [Web sitelerinin kullanılabilirliğini ve yanıt hızını izleme](../app/monitor-web-app-availability.md) |
| Azure Izleyici ölçümleri | Application Insights, uygulamanızda Azure Izleyici ölçümleri veritabanına tanımladığınız özel ölçümlere ek olarak uygulamanın performansını ve işlemini açıklayan ölçümleri toplar. | [Application Insights 'de günlük tabanlı ve önceden toplanmış ölçümler](../app/pre-aggregated-metrics-log-metrics.md)<br>[Özel olaylar ve ölçümler için Application Insights API 'SI](../app/api-custom-events-metrics.md) |
| Azure Depolama | Arşivleme için uygulama verilerini Azure depolama 'ya gönderin. | [Application Insights’tan telemetriyi dışarı aktarma](../app/export-telemetry.md) |
|            | Kullanılabilirlik testlerinin ayrıntıları Azure Storage 'da depolanır. Yerel Analize indirmek için Azure portal Application Insights kullanın. Kullanılabilirlik testlerinin sonuçları Azure Izleyici günlüklerinde depolanır. | [Web sitelerinin kullanılabilirliğini ve yanıt hızını izleme](../app/monitor-web-app-availability.md) |
|            | Profil Oluşturucu izleme verileri Azure depolama 'da depolanır. Yerel Analize indirmek için Azure portal Application Insights kullanın.  | [Application Insights ile Azure 'da üretim uygulamaları profilini yapın](../app/profiler-overview.md) 
|            | Özel durumların bir alt kümesi için yakalanan hata ayıklama anlık görüntüsü verileri Azure depolama 'da depolanır. Yerel Analize indirmek için Azure portal Application Insights kullanın.  | [Anlık görüntülerin nasıl çalıştığı](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Çözümleri ve öngörüleri izleme
[Çözümleri](../insights/solutions.md) ve [öngörüleri](../insights/insights-overview.md) izlemek, belirli bir hizmet veya uygulamanın çalışması hakkında ek Öngörüler sağlamak için veri toplar. Farklı uygulama katmanlarında ve hatta birden çok katmanda kaynakları ele alabilir.

### <a name="monitoring-solutions"></a>İzleme çözümleri

| Hedef | Açıklama | Başvuru
|:---|:---|:---|
| Azure İzleyici Günlükleri | Çözümleri izleme, Azure Izleyici günlüklerine veri toplar ve bu, genellikle çözüme dahil edilen sorgu dili veya [Görünümler](view-designer.md) kullanılarak analiz edilebilir. | [Azure 'da çözüm izlemek için veri toplama ayrıntıları](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Kapsayıcılar için Azure İzleyici
[Kapsayıcılar Için Azure izleyici](../insights/container-insights-overview.md) , [Azure Kubernetes HIZMETI (aks)](/azure/aks/)için özelleştirilmiş bir izleme deneyimi sağlar. Aşağıdaki tabloda açıklanan bu kaynaklarla ilgili ek veriler toplar.

| Hedef | Açıklama | Başvuru |
|:---|:---|:---|
| Azure İzleyici Günlükleri | Envanter, Günlükler ve olaylar gibi AKS için izleme verilerini depolar. Ölçüm verileri, portalda analiz işlevlerinin yararlanmak için günlüklere da depolanır. | [Kapsayıcılar için Azure İzleyici ile AKS kümesi performansını anlama](../insights/container-insights-analyze.md) |
| Azure Izleyici ölçümleri | Ölçüm verileri, görselleştirme ve uyarıları yönlendirmek için ölçüm veritabanında depolanır. | [Ölçüm Gezgininde kapsayıcı ölçümlerini görüntüleme](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | , Portaldaki Azure Kubernetes hizmeti (AKS) kapsayıcı günlüklerine (stdout/stderror), olaylara ve pod ölçümlere doğrudan erişim sağlar. | [Kubernetes günlüklerini, olayları ve pod ölçümlerini gerçek zamanlı olarak görüntüleme](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>VM'ler için Azure İzleyici
[VM'ler için Azure izleyici](../insights/vminsights-overview.md) , sanal makineleri izlemeye yönelik özelleştirilmiş bir deneyim sağlar. VM'ler için Azure İzleyici tarafından toplanan verilerin açıklaması yukarıdaki [Işletim sistemi (konuk)](#operating-system-guest) bölümüne dahil edilir.

## <a name="custom-sources"></a>Özel kaynaklar
Bir uygulamanın standart katmanlarına ek olarak, diğer veri kaynaklarıyla toplanamayacak telemetri içeren diğer kaynakları izlemeniz gerekebilir. Bu kaynaklar için, bu verileri Azure Izleyici API 'SI kullanarak ölçümler veya günlüklere yazın.

![Özel koleksiyon](media/data-sources/custom.png)

| Hedef | Yöntem | Açıklama | Başvuru |
|:---|:---|:---|:---|
| Azure İzleyici Günlükleri | Veri Toplayıcı API’si | Herhangi bir REST istemcisinden günlük verilerini toplayın ve Log Analytics çalışma alanında depolayın. | [HTTP Veri Toplayıcı API 'SI ile günlük verilerini Azure Izleyici 'ye gönderme (Genel Önizleme)](data-collector-api.md) |
| Azure Izleyici ölçümleri | Özel ölçümler API 'SI | Azure Izleyici ölçüm veritabanındaki herhangi bir REST istemcisinden ve mağazalardan ölçüm verileri toplayın. | [Bir Azure kaynağı için Azure Izleyici ölçüm deposuna bir REST API kullanarak özel ölçümler gönderme](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Diğer hizmetler
Azure 'daki diğer hizmetler, verileri Azure Izleyici veri platformuna yazar. Bu, Azure Izleyici tarafından toplanan verilerle bu hizmetler tarafından toplanan verileri analiz etmenize ve aynı analiz ve görselleştirme araçlarından faydalanmanıza olanak sağlar.

| Hizmet | Hedef | Açıklama | Başvuru |
|:---|:---|:---|:---|
| [Azure Güvenlik Merkezi](/azure/security-center/) | Azure İzleyici Günlükleri | Azure Güvenlik Merkezi, topladığı güvenlik verilerini Azure Izleyici tarafından toplanan diğer günlük verileriyle analiz edilmesini sağlayan bir Log Analytics çalışma alanında depolar.  | [Azure Güvenlik Merkezinde veri toplama](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure İzleyici Günlükleri | Azure Sentinel, farklı veri kaynaklarından topladığı verileri, Azure Izleyici tarafından toplanan diğer günlük verileriyle analiz edilmesini sağlayan bir Log Analytics çalışma alanında depolar.  | [Veri kaynaklarını bağlama](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Sonraki adımlar

- [Azure izleyici tarafından toplanan izleme verilerinin türleri](data-platform.md) ve bu verilerin nasıl görüntüleneceği ve çözümleneceği hakkında daha fazla bilgi edinin.
- [Azure kaynaklarının verileri depolayan](data-locations.md) ve bu verilere nasıl erişebilecekleri farklı konumları listeleyin. 
