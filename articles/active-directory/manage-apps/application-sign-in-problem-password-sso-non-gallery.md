---
title: Access panel 'de parola tabanlı çoklu oturum açma (SSO) | Microsoft Docs
description: Parola çoklu oturum açma için yapılandırılmış Azure AD Galeri uygulamalarında oturum açma ile ilgili sorunları gidermeye yönelik yönergeler sunan sorun sorunlarını açıklar.
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca192c28757df189e531aee0ba2d8da288ba7e6
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381229"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Parola çoklu oturum açma için yapılandırılmış bir Azure AD Galeri uygulamasında oturum açma sorunları

Erişim paneli, Azure Active Directory (Azure AD) içinde bir iş veya okul hesabına sahip olan bir kullanıcının, Azure AD yöneticisinin erişim izni verdiği bulut tabanlı uygulamaları görüntülemesini ve kullanmasını sağlayan Web tabanlı bir portaldır. Azure AD sürümleri olan bir Kullanıcı, erişim paneli aracılığıyla self servis grup ve uygulama yönetimi özelliklerini de kullanabilir. Erişim paneli Azure portal ayrıdır ve kullanıcıların bir Azure aboneliğine sahip olmasını gerektirmez.

Erişim panelinde parola tabanlı çoklu oturum açma 'yı (SSO) kullanmak için, erişim paneli uzantısının kullanıcının tarayıcısına yüklenmesi gerekir. Bu uzantı, bir kullanıcı parola tabanlı SSO için yapılandırılmış bir uygulamayı seçtiğinde otomatik olarak indirilir.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Erişim paneli için toplantı tarayıcısı gereksinimleri

Erişim paneli JavaScript 'ı destekleyen ve CSS 'nin etkinleştirildiği bir tarayıcı gerektirir. Erişim panelinde parola tabanlı çoklu oturum açma 'yı (SSO) kullanmak için, erişim paneli uzantısının kullanıcının tarayıcısına yüklenmesi gerekir. Bu uzantı, bir kullanıcı parola tabanlı SSO için yapılandırılmış bir uygulamayı seçtiğinde otomatik olarak indirilir.

Parola tabanlı SSO için son kullanıcının tarayıcıları şunları yapabilir:

-   Internet Explorer 8, 9, 10, 11--Windows 7 veya sonraki sürümlerde

-   Chrome--Windows 7 veya üzeri sürümlerde ve MacOS X veya üzeri sürümlerde

-   Firefox 26,0 veya üzeri--Windows XP SP2 veya üzeri sürümlerde ve Mac OS X 10,6 veya üzeri

>[!NOTE]
>Parola tabanlı SSO uzantısı, Microsoft Edge için tarayıcı uzantıları desteklenmesine göre Windows 10 ' da Microsoft Edge için kullanılabilir hale gelir.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Erişim paneli tarayıcı uzantısını nasıl yüklenir

Erişim paneli tarayıcı uzantısını yüklemek için aşağıdaki adımları izleyin:

1.  Desteklenen tarayıcılardan birinde [erişim panelini](https://myapps.microsoft.com) açın ve Azure AD 'de **Kullanıcı** olarak oturum açın.

2.  Erişim panelinde bir **Password-SSO uygulamasına** tıklayın.

3.  Yazılımı yüklemek isteyip istememe isteminde **Şimdi yüklensin**' i seçin.

4.  Tarayıcınıza bağlı olarak, indirme bağlantısına yönlendirilirsiniz. Uzantıyı tarayıcınıza **ekleyin** .

5.  Tarayıcınız istediğinde, uzantıyı **etkinleştirmek** veya **izin vermek** için seçin.

6.  Yüklendikten sonra tarayıcı oturumunuzu **yeniden başlatın** .

7.  Erişim panelinde oturum açın ve parola SSO uygulamalarınızı **başlatıp başlatamadıysanız** bkz.

Chrome ve Firefox uzantısını aşağıdaki doğrudan bağlantılardan de indirebilirsiniz:

-   [Chrome erişim paneli uzantısı](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox erişim paneli uzantısı](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Internet Explorer için bir grup ilkesi ayarlama

Kullanıcılarınızın makinelerinde Internet Explorer için erişim paneli uzantısını uzaktan yüklemenize izin veren bir grup ilkesi ayarlayabilirsiniz.

Önkoşullar şunları içerir:

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)ayarlamış ve kullanıcılarınızın makinelerinizi etki alanına katıldıysanız.

-   Grup ilkesi nesnesini (GPO) düzenlemek için "Ayarları Düzenle" izninizin olması gerekir. Varsayılan olarak, aşağıdaki güvenlik gruplarının üyeleri bu izne sahiptir: Etki alanı yöneticileri, kuruluş yöneticileri ve grup ilkesi Oluşturucu sahipleri. [Daha fazla bilgi edinin](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Grup ilkesini yapılandırma ve kullanıcılara dağıtma hakkında adım adım yönergeler için [Grup İlkesi kullanarak Internet Explorer Için erişim paneli uzantısını dağıtma](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) öğreticisini izleyin.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Internet Explorer 'da erişim paneli sorunlarını giderme

Tanılama aracına erişim için [Internet Explorer Için erişim paneli uzantısının sorunlarını giderme](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) Kılavuzu ve IE için uzantıyı yapılandırmaya yönelik adım adım yönergeleri izleyin.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Galeri dışı bir uygulama için parola çoklu oturum açmayı yapılandırma

Azure AD Galerisi 'nden bir uygulamayı yapılandırmak için şunları yapmanız gerekir:

-   [Galeri dışı bir uygulama ekleme](#add-a-non-gallery-application)

-   [Uygulamayı parola çoklu oturum açma için yapılandırma](#configure-the-application-for-password-single-sign-on)

-   [Uygulamaya Kullanıcı atama](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Galeri dışı bir uygulama ekleme

Azure AD Galerisi 'nden bir uygulama eklemek için aşağıdaki adımları izleyin:

1.  [Azure Portal](https://portal.azure.com) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4.  Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5.  **Kurumsal uygulamalar** bölmesindeki sağ üst köşedeki **Ekle** düğmesine tıklayın.

6.  **Galeri dışı uygulama** ' ya tıklayın.

7.  **Ad** metin kutusuna uygulamanızın adını girin. Ekle ' yi seçin **.**

Kısa bir süre sonra, uygulamanın yapılandırma bölmesini görebilirsiniz.

### <a name="configure-the-application-for-password-single-sign-on"></a>Uygulamayı parola çoklu oturum açma için yapılandırma

Bir uygulama için çoklu oturum açmayı yapılandırmak için aşağıdaki adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada show istediğiniz uygulamayı göremiyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini** ayarlayıp **Göster** seçeneğini **tüm Uygulamalar.**

6. Çoklu oturum açmayı yapılandırmak istediğiniz uygulamayı seçin

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.

8. Mod **parola tabanlı oturum açmayı seçin.**

9. **Oturum açma URL 'sini**girin. Bu, kullanıcıların oturum açmak için Kullanıcı adını ve parolasını girmesi gereken URL 'dir. Oturum açma alanlarının URL 'de görünür olduğundan emin olun.

10. Kullanıcılara uygulama atama.

11. Ayrıca, kullanıcıların adına Kullanıcı adına kimlik bilgileri de sağlayabilirsiniz ve **kimlik bilgilerini güncelleştir** ' i tıklatarak ve Kullanıcı adına Kullanıcı adını ve parolayı girebilirsiniz. Aksi takdirde, kullanıcılardan başlatma sırasında kimlik bilgilerini girmesi istenir.

### <a name="assign-users-to-the-application"></a>Uygulamaya kullanıcı atama

Bir veya daha fazla kullanıcıları uygulamaya doğrudan atamak için aşağıdaki adımları izleyin:

1. Açık [ **Azure portalında** ](https://portal.azure.com/) ve oturum açma bir **genel yönetici.**

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada show istediğiniz uygulamayı göremiyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini** ayarlayıp **Göster** seçeneğini **tüm Uygulamalar.**

6. Listeden bir kullanıcıya atamak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Kullanıcılar ve gruplar** ' a tıklayın.

8. Tıklayın **Ekle** üstünde düğme **kullanıcılar ve gruplar** listesini açmak için **atama Ekle** bölmesi.

9. tıklayın **kullanıcılar ve gruplar** seçiciden **atama Ekle** bölmesi.

10. Yazın **tam adı** veya **e-posta adresi** içine atama isteyen kullanıcının **adına veya e-posta adresine göre arama** arama kutusu.

11. Üzerine **kullanıcı** göstermek için listedeki bir **onay kutusu**. Kullanıcının profil fotoğrafı veya kullanıcı için eklenecek logosu yanındaki onay kutusuna tıklayın **seçili** listesi.

12. **Seçim** Birden **fazla kullanıcı eklemek**istiyorsanız **ada veya e-posta adresine göre ara** kutusuna başka bir **tam ad** veya **e-posta adresi** yazın ve bu kullanıcıyı **Seçili** listeye eklemek için onay kutusuna tıklayın.

13. Kullanıcı seçme işlemini tamamladığınızda, tıklayın **seçin** uygulamaya atanan kullanıcıların ve grupların listesi eklemek için düğme.

14. **İsteğe bağlı:** tıklayın **rolü Seç** seçicide **atama Ekle** bölmesinde seçtiğiniz kullanıcılara atamak için bir rol seçin.

15. Tıklayın **atama** düğmesi Seçilen kullanıcılara uygulamayı atamak için.

Kısa bir süre sonra, seçtiğiniz kullanıcılar erişim panelinde bu uygulamaları başlatabilir.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Bu sorun giderme adımları sorunu gidermezse

varsa, aşağıdaki bilgilerle bir destek bileti açın:

-   Bağıntı hata KIMLIĞI

-   UPN (Kullanıcı e-posta adresi)

-   Değerine

-   Tarayıcı türü

-   Hata sırasında saat dilimi ve zaman/zaman dilimi

-   Fiddler izlemeleri

## <a name="next-steps"></a>Sonraki adımlar
[Uygulama Ara sunucusu ile uygulamalarınıza çoklu oturum açma sağlayın](application-proxy-configure-single-sign-on-with-kcd.md)

