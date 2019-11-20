---
title: E-postayı kullanmak için güvenlik bilgilerini ayarlama (Önizleme)-Azure AD
description: E-posta adresinizi kullanarak kimliğinizi doğrulamak için güvenlik bilgilerinizi ayarlama.
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
ms.openlocfilehash: a025005065174d5a6ca5fca27ee06ebb036b7b1d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820338"
---
# <a name="set-up-security-info-preview-to-use-your-email-address"></a>E-posta adresinizi kullanmak için güvenlik bilgilerini ayarlama (Önizleme)

Parola sıfırlama yönteminizi eklemek için bu adımları takip edebilirsiniz. İlk kez ayarladıktan sonra güvenlik bilgilerini eklemek, güncelleştirmek veya silmek için **güvenlik** bilgileri sayfasına dönebilirsiniz.

Parola sıfırlama yönteminizi ayarladıktan sonra, bir [doğrulayıcı uygulaması](security-info-setup-auth-app.md), [SMS mesajı](security-info-setup-text-msg.md)veya [telefon araması](security-info-setup-phone-number.md)kullanarak iki öğeli doğrulama yönteminizi de ayarlamanız gerekir.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Güvenlik bilgileri sayfasından e-posta adresinizi ayarlayın

Kuruluşunuzun ayarlarına bağlı olarak, e-posta adresinizi güvenlik bilgisi yöntemlerinden biri olarak kullanabilirsiniz.

>[!Note]
>Ağ parolanızın erişmesini gerektirmeyen bir e-posta adresi kullanmanızı öneririz. E-posta seçeneğini görmüyorsanız, kuruluşunuz doğrulama için bir e-posta kullanmanıza izin vermiyor olabilir. Bu durumda başka bir yöntem seçmeniz veya daha fazla yardım almak için yöneticinize başvurmanız gerekir.

### <a name="to-set-up-your-email-address"></a>E-posta adresinizi ayarlamak için

1. İş veya okul hesabınızda oturum açın ve ardından https://myprofile.microsoft.com/ sayfanıza gidin.

    ![, Vurgulanan güvenlik bilgisi bağlantılarını gösteren profilim sayfası](media/security-info/securityinfo-myprofile-page.png)

2. Sol gezinti bölmesinden veya **güvenlik bilgileri** bloğundaki bağlantıdan **güvenlik bilgileri** ' ni seçin ve ardından **güvenlik bilgileri** sayfasından **Yöntem Ekle** ' yi seçin.

    ![Vurgulanan yöntem ekleme seçeneğiyle güvenlik bilgileri sayfası](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **Yöntem Ekle** sayfasında, açılan listeden **e-posta** ' yı seçin ve ardından **Ekle**' yi seçin.

    ![E-posta seçiliyken Yöntem kutusu Ekle](media/security-info/securityinfo-myprofile-addemail.png)

4. **E-posta** sayfasında, e-posta adresinizi yazın (örneğin, alain@gmail.com) ve ardından **İleri**' yi seçin.

    ![Telefon numarası ekleyin ve telefon görüşmeleri seçin](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Bu e-posta adresi iş veya okul e-postanız olamaz.

5. Belirtilen e-posta adresinize gönderilen kodu yazın ve ardından **İleri**' yi seçin.

    ![Telefon numarası Ekle ve metin iletilerini Seç](media/security-info/securityinfo-myprofile-emailcode.png)

    Güvenlik bilgileriniz güncelleştirildi ve parola sıfırlama kullanırken kimliğinizi doğrulamak için e-posta adresinizi kullanabilirsiniz.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Güvenlik bilgileri yöntemlerinizin e-posta adresini silin

Artık e-posta adresinizi bir güvenlik bilgisi yöntemi olarak kullanmak istemiyorsanız, **güvenlik bilgileri** sayfasından kaldırabilirsiniz.

>[!Important]
>E-posta adresinizi yanlışlıkla silerseniz, geri almanın bir yolu yoktur. Bu makalenin [e-posta adresinizi ayarlama](#set-up-your-email-address-from-the-security-info-page) bölümündeki adımları izleyerek yöntemi tekrar eklemeniz gerekir.

### <a name="to-delete-your-email-address"></a>E-posta adresinizi silmek için

1. **Güvenlik bilgileri** sayfasında, **e-posta** seçeneğinin yanındaki **Sil** bağlantısını seçin.

    ![Güvenlik bilgileri 'nden telefon yöntemini silmeye yönelik bağlantı](media/security-info/securityinfo-myprofile-emaildelete.png)

2. **E-posta** hesabını silmek için onay kutusundan **Evet** ' i seçin. E-posta hesabı silindikten sonra güvenlik bilgilerim kaldırılır ve **güvenlik bilgileri** sayfasından kaybolur.

## <a name="additional-security-info-methods"></a>Ek güvenlik bilgileri yöntemleri

Kuruluşunuzun, kimliğinizi doğrulamak için size ne olduğuna bağlı olarak kimliğinizi nasıl öğrendiğini öğrenmek için ek seçenekleriniz vardır. Seçeneklere şunlar dahildir:

- **Authenticator uygulaması.** İki adımlı doğrulama veya parola sıfırlama için bir onay bildirimi ya da rastgele oluşturulmuş bir onay kodu almak üzere bir Authenticator uygulaması indirin ve kullanın. Microsoft Authenticator uygulamasını ayarlama ve kullanma hakkında adım adım yönergeler için bkz. [güvenlik bilgilerini bir Authenticator uygulaması kullanmak Için ayarlama](security-info-setup-auth-app.md).

- **Mobil cihaz metni.** Mobil cihaz numaranızı girin ve iki adımlı doğrulama veya parola sıfırlama için kullanacağınız kodu bir metin alın. Bir SMS mesajı (SMS) ile kimliğinizi doğrulamaya yönelik adım adım yönergeler için bkz. [güvenlik bilgilerini ayarlama hakkında bilgi iletisi (SMS)](security-info-setup-text-msg.md).

- **Mobil cihaz veya iş telefonu çağrısı.** Mobil cihaz numaranızı girin ve iki adımlı doğrulama veya parola sıfırlama için bir telefon araması alın. Telefon numarası ile kimliğinizi doğrulama hakkında adım adım yönergeler için bkz. [telefon aramalarını kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-phone-number.md).

- **Güvenlik anahtarı.** Microsoft uyumlu güvenlik anahtarınızı kaydedin ve iki adımlı doğrulama veya parola sıfırlama için PIN ile birlikte kullanın. Bir güvenlik anahtarı ile kimliğinizi doğrulama hakkında adım adım yönergeler için bkz. Güvenlik [anahtarı kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-security-key.md).

- **Güvenlik soruları.** Yöneticiniz tarafından kuruluşunuz için oluşturulan bazı güvenlik sorularını yanıtlayın. Bu seçenek yalnızca parola sıfırlama için kullanılabilir ve iki adımlı doğrulama için kullanılamaz. Güvenlik sorularınızı ayarlama hakkında adım adım yönergeler için Güvenlik [sorularını kullanmak üzere güvenlik bilgilerini ayarlama](security-info-setup-questions.md) makalesine bakın.

    >[!Note]
    >Bu seçeneklerden bazıları eksikse, kuruluşunuzun bu yöntemlere izin vermediği için büyük olasılıkla olasıdır. Bu durumda, daha fazla yardım için kullanılabilir bir yöntem seçmeniz veya yöneticinize başvurmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- Parola [sıfırlama portalından](https://passwordreset.microsoftonline.com/) kayıp veya unuttuysanız parolanızı sıfırlayın veya [iş veya okul parolanızı sıfırlama](active-directory-passwords-update-your-own-password.md) makalesindeki adımları izleyin.

- [Microsoft hesabı makalesinde oturum açma](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) sorunları için sorun giderme ipuçları ve yardım alın.
