---
title: Kiracı uygulamalarını görüntüleme - Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) kiracınızdaki uygulamaları görüntülemek için Azure portalı kullanın.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 764c7c048dd0398b495ee31421a8e2bc9f8169ee
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477188"
---
# <a name="view-your-azure-active-directory-tenant-applications"></a>Azure Active Directory kiracınızdaki uygulamaları görüntüleme

Bu hızlı başlangıçta Azure Active Directory (Azure AD) kiracınızdaki uygulamaları görüntülemek için Azure portal kullanılmaktadır.

## <a name="before-you-begin"></a>Başlamadan önce

Sonuçları görmek için Azure AD kiracınızda en az bir uygulamanız olması gerekir. Uygulama eklemek için [Uygulama ekleme](add-application-portal.md) hızlı başlangıcına bakın.

[Azure portalda](https://portal.azure.com) Azure AD kiracınızın genel yönetici, bulut uygulaması yöneticisi veya uygulama yöneticisi hesabıyla oturum açın.

## <a name="find-the-list-of-tenant-applications"></a>Kiracı uygulamaları listesini bulma

Azure AD kiracınızdaki uygulamaları Azure portalın **Kurumsal uygulamalar** bölümünden görüntüleyebilirsiniz.

Kiracı uygulamalarınızı bulmak için:

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.
1. **Azure Active Directory** bölmesinde **Kurumsal uygulamalar**' ı seçin.
1. **Uygulama türü** açılan menüsünde, **tüm uygulamalar**' ı seçin ve **Uygula**' yı seçin. Kiracınızdaki uygulamalardan rastgele seçilenler burada görünür.
1. Daha fazla uygulama görüntülemek için, listenin en altında **daha fazla yükle** ' yi seçin. Kiracınızdaki uygulama sayısına bağlı olarak listeyi kaydırmak yerine [belirli bir uygulamayı aramak](#search-for-a-tenant-application) daha kolay olabilir.

## <a name="select-viewing-options"></a>Görüntüleme seçeneklerini belirleme

Aradığınız görünüme göre seçenekleri belirleyin.

1. Uygulamaları **uygulama türüne**, **Uygulama durumuna**ve **uygulama görünürlüğüne**göre görüntüleyebilirsiniz.
1. **Uygulama Türü** bölümünde aşağıdaki seçeneklerden birini belirleyin:

    - **Kurumsal Uygulamalar** seçeneği Microsoft harici uygulamaları gösterir.
    - **Microsoft Uygulamaları** seçeneği Microsoft uygulamalarını gösterir.
    - **Tüm Uygulamalar** seçeneği hem Microsoft harici uygulamaları hem de Microsoft uygulamalarını gösterir.

1. **Uygulama Durumu** bölümünde **Tümü**, **Devre dışı** veya **Etkin** seçeneğini belirleyin. **Tümü** seçeneği hem devre dışı hem de etkin uygulamaları gösterir.
1. **Uygulama Görünürlüğü** bölümünde **Tümü** veya **Gizli** seçeneğini belirleyin. **Gizli** seçeneği, Kiracıdaki uygulamaları gösterir, ancak kullanıcılara görünür değildir.
1. İstediğiniz seçenekleri seçtikten sonra **Uygula**' yı seçin.

## <a name="search-for-a-tenant-application"></a>Belirli bir kiracı uygulamasını arama

Belirli bir uygulamayı aramak için:

1. **Uygulama türü** menüsünde, **tüm uygulamalar**' ı seçin ve **Uygula**' yı seçin.
1. Bulmak istediğiniz uygulamanın adını girin. Uygulama Azure AD kiracınıza eklendiyse, arama sonuçlarında görünür. Bu örnekte GitHub 'ın kiracı uygulamalarına eklenmemiş olması gösterilmektedir.

    ![Örnek, kiracıya eklenmemiş bir uygulama gösterir](media/view-applications-portal/search-for-tenant-application.png)

1. Uygulama adının ilk birkaç harfini girmeyi deneyin. Bu örnek **Sales** ile başlayan tüm uygulamaları gösterir.

    ![Örnek, Sales ile başlayan tüm uygulamaları gösterir](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure AD kiracınızdaki uygulamaları görüntülemeyi öğrendiniz. Uygulama türü, durum ve görünürlük ile uygulama listesinin nasıl filtreleneceğini öğrendiniz. Ayrıca belirli bir uygulamayı aramayı da öğrendiniz.

Aradığınız uygulamayı buldığınıza göre, [kiracınıza daha fazla uygulama eklemeye](add-application-portal.md)devam edebilirsiniz. Ya da özellikleri ve yapılandırma seçeneklerini görüntülemek veya düzenlemek için uygulamayı seçebilirsiniz. Örneğin çoklu oturum açmayı yapılandırabilirsiniz.

> [!div class="nextstepaction"]
> [Çoklu oturum açmayı yapılandırma](configure-single-sign-on-non-gallery-applications.md)
