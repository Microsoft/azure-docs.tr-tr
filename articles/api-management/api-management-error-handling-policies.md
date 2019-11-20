---
title: Azure API Management ilkelerinde hata işleme | Microsoft Docs
description: Azure API Management isteklerin işlenmesi sırasında oluşabilecek hata koşullarına nasıl yanıt verileceğini öğrenin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/19/2018
ms.author: apimpm
ms.openlocfilehash: df7b14c8221ab7837cabe968a82cfc5d5d9050c4
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072589"
---
# <a name="error-handling-in-api-management-policies"></a>API Management ilkelerinde hata işleme

Azure API Management, `ProxyError` bir nesne sağlayarak, yayımcıların isteklerin işlenmesi sırasında oluşabilecek hata koşullarına yanıt vermesini sağlar. `ProxyError` Nesnesine bağlam üzerinden erişilir [. LastError](api-management-policy-expressions.md#ContextVariables) özelliği ve `on-error` ilke bölümünde ilkeler tarafından kullanılabilir. Bu makalede, Azure API Management 'daki hata işleme özelliklerine yönelik bir başvuru sağlanmaktadır.  
  
## <a name="error-handling-in-api-management"></a>API Management işlenirken hata oluştu

 Azure `inbound`API Management ilkeleri `backend` ,aşağıdakiörnektegösterildiği`on-error` gibi, ,vebölümlerinebölünür.`outbound`  
  
```xml  
<policies>  
  <inbound>  
    <!-- statements to be applied to the request go here -->  
  </inbound>  
  <backend>  
    <!-- statements to be applied before the request is   
         forwarded to the backend service go here -->  
    </backend>  
    <outbound>  
      <!-- statements to be applied to the response go here -->  
    </outbound>  
    <on-error>  
        <!-- statements to be applied if there is an error   
             condition go here -->  
  </on-error>  
</policies>  
```  
  
Bir isteğin işlenmesi sırasında, yerleşik adımlar, istek kapsamında olan herhangi bir ilke ile birlikte yürütülür. Bir hata oluşursa, işleme hemen `on-error` ilke bölümüne atlar.  
`on-error` İlke bölümü herhangi bir kapsamda kullanılabilir. API yayımcıları, hatayı olay hub 'larına kaydetme veya arayana dönmek için yeni bir yanıt oluşturma gibi özel davranışları yapılandırabilir.  
  
> [!NOTE]
>  `on-error` Bölümü varsayılan olarak ilkelerde yer almıyor. `on-error` Bölümü bir ilkeye eklemek için, ilke düzenleyicisinde istenen ilkeye gidin ve ekleyin. İlkeleri yapılandırma hakkında daha fazla bilgi için bkz. [API Management ilkeleri](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  `on-error` Bölüm yoksa, arayanlar bir hata durumu oluşursa 400 veya 500 http yanıt iletisi alır.  
  
### <a name="policies-allowed-in-on-error"></a>Hatada izin verilen ilkeler

 `on-error` İlke bölümünde aşağıdaki ilkeler kullanılabilir.  
  
-   ['yu](api-management-advanced-policies.md#choose)  
-   [Set değişkeni](api-management-advanced-policies.md#set-variable)  
-   [bul ve Değiştir](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [dönüş-yanıt](api-management-advanced-policies.md#ReturnResponse)  
-   [Set-Header](api-management-transformation-policies.md#SetHTTPheader)  
-   [Set-yöntemi](api-management-advanced-policies.md#SetRequestMethod)  
-   [durum ayarla](api-management-advanced-policies.md#SetStatus)  
-   [gönderme isteği](api-management-advanced-policies.md#SendRequest)  
-   [tek yönlü istek gönder](api-management-advanced-policies.md#SendOneWayRequest)  
-   [-eventhub arası](api-management-advanced-policies.md#log-to-eventhub)  
-   [JSON-XML](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [XML-JSON](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError

 Bir hata oluştuğunda ve denetim `on-error` ilke bölümüne atdığında, hata bağlamda saklanır [. LastError](api-management-policy-expressions.md#ContextVariables) özelliği, `on-error` bölümünde ilkeler tarafından erişilebilecek. LastError aşağıdaki özelliklere sahiptir.  
  
| Name       | Tür   | Açıklama                                                                                               | Gerekli |
|------------|--------|-----------------------------------------------------------------------------------------------------------|----------|
| `Source`   | dize | Hatanın oluştuğu öğeyi adlandırır. İlke ya da yerleşik bir ardışık düzen adımı adı olabilir.     | Evet      |
| `Reason`   | dize | Hata işlemede kullanılabilecek makine dostu hata kodu.                                       | Hayır       |
| `Message`  | dize | İnsan tarafından okunabilen hata açıklaması.                                                                         | Evet      |
| `Scope`    | dize | Hatanın oluştuğu kapsamın adı ve "genel", "ürün", "API" veya "işlem" olabilir | Hayır       |
| `Section`  | dize | Hatanın oluştuğu bölüm adı. Olası değerler: "gelen", "arka uç", "giden" veya "hata üzerinde".       | Hayır       |
| `Path`     | dize | İç içe geçmiş ilkeyi belirtir, örneğin "SELECT [3]/, [2]".                                                        | Hayır       |
| `PolicyId` | dize | Hatanın oluştuğu ilkede, müşteri tarafından belirtilmişse özniteliğindeğeri`id`             | Hayır       |

> [!TIP]
> Durum koduna bağlam aracılığıyla erişebilirsiniz. Response. StatusCode.  

> [!NOTE]
> Tüm ilkelerin, ilkenin kök `id` öğesine eklenebilen bir isteğe bağlı özniteliği vardır. Bir hata durumu oluştuğunda bu öznitelik bir ilkede mevcutsa özniteliğin değeri `context.LastError.PolicyId` özelliği kullanılarak alınabilir.

## <a name="predefined-errors-for-built-in-steps"></a>Yerleşik adımlar için önceden tanımlanmış hatalar  
 Aşağıdaki hatalar, yerleşik işleme adımlarının değerlendirmesi sırasında oluşabilecek hata koşulları için önceden tanımlanmıştır.  
  
| Source        | Koşul                                 | Reason                  | `Message`                                                                                                                |
|---------------|-------------------------------------------|-------------------------|------------------------------------------------------------------------------------------------------------------------|
| yapılandırma | URI, herhangi bir API veya Işlemle eşleşmiyor | OperationNotFound       | Gelen istek bir işleme dönüştürülemiyor.                                                                      |
| authorization | Abonelik anahtarı sağlanmadı             | SubscriptionKeyNotFound | Eksik abonelik anahtarı nedeniyle erişim reddedildi. Bu API 'ye istek yaparken abonelik anahtarını eklediğinizden emin olun. |
| authorization | Abonelik anahtarı değeri geçersiz         | Subscriptionkeygeçersiz  | Geçersiz abonelik anahtarı nedeniyle erişim reddedildi. Etkin bir abonelik için geçerli bir anahtar sağladığınızdan emin olun.            |
  
## <a name="predefined-errors-for-policies"></a>İlkeler için önceden tanımlanmış hatalar  
 Aşağıdaki hatalar, ilke değerlendirmesi sırasında oluşabilecek hata koşulları için önceden tanımlanmıştır.  
  
| Source       | Koşul                                                       | Reason                    | `Message`                                                                                                                              |
|--------------|-----------------------------------------------------------------|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| hız limiti   | Hız sınırı aşıldı                                             | Ratelimitexcebaşında         | Hız sınırı aşıldı                                                                                                               |
| kota        | Kota aşıldı                                                  | QuotaExceeded             | Çağrı hacmi kotası aşıldı. Kota xx: xx: xx içinde replenished olacaktır. -veya-bant genişliği kotası. Kota xx: xx: xx içinde replenished olacaktır. |
| JSONP        | Geri çağırma parametre değeri geçersiz (yanlış karakterler içeriyor) | Callbackparametergeçersiz  | {Callback-Parameter-Name} geri çağırma parametresinin değeri geçerli bir JavaScript tanımlayıcısı değil.                                          |
| IP filtresi    | Çağıran IP, istekten ayrıştırılamadı                          | Failedtoparsecallerıp     | Çağıran için IP adresi oluşturulamadı. Erişim reddedildi.                                                                        |
| IP filtresi    | Arayan IP 'si izin verilenler listesinde değil                                | Callerıpnotallowed        | {IP-Address} çağıran IP adresine izin verilmez. Erişim reddedildi.                                                                        |
| IP filtresi    | Çağıran IP engellenen listede                                    | Calleripengellenen           | Çağıran IP adresi engellendi. Erişim reddedildi.                                                                                         |
| onay üst bilgisi | Gerekli üst bilgi sunulmadı veya değer eksik               | HeaderNotFound            | İstekte {Header-name} üst bilgisi bulunamadı. Erişim reddedildi.                                                                    |
| onay üst bilgisi | Gerekli üst bilgi sunulmadı veya değer eksik               | HeaderValueNotAllowed     | {Header-value} öğesinin üst bilgi {Header-Name} değerine izin verilmez. Erişim reddedildi.                                                          |
| Validate-JWT | İstekte JWT belirteci eksik                                 | TokenNotFound             | İstekte JWT bulunamadı. Erişim reddedildi.                                                                                         |
| Validate-JWT | İmza doğrulama başarısız oldu                                     | Tokensignaturegeçersiz     | JWT kitaplığından\>ileti <. Erişim reddedildi.                                                                                          |
| Validate-JWT | Geçersiz hedef kitle                                                | TokenAudienceNotAllowed   | JWT kitaplığından\>ileti <. Erişim reddedildi.                                                                                          |
| Validate-JWT | Geçersiz veren                                                  | Tokenıssuernotallowed     | JWT kitaplığından\>ileti <. Erişim reddedildi.                                                                                          |
| Validate-JWT | Belirtecin süresi doldu                                                   | Tokenaşımına uğradı              | JWT kitaplığından\>ileti <. Erişim reddedildi.                                                                                          |
| Validate-JWT | İmza anahtarı ID tarafından çözümlenemedi                            | TokenSignatureKeyNotFound | JWT kitaplığından\>ileti <. Erişim reddedildi.                                                                                          |
| Validate-JWT | Gerekli talepler belirteçte eksik                          | TokenClaimNotFound        | JWT belirtecinde şu talepler eksik: < C1\>, < C2\>,... Erişim reddedildi.                                                            |
| Validate-JWT | Talep değerleri uyumsuzluğu                                           | TokenClaimValueNotAllowed | {Claim-Value} talebinin {Claim-Name} değerine izin verilmez. Erişim reddedildi.                                                             |
| Validate-JWT | Diğer doğrulama sorunları                                       | Jwtgeçersiz                | JWT kitaplığından ileti <\>                                                                                                          |

## <a name="example"></a>Örnek

API ilkesini şu şekilde ayarlama:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <set-header name="ErrorSource" exists-action="override">
            <value>@(context.LastError.Source)</value>
        </set-header>
        <set-header name="ErrorReason" exists-action="override">
            <value>@(context.LastError.Reason)</value>
        </set-header>
        <set-header name="ErrorMessage" exists-action="override">
            <value>@(context.LastError.Message)</value>
        </set-header>
        <set-header name="ErrorScope" exists-action="override">
            <value>@(context.LastError.Scope)</value>
        </set-header>
        <set-header name="ErrorSection" exists-action="override">
            <value>@(context.LastError.Section)</value>
        </set-header>
        <set-header name="ErrorPath" exists-action="override">
            <value>@(context.LastError.Path)</value>
        </set-header>
        <set-header name="ErrorPolicyId" exists-action="override">
            <value>@(context.LastError.PolicyId)</value>
        </set-header>
        <set-header name="ErrorStatusCode" exists-action="override">
            <value>@(context.Response.StatusCode.ToString())</value>
        </set-header>
        <base />
    </on-error>
</policies>
```

ve yetkisiz bir istek göndermek aşağıdaki yanıta neden olur:

![Yetkisiz hata yanıtı](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Sonraki adımlar

İlkelerle çalışma hakkında daha fazla bilgi için bkz.

+ [API Management ilkeler](api-management-howto-policies.md)
+ [API dönüştürme](transform-api.md)
+ İlke deyimlerinin ve ayarlarının tam listesi için [Ilke başvurusu](api-management-policy-reference.md)
+ [İlke örnekleri](policy-samples.md)   
