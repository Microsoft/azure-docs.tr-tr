---
title: Koşullu erişim temel koruma ilkeleri-Azure Active Directory
description: Kuruluşların ortak saldırılara karşı korunması için temel koşullu erişim ilkeleri
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecd46b8cb734355a8394b7480c6def341cf9700d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430335"
---
# <a name="what-are-baseline-policies"></a>Temel ilkeler nelerdir?

Temel ilkeler, kuruluşların pek çok yaygın saldırılara karşı korunmasına yardımcı olan önceden tanımlanmış bir ilke kümesidir. Bu yaygın saldırılar, parola ilaç, yeniden yürütme ve kimlik avı içerebilir. Temel ilkeler Azure AD 'nin tüm sürümlerinde kullanılabilir. Microsoft bu temel koruma ilkelerini herkes tarafından kullanılabilir hale getirtiğinden, kimlik tabanlı saldırılar son birkaç yılda arttığında. Bu dört ilkenin hedefi, tüm kuruluşların ek bir ücret ödemeden etkin bir temel güvenlik düzeyine sahip olmasını sağlamaktır.  

Özelleştirilmiş koşullu erişim ilkelerinin yönetilmesi için Azure AD Premium lisansı gerekir.

## <a name="baseline-policies"></a>Ana hat ilkeleri

![Azure portal koşullu erişim temel ilkeleri](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Dört temel ilke vardır:

* Yöneticiler için MFA gerektir (Önizleme)
* Son Kullanıcı koruması (Önizleme)
* Eski kimlik doğrulamasını engelle (Önizleme)
* Hizmet yönetimi için MFA gerektir (Önizleme)

Bu ilkelerin dördü, POP, IMAP ve eski Office Masaüstü istemcileri gibi eski kimlik doğrulama akışlarını etkiler.

### <a name="require-mfa-for-admins-preview"></a>Yöneticiler için MFA gerektir (Önizleme)

Yönetici hesaplarının sahip olduğu güç ve erişim nedeniyle, onlara özel bir dikkatli davranmanız gerekir. Ayrıcalıklı hesapların korunmasını artırmanın yaygın bir yöntemi, oturum açmak için kullanıldıkları zaman daha güçlü bir hesap doğrulama biçimi gerektirmaktır. Azure Active Directory, yöneticilerin Azure Multi-Factor Authentication kaydolmasına ve kullanmasına gerek duymadan daha güçlü bir hesap doğrulaması sağlayabilirsiniz.

Yöneticiler için MFA gerektir (Önizleme), aşağıdaki dizin rolleri için çok faktörlü kimlik doğrulaması (MFA) gerektiren bir temel ilkedir ve en ayrıcalıklı Azure AD rolleri olarak kabul edilir:

* Genel yönetici
* SharePoint yöneticisi
* Exchange Yöneticisi
* Koşullu Erişim Yöneticisi
* Güvenlik yöneticisi
* Yardım Masası Yönetici/Parola Yöneticisi
* Faturalama yöneticisi
* Kullanıcı Yöneticisi

Kuruluşunuzun komut dosyalarında veya kodda kullanımda olan bu hesapları varsa, bunları [yönetilen kimliklerle](../managed-identities-azure-resources/overview.md)değiştirmeyi göz önünde bulundurun.

### <a name="end-user-protection-preview"></a>Son Kullanıcı koruması (Önizleme)

Yüksek ayrıcalıklı Yöneticiler yalnızca saldırılara yönelik olan tek alanlardır. Kötü aktörler, normal kullanıcıları hedefleyecek şekilde eğilimlidir. Erişim kazandıktan sonra, bu kötü aktör, özgün hesap sahibi adına ayrıcalıklı bilgilere erişim isteğinde bulunabilir veya tüm kuruluşunuzda bir kimlik avı saldırısı gerçekleştirebilir. Tüm kullanıcıların korumasını geliştirmenin yaygın bir yöntemi, riskli oturum açma algılandığında daha güçlü bir hesap doğrulama biçimi gerektirmaktır.

**Son Kullanıcı koruması (Önizleme)** , bir dizindeki tüm kullanıcıları koruyan bir temel ilkedir. Bu ilkenin etkinleştirilmesi, tüm kullanıcıların Azure Multi-Factor Authentication için 14 gün içinde kaydolmanızı gerektirir. Kaydolduktan sonra kullanıcılardan yalnızca riskli oturum açma girişimleri sırasında MFA sorulur. Parola sıfırlama ve risk ortadan kalana kadar güvenliği aşılmış Kullanıcı hesapları engellenir. 

[!NOTE]
Daha önce risk işaretli olan tüm kullanıcılar, ilke etkinleştirilmesinde parola sıfırlamasına ve risk ortadan kaldırılana kadar engellenir.

### <a name="block-legacy-authentication-preview"></a>Eski kimlik doğrulamasını engelle (Önizleme)

Eski kimlik doğrulama protokolleri (örn: IMAP, SMTP, POP3), daha eski posta istemcileri tarafından kimlik doğrulaması için normalde kullanılan protokollerdir. Eski protokoller Multi-Factor Authentication 'ı desteklemez. Dizininiz için çok faktörlü kimlik doğrulaması gerektiren bir ilkeniz olsa da, hatalı aktör bu eski protokollerden birini kullanarak kimlik doğrulaması yapabilir ve Multi-Factor Authentication 'ı atlayabilir.

Eski protokoller tarafından yapılan kötü amaçlı kimlik doğrulama isteklerinden hesabınızı korumanın en iyi yolu, bunları engellenemez.

**Eski kimlik doğrulaması engelleme (Önizleme)** temel ilkesi, eski protokoller kullanılarak yapılan kimlik doğrulama isteklerini engeller. Tüm kullanıcılar için başarıyla oturum açmak üzere modern kimlik doğrulaması kullanılmalıdır. Diğer ana hat ilkeleriyle birlikte kullanıldığında, eski protokollerden gelen istekler engellenir. Ayrıca, tüm kullanıcıların her gerektiğinde MFA yapması gerekecektir. Bu ilke Exchange ActiveSync 'ı engellemez.

### <a name="require-mfa-for-service-management-preview"></a>Hizmet yönetimi için MFA gerektir (Önizleme)

Kuruluşlar çeşitli Azure hizmetlerini kullanır ve bunları şu şekilde Azure Resource Manager tabanlı araçlarla yönetebilir:

* Azure portalı
* Azure PowerShell
* Azure CLI

Kaynak yönetimini gerçekleştirmek için bu araçlardan herhangi birini kullanmak, yüksek ayrıcalıklı bir işlemdir. Bu araçlar, hizmet ayarları ve abonelik faturalaması gibi abonelik genelinde yapılandırmaların tümünü değiştirebilir.

Ayrıcalıklı eylemleri korumak için, bu **hizmet yönetimi IÇIN MFA gerektir (Önizleme)** ilkesi, Azure portal, Azure PowerShell veya Azure CLI 'ye erişen herhangi bir kullanıcı için çok faktörlü kimlik doğrulaması gerektirir.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz.

* [Ortak koşullu erişim ilkeleri](concept-conditional-access-policy-common.md)
* [Kimlik altyapınızı güvenli hale getirmenin beş adımı](../../security/fundamentals/steps-secure-identity.md)
* [Azure Active Directory Koşullu erişim nedir?](overview.md)
