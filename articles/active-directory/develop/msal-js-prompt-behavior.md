---
title: Etkileşimli isteklerde istem davranışı (JavaScript için Microsoft kimlik doğrulama kitaplığı)
titleSuffix: Microsoft identity platform
description: JavaScript için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL. js) kullanarak etkileşimli çağrılarındaki istem davranışını özelleştirme hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d6c4415a3eeb28c999d95b838c6dd7c0f6e606
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803020"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>MSAL. js etkileşimli isteklerindeki istem davranışı

Bir Kullanıcı birden çok kullanıcı hesabıyla etkin bir Azure AD oturumu kurmışsa, varsayılan olarak Azure AD oturum açma sayfasında oturum açmadan önce kullanıcıdan bir hesap seçmesini istenir. Azure AD ile yalnızca tek bir kimliği doğrulanmış oturum varsa, kullanıcılar bir hesap seçme deneyimi görmez.

MSAL. js kitaplığı (v 0.2.4 'den başlayarak) etkileşimli istekler (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` ve `acquireTokenPopup`) sırasında istem parametresi göndermez ve bu nedenle herhangi bir istem davranışını zorlamaz. `acquireTokenSilent` yöntemi kullanılarak sessiz Belirteç istekleri için, MSAL. js, `none`olarak ayarlanan bir istem parametresi geçirir.

Uygulama senaryonuza bağlı olarak, metotlara geçirilen istek parametrelerinde Prompt parametresini ayarlayarak etkileşimli isteklerin istem davranışını kontrol edebilirsiniz. Örneğin, hesap seçme deneyimini çağırmak istiyorsanız:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Azure AD kimlik doğrulaması sırasında aşağıdaki istem değerleri geçirilebilir:

**oturum aç:** Bu değer, kullanıcının kimlik doğrulama isteğine kimlik bilgilerini girmesini zorlayacaktır.

**select_account:** Bu değer, kullanıcıya oturumdaki tüm hesapların dökümünü alacak bir hesap seçme deneyimi sağlar.

**onay:** Bu değer, kullanıcıların uygulamaya izin vermesini sağlayan OAuth onay iletişim kutusunu çağırır.

**hiçbiri:** Bu değer, kullanıcının etkileşimli bir istem görmemesini sağlayacaktır. Beklenmedik davranışlara sahip olduğundan, bu değerin MSAL. js içindeki etkileşimli yöntemlere iletilmesi önerilir. Bunun yerine, sessiz çağrılar elde etmek için `acquireTokenSilent` yöntemini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

MSAL. js kitaplığı tarafından kullanılan [OAuth 2,0 örtük izin](v2-oauth2-implicit-grant-flow.md) protokolündeki `prompt` parametresi hakkında daha fazla bilgi edinin.
