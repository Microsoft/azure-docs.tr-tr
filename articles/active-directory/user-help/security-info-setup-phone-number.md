---
title: Telefon aramaları için güvenlik bilgilerini ayarlama (Önizleme)-Azure AD
description: Telefon çağrılarını kullanarak kimliğinizi doğrulamak için güvenlik bilgilerinizi ayarlama.
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
ms.openlocfilehash: a5dc5ec06e60112e69a31d1ce4c69e698576609f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73803811"
---
# <a name="set-up-security-info-preview-to-use-phone-calls"></a>Telefon aramalarını kullanmak için güvenlik bilgilerini ayarlama (Önizleme)

İki öğeli doğrulama ve parola sıfırlama yöntemlerinizi eklemek için aşağıdaki adımları izleyebilirsiniz. İlk kez ayarladıktan sonra güvenlik bilgilerini eklemek, güncelleştirmek veya silmek için **güvenlik** bilgileri sayfasına dönebilirsiniz.

Bunu iş veya okul hesabınızda oturum açtıktan hemen sonra ayarlamanız istenirse, [oturum açma sayfası istemi makalesindeki güvenlik bilgilerinizi ayarlama](security-info-setup-signin.md) makalesindeki ayrıntılı adımlara bakın.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> Güvenlik bilgileri telefon uzantılarının kullanımını desteklemiyor. Doğru biçimi ekseniz bile, + 1 4255551234X12345, çağrı yerleştirilmadan önce uzantılar kaldırılır.
>
> Telefon seçeneği görmüyorsanız, kuruluşunuz doğrulama için bir telefon numarası kullanmanıza izin vermiyor olabilir. Bu durumda, daha fazla yardım için başka bir yöntem seçmeniz veya yöneticinize başvurmanız gerekir.

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Güvenlik bilgileri sayfasından telefon çağrılarını ayarlama

Kuruluşunuzun ayarlarına bağlı olarak, telefon çağrılarını güvenlik bilgileri yöntemlerinizin biri olarak kullanabilirsiniz.

>[!Note]
>Telefon araması yerine bir kısa mesaj almak istiyorsanız, [SMS mesajı kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-text-msg.md) makalesini izleyin.

### <a name="to-set-up-phone-calls"></a>Telefon görüşmelerini ayarlamak için

1. İş veya okul hesabınızda oturum açın ve ardından https://myprofile.microsoft.com/ sayfanıza gidin.

    ![, Vurgulanan güvenlik bilgisi bağlantılarını gösteren profilim sayfası](media/security-info/securityinfo-myprofile-page.png)

2. Sol gezinti bölmesinden veya **güvenlik bilgileri** bloğundaki bağlantıdan **güvenlik bilgileri** ' ni seçin ve ardından **güvenlik bilgileri** sayfasından **Yöntem Ekle** ' yi seçin.

    ![Vurgulanan yöntem ekleme seçeneğiyle güvenlik bilgileri sayfası](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **Yöntem Ekle** sayfasında, açılan listeden **Telefon** ' i seçin ve ardından **Ekle**' yi seçin.

    ![Telefon seçiliyken Yöntem kutusu Ekle](media/security-info/securityinfo-myprofile-addphonetext.png)

4. **Telefon** sayfasında, mobil cihazınızın telefon numarasını yazın, **beni çağır**' ı seçin ve ardından **İleri**' yi seçin.

    ![Telefon numarası ekleyin ve telefon görüşmeleri seçin](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Doğrulama telefon aramasını yanıtlayın, girdiğiniz telefon numarasına gönderilir ve yönergeleri izleyin.

    Sayfa, başarınızı gösterecek şekilde değişir.

    ![Başarı bildirimi, telefon numarasını bağlama, telefon görüşmeleri alma seçeneği ve hesabınız](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Güvenlik bilgileriniz güncellenir ve iki adımlı doğrulama veya parola sıfırlama kullanırken kimliğinizi doğrulamak için telefon çağrılarını kullanabilirsiniz. Varsayılan yönteminizin telefon araması yapmak istiyorsanız, bu makalenin [varsayılan güvenlik bilgileri yönteminizi değiştirme](#change-your-default-security-info-method) bölümüne bakın.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Güvenlik bilgileri yöntemlerinizin telefon çağrılarını silme

Artık telefon çağrılarını bir güvenlik bilgisi yöntemi olarak kullanmak istemiyorsanız, **güvenlik bilgileri** sayfasından kaldırabilirsiniz.

>[!Important]
>Telefon çağrılarını yanlışlıkla silerseniz geri alma yöntemi yoktur. Bu makalenin [telefon aramalarını ayarlama](#set-up-phone-calls-from-the-security-info-page) bölümündeki adımları izleyerek yöntemi tekrar eklemeniz gerekir.

### <a name="to-delete-phone-calls"></a>Telefon aramalarını silmek için

1. **Güvenlik bilgileri** sayfasında, **Telefon** seçeneğinin yanındaki **Sil** bağlantısını seçin.

    ![Güvenlik bilgileri 'nden telefon yöntemini silmeye yönelik bağlantı](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. **Telefon** numarasını silmek için onay kutusundan **Evet** ' i seçin. Telefon numaranız silindikten sonra güvenlik bilgileriniz kaldırılır ve **güvenlik bilgileri** sayfasından kaybolur. **Telefon** varsayılan yönteminiz ise, varsayılan olarak bir kullanılabilir yönteme geçiş yapılır.

## <a name="change-your-default-security-info-method"></a>Varsayılan güvenlik bilgileri yönteminizi değiştirme

Telefon aramalarının, iki öğeli doğrulama veya parola sıfırlama istekleri kullanarak iş veya okul hesabınızda oturum açtığınızda kullanılan varsayılan yöntem olmasını istiyorsanız, **güvenlik bilgileri** sayfasından ayarı yapabilirsiniz.

### <a name="to-change-your-default-security-info-method"></a>Varsayılan güvenlik bilgileri yönteminizi değiştirmek için

1. **Güvenlik bilgileri** sayfasında, **varsayılan oturum açma yöntemi** bilgisinin yanındaki bağlantıyı **Değiştir** ' i seçin.

    ![Varsayılan oturum açma yönteminin bağlantısını değiştir](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Kullanılabilir yöntemlerin aşağı açılan listesinden **telefon araması ( *_your_phone_number_* )** öğesini seçin ve ardından **Onayla**' yı seçin.

    ![Varsayılan oturum açma yöntemini seçin](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    **Telefon araması ( *_your_phone_number_* )** için oturum açma değişiklikleri için kullanılan varsayılan yöntem.

## <a name="additional-security-info-methods"></a>Ek güvenlik bilgileri yöntemleri

Kuruluşunuzun, kimliğinizi doğrulamak için size ne olduğuna bağlı olarak kimliğinizi nasıl öğrendiğini öğrenmek için ek seçenekleriniz vardır. Seçeneklere şunlar dahildir:

- **Authenticator uygulaması.** İki adımlı doğrulama veya parola sıfırlama için bir onay bildirimi ya da rastgele oluşturulmuş bir onay kodu almak üzere bir Authenticator uygulaması indirin ve kullanın. Microsoft Authenticator uygulamasını ayarlama ve kullanma hakkında adım adım yönergeler için bkz. [güvenlik bilgilerini bir Authenticator uygulaması kullanmak Için ayarlama](security-info-setup-auth-app.md).

- **Mobil cihaz metni.** Mobil cihaz numaranızı girin ve iki adımlı doğrulama veya parola sıfırlama için kullanacağınız kodu bir metin alın. Bir SMS mesajı (SMS) ile kimliğinizi doğrulamaya yönelik adım adım yönergeler için bkz. [güvenlik bilgilerini ayarlama hakkında bilgi iletisi (SMS)](security-info-setup-text-msg.md).

- **Güvenlik anahtarı.** Microsoft uyumlu güvenlik anahtarınızı kaydedin ve iki adımlı doğrulama veya parola sıfırlama için PIN ile birlikte kullanın. Bir güvenlik anahtarı ile kimliğinizi doğrulama hakkında adım adım yönergeler için bkz. Güvenlik [anahtarı kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-security-key.md).

- **E-posta adresi.** Parola sıfırlama için bir e-posta almak üzere iş veya okul e-posta adresinizi girin. Bu seçenek, iki adımlı doğrulama için kullanılamaz. E-postanızı ayarlama hakkında adım adım yönergeler için bkz. [e-posta kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-email.md).

- **Güvenlik soruları.** Yöneticiniz tarafından kuruluşunuz için oluşturulan bazı güvenlik sorularını yanıtlayın. Bu seçenek yalnızca parola sıfırlama için kullanılabilir ve iki adımlı doğrulama için kullanılamaz. Güvenlik sorularınızı ayarlama hakkında adım adım yönergeler için Güvenlik [sorularını kullanmak üzere güvenlik bilgilerini ayarlama](security-info-setup-questions.md) makalesine bakın.

    >[!Note]
    >Bu seçeneklerden bazıları eksikse, kuruluşunuzun bu yöntemlere izin vermediği için büyük olasılıkla olasıdır. Bu durumda, daha fazla yardım için kullanılabilir bir yöntem seçmeniz veya yöneticinize başvurmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- Parola [sıfırlama portalından](https://passwordreset.microsoftonline.com/) kayıp veya unuttuysanız parolanızı sıfırlayın veya [iş veya okul parolanızı sıfırlama](active-directory-passwords-update-your-own-password.md) makalesindeki adımları izleyin.

- [Microsoft hesabı makalesinde oturum açma](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) sorunları için sorun giderme ipuçları ve yardım alın.
