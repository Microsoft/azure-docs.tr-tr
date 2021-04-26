---
title: Azure DevTest Labs için Azure Güvenlik temeli
description: Azure DevTest Labs güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: devtest-lab
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 27a0d5b809480b2ce4aff36c5acd43c149ed5bb3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562843"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azure DevTest Labs için Azure Güvenlik temeli

Bu güvenlik temeli, Azure DevTest Labs için [Azure Güvenlik kıyaslama sürüm 1,0](../security/benchmarks/overview-v1.md) ' den rehberlik uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure DevTest Labs için geçerli olan ilgili kılavuza göre gruplandırılır. Azure DevTest Labs için geçerli olmayan **denetimler** dışlandı.

Azure DevTest Labs Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Azure DevTest Labs güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: Azure DevTest Labs kaynakları dağıtırken, var olan bir sanal ağı oluşturmanız veya kullanmanız gerekir. Seçilen sanal ağın alt ağlarına ve uygulamanızın güvenilen bağlantı noktalarına ve kaynaklarına özgü olarak yapılandırılmış ağ erişim denetimlerine uygulanmış bir ağ güvenlik grubu olduğundan emin olun. Laboratuvar kaynakları bir sanal ağla yapılandırıldığında, bunlar genel olarak adreslenebilir ve yalnızca sanal ağ içinden erişilebilir. Laboratuvar ortamlarının yanı sıra, depolama hesabı ve Anahtar kasaları gibi laboratuvar kaynakları da tamamen yalıtılarak ve yalnızca belirtilen uç noktalarda erişilebilen, ağ yalıtımlı bir laboratuvar oluşturmayı da tercih edebilirsiniz. 

Kuruluş gereksinimlerinize bağlı olarak, Azure Güvenlik Duvarı hizmetini kullanarak abonelikler ve sanal ağlarda uygulama ve ağ bağlantısı ilkelerini merkezi olarak oluşturun, uygulayın ve günlüğe kaydedin.

- [Azure DevTest Labs için sanal ağ yapılandırma](devtest-lab-configure-vnet.md)

- [Ağ yalıtılmış DevTest Labs örneği oluşturma](network-isolation.md)

- [Güvenlik kuralları ile ağ güvenlik grubu oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Güvenlik duvarını dağıtma ve yapılandırma](../firewall/tutorial-firewall-deploy-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: sanal ağların, alt ağların ve NIC 'lerin yapılandırmasını ve trafiğini izleyin ve günlüğe kaydedin

**Rehberlik**: ağ üzerinde Azure DevTest Labs kaynaklarınızın dağıtıldığı ağ güvenlik grubu dağıtın. Trafik denetimi için ağ güvenlik gruplarında ağ güvenlik grubu akış günlüklerini etkinleştirin.

Ayrıca, NSG akış günlüklerini bir Log Analytics çalışma alanına gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="13-protect-critical-web-applications"></a>1,3: kritik Web uygulamalarını koruma

**Kılavuz**: Azure Web uygulaması güvenlik duvarını (WAF), gelen trafiğin ek incelemesi için Azure Resource Manager şablonlar kullanılarak dağıtılan kritik Web uygulamalarının önünde dağıtın. WAF ve alma günlükleri için tanılama ayarını bir depolama hesabı, Olay Hub 'ı veya Log Analytics çalışma alanında etkinleştirin.

- [Azure WAF dağıtma](../web-application-firewall/ag/create-waf-policy-ag.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: Laboratuvar Için bir Azure sanal ağı (VNet) dağıtmak, laboratuvarınız için güvenlik ve yalıtımı geliştirir. Alt ağlar, erişim denetim ilkeleri ve diğer özellikler erişimi kısıtlama konusunda da yardımcı olur. VNet 'te dağıtıldığında, Azure DevTest Labs herkese açık bir şekilde adreslenebilir ve yalnızca sanal makineden ve VNet içindeki uygulamalardan erişilebilir.

DDoS saldırılarına karşı koruma sağlamak için Azure sanal ağlarınızda DDoS standart korumasını etkinleştirin. Bilinen kötü amaçlı IP adresleriyle iletişimleri reddetmek için Azure Güvenlik Merkezi tümleşik tehdit zekasını kullanın.

Tehdit zekası etkinleştirilmiş ve kötü amaçlı ağ trafiği için "uyarı ve reddetme" olarak yapılandırılmış bir kuruluşun ağ sınırlarının her birinde Azure Güvenlik Duvarı 'Nı dağıtın. NSG 'leri, sınırlı bir süre için bitiş noktaları onaylı IP adresleriyle sınırlamak üzere Azure Güvenlik Merkezi 'ni tam zamanında ağ erişimi ile kullanın. Bağlantı noktalarını ve kaynak IP 'Leri gerçek trafik ve tehdit zeka göre sınırlayan NSG yapılandırmalarının kullanılması için Azure Güvenlik Merkezi Uyarlamalı ağ sağlamlaştırma kullanın.

- [Azure DevTest Labs için sanal ağ yapılandırma](devtest-lab-configure-vnet.md)

- [DDoS korumasını yapılandırma](../ddos-protection/manage-ddos-protection.md)

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Güvenlik Merkezi tümleşik tehdit zekasını anlama](../security-center/azure-defender.md)

- [Azure Güvenlik Merkezi Uyarlamalı ağ sağlamlaştırma 'yi anlama](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Güvenlik Merkezi 'Ni tam zamanında ağ Access Control anlama](../security-center/security-center-just-in-time.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Rehberlik**: anormal etkinlikleri araştırmak için laboratuvar sanal ağınızda Ağ İzleyicisi paket yakalamayı etkinleştirin. 

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: tehdit zekası etkinleştirilmiş sanal ağınıza dağıtılmış bir Azure Güvenlik duvarı kullanın. Azure Güvenlik Duvarı tehdit zekası tabanlı filtreleme, bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen ve giden trafiği uyarabilir ve reddedebilir. IP adresleri ve etki alanları, Microsoft Tehdit Analizi akışından alınır. Kuruluşunuz Azure Güvenlik duvarının sağlayabileceği kadar ek işlevsellik gerektiriyorsa, Azure Marketi 'nden, yük İnceleme özellikleri ile KIMLIKLER/IP işlevlerini destekleyen uygun bir teklif seçin.

Kötü amaçlı trafiği algılamak ve/veya reddetmek için kuruluşunuzun ağ sınırlarının her birinde tercih ettiğiniz güvenlik duvarı çözümünü dağıtın.

- [Azure Market](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)
 

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)
 

- [Azure Güvenlik Duvarı ile uyarıları yapılandırma](../firewall/threat-intel.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Web uygulamalarına trafiği yönetme

**Kılavuz**: Web uygulamaları Içeren, DevTest Labs Azure Resource Manager tabanlı ortamlarda çalışırken, güvenilen sertifikalar IÇIN etkin https/TLS Ile bir Azure Application Gateway dağıtın.

- [Application Gateway dağıtma](../application-gateway/quick-create-portal.md)

- [Application Gateway HTTPS kullanacak şekilde yapılandırma](../application-gateway/create-ssl-portal.md)

- [Azure Web uygulaması ağ geçitleri ile katman 7 yük dengelemesini anlama](../application-gateway/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Rehberlik**: ağ güvenlik gruplarında veya DevTest Labs kaynaklarınız Için yapılandırılmış Azure Güvenlik duvarında ağ erişim denetimleri tanımlamak Için sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adı (örn., Apimanaya) belirterek, ilgili hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

Ayrıca, karmaşık güvenlik yapılandırmasını basitleştirmeye yardımcı olması için uygulama güvenlik gruplarını kullanabilirsiniz. Uygulama güvenlik grupları ağ güvenliğini uygulamanın yapısının doğal bir uzantısı olarak yapılandırmanıza imkan vererek sanal makineleri gruplamanızı ve ağ güvenlik ilkelerini bu gruplara göre tanımlamanızı sağlar.

- [Hizmet etiketlerini anlama ve kullanma](../virtual-network/service-tags-overview.md)

- [Uygulama güvenlik gruplarını anlama ve kullanma](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Kılavuz**: Azure ilkesiyle ağ kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın.

Azure şemaları, Azure kaynakları Yöneticisi şablonları, RBAC denetimleri ve ilkeleri gibi tek bir şema tanımında paket ortam yapıtları sunarak büyük ölçekli Azure dağıtımlarını basitleştirmek için de kullanabilirsiniz. Şema 'i yeni aboneliklere uygulayabilir ve sürüm oluşturma aracılığıyla denetim ve yönetimi ayarlayabilirsiniz.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Ağ için Azure Ilke örnekleri](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: bir taksonomiyle mantıksal olarak organize etmek için Azure DevTest Labs dağıtımınız ile ilişkili ağ kaynakları için Etiketler kullanın. Bireysel NSG kuralları için "Açıklama" alanını kullanarak ağa giden/giden trafiğe izin veren kuralların iş gereksinimini ve/veya süresini (vs.) belirtin.

Tüm kaynakların etiketlerle oluşturulmasını ve mevcut etiketlenmemiş kaynakları bilgilendirmesini sağlamak için etiketlemeyle ilgili yerleşik Azure Ilke tanımlarından herhangi birini ("etiket ve onun değeri gerektir" gibi) kullanın.

Azure PowerShell veya Azure CLı kullanarak, etiketlerine göre kaynaklar üzerinde arama yapabilir veya eylemler gerçekleştirebilirsiniz.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Kılavuz**: Azure etkinlik günlüğü 'nü kullanarak kaynak yapılandırmalarının yanı sıra Azure kaynaklarınızda yapılan değişiklikleri tespit edin. Kritik kaynaklardaki değişiklikler gerçekleşirken tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: onaylanan zaman eşitleme kaynaklarını kullanın

**Rehberlik**: Microsoft, Azure kaynakları için zaman kaynaklarını korur. Ancak, işlem kaynaklarınız için zaman eşitleme ayarlarını yönetebilirsiniz.

- [Azure 'da Windows VM 'Leri için zaman eşitleme](../virtual-machines/windows/time-sync.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Kılavuz**: Azure etkinlik günlüğü tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına, Azure Olay Hub 'ına veya Arşiv için Azure depolama hesabına gönderin. Etkinlik günlükleri, yönetim düzlemi düzeyinde Azure DevTest Labs örneklerinizin üzerinde gerçekleştirilen işlemlere ilişkin öngörüler sağlar. Azure etkinlik günlüğü verilerini kullanarak, DevTest Labs örnekleriniz için yönetim düzlemi düzeyinde herhangi bir yazma işlemi (PUT, POST, DELETE) için "ne, kim ve ne zaman" belirleyebilirsiniz.

- [Platform günlüklerini ve ölçümlerini farklı hedeflere göndermek için tanılama ayarlarını oluşturma](../azure-monitor/essentials/diagnostic-settings.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Kılavuz**: Azure etkinlik günlüğü tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına, Azure Olay Hub 'ına veya Arşiv için Azure depolama hesabına gönderin. Etkinlik günlükleri, yönetim düzlemi düzeyinde Azure DevTest Labs örneklerinizin üzerinde gerçekleştirilen işlemlere ilişkin öngörüler sağlar. Azure etkinlik günlüğü verilerini kullanarak, DevTest Labs örnekleriniz için yönetim düzlemi düzeyinde herhangi bir yazma işlemi (PUT, POST, DELETE) için "ne, kim ve ne zaman" seçeneğini belirleyebilirsiniz.

- [Platform günlüklerini ve ölçümlerini farklı hedeflere göndermek için tanılama ayarlarını oluşturma](../azure-monitor/essentials/diagnostic-settings.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: işletim sistemlerinden güvenlik günlüklerini toplama

**Rehberlik**: Azure DevTest Labs sanal makineler (VM 'ler) oluşturulur ve müşteriye aittir. Bu nedenle, bu, kuruluşun izleme sorumluluğudur. İşlem işletim sistemini izlemek için Azure Güvenlik Merkezi 'ni kullanabilirsiniz. İşletim sisteminden Güvenlik Merkezi tarafından toplanan veriler işletim sistemi türü ve sürümü, işletim sistemi (Windows olay günlükleri), çalışan süreçler, makine adı, IP adresleri ve oturum açan kullanıcı içerir. Log Analytics Aracısı Ayrıca kilitlenme bilgi döküm dosyalarını da toplar.

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Azure Izleyici ile Azure sanal makine iç konak günlüklerini toplama](../azure-monitor/vm/quick-collect-azurevm.md)

- [Azure Güvenlik Merkezi veri toplamayı anlama](../security-center/security-center-enable-data-collection.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure izleyici 'de, kuruluşunuzun uyumluluk düzenlemelerine göre Azure DevTest Labs örneklerinize ilişkin Log Analytics çalışma alanları için günlük tutma süresi ayarlayın.

- [Daha fazla bilgi için aşağıdaki makaleye bakın](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Kılavuz**: Azure etkinlik günlüğü tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına gönderin. Terimleri aramak, eğilimleri belirlemek, desenleri analiz etmek ve Azure DevTest Labs için toplanabilecek etkinlik günlüğü verilerine göre birçok diğer öngörü sağlamak için Log Analytics sorguları çalıştırın.

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Izleyici 'de Log Analytics çalışma alanında Azure etkinlik günlüklerini toplama ve çözümleme](../azure-monitor/essentials/activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Kılavuz**: Azure Log Analytics çalışma alanını, Güvenlik günlüklerine ve Azure DevTest Labs ilgili olaylara yönelik anormal etkinlikleri izlemek ve uyarmak için kullanın.

- [Log Analytics günlük verilerinde uyarı alma](../azure-monitor/alerts/tutorial-response.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: Azure Active Directory (Azure AD), açıkça atanması ve sorgulanabilir olması gereken yerleşik roller içerir. Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular çalıştırmak için Azure AD PowerShell modülünü kullanın.

- [Azure AD 'de PowerShell ile dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure DevTest Labs rolleri](devtest-lab-add-devtest-user.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Rehberlik**: Azure Active Directory (Azure AD) varsayılan parola kavramına sahip değildir. Parola gerektiren diğer Azure kaynakları, karmaşıklık gereksinimleriyle ve hizmete bağlı olarak, en düşük parola uzunluğuna sahip bir parola oluşturulmasını zorlar. Varsayılan parolaları kullanbilen üçüncü taraf uygulamalardan ve Market hizmetlerinden siz sorumlusunuz.

DevTest Labs varsayılan parola kavramına sahip değildir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturun. Yönetim hesaplarının sayısını izlemek için Azure Güvenlik Merkezi kimlik ve erişim yönetimi 'ni kullanın.

Ayrıca, özel yönetim hesaplarını izlemenize yardımcı olmak için Azure Güvenlik Merkezi veya yerleşik Azure Ilkeleri önerilerini kullanabilirsiniz; örneğin:

- Aboneliğinize birden fazla sahip atanmalıdır

- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır

- [Kimlik ve erişim (Önizleme) izlemek için Azure Güvenlik Merkezi 'ni kullanma](../security-center/security-center-identity-access.md)

- [Azure Ilkesini kullanma](../governance/policy/tutorials/create-and-manage.md)

- [Azure DevTest Labs rolleri](devtest-lab-add-devtest-user.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Azure Active Directory ile çoklu oturum açma (SSO) kullanın

**Rehberlik**: DevTest Labs kimlik yönetimi için Azure Active Directory (Azure AD) hizmetini kullanır. Kullanıcılara DevTest Labs tabanlı bir ortama erişim verdiğinizde, bu iki temel yönü göz önünde bulundurun:
- Kaynak yönetimi: kaynakları yönetmek (VM oluşturma, ortamlar oluşturma, başlatma, durdurma, yeniden başlatma, silme ve yapıları uygulama vb.) için Azure portal erişim sağlar. Azure rol tabanlı erişim denetimi (Azure RBAC) kullanılarak kaynak yönetimi Azure 'da yapılır. Kullanıcılara roller atarsınız ve kaynak ve erişim düzeyi izinlerini ayarlarsınız.
- Sanal makineler (ağ düzeyi): Varsayılan yapılandırmada VM 'Ler bir yerel yönetici hesabı kullanır. Kullanılabilir bir etki alanı (Azure Active Directory Domain Services (Azure AD DS), şirket içi etki alanı veya bulut tabanlı etki alanı) varsa, makineler etki alanına katılabilir. Kullanıcılar, makinelere bağlanmak için etki alanına yönelik bir yapıt kullanarak etki alanı tabanlı kimlikleri kullanabilir.

- [DevTest Labs için başvuru mimarisi](./devtest-lab-reference-architecture.md#architecture)

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

**Kılavuz**: Azure kaynaklarını açmak ve yapılandırmak için yapılandırılmış çok faktörlü kimlik doğrulaması ile ayrıcalıklı erişim iş Istasyonları (Paw) kullanın. 

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 
- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: ortamda şüpheli veya güvenli olmayan bir etkinlik olduğunda Günlükler ve uyarılar oluşturmak için Azure Active Directory (Azure AD) güvenlik raporları kullanın. Kimlik ve erişim etkinliğini izlemek için Azure Güvenlik Merkezi 'ni kullanın.

- [Riskli etkinlik bayrağıyla işaretlenen Azure AD kullanıcılarını belirleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Güvenlik Merkezi’nde kullanıcıların kimliğini ve erişim etkinliğini izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: IP adresi aralıklarının veya ülkelerin/bölgelerin yalnızca belirli mantıksal gruplarından erişime izin vermek için adlandırılmış konumlar kullanın.

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifrelemeyi kullanarak verileri korur. Azure AD Ayrıca, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlar. Ayrıca, grup üyeliklerini etkin bir şekilde yönetmek, kurumsal uygulamalara erişmek ve rol atamaları için Azure kimlik erişimi İncelemeleri kullanın. Yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için, Kullanıcı erişimi düzenli olarak incelenebilir.

- [Azure AD raporlamayı anlama](../active-directory/reports-monitoring/overview-reports.md)

- [Azure kimlik erişimi incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: herhangi bir güvenlik bilgisi ve olay yönetimi (SIEM)/Monitoring aracıyla tümleştirmenize olanak tanıyan Azure Active Directory (Azure AD) oturum açma etkinliği, denetim ve risk olay günlüğü kaynaklarına erişiminiz vardır.

Bu işlemi, Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturarak ve Log Analytics çalışma alanına denetim günlüklerini ve oturum açma günlüklerini göndererek kolaylaştırabilirsiniz. Log Analytics çalışma alanı içinde uyarıları yapılandırabilirsiniz.

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapmasından uyar

**Rehberlik**: Kullanıcı kimlikleriyle ilgili şüpheli eylemleri algılanan otomatik yanıtları yapılandırmak için Azure Active Directory (Azure AD) riskini ve kimlik koruması özelliklerini kullanın.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: hassas bilgileri depolayan veya işleyen Azure kaynaklarını izlemeye yardımcı olması için etiketleri kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: kaynaklara erişimi yönetmek için Azure RBAC kullanma

**Rehberlik**: Azure DevTest Labs Labs 'e erişimi denetlemek için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanın.

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md)

- [DevTest Labs 'de rolleri anlayın](devtest-lab-add-devtest-user.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Rehberlik**: değişikliklerin DevTest Labs örneklerine ve diğer kritik veya ilgili kaynaklara ne zaman gerçekleştiği hakkında uyarı oluşturmak Için Azure etkinlik günlüğü Ile Azure izleyici 'yi kullanın.

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

- [DevTest Labs etkinlik günlüğü olayları için uyarı oluşturma](create-alerts.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: aboneliklerinizde tüm kaynakları (DevTest Labs kaynakları dahil) sorgulamak ve saptamak Için Azure Kaynak grafiğini kullanın. Kiracınızda uygun (okuma) izinleriniz olduğundan ve aboneliklerinizde tüm Azure aboneliklerini ve kaynaklarını numaralandırabilmeniz için emin olun.

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Kılavuz**: Azure kaynaklarına Etiketler uygulayarak bunları bir sınıflandırmaya göre mantıksal olarak organize etmek için meta veriler sağlar.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [DevTest Labs için Etiketler yapılandırma](devtest-lab-add-tag.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: etiketleme, yönetim grupları ve ayrı abonelikler kullanın ve laboratuvarları ve laboratuvara ilgili kaynakları düzenlemek ve izlemek için uygun yerlerde farklı laboratuvarları kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların abonelikten hızlıca silindiğinden emin olun.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [DevTest Labs kullanarak laboratuvar oluşturma](devtest-lab-create-lab.md)

- [Etiketler oluşturma ve kullanma](devtest-lab-add-tag.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynakları envanterini tanımlama ve sürdürme

**Rehberlik**: Kurumsal gereksinimlere göre işlem kaynakları Için onaylanan Azure kaynakları ve onaylanan yazılım envanterini oluşturun. Abonelik Yöneticisi olarak, yapılandırılmış laboratuvar makineleri gruplarında çalışmasına izin verilen bir uygulamalar kümesi tanımlamanıza yardımcı olması için Azure Güvenlik Merkezi 'nin bir özelliği olan Uyarlamalı uygulama denetimlerini de kullanabilirsiniz. Bu özellik hem Azure hem de Azure olmayan Windows (tüm sürümler, klasik veya Azure Resource Manager) ve Linux makineler için kullanılabilir.

- [Uyarlamalı uygulama denetimini etkinleştirme](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak için Azure ilkesini kullanın:

- İzin verilmeyen kaynak türleri

- İzin verilen kaynak türleri

Ayrıca, aboneliklerdeki kaynakları sorgulamak/saptamak için Azure Kaynak grafiğini kullanın. Depolama hesapları gibi yüksek güvenlik tabanlı ortamlarda yardımcı olabilir.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: işlem kaynakları içindeki onaylanmamış yazılım uygulamaları için izleyici

**Kılavuz**: Azure Otomasyonu, dağıtım, işlemler ve iş yüklerinin ve kaynakların yetkisini alma sırasında tamamen denetim sağlar. Abonelik Yöneticisi olarak, aboneliğinizdeki DevTest Labs sanal makinelerinde bulunan tüm yazılımlarla ilgili bilgi toplamayı otomatik hale getirmek için Azure sanal makine envanterini kullanabilirsiniz. Yazılım adı, sürüm, yayımcı ve yenileme süresi özellikleri Azure portal kullanılabilir. Yüklemenin tarih ve diğer bilgilerine erişim sağlamak için, müşteri, Konuk düzeyinde tanılamayı etkinleştirmek ve Windows olay günlüklerini bir Log Analytics çalışma alanına getirmelerini sağlamak için gereklidir.

Yazılım uygulamalarının izlenmesi için Değişiklik İzleme kullanmanın yanı sıra, Azure Güvenlik Merkezi 'ndeki Uyarlamalı uygulama denetimleri, makinelerinizde çalışan uygulamaları analiz etmek ve bu zekası aracılığıyla bir izin verilenler listesi oluşturmak için makine öğrenimini kullanır. Bu özellik, uygulama izin verilenler listesi ilkelerini yapılandırma ve sürdürme sürecini önemli ölçüde basitleştirir. böylece, ortamınızda istenmeyen yazılımların kullanılmasını önleyebilirsiniz. Denetim modunu veya zorlama modunu yapılandırabilirsiniz. Denetim modu yalnızca korumalı VM 'lerdeki etkinliği denetler. Zorla modu kuralları zorunlu kılar ve çalışmasına izin verilmeyen uygulamaların engellenmiş olmasını sağlar. 

- [Azure Otomasyonu’na giriş](../automation/automation-intro.md)

- [Azure VM envanterini etkinleştirme](../automation/automation-tutorial-installed-software.md)

- [Uyarlamalı uygulama denetimlerini anlama](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Kılavuz**: Azure Otomasyonu, dağıtım, işlemler ve iş yüklerinin ve kaynakların yetkisini alma sırasında tamamen denetim sağlar. Abonelik Yöneticisi olarak, DevTest Labs 'de barındırılan VM 'lerde yüklü tüm yazılımları tanımlamak için Değişiklik İzleme kullanabilirsiniz. Kendi işleminizi uygulayabilir veya yetkisiz yazılımı kaldırmak için Azure Otomasyonu durum yapılandırması ' nı kullanabilirsiniz.

- [Azure Otomasyonu’na giriş](../automation/automation-intro.md)

- [Değişiklik İzleme çözümü ile ortamınızdaki değişiklikleri izleyin](../automation/change-tracking/overview.md)

- [Azure Otomasyonu durum yapılandırmasına genel bakış](../automation/automation-dsc-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="68-use-only-approved-applications"></a>6,8: yalnızca onaylanan uygulamaları kullan

**Rehberlik**: Abonelik Yöneticisi olarak, yalnızca yetkili yazılımın yürütüldüğünü ve tüm yetkisiz yazılımların DevTest Labs 'de barındırılan Azure VM 'lerde yürütülmesini engellediği sağlamak Için Azure Güvenlik Merkezi Uyarlamalı uygulama denetimleri kullanabilirsiniz.

- [Azure Güvenlik Merkezi Uyarlamalı uygulama denetimlerini kullanma](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak için Azure ilkesini kullanın:
- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

Başvuru malzemeleri:

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](../governance/policy/samples/built-in-policies.md#general)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: onaylanan yazılım başlıkları envanterini koruyun

**Rehberlik**: Uyarlamalı uygulama denetimi, Azure Güvenlik Merkezi 'Nden, DevTest Labs 'de barındırılan Azure ve Azure dışı makinelerde (Windows ve Linux) hangi uygulamaların çalıştırılacağını denetlemenize yardımcı olan akıllı, otomatik, uçtan uca bir çözümdür. Bu ayarı, DevTest Labs 'de barındırılan temeldeki işlem kaynakları için yapılandırmak üzere bir abonelik yöneticisi olmanız gerektiğini unutmayın. Bu ayar kuruluşunuzun gereksinimini karşılamıyorsa üçüncü taraf çözümünü uygulayın.

- [Azure Güvenlik Merkezi Uyarlamalı uygulama denetimlerini kullanma](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Kılavuz**: **Microsoft Azure Yönetim** uygulamasına yönelik **blok erişimini** yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak için Azure koşullu erişimi kullanın.

- [Azure Resource Manager erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: kullanıcıların işlem kaynaklarında betikleri yürütme yeteneğini sınırlayın

**Kılavuz**: betiklerin türüne bağlı olarak, kullanıcıların DevTest Labs 'de barındırılan VM 'ler içinde betikleri yürütme yeteneğini sınırlamak için sisteme özgü işletim yapılandırmalarına veya üçüncü taraf kaynaklarına sahip olabilirsiniz. Ayrıca, Azure Güvenlik Merkezi Uyarlamalı uygulama denetimlerini yalnızca yetkili yazılımın yürütüldüğünü ve tüm yetkisiz yazılımların temel alınan Azure VM 'lerinde yürütülmesini engellediği şekilde kullanabilirsiniz.

- [Windows ortamlarında PowerShell betiği yürütmeyi denetleme](/powershell/module/microsoft.powershell.security/set-executionpolicy?preserve-view=true&view=powershell-7)

- [Azure Güvenlik Merkezi Uyarlamalı uygulama denetimlerini kullanma](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: yüksek riskli uygulamaları fiziksel olarak veya mantıksal olarak ayırt edin

**Rehberlik**: Azure ortamınızda dağıtılan yüksek riskli uygulamalar, sanal ağ, alt ağ, abonelikler, yönetim grupları vb. kullanılarak yalıtılmış olabilir. ve bir Azure Güvenlik Duvarı, Web uygulaması güvenlik duvarı (WAF) veya ağ güvenlik grubu (NSG) ile yeterince güvenli hale getirilir.

- [DevTest Labs için sanal ağı yapılandırma](devtest-lab-configure-vnet.md)

- [Azure Güvenlik Duvarı 'na genel bakış](../web-application-firewall/overview.md)

- [Web uygulaması güvenlik duvarına genel bakış](../virtual-network/network-security-groups-overview.md)

- [Ağ güvenliğine genel bakış](security-baseline.md)

- [Kaynaklarınızı Azure yönetim gruplarıyla düzenleme](../governance/management-groups/overview.md)

- [Abonelik kararı kılavuzu](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Rehberlik**: DevTest Labs 'in bir parçası olarak oluşturulan Azure kaynaklarınızın yapılandırmasını denetlemek veya zorlamak için özel ilkeler oluşturmak üzere Azure ilke diğer adlarını kullanın. Yerleşik Azure Ilke tanımlarını da kullanabilirsiniz.

Ayrıca, Azure Resource Manager, yapılandırmanın kuruluşunuzun güvenlik gereksinimlerini karşıladığından/aştığından emin olmak için gözden geçirilmesi gereken JavaScript Nesne Gösterimi (JSON) içinde şablonu dışarı aktarma özelliğine sahiptir.

Azure Güvenlik Merkezi 'nin önerilerini Azure kaynaklarınız için güvenli bir yapılandırma temeli olarak da kullanabilirsiniz.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Öğretici: uyumluluğu zorlamak için ilke oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Güvenlik önerileri - başvuru kılavuzu](../security-center/recommendations-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: güvenli işletim sistemi yapılandırması oluşturma

**Rehberlik**: DevTest Labs 'in bir parçası olarak oluşturulan tüm temeldeki işlem kaynaklarında güvenlik yapılandırmalarının bakımını yapmak Için Azure Güvenlik Merkezi önerilerini kullanın. Ayrıca, kuruluşunuz tarafından istenen işletim sisteminin güvenlik yapılandırmasını oluşturmak için özel işletim sistemi görüntülerini veya Azure Automation durum yapılandırması veya DevTest Labs yapılarını kullanabilirsiniz.

- [Azure Güvenlik Merkezi önerilerini izleme](../security-center/security-center-recommendations.md)

- [Güvenlik önerileri - başvuru kılavuzu](../security-center/recommendations-reference.md)

- [Azure Otomasyonu durum yapılandırmasına genel bakış](../automation/automation-dsc-overview.md)

- [Bir VHD 'YI karşıya yükleyin ve Azure 'da yeni Windows VM 'Leri oluşturmak için kullanın](../virtual-machines/windows/upload-generalized-managed.md)

- [Azure CLı ile özel diskten bir Linux VM oluşturma](../virtual-machines/linux/upload-vhd.md)

- [Özel görüntüleri oluşturma ve birden çok DevTest Labs 'e dağıtma](image-factory-save-distribute-custom-images.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Rehberlik**: DevTest Labs 'in bir parçası olarak oluşturulan Azure kaynaklarınız genelinde güvenli ayarları zorlamak Için Azure ilkesi **reddetme** ve **dağıtım yok** kurallarını kullanın. Ayrıca, kuruluşunuz tarafından gerekli olan Azure kaynaklarınızın güvenlik yapılandırmasını sürdürmek için Azure Resource Manager şablonları kullanabilirsiniz.

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

- [Uyumluluğu zorunlu tutmak için ilkeleri oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager şablonlarına genel bakış](../azure-resource-manager/templates/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: güvenli işletim sistemi yapılandırmalarının bakımını yapma

**Rehberlik**: laboratuvarın bir parçası olarak oluşturulan temel Azure işlem kaynaklarınız üzerinde güvenlik açığı değerlendirmelerinde Azure Güvenlik Merkezi 'nin önerilerini izleyin. Ayrıca, kuruluşunuz tarafından istenen işletim sisteminin güvenlik yapılandırmasını sürdürmek için Azure Resource Manager şablonları, özel işletim sistemi görüntüleri veya Azure Otomasyonu durum yapılandırması ' nı kullanabilirsiniz. Ayrıca, istenen tüm yapılandırmalara göre düzenli olarak görüntü oluşturup dağıtan bir kod yapılandırma çözümü olan Image Factory çözümünü de kullanabilirsiniz.

Ayrıca, Microsoft tarafından yayımlanan Azure Market sanal makine görüntüleri Microsoft tarafından yönetilir ve sürdürülür.

- [Azure Güvenlik Merkezi güvenlik açığı değerlendirmesi önerilerini uygulama](../security-center/deploy-vulnerability-assessment-vm.md)

- [Azure Otomasyonu durum yapılandırmasına genel bakış](../automation/automation-dsc-overview.md)

- [Bir VHD’yi Azure’a yüklemek ve yeni bir sanal makine oluşturmak için örnek betik](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

- [DevTest Labs 'de görüntü fabrikası oluşturma](image-factory-create.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilkeleri, Azure Resource Manager şablonları ve Istenen durum yapılandırması betikleri gibi kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure DevOps kullanın. Azure DevOps 'da yönettiğiniz kaynaklara erişmek için, Azure DevOps ile tümleşikse, belirli kullanıcılar, yerleşik güvenlik grupları veya Azure Active Directory (Azure AD) içinde tanımlanan gruplar için izin verebilir veya vermeyebilirsiniz.

- [Git öğreticisi Azure Repos](/azure/devops/repos/git/gitworkflow)

- [İzinler ve gruplar hakkında](/azure/devops/organizations/security/about-permissions?preserve-view=true&tabs=preview-page&view=azure-devops)

- [Azure DevTest Labs ile Azure DevOps iş akışı arasında tümleştirme](devtest-lab-dev-ops.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: özel işletim sistemi görüntülerini güvenli bir şekilde depolayın

**Kılavuz**: özel görüntüler kullanıyorsanız, görüntülere yalnızca yetkili kullanıcıların erişebildiğinden emin olmak için Azure rol tabanlı erişim denetimi 'Ni (Azure RBAC) kullanın. Paylaşılan bir görüntü Galerisi 'ni kullanarak görüntülerinizi, ihtiyaç duyulan belirli laboratuvarlara paylaşabilirsiniz. Kapsayıcı görüntüleri için, bunları Azure Container Registry depolayın ve yalnızca yetkili kullanıcıların görüntülere erişebildiğinden emin olmak için Azure RBAC kullanın.

- [Azure RBAC 'yi anlama](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [DevTest Labs için paylaşılan görüntü Galerisi yapılandırma](configure-shared-image-gallery.md)

- [Container Registry için Azure RBAC 'nı anlama](../container-registry/container-registry-roles.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Kılavuz**: Azure Ilkesini kullanarak Azure kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın. DevTest Labs altında oluşturulan Azure kaynaklarınızın ağ yapılandırmasını denetlemek veya zorlamak için özel ilkeler oluşturmak üzere Azure Ilke diğer adlarını kullanın. Ayrıca, belirli kaynaklarınızla ilgili yerleşik ilke tanımlarından da yararlanabilirsiniz. Ayrıca, Azure Otomasyonu ' nu yapılandırma değişikliklerini dağıtmak için de kullanabilirsiniz.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Diğer adları kullanma](../governance/policy/concepts/definition-structure.md#aliases)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: işletim sistemleri için yapılandırma yönetimi araçları dağıtma

**Kılavuz**: Azure Otomasyonu durum yapılandırması, herhangi bir bulutta veya şirket içi veri merkezinde Istenen durum YAPıLANDıRMASı (DSC) düğümleri için bir yapılandırma yönetim hizmetidir. Makineleri kolayca ekleyebilir, bunlara bildirime dayalı yapılandırmalara atayabilir ve her makinenin, belirttiğiniz istenen duruma göre uyumluluğunu gösteren raporları görüntüleyebilirsiniz. Ayrıca, kurumsal ilkelerin izlediklerinden emin olmak için her laboratuvar makinesinde yüklenebilen özel bir yapıt da yazabilirsiniz. 

- [Azure Otomasyonu durum yapılandırmasına göre yönetim için makine ekleme](../automation/automation-dsc-onboarding.md)

- [DevTest Labs sanal makineleri için özel yapılar oluşturma](devtest-lab-artifact-author.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: DevTest Labs altında oluşturulan Azure kaynaklarınıza yönelik temel taramalar gerçekleştirmek Için Azure Güvenlik Merkezi 'ni kullanın. Ayrıca Azure Ilkesi 'ni kullanarak Azure Kaynak konfigürasyonları 'nı uyarır ve denetleyin.

- [Azure Güvenlik Merkezi 'nde öneriler nasıl düzeltileceği](../security-center/security-center-remediate-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: işletim sistemleri için otomatik yapılandırma izlemeyi Uygula

**Rehberlik**: laboratuvarınızda çalıştırılan kapsayıcılar için işletim sistemi ve Docker ayarlarına yönelik temel taramalar gerçekleştirmek üzere Azure Güvenlik Merkezi 'ni kullanın.

- [Azure Güvenlik Merkezi'ndeki kapsayıcı önerilerini anlama](../security-center/container-security.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: bulut uygulamalarınız için gizli yönetimi basitleştirmek ve güvenli hale getirmek için Azure Key Vault ile birlikte yönetilen hizmet kimliği kullanın.

- [DevTest Labs 'de Azure Resource Manager ortamları dağıtmak için yönetilen kimlik yapılandırma](use-managed-identities-environments.md)

- [DevTest Labs 'de sanal makineleri dağıtmak için yönetilen kimlik yapılandırma](enable-managed-identities-lab-vms.md)

- [Anahtar Kasası oluşturma](../key-vault/general/quick-create-portal.md)

- [Yönetilen kimlik ile Key Vault kimlik doğrulaması sağlama](../key-vault/general/authentication.md)

- [Key Vault erişim ilkesi atama](../key-vault/general/assign-access-policy-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Rehberlik**: Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik ile Azure hizmetleri sağlamak Için Yönetilen kimlikler kullanın. Yönetilen kimlikler, kodunuzda kimlik bilgileri olmadan Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmanıza olanak sağlar.

- [DevTest Labs 'de Azure Resource Manager ortamları dağıtmak için yönetilen kimlik yapılandırma](use-managed-identities-environments.md)

- [DevTest Labs 'de sanal makineleri dağıtmak için yönetilen kimlik yapılandırma](enable-managed-identities-lab-vms.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: kod içinde kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayın. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar.

- [Kimlik bilgisi tarayıcısını ayarlama](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: merkezi olarak yönetilen kötü amaçlı yazılımdan koruma yazılımı kullanma

**Rehberlik**: kaynaklarınızı sürekli olarak izlemek ve savunmak için Azure Için Microsoft kötü amaçlı yazılımdan koruma kullanın. Linux için üçüncü taraf kötü amaçlı yazılımdan koruma çözümünü kullanın.  Ayrıca, depolama hesaplarına yüklenen kötü amaçlı yazılımları algılamak için veri Hizmetleri için Azure Güvenlik Merkezi 'nin tehdit algılamasını kullanın. 

- [Azure için Microsoft Antimalware 'i yapılandırma](../security/fundamentals/antimalware.md) 

- [Azure Güvenlik Merkezi’nde tehdit koruması](../security-center/azure-defender.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: işlem dışı Azure kaynaklarına yüklenecek dosyaları önceden Tara

**Rehberlik**: Microsoft kötü amaçlı yazılımdan koruma, Azure hizmetlerini destekleyen temel ana bilgisayar üzerinde etkinleştirilmiştir (örneğin, bir laboratuvarda barındırılan Azure App Service), ancak içeriklerde çalışmaz. 

App Service, Data Lake Storage, BLOB depolama gibi işlem dışı Azure kaynaklarına yüklenen dosyaları önceden tarayın. 

Depolama hesaplarına yüklenen kötü amaçlı yazılımları algılamak için veri Hizmetleri için Azure Güvenlik Merkezi 'nin tehdit algılamasını kullanın. 

- [Azure için Microsoft Antimalware 'i anlama](../security/fundamentals/antimalware.md) 

- [Azure Güvenlik Merkezi 'nin veri Hizmetleri tehdit algılamasını anlama](../security-center/azure-defender.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: kötü amaçlı yazılımdan koruma yazılımlarının ve imzaların güncelleştirildiğinden emin olun

**Kılavuz**: dağıtıldığında, Azure Için Microsoft Antimalware, varsayılan olarak en son imza, platform ve altyapı güncelleştirmelerini otomatik olarak yükler. Azure Güvenlik Merkezi 'ndeki önerileri takip edin: "Işlem &amp; Uygulamaları" temel alınan işlem kaynakları için tüm uç noktaların en son imzalara güncel olduğundan emin olmak için. Windows işletim sistemi, Azure Güvenlik Merkezi ile tümleşen Microsoft Defender Gelişmiş tehdit koruması hizmeti ile virüs veya kötü amaçlı yazılım tabanlı saldırılar riskini sınırlamak üzere ek güvenlik ile daha da korunabilir.

- [Azure için Microsoft Antimalware nasıl dağıtılır](../security/fundamentals/antimalware.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli Otomatik yedeklemeli UPS sağlayın

**Rehberlik**: şu anda Azure DevTest Labs VM yedeklemelerini ve anlık görüntülerini desteklemez. Ancak, DevTest Labs 'de barındırılan temel Azure VM 'lerinde Azure Backup etkinleştirebilir ve yapılandırabilirsiniz. Ayrıca, temeldeki işlem kaynaklarına uygun erişiminiz olduğu sürece otomatik yedeklemeler için istenen sıklık ve bekletme süresini de yapılandırabilirsiniz. 

- [Azure VM yedeklemesine genel bakış](../backup/backup-azure-vms-introduction.md)

- [VM ayarlarından bir Azure VM 'yi yedekleme](../backup/backup-azure-vms-first-look-arm.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: şu anda Azure DevTest Labs VM yedeklemelerini ve anlık görüntülerini desteklemez. Ancak, temel alınan işlem kaynaklarına uygun erişiminiz olduğu sürece, DevTest Labs veya bu örneklere eklenen yönetilen diskler için, PowerShell veya REST API 'Leri kullanılarak barındırılan temel Azure sanal makinelerinizin anlık görüntülerini oluşturabilirsiniz. Ayrıca, Azure Key Vault içinde müşteri tarafından yönetilen tüm anahtarları da yedekleyebilirsiniz.

Hedef Azure VM 'lerinde, istenen sıklık ve bekletme dönemlerinde Azure Backup etkinleştirin. Sistem durumu yedeklemesini tamamen içerir. Azure Disk Şifrelemesi kullanıyorsanız, Azure VM yedeklemesi, müşteri tarafından yönetilen anahtarların yedeklemesini otomatik olarak işler.

- [Şifreleme kullanan Azure VM 'lerinde yedekleme](../backup/backup-azure-vms-encryption.md)

- [Azure VM yedeklemesine genel bakış](../backup/backup-azure-vms-introduction.md)

- [Azure VM yedeklemesine genel bakış](../backup/backup-azure-vms-introduction.md)

- [Azure 'da Key Vault anahtarlarını yedekleme](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: Azure Backup içindeki içeriğin düzenli olarak veri geri yüklemesini gerçekleştirme olanağı sağlar. Gerekirse, içeriği yalıtılmış bir sanal ağa veya aboneliğe geri yüklemeyi test edin. Ayrıca, yedeklenen müşteri tarafından yönetilen anahtarların sınamasını geri yükleme.

Azure Disk Şifrelemesi kullanıyorsanız, Azure VM 'yi disk şifreleme anahtarlarıyla geri yükleyebilirsiniz. Disk şifrelemeyi kullanırken, Azure VM 'yi disk şifreleme anahtarlarıyla geri yükleyebilirsiniz.

- [Şifreleme kullanan Azure VM 'lerinde yedekleme](../backup/backup-azure-vms-encryption.md)

- [Azure VM yedeğinden dosyaları kurtarma](../backup/backup-azure-restore-files-from-vm.md)

- [Azure 'da Anahtar Kasası anahtarlarını geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

- [Şifrelenmiş bir VM 'yi yedekleme ve geri yükleme](../backup/backup-azure-vms-encryption.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: yönetilen diskleri Azure Backup yedeklediyseniz, vm 'ler depolama HIZMETI ŞIFRELEMESI (SSE) ile geri kalanıyla şifrelenir. Azure Backup, Azure disk şifrelemesi kullanılarak şifrelenmiş Azure VM 'Leri de yedekleyebilir. Azure disk şifrelemesi, bir anahtar kasasında gizli dizi olarak korunmuş olan BitLocker şifreleme anahtarları (BEKs) ile tümleşir. Azure disk şifrelemesi Ayrıca Azure Key Vault anahtar şifreleme anahtarları (KEKs) ile tümleşir. Anahtarları yanlışlıkla veya kötü amaçlı silmeye karşı korumak için Key Vault Soft-Delete etkinleştirin.

- [VM 'Ler için geçici silme](../backup/soft-delete-virtual-machines.md)

- [Azure Key Vault geçici silmeye genel bakış](../key-vault/general/soft-delete-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: kuruluşunuz için bir olay yanıtı Kılavuzu geliştirin. Tüm personel rollerinin yanı sıra olay işleme ve yönetim 'in algılanmasından olay sonrası gözden geçirme aşamalarını tanımlayan, yazılı olay yanıt planları bulunduğundan emin olun. 

- [Kendi güvenlik olay yanıtı işleminizi oluşturma kılavuzu](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Güvenlik Yanıt Merkezi 'nin bir olayın anatomisi](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Kendi olay yanıtı planınızın oluşturulmasına yardımcı olması için NıST 'nin bilgisayar güvenliği olay Işleme kılavuzunu kullanın](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Kılavuz**: Azure Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, uyarı veren etkinliğin arkasında kötü amaçlı bir amaç olduğunu ve uyarıyı vermek için kullanılan analitik düzeyini, ne kadar güvenli bir güvenlik merkezinin olduğunu temel alır.

Ayrıca, abonelikleri kullanarak abonelikleri işaretleyin ve Azure kaynaklarını tanımlamak ve kategorilere ayırmak için özellikle de hassas verileri işleyen bir adlandırma sistemi oluşturun.  Olayın gerçekleştiği Azure kaynakları ve ortamının önem derecesine bağlı olarak, uyarıların düzeltilmesine öncelik vermek sizin sorumluluğunuzdadır. 

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md) 

- [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: Azure kaynaklarınızın korunmasına yardımcı olmak için, sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve boşlukları belirleyip yanıt planınızı gerektiği gibi düzeltin. 

- [NıST 'nin Yayımlama Kılavuzu, BT planları ve becerileri için programları test etme, eğitim ve alıştırma Kılavuzu](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: Microsoft Güvenlik Yanıt MERKEZI (MSRC), verilerinize izinsiz veya yetkisiz bir taraf tarafından erişildiğini belirlerse, Microsoft tarafından sizinle iletişim kurmak için güvenlik olayı iletişim bilgileri kullanılacaktır. Sorunların çözümlendiğinden emin olmak için gerçesonra olayları gözden geçirin. 

- [Azure Güvenlik Merkezi güvenlik ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: güvenlik uyarılarını olay yanıt sisteminizle birleştirme

**Kılavuz**: Azure kaynaklarına yönelik riskleri belirlemenize yardımcı olmak için sürekli dışarı aktarma özelliğini kullanarak Azure Güvenlik Merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Sürekli dışa aktarma, uyarıları ve önerileri el ile veya devam eden sürekli bir biçimde dışa aktarmanız sağlar. Azure Güvenlik Merkezi veri bağlayıcısını kullanarak uyarıları Azure Sentinel 'e aktarabilirsiniz. 

- [Sürekli dışarı aktarmayı yapılandırma](../security-center/continuous-export.md) 

- [Uyarıların Azure Sentinel’e akışını yapma](../sentinel/connect-azure-security-center.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: güvenlik uyarılarına yanıtı otomatikleştirme

**Rehberlik**: iş akışı Otomasyonu özelliği Azure Güvenlik Merkezi 'Ni kullanarak Azure kaynaklarınızı korumaya yönelik güvenlik uyarılarına ve önerilerine yönelik yanıtları otomatik olarak tetikleyin. 

- [Güvenlik Merkezi 'nde iş akışı Otomasyonu nasıl yapılandırılır](../security-center/workflow-automation.md)

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
