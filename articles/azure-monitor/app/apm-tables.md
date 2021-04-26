---
title: Azure Izleyici Application Insights çalışma alanı tabanlı kaynak şeması
description: Azure Izleyici Application Insights çalışma alanı tabanlı kaynakların yeni tablo yapısı ve şeması hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 05/09/2020
ms.openlocfilehash: ef9d22cd2b45679928ee54778b2a521ea9ecab03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575609"
---
# <a name="workspace-based-resource-changes"></a>Çalışma alanı tabanlı kaynak değişiklikleri

[Çalışma alanı tabanlı Application Insights kaynaklarının](create-workspace-resource.md)kullanıma sunulmasından önce Application Insights verileri Azure izleyici 'deki diğer günlük verilerinden ayrı olarak depolandı. Her ikisi de Azure Veri Gezgini tabanlıdır ve aynı kusto sorgu dilini (KQL) kullanır. Bu, [Azure izleyici 'de günlüklerde](../logs/data-platform-logs.md)açıklanmıştır.

Çalışma alanı tabanlı Application Insights kaynak verileri, diğer izleme verileri ve uygulama verileriyle birlikte bir Log Analytics çalışma alanında depolanır. Bu, birden çok çözüm genelinde verileri daha kolay analiz etmenize ve çalışma alanlarının özelliklerinden yararlanmasını sağlayarak yapılandırmanızı basitleştirir.

## <a name="table-structure"></a>Tablo yapısı

| Eski tablo adı | Tablonun yeni adı | Description |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  Kullanılabilirlik testlerinden özet veriler.|
| Browserzamanlamalar | Appbrowserzamanlamalar | Gelen verileri işlemek için geçen süre gibi istemci performansı hakkındaki veriler.|
| bağımlılıklar | AppDependencies | Uygulamadan, TrackDependency () ile kaydedilen diğer bileşenlere (Dış bileşenler dahil) yapılan çağrılar (örneğin, REST API, veritabanı veya dosya sistemine yapılan çağrılar).  |
| customEvents | AppEvents | Uygulamanız tarafından oluşturulan özel olaylar. |
| customMetrics | Appölçümler | Uygulamanız tarafından oluşturulan özel ölçümler. |
| pageViews | AppPageViews| Tarayıcı bilgileriyle her bir Web sitesi görünümüyle ilgili veriler. |
| performanceCounters | AppPerformanceCounters | Uygulamayı destekleyen işlem kaynaklarından performans ölçümleri, örneğin, Windows performans sayaçları. |
| istekleri | AppRequests | Uygulamanız tarafından alınan istekler. Örneğin, Web uygulamanızın aldığı her HTTP isteği için ayrı bir istek kaydı kaydedilir.  |
| larý | AppSystemEvents | Uygulama çalışma zamanı tarafından oluşturulan özel durumlar, hem sunucu tarafı hem de istemci tarafı (tarayıcı) özel durumlarını yakalar. |
| lerin | Appizlemeler | TrackTrace () ile kaydedilen uygulama kodu/günlük çerçeveleri aracılığıyla yayılan ayrıntılı Günlükler (izlemeler). |

## <a name="table-schemas"></a>Tablo şemaları

Aşağıdaki bölümlerde, klasik Özellik adları ve yeni çalışma alanı tabanlı Application Insights Özellik adları arasındaki eşleme gösterilmektedir.  Eski tabloları kullanarak herhangi bir sorguyu dönüştürmek için bu bilgileri kullanın.

Sütunların çoğu, farklı bir büyük harfle aynı ada sahiptir. KQL büyük/küçük harfe duyarlı olduğundan, her bir sütun adını, varolan sorgularda tablo adlarıyla birlikte değiştirmeniz gerekir. Büyük/küçük harfe ek olarak değişiklik içeren sütunlar vurgulanır. Klasik Application Insights sorgularınızı, çalışma alanı tabanlı bir kaynak olsa bile, Application Insights kaynağınızın **Günlükler** bölmesinde kullanmaya devam edebilirsiniz. Log Analytics çalışma alanı deneyimi bağlamı içinden sorgulama yaparken yeni özellik adları gereklidir.

### <a name="appavailabilityresults"></a>AppAvailabilityResults

Eski tablo: kullanılabilirlik

|ApplicationInsights|Tür|Günlüğe kaydetme Analizi|Tür|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|Clientstateoril|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Approtaınstance|string|
|cloud_RoleName|string|Approtaname|string|
|customDimensions|dynamic|Özellikler|Dinamik|
|Customölçüler|dynamic|Ölçümler|Dinamik|
|süre|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|ItemCount|int|ItemCount|int|
|ID|string|\_ID|string|
|ItemType|string|Tür|Dize|
|location|string|Konum|string|
|message|string|İleti|string|
|name|string|Name|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|Operationparentıd|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|SDK sürümü|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|boyut|real|Boyut|real|
|başarılı|string|Başarılı|Bool|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|Useraccountıd|string|
|user_AuthenticatedId|string|Userauthenticatedıd|string|
|user_Id|string|UserId|string|

### <a name="appbrowsertimings"></a>Appbrowserzamanlamalar

Eski tablo: Browserzamanlamalar

|ApplicationInsights|Tür|Günlüğe kaydetme Analizi|Tür|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|Clientstateoril|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Approtaınstance|string|
|cloud_RoleName|string|Approtaname|string|
|customDimensions|dynamic|Özellikler|Dinamik|
|Customölçüler|dynamic|Ölçümler|Dinamik|
|iKey|string|IKey|string|
|ItemCount|int|ItemCount|int|
|ID|string|\_ID|string|
|ItemType|string|Tür|string|
|name|string|Name|datetime|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|Operationparentıd|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|SDK sürümü|string|SdkVersion|string|
|sendDuration|real|SendDurationMs|real|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|Toplam süre|real|TotalDurationMs|real|
|url|string|Url|string|
|user_AccountId|string|Useraccountıd|string|
|user_AuthenticatedId|string|Userauthenticatedıd|string|
|user_Id|string|UserId|string|

### <a name="appdependencies"></a>AppDependencies

Eski tablo: bağımlılıklar

|ApplicationInsights|Tür|Günlüğe kaydetme Analizi|Tür|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|Clientstateoril|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Approtaınstance|string|
|cloud_RoleName|string|Approtaname|string|
|customDimensions|dynamic|Özellikler|Dinamik|
|Customölçüler|dynamic|Ölçümler|Dinamik|
|veriler|string|Veriler|string|
|süre|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|ItemCount|int|ItemCount|int|
|ID|string|\_ID|string|
|ItemType|string|Tür|Dize|
|name|string|Name|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|Operationparentıd|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|resultCode|string|ResultCode|string|
|SDK sürümü|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|başarılı|string|Başarılı|Bool|
|hedef|string|Hedef|string|
|timestamp|datetime|TimeGenerated|datetime|
|tür|string|DependencyType|string|
|user_AccountId|string|Useraccountıd|string|
|user_AuthenticatedId|string|Userauthenticatedıd|string|
|user_Id|string|UserId|string|

### <a name="appevents"></a>AppEvents

Eski tablo: customEvents

|ApplicationInsights|Tür|Günlüğe kaydetme Analizi|Tür|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|Clientstateoril|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Approtaınstance|string|
|cloud_RoleName|string|Approtaname|string|
|customDimensions|dynamic|Özellikler|Dinamik|
|Customölçüler|dynamic|Ölçümler|Dinamik|
|iKey|string|IKey|string|
|ItemCount|int|ItemCount|int|
|ID|string|\_ID|string|
|ItemType|string|Tür|string|
|name|string|Name|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|Operationparentıd|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|SDK sürümü|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|Useraccountıd|string|
|user_AuthenticatedId|string|Userauthenticatedıd|string|
|user_Id|string|UserId|string|

### <a name="appmetrics"></a>Appölçümler

Eski tablo: Customölçümler

|ApplicationInsights|Tür|Günlüğe kaydetme Analizi|Tür|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|Clientstateoril|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Approtaınstance|string|
|cloud_RoleName|string|Approtaname|string|
|customDimensions|dynamic|Özellikler|Dinamik|
|iKey|string|IKey|string|
|ID|string|\_ID|string|
|ItemType|string|Tür|string|
|name|string|Name|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|Operationparentıd|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|SDK sürümü|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|Useraccountıd|string|
|user_AuthenticatedId|string|Userauthenticatedıd|string|
|user_Id|string|UserId|string|
|değer|real|kaldırıldı||
|valueCount|int|ValueCount|int|
|valueMax|real|ValueMax|real|
|valueMin|real|ValueMin|real|
|valueStdDev|real|ValueStdDev|real|
|valueSum|real|ValueSum|real|

### <a name="apppageviews"></a>AppPageViews

Eski tablo: pageViews

|ApplicationInsights|Tür|Günlüğe kaydetme Analizi|Tür|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|Clientstateoril|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Approtaınstance|string|
|cloud_RoleName|string|Approtaname|string|
|customDimensions|dynamic|Özellikler|Dinamik|
|Customölçüler|dynamic|Ölçümler|Dinamik|
|süre|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|ItemCount|int|ItemCount|int|
|ID|string|\_ID|string|
|ItemType|string|Tür|Dize|
|name|string|Name|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|Operationparentıd|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|SDK sürümü|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|url|string|Url|string|
|user_AccountId|string|Useraccountıd|string|
|user_AuthenticatedId|string|Userauthenticatedıd|string|
|user_Id|string|UserId|string|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Eski tablo: performanceCounters

|ApplicationInsights|Tür|Günlüğe kaydetme Analizi|Tür|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|category|string|Kategori|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|Clientstateoril|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Approtaınstance|string|
|cloud_RoleName|string|Approtaname|string|
|counter|string|kaldırıldı||
|customDimensions|dynamic|Özellikler|Dinamik|
|iKey|string|IKey|string|
|örnek|string|Örnek|string|
|ID|string|\_ID|string|
|ItemType|string|Tür|string|
|name|string|Name|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|Operationparentıd|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|SDK sürümü|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|Useraccountıd|string|
|user_AuthenticatedId|string|Userauthenticatedıd|string|
|user_Id|string|UserId|string|
|değer|real|Değer|real|

### <a name="apprequests"></a>AppRequests

Eski tablo: istekler

|ApplicationInsights|Tür|Günlüğe kaydetme Analizi|Tür|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|Clientstateoril|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Approtaınstance|string|
|cloud_RoleName|string|Approtaname|string|
|customDimensions|dynamic|Özellikler|Dinamik|
|Customölçüler|dynamic|Ölçümler|Dinamik|
|süre|real|DurationMs|Gerçek|
|`id`|string|`Id`|Dize|
|iKey|string|IKey|string|
|ItemCount|int|ItemCount|int|
|ID|string|\_ID|string|
|ItemType|string|Tür|Dize|
|name|string|Name|Dize|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|Operationparentıd|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|Dize|
|resultCode|string|ResultCode|Dize|
|SDK sürümü|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|kaynak|dize|Kaynak|Dize|
|başarılı|string|Başarılı|Bool|
|timestamp|datetime|TimeGenerated|datetime|
|url|string|Url|Dize|
|user_AccountId|string|Useraccountıd|string|
|user_AuthenticatedId|string|Userauthenticatedıd|string|
|user_Id|string|UserId|string|

### <a name="appsystemevents"></a>AppSystemEvents

Eski tablo: özel durumlar

|ApplicationInsights|Tür|Günlüğe kaydetme Analizi|Tür|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|derleme|string|Bütünleştirilmiş Kod|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|Clientstateoril|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Approtaınstance|string|
|cloud_RoleName|string|Approtaname|string|
|customDimensions|dynamic|Özellikler|dynamic|
|Customölçüler|dynamic|Ölçümler|dynamic|
|bilgileri|dynamic|Ayrıntılar|dynamic|
|handledAt|string|HandledAt|string|
|iKey|string|IKey|string|
|ınnermostassembly|string|Innermostassembly|string|
|ınnermostmessage|string|Innermostmessage|string|
|ınnermostmethod|string|Innermostmethod|string|
|ınnermosttype|string|Innermosttype|string|
|ItemCount|int|ItemCount|int|
|ID|string|\_ID|string|
|ItemType|string|Tür|string|
|message|string|İleti|string|
|method|string|Yöntem|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|Operationparentıd|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|outerAssembly|string|OuterAssembly|string|
|outerMessage|string|OuterMessage|string|
|outerMethod|string|OuterMethod|string|
|outerType|string|OuterType|string|
|problemId|string|ProblemId|string|
|SDK sürümü|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|Severıtylevel|int|Severıtylevel|int|
|timestamp|datetime|TimeGenerated|datetime|
|tür|string|Türü|string|
|user_AccountId|string|Useraccountıd|string|
|user_AuthenticatedId|string|Userauthenticatedıd|string|
|user_Id|string|UserId|string|

### <a name="apptraces"></a>Appizlemeler

Eski tablo: izlemeler

|ApplicationInsights|Tür|Günlüğe kaydetme Analizi|Tür|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|Clientstateoril|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Approtaınstance|string|
|cloud_RoleName|string|Approtaname|string|
|customDimensions|dynamic|Özellikler|dynamic|
|Customölçüler|dynamic|Ölçümler|dynamic|
|iKey|string|IKey|string|
|ItemCount|int|ItemCount|int|
|ID|string|\_ID|string|
|ItemType|string|Tür|string|
|message|string|İleti|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|Operationparentıd|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|SDK sürümü|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|Severıtylevel|int|Severıtylevel|int|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|Useraccountıd|string|
|user_AuthenticatedId|string|Userauthenticatedıd|string|
|user_Id|string|UserId|string|

## <a name="next-steps"></a>Sonraki adımlar

* [Ölçümleri keşfetme](../essentials/metrics-charts.md)
* [Analytics sorguları yazma](../logs/log-query-overview.md)

