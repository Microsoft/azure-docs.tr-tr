---
title: JavaScript tek sayfalı uygulamayı örtük yetki kodu akışına geçir | Mavisi
titleSuffix: Microsoft identity platform
description: MSAL.js 1. x ile bir JavaScript SPA 'yı güncelleştirme ve MSAL.js 2. x ve kimlik doğrulama kodu akışı ile PKI CE ve CORS desteği için örtük verme akışı.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 3c11334fe1b4d77be6e64febfc1d3de6efa302c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100365949"
---
# <a name="migrate-a-javascript-single-page-app-from-implicit-grant-to-auth-code-flow"></a>Bir JavaScript tek sayfalı uygulamayı örtük kimlik doğrulama kod akışına geçirme

JavaScript için Microsoft kimlik doğrulama kitaplığı (MSAL.js) v 2.0, Microsoft Identity platformunda tek sayfalı uygulamalar için PKCE ve CORS ile yetkilendirme kodu akışına yönelik destek sunar. MSAL.js 1. x uygulamanızı örtük olarak MSAL.js 2.0 + (herbundan sonra *2. x*) ve Auth kod akışı kullanarak geçirmek için aşağıdaki bölümlerde bulunan adımları izleyin.

MSAL.js 2. x, dolaylı verme akışı yerine tarayıcıda yetkilendirme kodu akışını destekleyerek MSAL.js 1. x ' i geliştirir. MSAL.js 2. x örtük **akışı desteklemez.**

## <a name="migration-steps"></a>Geçiş adımları

Uygulamanızı 2. x ve kimlik doğrulama kod akışına MSAL.js güncelleştirmek için üç birincil adım vardır:

1. [Uygulama kaydı](#switch-redirect-uris-to-spa-platform) YENIDEN yönlendirme URI 'leri **Web** platformundan **tek sayfalı uygulama** platformuna geçirin.
1. [Kodunuzu](#switch-redirect-uris-to-spa-platform) MSAL.js 1. x ile **2. x** arasında güncelleştirin.
1. Kaydı paylaşan tüm uygulamalar 2. x MSAL.js ve kimlik doğrulama kodu akışına güncelleştirildiği zaman, uygulama kaydındaki [örtük izni](#disable-implicit-grant-settings) devre dışı bırakın.

Aşağıdaki bölümlerde her bir adım ek ayrıntılarla açıklanır.

## <a name="switch-redirect-uris-to-spa-platform"></a>Yeniden yönlendirme URI 'Lerini SPA platformuna geçir

Uygulamalarınız için mevcut uygulama kaydınızı kullanmaya devam etmek istiyorsanız, kaydın yeniden yönlendirme URI 'Lerini SPA platformuna güncelleştirmek için Azure portal kullanın. Bunun yapılması, kayıt kullanan uygulamalar için PKI ve CORS desteğiyle yetkilendirme kodu akışını sağlar (yine de uygulamanızın kodunu MSAL.js v2. x ' e güncelleştirmeniz gerekir).

Şu anda **Web** platformu yeniden yönlendirme URI 'leriyle yapılandırılmış olan uygulama kayıtları için şu adımları izleyin:

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> oturum açın ve **Azure Active Directory** kiracınızı seçin.
1. **Uygulama kayıtları**, uygulamanızı ve sonra **kimlik doğrulamasını** seçin.
1. **Yeniden yönlendirme URI 'leri** altındaki **Web** platformu kutucuğunda, URI 'larınızı geçirmeniz gerektiğini belirten uyarı başlığını seçin.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-01-implicit-warning-banner.png" alt-text="Azure portal web uygulaması kutucuğunda örtük akış uyarı başlığı":::
1. *Yalnızca* uygulamaları 2. x MSAL.js kullanacağı yeniden yönlendirme URI 'lerini seçin ve ardından **Yapılandır**' ı seçin.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-02-select-redirect-uri.png" alt-text="Azure portal içindeki SPA bölmesinde URI bölmesini yeniden yönlendir bölmesini seçin":::

Bu yeniden yönlendirme URI 'Leri artık **tek sayfalı uygulama** platformu kutucuğunda görünmeli ve bu URI 'ler için yetkilendirme kodu akışında CORS desteğinin etkin olduğunu gösterir.

:::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-03-spa-redirect-uri-tile.png" alt-text="Azure portal 'de uygulama kaydında tek sayfalı uygulama kutucuğu":::

Ayrıca, var olan kaydlarınızın yeniden yönlendirme URI 'Lerini güncelleştirmek yerine [Yeni bir uygulama kaydı da oluşturabilirsiniz](scenario-spa-app-registration.md) .

## <a name="update-your-code-to-msaljs-2x"></a>Kodunuzu 2. x MSAL.js için güncelleştirin

MSAL 1. x içinde, aşağıdaki gibi bir [Userbir TApplication][msal-js-useragentapplication] başlatarak bir uygulama örneği oluşturdunuz:

```javascript
// MSAL 1.x
import * as msal from "msal";

const msalInstance = new msal.UserAgentApplication(config);
```

MSAL 2. x içinde, bir [Publicclientapplication][msal-js-publicclientapplication]yerine Initialize:

```javascript
// MSAL 2.x
import * as msal from "@azure/msal-browser";

const msalInstance = new msal.PublicClientApplication(config);
```

Uygulamanıza MSAL 2. x ekleme hakkında daha fazla bilgi için bkz. [öğretici: Kullanıcı oturum açma ve kimlik doğrulama kod akışı kullanarak JavaScript tek sayfalı uygulama (Spa) MICROSOFT Graph API 'sini çağırma](tutorial-v2-javascript-auth-code.md).

Kodunuzda yapmanız gerekebilecek ek değişiklikler için GitHub 'da [geçiş kılavuzuna](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/v1-migration.md) bakın.

## <a name="disable-implicit-grant-settings"></a>Örtük verme ayarlarını devre dışı bırak

Bu uygulama kaydını ve onun istemci KIMLIĞINI MSAL 2. x ve yetkilendirme kodu akışına kullanan tüm üretim uygulamalarınızı güncelleştirdikten sonra, uygulama kaydında örtük verme ayarlarının işaretini kaldırmanız gerekir.

Uygulama kaydında örtük verme ayarlarının işaretini kaldırdığınızda, kayıt ve istemci KIMLIĞI kullanan tüm uygulamalar için örtük akış devre dışı bırakılır.

Tüm uygulamalarınızı MSAL.js 2. x ve [Publicclientapplication][msal-js-publicclientapplication]' a güncelleştirdikten önce **örtük verme akışını devre dışı** bırakın.

## <a name="next-steps"></a>Sonraki adımlar

Örtülü ve kimlik doğrulama kod akışları arasındaki farklılıklar da dahil olmak üzere, yetkilendirme kodu akışı hakkında daha fazla bilgi edinmek için bkz. [Microsoft Identity platform ve OAuth 2,0 yetkilendirme kodu akışı](v2-oauth2-auth-code-flow.md).

Microsoft Identity platformunda JavaScript tek sayfalı uygulama geliştirmeyi daha ayrıntılı bir şekilde incelemek isterseniz, çok parçalı [Senaryo: tek sayfalı uygulama](scenario-spa-overview.md) bir makale serisi, başlamanıza yardımcı olabilir.

<!-- LINKS - external -->
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html
