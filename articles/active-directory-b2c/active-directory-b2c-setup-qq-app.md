---
title: Azure Active Directory B2C kullanarak bir QQ hesabı ile kaydolma ve oturum açma ayarlama
description: Azure Active Directory B2C kullanarak uygulamalarınızda QQ hesaplarına kaydolma ve oturum açma özelliği sağlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c17b344764fd9c1d18f787952a6193f0c8b634d0
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065010"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak bir QQ hesabı ile kaydolma ve oturum açma ayarlama

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>QQ uygulaması oluşturma

Bir QQ hesabını Azure Active Directory B2C (Azure AD B2C) ' de bir kimlik sağlayıcısı olarak kullanmak için kiracınızda onu temsil eden bir uygulama oluşturmanız gerekir. Zaten bir QQ hesabınız yoksa, ' de kaydolabilirsiniz [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>QQ geliştirici programına kaydolma

1. QQ hesabı kimlik bilgilerinizle [QQ Geliştirici Portalında](http://open.qq.com) oturum açın.
1. Oturum açtıktan sonra, kendinizi bir [https://open.qq.com/reg](https://open.qq.com/reg) geliştirici olarak kaydetmek için bölümüne gidin.
1. **个人**(bireysel Geliştirici) seçeneğini belirleyin.
1. Gerekli bilgileri girin ve**下一步**(sonraki adım) seçeneğini belirleyin.
1. E-posta doğrulama sürecini doldurun. Bir geliştirici olarak kaydedildikten sonra birkaç günün onaylanmasını beklemeniz gerekecektir.

### <a name="register-a-qq-application"></a>Bir QQ uygulamasını kaydetme

1. [https://connect.qq.com/index.html](https://connect.qq.com/index.html) kısmına gidin.
1. **应用管理**(uygulama yönetimi) öğesini seçin.
1. **创建应用**(uygulama oluştur) seçeneğini belirleyin ve gerekli bilgileri girin.
1. 授权回调域`https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` ( geri arama URL) girin. Örneğin, `tenant_name` contoso ise, URL 'yi `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`olarak ayarlayın.
1. **创建应用**(uygulama oluştur) seçeneğini belirleyin.
1. Onay sayfasında, uygulama yönetimi sayfasına dönmek için**应用管理**(uygulama yönetimi) öğesini seçin.
1. Oluşturduğunuz uygulamanın yanındaki**查看**(görünüm) seçeneğini belirleyin.
1. **修改**(Düzenle) öğesini seçin.
1. **Uygulama kimliği** ve **uygulama anahtarı**' nı kopyalayın. Kimlik sağlayıcısını kiracınıza eklemek için bu değerlerin her ikisine de ihtiyacınız vardır.

## <a name="configure-qq-as-an-identity-provider"></a>QQ 'ı kimlik sağlayıcısı olarak yapılandırma

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve **QQ (Önizleme)** öğesini seçin.
1. Bir **ad**girin. Örneğin, *QQ*.
1. **ISTEMCI kimliği**için, daha önce oluşturduğunuz QQ UYGULAMASıNıN uygulama kimliğini girin.
1. **İstemci parolası**için, KAYDETTIĞINIZ uygulama anahtarını girin.
1. **Kaydet**’i seçin.
