---
title: Azure AD uygulamasını bir Kullanıcı kümesiyle kısıtlama | Mavisi
titleSuffix: Microsoft identity platform
description: Azure AD 'de kayıtlı uygulamalarınıza erişimi seçili bir kullanıcı kümesine kısıtlamayı öğrenin.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 71cfe08da42b8eec9ddbd0e4246ba1b72f895414
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199591"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Nasıl yapılır: Azure AD kiracısındaki bir Kullanıcı kümesiyle Azure AD uygulamanızı kısıtlama

Bir Azure Active Directory (Azure AD) kiracısında kayıtlı uygulamalar, varsayılan olarak, başarıyla kimlik doğrulayan kiracının tüm kullanıcıları tarafından kullanılabilir.

Benzer şekilde, [çok kiracılı](howto-convert-app-to-be-multi-tenant.md) bir uygulama söz konusu olduğunda, bu uygulamanın SAĞLANDıĞı Azure AD kiracısındaki tüm kullanıcılar ilgili kiracısında başarıyla kimlik doğrulamasından sonra bu uygulamaya erişebilir.

Kiracı yöneticileri ve geliştiricileri, genellikle bir uygulamanın belirli bir Kullanıcı kümesiyle kısıtlanması gereken gereksinimlere sahiptir. Geliştiriciler, Azure rol tabanlı erişim denetimi (Azure RBAC) gibi popüler yetkilendirme düzenlerini kullanarak aynısını gerçekleştirebilir, ancak bu yaklaşım, geliştiricinin bir bölümünde önemli miktarda iş gerektirir.

Kiracı yöneticileri ve geliştiriciler, Azure AD 'nin bu yerleşik özelliğini kullanarak bir uygulamayı Kiracıdaki belirli bir kullanıcı veya güvenlik grubu kümesiyle kısıtlayabilir.

## <a name="supported-app-configurations"></a>Desteklenen uygulama konfigürasyonları

Bir uygulamayı bir Kiracıdaki belirli bir kullanıcı veya güvenlik grubu kümesiyle kısıtlama seçeneği aşağıdaki uygulama türleri ile birlikte kullanılabilir:

- SAML tabanlı kimlik doğrulamasıyla federe çoklu oturum açma için yapılandırılmış uygulamalar.
- Azure AD ön kimlik doğrulaması kullanan uygulama proxy uygulamaları.
- Bir kullanıcı veya yönetici bu uygulamaya alındıktan sonra, OAuth 2.0/OpenID Connect kimlik doğrulaması kullanan doğrudan Azure AD uygulama platformunda oluşturulan uygulamalar.

     > [!NOTE]
     > Bu özellik yalnızca Web uygulaması/Web API 'SI ve kurumsal uygulamalar için kullanılabilir. [Yerel](./quickstart-register-app.md) olarak kaydedilen uygulamalar, Kiracıdaki bir grup kullanıcı veya güvenlik grubu ile kısıtlanamaz.

## <a name="update-the-app-to-enable-user-assignment"></a>Kullanıcı atamasını etkinleştirmek için uygulamayı güncelleştirme

Etkin Kullanıcı atamasına sahip bir uygulama oluşturmanın iki yolu vardır. Biri **genel yönetici** rolü gerektirir, ikincisi değildir.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Kurumsal uygulamalar (genel yönetici rolü gerektirir)

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> **genel yönetici** olarak oturum açın.
1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** altında **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Listeden bir kullanıcı veya güvenlik grubu atamak istediğiniz uygulamayı seçin. 
    Belirli bir uygulamayı aramak için pencerenin üst kısmındaki filtreleri kullanın.
1. Uygulamanın **genel bakış** sayfasında, **Yönet** altında **Özellikler**' i seçin.
1. **Kullanıcı atamasının gerekli** ayarını bulun ve **Evet** olarak ayarlayın. Bu seçenek **Evet** olarak ayarlandığında, Kiracıdaki kullanıcılardan önce bu uygulamaya atanması gerekir, aksi durumda bu uygulamada oturum açamazsınız.
1. **Kaydet**’i seçin.

### <a name="app-registration"></a>Uygulama kaydı

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet**'in altında **Uygulama kayıtları** nı seçin.
1. Yönetmek istediğiniz uygulamayı oluşturun veya seçin. Bu uygulamanın **sahibi** olmanız gerekir.
1. Uygulamanın **genel bakış** sayfasında, **temel** bileşenler bölümündeki **Yerel Dizin ' de yönetilen uygulama** bağlantısını seçin.
1. **Yönet**'in altında **Özellikler**'i seçin.
1. **Kullanıcı atamasının gerekli** ayarını bulun ve **Evet** olarak ayarlayın. Bu seçenek **Evet** olarak ayarlandığında, Kiracıdaki kullanıcılardan önce bu uygulamaya atanması gerekir, aksi durumda bu uygulamada oturum açamazsınız.
1. **Kaydet**’i seçin.

## <a name="assign-users-and-groups-to-the-app"></a>Uygulamaya Kullanıcı ve Grup atama

Uygulamanızı Kullanıcı atamasını etkinleştirecek şekilde yapılandırdıktan sonra, uygulamaya gidip kullanıcıları ve grupları uygulamaya atayabilirsiniz.

1. **Yönet** altında **Kullanıcılar ve gruplar**  >  **Kullanıcı/Grup Ekle** ' yi seçin.
1. **Kullanıcılar** seçicisini seçin. 

     Kullanıcı ve güvenlik gruplarının listesi, belirli bir kullanıcı veya grubu aramak ve bulmak için bir metin kutusuyla birlikte gösterilir. Bu ekran, tek bir go içinde birden çok kullanıcı ve grup seçmenizi sağlar.

1. Kullanıcıları ve grupları seçmeyi tamamladıktan sonra **Seç**' i seçin.
1. Seçim Uygulamanızda uygulama rolleri tanımladıysanız, seçilen kullanıcıları ve grupları uygulamanın rollerinin birine atamak için **Rol Seç** seçeneğini kullanabilirsiniz. 
1. Kullanıcı ve grupların uygulamaya atamalarını tamamlamaya yönelik **ata** ' yı seçin. 
1. Eklediğiniz kullanıcı ve grupların, güncelleştirilmiş **Kullanıcılar ve gruplar** listesinde görüntülendiğini doğrulayın.

## <a name="more-information"></a>Daha fazla bilgi

- [Nasıl yapılır: uygulamanıza uygulama rolleri ekleme](./howto-add-app-roles-in-azure-ad-apps.md)
- [Uygulama rollerini kullanarak yetkilendirme ekleme ASP.NET Core Web uygulaması için rol talepleri &](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Uygulamalarınızda güvenlik gruplarını ve uygulama rollerini kullanma (video)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [Azure Active Directory, artık grup talepleri ve uygulama rolleriyle birlikte](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory uygulama bildirimi](./reference-app-manifest.md)
