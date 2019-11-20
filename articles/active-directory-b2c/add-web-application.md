---
title: Web API uygulaması ekleme-Azure Active Directory B2C | Microsoft Docs
description: Active Directory B2C kiracınıza bir Web API uygulaması eklemeyi öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 37cb242f667190fcd29bed1b7a82ca44ba2c94e9
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641552"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C kiracınıza bir Web API uygulaması ekleme

 Web API kaynaklarını kiracınıza kaydederek erişim belirtecini sunan istemci uygulamalarına yönelik istekleri kabul edebilir ve yanıtlayabilir. Bu makalede, bir Web API 'sinin Azure Active Directory B2C (Azure AD B2C) nasıl kaydedileceği gösterilmektedir.

Bir uygulamayı Azure AD B2C kiracınıza kaydetmek için, geçerli **uygulamalar** deneyimini veya yeni Birleşik **uygulama kayıtları (Önizleme)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Uygulamalar](#tab/applications/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve kiracınızı içeren dizini seçin.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. **Uygulamalar**' ı seçin ve ardından **Ekle**' yi seçin.
5. Uygulama için bir ad girin. Örneğin, *webapi1*.
6. **Web uygulaması/Web API 'Si Ekle** ve **örtük akışa Izin ver**için **Evet**' i seçin.
7. **Yanıt URL 'si**için Azure AD B2C uygulamanızın isteklerinizin belirteçleri döndürmesi gereken bir uç nokta girin. Üretim uygulamanızda, yanıt URL 'sini `https://localhost:44332`gibi bir değere ayarlayabilirsiniz. Sınama amacıyla, yanıt URL 'sini `https://jwt.ms`olarak ayarlayın.
8. **Uygulama kimliği URI 'si**için, Web API 'niz için kullanılan tanımlayıcıyı girin. Tam etki alanı ile birlikte URI tanımlayıcısı sizin için oluşturulur. Örneğin, `https://contosotenant.onmicrosoft.com/api`.
9. **Oluştur**'a tıklayın.
10. Özellikler sayfasında, Web uygulamasını yapılandırırken kullanacağınız uygulama KIMLIĞINI kaydedin.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Uygulama kayıtları (Önizleme)](#tab/app-reg-preview/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C**seçin.
1. **Uygulama kayıtları (Önizleme)** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** girin. Örneğin, *webapi1*.
1. **Yeniden yönlendirme URI 'si**altında **Web**' i seçin ve ardından Azure AD B2C uygulamanızın isteklerinizin belirteçleri döndürmesi gerektiği bir uç nokta girin. Bir üretim uygulamasında yeniden yönlendirme URI 'sini `https://localhost:5000`gibi bir uç nokta olarak ayarlayabilirsiniz. Geliştirme veya test sırasında, bir belirtecin kodu çözülmüş içeriğini görüntüleyen Microsoft 'a ait bir Web uygulaması olan `https://jwt.ms`olarak ayarlayabilirsiniz (belirtecin içeriği hiçbir şekilde tarayıcıdan ayrılmayın). İstediğiniz zaman kayıtlı uygulamalarınıza yeniden yönlendirme URI 'Leri ekleyebilir ve bunları değiştirebilirsiniz.
1. **Kaydol**’u seçin.
1. Web API 'nizin kodunda kullanılmak üzere **uygulama (istemci) kimliğini** kaydedin.

Örtük verme akışını uygulayan bir uygulamanız varsa (örneğin, JavaScript tabanlı tek sayfalı uygulama (SPA), aşağıdaki adımları izleyerek akışı etkinleştirebilirsiniz:

1. **Yönet**altında **kimlik doğrulaması**' nı seçin.
1. **Yeni deneyimi deneyin** (gösteriliyorsa) seçeneğini belirleyin.
1. **Örtük izin**' ın altında, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularını seçin.
1. **Kaydet**’i seçin.

* * *

## <a name="configure-scopes"></a>Kapsamları yapılandırma

Kapsamlar, korumalı kaynaklara erişimi yönetmek için bir yol sağlar. Kapsamlar web API’si tarafından kapsam tabanlı erişim denetimi uygulamak için kullanılır. Örneğin web API'sinin kullanıcıları hem okuma hem de yazma veya yalnızca okuma erişimine sahip olabilir. Bu öğreticide web API’si için okuma ve yazma izinlerini tanımlamak için kapsamları kullanacaksınız.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>İzinleri verme

Bir uygulamadan korumalı bir Web API 'SI çağırmak için uygulamanızın API 'sine izin vermeniz gerekir. Örneğin, [öğretici: uygulamayı Azure Active Directory B2C kaydetme](tutorial-register-applications.md)bölümünde, *WebApp1* adlı bir Web uygulaması Azure AD B2C kaydedilir. Web API 'sini çağırmak için bu uygulamayı kullanabilirsiniz.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Uygulamanız, korumalı Web API 'sini çağırmak için kaydedilir. Kullanıcı, uygulamayı kullanmak için Azure AD B2C kimliğini doğrular. Uygulama, korumalı Web API 'sine erişmek için Azure AD B2C bir yetkilendirme izni alır.
