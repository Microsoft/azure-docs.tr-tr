---
title: Azure için Azure Güvenlik temeli açık
description: Azure Mathouse güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 69234d7c22f1725f6f21fe52a455e64d743f052e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709861"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>Azure için Azure Güvenlik temeli açık

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 2,0](../security/benchmarks/overview.md) ' dan Azure açık thouse 'a kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure Mathouse için geçerli olan ilgili kılavuza göre gruplandırılır. Azure için geçerli olmayan **denetimler** dahil değildir.

Azure Mathouse 'ın Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Azure mathouse güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="identity-management"></a>Kimlik Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Kimlik Yönetimi](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory'yi standart merkezi kimlik ve kimlik doğrulaması sistemi haline getirin

**Kılavuz**: Azure Aydınlathouse, varsayılan kimlik ve erişim yönetimi hizmeti olarak Azure Active Directory (Azure AD) kullanır. Kuruluşunuzun kimlik ve erişim yönetimini yönetmek için Azure AD 'yi standartlaştırın:
- Azure portal, Azure depolama, Azure sanal makinesi (Linux ve Windows), Azure Key Vault, PaaS ve SaaS uygulamaları gibi Microsoft Bulut kaynaklar.
- Kuruluşunuzun kaynakları. Örneğin Azure’daki uygulamalar veya kurumsal ağ kaynaklarınız.

Azure, bir yönetim kiracısındaki belirlenen kullanıcılara, bir müşterinin kiracısında Temsilcili aboneliklere ve/veya kaynak gruplarına erişmelerine olanak sağlayan bir Azure yerleşik rolü vardır. Tüm yerleşik roller, sahip veya DataActions iznine sahip herhangi bir yerleşik rol haricinde Şu anda desteklenmektedir. Kullanıcı erişimi yönetici rolü yalnızca yönetilen kimliklere rol atama konusunda sınırlı kullanım için desteklenir. Özel roller ve klasik abonelik yöneticisi rolleri desteklenmez.

- [Azure Active Directory'deki kiracılar](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Uygulama için dış kimlik sağlayıcılarını kullanma](../active-directory/external-identities/identity-providers.md) 

- [Azure Active Directory'deki kimlik güvenliği puanı nedir?](../active-directory/fundamentals/identity-secure-score.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Uygulama kimliklerini güvenli ve otomatik bir şekilde yönetin

**Kılavuz**: Azure Yönetilen kimlikler, Azure hizmetleri ve Azure AD kimlik doğrulamasını destekleyen kaynaklar için kimlik doğrulaması yapabilir. Kimlik doğrulaması, önceden tanımlanmış erişim verme kuralları aracılığıyla etkinleştirilir ve kaynak kodunda veya yapılandırma dosyalarında sabit kodlu kimlik bilgileri önlenir. Azure 'un bir müşteri aboneliğinde Kullanıcı erişimi yönetici rolüne sahip kullanıcılar, bu müşterinin kiracısında yönetilen bir kimlik oluşturabilir. Bu rol, Azure Athouse ile genel olarak desteklenmekle birlikte, bu özel senaryoda kullanılabilir ve bu izne sahip kullanıcılar yönetilen kimliklere bir veya daha fazla yerleşik rol atayabilir.

Yönetilen kimlikleri desteklemeyen hizmetler için, bunun yerine kaynak düzeyinde kısıtlanmış izinlerle bir hizmet sorumlusu oluşturmak için Azure AD 'yi kullanın. Azure Kathouse, hizmet sorumlularının, ekleme işlemi sırasında verilen rollere göre müşteri kaynaklarına erişmesine olanak sağlar. Hizmet sorumlularını sertifika kimlik bilgileriyle yapılandırmanız ve istemci gizliliklerinin geri dönmesi önerilir. Her iki durumda da, Azure Key Vault çalışma zamanı ortamının (bir Azure işlevi gibi) anahtar kasasından kimlik bilgilerini alabilmesi için Azure tarafından yönetilen kimliklerle birlikte kullanılabilir.

- [Azure Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md)

- [Azure hizmet sorumlusu](/powershell/azure/create-azure-service-principal-azureps)

- [Güvenlik sorumlusu kaydı için Azure Key Vault kullanma](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [Bir müşteri kiracısında yönetilen bir kimliğe roller atayabilecek bir Kullanıcı oluşturma](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Tüm Azure Active Directory tabanlı erişim girişimleri için güçlü kimlik doğrulaması denetimleri kullanın

**Rehberlik**: Temsilcili müşteri kaynaklarına erişimi olacak kullanıcılar da dahil olmak üzere, yönettiğiniz kiracınızdaki tüm kullanıcılar için MULTI-Factor AUTHENTICATION (MFA) gerektir. Müşterilerinizin kiracılarında MFA 'yı da uygulamasına sormasını istediğinizi öneririz.

- [Azure'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Hesap anomalilerini izleyin ve uyarı oluşturun

**Kılavuz**: Azure AD aşağıdaki veri kaynaklarını sağlar: 

-   Oturum açma bilgileri – Oturum açma bilgileri raporu, yönetilen uygulamaların kullanımı ve kullanıcı oturum açma etkinlikleri hakkında bilgi sağlar.

-   Denetim günlükleri-Azure AD 'deki çeşitli özelliklerle yapılan tüm değişiklikler için Günlükler aracılığıyla izlenebilirlik sağlar. Günlüğe kaydedilen değişiklikler denetim günlüklerine örnek olarak Kullanıcı, uygulama, Grup, rol ve ilke ekleme veya kaldırma sayılabilir.

-   Riskli oturum açma işlemleri - Riskli oturum açma işlemi bir kullanıcı hesabının meşru sahibi olmayan bir kişi tarafından gerçekleştirilmiş olabilecek oturum açma girişiminin göstergesidir.

-   Riskli oldukları belirlenen kullanıcılar - Riskli kullanıcı, güvenliği tehlikeye girmiş olabilecek bir kullanıcı hesabının göstergesidir.

Bu veri kaynakları Azure Izleyici, Azure Sentinel veya üçüncü taraf SıEM sistemleriyle tümleştirilebilir.

Azure Pthouse kullanan hizmet sağlayıcıları Azure AD günlüklerini Azure Sentinel 'e iletebilir ve hesap bozuklularını izlemek ve uyarmak için kiracılar genelinde uyarıları görüntüleyebilir/ayarlayabilir.

- [Azure AD 'de denetim etkinlik raporları](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Sentinel çalışma alanlarını ölçekli olarak yönetme](how-to/manage-sentinel-workspaces.md)

- [Azure AD 'den Azure Sentinel 'e veri bağlama](../sentinel/connect-azure-active-directory.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Azure kaynaklarına erişimi koşullara göre kısıtlayın

**Kılavuz**: Azure Aydınlathouse, temsilcili müşteri kaynakları için koşullu erişim özelliğini desteklemez. Yönetim kiracısında, belirli IP aralıklarından Multi-Factor Authentication (MFA) kullanılmasına yönelik Kullanıcı oturumu açma gerekliliği gibi Kullanıcı tanımlı koşullara göre daha ayrıntılı erişim denetimi için Azure AD koşullu erişimi kullanın. Ayrıntılı bir kimlik doğrulama oturumu yönetimi, farklı kullanım durumları için Azure AD koşullu erişim ilkesi aracılığıyla da kullanılabilir. 

Temsilcili müşteri kaynaklarına erişimi olacak kullanıcılar da dahil olmak üzere, yönettiğiniz kiracınızdaki tüm kullanıcılar için MFA gerektirmelisiniz. Müşterilerinizin kiracılarında MFA 'yı da uygulamasına sormasını istediğinizi öneririz.

- [Azure koşullu erişimine genel bakış](../active-directory/conditional-access/overview.md)

- [Ortak koşullu erişim ilkeleri](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Erişim Denetimi ile kimlik doğrulama oturum yönetimini yapılandırma](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="privileged-access"></a>Ayrıcalıklı Erişim

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ayrıcalıklı Erişim](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Yüksek ayrıcalıklı kullanıcıları koruyun ve sınırlayın

**Rehberlik**: yüksek ayrıcalıklı kullanıcı hesabı sayısını sınırlayın ve bu hesapları yükseltilmiş bir düzeyde koruyun. Azure Aydınlathouse özelliğini etkinleştirmek ve kullanmak için genel yönetici hesabı gerekli değildir.

Kiracı düzeyindeki etkinlik günlüğü verilerine erişmek için bir hesaba, kök kapsamdaki (/) Izleme okuyucusu Azure yerleşik rolünün atanması gerekir. Kök kapsamdaki Izleme okuyucusu rolü geniş bir erişim düzeyi olduğundan, bu rolü tek bir kullanıcı veya gruba göre değil, bir hizmet sorumlusu hesabına atamanızı öneririz. Bu atama, ek yükseltilmiş erişimle genel yönetici rolüne sahip bir kullanıcı tarafından gerçekleştirilmelidir. Bu yükseltilmiş erişim, rol ataması yapılmadan önce hemen eklenmelidir, sonra atama tamamlandığında kaldırılır.

- [Azure AD'de yönetici rolü izinleri](../active-directory/roles/permissions-reference.md)

- [Kiracı düzeyindeki etkinlik günlüğü verilerini izlemek için erişim atama](how-to/monitor-delegation-changes.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: İş açısından kritik sistemlere yönetici erişimini kısıtlayın

**Kılavuz**: Azure ıthouse, iş açısından kritik sistemlere erişimi yalıtmak için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanır.

Kullanıcıların yalnızca belirli görevlerini gerçekleştirmek için gerekli izinlere sahip olması için en az ayrıcalık ilkesini izlediğinizden emin olun.

Active Directory Etki Alanı Denetleyicileri (DC 'Ler), güvenlik araçları ve iş açısından kritik sistemlerde yüklü aracılarla sistem yönetimi araçları gibi iş açısından kritik erişimlerinize yönetim erişimi olan yönetim, kimlik ve güvenlik sistemlerine erişimi de kısıtlatığınızdan emin olun. Bu yönetim ve güvenlik sistemlerine zarar veren saldırganlar, iş açısından kritik varlıkların güvenliğini tehlikeye atabilir.

Tutarlı erişim denetimi sağlamak için, tüm erişim denetimi türleri kurumsal segmentasyon stratejinize hizalanmalıdır.

- [Azure bileşenleri ve başvuru modeli](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Yönetim grubu erişimi](../governance/management-groups/overview.md#management-group-access)

- [Azure abonelik yöneticileri](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Azure ışıklı kullanımı için kullanıcıları ve rolleri tanımlamaya yönelik en iyi uygulamalar](concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Kullanıcı erişimini düzenli olarak gözden geçirin ve mutabık kılın

**Kılavuz**: Azure ışıklı kullanım, kaynaklarını yönetmek için Azure Active Directory (Azure AD) hesapları kullanır, hesapların ve erişiminin geçerli olduğundan emin olmak için Kullanıcı hesaplarını ve erişim atamasını düzenli olarak gözden geçirin. Azure AD erişim incelemelerini, grup üyeliklerini ve kurumsal uygulamalara erişimi ve rol atamalarını gözden geçirmek için kullanabilirsiniz. Azure AD raporlama, eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlayabilir. Gözden geçirme sürecini kolaylaştırmak için, erişim gözden geçirme raporu iş akışı oluşturmak için Azure AD Privileged Identity Management de kullanabilirsiniz.

Müşteriler, Azure portal Azure ışıklı aracılığıyla kiracı yönetimi aracılığıyla kullanıcılara verilen erişim düzeyini gözden geçirebilir. Bu erişimi istediğiniz zaman kaldırabilirler.

Ayrıca, Azure Privileged Identity Management, çok fazla sayıda yönetici hesabı oluşturulduğunda ve eski veya yanlış yapılandırılmış yönetici hesaplarını belirlemek için de yapılandırılabilir.

Not: bazı Azure Hizmetleri, Azure AD aracılığıyla yönetilmeyen yerel kullanıcıları ve rolleri destekler. Bu kullanıcıları ayrı ayrı yönetmeniz gerekir.

- [Privileged Identity Management (PıM) içinde Azure Kaynak rolleri için erişim gözden geçirmesi oluşturma](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Bir temsilciye erişimi kaldırma](how-to/remove-delegation.md)

- [Azure AD kimlik ve erişim gözden geçirmelerini kullanma](../active-directory/governance/access-reviews-overview.md)

- [Azure portal hizmet sağlayıcıları sayfasını görüntüleme](how-to/view-manage-service-providers.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Azure AD'de acil durum erişimini ayarlayın

**Kılavuz**: Azure açık thouse, kaynaklarını yönetmek için Azure Active Directory ile tümleşiktir. Yanlışlıkla Azure AD kuruluşunuzun dışına kilitlenmesini engellemek için, normal yönetim hesapları kullanılmazsa erişim için bir acil durum erişim hesabı ayarlayın. Acil durum erişim hesapları genellikle yüksek ayrıcalığa sahiptir ve herhangi bir kişiye atanmamalıdır. Acil durum erişim hesaplarının kullanım alanı, normal yönetici hesaplarının kullanılamadığı acil veya "camı kırın" senaryolarıyla sınırlıdır.

Acil durum erişim hesaplarının kimlik bilgilerinin (parola, sertifika veya akıllı kart) güvenli bir şekilde saklandığından ve yalnızca acil bir durumda bunları kullanma yetkisine sahip olan kullanıcılar tarafından bilindiğinden emin olmanız gerekir.

- [Azure AD'deki acil durum erişim hesaplarını yönetme](../active-directory/roles/security-emergency-access.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="pa-5-automate-entitlement-management"></a>PA-5: yetkilendirme yönetimini otomatikleştirme 

**Kılavuz**: Azure Aydınlathouse, kaynaklarını yönetmek için Azure Active Directory (Azure AD) ile tümleşiktir. Erişim atamaları, incelemeler ve süre sonu dahil olmak üzere, erişim isteği iş akışlarını otomatikleştirmek için Azure AD yetkilendirme yönetimi özelliklerini kullanın. Çift veya çok aşamalı onay de desteklenir.

- [Azure AD erişim gözden geçirmeleri nelerdir?](../active-directory/governance/access-reviews-overview.md) 

- [Azure AD yetkilendirme yönetimi nedir?](../active-directory/governance/entitlement-management-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Ayrıcalıklı erişim iş istasyonlarını kullanın

**Rehberlik**: Güvenli ve yalıtılmış iş istasyonları yöneticiler, geliştiriciler ve kritik hizmet operatörleri gibi hassas rollerin güvenliği açısından kritik öneme sahiptir. Gereksinimlerinize bağlı olarak, üretim ortamlarında Azure açık Thouse ile yönetim görevlerini gerçekleştirmek için yüksek düzeyde güvenli Kullanıcı iş istasyonları ve/veya Azure savunma kullanabilirsiniz. Yönetim görevlerine yönelik güvenli ve yönetilen bir kullanıcı iş istasyonu dağıtmak için Azure Active Directory, Microsoft Defender Gelişmiş Tehdit Koruması (ATP) ve/veya Microsoft Intune hizmetlerini kullanın. Güvenli iş istasyonları, güçlü kimlik doğrulaması, yazılım ve donanım temelleri ve kısıtlı mantıksal ve ağ erişimi gibi güvenli yapılandırmayı zorlamak için merkezi olarak yönetilebilir. 

- [Ayrıcalıklı erişim iş istasyonlarını anlama](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Ayrıcalıklı erişim iş istasyonu dağıtma](/security/compass/privileged-access-deployment)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>AE-7: Tam yetecek kadar yönetim uygulama (en düşük ayrıcalık ilkesi) 

**Kılavuz**: Azure Aydınlathouse, kaynaklarını yönetmek için Azure rol tabanlı erişim denetımı (RBAC) ile tümleşiktir. Azure RBAC ağ atamaları aracılığıyla Azure kaynak erişimini yönetmenize olanak tanır. Kullanıcılar, gruplar, hizmet sorumluları ve yönetilen kimliklere bu yerleşik rolleri atayabilirsiniz. Belirli kaynaklar için önceden tanımlanmış yerleşik roller vardır ve Azure CLI, Azure PowerShell veya Azure portal gibi araçlardan yararlanarak bu rollerin envanteri çıkarılabilir ve bunlar sorgulanabilir. Azure RBAC aracılığıyla kaynaklara atadığınız ayrıcalıklar her zaman yalnızca söz konusu rollere gerekenlerle sınırlanmalıdır. Bu yöntem Azure AD Privileged Identity Management’ın (PIM) tam zamanında (JIT) yaklaşımını tamamlayıcı niteliktedir ve düzenli aralıklarla gözden geçirilmelidir. İzin ayırmak için yerleşik roller kullanın ve gerektiğinde yalnızca özel roller oluşturun.

Azure kathouse, Azure yerleşik rollerini kullanarak Temsilcili müşteri kaynaklarına erişim sağlar. Çoğu durumda, bu rolleri birçok bireysel kullanıcı hesabı yerine bir grup veya hizmet sorumlusuna atamak isteyeceksiniz. Bu, erişim gereksinimleriniz değiştiğinde planı güncelleştirmek ve yeniden yayınlamak zorunda kalmadan bireysel kullanıcılar için erişim eklemenize veya kaldırmanıza olanak sağlar.

Müşteri kaynaklarını bir yönetim kiracısına devretmek için bir dağıtımın, eklendi olan abonelik (veya eklendi olan kaynak gruplarını içeren) için sahip yerleşik rolüne sahip olan, müşterinin kiracısındaki Konuk olmayan bir hesap tarafından yapılması gerekir.

- [Azure açık Thouse kiracılarını, kullanıcıları ve rolleri anlayın](concepts/tenants-users-roles.md)

- [En az ayrıcalık ilkesini Azure ışıklı kullanım için uygulama](concepts/recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)

- [Azure rol tabanlı erişim denetimi nedir (Azure RBAC)](../role-based-access-control/overview.md) 

- [Azure AD kimlik ve erişim gözden geçirmelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

## <a name="asset-management"></a>Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Varlık Yönetimi](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>VY-1: Güvenlik ekibinin varlıklara yönelik risklerde görünürlük elde etmesini sağlama

**Rehber**: Güvenlik ekiplerinin Azure Güvenlik Merkezi’ni kullanarak güvenlik risklerini izlemesini sağlamak için bu ekiplere Azure kiracınız ve abonelikleriniz üzerinde Güvenlik Okuyucusu izinleri verildiğinden emin olun. 

Güvenlik ekibi sorumluluklarının yapılandırmasına bağlı olarak, güvenlik risklerinin izlenmesi merkezi güvenlik ekibinin veya yerel bir ekibin sorumluluğunda olabilir. Gerçi güvenlik içgörüleri ve risklerinin kuruluş içinde her zaman merkezi bir konumda toplanması gerekir. 

Güvenlik Okuyucusu izinleri kiracının tamamına (Kök Yönetim Grubu) geniş kapsamlı olarak uygulanabileceği gibi, izinlerin kapsamı yönetim gruplarıyla veya belirli aboneliklerle de sınırlanabilir. 

Not: İş yükleri ve hizmetlerin görünürlüğünü elde etmek için ek izinler gerekebilir. 

- [Güvenlik Okuyucusu Rolüne Genel Bakış](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure Yönetim Gruplarına Genel Bakış](../governance/management-groups/overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Güvenlik ekibinin varlık envanterine ve meta verilerine erişebildiğinden emin olun

**Rehberlik**: müşterilerin güvenlik takımları, Azure açık thouse kullanan hizmet sağlayıcılarının yaptığı etkinlikleri görmek için etkinlik günlüklerini gözden geçirebilir. 

Bir hizmet sağlayıcı, güvenlik ekibinin Temsilcili müşteri kaynaklarını incelemesine izin vermek isterse, güvenlik ekibinin yetkilendirmeleri okuyucu yerleşik rolünü içermelidir.

- [Müşterinin hizmet sağlayıcısı etkinliğini nasıl gözden geçirebilirler](how-to/view-service-provider-activity.md)

- [Azure 'da bir müşteriyi kullanıma ekleme sırasında rolleri belirtme](how-to/onboard-customer.md#define-roles-and-permissions)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Yalnızca onaylı Azure hizmetlerini kullanın

**Rehberlik**: kullanıcıların ortamınızda hangi hizmetleri sağlayabileceğini denetlemek ve kısıtlamak Için Azure İlkesi ' ni kullanın. Abonelikler içindeki kaynakları sorgulamak ve bulmak için Azure Kaynak Grafı'nı kullanın. Ayrıca Azure İzleyici'yi kullanarak onaylanmamış hizmetler algılandığında uyarı tetikleme amacıyla kurallar oluşturabilirsiniz.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](../governance/policy/samples/built-in-policies.md#general) 

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>VY-4: Varlık yaşam döngüsü yönetiminin güvenliğini sağlama

**Rehberlik**: artık gerekli olmadıklarında Azure açık thouse tarafından temsilci seçilmiş kaynaklara erişimi kaldırın, böylece hizmet sağlayıcılarının artık erişimi olmaz. Erişim, müşteri ya da hizmet sağlayıcısı tarafından kaldırılabilir. 

- [Temsilci erişimini kaldırma](how-to/remove-delegation.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>ÖÖ-5: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Kılavuz**: Azure Aydınlathouse, kimlik ve kimlik doğrulama için Azure Active Directory (Azure AD) ile tümleşiktir. "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcıların Azure kaynakları yöneticisi ile etkileşime geçmesini sınırlamak için Azure koşullu erişimi kullanabilirsiniz.

- [Azure Kaynak Yöneticisi 'ne erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

## <a name="logging-and-threat-detection"></a>Günlüğe Kaydetme ve Tehdit Algılama

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Günlüğe Kaydetme ve Tehdit Algılama](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure kaynakları için tehdit algılamayı etkinleştirme

**Kılavuz**: Azure ışıklı kullanım sayesinde müşterilerinizin Azure kaynaklarını olası tehditler ve bozukluklar için izleyebilirsiniz. Analistlerin sıralama için yanlış pozitif durumları azaltmaya yönelik yüksek kaliteli uyarılar almaya odaklanın. Uyarılar, günlük verilerinden, aracılardan veya diğer verilerden kaynaklıdır.

Azure hizmet telemetrisini izlemeyi ve hizmet günlüklerini çözümlemeyi temel alan Azure Güvenlik Merkezi 'nin yerleşik tehdit algılama özelliğini kullanın. Veriler, sistemden güvenlikle ilgili çeşitli yapılandırma ve olay günlüklerini okuyan ve analiz için verileri çalışma alanınıza kopyalayan Log Analytics Aracı kullanılarak toplanır. 

Ayrıca, müşteri ortamınızda belirli ölçütlerle eşleşen tehditleri sağlayan analiz kuralları oluşturmak için Azure Sentinel ' i kullanın. Kurallar, her olayı araştırmak için ölçütler eşleştiğinde olaylar oluşturur. Azure Sentinel, tehdit algılama yeteneklerini geliştirmek için üçüncü taraf tehdit bilgilerini de içeri aktarabilir. 

- [Azure Güvenlik Merkezi’nde tehdit koruması](../security-center/azure-defender.md)

- [Azure Güvenlik Merkezi güvenlik uyarıları başvuru kılavuzu](../security-center/alerts-reference.md)

- [Tehditleri algılamak için özel analiz kuralları oluşturma](../sentinel/tutorial-detect-threats-custom.md)

- [Azure Sentinel çalışma alanlarını ölçekli olarak yönetme](how-to/manage-sentinel-workspaces.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure kimlik ve erişim yönetimi için tehdit algılamayı etkinleştirin

**Kılavuz**: Azure Dıthouse sayesinde, çok sayıda başarısız kimlik doğrulama girişimi ve abonelikteki kullanım dışı hesaplar gibi, yönettiğiniz müşteri kiracılarında belirli şüpheli etkinliklerle ilgili uyarı almak Için Azure Güvenlik Merkezi 'ni kullanabilirsiniz.

Azure Active Directory (Azure AD), daha karmaşık izleme ve analiz kullanım örnekleri için Azure AD raporlama 'da görüntülenebilen veya Azure Izleyici, Azure Sentinel veya diğer SıEM/izleme araçlarıyla tümleştirilen aşağıdaki kullanıcı günlüklerini sağlar:
- Oturum açma-oturum açma raporu, yönetilen uygulamaların kullanımı ve Kullanıcı oturum açma etkinliklerinin kullanımı hakkında bilgi sağlar.
- Denetim günlükleri - Azure AD içindeki çeşitli özellikler tarafından yapılan tüm değişiklikler için günlükler aracılığıyla izlenebilirlik sağlar. Azure AD içindeki herhangi bir kaynakta yapılan kullanıcı, uygulama, grup, rol ve ilkeleri ekleme veya kaldırma işlemleri gibi değişiklikler, denetim günlüklerine örnek gösterilebilir.
- Riskli oturum açma-riskli oturum açma, bir kullanıcı hesabının meşru sahibi olmayan birisi tarafından gerçekleştirilmiş olabilecek oturum açma girişimine yönelik bir göstergedir.
- Riskli oldukları belirlenen kullanıcılar - Riskli kullanıcı, güvenliği tehlikeye girmiş olabilecek bir kullanıcı hesabının göstergesidir.

Azure Güvenlik Merkezi aşırı fazla sayıda başarısız kimlik doğrulaması girişimi, abonelikte kullanım dışı kalan hesaplar gibi bazı şüpheli etkinliklerde de uyarı verebilir. Temel güvenlik durumunu izlemeye ek olarak, Azure Güvenlik Merkezi’nin Tehdit Koruması modülü tek tek Azure işlem kaynaklarından (sanal makineler, kapsayıcılar, uygulama hizmeti), veri kaynaklarından (SQL DB ve depolama) ve Azure hizmet katmanlarından daha kapsamlı güvenlik uyarıları da toplayabilir. Bu özellik, bireysel kaynakların içindeki hesap anormallikleri hakkında görünürlük sağlar. 

- [Azure Pthouse ile geliştirilmiş hizmetler ve senaryolar](concepts/cross-tenant-management-experience.md#enhanced-services-and-scenarios)

- [Azure Active Directory’de denetim etkinliği raporları](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Kimlik Koruması’nı etkinleştirme](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure Güvenlik Merkezi’nde tehdit koruması](../security-center/azure-defender.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure kaynakları için günlüğe kaydetmeyi etkinleştirin

**Kılavuz**: otomatik olarak kullanılabilen etkinlik günlükleri, okuma IŞLEMLERI (Get) dışında Azure açık thouse kaynaklarınız için tüm yazma IŞLEMLERINI (put, Post, Delete) içerir. Etkinlik günlükleri, sorun giderirken bir hata bulmak veya kuruluşunuzdaki bir kullanıcının bir kaynağı nasıl değiştirdiğinizi izlemek için kullanılabilir.

Azure Use ile Azure Izleyici günlüklerini yönettiğiniz müşteri kiracılarında ölçeklenebilir bir şekilde kullanabilirsiniz. Müşteri verileri, kendilerine aktarılması yerine kiracılarında kalacak şekilde doğrudan müşteri kiracılarında Log Analytics çalışma alanları oluşturun. Bu Ayrıca, Log Analytics tarafından desteklenen herhangi bir kaynak ve hizmetin merkezi olarak izlenmesini sağlar ve bu sayede izlediğiniz veri türlerine daha fazla esneklik sunar.

Azure açık bir kullanım için abonelikleri temsilci seçen müşteriler, gerçekleştirilen tüm eylemleri görmek için Azure etkinlik günlüğü verilerini görüntüleyebilir. Bu, müşterilere hizmet sağlayıcılarının yaptığı işlemlere ve müşterinin kendi Azure Active Directory (Azure AD) kiracısındaki kullanıcılar tarafından gerçekleştirilen işlemlere tam görünürlük sağlar.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure 'da günlüğe kaydetme ve farklı günlük türlerini anlama](../azure-monitor/essentials/platform-logs-overview.md)

- [Temsil edilen kaynakları ölçeklendirmeye göre izleme](how-to/monitor-at-scale.md)

- [Hizmet sağlayıcısı etkinliğini görüntüleme](how-to/view-service-provider-activity.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Paylaşılan

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Güvenlik günlüğü yönetim ve analiz süreçlerini merkezileştirin

**Rehberlik**: bağıntı sağlamak için günlük depolamayı ve çözümlemeyi merkezileştirme. Her günlük kaynağı için bir veri sahibi, erişim Kılavuzu, depolama konumu, verileri işlemek ve erişmek için kullanılan araçları ve veri saklama gereksinimlerini atadığınızdan emin olun.

Azure etkinlik günlüklerini merkezi günlüklerinizi tümleştirdiğinizden emin olun. Endpoint cihazları, ağ kaynakları ve diğer güvenlik sistemleri tarafından oluşturulan güvenlik verilerini toplamak için Azure Izleyici aracılığıyla günlük alma. Azure Izleyici 'de analiz sorgulamak ve gerçekleştirmek için Log Analytics çalışma alanlarını kullanın ve uzun süreli ve arşiv depolama için Azure depolama hesaplarını kullanın.

Ayrıca, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'e verileri etkinleştirin ve ekleyin.

Azure Use ile Azure Izleyici günlüklerini yönettiğiniz müşteri kiracılarında ölçeklenebilir bir şekilde kullanabilirsiniz. Müşteri verileri, kendilerine aktarılması yerine kiracılarında kalacak şekilde doğrudan müşteri kiracılarında Log Analytics çalışma alanları oluşturun. Bu Ayrıca, Log Analytics tarafından desteklenen herhangi bir kaynak ve hizmetin merkezi olarak izlenmesini sağlar ve bu sayede izlediğiniz veri türlerine daha fazla esneklik sunar.

Azure açık bir kullanım için abonelikleri temsilci seçen müşteriler, gerçekleştirilen tüm eylemleri görmek için Azure etkinlik günlüğü verilerini görüntüleyebilir. Bu, müşterilere hizmet sağlayıcılarının yaptığı işlemlere ve müşterinin kendi Azure Active Directory (Azure AD) kiracısındaki kullanıcılar tarafından gerçekleştirilen işlemlere tam görünürlük sağlar.

Birçok kuruluş, daha az sıklıkta kullanılan "soğuk" verileri için sık kullanılan ve Azure depolama için Azure Sentinel ' i "etkin" veriler için kullanmayı seçer.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md)

- [Temsil edilen kaynakları ölçeklendirmeye göre izleme](how-to/monitor-at-scale.md)

- [Müşterilerin hizmet sağlayıcısı etkinliğini nasıl görüntüleyebilirler](how-to/view-service-provider-activity.md)

- [Azure Sentinel çalışma alanlarını ölçekli olarak yönetme](how-to/manage-sentinel-workspaces.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Günlük depolama alanının saklama süresini yapılandırın

**Kılavuz**: Azure Mathouse Şu anda güvenlikle ilgili hiçbir günlük oluşturmuyor. Hizmet sağlayıcısı etkinliğini görüntülemek isteyen müşteriler, uyumluluk, düzenleme ve iş gereksinimlerine göre günlük bekletmeyi yapılandırabilir. 

Azure Izleyici 'de, Log Analytics çalışma alanı saklama dönemini kuruluşunuzun uyumluluk düzenlemelerine göre ayarlayabilirsiniz. Uzun süreli ve arşiv depolama için Azure depolama, Data Lake veya Log Analytics çalışma alanı hesapları kullanın.

- [Log Analytics veri saklama süresini değiştirme](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Azure depolama hesabı günlükleri için bekletme ilkesini yapılandırma](../storage/common/manage-storage-analytics-logs.md#configure-logging)

- [Azure Güvenlik Merkezi uyarıları ve öneriler dışa aktarma](../security-center/continuous-export.md) yapılandırması, müşteri herhangi bir günlük bekletme kuramıyor.

- [Müşteri, hizmet sağlayıcılarının etkinlik günlüğü verilerini nasıl gözden geçirebilir?](how-to/view-service-provider-activity.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Hazırlık: Azure için olay yanıtı sürecini güncelleştirin

**Rehberlik**: Kuruluşunuzda güvenlik olaylarına yanıt vermeye yönelik süreçler bulunduğundan, bu süreçlerin Azure için güncelleştirildiğinden ve hazır olduğunuzdan emin olmak için bu süreçlerin düzenli olarak uygulandığından emin olun.

- [Güvenliği kuruluş genelinde uygulama](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Olay yanıtı başvuru kılavuzu](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Hazırlık: Olay bildirimini ayarlayın

**Rehberlik**: Azure Güvenlik Merkezi'nde güvenlik olayı iletişim bilgilerini ayarlayın. Microsoft, Microsoft Güvenlik Yanıt Merkezi'nin (MSRC) verilerinize kanuna aykırı veya yetkisiz erişim sağlanmasını keşfetmesi durumunda sizinle iletişime geçmek için bu iletişim bilgilerini kullanır. İsterseniz farklı Azure hizmetlerindeki olay uyarılarını ve bildirimlerini olay yanıt gereksinimlerinize göre özelleştirebilirsiniz. 

- [Azure Güvenlik Merkezi güvenlik iletişimini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Algılama ve analiz: Yüksek kaliteli uyarıları temel alan olaylar oluşturun

**Rehberlik**: Yüksek kaliteli uyarılar oluşturmaya ve uyarıların kalitesini ölçmeye yönelik bir sürece sahip olduğunuzdan emin olun. Bu sayede önceki olaylardan dersler çıkarabilir ve analistler için uyarıları önceliklendirebilirsiniz. Bu sayede hatalı pozitif sonuçlarla zaman kaybetmelerini önlemiş olursunuz. 

Yüksek kaliteli uyarılar önceki olaylardan alınan dersler, doğrulanmış topluluk kaynakları ve farklı sinyal kaynaklarını birleştirip bağlantı oluşturarak uyarı oluşturmaya ve temizlemeye yönelik araçlar kullanılarak geliştirilebilir. 

Azure Güvenlik Merkezi, birçok Azure varlığı arasında yüksek kaliteli uyarılar sağlar. ASC veri bağlayıcısını kullanarak uyarıların Azure Sentinel’e akışını yapabilirsiniz. Azure Sentinel bir araştırmaya yönelik olayların otomatik olarak oluşturulması için gelişmiş uyarı kuralları oluşturur. 

Azure kaynaklarına yönelik riskleri tanımlamaya yardımcı olmak için, dışarı aktarma özelliğini kullanarak Azure Güvenlik Merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Uyarıları ve önerileri el ile veya sürekli bir biçimde dışarı aktarabilirsiniz.

- [Dışarı aktarma işlemini yapılandırma](../security-center/continuous-export.md)

- [Uyarıların Azure Sentinel’e akışını yapma](../sentinel/connect-azure-security-center.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>OY-4: Algılama ve analiz – olayı araştırma

**Rehber**: Analistlerin olası olayları araştırırken neler olup bittiğini tam olarak görmek için çeşitli veri kaynaklarını sorgulayabildiğinden ve kullanabildiğinden emin olun. Sonlandırma zinciri boyunca olası saldırganın etkinliklerinin izlenebilmesi için çeşitli günlükler toplanmalı, kör noktaların olması önlenmelidir.  Ayrıca diğer analistler için ve gelecekte başvurmak üzere içgörülerin ve çıkarılan derslerin yakalandığından da emin olmalısınız.  

Araştırmaya yönelik veri kaynakları kapsam içindeki hizmetlerden ve çalışan sistemlerden zaten toplanmakta olan merkezi günlük kaynaklarının yanı sıra şunları da içerebilir:

- Ağ verileri – ağ akış günlüklerini ve diğer analiz bilgilerini yakalamak için ağ güvenlik gruplarının akış günlüklerini, Azure Ağ İzleyicisi’ni ve Azure İzleyici’ni kullanın. 

- Çalışan sistemlerin anlık görüntüleri: 

    - Çalışan sistemin diskinin anlık görüntüsünü oluşturmak için Azure sanal makinesinin anlık görüntü özelliğini kullanın. 

    - Çalışan sistemin belleğinin anlık görüntüsünü oluşturmak için işletim sisteminin yerel bellek dökümü özelliğini kullanın.

    - Çalışan sistemlerin anlık görüntülerini oluşturmak için Azure hizmetlerinin anlık görüntü özelliğini veya yazılımınızın kendi özelliğini kullanın.

Azure Sentinel hemen her veri kaynağı üzerinde kapsamlı veri analizi gerçekleştirdiği gibi, olayların tüm yaşam döngüsünü yöneten bir olay yönetim portalı da sağlar. Araştırma sırasında elde edilen inceleme bilgileri izleme ve raporlama amacıyla bir olayla ilişkilendirilebilir. 

- [Windows makinesi diskinin anlık görüntüsü](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux makinesi diskinin anlık görüntüsü](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure Desteği tanılama bilgileri ve bellek dökümü toplama](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel ile olayları araştırma](../sentinel/tutorial-investigate-cases.md)

**Azure Güvenlik Merkezi izlemesi**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Algılama ve analiz: Olayları önceliklendirin

**Rehber**: Uyarı önem derecesi ve uyarı hassasiyeti temelinde analistlere önce hangi olaylara odaklanılacağı konusunda bağlam sağlayın. 

Azure Güvenlik Merkezi önce hangi uyarıların araştırılması gerektiğini belirlemenize yardımcı olmak için her uyarıya bir önem derecesi atar. Önem derecesinde, Güvenlik Merkezi’nin bulguya ne kadar güvendiği veya uyarıyı verirken kullanılan analiz kadar, uyarıya yol açan etkinliğin ardında kötü bir amaç olduğuna ilişkin güvenilirlik düzeyi de temel alınır.

Buna ek olarak Azure kaynaklarını, özellikle hassas verileri işleyen kaynakları belirlemek ve kategorilere ayırmak için etiketleri kullanarak kaynakları işaretleyin ve bir adlandırma sistemi oluşturun.  Azure kaynaklarının önem düzeyine ve olayın oluştuğu ortama bağlı olarak uyarıların çözümünde önceliği belirlemek sizin sorumluluğunuzdadır.

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md)

- [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../azure-resource-manager/management/tag-resources.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Kapsama, ortadan kaldırma ve kurtarma: Olay işleme sürecini otomatikleştirin

**Rehberlik**: Yanıt süresini kısaltmak ve analistlerin yükünü hafifletmek için el ile yapılan yinelenen görevleri otomatikleştirin. El ile gerçekleştirilen görevlerin yürütülmesi daha uzun sürer ve olay yanıt sürecini yavaşlatarak analistlerin işleyebileceği olay sayısını azaltır. Ayrıca el ile gerçekleştirilen görevler analistler açısından daha yorucudur. Dolayısıyla gecikmelere neden olan insan hatası riskini artırır ve analistlerin karmaşık görevlere etkili bir şekilde odaklanma becerisini azaltır. Eylemleri otomatik olarak tetiklemek veya gelen güvenlik uyarılarını yanıtlamak üzere bir playbook çalıştırmak için Azure Güvenlik Merkezi’nin ve Azure Sentinel’in iş akışı otomasyonu özelliklerini kullanın. Playbook bildirimleri gönderme, hesapları devre dışı bırakma ve sorunlu ağları yalıtma gibi eylemleri gerçekleştirir. 

- [Güvenlik Merkezi’nde iş akışı otomasyonunu yapılandırma](../security-center/workflow-automation.md)

- [Azure Güvenlik Merkezi’nde otomatik tehdit yanıtlarını ayarlama](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel’de otomatik tehdit yanıtlarını ayarlama](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="posture-and-vulnerability-management"></a>Duruş ve Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Duruş ve Güvenlik Açığı Yönetimi](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Azure hizmetleri için güvenli yapılandırmalar oluşturun 

**Kılavuz**: Azure Mathouse, Azure kaynaklarınızın yapılandırmasını denetlemek ve zorlamak Için Azure Güvenlik Merkezi 'nde mevcut olan hizmete özgü ilkelerin altına daha fazla destek sağlar. Bu, Azure Güvenlik Merkezi veya Azure Ilke girişimleri içinde yapılandırılabilir.

- Kiracı kimliklerinin Azure Dıthouse aracılığıyla eklenmesine izin ver

- Bir yönetim kiracısına kapsamları yetkilendirmeyi denetleme

Tek bir şema tanımında Azure Resource Manager şablonları, Azure RBAC denetimleri ve ilkeleri de dahil olmak üzere hizmetlerin ve uygulama ortamlarının dağıtımını ve yapılandırmasını otomatik hale getirmek için Azure şemaları ' nı kullanabilirsiniz.

- [Azure açık kullanım Ilkeleri](samples/policy-reference.md)

- [Öğretici-uyumluluğu zorlamak için ilke oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprints](../governance/blueprints/overview.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Azure hizmetleri için güvenli yapılandırmaların sürekliliğini sağlayın

**Kılavuz**: Azure Mathouse, Azure kaynaklarınızın yapılandırmasını denetlemek ve zorlamak Için Azure Güvenlik Merkezi 'nde mevcut olan hizmete özgü ilkelerin altına daha fazla destek sağlar. Bu, Azure Güvenlik Merkezi veya Azure Ilke girişimleri içinde yapılandırılabilir.

- [Azure açık kullanım Ilkeleri](samples/policy-reference.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>BD-3: işlem kaynakları için güvenli yapılandırma oluşturma

**Rehberlik**: VM 'ler, kapsayıcılar ve diğerleri dahil tüm işlem kaynaklarında güvenli konfigürasyonlar oluşturmak Için Azure Güvenlik Merkezi 'Ni ve Azure ilkesini kullanın.

- [Azure Güvenlik Merkezi önerilerini izleme](../security-center/security-center-recommendations.md) 

- [Güvenlik önerileri - başvuru kılavuzu](../security-center/recommendations-reference.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Düzenli aralıklarla saldırı simülasyonları yapın

**Rehberlik**: Gerektiğinde Azure kaynaklarınızda sızma testi veya kırmızı takım etkinlikleri gerçekleştirerek tüm kritik güvenlik bulgularının düzeltilmesini sağlayın.
Sızma testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak için Microsoft Bulut Sızma Testi Etkileşim Kuralları'na uygun hareket edin. Microsoft tarafından yönetilen bulut altyapısına, hizmetlere ve uygulamalara yönelik kırmızı takım ve canlı site sızma testi gerçekleştirmek için Microsoft'un stratejisini ve yürütme sürecini kullanın.

- [Azure'da sızma testi yapma](../security/fundamentals/pen-testing.md)

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşımlı

## <a name="governance-and-strategy"></a>İdare ve Strateji

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: İdare ve Strateji](../security/benchmarks/security-controls-v2-governance-strategy.md).*

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
- [Azure Güvenlik Mimarisi Önerileri: Depolama, veri ve şifreleme](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure Güvenliği Temelleri: Azure'da veri güvenliği, şifreleme ve depolama](../security/fundamentals/encryption-overview.md)

- [Bulut Benimseme Çerçevesi: Azure'da veri güvenliği ve şifreleme için en iyi deneyimler](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Güvenlik Karşılaştırması: Varlık yönetimi](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure Güvenlik Karşılaştırması: Veri koruma](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Kurumsal segmentasyon stratejisini tanımlayın 

**Rehberlik**: Kimlik, ağ, uygulama, abonelik, yönetim grubu ve diğer denetimleri kullanarak varlık erişimi segmentasyonuna yönelik kuruluş genelinde kullanılacak bir strateji belirleyin.

Güvenlik ayrımı gereksinimiyle birbirleriyle iletişim kurma ve verilere erişme gereksinimine sahip olan sistemlerin günlük çalışmasını etkinleştirme gereksinimi arasında bir denge kurun.

Segmentasyon stratejisinin ağ güvenliği, kimlik ve erişim modelleri, uygulama izinleri/erişim modelleri ve insanlar tarafından gerçekleştirilen süreç denetimleri gibi farklı denetim türlerinde tutarlı bir şekilde uygulandığından emin olun.

- [Azure için segmentasyon stratejisi rehberi (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure için segmentasyon stratejisi rehberi (belge)](/security/compass/governance#enterprise-segmentation-strategy)

- [Ağ segmentasyonunu kurumsal segmentasyon stratejisiyle uyumlu hale getirme](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Güvenlik duruşu yönetim stratejisini tanımlayın

**Rehberlik**: Varlıklarınız ve içinde bulundukları ortamla ilgili riskleri sürekli olarak ölçün ve ortadan kaldırın. Yayımlanmış uygulamalar, ağ giriş ve çıkış noktaları, kullanıcı ve yönetici uç noktaları gibi değeri yüksek varlıkları ve kullanıma açık olan saldırı yüzeylerini önceliklendirin.

- [Azure Güvenlik Karşılaştırması: Duruş ve güvenlik açığı yönetimi](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Kuruluş genelindeki rolleri, sorumlulukları ve hesap verilebilirlik durumlarını uyumlu hale getirin

**Rehberlik**: Güvenlik kuruluşunuzdaki roller ve sorumluluklar için net bir strateji oluşturup bunu belgelendirdiğinizden ve paylaştığınızdan emin olun. Güvenlik kararları için net bir hesap verilebilirlik süreci oluşturmayı önceliklendirin, herkesi paylaşılan sorumluluk modeli hakkında eğitin ve teknik ekiplere bulut ortamının güvenliğini sağlamaya yönelik eğitimler verin.

- [Azure Güvenliği En İyi Deneyimi 1 - İnsanlar: Ekipleri Bulut Güvenliği Yolculuğu Hakkında Eğitin](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Güvenliği En İyi Deneyimi 2 - İnsanlar: Ekipleri Bulut Güvenliği Teknolojileri Hakkında Eğitin](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Güvenliği En İyi Deneyimi 3 - Süreç: Bulut Güvenliği Kararları için Hesap Verilebilirlik Ataması Yapın](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Ağ güvenlik stratejisini tanımlayın

**Rehberlik**: Kuruluşunuzun genel güvenlik erişimi denetimi stratejisi kapsamında Azure ağ güvenliği için bir yaklaşım belirleyin.  

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   Merkezi ağ yönetimi ve güvenlik sorumluluğu

-   Kurumsal segmentasyon stratejisiyle uyumlu sanal ağ segmentasyon modeli

-   Farklı tehdit ve saldırı senaryolarına yönelik düzeltme stratejisi

-   Internet Edge ve giriş ve çıkış stratejisi/Azure/Security/benchmarks/Security-Controls-v2-Logging-Threat-Detection
-   Hibrit bulut ve şirket içi bağlantı stratejisi

-   Güncel ağ güvenliği yapıtları (ağ diyagramları, başvuru amaçlı ağ mimarisi vb.)

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:
- [Azure Güvenliği En İyi Deneyimi 11 - Mimari. Tek ve birleşik güvenlik stratejisi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure ile ağ güvenliğine genel bakış](../security/fundamentals/network-overview.md)

- [Kurumsal ağ mimarisi stratejisi](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Kimlik ve ayrıcalıklı erişim stratejisini tanımlayın

**Rehberlik**: Kuruluşunuzun genel güvenlik erişimi denetimi stratejisi kapsamında Azure kimlik ve ayrıcalıklı erişim yaklaşımları belirleyin.  

Bu strateji kapsamında aşağıdaki öğeler için rehberlik, ilkeler ve standartlar belgelenmiş olmalıdır: 

-   Merkezi bir kimlik ve kimlik doğrulaması sistemi ile diğer iç ve dış kimlik sistemleriyle olan bağlantısı

-   Farklı kullanım örneklerine ve koşullara yönelik güçlü kimlik doğrulama yöntemleri

-   Yüksek ayrıcalıklı kullanıcıların korunması

-   Kullanıcı etkinlikleri için anomali izleme ve işleme  

-   Kullanıcı kimliği ve erişim gözden geçirmesi ile uzlaştırma süreci

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Azure Güvenlik Karşılaştırması: Kimlik yönetimi](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Güvenlik Karşılaştırması: Ayrıcalıklı erişim](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure Güvenliği En İyi Deneyimi 11 - Mimari. Tek ve birleşik güvenlik stratejisi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure'da kimlik yönetim güvenliğine genel bakış](../security/fundamentals/identity-management-overview.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

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

- [Azure Güvenlik Karşılaştırması: Günlüğe kaydetme ve tehdit algılama](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Güvenlik Karşılaştırması: Olay yanıtı](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure Güvenliği En İyi Deneyimi 4 - Süreç. Bulut için Olay Yanıt Sürecini Güncelleştirme](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Benimseme Çerçevesi, günlüğe kaydetme ve raporlama kararı rehberi](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure ile kurumsal ölçek, yönetim ve izleme](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](../security/benchmarks/overview.md)
- [Azure güvenlik temelleri](../security/benchmarks/security-baselines-overview.md) hakkında daha fazla bilgi edinin