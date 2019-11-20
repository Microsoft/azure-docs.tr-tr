---
title: Uygulama erişim paneli tarayıcı uzantısını yükler-Azure | Microsoft Docs
description: Erişim paneli tarayıcı uzantısını yüklerken karşılaşılan yaygın hataları giderme.
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
ms.topic: article
ms.date: 05/04/2018
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 580207bb10680e84cfda7d4b1874f2b460602973
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082175"
---
# <a name="install-the-access-panel-browser-extension"></a>Erişim paneli tarayıcı uzantısını yükler

Erişim paneli, Web tabanlı bir portaldır. Azure Active Directory (Azure AD) ' de bir iş veya okul hesabınız varsa, erişim panelini kullanarak bir Azure AD yöneticisinin erişim izni verdiği bulut tabanlı uygulamaları görüntüleyebilir ve başlatabilirsiniz. 

Azure AD sürümlerini kullanıyorsanız, erişim paneli aracılığıyla self servis grup ve uygulama yönetimi özelliklerini de kullanabilirsiniz. 

Erişim paneli Azure portal ayrıdır. Azure aboneliğinizin olması gerekmez.

## <a name="web-browser-requirements"></a>Web tarayıcısı gereksinimleri

En azından, erişim paneli JavaScript 'ı destekleyen ve CSS 'nin etkinleştirildiği bir tarayıcı gerektirir. Erişim panelinde parola tabanlı SSO aracılığıyla uygulamalarda oturum açmanız için, tarayıcıda erişim paneli uzantısının yüklü olması gerekir. Parola tabanlı SSO için yapılandırılmış bir uygulama seçtiğinizde uzantı otomatik olarak indirilir.

Parola tabanlı SSO için aşağıdaki tarayıcılardan herhangi birini kullanabilirsiniz:

- **Microsoft Edge**: Windows 10 yıldönümü sürümünde veya sonraki sürümlerde. 
- **Chrome**: Windows 7 veya sonraki sürümlerde ve MacOS X veya üzeri sürümlerde.
- **Firefox 26,0 veya üzeri**: WINDOWS XP SP2 veya sonraki sürümlerde ve Mac OS X 10,6 veya üzeri sürümlerde.

## <a name="install-the-access-panel-browser-extension"></a>Erişim paneli tarayıcı uzantısını yükler

Erişim paneli tarayıcı uzantısını yüklemek için aşağıdakileri yapın:

1.  Desteklenen tarayıcılardan birinde, [erişim panelini](https://myapps.microsoft.com)açın ve ardından Azure AD hesabınızda bir kullanıcı olarak oturum açın.

2.  Parola tabanlı bir SSO uygulaması seçin.

3.  İstendiğinde, **Şimdi yüklensin**' i seçin.  
    Seçtiğiniz tarayıcı için indirme bağlantısına yönlendirilirsiniz. 
    
4.  **Add (Ekle)** seçeneğini belirleyin.

5.  İstenirse, uzantıyı **etkinleştirin** ya da **buna izin verin** .

6.  Yükleme tamamlandıktan sonra tarayıcınızı yeniden başlatın.

7.  Erişim paneli ' nde oturum açın ve parola tabanlı SSO uygulamalarınızı başlatıp başlatamayacağını denetleyin.

Chrome ve Microsoft Edge 'in uzantısını doğrudan aşağıdaki sitelerden da indirebilirsiniz:

- [Chrome uzantısı](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Microsoft Edge uzantısı](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Firefox uzantısı](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Uygulamalarımın güvenli oturum açma uzantısını kullanma
* `https://myapps.microsoft.com`dışında bir uygulamam URL 'SI kullanıyorsanız, varsayılan URL 'nizi aşağıdakileri yaparak yapılandırın:
   1. Uzantıya *oturum açmadığınızdan uzantı* simgesine sağ tıklayın.
   2. Menüsünde, **uygulamalar URL 'si**' ni seçin.
   3. Varsayılan URL 'nizi seçin.
   4. Uzantı simgesini seçin.
   5. Uzantıya oturum açmak için **oturum aç**' ı seçerek kullanmaya başlayın.

* Tarayıcıdan doğrudan bir uygulamada oturum açmak için aşağıdakileri yapın:
   1. Uzantıyı yükledikten sonra, **kullanmaya başlamak Için oturum aç**' ı seçerek oturum açın.
   2. Oturum açma URL 'SI ile uygulamada oturum açın.  
       Oturum açma URL 'SI genellikle oturum açma formunu gösteren uygulamanın URL 'sidir.
      Uzantı durumu değiştirmeli ve bir parolanın kullanılabilir olduğunu bilmenizi sağlar.
   3. Oturum açmak için uzantı simgesini seçin.

* Uzantıdan bir uygulamayı başlatmak için aşağıdakileri yapın:
   1. Uzantıyı yükledikten sonra, **kullanmaya başlamak Için oturum aç**' ı seçerek oturum açın.
   2. Kendi menüsünü açmak için uzantı simgesini seçin.
   3. Uygulamalarım portalındaki kullanılabilir bir uygulamayı arayın.
   4. Arama sonuçları listesinde, uygulamayı seçin.  
       Kullandığınız son üç uygulama, **son kullanılan** kısayol listesinde görüntülenir.
       
* Uzak sırada iç şirket URL 'Lerini kullanmak için şunları yapın:
    1. Kiracınızda [uygulama proxy 'Sini yapılandırma](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable)
    2. Uygulama proxy 'si aracılığıyla uygulamayı ve URL 'YI [Yayımlama](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal)
    3. Uzantıyı yükledikten sonra oturum aç ' ı seçerek oturum açın ve kullanmaya başlayın
    4. Artık uzak sırada bile iç şirket URL 'sine gidebilirsiniz

> [!NOTE]
> Yukarıdaki seçenekler yalnızca Microsoft Edge, Chrome ve Firefox için kullanılabilir.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Internet Explorer için bir grup ilkesi ayarlama

Kullanıcılarınızın makinelerinde Internet Explorer için erişim paneli uzantısını uzaktan yüklemenize olanak tanıyan bir grup ilkesi ayarlayabilirsiniz.

Bir grup ilkesi ayarlamadan önce aşağıdakileri doğrulayın:

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)ayarlamış ve kullanıcılarınızın makinelerinizi etki alanına katıldıysanız.

-   Grup ilkesi nesnesini (GPO) düzenlemek için, *düzenleme ayarları* izinleriniz olmalıdır. Varsayılan olarak, bu izin şu güvenlik gruplarının üyelerine verilir: etki alanı yöneticileri, kuruluş yöneticileri ve Grup İlkesi Oluşturucu sahipleri.

Grup ilkesini yapılandırma ve kullanıcılara dağıtma hakkında adım adım yönergeler için, bkz. [Grup İlkesi kullanarak Internet Explorer için erişim paneli uzantısını dağıtma](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Internet Explorer 'da erişim paneli uzantısı sorunlarını giderme

Bir tanılama aracına ve Internet Explorer için uzantıyı yapılandırmayla ilgili bilgilere erişim için, bkz. [Internet Explorer için erişim paneli uzantısı sorunlarını giderme](manage-access-panel-browser-extension.md).

> [!NOTE]
> Internet Explorer sınırlı desteğe sahiptir ve artık yeni yazılım güncelleştirmeleri almaz. Microsoft Edge önerilen tarayıcıdır.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Yukarıdaki adımlar sorunu çözmezse

Varsa, aşağıdaki bilgilerle bir destek bileti açın:

-   Bağıntı hata KIMLIĞI
-   UPN (Kullanıcı e-posta adresi)
-   Değerine
-   Tarayıcı türü
-   Hatanın gerçekleştiği saat dilimi ve saat veya zaman dilimi
-   Fiddler izlemeleri

## <a name="next-steps"></a>Sonraki adımlar
[Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](what-is-single-sign-on.md)
