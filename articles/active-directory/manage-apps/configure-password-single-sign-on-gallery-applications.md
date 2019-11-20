---
title: Azure AD Galeri uygulaması için parola çoklu oturum açmayı yapılandırma | Microsoft Docs
description: Bir uygulamayı Azure AD uygulama galerisinde zaten listelendiğinde güvenli parola tabanlı çoklu oturum açma için yapılandırma
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 9fb33c4110a590539c85364885da9a27853f6bd0
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146882"
---
# <a name="configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Azure AD Galeri uygulaması için parola çoklu oturum açmayı yapılandırma

[Azure Active Directory (Azure AD) uygulama galerisinden](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)bir uygulama eklediğinizde, kullanıcılarınızın bu uygulamada nasıl oturum açmasını istediğinizi seçebilirsiniz. Bu seçeneği, [Azure Portal](https://portal.azure.com/)bir kuruluş uygulamasında **Çoklu oturum açmayı** seçerek dilediğiniz zaman yapılandırabilirsiniz.

Kullanılabilir çoklu oturum açma (SSO) seçeneklerinden biri [parola tabanlı çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Bu, uygulamaları Azure AD 'ye hızlı bir şekilde tümleştirmeye başlamak için harika bir yoldur. Şunları sunar:

-   Azure AD ile tümleştirmiş olduğunuz uygulama için Kullanıcı adlarını ve parolaları güvenli bir şekilde depolar ve yeniden yürütür

-   Yalnızca Kullanıcı adı ve parola alanlarının ötesinde birden çok oturum açma alanı gerektiren uygulamalar için destek sağlar

-   Kullanıcılarınızın kimlik bilgilerini girerken [uygulama erişimi panelinde](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) göreceği Kullanıcı adı ve parola alanlarının etiketlerini özelleştirmenizi sağlar

-   Kullanıcılarınızın [uygulama erişim paneline](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) el ile girdikleri mevcut herhangi bir uygulama hesabı için kendi kullanıcı adlarını ve parolalarını sağlamasına izin verir

-   Bir kullanıcıya atanan kullanıcı adlarını ve parolaları belirtmek için iş grubunun bir üyesinin [self servis uygulama erişimi](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) özelliğini kullanmasına izin verir

-   Yöneticinin bir [kullanıcıya Kullanıcı atarken](#assign-a-user-to-an-application-directly) kimlik bilgilerini güncelleştir özelliğini kullanarak bir kullanıcıya atanan kullanıcı adlarını ve parolaları belirtmesini sağlar

-   Yöneticinin bir [gruba Grup atarken](#assign-an-application-to-a-group-directly) paylaşılan kullanıcı adını veya parolasını belirtmek Için kimlik bilgilerini güncelleştir özelliğini kullanmasına izin verir

Aşağıdaki bölümde, zaten [Azure AD uygulama galerisinde](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)bulunan bir uygulamada [parola tabanlı çoklu oturum açmayı](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) nasıl etkinleştirebileceğinizi açıklanmaktadır.

## <a name="overview-of-required-steps"></a>Gerekli adımlara genel bakış
Azure AD Galerisi 'nden bir uygulamayı yapılandırmak için şunları yapmanız gerekir:

-   [Azure AD Galerisi 'nden uygulama ekleme](#add-an-application-from-the-azure-ad-gallery)

-   [Uygulamayı parola çoklu oturum açma için yapılandırma](#configure-the-application-for-password-single-sign-on)

-   Uygulamayı bir kullanıcıya veya gruba atayın:

    -   [Uygulamaya doğrudan kullanıcı atama](#assign-a-user-to-an-application-directly)

    -   [Bir gruba doğrudan bir uygulama atama](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Azure AD Galerisi 'nden uygulama ekleme

Azure AD Galerisi 'nden bir uygulama eklemek için şu adımları izleyin:

1.  [Azure Portal](https://portal.azure.com)açın ve **genel yönetici** veya **ortak yönetici**olarak oturum açın.

2.  Sol taraftaki menünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.

3.  Arama kutusuna **Azure Active Directory** ' yi girin ve **Azure Active Directory** öğesini seçin.

4.  Sol taraftaki Azure AD menüsünde **Kurumsal uygulamalar** ' ı seçin.

5.  **Kurumsal uygulamalar** bölmesinin sağ üst köşesindeki **Ekle** düğmesini seçin.

6.  **Galeriden Ekle** bölümünde **bir ad girin** kutusuna uygulamanın adını girin.

7.  Çoklu oturum açma için yapılandırmak istediğiniz uygulamayı seçin.

8.  Uygulamayı eklemeden önce **ad** kutusunda adını değiştirebilirsiniz.

9.  Uygulamayı eklemek için **Ekle** ' yi seçin.

Kısa bir süre sonra, uygulamanın yapılandırma bölmesini görebilirsiniz.

## <a name="configure-the-application-for-password-single-sign-on"></a>Uygulamayı parola çoklu oturum açma için yapılandırma

Bir uygulama için çoklu oturum açmayı yapılandırmak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici**olarak oturum açın.

2. Sol taraftaki menünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.

3. Arama kutusuna **Azure Active Directory** ' yi girin ve **Azure Active Directory** öğesini seçin.

4. Soldaki Azure Active Directory menüsünden **Kurumsal uygulamalar** ' ı seçin.

5. Tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

   Burada istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin**en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar**olarak ayarlayın.

6. Çoklu oturum açma için yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, sol taraftaki uygulamanın menüsünden **Çoklu oturum açma** seçeneğini belirleyin.

8. **Parola tabanlı oturum açma** modunu seçin.

9. [Kullanıcılara uygulama atama](#assign-a-user-to-an-application-directly).

10. Ayrıca, bir kullanıcının satırını seçerek, **kimlik bilgilerini güncelleştir**' i seçerek ve ardından Kullanıcı adı ve parolayı girerek Kullanıcı adına kimlik bilgileri sağlayabilirsiniz. Aksi takdirde, kullanıcılardan uygulamayı başlatırken kimlik bilgilerini girmesi istenir.

## <a name="assign-a-user-to-an-application-directly"></a>Uygulamaya doğrudan kullanıcı atama

Bir uygulamaya doğrudan bir veya daha fazla kullanıcı atamak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) açın ve **genel yönetici**olarak oturum açın.

2. Sol taraftaki menünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.

3. Arama kutusuna **Azure Active Directory** ' yi girin ve **Azure Active Directory** öğesini seçin.

4. Soldaki Azure Active Directory menüsünden **Kurumsal uygulamalar** ' ı seçin.

5. Tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

   Burada istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin**en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar**olarak ayarlayın.

6. Kullanıcı atamak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, sol taraftaki uygulama menüsünden **Kullanıcılar ve gruplar** ' ı seçin.

8. **Atama Ekle** bölmesini açmak için **Kullanıcılar ve gruplar** listesinin üstündeki **Ekle** düğmesini seçin.

9. **Atama Ekle** bölmesinden **Kullanıcılar ve gruplar ' ı** seçin.

10. **Ada veya e-posta adresine göre ara** arama kutusuna kullanıcının tam adını veya e-posta adresini girin.

11. Listedeki kullanıcının üzerine gelin ve ardından **Seçilen** listeye eklemek için kullanıcının profil fotoğrafı veya logosu yanındaki onay kutusunu işaretleyin.

12. İsteğe bağlı: Birden fazla kullanıcı eklemek istiyorsanız **ada veya e-posta adresine göre ara** kutusuna başka bir tam ad veya e-posta adresi girin ve bu kullanıcının **Seçili** listeye eklemek için onay kutusunu seçin.

13. Kullanıcıları seçmeyi tamamladığınızda, uygulamayı atanacak kullanıcılar ve gruplar listesine eklemek için **Seç** düğmesini kullanın.

14. İsteğe bağlı: Seçtiğiniz kullanıcılara atanacak bir rol seçmek için **atama Ekle** bölmesinde **Rol Seç** komutunu kullanın.

15. Uygulamayı seçili kullanıcılara atamak için **ata** ' yı seçin.

## <a name="assign-an-application-to-a-group-directly"></a>Bir gruba doğrudan bir uygulama atama

Bir uygulamaya doğrudan bir veya daha fazla grup atamak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) açın ve **genel yönetici**olarak oturum açın.

2. Sol taraftaki menünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.

3. Arama kutusuna **Azure Active Directory** ' yi girin ve **Azure Active Directory** öğesini seçin.

4. Sol taraftaki Azure AD menüsünden **Kurumsal uygulamalar** ' ı seçin.

5. Tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

   Burada istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar**olarak ayarlayın.

6. Kullanıcı atamak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, sol taraftaki uygulama menüsünden **Kullanıcılar ve gruplar** ' ı seçin.

8. **Atama Ekle** bölmesini açmak için **Kullanıcılar ve gruplar** listesinin en üstündeki **Ekle** düğmesini seçin.

9. **Atama Ekle** bölmesinden **Kullanıcılar ve gruplar ' ı** seçin.

10. **Ada veya e-posta adresine göre ara** arama kutusuna atamak istediğiniz grubun tam grup adını girin.

11. Listede grubun üzerine gelin ve ardından grubun profil fotoğrafı veya logosu ' nın yanındaki onay kutusunu seçerek grubu **Seçili** listeye ekleyin.

12. İsteğe bağlı: Birden fazla grup eklemek istiyorsanız **ada veya e-posta adresine göre ara** arama kutusuna başka bir tam grup adı girin ve ardından bu grubu **Seçili** listeye eklemek için ilgili onay kutusunu seçin.

13. Grupları seçmeyi tamamladığınızda, uygulamayı atanacak kullanıcılar ve gruplar listesine eklemek için **Seç** düğmesini kullanın.

14. İsteğe bağlı: Seçtiğiniz gruplara atanacak bir rol seçmek için **atama Ekle** bölmesinde **Rol Seç** komutunu kullanın.

15. Uygulamayı seçili gruplara atamak için **ata** ' yı seçin.

Kısa bir süre sonra, seçtiğiniz kullanıcılar bu uygulamaları erişim panelinden başlatabilmelidir.

## <a name="next-steps"></a>Sonraki adımlar
[Uygulama proxy 'si aracılığıyla uygulamalarınıza çoklu oturum açma sağlayın](application-proxy-configure-single-sign-on-with-kcd.md).
