---
title: Yaygın iki öğeli doğrulama sorunlarını giderme-Azure AD
description: Daha yaygın iki öğeli doğrulama sorunlarından bazılarına yönelik olası çözümler hakkında bilgi edinin.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lizross
ms.reviewer: kexia
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8d226d61f4b67541a6991fef3a435b4cfff500f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805296"
---
# <a name="troubleshoot-common-two-factor-verification-problems"></a>Yaygın iki öğeli doğrulama sorunlarını giderme

Kuruluşunuz iki öğeli doğrulamayı açık bıraktı, yani iş veya okul hesabınızın oturum açması artık Kullanıcı adınızın, parolanızın ve mobil cihazın veya telefonunuzun bir birleşimini gerektiriyor. İki kimlik doğrulama biçimini (bildiğiniz bir şeyi ve sizinle bir şeyi) bağlı olarak yalnızca bir paroladan daha güvenli olduğundan, kuruluşunuz bu ek doğrulamaya açıktır. İki öğeli doğrulama kötü amaçlı korsanların sizin için hazır olmasını durdurmaya yardımcı olabilir, çünkü parolankenizin olsa da, gürültü, cihazınıza sahip olmalardır.

Bizden daha sık gerçekleşmemizi sağlayan bazı yaygın iki öğeli doğrulama sorunları vardır. En yaygın sorunları ve olası düzeltmeleri ele almak için bu makaleye bir araya koyuyoruz.

>[!Important]
>Bu içerik kullanıcılara yöneliktir. Yöneticiyseniz, Azure Active Directory (Azure AD) ortamınızı ayarlama ve yönetme hakkında daha fazla bilgi için [Azure Active Directory Belgelerine](https://docs.microsoft.com/azure/active-directory) bakabilirsiniz.
>
>Bu içerik, yalnızca kuruluşunuz tarafından sağlanmış olan hesap (örneğin, alain@contoso.com) için yalnızca iş veya okul hesabınızla birlikte kullanılmak üzere tasarlanmıştır. İki öğeli doğrulamayla ilgili sorun yaşıyorsanız ve kişisel Microsoft hesabı, kendiniz için ayarladığınız hesap (örneğin, danielle@outlook.com), [Microsoft hesabı için iki öğeli doğrulamayı açma veya kapatma](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)konusuna bakın.

## <a name="i-forgot-my-mobile-device-at-home"></a>Evde mobil aygıtımı unuttum

Meydana gelir. Mobil cihazınızı evde bıraktınız ve artık sizi söylediğinizi doğrulamak için telefonunuzu kullanamazsınız. Hesabınızda, ofis telefonunuz gibi daha önceden oturum açmak için başka bir yöntem eklediyseniz, bu yöntemi şimdi kullanabilmeniz gerekir. Daha önce ek bir doğrulama yöntemi eklemediyseniz, yardım masasına başvurmanız ve hesabınıza geri dönmek için size yardımcı olmanız gerekir.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Başka bir doğrulama yöntemi kullanarak iş veya okul hesabınızda oturum açmak için

1. Hesabınızda normal olarak oturum açın ve **iki öğeli doğrulama** sayfasında **başka bir şekilde oturum aç** bağlantısını seçin.

    ![Doğrulama yönteminde oturum açma değiştirme yöntemi](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >**Başka bir yolla oturum açma** bağlantısını görmüyorsanız, başka bir doğrulama yöntemi ayarlamadıysanız anlamına gelir. Hesabınızda oturum açarken yardım almak için yöneticinize başvurmanız gerekir.

2. Alternatif doğrulama yönteminizi seçin ve iki öğeli doğrulama işlemiyle devam edin.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Mobil cihazımı kaybettim veya Çalındım

Kaybolması veya mobil cihazınızın çalınması durumunda, farklı bir yöntem kullanarak oturum açabilir veya yardım masanıza ayarlarınızı temizleyebilir. Yardım masasına telefonunuzun kaybolması veya çalınması durumunda bu sayede hesabınıza uygun güncelleştirmelerin yapılabilmesi için size kesinlikle izin vermenizi öneririz. Ayarlarınız temizlendikten sonra, bir sonraki oturum açışınızda [iki öğeli doğrulama için kaydolmanız](multi-factor-authentication-end-user-first-time.md) istenir.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Mobil Cihazlarıma gönderilen doğrulama kodunu alamıyorum

Doğrulama kodunuzun olmaması yaygın bir sorundur ve genellikle mobil cihazınızın ve ayarlarının ayarlarıyla ilgilidir. Deneyebileceğiniz bazı olası şeyler:

- **Mobil cihazınızı yeniden başlatın.** Bazen cihazınızda bir yenileme yeterlidir. Cihazınızın yeniden başlatılması, çalışmakta olan herhangi bir arka plan işlemini veya hizmeti sonlandırır ve sorun oluşmasına ve cihazınızın çekirdek bileşenlerini yenilemeye ve bu durumda, bir noktada kilitlendiklerinde yeniden başlatılmasına neden olabilir.

- **Güvenlik bilgilerinizin doğru olduğundan emin olun.** Güvenlik doğrulama yöntemi bilgilerinizin doğru, özellikle telefon numaralarınızı doğru olduğundan emin olun. Yanlış telefon numarasına yerleştirirseniz, Tüm uyarılarınız bu hatalı sayıya gider. Neyse ki, bu kullanıcı uyarılarla hiçbir şey yapamayacaktır, ancak hesabınızda oturum açmanıza de yardımcı olmaz. Bilgilerinizin doğru olduğundan emin olmak için, [iki öğeli doğrulama yöntemi ayarlarını yönetme](multi-factor-authentication-end-user-manage-settings.md) makalesindeki yönergelere bakın.

- **Bildirimlerin açık olduğunu doğrulayın.** Mobil cihazınızın bildirimleri açık olduğundan ve telefon çağrılarına, kimlik doğrulama uygulamanıza ve mesajlaşma uygulamanıza (metin iletileri için) mobil cihazınıza görünür uyarı bildirimleri göndermesini sağlayan bir bildirim yöntemi seçtiğinizden emin olun.

- **Bir cihaz sinyaliniz ve Internet bağlantınız olduğundan emin olun.** Telefon aramalarınızın ve SMS iletilerinizin mobil cihazınıza geldiğinden emin olun. Sizi bir arkadaşınız araması yapın ve her ikisini de aldığınızdan emin olmak için size bir kısa mesaj gönderin. Bunu yapmazsanız, önce mobil cihazınızın açık olduğundan emin olun. Cihazınız açıksa, ancak aramayı veya metni alamıyorsanız, büyük olasılıkla ağınızla ilgili bir sorun olabilir ve sağlayıcınızla konuşmanız gerekir. Genellikle sinyalle ilgili sorunlarınız varsa, mobil cihazınızda [Microsoft Authenticator uygulamasını](user-help-auth-app-download-install.md) yüklemenizi ve kullanmanızı öneririz. Doğrulayıcı uygulaması, herhangi bir hücre sinyali veya Internet bağlantısı gerekmeden oturum açma için rastgele güvenlik kodları oluşturabilir.

- **Rahatsız değil ' i kapatın.** Mobil cihazınız için **rahatsız değil** özelliğini açtığınızdan emin olun. Bu özellik açık olduğunda, bildirimlerin mobil cihazınızda sizi uyarmasını izin verilmez. Bu özelliğin nasıl kapatılacağı hakkında yönergeler için mobil cihazınızın kılavuzuna bakın.

- **Telefon numaralarının engellemesini kaldır** Birleşik Devletler, Microsoft 'tan gelen sesli çağrılar şu numaralardan gelir: + 1 (866) 539 4191, + 1 (855) 330 8653 ve + 1 (877) 668 6536.

- **Pilyle ilgili ayarlarınızı denetleyin.** Bu, yüzeyde tek bir bit gibi görünür, ancak daha az kullanılan uygulamaların arka planda kalan etkin durumdan durması için pil iyileştirmesini ayarladıysanız, bildirim sisteminizin büyük olasılıkla etkilendi. Bu sorunu gidermeyi denemek için, kimlik doğrulama uygulamanız ve mesajlaşma uygulamanız için pil iyileştirmesi ' nı kapatın ve sonra hesabınızda yeniden oturum açmayı deneyin.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>İkinci doğrulama bilgilerim hakkında sorulmıyorum

Kullanıcı adınızı ve parolanızı kullanarak iş veya okul hesabınızda oturum açtıysanız, ancak ek güvenlik doğrulama bilgileriniz hakkında uyarılmadıysanız, cihazınızın henüz ayarlanmamasından kaynaklanabilir. Mobil cihazınızın özel olarak ek güvenlik doğrulama yöntemiyle çalışacak şekilde ayarlanması gerekir. Mobil cihazınızı açtığınızdan ve doğrulama yöntemiyle birlikte kullanılmak üzere kullanılabildiğinden emin olmak için, [iki öğeli doğrulama yöntemi ayarlarını yönetme](multi-factor-authentication-end-user-manage-settings.md) makalesine bakın. Cihazınızı veya hesabınızı ayarlamadığınız biliyorsanız, bunu şu anda [iki adımlı doğrulama için hesabımı ayarlama](multi-factor-authentication-end-user-first-time.md) makalesindeki adımları izleyerek yapabilirsiniz.

## <a name="i-got-a-new-phone-number-how-do-i-change-it-for-two-factor-verification"></a>Yeni bir telefon numarası aldım, iki öğeli doğrulama için nasıl değiştirebilirim?

Yeni bir telefon numarası aldıysanız, doğrulama istemlerinizin doğru konuma gitmesi için güvenlik doğrulama yöntemi ayrıntılarınızı güncelleştirmeniz gerekir. Doğrulama yönteminizi güncelleştirmek için, [iki öğeli doğrulama yöntemi ayarlarını yönetme](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) makalesindeki **telefon numaranızı ekleme veya değiştirme** bölümündeki adımları izleyin.

## <a name="i-got-a-new-mobile-device-how-do-i-add-it"></a>Yeni bir mobil cihaz aldım, nasıl ekleyebilirim?

Yeni bir mobil cihaz aldıysanız, bunu iki öğeli doğrulamayla çalışacak şekilde ayarlamanız gerekir. Bu, çok adımlı bir çözümdür:

1. Hesabınızı iş veya okul hesabınızla çalışacak şekilde ayarlayarak, [iki adımlı doğrulama için hesabımı ayarlama](multi-factor-authentication-end-user-first-time.md) makalesindeki adımları uygulayın.

2. Hesap ve cihaz bilgilerinizi **ek güvenlik doğrulama** sayfasında güncelleştirin, eski cihazınızı silin ve yeni bir tane ekleyin. Daha fazla bilgi için, [iki öğeli doğrulama yöntemi ayarlarını yönetme](multi-factor-authentication-end-user-manage-settings.md) makalesine bakın.

3. İsteğe bağlı. [Microsoft Authenticator uygulamasını indirme ve yükleme](user-help-auth-app-download-install.md) makalesindeki adımları izleyerek mobil cihazınıza Microsoft Authenticator uygulamasını indirin, yükleyin ve ayarlayın.

4. İsteğe bağlı. İki öğeli doğrulama [yöntemi ayarlarını yönetme](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) konusunun **Güvenilen bir cihazda iki öğeli doğrulama istemlerini aç** bölümünde yer alan adımları izleyerek, güvenilen cihazlarınız için iki öğeli doğrulamayı etkinleştirin.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Mobil cihazımın yolculukta oturum açarken sorun yaşıyorum

Uluslararası bir konumdayken, kısa mesaj gibi mobil cihazla ilgili doğrulama yöntemini kullanmayı daha zor bulabilirsiniz. Mobil cihazınız, dolaşım ücretlerine tabi hale gelmesine neden olabilir. Bu durum için, Wi-Fi etkin noktaya bağlanma seçeneğiyle Microsoft Authenticator uygulamasını kullanmanızı öneririz. Mobil cihazınızda Microsoft Authenticator uygulamasının nasıl indirileceği, yükleneceği ve ayarlandığı hakkında daha fazla bilgi için, [Microsoft Authenticator uygulamasını indirme ve yükleme](user-help-auth-app-download-install.md) makalesine bakın.

## <a name="i-cant-get-my-app-passwords-to-work"></a>Uygulama parolalarımı çalışacak şekilde alamıyoruz

Uygulama parolaları, iki öğeli doğrulamayı desteklemeyen eski masaüstü uygulamaları için normal parolanızı değiştirir. İlk olarak, parolayı doğru yazdığınızdan emin olun. Bu uygulamayı gidermezse, [iki adımlı doğrulama için uygulama parolalarını yönetme](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) konusunun **uygulamalarımı kullanarak uygulama parolaları oluşturma ve silme** bölümündeki adımları izleyerek uygulama için yeni bir uygulama parolası oluşturmayı deneyin.

## <a name="why-cant-i-turn-two-factor-verification-off"></a>İki öğeli doğrulamayı neden kapatamıyorum?

İş veya okul hesabınızla iki öğeli doğrulama kullanıyorsanız (örneğin, alain@contoso.com), büyük olasılıkla kuruluşunuzun bu ek güvenlik özelliğini kullanmanız gerektiğine karar vermiş olması anlamına gelir. Kuruluşunuz bu özelliği kullanmanız gerektiğine karar verdiği için, tek yapmanız gereken bir yöntem yoktur. Bununla birlikte, alain@outlook.comgibi bir kişisel hesapla iki öğeli doğrulama kullanıyorsanız, özelliği açıp kapatabilirsiniz. Kişisel Microsoft hesaplarınız için iki öğeli doğrulamanın nasıl denetılacağı hakkında yönergeler için, [Microsoft hesabı için iki öğeli doğrulamayı açma veya kapatma](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)konusuna bakın.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Sorunum için bir yanıt bulamadık

Bu adımları denediyseniz ancak hala sorun yaşıyorsanız yardım için yardım masasına başvurun.

## <a name="related-articles"></a>İlgili makaleler:

- [İki öğeli doğrulama yöntemi ayarlarınızı yönetin](multi-factor-authentication-end-user-manage-settings.md)

- [İki adımlı doğrulama için hesabımı ayarlama](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator uygulaması hakkında SSS](user-help-auth-app-faq.md)
