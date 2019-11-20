---
title: Azure AD hizmetten hizmete kimlik doğrulaması OAuth 2.0 adına taslak belirtimi | Microsoft Docs
description: Bu makalede, OAuth 2.0 on-of-the Service-to-Service Flow ile hizmetten hizmete kimlik doğrulaması uygulamak için HTTP iletilerinin nasıl kullanılacağı açıklanır.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: accd14446ab8f4a70336e3bd6787cbd8c93ff21d
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891510"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Şirket adına temsilci Kullanıcı kimliği kullanan hizmetten hizmete çağrılar

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

OAuth 2,0 adına (OBO) akışı, bir hizmet veya Web API 'SI çağıran bir uygulamanın, Kullanıcı kimlik doğrulamasını başka bir hizmete veya Web API 'sine geçmesini sağlar. OBO akışı, temsilci kullanıcı kimliğini ve izinleri istek zinciri aracılığıyla yayar. Orta katman hizmetin, aşağı akış hizmetine kimliği doğrulanmış istekleri yapması için Kullanıcı adına Azure Active Directory (Azure AD) ' dan bir erişim belirtecinin güvenli hale getirme yapması gerekir.

> [!IMPORTANT]
> Mayıs 2018 itibariyle, `id_token` ' ın adına akış için kullanılamaz.  Tek sayfalı uygulamalar (maça 'Lar), OBO akışlarını gerçekleştirmek için orta katmanlı gizli bir istemciye erişim belirteci iletmelidir. Adına çağrı gerçekleştirebilen istemciler hakkında daha fazla ayrıntı için bkz. [sınırlamalar](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>Şirket adına akış diyagramı

OBO akışı, [OAuth 2,0 yetkilendirme kodu verme akışını](v1-protocols-oauth-code.md)kullanan bir uygulamada kullanıcının kimliği doğrulandıktan sonra başlar. Bu noktada, uygulama bir erişim belirteci (belirteç A), kullanıcının taleplerini ve A 'ya erişim iznini içeren orta katman Web API 'sine (API a) gönderir. Sonra, API A, aşağı akış Web API 'sine (API B) kimliği doğrulanmış bir istek yapar.

Bu adımlar, şirket adına akışı oluşturur: ![OAuth 2.0 'ın adına olan akışındaki adımları gösterir](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. İstemci uygulaması, a belirtecine sahip a API 'sine bir istek yapar.
1. API A, Azure AD belirteç verme uç noktasında kimlik doğrular ve API B 'ye erişmek için bir belirteç ister.
1. Azure AD belirteç verme uç noktası, API A 'nın belirteç A ile kimlik bilgilerini doğrular ve API B (belirteç B) için erişim belirtecini yayınlar.
1. API B 'ye yapılan istek yetkilendirme üstbilgisindeki B belirtecini içerir.
1. API B, güvenli kaynaktaki verileri döndürür.

>[!NOTE]
>Bir aşağı akış hizmeti için bir belirteç istemek için kullanılan bir erişim belirtecindeki hedef kitle talebi, OBO isteğini yapan hizmetin KIMLIĞI olmalıdır. Belirtecin Ayrıca, Azure Active Directory genel imzalama anahtarıyla imzalanması gerekir (Bu, portalda **uygulama kayıtları** ile kaydedilen uygulamalar için varsayılandır).

## <a name="register-the-application-and-service-in-azure-ad"></a>Uygulamayı ve hizmeti Azure AD 'ye kaydetme

Hem orta katman hizmeti hem de istemci uygulamasını Azure AD 'ye kaydedin.

### <a name="register-the-middle-tier-service"></a>Orta katman hizmetini kaydetme

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. En üstteki çubukta, hesabınızı seçin ve uygulamanız için Active Directory kiracı seçmek üzere **Dizin** listesi altına bakın.
1. Sol bölmede **diğer hizmetler** ' i seçin ve **Azure Active Directory**' yi seçin.
1. **Uygulama kayıtları** ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için kolay bir ad girin ve uygulama türü ' nü seçin.
1. **Desteklenen hesap türleri**altında, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.
1. Yeniden yönlendirme URI 'sini temel URL 'ye ayarlayın.
1. Uygulamayı kaydetmek için **Kaydet**'i seçin.
1. Azure portal çıkmadan önce bir istemci gizli dizisi oluşturun.
1. Azure portal, uygulamanızı seçin ve **gizli dizileri & sertifikalar**' ı seçin.
1. **Yeni istemci parolası** ' nı seçin ve süresi bir yıl ya da iki yıl olan bir parola ekleyin.
1. Bu sayfayı kaydettiğinizde Azure portal gizli değeri görüntüler. Gizli bir konumda gizli bir konuma kopyalayın ve kaydedin.

> [!IMPORTANT]
> Uygulamanızda uygulama ayarlarını yapılandırmak için gizli anahtar gerekir. Bu gizli dizi değeri bir daha görüntülenmez ve başka hiçbir şekilde alınabilir değildir. Azure portal görünür hale geldiğinde onu kaydedin.

### <a name="register-the-client-application"></a>İstemci uygulamasını kaydetme

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. En üstteki çubukta, hesabınızı seçin ve uygulamanız için Active Directory kiracı seçmek üzere **Dizin** listesi altına bakın.
1. Sol bölmede **diğer hizmetler** ' i seçin ve **Azure Active Directory**' yi seçin.
1. **Uygulama kayıtları** ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için kolay bir ad girin ve uygulama türü ' nü seçin.
1. **Desteklenen hesap türleri**altında, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.
1. Yeniden yönlendirme URI 'sini temel URL 'ye ayarlayın.
1. Uygulamayı kaydetmek için **Kaydet**'i seçin.
1. Uygulamanız için izinleri yapılandırın. **API izinleri**' nde, **izin Ekle** ve sonra **API 'lerim**' i seçin.
1. Metin alanına orta katman hizmetinin adını yazın.
1. **İzinleri seç** ' i seçin ve **ardından \<> Erişim hizmeti adı**' nı seçin.

### <a name="configure-known-client-applications"></a>Bilinen istemci uygulamalarını yapılandırma

Bu senaryoda, orta katman hizmetinin Kullanıcı etkileşimi olmadan aşağı akış API 'sine erişme iznini alması gerekir. Aşağı akış API 'sine erişim izni verme seçeneği, kimlik doğrulama sırasında izin adımının bir parçası olarak ön gösterilmelidir.

İstemci uygulamasının kaydını, orta katman hizmetinin kaydıyla Azure AD 'ye açıkça bağlamak için aşağıdaki adımları izleyin. Bu işlem hem istemci hem de orta katman için gereken onayı tek bir iletişim kutusuna birleştirir.

1. Orta katman hizmeti kaydına gidin ve bildirim düzenleyicisini açmak için **bildirim** ' ı seçin.
1. `knownClientApplications` Dizi özelliğini bulun ve istemci uygulamasının istemci kimliğini bir öğesi olarak ekleyin.
1. **Kaydet**' i seçerek bildirimi kaydedin.

## <a name="service-to-service-access-token-request"></a>Hizmetten hizmete erişim belirteci isteği

Erişim belirteci istemek için, kiracıya özgü Azure AD uç noktasında aşağıdaki parametrelerle bir HTTP GÖNDERISI yapın:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

İstemci uygulaması, paylaşılan bir gizli dizi ya da bir sertifika tarafından güvenli hale getirilir.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>İlk durum: Paylaşılan gizli dizi ile belirteç isteğine erişim

Paylaşılan bir gizli dizi kullanılırken hizmetten hizmete erişim belirteci isteği aşağıdaki parametreleri içerir:

| Parametre |  | Açıklama |
| --- | --- | --- |
| grant_type |gerekli | Belirteç isteğinin türü. OBO isteği bir JSON Web Token (JWT) kullanır, bu nedenle değer **urn: IETF: params: OAuth: Grant-Type: JWT-taşıyıcı**olmalıdır. |
| assertion |gerekli | İstekte kullanılan erişim belirtecinin değeri. |
| client_id |gerekli | Azure AD 'ye kayıt sırasında çağıran hizmete atanan uygulama KIMLIĞI. Azure portal uygulama KIMLIĞINI bulmak için **Active Directory**' i seçin, dizini seçin ve ardından uygulama adını seçin. |
| client_secret |gerekli | Azure AD 'de çağıran hizmet için kaydedilen anahtar. Bu değer kayıt sırasında belirtilmiştir. |
| resource |gerekli | Alıcı hizmetin uygulama KIMLIĞI URI 'SI (güvenli kaynak). Azure portal uygulama KIMLIĞI URI 'sini bulmak için **Active Directory** ' i seçin ve dizini seçin. Uygulama adı ' nı seçin, **Tüm ayarlar**' ı seçin ve ardından **Özellikler**' i seçin. |
| requested_token_use |gerekli | İsteğin nasıl işleneceğini belirtir. Adına akışta, değer **on_behalf_of**olmalıdır. |
| scope |gerekli | Belirteç isteği için bir alan ayrılmış kapsam listesi. OpenID Connect için, **OpenID** kapsamı belirtilmelidir.|

#### <a name="example"></a>Örnek

Aşağıdaki http post, https://graph.windows.net Web API 'si için bir erişim belirteci ister. , `client_id` Erişim belirtecini isteyen hizmeti belirler.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>İkinci durum: Bir sertifikayla erişim belirteci isteği

Bir sertifikaya sahip hizmetten hizmete erişim belirteci isteği aşağıdaki parametreleri içerir:

| Parametre |  | Açıklama |
| --- | --- | --- |
| grant_type |gerekli | Belirteç isteğinin türü. OBO isteği bir JWT erişim belirteci kullanır, bu yüzden değer **urn: IETF: params: OAuth: Grant-Type: JWT-taşıyıcı**olmalıdır. |
| assertion |gerekli | İstekte kullanılan belirtecin değeri. |
| client_id |gerekli | Azure AD 'ye kayıt sırasında çağıran hizmete atanan uygulama KIMLIĞI. Azure portal uygulama KIMLIĞINI bulmak için **Active Directory**' i seçin, dizini seçin ve ardından uygulama adını seçin. |
| client_assertion_type |gerekli |Değer şu şekilde olmalıdır`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |gerekli | Uygulamanız için kimlik bilgileri olarak kaydettiğiniz sertifikayla oluşturduğunuz ve oturum açarken kullanabileceğiniz bir JSON Web Token. Onaylama biçimi ve sertifikanızın nasıl kaydedileceği hakkında bilgi edinmek için bkz. [sertifika kimlik bilgileri](active-directory-certificate-credentials.md) .|
| resource |gerekli | Alıcı hizmetin uygulama KIMLIĞI URI 'SI (güvenli kaynak). Azure portal uygulama KIMLIĞI URI 'sini bulmak için **Active Directory** ' i seçin ve dizini seçin. Uygulama adı ' nı seçin, **Tüm ayarlar**' ı seçin ve ardından **Özellikler**' i seçin. |
| requested_token_use |gerekli | İsteğin nasıl işleneceğini belirtir. Adına akışta, değer **on_behalf_of**olmalıdır. |
| scope |gerekli | Belirteç isteği için bir alan ayrılmış kapsam listesi. OpenID Connect için, **OpenID** kapsamı belirtilmelidir.|

Bu parametreler, `client_secret parameter` paylaşılan gizli dizi ile aynı şekilde, iki parametre tarafından değiştirilmeleri dışında neredeyse aynıdır: `client_assertion_type` ve `client_assertion`.

#### <a name="example"></a>Örnek

Aşağıdaki http post, bir sertifika ile https://graph.windows.net Web API 'si için bir erişim belirteci ister. , `client_id` Erişim belirtecini isteyen hizmeti belirler.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Hizmetten hizmete erişim belirteci yanıtı

Başarılı bir yanıt, aşağıdaki parametrelere sahip bir JSON OAuth 2,0 yanıtdır:

| Parametre | Açıklama |
| --- | --- |
| token_type |Belirteç türü değerini gösterir. Azure AD 'nin desteklediği tek tür **taşıyıcı**. Taşıyıcı belirteçleri hakkında daha fazla bilgi için bkz [. OAuth 2,0 yetkilendirme çerçevesi: Taşıyıcı belirteç kullanımı (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Belirteçte izin verilen erişim kapsamı. |
| expires_in |Erişim belirtecinin geçerli olduğu sürenin uzunluğu (saniye cinsinden). |
| expires_on |Erişim belirtecinin süre sonu. Tarih, 1970-01-01T0:0: 0Z UTC 'den sona erme zamanına kadar saniye sayısı olarak gösterilir. Bu değer, önbelleğe alınmış belirteçlerin ömrünü belirlemede kullanılır. |
| resource |Alıcı hizmetin uygulama KIMLIĞI URI 'SI (güvenli kaynak). |
| access_token |İstenen erişim belirteci. Çağıran hizmet, bu belirteci, alıcı hizmette kimlik doğrulaması yapmak için kullanabilir. |
| id_token |İstenen KIMLIK belirteci. Çağıran hizmet, kullanıcının kimliğini doğrulamak ve kullanıcıyla oturum başlatmak için bu belirteci kullanabilir. |
| refresh_token |İstenen erişim belirtecinin yenileme belirteci. Çağıran hizmet, geçerli erişim belirtecinin süresi dolduktan sonra başka bir erişim belirteci istemek için bu belirteci kullanabilir. |

### <a name="success-response-example"></a>Başarı yanıtı örneği

Aşağıdaki örnek, https://graph.windows.net Web API 'si için bir erişim belirteci isteğine yönelik başarılı yanıtı gösterir.

```json
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Hata yanıtı örneği

Azure AD belirteç uç noktası, koşullu erişim ilkesiyle ayarlanmış bir aşağı akış API 'SI için erişim belirteci edinmeyi denediğinde bir hata yanıtı döndürür (örneğin, Multi-Factor Authentication). Orta katman hizmeti, istemci uygulamanın koşullu erişim ilkesini karşılamak üzere kullanıcı etkileşimini sağlayabilmesi için bu hatayı istemci uygulamasına sunmalıdır.

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Güvenli kaynağa erişmek için erişim belirtecini kullanma

Orta katman hizmeti, `Authorization` üstbilgideki belirteci ayarlayarak aşağı akış Web API 'sine kimliği doğrulanmış istekler yapmak için alınan erişim belirtecini kullanabilir.

### <a name="example"></a>Örnek

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>SAML onayları bir OAuth 2.0 OBO akışıyla alındı

Bazı OAuth tabanlı Web hizmetlerinin, etkileşimli olmayan akışlarda SAML onayları kabul eden diğer Web hizmeti API 'Lerine erişmesi gerekir. Azure Active Directory, SAML tabanlı bir Web hizmetini hedef kaynak olarak kullanan şirket adına bir akışa yanıt olarak bir SAML onayı sağlayabilir.

>[!NOTE]
>Bu, OAuth2 tabanlı bir uygulamanın SAML belirteçlerini kullanan Web hizmeti API uç noktalarına erişmesine olanak tanıyan, OAuth 2,0 için standart olmayan bir uzantıdır.

> [!TIP]
> Bir ön uç Web uygulamasından SAML korumalı bir Web hizmeti çağırdığınızda, API 'yi çağırabilir ve kullanıcının mevcut oturumunda normal bir etkileşimli kimlik doğrulama akışı başlatabilirsiniz. Yalnızca bir hizmetten hizmete çağrı, Kullanıcı bağlamı sağlamak için bir SAML belirteci gerektirdiğinde bir OBO akışı kullanmanız gerekir.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Paylaşılan gizli dizi ile OBO isteği kullanarak SAML belirteci alma

SAML onaylama işlemi için hizmetten hizmete yönelik bir istek aşağıdaki parametreleri içerir:

| Parametre |  | Açıklama |
| --- | --- | --- |
| grant_type |gerekli | Belirteç isteğinin türü. JWT kullanan bir istek için değer **urn: IETF: params: OAuth: Grant-Type: JWT-taşıyıcı**olmalıdır. |
| assertion |gerekli | İstekte kullanılan erişim belirtecinin değeri.|
| client_id |gerekli | Azure AD 'ye kayıt sırasında çağıran hizmete atanan uygulama KIMLIĞI. Azure portal uygulama KIMLIĞINI bulmak için **Active Directory**' i seçin, dizini seçin ve ardından uygulama adını seçin. |
| client_secret |gerekli | Azure AD 'de çağıran hizmet için kaydedilen anahtar. Bu değer kayıt sırasında belirtilmiştir. |
| resource |gerekli | Alıcı hizmetin uygulama KIMLIĞI URI 'SI (güvenli kaynak). Bu, SAML belirtecinin hedef kitlesi olacak kaynaktır. Azure portal uygulama KIMLIĞI URI 'sini bulmak için **Active Directory** ' i seçin ve dizini seçin. Uygulama adı ' nı seçin, **Tüm ayarlar**' ı seçin ve ardından **Özellikler**' i seçin. |
| requested_token_use |gerekli | İsteğin nasıl işleneceğini belirtir. Adına akışta, değer **on_behalf_of**olmalıdır. |
| requested_token_type | gerekli | İstenen belirtecin türünü belirtir. Değer **urn: IETF: params: OAuth: Token-Type: SAML2** veya **urn: IETF: params: OAuth: Token-Type: saml1 ile** erişilen kaynağın gereksinimlerine bağlı olarak değişebilir. |

Yanıt, UTF8 ve Base64url içinde kodlanmış bir SAML belirteci içeriyor.

- Bir **OBO çağrısından kaynaklanan BIR SAML onaylama işlemi Için SubjectConfirmationData**: Hedef uygulama **SubjectConfirmationData**'da bir alıcı değeri gerektiriyorsa, kaynak uygulama yapılandırmasındaki joker karakter olmayan bir yanıt URL 'si olmalıdır.
- **SubjectConfirmationData düğümü**: Bir SAML yanıtının parçası olmadığından düğüm bir **InResponseTo** özniteliği içeremez. SAML belirtecini alan uygulamanın, bir **InResponseTo** ÖZNITELIĞI olmadan SAML onaylama işlemi kabul edebilmesi gerekir.

- **Onay**: OAuth akışında Kullanıcı verilerini içeren bir SAML belirteci almak için onay verilmelidir. İzinler ve yönetici onayı alma hakkında daha fazla bilgi için, [Azure Active Directory v 1.0 uç noktasındaki izinler ve onay](https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent)konusuna bakın.

### <a name="response-with-saml-assertion"></a>SAML onaylama ile yanıt

| Parametre | Açıklama |
| --- | --- |
| token_type |Belirteç türü değerini gösterir. Azure AD 'nin desteklediği tek tür **taşıyıcı**. Taşıyıcı belirteçleri hakkında daha fazla bilgi için bkz [. OAuth 2,0 yetkilendirme çerçevesi: Taşıyıcı belirteç kullanımı (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Belirteçte izin verilen erişim kapsamı. |
| expires_in |Erişim belirtecinin geçerli olduğu sürenin uzunluğu (saniye cinsinden). |
| expires_on |Erişim belirtecinin süre sonu. Tarih, 1970-01-01T0:0: 0Z UTC 'den sona erme zamanına kadar saniye sayısı olarak gösterilir. Bu değer, önbelleğe alınmış belirteçlerin ömrünü belirlemede kullanılır. |
| resource |Alıcı hizmetin uygulama KIMLIĞI URI 'SI (güvenli kaynak). |
| access_token |SAML onaylama işlemi döndüren parametre. |
| refresh_token |Yenileme belirteci. Çağıran hizmet, geçerli SAML onaylama süresi dolduktan sonra başka bir erişim belirteci istemek için bu belirteci kullanabilir. |

- token_type: Taşıyıcı
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- Kaynak`https://api.contoso.com`
- access_token: \<SAML onaylama\>
- issued_token_type: urn: IETF: params: OAuth: Token-Type: SAML2
- refresh_token: \<Belirteci Yenile\>

## <a name="client-limitations"></a>İstemci sınırlamaları

Joker karakter yanıt URL 'leri olan ortak istemciler, `id_token` OBO akışları için kullanamaz. Ancak, genel istemcide kaydedilmiş bir joker karakter yeniden yönlendirme URI 'SI olsa bile gizli bir istemci, örtük verme akışı aracılığıyla alınan **erişim** belirteçlerini kullanmaya devam edebilir.

## <a name="next-steps"></a>Sonraki adımlar

OAuth 2,0 protokolü ve istemci kimlik bilgilerini kullanan hizmetten hizmete kimlik doğrulaması gerçekleştirmenin başka bir yolu hakkında daha fazla bilgi edinin:

* [Azure AD 'de OAuth 2,0 istemci kimlik bilgilerini kullanarak hizmetten hizmete kimlik doğrulamaya hizmet verme](v1-oauth2-client-creds-grant-flow.md)
* [Azure AD 'de OAuth 2,0](v1-protocols-oauth-code.md)
