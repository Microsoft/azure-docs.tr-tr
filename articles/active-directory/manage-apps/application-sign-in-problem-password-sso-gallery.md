---
title: SSO için yapılandırılmış Azure AD Galeri uygulamasında oturum açma sorunu | Microsoft Docs
description: Parola çoklu oturum açma için yapılandırılmış Azure AD Galeri uygulamasıyla ilgili sorunları giderme.
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9fd17d9e066be6a1abff5165436a09b8921184e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381301"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>SSO için yapılandırılmış bir Azure AD Galeri uygulamasıyla oturum açma sorunları

Access panel, Web tabanlı bir portaldır. Azure Active Directory (Azure AD) iş veya okul hesabı olan kullanıcıların, izinleri olan bulut tabanlı uygulamalara erişmesine olanak sağlar. Azure AD sürümlerine sahip kullanıcılar, erişim paneli aracılığıyla self servis grup ve uygulama yönetimi özelliklerini de kullanabilir.

Erişim paneli Azure portal farklıdır. Kullanıcıların erişim paneli 'ni kullanması için bir Azure aboneliğine ihtiyacı yoktur.

Erişim paneli 'nde parola tabanlı çoklu oturum açma 'yı (SSO) kullanmak için tarayıcınıza erişim paneli uzantısı yüklenmelidir. Parola tabanlı SSO için yapılandırılmış bir uygulama seçtiğinizde uzantı otomatik olarak indirilir.

## <a name="browser-requirements-for-access-panel"></a>Erişim paneli için tarayıcı gereksinimleri

Erişim paneli JavaScript 'ı destekleyen ve CSS 'nin etkinleştirildiği bir tarayıcı gerektirir.

Aşağıdaki tarayıcılarda parola tabanlı SSO desteklenir:

- Windows 7 veya sonraki sürümlerde Internet Explorer 8, 9, 10 ve 11

- Windows 7 veya üzeri cihazlarda veya MacOS X veya üzeri cihazlarda Chrome

- Windows XP SP2 veya sonraki sürümlerde veya Mac OS X 10,6 veya üzeri sürümlerde Firefox 26,0 veya üzeri

>[!NOTE]
>Microsoft Edge 'e tarayıcı uzantıları desteği eklendiğinde, parola tabanlı SSO uzantısı Windows 10 ' da Microsoft Edge için kullanılabilir hale gelir.

## <a name="install-the-access-panel-browser-extension"></a>Erişim paneli tarayıcı uzantısını yükler

Şu adımları uygulayın:

1. [Access panel](https://myapps.microsoft.com) 'i desteklenen bir tarayıcıda açın ve Azure AD 'de Kullanıcı olarak oturum açın.

2. Erişim paneli 'nde parola-SSO özellikli bir uygulama seçin.

3. Sorulduğunda **Şimdi yüklensin**' i seçin.

4. Tarayıcınızı temel alan bir indirme bağlantısına yönlendirilirsiniz. Tarayıcı uzantısını yüklemek için **Ekle** ' yi seçin.

5. İstenirse **Etkinleştir** veya **izin ver**' i seçin.

6. Yükleme sonrasında tarayıcınızı yeniden başlatın.

7.  Erişim paneli ' nde oturum açın ve parola SSO özellikli uygulamalarınızı başlatıp başlatamadıysanız bakın.

Ayrıca, Chrome ve Firefox uzantılarını bu bağlantılar aracılığıyla doğrudan indirebilirsiniz:

-   [Chrome erişim paneli uzantısı](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox erişim paneli uzantısı](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Internet Explorer için bir grup ilkesi ayarlama

Kullanıcılarınızın makinelerinde Internet Explorer için erişim paneli uzantısını uzaktan yüklemenize olanak tanıyan bir grup ilkesi ayarlayabilirsiniz.

Önkoşullar şunlardır:

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) ayarlanması gerekir ve kullanıcılarınızın makineleri etki alanına katılması gerekir.

-   Grup ilkesi nesnesini (GPO) düzenlemek için "Ayarları Düzenle" izninizin olması gerekir. Varsayılan olarak, aşağıdaki güvenlik gruplarının üyeleri bu izne sahiptir: Etki alanı yöneticileri, kuruluş yöneticileri ve grup ilkesi Oluşturucu sahipleri. [Daha fazla bilgi edinin](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Grup ilkesini yapılandırmak ve kullanıcılara dağıtmak için bkz. [Grup İlkesi kullanarak Internet Explorer Için erişim paneli uzantısını dağıtma](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Internet Explorer 'da erişim paneli sorunlarını giderme

Uzantıyı yapılandırmaya yönelik bir tanılama aracına ve yönergelerine erişmek için bkz. [Internet Explorer Için erişim paneli uzantısı sorunlarını giderme](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Azure AD Galeri uygulaması için parola SSO 'yu yapılandırma

Azure AD Galerisi 'nden bir uygulamayı yapılandırmak için şu işlemleri yapmanız gerekir:

-   Uygulamayı Azure AD Galerisi 'nden ekleyin
-   [Uygulamayı parola çoklu oturum açma için yapılandırma](#configure-the-app-for-password-sso)
-   [Uygulamaya Kullanıcı atama](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Uygulamayı Azure AD Galerisi 'nden ekleyin

Şu adımları uygulayın:

1. [Azure Portal](https://portal.azure.com) açın ve genel yönetici veya ortak yönetici olarak oturum açın.

2. Azure AD uzantısını açmak için sol taraftaki Gezinti bölmesinin en üstünde bulunan **tüm hizmetler** ' i seçin.

3. Filtre arama kutusuna **Azure Active Directory** yazın ve **Azure Active Directory**' ı seçin.

4. Azure AD gezinti bölmesinden **Kurumsal uygulamalar** ' ı seçin.

5. **Kurumsal uygulamalar** bölmesinin sağ üst köşesindeki **Ekle** ' yi seçin.

6. **Galeriden Ekle** bölümünde, **bir ad girin** kutusuna uygulamanın adını yazın.

7. SSO için yapılandırmak istediğiniz uygulamayı seçin.

8. *Seçim* Uygulamayı eklemeden önce **ad** kutusunda adını değiştirebilirsiniz.

9. Uygulamayı eklemek için **Ekle** ' ye tıklayın.

   Kısa bir gecikmeden sonra uygulamanın yapılandırma bölmesini görebileceksiniz.

### <a name="configure-the-app-for-password-sso"></a>Uygulamayı parola SSO 'SU için yapılandırma

Şu adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/) açın ve genel yönetici veya ortak yönetici olarak oturum açın.

2. Azure AD uzantısını açmak için sol taraftaki Gezinti bölmesinin en üstünde bulunan **tüm hizmetler** ' i seçin.

3. Filtre arama kutusuna **Azure Active Directory** yazın ve **Azure Active Directory**' ı seçin.

4. Azure AD gezinti bölmesinde **Kurumsal uygulamalar** ' ı seçin.

5. Uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

   > [!NOTE]
   > İstediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin**en üstündeki **filtre** denetimini kullanın. **Göster** seçeneğini "tüm uygulamalar" olarak ayarlayın.

6. SSO için yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol tarafındaki bölmedeki **Çoklu oturum açma** seçeneğini belirleyin.

8. **Parola tabanlı oturum açma** modunu seçin.

9. Kullanıcıları uygulamaya atayın.

10. Ayrıca, kullanıcılar için kimlik bilgileri de sağlayabilirsiniz. (Aksi takdirde, kullanıcılardan uygulama başlangıcında kimlik bilgilerini girmesi istenir.) Bunu yapmak için kullanıcıların satırlarını seçin. Ardından **kimlik bilgilerini güncelleştir** ' i seçin ve Kullanıcı adlarını ve parolalarını girin.

### <a name="assign-users-to-the-app"></a>Uygulamaya kullanıcı atama

Kullanıcıları doğrudan bir uygulamaya atamak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) açın ve genel yönetici olarak oturum açın.

2. Azure AD uzantısını açmak için sol taraftaki Gezinti sorunun **tüm hizmetleri** ' ni seçin.

3. Filtre arama kutusuna **Azure Active Directory** yazın ve **Azure Active Directory**' ı seçin.

4. Azure AD gezinti bölmesinde **Kurumsal uygulamalar** ' ı seçin.

5. Uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

   > [!NOTE]
   > İstediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin**en üstündeki **filtre** denetimini kullanın. **Göster** seçeneğini "tüm uygulamalar" olarak ayarlayın.

6. Listeden, kullanıcı atamak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, sol taraftaki uygulamanın gezinti bölmesinden **Kullanıcılar ve gruplar** ' ı seçin.

8. **Atama Ekle** bölmesini açmak için **Kullanıcılar ve gruplar** listesinin üst kısmında **Ekle** ' yi seçin.

9. **Atama Ekle** bölmesinde **Kullanıcılar ve gruplar ' ı** seçin.

10. **Ada veya e-posta adresine göre ara** kutusuna, atamak istediğiniz kullanıcının tam adını veya e-posta adresini yazın.

11. Listedeki kullanıcının üzerine gelin. Kullanıcının profil fotoğrafı veya logosu ' nın yanındaki onay kutusunu seçerek bu kullanıcıyı **Seçili** listeye ekleyin.

12. *Seçim* Başka bir kullanıcı eklemek için **ada veya e-posta adresine göre ara** kutusuna başka bir ad veya e-posta adresi yazın ve ardından bu kullanıcıyı **Seçili** listeye eklemek için onay kutusunu işaretleyin.

13. Kullanıcıları seçmeyi tamamladığınızda, uygulamayı uygulamaya atanan kullanıcı ve gruplar listesine eklemek için **Seç** ' e tıklayın.

14. *Seçim* Seçtiğiniz kullanıcılara atanacak bir rol seçmek için **atama Ekle** bölmesinde **Rol Seç** ' e tıklayın.

15. Uygulamayı seçili kullanıcılara atamak için **ata** ' yı seçin.

    Kısa bir gecikmeden sonra kullanıcılar bu uygulamalara erişim panelinden erişebilecektir.

## <a name="request-support"></a>Destek iste 
SSO 'yu ayarlarken ve kullanıcıları atarken bir hata mesajı alırsanız, bir destek bileti açın. Mümkün olduğunca aşağıdaki bilgilerin çoğunu ekleyin:

-   Bağıntı hata KIMLIĞI
-   UPN (Kullanıcı e-posta adresi)
-   Değerine
-   Tarayıcı türü
-   Hatanın gerçekleştiği saat dilimi ve saat/saat dilimi
-   Fiddler izlemeleri

## <a name="next-steps"></a>Sonraki adımlar
[Uygulama Ara sunucusu ile uygulamalarınıza çoklu oturum açma sağlayın](application-proxy-configure-single-sign-on-with-kcd.md)
