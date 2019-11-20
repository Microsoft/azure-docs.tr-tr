---
title: LinkedIn hesabı ile kaydolma ve oturum açma bilgilerini ayarlama-Azure Active Directory B2C
description: Azure Active Directory B2C kullanarak uygulamalarınızda LinkedIn hesapları olan kaydolma ve oturum açma bilgilerini sağlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: be347fe33e03b4c2a7ecc1015a407c5a58062326
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065143"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak bir LinkedIn hesabı ile kaydolma ve oturum açma ayarlama

## <a name="create-a-linkedin-application"></a>LinkedIn uygulaması oluşturma

Azure Active Directory B2C (Azure AD B2C) ' de bir LinkedIn hesabını [kimlik sağlayıcısı](active-directory-b2c-reference-oauth-code.md) olarak kullanmak için kiracınızda onu temsil eden bir uygulama oluşturmanız gerekir. Zaten bir LinkedIn hesabınız yoksa, ' de kaydolabilirsiniz [https://www.linkedin.com/](https://www.linkedin.com/).

1. LinkedIn hesabı kimlik bilgilerinizle [LinkedIn geliştiricileri Web sitesinde](https://www.developer.linkedin.com/) oturum açın.
1. **Uygulamalarım**' ı seçin ve ardından **uygulama oluştur**' a tıklayın.
1. **Şirket adı**, **uygulama adı**, **uygulama açıklaması**, **uygulama logosu**, **uygulama kullanımı**, **Web sitesi URL 'si**, **iş e-postası**ve **iş telefonu**girin.
1. **LINKEDıN API kullanım koşullarını** kabul edin ve **Gönder**' e tıklayın.
1. Değerlerini kopyalamayı **istemci kimliği** ve **gizli**. Bunları, **kimlik doğrulama anahtarları**altında bulabilirsiniz. Bunları kiracınızda bir kimlik sağlayıcısı olarak yapılandırmak için her ikisine de ihtiyacınız olacak. **Istemci parolası** önemli bir güvenlik kimlik bilgileridir.
1. `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **Yetkili yeniden yönlendirme URL 'leri**girin. Kiracınızın adıyla değiştirin `your-tenant-name` . Kiracı, Azure AD B2C büyük harfle tanımlansa bile kiracı adınızı girerken tüm küçük harfleri kullanmanız gerekir. **Ekle**' yi ve ardından **Güncelleştir**' i seçin.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Bir LinkedIn hesabını kimlik sağlayıcısı olarak yapılandırma

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve **LinkedIn**' i seçin.
1. Bir **ad**girin. Örneğin, *LinkedIn*.
1. **ISTEMCI kimliği**için, daha önce oluşturduğunuz LinkedIn UYGULAMASıNıN istemci kimliğini girin.
1. **İstemci parolası**için, kaydettiğiniz istemci gizli anahtarını girin.
1. **Kaydet**’i seçin.

## <a name="migration-from-v10-to-v20"></a>V 1.0 'dan v 2.0'a geçiş

LinkedIn [, API 'lerini son zamanlarda v 1.0 'dan v 2.0 'a güncelleştirmiştir](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Geçişin bir parçası olarak, Azure AD B2C yalnızca kaydolma sırasında LinkedIn kullanıcısının tam adını elde edebilir. Kayıt sırasında toplanan özniteliklerden biri e-posta adresi ise, kullanıcının e-posta adresini el ile girmesi ve doğrulaması gerekir.
