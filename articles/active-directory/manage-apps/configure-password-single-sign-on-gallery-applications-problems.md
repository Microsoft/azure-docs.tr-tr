---
title: Azure AD Galeri uygulaması için parola çoklu oturum açmayı yapılandırma sorunları | Microsoft Docs
description: Azure AD uygulama galerisinde zaten listelenen uygulamalar için parola çoklu oturum açmayı yapılandırırken insanların karşılaştığı yaygın sorunları anlayın
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc75346b1093cc41a44edad1376c5f10dfec2409
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381133"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Azure AD Galeri uygulaması için parola çoklu oturum açmayı yapılandırma sorunu

Bu makale, Azure AD Galeri uygulamasıyla **parola çoklu oturum açmayı** yapılandırırken insanların karşılaştığı yaygın sorunları anlamanıza yardımcı olur.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Kimlik bilgileri doldurulmuştur, ancak uzantı onları gönderemiyor

Bu sorun genellikle uygulama satıcısı oturum açma sayfasını son zamanlarda bir alan eklemek, Kullanıcı adı ve parola alanlarını algılamak için kullanılan tanımlayıcıyı değiştirmek veya oturum açma deneyiminin uygulama için nasıl çalıştığını değiştirmek üzere değiştirdiyse meydana gelir. Neyse ki, birçok örnekte Microsoft bu sorunları hızlı bir şekilde çözmek için uygulama satıcılarıyla birlikte çalışabilir.

Microsoft, tümleştirmelerin ne zaman otomatik olarak algılanması için teknolojiler sağlarken, sorunları hemen bulmak mümkün olmayabilir veya sorunların düzeltilmesi biraz zaman alabilir. Bu tümleştirmelerin bir örneği doğru şekilde çalışmazsa, mümkün olduğunca çabuk düzeltibilmeleri için bir destek talebi açın.

**Bu uygulamanın satıcısıyla iletişim kursunsam, Microsoft 'un** kendi uygulamasını Azure Active Directory ile yerel olarak tümleştirmeleri için onlarla birlikte çalışmasına yol açabilir. Satıcıyı başlamak için [Azure Active Directory Uygulama galerisinde uygulamanızı listeye](../develop/howto-app-gallery-listing.md) gönderebilirsiniz.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Kimlik bilgileri doldurulmuştur ve gönderilir, ancak sayfa kimlik bilgilerinin yanlış olduğunu belirtiyor

Bu sorunu çözmek için öncelikle şunları deneyin:

- Kullanıcının **uygulama Web sitesinde, doğrudan** bunlar için depolanan kimlik bilgileriyle oturum açmaya çalışın.

  * Oturum açma çalışır durumda olduğunda, kullanıcının en son bilinen çalışma Kullanıcı adı ve parolasıyla güncelleştirilmesi için [uygulama erişimi panelinin](https://myapps.microsoft.com/) **uygulamalar** bölümünde yer alan **uygulama kutucuğunda** bulunan **kimlik bilgilerini güncelleştir** düğmesine tıklamasını sağlar.

  * Ya da bu kullanıcı için kimlik bilgilerini başka bir yönetici atadıysanız, uygulamanın **kullanıcılar & gruplar** sekmesine giderek kullanıcının veya grubun uygulama atamasını bulun, atamayı seçip **güncelleştirme kimlik bilgilerine tıklayın** düğmesine basın.

- Kullanıcı kendi kimlik bilgilerini atamışsa, kullanıcının, **parolasının uygulamanın süresi dolmadığından emin** olup olmadığını denetleyin ve bu durumda, uygulamada doğrudan oturum açarak, **süresi doldu parolasını güncelleştirin** .

  * Parola uygulamada güncelleştirildikten sonra, uygulamayı bilinen en son güncelleştirmeyle güncelleştirmek için [uygulama erişim paneli](https://myapps.microsoft.com/) 'nin **uygulamalar** bölümündeki **uygulama kutucuğunda** bulunan **kimlik bilgilerini güncelleştir** düğmesine tıklamasını isteyin. çalışma Kullanıcı adı ve parola.

  * Ya da bu kullanıcı için kimlik bilgilerini başka bir yönetici atadıysanız, uygulamanın **kullanıcılar & gruplar** sekmesine giderek kullanıcının veya grubun uygulama atamasını bulun, atamayı seçip **güncelleştirme kimlik bilgilerine tıklayın** düğmesine basın.

- Kullanıcı erişim paneli [tarayıcı uzantısını yüklemek](#how-to-install-the-access-panel-browser-extension) bölümünde aşağıdaki adımları izleyerek kullanıcının erişim paneli tarayıcı uzantısını güncelleştirmesini sağlayabilirsiniz.

- Erişim paneli tarayıcı uzantısının, kullanıcının tarayıcısında çalıştığından ve etkinleştirildiğinden emin olun.

- Kullanıcılarınızın erişim panelinden uygulamada oturum açmaya çalışmalarından **, InPrivate veya özel moddayken**emin olun. Bu modlarda erişim paneli uzantısı desteklenmez.

Önceki önerilerin çalışmamasına karşı, uygulamanın Azure AD ile tümleştirmesini geçici olarak bozan uygulama tarafında bir değişikliğin oluşması durumunda olabilir. Örneğin, bu durum, uygulama satıcısı kendi sayfasında bir betiği ortaya çıkardığı zaman gerçekleşebilir ve bu, kendi otomatik entegrasyonumuz gibi otomatik tümleştirmeye neden olur. Neyse ki, birçok örnekte Microsoft bu sorunları hızlı bir şekilde çözmek için uygulama satıcılarıyla birlikte çalışabilir.

Microsoft, uygulama tümleştirmeleri sırasında otomatik olarak algılamaya yönelik teknolojiler sağlarken, sorunları hemen bulmak mümkün olmayabilir veya sorunların düzeltilmesi biraz zaman alabilir. Bir tümleştirme düzgün çalışmazsa, mümkün olduğunca hızlı bir şekilde düzeltilmesi için bir destek talebi açabilirsiniz. 

Buna ek olarak, **Bu uygulamanın satıcısıyla iletişim**  kursunuz, uygulamaları Azure Active Directory ile yerel olarak tümleştirmek üzere bunlarla çalışabilmemiz için sizinle birlikte çalışmamız gerekir. Satıcıyı başlamak için [Azure Active Directory Uygulama galerisinde uygulamanızı listeye](../develop/howto-app-gallery-listing.md) gönderebilirsiniz.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>Uzantı Chrome ve Firefox 'ta çalışmaktadır, ancak Internet Explorer 'da kullanılamaz

Bu sorunun başlıca iki nedeni vardır:

- Internet Explorer 'da etkinleştirilen güvenlik ayarlarına bağlı olarak, Web sitesi **Güvenilen bir bölgenin**parçası değilse, bazen betiğimizin uygulama için yürütülmesi engellenir.

  *  Bu sorunu çözmek için, kullanıcının **Internet Explorer güvenlik ayarları**Içindeki **Güvenilen siteler** listesine **uygulamanın Web sitesini eklemesini** isteyin. Ayrıntılı yönergeler için kullanıcılarınızı, [Güvenilen siteler listesinden site ekleme](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) makalesine gönderebilirsiniz.

- Nadir koşullarda, Internet Explorer 'ın güvenlik doğrulaması bazen sayfanın betiğimizin yürütülmesinden daha yavaş yüklenmesine neden olabilir.

  * Ne yazık ki bu durum tarayıcı sürümüne, bilgisayar hızına veya ziyaret edilen siteye bağlı olarak farklılık gösterebilir. Bu durumda, bu belirli uygulama için tümleştirmeyi çözebilmemiz için destek ekibiyle iletişime geçin.

Buna ek olarak, **Bu uygulamanın satıcısıyla iletişim**  kursunuz, uygulamaları Azure Active Directory ile yerel olarak tümleştirmek üzere bunlarla çalışabilmemiz için sizinle birlikte çalışmamız gerekir. Satıcıyı başlamak için [Azure Active Directory Uygulama galerisinde uygulamanızı listeye](../develop/howto-app-gallery-listing.md) gönderebilirsiniz.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Uygulamanın oturum açma sayfasının yakın zamanda değişip değişmediğini ve ek bir alan gerektirip gerektirmediğini denetleyin

Uygulamanın oturum açma sayfası büyük ölçüde değiştirilmişse, bazı durumlarda tümleştirmelerimiz kesintiye neden olur. Bunun bir örneği, bir uygulama satıcısının deneyimler için bir oturum açma alanı, CAPTCHA veya Multi-Factor Authentication eklediğinde oluşur. Neyse ki, birçok örnekte Microsoft bu sorunları hızlı bir şekilde çözmek için uygulama satıcılarıyla birlikte çalışabilir.

Microsoft, uygulama tümleştirmeleri sırasında otomatik olarak algılamaya yönelik teknolojiler sağlarken, sorunları hemen bulmak mümkün olmayabilir veya sorunların düzeltilmesi biraz zaman alabilir. Bir tümleştirme düzgün çalışmazsa, mümkün olduğunca hızlı bir şekilde düzeltilmesi için bir destek talebi açabilirsiniz. 

Buna ek olarak, **Bu uygulamanın satıcısıyla iletişim**  kursunuz, uygulamaları Azure Active Directory ile yerel olarak tümleştirmek üzere bunlarla çalışabilmemiz için sizinle birlikte çalışmamız gerekir. Satıcıyı başlamak için [Azure Active Directory Uygulama galerisinde uygulamanızı listeye](../develop/howto-app-gallery-listing.md) gönderebilirsiniz.

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

## <a name="next-steps"></a>Sonraki adımlar
[Uygulama Ara sunucusu ile uygulamalarınıza çoklu oturum açma sağlayın](application-proxy-configure-single-sign-on-with-kcd.md)

