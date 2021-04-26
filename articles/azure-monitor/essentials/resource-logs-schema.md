---
title: Azure kaynak günlüğü desteklenen Hizmetleri ve şemaları
description: Azure Kaynak günlükleri için desteklenen Hizmetleri ve olay şemasını anlayın.
ms.topic: reference
ms.date: 04/07/2020
ms.openlocfilehash: 09415353d93313f1b3c4844202b59f577ef3838d
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105774"
---
# <a name="common-and-service-specific-schema-for-azure-resource-logs"></a>Azure Kaynak günlükleri için ortak ve hizmete özgü şema

> [!NOTE]
> Kaynak günlükleri daha önce tanılama günlükleri olarak bilinirdi. Azure Izleyici tarafından toplanan günlüklerin türleri yalnızca Azure kaynağına eşit olacak şekilde kaydırılacağı için ad Ekim 2019 ' de değiştirilmiştir. Ayrıca, bu makalede listelenmak üzere toplayacağınız kaynak günlüğü kategorilerinin listesi. Bunlar artık [kaynak günlük kategorileridir](resource-logs-categories.md). 

Azure [izleyici kaynak günlükleri](../essentials/platform-logs-overview.md) , bu hizmet veya kaynakların çalışmasını tanımlayan Azure hizmetleri tarafından oluşturulan günlüklerdir. Azure Izleyici aracılığıyla kullanılabilen tüm kaynak günlükleri, her bir hizmetin kendi olayları için benzersiz özellikler yaymasını sağlayan ortak bir üst düzey şemayı paylaşır.

Kaynak türünün bir birleşimi ( `resourceId` özellikte mevcuttur) ve `category` bir şemayı benzersiz bir şekilde tanımlar. Bu makalede, kaynak günlükleri için en üst düzey şema ve her hizmet için şemaların serileştirilmesi bağlantıları açıklanmaktadır.


## <a name="top-level-common-schema"></a>Üst düzey ortak şema

| Name | Gerekli/İsteğe Bağlı | Description |
|---|---|---|
| time | Gerekli | Olayın zaman damgası (UTC). |
| resourceId | Gerekli | Olayı veren kaynağın kaynak KIMLIĞI. Kiracı Hizmetleri için bu,/Tenants/Tenant-id/Providers/Provider-nameformundadır. |
| Değerine | Kiracı günlükleri için gereklidir | Bu olayın bağlı olduğu Active Directory kiracının kiracı KIMLIĞI. Bu özellik yalnızca kiracı düzeyindeki Günlükler için kullanılır, kaynak düzeyinde günlüklerde görünmez. |
| operationName | Gerekli | Bu olayla temsil edilen işlemin adı. Olay bir Azure RBAC işlemini gösteriyorsa, bu Azure RBAC işlem adıdır (örneğin, Microsoft. Storage/storageAccounts/blobServices/blob/Read). Genellikle Kaynak Yöneticisi bir işlem biçiminde modellenmiştir, bunlar gerçek belgelenmiş Kaynak Yöneticisi işlemleri () olmasalar bile `Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>` |
| operationVersion | İsteğe Bağlı | OperationName bir API kullanılarak gerçekleştirildiyse (örneğin,), işlemle ilişkili api sürümü `http://myservice.windowsazure.net/object?api-version=2016-06-01` . Bu işleme karşılık gelen bir API yoksa, sürüm, gelecekte işlemle ilişkili özellikler, bu işlemin sürümünü temsil eder. |
| category | Gerekli | Etkinliğin günlük kategorisi. Kategori, belirli bir kaynaktaki günlükleri etkinleştirebilmeniz veya devre dışı bırakabilmeniz için gereken ayrıntı düzeyi. Bir olayın Özellikler blobu içinde görünen özellikler, belirli bir günlük kategorisi ve kaynak türü içinde aynıdır. Tipik günlük kategorileri "Audit" "Işletimsel" "yürütme" ve "Istek" dir. |
| resultType | İsteğe Bağlı | Etkinliğin durumu. Tipik değerler başlangıç, devam etme, başarılı, başarısız, etkin ve Çözümlenmiş. |
| resultSignature | İsteğe Bağlı | Etkinliğin alt durumu. Bu işlem bir REST API çağrısına karşılık geliyorsa, bu alan karşılık gelen REST çağrısının HTTP durum kodudur. |
| resultDescription | İsteğe Bağlı | Bu işlemin statik metin açıklaması (örneğin, "depolama dosyası Al"). |
| durationMs | İsteğe Bağlı | İşlem süresi (milisaniye cinsinden). |
| callerIpAddress | İsteğe Bağlı | İşlem, genel olarak kullanılabilir IP adresine sahip bir varlıktan gelen bir API çağrısına karşılık geliyorsa, çağıran IP adresi. |
| correlationId | İsteğe Bağlı | Bir dizi ilgili olayı gruplamak için kullanılan GUID. Genellikle, iki olay aynı operationName öğesine, ancak iki farklı durum (örneğin, "başlatılmış" ve "başarılı") varsa, aynı bağıntı KIMLIĞINI paylaşır. Bu, olaylar arasındaki diğer ilişkileri de temsil edebilir. |
| identity | İsteğe Bağlı | İşlemi gerçekleştiren kullanıcının veya uygulamanın kimliğini açıklayan bir JSON blobu. Bu alan genellikle Active Directory 'den yetkilendirme ve talepler/JWT belirtecini içerir. |
| Level | İsteğe Bağlı | Etkinliğin önem düzeyi. Bilgilendirici, uyarı, hata veya kritik bir olmalıdır. |
| location | İsteğe Bağlı | Olayı yayan kaynak bölgesi, örneğin "Doğu ABD" veya "Fransa Güney" |
| properties | İsteğe Bağlı | Bu belirli olay kategorisiyle ilgili genişletilmiş özellikler. Tüm özel/benzersiz özellikler şemanın "Bölüm B" içinde yerleştirilmelidir. |

## <a name="service-specific-schemas"></a>Hizmete özgü şemalar

Kaynak günlükleri şeması, kaynak ve günlük kategorisine göre değişir. Bu liste, kullanılabilir olduğunda, kullanılabilir kaynak günlüklerini ve hizmet ve kategoriye özgü şemaya bağlantıları yapan Hizmetleri gösterir. Yeni hizmetler eklendikçe bu liste tüm zamanı değiştiriyor, bu nedenle, aşağıda gerekli olanları görmüyorsanız, ek belge bulmak için bir arama altyapısı kullanın. Bu makalede, güncelleştirebilmemiz için bir GitHub sorunu açmanız ücretsizdir.

| Hizmet | Şema & belgeleri |
| --- | --- |
| Azure Active Directory | [Genel bakış](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [Denetim günlüğü şeması](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) ve [oturum açma şeması](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | [Azure Analysis Services-tanılama günlüğü kurulumu](../../analysis-services/analysis-services-logging.md) |
| API Management | [Kaynak günlüklerini API Management](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| App Service | [App Service günlükleri](../../app-service/troubleshoot-diagnostic-logs.md)
| Uygulama Ağ Geçitleri |[Application Gateway için günlüğe kaydetme](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Otomasyonu |[Azure Otomasyonu için Log Analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Günlüğe kaydetme Azure Batch](../../batch/batch-diagnostics.md) |
| Bilişsel Hizmetler | [Azure bilişsel hizmetler için günlüğe kaydetme](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Azure Container Registry için günlüğe kaydetme](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [CDN için Azure günlükleri](../../cdn/cdn-azure-diagnostic-logs.md) |
| Cosmos DB | [Günlüğe kaydetme Azure Cosmos DB](../../cosmos-db/monitor-cosmos-db.md) |
| Data Factory | [Azure Izleyici 'yi kullanarak veri fabrikalarını izleme](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Azure Data Lake Analytics için günlüklere erişme](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Storage |[Azure Data Lake Store için günlüklere erişme](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure Veri Gezgini | [Azure Veri Gezgini günlükleri](/azure/data-explorer/using-diagnostic-logs) |
| MySQL için Azure Veritabanı | [MySQL için Azure veritabanı tanılama günlükleri](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| PostgreSQL için Azure Veritabanı | [PostgreSQL için Azure veritabanı günlükleri](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Azure Databricks'te tanılama günlüğü](/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| DDOS Koruması | [Azure DDoS koruması standardı için günlüğe kaydetme](../../ddos-protection/diagnostic-logging.md#log-schemas) |
| Azure Digital Twins | [Azure dijital TWINS tanılamayı ayarlama](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Event Hubs |[Azure Event Hubs günlükleri](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Şema kullanılamıyor. |
| Azure Güvenlik Duvarı | Şema kullanılamıyor. |
| Front Door | [Ön kapı için günlüğe kaydetme](../../frontdoor/front-door-diagnostics.md) |
| IoT Hub | [IoT Hub Işlemler](../../iot-hub/monitor-iot-hub-reference.md#resource-logs) |
| Key Vault |[Günlüğe kaydetme Azure Key Vault](../../key-vault/general/logging.md) |
| Kubernetes Hizmeti |[Azure Kubernetes günlüğü](../../aks/view-control-plane-logs.md#log-event-schema) |
| Load Balancer |[Azure Load Balancer için Log Analytics](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B özel izleme şeması](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Ağ Güvenlik Grupları |[Ağ güvenlik grupları (NSG’ler) için Log Analytics](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Power BI Ayrılmış | [Azure 'da Power BI Embedded için günlüğe kaydetme](/power-bi/developer/azure-pbie-diag-logs) |
| Kurtarma Hizmetleri | [Azure Backup için veri modeli](../../backup/backup-azure-reports-data-model.md)|
| Arayın |[Arama Trafik Analizi etkinleştirme ve kullanma](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus günlükleri](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Veritabanı | [Azure SQL veritabanı günlüğü](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Stream Analytics |[İş günlükleri](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Depolama | [BLOB 'lar](/azure/storage/blobs/monitor-blob-storage-reference#resource-logs-preview), [dosyalar](/azure/storage/files/storage-files-monitoring-reference#resource-logs-preview), [Kuyruklar](/azure/storage/queues/monitor-queue-storage-reference#resource-logs-preview),  [Tablolar](/azure/storage/tables/monitor-table-storage-reference#resource-logs-preview) |
| Traffic Manager | [Traffic Manager log şeması](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Sanal Ağlar | Şema kullanılamıyor. |
| Sanal Ağ Geçitleri | Şema kullanılamıyor. |



## <a name="next-steps"></a>Sonraki Adımlar

* [Toplayacağınız kaynak günlüğü kategorilerine bakın](resource-logs-categories.md)
* [Kaynak günlükleri hakkında daha fazla bilgi edinin](../essentials/platform-logs-overview.md)
* [**Event Hubs** için kaynak kaynağı günlüklerini akışla](./resource-logs.md#send-to-azure-event-hubs)
* [Azure Izleyici REST API kullanarak kaynak günlüğü tanılama ayarlarını değiştirme](/rest/api/monitor/diagnosticsettings)
* [Azure depolama 'daki günlükleri Log Analytics ile analiz etme](./resource-logs.md#send-to-log-analytics-workspace)
