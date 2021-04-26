---
title: Koşullu erişim-Yöneticiler için MFA gerektir-Azure Active Directory
description: Yöneticilerin Multi-Factor Authentication gerçekleştirmesini gerektirmek için özel bir koşullu erişim ilkesi oluşturma
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35178ecc9bc736bbaca3adc932022b15cc2fc956
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632093"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Koşullu erişim: Yöneticiler için MFA gerektir

Yönetici hakları atanmış hesaplar saldırganlar tarafından hedeflenir. Bu hesaplar üzerinde Multi-Factor Authentication (MFA) gerektirme, bu hesapların tehlikeye düşmesi riskini azaltmanın kolay bir yoludur.

Microsoft, en azından aşağıdaki rollerde MFA gerektirmenizi önerir:

* Kimlik doğrulama Yöneticisi
* Faturalama yöneticisi
* Koşullu Erişim Yöneticisi
* Exchange yöneticisi
* Genel yönetici
* Yardım Masası Yöneticisi
* Parola yöneticisi
* Ayrıcalıklı rol yöneticisi
* Güvenlik yöneticisi
* SharePoint yöneticisi
* Kullanıcı yöneticisi

Kuruluşlar, uygun gördüğünüz şekilde rolleri dahil etmek veya hariç tutmak seçebilirler.

## <a name="user-exclusions"></a>Kullanıcı dışlamaları

Koşullu erişim ilkeleri güçlü araçlardır ve ilkenizde aşağıdaki hesapların dışlanmasını öneririz:

* Kiracı genelindeki hesap kilitlenmesini engellemek için **acil durum erişimi** veya **kesme camı** hesapları. Olası olmayan senaryoda tüm yöneticiler kiracınızın dışında kilitlendiğinden, acil durum erişimi yönetim hesabınız kiracıya oturum açmak için kullanılabilir ve erişimi kurtarmak için gerekli adımları uygulayın.
   * Daha fazla bilgi için [Azure AD 'de acil durum erişim hesaplarını yönetme](../roles/security-emergency-access.md)makalesinde bulabilirsiniz.
* Azure AD Connect eşitleme hesabı gibi **hizmet hesapları** ve **hizmet sorumluları**. Hizmet hesapları, belirli bir kullanıcıya bağlı olmayan etkileşimli olmayan hesaplardır. Bunlar normalde, uygulamalar için programlı erişime izin veren, ancak aynı zamanda yönetim amaçlarıyla sistemlerde oturum açmak için kullanılan arka uç hizmetleri tarafından kullanılır. MFA programlı olarak tamamlanmadığı için bunlar gibi hizmet hesapları dışlanmalıdır. Hizmet sorumluları tarafından yapılan çağrılar koşullu erişim tarafından engellenmiyor.
   * Kuruluşunuzun komut dosyalarında veya kodda kullanımda olan bu hesapları varsa, bunları [yönetilen kimliklerle](../managed-identities-azure-resources/overview.md)değiştirmeyi göz önünde bulundurun. Geçici bir çözüm olarak, bu belirli hesapları temel ilkeden hariç bırakabilirsiniz.

## <a name="create-a-conditional-access-policy"></a>Koşullu erişim ilkesi oluşturma

Aşağıdaki adımlar, atanan yönetim rollerinin Multi-Factor Authentication gerçekleştirmesini gerektirmek için bir koşullu erişim ilkesi oluşturmanıza yardımcı olur.

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**'ne gidin.
1. **Yeni ilke**' yi seçin.
1. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
1. **Atamalar** altında **Kullanıcılar ve gruplar** ' ı seçin.
   1. Ekle ' nin altında **Dizin rolleri** ' ni seçin ve **aşağıdakiler** gibi yerleşik roller ' i seçin:
      * Kimlik doğrulama Yöneticisi
      * Faturalama yöneticisi
      * Koşullu Erişim Yöneticisi
      * Exchange yöneticisi
      * Genel yönetici
      * Yardım Masası Yöneticisi
      * Parola yöneticisi
      * Güvenlik yöneticisi
      * SharePoint yöneticisi
      * Kullanıcı yöneticisi
   
      > [!WARNING]
      > Koşullu erişim ilkeleri yerleşik rolleri destekler. Koşullu erişim ilkeleri, [yönetim birimi kapsamlı](../roles/admin-units-assign-roles.md) veya [özel roller](../roles/custom-create.md)de dahil diğer rol türleri için zorlanmaz.

   1. **Dışla** altında, **Kullanıcılar ve gruplar** ' ı seçin ve kuruluşunuzun acil erişim veya kesme camı hesaplarını seçin. 
   1. **Bitti** seçeneğini belirleyin.
1. **Bulut uygulamaları veya eylemleri**  >  **dahil**, **tüm bulut uygulamaları**' nı seçin ve **bitti**' yi seçin.
1. **Erişim denetimleri**  >  **izni** altında **erişim ver**' i seçin, **Multi-Factor Authentication gerektir**' i seçin ve **Seç**' i seçin
1. Ayarlarınızı doğrulayın ve **ilke** ayarını **Açık** olarak ayarlayın.
1. İlkenizi etkinleştirmek için oluşturmak **için Oluştur ' u seçin.**

## <a name="next-steps"></a>Sonraki adımlar

[Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)

[Koşullu erişim yalnızca rapor modunu kullanarak etkiyi belirleme](howto-conditional-access-insights-reporting.md)

[Koşullu erişim What If aracını kullanarak oturum açma davranışının benzetimini yapma](troubleshoot-conditional-access-what-if.md)
