---
title: Azure Izleyici tarafından izlenen
description: Azure Izleyici tarafından izlenen tüm hizmetlerin ve diğer kaynakların başvurusu.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 513f262f5d09cf56c4506a4f20c9aa41507c2abd
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515286"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Azure Izleyici ile neler izlenir?
Bu makalede, Azure Izleyici tarafından izlenen farklı uygulama ve hizmetler açıklanır. 

## <a name="insights-and-core-solutions"></a>Öngörüler ve temel çözümler
Temel Öngörüler ve çözümler Azure Izleyici 'nin bir parçası olarak kabul edilir ve Azure için destek ve hizmet düzeyi sözleşmelerini izler. Azure Izleyici 'nin kullanılabildiği tüm Azure bölgelerinde desteklenir.

### <a name="insights"></a>Insights

Öngörüler, belirli uygulamalar ve hizmetler için özelleştirilmiş bir izleme deneyimi sağlar. Hem günlükleri hem de ölçümleri toplar ve analiz eder.

| İçgörü | Açıklama |
|:---|:---|
| [Uygulama Bilgileri](app/app-insights-overview.md) | Canlı Web uygulamanızı herhangi bir platformda izlemek için Genişletilebilir uygulama performans yönetimi (APM) hizmeti. |
| [Kapsayıcı öngörüleri](containers/container-insights-overview.md) | Azure Kubernetes Service (AKS) üzerinde barındırılan Azure Container Instances ya da yönetilen Kubernetes kümelerine dağıtılan kapsayıcı iş yüklerinin performansını izler. |
| [Cosmos DB için Azure Izleyici](insights/cosmosdb-insights-overview.md) | Birleşik etkileşimli bir deneyime sahip tüm Azure Cosmos DB kaynaklarınızın genel performans, başarısızlık, kapasite ve işlemsel sistem durumunun bir görünümünü sağlar. |
| [Ağlar için Azure Izleyici (Önizleme)](insights/network-insights-overview.md) | Tüm ağ kaynağınız için sistem durumu ve ölçümlerinin kapsamlı bir görünümünü sağlar. Gelişmiş arama özelliği, yalnızca Web sitenizin adını arayarak Web sitenizi barındıran kaynağı tanımlama gibi senaryoları etkinleştirerek Kaynak bağımlılıklarını belirlemenize yardımcı olur. |
[Kaynak grupları için Azure Izleyici (Önizleme)](insights/resource-group-insights.md) |  Tek tek kaynaklarınızın karşılaştığı sorunları önceliklendirin ve tanılayın ve kaynak grubunun bir bütün olarak sistem durumu ve performansına göre bağlamı sunar. |
| [Depolama için Azure Izleyici](insights/storage-insights-overview.md) | Azure depolama hizmetleri Performanslarınızın, kapasitesinin ve kullanılabilirliğinden oluşan Birleşik bir görünüm sunarak Azure depolama hesaplarınızın kapsamlı bir şekilde izlenmesini sağlar. |
| [VM öngörüleri](vm/vminsights-overview.md) | Azure sanal makinelerinizi (VM) ve sanal makine ölçek kümelerinizi ölçeklendirerek izler. İşlemlerini ve diğer kaynaklarla dış işlemlere olan bağımlılıklarını izleyerek Windows ve Linux VM'lerinizin performansını ve sistem durumunu analiz eder. |
| [Key Vault için Azure Izleyici (Önizleme)](./insights/key-vault-insights-overview.md) | Key Vault isteklerinizin, performanlarınızın ve gecikmelerinden oluşan Birleşik bir görünüm sunarak anahtar kasalarınızın kapsamlı bir şekilde izlenmesini sağlar. |
| [Redin için Azure önbelleği için Azure Izleyici (Önizleme)](insights/redis-cache-insights-overview.md) |  Genel performans, başarısızlık, kapasite ve işlemsel sistem durumunun Birleşik, etkileşimli bir görünümünü sağlar. |


### <a name="core-solutions"></a>Temel çözümler

Çözümler, belirli bir uygulama veya hizmet için özelleştirilmiş günlük sorgularını ve görünümleri temel alır. Bunlar yalnızca günlükleri toplayıp analiz eder ve Öngörüler için zaman içinde kullanım dışıdır.

| Çözüm | Açıklama |
|:---|:---|
| [Aracı sistem durumu](insights/solution-agenthealth.md) | Log Analytics aracılarının sistem durumunu ve yapılandırmasını çözümleyin. |
| [Uyarı yönetimi](insights/alert-management-solution.md) | System Center Operations Manager, Nagios veya Zabbix ' ten toplanan uyarıları analiz edin. |
| [Hizmet Eşlemesi](vm/service-map.md) | Windows ve Linux sistemlerindeki uygulama bileşenlerini otomatik olarak bulur ve hizmetler arasındaki iletişimi eşler. |



## <a name="azure-services"></a>Azure hizmetleri
Aşağıdaki tabloda Azure hizmetleri ve Azure Izleyici 'de topladıkları veriler listelenmektedir. 

- Ölçümler-hizmet ölçümleri Azure Izleyici ölçümlerine otomatik olarak toplar. 
- Günlükler-hizmet, Azure Izleyici günlüklerinde platform günlüklerini ve ölçümlerini toplayabilen tanılama ayarlarını destekler.
- Öngörüler-hizmet için özelleştirilmiş bir izleme deneyimi sağlayan, hizmet için sunulan bir öngörü vardır.

| Hizmet | Ölçümler | Günlükler | İçgörü | Notlar |
|:---|:---|:---|:---|:---|
|Active Directory | Hayır | Yes | [Evet](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Hayır | Hayır | Hayır |  |
|Active Directory Domain Services | Hayır | Yes | Hayır |  |
|Etkinlik günlüğü | Hayır | Yes | Hayır | |
|Gelişmiş Tehdit Koruması | Hayır | Hayır | Hayır |  |
|Advisor | Hayır | Hayır | Hayır |  |
|AI Builder | Hayır | Hayır | Hayır |  |
|Analysis Services | Yes | Yes | Hayır |  |
|FHIR için API | Hayır | Hayır | Hayır |  |
|API Management | Yes | Yes | Hayır |  |
|App Service | Yes | Yes | Hayır |  |
|AppConfig | Hayır | Hayır | Hayır |  |
|Application Gateway | Yes | Yes | Hayır |  |
|Kanıtlama hizmeti | Hayır | Hayır | Hayır |  |
|Otomasyon | Yes | Yes | Hayır |  |
|Azure Service Manager (RDFE) | Hayır | Hayır | Hayır |  |
|Backup | Hayır | Yes | Hayır |  |
|Bastion | Hayır | Hayır | Hayır |  |
|Batch | Yes | Yes | Hayır |  |
|Batch AI | Hayır | Hayır | Hayır |  |
|Blok Zinciri Hizmeti | Hayır | Yes | Hayır |  |
|Blueprints | Hayır | Hayır | Hayır |  |
|Bot Hizmeti | Hayır | Hayır | Hayır |  |
|Cloud Services | Yes | Yes | Hayır | Konuk işletim sistemini ve iş akışlarını izlemek için aracı gereklidir.  |
|Cloud Shell | Hayır | Hayır | Hayır |  |
|Bilişsel Hizmetler | Yes | Yes | Hayır |  |
|Container Instances | Yes | Hayır | Hayır |  |
|Container Registry | Yes | Yes | Hayır |  |
|Content Delivery Network (CDN) | Hayır | Yes | Hayır |  |
|Cosmos DB | Yes | Yes | [Evet](insights/cosmosdb-insights-overview.md) |  |
|Maliyet Yönetimi | Hayır | Hayır | Hayır |  |
|Data Box | Hayır | Hayır | Hayır |  |
|Veri Kataloğu Gen2 | Hayır | Hayır | Hayır |  |
|Veri Gezgini | Yes | Yes | Hayır |  |
|Data Factory | Yes | Yes | Hayır |  |
|Data Factory v2 | Hayır | Yes | Hayır |  |
|Veri Paylaşımı | Hayır | Hayır | Hayır |  |
|MariaDB için Veritabanı | Yes | Yes | Hayır |  |
|MySQL için Veritabanı | Yes | Yes | Hayır |  |
|PostgreSQL için Veritabanı | Yes | Yes | Hayır |  |
|Veritabanı Geçiş Hizmeti | Hayır | Hayır | Hayır |  |
|Databricks | Hayır | Yes | Hayır |  |
|DDOS Koruması | Yes | Yes | Hayır |  |
|DevOps | Hayır | Hayır | Hayır |  |
|DNS | Yes | Hayır | Hayır |  |
|Etki alanı adları | Hayır | Hayır | Hayır |  |
|DPS | Hayır | Hayır | Hayır |  |
|Dynamics 365 müşteri katılımı | Hayır | Hayır | Hayır |  |
|Dynamics 365 finans ve Işlemler | Hayır | Hayır | Hayır |  |
|Event Grid | Yes | Hayır | Hayır |  |
|Event Hubs | Yes | Yes | Hayır |  |
|ExpressRoute | Yes | Yes | Hayır |  |
|Güvenlik Duvarı | Yes | Yes | Hayır |  |
|Front Door | Yes | Yes | Hayır |  |
|İşlevler | Yes | Yes | Hayır |  |
|HDInsight | Hayır | Yes | Hayır |  |
|HPC Önbelleği | Hayır | Hayır | Hayır |  |
|Information Protection | Hayır | Yes | Hayır |  |
|Intune | Hayır | Yes | Hayır |  |
|IoT Central | Hayır | Hayır | Hayır |  |
|IoT Hub | Yes | Yes | Hayır |  |
|Key Vault | Yes | Yes | [Evet](./insights/key-vault-insights-overview.md) |  |
|Kubernetes Hizmeti (AKS) | Hayır | Hayır | [Evet](containers/container-insights-overview.md)  |  |
|Load Balancer | Yes | Hayır | Hayır |  |
|Logic Apps | Yes | Yes | Hayır |  |
|Machine Learning Hizmeti | Hayır | Hayır | Hayır |  |
|Yönetilen Uygulamalar  | Hayır | Hayır | Hayır |  |
|Haritalar  | Hayır | Hayır | Hayır |  |
|Media Services | Yes | Yes | Hayır |  |
|Microsoft Yönetilen Masaüstü | Hayır | Hayır | Hayır |  |
|Microsoft PowerApps | Hayır | Hayır | Hayır |  |
|Microsoft Social Engagement | Hayır | Hayır | Hayır |  |
|Microsoft Stream | Yes | Yes | Hayır |  |
|Geçiş | Hayır | Hayır | Hayır |  |
|Multi-Factor Authentication | Hayır | Yes | Hayır |  |
|Ağ İzleyicisi | Yes | Yes | Hayır |  |
|Notification Hubs | Yes | Hayır | Hayır |  |
|Açık Veri Kümeleri | Hayır | Hayır | Hayır |  |
|İlke | Hayır | Hayır | Hayır |  |
|Power Automate | Hayır | Hayır | Hayır |  |
|Power BI Embedded | Yes | Yes | Hayır |  |
|Özel Bağlantı | Hayır | Hayır | Hayır |  |
|Proje biriktirme Iletişim platformu | Hayır | Hayır | Hayır |  |
|Red Hat OpenShift | Hayır | Hayır | Hayır |  |
|Redis Cache | Yes | Yes | [Evet](insights/redis-cache-insights-overview.md) | |
|Kaynak Grafı | Hayır | Hayır | Hayır |  |
|Resource Manager | Hayır | Hayır | Hayır |  |
|Perakende arama-Bing tarafından | Hayır | Hayır | Hayır |  |
|Arayın | Yes | Yes | Hayır |  |
|Service Bus | Yes | Yes | Hayır |  |
|Service Fabric | Hayır | Yes | Hayır | Konuk işletim sistemini ve iş akışlarını izlemek için aracı gereklidir.  |
|Kaydolma portalı | Hayır | Hayır | Hayır |  |
|Site Recovery | Hayır | Yes | Hayır |  |
|Yay bulut hizmeti | Hayır | Hayır | Hayır |  |
|Azure Synapse Analytics | Yes | Yes | Hayır |  |
|SQL Veritabanı | Yes | Yes | Hayır |  |
|SQL Server Stretch Database | Yes | Yes | Hayır |  |
|Yığın | Hayır | Hayır | Hayır |  |
|Depolama | Yes | Hayır | [Evet](insights/storage-insights-overview.md) |  |
|Depolama önbelleği | Hayır | Hayır | Hayır |  |
|Depolama Eşitleme Hizmetleri | Hayır | Hayır | Hayır |  |
|Stream Analytics | Yes | Yes | Hayır |  |
|Time Series Insights | Yes | Yes | Hayır |  |
|TINA | Hayır | Hayır | Hayır |  |
|Traffic Manager | Yes | Yes | Hayır |  |
|Evrensel Yazdırma | Hayır | Hayır | Hayır |  |
|Sanal Makine Ölçek Kümeleri | Hayır | Yes | [Evet](vm/vminsights-overview.md) | Konuk işletim sistemini ve iş akışlarını izlemek için aracı gereklidir. |
|Sanal Makineler | Yes | Yes | [Evet](vm/vminsights-overview.md) | Konuk işletim sistemini ve iş akışlarını izlemek için aracı gereklidir. |
|Sanal Ağ | Yes | Yes | [Evet](insights/network-insights-overview.md) |  |
|Sanal ağ-NSG akış günlükleri | Hayır | Yes | Hayır |  |
|VPN Gateway | Yes | Yes | Hayır |  |
|Windows Sanal Masaüstü | Hayır | Yes | Hayır |  |

## <a name="virtual-machine-agents"></a>Sanal makine aracıları
Aşağıdaki tabloda, sanal makinelerin Konuk işletim sisteminden veri toplayabileceği ve Izlemek için veri gönderebilen aracılar listelenmektedir. Her aracı farklı verileri toplayabilir ve Azure Izleyici 'de ölçümler ya da günlüklere gönderebilir. 

Her bir aracının toplayabileceği veriler hakkında ayrıntılı bilgi için bkz. [Azure izleyici aracılarına genel bakış](agents/agents-overview.md) .

| Aracı |  Ölçümler | Günlükler |
|:---|:---|:---|:---|
| [Azure Izleyici Aracısı (Önizleme)](agents/azure-monitor-agent-overview.md) | Yes | Yes |
| [Log Analytics aracısı](agents/log-analytics-agent.md) | Hayır | Yes|
| [Tanılama uzantısı](agents/diagnostics-extension-overview.md) | Yes | Hayır |
| [Telegraf Aracısı](essentials/collect-custom-metrics-linux-telegraf.md) | Yes | Hayır |
| [Bağımlılık aracısı](vm/vminsights-enable-overview.md) | Hayır | Yes |


## <a name="product-integrations"></a>Ürün tümleştirmeleri
Aşağıdaki tabloda yer alan hizmetler ve çözümler, Azure Izleyici tarafından toplanan diğer günlük verileriyle çözümlenebilmesi için verilerini bir Log Analytics çalışma alanında depolar.

| Ürün/hizmet | Açıklama |
|:---|:---|
| [Azure Otomasyonu](../automation/index.yml) | Windows ve Linux bilgisayarlarda işletim sistemi güncelleştirmelerini yönetin ve değişiklikleri izleyin. Bkz. [değişiklik izleme](../automation/change-tracking/overview.md) ve [güncelleştirme yönetimi](../automation/update-management/overview.md). |
| [Azure Information Protection ](/azure/information-protection/) | Belge ve e-postaları sınıflandırma ve isteğe bağlı olarak koruma. Bkz. [Azure Information Protection Için merkezi raporlama](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Güvenlik Merkezi](../security-center/index.yml) | Güvenlik olaylarını toplayın ve çözümleyin ve tehdit analizi gerçekleştirin. Bkz. [Azure Güvenlik Merkezi 'Nde veri toplamayı](../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../sentinel/index.yml) | Office 365 ve Amazon Web Services bulut Izi gibi farklı kaynaklara bağlanır. Bkz. [veri kaynaklarını bağlama](../sentinel/connect-data-sources.md). |
| [Microsoft Intune](/intune/) | Günlükleri Azure Izleyici 'ye göndermek için bir tanılama ayarı oluşturun. Bkz. [Intune 'da günlük verilerini depolama, Olay Hub 'ları veya Log Analytics 'e gönderme (Önizleme)](/intune/fundamentals/review-logs-using-azure-monitor).  |
| Ağ  | [Ağ performansı İzleyicisi](insights/network-performance-monitor.md) -hizmet ve uygulama uç noktalarına ağ bağlantısını ve performansını izleyin.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics) -Azure Application Gateway günlükleri ve ölçümleri analiz edin.<br>[Trafik Analizi](../network-watcher/traffic-analytics.md) -Azure bulutunuzda trafik akışına ilişkin Öngörüler sağlamak Için Ağ İzleyicisi ağ güvenlik grubu (NSG) akış günlüklerini analiz eder. |
| [Office 365](insights/solution-office-365.md) | Office 365 ortamınızı izleyin. Azure Sentinel ile sunulan geliştirilmiş ekleme ile güncelleştirilmiş sürüm. |
| [SQL Analytics](insights/azure-sql.md) | Azure SQL veritabanlarının ve SQL yönetilen örneklerinin performansını ölçeklendirerek ve birden çok aboneliğe göre izleyin. |
| [Surface Hub](insights/surface-hubs.md) | Surface Hub cihazların sistem durumunu ve kullanımını izleyin. |
| [System Center Operations Manager](/system-center/scom) | Yönetim gruplarını Azure Izleyici 'ye bağlayarak Operations Manager aracılardan veri toplayın. Bkz. [Azure izleyici 'ye bağlanma Operations Manager](agents/om-agents.md)<br> [Operations Manager değerlendirmesi](insights/scom-assessment.md) çözümüyle System Center Operations Manager yönetim grubunuzun riskini ve sistem durumunu değerlendirin. |
| [Microsoft ekipleri Odalar](/microsoftteams/room-systems/azure-monitor-deploy) | Microsoft ekipleri Oda cihazlarının tümleşik, uçtan uca yönetimi. |
| [Visual Studio App Center](/appcenter/) | Uygulamalar oluşturun, test edin ve dağıtın ve ardından durumlarını ve kullanımını izleyin. Bkz. [App Center ve Application Insights mobil uygulamanızı çözümlemeyi başlatma](app/mobile-center-quickstart.md). |
| Windows | [Uyumluluk Windows Update](/windows/deployment/update/update-compliance-get-started) -Windows Masaüstü yükseltmelerinizi değerlendirin.<br>[Masaüstü Analizi](/configmgr/desktop-analytics/overview) -Windows istemcilerinizin güncelleştirme hazırlığı hakkında daha bilinçli kararlar almak için öngörüleri ve zekası sağlamak üzere Configuration Manager ile tümleşir. |



## <a name="other-solutions"></a>Diğer çözümler
Farklı uygulama ve hizmetleri izlemek için diğer çözümler mevcuttur, ancak etkin geliştirme durdurulur ve tüm bölgelerde kullanılamayabilir. Bunlar Azure Log Analytics veri alma hizmeti düzeyi sözleşmesi kapsamında ele alınmıştır.

| Çözüm | Açıklama |
|:---|:---|
| [Active Directory sistem durumu denetimi](insights/ad-assessment.md) | Active Directory ortamlarınızın riskini ve sistem durumunu değerlendirin. |
| [Active Directory çoğaltma durumu](insights/ad-replication-status.md) | Active Directory ortamınızı tüm çoğaltma hatalarıyla düzenli olarak izler. |
| [Etkinlik günlüğü Analizi](essentials/activity-log.md#activity-log-analytics-monitoring-solution) | Etkinlik günlüğü girdilerini görüntüleyin. |
| [DNS Analizi (Önizleme)](insights/dns-analytics.md) | Windows DNS analitik ve denetim günlüklerini ve DNS sunucularınızdaki diğer ilgili verileri toplar, analiz eder ve ilişkilendirir. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Cloud Foundry sistem durumu ve performans ölçümlerinizi birden çok dağıtımda toplayın, görüntüleyin ve çözümleyin. |
| [Kapsayıcılar](containers/containers.md) | Docker ve Windows kapsayıcı ana bilgisayarlarını görüntüleyin ve yönetin. |
| [Isteğe bağlı değerlendirmeler](/services-hub/health/getting_started_with_on_demand_assessments) | Şirket içi, karma ve bulut Microsoft teknoloji ortamlarınızın kullanılabilirliğini, güvenliğini ve performansını değerlendirin ve iyileştirin. |
| [SQL sistem durumu denetimi](insights/sql-assessment.md) | SQL Server ortamlarınızın riskini ve sistem durumunu değerlendirin.  |
| [Kablo Verileri](insights/wire-data.md) | Windows ile bağlı ve Linux bağlantılı bilgisayarlardan Log Analytics aracısıyla toplanan birleştirilmiş ağ ve performans verileri. |

## <a name="third-party-integration"></a>Üçüncü taraf tümleştirmesi

| Çözüm | Açıklama |
|:---|:---|
| [ITSM](alerts/itsmc-overview.md) | BT Hizmet Yönetimi Bağlayıcısı (ITSMC), Azure ile desteklenen BT Hizmet Yönetimi (ITSM) ürünü/hizmeti arasında bağlantı kurmanızı sağlar.  |


## <a name="resources-outside-of-azure"></a>Azure dışındaki kaynaklar
Azure Izleyici, aşağıdaki tabloda listelenen yöntemleri kullanarak Azure dışındaki kaynaklardan veri toplayabilir.

| Kaynak | Yöntem |
|:---|:---|
| Uygulamalar | Application Insights kullanarak Azure dışındaki Web uygulamalarını izleyin. Bkz. [Application Insights nedir?](./app/app-insights-overview.md). |
| Sanal makineler | Diğer bulut ortamlarında veya Şirket içindeki sanal makinelerin Konuk işletim sisteminden veri toplamak için aracıları kullanın. Bkz. [Azure izleyici aracılarına genel bakış](agents/agents-overview.md). |
| REST API Istemcisi | Her bir REST API istemcisinden Azure Izleyici günlüklerine ve ölçümlerine veri yazmak için ayrı API 'Ler kullanılabilir. Bkz. Azure izleyici 'de Günlükler için [http veri toplayıcı API 'si ile günlük verileri gönderme](logs/data-collector-api.md) ve bir [Azure kaynağı için azure izleyici ölçüm REST API deposuna özel ölçümler gönderme](essentials/metrics-store-custom-rest-api.md) . |



## <a name="next-steps"></a>Sonraki adımlar

- [Öngörüler ve çözümler tarafından toplanan günlükleri ve ölçümleri depolayan Azure izleyici veri platformu](data-platform.md)hakkında daha fazla bilgi edinin.
- Bir [Azure kaynağını izlemeye yönelik bir öğreticiyi](essentials/tutorial-resource-logs.md)doldurun.
- [Azure Izleyici günlüklerinde verileri çözümlemek için günlük sorgusu yazma öğreticisini](essentials/tutorial-resource-logs.md)doldurun.
- [Azure Izleyici ölçümlerinde verileri çözümlemek için ölçüm grafiği oluşturma öğreticisini](essentials/tutorial-metrics-explorer.md)doldurun.

