---
title: Azure Active Directory Web Apps
description: Bu uygulama türü için Web uygulamalarının ne olduğunu ve protokol akışı, kayıt ve belirteç süre sonu hakkındaki temel bilgileri açıklar.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154415"
---
# <a name="web-apps"></a>Web uygulamaları

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Web Apps, Web tarayıcısında bir kullanıcının kimliğini bir Web uygulamasına doğrulayan uygulamalardır. Bu senaryoda Web uygulaması, kullanıcının tarayıcısını Azure AD 'de oturum açmak üzere yönlendirir. Azure AD, kullanıcının tarayıcısı aracılığıyla bir güvenlik belirtecinde Kullanıcı hakkında talepler içeren bir oturum açma yanıtı döndürür. Bu senaryo, OpenID Connect, SAML 2,0 ve WS-Federation protokollerini kullanarak oturum açmayı destekler.

## <a name="diagram"></a>Diyagram

![Tarayıcı için Web uygulamasına yönelik kimlik doğrulama akışı](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>Protokol akışı

1. Bir Kullanıcı uygulamayı ziyaret ettiğinde ve oturum açması gerektiğinde, Azure AD 'de kimlik doğrulama uç noktası için bir oturum açma isteği aracılığıyla yeniden yönlendirilir.
1. Kullanıcı oturum açma sayfasında oturum açar.
1. Kimlik doğrulaması başarılı olursa, Azure AD bir kimlik doğrulama belirteci oluşturur ve uygulamanın Azure portal yapılandırılan yanıt URL 'sine bir oturum açma yanıtı döndürür. Bir üretim uygulaması için, bu yanıt URL 'SI HTTPS olmalıdır. Döndürülen belirteç, Kullanıcı ve belirteci doğrulamak için uygulamanın gerektirdiği Azure AD taleplerini içerir.
1. Uygulama, Azure AD için Federasyon meta verileri belgesinde bulunan ortak bir imzalama anahtarı ve veren bilgilerini kullanarak belirteci doğrular. Uygulama belirteci doğruladıktan sonra, Kullanıcı ile yeni bir oturum başlatır. Bu oturum, kullanıcının süresi dolana kadar uygulamaya erişmesine izin verir.

## <a name="code-samples"></a>Kod örnekleri

Web tarayıcısı için kod örneklerine bakın Web uygulaması senaryoları. Yeni örnekler sık sık eklendikçe, daha sonra da yeniden kontrol edin.

## <a name="app-registration"></a>Uygulama kaydı

Bir Web uygulamasını kaydetmek için bkz. [uygulamayı kaydetme](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Tek kiracı-yalnızca kuruluşunuz için bir uygulama oluşturuyorsanız, Azure portal kullanılarak şirketinizin dizinine kayıtlı olması gerekir.
* Çok kiracılı-kuruluşunuzun dışındaki kullanıcılar tarafından kullanılabilecek bir uygulama oluşturuyorsanız, şirketinizin dizinine kayıtlı olması gerekir, ancak aynı zamanda uygulamanın kullanacağı her bir kuruluşun dizinine kayıtlı olması gerekir. Uygulamanızı dizininde kullanılabilir hale getirmek için müşterilerinizin uygulamanıza izin vermesini sağlayan bir kaydolma işlemi ekleyebilirsiniz. Uygulamanıza kaydolduklarında, uygulamaya gereken izinleri gösteren bir iletişim kutusu görüntülenir ve ardından onay seçeneği sunulur. Gerekli izinlere bağlı olarak, diğer kuruluştaki bir yöneticinin onay vermesi gerekebilir. Kullanıcı veya yönetici, uygulama kendi dizinine kaydedilir.

## <a name="token-expiration"></a>Belirteç süre sonu

Azure AD tarafından verilen belirtecin ömrü sona erdiğinde kullanıcının oturumunun süresi dolar. Uygulamanız, istenirse bu zaman dilimini kısaltıp bir süre işlem yapılmayan bir döneme göre Kullanıcı oturumu açabilir. Oturumun süresi dolarsa, kullanıcıdan yeniden oturum açması istenir.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [uygulama türleri ve senaryolar](app-types.md) hakkında daha fazla bilgi edinin
* Azure AD [kimlik doğrulaması temelleri](v1-authentication-scenarios.md) hakkında bilgi edinin
