---
title: Azure haritalar 'da kimlik doğrulamasını yönetme | Microsoft Docs
description: Azure haritalar 'da kimlik doğrulamasını yönetmek için Azure portal kullanabilirsiniz.
author: walsehgal
ms.author: v-musehg
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 496edb4f3528daa5bd06193383f0277922e8a93a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73478779"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure haritalar 'da kimlik doğrulamasını yönetme

Azure haritalar hesabı oluşturduktan sonra, Azure Active Directory (Azure AD) ya da paylaşılan anahtar kimlik doğrulamasını desteklemek için bir istemci KIMLIĞI ve anahtarları oluşturulur.

## <a name="view-authentication-details"></a>Kimlik doğrulama ayrıntılarını görüntüle

Kimlik doğrulama ayrıntılarınızı Azure portal görüntüleyebilirsiniz. Hesabınıza gidin ve **Ayarlar** menüsünde **kimlik doğrulaması** ' nı seçin.

![Kimlik doğrulaması ayrıntıları](./media/how-to-manage-authentication/how-to-view-auth.png)

 Daha fazla bilgi için bkz. [Azure Maps Ile kimlik doğrulama](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Azure AD uygulama kaydını ayarlama

Azure haritalar hesabı oluşturduktan sonra Azure AD kiracınız ile Azure haritalar kaynağı arasında bir bağlantı kurmanız gerekir.

1. Azure AD dikey penceresine gidip bir uygulama kaydı oluşturun. Kayıt için bir ad girin. **Oturum açma URL 'si** kutusunda, Web UYGULAMASıNıN/API 'nin ana sayfasını sağlayın (örneğin, https:\//localhost/). Zaten kayıtlı bir uygulamanız varsa 2. adıma gidin.

    ![Uygulama kaydı](./media/how-to-manage-authentication/app-registration.png)

    ![Uygulama kaydı ayrıntıları](./media/how-to-manage-authentication/app-create.png)

2. Azure haritalar 'a temsil edilen API izinleri atamak için, **uygulama kayıtları**altındaki uygulamaya gidin ve ardından **Ayarlar**' ı seçin.  **Gerekli izinler**' i seçin ve ardından **Ekle**' yi seçin. **BIR API seçin**altında **Azure haritaları** ' nı arayıp seçin ve ardından **Seç** düğmesini seçin.

    ![Uygulama API 'SI izinleri](./media/how-to-manage-authentication/app-permissions.png)

3. **Izinleri Seç**altında **Azure Maps 'e eriş**' i seçin ve ardından **Seç** düğmesini seçin.

    ![Uygulama API 'SI izinlerini seçin](./media/how-to-manage-authentication/select-app-permissions.png)

4. Kimlik doğrulama yönteminize bağlı olarak, a veya b adımını tamamen doldurun.

    1. Uygulamanız Azure Maps web SDK 'Sı ile kullanıcı belirteci kimlik doğrulaması kullanıyorsa, uygulama kayıt ayrıntısı sayfanızın bildirim bölümünde true olarak ayarlayarak `oauthEnableImplicitFlow` etkinleştirin.
    
       ![Uygulama bildirimi](./media/how-to-manage-authentication/app-manifest.png)

    2. Uygulamanız sunucu/uygulama kimlik doğrulaması kullanıyorsa, uygulama kaydı ' nda **anahtarlar** dikey penceresine gidin ve bir parola oluşturun ya da uygulama kaydına bir ortak anahtar sertifikası yükleyin. Bir parola oluşturursanız, **Kaydet**' i seçtikten sonra parolayı daha sonra kopyalayın ve güvenli bir şekilde depolayın. Bu parolayı Azure AD 'den belirteç almak için kullanacaksınız.

       ![Uygulama anahtarları](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Azure Maps 'e RBAC verme

Azure haritalar hesabını Azure AD kiracınızla ilişkilendirdikten sonra, bir veya daha fazla Azure Maps erişim denetimi rolüne kullanıcı, Grup veya uygulama atayarak erişim denetimi sağlayabilirsiniz.

1. **Erişim denetimi 'ne (IAM)** gidin, **rol atamaları**' nı seçin ve ardından **rol ataması Ekle**' yi seçin.

    ![RBAC verme](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **Rol ataması Ekle** penceresinde, **rol**altında **Azure Maps Tarih okuyucusu (Önizleme)** seçeneğini belirleyin. **Erişim ata**' nın altında **Azure AD Kullanıcı, Grup veya hizmet sorumlusu**' nı seçin. **Seç**' in altında Kullanıcı veya uygulamayı seçin. **Kaydet**’i seçin.

    ![Rol ataması Ekle](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Kullanılabilir Azure Maps RBAC rollerini görüntüleme

Azure haritalar için kullanılabilen rol tabanlı erişim denetimi (RBAC) rollerini görüntülemek için **erişim denetimi (IAM)** sayfasına gidin, **Roller**' i seçin ve ardından **Azure Maps**ile başlayan roller için arama yapın. Bunlar, erişim izni vermek için kullanabileceğiniz rollerdir.

![Kullanılabilir rolleri görüntüle](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Azure haritalar RBAC 'yi görüntüleme

RBAC, ayrıntılı erişim denetimi sağlar.

Azure haritalar için RBAC verilmiş kullanıcıları ve uygulamaları görüntülemek için **Access Control (IAM)** sayfasına gidin, **rol atamaları**' nı seçin ve ardından **Azure Maps**'a göre filtreleyin.

![RBAC verilen kullanıcıları ve uygulamaları görüntüleme](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Azure haritalar için istek belirteçleri

Uygulamanızı kaydettikten ve Azure Maps ile ilişkilendirdikten sonra, erişim belirteçleri isteyebilirsiniz.

* Uygulamanız Azure Maps web SDK 'Sı ile kullanıcı belirteci kimlik doğrulamasını kullanıyorsa, HTML sayfanızı Azure Maps istemci KIMLIĞI ve Azure AD uygulama KIMLIĞI ile yapılandırmanız gerekir.

* Uygulamanız sunucu/uygulama kimlik doğrulaması kullanıyorsa Azure ad kaynak KIMLIĞI `https://atlas.microsoft.com/`, Azure Maps istemci KIMLIĞI, Azure AD uygulama KIMLIĞI ve Azure AD uygulama kayıt parolası ile Azure AD belirteç uç noktası `https://login.microsoftonline.com` bir belirteç istemeniz gerekir veya Sertifika.

| Azure ortamı   | Azure AD belirteç uç noktası | Azure Kaynak KIMLIĞI |
| --------------------|-------------------------|-------------------|
| Azure genel        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure Kamu    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

Kullanıcılar ve hizmet sorumluları için Azure AD 'den erişim belirteçleri isteme hakkında daha fazla bilgi için bkz. [Azure AD Için kimlik doğrulama senaryoları](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Sonraki adımlar

Azure AD kimlik doğrulaması ve Azure Maps web SDK hakkında daha fazla bilgi edinmek için bkz. [Azure AD ve Azure Maps web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Azure haritalar hesabınız için API kullanım ölçümlerini nasıl görebileceğinizi öğrenin:
> [!div class="nextstepaction"] 
> [Kullanım ölçümlerini görüntüle](how-to-view-api-usage.md)

Azure Active Directory (AAD) Azure Maps ile nasıl tümleştirileceğini gösteren örneklerin listesi için bkz.:

> [!div class="nextstepaction"]
> [AAD kimlik doğrulama örnekleri](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)