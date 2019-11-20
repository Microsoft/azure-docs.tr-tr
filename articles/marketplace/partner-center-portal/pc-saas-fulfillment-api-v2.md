---
title: SaaS karşılama API v2 | Azure Marketi
description: Bu makalede, ilişkili karşılama v2 API 'Leri kullanılarak AppSource ve Azure Marketi üzerinde bir SaaS teklifinin nasıl oluşturulacağı ve yönetileceği açıklanmaktadır.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: evansma
ms.openlocfilehash: 4c73a59352422626ec3c6012607009995479d0cc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816600"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS karşılama API 'Leri, sürüm 2 

Bu makalede, iş ortaklarının, SaaS uygulamalarını AppSource marketi ve Azure Marketi 'nde satmasını sağlayan API 'Ler ayrıntılı olarak anlatılmaktadır. Bu API 'Ler, AppSource ve Azure Marketi üzerinde transactable SaaS teklifleri için bir gereksinimdir.

## <a name="managing-the-saas-subscription-life-cycle"></a>SaaS Abonelik yaşam döngüsünü yönetme

Azure SaaS, SaaS aboneliği satın alma 'nın tüm yaşam döngüsünü yönetir. Gerçek yerine getirme, planlardaki değişiklikler ve iş ortağı ile aboneliğin silinmesi için bir mekanizma olarak yerine getirme API 'Lerini kullanır. Müşterinin faturanız, Microsoft 'un koruduğu SaaS aboneliğinin durumuna bağlıdır. Aşağıdaki diyagramda durumları ve durumları arasındaki değişiklikleri sağlayan işlemler gösterilmektedir.

![SaaS aboneliği yaşam döngüsü durumları](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS aboneliğinin durumları

Aşağıdaki tabloda her biri için bir açıklama ve sıra diyagramı (varsa) dahil olmak üzere SaaS aboneliğinin sağlama durumları listelenmektedir. 

#### <a name="provisioning"></a>Sağlama

Bir müşteri satın alma işlemini başlattığında, iş ortağı bu bilgileri bir URL parametresi kullanan müşteri etkileşimli Web sayfasındaki bir yetkilendirme kodunda alır. Bir örnek `https://contoso.com/signup?token=..`, ancak Iş ortağı merkezindeki giriş sayfası URL 'SI `https://contoso.com/signup`. Yetkilendirme kodu, Resolve API 'SI çağırarak sağlama hizmetinin ayrıntıları için doğrulanabilir ve değiştirilebilir.  SaaS hizmeti sağlamayı tamamladığında, tamamlama işleminin tamamlandığını ve müşterinin faturalandırılabileceği sinyali veren bir etkinleştirme çağrısı gönderir. 

Aşağıdaki diyagramda, bir sağlama senaryosu için API çağrılarının sırası gösterilmektedir.  

![SaaS hizmeti sağlamak için API çağrıları](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Sağlanan

Bu durum, sağlanan bir hizmetin kararlı durumudur.

##### <a name="provisioning-for-update"></a>Güncelleştirme için sağlama 

Bu durum, var olan bir hizmete yönelik bir güncelleştirmenin beklendiğini belirtir. Bu tür bir güncelleştirme, müşteri tarafından Market 'ten veya SaaS hizmetinden (yalnızca doğrudan müşteri işlemleri için) başlatılabilir.

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Güncelleştirme için sağlama (Market 'ten başlatıldığında)

Aşağıdaki diyagramda, Market 'ten bir güncelleştirme başlatıldığında eylemlerin sırası gösterilmektedir.

![Market 'ten güncelleştirme başlatıldığında API çağrıları](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Güncelleştirme için sağlama (SaaS hizmetinden başlatıldığında)

Aşağıdaki diyagramda, SaaS hizmetinden bir güncelleştirme başlatıldığında eylemler gösterilmektedir. (Web kancası çağrısı, SaaS hizmeti tarafından başlatılan abonelikle bir güncelleştirmeyle değiştirilmiştir.) 

![SaaS hizmetinden güncelleştirme başlatıldığında API çağrıları](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Alın

Bu durum, bir müşterinin ödemesinin alınmadığını gösterir. İlkeye göre, aboneliği iptal etmeden önce müşteriye yetkisiz kullanım süresi sağlıyoruz. Bu durumda bir abonelik olduğunda: 

- İş ortağı olarak, kullanıcının hizmete erişimini düşürmeye veya engellemeye seçim yapabilirsiniz.
- Abonelik, verilerin veya ayarların kaybedilmesi gerekmeden tam işlevselliği geri yükleyen kurtarılabilir bir durumda tutulmalıdır. 
- Bu abonelik için kabul eden API 'Ler veya yetkisiz kullanım süresi sonunda bir ön sağlama isteği aracılığıyla bu abonelik için bir yeniden etkinleştirme isteği almanız bekleniyor. 

#### <a name="unsubscribed"></a>Kaldırıldı 

Abonelikler, açık müşteri isteğine veya Dues 'nin ödemesine yanıt olarak bu duruma ulaşacak. İş ortağının beklentisi, müşterinin verilerinin belirli bir gün sayısı için istek üzerine kurtarılmasına ve sonra silinmesine bağlıdır. 


## <a name="api-reference"></a>API başvurusu

Bu bölüm SaaS *aboneliği API 'si* ve *işlemler API*'sini belgeler.  Sürüm 2 API 'Leri için `api-version` parametresinin değeri `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Parametre ve varlık tanımları

Aşağıdaki tabloda, API 'Leri karşılama tarafından kullanılan ortak parametre ve varlıkların tanımları listelenmektedir.

|     Varlık/parametre     |     Tanım                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS kaynağı için GUID tanımlayıcısı.  |
| `name`                   | Müşteri tarafından bu kaynak için bir kolay ad belirtildi. |
| `publisherId`            | Her yayımcı için benzersiz bir dize tanımlayıcısı (örneğin: "contoso"). |
| `offerId`                | Her teklif için benzersiz bir dize tanımlayıcı (örneğin: "offer1").  |
| `planId`                 | Her plan/SKU için benzersiz bir dize tanımlayıcısı (örneğin: "Gümüş"). |
| `operationId`            | Belirli bir işlemin GUID tanımlayıcısı.  |
|  `action`                | `Unsubscribe`, `Suspend`, `Reinstate`veya `ChangePlan`, `ChangeQuantity`, `Transfer`bir kaynakta gerçekleştirilen eylem. |
|   |   |

Genel olarak benzersiz tanımlayıcılar ([GUID 'ler](https://en.wikipedia.org/wiki/Universally_unique_identifier)) genellikle otomatik olarak oluşturulan 128 bitlik (32-onaltılı) sayılardır. 

#### <a name="resolve-a-subscription"></a>Aboneliği çözümle 

Çözümleme uç noktası, yayımcının bir market belirtecini kalıcı bir kaynak KIMLIĞINE çözümlemesine olanak sağlar. Kaynak KIMLIĞI, SaaS aboneliğinin benzersiz tanımlayıcısıdır. Bir Kullanıcı bir iş ortağının Web sitesine yeniden yönlendirildiğinde, URL sorgu parametrelerinde bir belirteç içerir. Ortağın bu belirteci kullanması ve bunu çözümlemek için bir istek yapması beklenmektedir. Yanıt, kaynak için benzersiz SaaS Abonelik KIMLIĞI, adı, teklif KIMLIĞI ve planı içerir. Bu belirteç yalnızca bir saat için geçerlidir. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Yayınla<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Bu istek için kullanılacak işlemin sürümü.  |

*İstek üst bilgileri:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  İçerik türü      | `application/json` |
|  x-MS-RequestId    |  İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
|  x-MS-bağıntıkimliği |  İstemcideki işlem için benzersiz bir dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  yetkisi     |  [JSON Web belirteci (JWT) taşıyıcı belirtecini al](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Örneğin: "`Bearer <access_token>`". |
|  x-MS-Market-belirteç  |  Kullanıcı SaaS ortağının Web sitesine Azure 'dan yönlendirildiğinde, URL 'deki belirteç sorgu parametresi (örneğin: `https://contoso.com/signup?token=..`). *Note:* URL, kullanılmadan önce tarayıcıdan belirteç değerinin kodunu çözer.  |

*Yanıt kodları:*

Kod: 200<br>
Donuk belirteci bir SaaS aboneliğine çözümler. Yanıt gövdesi:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Kod: 400<br>
Hatalı istek. x-MS-Market-belirteç eksik, hatalı biçimlendirilmiş veya süresi dolmuştur.

Kod: 403<br>
Erişilmesini. Kimlik doğrulama belirteci sağlanmamış veya geçersiz ya da istek geçerli yayımcıya ait olmayan bir alıcıyla erişmeye çalışıyor.

Kod: 404<br>
Bulunamadı.

Kod: 500<br>
İç sunucu hatası.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>Abonelik API 'SI

Abonelik API 'SI şu HTTPS işlemlerini destekler: **Get**, **Post**, **Patch**ve **Delete**.


#### <a name="list-subscriptions"></a>Abonelikleri Listele

Bir yayımcının tüm SaaS aboneliklerini listeler.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Al<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Bu istek için kullanılacak işlemin sürümü.  |

*İstek üst bilgileri:*

|                    |                   |
|  ---------------   |  ---------------  |
| İçerik türü       |  `application/json`  |
| x-MS-RequestId     |  İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
| x-MS-bağıntıkimliği |  İstemcideki işlem için benzersiz bir dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
| yetkisi      |  [JSON Web belirteci (JWT) taşıyıcı belirtecini al](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Örneğin: "`Bearer <access_token>`".  |

*Yanıt kodları:*

Kod: 200 <br/>
Kimlik doğrulama belirtecine göre yayımcının tüm teklifleri için yayımcıyı ve ilgili abonelikleri alır.

>[!Note]
>[Sahte API 'ler](#mock-apis) , teklifi ilk geliştirirken kullanılır, ancak teklif yayımlandığında gerçek API 'lerin kullanılması gerekir.  Gerçek API 'ler ve sahte API 'Ler kodun ilk satırına göre farklılık gösterir.  Gerçek API 'de, bu bölüm, sahte API için mevcut olmadığından `subscription` bölümü vardır.

Sahte API için yanıt yükü:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
Ve gerçek API için: <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
Devamlılık belirteci yalnızca alınacak planların ek "sayfaları" varsa mevcut olacaktır. 

Kod: 403 <br>
Erişilmesini. Kimlik doğrulama belirteci sağlanmamış veya geçersiz ya da istek geçerli yayımcıya ait olmayan bir alıcıyla erişmeye çalışıyor. 

Kod: 500<br>
İç sunucu hatası.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Abonelik al

Belirtilen SaaS aboneliğini alır. Lisans bilgilerini ve plan bilgilerini almak için bu çağrıyı kullanın.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Al<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Belirteci çözümle API aracılığıyla çözümlendikten sonra elde edilen SaaS aboneliğinin benzersiz tanıtıcısı.   |
|  ApiVersion        |   Bu istek için kullanılacak işlemin sürümü.   |

*İstek üst bilgileri:*

|                    |                   |
|  ---------------   |  ---------------  |
|  İçerik türü      |  `application/json`  |
|  x-MS-RequestId    |  İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
|  x-MS-bağıntıkimliği |  İstemcideki işlem için benzersiz bir dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  yetkisi     |  [JSON Web belirteci (JWT) taşıyıcı belirtecini al](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Örneğin: "`Bearer <access_token>`".  |

*Yanıt kodları:*

Kod: 200<br>
Tanımlayıcıdan SaaS aboneliğini alır. Yanıt yükü:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Kod: 403<br>
Erişilmesini. Kimlik doğrulama belirteci sağlanmamış veya geçersiz ya da istek geçerli yayımcıya ait olmayan bir alıcıyla erişmeye çalışıyor.

Kod: 404<br>
Bulunamadı.<br> 

Kod: 500<br>
İç sunucu hatası.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Kullanılabilir planları listeleme

Geçerli yayımcı için özel veya genel teklifler olup olmadığını bulmak için bu çağrıyı kullanın.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Al<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Bu istek için kullanılacak işlemin sürümü.  |

*İstek üst bilgileri:*

|                    |                   |
|  ---------------   |  ---------------  |
|   İçerik türü     |  `application/json` |
|   x-MS-RequestId   |   İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
|  x-MS-bağıntıkimliği  | İstemcideki işlem için benzersiz bir dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
|  yetkisi     |  [JSON Web belirteci (JWT) taşıyıcı belirtecini al](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Örneğin: "`Bearer <access_token>`". |

*Yanıt kodları:*

Kod: 200<br>
Bir müşteri için kullanılabilir planların bir listesini alır. Yanıt gövdesi:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Kod: 404<br>
Bulunamadı.<br> 

Kod: 403<br>
Erişilmesini. Kimlik doğrulama belirteci sağlanmamış veya geçersiz ya da istek geçerli yayımcıya ait olmayan bir alıcıyla erişmeye çalışıyor. <br> 

Kod: 500<br>
İç sunucu hatası.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Aboneliği etkinleştir

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Yayınla<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Bu istek için kullanılacak işlemin sürümü.  |
| subscriptionId     | Çözümle API 'SI kullanılarak belirteç çözümlendikten sonra elde edilen SaaS aboneliğinin benzersiz tanıtıcısı.  |

*İstek üst bilgileri:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  İçerik türü      | `application/json`  |
|  x-MS-RequestId    | İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  x-MS-bağıntıkimliği  | İstemcideki işlem için benzersiz bir dize değeri. Bu dize, istemci işlemindeki tüm olayları sunucu tarafında bulunan olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  yetkisi     |  [JSON Web belirteci (JWT) taşıyıcı belirtecini al](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Örneğin: "`Bearer <access_token>`". |

*İstek yükü:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Yanıt kodları:*

Kod: 200<br>
Aboneliği etkinleştirir.<br>

Kod: 400<br>
Hatalı istek: doğrulama sorunları.

Kod: 403<br>
Erişilmesini. Kimlik doğrulama belirteci sağlanmamış veya geçersiz ya da istek geçerli yayımcıya ait olmayan bir alıcıyla erişmeye çalışıyor.

Kod: 404<br>
Bulunamadı.

Kod: 500<br>
İç sunucu hatası.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Abonelikte planı değiştirme

Abonelikte planı güncelleştirin.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Düzeltmesi<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Bu istek için kullanılacak işlemin sürümü.  |
| subscriptionId     | Çözümle API 'SI kullanılarak belirteç çözümlendikten sonra elde edilen SaaS aboneliğinin benzersiz tanıtıcısı.  |

*İstek üst bilgileri:*

|                    |                   |
|  ---------------   |  ---------------  |
|  İçerik türü      | `application/json` |
|  x-MS-RequestId    |   İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  x-MS-bağıntıkimliği  |  İstemcideki işlem için benzersiz bir dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.    |
| yetkisi      |  [JSON Web belirteci (JWT) taşıyıcı belirtecini al](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Örneğin: "`Bearer <access_token>`".  |

*İstek yükü:*

```json
Request Body:
{
    "planId": "gold"
}
```

*İstek üst bilgileri:*

|                    |                   |
|  ---------------   |  ---------------  |
| İşlem-konum | İşlemin durumunu almak için bir kaynağa olan bağlantı.   |

*Yanıt kodları:*

Kod: 202<br>
Planı değiştirme isteği kabul edildi. Ortağın, bir başarı veya başarısızlık tespit etmek için Işlem konumunu yoklamak üzere olması beklenir. <br>

Kod: 400<br>
Hatalı istek: doğrulama sorunları.

Kod: 403<br>
Erişilmesini. Kimlik doğrulama belirteci sağlanmamış veya geçersiz ya da istek geçerli yayımcıya ait olmayan bir alıcıyla erişmeye çalışıyor.

Kod: 404<br>
Bulunamadı.

Kod: 500<br>
İç sunucu hatası.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Tek seferde değil yalnızca bir plan veya miktar düzeltme eki uygulanabilir. **Güncelleştirme** ile bir abonelikte yapılan düzenlemeler `allowedCustomerOperations`değil.

#### <a name="change-the-quantity-on-the-subscription"></a>Abonelikteki miktarı değiştirme

Abonelikteki miktarı güncelleştirin.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Düzeltmesi<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Bu istek için kullanılacak işlemin sürümü.  |
| subscriptionId     | Çözümle API 'SI kullanılarak belirteç çözümlendikten sonra elde edilen SaaS aboneliğinin benzersiz tanıtıcısı.  |

*İstek üst bilgileri:*

|                    |                   |
|  ---------------   |  ---------------  |
|  İçerik türü      | `application/json` |
|  x-MS-RequestId    |   İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  x-MS-bağıntıkimliği  |  İstemcideki işlem için benzersiz bir dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.    |
| yetkisi      |  [JSON Web belirteci (JWT) taşıyıcı belirtecini al](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Örneğin: "`Bearer <access_token>`".  |

*İstek yükü:*

```json
Request Body:
{
    "quantity": 5
}
```

*İstek üst bilgileri:*

|                    |                   |
|  ---------------   |  ---------------  |
| İşlem-konum | İşlemin durumunu almak için bir kaynağa bağlayın.   |

*Yanıt kodları:*

Kod: 202<br>
Miktarı değiştirme isteği kabul edildi. Ortağın, bir başarı veya başarısızlık tespit etmek için Işlem konumunu yoklamak üzere olması beklenir. <br>

Kod: 400<br>
Hatalı istek: doğrulama sorunları.


Kod: 403<br>
Erişilmesini. Kimlik doğrulama belirteci sağlanmamış veya geçersiz ya da istek geçerli yayımcıya ait olmayan bir alıcıyla erişmeye çalışıyor.

Kod: 404<br>
Bulunamadı.

Kod: 500<br>
İç sunucu hatası.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Tek seferde değil yalnızca bir plan veya miktar düzeltme eki uygulanabilir. **Güncelleştirme** ile bir abonelikte yapılan düzenlemeler `allowedCustomerOperations`değil.

#### <a name="delete-a-subscription"></a>Abonelik silme

Belirtilen aboneliği kaldırın ve silin.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>Sil<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Bu istek için kullanılacak işlemin sürümü.  |
| subscriptionId     | Çözümle API 'SI kullanılarak belirteç çözümlendikten sonra elde edilen SaaS aboneliğinin benzersiz tanıtıcısı.  |

*İstek üst bilgileri:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   İçerik türü     |  `application/json` |
|  x-MS-RequestId    |   İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.   |
|  x-MS-bağıntıkimliği  |  İstemcideki işlem için benzersiz bir dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.   |
|  yetkisi     |  [JSON Web belirteci (JWT) taşıyıcı belirtecini al](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Örneğin: "`Bearer <access_token>`".  |

*Yanıt kodları:*

Kod: 202<br>
İş ortağı bir SaaS aboneliğinin aboneliğini kaldırma çağrısını başlattı.<br>

Kod: 400<br>
**Silme** `allowedCustomerOperations`olmayan bir abonelikte silme.

Kod: 403<br>
Erişilmesini. Kimlik doğrulama belirteci sağlanmamış veya geçersiz ya da istek geçerli yayımcıya ait olmayan bir alıcıyla erişmeye çalışıyor.

Kod: 404<br>
Bulunamadı.

Kod: 500<br>
İç sunucu hatası.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>İşlem API 'SI

Operations API 'SI aşağıdaki düzeltme ekini ve Al işlemlerini destekler.

#### <a name="list-outstanding-operations"></a>Bekleyen işlemleri Listele 

Geçerli yayımcı için bekleyen işlemleri listeler. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Al<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Bu istek için kullanılacak işlemin sürümü.                |
| subscriptionId     | Çözümle API 'SI kullanılarak belirteç çözümlendikten sonra elde edilen SaaS aboneliğinin benzersiz tanıtıcısı.  |

*İstek üst bilgileri:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   İçerik türü     |  `application/json` |
|  x-MS-RequestId    |  İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  x-MS-bağıntıkimliği |  İstemcideki işlem için benzersiz bir dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  yetkisi     |  [JSON Web belirteci (JWT) taşıyıcı belirtecini al](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Örneğin: "`Bearer <access_token>`".  |

*Yanıt kodları:*

Kod: 200<br> Bir abonelikte bekleyen işlemlerin listesini alır. Yanıt yükü:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


Kod: 400<br>
Hatalı istek: doğrulama sorunları.

Kod: 403<br>
Erişilmesini. Kimlik doğrulama belirteci sağlanmamış veya geçersiz ya da istek geçerli yayımcıya ait olmayan bir alıcıyla erişmeye çalışıyor.

Kod: 404<br>
Bulunamadı.

Kod: 500<br>
İç sunucu hatası.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>İşlem durumunu al

Yayımcının belirtilen tetiklenen zaman uyumsuz işlemin durumunu izlemesini sağlar (örneğin, `Subscribe`, `Unsubscribe`, `ChangePlan`veya `ChangeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Al<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Bu istek için kullanılacak işlemin sürümü.  |

*İstek üst bilgileri:*

|                    |                   |
|  ---------------   |  ---------------  |
|  İçerik türü      |  `application/json`   |
|  x-MS-RequestId    |   İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  x-MS-bağıntıkimliği |  İstemcideki işlem için benzersiz bir dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır.  |
|  yetkisi     |  [JSON Web belirteci (JWT) taşıyıcı belirtecini al](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Örneğin: "`Bearer <access_token>`".  |

*Yanıt kodları:*<br>

Kod: 200<br> Belirtilen bekleyen SaaS işlemini alır. Yanıt yükü:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Kod: 400<br>
Hatalı istek: doğrulama sorunları.

Kod: 403<br>
Erişilmesini. Kimlik doğrulama belirteci sağlanmamış veya geçersiz ya da istek geçerli yayımcıya ait olmayan bir alıcıyla erişmeye çalışıyor.
 
Kod: 404<br>
Bulunamadı.

Kod: 500<br> İç sunucu hatası.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>İşlemin durumunu güncelleştirme

Bir işlemin durumunu, belirtilen değerlerle başarı veya başarısızlığı gösterecek şekilde güncelleştirin.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Düzeltmesi<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Bu istek için kullanılacak işlemin sürümü.  |
| subscriptionId     | Çözümle API 'SI kullanılarak belirteç çözümlendikten sonra elde edilen SaaS aboneliğinin benzersiz tanıtıcısı.  |
|  operationId       | Tamamlanan işlem. |

*İstek üst bilgileri:*

|                    |                   |
|  ---------------   |  ---------------  |
|   İçerik türü     | `application/json`   |
|   x-MS-RequestId   |   İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
|  x-MS-bağıntıkimliği |  İstemcideki işlem için benzersiz bir dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
|  yetkisi     |  [JSON Web belirteci (JWT) taşıyıcı belirtecini al](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Örneğin: "`Bearer <access_token>`".  |

*İstek yükü:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Yanıt kodları:*

Kod: 200<br> İş ortağı tarafındaki bir işlemin tamamlandığını bilgilendirmek için bir çağrı. Örneğin, bu yanıt, koltuk veya planların değiştirilmesini işaret verebilir.

Kod: 400<br>
Hatalı istek: doğrulama sorunları.

Kod: 403<br>
Erişilmesini. Kimlik doğrulama belirteci sağlanmamış veya geçersiz ya da istek geçerli yayımcıya ait olmayan bir alıcıyla erişmeye çalışıyor.

Kod: 404<br>
Bulunamadı.

Kod: 409<br>
Uzantıları. Örneğin, daha yeni bir işlem zaten yerine getirilir.

Kod: 500<br> İç sunucu hatası.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>SaaS hizmetinde Web kancası uygulama

Yayımcının, kullanıcılarına hizmetinde değişiklik yapmak için bu SaaS hizmetinde bir Web kancası uygulaması gerekir. SaaS hizmetinin, Web kancası bildiriminde bir işlem yapmadan önce doğrulamak ve yetkilendirmek için Operations API 'sini çağırması beklenmektedir.


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Eylemin aşağıdakilerden biri olabilir: 
- `Unsubscribe` (kaynak silindiğinde)
- `ChangePlan` (değişiklik planı işlemi tamamlandığında)
- `ChangeQuantity` (miktar değiştirme işlemi tamamlandığında)
- `Suspend` (kaynak askıya alındığında)
- `Reinstate` (kaynak askıya alındıktan sonra tekrar ifade edildiğinde)

Durum aşağıdakilerden biri olabilir: 
- **NotStarted** <br>
 - **Ediyor** <br>
- **Baarı** <br>
- **Başaramadı** <br>
- **Uzantıları** <br>

Web kancası bildiriminde, işlem yapılabilir durumlar **başarılı** ve **başarısız**olabilir. Bir işlemin yaşam döngüsü, **NotStarted** 'den **başarılı**, **başarısız**veya **Çakışma**gibi bir Terminal durumuna kadar olur. **NotStarted** veya **InProgress**alırsanız, işlem yapılmadan önce işlem bir TERMINAL durumuna ulaşıncaya kadar API al aracılığıyla durumu isteme işlemine devam edin. 

## <a name="mock-apis"></a>Sahte API 'Ler

Geliştirme, özellikle Prototipleme ve projeleri test etme ile çalışmaya başlamanıza yardımcı olması için, sahte API 'lerimizi kullanabilirsiniz. 

Konak uç noktası: `https://marketplaceapi.microsoft.com/api` (kimlik doğrulaması gerekmez)<br/>
API sürümü: `2018-09-15`<br/>
Örnek URI: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

API uç noktası yolları hem sahte hem de gerçek API 'lerde aynıdır, ancak API sürümleri farklıdır. Sürüm, sahte sürüm için `2018-09-15` ve üretim sürümü için `2018-08-31`. 

Bu makaledeki API çağrılarından herhangi biri, sahte konak uç noktasına yapılabilir. Genel olarak, sahte verileri yanıt olarak geri almak beklenir. Sahte API üzerinde abonelik güncelleştirme yöntemlerine yapılan çağrılar her zaman 500 ' i döndürür. 

## <a name="next-steps"></a>Sonraki adımlar

Geliştiriciler, [bulut iş ortağı PORTALı REST API 'lerini](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)kullanarak iş yüklerini, teklifleri ve yayımcı profillerini de programlı bir şekilde alıp işleyebilir.
