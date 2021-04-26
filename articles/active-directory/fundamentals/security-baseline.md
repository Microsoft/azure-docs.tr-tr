---
title: Azure Active Directory için Azure Güvenlik temeli
description: Azure Active Directory güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: active-directory
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8d4203b7b5d7742bf198778864fa4f42b7423596
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107286026"
---
# <a name="azure-security-baseline-for-azure-active-directory"></a>Azure Active Directory için Azure Güvenlik temeli

Bu güvenlik temeli, Azure Active Directory için [Azure Güvenlik kıyaslama sürüm 2,0](../../security/benchmarks/overview.md) ' den rehberlik uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure Active Directory için geçerli olan ilgili kılavuza göre gruplandırılır. 

> [!NOTE]
> Azure Active Directory için geçerli olmayan **denetimler** veya Microsoft 'un sorumluluğunun bu taban çizgisinden hariç tutulması. Azure Active Directory Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Azure Active Directory güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: ağ güvenlik kurallarını basitleştirme

**Rehberlik**: ağ güvenlik gruplarında veya Azure Active Directory kaynaklarınız Için yapılandırılmış Azure Güvenlik duvarında ağ erişim denetimleri tanımlamak Için Azure sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. Bir kuralın uygun kaynak veya hedef alanındaki ' AzureActiveDirectory ' gibi hizmet etiketi adını belirterek, ilgili hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir. 

- [Hizmet etiketlerini anlama ve kullanma](../../virtual-network/service-tags-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-management"></a>Kimlik Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Kimlik Yönetimi](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory'yi standart merkezi kimlik ve kimlik doğrulaması sistemi haline getirin

**Rehberlik**: varsayılan kimlik ve erişim yönetimi hizmeti olarak Azure Active Directory (Azure AD) kullanın. Kuruluşunuzun kimlik ve erişim yönetimini yönetmek için Azure AD 'yi standartlaşmanız gerekir: 
- Azure portal, Azure depolama, Azure sanal makinesi (Linux ve Windows), Azure Key Vault, PaaS ve SaaS uygulamaları gibi Microsoft Bulut kaynaklar. 
- Kuruluşunuzun kaynakları. Örneğin Azure’daki uygulamalar veya kurumsal ağ kaynaklarınız. 
 

Azure AD'nin güvenliğini sağlamak, kuruluşunuzun güvenlik uygulamalarının yüksek öncelikli bileşenlerinden biri olmalıdır. Azure AD, kimlik güvenliği duruşunu Microsoft'un en iyi deneyim önerilerine göre değerlendirmenize yardımcı olmak için bir kimlik güvenliği puanı sağlar. Bu puanı kullanarak yapılandırmanızın en iyi deneyim önerileriyle ne kadar uyumlu olduğunu görebilir ve güvenlik duruşunuzda geliştirmeler yapabilirsiniz. 

Azure AD, Microsoft hesabı olmayan kullanıcıların kendi uygulamalarına ve kaynaklarına kendi dış kimlik bilgileriyle oturum açmasına olanak tanıyan dış kimliği destekler. 

- [Azure Active Directory'deki kiracılar](../develop/single-and-multi-tenant-apps.md)

- [Azure AD örneği oluşturma ve yapılandırma](active-directory-access-create-new-tenant.md)

- [Uygulama için dış kimlik sağlayıcılarını kullanma](/azure/active-directory/b2b/identity-providers)

- [Azure Active Directory'deki kimlik güvenliği puanı nedir?](identity-secure-score.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Uygulama kimliklerini güvenli ve otomatik bir şekilde yönetin

**Kılavuz**: hizmetler veya Otomasyon gibi insan dışı hesaplar Için Azure kaynakları Için yönetilen kimlik kullanın, kaynaklarınıza erişmek veya onları yürütmek üzere daha güçlü bir insan hesabı oluşturmak yerine Azure tarafından yönetilen kimlik özelliği kullanılması önerilir. Kaynak kodunda veya yapılandırma dosyalarında kimlik bilgisi sabit kodlanmış olarak önceden tanımlı erişim verme kuralıyla Azure Active Directory (Azure AD) kimlik doğrulamasını destekleyen Azure hizmetleri/kaynaklarında yerel olarak kimlik doğrulaması yapabilirsiniz. Azure yönetilen kimliklerini Azure AD kaynaklarına atayamazsınız ancak Azure AD, diğer hizmetin kaynaklarına atanmış Yönetilen kimlikler ile kimlik doğrulama mekanizmasıdır.

- [Azure kaynakları için yönetilen kimlik](../managed-identities-azure-resources/overview.md)

- [Azure kaynakları için yönetilen kimliği destekleyen hizmetler](../managed-identities-azure-resources/services-support-managed-identities.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Uygulama erişimi için Azure AD çoklu oturum açma (SSO) özelliğini kullanın

**Rehberlik**: Azure kaynaklarına, bulut uygulamalarına ve şirket içi uygulamalara kimlik ve erişim yönetimi sağlamak için Azure Active Directory kullanın. Buna çalışanlar gibi kuruluş kimliklerinin yanı sıra iş ortakları, satıcılar ve tedarikçiler gibi dış kimlikler de dahildir. Bu seçenek, kuruluşunuzun şirket içi ortamda ve bulutta bulunan verilerini ve kaynaklarını yönetip bunlara güvenli erişim sağlamak için çoklu oturum açma (SSO) özelliğinin kullanılmasını sağlar. Tüm kullanıcılarınızı, uygulamalarınızı ve cihazlarınızı Azure AD'ye bağlayarak sorunsuz ve güvenli erişimin yanı sıra daha fazla görünürlük ve denetim elde edebilirsiniz.

 
- [Azure AD ile uygulamalar için SSO yaklaşımını anlama](../manage-apps/what-is-single-sign-on.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Tüm Azure Active Directory tabanlı erişim girişimleri için güçlü kimlik doğrulaması denetimleri kullanın

**Rehberlik**: Multi-Factor AUTHENTICATION (MFA) ve güçlü parolasız yöntemler aracılığıyla güçlü kimlik doğrulama denetimlerini desteklemek için Azure Active Directory kullanın.
- Çok faktörlü kimlik doğrulaması: Azure AD çok faktörlü kimlik doğrulaması özelliğini etkinleştirin ve çok faktörlü kimlik doğrulaması ayarlarınızla ilgili en iyi deneyimlere yönelik Azure Güvenlik Merkezi Kimlik ve Erişim Yönetimi önerilerini izleyin. Çok faktörlü kimlik doğrulaması tüm kullanıcılara, yalnızca belirli kullanıcılara veya oturum açma koşullarıyla risk faktörlerine göre kullanıcı düzeyinde uygulanabilir.
- Passwordless kimlik doğrulaması-üç adet passwordless kimlik doğrulama seçeneği mevcuttur: Iş için Windows Hello, Microsoft Authenticator uygulaması ve akıllı kartlar gibi şirket içi kimlik doğrulama yöntemleri.

Yönetici ve ayrıcalıklı kullanıcılar için, güçlü kimlik doğrulama yönteminin en yüksek düzeyinin kullanıldığından emin olun ve ardından uygun güçlü kimlik doğrulama ilkesini diğer kullanıcılara teslim edin.

Azure AD, şirket içi parola ilkelerini izleyecek bir temel parola ilkesi veya hibrit hesapları (Şirket içi Active Directory gelen Kullanıcı hesapları) olan, yalnızca bulut hesapları (doğrudan Azure AD 'de oluşturulan kullanıcı hesapları) gibi eski parola tabanlı kimlik doğrulamasını destekler. Azure AD, parola tabanlı kimlik doğrulaması kullanırken kullanıcıların tahmin edilmesi kolay parolalar ayarlamamasını engelleyen bir parola koruma yeteneği sağlar. Microsoft, telemetri temelinde güncellenen yasaklanmış parolaların küresel bir listesini sağlar ve müşteriler bu listeyi gereksinimlerine göre artırabilir (örn. marka, kültürel başvuruları, vb.). Bu parola koruması, yalnızca bulut ve karma hesaplar için kullanılabilir.

Yalnızca parola kimlik bilgilerine dayalı kimlik doğrulaması popüler saldırı yöntemlerine açıktır. Daha yüksek güvenlik için MFA ve güçlü bir parola ilkesi gibi güçlü kimlik doğrulaması kullanın. Varsayılan parolalara sahip olabilecek üçüncü taraf uygulamalar ve Market Hizmetleri için, bunları hizmet ilk kurulumunda değiştirmelisiniz.

 
- [Azure AD MFA 'yı dağıtma](../authentication/howto-mfa-getstarted.md) 

 

 
- [Azure Active Directory için parolasız kimlik doğrulaması seçeneklerine giriş](../authentication/concept-authentication-passwordless.md) 

 

 
- [Azure AD varsayılan parola ilkesi](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#password-policies-that-only-apply-to-cloud-user-accounts)

- [Azure AD Parola Koruması kullanarak hatalı parolaları ortadan kaldırma](../authentication/concept-password-ban-bad.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Hesap anomalilerini izleyin ve uyarı oluşturun

**Rehberlik**: Azure Active Directory aşağıdaki veri kaynaklarını sağlar:

 
- Oturum açma bilgileri - Oturum açma bilgileri raporu yönetilen uygulamaların kullanımı ve kullanıcıların oturum açma etkinlikleri hakkında bilgi sağlar.

 
- Denetim günlükleri - Azure AD içindeki çeşitli özellikler tarafından yapılan tüm değişiklikler için günlükler aracılığıyla izlenebilirlik sağlar. Azure AD içindeki herhangi bir kaynakta yapılan kullanıcı, uygulama, grup, rol ve ilkeleri ekleme veya kaldırma işlemleri gibi değişiklikler, denetim günlüklerine örnek gösterilebilir.

 
- Riskli oturum açma işlemleri - Riskli oturum açma işlemi bir kullanıcı hesabının meşru sahibi olmayan bir kişi tarafından gerçekleştirilmiş olabilecek oturum açma girişiminin göstergesidir.

 
- Riskli oldukları belirlenen kullanıcılar - Riskli kullanıcı, güvenliği tehlikeye girmiş olabilecek bir kullanıcı hesabının göstergesidir.

 

Bu veri kaynakları Azure Izleyici, Azure Sentinel veya üçüncü taraf SıEM sistemleriyle tümleştirilebilir.

 

 
Azure Güvenlik Merkezi aşırı fazla sayıda başarısız kimlik doğrulaması girişimi, abonelikte kullanım dışı kalan hesaplar gibi bazı şüpheli etkinliklerde de uyarı verebilir.

 

 
Azure Gelişmiş Tehdit Koruması (ATP) gelişmiş tehditleri, risk altındaki kimlikleri ve içeriden kötü amaçlı eylemleri belirlemek, algılamak ve araştırmak için Active Directory sinyallerini kullanabilen bir güvenlik çözümüdür.

 

 
- [Azure Active Directory’de denetim etkinliği raporları](../reports-monitoring/concept-audit-logs.md) 

 

 
- [Azure AD riskli oturum açma işlemlerini görüntüleme](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

 

 
- [Riskli etkinlik bayrağıyla işaretlenen Azure AD kullanıcılarını belirleme](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

 

 
- [Azure Güvenlik Merkezi’nde kullanıcıların kimliğini ve erişim etkinliğini izleme](../../security-center/security-center-identity-access.md) 

 

 
- [Azure Güvenlik Merkezi’nin tehdit analizi koruma modülündeki uyarılar](../../security-center/alerts-reference.md) 

 

 
- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Azure kaynaklarına erişimi koşullara göre kısıtlayın

**Rehberlik**: belirli IP aralıklarından gelen kullanıcı oturumlarının oturum açması için Kullanıcı tanımlı koşullara göre daha ayrıntılı erişim denetimi için Azure Active Directory (Azure AD) koşullu erişimi kullanın. Ayrıntılı kimlik doğrulaması oturumu yönetim ilkesi farklı kullanım örneklerinde de kullanılabilir.

 
- [Azure AD koşullu erişim genel bakış](../conditional-access/overview.md) 

 

 
- [Ortak koşullu erişim ilkeleri](../conditional-access/concept-conditional-access-policy-common.md) 

 

 
- [Erişim Denetimi ile kimlik doğrulama oturum yönetimini yapılandırma](../conditional-access/howto-conditional-access-session-lifetime.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="im-8-secure-user-access-to-legacy-applications"></a>Im-8: eski uygulamalara Kullanıcı erişimini güvenli hale getirme

**Rehberlik**: eski uygulamalar için modern erişim denetimlerine ve oturum izlemeye sahip olduğunuzdan ve bunların depolandığı ve işledikleri verilere sahip olduğunuzdan emin olun. VPN 'Ler, eski uygulamalara erişmek için yaygın olarak kullanıldığında, genellikle yalnızca temel erişim denetimi ve sınırlı oturum izleme olur.

 
Azure AD Uygulama Ara Sunucusu, Azure AD koşullu erişim ile hem uzak kullanıcıların hem de cihazların güvenilirliğini açıkça doğrularken, eski şirket içi uygulamaları SSO ile uzak kullanıcılara yayımlamanıza olanak sağlar.

 

 
Alternatif olarak, Microsoft Cloud App Security, kullanıcının uygulama oturumlarını ve engelleme eylemlerini (Şirket içi uygulamalar ve bulut yazılım hizmeti (SaaS) uygulamaları için) izlemeye yönelik denetimler sağlayabilen bir bulut erişim güvenlik Aracısı (CASB) hizmetidir.

 

 
- [Azure Active Directory Uygulama Ara Sunucusu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#what-is-application-proxy) 

 

 
- [En iyi Microsoft Cloud App Security uygulamalar](/cloud-app-security/best-practices)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="privileged-access"></a>Ayrıcalıklı Erişim

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ayrıcalıklı Erişim](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Yüksek ayrıcalıklı kullanıcıları koruyun ve sınırlayın

**Rehberlik**: Bu iki role atanan kullanıcılar yönetici rollerine temsilci olarak atanabileceği için, en kritik yerleşik roller Azure AD 'Nin genel yönetici ve ayrıcalıklı rol yöneticisidir.

- Genel yönetici: Bu role sahip olan kullanıcılar, Azure AD 'deki tüm yönetim özelliklerine, ayrıca Azure AD kimlikleri kullanan hizmetlere erişebilir.

- Ayrıcalıklı rol yöneticisi: Bu role sahip olan kullanıcılar, Azure AD 'de rol atamalarını ve Azure AD Privileged Identity Management (PıM) içinde yönetebilir. Ayrıca, bu rol, PıM ve yönetim birimlerinin tüm yönlerinin yönetimine izin verir.

Belirli ayrıcalıklı izinlerle atanmış özel roller kullanıyorsanız, yönetilmelidir başka kritik rolleriniz olabilir. Ayrıca, önemli iş varlıklarının yönetici hesabına de benzer denetimler uygulamak isteyebilirsiniz.

Azure AD, yüksek ayrıcalıklı hesaplara sahiptir: doğrudan veya dolaylı olarak atanmış olan veya genel yönetici ya da ayrıcalıklı rol yöneticisi rollerine ve Azure AD ve Azure 'daki diğer yüksek ayrıcalıklı rollere yönelik olan veya uygun olan kullanıcılar ve hizmet sorumluları.

Bu ayrıcalığa sahip olan kullanıcılar Azure ortamınızdaki her kaynağı doğrudan veya dolaylı olarak okuyup değiştirebildiğinden, yüksek ayrıcalıklı hesapların sayısını sınırlayın ve bu hesapları yükseltilmiş bir düzeyde koruyun.

Azure AD Privileged Identity Management’ı (PIM) kullanarak Azure kaynaklarına ve Azure AD’ye tam zamanında (JIT) ayrıcalıklı erişimi etkinleştirebilirsiniz. JIT yalnızca kullanıcıların ihtiyacı olduğunda ayrıcalıklı görevler gerçekleştirmeleri için geçici izinler verir. Azure AD kuruluşunuzda güvenli olmayan veya şüpheli etkinlikler olduğunda da PIM güvenlik uyarıları oluşturabilir.

- [Azure AD'de yönetici rolü izinleri](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [Azure Privileged Identity Management güvenlik uyarılarını kullanma](../privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Azure AD'de karma ve bulut dağıtımları için ayrıcalıklı erişim güvenliğini sağlama](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: İş açısından kritik sistemlere yönetici erişimini kısıtlayın

**Rehberlik**: iş açısından kritik sistemlere yönetici erişimini kısıtlamak için Azure Active Directory Privileged Identity Management ve Multi-Factor Authentication kullanın.

- [Rol etkinleştirme isteklerinin onayını Privileged Identity Management](../privileged-identity-management/azure-ad-pim-approval-workflow.md)

- [Multi-Factor Authentication ve koşullu erişim](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Kullanıcı erişimini düzenli olarak gözden geçirin ve mutabık kılın

**Rehberlik**: hesapların ve erişiminin geçerli olduğundan emin olmak için Kullanıcı hesabı erişim atamalarını düzenli olarak gözden geçirin, özellikle de genel yönetici ve ayrıcalıklı rol yöneticisi dahil olmak üzere yüksek ayrıcalıklı rollere odaklanılmıştır. Azure AD rolleri ve Azure rolleri için grup üyeliklerini, kurumsal uygulamalara erişimi ve rol atamalarını gözden geçirmek üzere Azure Active Directory (Azure AD) erişim gözden geçirmeleri kullanabilirsiniz. Azure AD raporlama, eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlayabilir. Gözden geçirme sürecini kolaylaştırmak için, erişim gözden geçirme raporu iş akışı oluşturmak için Azure AD Privileged Identity Management de kullanabilirsiniz.

Ayrıca, Azure Privileged Identity Management, çok fazla sayıda yönetici hesabı oluşturulduğunda ve eski veya yanlış yapılandırılmış yönetici hesaplarını belirlemek için de yapılandırılabilir.

- [Privileged Identity Management (PıM) içinde Azure AD rolleri için erişim gözden geçirmesi oluşturma](../privileged-identity-management/pim-how-to-start-security-review.md)

- [Privileged Identity Management (PıM) içinde Azure Kaynak rolleri için erişim gözden geçirmesi oluşturma](../privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Azure AD kimlik ve erişim gözden geçirmelerini kullanma](../governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Azure AD'de acil durum erişimini ayarlayın

**Kılavuz**: Azure AD kuruluşunuzun yanlışlıkla kilitlenmesini engellemek için, normal yönetim hesapları kullanılmazsa erişim için bir acil durum erişim hesabı ayarlayın. Acil durum erişim hesapları genellikle yüksek ayrıcalığa sahiptir ve herhangi bir kişiye atanmamalıdır.
Acil durum erişim hesaplarının kullanım alanı, normal yönetici hesaplarının kullanılamadığı acil veya "camı kırın" senaryolarıyla sınırlıdır.

Acil durum erişim hesaplarının kimlik bilgilerinin (parola, sertifika veya akıllı kart) güvenli bir şekilde saklandığından ve yalnızca acil bir durumda bunları kullanma yetkisine sahip olan kullanıcılar tarafından bilindiğinden emin olmanız gerekir.

- [Azure AD'deki acil durum erişim hesaplarını yönetme](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pa-5-automate-entitlement-management"></a>PA-5: yetkilendirme yönetimini otomatikleştirme 

**Rehberlik**: erişim atamaları, incelemeler ve süre sonu dahil olmak üzere erişim isteği iş akışlarını otomatikleştirmek IÇIN Azure AD yetkilendirme yönetimi özelliklerini kullanın. Çift veya çok aşamalı onay de desteklenir.

- [Azure AD yetkilendirme yönetimi nedir?](../governance/entitlement-management-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Ayrıcalıklı erişim iş istasyonlarını kullanın

**Rehberlik**: Güvenli ve yalıtılmış iş istasyonları yöneticiler, geliştiriciler ve kritik hizmet operatörleri gibi hassas rollerin güvenliği açısından kritik öneme sahiptir. Yönetim görevleri için yüksek düzeyde güvenli Kullanıcı iş istasyonları ve/veya Azure savunma kullanın. Yönetim görevlerine yönelik güvenli ve yönetilen bir kullanıcı iş istasyonu dağıtmak için Azure Active Directory, Microsoft Defender Gelişmiş Tehdit Koruması (ATP) ve/veya Microsoft Intune hizmetlerini kullanın. Güvenli iş istasyonları güçlü kimlik doğrulaması, yazılım ve donanım temelleri, kısıtlı mantıksal erişim ve ağ erişimi gibi güvenli yapılandırma özelliklerinin uygulanması için merkezi olarak yönetilebilir.

- [Ayrıcalıklı erişimin bir parçası olarak cihazların güvenliğini sağlama](/security/compass/privileged-access-devices)

- [Ayrıcalıklı erişim uygulaması](/security/compass/privileged-access-deployment)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>AE-7: Tam yetecek kadar yönetim uygulama (en düşük ayrıcalık ilkesi) 

**Rehberlik**: müşteriler, kullanıcıların yönetim görevlerini tamamlaması için gereken en düşük izinlere sahip rollere atayarak, en az ayrıcalık için Azure Active Directory (Azure AD) dağıtımını yapılandırabilir.

- [Azure AD'de yönetici rolü izinleri](../roles/permissions-reference.md)

- [Azure AD 'de yönetici rolleri atama](../roles/manage-roles-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Microsoft desteği için onay işlemini seçin  

**Rehberlik**: Azure Active Directory müşteri kasayı desteklemez. Microsoft, müşteri verilerine erişim onayı için kasa dışı yöntemler aracılığıyla müşterilerle çalışabilir.

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Hassas verileri koruyun

**Rehberlik**: hassas verilerinizin korunmasını uygulamak için aşağıdaki kılavuzu göz önünde bulundurun:

- **Yalıtım:** Dizin, Azure Active Directory (Azure AD) Hizmetleri tarafından bir müşterinin verilerini depolayan ve işleyen veri sınırıdır. Müşteriler, kendi dizinlerinden ülke özelliğini ayarlayarak Azure AD müşteri verilerinin çoğunun nerede bulunmasını istediğinizi belirlemelidir.

- **Segmentleme:** Genel yönetici rolü tüm dizin verilerinin tam denetimine ve erişim ve işleme talimatlarını yöneten kurallara sahiptir. Bir dizin, yönetim birimlerine ayrılabilir ve bu birimlerin yöneticileri tarafından yönetilmek üzere kullanıcılar ve gruplarla sağlanabilir. Genel Yöneticiler, daha önceden tanımlanmış rollere veya oluşturdukları özel rollere atayarak, kuruluştaki diğer ilkelere sorumluluğa yetki verebilir.

 
- **Erişim:** İzinler, bir Kullanıcı, Grup, rol, uygulama veya cihaza uygulanabilir. Atama, Privileged Identity Management yapılandırma başına kalıcı veya zamana bağlı olabilir. 
  
- **Şifreleme:** Azure AD, bekleyen veya yoldaki tüm verileri şifreler. Teklif, müşterilerin dizin verilerini kendi şifreleme anahtarıyla şifrelemesine izin vermez. 

Seçili ülkelerin dizinlerinin fiziksel konumuyla nasıl eşlendiğini öğrenmek için, ' nerede veri bulunduğu makalesi ' bölümüne bakın.

- [Verilerinizin bulunduğu Makale](https://www.microsoft.com/trust-center/privacy/data-location)

Müşteri, dizinlerle etkileşime geçen çeşitli Azure AD araçları, özellikleri ve uygulamaları kullandığından, Azure Active Directory – verileriniz nerede bulunur? makalesini kullanın.

- [Verilerin bulunduğu Pano](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)

- [Azure AD rolleriyle ilgili belgeler](/azure/active-directory/roles/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Hassas bilgileri aktarım sırasında şifreleyin

**Rehberlik**: erişim denetimlerini tamamlamak için, yoldaki veriler, saldırganların verileri kolayca okuyamadığından veya değiştiremeyeceği için şifreleme kullanılarak ' bant dışı ' saldırılarına (ör. trafik yakalama) karşı korunmalıdır.

Azure AD, TLS v 1.2 veya üzerini kullanarak geçişte veri şifrelemesini destekler.

Bu, özel ağlardaki trafik için isteğe bağlı olsa da, bu, dış ve genel ağlardaki trafik için önemlidir. HTTP trafiği için, Azure kaynaklarınıza bağlanan istemcilerin TLS v 1.2 veya üzerini anlaştığınızdan emin olun. Uzaktan Yönetim için şifrelenmemiş bir protokol yerine SSH (Linux için) veya RDP/TLS (Windows için) kullanın. Kullanımdan çıkarıldı SSL, TLS ve SSH sürümleri ve protokolleri ve zayıf şifrelemeler devre dışı bırakılmalıdır.

Azure, varsayılan olarak Azure veri merkezleri arasında aktarım halindeki veriler için şifreleme sağlar.

- [Azure ile iletim sırasında şifrelemeyi anlama](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit) 

- [TLS güvenliği hakkında bilgi](/security/engineering/solving-tls1-problem) 

- [Yoldaki Azure verileri için çift şifreleme](https://docs.microsoft.com/azure/security/fundamentals/double-encryption#data-in-transit)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="asset-management"></a>Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Varlık Yönetimi](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>VY-1: Güvenlik ekibinin varlıklara yönelik risklerde görünürlük elde etmesini sağlama

**Rehber**: Güvenlik ekiplerinin Azure Güvenlik Merkezi’ni kullanarak güvenlik risklerini izlemesini sağlamak için bu ekiplere Azure kiracınız ve abonelikleriniz üzerinde Güvenlik Okuyucusu izinleri verildiğinden emin olun. 

Güvenlik ekibi sorumluluklarının yapılandırmasına bağlı olarak, güvenlik risklerinin izlenmesi merkezi güvenlik ekibinin veya yerel bir ekibin sorumluluğunda olabilir. Gerçi güvenlik içgörüleri ve risklerinin kuruluş içinde her zaman merkezi bir konumda toplanması gerekir. 

Güvenlik Okuyucusu izinleri kiracının tamamına (Kök Yönetim Grubu) geniş kapsamlı olarak uygulanabileceği gibi, izinlerin kapsamı yönetim gruplarıyla veya belirli aboneliklerle de sınırlanabilir. 

İş yüklerinin ve hizmetlerin görünürlüğünü almak için ek izinler gerekebilir. 

- [Güvenlik Okuyucusu Rolüne Genel Bakış](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Azure Yönetim Gruplarına Genel Bakış](../../governance/management-groups/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>ÖÖ-5: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" yapılandırarak, kullanıcıların Azure Azure Portal ad ile etkileşim kurmasına yönelik Azure Active Directory (Azure AD) koşullu erişim özelliğini kullanın.

- [Azure Kaynak Yöneticisi 'ne erişimi engellemek için koşullu erişimi yapılandırma](../../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-threat-detection"></a>Günlüğe Kaydetme ve Tehdit Algılama

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Günlüğe Kaydetme ve Tehdit Algılama](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure kaynakları için tehdit algılamayı etkinleştirme

**Kılavuz**: Azure AD kaynaklarınız için Azure Active Directory (Azure AD) kimlik koruması yerleşik tehdit algılama özelliğini kullanın. 
 
 
 

 
Azure AD, tehdit algılama ve tehdit arama için sık kullanılan etkinlik günlükleri oluşturur. Azure AD oturum açma günlükleri, kullanıcılar, hizmetler ve uygulamalar için kimlik doğrulama ve yetkilendirme etkinliğinin bir kaydını sağlar. Azure AD denetim günlükleri, güvenlik duruşunu geliştiren veya azalan değişiklikler dahil olmak üzere bir Azure AD kiracısında yapılan değişiklikleri izler.

- [Azure Active Directory Kimlik Koruması nedir?](../identity-protection/overview-identity-protection.md)

- [Azure AD Kimlik Koruması verilerini Azure Sentinel 'e bağlama](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Active Directory verilerini Azure Sentinel 'e bağlama](../../sentinel/connect-azure-active-directory.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure kimlik ve erişim yönetimi için tehdit algılamayı etkinleştirin

**Rehberlik**: Azure Active Directory (Azure AD), daha ayrıntılı izleme ve analiz kullanım örnekleri IÇIN Azure AD raporlama 'da görüntülenebilen veya Azure Izleyici, Azure Sentinel veya diğer SIEM/izleme araçlarıyla tümleştirilen aşağıdaki kullanıcı günlüklerini sağlar: 
- Oturum açma bilgileri – Oturum açma bilgileri raporu, yönetilen uygulamaların kullanımı ve kullanıcı oturum açma etkinlikleri hakkında bilgi sağlar. 
- Denetim günlükleri - Azure AD içindeki çeşitli özellikler tarafından yapılan tüm değişiklikler için günlükler aracılığıyla izlenebilirlik sağlar. Azure AD içindeki herhangi bir kaynakta yapılan kullanıcı, uygulama, grup, rol ve ilkeleri ekleme veya kaldırma işlemleri gibi değişiklikler, denetim günlüklerine örnek gösterilebilir. 
- Riskli oturum açma işlemleri - Riskli oturum açma işlemi bir kullanıcı hesabının meşru sahibi olmayan bir kişi tarafından gerçekleştirilmiş olabilecek oturum açma girişiminin göstergesidir. 
- Riskli kullanıcılar-riskli bir Kullanıcı, tehlikeye girmiş olabilecek bir kullanıcı hesabı için bir göstergedir. 

Kimlik koruması risk algılamaları varsayılan olarak etkindir ve ekleme işlemi gerektirmez. Ayrıntı düzeyi veya risk verileri, lisans SKU 'SU tarafından belirlenir. 

- [Azure Active Directory’de denetim etkinliği raporları](../reports-monitoring/concept-audit-logs.md)  

- [Azure Kimlik Koruması’nı etkinleştirme](../identity-protection/overview-identity-protection.md)  

- [Azure Güvenlik Merkezi’nde tehdit koruması](/azure/security-center/threat-protection)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure kaynakları için günlüğe kaydetmeyi etkinleştirin

**Rehberlik**: Azure Active Directory (Azure AD) etkinlik günlükleri oluşturur. Azure AD bazı Azure hizmetlerinden farklı olarak, etkinlik ve kaynak günlükleri arasında ayrım yapmaz. Etkinlik günlükleri Azure portal Azure AD bölümünde otomatik olarak kullanılabilir ve Azure Izleyici, Azure Event Hubs, Azure depolama, Sıems ve diğer konumlara aktarılabilir.
 
- Oturum açma bilgileri – Oturum açma bilgileri raporu, yönetilen uygulamaların kullanımı ve kullanıcı oturum açma etkinlikleri hakkında bilgi sağlar.  
 
- Denetim günlükleri - Azure AD içindeki çeşitli özellikler tarafından yapılan tüm değişiklikler için günlükler aracılığıyla izlenebilirlik sağlar. Azure AD içindeki herhangi bir kaynakta yapılan kullanıcı, uygulama, grup, rol ve ilkeleri ekleme veya kaldırma işlemleri gibi değişiklikler, denetim günlüklerine örnek gösterilebilir.  
 
 

Azure AD Ayrıca daha ayrıntılı izleme ve analiz kullanım örnekleri için Azure AD raporlama 'da görüntülenebilecek veya Azure Izleyici, Azure Sentinel veya diğer SıEM/izleme araçlarıyla tümleştirilen güvenlikle ilgili Günlükler sağlar: 
- Riskli oturum açma işlemleri - Riskli oturum açma işlemi bir kullanıcı hesabının meşru sahibi olmayan bir kişi tarafından gerçekleştirilmiş olabilecek oturum açma girişiminin göstergesidir.
- Riskli kullanıcılar-riskli bir Kullanıcı, tehlikeye girmiş olabilecek bir kullanıcı hesabı için bir göstergedir. 

Kimlik koruması risk algılamaları varsayılan olarak etkindir ve ekleme işlemi gerektirmez. Ayrıntı düzeyi veya risk verileri, lisans SKU 'SU tarafından belirlenir. 

 
- [Azure Active Directory etkinlik ve güvenlik raporları](../reports-monitoring/overview-reports.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Güvenlik günlüğü yönetim ve analiz süreçlerini merkezileştirin

**Rehberlik**: müşteriler daha kolay tehdit ve güvenlik sonrası analizine yönelik güvenlik günlüklerini merkezileştirmek istediğinizde aşağıdaki yönergeleri öneriyoruz:
 
- Bağıntıyı etkinleştirmek için günlük depolamayı ve çözümlemeyi merkezileştirin. Azure AD içindeki her günlük kaynağı için bir veri sahibi, erişim Kılavuzu, depolama konumu, verileri işlemek ve erişmek için kullanılan araçlar ve veri saklama gereksinimleri atanmasını sağlayın.  
 
- Azure etkinlik günlüklerini merkezi günlüklerinizi tümleştirdiğinizden emin olun. Endpoint cihazları, ağ kaynakları ve diğer güvenlik sistemleri tarafından oluşturulan güvenlik verilerini toplamak için Azure Izleyici aracılığıyla günlük alma. Azure Izleyici 'de analiz sorgulamak ve gerçekleştirmek için Log Analytics çalışma alanlarını kullanın ve uzun süreli ve arşiv depolama için Azure depolama hesaplarını kullanın.  
 
- Ayrıca, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'e verileri etkinleştirin ve ekleyin.  
 

Birçok kuruluş, daha az sıklıkta kullanılan "soğuk" verileri için sık kullanılan ve Azure depolama için Azure Sentinel ' i "etkin" veriler için kullanmayı seçer.  
 

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](/azure/azure-monitor/platform/diagnostic-settings)   
 

- [Azure Sentinel 'i ekleme](../../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Günlük depolama alanının saklama süresini yapılandırın

**Rehberlik**: Azure Active Directory oturum açma günlüklerini, Denetim günlüklerini ve risk veri günlüklerini depolamak için kullanılan tüm depolama hesapları veya Log Analytics çalışma alanlarının, kuruluşunuzun uyumluluk düzenlemelerine göre ayarlanmış günlük tutma süresine sahip olduğundan emin olun.

Azure Izleyici 'de, Log Analytics çalışma alanı saklama dönemini kuruluşunuzun uyumluluk düzenlemelerine göre ayarlayabilirsiniz. Uzun süreli ve arşiv depolama için Azure depolama, Data Lake veya Log Analytics çalışma alanı hesapları kullanın.

- [Log Analytics çalışma alanı saklama süresini yapılandırma](/azure/azure-monitor/platform/manage-cost-storage)  

- [Kaynak günlüklerini bir Azure depolama hesabında depolama](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Hazırlık: Azure için olay yanıtı sürecini güncelleştirin

**Rehberlik**: Kuruluşunuzda güvenlik olaylarına yanıt vermeye yönelik süreçler bulunduğundan, bu süreçlerin Azure için güncelleştirildiğinden ve hazır olduğunuzdan emin olmak için bu süreçlerin düzenli olarak uygulandığından emin olun.

- [Güvenliği kuruluş genelinde uygulama](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Olay yanıtı başvuru kılavuzu](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Hazırlık: Olay bildirimini ayarlayın

**Rehberlik**: Azure Güvenlik Merkezi'nde güvenlik olayı iletişim bilgilerini ayarlayın. Microsoft, Microsoft Güvenlik Yanıt Merkezi'nin (MSRC) verilerinize kanuna aykırı veya yetkisiz erişim sağlanmasını keşfetmesi durumunda sizinle iletişime geçmek için bu iletişim bilgilerini kullanır. İsterseniz farklı Azure hizmetlerindeki olay uyarılarını ve bildirimlerini olay yanıt gereksinimlerinize göre özelleştirebilirsiniz. 

- [Azure Güvenlik Merkezi güvenlik ilgili kişisini ayarlama](../../security-center/security-center-provide-security-contact-details.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: algılama ve analiz – yüksek kaliteli uyarılara göre olaylar oluşturun 

**Rehberlik**: Yüksek kaliteli uyarılar oluşturmaya ve uyarıların kalitesini ölçmeye yönelik bir sürece sahip olduğunuzdan emin olun. Bu sayede önceki olaylardan dersler çıkarabilir ve analistler için uyarıları önceliklendirebilirsiniz. Bu sayede hatalı pozitif sonuçlarla zaman kaybetmelerini önlemiş olursunuz. 

Yüksek kaliteli uyarılar önceki olaylardan alınan dersler, doğrulanmış topluluk kaynakları ve farklı sinyal kaynaklarını birleştirip bağlantı oluşturarak uyarı oluşturmaya ve temizlemeye yönelik araçlar kullanılarak geliştirilebilir. 

Azure Güvenlik Merkezi, birçok Azure varlığı arasında yüksek kaliteli uyarılar sağlar. ASC veri bağlayıcısını kullanarak uyarıların Azure Sentinel’e akışını yapabilirsiniz. Azure Sentinel bir araştırmaya yönelik olayların otomatik olarak oluşturulması için gelişmiş uyarı kuralları oluşturur. 

Azure kaynaklarına yönelik riskleri tanımlamaya yardımcı olmak için, dışarı aktarma özelliğini kullanarak Azure Güvenlik Merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Uyarıları ve önerileri el ile veya sürekli bir biçimde dışarı aktarabilirsiniz.

- [Dışarı aktarma işlemini yapılandırma](../../security-center/continuous-export.md)

- [Uyarıların Azure Sentinel’e akışını yapma](../../sentinel/connect-azure-security-center.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>OY-4: Algılama ve analiz – olayı araştırma

**Rehber**: Analistlerin olası olayları araştırırken neler olup bittiğini tam olarak görmek için çeşitli veri kaynaklarını sorgulayabildiğinden ve kullanabildiğinden emin olun. Sonlandırma zinciri boyunca olası saldırganın etkinliklerinin izlenebilmesi için çeşitli günlükler toplanmalı, kör noktaların olması önlenmelidir.  Ayrıca diğer analistler için ve gelecekte başvurmak üzere içgörülerin ve çıkarılan derslerin yakalandığından da emin olmalısınız.  

Araştırmaya yönelik veri kaynakları kapsam içindeki hizmetlerden ve çalışan sistemlerden zaten toplanmakta olan merkezi günlük kaynaklarının yanı sıra şunları da içerebilir:

- Ağ verileri – ağ akış günlüklerini ve diğer analiz bilgilerini yakalamak için ağ güvenlik gruplarının akış günlüklerini, Azure Ağ İzleyicisi’ni ve Azure İzleyici’ni kullanın. 

- Çalışan sistemlerin anlık görüntüleri: 

    - Çalışan sistemin diskinin anlık görüntüsünü oluşturmak için Azure sanal makinesinin anlık görüntü özelliğini kullanın. 

    - Çalışan sistemin belleğinin anlık görüntüsünü oluşturmak için işletim sisteminin yerel bellek dökümü özelliğini kullanın.

    - Çalışan sistemlerin anlık görüntülerini oluşturmak için Azure hizmetlerinin anlık görüntü özelliğini veya yazılımınızın kendi özelliğini kullanın.

Azure Sentinel hemen her veri kaynağı üzerinde kapsamlı veri analizi gerçekleştirdiği gibi, olayların tüm yaşam döngüsünü yöneten bir olay yönetim portalı da sağlar. Araştırma sırasında elde edilen inceleme bilgileri izleme ve raporlama amacıyla bir olayla ilişkilendirilebilir. 

- [Windows makinesi diskinin anlık görüntüsü](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux makinesi diskinin anlık görüntüsü](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure Desteği tanılama bilgileri ve bellek dökümü toplama](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel ile olayları araştırma](../../sentinel/tutorial-investigate-cases.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Algılama ve analiz: Olayları önceliklendirin

**Rehber**: Uyarı önem derecesi ve uyarı hassasiyeti temelinde analistlere önce hangi olaylara odaklanılacağı konusunda bağlam sağlayın. 

Azure Güvenlik Merkezi önce hangi uyarıların araştırılması gerektiğini belirlemenize yardımcı olmak için her uyarıya bir önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizde ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır.

Buna ek olarak Azure kaynaklarını, özellikle hassas verileri işleyen kaynakları belirlemek ve kategorilere ayırmak için etiketleri kullanarak kaynakları işaretleyin ve bir adlandırma sistemi oluşturun.  Azure kaynaklarının önem düzeyine ve olayın oluştuğu ortama bağlı olarak uyarıların çözümünde önceliği belirlemek sizin sorumluluğunuzdadır.

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../../security-center/security-center-alerts-overview.md)

- [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](/azure/azure-resource-manager/resource-group-using-tags)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Kapsama, ortadan kaldırma ve kurtarma: Olay işleme sürecini otomatikleştirin

**Rehberlik**: Yanıt süresini kısaltmak ve analistlerin yükünü hafifletmek için el ile yapılan yinelenen görevleri otomatikleştirin. El ile gerçekleştirilen görevlerin yürütülmesi daha uzun sürer ve olay yanıt sürecini yavaşlatarak analistlerin işleyebileceği olay sayısını azaltır. Ayrıca el ile gerçekleştirilen görevler analistler açısından daha yorucudur. Dolayısıyla gecikmelere neden olan insan hatası riskini artırır ve analistlerin karmaşık görevlere etkili bir şekilde odaklanma becerisini azaltır. Eylemleri otomatik olarak tetiklemek veya gelen güvenlik uyarılarını yanıtlamak üzere bir playbook çalıştırmak için Azure Güvenlik Merkezi’nin ve Azure Sentinel’in iş akışı otomasyonu özelliklerini kullanın. Playbook bildirimleri gönderme, hesapları devre dışı bırakma ve sorunlu ağları yalıtma gibi eylemleri gerçekleştirir. 

- [Güvenlik Merkezi’nde iş akışı otomasyonunu yapılandırma](../../security-center/workflow-automation.md)

- [Azure Güvenlik Merkezi’nde otomatik tehdit yanıtlarını ayarlama](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Azure Sentinel’de otomatik tehdit yanıtlarını ayarlama](../../sentinel/tutorial-respond-threats-playbook.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="posture-and-vulnerability-management"></a>Duruş ve Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Duruş ve Güvenlik Açığı Yönetimi](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Azure hizmetleri için güvenli yapılandırmalar oluşturun 

**Rehberlik**: Microsoft kimlik ve erişim yönetimi çözümleri, Şirket içindeki ve buluttaki uygulamalara ve kaynaklara erişimi korumaya yardımcı olur. Kuruluşların kimlik ve erişim yönetimi uygulamalarının güvenli ve saldırılara karşı daha dayanıklı olmasını sağlamak için en iyi güvenlik uygulamalarını izlemesi önemlidir. 

Kimlik ve erişim yönetimi uygulama stratejinize bağlı olarak, kuruluşunuz kimlik altyapınızı güvenli hale getirmek için Microsoft 'un en iyi yöntem kılavuzunu izlemelidir. 

Dış iş ortaklarıyla işbirliği yapan kuruluşlar, güvenlik riskini azaltmak ve hassas kaynakları korumak için uygun idare, güvenlik ve uyumluluk yapılandırmalarına ek olarak değerlendirme ve uygulama.

- [Azure Kimlik Yönetimi ve erişim denetimi için en iyi güvenlik uygulamaları](../../security/fundamentals/identity-management-best-practices.md)

- [Kimlik altyapınızı güvenli hale getirmenin beş adımı](../../security/fundamentals/steps-secure-identity.md)

- [Azure Active Directory ve Microsoft 365 dış işbirliğinin güvenliğini sağlama](secure-external-access-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Azure hizmetleri için güvenli yapılandırmaların sürekliliğini sağlayın

**Rehberlik**: Microsoft güvenli puanı, kuruluşlara kuruluşların tehditleri korumalarına yardımcı olan güvenlik sonrası ve önerilerinden oluşan bir ölçüm sağlar. Kuruluşların kimlik güvenlik duruşunu geliştirmek üzere önerilen geliştirme eylemleri için güvenli puanı düzenli olarak gözden geçirmesi önerilir. 

- [Azure Active Directory kimlik güvenli puanı nedir?](identity-secure-score.md)

- [Microsoft Güvenlik Puanı](/microsoft-365/security/mtp/microsoft-secure-score)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Düzenli aralıklarla saldırı simülasyonları yapın

**Rehberlik**: Gerektiğinde Azure kaynaklarınızda sızma testi veya kırmızı takım etkinlikleri gerçekleştirerek tüm kritik güvenlik bulgularının düzeltilmesini sağlayın.
Sızma testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak için Microsoft Bulut Sızma Testi Etkileşim Kuralları'na uygun hareket edin. Microsoft tarafından yönetilen bulut altyapısına, hizmetlere ve uygulamalara yönelik kırmızı takım ve canlı site sızma testi gerçekleştirmek için Microsoft'un stratejisini ve yürütme sürecini kullanın.

- [Azure'da sızma testi yapma](../../security/fundamentals/pen-testing.md)

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="governance-and-strategy"></a>İdare ve Strateji

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: İdare ve Strateji](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Varlık yönetimi ve veri koruma stratejisi tanımlayın 

**Rehberlik**: Sistemlerin ve verilerin sürekli izlenmesine ve korunmasına yönelik net bir strateji belgelendirdiğinizden ve paylaştığınızdan emin olun. İş açısından kritik verilerin ve sistemlerin bulma, değerlendirme, koruma ve izleme süreçleri için öncelik belirleyin. 
 

 
Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 
 
-   İş risklerine göre veri sınıflandırma standardı
 
-   Riskler ve varlık envanteriyle ilgili güvenlik kuruluşu görünürlüğü 
 
-   Güvenlik kuruluşunun kullanılmasını onayladığı Azure hizmetleri 
 
-   Varlıkların yaşam döngüsü boyunca güvenliği
 
-   Kuruluşun veri sınıflandırmasına uygun gerekli erişim denetimi stratejisi
 
-   Yerel Azure ve üçüncü taraf veri koruma özelliklerinin kullanılması
 
-   Taşıma durumundaki ve bekleyen veriler için şifreleme gereksinimleri
 
-   Uygun şifreleme standartları
 

 
Daha fazla bilgi için aşağıdaki başvuruları inceleyin: 
- [Azure Güvenlik Mimarisi Önerileri: Depolama, veri ve şifreleme](/azure/architecture/framework/security/storage-data-encryption)
 

 
- [Azure Güvenliği Temelleri: Azure'da veri güvenliği, şifreleme ve depolama](../../security/fundamentals/encryption-overview.md) 

 
- [Bulut Benimseme Çerçevesi: Azure'da veri güvenliği ve şifreleme için en iyi deneyimler](../../security/fundamentals/data-encryption-best-practices.md) 

 
- [Azure Güvenlik Karşılaştırması: Varlık yönetimi](/azure/security/benchmarks/security-benchmark-v2-asset-management) 

 
- [Azure Güvenlik Karşılaştırması: Veri koruma](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Kurumsal segmentasyon stratejisini tanımlayın 

**Rehberlik**: Kimlik, ağ, uygulama, abonelik, yönetim grubu ve diğer denetimleri kullanarak varlık erişimi segmentasyonuna yönelik kuruluş genelinde kullanılacak bir strateji belirleyin.

Güvenlik ayrımı gereksinimiyle birbirleriyle iletişim kurma ve verilere erişme gereksinimine sahip olan sistemlerin günlük çalışmasını etkinleştirme gereksinimi arasında bir denge kurun.

Segmentasyon stratejisinin ağ güvenliği, kimlik ve erişim modelleri, uygulama izinleri/erişim modelleri ve insanlar tarafından gerçekleştirilen süreç denetimleri gibi farklı denetim türlerinde tutarlı bir şekilde uygulandığından emin olun.

- [Azure için segmentasyon stratejisi rehberi (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure için segmentasyon stratejisi rehberi (belge)](/security/compass/governance#enterprise-segmentation-strategy)

- [Ağ segmentasyonunu kurumsal segmentasyon stratejisiyle uyumlu hale getirme](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Güvenlik duruşu yönetim stratejisini tanımlayın

**Rehberlik**: Varlıklarınız ve içinde bulundukları ortamla ilgili riskleri sürekli olarak ölçün ve ortadan kaldırın. Yayımlanmış uygulamalar, ağ giriş ve çıkış noktaları, kullanıcı ve yönetici uç noktaları gibi değeri yüksek varlıkları ve kullanıma açık olan saldırı yüzeylerini önceliklendirin.

- [Azure Güvenlik Karşılaştırması: Duruş ve güvenlik açığı yönetimi](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Kuruluş genelindeki rolleri, sorumlulukları ve hesap verilebilirlik durumlarını uyumlu hale getirin

**Rehberlik**: Güvenlik kuruluşunuzdaki roller ve sorumluluklar için net bir strateji oluşturup bunu belgelendirdiğinizden ve paylaştığınızdan emin olun. Güvenlik kararları için net bir hesap verilebilirlik süreci oluşturmayı önceliklendirin, herkesi paylaşılan sorumluluk modeli hakkında eğitin ve teknik ekiplere bulut ortamının güvenliğini sağlamaya yönelik eğitimler verin.

- [Azure Güvenliği En İyi Deneyimi 1 - İnsanlar: Ekipleri Bulut Güvenliği Yolculuğu Hakkında Eğitin](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Güvenliği En İyi Deneyimi 2 - İnsanlar: Ekipleri Bulut Güvenliği Teknolojileri Hakkında Eğitin](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Güvenliği En İyi Deneyimi 3 - Süreç: Bulut Güvenliği Kararları için Hesap Verilebilirlik Ataması Yapın](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Ağ güvenlik stratejisini tanımlayın

**Rehberlik**: Kuruluşunuzun genel güvenlik erişimi denetimi stratejisi kapsamında Azure ağ güvenliği için bir yaklaşım belirleyin.  

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   Merkezi ağ yönetimi ve güvenlik sorumluluğu

-   Kurumsal segmentasyon stratejisiyle uyumlu sanal ağ segmentasyon modeli

-   Farklı tehdit ve saldırı senaryolarına yönelik düzeltme stratejisi

-   İnternet uç düğümü ile giriş ve çıkış stratejisi

-   Hibrit bulut ve şirket içi bağlantı stratejisi

-   Güncel ağ güvenliği yapıtları (ağ diyagramları, başvuru amaçlı ağ mimarisi vb.)

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:
- [Azure Güvenliği En İyi Deneyimi 11 - Mimari. Tek ve birleşik güvenlik stratejisi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure ile ağ güvenliğine genel bakış](../../security/fundamentals/network-overview.md)

- [Kurumsal ağ mimarisi stratejisi](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Kimlik ve ayrıcalıklı erişim stratejisini tanımlayın

**Rehberlik**: Kuruluşunuzun genel güvenlik erişimi denetimi stratejisi kapsamında Azure kimlik ve ayrıcalıklı erişim yaklaşımları belirleyin.  

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   Merkezi bir kimlik ve kimlik doğrulaması sistemi ile diğer iç ve dış kimlik sistemleriyle olan bağlantısı

-   Farklı kullanım örneklerine ve koşullara yönelik güçlü kimlik doğrulama yöntemleri

-   Yüksek ayrıcalıklı kullanıcıların korunması

-   Kullanıcı etkinlikleri için anomali izleme ve işleme  

-   Kullanıcı kimliği ve erişim gözden geçirmesi ile uzlaştırma süreci

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Azure Güvenlik Karşılaştırması: Kimlik yönetimi](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Güvenlik Karşılaştırması: Ayrıcalıklı erişim](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Güvenliği En İyi Deneyimi 11 - Mimari. Tek ve birleşik güvenlik stratejisi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure'da kimlik yönetim güvenliğine genel bakış](../../security/fundamentals/identity-management-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Günlüğe kaydetme ve tehdit yanıtı stratejisi tanımlayın

**Rehberlik**: Uyumluluk gereksinimleri kapsamında tehditleri hızlı bir şekilde algılamak ve düzeltmek için bir günlüğe kaydetme ve tehdit yanıtı stratejisi oluşturun. Tümleştirme ve el ile gerçekleştirilen adımlar yerine tehditlere odaklanabilmeleri için analistlere yüksek kaliteli uyarılar ve sorunsuz deneyimler sunmayı önceliklendirin. 

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   Güvenlik operasyonları (SecOps) organizasyonunun rolü ve sorumlulukları 

-   NIST veya diğer sektör çerçeveleriyle uyumlu iyi tanımlanmış bir olay yanıt süreci 

-   Tehdit algılama, olay yanıtı ve uyumluluk gereksinimlerini karşılamak için günlük yakalama ve saklama

-   SIEM, yerel Azure özellikleri ve diğer kaynaklar aracılığıyla merkezi görünürlük ve bilgi bağıntısı 

-   Planı müşterilerinize, tedarikçilerinize ve diğer proje katılımcılarına iletme ve onları bilgilendirme

-   Günlüğe kaydetme ve tehdit algılama, adli araştırma, saldırı önleme ve ortadan kaldırma gibi olay işleme adımları için yerel Azure ve üçüncü taraf platformları kullanma

-   Olayları ve çıkarılan dersler ile kanıt koruma gibi olay sonrası etkinlikleri işlemeye yönelik süreçler

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Azure Güvenlik Karşılaştırması: Günlüğe kaydetme ve tehdit algılama](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Güvenlik Karşılaştırması: Olay yanıtı](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Güvenliği En İyi Deneyimi 4 - Süreç. Bulut için Olay Yanıt Sürecini Güncelleştirme](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Benimseme Çerçevesi, günlüğe kaydetme ve raporlama kararı rehberi](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure ile kurumsal ölçek, yönetim ve izleme](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](/azure/security/benchmarks/overview)
- [Azure güvenlik temelleri](/azure/security/benchmarks/security-baselines-overview) hakkında daha fazla bilgi edinin
