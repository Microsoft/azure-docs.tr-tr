---
title: Event Hubs için Azure Güvenlik temeli
description: Event Hubs güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 33417a9bda9ad4ce36dd6e14f74a53911f3c3473
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587162"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Event Hubs için Azure Güvenlik temeli

Bu güvenlik temeli, Event Hubs için [Azure Güvenlik kıyaslama sürüm 1,0](../security/benchmarks/overview-v1.md) ' den rehberlik uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar.
İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Event Hubs için geçerli olan ilgili kılavuza göre gruplandırılır. Event Hubs için geçerli olmayan **denetimler** dışlandı.

 
Event Hubs Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Event Hubs güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Kılavuz**: Azure Event Hubs sanal ağ hizmet uç noktaları ile tümleştirme, sanal ağlara bağlı sanal makineler gibi iş yüklerinden gelen, her iki uçta da güvenli hale getirilen ağ trafiği yolu ile güvenli erişim olanağı sağlar.

En az bir sanal ağ alt ağ hizmeti uç noktasına bağlandıktan sonra, ilgili Event Hubs ad alanı artık trafiği sanal ağlardaki her yerden kabul etmez. Sanal ağ perspektifinden, Event Hubs ad alanınızı bir hizmet uç noktasına bağlamak, sanal ağ alt ağından mesajlaşma hizmetine yalıtılmış bir ağ tüneli yapılandırır. 

Ayrıca, Azure özel bağlantı hizmeti 'ni kullanarak, hizmeti Event Hubs özel olarak ve güvenli bir şekilde bağlayan bir ağ arabirimi olan özel bir uç nokta da oluşturabilirsiniz. Özel uç nokta, sanal ağınızdan hizmeti etkin bir şekilde sanal ağınıza getiren özel bir IP adresi kullanır. Hizmete giden tüm trafik özel uç nokta aracılığıyla yönlendirilebilir, bu nedenle ağ geçitleri, NAT cihazları, ExpressRoute veya VPN bağlantıları ya da genel IP adresleri gerekmez. 

Azure Event Hubs ad alanınızı güvenlik duvarları kullanarak da güvenli hale getirebilirsiniz. Event Hubs, gelen güvenlik duvarı desteği için IP tabanlı erişim denetimlerini destekler. Azure portal, Azure Resource Manager şablonlarını veya Azure CLı veya Azure PowerShell aracılığıyla güvenlik duvarı kuralları belirleyebilirsiniz.

- [Azure Event Hubs sanal ağ hizmet uç noktalarını kullanma](event-hubs-service-endpoints.md)

- [Azure Event Hubs Azure özel bağlantısı ile tümleştirme](private-link-service.md)

- [Azure Event Hubs ad alanları için IP güvenlik duvarı kurallarını yapılandırma](event-hubs-ip-filtering.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: sanal ağların, alt ağların ve ağ arabirimlerinin yapılandırma ve trafiğini izleme ve günlüğe kaydetme

**Rehberlik**: Azure Güvenlik Merkezi 'ni kullanın ve azure 'daki Event Hubs kaynaklarınızın güvenliğini sağlamaya yardımcı olmak için ağ koruma önerilerini izleyin. Olay Hub 'larınızı erişmek için Azure sanal makineleri kullanıyorsanız, ağ güvenlik grubu akış günlüklerini etkinleştirin ve trafik denetimi için günlükleri bir depolama hesabına gönderin.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: dağıtılmış hizmet reddi (DDoS) saldırılarına karşı koruma sağlamak için, Olay Hub 'larınızla ilişkili sanal ağlarda DDoS koruma standardını etkinleştirin. Bilinen kötü amaçlı veya kullanılmayan Internet IP adresleriyle iletişimleri reddetmek için Azure Güvenlik Merkezi tümleşik tehdit zekasını kullanın.

- [DDoS korumasını yapılandırma](../ddos-protection/manage-ddos-protection.md)

- [Azure Güvenlik Merkezi tümleşik tehdit bilgileri hakkında daha fazla bilgi için](../security-center/azure-defender.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Rehberlik**: Olay Hub 'larınızı erişmek için Azure sanal makinelerini kullanıyorsanız, ağ güvenlik grubu akış günlüklerini etkinleştirin ve trafik denetimi için günlükleri bir depolama hesabına gönderin. Ayrıca, bir Log Analytics çalışma alanına ağ güvenlik grubu akış günlükleri gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır.

Anormal etkinlikleri araştırmak için gerekliyse, ağ Izleyicisi paket yakalamayı etkinleştirin.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

- [Ağ İzleyicisini etkinleştirme](../network-watcher/network-watcher-create.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: Olay Hub 'larınız için Azure sanal makineleri kullanıyorsanız, yük İnceleme ÖZELLIKLERINE sahip kimlikleri/IP 'Leri destekleyen Azure Marketi 'nden bir teklif seçin. Kuruluşunuz için yük incelemesini temel alan yetkisiz giriş algılama ve/veya önleme gerekmiyorsa Azure Event Hubs ' yerleşik güvenlik duvarı özelliğini kullanabilirsiniz. Güvenlik duvarı kurallarını kullanarak, sınırlı bir IP adresi aralığı veya belirli bir IP adresi için Event Hubs ad uzayına erişimi sınırlayabilirsiniz.

- [Azure Market](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Belirli bir IP adresi için Event Hubs bir güvenlik duvarı kuralı ekleme](event-hubs-ip-filtering.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Kılavuz**: Azure Ilkesi ile Azure Event Hubs ad boşluklarınızla ilişkili ağ kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın. Event Hubs ad alanlarınızın ağ yapılandırmasını denetlemek veya zorlamak üzere özel ilke tanımları oluşturmak için **Microsoft. EventHub** ve **Microsoft. Network** ad alanlarında Azure ilke diğer adlarını kullanın. Ayrıca, Azure Event Hubs ile ilgili yerleşik ilke tanımlarından da yararlanabilirsiniz, örneğin:

- Olay Hub 'ı bir sanal ağ hizmeti uç noktası kullanmalıdır.

Ek bilgiler başvurulan bağlantılarda bulunabilir.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Event Hubs ad alanı için Azure yerleşik Ilkesi](../governance/policy/samples/built-in-policies.md#event-hub)

- [Ağ için Azure Ilke örnekleri](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: sanal ağlar ve Olay Hub 'larınızla ilişkili ağ güvenliği ve trafik akışıyla ilgili diğer kaynaklar için Etiketler kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Kılavuz**: Azure etkinlik günlüğü 'nü kullanarak ağ kaynak yapılandırmasını Izleyin ve Azure Event Hubs ile ilgili ağ kaynaklarına yönelik değişiklikleri tespit edin. Kritik ağ kaynaklarında yapılan değişiklikler yürürlüğe girdiğinde tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Kılavuz**: Azure izleyici 'de, olayları bir Log Analytics çalışma alanına veya uzun vadeli arşiv depolamaya yönelik bir depolama hesabına göndermek Için etkinlik günlüğü ve Olay Hub 'ı Tanılama ayarları içindeki olay hub 'ları ile ilgili günlükleri yapılandırın.

- [Azure Event Hubs için tanılama ayarlarını yapılandırma](event-hubs-diagnostic-logs.md)

- [Azure etkinlik günlüğünü anlama](../azure-monitor/essentials/platform-logs-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Kılavuz**: Azure Event Hubs ad alanınız için tanılama ayarlarını etkinleştirin. Azure Event Hubs için üç tanılama ayarı kategorisi vardır: Arşiv günlükleri, Işletimsel Günlükler ve otomatik ölçeklendirme günlükleri. İşlem günlüklerini, Olay Hub 'ı oluşturma, kullanılan kaynaklar ve işlem durumu dahil olmak üzere Event Hubs işlemleri sırasında oluşan işlemler, özellikle de işlem türü hakkında bilgi yakalamak için etkinleştirin.

Ayrıca, Azure etkinlik günlüğü tanılama ayarlarını etkinleştirebilir ve bunları bir Azure depolama hesabına, Olay Hub 'ına veya Log Analytics çalışma alanına gönderebilirsiniz. Etkinlik günlükleri, Azure Event Hubs ve diğer kaynaklarda gerçekleştirilen işlemlere ilişkin öngörüler sağlar. Etkinlik günlüklerini kullanarak, Azure Event Hubs ad boşluklarında herhangi bir yazma işlemi (PUT, POST, SILME) için "ne, kim ve ne zaman" i belirleyebilirsiniz.

- [Azure Event Hubs için tanılama ayarlarını etkinleştirme](event-hubs-diagnostic-logs.md)

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](../azure-monitor/essentials/activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure izleyici 'de, Olay Hub 'ı ile ilgili olayları yakalamak ve gözden geçirmek için kuruluşunuzun uyumluluk düzenlemelerine göre Log Analytics çalışma alanı saklama süresini ayarlayın.

- [Log Analytics çalışma alanları için günlük saklama parametrelerini ayarlama](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Rehberlik**: anormal davranış için günlükleri çözümleyin ve izleyin ve Olay Hub 'larıyla ilgili sonuçları düzenli olarak gözden geçirin. Günlükleri gözden geçirmek ve günlük verilerinde sorgular gerçekleştirmek için Azure Izleyici Log Analytics kullanın. Alternatif olarak, Azure Sentinel veya bir üçüncü taraf sistem bilgileri ve olay yönetimi çözümünde veri ve şirket içi verileri etkinleştirebilir.

- [Log Analytics çalışma alanı hakkında daha fazla bilgi için](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Izleyici 'de özel sorgular gerçekleştirme](../azure-monitor/logs/get-started-queries.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Kılavuz**: Azure izleyici 'de, etkinlik günlüğü içinde Azure Event Hubs ile ilgili günlükleri yapılandırın ve bir Log Analytics çalışma alanına veya uzun vadeli arşiv depolaması için bir depolama hesabına Günlükler göndermek üzere tanılama ayarlarını Event Hubs. Güvenlik günlükleri ve olayları 'nda bulunan anormal etkinlikler için uyarı oluşturmak üzere Log Analytics çalışma alanını kullanın.

Alternatif olarak, Azure Sentinel 'de ve yerleşik verileri etkinleştirebilir. 

- [Azure etkinlik günlüğünü anlama](../azure-monitor/essentials/platform-logs-overview.md)

- [Azure Event Hubs için tanılama ayarlarını yapılandırma](event-hubs-diagnostic-logs.md)

- [Log Analytics çalışma alanı günlük verilerinde uyarı alma](../azure-monitor/alerts/tutorial-response.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: Azure Active Directory (Azure AD), açıkça atanması ve sorgulanabilir olması gereken yerleşik roller içerir. Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü kullanın.

- [Azure AD 'de PowerShell ile dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Rehberlik**: denetim düzlemi erişimi, Event Hubs Azure Active Directory (Azure AD) ile denetlenir. Azure AD varsayılan parola kavramına sahip değildir.

Event Hubs veri düzlemi erişimi, Yönetilen kimlikler veya Uygulama kayıtları ile paylaşılan erişim imzaları ile Azure AD aracılığıyla denetlenir. Paylaşılan erişim imzaları, Olay Hub 'larınızı bağlayan istemciler tarafından kullanılır ve herhangi bir zamanda yeniden oluşturulabilir.

- [Event Hubs için paylaşılan erişim imzalarını anlayın](authenticate-shared-access-signature.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturun. Yönetim hesaplarının sayısını izlemek için Azure Güvenlik Merkezi kimlik ve erişim yönetimi 'ni kullanın.

Ayrıca, özel yönetim hesaplarını izlemenize yardımcı olmak için Azure Güvenlik Merkezi veya yerleşik Azure Ilkeleri önerilerini kullanabilirsiniz; örneğin:

- Aboneliğinize birden fazla sahip atanmalıdır

- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır

- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır

Ek bilgiler başvurulan bağlantılarda bulunabilir.

- [Kimlik ve erişim (Önizleme) izlemek için Azure Güvenlik Merkezi 'ni kullanma](../security-center/security-center-identity-access.md)

- [Azure Ilkesini kullanma](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory çoklu oturum açma kullan (SSO)

**Rehberlik**: Microsoft Azure, Azure Active Directory (Azure AD) tabanlı kaynaklar ve uygulamalar için tümleşik erişim denetimi yönetimi sağlar. Azure AD 'yi Azure Event Hubs kullanmanın önemli bir avantajı, kimlik bilgilerinizi artık kodda depolamanızı gerektirmez. Bunun yerine, Microsoft Identity platform 'dan bir OAuth 2,0 erişim belirteci isteyebilirsiniz. Belirteç istemek için kaynak adı https: \/ /eventhubs.Azure.net/. Azure AD, uygulamayı çalıştıran güvenlik sorumlusu (bir Kullanıcı, Grup veya hizmet sorumlusu) kimliğini doğrular. Kimlik doğrulaması başarılı olursa, Azure AD uygulamaya bir erişim belirteci döndürür ve uygulama Azure Event Hubs kaynaklarına istek yetkilendirmek için erişim belirtecini kullanabilir.

- [Event Hubs kaynaklarına erişmek için Azure AD ile bir uygulamanın kimliğini doğrulama](authenticate-application.md)

- [Azure AD ile SSO 'yu anlama](../active-directory/manage-apps/what-is-single-sign-on.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin ve Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini Izleyerek Olay Hub 'ı özellikli kaynaklarınızı korumaya yardımcı olun.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: tüm yönetim görevleri için adanmış makineler (ayrıcalıklı erişim Iş Istasyonları) kullanın

**Rehberlik**: Olay Hub 'ı etkin kaynakları açmak ve yapılandırmak için yapılandırılmış çok faktörlü kimlik doğrulaması ile ayrıcalıklı erişim iş istasyonları (Paw) kullanın.

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](/security/compass/privileged-access-devices)

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: ortamda şüpheli veya güvenli olmayan bir etkinlik olduğunda günlüklerin ve uyarıların üretilmesi için Azure Active Directory (Azure AD) Privileged Identity Management kullanın. Riskli Kullanıcı davranışında uyarıları ve raporları görüntülemek için Azure AD risk algılamalarını kullanın. Ek günlük kaydı için Azure Güvenlik Merkezi risk algılama uyarılarını Azure Izleyicisine gönderin ve eylem gruplarını kullanarak özel uyarı/bildirim yapılandırın.

- [Azure AD risk algılamalarını anlama](../active-directory/identity-protection/overview-identity-protection.md)

- [Özel uyarı ve bildirim için eylem gruplarını yapılandırma](../azure-monitor/alerts/action-groups.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: IP adresi aralıklarının veya ülkelerin/bölgelerin yalnızca belirli mantıksal gruplarından erişime izin vermek Için adlandırılmış konumlar kullanın.

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: Event Hubs gibi Azure kaynakları için merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Bu, Azure rol tabanlı erişim denetimi (Azure RBAC) ile yönetici duyarlı kaynaklara izin verir.

- [ Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure AD kullanarak Event Hubs kaynaklarına erişim yetkisi verme](authorize-access-azure-active-directory.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) eski hesapları keşfetmenize yardımcı olacak Günlükler sağlar. Ayrıca, grup üyeliklerini etkin bir şekilde yönetmek, kurumsal uygulamalara erişmek ve rol atamaları için Azure kimlik erişimi Incelemelerini kullanın. Yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için, Kullanıcı erişimi düzenli olarak incelenebilir.

Ayrıca, Event Hubs ' paylaşılan erişim imzalarınızı düzenli olarak döndürün.

- [Azure AD raporlamayı anlama](../active-directory/reports-monitoring/index.yml)

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

- [Event Hubs için paylaşılan erişim imzalarını anlama](authenticate-shared-access-signature.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: herhangi bir üçüncü taraf sistem bilgileri ve olay yönetimi çözümü veya izleme aracı ile tümleştirmenize olanak tanıyan Azure Active Directory (Azure AD) oturum açma etkinliğine, denetim ve risk olay günlüğü kaynaklarına erişiminiz vardır.

Bu işlemi, Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturarak ve Log Analytics çalışma alanına denetim günlüklerini ve oturum açma günlüklerini göndererek kolaylaştırabilirsiniz. Log Analytics içinde, istenen günlük uyarılarını yapılandırabilirsiniz.

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure AD kullanarak Event Hubs kaynaklarına erişim yetkisi verme](authorize-access-azure-active-directory.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapması üzerinde uyarı

**Rehberlik**: Event Hubs etkin kaynaklarınızla ilişkili şüpheli eylemler için otomatik yanıtlar yapılandırmak üzere Azure Active Directory (Azure AD) Içindeki kimlik koruması ve risk algılama özelliklerini kullanın. Kuruluşunuzun güvenlik yanıtlarını uygulamak için Azure Sentinel aracılığıyla otomatikleştirilmiş yanıtları etkinleştirmeniz gerekir.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: destek senaryoları sırasında Microsoft 'un ilgili müşteri verilerine erişimini sağlama

**Rehberlik**: Şu anda kullanılamıyor; Müşteri Kasası, Event Hubs için henüz desteklenmiyor.

- [Müşteri Kasası tarafından desteklenen hizmetler listesi](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: hassas bilgileri depolayan veya işleyen Azure kaynaklarını izlemeye yardımcı olmak için Event Hubs ilişkili kaynaklarda Etiketler kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: isteğe bağlı olarak, geliştirme, test ve üretim için yönetim gruplarıyla ayrı abonelikler uygulayın. Event Hubs ad alanları, hizmet uç noktaları etkinleştirilmiş ve uygun şekilde etiketlenmiş sanal ağ ile ayrılmalıdır.

Azure Event Hubs ad alanınızı güvenlik duvarları kullanarak da güvence altına alabilirsiniz. Azure Event Hubs, gelen güvenlik duvarı desteği için IP tabanlı erişim denetimlerini destekler. Azure portal, Azure Resource Manager şablonlarını veya Azure CLı veya Azure PowerShell aracılığıyla güvenlik duvarı kuralları belirleyebilirsiniz.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Azure Event Hubs ad alanları için IP güvenlik duvarı kurallarını yapılandırma](event-hubs-ip-filtering.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: Olay Hub 'larınıza erişmek için sanal makineleri kullanırken, veri kaybı olasılığını azaltmak için sanal ağlar, hizmet uç noktaları, Event Hubs güvenlik duvarı, ağ güvenlik grupları ve hizmet etiketleri kullanın.

Microsoft, Azure Event Hubs için temel altyapıyı yönetir ve müşteri verilerinin kaybını veya açıklanmasını engellemek için katı denetimler uygulamıştır.

- [Azure Event Hubs ad alanları için IP güvenlik duvarı kurallarını yapılandırma](event-hubs-ip-filtering.md)

- [Azure Event Hubs sanal ağ hizmet uç noktalarını anlayın](event-hubs-service-endpoints.md)

- [Azure Event Hubs Azure özel bağlantısı ile tümleştirme](private-link-service.md)

- [Ağ güvenlik gruplarını ve hizmet etiketlerini anlayın](../virtual-network/network-security-groups-overview.md)

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: veri tanımlama, sınıflandırma ve kayıp önleme özellikleri Azure Event Hubs için henüz kullanılamıyor. Uyumluluk amacıyla gerekliyse üçüncü taraf çözümünü uygulayın.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: kaynaklara erişimi denetlemek için rol tabanlı erişim denetimi kullanma

**Kılavuz**: Azure Event Hubs, istekleri Event Hubs kaynaklara yetkilendirmek için Azure Active Directory (Azure AD) kullanımını destekler. Azure AD ile, bir kullanıcı veya uygulama hizmeti sorumlusu olabilecek bir güvenlik sorumlusu için izin vermek üzere Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanabilirsiniz.

- [Azure RBAC ve kullanılabilir Azure Event Hubs rollerini anlama](authorize-access-azure-active-directory.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Kılavuz**: Azure Event Hubs, bekleyen verileri Microsoft tarafından yönetilen anahtarlarla veya müşteri tarafından yönetilen anahtarlarla şifreleme seçeneğini destekler. Bu özellik, Azure Event Hubs verilerini bekleyen bir şekilde şifrelemek için kullanılan müşteri tarafından yönetilen anahtarlara erişimi oluşturmanıza, döndürmenize, devre dışı bırakmanızı ve iptal etmenize olanak sağlar.

- [Azure Event Hubs şifrelemek için müşteri tarafından yönetilen anahtarları yapılandırma](configure-customer-managed-key.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Kılavuz**: Azure izleyici 'Yi Azure etkinlik günlüğü ile birlikte kullanarak Azure Event Hubs 'in üretim örneklerine ve diğer önemli veya ilgili kaynaklara yönelik değişikliklerin ne zaman gerçekleştiği hakkında uyarılar oluşturun.

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: abonelikleriniz dahilinde tüm kaynakları (Azure Event Hubs ad alanları dahil) sorgulamak ve saptamak Için Azure Kaynak grafiğini kullanın. Kiracınızda uygun (okuma) izinleriniz olduğundan ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerinin listesini belirleyebildiğinizden emin olun.

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Kılavuz**: Azure kaynaklarına Etiketler uygulayarak bunları bir taksonomi halinde mantıksal olarak organize etmek için meta veriler verirsiniz.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: Azure Event Hubs ad alanlarını ve ilgili kaynakları düzenlemek ve izlemek için uygun yerlerde etiketleme, yönetim grupları ve ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:

- İzin verilmeyen kaynak türleri

- İzin verilen kaynak türleri

Ayrıca, aboneliklerdeki kaynakları sorgulamak/öğrenmek için Azure Kaynak Grafiği ' ni kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:

- İzin verilmeyen kaynak türleri

- İzin verilen kaynak türleri

Ek bilgiler başvurulan bağlantılarda bulunabilir.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](../governance/policy/samples/built-in-policies.md#general)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak üzere Azure koşullu erişimini yapılandırın.

- [Azure Resource Manager erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Rehberlik**: Azure Event Hubs dağıtımlarınız için standart güvenlik yapılandırması tanımlayın ve uygulayın. Konfigürasyonları denetlemek veya zorlamak üzere özel ilkeler oluşturmak için **Microsoft. EventHub** ad alanındaki Azure ilke diğer adlarını kullanın. Azure Event Hubs için yerleşik ilke tanımlarından da yararlanabilirsiniz:

- Olay Hub 'ındaki tanılama günlükleri etkinleştirilmelidir

- Olay Hub 'ı bir sanal ağ hizmeti uç noktası kullanmalıdır

Ek bilgiler başvurulan bağlantılarda bulunabilir.

- [Event Hubs ad alanı için Azure yerleşik Ilkesi](../governance/policy/samples/built-in-policies.md#event-hub)

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Rehberlik**: Event Hubs etkinleştirilmiş kaynaklarınız genelinde güvenli ayarları zorlamak Için Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] etkileri kullanın. 

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

 
- [Azure Ilke etkileri hakkında daha fazla bilgi için](../governance/policy/concepts/effects.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: sistem yapılandırmalarına uyarı vermek, denetlemek ve zorlamak için özel ilkeler oluşturmak üzere "Microsoft. EventHub" ad alanındaki Azure ilke diğer adlarını kullanın. Ayrıca, ilke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: sistem yapılandırmalarına uyarı vermek, denetlemek ve zorlamak için özel ilkeler oluşturmak üzere "Microsoft. EventHub" ad alanındaki Azure ilke diğer adlarını kullanın. Azure Event Hubs dağıtımlarınız ve ilgili kaynaklarınızın yapılandırmasını otomatik olarak zorlamak için [Denetim], [reddetme] ve [dağıtım yoksa dağıt] etkileri kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Kılavuz**: Azure sanal makineleri veya Olay Hub 'larınıza erişmek için kullanılan Azure App Service çalışan Web uygulamaları Için, Azure Event Hubs dağıtımlarınız için paylaşılan erişim imza yönetimini basitleştirmek ve güvenli hale getirmek üzere Azure Key Vault ile birlikte yönetilen hizmet kimliği kullanın. Key Vault, geçici silme etkin ile yapılandırıldığından emin olun.

- [Event Hubs kaynaklara erişmek için Azure Active Directory (Azure AD) ile yönetilen bir kimliğin kimliğini doğrulama](./authenticate-managed-identity.md?tabs=latest)

- [Event Hubs için müşteri tarafından yönetilen anahtarları yapılandırma](configure-customer-managed-key.md)

- [Azure yönetilen kimliklerle tümleştirme](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault oluşturma](../key-vault/secrets/quick-create-portal.md)

- [Yönetilen kimlik ile Key Vault kimlik doğrulaması sağlama](../key-vault/general/assign-access-policy-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Kılavuz**: Azure sanal makineleri veya Olay Hub 'larınız için kullanılan Azure App Service çalışan Web uygulamaları Için, Azure Event Hubs 'yi basitleştirmek ve güvenli hale getirmek için Azure Key Vault birlikte yönetilen hizmet kimliği kullanın. Key Vault, geçici silme etkin ile yapılandırıldığından emin olun.

Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik ile Azure hizmetleri sağlamak için Yönetilen kimlikler kullanın. Yönetilen kimlikler, kodunuzda kimlik bilgileri olmadan Azure Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmanıza olanak sağlar.

- [Event Hubs kaynaklarına erişmek için Azure AD ile yönetilen bir kimliğin kimliğini doğrulama](./authenticate-managed-identity.md?tabs=latest)

- [Event Hubs için müşteri tarafından yönetilen anahtarları yapılandırma](configure-customer-managed-key.md)

- [Yönetilen kimlikleri yapılandırma](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Azure yönetilen kimliklerle tümleştirme](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: kod içinde kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayın. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar.

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: işlem dışı Azure kaynaklarına yüklenecek dosyaları önceden Tara

**Kılavuz**: Azure Event Hubs, App Service, Data Lake Storage, BLOB depolama, PostgreSQL Için Azure veritabanı vb. gibi Işlem dışı Azure kaynaklarına yüklenen tüm içerikleri önceden tarayın. Microsoft bu örneklerdeki verilerinize erişemez.

Microsoft 'un kötü amaçlı yazılımdan koruma özelliği, Azure hizmetlerini destekleyen temel ana bilgisayar üzerinde etkinleştirilmiştir (örneğin, Reddır için Azure önbelleği), ancak müşteri içeriği üzerinde çalışmaz.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli otomatik arka pencerelere emin olun

**Kılavuz**: Azure Event Hubs için coğrafi olağanüstü durum kurtarmayı yapılandırma. Azure bölgelerinin veya veri merkezlerinin (hiçbir kullanılabilirlik alanı kullanılmıyorsa) çalışma süresi kapalı kalma süresi, veri işlemenin farklı bir bölgede veya veri merkezinde çalışmaya devam edebilmesi için kritik öneme sahiptir. Bu nedenle, coğrafi olağanüstü durum kurtarma ve coğrafi çoğaltma, herhangi bir kuruluş için önemli özelliklerdir. Azure Event Hubs, ad alanı düzeyinde hem coğrafi olağanüstü durum kurtarmayı hem de Coğrafi çoğaltmayı destekler. 

- [Azure Event Hubs coğrafi olağanüstü durum kurtarmayı anlama](./event-hubs-geo-dr.md#availability-zones)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Kılavuz**: Azure Event Hubs, Azure depolama hizmeti şifrelemesi (Azure SSE) ile bekleyen verilerin şifrelenmesini sağlar. Event Hubs, verileri depolamak için Azure depolama 'yı kullanır ve varsayılan olarak, Azure Storage ile depolanan tüm veriler Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir. Müşteri tarafından yönetilen anahtarları depolamak için Azure Key Vault kullanıyorsanız, anahtarlarınızın düzenli otomatik yedeklemelerini sağlayın.

Aşağıdaki PowerShell komutuyla Key Vault Sırlarınızın düzenli otomatik yedeklemelerini sağlayın: Backup-AzKeyVaultSecret

- [Bekleyen Azure Event Hubs verilerini şifrelemek için müşteri tarafından yönetilen anahtarları yapılandırma](configure-customer-managed-key.md)

- [Key Vault gizli dizileri yedekleme](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: yedeklenen müşteri tarafından yönetilen anahtarların sınamasını geri yükleme.

- [Azure 'da Anahtar Kasası anahtarlarını geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: anahtarları yanlışlıkla veya kötü amaçlı silmeye karşı korumak için Key Vault geçici silme özelliğini etkinleştirin. Azure Event Hubs, müşteri tarafından yönetilen anahtarların geçici silme ve bu şekilde temizlenme gerektirir.

Event Hubs verileri yakalamak için kullanılan Azure depolama hesabı için geçici silme yapılandırın. Bu özelliğin henüz Azure Data Lake Storage Gen 2 için desteklenmediğini unutmayın.

- [Key Vault 'da geçici silme nasıl etkinleştirilir](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Anahtarlar içeren bir Anahtar Kasası ayarlama](configure-customer-managed-key.md)

- [Azure Depolama blobları için geçici silme](/azure/storage/blobs/soft-delete-blob-overview)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: personel rollerinin yanı sıra olay işleme/yönetim aşamalarını tanımlayan yazılı olay yanıt planları olduğundan emin olun.

- [Azure Güvenlik Merkezi 'nde Iş akışı otomasyonlarını yapılandırma](../security-center/security-center-planning-and-operations-guide.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Rehberlik**: Güvenlik Merkezi, her uyarıya katılma sırasını önceliklendirmenize yardımcı olmak için uyarılara önem derecesi atar. böylece, bir kaynak tehlikeye girdiğinde, hemen bu işe başlayabilirsiniz. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizte ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır.

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

**Rehberlik**: Penettim testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak Için Microsoft bulut Penme test kurallarını izleyin. Microsoft 'un, Microsoft tarafından yönetilen bulut altyapısına, hizmetlerine ve uygulamalarına karşı kırmızı ekip oluşturma ve canlı site sızma testi stratejisini kullanın. 

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](../security/benchmarks/overview.md)
- [Azure güvenlik temelleri](../security/benchmarks/security-baselines-overview.md) hakkında daha fazla bilgi edinin
