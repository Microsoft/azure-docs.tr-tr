---
title: Azure AD uygulamaları için bağlantılı oturum açma-Microsoft Identity platform | Microsoft Docs
description: Microsoft Identity platform (Azure AD) içinde Azure AD kurumsal uygulamalarınızın bağlantılı çoklu oturum açmayı (SSO) yapılandırma
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc1e26a3c7d284a60b830f6f66cdcecef97db4d6
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834625"
---
# <a name="configure-linked-sign-on"></a>Bağlantılı oturum açmayı yapılandırma

Galeri veya Galeri olmayan bir Web uygulaması eklediğinizde, kullanabileceğiniz çoklu oturum açma seçeneklerinden biri [bağlı oturum açma](what-is-single-sign-on.md)seçenekleridir. Kuruluşunuzun Azure AD erişim panelinde veya Office 365 portalındaki uygulamaya bir bağlantı eklemek için bu seçeneği belirleyin. Kimlik doğrulaması için Azure AD yerine şu anda Active Directory Federasyon Hizmetleri (AD FS) (veya başka bir Federasyon Hizmeti) kullanan özel Web uygulamalarına bağlantılar eklemek için bu yöntemi kullanabilirsiniz. Ya da yalnızca kullanıcının erişim Panellerinizde görünmesini istediğiniz belirli SharePoint sayfalarına veya diğer Web sayfalarına derin bağlantılar ekleyebilirsiniz.

## <a name="before-you-begin"></a>Başlamadan önce

Uygulama Azure AD kiracınıza eklenmemişse, bkz. [Galeri uygulaması ekleme](add-gallery-app.md) veya [Galeri dışı bir uygulama ekleme](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Uygulamayı açın ve bağlı oturum aç ' ı seçin.

1. [Azure Portal](https://portal.azure.com) bir bulut uygulaması Yöneticisi veya Azure AD kiracınız için bir uygulama Yöneticisi olarak oturum açın.

1. **Azure Active Directory** > **kurumsal uygulamalara**gidin. Azure AD kiracınızdaki uygulamaların rastgele bir örneği görüntülenir. 

1. **Uygulama türü** menüsünde, **tüm uygulamalar**' ı seçin ve ardından **Uygula**' yı seçin.

1. Arama kutusuna uygulamanın adını girin ve sonra sonuçlardan uygulamayı seçin.

1. **Yönet** bölümünde **Çoklu oturum açma**' yı seçin. 

1. **Bağlı**' yı seçin.

1. Bağlantı yapılacak uygulamanın URL 'sini girin. URL 'YI yazın ve **Kaydet**' i seçin. 
 
1. Uygulamaya kullanıcılar ve gruplar atayabilirsiniz, bu da uygulamanın [Office 365 uygulama başlatıcısı](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) 'nda veya bu kullanıcılar IÇIN [Azure AD erişim panelinde](end-user-experiences.md) görünmesine neden olur.

1. **Kaydet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulamaya Kullanıcı veya Grup atama](methods-for-assigning-users-and-groups.md)
- [Otomatik Kullanıcı hesabı sağlamayı yapılandırma](configure-automatic-user-provisioning-portal.md)
