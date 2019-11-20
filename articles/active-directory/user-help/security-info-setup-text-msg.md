---
title: Metin iletileri için güvenlik bilgilerini ayarlama (Önizleme)-Azure AD
description: Metin mesajlaşma ve mobil cihazınızı kullanarak kimliğinizi doğrulamak için güvenlik bilgilerinizi ayarlama.
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
ms.openlocfilehash: 5a4be99a84964b7a55448692385d027262ecfec3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73803832"
---
# <a name="set-up-security-info-preview-to-use-text-messaging"></a>Metin iletilerini kullanmak için güvenlik bilgilerini ayarlama (Önizleme)

İki öğeli doğrulama ve parola sıfırlama yöntemlerinizi eklemek için aşağıdaki adımları izleyebilirsiniz. İlk kez ayarladıktan sonra güvenlik bilgilerini eklemek, güncelleştirmek veya silmek için **güvenlik** bilgileri sayfasına dönebilirsiniz.

Bunu iş veya okul hesabınızda oturum açtıktan hemen sonra ayarlamanız istenirse, [oturum açma sayfası istemi makalesindeki güvenlik bilgilerinizi ayarlama](security-info-setup-signin.md) makalesindeki ayrıntılı adımlara bakın.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Telefon seçeneği görmüyorsanız, kuruluşunuz doğrulama için bir telefon numarası kullanmanıza izin vermiyor olabilir. Bu durumda, daha fazla yardım için başka bir yöntem seçmeniz veya yöneticinize başvurmanız gerekir.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Güvenlik bilgileri sayfasından metin iletilerini ayarlama

Kuruluşunuzun ayarlarına bağlı olarak, metin mesajlaşma 'yı güvenlik bilgileri yöntemlerinizin biri olarak kullanabilirsiniz. Kısa mesaj seçeneği telefon seçeneğinin bir parçasıdır, bu nedenle her şeyi telefon numaranız için yaptığınız şekilde ayarlayacaksınız, ancak Microsoft 'un sizi aramasını sağlamak yerine bir kısa mesaj kullanacağınızı tercih edersiniz.

>[!Note]
>Kısa mesaj yerine bir telefon araması almak istiyorsanız, [telefon aramalarını kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-phone-number.md) makalesini izleyin.

### <a name="to-set-up-text-messages"></a>Metin iletilerini ayarlamak için

1. İş veya okul hesabınızda oturum açın ve ardından https://myprofile.microsoft.com/ sayfanıza gidin.

    ![, Vurgulanan güvenlik bilgisi bağlantılarını gösteren profilim sayfası](media/security-info/securityinfo-myprofile-page.png)

2. Sol gezinti bölmesinden veya **güvenlik bilgileri** bloğundaki bağlantıdan **güvenlik bilgileri** ' ni seçin ve ardından **güvenlik bilgileri** sayfasından **Yöntem Ekle** ' yi seçin.

    ![Vurgulanan yöntem ekleme seçeneğiyle güvenlik bilgileri sayfası](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **Yöntem Ekle** sayfasında, açılan listeden **Telefon** ' i seçin ve ardından **Ekle**' yi seçin.

    ![Telefon seçiliyken Yöntem kutusu Ekle](media/security-info/securityinfo-myprofile-addphonetext.png)

4. **Telefon** sayfasında, mobil cihazınızın telefon numarasını yazın, **bir kod gönder**' i seçin ve ardından **İleri**' yi seçin.

    ![Telefon numarası Ekle ve metin iletilerini Seç](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Mobil cihazınıza SMS mesajı aracılığıyla size gönderilen kodu yazın ve ardından **İleri**' yi seçin.

    ![Telefon numarası Ekle ve metin iletilerini Seç](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    Sayfa, başarınızı gösterecek şekilde değişir.

    ![Başarı bildirimi, telefon numarasını bağlama, SMS mesajı alma seçeneği ve hesabınız](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Güvenlik bilgileriniz güncelleştirildi ve iki adımlı doğrulama veya parola sıfırlama kullanırken kimliğinizi doğrulamak için SMS mesajı 'nı kullanabilirsiniz. Varsayılan yönteminizin metin mesajı vermek istiyorsanız, bu makalenin [varsayılan güvenlik bilgileri yönteminizi değiştirme](#change-your-default-security-info-method) bölümüne bakın.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Güvenlik bilgileri yöntemlerinizin metin iletilerini silme

Artık metin iletilerini bir güvenlik bilgisi yöntemi olarak kullanmak istemiyorsanız, **güvenlik bilgileri** sayfasından kaldırabilirsiniz.

>[!Important]
>Kısa mesajlaşmayı yanlışlıkla silerseniz, geri almanın bir yolu yoktur. Bu makalenin [SMS Iletilerini ayarlama](#set-up-text-messages-from-the-security-info-page) bölümündeki adımları izleyerek yöntemi tekrar eklemeniz gerekir.

### <a name="to-delete-text-messaging"></a>SMS iletilerini silmek için

1. **Güvenlik bilgileri** sayfasında, **Telefon** seçeneğinin yanındaki **Sil** bağlantısını seçin.

    ![Güvenlik bilgileri 'nden telefon ve metin mesajlaşma yöntemini silmeye yönelik bağlantı](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. **Telefon** numarasını silmek için onay kutusundan **Evet** ' i seçin. Telefon numaranız silindikten sonra güvenlik bilgileriniz kaldırılır ve **güvenlik bilgileri** sayfasından kaybolur. **Telefon** varsayılan yönteminiz ise, varsayılan olarak bir kullanılabilir yönteme geçiş yapılır.

## <a name="change-your-default-security-info-method"></a>Varsayılan güvenlik bilgileri yönteminizi değiştirme

İki öğeli doğrulama veya parola sıfırlama istekleri kullanarak iş veya okul hesabınızda oturum açtığınızda, metin mesajlaşma 'nın varsayılan yöntem olmasını istiyorsanız, **güvenlik bilgileri** sayfasından ayarı yapabilirsiniz.

### <a name="to-change-your-default-security-info-method"></a>Varsayılan güvenlik bilgileri yönteminizi değiştirmek için

1. **Güvenlik bilgileri** sayfasında, **varsayılan oturum açma yöntemi** bilgisinin yanındaki bağlantıyı **Değiştir** ' i seçin.

    ![Varsayılan oturum açma yönteminin bağlantısını değiştir](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Kullanılabilir yöntemlerin aşağı açılan listesinden **Telefon metni ( *_your_phone_number_* )** seçeneğini belirleyin ve ardından **Onayla**' yı seçin.

    ![Varsayılan oturum açma yöntemini seçin](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    **Telefon metni ( *_your_phone_number_* )** için oturum açma değişiklikleri için kullanılan varsayılan yöntem.

## <a name="additional-security-info-methods"></a>Ek güvenlik bilgileri yöntemleri

Kuruluşunuzun, kimliğinizi doğrulamak için size ne olduğuna bağlı olarak kimliğinizi nasıl öğrendiğini öğrenmek için ek seçenekleriniz vardır. Seçeneklere şunlar dahildir:

- **Authenticator uygulaması.** İki adımlı doğrulama veya parola sıfırlama için bir onay bildirimi ya da rastgele oluşturulmuş bir onay kodu almak üzere bir Authenticator uygulaması indirin ve kullanın. Microsoft Authenticator uygulamasını ayarlama ve kullanma hakkında adım adım yönergeler için bkz. [güvenlik bilgilerini bir Authenticator uygulaması kullanmak Için ayarlama](security-info-setup-auth-app.md).

- **Mobil cihaz veya iş telefonu çağrısı.** Mobil cihaz numaranızı girin ve iki adımlı doğrulama veya parola sıfırlama için bir telefon araması alın. Telefon numarası ile kimliğinizi doğrulama hakkında adım adım yönergeler için bkz. [telefon aramalarını kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-phone-number.md).

- **Güvenlik anahtarı.** Microsoft uyumlu güvenlik anahtarınızı kaydedin ve iki adımlı doğrulama veya parola sıfırlama için PIN ile birlikte kullanın. Bir güvenlik anahtarı ile kimliğinizi doğrulama hakkında adım adım yönergeler için bkz. Güvenlik [anahtarı kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-security-key.md).

- **E-posta adresi.** Parola sıfırlama için bir e-posta almak üzere iş veya okul e-posta adresinizi girin. Bu seçenek, iki adımlı doğrulama için kullanılamaz. E-postanızı ayarlama hakkında adım adım yönergeler için bkz. [e-posta kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-email.md).

- **Güvenlik soruları.** Yöneticiniz tarafından kuruluşunuz için oluşturulan bazı güvenlik sorularını yanıtlayın. Bu seçenek yalnızca parola sıfırlama için kullanılabilir ve iki adımlı doğrulama için kullanılamaz. Güvenlik sorularınızı ayarlama hakkında adım adım yönergeler için Güvenlik [sorularını kullanmak üzere güvenlik bilgilerini ayarlama](security-info-setup-questions.md) makalesine bakın.

    >[!Note]
    >Bu seçeneklerden bazıları eksikse, kuruluşunuzun bu yöntemlere izin vermediği için büyük olasılıkla olasıdır. Bu durumda, daha fazla yardım için kullanılabilir bir yöntem seçmeniz veya yöneticinize başvurmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- Parola [sıfırlama portalından](https://passwordreset.microsoftonline.com/) kayıp veya unuttuysanız parolanızı sıfırlayın veya [iş veya okul parolanızı sıfırlama](active-directory-passwords-update-your-own-password.md) makalesindeki adımları izleyin.

- [Microsoft hesabı makalesinde oturum açma](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) sorunları için sorun giderme ipuçları ve yardım alın.
