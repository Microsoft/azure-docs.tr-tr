---
title: Self servis parola sıfırlama-Azure Active Directory özelleştirme
description: Azure AD self servis parola sıfırlama için Kullanıcı görüntüleme ve deneyim seçeneklerini özelleştirmeyi öğrenin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3766db0d5070f15216ac612353e2c25737ad092a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741635"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Azure Active Directory self servis parola sıfırlama için Kullanıcı deneyimini özelleştirme

Self servis parola sıfırlama (SSPR), Azure Active Directory kullanıcılara (Azure AD), yönetici veya yardım masası katılımı olmadan parolasını değiştirme veya sıfırlama yeteneği verir. Bir kullanıcının hesabı kilitliyse veya parolalarını unutduklarında, kendi kendilerini engellemeyi kaldırmak ve çalışmaya geri dönmek için istemleri izleyebilir. Bu özellik, bir Kullanıcı cihazlarındaki veya bir uygulamada oturum açarken yardım masası çağrılarını ve üretkenlik kaybını azaltır.

Kullanıcılar için SSPR deneyimini geliştirmek için parola sıfırlama sayfasının, e-posta bildirimlerinin veya oturum açma sayfalarının görünümünü özelleştirebilirsiniz. Bu özelleştirme seçenekleri, doğru yerde oldukları kullanıcıya açık hale getirir ve bunlara şirket kaynaklarına eriştiği konusunda size güven verir.
    
Bu makalede, kullanıcılar, Şirket markası ve AD FS oturum açma sayfası bağlantısı için SSPR e-posta bağlantısını nasıl özelleştireceğiniz gösterilmektedir.

## <a name="customize-the-contact-your-administrator-link"></a>"Yöneticinize başvurun" bağlantısını özelleştirme

Kullanıcıların Self servis parola sıfırlama konusunda yardım almak için parola sıfırlama portalında bir "yöneticinize başvurun" bağlantısı gösterilir. Bir Kullanıcı bu bağlantıyı seçerse, iki işlemlerden birini yapar:

* Bu iletişim bağlantısı varsayılan durumda bırakılırsa, yöneticilerinize bir e-posta gönderilir ve kullanıcının parolasını değiştirme konusunda yardım sağlamasını ister. Aşağıdaki örnek e-postada bu varsayılan e-posta iletisi gösterilmektedir:

    ![Yöneticiye gönderilen e-postayı sıfırlamaya yönelik örnek istek](./media/howto-sspr-customization/sspr-contact-admin.png)

* Özelleştirildiyse, kullanıcıyı yardım için yönetici tarafından belirtilen bir Web sayfasına veya e-posta adresine gönderir.
    * Bunu özelleştirirseniz, bunu kullanıcıların destek için zaten alışık olduğu bir şey olarak ayarlamamız önerilir.

    > [!WARNING]
    > Bu ayarı, parola sıfırlama gerektiren bir e-posta adresi ve hesap ile özelleştirirseniz, Kullanıcı yardım istememeyebilir.

### <a name="default-email-behavior"></a>Varsayılan e-posta davranışı

Varsayılan iletişim e-postası alıcılara aşağıdaki sırayla gönderilir:

1. *Yardım Masası yönetici* rolü veya *parola yönetici* rolü atanırsa, bu rollere sahip yöneticiler bilgilendirilir.
1. Yardım Masası Yöneticisi veya parola Yöneticisi atanmamışsa, *Kullanıcı Yöneticisi* rolüne sahip yöneticilere bildirilir.
1. Önceki rollerden Hiçbiri atanmamışsa *Genel Yöneticiler* bilgilendirilir.

Her durumda, en fazla 100 alıcı bilgilendirilir.

Farklı yönetici rolleri ve bunların nasıl atanacağı hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory yönetici rolleri atama](../roles/permissions-reference.md).

### <a name="disable-contact-your-administrator-emails"></a>"Yöneticinize başvurun" e-postaları devre dışı bırakın

Kuruluşunuz yöneticilere parola sıfırlama istekleri hakkında bildirim almak istemiyor aşağıdaki yapılandırma seçenekleri kullanılabilir:

* Kullanıcıların yardım almak için kullanabileceği bir Web URL 'SI veya mailto: adresi sağlamak için yardım masası bağlantısını özelleştirin. Bu seçenek **parola sıfırlama**  >  **özelleştirmesi**  >  **özel yardım masası e-postası veya URL 'si** altındadır.
* Tüm kullanıcılar için self servis parola sıfırlamayı etkinleştirin. Bu seçenek **parola sıfırlama**  >  **özellikleri**' nin altındadır. Kullanıcıların kendi parolalarını sıfırlamalarını istemiyorsanız boş bir gruba erişim kapsamını atayabilirsiniz. *Bu seçeneği önermiyoruz.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>Oturum açma sayfası ve erişim panelini özelleştirme

Şirket markasına uyan görüntüyle birlikte görünen bir logo eklemek gibi oturum açma sayfasını özelleştirebilirsiniz. Şirket markasını yapılandırma hakkında daha fazla bilgi için bkz. [Azure AD 'de oturum açma sayfanıza Şirket markası ekleme](../fundamentals/customize-branding.md).

Seçtiğiniz grafikler aşağıdaki durumlarda gösterilmektedir:

* Kullanıcı Kullanıcı adını girdikten sonra
* Kullanıcı özelleştirilmiş URL 'ye eriştiğinde:
   * `whr`Parametresini parola sıfırlama sayfasına geçirerek, örneğin`https://login.microsoftonline.com/?whr=contoso.com`
   * `username`Parametresini parola sıfırlama sayfasına geçirerek, örneğin`https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Dizin adı

İşleri daha kolay bir şekilde görmek için portalda ve otomatikleştirilmiş iletişimlerdeki kuruluş adını değiştirebilirsiniz. Azure Portal dizin adı özniteliğini değiştirmek için **Azure Active Directory**  >  **özelliklerine** gidin. Bu kolay kuruluş adı seçeneği, aşağıdaki örneklerde olduğu gibi otomatik e-postalarda en iyi şekilde görülebilir:

* E-postadaki kolay ad (örneğin, "*contoso tanıtımı adına Microsoft*")
* E-postadaki konu satırı, örneğin "*contoso demo hesabı e-posta doğrulama kodu*"

## <a name="customize-the-ad-fs-sign-in-page"></a>AD FS oturum açma sayfasını özelleştirme

Kullanıcı oturum açma olayları için Active Directory Federasyon Hizmetleri (AD FS) (AD FS) kullanırsanız, oturum açma sayfası [açıklaması eklemek](/windows-server/identity/ad-fs/operations/add-sign-in-page-description)için makalesindeki Kılavuzu kullanarak oturum açma sayfasına bir bağlantı ekleyebilirsiniz.

Kullanıcılara, gibi SSPR iş akışını girmesi için sayfaya bir bağlantı sağlayın *https://passwordreset.microsoftonline.com* . AD FS oturum açma sayfasına bağlantı eklemek için AD FS sunucunuzda aşağıdaki komutu kullanın:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Sonraki adımlar

Ortamınızdaki SSPR kullanımını anlamak için bkz. [Azure AD parola yönetimi Için raporlama seçenekleri](howto-sspr-reporting.md).

Eğer veya kullanıcılarınızın SSPR ile ilgili sorunları varsa bkz. [self servis parola sıfırlama sorunlarını giderme](./troubleshoot-sspr.md)