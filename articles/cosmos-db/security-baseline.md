---
title: Azure Cosmos DB için Azure Güvenlik temeli
description: Azure Cosmos DB güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: dead43f2e9f2e8913bcebde43d543b8df8d33ced
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565682"
---
# <a name="azure-security-baseline-for-azure-cosmos-db"></a>Azure Cosmos DB için Azure Güvenlik temeli

Bu güvenlik temeli, Azure Cosmos DB için [Azure Güvenlik kıyaslama sürüm 1,0](../security/benchmarks/overview-v1.md) ' den rehberlik uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar.
İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure Cosmos DB için geçerli olan ilgili kılavuza göre gruplandırılır. Azure Cosmos DB için geçerli olmayan **denetimler** dışlandı.

 
Azure Cosmos DB Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Azure Cosmos DB güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Kılavuz**: Azure özel bağlantısını kullanarak, bir Azure Cosmos hesabına özel bir uç nokta aracılığıyla bağlanabilirsiniz. Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağı üzerinden geçer ve genel İnternet’ten etkilenme olasılığı ortadan kaldırılır. Ayrıca, bir ağ güvenlik grubu (NSG) üzerinde sıkı bir giden kuralları kümesi yapılandırarak ve bu NSG 'yi alt ağınızla ilişkilendirerek veri sızdırma riskini azaltabilirsiniz.

Hizmet uç noktalarını Azure Cosmos hesabınızın güvenliğini sağlamak için de kullanabilirsiniz. Bir hizmet uç noktası etkinleştirerek Azure Cosmos hesaplarını yalnızca belirli bir Azure sanal ağının alt ağından erişime izin verecek şekilde yapılandırabilirsiniz. Azure Cosmos DB hizmeti uç noktası etkinleştirildikten sonra, bir Azure Cosmos hesabına erişimi, bir sanal ağdaki bir alt ağdan gelen bağlantılarla sınırlayabilirsiniz.

Ayrıca, IP güvenlik duvarlarını kullanarak Azure Cosmos hesabınızda depolanan verilerin güvenliğini sağlayabilirsiniz. Azure Cosmos DB, gelen güvenlik duvarı desteği için IP tabanlı erişim denetimlerini destekler. Azure portal, Azure Resource Manager şablonlarını veya Azure CLı veya Azure PowerShell aracılığıyla Azure Cosmos hesabında bir IP güvenlik duvarı ayarlayabilirsiniz.

- [Azure Özel Bağlantı'ya Genel Bakış](../private-link/private-link-overview.md)

- [Azure Cosmos DB için özel bir uç nokta yapılandırma](how-to-configure-private-endpoints.md) 

- [Güvenlik Yapılandırması ile ağ güvenlik grubu oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

- [Cosmos DB 'da IP güvenlik duvarını yapılandırma](how-to-configure-firewall.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilke yerleşik tanımları-Microsoft.DocumentDB**:

[!INCLUDE [Resource Policy for Microsoft.DocumentDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.documentdb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: sanal ağların, alt ağların ve ağ arabirimlerinin yapılandırma ve trafiğini izleme ve günlüğe kaydetme

**Kılavuz**: Azure Güvenlik Merkezi 'ni kullanın ve Azure Cosmos hesabınızla ilgili ağ kaynaklarının güvenliğini sağlamaya yardımcı olmak için ağ koruma önerilerini izleyin.

Sanal makineler Azure Cosmos hesabınızla aynı sanal ağa dağıtıldığında, veri ayıklanmasının riskini azaltmak için bir ağ güvenlik grubu (NSG) kullanabilirsiniz. NSG akış günlüklerini etkinleştirin ve trafik denetimleri için günlükleri Azure depolama hesabına gönderin. Ayrıca, NSG akış günlüklerini bir Log Analytics çalışma alanına gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır.

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="13-protect-critical-web-applications"></a>1,3: kritik Web uygulamalarını koruma

**Rehberlik**: bir etki alanı altında çalışan bir Web uygulamasının başka bir etki alanındaki kaynaklara erişmesine olanak tanımak Için, çıkış noktaları arası kaynak PAYLAŞıMı (CORS) özelliğini kullanın. Web tarayıcıları, bir Web sayfasının farklı bir etki alanındaki API 'Leri aramasını önleyen aynı kaynak ilkesi olarak bilinen bir güvenlik kısıtlaması uygular. Ancak CORS, kaynak etki alanının başka bir etki alanındaki API 'Leri çağırmasını sağlamak için güvenli bir yol sağlar. Azure Cosmos hesabınız için CORS desteğini etkinleştirdikten sonra, belirttiğiniz kurallara göre izin verilip verilmeyeceğini belirlemede yalnızca kimliği doğrulanmış istekler değerlendirilir.

- [Çıkış noktaları arası kaynak paylaşımını yapılandırma](how-to-configure-cross-origin-resource-sharing.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: Azure Cosmos DB Için Gelişmiş tehdit KORUMASı (ATP) kullanın. Azure Cosmos DB için ATP, Azure Cosmos hesaplarına erişmek veya bu hesaplara yararlanmak için olağandışı ve potansiyel olarak zararlı girişimleri algılayan ek bir güvenlik zekası katmanı sağlar. Bu koruma katmanı, tehditleri ele almanıza ve bunları Merkezi güvenlik izleme sistemleriyle tümleştirmenize olanak tanır.

DDoS saldırılarına karşı koruma sağlamak için Azure Cosmos DB örneklarınızla ilişkili sanal ağlarda DDoS koruma standardını etkinleştirin. Bilinen kötü amaçlı veya kullanılmayan Internet IP adresleriyle iletişimleri reddetmek için Azure Güvenlik Merkezi tümleşik tehdit zekasını kullanın.

- [Gelişmiş tehdit koruması Azure Cosmos DB nasıl yapılandırılır](cosmos-db-advanced-threat-protection.md)

- [DDoS korumasını yapılandırma](../ddos-protection/manage-ddos-protection.md)

- [Azure Güvenlik Merkezi tümleşik tehdit zekasını anlama](../security-center/azure-defender.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Rehberlik**: trafik denetimi için ağ güvenlik grubu (NSG) akış günlüklerini etkinleştirin ve günlükleri bir depolama hesabına gönderin. NSG akış günlüklerini bir Log Analytics çalışma alanına gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: Azure Cosmos DB Için Gelişmiş tehdit KORUMASı (ATP) kullanın. Azure Cosmos DB için ATP, Azure Cosmos hesaplarına erişmek veya bu hesaplara yararlanmak için olağandışı ve potansiyel olarak zararlı girişimleri algılayan ek bir güvenlik zekası katmanı sağlar. Bu koruma katmanı, tehditleri ele almanıza ve bunları Merkezi güvenlik izleme sistemleriyle tümleştirmenize olanak tanır. 

- [Gelişmiş tehdit koruması Cosmos DB nasıl yapılandırılır](cosmos-db-advanced-threat-protection.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Kılavuz**: Azure Cosmos hesabınıza erişmesi gereken kaynaklar için, ağ güvenlik gruplarında veya Azure Güvenlik duvarında ağ erişim denetimleri tanımlamak üzere sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. Bir kuralın uygun kaynak veya hedef alanındaki hizmet etiketi adını (ör. Azuversmosdb) belirterek, karşılık gelen hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

- [Hizmet etiketlerini kullanma hakkında daha fazla bilgi için](../virtual-network/service-tags-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Kılavuz**: Azure ilkesiyle ağ kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın. Azure Cosmos DB örneklerinizin ağ yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft.DocumentDB" ve "Microsoft. Network" ad alanlarında Azure Ilke diğer adlarını kullanın. Ayrıca, Azure Cosmos DB için yerleşik ilke tanımlarından da yararlanabilirsiniz, örneğin:

- Cosmos DB hesapları için Gelişmiş tehdit koruması dağıtma

- Cosmos DB bir sanal ağ hizmeti uç noktası kullanmalıdır

Ayrıca, Azure Resource Manager şablonları, Azure rol tabanlı erişim denetimi (Azure RBAC) ve tek bir şema tanımında ilkeler gibi anahtar ortam yapıtlarını paketleyerek büyük ölçekli Azure dağıtımlarını basitleştirmek için Azure şemaları 'nı kullanabilirsiniz. Yeni aboneliklere, ortamlara kolayca şema uygulayabilir ve sürüm oluşturma aracılığıyla denetim ve yönetime yönetim sağlayabilirsiniz.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: bir taksonomiyle mantıksal olarak organize etmek için Azure Cosmos DB dağıtımınız ile ilişkili ağ kaynakları için Etiketler kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Kılavuz**: Azure etkinlik günlüğü 'nü kullanarak ağ kaynak yapılandırmasını izleyin ve Azure Cosmos DB örneklerinizin ilişkili olduğu ağ kaynaklarına yönelik değişiklikleri tespit edin. Kritik ağ kaynaklarında yapılan değişiklikler yürürlüğe girdiğinde tetiklenecek Azure Izleyici içinde uyarılar oluşturun. 

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: Azure Cosmos DB tarafından oluşturulan güvenlik verilerini toplamak Için Azure izleyici aracılığıyla günlükleri alma. Azure Izleyici 'de, Log Analytics çalışma alanlarını kullanarak Analizi sorgulayın ve gerçekleştirin ve uzun süreli/arşiv depolama için depolama hesaplarını kullanın. Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf güvenlik olayına ve olay yönetimine (SıEM) Pano verileri ekleyebilirsiniz. 

- [Azure Cosmos DB için tanılama günlüklerini etkinleştirme](./monitor-cosmos-db.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: Azure Cosmos DB için tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına ya da Azure depolama hesabına gönderin. Azure Cosmos DB Tanılama ayarları, kaynak günlüklerini toplamak için kullanılır. Bu Günlükler istek başına yakalanır ve **veri düzlemi günlükleri** olarak da anılır. Veri düzlemi işlemlerine bazı örnekler silme, ekleme ve okumayı içerir. 

Ayrıca, Azure etkinlik günlüğü tanılama ayarlarını etkinleştirebilir ve bu günlükleri Azure Cosmos DB günlükleri için kullandığınız Log Analytics çalışma alanına gönderebilirsiniz.

- [Azure Cosmos DB için tanılama ayarlarını etkinleştirme](./monitor-cosmos-db.md)

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](../azure-monitor/essentials/activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure izleyici 'de, kuruluşunuzun uyumluluk düzenlemelerine göre Azure Cosmos DB örneklerinize ilişkin Log Analytics çalışma alanları için günlük tutma süresini ayarlayın.

- [Günlük tutma parametrelerini ayarlama](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Rehberlik**: bir çalışma alanında Log Analytics sorguları, terimleri aramak, eğilimleri belirlemek, desenleri analiz etmek ve topladığınız Azure Cosmos DB günlüklere göre birçok öngörü sağlamak için yapabilirsiniz.

- [Log Analytics çalışma alanlarında Azure Cosmos DB sorguları gerçekleştirme](monitor-cosmos-db.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Kılavuz**: Azure Güvenlik Merkezi 'nde güvenlik günlüklerinde ve olaylarda anormal etkinlikleri izlemek üzere Azure Cosmos DB Için Gelişmiş tehdit koruması 'nı etkinleştirin. Azure Cosmos DB tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına gönderin.

 

Ayrıca, bir güvenlik Orchestration otomatik yanıtı (SOAR) çözümü sağladığından Log Analytics çalışma alanınızı Azure Sentinel 'e ekleyebilirsiniz. Bu, güvenlik sorunlarını gidermek için PlayBook 'ları (otomatikleştirilmiş çözümlerin) oluşturulmasına ve kullanılmasına olanak tanır. Ayrıca, Azure Izleyici 'yi kullanarak Log Analytics çalışma alanınızda özel günlük uyarıları da oluşturabilirsiniz.

- [Azure Cosmos DB için tehdit koruması uyarıları listesi](../security-center/alerts-reference.md#alerts-azurecosmos)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Izleyici 'yi kullanarak günlük uyarıları oluşturma, görüntüleme ve yönetme](../azure-monitor/alerts/alerts-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: rol tabanlı erişim denetimi 'NI (RBAC) yapılandırmak ve Azure Cosmos DB kaynaklarında stoku sürdürmek Için Azure Portal kimlik ve erişim denetımı (IAM) bölmesini kullanabilirsiniz. Roller, kullanıcılar, gruplar, hizmet sorumluları ve Azure Active Directory (Azure AD) içindeki yönetilen kimliklere uygulanır. Bireyler ve gruplar için yerleşik roller veya özel roller kullanabilirsiniz.

Azure Cosmos DB, Azure Cosmos DB ortak yönetim senaryoları için yerleşik Azure RBAC sağlar. Azure AD 'de bir profili olan bir kişi, Azure Cosmos DB kaynaklardaki kaynaklara ve işlemlere erişim vermek veya erişimi reddetmek için bu Azure rollerini kullanıcılara, gruplara, hizmet sorumlularına veya yönetilen kimliklere atayabilir.

Ayrıca, yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü de kullanabilirsiniz.

Ayrıca, Azure Cosmos DB bazı eylemler Azure AD ve hesaba özgü ana anahtarlarla denetlenebilir. Anahtar erişimini denetlemek için ' disableKeyBasedMetadataWriteAccess ' hesap ayarını kullanın.

- [Azure Cosmos DB 'de rol tabanlı erişim denetimini anlama](role-based-access-control.md)

- [Azure Cosmos DB eylemleri kullanarak kendi özel rollerinizi oluşturun (Microsoft.DocumentDB ad alanı)](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

- [Azure AD 'de yeni bir rol oluşturma](../role-based-access-control/custom-roles.md)

- [Azure AD 'de PowerShell ile dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Kullanıcı erişimini yalnızca veri işlemleriyle sınırlama](how-to-restrict-user-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Rehberlik**: Azure Active Directory (Azure AD) veya Azure Cosmos DB ilişkili olarak varsayılan veya boş parola kavramı yoktur. Bunun yerine, Azure Cosmos DB kullanıcıların kimliğini doğrulamak ve veri ve kaynaklarına erişim sağlamak için iki tür anahtar kullanır; Ana anahtarlar ve kaynak belirteçleri. Anahtarlar dilediğiniz zaman yeniden oluşturulabilir.

- [Azure Cosmos DB verilere güvenli erişimi anlama](secure-access-to-data.md)

- [Azure Cosmos DB anahtarlarını yeniden oluşturma](./manage-with-powershell.md#regenerate-keys)

- [Azure AD kullanarak tuşlara programlı olarak erişme](certificate-based-authentication.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: uygulanamaz; Azure Cosmos DB, yönetici hesaplarını desteklemez. Tüm erişim Azure Active Directory (Azure AD) ve Azure rol tabanlı erişim denetimi (Azure RBAC) ile tümleşiktir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory çoklu oturum açma kullan (SSO)

**Rehberlik**: Azure Cosmos DB kullanıcılara yetki vermek için iki tür anahtar kullanır ve veri düzlemi düzeyinde tek Sign-On (SSO) desteklemez. Cosmos DB için denetim düzlemine erişim REST API aracılığıyla kullanılabilir ve SSO 'yu destekler. Kimlik doğrulaması yapmak için isteklerinizin yetkilendirme üst bilgisini Azure Active Directory (Azure AD) ' dan aldığınız JSON Web Token ayarlayın.

- [Cosmos DB için Azure veritabanı 'nı anlayın REST API](/rest/api/cosmos-db/)

- [Azure AD ile SSO 'yu anlama](../active-directory/manage-apps/what-is-single-sign-on.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin ve Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini izleyin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: tüm yönetim görevleri için adanmış makineler (ayrıcalıklı erişim Iş Istasyonları) kullanın

**Kılavuz**: Azure kaynaklarını açmak ve yapılandırmak için yapılandırılmış çok faktörlü kimlik doğrulaması Ile ayrıcalıklı erişim iş istasyonları (Paw) kullanın. 

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](/security/compass/privileged-access-devices)
 
- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: Azure Cosmos DB Için Gelişmiş tehdit KORUMASı (ATP) kullanın. Azure Cosmos DB için ATP, Azure Cosmos hesaplarına erişmek veya bu hesaplara yararlanmak için olağandışı ve potansiyel olarak zararlı girişimleri algılayan ek bir güvenlik zekası katmanı sağlar. Bu koruma katmanı, tehditleri ele almanıza ve bunları Merkezi güvenlik izleme sistemleriyle tümleştirmenize olanak tanır.

Ayrıca, ortamda şüpheli veya güvenli olmayan bir etkinlik olduğunda Günlükler ve uyarılar oluşturmak için Azure Active Directory (Azure AD) Privileged Identity Management (PıM) kullanabilirsiniz.

Riskli Kullanıcı davranışında uyarıları ve raporları görüntülemek için Azure AD risk algılamalarını kullanın.

- [Privileged Identity Management dağıtma (PıM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD risk algılamalarını anlama](../active-directory/identity-protection/overview-identity-protection.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: koşullu erişim ilkesinin konum koşulunu yapılandırın ve adlandırılmış konumlarınızı yönetin. Adlandırılmış konumlarla, IP adresi aralıkları veya ülkeler ve bölgeler için mantıksal gruplandırmaları oluşturabilirsiniz. Azure Cosmos DB örneklerinizin gibi hassas kaynaklarla erişimi, yapılandırılmış adlandırılmış konumlara kısıtlayabilirsiniz.

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifrelemeyi kullanarak verileri korur. Azure AD Ayrıca, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory-domain-services/tutorial-create-instance.md)

- [Azure SQL ile Azure AD kimlik doğrulamasını yapılandırma ve yönetme](../azure-sql/database/authentication-aad-configure.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlar. Ayrıca, grup üyeliklerini verimli bir şekilde yönetmek için Azure kimlik erişimi Incelemelerini, kurumsal uygulamalara erişimi ve rol atamalarını de kullanabilirsiniz. Kullanıcının erişimi, yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için düzenli olarak incelenebilir.

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: Azure Active Directory (Azure AD) Kullanıcı hesapları için Tanılama ayarları oluşturabilir, Denetim günlüklerini ve oturum açma günlüklerini, istenen uyarıları yapılandırabileceğiniz bir Log Analytics çalışma alanına gönderebilirsiniz.

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapması üzerinde uyarı

**Rehberlik**: Azure Cosmos DB Için Gelişmiş tehdit KORUMASı (ATP) kullanın. Azure Cosmos DB için ATP, Azure Cosmos hesaplarına erişmek veya bu hesaplara yararlanmak için olağandışı ve potansiyel olarak zararlı girişimleri algılayan ek bir güvenlik zekası katmanı sağlar. Bu koruma katmanı, tehditleri ele almanıza ve bunları Merkezi güvenlik izleme sistemleriyle tümleştirmenize olanak tanır.

Ayrıca, Kullanıcı kimlikleriyle ilgili şüpheli eylemleri tespit etmek üzere otomatik yanıtları yapılandırmak için Azure Active Directory (Azure AD) kimlik koruması ve risk algılamaları özelliğini de kullanabilirsiniz. Ayrıca, daha fazla araştırma için günlükleri Azure Sentinel 'e aktarabilirsiniz.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: hassas bilgileri depolayan veya işleyen Azure Cosmos DB örnekleri izlemeye yardımcı olması için Etiketler kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: geliştirme, test ve üretim için ayrı abonelikler ve/veya yönetim grupları uygulayın. Azure Cosmos DB örnekleri, sanal ağ/alt ağ tarafından ayrılır, uygun şekilde etiketlenebilir ve bir ağ güvenlik grubu (NSG) veya Azure Güvenlik duvarı içinde güvenli hale getirilir. Hassas verileri depolayan Azure Cosmos DB örneklerin yalıtılmalıdır. Azure özel bağlantısı ' nı kullanarak, bir Azure Cosmos DB örneği hesabına özel bir uç nokta aracılığıyla bağlanabilirsiniz. Özel uç nokta, sanal ağınız içindeki bir alt ağda bulunan özel IP adresleri kümesidir. Ardından, seçilen özel IP adreslerine erişimi sınırlayabilirsiniz. 

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Azure Cosmos DB için özel bir uç nokta yapılandırma](how-to-configure-private-endpoints.md)

- [Güvenlik Yapılandırması ile ağ güvenlik grubu oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: Azure Cosmos DB Için Gelişmiş tehdit koruması dağıtabilirsiniz ve bu, veritabanlarına erişmek veya veritabanına yararlanma amacıyla olağan dışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri tespit eder. Şu anda şu uyarıları tetikleyebilirler:

- Olağan dışı konumlardan erişim

- Olağan dışı veri ayıklama

Ayrıca, Azure Cosmos DB örneklerinizi erişmek için sanal makineler kullanılırken, veri kaybı olasılığını azaltmak için özel bağlantı, güvenlik duvarı, ağ güvenlik grupları ve hizmet etiketleri kullanın. Microsoft, Azure Cosmos DB için temel altyapıyı yönetir ve müşteri verilerinin kaybını veya açıklanmasını engellemek için katı denetimler uygulamıştır.

- [Gelişmiş tehdit koruması Cosmos DB nasıl yapılandırılır](cosmos-db-advanced-threat-protection.md)

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: otomatik veri tanımlama, sınıflandırma ve kayıp önleme özellikleri henüz Azure Cosmos DB için kullanılamaz. Ancak, sınıflandırma ve veri analizi için Azure Bilişsel Arama tümleştirmesini kullanabilirsiniz. Uyumluluk amaçlarıyla gerekirse bir üçüncü taraf çözümü de uygulayabilirsiniz.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure Bilişsel Arama ile Azure Cosmos DB verileri dizin oluştur](../search/search-howto-index-cosmosdb.md?bc=%2fazure%2fcosmos-db%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcosmos-db%2ftoc.json)

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: kaynaklara erişimi denetlemek için rol tabanlı erişim denetimi kullanma

**Rehberlik**: Azure Cosmos DB Azure Cosmos DB genel yönetim senaryoları Için yerleşik Azure rol tabanlı erişim denetimi (Azure RBAC) sağlar. Azure Active Directory (Azure AD) içinde bir profili olan bir kişi, Azure Cosmos DB kaynaklardaki kaynaklara ve işlemlere erişim vermek veya erişimi reddetmek için bu Azure rollerini kullanıcılara, gruplara, hizmet sorumlularına veya yönetilen kimliklere atayabilir. Rol atamaları yalnızca, Azure Cosmos hesaplarına, veritabanlarına, kapsayıcılarına ve tekliflere (verimlilik) erişimi de içeren denetim düzlemi erişimini kapsar.

- [Azure Cosmos DB 'de Azure RBAC 'yi uygulama](role-based-access-control.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Rehberlik**: Cosmos DB depolanan tüm Kullanıcı verileri varsayılan olarak geri kalanında şifrelenir. Kapatılacak denetim yok. Azure Cosmos DB, hesabın çalıştığı tüm bölgelerde AES-256 şifrelemesini kullanır.

Varsayılan olarak, Microsoft, Azure Cosmos hesabınızdaki verileri şifrelemek için kullanılan anahtarları yönetir. İsteğe bağlı olarak, kendi anahtarlarınız ile ikinci bir şifreleme katmanı eklemeyi seçebilirsiniz.

- [Azure Cosmos DB ile bekleyen şifrelemeyi anlama](database-encryption-at-rest.md)

- [Azure Cosmos DB ile bekleyen şifreleme için anahtar yönetimini anlama]()

- [Azure Cosmos DB hesabınız için müşteri tarafından yönetilen anahtarları yapılandırma](how-to-setup-cmk.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Kılavuz**: Azure Cosmos DB üretim örneklerinde değişiklik gerçekleşirken uyarı oluşturmak Için Azure etkinlik günlüğü Ile Azure izleyici 'yi kullanın.

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: otomatikleştirilmiş güvenlik açığı tarama araçlarını çalıştırma

**Rehberlik**: Azure Cosmos DB örneklerinizin Azure Güvenlik Merkezi önerilerini izleyin. 

Microsoft, Azure Cosmos DB örneklerinizi destekleyen temel ana bilgisayarlarda sistem düzeltme eki ve güvenlik açığı yönetimi gerçekleştirir. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure Güvenlik Merkezi’nde sunulan desteklenen özellikler](../security-center/security-center-services.md?tabs=features-windows)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: abonelikleriniz içinde tüm kaynakları (Azure Cosmos DB sınırlı olmamak üzere, ancak işlem, diğer depolama, ağ, bağlantı noktaları ve protokoller vb.) öğrenmek için Azure Portal veya Azure Kaynak grafiğini kullanın. Kiracınızda uygun izinlere sahip olduğunuzdan ve aboneliklerinizin içindeki kaynakların yanı sıra tüm Azure aboneliklerinin listesini belirleyebildiğinizden emin olun.

Klasik Azure kaynakları kaynak Graph aracılığıyla bulunabilse de, ileri doğru Azure Resource Manager kaynak oluşturmanız ve kullanılması kesinlikle önerilir.

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Azure rol tabanlı erişim denetimini anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Rehberlik**: Azure Cosmos DB örneklerinizin ve ilgili kaynaklarınızın etiketlerini, mantıksal olarak bir taksonomi halinde düzenlemek için meta verilerle uygulayın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Hangi Azure Cosmos DB kaynakları etiketleri destekler](../azure-resource-manager/management/tag-support.md#microsoftdocumentdb)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: Azure Cosmos DB kaynaklarıyla sınırlı olmamak üzere, varlıkları düzenlemek ve izlemek için etiketleme, yönetim grupları ve ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:

- İzin verilmeyen kaynak türleri

- İzin verilen kaynak türleri

Ayrıca, aboneliklerdeki kaynakları sorgulamak ve saptamak için Azure Kaynak grafiğini kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerine oluşturulabilecek kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:
- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](../governance/policy/samples/built-in-policies.md#general)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak Için Azure koşullu erişimini kullanın. Bu, yüksek bir güvenlik ortamındaki kaynaklarda oluşturma ve değişiklik yapılmasını önleyebilir.

- [Azure Resource Manager erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Kılavuz**: Azure ilkesiyle Cosmos DB örneklerinizin standart güvenlik yapılandırmasını tanımlayın ve uygulayın. Cosmos DB örneklerinizin yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için **Microsoft.DocumentDB** ad alanındaki Azure ilke diğer adlarını kullanın. Ayrıca, Azure Cosmos DB için yerleşik ilke tanımlarından da yararlanabilirsiniz, örneğin:
- Cosmos DB hesapları için Gelişmiş tehdit koruması dağıtma
- Cosmos DB bir sanal ağ hizmeti uç noktası kullanmalıdır

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Kılavuz**: Azure kaynaklarınız genelinde güvenli ayarları zorlamak Için Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Rehberlik**: Cosmos DB veya ilgili kaynaklarınız Için özel Azure ilke tanımları kullanıyorsanız, özel ilke tanımlarınızı kod olarak depolamak ve yönetmek için Azure Repos kullanın.

- [İlkeyi Kod iş akışları olarak tasarklama](../governance/policy/concepts/policy-as-code.md)

- [Azure Repos belgeleri](/azure/devops/repos/git/gitworkflow)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: sistem yapılandırmalarına uyarı vermek, denetlemek ve zorlamak için özel ilkeler oluşturmak üzere "Microsoft.DocumentDB" ad alanındaki Azure ilke diğer adlarını kullanın. Ayrıca, ilke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: sistem yapılandırmalarına uyarı vermek, denetlemek ve zorlamak için özel ilkeler oluşturmak üzere "Microsoft.DocumentDB" ad alanındaki Azure ilke diğer adlarını kullanın. Azure Cosmos DB örneklerinizin ve ilgili kaynaklarınızın yapılandırmasını otomatik olarak zorlamak için [Denetim], [reddetme] ve [dağıtım yok] Azure Ilkesini kullanın. 

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: Azure Cosmos DB örneklerinizi erişmek için kullanılan Azure App Service üzerinde çalışan Azure sanal makineleri veya Web uygulamaları için, Azure Cosmos DB gizli yönetimi basitleştirmek ve güvenli hale getirmek için Azure Key Vault birlikte yönetilen hizmet kimliği kullanın. Key Vault geçici silmenin etkinleştirildiğinden emin olun.

- [Azure yönetilen kimliklerle tümleştirme](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault oluşturma](../key-vault/secrets/quick-create-portal.md)

- [Key Vault kimlik doğrulaması yapma](../key-vault/general/authentication.md)

- [Key Vault erişim ilkesi atama](../key-vault/general/assign-access-policy-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Rehberlik**: Azure Cosmos DB örneklerinizi erişmek için kullanılan Azure App Service üzerinde çalışan Azure sanal makineleri veya Web uygulamaları için, Azure Cosmos DB gizli yönetimi basitleştirmek ve güvenli hale getirmek için Azure Key Vault birlikte yönetilen hizmet kimliği kullanın.

Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik ile Azure hizmetleri sağlamak için Yönetilen kimlikler kullanın. Yönetilen kimlikler, kodunuzda kimlik bilgileri olmadan Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmanıza olanak sağlar.

- [Yönetilen kimlikleri yapılandırma](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Azure yönetilen kimliklerle tümleştirme](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: kod içinde kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayın. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar.

- [Kimlik bilgisi tarayıcısını ayarlama](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: işlem dışı Azure kaynaklarına yüklenecek dosyaları önceden Tara

**Rehberlik**: Microsoft kötü amaçlı yazılımdan koruma, Azure hizmetlerini destekleyen temel alınan konakta (örneğin, Azure cosmosdb) etkinleştirilir, ancak müşteri içeriğinde çalışmaz.

Azure Cosmos DB dahil olmak üzere işlem dışı Azure kaynaklarına karşıya yüklenen tüm dosyaları önceden taramak sizin sorumluluğunuzdadır. Microsoft müşteri verilerine erişemez ve bu nedenle müşteri içeriğine ait kötü amaçlı yazılımdan koruma taramalarının sizin adınıza yapılamaz.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: Azure Cosmos DB, düzenli aralıklarla verilerinizin yedeklerini otomatik olarak alır. Veritabanı veya kapsayıcı silinirse, verileri otomatik çevrimiçi yedeklemelerden geri yüklemek için bir destek bileti oluşturabilir veya Azure Desteği ' ni çağırabilirsiniz. Azure desteği, yalnızca standart, geliştirici ve planlardan daha yüksek planlar gibi seçili planlar için kullanılabilir. Yedeklemenin belirli bir anlık görüntüsünü geri yüklemek için Azure Cosmos DB, verilerin söz konusu anlık görüntüye ait yedekleme döngüsünün süresi boyunca kullanılabilir olmasını gerektirir. 

Cosmos DB örneklerinizin kimlik bilgilerini depolamak için Key Vault kullanıyorsanız, anahtarlarınızın düzenli otomatik yedeklemelerini sağlayın.

- [Otomatik yedeklemeleri Azure Cosmos DB anlama](online-backup-and-restore.md)

- [Azure Cosmos DB verileri geri yükleme](./online-backup-and-restore.md)

- [Key Vault anahtarlarını yedekleme](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: veritabanı veya kapsayıcı silinirse, verileri otomatik çevrimiçi yedeklemelerden geri yüklemek için bir destek bileti dosyası veya Azure desteği çağırabilirsiniz. Azure desteği, yalnızca standart, geliştirici ve planlardan daha yüksek planlar gibi seçili planlar için kullanılabilir. Yedeklemenin belirli bir anlık görüntüsünü geri yüklemek için Azure Cosmos DB, verilerin söz konusu anlık görüntüye ait yedekleme döngüsünün süresi boyunca kullanılabilir olmasını gerektirir.

PowerShell kullanarak Azure Key Vault depolanan Sırlarınızın sınamasını geri yükleme. Restore-AzureKeyVaultKey cmdlet 'i belirtilen anahtar kasasında bir anahtar oluşturur. Bu anahtar, giriş dosyasındaki yedeklenen anahtarın bir çoğaltmasıdır ve özgün anahtarla aynı ada sahiptir.

- [Otomatik yedeklemeleri Azure Cosmos DB anlama](online-backup-and-restore.md)

- [Azure Cosmos DB verileri geri yükleme](./online-backup-and-restore.md)

- [Azure Key Vault parolaları geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Kılavuz**: Cosmos db içinde depolanan tüm Kullanıcı verileri REST ve aktarımda şifrelendiğinden, herhangi bir işlem gerçekleştirmeniz gerekmez. Bunu yerleştirmek için başka bir yol da, bekleyen şifrelemenin varsayılan olarak "açık" olmasını sağlar. Kapatılacak veya açık bir denetim yoktur. Azure Cosmos DB, hesabın çalıştığı tüm bölgelerde AES-256 şifrelemesini kullanır.

Anahtarları yanlışlıkla veya kötü amaçlı silmeye karşı korumak için Key Vault Soft-Delete etkinleştirin.

- [Azure Cosmos DB 'de veri şifrelemeyi anlama](database-encryption-at-rest.md)

- [Key Vault Soft-Delete etkinleştirme](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: Kuruluşunuz için bir olay yanıt kılavuzu oluşturun. Tüm personelin rollerine ek olarak algılama aşamasından olay sonrası gözden geçirme aşamasına kadar tüm olay işleme/yönetim aşamalarını tanımlayan yazılı olay yanıt planları bulunduğundan emin olun.

- [Ayrıca, NıST 'nin bilgisayar güvenliği olay Işleme kılavuzumuzu, kendi olay yanıtı planınızın oluşturulmasına yardımcı olması için de kullanabilirsiniz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

- [Azure Güvenlik Merkezi 'nde Iş akışı otomasyonlarını yapılandırma](../security-center/security-center-planning-and-operations-guide.md)

- [Kendi güvenlik olay yanıtı işleminizi oluşturma kılavuzu](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Güvenlik Yanıt Merkezi 'nin bir olayın anatomisi](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Rehberlik**: Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin bulmada ne kadar önemli olduğunu ya da uyarıyı vermek için kullanılan analizlerin yanı sıra uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu belirten güven düzeyini temel alır.

Ayrıca, abonelikleri açıkça işaretleyin (örn. üretim, üretim dışı) ve Azure kaynaklarını net bir şekilde tanımlamak ve kategorilere ayırmak için bir adlandırma sistemi oluşturun.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve açıkları belirleyip planı gerektiği şekilde gözden geçirin.

- [NıST 'nin yayını: BT planları ve özellikleri için test, eğitim ve alıştırma programlarını inceleyin](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: Microsoft Güvenlik Yanıt MERKEZI (MSRC), müşterinin verilerine izinsiz veya yetkisiz bir taraf tarafından erişildiğini belirlerse, Microsoft tarafından sizinle iletişim kurmak için güvenlik olayı iletişim bilgileri kullanılacaktır.  Sorunların çözümlendiğinden emin olmak için gerçesonra olayları gözden geçirin.

- [Azure Güvenlik Merkezi güvenlik Ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: güvenlik uyarılarını olay yanıt sisteminizle birleştirme

**Rehberlik**: sürekli dışa aktarma özelliğini kullanarak Azure Güvenlik Merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Sürekli dışa aktarma, uyarıları ve önerileri el ile veya devam eden sürekli bir biçimde dışa aktarmanız sağlar. Azure Güvenlik Merkezi veri bağlayıcısını kullanarak uyarıları Azure Sentinel 'e akışını sağlayabilirsiniz.

- [Sürekli dışarı aktarmayı yapılandırma](../security-center/continuous-export.md)

- [Uyarıların Azure Sentinel’e akışını yapma](../sentinel/connect-azure-security-center.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: güvenlik uyarılarına yanıtı otomatikleştirme

**Rehberlik**: güvenlik uyarılarında ve önerilerinde "Logic Apps" aracılığıyla yanıtları otomatik olarak tetiklemek Için Azure Güvenlik Merkezi 'Nde Iş akışı Otomasyonu özelliğini kullanın.

- [Iş akışı otomasyonu ve Logic Apps yapılandırma](../security-center/workflow-automation.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="penetration-tests-and-red-team-exercises"></a>Sızma Testleri ve Red Team Alıştırmaları

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: Penetme testleri ve Red ekibi alıştırmaları](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Azure kaynaklarınızın düzenli olarak sızma testini gerçekleştirin ve tüm kritik güvenlik bulgularını düzeltmeye dikkat edin

**Rehberlik**: Penettim testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak Için Microsoft bulut Penme test kurallarını izleyin. Microsoft tarafından yönetilen bulut altyapısına, hizmetlere ve uygulamalara yönelik kırmızı takım ve canlı site sızma testi gerçekleştirmek için Microsoft'un stratejisini ve yürütme sürecini kullanın. 

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](../security/benchmarks/overview.md)
- [Azure güvenlik temelleri](../security/benchmarks/security-baselines-overview.md) hakkında daha fazla bilgi edinin
