---
title: SAML tabanlı çoklu oturum açma Azure Active Directory hata ayıkla
description: Azure Active Directory içindeki uygulamalarda SAML tabanlı çoklu oturum açma hatalarını ayıklayın.
services: active-directory
ms.author: iangithinji
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: troubleshooting
ms.workload: identity
ms.date: 02/18/2019
ms.reviewer: luleon, hirsin, paulgarn
ms.openlocfilehash: aa86bbcec0dc6523ae701e5237f2c55d14db38e4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374327"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Azure Active Directory'de SAML tabanlı çoklu oturum açma uygulamaları için hata ayıklama

SAML tabanlı çoklu oturum açma kullanan Azure Active Directory (Azure AD) uygulamaları için [Çoklu oturum açma](what-is-single-sign-on.md) sorunlarını bulmayı ve gidermeyi öğrenin. 

## <a name="before-you-begin"></a>Başlamadan önce

[Uygulamalarımın güvenli oturum açma uzantısını](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension)yüklemenizi öneririz. Bu tarayıcı uzantısı, çoklu oturum açma ile ilgili sorunları çözmek için ihtiyaç duyduğunuz SAML isteği ve SAML yanıt bilgilerinin toplanmasını kolaylaştırır. Uzantıyı yükleyemezseniz, bu makalede, hem ile hem de uzantı yüklü olmadan sorunların nasıl çözümleneceği gösterilmektedir.

Uygulamalarım güvenli oturum açma uzantısını indirip yüklemek için aşağıdaki bağlantılardan birini kullanın.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>SAML tabanlı çoklu oturum açmayı sına

Azure AD ile hedef uygulama arasında SAML tabanlı çoklu oturum açmayı test etmek için:

1. [Azure Portal](https://portal.azure.com) bir genel yönetici veya uygulamaları yönetme yetkisine sahip başka bir yönetici olarak oturum açın.
1. Sol dikey pencerede **Azure Active Directory**' yi seçin ve ardından **Kurumsal uygulamalar**' ı seçin. 
1. Kurumsal uygulamalar listesinden, çoklu oturum açmayı test etmek istediğiniz uygulamayı seçin ve ardından sol taraftaki seçeneklerden **Çoklu oturum açma** seçeneğini belirleyin.
1. SAML tabanlı çoklu oturum açma testi deneyimini açmak için, **Çoklu oturum açma** (5. adım) sayfasına gidin. **Test** düğmesi gri Ise, **temel SAML yapılandırması** bölümünde önce gerekli öznitelikleri doldurmanız ve kaydetmeniz gerekir.
1. **Çoklu oturum açma** dikey penceresinde, hedef uygulamada oturum açmak için şirket kimlik bilgilerinizi kullanın. Geçerli Kullanıcı olarak veya farklı bir kullanıcı olarak oturum açabilirsiniz. Farklı bir kullanıcı olarak oturum açarsanız, bir istem sizden kimlik doğrulaması isteyeceğiz.

    ![Test SAML SSO sayfasını gösteren ekran görüntüsü](./media/debug-saml-sso-issues/test-single-sign-on.png)

Oturumunuz başarıyla açıldı, test geçildi. Bu durumda, Azure AD uygulamaya bir SAML yanıt belirteci verdi. Uygulama, başarıyla oturumunuzu açmak için SAML belirtecini kullandı.

Şirket oturum açma sayfasında veya uygulamanın sayfasında bir hata varsa, hatayı çözmek için sonraki bölümlerden birini kullanın.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Şirket oturum açma sayfanızda oturum açma hatasını çözün

Oturum açmaya çalıştığınızda, aşağıdaki örneğe benzer şekilde şirket oturum açma sayfasında bir hata görebilirsiniz.

![Şirket oturum açma sayfasında bir hata gösteren örnek](./media/debug-saml-sso-issues/error.png)

Bu hatayı ayıklamak için hata iletisi ve SAML isteği gerekir. Uygulamalarım güvenli oturum açma uzantısı bu bilgileri otomatik olarak toplar ve Azure AD 'de çözüm kılavuzunu görüntüler.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Uygulamalarım güvenli oturum açma uzantısı yüklü olan oturum açma hatasını çözümlemek için

1. Bir hata oluştuğunda, uzantı sizi Azure AD **testi çoklu oturum açma** dikey penceresine geri yönlendirir.
1. **Çoklu oturum açma sınaması** dikey PENCERESINDE, **SAML isteğini indir**' i seçin.
1. Hataya ve SAML isteğindeki değerlere göre belirli çözüm kılavuzlarını görmeniz gerekir.
1. Sorunu çözmek için Azure AD 'de yapılandırmayı otomatik olarak güncellemek üzere bir sorun **giderme** düğmesini görürsünüz. Bu düğmeyi görmüyorsanız, oturum açma sorunu Azure AD 'de yanlış yapılandırma nedeniyle değildir.

Oturum açma hatası için hiçbir çözüm sağlanmazsa, bize bildirmek için geri bildirim metin kutusunu kullanmanızı öneririz.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Uygulamamın güvenli oturum açma uzantısını yüklemeden hatayı çözümlemek için

1. Sayfanın sağ alt köşesindeki hata mesajını kopyalayın. Hata iletisi şunları içerir:
    - Bir CorrelationId ve zaman damgası. Bu değerler, mühendislerin sorununuzu belirlemesine ve sorununuzla ilgili doğru bir çözüm sağlamasına yardımcı olduklarından, Microsoft ile bir destek durumu oluşturduğunuzda önemlidir.
    - Sorunun kök nedenini tanımlayan bir ifade.
1. Azure AD 'ye dönün ve **Çoklu oturum açma** dikey penceresini bulun.
1. Yukarıdaki metin kutusunda, **çözüm kılavuzunu al**' ı, hata iletisini yapıştırın.
1. Sorunu çözmeye yönelik adımları göstermek için **çözüm kılavuzunu al** ' a tıklayın. Bu kılavuzda, SAML isteğinden veya SAML yanıtından bilgi gerekebilir. Uygulamalarım güvenli oturum açma uzantısını kullanmıyorsanız, SAML isteği ve yanıtını almak için [Fiddler](https://www.telerik.com/fiddler) gibi bir araca ihtiyacınız vardır.
1. SAML isteğindeki hedefin Azure AD 'den alınan SAML tek Sign-On hizmet URL 'sine karşılık geldiğini doğrulayın.
1. SAML isteğindeki veren 'in, Azure AD 'de uygulama için yapılandırdığınız tanımlayıcıyla aynı olduğunu doğrulayın. Azure AD, dizininizdeki bir uygulamayı bulmak için veren 'i kullanır.
1. AssertionConsumerServiceURL 'nin, uygulamanın Azure AD 'den SAML belirtecini almak için beklediği konum olduğunu doğrulayın. Bu değeri Azure AD 'de yapılandırabilirsiniz, ancak SAML isteğinin bir parçası ise zorunlu değildir.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Uygulama sayfasında oturum açma hatasını çözme

Başarılı bir şekilde oturum açabilir ve sonra uygulamanın sayfasında bir hata görebilirsiniz. Bu durum, Azure AD uygulamaya bir belirteç verildiğinde, ancak uygulama yanıtı kabul etmediğinde oluşur.

Hatayı gidermek için aşağıdaki adımları izleyin veya [Azure AD 'yi kullanarak SAML SSO sorunlarını gidermek için bu kısa videoyu](https://www.youtube.com/watch?v=poQCJK0WPUk&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0&index=8)izleyin:

1. Uygulama Azure AD galerisinde ise, uygulamayı Azure AD ile tümleştirmek için tüm adımları izlediğinizden emin olun. Uygulamanıza ilişkin tümleştirme yönergelerini bulmak için, [SaaS uygulaması tümleştirme öğreticilerinin listesine](../saas-apps/tutorial-list.md)bakın.
1. SAML yanıtını alın.
    - Uygulamalarım güvenli oturum açma uzantısı yüklüyse, **Çoklu oturum açma** DIKEY penceresinde **SAML yanıtını indir**' e tıklayın.
    - Uzantı yüklü değilse, SAML yanıtını almak için [Fiddler](https://www.telerik.com/fiddler) gibi bir araç kullanın.
1. SAML yanıt belirtecindeki şu öğelere dikkat edin:
   - NameID değeri ve biçiminin Kullanıcı benzersiz tanımlayıcısı
   - Belirteçte verilen talepler
   - Belirteci imzalamak için kullanılan sertifika.

     SAML yanıtı hakkında daha fazla bilgi için bkz. [Çoklu oturum açma SAML Protokolü](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

1. SAML yanıtını inceledikten sonra, sorunu çözme konusunda rehberlik için [oturum açtıktan sonra uygulamanın sayfasındaki hata](application-sign-in-problem-application-error.md) başlığına bakın. 
1. Hala başarıyla oturum açmadıysanız, SAML yanıtında uygulama satıcısına kayıp olup olmadığını sorabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Artık çoklu oturum açma, uygulamanız için çalışmaya başladığına göre, [SaaS uygulamalarına Kullanıcı hazırlama ve sağlamayı kaldırma Işlemini otomatikleştirebilir](../app-provisioning/user-provisioning.md) veya [koşullu erişime](../conditional-access/app-based-conditional-access.md)başlamanızı sağlayabilirsiniz.
