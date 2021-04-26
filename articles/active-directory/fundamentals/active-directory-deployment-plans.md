---
title: Dağıtım planları-Azure Active Directory | Microsoft Docs
description: Birçok Azure Active Directory özelliği dağıtma hakkında uçtan uca rehberlik.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4185ffd644d54c419f42c78326ca10bf100443c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99051439"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory dağıtım planları
Azure Active Directory (Azure AD) yeteneklerini dağıtmaya yönelik uçtan uca yönergeler mi arıyorsunuz? Azure AD dağıtım planları, yaygın Azure AD yeteneklerini başarıyla dağıtmak için gereken iş değeri, planlama konuları ve işlem yordamları boyunca size yol gösterir.

Herhangi bir plan sayfasında, belgelerin güncel çevrimdışı bir sürümünü oluşturmak için tarayıcınızın PDF 'ye yazdır özelliğini kullanın.


## <a name="deploy-authentication"></a>Kimlik doğrulaması dağıtma

| Özellik | Açıklama|
| -| -|
| [Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)| Azure AD Multi-Factor Authentication (MFA), Microsoft 'un iki adımlı doğrulama çözümüdür. Yönetici onaylı kimlik doğrulama yöntemlerini kullanarak, Azure AD MFA, basit bir oturum açma işlemi talebini karşılarken verilerinize ve uygulamalarınıza erişimi korumaya yardımcı olur. [Kiracınızda Multi-Factor Authentication 'ı yapılandırma ve zorunlu kılmak için](https://www.youtube.com/watch?v=qNndxl7gqVM) bu videoyu izleyin|
| [Koşullu Erişim](../conditional-access/plan-conditional-access.md)| Koşullu erişimle, koşullara göre bulut uygulamalarınıza kimlerin erişebileceği otomatik erişim denetimi kararları uygulayabilirsiniz. |
| [Self servis parola sıfırlama](../authentication/howto-sspr-deployment.md)| Self servis parola sıfırlama, kullanıcılarınızın parolalarını yönetici müdahalesi olmadan sıfırlamasına, ne zaman ve nerede ihtiyaç duymalarına yardımcı olur. |
| [Parolasız](../authentication/howto-authentication-passwordless-deployment.md) | Kuruluşunuzda Microsoft Authenticator App veya FIDO2 güvenlik anahtarlarını kullanarak passwordless kimlik doğrulaması uygulama |

## <a name="deploy-application-and-device-management"></a>Uygulama ve cihaz yönetimini dağıtma

| Özellik | Açıklama|
| -| - |
| [Çoklu oturum açma](../manage-apps/plan-sso-deployment.md)| Çoklu oturum açma, kullanıcılarınızın yalnızca bir kez oturum açarken iş yapması gereken uygulamalara ve kaynaklara erişmesine yardımcı olur. Oturum açtıktan sonra, kimlik bilgilerini ikinci bir kez girmek zorunda kalmadan iç uygulamalara Microsoft Office, SalesForce 'a kadar gidebilirler. |
| [Uygulamalarım](../manage-apps/my-apps-deployment-plan.md)| Kullanıcılarınıza tüm uygulamalarını keşfetmek ve bunlara erişmek için basit bir hub sunun. Uygulamalar ve gruplar için erişim isteğinde bulunma veya diğerleri adına kaynaklara erişimi yönetme gibi self servis özellikleri ile daha üretken olmalarını sağlayın. |
| [Cihazlar](../devices/plan-device-deployment.md) | Bu makale, cihazınızı Azure AD ile tümleştirme yöntemlerini değerlendirmenize yardımcı olur, uygulama planını seçebilir ve desteklenen cihaz yönetim araçlarına yönelik anahtar bağlantıları sağlar. |


## <a name="deploy-hybrid-scenarios"></a>Karma senaryoları dağıtma

| Özellik | Açıklama|
| -| -|
| [Parola Karması Eşitleme için ADFS](../hybrid/plan-migrate-adfs-password-hash-sync.md)| Parola karması eşitlemeyle, kullanıcı parolalarının karmaları şirket içi Active Directory Azure AD 'ye eşitlenir, bu da Azure AD 'nin kullanıcıların şirket içi bir etkileşim olmadan kimlik doğrulaması yapmasına olanak sağlar Active Directory |
| [Doğrudan Geçiş Kimlik Doğrulaması için ADFS](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| Azure AD geçişli kimlik doğrulaması, kullanıcılarınızın aynı parolaları kullanarak hem şirket içi hem de bulut tabanlı uygulamalarda oturum açmasını sağlar. Bu özellik, kullanıcılara daha fazla deneyim sağlamak için daha az bir parola sağlar ve kullanıcılar oturum açmayı unutmamak daha az olabileceğinden BT yardım masası maliyetlerini azaltır. Kullanıcılar Azure AD'de oturum açtığında bu özellik parolaları doğrudan şirket için Active Directory dizininizde doğrular. |
| [Azure AD Uygulama Ara Sunucusu](../manage-apps/application-proxy-deployment-plan.md) |Günümüzde çalışanlar her yerden, her zaman ve tüm cihazlardan çalışmak istemektedir. Bulut ve şirket içi uygulamalarda SaaS uygulamalarına erişmesi gerekir. Azure AD uygulama proxy 'si, pahalı ve karmaşık sanal özel ağlar (VPN) veya sivil bölgeler (DMZs) olmadan bu güçlü erişimi mümkün hale getirmenizi sunar. |
| [Sorunsuz çoklu oturum açma](../hybrid/how-to-connect-sso-quick-start.md)| Azure Active Directory Sorunsuz Çoklu Oturum Açma (Azure AD Sorunsuz SSO) özelliği, kurumsal ağınıza bağlı kuruluş cihazlarını kullanan kullanıcıların otomatik olarak oturum açmasını sağlar. Bu özellikle, kullanıcıların Azure AD 'de oturum açması için parolalarını girmesi gerekmez ve genellikle kullanıcı adlarını girmesi gerekmez. Bu özellik, yetkili kullanıcılara ek şirket içi bileşenlere gerek duymadan bulut tabanlı uygulamalarınıza kolay erişim sağlar. |

## <a name="deploy-user-provisioning"></a>Kullanıcı sağlamayı dağıtma

| Özellik | Açıklama|
| -| -|
| [Kullanıcı sağlama](../app-provisioning/plan-auto-user-provisioning.md)| Azure AD; Dropbox, Salesforce ve ServiceNow gibi bulut (SaaS) uygulamalarında kullanıcı oluşturma, bakım ve kaldırma adımlarını otomatikleştirmenize yardımcı olur. |
| [Bulut HR Kullanıcı sağlama](../app-provisioning/plan-cloud-hr-provision.md)| Active Directory için bulut HR Kullanıcı sağlama, devam eden kimlik yönetimi için bir temel oluşturur ve yetkili kimlik verilerine dayanan iş işlemlerinin kalitesini geliştirir. Bu özelliği, iş günü veya başarılı faktörleri gibi bulut HR ürününüzle birlikte kullanarak, birleştirici ve iş çalışanlarının kimlik yaşam döngüsünü sorunsuz bir şekilde yönetebilir (yeni Işe alma, sonlandırma, aktarma gibi) BT sağlama eylemlerine (örneğin, oluşturma, etkinleştirme, devre dışı bırakma) eşlenen kuralları yapılandırarak çalışanların kimlik ömrünü sorunsuzca yönetebilirsiniz |

## <a name="deploy-governance-and-reporting"></a>İdare ve raporlama dağıtma

| Özellik | Açıklama|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD Privileged Identity Management (PıM), Azure AD, Azure kaynakları ve diğer Microsoft Online Services genelinde ayrıcalıklı yönetim rollerini yönetmenize yardımcı olur. PıM, tam zamanında erişim, onay iş akışları isteme ve tam olarak tümleşik erişim incelemeleri gibi çözümler sağlayarak ayrıcalıklı rollerin kötü amaçlı etkinliklerini belirlemek, açmak ve gerçek zamanlı olarak engellemek için. |
| [Raporlama ve Izleme](../reports-monitoring/plan-monitoring-and-reporting.md)| Azure AD raporlama ve izleme çözümünüzün tasarımı, yasal, güvenlik ve işletimsel gereksinimlerinize ek olarak mevcut ortamınız ve süreçlerinize bağlıdır. Bu makalede, çeşitli tasarım seçenekleri sunulmakta ve doğru dağıtım stratejisinde size rehberlik sunulmaktadır. |
| [Erişim Incelemeleri](../governance/deploy-access-reviews.md) | Erişim Incelemeleri, yönetim stratejinizin önemli bir parçasıdır. bu sayede kimlerin erişimi olduğunu ve neleri erişebileceğini bilmenize ve yönetmenize olanak sağlar. Bu makale, istediğiniz güvenlik ve işbirliği sonrası için erişim gözden geçirmeleri planlayıp dağıtmanıza yardımcı olur. |

## <a name="include-the-right-stakeholders"></a>Doğru paydaşları dahil et

Dağıtım planlamasına yeni bir özellik için Başlarken, kuruluşunuzda önemli paydaşlar eklemek önemlidir. Aşağıdaki rollerin her birini karşılayan kişiyi veya kişileri tanımlamanızı ve belgeleyeceğini ve projede katılımlarını belirlemek için bunlarla çalışmayı öneririz.  

Roller aşağıdaki gibi bulunabilir 

|Rol |Açıklama |
|-|-|
|Son Kullanıcı|Özelliği uygulanacak olan temsili Kullanıcı grubu. Genellikle bir pilot programdaki değişikliklere önizleme gösterir.
|BT Destek Yöneticisi|Bu değişikliğin bir yardım masası perspektifinden desteklenebilirliği hakkında giriş sağlayabilen kuruluş temsilcisini destekler.  
|Kimlik mimarı veya Azure genel Yöneticisi|Bu değişikliğin kuruluşunuzdaki çekirdek kimlik yönetimi altyapısına nasıl hizalandığını tanımlamaya yönelik kimlik yönetimi ekibi temsilcisi.|
|Uygulama Iş sahibi |Erişim yönetimi de içerebilen, etkilenen uygulamaların genel iş sahibidir.Ayrıca, son kullanıcının perspektifinden bu değişikliğin kullanıcı deneyiminde ve yararlılığı üzerinde giriş sağlayabilir.
|Güvenlik sahibi|Güvenlik ekibinden, planın kuruluşunuzun güvenlik gereksinimlerini karşıladığını kapatan bir temsilci.|
|Uyumluluk Yöneticisi|Kuruluşunuzda kurumsal, sektör veya kamu gereksinimleriyle uyumluluk sağlamaktan sorumlu kişi.|

**Katılma düzeyleri şunları içerebilir:**

- Proje planı ve sonucu uygulamak için **R** esponerişilebilir 

- Proje planının ve sonucunun pproval 'i 

- **C** ontributor-proje planı ve sonucu 

- Proje planı ve sonucu **oluşturdum**


## <a name="best-practices-for-a-pilot"></a>Pilot için en iyi uygulamalar
Pilot, herkes için bir özelliği açmadan önce küçük bir grupla test etmenizi sağlar. Test etmenin bir parçası olarak, kuruluşunuzdaki her kullanım durumunun büyük bir şekilde sınandığından emin olun. Bunu kuruluşunuza bir bütün olarak yapmadan önce belirli bir pilot kullanıcı grubunu hedefleyecek en iyisidir.

İlk dalga uygulamanızda BT 'yi, kullanılabilirliği ve geri bildirim sağlayabilen ve bunları sağlayabilecek diğer uygun kullanıcıları hedefleyebilirsiniz. Bu geri bildirim, kullanıcılarınıza göndereceğiniz iletişimleri ve talimatları daha fazla geliştirmek ve destek personelinizin görebileceği sorun türleri hakkında Öngörüler sağlamak için kullanılmalıdır. 

Daha büyük Kullanıcı gruplarına dağıtım, hedeflenen Grup kapsamı arttırılarak gerçekleştirilmelidir. Bu, [dinamik grup üyeliği](../enterprise-users/groups-dynamic-membership.md)aracılığıyla veya hedeflenen gruplara el ile Kullanıcı ekleyerek yapılabilir.
