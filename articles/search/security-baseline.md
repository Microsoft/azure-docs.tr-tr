---
title: Azure Bilişsel Arama için Azure Güvenlik temeli
description: Azure Bilişsel Arama güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 313ac05d8e4fdc19736d0c35285c2b854ec26968
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589202"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Azure Bilişsel Arama için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 1,0](../security/benchmarks/overview-v1.md) ' dan Azure bilişsel arama kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure bilişsel arama yönelik ilgili kılavuza göre gruplandırılır. **Denetimler** Azure bilişsel arama için geçerli değildir veya müşteri hariç tutulur.

Azure Bilişsel Arama 'ın Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam azure bilişsel arama güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: tüm Microsoft Azure sanal ağ alt ağ dağıtımlarının "en az ayrıcalıklı" erişim şeması uygulamak için kurallarla uygulanmış bir ağ güvenlik grubu olduğundan emin olun. Yalnızca uygulamanızın güvenilen bağlantı noktalarına ve IP adresi aralıklarına erişime izin verin. Azure Bilişsel Arama 'yi, sanal ağınızdan hizmetlere özel erişimi etkinleştirmek için uygun durumlarda Azure özel uç noktasıyla dağıtın.

Bilişsel Arama, ağ erişim denetimi listelerini yönetmek için ek ağ güvenliği işlevselliğini da destekler. Arama hizmetinizi, Güvenlik Duvarı özelliğini kullanarak belirli genel IP adresi aralıklarından erişimi kısıtlayarak yalnızca güvenilen kaynaklarla iletişime izin verecek şekilde yapılandırın.

- [Azure Bilişsel Arama için özel uç noktaları yapılandırma](service-create-private-endpoint.md)

- [Azure Bilişsel Arama güvenlik duvarını yapılandırma](service-configure-firewall.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: sanal ağların, alt ağların ve NIC 'lerin yapılandırmasını ve trafiğini izleyin ve günlüğe kaydedin

**Rehberlik**: bilişsel arama doğrudan bir sanal ağa dağıtılamaz. Ancak, istemci uygulamanız veya veri kaynaklarınız bir sanal ağda yer alıyorsa, buluttaki bir arama hizmetine gönderilen istekler dahil olmak üzere ağ içi bileşenlere yönelik trafiği izleyebilir ve günlüğe kaydedebilirsiniz. Standart öneriler, bir ağ güvenlik grubu akış günlüğünü etkinleştirmeyi ve günlükleri Azure depolama 'ya veya bir Log Analytics çalışma alanına göndermeyi içerir. İsteğe bağlı olarak trafik desenlerine yönelik Öngörüler için Trafik Analizi kullanabilirsiniz.

- [Ağ güvenlik grubu akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: bilişsel arama, dağıtılmış bir hizmet reddi saldırısına karşı mücadele etmek için belirli bir özellik sağlamaz, ancak genel koruma için bilişsel arama hizmetinize ilişkili sanal ağlarda DDoS koruma standardını etkinleştirebilirsiniz.

- [DDoS korumasını yapılandırma](../ddos-protection/manage-ddos-protection.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Rehberlik**: bilişsel arama hizmetinize bağlanan Azure sanal MAKINELERINI (VM) koruyan ağ güvenlik grupları için ağ güvenlik grubu akış günlüklerini etkinleştirin. Trafik denetimi için günlükleri Azure depolama hesabına gönderin. 

Anormal etkinlikleri araştırmak için gerekliyse ağ Izleyicisi paket yakalamayı etkinleştirin.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Ağ İzleyicisini etkinleştirme](../network-watcher/network-watcher-create.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: bilişsel arama ağ izinsiz giriş algılamayı desteklemez, ancak yetkisiz bir risk azaltma olarak, bilişsel arama hizmeti tarafından kabul edilen IP adreslerini belirtmek için güvenlik duvarı kurallarını yapılandırabilirsiniz. Genel internet 'ten gelen arama trafiğini dışarıda tutmak için özel bir uç nokta yapılandırın.

- [Veri şifreleme için müşteri tarafından yönetilen anahtarları yapılandırma](search-security-manage-encryption-keys.md)

- [Dizinlerden ve eş anlamlı eşlemlerden müşteri tarafından yönetilen anahtar bilgileri alma](search-security-get-encryption-keys.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Rehberlik**: bilişsel arama ' de Dizin oluşturucular ve becerileri kullanıyorsanız, dış kaynaklara bağlanma izni olan bir IP adresi aralığını temsil etmek için hizmet etiketleri kullanın. 

Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adı (örneğin, AzureCognitiveSearch) belirterek kaynaklara giden trafiğe izin verin veya reddedin. 

- [Sanal ağ hizmeti etiketleri](../virtual-network/service-tags-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: arama hizmetinizi bir sanal ağla tümleştirmek için Azure özel uç noktası ile bilişsel arama yapılandırabilirsiniz.  Ağ güvenlik grupları ve ağ güvenliği ve trafik akışıyla ilgili diğer kaynaklar için kaynak etiketleri kullanın. Tek tek ağ güvenlik grubu kuralları için "Açıklama" alanını kullanarak bir ağdan gelen/giden trafiğe izin veren kuralları belgeleyin. 
 

Tüm kaynakların etiketlerle oluşturulmasını ve mevcut etiketlenmemiş kaynakları bilgilendirmesini sağlamak için etiketlemeyle ilgili yerleşik Azure Ilke tanımlarından herhangi birini ("etiket ve onun değeri gerektir" etkileri gibi) kullanın. 

Azure PowerShell veya Azure CLı kullanarak, etiketlerine göre kaynakları arayabilir veya bunlarla ilgili eylemler gerçekleştirebilirsiniz.  

 
- [Bilişsel Arama için özel uç nokta oluşturma](service-create-private-endpoint.md) 

 
 
- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Azure sanal ağı oluşturma](../virtual-network/quick-create-portal.md) 

 
- [Ağ güvenlik grubu kuralları ile ağ trafiğini filtreleme](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: uç nokta cihazları, ağ kaynakları ve diğer güvenlik sistemleri tarafından oluşturulan güvenlik verilerini toplamak Için Azure izleyici aracılığıyla bilişsel arama ile ilişkili Günlükler. Azure Izleyici 'de analiz sorgulamak ve gerçekleştirmek için Log Analytics çalışma alanlarını kullanın ve uzun süreli ve arşiv depolama için Azure depolama hesaplarını kullanın. Alternatif olarak, bu verileri Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye etkinleştirebilir ve bu verileri ayarlayabilirsiniz.
 

 
- [Azure Izleyici ve üçüncü taraf SıEM tümleştirmesi ile çalışmaya başlama](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)
 

 
- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md) 
 

 
- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: tanılama ve işletimsel Günlükler bilişsel arama ayrıntılı işlemlerine ilişkin öngörüler sağlar ve hizmeti izlemek ve hizmetinize erişen iş yükleri için faydalıdır.  Tanılama verilerini yakalamak için günlük bilgilerinin depolanacağı yeri belirterek günlüğe kaydetmeyi etkinleştirin.
 

 
- [Azure Bilişsel Arama günlük verilerini toplama ve çözümleme](search-monitor-logs.md) 

 
- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Search**:

[!INCLUDE [Resource Policy for Microsoft.Search 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.search-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Rehberlik**: Tanılama ölçümlerine akış yapan geçmiş veriler, varsayılan olarak 30 gün boyunca bilişsel arama tarafından korunur. Daha uzun bekletme için, günlüğe kaydedilen olayları ve ölçümleri kalıcı hale getiren bir depolama seçeneğini belirten ayarı etkinleştirdiğinizden emin olun.
 

 
Azure Izleyici 'de kuruluşunuzun uyumluluk düzenlemelerine göre Log Analytics çalışma alanı saklama süresini ayarlayın. Uzun süreli ve arşiv depolama için Azure depolama hesaplarını kullanın. 
 

 
- [Log Analytics veri saklama süresini değiştirme](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period) 

 
- [Azure depolama hesabı günlükleri için bekletme ilkesini yapılandırma](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#enable-logs)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Rehberlik**: anormal davranışlar için bilişsel arama hizmetinizdeki günlükleri çözümleyin ve izleyin. Günlükleri gözden geçirmek ve günlük verilerinde sorgular gerçekleştirmek için Azure Izleyici Log Analytics kullanın. Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz. 

 
 
- [Bilişsel Arama günlük verilerini toplama ve analiz etme](search-monitor-logs.md)
 
- [Power BI 'de arama günlüğü verilerini görselleştirme](search-monitor-logs-powerbi.md)
 

 
- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)
 

 
- [Log Analytics hakkında bilgi edinin](../azure-monitor/logs/log-analytics-tutorial.md)
 

 
- [Azure Izleyici 'de özel sorgular gerçekleştirme](../azure-monitor/logs/get-started-queries.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Rehberlik**: Güvenlik Merkezi 'ni, güvenlik günlükleri ve olayları 'nda bulunan anormal etkinlikleri izlemek ve uyarmak için Log Analytics çalışma alanıyla kullanın. Alternatif olarak, Azure Sentinel 'de ve yerleşik verileri etkinleştirebilir.
 

 
- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)
 

 
- [Azure Güvenlik Merkezi 'nde uyarıları yönetme](../security-center/security-center-managing-and-responding-alerts.md)
 

 
- [Log Analytics günlük verilerinde uyarı alma](../azure-monitor/alerts/tutorial-response.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Kılavuz**: Azure rol tabanlı erişim denetimi (Azure RBAC), Azure kaynaklarına erişimi rol atamaları aracılığıyla yönetmenizi sağlar. Bu rolleri kullanıcılara atayabilir, hizmet sorumlularını ve yönetilen kimlikleri gruplara atayabilirsiniz. Belirli kaynaklar için önceden tanımlanmış yerleşik roller vardır ve Azure CLI, Azure PowerShell veya Azure portal gibi araçlardan yararlanarak bu rollerin envanteri çıkarılabilir ve bunlar sorgulanabilir.

Bilişsel Arama roller, hizmet düzeyi yönetim görevlerini destekleyen izinlerle ilişkilendirilir. Bu roller hizmet uç noktasına erişim vermez. Uç noktaya karşı işlemlere (Dizin Yönetimi, dizin oluşturma ve arama verilerinde sorgular gibi) erişim, isteğin kimliğini doğrulamak için API anahtarlarını kullanır.

- [Azure Bilişsel Arama yönetici erişimi için rolleri ayarlama](search-security-rbac.md)

- [Azure Bilişsel Arama hizmeti için API anahtarları oluşturma ve yönetme](search-security-api-keys.md)

- [PowerShell ile Azure Active Directory (Azure AD) içinde dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: bilişsel arama dizinleri ve işlemleri yönetmek için kullanılabilecek herhangi bir yerel düzey veya Azure Active Directory (Azure AD) yönetici hesabı kavramını içermez. 

Yönetim işlemleri için açıkça atanması gereken Azure AD yerleşik rollerini kullanın. Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü çağırın.

- [Bilişsel Arama 'de Yönetim erişimi için rolleri kullanma](search-security-rbac.md)

- [Azure AD 'de PowerShell ile dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Azure Active Directory ile çoklu oturum açma (SSO) kullanın

**Rehberlik**: Azure Resource Manager aracılığıyla desteklenen yönetim işlemleri için arama hizmeti bilgilerine erişmek üzere Azure Active Directory (Azure AD) ile SSO kimlik doğrulaması kullanın. 

Kuruluşunuzun önceden var olan kimlikleri olan hizmet için SSO 'yu etkinleştirerek kimlik ve kimlik bilgileri sayısını azaltmak için bir işlem oluşturun.

- [Azure AD ile SSO 'yu anlama](../active-directory/manage-apps/what-is-single-sign-on.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) 'ın çok faktörlü kimlik doğrulama özelliğini etkinleştirin ve Güvenlik Merkezi 'nin kimlik ve erişim önerilerini izleyin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: tüm yönetim görevleri için adanmış makineler (ayrıcalıklı erişim Iş Istasyonları) kullanın

**Rehberlik**: çok faktörlü kimlik doğrulaması ile oturum açmak ve Azure kaynaklarına erişmek için yapılandırılmış bir ayrıcalıklı erişim iş istasyonu (Paw) kullanın.
 

 
- [Güvenli, Azure tarafından yönetilen iş istasyonlarını anlayın](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

 
- [Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: ortamda şüpheli veya güvenli olmayan etkinliklerin ne zaman oluştuğunu algılamak için Azure Active Directory (Azure AD) güvenlik raporları ve izleme kullanın. Kimlik ve erişim etkinliğini izlemek için Güvenlik Merkezi 'ni kullanın.

- [Riskli etkinlik bayrağıyla işaretlenen Azure AD kullanıcılarını belirleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Güvenlik Merkezi’nde kullanıcıların kimliğini ve erişim etkinliğini izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Kılavuz**: Azure bilişsel arama 'de hizmet düzeyi yönetim görevleri için merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD kimlikleri, arama hizmeti uç noktasına erişim vermez.  Dizin Yönetimi, dizin oluşturma ve arama verilerinde sorgular gibi işlemlere erişim API anahtarları aracılığıyla kullanılabilir.

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure Bilişsel Arama hizmeti için API anahtarları oluşturma ve yönetme](search-security-api-keys.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlar. Grup üyeliklerini verimli bir şekilde yönetmek için Azure AD 'nin kimlik ve erişim gözden geçirmeleri ' nı kullanın, kurumsal uygulamalara ve rol atamalarına erişin. Yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için, Kullanıcı erişimi düzenli olarak incelenebilir. 

Dizin Yönetimi, dizin oluşturma ve sorgular gibi arama hizmeti uç noktasındaki etkinlik için Bilişsel Arama tanılama günlüklerini gözden geçirin.

- [Azure AD raporlamayı anlama](/azure/active-directory/reports-monitoring/)

- [Azure AD kimlik ve erişim gözden geçirmelerini kullanma](../active-directory/governance/access-reviews-overview.md)

- [Azure Bilişsel Arama işlemlerini ve etkinliklerini izleme](search-monitor-usage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: Azure Active Directory erişimi (Azure AD) oturum açma etkinliği, denetim ve risk olay günlüğü kaynakları, herhangi bir SIEM veya izleme aracıyla tümleştirmenize imkan tanır.

Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturup Log Analytics çalışma alanına denetim günlüklerini ve oturum açma günlüklerini göndererek bu işlemi kolaylaştırın. Log Analytics çalışma alanı içinde istenen uyarıları yapılandırın.

- [Azure Izleyici ile Azure etkinlik günlüklerini tümleştirme](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapmasından uyar

**Rehberlik**: otomatik yanıtları yapılandırmak için Azure Active Directory (Azure AD) kimlik koruması özelliklerini kullanın, Kullanıcı kimlikleriyle ilgili şüpheli eylemleri tespit edin. Gerektiğinde daha fazla araştırma için Azure Sentinel 'e veri alma.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: hassas bilgileri depolayan veya işleyen Azure kaynaklarını izlemeye yardımcı olması için etiketleri kullanın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: geliştirme, test ve üretim için ayrı abonelikler ve/veya yönetim grupları uygulayın. Kaynaklar, sanal ağ/alt ağ ile ayrılmalıdır, uygun şekilde etiketlenebilir ve bir ağ güvenlik grubu ya da Azure Güvenlik duvarı içinde güvenliği sağlanmış olmalıdır. Hassas verileri depolayan veya işleyen kaynaklar yalıtılmalıdır. Bilişsel Arama için özel bir uç nokta yapılandırmak üzere özel bağlantıyı kullanın.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Bilişsel Arama için özel uç nokta oluşturma](service-create-private-endpoint.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: hassas bilgilerin yetkisiz aktarımını izlemek ve bilgi güvenliği uzmanlarına uyarı ederken bu tür aktarımları engellemek için ağ Perimetrelerinde Azure Marketi 'nde üçüncü taraf bir çözüm kullanın.

Microsoft, temel platformu yönetir ve müşterinin veri kaybına ve açığa çıkmasına karşı tüm müşteri içeriklerini gizli ve koruyucuları olarak değerlendirir. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: veri tanımlama, sınıflandırma ve kayıp önleme özellikleri henüz bilişsel arama için kullanılabilir değil. Uyumluluk amacıyla gerekliyse bir üçüncü taraf çözümü uygulayın. 

Microsoft, temel platformu yönetir ve müşterinin veri kaybına ve açığa çıkmasına karşı tüm müşteri içeriklerini gizli ve koruyucuları olarak değerlendirir. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: kaynaklara erişimi yönetmek için Azure RBAC kullanma

**Rehberlik**: hizmet yönetimi için, anahtar ve yapılandırmaya erişimi yönetmek üzere Azure rol tabanlı erişim denetimi (Azure RBAC) kullanın. Dizin oluşturma ve sorgular gibi içerik işlemleri için Bilişsel Arama, kimlik tabanlı erişim denetimi modeli yerine anahtarlar kullanır. Anahtarlara erişimi denetlemek için Azure RBAC kullanın. 

 
- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md)  
 
 
- [Bilişsel Arama için yönetici erişimi için roller kullanma](search-security-rbac.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Rehberlik**: bilişsel arama, Microsoft tarafından yönetilen anahtarlarla bekleyen dizinli içeriği otomatik olarak şifreler. Daha fazla koruma gerekiyorsa, Azure Key Vault içinde oluşturduğunuz ve yönettiğiniz anahtarları kullanarak ikinci bir şifreleme katmanıyla varsayılan şifrelemeyi destekleyebilirsiniz.

- [Azure Bilişsel Arama veri şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırma](search-security-manage-encryption-keys.md)

- [Azure’da bekleyen veri şifrelemesini anlama](../security/fundamentals/encryption-atrest.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Kılavuz**: bilişsel arama üretim örneklerine ve diğer önemli veya ilgili kaynaklara yönelik değişikliklerin ne zaman gerçekleştiği hakkında uyarı oluşturmak Için Azure etkinlik günlüğü Ile Azure izleyici 'yi kullanın. 

 
- [Azure etkinlik günlüğü olayları için uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Bilişsel Arama etkinlikleri için uyarı oluşturma](search-monitor-logs.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: aboneliklerinizde tüm kaynakları sorgulamak ve (işlem, depolama, ağ, bağlantı noktaları, protokoller vb.) sorgulamak Için Azure Kaynak grafiğini kullanın.

Kiracınızda uygun (okuma) izinlere sahip olun ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerini numaralandırın.

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Rehberlik**: verileri bir taksonomi halinde mantıksal olarak düzenlemek için meta verilerle Azure kaynaklarına Etiketler uygulayın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: varlıkları düzenlemek ve izlemek için etiketleme, yönetim grupları ve uygun durumlarda ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynakları envanterini tanımlama ve sürdürme

**Rehberlik**: bilişsel arama ' de dizin oluşturma ve beceri işlemeyle Ilgili onaylanan Azure kaynaklarının bir listesini tanımlayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: daha önce kuruluş ilkelerinize ve standartlara göre kullanım Için onaylanmış Azure kaynakları envanterini tanımlamanız ve ardından Azure Ilkesi veya Azure Kaynak Grafiği Ile onaylanmamış Azure kaynaklarını izlemeniz önerilir.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:
- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

Aboneliklerinizin içindeki kaynakları sorgulamak veya bulmayı sağlamak için Azure Kaynak Grafiği 'ni kullanın. Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](/azure/governance/policy/samples/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak Için Azure koşullu erişimi kullanın.  

 
Özellikle de Bilişsel Arama içerikle ilgili olan tüm diğer işlemler için isteklerin kimliğini doğrulamak için kullanılan anahtarlara erişimi denetleyin.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Kılavuz**: Azure bilişsel arama kaynaklarınızın yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak Için "Microsoft. Search" ad alanındaki Azure ilke diğer adlarını kullanın. Ayrıca, gibi Bilişsel Arama Hizmetleri için yerleşik Azure Ilke tanımlarını da kullanabilirsiniz:

Azure kaynakları için denetim günlüğünü etkinleştirme

Azure Resource Manager, yapılandırmaların kuruluşunuzun güvenlik gereksinimlerini karşıladığından emin olmak için gözden geçirilmesi gereken JavaScript Nesne Gösterimi (JSON) içinde şablonu dışarı aktarma özelliğine sahiptir.

Azure Güvenlik Merkezi önerilerini Azure kaynaklarınız için güvenli bir yapılandırma temeli olarak da kullanabilirsiniz.

- [Azure Bilişsel Arama için Azure Ilke mevzuat uyumluluk denetimleri](security-controls-policy.md)

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Rehberlik**: bilişsel arama hizmet kaynaklarınız genelinde güvenli ayarları zorlamak Için Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] etkileri kullanın. 

Azure Resource Manager şablonları, kuruluşunuz tarafından gereken Azure kaynaklarınızın güvenlik yapılandırmasını sürdürmek için kullanılabilir. 

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

- [Azure Bilişsel Arama için Azure Ilke mevzuat uyumluluk denetimleri](security-controls-policy.md)

- [Uyumluluğu zorunlu tutmak için ilkeleri oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager şablonlarına genel bakış](../azure-resource-manager/templates/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilke tanımları kullanıyorsanız, kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure devops veya Azure Repos kullanın.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow)

- [Azure Repos belgeleri](/azure/devops/repos/index)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: Azure ilkesini kullanarak bilişsel arama hizmet kaynaklarınız için standart güvenlik yapılandırması tanımlayın ve uygulayın. 

Ağ yapılandırmasını denetlemek veya zorlamak için özel ilkeler oluşturmak üzere diğer adlar kullanın. Ayrıca, belirli kaynaklarınızla ilgili yerleşik ilke tanımlarından da yararlanabilirsiniz. 

Ayrıca, yapılandırma değişikliklerini dağıtmak ve ilke özel durumlarını yönetmek için Azure Otomasyonu ' nu da kullanabilirsiniz. 

- [Azure Bilişsel Arama için Azure Ilke mevzuat uyumluluk denetimleri](security-controls-policy.md)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: bilişsel arama hizmeti kaynaklarınızın temel taranmasını gerçekleştirmek Için Güvenlik Merkezi 'ni kullanın.  Ayrıca, Azure Ilkesi ' ni kullanarak kaynak yapılandırmalarınızı uyarır ve denetleyin. 

- [Azure Güvenlik Merkezi 'nde öneriler nasıl düzeltileceği](../security-center/security-center-remediate-recommendations.md)

- [Azure Bilişsel Arama için Azure Ilke mevzuat uyumluluk denetimleri](security-controls-policy.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: bulut uygulamalarınız için gizli yönetimi basitleştirmek üzere Azure yönetilen kimliklerini Azure Key Vault birlikte kullanın.

- [Azure kaynakları için Yönetilen kimlikler kullanma](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Key Vault oluşturma](../key-vault/general/quick-create-portal.md)

- [Yönetilen kimlik ile Key Vault kimlik doğrulaması sağlama](../key-vault/general/assign-access-policy-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Rehberlik**: Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik kullanarak Key Vault ve Dizin Oluşturucu veri kaynakları gibi diğer azure hizmetlerine bilişsel arama erişim sağlamak Için Azure yönetilen kimliği kullanın. Yönetilen kimlikler, kodunuzda kimlik bilgileri olmadan Azure Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmanıza olanak sağlar. 

- [Yönetilen kimlik kullanarak veri kaynağına bir Dizin Oluşturucu bağlantısı kurma](search-howto-managed-identities-data-sources.md)

- [Yönetilen bir kimlik kullanarak veri şifreleme için müşteri tarafından yönetilen anahtarları yapılandırma](search-security-manage-encryption-keys.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: işlem dışı Azure kaynaklarına yüklenecek dosyaları önceden Tara

**Rehberlik**: bilişsel arama, BLOB depolama, Azure SQL veritabanı gibi Işlem dışı Azure kaynaklarına yüklenen tüm içerikleri önceden tarayın. 

İşlem dışı Azure kaynaklarına karşıya yüklenen tüm içerikleri önceden taramak sizin sorumluluğunuzdadır. Microsoft müşteri verilerine erişemez ve bu nedenle müşteri içeriğine ait kötü amaçlı yazılımdan koruma taramalarının sizin adınıza yapılamaz.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: kötü amaçlı yazılımdan koruma yazılımlarının ve imzaların güncelleştirildiğinden emin olun

**Rehberlik**: bilişsel arama için geçerli değildir. Kötü amaçlı yazılımdan koruma çözümlerinin kaynaklarına yüklenmesine izin vermez. Temel alınan platform için Microsoft, kötü amaçlı yazılımdan koruma yazılımlarını ve imzalarını güncelleştirmeyi işler.  

 
Kuruluşunuza ait olan ve arama çözümünüzde kullanılan işlem kaynakları için, &amp; tüm uç noktaların en son imzalarla güncel olduğundan emin olmak Için Güvenlik Merkezi, işlem uygulamaları ' nda öneriler ' i izleyin. Linux için, üçüncü taraf bir kötü amaçlı yazılımdan koruma çözümü kullanın.

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli Otomatik yedeklemeli UPS sağlayın

**Rehberlik**: bir arama hizmetinde depolanan içerik Azure Backup veya başka bir yerleşik mekanizmaya karşı yedeklenemez, ancak uygulama kaynak kodu ve birincil veri kaynaklarından bir dizini yeniden oluşturabilir veya dizini oluşturulmuş içeriği almak ve depolamak için özel bir araç oluşturabilirsiniz. 

 
- [GitHub dizini-yedekleme-geri yükleme örneği](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: bilişsel arama şu anda bir arama hizmetindeki veriler için Otomatik yedeklemeyi desteklemiyor ve el ile gerçekleştirilen bir işlem aracılığıyla yedeklenmelidir. Ayrıca, Azure Key Vault içinde müşteri tarafından yönetilen anahtarları da yedekleyebilirsiniz.
 

- [Azure Bilişsel Arama dizinini yedekleme ve geri yükleme](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Azure 'da Key Vault anahtarlarını yedekleme](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: bilişsel arama şu anda bir arama hizmetindeki veriler için Otomatik yedeklemeyi desteklemez ve el ile gerçekleştirilen bir işlem aracılığıyla yedeklenmesi ve geri yüklenmesi gerekir. Yedekleme işleminizin uçtan uca bütünlüğünü sağlamak için el ile yedeklediğiniz içeriğin veri geri yüklemesini düzenli olarak gerçekleştirin.

- [Azure Bilişsel Arama dizinini yedekleme ve geri yükleme](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Azure 'da Key Vault anahtarlarını geri yükleme](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: bilişsel arama şu anda bir arama hizmetindeki veriler için Otomatik yedeklemeyi desteklemez ve el ile gerçekleştirilen bir işlem aracılığıyla yedeklenmelidir.  Ayrıca, Azure Key Vault içinde müşteri tarafından yönetilen anahtarları da yedekleyebilirsiniz.  

 
Anahtarları yanlışlıkla veya kötü amaçlı silmeye karşı korumak için Key Vault ' de geçici silme ve Temizleme korumasını etkinleştirin. El ile yedeklemeleri depolamak için Azure depolama kullanılıyorsa, Bloblar veya blob anlık görüntüleri silindiğinde verilerinizi kaydetmek ve kurtarmak için geçici silme özelliğini etkinleştirin. 
 

 
- [Azure Bilişsel Arama dizinini yedekleme ve geri yükleme](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)
 

 
- [Key Vault'ta geçici silme ve temizleme koruması özelliklerini etkinleştirme](../storage/blobs/soft-delete-blob-overview.md) 

- [Azure Blob depolama için geçici silme](../storage/blobs/soft-delete-blob-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: kuruluşunuz için bir olay yanıtı Kılavuzu geliştirin. Tüm personel rollerinin yanı sıra olay işleme ve yönetim 'in algılanmasından olay sonrası gözden geçirme aşamalarını tanımlayan, yazılı olay yanıt planları bulunduğundan emin olun.

- [Kendi güvenlik olay yanıtı işleminizi oluşturma kılavuzu](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Güvenlik Yanıt Merkezi 'nin bir olayın anatomisi](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Müşteri, kendi olay yanıt planının oluşturulmasına yardımcı olması için NıST 'nin bilgisayar güvenliği olay Işleme kılavuzunu da kullanabilir](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Rehberlik**: Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, uyarı veren etkinliğin arkasında kötü amaçlı bir amaç olduğunu ve uyarıyı vermek için ne kadar uygun güvenlik merkezi 'nin bulunması ya da analizin olduğunu temel alır.

Ayrıca, abonelikleri kullanarak abonelikleri işaretleyin ve Azure kaynaklarını tanımlamak ve kategorilere ayırmak için özellikle de hassas verileri işleyen bir adlandırma sistemi oluşturun. Olayın gerçekleştiği Azure kaynakları ve ortamının önem derecesine bağlı olarak, uyarıların düzeltilmesine öncelik vermek sizin sorumluluğunuzdadır.

- [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../azure-resource-manager/management/tag-resources.md)

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve açıkları belirleyip planı gerektiği şekilde gözden geçirin.

- [NıST yayımı, "BT planları ve özellikleri için test, eğitim ve alıştırmaya yönelik kılavuza bakın"](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: Microsoft Güvenlik Yanıt MERKEZI (MSRC), verilerinize izinsiz veya yetkisiz bir taraf tarafından erişildiğini belirlerse, Microsoft tarafından sizinle iletişim kurmak için güvenlik olayı iletişim bilgileri kullanılacaktır. Sorunların çözümlendiğinden emin olmak için gerçesonra olayları gözden geçirin.

- [Azure Güvenlik Merkezi güvenlik Ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: güvenlik uyarılarını olay yanıt sisteminizle birleştirme

**Rehberlik**: sürekli dışa aktarma özelliğini kullanarak güvenlik merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Sürekli dışa aktarma, uyarıları ve önerileri el ile ya da sürekli olarak dışa aktarmanız sağlar. Uyarıları Azure Sentinel 'e akışa almak için Güvenlik Merkezi veri bağlayıcısını kullanabilirsiniz.

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

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](/azure/security/benchmarks/overview)
- [Azure güvenlik temelleri](/azure/security/benchmarks/security-baselines-overview) hakkında daha fazla bilgi edinin
