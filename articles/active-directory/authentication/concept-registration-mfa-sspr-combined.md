---
title: SSPR ve Azure AD Multi-Factor Authentication için Birleşik kayıt Azure Active Directory
description: Kullanıcıların hem Azure AD Multi-Factor Authentication hem de Self servis parola sıfırlama için kaydolmasına izin vermek üzere Azure Active Directory için Birleşik kayıt deneyimi hakkında bilgi edinin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 112ad0714c84cd3be08788b3277f52372f6d0373
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938466"
---
# <a name="combined-security-information-registration-for-azure-active-directory-overview"></a>Azure Active Directory genel bakış için Birleşik güvenlik bilgileri kaydı

Birleşik kayıt öncesinde, kullanıcılar Azure AD Multi-Factor Authentication ve self servis parola sıfırlama (SSPR) için kimlik doğrulama yöntemlerini ayrı olarak kaydetti. İnsanlar Multi-Factor Authentication ve SSPR için benzer yöntemlerin kullanıldığını, ancak her iki özelliğe de kaydolmaları gerektiğini karıştı. Artık, birleştirilmiş kayıt ile kullanıcılar bir kez kaydolduktan sonra hem Multi-Factor Authentication hem de SSPR avantajlarından yararlanabilir.

> [!NOTE]
> 15 Ağustos 2020 ' den itibaren, tüm yeni Azure AD kiracılar Birleşik kayıt için otomatik olarak etkinleştirilir. 

Bu makalede, Birleşik güvenlik kaydının ne olduğu özetlenmektedir. Birleşik güvenlik kaydıyla çalışmaya başlamak için aşağıdaki makaleye bakın:

> [!div class="nextstepaction"]
> [Birleşik güvenlik kaydını etkinleştir](howto-registration-mfa-sspr-combined.md)

![Bir kullanıcı için kayıtlı güvenlik bilgilerini gösteren Hesabım](media/concept-registration-mfa-sspr-combined/combined-security-info-defaults-registered.png)

Yeni deneyimi etkinleştirmeden önce, bu özelliğin işlevselliğini ve etkisini anladığınızdan emin olmak için bu yöneticiye odaklanmış belgeleri ve Kullanıcı odaklı belgeleri gözden geçirin. Kullanıcılarınıza yeni deneyim hazırlamak ve başarılı bir dağıtım sağlamaya yardımcı olmak için, [Kullanıcı belgelerine](../user-help/security-info-setup-signin.md) yönelik eğitime dayandırın.

Azure AD Birleşik güvenlik bilgileri kaydı, Azure Almanya veya Azure Çin 21Vianet gibi ulusal bulutlar için şu anda kullanılamıyor. Azure ABD kamu için kullanılabilir.

> [!IMPORTANT]
> Hem özgün önizleme hem de geliştirilmiş Birleşik kayıt deneyimi için etkinleştirilen kullanıcılar yeni davranışı görür. Her iki deneyim için etkinleştirilen kullanıcılar yalnızca Hesabım deneyimidir. Hesabım Birleşik kaydın görünümü ve hissi *ile hizalanır ve* kullanıcılar için sorunsuz bir deneyim sağlar. Kullanıcılar hesabımı ' a giderek görebilir [https://myaccount.microsoft.com](https://myaccount.microsoft.com) .
>
> "Üzgünüz, oturumunuzu açamıyoruz" gibi güvenlik bilgisi seçeneğine erişmeye çalışırken bir hata iletisiyle karşılaşabilirsiniz. Web tarayıcısında üçüncü taraf tanımlama bilgilerini engelleyen bir yapılandırma veya Grup İlkesi nesneniz olmadığından emin olun.

*Hesabım Sayfalarım* , sayfaya erişen bilgisayarın dil ayarlarına bağlı olarak yerelleştirilir. Microsoft, tarayıcı önbelleğinde kullanılan en son dili depolar, böylece sonraki sayfalara erişim girişimleri kullanılan son dilde işlemeye devam eder. Önbelleği temizlerseniz, sayfalar yeniden işlenir.

Belirli bir dili zorlamak istiyorsanız `?lng=<language>` URL 'nin sonuna ekleyebilirsiniz; burada, `<language>` işlemek istediğiniz dilin kodudur.

![SSPR veya diğer güvenlik doğrulama yöntemlerini ayarlama](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Birleşik kayıtta kullanılabilen Yöntemler

Birleşik kayıt aşağıdaki kimlik doğrulama yöntemlerini ve eylemleri destekler:

| Yöntem | Kaydol | Değiştir | Sil |
| --- | --- | --- | --- |
| Microsoft Authenticator | Evet (en fazla 5) | Hayır | Yes |
| Diğer kimlik doğrulayıcı uygulaması | Evet (en fazla 5) | Hayır | Yes |
| Donanım belirteci | Hayır | Hayır | Yes |
| Telefon | Yes | Yes | Yes |
| Alternatif telefon | Yes | Yes | Yes |
| Ofis telefonu | Yes | Yes | Yes |
| E-posta | Yes | Yes | Yes |
| Güvenlik soruları | Yes | Hayır | Yes |
| Uygulama parolaları | Yes | Hayır | Yes |
| FIDO2 güvenlik anahtarları<br />*Yalnızca [güvenlik bilgileri](https://mysignins.microsoft.com/security-info) sayfasından yönetilen mod*| Yes | Yes | Yes |

> [!NOTE]
> Uygulama parolaları yalnızca Multi-Factor Authentication için zorlanan kullanıcılar tarafından kullanılabilir. Uygulama parolaları, koşullu erişim ilkesi aracılığıyla Multi-Factor Authentication için etkinleştirilen kullanıcılar tarafından kullanılamaz.

Kullanıcılar aşağıdaki seçeneklerden birini varsayılan Multi-Factor Authentication yöntemi olarak ayarlayabilir:

- Microsoft Authenticator – bildirimi.
- Doğrulayıcı uygulaması veya donanım belirteci – kod.
- Telefon çağrısı.
- Kısa mesaj.

Azure AD 'ye daha fazla kimlik doğrulama yöntemi eklemeye devam ediyoruz, bu yöntemler Birleşik kayıtta kullanılabilir.

## <a name="combined-registration-modes"></a>Birleşik Kayıt modları

İki Birleşik kayıt modu vardır: kesme ve yönetme.

- **Kesme modu** , oturum açma sırasında güvenlik bilgilerini kaydederken veya yenilediklerinde kullanıcılara sunulan sihirbaza benzer bir deneyimdir.
- **Yönetim modu** Kullanıcı profilinin bir parçasıdır ve kullanıcıların güvenlik bilgilerini yönetmesine olanak tanır.

Her iki mod için, daha önce Multi-Factor Authentication için kullanılabilen ve güvenlik bilgilerine erişmeden önce Multi-Factor Authentication gerçekleştirmesi gereken bir yöntemi kaydetmiş olan kullanıcılar. Kullanıcıların, önceden kaydedilmiş yöntemlerini kullanmaya devam etmeden önce bilgilerini onaylamasını gerekir. 

### <a name="interrupt-mode"></a>Kesme modu

Her ikisi de kiracınız için etkinse, birleştirilmiş kayıt hem Multi-Factor Authentication hem de SSPR ilkelerine uyar. Bu ilkeler, kullanıcının oturum açma sırasında kayıt için kesintiye uğratılmadığını ve kayıt için hangi yöntemlerin kullanılabildiğini denetler.

Aşağıdakiler, kullanıcıların güvenlik bilgilerini kaydetmesi veya yenilememiz gerekebilecek örnek senaryolardır:

- *Kimlik koruması aracılığıyla Multi-Factor Authentication kaydı zorlandı:* Kullanıcıların oturum açma sırasında kaydolması istenir. Multi-Factor Authentication yöntemleri ve SSPR yöntemlerini (Kullanıcı SSPR için etkinleştirildiyse) kaydeder.
- *Kullanıcı başına Multi-Factor Authentication üzerinden Multi-Factor Authentication kaydı zorlandı:* Kullanıcıların oturum açma sırasında kaydolması istenir. Multi-Factor Authentication yöntemleri ve SSPR yöntemlerini (Kullanıcı SSPR için etkinleştirildiyse) kaydeder.
- *Koşullu erişim veya diğer ilkeler aracılığıyla Multi-Factor Authentication kaydı zorlandı:* Kullanıcılardan, Multi-Factor Authentication gerektiren bir kaynağı kullandıklarında kaydolmaları istenir. Multi-Factor Authentication yöntemleri ve SSPR yöntemlerini (Kullanıcı SSPR için etkinleştirildiyse) kaydeder.
- *SSPR kaydı zorlandı:* Kullanıcıların oturum açma sırasında kaydolması istenir. Bunlar yalnızca SSPR yöntemlerini kaydeder.
- *SSPR yenileme zorlandı:* Kullanıcıların, güvenlik bilgilerini yönetici tarafından ayarlanan bir aralığa incebilmeleri gerekir. Kullanıcılar bilgilerini gösterilir ve gerekirse geçerli bilgileri doğrulayabilirler veya değişiklik yapabilir.

Kayıt zorlandığında, kullanıcılara en az Multi-Factor Authentication ve SSPR ilkeleriyle uyumlu olması için gereken en az sayıda yöntem gösterilir.

Aşağıdaki örnek senaryoyu göz önünde bulundurun:

- Bir Kullanıcı SSPR için etkinleştirilmiştir. SSPR ilkesi, mobil uygulama kodu, e-posta ve telefon 'i sıfırlamak ve etkinleştirmek için iki yöntem gerektirir.
- Bu kullanıcının iki yöntemi kaydetmesi gerekir.
   - Kullanıcı, kimlik doğrulayıcı uygulaması ve telefon varsayılan olarak gösterilir.
   - Kullanıcı, kimlik doğrulayıcı uygulaması veya telefon yerine e-posta kaydetmeyi tercih edebilir.

Aşağıdaki akış çizelgesi, oturum açma sırasında kaydolmak üzere kesintiye uğradığında kullanıcıya hangi yöntemlerin gösterildiğini açıklar:

![Birleşik güvenlik bilgisi akış çizelgesi](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Hem Multi-Factor Authentication hem de SSPR etkinse, Multi-Factor Authentication kayıt uygulanmasını öneririz.

SSPR ilkesi, kullanıcıların güvenlik bilgilerini düzenli aralıklarla incelemesini gerektiriyorsa, oturum açma işlemi sırasında kullanıcılar kesintiye uğrar ve tüm kayıtlı yöntemleri gösterilir. Güncel bilgileri güncel değilse doğrulayabilirler veya gerektiğinde değişiklik yapabilirler. Bu sayfaya erişirken kullanıcıların Multi-Factor Authentication gerçekleştirmesi gerekir.

### <a name="manage-mode"></a>Yönetim modu

Kullanıcılar [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) Hesabım listesinden **güvenlik bilgileri** ' ni seçerek veya ' a giderek, yönetim moduna erişebilir. Kullanıcılar buradan Yöntemler ekleyebilir, var olan yöntemleri silebilir veya değiştirebilir, varsayılan yöntemi değiştirebilir ve daha fazlasını yapabilir.

## <a name="key-usage-scenarios"></a>Anahtar kullanımı senaryoları

### <a name="set-up-security-info-during-sign-in"></a>Oturum açma sırasında güvenlik bilgilerini ayarlama

Yönetici kaydı zorladı.

Bir kullanıcı gerekli tüm güvenlik bilgilerini ayarlamadı ve Azure portal gider. Kullanıcı adını ve parolayı girdikten sonra kullanıcıdan güvenlik bilgilerini ayarlaması istenir. Daha sonra Kullanıcı, gerekli güvenlik bilgilerini ayarlamak için sihirbazda gösterilen adımları izler. Ayarlarınıza izin verirseniz, Kullanıcı varsayılan olarak gösterilenler dışındaki yöntemleri ayarlamayı tercih edebilir. Sihirbaz tamamlandıktan sonra, kullanıcılar ayarladıkları yöntemleri ve Multi-Factor Authentication varsayılan yöntemlerini gözden geçirir. Kurulum işlemini gerçekleştirmek için Kullanıcı bilgileri onaylar ve Azure portal devam eder.

### <a name="set-up-security-info-from-my-account"></a>Hesabımın güvenlik bilgilerini ayarlama

Yönetici kaydı zormadı.

Gerekli güvenlik bilgilerini henüz ayarlamış olan bir Kullanıcı öğesine gider [https://myaccount.microsoft.com](https://myaccount.microsoft.com) . Kullanıcı sol bölmedeki **güvenlik bilgilerini** seçer. Buradan Kullanıcı bir yöntem eklemeyi seçer, kullanılabilir yöntemlerin birini seçer ve bu yöntemi ayarlama adımlarını izler. İşiniz bittiğinde, Kullanıcı güvenlik bilgileri sayfasında ayarlanan yöntemi görür.

### <a name="delete-security-info-from-my-account"></a>Hesabım listesinden güvenlik bilgilerini sil

Daha önce en az bir yöntemi kuran bir Kullanıcı öğesine gider [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Kullanıcı önceden kaydedilmiş yöntemlerden birini silmeyi seçer. İşiniz bittiğinde, Kullanıcı artık güvenlik bilgileri sayfasında bu yöntemi görmediğini.

### <a name="change-the-default-method-from-my-account"></a>Varsayılan yöntemi Hesabım listesinden değiştirme

Daha önce Multi-Factor Authentication için kullanılabilecek en az bir yöntemi ayarlamış olan bir Kullanıcı ' A gider [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Kullanıcı geçerli varsayılan yöntemi farklı bir varsayılan yönteme geçirir. İşiniz bittiğinde, Kullanıcı, güvenlik bilgileri sayfasında yeni varsayılan yöntemi görür.

## <a name="next-steps"></a>Sonraki adımlar

Başlamak için, [self servis parola sıfırlamayı etkinleştirme](tutorial-enable-sspr.md) ve [Azure AD Multi-Factor Authentication etkinleştirme](tutorial-enable-azure-mfa.md)öğreticilerine bakın.

[Kiracınızda Birleşik kaydın nasıl etkinleştirileceğini](howto-registration-mfa-sspr-combined.md) veya [kullanıcılara kimlik doğrulama yöntemlerini yeniden kaydetmeye zorleyeceğinizi](howto-mfa-userdevicesettings.md#manage-user-authentication-options)öğrenin.

Ayrıca, [Azure AD Multi-Factor Authentication ve SSPR için kullanılabilir yöntemleri](concept-authentication-methods.md)inceleyebilirsiniz.
