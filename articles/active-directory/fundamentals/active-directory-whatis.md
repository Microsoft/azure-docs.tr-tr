---
title: Azure Active Directory nedir? -Azure Active Directory | Microsoft Docs
description: Terminoloji, kullanılabilir lisanslar ve daha fazla bilgi için bağlantılarla ilişkili özelliklerin bir listesi de dahil olmak üzere Azure Active Directory hakkında genel bakış ve kavramsal bilgiler.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.date: 06/05/2020
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b429182a4fd68fb03cea6b783e57cf30066f105
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063449"
---
# <a name="what-is-azure-active-directory"></a>Azure Active Directory nedir?

Azure Active Directory (Azure AD), Microsoft 'un bulut tabanlı kimlik ve erişim yönetimi hizmeti olduğundan, çalışanlarınızın ' de oturum açmasını ve kaynaklara erişmesini sağlar.

- Microsoft 365, Azure portal ve binlerce diğer SaaS uygulaması gibi dış kaynaklar.

- Şirket ağınızdaki ve intranetinizdeki uygulamalar gibi iç kaynaklar, kendi kuruluşunuz tarafından geliştirilen tüm bulut uygulamalarıyla birlikte. Kuruluşunuz için kiracı oluşturma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure Active Directory yeni kiracı oluşturma](active-directory-access-create-new-tenant.md).

Azure AD ve Active Directory Domain Services arasındaki farkı öğrenmek için bkz. [Azure Active Directory Active Directory karşılaştırma](active-directory-compare-azure-ad-to-ad.md). Ayrıca, Azure, Azure AD ve Microsoft 365 temel kimlik hizmetlerini daha iyi anlamak için [kuruluş mimarları serisi](/microsoft-365/solutions/cloud-architecture-models) posterleri için çeşitli Microsoft bulut de kullanabilirsiniz.

## <a name="who-uses-azure-ad"></a>Azure AD'yi kimler kullanır?

Azure AD şunları yöneliktir:

- **BT yöneticileri.** BT Yöneticisi olarak, iş gereksinimlerinize göre uygulamalarınıza ve uygulama kaynaklarınıza erişimi denetlemek için Azure AD ' yi kullanabilirsiniz. Örneğin, Azure AD 'yi, önemli kuruluş kaynaklarına erişirken Multi-Factor Authentication istemek için kullanabilirsiniz. Ayrıca, Microsoft 365 dahil olmak üzere mevcut Windows Server AD ve bulut uygulamalarınız arasında kullanıcı sağlamayı otomatik hale getirmek için Azure AD 'yi de kullanabilirsiniz. Son olarak, Azure AD, Kullanıcı kimliklerini ve kimlik bilgilerini korumaya ve erişim idare gereksinimlerinizi karşılayacak şekilde otomatik olarak yardımcı olacak güçlü araçlar sağlar. Başlamak için [30 günlük ücretsiz Azure Active Directory Premium deneme sürümü](https://azure.microsoft.com/trial/get-started-active-directory/)için kaydolun.

- **Uygulama geliştiricileri.** Uygulama geliştiricisi olarak, Azure AD 'yi uygulamanıza çoklu oturum açma (SSO) eklemek için standartlara dayalı bir yaklaşım olarak kullanabilirsiniz, böylece kullanıcının önceden mevcut kimlik bilgileriyle çalışmasına izin verebilirsiniz. Azure AD, mevcut kurumsal verileri kullanarak kişiselleştirilmiş uygulama deneyimleri oluşturmanıza yardımcı olabilecek API 'Ler de sağlar. Başlamak için [30 günlük ücretsiz Azure Active Directory Premium deneme sürümü](https://azure.microsoft.com/trial/get-started-active-directory/)için kaydolun. Daha fazla bilgi için [Azure Active Directory geliştiriciler için](../develop/index.yml)de bkz..

- **Microsoft 365, Office 365, Azure veya Dynamics CRM Online aboneleri.** Abone olarak, Azure AD 'yi zaten kullanıyorsunuz. Her Microsoft 365, Office 365, Azure ve Dynamics CRM Online kiracısı aynı zamanda bir Azure AD kiracısıdır. Tümleşik bulut uygulamalarınıza erişimi hemen yönetmeye başlayabilirsiniz.

## <a name="what-are-the-azure-ad-licenses"></a>Azure AD lisansları nelerdir?

Microsoft 365 veya Microsoft Azure gibi Microsoft Online iş hizmetleri, oturum açma için Azure AD ve kimlik korumasıyla ilgili yardım sağlar. Herhangi bir Microsoft Online iş hizmetine abone değilseniz, tüm ücretsiz özelliklere erişimi olan Azure AD 'yi otomatik olarak alırsınız.

Azure AD uygulamanızı geliştirmek için, Azure Active Directory Premium P1 veya Premium P2 lisanslarına yükselterek ücretli yetenekler de ekleyebilirsiniz. Azure AD ücretli lisanslar, mobil kullanıcılarınız için self servis, Gelişmiş izleme, güvenlik raporlaması ve güvenli erişim sağlayan mevcut ücretsiz dizininizin üzerine kurulmuştur.

>[!Note]
>Bu lisansların fiyatlandırma seçenekleri için bkz. [Azure Active Directory fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory/).
>
>Azure Active Directory Premium P1 ve Premium P2 Şu anda Çin 'de desteklenmemektedir. Azure AD fiyatlandırması hakkında daha fazla bilgi için [Azure Active Directory forumuna](https://azure.microsoft.com/support/community/?product=active-directory)başvurun.

- **Azure Active Directory Ücretsiz.** Kullanıcı ve Grup Yönetimi, şirket içi dizin eşitleme, temel raporlar, bulut kullanıcıları için self servis parola değişikliği ve Azure, Microsoft 365 ve birçok popüler SaaS uygulamasında çoklu oturum açma sağlar.

- **Azure Active Directory Premium P1.** P1, ücretsiz özelliklere ek olarak, karma kullanıcılarınızın hem şirket içi hem de bulut kaynaklarına erişmesine imkan tanır. Ayrıca, dinamik gruplar, Self Servis Grup Yönetimi, Microsoft Identity Manager (Şirket içi kimlik ve erişim yönetimi paketi) ve şirket içi kullanıcılarınız için self servis parola sıfırlamaya izin veren bulut geri yazma özellikleri gibi gelişmiş yönetimi de destekler.

- **Azure Active Directory Premium P2.** Ücretsiz ve P1 özelliklerine ek olarak, P2, uygulamalarınıza ve önemli şirket verilerinize yönelik risk tabanlı koşullu erişim sağlamaya yardımcı olmak için [Azure Active Directory kimlik koruması](../identity-protection/overview-identity-protection.md) , yöneticilerin ve kaynaklara erişimlerini bulma, kısıtlama ve izleme konusunda yardımcı olmak ve gerektiğinde tam zamanında erişim sağlamak için [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) sunar.

- **"Kullandıkça öde" özelliği lisansları.** Azure Active Directory Işletmeden müşteriye (B2C) gibi ek özellik lisansları da alabilirsiniz. B2C, müşterilere yönelik uygulamalarınız için kimlik ve erişim yönetimi çözümleri sağlamanıza yardımcı olabilir. Daha fazla bilgi için bkz. [Azure Active Directory B2C belgeleri](../../active-directory-b2c/index.yml).

Azure aboneliğini Azure AD 'ye ilişkilendirme hakkında daha fazla bilgi için bkz. [Azure Active Directory Için Azure aboneliği ilişkilendirme veya ekleme](active-directory-how-subscriptions-associated-directory.md) ve kullanıcılarınıza lisans atama hakkında daha fazla bilgi için bkz. [nasıl yapılır: Azure Active Directory lisansları atama veya kaldırma](license-users-groups.md).

## <a name="which-features-work-in-azure-ad"></a>Azure AD 'de hangi özellikler çalışıyor?

Azure AD lisansınızı seçtikten sonra, kuruluşunuz için aşağıdaki özelliklerden bazılarına veya tümüne erişebilirsiniz:

|Kategori|Açıklama|
|-------|-----------|
|Uygulama yönetimi|Uygulama proxy 'Si, çoklu oturum açma, Uygulamalarım portalı (erişim paneli olarak da bilinir) ve hizmet olarak yazılım (SaaS) uygulamaları kullanarak bulutunuzu ve şirket içi uygulamalarınızı yönetin. Daha fazla bilgi için bkz. Şirket [içi uygulamalara güvenli uzaktan erişim sağlama](../manage-apps/application-proxy.md) ve [uygulama yönetimi belgeleri](../manage-apps/index.yml).|
|Kimlik Doğrulaması|Azure Active Directory self servis parola sıfırlama, Multi-Factor Authentication, özel yasaklanmış parola listesi ve akıllı kilitleme 'yi yönetin. Daha fazla bilgi için bkz. [Azure AD kimlik doğrulaması belgeleri](../authentication/index.yml).|
|Geliştiriciler için Azure Active Directory|Tüm Microsoft kimliklerinde oturum açma, Microsoft Graph, diğer Microsoft API 'Leri veya özel API 'Leri çağırmak için belirteçleri alan uygulamalar oluşturun. Daha fazla bilgi için bkz. [Microsoft Identity platform (geliştiriciler için Azure Active Directory)](../develop/index.yml).|
|İşletmeden İşletmeye (B2B)|Konuk kullanıcılarınızı ve dış iş ortaklarınızı yönetin ve kendi şirket verileriniz üzerinde denetimi koruyun. Daha fazla bilgi için bkz. [Azure ACTIVE DIRECTORY B2B belgeleri](../external-identities/index.yml).|
|İşletmeden Müşteriye (B2C)|Uygulamalarınızı kullanırken kullanıcıların nasıl kaydolma, oturum açma ve profillerini yönetme şeklini özelleştirin ve denetleyin. Daha fazla bilgi için bkz. [Azure Active Directory B2C belgeleri](../../active-directory-b2c/index.yml).|
|Koşullu Erişim|Bulut uygulamalarınıza erişimi yönetin. Daha fazla bilgi için bkz. [Azure AD koşullu erişim belgeleri](../conditional-access/index.yml).|
|Aygıt Yönetimi|Buluttaki veya şirket içindeki cihazlarınızın kurumsal verilerinize erişimini yönetin. Daha fazla bilgi için bkz. [Azure AD cihaz yönetimi belgeleri](../devices/index.yml).|
|Etki alanı hizmetleri|Azure sanal makinelerini etki alanı denetleyicileri kullanmadan bir etki alanına ekleyin. Daha fazla bilgi için bkz. [Azure AD Domain Services belgeleri](../../active-directory-domain-services/index.yml).|
|Kurumsal kullanıcılar|Grupları ve yönetici rollerini kullanarak lisans atamasını yönetin, uygulamalara erişin ve temsilcileri ayarlayın. Daha fazla bilgi için bkz. [Kullanıcı yönetimi belgelerini Azure Active Directory](../enterprise-users/index.yml).|
|Karma kimlik|Konumdan (bulut veya şirket içi) bağımsız olarak tüm kaynaklarda kimlik doğrulaması ve yetkilendirme için tek bir kullanıcı kimliği sağlamak üzere Azure Active Directory Connect ve Connect Health kullanın. Daha fazla bilgi için bkz. [karma kimlik belgeleri](../hybrid/index.yml).|
|Kimlik idaresi|Kuruluşunuzun kimliğini çalışan, iş ortağı, satıcı, hizmet ve uygulama erişim denetimleri aracılığıyla yönetin. Ayrıca, erişim gözden geçirmeleri gerçekleştirebilirsiniz. Daha fazla bilgi için bkz. [Azure AD Identity idare belgeleri](../governance/identity-governance-overview.md) ve [Azure AD erişim İncelemeleri](../governance/access-reviews-overview.md).|
|Kimlik koruması|Kuruluşunuzun kimliklerini etkileyen olası güvenlik açıklarını tespit edin, şüpheli eylemlere yanıt vermek için ilkeleri yapılandırın ve ardından bunları çözmek için uygun işlemleri gerçekleştirin. Daha fazla bilgi için bkz. [Azure AD kimlik koruması](../identity-protection/index.yml).|
|Azure kaynakları için yönetilen kimlikler|Azure AD 'de, Key Vault dahil olmak üzere Azure AD tarafından desteklenen herhangi bir kimlik doğrulama hizmetinin kimliğini doğrulayabilecek otomatik olarak yönetilen bir kimlikle Azure hizmetlerinizi sağlar. Daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../managed-identities-azure-resources/overview.md).|
|Privileged identity management (PIM)|Kuruluşunuzun içindeki erişimi yönetin, denetleyin ve izleyin. Bu özellik Azure AD ve Azure 'daki kaynaklara ve Microsoft 365 veya Intune gibi diğer Microsoft çevrimiçi hizmetlerine erişimi içerir. Daha fazla bilgi için bkz. [Azure AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Raporlar ve izleme|Ortamınızdaki güvenlik ve kullanım desenleriyle ilgili Öngörüler elde edin. Daha fazla bilgi için bkz. [Azure Active Directory raporları ve izleme](../reports-monitoring/index.yml).|

## <a name="terminology"></a>Terminoloji

Azure AD ve belgelerini daha iyi anlamak için aşağıdaki koşulları gözden geçiririz.

|Terim veya kavram|Açıklama|
|---------------|-----------|
|Kimlik| Kimliği doğrulanmış bir şey. Kimlik, Kullanıcı adı ve parola içeren bir kullanıcı olabilir. Kimlikler Ayrıca gizli anahtarlar veya sertifikalar aracılığıyla kimlik doğrulaması gerektirebilecek uygulamaları veya diğer sunucuları da içerir.|
|Hesap| Kendisiyle ilişkili verilerin bulunduğu bir kimlik. Kimliği olmayan bir hesabınız olamaz.|
|Azure AD hesabı| Azure AD veya başka bir Microsoft bulut hizmeti (Microsoft 365 gibi) ile oluşturulmuş bir kimlik. Kimlikler Azure AD 'de depolanır ve kuruluşunuzun bulut hizmeti aboneliklerinde erişilebilir. Bu hesaba bazen Iş veya okul hesabı da denir.|
|Hesap Yöneticisi|Bu klasik abonelik Yöneticisi rolü, kavramsal olarak bir aboneliğin fatura sahibidir. Bu rolün [Azure Hesap Merkezi](https://account.azure.com/Subscriptions) erişimi vardır ve bir hesaptaki tüm abonelikleri yönetmenizi sağlar. Daha fazla bilgi için bkz. [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD yönetici rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Hizmet Yöneticisi|Bu klasik abonelik Yöneticisi rolü, erişim dahil olmak üzere tüm Azure kaynaklarını yönetmenizi sağlar. Bu rol, abonelik kapsamında sahip rolü atanan bir kullanıcıya eşdeğer erişime sahiptir. Daha fazla bilgi için bkz. [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD yönetici rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Sahip|Bu rol, erişim de dahil olmak üzere tüm Azure kaynaklarını yönetmenize yardımcı olur. Bu rol, Azure kaynaklarına ayrıntılı erişim yönetimi sağlayan Azure rol tabanlı erişim denetimi (Azure RBAC) adlı daha yeni bir yetkilendirme sistemi üzerine kurulmuştur. Daha fazla bilgi için bkz. [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD yönetici rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Azure AD Genel yöneticisi|Bu yönetici rolü, Azure AD kiracısı tarafından oluşturulan çoğuna 'e otomatik olarak atanır. Genel Yöneticiler, Azure AD 'ye yönelik tüm yönetim işlevlerini ve Exchange Online, SharePoint Online ve Skype Kurumsal Çevrimiçi sürümü gibi Azure AD 'ye federe Hizmetleri gerçekleştirebilir. Birden çok genel yöneticisi olabilir, ancak yalnızca genel Yöneticiler yönetici rollerini (diğer genel yöneticileri atama dahil) kullanıcılara atayabilirler. Çeşitli yönetici rolleri hakkında daha fazla bilgi için [Azure Active Directory Içindeki yönetici rolü izinleri](../roles/permissions-reference.md)bölümüne bakın.|
|Azure aboneliği| Azure bulut hizmetleri için ödeme yapmak üzere kullanılır. Birçok aboneliğiniz olabilir ve bunlar kredi kartıyla bağlantılıdır.|
|Azure kiracısı| Kuruluşunuz, Microsoft Azure, Microsoft Intune veya Microsoft 365 gibi bir Microsoft bulut hizmeti aboneliğine kaydolduğunda otomatik olarak oluşturulan adanmış ve güvenilir bir Azure AD örneğidir. Azure kiracısı tek kuruluşu temsil eder.|
|Tek kiracılı| Ayrılmış bir ortamdaki diğer hizmetlere erişen Azure kiracılar tek kiracı olarak kabul edilir.|
|Çok kiracılı| Birden çok kuruluşta paylaşılan bir ortamdaki diğer hizmetlere erişen Azure kiracılar çok kiracılı olarak değerlendirilir.|
|Azure AD dizini|Her Azure kiracının adanmış ve güvenilen bir Azure AD dizini vardır. Azure AD dizini, kiracının kullanıcılarını, gruplarını ve uygulamalarını içerir ve kiracı kaynakları için kimlik ve erişim yönetimi işlevleri gerçekleştirmek üzere kullanılır.|
|Özel etki alanı|Her yeni Azure AD dizini, domainname.onmicrosoft.com ilk etki alanı adı ile gelir. Bu başlangıç adının yanı sıra, iş için kullandığınız adları ve kullanıcılarınızın kuruluşunuzun kaynaklarına erişmek için kullandığı adları içeren kuruluşunuzun etki alanı adlarını da ekleyebilirsiniz. Özel etki alanı adları eklemek, kullanıcılarınıza bildiğiniz Kullanıcı adlarını (örneğin,) oluşturmanıza yardımcı olur alain@contoso.com .|
|Microsoft hesabı (da denir, MSA)|Müşteri odaklı Microsoft ürünlerine ve Outlook, OneDrive, Xbox LIVE veya Microsoft 365 gibi bulut hizmetlerine erişim sağlayan kişisel hesaplar. Microsoft hesabı, Microsoft tarafından çalıştırılan Microsoft tüketici kimliği hesap sisteminde oluşturulur ve depolanır.|

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory Premium'a kaydolma](active-directory-get-started-premium.md)

- [Azure aboneliğini Azure Active Directory ilişkilendir](active-directory-how-subscriptions-associated-directory.md)

- [Azure Active Directory Premium P2 özelliği dağıtım denetim listesi](active-directory-deployment-checklist-p2.md)
