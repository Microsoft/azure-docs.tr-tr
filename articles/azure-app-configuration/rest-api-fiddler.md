---
title: Azure Active Directory REST API-Fiddler kullanarak test etme
description: Azure uygulama yapılandırmasını test etmek için Fiddler 'ı kullanın REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 1142aa25212d87c5484963cda4e172df3d1fbafc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932617"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>Fiddler kullanarak Azure uygulama yapılandırma REST API test etme

[Fiddler](https://www.telerik.com/fiddler)'ı kullanarak REST API test etmek için isteklerinizin [kimlik doğrulaması](./rest-api-authentication-hmac.md) için gereken http üst bilgilerini eklemeniz gerekir. REST API test etmek için Fiddler 'ı yapılandırma, otomatik olarak kimlik doğrulama üst bilgileri oluşturma:

1. TLS 1,2 ' nin izin verilen bir protokol olduğundan emin olun:
    1. **Araçlar**  >  **Seçenekler**  >  **https**) bölümüne gidin.
    1. **Https trafiğinin şifresini çöz** ' ün işaretli olduğundan emin olun.
    1. Protokoller listesinde, yoksa **TLS 1.2** ' ı ekleyin.
1. **Fiddler betik düzenleyicisini** açın veya Fiddler içinde **CTRL-R** tuşlarına basın
1. `Handlers`İşlevden önce sınıfın içine aşağıdaki kodu ekleyin `OnBeforeRequest`

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. İşlevin sonuna aşağıdaki kodu ekleyin `OnBeforeRequest` . Erişim anahtarını TODO açıklamasıyla belirtilen şekilde güncelleştirin.

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. İstek oluşturmak ve göndermek için [Fiddler 'In besteci](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest) kullanma
