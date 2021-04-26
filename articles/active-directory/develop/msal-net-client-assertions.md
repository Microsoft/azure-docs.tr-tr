---
title: İstemci onayları (MSAL.NET) | Mavisi
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı 'ndaki gizli istemci uygulamaları için imzalanmış istemci onayları desteği (MSAL.NET) hakkında bilgi edinin.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/18/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 000aeffa982c59f1efbb6ecae73f6b48e95f981e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967328"
---
# <a name="confidential-client-assertions"></a>Gizli istemci onayları

Gizli istemci uygulamalarının kimliğini kanıtlamak için Azure AD ile gizli bir parola değişimi yapın. Gizli dizi şu olabilir:
- Bir istemci parolası (uygulama parolası).
- Standart talepler içeren imzalı bir onaylama oluşturmak için kullanılan bir sertifika.

Bu gizli dizi doğrudan imzalı bir onaylama da olabilir.

MSAL.NET, gizli istemci uygulamasına kimlik bilgileri veya onaylama sağlamak için dört yöntem içerir:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> `WithClientAssertion()`Gizli istemci için belirteçleri almak üzere API kullanmak mümkün olsa da, daha gelişmiş olduğu ve yaygın olmayan çok sayıda senaryoyu işleyecek şekilde tasarlanan varsayılan olarak kullanılması önerilmez. API 'nin kullanılması `.WithCertificate()` msal.net bunu sizin için işlemesini sağlayacaktır. Bu API, gerekirse kimlik doğrulama isteğinizi özelleştirmenizi sağlar ancak tarafından oluşturulan varsayılan onaylama, `.WithCertificate()` çoğu kimlik doğrulama senaryosunda yeterli olacaktır. Bu API, MSAL.NET imzalama işlemini dahili olarak gerçekleştiremediği bazı senaryolarda geçici çözüm olarak da kullanılabilir.

### <a name="signed-assertions"></a>İmzalı Onaylamalar

İmzalı bir istemci onaylama işlemi, Azure AD tarafından istenen kimlik doğrulama taleplerini uygulanan, Base64 kodlamalı bir yük ile imzalı JWT biçimini alır. Bunu kullanmak için:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Ayrıca, onaylama formunu da kullanabilirsiniz ve bu da onay için tam zamanında işlem yapmanızı sağlar:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(() => { return GetSignedClientAssertion(); } )
                                          .Build();
```

İmzalı onaylama sırasında [Azure AD tarafından beklenen talepler](active-directory-certificate-credentials.md) şunlardır:

Talep türü | Değer | Açıklama
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | "AUD" (hedef kitle) talebi, JWT 'nin hedeflenen alıcılarını tanımlar (burada Azure AD) bkz. [RFC 7519, Bölüm 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  Bu durumda, bu alıcı, oturum açma sunucusudur (login.microsoftonline.com).
exp | 1601519414 | "Exp" (sona erme saati) talebi, JWT 'ın işlenmek üzere kabul edilmemelidir. Bkz. [RFC 7519, Bölüm 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Bu, onaylamanın daha sonra kullanılmasını sağlar, bu nedenle en çok kısa 5-10 dakika sonra devam edin `nbf` .  Azure AD Şu anda kısıtlamalar yerleştirmez `exp` . 
ğe | ClientID | "ISS" (veren) talebi, bu durumda istemci uygulamanız için JWT veren sorumluyu tanımlar.  GUID uygulama KIMLIĞINI kullanın.
JTI dili | (GUID) | "JTI" (JWT ID) talebi, JWT için benzersiz bir tanımlayıcı sağlar. Tanımlayıcı değeri, aynı değerin yanlışlıkla farklı bir veri nesnesine atanabileceği bir olasılık olmasını sağlayacak şekilde atanmalıdır; uygulama birden çok veren kullanıyorsa, çarpışmaların de farklı verenler tarafından üretilen değerler arasında engellenmeleri gerekır. "JTI" değeri büyük/küçük harfe duyarlı bir dizedir. [RFC 7519, Bölüm 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
NBF | 1601519114 | "NBF" (before) talebi, JWT 'ın işlenmek üzere kabul edilmeden önce geçen süreyi tanımlar. [RFC 7519, Bölüm 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  Geçerli sürenin kullanılması uygun. 
alt | ClientID | "Sub" (konu) talebi, bu örnekte de, JWT konusunun konusunu tanımlar. İle aynı değeri kullanın `iss` . 

Bu taleplerin nasıl yapıldığını gösteren bir örnek aşağıda verilmiştir:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

İmzalı bir istemci onayını oluşturma:

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims()).ToString()));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Alternatif Yöntem

Ayrıca sizin için onay oluşturmak üzere [ onWebTokensMicrosoft.IdentityModel.Js](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) kullanma seçeneğiniz de vardır. Kod, aşağıdaki örnekte gösterildiği gibi daha zarif olacaktır:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

İmzalı istemci onayınız olduktan sonra, MSAL API 'leriyle aşağıda gösterildiği gibi kullanabilirsiniz.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>Withclientclaim

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` Varsayılan olarak, Azure AD ile beklenen talepleri ve göndermek istediğiniz ek istemci taleplerini içeren imzalı bir onaylama işlemi oluşturacaktır. Bunun nasıl yapılacağını gösteren bir kod parçacığı aşağıda verilmiştir.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Geçirdiğiniz sözlüklerdeki taleplerden biri zorunlu taleplerden biriyle aynıysa, ek talebin değeri hesaba alınacaktır. Bu, MSAL.NET tarafından hesaplanan talepleri geçersiz kılar.

Azure AD tarafından beklenen zorunlu talepler dahil olmak üzere kendi taleplerinizi sağlamak istiyorsanız, `false` parametresi için geçirin `mergeWithDefaultClaims` .
