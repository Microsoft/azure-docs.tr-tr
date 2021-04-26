---
title: Ticari Market Analytics verilerine erişmek için ilk API çağrısını yapın
description: Ticari Market Analytics verilerine erişmek için API kullanmayı öğrenme örnekleri.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 9e5fbdfca80d19f026a014a89ffbf137bacb521c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639575"
---
# <a name="make-your-first-api-call-to-access-commercial-marketplace-analytics-data"></a>Ticari Market Analytics verilerine erişmek için ilk API çağrısını yapın

Ticari Market Analytics verilerine erişim için API 'lerin bir listesi için bkz. [ticari Market Analytics verilerine erişim Için API 'ler](analytics-available-apis.md). İlk API çağrısını yapmadan önce, ticari Market Analytics verilerine programlı bir şekilde erişmek için [önkoşulları](analytics-prerequisites.md) karşıladığınızdan emin olun.

## <a name="token-generation"></a>Belirteç oluşturma

Yöntemlerden herhangi birini çağırmadan önce, önce bir Azure Active Directory (Azure AD) erişim belirteci edinmeniz gerekir. Azure AD erişim belirtecini API 'deki her yöntemin yetkilendirme üstbilgisine geçirmeniz gerekir. Erişim belirteci aldıktan sonra, süresi dolmadan önce onu kullanmanız 60 dakika sürer. Belirtecin süresi dolduktan sonra belirteci yenileyebilir ve API 'ye yönelik daha fazla çağrı için kullanmaya devam edebilirsiniz.

Belirteç oluşturmak için aşağıdaki örnek bir isteğe başvurun. Belirteci oluşturmak için gereken üç değer `clientId` , ve ' dir `clientSecret` `tenantId` . `resource`Parametresi olarak ayarlanmalıdır `https://graph.windows.net` .

***Istek örneği***:

```json
curl --location --request POST 'https://login.microsoftonline.com/{TenantId}/oauth2/token' \
--header 'return-client-request-id: true' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'resource=https://graph.windows.net' \
--data-urlencode 'client_id={client_id}' \
--data-urlencode 'client_secret={client_secret}' \
--data-urlencode 'grant_type=client_credentials'
```

***Yanıt örneği***:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1612794445",
    "not_before": "1612790545",
    "resource": "https://graph.windows.net",
    "access_token": {Token}
}
```

Uygulamanız için bir Azure AD belirteci alma hakkında daha fazla bilgi için bkz. [Store Services kullanarak Analytics verilerine erişme](/windows/uwp/monetize/access-analytics-data-using-windows-store-services#step-2-obtain-an-azure-ad-access-token).

## <a name="programmatic-api-call"></a>Programlı API çağrısı

Önceki bölümde açıklandığı gibi Azure AD belirtecini elde ettikten sonra, ilk programlı erişim raporunuzu oluşturmak için aşağıdaki adımları izleyin.

Veriler aşağıdaki veri kümelerinden indirilebilir (datasetName):

- ISVCustomer
- ISVMarketplaceInsights
- ISVUsage
- ISVOrder

Aşağıdaki bölümlerde, ISVOrder veri kümesinden programlı olarak nasıl erişebiliriz örnekleri görülecektir `OrderId` .

### <a name="step-1-make-a-rest-call-using-the-get-datasets-api"></a>1. Adım: Get DataSet API 'sini kullanarak REST çağrısı yapma

API yanıtı, raporu indirebileceğiniz veri kümesi adını sağlar. Belirli bir veri kümesi için, API yanıtı özel rapor şablonunuz için kullanılabilen seçilebilir sütunların listesini de sağlar. Ayrıca, sütunların adlarını almak için aşağıdaki tablolara başvurabilirsiniz:

- [Sipariş Ayrıntıları tablosu](orders-dashboard.md#orders-details-table)
- [Kullanım Ayrıntıları tablosu](usage-dashboard.md#usage-details-table)
- [Müşteri Ayrıntıları tablosu](customer-dashboard.md#customer-details-table)
- [Market öngörüleri Ayrıntıları tablosu](insights-dashboard.md#marketplace-insights-details-table)

***İstek örneği***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset ' \ 
--header 'Authorization: Bearer <AzureADToken>'
```

***Yanıt örneği***:

```json
{
    "value": [
        {
            "datasetName": "ISVOrder",
            "selectableColumns": [
                "MarketplaceSubscriptionId",
                "MonthStartDate",
                "OfferType",
                "AzureLicenseType",
                "Sku",
                "CustomerCountry",
                "IsPreviewSKU",
                "OrderId",
                "OrderQuantity",
                "CloudInstanceName",
                "IsNewCustomer",
                "OrderStatus",
                "OrderCancelDate",
                "CustomerCompanyName",
                "CustomerName",
                "OrderPurchaseDate",
                "OfferName",
                "TrialEndDate",
                "CustomerId",
                "BillingAccountId"
            ],
            "availableMetrics": [],
            "availableDateRanges": [
                "LAST_MONTH",
                "LAST_3_MONTHS",
                "LAST_6_MONTHS",
                "LIFETIME"
            ]
        },
    ],
    "totalCount": 1,
    "message": "Dataset fetched successfully",
    "statusCode": 200
}
```

### <a name="step-2-create-the-custom-query"></a>2. Adım: özel sorgu oluşturma

Bu adımda, istediğimiz rapor için özel bir sorgu oluşturmak üzere siparişler raporundan sipariş KIMLIĞINI kullanacağız. `timespan`Sorguda belirtilmemişse varsayılan değer altı ay olur.

***İstek örneği***:

```json
curl 
--location 
--request POST ' https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries' \ 
--header ' Authorization: Bearer <AzureAD_Token>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
            '{ 
                "Query": "SELECT OrderId from ISVOrder", 
                "Name": "ISVOrderQuery1", 
                "Description": "Get a list of all Order IDs" 
             }'
```

***Yanıt örneği***:

```json
{
    "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": "ISVOrderQuery1",
            "description": "Get a list of all Order IDs”,
            "query": "SELECT OrderId from ISVOrder",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34",
            "modifiedTime": null
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

Sorgunun başarıyla yürütülmesi sırasında, `queryId` raporu oluşturmak için kullanılması gereken bir oluşturulur.

### <a name="step-3-execute-test-query-api"></a>3. Adım: test sorgu API 'sini yürütme

Bu adımda, oluşturulan sorgunun ilk 100 satırını almak için test sorgu API 'sini kullanacağız.

***İstek örneği***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery=SELECT%20OrderId%20from%20ISVOrder' \ 
--header ' Authorization: Bearer <AzureADToken>'
```

***Yanıt örneği***:

```json
{
    "value": [
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2ba8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bb8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bc8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bd8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2be8"
        },
               .
               .
               .

        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf0"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf1"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf2"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf3"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf4"
        }
    ],
    "totalCount": 100,
    "message": null,
    "statusCode": 200
}
```

### <a name="step-4-create-the-report"></a>4. Adım: raporu oluşturma

Bu adımda, raporu oluşturmak için önceden oluşturulan öğesini kullanacağız `QueryId` .

***İstek örneği***:

```json
curl 
--location 
--request POST 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport' \ 
--header ' Authorization: Bearer <AzureADToken>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
                 '{
                   “ReportName": "ISVReport1",
                   "Description": "Report for getting list of Order Ids",
                   "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
                   "StartTime": "2021-01-06T19:00:00Z”,
                   "RecurrenceInterval": 48,
                   "RecurrenceCount": 20,
                    "Format": "csv"
                  }'
```

<br>

_**Tablo 1: Bu istek örneğinde kullanılan parametrelerin açıklaması**_

| Parametre | Açıklama |
| ------------ | ------------- |
| `Description` | Oluşturulan raporun kısa bir açıklamasını girin. |
| `QueryId` | Bu, `queryId` sorgu 2. Adım: özel sorgu oluşturma bölümünde oluşturulduğunda oluşturulmuştur. |
| `StartTime` | Raporun ilk yürütülmesi sırasında başlangıç zamanı. |
| `RecurrenceInterval` | Rapor oluşturma sırasında yinelenme aralığı belirtildi. |
| `RecurrenceCount` | Rapor oluşturma sırasında yinelenme sayısı belirtildi. |
| `Format` | CSV ve TSV dosya biçimleri desteklenir. |
|||

***Yanıt örneği***:

```json
{
    "value": [
        {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVReport1",
            "description": "Report for getting list of Order Ids",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT OrderId from ISVOrder",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv"
        }
    ],
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200
}
```

Başarılı bir yürütme sırasında, `reportId` raporun karşıdan yüklenmesini zamanlamak için kullanılması gereken bir oluşturulur.

### <a name="step-5-execute-report-executions-api"></a>5. Adım: rapor yürütmeleri API 'sini yürütme

Raporun güvenli konumunu (URL) almak için artık rapor yürütmeleri API 'sini yürüteceğiz.

***İstek örneği***:

```json
Curl
--location
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/72fa95ab-35f5-4d44-a1ee-503abbc88003' \
--header ' Authorization: Bearer <AzureADToken>' \
```

***Yanıt örneği***:

```json
{
    "value": [
        {
            "executionId": "1f18b53b-df30-4d98-85ee-e6c7e687aeed",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Swagger API URL 'si](https://swagger.io/docs/specification/api-host-and-base-path/) aracılığıyla API 'leri deneyebilirsiniz
- [Programlı erişim paradigması](analytics-programmatic-access.md)
