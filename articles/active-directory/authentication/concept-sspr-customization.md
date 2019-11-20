---
title: Azure AD self servis parola sıfırlama-Azure Active Directory özelleştirme
description: Azure AD self servis parola sıfırlama için özelleştirme seçenekleri
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 527dd99f122ec70cc47305947a5cbce3207b9664
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666310"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Self servis parola sıfırlama için Azure AD işlevselliğini özelleştirme

Azure Active Directory 'de (Azure AD) self servis parola sıfırlama (SSPR) dağıtmak isteyen BT uzmanları, deneyimini kullanıcıların gereksinimleriyle eşleşecek şekilde özelleştirebilir.

## <a name="customize-the-contact-your-administrator-link"></a>"Yöneticinize başvurun" bağlantısını özelleştirme

Self servis parola sıfırlama kullanıcıları, parola sıfırlama portalında kendilerine sunulan "yöneticinize başvurun" bağlantısına sahip olabilir. Bir Kullanıcı bu bağlantıyı seçerse, iki işlemlerden birini yapar:

* Varsayılan durumda bırakılırsa:
   * E-posta, yöneticilerinize gönderilir ve kullanıcının parolasını değiştirme konusunda yardım sağlamasını ister. Aşağıdaki [örnek e-postaya](#sample-email) bakın.
* Özelleştirildiyse:
   * Kullanıcıyı, yardım için yönetici tarafından belirtilen bir Web sayfasına veya e-posta adresine gönderir.

> [!TIP]
> Bunu özelleştirirseniz, bunu, kullanıcıların destek için zaten alışık olduğu bir şey olarak ayarlamamız önerilir

> [!WARNING]
> Bu ayarı, parola sıfırlama gerektiren bir e-posta adresi ve hesap ile özelleştirirseniz, Kullanıcı yardım istememeyebilir.

### <a name="sample-email"></a>Örnek e-posta

![Yöneticiye gönderilen e-postayı sıfırlamaya yönelik örnek istek][Contact]

İletişim e-postası aşağıdaki alıcılara aşağıdaki sırayla gönderilir:

1. **Parola yönetici** rolü atanmışsa, bu role sahip yöneticiler bilgilendirilir.
2. Parola Yöneticisi atanmamışsa, **Kullanıcı Yöneticisi** rolüne sahip yöneticilere bildirilir.
3. Önceki rollerden hiçbiri atanmadıysa, **Genel Yöneticiler** bilgilendirilir.

Her durumda, en fazla 100 alıcı bilgilendirilir.

Farklı yönetici rolleri ve bunların nasıl atanacağı hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory yönetici rolleri atama](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>"Yöneticinize başvurun" e-postaları devre dışı bırakın

Kuruluşunuz yöneticileri parola sıfırlama istekleri hakkında bilgilendirmek istemiyor, aşağıdaki yapılandırmayı etkinleştirebilirsiniz:

* Tüm son kullanıcılar için self servis parola sıfırlamayı etkinleştirin. Bu seçenek **parola sıfırlama** > **özellikleri**' nin altındadır. Kullanıcıların kendi parolalarını sıfırlamalarını istemiyorsanız boş bir gruba erişim kapsamını atayabilirsiniz. *Bu seçeneği önermiyoruz.*
* Kullanıcıların yardım almak için kullanabileceği bir Web URL 'SI veya mailto: adresi sağlamak için yardım masası bağlantısını özelleştirin. Bu seçenek **parola sıfırlama** > **özelleştirmesi** > **özel yardım masası e-postası veya URL 'si**altındadır.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>SSPR için AD FS oturum açma sayfasını özelleştirme

Active Directory Federasyon Hizmetleri (AD FS) (AD FS) yöneticileri, oturum açma sayfası [açıklaması ekle](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) makalesinde bulunan Kılavuzu kullanarak oturum açma sayfasına bir bağlantı ekleyebilir.

AD FS oturum açma sayfasına bağlantı eklemek için AD FS sunucunuzda aşağıdaki komutu kullanın. Kullanıcılar bu sayfayı kullanarak SSPR iş akışını girebilirler.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Oturum açma sayfası ve erişim paneli görünüm görünümünü özelleştirme

Oturum açma sayfasını özelleştirebilirsiniz. Şirket markasına uyan görüntüyle birlikte görünen bir logo ekleyebilirsiniz.

Seçtiğiniz grafikler aşağıdaki durumlarda gösterilmektedir:

* Kullanıcı Kullanıcı adını girdikten sonra
* Kullanıcı özelleştirilmiş URL 'ye eriştiğinde:
   * `whr` Parametresini parola sıfırlama sayfasına geçirerek, örneğin`https://login.microsoftonline.com/?whr=contoso.com`
   * `username` Parametresini parola sıfırlama sayfasına geçirerek, örneğin`https://login.microsoftonline.com/?username=admin@contoso.com`

[Azure AD 'de oturum açma sayfanıza Şirket markası ekleme](../fundamentals/customize-branding.md)makalesindeki şirket markasının nasıl yapılandırılacağı hakkında ayrıntılı bilgi edinin.

### <a name="directory-name"></a>Dizin adı

**Azure Active Directory** > **özellikleri**altında dizin adı özniteliğini değiştirebilirsiniz. Portalda ve otomatik iletişimde görülen kolay bir kuruluş adı gösterebilirsiniz. Bu seçenek, aşağıdaki formlardaki otomatik e-postalarda en iyi şekilde görülebilir:

* E-postadaki kolay ad (örneğin, "CONTOSO tanıtımı adına Microsoft")
* E-postadaki konu satırı, örneğin "CONTOSO demo hesabı e-posta doğrulama kodu"

## <a name="next-steps"></a>Sonraki adımlar

* [SSPR’yi başarılı bir şekilde nasıl piyasaya çıkarabilirim?](howto-sspr-deployment.md)
* [Parolanızı sıfırlama veya değiştirme](../user-help/active-directory-passwords-update-your-own-password.md)
* [Self servis parola sıfırlama için kaydolma](../user-help/active-directory-passwords-reset-register.md)
* [Lisansla ilgili bir sorunuz mu var?](concept-sspr-licensing.md)
* [SSPR hangi verileri kullanır ve kullanıcılarınız için hangi verileri doldurmanız gerekir?](howto-sspr-authenticationdata.md)
* [Kullanıcılar hangi kimlik doğrulama yöntemlerini kullanabilir?](concept-sspr-howitworks.md#authentication-methods)
* [SSPR ile kullanılabilen ilke seçenekleri nelerdir?](concept-sspr-policy.md)
* [Parola geri yazma nedir ve neden önemlidir?](howto-sspr-writeback.md)
* [SSPR’de etkinliği nasıl bildirebilirim?](howto-sspr-reporting.md)
* [SSPR’deki tüm seçenekler nelerdir ve ne anlama gelir?](concept-sspr-howitworks.md)
* [Bir arıza olduğunu düşünüyorum. SSPR’de nasıl sorun giderebilirim?](active-directory-passwords-troubleshoot.md)
* [Başka bir yerde ele alınmayan bir sorum var](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Parola e-posta dosyanızı sıfırlamaya yönelik yardım için yöneticinize başvurun"
