---
title: Uygulama çoklu oturum açmayı yapılandırma
description: Azure AD ile geliştirdiğiniz ve kayıt yaptığınız özel bir uygulama için çoklu oturum açmayı yapılandırma.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 60a07145e38c19f577ad9864efb599e7b244aa83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653720"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Bir uygulama için çoklu oturum açmayı yapılandırma

OpenID Connect, SAML 2,0 veya WS-beslemelerine yönelik Azure AD aracılığıyla federasyona katılarak, uygulamanızda federe çoklu oturum açmayı (SSO) etkinleştirme işlemi otomatik olarak etkinleştirilir. Azure AD 'de zaten mevcut bir oturuma sahip olsa da son kullanıcılarınızın oturum açması gerekiyorsa, uygulamanız yanlış yapılandırılmış olabilir.

* ADAL/MSAL kullanıyorsanız, **Promptbehavior** ' ın **her zaman** yerine **Auto** olarak ayarlandığından emin olun.

* Bir mobil uygulama oluşturuyorsanız, aracılı veya aracılı olmayan SSO 'yu etkinleştirmek için ek yapılandırmalara ihtiyacınız olabilir.

Android için bkz. [Android 'de uygulamalar arası SSO 'Yu etkinleştirme](../azuread-dev/howto-v1-enable-sso-android.md).<br>

İOS için bkz. [iOS 'Ta uygulamalar arası SSO etkinleştirme](../azuread-dev/howto-v1-enable-sso-ios.md).

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD SSO 'SU](../manage-apps/what-is-single-sign-on.md)<br>

[Android 'de uygulamalar arası SSO 'yu etkinleştirme](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[İOS 'ta çapraz uygulama SSO 'SU etkinleştirme](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[Uygulamaları AzureAD ile tümleştirme](./quickstart-register-app.md)<br>

[Microsoft Identity platformunda izinler ve onay](./v2-permissions-and-consent.md)<br>

[AzureAD Microsoft Q&A](/answers/topics/azure-active-directory.html)