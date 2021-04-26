---
title: Azure Active Directory REST API-kimlik doğrulaması
description: Azure Uygulama yapılandırmasında kimlik doğrulamak için Azure Active Directory kullanın REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cbf05245768a663e324e9bb6e1ad422eeee3ab1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930526"
---
# <a name="azure-active-directory-authentication"></a>Azure Active Directory kimlik doğrulaması

`Bearer`Azure Active Directory (Azure AD) tarafından alınan bir belirteçle kimlik doğrulama şemasını kullanarak http isteklerinin kimliğini doğrulayabilirsiniz. Bu istekleri Aktarım Katmanı Güvenliği (TLS) üzerinden iletmelidir.

## <a name="prerequisites"></a>Önkoşullar

Azure AD belirteci istemek için kullanılan sorumluyu, ilgili [Azure uygulama yapılandırma rollerinden](./rest-api-authorization-azure-ad.md)birine atamanız gerekir.

Kimlik doğrulaması için gereken tüm HTTP üstbilgilerini içeren her isteği sağlayın. En düşük gereksinim aşağıda verilmiştir:

|  İstek üst bilgisi | Açıklama  |
| --------------- | ------------ |
| `Authorization` | Düzen için gereken kimlik doğrulama bilgileri `Bearer` . |

**Örnek:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Azure AD belirteç alımı

Bir Azure AD belirteci almadan önce, hangi kullanıcının kimlik doğrulaması yapmasını istediğinizi, belirteç istediğiniz hedef kitleyi ve hangi Azure AD uç noktasını (yetkilisini) kullanacağınızı tanımlamalısınız.

### <a name="audience"></a>Hedef kitle

Azure AD belirtecini uygun bir hedef kitle ile isteyin. Azure Uygulama yapılandırması için aşağıdaki izleyicilerin birini kullanın. Hedef kitle, belirtecin istendiği *kaynak* olarak da adlandırılabilir.

- {configurationStoreName}. azconfig. IO
- *. azconfig.io

> [!IMPORTANT]
> İstenen hedef kitle olduğunda `{configurationStoreName}.azconfig.io` , `Host` isteği göndermek için kullanılan istek üst bilgisiyle (büyük/küçük harfe duyarlı) tam olarak eşleşmesi gerekir.

### <a name="azure-ad-authority"></a>Azure AD yetkilisi

Azure AD yetkilisi, bir Azure AD belirteci edinmek için kullandığınız uç noktadır. Bu biçimde olur `https://login.microsoftonline.com/{tenantId}` . `{tenantId}`Segment, kimlik doğrulamaya çalışan Kullanıcı veya uygulamanın ait olduğu Azure AD KIRACı kimliğini ifade eder.

### <a name="authentication-libraries"></a>Kimlik doğrulama kitaplıkları

Azure, bir Azure AD belirteci alma işlemini basitleştirmek için Azure Active Directory kimlik doğrulama kitaplıkları adlı bir kitaplık kümesi sağlar. Azure bu kitaplıkları birden çok dil için oluşturur. Daha fazla bilgi için [belgelere](../active-directory/azuread-dev/active-directory-authentication-libraries.md) bakın.

## <a name="errors"></a>Hatalar

Aşağıdaki hatalarla karşılaşabilirsiniz.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Neden:** Şemayla yetkilendirme isteği üst bilgisini belirtmediniz `Bearer` .

**Çözüm:** Geçerli bir `Authorization` http istek üst bilgisi sağlayın.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Neden:** Azure AD belirteci geçerli değil.

**Çözüm:** Azure AD yetkilisinden bir Azure AD belirteci alın ve uygun izleyiciyi kullandığınızdan emin olun.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Neden:** Azure AD belirteci geçerli değil.

**Çözüm:** Azure AD yetkilisinden bir Azure AD belirteci alın. Azure AD kiracının, yapılandırma deposunun ait olduğu abonelikle ilişkili bir tane olduğundan emin olun. Asıl öğe birden fazla Azure AD kiracısına aitse bu hata görünebilir.