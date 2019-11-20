---
title: Bir Google hesabı ile kaydolma ve oturum açma ayarlama-Azure Active Directory B2C
description: Azure Active Directory B2C kullanarak uygulamalarınızda Google hesabı bulunan müşterilere kaydolma ve oturum açma sağlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 158bb3b1329d2ad2cbd55ecf44f980966e2546e2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065182"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak bir Google hesabı ile kaydolma ve oturum açma ayarlama

## <a name="create-a-google-application"></a>Google uygulaması oluşturma

Bir Google hesabını Azure Active Directory B2C (Azure AD B2C) ' de bir [kimlik sağlayıcısı](active-directory-b2c-reference-oauth-code.md) olarak kullanmak için kiracınızda onu temsil eden bir uygulama oluşturmanız gerekir. Henüz bir Google hesabınız yoksa kaydolabilirsiniz [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Google [Developers konsolunda](https://console.developers.google.com/) Google hesabı kimlik bilgilerinizle oturum açın.
1. Sayfanın sol üst köşesinde proje listesini seçin ve ardından **Yeni proje**' yi seçin.
1. Bir **Proje adı**girin, **Oluştur**' a tıklayın ve ardından yeni projeyi kullandığınızdan emin olun.
1. Sol menüden **kimlik** bilgileri ' ni seçin ve **kimlik bilgileri** > oluştur**OAuth istemci kimliği**' ni seçin.
1. **Uygulama türü**altında **Web uygulaması**' nı seçin.
1. Uygulamanız için bir **ad** girin, **yetkili JavaScript kaynakları**' na ve `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **yetkili yeniden yönlendirme URI 'lerinde**girin. `https://your-tenant-name.b2clogin.com` Kiracınızın adıyla değiştirin `your-tenant-name` . Kiracı, Azure AD B2C büyük harfle tanımlansa bile kiracı adınızı girerken tüm küçük harfleri kullanmanız gerekir.
1. **Oluştur**'a tıklayın.
1. **ISTEMCI kimliği** ve **istemci parolası**değerlerini kopyalayın. Google 'ı kiracınızda bir kimlik sağlayıcısı olarak yapılandırmak için her ikisine de ihtiyacınız olacak. **İstemci parolası** önemli bir güvenlik kimlik bilgileridir.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Bir Google hesabını kimlik sağlayıcısı olarak yapılandırma

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve ardından **Google**' ı seçin.
1. Bir **ad**girin. Örneğin, *Google*.
1. **ISTEMCI kimliği**için, daha önce oluşturduğunuz Google UYGULAMASıNıN istemci kimliğini girin.
1. **İstemci parolası**için, kaydettiğiniz istemci gizli anahtarını girin.
1. **Kaydet**’i seçin.
