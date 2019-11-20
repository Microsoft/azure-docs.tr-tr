---
title: Kayıt oluşturma ve Facebook hesabı ile oturum açma ayarlama-Azure Active Directory B2C
description: Azure Active Directory B2C kullanarak uygulamalarınızda Facebook hesabı bulunan müşterilere kaydolma ve oturum açma sağlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 77b2fccaeba3cd1d164a3ce428c04083fd69fb5a
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71264213"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak kayıt ve Facebook hesabı ile oturum açma ayarlama

## <a name="create-a-facebook-application"></a>Facebook uygulaması oluşturma

Azure Active Directory B2C (Azure AD B2C) ' de bir Facebook hesabını [kimlik sağlayıcısı](active-directory-b2c-reference-oauth-code.md) olarak kullanmak için kiracınızda onu temsil eden bir uygulama oluşturmanız gerekir. Henüz bir Facebook hesabınız yoksa kaydolabilirsiniz [https://www.facebook.com/](https://www.facebook.com/).

1. Facebook hesabı kimlik bilgilerinizle [geliştiriciler Için Facebook](https://developers.facebook.com/) 'ta oturum açın.
1. Daha önce yapmadıysanız, Facebook geliştiricisi olarak kaydolmanız gerekir. Bunu yapmak için sayfanın sağ üst **köşesinden başlayın ' ı seçin,** Facebook ilkelerini kabul edin ve kayıt adımlarını uygulayın.
1. **Uygulamalarım** ' ı ve ardından **uygulama oluştur**' u seçin.
1. Bir **görünen ad** ve geçerli bir **Iletişim e-postası**girin.
1. **Uygulama kimliği oluştur**' u seçin. Bu, Facebook platformu ilkelerini kabul etmenizi ve çevrimiçi bir güvenlik denetimi tamamlamanızı gerektirebilir.
1. **Ayarlar** > **temel**öğesini seçin.
1. Örneğin`Business and Pages`, bir **Kategori**seçin. Bu değer Facebook için gereklidir, ancak Azure AD B2C için kullanılmaz.
1. Sayfanın alt kısmındaki **Platform Ekle**' yi ve ardından **Web sitesi**' ni seçin.
1. **Site URL 'si**' nde `https://your-tenant-name.b2clogin.com/` , `your-tenant-name` kiracınızın adıyla değiştirin yazın. **Gizlilik ILKESI URL**'si IÇIN bir URL girin, örneğin `http://www.contoso.com`. İlke URL 'SI, uygulamanız için gizlilik bilgilerini sağlamak üzere tuttuğunuz bir sayfasıdır.
1. **Değişiklikleri Kaydet**' i seçin.
1. Sayfanın üst kısmında, **uygulama kimliği**değerini kopyalayın.
1. **Göster** ' i seçin ve **uygulama gizli anahtarı**değerini kopyalayın. Facebook 'ı kiracınızda bir kimlik sağlayıcısı olarak yapılandırmak için her ikisini de kullanabilirsiniz. **Uygulama gizli anahtarı** önemli bir güvenlik kimlik bilgileridir.
1. **Ürünlerin**yanındaki artı işaretini ve ardından **Facebook oturumu**altında **Ayarla** ' yı seçin.
1. **Facebook oturum açma**bölümünde **Ayarlar**' ı seçin.
1. **Geçerli OAuth yeniden yönlendirme URI 'lerinde**, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`girin. Kiracınızın adıyla değiştirin `your-tenant-name` . Sayfanın alt kısmındaki **Değişiklikleri Kaydet** ' i seçin.
1. Facebook uygulamanızı Azure AD B2C için kullanılabilir hale getirmek için sayfanın sağ üst kısmındaki durum seçiciyi seçin ve uygulamayı ortak hale **getirmek için açın** , sonra **Mod Değiştir**' i seçin.  Bu noktada, durumun **geliştirme aşamasında** **canlı**olarak değiştirilmesi gerekir.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Bir Facebook hesabını kimlik sağlayıcısı olarak yapılandırma

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve **Facebook**' u seçin.
1. Bir **ad**girin. Örneğin, *Facebook*.
1. **ISTEMCI kimliği**için, daha önce oluşturduğunuz Facebook UYGULAMASıNıN uygulama kimliğini girin.
1. **İstemci parolası**için, kaydettiğiniz uygulama gizli anahtarını girin.
1. **Kaydet**’i seçin.
