---
title: Doğrulama yönteminiz olarak güvenlik sorularını ayarlama-Azure AD
description: Doğrulama yönteminiz olarak önceden tanımlanmış güvenlik sorularını kullanarak kimliğinizi doğrulamak için güvenlik bilgileriniz (Önizleme) sayfasını ayarlama.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 0c55f15d12ddc51573f59f56c742a8cac525af0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83744427"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Doğrulama yönteminiz olarak güvenlik sorularını ayarlama

Parola sıfırlama yönteminizi eklemek için bu adımları takip edebilirsiniz. İlk kez ayarladıktan sonra güvenlik bilgilerini eklemek, güncelleştirmek veya silmek için **güvenlik** bilgileri sayfasına dönebilirsiniz.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Güvenlik soruları seçeneğini görmüyorsanız, kuruluşunuz parola sıfırlama yönteminiz için bu seçeneği kullanmanıza izin vermiyor olabilir. Bu durumda, daha fazla yardım için başka bir yöntem seçmeniz veya kuruluşunuzun yardım masasına başvurmanız gerekir.

## <a name="security-verification-versus-password-reset-authentication"></a>Güvenlik doğrulaması ve parola sıfırlama kimlik doğrulaması

Güvenlik bilgileri yöntemleri hem iki öğeli güvenlik doğrulaması hem de parola sıfırlama için kullanılır. Ancak, her ikisi için tüm yöntemler kullanılamaz.

| Yöntem | Kullanıldığı yerler |
| ------ | -------- |
| Authenticator uygulaması | İki öğeli doğrulama ve parola sıfırlama kimlik doğrulaması. |
| SMS mesajları | İki öğeli doğrulama ve parola sıfırlama kimlik doğrulaması. |
| Telefon aramaları | İki öğeli doğrulama ve parola sıfırlama kimlik doğrulaması. |
| Güvenlik anahtarı | İki öğeli doğrulama ve parola sıfırlama kimlik doğrulaması. |
| E-posta hesabı | Yalnızca parola sıfırlama kimlik doğrulaması. İki öğeli doğrulama için başka bir yöntem seçmeniz gerekir. |
| Güvenlik soruları | Yalnızca parola sıfırlama kimlik doğrulaması. İki öğeli doğrulama için başka bir yöntem seçmeniz gerekir. |

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Güvenlik bilgileri sayfasından güvenlik sorularınızı ayarlayın

Kuruluşunuzun ayarlarına bağlı olarak, güvenlik bilgileri yöntemlerinizin biri olarak birkaç güvenlik sorusu seçebilir ve yanıt verebilir. Yöneticiniz, seçmek ve yanıtlamak için gereken güvenlik sorusu sayısını ayarlar.

Güvenlik sorularını kullanıyorsanız, bunları başka bir yöntemle birlikte kullanmanızı öneririz. Güvenlik soruları, bazı kişiler sorularınızın yanıtlarını bilebileceğinden diğer yöntemlerden daha az güvenli olabilir.

> [!Note]
> Güvenlik soruları, dizindeki bir kullanıcı nesnesi üzerinde özel olarak ve güvenli bir şekilde depolanır ve yalnızca kayıt sırasında yanıtlanabilecek. Yöneticinizin sorularınızı veya yanıtlarınızı okuması veya değiştirmesi için bir yol yoktur.
>
> Güvenlik soruları seçeneğini görmüyorsanız, kuruluşunuz doğrulama için güvenlik sorularını kullanmanıza izin vermiyor olabilir. Bu durumda başka bir yöntem seçmeniz veya daha fazla yardım almak için yöneticinize başvurmanız gerekir.
>
> Yönetici hesaplarının, bir parola sıfırlama yöntemi olarak güvenlik sorularını kullanmasına izin verilmez. Yönetici düzeyi hesap olarak oturum açtıysanız, bu seçenekleri görmezsiniz.

### <a name="to-set-up-your-security-questions"></a>Güvenlik sorularınızı ayarlamak için

1. İş veya okul hesabınızda oturum açın ve ardından https://myaccount.microsoft.com/ sayfanıza gidin.

    ![, Vurgulanan güvenlik bilgisi bağlantılarını gösteren profilim sayfası](media/security-info/securityinfo-myprofile-page.png)

2. Sol gezinti bölmesinden veya **güvenlik bilgileri** bloğundaki bağlantıdan **güvenlik bilgileri** ' ni seçin ve ardından **güvenlik bilgileri** sayfasından **Yöntem Ekle** ' yi seçin.

    ![Vurgulanan yöntem ekleme seçeneğiyle güvenlik bilgileri sayfası](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **Yöntem Ekle** sayfasında, açılan listeden **güvenlik soruları** ' nı seçin ve ardından **Ekle**' yi seçin.

    ![Güvenlik soruları seçiliyken Yöntem kutusu Ekle](media/security-info/securityinfo-myprofile-addquestions.png)

4. **Güvenlik soruları** sayfasında Güvenlik sorularınızı seçin ve yanıtlayın ve ardından **Kaydet**' i seçin.

    ![Telefon numarası ekleyin ve telefon görüşmeleri seçin](media/security-info/securityinfo-myprofile-securityquestions.png)

    Güvenlik bilgileriniz güncelleştirildi ve parola sıfırlama kullanırken kimliğinizi doğrulamak için güvenlik sorularınızı kullanabilirsiniz.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Güvenlik bilgileri yöntemlerinizin güvenlik sorularını silme

Güvenlik sorularınızı artık güvenlik bilgileri yöntemi olarak kullanmak istemiyorsanız, bunları **güvenlik bilgileri** sayfasından kaldırabilirsiniz.

>[!Important]
>Güvenlik sorularınızı yanlışlıkla silerseniz, geri almanın bir yolu yoktur. Bu makalenin [güvenlik sorularını ayarlama](#set-up-your-security-questions-from-the-security-info-page) bölümündeki adımları izleyerek yöntemi tekrar eklemeniz gerekir.

### <a name="to-delete-your-security-questions"></a>Güvenlik sorularınızı silmek için

1. **Güvenlik bilgileri** sayfasında **güvenlik soruları** seçeneğinin yanındaki **Sil** bağlantısını seçin.

    ![Güvenlik bilgileri 'nden telefon yöntemini silmeye yönelik bağlantı](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. **Güvenlik sorularınızı** silmek için onay kutusundan **Evet** ' i seçin. Güvenlik sorularınızı sildikten sonra yöntem güvenlik bilgilerinizden kaldırılır ve **güvenlik bilgileri** sayfasından kaybolur.

## <a name="additional-security-info-methods"></a>Ek güvenlik bilgileri yöntemleri

Kuruluşunuzun, kimliğinizi doğrulamak için size ne olduğuna bağlı olarak kimliğinizi nasıl öğrendiğini öğrenmek için ek seçenekleriniz vardır. Seçeneklere şunlar dahildir:

- **Authenticator uygulaması.** İki adımlı doğrulama veya parola sıfırlama için bir onay bildirimi ya da rastgele oluşturulmuş bir onay kodu almak üzere bir Authenticator uygulaması indirin ve kullanın. Microsoft Authenticator uygulamasını ayarlama ve kullanma hakkında adım adım yönergeler için bkz. [güvenlik bilgilerini bir Authenticator uygulaması kullanmak Için ayarlama](security-info-setup-auth-app.md).

- **Mobil cihaz metni.** Mobil cihaz numaranızı girin ve iki adımlı doğrulama veya parola sıfırlama için kullanacağınız kodu bir metin alın. Bir SMS mesajı (SMS) ile kimliğinizi doğrulamaya yönelik adım adım yönergeler için bkz. [güvenlik bilgilerini ayarlama hakkında bilgi iletisi (SMS)](security-info-setup-text-msg.md).

- **Mobil cihaz veya iş telefonu çağrısı.** Mobil cihaz numaranızı girin ve iki adımlı doğrulama veya parola sıfırlama için bir telefon araması alın. Telefon numarası ile kimliğinizi doğrulama hakkında adım adım yönergeler için bkz. [telefon aramalarını kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-phone-number.md).

- **Güvenlik anahtarı.** Microsoft uyumlu güvenlik anahtarınızı kaydedin ve iki adımlı doğrulama veya parola sıfırlama için PIN ile birlikte kullanın. Bir güvenlik anahtarı ile kimliğinizi doğrulama hakkında adım adım yönergeler için bkz. Güvenlik [anahtarı kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-security-key.md).

- **E-posta adresi.** Parola sıfırlama için bir e-posta almak üzere iş veya okul e-posta adresinizi girin. Bu seçenek, iki adımlı doğrulama için kullanılamaz. E-postanızı ayarlama hakkında adım adım yönergeler için bkz. [e-posta kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-email.md).

    >[!Note]
    >Bu seçeneklerden bazıları eksikse, kuruluşunuzun bu yöntemlere izin vermediği için büyük olasılıkla olasıdır. Bu durumda, daha fazla yardım için kullanılabilir bir yöntem seçmeniz veya yöneticinize başvurmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- Parola [sıfırlama portalından](https://passwordreset.microsoftonline.com/) kayıp veya unuttuysanız parolanızı sıfırlayın veya [iş veya okul parolanızı sıfırlama](active-directory-passwords-update-your-own-password.md) makalesindeki adımları izleyin.

- [Microsoft hesabı makalesinde oturum açma](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) sorunları için sorun giderme ipuçları ve yardım alın.
