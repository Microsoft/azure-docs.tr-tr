---
title: Azure Active Directory yerel uygulamalar
description: Bu uygulama türü için yerel uygulamaların ne olduğunu ve protokol akışı, kayıt ve belirteç süre sonu hakkındaki temel bilgileri açıklar.
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
ms.openlocfilehash: 0034668231d97e64602bdbdd0836bded97bb733d
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373879"
---
# <a name="native-apps"></a>Yerel uygulamalar

Yerel uygulamalar, bir kullanıcı adına bir Web API 'SI çağıran uygulamalardır. Bu senaryo, OAuth [2,0 belirtiminin](https://tools.ietf.org/html/rfc6749)4,1 bölümünde açıklandığı gibi, genel bir istemciyle OAuth 2,0 yetkilendirme kodu verme türü üzerine kurulmuştur. Yerel uygulama, OAuth 2,0 protokolünü kullanarak Kullanıcı için bir erişim belirteci alır. Bu erişim belirteci daha sonra, kullanıcıya yetki veren ve istenen kaynağı döndüren Web API 'sine yönelik istekte gönderilir.

## <a name="diagram"></a>Diyagram

![Yerel uygulamadan Web API diyagramına](./media/authentication-scenarios/native_app_to_web_api.png)

## <a name="protocol-flow"></a>Protokol akışı

AD kimlik doğrulama kitaplıklarını kullanıyorsanız, aşağıda açıklanan protokol ayrıntılarının çoğu sizin için, tarayıcı açılır, belirteç önbelleği ve yenileme belirteçlerinin işlenmesi gibi, sizin için işlenir.

1. Bir tarayıcı açılır penceresi kullanarak, yerel uygulama Azure AD 'de yetkilendirme uç noktasına bir istek yapar. Bu istek, Azure portal gösterildiği gibi yerel uygulamanın uygulama KIMLIĞINI ve yeniden yönlendirme URI 'sini ve Web API 'SI için uygulama KIMLIĞI URI 'sini içerir. Kullanıcı henüz oturum açmadıysa, yeniden oturum açması istenir
1. Azure AD, kullanıcının kimliğini doğrular. Bu bir çok kiracılı bir uygulamadır ve uygulamayı kullanmak için onay gerekiyorsa, kullanıcının zaten yapılmamış olmaları durumunda kabul etmek gerekecektir. Onay verdikten ve başarılı kimlik doğrulamasından geçtikten sonra Azure AD, istemci uygulamasının yeniden yönlendirme URI 'sine bir yetkilendirme kodu yanıtı verir.
1. Azure AD yeniden yönlendirme URI 'sine bir yetkilendirme kodu yanıtı verdiği zaman, istemci uygulaması tarayıcı etkileşimini durduruyor ve yetkilendirme kodunu yanıttan ayıklar. İstemci uygulaması, bu yetkilendirme kodunu kullanarak Azure AD 'nin belirteç uç noktasına, yetkilendirme kodunu, istemci uygulaması (uygulama KIMLIĞI ve yeniden yönlendirme URI 'SI) ve istenen kaynak (örneğin, uygulama KIMLIĞI URI 'SI) bilgilerini içeren bir istek gönderir. Web API 'SI).
1. Yetkilendirme kodu ve istemci uygulaması ve Web API 'SI ile ilgili bilgiler Azure AD tarafından onaylanır. Doğrulama başarıyla tamamlandığında, Azure AD iki belirteç döndürür: bir JWT erişim belirteci ve JWT yenileme belirteci. Ayrıca, Azure AD Kullanıcı hakkında, görünen adı ve kiracı KIMLIĞI gibi temel bilgileri döndürür.
1. HTTPS üzerinden istemci uygulaması, isteğin yetkilendirme üstbilgisinde Web API 'sine bir "taşıyıcı" atamaya sahip JWT dizesini eklemek için döndürülen JWT erişim belirtecini kullanır. Web API 'SI daha sonra JWT belirtecini doğrular ve doğrulama başarılı olursa, istenen kaynağı döndürür.
1. Erişim belirtecinin süresi dolarsa, istemci uygulama, kullanıcının yeniden kimlik doğrulaması yapması gerektiğini belirten bir hata alır. Uygulamanın geçerli bir yenileme belirteci varsa, kullanıcının yeniden oturum açmasını istemeden yeni bir erişim belirteci almak için kullanılabilir. Yenileme belirtecinin süresi dolarsa, uygulamanın kullanıcı tarafından bir kez daha etkileşimli olarak doğrulanması gerekir.

> [!NOTE]
> Azure AD tarafından verilen yenileme belirteci, birden fazla kaynağa erişmek için kullanılabilir. Örneğin, iki Web API 'sini çağırma iznine sahip bir istemci uygulamanız varsa, diğer Web API 'sine de erişim belirteci almak için yenileme belirteci kullanılabilir.

## <a name="code-samples"></a>Kod örnekleri

Web API 'SI senaryolarında yerel uygulama için kod örneklerine bakın. Ve sık sık kontrol edin. bu sıklıkla yeni örnekler ekleyeceğiz. [Web API 'Sine yerel uygulama](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Uygulama kaydı

Bir uygulamayı Azure AD v 1.0 uç noktasıyla kaydetmek için bkz. [uygulamayı kaydetme](quickstart-register-app.md).

* Tek kiracı-hem yerel uygulama hem de Web API 'SI Azure AD 'de aynı dizine kaydedilmelidir. Web API 'SI, yerel uygulamanın kaynaklarına erişimini sınırlamak için kullanılan bir izin kümesini açığa çıkarmak üzere yapılandırılabilir. İstemci uygulaması daha sonra, Azure portal "diğer uygulamalara Izinler" açılan menüsünde istenen izinleri seçer.
* Çok kiracılı-Ilki, yerel uygulama yalnızca geliştirici veya yayımcının dizinine kaydoldu. İkinci olarak, yerel uygulama çalışır olması gereken izinleri belirtecek şekilde yapılandırılmıştır. Bu gerekli izinler listesi, hedef dizindeki bir kullanıcı veya yönetici uygulamaya izin verdiği zaman bir iletişim kutusunda gösterilir. Bu, kuruluş tarafından kullanılabilir hale gelir. Bazı uygulamalar yalnızca, kuruluştaki herhangi bir kullanıcının izin verebileceği Kullanıcı düzeyi izinler gerektirir. Diğer uygulamalar, kuruluştaki bir kullanıcının onay veremediği yönetici düzeyinde izinler gerektirir. Bu izin düzeyini gerektiren uygulamalara yalnızca bir dizin Yöneticisi izin verebilir. Kullanıcı veya yönetici, kendi dizinine yalnızca Web API 'SI kaydedilir. 

## <a name="token-expiration"></a>Belirteç süre sonu

Yerel uygulama bir JWT erişim belirteci almak için yetkilendirme kodunu kullandığında, ayrıca bir JWT yenileme belirteci alır. Erişim belirtecinin süresi dolmuşsa, yenileme belirteci kullanıcının yeniden oturum açmasını gerektirmeden yeniden kimlik doğrulaması yapmak için kullanılabilir. Bu yenileme belirteci daha sonra kullanıcının kimliğini doğrulamak için kullanılır, bu da yeni bir erişim belirteci ve yenileme belirteci ile sonuçlanır.

## <a name="next-steps"></a>Sonraki adımlar

- Diğer [uygulama türleri ve senaryolar](app-types.md) hakkında daha fazla bilgi edinin
- Azure AD [kimlik doğrulaması temelleri](v1-authentication-scenarios.md) hakkında bilgi edinin
