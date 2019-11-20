---
title: Azure Active Directory 'de hizmetten hizmete uygulamalar
description: Bu uygulama türü için hizmet-hizmet uygulamalarını ve protokol akışı, kayıt ve belirteç süre sonu hakkındaki temel bilgileri açıklar.
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
ms.openlocfilehash: a8f95dfd6410ae22a4596ac7d5d72add57e8029d
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373886"
---
# <a name="service-to-service-apps"></a>Hizmetten hizmete uygulamalar

Hizmetten hizmete uygulamalar, bir Web API 'sinden kaynak alması gereken bir Daemon veya sunucu uygulamasıdır. Bu bölüm için uygulanan iki alt senaryo vardır:

- OAuth 2,0 istemci kimlik bilgileri verme türü üzerine inşa edilen bir Web API 'sini çağırması gereken bir Daemon

    Bu senaryoda, birkaç şeyi anlamak önemlidir. İlk olarak, Kullanıcı etkileşimi, uygulamanın kendi kimliğine sahip olmasını gerektiren bir Daemon uygulamasıyla mümkün değildir. Bir Daemon uygulamasına bir örnek, bir toplu iş veya arka planda çalışan bir işletim sistemi hizmetidir. Bu tür bir uygulama, uygulama kimliğini kullanarak bir erişim belirteci ister ve uygulama KIMLIĞI, kimlik bilgileri (parola veya sertifika) ve uygulama KIMLIĞI URI 'sini Azure AD 'ye sunuyor. Başarılı kimlik doğrulamasından sonra, Daemon, Azure AD 'den bir erişim belirteci alır ve bu, daha sonra Web API 'sini çağırmak için kullanılır.

- Bir Web API 'sini çağırması gereken (örneğin, bir Web API 'SI) bir sunucu uygulaması (örneğin, OAuth 2,0)

    Bu senaryoda, bir kullanıcının yerel uygulama üzerinde kimlik doğrulaması yapıldığını ve bu yerel uygulamanın bir Web API 'SI çağırması gerektiğini düşünün. Azure AD, Web API 'sini çağırmak için bir JWT erişim belirteci yayınlar. Web API 'sinin başka bir aşağı akış Web API 'SI çağırması gerekiyorsa, kullanıcının kimliğini devretmek ve ikinci katman Web API 'sine kimlik doğrulaması yapmak için şirket adına akışını kullanabilir.

## <a name="diagram"></a>Diyagram

![Web API diyagramına arka plan programı veya sunucu uygulaması](./media/authentication-scenarios/daemon_server_app_to_web_api.png)

## <a name="dprotocol-flow"></a>DProtocol akışı

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>OAuth 2,0 istemci kimlik bilgileri izni ile uygulama kimliği

1. İlk olarak, sunucu uygulamasının, etkileşimli oturum açma iletişim kutusu gibi herhangi bir insan etkileşimi olmadan Azure AD ile kimlik doğrulaması yapması gerekir. Kimlik bilgisini, uygulama KIMLIĞINI ve uygulama KIMLIĞI URI 'sini sağlayarak Azure AD 'nin belirteç uç noktası için bir istek yapar.
1. Azure AD uygulamanın kimliğini doğrular ve Web API 'sini çağırmak için kullanılan bir JWT erişim belirteci döndürür.
1. HTTPS üzerinden Web uygulaması, isteğin yetkilendirme üstbilgisinde Web API 'sine bir "taşıyıcı" atamaya sahip JWT dizesini eklemek için döndürülen JWT erişim belirtecini kullanır. Web API 'SI daha sonra JWT belirtecini doğrular ve doğrulama başarılı olursa, istenen kaynağı döndürür.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Kullanıcı kimliği, OAuth 2,0 için isteğe bağlı taslak belirtimi ile verildi

Aşağıda ele alınan akış, bir kullanıcının başka bir uygulamada (yerel bir uygulama gibi) kimlik doğrulamasının yapıldığını ve ilk katman Web API 'sine bir erişim belirteci almak için Kullanıcı kimliğinin kullanıldığını varsayar.

1. Yerel uygulama, erişim belirtecini ilk katman Web API 'sine gönderir.
1. Birinci katman Web API 'SI, Azure AD 'nin belirteç uç noktasına bir istek göndererek uygulama KIMLIĞI ve kimlik bilgilerinin yanı sıra kullanıcının erişim belirtecini sağlar. Ayrıca, istek, Web API 'sinin özgün kullanıcı adına bir aşağı akış Web API 'SI çağırmak için yeni belirteçler istediğini belirten bir on_behalf_of parametresiyle gönderilir.
1. Azure AD, birinci katman Web API 'sinin ikinci katman Web API 'sine erişim izinleri olduğunu doğrular ve isteği doğrular, bir JWT erişim belirteci ve bir JWT yenileme belirtecini birinci katman Web API 'sine döndürür.
1. HTTPS üzerinden ilk katman Web API 'SI, isteğin yetkilendirme üstbilgisine belirteç dizesini ekleyerek ikinci katmanlı Web API 'sini çağırır. İlk katman Web API 'SI, erişim belirteci ve yenileme belirteçleri geçerli olduğu sürece ikinci katmanlı Web API 'sini çağırmaya devam edebilir.

## <a name="code-samples"></a>Kod örnekleri

Web API senaryolarında arka plan programı veya sunucu uygulaması için kod örneklerine bakın. Yeni örnekler sık sık eklendikçe, daha sonra da yeniden kontrol edin. [Web API 'ye sunucu veya Daemon uygulaması](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Uygulama kaydı

* Tek kiracı-hem uygulama kimliği hem de temsilci Kullanıcı kimliği durumları Için, arka plan veya sunucu uygulamasının Azure AD 'de aynı dizine kaydedilmesi gerekir. Web API 'SI, arka plan programı veya sunucunun kaynaklarına erişimini sınırlandırmak için kullanılan bir izin kümesini açığa çıkarmak üzere yapılandırılabilir. Yetkilendirilmiş bir kullanıcı kimliği türü kullanılıyorsa, sunucu uygulamasının, Azure portal "diğer uygulamalara yönelik Izinler" açılan menüsünde istenen izinleri seçmesini gerekir. Uygulama kimliği türü kullanılıyorsa bu adım gerekli değildir.
* Çok kiracılı-Birincisi, Daemon veya sunucu uygulaması, işlevsel olması gereken izinleri belirtecek şekilde yapılandırılmıştır. Bu gerekli izinler listesi, hedef dizindeki bir kullanıcı veya yönetici uygulamaya izin verdiği zaman bir iletişim kutusunda gösterilir. Bu, kuruluş tarafından kullanılabilir hale gelir. Bazı uygulamalar yalnızca, kuruluştaki herhangi bir kullanıcının izin verebileceği Kullanıcı düzeyi izinler gerektirir. Diğer uygulamalar, kuruluştaki bir kullanıcının onay veremediği yönetici düzeyinde izinler gerektirir. Bu izin düzeyini gerektiren uygulamalara yalnızca bir dizin Yöneticisi izin verebilir. Kullanıcı veya yönetici, Web API 'lerinin her ikisi de kendi dizinine kaydedilir.

## <a name="token-expiration"></a>Belirteç süre sonu

İlk uygulama bir JWT erişim belirteci almak için yetkilendirme kodunu kullandığında, ayrıca bir JWT yenileme belirteci alır. Erişim belirtecinin süresi dolarsa, kimlik bilgileri istenmeden Kullanıcı kimliğini yeniden doğrulamak için yenileme belirteci kullanılabilir. Bu yenileme belirteci daha sonra kullanıcının kimliğini doğrulamak için kullanılır, bu da yeni bir erişim belirteci ve yenileme belirteci ile sonuçlanır.

## <a name="next-steps"></a>Sonraki adımlar

- Diğer [uygulama türleri ve senaryolar](app-types.md) hakkında daha fazla bilgi edinin
- Azure AD [kimlik doğrulaması temelleri](v1-authentication-scenarios.md) hakkında bilgi edinin
