---
title: Microsoft Identity platform (v 2.0) genel bakış-Azure
description: Microsoft Identity platform (v 2.0) uç noktası ve platformu hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbbcd854434bcc085f1b63fb864755dd0e928fc9
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852154"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Microsoft Identity platform (v 2.0) genel bakış

Microsoft kimlik platformu, Azure Active Directory (Azure AD) geliştirici platformunun geliştirilmesiyle ortaya çıkmıştır. Geliştiricilerin tüm Microsoft kimliklerinden oturum açıp Microsoft API 'Leri (Microsoft Graph veya geliştiricilerin oluşturduğu API 'Ler) çağırmak için belirteçleri almasına olanak tanır. Microsoft Identity platformu şunlardan oluşur:

- OAuth 2,0 ve OpenID, geliştiricilerin aşağıdakiler de dahil olmak üzere herhangi bir Microsoft kimliği için kimlik doğrulamasını sağlayan **standart uyumlu kimlik doğrulama hizmetini bağlama**
  - İş veya okul hesapları (Azure AD aracılığıyla temin edilmiş)
  - Kişisel Microsoft hesapları (Skype, Xbox ve Outlook.com gibi)
  - Sosyal veya yerel hesaplar (Azure AD B2C aracılığıyla)
- **Açık kaynak kitaplıkları**: Microsoft kimlik doğrulama kitaplıkları (MSAL) ve standartlara uyumlu diğer kitaplıklar için destek
- **Uygulama Yönetimi Portalı**: Diğer tüm Azure Yönetim olanaklarınızla birlikte Azure portal yerleşik olarak bulunan bir kayıt ve yapılandırma deneyimi.
- **Uygulama yapılandırma API 'si ve PowerShell**: uygulamalarınızın REST API (Microsoft Graph ve Azure Active Directory Graph 1,6) ve PowerShell aracılığıyla programlı yapılandırılmasına izin verir. böylece DevOps görevlerinizi otomatikleştirebilmenizi sağlayabilirsiniz.
- **Geliştirici içeriği**: kavramsal ve başvuru belgeleri, hızlı başlangıç örnekleri, kod örnekleri, öğreticiler ve nasıl yapılır kılavuzlarından.

Geliştiriciler için Microsoft Identity platformu, kimlik ve güvenlik alanındaki, passwordless kimlik doğrulaması, adım kimlik doğrulama ve koşullu erişim gibi yeniliklere sorunsuz tümleştirme sunar.  Bu tür işlevselliği kendiniz uygulamanız gerekmez: Microsoft Identity platformu ile tümleştirilmiş uygulamalar, bu tür yeniliklerden yararlanır.

Microsoft Identity platformu ile kodu bir kez yazabilir ve herhangi bir kullanıcıya ulaşabilirsiniz. Bir uygulamayı bir kez oluşturup çok sayıda platformda çalışır hale getirebilirsiniz veya istemci olarak bir kaynak uygulaması (API) olarak işlev gören bir uygulama oluşturabilirsiniz.

## <a name="getting-started"></a>Başlarken

Kimlikle çalışmanın zor olması şart değil. Size uygulanan bir [senaryo](authentication-flows-app-scenarios.md) seçin — her senaryo yolunda bir hızlı başlangıç ve bir genel bakış sayfası, birkaç dakika içinde çalışmaya başlamanızı sağlar:

- [Tek sayfalı uygulama oluşturma](scenario-spa-overview.md)
- [Kullanıcılarda oturum açan bir Web uygulaması oluşturun](scenario-web-app-sign-user-overview.md)
- [Web API 'Lerini çağıran bir Web uygulaması oluşturma](scenario-web-app-call-api-overview.md)
- [Korumalı bir Web API 'SI oluşturma](scenario-protected-web-api-overview.md)
- [Web API 'Lerini çağıran bir Web API 'SI oluşturma](scenario-web-api-call-api-overview.md)
- [Masaüstü uygulaması oluşturma](scenario-desktop-overview.md)
- [Daemon uygulaması oluşturma](scenario-daemon-overview.md)
- [Mobil uygulama oluşturma](scenario-mobile-overview.md)

Aşağıdaki grafik ortak kimlik doğrulama uygulaması senaryolarını özetler – Microsoft Identity platformunu uygulamanızla tümleştirdiğinizde bu uygulamayı bir başvuru olarak kullanın.

[![Microsoft Identity platformunda uygulama senaryoları](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

Çekirdek kimlik doğrulama kavramları hakkında daha fazla bilgi edinmek istiyorsanız şu konularda başlamanız önerilir:

- [Kimlik doğrulama akışları ve uygulama senaryoları](authentication-flows-app-scenarios.md)
- [Kimlik doğrulaması temel bilgileri](authentication-scenarios.md)
- [Uygulama ve hizmet sorumluları](app-objects-and-service-principals.md)
- [Kitle](v2-supported-account-types.md)
- [İzinler ve onay](v2-permissions-and-consent.md)
- [Kimlik belirteçleri](id-tokens.md) ve [erişim belirteçleri](access-tokens.md)

[Microsoft Graph](https://docs.microsoft.com/graph/overview)çağıran veri açısından zengin bir uygulama oluşturun.

Uygulamanızı bir **Üretim ortamında**başlatmaya hazırsanız, bu en iyi yöntemleri gözden geçirin:

- Uygulamanızda [günlüğe kaydetmeyi etkinleştirin](msal-logging.md) .
- Uygulamanızda Telemetriyi etkinleştirin.
- [Proxy 'leri etkinleştirin ve http istemcilerini özelleştirin](msal-net-provide-httpclient.md).
- [Microsoft Identity platform tümleştirme denetim listesini](identity-platform-integration-checklist.md)izleyerek tümleştirmenizi test edin.

## <a name="learn-more"></a>Daha fazla bilgi edinin

Sosyal ve yerel kimliklerinizde oturum açan müşterilere yönelik bir uygulama oluşturmayı planlıyorsanız bkz. [Azure AD B2C genel bakış](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).
