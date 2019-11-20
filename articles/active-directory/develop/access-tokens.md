---
title: Microsoft Identity platform erişim belirteçleri başvurusu | Mavisi
description: Azure AD v 1.0 ve Microsoft Identity platform (v 2.0) uç noktaları tarafından yayılan erişim belirteçleri hakkında bilgi edinin.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34548b623317eff17bcbf5a7749cd411a44bd722
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73935852"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Microsoft Identity platform erişim belirteçleri

Erişim belirteçleri, istemcilerin Azure tarafından korunan API 'Leri güvenli bir şekilde çağırmasını sağlar. Microsoft Identity platform erişim belirteçleri, Azure tarafından imzalanmış olan [Jwts](https://tools.ietf.org/html/rfc7519), Base64 kodlamalı JSON nesneleridir. Belirtecin içeriği yalnızca kaynak için tasarlanan istemciler, erişim belirteçlerini donuk dizeler olarak kabul etmelidir. Geliştiriciler, doğrulama ve hata ayıklama amacıyla, [JWT.MS](https://jwt.ms)gibi bir siteyi kullanarak JWTs 'nin kodunu çözebilir. İstemciniz, çok sayıda protokol kullanarak v 1.0 uç noktasından veya v 2.0 uç noktasından bir erişim belirteci alabilir.

İstemciniz bir erişim belirteci istediğinde, Azure AD uygulamanızın tüketimi için erişim belirteci hakkında bazı meta veriler de döndürür. Bu bilgiler, erişim belirtecinin süre sonu süresini ve geçerli olduğu kapsamları içerir. Bu veriler, uygulamanızın erişim belirtecinin kendisini ayrıştırmasına gerek kalmadan erişim belirteçlerinin akıllı önbelleğe alınmasına izin verir.

Uygulamanız, istemcilerin erişim isteyebileceklerini belirten bir kaynaktır (Web API), erişim belirteçleri Kullanıcı, istemci, verenin, izinler ve daha fazlası gibi kimlik doğrulama ve yetkilendirme için yararlı bilgiler sağlar.

Bir kaynağın bir erişim belirtecinin içindeki talepleri nasıl doğrulayacağını ve kullandığını öğrenmek için aşağıdaki bölümlere bakın.

> [!IMPORTANT]
> Erişim belirteçleri, belirtecin *kitlelerine* göre oluşturulur ve bu, belirteçteki kapsamların sahibi olan uygulama anlamına gelir.  Bu, [uygulama `2` bildiriminde](reference-app-manifest.md#manifest-reference) `accessTokenAcceptedVersion` bir kaynak ayarının, v 1.0 uç noktasını çağıran bir istemcinin v 2.0 erişim belirteci almasına izin verir.  Benzer şekilde, bu, istemciniz için erişim belirteci [isteğe bağlı taleplerini](active-directory-optional-claims.md) değiştirmenin neden, MS Graph kaynağına ait olan `user.read`için bir belirteç istendiğinde alınan erişim belirtecini değiştirmez.  
> Aynı nedenden dolayı, istemci uygulamanızı bir kişisel hesapla (örneğin hotmail.com veya outlook.com) sınarken, istemciniz tarafından alınan erişim belirtecinin donuk bir dize olduğunu fark edebilirsiniz. Bunun nedeni, erişildiği kaynağın şifreli ve istemci tarafından anlaşılamadığından eski MSA (Microsoft hesabı) biletler istedi.

## <a name="sample-tokens"></a>Örnek belirteçler

v 1.0 ve v 2.0 belirteçleri benzer bir şekilde görünür ve aynı taleplerin çoğunu içerir. Burada her birine bir örnek verilmiştir.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Bu v 1.0 belirtecini [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd)içinde görüntüleyin.

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Bu v 2.0 belirtecini [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt)içinde görüntüleyin.

## <a name="claims-in-access-tokens"></a>Erişim belirteçlerinde talepler

JWTs üç parçaya ayrılır:

* **Üstbilgi** -belirtecin türü ve nasıl imzalanacağı hakkında bilgi dahil olmak üzere [belirtecin nasıl doğrulanacağı](#validating-tokens) hakkında bilgi sağlar.
* **Yük** -hizmetinize çağrı yapmaya çalışan Kullanıcı veya uygulamayla ilgili tüm önemli verileri içerir.
* **İmza** -belirteci doğrulamak için kullanılan ham malzemedir.

Her parça bir noktayla (`.`) ayrılır ve ayrı olarak Base64 kodlamalı olur.

Talepler yalnızca, doldurulması için bir değer varsa vardır. Bu nedenle, uygulamanız mevcut bir talep üzerine bir bağımlılık alamaz. Örnekler arasında `pwd_exp` (her kiracı parolaların süre sonu gerektirmez) veya `family_name` ([istemci kimlik bilgisi](v1-oauth2-client-creds-grant-flow.md) akışları, adları olmayan uygulamalar adına). Erişim belirteci doğrulama için kullanılan talepler her zaman mevcut olacaktır.

> [!NOTE]
> Bazı talepler, Azure AD güvenli belirteçlerinin yeniden kullanım durumunda kullanılmasına yardımcı olmak için kullanılır. Bunlar, açıklamada "donuk" olarak genel tüketim için değil olarak işaretlenir. Bu talepler bir belirteçte görünebilir veya görünmeyebilir ve bildirimde bulunulmadan yenilerini eklenebilir.

### <a name="header-claims"></a>Üst bilgi talepleri

|İste | Biçimlendir | Açıklama |
|--------|--------|-------------|
| `typ` | Dize-Always "JWT" | Belirtecin JWT olduğunu gösterir.|
| `nonce` | Dize | Belirteç yeniden yürütme saldırılarına karşı korumak için kullanılan benzersiz bir tanımlayıcı. Kaynağınız, yeniden oynacak şekilde korumak için bu değeri kaydedebilir. |
| `alg` | Dize | Belirteci imzalamak için kullanılan algoritmayı belirtir, örneğin, "RS256" |
| `kid` | Dize | Bu belirteci imzalamak için kullanılan ortak anahtar için parmak izini belirtir. Hem v 1.0 hem de v 2.0 erişim belirteçlerine yayılmıştır. |
| `x5t` | Dize | Aynı (kullanımda ve değerde) `kid`olarak çalışır. `x5t`, uyumluluk amacıyla yalnızca v 1.0 erişim belirteçlerine yayılan eski bir taleptür. |

### <a name="payload-claims"></a>Yük talepleri

| İste | Biçimlendir | Açıklama |
|-----|--------|-------------|
| `aud` | Dize, uygulama KIMLIĞI URI 'SI | Belirtecin amaçlanan alıcısını tanımlar. Kimlik belirteçlerinde hedef kitle, uygulamanızın Azure portal uygulamanıza atanan uygulama KIMLIĞIDIR. Uygulamanızın bu değeri doğrulaması ve değer eşleşmezse belirteci reddetmesi gerekir. |
| `iss` | Dize, STS URI 'SI | Belirteci oluşturan ve döndüren güvenlik belirteci hizmetini (STS) ve kullanıcının kimlik doğrulamasının bulunduğu Azure AD kiracısını tanımlar. Verilen belirteç bir v 2.0 belirteci ise (`ver` talebine bakın), URI `/v2.0`sona ermeyecektir. Kullanıcının bir Microsoft hesabı bir tüketici kullanıcısı olduğunu belirten GUID `9188040d-6c67-4c5b-b112-36a304b66dad`. Uygulamanız, varsa uygulamada oturum açmak için gereken kiracılar kümesini kısıtlamak için talebin GUID kısmını kullanmalıdır. |
|`idp`| Dize, genellikle STS URI 'SI | Belirtecin öznesinin kimliğini doğrulayan kimlik sağlayıcısını kaydeder. Bu değer, Kullanıcı hesabı veren konukiyle aynı kiracıda olmadığı sürece veren talebinin değeriyle aynıdır, örneğin. Talep yoksa, bunun yerine `iss` değerinin kullanılabileceği anlamına gelir.  Bir kuruluş bağlamında kullanılan kişisel hesaplar için (örneğin, bir Azure AD kiracısına davet edilen kişisel hesap), `idp` talebi ' live.com ' veya Microsoft hesabı kiracı `9188040d-6c67-4c5b-b112-36a304b66dad`içeren bir STS URI 'SI olabilir. |  
| `iat` | int, UNIX zaman damgası | "Çıkarılan", bu belirteç için kimlik doğrulamanın ne zaman oluştuğunu gösterir. |
| `nbf` | int, UNIX zaman damgası | "NBF" (before) talebi, JWT 'ın işlenmek üzere kabul edilmeden önce geçen süreyi tanımlar. |
| `exp` | int, UNIX zaman damgası | "Exp" (sona erme saati) talebi, JWT 'ın işlenmek üzere kabul edilmemelidir. Bir kaynağın bu süreden önce belirteci reddedebileceğini, örneğin, kimlik doğrulamasında yapılan bir değişikliğin gerekli olduğu veya bir belirteç iptalinin algılanabilmesi önemlidir. |
| `aio` | Donuk dize | Azure AD tarafından belirteç yeniden kullanımı için veri kaydetmek üzere kullanılan bir iç talep. Kaynaklar bu talebi kullanmamalıdır. |
| `acr` | Dize, "0" veya "1" | Yalnızca v 1.0 belirteçlerinde bulunur. "Kimlik doğrulama bağlamı sınıfı" talebi. "0" değeri, son kullanıcı kimlik doğrulamasının ISO/ıEC 29115 gereksinimlerini karşılamadığı anlamına gelir. |
| `amr` | Dizelerin JSON dizisi | Yalnızca v 1.0 belirteçlerinde bulunur. Belirtecin konusunun nasıl doğrulandığını tanımlar. Daha fazla bilgi için [AMR Claim bölümüne](#the-amr-claim) bakın. |
| `appid` | Dize, GUID | Yalnızca v 1.0 belirteçlerinde bulunur. Belirteci kullanan istemcinin uygulama KIMLIĞI. Uygulama kendi kendine veya bir kullanıcı adına çalışabilir. Uygulama KIMLIĞI genellikle bir uygulama nesnesini temsil eder, ancak Azure AD 'de bir hizmet sorumlusu nesnesini de temsil edebilir. |
| `appidacr` | "0", "1" veya "2" | Yalnızca v 1.0 belirteçlerinde bulunur. İstemcinin nasıl doğrulandığını gösterir. Ortak istemci için, değer "0" olur. İstemci KIMLIĞI ve istemci gizli anahtarı kullanılıyorsa, değer "1" olur. Kimlik doğrulaması için bir istemci sertifikası kullanılmışsa, değer "2" olur. |
| `azp` | Dize, GUID | Yalnızca v 2.0 belirteçlerinde bulunur, `appid`için bir değiştirme. Belirteci kullanan istemcinin uygulama KIMLIĞI. Uygulama kendi kendine veya bir kullanıcı adına çalışabilir. Uygulama KIMLIĞI genellikle bir uygulama nesnesini temsil eder, ancak Azure AD 'de bir hizmet sorumlusu nesnesini de temsil edebilir. |
| `azpacr` | "0", "1" veya "2" | Yalnızca v 2.0 belirteçlerinde bulunur, `appidacr`için bir değiştirme. İstemcinin nasıl doğrulandığını gösterir. Ortak istemci için, değer "0" olur. İstemci KIMLIĞI ve istemci gizli anahtarı kullanılıyorsa, değer "1" olur. Kimlik doğrulaması için bir istemci sertifikası kullanılmışsa, değer "2" olur. |
| `preferred_username` | Dize | Kullanıcıyı temsil eden birincil Kullanıcı adı. Belirtilen biçim olmadan bir e-posta adresi, telefon numarası veya genel Kullanıcı adı olabilir. Değeri değişebilir ve zaman içinde değişebilir. Değişebilir olduğundan, bu değer yetkilendirme kararları almak için kullanılmamalıdır.  Bu, Kullanıcı adı ipuçları için de kullanılabilir. Bu talebi alabilmesi için `profile` kapsamı gereklidir. |
| `name` | Dize | Belirtecin konusunu tanımlayan, okunabilir bir değer sağlar. Değerin benzersiz olması garanti edilmez, değişebilir ve yalnızca görüntüleme amacıyla kullanılmak üzere tasarlanmıştır. Bu talebi alabilmesi için `profile` kapsamı gereklidir. |
| `scp` | Dize, virgülle ayrılmış bir kapsam listesi | Uygulamanız tarafından sunulan, istemci uygulamasının istediği (ve aldığı) izin veren kapsamlar kümesi. Uygulamanız, bu kapsamların uygulamanız tarafından açığa çıkarılan geçerli olduğunu doğrulamalıdır ve bu kapsamların değerine göre yetkilendirme kararları verir. Yalnızca [Kullanıcı belirteçleri](#user-and-application-tokens)için dahil edilmiştir. |
| `roles` | Dizelerin dizisi, bir izin listesi | Uygulamanız tarafından kullanıma sunulan uygulamanın veya kullanıcının çağrı için izin verilen izinler kümesi. [Uygulama belirteçleri](#user-and-application-tokens)için, bu, Kullanıcı kapsamları yerine [istemci kimlik bilgileri](v1-oauth2-client-creds-grant-flow.md) akışı sırasında kullanılır.  [Kullanıcı belirteçleri](#user-and-application-tokens) için bu, kullanıcının hedef uygulamada atandığı rollerle doldurulur. |
| `wids` | [Roletemplateıd](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) GUID dizisi | Bu kullanıcıya atanan kiracı genelindeki rolleri, [yönetici rolleri sayfasında](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids)bulunan roller bölümünden gösterir.  Bu talep uygulama [bildiriminin](reference-app-manifest.md)`groupMembershipClaims` özelliği aracılığıyla uygulama başına temelinde yapılandırılır.  "All" veya "DirectoryRole" olarak ayarlanması gerekir.  Belirteç uzunluğu sorunları nedeniyle örtük akış aracılığıyla alınan belirteçlerde bulunmayabilir. |
| `groups` | GUID 'lerin JSON dizisi | Konunun grup üyeliklerini temsil eden nesne kimliklerini sağlar. Bu değerler benzersizdir (bkz. nesne KIMLIĞI) ve bir kaynağa erişmek için Yetkilendirmeyi zorlama gibi erişimi yönetmek için güvenli bir şekilde kullanılabilir. Gruplar talebine dahil olan gruplar, [uygulama bildiriminin](reference-app-manifest.md)`groupMembershipClaims` özelliği aracılığıyla uygulama başına temelinde yapılandırılır. Null değeri tüm grupları dışlayacak, "SecurityGroup" değeri yalnızca Active Directory güvenlik grubu üyeliklerini içerir ve "All" değeri hem güvenlik gruplarını hem de Office 365 dağıtım listelerini içerir. <br><br>Örtülü izin ile `groups` talebini kullanma hakkında daha fazla bilgi için aşağıdaki `hasgroups` talebine bakın. <br>Diğer akışlar için, kullanıcının sayısı bir sınırı (SAML için 150 200, JWT için) alıyorsa, Kullanıcı için Grup listesini içeren AAD Graf uç noktasını gösteren talep kaynaklarına fazla kullanım talebi eklenecektir. |
| `hasgroups` | Boole | Varsa, her zaman `true`, kullanıcının en az bir grupta olduğunu belirten. Tam gruplar talebi, URI parçasını URL uzunluğu sınırlarının ötesinde (Şu anda 6 veya daha fazla grup) genişletecek, örtük verme akışlarında JWTs için `groups` talebi yerine kullanılır. İstemcinin, kullanıcının gruplarını (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`) belirleyebilmek için grafiği kullanması gerektiğini belirtir. |
| `groups:src1` | JSON nesnesi | Sınırlı olmayan Belirteç istekleri için (bkz. `hasgroups` yukarıya) ancak belirteç için hala çok büyük, kullanıcının tam gruplar listesine bir bağlantı dahil edilir. `groups` talebi yerine yeni bir talep olarak SAML için, bir dağıtılmış talep olarak JWTs için. <br><br>**Örnek JWT değeri**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }` |
| `sub` | Dize, GUID | Belirtecin, uygulamanın kullanıcısı gibi bilgilerin hangi sorumluya ait olduğunu belirten sorumlu. Bu değer sabittir ve yeniden atanamaz veya tekrar kullanılamaz. Belirteç bir kaynağa erişmek için kullanıldığında ve veritabanı tablolarında anahtar olarak kullanılabilecek gibi, güvenli bir şekilde yetkilendirme denetimleri gerçekleştirmek için kullanılabilir. Konu her zaman Azure AD 'nin sorun verdiği belirteçlerde bulunduğundan, bu değeri genel amaçlı yetkilendirme sisteminde kullanmanızı öneririz. Bununla birlikte, bir ikili tanımlayıcı, belirli bir uygulama KIMLIĞI için benzersizdir. Bu nedenle, tek bir Kullanıcı iki farklı istemci kimliği kullanarak iki farklı uygulama üzerinde oturum açarsa, bu uygulamalar konu talebi için iki farklı değer alır. Bu, mimarinize ve gizlilik gereksinimlerinize bağlı olarak istenebilir veya istenmeyebilir. Ayrıca bkz. `oid` talebi (bir Kiracıdaki uygulamalarda aynı olmaya devam eder). |
| `oid` | Dize, GUID | Bu örnekte, bir kullanıcı hesabı olan Microsoft Identity platformunda bir nesne için sabit tanımlayıcı. Ayrıca, kimlik doğrulama denetimlerini güvenli bir şekilde ve veritabanı tablolarında anahtar olarak gerçekleştirmek için de kullanılabilir. Bu KIMLIK, kullanıcıyı uygulamalar arasında benzersiz şekilde tanımlar. aynı kullanıcı için iki farklı uygulama oturum açma, `oid` talebinde aynı değeri alacaktır. Bu nedenle, Microsoft Graph gibi Microsoft çevrimiçi hizmetler sorguları yaparken `oid` kullanılabilir. Microsoft Graph, bu KIMLIĞI belirli bir [Kullanıcı hesabı](/graph/api/resources/user)için `id` özelliği olarak döndürür. `oid` birden çok uygulamanın kullanıcıları ilişkilendirme yapmasına izin verdiğinden, bu talebi alabilmesi için `profile` kapsamı gereklidir. Tek bir Kullanıcı birden fazla kiracıda varsa, kullanıcının her kiracıda farklı bir nesne KIMLIĞI içerdiğini ve Kullanıcı aynı kimlik bilgilerine sahip her hesapta oturum açtığı halde farklı hesaplar olarak kabul edileceğini unutmayın. |
| `tid` | Dize, GUID | Kullanıcının ait olduğu Azure AD kiracısını temsil eder. İş ve okul hesapları için GUID, kullanıcının ait olduğu kuruluşun sabit kiracı KIMLIĞIDIR. Kişisel hesaplar için değer `9188040d-6c67-4c5b-b112-36a304b66dad`. Bu talebi alabilmesi için `profile` kapsamı gereklidir. |
| `unique_name` | Dize | Yalnızca v 1.0 belirteçlerinde bulunur. Belirtecin konusunu tanımlayan ve okunabilir bir değer sunar. Bu değerin kiracı içinde benzersiz olması garanti edilmez ve yalnızca görüntüleme amacıyla kullanılmalıdır. |
| `uti` | Donuk dize | Belirteçleri yeniden doğrulamak için Azure tarafından kullanılan bir iç talep. Kaynaklar bu talebi kullanmaz. |
| `rh` | Donuk dize | Belirteçleri yeniden doğrulamak için Azure tarafından kullanılan bir iç talep. Kaynaklar bu talebi kullanmamalıdır. |
| `ver` | Dize, `1.0` ya da `2.0` | Erişim belirtecinin sürümünü gösterir. |


> [!NOTE]
> **Gruplar fazla kullanım talebi**
>
> Belirteç boyutunun HTTP üst bilgi boyutu sınırlarını aşmadığından emin olmak için, Azure AD, gruplar talebinde içerdiği nesne kimliklerinin sayısını sınırlar. Bir Kullanıcı fazla kullanım sınırından daha fazla grup üyesiyse (SAML belirteçleri için 150, JWT belirteçleri için 200), Azure AD, grup talebini belirteçte göstermez. Bunun yerine, belirteçte kullanıcının grup üyeliğini almak üzere Graph API sorgulamak üzere uygulamayı gösteren bir fazla kullanım talebi içerir.
  ```csharp
  {
    ...
    "_claim_names": {
     "groups": "src1"
      },
      {
    "_claim_sources": {
      "src1": {
          "endpoint":"[Graph Url to get this user's group membership from]"
          }
         }
       }
    ...
   }
   ```
> Fazla kullanım senaryolarını sınamaya yardımcı olması için [uygulama oluşturma betikleri](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims/blob/master/AppCreationScripts/) klasöründe sağlanan `BulkCreateGroups.ps1` kullanabilirsiniz.

#### <a name="v10-basic-claims"></a>v 1.0 temel talepler

Aşağıdaki talepler varsa v 1.0 belirteçlerine dahil edilir, ancak varsayılan olarak v 2.0 belirteçlerine dahil edilmez. V 2.0 kullanıyorsanız ve bu taleplerden birine ihtiyacınız varsa, bunları [isteğe bağlı talepler](active-directory-optional-claims.md)kullanarak isteyin.

| İste | Biçimlendir | Açıklama |
|-----|--------|-------------|
| `ipaddr`| Dize | Kullanıcının kimliği doğrulanan IP adresi. |
| `onprem_sid`| Dize, [SID biçiminde](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | Kullanıcının şirket içi kimlik doğrulamasına sahip olduğu durumlarda, bu talep SID 'leri sağlar. Eski uygulamalarda yetkilendirme için `onprem_sid` kullanabilirsiniz.|
| `pwd_exp`| int, UNIX zaman damgası | Kullanıcının parolasının süresinin dolacağını gösterir. |
| `pwd_url`| Dize | Parolalarını sıfırlamak için kullanıcıların gönderilebileceği bir URL. |
| `in_corp`| boole | İstemci şirket ağından oturum açıyorsanız bildirir. Aksi takdirde talep dahil edilmez. |
| `nickname`| Dize | Kullanıcı için, adı ilk veya soyadı olan ek bir ad.|
| `family_name` | Dize | Kullanıcı nesnesinde tanımlandığı şekilde kullanıcının soyadı, soyadı veya aile adını sağlar. |
| `given_name` | Dize | Kullanıcı nesnesinde ayarlandığı gibi, kullanıcının adını veya verilen adı sağlar. |
| `upn` | Dize | Kullanıcının Kullanıcı adı. Telefon numarası, e-posta adresi veya biçimlendirilmemiş dize olabilir. Yalnızca görüntüleme amacıyla ve yeniden kimlik doğrulama senaryolarında Kullanıcı adı ipuçları sağlamak için kullanılmalıdır. |

#### <a name="the-amr-claim"></a>`amr` talebi

Microsoft kimlikleri, uygulamanız için uygun olabilecek farklı yollarla kimlik doğrulaması yapabilir. `amr` talebi, hem parola hem de Authenticator uygulaması kullanan bir kimlik doğrulaması için `["mfa", "rsa", "pwd"]`gibi birden çok öğe içerebilen bir dizidir.

| Değer | Açıklama |
|-----|-------------|
| `pwd` | Parola kimlik doğrulaması, kullanıcının Microsoft parolası veya bir uygulamanın istemci gizli anahtarı. |
| `rsa` | Kimlik doğrulaması, örneğin [Microsoft Authenticator uygulamasına](https://aka.ms/AA2kvvu)sahıp bir RSA anahtarı kanıtını temel alır. Bu, kimlik doğrulamasının, hizmetin sahip olduğu x509 sertifikası ile kendinden imzalı bir JWT tarafından gerçekleştirilip yapılmadığını içerir. |
| `otp` | Bir e-posta veya kısa mesaj kullanarak bir kerelik geçiş kodu. |
| `fed` | Bir Federasyon kimlik doğrulama onayı (JWT veya SAML gibi) kullanıldı. |
| `wia` | Windows tümleşik kimlik doğrulaması |
| `mfa` | Multi-Factor Authentication kullanıldı. Bu mevcut olduğunda diğer kimlik doğrulama yöntemleri de dahil edilir. |
| `ngcmfa` | Belirli Gelişmiş kimlik bilgisi türlerini sağlamak için kullanılan `mfa`eşdeğerdir. |
| `wiaormfa`| Kullanıcı, kimlik doğrulamak için Windows veya MFA kimlik bilgilerini kullandı. |
| `none` | Kimlik doğrulaması yapılmadı. |

## <a name="validating-tokens"></a>Belirteçleri doğrulama

Bir id_token veya access_token doğrulamak için, uygulamanızın hem belirtecin imzasını hem de taleplerini doğrulaması gerekir. Erişim belirteçlerini doğrulamak için, uygulamanız sertifikayı veren, seyirci ve imzalama belirteçlerini de doğrulamalıdır. Bu, OpenID keşif belgesindeki değerlere karşı doğrulanması gerekir. Örneğin, belgenin kiracı bağımsız sürümü [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration)konumunda bulunur.

Azure AD ara yazılımı, erişim belirteçlerini doğrulamaya yönelik yerleşik yeteneklere sahiptir ve seçtiğiniz dilde bir tane bulmak için [örneklerimize](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) göz atabilirsiniz. Bir JWT belirtecinin açıkça nasıl doğrulanacağı hakkında daha fazla bilgi için [El Ile JWT doğrulama örneğine](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation)bakın.

Belirteç doğrulamanın nasıl kolayca işleneceğini gösteren kitaplıklar ve kod örnekleri sağlıyoruz. Aşağıdaki bilgiler, temeldeki işlemi anlamak isteyen kişiler için verilmiştir. Ayrıca, JWT doğrulaması için kullanılabilen birkaç üçüncü taraf açık kaynak kitaplığı vardır. neredeyse her platform için en az bir seçenek ve burada dil vardır. Azure AD kimlik doğrulama kitaplıkları ve kod örnekleri hakkında daha fazla bilgi için bkz. [v 1.0 kimlik doğrulama kitaplıkları](active-directory-authentication-libraries.md) ve [v 2.0 kimlik doğrulama kitaplıkları](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>İmza doğrulanıyor

JWT, `.` karakteriyle ayrılmış üç kesim içerir. İlk kesim **başlık**, **gövde**olarak, ikinci ise **imza**olarak bilinir. İmza segmenti, uygulamanızın güvenilebilmesi için belirtecin orijinalliğini doğrulamak üzere kullanılabilir.

Azure AD tarafından verilen belirteçler, RSA 256 gibi sektör standardı asimetrik şifreleme algoritmaları kullanılarak imzalanır. JWT üst bilgisi, belirteci imzalamak için kullanılan anahtar ve şifreleme yöntemiyle ilgili bilgiler içerir:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg` talebi, belirteci imzalamak için kullanılan algoritmayı belirtir, ancak `kid` talebi belirteci doğrulamak için kullanılan belirli ortak anahtarı gösterir.

Azure AD, belirli bir zamanda belirli bir genel-özel anahtar çiftleri kümesinden birini kullanarak bir id_token imzalayabilir. Azure AD, olası anahtar kümesini düzenli aralıklarla döndürür, bu nedenle uygulamanızın bu anahtar değişikliklerini otomatik olarak işleyecek şekilde yazılması gerekir. Azure AD tarafından kullanılan ortak anahtarlarda güncelleştirmelerin her 24 saatte bir denetlenmesi için makul bir sıklık vardır.

İmzayı doğrulamak için gereken imzalama anahtarı verilerini, şurada bulunan [OpenID Connect meta veri belgesini](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) kullanarak elde edebilirsiniz:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Bu [URL 'yi](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) bir tarayıcıda deneyin!

Bu meta veri belgesi:

* , OpenID Connect kimlik doğrulaması yapmak için gereken çeşitli uç noktaların konumu gibi çeşitli yararlı bilgi parçalarını içeren bir JSON nesnesidir.
* Belirteçleri imzalamak için kullanılan ortak anahtar kümesinin konumunu veren bir `jwks_uri`içerir. `jwks_uri` bulunan JSON Web anahtarı (JWK), zaman içinde o anda kullanılan tüm ortak anahtar bilgilerini içerir.  JWK biçimi, [RFC 7517](https://tools.ietf.org/html/rfc7517)' de açıklanmaktadır.  Uygulamanız, bu belgedeki hangi ortak anahtarın belirli bir belirteci imzalamak için kullanıldığını seçmek üzere JWT üstbilgisindeki `kid` talebini kullanabilir. Daha sonra doğru ortak anahtarı ve belirtilen algoritmayı kullanarak imza doğrulaması yapabilir.

> [!NOTE]
> V 1.0 uç noktası hem `x5t` hem de `kid` taleplerini döndürür, ancak v 2.0 uç noktası yalnızca `kid` talep ile yanıt verir. İleri giderek, belirtecinizi doğrulamak için `kid` talebi kullanmanızı öneririz.

İmza doğrulama işlemi, bu belgenin kapsamı dışındadır. gerekirse, bunu yapmanıza yardımcı olmak için kullanılabilecek çok sayıda açık kaynak kitaplığı vardır.  Ancak, Microsoft Identity platformunda standart özel imzalama anahtarlarına tek bir belirteç imzalama uzantısı vardır.  

Uygulamanızda, [talep eşleme](active-directory-claims-mapping.md) özelliğini kullanmanın bir sonucu olarak özel İmzalama anahtarları varsa, uygulamanızın doğrulama için kullanılması gereken imzalama anahtarı bilgilerine işaret eden bir `jwks_uri` almak IÇIN uygulama kimliğini içeren `appid` bir sorgu parametresi eklemeniz gerekir. Örneğin: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e``jwks_uri` içerir.

### <a name="claims-based-authorization"></a>Talep tabanlı yetkilendirme

Uygulamanızın iş mantığı bu adımı dikte edecek, bazı yaygın yetkilendirme yöntemleri aşağıda düzenlenmiştir.

* Tüm mevcut kapsamların API 'niz tarafından sunulanlarla eşleştiğini doğrulamak ve istemcinin istenen eylemi yapmasına izin vermek için `scp` veya `roles` talebini denetleyin.
* Çağıran istemcinin `appid` talebini kullanarak API 'nizi çağırmasının izin verildiğinden emin olun.
* `appidacr` kullanarak çağıran istemcinin kimlik doğrulama durumunu doğrulama-genel istemcilerin API 'nizi çağırmaya izin verilmiyorsa 0 olmaması gerekir.
* Belirtecin yeniden çalındığını doğrulamak için geçmiş `nonce` talepler listesine karşı denetleyin.
* `tid`, API 'nizi çağırmaya izin verilen bir kiracı ile eşleştiğinden emin olun.
* Kullanıcının MFA gerçekleştirdi olduğunu doğrulamak için `acr` talebini kullanın. Bu, [koşullu erişim](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)kullanılarak zorunlu kılınmalıdır.
* Erişim belirtecinde `roles` veya `groups` talepleri istemiş olmanız durumunda, kullanıcının bu eylemi yapmasına izin verilen grupta olduğunu doğrulayın.
  * Örtük akış kullanılarak alınan belirteçler için, büyük olasılıkla belirtece sığamayacak kadar büyük olduğu için bu verilerin [Microsoft Graph](https://developer.microsoft.com/graph/) sorgulanmak zorunda olacaksınız.

## <a name="user-and-application-tokens"></a>Kullanıcı ve uygulama belirteçleri

Uygulamanız, bir kullanıcı adına (normal akış) veya doğrudan bir uygulamadan ( [istemci kimlik bilgileri akışı](v1-oauth2-client-creds-grant-flow.md)aracılığıyla) belirteç alabilir. Bu yalnızca uygulama belirteçleri, bu çağrının bir uygulamadan geldiğini ve bunu yedekleyen bir kullanıcıya sahip olmadığını gösterir. Bu belirteçler büyük ölçüde aynı şekilde işlenir ve bazı farklılıklar vardır:

* Yalnızca uygulama belirteçlerinin bir `scp` talebi olmayacaktır ve bunun yerine `roles` talebi olabilir. Bu, uygulama izninin (temsilci izinlerin aksine) kaydedileceği yerdir. Temsilci ve uygulama izinleri hakkında daha fazla bilgi için, bkz. [v 1.0](v1-permissions-and-consent.md) ve [v 2.0](v2-permissions-and-consent.md)'da izin ve onay.
* `name` veya `upn`gibi insana özgü birçok talep eksik olacaktır.
* `sub` ve `oid` talepleri aynı olacaktır. 

## <a name="token-revocation"></a>Belirteç iptali

Farklı nedenlerle yenileme belirteçleri herhangi bir zamanda geçersiz kılınabilir veya iptal edilebilir. Bunlar iki ana kategoriye ayrılır: zaman aşımları ve iptal edilecek.

### <a name="token-timeouts"></a>Belirteç zaman aşımları

* Maxınactivetime: yenileme belirteci, Maxınactivetime tarafından dikte edilen süre içinde kullanılmıyorsa, yenileme belirteci artık geçerli olmayacaktır.
* MaxSessionAge: MaxAgeSessionMultiFactor veya Maxagesessionsingsolaktör, varsayılanlarını (iptal edilene kadar) farklı bir değere ayarlandıysa, MaxAgeSession * geçtiğinde ayarlanan süre sonunda yeniden kimlik doğrulaması gerekecektir.
* Örnekler:
  * Kiracının beş gün boyunca bir Maxınactivetime vardır ve Kullanıcı bir hafta boyunca tatilden oluşur ve Azure AD, kullanıcıdan 7 gün içinde yeni bir belirteç isteği görmemiştir. Kullanıcı yeni bir belirteç istediğinde yenileme belirtecinin iptal edildiğini bulacak ve kimlik bilgilerini tekrar girmeleri gerekir.
  * Hassas bir uygulamanın bir gün için Maxagesessionsinglefaktör vardır. Bir kullanıcı Pazartesi günü oturum açarsa ve Salı günü (25 saat geçtikten sonra), yeniden kimlik doğrulaması yapmanız gerekir.

### <a name="revocation"></a>Sayılabilir

|   | Parola tabanlı tanımlama bilgisi | Parola tabanlı belirteç | Parola tabanlı olmayan tanımlama bilgisi | Parola tabanlı olmayan belirteç | Gizli istemci belirteci |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Parola süre sonu | Canlı kalır | Canlı kalır | Canlı kalır | Canlı kalır | Canlı kalır |
| Parola Kullanıcı tarafından değiştirildi | İptal Edildi | İptal Edildi | Canlı kalır | Canlı kalır | Canlı kalır |
| Kullanıcı SSPR | İptal Edildi | İptal Edildi | Canlı kalır | Canlı kalır | Canlı kalır |
| Yönetici parolayı sıfırlar | İptal Edildi | İptal Edildi | Canlı kalır | Canlı kalır | Canlı kalır |
| Kullanıcı, [PowerShell aracılığıyla](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) yenileme belirteçlerini iptal eder | İptal Edildi | İptal Edildi | İptal Edildi | İptal Edildi | İptal Edildi |
| Yönetici, kiracı için tüm yenileme belirteçlerini [PowerShell aracılığıyla](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) iptal eder | İptal Edildi | İptal Edildi |İptal Edildi | İptal Edildi | İptal Edildi |
| Web 'de [Çoklu oturum](v1-protocols-openid-connect-code.md#single-sign-out) kapatma | İptal Edildi | Canlı kalır | İptal Edildi | Canlı kalır | Canlı kalır |

> [!NOTE]
> "Parola tabanlı olmayan" bir oturum açma, kullanıcının bunu almak için bir parola yazmamış olduğu bir paroladır. Örneğin, Windows Hello, FIDO2 anahtarı veya PIN ile yüzünüzü kullanma.
>
> Windows 10 ' da birincil yenileme belirteçleri (PRT), kimlik bilgilerine göre ayrılmış olarak dağıtılır. Örneğin, Windows Hello ve Password kendilerine ait olan Prkalar varsa, bunlardan yalıtılmıştır. Bir Kullanıcı bir Hello kimlik bilgileri (PIN veya biyometri) ile oturum açtığında ve parolayı değiştirdiğinde, daha önce edinilen parola tabanlı PRT iptal edilir. Parola ile yeniden oturum açmak eski PRT 'yi geçersiz kılar ve yeni bir tane ister.
>
> Yeni bir erişim belirteci getirmek ve belirteci yenilemek için kullanıldığında, yenileme belirteçleri geçersiz kılınmaz veya iptal edilmez.  

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD 'de`id_tokens`](id-tokens.md)hakkında bilgi edinin.
* [V 1.0](v1-permissions-and-consent.md) ve [v 2.0](v2-permissions-and-consent.md)'da izin ve onay hakkında bilgi edinin.
