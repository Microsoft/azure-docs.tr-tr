---
title: Container Instances için Azure Güvenlik temeli
description: Container Instances güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: container-instances
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8790e05edbaeb40debd997ea9b35d31b25947761
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598797"
---
# <a name="azure-security-baseline-for-container-instances"></a>Container Instances için Azure Güvenlik temeli

Bu güvenlik temeli, Container Instances için [Azure Güvenlik kıyaslama sürümü 1.0'dan](../security/benchmarks/overview-v1.md) rehberlik uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Container Instances için geçerli olan ilgili kılavuza göre gruplandırılır. Container Instances için geçerli olmayan **denetimler** veya Microsoft 'un hangi sorumluluğun çıkarılmadığı.

Container Instances Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Container Instances güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: kapsayıcı gruplarınızı Azure sanal ağıyla Azure Container Instances tümleştirme.  Azure sanal ağları, internet 'ten yönlendirilebilir olmayan bir ağa kapsayıcı grupları gibi Azure kaynaklarınızın birçoğunu yerleştirmenizi sağlar.

Azure Güvenlik Duvarı 'nı kullanarak Azure Container Instances Temsilcili bir alt ağdan giden ağ erişimini denetleyin. 

- [Kapsayıcı örneklerini Azure sanal ağına dağıtma](/azure/container-instances/container-instances-vnet)

- [Azure Güvenlik duvarını dağıtma ve yapılandırma](../firewall/tutorial-firewall-deploy-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: sanal ağların, alt ağların ve ağ arabirimlerinin yapılandırma ve trafiğini izleme ve günlüğe kaydetme

**Rehberlik**: Azure Güvenlik Merkezi 'ni kullanarak ağ kaynaklarınızın güvenliğini sağlamaya yardımcı olmak için ağ koruma önerilerini izleyin. Trafik denetimi için NSG akış günlüklerini etkinleştirin ve günlükleri bir depolama hesabına gönderin. Ayrıca, NSG akış günlüklerini bir Log Analytics çalışma alanına gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır. 

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md) 

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="13-protect-critical-web-applications"></a>1,3: kritik Web uygulamalarını koruma

**Rehberlik**: uygulanamaz. Kıyaslama, Web uygulamalarını barındıran Azure App Service veya işlem kaynaklarına yöneliktir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: DDoS saldırılarına karşı korumalar için sanal ağlarınızda DDoS standart korumasını etkinleştirin. Bilinen kötü amaçlı veya kullanılmayan Internet IP adresleriyle iletişimleri reddetmek için Azure Güvenlik Merkezi tümleşik tehdit zekasını kullanın.  Tehdit zekası etkinleştirilmiş ve kötü amaçlı ağ trafiği için "uyarı ve reddetme" olarak yapılandırılmış bir kuruluşun ağ sınırlarının her birinde Azure Güvenlik Duvarı 'Nı dağıtın.

NSG 'leri, sınırlı bir süre için son bitiş noktaları onaylı IP adresleriyle sınırlamak üzere Azure Güvenlik Merkezi 'ni tam zamanında ağ erişimi ile kullanabilirsiniz. Ayrıca, bağlantı noktalarını ve kaynak IP 'Leri gerçek trafik ve tehdit zekası temelinde sınırlayan NSG yapılandırmalarının kullanılması için Azure Güvenlik Merkezi Uyarlamalı ağ sağlamlaştırma kullanın.

- [DDoS korumasını yapılandırma](/azure/virtual-network/manage-ddos-protection)

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Güvenlik Merkezi tümleşik tehdit zekasını anlama](../security-center/azure-defender.md)

- [Azure Güvenlik Merkezi Uyarlamalı ağ sağlamlaştırma 'yi anlama](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Güvenlik Merkezi tam zamanında ağ Access Control](../security-center/security-center-just-in-time.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Kılavuz**: Azure Container registry gibi Azure Container Instances bulut tabanlı özel bir kayıt defteri kullanıyorsanız, Azure Container Registry 'nizi korumak için kullanılan alt ağa bağlı olan NSG için ağ güvenlik grubu (NSG) akış günlüklerini etkinleştirebilirsiniz. Flow kayıtları oluşturmak için NSG akış günlüklerini bir Azure depolama hesabına kaydedebilirsiniz. Anormal etkinlikleri araştırmak için gerekliyse, Azure ağ Izleyicisi paket yakalamayı etkinleştirin.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Ağ İzleyicisini etkinleştirme](../network-watcher/network-watcher-create.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Kılavuz**: Azure Marketi 'nden, yük İnceleme ÖZELLIKLERI ile KIMLIKLER/IP işlevlerini destekleyen bir teklif seçin. Yük incelemesini temel alan yetkisiz giriş algılama ve/veya önleme bir gereksinim değilse, tehdit zekasıyla Azure Güvenlik Duvarı kullanılabilir. Azure Güvenlik Duvarı tehdit zekası tabanlı filtreleme, bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen ve giden trafiği uyarabilir ve reddedebilir. IP adresleri ve etki alanları, Microsoft Tehdit Analizi akışından alınır.

Kötü amaçlı trafiği algılamak ve/veya reddetmek için kuruluşunuzun ağ sınırlarının her birinde tercih ettiğiniz güvenlik duvarı çözümünü dağıtın.

- [Azure Market](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Güvenlik Duvarı ile uyarıları yapılandırma](../firewall/threat-intel.md)

- [Sanal bir ağda dağıtma-Azure Container Instances](container-instances-vnet.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Web uygulamalarına trafiği yönetme

**Rehberlik**: uygulanamaz. Kıyaslama, Azure App Service veya işlem kaynaklarında çalışan Web uygulamalarına yöneliktir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Kılavuz**: Azure Container registry gibi Azure Container Instances ile bulut tabanlı özel bir kayıt defteri kullanıyorsanız, kapsayıcı Kayıt defterinize erişmesi gereken kaynaklar Için, ağ güvenlik gruplarında veya Azure Güvenlik duvarında ağ erişim denetimleri tanımlamak üzere Azure Container Registry hizmeti için sanal ağ hizmeti etiketleri kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. "AzureContainerRegistry" hizmet etiketi adını bir kuralın uygun kaynak veya hedef alanında belirterek, ilgili hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

- [Hizmet etiketine göre erişime izin ver](https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Kılavuz**: Azure Container Instances Azure Container Registry kullanırken, Azure Container Registry 'niz ile ilişkili ağ kaynakları için standart güvenlik yapılandırması tanımlamanızı ve uygulamanızı öneririz.

Kapsayıcı kayıt defterlerinizin ağ yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için **Microsoft. ContainerRegistry** ve **Microsoft. Network** ad alanlarında Azure ilke diğer adlarını kullanın.

Azure şemaları, Azure Resource Manager şablonları, Azure RBAC denetimleri ve ilke tanımlarını tek bir şema tanımına paketleyerek büyük ölçekli Azure dağıtımlarını basitleştirmek için kullanabilirsiniz. Şema 'i yeni aboneliklere kolayca uygulayın ve sürüm oluşturma aracılığıyla denetim ve yönetime ince ayar yapın.

- [Azure Ilkesi kullanarak Azure Container Registry 'nin uyumluluğunu denetleme](../container-registry/container-registry-azure-policy.md)

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: müşteri, tek bir şema tanımında Azure Resource Manager şablonları, Azure RBAC denetimleri ve ilkeleri gibi temel ortam yapıtlarını paketleyerek büyük ölçekli Azure dağıtımlarını basitleştirmek için Azure şemaları kullanabilir. Şema 'i yeni aboneliklere kolayca uygulayın ve sürüm oluşturma aracılığıyla denetim ve yönetime ince ayar yapın.

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Kılavuz**: Azure etkinlik günlüğü 'nü kullanarak ağ kaynak yapılandırmasını izleyin ve kapsayıcı kayıt defterlerinden ilgili ağ kaynaklarına yönelik değişiklikleri tespit edin. Kritik ağ kaynaklarında yapılan değişiklikler yürürlüğe girdiğinde tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](/azure/azure-monitor/platform/alerts-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: bir Azure Container Instance tarafından oluşturulan güvenlik verilerini toplamak Için Azure izleyici aracılığıyla günlükleri alma. Azure Izleyici 'de analiz sorgulamak ve gerçekleştirmek için bir Log Analytics çalışma alanı kullanın ve uzun süreli/arşiv depolama için Azure depolama hesaplarını kullanın.

- [Azure Izleyici günlükleri ile kapsayıcı grubu ve örnek günlüğü](container-instances-log-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Kılavuz**: Azure izleyici, Kullanıcı odaklı olaylar için kaynak günlüklerini (eski adıyla tanılama günlükleri olarak adlandırılır) toplar. Kapsayıcı kimlik doğrulama olaylarını denetlemek için bu verileri toplayın ve kullanın ve kapsayıcı grubunuzdaki güvenlik sorunlarını tanılamanıza olanak tanımak için çekme ve anında iletme olayları gibi yapıtlar üzerinde tüm etkinlik izini sağlayın.

- [Azure Izleyici günlükleri ile kapsayıcı grubu ve örnek günlüğü](container-instances-log-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure izleyici 'de, Log Analytics çalışma alanı saklama dönemini kuruluşunuzun uyumluluk düzenlemelerine göre ayarlayın. Uzun süreli/arşiv depolama için Azure depolama hesaplarını kullanın.

- [Log Analytics çalışma alanları için günlük saklama parametrelerini ayarlama](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Rehberlik**: anormal davranış için Azure Container Instances günlüklerini çözümleyin ve izleyin ve sonuçları düzenli olarak gözden geçirin. Günlükleri gözden geçirmek ve günlük verilerinde sorgular gerçekleştirmek için Azure Izleyici Log Analytics çalışma alanını kullanın.

- [Log Analytics çalışma alanını anlayın](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Azure Izleyici 'de özel sorgular gerçekleştirme](/azure/azure-monitor/log-query/get-started-queries)

- [Günlük özellikli kapsayıcı grubu ve sorgu günlükleri oluşturma](container-instances-log-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Kılavuz**: Azure Container registry gibi Azure Container Instances bulut tabanlı özel bir kayıt defteri kullanıyorsanız Azure Container Registry ile ilgili güvenlik günlüklerine ve olaylara yönelik anormal etkinlikleri izlemek ve uyarmak için Azure Log Analytics çalışma alanını kullanın.

- [Tanılama değerlendirmesi ve denetimi için Azure Container Registry günlükleri](../container-registry/container-registry-diagnostics-audit-logs.md)

- [Log Analytics günlük verilerinde uyarı alma](/azure/azure-monitor/learn/tutorial-response)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="28-centralize-anti-malware-logging"></a>2,8: kötü amaçlı yazılımdan koruma 'yı merkezileştirme

**Rehberlik**: uygulanamaz. Azure Container Instances ile Azure Container Registry gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız, Azure Container Registry, kötü amaçlı yazılımdan koruma ile ilgili günlükleri işlemez veya üretmez.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="29-enable-dns-query-logging"></a>2,9: DNS sorgu günlüğünü etkinleştir

**Rehberlik**: uygulanamaz. Azure Container Instances ile Azure Container Registry gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız Azure Container Registry bir uç noktasıdır ve iletişim başlatmaz ve hizmet DNS 'i sorgulamaz.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: Azure Active Directory (Azure AD), açıkça atanması ve sorgulanabilir olması gereken yerleşik roller içerir. Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü kullanın.

Azure Container Instances ile Azure Container Registry gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız, her Azure Container Registry için yerleşik yönetici hesabının etkin veya devre dışı olduğunu izleyin. Hesabı kullanımda olmadığında devre dışı bırakın.

- [Azure AD 'de PowerShell ile dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Yönetici hesabı Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Rehberlik**: Azure Active Directory (Azure AD) varsayılan parola kavramına sahip değil. Parola gerektiren diğer Azure kaynakları, karmaşıklık gereksinimleriyle ve hizmete bağlı olarak, en düşük parola uzunluğuna sahip bir parola oluşturulmasını zorlar. Varsayılan parolaları kullanbilen üçüncü taraf uygulamalardan ve Market hizmetlerinden siz sorumlusunuz.

Azure Container Instances ile Azure Container Registry gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız, bir Azure Container Registry 'nin varsayılan yönetici hesabı etkinse, karmaşık parolalar otomatik olarak oluşturulur ve döndürülmelidir. Hesabı kullanımda olmadığında devre dışı bırakın.

- [Yönetici hesabı Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturun. Yönetim hesaplarının sayısını izlemek için Azure Güvenlik Merkezi kimlik ve erişim yönetimi 'ni kullanın.

Azure Container Instances ile Azure Container Registry gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız, kapsayıcı kayıt defterinin yerleşik yönetici hesabını etkinleştirmek için yordamlar oluşturun. Hesabı kullanımda olmadığında devre dışı bırakın.

- [Azure Güvenlik Merkezi kimlik ve erişimini anlama](../security-center/security-center-identity-access.md)

- [Yönetici hesabı Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory çoklu oturum açma kullan (SSO)

**Rehberlik**: mümkün olan yerlerde, tek başına bağımsız kimlik bilgilerini hizmet başına yapılandırmak yerine Azure Active Directory (Azure AD) SSO kullanın. Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini kullanın.

Azure Container Instances ile Azure Container Registry gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız, kapsayıcı kayıt defterine bireysel erişim için Azure AD ile bağımsız olarak tümleşik oturum açma kullanın.

- [Azure AD ile SSO 'yu anlama](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Bir kapsayıcı kayıt defterinde tek oturum açma](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin ve Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini izleyin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: tüm yönetim görevleri için adanmış makineler (ayrıcalıklı erişim Iş Istasyonları) kullanın

**Kılavuz**: Azure kaynaklarını açmak ve yapılandırmak için yapılandırılmış çok faktörlü kimlik doğrulaması Ile Paws (ayrıcalıklı erişim iş istasyonları) kullanın.

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

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: IP adresi aralıklarının veya ülkelerin/bölgelerin yalnızca belirli mantıksal gruplarından erişime izin vermek Için adlandırılmış konumlar kullanın.

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifrelemeyi kullanarak verileri korur. Azure AD Ayrıca, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlar. Ayrıca, grup üyeliklerini etkin bir şekilde yönetmek, kurumsal uygulamalara erişmek ve rol atamaları için Azure kimlik erişimi Incelemelerini kullanın. Yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için, Kullanıcı erişimi düzenli olarak incelenebilir.

- [Azure AD raporlamayı anlama](/azure/active-directory/reports-monitoring/)

- [Azure kimlik erişimi incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: herhangi bir güvenlik bilgisi ve olay yönetimi (SIEM)/Monitoring aracıyla tümleştirmenize olanak tanıyan Azure Active Directory (Azure AD) oturum açma etkinliğine, denetim ve risk olay günlüğü kaynaklarına erişiminiz vardır.

Bu işlemi Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturarak ve denetim günlüklerini göndererek ve bir Log Analytics çalışma alanında oturum açmak için kullanabilirsiniz. İstenen uyarıları Log Analytics çalışma alanı içinde yapılandırabilirsiniz.

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapması üzerinde uyarı

**Rehberlik**: Kullanıcı kimlikleriyle ilgili şüpheli eylemleri algılanan otomatik yanıtları yapılandırmak için Azure Active Directory (Azure AD) riskini ve kimlik koruması özelliklerini kullanın.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: destek senaryoları sırasında Microsoft 'un ilgili müşteri verilerine erişimini sağlama

**Rehberlik**: kullanılamıyor; Müşteri Kasası Azure Container Instances için şu anda desteklenmiyor.

- [Desteklenen Müşteri Kasası hizmetleri listesi](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: hassas bilgileri depolayan veya işleyen Azure Container Registry 'leri izlemeye yardımcı olması için kaynak etiketlerini kullanın.

Gizli bilgileri depolayan veya işleyen görüntüleri izlemeye yardımcı olmak için bir kayıt defterindeki bir kayıt defteri ve sürüm kapsayıcısı görüntülerini veya diğer yapıtları ve görüntüleri veya depoları kilitleyin.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Kapsayıcı görüntülerini etiketleme ve sürüm oluşturma önerileri](../container-registry/container-registry-image-tag-version.md)

- [Azure Container Registry 'de kapsayıcı görüntüsünü kilitleme](../container-registry/container-registry-image-lock.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: geliştirme, test ve üretim için ayrı kapsayıcı kayıt defterleri, abonelikler ve/veya yönetim grupları uygulayın. Hassas verileri depolayan veya işleyen kaynaklar yeterince yalıtılmalıdır.

Kaynaklar, sanal ağ veya alt ağ ile ayrılmalıdır, uygun şekilde etiketlenebilir ve bir ağ güvenlik grubu (NSG) veya Azure güvenlik duvarıyla korunmuş olmalıdır.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Azure sanal ağını veya güvenlik duvarı kurallarını kullanarak bir Azure Container Registry 'ye erişimi kısıtlama](../container-registry/container-registry-vnet.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Güvenlik Duvarı ile uyarı veya uyarı yapılandırma ve reddetme](../firewall/threat-intel.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: ağ engelleyicilerinde, hassas bilgilerin yetkisiz aktarımını izleyen ve bilgi güvenliği uzmanlarına uyarı ederken bu tür aktarımları engelleyen bir otomatik araç dağıtın.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri verilerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Rehberlik**: Azure Container Registry bağlanan istemcilerin TLS 1,2 veya daha büyük bir bağlantı kurabildiğinden emin olun. Microsoft Azure kaynaklar varsayılan olarak TLS 1,2 ' a anlaş.

Azure Güvenlik Merkezi önerilerini, varsa, bekleyen ve geçişte şifreleme için kullanın.

- [Azure ile iletim sırasında şifrelemeyi anlama](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Kılavuz**: Azure Container registry gibi Azure Container Instances, veri tanımlama, sınıflandırma ve kayıp önleme özellikleri gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız, Azure Container Registry için henüz kullanılamaz. Uyumluluk amacıyla gerekliyse üçüncü taraf çözümünü uygulayın.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri verilerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Dağıtım verilerini Azure Container Instances ile şifreleyin](container-instances-encrypt-data.md)

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: kaynaklara erişimi denetlemek için rol tabanlı erişim denetimi kullanma

**Rehberlik**: Azure Container Instances ile Azure Container Registry gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız Azure Container Registry 'deki veri ve kaynaklara erişimi yönetmek için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanın.

- [Azure 'da Azure RBAC yapılandırma](../role-based-access-control/role-assignments-portal.md)

- [Azure Container Registry roller ve izinler](../container-registry/container-registry-roles.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: erişim denetimini zorlamak için ana bilgisayar tabanlı veri kaybı önleme kullanın

**Rehberlik**: işlem kaynaklarında uyumluluk için gerekliyse, verileri bir sistemden kopyalandıklarında bile verilere erişim denetimlerine zorlamak için otomatik ana bilgisayar tabanlı veri kaybı önleme çözümü gibi bir üçüncü taraf aracı uygulayın.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri verilerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Rehberlik**: tüm Azure kaynaklarında bekleyen şifreleme kullanın. Azure Container Instances ile Azure Container Registry gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız, varsayılan olarak bir Azure Container Registry 'deki tüm veriler Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir.

- [Azure’da bekleyen veri şifrelemesini anlama](../security/fundamentals/encryption-atrest.md)

- [Azure Container Registry 'de müşteri tarafından yönetilen anahtarlar](https://aka.ms/acr/cmk)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Rehberlik**: Log Analytics çalışma alanları, yalnızca Azure kaynaklarından değil, aynı zamanda şirket içi kaynakları ve diğer bulutlardaki kaynakları depolamak ve sorgulamak için merkezi bir konum sağlar. Azure Container Instances, Azure Izleyici günlüklerine Günlükler ve olay verileri göndermek için yerleşik destek içerir.

- [Azure Izleyici günlükleri ile kapsayıcı grubu ve örnek günlüğü](container-instances-log-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: otomatikleştirilmiş güvenlik açığı tarama araçlarını çalıştırma

**Kılavuz**: özel bir kayıt defterinde kapsayıcı görüntülerini taramak ve olası güvenlik açıklarını belirlemek için çözümlerle yararlanın. Farklı çözümlerin sağladığı tehdit algılama derinliğini anlamak önemlidir. Kapsayıcı görüntülerinde güvenlik açığı değerlendirmelerinde Azure Güvenlik Merkezi 'nin önerilerini izleyin. Azure Market 'ten, görüntü güvenlik açığı değerlendirmelerini gerçekleştirmek için isteğe bağlı olarak üçüncü taraf çözümler dağıtın.

- [Azure Container Instances için kapsayıcı izleme ve tarama güvenlik önerileri](container-instances-image-security.md)

- [Güvenlik Merkezi ile Azure Container Registry tümleştirme](/azure/security-center/azure-container-registry-integration)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: otomatik işletim sistemi düzeltme eki yönetimi çözümünü dağıtma

**Rehberlik**: Microsoft, çalışan kapsayıcıları destekleyen temel sistemler üzerinde düzeltme eki yönetimi gerçekleştirir.

İşletim sisteminden ve diğer düzeltme eklerinden temel görüntülere yönelik güncelleştirmeler algılandığında kapsayıcı görüntüsü güncelleştirmelerini otomatikleştirin.

- [Azure Container Registry görevler için temel görüntü güncelleştirmeleri hakkında](../container-registry/container-registry-tasks-base-images.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: üçüncü taraf yazılım başlıkları için otomatik düzeltme eki yönetimi çözümünü dağıtma

**Rehberlik**: uygulama görüntülerini yama yapmak için üçüncü taraf bir çözüm kullanabilirsiniz. Ayrıca, Azure Container Instances ile Azure Container Registry gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız, temel görüntülerdeki güvenlik düzeltme eklerine veya diğer güncelleştirmelere dayalı olarak bir kapsayıcı kayıt defterindeki uygulama görüntülerinin güncelleştirmelerini otomatikleştirmek için Azure Container Registry görevlerini çalıştırabilirsiniz.

- [ACR görevleri için temel görüntü güncelleştirmeleri hakkında](../container-registry/container-registry-tasks-base-images.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: geri dönüş güvenlik açığı taramalarını karşılaştırın

**Rehberlik**: Azure Container Instances ile Azure Container Registry gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız, güvenlik açıkları için kapsayıcı görüntülerinin düzenli olarak taranmasını sağlamak üzere Azure Güvenlik Merkezi ile Azure Container Registry 'YI (ACR) tümleştirin. Düzenli olarak görüntü güvenlik açığı taramaları gerçekleştirmek için Azure Marketi 'nden farklı üçüncü taraf çözümleri dağıtın.

- [Güvenlik Merkezi ile Azure Container Registry tümleştirme](../security-center/defender-for-container-registries-introduction.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek için risk derecelendirme işlemi kullanın

**Rehberlik**: Azure Container Instances ile Azure Container Registry gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız, güvenlik açıkları için kapsayıcı görüntülerinin düzenli olarak taranmasını sağlamak ve riskleri sınıflandırmak Için Azure Güvenlik merkezi ile Azure Container Registry (ACR) tümleştirin. İsteğe bağlı olarak Azure Marketi 'nden üçüncü taraf çözümler dağıtarak düzenli görüntü güvenlik açığı taramaları ve risk sınıflandırması gerçekleştirin.

- [Güvenlik Merkezi ile Azure Container Registry tümleştirme](../security-center/defender-for-container-registries-introduction.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: abonelikleriniz dahilinde (işlem, depolama, ağ, bağlantı noktaları ve protokoller vb.) tüm kaynakları sorgulamak/öğrenmek Için Azure Kaynak grafiğini kullanın. Kiracınızda uygun (okuma) izinlere sahip olun ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerini numaralandırın.

Klasik Azure kaynakları kaynak Graph aracılığıyla bulunabilse de, ileri doğru Azure Resource Manager kaynak oluşturmanız ve kullanılması kesinlikle önerilir.

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Rehberlik**: Azure Container Instances ile Azure Container Registry (ACR) gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız, ACR, bir kayıt defterindeki görüntüler için Etiketler ve bildirimler dahil meta verileri korur. Yapıtları etiketlemek için önerilen uygulamaları izleyin.

- [Kayıt defterleri, depolar ve görüntüler hakkında](../container-registry/container-registry-concepts.md)

- [Kapsayıcı görüntülerini etiketleme ve sürüm oluşturma önerileri](../container-registry/container-registry-image-tag-version.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: Azure Container Instances ile Azure Container Registry (ACR) gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız, ACR, bir kayıt defterindeki görüntüler için Etiketler ve bildirimler dahil meta verileri korur. Yapıtları etiketlemek için önerilen uygulamaları izleyin.

- [Kayıt defterleri, depolar ve görüntüler hakkında](../container-registry/container-registry-concepts.md)

- [Kapsayıcı görüntülerini etiketleme ve sürüm oluşturma önerileri](../container-registry/container-registry-image-tag-version.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynaklarının envanterini tanımlayın ve saklayın

**Rehberlik**: Kurumsal gereksinimlerinize göre onaylanan Azure kaynakları envanterini oluşturmanız gerekecektir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: abonelikleriniz içinde oluşturulabilecek kaynak türlerine yönelik kısıtlamalar koymak Için Azure ilkesini kullanın.

Azure Kaynak Grafiği 'ni kullanarak aboneliklerinde kaynakları sorgulama/bulma. Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

- [Azure Ilkesi kullanarak Azure Container Registry 'nin uyumluluğunu denetleme](../container-registry/container-registry-azure-policy.md)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: işlem kaynakları içindeki onaylanmamış yazılım uygulamaları için izleyici

**Rehberlik**: Azure Container Instances ile Azure Container Registry (ACR) gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız, anormal davranış için Azure Container Registry günlüklerini çözümleyin ve izleyin ve sonuçları düzenli olarak gözden geçirin. Günlükleri gözden geçirmek ve günlük verilerinde sorgular gerçekleştirmek için Azure Izleyici Log Analytics çalışma alanını kullanın.

- [Tanılama değerlendirmesi ve denetimi için Azure Container Registry günlükleri](../container-registry/container-registry-diagnostics-audit-logs.md)

- [Log Analytics çalışma alanını anlayın](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Azure Izleyici 'de özel sorgular gerçekleştirme](/azure/azure-monitor/log-query/get-started-queries)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Kılavuz**: Azure Otomasyonu, dağıtım, işlemler ve iş yüklerinin ve kaynakların yetkisini alma sırasında tamamen denetim sağlar. Yetkisiz Azure kaynaklarını kaldırmak için kendi çözümünüzü uygulayabilirsiniz. 

- [Azure Otomasyonu’na giriş](../automation/automation-intro.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="68-use-only-approved-applications"></a>6,8: yalnızca onaylanan uygulamaları kullan

**Rehberlik**: uygulanamaz. Kıyaslama işlem kaynakları için tasarlanmıştır.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: ortamınızda sağlayabileceğiniz hizmetleri kısıtlamak Için Azure ilkesinden yararlanın.

- [Azure Ilkesi kullanarak Azure Container Registry 'nin uyumluluğunu denetleme](../container-registry/container-registry-azure-policy.md)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: onaylanan yazılım başlıkları envanterini koruyun

**Rehberlik**: uygulanamaz. Kıyaslama işlem kaynakları için tasarlanmıştır.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: kullanıcıların Azure işlem kaynakları içinde betikleri yürütme yeteneğini sınırlamak için işletim sistemine özgü yapılandırma veya üçüncü taraf kaynakları kullanın.

- [Azure Kaynak Yöneticisi 'ne erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: kullanıcıların işlem kaynakları içinde betikleri yürütme yeteneğini sınırlayın

**Rehberlik**: kullanıcıların Azure işlem kaynakları içinde betikleri yürütme yeteneğini sınırlamak için işletim sistemine özgü yapılandırma veya üçüncü taraf kaynakları kullanın.

- [Örneğin, Windows ortamlarında PowerShell betiği yürütmeyi denetleme](/powershell/module/microsoft.powershell.security/set-executionpolicy)

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

**Kılavuz**: Azure Ilkesi veya Azure Güvenlik Merkezi 'ni kullanarak tüm Azure kaynakları için güvenlik yapılandırmalarının bakımını yapın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesi kullanarak Azure Container Registry 'nin uyumluluğunu denetleme](../container-registry/container-registry-azure-policy.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: güvenli işletim sistemi yapılandırması oluşturma

**Rehberlik**: tüm bilgi işlem kaynaklarında güvenlik yapılandırmalarının bakımını yapmak Için Azure Güvenlik Merkezi önerisini "sanal makinelerinizdeki güvenlik yapılandırmalarında güvenlik açıklarını düzeltme" önerilerini kullanın.

- [Azure Güvenlik Merkezi önerilerini izleme](../security-center/security-center-recommendations.md)

- [Azure Güvenlik Merkezi önerilerini Düzeltme](../security-center/security-center-remediate-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Rehberlik**: Azure kaynaklarınız genelinde güvenli ayarları zorlamak için Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] etkileri kullanın.

Azure Container Instances ile Azure Container Registry (ACR) gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız Azure Ilkesi kullanarak Azure Container kayıt defterlerinin uyumluluğunu denetleyin:.

- [Azure Ilkesi kullanarak Azure Container Registry 'nin uyumluluğunu denetleme](../container-registry/container-registry-azure-policy.md)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: güvenli işletim sistemi yapılandırmalarının bakımını yapma

**Rehberlik**: uygulanamaz; Bu denetim, işlem kaynaklarına yöneliktir.

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilke tanımları kullanıyorsanız, kodunuzu güvenli bir şekilde depolamak ve yönetmek için Azure Repos kullanın.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow)

- [Azure Repos belgeleri](/azure/devops/repos/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: özel işletim sistemi görüntülerini güvenli bir şekilde depolayın

**Rehberlik**: uygulanamaz; Bu denetim yalnızca işlem kaynakları için geçerlidir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Kılavuz**: Azure ilkesini kullanarak sistem yapılandırmalarının uyarılarını, denetimini ve zorunlu tutun. Ayrıca, ilke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

Azure Container Instances ile Azure Container Registry (ACR) gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız Azure Ilkesi kullanarak Azure Container kayıt defterlerinin uyumluluğunu denetleyin:.

- [Azure Ilkesi kullanarak Azure Container Registry 'nin uyumluluğunu denetleme](../container-registry/container-registry-azure-policy.md)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: işletim sistemleri için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: uygulanamaz. Kıyaslama işlem kaynakları için geçerlidir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: Azure kaynaklarınız için temel taramalar gerçekleştirmek üzere Azure Güvenlik Merkezi 'ni kullanın. 

Aboneliklerinizde oluşturulabilecek kaynak türlerine yönelik kısıtlamalar koymak için Azure Ilkesini uygulayın.

Azure Container Instances ile Azure Container Registry (ACR) gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız Azure Ilkesi kullanarak Azure Container kayıt defterlerinin uyumluluğunu denetleyin:.

- [Azure Güvenlik Merkezi 'nde öneriler nasıl düzeltileceği](../security-center/security-center-remediate-recommendations.md)

- [Azure Ilkesi kullanarak Azure Container Registry 'nin uyumluluğunu denetleme](../container-registry/container-registry-azure-policy.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: işletim sistemleri için otomatik yapılandırma izlemeyi Uygula

**Rehberlik**: kapsayıcılar için işletim sistemi ve Docker ayarları için temel taramalar gerçekleştirmek üzere Azure Güvenlik Merkezi 'ni kullanın. 

- [Azure Güvenlik Merkezi'ndeki kapsayıcı önerilerini anlama](../security-center/container-security.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: bulut uygulamalarınız için gizli yönetimi basitleştirmek ve güvenli hale getirmek için Azure Key Vault ile birlikte yönetilen hizmet kimliği kullanın.

- [Azure yönetilen kimliklerle tümleştirme](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault oluşturma](../key-vault/secrets/quick-create-portal.md)

- [Key Vault kimlik doğrulaması yapma](container-instances-managed-identity.md)

- [Key Vault erişim ilkesi atama](../key-vault/general/assign-access-policy-portal.md)

- [Azure Container Instances ile yönetilen kimlikleri kullanma](../container-registry/container-registry-tasks-authentication-managed-identity.md)

- [Container Instances ile verileri şifreleme](container-instances-encrypt-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Rehberlik**: Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik ile Azure hizmetleri sağlamak Için Yönetilen kimlikler kullanın. Yönetilen kimlikler, kodunuzda kimlik bilgileri olmadan Azure Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmanıza olanak sağlar.

Azure Container Instances ile Azure Container Registry (ACR) gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız Azure Ilkesi kullanarak Azure Container kayıt defterlerinin uyumluluğunu denetleyin:.

- [Azure Ilkesi kullanarak Azure Container Registry 'nin uyumluluğunu denetleme](../container-registry/container-registry-azure-policy.md)

- [Yönetilen kimlikleri yapılandırma](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Azure Container Instances ile yönetilen kimlikleri kullanma](container-instances-managed-identity.md)

- [Azure Container Registry 'de kimlik doğrulamak için Azure yönetilen kimliği kullanma](../container-registry/container-registry-authentication-managed-identity.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: kod içinde kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayın. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar.

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: merkezi olarak yönetilen kötü amaçlı yazılımdan koruma yazılımı kullanın

**Rehberlik**: kaynaklarınızı sürekli olarak izlemek ve savunmak için Azure Cloud Services ve sanal makineler Için Microsoft kötü amaçlı yazılımdan koruma kullanın. Linux için üçüncü taraf kötü amaçlı yazılımdan koruma çözümünü kullanın.

- [Cloud Services ve sanal makineler için Microsoft Antimalware 'i yapılandırma](../security/fundamentals/antimalware.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: işlem dışı Azure kaynaklarına yüklenecek dosyaları önceden Tara

**Rehberlik**: Microsoft kötü amaçlı yazılımdan koruma, Azure hizmetlerini destekleyen temel alınan konakta (örneğin, Azure Container Instances) etkinleştirilir, ancak müşteri verilerinde çalışmaz.

App Service, Data Lake Storage, BLOB depolama vb. gibi işlem dışı Azure kaynaklarına yüklenen tüm dosyaları önceden tarayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli otomatik arka pencerelere emin olun

**Kılavuz**: Azure Container Instances ile Azure Container Registry (ACR) gibi bulut tabanlı özel bir kayıt defteri kullanıyorsanız, dayanıklılık ve yüksek kullanılabilirlik sağlamak için her zaman otomatik olarak çoğaltılır Microsoft Azure Kapsayıcı kayıt defterinizdeki veriler. Azure Container Registry, verileri planlı ve planlanmamış olaylardan korumak için kopyalar.

İsteğe bağlı olarak, birden çok Azure bölgesindeki kayıt defteri çoğaltmalarını sürdürmek için bir kapsayıcı kayıt defterini çoğaltın.

- [Azure Container Registry coğrafi çoğaltma](../container-registry/container-registry-geo-replication.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: isteğe bağlı olarak, bir kayıt defterinden diğerine aktararak kapsayıcı görüntülerini yedekleyin.

Azure komut satırı araçlarını veya SDK 'larını kullanarak Azure Key Vault müşteri tarafından yönetilen anahtarları yedekleyin.

- [Kapsayıcı görüntülerini kapsayıcı kayıt defterine aktarma](../container-registry/container-registry-import-images.md)

- [Azure 'da Anahtar Kasası anahtarlarını yedekleme](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Dağıtım verilerini Container Instances ile şifreleme](container-instances-encrypt-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: Azure komut satırı araçlarını veya SDK 'larını kullanarak Azure Key Vault, yedeklenen müşteri tarafından yönetilen anahtarların sınamasını geri yükleme.

- [Azure 'da Azure Key Vault anahtarlarını geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: Azure Key Vault Soft-Delete, anahtarları yanlışlıkla veya kötü amaçlı silmeye karşı korumak için etkinleştirebilirsiniz.

- [Key Vault Soft-Delete etkinleştirme](../storage/blobs/soft-delete-blob-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: Kuruluşunuz için bir olay yanıt kılavuzu oluşturun. Tüm personelin rollerine ek olarak algılama aşamasından olay sonrası gözden geçirme aşamasına kadar tüm olay işleme/yönetim aşamalarını tanımlayan yazılı olay yanıt planları bulunduğundan emin olun.

- [Azure Güvenlik Merkezi 'nde Iş akışı otomasyonlarını yapılandırma](../security-center/security-center-planning-and-operations-guide.md)

- [Kendi güvenlik olay yanıtı işleminizi oluşturma kılavuzu](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Güvenlik Yanıt Merkezi 'nin bir olayın anatomisi](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [NıST 'nin bilgisayar güvenliği olay Işleme Kılavuzu](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Kılavuz**: Azure Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, uyarı veren etkinliğin arkasında kötü amaçlı bir amaç olduğunu ve uyarıyı vermek için kullanılan analitik düzeyini, ne kadar güvenli bir güvenlik merkezinin olduğunu temel alır.

Ayrıca, abonelikleri kullanarak abonelikleri işaretleyin ve Azure kaynaklarını tanımlamak ve kategorilere ayırmak için özellikle de hassas verileri işleyen bir adlandırma sistemi oluşturun.  Olayın gerçekleştiği Azure kaynakları ve ortamının önem derecesine bağlı olarak, uyarıların düzeltilmesine öncelik vermek sizin sorumluluğunuzdadır. 

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md) 

- [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](/azure/azure-resource-manager/resource-group-using-tags)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve açıkları belirleyip planı gerektiği şekilde gözden geçirin.

- [NıST 'nin Yayımlama Kılavuzu, BT planları ve becerileri için programları test etme, eğitim ve alıştırma Kılavuzu](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: Microsoft Güvenlik Yanıt MERKEZI (MSRC), müşterinin verilerine izinsiz veya yetkisiz bir taraf tarafından erişildiğini belirlerse, Microsoft tarafından sizinle iletişim kurmak için güvenlik olayı iletişim bilgileri kullanılacaktır. Sorunların çözümlendiğinden emin olmak için gerçesonra olayları gözden geçirin.

- [Azure Güvenlik Merkezi güvenlik ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

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

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](/azure/security/benchmarks/overview)
- [Azure güvenlik temelleri](/azure/security/benchmarks/security-baselines-overview) hakkında daha fazla bilgi edinin
