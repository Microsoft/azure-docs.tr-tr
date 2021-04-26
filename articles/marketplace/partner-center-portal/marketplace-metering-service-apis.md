---
title: Ölçüm hizmeti API 'Leri-Microsoft ticari Market
description: Kullanım olayı API 'SI, Microsoft AppSource ve Azure Marketi 'nde SaaS teklifleri için kullanım olaylarını yayalmanıza olanak sağlar.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: b75964f8cfc41efc35858284dbffded3aa406eb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96436071"
---
# <a name="marketplace-metered-billing-apis"></a>Market ölçülen faturalandırma API 'Leri

Yayımcı, Iş Ortağı Merkezi 'nde yayımlanacak bir teklif için özel ölçüm boyutları oluşturduğunda ölçülen faturalandırma API 'Leri kullanılmalıdır. Kullanım olaylarını yaymaya yönelik özel boyutları olan bir veya daha fazla planı olan satın alınan tüm teklifler için ölçülen faturalandırma API 'Leri ile tümleştirme gereklidir.

SaaS için özel ölçüm boyutları oluşturma hakkında daha fazla bilgi için bkz. [SaaS ölçülen faturalandırma](saas-metered-billing.md).

Yönetilen uygulama planına sahip bir Azure uygulaması teklifi için özel ölçüm boyutları oluşturma hakkında daha fazla bilgi için bkz. [Azure uygulamanızı yapılandırma teklif kurulumu ayrıntıları](../create-new-azure-apps-offer.md#configure-your-azure-application-offer-setup-details).

## <a name="enforcing-tls-12-note"></a>TLS 1,2 zorlama

TLS sürüm 1,2 sürümü, HTTPS iletişimleri için en düşük sürüm olarak zorlanır. Kodunuzda bu TLS sürümünü kullandığınızdan emin olun. TLS sürüm 1,0 ve 1,1 kullanım dışıdır ve bağlantı girişimleri reddedilir.

## <a name="metered-billing-single-usage-event"></a>Ölçülen faturalandırma tek kullanım olayı

Kullanım olayı API 'SI, belirli müşteri tarafından satın alınan plana yönelik kullanım olaylarını etkin bir kaynağa (abone olunmuş) göre göstermek için yayımcı tarafından çağrılmalıdır. Kullanım olayı, teklif yayımlanırken yayımcı tarafından tanımlanan planın her özel boyutu için ayrı olarak yayınlanır.

Takvim gününün her bir saati için yalnızca bir kullanım olayı dağıtılabilir. Örneğin, 8:15 ' te bugün bir kullanım olayı oluşturabilirsiniz. Bu olay kabul edilirse, sonraki kullanım etkinliği bugün 9:00 ' den kabul edilecektir. 8:15 ile 8:59:59 arasında ek bir olay gönderirseniz, bu, bir yinelenen olarak reddedilir. Bir saat içinde tüketilen tüm birimleri biriktirmek ve sonra tek bir olayda yaymalısınız.

Kaynak başına bir takvim gününe ait her saat için yalnızca bir kullanım olayı dağıtılabilir. Bir saat içinde birden fazla birim tüketilirse, saat içinde tüketilen tüm birimleri birikir ve tek bir olayda yayın. Kullanım olayları yalnızca son 24 saat için kullanılabilir. Bir kullanım olayını 8:00 ile 8:59:59 arasında herhangi bir zamanda yayar (ve kabul edilirse) ve aynı gün için 8:00 ve 8:59:59 arasında ek bir olay gönderirseniz, yineleme olarak reddedilir.

**Gönderi**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Sorgu parametreleri:*

| Parametresi | Öneri          |
| ---------- | ---------------------- |
| `ApiVersion` | 2018-08-31 kullanın. |
| | |

*İstek üst bilgileri:*

| İçerik türü       | `application/json` komutunu kullanma  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | İstemciden gelen isteği izlemek için benzersiz dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır. |
| `x-ms-correlationid` | İstemcideki işlem için benzersiz dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
| `authorization`   | Bu API çağrısını yapan ISV 'YI tanımlayan benzersiz bir erişim belirteci. Biçim, `"Bearer <access_token>"` belirteç değerinin yayımcı tarafından şu şekilde açıklandığı gibi alındığı <br> <ul> <li> İçindeki SaaS [, belirteci BIR http gönderimiyle alır](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> [Kimlik doğrulama stratejilerinde](./marketplace-metering-service-authentication.md)yönetilen uygulama. </li> </ul> |
| | |

*İstek gövdesi örneği:*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId` SaaS uygulaması ve yönetilen uygulama yayma özel ölçümü için farklı anlamlara sahiptir. 

Azure uygulama tarafından yönetilen uygulamalar planları için, `resourceId` yönetilen uygulamadır `resource group Id` . [Azure tarafından yönetilen kimlikler belirtecini kullanarak](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token), bu dosyayı getirmeye yönelik örnek bir betik bulunabilir. 

SaaS teklifleri için `resourceId` SaaS ABONELIK kimliğidir. SaaS abonelikleri hakkında daha fazla bilgi için bkz. [abonelikleri listeleme](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Yanıtlar

Kod: 200<br>
Tamam ögesini seçin. Kullanım egörevi, daha fazla işlem ve faturalandırma için Microsoft tarafında kabul edildi ve kaydedildi.

Yanıt yükü örneği: 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

Kod: 400 <br>
Hatalı istek.

* Eksik veya geçersiz istek verileri belirtildi.
* `effectiveStartTime` Geçmişte 24 saatten fazla. Olayın süresi doldu.
* SaaS aboneliği abone durumunda değil.

Yanıt yükü örneği: 

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Kod: 403<br>

Inı. Yetkilendirme belirteci belirtilmemiş, geçersiz veya zaman aşımına uğradı.  Ya da istek, yetkilendirme belirtecini oluşturmak için kullanılan bir Azure AD Uygulaması KIMLIĞIYLE yayınlanan bir teklifin aboneliğine erişmeye çalışıyor.

Kod: 409<br>
Uzantıları. Belirtilen kaynak KIMLIĞI, geçerli kullanım tarihi ve saati için bir kullanım olayı zaten başarıyla bildirildi.

Yanıt yükü örneği: 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>Ölçülen faturalandırma toplu işlem kullanım olayı

Toplu kullanım olayı API 'SI, birden fazla satın alınan kaynağın kullanım olaylarını aynı anda yayalmanıza olanak sağlar. Aynı zamanda, farklı takvim saatlerinde olduğu sürece aynı kaynak için birkaç kullanım olayı da yayabilirsiniz. Tek bir toplu işlemdeki olayların maxhayvan sayısı 25 ' tir.

**Gönderi:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Sorgu parametreleri:*

| Parametre  | Öneri     |
| ---------- | -------------------- |
| `ApiVersion` | 2018-08-31 kullanın. |

*İstek üst bilgileri:*

| İçerik türü       | `application/json` komutunu kullanma       |
| ------------------ | ------ |
| `x-ms-requestid`     | İstemciden gelen isteği izlemek için benzersiz dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır. |
| `x-ms-correlationid` | İstemcideki işlem için benzersiz dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanmış olur. |
| `authorization`      | Bu API çağrısını yapan ISV 'YI tanımlayan benzersiz bir erişim belirteci. Biçim, `Bearer <access_token>` belirteç değerinin yayımcı tarafından şu şekilde açıklandığı gibi alındığı <br> <ul> <li> İçindeki SaaS [, belirteci BIR http gönderimiyle alır](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> [Kimlik doğrulama stratejilerinde](./marketplace-metering-service-authentication.md)yönetilen uygulama. </li> </ul> |
| | |


*İstek gövdesi örneği:*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId` SaaS uygulaması ve yönetilen uygulama yayma özel ölçümü için farklı anlamlara sahiptir. 

Azure uygulama tarafından yönetilen uygulamalar planları için, `resourceId` yönetilen uygulamadır `resource group Id` . [Azure tarafından yönetilen kimlikler belirtecini kullanarak](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token), bu dosyayı getirmeye yönelik örnek bir betik bulunabilir. 

SaaS teklifleri için `resourceId` SaaS ABONELIK kimliğidir. SaaS abonelikleri hakkında daha fazla bilgi için bkz. [abonelikleri listeleme](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Yanıtlar

Kod: 200<br>
Tamam ögesini seçin. Toplu kullanım egörev, daha fazla işlem ve faturalandırma için Microsoft tarafında kabul edildi ve kaydedildi. Yanıt listesi, toplu işteki her bir olay için durum ile döndürülür. Toplu iş olayının bir parçası olarak gönderilen her bir kullanım olayının yanıtlarını anlamak için yanıt yükünde yineleme yapmanız gerekir.

Yanıt yükü örneği: 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

API yanıtında başvurulan durum kodu açıklaması `BatchUsageEvent` :

| Durum kodu  | Description |
| ---------- | -------------------- |
| `Accepted` | Eden. |
| `Expired` | Kullanım zaman aşımına uğradı. |
| `Duplicate` | Yinelenen kullanım belirtildi. |
| `Error` | Hata kodu. |
| `ResourceNotFound` | Belirtilen kullanım kaynağı geçersiz. |
| `ResourceNotAuthorized` | Bu kaynak için kullanım sağlama yetkiniz yok. |
| `InvalidDimension` | Bu teklif/plan için kullanım geçirildiği boyut geçersiz. |
| `InvalidQuantity` | Geçirilen miktar 0 ' dan küçük veya buna eşit. |
| `BadArgument` | Giriş eksik veya hatalı biçimlendirilmiş. |

Kod: 400<br>
Hatalı istek. Batch, 25 ' ten fazla kullanım olayı içeriyordu.

Kod: 403<br>
Inı. Yetkilendirme belirteci belirtilmemiş, geçersiz veya zaman aşımına uğradı.  Ya da istek, yetkilendirme belirtecini oluşturmak için kullanılan bir Azure AD Uygulaması KIMLIĞIYLE yayınlanan bir teklifin aboneliğine erişmeye çalışıyor.

## <a name="development-and-testing-best-practices"></a>Geliştirme ve test en iyi uygulamaları

Özel Ölçüm emisyonunu test etmek için, ölçüm API 'SI ile tümleştirmeyi uygulayın, bir birim başına sıfır fiyatla birlikte, yayınlanmış SaaS teklifiniz için bir plan oluşturun. Ve bu teklifi önizleme olarak yayımlayarak yalnızca sınırlı kullanıcılar tümleştirmeyi erişebilir ve test edebilir.

Ayrıca, sınırlı hedef kitlelerine test sırasında bu plana erişimi sınırlandırmak için, mevcut bir canlı teklif için özel planı da kullanabilirsiniz.

## <a name="get-support"></a>Destek alın

Yayımcı desteği seçeneklerini anlamak ve Microsoft ile destek bileti açmak için [Iş Ortağı Merkezi ' nde ticari Market programına yönelik destek](../support.md) bölümündeki yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Ölçüm hizmeti API 'Leri hakkında daha fazla bilgi için bkz. [Market ölçüm hizmeti API 'LERI SSS](./marketplace-metering-service-apis-faq.md).
