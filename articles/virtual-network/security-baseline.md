---
title: Sanal ağ için Azure Güvenlik temeli
description: Sanal ağ güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 433f20de4d150aadb48ead685e981c60579f2730
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285539"
---
# <a name="azure-security-baseline-for-virtual-network"></a>Sanal ağ için Azure Güvenlik temeli

Bu güvenlik temeli [Azure Güvenlik kıyaslama sürümü 1,0](../security/benchmarks/overview-v1.md) ' dan Azure sanal ağı 'na kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure sanal ağı için geçerli olan ilgili kılavuza göre gruplandırılır. 

> [!NOTE]
> **Denetimler** , sanal ağ için geçerli değildir veya Microsoft 'un sorumluluğunu Microsoft 'un dışında tutulur. Sanal ağın Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, **[tam sanal ağ güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/virtual-network-security-baseline-v1.1.xlsx)** bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: sanal ağların, alt ağların ve ağ arabirimlerinin yapılandırma ve trafiğini izleme ve günlüğe kaydetme

**Rehberlik**: Güvenlik Merkezi 'ni kullanın ve ağ kaynaklarınızı Azure 'da güvenli hale getirmek için ağ koruma önerilerini izleyin. 

Ağ güvenlik grubu akış günlüklerini bir Log Analytics çalışma alanına gönderin ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanın. Trafik Analizi, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için bir özellik sunar. 

Ortamınıza ilişkin Öngörüler sağlamak için Azure Izleyici günlüklerini kullanın. Veri harmanlamaları ve çözümlenmesi için bir çalışma alanı kullanılmalıdır ve Application Insights ve Güvenlik Merkezi gibi diğer Azure hizmetleriyle tümleştirilebilir. 

Azure depolama hesabına veya bir olay hub 'ına göndermek için kaynak günlükleri ' ni seçin. Günlükleri çözümlemek için farklı bir platform da kullanılabilir. 

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

- [Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: DDoS saldırılarına karşı koruma sağlamak Için Azure sanal ağınızda dağıtılmış hizmet reddi (DDoS) standart korumasını etkinleştirin.

Tehdit zekası tabanlı filtreleme etkin ve kötü amaçlı ağ trafiği için "uyarı ve reddetme" olarak yapılandırılmış bir kuruluşun ağ sınırlarının her birinde Azure Güvenlik Duvarı 'Nı dağıtın.

Güvenlik Merkezi 'nin tehdit koruması özelliklerini kullanarak bilinen kötü amaçlı IP adresleriyle iletişimleri tespit edin.

Bağlantı noktalarını ve kaynak IP 'Leri gerçek trafik ve tehdit zeka göre sınırlayan ağ güvenlik grubu yapılandırmalarına yönelik güvenlik merkezi 'nin Uyarlamalı ağ sağlamlaştırma önerilerini uygulayın. 

- [Azure portal kullanarak Azure DDoS koruması standardını yönetme](../ddos-protection/manage-ddos-protection.md)

- [Azure Güvenlik Duvarı tehdit zekası tabanlı filtreleme](../firewall/threat-intel.md)

- [Güvenlik Merkezi 'nde tehdit koruması](../security-center/azure-defender.md)

- [Azure Güvenlik Merkezi 'nde Uyarlamalı ağ sağlamlaştırma](../security-center/security-center-adaptive-network-hardening.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Rehberlik**: ağ paketlerini kaydetmek için yaygın olarak kullanılabilir paket yakalama araçlarına ek olarak VPN Gateway paket yakalamayı kullanın. 

Ayrıca, Azure Marketi tekliflerinizde bulunan paket Aracısı iş ortakları aracılığıyla Terminal erişim noktası (TAP) veya ağ görünürlüğü işlevselliği sağlayan aracı tabanlı veya NVA çözümlerini gözden geçirebilirsiniz.

- [VPN ağ geçitleri için paket yakalamaları yapılandırma](../vpn-gateway/packet-capture.md) 

- [Ağ sanal gereç Iş ortağı](https://azure.microsoft.com/solutions/network-appliances)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: tehdit zekası etkinleştirilmiş sanal ağınıza dağıtılmış bir Azure Güvenlik duvarı kullanın. Bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen ve giden trafiği reddetmek veya engellemek için Azure Güvenlik Duvarı tehdit zekası tabanlı filtreleme kullanın. IP adresleri ve etki alanları, Microsoft Tehdit Analizi akışından alınır. 

Ayrıca, Azure Marketi 'nden, yük İnceleme özellikleri ile KIMLIKLER/IP işlevlerini destekleyen uygun bir teklif seçebilirsiniz.

Kötü amaçlı trafiği algılamak ve/veya reddetmek için kuruluşunuzun ağ sınırlarının her birinde tercih ettiğiniz güvenlik duvarı çözümünü dağıtın.

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Güvenlik Duvarı ile uyarıları yapılandırma](../firewall/threat-intel.md)

- [Azure Market](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Rehberlik**: ağ güvenlik gruplarında veya Azure Güvenlik duvarında ağ erişim denetimleri tanımlamak Için sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketleri, güvenlik kuralları oluştururken belirli IP adreslerinin yerine kullanılabilir. Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adı (örneğin, Apimanaya) belirterek ilgili hizmet için trafiğe izin verin veya reddedin. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

Karmaşık güvenlik yapılandırmasını basitleştirmeye yardımcı olması için uygulama güvenlik gruplarını kullanın. Uygulama güvenlik grupları, bir uygulamanın yapısının doğal bir uzantısı olarak ağ güvenliğini yapılandırmanıza olanak tanır. Bu, sanal makineleri gruplandırmanızı ve bu gruplara göre ağ güvenlik ilkelerini tanımlamanızı sağlar.

- [Hizmet etiketlerini anlama ve kullanma](service-tags-overview.md)

- [Uygulama güvenlik gruplarını anlama ve kullanma](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview#application-security-groups)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Rehberlik**: Azure ilkesiyle ağ kaynakları için standart güvenlik yapılandırması tanımlayıp uygulayın ve uygulama için yerleşik ağ ilkesi tanımlarını gözden geçirin.

Sanal ağlarınızla ilgili kullanılabilir güvenlik önerilerini içeren Güvenlik Merkezi için varsayılan ilkeye bakın.

Tek bir şema tanımında Azure Resource Manager şablonları, rol tabanlı erişim denetimi (Azure RBAC) atamaları ve ilkeleri gibi anahtar ortam yapıtlarını paketleyerek büyük ölçekli Azure dağıtımlarını basitleştirmek için Azure şemaları 'nı kullanın. Azure Blueprint, sürüm oluşturma aracılığıyla ince ayar yapılan denetim ve yönetim için yeni aboneliklere uygulanabilir. 

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Ağ için Azure Ilke örnekleri](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network) 

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

- [Azure Güvenlik Merkezi 'nde izlemeyi etkinleştirme](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Security%20Center/AzureSecurityCenter.json)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: ağ güvenlik grupları ve ağ güvenliği ve trafik akışıyla ilgili diğer kaynaklar için Etiketler kullanın. Tek tek ağ güvenlik grubu kuralları için bir ağdan gelen/giden trafiğe izin veren herhangi bir kurala yönelik iş gereksinimini, süreyi ve diğer bilgileri belirtmek için "Açıklama" alanını kullanın.
Tüm kaynakların etiketlerle oluşturulmasını ve mevcut etiketlenmemiş kaynakları bilgilendirmesini sağlamak için etiketlemeyle ilgili yerleşik Azure Ilke tanımlarından herhangi birini ("etiket ve onun değeri gerektir" gibi) kullanın.

Azure PowerShell veya Azure CLı ' yi seçerek etiketlere göre kaynakları arayabilir veya bunlarla ilgili eylemleri gerçekleştirin.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Sanal ağ oluşturma](quick-create-portal.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Rehberlik**: kaynak yapılandırmasını izlemek ve sanal ağınızdaki değişiklikleri algılamak Için Azure etkinlik günlüğü 'nü kullanın. Kritik kaynaklardaki değişiklikler gerçekleşirken tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: olay kaynağı, tarih, Kullanıcı, zaman damgası, kaynak adresleri, hedef adresler ve diğer yararlı öğeleri içeren denetim ve etkinlik günlüklerinize erişim Için Azure izleyicisini etkinleştirin. 

Azure Izleyici 'de analiz sorgulamak ve gerçekleştirmek için Log Analytics çalışma alanlarını kullanın ve uzun süreli/arşiv depolama için Azure depolama hesaplarını kullanın.
Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz. 

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: olay kaynağı, tarih, Kullanıcı, zaman damgası, kaynak adresleri, hedef adresler ve diğer yararlı öğeleri içeren denetim ve etkinlik günlüklerinize erişim Için Azure izleyicisini etkinleştirin.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure izleyici 'de, Log Analytics çalışma alanı saklama dönemini kuruluşunuzun uyumluluk düzenlemelerine göre ayarlayın. Azure depolama hesaplarını, güvenlik günlüğü depolama saklama 'nin uzun süreli/arşiv depolaması için kullanın.

- [Log Analytics veri saklama süresini değiştirme](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Azure depolama hesabı günlükleri için bekletme ilkesini yapılandırma](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Rehberlik**: anormal davranış için günlükleri çözümleyin ve izleyin ve sonuçları düzenli olarak gözden geçirin. Analiz sorgulamak ve gerçekleştirmek için Azure Izleyici Log Analytics çalışma alanını kullanın ve uzun süreli/arşiv depolama için Azure depolama hesaplarını kullanın. 

Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz. 

- [Log Analytics çalışma alanını anlayın](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Izleyici 'de özel sorgular gerçekleştirme](../azure-monitor/logs/get-started-queries.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Izleyici ve üçüncü taraf SıEM tümleştirmesi ile çalışmaya başlama](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Rehberlik**: Güvenlik Merkezi 'ni, güvenlik günlükleri ve olayları 'nda bulunan anormal etkinlikleri izlemek ve uyarmak Için Log Analytics çalışma alanıyla kullanın.

Alternatif olarak, uyarı için Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri de ekleyebilirsiniz.

- [Güvenlik Merkezi 'nde uyarıları yönetme](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics günlük verilerinde uyarı alma](../azure-monitor/alerts/tutorial-response.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="29-enable-dns-query-logging"></a>2,9: DNS sorgu günlüğünü etkinleştir

**Rehberlik**: Kurumsal gereksiniminizden bağımsız olarak, DNS günlüğü çözümü Için Azure Marketi 'nden bir üçüncü taraf çözümü uygulayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: Azure Active Directory (Azure AD), açıkça atanabilen ve sorgulanabilir olan yerleşik yönetici rollerini kullanın.

Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü kullanın.

- [Azure AD 'de PowerShell ile dizin rolü alma](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturun. Yönetim hesaplarının sayısını izlemek için Güvenlik Merkezi 'nin kimlik ve erişim yönetimi ' ni kullanın.

Microsoft Hizmetleri ve Azure Resource Manager için ayrıcalıklı rolleri Privileged Identity Management Azure Active Directory (Azure AD) kullanarak tam zamanında/tam erişimi etkinleştirin.

- [Privileged Identity Management hakkında daha fazla bilgi edinin](/azure/active-directory/privileged-identity-management/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory çoklu oturum açma kullan (SSO)

**Rehberlik**: tek başına bağımsız kimlik bilgilerini hizmet başına yapılandırmak yerine Azure Active Directory (Azure AD) ile SSO kullanın. Güvenlik Merkezi 'nin kimlik ve erişim yönetimi önerilerini kullanın.

- [Azure AD 'de uygulamalarda çoklu oturum açma](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin ve Güvenlik Merkezi 'nin kimlik ve erişim yönetimi önerilerini izleyin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: yönetim görevleri için güvenli, Azure tarafından yönetilen iş istasyonları kullanın

**Rehberlik**: çok faktörlü kimlik doğrulaması ile oturum açmak ve Azure ağ kaynaklarına erişmek Için yapılandırılmış ayrıcalıklı erişim iş istasyonları (Paw) kullanın. 

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: riskli Kullanıcı davranışında uyarıları ve raporları görüntülemek için Azure Active Directory (Azure AD) risk algılamalarını kullanın. 

Güvenlik Merkezi risk algılama, Azure Izleyici 'ye uyarı verebilir ve eylem gruplarını kullanarak özel uyarı/bildirim yapılandırır.

- [Güvenlik Merkezi risk algılamalarını anlama (şüpheli etkinlik)](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

- [Özel uyarı ve bildirim için eylem gruplarını yapılandırma](../azure-monitor/alerts/action-groups.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: IP adresi aralıklarının veya ülkelerin/bölgelerin yalnızca belirli mantıksal gruplarından erişime izin vermek için adlandırılmış konumlar kullanın.

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: hizmetleriniz için merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifreleme kullanarak verileri korur ve ayrıca, salları, karmaları ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.  

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory-domain-services/tutorial-create-instance.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: eski hesapların keşfedilmesine yardımcı olmak için günlük sağlamak üzere Azure Active Directory (Azure AD) kullanın. 

Azure kimlik erişimi Incelemeleri, grup üyeliklerini verimli bir şekilde yönetmek, kurumsal uygulamalara erişmek ve rol atamaları için gerçekleştirilebilir. Yalnızca etkin kullanıcıların erişmeye devam ettiğinden emin olmak için Kullanıcı erişiminin düzenli olarak gözden geçirilmesi gerekir.

- [Azure AD raporlamayı anlama](/azure/active-directory/reports-monitoring/)

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: Azure Active Directory (Azure AD) oturum açma etkinliğini tümleştirin, erişiminizle ilgili herhangi bir SIEM veya izleme aracıyla, olay günlüğü kaynaklarını denetleyebilir ve risk altında bulabilirsiniz.

Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturup Log Analytics çalışma alanına denetim günlüklerini ve oturum açma günlüklerini göndererek bu işlemi kolaylaştırın. İstenen tüm uyarılar, Log Analytics çalışma alanı içinde yapılandırılabilir.

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapması üzerinde uyarı

**Rehberlik**: otomatik yanıtları, sanal ağınızın Kullanıcı kimlikleriyle ilgili şüpheli eylemler için yapılandırmak üzere Azure Active Directory (Azure AD) risk ve kimlik koruması özelliklerini kullanın. Daha fazla araştırma için Azure Sentinel 'e veri alma.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Rehberlik**: yoldaki tüm hassas bilgileri şifreleyin. Sanal ağlarınızdaki Azure kaynaklarınıza bağlanan tüm istemcilerin TLS 1,2 veya daha yüksek bir bağlantı kurabildiğinden emin olun. Geçiş sırasında bekleyen ve şifreleme için Güvenlik Merkezi önerilerini izleyin. 

Microsoft, müşteriler tarafından Azure ağı dahilinde ve dışarıdan son kullanıcıya dışarıdan geçiş sırasında verilerin güvenliğini sağlamak için kullanılabilecek çeşitli seçenekler sunar. Bunlar arasında sanal özel ağlar (IPSec/ıKE şifrelemesi kullanılarak), Aktarım Katmanı Güvenliği (TLS) 1,2 veya üzeri (Application Gateway veya Azure ön kapılı Azure bileşenleri aracılığıyla), doğrudan Azure sanal makinelerinde (Windows IPSec veya SMB gibi) protokoller ve daha fazlası bulunur.

Ayrıca, MACsec (veri bağlantı katmanında bir IEEE standardı) kullanılarak "varsayılan olarak şifreleme" özelliği, müşteri verilerinin gizliliğini ve bütünlüğünü sağlamak için Azure veri merkezleri arasında seyahat eden tüm Azure trafiği için etkinleştirilir.

- [Azure ile iletim sırasında şifrelemeyi anlama](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: kaynaklara erişimi yönetmek için Azure RBAC kullanma 

**Rehberlik**: veri ve kaynaklara erişimi yönetmek için Azure rol tabanlı erişim denetimi 'Ni (Azure RBAC) kullanın. Aksi takdirde, hizmete özgü erişim denetimi yöntemlerini kullanın. 

Sahip, katkıda bulunan veya ağ katılımcısı gibi yerleşik roller ' i seçin ve rolü uygun kapsama atayın. Örneğin, bu rollerden herhangi birine sanal ağlar için gereken belirli izinlerle sanal ağ yeteneklerinin bir alt kümesini atayabilirsiniz. 

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md)

- [Sanal ağları planlama](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#permissions)

- [Azure yerleşik rollerini gözden geçirin](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#networking)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Kılavuz**: sanal ağlar ve ağ güvenlik grupları gibi kritik Azure kaynaklarında değişiklik gerçekleşirken uyarı oluşturmak Için Azure etkinlik günlükleri Ile Azure izleyici 'yi kullanın.

- [Ağ güvenlik grubu için tanılama günlüğü](virtual-network-nsg-manage-log.md)

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Kılavuz**: Azure Kaynak Grafiği 'Ni kullanarak sanal ağlar ve aboneliklerinizde bulunan alt ağlar gibi tüm ağ kaynaklarını sorgulayın ve bulun. Kiracınızda uygun (okuma) izinleriniz olduğundan ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerinin listesini belirleyebildiğinizden emin olun.

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Kılavuz**: Azure kaynaklarına Etiketler uygulayarak bunları bir taksonomi halinde mantıksal olarak organize etmek için meta veriler verirsiniz.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: sanal ağı ve ilgili kaynakları düzenlemek ve izlemek için uygun yerlerde etiketleme, yönetim grupları ve ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynaklarının envanterini tanımlayın ve saklayın

**Rehberlik**: Kurumsal gereksinimleriniz uyarınca, işlem kaynakları Için onaylanan Azure kaynakları ve onaylanan yazılım envanterini oluşturmanız gerekir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:

- İzin verilmeyen kaynak türleri 

- İzin verilen kaynak türleri 

Azure depolama hesaplarıyla ilgili olanlar gibi yüksek güvenlik tabanlı ortamlarda Azure Kaynak Grafında abonelikler içindeki kaynakları sorgulayın veya bulun. 

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Sanal ağ için Azure ilke örneği yerleşik bileşenleri](policy-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Rehberlik**: kuruluşun ilkelerine göre gereken Azure ilkesiyle kaynak oluşturmayı veya kullanımını engelleyin. Yetkisiz kaynakları kaldırmak için süreçler uygulayın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:
- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Sanal ağ için Azure ilke örneği yerleşik bileşenleri](policy-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcının Azure Resource Manager etkileşime geçmesini sınırlamak Için Azure koşullu erişimi kullanın.

- [Azure Resource Manager erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Kılavuz**: Azure ilkesi diğer adlarını kullanarak Azure ağ kaynaklarınızın yapılandırmasını denetlemek veya zorlamak için özel ilkeler oluşturun ve ayrıca yerleşik Azure ilke tanımlarını kullanın.

Yapılandırma şablonlarınızın herhangi birini JavaScript Nesne Gösterimi (JSON) biçiminde Azure Resource Manager dışa aktarın ve yapılandırmaların kuruluşunuzun güvenlik gereksinimlerini karşıladığından veya aşdığından emin olmak için gözden geçirin.

Azure kaynaklarınız için güvenli bir yapılandırma temeli olarak güvenlik merkezi 'nden öneriler uygulayın.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Öğretici: uyumluluğu zorlamak için ilke oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Sanal ağ için Azure ilke örneği yerleşik bileşenleri](policy-reference.md)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Güvenlik önerileri - başvuru kılavuzu](../security-center/recommendations-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Rehberlik**: sanal ağla ve ilgili kaynaklarla ilişkili Azure kaynaklarını güvenli bir şekilde yapılandırmak için Azure Resource Manager şablonları ve Azure ilkeleri kullanın.  Azure Resource Manager şablonlar, Azure kaynaklarıyla birlikte sanal makineleri dağıtmak için kullanılan JSON (JavaScript Nesne Gösterimi) tabanlı dosyalardır. Microsoft, temel şablonlarda Bakımı gerçekleştirir.  

Azure kaynaklarınızın tamamında güvenli ayarları zorlamak için Azure Ilkesi [reddetme] ve [dağıtım yoksa dağıt] etkileri kullanın.

- [Azure Resource Manager şablonları oluşturma hakkında bilgi](../virtual-machines/windows/ps-template.md) 

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

- [Sanal ağ için Azure Resource Manager şablonu örnekleri](template-samples.md)

- [Sanal ağ için Azure ilke örneği yerleşik bileşenleri](policy-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilkeleri, Azure Resource Manager şablonları, istenen durum yapılandırması betikleri gibi kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure DevOps kullanın. vb.

Azure DevOps 'da, kodunuzun, derlemelerinizin ve iş izlemenin gibi yönetmek istediğiniz kaynaklara erişmek için izinleriniz olmalıdır. Birçok izin yerleşik güvenlik grupları aracılığıyla verilir. Azure DevOps ile tümleşikse veya Team Foundation Server ile tümleşikse Active Directory belirli kullanıcılara, yerleşik güvenlik gruplarına veya Azure Active Directory (Azure AD) tanımlanmış gruplara izin verebilir veya vermeyebilirsiniz.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure DevOps 'da izinler ve gruplar hakkında](/azure/devops/organizations/security/about-permissions)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Kılavuz**: Azure Ilkesini kullanarak Azure kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın. Azure kaynaklarınızı ve belirli kaynaklarla ilgili yerleşik ilke tanımlarını denetlemek veya zorlamak için özel ilkeler oluşturmak üzere Azure Ilke diğer adlarını kullanın. 

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Diğer adları kullanma](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

- [Sanal ağ için Azure ilke örneği yerleşik bileşenleri](policy-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Kılavuz**: Azure sanal ağınız ve ilgili kaynaklarınız için temel taramalar gerçekleştirmek üzere güvenlik merkezi 'ni kullanın. Azure Kaynak konfigürasyonları 'nı uyarmak ve denetlemek için Azure Ilkesini kullanın.

- [Güvenlik Merkezi 'nde öneriler nasıl düzeltileceği](../security-center/security-center-remediate-recommendations.md)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Sanal ağ için Azure ilke örneği yerleşik bileşenleri](policy-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: bir Azure sanal ağında barındırılan Azure kaynaklarınızın gizli yönetimini basitleştirmek ve güvenli hale getirmek için Azure Key Vault ile birlikte yönetilen hizmet kimliği kullanın.

- [Azure yönetilen kimliklerle tümleştirme](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Key Vault oluşturma](../key-vault/secrets/quick-create-portal.md) 

- [Yönetilen kimlik ile Key Vault kimlik doğrulaması sağlama](../key-vault/general/assign-access-policy-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: kod içinde kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayın. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar.

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli otomatik arka pencerelere emin olun

**Rehberlik**: bir sanal ağı ve ilgili kaynakları dağıtmak için Azure Resource Manager kullanın. Azure Resource Manager, sanal ağı ve ilgili kaynakları geri yüklemek için yedeklemeler olarak kullanılabilecek şablonları dışarı aktarma olanağı sağlar.  Azure Resource Manager şablonu dışarı aktarma API 'sini düzenli aralıklarla çağırmak için Azure Otomasyonu 'nu kullanın.

- [Azure Resource Manager'a genel bakış](../azure-resource-manager/management/overview.md)

- [Sanal ağ için Azure Resource Manager şablonu örnekleri](template-samples.md)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Kaynak grupları-şablonu dışarı aktarma](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Otomasyonu 'na giriş](../automation/automation-intro.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: bir sanal ağı ve ilgili kaynakları dağıtmak için Azure Resource Manager kullanın. Azure Resource Manager, sanal ağı ve ilgili kaynakları geri yüklemek için yedeklemeler olarak kullanılabilecek şablonları dışarı aktarma olanağı sağlar. Azure Resource Manager şablonu dışarı aktarma API 'sini düzenli aralıklarla çağırmak için Azure Otomasyonu 'nu kullanın. Azure Key Vault içinde müşteri tarafından yönetilen anahtarları yedekleyin.

- [Azure Resource Manager'a genel bakış](../azure-resource-manager/management/overview.md)

- [Sanal ağ için Azure Resource Manager şablonu örnekleri](template-samples.md)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Kaynak grupları-şablonu dışarı aktarma](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Otomasyonu 'na giriş](../automation/automation-intro.md)

- [Azure 'da Anahtar Kasası anahtarlarını yedekleme](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: düzenli olarak Azure Resource Manager şablonlarının dağıtımını yalıtılmış bir aboneliğe ve yedeklenen müşteri tarafından yönetilen anahtarların test altına geri yüklemeye gerçekleştirir.

- [ARM şablonları ve Azure portal kaynak dağıtma](../azure-resource-manager/templates/deploy-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Kılavuz**: özel Azure ilke tanımları ve Azure Resource Manager şablonları gibi kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure DevOps kullanın.

Azure DevOps ile tümleşikse veya Team Foundation Server ile tümleşikse Active Directory belirli kullanıcılara, yerleşik güvenlik gruplarına veya Azure Active Directory (Azure AD) tanımlanmış gruplara izin verin veya reddedin.

Müşteri tarafından yönetilen anahtarları korumak için Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanın. 

Anahtarları yanlışlıkla veya kötü amaçlı silmeye karşı korumak için Key Vault Soft-Delete ve Temizleme korumasını etkinleştirin.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps 'da izinler ve gruplar hakkında](/azure/devops/organizations/security/about-permissions)

- [Key Vault 'de Soft-Delete ve Temizleme korumasını etkinleştirme](../storage/blobs/soft-delete-blob-overview.md)

- [Azure Depolama blobları için geçici silme](../storage/blobs/soft-delete-blob-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: Kuruluşunuz için bir olay yanıt kılavuzu oluşturun. Tüm personelin rollerine ek olarak algılama aşamasından olay sonrası gözden geçirme aşamasına kadar tüm olay işleme/yönetim aşamalarını tanımlayan yazılı olay yanıt planları bulunduğundan emin olun.  

- [Kendi güvenlik olay yanıtı işleminizi oluşturma kılavuzu](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Güvenlik Yanıt Merkezi 'nin bir olayın anatomisi](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Kendi olay yanıtı planınızı oluşturmaya yardımcı olmak için NıST 'nin bilgisayar güvenliği olay Işleme kılavuzuyla yararlanın](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Rehberlik**: Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizte ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır.

Etiketleri kullanarak abonelikleri (örneğin, üretim veya üretim dışı) açık bir şekilde işaretleyin ve Azure kaynaklarını net bir şekilde tanımlamak ve kategorilere ayırmak için özellikle de hassas verileri işleyen bir adlandırma sistemi oluşturun.  Azure kaynaklarının önem düzeyine ve olayın oluştuğu ortama bağlı olarak uyarıların çözümünde önceliği belirlemek sizin sorumluluğunuzdadır.

- [Güvenlik Merkezi 'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md)

- [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: Azure kaynaklarınızın korunmasına yardımcı olmak için, sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve açıkları belirleyip planı gerektiği şekilde gözden geçirin.

- [NıST 'nin Yayımlama Kılavuzu, BT planları ve becerileri için programları test etme, eğitim ve alıştırma Kılavuzu](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: Microsoft Güvenlik Yanıt MERKEZI (MSRC), verilerinize izinsiz veya yetkisiz bir taraf tarafından erişildiğini belirlerse, Microsoft tarafından sizinle iletişim kurmak için güvenlik olayı iletişim bilgileri kullanılacaktır. Sorunların çözümlendiğinden emin olmak için gerçesonra olayları gözden geçirin.

- [Güvenlik Merkezi güvenlik Ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: güvenlik uyarılarını olay yanıt sisteminizle birleştirme

**Kılavuz**: Azure kaynaklarına riskleri belirlemenize yardımcı olmak Için sürekli dışarı aktarma özelliğini kullanarak güvenlik merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Sürekli dışa aktarma, uyarıları ve önerileri el ile veya devam eden sürekli bir biçimde dışa aktarmanız sağlar. 

Uyarıları Azure Sentinel 'e göndermek için Güvenlik Merkezi veri bağlayıcısını de kullanabilirsiniz.

- [Sürekli dışarı aktarmayı yapılandırma](../security-center/continuous-export.md)

- [Uyarıların Azure Sentinel’e akışını yapma](../sentinel/connect-azure-security-center.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: güvenlik uyarılarına yanıtı otomatikleştirme

**Kılavuz**: Güvenlik Merkezi 'Ndeki Iş akışı Otomasyonu özelliğini kullanarak yanıtları otomatik olarak tetikleyin ve Azure kaynaklarınızı korumaya yönelik öneriler ve güvenlik uyarıları üzerinde "Logic Apps" aracılığıyla yanıtları otomatik olarak tetikler.

- [Iş akışı otomasyonu ve Logic Apps yapılandırma](../security-center/workflow-automation.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="penetration-tests-and-red-team-exercises"></a>Sızma Testleri ve Red Team Alıştırmaları

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: Penetme testleri ve Red ekibi alıştırmaları](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Azure kaynaklarınızın düzenli olarak sızma testini gerçekleştirin ve tüm kritik güvenlik bulgularını düzeltmeye dikkat edin

**Rehberlik**: sızma testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak Için Microsoft katılım kurallarını izleyin. Microsoft 'un, Microsoft tarafından yönetilen bulut altyapısına, hizmetlerine ve uygulamalarına karşı kırmızı ekip oluşturma ve canlı site sızma testi stratejisini kullanın.

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](/azure/security/benchmarks/overview)
- [Azure güvenlik temelleri](/azure/security/benchmarks/security-baselines-overview) hakkında daha fazla bilgi edinin
