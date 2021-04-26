---
title: Etkileşimli istek istemi davranışı (MSAL.js) | Mavisi
titleSuffix: Microsoft identity platform
description: JavaScript için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL.js) kullanarak etkileşimli çağrılarındaki istem davranışını özelleştirmeyi öğrenin.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 85f5269c3c2616070b826ecc38735c31617db537
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85477558"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>MSAL.js etkileşimli isteklerde istem davranışı

Bir Kullanıcı birden çok kullanıcı hesabıyla etkin bir Azure AD oturumu kurmışsa, varsayılan olarak Azure AD oturum açma sayfasında oturum açmadan önce kullanıcıdan bir hesap seçmesini istenir. Azure AD ile yalnızca tek bir kimliği doğrulanmış oturum varsa, kullanıcılar bir hesap seçme deneyimi görmez.

MSAL.js kitaplığı (v 0.2.4 ' den başlayarak) etkileşimli istekler (,, ve) sırasında istem parametresi göndermez `loginRedirect` `loginPopup` ve bu `acquireTokenRedirect` `acquireTokenPopup` nedenle herhangi bir istem davranışını zorlamaz. Yöntemini kullanan sessiz Belirteç istekleri için `acquireTokenSilent` MSAL.js, olarak ayarlanmış bir istem parametresi geçirir `none` .

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

**hiçbiri:** Bu değer, kullanıcının etkileşimli bir istem görmemesini sağlayacaktır. Beklenmeyen davranışlara sahip olduğundan, bu değerin MSAL.js etkileşimli yöntemlere iletilmesi önerilir. Bunun yerine, `acquireTokenSilent` sessiz çağrılar elde etmek için yöntemini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

`prompt`MSAL.js kitaplığı 'nın kullandığı [OAuth 2,0 örtük verme](v2-oauth2-implicit-grant-flow.md) protokolündeki parametresi hakkında daha fazla bilgi edinin.
