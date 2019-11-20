---
title: Azure Active Directory güvenlik Varsayılanları
description: Kuruluşların yaygın saldırılara karşı korunmasına yardımcı olan güvenlik varsayılan ilkeleri
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bde1741d12cc7ef181fb60bc7eecbec1fed8cbd0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151212"
---
# <a name="what-are-security-defaults"></a>Güvenlik Varsayılanları nelerdir?

Kimlik ile ilgili yaygın saldırılar daha fazla ve popüler olduğunda güvenliği yönetmek zor olabilir. Bu saldırılar parola spreyi, yeniden oynama ve kimlik avı içerir.

Azure Active Directory (Azure AD) içindeki güvenlik Varsayılanları, güvenli olmasını ve kuruluşunuzu korumanıza yardımcı olmasını kolaylaştırır. Güvenlik Varsayılanları, yaygın saldırılar için önceden yapılandırılmış güvenlik ayarlarını içerir. 

Microsoft, güvenlik varsayılanlarını herkes için kullanılabilir hale yapıyor. Amaç, tüm kuruluşların hiçbir ek maliyet olmadan etkin bir temel güvenlik düzeyi olmasını sağlamaktır. Azure portal güvenlik varsayılanlarını açın.

![Güvenlik varsayılanlarını etkinleştirmek için geçiş ile birlikte Azure portal ekran görüntüsü](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
Kiracınızda aşağıdaki güvenlik yapılandırması açılır. 

## <a name="unified-multi-factor-authentication-registration"></a>Birleşik Multi-Factor Authentication kaydı

Kiracınızdaki tüm kullanıcıların, Azure Multi-Factor Authentication hizmeti biçiminde Multi-Factor Authentication (MFA) için kaydolmalıdır. Kullanıcıların, Microsoft Authenticator uygulamasını kullanarak Multi-Factor Authentication için kaydolmak üzere 14 gün vardır. 14 gün geçtikten sonra, Multi-Factor Authentication kaydı tamamlanana kadar Kullanıcı oturum açamaz.

Bazı kullanıcıların Office dışı olabileceğini veya güvenlik varsayılanlarını etkinleştirdikten hemen sonra 14 gün boyunca oturum açmasını anladık. Her kullanıcının Multi-Factor Authentication için yeterli zamana sahip olduğundan emin olmak için, her kullanıcı için 14 günlük süre benzersizdir. Kullanıcının 14 günlük süresi, güvenlik varsayılanlarını etkinleştirdikten sonra, ilk başarılı etkileşimli oturum açma işleminden sonra başlar.

## <a name="multi-factor-authentication-enforcement"></a>Multi-Factor Authentication zorlama

### <a name="protecting-administrators"></a>Yöneticileri koruma

Ayrıcalıklı hesaplara erişimi olan kullanıcıların ortamınıza erişimi artar. Bu hesapların gücüyle ilgili olan güç nedeniyle, onlara özel bir dikkatli davranmanız gerekir. Ayrıcalıklı hesapların korunmasını artırmanın yaygın bir yöntemi, oturum açma için daha güçlü bir hesap doğrulama biçimi gerektirmaktır. Azure AD 'de Multi-Factor Authentication isteyerek daha güçlü bir hesap doğrulaması edinebilirsiniz.

Multi-Factor Authentication kaydı tamamlandıktan sonra, her oturum açtıklarında ek kimlik doğrulaması gerçekleştirmek için aşağıdaki dokuz Azure AD yönetici rolü gerekecektir:

- Genel yönetici
- SharePoint yöneticisi
- Exchange Yöneticisi
- Koşullu Erişim Yöneticisi
- Güvenlik yöneticisi
- Yardım Masası yönetici veya parola Yöneticisi
- Faturalama yöneticisi
- Kullanıcı Yöneticisi
- Kimlik doğrulama Yöneticisi

### <a name="protecting-all-users"></a>Tüm kullanıcıları koruma

Yönetici hesaplarının, çok fazla kimlik doğrulama katmanı gerektiren tek hesaplar olduğunu düşündük. Yöneticiler hassas bilgilere geniş erişime sahiptir ve abonelik genelindeki ayarlarda değişiklik yapabilir. Ancak saldırganlar, son kullanıcıları hedefleyecek şekilde eğilimlidir. 

Bu saldırganlar erişim kazandıktan sonra, özgün hesap sahibi adına ayrıcalıklı bilgilere erişim talep edebilir. Hatta tüm kuruluşunuzda kimlik avı saldırısı gerçekleştirmek için tüm dizini indirebilir. 

Tüm kullanıcılar için korumayı geliştirmenin yaygın bir yöntemi, herkes için Multi-Factor Authentication gibi daha güçlü bir hesap doğrulama biçimi gerektirmaktır. Kullanıcılar Multi-Factor Authentication kaydı tamamladıktan sonra gerektiğinde ek kimlik doğrulaması istenir.

### <a name="blocking-legacy-authentication"></a>Eski kimlik doğrulaması engelleniyor

Kullanıcılarınıza bulut uygulamalarınıza kolay erişim sağlamak için Azure AD, eski kimlik doğrulaması dahil olmak üzere çeşitli kimlik doğrulama protokollerini destekler. *Eski kimlik doğrulaması* , tarafından yapılan bir kimlik doğrulama isteğine başvuran bir terimdir:

- Modern kimlik doğrulaması kullanmayan eski Office istemcileri (örneğin, bir Office 2010 istemcisi).
- IMAP, SMTP veya POP3 gibi eski posta protokollerini kullanan tüm istemciler.

Günümüzde, oturum açma girişimlerinin çoğu eski kimlik doğrulamasından geliyor. Eski kimlik doğrulama Multi-Factor Authentication desteklemez. Dizininizde etkinleştirilmiş bir Multi-Factor Authentication ilkeniz olsa da, bir saldırgan eski bir protokolü kullanarak kimlik doğrulaması yapabilir ve Multi-Factor Authentication atlayabilirsiniz. 

Kiracınızda güvenlik Varsayılanları etkinleştirildikten sonra, eski bir protokol tarafından yapılan tüm kimlik doğrulama istekleri engellenir. Güvenlik Varsayılanları Exchange ActiveSync 'ı engellemez.

### <a name="protecting-privileged-actions"></a>Ayrıcalıklı eylemleri koruma

Kuruluşlar, aşağıdakiler dahil olmak üzere Azure Resource Manager API 'SI aracılığıyla yönetilen çeşitli Azure hizmetleri kullanır:

- Azure portalı 
- Azure PowerShell 
- Azure CLI

Hizmetlerinizi yönetmek için Azure Resource Manager kullanmak, yüksek ayrıcalıklı bir işlemdir. Azure Resource Manager, hizmet ayarları ve abonelik faturalandırması gibi kiracı genelinde yapılandırmaların üzerinde değişiklik yapabilir. Tek faktörlü kimlik doğrulaması, kimlik avı ve parola spreyi gibi çeşitli saldırılara açıktır. 

Azure Resource Manager erişmek ve yapılandırmaya güncelleştirmek isteyen kullanıcıların kimliğini doğrulamak önemlidir. Erişim izni vermeden önce ek kimlik doğrulaması gerektirerek kimliklerini doğrularsınız.

Kiracınızda güvenlik varsayılanlarını etkinleştirdikten sonra, Azure portal, Azure PowerShell veya Azure CLı 'ye erişen tüm kullanıcıların ek kimlik doğrulamasını tamamlaması gerekir. Bu ilke, yönetici veya Kullanıcı olup olmadıkları gibi Azure Resource Manager erişen tüm kullanıcılar için geçerlidir. 

Kullanıcı Multi-Factor Authentication için kayıtlı değilse, kullanıcının devam edebilmesi için Microsoft Authenticator uygulamasını kullanarak kaydetmesi gerekecektir. 14 günlük Multi-Factor Authentication kayıt süresi sağlanmaz.

## <a name="deployment-considerations"></a>Dağıtma konuları

Aşağıdaki ek konular kiracınızın güvenlik varsayılanlarını dağıtma ile ilgilidir.

### <a name="older-protocols"></a>Eski protokoller

Posta istemcileri, kimlik doğrulama isteklerini yapmak için eski kimlik doğrulama protokollerini (IMAP, SMTP ve POP3 gibi) kullanır. Bu protokoller Multi-Factor Authentication desteklemez. Microsoft 'un gördüğü hesabın çoğu, Multi-Factor Authentication atlamaya çalışan eski protokollere karşı saldırılara karşı bir çalışmalardır. 

Multi-Factor Authentication, bir yönetici hesapta oturum açmak için gerekli olduğundan ve saldırganların onu atlayamazsa, güvenlik Varsayılanları eski protokollerden yönetici hesaplarına yapılan tüm kimlik doğrulama isteklerini engeller.

> [!WARNING]
> Bu ayarı etkinleştirmeden önce, yöneticileriniz tarafından daha eski kimlik doğrulama protokolleri kullanmıyor olduğundan emin olun. Daha fazla bilgi için bkz. [eski kimlik doğrulamasından nasıl uzaklaşın](concept-conditional-access-block-legacy-authentication.md).

### <a name="conditional-access"></a>Koşullu Erişim

Güvenlik Varsayılanları tarafından etkinleştirilen aynı davranışı sağlayan ilkeleri yapılandırmak için koşullu erişimi kullanabilirsiniz. Koşullu erişim kullanıyorsanız ve ortamınızda koşullu erişim ilkeleri etkinleştirilmişse, güvenlik Varsayılanları sizin için kullanılamaz. Koşullu erişim sağlayan ancak ortamınızda hiç koşullu erişim ilkesi etkinleştirilmemiş bir lisansınız varsa, koşullu erişim ilkelerini etkinleştirene kadar güvenlik varsayılanlarını kullanmaya hoş geldiniz.

![Uyarı iletisi yalnızca güvenlik varsayılanlarına veya koşullu erişime sahip olabilirsiniz](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

Eşdeğer ilkeleri yapılandırmak için koşullu erişimi nasıl kullanabileceğinizi gösteren adım adım kılavuzlar aşağıda verilmiştir:

- [Yöneticiler için MFA gerektir](howto-conditional-access-policy-admin-mfa.md)
- [Azure yönetimi için MFA gerektir](howto-conditional-access-policy-azure-management.md)
- [Eski kimlik doğrulamasını engelle](howto-conditional-access-policy-block-legacy.md)
- [Tüm kullanıcılar için MFA gerektir](howto-conditional-access-policy-all-users-mfa.md)

## <a name="enabling-security-defaults"></a>Güvenlik varsayılanlarını etkinleştirme

Dizininizde güvenlik varsayılanlarını etkinleştirmek için:

1.  [Azure portal](https://portal.azure.com) Bir Güvenlik Yöneticisi, koşullu erişim Yöneticisi veya genel yönetici olarak oturum açın.
1.  **Azure Active Directory**   > **özelliklerine**gidin.
1. **Güvenlik varsayılanlarını Yönet**' i seçin.
1. **Güvenlik varsayılanlarını etkinleştir** ayarını **Evet**olarak ayarlayın.
1. **Kaydet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

[Ortak koşullu erişim ilkeleri](concept-conditional-access-policy-common.md)

[Koşullu erişim nedir?](overview.md)
