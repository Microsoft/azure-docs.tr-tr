---
title: Microsoft Identity platform KIMLIĞI belirteç başvurusu | Microsoft Docs
description: Azure AD v 1.0 ve Microsoft Identity platform (v 2.0) uç noktaları tarafından yayılan id_tokens kullanmayı öğrenin.
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
ms.date: 08/27/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 271d00539d1e502fe172d086067664fe15000dcf
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554808"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft Identity platform KIMLIĞI belirteçleri

`id_tokens`, istemci uygulamasına bir [OpenID Connect](v1-protocols-openid-connect-code.md) akışının parçası olarak gönderilir. Bunlar, bir erişim belirteci yerine ya da üzerinden gönderilebilir ve istemci tarafından kullanıcının kimliğini doğrulamak için kullanılır.

## <a name="using-the-id_token"></a>İd_token kullanma

KIMLIK belirteçleri, bir kullanıcının iddia ettikleri kim olduğunu doğrulamak ve bunlarla ilgili yararlı bilgiler almak için kullanılmalıdır; bir [erişim belirtecinin](access-tokens.md)yerine yetkilendirme için kullanılmamalıdır. Sağladığı talepler, uygulamanızın içindeki UX için, bir veritabanında anahtarlar olarak ve istemci uygulamasına erişim sağlamak için kullanılabilir.  Bir veritabanı için anahtar oluştururken `idp`, Konuk senaryolarını kullanırken kullanılmamalıdır.  Anahtarlama, gerektiğinde yönlendirme için kullanılan `tid` birlikte `sub` tek başına (her zaman benzersiz) yapılmalıdır.  Hizmetler arasında veri paylaşmanız gerekiyorsa `oid` + `sub` + `tid` aynı `oid` sahip olduğu için çalışacaktır.

## <a name="claims-in-an-id_token"></a>Bir id_token içindeki talepler

bir Microsoft kimliği için [`id_tokens`, bir üst bilgi, yük](https://tools.ietf.org/html/rfc7519)ve imza bölümünden oluþdukları anlamına gelir. Belirtecin orijinalliğini doğrulamak için üst bilgi ve imzayı, yük ise istemciniz tarafından istenen kullanıcı hakkındaki bilgileri içerdiğinde de kullanabilirsiniz. Aksi belirtilmedikçe, burada listelenen tüm talepler v 1.0 ve v 2.0 belirteçlerinde görünür.

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
|`typ` | Dize-Always "JWT" | Belirtecin JWT olduğunu gösterir.|
|`alg` | Dize | Belirteci imzalamak için kullanılan algoritmayı belirtir. Örnek: "RS256" |
|`kid` | Dize | Bu belirteci imzalamak için kullanılan ortak anahtar için parmak izi. Hem v 1.0 hem de v 2.0 `id_tokens` yayınlanır. |
|`x5t` | Dize | @No__t_0 olarak aynı (kullanımda ve değerde). Ancak bu, uyumluluk amacıyla yalnızca v 1.0 `id_tokens` yayılan eski bir taleptür. |

### <a name="payload-claims"></a>Yük talepleri

Bu liste varsayılan olarak en id_tokens olan talepleri gösterir (belirtilen durumlar dışında).  Bununla birlikte, uygulamanız id_token içinde ek talepler istemek için [isteğe bağlı talepler](active-directory-optional-claims.md) kullanabilir.  Bunlar, `groups` talebine kullanıcının adı hakkında bilgi olarak değişebilir.

|İste | Biçimlendir | Açıklama |
|-----|--------|-------------|
|`aud` |  Dize, uygulama KIMLIĞI URI 'SI | Belirtecin amaçlanan alıcısını tanımlar. @No__t_0, hedef kitle uygulamanıza Azure portal uygulamanıza atanan uygulama KIMLIĞIDIR. Uygulamanızın bu değeri doğrulaması ve değer eşleşmezse belirteci reddetmesi gerekir. |
|`iss` |  Dize, STS URI 'SI | Belirteci oluşturan ve döndüren güvenlik belirteci hizmetini (STS) ve kullanıcının kimlik doğrulamasının bulunduğu Azure AD kiracısını tanımlar. Belirteç, v 2.0 uç noktası tarafından verildiyse, URI `/v2.0` sona ermeyecektir.  Kullanıcının bir Microsoft hesabı bir tüketici kullanıcısı olduğunu belirten GUID `9188040d-6c67-4c5b-b112-36a304b66dad` ' dır. Uygulamanız, varsa uygulamada oturum açmak için gereken kiracılar kümesini kısıtlamak için talebin GUID kısmını kullanmalıdır. |
|`iat` |  int, UNIX zaman damgası | "Çıkarılan", bu belirteç için kimlik doğrulamanın ne zaman oluştuğunu gösterir.  |
|`idp`|Dize, genellikle STS URI 'SI | Belirtecin öznesinin kimliğini doğrulayan kimlik sağlayıcısını kaydeder. Bu değer, Kullanıcı hesabı veren konukiyle aynı kiracıda olmadığı sürece veren talebinin değeriyle aynıdır, örneğin. Talep yoksa, bunun yerine `iss` değerinin kullanılabileceği anlamına gelir.  Bir kuruluş bağlamında kullanılan kişisel hesaplar için (örneğin, bir Azure AD kiracısına davet edilen kişisel hesap), `idp` talebi ' live.com ' veya `9188040d-6c67-4c5b-b112-36a304b66dad` Microsoft hesabı kiracı içeren bir STS URI 'SI olabilir. |
|`nbf` |  int, UNIX zaman damgası | "NBF" (before) talebi, JWT 'ın işlenmek üzere kabul edilmeden önce geçen süreyi tanımlar.|
|`exp` |  int, UNIX zaman damgası | "Exp" (sona erme saati) talebi, JWT 'ın işlenmek üzere kabul edilmemelidir.  Bir kaynağın bu süreden önce belirteci reddedebileceğini unutmayın. Örneğin, kimlik doğrulamasında bir değişiklik gerekliyse veya bir belirteç iptali algılanırsa. |
| `c_hash`| Dize |Kod karması, yalnızca KIMLIK belirteci bir OAuth 2,0 yetkilendirme koduyla verildiğinde KIMLIK belirteçlerine dahil edilir. Bir yetkilendirme kodunun gerçekliğini doğrulamak için kullanılabilir. Bu doğrulamayı gerçekleştirme hakkında daha fazla bilgi için [OpenID Connect belirtimine](https://openid.net/specs/openid-connect-core-1_0.html)bakın. |
|`at_hash`| Dize |Erişim belirteci karması yalnızca KIMLIK belirteci bir OAuth 2,0 erişim belirteciyle verildiğinde KIMLIK belirteçlerine dahil edilir. Bir erişim belirtecinin orijinalliğini doğrulamak için kullanılabilir. Bu doğrulamayı gerçekleştirme hakkında daha fazla bilgi için [OpenID Connect belirtimine](https://openid.net/specs/openid-connect-core-1_0.html)bakın. |
|`aio` | Donuk dize | Azure AD tarafından belirteç yeniden kullanımı için veri kaydetmek üzere kullanılan bir iç talep. Göz ardı edilmelidir.|
|`preferred_username` | Dize | Kullanıcıyı temsil eden birincil Kullanıcı adı. Belirtilen biçim olmadan bir e-posta adresi, telefon numarası veya genel Kullanıcı adı olabilir. Değeri değişebilir ve zaman içinde değişebilir. Değişebilir olduğundan, bu değer yetkilendirme kararları almak için kullanılmamalıdır. @No__t_0 kapsamı, bu talebi almak için gereklidir.|
|`email` | Dize | @No__t_0 talebi, bir e-posta adresine sahip konuk hesapları için varsayılan olarak mevcuttur.  Uygulamanız, `email` [isteğe bağlı talebi](active-directory-optional-claims.md)kullanarak yönetilen kullanıcılar (kaynakla aynı kiracıdan aynı) için e-posta talebi talep edebilir.  V 2.0 uç noktasında, uygulamanız `email` OpenID Connect kapsamına da talep edebilir. talebi almak için hem isteğe bağlı talebi hem de kapsamı istemeniz gerekmez.  E-posta talebi yalnızca kullanıcının profil bilgilerinde adreslenebilir postaları destekler. |
|`name` | Dize | @No__t_0 talebi, belirtecin konusunu tanımlayan, insan tarafından okunabilen bir değer sağlar. Değerin benzersiz olması garanti edilmez, değişebilir ve yalnızca görüntüleme amacıyla kullanılmak üzere tasarlanmıştır. @No__t_0 kapsamı, bu talebi almak için gereklidir. |
|`nonce`| Dize | Nonce, ıDP 'ye özgün/Yetkilendir isteğine dahil edilen parametreyle eşleşir. Eşleşmiyorsa, uygulamanız belirteci reddetmelidir. |
|`oid` | Dize, GUID | Bu örnekte, bir kullanıcı hesabı olan Microsoft Identity sistemindeki bir nesne için sabit tanımlayıcı. Bu KIMLIK, kullanıcıyı uygulamalar arasında benzersiz bir şekilde tanımlar. aynı kullanıcı için iki farklı uygulama oturumu açmak, `oid` talebinde aynı değeri alacaktır. Microsoft Graph, bu KIMLIĞI belirli bir kullanıcı hesabı için `id` özelliği olarak döndürür. @No__t_0 birden çok uygulamanın kullanıcıları ilişkilendirme yapmasına izin verdiğinden, bu talebi almak için `profile` kapsamı gerekir. Tek bir Kullanıcı birden fazla kiracıda varsa, kullanıcının her kiracıda farklı bir nesne KIMLIĞI içerdiğini ve Kullanıcı aynı kimlik bilgilerine sahip her hesapta oturum açtığı halde farklı hesaplar olarak kabul edileceğini unutmayın. @No__t_0 talebi bir GUID 'dir ve yeniden kullanılamaz. |
|`roles`| Dizeler dizisi | Oturum açan kullanıcıya atanan roller kümesi. |
|`rh` | Donuk dize |Belirteçleri yeniden doğrulamak için Azure tarafından kullanılan bir iç talep. Göz ardı edilmelidir. |
|`sub` | Dize, GUID | Belirtecin, uygulamanın kullanıcısı gibi bilgilerin hangi sorumluya ait olduğunu belirten sorumlu. Bu değer sabittir ve yeniden atanamaz veya tekrar kullanılamaz. Konu, belirli bir uygulama KIMLIĞI için benzersiz bir tanımlayıcıdır. Tek bir Kullanıcı iki farklı istemci kimliği kullanarak iki farklı uygulama üzerinde oturum açarsa, bu uygulamalar konu talebi için iki farklı değer alır. Bu, mimarinize ve gizlilik gereksinimlerinize bağlı olarak istenebilir veya olmayabilir. |
|`tid` | Dize, GUID | Kullanıcının ait olduğu Azure AD kiracısını temsil eden bir GUID. İş ve okul hesapları için GUID, kullanıcının ait olduğu kuruluşun sabit kiracı KIMLIĞIDIR. Kişisel hesaplar için değer `9188040d-6c67-4c5b-b112-36a304b66dad` ' dır. @No__t_0 kapsamı, bu talebi almak için gereklidir. |
|`unique_name` | Dize | Belirtecin konusunu tanımlayan ve okunabilir bir değer sunar. Bu değerin bir kiracı içinde benzersiz olması garanti edilmez ve yalnızca görüntüleme amacıyla kullanılmalıdır. Yalnızca v 1.0 `id_tokens` verilir. |
|`uti` | Donuk dize | Belirteçleri yeniden doğrulamak için Azure tarafından kullanılan bir iç talep. Göz ardı edilmelidir. |
|`ver` | Dize, 1,0 ya da 2,0 | İd_token sürümünü gösterir. |

## <a name="validating-an-id_token"></a>Bir id_token doğrulanıyor

Bir `id_token` doğrulamak, [bir erişim belirtecini doğrulamaya](access-tokens.md#validating-tokens) yönelik ilk adımla benzerdir. istemciniz doğru veren 'in belirteci geri gönderdiğini ve üzerinde oynanmadığını doğrulaması gerekir. @No__t_0 her zaman bir JWT olduğundan, bu belirteçleri doğrulamak için birçok kitaplık bulunur; bunlardan birini kendi başınıza yapmak yerine kullanmanızı öneririz.

Belirteci el ile doğrulamak için, [erişim belirtecini doğrulama](access-tokens.md#validating-tokens)konusunda bulunan adım ayrıntılarına bakın. Belirteçteki imza doğrulandıktan sonra, id_token içinde aşağıdaki talepler doğrulanmalıdır (Bunlar ayrıca belirteç doğrulama kitaplığınız tarafından da yapılabilir):

* Zaman damgaları: `iat`, `nbf` ve `exp` zaman damgaları, uygun şekilde geçerli zamandan önce veya sonra düşmelidir. 
* Hedef kitle: `aud` talebi, uygulamanızın uygulama KIMLIĞIYLE eşleşmelidir.
* Nonce: yükteki `nonce` talebi, ilk istek sırasında/yetkilendirmeli uç noktasına geçirilen nonce parametresiyle eşleşmelidir.

## <a name="next-steps"></a>Sonraki adımlar

* [Erişim belirteçleri](access-tokens.md) hakkında bilgi edinin
* [İsteğe bağlı talepler](active-directory-optional-claims.md)kullanarak id_token içindeki talepleri özelleştirin.
