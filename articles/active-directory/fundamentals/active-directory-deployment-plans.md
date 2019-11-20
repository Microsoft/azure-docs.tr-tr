---
title: Dağıtım planları-Azure Active Directory | Microsoft Docs
description: Birçok Azure Active Directory özelliği dağıtma hakkında uçtan uca rehberlik.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 949676c622c87831d22dcfc7e1bc6d920b622738
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473286"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory dağıtım planları
Azure Active Directory (Azure AD) yeteneklerini dağıtmaya yönelik uçtan uca yönergeler mi arıyorsunuz? Azure AD dağıtım planları, yaygın Azure AD yeteneklerini başarıyla dağıtmak için gereken iş değeri, planlama konuları ve işlem yordamları boyunca size yol gösterir.

Herhangi bir plan sayfasında, belgelerin güncel çevrimdışı bir sürümünü oluşturmak için tarayıcınızın PDF 'ye yazdır özelliğini kullanın.
## <a name="include-the-right-stakeholders"></a>Doğru paydaşları dahil et

Dağıtım planlamasına yeni bir özellik için Başlarken, kuruluşunuzda önemli paydaşlar eklemek önemlidir. Aşağıdaki rollerin her birini karşılayan kişiyi veya kişileri tanımlamanızı ve belgeleyeceğini ve projede katılımlarını belirlemek için bunlarla çalışmayı öneririz.  

Roller aşağıdaki gibi bulunabilir 

|Rol |Açıklama |
|-|-|
|Son Kullanıcı|Özelliği uygulanacak olan temsili Kullanıcı grubu. Genellikle bir pilot programdaki değişikliklere önizleme gösterir.
|BT Destek Yöneticisi|Bu değişikliğin bir yardım masası perspektifinden desteklenebilirliği hakkında giriş sağlayabilen kuruluş temsilcisini destekler.  
|Kimlik mimarı veya Azure genel Yöneticisi|Bu değişikliğin kuruluşunuzdaki çekirdek kimlik yönetimi altyapısına nasıl hizalandığını tanımlamaya yönelik kimlik yönetimi ekibi temsilcisi.|
|Uygulama Iş sahibi |Erişim yönetimi de içerebilen, etkilenen uygulamaların genel iş sahibidir.  Ayrıca, son kullanıcının perspektifinden bu değişikliğin kullanıcı deneyiminde ve yararlılığı üzerinde giriş sağlayabilir.
|Güvenlik sahibi|Güvenlik ekibinden, planın kuruluşunuzun güvenlik gereksinimlerini karşıladığını kapatan bir temsilci.|
|Uyumluluk Yöneticisi|Kuruluşunuzda kurumsal, sektör veya kamu gereksinimleriyle uyumluluk sağlamaktan sorumlu kişi.|

**Katılma düzeyleri şunları içerebilir:**

- Proje planı ve sonucu uygulamak için **R**esponerişilebilir 

- Proje planının ve sonucunun pproval 'i 

- **C**ontributor-proje planı ve sonucu 

- Proje planı ve sonucu **oluşturdum**


## <a name="best-practices-for-a-pilot"></a>Pilot için en iyi uygulamalar
Pilot, herkes için bir özelliği açmadan önce küçük bir grupla test etmenizi sağlar. Test etmenin bir parçası olarak, kuruluşunuzdaki her kullanım durumunun büyük bir şekilde sınandığından emin olun. Bunu kuruluşunuza bir bütün olarak yapmadan önce belirli bir pilot kullanıcı grubunu hedefleyecek en iyisidir.

İlk dalga uygulamanızda BT 'yi, kullanılabilirliği ve geri bildirim sağlayabilen ve bunları sağlayabilecek diğer uygun kullanıcıları hedefleyebilirsiniz. Bu geri bildirim, kullanıcılarınıza göndereceğiniz iletişimleri ve talimatları daha fazla geliştirmek ve destek personelinizin görebileceği sorun türleri hakkında Öngörüler sağlamak için kullanılmalıdır. 

Daha büyük Kullanıcı gruplarına dağıtım, hedeflenen Grup kapsamı arttırılarak gerçekleştirilmelidir. Bu, [dinamik grup üyeliği](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)aracılığıyla veya hedeflenen gruplara el ile Kullanıcı ekleyerek yapılabilir.


## <a name="deploy-authentication"></a>Kimlik doğrulaması dağıtma

| Özellik | Açıklama|
| -| -|
| [Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)| Azure Multi-Factor Authentication (MFA) Microsoft'un iki adımlı doğrulama çözümüdür. Azure MFA, yönetici onaylı kimlik doğrulama yöntemlerini kullanarak, basit bir oturum açma işlemi talebini karşılarken verilerinize ve uygulamalarınıza erişimi korumaya yardımcı olur. |
| [Koşullu erişim](https://aka.ms/deploymentplans/ca)| Koşullu erişimle, koşullara göre bulut uygulamalarınıza kimlerin erişebileceği otomatik erişim denetimi kararları uygulayabilirsiniz. |
| [Self servis parola sıfırlama](https://aka.ms/deploymentplans/sspr)| Self servis parola sıfırlama, kullanıcılarınızın parolalarını yönetici müdahalesi olmadan sıfırlamasına, ne zaman ve nerede ihtiyaç duymalarına yardımcı olur. |
| [Passwordless](https://aka.ms/deploymentplans/passwordless) | Kuruluşunuzda Microsoft Authenticator App veya FIDO2 güvenlik anahtarlarını kullanarak passwordless kimlik doğrulaması uygulama |

## <a name="deploy-application-management"></a>Uygulama yönetimini dağıtma

| Özellik | Açıklama|
| -| - |
| [Çoklu oturum açma](https://aka.ms/deploymentplans/sso)| Çoklu oturum açma, kullanıcılarınızın yalnızca bir kez oturum açarken iş yapması gereken uygulamalara ve kaynaklara erişmesine yardımcı olur. Oturum açtıktan sonra, kimlik bilgilerini ikinci bir kez girmek zorunda kalmadan iç uygulamalara Microsoft Office, SalesForce 'a kadar gidebilirler. |
| [Erişim paneli](https://aka.ms/deploymentplans/accesspanel)| Kullanıcılarınıza tüm uygulamalarını keşfetmek ve bunlara erişmek için basit bir hub sunun. Uygulamalar ve gruplar için erişim isteğinde bulunma veya diğerleri adına kaynaklara erişimi yönetme gibi self servis özellikleri ile daha üretken olmalarını sağlayın. |


## <a name="deploy-hybrid-scenarios"></a>Karma senaryoları dağıtma

| Özellik | Açıklama|
| -| -|
| [Parola Karması Eşitleme için ADFS](https://aka.ms/deploymentplans/adfs2phs)| Parola karması eşitlemeyle, kullanıcı parolalarının karmaları şirket içi Active Directory Azure AD 'ye eşitlenir, bu da Azure AD 'nin kullanıcıların şirket içi bir etkileşim olmadan kimlik doğrulaması yapmasına olanak sağlar Active Directory |
| [Doğrudan Geçiş Kimlik Doğrulaması için ADFS](https://aka.ms/deploymentplans/adfs2pta)| Azure AD geçişli kimlik doğrulaması, kullanıcılarınızın aynı parolaları kullanarak hem şirket içi hem de bulut tabanlı uygulamalarda oturum açmasını sağlar. Bu özellik, kullanıcılara daha fazla deneyim sağlamak için daha az bir parola sağlar ve kullanıcılar oturum açmayı unutmamak daha az olabileceğinden BT yardım masası maliyetlerini azaltır. Kullanıcılar Azure AD'de oturum açtığında bu özellik parolaları doğrudan şirket için Active Directory dizininizde doğrular. |
| [Azure AD Uygulama Ara Sunucusu](https://aka.ms/deploymentplans/appproxy)| Günümüzde çalışanlar her yerden, her zaman ve tüm cihazlardan çalışmak istemektedir. Bulut ve şirket içi uygulamalarda SaaS uygulamalarına erişmesi gerekir. Azure AD uygulama proxy 'si, pahalı ve karmaşık sanal özel ağlar (VPN) veya sivil bölgeler (DMZs) olmadan bu güçlü erişimi mümkün hale getirmenizi sunar. |
| [Sorunsuz çoklu oturum açma](https://aka.ms/SeamlessSSODPDownload)| Azure Active Directory Sorunsuz Çoklu Oturum Açma (Azure AD Sorunsuz SSO) özelliği, kurumsal ağınıza bağlı kuruluş cihazlarını kullanan kullanıcıların otomatik olarak oturum açmasını sağlar. Bu özellikle, kullanıcıların Azure AD 'de oturum açması için parolalarını girmesi gerekmez ve genellikle kullanıcı adlarını girmesi gerekmez. Bu özellik, yetkili kullanıcılara ek şirket içi bileşenlere gerek duymadan bulut tabanlı uygulamalarınıza kolay erişim sağlar. |

## <a name="deploy-user-provisioning"></a>Kullanıcı sağlamayı dağıtma

| Özellik | Açıklama|
| -| -|
| [Kullanıcı sağlama](https://aka.ms/deploymentplans/userprovisioning)| Azure AD; Dropbox, Salesforce ve ServiceNow gibi bulut (SaaS) uygulamalarında kullanıcı oluşturma, bakım ve kaldırma adımlarını otomatikleştirmenize yardımcı olur. |
| [Workday odaklı gelen Kullanıcı sağlama](https://aka.ms/WorkdayDeploymentPlan)| Active Directory iş günü odaklı gelen Kullanıcı sağlama, devam eden kimlik yönetimi için bir temel oluşturur ve yetkili kimlik verilerine dayanan iş işlemlerinin kalitesini geliştirir. Bu özelliği kullanarak, bu çalışanların ve contentik çalışanların kimlik yaşam döngüsünü, birleştirici-Mover-Leaver işlemlerini (örneğin, yeni Işe alma, sonlandırma, aktarım), BT sağlama eylemlerine (örneğin, oluşturma, etkinleştirme) eşleştiren kuralları yapılandırarak sorunsuzca yönetebilirsiniz. Dıı |

## <a name="deploy-governance-and-reporting"></a>İdare ve raporlama dağıtma

| Özellik | Açıklama|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Azure AD Privileged Identity Management (PıM), Azure AD, Azure kaynakları ve diğer Microsoft Online Services genelinde ayrıcalıklı yönetim rollerini yönetmenize yardımcı olur. PıM, tam zamanında erişim, onay iş akışları isteme ve tam olarak tümleşik erişim incelemeleri gibi çözümler sağlayarak ayrıcalıklı rollerin kötü amaçlı etkinliklerini belirlemek, açmak ve gerçek zamanlı olarak engellemek için. |
| [Raporlama ve Izleme](https://aka.ms/deploymentplans/reporting)| Azure AD raporlama ve izleme çözümünüzün tasarımı, yasal, güvenlik ve işletimsel gereksinimlerinize ek olarak mevcut ortamınız ve süreçlerinize bağlıdır. Bu makalede, çeşitli tasarım seçenekleri sunulmakta ve doğru dağıtım stratejisinde size rehberlik sunulmaktadır. |
