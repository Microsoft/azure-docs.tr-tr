---
title: Tek sayfalı uygulamaları kaydetme (SPA) | Mavisi
titleSuffix: Microsoft identity platform
description: Tek sayfalı uygulama oluşturmayı öğrenin (uygulama kaydı)
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 9366bb5b2bb5820245ec1b699bbf2ddda0dd9f9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103184"
---
# <a name="single-page-application-app-registration"></a>Tek sayfalı uygulama: uygulama kaydı

Microsoft Identity platformunda tek sayfalı uygulama (SPA) kaydetmek için aşağıdaki adımları izleyin. Kayıt adımları, örtük verme akışını destekleyen MSAL.js 1,0 arasında farklılık gösterir ve PKCE ile yetkilendirme kodu akışını destekleyen MSAL.js 2,0 ' dir.

## <a name="create-the-app-registration"></a>Uygulama kaydını oluşturma

MSAL.js 1,0 ve 2,0 tabanlı uygulamalarda, ilk uygulama kaydını oluşturmak için aşağıdaki adımları tamamlayarak öğesini başlatın.

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
1. Uygulamanız için bir **ad** girin. Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
1. Uygulama için **Desteklenen hesap türlerini** seçin. **Yeniden yönlendirme URI 'si** **girmeyin.** Farklı hesap türlerinin bir açıklaması için, bkz. [uygulamayı kaydetme](quickstart-register-app.md).
1. Uygulama kaydını oluşturmak için **Kaydet** ' i seçin.

Ardından, Microsoft Identity platformunun istemciyi bir güvenlik belirteci ile birlikte yeniden yönlendirdiğini belirtmek için bir **yeniden yönlendirme URI 'si** ile uygulama kaydını yapılandırın. Uygulamanızda kullanmakta olduğunuz MSAL.js sürümü için uygun olan adımları kullanın:

- [ AUTH Code Flow ileMSAL.js 2,0](#redirect-uri-msaljs-20-with-auth-code-flow) (önerilir)
- [ Örtük Flow ileMSAL.js 1,0](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>Yeniden yönlendirme URI 'SI: [MSAL.js 2,0, auth Code Flow](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

MSAL.js 2,0 veya sonraki bir sürümü kullanan bir uygulama için yeniden yönlendirme URI 'SI eklemek için bu adımları izleyin. MSAL.js 2.0 +, [tarayıcı üçüncü taraf tanımlama bilgisi kısıtlamalarına](reference-third-party-cookies-spas.md)yanıt olarak pkce ve CORS ile yetkilendirme kodu akışını destekler. Örtük izin akışı MSAL.js 2.0 + içinde desteklenmez.

1. Azure portal, [uygulama kaydını oluşturma](#create-the-app-registration)bölümünde daha önce oluşturduğunuz uygulama kaydını seçin.
1. **Yönet** altında **kimlik doğrulama**  >  **Platform ekleme**' yi seçin.
1. **Web uygulamaları** altında, **tek sayfalı uygulama** kutucuğunu seçin.
1. **Yeniden yönlendirme URI 'leri** altında bir [yeniden yönlendirme URI 'si](reply-url.md)girin. **Örtük verme ve karma akışlar** altında onay **kutusunu seçmeyin.**
1. Yeniden yönlendirme URI 'sini eklemeyi bitirmeden **Yapılandır** ' ı seçin.

Artık tek sayfalı uygulamanızın (SPA) kaydını tamamladınız ve istemcinin yeniden yönlendirileceği ve herhangi bir güvenlik belirtecinin gönderileceği bir yeniden yönlendirme URI 'SI yapılandırdınız. **Platform Ekle** bölmesindeki **tek sayfalı uygulama** kutucuğunu kullanarak yeniden yönlendirme URI 'sini yapılandırarak, uygulama kaydınız, PKI CE ve CORS ile yetkilendirme kodu akışını destekleyecek şekilde yapılandırılır.

Daha fazla rehberlik için [öğreticiyi](tutorial-v2-javascript-auth-code.md) izleyin.

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>Yeniden yönlendirme URI 'SI: [MSAL.js 1,0 örtük akışla](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)

MSAL.js 1,3 veya önceki bir sürümü ve örtük izin akışını kullanan tek sayfalı bir uygulama için yeniden yönlendirme URI 'SI eklemek için bu adımları izleyin. MSAL.js 1,3 veya önceki bir sürümü kullanan uygulamalar, kimlik doğrulama kod akışını desteklemez.

1. Azure portal, [uygulama kaydını oluşturma](#create-the-app-registration)bölümünde daha önce oluşturduğunuz uygulama kaydını seçin.
1. **Yönet** altında **kimlik doğrulama**  >  **Platform ekleme**' yi seçin.
1. **Web uygulamaları** altında, **tek sayfalı uygulama** Kutucuğu ' nı seçin.
1. **Yeniden yönlendirme URI 'leri** altında bir [yeniden yönlendirme URI 'si](reply-url.md)girin.
1. **Örtük verme ve karma akışları** etkinleştirin:
    - Uygulamanız kullanıcıların oturumunu açarsa, **Kimlik belirteçleri**' ni seçin.
    - Uygulamanızın de korumalı bir Web API 'SI çağırması gerekiyorsa, **erişim belirteçleri**' ni seçin. Bu belirteç türleri hakkında daha fazla bilgi için bkz. [Kimlik belirteçleri](id-tokens.md) ve [erişim belirteçleri](access-tokens.md).
1. Yeniden yönlendirme URI 'sini eklemeyi bitirmeden **Yapılandır** ' ı seçin.

Artık tek sayfalı uygulamanızın (SPA) kaydını tamamladınız ve istemcinin yeniden yönlendirileceği ve herhangi bir güvenlik belirtecinin gönderileceği bir yeniden yönlendirme URI 'SI yapılandırdınız. **Kimlik belirteçlerinin** ve **erişim belirteçlerinin** birini veya her ikisini belirleyerek örtük verme akışını etkinleştirdiniz.

Daha fazla rehberlik için [öğreticiyi](tutorial-v2-javascript-spa.md) izleyin.

## <a name="note-about-authorization-flows"></a>Yetkilendirme akışları hakkında

Varsayılan olarak, tek sayfalı uygulama platformu yapılandırması kullanılarak oluşturulan bir uygulama kaydı, yetkilendirme kodu akışına izin vermez. Bu akıştan faydalanmak için uygulamanızın MSAL.js 2,0 veya üzeri bir sürümü kullanması gerekir.

Daha önce bahsedildiği gibi, MSAL.js 1,3 kullanan tek sayfalı uygulamalar örtük izin akışı ile kısıtlıdır. Geçerli [OAuth 2,0 en iyi uygulamaları](v2-oauth2-auth-code-flow.md) , Spas için örtük akış yerine yetkilendirme kodu akışının kullanılmasını önerir. Sınırlı ömür yenileme belirteçlerinin olması, uygulamanızın Safari IP gibi [modern tarayıcı tanımlama bilgisi gizlilik kısıtlamalarına](reference-third-party-cookies-spas.md)uyum sağlamasına de yardımcı olur.

Bir uygulama kaydı tarafından temsil edilen tüm üretim tek sayfalı uygulamalarınız MSAL.js 2,0 ' i ve yetkilendirme kodu akışını kullanıyorsa, örtülü izin ayarları ' nın Azure portal uygulama kaydının **kimlik doğrulama** Bölmesi ' ni kaldırın. MSAL.js 1. x ve örtük akış kullanan uygulamalar çalışmaya devam edebilir, ancak örtük akışı etkin bırakırsanız (işaretli).

## <a name="next-steps"></a>Sonraki adımlar

Uygulamanızın kodunu önceki adımlarda oluşturduğunuz uygulama kaydını kullanacak şekilde yapılandırın: [uygulamanın kod yapılandırması](scenario-spa-app-configuration.md).
