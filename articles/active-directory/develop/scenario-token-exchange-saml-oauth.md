---
title: Azure Active Directory 'de SAML ve OıDC/OAuth ile Microsoft Identity platform belirteci değişim senaryosu
description: Azure Active Directory 'de SAML ve OıDC/OAuth ile çalışırken ortak belirteç değişim senaryoları hakkında bilgi edinin.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 812bf7bd68362667fcd1b636d0d28fdbb21c1409
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582358"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>SAML ve OıDC/OAuth ile Microsoft Identity platform belirteci değişim senaryoları

SAML ve OpenID Connect (OıDC)/OAuth, tek Sign-On (SSO) uygulamak için kullanılan popüler protokollerdir. Bazı uygulamalar yalnızca SAML uygulayabilir ve diğerleri yalnızca OıDC/OAuth uygulayabilir. Her iki protokol de gizli dizileri iletmek için belirteçleri kullanır. SAML hakkında daha fazla bilgi edinmek için bkz. [tek Sign-On SAML Protokolü](single-sign-on-saml-protocol.md). OıDC/OAuth hakkında daha fazla bilgi edinmek için bkz. [Microsoft Identity platformunda OAuth 2,0 ve OpenID Connect protokolleri](active-directory-v2-protocols.md).

Bu makalede, bir uygulamanın SAML uyguladığı ancak OıDC/OAuth kullanan Graph API çağırdığı yaygın bir senaryo özetlenmektedir. Bu senaryoyla çalışan kişiler için temel kılavuz sağlanır.

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>Senaryo: bir SAML belirteciniz var ve Graph API çağırmak istiyorsunuz
SAML ile birçok uygulama uygulanır. Ancak, Graph API OıDC/OAuth protokollerini kullanır. Bir SAML uygulamasına OıDC/OAuth işlevselliği eklemek çok basit olmasa da mümkündür. Bir uygulamada OAuth işlevselliği kullanılabilir olduğunda Graph API kullanılabilir.

Genel strateji, OıDC/OAuth yığınını uygulamanıza eklemektir. Her iki standardı da uygulayan bir oturum tanımlama bilgisi kullanabilirsiniz. Belirteci açıkça hiç değiş tokuş değilsiniz. Bir oturum tanımlama bilgisi üreten SAML ile ' de bir Kullanıcı oturum açtınız. Graph API bir OAuth akışını istediğinde, kimlik doğrulaması için oturum tanımlama bilgisini kullanırsınız. Bu strateji koşullu erişim denetimlerinin başarılı olduğunu ve kullanıcının yetkilendirildiğini varsayar.

> [!NOTE]
> OıDC/OAuth davranışı eklemek için önerilen kitaplık, Microsoft kimlik doğrulama Kitaplığı ' dır (MSAL). MSAL hakkında daha fazla bilgi edinmek için bkz. [Microsoft kimlik doğrulama kitaplığı 'Na genel bakış (msal)](msal-overview.md). Önceki kitaplığa Active Directory Authentication Library (ADAL) çağrılmıştı, ancak MSAL bunun yerine kullanılması önerilmez.

## <a name="next-steps"></a>Sonraki adımlar
- [Kimlik doğrulama akışları ve uygulama senaryoları](authentication-flows-app-scenarios.md)
