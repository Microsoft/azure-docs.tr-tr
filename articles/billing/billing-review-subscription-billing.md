---
title: REST API ile Azure abonelik faturalama verilerini gözden geçirme | Microsoft Docs
description: Abonelik faturalama ayrıntılarını gözden geçirmek için Azure REST API’lerinin nasıl kullanılacağını öğrenin.
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
ms.openlocfilehash: 05ce5fd560168c1f60b7c559539f4a368ba83ca8
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754292"
---
# <a name="review-subscription-billing-using-rest-apis"></a>REST API’leri kullanarak abonelik faturalamasını gözden geçirme

Azure Raporlama API’leri, Azure maliyetlerinizi gözden geçirmenize ve yönetmenize yardımcı olur.

Filtreler, sonuçları gereksinimlerinize uyacak şekilde özelleştirmenize yardımcı olur.

Burada, REST API kullanarak belirli bir tarih aralığına ait abonelik faturalama ayrıntılarını döndürmeyi öğrenirsiniz.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>İsteği oluşturma

`{subscriptionID}` parametresi gereklidir ve hedef aboneliği belirtir.

`{billingPeriod}` parametresi gereklidir ve geçerli bir [faturalama dönemini](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) belirtir.

`${startDate}` ve `${endDate}` parametresi, bu örnek için gereklidir, ancak uç nokta için isteğe bağlıdır. Tarih aralığını YYYY-AA-GG biçiminde dize olarak belirtir (örnekler: `'20180501'` ve `'20180615'`).

Aşağıdaki üstbilgiler gereklidir:

|İstek üst bilgisi|Açıklama|
|--------------------|-----------------|
|*Content-Type:*|Gereklidir. `application/json` olarak ayarlayın.|
|*Yetkilendirme:*|Gereklidir. Geçerli bir `Bearer` [erişim belirtecine](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) ayarlayın. |

## <a name="response"></a>Yanıt

Başarılı yanıt için, hesabınızın ayrıntılı maliyetlerinin listesini içeren durum kodu 200 (Tamam) döndürülür.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

**Değer** içindeki her bir öğe, bir hizmet kullanımıyla ilgili ayrıntıları temsil eder:

|Yanıt özelliği|Açıklama|
|----------------|----------|
|**subscriptionGuid** | Aboneliğin genel olarak benzersiz kimliği. |
|**startDate** | Kullanımın başlangıç tarihi. |
|**endDate** | Kullanımın bitiş tarihi. |
|**useageQuantity** | Kullanılan miktar. |
|**billableQuantity** | Fiili olarak faturalanan miktar. |
|**pretaxCost** | Geçerli vergiler uygulanmadan önce faturalanan maliyet. |
|**meterDetails** | Kullanımla ilgili ayrıntılı bilgiler. |
|**nextLink**| Bu ayarlandığında, ayrıntılar için sonraki “sayfanın” URL’sini belirtir. Sayfa son sayfa olduğunda boştur. |

Bu örnek kısaltılmıştır; her bir yanıt alanının tam açıklaması için bkz. [Kullanım ayrıntılarını listeleme](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod-legacy).

Diğer durum kodları, hata koşullarını belirtir. Bu durumlarda yanıt nesnesi, isteğin neden başarısız olduğunu açıklar.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar
- [Kurumsal raporlamaya genel bakış](https://docs.microsoft.com/azure/billing/billing-enterprise-api) bölümünü gözden geçirin
- [Kurumsal Faturalama REST API](https://docs.microsoft.com/rest/api/billing/)’yi araştırın
- [Azure REST API’yi kullanmaya başlayın](https://docs.microsoft.com/rest/api/azure/)
