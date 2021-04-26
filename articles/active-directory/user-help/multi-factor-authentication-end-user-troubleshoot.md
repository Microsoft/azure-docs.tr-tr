---
title: Hesap iki öğeli kimlik doğrulama ile ilgili yaygın sorunlar-Azure AD
description: Daha yaygın iki öğeli doğrulama sorunu ve iş veya okul hesabınız için çözümler.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/01/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: contperf-fy21q1
ms.openlocfilehash: 0cb5e3463eda5d77bbbdefe223954105fd125c71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98033991"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>İki öğeli doğrulama ve iş veya okul hesabınızla ilgili yaygın sorunlar

Bizden daha sık gerçekleşmemizi sağlayan bazı yaygın iki öğeli doğrulama sorunları vardır. En yaygın sorunlara yönelik düzeltmeleri anlatan bu makaleye bir araya koyuyoruz.

Azure Active Directory (Azure AD) kuruluşunuz, hesabınız için iki öğeli doğrulamayı açabilir. İki öğeli doğrulama açık olduğunda, hesap oturum açma bilgileriniz aşağıdaki verilerin birleşimini gerektirir:

- Kullanıcı adınız
- Parolanız
- Bir mobil cihaz veya telefon

İki öğeli doğrulama yalnızca bir paroladan daha güvenlidir, çünkü iki öğeli doğrulama, _sahip_ olduğunuz bir şeyi _bildiğiniz_ bir şey gerektirir. Fiziksel telefonunuza sahip bir korsan yok.

>[!Important]
>Yöneticimce sahipseniz Azure AD [belgelerinde](../index.yml)Azure AD ortamınızı ayarlama ve yönetme hakkında daha fazla bilgi edinebilirsiniz.

Bu içerik, kuruluşunuz tarafından size sağlanan hesap (örneğin,) için iş veya okul hesabınızda size yardımcı olabilir dritan@contoso.com . Kendiniz için ayarladığınız bir hesap olan kişisel Microsoft hesabı iki öğeli doğrulamayla ilgili sorun yaşıyorsanız (örneğin, danielle@outlook.com ), [Microsoft hesabı için iki öğeli doğrulamayı açma veya kapatma](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)konusuna bakın.

## <a name="i-dont-have-my-mobile-device-with-me"></a>Mobil cihazım benim benimle yok

Meydana gelir. Mobil cihazınızı evde bıraktınız ve şimdi nerede olduğunuzu doğrulamak için telefonunuzu kullanamazsınız. Belki de hesabınızda oturum açmak için, ofis telefonunuz gibi alternatif bir yöntem eklemiş olabilirsiniz. Bu durumda, bu alternatif yöntemi şimdi kullanabilirsiniz. Bir alternatif doğrulama yöntemi eklemediyseniz, yardım almak için kuruluşunuzun yardım masasına başvurabilirsiniz.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Başka bir doğrulama yöntemi kullanarak iş veya okul hesabınızda oturum açmak için

1. Hesabınızda oturum açın, ancak **iki öğeli doğrulama** sayfasında **başka bir şekilde oturum aç** bağlantısını seçin.

    ![Doğrulama yönteminde oturum açma değiştirme yöntemi](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    **Başka bir yolla oturum açma** bağlantısını görmüyorsanız, başka bir doğrulama yöntemi ayarlamadıysanız anlamına gelir. Hesabınızda oturum açarken yardım almak için yöneticinize başvurmanız gerekir.

2. Alternatif doğrulama yönteminizi seçin ve iki öğeli doğrulama işlemiyle devam edin.

## <a name="i-cant-turn-two-factor-verification-off"></a>İki öğeli doğrulamayı kapatma

- Bir Microsoft hizmeti için kişisel hesapla iki öğeli doğrulama kullanıyorsanız, bu alain@outlook.com [özelliği açabilir ve kapatabilirsiniz](https://account.live.com/proofs/Manage).

- İş veya okul hesabınızla iki öğeli doğrulama kullanıyorsanız, büyük olasılıkla kuruluşunuzun bu ek güvenlik özelliğini kullanmanız gerektiğine karar vermiş olması anlamına gelir. Tek yapmanız mümkün değildir.

İki öğeli doğrulamayı kapatamaz, Ayrıca kuruluş düzeyinde uygulanmış olan güvenlik varsayılanlarının de olabilir. Güvenlik Varsayılanları hakkında daha fazla bilgi için bkz. [güvenlik Varsayılanları nelerdir?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="my-device-was-lost-or-stolen"></a>Cihazım kayboldu veya çalındı

Kaybolması veya mobil cihazınızın çalınması durumunda aşağıdaki eylemlerden birini gerçekleştirebilirsiniz:

- Farklı bir yöntem kullanarak oturum açın.
- Kuruluşunuzun yardım masasına ayarlarınızı temizlemesini isteyin.

Telefonunuzun kaybolması veya çalınması durumunda kuruluşunuzun yardım masasına izin vermenizi önemle öneririz. Yardım masası, hesabınıza uygun güncelleştirmeleri yapabilir. Ayarlarınız temizlendikten sonra, bir sonraki oturum açışınızda [iki öğeli doğrulama için kaydolmanız](multi-factor-authentication-end-user-first-time.md) istenir.

## <a name="im-not-receiving-the-verification-code-sent-to-my-mobile-device"></a>Mobil cihazma gönderilen doğrulama kodunu almadım

Doğrulama kodunuzun alınmıyor yaygın bir sorundur. Sorun genellikle mobil cihazlarınız ve ayarları ile ilgilidir. Deneyebileceğiniz bazı eylemler aşağıda verilmiştir.

Bunu deneyin | Kılavuz bilgileri
--------- | ------------
Microsoft Authenticator uygulamasını veya doğrulama kodlarını kullanma | Oturum açma sırasında "doğrulama çağrılarında limitimize ulaştınız" veya "metin Doğrulama kodlarıyla sınırımızda karşılaştık" hata iletileriyle karşılaşıyoruz. <br/><br/>Microsoft, aynı kullanıcı tarafından kısa bir süre içinde yapılan yinelenen kimlik doğrulama girişimlerini sınırlayabilir. Bu sınırlama Microsoft Authenticator veya doğrulama kodu için geçerlidir. Bu sınırlara ulaşırsanız, doğrulayıcı uygulamasını, doğrulama kodunu kullanabilir veya birkaç dakika içinde yeniden oturum açmayı deneyebilirsiniz. <br/><br/> Oturum açma sırasında "Üzgünüz, hesabınızı doğrularken sorun yaşıyoruz" hata iletisini alıyorsunuz. <br/><br/> Microsoft, yüksek sayıda başarısız sesli veya SMS kimlik doğrulama girişimi nedeniyle aynı kullanıcı, telefon numarası veya kuruluş tarafından gerçekleştirilen sesli veya SMS kimlik doğrulama girişimlerini sınırlayabilir veya engelleyebilir. Bu hatayla karşılaşıyorsanız, kimlik doğrulayıcı uygulaması veya doğrulama kodu gibi başka bir yöntemi deneyebilir veya destek için yöneticinize erişebilirsiniz.
Mobil cihazınızı yeniden başlatın | Bazen cihazınızda bir yenileme yeterlidir. Cihazınızı yeniden başlattığınızda, tüm arka plan işlem ve Hizmetleri sonlandırılır. Yeniden başlatma, cihazınızın temel bileşenlerini de kapatır. Cihazınızı yeniden başlattığınızda herhangi bir hizmet veya bileşen yenilenir.
Güvenlik bilgilerinizin doğru olduğundan emin olun | Güvenlik doğrulama yöntemi bilgilerinizin doğru, özellikle telefon numaralarınızı doğru olduğundan emin olun. Yanlış telefon numarasına yerleştirirseniz, Tüm uyarılarınız bu hatalı sayıya gider. Neyse ki, bu kullanıcı uyarılarla hiçbir şey yapamayacaktır, ancak hesabınızda oturum açmanıza de yardımcı olmaz. Bilgilerinizin doğru olduğundan emin olmak için, [iki öğeli doğrulama yöntemi ayarlarını yönetme](multi-factor-authentication-end-user-manage-settings.md) makalesindeki yönergelere bakın.
Bildirimlerin açık olduğunu doğrulayın | Mobil cihazınızın bildirimleri açık olduğundan emin olun. Aşağıdaki bildirim modlarına izin verildiğinden emin olun: <br/><br/> &bull; Telefon görüşmeleri <br/> &bull; Kimlik doğrulama uygulamanız <br/> &bull; SMS mesajlaşma uygulamanız <br/><br/> Bu modların cihazınızda _görünür_ bir uyarı oluşturtığınızdan emin olun.
Bir cihaz sinyaliniz ve Internet bağlantınız olduğundan emin olun | Telefon aramalarınızın ve SMS iletilerinizin mobil cihazınıza geldiğinden emin olun. Sizi bir arkadaşınız araması yapın ve her ikisini de aldığınızdan emin olmak için size bir kısa mesaj gönderin. Çağrıyı veya metni almazsanız, önce mobil cihazınızın açık olduğundan emin olun. Cihazınız açıksa, ancak yine de aramayı veya metni almadıysanız, ağınızla ilgili bir sorun olabilir. Sağlayıcınızla konuşmanız gerekir. Genellikle sinyalle ilgili sorunlarınız varsa, mobil cihazınızda [Microsoft Authenticator uygulamasını](user-help-auth-app-download-install.md) yüklemenizi ve kullanmanızı öneririz. Doğrulayıcı uygulaması, herhangi bir hücre sinyali veya Internet bağlantısı gerekmeden oturum açma için rastgele güvenlik kodları oluşturabilir.
Rahatsız değil ' i Kapat | Mobil cihazınız için **rahatsız değil** özelliğini açtığınızdan emin olun. Bu özellik açık olduğunda, bildirimlerin mobil cihazınızda sizi uyarmasını izin verilmez. Bu özelliğin nasıl kapatılacağı hakkında yönergeler için mobil cihazınızın kılavuzuna bakın.
Telefon numaralarının engellemesini kaldır | Birleşik Devletler, Microsoft 'tan gelen sesli çağrılar şu numaralardan gelir: + 1 (866) 539 4191, + 1 (855) 330 8653 ve + 1 (877) 668 6536.
Pilyle ilgili ayarları denetleyin | Pil iyileştirmenizi, arka planda daha az sıklıkta kullanılan uygulamaları durdurmak üzere ayarlarsanız, bildirim sisteminiz büyük olasılıkla etkilendi. Hem kimlik doğrulama uygulamanız hem de mesajlaşma uygulamanız için pil iyileştirmesini kapatmayı deneyin. Sonra hesabınızda yeniden oturum açmayı deneyin.
Üçüncü taraf güvenlik uygulamalarını devre dışı bırak | Bazı Phone güvenlik uygulamaları, bilinmeyen çağıranlara ait metin iletilerini ve telefon çağrılarını engeller. Bir güvenlik uygulaması, telefonun doğrulama kodunu almasını önleyebilir. Telefonunuzdaki tüm üçüncü taraf güvenlik uygulamalarını devre dışı bırakmayı deneyin ve daha sonra başka bir doğrulama kodu gönderilmesini isteyin.

## <a name="im-not-being-prompted-for-my-second-verification-information"></a>İkinci doğrulama bilgileriniz istenmiyor

Kullanıcı adınızı ve parolanızı kullanarak iş veya okul hesabınızda oturum açın. Daha sonra ek güvenlik doğrulama bilgileriniz istenir. İstenirse, belki henüz cihazınızı ayarlamadınız. Mobil cihazınızın, özel ek güvenlik doğrulama yöntemiyle çalışacak şekilde ayarlanması gerekir.

Cihazınız henüz ayarlanmamış olabilir. Mobil cihazınızın, özel ek güvenlik doğrulama yöntemiyle çalışacak şekilde ayarlanması gerekebilir. Mobil cihazınızın doğrulama yöntemiyle kullanılabilmesini sağlama adımları için, bkz. [iki öğeli doğrulama yöntemi ayarlarını yönetme](multi-factor-authentication-end-user-manage-settings.md). Cihazınızı veya hesabınızı henüz kurmadığınızdan emin değilseniz, [iki adımlı doğrulama için hesabımı ayarlama](multi-factor-authentication-end-user-first-time.md) makalesindeki adımları izleyebilirsiniz.

## <a name="i-have-a-new-phone-number-and-i-want-to-add-it"></a>Yeni bir telefon numaranız var ve bunu eklemek istiyorum

Yeni bir telefon numaranız varsa güvenlik doğrulama yöntemi ayrıntılarınızı güncelleştirmeniz gerekir. Bu, doğrulama istemlerinizin doğru konuma gitmesini sağlar. Doğrulama yönteminizi güncelleştirmek için, [iki öğeli doğrulama yöntemi ayarlarını yönetme](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) makalesindeki **telefon numaranızı ekleme veya değiştirme** bölümündeki adımları izleyin.

## <a name="i-have-a-new-mobile-device-and-i-want-to-add-it"></a>Yeni bir mobil cihazım var ve bunu eklemek istiyorum

Yeni bir mobil cihazınız varsa, bunu iki öğeli doğrulamayla çalışacak şekilde ayarlamanız gerekir. Bu, çok adımlı bir çözümdür:

1. [İki adımlı doğrulama için hesabımı ayarlama](multi-factor-authentication-end-user-first-time.md) makalesindeki adımları izleyerek cihazınızı hesabınızla çalışacak şekilde ayarlayın.

1. Hesap ve cihaz bilgilerinizi **ek güvenlik doğrulama** sayfasında güncelleştirin. Eski cihazınızı silerek ve yeni bir cihaz ekleyerek güncelleştirmeyi gerçekleştirin. Daha fazla bilgi için, [iki öğeli doğrulama yöntemi ayarlarını yönetme](multi-factor-authentication-end-user-manage-settings.md) makalesine bakın.

İsteğe bağlı adımlar:

- [Microsoft Authenticator uygulamasını indirme ve yükleme](user-help-auth-app-download-install.md) makalesindeki adımları izleyerek mobil cihazınıza Microsoft Authenticator uygulamasını yükleyin.

- İki öğeli doğrulama [yöntemi ayarlarını yönetme](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) konusunun **Güvenilen bir cihazda iki öğeli doğrulama istemlerini aç** bölümünde yer alan adımları izleyerek, güvenilen cihazlarınız için iki öğeli doğrulamayı etkinleştirin.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Mobil cihazımın yolculukta oturum açarken sorun yaşıyorum

Uluslararası bir konumdayken, kısa mesaj gibi mobil cihazla ilgili doğrulama yöntemini kullanmayı daha zor bulabilirsiniz. Mobil cihazınız, dolaşım ücretlerine tabi hale gelmesine neden olabilir. Bu durum için, Wi-Fi etkin bir noktaya bağlanma seçeneğiyle Microsoft Authenticator uygulamasını kullanmanızı öneririz. Mobil cihazınızda Microsoft Authenticator uygulamasının nasıl ayarlanacağı hakkında daha fazla bilgi için, [Microsoft Authenticator uygulamasını indirme ve yükleme](user-help-auth-app-download-install.md) makalesine bakın.

## <a name="i-cant-get-my-app-passwords-to-work"></a>Uygulama parolalarımı çalışacak şekilde alamıyoruz

Uygulama parolaları, iki öğeli doğrulamayı desteklemeyen eski masaüstü uygulamaları için normal parolanızı değiştirir. İlk olarak, parolayı doğru yazdığınızdan emin olun. Bu dosyayı düzelmezse, uygulama için yeni bir uygulama parolası oluşturmayı deneyin. Bunu, [iki adımlı doğrulama için uygulama parolalarını yönetme](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) konusunun **My Apps Portal 'ı kullanarak uygulama parolaları oluşturma ve silme** bölümündeki adımları izleyerek yapın.

## <a name="i-cant-turn-off-two-factor-verification"></a>İki öğeli doğrulamayı kapatamaz

İş veya okul hesabınızla iki öğeli doğrulama kullanıyorsanız (örneğin, alain@contoso.com ), büyük olasılıkla kuruluşunuzun bu ek güvenlik özelliğini kullanmanız gerektiğine karar vermiş olması anlamına gelir. Kuruluşunuz bu özelliği kullanmanız gerektiğine karar verdiği için, tek yapmanız gereken bir yöntem yoktur. Bununla birlikte, bir kişisel hesapla iki öğeli doğrulama kullanıyorsanız, bu alain@outlook.com özelliği açıp kapatabilirsiniz. Kişisel hesaplarınız için iki öğeli doğrulamanın nasıl denetılacağı hakkında yönergeler için, [Microsoft hesabı için iki öğeli doğrulamayı açma veya kapatma](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)konusuna bakın.

İki öğeli doğrulamayı kapatamaz, Ayrıca kuruluş düzeyinde uygulanmış olan güvenlik varsayılanlarının de olabilir. Güvenlik Varsayılanları hakkında daha fazla bilgi için bkz. [güvenlik Varsayılanları nelerdir?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Sorunum için bir yanıt bulamadık

Bu adımları denediyseniz ancak hala sorun yaşıyorsanız, yardım almak için kuruluşunuzun yardım masasına başvurun.

## <a name="related-articles"></a>İlgili makaleler:

- [İki öğeli doğrulama yöntemi ayarlarınızı yönetin](multi-factor-authentication-end-user-manage-settings.md)

- [İki adımlı doğrulama için hesabımı ayarlama](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator uygulaması hakkında SSS](user-help-auth-app-faq.md)
