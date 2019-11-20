---
title: Azure kaynak günlüğü desteklenen Hizmetleri ve şemaları
description: Azure tanılama günlükleri için desteklenen Hizmetleri ve olay şemasını anlayın.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
author: rboucher
ms.author: robb
ms.openlocfilehash: 09d1a25b83f405b45bbefd39766c82565ea86925
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476664"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Azure Kaynak günlükleri için desteklenen hizmetler, şemalar ve Kategoriler

> [!NOTE]
> Kaynak günlükleri daha önce tanılama günlükleri olarak bilinirdi.

Azure [izleyici kaynak günlükleri](../../azure-monitor/platform/resource-logs-overview.md) , bu hizmet veya kaynakların çalışmasını tanımlayan Azure hizmetleri tarafından oluşturulan günlüklerdir. Azure Izleyici aracılığıyla kullanılabilen tüm kaynak günlükleri, her bir hizmetin kendi olayları için benzersiz özellikler yaymasını sağlayan ortak bir üst düzey şemayı paylaşır.

Kaynak türünün bir birleşimi (`resourceId` özelliğinde kullanılabilir) ve `category` bir şemayı benzersiz bir şekilde tanımlar. Bu makalede, kaynak günlükleri için en üst düzey şema ve her hizmet için şemaların serileştirilmesi bağlantıları açıklanmaktadır.

## <a name="top-level-resource-logs-schema"></a>Üst düzey kaynak günlükleri şeması

| Ad | Gerekli/Isteğe bağlı | Açıklama |
|---|---|---|
| time | Gerekli | Olayın zaman damgası (UTC). |
| resourceId | Gerekli | Olayı veren kaynağın kaynak KIMLIĞI. Kiracı Hizmetleri için bu,/Tenants/Tenant-id/Providers/Provider-nameformundadır. |
| Değerine | Kiracı günlükleri için gereklidir | Bu olayın bağlı olduğu Active Directory kiracının kiracı KIMLIĞI. Bu özellik yalnızca kiracı düzeyindeki Günlükler için kullanılır, kaynak düzeyinde günlüklerde görünmez. |
| operationName | Gerekli | Bu olayla temsil edilen işlemin adı. Olay bir RBAC işlemini gösteriyorsa, bu RBAC işlem adıdır (örn. Microsoft. Storage/storageAccounts/blobServices/blob/Read). Genellikle Kaynak Yöneticisi bir işlem biçiminde modellenmiştir, bunlar gerçek belgelenmiş Kaynak Yöneticisi işlemleri (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) olmasalar bile |
| operationVersion | İsteğe bağlı | OperationName bir API (örn.) kullanılarak gerçekleştirildiyse, işlemle ilişkili api sürümü (ör. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Bu işleme karşılık gelen bir API yoksa, sürüm, gelecekte işlemle ilişkili özellikler, bu işlemin sürümünü temsil eder. |
| category | Gerekli | Etkinliğin günlük kategorisi. Kategori, belirli bir kaynaktaki günlükleri etkinleştirebilmeniz veya devre dışı bırakabilmeniz için gereken ayrıntı düzeyi. Bir olayın Özellikler blobu içinde görünen özellikler, belirli bir günlük kategorisi ve kaynak türü içinde aynıdır. Tipik günlük kategorileri "Audit" "Işletimsel" "yürütme" ve "Istek" dir. |
| resultType | İsteğe bağlı | Etkinliğin durumu. Tipik değerler başlangıç, devam etme, başarılı, başarısız, etkin ve Çözümlenmiş. |
| resultSignature | İsteğe bağlı | Etkinliğin alt durumu. Bu işlem bir REST API çağrısına karşılık geliyorsa, karşılık gelen REST çağrısının HTTP durum kodudur. |
| resultDescription | İsteğe bağlı | Bu işlemin statik metin açıklaması, örn. "Depolama dosyası Al". |
| durationMs | İsteğe bağlı | İşlem süresi (milisaniye cinsinden). |
| callerIpAddress | İsteğe bağlı | İşlem, genel kullanıma açık bir IP adresine sahip bir varlıktan gelen bir API çağrısına karşılık geliyorsa, çağıran IP adresi. |
| correlationId | İsteğe bağlı | Bir dizi ilgili olayı gruplamak için kullanılan GUID. Genellikle, iki olay aynı operationName öğesine ancak iki farklı durum (örn. "Başlatıldı" ve "başarılı"), aynı bağıntı KIMLIĞINI paylaşır. Bu, olaylar arasındaki diğer ilişkileri de temsil edebilir. |
| identity | İsteğe bağlı | İşlemi gerçekleştiren kullanıcının veya uygulamanın kimliğini açıklayan bir JSON blobu. Genellikle bu, Active Directory 'den yetkilendirme ve talepler/JWT belirtecini içerir. |
| Düzey | İsteğe bağlı | Etkinliğin önem düzeyi. Bilgilendirici, uyarı, hata veya kritik bir olmalıdır. |
| location | İsteğe bağlı | Olayı yayan kaynağın bölgesi, örn. "Doğu ABD" veya "Fransa Güney" |
| properties | İsteğe bağlı | Bu belirli olay kategorisiyle ilgili genişletilmiş özellikler. Tüm özel/benzersiz özellikler şemanın "Bölüm B" içinde yerleştirilmelidir. |

## <a name="service-specific-schemas-for-resource-logs"></a>Kaynak günlükleri için hizmete özgü şemalar
Kaynak tanılama günlükleri şeması, kaynak ve günlük kategorisine göre değişir. Bu liste, kullanılabilir olduğunda, kullanılabilir kaynak günlüklerini ve hizmet ve kategoriye özgü şemaya bağlantıları yapan tüm hizmetleri gösterir.

| Hizmet | Şema & belgeleri |
| --- | --- |
| Azure Active Directory | [Genel bakış](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [Denetim günlüğü şeması](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) ve [oturum açma şeması](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Kaynak günlüklerini API Management](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Uygulama Ağ Geçitleri |[Application Gateway için günlüğe kaydetme](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Otomasyonu |[Azure Otomasyonu için Log Analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Günlüğe kaydetme Azure Batch](../../batch/batch-diagnostics.md) |
| MySQL için Azure Veritabanı | [MySQL için Azure veritabanı tanılama günlükleri](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| PostgreSQL için Azure Veritabanı | [PostgreSQL için Azure veritabanı günlükleri](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Veri Gezgini | [Azure Veri Gezgini günlükleri](../../data-explorer/using-diagnostic-logs.md) |
| Bilişsel Hizmetler | [Azure bilişsel hizmetler için günlüğe kaydetme](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Azure Container Registry için günlüğe kaydetme](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [CDN için Azure günlükleri](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Günlüğe kaydetme Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Azure Izleyici 'yi kullanarak veri fabrikalarını izleme](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Azure Data Lake Analytics için günlüklere erişme](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Azure Data Lake Store için günlüklere erişme](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs günlükleri](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Şema kullanılamıyor. |
| Azure Güvenlik Duvarı | Şema kullanılamıyor. |
| IoT Hub | [IoT Hub Işlemler](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Anahtar Kasası |[Azure Anahtar Kasası Günlüğü](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Azure Kubernetes günlüğü](../../aks/view-master-logs.md#log-event-schema) |
| Yük Dengeleyici |[Azure Load Balancer için Log Analytics](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B özel izleme şeması](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Ağ Güvenlik Grupları |[Ağ güvenlik grupları (NSG’ler) için Log Analytics](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Koruması | [Azure DDoS koruması standardını yönetme](../../virtual-network/manage-ddos-protection.md) |
| Power BI Ayrılmış | [Azure 'da Power BI Embedded için günlüğe kaydetme](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Kurtarma Hizmetleri | [Azure Backup için veri modeli](../../backup/backup-azure-reports-data-model.md)|
| Arama |[Arama Trafik Analizi etkinleştirme ve kullanma](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus günlükleri](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Veritabanı | [Azure SQL veritabanı günlüğü](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[İş günlükleri](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager log şeması](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Sanal Ağlar | Şema kullanılamıyor. |
| Sanal Ağ Geçitleri | Şema kullanılamıyor. |

## <a name="supported-log-categories-per-resource-type"></a>Kaynak türü başına desteklenen günlük kategorileri
|Kaynak Türü|Kategori|Kategori görünen adı|
|---|---|---|
|Microsoft. AAD/domainServices|SystemSecurity|SystemSecurity|
|Microsoft. AAD/domainServices|Hesap yönetimi|Hesap yönetimi|
|Microsoft. AAD/domainServices|LogonLogoff|LogonLogoff|
|Microsoft. AAD/domainServices|ObjectAccess|ObjectAccess|
|Microsoft. AAD/domainServices|PolicyChange|PolicyChange|
|Microsoft. AAD/domainServices|Ayrıcalıklı Geuse|Ayrıcalıklı Geuse|
|Microsoft. AAD/domainServices|Ayrıntı Izleme|Ayrıntı Izleme|
|Microsoft. AAD/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft. AAD/domainServices|AccountLogon|AccountLogon|
|Microsoft. aadihar/kiracılar|Giriş|Giriş|
|Microsoft. AnalysisServices/sunucuları|Altyapısına|Altyapısına|
|Microsoft. AnalysisServices/sunucuları|Hizmet|Hizmet|
|Microsoft.ApiManagement/service|GatewayLogs|Imanagementpackgateway ile ilgili Günlükler|
|Microsoft. AppPlatform/yay|ApplicationConsole|Uygulama konsolu|
|Microsoft. Automation/automationAccounts|JobLogs|İş günlükleri|
|Microsoft. Automation/automationAccounts|JobStreams|İş akışları|
|Microsoft. Automation/automationAccounts|DscNodeStatus|DSC düğüm durumu|
|Microsoft. Batch/batchAccounts|ServiceLog|Hizmet günlükleri|
|Microsoft. Batchaı/çalışma alanları|BaiClusterEvent|BaiClusterEvent|
|Microsoft. Batchaı/çalışma alanları|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft. Batchaı/çalışma alanları|BaiJobEvent|BaiJobEvent|
|Microsoft. Blockzincirine/blockchainMembers|BlockchainApplication|Blok zinciri uygulaması|
|Microsoft. Blockzincirine/blockchainMembers|Ara sunucu|Ara sunucu|
|Microsoft. CDN/profiller/uç noktaları|CoreAnalytics|Uç noktanın ölçümlerini (örn. bant genişliği, çıkış vb.) alır.|
|Microsoft. ClassicNetwork/networksecuritygroups|Ağ güvenlik grubu kural akışı olayı|Ağ güvenlik grubu kural akışı olayı|
|Microsoft. Biliveservices/hesapları|Denetim|Denetim Günlükleri|
|Microsoft. Biliveservices/hesapları|RequestResponse|İstek ve yanıt günlükleri|
|Microsoft. ContainerRegistry/kayıt defterleri|Containerregistrydepotoryevents|Olay günlüklerini günlüğe kaydeder (Önizleme)|
|Microsoft. ContainerRegistry/kayıt defterleri|ContainerRegistryLoginEvents|Oturum açma olayları (Önizleme)|
|Microsoft. ContainerService/Managedkümeler|kuin-apiserver|Kubernetes API sunucusu|
|Microsoft. ContainerService/Managedkümeler|kuin-Controller-Manager|Kubernetes Denetleyici Yöneticisi|
|Microsoft. ContainerService/Managedkümeler|kuin-Scheduler|Kubernetes Zamanlayıcı|
|Microsoft. ContainerService/Managedkümeler|kuin-denetim|Kubernetes denetimi|
|Microsoft. ContainerService/Managedkümeler|küme-otomatik Scaler|Kubernetes kümesi otomatik Scaler|
|Microsoft. Databricks/çalışma alanları|dBFS|Databricks Dosya Sistemi|
|Microsoft. Databricks/çalışma alanları|leriniz|Databricks kümeleri|
|Microsoft. Databricks/çalışma alanları|accounts|Databricks hesapları|
|Microsoft. Databricks/çalışma alanları|Çizelge|Databricks İşleri|
|Microsoft. Databricks/çalışma alanları|Mini|Databricks Not Defteri|
|Microsoft. Databricks/çalışma alanları|SSH|Databricks SSH|
|Microsoft. Databricks/çalışma alanları|alanında|Databricks Çalışma Alanı|
|Microsoft. Databricks/çalışma alanları|kaynaklanır|Databricks gizli dizileri|
|Microsoft. Databricks/çalışma alanları|sqlPermissions|Databricks SQLPermissions|
|Microsoft. Databricks/çalışma alanları|ınstancepools|Örnek Havuzları|
|Microsoft. DataCatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Microsoft. DataFactory/Factory|Etkinlik çalıştırmaları|İşlem hattı etkinlik günlüğü çalıştırmaları|
|Microsoft. DataFactory/Factory|Ardışık düzen eylemsizlik|İşlem hattı çalıştırmaları günlüğü|
|Microsoft. DataFactory/Factory|Triggerçalıştırmaları|Tetikleyici çalıştırma günlüğü|
|Microsoft. DataLakeAnalytics/hesapları|Denetim|Denetim Günlükleri|
|Microsoft. DataLakeAnalytics/hesapları|İstekler|İstek günlükleri|
|Microsoft. DataLakeStore/accounts|Denetim|Denetim Günlükleri|
|Microsoft. DataLakeStore/accounts|İstekler|İstek günlükleri|
|Microsoft. DataShare/hesapları|Paylaşımlar|Paylaşımlar|
|Microsoft. DataShare/hesapları|Parça sayısı|Abonelikleri paylaşma|
|Microsoft. DataShare/hesapları|SentShareSnapshots|Gönderilen paylaşılan anlık görüntüler|
|Microsoft. DataShare/hesapları|ReceivedShareSnapshots|Alınan paylaşma anlık görüntüleri|
|Microsoft. Dbformyısql/sunucuları|Mysqlyavaşlatma günlükleri|MySQL Server günlükleri|
|Microsoft. Dbformyısql/sunucuları|MySqlAuditLogs|MySQL denetim günlükleri|
|Microsoft. DBforPostgreSQL/sunucuları|Postgressqllogs|PostgreSQL sunucu günlükleri|
|Microsoft. DBforPostgreSQL/sunucuları|QueryStoreRuntimeStatistics|PostgreSQL sorgu deposu çalışma zamanı Istatistikleri|
|Microsoft. DBforPostgreSQL/sunucuları|QueryStoreWaitStatistics|PostgreSQL sorgu deposu bekleme Istatistikleri|
|Microsoft. DBforPostgreSQL/serversv2|Postgressqllogs|PostgreSQL sunucu günlükleri|
|Microsoft. DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|PostgreSQL sorgu deposu çalışma zamanı Istatistikleri|
|Microsoft. DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|PostgreSQL sorgu deposu bekleme Istatistikleri|
|Microsoft. DesktopVirtualization/çalışma alanları|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/çalışma alanları|Hata|Hata|
|Microsoft. DesktopVirtualization/çalışma alanları|Yönetim|Yönetim|
|Microsoft. DesktopVirtualization/çalışma alanları|Akış|Akış|
|Microsoft. DesktopVirtualization/applicationGroups|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/applicationGroups|Hata|Hata|
|Microsoft. DesktopVirtualization/applicationGroups|Yönetim|Yönetim|
|Microsoft. DesktopVirtualization/hostPools|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/hostPools|Hata|Hata|
|Microsoft. DesktopVirtualization/hostPools|Yönetim|Yönetim|
|Microsoft. DesktopVirtualization/hostPools|Bağlantı|Bağlantı|
|Microsoft. DesktopVirtualization/hostPools|HostRegistration|HostRegistration|
|Microsoft. Devices/IotHubs|Bağlantılar|Bağlantılar|
|Microsoft. Devices/IotHubs|Devicetelemetri|Cihaz Telemetrisi|
|Microsoft. Devices/IotHubs|C2DCommands|C2D komutları|
|Microsoft. Devices/IotHubs|Deviceıdentityoperations|Cihaz kimliği Işlemleri|
|Microsoft. Devices/IotHubs|FileUploadOperations|Karşıya dosya yükleme Işlemleri|
|Microsoft. Devices/IotHubs|Yollar|Yollar|
|Microsoft. Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft. Devices/IotHubs|C2DTwinOperations|C2D Ikizi Işlemleri|
|Microsoft. Devices/IotHubs|TwinQueries|İkizi sorguları|
|Microsoft. Devices/IotHubs|JobsOperations|İş Işlemleri|
|Microsoft. Devices/IotHubs|DirectMethods|Doğrudan Yöntemler|
|Microsoft. Devices/IotHubs|Distributedizleme|Dağıtılmış Izleme (Önizleme)|
|Microsoft. Devices/IotHubs|Yapılandırmalar|Yapılandırmalar|
|Microsoft. Devices/IotHubs|DeviceStreams|Cihaz akışları (Önizleme)|
|Microsoft. Devices/provisioningServices|DeviceOperations Işlemleri|Cihaz Işlemleri|
|Microsoft. Devices/provisioningServices|ServiceOperations|Hizmet Işlemleri|
|Microsoft. DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft. DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft. DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft. DocumentDB/databaseAccounts|PartitionKeyStatistics|PartitionKeyStatistics|
|Microsoft. DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft. EnterpriseKnowledgeGraph/hizmetler|AuditEvent|AuditEvent günlüğü|
|Microsoft. EnterpriseKnowledgeGraph/hizmetler|Veri sorunu|Datasorun günlüğü|
|Microsoft. EnterpriseKnowledgeGraph/hizmetler|İstekler|Yapılandırma günlüğü|
|Microsoft. EventHub/ad alanları|ArchiveLogs|Arşiv günlükleri|
|Microsoft. EventHub/ad alanları|OperationalLogs|İşletimsel Günlükler|
|Microsoft. EventHub/ad alanları|Oto Scalelogs|Günlükleri otomatik ölçeklendir|
|Microsoft. EventHub/ad alanları|KafkaCoordinatorLogs|Kafka Coordinator günlükleri|
|Microsoft. EventHub/ad alanları|KafkaUserErrorLogs|Kafka Kullanıcı hatası günlükleri|
|Microsoft. EventHub/ad alanları|EventHubVNetConnectionEvent|VNet/IP filtreleme bağlantı günlükleri|
|Microsoft. EventHub/ad alanları|CustomerManagedKeyUserLogs|Müşteri tarafından yönetilen anahtar günlükleri|
|Microsoft. Healthgelişme API 'leri/Hizmetleri|AuditLogs|Denetim günlükleri|
|Microsoft. Insights/oto Scalesettings|Oto Scaledeğerlendirmeleri|Otomatik ölçeklendirme değerlendirmeleri|
|Microsoft. Insights/oto Scalesettings|Oto Scalescaleactions|Otomatik ölçeklendirme ölçeklendirme eylemleri|
|Microsoft. ıotspaces/Graph|İzleme|İzleme|
|Microsoft. ıotspaces/Graph|İşlemdeki|İşlemdeki|
|Microsoft. ıotspaces/Graph|Denetim|Denetim|
|Microsoft. ıotspaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft. ıotspaces/Graph|Giriş|Giriş|
|Microsoft. ıotspaces/Graph|Çıkış|Çıkış|
|Microsoft. Keykasası/kasa|AuditEvent|Denetim Günlükleri|
|Microsoft. kusto/kümeler|SucceededIngestion|Başarılı alma işlemleri|
|Microsoft. kusto/kümeler|FailedIngestion|Başarısız alma işlemleri|
|Microsoft. Logic/iş akışları|Başlatıldıktan|İş akışı çalışma zamanı tanılama olayları|
|Microsoft. Logic/ıntegrationaccounts|Integrationaccounttrackingevents|Tümleştirme hesabı izleme olayları|
|Microsoft. MachineLearningServices/çalışma alanları|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft. MachineLearningServices/çalışma alanları|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft. MachineLearningServices/çalışma alanları|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft. Media/mediaservices|KeyDeliveryRequests|Anahtar teslim Istekleri|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupEvent|Ağ güvenlik grubu olayı|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Ağ güvenlik grubu kuralı sayacı|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Ağ güvenlik grubu kural akışı olayı|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS koruması bildirimleri|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|DDoS azaltma kararlarının akış günlükleri|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|DDoS azaltmaları raporları|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM koruma uyarıları|
|Microsoft. Network/Applicationgateway 'ler|ApplicationGatewayAccessLog|Application Gateway erişim günlüğü|
|Microsoft. Network/Applicationgateway 'ler|ApplicationGatewayPerformanceLog|Application Gateway performans günlüğü|
|Microsoft. Network/Applicationgateway 'ler|ApplicationGatewayFirewallLog|Application Gateway Güvenlik duvarı günlüğü|
|Microsoft. Network/azurefirewalls|AzureFirewallApplicationRule|Azure Güvenlik Duvarı uygulama kuralı|
|Microsoft. Network/azurefirewalls|AzureFirewallNetworkRule|Azure Güvenlik Duvarı ağ kuralı|
|Microsoft. Network/Virtualnetworkgateway 'ler|GatewayDiagnosticLog|Ağ geçidi tanılama günlükleri|
|Microsoft. Network/Virtualnetworkgateway 'ler|Tüneldiagnosticlog|Tünel tanılama günlükleri|
|Microsoft. Network/Virtualnetworkgateway 'ler|RouteDiagnosticLog|Tanılama günlüklerini yönlendirme|
|Microsoft. Network/Virtualnetworkgateway 'ler|Ikediagnosticlog|IKE tanılama günlükleri|
|Microsoft. Network/Virtualnetworkgateway 'ler|P2SDiagnosticLog|P2S tanılama günlükleri|
|Microsoft. Network/trafficManagerProfiles|Probehealthkara Sevents|Araştırma durumu sonuçları olayını Traffic Manager|
|Microsoft. Network/Expressroutedevreleri|PeeringRouteLog|Yol tablosu günlüklerini eşleme|
|Microsoft. Network/Vpngateway 'ler|GatewayDiagnosticLog|Ağ geçidi tanılama günlükleri|
|Microsoft. Network/Vpngateway 'ler|Tüneldiagnosticlog|Tünel tanılama günlükleri|
|Microsoft. Network/Vpngateway 'ler|RouteDiagnosticLog|Tanılama günlüklerini yönlendirme|
|Microsoft. Network/Vpngateway 'ler|Ikediagnosticlog|IKE tanılama günlükleri|
|Microsoft. Network/frontkapaklı|FrontdoorAccessLog|Frontkapısı erişim günlüğü|
|Microsoft. Network/frontkapaklı|FrontdoorWebApplicationFirewallLog|Frontkapılı Web uygulaması güvenlik duvarı günlüğü|
|Microsoft. Network/p2sVpnGateways|GatewayDiagnosticLog|Ağ geçidi tanılama günlükleri|
|Microsoft. Network/p2sVpnGateways|Ikediagnosticlog|IKE tanılama günlükleri|
|Microsoft. Network/p2sVpnGateways|P2SDiagnosticLog|P2S tanılama günlükleri|
|Microsoft. Network/bastionHosts|Savunma günlükleri|Savunma denetim günlükleri|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Uyarı olaylarını Load Balancer|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Load Balancer araştırma sistem durumu|
|Microsoft. Powerbiadanmış/kapasiteler|Altyapısına|Altyapısına|
|Microsoft. RecoveryServices/kasa|AzureBackupReport|Raporlama verilerini Azure Backup|
|Microsoft. RecoveryServices/kasa|CoreAzureBackup|Çekirdek Azure Backup verileri|
|Microsoft. RecoveryServices/kasa|AddonAzureBackupJobs|Eklenti Azure Backup Iş verileri|
|Microsoft. RecoveryServices/kasa|AddonAzureBackupAlerts|Eklenti Azure Backup uyarı verisi|
|Microsoft. RecoveryServices/kasa|AddonAzureBackupPolicy|Eklenti Azure Backup Ilkesi verileri|
|Microsoft. RecoveryServices/kasa|AddonAzureBackupStorage|Eklenti Azure Backup depolama verileri|
|Microsoft. RecoveryServices/kasa|AddonAzureBackupProtectedInstance|Eklenti Azure Backup korumalı örnek verileri|
|Microsoft. RecoveryServices/kasa|AzureSiteRecoveryJobs|Azure Site Recovery Işleri|
|Microsoft. RecoveryServices/kasa|Azuresterekapak Yevents|Azure Site Recovery olaylar|
|Microsoft. RecoveryServices/kasa|AzureSiteRecoveryReplicatedItems|Çoğaltılan öğeleri Azure Site Recovery|
|Microsoft. RecoveryServices/kasa|AzureSiteRecoveryReplicationStats|Azure Site Recovery çoğaltma Istatistikleri|
|Microsoft. RecoveryServices/kasa|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery kurtarma noktaları|
|Microsoft. RecoveryServices/kasa|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery çoğaltma verilerini karşıya yükleme hızı|
|Microsoft. RecoveryServices/kasa|AzureSiteRecoveryProtectedDiskDataChurn|Korunan disk verileri dalgalanmasını Azure Site Recovery|
|Microsoft. Search/searchServices|OperationLogs|İşlem günlükleri|
|Microsoft. ServiceBus/ad alanları|OperationalLogs|İşletimsel Günlükler|
|Microsoft. SQL/Servers/veritabanları|Sqlinsıghts|SQL öngörüleri|
|Microsoft. SQL/Servers/veritabanları|Otomatik olarak ayarlama|Otomatik ayarlama|
|Microsoft. SQL/Servers/veritabanları|QueryStoreRuntimeStatistics|Sorgu deposu çalışma zamanı Istatistikleri|
|Microsoft. SQL/Servers/veritabanları|QueryStoreWaitStatistics|Sorgu deposu bekleme Istatistikleri|
|Microsoft. SQL/Servers/veritabanları|Hatalar|Hatalar|
|Microsoft. SQL/Servers/veritabanları|DatabaseWaitStatistics|Veritabanı bekleme Istatistikleri|
|Microsoft. SQL/Servers/veritabanları|Zaman aşımları|Zaman aşımları|
|Microsoft. SQL/Servers/veritabanları|Öbekleri|Öbekleri|
|Microsoft. SQL/Servers/veritabanları|Çık|Çık|
|Microsoft. SQL/Servers/veritabanları|Denetim|Denetim Günlükleri|
|Microsoft. SQL/Servers/veritabanları|SQLSecurityAuditEvents|SQL güvenlik denetim olayı|
|Microsoft. SQL/Servers/veritabanları|Dmsçalışanları|DMS çalışanları|
|Microsoft. SQL/Servers/veritabanları|ExecRequests|Yürütme Istekleri|
|Microsoft. SQL/Servers/veritabanları|RequestSteps|İstek adımları|
|Microsoft. SQL/Servers/veritabanları|SqlRequests|SQL Istekleri|
|Microsoft. SQL/Servers/veritabanları|Bekler|Bekler|
|Microsoft. SQL/ManagedInstances|ResourceUsageStats|Kaynak kullanım Istatistikleri|
|Microsoft. SQL/ManagedInstances|SQLSecurityAuditEvents|SQL güvenlik denetim olayı|
|Microsoft. SQL/ManagedInstances/veritabanları|Sqlinsıghts|SQL öngörüleri|
|Microsoft. SQL/ManagedInstances/veritabanları|QueryStoreRuntimeStatistics|Sorgu deposu çalışma zamanı Istatistikleri|
|Microsoft. SQL/ManagedInstances/veritabanları|QueryStoreWaitStatistics|Sorgu deposu bekleme Istatistikleri|
|Microsoft. SQL/ManagedInstances/veritabanları|Hatalar|Hatalar|
|Microsoft. Storage/storageAccounts/tableServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/tableServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft. Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft. Storage/storageAccounts/fileServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/fileServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/fileServices|StorageDelete|StorageDelete|
|Microsoft. Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft. StreamAnalytics/streamingjobs|Yürütme|Yürütme|
|Microsoft. StreamAnalytics/streamingjobs|Yazma|Yazma|
|Microsoft. Web/hostingenvironments|AppServiceEnvironmentPlatformLogs|App Service Ortamı platform günlükleri|
|Microsoft. Web/siteler|FunctionAppLogs|İşlev uygulama günlükleri|
|Microsoft. Web/siteler|AppServiceHTTPLogs|HTTP günlükleri|
|Microsoft. Web/siteler|AppServiceConsoleLogs|Konsol günlüklerini App Service|
|Microsoft. Web/siteler|AppServiceAppLogs|Uygulama günlüklerini App Service|
|Microsoft. Web/siteler|AppServiceFileAuditLogs|Site Içeriği değişikliği denetim günlükleri|
|Microsoft. Web/siteler|AppServiceAuditLogs|Denetim günlüklerine erişim|
|Microsoft. Web/Sites/Yuvaları|FunctionAppLogs|İşlev uygulama günlükleri|
|Microsoft. Web/Sites/Yuvaları|AppServiceHTTPLogs|HTTP günlükleri|
|Microsoft. Web/Sites/Yuvaları|AppServiceConsoleLogs|Konsol günlükleri|
|Microsoft. Web/Sites/Yuvaları|AppServiceAppLogs|Uygulama Günlükleri|
|Microsoft. Web/Sites/Yuvaları|AppServiceFileAuditLogs|Site Içeriği değişikliği denetim günlükleri|
|Microsoft. Web/Sites/Yuvaları|AppServiceAuditLogs|Denetim günlüklerine erişim|

## <a name="next-steps"></a>Sonraki Adımlar

* [Kaynak günlükleri hakkında daha fazla bilgi edinin](../../azure-monitor/platform/resource-logs-overview.md)
* [**Event Hubs** için kaynak kaynağı günlüklerini akışla](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Azure Izleyici REST API kullanarak kaynak günlüğü tanılama ayarlarını değiştirme](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Azure depolama 'daki günlükleri Log Analytics ile analiz etme](../../azure-monitor/platform/collect-azure-metrics-logs.md)
