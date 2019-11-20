---
title: Azure Active Directory Web API uygulamaları
description: Web API uygulamalarının ne olduğunu ve bu uygulama türü için protokol akışı, kayıt ve belirteç süre sonu hakkındaki temel bilgileri açıklar.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4babb7e869f4fc83bcdb530a580a29dda234293
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373788"
---
# <a name="web-api"></a>Web API’si

Web API Apps, bir Web API 'sinden kaynak alması gereken Web uygulamalarıdır. Bu senaryoda, Web uygulamasının, Web API 'sini doğrulamak ve çağırmak için kullanabileceği iki kimlik türü vardır:

- **Uygulama kimliği** -bu senaryo, uygulama olarak kimlik doğrulaması yapmak ve Web API 'sine erişmek için OAuth 2,0 istemci kimlik bilgileri kullanır. Web API 'si, bir uygulama kimliği kullanırken, Web API 'SI Kullanıcı hakkında herhangi bir bilgi almadıkça yalnızca Web uygulamasının bunu çağırıyor olduğunu algılayabilir. Uygulama, Kullanıcı hakkında bilgi alırsa uygulama protokolü aracılığıyla gönderilir ve Azure AD tarafından imzalanmamıştır. Web API 'SI, Web uygulamasının kimliğinin doğruladığını güvendiğinde. Bu nedenle, bu modele güvenilen alt sistem denir.
- **Temsil edilen Kullanıcı kimliği** -bu senaryo iki şekilde gerçekleştirilebilir: OpenID Connect ve OAuth 2,0 yetkilendirme kodu, gizli bir istemciyle birlikte kullanılabilir. Web uygulaması, Kullanıcı için bir erişim belirteci edinir, bu, kullanıcının Web uygulamasına başarıyla kimlik doğruladığını ve Web uygulamasının Web API 'sini çağırmak için yetkilendirilmiş bir kullanıcı kimliği elde edebildiğini kanıtlayan Web API 'sine sahip olur. Bu erişim belirteci, kullanıcıya yetki veren ve istenen kaynağı döndüren Web API 'sine yönelik istekte gönderilir.

Hem uygulama kimliği hem de temsilci Kullanıcı kimlik türleri aşağıdaki akışta ele alınmıştır. Aralarındaki temel fark, temsilci Kullanıcı kimliğinin, Kullanıcı oturum açabilmeniz ve Web API 'sine erişim kazanmak için öncelikle bir yetkilendirme kodu edinmesi gerekir.

## <a name="diagram"></a>Diyagram

![Web uygulamasından Web API diyagramına](./media/authentication-scenarios/web_app_to_web_api.png)

## <a name="protocol-flow"></a>Protokol akışı

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>OAuth 2,0 istemci kimlik bilgileri izni ile uygulama kimliği

1. Bir Kullanıcı Web uygulamasında Azure AD 'ye oturum açtı (daha fazla bilgi için **Web Apps** bölümüne bakın).
1. Web uygulamasının, Web API 'sinin kimliğini doğrulayabilmesi ve istenen kaynağı alabilmesi için bir erişim belirteci alması gerekir. Kimlik bilgisi, uygulama KIMLIĞI ve Web API 'sinin uygulama KIMLIĞI URI 'sini sağlayarak Azure AD 'nin belirteç uç noktası için bir istek yapar.
1. Azure AD uygulamanın kimliğini doğrular ve Web API 'sini çağırmak için kullanılan bir JWT erişim belirteci döndürür.
1. HTTPS üzerinden Web uygulaması, isteğin yetkilendirme üstbilgisinde Web API 'sine bir "taşıyıcı" atamaya sahip JWT dizesini eklemek için döndürülen JWT erişim belirtecini kullanır. Web API 'SI daha sonra JWT belirtecini doğrular ve doğrulama başarılı olursa, istenen kaynağı döndürür.

### <a name="delegated-user-identity-with-openid-connect"></a>OpenID Connect ile temsilci Kullanıcı kimliği

1. Bir Kullanıcı Azure AD kullanarak bir Web uygulamasında oturum açtı (yukarıdaki Web uygulamasına Web tarayıcısı bölümüne bakın). Web uygulamasının kullanıcısı, Web uygulamasının kendi adına Web API 'sini çağırması için henüz onay vermediyse, kullanıcının onaylaması gerekir. Uygulama, gerektirdiği izinleri görüntüler ve bunlardan herhangi biri yönetici düzeyindeki izinlerdir, dizindeki normal bir Kullanıcı onay yapamaz. Bu izin işlemi, uygulama zaten gerekli izinlere sahip olacağı için tek kiracılı uygulamalar değil, yalnızca çok kiracılı uygulamalar için geçerlidir. Kullanıcı oturum açtıktan sonra, Web uygulaması, bir KIMLIK belirtecinin yanı sıra kullanıcı hakkındaki bilgileri ve bir yetkilendirme kodunu almıştır.
1. Web uygulaması, Azure AD tarafından verilen Yetkilendirme kodunu kullanarak Azure AD 'nin belirteç uç noktasına, yetkilendirme kodunu, istemci uygulamayla ilgili ayrıntıları (uygulama KIMLIĞI ve yeniden yönlendirme URI 'SI) ve istenen kaynağı (uygulama KIMLIĞI) içeren bir istek gönderir. Web API 'SI için URI).
1. Yetkilendirme kodu ve Web uygulaması ve Web API 'SI ile ilgili bilgiler Azure AD tarafından onaylanır. Doğrulama başarıyla tamamlandığında, Azure AD iki belirteç döndürür: bir JWT erişim belirteci ve JWT yenileme belirteci.
1. HTTPS üzerinden Web uygulaması, isteğin yetkilendirme üstbilgisinde Web API 'sine bir "taşıyıcı" atamaya sahip JWT dizesini eklemek için döndürülen JWT erişim belirtecini kullanır. Web API 'SI daha sonra JWT belirtecini doğrular ve doğrulama başarılı olursa, istenen kaynağı döndürür.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>OAuth 2,0 yetkilendirme kodu izni ile temsilci Kullanıcı kimliği

1. Bir Kullanıcı, kimlik doğrulama mekanizması Azure AD 'den bağımsız olan bir Web uygulamasında zaten oturum açtı.
1. Web uygulaması, bir erişim belirteci almak için bir yetkilendirme kodu gerektirir, bu nedenle, başarılı bir kimlik doğrulamasından sonra Web uygulaması için uygulama KIMLIĞI ve yeniden yönlendirme URI 'sini sağlayarak Azure AD 'nin yetkilendirme uç noktasına tarayıcıdan bir istek yayınlar. Kullanıcı Azure AD 'de oturum açar.
1. Web uygulamasının kullanıcısı, Web uygulamasının kendi adına Web API 'sini çağırması için henüz onay vermediyse, kullanıcının onaylaması gerekir. Uygulama, gerektirdiği izinleri görüntüler ve bunlardan herhangi biri yönetici düzeyindeki izinlerdir, dizindeki normal bir Kullanıcı onay yapamaz. Bu izin hem tek hem de çok kiracılı uygulamalar için geçerlidir. Tek kiracılı bir durumda, yönetici kullanıcı adına yönetici onayı gerçekleştirebilir. Bu işlem, [Azure portal](https://portal.azure.com)`Grant Permissions` düğmesi kullanılarak yapılabilir. 
1. Kullanıcı onayladıktan sonra, Web uygulaması erişim belirteci alması için gereken yetkilendirme kodunu alır.
1. Web uygulaması, Azure AD tarafından verilen Yetkilendirme kodunu kullanarak Azure AD 'nin belirteç uç noktasına, yetkilendirme kodunu, istemci uygulamayla ilgili ayrıntıları (uygulama KIMLIĞI ve yeniden yönlendirme URI 'SI) ve istenen kaynağı (uygulama KIMLIĞI) içeren bir istek gönderir. Web API 'SI için URI).
1. Yetkilendirme kodu ve Web uygulaması ve Web API 'SI ile ilgili bilgiler Azure AD tarafından onaylanır. Doğrulama başarıyla tamamlandığında, Azure AD iki belirteç döndürür: bir JWT erişim belirteci ve JWT yenileme belirteci.
1. HTTPS üzerinden Web uygulaması, isteğin yetkilendirme üstbilgisinde Web API 'sine bir "taşıyıcı" atamaya sahip JWT dizesini eklemek için döndürülen JWT erişim belirtecini kullanır. Web API 'SI daha sonra JWT belirtecini doğrular ve doğrulama başarılı olursa, istenen kaynağı döndürür.

## <a name="code-samples"></a>Kod örnekleri

Web uygulaması için kod örneklerine Web API senaryolarına bakın. Ve sık sık kontrol edin. yeni örnekler sık sık eklenir. Web [API 'sine Web uygulaması](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Uygulama kaydı

Bir uygulamayı Azure AD v 1.0 uç noktasıyla kaydetmek için bkz. [uygulamayı kaydetme](quickstart-register-app.md).

* Tek kiracılı-hem uygulama kimliği hem de temsilci Kullanıcı kimliği durumları Için, Web uygulaması ve Web API 'SI, Azure AD 'de aynı dizine kaydedilmelidir. Web API 'SI, Web uygulamasının kaynaklarına erişimini sınırlamak için kullanılan bir izin kümesini açığa çıkarmak üzere yapılandırılabilir. Yetkilendirilmiş bir kullanıcı kimliği türü kullanılıyorsa, Web uygulamasının Azure portal **diğer uygulamalar Için izinler** açılan menüsünde istenen izinleri seçmesini gerekir. Uygulama kimliği türü kullanılıyorsa bu adım gerekli değildir.
* Çok kiracılı-Birincisi, Web uygulaması işlevsel olması gereken izinleri belirtecek şekilde yapılandırılmıştır. Bu gerekli izinler listesi, hedef dizindeki bir kullanıcı veya yönetici uygulamaya izin verdiği zaman bir iletişim kutusunda gösterilir. Bu, kuruluş tarafından kullanılabilir hale gelir. Bazı uygulamalar yalnızca, kuruluştaki herhangi bir kullanıcının izin verebileceği Kullanıcı düzeyi izinler gerektirir. Diğer uygulamalar, kuruluştaki bir kullanıcının onay veremediği yönetici düzeyinde izinler gerektirir. Bu izin düzeyini gerektiren uygulamalara yalnızca bir dizin Yöneticisi izin verebilir. Kullanıcı veya yönetici, Web uygulamasının ve Web API 'sinin her ikisi de kendi dizinine kaydedilir.

## <a name="token-expiration"></a>Belirteç süre sonu

Web uygulaması, bir JWT erişim belirteci almak için yetkilendirme kodunu kullandığında, ayrıca bir JWT yenileme belirteci alır. Erişim belirtecinin süresi dolmuşsa, yenileme belirteci kullanıcıyı yeniden oturum açmalarına gerek kalmadan yeniden kimlik doğrulaması yapmak için kullanılabilir. Bu yenileme belirteci daha sonra kullanıcının kimliğini doğrulamak için kullanılır, bu da yeni bir erişim belirteci ve yenileme belirteci ile sonuçlanır.

## <a name="next-steps"></a>Sonraki adımlar

- Diğer [uygulama türleri ve senaryolar](app-types.md) hakkında daha fazla bilgi edinin
- Azure AD [kimlik doğrulaması temelleri](v1-authentication-scenarios.md) hakkında bilgi edinin
