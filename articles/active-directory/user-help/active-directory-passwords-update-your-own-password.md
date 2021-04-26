---
title: Güvenlik bilgilerini kullanarak parolanızı sıfırlayın-Azure Active Directory | Microsoft Docs
description: Bilgisayarınızı unutursanız, güvenlik bilgilerinizi ve iki aşamalı doğrulamayı kullanarak kendi parolanızı sıfırlayın.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/10/2020
ms.author: curtand
ms.openlocfilehash: c47d573dab3e72a956f6f0b9d09a1dafadf0225c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91827770"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>Güvenlik bilgilerini kullanarak iş veya okul parolanızı sıfırlayın

İş veya okul parolanızı unuttuysanız, kuruluşunuzda hiç parola yoksa veya hesabınızı kilitlediyseniz, iş veya okul parolanızı sıfırlamak için güvenlik bilgilerinizi ve mobil cihazınızı kullanabilirsiniz. Bilgilerinizi ayarlayabilmeniz ve [kendi parolanızı sıfırlayabilmeniz](./active-directory-passwords-reset-register.md)için yöneticinizin bu özelliği açmanız gerekir.

Parolanızı biliyorsanız ve değiştirmek istiyorsanız, bu makalenin [parola adımlarınızı değiştirme](#how-to-change-your-password) başlıklı bölüme bakın.

>[!Important]
>Bu makale, unutulan veya bilinmeyen bir iş veya okul hesabı parolasını sıfırlama kullanmaya çalışan kullanıcılara yöneliktir. Çalışanlarınız veya diğer kullanıcılarınız için self servis parola sıfırlamayı açma hakkında bilgi için bkz. [Azure AD self servis parola sıfırlama ve diğer makalelere dağıtım](../authentication/howto-sspr-deployment.md).

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Bir iş veya okul hesabı için parolanızı sıfırlama veya kilidini açma

Azure Active Directory (Azure AD) Hesabınıza erişemiyorsanız aşağıdakilerden biri olabilir:

- Parolanız çalışmıyor ve sıfırlamak istiyorsunuz veya

- Parolanızı bilirsiniz, ancak hesabınız kilitlidir ve kilidini açmanız gerekir.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Parolanızı sıfırlamak ve hesabınıza geri dönmek için

1. **Parola gir** ekranında **Parolamı unuttum**' ı seçin.

2. **Hesabınızı geri al** ekranında iş veya okul **Kullanıcı Kimliğinizi** (örneğin, e-posta adresiniz) yazın, ekranda gördüğünüz karakterleri girerek bir robot olmadığınızı kanıtlayın ve sonra **İleri**' yi seçin.

   ![Hesap ekranınıza geri dönün](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Yöneticiniz kendi parolanızı sıfırlamanıza izin vermediyse, **Hesap ekranınıza geri dönmek** yerine yönetici bağlantısı Ile **iletişim kurun** . Bu bağlantı, e-posta veya Web portalı aracılığıyla parolanızı sıfırlama hakkında yöneticinize başvurmanızı sağlar.

3. Kimliğinizi doğrulamak ve parolanızı değiştirmek için aşağıdaki yöntemlerden birini seçin. Yöneticinizin kuruluşunuzu nasıl ayarladığına bağlı olarak, ikinci bir doğrulama adımı için bilgi ekleyerek bu işleme ikinci kez gitmeniz gerekebilir.

    ![Hesabınıza geri dönün, doğrulama adımından yararlanın #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >Yöneticinizin kuruluşunuzu nasıl ayarladığına bağlı olarak, bu doğrulama seçeneklerinden bazıları kullanılamayabilir. Bu yöntemlerden en az birini kullanarak, daha önce mobil cihazınızı doğrulama için ayarlamanız gerekir.<br><br>Ayrıca, Yeni parolanızın belirli güç gereksinimlerini karşılaması gerekebilir. Güçlü parolalar genellikle, büyük ve küçük harfler, en az bir sayı ve en az bir özel karakter dahil olmak üzere 8 ile 16 arasında karakter uzunluğunda olabilir.

- **Bir e-posta adresi kullanarak parolanızı sıfırlayın.** Daha önce iki adımlı doğrulama veya güvenlik bilgilerinde ayarladığınız e-posta adresine bir e-posta gönderir. Yöneticiniz güvenlik bilgileri deneyimini etkinleştirmiştir, e-posta adresi ayarlama hakkında daha fazla bilgi edinmek [için güvenlik bilgilerini ayarla (Önizleme) makalesini kullanın](security-info-setup-email.md) . Henüz güvenlik bilgilerini kullanmıyorsanız, [iki adımlı doğrulama için hesabımı ayarla](multi-factor-authentication-end-user-first-time.md) ' da bir e-posta adresi ayarlama hakkında daha fazla bilgi edinebilirsiniz. 

    1. Diğer e- **postam** i seçin ve ardından **e-posta** seçeneğini belirleyin.

    2. E-postadaki doğrulama kodunu kutuya yazın ve ardından **İleri**' yi seçin.

    3. Yeni parolanızı yazın ve doğrulayın ve ardından **son**' u seçin.

- **Kısa mesaj kullanarak parolanızı sıfırlayın.** Daha önce güvenlik bilgilerinde ayarladığınız telefon numarasına bir kısa mesaj gönderir. Yöneticiniz güvenlik bilgileri deneyimini etkinleştirmiştir, SMS mesajı ayarlama hakkında daha fazla bilgi edinmek [için güvenlik bilgilerini ayarlama (Önizleme) makalesini kullanın](security-info-setup-text-msg.md) . Henüz güvenlik bilgilerini kullanmıyorsanız, [iki adımlı doğrulama için hesabımı ayarla](multi-factor-authentication-end-user-first-time.md) bölümünde metin iletilerini ayarlama hakkında daha fazla bilgi edinebilirsiniz.

    1. **Cep telefonumu** seçin, telefon numaranızı yazın ve ardından **metin**' i seçin.

    2. Metin iletisinden doğrulama kodunu kutuya yazın ve ardından **İleri**' yi seçin.

    3. Yeni parolanızı yazın ve doğrulayın ve ardından **son**' u seçin.

- **Bir telefon numarası kullanarak parolanızı sıfırlayın.** Daha önce güvenlik bilgilerinde ayarladığınız telefon numarasına otomatik bir sesli çağrı koyar. Yöneticiniz güvenlik bilgileri deneyimini kullanıyorsa, bir telefon [araması (Önizleme) kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-phone-number.md) bölümünde bir telefon numarası ayarlama hakkında daha fazla bilgi edinebilirsiniz. Henüz güvenlik bilgilerini kullanmıyorsanız, [iki adımlı doğrulama için hesabımı ayarla](multi-factor-authentication-end-user-first-time.md) makalesinde bir telefon numarası ayarlama hakkında daha fazla bilgi edinebilirsiniz.

    1. **Cep telefonumu çağır**' ı seçin, telefon numaranızı yazın ve ardından **çağır**' ı seçin.

    2. Telefon görüşmesini yanıtlayın ve kimliğinizi doğrulamak için yönergeleri izleyin ve sonra **İleri**' yi seçin.

    3. Yeni parolanızı yazın ve doğrulayın ve ardından **son**' u seçin.

- **Güvenlik sorularını kullanarak parolanızı sıfırlayın.** Güvenlik bilgilerinde ayarladığınız güvenlik sorularının listesini gösterir. Yöneticiniz güvenlik bilgileri deneyimini etkinleştirmişse güvenlik sorularınızı ayarlama hakkında daha fazla bilgi edinmek [için güvenlik bilgilerini ayarlama bölümünde önceden tanımlı güvenlik soruları (Önizleme) makalesini kullanın](security-info-setup-questions.md) . Henüz güvenlik bilgilerini kullanmıyorsanız, [iki adımlı doğrulama için hesabımı ayarla](multi-factor-authentication-end-user-first-time.md) bölümünde güvenlik sorularını ayarlama hakkında daha fazla bilgi edinebilirsiniz.

    1. **Güvenlik Sorularımı yanıtla**' yı seçin, soruları yanıtlayın ve sonra **İleri**' yi seçin.

    2. Yeni parolanızı yazın ve doğrulayın ve ardından **son**' u seçin.

- **Kimlik doğrulayıcı uygulamanızdan bir bildirim kullanarak parolanızı sıfırlayın.** Doğrulayıcı uygulamasına bir onay bildirimi gönderir. Yöneticiniz güvenlik bilgileri deneyimini etkinleştirmiştir, [kimlik doğrulama uygulaması (Önizleme) için güvenlik bilgilerini ayarlama](security-info-setup-auth-app.md) makalesine bildirim göndermek üzere bir Authenticator uygulaması ayarlama hakkında daha fazla bilgi bulabilirsiniz. Henüz güvenlik bilgilerini kullanmıyorsanız, [iki adımlı doğrulama için hesabımı ayarla](multi-factor-authentication-end-user-first-time.md) makalesinde bildirim göndermek üzere bir Authenticator uygulaması ayarlama hakkında daha fazla bilgi bulabilirsiniz.

    1. **Doğrulayıcı uygulamamda bildirimi Onayla**' yı seçin ve **bildirim gönder**' i seçin.

    2. Kimlik doğrulayıcı uygulamanızdan oturum açmayı onaylayın.

    3. Yeni parolanızı yazın ve doğrulayın ve ardından **son**' u seçin.

- **Kimlik doğrulayıcı uygulamanızdan bir kod kullanarak parolanızı sıfırlayın.** Kimlik doğrulama uygulamanız tarafından sunulan rastgele bir kodu kabul eder. Yöneticiniz güvenlik bilgileri deneyimini etkinleştirmiştir, [kimlik doğrulama uygulaması (Önizleme) için güvenlik bilgilerini ayarlama](security-info-setup-auth-app.md) bölümünde bir kod sağlamak üzere bir Authenticator uygulaması ayarlama hakkında daha fazla bilgi edinebilirsiniz. Henüz güvenlik bilgilerini kullanmıyorsanız, [iki adımlı doğrulama için hesabımı ayarlama](multi-factor-authentication-end-user-first-time.md) bölümünde bir kod sağlamak üzere bir Authenticator uygulaması ayarlama hakkında daha fazla bilgi edinebilirsiniz.

  1. **Doğrulayıcı uygulamamda bir kod girin**' i seçin ve **bildirim gönder**' i seçin.

  2. Kimlik doğrulayıcı uygulamanızı açın, hesabınıza ait doğrulama kodunu kutuya yazın ve ardından **İleri**' yi seçin.

  3. Yeni parolanızı yazın ve doğrulayın ve ardından **son**' u seçin.

  4. Parolanızın sıfırlandığını söyleyen iletiyi aldıktan sonra, hesabınızda yeni parolanızı kullanarak oturum açabilirsiniz.

     Hesabınıza hala erişemiyorsanız, daha fazla yardım için kuruluşunuzun yöneticisiyle iletişim kurmanız gerekir.

Parolanızı sıfırladıktan sonra, "Microsoft adına Microsoft" gibi bir hesaptan gelen bir onay e-postası alabilirsiniz \<*your_organization*> . Benzer bir e-posta alırsanız ancak parolanızı kısa süre önce sıfırlamadıysanız, kuruluşunuzun yöneticisiyle hemen iletişim kurmanız gerekir.

## <a name="how-to-change-your-password"></a>Parolanızı değiştirme

Yalnızca parolanızı değiştirmek istiyorsanız, bunu Office 365 Portalı, My Apps portalı veya Windows 10 oturum açma sayfasından yapabilirsiniz.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Office 365 portalını kullanarak parolanızı değiştirme

Genellikle uygulamalarınıza Office portalından erişmeniz durumunda bu yöntemi kullanın:

1. Mevcut parolanızı kullanarak [Office 365 hesabınızda](https://portal.office.com)oturum açın.

2. Sağ üst taraftaki profilinizi seçin ve sonra **hesabı görüntüle**' yi seçin.

3. **Güvenlik & gizlilik**  >  **parolası**' nı seçin.

4. Eski parolanızı yazın, yeni parolanızı oluşturup onaylayın ve ardından **Gönder**' i seçin.

### <a name="to-change-your-password-from-the-my-apps-portal"></a>Uygulamalarım portalından parolanızı değiştirmek için

Uygulamalarınıza genellikle uygulamalarım portalından erişmeniz durumunda bu yöntemi kullanın:

1. Mevcut parolanızı kullanarak, [My Apps portalında](https://myapps.microsoft.com/)oturum açın.

2. Sağ üst taraftaki profilinizi seçip **profil**' i seçin.

3. **Parolayı Değiştir**' i seçin.

4. Eski parolanızı yazın, yeni parolanızı oluşturup onaylayın ve ardından **Gönder**' i seçin.

### <a name="to-change-your-password-at-windows-sign-in"></a>Windows oturum açma sırasında parolanızı değiştirmek için

Yöneticiniz işlevselliği etkinleştirdiyseniz, Windows 7, Windows 8, Windows 8.1 veya Windows 10 oturum açma ekranında **parola sıfırlama** bağlantısını görebilirsiniz.

1. Normal web tabanlı deneyimi kullanmak zorunda kalmadan parola sıfırlama işlemini başlatmak için **parola sıfırlama** bağlantısını seçin.

2. Kullanıcı KIMLIĞINIZI doğrulayın ve Ileri ' **yi** seçin.

3. Doğrulama için bir iletişim yöntemi seçin ve onaylayın. Gerekirse, gerekli bilgileri doldurarak, önceki bir kaynaktan farklı ikinci bir doğrulama seçeneği belirleyin.

4. **Yeni parola oluştur** sayfasında, yeni parolanızı yazın ve doğrulayın ve ardından **İleri**' yi seçin.

    Güçlü parolalar genellikle, büyük ve küçük harfler, en az bir sayı ve en az bir özel karakter dahil olmak üzere 8 ile 16 arasında karakter uzunluğunda olabilir.

5. Parolanızın sıfırlandığını söyleyen iletiyi aldıktan sonra, **son**' u seçebilirsiniz.

    Hesabınıza hala erişemiyorsanız, daha fazla yardım için kuruluşunuzun yöneticisiyle iletişim kurmanız gerekir.

## <a name="common-problems-and-their-solutions"></a>Yaygın sorunlar ve çözümleri

Aşağıda bazı yaygın hata durumları ve çözümleri verilmiştir:

|Sorun|Açıklama|Çözüm|
| --- | --- | --- |
|Parolamı değiştirmeyi denediğimde bir hata alıyorum. |Parolanız, parolanızı kolayca tahmin eden bir sözcük, tümcecik veya düzene sahiptir.| Daha güçlü bir parola kullanarak yeniden deneyin.|
|Kullanıcı kimliğimi girdikten sonra, "lütfen yöneticinize başvurun" yazan bir sayfaya gideceğim.|Microsoft, Kullanıcı hesabınızın parolasının şirket içi bir ortamda yönetilip yönetilmediğini belirledi. Sonuç olarak, parolanızı "hesabınıza erişemiyor" bağlantısına sıfırlayamazsınız. |Daha fazla yardım için yöneticinize başvurun.|
|Kullanıcı kimliğimi girdikten sonra, "hesabınız parola sıfırlama için etkinleştirilmemiş" ifadesini içeren bir hata alıyorum.|Yöneticiniz, kendi parolanızı sıfırlayabilmeniz için hesabınızı kurmadı.|Yöneticiniz, "hesabınıza erişemiyor" bağlantısına sahip kuruluşunuz için parola sıfırlamasını açılmadı veya özelliği kullanmak için size lisanslandırmadı.<br><br> Parolanızı sıfırlamak için şirketinizin yöneticisine bir e-posta göndermek üzere "Yönetici bağlantısına başvurun" seçeneğini belirleyip parolanızı sıfırlamak istediğinizi bilmeleri gerekir.|
|Kullanıcı kimliğimi girdikten sonra, "hesabınızı doğrulayamadık" ifadesini içeren bir hata alıyorum.|Oturum açma işlemi, hesap bilgilerinizi doğrulayamadı.|Bu iletiyi görmenizin iki nedeni vardır.<br><br>1. yöneticiniz kuruluşunuz için parola sıfırlamasını açtınız, ancak hizmeti kullanmak üzere kaydolmadınız. Parola sıfırlama için kaydolmak üzere doğrulama yönteminizi temel alarak aşağıdaki makalelerden birine bakın: [bir kimlik doğrulayıcı uygulaması (Önizleme) kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-auth-app.md), [bir telefon araması kullanmak](security-info-setup-phone-number.md)için güvenlik bilgilerini ayarlama (Önizleme), [metin iletilerini](security-info-setup-text-msg.md)kullanacak şekilde güvenlik bilgilerini ayarlama (Önizleme), güvenlik bilgilerini kullanacak şekilde güvenlik bilgilerini ayarlama [(Önizleme](security-info-setup-email.md)) veya güvenlik sorularını kullanmak için güvenlik bilgilerini ayarlama ( [Önizleme)](security-info-setup-questions.md).<br><br>2. yöneticiniz, kuruluşunuz için parola sıfırlama özelliği açılmadı. Bu durumda, yöneticinize e-posta göndermek için "Yönetici bağlantısına başvurun" seçeneğini seçmeniz ve parolanızı sıfırlamamanız gerekir.|

## <a name="next-steps"></a>Sonraki adımlar

- Güvenlik [bilgileri (Önizleme) genel bakış](./security-info-setup-signin.md) makalesindeki güvenlik bilgileri hakkında bilgi edinin.

- Xbox, hotmail.com veya outlook.com gibi bir kişisel hesaba geri dönmek istiyorsanız, [Microsoft hesabı makalesinde oturum](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)açarken önerilerini deneyin.
