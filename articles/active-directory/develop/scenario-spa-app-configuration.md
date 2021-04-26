---
title: Tek sayfalı uygulamayı yapılandırma | Mavisi
titleSuffix: Microsoft identity platform
description: Tek sayfalı bir uygulama (uygulamanın kod yapılandırması) oluşturmayı öğrenin
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: fe73832ec5eaee62a2dc2d397c12f82334e2efd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010720"
---
# <a name="single-page-application-code-configuration"></a>Tek sayfalı uygulama: kod yapılandırma

Tek sayfalı uygulamanız (SPA) için kodu yapılandırmayı öğrenin.

## <a name="microsoft-libraries-supporting-single-page-apps"></a>Tek sayfalı uygulamaları destekleyen Microsoft kitaplıkları 

Aşağıdaki Microsoft kitaplıkları tek sayfalı uygulamaları destekler:

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="application-code-configuration"></a>Uygulama kodu yapılandırması

Bir MSAL kitaplığında, kitaplık başlatma sırasında uygulama kayıt bilgileri yapılandırma olarak geçirilir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Yapılandırılabilir seçenekler hakkında daha fazla bilgi için bkz. [MSAL.jsuygulama başlatma ](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryodaki bir sonraki makaleye geçin, [oturum açın ve oturumu kapatın](scenario-spa-sign-in.md).
