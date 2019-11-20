---
title: Kimlik doğrulama uygulaması için güvenlik bilgilerini ayarlama (Önizleme)-Azure AD
description: Microsoft Authenticator uygulamasını kullanarak kimliğinizi doğrulamak için güvenlik bilgilerinizi ayarlama.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19f44f4df9e3c17c376830c6a404a21e8a5d67b2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805077"
---
# <a name="set-up-security-info-preview-to-use-an-authenticator-app"></a>Kimlik doğrulayıcı uygulaması kullanmak için güvenlik bilgilerini ayarlama (Önizleme)

İki öğeli doğrulama ve parola sıfırlama yöntemlerinizi eklemek için aşağıdaki adımları izleyebilirsiniz. İlk kez ayarladıktan sonra güvenlik bilgilerini eklemek, güncelleştirmek veya silmek için **güvenlik** bilgileri sayfasına dönebilirsiniz.

Bunu iş veya okul hesabınızda oturum açtıktan hemen sonra ayarlamanız istenirse, [oturum açma sayfası istemi makalesindeki güvenlik bilgilerinizi ayarlama](security-info-setup-signin.md) makalesindeki ayrıntılı adımlara bakın.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Bir Authenticator uygulama seçeneği görmüyorsanız, kuruluşunuz doğrulama için bir kimlik doğrulama uygulaması kullanmanıza izin vermiyor olabilir. Bu durumda, daha fazla yardım için başka bir yöntem seçmeniz veya yöneticinize başvurmanız gerekir.

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Güvenlik bilgileri sayfasından Microsoft Authenticator uygulamasını ayarlama

Kuruluşunuzun ayarlarına bağlı olarak, güvenlik bilgileri yöntemlerinizin biri olarak bir kimlik doğrulama uygulaması kullanabilirsiniz. Microsoft Authenticator uygulamasını kullanmanız gerekmez ve kurulum işlemi sırasında farklı bir uygulama seçebilirsiniz. Ancak, bu makale Microsoft Authenticator uygulamasını kullanır.

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Microsoft Authenticator uygulamasını ayarlamak için

1. İş veya okul hesabınızda oturum açın ve ardından https://myprofile.microsoft.com/ sayfanıza gidin.

    ![, Vurgulanan güvenlik bilgisi bağlantılarını gösteren profilim sayfası](media/security-info/securityinfo-myprofile-page.png)

2. Sol gezinti bölmesinden veya **güvenlik bilgileri** bloğundaki bağlantıdan **güvenlik bilgileri** ' ni seçin ve ardından **güvenlik bilgileri** sayfasından **Yöntem Ekle** ' yi seçin.

    ![Vurgulanan yöntem ekleme seçeneğiyle güvenlik bilgileri sayfası](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **Yöntem Ekle** sayfasında, açılan listeden **Authenticator uygulaması** ' nı seçin ve ardından **Ekle**' yi seçin.

    ![Kimlik doğrulayıcı uygulaması seçiliyken Yöntem kutusu Ekle](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Uygulamayı alarak **Başlarken** sayfasında, mobil cihazınıza Microsoft Authenticator uygulamayı indirip yüklemek Için **Şimdi İndir** ' i seçin ve ardından **İleri**' yi seçin.

    Uygulamanın nasıl indirileceği ve yükleneceği hakkında daha fazla bilgi için, bkz. [Microsoft Authenticator uygulamasını indirme ve yükleme](user-help-auth-app-download-install.md).

    ![Uygulama sayfasını alarak başlayın](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Microsoft Authenticator uygulaması dışında bir Authenticator uygulaması kullanmak istiyorsanız, **farklı bir Authenticator uygulama bağlantısı kullanmak istiyorum** bağlantısını seçin.
   >
   > Kuruluşunuz, doğrulayıcı uygulamasının yanı sıra farklı bir yöntem seçmenizi sağlar, **farklı bir yöntem bağlantısı kurmak istiyorum**' u seçebilirsiniz.

5. Mobil cihazınızda Microsoft Authenticator uygulamayı ayarlarken **hesabınızı ayarlama** sayfasında kalır.

    ![Authenticator uygulama sayfasını ayarlama](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Microsoft Authenticator uygulamasını açın, bildirimlere izin ver ' i seçin (istenirse), sağ üst köşedeki **Özelleştir ve denetim** simgesinden **Hesap Ekle** ' yi seçin ve sonra **iş veya okul hesabı**' nı seçin.

    >[!Note]
    >Microsoft Authenticator uygulamasını ilk kez ayarlıyorsanız, uygulamanın kameranıza erişmesine izin verip vermeyeceğinizi (iOS) veya uygulamanın resim almasına ve video (Android) kaydetmesine izin verip vermeyeceğinizi soran bir istem alabilirsiniz. Bir sonraki adımda QR kodunun bir resmini almak için, kimlik doğrulayıcı uygulamasının kameranıza erişebilmesi için **Izin ver** ' i seçmeniz gerekir. Kameraya izin vermezseniz, kimlik doğrulayıcı uygulamasını ayarlamaya devam edebilirsiniz, ancak kod bilgilerini el ile eklemeniz gerekir. Kodu el ile ekleme hakkında daha fazla bilgi için bkz. [uygulamaya el ile hesap ekleme](user-help-auth-app-add-account-manual.md).

7. Bilgisayarınızda **hesabınızı ayarlama** sayfasına dönün ve sonra **İleri**' yi seçin.

    **QR kodunu Tara** sayfası görüntülenir.

    ![Doğrulayıcı uygulamasını kullanarak QR kodunu tarama](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Adım 6 ' da iş veya okul hesabınızı oluşturduktan sonra, mobil cihazınızda görüntülenen Microsoft Authenticator App QR kod okuyucusu ile belirtilen kodu tarayın.

    Kimlik doğrulayıcı uygulaması, size herhangi bir ek bilgi gerekmeden iş veya okul hesabınızı başarıyla eklememelidir. Ancak, QR kod okuyucusu kodu okuyaamazsa **QR kodu bağlantısını tarayamaz** ve kodu ve URL 'yi Microsoft Authenticator uygulamasına el ile girebilirsiniz. Kodu el ile ekleme hakkında daha fazla bilgi için bkz. [uygulamaya el ile hesap ekleme](user-help-auth-app-add-account-manual.md).

9. Bilgisayarınızdaki **QR kodunu Tara** sayfasında **İleri ' yi** seçin.

    Hesabınızı test etmek için mobil cihazınızdaki Microsoft Authenticator uygulamasına bir bildirim gönderilir.

    ![Kimlik doğrulayıcı uygulamasıyla hesabınızı test etme](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Microsoft Authenticator uygulamasındaki bildirimi onaylayın ve ardından **İleri**' yi seçin.

     ![Başarı bildirimi, uygulamayı ve hesabınızı bağlama](media/security-info/securityinfo-myprofile-successauthapp.png)

     Güvenlik bilgileriniz, iki adımlı doğrulama veya parola sıfırlama kullanırken kimliğinizi doğrulamak için varsayılan olarak Microsoft Authenticator uygulamasını kullanacak şekilde güncelleştirilir.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Güvenlik bilgileri yöntemlerinizin kimlik doğrulayıcı uygulamanızı silme

Artık kimlik doğrulayıcı uygulamanızı bir güvenlik bilgisi yöntemi olarak kullanmak istemiyorsanız, **güvenlik bilgileri** sayfasından kaldırabilirsiniz. Bu, yalnızca Microsoft Authenticator uygulaması değil, tüm Authenticator uygulamaları için geçerlidir. Uygulamayı sildikten sonra, mobil cihazınızda kimlik doğrulayıcı uygulamasına gitmeniz ve hesabı silmeniz gerekir.

>[!Important]
>Doğrulayıcı uygulamasını yanlışlıkla silerseniz, geri alma yöntemi yoktur. Bu makalenin [Authenticator uygulamasını ayarlama](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) bölümündeki adımları izleyerek Doğrulayıcı uygulamasını tekrar eklemeniz gerekir.

### <a name="to-delete-the-authenticator-app"></a>Doğrulayıcı uygulamasını silmek için

1. **Güvenlik bilgileri** sayfasında, doğrulayıcı uygulamasının yanındaki **Sil** bağlantısını seçin.

    ![Güvenlik bilgileri 'nden kimlik doğrulayıcı uygulamasını silme bağlantısı](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Kimlik doğrulayıcı uygulamasını silmek için onay kutusundan **Evet** ' i seçin. Kimlik doğrulayıcı uygulaması silindikten sonra, güvenlik bilgileriniz kaldırılır ve **güvenlik bilgileri** sayfasından kaybolur. Kimlik doğrulayıcı uygulaması varsayılan yönteminiz ise, varsayılan olarak bir kullanılabilir yönteme geçiş yapılır.

3. Mobil cihazınızda kimlik doğrulayıcı uygulamasını açın, **hesapları Düzenle**' yi seçin ve iş veya okul hesabınızı kimlik doğrulayıcı uygulamasından silin.

    Hesabınız iki öğeli doğrulama ve parola sıfırlama istekleri için doğrulayıcı uygulamasından tamamen kaldırılır.

## <a name="change-your-default-security-info-method"></a>Varsayılan güvenlik bilgileri yönteminizi değiştirme

Kimlik doğrulayıcı uygulamasının, iki öğeli doğrulama kullanarak iş veya okul hesabınızda oturum açtığınızda veya parola sıfırlama istekleri için kullanılan varsayılan yöntem olmasını istiyorsanız, güvenlik **bilgileri** sayfasından ayarı yapabilirsiniz.

### <a name="to-change-your-default-security-info-method"></a>Varsayılan güvenlik bilgileri yönteminizi değiştirmek için

1. **Güvenlik bilgileri** sayfasında, **varsayılan oturum açma yöntemi** bilgisinin yanındaki bağlantıyı **Değiştir** ' i seçin.

    ![Varsayılan oturum açma yönteminin bağlantısını değiştir](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Kullanılabilir yöntemlerin aşağı açılan listesinden **Microsoft Authenticator bildirimi** ' ni seçin. Microsoft Authenticator uygulamasını kullanmıyorsanız, **Authenticator uygulama veya donanım belirteci** seçeneğini belirleyin.

    ![Varsayılan oturum açma yöntemini seçin](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. **Onayla**seçeneğini belirleyin.

    Microsoft Authenticator uygulamasında oturum açma değişiklikleri için kullanılan varsayılan yöntem.

## <a name="additional-security-info-methods"></a>Ek güvenlik bilgileri yöntemleri

Kuruluşunuzun, kimliğinizi doğrulamak için size ne olduğuna bağlı olarak kimliğinizi nasıl öğrendiğini öğrenmek için ek seçenekleriniz vardır. Seçeneklere şunlar dahildir:

- **Mobil cihaz metni.** Mobil cihaz numaranızı girin ve iki adımlı doğrulama veya parola sıfırlama için kullanacağınız kodu bir metin alın. Bir SMS mesajı (SMS) ile kimliğinizi doğrulamaya yönelik adım adım yönergeler için bkz. [güvenlik bilgilerini ayarlama hakkında bilgi iletisi (SMS)](security-info-setup-text-msg.md).

- **Mobil cihaz veya iş telefonu çağrısı.** Mobil cihaz numaranızı girin ve iki adımlı doğrulama veya parola sıfırlama için bir telefon araması alın. Telefon numarası ile kimliğinizi doğrulama hakkında adım adım yönergeler için bkz. [telefon aramalarını kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-phone-number.md).

- **Güvenlik anahtarı.** Microsoft uyumlu güvenlik anahtarınızı kaydedin ve iki adımlı doğrulama veya parola sıfırlama için PIN ile birlikte kullanın. Bir güvenlik anahtarı ile kimliğinizi doğrulama hakkında adım adım yönergeler için bkz. Güvenlik [anahtarı kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-security-key.md).

- **E-posta adresi.** Parola sıfırlama için bir e-posta almak üzere iş veya okul e-posta adresinizi girin. Bu seçenek, iki adımlı doğrulama için kullanılamaz. E-postanızı ayarlama hakkında adım adım yönergeler için bkz. [e-posta kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-email.md).

- **Güvenlik soruları.** Yöneticiniz tarafından kuruluşunuz için oluşturulan bazı güvenlik sorularını yanıtlayın. Bu seçenek yalnızca parola sıfırlama için kullanılabilir ve iki adımlı doğrulama için kullanılamaz. Güvenlik sorularınızı ayarlama hakkında adım adım yönergeler için Güvenlik [sorularını kullanmak üzere güvenlik bilgilerini ayarlama](security-info-setup-questions.md) makalesine bakın.

    >[!Note]
    >Bu seçeneklerden bazıları eksikse, kuruluşunuzun bu yöntemlere izin vermediği için büyük olasılıkla olasıdır. Bu durumda, daha fazla yardım için kullanılabilir bir yöntem seçmeniz veya yöneticinize başvurmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [İki adımlı doğrulama veya güvenlik bilgilerini kullanarak oturum açma](security-info-setup-signin.md) makalesindeki adımları izleyerek Microsoft Authenticator uygulamasını kullanarak oturum açın.

- Parola [sıfırlama portalından](https://passwordreset.microsoftonline.com/) kayıp veya unuttuysanız parolanızı sıfırlayın veya [iş veya okul parolanızı sıfırlama](active-directory-passwords-update-your-own-password.md) makalesindeki adımları izleyin.

- [Microsoft hesabı makalesinde oturum açma](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) sorunları için sorun giderme ipuçları ve yardım alın.
