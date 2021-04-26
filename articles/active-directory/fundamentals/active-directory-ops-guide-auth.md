---
title: Azure Active Directory kimlik doğrulaması yönetim işlemleri başvuru kılavuzu
description: Bu işlemler başvuru kılavuzu, kimlik doğrulama yönetimini güvenli hale getirmek için gerçekleştirmeniz gereken denetimleri ve eylemleri açıklar
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 26b5331aa9242978f0f097c8e90bc807fc65f745
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531937"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure Active Directory kimlik doğrulaması yönetim işlemleri başvuru kılavuzu

[Azure AD işlemler başvuru kılavuzu](active-directory-ops-guide-intro.md) 'nun bu bölümünde, kimlik bilgilerini güvenli hale getirmek ve yönetmek, kimlik doğrulama deneyimini tanımlamak, temsilci atama, kullanım ölçmek ve kurumsal güvenlik duruşunu temel alan erişim ilkeleri tanımlamak için gerçekleştirmeniz gereken denetimler ve eylemler açıklanmaktadır.

> [!NOTE]
> Bu öneriler, yayımlama tarihi itibariyle geçerli olmakla kalmaz, zaman içinde değişebilir. Kuruluşlar, Microsoft ürün ve Hizmetleri zaman içinde geliştikçe kimlik uygulamalarını sürekli olarak değerlendirmelidir.

## <a name="key-operational-processes"></a>Anahtar işletimsel işlemler

### <a name="assign-owners-to-key-tasks"></a>Anahtar görevlere sahipler atama

Azure Active Directory Yönetimi, önemli işlem görevlerinin ve işlemlerin sürekli yürütülmesini gerektirir ve bu, bir dağıtım projesinin parçası olmayabilir. Ortamınızı iyileştirmek için bu görevleri ayarlamanız de önemlidir. Önemli görevler ve bunların önerilen sahipleri şunlardır:

| Görev | Sahip |
| :- | :- |
| Azure AD 'de çoklu oturum açma (SSO) yapılandırmasının yaşam döngüsünü yönetme | IAM Işlemler ekibi |
| Azure AD uygulamaları için koşullu erişim ilkeleri tasarlama | InfoSec mimarisi ekibi |
| SıEM sisteminde arşiv oturum açma etkinliği | InfoSec Işlemler ekibi |
| SıEM sistemindeki risk olaylarını arşivleme | InfoSec Işlemler ekibi |
| Güvenlik raporlarını önceliklendirme ve araştır | InfoSec Işlemler ekibi |
| Risk olaylarını önceliklendirme ve araştırın | InfoSec Işlemler ekibi |
| Azure AD Kimlik Koruması risk ve güvenlik açığı raporları için işaretlenen kullanıcıları önceliklendirme ve araştır | InfoSec Işlemler ekibi |

> [!NOTE]
> Azure AD Kimlik Koruması, bir Azure AD Premium P2 lisansı gerektirir. Gereksinimlerinize uygun lisans bulmak için bkz. [Azure AD ücretsiz ve Azure AD Premium sürümlerinin genel olarak kullanılabilir özelliklerini karşılaştırma](https://azure.microsoft.com/pricing/details/active-directory/).

Listenizi gözden geçirdikten sonra, sahibi eksik olan görevler için bir sahip atamanız veya yukarıdaki önerilere göre hizalanmamış olan sahiplerin sahipliğini ayarlamanız gerekebilir.

#### <a name="owner-recommended-reading"></a>Sahibi tarafından önerilen okuma

- [Azure Active Directory’de yönetici rolü atama](../roles/permissions-reference.md)
- [Azure’da idare](../../governance/index.yml)

## <a name="credentials-management"></a>Kimlik bilgileri yönetimi

### <a name="password-policies"></a>Parola ilkeleri

Parolaların güvenli bir şekilde yönetilmesi, kimlik ve erişim yönetiminin en kritik parçalarından biridir ve genellikle saldırıların en büyük hedefleridir. Azure AD, saldırının başarılı olmasını önlemeye yardımcı olabilecek çeşitli özellikleri destekler.

Giderilmesi gereken sorunu azaltmaya yönelik önerilen çözümü bulmak için aşağıdaki tabloyu kullanın:

| Sorun | Öneri |
| :- | :- |
| Zayıf parolalara karşı koruma mekanizması yoktur | Azure AD [self servis parola sıfırlama (SSPR)](../authentication/concept-sspr-howitworks.md) ve [parola korumasını](../authentication/concept-password-ban-bad-on-premises.md) etkinleştirme |
| Sızdırılan parolaları algılamaya yönelik bir mekanizma yok | Öngörüler kazanmak için [Parola karması eşitlemesini](../hybrid/how-to-connect-password-hash-synchronization.md) (PHS) etkinleştirin |
| AD FS kullanma ve yönetilen kimlik doğrulamasına taşınamıyor | [AD FS Extranet akıllı kilitleme](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) ve/veya [Azure AD akıllı kilitleme](../authentication/howto-password-smart-lockout.md) 'yi etkinleştirme |
| Parola ilkesi, uzunluk, birden çok karakter kümesi veya süre sonu gibi karmaşıklık tabanlı kurallar kullanır | [Önerilen Microsoft uygulamaları](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) için göz önünde bulundurun ve parola yönetimine yaklaşımınızı geçirin ve [Azure AD parola korumasını](../authentication/concept-password-ban-bad.md)dağıtın. |
| Kullanıcılar Multi-Factor Authentication (MFA) kullanmak üzere kayıtlı değil | Kullanıcının kimliğini parolasıyla birlikte doğrulama mekanizması olarak kullanılabilmesi için [tüm kullanıcıların güvenlik bilgilerini kaydedin](../identity-protection/howto-identity-protection-configure-mfa-policy.md) |
| Kullanıcı riskini temel alan parolaların iptali yoktur | SSPR kullanarak sızdırılan kimlik bilgilerinde parola değişikliklerini zorlamak için Azure AD [kimlik koruması Kullanıcı risk ilkelerini](../identity-protection/howto-identity-protection-configure-risk-policies.md) dağıtma |
| Tanınan IP adreslerinden gelen kötü amaçlı kimlik doğrulamasını korumak için akıllı kilitleme mekanizması yoktur | Parola karması eşitleme veya [geçişli kimlik doğrulaması](../hybrid/how-to-connect-pta-quick-start.md) (PTA) ile bulut tarafından yönetilen kimlik doğrulaması dağıtma |

#### <a name="password-policies-recommended-reading"></a>Parola ilkelerinin okunması önerilir

- [Azure AD ve AD FS en iyi uygulamalar: parola spreyi saldırılarına karşı savunma-Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Self servis parola sıfırlama ve parola korumasını etkinleştirme

Parolalarını değiştirmesi veya sıfırlaması gereken kullanıcılar, en büyük birim kaynaklarından ve yardım masası çağrılarının maliyetinden biridir. Maliyete ek olarak, Kullanıcı riskini azaltmak için parolayı bir araç olarak değiştirmek, kuruluşunuzun güvenlik duruşunu geliştirmekte olan temel bir adımdır.

En azından Azure AD [self servis parola sıfırlama](../authentication/concept-sspr-howitworks.md) (SSPR) ve şirket içi [parola korumasını](../authentication/howto-password-ban-bad-on-premises-deploy.md) dağıtmanız önerilir:

- Yardım Masası çağrılarını erteleme.
- Geçici parolaların kullanımını değiştirin.
- Şirket içi bir çözüme dayanan mevcut bir self servis parola yönetimi çözümünü değiştirin.
- Kuruluşunuzdaki [zayıf parolaları kaldırın](../authentication/concept-password-ban-bad.md) .

> [!NOTE]
> Azure AD Premium P2 aboneliği olan kuruluşlar için SSPR 'nin dağıtılması ve [kimlik koruması Kullanıcı risk ilkesinin](../identity-protection/howto-identity-protection-configure-risk-policies.md)bir parçası olarak kullanılması önerilir.

### <a name="strong-credential-management"></a>Güçlü kimlik bilgisi yönetimi

Kötü aktörlerin ortamınıza erişim kazanmasını engellemek için kendilerine göre parolalar yeterince güvenli değildir. En azından, ayrıcalıklı hesabı olan tüm kullanıcıların Multi-Factor Authentication (MFA) için etkinleştirilmesi gerekir. İdeal olarak, [Birleşik kayıt](../authentication/concept-registration-mfa-sspr-combined.md) [deneyimini](../user-help/security-info-setup-signin.md)kullanarak tüm kullanıcıların MFA ve SSPR 'ye kaydolması gerekir. Sonuç olarak, öngörülemeyen koşullar nedeniyle kilitleme riskini azaltmak için [esnekliği sağlamak](../authentication/concept-resilient-controls.md) üzere bir strateji benimsemenizi öneririz.

![Birleşik Kullanıcı deneyimi akışı](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Şirket içi kesinti kimlik doğrulama dayanıklılığı

Basitlik avantajları ve sızdırılan kimlik bilgisi algılamayı etkinleştirmenin yanı sıra, Azure AD Parola karması eşitleme (PHS) ve Azure AD MFA, kullanıcıların SaaS uygulamalarına erişmesine ve [Notpetya gibi Sife](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/)nedeniyle Şirket içi kesintiler artma içinde Microsoft 365 olanak tanır. Ayrıca, Federasyonla birlikte da PHS 'yi etkinleştirmek mümkündür. Fes 'nin etkinleştirilmesi, Federasyon Hizmetleri kullanılabilir olmadığında kimlik doğrulamanın geri yüklenmesine izin verir.

Şirket içi kuruluşunuzda bir kesinti dayanıklılık stratejisi yoksa veya Azure AD ile tümleştirilmiş olmayan bir sorun varsa, Azure AD PHS 'yi dağıtmanız ve PHS içeren bir olağanüstü durum kurtarma planı tanımlamanız gerekir. Azure AD PHS 'in etkinleştirilmesi, kullanıcıların Azure AD 'de kimlik doğrulamasından geçmesini sağlar ve şirket içi Active Directory kullanılamaz hale gelmelidir.

![Parola karması eşitleme akışı](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Kimlik doğrulama seçeneklerinizi daha iyi anlamak için bkz. [Azure Active Directory hibrit kimlik çözümünüz için doğru kimlik doğrulama yöntemini seçme](../hybrid/choose-ad-authn.md).

### <a name="programmatic-usage-of-credentials"></a>Kimlik bilgilerinin programlı kullanımı

PowerShell veya Microsoft Graph API kullanan uygulamalar kullanan Azure AD betikleri, güvenli kimlik doğrulaması gerektirir. Bu komut dosyalarını ve araçları yürüten zayıf kimlik bilgisi yönetimi, kimlik bilgilerinin hırsızlık riskini artırır. Sabit kodlanmış parolalara veya parola istemlerine dayalı betikler veya uygulamalar kullanıyorsanız, öncelikle yapılandırma dosyalarında veya kaynak kodunda parolaları gözden geçirmeniz, sonra bu bağımlılıkları değiştirmeniz ve mümkün olduğunda Azure yönetilen kimliklerini, Integrated-Windows kimlik doğrulamasını veya [sertifikaları](../reports-monitoring/tutorial-access-api-with-certificates.md) kullanmanız gerekir. Önceki çözümlerin mümkün olmadığı uygulamalar için [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)kullanmayı göz önünde bulundurun.

Parola kimlik bilgileri ile hizmet sorumluları olduğunu tespit ederseniz ve bu parola kimlik bilgilerinin betikler veya uygulamalar tarafından nasıl sağlandığı hakkında emin değilseniz, kullanım düzenlerini daha iyi anlamak için uygulamanın sahibine başvurun.

Microsoft ayrıca, parola kimlik bilgilerine sahip hizmet sorumluları varsa kullanım düzenlerini anlamak için uygulama sahiplerine başvurmanızı da önerir.

## <a name="authentication-experience"></a>Kimlik doğrulama deneyimi

### <a name="on-premises-authentication"></a>Şirket içi kimlik doğrulaması

Tümleşik Windows kimlik doğrulaması (ıWA) veya kesintisiz tek Sign-On (SSO) ile yönetilen kimlik doğrulamasıyla, Parola karması eşitleme veya doğrudan kimlik doğrulama ile federal kimlik doğrulaması, şirket ağının içinde şirket içi etki alanı denetleyicilerine göz atın. Kimlik bilgisi istemi FAG 'sini en aza indirir ve kullanıcıların kimlik avı saldırılarına karşı daha fazla erişme riskini azaltır. Daha önce PHS veya PTA ile bulut tarafından yönetilen kimlik doğrulaması kullanıyorsanız, ancak kullanıcıların şirket içinde kimlik doğrulaması yaparken parolalarını yazmaları gerekiyorsa, [sorunsuz SSO](../hybrid/how-to-connect-sso.md)'yu hemen dağıtmanız gerekir. Diğer taraftan, şu anda, son olarak bulut tarafından yönetilen kimlik doğrulamasına geçirmeye yönelik planlarınız varsa, geçiş projesinin bir parçası olarak sorunsuz SSO uygulamanız gerekir.

### <a name="device-trust-access-policies"></a>Cihaz güven erişimi ilkeleri

Kuruluşunuzdaki bir kullanıcı gibi, bir cihaz da korumak istediğiniz çekirdek kimliktir. Kaynaklarınızı istediğiniz zaman ve herhangi bir konumdan korumak için bir cihazın kimliğini kullanabilirsiniz. Dağıtım türü için cihazın ve hesaplama kimlik doğrulaması, güvenlik sonrası ve kullanışlarınızı şu şekilde geliştirir:

- Örneğin, cihaz güvenildiği zaman MFA ile, örneğin, uçuşmaktan kaçınma
- Güvenilmeyen cihazlardan erişimi engelleme
- Windows 10 cihazlarında, [Şirket içi kaynaklarda çoklu oturum açmayı sorunsuz bir şekilde](../devices/azuread-join-sso.md)sağlayın.

Aşağıdaki yöntemlerden birini kullanarak cihaz kimliklerini ve bunları Azure AD 'de yöneterek bu hedefi gerçekleştirebilirsiniz:

- Kuruluşlar, cihazı yönetmek ve uyumluluk ilkelerini zorlamak, cihaz durumunu doğrulamak ve cihazın uyumlu olup olmadığına bağlı olarak koşullu erişim ilkeleri ayarlamak için [Microsoft Intune](/intune/what-is-intune) kullanabilir. Microsoft Intune iOS cihazlarını, Mac masaüstlerini (JAMF tümleştirmesi aracılığıyla), Windows Masaüstü 'nü (Windows 10 için mobil cihaz yönetimini ve Microsoft uç noktası Configuration Manager ile birlikte ortak yönetimi kullanarak) ve Android mobil cihazlarını yönetebilir.
- [Hibrit Azure AD katılımı](../devices/hybrid-azuread-join-managed-domains.md) , Active Directory etki alanına katılmış bilgisayarlar cihazlarından oluşan bir ortamda Grup Ilkeleri veya Microsoft uç noktası Configuration Manager yönetim sağlar. Kuruluşlar, sorunsuz SSO ile PHS ya da PTA aracılığıyla yönetilen bir ortam dağıtabilir. Cihazlarınızı Azure AD 'ye getirmek, bulut ve şirket içi kaynaklarınız üzerindeki SSO aracılığıyla Kullanıcı üretkenliğini en üst düzeye çıkarır. bu sayede, bulut ve şirket içi kaynaklarınız için aynı anda [koşullu erişimle](../conditional-access/overview.md) erişim sağlamanıza olanak tanır.

Bulutta kayıtlı olmayan, ancak koşullu erişim ilkeleri olmadan etki alanına katılmış Windows cihazlarına veya bulutta kayıtlı olan, etki alanına katılmış Windows cihazlarınız varsa, kayıtsız cihazları kaydetmeniz ve her iki durumda da, koşullu erişim ilkeleriniz üzerinde [bir denetim olarak karma Azure AD JOIN 'i kullanmanız](../conditional-access/require-managed-devices.md) gerekir.

![Karma cihaz gerektiren koşullu erişim ilkesinde verme 'nin ekran görüntüsü](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Cihazları MDM veya Microsoft Intune ile yönetiyorsanız, ancak koşullu erişim ilkelerinizin cihaz denetimlerini kullanmıyorsanız, cihazın bu ilkelerde denetim olarak [uyumlu olarak Işaretlenmesini gerektir](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) seçeneğini kullanmanızı öneririz.

![Koşullu erişim ilkesinde cihaz uyumluluğu gerektiren verme 'nin ekran görüntüsü](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Okuma için önerilen cihaz güven erişimi ilkeleri

- [Nasıl yapılır: karma Azure Active Directory JOIN Uygulamanızı planlayın](../devices/hybrid-azuread-join-plan.md)
- [Kimlik ve cihaz erişim yapılandırmaları](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>İş İçin Windows Hello

Windows 10 ' da, [iş Için Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification) , bilgisayarlarda güçlü iki öğeli kimlik doğrulama ile parolaları değiştirir. Iş için Windows Hello, kullanıcılar için daha kolay bir MFA deneyimi sunar ve parolalarınızın parolasını azaltır. Windows 10 cihazlarını kullanıma almadıysanız veya yalnızca kısmen dağıttıysanız, Windows 10 ' a yükseltmenizi ve tüm cihazlarda [iş Için Windows Hello 'yu etkinleştirmenizi](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) öneririz.

Parolasız kimlik doğrulama hakkında daha fazla bilgi edinmek istiyorsanız, [Azure Active Directory ile parolalar olmadan bir dünya](../authentication/concept-authentication-passwordless.md)bölümüne bakın.

## <a name="application-authentication-and-assignment"></a>Uygulama kimlik doğrulaması ve atama

### <a name="single-sign-on-for-apps"></a>Uygulamalar için çoklu oturum açma

Tüm kuruluş için standartlaştırılmış bir çoklu oturum açma mekanizması sağlamak, en iyi kullanıcı deneyimi, risk azaltma, rapor verme ve idare açısından önemlidir. Azure AD ile SSO 'yu destekleyen, ancak şu anda yerel hesapları kullanacak şekilde yapılandırılmış uygulamalar kullanıyorsanız, bu uygulamaları Azure AD ile SSO kullanacak şekilde yeniden yapılandırmanız gerekir. Benzer şekilde, Azure AD ile SSO 'yu destekleyen, ancak başka bir kimlik sağlayıcısı kullanan uygulamalar kullanıyorsanız, bu uygulamaları Azure AD ile SSO kullanacak şekilde yeniden yapılandırmanız gerekir. Federasyon protokollerini desteklemeyen ancak form tabanlı kimlik doğrulamasını destekleyen uygulamalar için, uygulamayı Azure AD Uygulama Ara Sunucusu ile [parola](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) kullanımını kullanacak şekilde yapılandırmanızı öneririz.

![AppProxy parola tabanlı oturum açma](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Kuruluşunuzda yönetilmeyen uygulamaları bulmak için bir mekanizmanız yoksa, [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security)gibi bir bulut erişim güvenlik Aracısı çözümü (casb) kullanarak bir bulma işlemi uygulamanızı öneririz.

Son olarak, Azure AD uygulama galeriniz varsa ve Azure AD ile SSO 'yu destekleyen uygulamalar kullanıyorsanız, [uygulamayı uygulama galerisinde listelemeyi](../develop/v2-howto-app-gallery-listing.md)öneririz.

#### <a name="single-sign-on-recommended-reading"></a>Okumanız için çoklu oturum açma önerilir

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>AD FS uygulamalarının Azure AD 'ye geçirilmesi

[Uygulamaları AD FS 'Den Azure AD 'ye geçirmek](../manage-apps/migrate-adfs-apps-to-azure.md) , güvenlik, daha tutarlı yönetilebilirlik ve daha iyi işbirliği deneyimi konusunda ek yetenekler sağlar. Azure AD ile SSO 'yu destekleyen AD FS ' de yapılandırılmış uygulamalarınız varsa, bu uygulamaları Azure AD ile SSO kullanacak şekilde yeniden yapılandırmanız gerekir. Azure AD tarafından desteklenmeyen yaygın olmayan yapılandırmalarda AD FS yapılandırılmış uygulamalarınız varsa, özel yapılandırmanın uygulamanın mutlak bir gereksinimi olup olmadığını anlamak için uygulama sahiplerine başvurmanız gerekir. Gerekli değilse, uygulamayı Azure AD ile SSO kullanacak şekilde yeniden yapılandırmanız gerekir.

![Birincil kimlik sağlayıcısı olarak Azure AD](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [ADFS için Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) , Azure AD 'ye geçirilebilecek olabilecek her uygulamayla ilgili yapılandırma ayrıntılarını toplamak üzere kullanılabilir.

### <a name="assign-users-to-applications"></a>Uygulamalara Kullanıcı atama

[Kullanıcılara uygulamalara atama](../manage-apps/assign-user-or-group-access-portal.md) , daha fazla esneklik ve ölçeğe göre yönetme olanağı sağladığından, gruplar kullanılarak en iyi şekilde eşleştirilir. Grupları kullanmanın avantajları, [öznitelik tabanlı dinamik grup üyeliği](../enterprise-users/groups-dynamic-membership.md) ve [uygulama sahiplerine temsilciliğini](../fundamentals/active-directory-accessmanagement-managing-group-owners.md)içerir. Bu nedenle, zaten grupları kullanıyorsanız ve yönetiyorsanız yönetimi artırmak için aşağıdaki işlemleri yapmanızı öneririz:

- Uygulama sahiplerine Grup Yönetimi ve idare verme.
- Uygulamaya self servis erişimine izin verin.
- Kullanıcı özniteliklerinin uygulamalara erişimi tutarlı bir şekilde belirleyebilmesi halinde dinamik grupları tanımlayın.
- [Azure AD erişim gözden geçirmeleri](../governance/access-reviews-overview.md)kullanarak uygulama erişimi için kullanılan gruplara kanıtlama uygulayın.

Öte yandan, bireysel kullanıcılara atama yapan uygulamalar bulursanız, bu uygulamaların etrafında [idare](../governance/index.yml) uyguladığınızdan emin olun.

#### <a name="assign-users-to-applications-recommended-reading"></a>Kullanıcıları okumak için önerilen uygulamalara atama

- [Azure Active Directory bir uygulamaya Kullanıcı ve Grup atama](../manage-apps/assign-user-or-group-access-portal.md)
- [Azure Active Directory 'de uygulama kayıt izinleri verme](../roles/delegate-app-roles.md)
- [Azure Active Directory gruplar için dinamik üyelik kuralları](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>Erişim ilkeleri

### <a name="named-locations"></a>Adlandırılmış konumlar

Azure AD 'de [adlandırılmış konumlar](../reports-monitoring/quickstart-configure-named-locations.md) sayesinde, KURULUŞUNUZDA güvenilir IP adresi aralıklarını etiketleyebilir. Azure AD, aşağıdakileri yapmak için adlandırılmış konumları kullanır:

- Risk olaylarında yanlış pozitif sonuçlar önleyin. Güvenilen bir ağ konumundan oturum açmak, kullanıcının oturum açma riskini azaltır.
- [Konum tabanlı koşullu erişimi](../reports-monitoring/quickstart-configure-named-locations.md)yapılandırın.

![Adlandırılmış konum](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Önceliğe göre, kuruluşunuzun ihtiyaçlarını en iyi şekilde karşılayan önerilen çözümü bulmak için aşağıdaki tabloyu kullanın:

| **Priority** | **Senaryo** | **Öneri** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | PHS veya PTA kullanıyorsanız ve adlandırılmış konumlar tanımlanmamışsa | Risk olaylarının algılanmasını geliştirmek için adlandırılmış konumları tanımlayın |
| 2 | Federe çalışıyorsanız ve "insideCorporateNetwork" talebini kullanmıyorsanız ve adlandırılmış konumlar tanımlanmamışsa | Risk olaylarının algılanmasını geliştirmek için adlandırılmış konumları tanımlayın |
| 3 | Koşullu erişim ilkelerinde adlandırılmış konumlar kullanmıyorsanız ve koşullu erişim ilkelerinde risk veya cihaz denetimi yoksa | Koşullu erişim ilkesini adlandırılmış konumları içerecek şekilde yapılandırma |
| 4 | Federe çalışıyorsanız ve "insideCorporateNetwork" talebini kullanıyorsanız ve adlandırılmış konumlar tanımlanmamışsa | Risk olaylarının algılanmasını geliştirmek için adlandırılmış konumları tanımlayın |
| 5 | Adlandırılmış konumlar yerine MFA ile güvenilir IP adresleri kullanıyorsanız ve bunları güvenilir olarak işaretlemek için | Risk olaylarının algılanmasını geliştirmek için adlandırılmış konumları tanımlayın ve bunları güvenilir olarak işaretleyin |

### <a name="risk-based-access-policies"></a>Risk tabanlı erişim ilkeleri

Azure AD, her oturum açma ve Kullanıcı için riskleri hesaplayabilir. Erişim ilkelerinde bir ölçüt olarak riskin kullanılması daha iyi bir kullanıcı deneyimi sağlayabilir, örneğin, daha az kimlik doğrulama istemi ve daha iyi güvenlik, örneğin, yalnızca gerektiğinde kullanıcılara istem ve yanıt ve düzeltmeyi otomatikleştirin.

![Oturum açma riski ilkesi](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Erişim ilkelerindeki risk kullanımını destekleyen Azure AD Premium P2 lisanslarınız zaten varsa, ancak kullanılmazlar, güvenlik duruşunuzda risk eklenmesini kesinlikle öneririz.

#### <a name="risk-based-access-policies-recommended-reading"></a>Risk tabanlı erişim ilkelerinin okunması önerilir

- [Nasıl yapılır: oturum açma risk ilkesini yapılandırma](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Nasıl yapılır: Kullanıcı risk ilkesini yapılandırma](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>İstemci uygulama erişim ilkeleri

Microsoft Intune uygulama yönetimi (MAM), depolama şifrelemesi, PIN, uzak depolama temizleme vb. gibi veri koruma denetimlerini Outlook Mobile gibi uyumlu istemci mobil uygulamalarına iletme yeteneği sağlar. Ayrıca, onaylanan veya uyumlu uygulamalardan Exchange Online gibi bulut hizmetlerine [erişimi kısıtlamak](../conditional-access/app-based-conditional-access.md) için koşullu erişim ilkeleri oluşturulabilir.

Çalışanlarınız, Exchange Online veya SharePoint Online gibi şirket kaynaklarına erişmek için Office mobil uygulamaları gibi MAM özellikli uygulamalar yüklerse ve KCG 'yi (kendi cihazını getir) desteklemeniz durumunda, uygulama yapılandırmasını MDM kaydı olmadan kişisel cihazlarda yönetmek ve ardından koşullu erişim ilkelerinizi yalnızca MAM özellikli istemcilerden erişime izin verecek şekilde güncelleştirmeniz önerilir.

![Koşullu erişim Izni denetimi](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Çalışanların kurumsal kaynaklara karşı MAM özellikli uygulamalar yüklemesi ve erişimi Intune tarafından yönetilen cihazlarda kısıtlanması gerekir, bu durumda, kişisel cihazların uygulama yapılandırmasını yönetmek ve koşullu erişim ilkelerini yalnızca MAM özellikli istemcilerden erişime izin verecek şekilde güncelleştirmeniz gerekir.

### <a name="conditional-access-implementation"></a>Koşullu erişim uygulama

Koşullu erişim, kuruluşunuzun güvenlik duruşunu iyileştirmek için gereken önemli bir araçtır. Bu nedenle, bu en iyi yöntemleri izlemeniz önemlidir:

- Tüm SaaS uygulamalarının en az bir ilkenin uygulanmış olduğundan emin olun
- Kilitleme riskini önlemek için **tüm uygulamalar** filtresini **blok** denetimiyle birleştirmemeye özen gösterin
- **Tüm kullanıcıları** filtre olarak kullanmaktan kaçının ve yanlışlıkla **konukları** ekleyin
- **Tüm "eski" ilkeleri Azure portal geçirin**
- Kullanıcılar, cihazlar ve uygulamalar için tüm ölçütleri yakala
- **Kullanıcı BAŞıNA MFA** kullanmak yerine [MFA uygulamak](../conditional-access/plan-conditional-access.md)için koşullu erişim ilkelerini kullanın
- Birden çok uygulama için uygulanabilecek küçük bir çekirdek ilke kümesine sahiptir
- Boş özel durum gruplarını tanımlayın ve özel durum stratejisi sağlamak için bunları ilkelere ekleyin
- MFA denetimleri olmadan [kesme camı](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) hesaplarını planlayın
- Microsoft 365 istemci uygulamaları genelinde (takımlar, OneDrive, Outlook vb.) tutarlı bir deneyim sağlayın. Exchange Online ve SharePoint Online gibi hizmetler için aynı denetim kümesini uygulayarak
- İlkelere atama, bireyler değil, gruplar aracılığıyla uygulanmalıdır
- İlkelerde kullanılan özel durum gruplarını, kullanıcıların güvenlik sonrası çıkış süresini sınırlandırmak için düzenli olarak gözden geçirin. Azure AD P2 sahibiyseniz, işlemi otomatikleştirmek için erişim gözden geçirmeleri kullanabilirsiniz

#### <a name="conditional-access-recommended-reading"></a>Koşullu erişimin okunması önerilir

- [Azure Active Directory 'de koşullu erişim için en iyi yöntemler](../conditional-access/overview.md)
- [Kimlik ve cihaz erişim yapılandırmaları](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory Koşullu erişim ayarları başvurusu](../conditional-access/concept-conditional-access-conditions.md)
- [Ortak koşullu erişim ilkeleri](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>Erişim yüzeyi alanı

### <a name="legacy-authentication"></a>Eski kimlik doğrulaması

MFA gibi güçlü kimlik bilgileri, kötü amaçlı aktörler tarafından tercih edilen saldırı vektörünü yapan eski kimlik doğrulama protokollerini kullanarak uygulamaları koruyamaz. Eski kimlik doğrulamasının kilitlenmesi, erişim güvenlik duruşunu geliştirmek için önemlidir.

Eski kimlik doğrulaması, şunun gibi uygulamalar tarafından kullanılan kimlik doğrulama protokollerine başvuran bir terimdir:

- Modern kimlik doğrulaması kullanmayan eski Office istemcileri (örneğin, Office 2010 istemcisi)
- IMAP/SMTP/POP gibi posta protokollerini kullanan istemciler

Saldırganlar bu protokolleri kesinlikle tercih ediyor-aslında yaklaşık [%100 parola spreyi saldırıları](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) eski kimlik doğrulama protokollerini kullanır! Saldırganlar, çok faktörlü kimlik doğrulaması ve cihaz kimlik doğrulaması gibi ek güvenlik sorunları için gereken etkileşimli oturum açmayı desteklemediğinden, eski kimlik doğrulama protokollerini kullanır.

Ortamınızda eski kimlik doğrulama yaygın olarak kullanılıyorsa, eski istemcileri [modern kimlik doğrulamasını](/office365/enterprise/modern-auth-for-office-2013-and-2016) destekleyen istemcilere en kısa sürede geçirmeyi planlamalısınız. Aynı belirteçte, bazı kullanıcılarınız zaten modern kimlik doğrulaması kullanıyor, ancak diğerleri eski kimlik doğrulamasını kullanmaya devam ediyorsa, eski kimlik doğrulama istemcilerini kilitlemek için aşağıdaki adımları uygulamanız gerekir:

1. Hala eski kimlik doğrulama ve plan düzeltmesini kullanan kullanıcıları tanımlamak için [oturum açma etkinlik raporlarını](../reports-monitoring/concept-sign-ins.md) kullanın:

   a. Etkilenen kullanıcılara modern kimlik doğrulaması özellikli istemcileri yükseltin.
   
   b. Aşağıdaki adımları uygulamak için bir tam geçişi zaman çerçevesi planlayın.
   
   c. Eski uygulamaların eski kimlik doğrulaması üzerinde ne kadar eski bir bağımlılığı olduğunu belirler. Aşağıdaki 3. adıma bakın.

2. Daha fazla pozlamayı önlemek için eski kimlik doğrulaması kullanmayan kullanıcılar için kaynaktaki eski protokolleri devre dışı bırakın (Örneğin Exchange posta kutusu).
3. Kalan hesaplar için (hizmet hesapları gibi ideal insan dışı kimlikler), eski protokollerin kimlik doğrulama sonrası olduğunu [kısıtlamak için koşullu erişim](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) kullanın.

#### <a name="legacy-authentication-recommended-reading"></a>Eski kimlik doğrulaması önerilir

- [Exchange Server 'da posta kutularına POP3 veya ıMAP4 erişimini etkinleştirme veya devre dışı bırakma](/exchange/clients/pop3-and-imap4/configure-mailbox-access)

### <a name="consent-grants"></a>İzin verir

Bir ıllicıt onayı verme saldırısında, saldırgan, iletişim bilgileri, e-posta veya belgeler gibi verilere erişim isteyen bir Azure AD 'ye kayıtlı uygulama oluşturur. Kullanıcılar kötü amaçlı Web sitelerine giriş yaparken kimlik avı saldırıları aracılığıyla kötü amaçlı uygulamalara izin verebilir.

Aşağıda, Microsoft bulut hizmetleri için Scrutinize isteyebileceğiniz izinlere sahip uygulamaların bir listesi verilmiştir:

- Uygulama veya Temsilcili uygulamalar \* . ReadWrite Izinleri
- Temsilci izinleri olan uygulamalar Kullanıcı adına e-posta okuyabilir, gönderebilir veya yönetebilir
- Aşağıdaki izinleri kullanarak verilen uygulamalar:

| Kaynak | İzin |
| :- | :- |
| Exchange Online | Olduğundan. AccessAsUser. All |
| | EWS. AccessAsUser. All |
| | Mail. Read |
| Microsoft Graph API | Mail. Read |
| | Mail. Read. Shared |
| | Mail. ReadWrite |

- Uygulamalar, oturum açmış kullanıcının tam kullanıcı kimliğine bürünme izni verdi. Örnek:

|Kaynak | İzin |
| :- | :- |
| Microsoft Graph API| Directory. AccessAsUser. All |
| Azure REST API | user_impersonation |

Bu senaryoya engel olmak için, Office 365 ' de sahip olduğu tüm uygulamaları belirlemek ve düzeltmek için gerekli olandan daha fazla izin veren uygulamalar belirlemek ve düzeltmek üzere [Office ' de Illicit onay iznini Algıla ve](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) Düzelt bölümüne başvurmalısınız. Sonra [Self-Service ' i tamamen kaldırın](../manage-apps/configure-user-consent.md) ve [idare yordamları oluşturun](../manage-apps/configure-admin-consent-workflow.md). Son olarak, uygulama izinleri için düzenli İncelemeleri zamanlayın ve gerek duyulmadığında kaldırın.

#### <a name="consent-grants-recommended-reading"></a>Onay, önerilen okumaya izin veriyor

- [Microsoft Graph API izinleri](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Kullanıcı ve grup ayarları

Aşağıda açık bir işletme gereksinimi yoksa kilitlenebilen Kullanıcı ve grup ayarları verilmiştir:

#### <a name="user-settings"></a>Kullanıcı ayarları

- **Dış kullanıcılar** -dış Işbirliği, ekipler, Power BI, SharePoint Online ve Azure Information Protection gibi hizmetlerle kurumsal olarak şirket içinde gerçekleşebilir. Kullanıcı tarafından başlatılan dış işbirliğini denetlemek için açık kısıtlamalarınız varsa, [Azure AD yetkilendirme yönetimini](../governance/entitlement-management-overview.md) veya yardım masanıza gibi denetimli bir işlemi kullanarak dış kullanıcıları etkinleştirmeniz önerilir. Hizmetler için organik dış işbirliğine izin vermek istemiyorsanız, [üyelerin dış kullanıcıları tamamen davet](../external-identities/delegate-invitations.md)edebilir. Alternatif olarak, dış Kullanıcı davetlerinde [belirli etki alanlarına da izin verebilir veya onları engelleyebilirsiniz](../external-identities/allow-deny-list.md) .
- **Uygulama kayıtları** -uygulama kayıtları etkinleştirildiğinde, son kullanıcılar uygulamaları kendi kendilerine ekleyebilir ve verilerine erişim verebilir. Uygulama kaydının tipik bir örneği, Outlook eklentilerini veya Alexa ve Siri gibi sesli yardımcıların kendi e-postalarını ve takvimini okumasını ya da kendi adına e-posta göndermesini sağlayan kullanıcılardır. Müşteri, uygulama kaydını kapatmaya karar verirse, INFOSEC ve ıAM ekipleri, uygulamaları bir yönetici hesabıyla kaydetmesi gerektiği ve büyük olasılıkla işlemi gerçekleştirmek için bir işlem tasarlamaya gerek duydukları özel durumların yönetimine (iş gereksinimlerine göre gereken uygulama kayıtları) dahil olmalıdır.
- **Yönetim Portalı** -kuruluşlar Azure Portal Azure AD dikey penceresini kilitleyerek, yönetici olmayan Azure Portal Azure AD yönetimine erişemez ve karıştırılır. Erişimi kısıtlamak için Azure AD yönetim portalındaki Kullanıcı ayarlarına gidin:

![Yönetim Portalı kısıtlı erişim](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Yöneticiler olmayanlar, komut satırı ve diğer programlı arabirimler aracılığıyla Azure AD Yönetim arabirimlerine erişmeye devam edebilir.

#### <a name="group-settings"></a>Grup ayarları

**Self Servis Grup Yönetimi/kullanıcıları, güvenlik grupları/Microsoft 365 grupları oluşturabilir.** Bulutta gruplar için geçerli self servis girişimi yoksa, müşteriler bu özelliği kullanmaya hazırlanana kadar devre dışı bırakma kararı verebilir.

#### <a name="groups-recommended-reading"></a>Okumanız önerilen gruplar

- [Azure Active Directory B2B işbirliği nedir?](../external-identities/what-is-b2b.md)
- [Uygulamaları Azure Active Directory tümleştirme](../develop/quickstart-register-app.md)
- [Azure Active Directory uygulamalar, izinler ve onay.](../develop/quickstart-register-app.md)
- [Azure Active Directory içindeki kaynaklara erişimi yönetmek için grupları kullanma](./active-directory-manage-groups.md)
- [Azure Active Directory içinde self servis uygulama erişim yönetimini ayarlama](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>Beklenmeyen konumlardan gelen trafik

Saldırganlar dünyanın çeşitli parçalarından geliyor. Koşul olarak konum ile koşullu erişim ilkeleri kullanarak bu riski yönetin. Bir koşullu erişim ilkesinin [konum koşulu](../conditional-access/location-condition.md) , ' den oturum açmak için herhangi bir iş nedeni olmayan konumlara erişimi engellemenize olanak sağlar.

![Yeni bir adlandırılmış konum oluştur](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Varsa, bölgeler arasında erişim düzenlerini çözümlemek ve bulmak için bir güvenlik bilgileri ve olay yönetimi (SıEM) çözümü kullanın. SıEM ürünü kullanmıyorsanız veya Azure AD 'den kimlik doğrulama bilgilerini geri almaya memişse, bölgeler arasında erişim düzenlerini belirlemek için [Azure izleyici](../../azure-monitor/overview.md) kullanmanızı öneririz.

## <a name="access-usage"></a>Erişim kullanımı

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD günlükleri arşivlenmiş ve olay yanıt planlarıyla tümleşiktir

Oturum açma etkinliğine erişim, Azure AD için denetimler ve risk olayları, sorun giderme, Kullanım Analizi ve adli araştırmalar açısından önemlidir. Azure AD, sınırlı saklama süresine sahip REST API 'Leri aracılığıyla bu kaynaklara erişim sağlar. Bir güvenlik bilgileri ve olay yönetimi (SıEM) sistemi veya eşdeğer arşivleme teknolojisi, denetim ve desteklenebilirlik için uzun süreli depolamaya yönelik bir anahtardır. Azure AD günlüklerinin uzun süreli depolanmasını etkinleştirmek için bunları mevcut SıEM çözümünüze eklemeniz veya [Azure izleyici](../reports-monitoring/concept-activity-logs-azure-monitor.md)'yi kullanmanız gerekir. Olay yanıt planlarınızın ve araştırmalarınızın bir parçası olarak kullanılabilen arşiv günlükleri.

#### <a name="logs-recommended-reading"></a>Günlük okuma önerilir

- [Azure Active Directory Denetim API 'SI başvurusu](/graph/api/resources/directoryaudit)
- [Azure Active Directory oturum açma etkinliği rapor API 'SI başvurusu](/graph/api/resources/signin)
- [Sertifikalarla Azure AD Raporlama API’sini kullanarak veri alma](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Azure Active Directory Kimlik Koruması için Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)
- [Office 365 yönetimi etkinlik API 'SI başvurusu](/office/office-365-management-api/office-365-management-activity-api-reference)
- [Azure Active Directory Power BI İçerik Paketi'ni kullanma](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Özet

Güvenli bir kimlik altyapısının 12 yönü vardır. Bu liste, kimlik bilgilerini daha güvenli ve yönetebilir, kimlik doğrulama deneyimini tanımlamanızı, atama atamasını, kullanım ölçümünü ve kurumsal güvenlik duruşunu temel alan erişim ilkeleri tanımlamanıza yardımcı olur.

- Anahtar görevlere sahip atayın.
- Zayıf veya sızdırılan parolaları algılama, parola yönetimini ve korumayı geliştirme ve kaynaklara daha fazla güvenli Kullanıcı erişimi sağlamak için çözümler uygulayın.
- Kaynaklarınızı istediğiniz zaman ve herhangi bir konumdan korumak için cihazların kimliğini yönetin.
- Passwordless kimlik doğrulaması uygulayın.
- Kuruluş genelinde standartlaştırılmış bir çoklu oturum açma mekanizması sağlayın.
- Daha iyi güvenlik ve daha tutarlı yönetilebilirlik sağlamak için AD FS uygulamaları Azure AD 'ye geçirin.
- Daha fazla esneklik ve ölçeğe göre yönetme olanağı sağlamak için grupları kullanarak uygulamalara Kullanıcı atama.
- Risk tabanlı erişim ilkelerini yapılandırın.
- Eski kimlik doğrulama protokollerini kilitle.
- Illicıt onayını tespit edin ve düzeltin.
- Kullanıcı ve grup ayarlarını kilitle.
- Sorun giderme, Kullanım Analizi ve Forli ICS araştırmaları için Azure AD günlüklerinin uzun süreli depolanmasını etkinleştirin.

## <a name="next-steps"></a>Sonraki adımlar

[Kimlik idare işlem denetimleri ve eylemleri](active-directory-ops-guide-govern.md)ile çalışmaya başlayın.
