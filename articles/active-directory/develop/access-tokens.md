---
title: Microsoft Identity platform erişim belirteçleri | Mavisi
titleSuffix: Microsoft identity platform
description: Azure AD v 1.0 ve Microsoft Identity platform (v 2.0) uç noktaları tarafından yayılan erişim belirteçleri hakkında bilgi edinin.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2021
ms.author: hirsin
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: f5a60f14799e872d835d651fc043edd27dfc6990
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105604"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Microsoft Identity platform erişim belirteçleri

Erişim belirteçleri, istemcilerin korumalı Web API 'Lerini güvenli bir şekilde çağırmasını sağlar ve Web API 'Leri tarafından kimlik doğrulama ve yetkilendirme gerçekleştirmek için kullanılır. OAuth belirtimine göre, erişim belirteçleri bir küme biçimi olmadan donuk dizelerdir; bazı kimlik sağlayıcıları (IDPs) GUID 'Leri kullanır, diğerleri şifreli blob 'ları kullanır. Microsoft Identity platformu, belirteci kabul eden API yapılandırmasına bağlı olarak çeşitli erişim belirteci biçimlerini kullanır. Microsoft Identity platformunda [geliştiriciler tarafından kaydedilen özel API 'ler](quickstart-configure-app-expose-web-apis.md) , "v1" ve "v2" adlı ıkı farklı JSON Web belirteçleri (JWTs) arasından seçim yapabilir ve Azure 'daki Microsoft Graph veya API 'Ler gibi Microsoft tarafından geliştirilen API 'ler ek özel belirteç biçimlerine sahiptir. Bu mülkiyet biçimleri şifreli belirteçler, JWTs veya doğrulanmayacak özel JWT benzeri belirteçler olabilir.

Belirtecin içeriği yalnızca kaynak (API) için tasarlanan istemciler, erişim belirteçlerini donuk dizeler olarak değerlendirmelidir. *Yalnızca* doğrulama ve hata ayıklama amacıyla geliştiriciler, [JWT.MS](https://jwt.ms)gibi bir siteyi kullanarak jwts 'nin kodunu çözebilir. Ancak, bir Microsoft API 'SI için aldığınız belirteçlerin her zaman bir JWT olmaması ve bunları her zaman çözemeyebilirsiniz.

Erişim belirtecinin içindeki özellikler hakkında daha fazla bilgi için, istemcileri istemcinize erişim belirteciyle döndürülen belirteç yanıt verilerini kullanmalıdır. İstemciniz bir erişim belirteci istediğinde, Microsoft Identity platform uygulamanızın tüketimine yönelik erişim belirteci hakkında bazı meta veriler de döndürür. Bu bilgiler, erişim belirtecinin süre sonu süresini ve geçerli olduğu kapsamları içerir. Bu veriler, uygulamanızın erişim belirtecinin kendisini ayrıştırmasına gerek kalmadan erişim belirteçlerinin akıllı önbelleğe alınmasına izin verir.

API 'nizin bir erişim belirtecinin içindeki talepleri nasıl doğrulayacağını ve kullandığını öğrenmek için aşağıdaki bölümlere bakın.  

> [!NOTE]
> Not Bu sayfadaki tüm belgeler, aksi belirtilmedikçe, yalnızca kaydettiğiniz API 'Ler için verilen belirteçler için geçerlidir.  Microsoft 'a ait API 'Ler için verilen belirteçler için geçerlidir veya Microsoft Identity platformunun oluşturduğunuz bir API için belirteçleri nasıl yayınlayacağını doğrulamak için bu belirteçleri kullanabilirsiniz.  

## <a name="token-formats-and-ownership"></a>Belirteç biçimleri ve sahiplik

### <a name="v10-and-v20"></a>v 1.0 ve v 2.0 

Microsoft Identity platformunda kullanılabilen iki erişim belirteçleri sürümü vardır: v 1.0 ve v 2.0.  Bu sürümler, bir Web API 'sinin belirteçlerin nasıl göründüğünü denetleyebilmesini sağlamak için, belirteçte talepleri yönetir. Web API 'Leri, yalnızca Azure AD uygulamaları için kayıt-v 1.0 ve tüketici hesaplarını destekleyen uygulamalar için v 2.0 için varsayılan olarak bu seçilenden birine sahiptir.  Bu, `accessTokenAcceptedVersion` [uygulama bildirimindeki](reference-app-manifest.md#manifest-reference)ayarı kullanan uygulamalar tarafından denetlenebilir, burada `null` ve `1` v 1.0 belirteçleriyle sonuçlanır ve `2` v 2.0 belirteçleriyle sonuçlanır.

### <a name="what-app-is-a-token-for"></a>Hangi uygulama "için" belirteç?

Bir erişim belirteci isteğinde yer alan iki taraf vardır: istemci, belirteci isteyen, ve API çağrıldığında belirteci kabul eden kaynak (API). `aud`Bir belirteçteki talep, belirtecin hedeflenen kaynağını gösterir (onun *hedef kitlesi*). İstemciler belirteci kullanır, ancak bunu anlamamalıdır veya ayrıştırmaya çalışır. Kaynaklar belirteci kabul eder.  

Microsoft Identity platformu herhangi bir sürüm uç noktasından herhangi bir belirteç sürümünün verilmesini destekler; bunlar ilgili değildir. Bu, bir kaynak ayarının, `accessTokenAcceptedVersion` Bu `2` API için bir belirteç almak üzere v 1.0 uç noktasını çağıran bir istemcinin, bir v 2.0 erişim belirteci alacağı anlamına gelir.  Kaynaklar her zaman belirteçlerini ( `aud` talepleri olan) ve belirteç ayrıntılarını değiştirebilmeleri gereken tek uygulamalardır. Bu, *istemciniz* için erişim belirteci [isteğe bağlı taleplerini](active-directory-optional-claims.md) değiştirmenin neden `user.read` , Microsoft Graph kaynağına ait olan için bir belirteç istendiğinde alınan erişim belirtecini değiştirmez.

### <a name="sample-tokens"></a>Örnek belirteçler

v 1.0 ve v 2.0 belirteçleri benzer bir şekilde görünür ve aynı taleplerin çoğunu içerir. Burada her birine bir örnek verilmiştir. Bu örnek belirteçler, ancak anahtarlar yayından önce döndürüldüğünde ve kişisel bilgiler bundan kaldırıldıktan sonra [doğrulanmaz](#validating-tokens).

#### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Bu v 1.0 belirtecini [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd)içinde görüntüleyin.

#### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Bu v 2.0 belirtecini [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt)içinde görüntüleyin.

## <a name="claims-in-access-tokens"></a>Erişim belirteçlerinde talepler

JWTs (JSON Web belirteçleri) üç parçaya ayrılır:

* **Üstbilgi** -belirtecin türü ve nasıl imzalanacağı hakkında bilgi dahil olmak üzere [belirtecin nasıl doğrulanacağı](#validating-tokens) hakkında bilgi sağlar.
* **Yük** -hizmetinize çağrı yapmaya çalışan Kullanıcı veya uygulamayla ilgili tüm önemli verileri içerir.
* **İmza** -belirteci doğrulamak için kullanılan ham malzemedir.

Her parça bir noktayla ( `.` ) ayrılır ve ayrı olarak Base64 kodlamalı olarak kodlanır.

Talepler yalnızca, doldurulması için bir değer varsa vardır. Uygulamanız, var olan bir talep üzerinde bir bağımlılık alamaz. Örnekler arasında `pwd_exp` (her kiracı parolaların kullanım süresini dolacak) ve `family_name` ([istemci kimlik bilgisi](v2-oauth2-client-creds-grant-flow.md) akışları, adları olmayan uygulamalar adına). Erişim belirteci doğrulama için kullanılan talepler her zaman mevcut olacaktır.

Bazı talepler, Azure AD güvenli belirteçlerinin yeniden kullanım durumunda kullanılmasına yardımcı olmak için kullanılır. Bunlar, açıklamada "donuk" olarak genel tüketim için değil olarak işaretlenir. Bu talepler bir belirteçte görünebilir veya görünmeyebilir ve bildirimde bulunulmadan yenilerini eklenebilir.

### <a name="header-claims"></a>Üst bilgi talepleri

|İste | Biçimlendir | Açıklama |
|--------|--------|-------------|
| `typ` | Dize-Always "JWT" | Belirtecin JWT olduğunu gösterir.|
| `nonce` | Dize | Belirteç yeniden yürütme saldırılarına karşı korumak için kullanılan benzersiz bir tanımlayıcı. Kaynağınız, yeniden oynacak şekilde korumak için bu değeri kaydedebilir. |
| `alg` | Dize | Belirteci imzalamak için kullanılan algoritmayı belirtir, örneğin, "RS256" |
| `kid` | Dize | Bu belirteci imzalamak için kullanılan ortak anahtar için parmak izini belirtir. Hem v 1.0 hem de v 2.0 erişim belirteçlerine yayılmıştır. |
| `x5t` | Dize | Aynı (kullanımda ve değerde) olarak çalışır `kid` . `x5t` , uyumluluk amacıyla yalnızca v 1.0 erişim belirteçlerine yayılan eski bir taleptür. |

### <a name="payload-claims"></a>Yük talepleri

| İste | Biçimlendir | Açıklama |
|-----|--------|-------------|
| `aud` | Dize, uygulama KIMLIĞI URI 'SI veya GUID | Belirteç kitlesinin amaçlanan alıcısını tanımlar.  API 'nizin bu değeri doğrulaması ve değer eşleşmezse belirteci reddetmesi gerekir. V 2.0 belirteçlerinde, bu her zaman API 'nin istemci KIMLIĞIDIR, v 1.0 belirteçlerinde, istemcinin belirteci isteme şekline bağlı olarak istekte kullanılan istemci KIMLIĞI veya Kaynak URI 'SI olabilir.|
| `iss` | Dize, STS URI 'SI | Belirteci oluşturan ve döndüren güvenlik belirteci hizmetini (STS) ve kullanıcının kimlik doğrulamasının bulunduğu Azure AD kiracısını tanımlar. Verilen belirteç bir v 2.0 belirteci ise ( `ver` talebe bakın), URI sona ermeyecektir `/v2.0` . Kullanıcının Microsoft hesabı bir tüketici kullanıcısı olduğunu gösteren GUID `9188040d-6c67-4c5b-b112-36a304b66dad` . Uygulamanız, varsa, uygulamada oturum açmak için kullanılan kiracılar kümesini kısıtlamak için talebin GUID bölümünü kullanabilir. |
|`idp`| Dize, genellikle STS URI 'SI | Belirtecin öznesinin kimliğini doğrulayan kimlik sağlayıcısını kaydeder. Bu değer, Kullanıcı hesabı veren konukiyle aynı kiracıda olmadığı sürece veren talebinin değeriyle aynıdır, örneğin. Talep yoksa, bunun yerine, değerinin kullanılabileceği anlamına gelir `iss` .  Bir kuruluş bağlamında kullanılan kişisel hesaplar için (örneğin, bir Azure AD kiracısına davet edilen kişisel hesap), `idp` talep ' Live.com ' veya Microsoft hesabı kiracıyı içeren BIR STS URI 'si olabilir `9188040d-6c67-4c5b-b112-36a304b66dad` . |
| `iat` | int, UNIX zaman damgası | "Çıkarılan", bu belirteç için kimlik doğrulamanın ne zaman oluştuğunu gösterir. |
| `nbf` | int, UNIX zaman damgası | "NBF" (before) talebi, JWT 'ın işlenmek üzere kabul edilmeden önce geçen süreyi tanımlar. |
| `exp` | int, UNIX zaman damgası | "Exp" (sona erme saati) talebi, JWT 'ın işlenmek üzere kabul edilmemelidir. Bir kaynağın bu süreden önce belirteci reddedebileceğini, örneğin, kimlik doğrulamasında yapılan bir değişikliğin gerekli olduğu veya bir belirteç iptalinin algılanabilmesi önemlidir. |
| `aio` | Donuk dize | Azure AD tarafından belirteç yeniden kullanımı için veri kaydetmek üzere kullanılan bir iç talep. Kaynaklar bu talebi kullanmamalıdır. |
| `acr` | Dize, "0" veya "1" | Yalnızca v 1.0 belirteçlerinde bulunur. "Kimlik doğrulama bağlamı sınıfı" talebi. "0" değeri, son kullanıcı kimlik doğrulamasının ISO/ıEC 29115 gereksinimlerini karşılamadığı anlamına gelir. |
| `amr` | Dizelerin JSON dizisi | Yalnızca v 1.0 belirteçlerinde bulunur. Belirtecin konusunun nasıl doğrulandığını tanımlar. Daha fazla bilgi için [AMR Claim bölümüne](#the-amr-claim) bakın. |
| `appid` | Dize, GUID | Yalnızca v 1.0 belirteçlerinde bulunur. Belirteci kullanan istemcinin uygulama KIMLIĞI. Uygulama kendi kendine veya bir kullanıcı adına çalışabilir. Uygulama KIMLIĞI genellikle bir uygulama nesnesini temsil eder, ancak Azure AD 'de bir hizmet sorumlusu nesnesini de temsil edebilir. |
| `azp` | Dize, GUID | Yalnızca, için bir değiştirme olan v 2.0 belirteçlerinde bulunur `appid` . Belirteci kullanan istemcinin uygulama KIMLIĞI. Uygulama kendi kendine veya bir kullanıcı adına çalışabilir. Uygulama KIMLIĞI genellikle bir uygulama nesnesini temsil eder, ancak Azure AD 'de bir hizmet sorumlusu nesnesini de temsil edebilir. |
| `appidacr` | "0", "1" veya "2" | Yalnızca v 1.0 belirteçlerinde bulunur. İstemcinin nasıl doğrulandığını gösterir. Ortak istemci için, değer "0" olur. İstemci KIMLIĞI ve istemci gizli anahtarı kullanılıyorsa, değer "1" olur. Kimlik doğrulaması için bir istemci sertifikası kullanılmışsa, değer "2" olur. |
| `azpacr` | "0", "1" veya "2" | Yalnızca, için bir değiştirme olan v 2.0 belirteçlerinde bulunur `appidacr` . İstemcinin nasıl doğrulandığını gösterir. Ortak istemci için, değer "0" olur. İstemci KIMLIĞI ve istemci gizli anahtarı kullanılıyorsa, değer "1" olur. Kimlik doğrulaması için bir istemci sertifikası kullanılmışsa, değer "2" olur. |
| `preferred_username` | Dize | Kullanıcıyı temsil eden birincil Kullanıcı adı. Belirtilen biçim olmadan bir e-posta adresi, telefon numarası veya genel Kullanıcı adı olabilir. Değeri değişebilir ve zaman içinde değişebilir. Değişebilir olduğundan, bu değer yetkilendirme kararları almak için kullanılmamalıdır.  Ancak Kullanıcı adı ipuçları için ve Kullanıcı adı olarak okunabilir Kullanıcı arabiriminde kullanılabilir. `profile`Bu talebi alabilmesi için kapsam gereklidir. Yalnızca v 2.0 belirteçlerinde bulunur. |
| `name` | Dize | Belirtecin konusunu tanımlayan, okunabilir bir değer sağlar. Değerin benzersiz olması garanti edilmez, değişebilir ve yalnızca görüntüleme amacıyla kullanılmak üzere tasarlanmıştır. `profile`Bu talebi alabilmesi için kapsam gereklidir. |
| `scp` | Dize, virgülle ayrılmış bir kapsam listesi | Uygulamanız tarafından sunulan, istemci uygulamasının istediği (ve aldığı) izin veren kapsamlar kümesi. Uygulamanız, bu kapsamların uygulamanız tarafından açığa çıkarılan geçerli olduğunu doğrulamalıdır ve bu kapsamların değerine göre yetkilendirme kararları verir. Yalnızca [Kullanıcı belirteçleri](#user-and-application-tokens)için dahil edilmiştir. |
| `roles` | Dizelerin dizisi, bir izin listesi | Uygulamanız tarafından kullanıma sunulan uygulamanın veya kullanıcının çağrı için izin verilen izinler kümesi. [Uygulama belirteçleri](#user-and-application-tokens)için, bu, Kullanıcı kapsamları yerine istemci kimlik bilgisi akışı ([v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v 2.0](v2-oauth2-client-creds-grant-flow.md)) sırasında kullanılır.  [Kullanıcı belirteçleri](#user-and-application-tokens) için bu, kullanıcının hedef uygulamada atandığı rollerle doldurulur. |
| `wids` | [Roletemplateıd](../roles/permissions-reference.md#all-roles) GUID dizisi | [Azure AD yerleşik rollerinde](../roles/permissions-reference.md#all-roles)bulunan roller bölümünden bu kullanıcıya atanan kiracı genelindeki rol sayısını belirtir.  Bu talep uygulama bildiriminin özelliği aracılığıyla uygulama başına temelinde yapılandırılır `groupMembershipClaims` . [](reference-app-manifest.md)  "All" veya "DirectoryRole" olarak ayarlanması gerekir.  Belirteç uzunluğu sorunları nedeniyle örtük akış aracılığıyla alınan belirteçlerde bulunmayabilir. |
| `groups` | GUID 'lerin JSON dizisi | Konunun grup üyeliklerini temsil eden nesne kimliklerini sağlar. Bu değerler benzersizdir (bkz. nesne KIMLIĞI) ve bir kaynağa erişmek için Yetkilendirmeyi zorlama gibi erişimi yönetmek için güvenli bir şekilde kullanılabilir. Gruplar talebine dahil olan gruplar, `groupMembershipClaims` [uygulama bildiriminin](reference-app-manifest.md)özelliği aracılığıyla uygulama başına temelinde yapılandırılır. Null değeri tüm grupları dışlayacak, "SecurityGroup" değeri yalnızca Active Directory güvenlik grubu üyeliklerini içerir ve "All" değeri hem güvenlik gruplarını hem de Microsoft 365 dağıtım listelerini içerir. <br><br>`hasgroups`Dolaylı izin ile talebi kullanma hakkında ayrıntılı bilgi için aşağıdaki talebe bakın `groups` . <br>Diğer akışlar için, kullanıcının sayısı bir sınırı (SAML için 150 200, JWT için) alıyorsa, bir fazla kullanım talebi, Kullanıcı için grupların listesini içeren Microsoft Graph uç noktasını gösteren talep kaynaklarına eklenir. |
| `hasgroups` | Boole | Varsa, her zaman `true` kullanıcının en az bir grupta olduğunu belirten. `groups`Tam gruplar TALEBI URI PARÇASıNı URL uzunluğu sınırlarının ötesinde (Şu anda 6 veya daha fazla grup) genişletecek, örtük verme akışlarında JWTs talebi yerine kullanılır. İstemcinin, kullanıcının gruplarını () belirleyebilmek için Microsoft Graph API 'sini kullanması gerektiğini belirtir `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` . |
| `groups:src1` | JSON nesnesi | Sınırlı olmayan Belirteç istekleri ( `hasgroups` yukarıya bakın), ancak belirteç için hala çok büyük olması için, kullanıcının tam gruplar listesine bir bağlantı dahil edilir. Cwts için, talep yerine yeni bir talep olarak SAML için dağıtılmış bir talep olarak `groups` . <br><br>**Örnek JWT değeri**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | Dize | Belirtecin, uygulamanın kullanıcısı gibi bilgilerin hangi sorumluya ait olduğunu belirten sorumlu. Bu değer sabittir ve yeniden atanamaz veya tekrar kullanılamaz. Belirteç bir kaynağa erişmek için kullanıldığında ve veritabanı tablolarında anahtar olarak kullanılabilecek gibi, güvenli bir şekilde yetkilendirme denetimleri gerçekleştirmek için kullanılabilir. Konu her zaman Azure AD 'nin sorun verdiği belirteçlerde bulunduğundan, bu değeri genel amaçlı yetkilendirme sisteminde kullanmanızı öneririz. Bununla birlikte, bir ikili tanımlayıcı, belirli bir uygulama KIMLIĞI için benzersizdir. Bu nedenle, tek bir Kullanıcı iki farklı istemci kimliği kullanarak iki farklı uygulama üzerinde oturum açarsa, bu uygulamalar konu talebi için iki farklı değer alır. Bu, mimarinize ve gizlilik gereksinimlerinize bağlı olarak istenebilir veya istenmeyebilir. Ayrıca bkz `oid` . talep (bir Kiracıdaki uygulamalar arasında aynı kalır). |
| `oid` | Dize, GUID | İsteğin "sorumlusu" için sabit tanımlayıcı-kimliği doğrulanmış kullanıcı veya hizmet sorumlusu.  KIMLIK belirteçleri ve uygulama + Kullanıcı belirteçleri ' nde bu, kullanıcının nesne KIMLIĞIDIR.  Yalnızca uygulama belirteçlerinde, bu, çağıran hizmet sorumlusunun nesne kimliğidir. Ayrıca, kimlik doğrulama denetimlerini güvenli bir şekilde ve veritabanı tablolarında anahtar olarak gerçekleştirmek için de kullanılabilir. Bu KIMLIK, uygulama genelinde sorumluyu benzersiz bir şekilde tanımlar. aynı kullanıcı için iki farklı uygulama oturum açma, talepteki aynı değeri alacaktır `oid` . Bu nedenle, `oid` Microsoft Graph gibi Microsoft çevrimiçi hizmetler sorguları yaparken kullanılabilir. Microsoft Graph, bu KIMLIĞI `id` verilen [Kullanıcı hesabının](/graph/api/resources/user)özelliği olarak döndürür. `oid`Birden çok uygulamanın sorumlular ile ilişkilendirilmesi sağladığından, `profile` Bu talebi kullanıcılara almak için kapsam gerekir. Tek bir Kullanıcı birden fazla kiracıda varsa, kullanıcının her kiracıda farklı bir nesne KIMLIĞI içerdiğini ve Kullanıcı aynı kimlik bilgilerine sahip her hesapta oturum açtığı halde farklı hesaplar olarak kabul edileceğini unutmayın. |
| `tid` | Dize, GUID | Kullanıcının ait olduğu Azure AD kiracısını temsil eder. İş ve okul hesapları için GUID, kullanıcının ait olduğu kuruluşun sabit kiracı KIMLIĞIDIR. Kişisel hesaplar için değer `9188040d-6c67-4c5b-b112-36a304b66dad` . `profile`Bu talebi alabilmesi için kapsam gereklidir. |
| `unique_name` | Dize | Yalnızca v 1.0 belirteçlerinde bulunur. Belirtecin konusunu tanımlayan ve okunabilir bir değer sunar. Bu değerin kiracı içinde benzersiz olması garanti edilmez ve yalnızca görüntüleme amacıyla kullanılmalıdır. |
| `uti` | Donuk dize | Belirteçleri yeniden doğrulamak için Azure tarafından kullanılan bir iç talep. Kaynaklar bu talebi kullanmaz. |
| `rh` | Donuk dize | Belirteçleri yeniden doğrulamak için Azure tarafından kullanılan bir iç talep. Kaynaklar bu talebi kullanmamalıdır. |
| `ver` | Dize, `1.0` ya da `2.0` | Erişim belirtecinin sürümünü gösterir. |

**Gruplar fazla kullanım talebi**

Belirteç boyutunun HTTP üst bilgi boyutu sınırlarını aşmadığından emin olmak için, Azure AD, gruplar talebinde içerdiği nesne kimliklerinin sayısını sınırlar. Bir Kullanıcı fazla kullanım sınırından daha fazla grup üyesiyse (SAML belirteçleri için 150, JWT belirteçleri için 200 ve yalnızca örtük akış ile verildiyse 6), Azure AD, belirteç içindeki grup talebini göstermez. Bunun yerine, belirtece, kullanıcının grup üyeliğini almak için Microsoft Graph API 'sini sorgulamak üzere uygulamayı gösteren bir fazla kullanım talebi içerir.

```JSON
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

`BulkCreateGroups.ps1`Fazla kullanım senaryolarını test etmenize yardımcı olması Için [uygulama oluşturma betikleri](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) klasöründe sağlanan ' i kullanabilirsiniz.

#### <a name="v10-basic-claims"></a>v 1.0 temel talepler

Aşağıdaki talepler varsa v 1.0 belirteçlerine dahil edilir, ancak varsayılan olarak v 2.0 belirteçlerine dahil edilmez. V 2.0 kullanıyorsanız ve bu taleplerden birine ihtiyacınız varsa, bunları [isteğe bağlı talepler](active-directory-optional-claims.md)kullanarak isteyin.

| İste | Biçimlendir | Açıklama |
|-----|--------|-------------|
| `ipaddr`| Dize | Kullanıcının kimliği doğrulanan IP adresi. |
| `onprem_sid`| Dize, [SID biçiminde](/windows/desktop/SecAuthZ/sid-components) | Kullanıcının şirket içi kimlik doğrulamasına sahip olduğu durumlarda, bu talep SID 'leri sağlar. `onprem_sid`Eski uygulamalarda yetkilendirme için ' i kullanabilirsiniz.|
| `pwd_exp`| int, UNIX zaman damgası | Kullanıcının parolasının süresinin dolacağını gösterir. |
| `pwd_url`| Dize | Parolalarını sıfırlamak için kullanıcıların gönderilebileceği bir URL. |
| `in_corp`| boolean | İstemci şirket ağından oturum açıyorsanız bildirir. Aksi takdirde talep dahil edilmez. |
| `nickname`| Dize | Kullanıcı için, adı ilk veya soyadı olan ek bir ad.|
| `family_name` | Dize | Kullanıcı nesnesinde tanımlandığı şekilde kullanıcının soyadı, soyadı veya aile adını sağlar. |
| `given_name` | Dize | Kullanıcı nesnesinde ayarlandığı gibi, kullanıcının adını veya verilen adı sağlar. |
| `upn` | Dize | Kullanıcının Kullanıcı adı. Telefon numarası, e-posta adresi veya biçimlendirilmemiş dize olabilir. Yalnızca görüntüleme amacıyla ve yeniden kimlik doğrulama senaryolarında Kullanıcı adı ipuçları sağlamak için kullanılmalıdır. |

#### <a name="the-amr-claim"></a>`amr`Talep

Microsoft kimlikleri, uygulamanız için uygun olabilecek farklı yollarla kimlik doğrulaması yapabilir. `amr`Talep, `["mfa", "rsa", "pwd"]` hem parola hem de doğrulayıcı uygulaması kullanan bir kimlik doğrulaması için gibi birden çok öğe içerebilen bir dizidir.

| Değer | Açıklama |
|-----|-------------|
| `pwd` | Parola kimlik doğrulaması, kullanıcının Microsoft parolası veya bir uygulamanın istemci gizli anahtarı. |
| `rsa` | Kimlik doğrulaması, örneğin [Microsoft Authenticator uygulamasına](https://aka.ms/AA2kvvu)sahıp bir RSA anahtarı kanıtını temel alır. Bu, kimlik doğrulamasının, hizmetin sahip olduğu x509 sertifikası ile kendinden imzalı bir JWT tarafından gerçekleştirilip yapılmadığını içerir. |
| `otp` | Bir e-posta veya kısa mesaj kullanarak bir kerelik geçiş kodu. |
| `fed` | Bir Federasyon kimlik doğrulama onayı (JWT veya SAML gibi) kullanıldı. |
| `wia` | Windows tümleşik kimlik doğrulaması |
| `mfa` | [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) kullanıldı. Bu mevcut olduğunda diğer kimlik doğrulama yöntemleri de dahil edilir. |
| `ngcmfa` | İle eşdeğer `mfa` , bazı gelişmiş kimlik bilgisi türlerini sağlamak için kullanılır. |
| `wiaormfa`| Kullanıcı, kimlik doğrulamak için Windows veya MFA kimlik bilgilerini kullandı. |
| `none` | Kimlik doğrulaması yapılmadı. |

## <a name="access-token-lifetime"></a>Erişim belirteci ömrü

Bir erişim belirtecinin varsayılan yaşam süresi, belirteci isteyen istemci uygulamasına bağlı olarak değişir. Örneğin, CAE duyarlı oturumlara anlaşacak sürekli erişim değerlendirmesi (CAE) özellikli istemciler uzun süreli bir belirteç ömrü (28 saate kadar) görür.  Erişim belirtecinin süresi dolarsa, istemcinin yenileme belirtecini (genellikle sessizce) yeni bir yenileme belirteci ve erişim belirteci almak için kullanması gerekir.

İstemci uygulamanın ne sıklıkta uygulama oturumunun süresinin dolacağını ve kullanıcının ne sıklıkta yeniden kimlik doğrulaması gerektirdiğini (sessizce veya etkileşimli olarak) denetlemek için bir erişim belirtecinin ömrünü ayarlayabilirsiniz. Daha fazla bilgi için, [yapılandırılabilir belirteç yaşam sürelerini](active-directory-configurable-token-lifetimes.md)okuyun.

## <a name="validating-tokens"></a>Belirteçleri doğrulama

Tüm uygulamaların belirteçleri doğrulaması gerekir. Yalnızca belirli senaryolarda uygulamalar bir belirteci doğrular:

* [Web API 'lerine](quickstart-configure-app-expose-web-apis.md) , bir istemci tarafından bunlara gönderilen erişim belirteçlerini doğrulaması gerekir.  Bunlar yalnızca talep içeren belirteçleri kabul etmelidir `aud` .
* ASP.NET Core gibi gizli Web uygulamaları, bir kullanıcının verilerine erişim izni vermeden veya bir oturum oluşturmadan önce, bu kullanıcılara karma akıştaki tarayıcıdan gönderilen KIMLIK belirteçlerini doğrulamalıdır.

Yukarıdaki senaryolardan hiçbiri uygulanmıyorsa, uygulamanız belirteci doğrulamaya yarar olmaz ve belirtecin geçerliliği temel alınarak kararlar yapılırsa bir güvenlik ve güvenilirlik riski sunabilir.  Yerel uygulamalar veya maça 'Lar gibi genel istemciler, belirteçlerin doğrulanması avantajına sahip değildir; bu nedenle SSL koruması, belirteçlerin geçerli olmasını sağlar.

API 'Ler ve Web Apps yalnızca uygulamalarıyla eşleşen bir talebi olan belirteçleri doğrulamalıdır `aud` ; diğer kaynaklar özel belirteç doğrulama kurallarına sahip olabilir. Örneğin, Microsoft Graph belirteçleri, kendi özel biçimleri nedeniyle bu kurallara göre doğrulanmaz. Başka bir kaynak için anlamı olan belirteçleri doğrulamak ve kabul etmek, [karıştırılan](https://cwe.mitre.org/data/definitions/441.html) bir sorun örneğidir.

Uygulamanızın yukarıya göre bir id_token veya access_token doğrulaması gerekiyorsa, uygulamanız öncelikle belirtecin imzasını ve veren öğesini OpenID keşif belgesindeki değerlere göre doğrulamalıdır. Örneğin, belgenin kiracı bağımsız sürümü konumunda bulunur [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration) .

Aşağıdaki bilgiler, temeldeki işlemi anlamak isteyen kişiler için verilmiştir. Azure AD ara yazılımı, erişim belirteçlerini doğrulamaya yönelik yerleşik yeteneklere sahiptir ve seçtiğiniz dilde bir tane bulmak için [örneklerimize](sample-v2-code.md) göz atabilirsiniz. Ayrıca, JWT doğrulaması için kullanılabilen bazı üçüncü taraf açık kaynaklı kitaplıklar vardır. neredeyse her platform ve dil için en az bir seçenek vardır. Azure AD kimlik doğrulama kitaplıkları ve kod örnekleri hakkında daha fazla bilgi için bkz. [kimlik doğrulama kitaplıkları](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>İmza doğrulanıyor

JWT, karakteriyle ayrılmış üç kesim içerir `.` . İlk kesim **başlık**, **gövde** olarak, ikinci ise **imza** olarak bilinir. İmza segmenti, uygulamanızın güvenilebilmesi için belirtecin orijinalliğini doğrulamak üzere kullanılabilir.

Azure AD tarafından verilen belirteçler, RS256 gibi sektör standardı asimetrik şifreleme algoritmaları kullanılarak imzalanır. JWT üst bilgisi, belirteci imzalamak için kullanılan anahtar ve şifreleme yöntemiyle ilgili bilgiler içerir:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg`Talep, belirteci imzalamak için kullanılan algoritmayı gösterir, `kid` talep ise belirteci doğrulamak için kullanılan belirli ortak anahtarı gösterir.

Azure AD, belirli bir zamanda belirli bir genel-özel anahtar çiftleri kümesinden birini kullanarak bir id_token imzalayabilir. Azure AD, olası anahtar kümesini düzenli aralıklarla döndürür, bu nedenle uygulamanızın bu anahtar değişikliklerini otomatik olarak işleyecek şekilde yazılması gerekir. Azure AD tarafından kullanılan ortak anahtarlarda güncelleştirmelerin her 24 saatte bir denetlenmesi için makul bir sıklık vardır.

İmzayı doğrulamak için gereken imzalama anahtarı verilerini, şurada bulunan [OpenID Connect meta veri belgesini](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) kullanarak elde edebilirsiniz:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Bu [URL 'yi](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) bir tarayıcıda deneyin!

Bu meta veri belgesi:

* , OpenID Connect kimlik doğrulaması yapmak için gereken çeşitli uç noktaların konumu gibi çeşitli yararlı bilgi parçalarını içeren bir JSON nesnesidir.
* `jwks_uri`Belirteçleri imzalamak için kullanılan ortak anahtar kümesinin konumunu veren bir içerir. Konumunda bulunan JSON Web anahtarı (JWK), `jwks_uri` Bu belirli bir anda kullanımda olan tüm ortak anahtar bilgilerini içerir.  JWK biçimi, [RFC 7517](https://tools.ietf.org/html/rfc7517)' de açıklanmaktadır.  Uygulamanız, `kid` Bu belgedeki hangi ortak anahtarın belirli bir belirteci imzalamak için kullanıldığını seçmek üzere JWT üstbilgisindeki talebi kullanabilir. Daha sonra doğru ortak anahtarı ve belirtilen algoritmayı kullanarak imza doğrulaması yapabilir.

> [!NOTE]
> `kid`Belirtecinizi doğrulamak için talebin kullanılması önerilir. V 1.0 belirteçleri hem hem de taleplerini içerse de `x5t` `kid` , v 2.0 belirteçleri yalnızca talebi içerir `kid` .

İmza doğrulama işlemi, bu belgenin kapsamı dışındadır. gerekirse, bunu yapmanıza yardımcı olacak çok sayıda açık kaynak kitaplığı mevcuttur.  Ancak, Microsoft Identity platformunda standart özel imzalama anahtarlarına tek bir belirteç imzalama uzantısı vardır.

Uygulamanızda, [talep eşleme](active-directory-claims-mapping.md) özelliğini kullanmanın bir sonucu olarak özel İmzalama anahtarları varsa, `appid` `jwks_uri` uygulamanızın imza anahtarı bilgilerine işaret etmek için, doğrulama IÇIN kullanılması gereken uygulama kimliğini içeren bir sorgu parametresi eklemeniz gerekir. Örneğin: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` bir içerir `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` .

### <a name="claims-based-authorization"></a>Talep tabanlı yetkilendirme

Uygulamanızın iş mantığı bu adımı dikte edecek, bazı yaygın yetkilendirme yöntemleri aşağıda düzenlenmiştir.

* `scp` `roles` Mevcut tüm kapsamların API 'niz tarafından açıklananlar ile eşleştiğini doğrulamak ve istemcinin istenen eylemi yapmasına izin vermek için veya talebini denetleyin.
* Çağrıyı yapan istemcinin, talebi kullanarak API 'nizi çağırmasının izin verildiğinden emin olun `appid` .
* Kullanarak çağıran istemcinin kimlik doğrulama durumunu doğrulama `appidacr` -ortak ISTEMCILERIN API 'nizi çağırma izni yoksa 0 olmaması gerekir.
* `nonce`Belirtecin yeniden çalındığını doğrulamak için geçmiş talepler listesine göz atın.
* ' `tid` In API 'nizi çağırması için izin verilen bir kiracı ile eşleştiğinden emin olun.
* `amr`Kullanıcının MFA gerçekleştirdi olduğunu doğrulamak için talebi kullanın. Bu, [koşullu erişim](../conditional-access/overview.md)kullanılarak zorunlu kılınmalıdır.
* `roles`Erişim belirtecinde veya taleplerini istemiş olmanız durumunda, `groups` kullanıcının bu eylemi yapmasına izin verilen grupta olduğunu doğrulayın.
  * Örtük akış kullanılarak alınan belirteçler için, büyük olasılıkla belirtece sığamayacak kadar büyük olduğu için bu verilerin [Microsoft Graph](https://developer.microsoft.com/graph/) sorgulanmak zorunda olacaksınız.

## <a name="user-and-application-tokens"></a>Kullanıcı ve uygulama belirteçleri

Uygulamanız kullanıcı (genellikle tartışılan akış) veya doğrudan bir uygulamadan ( [istemci kimlik bilgileri akışı](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)aracılığıyla) belirteç alabilir. Bu yalnızca uygulama belirteçleri, bu çağrının bir uygulamadan geldiğini ve bunu yedekleyen bir kullanıcıya sahip olmadığını gösterir. Bu belirteçler büyük ölçüde aynı şekilde işlenir:

* `roles`Belirtecin konusuna verilen izinleri görmek için kullanın.
* `oid` `sub` Çağıran hizmet sorumlusunun beklenen bir hizmet olduğunu doğrulamak için veya kullanın.

Uygulamanızın, kullanıcılar için yalnızca uygulama erişim belirteçleri ve erişim belirteçleri arasında ayrım yapması gerekiyorsa, `idtyp` [isteğe bağlı talebi](active-directory-optional-claims.md)kullanın.  `idtyp`Alana talep ekleyerek `accessToken` ve değeri denetleyerek `app` , yalnızca uygulama erişim belirteçlerini tespit edebilirsiniz.  Kullanıcılar için KIMLIK belirteçleri ve erişim belirteçlerine `idtyp` talep dahil edilmez.

## <a name="token-revocation"></a>Belirteç iptali

Farklı nedenlerle yenileme belirteçleri herhangi bir zamanda geçersiz kılınabilir veya iptal edilebilir. Bunlar iki ana kategoriye ayrılır: zaman aşımları ve iptal edilecek.

### <a name="token-timeouts"></a>Belirteç zaman aşımları

[Belirteç ömrü yapılandırması](active-directory-configurable-token-lifetimes.md)kullanıldığında, yenileme belirteçlerinin ömrü değiştirilebilir.  Bu normaldir ve bazı belirteçlerin kullanılmadan gitmesi beklenir (ör. Kullanıcı uygulamayı 3 ay boyunca açmaz) ve bu nedenle sona erer.  Uygulamalar, oturum açma sunucusunun yaşı nedeniyle yenileme belirtecini reddettiği senaryolarla karşılaşacaktır.

* Maxınactivetime: yenileme belirteci, Maxınactivetime tarafından dikte edilen süre içinde kullanılmıyorsa, yenileme belirteci artık geçerli olmayacaktır.
* MaxSessionAge: MaxAgeSessionMultiFactor veya Maxagesessionsingsolaktör, varsayılanlarını (iptal edilene kadar) farklı bir değere ayarlandıysa, MaxAgeSession * geçtiğinde ayarlanan süre sonunda yeniden kimlik doğrulaması gerekecektir.
* Örnekler:
  * Kiracının beş gün boyunca bir Maxınactivetime vardır ve Kullanıcı bir hafta boyunca tatilden oluşur ve Azure AD, kullanıcıdan 7 gün içinde yeni bir belirteç isteği görmemiştir. Kullanıcı yeni bir belirteç istediğinde yenileme belirtecinin iptal edildiğini bulacak ve kimlik bilgilerini tekrar girmeleri gerekir.
  * Hassas bir uygulamanın bir gün için Maxagesessionsinglefaktör vardır. Bir kullanıcı Pazartesi günü oturum açarsa ve Salı günü (25 saat geçtikten sonra), yeniden kimlik doğrulaması yapmanız gerekir.

### <a name="revocation"></a>Sayılabilir

Yenileme belirteçleri, kimlik bilgilerinde bir değişiklik veya kullanım ya da yönetici eylemi nedeniyle sunucu tarafından iptal edilebilir.  Yenileme belirteçleri, gizli istemcilere (en sağdaki sütun) ve ortak istemcilere verilen (diğer tüm sütunlar) verilen iki sınıfa ayrılır.

| Değiştir | Parola tabanlı tanımlama bilgisi | Parola tabanlı belirteç | Parola tabanlı olmayan tanımlama bilgisi | Parola tabanlı olmayan belirteç | Gizli istemci belirteci |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Parola süre sonu | Canlı kalır | Canlı kalır | Canlı kalır | Canlı kalır | Canlı kalır |
| Parola Kullanıcı tarafından değiştirildi | İptal Edildi | İptal Edildi | Canlı kalır | Canlı kalır | Canlı kalır |
| Kullanıcı SSPR | İptal Edildi | İptal Edildi | Canlı kalır | Canlı kalır | Canlı kalır |
| Yönetici parolayı sıfırlar | İptal Edildi | İptal Edildi | Canlı kalır | Canlı kalır | Canlı kalır |
| Kullanıcı, [PowerShell aracılığıyla](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) yenileme belirteçlerini iptal eder | İptal Edildi | İptal Edildi | İptal Edildi | İptal Edildi | İptal Edildi |
| Yönetici, bir kullanıcı için [PowerShell aracılığıyla](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) tüm yenileme belirteçlerini iptal eder | İptal Edildi | İptal Edildi |İptal Edildi | İptal Edildi | İptal Edildi |
| Web 'de çoklu oturum açma ([v 1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v 2.0](v2-protocols-oidc.md#single-sign-out) ) | İptal Edildi | Canlı kalır | İptal Edildi | Canlı kalır | Canlı kalır |

#### <a name="non-password-based"></a>Parola tabanlı olmayan

*Parola tabanlı olmayan* bir oturum açma, kullanıcının bunu almak için bir parola yazmamış olduğu yerdir. Parola tabanlı olmayan oturum açma örnekleri şunları içerir:

- Windows Hello ile yüzünüzü kullanma
- FIDO2 anahtarı
- SMS
- Ses
- PIN

Birincil yenileme belirteçleri hakkında daha fazla bilgi için [birincil yenileme belirteçlerine](../devices/concept-primary-refresh-token.md) göz atın.

## <a name="next-steps"></a>Sonraki adımlar

* [ `id_tokens` Azure AD 'de](id-tokens.md)hakkında bilgi edinin.
* İzin ve onay ( [v 1.0](../azuread-dev/v1-permissions-consent.md), [v 2.0](v2-permissions-and-consent.md)) hakkında bilgi edinin.
