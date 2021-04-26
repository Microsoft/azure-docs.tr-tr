---
title: 'Hızlı başlangıç: kimlik yönetimi için Azure Active Directory (Azure AD) kiracınızı kullanan uygulamaların listesini görüntüleme'
description: Bu hızlı başlangıçta, kimlik yönetimi için Azure Active Directory (Azure AD) kiracınızı kullanmak üzere kayıtlı uygulamaların listesini görüntülemek için Azure portal kullanın.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: iangithinji
ms.reviewer: arvinh
ms.custom: it-pro
ms.openlocfilehash: c4a03e85798d4406068bd5923dbc753d8e5d31b6
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376452"
---
# <a name="quickstart-view-the-list-of-applications-that-are-using-your-azure-active-directory-azure-ad-tenant-for-identity-management"></a>Hızlı başlangıç: kimlik yönetimi için Azure Active Directory (Azure AD) kiracınızı kullanan uygulamaların listesini görüntüleme

Kuruluşunuzun kullandığı uygulamalar için kimlik ve erişim yönetimi (ıAM) sistemi olarak Azure AD 'yi kullanmaya başlayın. Bu hızlı başlangıçta, Azure AD kiracınızı kimlik sağlayıcısı (IDP) olarak kullanmak üzere zaten ayarlanmış uygulamalar olarak da bilinen uygulamaları görüntüleyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Azure AD kiracınızda kayıtlı olan uygulamaları görüntülemek için şunlar gerekir:

- Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

>[!IMPORTANT]
>Bu hızlı başlangıçtaki adımları test etmek için üretim dışı bir ortam kullanmanızı öneririz.

## <a name="find-the-list-of-applications-in-your-tenant"></a>Kiracınızdaki uygulamaların listesini bulun

Azure AD kiracınıza kayıtlı uygulamalar Azure portal **Kurumsal uygulamalar** bölümünde görüntülenebilir.

Kiracınızda kayıtlı olan uygulamaları görüntülemek için:

1. [Azure Portal](https://portal.azure.com)oturum açın.
2. Sol gezinti panelinde **Azure Active Directory**' yi seçin.
3. **Azure Active Directory** bölmesinde **Kurumsal uygulamalar**' ı seçin.
4. **Uygulama türü** açılan menüsünde, **tüm uygulamalar**' ı seçin ve **Uygula**' yı seçin. Kiracınızdaki uygulamalardan rastgele seçilenler burada görünür.
5. Daha fazla uygulama görüntülemek için, listenin en altında **daha fazla yükle** ' yi seçin. Kiracınızda çok sayıda uygulama varsa, listede gezinmek yerine belirli bir uygulamayı aramak daha kolay olabilir. Belirli bir uygulamayı aramak, bu hızlı başlangıçta daha sonra ele alınmıştır.

## <a name="select-viewing-options"></a>Görüntüleme seçeneklerini belirleme

Aradığınız görünüme göre seçenekleri belirleyin.

1. Uygulamaları **uygulama türüne**, **Uygulama durumuna** ve **uygulama görünürlüğüne** göre görüntüleyebilirsiniz.
2. **Uygulama Türü** bölümünde aşağıdaki seçeneklerden birini belirleyin:
    - **Kurumsal Uygulamalar** seçeneği Microsoft harici uygulamaları gösterir.
    - **Microsoft Uygulamaları** seçeneği Microsoft uygulamalarını gösterir.
    - **Tüm Uygulamalar** seçeneği hem Microsoft harici uygulamaları hem de Microsoft uygulamalarını gösterir.
3. **Uygulama Durumu** bölümünde **Tümü**, **Devre dışı** veya **Etkin** seçeneğini belirleyin. **Tümü** seçeneği hem devre dışı hem de etkin uygulamaları gösterir.
4. **Uygulama Görünürlüğü** bölümünde **Tümü** veya **Gizli** seçeneğini belirleyin. **Gizli** seçeneği, Kiracıdaki uygulamaları gösterir, ancak kullanıcılara görünür değildir.
5. İstediğiniz seçenekleri seçtikten sonra **Uygula**' yı seçin.

## <a name="search-for-an-application"></a>Uygulama arama

Belirli bir uygulamayı aramak için:

1. **Uygulama türü** menüsünde, **tüm uygulamalar**' ı seçin ve **Uygula**' yı seçin.
2. Bulmak istediğiniz uygulamanın adını girin. Uygulama Azure AD kiracınıza eklendiyse, arama sonuçlarında görünür. Bu örnekte GitHub 'ın kiracı uygulamalarına eklenmemiş olması gösterilmektedir.
    ![Örnek, kiracıya eklenmemiş bir uygulama gösterir](media/view-applications-portal/search-for-tenant-application.png)
3. Uygulama adının ilk birkaç harfini girmeyi deneyin. Bu örnek **Sales** ile başlayan tüm uygulamaları gösterir.
    ![Örnek, Sales ile başlayan tüm uygulamaları gösterir](media/view-applications-portal/search-by-prefix.png)


> [!TIP]
> Graph API kullanarak uygulama yönetimini otomatik hale getirebilirsiniz, bkz. [MICROSOFT Graph API ile uygulama yönetimini otomatikleştirme](/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçta yeni kaynak oluşturmadınız, temizlenecek bir şey yok.

## <a name="next-steps"></a>Sonraki adımlar

Bir uygulama için kimlik sağlayıcısı olarak Azure AD 'yi nasıl kullanacağınızı öğrenmek için sonraki makaleye ilerleyin.
> [!div class="nextstepaction"]
> [Uygulama ekleme](add-application-portal.md)