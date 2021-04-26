---
title: MSAL.js hataları ve özel durumları günlüğe kaydetme
titleSuffix: Microsoft identity platform
description: MSAL.js hataları ve özel durumları günlüğe kaydetme hakkında bilgi edinin
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d7463e3376847a219750254548c25ca79f4cdfb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954826"
---
# <a name="logging-in-msaljs"></a>MSAL.js’de oturum açma

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>MSAL.js oturum açmayı yapılandırma

Örnek oluşturmak için yapılandırma sırasında bir günlükçü nesnesi geçirerek MSAL.js (JavaScript) oturumunu etkinleştirin `UserAgentApplication` . Bu günlükçü nesnesi aşağıdaki özelliklere sahiptir:

- `localCallback`: geliştirici tarafından günlükleri özel bir biçimde tüketmek ve yayımlamak için kullanılabilecek bir geri çağırma örneği. Günlükleri yeniden yönlendirmek istediğiniz yönteme bağlı olarak localCallback yöntemini uygulayın.
- `level` (isteğe bağlı): yapılandırılabilir günlük düzeyi. Desteklenen günlük düzeyleri şunlardır: `Error` , `Warning` , `Info` , ve `Verbose` . Varsayılan değer: `Info`.
- `piiLoggingEnabled` (isteğe bağlı): true olarak ayarlanırsa, kişisel ve kurumsal verileri günlüğe kaydeder. Bu, varsayılan olarak, uygulamanızın kişisel verileri günlüğe almamasını sağlamak için false 'tur. Kişisel veri günlükleri hiçbir şekilde konsol, Logcat veya NSLog gibi varsayılan çıkışlara yazılmaz.
- `correlationId` (isteğe bağlı): isteği hata ayıklama amacıyla Yanıtla eşlemek için kullanılan benzersiz bir tanımlayıcı. Varsayılan olarak RFC4122 sürüm 4 GUID (128 bit) olur.

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
    system: {
        logger: new Msal.Logger(
            loggerCallback , {
                level: Msal.LogLevel.Verbose,
                piiLoggingEnabled: false,
                correlationId: '1234'
            }
        )
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla kod örneği için [Microsoft Identity platform kod örneklerine](sample-v2-code.md)bakın.