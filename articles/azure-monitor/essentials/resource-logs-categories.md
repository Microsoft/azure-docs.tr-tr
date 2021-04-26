---
title: Azure Izleyici kaynak günlüğü desteklenen Hizmetleri ve kategorileri
description: Azure Izleyici başvurusu, Azure Kaynak günlükleri için desteklenen Hizmetleri ve olay şemasını anlayın.
ms.topic: reference
ms.date: 03/30/2021
ms.openlocfilehash: a4ab4a2e425b752198223da5efd1b07466ab83d1
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166947"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Azure Kaynak günlükleri için desteklenen Kategoriler

> [!NOTE]
> Kaynak günlükleri daha önce tanılama günlükleri olarak bilinirdi. Azure Izleyici tarafından toplanan günlüklerin türleri yalnızca Azure kaynağına eşit olacak şekilde kaydırılacağı için ad Ekim 2019 ' de değiştirilmiştir.

Azure [izleyici kaynak günlükleri](../essentials/platform-logs-overview.md) , bu hizmet veya kaynakların çalışmasını tanımlayan Azure hizmetleri tarafından oluşturulan günlüklerdir. Azure Izleyici aracılığıyla kullanılabilen tüm kaynak günlükleri, her bir hizmetin kendi olayları için benzersiz özellikler yaymasını sağlayan ortak bir üst düzey şemayı paylaşır.

Kaynak türünün bir birleşimi ( `resourceId` özellikte mevcuttur) ve `category` bir şemayı benzersiz bir şekilde tanımlar. Hizmete özgü alanlarla, daha sonra farklı günlük kategorileri için eklenen tüm kaynak günlüklerine ortak bir şema vardır. Daha fazla bilgi için bkz. [Azure Kaynak günlükleri Için ortak ve hizmete özgü şema]()


## <a name="costs"></a>Maliyetler

Verileri Log Analytics, Azure depolama ve/veya Olay Hub 'ına gönderme ve depolama ile ilişkili maliyetler vardır. Bu konumlara verileri almak ve bu konumlara ulaşmak için ücret ödeyebilirsiniz.  Kaynak günlükleri, bu konumlara gönderebilmeniz için bir veri türüdür. 

Bazı kaynak günlüğü kategorilerini bu konumlara dışarı aktarmak için ek bir maliyet vardır. Dışarı aktarma maliyetlerine sahip bu Günlükler aşağıdaki tabloda listelenmiştir. Bu fiyatlandırma hakkında daha fazla bilgi için [Azure izleyici fiyatlandırma sayfasındaki](https://azure.microsoft.com/pricing/details/monitor/)platform günlükleri bölümüne bakın.

## <a name="supported-log-categories-per-resource-type"></a>Kaynak türü başına desteklenen günlük kategorileri

Aşağıda her bir kaynak türü için kullanılabilen günlük türlerinin bir listesi verilmiştir. 

Bazı kategoriler yalnızca belirli kaynak türleri için desteklenir. Bir kaynağınız eksik olduğunu düşünüyorsanız kaynağa özgü belgelere bakın. Örneğin, Microsoft. SQL/Servers/veritabanları kategorileri tüm veritabanı türleri için kullanılamaz. Daha fazla bilgi için bkz. [SQL veritabanı tanılama günlüğü bilgileri](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Eksik bir sorun olduğunu düşünüyorsanız, bu makalenin alt kısmında bir GitHub açıklaması açabilirsiniz.

## <a name="microsoftaaddomainservices"></a>Microsoft. AAD/DomainServices

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AccountLogon|AccountLogon|No|
|Hesap yönetimi|Hesap yönetimi|No|
|Ayrıntı Izleme|Ayrıntı Izleme|No|
|DirectoryServiceAccess|DirectoryServiceAccess|No|
|LogonLogoff|LogonLogoff|No|
|ObjectAccess|ObjectAccess|No|
|PolicyChange|PolicyChange|No|
|Ayrıcalıklı Geuse|Ayrıcalıklı Geuse|No|
|SystemSecurity|SystemSecurity|No|


## <a name="microsoftaadiamtenants"></a>Microsoft. aadihar/kiracılar

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Giriş|Giriş|Yes|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/sunucuları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Altyapı|Altyapı|No|
|Hizmet|Hizmet|No|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|GatewayLogs|Imanagementpackgateway ile ilgili Günlükler|No|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/Configurationmağazaları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|HttpRequest|HTTP Istekleri|Yes|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/yay

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|ApplicationConsole|Uygulama konsolu|No|
|Sistem günlükleri|Sistem günlükleri|No|


## <a name="microsoftattestationattestationproviders"></a>Microsoft. kanıtlama/attestationProviders

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AuditEvent|AuditEvent ileti günlüğü kategorisi.|No|
|HATA|Hata iletisi günlüğü kategorisi.|No|
|INF|Bilgilendirici ileti günlüğü kategorisi.|No|
|UYR|Uyarı iletisi günlüğü kategorisi.|No|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|DscNodeStatus|DSC düğüm durumu|No|
|JobLogs|İş günlükleri|No|
|JobStreams|İş akışları|No|


## <a name="microsoftautonomousdevelopmentplatformaccounts"></a>Microsoft. AutonomousDevelopmentPlatform/accounts

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Denetim|Denetim|Yes|
|Operasyonel|Operasyonel|Yes|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|ServiceLog|Hizmet günlükleri|No|


## <a name="microsoftbatchaiworkspaces"></a>ChAI/çalışma alanlarını Microsoft.Bat

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|No|
|BaiClusterNodeEvent|BaiClusterNodeEvent|No|
|BaiJobEvent|BaiJobEvent|No|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. Blockzincirine/blockchainMembers

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|BlockchainApplication|Blok zinciri uygulaması|No|
|FabricOrderer|Doku sırası|No|
|FabricPeer|Doku eşi|No|
|Ara sunucu|Ara sunucu|No|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. Blockzincirine/Cordadmembers

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|BlockchainApplication|Blok zinciri uygulaması|No|


## <a name="microsoftbotservicebotservices"></a>Microsoft. botservice/botservices

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|BotRequest|Kanallardan bot 'a istek|No|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|WebApplicationFirewallLogs|Web çıkışının başlatılıp başlatılmayacağını güvenlik duvarı günlükleri|No|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profiller

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AzureCdnAccessLog|Azure CDN erişim günlüğü|No|
|FrontDoorAccessLog|Frontkapısı erişim günlüğü|Yes|
|FrontDoorHealthProbeLog|Frontkapılı sistem durumu araştırma günlüğü|Yes|
|FrontDoorWebApplicationFirewallLog|Frontkapısı WebApplicationFirewall günlüğü|Yes|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft. CDN/profiller/uç noktaları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|CoreAnalytics|Uç noktanın ölçümlerini (örn. bant genişliği, çıkış vb.) alır.|No|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft. ClassicNetwork/networksecuritygroups

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Ağ güvenlik grubu kural akışı olayı|Ağ güvenlik grubu kural akışı olayı|No|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. Biliveservices/hesapları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Denetim|Denetim Günlükleri|No|
|RequestResponse|İstek ve yanıt günlükleri|No|
|İzleme|İzleme günlükleri|No|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/CommunicationServices

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Authopersel|İşletimsel kimlik doğrulama günlükleri|Yes|
|ChatOperational|İşletimsel sohbet günlükleri|No|
|SMSOperational|İşletimsel SMS günlükleri|No|
|Kullanım|Kullanım kayıtları|No|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/kayıt defterleri

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|ContainerRegistryLoginEvents|Oturum açma olayları|No|
|Containerregistrydepotoryevents|Olay günlüklerini günlüğe kaydeder|No|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/Managedkümeler

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|küme-otomatik Scaler|Kubernetes kümesi otomatik Scaler|No|
|Guard|Guard|No|
|kuin-apiserver|Kubernetes API sunucusu|No|
|kuin-denetim|Kubernetes denetimi|No|
|Kume-denetim-yönetici|Kubernetes denetimi yönetici günlükleri|No|
|kuin-Controller-Manager|Kubernetes Denetleyici Yöneticisi|No|
|kuin-Scheduler|Kubernetes Zamanlayıcı|No|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AuditLogs|Minırp çağrıları için denetim günlükleri|No|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft. D365CustomerInsights/örnekleri

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Denetim|Denetim olayları|No|
|Operasyonel|İşletimsel olaylar|No|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. Databricks/çalışma alanları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|accounts|Databricks hesapları|No|
|leriniz|Databricks kümeleri|No|
|dBFS|Databricks Dosya Sistemi|No|
|ınstancepools|Örnek havuzları|No|
|Çizelge|Databricks Işleri|No|
|not defteri|Databricks Not Defteri|No|
|kaynaklanır|Databricks gizli dizileri|No|
|sqlPermissions|Databricks SQLPermissions|No|
|SSH|Databricks SSH|No|
|çalışma alanı|Databricks çalışma alanı|No|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft. Dataişbirliği/çalışma alanları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Ortak çalışan Ationaudit|İşbirliği denetimi|Yes|
|Veri varlıkları|Veri varlıkları|No|
|Pipelines|Pipelines|No|
|Tekliflerden|Tekliflerden|No|
|Betikler|Betikler|No|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/Factory

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Etkinlik çalıştırmaları|İşlem hattı etkinlik günlüğü çalıştırmaları|No|
|Ardışık düzen eylemsizlik|İşlem hattı çalıştırmaları günlüğü|No|
|Sandboxactivityçalıştırmaları|Korumalı alan etkinliği günlüğü çalıştırır|Yes|
|Sandboxardışık düzen eylemsizlik|Sandbox işlem hattı çalıştırmaları günlüğü|Yes|
|Ssisıntegrationruntimelogs 'lar|SSIS tümleştirme çalışma zamanı günlükleri|No|
|SSISPackageEventMessageContext|SSIS paketi olay iletisi bağlamı|No|
|SSISPackageEventMessages|SSIS paketi olay iletileri|No|
|Ssispackageyürütülebilirstatıstıcs|SSIS paketi yürütülebilir istatistikleri|No|
|Ssispackageexecutioncomponentaşamalar|SSIS paketi yürütme bileşeni aşamaları|No|
|Ssispackageexecutiondatastatıstıcs|SSIS paketi veri istatistikleri istatistikleri|No|
|Triggerçalıştırmaları|Tetikleyici çalıştırma günlüğü|No|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/hesapları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Denetim|Denetim Günlükleri|No|
|İstekler|İstek günlükleri|No|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/accounts

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Denetim|Denetim Günlükleri|No|
|İstekler|İstek günlükleri|No|


## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/hesapları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|ReceivedShareSnapshots|Alınan paylaşma anlık görüntüleri|No|
|SentShareSnapshots|Gönderilen paylaşılan anlık görüntüler|No|
|Paylaşımlar|Paylaşımlar|No|
|Parça sayısı|Abonelikleri paylaşma|No|


## <a name="microsoftdbformariadbservers"></a>Microsoft. Dbformarıdb/sunucular

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|MySqlAuditLogs|MariaDB denetim günlükleri|No|
|Mysqlyavaşlatma günlükleri|MariaDB sunucu günlükleri|No|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. Dbformyısql/Flexibelservers

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|MySqlAuditLogs|MySQL denetim günlükleri|No|
|Mysqlyavaşlatma günlükleri|MySQL yavaş Günlükler|No|


## <a name="microsoftdbformysqlservers"></a>Microsoft. Dbformyısql/sunucuları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|MySqlAuditLogs|MySQL denetim günlükleri|No|
|Mysqlyavaşlatma günlükleri|MySQL Server günlükleri|No|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/Flexibtaservers

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Postgressqllogs|PostgreSQL sunucu günlükleri|No|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft. DBForPostgreSQL/serverGroupsv2

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Postgressqllogs|PostgreSQL sunucu günlükleri|Yes|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/sunucuları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Postgressqllogs|PostgreSQL sunucu günlükleri|No|
|QueryStoreRuntimeStatistics|PostgreSQL sorgu deposu çalışma zamanı Istatistikleri|No|
|QueryStoreWaitStatistics|PostgreSQL sorgu deposu bekleme Istatistikleri|No|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Postgressqllogs|PostgreSQL sunucu günlükleri|No|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Checkpoint|Checkpoint|No|
|Hata|Hata|No|
|Yönetim|Yönetim|No|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Yazılım durumu|Yazılım durumu|No|
|Checkpoint|Checkpoint|No|
|Bağlantı|Bağlantı|No|
|Hata|Hata|No|
|HostRegistration|HostRegistration|No|
|Yönetim|Yönetim|No|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/çalışma alanları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Checkpoint|Checkpoint|No|
|Hata|Hata|No|
|Akış|Akış|No|
|Yönetim|Yönetim|No|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft. Devices/Elaun havuzları/Iothubkiracılar

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|C2DCommands|C2D komutları|No|
|C2DTwinOperations|C2D Ikizi Işlemleri|No|
|Yapılandırmalar|Yapılandırmalar|No|
|Bağlantılar|Bağlantılar|No|
|D2CTwinOperations|D2CTwinOperations|No|
|Deviceıdentityoperations|Cihaz kimliği Işlemleri|No|
|DeviceStreams|Cihaz akışları (Önizleme)|No|
|Devicetelemetri|Cihaz Telemetrisi|No|
|DirectMethods|Doğrudan Yöntemler|No|
|Distributedizleme|Dağıtılmış Izleme (Önizleme)|No|
|FileUploadOperations|Karşıya dosya yükleme Işlemleri|No|
|JobsOperations|İş Işlemleri|No|
|Yollar|Yollar|No|
|TwinQueries|İkizi sorguları|No|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|C2DCommands|C2D komutları|No|
|C2DTwinOperations|C2D Ikizi Işlemleri|No|
|Yapılandırmalar|Yapılandırmalar|No|
|Bağlantılar|Bağlantılar|No|
|D2CTwinOperations|D2CTwinOperations|No|
|Deviceıdentityoperations|Cihaz kimliği Işlemleri|No|
|DeviceStreams|Cihaz akışları (Önizleme)|No|
|Devicetelemetri|Cihaz Telemetrisi|No|
|DirectMethods|Doğrudan Yöntemler|No|
|Distributedizleme|Dağıtılmış Izleme (Önizleme)|No|
|FileUploadOperations|Karşıya dosya yükleme Işlemleri|No|
|JobsOperations|İş Işlemleri|No|
|Yollar|Yollar|No|
|TwinQueries|İkizi sorguları|No|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|DeviceOperations Işlemleri|Cihaz Işlemleri|No|
|ServiceOperations|Hizmet İşlemleri|No|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/Digitaltwınsınstances

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|No|
|EventRoutesOperation|EventRoutesOperation|No|
|ModelsOperation|ModelsOperation|No|
|Queryoperation başvurusu destekleniyor|Queryoperation başvurusu destekleniyor|No|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>UmentDB/databaseAccounts Microsoft.Doc

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|CassandraRequests|CassandraRequests|No|
|ControlPlaneRequests|ControlPlaneRequests|No|
|DataPlaneRequests|DataPlaneRequests|No|
|GremlinRequests|GremlinRequests|No|
|MongoRequests|MongoRequests|No|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|No|
|PartitionKeyStatistics|PartitionKeyStatistics|No|
|QueryRuntimeStatistics|QueryRuntimeStatistics|No|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/Domains

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Deliveryarızaları|Teslim hatası günlükleri|No|
|Publisharızaları|Hata günlüklerini Yayımla|No|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Deliveryarızaları|Teslim hatası günlükleri|No|
|Publisharızaları|Hata günlüklerini Yayımla|No|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/Partnerkonuları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Deliveryarızaları|Teslim hatası günlükleri|No|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/Systemkonuları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Deliveryarızaları|Teslim hatası günlükleri|No|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/konuları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Deliveryarızaları|Teslim hatası günlükleri|No|
|Publisharızaları|Hata günlüklerini Yayımla|No|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/ad alanları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|ArchiveLogs|Arşiv günlükleri|No|
|Oto Scalelogs|Günlükleri otomatik ölçeklendir|No|
|CustomerManagedKeyUserLogs|Müşteri tarafından yönetilen anahtar günlükleri|No|
|EventHubVNetConnectionEvent|VNet/IP filtreleme bağlantı günlükleri|No|
|KafkaCoordinatorLogs|Kafka Coordinator günlükleri|No|
|KafkaUserErrorLogs|Kafka Kullanıcı hatası günlükleri|No|
|OperationalLogs|İşletimsel Günlükler|No|


## <a name="microsoftexperimentationexperimentworkspaces"></a>Microsoft. deneme/experimentWorkspaces

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|İstek|İstek|No|


## <a name="microsofthealthcareapisservices"></a>Microsoft. Healthgelişme API 'leri/Hizmetleri

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AuditLogs|Denetim günlükleri|No|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/oto scalesettings

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Oto Scaledeğerlendirmeleri|Otomatik ölçeklendirme değerlendirmeleri|No|
|Oto Scalescaleactions|Otomatik ölçeklendirme ölçeklendirme eylemleri|No|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/bileşenler

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AppAvailabilityResults|Kullanılabilirlik sonuçları|No|
|Appbrowserzamanlamalar|Tarayıcı zamanlamaları|No|
|AppDependencies|Bağımlılıklar|No|
|AppEvents|Ekinlikler|No|
|AppExceptions|Özel durumlar|No|
|Appölçümler|Ölçümler|No|
|AppPageViews|Sayfa görünümleri|No|
|AppPerformanceCounters|Performans sayaçları|No|
|AppRequests|İstekler|No|
|AppSystemEvents|Sistem olayları|No|
|Appizlemeler|İzlemeler|No|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. keykasası/managedhsms

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AuditEvent|Denetim Olayı|No|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. Keykasası/kasa

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AuditEvent|Denetim Günlükleri|No|


## <a name="microsoftkustoclusters"></a>Microsoft. kusto/kümeler

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Komut|Komut|No|
|FailedIngestion|Başarısız alma işlemleri|No|
|İçeri toplu işleme|Alma toplu işlemi|No|
|Sorgu|Sorgu|No|
|SucceededIngestion|Başarılı alma işlemleri|No|
|Tabloayrıntıları|Tablo ayrıntıları|No|
|Tablousagestatistika|Tablo kullanım istatistikleri|No|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft. Logic/ıntegrationaccounts

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Integrationaccounttrackingevents|Tümleştirme hesabı izleme olayları|No|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/iş akışları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Başlatıldıktan|İş akışı çalışma zamanı tanılama olayları|No|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/çalışma alanları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|No|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|No|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|No|
|AmlComputeJobEvent|AmlComputeJobEvent|No|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|No|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/mediaservices

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|KeyDeliveryRequests|Anahtar teslim Istekleri|No|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/Applicationgateway 'ler

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|ApplicationGatewayAccessLog|Application Gateway erişim günlüğü|No|
|ApplicationGatewayFirewallLog|Application Gateway Güvenlik duvarı günlüğü|No|
|ApplicationGatewayPerformanceLog|Application Gateway performans günlüğü|No|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AzureFirewallApplicationRule|Azure Güvenlik Duvarı uygulama kuralı|No|
|AzureFirewallDnsProxy|Azure Güvenlik duvarı DNS proxy 'Si|No|
|AzureFirewallNetworkRule|Azure Güvenlik Duvarı ağ kuralı|No|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Savunma günlükleri|Savunma denetim günlükleri|No|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/Expressroutedevreleri

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|PeeringRouteLog|Yol tablosu günlüklerini eşleme|No|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontkapaklı

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|FrontdoorAccessLog|Frontkapısı erişim günlüğü|No|
|FrontdoorWebApplicationFirewallLog|Frontkapılı Web uygulaması güvenlik duvarı günlüğü|No|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|LoadBalancerAlertEvent|Uyarı olaylarını Load Balancer|No|
|LoadBalancerProbeHealthStatus|Load Balancer araştırma sistem durumu|No|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft. Network/networksecuritygroups

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|NetworkSecurityGroupEvent|Ağ güvenlik grubu olayı|No|
|NetworkSecurityGroupFlowEvent|Ağ güvenlik grubu kural akışı olayı|No|
|NetworkSecurityGroupRuleCounter|Ağ güvenlik grubu kuralı sayacı|No|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|GatewayDiagnosticLog|Ağ geçidi tanılama günlükleri|No|
|Ikediagnosticlog|IKE tanılama günlükleri|No|
|P2SDiagnosticLog|P2S tanılama günlükleri|No|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/Publicıpaddresses

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|DDoSMitigationFlowLogs|DDoS azaltma kararlarının akış günlükleri|No|
|DDoSMitigationReports|DDoS azaltmaları raporları|No|
|DDoSProtectionNotifications|DDoS koruması bildirimleri|No|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Probehealthkara Sevents|Araştırma durumu sonuçları olayını Traffic Manager|No|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/Virtualnetworkgateway 'ler

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|GatewayDiagnosticLog|Ağ geçidi tanılama günlükleri|No|
|Ikediagnosticlog|IKE tanılama günlükleri|No|
|P2SDiagnosticLog|P2S tanılama günlükleri|No|
|RouteDiagnosticLog|Tanılama günlüklerini yönlendirme|No|
|Tüneldiagnosticlog|Tünel tanılama günlükleri|No|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|VMProtectionAlerts|VM koruma uyarıları|No|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/Vpngateway 'ler

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|GatewayDiagnosticLog|Ağ geçidi tanılama günlükleri|No|
|Ikediagnosticlog|IKE tanılama günlükleri|No|
|RouteDiagnosticLog|Tanılama günlüklerini yönlendirme|No|
|Tüneldiagnosticlog|Tünel tanılama günlükleri|No|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft. Notificationhub 'Lar/ad alanları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|OperationalLogs|İşletimsel Günlükler|No|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. Operationalınsights/çalışma alanları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Denetim|Denetim Günlükleri|No|


## <a name="microsoftpowerbitenants"></a>Microsoft. PowerBI/kiracılar

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Altyapı|Altyapı|No|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft. PowerBI/kiracılar/çalışma alanları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Altyapı|Altyapı|No|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. Powerbiadanmış/kapasiteler

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Altyapı|Altyapı|No|


## <a name="microsoftpurviewaccounts"></a>Microsoft. purview/hesapları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|No|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft. RecoveryServices/kasa

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AddonAzureBackupAlerts|Eklenti Azure Backup uyarı verisi|No|
|AddonAzureBackupJobs|Eklenti Azure Backup Iş verileri|No|
|AddonAzureBackupPolicy|Eklenti Azure Backup Ilkesi verileri|No|
|AddonAzureBackupProtectedInstance|Eklenti Azure Backup korumalı örnek verileri|No|
|AddonAzureBackupStorage|Eklenti Azure Backup depolama verileri|No|
|AzureBackupReport|Raporlama verilerini Azure Backup|No|
|Azuresterekapak Yevents|Azure Site Recovery olaylar|No|
|AzureSiteRecoveryJobs|Azure Site Recovery Işleri|No|
|AzureSiteRecoveryProtectedDiskDataChurn|Korunan disk verileri dalgalanmasını Azure Site Recovery|No|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery kurtarma noktaları|No|
|AzureSiteRecoveryReplicatedItems|Çoğaltılan öğeleri Azure Site Recovery|No|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery çoğaltma verilerini karşıya yükleme hızı|No|
|AzureSiteRecoveryReplicationStats|Azure Site Recovery çoğaltma Istatistikleri|No|
|CoreAzureBackup|Çekirdek Azure Backup verileri|No|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/Namespace

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|HybridConnectionsEvent|HybridConnections olayları|No|
|HybridConnectionsLogs|HybridConnectionsLogs|No|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|OperationLogs|İşlem günlükleri|No|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/ad alanları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|OperationalLogs|İşletimsel Günlükler|No|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/SignalR

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AllLogs|Azure SignalR hizmeti günlükleri.|No|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft. SignalRService/WebPubSub

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AllLogs|Azure Web PubSub hizmeti günlükleri.|Yes|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/ManagedInstances

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Devopsoperationsaudıt|DevOps işlemleri denetim günlükleri|No|
|ResourceUsageStats|Kaynak kullanım Istatistikleri|No|
|SQLSecurityAuditEvents|SQL güvenlik denetim olayı|No|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/ManagedInstances/veritabanları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Hatalar|Hatalar|No|
|QueryStoreRuntimeStatistics|Sorgu deposu çalışma zamanı Istatistikleri|No|
|QueryStoreWaitStatistics|Sorgu deposu bekleme Istatistikleri|No|
|Sqlinsıghts|SQL İçgörüleri|No|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/Servers/veritabanları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Otomatik olarak ayarlama|Otomatik ayarlama|No|
|Bloklar|Bloklar|No|
|DatabaseWaitStatistics|Veritabanı bekleme Istatistikleri|No|
|Kilitlenmeler|Kilitlenmeler|No|
|Devopsoperationsaudıt|DevOps işlemleri denetim günlükleri|No|
|Dmsçalışanları|DMS çalışanları|No|
|Hatalar|Hatalar|No|
|ExecRequests|Yürütme Istekleri|No|
|QueryStoreRuntimeStatistics|Sorgu deposu çalışma zamanı Istatistikleri|No|
|QueryStoreWaitStatistics|Sorgu deposu bekleme Istatistikleri|No|
|RequestSteps|İstek adımları|No|
|Sqlinsıghts|SQL İçgörüleri|No|
|SqlRequests|SQL Istekleri|No|
|SQLSecurityAuditEvents|SQL güvenlik denetim olayı|No|
|Zaman aşımları|Zaman aşımları|No|
|Bekler|Bekler|No|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Yazma|Yazma|No|
|Yürütme|Yürütme|No|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. SYNAPSE/çalışma alanları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|BuiltinSqlReqsEnded|Yerleşik SQL havuzu Istekleri sona erdi|No|
|GatewayApiRequests|SYNAPSE ağ geçidi API 'Si Istekleri|No|
|Integrationactivityçalıştırmaları|Tümleştirme etkinliği çalıştırmaları|Yes|
|Integrationardışık düzen eylemsizlik|Tümleştirme işlem hattı çalıştırmaları|Yes|
|Integrationtriggerçalıştırmaları|Tümleştirme tetikleyicisi çalıştırmaları|Yes|
|SQLSecurityAuditEvents|SQL güvenlik denetim olayı|No|
|SynapseRbacOperations|SYNAPSE RBAC Işlemleri|No|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. SYNAPSE/Workspaces/bigDataPools

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|BigDataPoolAppsEnded|Büyük veri havuzu uygulamaları sona erdi|No|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. SYNAPSE/Workspaces/Sqlhavuzlar

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Dmsçalışanları|DMS çalışanları|No|
|ExecRequests|Yürütme Istekleri|No|
|RequestSteps|İstek adımları|No|
|SqlRequests|SQL Istekleri|No|
|SQLSecurityAuditEvents|SQL güvenlik denetim olayı|No|
|Bekler|Bekler|No|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. Timeseriesınsights/ortamlar

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Giriş|Giriş|No|
|Yönetim|Yönetim|No|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. Timeseriesınsights/ortamlar/EventSources

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|Giriş|Giriş|No|
|Yönetim|Yönetim|No|


## <a name="microsoftwebhostingenvironments"></a>Microsoft. Web/hostingenvironments

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|App Service Ortamı platform günlükleri|No|


## <a name="microsoftwebsites"></a>Microsoft. Web/siteler

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Virüsten koruma denetim günlüklerini raporla|No|
|AppServiceAppLogs|Uygulama günlüklerini App Service|No|
|AppServiceAuditLogs|Denetim günlüklerine erişim|No|
|AppServiceConsoleLogs|Konsol günlüklerini App Service|No|
|AppServiceFileAuditLogs|Site Içeriği değişikliği denetim günlükleri|No|
|AppServiceHTTPLogs|HTTP günlükleri|No|
|AppServiceIPSecAuditLogs|IPSecurity denetim günlükleri|No|
|AppServicePlatformLogs|App Service platform günlükleri|No|
|FunctionAppLogs|İşlev uygulama günlükleri|No|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/Yuvaları

|Kategori|Kategori görünen adı|Dışarı aktarılacak maliyetler|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Virüsten koruma denetim günlüklerini raporla|No|
|AppServiceAppLogs|Uygulama günlüklerini App Service|No|
|AppServiceAuditLogs|Denetim günlüklerine erişim|No|
|AppServiceConsoleLogs|Konsol günlüklerini App Service|No|
|AppServiceFileAuditLogs|Site Içeriği değişikliği denetim günlükleri|No|
|AppServiceHTTPLogs|HTTP günlükleri|No|
|AppServiceIPSecAuditLogs|IPSecurity denetim günlükleri|No|
|AppServicePlatformLogs|App Service platform günlükleri|No|
|FunctionAppLogs|İşlev uygulama günlükleri|No|


## <a name="next-steps"></a>Sonraki Adımlar

* [Kaynak günlükleri hakkında daha fazla bilgi edinin](../essentials/platform-logs-overview.md)
* [**Event Hubs** için kaynak kaynağı günlüklerini akışla](./resource-logs.md#send-to-azure-event-hubs)
* [Azure Izleyici REST API kullanarak kaynak günlüğü tanılama ayarlarını değiştirme](/rest/api/monitor/diagnosticsettings)
* [Azure depolama 'daki günlükleri Log Analytics ile analiz etme](./resource-logs.md#send-to-log-analytics-workspace)

