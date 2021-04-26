---
title: Koşullu erişim ilkesinde denetim verme-Azure Active Directory
description: Azure AD koşullu erişim ilkesinde izin verme denetimleri
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bce54bb845e3085d654e3980123ef5c8a856fd98
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530186"
---
# <a name="conditional-access-grant"></a>Koşullu erişim: ver

Bir yönetici, koşullu erişim ilkesinde, kaynaklara erişim izni vermek veya erişimi engellemek için erişim denetimlerini kullanabilir.

![Multi-Factor Authentication gerektiren bir izin denetimi olan koşullu erişim ilkesi](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Erişimi engelleme

Blok tüm atamaları hesaba ayırır ve koşullu erişim ilkesi yapılandırmasına bağlı olarak erişimi engeller.

Blok, uygun bilgilerle silinmeli güçlü bir denetimdir. Blok deyimlerine sahip ilkeler istenmeden yan etkilere sahip olabilir. Uygun test ve doğrulama, ölçeklendirmeye etkinleştirilmeden önce hayati önem taşır. Yöneticiler, değişiklik yaparken koşullu erişim [yalnızca rapor modu](concept-conditional-access-report-only.md) ve [What If aracı](what-if-tool.md) gibi araçları kullanmalıdır.

## <a name="grant-access"></a>Erişim verme

Yöneticiler, erişim izni verirken bir veya daha fazla denetimi zorlamayı tercih edebilir. Bu denetimler aşağıdaki seçenekleri içerir: 

- [Multi-Factor Authentication gerektir (Azure AD Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Cihazın uyumlu olarak işaretlenmesini gerektir (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Karma Azure AD 'ye katılmış cihaz gerektir](../devices/concept-azure-ad-join-hybrid.md)
- [Onaylanan istemci uygulaması gerektir](app-based-conditional-access.md)
- [Uygulama koruma ilkesi gerektir](app-protection-based-conditional-access.md)
- [Parola değişikliği iste](#require-password-change)

Yöneticiler bu seçenekleri birleştirmeyi seçerken, aşağıdaki yöntemleri seçebilirler:

- Seçili tüm denetimleri gerektir (Denetim **ve** denetim)
- Seçili denetimlerden birini gerektir (Denetim **veya** denetim)

Varsayılan olarak, koşullu erişim tüm seçili denetimleri gerektirir.

### <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication gerektir

Bu onay kutusunun belirlenmesi, kullanıcıların Azure AD Multi-Factor Authentication gerçekleştirmesini gerektirir. Azure AD Multi-Factor Authentication dağıtma hakkında daha fazla bilgi, [bulut tabanlı Azure ad Multi-Factor Authentication dağıtımını planlama](../authentication/howto-mfa-getstarted.md)makalesinde bulunabilir.

[İş Için Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview) , koşullu erişim ilkelerinde Multi-Factor Authentication gereksinimini karşılar. 

### <a name="require-device-to-be-marked-as-compliant"></a>Cihazın uyumlu olarak işaretlenmesini gerektir

Microsoft Intune dağıtılan kuruluşlar, belirli uyumluluk gereksinimlerini karşılayan cihazları tanımlamak için cihazlarından döndürülen bilgileri kullanabilir. Bu ilke uyumluluk bilgileri, Intune 'dan Azure AD 'ye iletilir ve Koşullu erişimin kaynaklara erişim izni vermek veya erişimi engellemek için kararlar verebilecekleri bir karardır. Uyumluluk ilkeleri hakkında daha fazla bilgi için, [Intune kullanarak kuruluşunuzdaki kaynaklara erişime izin vermek üzere cihazlarda kuralları ayarlama](/intune/protect/device-compliance-get-started)makalesine bakın.

Bir cihaz, Intune (herhangi bir cihaz işletim sistemi için) veya Windows 10 cihazları için üçüncü taraf MDM sistemi tarafından uyumlu olarak işaretlenebilir. JAMF Pro yalnızca desteklenen üçüncü taraf MDM sistemidir. Tümleştirme hakkında daha fazla bilgi için bkz. Bu makalede, [Uyumluluk Için JAMF Pro 'Yu Intune Ile tümleştirin](/intune/protect/conditional-access-integrate-jamf).

Cihazların uyumlu olarak işaretlenmeleri için önce Azure AD 'de kayıtlı olmaları gerekir. Cihaz kaydı hakkında daha fazla bilgi, makalesinde [cihaz kimliği nedir?](../devices/overview.md)makalesinde bulunabilir.

### <a name="require-hybrid-azure-ad-joined-device"></a>Karma Azure AD 'ye katılmış cihaz gerektir

Kuruluşlar, koşullu erişim ilkesinin bir parçası olarak cihaz kimliğini kullanmayı seçebilir. Kuruluşlar, bu onay kutusu kullanılarak cihazların karma Azure AD 'ye katılmış olmasını gerektirebilir. Cihaz kimlikleri hakkında daha fazla bilgi için, [cihaz kimliği nedir?](../devices/overview.md)makalesine bakın.

[Cihaz kodu OAuth akışını](../develop/v2-oauth2-device-code.md)kullanırken, yönetilen cihaz izin verme denetimi veya cihaz durumu koşulu desteklenmez. Bunun nedeni, kimlik doğrulaması gerçekleştiren cihazın cihaz durumunu cihaza bir kod sağlamak ve belirteçteki cihaz durumunun kimlik doğrulaması gerçekleştiren cihaza kilitli olması nedeniyle oluşur. Bunun yerine Multi-Factor Authentication izin iste denetimini kullanın.

### <a name="require-approved-client-app"></a>Onaylanan istemci uygulaması gerektir

Kuruluşlar, onaylanan bir istemci uygulamasından seçili bulut uygulamalarına yönelik bir erişim denemesinin yapılması gerektiğini gerektirebilir. Bu onaylanan istemci uygulamaları, mobil cihaz yönetimi (MDM) çözümünden bağımsız olarak [Intune uygulama koruma ilkelerini](/intune/app-protection-policy) destekler.

Bu izin denetiminden yararlanmak için, koşullu erişim cihazın bir aracı uygulamasının kullanılmasını gerektiren Azure Active Directory kaydedilmesini gerektirir. Aracı uygulaması iOS için Microsoft Authenticator veya Android cihazları için Microsoft Authenticator ya da Microsoft şirket portalı olabilir. Kullanıcı kimlik doğrulamaya çalıştığında cihazda bir aracı uygulaması yüklü değilse, Kullanıcı gerekli Aracı uygulamasını yüklemek için uygun uygulama deposuna yeniden yönlendirilir.

Bu ayar, aşağıdaki iOS ve Android uygulamaları için geçerlidir:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft faturalandırma
- Microsoft Kaizala
- Microsoft başlatıcısı
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype Kurumsal
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft beyaz tahta
- Yönetici Microsoft 365

**Açıklamalar**

- Onaylanan istemci uygulamaları, Intune mobil uygulama yönetimi özelliğini destekler.
- **Onaylanan istemci uygulaması gereksinimini gerektir** :
   - Yalnızca iOS ve Android for Device platform koşulunu destekler.
   - Cihazı kaydetmek için bir aracı uygulaması gereklidir. Aracı uygulaması iOS için Microsoft Authenticator veya Android cihazları için Microsoft Authenticator ya da Microsoft şirket portalı olabilir.
- Koşullu erişim, onaylanan bir istemci uygulamasında Microsoft Edge 'i InPrivate modunda kabul edemez.
- Power BI mobil uygulamanın şirket içi uygulamasına bağlanmasını sağlamak için Azure AD Uygulama Ara Sunucusu kullanma Power BI Rapor Sunucusu, Microsoft Power BI uygulaması onaylı bir istemci uygulaması olarak gerektiren koşullu erişim ilkeleriyle desteklenmez.

Yapılandırma örnekleri için [koşullu erişimle bkz. nasıl yapılır: Cloud App Access için onaylanan istemci uygulamaları gerektirme](app-based-conditional-access.md) .

### <a name="require-app-protection-policy"></a>Uygulama koruma ilkesi gerektir

Koşullu erişim ilkenizde, seçili bulut uygulamalarına erişim izni vermeden önce istemci uygulamada bir [Intune uygulama koruma ilkesinin](/intune/app-protection-policy) mevcut olmasını zorunlu kılabilirsiniz. 

Bu izin denetiminden yararlanmak için, koşullu erişim cihazın bir aracı uygulamasının kullanılmasını gerektiren Azure Active Directory kaydedilmesini gerektirir. Aracı uygulama, iOS için Microsoft Authenticator ya da Android cihazlar için Microsoft Şirket portalı olabilir. Kullanıcı kimlik doğrulamaya çalıştığında cihazda bir aracı uygulaması yüklü değilse, aracı uygulamasını yüklemek için Kullanıcı App Store 'a yönlendirilir.

Uygulamalar, **Ilke güvencesi** ile **Intune SDK 'sını** sağlamak ve bu ayarı desteklemek için belirli diğer gereksinimleri karşılamak zorundadır. Intune SDK 'Sı ile uygulama uygulayan geliştiriciler, bu gereksinimlere ilişkin SDK belgelerinde daha fazla bilgi bulabilir.

Bu ayarı desteklemek için aşağıdaki istemci uygulamaları onaylandı:

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft listeleri (iOS)
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- Intune için çok satırlı
- Dokuz posta-e-posta & takvimi

> [!NOTE]
> Microsoft Microsoft ekipleri, Microsoft Kaizala, Microsoft Skype Kurumsal ve Microsoft Visio, **Uygulama koruma Ilkesi gerektir** iznini desteklemez. Bu uygulamaların çalışmasını gerektiriyorsa, lütfen **onaylanan uygulamaları gerektir** ' i kullanın. İki izin kümesi arasındaki OR yan tümcesinin kullanımı, bu üç uygulama için çalışmaz.

**Açıklamalar**

- Uygulama koruma ilkesi için uygulamalar, ilke korumasıyla Intune mobil uygulama yönetimi özelliğini destekler.
- **Uygulama koruma ilkesi gereksinimlerini gerektir** :
    - Yalnızca iOS ve Android for Device platform koşulunu destekler.
    - Cihazı kaydetmek için bir aracı uygulaması gereklidir. İOS 'ta, aracı uygulaması Microsoft Authenticator ve Android üzerinde Intune Şirket Portalı uygulamasıdır.

Yapılandırma örnekleri için [koşullu erişimle bkz. nasıl yapılır: uygulama koruma Ilkesi gerektirme ve bulut uygulaması erişimi için onaylanan bir istemci uygulaması](app-protection-based-conditional-access.md) .

### <a name="require-password-change"></a>Parola değişikliği iste 

Kullanıcı riski algılandığında, Kullanıcı risk ilkesi koşulları kullanılarak Yöneticiler, kullanıcının Azure AD self servis parola sıfırlama kullanarak parolayı güvenli bir şekilde değiştirmesini seçebilirler. Kullanıcı riski algılanırsa, kullanıcılar kendi kendine düzeltme için bir self servis parola sıfırlaması gerçekleştirebilir, bu, Yöneticiler için gereksiz paraziti engellemek üzere Kullanıcı riski olayını kapatır. 

Kullanıcıdan parolasını değiştirmesi istendiğinde, önce çok faktörlü kimlik doğrulamasının tamamlanmaları gerekir. Tüm kullanıcılarınızın çok faktörlü kimlik doğrulaması için kaydoldıklarından emin olmak isteyeceksiniz. bu nedenle, hesapları için bir risk algılanarak hazırlanırlar.  

> [!WARNING]
> Kullanıcılar, Kullanıcı risk ilkesini tetiklemeden önce self servis parola sıfırlama için önceden kaydolmalıdır. 

Parola değiştirme denetimini kullanarak bir ilkeyi yapılandırırken, bir yerde birkaç kısıtlama bulunur.  

1. İlke, ' tüm bulut uygulamaları ' için atanmalıdır. Bu, bir saldırganın farklı bir uygulamada oturum açarak Kullanıcı parolasını değiştirmesini ve hesap riskini sıfırlamasını engeller. 
1. Parola değiştirme gerektir, uyumlu bir cihaz gerektirme gibi diğer denetimlerle kullanılamaz.  
1. Parola değiştirme denetimi yalnızca Kullanıcı ve Grup atama koşulu, bulut uygulaması atama koşulu (tümü olarak ayarlanması gerekir) ve Kullanıcı risk koşulları ile kullanılabilir. 

### <a name="terms-of-use"></a>Kullanım koşulları

Kuruluşunuz kullanım koşulları oluşturmışsa, denetim ver altında ek seçenekler görünür olabilir. Bu seçenekler, yöneticilerin ilke tarafından korunan kaynaklara erişme koşulu olarak kullanım koşulları onayını gerektirmesini sağlar. Kullanım koşulları hakkında daha fazla bilgi makalesinde [Azure Active Directory kullanım koşulları](terms-of-use.md)' nda bulunabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Koşullu erişim: oturum denetimleri](concept-conditional-access-session.md)

- [Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)

- [Yalnızca rapor modu](concept-conditional-access-report-only.md)
