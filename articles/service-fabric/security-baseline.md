---
title: Service Fabric için Azure Güvenlik temeli
description: Service Fabric güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ee294cff85bb71b5c2a238fcf985fc870ed32618
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285471"
---
# <a name="azure-security-baseline-for-service-fabric"></a>Service Fabric için Azure Güvenlik temeli

Bu güvenlik temeli, Service Fabric için [Azure Güvenlik kıyaslama sürümü 1.0'dan](../security/benchmarks/overview-v1.md) rehberlik uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Service Fabric için geçerli olan ilgili kılavuza göre gruplandırılır.

> [!NOTE]
> Service Fabric için geçerli olmayan **denetimler** veya Microsoft 'un hangi sorumluluğun çıkarılmadığı. Service Fabric Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, **[tam Service Fabric güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/service-fabric-security-baseline-v1.1.xlsx)** bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: tüm sanal ağ alt ağ dağıtımlarının, uygulamanızın güvenilen bağlantı noktalarına ve kaynaklarına özel ağ erişim denetimleriyle uygulanmış bir ağ güvenlik grubu olduğundan emin olun. 

- [Şablon kullanarak Azure Güvenlik Duvarı’nı dağıtma](../firewall/deploy-template.md)

- [Azure ağ güvenlik gruplarını (NSG 'ler) kullanarak çevre ağları oluşturma](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices#use-network-isolation-and-security-with-azure-service-fabric)

- [Azure Service Fabric kümenizi mevcut bir sanal ağla tümleştirme](service-fabric-patterns-networking.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: sanal ağların, alt ağların ve NIC 'lerin yapılandırmasını ve trafiğini izleyin ve günlüğe kaydedin

**Rehberlik**: Güvenlik Merkezi 'ni kullanın ve Azure Service Fabric kümenizin güvenliğini sağlamak için kullanılan sanal ağ, alt ağ ve ağ güvenlik grubu için ağ koruma önerilerini düzeltin. Ağ güvenlik grubu akış günlüklerini etkinleştirin ve günlükleri bir Azure depolama hesabına trafik denetimine gönderin. Ayrıca, bir Azure Log Analytics çalışma alanına ağ güvenlik grupları akış günlükleri gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Azure Trafik Analizi kullanabilirsiniz. Azure Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="13-protect-critical-web-applications"></a>1,3: kritik Web uygulamalarını koruma

**Rehberlik**: kullanıcılar, cihazlar veya diğer uygulamalar için tek bir giriş noktası sağlamak üzere ön uç ağ geçidi sağlama. Azure API Management, arka uç hizmetlerine erişimi güvenli hale getirmeye, azaltmayı kullanarak DOS saldırılarını engellemeye ve API anahtarlarını, JWT belirteçlerini, sertifikaları ve diğer kimlik bilgilerini doğrulamanıza olanak sağlayan, doğrudan Service Fabric ile tümleşir.

Azure Web uygulaması güvenlik duvarını (WAF), gelen trafiğin ek incelemesi için kritik Web uygulamaları önünde dağıtmaya dikkat edin. WAF ve alma günlükleri için tanılama ayarını bir depolama hesabı, Olay Hub 'ı veya Log Analytics çalışma alanında etkinleştirin.

- [Service Fabric ve Azure API Management'a genel bakış](service-fabric-api-management-overview.md)

- [API Management'ı Application Gateway ile için sanal ağda tümleştirme](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Azure WAF dağıtma](../web-application-firewall/ag/create-waf-policy-ag.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Kılavuz**: DDoS saldırılarına karşı korumalar için Azure Service Fabric kümenizin dağıtıldığı sanal ağ üzerinde Azure DDoS standart korumasını etkinleştirin. Bilinen kötü amaçlı veya kullanılmayan Internet IP adresleriyle iletişimleri reddetmek için Azure Güvenlik Merkezi tümleşik tehdit zekasını kullanın.

- [DDoS korumasını yapılandırma](../ddos-protection/manage-ddos-protection.md)

- [Azure Güvenlik Merkezi tümleşik tehdit zekasını anlama](../security-center/azure-defender.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Rehberlik**: Service Fabric kümenizi korumak için kullanılan alt ağa bağlı ağ güvenlik grupları için ağ güvenlik grubu akış günlüklerini etkinleştirin. Flow kayıtları oluşturmak için NSG akış günlüklerini bir Azure depolama hesabına kaydedin. Anormal etkinlikleri araştırmak için gerekliyse, Azure ağ Izleyicisi paket yakalamayı etkinleştirin.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Ağ İzleyicisini etkinleştirme](../network-watcher/network-watcher-create.md)

- [NSG akış günlüklerini görselleştirmek için trafik analizini kullanma](../network-watcher/traffic-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Kılavuz**: Azure Marketi 'nden, yük İnceleme ÖZELLIKLERI ile KIMLIKLER/IP işlevlerini destekleyen bir teklif seçin.  Yük incelemesini temel alan yetkisiz giriş algılama ve/veya önleme bir gereksinim değilse, tehdit zekasıyla Azure Güvenlik Duvarı kullanılabilir. Azure Güvenlik Duvarı tehdit zekası tabanlı filtreleme, bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen ve giden trafiği uyarabilir ve reddedebilir. IP adresleri ve etki alanları, Microsoft Tehdit Analizi akışından alınır. 

Kötü amaçlı trafiği algılamak ve/veya reddetmek için kuruluşunuzun ağ sınırlarının her birinde tercih ettiğiniz güvenlik duvarı çözümünü dağıtın. 

- [Azure Market](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Güvenlik Duvarı ile uyarıları yapılandırma](../firewall/threat-intel.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Web uygulamalarına trafiği yönetme

**Rehberlik**: güvenilen SERTIFIKALAR için HTTPS/SSL özellikli Web uygulamaları için Azure Application Gateway dağıtın. 

- [Application Gateway dağıtma](../application-gateway/quick-create-portal.md)

- [Application Gateway HTTPS kullanacak şekilde yapılandırma](../application-gateway/create-ssl-portal.md)

- [Azure Web uygulaması ağ geçitleri ile katman 7 yük dengelemesini anlama](../application-gateway/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Kılavuz**: Azure Service Fabric kümenizin dağıtıldığı alt ağa bağlı ağ güvenlik grupları (NSG) üzerinde ağ erişim denetimleri tanımlamak için sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adı (örn., Apimanaya) belirterek, ilgili hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

- [Sanal ağ hizmeti etiketleri](../virtual-network/service-tags-overview.md)

- [Service Fabric ağ en iyi uygulamaları](service-fabric-best-practices-networking.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Rehberlik**: Azure Service Fabric kümeniz ile ilgili ağ kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın. Azure Service Fabric kümenizin ağ yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft. ServiceFabric" ve "Microsoft. Network" ad alanlarında Azure Ilke diğer adlarını kullanın.

Ayrıca, tek bir şema tanımında Azure Resource Manager şablonları, Azure RBAC denetimleri ve ilkeleri gibi temel ortam yapıtlarını paketleyerek büyük ölçekli Azure dağıtımlarını basitleştirmek için Azure şemaları 'nı kullanabilirsiniz. Şema 'i yeni abonelikler ve ortamlara kolayca uygulayın ve sürüm oluşturma aracılığıyla denetimi ve yönetimi ayrıntılı olarak ayarlayın.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: ağ güvenlik grupları ve Service Fabric kümeniz ile ilişkili ağ güvenliği ve trafik akışıyla ilgili diğer kaynaklar için Etiketler kullanın. Tek tek ağ güvenlik grupları kuralları için "Açıklama" alanını kullanarak ağa giden/giden trafiğe izin veren tüm kurallar için iş ihtiyacı, süresi vb. belirtin.

Tüm kaynakların etiketlerle oluşturulmasını ve mevcut etiketlenmemiş kaynakları bilgilendirmesini sağlamak için etiketlemeyle ilgili yerleşik Azure Ilke tanımlarından herhangi birini ("etiket ve onun değeri gerektir" gibi) kullanın.

Azure PowerShell veya Azure komut satırı arabirimi 'ni (CLı) kullanarak, etiketlerine göre kaynakları arayabilir veya bunlarla ilgili eylemler gerçekleştirebilirsiniz.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Kılavuz**: Azure etkinlik günlüğü 'nü kullanarak ağ kaynak yapılandırmasını Izleyin ve Azure Service Fabric dağıtımlarınızla ilgili ağ kaynaklarına yönelik değişiklikleri tespit edin. Kritik ağ kaynaklarında yapılan değişiklikler yürürlüğe girdiğinde tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](/azure/azure-monitor/platform/alerts-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: küme tarafından oluşturulan güvenlik verilerini toplamak için Azure Service Fabric kümenizi Azure izleyici 'ye ekleyebilirsiniz. Bkz. Service Fabric ile örnek Tanılama sorunları ve çözümleri.

- [Azure Izleyici günlüklerini Service Fabric ile tümleştirmeyi yapılandırma](service-fabric-diagnostics-oms-setup.md)

- [Azure 'da izleme kapsayıcıları için Azure Izleyici günlüklerini ayarlama Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Yaygın Service Fabric senaryolarını tanılama](service-fabric-diagnostics-common-scenarios.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: Service Fabric kümesi Için Azure izleyicisini etkinleştirin ve bunu bir Log Analytics çalışma alanına yönlendirin. Bu, tüm Azure Service Fabric küme düğümleri için ilgili küme bilgilerini ve işletim sistemi ölçümlerini günlüğe kaydeder.

- [Azure Izleyici günlüklerini Service Fabric ile tümleştirmeyi yapılandırma](service-fabric-diagnostics-oms-setup.md)

- [Azure 'da izleme kapsayıcıları için Azure Izleyici günlüklerini ayarlama Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Log Analytics aracısını düğümleriniz üzerine dağıtma](service-fabric-diagnostics-oms-agent.md)

- [Log Analytics günlük aramaları](/azure/azure-monitor/log-query/log-query-overview)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: işletim sistemlerinden güvenlik günlüklerini toplama

**Kılavuz**: Azure Service Fabric kümesini Azure izleyici 'ye ekleme. Kullanılan Log Analytics çalışma alanının, kuruluşunuzun uyumluluk düzenlemelerine göre ayarlanmış günlük tutma süresine sahip olduğundan emin olun.

- [Azure Izleyici günlüklerini Service Fabric ile tümleştirmeyi yapılandırma](service-fabric-diagnostics-oms-setup.md)

- [Azure 'da izleme kapsayıcıları için Azure Izleyici günlüklerini ayarlama Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Log Analytics aracısını düğümleriniz üzerine dağıtma](service-fabric-diagnostics-oms-agent.md) 

- [Log Analytics çalışma alanı saklama süresini yapılandırma](/azure/azure-monitor/platform/manage-cost-storage)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Rehberlik**: Service Fabric kümesini Azure izleyici 'ye ekleme. Kullanılan Log Analytics çalışma alanının, kuruluşunuzun uyumluluk düzenlemelerine göre ayarlanmış günlük tutma süresine sahip olduğundan emin olun.

- [Azure Izleyici günlüklerini Service Fabric ile tümleştirmeyi yapılandırma](service-fabric-diagnostics-oms-setup.md)

- [Azure 'da izleme kapsayıcıları için Azure Izleyici günlüklerini ayarlama Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Log Analytics aracısını düğümleriniz üzerine dağıtma](service-fabric-diagnostics-oms-agent.md)

- [Log Analytics çalışma alanı saklama süresini yapılandırma](/azure/azure-monitor/platform/manage-cost-storage)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Kılavuz**: Azure Service Fabric günlüklerini sorgulamak için Azure Log Analytics çalışma alanı sorgularını kullanın.

- [Log Analytics günlük aramaları](/azure/azure-monitor/log-query/log-query-overview)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Kılavuz**: Azure Log Analytics çalışma alanını, güvenlik günlükleri ve Azure Service Fabric kümenizle ilgili olaylar için anormal etkinlikleri izlemek ve uyarmak üzere kullanın.

- [Azure Güvenlik Merkezi 'nde uyarıları yönetme](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics günlük verilerinde uyarı alma](/azure/azure-monitor/learn/tutorial-response)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="28-centralize-anti-malware-logging"></a>2,8: kötü amaçlı yazılımdan koruma 'yı merkezileştirme

**Yönergeler**: varsayılan olarak Windows Defender, windows Server 2016 ' de yüklüdür. Windows Defender kullanmıyorsanız yapılandırma kuralları için Antimaleware belgelerinize bakın. Windows Defender, Linux üzerinde desteklenmez.

- [Ayrıntılar için bkz. Windows Server 'da Windows Defender virüsten koruma 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="29-enable-dns-query-logging"></a>2,9: DNS sorgu günlüğünü etkinleştir

**Rehberlik**: DNS günlüğü için bir üçüncü taraf çözümü uygulayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="210-enable-command-line-audit-logging"></a>2,10: komut satırı denetim günlüğünü etkinleştir

**Rehberlik**: konsol günlüğünü her düğüm temelinde el ile yapılandırın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Kılavuz**: Azure Service Fabric kümesinin küme sağlanması sırasında oluşturulan yerel yönetim hesabının kaydını ve oluşturduğunuz diğer hesapları saklayın. Ayrıca, Azure Active Directory (Azure AD) Tümleştirmesi kullanılırsa, Azure AD 'nin açıkça atanması ve bu nedenle sorgulanabilir olması gereken yerleşik rolleri vardır. Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü kullanın.

Ayrıca, Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini de kullanabilirsiniz.

- [Azure AD 'de PowerShell ile dizin rolü alma](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Azure Güvenlik Merkezi ile kimlik ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Rehberlik**: bir küme sağlanırken Azure, Web portalı için yeni parolalar oluşturmanızı gerektirir. Değiştirilecek varsayılan parola yoktur, ancak Web portalı erişimi için farklı parolalar belirtebilirsiniz.

- [Azure portalda oluşturma](service-fabric-cluster-creation-via-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: Service Fabric Için kimlik doğrulamasını Azure Active Directory (Azure AD) ile tümleştirin. Adanmış yönetim hesaplarının kullanımı etrafında ilke ve yordamlar oluşturun.

Ayrıca, güvenlik merkezi kimlik ve erişim yönetimi önerilerini de kullanabilirsiniz.

- [Azure AD istemci kimlik doğrulamasını ayarlama](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster#set-up-azure-active-directory-client-authentication)

- [Azure Güvenlik Merkezi ile kimlik ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Azure Active Directory ile çoklu oturum açma (SSO) kullanın

**Rehberlik**: mümkün olan yerlerde, tek başına bağımsız kimlik bilgilerini hizmet başına yapılandırmak yerine Azure Active Directory (Azure AD) SSO kullanın. Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini kullanın. 

- [Azure AD ile SSO 'yu anlama](../active-directory/manage-apps/what-is-single-sign-on.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin ve güvenlik merkezi kimlik ve erişim yönetimi önerilerini izleyin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: tüm yönetim görevleri için adanmış makineler (ayrıcalıklı erişim Iş Istasyonları) kullanın

**Rehberlik**: Service Fabric kümelerinizi ve ilgili kaynaklarınızı açmak ve yapılandırmak için yapılandırılmış çok faktörlü kimlik doğrulaması Ile ayrıcalıklı erişim iş istasyonu (Paw) kullanın.

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: ortamda şüpheli veya güvenli olmayan bir etkinlik oluştuğunda günlükler ve uyarılar oluşturmak için Azure Active Directory (Azure AD) PRIVILEGED IDENTITY Management (PIM) kullanın. Ayrıca, riskli Kullanıcı davranışında uyarıları ve raporları görüntülemek için Azure AD risk algılamalarını kullanın.

- [Privileged Identity Management dağıtma (PıM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD risk algılamalarını anlama](../active-directory/identity-protection/overview-identity-protection.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: IP adresi aralıklarının veya ülkelerin/bölgelerin yalnızca belirli mantıksal gruplarından erişime izin vermek Için adlandırılmış konumlar kullanın. 

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: Service Fabric kümelerinin yönetim uç noktalarına erişimi güvenli hale getirmek için merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifrelemeyi kullanarak verileri korur. Azure AD Ayrıca, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Service Fabric istemci kimlik doğrulaması için Azure AD kurulumu](service-fabric-cluster-creation-setup-aad.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. ServiceFabric**:

[!INCLUDE [Resource Policy for Microsoft.ServiceFabric 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.servicefabric-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Service Fabric kümenizde Azure Active Directory (Azure AD) kimlik doğrulaması kullanın. Azure AD, eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlar. Ayrıca, grup üyeliklerini etkin bir şekilde yönetmek, kurumsal uygulamalara erişmek ve rol atamaları için Azure kimlik erişimi Incelemelerini kullanın. Kullanıcının erişimi, yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için düzenli olarak incelenebilir.

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-alert-on-account-login-behavior-deviation"></a>3,11: hesap oturum açma davranışı sapmasından uyar

**Rehberlik**: devre dışı bırakılmış hesaplara erişme girişimlerini izlemek için Azure Active Directory (Azure AD) oturum açma ve denetim günlüklerini kullanın; Bu Günlükler, herhangi bir üçüncü taraf SıEM/izleme aracıyla tümleştirilebilir.

Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturarak bu işlemi kolaylaştırarak Azure Log Analytics çalışma alanına denetim günlüklerini ve oturum açma günlüklerini gönderebilirsiniz. Azure Log Analytics çalışma alanında istenen uyarıları yapılandırın.

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapması üzerinde uyarı

**Rehberlik**: Kullanıcı kimlikleriyle ilgili şüpheli eylemleri algılanan otomatik yanıtları yapılandırmak için Azure Active Directory (Azure AD) riskini ve kimlik koruması özelliklerini kullanın. Ayrıca, daha fazla araştırma için verileri Azure Sentinel 'e aktarabilirsiniz.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: hassas bilgileri depolayan veya işleyen Azure kaynaklarını izlemeye yardımcı olmak için Service Fabric kümesi dağıtımlarınızla ilgili kaynaklarda Etiketler kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: geliştirme, test ve üretim için ayrı abonelikler ve/veya yönetim grupları uygulayın. Kaynaklar, sanal ağ veya alt ağ ile ayrılmalıdır, uygun şekilde etiketlenebilir ve bir ağ güvenlik grupları veya Azure Güvenlik Duvarı tarafından güvenli hale getirilir. Hassas verileri depolayan veya işleyen kaynaklar yeterince yalıtılmalıdır. Hassas verileri depolayan veya işleyen sanal makineler için, kullanımda olmadığında devre dışı bırakmak üzere ilke ve yordamlar uygulayın. 

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Güvenlik Duvarı ile uyarı veya uyarı yapılandırma ve reddetme](../firewall/threat-intel.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: ağ engelleyicilerinde, hassas bilgilerin yetkisiz aktarımını izleyen ve bilgi güvenliği uzmanlarına uyarı ederken bu tür aktarımları engelleyen bir otomatik araç dağıtın. 

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar. 

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Rehberlik**: yoldaki tüm hassas bilgileri şifreleyin. Azure kaynaklarınıza bağlanan tüm istemcilerin TLS 1,2 veya üzerini anlaşamadığından emin olun. 

İstemciden düğüme karşılıklı kimlik doğrulaması Service Fabric için, sunucu kimliği için bir X. 509.440 sertifikası ve HTTP iletişiminin TLS şifrelemesi kullanın. Uygulama yapılandırma değerlerinin şifrelenmesi ve şifresinin çözülmesi dahil olmak üzere uygulama güvenliği amaçları için bir kümeye herhangi bir sayıda ek sertifika yüklenebilir ve bu da çoğaltma sırasında düğümler arasında verilerin şifresini çözmelidir.
Uygun olduğunda, bekleyen ve geçişte şifreleme için Güvenlik Merkezi önerilerini izleyin. 

- [Azure ile iletim sırasında şifrelemeyi anlama](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

- [Service Fabric kümesi güvenlik senaryoları](service-fabric-cluster-security.md)

- [TLS yapılandırması için sorun giderme kılavuzu Service Fabric](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: veri tanımlama, sınıflandırma ve kayıp önleme özellikleri, Azure depolama veya işlem kaynakları için henüz kullanılamıyor. Uyumluluk amacıyla gerekliyse üçüncü taraf çözümünü uygulayın.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: erişim denetimini zorlamak için ana bilgisayar tabanlı veri kaybı önleme kullanın

**Kılavuz**: hassas bilgileri depolayan veya işleyen Service Fabric kümeler için, etiketleri kullanarak kümeyi ve ilgili kaynakları hassas olarak işaretleyin. Veri tanımlama, sınıflandırma ve kayıp önleme özellikleri, Azure depolama veya işlem kaynakları için henüz kullanılamıyor. Uyumluluk amacıyla gerekliyse üçüncü taraf çözümünü uygulayın.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Rehberlik**: tüm Azure kaynaklarında bekleyen şifreleme kullanın. Microsoft, Azure 'un şifreleme anahtarlarınızı yönetmesine izin vermesini önerir, ancak bazı örneklerde kendi anahtarlarınızı yönetmeniz için seçenek vardır. 

- [Azure’da bekleyen veri şifrelemesini anlama](../security/fundamentals/encryption-atrest.md)  

- [Müşteri tarafından yönetilen şifreleme anahtarlarını yapılandırma](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Windows 'da Azure Service Fabric küme düğümleri için disk şifrelemeyi etkinleştirme](service-fabric-enable-azure-disk-encryption-windows.md)

- [Linux 'ta Azure Service Fabric küme düğümleri için disk şifrelemeyi etkinleştirme](service-fabric-enable-azure-disk-encryption-linux.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. ServiceFabric**:

[!INCLUDE [Resource Policy for Microsoft.ServiceFabric 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.servicefabric-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Kılavuz**: Azure Izleyici 'Yi Azure etkinlik günlüğü ile birlikte kullanarak, önemli Azure kaynaklarına yapılan değişikliklerin ne zaman gerçekleştiği hakkında uyarılar oluşturun. 

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](/azure/azure-monitor/platform/alerts-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: otomatikleştirilmiş güvenlik açığı tarama araçlarını çalıştırma

**Rehberlik**: hizmetlerinizin sağlamlık ve güvenilirliğini değerlendirmek için küme genelinde hataların benzetimini yapmak üzere Service Fabric hata analizi hizmetini ve Chaos hizmetlerini düzenli olarak çalıştırın.

Azure sanal makinelerinizde ve kapsayıcı görüntülerinde güvenlik açığı değerlendirmeleri gerçekleştirmeye yönelik güvenlik merkezi önerilerini izleyin. 

Ağ cihazlarında ve Web uygulamalarında güvenlik açığı değerlendirmeleri gerçekleştirmek için bir üçüncü taraf çözümü kullanın. Uzak taramalar yaparken, tek bir kalıcı, yönetici hesabı kullanmayın. Tarama hesabı için JıT sağlama yöntemini uygulamayı düşünün. Tarama hesabı için kimlik bilgileri korunmalıdır, izlenir ve yalnızca güvenlik açığı taraması için kullanılmalıdır. 

- [Service Fabric hata analiz hizmetine giriş](service-fabric-testability-overview.md)

- [Service Fabric kümelerinde ı, kontrollü Chaos](service-fabric-controlled-chaos.md)

- [Azure Güvenlik Merkezi güvenlik açığı değerlendirmesi önerilerini uygulama](../security-center/deploy-vulnerability-assessment-vm.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: otomatik işletim sistemi düzeltme eki yönetimi çözümünü dağıtma

**Rehberlik**: Service Fabric kümenizin sanal makine ölçek kümelerinde otomatik işletim sistemi görüntüsü yükseltmelerini etkinleştirin. 

Alternatif olarak, üretime geçmeden önce işletim sistemi düzeltme eklerini test etmek için ölçek kümesinin işletim sistemi görüntüsü yükseltmeleri için el ile tetikleyiciyi kullanın. El ile tetikleme seçeneğinin yerleşik geri alma özelliği sunmadığını unutmayın. Azure Otomasyonu 'ndan Güncelleştirme Yönetimi kullanarak işletim sistemi düzeltme eklerini izleyin. 

- [Service Fabric küme düğümleri için Düzeltme Eki Yönetimi](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

- [Azure sanal makine ölçek kümeleri ile otomatik işletim sistemi görüntüsü yükseltmeleri](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

- [En son ölçek kümesi modeliyle VM 'Leri güncel hale getirme](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

- [Azure Otomasyonu Güncelleştirme Yönetimi Genel Bakış](/azure/automation/update-management/update-mgmt-overview)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: üçüncü taraf yazılım başlıkları için otomatik düzeltme eki yönetimi çözümünü dağıtma

**Kılavuz**: Azure Service Fabric kümenizin sanal makine ölçek kümelerinde otomatik işletim sistemi görüntüsü yükseltmelerini etkinleştirin. Düzeltme Eki düzenleme uygulaması (POA), Azure dışında barındırılan Service Fabric kümelerine yönelik alternatif bir çözümdür. POA, Azure kümeleriyle birlikte kullanılabilir ve bazı ek barındırma yüklerine sahip olabilir.

- [Service Fabric küme düğümleri için Düzeltme Eki Yönetimi](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

- [Azure sanal makine ölçek kümeleri ile otomatik işletim sistemi görüntüsü yükseltmeleri](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

- [Service Fabric kümeleri için işletim sistemi düzeltme eki uygulama zamanlamasını yapılandırma](service-fabric-patch-orchestration-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: geri dönüş güvenlik açığı taramalarını karşılaştırın

**Rehberlik**: tarama sonuçlarını tutarlı aralıklarla dışarı aktarın ve güvenlik açıklarının düzeltildiğini doğrulamak için sonuçları karşılaştırın. Güvenlik Merkezi tarafından önerilen güvenlik açığı yönetimi önerilerini kullanırken, geçmiş tarama verilerini görüntülemek için seçili çözümün portalına de Pivot ekleyebilirsiniz.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek için risk derecelendirme işlemi kullanın

**Rehberlik**: ortak bir risk Puanlama programı (örneğin, ortak güvenlik açığı Puanlama sistemi) veya üçüncü taraf tarama aracınız tarafından sunulan varsayılan risk derecelendirmelerini kullanın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: abonelikleriniz dahilinde (işlem, depolama, ağ, bağlantı noktaları ve protokoller vb.) tüm kaynakları sorgulamak/öğrenmek Için Azure Kaynak grafiğini kullanın. Kiracınızda uygun (okuma) izinlere sahip olun ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerini numaralandırın.

Klasik Azure kaynakları kaynak Graph aracılığıyla bulunabilse de, ileri doğru Azure Resource Manager kaynak oluşturmanız ve kullanılması kesinlikle önerilir.

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

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

**Rehberlik**: varlıkları düzenlemek ve izlemek için uygun yerlerde etiketleme, yönetim grupları ve ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynaklarının envanterini tanımlayın ve saklayın

**Rehberlik**: işlem kaynakları Için onaylanan Azure kaynakları ve onaylanan yazılımlar tanımlayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerine oluşturulabilecek kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:

İzin verilmeyen kaynak türleri

İzin verilen kaynak türleri

Aboneliklerinizde kaynakları sorgulamak/öğrenmek için Azure Kaynak Grafiği ' ni kullanın. Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: işlem kaynakları içindeki onaylanmamış yazılım uygulamaları için izleyici

**Rehberlik**: onaylanmamış yazılım uygulamalarına yönelik küme düğümlerini izlemek için bir üçüncü taraf çözümü uygulayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Rehberlik**: aboneliklerinizde Service Fabric kümeleri de dahil olmak üzere tüm kaynakları (işlem, depolama, ağ, bağlantı noktaları ve protokoller vb.) sorgulamak/öğrenmek Için Azure Kaynak grafiğini kullanın. Keşfettiğiniz onaylanmamış tüm Azure kaynaklarını kaldırın. Service Fabric küme düğümleri için, onaylanmamış yazılımları kaldırmak veya uyarmak üzere bir üçüncü taraf çözümü uygulayın.

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="68-use-only-approved-applications"></a>6,8: yalnızca onaylanan uygulamaları kullan

**Rehberlik**: Service Fabric küme düğümleri için, yetkisiz yazılımın yürütülmesini engelleyen bir üçüncü taraf çözümü uygulayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: ortamınızda sağlayabileceğiniz hizmetleri kısıtlamak Için Azure ilkesini kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: onaylanan yazılım başlıkları envanterini koruyun

**Kılavuz**: Azure Service Fabric küme düğümleri için, yetkisiz dosya türlerinin yürütülmesini engelleyen bir üçüncü taraf çözümü uygulayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcıların Azure kaynakları yöneticisiyle etkileşime geçmesini sınırlamak Için Azure koşullu erişimi kullanın. 

- [Azure Kaynak Yöneticisi 'ne erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: kullanıcıların işlem kaynakları içinde betikleri yürütme yeteneğini sınırlayın

**Rehberlik**: kullanıcıların Azure işlem kaynakları içinde betikleri yürütme yeteneğini sınırlamak için işletim sistemine özgü yapılandırma veya üçüncü taraf kaynakları kullanın.

- [Örneğin, Windows ortamlarında PowerShell betiği yürütmeyi denetleme](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: yüksek riskli uygulamaları fiziksel olarak veya mantıksal olarak ayırt edin

**Rehberlik**: iş işlemleri için gerekli olan, ancak kuruluşa daha fazla risk doğurabilecek yazılımlar, kendi sanal makinesi ve/veya sanal ağı içinde yalıtılmalıdır ve bir Azure Güvenlik Duvarı veya ağ güvenlik grubuyla yeterince güvenli hale getirilir. 

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md) 

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Rehberlik**: Service Fabric kümenizin ağ yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak Için "Microsoft. servicefabric" ad alanındaki Azure ilke diğer adlarını kullanın.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: güvenli işletim sistemi yapılandırması oluşturma

**Rehberlik**: Service Fabric Işletim sistemi görüntüleri Microsoft tarafından yönetilir ve sürdürülür. Küme düğümlerinizin işletim sistemine yönelik güvenli yapılandırmaların uygulamamasından sorumlu müşteri.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Kılavuz**: Azure Service Fabric kümeleriniz ve ilgili kaynaklarınız için güvenli ayarları zorlamak üzere Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] etkileri kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: güvenli işletim sistemi yapılandırmalarının bakımını yapma

**Rehberlik**: Microsoft tarafından yönetilen ve korunan küme Işletim sistemi görüntülerini Service Fabric. Müşteri, işletim sistemi düzeyinde durum yapılandırması uygulamaktan sorumludur.

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilke tanımları kullanıyorsanız, kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure devops veya Azure Repos kullanın.

- [Azure DevOps 'da kod depolama](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure Repos belgeleri](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: özel işletim sistemi görüntülerini güvenli bir şekilde depolayın

**Kılavuz**: özel görüntüler kullanıyorsanız, görüntülere yalnızca yetkili kullanıcıların erişebildiğinden emin olmak için Azure rol tabanlı erişim denetimi 'Ni (Azure RBAC) kullanın. Kapsayıcı görüntüleri için Azure Container Registry depolayın ve yalnızca yetkili kullanıcıların görüntülere erişebildiğinden emin olmak için Azure RBAC 'den yararlanın. 

- [Azure RBAC 'yi anlama](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Container Registry için Azure RBAC 'nı anlama](../container-registry/container-registry-roles.md) 

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: sistem yapılandırmalarına uyarı vermek, denetlemek ve zorlamak için özel ilkeler oluşturmak üzere "Microsoft. servicefabric" ad alanındaki Azure ilke diğer adlarını kullanın. Ayrıca, ilke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: Service Fabric kümenizin yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak Için "Microsoft. servicefabric" ad alanındaki Azure ilke diğer adlarını kullanın.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: işletim sistemleri için otomatik yapılandırma izlemeyi Uygula

**Rehberlik**: kapsayıcılar için işletim sistemi ve Docker ayarları için temel taramalar gerçekleştirmek üzere güvenlik merkezi 'ni kullanın. 

- [Azure Güvenlik Merkezi'ndeki kapsayıcı önerilerini anlama](../security-center/container-security.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: bulut uygulamalarınız için gizli yönetimi basitleştirmek ve güvenli hale getirmek için Azure Key Vault ile birlikte yönetilen hizmet kimliği kullanın. 

- [Service Fabric ile Azure için Yönetilen kimlikler kullanma](concepts-managed-identity.md)

- [Yeni bir Service Fabric kümesi için yönetilen kimlik desteğini yapılandırma](configure-new-azure-service-fabric-enable-managed-identity.md)

- [Yönetilen kimliği bir Service Fabric uygulamayla kullanma](how-to-managed-identity-service-fabric-app-code.md)

- [Service Fabric uygulamalar için KeyVaultReference desteği](service-fabric-keyvault-references.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Rehberlik**: Yönetilen kimlikler, Azure tarafından dağıtılan Service Fabric kümelerinde ve Azure kaynakları olarak dağıtılan uygulamalarda kullanılabilir. Yönetilen kimlikler, kodunuzda kimlik bilgileri olmadan Key Vault dahil olmak üzere Azure Active Directory (Azure AD) kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmanıza olanak sağlar.

- [Service Fabric ile Azure için Yönetilen kimlikler kullanma](concepts-managed-identity.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Kılavuz**: Azure Service Fabric dağıtımınız ile ilgili herhangi bir kod kullanıyorsanız, kod içindeki kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayabilirsiniz. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar.

Service Fabric küme sertifikalarını otomatik olarak döndürmek için Azure Key Vault kullanın.

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Service Fabric kümelerinde sertifika yönetimi](https://docs.microsoft.com/azure/service-fabric/cluster-security-certificate-management#certificate-rotation)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: merkezi olarak yönetilen kötü amaçlı yazılımdan koruma yazılımı kullanma

**Rehberlik**: Windows Defender virüsten koruma, varsayılan olarak windows Server 2016 ' ye yüklenmiştir. Kullanıcı arabirimi bazı SKU 'Larda varsayılan olarak yüklenir, ancak gerekli değildir.

Windows Defender kullanmıyorsanız yapılandırma kuralları için kötü amaçlı yazılımdan koruma belgelerinize bakın. Windows Defender, Linux üzerinde desteklenmez.

- [Windows Server 2016 ' de Windows Defender virüsten koruma 'yı anlama](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli Otomatik yedeklemeli UPS sağlayın

**Rehberlik**: Service Fabric ' deki yedekleme ve geri yükleme hizmeti, durum bilgisi olan hizmetlerde depolanan bilgilerin kolay ve otomatik yedeklemesini mümkün bir şekilde sunar. Uygulama verilerini düzenli aralıklarla yedeklemek, veri kaybına ve hizmetin kullanılamamasına karşı koruma için temel bir uygulamadır. Service Fabric, ek kod yazmak zorunda kalmadan durum bilgisi olan Reliable Services (aktör hizmetleri dahil) düzenli olarak yedeklenmesini yapılandırmanıza olanak tanıyan, isteğe bağlı bir yedekleme ve geri yükleme hizmeti sağlar. Ayrıca, daha önce alınan yedeklemelerin geri yüklenmesini de kolaylaştırır.

- [Azure Service Fabric kümesinde düzenli olarak yedekleme ve geri yükleme](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: Service Fabric kümenizdeki yedekleme geri yükleme hizmetini etkinleştirin ve durum bilgisi olan hizmetleri düzenli aralıklarla ve isteğe bağlı olarak yedeklemek için yedekleme ilkeleri oluşturun. Azure Key Vault içinde müşteri tarafından yönetilen anahtarları yedekleyin.

- [Azure Service Fabric kümesinde düzenli olarak yedekleme ve geri yükleme](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

- [Azure Service Fabric düzenli aralıklarla yedekleme yapılandırmasını anlama](service-fabric-backuprestoreservice-configure-periodic-backup.md)

- [Azure 'da Anahtar Kasası anahtarlarını yedekleme](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: yedekleme yapılandırma bilgilerini ve kullanılabilir yedeklemeleri düzenli olarak inceleyerek yedekleme geri yükleme hizmetinden geri yükleme gerçekleştirebilme olanağı sağlayın. Yedeklenen müşteri tarafından yönetilen anahtarların test geri yüklemesi.

- [Azure Service Fabric düzenli aralıklarla yedekleme yapılandırmasını anlama](service-fabric-backuprestoreservice-configure-periodic-backup.md)

- [Azure Service Fabric yedeklemeyi geri yükleme](service-fabric-backup-restore-service-trigger-restore.md)

- [Azure 'da Anahtar Kasası anahtarlarını geri yükleme](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: Service Fabric yedekleme geri yükleme hizmetinden yedeklemeler, aboneliğinizde bir Azure depolama hesabı kullanır. Azure depolama, bekleyen bir depolama hesabındaki tüm verileri şifreler. Varsayılan olarak, veriler Microsoft tarafından yönetilen anahtarlarla şifrelenir. Şifreleme anahtarları üzerinde ek denetim için, depolama verilerinin şifrelenmesi için müşteri tarafından yönetilen anahtarlar sağlayabilirsiniz.

Müşteri tarafından yönetilen anahtarlar kullanıyorsanız, Key Vault içindeki Soft-Delete yanlışlıkla veya kötü amaçlı silmeye karşı anahtarları korumak için etkinleştirildiğinden emin olun.

- [Azure Depolama bekleyen verileri şifreleme](../storage/common/storage-service-encryption.md)

- [Key Vault Soft-Delete etkinleştirme](../storage/blobs/soft-delete-blob-overview.md)

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

**Rehberlik**: Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin, uyarıyı vermek için kullanılan bulma veya ölçümde ne kadar uygun olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin güven düzeyini temel alır.

Ayrıca, abonelikleri kullanarak abonelikleri işaretleyin ve Azure kaynaklarını tanımlamak ve kategorilere ayırmak için özellikle de hassas verileri işleyen bir adlandırma sistemi oluşturun.  Olayın gerçekleştiği Azure kaynakları ve ortamının önem derecesine bağlı olarak, uyarıların düzeltilmesine öncelik vermek sizin sorumluluğunuzdadır. 

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md) 

- [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve açıkları belirleyip planı gerektiği şekilde gözden geçirin. 

- [BT planları ve özellikleri için test, eğitim ve alıştırma programları için NıST Kılavuzu](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: Microsoft Güvenlik Yanıt MERKEZI (MSRC), verilerinize izinsiz veya yetkisiz bir taraf tarafından erişildiğini belirlerse, Microsoft tarafından sizinle iletişim kurmak için güvenlik olayı iletişim bilgileri kullanılacaktır. Sorunların çözümlendiğinden emin olmak için gerçesonra olayları gözden geçirin. 

- [Azure Güvenlik Merkezi güvenlik ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: güvenlik uyarılarını olay yanıt sisteminizle birleştirme

**Rehberlik**: sürekli dışa aktarma özelliğini kullanarak güvenlik merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Sürekli dışa aktarma, uyarıları ve önerileri el ile veya devam eden sürekli bir biçimde dışa aktarmanız sağlar. Uyarıları Sentinel 'e akışa almak için Güvenlik Merkezi veri bağlayıcısını kullanabilirsiniz.

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
