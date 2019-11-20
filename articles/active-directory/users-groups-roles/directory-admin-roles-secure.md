---
title: Güvenli yönetici erişimi için en iyi uygulamalar-Azure AD | Microsoft Docs
description: Kuruluşunuzun yönetim erişiminin ve yönetici hesaplarının güvenli olduğundan emin olun. Azure AD, Azure ve Microsoft Online hizmetlerini yapılandıran sistem mimarları ve BT uzmanları için.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 11/13/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: e65714f67dde79847bf07efda358a4e1f9ea938d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028448"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Azure AD 'de karma ve bulut dağıtımları için ayrıcalıklı erişimin güvenliğini sağlama

Modern kuruluştaki çoğu veya tüm iş varlıklarının güvenliği, BT sistemlerini yöneten ayrıcalıklı hesapların bütünlüğüne bağlıdır. Cyber-saldırganlar de dahil olmak üzere kötü amaçlı aktörler genellikle, kimlik bilgilerinin hırsızlık saldırıları ile hassas verilere ve sistemlere hızla erişim kazanmak için yönetici hesaplarını ve diğer ayrıcalıklı erişim öğelerini hedefler Bulut hizmetleri için, önleme ve yanıt, bulut hizmeti sağlayıcısının ve müşterinin ortak sorumluluklarına yöneliktir. Uç noktalara ve buluta yönelik en son tehditler hakkında daha fazla bilgi için [Microsoft Güvenlik Intelligence raporuna](https://www.microsoft.com/security/operations/security-intelligence-report)bakın. Bu makale, geçerli planlarınız ve burada açıklanan kılavuz arasındaki boşlukları kapatmaya yönelik bir yol haritası geliştirmenize yardımcı olabilir.

> [!NOTE]
> Microsoft, en yüksek düzeyde güven, saydamlık, standart uyumluluk ve mevzuat uyumluluğuna kararlıdır. Microsoft küresel olay yanıtı ekibinin, bulut hizmetlerine yönelik saldırıların etkilerini nasıl azaltmasının yanı sıra Microsoft Güven Merkezi- [Uyumluluk](https://www.microsoft.com/trustcenter/compliance)' de Microsoft [Trust Center-güvenlik](https://www.microsoft.com/trustcenter/security) ve Microsoft Uyumluluk hedefleri ' nde Microsoft iş ürünleri ve bulut Hizmetleri ' ne nasıl oluşturulduğu hakkında daha fazla bilgi edinin.

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
Çoğu kuruluş için iş varlıklarının güvenliği, BT sistemlerini yöneten ve yöneten ayrıcalıklı hesapların bütünlüğüne bağlıdır. Cyber-saldırganlar, kuruluşun hassas verilerine erişim kazanmak için altyapı sistemlerine (örneğin Active Directory ve Azure Active Directory) ayrıcalıklı Erişime odaklanmaktadır. 

Birincil güvenlik çevresi olarak bir ağın giriş ve çıkış noktalarının güvenliğini sağlamaya odaklanmaya odaklanılmış olan geleneksel yaklaşımlar, SaaS uygulamalarının ve kişisel cihazların Internet 'te kullanımıyla ilgili olarak daha az etkilidir. Karmaşık bir modern kuruluşta ağ güvenlik çevre 'nın doğal değişikliği, bir kuruluşun kimlik katmanındaki kimlik doğrulama ve yetkilendirme denetimleridir.

Ayrıcalıklı yönetim hesapları, bu yeni "güvenlik çevre" denetiminde etkili bir şekilde kontrol edilir. Ortamın şirket içi, bulut ya da karma şirket içi ve bulut tarafından barındırılan hizmetler olmasından bağımsız olarak ayrıcalıklı erişimin korunması önemlidir. Belirlenen yönetici erişiminin korunması, kuruluşunuzun sistemlerini risklerden yalıtmak için tamamen ve düşünceli bir yaklaşım yapmanızı gerektirir. 

Ayrıcalıklı erişimin güvenliğini sağlamak için değişiklikler gerekir

* Süreçler, yönetim uygulamaları ve bilgi yönetimi
* Ana bilgisayar savunmaları, hesap korumaları ve kimlik yönetimi gibi teknik bileşenler

Bu belge, birincil olarak Azure AD, Microsoft Azure, Office 365 ve diğer bulut hizmetlerinde yönetilen veya bildirilen kimliklerin ve erişimin güvenliğini sağlamak için bir yol haritası oluşturmaya odaklanır. Şirket içi yönetim hesapları olan kuruluşlar için, [ayrıcalıklı erişimin güvenliğini sağlamak](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)üzere Active Directory tarafından yönetilen şirket içi ve karma ayrıcalıklı erişim Kılavuzu ' na bakın. 

> [!NOTE] 
> Bu makaledeki kılavuz, birincil olarak Azure Active Directory Premium planlarına P1 ve P2 'a eklenen Azure Active Directory özelliklerine başvurur. Azure Active Directory Premium P2, EMS E5 Suite ve Microsoft 365 E5 Suite 'e dahildir. Bu kılavuzda, kuruluşunuzda kullanıcılarınız için satın alınan Azure AD Premium P2 lisansları zaten var. Bu lisanslarınız yoksa, bazı kılavuzlardan biri kuruluşunuza uygulanmayabilir. Ayrıca, bu makale boyunca genel yönetici (veya genel yönetici) terimi "Şirket Yöneticisi" veya "Kiracı Yöneticisi" ile eşanlamlıdır.

## <a name="develop-a-roadmap"></a>Yol haritası geliştirme 

Microsoft, siber saldırganlarına karşı ayrıcalıklı erişimi güvenli hale getirmek için bir yol haritası geliştirmenizi ve izlemenizi önerir. Her zaman yol haritasını, mevcut olanaklarınızı ve kuruluşunuzdaki belirli gereksinimleri karşılayacak şekilde ayarlayabilirsiniz. Yol haritası 'nın her aşaması, reklam işlemleri için, şirket içi, bulut ve karma varlıklarınız için ayrıcalıklı erişim saldırıları için maliyet ve zorluk derecesini yükseltmelidir. Microsoft aşağıdaki dört yol haritası aşamasını önerir: Bu önerilen yol haritası, Microsoft 'un Cyber saldırı olayı ve yanıt uygulamasıyla ilgili deneyimlerine bağlı olarak en etkili ve en hızlı uygulamaları zamanlar. Bu yol haritası için zaman çizelgeleri yaklaşık değerlerdir.

![Zaman çizgileri ile yol haritası aşamaları](./media/directory-admin-roles-secure/roadmap-timeline.png)

* 1\. aşama (24-48 saat): hemen yapmanızı önerdiğimiz kritik öğeler

* 2\. aşama (2-4 hafta): en sık kullanılan saldırı tekniklerini azaltma

* 3\. aşama (1-3 ay): yönetici etkinliğinin görünürlüğünü oluşturun ve tam denetim oluşturun

* 4\. aşama (altı ay ve daha fazla): güvenlik platformunuzu daha fazla savunmaya yönelik savunma oluşturmaya devam edin

Bu yol haritası çerçevesi, zaten dağıttığınız Microsoft teknolojilerinin kullanımını en üst düzeye çıkarmak için tasarlanmıştır. Ayrıca, geçerli ve yaklaşan güvenlik teknolojilerinden yararlanabilir ve önceden dağıttığınız veya dağıtımını ele aldığınız diğer satıcılardan güvenlik araçlarını tümleştirebilirsiniz. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>1\. Aşama: hemen yapmanız önerdiğimiz kritik öğeler

![İlk yapmanız için 1. aşama kritik öğe](./media/directory-admin-roles-secure/stage-one.png)

Yol haritasının 1. aşaması, hızlı ve kolay bir şekilde uygulanması gereken kritik görevlere odaklanılmıştır. Temel düzeyde güvenli ayrıcalıklı erişim sağlamak için bu birkaç öğeyi ilk 24-48 saat içinde hemen yapmanızı öneririz. Güvenli ayrıcalıklı erişim yol haritası 'nın bu aşaması aşağıdaki eylemleri içerir:

### <a name="general-preparation"></a>Genel hazırlık

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management aç

Azure AD Privileged Identity Management (PıM) zaten açık değilse üretim kiracınızda bunu yapın. Privileged Identity Management açtıktan sonra, ayrıcalıklı erişim rolü değişiklikleri için bildirim e-posta iletilerini alacaksınız. Bu bildirimler, dizininizde son derece ayrıcalıklı rollere ek kullanıcılar eklendiğinde erken uyarı sağlar.

Azure AD Privileged Identity Management, Azure AD Premium P2 veya EMS E5 'ye dahildir. Bu çözümler, şirket içi ortamda ve bulutta uygulamalara ve kaynaklara erişimi korumanıza yardımcı olur. Zaten Azure AD Premium P2 veya EMS E5 yoksa ve bu yol haritasında başvurulan özelliklerin daha fazlasını değerlendirmek istiyorsanız, [ücretsiz 90 günlük ücretsiz denemeye Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)kaydolun. Azure AD Privileged Identity Management ve Azure AD Kimlik Koruması denemek için bu lisans denemelerinden yararlanarak Azure AD gelişmiş güvenlik raporlama, denetim ve uyarılarını kullanarak etkinliği izleyin.

Azure AD Privileged Identity Management açtıktan sonra:

1. Üretim kiracınızın genel yöneticisi olan bir hesapla [Azure Portal](https://portal.azure.com/) oturum açın.

2. Privileged Identity Management kullanmak istediğiniz kiracıyı seçmek için, Azure portal sağ üst köşesindeki Kullanıcı adınızı seçin.

3. **Tüm hizmetler** ' i seçin ve **Azure AD Privileged Identity Management**listeyi filtreleyin.

4. **Tüm hizmetler** listesinden Privileged Identity Management açın ve panonuza sabitleyin.

Kiracınızda Azure AD Privileged Identity Management kullanmaya yönelik ilk kişi, Kiracıdaki **Güvenlik Yöneticisi** ve **ayrıcalıklı rol yöneticisi** rollerine otomatik olarak atanır. Yalnızca ayrıcalıklı rol yöneticileri kullanıcıların Azure AD dizin rolü atamalarını yönetebilir. Ayrıca, Azure AD Privileged Identity Management eklendikten sonra, ilk bulma ve atama deneyiminde size yol gösteren Güvenlik Sihirbazı gösterilir. Bu sırada başka bir değişiklik yapmadan sihirbazdan çıkabilirsiniz. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Yüksek ayrıcalıklı rollerdeki hesapları belirleme ve kategorilere ayırma 

Azure AD Privileged Identity Management açtıktan sonra, Dizin rolleri genel Yöneticisi, ayrıcalıklı rol yöneticisi, Exchange Online Yöneticisi ve SharePoint Online Yöneticisi içindeki kullanıcıları görüntüleyin. Kiracınızda Azure AD PıM 'niz yoksa [POWERSHELL API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)'sini kullanabilirsiniz. Bu rol genel olduğu için genel yönetici rolü ile başlayın: Bu yönetici rolüne atanan bir Kullanıcı, kuruluşunuzda abone olan tüm bulut hizmetlerinde aynı izinlere sahip olduğundan Microsoft 365 Bu rolün atanıp atanmadığına bakılmaksızın aynı izinlere sahiptir Yönetim Merkezi, Azure portal veya Microsoft PowerShell için Azure AD modülünü kullanarak. 

Bu rollerde artık gerekmeyen hesapları kaldırın. Ardından, yönetici rollerine atanan kalan hesapları kategorilere ayırın:

* Yönetici kullanıcılara ayrı ayrı atanır ve ayrıca yönetici olmayan amaçlar için de kullanılabilir (örneğin, kişisel e-posta)
* Tek tek yönetici kullanıcılara atanır ve yalnızca yönetim amacıyla belirlenir
* Birden çok kullanıcı arasında paylaşılıyor
* Kesme camı için acil durum erişim senaryoları
* Otomatikleştirilmiş betikler için
* Dış kullanıcılar için

#### <a name="define-at-least-two-emergency-access-accounts"></a>En az iki acil durum erişim hesabı tanımlayın 

Oturum açma veya mevcut bir kullanıcının hesabını yönetici olarak etkinleştirememe nedeniyle, Azure AD kiracınızın yönetiminden yanlışlıkla kilitlendikleri bir duruma ulaşmadığınızdan emin olun. Örneğin, kuruluş şirket içi bir kimlik sağlayıcısına federe ise, kullanıcıların şirket içinde oturum açması için bu kimlik sağlayıcısı kullanılamıyor olabilir. Kiracınızda iki veya daha fazla acil durum erişim hesabı depolayarak yönetim erişiminin yanlışlıkla ne kadar etkili olduğunu azaltabilirsiniz.

Acil erişim hesapları, kuruluşların mevcut bir Azure Active Directory ortamında ayrıcalıklı erişimi kısıtlamalarına yardımcı olur. Bu hesaplar son derece ayrıcalıklı ve belirli kişilere atanmamıştır. Acil durum erişim hesapları, normal yönetim hesaplarının kullanılabileceği ' kesme camı ' senaryolarında acil durum ile sınırlıdır. Kuruluşlar, acil durum hesabının kullanımını yalnızca gerekli olduğu zamana göre denetim altına almak ve azaltmak zorunda kalmaz.

Genel yönetici rolü için atanan veya uygun olan hesapları değerlendirin. *. Onmicrosoft.com etki alanını ("cam" acil erişim için tasarlanmıştır) kullanarak yalnızca herhangi bir bulut hesabı görmüyorsanız, bunları oluşturun. Daha fazla bilgi için bkz. [Azure AD 'de acil durum erişimi yönetim hesaplarını yönetme](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Multi-Factor Authentication 'ı açın ve diğer tüm yüksek ayrıcalıklı tek kullanıcılı yönetici hesaplarını kaydedin

Azure AD Yönetici rollerinin bir veya daha fazlasına kalıcı olarak atanan tüm bireysel kullanıcılar için oturum açma sırasında Azure Multi-Factor Authentication (MFA) iste: genel yönetici, ayrıcalıklı rol yöneticisi, Exchange Online Yöneticisi ve SharePoint Çevrimiçi yönetici. [Yönetici hesaplarınız Için Multi-Factor Authentication 'ı (MFA)](../authentication/howto-mfa-userstates.md) etkinleştirmek ve tüm bu kullanıcıların [https://aka.ms/mfasetup](https://aka.ms/mfasetup)kayıtlı olduğundan emin olmak için kılavuzu kullanın. 2\. adım ve [Office 365 ' de veri ve hizmetlere erişimi koruma](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)kılavuzunun 3. adımında daha fazla bilgi bulunabilir. 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>2\. Aşama: en sık kullanılan saldırı tekniklerini azaltma

![2\. aşama sık kullanılan saldırıları azaltır](./media/directory-admin-roles-secure/stage-two.png)

Yol haritası 'nın 2. aşaması, kimlik bilgilerinin hırsızlık ve kötüye kullanımı için en sık kullanılan saldırı tekniklerini azaltmaya odaklanır ve yaklaşık 2-4 hafta içinde uygulanabilir. Güvenli ayrıcalıklı erişim yol haritası 'nın bu aşaması aşağıdaki eylemleri içerir.

### <a name="general-preparation"></a>Genel hazırlık

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Hizmetler, sahipler ve yöneticilerin envanterini yürütün

Kendi cihazını getir (KCG) ve ev-giriş ilkelerindeki ve işletmelerde kablosuz bağlantı artışının büyümesi sayesinde, ağınıza kimlerin bağlanıldığını izlemeniz önemlidir. Etkin bir güvenlik denetimi genellikle ağınızda çalışan ve bu nedenle güvenli olmayan cihazları, uygulamaları ve programları ortaya çıkarır. Daha fazla bilgi için bkz. [Azure Güvenlik yönetimi ve izlemeye genel bakış](../../security/fundamentals/management-monitoring-overview.md). Envanter sürecinizdeki aşağıdaki görevlerin tümünü dahil olduğunuzdan emin olun. 

* Yönetici rollerine sahip kullanıcıları ve yönetebilecekleri Hizmetleri belirler.
* Kuruluşunuzdaki hangi kullanıcıların Azure AD 'ye yönetim erişimi olduğunu öğrenmek için, 1. Aşama ' de listelenenlerin ötesinde ek roller de dahil olmak üzere Azure AD PıM 'yi kullanın.
* Office 365, Azure AD 'de tanımlanan rollerin ötesinde, kuruluşunuzdaki kullanıcılara atayabileceğiniz bir yönetici rolleri kümesiyle birlikte gelir. Her yönetici rolü ortak iş işlevlerine eşlenir ve kuruluşunuzdaki kişilere [Microsoft 365 Yönetim merkezinde](https://admin.microsoft.com)belirli görevleri yapmak için izinler verir. Kuruluşunuzdaki kullanıcıların, Azure AD 'de yönetilmeyen roller aracılığıyla da dahil olmak üzere Office 365 ' e yönetici erişimi olduğunu öğrenmek için Microsoft 365 Yönetim merkezini kullanın. Daha fazla bilgi için bkz. Office [365 Yönetici rolleri](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) ve [En Iyi güvenlik uygulamaları hakkında Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Kuruluşunuzun Azure, Intune veya Dynamics 365 gibi bağlı olduğu diğer hizmetlerde envanterini yapın.
* Yönetici hesaplarınızın (yalnızca kullanıcılara ait gündelik hesapları değil, yönetim amacıyla kullanılan hesaplara), bunlara iliştirilmiş ve Azure MFA için kayıtlı ve şirket içi MFA kullanan bir çalışan e-posta adresi olduğundan emin olun.
* Kullanıcılara yönetim erişimi için iş gerekçesini sorun.
* İhtiyaç duymayacak kişiler ve hizmetler için yönetici erişimini kaldırın.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Yönetim rollerinde iş veya okul hesaplarına geçiş yapması gereken Microsoft hesaplarını tanımla

Bazen bir kuruluşun ilk genel yöneticileri, Azure AD kullanmaya başladıklarında mevcut Microsoft hesabı kimlik bilgilerini yeniden kullanır. Bu Microsoft hesaplarının ayrı ayrı bulut tabanlı veya eşitlenmiş hesaplarla değiştirilmeleri gerekir. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Genel yönetici hesapları için ayrı kullanıcı hesapları ve posta iletimi sağlayın 

Kişisel e-posta hesapları, siber saldırganlar tarafından düzenli olarak kesilmediğinden, genel yönetici hesaplarında kişisel e-posta adresleri olmamalıdır. İnternet risklerini (istenmeyen web taraması) yönetici ayrıcalıklarından ayırmaya yardımcı olmak için, yönetici ayrıcalıklarına sahip her bir kullanıcı için adanmış hesaplar oluşturun. 

Daha önce yapmadıysanız, kullanıcıların yanlışlıkla e-postaları açmadığından veya yönetici hesaplarıyla ilişkili programları çalıştırdıklarından emin olmak için kullanıcıların genel yönetici görevlerini gerçekleştirmesi için ayrı hesaplar oluşturun. Bu hesapların e-postalarını çalışan bir posta kutusuna iletdiğinden emin olun.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Yönetim hesaplarının parolalarının yakın zamanda değiştiğinden emin olun

Tüm kullanıcıların yönetim hesaplarında oturum açdığından ve en son 90 gün içinde parolalarını en az bir kez değiştiğinden emin olun. Ayrıca, birden fazla kullanıcının, parolayı bildiğiniz tüm paylaşılan hesapların parolalarının yakın zamanda değiştiğinden emin olun.

#### <a name="turn-on-password-hash-synchronization"></a>Parola karması eşitlemesini aç

Parola karması eşitleme, Kullanıcı parolası karmalarının karmalarını şirket içi Active Directory örneğinden bulut tabanlı bir Azure AD örneğine eşitlemek için kullanılan bir özelliktir. Active Directory Federasyon Hizmetleri (AD FS) (AD FS) veya diğer kimlik sağlayıcılarıyla Federasyon kullanmaya karar verseniz bile, AD veya ADFS sunucuları gibi şirket içi altyapınız başarısız olursa veya olduğunda isteğe bağlı olarak parola karması eşitlemesini bir yedekleme olarak ayarlayabilirsiniz geçici olarak kullanılamıyor. Bu, kullanıcıların şirket içi AD örneğinde oturum açmak için kullandıkları aynı parolayı kullanarak hizmette oturum açmasını sağlar. Ayrıca, bir kullanıcının Azure AD 'ye bağlı olmayan diğer hizmetlerde aynı e-posta adresini ve parolasını yararlanılabilir sahip olması durumunda, kimlik koruması 'nın tehlikeye girdiği bilinen parolalarla karşılaştırılmasıyla, kimlik koruması aşılan kimlik bilgilerini algılamasını sağlar.  Daha fazla bilgi için bkz. [Azure AD Connect eşitleme ile parola karması eşitlemeyi uygulama](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Tüm ayrıcalıklı rollerdeki kullanıcılar ve kullanıma sunulan kullanıcılar için çok faktörlü kimlik doğrulaması (MFA) gerektir

Azure AD, yöneticiler dahil tüm kullanıcılarınız için çok faktörlü kimlik doğrulaması (MFA) gerektirmenizi ve hesapları tehlikeye düştüğünde (örneğin, finans officileri) önemli bir etkiye sahip olan diğer tüm kullanıcıları da yapmanızı önerir. Bu, güvenliği aşılmış bir parola nedeniyle saldırının riskini azaltır.

Aç:

* Kuruluşunuzdaki tüm kullanıcılar için [koşullu erişim ilkelerini kullanarak MFA](../authentication/howto-mfa-getstarted.md) .

Iş için Windows Hello kullanıyorsanız, MFA gereksinimi Windows Hello oturum açma deneyimi kullanılarak karşılanır. Daha fazla bilgi için bkz. [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Kimlik korumasını yapılandırma 

Azure AD Kimlik Koruması, kuruluşunuzun kimliklerini etkileyen olası güvenlik açıklarını algılamak için kullanabileceğiniz, algoritma tabanlı bir izleme ve raporlama aracıdır. Algılanan şüpheli etkinliklere yönelik otomatikleştirilmiş yanıtları yapılandırabilir ve bunları çözmek için uygun işlemleri gerçekleştirebilirsiniz. Daha fazla bilgi için bkz. [Azure Active Directory kimlik koruması](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Office 365 güvenli puanınızı edinin (Office 365 kullanıyorsanız)

Güvenli puan, kullandığınız Office 365 hizmetlerinin (OneDrive, SharePoint ve Exchange gibi), ayarlarınızı ve etkinliklerinize baktığı ve bunları Microsoft tarafından belirlenen bir taban çizgisiyle karşılaştıran şekilde şekillendirilir. En iyi güvenlik uygulamalarından nasıl hizalandığını temel alarak bir puan alacaksınız. Office 365 Iş Premium veya kurumsal abonelik için yönetici izinlerine (genel yönetici veya özel yönetici rolü) sahip olan herkes, [https://securescore.office.com](https://securescore.office.com/)adresindeki güvenli puana erişebilir.

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Office 365 güvenlik ve Uyumluluk kılavuzunu gözden geçirin (Office 365 kullanıyorsanız)

[Güvenlik ve uyumluluk planı](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) , Office 365 müşterilerinin Office 365 ' nin nasıl yapılandırılması ve diğer EMS özelliklerinden faydalanma yaklaşımını özetler. Daha sonra, [office 365 ' de veri ve hizmetlere erişimi koruma](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) 3-6 ve [Office 365 güvenlik ve uyumluluğunu izleme](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6)Kılavuzu ' nda bulunan adımları gözden geçirin.

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Office 365 etkinlik Izlemeyi yapılandırma (Office 365 kullanıyorsanız)

Kuruluşunuzdaki kişilerin Office 365 hizmetlerini nasıl kullandığını izleyip, yönetici hesabına sahip olan ve bu portallarda oturum olmaması nedeniyle Office 365 erişimine gerek olmayan kullanıcıları tanımlamanızı sağlayabilirsiniz. Daha fazla bilgi için, [Microsoft 365 Yönetim merkezinde etkinlik raporları](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263)' na bakın.

#### <a name="establish-incidentemergency-response-plan-owners"></a>Olay/acil durum yanıt planı sahipleri oluşturma

Olay yanıtının etkili bir şekilde gerçekleştirilmesi karmaşık bir zorunlulukdır. Bu nedenle, başarılı bir olay yanıtı özelliği oluşturmak için önemli planlama ve kaynaklar gerekir. Iber saldırıları için sürekli olarak izlemeniz ve olayların işlenmesine öncelik verme yordamları oluşturmanız önemlidir. Verilerin toplanması, çözümlenmesi ve raporlanması için etkili yöntemler, ilişki oluşturmak ve diğer dahili gruplarla ve plan sahipleriyle iletişim kurmak için hayati öneme sahiptir. Daha fazla bilgi için bkz. [Microsoft Güvenlik Yanıt Merkezi](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Daha önce yapmadıysanız, şirket içi ayrıcalıklı yönetim hesaplarının güvenliğini sağlayın

Azure Active Directory kiracınız şirket içi Active Directory eşitlendiğinde, [güvenlik ayrıcalıklı erişim yol haritası](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): Aşama 1 ' deki yönergeleri izleyin. Bu, şirket içi yönetim görevleri yapması, Active Directory yöneticileri için ayrıcalıklı erişim Iş Istasyonları dağıtmak ve iş istasyonları için benzersiz yerel yönetici parolaları oluşturmak isteyen kullanıcılar için ayrı yönetici hesapları oluşturmayı içerir. larý.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure 'a erişimi yöneten kuruluşlar için ek adımlar

#### <a name="complete-an-inventory-of-subscriptions"></a>Aboneliklerin envanterini doldurun

Kuruluşunuzda üretim uygulamalarını barındıran abonelikleri belirlemek için Enterprise Portal 'ı ve Azure portal kullanın.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Microsoft hesaplarını yönetici rollerinden kaldır

Xbox, Live ve Outlook gibi diğer programlardaki Microsoft hesapları, kurumsal abonelikler için yönetici hesabı olarak kullanılmamalıdır. Tüm Microsoft hesaplarından yönetici durumunu kaldırın ve Azure Active Directory (örneğin, chris@contoso.com) iş veya okul hesaplarıyla değiştirin.

#### <a name="monitor-azure-activity"></a>Azure etkinliğini izleme

Azure etkinlik günlüğü, Azure 'da abonelik düzeyindeki olayların geçmişini sağlar. Hangi kaynakları oluşturan, güncelleştiren ve silen ve bu olaylar ne zaman gerçekleştiği hakkında bilgi sunar. Daha fazla bilgi için bkz. [Azure aboneliğinizdeki önemli eylemlerle ilgili bildirimleri denetleme ve alma](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Azure AD aracılığıyla diğer bulut uygulamalarına erişimi yöneten kuruluşlar için ek adımlar

#### <a name="configure-conditional-access-policies"></a>Koşullu erişim ilkelerini yapılandırma

Şirket içi ve bulutta barındırılan uygulamalar için koşullu erişim ilkeleri hazırlayın. Kullanıcılarınızın çalışma alanına katılmış cihazları varsa, [Azure Active Directory cihaz kaydı kullanarak şirket Içi koşullu erişim ayarlamalarından](../active-directory-device-registration-on-premises-setup.md)daha fazla bilgi alın.


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>3\. Aşama: yönetim etkinliğinin görünürlüğünü oluşturun ve tam denetim yapın

![3\. aşama yönetici etkinliğinin denetimini al](./media/directory-admin-roles-secure/stage-three.png)

3\. Aşama 2. aşama üzerinde azaltmalar oluşturur ve yaklaşık 1-3 ayda uygulanacak şekilde tasarlanmıştır. Güvenli ayrıcalıklı erişim yol haritası 'nın bu aşaması aşağıdaki bileşenleri içerir.

### <a name="general-preparation"></a>Genel hazırlık

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Yönetici rollerindeki kullanıcıların erişim incelemesini tamamlar

Daha fazla şirket kullanıcısı bulut hizmetleri aracılığıyla ayrıcalıklı erişim elde edebilir ve bu da artan bir yönetilmeyen platforma yol açabilir. Bu, Office 365, Azure abonelik yöneticileri ve sanal makinelere ya da SaaS uygulamaları aracılığıyla yönetici erişimi olan kullanıcılar için küresel yöneticilere sahip olan kullanıcıları içerir. Bunun yerine, kuruluşların tüm çalışanları, özellikle Yöneticiler, günlük iş işlemlerini ayrıcalıksız kullanıcılar olarak işlemesini ve yalnızca gerektiğinde yönetici haklarına sahip olması gerekir. Yönetici rollerindeki kullanıcı sayısı ilk benimsemeye başladıktan sonra, yönetici ayrıcalıklarını etkinleştirmeye uygun olan her kullanıcıyı tanımlamak ve doğrulamak için tüm erişim incelemelerini gözden geçirdiklerinden emin olun. 

Şunları yapın:

* Hangi kullanıcıların Azure AD yöneticileri olduğunu belirleme, isteğe bağlı, tam zamanında yönetici erişimi ve rol tabanlı güvenlik denetimlerini etkinleştirme.
* Yönetici ayrıcalıklı erişimi için açık bir gerekçe olmayan kullanıcıları farklı bir role dönüştürün (uygun rol yoksa, bunları kaldırın).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Tüm kullanıcılar için daha güçlü kimlik doğrulaması dağıtımına devam et 

C Suite yöneticileri, üst düzey yöneticiler, kritik BT ve güvenlik personeli ve diğer yüksek kaliteli kullanıcıların Azure MFA veya Windows Hello gibi modern, güçlü kimlik doğrulamasına sahip olmasını gerektirir. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Azure AD yönetimi için adanmış iş istasyonlarını kullanma

Saldırganlar, bir kuruluşun verilerine ve sistemlerine erişim kazanmak için ayrıcalıklı hesapları hedefleyebilir ve böylece, yöneticinin bir kimlik bilgisi girerek program mantığını veya gözops 'u değiştiren kötü amaçlı kodlar aracılığıyla verilerin bütünlüğünü ve gerçekliğini kesintiye uğramasını sağlayabilir. Ayrıcalıklı erişim Iş Istasyonları (Paw 'lar), Internet saldırıları ve tehdit vektörlerinden korunan hassas görevler için adanmış bir işletim sistemi sağlar. Bu hassas görevler ve hesapların günlük kullanım iş istasyonları ve cihazlarından ayrılması, kimlik avı saldırıları, uygulama ve işletim sistemi güvenlik açıkları, çeşitli kimliğe bürünme saldırıları ve tuş vuruşu gibi kimlik bilgisi hırsızlığı saldırılarına karşı çok güçlü koruma sağlar günlüğe kaydetme, karma değer geçişi ve anahtar geçişi. Ayrıcalıklı erişim iş istasyonlarını dağıtarak, sağlamlaştırılmış bir masaüstü ortamı dışında yöneticinin yönetici kimlik bilgilerini girme riskini azaltabilirsiniz. Daha fazla bilgi için bkz. [ayrıcalıklı erişim Iş istasyonları](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Olayları işlemeye yönelik ulusal standartlar ve teknoloji önerilerini gözden geçirin 

Ulusal Standartlar ve Teknoloji Enstitüsü (NıST), özellikle olayla ilgili verileri analiz etmek ve her olaya uygun yanıtı belirlemek için olay işleme için yönergeler sağlar. Daha fazla bilgi için bkz. [(NIST) bilgisayar güvenliği olay Işleme Kılavuzu (SP 800-61, düzeltme 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Ek yönetim rollerine JıT için Privileged Identity Management (PıM) uygulayın

Azure Active Directory için [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) özelliğini kullanın. Ayrıcalıklı rollerin zaman sınırlı etkinleştirilmesi şunları sağlar:

* Belirli bir görevi gerçekleştirmek için yönetici ayrıcalıklarını etkinleştirin
* Etkinleştirme işlemi sırasında MFA 'yı zorlama
* Yöneticilere bant dışı değişiklikler hakkında bilgi vermek için uyarıları kullanın
* Kullanıcıların önceden yapılandırılmış bir süre için belirli ayrıcalıkları korumasını sağlar
* Güvenlik yöneticilerinin tüm ayrıcalıklı kimlikleri bulmasına, Denetim raporlarını görüntülemesine ve yönetici ayrıcalıklarını etkinleştirecek şekilde uygun olan her kullanıcıyı belirlemek için erişim İncelemeleri oluşturmasına izin verin

Zaten Azure AD Privileged Identity Management kullanıyorsanız zamana ait ayrıcalıklar için zaman çerçevesini gerektiği gibi ayarlayın (örneğin, bakım pencereleri).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Parola tabanlı oturum açma protokollerinin görünürlüğünü belirleme (Exchange Online kullanıyorsanız)

Geçmişte, protokoller Kullanıcı adı/parola birleşimlerinin cihazlara, e-posta hesaplarına, telefonlara, vb. gömülü olduğunu kabul eder. Ancak artık buluttaki siber saldırılara karşı risk altınıza göre, kimlik bilgilerinin tehlikeye girdiği, kuruluşa çok zararlı olabilecek ve güçlü kimlik doğrulama gereksinimleri ve koşullu erişim uygulayarak Kullanıcı adı/parola aracılığıyla e-postalarında oturum açabiliyor olan tüm olası kullanıcıları tanımlamanızı öneririz. [Koşullu erişimi kullanarak eski kimlik doğrulamasını](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication)engelleyebilirsiniz. Lütfen exchnage Online aracılığıyla [temel kimlik doğrulamasını engelleme](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) hakkındaki ayrıntıları kontrol edin. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Office 365 rolleri için bir rol gözden geçirme değerlendirmesi tamamlamayı (Office 365 kullanıyorsanız)

Tüm Yöneticiler kullanıcılarının doğru rollerde olup olmadığını değerlendirin (bu değerlendirmeye göre Sil ve yeniden ata).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Office 365 ' de kullanılan güvenlik olay yönetimi yaklaşımını gözden geçirin ve kendi kuruluşunuzla karşılaştırın

Bu raporu, [Microsoft Office 365 ' deki güvenlik olay yönetimi](https://www.microsoft.com/download/details.aspx?id=54302)'nden indirebilirsiniz.

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Şirket içi ayrıcalıklı yönetim hesaplarını güvenli hale getirmeye devam edin

Azure Active Directory Şirket içi Active Directory bağlıysa, [güvenlik ayrıcalıklı erişim yol haritası](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): Aşama 2 ' deki yönergeleri izleyin. Bu, tüm yöneticiler için ayrıcalıklı erişim Iş Istasyonlarını dağıtmak, etki alanlarının saldırı yüzeyini düşürmek ve saldırı algılama için ATA dağıtmak üzere yalnızca DC bakımı için yeterli yönetici kullanarak MFA gerektirmektir.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure 'a erişimi yöneten kuruluşlar için ek adımlar

#### <a name="establish-integrated-monitoring"></a>Tümleşik izleme oluşturma

[Azure Güvenlik Merkezi](../../security-center/security-center-intro.md) , Azure aboneliklerinizde tümleşik güvenlik izleme ve ilke yönetimi sağlar, başka türlü fark edilmemiş tehditleri algılamaya yardımcı olur ve güvenlik çözümlerinin geniş bir ekosistemiyle birlikte çalışabilir.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Barındırılan sanal makineler içindeki ayrıcalıklı hesaplarınızın envanterini çıkarın

Çoğu durumda kullanıcılara tüm Azure abonelikleriniz veya kaynaklarınız için kısıtlanmamış izinler vermeniz gerekmez. Kuruluşunuzdaki görevleri ayırmak ve yalnızca belirli işleri gerçekleştirmesi gereken kullanıcılara erişim miktarına izin vermek için Azure AD yönetici rolleri ' ni kullanabilirsiniz. Örneğin, bir yöneticinin bir abonelikteki yalnızca VM 'Leri yönetmesine izin vermek için Azure AD yönetici rolleri ' ni kullanın, diğer bir deyişle SQL veritabanlarını aynı abonelik içinde yönetebilir. Daha fazla bilgi için, bkz. [Azure Portal rol tabanlı Access Control kullanmaya başlama](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Azure AD yönetici rolleri için PıM uygulama

Azure kaynaklarına erişimi yönetmek, denetlemek ve izlemek için Azure AD yönetici rolleriyle ayrıcalıklı kimlik yönetimi kullanın. PıM 'nin kullanılması, ayrıcalıkların etkilenme süresini azaltarak ve raporlarınızı raporlar ve uyarılar aracılığıyla kullanım için artırarak ayrıcalıklıya saldırılarına karşı ayrıcalıklı hesapları korur. Daha fazla bilgi için bkz. [Privileged Identity Management Ile Azure KAYNAKLARıNA RBAC erişimini yönetme](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>İlgili Azure günlüklerini SıEM sistemlerinize göndermek için Azure günlük tümleştirmelerini kullanın 

Azure günlük tümleştirmesi, Azure kaynaklarınızdan ham günlükleri kuruluşunuzun mevcut güvenlik bilgileri ve olay yönetimi (SıEM) sistemleriyle tümleştirmenize olanak sağlar. [Azure günlük tümleştirmesi](../../security/fundamentals/azure-log-integration-overview.md) , Windows Olay Görüntüleyicisi günlüklerinden Windows olaylarını ve Azure etkinlik günlükleri, Azure Güvenlik Merkezi uyarıları ve Azure tanılama günlükleri 'nden Azure kaynaklarını toplar. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Azure AD aracılığıyla diğer bulut uygulamalarına erişimi yöneten kuruluşlar için ek adımlar

#### <a name="implement-user-provisioning-for-connected-apps"></a>Bağlı uygulamalar için Kullanıcı sağlamayı uygulama

Azure AD, kullanıcı kimliklerinin Dropbox, Salesforce, ServiceNow gibi bulut (SaaS) uygulamalarında oluşturulmasını, bakımını ve kaldırılmasını otomatik hale getirmenizi sağlar. Daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Tümleştirme bilgi koruması

MCAS, dosyaları araştırmanıza ve Azure Information Protection sınıflandırma etiketlerine göre ilkeler ayarlamanıza olanak tanıyarak, buluttaki verilerinizin daha fazla görünürlüğünü ve denetimini etkinleştirir. Bulutta dosyaları tarayın ve sınıflandırın ve Azure Information Protection etiketlerini uygulayın. Daha fazla bilgi için [Azure Information Protection tümleştirmesi](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Koşullu erişimi yapılandırma

[SaaS uygulamaları](https://azure.microsoft.com/overview/what-is-saas/) ve Azure AD bağlı uygulamaları için bir grup, konum ve uygulama duyarlılığı temelinde koşullu erişimi yapılandırın. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Bağlı bulut uygulamalarındaki etkinlikleri izleme

Kullanıcıların erişiminin bağlı uygulamalarda da korunduğundan emin olmak için [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)kullanmanızı öneririz. Bu, yönetim hesaplarınızı güvenli hale getirmenin yanı sıra, bulut uygulamalarına kurumsal erişimin güvenliğini sağlar ve şunları yapabilirsiniz:

* Görünürlük ve denetimi bulut uygulamalarına genişletme
* Erişim, etkinlikler ve veri paylaşımı için ilkeler oluşturma
* Riskli etkinlikleri, olağan dışı davranışları ve tehditleri otomatik olarak belirleyin
* Veri sızıntısını önleme
* Risk ve otomatik tehdit önleme ve ilke zorlamayı en aza indirme

Cloud App Security SıEM Aracısı, Office 365 uyarıları ve etkinliklerini merkezi olarak izlemeyi etkinleştirmek için SıEM sunucunuz ile Cloud App Security tümleştirir. Sunucunuzda çalışır ve Cloud App Security uyarı ve etkinlik çeker ve bunları SıEM sunucusuna akıp. Daha fazla bilgi için [SIEM tümleştirmesi](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>4\. Aşama: daha proaktif bir güvenlik duruşunu savunma oluşturmaya devam edin

![4\. aşama proaktif güvenlik duruşunu benimseyin](./media/directory-admin-roles-secure/stage-four.png)

Yol haritası 'nın 4. aşaması, 1. aşama 'daki görünürlüğe göre oluşturulur ve altı ayda ve sonra uygulanacak şekilde tasarlanmıştır. Bir yol haritasını tamamlamak, şu anda bilinen ve kullanılabilir olan olası saldırılara karşı güçlü ayrıcalıklı erişim korumaları geliştirmenize yardımcı olur. Ne yazık ki güvenlik tehditleri sürekli olarak geliştikçe ve kaydırılarak, maliyeti artırmaya ve ortamınızı hedefleyen duyuru başarı oranını düşürmeye odaklanan devam eden bir işlem olarak güvenliği görüntülemenizi öneririz.

Ayrıcalıklı erişimin güvenliğini sağlamak, modern bir kuruluştaki iş varlıkları için güvenlik unsurları oluşturmaya yönelik kritik bir ilk adımdır, ancak ilke, işlemler, bilgiler gibi öğeleri içeren bir tamamen güvenlik programının yalnızca bir parçası değildir. Güvenlik, sunucular, uygulamalar, bilgisayarlar, cihazlar, bulut dokusu ve diğer bileşenler sürekli güvenlik hedefleri sağlar. 

Ayrıcalıklı erişim hesaplarınızı yönetmenin yanı sıra, aşağıdakileri devam eden bir şekilde incelemenizi öneririz:

* Yöneticilerin, her gün İşletmelerini ayrıcalıksız kullanıcılar olarak yaptığını doğrulayın.
* Yalnızca gerektiğinde ayrıcalıklı erişim verin ve daha sonra (tam zamanında) kaldırın.
* Ayrıcalıklı hesaplarla ilgili denetim etkinliğini koruyun ve gözden geçirin.

Tüm güvenlik yol haritası oluşturma hakkında daha fazla bilgi için bkz. [Microsoft Cloud It Architecture kaynakları](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Bu konulardan herhangi birine yardımcı olmak üzere Microsoft hizmetlerine başvurma hakkında daha fazla bilgi için Microsoft temsilcinize başvurun veya bkz. [kuruluşunuzu korumak Için derleme açısından kritik siber savunmaları](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Güvenli ayrıcalıklı erişim yol haritası 'nın bu son devam eden aşaması aşağıdaki bileşenleri içerir.

### <a name="general-preparation"></a>Genel hazırlık

#### <a name="review-admin-roles-in-azure-active-directory"></a>Azure Active Directory yönetici rollerini gözden geçirin 

Mevcut yerleşik Azure AD Yönetici rollerinin hala güncel olup olmadığını ve kullanıcıların yalnızca ilgili izinler için ihtiyaç duydukları roller ve temsilci olduklarından emin olun. Azure AD ile ayrı yöneticileri farklı işlevlere sunacak şekilde belirleyebilirsiniz. Daha fazla bilgi için bkz. [Azure Active Directory yönetici rolleri atama](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Azure AD 'ye katılmış cihazların yönetimine sahip kullanıcıları gözden geçirme

Daha fazla bilgi için bkz. [karma Azure Active Directory katılmış cihazları yapılandırma](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>[Yerleşik Office 365 Yönetici rollerinin](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) üyelerini gözden geçirme
Office 365 kullanıyorsanız.
‎
#### <a name="validate-incident-response-plan"></a>Olay yanıtı planını doğrula

Planınızı geliştirmek için, Microsoft planınızın beklendiği gibi çalıştığını düzenli olarak doğrulamanızı önerir:

* Eksik olanları görmek için mevcut yol eşlemenizi inceleyin
* Postmordıtem analizine bağlı olarak, var olan veya yeni en iyi yöntemleri tanımla
* Güncelleştirilmiş olay yanıt planınızın ve en iyi uygulamaların kuruluşunuz genelinde dağıtıldığından emin olun


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure 'a erişimi yöneten kuruluşlar için ek adımlar 

[Bir Azure aboneliğinin sahipliğini başka bir hesaba aktarmanız](../../billing/billing-subscription-transfer.md)gerekip gerekmediğini belirleme.
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Cam kesmesi": acil durum

![Acil durum kesme camı erişimi hesapları](./media/directory-admin-roles-secure/emergency.jpeg)

1. Olay ile ilgili uygun bilgileri kullanarak anahtar yöneticilerine ve güvenlik ofislere bildirim gönderin.

2. Saldırı PlayBook 'unu gözden geçirin. 

3. Azure AD 'de oturum açmak için "cam" hesabı Kullanıcı adı/parola birleşimine erişin. 

4. [Azure destek isteği açarak](../../azure-supportability/how-to-create-azure-support-request.md)Microsoft 'tan yardım alın.

5. [Azure AD oturum açma raporlarına](../reports-monitoring/overview-reports.md)bakın. Oluşan bir olay ve rapora dahil edildiğinde bir gecikme olabilir.

6. Karma ortamlarda, federe ve AD FS sunucunuz kullanılamazsa, geçici olarak federal kimlik doğrulamasından Parola karması eşitlemesini kullanacak şekilde geçiş yapmanız gerekebilir. Bu, AD FS sunucusu kullanılabilir hale gelene kadar etki alanı federasyonunu yönetilen kimlik doğrulamasına geri döndürür.

7. Ayrıcalıklı hesaplar için e-postayı izleyin.

8. Olası bir ve yasal araştırma için ilgili günlüklerin yedeklerini kaydettiğinizden emin olun.

Microsoft Office 365 güvenlik olaylarını nasıl işleyeceği hakkında daha fazla bilgi için, bkz. [Microsoft Office 365 ' de güvenlik olay yönetimi](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>SSS: ayrıcalıklı erişimin güvenliğini sağlama hakkında aldığımız yaygın sorular  

**S:** Henüz bir güvenli erişim bileşeni uygulamadım, ne yapmam gerekiyor?

**Cevap:** En az iki adet kesme camı hesabı tanımlayın, ayrıcalıklı yönetici hesaplarınıza MFA atayın ve küresel yönetici hesaplarından Kullanıcı hesaplarını ayırın.

**S:** Bir ihlal sonrasında ilk olarak ilgilenilmesi gereken en önemli sorun nedir?

**Cevap:** Yüksek düzeyde açığa çıkarılan bireyler için en güçlü kimlik doğrulaması gerektirdiğinizden emin olun.

**S:** Ayrıcalıklı yöneticilerimiz devre dışı bırakılmışsa ne olur?

**Cevap:** Her zaman güncel tutulan bir genel yönetici hesabı oluşturun.

**S:** Yalnızca bir genel yönetici yoksa ve bunlara ulaşılamadığından ne olur? 

**Cevap:** Acil erişim sağlamak için, kesme camı hesaplarından birini kullanın.

**S:** Kuruluşumun içindeki yöneticileri nasıl koruyabilirim?

**Cevap:** Yöneticilerin her zaman gündelik işletmelerini standart "ayrıcalıksız" kullanıcılar olarak yapması gerekir.

**S:** Azure AD 'de yönetici hesapları oluşturmak için en iyi uygulamalar nelerdir?

**Cevap:** Belirli yönetici görevleri için ayrıcalıklı erişim ayırın.

**S:** Kalıcı yönetici erişimini azaltmak için hangi araçlar var?

**Cevap:** Privileged Identity Management (PıM) ve Azure AD yönetici rolleri.

**S:** Yönetici hesaplarını Azure AD 'ye eşitlemeye ilişkin Microsoft konumu nedir?

**Cevap:** Katman 0 Yönetici hesapları (hesaplar, gruplar ve AD Ormanı, etki alanları veya etki alanı denetleyicileri ve tüm varlıklar) için doğrudan veya dolaylı yönetim denetimine sahip olan diğer varlıklar dahil, yalnızca şirket içi AD hesapları için kullanılır ve genellikle bulut için Azure AD ile eşitlenmez.

**S:** Yöneticilerin portalda rastgele yönetici erişimi atamasını nasıl sağlıyoruz?

**Cevap:** Tüm kullanıcılar ve yöneticiler için ayrıcalıklı olmayan hesaplar kullanın. Hangi yönetici hesaplarının ayrıcalıklı olacağını öğrenmek için kuruluşun parmak izini geliştirerek başlayın. Ve yeni oluşturulan yönetim kullanıcılarını izler.

## <a name="next-steps"></a>Sonraki adımlar

* [Ürün güvenliği Için Microsoft Güven Merkezi](https://www.microsoft.com/trustcenter/security) – Microsoft bulut ürünlerinin ve hizmetlerinin güvenlik özellikleri

* [Microsoft Güven Merkezi-uyumluluk](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – Microsoft 'un bulut hizmetleri için kapsamlı bir uyumluluk teklifleri kümesi

* [Risk değerlendirmesi gerçekleştirme hakkında yönergeler](https://www.microsoft.com/trustcenter/guidance/risk-assessment) -Microsoft bulut hizmetleri için güvenlik ve uyumluluk gereksinimlerini yönetme

### <a name="other-microsoft-online-services"></a>Diğer Microsoft Çevrimiçi Hizmetleri

* [Microsoft Intune güvenliği](https://www.microsoft.com/trustcenter/security/intune-security) – Intune, buluttan mobil cihaz yönetimi, mobil uygulama YÖNETIMI ve bilgisayar yönetimi özellikleri sağlar.

* [Microsoft dynamics 365 güvenliği](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365, müşteri ilişkileri YÖNETIMI (CRM) ve kurumsal kaynak planlama (ERP) yeteneklerini birleştiren Microsoft bulut tabanlı çözümüdür.
