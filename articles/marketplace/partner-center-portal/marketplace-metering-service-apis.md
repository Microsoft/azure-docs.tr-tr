---
title: Market ölçüm hizmeti API 'Leri | Azure Marketi
description: Azure Marketi 'nde SaaS teklifleri için kullanım olayı.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 36ca95191e0e6422bd93360b98243393acad8147
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825485"
---
# <a name="marketplace-metering-service-apis"></a>Market ölçüm hizmeti API’leri

Kullanım olayı API 'SI, satın alınan belirli bir varlık için kullanım olaylarını yayalmanıza olanak sağlar. Kullanım olayı isteği, teklif yayımlanırken yayımcı tarafından tanımlanan ölçüm hizmetleri boyutuna başvurur.

## <a name="usage-event"></a>Kullanım olayı

**Gönderi**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | Bu istek için kullanılacak işlemin sürümü. En son API sürümü 2018-08-31 ' dir. |

*İstek üst bilgileri:*

| içerik türü       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | İstemciden gelen isteği izlemek için benzersiz dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır. |
| `x-ms-correlationid` | İstemcideki işlem için benzersiz dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanacaktır. |
| `authorization`   | [JSON Web belirteci (JWT) taşıyıcı belirtecini al.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Note: HTTP isteği yapıldığında, önek başvurulan bağlantıdan alınan belirtece `Bearer`. |

*İsteyen*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>Yanıtlar

Kod: 200<br>
Tamam 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

Kod: 400 <br>
Hatalı istek, eksik veya geçersiz veri sağlanmış veya geçerliliği zaman aşımına uğradı

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
Hatalı istek, eksik veya geçersiz veri sağlanmış veya geçerliliği zaman aşımına uğradı

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Kod: 409<br>
Kullanım kaynak KIMLIĞI için kullanım çağrısını ve zaten var olan etkin kullanımı aldığımızda çakışma. Yanıt, kabul edilen ileti hakkında bilgi içeren `additionalInfo` alanı içerecektir.

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>Toplu kullanım olayı

Toplu kullanım olayı API 'SI, bir kerede birden fazla satın alınan varlık için kullanım olaylarını yayalmanıza olanak sağlar. Toplu kullanım olay isteği, teklif yayımlanırken yayımcı tarafından tanımlanan ölçüm hizmetleri boyutuna başvurur.

>[!Note]
>Microsoft 'un ticari marketi 'nde birden çok SaaS teklifi kaydedebilirsiniz. Her kayıtlı SaaS teklifinin, kimlik doğrulama ve yetkilendirme amaçları için kayıtlı benzersiz bir Azure AD uygulaması vardır. Toplu iş içinde Yayınlanan olaylar, teklifi kaydetme sırasında aynı Azure AD uygulamasıyla sunulan tekliflere ait olmalıdır.

**Gönderi:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Sorgu parametreleri:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | Bu istek için kullanılacak işlemin sürümü. En son API sürümü 2018-08-31 ' dir. |

*İstek üst bilgileri:*

| içerik türü       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | İstemciden gelen isteği izlemek için benzersiz dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır. |
| `x-ms-correlationid` | İstemcideki işlem için benzersiz dize değeri. Bu parametre, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanmış olur. |
| `authorization`      | [JSON Web belirteci (JWT) taşıyıcı belirtecini al.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Note: HTTP isteği yapıldığında, önek başvurulan bağlantıdan alınan belirtece `Bearer`.  |

*İsteyen*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>Yanıtlar

Kod: 200<br>
Tamam

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

`BatchUsageEvent` API yanıtında başvurulan durum kodu açıklaması:

| Durum kodu  | Açıklama |
| ---------- | -------------------- |
| `Accepted` | Kabul edilen kod. |
| `Expired` | Kullanım zaman aşımına uğradı. |
| `Duplicate` | Yinelenen kullanım belirtildi. |
| `Error` | Hata kodu. |
| `ResourceNotFound` | Belirtilen kullanım kaynağı geçersiz. |
| `ResourceNotAuthorized` | Bu kaynak için kullanım sağlama yetkiniz yok. |
| `InvalidDimension` | Bu teklif/plan için kullanım geçirildiği boyut geçersiz. |
| `InvalidQuantity` | Geçirilen miktar 0 <. |
| `BadArgument` | Giriş eksik veya hatalı biçimlendirilmiş. |

Kod: 400<br>
Hatalı istek, eksik veya geçersiz veri sağlanmış veya geçerliliği zaman aşımına uğradı

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
Kod: 403<br>
Kullanıcının bu çağrıyı yapması yetkilendirilmemiş

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [SaaS ölçülen faturalandırma](./saas-metered-billing.md).
