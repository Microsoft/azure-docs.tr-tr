---
title: Sanal WAN için Azure Güvenlik temeli
description: Sanal WAN güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e4bbc1565cec2c356e916f813a6a334648d82754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726742"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Sanal WAN için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 2,0](../security/benchmarks/overview.md) ' dan sanal WAN Microsoft Azure kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve sanal WAN için geçerli olan ilgili kılavuza göre gruplandırılır. Sanal WAN için geçerli olmayan **denetimler** dışlandı.

Sanal WAN 'ın Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için [tam sanal WAN güvenlik taban çizgisi eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: iç trafik için güvenlik uygulama

**Rehberlik**: MICROSOFT Azure sanal WAN, özel yönlendirme özellikleri sağlar ve ExpressRoute trafiğiniz için şifreleme sağlar. Tüm rota yönetimi, sanal ağlar arasında aktarım bağlantısı sağlayan sanal hub yönlendiricisi tarafından sağlanır. ExpressRoute trafiğinizi sanal WAN ile şifrelemek, genel İnternet üzerinden veya genel IP adreslerini kullanmadan, ExpressRoute üzerinden şirket içi ağlar ve Azure sanal ağları arasında şifrelenmiş bir aktarım sağlar. 

- [ExpressRoute trafik şifrelemesi](virtual-wan-about.md#encryption)

- [Sanal WAN 'da özel bağlantı kullan](howto-private-link.md)

- [Özel yönlendirme senaryoları](scenario-any-to-any.md)

- [Özel yol tablosu belgeleri](how-to-virtual-hub-routing.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ns-2-connect-private-networks-together"></a>NS-2: özel ağları birbirine bağlama 

**Rehberlik**: Microsoft Azure ExpressRoute Azure sanal WAN ile özel bağlantı sağlar. ExpressRoute bağlantıları ortak internet üzerinden geçmediğinden ExpressRoute, tipik İnternet bağlantılarına göre daha fazla güvenilirlik, daha hızlı hız ve daha düşük gecikme süresi sunar. Ayrıca, siteden siteye (S2S) VPN veya Noktadan siteye (P2S) VPN aracılığıyla Azure 'a bağlanmak için bir sanal özel ağ kullanabilirsiniz.

- [Sanal WAN 'da ExpressRoute](virtual-wan-expressroute-portal.md)

- [Siteden siteye VPN 'ye Genel Bakış](virtual-wan-site-to-site-portal.md)

- [Noktadan siteye VPN 'ye Genel Bakış](virtual-wan-point-to-site-portal.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Paylaşılan

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: uygulamaları ve Hizmetleri dış ağ saldırılarına karşı koruma

**Rehberlik**: sanal WAN, dış ağlarda, geleneksel ağ korumalarının güvenliğini sağlamak için herhangi bir uç nokta sunmaz. Sanal ağ koruma hizmetleri 'ni kullanarak bağlı olan sanal ağlardaki (sanal bir hub 'a bağlı herhangi bir sanal ağ) kaynakları koruyabilirsiniz. 

Uygulamaları ve Hizmetleri Internet 'ten ve diğer dış konumlardan gelen kötü amaçlı olabilecek trafiğe karşı korumak için Azure Güvenlik Duvarı 'nı kullanın. 

Varlıklarınızı Azure sanal ağlarınızdaki saldırılara karşı korumak için Azure tarafından sağlanmış DDoS Koruması ' nı seçin. Ağınızla ilgili kaynaklarınızla ilgili yanlış yapılandırma risklerini algılamak için Azure Güvenlik Merkezi 'ni kullanın.

- [Azure Güvenlik Duvarı belgeleri](../firewall/index.yml)

- [Azure portal kullanarak Azure DDoS koruması standardını yönetme](../ddos-protection/manage-ddos-protection.md) 

- [Azure Güvenlik Merkezi önerileri](../security-center/recommendations-reference.md#recs-networking)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Paylaşılan

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: izinsiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: sanal WAN, Microsoft tarafından yönetilen bir hizmettir. Yerel yetkisiz giriş algılama veya yetkisiz erişim önleme özellikleri sunmaz. Ancak, Birleşik bir ilke denetimi noktasını etkinleştirmek için Azure Güvenlik Duvarı üzerinden sanal WAN 'a sunulan güvenlik özellikleri vardır. Bir Azure Güvenlik duvarı ilkesi oluşturabilir ve mevcut sanal WAN hub 'ının güvenli bir sanal hub olarak çalışmasına izin vermek için ilkeyi bir sanal WAN hub 'ına bağlayabilirsiniz ve gerekli Azure Güvenlik Duvarı kaynakları dağıtılır.

- [Sanal WAN hub 'ında Azure Güvenlik duvarını yapılandırma](howto-firewall.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: ağ güvenlik kurallarını basitleştirme

**Rehberlik**: ağ güvenlik gruplarında veya Azure Güvenlik duvarında ağ erişim denetimleri tanımlamak Için sanal ağ hizmeti etiketlerinden yararlanarak ağ güvenlik kurallarını kolaylaştırın. Hizmet etiketleri, güvenlik kuralları oluştururken belirli IP adreslerinin yerine kullanılabilir. Bir kuralın kaynak veya hedef alanında hizmet etiketi adını belirterek, karşılık gelen hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

- [Hizmet etiketlerini anlama ve kullanma](../virtual-network/service-tags-overview.md)

- [Uygulama güvenlik gruplarını anlama ve kullanma](../virtual-network/network-security-groups-overview.md#application-security-groups)

- [Azure Güvenlik Duvarı belgeleri](../firewall/index.yml)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: güvenli etki alanı adı hizmeti (DNS)

**Rehberlik**: güvenli DNS özellikleri, Azure Güvenlik Duvarı Ile sanal WAN 'a sağlanır. Azure Güvenlik duvarını, istemci sanal makinelerinden bir DNS sunucusuna olan DNS istekleri için bir aracı haline gelen bir DNS proxy 'si görevi görecek şekilde yapılandırın. Özel DNS sunucusu yapılandırmalarında, DNS çözümleme uyuşmazlığını önlemek için DNS proxy 'yi etkinleştirin ve ağ kurallarında tam etki alanı adı filtrelemeyi etkinleştirin. 

- [Azure Güvenlik Duvarı belgeleri](../firewall/index.yml)

- [Azure Güvenlik duvarı DNS ayarları](../firewall/dns-settings.md)

- [Azure Güvenlik duvarını özel bağlantıyla bir DNS Ileticisi olarak kullanma](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="identity-management"></a>Kimlik Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Kimlik Yönetimi](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory'yi standart merkezi kimlik ve kimlik doğrulaması sistemi haline getirin

**Rehberlik**: Azure Active Directory (Azure AD), Azure hizmetleri için varsayılan kimlik ve erişim yönetimi hizmetidir. Sanal WAN dahil. Kuruluşunuzun kimlik ve erişim yönetimini yönetmek için Azure AD 'yi standartlaştırın:

- Azure portal, Azure depolama, Azure sanal makineleri (Linux ve Windows), Azure Key Vault, hizmet olarak platform (PaaS) ve hizmet olarak yazılım (SaaS) uygulamaları gibi Microsoft Bulut kaynaklar.
- Kuruluşunuzun Azure veya kurumsal ağ kaynaklarınızın uygulamaları gibi kaynakları

Kuruluşunuzun bulut güvenlik pratikte Azure AD 'yi yüksek öncelikli olarak güvenli hale getirin. Güvenlik puanı özelliği sayesinde kimlik ve güvenlik duruşunuzu, yapılandırmanızın Microsoft 'un en iyi yöntem önerilerini ne kadar yakından eşleştirdireceğini ölçmenize kadar değerlendirin. Gerektiğinde, güvenlik duruşmanıza yönelik iyileştirmeler için Microsoft 'un en iyi yöntem önerilerini uygulayın.

Azure AD Ayrıca, Microsoft hesabı olmayan kullanıcıların kendi uygulamalarına ve kaynaklarına kendi dış kimlikleriyle oturum açmasını sağlayan dış kimlikleri de destekler. 

Başvurulan bağlantılarda Noktadan siteye VPN senaryolarında Azure AD kullanma hakkındaki bilgileri gözden geçirin.

- [P2S OpenVPN Protokolü bağlantıları için Azure Active Directory (AD) kiracısı oluşturma](openvpn-azure-ad-tenant-multi-app.md)

- [Kullanıcı VPN için Azure Active Directory kimlik doğrulamasını yapılandırma](virtual-wan-point-to-site-azure-ad.md)

- [Azure Active Directory'deki kiracılar](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Paylaşılan

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Tüm Azure Active Directory tabanlı erişim girişimleri için güçlü kimlik doğrulaması denetimleri kullanın

**Rehberlik**: Şu anda, sanal WAN Noktadan siteye VPN ile tümleştirme aracılığıyla Azure Active Directory (Azure AD) kimlik doğrulaması sağlanır.

Azure Active Directory (Azure AD), Azure hizmetleri için varsayılan kimlik ve erişim yönetimi hizmetidir. Azure AD, çok faktörlü Authentication ile güçlü kimlik doğrulama denetimlerini ve güçlü passwordless yöntemlerini destekler.

Azure AD, güçlü kimlik doğrulama denetimleri için aşağıdakileri önerir:

- Çok faktörlü Authentication-Azure AD çok faktörlü kimlik doğrulamasını etkinleştirin ve en iyi güvenlik uygulamaları için Azure Güvenlik Merkezi 'nde kimlik ve erişim yönetimi önerilerini izleyin. Çok faktörlü kimlik doğrulamasını zorlamak için, oturum açma koşulları ve risk faktörleri temelinde kullanıcıları veya Kullanıcı başına düzeyini seçin

- Passwordless kimlik doğrulaması – üç adet passwordless kimlik doğrulama seçeneği mevcuttur. Bunlar şunlardır. Iş için Windows Hello, Microsoft Authenticator uygulaması ve akıllı kartlar gibi şirket içi kimlik doğrulama yöntemleri

Güçlü kimlik doğrulama yönteminin en yüksek düzeyinin yönetici ve ayrıcalıklı kullanıcılar için kullanıldığından ve daha sonra diğer kullanıcılara güçlü bir kimlik doğrulama ilkesinin bir toplaması olduğundan emin olun.

- [Sanal WAN için P2S VPN 'de MFA 'yı etkinleştirme](openvpn-azure-ad-mfa.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Paylaşılan

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Azure kaynaklarına erişimi koşullara göre kısıtlayın

**Kılavuz**: Azure AD kimlik DOĞRULAMASıNı kullanarak VPN kullanıcıları için (Noktadan siteye) Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin. Kullanıcı başına çok faktörlü kimlik doğrulamasını yapılandırın veya koşullu erişimle çok faktörlü kimlik doğrulamasından yararlanın. Koşullu erişim, ikinci bir faktörün nasıl yükseltilmesi gerektiği konusunda daha ayrıntılı denetim sağlar. Çok faktörlü kimlik doğrulamasının yalnızca VPN 'e atanmasına ve Azure AD kiracısına bağlı diğer uygulamaları dışlayacak şekilde izin verebilir. 

Azure AD kimlik doğrulamasının yalnızca OpenVPN protokolünü kullanan ağ geçitleri ve Windows çalıştıran istemciler için kullanılabilir olduğunu unutmayın.

- [Koşullu erişim nedir?](../active-directory/conditional-access/overview.md)

- [Kullanıcı VPN için Azure Active Directory kimlik doğrulamasını yapılandırma](virtual-wan-point-to-site-azure-ad.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Kimlik bilgilerinin istenmeden açığa çıkma olasılığını ortadan kaldırın

**Rehberlik**: sanal WAN 'daki sıteden siteye VPN, Azure Key Vault müşteri tarafından bulunan, oluşturulan ve yönetilen önceden paylaşılan anahtarları (PSK) kullanır. Kod içinde kimlik bilgilerini tanımlamak için kimlik bilgisi tarayıcısı uygulayın. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar.

GitHub için yerel gizli dizi tarama özelliğini kullanarak kod içindeki kimlik bilgilerini veya diğer gizli dizileri bulabilirsiniz.

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [GitHub'da gizli dizi tarama](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="privileged-access"></a>Ayrıcalıklı Erişim

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ayrıcalıklı Erişim](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: İş açısından kritik sistemlere yönetici erişimini kısıtlayın

**Kılavuz**: Azure sanal WAN, iş açısından kritik sistemlere erişimi yalıtmak için Azure rol tabanlı erişim denetimleri (Azure RBAC) kullanır.

Ayrıca, iş açısından kritik sistemlere yüklenen aracılarla birlikte Active Directory Etki Alanı Denetleyicileri, güvenlik araçları ve sistem yönetimi araçları gibi iş açısından kritik erişimlerinize yönetim erişimi olan yönetim, kimlik ve güvenlik sistemlerine erişimi de kısıtlayın. Bu yönetim ve güvenlik sistemlerine zarar veren saldırganlar, iş açısından kritik varlıkların güvenliğini tehlikeye atabilir.

Tutarlı erişim denetimi sağlamak için, tüm erişim denetimi türleri kurumsal segmentasyon stratejinize hizalanmalıdır.

- [Azure bileşenleri ve başvuru modeli](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Yönetim grubu erişimi](../governance/management-groups/overview.md#management-group-access) 

- [Azure abonelik yöneticileri](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Hassas bilgileri aktarım sırasında şifreleyin

**Rehberlik**: bağlantı gereksinimleriniz IÇIN sanal WAN ile noktadan siteye VPN, sıteden siteye VPN ve şifrelenmiş Express yolu kullanın. VPN şifrelemesi, saldırganların verileri okuyamadığını veya değiştirememesini sağlamak için ' bant dışı ' saldırılardan (örneğin, trafik yakalama) geçiş sırasında verileri korur. 

- [Noktadan siteye VPN](virtual-wan-point-to-site-portal.md)

- [Siteler arası VPN](virtual-wan-site-to-site-portal.md)

- [Şifrelenmiş ExpressRoute](vpn-over-expressroute.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Paylaşılan

## <a name="asset-management"></a>Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Varlık Yönetimi](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>VY-1: Güvenlik ekibinin varlıklara yönelik risklerde görünürlük elde etmesini sağlama

**Rehber**: Güvenlik ekiplerinin Azure Güvenlik Merkezi’ni kullanarak güvenlik risklerini izlemesini sağlamak için bu ekiplere Azure kiracınız ve abonelikleriniz üzerinde Güvenlik Okuyucusu izinleri verildiğinden emin olun. 

Güvenlik ekibi sorumluluklarının yapılandırmasına bağlı olarak, güvenlik risklerinin izlenmesi merkezi güvenlik ekibinin veya yerel bir ekibin sorumluluğunda olabilir. Bu şekilde, güvenlik öngörüleri ve riskleri her zaman bir kuruluş içinde toplanmalıdır. 

Güvenlik okuyucusu izinleri bir kiracının tamamına (kök yönetim grubu) veya yönetim gruplarına veya belirli aboneliklere göre uygulanabilir. 

Not: İş yükleri ve hizmetlerin görünürlüğünü elde etmek için ek izinler gerekebilir. 

- [Güvenlik Okuyucusu Rolüne Genel Bakış](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure Yönetim Gruplarına Genel Bakış](../governance/management-groups/overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Güvenlik ekibinin varlık envanterine ve meta verilerine erişebildiğinden emin olun

**Rehberlik**: Azure kaynaklarınıza, kaynak gruplarına ve aboneliklerinize mantıksal olarak bunları bir taksonomi halinde düzenlemek için Etiketler uygulayın. Her etiket bir ad ve bir değer çiftinden oluşur. Örneğin, "Ortam" adını ve "Üretim" değerini üretimdeki tüm kaynaklara uygulayabilirsiniz. Azure sanal WAN Ayrıca, varlık şablonlarını dışarı aktarmak için Azure Resource Manager tabanlı kaynak dağıtımlarını destekler. 

- [Kaynak adlandırma ve etiketleme kararı kılavuzu](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Azure Güvenlik Merkezi varlık envanteri yönetimi](../security-center/asset-inventory.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Yalnızca onaylı Azure hizmetlerini kullanın

**Rehberlik**: onaylanmamış bir hizmet algılandığında uyarıları tetiklemeye yönelik kurallar oluşturmak Için Azure izleyici 'yi kullanın. Sanal WAN, tek bir işlemsel arabirim sağlamak için birçok ağ, güvenlik ve yönlendirme işlevlerini birlikte getirir. Sanal WAN VPN Gateway 'leri, ExpressRoute ağ geçitleri ve Azure Güvenlik Duvarı, Azure Izleyici aracılığıyla günlüğe kaydetme ve ölçümlere sahiptir. 
 

- [Sanal WAN günlükleri ve ölçümleri](logs-metrics.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>ÖÖ-5: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcıların Azure kaynakları yöneticisi ile etkileşime geçmesini sınırlamak Için Azure koşullu erişimi kullanın.

- [Azure Resource Manager erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="logging-and-threat-detection"></a>Günlüğe Kaydetme ve Tehdit Algılama

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Günlüğe Kaydetme ve Tehdit Algılama](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure kaynakları için tehdit algılamayı etkinleştirme

**Rehberlik**: sanal WAN ile noktadan siteye VPN, Azure Active Directory (Azure AD) ile tümleşiktir. Azure AD, daha karmaşık tehdit izleme ve analiz kullanım örnekleri için Azure AD raporlama 'da görüntülenebilen veya Azure Izleyici, Azure Sentinel, SıEM veya izleme araçlarıyla tümleştirilebilen aşağıdaki kullanıcı günlüklerini sağlar. Bunlar:

- Oturum aç – oturum açma raporu, yönetilen uygulamaların kullanımı ve Kullanıcı oturum açma etkinliklerinin kullanımı hakkında bilgi sağlar.
- Denetim günlükleri - Azure AD içindeki çeşitli özellikler tarafından yapılan tüm değişiklikler için günlükler aracılığıyla izlenebilirlik sağlar. Denetim günlüklerine örnek olarak, Azure AD 'de Kullanıcı, uygulama, Grup, rol ve ilke ekleme veya kaldırma gibi herhangi bir kaynak üzerinde yapılan değişiklikler verilebilir.
- Riskli oturum açma-riskli oturum açma, bir kullanıcı hesabının meşru sahibi olmayan birisi tarafından gerçekleştirilmiş olabilecek oturum açma girişimine yönelik bir göstergedir.
- Riskli oldukları belirlenen kullanıcılar - Riskli kullanıcı, güvenliği tehlikeye girmiş olabilecek bir kullanıcı hesabının göstergesidir.

Abonelikte kullanımdan kaldırılan hesaplar dahil olmak üzere çok sayıda başarısız kimlik doğrulama girişimi gibi belirli şüpheli etkinliklerde uyarı oluşturmak için Azure Güvenlik Merkezi 'ni kullanın. Temel güvenlik durumu izlemeye ek olarak, bireysel Azure işlem kaynaklarından (sanal makineler, kapsayıcılar, App Service), veri kaynakları (SQL DB ve depolama) ve Azure hizmet katmanlarından daha ayrıntılı güvenlik uyarıları toplamak için Güvenlik Merkezi 'nden tehdit koruması modülünü kullanın. Bu özellik tek tek kaynakların içindeki hesap anomalilerine görünürlük kazandırmanızı da sağlar.

- [Azure Active Directory’de denetim etkinliği raporları](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Kimlik Koruması’nı etkinleştirme](../active-directory/identity-protection/overview-identity-protection.md)

- [Sanal WAN hub 'ında Azure Güvenlik duvarını yapılandırma](howto-firewall.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Paylaşılan

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure kimlik ve erişim yönetimi için tehdit algılamayı etkinleştirin

**Rehberlik**: sanal WAN ile noktadan siteye VPN, Azure Active Directory (Azure AD) ile tümleşiktir. Azure AD, daha karmaşık tehdit izleme ve analiz kullanım örnekleri için Azure AD raporlama 'da görüntülenebilen veya Azure Izleyici, Azure Sentinel, SıEM veya izleme araçlarıyla tümleştirilebilen aşağıdaki kullanıcı günlüklerini sağlar. Bunlar:

- Oturum aç – oturum açma raporu, yönetilen uygulamaların kullanımı ve Kullanıcı oturum açma etkinliklerinin kullanımı hakkında bilgi sağlar.
- Denetim günlükleri - Azure AD içindeki çeşitli özellikler tarafından yapılan tüm değişiklikler için günlükler aracılığıyla izlenebilirlik sağlar. Denetim günlüklerine örnek olarak, Azure AD 'de Kullanıcı, uygulama, Grup, rol ve ilke ekleme veya kaldırma gibi herhangi bir kaynak üzerinde yapılan değişiklikler verilebilir.
- Riskli oturum açma-riskli oturum açma, bir kullanıcı hesabının meşru sahibi olmayan birisi tarafından gerçekleştirilmiş olabilecek oturum açma girişimine yönelik bir göstergedir.
- Riskli oldukları belirlenen kullanıcılar - Riskli kullanıcı, güvenliği tehlikeye girmiş olabilecek bir kullanıcı hesabının göstergesidir.

Abonelikte kullanımdan kaldırılan hesaplar dahil olmak üzere çok sayıda başarısız kimlik doğrulama girişimi gibi belirli şüpheli etkinliklerde uyarı oluşturmak için Azure Güvenlik Merkezi 'ni kullanın. Temel güvenlik durumu izlemeye ek olarak, bireysel Azure işlem kaynaklarından (sanal makineler, kapsayıcılar, App Service), veri kaynakları (SQL DB ve depolama) ve Azure hizmet katmanlarından daha ayrıntılı güvenlik uyarıları toplamak için Güvenlik Merkezi 'nden tehdit koruması modülünü kullanın. Bu özellik tek tek kaynakların içindeki hesap anomalilerine görünürlük kazandırmanızı da sağlar.

- [Azure Active Directory’de denetim etkinliği raporları](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Kimlik Koruması’nı etkinleştirme](../active-directory/identity-protection/overview-identity-protection.md)

- [Sanal WAN hub 'ında Azure Güvenlik duvarını yapılandırma](howto-firewall.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Paylaşılan

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Azure ağ etkinlikleri için günlüğe kaydetmeyi etkinleştirin

**Kılavuz**: Azure Izleyici Ile Azure sanal WAN izleyin. Sanal WAN, tek bir işlemsel arabirim sağlamak için birçok ağ, güvenlik ve yönlendirme işlevlerini birlikte getirir. Sanal WAN VPN Gateway 'leri, ExpressRoute ağ geçitleri ve Azure Güvenlik Duvarı, Azure Izleyici aracılığıyla günlüğe kaydetme ve ölçümlere sahiptir. Etkinlik günlüğü girdileri varsayılan olarak toplanır ve Azure portal görüntülenebilir. Azure aboneliğinize gönderilen tüm işlemleri görüntülemek için Azure etkinlik günlüklerini (eski adıyla işlemsel Günlükler ve denetim günlükleri olarak bilinir) kullanabilirsiniz.

Sanal WAN için çeşitli tanılama günlükleri de mevcuttur ve Azure portal ile sanal WAN kaynağı için yapılandırılabilir.  Log Analytics, bir olay hub 'ına ya da yalnızca bir depolama hesabına arşivleme ' ye gönderme seçeneğini belirleyebilirsiniz. 
 

- [Sanal WAN günlükleri ve ölçümleri](logs-metrics.md)

- [Azure Güvenlik Duvarı günlükleri ve ölçümleri](../firewall/logs-and-metrics.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure kaynakları için günlüğe kaydetmeyi etkinleştirin

**Kılavuz**: otomatik olarak etkinleştirilen Azure etkinlik günlükleri, okuma (Get) Işlemleri hariç Azure sanal WAN kaynaklarınız için tüm yazma IŞLEMLERINI (put, Post, Delete) içerir. Etkinlik günlükleri, sorun giderme sırasında hata bulmak veya kuruluşunuzdaki bir kullanıcının bir kaynağı nasıl değiştirdiğinizi izlemek için kullanılabilir.

Sanal WAN için Azure Kaynak günlüklerini etkinleştirin. Kaynak günlüklerini ve günlük verilerini toplamayı etkinleştirmek için Azure Güvenlik Merkezi 'ni ve Azure Ilkesi 'ni kullanabilirsiniz. Bu Günlükler, daha sonra güvenlik olaylarını araştırmak ve bu uygulamaların gerçekleştirilmesi için kritik öneme sahip olabilir.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure 'da günlüğe kaydetme ve farklı günlük türlerini anlama](../azure-monitor/essentials/platform-logs-overview.md) 

- [Azure Güvenlik Merkezi veri toplamayı anlama](../security-center/security-center-enable-data-collection.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Paylaşılan

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Güvenlik günlüğü yönetim ve analiz süreçlerini merkezileştirin

**Kılavuz**: Azure Izleyici Ile sanal WAN için güvenlik günlüğünü etkinleştirin. Sanal WAN, tek bir işlemsel arabirim sağlamak için birçok ağ, güvenlik ve yönlendirme işlevlerini birlikte getirir. Sanal WAN VPN Gateway 'leri, ExpressRoute ağ geçitleri ve Azure Güvenlik Duvarı, Azure Izleyici aracılığıyla günlüğe kaydetme ve ölçümlere sahiptir. Etkinlik günlüğü girdileri varsayılan olarak toplanır ve Azure portal görüntülenebilir. Azure aboneliğinize gönderilen tüm işlemleri görüntülemek için Azure etkinlik günlüklerini (eski adıyla işlemsel Günlükler ve denetim günlükleri olarak bilinir) kullanabilirsiniz. 

Sanal WAN için çeşitli tanılama günlükleri de mevcuttur ve Azure portal ile sanal WAN kaynağı için yapılandırılabilir. Log Analytics, bir olay hub 'ına akışa gönderin veya yalnızca bir depolama hesabına arşivleme yapın. Ayrıca, verileri Azure Sentinel 'e veya bir üçüncü taraf güvenlik bilgilerine ve olay yönetimi çözümüne etkinleştirin ve ekleyin. 
 

- [Sanal WAN günlükleri ve ölçümleri](logs-metrics.md)

- [Azure Güvenlik Duvarı günlükleri ve ölçümleri](../firewall/logs-and-metrics.md)

Azure sanal WAN güvenliği, Azure Güvenlik Duvarı üzerinden sağlanır. 

- [Azure Güvenlik Duvarı belgeleri](../firewall/overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Paylaşılan

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Günlük depolama alanının saklama süresini yapılandırın

**Rehberlik**: günlük saklama alanınızı uyumluluk, düzenleme ve iş gereksinimlerinize göre yapılandırın. Azure Izleyici 'de, Log Analytics çalışma alanı saklama dönemini kuruluşunuzun uyumluluk düzenlemelerine göre ayarlayabilirsiniz. Uzun süreli ve arşiv depolama için Azure depolama, Data Lake veya Log Analytics çalışma alanı hesapları kullanın.

- [Log Analytics veri saklama süresini değiştirme](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Azure depolama hesabı günlükleri için bekletme ilkesini yapılandırma](../storage/common/manage-storage-analytics-logs.md#configure-logging)

- [Azure Güvenlik Merkezi uyarıları ve önerileri dışarı aktarma](../security-center/continuous-export.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

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

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>OY-3: Algılama ve analiz – yüksek kaliteli uyarılar temelinde olaylar oluşturma

**Rehber**: Yüksek kaliteli uyarılar oluşturmak ve uyarıların kalitesini ölçmek için bir süreciniz olduğundan emin olun. Bu sayede geçmiş olaylardan ders çıkarabilir ve analistler için uyarıların önceliğini belirleyerek yanlış pozitif sonuçlarla zaman kaybetmelerini önleyebilirsiniz. 

Yüksek kaliteli uyarılar geçmiş olaylardan edinilen deneyime, doğrulanmış topluluk kaynaklarına ve çeşitli sinyal kaynaklarını birleştirerek ve aralarında bağıntı kurarak uyarılar oluşturmak ve temizlemek için tasarlanmış araçlara dayalı olarak oluşturulabilir. 

Azure Güvenlik Merkezi birçok Azure varlığı arasında yüksek kaliteli uyarılar sağlar. ASC veri bağlayıcısını kullanarak uyarıların Azure Sentinel’e akışını yapabilirsiniz. Azure Sentinel bir araştırmaya yönelik olayların otomatik olarak oluşturulması için gelişmiş uyarı kuralları oluşturur. 

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

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Düzenli aralıklarla saldırı simülasyonları yapın

**Rehberlik**: Gerektiğinde Azure kaynaklarınızda sızma testi veya kırmızı takım etkinlikleri gerçekleştirerek tüm kritik güvenlik bulgularının düzeltilmesini sağlayın.
Sızma testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak için Microsoft Bulut Sızma Testi Etkileşim Kuralları'na uygun hareket edin. Microsoft tarafından yönetilen bulut altyapısına, hizmetlere ve uygulamalara yönelik kırmızı takım ve canlı site sızma testi gerçekleştirmek için Microsoft'un stratejisini ve yürütme sürecini kullanın.

- [Azure'da sızma testi yapma](../security/fundamentals/pen-testing.md)

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

## <a name="endpoint-security"></a>Uç nokta güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: uç nokta güvenliği](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: uç nokta algılamayı ve yanıtını kullanın (EDR)

**Rehberlik**: müşterilerin Endpoint Detection ve yanıt ayarlarını yapılandırmasına açıkça izin verilmez. Ancak, Azure sanal WAN sunumunda kullanılan sanal makineler bu özellikleri kullanır. Başvurulan bağlantılarda bu genel yetenekler hakkında daha fazla bilgi edinin. 

- [Microsoft Defender Gelişmiş tehdit korumasına genel bakış](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Windows Server 'lar için Microsoft Defender ATP hizmeti](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Windows dışı sunucular için Microsoft Defender ATP hizmeti](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure Güvenlik Merkezi izlemesi**: Yes

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

- [Azure Güvenlik Karşılaştırması: Kimlik yönetimi](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Güvenlik Karşılaştırması: Ayrıcalıklı erişim](../security/benchmarks//security-controls-v2-privileged-access.md)

- [Azure Güvenliği En İyi Deneyimi 11 - Mimari. Tek ve birleşik güvenlik stratejisi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure'da kimlik yönetim güvenliğine genel bakış](../security/fundamentals/identity-management-overview.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Günlüğe kaydetme ve tehdit yanıtı stratejisi tanımlayın

**Rehber**: Tehditleri hızla algılar ve giderirken uyumluluk gereksinimlerini de karşılamak için bir günlüğe kaydetme ve tehdit yanıtı stratejisi oluşturun. Analistlerin tümleştirmeyle ve el ile uygulanan adımlarla ilgilenmek yerine tehditlere odaklanabilmelerini sağlamak için onlara yüksek kaliteli uyarılar ve sorunsuz deneyimler sağlamaya öncelik verin. 

Bu strateji aşağıdaki öğelerle ilgili olarak belgelenmiş rehberlik, ilke ve standartlar içermelidir: 

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