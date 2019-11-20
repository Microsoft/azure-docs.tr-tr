---
title: Erişim panelinden bir uygulamada oturum açma sorunları | Microsoft Docs
description: Myapps.microsoft.com adresindeki Microsoft Azure AD erişim panelinden bir uygulamaya erişim sorunlarını giderme
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
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b93ee38666b93253c7cda6c756d4f58daaea236
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082154"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Erişim panelinden bir uygulamada oturum açma sorunları

Erişim paneli, Azure Active Directory (Azure AD) ' de iş veya okul hesabı olan bir kullanıcının Azure AD yöneticisinin erişim izni verdiği bulut tabanlı uygulamaları görüntülemesini ve başlatmasını sağlayan Web tabanlı bir portaldır. 

Bu uygulamalar, Azure AD portalındaki Kullanıcı adına yapılandırılır. Uygulama, uygulamayı erişim paneli 'nde görmek için bir Kullanıcı ya da Kullanıcı üyesi olan bir gruba atanmalıdır.

Bir kullanıcının görebilecekleri uygulamaların türü aşağıdaki kategorilere ayrılır:

-   Office 365 uygulamaları

-   Federasyon tabanlı SSO ile yapılandırılan Microsoft ve üçüncü taraf uygulamalar

-   Parola tabanlı SSO uygulamaları

-   Var olan SSO çözümlerini içeren uygulamalar

## <a name="general-issues-to-check-first"></a>Önce denetlenecek genel sorunlar

-   Erişim paneli için en düşük gereksinimleri karşılayan bir **tarayıcı** kullandığınızdan emin olun.

-   Kullanıcının tarayıcısının uygulamanın URL 'sini **Güvenilen sitelerine**eklediğinizden emin olun.

-   Uygulamanın doğru şekilde **yapılandırıldığını** kontrol ettiğinizden emin olun.

-   Oturum açma işlemlerinin Kullanıcı hesabının **etkinleştirildiğinden** emin olun.

-   Kullanıcı hesabının **kilitlenmediğinden** emin olun.

-   Kullanıcının **parolasının dolmadığından veya unutulmuş** olduğundan emin olun.

-   **Multi-Factor Authentication** Kullanıcı erişimini engellemediğinden emin olun.

-   **Koşullu erişim ilkesi** veya **kimlik koruma** ilkesinin Kullanıcı erişimini engellemediğinden emin olun.

-   Multi-Factor Authentication veya koşullu erişim ilkelerinin zorlanmasını sağlamak için kullanıcının **kimlik doğrulaması iletişim bilgilerinin** güncel olduğundan emin olun.

-   Ayrıca, tarayıcınızın tanımlama bilgilerini temizlemeyi ve yeniden oturum açmayı denediğinizden emin olun.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Erişim paneli için toplantı tarayıcısı gereksinimleri

Erişim paneli JavaScript 'ı destekleyen ve CSS 'nin etkinleştirildiği bir tarayıcı gerektirir. Erişim panelinde parola tabanlı çoklu oturum açma 'yı (SSO) kullanmak için, erişim paneli uzantısının kullanıcının tarayıcısına yüklenmesi gerekir. Bu uzantı, bir kullanıcı parola tabanlı SSO için yapılandırılmış bir uygulamayı seçtiğinde otomatik olarak indirilir.

Parola tabanlı SSO için son kullanıcının tarayıcıları şunları yapabilir:

-   Internet Explorer 8, 9, 10, 11--Windows 7 veya sonraki sürümlerde

-   Windows 10 yıldönümü sürümü veya sonraki sürümlerde Microsoft Edge

-   Chrome--Windows 7 veya üzeri sürümlerde ve MacOS X veya üzeri sürümlerde

-   Firefox 26,0 veya üzeri--Windows XP SP2 veya üzeri sürümlerde ve Mac OS X 10,6 veya üzeri

## <a name="how-to-install-the-access-panel-browser-extension"></a>Erişim paneli tarayıcı uzantısını nasıl yüklenir

Erişim paneli tarayıcı uzantısını yüklemek için aşağıdaki adımları izleyin:

1.  Desteklenen tarayıcılardan birinde [erişim panelini](https://myapps.microsoft.com) açın ve Azure AD 'de **Kullanıcı** olarak oturum açın.

2.  Erişim panelinde bir **Password-SSO uygulamasına** tıklayın.

3.  Yazılımı yüklemek isteyip istememe isteminde **Şimdi yüklensin**' i seçin.

4.  Tarayıcınıza bağlı olarak, indirme bağlantısına yönlendirilirsiniz. Uzantıyı tarayıcınıza **ekleyin** .

5.  Tarayıcınız istediğinde, uzantıyı **etkinleştirmek** veya **izin vermek** için seçin.

6.  Yüklendikten sonra tarayıcı oturumunuzu **yeniden başlatın** .

7.  Erişim panelinde oturum açın ve parola SSO uygulamalarınızı **başlatıp başlatamadıysanız** bkz.

Chrome ve Microsoft Edge 'in uzantısını aşağıdaki doğrudan bağlantılardan de indirebilirsiniz:

-   [Chrome erişim paneli uzantısı](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Microsoft Edge erişim paneli uzantısı](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Azure AD Galeri uygulaması için Federasyon çoklu oturum açmayı yapılandırma

Azure AD galerisinde Kurumsal Çoklu oturum açma özelliğiyle etkinleştirilen tüm uygulamaların sunulan adım adım öğretici vardır. Ayrıntılı adım adım yönergeler için, [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğretici listesine](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) erişebilirsiniz.

Azure AD Galerisi 'nden bir uygulamayı yapılandırmak için şunları yapmanız gerekir:

-   Azure AD Galerisi 'nden uygulama ekleme

-   [Azure AD 'de uygulamanın meta veri değerlerini yapılandırma (oturum açma URL 'SI, tanımlayıcı, yanıt URL 'SI)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Kullanıcı tanımlayıcısı ' nı seçin ve uygulamaya gönderilecek kullanıcı özniteliklerini ekleyin](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Azure AD meta verilerini ve sertifikasını alma](#download-the-azure-ad-metadata-or-certificate)

-   [Uygulamada Azure AD meta veri değerlerini yapılandırma (oturum açma URL 'SI, verenin, oturum kapatma URL 'SI ve sertifika)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Uygulamaya kullanıcı atama

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Azure AD Galerisi 'nden uygulama ekleme

Azure AD Galerisi 'nden bir uygulama eklemek için aşağıdaki adımları izleyin:

1.  [Azure Portal](https://portal.azure.com) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4.  Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5.  **Kurumsal uygulamalar** bölmesindeki sağ üst köşedeki **Ekle** düğmesine tıklayın.

6.  **Galeriden Ekle** bölümünden **bir ad girin** metin kutusuna uygulamanın adını yazın.

7.  Çoklu oturum açma için yapılandırmak istediğiniz uygulamayı seçin.

8.  Uygulamayı eklemeden önce **ad metin kutusundan adını değiştirebilirsiniz** .

9.  Uygulamayı eklemek için **Ekle** düğmesine tıklayın.

Kısa bir süre sonra, uygulamanın yapılandırma bölmesini görebilirsiniz.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Azure AD galerisinden bir uygulama için çoklu oturum açmayı yapılandırma

Bir uygulama için çoklu oturum açmayı yapılandırmak için aşağıdaki adımları izleyin:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>[**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada show istediğiniz uygulamayı göremiyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini** ayarlayıp **Göster** seçeneğini **tüm Uygulamalar.**

6. Çoklu oturum açmayı yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.

8. **Mod** açılır listesinden **SAML tabanlı oturum açma '** yı seçin.

9. **Etki alanı ve URL 'ler** için gerekli değerleri girin. Bu değerleri uygulama satıcısından almalısınız.

   1. Uygulamayı SP tarafından başlatılan SSO olarak yapılandırmak için, oturum açma URL 'SI gerekli bir değerdir. Bazı uygulamalarda, tanımlayıcı de gerekli bir değerdir.

   2. Uygulamayı IDP tarafından başlatılan SSO olarak yapılandırmak için, yanıt URL 'SI gerekli bir değerdir. Bazı uygulamalarda, tanımlayıcı de gerekli bir değerdir.

10. **Isteğe bağlı:** gerekli olmayan değerleri görmek ISTIYORSANıZ **Gelişmiş URL ayarlarını göster** ' e tıklayın.

11. **Kullanıcı öznitelikleri**' nde, **Kullanıcı tanımlayıcısı** açılan menüsünde kullanıcılarınız için benzersiz tanımlayıcıyı seçin.

12. **Isteğe bağlı:** kullanıcılar oturum açtığında SAML belirtecinde uygulamaya gönderilecek öznitelikleri düzenlemek için **diğer tüm Kullanıcı özniteliklerini görüntüle ve Düzenle** ' ye tıklayın.

    Bir öznitelik eklemek için:

    1. **öznitelik Ekle**' ye tıklayın. **Adı** girin ve açılan listeden **değeri** seçin.

    2. Kaydet ' e tıklayın **.** Yeni özniteliği tabloda görürsünüz.

13. uygulamada çoklu oturum açmayı yapılandırmayla ilgili belgelere erişmek için **&gt;&lt;uygulama adını Yapılandır** ' a tıklayın. Ayrıca, uygulama ile SSO 'yu ayarlamak için gereken meta veri URL 'Leri ve sertifikaya sahip olursunuz.

14. Yapılandırmayı kaydetmek için **Kaydet** ' e tıklayın.

15. Kullanıcılara uygulama atama.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Kullanıcı tanımlayıcısı ' nı seçin ve uygulamaya gönderilecek kullanıcı özniteliklerini ekleyin

Kullanıcı tanımlayıcısını seçmek veya Kullanıcı öznitelikleri eklemek için aşağıdaki adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada görünmesini istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar** olarak ayarlayın.

6. Çoklu oturum açma yapılandırdığınız uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.

8. **Kullanıcı öznitelikleri** bölümünde, **Kullanıcı tanımlayıcısı** açılan menüsünde kullanıcılarınız için benzersiz tanımlayıcıyı seçin. Seçili seçeneğin, kullanıcının kimliğini doğrulamak için uygulamadaki beklenen değerle eşleşmesi gerekir.

   >[!NOTE]
   >Azure AD, seçilen değere veya SAML AuthRequest 'te uygulama tarafından istenen biçime göre NameID özniteliği (Kullanıcı tanımlayıcısı) biçimini seçin. Daha fazla bilgi için, Nameıdpolicy bölümünde [Çoklu oturum açma SAML Protokolü](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) makalesini ziyaret edin.
   >
   >

9. Kullanıcı öznitelikleri eklemek için, kullanıcılar oturum açtığında SAML belirtecinde uygulamaya gönderilecek öznitelikleri düzenlemek için **diğer tüm Kullanıcı özniteliklerini görüntüle ve Düzenle** ' ye tıklayın.

   Bir öznitelik eklemek için:

   1. **öznitelik Ekle**' ye tıklayın. **Adı** girin ve açılan listeden **değeri** seçin.

   2. Kaydet ' e tıklayın **.** Yeni özniteliği tabloda görürsünüz.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Azure AD meta verilerini veya sertifikasını indirin

Azure AD 'den uygulama meta verilerini veya sertifikasını indirmek için aşağıdaki adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada show istediğiniz uygulamayı göremiyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini** ayarlayıp **Göster** seçeneğini **tüm Uygulamalar.**

6. Çoklu oturum açma yapılandırdığınız uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.

8. **SAML Imzalama sertifikası** bölümüne gidin ve ardından sütun değerini **İndir** ' e tıklayın. Uygulamanın çoklu oturum açmayı yapılandırma ihtiyacı olduğuna bağlı olarak, meta veri XML 'sini veya sertifikayı indirme seçeneğini görürsünüz.

   Azure AD meta verileri almak için bir URL sağlamıyor. Meta veriler yalnızca bir XML dosyası olarak alınabilir.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Galeri dışı bir uygulama için Federasyon çoklu oturum açmayı yapılandırma

Galeri olmayan bir uygulamayı yapılandırmak için Azure AD Premium 'a sahip olmanız ve uygulamanın SAML 2,0 ' yi desteklemesi gerekir. Azure AD sürümleri hakkında daha fazla bilgi için [Azure AD fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory/)ziyaret edin.

-   Azure AD 'de uygulamanın meta veri değerlerini yapılandırma (oturum açma URL 'SI, tanımlayıcı, yanıt URL 'SI)

-   [Kullanıcı tanımlayıcısı ' nı seçin ve uygulamaya gönderilecek kullanıcı özniteliklerini ekleyin](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Azure AD meta verilerini ve sertifikasını alma](#download-the-azure-ad-metadata-or-certificate)

-   Uygulamada Azure AD meta veri değerlerini yapılandırma (oturum açma URL 'SI, verenin, oturum kapatma URL 'SI ve sertifika)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Azure AD 'de uygulamanın meta veri değerlerini yapılandırma (oturum açma URL 'SI, tanımlayıcı, yanıt URL 'SI)

Azure AD galerisinde olmayan bir uygulama için çoklu oturum açmayı yapılandırmak için aşağıdaki adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. **Kurumsal uygulamalar** bölmesindeki sağ üst köşedeki **Ekle** düğmesine tıklayın.

6. **kendi uygulamanızı ekleme** bölümünde **Galeri dışı uygulama** ' ya tıklayın.

7. **Ad** metin kutusuna uygulamanın adını girin.

8. Uygulamayı eklemek için **Ekle** düğmesine tıklayın.

9. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.

10. **Mod** açılan menüsünde **SAML tabanlı oturum açma** seçin

11. **Etki alanı ve URL 'ler** için gerekli değerleri girin. Bu değerleri uygulama satıcısından almalısınız.

    1. Uygulamayı IDP tarafından başlatılan SSO olarak yapılandırmak için, yanıt URL 'sini ve tanımlayıcıyı girin.

    2. **Isteğe bağlı:** Uygulamayı SP tarafından başlatılan SSO olarak yapılandırmak için, oturum açma URL 'SI gerekli bir değerdir.

12. **Kullanıcı öznitelikleri**' nde, **Kullanıcı tanımlayıcısı** açılan menüsünde kullanıcılarınız için benzersiz tanımlayıcıyı seçin.

13. **Isteğe bağlı:** kullanıcılar oturum açtığında SAML belirtecinde uygulamaya gönderilecek öznitelikleri düzenlemek için **diğer tüm Kullanıcı özniteliklerini görüntüle ve Düzenle** ' ye tıklayın.

    Bir öznitelik eklemek için:

    1. **öznitelik Ekle**' ye tıklayın. **Adı** girin ve açılan listeden **değeri** seçin.

    2. Kaydet ' e tıklayın **.** Yeni özniteliği tabloda görürsünüz.

14. uygulamada çoklu oturum açmayı yapılandırmayla ilgili belgelere erişmek için **&gt;&lt;uygulama adını Yapılandır** ' a tıklayın. Ayrıca, Azure AD URL 'Lerine ve uygulama için gerekli sertifikaya sahip olursunuz.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Kullanıcı tanımlayıcısı ' nı seçin ve uygulamaya gönderilecek kullanıcı özniteliklerini ekleyin

Kullanıcı tanımlayıcısını seçmek veya Kullanıcı öznitelikleri eklemek için aşağıdaki adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada show istediğiniz uygulamayı göremiyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini** ayarlayıp **Göster** seçeneğini **tüm Uygulamalar.**

6. Çoklu oturum açma yapılandırdığınız uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.

8. **Kullanıcı öznitelikleri** bölümünde, **Kullanıcı tanımlayıcısı** açılan menüsünde kullanıcılarınız için benzersiz tanımlayıcıyı seçin. Seçili seçeneğin, kullanıcının kimliğini doğrulamak için uygulamadaki beklenen değerle eşleşmesi gerekir.

   >[!NOTE]
   >Azure AD, seçilen değere veya SAML AuthRequest 'te uygulama tarafından istenen biçime göre NameID özniteliği (Kullanıcı tanımlayıcısı) biçimini seçin. Daha fazla bilgi için, Nameıdpolicy bölümünde [Çoklu oturum açma SAML Protokolü](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) makalesini ziyaret edin.
   >
   >

9. Kullanıcı öznitelikleri eklemek için, kullanıcılar oturum açtığında SAML belirtecinde uygulamaya gönderilecek öznitelikleri düzenlemek için **diğer tüm Kullanıcı özniteliklerini görüntüle ve Düzenle** ' ye tıklayın.

   Bir öznitelik eklemek için:

   1. **öznitelik Ekle**'ye tıklayın. **Adı** girin ve açılan listeden **değeri** seçin.

   2 Kaydet 'e tıklayın **.** Yeni özniteliği tabloda görürsünüz.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Azure AD meta verilerini veya sertifikasını indirin

Azure AD 'den uygulama meta verilerini veya sertifikasını indirmek için aşağıdaki adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada show istediğiniz uygulamayı göremiyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini** ayarlayıp **Göster** seçeneğini **tüm Uygulamalar.**

6. Çoklu oturum açma yapılandırdığınız uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.

8. **SAML Imzalama sertifikası** bölümüne gidin ve ardından sütun değerini **İndir** ' e tıklayın. Uygulamanın çoklu oturum açmayı yapılandırma ihtiyacı olduğuna bağlı olarak, meta veri XML 'sini veya sertifikayı indirme seçeneğini görürsünüz.

   Azure AD meta verileri almak için bir URL sağlamıyor. Meta veriler yalnızca bir XML dosyası olarak alınabilir.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Azure AD Galeri uygulaması için parola çoklu oturum açmayı yapılandırma

Azure AD Galerisi 'nden bir uygulamayı yapılandırmak için şunları yapmanız gerekir:

-   Azure AD Galerisi 'nden uygulama ekleme

-   Uygulamayı parola çoklu oturum açma için yapılandırma

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Azure AD Galerisi 'nden uygulama ekleme

Azure AD Galerisi 'nden bir uygulama eklemek için aşağıdaki adımları izleyin:

1.  [Azure Portal](https://portal.azure.com) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4.  Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5.  **Kurumsal uygulamalar** bölmesindeki sağ üst köşedeki **Ekle** düğmesine tıklayın.

6.  **Galeriden Ekle** bölümündeki **bir adı girin** metin kutusuna uygulamanın adını yazın.

7.  Çoklu oturum açma için yapılandırmak istediğiniz uygulamayı seçin

8.  Uygulamayı eklemeden önce **ad metin kutusundan adını değiştirebilirsiniz** .

9.  Uygulamayı eklemek için **Ekle** düğmesine tıklayın.

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

9. Kullanıcılara uygulama atama.

10. Ayrıca, kullanıcıların adına Kullanıcı adına kimlik bilgileri de sağlayabilirsiniz ve **kimlik bilgilerini güncelleştir** ' i tıklatarak ve Kullanıcı adına Kullanıcı adını ve parolayı girebilirsiniz. Aksi takdirde, kullanıcılardan başlatma sırasında kimlik bilgilerini girmesi istenir.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Galeri dışı bir uygulama için parola çoklu oturum açmayı yapılandırma

Azure AD Galerisi 'nden bir uygulamayı yapılandırmak için şunları yapmanız gerekir:

-   [Galeri dışı bir uygulama ekleme](#add-a-non-gallery-application)

-   [Uygulamayı parola çoklu oturum açma için yapılandırma](#configure-the-application-for-password-single-sign-on)

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

6. Çoklu oturum açmayı yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.

8. Mod **parola tabanlı oturum açmayı seçin.**

9. **Oturum açma URL 'sini**girin. Bu, kullanıcıların oturum açmak için Kullanıcı adını ve parolasını girmesi gereken URL 'dir. Oturum açma alanlarının URL 'de görünür olduğundan emin olun.

10. Kullanıcılara uygulama atama.

11. Ayrıca, kullanıcıların adına Kullanıcı adına kimlik bilgileri de sağlayabilirsiniz ve **kimlik bilgilerini güncelleştir** ' i tıklatarak ve Kullanıcı adına Kullanıcı adını ve parolayı girebilirsiniz. Aksi takdirde, kullanıcılardan başlatma sırasında kimlik bilgilerini girmesi istenir.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Bir kullanıcıyı doğrudan uygulamaya atama

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

12. **İsteğe bağlı:** isteyip istemediğini **birden fazla kullanıcı eklemek**, başka bir tür **tam adı** veya **e-posta adresi** içine **adına göre arama veya e-posta adresi** arama kutusuna ve bu kullanıcıyı eklemek için onay kutusunu **seçili** listesi.

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

