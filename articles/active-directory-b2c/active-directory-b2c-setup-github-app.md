---
title: Kayıt ayarlama ve bir GitHub hesabı ile oturum açma-Azure Active Directory B2C
description: Azure Active Directory B2C kullanarak uygulamalarınızda GitHub hesabı olan müşteriler için kaydolma ve oturum açma sağlar.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 74c663d8847c2829a5d9466f8e601dd44593a6f8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065192"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak bir GitHub hesabı ile kaydolma ve oturum açma ayarlama

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>GitHub OAuth uygulaması oluşturma

Azure Active Directory B2C (Azure AD B2C) ' de bir GitHub hesabını bir [kimlik sağlayıcısı](active-directory-b2c-reference-oauth-code.md) olarak kullanmak için kiracınızda onu temsil eden bir uygulama oluşturmanız gerekir. Zaten bir GitHub hesabınız yoksa kaydolabilirsiniz [https://www.github.com/](https://www.github.com/).

1. Oturum [GitHub Geliştirici](https://github.com/settings/developers) GitHub kimlik bilgilerinizle Web sitesi.
1. Seçin **OAuth uygulamaları** seçip **yeni bir OAuth uygulaması**.
1. Girin bir **uygulama adı** ve **giriş sayfası URL'si**.
1. Girin `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` içinde **yetkilendirme geri çağırma URL'si**. Değiştirin `your-tenant-name` Azure AD B2C kiracınızın adı. Azure AD B2C kodunda büyük harfler ile Kiracı tanımlansa bile Kiracı adınızın girerken tamamen küçük harf kullanın.
1. Tıklayın **kaydetme uygulama**.
1. Değerlerini kopyalamayı **istemci kimliği** ve **gizli**. Kiracınız için kimlik sağlayıcısı eklemek için her ikisi de gerekir.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Bir GitHub hesabı kimlik sağlayıcısı olarak yapılandırma

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
1. **Kimlik sağlayıcıları**' nı ve ardından **GitHub (Önizleme)** öğesini seçin.
1. Bir **ad**girin. Örneğin, *GitHub*.
1. **ISTEMCI kimliği**için, daha önce oluşturduğunuz GitHub UYGULAMASıNıN istemci kimliğini girin.
1. **İstemci parolası**için, kaydettiğiniz istemci gizli anahtarını girin.
1. **Kaydet**’i seçin.
