---
title: Programlı erişim paradigması
description: Programlı analizler için API çağrı deseninin üst düzey akışını anlayın. Microsoft Commercial Market 'teki analiz raporlarına erişim API 'Leri de ele alınmıştır.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8e0b94a46e96dd8ba16040e16b421520eb67de19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584228"
---
# <a name="programmatic-access-paradigm"></a>Programlı erişim paradigması

Bu diyagram, yeni bir rapor şablonu oluşturmak, özel raporu zamanlamak ve hata verilerini almak için kullanılan API çağrı modelini gösterir.

[ ![ Yeni bir rapor şablonu oluşturmak, özel raporu zamanlamak ve hata verilerini almak IÇIN kullanılan API çağrı modelini gösterir.](./media/analytics-programmatic-access/analytics-high-level-flow.png)](./media/analytics-programmatic-access/analytics-high-level-flow.png#lightbox) 
 ***Şekil 1: API çağrı deseninin üst düzey akışı***

Bu liste Şekil 1 hakkında daha fazla ayrıntı sağlar.

1. Istemci uygulaması, [rapor sorgusu oluştur API](#create-report-query-api)'sini çağırarak özel rapor şemasını/şablonunu tanımlayabilir. Alternatif olarak, `QueryId` [Sistem sorguları listesinden](analytics-system-queries.md)bir rapor şablonu () kullanabilirsiniz.
1. Başarı durumunda, rapor şablonu oluşturma API 'SI döndürür `QueryId` .
1. İstemci uygulaması daha sonra [](#create-report-api) `QueryID` rapor başlangıç tarihi, yineleme aralığı, yinelenme ve Isteğe bağlı bir geri çağırma URI 'si Ile birlikte kullanarak rapor oluşturma API 'sini çağırır.
1. Başarı durumunda, [rapor oluşturma API 'si](#create-report-api) döndürür `ReportID` .
1. İstemci uygulaması, rapor verileri indirilmeye uygun hale geldiğinde geri çağırma URI 'sinden bildirim alır.
1. İstemci uygulaması daha sonra raporun durumunu ve tarih aralığını sorgulamak için [rapor yürütmelerini al API](#get-report-executions-api) 'sini kullanır `Report ID` .
1. Başarı durumunda, rapor indirme bağlantısı döndürülür ve uygulama verilerin indirilmesini başlatabilir.

## <a name="report-query-language-specification"></a>Rapor sorgusu dil belirtimi

Raporları oluşturmak için kullanabileceğiniz [Sistem sorguları](analytics-system-queries.md) sağladığımızda, iş gereksinimlerinize göre kendi sorgularınızı de oluşturabilirsiniz. Özel sorgular hakkında daha fazla bilgi için bkz. [özel sorgu belirtimi](analytics-custom-query-specification.md).

## <a name="create-report-query-api"></a>Rapor sorgu API 'SI oluştur

Bu API, hangi sütunların ve ölçümlerin verilmesi gereken veri kümesini tanımlayan özel sorgular oluşturmaya yardımcı olur. API, iş gereksinimlerinize göre yeni bir raporlama şablonu oluşturma esnekliğini sağlar.

Sağladığımız [sistem sorgularını](analytics-system-queries.md) da kullanabilirsiniz. Özel rapor şablonları gerekli olmadığında, sağladığımız sistem sorgularının [QueryIds değerlerini](analytics-system-queries.md) kullanarak [rapor oluşturma API](#create-report-api) 'sini doğrudan çağırabilirsiniz.

Aşağıdaki örnek, son ayın [ISVUsage](analytics-make-your-first-api-call.md#programmatic-api-call) VERI kümesinden _ücretli SKU 'Lar için normalleştirilmiş kullanım ve tahmini mali ücretler_ almak üzere nasıl özel bir sorgu oluşturulacağını gösterir.

*İstek sözdizimi*

| Yöntem | İstek URI'si |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries` |
|||

*İstek üst bilgisi*

| Üst bilgi | Tür | Description |
| ------------- | ------------- | ------------- |
| Yetkilendirme | string | Gereklidir. Azure Active Directory (Azure AD) erişim belirteci. Biçim `Bearer <token>` . |
| İçerik Türü | `string` | `application/JSON` |
||||

*Yol parametresi*

Yok

*Sorgu parametresi*

Yok

*İstek yükü örneği*

```json
{
    "Name": "ISVUsageQuery",
    "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
    "Query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"
}
```

*Sözlük*

Bu tablo, istek yükünde öğelerin temel tanımlarını sağlar.

| Parametre | Gerekli | Açıklama | İzin verilen değerler |
| ------------ | ------------- | ------------- | ------------- |
| `Name` | Yes | Sorgunun kolay adı | dize |
| `Description` | No | Sorgunun döndürdüğü açıklama | string |
| `Query` | Yes | Rapor sorgu dizesi | Veri türü: dize<br>İş gereksinimini temel alan [özel sorgu](analytics-sample-queries.md) |
|||||

> [!NOTE]
> Özel sorgu örnekleri için bkz. [örnek sorgu örnekleri](analytics-sample-queries.md).

*Örnek yanıt*

Yanıt yükü aşağıdaki şekilde yapılandırılır:

Yanıt kodları: 200, 400, 401, 403, 500

Yanıt yükü örneği:

```json
{
  "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": " ISVUsageQuery",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs”,
            "query": " SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34Z"
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

*Sözlük*

Bu tablo, yanıttaki öğelerin anahtar tanımlarını sağlar.

| Parametre | Açıklama |
| ------------ | ------------- |
| `QueryId` | Oluşturduğunuz sorgunun evrensel benzersiz tanımlayıcısı (UUID) |
| `Name` | İstek yükünde sorguya kolay ad verildi |
| `Description` | Sorgunun oluşturulması sırasında verilen açıklama |
| `Query` | Sorgu oluşturma sırasında rapor sorgusu giriş olarak geçildi |
| `Type` | Ayarla `userDefined` |
| `User` | Sorguyu oluşturmak için kullanılan Kullanıcı KIMLIĞI |
| `CreatedTime` | Sorgunun oluşturulduğu UTC saati: yyyy-MM-ddTHH: mm: ssZ |
| `TotalCount` | Değer dizisindeki veri kümesi sayısı |
| `StatusCode` | Sonuç Kodu<br>Olası değerler 200, 400, 401, 403, 500 |
| `message` | API 'nin yürütülmesindeki durum iletisi |
|||

## <a name="create-report-api"></a>Rapor API 'SI oluştur

Özel rapor şablonu oluşturma ve `QueryID` [rapor sorgusu yanıtı oluşturma](#create-report-query-api) ' nın bir parçası olarak alma hakkında, bu API, düzenli aralıklarla yürütülecek bir sorgu zamanlamak için çağrılabilir. Raporun teslim edileceği sıklığı ve zamanlamayı ayarlayabilirsiniz. Sağladığımız sistem sorguları için, rapor oluşturma API 'SI de [QueryId](analytics-sample-queries.md)ile çağrılabilir.

*İstek sözdizimi*

| Yöntem | İstek URI'si |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport` |
|||

*İstek üst bilgisi*

| Üst bilgi | Tür | Description |
| ------ | ---- | ----------- |
| Yetkilendirme | string | Gereklidir. Azure Active Directory (Azure AD) erişim belirteci. Biçim `Bearer <token>` . |
| İçerik Türü | string | `application/JSON` |
||||

*Yol parametresi*

Yok

*Sorgu parametresi*

Yok

*İstek yükü örneği*

```json
{
  "ReportName": "ISVUsageReport",
  "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
  "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c ",
  "StartTime": "2021-01-06T19:00:00Z ",
  "RecurrenceInterval": 48,
  "RecurrenceCount": 20,
  "Format": "csv",
  "CallbackUrl": "https://<SampleCallbackUrl>"
}
```

*Sözlük*

Bu tablo, istek yükünde öğelerin temel tanımlarını sağlar.

| Parametre | Gerekli | Açıklama | İzin verilen değerler |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Yes | Rapora atanan ad | dize |
| `Description` | No | Oluşturulan raporun açıklaması | string |
| `QueryId` | Yes | Rapor sorgu KIMLIĞI | string |
| `StartTime` | Yes | Rapor oluşturmanın başlayacağı UTC zaman damgası.<br>Biçim şu şekilde olmalıdır: yyyy-MM-ddTHH: mm: ssZ | string |
| `RecurrenceInterval` | Yes | Raporun oluşturulması gereken saat sıklığı.<br>En küçük değer 4, en büyük değer 90 ' dir. | tamsayı |
| `RecurrenceCount` | No | Oluşturulacak rapor sayısı. | tamsayı |
| `Format` | No | İçe aktarılmış dosyanın dosya biçimi.<br>Varsayılan biçim. CSV. | CSV/TSV |
| `CallbackUrl` | No | İsteğe bağlı olarak, geri çağırma hedefi olarak yapılandırılabilen, genel olarak erişilebilen URL. | Dize (http URL 'SI) |
| `ExecuteNow` | No | Bu parametre, yalnızca bir kez yürütülecek bir rapor oluşturmak için kullanılmalıdır. `StartTime`, `RecurrenceInterval` , ve `RecurrenceCount` olarak ayarlandıysa yok sayılır `true` . Rapor, anında zaman uyumsuz bir biçimde yürütülür. | doğru/yanlış |
| `QueryStartTime` | No | İsteğe bağlı olarak, verileri ayıklayan sorgunun başlangıç saatini belirtir. Bu parametre yalnızca, olarak ayarlanmış bir kerelik yürütme raporu için geçerlidir `ExecuteNow` `true` . Biçim yyyy-MM-ddTHH: mm: ssZ olmalıdır | Dize olarak zaman damgası |
| `QueryEndTime` | No | İsteğe bağlı olarak, verileri ayıklayan sorgunun bitiş saatini belirtir. Bu parametre yalnızca, olarak ayarlanmış bir kerelik yürütme raporu için geçerlidir `ExecuteNow` `true` . Biçim yyyy-MM-ddTHH: mm: ssZ olmalıdır | Dize olarak zaman damgası |
|||||

*Örnek yanıt*

Yanıt yükü aşağıdaki şekilde yapılandırılır:

Yanıt kodu: 200, 400, 401, 403, 404, 500

Yanıt yükü:

```json
{
  "Value": [
    {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVUsageReport",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": "https://<SampleCallbackUrl>",
            "format": "csv"    
    }
  ],
  "TotalCount": 1,
  "Message": "Report created successfully",
  "StatusCode": 200
}
```

*Sözlük*

Bu tablo, yanıttaki öğelerin anahtar tanımlarını sağlar.

| Parametre | Açıklama |
| ------------ | ------------- |
| `ReportId` | Oluşturduğunuz raporun evrensel benzersiz tanımlayıcısı (UUID) |
| `ReportName` | İstek yükünde rapora verilen ad |
| `Description` | Raporun oluşturulması sırasında verilen açıklama |
| `QueryId` | Raporu oluşturduğunuz sırada geçirilen sorgu KIMLIĞI |
| `Query` | Bu rapor için yürütülecek sorgu metni |
| `User` | Raporu oluşturmak için kullanılan Kullanıcı KIMLIĞI |
| `CreatedTime` | Raporun şu biçimde oluşturulduğu UTC saati: yyyy-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | Raporun bu biçimde en son değiştirildiği UTC saati: yyyy-MM-ddTHH: mm: ssZ |
| `StartTime` | Rapor yürütmenin bu biçimde başlayacağı UTC saati: yyyy-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Rapor yürütmenin durumu. Olası değerler **duraklatıldı**, **etkin** ve **etkin** değildir. |
| `RecurrenceInterval` | Rapor oluşturma sırasında belirtilen yinelenme aralığı |
| `RecurrenceCount` | Rapor oluşturma sırasında belirtilen yinelenme sayısı |
| `CallbackUrl` | İstekte belirtilen geri çağırma URL 'SI |
| `Format` | Rapor dosyalarının biçimi. Olası değerler CSV veya TSV ' dir. |
| `TotalCount` | Değer dizisindeki veri kümesi sayısı |
| `StatusCode` | Sonuç Kodu<br>Olası değerler 200, 400, 401, 403, 500 |
| `message` | API 'nin yürütülmesindeki durum iletisi |
|||

## <a name="get-report-executions-api"></a>Rapor yürütmeleri API 'sini al

Rapor `ReportId` [oluşturma API](#create-report-api)'sinden alınan öğesini kullanarak rapor yürütmenin durumunu sorgulamak için bu yöntemi kullanabilirsiniz. Rapor indirmeye hazırsanız, yöntemi rapor indirme bağlantısını döndürür. Aksi takdirde, yöntemi durumu döndürür. Belirli bir rapor için gerçekleşen tüm yürütmeleri almak için bu API 'yi de kullanabilirsiniz.

> [!IMPORTANT]
> Bu API, ve için ayarlanmış varsayılan sorgu parametrelerine sahiptir `executionStatus=Completed`  `getLatestExecution=true` . Bu nedenle, raporun ilk başarılı yürütülmesi için önce API çağrılması 404 döndürür. Bekleyen yürütmeler, ayarıyla elde edilebilir `executionStatus=Pending` .

*İstek sözdizimi*

| Yöntem | İstek URI'si |
| ------------ | ------------- |
| Al | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/{reportId}?executionId={executionId}&executionStatus={executionStatus}&getLatestExecution={getLatestExecution}` |
|||

*İstek üst bilgisi*

| Üst bilgi | Tür | Description |
| ------ | ------ | ------ |
| Yetkilendirme | string | Gereklidir. Azure Active Directory (Azure AD) erişim belirteci. Biçim `Bearer <token>` . |
| İçerik türü | string | `application/json` |
||||

*Yol parametresi*

Yok

*Sorgu parametresi*

| Parametre adı | Gerekli | Tür | Açıklama |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Evet | string | Yalnızca bu bağımsız değişkende verilen raporların yürütme ayrıntılarını almak için filtreleyin `reportId` . Birden çok `reportIds` noktalı virgül ";" ile ayrılarak belirtilebilir. |
| `executionId` | No | string | Yalnızca bu bağımsız değişkende verilen raporların ayrıntılarını almak için filtreleyin `executionId` . Birden çok `executionIds` noktalı virgül ";" ile ayrılarak belirtilebilir. |
| `executionStatus` | No | dize/Enum | Yalnızca bu bağımsız değişkende verilen raporların ayrıntılarını almak için filtreleyin `executionStatus` .<br>Geçerli değerler şunlardır: `Pending` , `Running` , `Paused` , ve `Completed` <br>`Completed` varsayılan değerdir. Birden çok durum noktalı virgül ";" ile ayrılarak belirtilebilir. |
| `getLatestExecution` | No | boolean | API, en son rapor yürütmesinin ayrıntılarını döndürür.<br>Varsayılan olarak, bu parametre olarak ayarlanır `true` . Bu parametrenin değerini olarak geçirmeye seçerseniz `false` , API son 90 gün yürütme örneğini döndürür. |
|||||

*İstek yükü*

Yok

*Örnek yanıt*

Yanıt yükü aşağıdaki şekilde yapılandırılır:

Yanıt kodları: 200, 400, 401, 403, 404, 500

Yanıt yükü örneği:

```json
{
    "value": [
        {
            "executionId": "a0bd78ad-1a05-40fa-8847-8968b718d00f",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 4,
            "recurrenceCount": 10,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Completed",
            "reportAccessSecureLink": "https://<path to report for download>",
            "reportExpiryTime": null,
            "reportGeneratedTime": "2021-01-13T14:40:46Z"
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

Rapor yürütme tamamlandıktan sonra, yürütme durumu `Completed` gösterilir. İçindeki URL 'YI seçerek raporu indirebilirsiniz `reportAccessSecureLink` .

*Sözlük*

Yanıttaki öğelerin anahtar tanımları.

| Parametre | Açıklama |
| ------------ | ------------- |
| `ExecutionId` | Yürütme örneğinin evrensel benzersiz tanımlayıcısı (UUID) |
| `ReportId` | Yürütme örneğiyle ilişkili rapor KIMLIĞI |
| `RecurrenceInterval` | Rapor oluşturma sırasında belirtilen yinelenme aralığı |
| `RecurrenceCount` | Rapor oluşturma sırasında belirtilen yinelenme sayısı |
| `CallbackUrl` | Yürütme örneğiyle ilişkili geri çağırma URL 'SI |
| `Format` | Yürütmenin sonunda oluşturulan dosyanın biçimi |
| `ExecutionStatus` | Rapor yürütme örneğinin durumu.<br>Geçerli değerler şunlardır: `Pending` , `Running` , `Paused` , ve `Completed` |
| `ReportAccessSecureLink` | Rapora güvenli erişilebilen bağlantı |
| `ReportExpiryTime` | Rapor bağlantısının süresinin dolmasına geçen UTC saati: yyyy-MM-ddTHH: mm: ssZ |
| `ReportGeneratedTime` | Raporun şu biçimde oluşturulduğu UTC saati: yyyy-MM-ddTHH: mm: ssZ |
| `TotalCount` | Değer dizisindeki veri kümesi sayısı |
| `StatusCode` | Sonuç Kodu <br>Olası değerler 200, 400, 401, 403, 404 ve 500 |
| `message` | API 'nin yürütülmesindeki durum iletisi |
|||

## <a name="next-steps"></a>Sonraki adımlar
- [Swagger API URL 'si](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html) aracılığıyla API 'leri deneyebilirsiniz
- Analiz verilerine programlı erişim ile çalışmaya başlama
