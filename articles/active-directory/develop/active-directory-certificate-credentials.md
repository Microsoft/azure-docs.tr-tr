---
title: Microsoft Identity platform sertifikası kimlik bilgileri
titleSuffix: Microsoft identity platform
description: Bu makalede, uygulama kimlik doğrulaması için sertifika kimlik bilgilerinin kaydı ve kullanımı ele alınmaktadır.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: cfbcc8523ff1d5858317a3654b58ec7b2d23607a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582035"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft Identity Platform uygulaması kimlik doğrulama sertifikası kimlik bilgileri

Microsoft Identity platformu, bir uygulamanın kimlik doğrulaması için kendi kimlik bilgilerini kullanmasını sağlar; örneğin, OAuth 2,0  [istemci kimlik bilgileri verme](v2-oauth2-client-creds-grant-flow.md) akışı ve [Şirket adına](v2-oauth2-on-behalf-of-flow.md) (OBO) akışı.

Uygulamanın kimlik doğrulaması için kullanabileceği bir kimlik bilgisi biçimi, uygulamanın sahip olduğu bir sertifikayla imzalanmış bir [JSON Web Token](./security-tokens.md#json-web-tokens-and-claims) (JWT) onaysıdır.

## <a name="assertion-format"></a>Onaylama biçimi

Onaylama işlemlerini hesaplamak için, tercih ettiğiniz dilde birçok JWT kitaplığı kullanabilirsiniz. [Bu, `.WithCertificate()` kullanarak bunu destekler ](msal-net-client-assertions.md). Bilgiler, [üst bilgisinde](#header), [taleplerde](#claims-payload)ve [imzasında](#signature)belirteç tarafından taşınır.

### <a name="header"></a>Üst bilgi

| Parametre |  Görüyorum |
| --- | --- |
| `alg` | **RS256** olmalıdır |
| `typ` | **JWT** olmalıdır |
| `x5t` | X. 509.440 sertifika karmasıdır (sertifikanın SHA-1 *parmak izi* olarak da bilinir) Base64url dize değeri olarak kodlanan onaltılık gösterimi. Örneğin, (onaltılı) bir X. 509.440 sertifikası karması verildiğinde `84E05C1D98BCE3A5421D225B140B36E86A3D5534` , `x5t` talep `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64url) olacaktır. |

### <a name="claims-payload"></a>Talepler (yük)

Talep türü | Değer | Açıklama
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | "AUD" (hedef kitle) talebi, JWT 'nin hedeflenen alıcılarını tanımlar (burada Azure AD) bkz. [RFC 7519, Bölüm 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  Bu durumda, bu alıcı, oturum açma sunucusudur (login.microsoftonline.com).
exp | 1601519414 | "Exp" (sona erme saati) talebi, JWT 'ın işlenmek üzere kabul edilmemelidir. Bkz. [RFC 7519, Bölüm 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Bu, onaylamanın daha sonra kullanılmasını sağlar, bu nedenle en çok kısa 5-10 dakika sonra devam edin `nbf` .  Azure AD Şu anda kısıtlamalar yerleştirmez `exp` . 
ğe | ClientID | "ISS" (veren) talebi, bu durumda istemci uygulamanız için JWT veren sorumluyu tanımlar.  GUID uygulama KIMLIĞINI kullanın.
JTI dili | (GUID) | "JTI" (JWT ID) talebi, JWT için benzersiz bir tanımlayıcı sağlar. Tanımlayıcı değeri, aynı değerin yanlışlıkla farklı bir veri nesnesine atanabileceği bir olasılık olmasını sağlayacak şekilde atanmalıdır; uygulama birden çok veren kullanıyorsa, çarpışmaların de farklı verenler tarafından üretilen değerler arasında engellenmeleri gerekır. "JTI" değeri büyük/küçük harfe duyarlı bir dizedir. [RFC 7519, Bölüm 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
NBF | 1601519114 | "NBF" (before) talebi, JWT 'ın işlenmek üzere kabul edilmeden önce geçen süreyi tanımlar. [RFC 7519, Bölüm 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  Geçerli sürenin kullanılması uygun. 
alt | ClientID | "Sub" (konu) talebi, bu örnekte de, JWT konusunun konusunu tanımlar. İle aynı değeri kullanın `iss` . 

### <a name="signature"></a>İmza

İmza, [JSON Web Token RFC7519 belirtiminde](https://tools.ietf.org/html/rfc7519)açıklandığı gibi sertifika uygulanarak hesaplanır.

## <a name="example-of-a-decoded-jwt-assertion"></a>Kodu çözülen JWT onaylama örneği

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Kodlanmış JWT onaylama örneği

Aşağıdaki dize, kodlanmış onaylama işlemi örneğidir. Dikkatlice bakarsanız, noktalarla () ayırarak üç bölüm olduğunu fark edersiniz `.` :

* İlk bölüm *üstbilgiyi* kodluyor
* İkinci bölüm, *talepleri* (yük) kodluyor
* Son bölüm, ilk iki bölümün içeriğinden sertifikalar ile hesaplanan *imzadan*

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Microsoft Identity platformu ile sertifikanızı kaydedin

Aşağıdaki yöntemlerden herhangi birini kullanarak sertifika kimlik bilgisini Microsoft Identity platformunda istemci uygulamasıyla Azure portal ilişkilendirebilirsiniz:

### <a name="uploading-the-certificate-file"></a>Sertifika dosyası karşıya yükleniyor

İstemci uygulaması için Azure uygulama kaydı:
1. **Sertifikalar & parolaları**' nı seçin.
2. **Sertifikayı karşıya yükle** ' ye tıklayın ve karşıya yüklenecek sertifika dosyasını seçin.
3. **Ekle**'ye tıklayın.
  Sertifika karşıya yüklendikten sonra, parmak izi, başlangıç tarihi ve süre sonu değerleri görüntülenir.

### <a name="updating-the-application-manifest"></a>Uygulama bildirimini güncelleştirme

Bir sertifika aldıktan sonra, bu değerleri Hesapla:

- `$base64Thumbprint` -Base64 kodlu sertifika karmasının değeri
- `$base64Value` -Base64 kodlamalı sertifika ham verileri değeri

Uygulama bildiriminde () anahtarı tanımlamak için bir GUID girin `$keyId` .

İstemci uygulaması için Azure uygulama kaydı:
1. Uygulama bildirimini açmak için **bildirim** ' ı seçin.
2. Aşağıdaki şemayı kullanarak *Keycredentials* özelliğini yeni sertifika bilgilerinizle değiştirin.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Düzenlemeleri uygulama bildiriminde kaydedin ve sonra bildirimi Microsoft Identity platform 'a yükleyin.

   `keyCredentials`Özelliği çok değerli olduğundan, daha zengin anahtar yönetimi için birden fazla sertifika yükleyebilirsiniz.
   
## <a name="using-a-client-assertion"></a>İstemci onayı kullanma

İstemci onayları, bir istemci parolasının kullanıldığı her yerde kullanılabilir.  Örneğin, [yetkilendirme kodu akışında](v2-oauth2-auth-code-flow.md), `client_secret` isteğin uygulamanızdan geldiğini kanıtlamak için bir de geçirebilirsiniz. Bunu ve parametreleri ile değiştirebilirsiniz `client_assertion` `client_assertion_type` . 

| Parametre | Değer | Açıklama|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| Bu, bir sertifika kimlik bilgisi kullandığınızı belirten sabit bir değerdir. |
|`client_assertion`| JWT |Bu, yukarıda oluşturulan JWT. |

## <a name="next-steps"></a>Sonraki adımlar

[Msal.NET kitaplığı, bu senaryoyu](msal-net-client-assertions.md) tek bir kod satırında işler.

GitHub 'da [Microsoft Identity platform kod örneğini kullanan .NET Core Daemon konsol uygulaması](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) , bir uygulamanın kimlik doğrulaması için kendi kimlik bilgilerini nasıl kullandığını gösterir. Ayrıca PowerShell cmdlet 'ini kullanarak [kendinden imzalı bir sertifikayı nasıl oluşturabileceğiniz](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) de gösterilmektedir `New-SelfSignedCertificate` . Ayrıca, sertifika oluşturmak, parmak izini hesaplamak ve daha fazlasını yapmak için örnek depoda [uygulama oluşturma komut dosyalarını](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) da kullanabilirsiniz.
