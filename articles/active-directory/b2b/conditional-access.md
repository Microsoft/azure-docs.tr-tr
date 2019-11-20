---
title: B2B işbirliği kullanıcıları için koşullu erişim-Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B işbirliği, kurumsal uygulamalarınıza seçmeli erişim için Multi-Factor Authentication 'ı (MFA) destekler
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a54e5006a268347148945fbe7fc5f18cfa41036
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357124"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>B2B işbirliği kullanıcıları için koşullu erişim

## <a name="multi-factor-authentication-for-b2b-users"></a>B2B kullanıcıları için Multi-Factor Authentication
Azure AD B2B işbirliğiyle kuruluşlar, B2B kullanıcıları için Multi-Factor Authentication (MFA) ilkelerini uygulayabilir. Bu ilkeler, kiracı, uygulama veya bireysel kullanıcı düzeyinde, kuruluşun tam zamanlı çalışanları ve üyeleri için etkinleştirildikleri şekilde zorlanabilir. MFA ilkeleri, kaynak kuruluşta zorlanır.

Örnek:
1. Şirket B 'deki yönetici veya bilgi çalışanı, Şirket A 'ya ait bir kullanıcı olan Şirket A 'da *foo* bir uygulamaya davet eder.
2. A şirketindeki *bir uygulama,* erişimde MFA gerektirecek şekilde yapılandırılmıştır.
3. Şirket B 'deki Kullanıcı, Şirket A kiracısında *foo* uygulamasına erişmeye çalıştığında, bir MFA sınamasını tamamlamaları istenir.
4. Kullanıcı, kendi MFA 'yı Şirket A ile ayarlayabilir ve MFA seçeneğini tercih edebilir.
5. Bu senaryo tüm kimlik (Azure AD veya MSA) için çalışarak (örneğin, B şirketindeki kullanıcılar sosyal KIMLIK kimlik doğrulamasını kullanıyorsa)
6. Şirket A 'nın MFA 'yı destekleyen yeterli Premium Azure AD Lisansı olmalıdır. Şirket B 'deki Kullanıcı, A şirketinin bu lisansını kullanır.

İş ortağı kuruluşun MFA özelliklerine sahip olsa bile, kiralamanın, iş ortağı kuruluştan kullanıcılara MFA 'dan her zaman sorumludur.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>B2B işbirliği kullanıcıları için MFA ayarlama
B2B işbirliği kullanıcıları için MFA 'yı ayarlamaya ne kadar kolay olduğunu öğrenmek için aşağıdaki videoda nasıl yapılacağını öğrenin:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B kullanıcıları teklifi satın alma için MFA deneyimi
Kullanım deneyimini görmek için aşağıdaki animasyonu inceleyin:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>B2B işbirliği kullanıcıları için MFA sıfırlaması
Şu anda yönetici, B2B işbirliği kullanıcılarının yalnızca aşağıdaki PowerShell cmdlet 'lerini kullanarak yeniden kanıt sağlamasını gerektirebilir:

1. Azure AD'ye bağlan

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Tüm kullanıcıları kanıtlama yöntemlerine al

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Örnek aşağıda verilmiştir:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Belirli bir kullanıcının, B2B işbirliği kullanıcısına düzeltme yöntemlerini yeniden ayarlaması için MFA yöntemini sıfırlayın. Örnek:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Kaynak kiralamamız neden MFA gerçekleştirdik?

Geçerli yayında MFA, tahmine dayalı nedenlerle her zaman kaynak kiralandır. Örneğin, bir contoso kullanıcısı (Sally) Fabrikam 'a davet edildiklerini ve Fabrikam 'ın B2B kullanıcıları için MFA 'yı etkinleştirdiklerini varsayalım.

Contoso, APP1 için MFA ilkesi etkinleştirmişse, app2 değil ise, belirteçte contoso MFA talebine baktığımızda, aşağıdaki sorunu görebiliriz:

* Gün 1: Kullanıcı contoso 'da MFA 'ya sahiptir ve APP1 'e erişiyor, sonra fabrikam 'da ek MFA istemi gösterilmez.

* Gün 2: Kullanıcı, contoso 'da App 2 ' ye eriştiğinde, bu nedenle fabrikam 'a erişirken, burada MFA 'ya kaydolmalıdır.

Bu işlem kafa karıştırıcı olabilir ve oturum açma tamamlanmalarından sonra bırakmaya yol açabilir.

Üstelik, contoso MFA özelliğine sahip olsa bile, Fabrikam 'ın contoso MFA ilkesine güveneceği her zaman böyle değildir.

Son olarak, kaynak kiracı MFA aynı zamanda MSAs ve sosyal kimlikler ve MFA 'nın ayarlanmayan iş ortakları için de çalışır.

Bu nedenle, B2B kullanıcıları için MFA önerisi her zaman davet kiracısında MFA gerektirdir. Bu gereksinim, bazı durumlarda çift MFA oluşmasına neden olabilir, ancak davet kiracıya erişirken son kullanıcıların deneyimi tahmin edilebilir: Sally, davet eden kiracıyla MFA için kaydolmalıdır.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>B2B kullanıcıları için cihaz tabanlı, konum tabanlı ve risk tabanlı koşullu erişim

Contoso, kurumsal verileri için cihaz tabanlı koşullu erişim ilkeleri etkinleştirdiğinde, contoso tarafından yönetilmeyen ve contoso cihaz ilkeleriyle uyumlu olmayan cihazlardan erişim engellenir.

B2B kullanıcısının aygıtı contoso tarafından yönetilmemişse, iş ortağı kuruluşlarından B2B kullanıcılarının erişimi, bu ilkelerin zorlandığı bağlamda engellenirler. Ancak contoso, belirli iş ortağı kullanıcılarını içeren dışlama listeleri oluşturabilir ve bunları cihaz tabanlı koşullu erişim ilkesinden hariç tutabilir.

#### <a name="mobile-application-management-policies-for-b2b"></a>B2B için mobil uygulama yönetimi ilkeleri

Koşullu erişim uygulama koruma ilkeleri B2B kullanıcılarına uygulanamıyor çünkü bu, davet eden kuruluşun B2B kullanıcısının ana kuruluşuna görünürlüğü yoktur.

#### <a name="location-based-conditional-access-for-b2b"></a>B2B için konum tabanlı koşullu erişim

Davet edilen kuruluş, iş ortağı kuruluşlarını tanımlayan güvenilir bir IP adresi aralığı oluşturabiliyorsa B2B kullanıcıları için konum tabanlı koşullu erişim ilkeleri zorlanabilir.

#### <a name="risk-based-conditional-access-for-b2b"></a>B2B için risk tabanlı koşullu erişim

Şu anda, B2B kullanıcısının ana kuruluşunda risk değerlendirmesi gerçekleştirildiğinden, risk tabanlı oturum açma ilkeleri B2B kullanıcılarına uygulanamıyor.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD B2B işbirliği ile ilgili aşağıdaki makalelere bakın:

* [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
* [Azure AD B2B işbirliği lisanslaması](licensing-guidance.md)
* [Azure Active Directory B2B işbirliği hakkında sık sorulan sorular (SSS)](faq.md)
