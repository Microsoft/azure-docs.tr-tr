---
title: Tek sayfalı uygulama (uygulamanın kod yapılandırması)-Microsoft Identity platform
description: Tek sayfalı bir uygulama (uygulamanın kod yapılandırması) oluşturmayı öğrenin
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7b4fba03f9edf8a3f4e42b23c6a1b5e06518863
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891524"
---
# <a name="single-page-application---code-configuration"></a>Tek sayfalı uygulama kodu yapılandırması

Tek sayfalı uygulamanız (SPA) için kodu yapılandırmayı öğrenin.

## <a name="msal-libraries-supporting-implicit-flow"></a>Örtük akışı destekleyen MSAL kitaplıkları

Microsoft Identity platformu, sektörde önerilen güvenli uygulamaları kullanarak örtük akışı desteklemek için MSAL. js kitaplığı sağlar.  

Örtük akışı destekleyen kitaplıklar şunlardır:

| MSAL kitaplığı | Açıklama |
|--------------|--------------|
| ![MSAL. js](media/sample-v2-code/logo_js.png) <br/> [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | JavaScript veya SPA çerçeveleri kullanılarak oluşturulan, angular, Vue. js, yanıt verme. js vb. gibi tüm istemci tarafı Web uygulamaları için kullanılmak üzere düz JavaScript kitaplığı. |
| ![MSAL angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Angular çerçevesiyle oluşturulan tek sayfalı uygulamalarda kullanımı basitleştirmek için Core MSAL. js kitaplığı sarmalayıcısı. Bu kitaplık önizlemededir ve belirli angular sürümlerinde ve tarayıcılarında [bilinen sorunlar](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) vardır. |

## <a name="application-code-configuration"></a>Uygulama kodu yapılandırması

MSAL kitaplığı 'nda, uygulama kayıt bilgileri kitaplık başlatma sırasında yapılandırma olarak geçirilir.

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
Kullanılabilir yapılandırılabilir seçenekler hakkında daha fazla bilgi için bkz. [msal. js ile uygulamayı başlatma](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Oturum açma ve oturum kapatma](scenario-spa-sign-in.md)
