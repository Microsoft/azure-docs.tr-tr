---
title: Azure Active Directory güvenlik Varsayılanları
description: Kuruluşların Azure AD 'de yaygın saldırılara karşı korunmasına yardımcı olan güvenlik varsayılan ilkeleri
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: efa88e1be5c5df5dd09cb5a97c8ece352496ccdb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769706"
---
# <a name="what-are-security-defaults"></a>Güvenlik Varsayılanları nelerdir?

Parola spreyi, yeniden oynama ve kimlik avı gibi yaygın kimlikle ilgili saldırılara karşı güvenliğin yönetilmesi zor olabilir. Güvenlik Varsayılanları, kuruluşunuzun önceden yapılandırılmış güvenlik ayarlarıyla bu saldırılara karşı korunmasına yardımcı olmaya daha kolay hale getirir:

- Tüm kullanıcıların Azure AD Multi-Factor Authentication için kaydolmasına gerek vardır.
- Yöneticilerin Multi-Factor Authentication gerçekleştirmesini gerektirme.
- Eski kimlik doğrulama protokolleri engelleniyor.
- Gerektiğinde kullanıcıların Multi-Factor Authentication gerçekleştirmesini isteme.
- Azure portal erişim gibi ayrıcalıklı etkinlikleri koruma.

![Güvenlik varsayılanlarını etkinleştirmek için geçiş ile birlikte Azure portal ekran görüntüsü](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
Güvenlik varsayılanlarını neden kullanılabilir hale getirdiğinizle ilgili daha fazla ayrıntı, [güvenlik varsayılanlarını sunarak](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)Alex Weinert 'ın blog gönderisine bulunabilir.

## <a name="availability"></a>Kullanılabilirlik

Microsoft, güvenlik varsayılanlarını herkes için kullanılabilir hale yapıyor. Amaç, tüm kuruluşların hiçbir ek maliyet olmadan etkin bir temel güvenlik düzeyi olmasını sağlamaktır. Azure portal güvenlik varsayılanlarını açın. Kiracınız 22 Ekim 2019 ' de oluşturulduysa veya sonrasında oluşturulduysa, kiracınızda güvenlik varsayılanlarının zaten etkinleştirilmiş olması olasıdır. Tüm kullanıcılarınızı korumak için, güvenlik Varsayılanları oluşturulan tüm yeni kiracılar için kullanıma alınıyor.

### <a name="whos-it-for"></a>Kim?

- Güvenlik duruşunuzu artırmak isteyen ancak nasıl veya nerede başlayabileceğinizi bilmiyorsanız, güvenlik varsayılanlarını sizin için kullanabilirsiniz.
- Azure Active Directory lisanslama 'nin ücretsiz katmanını kullanan bir kuruluşunuzda, güvenlik Varsayılanları sizin için kullanılır.

### <a name="who-should-use-conditional-access"></a>Koşullu erişimi kimler kullanmalıdır?

- Şu anda sinyalleri bir araya getirmek için koşullu erişim ilkelerini kullanan bir kuruluşunuzda, kararlar almak ve kuruluş ilkelerini zorlamak için, güvenlik Varsayılanları muhtemelen sizin için doğru değildir. 
- Azure Active Directory Premium lisansları olan bir kuruluşunuzda, güvenlik Varsayılanları muhtemelen sizin için doğru değildir.
- Kuruluşunuzun karmaşık güvenlik gereksinimleri varsa, koşullu erişimi dikkate almalısınız.

## <a name="policies-enforced"></a>Zorlanan ilkeler

### <a name="unified-multi-factor-authentication-registration"></a>Birleşik Multi-Factor Authentication kaydı

Kiracınızdaki tüm kullanıcıların, Azure AD Multi-Factor Authentication biçiminde Multi-Factor Authentication (MFA) için kaydolmalıdır. Kullanıcıların, Microsoft Authenticator uygulamasını kullanarak Azure AD Multi-Factor Authentication için kaydolmak üzere 14 gün vardır. 14 gün geçtikten sonra, kayıt tamamlanana kadar Kullanıcı oturum açamaz. Kullanıcının 14 günlük süresi, güvenlik varsayılanlarını etkinleştirdikten sonra, ilk başarılı etkileşimli oturum açma işleminden sonra başlar.

### <a name="protecting-administrators"></a>Yöneticileri koruma

Ayrıcalıklı erişimi olan kullanıcılar ortamınıza erişimi artırmıştır. Bu hesapların gücüyle ilgili olan güç nedeniyle, onlara özel bir dikkatli davranmanız gerekir. Ayrıcalıklı hesapların korunmasını artırmanın yaygın bir yöntemi, oturum açma için daha güçlü bir hesap doğrulama biçimi gerektirmaktır. Azure AD 'de Multi-Factor Authentication 'ı zorunlu kılarak daha güçlü bir hesap doğrulaması edinebilirsiniz.

Azure AD Multi-Factor Authentication kaydı tamamlandıktan sonra, her oturum açtıklarında ek kimlik doğrulaması gerçekleştirmek için aşağıdaki dokuz Azure AD yönetici rolü gerekir:

- Genel yönetici
- SharePoint yöneticisi
- Exchange yöneticisi
- Koşullu Erişim Yöneticisi
- Güvenlik yöneticisi
- Yardım Masası Yöneticisi
- Faturalama yöneticisi
- Kullanıcı yöneticisi
- Kimlik doğrulama Yöneticisi

> [!WARNING]
> Dizininizde, genel yönetici ayrıcalıklarına sahip en az iki hesap atanmış olduğundan emin olun. Bu, bir genel yöneticinin kilitlenme olasılığına karşı yardımcı olur. Daha ayrıntılı bilgi için [Azure AD 'de acil durum erişim hesaplarını yönetme](../roles/security-emergency-access.md)makalesine bakın.

### <a name="protecting-all-users"></a>Tüm kullanıcıları koruma

Yönetici hesaplarının, çok fazla kimlik doğrulama katmanı gerektiren tek hesaplar olduğunu düşündük. Yöneticiler hassas bilgilere geniş erişime sahiptir ve abonelik genelindeki ayarlarda değişiklik yapabilir. Ancak saldırganlar son kullanıcıları sıklıkla hedefleyebilir. 

Bu saldırganlar erişim kazandıktan sonra, özgün hesap sahibi adına ayrıcalıklı bilgilere erişim talep edebilir. Hatta tüm kuruluşunuzda kimlik avı saldırısı gerçekleştirmek için tüm dizini indirebilir. 

Tüm kullanıcılar için korumayı geliştirmenin yaygın bir yöntemi, herkes için Multi-Factor Authentication gibi daha güçlü bir hesap doğrulama biçimi gerektirmaktır. Kullanıcılar Multi-Factor Authentication kaydı tamamladıktan sonra gerektiğinde ek kimlik doğrulaması istenir. Kullanıcılar öncelikle yeni bir cihaz veya uygulama kullanarak kimlik doğrulaması yaparken veya kritik rol ve görevleri gerçekleştirirken istenir. Bu işlevsellik, SaaS uygulamaları dahil olmak üzere Azure AD 'ye kayıtlı tüm uygulamaları korur.

### <a name="blocking-legacy-authentication"></a>Eski kimlik doğrulaması engelleniyor

Kullanıcılarınıza bulut uygulamalarınıza kolay erişim sağlamak için Azure AD, eski kimlik doğrulama dahil olmak üzere çeşitli kimlik doğrulama protokollerini destekler. *Eski kimlik doğrulaması* , tarafından yapılan bir kimlik doğrulama isteğine başvuran bir terimdir:

- Modern kimlik doğrulaması kullanmayan istemciler (örneğin, bir Office 2010 istemcisi).
- IMAP, SMTP veya POP3 gibi eski posta protokollerini kullanan tüm istemciler.

Günümüzde, en çok güvenliği ihlal eden oturum açma girişimleri eski kimlik doğrulamasından geliyor. Eski kimlik doğrulama Multi-Factor Authentication desteklemez. Dizininizde etkinleştirilmiş bir Multi-Factor Authentication ilkeniz olsa da, bir saldırgan eski bir protokolü kullanarak kimlik doğrulaması yapabilir ve Multi-Factor Authentication atlayabilirsiniz. 

Kiracınızda güvenlik Varsayılanları etkinleştirildikten sonra, eski bir protokol tarafından yapılan tüm kimlik doğrulama istekleri engellenir. Güvenlik Varsayılanları Exchange Active Sync temel kimlik doğrulamasını engeller.

> [!WARNING]
> Güvenlik varsayılanlarını etkinleştirmeden önce, yöneticileriniz tarafından daha eski kimlik doğrulama protokolleri kullanmıyor olduğundan emin olun. Daha fazla bilgi için bkz. [eski kimlik doğrulamasından nasıl uzaklaşın](concept-fundamentals-block-legacy-authentication.md).

- [Microsoft 365 kullanarak e-posta göndermek için çok işlevli bir cihaz veya uygulamayı ayarlama](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>Ayrıcalıklı eylemleri koruma

Kuruluşlar, Azure Resource Manager API ile yönetilen çeşitli Azure hizmetlerini kullanarak aşağıdakiler de dahildir:

- Azure portalı 
- Azure PowerShell 
- Azure CLI

Hizmetlerinizi yönetmek için Azure Resource Manager kullanmak, yüksek ayrıcalıklı bir işlemdir. Azure Resource Manager, hizmet ayarları ve abonelik faturalandırması gibi kiracı genelinde yapılandırmaların üzerinde değişiklik yapabilir. Tek faktörlü kimlik doğrulaması, kimlik avı ve parola spreyi gibi çeşitli saldırılara açıktır. 

Azure Resource Manager erişmek ve yapılandırmaya güncelleştirmek isteyen kullanıcıların kimliğini doğrulamak önemlidir. Erişim izni vermeden önce ek kimlik doğrulaması gerektirerek kimliklerini doğrularsınız.

Kiracınızda güvenlik varsayılanlarını etkinleştirdikten sonra, Azure portal, Azure PowerShell veya Azure CLı 'ye erişen tüm kullanıcıların ek kimlik doğrulamasını tamamlaması gerekir. Bu ilke, yönetici veya Kullanıcı olup olmadıkları gibi Azure Resource Manager erişen tüm kullanıcılar için geçerlidir. 

> [!NOTE]
> 2017 öncesi Exchange Online kiracılarında modern kimlik doğrulaması varsayılan olarak devre dışıdır. Bu kiracılar aracılığıyla kimlik doğrulaması yaparken bir oturum açma döngüsü olasılığını önlemek için [modern kimlik doğrulamayı etkinleştirmeniz](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)gerekir.

> [!NOTE]
> Azure AD Connect eşitleme hesabı güvenlik varsayılanlarını dışarıda bırakılır ve Multi-Factor Authentication 'ı kaydetmesi veya gerçekleştirmesi istenmez. Kuruluşlar bu hesabı başka amaçlar için kullanmamalıdır.

## <a name="deployment-considerations"></a>Dağıtma konuları

Aşağıdaki ek konular, güvenlik varsayılanlarını dağıtma ile ilgilidir.

### <a name="authentication-methods"></a>Kimlik doğrulama yöntemleri

Bu ücretsiz güvenlik Varsayılanları **, bildirimleri kullanarak yalnızca Microsoft Authenticator uygulamasını kullanarak** Azure AD Multi-Factor Authentication 'nin kaydedilmesine ve kullanımına izin verir. Koşullu erişim, yöneticinin etkinleştirmek üzere seçtiği herhangi bir kimlik doğrulama yönteminin kullanılmasına izin verir.

| Yöntem | Güvenlik varsayılanları | Koşullu Erişim |
| --- | --- | --- |
| Mobil uygulama aracılığıyla bildirim | X | X |
| Mobil uygulama veya donanım belirtecinden doğrulama kodu | X * * | X |
| Telefona kısa mesaj |   | X |
| Telefon çağrısı |   | X |
| Uygulama parolaları |   | X * * * |

- * * Kullanıcılar Microsoft Authenticator uygulamasındaki doğrulama kodlarını kullanabilir, ancak yalnızca bildirim seçeneğini kullanarak kaydolabilirsiniz.
- Uygulama parolaları yalnızca yöneticiler tarafından etkinleştirildiyse eski kimlik doğrulama senaryolarıyla Kullanıcı başına MFA 'da kullanılabilir.

### <a name="disabled-mfa-status"></a>Devre dışı MFA durumu

Kuruluşunuz, Kullanıcı başına tabanlı Azure AD Multi-Factor Authentication 'ın önceki bir kullanıcısı ise, Multi-Factor auth durum sayfasına bakarsanız, **etkin** veya **Zorlanmış** bir durumda kullanıcıları görmeyen bir uyarıda yok. **Devre dışı** , güvenlik varsayılanlarını veya koşullu erişim tabanlı Azure AD Multi-Factor Authentication kullanan kullanıcılar için uygun durumlardır.

### <a name="conditional-access"></a>Koşullu Erişim

Güvenlik varsayılanlarına benzer ilkeleri yapılandırmak için koşullu erişimi kullanabilirsiniz, ancak güvenlik Varsayılanları ' nda kullanılamayan Kullanıcı dışlamaları dahil daha fazla ayrıntı düzeyi vardır. Koşullu erişim kullanıyorsanız ve ortamınızda koşullu erişim ilkeleri etkinleştirilmişse, güvenlik Varsayılanları sizin için kullanılamaz. Koşullu erişim sağlayan ancak ortamınızda hiç koşullu erişim ilkesi etkinleştirilmemiş bir lisansınız varsa, koşullu erişim ilkelerini etkinleştirene kadar güvenlik varsayılanlarını kullanmaya hoş geldiniz. Azure AD Lisansı hakkında daha fazla bilgi için [Azure AD fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/active-directory/)bulabilirsiniz.

![Uyarı iletisi yalnızca güvenlik varsayılanlarına veya koşullu erişime sahip olabilirsiniz](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Aşağıda, güvenlik Varsayılanları tarafından etkinleştirilen ilkelerle eşdeğer ilkeleri yapılandırmak için koşullu erişimi nasıl kullanabileceğiniz hakkında adım adım kılavuzlar verilmiştir:

- [Yöneticiler için MFA gerektir](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Azure yönetimi için MFA gerektirme](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Eski kimlik doğrulamasını engelle](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Tüm kullanıcılar için MFA gerektirme](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Azure AD MFA kaydı gerektir](../identity-protection/howto-identity-protection-configure-mfa-policy.md) -Azure AD Premium P2 Azure AD kimlik koruması bir parçası gerektirir.

## <a name="enabling-security-defaults"></a>Güvenlik varsayılanlarını etkinleştirme

Dizininizde güvenlik varsayılanlarını etkinleştirmek için:

1.  [Azure Portal](https://portal.azure.com)   bir güvenlik yöneticisi, koşullu erişim Yöneticisi veya genel yönetici olarak oturum açın.
1.  **Azure Active Directory**   >  **özelliklere** gidin.
1. **Güvenlik varsayılanlarını Yönet**' i seçin.
1. **Güvenlik varsayılanlarını etkinleştir** ayarını **Evet** olarak ayarlayın.
1. **Kaydet**’i seçin.

## <a name="disabling-security-defaults"></a>Güvenlik varsayılanlarını devre dışı bırakma

Güvenlik varsayılanlarını değiştirecek koşullu erişim ilkelerini uygulamayı tercih eden kuruluşlar, güvenlik varsayılanlarını devre dışı bırakmalıdır. 

![Uyarı iletisi koşullu erişimi etkinleştirmek için güvenlik varsayılanlarını devre dışı bırak](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Dizininizde güvenlik varsayılanlarını devre dışı bırakmak için:

1.  [Azure Portal](https://portal.azure.com)   bir güvenlik yöneticisi, koşullu erişim Yöneticisi veya genel yönetici olarak oturum açın.
1.  **Azure Active Directory**   >  **özelliklere** gidin.
1. **Güvenlik varsayılanlarını Yönet**' i seçin.
1. **Güvenlik varsayılanlarını etkinleştir** ' i ayarla **Hayır** olarak ayarlayın.
1. **Kaydet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

[Ortak koşullu erişim ilkeleri](../conditional-access/concept-conditional-access-policy-common.md)
