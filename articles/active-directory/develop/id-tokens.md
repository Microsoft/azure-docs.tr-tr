---
title: Microsoft Identity platform KIMLIĞI belirteçleri | Mavisi
titleSuffix: Microsoft identity platform
description: Azure AD v 1.0 ve Microsoft Identity platform (v 2.0) uç noktaları tarafından yayılan id_tokens nasıl kullanacağınızı öğrenin.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom:
- aaddev
- identityplatformtop40
- fasttrack-edit
ms.openlocfilehash: 885379a02c8866f2829fb681683a93b1d8d314fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530024"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft Identity platform KIMLIĞI belirteçleri

`id_tokens` , bir [OpenID Connect](v2-protocols-oidc.md) (OIDC) akışının parçası olarak istemci uygulamasına gönderilir. Bunlar, bir erişim belirteci yerine veya bu şekilde gönderilebilir ve istemci tarafından kullanıcının kimliğini doğrulamak için kullanılır.

## <a name="using-the-id_token"></a>İd_token kullanma

KIMLIK belirteçleri, bir kullanıcının iddia ettikleri kim olduğunu doğrulamak ve bunlarla ilgili yararlı bilgiler almak için kullanılmalıdır; bir [erişim belirtecinin](access-tokens.md)yerine yetkilendirme için kullanılmamalıdır. Sağladığı talepler, uygulamanızın içindeki UX için, [bir veritabanında anahtarlar](#using-claims-to-reliably-identify-a-user-subject-and-object-id)olarak ve istemci uygulamasına erişim sağlamak için kullanılabilir.  

## <a name="claims-in-an-id_token"></a>İd_token talepler

`id_tokens` , [Jwts](https://tools.ietf.org/html/rfc7519) (JSON Web belirteçleri), yani bir üst bilgi, yük ve imza kısmından oluşur. Belirtecin orijinalliğini doğrulamak için üst bilgi ve imzayı, yük ise istemciniz tarafından istenen kullanıcı hakkındaki bilgileri içerdiğinde de kullanabilirsiniz. Aksi belirtilmedikçe, burada listelenen tüm JWT talepleri v 1.0 ve v 2.0 belirteçlerinde görünür.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Bu v 1.0 örnek belirtecini [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q)içinde görüntüleyin.

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Bu v 2.0 örnek belirtecini [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw)içinde görüntüleyin.

### <a name="header-claims"></a>Üst bilgi talepleri

|İste | Biçimlendir | Açıklama |
|-----|--------|-------------|
|`typ` | Dize-Always "JWT" | Belirtecin bir JWT belirteci olduğunu gösterir.|
|`alg` | Dize | Belirteci imzalamak için kullanılan algoritmayı belirtir. Örnek: "RS256" |
|`kid` | Dize | Bu belirteci doğrulamak için kullanılan ortak anahtar için parmak izi. Hem v 1.0 hem de v 2.0 içinde yayınlanır `id_tokens` . |
|`x5t` | Dize | Aynı (kullanımda ve değerde) olarak `kid` . Bununla birlikte, bu, uyumluluk amacıyla yalnızca v 1.0 ile yayılan eski bir taleptür `id_tokens` . |

### <a name="payload-claims"></a>Yük talepleri

Bu liste varsayılan olarak en çok id_tokens olan JWT taleplerini gösterir (aksi belirtilmedikçe).  Bununla birlikte, uygulamanız id_token ek JWT talepleri istemek için [isteğe bağlı talepler](active-directory-optional-claims.md) kullanabilir.  Bunlar, `groups` kullanıcıdan kullanıcının adı hakkında bilgi talep edebilir.

|İste | Biçimlendir | Açıklama |
|-----|--------|-------------|
|`aud` |  Dize, uygulama KIMLIĞI URI 'SI | Belirtecin amaçlanan alıcısını tanımlar. `id_tokens`' De hedef kitle, uygulamanızın Azure Portal uygulamanıza atanan uygulama kimliğidir. Uygulamanızın bu değeri doğrulaması ve değer eşleşmezse belirteci reddetmesi gerekir. |
|`iss` |  Dize, STS URI 'SI | Belirteci oluşturan ve döndüren güvenlik belirteci hizmetini (STS) ve kullanıcının kimlik doğrulamasının bulunduğu Azure AD kiracısını tanımlar. Belirteç v 2.0 uç noktası tarafından verildiyse, URI sona ermeyecektir `/v2.0` .  Kullanıcının Microsoft hesabı bir tüketici kullanıcısı olduğunu gösteren GUID `9188040d-6c67-4c5b-b112-36a304b66dad` . Uygulamanız, varsa uygulamada oturum açmak için gereken kiracılar kümesini kısıtlamak için talebin GUID kısmını kullanmalıdır. |
|`iat` |  int, UNIX zaman damgası | "Çıkarılan", bu belirteç için kimlik doğrulamanın ne zaman oluştuğunu gösterir.  |
|`idp`|Dize, genellikle STS URI 'SI | Belirtecin öznesinin kimliğini doğrulayan kimlik sağlayıcısını kaydeder. Bu değer, Kullanıcı hesabı veren konukiyle aynı kiracıda olmadığı sürece veren talebinin değeriyle aynıdır, örneğin. Talep yoksa, bunun yerine, değerinin kullanılabileceği anlamına gelir `iss` .  Bir kuruluş bağlamında kullanılan kişisel hesaplar için (örneğin, bir Azure AD kiracısına davet edilen kişisel hesap), `idp` talep ' Live.com ' veya Microsoft hesabı kiracıyı içeren BIR STS URI 'si olabilir `9188040d-6c67-4c5b-b112-36a304b66dad` . |
|`nbf` |  int, UNIX zaman damgası | "NBF" (before) talebi, JWT 'ın işlenmek üzere kabul edilmeden önce geçen süreyi tanımlar.|
|`exp` |  int, UNIX zaman damgası | "Exp" (sona erme saati) talebi, JWT 'ın işlenmek üzere kabul edilmemelidir.  Bir kaynağın bu süreden önce belirteci reddedebileceğini unutmayın. Örneğin, kimlik doğrulamasında bir değişiklik gerekliyse veya bir belirteç iptali algılanırsa. |
| `c_hash`| Dize |Kod karması, yalnızca KIMLIK belirteci bir OAuth 2,0 yetkilendirme koduyla verildiğinde KIMLIK belirteçlerine dahil edilir. Bir yetkilendirme kodunun gerçekliğini doğrulamak için kullanılabilir. Bu doğrulamayı gerçekleştirme hakkında daha fazla bilgi için [OpenID Connect belirtimine](https://openid.net/specs/openid-connect-core-1_0.html)bakın. |
|`at_hash`| Dize |Erişim belirteci karması, kimlik belirteçlerine yalnızca KIMLIK belirteci `/authorize` bir OAuth 2,0 erişim belirtecine sahip uç noktadan verildiğinde dahil edilir. Bir erişim belirtecinin orijinalliğini doğrulamak için kullanılabilir. Bu doğrulamayı gerçekleştirme hakkında daha fazla bilgi için [OpenID Connect belirtimine](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken)bakın. Bu, uç noktadan alınan KIMLIK belirteçlerinde döndürülmez `/token` . |
|`aio` | Donuk dize | Azure AD tarafından belirteç yeniden kullanımı için veri kaydetmek üzere kullanılan bir iç talep. Göz ardı edilmelidir.|
|`preferred_username` | Dize | Kullanıcıyı temsil eden birincil Kullanıcı adı. Belirtilen biçim olmadan bir e-posta adresi, telefon numarası veya genel Kullanıcı adı olabilir. Değeri değişebilir ve zaman içinde değişebilir. Değişebilir olduğundan, bu değer yetkilendirme kararları almak için kullanılmamalıdır. `profile`Kapsam bu talebi almak için gereklidir.|
|`email` | Dize | `email`Talep, bir e-posta adresine sahip konuk hesapları için varsayılan olarak mevcuttur.  Uygulamanız, `email` [isteğe bağlı talebi](active-directory-optional-claims.md)kullanarak yönetilen kullanıcılar (kaynakla aynı kiracıya ait olanlar) için e-posta talebi talep edebilir.  V 2.0 uç noktasında, uygulamanız `email` OpenID Connect kapsamını da talep edebilir. talebi almak için hem isteğe bağlı talebi hem de kapsamı istemeniz gerekmez.  E-posta talebi yalnızca kullanıcının profil bilgilerinde adreslenebilir postaları destekler. |
|`name` | Dize | `name`Talep, belirtecin konusunu tanımlayan, insan tarafından okunabilen bir değer sağlar. Değerin benzersiz olması garanti edilmez, değişebilir ve yalnızca görüntüleme amacıyla kullanılmak üzere tasarlanmıştır. `profile`Kapsam bu talebi almak için gereklidir. |
|`nonce`| Dize | Nonce, ıDP 'ye özgün/Yetkilendir isteğine dahil edilen parametreyle eşleşir. Eşleşmiyorsa, uygulamanız belirteci reddetmelidir. |
|`oid` | Dize, GUID | Bu örnekte, bir kullanıcı hesabı olan Microsoft Identity sistemindeki bir nesne için sabit tanımlayıcı. Bu KIMLIK, kullanıcıyı uygulamalar arasında benzersiz şekilde tanımlar. aynı kullanıcı için iki farklı uygulama imzalanırken, talepteki aynı değer de alınır `oid` . Microsoft Graph, bu KIMLIĞI `id` verilen kullanıcı hesabının özelliği olarak döndürür. `oid`Birden çok uygulamanın kullanıcıları ilişkilendirme olanağı sağladığından, `profile` Bu talebi almak için kapsamın olması gerekir. Tek bir Kullanıcı birden fazla kiracıda varsa, kullanıcının her kiracıda farklı bir nesne KIMLIĞI içerdiğini ve Kullanıcı aynı kimlik bilgilerine sahip her hesapta oturum açtığı halde farklı hesaplar olarak kabul edileceğini unutmayın. `oid`Talep BIR GUID 'dir ve yeniden kullanılamaz. |
|`roles`| Dizeler dizisi | Oturum açan kullanıcıya atanan roller kümesi. |
|`rh` | Donuk dize |Belirteçleri yeniden doğrulamak için Azure tarafından kullanılan bir iç talep. Göz ardı edilmelidir. |
|`sub` | Dize | Belirtecin, uygulamanın kullanıcısı gibi bilgilerin hangi sorumluya ait olduğunu belirten sorumlu. Bu değer sabittir ve yeniden atanamaz veya tekrar kullanılamaz. Konu, belirli bir uygulama KIMLIĞI için benzersiz bir tanımlayıcıdır. Tek bir Kullanıcı iki farklı istemci kimliği kullanarak iki farklı uygulama üzerinde oturum açarsa, bu uygulamalar konu talebi için iki farklı değer alır. Bu, mimarinize ve gizlilik gereksinimlerinize bağlı olarak istenebilir veya olmayabilir. |
|`tid` | Dize, GUID | Kullanıcının ait olduğu Azure AD kiracısını temsil eden bir GUID. İş ve okul hesapları için GUID, kullanıcının ait olduğu kuruluşun sabit kiracı KIMLIĞIDIR. Kişisel hesaplar için değer `9188040d-6c67-4c5b-b112-36a304b66dad` . `profile`Kapsam bu talebi almak için gereklidir. |
|`unique_name` | Dize | Belirtecin konusunu tanımlayan ve okunabilir bir değer sunar. Bu değer, herhangi bir zamanda belirli bir noktada benzersizdir, ancak e-postalar ve diğer tanımlayıcılar yeniden kullanılabilir, bu değer diğer hesaplarda yeniden görüntülenebilir ve bu nedenle yalnızca görüntüleme amacıyla kullanılmalıdır. Yalnızca v 1.0 'da verilir `id_tokens` . |
|`uti` | Donuk dize | Belirteçleri yeniden doğrulamak için Azure tarafından kullanılan bir iç talep. Göz ardı edilmelidir. |
|`ver` | Dize, 1,0 ya da 2,0 | İd_token sürümünü gösterir. |
|`hasgroups`|Boole|Varsa, her zaman true, kullanıcının en az bir grupta olduğunu belirten. Tam gruplar talebi, URI parçasını URL uzunluğu sınırlarının ötesinde (Şu anda 6 veya daha fazla grup) genişletecek, örtük verme akışlarında JWTs için gruplar talebi yerine kullanılır. İstemcinin, kullanıcının gruplarını () belirleyebilmek için Microsoft Graph API 'sini kullanması gerektiğini belirtir `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` .|
|`groups:src1`|JSON nesnesi | Sınırlı olmayan Belirteç istekleri ( `hasgroups` yukarıya bakın), ancak belirteç için hala çok büyük olması için, kullanıcının tam gruplar listesine bir bağlantı dahil edilir. Cwts için, talep yerine yeni bir talep olarak SAML için dağıtılmış bir talep olarak `groups` . <br><br>**Örnek JWT değeri**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> Daha fazla bilgi için bkz. [Grup fazla kullanım talebi](#groups-overage-claim).|

> [!NOTE]
> V 1.0 ve v 2.0 id_token, yukarıdaki örneklerden görüldüğü gibi taşıyabilecekleri bilgi miktarındaki farklara sahiptir. Sürüm, istenen bitiş noktasına göre belirlenir. Mevcut uygulamalar büyük olasılıkla Azure AD uç noktasını kullanıyor olsa da yeni uygulamalar "Microsoft Identity platform" öğesini kullanmalıdır.
>
> - v 1.0: Azure AD uç noktaları: `https://login.microsoftonline.com/common/oauth2/authorize`
> - v 2.0: Microsoft Identity platform uç noktaları: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Bir kullanıcıyı güvenilir bir şekilde tanımlamak için talepler kullanma (konu ve nesne KIMLIĞI)

Bir Kullanıcı (örneğin, bir veritabanında arayarak veya sahip oldukları izinlere karar verirken) tanımlarken, zaman içinde sabit ve benzersiz kalacak bilgilerin kullanılması önemlidir. Eski uygulamalar bazen e-posta adresi, telefon numarası veya UPN gibi alanları kullanır.  Bunların hepsi zaman içinde değişebilir ve zaman içinde, bir çalışan adını değiştirdiğinde ya da bir çalışana bir önceki, artık çalışanla ilgili bir e-posta adresi verildiğinde, bir çalışanın adı değiştiğinde yeniden kullanılabilir. Bu nedenle, uygulamanızın okunabilir bir kullanıcıyı tanımlamak için insan tarafından okunabilen verileri kullanması **önemli bir öneme** sahiptir. Bunun yerine, OıDC standardı tarafından sunulan talepleri veya Microsoft-ve talepleri tarafından sunulan uzantı taleplerini kullanın `sub` `oid` .

Kullanıcı başına bilgileri doğru bir şekilde depolamak için, `sub` `oid` gerektiğinde yönlendirme veya parçalama için kullanılan veya tek başına (GUID 'ler benzersiz olan) kullanın `tid` .  Verileri hizmetler arasında paylaşmanız gerekiyorsa, `oid` + `tid` tüm uygulamalar `oid` `tid` belirli bir kullanıcı için aynı ve talepler elde ettiği için en iyi seçenektir.  `sub`Microsoft Identity platform 'daki talep "çift yönlü", belirteç alıcısı, kiracı ve Kullanıcı birleşimine göre benzersizdir.  Bu nedenle, belirli bir kullanıcı için KIMLIK belirteçleri isteyen iki uygulama farklı talepler alır `sub` , ancak `oid` bu kullanıcı için aynı taleplerdir.

>[!NOTE]
> `idp`Kiracılar genelinde kullanıcıları ilişkilendirme girişiminde bir kullanıcı hakkındaki bilgileri depolamak için talebi kullanmayın.  `oid` `sub` Uygulamaların kiracılar genelinde kullanıcıları izleyemediğinden emin olmak için, bir kullanıcının kiracılar genelinde değişiklik yaptığı, tasarıma göre de bu çalışmaz.  
>
> Bir kullanıcının bir kiracıda bulunduğu ve başka bir kiracının kimliğini doğrulayan Konuk senaryolar, kullanıcıya hizmet için yepyeni bir kullanıcı gibi davranmalıdır.  Contoso kiracısındaki belgeleriniz ve ayrıcalıklarınız fabrikam kiracısında uygulanmamalıdır. Bu, kiracılar genelinde yanlışlıkla veri sızıntılarını engellemek açısından önemlidir.

### <a name="groups-overage-claim"></a>Gruplar fazla kullanım talebi
Belirteç boyutunun HTTP üst bilgi boyutu sınırlarını aşmadığından emin olmak için, Azure AD, talebe dahil edilen nesne kimliklerinin sayısını sınırlar `groups` . Bir Kullanıcı fazla kullanım sınırından daha fazla grup üyesiyse (SAML belirteçleri için 150, JWT belirteçleri için 200), Azure AD, grup talebini belirteçte göstermez. Bunun yerine, belirtece, kullanıcının grup üyeliğini almak için Microsoft Graph API 'sini sorgulamak üzere uygulamayı gösteren bir fazla kullanım talebi içerir.

```json
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

## <a name="id-token-lifetime"></a>KIMLIK belirteci ömrü

Varsayılan olarak, bir KIMLIK belirteci 1 saat boyunca geçerlidir; 1 saat sonra istemci yeni bir KIMLIK belirteci almalıdır.

İstemci uygulamanın ne sıklıkta uygulama oturumunun süresinin dolacağını ve kullanıcının ne sıklıkta yeniden kimlik doğrulaması gerektirdiğini (sessizce veya etkileşimli olarak) denetlemek için bir KIMLIK belirtecinin ömrünü ayarlayabilirsiniz. Daha fazla bilgi için, [yapılandırılabilir belirteç yaşam sürelerini](active-directory-configurable-token-lifetimes.md)okuyun.

## <a name="validating-an-id_token"></a>İd_token doğrulama

Bir `id_token` [erişim belirtecini doğrulamaya](access-tokens.md#validating-tokens) yönelik ilk adımla benzer bir şekilde doğrulama, istemciniz doğru veren 'in belirteci geri gönderdiğini ve üzerinde oynanmadığını doğrulayabilirler. `id_tokens`Her zaman BIR JWT belirteci olduğundan, bu belirteçleri doğrulamak için birçok kitaplık bulunur; bunlardan birini kendi başınıza yapmak yerine kullanmanızı öneririz.  Yalnızca gizli istemcilerin (gizli olmayan) KIMLIK belirteçlerini doğrulaması gerektiğini unutmayın.  Ortak uygulamalar (bir cihaz veya ağ üzerinde çalışan kod; Örneğin, bir kullanıcının tarayıcısı veya ağı), kötü niyetli bir Kullanıcı belirtecin doğrulanması için kullanılan anahtarları ele geçirebilir ve düzenleyebildiğinden, kimlik belirtecinin doğrulanması avantajına sahip değildir.

Belirteci el ile doğrulamak için, [erişim belirtecini doğrulama](access-tokens.md#validating-tokens)konusunda bulunan adım ayrıntılarına bakın. Belirteçteki imzayı doğruladıktan sonra, aşağıdaki JWT taleplerinin id_token doğrulanması gerekir (Bunlar ayrıca, belirteç doğrulama kitaplığınız tarafından da yapılabilir):

* Zaman damgaları: `iat` , `nbf` , ve `exp` zaman damgaları, uygun şekilde, geçerli zamandan önce veya sonra düşmelidir.
* Hedef kitle: `aud` talebin, uygulamanızın uygulama kimliğiyle eşleşmesi gerekir.
* Nonce: `nonce` yükteki talep, ilk istek sırasında/yetkilendirmeli uç noktasına geçirilen nonce parametresiyle eşleşmelidir.

## <a name="next-steps"></a>Sonraki adımlar

* [Erişim belirteçleri](access-tokens.md) hakkında bilgi edinin
* [İsteğe bağlı talepler](active-directory-optional-claims.md)kullanarak id_token JWT taleplerini özelleştirin.
