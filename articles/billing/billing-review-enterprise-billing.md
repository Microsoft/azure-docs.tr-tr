---
title: REST API ile Azure kurumsal kayıt faturalama verilerini gözden geçirme | Microsoft Docs
description: Kurumsal kayıt faturalama bilgilerini gözden geçirmek için Azure REST API’lerinin nasıl kullanılacağını öğrenin.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 47668169841cc2b180995808349487cefed02391
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754297"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>REST API’leri kullanarak kurumsal kayıt faturalamasını gözden geçirme

Azure Raporlama API’leri, Azure maliyetlerinizi gözden geçirmenize ve yönetmenize yardımcı olur.

Bu makalede, Azure REST API’leri kullanarak ödeme hesapları, bölüm veya kurumsal anlaşma (EA) kayıt hesaplarıyla ilişkili faturalama bilgilerini almayı öğrenirsiniz. 

## <a name="individual-account-billing"></a>Bireysel hesap faturalama

Bir bölümdeki hesapların kullanım ayrıntılarını almak için:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{billingAccountId}` parametresi gereklidir ve hesabın kimliğini içermelidir.

Aşağıdaki üstbilgiler gereklidir: 

|İstek üst bilgisi|Açıklama|  
|--------------------|-----------------|  
|*Content-Type:*|Gereklidir. `application/json` olarak ayarlayın.|  
|*Yetkilendirme:*|Gereklidir. Geçerli bir `Bearer` [API anahtarına](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) ayarlayın. |  

Bu örnekte, geçerli faturalama dönemine ilişkin ayrıntıları döndüren zaman uyumlu bir çağrı gösterilmektedir. Performans nedeniyle zaman uyumlu çağrılar geçen aya ilişkin bilgileri döndürür.  Ayrıca 36 aylık verileri döndürmek için de [Zaman uyumsuz API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)’yi çağırabilirsiniz.


## <a name="response"></a>Yanıt  

Başarılı yanıt için, hesabın ayrıntılı maliyetlerinin listesini içeren durum kodu 200 (Tamam) döndürülür.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Bu örnek kısaltılmıştır; her bir yanıt alanının tam açıklaması ve hata işleme için bkz. [Ödeme hesabı için kullanım ayrıntısını alma](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist-legacy).

## <a name="department-billing"></a>Bölüm faturalaması 

Bir bölümdeki tüm hesaplar için toplanan kullanım ayrıntılarını alın. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{departmentId}` parametresi gereklidir ve kayıt hesabında bölümün kimliğini içermelidir.

Aşağıdaki üstbilgiler gereklidir: 

|İstek üst bilgisi|Açıklama|  
|--------------------|-----------------|  
|*Content-Type:*|Gereklidir. `application/json` olarak ayarlayın.|  
|*Yetkilendirme:*|Gereklidir. Geçerli bir `Bearer` [API anahtarına](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) ayarlayın. |  

Bu örnekte, geçerli faturalama dönemine ilişkin ayrıntıları döndüren zaman uyumlu bir çağrı gösterilmektedir. Performans nedeniyle zaman uyumlu çağrılar geçen aya ilişkin bilgileri döndürür.  Ayrıca 36 aylık verileri döndürmek için de [Zaman uyumsuz API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)’yi çağırabilirsiniz.

### <a name="response"></a>Yanıt  

Başarılı bir yanıt için, ayrıntılı kullanım bilgileri listesini ve bölüm için belirtilen faturalama dönemi ve fatura kimliği maliyetlerini içeren durum kodu 200 (Tamam) döndürülür.


Aşağıdaki örnekte `1234` bölümü için REST API çıktısı gösterilmektedir.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Bu örnek kısaltılmıştır; her bir yanıt alanının tam açıklaması ve hata işleme için bkz. [Bölüm için kullanım ayrıntısını alma](/rest/api/consumption/usagedetails/list#departmentusagedetailslist-legacy).

## <a name="enrollment-account-billing"></a>Kayıt hesabı faturalaması

Kayıt hesabı için toplanan kullanım ayrıntılarını alın.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{enrollmentAccountId}` parametresi gereklidir ve kayıt hesabının kimliğini içermelidir.

Aşağıdaki üstbilgiler gereklidir: 

|İstek üst bilgisi|Açıklama|  
|--------------------|-----------------|  
|*Content-Type:*|Gereklidir. `application/json` olarak ayarlayın.|  
|*Yetkilendirme:*|Gereklidir. Geçerli bir `Bearer` [API anahtarına](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) ayarlayın. |  

Bu örnekte, geçerli faturalama dönemine ilişkin ayrıntıları döndüren zaman uyumlu bir çağrı gösterilmektedir. Performans nedeniyle zaman uyumlu çağrılar geçen aya ilişkin bilgileri döndürür.  Ayrıca 36 aylık verileri döndürmek için de [Zaman uyumsuz API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)’yi çağırabilirsiniz.

### <a name="response"></a>Yanıt  

Başarılı bir yanıt için, ayrıntılı kullanım bilgileri listesini ve bölüm için belirtilen faturalama dönemi ve fatura kimliği maliyetlerini içeren durum kodu 200 (Tamam) döndürülür.

Aşağıdaki örnekte, `1234` kurumsal kaydı için REST API çıktısı gösterilmektedir.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

Bu örnek kısaltılmıştır; her bir yanıt alanının tam açıklaması ve hata işleme için bkz. [Kayıt hesabı için kullanım ayrıntısını alma](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist-legacy).

## <a name="next-steps"></a>Sonraki adımlar 
- [Kurumsal raporlamaya genel bakış](https://docs.microsoft.com/azure/billing/billing-enterprise-api) bölümünü gözden geçirin
- [Kurumsal Faturalama REST API](https://docs.microsoft.com/rest/api/billing/)’yi araştırın   
- [Azure REST API’yi kullanmaya başlayın](https://docs.microsoft.com/rest/api/azure/)   
