---
title: Azure HPC önbelleği için Azure Güvenlik temeli
description: Azure HPC önbellek güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e5a395f71ed1b1ab06d97b8aa6d83278d59d15c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091964"
---
# <a name="azure-security-baseline-for-azure-hpc-cache"></a>Azure HPC önbelleği için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 2,0](../security/benchmarks/overview.md) ' dan HPC önbelleğine Microsoft Azure kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure HPC Cache için geçerli olan ilgili kılavuza göre gruplandırılır. Azure HPC önbelleği için geçerli olmayan **denetimler** dışlandı.

Azure HPC önbelleğinin Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Azure HPC Cache Security taban çizgisi eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: iç trafik için güvenlik uygulama

**Kılavuz**: Azure HPC önbellek kaynaklarını dağıtırken, var olan bir sanal ağ oluşturmanız veya kullanmanız gerekir. 

Tüm Azure sanal ağlarının, iş risklerine göre bir kurumsal kesimleme ilkesini izlediğinden emin olun. Kuruluş için daha yüksek risk doğurabilecek tüm sistemleri kendi sanal ağı içinde yalıtılmalı ve bir ağ güvenlik grubu (NSG) ve/veya Azure güvenlik duvarıyla yeterince güvenli hale gelmelidir.

Önbelleğinizi kullanabilmeniz için ağ ile ilgili iki önkoşul ayarlanmalıdır: 

- Azure HPC önbellek örneği için ayrılmış alt ağ

- Önbelleğin depolamaya ve diğer kaynaklara erişebilmesi için DNS desteği

Azure HPC önbelleği, bu kaliteleri olan ayrılmış bir alt ağa ihtiyaç duyuyor: 

- Alt ağda en az 64 IP adresi kullanılabilir olmalıdır.

- Alt ağ, istemci makineler gibi ilgili hizmetler için bile diğer VM 'Leri barındıramaz.

- Birden çok Azure HPC önbellek örneği kullanıyorsanız, her birinin kendi alt ağına ihtiyacı vardır.

En iyi yöntem, her önbellek için yeni bir alt ağ oluşturmaktır. Önbellek oluşturmanın bir parçası olarak yeni bir sanal ağ ve alt ağ oluşturabilirsiniz.

HPC önbelleğini şirket içi NAS depolamayla birlikte kullanmak için, şirket içi ağdaki belirli bağlantı noktalarının Azure HPC önbelleğinin alt ağından sınırsız trafiğe izin verildiğinden emin olmanız gerekir. 

- [NFS depolama erişimi için bağlantı noktalarını yapılandırma](hpc-cache-prerequisites.md#nfs-storage-requirements)

- [Güvenlik kuralları ile ağ güvenlik grubu oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Azure Güvenlik duvarını dağıtma ve yapılandırma](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="ns-2-connect-private-networks-together"></a>NS-2: özel ağları birbirine bağlama

**Rehberlik**: bir birlikte bulundurma ortamında Azure veri merkezleri ile şirket içi altyapı arasında özel bağlantılar oluşturmak Için Azure ExpressRoute veya Azure sanal özel ağ (VPN) kullanın. ExpressRoute bağlantıları, genel İnternet üzerinden geçmez ve tipik İnternet bağlantılarına göre daha fazla güvenilirlik, daha hızlı hız ve daha düşük gecikme süresi sunar. Noktadan siteye VPN ve siteden siteye VPN için, bu VPN seçeneklerinin ve Azure ExpressRoute 'un herhangi bir birleşimini kullanarak şirket içi cihazları veya ağları bir sanal ağa bağlayabilirsiniz. 

İki veya daha fazla sanal ağı Azure 'da birlikte bağlamak için sanal ağ eşlemesi ' ni kullanın. Eşlenen sanal ağlar arasındaki ağ trafiği özeldir ve Azure omurga ağında tutulur.

- [ExpressRoute bağlantı modelleri nelerdir?](../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN 'ye Genel Bakış](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Sanal ağ eşleme](../virtual-network/virtual-network-peering-overview.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Azure hizmetlerine özel ağ erişimi sağlayın

**Rehberlik**: HPC önbelleğine güvenli erişim sağlamak Için Azure sanal ağ hizmet uç noktalarını kullanın. Hizmet uç noktaları, Azure omurga ağı üzerinden İnternet 'ten çıkmadan iyileştirilmiş bir yoldur. 

HPC önbelleği, yönetim uç noktalarının özel bir ağa güvenliğini sağlamak için Azure özel bağlantısının kullanılmasını desteklemez. 

Özel erişim, Azure hizmetleri tarafından sunulan kimlik doğrulama ve trafik güvenliğine ek olarak, ek bir derinlemesine savunma ölçümüdür.

- [Sanal ağ hizmeti uç noktalarını anlama](../virtual-network/virtual-network-service-endpoints-overview.md) 

- [Azure HPC önbelleğini nasıl bağlayacağınızı öğrenin](hpc-cache-mount.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: uygulamaları ve Hizmetleri dış ağ saldırılarına karşı koruma

**Rehberlik**: dağıtılmış hizmet reddi (DDoS) saldırıları, uygulamaya özgü saldırılar ve istenmeyen ve potansiyel olarak kötü amaçlı internet trafiği dahil olmak üzere, HPC önbellek kaynaklarınızı dış ağlardan gelen saldırılara karşı koruyun. 

Azure, bu koruma için yerel özellikleri içerir: 

- Uygulamaları ve Hizmetleri Internet 'ten ve diğer dış konumlardan gelen kötü amaçlı olabilecek trafiğe karşı korumak için Azure Güvenlik Duvarı 'nı kullanın. 
- Azure sanal ağlarınızda DDoS standart korumasını etkinleştirerek varlıklarınızı DDoS saldırılarına karşı koruyun. 
- Ağ kaynaklarınızla ilgili yanlış yapılandırma risklerini algılamak için Azure Güvenlik Merkezi 'ni kullanın.

Azure HPC Cache, Web uygulamalarını çalıştırmak üzere tasarlanmamıştır ve Web uygulamalarını hedef alan dış ağ saldırılarına karşı korumak için herhangi bir ek ağ hizmeti yapılandırmanıza veya ek ağ hizmetleri dağıtmanıza gerek yoktur.

- [Azure Güvenlik Duvarı belgeleri](../firewall/index.yml) 

- [Azure portal kullanarak Azure DDoS koruması standardını yönetme](../ddos-protection/manage-ddos-protection.md) 

- [Azure Güvenlik Merkezi önerileri](../security-center/recommendations-reference.md#recs-networking)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: izinsiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: bilinen kötü amaçlı IP adreslerine ve etki alanlarına gelen ve giden trafiği engellemek ve/veya bu kaynaklardan gelen trafiği engellemek için tehdit zekası tabanlı filtreleme Ile Azure Güvenlik Duvarı 'nı kullanın. IP adresleri ve etki alanları, Microsoft Tehdit Analizi akışından alınır. 

Yük incelemesi gerektiğinde, Azure Marketi 'nden yük İnceleme özelliklerine sahip bir üçüncü taraf yetkisiz giriş algılama/izinsiz erişim engelleme/yetkisiz çözüm yükleyebilirsiniz. Alternatif olarak, ağ tabanlı KIMLIKLER/IP 'ler yerine veya ile birlikte, ana bilgisayar tabanlı KIMLIKLERI/IP 'leri ya da ana bilgisayar tabanlı bir uç nokta algılama ve yanıt (EDR) çözümünü kullanmayı tercih edebilirsiniz.

Dikkat: KIMLIKLER/IP 'ler için bir yasal düzenleme veya başka gereksinimleriniz varsa, SıEM çözümünüz için yüksek kaliteli uyarılar sağlamak üzere her zaman ayarlanmış olduğundan emin olun.

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Market üçüncü taraf KIMLIKLERI özellikleri](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Microsoft Defender ATP EDR özelliği](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: ağ güvenlik kurallarını basitleştirme

**Rehberlik**: uygulanamaz; Bu öneri, Azure sanal ağlarına dağıtılabilecek veya verimli yönetim için izin verilen IP aralıklarının gruplamalarını tanımlama yeteneğine sahip olan tekliflere yöneliktir. HPC Cache Şu anda hizmet etiketlerini desteklemiyor. 

En iyi yöntem, her önbellek için yeni bir alt ağ oluşturmaktır. Önbellek oluşturmanın bir parçası olarak yeni bir sanal ağ ve alt ağ oluşturabilirsiniz.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: güvenli etki alanı adı hizmeti (DNS)

**Rehberlik**: DNS güvenliği için en iyi yöntemleri izleyerek DNS, DNS yükseltme SALDıRıLARı, DNS kirletme ve yanıltma ve diğer kişiler gibi yaygın saldırıları azaltır.

Azure HPC Cache 'in, önbelleğin özel sanal ağının dışındaki kaynaklara erişmesi için DNS ihtiyacı vardır. İş akışınız Azure dışındaki kaynakları içeriyorsa, Azure DNS kullanmaya ek olarak kendi DNS sunucunuzu ayarlamanız ve güvence altına almanız gerekir.

- Azure Blob depolama uç noktalarına, Azure tabanlı istemci makinelerine veya diğer Azure kaynaklarına erişmek için Azure DNS kullanın.
- Şirket içi depolamaya erişmek veya Azure dışındaki istemcilerden gelen önbelleğe bağlanmak için, bu konak adlarını çözebilecek özel bir DNS sunucusu oluşturmanız gerekir.
- İş akışınız hem iç hem de dış kaynak içeriyorsa, Azure 'a özgü çözümleme isteklerini Azure DNS sunucusuna iletmek için özel DNS sunucunuzu ayarlayın. 

Yetkili DNS hizmetiniz olarak Azure DNS kullanıldığında, DNS bölgelerinin ve kayıtlarının Azure RBAC ve kaynak kilitleri kullanılarak yanlışlıkla veya kötü amaçlı değişimlerden korunduğundan emin olun.

Kendi DNS sunucunuzu yapılandırıyorsanız, bu güvenlik yönergelerini izlediğinizden emin olun:

- [Güvenli etki alanı adı sistemi (DNS) dağıtım kılavuzu](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [DNS girişlerinin tehlikini önleyin ve alt etki alanı devralmayı önleyin](../security/fundamentals/subdomain-takeover.md) 

- [HPC önbelleği için DNS erişim gereksinimleri hakkında daha fazla bilgi edinin](hpc-cache-prerequisites.md#dns-access)

- [Azure DNS genel bakış](../dns/dns-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="identity-management"></a>Kimlik Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Kimlik Yönetimi](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory'yi standart merkezi kimlik ve kimlik doğrulaması sistemi haline getirin

**Kılavuz**: Azure HPC Cache, iç işlemler için Azure Active Directory ile tümleştirilmiştir. Ancak, Azure AD, HPC önbellek dağıtımlarını ve ilgili bileşenleri oluşturmak, görüntülemek ve yönetmek için Azure portal veya CLı içindeki kullanıcıların kimliğini doğrulamak için kullanılabilir.

Azure Active Directory (Azure AD), Azure 'daki varsayılan kimlik ve erişim yönetimi hizmetidir. Kuruluşunuzun kimlik ve erişim yönetimini yönetmek için Azure AD 'yi standartlaşmanız gerekir:

- Azure portal, Azure depolama, Azure sanal makinesi (Linux ve Windows), Azure Key Vault, PaaS ve SaaS uygulamaları gibi Microsoft Bulut kaynaklar.

- Kuruluşunuzun kaynakları. Örneğin Azure’daki uygulamalar veya kurumsal ağ kaynaklarınız.

Azure AD'nin güvenliğini sağlamak, kuruluşunuzun güvenlik uygulamalarının yüksek öncelikli bileşenlerinden biri olmalıdır. Azure AD, kimlik güvenliği duruşunu Microsoft'un en iyi deneyim önerilerine göre değerlendirmenize yardımcı olmak için bir kimlik güvenliği puanı sağlar. Bu puanı kullanarak yapılandırmanızın en iyi deneyim önerileriyle ne kadar uyumlu olduğunu görebilir ve güvenlik duruşunuzda geliştirmeler yapabilirsiniz.

Note: Azure AD, Microsoft hesabı olmayan kullanıcıların kendi uygulamalarına ve kaynaklarına kendi dış kimlik bilgileriyle oturum açmasına olanak tanıyan dış kimliği destekler.

- [Azure Active Directory'deki kiracılar](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Bir uygulama için dış kimlik sağlayıcılarını kullanma](../active-directory/external-identities/identity-providers.md) 

- [Azure Active Directory'deki kimlik güvenliği puanı nedir?](../active-directory/fundamentals/identity-secure-score.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Uygulama kimliklerini güvenli ve otomatik bir şekilde yönetin

**Rehberlik**: HPC önbelleği, hizmetler veya Otomasyon gibi insan dışı hesaplar için Azure tarafından yönetilen kimlikleri kullanır. Kaynaklarınıza erişmek veya onları yürütmek üzere daha güçlü bir insan hesabı oluşturmak yerine Azure 'un yönetilen kimlik özelliğinin kullanılması önerilir. 

HPC önbelleği, önceden tanımlanmış erişim verme kuralları aracılığıyla Azure AD kimlik doğrulamasını destekleyen Azure Hizmetleri/kaynakları için yerel olarak kimlik doğrulaması yapabilir. Bu, kaynak kodunda veya yapılandırma dosyalarında sabit kodlu kimlik bilgileri kullanılması gereksinimini ortadan kaldırır.

- [Azure tarafından yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) 

- [Azure kaynakları için yönetilen kimlikleri destekleyen hizmetler](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Uygulama erişimi için Azure AD çoklu oturum açma (SSO) özelliğini kullanın

**Kılavuz**: Azure HPC önbelleği, iç Işlemler IÇIN Azure AD ile tümleştirilemez. Ancak, Azure AD, HPC önbellek dağıtımlarını ve ilgili bileşenleri oluşturmak, görüntülemek ve yönetmek için Azure portal veya CLı içindeki kullanıcıların kimliğini doğrulamak için kullanılabilir.

Azure Active Directory Azure kaynaklarına, bulut uygulamalarına ve şirket içi uygulamalara kimlik ve erişim yönetimi sağlar. Buna çalışanlar gibi kuruluş kimliklerinin yanı sıra iş ortakları, satıcılar ve tedarikçiler gibi dış kimlikler de dahildir. Bu seçenek, kuruluşunuzun şirket içi ortamda ve bulutta bulunan verilerini ve kaynaklarını yönetip bunlara güvenli erişim sağlamak için çoklu oturum açma (SSO) özelliğinin kullanılmasını sağlar. Tüm kullanıcılarınızı, uygulamalarınızı ve cihazlarınızı Azure AD'ye bağlayarak sorunsuz ve güvenli erişimin yanı sıra daha fazla görünürlük ve denetim elde edebilirsiniz.

- [Azure AD ile uygulamalar için SSO yaklaşımını anlama](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Tüm Azure Active Directory tabanlı erişim girişimleri için güçlü kimlik doğrulaması denetimleri kullanın

**Kılavuz**: Azure HPC önbelleği iç Işlemler IÇIN Azure AD ile tümleştirilemez, ancak Azure AD, HPC önbellek dağıtımlarını ve ilgili bileşenleri oluşturmak, görüntülemek ve yönetmek için Azure Portal veya CLI içindeki kullanıcıların kimliğini doğrulamak için kullanılabilir.  

Azure AD, Multi-Factor Authentication (MFA) ve güçlü parolasız yöntemler aracılığıyla güçlü kimlik doğrulama denetimlerini destekler.

- Multi-Factor Authentication: Azure AD MFA 'yı etkinleştirin ve MFA kuruluminizdeki bazı en iyi uygulamalar için Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini izleyin. MFA, tüm kullanıcılar, kullanıcılar veya oturum açma koşulları ve risk faktörleri temelinde Kullanıcı başına düzeyinde zorlanabilir.
- Parolasız kimlik doğrulaması: Üç farklı parolasız kimlik doğrulaması seçeneği vardır: İş İçin Windows Hello, Microsoft Authenticator uygulaması ve akıllı kart gibi şirket içi kimlik doğrulama yöntemleri.

Yönetici ve ayrıcalıklı kullanıcılar için güçlü kimlik doğrulama yönteminin en üst düzeyini kullandığınızdan emin olun. Uygun güçlü kimlik doğrulama ilkesini diğer kullanıcılara alın.

Azure HPC Cache Ayrıca, önbelleğe bağlanan istemci sistemleri için eski parola tabanlı kimlik doğrulamasını destekler. Bu tür bağlantılar, şirket içi parola ilkelerini izleyecek bir temel parola ilkesi veya karma hesapları (Şirket içi Active Directory gelen Kullanıcı hesapları) içeren yalnızca bulut hesapları (doğrudan Azure 'da oluşturulan kullanıcı hesapları) içerir. 

Azure AD, parola tabanlı kimlik doğrulaması kullanırken kullanıcıların tahmin edilmesi kolay parolalar ayarlamamasını engelleyen bir parola koruma yeteneği sağlar. Microsoft, telemetri temelinde güncellenen yasaklanmış parolaların küresel bir listesini sağlar ve müşteriler bu listeyi gereksinimlerine göre artırabilir (örneğin, marka, kültürel başvuruları vb.). Bu parola koruması, yalnızca bulut ve karma hesaplar için kullanılabilir.

Note: tek başına parola kimlik bilgileri tabanlı kimlik doğrulaması popüler saldırı yöntemlerine açıktır. Daha yüksek güvenlik için MFA ve güçlü bir parola ilkesi gibi güçlü kimlik doğrulaması kullanın. Varsayılan parolalara sahip üçüncü taraf uygulamaları ve Market hizmetlerini kullanıyorsanız, hizmeti ilk kez ayarlarken yeni, güvenli bir parola ayarlayın. 

- [Azure'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Active Directory için parolasız kimlik doğrulaması seçeneklerine giriş](../active-directory/authentication/concept-authentication-passwordless.md)

- [Azure AD varsayılan parola ilkesi](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Azure AD Parola Koruması kullanarak hatalı parolaları ortadan kaldırma](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Hesap anomalilerini izleyin ve uyarı oluşturun

**Kılavuz**: Azure HPC önbelleği, Azure Portal Kullanıcı yönetimi için Azure Active Directory kullanabilir.  Azure Active Directory aşağıdaki veri kaynaklarını sağlar:

- Oturum açma bilgileri - Oturum açma bilgileri raporu yönetilen uygulamaların kullanımı ve kullanıcıların oturum açma etkinlikleri hakkında bilgi sağlar.

- Denetim günlükleri - Azure AD içindeki çeşitli özellikler tarafından yapılan tüm değişiklikler için günlükler aracılığıyla izlenebilirlik sağlar. Azure AD içindeki herhangi bir kaynakta yapılan kullanıcı, uygulama, grup, rol ve ilkeleri ekleme veya kaldırma işlemleri gibi değişiklikler, denetim günlüklerine örnek gösterilebilir.

- Riskli oturum açma işlemleri - Riskli oturum açma işlemi bir kullanıcı hesabının meşru sahibi olmayan bir kişi tarafından gerçekleştirilmiş olabilecek oturum açma girişiminin göstergesidir.

- Riskli oldukları belirlenen kullanıcılar - Riskli kullanıcı, güvenliği tehlikeye girmiş olabilecek bir kullanıcı hesabının göstergesidir.

Bu veri kaynakları Azure Izleyici, Azure Sentinel veya üçüncü taraf SıEM sistemleriyle tümleştirilebilir.

Azure Güvenlik Merkezi, çok fazla sayıda başarısız kimlik doğrulama girişimi ve abonelikteki kullanım dışı hesaplar gibi bazı şüpheli etkinlikler üzerinde de uyarı verebilir.

Azure Gelişmiş Tehdit Koruması (ATP) gelişmiş tehditleri, risk altındaki kimlikleri ve içeriden kötü amaçlı eylemleri belirlemek, algılamak ve araştırmak için Active Directory sinyallerini kullanabilen bir güvenlik çözümüdür.

- [Azure Active Directory içindeki denetim etkinliği raporları](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md) 

- [Riskli etkinlik bayrağıyla işaretlenen Azure AD kullanıcılarını belirleme](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure Güvenlik Merkezi’nde kullanıcıların kimliğini ve erişim etkinliğini izleme](../security-center/security-center-identity-access.md)

 

- [Azure Güvenlik Merkezi’nin tehdit analizi koruma modülündeki uyarılar](../security-center/alerts-reference.md) 

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Paylaşılan

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Kimlik bilgilerinin istenmeden açığa çıkma olasılığını ortadan kaldırın

**Rehberlik**: uygulanamaz; HPC önbelleği, müşterilerin kalıcı verileri çalışan ortama dağıtılmasına izin vermez.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="privileged-access"></a>Ayrıcalıklı Erişim

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ayrıcalıklı Erişim](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: İş açısından kritik sistemlere yönetici erişimini kısıtlayın

**Rehberlik**: HPC önbelleği, hangi hesapların içinde bulundukları aboneliklere ve yönetim gruplarına ayrıcalıklı erişim verildiğini kısıtlayarak iş açısından kritik sistemlere erişimi yalıtmak IÇIN Azure RBAC kullanır.

Adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturun. Bir önbellek oluşturmak için HPC Cache, kullanıcıların NIC 'ler oluşturmak üzere abonelikte yeterli ayrıcalıklara sahip olmasını gerektirir. BLOB depolama kullanılıyorsa, HPC önbelleğinin depolamaya erişmesi için Azure rolleri depolama hesabı katılımcısı ve Depolama Blobu veri katılımcısı gerekir. 

Active Directory Etki Alanı Denetleyicileri (DC 'Ler), güvenlik araçları ve iş açısından kritik sistemlerde yüklü aracılarla sistem yönetimi araçları gibi iş açısından kritik varlıklarınıza yönetici erişimi olan yönetim, kimlik ve güvenlik sistemlerine erişimi de kısıtlatığınızdan emin olun. Bu yönetim ve güvenlik sistemlerinden ödün veren saldırganlar, iş açısından kritik varlıkların güvenliğini tehlikeye atabilir.

Tutarlı erişim denetimi sağlamak için, tüm erişim denetimi türleri kurumsal segmentasyon stratejinize hizalanmalıdır.

- [Azure HPC Cache RBAC izinleri](hpc-cache-prerequisites.md#permissions)

- [Azure bileşenleri ve başvuru modeli](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Yönetim grubu erişimi](../governance/management-groups/overview.md#management-group-access)

- [Azure abonelik yöneticileri](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Kullanıcı erişimini düzenli olarak gözden geçirin ve mutabık kılın

**Rehberlik**: hesapların ve erişim düzeylerinin geçerli olduğundan emin olmak için Kullanıcı hesaplarını ve erişim atamasını düzenli olarak gözden geçirin. 

Azure HPC önbelleği, Azure portal ve ilgili arabirimler aracılığıyla Kullanıcı erişimini yönetmek için Azure Active Directory (Azure AD) hesaplarını kullanabilir. Azure AD, grup üyeliklerini incelemenizi, kurumsal uygulamalara erişiminizi ve rol atamalarına yardımcı olan erişim İncelemeleri sunmaktadır. Azure AD raporlama, eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlayabilir. Gözden geçirme sürecini kolaylaştırmak için, erişim gözden geçirme raporu iş akışı oluşturmak için Azure AD Privileged Identity Management de kullanabilirsiniz.

Ayrıca, Azure Privileged Identity Management, çok fazla sayıda yönetici hesabı oluşturulduğunda ve eski veya yanlış yapılandırılmış yönetici hesaplarını belirlemek için de kullanılabilir.

Not: bazı Azure Hizmetleri, Azure AD aracılığıyla yönetilmeyen yerel kullanıcıları ve rolleri destekler. Bu kullanıcıları ayrı ayrı yönetmeniz gerekir.

NFS depolama hedeflerini kullanırken, erişim ayarlarını doğrulamak ve Azure HPC önbelleğinin NFS depolama sistemleriyle iletişim kurabildiğinden emin olmak için ağ yöneticilerinizle ve güvenlik duvarı yöneticileriyle çalışmanız gerekir.

- [HPC önbellek izinlerinin listesi için Azure HPC önbellek önkoşullarını okuyun](hpc-cache-prerequisites.md) 

- [Privileged Identity Management (PıM) içinde Azure Kaynak rolleri için erişim gözden geçirmesi oluşturma](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Azure AD kimlik ve erişim gözden geçirmelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Azure AD'de acil durum erişimini ayarlayın

**Rehberlik**: HPC önbelleği, Azure Portal üzerinden kaynak erişimini yönetmek için Azure Active Directory kullanabilir. Yanlışlıkla Azure AD kuruluşunuzun dışına kilitlenmesini engellemek için, normal yönetim hesapları kullanılmazsa erişim için bir acil durum erişim hesabı ayarlayın. Acil durum erişim hesapları genellikle yüksek ayrıcalığa sahiptir ve herhangi bir kişiye atanmamalıdır. Acil durum erişim hesaplarının kullanım alanı, normal yönetici hesaplarının kullanılamadığı acil veya "camı kırın" senaryolarıyla sınırlıdır.

Acil durum erişim hesaplarının kimlik bilgilerinin (parola, sertifika veya akıllı kart) güvenli bir şekilde saklandığından ve yalnızca acil bir durumda bunları kullanma yetkisine sahip olan kullanıcılar tarafından bilindiğinden emin olmanız gerekir.

- [Azure AD'deki acil durum erişim hesaplarını yönetme](../active-directory/roles/security-emergency-access.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="pa-5-automate-entitlement-management"></a>PA-5: yetkilendirme yönetimini otomatikleştirme 

**Rehberlik**: HPC önbelleği, Azure Portal üzerinden önbellek kaynaklarına erişimi yönetmek için Azure Active Directory kullanabilir. 

Erişim atamaları, incelemeler ve süre sonu dahil olmak üzere, erişim isteği iş akışlarını otomatikleştirmek için Azure AD yetkilendirme yönetimi özelliklerini kullanın. Çift veya çok aşamalı onay de desteklenir. 

- [Azure AD erişim gözden geçirmeleri nelerdir?](../active-directory/governance/access-reviews-overview.md) 

- [Azure AD yetkilendirme yönetimi nedir?](../active-directory/governance/entitlement-management-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>AE-7: Tam yetecek kadar yönetim uygulama (en düşük ayrıcalık ilkesi) 

**Rehberlik**: HPC önbelleği, kaynaklarını yönetmek için Azure rol tabanlı erişim denetımı (RBAC) ile tümleşiktir. Azure RBAC ağ atamaları aracılığıyla Azure kaynak erişimini yönetmenize olanak tanır. Bu rolleri kullanıcılara atayabilir, hizmet sorumlularını ve yönetilen kimlikleri gruplara atayabilirsiniz. Belirli kaynaklar için önceden tanımlanmış yerleşik roller vardır ve Azure CLI, Azure PowerShell veya Azure portal gibi araçlardan yararlanarak bu rollerin envanteri çıkarılabilir ve bunlar sorgulanabilir. 

Azure RBAC aracılığıyla kaynaklara atadığınız ayrıcalıkların her zaman roller için gerekli olan ile sınırlı olması gerekir. Bu, Azure AD Privileged Identity Management (PıM) ' nin tam zamanında (JıT) yaklaşımını tamamlar ve düzenli olarak incelenmelidir.

Yerleşik rolleri kullanarak izin verin ve yalnızca gerektiğinde özel rol oluşturun.

- [Azure rol tabanlı erişim denetimi nedir (Azure RBAC)](../role-based-access-control/overview.md) 

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md) 

- [Azure AD kimlik ve erişim gözden geçirmelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: hassas verileri bulma, sınıflandırma ve etiketleme 

**Rehberlik**: HPC Cache hassas verileri yönetir ancak hassas verileri bulma, sınıflandırma ve etiketleme yeteneğine sahip değildir.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Hassas verileri koruyun

**Rehberlik**: Azure rol tabanlı Access Control (Azure RBAC), ağ tabanlı erişim denetimleri ve Azure hizmetlerindeki belirli DENETIMLERI (SQL ve diğer veritabanlarında şifreleme gibi) kullanarak erişimi kısıtlayarak hassas verileri koruyun.

Tutarlı erişim denetimi sağlamak için tüm erişim denetimi türlerinin kurumsal segmentasyon stratejinizle uyumlu olması gerekir. Ayrıca kurumsal segmentasyon stratejisi, hassas ve iş açısından kritik verilerle sistemlerin konumu hakkında da bilgilendirilmelidir.

Microsoft tarafından yönetilen temel platform açısından, Microsoft tüm müşteri içeriğini hassas olarak değerlendirir ve müşteri verilerinin kaybına ve açığa çıkmasına karşı koruma sağlar. Azure içindeki müşteri verilerinin güvenlik altında kalmasını sağlamak için Microsoft bazı varsayılan veri koruma denetimleri ve özellikleri uygulamıştır.

- [Azure Rol Tabanlı Erişim Denetimi (RBAC)](../role-based-access-control/overview.md) 

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Azure Güvenlik Merkezi izlemesi**: Uygulanamaz

**Sorumluluk**: Paylaşılan

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Yetkisiz hassas veri aktarımını izleyin

**Rehberlik**: HPC Cache hassas verileri iletir ancak hassas verilerin yetkisiz aktarımını izlemeyi desteklemez.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Hassas bilgileri aktarım sırasında şifreleyin

**Rehberlik**: HPC Cache, TLS v 1.2 veya üzerini kullanarak geçiş sırasında veri şifrelemesini destekler.

Bu, özel ağlardaki trafik için isteğe bağlı olsa da, bu, dış ve genel ağlardaki trafik için önemlidir. HTTP trafiği için, Azure kaynaklarınıza bağlanan istemcilerin TLS v 1.2 veya üzerini anlaştığınızdan emin olun. Uzaktan Yönetim için şifrelenmemiş bir protokol yerine SSH (Linux için) veya RDP/TLS (Windows için) kullanın. Kullanımdan çıkarıldı SSL, TLS ve SSH sürümleri ve protokolleri ve zayıf şifrelemeler devre dışı bırakılmalıdır.

Azure, varsayılan olarak Azure veri merkezleri arasında aktarım halindeki veriler için şifreleme sağlar.

- [Azure ile iletim sırasında şifrelemeyi anlama](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [TLS güvenliği hakkında bilgi](/security/engineering/solving-tls1-problem) 

- [Yoldaki Azure verileri için çift şifreleme](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Paylaşılan

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Bekleyen hassas verileri şifreleyin

**Kılavuz**: erişim denetimlerini tamamlamak için REST 'teki veriler, şifreleme kullanılarak "bant dışı" saldırılarına karşı korunmalıdır (örneğin, temeldeki depolamaya erişme). Bu, saldırganların verileri kolayca okuyabilmesini veya değiştirememesini sağlamaya yardımcı olur.

Azure, varsayılan olarak bekleyen şifreleme verileri sağlar. Son derece hassas veriler için, kullanılabilir olduğunda tüm Azure kaynaklarında bekleyen ek şifreleme uygulama seçenekleriniz vardır. Azure, şifreleme anahtarlarınızı varsayılan olarak yönetir, ancak Azure belirli Azure hizmetleri için kendi anahtarlarınızı (müşteri tarafından yönetilen anahtarlar) yönetme seçenekleri sunar.

Azure 'da depolanan tüm veriler, önbellek disklerinde de dahil olmak üzere varsayılan olarak Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir. Yalnızca verilerinizi şifrelemek için kullanılan anahtarları yönetmek istiyorsanız Azure HPC önbellek ayarlarını özelleştirmeniz gerekir.

İşlem kaynaklarında uyumluluk için gerekliyse, verileri bir sistemden kopyalandıklarında bile verilere erişim denetimlerine zorlamak için otomatik ana bilgisayar tabanlı veri kaybı önleme çözümü gibi bir üçüncü taraf aracı uygulayın.

- [Azure HPC Cache ile müşteri tarafından yönetilen şifreleme anahtarlarını kullanma](./hpc-cache-create.md?tabs=azure-portal#enable-azure-key-vault-encryption-optional)

- [Azure’da bekleyen veri şifrelemesini anlama](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Müşteri tarafından yönetilen şifreleme anahtarlarını yapılandırma](../storage/common/customer-managed-keys-configure-key-vault.md) 

- [Şifreleme modeli ve anahtar yönetimi tablosu](../security/fundamentals/encryption-atrest.md)

 

- [Azure 'da Rest çift Şifrelemeli veriler](../security/fundamentals/double-encryption.md#data-at-rest)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

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

**Kılavuz**: Azure HPC Cache etiketlerin kullanılmasını destekler. Bunları bir taksonomiye mantıksal olarak düzenlemek için Azure kaynaklarınıza, kaynak gruplarına ve aboneliklerinize Etiketler uygulayın. Her etiket bir ad ve bir değer çiftinden oluşur. 

Örneğin, "Ortam" adını ve "Üretim" değerini üretimdeki tüm kaynaklara uygulayabilirsiniz. Bir önbellek ve önbellek dağıtıldıktan sonra da Etiketler eklenebilir. 

Sanal makinelerde yazılım hakkında bilgi toplamayı otomatik hale getirmek için Azure sanal makine envanterini kullanın. Yazılım adı, sürüm, yayımcı ve yenileme süresi Azure portal kullanılabilir. Yüklemeyi tarih ve diğer bilgilere erişim sağlamak için, Konuk düzeyinde tanılamayı etkinleştirin ve Windows olay günlüklerini Log Analytics çalışma alanına getirin.
HPC önbelleği, kaynak üzerinde bir uygulamanın veya yazılımın yüklemesinin çalıştırılmasına izin vermiyor. 

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md) 

- [Azure Güvenlik Merkezi varlık envanteri yönetimi](../security-center/asset-inventory.md) 

- [Kaynak adlandırma ve etiketleme kararı kılavuzu](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json) 

- [Azure sanal makine envanterini etkinleştirme](../automation/automation-tutorial-installed-software.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Yalnızca onaylı Azure hizmetlerini kullanın

**Rehberlik**: HPC önbelleği Azure Resource Manager dağıtımlarını destekler. Kullanıcılarınızın ortamınızda sağlayabileceği hizmetleri denetlemek ve kısıtlamak için Azure İlkesi'ni kullanın. Abonelikler içindeki kaynakları sorgulamak ve bulmak için Azure Kaynak Grafı'nı kullanın. Ayrıca Azure İzleyici'yi kullanarak onaylanmamış hizmetler algılandığında uyarı tetikleme amacıyla kurallar oluşturabilirsiniz.

- [Azure Ilkesini yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](../governance/policy/samples/index.md) 

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>VY-4: Varlık yaşam döngüsü yönetiminin güvenliğini sağlama

**Rehberlik**: uygulanamaz. Azure HPC önbelleği, bir yaşam döngüsü yönetimi işlemindeki varlıkların güvenliğini sağlamak için kullanılamaz. Bu, yüksek etki olarak kabul edilen varlıkların özniteliklerinin ve ağ yapılandırmalarının korunması için müşterinin sorumluluğundadır. 

Müşterinin özniteliği ve ağ yapılandırması değişikliklerini yakalamak, değişiklik etkisini ölçmek ve uygun şekilde düzeltme görevleri oluşturmak için bir işlem oluşturması önerilir.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="logging-and-threat-detection"></a>Günlüğe Kaydetme ve Tehdit Algılama

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Günlüğe Kaydetme ve Tehdit Algılama](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure kaynakları için tehdit algılamayı etkinleştirme

**Kılavuz**: Azure Güvenlik Merkezi 'nin yerleşik tehdit algılama özelliğini kullanın ve HPC önbellek kaynaklarınız Için Azure Defender 'ı (resmi olarak Azure Gelişmiş tehdit koruması) etkinleştirin. HPC için Azure Defender önbelleği, önbellek kaynaklarınıza erişmek veya bu kaynaklara yararlanmak için olağandışı ve potansiyel olarak zararlı girişimleri algılayan ek bir güvenlik bilgileri katmanı sağlar.

HPC önbelleğinden tüm günlükleri, özel tehdit algılamalarını ayarlamak için kullanılabilen SıEM 'nize iletin. Olası tehditler ve bozukluklar için farklı türlerde Azure varlıklarını izlemediğinizden emin olun. Analistlerin sıralama için yanlış pozitif durumları azaltmaya yönelik yüksek kaliteli uyarılar almaya odaklanın. Uyarılar, günlük verilerinden, aracılardan veya diğer verilerden kaynaklıdır.

- [Azure Güvenlik Merkezi’nde tehdit koruması](../security-center/azure-defender.md) 

- [Azure Güvenlik Merkezi güvenlik uyarıları başvuru kılavuzu](../security-center/alerts-reference.md) 

- [Tehditleri algılamak için özel analiz kuralları oluşturma](../sentinel/tutorial-detect-threats-custom.md) 

- [Azure Sentinel ile Cyber Threat Intelligence](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure kimlik ve erişim yönetimi için tehdit algılamayı etkinleştirin

**Kılavuz**: Azure AD, Azure AD raporlama 'da görüntülenebilen veya Azure Izleyici, Azure Sentinel veya diğer SIEM/izleme araçlarıyla tümleştirilen, daha gelişmiş izleme ve analiz kullanım durumları için kullanılabilen aşağıdaki kullanıcı günlüklerini sağlar:
- Oturum açma bilgileri – Oturum açma bilgileri raporu, yönetilen uygulamaların kullanımı ve kullanıcı oturum açma etkinlikleri hakkında bilgi sağlar.

- Denetim günlükleri-Azure AD içindeki çeşitli özelliklerle yapılan tüm değişiklikler için günlüklere yönelik izlenebilirlik sağlar. Denetim günlüklerine örnek olarak, Azure AD 'de Kullanıcı, uygulama, Grup, rol ve ilke ekleme veya kaldırma gibi her türlü kaynak üzerinde yapılan değişiklikler verilebilir.

- Riskli oturum açma işlemleri - Riskli oturum açma işlemi bir kullanıcı hesabının meşru sahibi olmayan bir kişi tarafından gerçekleştirilmiş olabilecek oturum açma girişiminin göstergesidir.

- Riskli oldukları belirlenen kullanıcılar - Riskli kullanıcı, güvenliği tehlikeye girmiş olabilecek bir kullanıcı hesabının göstergesidir.

Azure Güvenlik Merkezi, çok fazla sayıda başarısız kimlik doğrulama girişimi veya abonelikte kullanım dışı bırakılmış hesaplar gibi bazı şüpheli etkinlikler üzerinde de uyarı verebilir. Temel güvenlik durumunu izlemeye ek olarak, Azure Güvenlik Merkezi’nin Tehdit Koruması modülü tek tek Azure işlem kaynaklarından (sanal makineler, kapsayıcılar, uygulama hizmeti), veri kaynaklarından (SQL DB ve depolama) ve Azure hizmet katmanlarından daha kapsamlı güvenlik uyarıları da toplayabilir. Bu özellik, bireysel kaynakların içindeki hesap bozukluklarını görünürlüğe sahip etmenize olanak tanır.

- [Azure Active Directory içindeki denetim etkinliği raporları](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Kimlik Koruması’nı etkinleştirme](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure Güvenlik Merkezi’nde tehdit koruması](../security-center/azure-defender.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Azure ağ etkinlikleri için günlüğe kaydetmeyi etkinleştirin

**Rehberlik**: VPN ağ geçitlerini ve paket yakalama yeteneklerini, sanal ağlarınız arasında seyahat edilen ağ paketlerini kaydetmek için yaygın olarak kullanılabilir paket yakalama araçlarına ek olarak kullanabilirsiniz.

Azure HPC önbellek kaynaklarınızın dağıtıldığı ağ üzerinde bir ağ güvenlik grubu dağıtın. Trafik denetimi için ağ güvenlik gruplarında ağ güvenlik grubu akış günlüklerini etkinleştirin.

Akış günlüklerinizin bir depolama hesabında tutulur. Trafik Analizi çözümünü, bu akış günlüklerini işlemek ve Log Analytics çalışma alanına göndermek için etkinleştirin. Trafik Analizi, Azure ağlarınız için trafik akışına ek öngörüler sağlar. Trafik Analizi, ağ etkinliğini görselleştirmenize, etkin noktaları belirlemenize, güvenlik tehditlerini belirlemenize, trafik akışı düzenlerini anlamanıza ve ağ yapılandırmalarını belirlemenize yardımcı olabilir.

Önbelleğin, sanal ağının dışındaki kaynaklara erişmesi için DNS ihtiyacı vardır. Kullandığınız kaynaklara bağlı olarak, özelleştirilmiş bir DNS sunucusu ayarlamanız ve bu sunucu ile Azure DNS sunucuları arasında iletmeyi yapılandırmanız gerekebilir. 

Kuruluşunuzun ihtiyaç duyduğu şekilde, DNS günlüğü çözümü için Azure Marketi 'nden bir üçüncü taraf çözümü uygulayın.

- [VPN ağ geçitleri için paket yakalamaları yapılandırma](../vpn-gateway/packet-capture.md) 

- [Ağ güvenlik grubu akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md) 

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md) 

- [DNS önkoşulları hakkında daha fazla bilgi edinin](hpc-cache-prerequisites.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure kaynakları için günlüğe kaydetmeyi etkinleştirin

**Kılavuz**: Azure HPC önbellek kaynakları otomatik olarak etkinlik günlükleri oluşturur. Bu Günlükler tüm yazma işlemlerini (PUT, POST, DELETE) içerir, ancak okuma işlemlerini (GET) içermez. Etkinlik günlükleri, sorun giderirken bir hata bulmak veya kuruluşunuzdaki bir kullanıcının bir kaynağı nasıl değiştirdiğinizi izlemek için kullanılabilir.

Ayrıca, Azure Güvenlik Merkezi 'ni ve Azure Ilkesini kullanarak HPC önbelleği için Azure Kaynak günlüklerini etkinleştirebilir ve veri toplamayı günlüğe kaydedebilir. Bu Günlükler, daha sonra güvenlik olaylarını araştırmak ve bu uygulamaların gerçekleştirilmesi için kritik öneme sahip olabilir.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure 'da günlüğe kaydetme ve farklı günlük türlerini anlama](../azure-monitor/essentials/platform-logs-overview.md) 

- [Azure Güvenlik Merkezi veri toplamayı anlama](../security-center/security-center-enable-data-collection.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Paylaşılan

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Güvenlik günlüğü yönetim ve analiz süreçlerini merkezileştirin

**Rehberlik**: bağıntı sağlamak için günlük depolamayı ve çözümlemeyi merkezileştirme. Her günlük kaynağı için bir veri sahibi, erişim Kılavuzu, depolama konumu, verileri işlemek ve erişmek için kullanılan araçları ve veri saklama gereksinimlerini atamış olduğunuzdan emin olun.

Azure etkinlik günlüklerini merkezi günlüklerinizi tümleştirdiğinizden emin olun. Endpoint cihazları, ağ kaynakları ve diğer güvenlik sistemleri tarafından oluşturulan güvenlik verilerini toplamak için Azure Izleyici aracılığıyla günlük alma. Azure Izleyici 'de analiz sorgulamak ve gerçekleştirmek için Log Analytics çalışma alanlarını kullanın ve uzun süreli ve arşiv depolama için Azure depolama hesaplarını kullanın.

Ayrıca, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'e verileri etkinleştirin ve ekleyin.

Birçok kuruluş, daha az sıklıkta kullanılan "soğuk" verileri için sık kullanılan ve Azure depolama için Azure Sentinel ' i "etkin" veriler için kullanmayı seçer.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Hazırlık: Azure için olay yanıtı sürecini güncelleştirin

**Rehberlik**: Kuruluşunuzda güvenlik olaylarına yanıt vermeye yönelik süreçler bulunduğundan, bu süreçlerin Azure için güncelleştirildiğinden ve hazır olduğunuzdan emin olmak için bu süreçlerin düzenli olarak uygulandığından emin olun.

- [Güvenliği kuruluş genelinde uygulama](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Olay yanıtı başvuru kılavuzu](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="ir-2-preparation--set-up-incident-notification"></a>IR-2: hazırlık – olay bildirimini ayarlama 

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

Azure Güvenlik Merkezi önce hangi uyarıların araştırılması gerektiğini belirlemenize yardımcı olmak için her uyarıya bir önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için bulma veya analiz etme sırasında ne kadar önemli olduğunu, hatta uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin güven düzeyini temel alır.

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

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>BD-3: işlem kaynakları için güvenli yapılandırma oluşturma

**Rehberlik**: VM 'ler, kapsayıcılar ve diğerleri dahil tüm işlem kaynaklarında güvenli konfigürasyonlar oluşturmak Için Azure Güvenlik Merkezi 'Ni ve Azure ilkesini kullanın.

- [Azure Güvenlik Merkezi önerilerini izleme](../security-center/security-center-recommendations.md) 

- [Güvenlik önerileri - başvuru kılavuzu](../security-center/recommendations-reference.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Düzenli aralıklarla saldırı simülasyonları yapın

**Rehberlik**: Gerektiğinde Azure kaynaklarınızda sızma testi veya kırmızı takım etkinlikleri gerçekleştirerek tüm kritik güvenlik bulgularının düzeltilmesini sağlayın.
Sızma testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak için Microsoft Bulut Sızma Testi Etkileşim Kuralları'na uygun hareket edin. Microsoft tarafından yönetilen bulut altyapısına, hizmetlere ve uygulamalara yönelik kırmızı takım ve canlı site sızma testi gerçekleştirmek için Microsoft'un stratejisini ve yürütme sürecini kullanın.

- [Azure'da sızma testi yapma](../security/fundamentals/pen-testing.md)

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

## <a name="backup-and-recovery"></a>Yedekleme ve Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Yedekleme ve Kurtarma](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: düzenli otomatik yedeklemeler sağlayın

**Kılavuz**: Azure HPC Cache bir depolama sistemi değil bir önbelleğe alma çözümü olduğundan, depolama hedeflerine ait verilerin düzenli olarak yedeklenmesini sağlamaya odaklanın. Azure Blob kapsayıcıları ve şirket içi depolama hedeflerini yedeklemek için standart yordamları izleyin. 

Bölgesel bir kesinti durumunda kesintiyi en aza indirmek için, bölgeler arası veri erişimi sağlamak için gerekli adımları izleyebilirsiniz.  

Her Azure HPC Cache örneği belirli bir abonelik içinde ve tek bir bölgede çalışır. Bu, bölgenin tam bir kesinti olması durumunda önbellek iş akışınızın kesintiye uğramaması anlamına gelir. Bu kesintiyi en aza indirmek için kuruluş, birden çok bölgeden erişilebilen arka uç depolamayı kullanmalıdır. Bu depolama alanı, uygun DNS desteğiyle şirket içi bir NAS sistemi ya da önbellekten farklı bir bölgede bulunan Azure Blob depolama alanı olabilir.

İş akışınız birincil bölgenize devam ettikçe, veriler bölge dışında uzun vadeli depolamaya kaydedilir. Önbellek bölgesi kullanılamaz hale gelirse, ikincil bölgede yinelenen bir Azure HPC önbellek örneği oluşturabilir, aynı depolamaya bağlanabilir ve yeni önbellekten çalışmayı sürdürebilirsiniz.

- [Bölgesel yük devretme hakkında daha fazla bilgi edinin](hpc-region-recovery.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="br-2-encrypt-backup-data"></a>BR-2: yedekleme verilerini şifreleme

**Rehberlik**: yedeklemelerinizin saldırılara karşı korunduğundan emin olun. Bu, gizlilik kaybına karşı korunmak için yedeklemelerin şifrelenmesini içermelidir.

Azure Backup kullanarak şirket içi yedekleme için, sağladığınız parola kullanılarak bekleyen şifreleme sağlanır. Normal Azure hizmeti yedeklemesi için, yedekleme verileri Azure platformu tarafından yönetilen anahtarlar kullanılarak otomatik olarak şifrelenir. Yedeklemeyi, müşteri tarafından yönetilen anahtarları kullanarak şifrelemeyi seçebilirsiniz. Bu durumda, anahtar kasasındaki bu müşterinin yönettiği anahtarın de yedekleme kapsamında olduğundan emin olun.

Azure HPC önbelleği, önbellek diskleri için bir müşteri anahtarı eklemeseniz bile, önbelleğe alınmış verilerinizi tutan yönetilen disklerde VM ana bilgisayar şifrelemesi ile de korunur. Çift şifreleme için müşteri tarafından yönetilen bir anahtar eklemek, yüksek güvenlik gereksinimlerine sahip müşteriler için ek bir güvenlik düzeyi sağlar. Ayrıntılar için Azure disk depolaması 'nın sunucu tarafı şifrelemesini okuyun.

Yedeklemeleri ve müşteri tarafından yönetilen anahtarları korumak için Azure Backup, Azure Key Vault veya diğer kaynaklarda rol tabanlı erişim denetimi kullanın. Ayrıca, yedeklemelerin değiştirilemeyeceği veya silinebilmesi için gelişmiş güvenlik özelliklerinin MFA gerektirmesini sağlayabilirsiniz.

- [VM konak şifrelemesi](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

- [Azure disk depolama 'nın sunucu tarafı şifrelemesi](../virtual-machines/disk-encryption.md)

- [Azure Backup güvenlik özelliklerine genel bakış](../backup/security-overview.md) 

- [Müşteri tarafından yönetilen anahtarları kullanarak yedekleme verilerini şifreleme](../backup/encryption-at-rest-with-cmk.md)  

- [Azure 'da Key Vault anahtarlarını yedekleme](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-5.1.0)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Müşteri tarafından yönetilen anahtarlar dahil olmak üzere tüm yedeklemeleri doğrulayın

**Rehberlik**: düzenli olarak, yedeklenen müşteri tarafından yönetilen anahtarları geri yüklemenize emin olun.

- [Azure 'da Key Vault anahtarlarını geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-5.1.0)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Kayıp anahtar riskini azaltma

**Rehberlik**: anahtar kaybını engellemek ve kurtarmak için ölçülerde yer aldığından emin olun. Anahtarları yanlışlıkla veya kötü amaçlı olarak silinmeye karşı korumak için Azure Key Vault'ta geçici silme ve temizleme koruması özelliklerini etkinleştirin.

- [Key Vault'ta geçici silme ve temizleme koruması özelliklerini etkinleştirme](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

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

- [Azure Güvenlik Karşılaştırması: Varlık yönetimi](../security/benchmarks/security-controls-v2-asset-management.md)

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

-   İnternet uç düğümü ile giriş ve çıkış stratejisi

-   Hibrit bulut ve şirket içi bağlantı stratejisi

-   Güncel ağ güvenliği yapıtları (ağ diyagramları, başvuru amaçlı ağ mimarisi vb.)

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:
- [Azure Güvenliği En İyi Deneyimi 11 - Mimari. Tek ve birleşik güvenlik stratejisi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-controls-v2-network-security.md)

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