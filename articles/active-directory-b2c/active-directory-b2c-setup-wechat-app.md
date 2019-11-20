---
title: Azure Active Directory B2C kullanarak bir WeChat hesabı ile kaydolma ve oturum açma ayarlama
description: Azure Active Directory B2C kullanarak uygulamalarınızda WeChat hesabı bulunan müşterilere kaydolma ve oturum açma sağlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ad5e5b766c38b2ce3c48c22e62dd1fd2b9a79853
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064957"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak bir WeChat hesabı ile kaydolma ve oturum açma ayarlama

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>WeChat uygulaması oluşturma

Azure Active Directory B2C (Azure AD B2C) içinde bir kimlik sağlayıcısı olarak WeChat hesabı kullanmak için kiracınızda onu temsil eden bir uygulama oluşturmanız gerekir. Zaten bir WeChat hesabınız yoksa bilgi [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html)edinebilirsiniz.

### <a name="register-a-wechat-application"></a>WeChat uygulamasını kaydetme

1. [https://open.weixin.qq.com/](https://open.weixin.qq.com/) Wechat kimlik bilgilerinizle oturum açın.
1. **管理中心**(Yönetim Merkezi) öğesini seçin.
1. Yeni bir uygulamayı kaydetmek için adımları izleyin.
1. 授权回调域`https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` ( geri arama URL) girin. Örneğin, kiracı adınız contoso ise, URL 'YI `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`olarak ayarlayın.
1. **Uygulama kimliği** ve **uygulama anahtarı**' nı kopyalayın. Kimlik sağlayıcısını kiracınıza eklemek için bunlara ihtiyacınız olacaktır.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>WeChat 'i kiracınızda kimlik sağlayıcısı olarak yapılandırma

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve ardından **Wechat (Önizleme)** öğesini seçin.
1. Bir **ad**girin. Örneğin, *Wechat*.
1. **ISTEMCI kimliği**için, daha önce oluşturduğunuz Wechat UYGULAMASıNıN uygulama kimliğini girin.
1. **İstemci parolası**için, KAYDETTIĞINIZ uygulama anahtarını girin.
1. **Kaydet**’i seçin.
