---
title: Web API uygulaması ekleme-Azure Active Directory B2C | Microsoft Docs
description: Active Directory B2C kiracınıza bir Web API uygulaması eklemeyi öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 0eeff0c8c338b09fbe375587db2b955a143138c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94949828"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C kiracınıza web API'si uygulaması ekleme

 Web API kaynaklarını kiracınıza kaydederek erişim belirtecini sunan istemci uygulamalarına yönelik istekleri kabul edebilir ve yanıtlayabilir. Bu makalede, bir Web API 'sinin Azure Active Directory B2C (Azure AD B2C) nasıl kaydedileceği gösterilmektedir.

Bir uygulamayı Azure AD B2C kiracınıza kaydetmek için yeni Birleşik **uygulama kayıtları** deneyimimizi veya eski  **uygulamalarımız (eski)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Uygulama kayıtları](#tab/app-reg-ga/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Uygulama kayıtları** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** girin. Örneğin, *webapi1*.
1. **Yeniden yönlendirme URI 'si** altında **Web**' i seçin ve ardından Azure AD B2C uygulamanızın isteklerinizin belirteçleri döndürmesi gerektiği bir uç nokta girin. Bir üretim uygulamasında yeniden yönlendirme URI 'sini gibi bir uç nokta ayarlayabilirsiniz `https://localhost:5000` . Geliştirme veya test sırasında, `https://jwt.ms` bir belirtecin kodu çözülmüş içeriğini görüntüleyen Microsoft 'a ait bir Web uygulaması olan olarak ayarlayabilirsiniz (belirtecin içeriği hiçbir şekilde tarayıcıdan ayrılmayın). İstediğiniz zaman kayıtlı uygulamalarınıza yeniden yönlendirme URI 'Leri ekleyebilir ve bunları değiştirebilirsiniz.
1. **Kaydet**’i seçin.
1. Web API 'nizin kodunda kullanılmak üzere **uygulama (istemci) kimliğini** kaydedin.

Örtük verme akışını uygulayan bir uygulamanız varsa (örneğin, [JavaScript tabanlı tek sayfalı uygulama (Spa)](tutorial-register-spa.md), aşağıdaki adımları izleyerek akışı etkinleştirebilirsiniz:

1. **Yönet** altında **kimlik doğrulaması**' nı seçin.
1. **Örtük izin**' ın altında, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularını seçin.
1. **Kaydet**’i seçin.

#### <a name="applications-legacy"></a>[Uygulamalar (eski)](#tab/applications-legacy/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve kiracınızı içeren dizini seçin.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. **Uygulamalar (eski)** öğesini seçin ve ardından **Ekle**' yi seçin.
5. Uygulama için bir ad girin. Örneğin, *webapi1*.
6. **Web uygulaması/Web API 'Si Ekle** ve **örtük akışa Izin ver** için **Evet**' i seçin.
7. **Yanıt URL 'si** için Azure AD B2C uygulamanızın isteklerinizin belirteçleri döndürmesi gereken bir uç nokta girin. Üretim uygulamanızda, yanıt URL 'sini gibi bir değere ayarlayabilirsiniz `https://localhost:44332` . Sınama amacıyla, yanıt URL 'sini olarak ayarlayın `https://jwt.ms` .
8. **Uygulama kimliği URI 'si** için, Web API 'niz için kullanılan tanımlayıcıyı girin. Tam etki alanı ile birlikte URI tanımlayıcısı sizin için oluşturulur. Örneğin, `https://contosotenant.onmicrosoft.com/api`.
9. **Oluştur**’a tıklayın.
10. Özellikler sayfasında, Web uygulamasını yapılandırırken kullanacağınız uygulama KIMLIĞINI kaydedin.

* * *

## <a name="configure-scopes"></a>Kapsamları yapılandırma

Kapsamlar korumalı kaynaklara erişimi yönetmenin bir yolunu sunar. Kapsamlar web API’si tarafından kapsam tabanlı erişim denetimi uygulamak için kullanılır. Örneğin web API'sinin kullanıcıları hem okuma hem de yazma veya yalnızca okuma erişimine sahip olabilir. Bu öğreticide web API’si için okuma ve yazma izinlerini tanımlamak için kapsamları kullanacaksınız.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>İzinleri verme

Bir uygulamadan korumalı bir Web API 'SI çağırmak için uygulamanızın API 'sine izin vermeniz gerekir. Örneğin, [öğretici: uygulamayı Azure Active Directory B2C kaydetme](tutorial-register-applications.md)bölümünde, *WebApp1* adlı bir Web uygulaması Azure AD B2C kaydedilir. Web API 'sini çağırmak için bu uygulamayı kullanabilirsiniz.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Uygulamanız, korumalı Web API 'sini çağırmak için kaydedilir. Kullanıcı, uygulamayı kullanmak için Azure AD B2C kimliğini doğrular. Uygulama, korumalı Web API 'sine erişmek için Azure AD B2C bir yetkilendirme izni alır.