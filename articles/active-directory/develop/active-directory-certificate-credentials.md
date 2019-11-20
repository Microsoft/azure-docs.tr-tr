---
title: Azure AD 'de sertifika kimlik bilgileri
titleSuffix: Microsoft identity platform
description: Bu makalede, uygulama kimlik doğrulaması için sertifika kimlik bilgilerinin kaydı ve kullanılması anlatılmaktadır
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fca872d639ab5c2d4053656cdd3e68a59fdc1e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473973"
---
# <a name="certificate-credentials-for-application-authentication"></a>Uygulama kimlik doğrulaması için sertifika kimlik bilgileri

Azure Active Directory (Azure AD), bir uygulamanın kimlik doğrulaması için kendi kimlik bilgilerini kullanmasını sağlar; örneğin, OAuth 2,0 Istemci kimlik bilgileri verme akışı ([v 1.0](v1-oauth2-client-creds-grant-flow.md), [v 2.0](v2-oauth2-client-creds-grant-flow.md)) ve şirket adına akış ([v 1.0](v1-oauth2-on-behalf-of-flow.md), [v 2.0](v2-oauth2-on-behalf-of-flow.md)).

Uygulamanın kimlik doğrulaması için kullanabileceği bir kimlik bilgisi biçimi, uygulamanın sahip olduğu bir sertifikayla imzalanmış bir JSON Web Token (JWT) onaysıdır.

## <a name="assertion-format"></a>Onaylama biçimi
Onaylama işlemlerini hesaplamak için, birçok [JSON Web Token](https://jwt.ms/) kütüphanesinden birini dilediğiniz dilde kullanabilirsiniz. Belirteç tarafından taşınan bilgiler aşağıdaki gibidir:

### <a name="header"></a>Üst bilgi

| Parametre |  Görüyorum |
| --- | --- |
| `alg` | **RS256** olmalıdır |
| `typ` | **JWT** olmalıdır |
| `x5t` | X. 509.440 sertifikası SHA-1 parmak izi olmalıdır |

### <a name="claims-payload"></a>Talepler (yük)

| Parametre |  Açıklamalar |
| --- | --- |
| `aud` | Hedef kitle: **https://login.microsoftonline.com/*tenant_Id*/OAuth2/Token** olmalıdır |
| `exp` | Sona erme tarihi: belirtecin süresi dolduğunda tarih. Süre, belirteç geçerliliği sona erene kadar 1 Ocak 1970 (1970-01-01T0:0: 0Z) UTC 'den saniye sayısı olarak gösterilir.|
| `iss` | Veren: client_id (istemci hizmetinin uygulama KIMLIĞI) olmalıdır |
| `jti` | GUID: JWT KIMLIĞI |
| `nbf` | Önünde değil: belirtecin kullanılacağı tarih. Süre, belirtecin verilme zamanına kadar 1 Ocak 1970 (1970-01-01T0:0: 0Z) UTC 'den saniye sayısı olarak gösterilir. |
| `sub` | Konu: `iss`için, client_id (istemci hizmetinin uygulama KIMLIĞI) olmalıdır |

### <a name="signature"></a>İmza

İmza, [JSON Web Token RFC7519 belirtiminde](https://tools.ietf.org/html/rfc7519) açıklandığı gibi sertifika tarafından hesaplanır

## <a name="example-of-a-decoded-jwt-assertion"></a>Kodu çözülen JWT onaylama örneği

```
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

Aşağıdaki dize, kodlanmış onaylama işlemi örneğidir. Dikkatlice bakarsanız, noktalarla (.) ayırarak üç bölüm olduğunu fark edersiniz:
* İlk bölüm üstbilgiyi kodluyor
* İkinci bölüm yükün kodluyor
* Son bölüm, ilk iki bölümün içeriğinden sertifikalar ile hesaplanan imzadan

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-azure-ad"></a>Azure AD 'ye sertifikanızı kaydetme

Aşağıdaki yöntemlerden herhangi birini kullanarak sertifika kimlik bilgilerini Azure AD 'deki istemci uygulamasıyla Azure portal ilişkilendirebilirsiniz:

### <a name="uploading-the-certificate-file"></a>Sertifika dosyası karşıya yükleniyor

İstemci uygulaması için Azure uygulama kaydı:
1. **Sertifikalar & parolaları**' nı seçin. 
2. **Sertifikayı karşıya yükle** ' ye tıklayın ve karşıya yüklenecek sertifika dosyasını seçin.
3. **Ekle**'ye tıklayın.
  Sertifika karşıya yüklendikten sonra, parmak izi, başlangıç tarihi ve süre sonu değerleri görüntülenir. 

### <a name="updating-the-application-manifest"></a>Uygulama bildirimini güncelleştirme

Sertifikayı tutan bir sertifika varsa şunları hesaplamanız gerekir:

- Sertifika karmasının Base64 kodlaması olan `$base64Thumbprint`
- sertifika ham verilerinin Base64 kodlaması olan `$base64Value`

Ayrıca uygulama bildiriminde anahtarı tanımlamak için bir GUID sağlamanız gerekir (`$keyId`).

İstemci uygulaması için Azure uygulama kaydı:
1. Uygulama bildirimini açmak için **bildirim** ' ı seçin.
2. Aşağıdaki şemayı kullanarak *Keycredentials* özelliğini yeni sertifika bilgilerinizle değiştirin.

   ```
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
3. Düzenlemeleri uygulama bildiriminde kaydedin ve sonra bildirimi Azure AD 'ye yükleyin. 

   `keyCredentials` özelliği çok değerli olduğundan, daha zengin anahtar yönetimi için birden fazla sertifika yükleyebilirsiniz.
   
## <a name="code-sample"></a>Kod örneği

> [!NOTE]
> X5T üst bilgisini sertifikanın karmasını kullanarak hesaplamanız ve bunu bir Base64 dizesine dönüştürmeniz gerekir. İçinde C# şuna benzer bir şey şöyle görünür: `System.Convert.ToBase64String(cert.GetCertHash());`

[Sertifikalarla birlikte Daemon uygulamalarında Azure AD 'de](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) kimlik doğrulaması yapmak için kod örneği, bir uygulamanın kimlik doğrulaması için kendi kimlik bilgilerini nasıl kullandığını gösterir. Ayrıca, `New-SelfSignedCertificate` PowerShell komutunu kullanarak [kendinden imzalı bir sertifikayı nasıl oluşturabileceğiniz](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) de gösterilmektedir. Ayrıca, sertifikaları oluşturmak, parmak izini hesaplamak ve daha fazlasını yapmak için [uygulama oluşturma komut dosyalarını](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md) da kullanabilirsiniz.
