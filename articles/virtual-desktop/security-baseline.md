---
title: Windows sanal masaüstü için Azure Güvenlik temeli
description: Windows sanal masaüstü güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 393495dabb77a5d177c97d37313433bb00ce5a36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726759"
---
# <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Windows sanal masaüstü için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürüm 2,0](../security/benchmarks/overview.md) ' den Windows Sanal Masaüstü ' ne kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Windows sanal masaüstü için geçerli olan ilgili kılavuza göre gruplandırılır. Windows sanal masaüstü için geçerli olmayan **denetimler** dışlandı.

Windows sanal masaüstü hizmeti, hizmetin kendisini, çoklu oturum sanal SKU 'su için Windows 10 Enterprise ve FSLogix ' i içerir. FSLogix ile ilgili güvenlik önerileri için bkz. [depolama için güvenlik taban çizgisi](../storage/common/security-baseline.md). Hizmetin sanal makinelerde çalışan bir Aracısı vardır ancak sanal makineler müşterinin tam denetimi altında olduğundan, [işlem için güvenlik önerilerini](../virtual-machines/windows/security-baseline.md) izleyin

Windows sanal masaüstü 'Nün Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Windows sanal masaüstü güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: iç trafik için güvenlik uygulama

**Rehberlik**: Windows sanal masaüstüne kaydedilecek sanal makineleri dağıtırken var olan bir sanal ağı oluşturmanız veya kullanmanız gerekir. Tüm Azure sanal ağlarının, iş risklerine göre bir kurumsal kesimleme ilkesini izlediğinden emin olun. Kuruluş için daha yüksek risk doğurabilecek tüm sistemleri kendi sanal ağı içinde yalıtılmalıdır ve bir ağ güvenlik grubu veya Azure güvenlik duvarıyla yeterince güvenli hale getirilir.

Bağlantı noktalarını ve kaynak IP 'Leri dış ağ trafiği kurallarına göre sınırlayan ağ güvenlik grubu yapılandırmalarının kullanılması için Azure Güvenlik Merkezi 'ndeki Uyarlamalı ağ sağlamlaştırma özelliklerini kullanın.

Uygulamalarınız ve kurumsal segmentasyon stratejinize bağlı olarak, ağ güvenlik grubu kurallarına göre iç kaynaklar arasındaki trafiği kısıtlayın veya buna izin verin. Belirli bir iyi tanımlanmış uygulamalarda (3 katmanlı bir uygulama gibi), bu, yüksek güvenlikli bir "varsayılan olarak Reddet, özel duruma izin ver" yaklaşımına sahip olabilir. Birbirleriyle etkileşim kuran çok sayıda uygulamanız ve uç noktalarınız varsa, bu durum düzgün ölçeklenmeyebilir. Azure Güvenlik Duvarı 'nı, çok sayıda kurumsal bölüt veya tekerlek (hub/bağlı bileşen topolojisinde) üzerinde merkezi yönetim gerektiren koşullarda da kullanabilirsiniz

Sanal makineniz (Windows sanal masaüstü 'nün parçası olan) alt ağlarının bulunduğu ağ güvenlik grupları için, giden trafiğe belirli uç noktalara izin vermeniz gerekir. 

- [Hangi URL 'Lerin Windows Sanal Masaüstü erişimine izin verileceğini öğrenin](safe-url-list.md)

- [Azure Güvenlik Merkezi 'nde Uyarlamalı ağ sağlamlaştırma](../security-center/security-center-adaptive-network-hardening.md) 

- [Windows sanal masaüstü için Azure Güvenlik Duvarı ](../firewall/protect-windows-virtual-desktop.md)

- [Güvenlik kuralları ile ağ güvenlik grubu oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Azure Güvenlik duvarını dağıtma ve yapılandırma](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ns-2-connect-private-networks-together"></a>NS-2: özel ağları birbirine bağlama

**Rehberlik**: bir birlikte bulundurma ortamında Azure veri merkezleri ile şirket içi altyapı arasında özel bağlantılar oluşturmak Için Azure ExpressRoute veya Azure sanal özel ağını kullanın. ExpressRoute bağlantıları, genel İnternet üzerinden geçmez, tipik İnternet bağlantılarına göre daha fazla güvenilirlik, daha hızlı hız ve daha düşük gecikme süresi sunar. 

Noktadan siteye ve siteden siteye sanal özel ağlar için, sanal özel ağ seçenekleri ve Azure ExpressRoute birleşimini kullanarak şirket içi cihazları veya ağları bir sanal ağa bağlayabilirsiniz.

Azure 'da iki veya daha fazla sanal ağı birbirine bağlamak için sanal ağ eşlemesi kullanın. Eşlenen sanal ağlar arasındaki ağ trafiği özeldir ve Azure omurga ağında kalır.

- [ExpressRoute bağlantı modelleri nelerdir?](../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN 'ye Genel Bakış](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Sanal ağ eşleme](../virtual-network/virtual-network-peering-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: uygulamaları ve Hizmetleri dış ağ saldırılarına karşı koruma

**Rehberlik**: uygulamaları ve Hizmetleri Internet 'ten ve diğer dış konumlardan gelen kötü amaçlı olabilecek trafiğe karşı korumak Için Azure Güvenlik Duvarı 'nı kullanın. Dağıtılmış hizmet reddi saldırıları, uygulamaya özgü saldırılar, istenmeyen ve potansiyel olarak kötü amaçlı internet trafiği de dahil olmak üzere, Windows sanal masaüstü kaynaklarınızı dış ağlardaki saldırılara karşı koruyun. Azure sanal ağlarınızda DDoS standart korumasını etkinleştirerek varlıklarınızı dağıtılmış hizmet reddi saldırılarına karşı koruyun. Ağınızla ilgili kaynaklarla ilgili yanlış yapılandırma risklerini algılamak için Azure Güvenlik Merkezi 'ni kullanın.

Windows sanal masaüstü, Web uygulamalarını çalıştırmak üzere tasarlanmamıştır ve Web uygulamalarını hedefleyen dış ağ saldırılarına karşı korumak için ek herhangi bir ağ hizmeti yapılandırmanız veya ek ağ hizmetleri dağıtmanız gerekmez.

- [Azure Güvenlik Duvarı belgeleri](../firewall/index.yml)

- [Azure portal kullanarak Azure DDoS koruması standardını yönetme](../ddos-protection/manage-ddos-protection.md) 

- [Azure Güvenlik Merkezi önerileri](../security-center/recommendations-reference.md#networking-recommendations)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: izinsiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Kılavuz**: Azure Güvenlik Duvarı 'nı kullanarak, uyarı almak ve isteğe bağlı olarak bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen trafiği engellemek için tehdit zeka tabanlı filtreleme ile IP adresleri ve etki alanları, Microsoft Tehdit Analizi akışından alınır. Yük incelemesi gerektiğinde, Azure Marketi 'nden üçüncü taraf bir izinsiz giriş algılama veya önleme çözümü dağıtabilirsiniz. 

İzinsiz giriş algılama veya çözüm kullanımını önleme konusunda bir düzenleme veya başka gereksinim varsa, güvenlik bilgileriniz ve olay yönetimi (SıEM) çözümünüze yüksek kaliteli uyarılar sağlamak için her zaman ayarlanmış olduğundan emin olun.

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketi 3. taraf KIMLIĞI özellikleri içerir](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Microsoft Defender ATP EDR özelliği](/bs-cyrl-ba/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: ağ güvenlik kurallarını basitleştirme

**Rehberlik**: ağ güvenlik gruplarında veya Windows sanal masaüstü kaynaklarınız için yapılandırılmış bir Azure Güvenlik duvarında ağ erişim denetimleri tanımlamak Için Azure sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adı (örneğin, WindowsVirtualDesktop) belirterek, karşılık gelen hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

- [Hizmet etiketlerini anlama ve kullanma](../virtual-network/service-tags-overview.md)

- [Burada Windows sanal masaüstü hizmeti etiketi kapsamında olan özellikler hakkında daha fazla bilgi edinin ](safe-url-list.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="identity-management"></a>Kimlik Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Kimlik Yönetimi](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory'yi standart merkezi kimlik ve kimlik doğrulaması sistemi haline getirin

**Rehberlik**: Windows sanal masaüstü, varsayılan kimlik ve erişim yönetimi hizmeti olarak Azure Active Directory (Azure AD) kullanır. Kuruluşunuzun kimlik ve erişim yönetimini yönetmek için Azure AD 'yi standartlaşmanız gerekir:

- Azure portal, Azure depolama, Azure sanal makinesi (Linux ve Windows), Azure Key Vault, PaaS ve SaaS uygulamaları gibi Microsoft Bulut kaynaklar.

- Kuruluşunuzun kaynakları. Örneğin Azure’daki uygulamalar veya kurumsal ağ kaynaklarınız.

Azure AD'nin güvenliğini sağlamak, kuruluşunuzun güvenlik uygulamalarının yüksek öncelikli bileşenlerinden biri olmalıdır. Azure AD, kimlik güvenliği duruşunu Microsoft'un en iyi deneyim önerilerine göre değerlendirmenize yardımcı olmak için bir kimlik güvenliği puanı sağlar. Bu puanı kullanarak yapılandırmanızın en iyi deneyim önerileriyle ne kadar uyumlu olduğunu görebilir ve güvenlik duruşunuzda geliştirmeler yapabilirsiniz.

Azure AD, Microsoft hesabı olmayan kullanıcıların kendi uygulamalarına ve kaynaklarına kendi dış kimlik bilgileriyle oturum açmasını sağlayan dış kimlikleri destekler.

- [Azure AD’de kiracılık](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Uygulama için dış kimlik sağlayıcılarını kullanma](../active-directory/external-identities/identity-providers.md)

- [Azure AD'de kimlik güvenliği puanı nedir?](../active-directory/fundamentals/identity-secure-score.md)

- [Windows sanal masaüstü 'Nü çalıştırmak için gereken belirli roller ](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Uygulama kimliklerini güvenli ve otomatik bir şekilde yönetin

**Rehberlik**: Windows sanal masaüstü, hizmetler veya Otomasyon gibi insan dışı hesaplar için Azure yönetilen kimliklerini destekler. Kaynaklarınıza erişmek veya onları yürütmek üzere daha güçlü bir insan hesabı oluşturmak yerine Azure yönetilen kimlik özelliğinin kullanılması önerilir. 

Windows sanal masaüstü, sertifika kimlik bilgileriyle hizmet sorumlularını yapılandırmak ve istemci gizliliklerinin geri dönmesi için kaynak düzeyinde kısıtlanmış izinlerle bir hizmet sorumlusu oluşturmak üzere Azure Active Directory (Azure AD) kullanılmasını önerir. Her iki durumda da, Azure Key Vault çalışma zamanı ortamının (örneğin, bir Azure Işlevi) anahtar kasasından kimlik bilgisini alabilmesi için Azure tarafından yönetilen kimliklerle birlikte kullanılabilir.

- [Azure kaynakları için yönetilen kimlikleri destekleyen hizmetler](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure hizmet sorumlusu](/powershell/azure/create-azure-service-principal-azureps) 

- [Sertifikalarla hizmet sorumlusu oluşturma](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Güvenlik sorumlusu kaydı için Azure Key Vault kullanma](../key-vault/general/authentication.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Uygulama erişimi için Azure AD çoklu oturum açma (SSO) özelliğini kullanın

**Rehberlik**: Windows sanal masaüstü, Azure kaynaklarına, bulut uygulamalarına ve şirket içi uygulamalara kimlik ve erişim yönetimi sağlamak için Azure Active Directory (Azure AD) kullanır. Buna çalışanlar gibi kuruluş kimliklerinin yanı sıra iş ortakları, satıcılar ve tedarikçiler gibi dış kimlikler de dahildir. Bu seçenek, kuruluşunuzun şirket içi ortamda ve bulutta bulunan verilerini ve kaynaklarını yönetip bunlara güvenli erişim sağlamak için çoklu oturum açma (SSO) özelliğinin kullanılmasını sağlar. Daha fazla görünürlük ve denetim sayesinde sorunsuz güvenli erişim sağlamak için tüm kullanıcılarınızı, uygulamalarınızı ve cihazlarınızı Azure AD 'ye bağlayın.

- [Azure AD ile uygulamalar için SSO yaklaşımını anlama](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Tüm Azure Active Directory tabanlı erişim girişimleri için güçlü kimlik doğrulaması denetimleri kullanın 

**Rehberlik**: Windows sanal masaüstü, çok faktörlü Authentication ve güçlü passwordless yöntemleri aracılığıyla güçlü kimlik doğrulama denetimlerini destekleyen Azure Active Directory (Azure AD) kullanır.

- Çok faktörlü Authentication-Azure AD çok faktörlü kimlik doğrulamasını etkinleştirin ve çok faktörlü kimlik doğrulama kuruluminizdeki bazı en iyi uygulamalar için Azure Güvenlik Merkezi 'nden kimlik ve erişim yönetimi önerilerini izleyin. Çok faktörlü kimlik doğrulaması, tüm kullanıcılara veya oturum açma koşullarını ve risk etmenlerine göre Kullanıcı başına düzeyinde uygulanabilir.

- Parolasız kimlik doğrulaması: Üç farklı parolasız kimlik doğrulaması seçeneği vardır: İş İçin Windows Hello, Microsoft Authenticator uygulaması ve akıllı kart gibi şirket içi kimlik doğrulama yöntemleri.

Windows sanal masaüstü, bir ana bilgisayar parolası ilkesi veya karma hesapları (Şirket içi parola ilkelerini izleyen şirket içi Azure AD 'den Kullanıcı hesapları) içeren, yalnızca bulut hesapları (doğrudan Azure 'da oluşturulan kullanıcı hesapları) gibi eski parola tabanlı kimlik doğrulamasını destekler. Azure AD, parola tabanlı kimlik doğrulaması kullanırken, kullanıcıların tahmin edilmesi kolay parolalar ayarlayamalarını engelleyen bir parola koruma yeteneği sağlar. Microsoft, telemetri temelinde güncellenen yasaklanmış parolaların küresel bir listesini sağlar ve müşteriler listeyi gereksinimlerine göre artırabilir (marka, kültürel başvuruları vb.). Bu parola koruması, yalnızca bulut ve karma hesaplar için kullanılabilir.

Yalnızca parola kimlik bilgilerine dayalı kimlik doğrulaması popüler saldırı yöntemlerine açıktır. Daha yüksek güvenlik için çok faktörlü kimlik doğrulaması ve güçlü bir parola ilkesi gibi güçlü kimlik doğrulaması kullanın. Varsayılan parolalara sahip olabilecek üçüncü taraf uygulamalar ve Market Hizmetleri için, bunları hizmet ilk kurulumunda değiştirmelisiniz.

Yönetici ve ayrıcalıklı kullanıcılar için, en üst düzey güçlü kimlik doğrulama yöntemlerinin kullanıldığından emin olun ve ardından uygun güçlü kimlik doğrulama ilkesini diğer kullanıcılara teslim edin.

- [Azure Active Directory için parolasız kimlik doğrulaması seçeneklerine giriş](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Azure AD varsayılan parola ilkesi](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Parola korumasını Azure Active Directory kullanarak kötü parolaları kaldırın](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Hesap anomalilerini izleyin ve uyarı oluşturun

**Rehberlik**: Windows sanal masaüstü, aşağıdaki veri kaynaklarını sağlayan Azure Active Directory (Azure AD) ile tümleşiktir:

- Oturum aç-oturum açma raporu, yönetilen uygulamaların kullanımı ve Kullanıcı oturum açma etkinliklerinin kullanımı hakkında bilgi sağlar.

- Denetim günlükleri - Azure AD içindeki çeşitli özellikler tarafından yapılan tüm değişiklikler için günlükler aracılığıyla izlenebilirlik sağlar. Azure AD içindeki herhangi bir kaynakta yapılan kullanıcı, uygulama, grup, rol ve ilkeleri ekleme veya kaldırma işlemleri gibi değişiklikler, denetim günlüklerine örnek gösterilebilir.

- Riskli oturum açma-riskli oturum açma, bir kullanıcı hesabının meşru sahibi olmayan birisi tarafından gerçekleştirilmiş olabilecek oturum açma girişimine yönelik bir göstergedir.

- Riskli oldukları belirlenen kullanıcılar - Riskli kullanıcı, güvenliği tehlikeye girmiş olabilecek bir kullanıcı hesabının göstergesidir.

Bu veri kaynakları Azure Izleyici, Azure Sentinel veya üçüncü taraf güvenlik bilgileri ve olay yönetimi (SıEM) sistemleriyle tümleştirilebilir. Azure Güvenlik Merkezi aşırı fazla sayıda başarısız kimlik doğrulaması girişimi, abonelikte kullanım dışı kalan hesaplar gibi bazı şüpheli etkinliklerde de uyarı verebilir. Azure Gelişmiş Tehdit Koruması (ATP) gelişmiş tehditleri, risk altındaki kimlikleri ve içeriden kötü amaçlı eylemleri belirlemek, algılamak ve araştırmak için Active Directory sinyallerini kullanabilen bir güvenlik çözümüdür.

- [Azure AD 'de denetim etkinlik raporları](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Güvenlik Merkezi’nin tehdit analizi koruma modülündeki uyarılar](../security-center/alerts-reference.md)

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Azure kaynaklarına erişimi koşullara göre kısıtlayın

**Rehberlik**: Windows sanal masaüstü, Kullanıcı tanımlı koşullara dayalı ayrıntılı erişim denetimi için Azure Active Directory (Azure AD) ile koşullu erişimi destekler. Örneğin, belirli IP aralıklarından kullanıcı oturumlarının, erişim için çok faktörlü kimlik doğrulamasını kullanması gerekebilir. 

Ayrıca, ayrıntılı kimlik doğrulama oturumu yönetimi ilkesi, farklı kullanım durumları için de kullanılabilir.

- [Azure'da koşullu erişime genel bakış](../active-directory/conditional-access/overview.md) 

- [Yaygın koşullu erişim ilkeleri](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Koşullu erişimle kimlik doğrulama oturumu yönetimini yapılandırma](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [Windows sanal masaüstüne özgü koşullu erişim kurulum bilgileri burada bulunabilir](set-up-mfa.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="privileged-access"></a>Ayrıcalıklı Erişim

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ayrıcalıklı Erişim](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: İş açısından kritik sistemlere yönetici erişimini kısıtlayın

**Rehberlik**: Windows sanal masaüstü, iş açısından kritik sistemlere erişimi yalıtmak için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanır. Active Directory Etki Alanı Denetleyicileri, güvenlik araçları ve iş açısından kritik sistemlere yüklenen aracılarla sistem yönetimi araçları gibi iş açısından kritik erişimlerinize yönetim erişimi olan yönetim, kimlik ve güvenlik sistemlerine erişimi de kısıtlatığınızdan emin olun. Bu yönetim ve güvenlik sistemlerinden ödün veren saldırganlar, iş açısından kritik varlıkların güvenliğini tehlikeye atabilir.

Tutarlı erişim denetimi sağlamak için, tüm erişim denetimi türleri kurumsal segmentasyon stratejinize hizalanmalıdır.

- [Azure bileşenleri ve başvuru modeli](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Yönetim grubu erişimi](../governance/management-groups/overview.md#management-group-access) 

- [Azure abonelik yöneticileri](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Windows sanal masaüstünü yönetmek için gereken en düşük yönetici izinleri](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Kullanıcı erişimini düzenli olarak gözden geçirin ve mutabık kılın

**Rehberlik**: Windows sanal masaüstü, kaynaklarını yönetmek için Azure Active Directory (Azure AD) hesapları kullanır, hesapların ve erişiminin geçerli olduğundan emin olmak için Kullanıcı hesaplarını ve erişim atamasını düzenli olarak gözden geçirin.

Grup üyeliklerini, kurumsal uygulamalara erişimi ve rol atamalarını gözden geçirmek için Azure AD erişim incelemelerini kullanın. Azure AD raporlama, eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlayabilir.

Ayrıca, Azure Privileged Identity Management, çok fazla sayıda yönetici hesabı oluşturulduğunda ve eski veya yanlış yapılandırılmış yönetici hesaplarını belirlemek için de yapılandırılabilir.

Bazı Azure Hizmetleri, Azure AD aracılığıyla yönetilmeyen yerel kullanıcıları ve rolleri destekler. Bu kullanıcıları ayrı ayrı yönetmeniz gerekir.

- [Windows sanal masaüstü için yerleşik roller](rbac.md)

- [Privileged Identity Management (PıM) içinde Azure Kaynak rolleri için erişim gözden geçirmesi oluşturma](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Azure AD kimlik ve erişim gözden geçirmelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Azure AD'de acil durum erişimini ayarlayın

**Rehberlik**: Windows sanal masaüstü, kaynaklarını yönetmek için Azure Active Directory (Azure AD) kullanır. Yanlışlıkla Azure AD kuruluşunuzun dışına kilitlenmesini engellemek için, normal yönetim hesapları kullanılmazsa erişim için bir acil durum erişim hesabı ayarlayın. Acil durum erişim hesapları genellikle yüksek ayrıcalığa sahiptir ve herhangi bir kişiye atanmamalıdır. Acil durum erişim hesaplarının kullanım alanı, normal yönetici hesaplarının kullanılamadığı acil veya "camı kırın" senaryolarıyla sınırlıdır.

Acil durum erişim hesaplarının kimlik bilgilerinin (parola, sertifika veya akıllı kart) güvenli bir şekilde saklandığından ve yalnızca acil bir durumda bunları kullanma yetkisine sahip olan kullanıcılar tarafından bilindiğinden emin olmanız gerekir.

- [Azure AD'deki acil durum erişim hesaplarını yönetme](../active-directory/roles/security-emergency-access.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="pa-5-automate-entitlement-management"></a>PA-5: yetkilendirme yönetimini otomatikleştirme 

**Rehberlik**: Windows sanal masaüstü, kaynaklarını yönetmek için Azure Active Directory (Azure AD) ile tümleşiktir. Erişim atamaları, incelemeler ve süre sonları dahil olmak üzere, erişim isteği iş akışlarını otomatikleştirmek için Azure AD yetkilendirme yönetimi özelliklerini kullanın. Ek olarak, çift veya çok aşamalı onaylar de desteklenir.

- [Azure AD erişim gözden geçirmeleri nelerdir?](../active-directory/governance/access-reviews-overview.md) 

- [Azure AD yetkilendirme yönetimi nedir?](../active-directory/governance/entitlement-management-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Ayrıcalıklı erişim iş istasyonlarını kullanın

**Rehberlik**: güvenli ve yalıtılmış iş istasyonları, Yöneticiler, geliştiriciler ve kritik hizmet işleçleri gibi hassas rollerin güvenliği açısından kritik öneme sahiptir. Yönetim görevleri için yüksek düzeyde güvenli Kullanıcı iş istasyonları ve/veya Azure savunma kullanın. 

Yönetim görevleri için güvenli ve yönetilen bir kullanıcı iş istasyonu dağıtmak üzere Azure Active Directory (Azure AD), Microsoft Defender Gelişmiş tehdit koruması (ATP) veya Microsoft Intune kullanın. Güvenli iş istasyonu, güçlü kimlik doğrulaması, yazılım ve donanım temelleri, kısıtlı mantıksal ve ağ erişimi gibi güvenli yapılandırmayı zorlamak için merkezi olarak yönetilebilir.

- [Ayrıcalıklı erişim iş istasyonlarını anlama](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Ayrıcalıklı erişim iş istasyonu dağıtma](/security/compass/privileged-access-deployment)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>AE-7: Tam yetecek kadar yönetim uygulama (en düşük ayrıcalık ilkesi) 

**Rehberlik**: Windows sanal masaüstü, kaynaklarını yönetmek için Azure rol tabanlı erişim denetimi (Azure RBAC) ile tümleşiktir. Azure RBAC ağ atamaları aracılığıyla Azure kaynak erişimini yönetmenize olanak tanır. Bu rolleri kullanıcılara atayabilir, hizmet sorumlularını ve yönetilen kimlikleri gruplara atayabilirsiniz. Belirli kaynaklar için önceden tanımlanmış yerleşik roller vardır ve Azure CLI, Azure PowerShell veya Azure portal gibi araçlardan yararlanarak bu rollerin envanteri çıkarılabilir ve bunlar sorgulanabilir. 

Azure RBAC ile kaynaklara atadığınız ayrıcalıkların her zaman roller için gereken olanlarla sınırlı olması gerekir. Bu, Azure Active Directory (Azure AD) ile Privileged Identity Management (PıM) 'nin tam zamanında (JıT) yaklaşımını tamamlar ve düzenli olarak incelenmelidir.

Ayrıca, izinleri ayırmak için yerleşik rolleri kullanın ve gerektiğinde yalnızca özel roller oluşturun.

- [Azure rol tabanlı erişim denetimi nedir (Azure RBAC)](../role-based-access-control/overview.md) 

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md) 

- [Azure AD kimlik ve erişim gözden geçirmelerini kullanma](../active-directory/governance/access-reviews-overview.md)

- [Windows sanal masaüstü için yerleşik roller](rbac.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Microsoft desteği için onay işlemini seçin  

**Kılavuz**: Microsoft 'un müşteri verilerine erişmesi gereken destek senaryolarında, Windows sanal masaüstü, müşteri verileri erişim isteklerini gözden geçirmeniz ve onaylamanız veya reddetmeniz için bir arabirim sağlamak üzere müşteri kasası destekler.

- [Müşteri Kasası anlayın](../security/fundamentals/customer-lockbox-overview.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Hassas verileri bulun, sınıflandırın ve etiketleyin

**Rehberlik**: uygun denetimleri tasarlayabilmeniz için hassas verilerinizi bulun, sınıflandırın ve etiketleyin. Bu, hassas bilgilerin, kuruluşun teknoloji sistemleri tarafından güvenli bir şekilde depolanmasını, işlenmesini ve aktarılmasını sağlamaktır.

Azure, şirket içi, Office 365 ve diğer konumlarda Office belgelerindeki önemli bilgiler için Azure Information Protection (ve ilişkili Tarama Aracı) kullanın.

Azure SQL Information Protection’ı kullanarak Azure SQL Veritabanlarında depolanan bilgilerin sınıflandırılmasına ve etiketlenmesine yardımcı olabilirsiniz.

- [Azure Information Protection kullanarak hassas bilgileri etiketleme](/azure/information-protection/what-is-information-protection) 

- [Azure SQL Veri Bulma’yı uygulama](../azure-sql/database/data-discovery-and-classification-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Hassas verileri koruyun

**Rehberlik**: Azure rol tabanlı Access Control (Azure RBAC), ağ tabanlı erişim denetimleri ve Azure hizmetlerindeki belirli DENETIMLERI (SQL ve diğer veritabanlarında şifreleme gibi) kullanarak erişimi kısıtlayarak hassas verileri koruyun.

Tutarlı erişim denetimi sağlamak için tüm erişim denetimi türlerinin kurumsal segmentasyon stratejinizle uyumlu olması gerekir. Ayrıca kurumsal segmentasyon stratejisi, hassas ve iş açısından kritik verilerle sistemlerin konumu hakkında da bilgilendirilmelidir.

Microsoft, tüm müşteri içeriklerini müşteri veri kaybına ve pozlamaya karşı hassas ve koruyucuları olarak değerlendirir. Azure içindeki müşteri verilerinin güvenlik altında kalmasını sağlamak için Microsoft bazı varsayılan veri koruma denetimleri ve özellikleri uygulamıştır.

- [Azure Rol Tabanlı Erişim Denetimi (RBAC)](../role-based-access-control/overview.md) 

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>VK-3: Hassas verilerin yetkisiz aktarımını izleme

**Rehber**: Kurumsal görünürlüğün ve denetimin dışındaki konumlara yetkisiz veri aktarımlarını izleyin. Bu normalde yetkisiz veri sızdırmaya işaret ediyor olabilecek anormal etkinliklerin (büyük veya alışılmamış aktarımlar) izlenmesini içerir.

Hem Azure Storage hem de Azure SQL ATP ile Gelişmiş tehdit koruması (ATP) özellikleri, önemli bilgilerin yetkisiz olarak aktarılmasıyla ilgili olarak, anormal bilgi aktarımına uyarı verebilir.

Azure Information Protection (AIP) sınıflandırılmış ve etiketlenmiş bilgileri izleme özellikleri sağlar.

Veri kaybını önlemek için algılama ve/veya önleyici denetimleri zorlamak üzere, ana bilgisayar tabanlı olanlar gibi veri kaybı önleme çözümlerini kullanın.

- [Azure SQL ATP’yi Etkinleştirme](../azure-sql/database/threat-detection-overview.md) 

- [Azure Depolama ATP’yi Etkinleştirme](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="asset-management"></a>Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Varlık Yönetimi](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>VY-1: Güvenlik ekibinin varlıklara yönelik risklerde görünürlük elde etmesini sağlama

**Rehber**: Güvenlik ekiplerinin Azure Güvenlik Merkezi’ni kullanarak güvenlik risklerini izlemesini sağlamak için bu ekiplere Azure kiracınız ve abonelikleriniz üzerinde Güvenlik Okuyucusu izinleri verildiğinden emin olun. 

Güvenlik ekibinin sorumluluklarına bağlı olarak, güvenlik risklerini izlemek, merkezi bir güvenlik ekibinin veya yerel ekibin sorumluluğunda olabilir. Gerçi güvenlik içgörüleri ve risklerinin kuruluş içinde her zaman merkezi bir konumda toplanması gerekir. 

Güvenlik Okuyucusu izinleri kiracının tamamına (Kök Yönetim Grubu) geniş kapsamlı olarak uygulanabileceği gibi, izinlerin kapsamı yönetim gruplarıyla veya belirli aboneliklerle de sınırlanabilir. 

İş yüklerinin ve hizmetlerin görünürlüğü için ek izinler gerekebilir. 

- [Güvenlik Okuyucusu Rolüne Genel Bakış](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure Yönetim Gruplarına Genel Bakış](../governance/management-groups/overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Güvenlik ekibinin varlık envanterine ve meta verilerine erişebildiğinden emin olun

**Rehberlik**: Azure kaynaklarınıza, kaynak gruplarına ve aboneliklerinize mantıksal olarak bunları bir taksonomi halinde düzenlemek için Etiketler uygulayın. Her etiket bir ad ve bir değer çiftinden oluşur. Örneğin, "Ortam" adını ve "Üretim" değerini üretimdeki tüm kaynaklara uygulayabilirsiniz.

Sanal makinelerde yazılım hakkında bilgi toplamayı otomatik hale getirmek için Azure sanal makine envanterini kullanın. Yazılım adı, sürüm, yayımcı ve yenileme süresi Azure portal kullanılabilir. Yüklemeyi tarih ve diğer bilgilere erişim sağlamak için, Konuk düzeyinde tanılamayı etkinleştirin ve Windows olay günlüklerini Log Analytics çalışma alanına getirin.

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md) 

- [Azure Güvenlik Merkezi varlık envanteri yönetimi](../security-center/asset-inventory.md) 

- [Kaynak adlandırma ve etiketleme kararı kılavuzu](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Azure sanal makine envanterini etkinleştirme](../automation/automation-tutorial-installed-software.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Yalnızca onaylı Azure hizmetlerini kullanın

**Rehberlik**: kullanıcıların ortamınızda hangi hizmetleri sağlayabileceğini denetlemek ve kısıtlamak Için Azure İlkesi ' ni kullanın. Abonelikler içindeki kaynakları sorgulamak ve bulmak için Azure Kaynak Grafı'nı kullanın. Ayrıca Azure İzleyici'yi kullanarak onaylanmamış hizmetler algılandığında uyarı tetikleme amacıyla kurallar oluşturabilirsiniz.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](../governance/policy/samples/built-in-policies.md#general) 

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>VY-4: Varlık yaşam döngüsü yönetiminin güvenliğini sağlama

**Rehberlik**: uygulanamaz. Windows sanal masaüstü, bir yaşam döngüsü yönetimi işlemindeki varlıkların güvenliğini sağlamak için kullanılamaz. Yüksek etki olarak kabul edilen varlıkların özniteliklerinin ve ağ yapılandırmalarının bakımını yapmak müşterinin sorumluluğundadır. 

Müşterinin özniteliği ve ağ yapılandırması değişikliklerini yakalamak, değişiklik etkisini ölçmek ve uygun şekilde düzeltme görevleri oluşturmak için bir işlem oluşturması önerilir.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>ÖÖ-5: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcıların Azure kaynakları yöneticisiyle etkileşime geçmesini sınırlamak Için Azure koşullu erişimi kullanın.

- [Azure Kaynak Yöneticisi 'ne erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>HAR-6: yalnızca işlem kaynaklarında onaylanan uygulamaları kullanın

**Rehberlik**: sanal makinelerdeki tüm yazılımlarla ilgili bilgi toplamayı otomatikleştirmek için Azure sanal makine envanterini kullanın. Yazılım adı, sürüm, yayımcı ve yenileme süresi Azure portal kullanılabilir. Yüklemeyi tarih ve diğer bilgilere erişim sağlamak için, Konuk düzeyinde tanılamayı etkinleştirin ve Windows olay günlüklerini Log Analytics çalışma alanına getirin.

- [Azure sanal makine envanterini etkinleştirme](../automation/automation-tutorial-installed-software.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="logging-and-threat-detection"></a>Günlüğe Kaydetme ve Tehdit Algılama

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Günlüğe Kaydetme ve Tehdit Algılama](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure kaynakları için tehdit algılamayı etkinleştirme

**Kılavuz**: Azure Güvenlik Merkezi 'nin yerleşik tehdit algılama özelliğini kullanın ve Windows sanal masaüstü kaynaklarınız Için Azure Defender 'ı (resmi olarak Azure Gelişmiş tehdit koruması) etkinleştirin. Windows sanal masaüstü için Azure Defender, Windows sanal masaüstü kaynaklarınıza yönelik olağan dışı ve potansiyel olarak zararlı girişimleri algılayan ek bir güvenlik zekası katmanı sağlar.

Windows sanal masaüstündeki tüm günlükleri, özel tehdit algılamalarını ayarlamak için kullanılabilecek güvenlik bilgileri olay yönetimi (SıEM) çözümünüze iletin. Olası tehditler ve bozukluklar için farklı türlerde Azure varlıklarını izlemediğinizden emin olun. Analistlerin sıralama için yanlış pozitif durumları azaltmaya yönelik yüksek kaliteli uyarılar almaya odaklanın. Uyarılar, günlük verilerinden, aracılardan veya diğer verilerden kaynaklıdır.

- [Azure Güvenlik Merkezi’nde tehdit koruması](../security-center/azure-defender.md) 

- [Azure Güvenlik Merkezi güvenlik uyarıları başvuru kılavuzu](../security-center/alerts-reference.md)

- [Tehditleri algılamak için özel analiz kuralları oluşturma](../sentinel/tutorial-detect-threats-custom.md) 

- [Azure Sentinel ile Cyber Threat Intelligence](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure kimlik ve erişim yönetimi için tehdit algılamayı etkinleştirin

**Rehberlik**: Azure Active Directory (Azure AD), Azure AD raporlama 'da görüntülenebilen veya Azure Izleyici, Azure Sentinel veya diğer güvenlik bilgileri ve olay yönetimi (SIEM) ya da daha ayrıntılı izleme ve analiz kullanım durumları için izleme araçlarıyla tümleştirilen aşağıdaki kullanıcı günlüklerini sağlar:

- Oturum açma-oturum açma raporu, yönetilen uygulamaların kullanımı ve Kullanıcı oturum açma etkinliklerinin kullanımı hakkında bilgi sağlar.

- Denetim günlükleri - Azure AD içindeki çeşitli özellikler tarafından yapılan tüm değişiklikler için günlükler aracılığıyla izlenebilirlik sağlar. Azure AD içindeki herhangi bir kaynakta yapılan kullanıcı, uygulama, grup, rol ve ilkeleri ekleme veya kaldırma işlemleri gibi değişiklikler, denetim günlüklerine örnek gösterilebilir.

- Riskli oturum açma-riskli oturum açma, bir kullanıcı hesabının meşru sahibi olmayan birisi tarafından gerçekleştirilmiş olabilecek oturum açma girişimine yönelik bir göstergedir.

- Riskli oldukları belirlenen kullanıcılar - Riskli kullanıcı, güvenliği tehlikeye girmiş olabilecek bir kullanıcı hesabının göstergesidir.

Azure Güvenlik Merkezi, abonelikte çok fazla sayıda başarısız kimlik doğrulama girişimi ve kullanım dışı bırakılmış hesaplar gibi bazı şüpheli etkinlikler üzerinde de uyarı verebilir. Temel güvenlik durumu izlemeye ek olarak, Azure Güvenlik Merkezi 'ndeki tehdit koruması modülü, tek tek Azure işlem kaynakları (sanal makineler, kapsayıcılar, App Service), veri kaynakları (SQL DB ve depolama) ve Azure hizmet katmanlarından daha ayrıntılı güvenlik uyarıları da toplayabilirler. Bu özellik, bireysel kaynakların içindeki hesap bozukluklarını görünürlüğe sahip etmenize olanak tanır.

- [Azure Active Directory’de denetim etkinliği raporları](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Kimlik Koruması’nı etkinleştirme](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure Güvenlik Merkezi’nde tehdit koruması](../security-center/azure-defender.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Azure ağ etkinlikleri için günlüğe kaydetmeyi etkinleştirin

**Rehberlik**: Windows sanal masaüstü, etki alanı ad HIZMETI (DNS) sorgu günlüklerini oluşturmaz veya işlemez. Ancak hizmete kayıtlı kaynaklar akış günlükleri oluşturabilir.

Olay araştırmaları, tehdit güvenliği ve güvenlik uyarısı oluşturmayı desteklemek üzere güvenlik çözümlemesi için ağ güvenlik grubu kaynağı ve akış günlüklerini, Azure Güvenlik Duvarı günlüklerini ve Web uygulaması güvenlik duvarı (WAF) günlüklerini etkinleştirin ve toplayın. Akış günlüklerini bir Azure Izleyici Log Analytics çalışma alanına gönderebilir ve sonra öngörüleri sağlamak için Trafik Analizi kullanabilirsiniz.

- [Ağ güvenlik grubu akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Azure Güvenlik Duvarı günlükleri ve ölçümleri](../firewall/logs-and-metrics.md) 

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md) 

- [Azure Izleyici 'de Azure ağ izleme çözümleri](../azure-monitor/insights/azure-networking-analytics.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure kaynakları için günlüğe kaydetmeyi etkinleştirin

**Kılavuz**: otomatik olarak etkinleştirilen etkinlik günlükleri, okuma IŞLEMLERI (Get) haricinde Windows sanal masaüstü kaynaklarınız için tüm yazma IŞLEMLERINI (put, Post, Delete) içerir. Etkinlik günlükleri, sorun giderirken bir hata bulmak veya kuruluşunuzdaki bir kullanıcının bir kaynağı nasıl değiştirdiğinizi izlemek için kullanılabilir.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure 'da günlüğe kaydetme ve farklı günlük türlerini anlama](../azure-monitor/essentials/platform-logs-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Paylaşılan

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Güvenlik günlüğü yönetim ve analiz süreçlerini merkezileştirin

**Rehberlik**: bağıntı sağlamak için günlük depolamayı ve çözümlemeyi merkezileştirme. Her günlük kaynağı için bir veri sahibi, erişim Kılavuzu, depolama konumu, verileri işlemek ve erişmek için kullanılan araçları ve veri saklama gereksinimlerini atamış olduğunuzdan emin olun.

Azure etkinlik günlüklerini merkezi günlüklerinizi tümleştirdiğinizden emin olun. Endpoint cihazları, ağ kaynakları ve diğer güvenlik sistemleri tarafından oluşturulan güvenlik verilerini toplamak için Azure Izleyici aracılığıyla günlük alma. Azure Izleyici 'de analiz sorgulamak ve gerçekleştirmek için Log Analytics çalışma alanlarını kullanın ve uzun süreli ve arşiv depolama için Azure depolama hesaplarını kullanın.

Ayrıca, verileri Azure Sentinel 'e veya bir üçüncü taraf güvenlik bilgileri olay yönetimine (SıEM) etkinleştirin ve ekleyin. Birçok kuruluş, daha az sıklıkta kullanılan "soğuk" verileri için sık kullanılan ve Azure depolama için Azure Sentinel ' i "etkin" veriler için kullanmayı seçer.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

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

Azure Güvenlik Merkezi önce hangi uyarıların araştırılması gerektiğini belirlemenize yardımcı olmak için her uyarıya bir önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizde ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır.

Buna ek olarak Azure kaynaklarını, özellikle hassas verileri işleyen kaynakları belirlemek ve kategorilere ayırmak için etiketleri kullanarak kaynakları işaretleyin ve bir adlandırma sistemi oluşturun. Azure kaynaklarının önem düzeyine ve olayın oluştuğu ortama bağlı olarak uyarıların çözümünde önceliği belirlemek sizin sorumluluğunuzdadır.

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

Kuruluşunuz tarafından istenen işletim sisteminin güvenlik yapılandırmasını oluşturmak için özel işletim sistemi görüntülerini veya Azure Automation durum yapılandırması ' nı kullanabilirsiniz.

- [Azure Güvenlik Merkezi önerilerini izleme](../security-center/security-center-recommendations.md) 

- [Azure Otomasyonu durum yapılandırmasına genel bakış](../automation/automation-dsc-overview.md) 

- [Windows Sanal Masaüstü ortamı](environment-setup.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>BD-4: işlem kaynakları için güvenli yapılandırmalara dayanmalar

**Rehberlik**: sanal makineler, kapsayıcılar ve diğerleri dahil olmak üzere Azure işlem kaynaklarınızın yapılandırma risklerini düzenli olarak değerlendirmek ve düzeltmek Için Azure Güvenlik Merkezi 'Ni ve Azure ilkesini kullanın. Ayrıca, kuruluşunuz tarafından istenen işletim sisteminin güvenlik yapılandırmasını sürdürmek için Azure Resource Manager şablonları, özel işletim sistemi görüntüleri veya Azure Otomasyonu durum yapılandırması ' nı kullanabilirsiniz. Azure Otomasyonu durum yapılandırması ile birleştirilmiş Microsoft sanal makine şablonları, güvenlik gereksinimlerinin karşılanmasına ve bakımında yardımcı olabilir.

Microsoft tarafından yayınlanan Azure Market sanal makine görüntüleri Microsoft tarafından yönetilir ve sürdürülür.

Azure Güvenlik Merkezi ayrıca kapsayıcı görüntüsündeki güvenlik açıklarını tarayabilir ve Merkezi Internet güvenliği 'nin Docker kıyaslaması için kapsayıcılarda Docker yapılandırmanızın sürekli olarak izlenmesini gerçekleştirebilir. Önerileri görüntülemek ve sorunları düzeltmek için Azure Güvenlik Merkezi öneriler sayfasını kullanabilirsiniz.

- [Azure Güvenlik Merkezi güvenlik açığı değerlendirmesi önerilerini uygulama](../security-center/deploy-vulnerability-assessment-vm.md) 

- [ARM şablonundan Azure sanal makinesi oluşturma](../virtual-machines/windows/ps-template.md) 

- [Azure Otomasyonu durum yapılandırmasına genel bakış](../automation/automation-dsc-overview.md) 

- [Güvenlik Merkezi’nde kapsayıcı güvenliği](../security-center/container-security.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>BD-5: özel işletim sistemi ve kapsayıcı görüntülerini güvenli bir şekilde depolayın

**Rehberlik**: Windows sanal masaüstü, müşterilerin işletim sistemi görüntülerini yönetmesine olanak tanır. Yalnızca yetkili kullanıcıların özel görüntülerinize erişebildiğinden emin olmak için Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanın. Azure Paylaşılan görüntü Galerisi 'ni kullanarak, görüntülerinizi kuruluşunuzdaki farklı kullanıcılara, hizmet sorumlularına veya Active Directory gruplarına paylaşabilirsiniz. Kapsayıcı görüntülerini Azure Container Registry depolayın ve yalnızca yetkili kullanıcıların erişimi olduğundan emin olmak için RBAC kullanın.

- [Azure RBAC 'yi anlama](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [Paylaşılan görüntü galerisine genel bakış](../virtual-machines/shared-image-galleries.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pv-6-perform-software-vulnerability-assessments"></a>BD-6: yazılım güvenlik açığı değerlendirmeleri gerçekleştirme

**Rehberlik**: Windows sanal masaüstü, kendi sanal makinelerinizi dağıtmanıza ve bu hizmetleri hizmete kaydetmenizi ve ortamda SQL veritabanının çalıştırılmasını sağlar.

Windows sanal masaüstü, ağ cihazlarında ve Web uygulamalarında güvenlik açığı değerlendirmesi gerçekleştirmek için üçüncü taraf bir çözüm kullanabilir. Uzak taramalar yaparken, tek bir kalıcı, yönetici hesabı kullanmayın. Tarama hesabı için JıT sağlama yöntemini uygulamayı düşünün. Tarama hesabı için kimlik bilgileri korunmalıdır, izlenir ve yalnızca güvenlik açığı taraması için kullanılmalıdır.

Gerekli olduğu gibi, güvenlik açıklarının düzeltildiğini doğrulamak için tarama sonuçlarını tutarlı aralıklarla dışarı aktarın ve sonuçları önceki taramalarla karşılaştırın.

Azure sanal makinelerinizde (ve SQL Server 'lar) güvenlik açığı değerlendirmesi gerçekleştirmek için Azure Güvenlik Merkezi 'nin önerilerini izleyin. Azure Güvenlik Merkezi 'nde sanal makine, kapsayıcı görüntüleri ve SQL veritabanı için yerleşik bir güvenlik açığı tarayıcısı vardır.

Gerektiğinde, güvenlik açıklarının düzeltildiğini doğrulamak için tarama sonuçlarını tutarlı aralıklarla dışarı aktarın ve sonuçları önceki taramalarla karşılaştırın. Azure Güvenlik Merkezi tarafından önerilen güvenlik açığı yönetimi önerilerini kullanırken, geçmiş tarama verilerini görüntülemek için seçili çözümün portalına de Pivot ekleyebilirsiniz.

- [Azure Güvenlik Merkezi güvenlik açığı değerlendirmesi önerilerini uygulama](../security-center/deploy-vulnerability-assessment-vm.md) 

- [Sanal makineler için tümleşik güvenlik açığı tarayıcısı](../security-center/deploy-vulnerability-assessment-vm.md) 
- [SQL güvenlik açığı değerlendirmesi](../azure-sql/database/sql-vulnerability-assessment.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>DG-7: Yazılım güvenlik açıklarını hızla ve otomatik olarak düzeltme

**Rehberlik**: Windows sanal masaüstü, herhangi bir üçüncü taraf yazılım kullanmaz veya gerektirmez. Ancak, Windows sanal masaüstü, kendi sanal makinelerinizi dağıtmanıza ve bunları hizmete kaydetmenize olanak tanır.

Windows Server sanal makinelerinizde en son güvenlik güncelleştirmelerinin yüklü olduğundan emin olmak için Azure Otomasyonu Güncelleştirme Yönetimi veya üçüncü taraf bir çözüm kullanın. Windows sanal makineleri için Windows Update etkinleştirildiğinden ve otomatik olarak güncelleştirilecek şekilde ayarlandığından emin olun.

Üçüncü taraf yazılım için bir üçüncü taraf düzeltme eki yönetimi çözümü veya Configuration Manager için System Center Updates Publisher kullanın.

- [Azure 'da sanal makineler için Güncelleştirme Yönetimi Yapılandırma](../automation/update-management/overview.md) 

- [Azure VM 'leriniz için güncelleştirmeleri ve düzeltme eklerini yönetme](../automation/update-management/manage-updates-for-vm.md)

- [Windows sanal masaüstü için Microsoft uç nokta Configuration Manager yapılandırma](configure-automatic-updates.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Düzenli aralıklarla saldırı simülasyonları yapın

**Rehberlik**: Windows sanal masaüstü, müşterilerin Windows sanal masaüstü kaynakları üzerinde kendi sızma testini gerçekleştirmesine izin vermez.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

## <a name="endpoint-security"></a>Uç nokta güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: uç nokta güvenliği](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: uç nokta algılamayı ve yanıtını kullanın (EDR)

**Rehberlik**: Windows sanal masaüstü, Endpoint Detection ve yanıt (EDR) işlemlerine yönelik belirli bir özellik sağlamaz. Ancak hizmete kaydedilen kaynaklar, uç nokta algılama ve yanıt özelliğinden yararlanabilir. 

Sunucular ve istemciler için uç nokta algılamayı ve yanıt yeteneklerini etkinleştirin ve bunları güvenlik bilgileri ve olay yönetimi (SıEM) çözümleri ve güvenlik Işlemleri işlemleriyle tümleştirin.

Microsoft Defender 'ın Gelişmiş tehdit koruması, gelişmiş tehditleri önlemeye, algılamaya, araştırmaya ve yanıt vermeye yönelik bir kurumsal uç nokta güvenlik platformunun parçası olarak uç nokta algılama ve yanıt özellikleri sağlar.

- [Microsoft Defender Gelişmiş tehdit korumasına genel bakış](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [Windows Server 'lar için Microsoft Defender ATP hizmeti](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [Windows dışı sunucular için Microsoft Defender ATP hizmeti](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [Kalıcı olmayan sanal masaüstü altyapısı için Microsoft Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: merkezi olarak yönetilen modern kötü amaçlı yazılımdan koruma yazılımı kullanın

**Rehberlik**: gerçek zamanlı ve düzenli tarama özellikli merkezi olarak yönetilen ve modern bir uç nokta kötü amaçlı yazılımdan koruma çözümü Ile Windows sanal masaüstü kaynaklarınızı koruyun.

Azure Güvenlik Merkezi, sanal makineleriniz için çeşitli popüler kötü amaçlı yazılımdan koruma çözümlerinin kullanımını otomatik olarak tanımlayabilir ve uç nokta koruma çalıştırma durumunu raporlar ve öneriler yapar.

Azure için Microsoft kötü amaçlı yazılımdan koruma Cloud Services, Windows sanal makineleri (VM 'Ler) için varsayılan kötü amaçlı yazılımdan koruma. Ayrıca, Azure depolama hesaplarına yüklenen kötü amaçlı yazılımları algılamak için veri Hizmetleri için Azure Güvenlik Merkezi ile tehdit algılama 'yı kullanabilirsiniz.

- [Cloud Services ve sanal makineler için Microsoft Antimalware 'i yapılandırma](../security/fundamentals/antimalware.md) 

- [Desteklenen Endpoint Protection çözümleri](../security-center/security-center-services.md?tabs=features-windows#supported-endpoint-protection-solutions-)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: kötü amaçlı yazılımdan koruma yazılımlarının ve imzaların güncelleştirildiğinden emin olun

**Rehberlik**: kötü amaçlı yazılımdan koruma imzalarının hızlı ve tutarlı bir şekilde güncelleştirildiğinden emin olun.

Azure Güvenlik Merkezi 'ndeki önerileri takip edin: "Işlem &amp; Uygulamaları", tüm sanal makinelerin ve/veya kapsayıcıların en son imzalarla güncel olduğundan emin olmak için.

Microsoft kötü amaçlı yazılımdan koruma, varsayılan olarak en son imzaları ve altyapı güncelleştirmelerini otomatik olarak yükler.

- [Azure Cloud Services ve sanal makineler için Microsoft Antimalware nasıl dağıtılır](../security/fundamentals/antimalware.md) 

- [Azure Güvenlik Merkezi 'nde Endpoint Protection değerlendirmesi ve önerileri](../security-center/security-center-endpoint-protection.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="backup-and-recovery"></a>Yedekleme ve Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Yedekleme ve Kurtarma](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: düzenli otomatik yedeklemeler sağlayın

**Rehberlik**: beklenmeyen bir olaydan sonra iş sürekliliği sağlamak için sistemleri ve verileri yedeklemediğinizden emin olun. Bu, kurtarma noktası hedefi (RPO) ve kurtarma süresi hedefi (RTO) için herhangi bir amaç tarafından rehberlik edilmelidir.

Azure Backup etkinleştirin ve yedekleme kaynağını (örn. Azure VM 'Leri, SQL Server, HANA veritabanları veya dosya paylaşımları) ve istenen sıklık ve bekletme süresini yapılandırın.

Daha yüksek düzeyde artıklık için, coğrafi olarak yedekli depolama seçeneğini etkinleştirerek yedek verileri ikincil bir bölgeye çoğaltabilir ve çapraz bölge geri yükleme kullanarak kurtarabilirsiniz.

- [Kurumsal ölçekli iş sürekliliği ve olağanüstü durum kurtarma](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [Azure Backup etkinleştirme](../backup/index.yml) 

- [Bölgeler arası geri yükleme nasıl etkinleştirilir?](../backup/backup-azure-arm-restore-vms.md#cross-region-restore) 

- [Windows sanal masaüstü 'nde iş sürekliliği ve olağanüstü durum kurtarma planı ayarlama](disaster-recovery.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="br-2-encrypt-backup-data"></a>BR-2: yedekleme verilerini şifreleme

**Rehberlik**: yedeklemelerinizin saldırılara karşı korunmasını sağlayın. Bu, gizlilik kaybına karşı korunmak için yedeklemelerin şifrelenmesini içermelidir.

Normal Azure hizmeti yedeklemesi için, yedekleme verileri Azure platformu tarafından yönetilen anahtarlar kullanılarak otomatik olarak şifrelenir. Yedeklemeyi, müşteri tarafından yönetilen anahtarı kullanarak şifrelemeyi seçebilirsiniz. Bu durumda, anahtar kasasındaki müşterinin yönettiği anahtarın de yedekleme kapsamında olduğundan emin olun.

Yedeklemeleri ve müşteri tarafından yönetilen anahtarları korumak için Azure Backup, Azure Key Vault veya diğer kaynaklarda rol tabanlı erişim denetimi kullanın. Ayrıca, yedeklemelerin değiştirilemeyeceği veya silinebilmesi için çok faktörlü kimlik doğrulaması istemek üzere gelişmiş güvenlik özellikleri etkinleştirebilirsiniz.

Azure Backup/Azure/Backup/Security-Overview ' deki güvenlik özelliklerine genel bakış 

- [Müşteri tarafından yönetilen anahtarları kullanarak yedekleme verilerini şifreleme](../backup/encryption-at-rest-with-cmk.md) 

- [Azure 'da Key Vault anahtarlarını yedekleme](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?preserve-view=true&view=azurermps-6.13.0)

- [Karma yedeklemelerin saldırılara karşı korunmasına yardımcı olacak güvenlik özellikleri](../backup/backup-azure-security-feature.md#prevent-attacks)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Müşteri tarafından yönetilen anahtarlar dahil olmak üzere tüm yedeklemeleri doğrulayın

**Rehberlik**: yedeklemenin düzgün çalıştığından emin olmak için bir veri geri yükleme işlemi gerçekleştirerek, yedekleme medyasındaki veri bütünlüğünü düzenli aralıklarla doğrulamanız önerilir.

- [Azure sanal makine yedeklemesinden dosyaları kurtarma](../backup/backup-azure-restore-files-from-vm.md)

- [Güvenlik uygulamaları](../backup/backup-azure-restore-files-from-vm.md#security-implementations)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

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

-   Kurumsal verilerin sınıflandırmasına uygun olarak gerekli erişim denetimi stratejisi

-   Azure yerel ve üçüncü taraf veri koruma özelliklerinin kullanımı

-   Aktarımdaki ve bekleyen veri kullanım örneklerine yönelik veri şifreleme gereksinimleri

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

- [Azure Güvenlik Karşılaştırması: Kimlik yönetimi](../automation/update-management/overview.md)

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
