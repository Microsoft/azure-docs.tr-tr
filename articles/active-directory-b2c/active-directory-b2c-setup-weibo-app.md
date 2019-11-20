---
title: Azure Active Directory B2C kullanarak oturum açma ve Weibo hesabı ile oturum açma ayarlama
description: Azure Active Directory B2C kullanarak uygulamalarınızda Weibo hesabı bulunan müşterilere kaydolma ve oturum açma sağlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4a019c3f197f123486dd28dc2bdb46e34f6cabc4
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064912"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak oturum açma ve Weibo hesabı ile oturum açma ayarlama

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Weibo uygulaması oluşturma

Azure Active Directory B2C (Azure AD B2C) ' de bir kimlik sağlayıcısı olarak bir Weibo hesabı kullanmak için kiracınızda onu temsil eden bir uygulama oluşturmanız gerekir. Zaten bir Weibo hesabınız yoksa kaydolabilirsiniz [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us).

1. [Weibo Geliştirici Portalında](https://open.weibo.com/) Weibo hesabı kimlik bilgilerinizle oturum açın.
1. Oturum açtıktan sonra sağ üst köşedeki görünen adınızı seçin.
1. Açılan menüde**编辑开发者信息**(geliştirici bilgilerini düzenle) öğesini seçin.
1. Gerekli bilgileri girin ve**提交**(Gönder) seçeneğini belirleyin.
1. E-posta doğrulama sürecini doldurun.
1. [Kimlik doğrulama sayfasına](https://open.weibo.com/developers/identity/edit)gidin.
1. Gerekli bilgileri girin ve**提交**(Gönder) seçeneğini belirleyin.

### <a name="register-a-weibo-application"></a>Bir Weibo uygulamasını kaydetme

1. [Yeni Weibo uygulaması kayıt sayfasına](https://open.weibo.com/apps/new)gidin.
1. Gerekli uygulama bilgilerini girin.
1. **创建**(Oluştur) seçeneğini belirleyin.
1. **Uygulama anahtarı** ve **uygulama gizli**anahtarı değerlerini kopyalayın. Kimlik sağlayıcısını kiracınıza eklemek için bunların her ikisine de ihtiyacınız vardır.
1. Gerekli Fotoğrafları karşıya yükleyin ve gerekli bilgileri girin.
1. **保存以上信息**(Kaydet) seçeneğini belirleyin.
1. **高级信息**(gelişmiş bilgiler) seçeneğini belirleyin.
1. OAuth 2.0**授权设置**(YENIDEN yönlendirme URL) alanının yanındaki**编辑**(Düzenle) öğesini seçin.
1. OAuth `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` 2.0**授权设置**(Redirect URL) için girin. Örneğin, kiracı adınız contoso ise, URL 'YI `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`olarak ayarlayın.
1. **提交**(Gönder) seçeneğini belirleyin.

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Bir Weibo hesabını kimlik sağlayıcısı olarak yapılandırma

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve ardından **Weibo (Önizleme)** öğesini seçin.
1. Bir **ad**girin. Örneğin, *Weibo*.
1. **ISTEMCI kimliği**için, daha önce oluşturduğunuz Weibo uygulamasının uygulama anahtarını girin.
1. **İstemci parolası**için, kaydettiğiniz uygulama gizli anahtarını girin.
1. **Kaydet**’i seçin.
