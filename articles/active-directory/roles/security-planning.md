---
title: Azure AD 'de Yöneticiler için güvenli erişim uygulamaları | Microsoft Docs
description: Kuruluşunuzun yönetim erişiminin ve yönetici hesaplarının güvenli olduğundan emin olun. Azure AD, Azure ve Microsoft Online hizmetlerini yapılandıran sistem mimarları ve BT uzmanları için.
services: active-directory
keywords: ''
author: rolyon
manager: daveba
ms.author: rolyon
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f2fe29ede2bf0f92049d1ae82bae87326057a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594289"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Azure AD'de karma ve bulut dağıtımları için ayrıcalıklı erişim güvenliğini sağlama

İş varlıklarının güvenliği, BT sistemlerinizi yöneten ayrıcalıklı hesapların bütünlüğüne bağlıdır. Cyber-saldırganlar, hassas verilere erişim kazanmak için yönetici hesaplarını ve diğer ayrıcalıklı erişimi hedeflemek üzere kimlik bilgisi hırsızlığı saldırıları kullanır.

Bulut hizmetleri için, önleme ve yanıt, bulut hizmeti sağlayıcısının ve müşterinin ortak sorumluluklarına yöneliktir. Uç noktalara ve buluta yönelik en son tehditler hakkında daha fazla bilgi için [Microsoft Güvenlik Intelligence raporuna](https://www.microsoft.com/security/operations/security-intelligence-report)bakın. Bu makale, geçerli planlarınız ve burada açıklanan kılavuz arasındaki boşlukları kapatmaya yönelik bir yol haritası geliştirmenize yardımcı olabilir.

> [!NOTE]
> Microsoft, en yüksek düzeyde güven, saydamlık, standart uyumluluk ve mevzuat uyumluluğuna kararlıdır. Microsoft küresel olay yanıtı ekibinin, bulut hizmetlerine yönelik saldırıların etkilerini nasıl azaltmasının yanı sıra Microsoft Güven Merkezi- [Uyumluluk](https://www.microsoft.com/trustcenter/compliance)' de Microsoft [Trust Center-güvenlik](https://www.microsoft.com/trustcenter/security) ve Microsoft Uyumluluk hedefleri ' nde Microsoft iş ürünleri ve bulut Hizmetleri ' ne nasıl oluşturulduğu hakkında daha fazla bilgi edinin.

Geleneksel olarak, kuruluş güvenliği, bir ağın giriş ve çıkış noktalarına güvenlik çevre birimi olarak odaklanmıştı. Ancak, Internet 'teki SaaS uygulamaları ve kişisel cihazlar bu yaklaşımı daha az etkin hale yaptı. Azure AD 'de ağ güvenlik kuşağını, denetimdeki ayrıcalıklı yönetim rollerine atanmış kullanıcılarla kuruluşunuzun kimlik katmanında kimlik doğrulamasıyla değiştirirsiniz. Ortamın şirket içinde, bulutta veya hibrit olması durumunda erişiminin korunması gerekir.

Ayrıcalıklı erişimin güvenliğini sağlamak için değişiklikler yapmanız gerekir:

* Süreçler, yönetim uygulamaları ve bilgi yönetimi
* Ana bilgisayar savunmaları, hesap korumaları ve kimlik yönetimi gibi teknik bileşenler

Önem verdiğiniz Microsoft hizmetlerinde yönetilen ve bildirilen bir şekilde ayrıcalıklı erişiminizi güvenli hale getirin. Şirket içi yönetici hesaplarınız varsa, [ayrıcalıklı erişimin güvenliğini sağlamaya](/windows-server/identity/securing-privileged-access/securing-privileged-access)yönelik Active Directory ' de şirket içi ve karma ayrıcalıklı erişim Kılavuzu ' na bakın.

> [!NOTE]
> Bu makaledeki kılavuz, birincil olarak Azure Active Directory Premium planlarına P1 ve P2 'a eklenen Azure Active Directory özelliklerine başvurur. Azure Active Directory Premium P2, EMS E5 Suite ve Microsoft 365 E5 Suite 'e dahildir. Bu kılavuzda, kuruluşunuzda kullanıcılarınız için satın alınan Azure AD Premium P2 lisansları zaten var. Bu lisanslarınız yoksa, bazı kılavuzlardan biri kuruluşunuza uygulanmayabilir. Ayrıca, bu makale boyunca genel yönetici terimi "Şirket Yöneticisi" veya "Kiracı Yöneticisi" ile aynı şeyi ifade ederler.

## <a name="develop-a-roadmap"></a>Yol haritası geliştirme

Microsoft, siber saldırganlarına karşı ayrıcalıklı erişimi güvenli hale getirmek için bir yol haritası geliştirmenizi ve izlemenizi önerir. Her zaman yol haritasını, mevcut olanaklarınızı ve kuruluşunuzdaki belirli gereksinimleri karşılayacak şekilde ayarlayabilirsiniz. Yol haritası 'nın her aşaması, reklam işlemleri için, şirket içi, bulut ve karma varlıklarınız için ayrıcalıklı erişim saldırıları için maliyet ve zorluk derecesini yükseltmelidir. Microsoft aşağıdaki dört yol haritası aşamasını önerir. Önce en etkili ve en hızlı uygulamaları zamanlayın. Bu makale, Microsoft 'un Cyber saldırı olayı ve yanıt uygulamasıyla ilgili deneyimlerine bağlı olarak kılavuzunuz olabilir. Bu yol haritası için zaman çizelgeleri yaklaşık olarak gösterilir.

![Zaman çizgileri ile yol haritası aşamaları](./media/security-planning/roadmap-timeline.png)

* 1. aşama (24-48 saat): hemen yapmanızı önerdiğimiz kritik öğeler

* 2. aşama (2-4 hafta): en sık kullanılan saldırı tekniklerini azaltma

* 3. aşama (1-3 ay): yönetici etkinliğinin görünürlüğünü oluşturun ve tam denetim oluşturun

* 4. aşama (altı ay ve daha fazla): güvenlik platformunuzu daha fazla savunmaya yönelik savunma oluşturmaya devam edin

Bu yol haritası çerçevesi, zaten dağıttığınız Microsoft teknolojilerinin kullanımını en üst düzeye çıkarmak için tasarlanmıştır. Daha önce dağıttığınız veya dağıtımını düşündüğünüz diğer satıcıların herhangi bir güvenlik araçlarına yerleştirmeyi göz önünde bulundurun.

## <a name="stage-1-critical-items-to-do-right-now"></a>1. Aşama: Şu anda yapılacak kritik öğeler

![İlk yapmanız için 1. aşama kritik öğe](./media/security-planning/stage-one.png)

Yol haritasının 1. aşaması, hızlı ve kolay bir şekilde uygulanması gereken kritik görevlere odaklanılmıştır. Temel düzeyde güvenli ayrıcalıklı erişim sağlamak için bu birkaç öğeyi ilk 24-48 saat içinde hemen yapmanızı öneririz. Güvenli ayrıcalıklı erişim yol haritası 'nın bu aşaması aşağıdaki eylemleri içerir:

### <a name="general-preparation"></a>Genel hazırlık

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management aç

Azure AD üretim ortamınızda Azure AD Privileged Identity Management (PıM) açmanızı öneririz. PıM 'yi etkinleştirdikten sonra, ayrıcalıklı erişim rolü değişiklikleri için bildirim e-posta iletilerini alacaksınız. Bildirimler, yüksek ayrıcalıklı rollere ek kullanıcılar eklendiğinde erken uyarı sağlar.

Azure AD Privileged Identity Management, Azure AD Premium P2 veya EMS E5 'ye dahildir. Şirket içindeki ve buluttaki uygulama ve kaynaklara erişimi korumanıza yardımcı olması için [Enterprise Mobility + Security ücretsiz 90 günlük deneme sürümü](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)için kaydolun. Azure AD raporlama, denetim ve uyarıları kullanarak güvenlik etkinliğini Azure AD Privileged Identity Management ve Azure AD Kimlik Koruması izleyin.

Azure AD Privileged Identity Management açtıktan sonra:

1. Azure AD üretim kuruluşunuzun genel yöneticisi olan bir hesapla [Azure Portal](https://portal.azure.com/) oturum açın.

2. Privileged Identity Management kullanmak istediğiniz Azure AD organizasyonunu seçmek için, Azure portal sağ üst köşesindeki Kullanıcı adınızı seçin.

3. Azure portal menüsünde **tüm hizmetler** ' i seçin ve **Azure AD Privileged Identity Management** listeyi filtreleyin.

4. **Tüm hizmetler** listesinden Privileged Identity Management açın ve panonuza sabitleyin.

Kuruluşunuzda PıM 'yi kullanmak için ilk kişinin **Güvenlik Yöneticisi** ve **ayrıcalıklı rol yöneticisi** rollerine atandığından emin olun. Yalnızca ayrıcalıklı rol yöneticileri kullanıcıların Azure AD dizin rolü atamalarını yönetebilir. PıM Güvenlik Sihirbazı, ilk bulma ve atama deneyiminde size kılavuzluk eder. Bu sırada başka bir değişiklik yapmadan sihirbazdan çıkabilirsiniz.

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Yüksek ayrıcalıklı rollerdeki hesapları belirleme ve kategorilere ayırma

Azure AD Privileged Identity Management açtıktan sonra, aşağıdaki Azure AD rollerinde bulunan kullanıcıları görüntüleyin:

* Genel yönetici
* Ayrıcalıklı rol yöneticisi
* Exchange yöneticisi
* SharePoint yöneticisi

Kuruluşunuzda Azure AD Privileged Identity Management yoksa [POWERSHELL API](/powershell/module/azuread/get-azureaddirectoryrolemember)'sini kullanabilirsiniz. Genel yönetici rolüyle başlayın çünkü genel yönetici, kuruluşunuzun abone olduğu tüm bulut hizmetlerinde aynı izinlere sahiptir. Bu izinlere nerede atandıklarından bağımsız olarak izin verilir: Microsoft 365 Yönetim Merkezi, Azure portal veya Microsoft PowerShell için Azure AD modülü.

Bu rollerde artık gerekmeyen hesapları kaldırın. Ardından, yönetici rollerine atanan kalan hesapları kategorilere ayırın:

* Yönetici kullanıcılara atanır, ancak yönetici olmayan amaçlar için de kullanılır (örneğin, kişisel e-posta)
* Yönetici kullanıcılara atandı ve yalnızca yönetim amacıyla kullanılır
* Birden çok kullanıcı arasında paylaşılıyor
* Kesme camı için acil durum erişim senaryoları
* Otomatikleştirilmiş betikler için
* Dış kullanıcılar için

#### <a name="define-at-least-two-emergency-access-accounts"></a>En az iki acil durum erişim hesabı tanımlayın

Bir kullanıcının rolünden yanlışlıkla kilitlenmesi mümkündür. Örneğin, bir şirket içi kimlik sağlayıcısı kullanılabilir değilse, kullanıcılar oturum açamaz veya mevcut bir yönetici hesabını etkinleştiremez. İki veya daha fazla acil durum erişim hesabını depolayarak yanlışlıkla erişim eksikliğine hazırlanabilirsiniz.

Acil durum erişim hesapları, bir Azure AD kuruluşunda ayrıcalıklı erişimi kısıtlamaya yardımcı olur. Bu hesaplar son derece ayrıcalıklı ve belirli kişilere atanmamıştır. Acil durum erişim hesapları, normal yönetim hesaplarının kullanılabileceği "cam" senaryolarında acil durum ile sınırlıdır. Acil durum hesabının kullanımını yalnızca gerekli olduğu zamana göre kontrol edin ve azalttığınızdan emin olun.

Genel yönetici rolü için atanan veya uygun olan hesapları değerlendirin. . Onmicrosoft.com etki alanını kullanarak yalnızca herhangi bir bulut hesabı görmüyorsanız \* ("cam" acil erişim için), bunları oluşturun. Daha fazla bilgi için bkz. [Azure AD 'de acil durum erişimi yönetim hesaplarını yönetme](security-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Multi-Factor Authentication 'ı açın ve diğer tüm yüksek ayrıcalıklı tek kullanıcılı yönetici hesaplarını kaydedin

Bir veya daha fazla Azure AD yönetici rolüne kalıcı olarak atanan her bireysel kullanıcı için oturum açma sırasında Azure AD Multi-Factor Authentication (MFA) gerektir: genel yönetici, ayrıcalıklı rol yöneticisi, Exchange Yöneticisi ve SharePoint Yöneticisi. [Yönetici hesaplarınız Için Multi-Factor Authentication 'ı (MFA)](../authentication/howto-mfa-userstates.md) etkinleştirmek ve tüm bu kullanıcıların kaydoldiğinden emin olmak için kılavuzu kullanın [https://aka.ms/mfasetup](https://aka.ms/mfasetup) . Daha fazla bilgi için adım 2 ' nin altında, [Microsoft 365 veri ve hizmetlere erişimi koruma](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)Kılavuzu ' nun adım 3 ' ün altında bulabilirsiniz. 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>2. Aşama: sık kullanılan saldırıları azaltma

![2. aşama sık kullanılan saldırıları azaltır](./media/security-planning/stage-two.png)

Yol haritası 'nın 2. aşaması, kimlik bilgilerinin hırsızlık ve kötüye kullanımı için en sık kullanılan saldırı tekniklerini azaltmaya odaklanır ve yaklaşık 2-4 hafta içinde uygulanabilir. Güvenli ayrıcalıklı erişim yol haritası 'nın bu aşaması aşağıdaki eylemleri içerir.

### <a name="general-preparation"></a>Genel hazırlık

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Hizmetler, sahipler ve yöneticilerin envanterini yürütün

"Kendi cihazını getir" ve ana ilkelerden çalışma ve kablosuz bağlantı artışının büyümesi, ağınıza kimlerin bağlanıldığını izlemek için önemli hale getirir. Güvenlik denetimi, ağınızdaki cihazları, uygulamaları ve programları, kuruluşunuzun desteklemediği ve yüksek riskli riski temsil eden programları açığa çıkarmaz. Daha fazla bilgi için bkz. [Azure Güvenlik yönetimi ve izlemeye genel bakış](../../security/fundamentals/management-monitoring-overview.md). Envanter sürecinizdeki aşağıdaki görevlerin tümünü dahil olduğunuzdan emin olun.

* Yönetici rollerine sahip kullanıcıları ve yönetebilecekleri Hizmetleri belirler.
* Kuruluşunuzdaki hangi kullanıcıların Azure AD 'ye yönetici erişimi olduğunu öğrenmek için Azure AD PıM 'yi kullanın.
* Azure AD 'de tanımlanan rollerin ötesinde Microsoft 365, kuruluşunuzdaki kullanıcılara atayabileceğiniz bir yönetici rolleri kümesiyle birlikte gelir. Her yönetici rolü ortak iş işlevlerine eşlenir ve kuruluşunuzdaki kişilere [Microsoft 365 Yönetim merkezinde](https://admin.microsoft.com)belirli görevleri yapmak için izinler verir. Kuruluşunuzdaki kullanıcıların, Azure AD 'de yönetilmeyen roller aracılığıyla da dahil olmak üzere Microsoft 365 yönetici erişimine sahip olduğunu öğrenmek için Microsoft 365 Yönetim merkezini kullanın. Daha fazla bilgi için bkz. [Office 365 için](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) [yönetici rollerini](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) ve güvenlik uygulamalarını Microsoft 365.
* Kuruluşunuzun Azure, Intune veya Dynamics 365 gibi temel aldığı hizmetlerde envanter yapın.
* Yönetim amaçlarıyla kullanılan hesaplarınızın olduğundan emin olun:

  * Kendisine bağlı çalışma e-posta adresleri var
  * Azure AD Multi-Factor Authentication kaydoldu veya şirket içi MFA 'yı kullanma
* Kullanıcılara yönetim erişimi için iş gerekçesini sorun.
* İhtiyaç duymayacak kişiler ve hizmetler için yönetici erişimini kaldırın.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Yönetim rollerinde iş veya okul hesaplarına geçiş yapması gereken Microsoft hesaplarını tanımla

İlk genel yöneticileriniz Azure AD kullanmaya başladıklarında mevcut Microsoft hesabı kimlik bilgilerini yeniden kullanıyorsa, Microsoft hesaplarını bulut tabanlı veya eşitlenmiş hesaplarla değiştirin.

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Genel yönetici hesapları için ayrı kullanıcı hesapları ve posta iletimi sağlayın

Kişisel e-posta hesapları, kişisel e-posta adreslerini genel yönetici hesapları için kabul edilemez hale getiren bir risk olan siber saldırganlar tarafından düzenli olarak çok fazla İnternet risklerini yönetici ayrıcalıklarından ayırmaya yardımcı olmak için, her kullanıcı için yönetici ayrıcalıklarına sahip adanmış hesaplar oluşturun.

* Kullanıcıların genel yönetici görevlerini yapması için ayrı hesaplar oluşturmayı unutmayın.
* Genel Yöneticilerinizdeki e-postaları yanlışlıkla açmadığından veya programları yönetici hesaplarıyla çalıştırmadığından emin olun.
* Bu hesapların e-postalarını çalışan bir posta kutusuna iletdiğinden emin olun.
* Genel yönetici (ve diğer ayrıcalıklı gruplar) hesapları, şirket içi Active Directory bağlı olmayan salt bulut hesapları olmalıdır.

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Yönetim hesaplarının parolalarının yakın zamanda değiştiğinden emin olun

Tüm kullanıcıların yönetim hesaplarında oturum açdığından ve en son 90 gün içinde parolalarını en az bir kez değiştiğinden emin olun. Ayrıca, tüm paylaşılan hesapların parolalarının yakın zamanda değiştirildiğini doğrulayın.

#### <a name="turn-on-password-hash-synchronization"></a>Parola karması eşitlemesini aç

Azure AD Connect, Kullanıcı parolasının karmasını, şirket içi Active Directory bulut tabanlı bir Azure AD kuruluşuna eşitler. Active Directory Federasyon Hizmetleri (AD FS) (AD FS) ile Federasyon kullanıyorsanız, Parola karması eşitlemesini yedekleme olarak kullanabilirsiniz. Şirket içi Active Directory veya AD FS sunucularınız geçici olarak kullanılamıyorsa bu yedekleme yararlı olabilir.

Parola karması eşitleme, kullanıcıların şirket içi Active Directory örneğinde oturum açmak için kullandıkları aynı parolayı kullanarak bir hizmette oturum açmasını sağlar. Parola karması eşitleme, parola karmalarını tehlikeye atılmak üzere bilinen parolalarla karşılaştırarak, kimlik koruması aşılan kimlik bilgilerini algılamanıza olanak tanır. Daha fazla bilgi için bkz. [Azure AD Connect eşitleme ile parola karması eşitlemeyi uygulama](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>Ayrıcalıklı rollerdeki ve kullanıma sunulan kullanıcılar için çok faktörlü kimlik doğrulaması gerektir

Azure AD, tüm kullanıcılarınız için Multi-Factor Authentication (MFA) gerektirmenizi önerir. Hesapları tehlikeye atılırsa (örneğin, finans ofisler) önemli bir etkiye sahip olan kullanıcıları göz önünde bulundurun. MFA, tehlikeye giren bir parola nedeniyle saldırının riskini azaltır.

Aç:

* Kuruluşunuzdaki tüm kullanıcılar için [koşullu erişim ilkelerini kullanarak MFA](../authentication/howto-mfa-getstarted.md) .

Iş için Windows Hello kullanıyorsanız, MFA gereksinimi Windows Hello oturum açma deneyimi kullanılarak karşılanır. Daha fazla bilgi için bkz. [Windows Hello](/windows/uwp/security/microsoft-passport).

#### <a name="configure-identity-protection"></a>Kimlik korumasını yapılandırma

Azure AD Kimlik Koruması, kuruluşunuzun kimliklerini etkileyen olası güvenlik açıklarını algılayan algoritma tabanlı bir izleme ve raporlama aracıdır. Algılanan şüpheli etkinliklere yönelik otomatikleştirilmiş yanıtları yapılandırabilir ve bunları çözmek için uygun işlemleri gerçekleştirebilirsiniz. Daha fazla bilgi için bkz. [Azure Active Directory Kimlik Koruması](../identity-protection/overview-identity-protection.md).

#### <a name="obtain-your-microsoft-365-secure-score-if-using-microsoft-365"></a>Microsoft 365 güvenli puanınızı edinin (Microsoft 365 kullanıyorsanız)

Güvenli puan, kullandığınız Microsoft 365 hizmetleri için ayarlarınıza ve etkinliklerinize bakar ve bunları Microsoft tarafından belirlenen bir taban çizgisiyle karşılaştırır. Güvenlik uygulamaları ile nasıl hizalandığını temel alarak bir puan alacaksınız. Bir Microsoft 365 İş standart veya kurumsal abonelik için yönetici izinlerine sahip olan herkes, adresindeki güvenli puana erişebilir [https://securescore.office.com](https://securescore.office.com/) .

#### <a name="review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365"></a>Microsoft 365 güvenlik ve Uyumluluk kılavuzunu gözden geçirin (Microsoft 365 kullanıyorsanız)

[Güvenlik ve uyumluluk planı](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) , Office 365 müşterilerinin Office 365 yapılandırmasına ve diğer EMS özelliklerini etkinleştirmesine yönelik yaklaşımı özetler. Daha sonra, [Microsoft 365 ' deki veri ve hizmetlere erişimin nasıl korunacağı](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) hakkında 3-6 adımları ve [Microsoft 365 güvenlik ve uyumluluğu izleme](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6)kılavuzunu gözden geçirin.

#### <a name="configure-microsoft-365-activity-monitoring-if-using-microsoft-365"></a>Microsoft 365 etkinlik Izlemeyi yapılandırın (Microsoft 365 kullanılıyorsa)

Yönetici hesabına sahip olan, ancak bu portallarda oturum açtıklarında Microsoft 365 erişimi gerektirmeyen personeli tanımlamak için Microsoft 365 kullanan kullanıcılar için kuruluşunuzu izleyin. Daha fazla bilgi için, [Microsoft 365 Yönetim merkezinde etkinlik raporları](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263)' na bakın.

#### <a name="establish-incidentemergency-response-plan-owners"></a>Olay/acil durum yanıt planı sahipleri oluşturma

Başarılı bir olay yanıtı özelliği oluşturmak için önemli planlama ve kaynaklar gerekir. Iber saldırıları için sürekli olarak izlemeniz ve olay işlemeye yönelik öncelikler oluşturmanız gerekir. İlişki derlemek ve diğer dahili gruplarla ve plan sahipleriyle iletişim kurmak için olay verilerini toplayın, çözümleyin ve raporlayın. Daha fazla bilgi için bkz. [Microsoft Güvenlik Yanıt Merkezi](https://technet.microsoft.com/security/dn440717).

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Daha önce yapmadıysanız, şirket içi ayrıcalıklı yönetim hesaplarının güvenliğini sağlayın

Azure Active Directory kuruluşunuz şirket içi Active Directory ile eşitlenmişse, [güvenlik ayrıcalıklı erişim yol haritası](/windows-server/identity/securing-privileged-access/securing-privileged-access): Bu aşama şunları içerir:

* Şirket içi yönetim görevleri yapması gereken kullanıcılar için ayrı yönetici hesapları oluşturma
* Active Directory yöneticileri için ayrıcalıklı erişim Iş Istasyonlarını dağıtma
* İş istasyonları ve sunucular için benzersiz yerel yönetici parolaları oluşturma

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure 'a erişimi yöneten kuruluşlar için ek adımlar

#### <a name="complete-an-inventory-of-subscriptions"></a>Aboneliklerin envanterini doldurun

Kuruluşunuzda üretim uygulamalarını barındıran abonelikleri belirlemek için Enterprise Portal 'ı ve Azure portal kullanın.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Microsoft hesaplarını yönetici rollerinden kaldır

Xbox, Live ve Outlook gibi diğer programlardaki Microsoft hesapları, kuruluşunuzun abonelikleri için yönetici hesabı olarak kullanılmamalıdır. Tüm Microsoft hesaplarından yönetici durumunu kaldırın ve Azure AD (örneğin, chris@contoso.com ) iş veya okul hesapları ile değiştirin. Yönetici amacıyla, Azure AD 'de kimlik doğrulaması yapılan ve diğer hizmetlerde bulunmayan hesaplara bağımlıdır.

#### <a name="monitor-azure-activity"></a>Azure etkinliğini izleme

Azure Etkinlik Günlüğü, Azure'da abonelik düzeyindeki olayların geçmişini sağlar. Hangi kaynakları oluşturan, güncelleştiren ve silen ve bu olaylar ne zaman gerçekleştiği hakkında bilgi sunar. Daha fazla bilgi için bkz. [Azure aboneliğinizdeki önemli eylemlerle ilgili bildirimleri denetleme ve alma](../../azure-monitor/alerts/alerts-activity-log.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Azure AD aracılığıyla diğer bulut uygulamalarına erişimi yöneten kuruluşlar için ek adımlar

#### <a name="configure-conditional-access-policies"></a>Koşullu erişim ilkelerini yapılandırma

Şirket içi ve bulutta barındırılan uygulamalar için koşullu erişim ilkeleri hazırlayın. Kullanıcılarınızın çalışma alanına katılmış cihazları varsa, [Azure Active Directory cihaz kaydı kullanarak şirket Içi koşullu erişim ayarlamalarından](../../active-directory-b2c/overview.md)daha fazla bilgi alın.

## <a name="stage-3-take-control-of-admin-activity"></a>3. Aşama: yönetici etkinliğinin denetimini al

![3. Aşama: yönetici etkinliğinin denetimini al](./media/security-planning/stage-three.png)

3. Aşama 2. aşama üzerinde azaltmalar üzerinde yapılar ve yaklaşık 1-3 ay içinde uygulanmalıdır. Güvenli ayrıcalıklı erişim yol haritası 'nın bu aşaması aşağıdaki bileşenleri içerir.

### <a name="general-preparation"></a>Genel hazırlık

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Yönetici rollerindeki kullanıcıların erişim incelemesini tamamlar

Daha fazla şirket kullanıcısı bulut hizmetleri aracılığıyla ayrıcalıklı erişim elde edebilir ve bu, yönetilmeyen erişime yol açabilir. Günümüzde kullanıcılar Microsoft 365, Azure aboneliği yöneticileri için küresel yöneticilere veya sanal makinelere veya SaaS uygulamalarına yönetici erişimine sahip olabilir.

Kuruluşunuz, tüm çalışanların olağan iş işlemlerini ayrıcalıksız kullanıcılar olarak işlemesini ve sonra yalnızca gerektiğinde yönetici hakları vermesini sağlamalıdır. Yönetici ayrıcalıklarını etkinleştirmeye uygun olan kullanıcıları tanımlamak ve onaylamak için erişim gözden geçirmeleri tamamlanmıştır.

Şunları yapmanızı öneririz:

1. Hangi kullanıcıların Azure AD yöneticileri olduğunu belirleme, isteğe bağlı, tam zamanında yönetici erişimi ve rol tabanlı güvenlik denetimlerini etkinleştirme.
2. Yönetici ayrıcalıklı erişimi için açık bir gerekçe olmayan kullanıcıları farklı bir role dönüştürün (uygun rol yoksa, bunları kaldırın).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Tüm kullanıcılar için daha güçlü kimlik doğrulaması dağıtımına devam et

Yüksek oranda kullanıma sunulan kullanıcıların Azure AD MFA veya Windows Hello gibi modern, güçlü kimlik doğrulamasına sahip olmasını gerektirir. Yüksek düzeyde açığa çıkarılan kullanıcıların örnekleri şunlardır:

* C paketi yöneticileri
* Üst düzey yöneticiler
* Kritik BT ve güvenlik personeli

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Azure AD yönetimi için adanmış iş istasyonlarını kullanma

Saldırganlar, verilerin bütünlüğünü ve gerçekliğini kesintiye uğramaları için ayrıcalıklı hesapları hedefleyebilir. Genellikle, yönetici bir kimlik bilgisi girerek program mantığını veya gözeops 'u değiştiren kötü amaçlı kod kullanırlar. Ayrıcalıklı Erişim İş İstasyonları (PAW), hassas görevler için İnternet saldırıları ve tehdit vektörlerinden korunan ayrılmış bir işletim sistemi sağlar. Bu hassas görevler ve hesapların günlük kullanım iş istasyonları ve cihazlarından ayrılması, ' den güçlü koruma sağlar:

* Sızdırma saldırıları
* Uygulama ve işletim sistemi güvenlik açıkları
* Kimliğe bürünme saldırıları
* Tuş vuruşu günlüğü, karma değer geçişi ve bilet geçişi gibi kimlik bilgileri hırsızlığı saldırıları

Ayrıcalıklı erişim iş istasyonlarını dağıtarak, yöneticilerin kimlik bilgilerini sağlamlaştırılmış bir masaüstü ortamında girmesi riskini azaltabilirsiniz. Daha fazla bilgi için bkz. [ayrıcalıklı erişim Iş istasyonları](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Olayları işlemeye yönelik ulusal standartlar ve teknoloji önerilerini gözden geçirin

Ulusal Standartlar ve Teknoloji Enstitüsü (NıST), özellikle olayla ilgili verileri analiz etmek ve her olaya uygun yanıtı belirlemek için olay işleme için yönergeler sağlar. Daha fazla bilgi için bkz. [(NIST) bilgisayar güvenliği olay Işleme Kılavuzu (SP 800-61, düzeltme 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Ek yönetim rollerine JıT için Privileged Identity Management (PıM) uygulayın

Azure Active Directory için [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) özelliğini kullanın. Ayrıcalıklı rollerin zaman sınırlı etkinleştirilmesi şunları sağlar:

* Belirli bir görevi yapmak için yönetici ayrıcalıklarını etkinleştirin
* Etkinleştirme işlemi sırasında MFA 'yı zorlama
* Yöneticilere bant dışı değişiklikler hakkında bilgi vermek için uyarıları kullanın
* Kullanıcıların, önceden yapılandırılmış bir süre için ayrıcalıklı erişimini tutmasını sağlama
* Güvenlik yöneticilerinin şunları yapmasına izin ver:

  * Tüm ayrıcalıklı kimlikleri bul
  * Denetim raporlarını görüntüleme
  * Yönetici ayrıcalıklarını etkinleştirmeye uygun olan her kullanıcıyı tanımlamak için erişim İncelemeleri oluşturun

Zaten Azure AD Privileged Identity Management kullanıyorsanız zamana ait ayrıcalıklar için zaman çerçevesini gerektiği gibi ayarlayın (örneğin, bakım pencereleri).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Parola tabanlı oturum açma protokollerinin görünürlüğünü belirleme (Exchange Online kullanıyorsanız)

Kimlik bilgileri tehlikeye atılırsa, kuruluşa çok zararlı olabilecek tüm olası kullanıcıları tanımlamanızı öneririz. Bu kullanıcılar için, Kullanıcı adı ve parola kullanarak e-postalarına oturum açmasını sağlamak üzere güçlü kimlik doğrulama gereksinimleri yerleştirin ve Azure AD koşullu erişim 'i kullanın. [Koşullu erişimi kullanarak eski kimlik doğrulamasını](../conditional-access/block-legacy-authentication.md)engelleyebilirsiniz ve Exchange Online aracılığıyla [temel kimlik doğrulamasını engelleyebilirsiniz](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) .

#### <a name="complete-a-roles-review-assessment-for-microsoft-365-roles-if-using-microsoft-365"></a>Microsoft 365 rolleri için bir rol gözden geçirme değerlendirmesi tamamlamayı doldurun (Microsoft 365 kullanılıyorsa)

Tüm Yöneticiler kullanıcılarının doğru rollerde olup olmadığını değerlendirin (bu değerlendirmeye göre Sil ve yeniden ata).

#### <a name="review-the-security-incident-management-approach-used-in-microsoft-365-and-compare-with-your-own-organization"></a>Microsoft 365 ' de kullanılan güvenlik olay yönetimi yaklaşımını gözden geçirin ve kendi kuruluşunuzla karşılaştırın

Bu raporu, [Microsoft 365 'de güvenlik olay yönetimi](https://www.microsoft.com/download/details.aspx?id=54302)'nden indirebilirsiniz.

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Şirket içi ayrıcalıklı yönetim hesaplarını güvenli hale getirmeye devam edin

Azure Active Directory Şirket içi Active Directory bağlıysa, [güvenlik ayrıcalıklı erişim yol haritası](/windows-server/identity/securing-privileged-access/securing-privileged-access): Aşama 2 ' deki yönergeleri izleyin. Bu aşamada şunları yapabilirsiniz:

* Tüm Yöneticiler için ayrıcalıklı erişim Iş Istasyonları dağıtma
* MFA gerektirme
* Etki alanı denetleyicisi bakımı için yalnızca yeterli yönetici kullanın, etki alanlarının saldırı yüzeyini kısın
* Saldırı algılama için Gelişmiş tehdit değerlendirmesi dağıtma

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure 'a erişimi yöneten kuruluşlar için ek adımlar

#### <a name="establish-integrated-monitoring"></a>Tümleşik izleme oluşturma

[Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md):

* Azure abonelikleriniz genelinde tümleşik güvenlik izleme ve ilke yönetimi sağlar
* Aksi takdirde açıklanabilecek tehditleri algılamaya yardımcı olur
* Geniş kapsamlı güvenlik çözümleri dizisiyle birlikte çalışarak

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Barındırılan sanal makineler içindeki ayrıcalıklı hesaplarınızın envanterini çıkarın

Genellikle kullanıcılara tüm Azure abonelikleriniz veya kaynaklarınız için kısıtlanmamış izinler vermeniz gerekmez. Yalnızca kendi işlerini yapması gereken kullanıcılarınızın erişimini sağlamak için Azure AD yönetici rolleri ' ni kullanın. Azure AD yönetici rollerini, bir yöneticinin yalnızca bir abonelikteki VM 'Leri yönetmesine izin vermek için kullanabilirsiniz, ancak başka bir deyişle SQL veritabanlarını aynı abonelik içinde yönetebilir. Daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi nedir?](../../active-directory-b2c/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Azure AD yönetici rolleri için PıM uygulama

Azure kaynaklarına erişimi yönetmek, denetlemek ve izlemek için Azure AD yönetici rolleriyle ayrıcalıklı kimlik yönetimi kullanın. , Ayrıcalıkların etkilenme süresini azaltarak ve görünürlüğünüzü raporlar ve uyarılar aracılığıyla kullanım için artırarak PıM 'nin kullanılması. Daha fazla bilgi için bkz. [Azure AD Privileged Identity Management nedir?](../privileged-identity-management/pim-configure.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>İlgili Azure günlüklerini SıEM sistemlerinize göndermek için Azure günlük tümleştirmelerini kullanın

Azure günlük tümleştirmesi, Azure kaynaklarınızdan ham günlükleri kuruluşunuzun mevcut güvenlik bilgileri ve olay yönetimi (SıEM) sistemleriyle tümleştirmenize olanak sağlar. [Azure günlük tümleştirmesi](/previous-versions/azure/security/fundamentals/azure-log-integration-overview) , Windows Olay Görüntüleyicisi günlüklerinden ve Azure kaynaklarından Windows olaylarını toplar:

* Azure etkinlik günlükleri
* Azure Güvenlik Merkezi uyarıları
* Azure kaynak günlükleri

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Azure AD aracılığıyla diğer bulut uygulamalarına erişimi yöneten kuruluşlar için ek adımlar

#### <a name="implement-user-provisioning-for-connected-apps"></a>Bağlı uygulamalar için Kullanıcı sağlamayı uygulama

Azure AD, Dropbox, Salesforce ve ServiceNow gibi bulut uygulamalarında kullanıcı kimliklerinin oluşturulmasını ve bakımının otomatik hale getirmenize olanak tanır. Daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Tümleştirme bilgi koruması

Microsoft Cloud App Security, dosyaları araştırmanıza ve Azure Information Protection sınıflandırma etiketlerine göre ilke ayarlamanıza olanak tanıyarak, bulut verilerinizin daha fazla görünürlüğünü ve denetimini etkinleştirir. Bulutta dosyaları tarayın ve sınıflandırın ve Azure Information Protection etiketlerini uygulayın. Daha fazla bilgi için bkz. [Azure Information Protection tümleştirme](/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Koşullu erişimi yapılandırma

[SaaS uygulamaları](https://azure.microsoft.com/overview/what-is-saas/) ve Azure AD bağlı uygulamaları için bir grup, konum ve uygulama duyarlılığı temelinde koşullu erişimi yapılandırın. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Bağlı bulut uygulamalarındaki etkinlikleri izleme

Kullanıcı erişiminin bağlı uygulamalarda da korunduğundan emin olmak için [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) kullanmanızı öneririz. Bu özellik, bulut uygulamalarına kurumsal erişimin güvenliğini sağlar ve yönetici hesaplarınızın güvenliğini sağlar ve şunları yapabilirsiniz:

* Görünürlük ve denetimi bulut uygulamalarına genişletme
* Erişim, etkinlikler ve veri paylaşımı için ilkeler oluşturma
* Riskli etkinlikleri, olağan dışı davranışları ve tehditleri otomatik olarak belirleyin
* Veri sızıntısını önleme
* Risk ve otomatik tehdit önleme ve ilke zorlamayı en aza indirme

Cloud App Security SıEM Aracısı, Microsoft 365 uyarıları ve etkinliklerini merkezi olarak izlemeyi etkinleştirmek için SıEM sunucunuz ile Cloud App Security tümleştirir. Sunucunuzda çalışır ve Cloud App Security uyarı ve etkinlik çeker ve bunları SıEM sunucusuna akıp. Daha fazla bilgi için bkz. [SIEM tümleştirmesi](/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses"></a>4. Aşama: savunma oluşturmaya devam edin

![4. Aşama: etkin güvenlik duruşunu benimseyin](./media/security-planning/stage-four.png)

Yol haritasının 4. aşaması altı ayda ve daha fazla uygulanmalıdır. Günümüzde bilinen saldırılara karşı ayrıcalıklı erişim korumalarınızı güçlendirin. Yarın güvenlik tehditleri için, maliyetleri yükseltmek ve ortamınızı hedefleyen duyuru başarı oranını azaltmak için güvenliği devam eden bir işlem olarak görüntülemeniz önerilir.

Ayrıcalıklı erişimin güvenliğini sağlamak, iş varlıklarınız için güvenlik hakkı sağlamak açısından önemlidir. Bununla birlikte, sürekli güvenlik kesintileri sağlayan bir bütün güvenlik programının parçası olmalıdır. Bu program şöyle bir öğe içermelidir:

* İlke
* Operations
* Bilgi güvenliği
* Sunucular
* Uygulamalar
* Bilgisayarlar
* Cihazlar
* Bulut dokusu

Ayrıcalıklı erişim hesaplarını yönetirken aşağıdaki yöntemleri öneririz:

* Yöneticilerin, günlük işleri ayrıcalıksız kullanıcılar olarak kullandığından emin olun
* Yalnızca gerektiğinde ayrıcalıklı erişim verin ve daha sonra kaldırın (tam zamanında)
* Ayrıcalıklı hesaplarla ilişkili denetim etkinlik günlüklerini sakla

Tüm güvenlik yol haritası oluşturma hakkında daha fazla bilgi için bkz. [Microsoft Cloud It Architecture kaynakları](https://almbok.com/office365/microsoft_cloud_it_architecture_resources). Yol Haritalarınızın herhangi bir bölümünü uygulamanıza yardımcı olmak üzere Microsoft hizmetleriyle birlikte çalışmak için Microsoft temsilcinize başvurun veya bkz. [kuruluşunuzu korumak Için derleme açısından kritik siber savunmaları](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Güvenli ayrıcalıklı erişim yol haritası 'nın bu son devam eden aşaması aşağıdaki bileşenleri içerir.

### <a name="general-preparation"></a>Genel hazırlık

#### <a name="review-admin-roles-in-azure-ad"></a>Azure AD 'de yönetici rollerini gözden geçirme

Mevcut yerleşik Azure AD Yönetici rollerinin hala güncel olup olmadığını ve kullanıcıların yalnızca ihtiyaç duydukları rollerde bulunduğundan emin olun. Azure AD ile farklı işlevlere hizmeti sağlamak için ayrı yöneticiler atayabilirsiniz. Daha fazla bilgi için bkz. [Azure Active Directory yönetici rolleri atama](permissions-reference.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Azure AD 'ye katılmış cihazların yönetimine sahip kullanıcıları gözden geçirme

Daha fazla bilgi için bkz. [karma Azure Active Directory katılmış cihazları yapılandırma](../devices/hybrid-azuread-join-plan.md).

#### <a name="review-members-of-built-in-microsoft-365-admin-roles"></a>[Yerleşik Microsoft 365 Yönetici rollerinin](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) üyelerini gözden geçirme
Microsoft 365 kullanmıyorsanız bu adımı atlayın.
‎
#### <a name="validate-incident-response-plan"></a>Olay yanıtı planını doğrula

Planınızı geliştirmek için, Microsoft planınızın beklendiği gibi çalıştığını düzenli olarak doğrulamanızı önerir:

* Eksik olanları görmek için mevcut yol eşlemenizi inceleyin
* Postmordıtem analizine bağlı olarak, var olan veya yeni uygulamaları tanımla ' yı gözden geçirin
* Güncelleştirilmiş olay yanıt planınız ve uygulamalarınızı kuruluşunuzun tamamında dağıttığınızdan emin olun


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure 'a erişimi yöneten kuruluşlar için ek adımlar 

[Bir Azure aboneliğinin sahipliğini başka bir hesaba aktarmanız](../../cost-management-billing/manage/billing-subscription-transfer.md)gerekip gerekmediğini belirleme.
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Cam kesmesi": acil durum

![Acil durum kesme camı erişimi hesapları](./media/security-planning/emergency.jpeg)

1. Olay hakkında bilgi ile anahtar yöneticilerine ve güvenlik ofislere bildirim gönderin.

2. Saldırı PlayBook 'unu gözden geçirin.

3. Azure AD 'de oturum açmak için "cam" hesabı Kullanıcı adı ve parola birleşimine erişin.

4. [Azure destek isteği açarak](../../azure-portal/supportability/how-to-create-azure-support-request.md)Microsoft 'tan yardım alın.

5. [Azure AD oturum açma raporlarına](../reports-monitoring/overview-reports.md)bakın. Oluşan bir olay ve rapora dahil edildiğinde bir süre olabilir.

6. Karma ortamlarda, şirket içi altyapınız federe ve AD FS sunucunuz kullanılabilir değilse, geçici olarak, Federasyon kimlik doğrulamasından parola karma eşitlemesini kullanacak şekilde geçiş yapabilirsiniz. Bu anahtar, AD FS sunucusu kullanılabilir hale gelene kadar etki alanı federasyonunu yönetilen kimlik doğrulamasına geri döndürür.

7. Ayrıcalıklı hesaplar için e-postayı izleyin.

8. Olası bir ve yasal araştırma için ilgili günlüklerin yedeklerini kaydettiğinizden emin olun.

Microsoft Office 365 güvenlik olaylarını nasıl işleyeceği hakkında daha fazla bilgi için, bkz. [Microsoft Office 365 ' de güvenlik olay yönetimi](https://aka.ms/Office365SIM).

## <a name="faq-answers-for-securing-privileged-access"></a>SSS: ayrıcalıklı erişimi güvenli hale getirme yanıtları  

**S:** Henüz bir güvenli erişim bileşeni uygulamadım, ne yapmam gerekiyor?

**Cevap:** En az iki adet kesme camı hesabı tanımlayın, ayrıcalıklı yönetici hesaplarınıza MFA atayın ve küresel yönetici hesaplarından Kullanıcı hesaplarını ayırın.

**S:** Bir ihlal sonrasında ilk olarak ilgilenilmesi gereken en önemli sorun nedir?

**Cevap:** Yüksek düzeyde kullanıma sunulan bireyler için en güçlü kimlik doğrulaması gerektirdiğinizden emin olun.

**S:** Ayrıcalıklı yöneticilerimiz devre dışı bırakılmışsa ne olur?

**Cevap:** Her zaman güncel tutulan bir genel yönetici hesabı oluşturun.

**S:** Yalnızca bir genel yönetici ayrıldıysa ve bunlara ulaşılamadığından ne olur?

**Cevap:** Acil erişim sağlamak için, kesme camı hesaplarından birini kullanın.

**S:** Kuruluşumun içindeki yöneticileri nasıl koruyabilirim?

**Cevap:** Yöneticilerin her zaman gündelik işletmelerini standart "ayrıcalıksız" kullanıcılar olarak yapması gerekir.

**S:** Azure AD 'de yönetici hesapları oluşturmak için en iyi uygulamalar nelerdir?

**Cevap:** Belirli yönetici görevleri için ayrıcalıklı erişim ayırın.

**S:** Kalıcı yönetici erişimini azaltmak için hangi araçlar var?

**Cevap:** Privileged Identity Management (PıM) ve Azure AD yönetici rolleri.

**S:** Yönetici hesaplarını Azure AD 'ye eşitlemeye ilişkin Microsoft konumu nedir?

**Cevap:** Katman 0 Yönetici hesapları yalnızca şirket içi AD hesapları için kullanılır. Bu tür hesaplar genellikle buluttaki Azure AD ile eşitlenmez. Katman 0 Yönetici hesapları, şirket içi Active Directory ormanı, etki alanları, etki alanı denetleyicileri ve varlıkların doğrudan veya dolaylı yönetim denetimine sahip hesapları, grupları ve diğer varlıkları içerir.

**S:** Yöneticilerin portalda rastgele yönetici erişimi atamasını nasıl sağlıyoruz?

**Cevap:** Tüm kullanıcılar ve yöneticiler için ayrıcalıklı olmayan hesaplar kullanın. Hangi yönetici hesaplarının ayrıcalıklı olacağını öğrenmek için kuruluşun parmak izini geliştirerek başlayın. Ve yeni oluşturulan yönetici kullanıcıları için izleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Ürün güvenliği Için Microsoft Güven Merkezi](https://www.microsoft.com/trustcenter/security) – Microsoft bulut ürünlerinin ve hizmetlerinin güvenlik özellikleri

* [Microsoft Güven Merkezi-uyumluluk](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – Microsoft 'un bulut hizmetleri için kapsamlı bir uyumluluk teklifleri kümesi

* [Risk değerlendirmesi yapma hakkında yönergeler](https://www.microsoft.com/trustcenter/guidance/risk-assessment) -Microsoft bulut hizmetleri için güvenlik ve uyumluluk gereksinimlerini yönetme

### <a name="other-microsoft-online-services"></a>Diğer Microsoft Çevrimiçi Hizmetleri

* [Microsoft Intune güvenliği](https://www.microsoft.com/trustcenter/security/intune-security) – Intune, buluttan mobil cihaz yönetimi, mobil uygulama YÖNETIMI ve bilgisayar yönetimi özellikleri sağlar.

* [Microsoft dynamics 365 güvenliği](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365, müşteri ilişkileri YÖNETIMI (CRM) ve kurumsal kaynak planlama (ERP) yeteneklerini birleştiren Microsoft bulut tabanlı çözümüdür.
