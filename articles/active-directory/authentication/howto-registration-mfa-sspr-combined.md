---
title: Birleşik güvenlik bilgileri kaydını etkinleştirin-Azure Active Directory
description: Birleşik Azure AD Multi-Factor Authentication ve self servis parola sıfırlama kaydı ile son kullanıcı deneyimini nasıl basitleştireceğinizi öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6eba5ac4ed61847596e12f56544e6d07dca8075
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829584"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Azure Active Directory 'da Birleşik güvenlik bilgileri kaydını etkinleştirme

Birleşik kayıt öncesinde, kullanıcılar Azure AD Multi-Factor Authentication ve self servis parola sıfırlama (SSPR) için kimlik doğrulama yöntemlerini ayrı olarak kaydetti. Kullanıcılar, Azure AD Multi-Factor Authentication ve SSPR için benzer yöntemlerin kullanıldığını, ancak her iki özelliğe de kaydolmaları gerektiğini karıştı. Artık, birleştirilmiş kayıt ile kullanıcılar bir kez kayıt yapabilir ve hem Azure AD Multi-Factor Authentication hem de SSPR 'nin avantajlarını alabilir.

> [!NOTE]
> 15 Ağustos 2020 ' den itibaren, tüm yeni Azure AD kiracılar Birleşik kayıt için otomatik olarak etkinleştirilir. Bu tarihten sonra oluşturulan kiracılar, eski kayıt iş akışlarından yararlanamaz.

Yeni deneyimi etkinleştirmeden önce işlevselliği ve etkileri anladığınızdan emin olmak için, [Birleşik güvenlik bilgileri kayıt kavramlarını](concept-registration-mfa-sspr-combined.md)inceleyin.

![Birleşik güvenlik bilgileri kaydı gelişmiş deneyimi](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Birleşik kaydı etkinleştirme

Birleşik kaydı etkinleştirmek için şu adımları izleyin:

1. Azure portal bir Kullanıcı Yöneticisi veya genel yönetici olarak oturum açın.
2. **Azure Active Directory**  >  **Kullanıcı ayarları**  >  **Kullanıcı özelliği Önizleme ayarlarını yönet**' e gidin.
3. Kullanıcılar ' ın altında, **birleştirilmiş güvenlik bilgileri kayıt deneyimini kullanabilir**, **Seçili** bir Kullanıcı grubu veya **Tüm** kullanıcılar için etkinleştirmeyi seçebilirsiniz.

   ![Kullanıcılar için Birleşik güvenlik bilgileri deneyimini etkinleştir](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Birleşik kaydı etkinleştirdikten sonra, bu yöntemler Azure AD Multi-Factor Authentication ve SSPR ilkelerinde etkinleştirilmişse, telefon numaralarını veya mobil uygulamaları yeni deneyim aracılığıyla kaydeden veya onaylayan kullanıcılar bunları Azure AD Multi-Factor Authentication ve SSPR için kullanabilir.
>
> Daha sonra bu deneyimi devre dışı bırakırsanız, önceki SSPR kayıt sayfasına `https://aka.ms/ssprsetup` gittikleri kullanıcıların sayfaya erişebilmeleri için önce Multi-Factor Authentication gerçekleştirmesi gerekir.

Internet Explorer 'da *siteden bölge atama listesini* yapılandırdıysanız, aşağıdaki sitelerin aynı bölgede olması gerekir:

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Birleşik kayıt için koşullu erişim ilkeleri

Kullanıcıların Azure AD Multi-Factor Authentication ve self servis parola sıfırlama için ne zaman ve nasıl kaydolacağı konusunda güvenli hale getirmek için, koşullu erişim ilkesinde kullanıcı eylemlerini kullanabilirsiniz. Bu işlev, kullanıcıların Azure AD Multi-Factor Authentication ve SSPR 'ye, HR ekleme sırasında güvenilen bir ağ konumu gibi bir merkezi konumdan kaydolmasına olanak tanıyan kuruluşlarda etkinleştirilebilir.

> [!NOTE]
> Bu ilke yalnızca bir Kullanıcı Birleşik bir kayıt sayfasına eriştiğinde geçerlidir. Bu ilke, bir Kullanıcı başka uygulamalara eriştiğinde MFA kaydını zorlamaz.
>
> Azure Identity Protection 'ı kullanarak MFA kayıt ilkesi oluşturabilirsiniz [-MFA Ilkesini yapılandırın](../identity-protection/howto-identity-protection-configure-mfa-policy.md).

Koşullu erişimde güvenilen konumlar oluşturma hakkında daha fazla bilgi için bkz. [Azure Active Directory Koşullu erişim içindeki konum koşulu nedir?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Güvenilen bir konumdan kayıt gerektirecek bir ilke oluşturun

Birleşik kayıt deneyimini kullanarak kaydolmayı deneyen tüm seçili kullanıcılara uygulanan bir ilke oluşturmak için aşağıdaki adımları uygulayın ve güvenilen ağ olarak işaretlenen bir konumdan bağlanmadıkları sürece erişimi engeller:

1. **Azure Portal** **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**' ne gidin.
1. **+ Yeni ilke**' yi seçin.
1. Bu ilke için *Güvenilen ağlarda Birleşik güvenlik bilgileri kaydı* gibi bir ad girin.
1. **Atamalar** altında **Kullanıcılar ve gruplar**’ı seçin. Bu ilkenin uygulanmasını istediğiniz kullanıcıları ve grupları seçin, sonra **bitti**' yi seçin.

   > [!WARNING]
   > Kullanıcıların Birleşik kayıt için etkinleştirilmesi gerekir.

1. **Bulut uygulamaları veya eylemler** altında **Kullanıcı eylemleri**' ni seçin. **Güvenlik bilgilerini kaydet**' i denetleyip **bitti**' yi seçin.

    ![Güvenlik bilgileri kaydını denetlemek için koşullu erişim ilkesi oluşturma](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. **Koşullar**  >  **konumlar** bölümünde, aşağıdaki seçenekleri yapılandırın:
   1. **Evet 'i** yapılandırın.
   1. **Herhangi bir konum** ekleyin.
   1. **Tüm güvenilen konumları** hariç tutun.
1. *Konumlar* penceresinde **bitti** ' yi seçin ve ardından *koşullar* penceresinde **bitti** ' yi seçin.
1. **Erişim denetimleri**  >  **izni** altında, **erişimi engelle**' yi seçin ve ardından öğesini **seçin**.
1. **İlkeyi etkinleştir**’i **Açık** duruma getirin.
1. İlkeyi sonlandırmak için **Oluştur**' u seçin.

## <a name="next-steps"></a>Sonraki adımlar

Yardıma ihtiyacınız varsa bkz. [Birleşik güvenlik bilgileri kaydı sorunlarını giderme](howto-registration-mfa-sspr-combined-troubleshoot.md) veya [Azure AD koşullu erişim 'de konum koşulunu neye](../conditional-access/location-condition.md) göre öğrenin?

Kullanıcılar Birleşik kayıt için etkinleştirildikten sonra [self servis parola sıfırlamayı etkinleştirebilir](tutorial-enable-sspr.md) ve [Azure AD Multi-Factor Authentication etkinleştirebilirsiniz](tutorial-enable-azure-mfa.md).

Gerekirse, [Kullanıcıları kimlik doğrulama yöntemlerini yeniden kaydetmeye zorlama](howto-mfa-userdevicesettings.md#manage-user-authentication-options)hakkında bilgi edinin.
