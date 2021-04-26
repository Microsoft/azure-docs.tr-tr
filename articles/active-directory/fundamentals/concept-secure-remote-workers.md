---
title: Azure Active Directory ile güvenli kimliklere hızlı bir şekilde yanıt verin
description: Azure AD bulut tabanlı kimliklerle tehditleri hızla yanıtlayın
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c4301a61a79ab2351c18af0c76cccc3d07dd202
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836691"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>Azure Active Directory ile güvenli kimliklere hızla yanıt verme

Özellikle hızla yanıt vermeniz ve birçok hizmete hızlı bir şekilde erişim sağlamanız gerektiğinde, günümüzün dünyanın her yerindeki çalışanlarınızı güvenli hale getirmeye çalışırken çok daha fazla görünebilir. Bu makale, sahip olduğunuz lisans türüne göre Azure AD özelliklerinin hangi sırada dağıtılacağını belirlemenize ve önceliklendirmenize yardımcı olan tüm eylemlerin kısa bir listesini sağlamak için tasarlanmıştır. Azure AD birçok özellik sunar ve kimlikleriniz için pek çok güvenlik katmanı sağlar ve hangi özelliğin ilgili olduğunu, bazen önemli bir şekilde kullanılabilir. Birçok kuruluş zaten bulutta veya hızla buluta taşınıyorsa, bu belge, kimlik bilgilerinizi birincil göz önünde bulundurarak güvenli bir şekilde dağıtmanıza olanak tanımak için tasarlanmıştır. 

Her tablo, Kullanıcı etkisini en aza indirerek ve Kullanıcı deneyimini geliştirirken ana güvenlik saldırılarından (ihlal yürütme, kimlik avı ve parola spreyi) hem yönetici hem de Kullanıcı kimliklerini koruyan tutarlı bir güvenlik önerisi sağlar. 

Bu kılavuzda, yöneticilerin SaaS ve şirket içi uygulamalarına güvenli ve korumalı bir şekilde erişimi yapılandırmasına ve bulut ya da karma (eşitlenmiş) kimlikler için geçerli olması ve uzaktan ya da ofiste çalışan kullanıcılara uygulanması sağlanır.

Bu denetim listesi, aşağıdaki adımları izleyerek kuruluşunuzu hemen korumak için kritik önerilen eylemleri hızlı bir şekilde dağıtmanıza yardımcı olur:

- Kimlik bilgilerinizi güçlendirin.
- Saldırı yüzeyi alanınızı küçültün.
- Tehdit yanıtını otomatikleştirin.
- Bulut zekası kullanın.
- Son Kullanıcı self servis hizmetini etkinleştirin.

## <a name="prerequisites"></a>Önkoşullar

Bu kılavuzda yalnızca bulutunuzun veya karma kimliklerinin Azure AD 'de zaten kurulduğu varsayılmaktadır. Kimlik türünü seçme konusunda yardım için, [Azure Active Directory karma kimlik çözümünüz için doğru kimlik doğrulama yöntemini seçin](../hybrid/choose-ad-authn.md) . 

## <a name="summary"></a>Özet

Güvenli bir kimlik altyapısının birçok yönü vardır, ancak bu denetim listesi, kullanıcıların uzaktan çalışmasını sağlayan güvenli ve güvenli bir kimlik altyapısına odaklanır. Kimliğinizi güvenli hale getirmenin güvenlik hikayenizi yalnızca bir parçası, verileri, uygulamaları ve cihazları korumanın de dikkate alınması gerekir.

### <a name="guidance-for-azure-ad-free-office-365-or-microsoft-365-customers"></a>Azure AD Ücretsiz, Office 365 veya Microsoft 365 müşterileri için rehberlik.

Azure AD Ücretsiz, Office 365 veya Microsoft 365 uygulama müşterilerinin Kullanıcı kimliklerini korumalarına yönelik olması gereken birkaç öneri vardır: Aşağıdaki tablo, aşağıdaki lisans abonelikleri için önemli eylemleri vurgulamaya yöneliktir:

- Office 365 (Office 365 E1, E3, E5, F1, a1, a3, A5)
- Microsoft 365 (Iş temel, Iş için uygulamalar, Iş standardı, Iş Premium, a1)
- Azure AD Ücretsiz (Azure, Dynamics 365, Intune ve güç platformu ile birlikte)

| Önerilen eylem | Ayrıntı |
| --- | --- |
| [Güvenlik varsayılanlarını etkinleştir](concept-fundamentals-security-defaults.md) | MFA 'yı etkinleştirerek ve eski kimlik doğrulamasını engelleyerek tüm Kullanıcı kimliklerini ve uygulamalarını koruyun |
| [Parola karma eşitlemesini etkinleştir](../hybrid/how-to-connect-password-hash-synchronization.md) (karma kimlikler kullanılıyorsa) | Kimlik doğrulaması için artıklık sağlayın ve güvenliği geliştirebilirsiniz (akıllı kilitleme, IP kilitleme ve sızdırılan kimlik bilgilerini bulma özelliği de dahil). |
| [ADFS akıllı kilitleme aşımını etkinleştir](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (varsa) | Kullanıcılarınızın kötü amaçlı etkinlikten extranet hesabı kilitlemesini yaşmalarını önler. |
| [Akıllı kilitleme Azure Active Directory etkinleştir](../authentication/howto-password-smart-lockout.md) (Yönetilen kimlikler kullanılıyorsa) | Akıllı kilitleme, kullanıcılarınızın parolalarını tahmin etmeye çalışan kötü aktörlerin kilitlenmesini sağlar veya almak için deneme yanılma yöntemlerini kullanır. |
| [Son Kullanıcı onayını uygulamalara devre dışı bırak](../manage-apps/configure-user-consent.md) | Yönetici onayı iş akışı yöneticilere yönetici onayı gerektiren uygulamalara erişim vermek için güvenli bir yol sağlar. böylece son kullanıcılar kurumsal verileri kullanıma sunmazlar. Microsoft, Surface alanınızı azaltmaya ve bu riski azaltmaya yardımcı olmak için gelecekteki Kullanıcı onay işlemlerinin devre dışı bırakılmasını öneriyor. |
| [Desteklenen SaaS uygulamalarını Galeriden Azure AD 'ye tümleştirin ve çoklu oturum açmayı etkinleştirin](../manage-apps/add-application-portal.md) | Azure AD 'nin binlerce önceden tümleştirilmiş uygulama içeren bir Galerisi vardır. Kuruluşunuzun kullandığı uygulamalardan bazıları büyük olasılıkla galeride doğrudan Azure portal erişilebilir. Geliştirilmiş kullanıcı deneyimiyle (SSO) Şirket SaaS uygulamalarına uzaktan ve güvenle erişim sağlama |
| [SaaS uygulamalarından Kullanıcı hazırlama ve sağlamayı kaldırma Işlemini otomatikleştirme](../app-provisioning/user-provisioning.md) (varsa) | Kullanıcıların erişmesi gereken bulut (SaaS) uygulamalarında Kullanıcı kimliklerini ve rolleri otomatik olarak oluşturun. Otomatik sağlama, kullanıcı kimliklerinin oluşturulmasına ek olarak, durum veya rol değişikliği olarak Kullanıcı kimliklerinin bakımını ve kaldırılmasını içerir ve kuruluşunuzun güvenliğini artırır. |
| [Güvenli karma erişimi etkinleştir: mevcut uygulama teslim denetleyicileri ve ağlarla (varsa) eski uygulamaları güvenli hale](../manage-apps/secure-hybrid-access.md) getirin | Mevcut uygulama teslim denetleyicinize veya ağınıza Azure AD 'ye bağlanarak şirket içi ve bulut eski kimlik doğrulama uygulamalarınızı yayımlayın ve koruyun. |
| [Self servis parola sıfırlamayı etkinleştir](../authentication/tutorial-enable-sspr.md) (yalnızca bulut hesapları için geçerlidir) | Bu özellik, bir Kullanıcı cihazlarındaki veya bir uygulamada oturum açarken yardım masası çağrılarını ve üretkenlik kaybını azaltır. |
| [Mümkün olduğunda genel olmayan yönetim rolleri kullanın](../roles/permissions-reference.md) | Yöneticilerinize yalnızca erişmesi gereken alanlara yalnızca ihtiyaç duydukları erişimi verin. Tüm yöneticilerin genel yönetici olması gerekmez. |
| [Microsoft 'un parola kılavuzunu etkinleştir](https://www.microsoft.com/research/publication/password-guidance/) | Kullanıcıların bir küme zamanlaması üzerinde parolalarını değiştirmesini, karmaşıklık gereksinimlerini devre dışı bırakmayı ve kullanıcılarınızın parolalarını anımsamasını ve bunları güvenli bir şekilde tutmasını sağlama gereğini durdur. |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Azure AD Premium plan 1 müşterileri için rehberlik.

Aşağıdaki tablo, aşağıdaki lisans abonelikleriyle ilgili önemli eylemleri vurgulamaya yöneliktir:

- Azure Active Directory Premium P1 (Azure AD P1)
- Enterprise Mobility + Security (EMS E3)
- Microsoft 365 (M365 E3, a3, F1, F3)

| Önerilen eylem | Ayrıntı |
| --- | --- |
| [Kullanıcı kayıt deneyimini basitleştirmek için Azure AD MFA ve SSPR için Birleşik kayıt deneyimini etkinleştirin](../authentication/howto-registration-mfa-sspr-combined.md) | Kullanıcılarınızın hem Azure AD Multi-Factor Authentication hem de Self servis parola sıfırlama için bir ortak deneyimden kaydolmalarına izin verin. |
| [Kuruluşunuz için MFA ayarlarını yapılandırma](../authentication/howto-mfa-getstarted.md) | Hesapların Multi-Factor Authentication ile güvenliğinin aşılmasına karşı korunduğundan emin olun |
| [Kendi kendine parola sıfırlamayı etkinleştirme](../authentication/tutorial-enable-sspr.md) | Bu özellik, bir Kullanıcı cihazlarındaki veya bir uygulamada oturum açarken yardım masası çağrılarını ve üretkenlik kaybını azaltır |
| [Parola geri yazmayı Uygula](../authentication/tutorial-enable-sspr-writeback.md) (karma kimlikler kullanılıyorsa) | Bulutta parola değişikliklerinin şirket içi Windows Server Active Directory ortamına geri yazılmasına izin verin. |
| Koşullu erişim ilkeleri oluşturma ve etkinleştirme | [Yöneticiler için yönetim hakları atanmış hesapları koruma MFA.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Eski kimlik doğrulama protokolleriyle ilişkili risk artışı nedeniyle eski kimlik doğrulama protokollerini engelleyin.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Kullanıcılarınızın ve uygulamalarınızın güvenliğini sağlamak için tüm kullanıcıların ve uygulamaların, ortamınız için dengeli bir MFA ilkesi oluşturması için MFA.](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [Azure kaynaklarına erişen herhangi bir kullanıcı için Multi-Factor Authentication 'ı zorunlu kılarak, Azure yönetimi için MFA 'yı, ayrıcalıklı kaynaklarınızın korunmasını isteyin.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Parola karma eşitlemesini etkinleştir](../hybrid/how-to-connect-password-hash-synchronization.md) (karma kimlikler kullanılıyorsa) | Kimlik doğrulaması için artıklık sağlayın ve güvenliği geliştirebilirsiniz (akıllı kilitleme, IP kilitleme ve sızdırılan kimlik bilgilerini bulma özelliği de dahil). |
| [ADFS akıllı kilitleme aşımını etkinleştir](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (varsa) | Kullanıcılarınızın kötü amaçlı etkinlikten extranet hesabı kilitlemesini yaşmalarını önler. |
| [Akıllı kilitleme Azure Active Directory etkinleştir](../authentication/howto-password-smart-lockout.md) (Yönetilen kimlikler kullanılıyorsa) | Akıllı kilitleme, kullanıcılarınızın parolalarını tahmin etmeye çalışan kötü aktörlerin kilitlenmesini sağlar veya almak için deneme yanılma yöntemlerini kullanır. |
| [Son Kullanıcı onayını uygulamalara devre dışı bırak](../manage-apps/configure-user-consent.md) | Yönetici onayı iş akışı yöneticilere yönetici onayı gerektiren uygulamalara erişim vermek için güvenli bir yol sağlar. böylece son kullanıcılar kurumsal verileri kullanıma sunmazlar. Microsoft, Surface alanınızı azaltmaya ve bu riski azaltmaya yardımcı olmak için gelecekteki Kullanıcı onay işlemlerinin devre dışı bırakılmasını öneriyor. |
| [Uygulama proxy 'Si ile şirket içi eski uygulamalara uzaktan erişimi etkinleştirme](../manage-apps/application-proxy-add-on-premises-application.md) | Azure AD Uygulama Ara Sunucusu etkinleştirin ve kullanıcıların Azure AD hesabıyla oturum açarak şirket içi uygulamalara güvenli bir şekilde erişmesini sağlamak için eski uygulamalarla tümleştirin. |
| [Güvenli karma erişimi etkinleştir: eski uygulamaları, mevcut uygulama teslim denetleyicileri ve ağlarla](../manage-apps/secure-hybrid-access.md) (varsa) güvenli hale getirin. | Mevcut uygulama teslim denetleyicinize veya ağınıza Azure AD 'ye bağlanarak şirket içi ve bulut eski kimlik doğrulama uygulamalarınızı yayımlayın ve koruyun. |
| [Desteklenen SaaS uygulamalarını Galeriden Azure AD 'ye tümleştirin ve çoklu oturum açmayı etkinleştirin](../manage-apps/add-application-portal.md) | Azure AD 'nin binlerce önceden tümleştirilmiş uygulama içeren bir Galerisi vardır. Kuruluşunuzun kullandığı uygulamalardan bazıları büyük olasılıkla galeride doğrudan Azure portal erişilebilir. Geliştirilmiş kullanıcı deneyimi (SSO) sayesinde kurumsal SaaS uygulamalarına uzaktan ve güvenle erişim sağlayın. |
| [SaaS uygulamalarından Kullanıcı hazırlama ve sağlamayı kaldırma Işlemini otomatikleştirme](../app-provisioning/user-provisioning.md) (varsa) | Kullanıcıların erişmesi gereken bulut (SaaS) uygulamalarında Kullanıcı kimliklerini ve rolleri otomatik olarak oluşturun. Otomatik sağlama, kullanıcı kimliklerinin oluşturulmasına ek olarak, durum veya rol değişikliği olarak Kullanıcı kimliklerinin bakımını ve kaldırılmasını içerir ve kuruluşunuzun güvenliğini artırır. |
| [Koşullu erişimi etkinleştirme – cihaz tabanlı](../conditional-access/require-managed-devices.md) | Cihaz tabanlı koşullu erişimle güvenlik ve kullanıcı deneyimlerini geliştirebilirsiniz. Bu adım, kullanıcıların yalnızca güvenlik ve uyumluluk standartlarınızı karşılayan cihazlardan erişmesini sağlar. Bu cihazlar yönetilen cihazlar olarak da bilinir. Yönetilen cihazlar Intune ile uyumlu veya hibrit Azure AD 'ye katılmış cihazlar olabilir. |
| [Parola korumasını etkinleştir](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Kullanıcıların zayıf ve kolay parolaların tahmin etmesini koruyun. |
| [Birden fazla genel yönetici belirleyin](../roles/security-emergency-access.md) | Acil durumda kullanılmak üzere en az iki bulut kalıcı genel yönetici hesabı atayın. Bu hesaplar günlük olarak kullanılmaz ve uzun ve karmaşık parolalara sahip olmalıdır. Cam hesapları kesme bir acil durumda hizmete erişebildiğinizden emin olun. |
| [Mümkün olduğunda genel olmayan yönetim rolleri kullanın](../roles/permissions-reference.md) | Yöneticilerinize yalnızca erişmesi gereken alanlara yalnızca ihtiyaç duydukları erişimi verin. Tüm yöneticilerin genel yönetici olması gerekmez. |
| [Microsoft 'un parola kılavuzunu etkinleştir](https://www.microsoft.com/research/publication/password-guidance/) | Kullanıcıların bir küme zamanlaması üzerinde parolalarını değiştirmesini, karmaşıklık gereksinimlerini devre dışı bırakmayı ve kullanıcılarınızın parolalarını anımsamasını ve bunları güvenli bir şekilde tutmasını sağlama gereğini durdur. |
| [Konuk Kullanıcı erişimi için bir plan oluşturun](../external-identities/what-is-b2b.md) | Kendi iş, okul veya sosyal kimliklerle uygulama ve hizmetlerinizde oturum açmalarına izin vererek Konuk kullanıcılarla işbirliği yapın. |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Azure AD Premium plan 2 müşterileri için rehberlik.

Aşağıdaki tablo, aşağıdaki lisans abonelikleriyle ilgili önemli eylemleri vurgulamaya yöneliktir:

- Azure Active Directory Premium P2 (Azure AD P2)
- Enterprise Mobility + Security (EMS E5)
- Microsoft 365 (M365 E5, A5)

| Önerilen eylem | Ayrıntı |
| --- | --- |
| [Kullanıcı kayıt deneyimini basitleştirmek için Azure AD MFA ve SSPR için Birleşik kayıt deneyimini etkinleştirin](../authentication/howto-registration-mfa-sspr-combined.md) | Kullanıcılarınızın hem Azure AD Multi-Factor Authentication hem de Self servis parola sıfırlama için bir ortak deneyimden kaydolmalarına izin verin. |
| [Kuruluşunuz için MFA ayarlarını yapılandırma](../authentication/howto-mfa-getstarted.md) | Hesapların Multi-Factor Authentication ile güvenliğinin aşılmasına karşı korunduğundan emin olun |
| [Kendi kendine parola sıfırlamayı etkinleştirme](../authentication/tutorial-enable-sspr.md) | Bu özellik, bir Kullanıcı cihazlarındaki veya bir uygulamada oturum açarken yardım masası çağrılarını ve üretkenlik kaybını azaltır |
| [Parola geri yazmayı Uygula](../authentication/tutorial-enable-sspr-writeback.md) (karma kimlikler kullanılıyorsa) | Bulutta parola değişikliklerinin şirket içi Windows Server Active Directory ortamına geri yazılmasına izin verin. |
| [MFA kaydını zorlamak için kimlik koruma ilkelerini etkinleştirme](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | Azure AD Multi-Factor Authentication (MFA) toplu olarak yönetin. |
| [Kimlik koruması Kullanıcı ve oturum açma risk ilkelerini etkinleştirme](../identity-protection/howto-identity-protection-configure-risk-policies.md) | Kimlik koruması Kullanıcı ve oturum açma ilkelerini etkinleştirin. Önerilen oturum açma ilkesi, orta riskli oturum açma işlemlerini hedeflemek ve MFA gerektirmelidir. Kullanıcı ilkeleri için parola değiştirme eylemini gerektiren yüksek riskli kullanıcıları hedeflemelidir. |
| Koşullu erişim ilkeleri oluşturma ve etkinleştirme | [Yöneticiler için yönetim hakları atanmış hesapları koruma MFA.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Eski kimlik doğrulama protokolleriyle ilişkili risk artışı nedeniyle eski kimlik doğrulama protokollerini engelleyin.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Azure kaynaklarına erişen herhangi bir kullanıcı için Multi-Factor Authentication 'ı zorunlu kılarak, Azure yönetimi için MFA 'yı, ayrıcalıklı kaynaklarınızın korunmasını isteyin.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Parola karma eşitlemesini etkinleştir](../hybrid/how-to-connect-password-hash-synchronization.md) (karma kimlikler kullanılıyorsa) | Kimlik doğrulaması için artıklık sağlayın ve güvenliği geliştirebilirsiniz (akıllı kilitleme, IP kilitleme ve sızdırılan kimlik bilgilerini bulma özelliği de dahil). |
| [ADFS akıllı kilitleme aşımını etkinleştir](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (varsa) | Kullanıcılarınızın kötü amaçlı etkinlikten extranet hesabı kilitlemesini yaşmalarını önler. |
| [Akıllı kilitleme Azure Active Directory etkinleştir](../authentication/howto-password-smart-lockout.md) (Yönetilen kimlikler kullanılıyorsa) | Akıllı kilitleme, kullanıcılarınızın parolalarını tahmin etmeye çalışan kötü aktörlerin kilitlenmesini sağlar veya almak için deneme yanılma yöntemlerini kullanır. |
| [Son Kullanıcı onayını uygulamalara devre dışı bırak](../manage-apps/configure-user-consent.md) | Yönetici onayı iş akışı yöneticilere yönetici onayı gerektiren uygulamalara erişim vermek için güvenli bir yol sağlar. böylece son kullanıcılar kurumsal verileri kullanıma sunmazlar. Microsoft, Surface alanınızı azaltmaya ve bu riski azaltmaya yardımcı olmak için gelecekteki Kullanıcı onay işlemlerinin devre dışı bırakılmasını öneriyor. |
| [Uygulama proxy 'Si ile şirket içi eski uygulamalara uzaktan erişimi etkinleştirme](../manage-apps/application-proxy-add-on-premises-application.md) | Azure AD Uygulama Ara Sunucusu etkinleştirin ve kullanıcıların Azure AD hesabıyla oturum açarak şirket içi uygulamalara güvenli bir şekilde erişmesini sağlamak için eski uygulamalarla tümleştirin. |
| [Güvenli karma erişimi etkinleştir: eski uygulamaları, mevcut uygulama teslim denetleyicileri ve ağlarla](../manage-apps/secure-hybrid-access.md) (varsa) güvenli hale getirin. | Mevcut uygulama teslim denetleyicinize veya ağınıza Azure AD 'ye bağlanarak şirket içi ve bulut eski kimlik doğrulama uygulamalarınızı yayımlayın ve koruyun. |
| [Desteklenen SaaS uygulamalarını Galeriden Azure AD 'ye tümleştirin ve çoklu oturum açmayı etkinleştirin](../manage-apps/add-application-portal.md) | Azure AD 'nin binlerce önceden tümleştirilmiş uygulama içeren bir Galerisi vardır. Kuruluşunuzun kullandığı uygulamalardan bazıları büyük olasılıkla galeride doğrudan Azure portal erişilebilir. Geliştirilmiş kullanıcı deneyimi (SSO) sayesinde kurumsal SaaS uygulamalarına uzaktan ve güvenle erişim sağlayın. |
| [SaaS uygulamalarından Kullanıcı hazırlama ve sağlamayı kaldırma Işlemini otomatikleştirme](../app-provisioning/user-provisioning.md) (varsa) | Kullanıcıların erişmesi gereken bulut (SaaS) uygulamalarında Kullanıcı kimliklerini ve rolleri otomatik olarak oluşturun. Otomatik sağlama, kullanıcı kimliklerinin oluşturulmasına ek olarak, durum veya rol değişikliği olarak Kullanıcı kimliklerinin bakımını ve kaldırılmasını içerir ve kuruluşunuzun güvenliğini artırır. |
| [Koşullu erişimi etkinleştirme – cihaz tabanlı](../conditional-access/require-managed-devices.md) | Cihaz tabanlı koşullu erişimle güvenlik ve kullanıcı deneyimlerini geliştirebilirsiniz. Bu adım, kullanıcıların yalnızca güvenlik ve uyumluluk standartlarınızı karşılayan cihazlardan erişmesini sağlar. Bu cihazlar yönetilen cihazlar olarak da bilinir. Yönetilen cihazlar Intune ile uyumlu veya hibrit Azure AD 'ye katılmış cihazlar olabilir. |
| [Parola korumasını etkinleştir](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Kullanıcıların zayıf ve kolay parolaların tahmin etmesini koruyun. |
| [Birden fazla genel yönetici belirleyin](../roles/security-emergency-access.md) | Acil durumda kullanılmak üzere en az iki bulut kalıcı genel yönetici hesabı atayın. Bu hesaplar günlük olarak kullanılmaz ve uzun ve karmaşık parolalara sahip olmalıdır. Cam hesapları kesme bir acil durumda hizmete erişebildiğinizden emin olun. |
| [Mümkün olduğunda genel olmayan yönetim rolleri kullanın](../roles/permissions-reference.md) | Yöneticilerinize yalnızca erişmesi gereken alanlara yalnızca ihtiyaç duydukları erişimi verin. Tüm yöneticilerin genel yönetici olması gerekmez. |
| [Microsoft 'un parola kılavuzunu etkinleştir](https://www.microsoft.com/research/publication/password-guidance/) | Kullanıcıların bir küme zamanlaması üzerinde parolalarını değiştirmesini, karmaşıklık gereksinimlerini devre dışı bırakmayı ve kullanıcılarınızın parolalarını anımsamasını ve bunları güvenli bir şekilde tutmasını sağlama gereğini durdur. |
| [Konuk Kullanıcı erişimi için bir plan oluşturun](../external-identities/what-is-b2b.md) | Kendi iş, okul veya sosyal kimliklerle uygulama ve hizmetlerinizde oturum açmalarına izin vererek Konuk kullanıcılarla işbirliği yapın. |
| [Privileged Identity Management etkinleştir](../privileged-identity-management/pim-configure.md) | , Kuruluşunuzdaki önemli kaynaklara erişimi yönetmenizi, denetlemenizi ve izlemenizi sağlar. böylece Yöneticiler yalnızca gerektiğinde ve onay ile erişime sahiptir |

## <a name="next-steps"></a>Sonraki adımlar

- Azure AD 'nin bireysel özelliklerine yönelik ayrıntılı dağıtım kılavuzu için [Azure AD proje dağıtım planlarını](active-directory-deployment-plans.md)gözden geçirin.

- Uçtan uca Azure AD Dağıtım denetim listesi için bkz. [özellik dağıtım kılavuzu Azure Active Directory](active-directory-deployment-checklist-p2.md) makalesi