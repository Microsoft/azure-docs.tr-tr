---
title: Azure Machine Learning için Azure Güvenlik temeli
description: Azure Machine Learning güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: machine-learning
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6457624329d7bb5e367e9de5a1963884e11ad2e3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817008"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Azure Machine Learning için Azure Güvenlik temeli

Bu güvenlik temeli, Microsoft Azure Machine Learning için [Azure Güvenlik kıyaslama sürüm 1,0](../security/benchmarks/overview-v1.md) ' den rehberlik uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar.
İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure Machine Learning için geçerli olan ilgili kılavuza göre gruplandırılır. Azure Machine Learning için geçerli olmayan **denetimler** dışlandı.

 
Azure Machine Learning Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Azure Machine Learning güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: Azure Machine Learning, işlem kaynakları Için diğer Azure hizmetlerini kullanır. İşlem kaynakları (işlem hedefleri) modelleri eğitmek ve dağıtmak için kullanılır. Bu işlem hedeflerini bir sanal ağda oluşturabilirsiniz. Örneğin, bir modeli eğitme ve sonra modeli Azure Kubernetes Service (AKS) ' e dağıtmak için Azure sanal Machine Learning işlem örneğini kullanabilirsiniz. Bir Azure sanal ağı içindeki Azure Machine Learning eğitimi ve çıkarım işlerini yalıtarak makine öğrenimi yaşam döngülerini güvenli hale getirebilirsiniz.

Azure Güvenlik Duvarı, Azure Machine Learning çalışma alanınıza ve genel İnternet 'e erişimi denetlemek için kullanılabilir.

- [Sanal ağ yalıtımı ve gizliliği genel bakış](how-to-network-security-overview.md)

- [Azure Machine Learning için Azure Güvenlik duvarının arkasındaki çalışma alanını kullanın](how-to-access-azureml-behind-firewall.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: sanal ağların, alt ağların ve NIC 'lerin yapılandırmasını ve trafiğini izleyin ve günlüğe kaydedin

**Rehberlik**: Azure Machine Learning, işlem kaynakları Için diğer Azure hizmetlerini kullanır. Machine Learning dağıtımınız olarak oluşturulan ağlara ağ güvenlik grupları atayın. 

Denetim için ağ güvenlik grubu akış günlüklerini etkinleştirin ve günlükleri bir Azure depolama hesabına gönderin. Akış günlüklerini bir Log Analytics çalışma alanına da gönderebilir ve sonra Azure bulutunuzda trafik desenlerine yönelik Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme, etkin noktaları ve güvenlik tehditlerini anlama, trafik akışı desenlerini anlama ve ağ yapılandırmalarını belirlemeyi bilme yeteneğidir.

- [Ağ güvenlik grubu akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="13-protect-critical-web-applications"></a>1,3: kritik Web uygulamalarını koruma

**Rehberlik**: Azure Machine Learning tarafından dağıtılan Web Hizmetleri ile güvenli iletişim sağlamak için https 'yi etkinleştirebilirsiniz. Web Hizmetleri, Azure Kubernetes Hizmetleri (AKS) veya Azure Container Instances (acı) üzerine dağıtılır ve istemciler tarafından gönderilen verilerin güvenliğini sağlar. Ayrıca, Puanlama kısıtlamak için AKS ile özel IP 'yi kullanarak yalnızca bir sanal ağın arkasındaki istemcilerin Web hizmetine erişebilmesi sağlayabilirsiniz.

- [TLS kullanarak Azure Machine Learning aracılığıyla web hizmetinin güvenliğini sağlama](how-to-secure-web-service.md)

- [Sanal ağ yalıtımı ve gizliliği genel bakış](how-to-network-security-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: dağıtılmış hizmet reddi (DDoS) saldırılarına karşı koruma sağlamak için Machine Learning örneğinizle ilişkili sanal ağlarda DDoS koruma standardını etkinleştirin. Bilinen kötü amaçlı veya kullanılmayan Internet IP adresleriyle iletişimi algılamak için Azure Güvenlik Merkezi tümleşik tehdit algılamasını kullanın.

Tehdit zekası tabanlı filtreleme etkin ve kötü amaçlı ağ trafiği için "uyarı ve reddetme" olarak yapılandırılmış bir kuruluşun ağ sınırlarının her birinde Azure Güvenlik Duvarı 'Nı dağıtın.

- [DDoS korumasını yapılandırma](../ddos-protection/manage-ddos-protection.md)

- [Azure Machine Learning için Azure Güvenlik duvarının arkasındaki çalışma alanını kullanın](how-to-access-azureml-behind-firewall.md)

- [Azure Güvenlik Merkezi tehdit algılama hakkında daha fazla bilgi için](../security-center/azure-defender.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Kılavuz**: Azure Machine Learning hizmetlerinizde uygun uzantıya sahip tüm VM 'ler için, anormal etkinlikleri araştırmak üzere Ağ İzleyicisi paket yakalamayı etkinleştirebilirsiniz. 

- [Ağ Izleyicisi örneği oluşturma](../network-watcher/network-watcher-create.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: kötü amaçlı trafiği algılamak ve/veya engellemek için kuruluşunuzun ağ sınırlarının her birinde tercih ettiğiniz güvenlik duvarı çözümünü dağıtın.

Azure Marketi 'nden, yük İnceleme özellikleri ile KIMLIKLER/IP işlevlerini destekleyen bir teklif seçin.  Yük incelemesi bir gereksinim olmadığında, Azure Güvenlik Duvarı tehdit bilgileri kullanılabilir. Azure Güvenlik Duvarı tehdit zekası tabanlı filtreleme, bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen ve giden trafiği filtrelemek ve/veya engellemek için kullanılır. IP adresleri ve etki alanları, Microsoft Tehdit Analizi akışından alınır.

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Güvenlik Duvarı ile uyarıları yapılandırma](../firewall/threat-intel.md)

- [Azure Market](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Web uygulamalarına trafiği yönetme

**Rehberlik**: uygulanamaz; Bu öneri, Azure App Service veya işlem kaynaklarında çalışan Web uygulamalarına yöneliktir.

**Sorumluluk**: geçerli değil

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Kılavuz**: Azure Machine Learning hesabınıza erişmesi gereken kaynaklar için, ağ güvenlik gruplarında veya Azure Güvenlik duvarında ağ erişim denetimleri tanımlamak üzere sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. Bir kuralın uygun kaynak veya hedef alanındaki hizmet etiketi adını (örneğin, AzureMachineLearning) belirterek, karşılık gelen hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

Azure Machine Learning hizmet, karmaşıklığı en aza indirmenize yardımcı olan bir sanal ağ içindeki işlem hedeflerine yönelik hizmet etiketlerinin bir listesini, ağ yönetiminde kılavuz olarak kullanabilirsiniz.

- [Hizmet etiketlerini kullanma hakkında daha fazla bilgi için](../virtual-network/service-tags-overview.md)

- [Sanal ağ yalıtımı ve gizliliği genel bakış](how-to-network-security-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Rehberlik**: Azure ilkesiyle Azure Machine Learning ad boşluklarınızla ilişkili ağ kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın. Machine Learning ad alanlarınızın ağ yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft. Machinöğrenim" ve "Microsoft. Network" ad alanlarında Azure Ilke diğer adlarını kullanın. 

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: bir sınıflandırmaya göre mantıksal olarak organize etmek için Azure Machine Learning dağıtımınız ile ilişkili ağ kaynakları için Etiketler kullanın.

Azure Machine Learning sanal ağınızdaki, açıklama alanını destekleyen bir kaynak için, bir ağdan gelen/giden trafiğe izin veren kuralları belgelemek üzere kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Kılavuz**: Azure etkinlik günlüğü 'nü kullanarak ağ kaynak yapılandırmasını izleyin ve Azure Machine Learning ilgili ağ kaynaklarına yönelik değişiklikleri tespit edin. Kritik ağ kaynaklarında yapılan değişiklikler yürürlüğe girdiğinde tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](/azure/azure-monitor/platform/alerts-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: Azure Machine Learning tarafından oluşturulan güvenlik verilerini toplamak Için Azure izleyici aracılığıyla günlükleri alma. Azure Izleyici 'de analiz sorgulamak ve gerçekleştirmek için Log Analytics çalışma alanlarını kullanın ve uzun süreli ve arşiv depolama için Azure depolama hesaplarını kullanın. Alternatif olarak, Azure Sentinel veya bir üçüncü taraf güvenlik olayı ve olay yönetimi (SıEM) üzerinde verileri etkinleştirebilir ve bu verilere izin verebilir.

- [Azure Machine Learning için tanılama günlüklerini yapılandırma](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#configuration)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: denetim, güvenlik ve tanılama günlüklerine erişmek için Azure kaynaklarında tanılama ayarlarını etkinleştirin. Otomatik olarak kullanılabilen etkinlik günlükleri Olay kaynağını, tarihi, kullanıcıyı, zaman damgasını, kaynak adreslerini, hedef adreslerini ve diğer yararlı öğeleri içerir.

Ayrıca, güvenlik ve uyumluluk amaçları için Machine Learning hizmeti işlem günlüklerinin ilişkilendirilmesi de vardır.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](/azure/azure-monitor/platform/diagnostic-settings)

- [Azure 'da günlüğe kaydetme ve farklı günlük türlerini anlama](/azure/azure-monitor/platform/platform-logs-overview)

- [Azure Machine Learning oturum açmayı etkinleştir](how-to-log-view-metrics.md)

- [İzleme Azure Machine Learning](monitor-azure-machine-learning.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: işletim sistemlerinden güvenlik günlüklerini toplama

**Rehberlik**: Bilgi Işlem kaynağı Microsoft 'a aitse, Microsoft bu işlemi toplamaktan ve izlemekten sorumludur. 

Azure Machine Learning, farklı işlem kaynakları ve hatta kendi işlem kaynaklarınız arasında değişen desteğe sahiptir. Kuruluşunuzun sahip olduğu işlem kaynakları için, işletim sistemini izlemek üzere Azure Güvenlik Merkezi 'ni kullanın. 

- [Azure Izleyici ile Azure sanal makine iç konak günlüklerini toplama](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Azure Güvenlik Merkezi veri toplamayı anlama](../security-center/security-center-enable-data-collection.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure izleyici 'de, kuruluşunuzun uyumluluk düzenlemelerine göre Azure Machine Learning örneklerinize ilişkin Log Analytics çalışma alanları için günlük tutma süresini ayarlayın.

- [Günlük tutma parametrelerini ayarlama](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Rehberlik**: anormal davranışlar için günlükleri çözümleyin ve izleyin ve Azure Machine Learning sonuçları düzenli olarak gözden geçirin. Günlükleri gözden geçirmek ve günlük verilerinde sorgular gerçekleştirmek için Azure Izleyici 'yi ve bir Log Analytics çalışma alanını kullanın.

Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz. 

- [Log Analytics çalışma alanlarında Azure Machine Learning sorguları gerçekleştirme](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#analyzing-log-data)

- [Azure Machine Learning oturum açmayı etkinleştir](how-to-log-view-metrics.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Log Analytics sorguları ile çalışmaya başlama](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Izleyici 'de özel sorgular gerçekleştirme](/azure/azure-monitor/log-query/get-started-queries)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Kılavuz**: Azure izleyici 'de, etkinlik günlüğü içinde Azure Machine Learning ile ilgili günlükleri yapılandırın ve bir Log Analytics çalışma alanına veya uzun vadeli arşiv depolaması için bir depolama hesabına Günlükler göndermek üzere tanılama ayarlarını Machine Learning. Güvenlik günlükleri ve olayları 'nda bulunan anormal etkinlikler için uyarı oluşturmak üzere Log Analytics çalışma alanını kullanın.

Alternatif olarak, Azure Sentinel 'de ve yerleşik verileri etkinleştirebilir.

- [Azure Machine Learning uyarıları hakkında daha fazla bilgi için](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#alerts)

- [Log Analytics çalışma alanı günlük verilerinde uyarı alma](/azure/azure-monitor/learn/tutorial-response)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="28-centralize-anti-malware-logging"></a>2,8: kötü amaçlı yazılımdan koruma 'yı merkezileştirme

**Rehberlik**: Bilgi Işlem kaynağı Microsoft 'a aitse, Microsoft tarafından kötü amaçlı yazılımdan koruma Azure Machine Learning hizmet dağıtımı sorumludur.

Azure Machine Learning, farklı işlem kaynakları ve hatta kendi işlem kaynaklarınız arasında değişen desteğe sahiptir. Kuruluşunuza ait işlem kaynakları için, Azure Cloud Services ve sanal makineler için Microsoft kötü amaçlı yazılımdan koruma olay toplamayı etkinleştirin.

- [Bulut hizmetleri için Microsoft kötü amaçlı yazılımdan koruma uzantısını yapılandırma](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Microsoft kötü amaçlı yazılımdan koruma](../security/fundamentals/antimalware.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="29-enable-dns-query-logging"></a>2,9: DNS sorgu günlüğünü etkinleştir

**Rehberlik**: uygulanamaz; Azure Machine Learning, DNS ile ilgili günlükleri işlemez veya oluşturmaz.

**Sorumluluk**: geçerli değil

**Azure Güvenlik Merkezi izleme**: yok

### <a name="210-enable-command-line-audit-logging"></a>2,10: komut satırı denetim günlüğünü etkinleştir

**Rehberlik**: Azure Machine Learning, farklı işlem kaynakları genelinde ve hatta kendi işlem kaynaklarınız üzerinde değişen desteğe sahiptir. İşlem kaynaklarının kuruluşunuza ait olması için Azure Güvenlik Merkezi 'ni kullanarak Azure sanal makineleri için güvenlik olay günlüğü izlemesini etkinleştirin. Azure Güvenlik Merkezi, desteklenen tüm Azure VM 'lerinde Log Analytics Aracısı ve otomatik sağlama etkinse oluşturulan tüm yenilerini sağlar. Veya aracıyı el ile de yükleyebilirsiniz. Aracı, işlem oluşturma olayı 4688 ve olay 4688 ' nin içindeki CommandLine alanı ' na izin vermez. VM üzerinde oluşturulan yeni süreçler olay günlüğü tarafından kaydedilir ve Güvenlik Merkezi 'nin algılama hizmetleri tarafından izlenir.

- [Azure Güvenlik Merkezinde veri toplama](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Kılavuz**: Azure rol tabanlı erişim denetimi 'Ni (Azure RBAC) yapılandırmak ve Azure Machine Learning kaynaklarında envanteri sürdürmek için Azure Portal bir kaynak için kimlik ve erişim yönetimi sekmesini kullanabilirsiniz. Roller, kullanıcılar, gruplar, hizmet sorumluları ve Active Directory içindeki yönetilen kimliklere uygulanır. Bireyler ve gruplar için yerleşik roller veya özel roller kullanabilirsiniz.

Azure Machine Learning, Azure Machine Learning ortak yönetim senaryoları için yerleşik roller sağlar. Azure Active Directory (Azure AD) içinde bir profili olan bir kişi, Azure Machine Learning kaynaklardaki kaynaklara ve işlemlere erişim vermek veya erişimi reddetmek için bu rolleri kullanıcılara, gruplara, hizmet sorumlularına veya yönetilen kimliklere atayabilir.

Ayrıca, yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü de kullanabilirsiniz.

- [Azure Machine Learning 'de Azure rol tabanlı erişim denetimini anlama](how-to-assign-roles.md)

- [Azure AD 'de PowerShell ile dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Rehberlik**: Machine Learning kaynaklara erişim yönetimi Azure Active Directory (Azure AD) ile denetlenir. Azure AD varsayılan parola kavramına sahip değildir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: Azure Machine Learning yeni bir çalışma alanı oluşturulduğunda, sahip hesaplarının kullanımı etrafında standart işletim yordamları oluşturarak üç varsayılan rolle gelir.

Ayrıca, Azure Active Directory (Azure AD) Privileged Identity Management ve Azure Resource Manager kullanarak Yönetimsel hesaplara tam zamanında erişimi de etkinleştirebilirsiniz.

- [Varsayılan rol Machine Learning daha fazla bilgi edinmek için](https://docs.microsoft.com/azure/machine-learning/how-to-assign-roles#default-roles)

- [Privileged Identity Management hakkında daha fazla bilgi edinin](/azure/active-directory/privileged-identity-management/index)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Azure Active Directory ile çoklu oturum açma (SSO) kullanın

**Kılavuz**: Machine Learning Azure Active Directory (Azure AD) ile tümleşiktir, tek başına bağımsız kimlik bilgilerini hizmet başına yapılandırmak yerıne Azure AD SSO 'yu kullanın. Azure Güvenlik Merkezi kimlik ve erişim önerilerini kullanın.

- [Azure AD ile SSO 'yu anlama](../active-directory/manage-apps/what-is-single-sign-on.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin ve Azure Güvenlik Merkezi kimlik ve erişim önerilerini izleyin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: tüm yönetim görevleri için adanmış makineler (ayrıcalıklı erişim Iş Istasyonları) kullanın

**Rehberlik**: yükseltilmiş ayrıcalıklar gerektiren yönetim görevleri için güvenli, Azure tarafından yönetilen bir iş Istasyonu (ayrıcalıklı erişim iş istasyonu veya Paw olarak da bilinir) kullanın.

- [Güvenli, Azure tarafından yönetilen iş istasyonlarını anlayın](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: ortamda şüpheli veya güvenli olmayan etkinliklerin ne zaman oluştuğunu algılamak için Azure Active Directory (Azure AD) güvenlik raporları ve izleme kullanın. Kimlik ve erişim etkinliğini izlemek için Azure Güvenlik Merkezi 'ni kullanın.

- [Riskli etkinlik bayrağıyla işaretlenen Azure AD kullanıcılarını belirleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Güvenlik Merkezi’nde kullanıcıların kimliğini ve erişim etkinliğini izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: IP adresi aralıklarının veya ülkelerin/bölgelerin yalnızca belirli mantıksal gruplarından erişime izin vermek için adlandırılmış konumları Azure Active Directory (Azure AD) kullanın.

- [Azure AD adlandırılmış konumlarını yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifrelemeyi kullanarak verileri korur. Azure AD Ayrıca, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.
 
Rol erişimi, Azure 'da birden çok düzey kapsamına eklenebilir. Machine Learning için roller çalışma alanı düzeyinde yönetilebilir, örneğin, bir çalışma alanına sahip erişiminiz, çalışma alanını içeren kaynak grubuna sahip erişimine sahip olmayabilir. Bu, aynı kaynak grubu içindeki rolleri ayırmak için daha ayrıntılı erişim denetimleri sağlar. 

- [Azure Machine Learning çalışma alanına erişimi yönetme](how-to-assign-roles.md) 
 
- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlar. Ayrıca, grup üyeliklerini verimli bir şekilde yönetmek, kurumsal uygulamalara erişmek ve rol atamaları için Azure AD kimlik ve erişim gözden geçirmeleri ' nı kullanın. Yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için, Kullanıcı erişimi düzenli olarak incelenebilir.

Ortamda şüpheli veya güvenli olmayan bir etkinlik olduğunda günlüklerin ve uyarıların oluşturulması için Azure AD Privileged Identity Management (PıM) kullanın.

- [Azure AD raporlamayı anlama](/azure/active-directory/reports-monitoring)

- [Azure AD kimlik ve erişim gözden geçirmelerini kullanma](../active-directory/governance/access-reviews-overview.md)

- [Azure AD Privileged Identity Management dağıtma (PıM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: herhangi bir SIEM/izleme aracıyla tümleştirmenize olanak tanıyan Azure Active Directory (Azure AD) oturum açma etkinliğine, denetimine ve risk olay günlüğü kaynaklarına erişiminiz vardır.

Bu işlemi, Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturarak ve Log Analytics çalışma alanına denetim günlüklerini ve oturum açma günlüklerini göndererek kolaylaştırabilirsiniz. İstenen uyarıları Log Analytics çalışma alanı içinde yapılandırabilirsiniz.

- [Azure Izleyici ile Azure etkinlik günlüklerini tümleştirme](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapmasından uyar

**Rehberlik**: otomatik yanıtları yapılandırmak için Azure Active Directory (Azure AD) kimlik koruması özelliklerini kullanın, Kullanıcı kimlikleriyle ilgili şüpheli eylemleri tespit edin. Ayrıca, daha fazla araştırma için verileri Azure Sentinel 'e aktarabilirsiniz.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: destek senaryoları sırasında Microsoft 'un ilgili müşteri verilerine erişimini sağlama

**Rehberlik**: uygulanamaz; Azure Machine Learning hizmet müşteri kasayı desteklemez.

**Sorumluluk**: geçerli değil

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: hassas bilgileri depolayan veya işleyen Azure kaynaklarını izlemeye yardımcı olması için etiketleri kullanın.
 
- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: ortam türü ve veri duyarlılığı düzeyi gibi bireysel güvenlik etki alanları için ayrı abonelikler ve yönetim grupları kullanarak yalıtım uygulayın. Uygulamalarınızın ve kurumsal ortamların talep ettiği Azure kaynaklarınıza erişim düzeyini kısıtlayabilirsiniz. Azure RBAC aracılığıyla Azure kaynaklarına erişimi denetleyebilirsiniz.
 
- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

 
- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: hassas bilgilerin yetkisiz aktarımını izlemek ve bilgi güvenliği uzmanlarına uyarı ederken bu tür aktarımları engellemek için ağ Perimetrelerinde Azure Marketi 'nde üçüncü taraf bir çözüm kullanın. 

Microsoft tarafından yönetilen temel platform açısından, Microsoft tüm müşteri içeriğini hassas olarak değerlendirir ve müşteri verilerinin kaybına ve açığa çıkmasına karşı koruma sağlar. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar. 

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Rehberlik**: Azure Machine Learning aracılığıyla dağıtılan Web Hizmetleri yalnızca aktarım sırasında veri ŞIFRELEMEYI zorlayan TLS sürüm 1,2 ' i destekler.

- [TLS kullanarak Azure Machine Learning aracılığıyla web hizmetinin güvenliğini sağlama](how-to-secure-web-service.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: veri tanımlama, sınıflandırma ve kayıp önleme özellikleri henüz Azure Machine Learning için kullanılabilir değil. Uyumluluk amacıyla gerekliyse bir üçüncü taraf çözümü uygulayın.

Microsoft tarafından yönetilen temel platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: kaynaklara erişimi yönetmek için Azure RBAC kullanma

**Rehberlik**: Azure Machine Learning, istekleri Machine Learning kaynaklara yetkilendirmek için Azure Active Directory (Azure AD) kullanmayı destekler. Azure AD ile, bir kullanıcı veya uygulama hizmeti sorumlusu olabilecek bir güvenlik sorumlusu için izin vermek üzere Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanabilirsiniz.

- [Azure Machine Learning çalışma alanına erişimi yönetme](how-to-assign-roles.md)

- [Kubernetes yetkilendirmesi için Azure RBAC kullanma](../aks/manage-azure-rbac.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Rehberlik**: Azure Machine Learning Azure Machine Learning çalışma alanına ve aboneliğinize bağlı Azure Blob depolama hesabında anlık görüntüler, çıktılar ve Günlükler depolar. Azure Blob depolama alanında depolanan tüm veriler, Microsoft tarafından yönetilen anahtarlarla Rest 'te şifrelenir. Ayrıca, Azure Blob depolamada depolanan verileri Machine Learning hizmetinde kendi anahtarlarınız ile şifreleyebilirsiniz. 

- [Bekleyen veri şifrelemesini Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#encryption-at-rest)

- [Azure’da bekleyen veri şifrelemesini anlama](../security/fundamentals/encryption-atrest.md)

- [Müşteri tarafından yönetilen şifreleme anahtarlarını yapılandırma](../storage/common/customer-managed-keys-configure-key-vault.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Kılavuz**: Azure Machine Learning üretim örneklerine ve diğer önemli veya ilgili kaynaklara yönelik değişikliklerin ne zaman gerçekleştiği hakkında uyarı oluşturmak Için Azure etkinlik günlüğü Ile Azure izleyici 'yi kullanın.

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](/azure/azure-monitor/platform/alerts-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: otomatikleştirilmiş güvenlik açığı tarama araçlarını çalıştırma

**Rehberlik**: Bilgi Işlem kaynağı Microsoft 'a aitse, Microsoft Azure Machine Learning hizmetinin güvenlik açığı yönetiminden sorumludur. 

Azure Machine Learning, farklı işlem kaynakları ve hatta kendi işlem kaynaklarınız arasında değişen desteğe sahiptir. Kuruluşunuza ait işlem kaynakları için Azure sanal makinelerinizde, kapsayıcı görüntülerinde ve SQL sunucularınızda güvenlik açığı değerlendirmeleri gerçekleştirmek üzere Azure Güvenlik Merkezi 'nin önerilerini izleyin.

- [Azure Güvenlik Merkezi güvenlik açığı değerlendirmesi önerilerini uygulama](../security-center/deploy-vulnerability-assessment-vm.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: otomatik işletim sistemi düzeltme eki yönetimi çözümünü dağıtma

**Rehberlik**: Bilgi Işlem kaynağı Microsoft 'a aitse, microsoft, Azure Machine Learning hizmetinin düzeltme eki yönetiminden sorumludur. 

Azure Machine Learning, farklı işlem kaynakları ve hatta kendi işlem kaynaklarınız arasında değişen desteğe sahiptir. Kuruluşunuza ait işlem kaynakları için, Windows ve Linux sanal makinelerinize en son güvenlik güncelleştirmelerinin yüklü olduğundan emin olmak için Azure Otomasyonu Güncelleştirme Yönetimi kullanın. Windows VM 'Leri için Windows Update etkinleştirildiğinden ve otomatik olarak güncelleştirilecek şekilde ayarlandığından emin olun.

- [Azure 'da sanal makineler için Güncelleştirme Yönetimi Yapılandırma](../automation/update-management/overview.md)

- [Güvenlik Merkezi tarafından izlenen Azure Güvenlik ilkelerini anlama](../security-center/policy-reference.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: üçüncü taraf yazılım başlıkları için otomatik düzeltme eki yönetimi çözümü dağıtma

**Rehberlik**: Azure Machine Learning, farklı işlem kaynakları genelinde ve hatta kendi işlem kaynaklarınız üzerinde değişen desteğe sahiptir. Kuruluşunuza ait işlem kaynakları için, üçüncü taraf bir düzeltme eki yönetimi çözümü kullanın. Ortamlarında zaten Configuration Manager kullanan müşteriler, Windows Server Update Service 'te özel güncelleştirmeler yayımlamasına izin veren System Center Updates Publisher da kullanabilir. Bu, Güncelleştirme Yönetimi, üçüncü taraf yazılımlarla güncelleştirme deposu olarak Configuration Manager kullanan makinelere yaması sağlar.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: geri dönüş güvenlik açığı taramalarını karşılaştırın

**Rehberlik**: Azure Machine Learning, farklı işlem kaynakları genelinde ve hatta kendi işlem kaynaklarınız üzerinde değişen desteğe sahiptir. Kuruluşunuza ait işlem kaynakları için, Azure sanal makinelerinizde, kapsayıcı görüntülerinde ve SQL sunucularınızda güvenlik açığı değerlendirmelerini gerçekleştirmek üzere Azure Güvenlik Merkezi 'ndeki öneriler ' i izleyin. Bir güvenlik açığının düzeltildiğini doğrulamak için tarama sonuçlarını tutarlı aralıklarla dışarı aktarın ve sonuçları önceki taramalarla karşılaştırın. Azure Güvenlik Merkezi tarafından önerilen güvenlik açığı yönetimi önerilerini kullanırken, geçmiş tarama verilerini görüntülemek için seçili çözümün portalına de Pivot ekleyebilirsiniz.

- [Azure Güvenlik Merkezi güvenlik açığı değerlendirmesi önerilerini uygulama](../security-center/deploy-vulnerability-assessment-vm.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek için risk derecelendirme işlemi kullanın

**Rehberlik**: uygulanamaz; Bu kılavuz işlem kaynaklarına yöneliktir.

**Sorumluluk**: geçerli değil

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: aboneliklerinizde kaynakları sorgulamak ve (işlem, depolama, ağ, bağlantı noktaları ve protokoller vb.) sorgulamak Için Azure Kaynak grafiğini kullanın. Kiracınızda uygun (okuma) izinlere sahip olun ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerini numaralandırın.

Klasik Azure kaynakları Azure Resource Graph Explorer aracılığıyla keşfedilebilir olsa da, ileri doğru Azure Resource Manager kaynak oluşturmanız ve kullanılması kesinlikle önerilir.

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Kılavuz**: Azure kaynaklarına Etiketler uygulayarak bir taksonomiye göre mantıksal olarak organize edilecek meta veriler ekleyin.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: varlıkları düzenlemek ve izlemek için etiketleme, yönetim grupları ve uygun durumlarda ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

- [ Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [ Yönetim grupları oluşturma](../governance/management-groups/create-management-group-portal.md)
 
- [ Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynakları envanterini tanımlama ve sürdürme

**Rehberlik**: Kurumsal gereksinimlerinize göre işlem kaynakları Için onaylanan Azure kaynakları ve onaylanan yazılım envanterini oluşturun.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:

* İzin verilmeyen kaynak türleri
* İzin verilen kaynak türleri

Ayrıca, aboneliklerdeki kaynakları sorgulamak/saptamak için Azure Kaynak grafiğini kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: işlem kaynakları içindeki onaylanmamış yazılım uygulamaları için izleyici

**Rehberlik**: Azure Machine Learning, farklı işlem kaynakları genelinde ve hatta kendi işlem kaynaklarınız üzerinde değişen desteğe sahiptir. Kuruluşunuza ait işlem kaynakları için, Windows ve Linux VM 'larınızdan envanter bilgilerinin toplanmasını otomatikleştirmek üzere Azure Otomasyonu Değişiklik İzleme ve envanterini kullanın. Yazılım adı, sürüm, yayımcı ve yenileme süresi Azure portal kullanılabilir. Yazılım Yükleme tarihini ve diğer bilgileri almak için, Konuk düzeyinde tanılamayı etkinleştirin ve Windows olay günlüklerini Log Analytics çalışma alanına yönlendirin.

- [Bir VM için Azure Otomasyonu envanter toplamayı etkinleştirme](../automation/automation-tutorial-installed-software.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Rehberlik**: Azure Machine Learning, farklı işlem kaynakları genelinde ve hatta kendi işlem kaynaklarınız üzerinde değişen desteğe sahiptir. Kuruluşunuza ait işlem kaynakları için, VM 'lerde yüklü olan tüm yazılımları tanımlamak için Azure Güvenlik Merkezi 'nin dosya bütünlüğü Izleme (FIM) kullanın. FIM ve Windows sanal makinelerinizden envanter toplamak için Azure Otomasyonu Değişiklik İzleme ve envanterinin yerine veya FIM ile birlikte kullanılabilecek başka bir seçenek. 

Yetkisiz yazılımı kaldırmak için kendi işleminizi uygulayabilirsiniz. Onaylanmamış yazılımları belirlemek için bir üçüncü taraf çözümü de kullanabilirsiniz.

Artık gerekmeyen Azure kaynaklarını kaldırın.

- [Dosya bütünlüğü Izlemeyi kullanma](../security-center/security-center-file-integrity-monitoring.md)

- [Azure Otomasyonu Değişiklik İzleme ve envanterini anlayın](../automation/change-tracking/overview.md)

- [Azure sanal makine envanterini etkinleştirme](../automation/automation-tutorial-installed-software.md)

- [Azure Kaynak grubu ve kaynak silme](../azure-resource-manager/management/delete-resource-group.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="68-use-only-approved-applications"></a>6,8: yalnızca onaylanan uygulamaları kullan

**Rehberlik**: Azure Machine Learning, farklı işlem kaynakları genelinde ve hatta kendi işlem kaynaklarınız üzerinde değişen desteğe sahiptir. Kuruluşunuza ait işlem kaynakları için, Azure Güvenlik Merkezi Uyarlamalı uygulama denetimlerini kullanarak yalnızca yetkili yazılımın yürütüldüğünü ve tüm yetkisiz yazılımların Azure sanal makinelerinde yürütülmesini engellediği emin olun.

- [Azure Güvenlik Merkezi Uyarlamalı uygulama denetimlerini kullanma](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:

* İzin verilmeyen kaynak türleri
* İzin verilen kaynak türleri

Ayrıca, aboneliklerdeki kaynakları sorgulamak ve saptamak için Azure Kaynak grafiğini kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: onaylanan yazılım başlıkları envanterini koruyun

**Rehberlik**: Azure Machine Learning, farklı işlem kaynakları genelinde ve hatta kendi işlem kaynaklarınız üzerinde değişen desteğe sahiptir. Kuruluşunuzun sahip olduğu işlem kaynakları için, bir kuralın hangi dosya türlerini uygulayabileceği veya uygulanmayabilir olduğunu belirtmek için Azure Güvenlik Merkezi Uyarlamalı uygulama denetimlerini kullanın.

Uyarlamalı uygulama denetimleri gereksinimi karşılamıyorsa, üçüncü taraf bir çözüm uygulayın.

- [Azure Güvenlik Merkezi Uyarlamalı uygulama denetimlerini kullanma](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: geçerli değil

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcıların Azure kaynakları yöneticisiyle etkileşime geçmesini sınırlamak için Azure Active Directory (Azure AD) koşullu erişimi kullanın.

- [Azure Kaynak Yöneticisi 'ne erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: kullanıcıların işlem kaynaklarında betikleri yürütme yeteneğini sınırlayın

**Rehberlik**: Azure Machine Learning, farklı işlem kaynakları genelinde ve hatta kendi işlem kaynaklarınız üzerinde değişen desteğe sahiptir. Kuruluşunuzun sahip olduğu işlem kaynakları için, betiklerin türüne bağlı olarak, kullanıcıların Azure işlem kaynaklarında betikleri yürütme yeteneğini sınırlamak üzere işletim sistemine özgü yapılandırma veya üçüncü taraf kaynakları kullanabilirsiniz. Ayrıca, Azure Güvenlik Merkezi Uyarlamalı uygulama denetimlerini kullanarak yalnızca yetkili yazılımın yürütüldüğünü ve tüm yetkisiz yazılımların Azure sanal makinelerinde yürütülmesini engellediği sağlayabilirsiniz.

- [Windows ortamlarında PowerShell betiği yürütmeyi denetleme](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Azure Güvenlik Merkezi Uyarlamalı uygulama denetimlerini kullanma](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: geçerli değil

**Azure Güvenlik Merkezi izleme**: yok

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: yüksek riskli uygulamaları fiziksel olarak veya mantıksal olarak ayırt edin

**Rehberlik**: uygulanamaz; Bu öneri, Azure App Service veya işlem kaynaklarında çalışan Web uygulamalarına yöneliktir.

**Sorumluluk**: geçerli değil

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Kılavuz**: Azure ilkesiyle Azure Machine Learning hizmetiniz için standart güvenlik yapılandırması tanımlayın ve uygulayın. Azure Machine Learning hizmetlerinizin yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft. Machinöğrenim" ad alanındaki Azure Ilke diğer adlarını kullanın.

Azure Resource Manager, yapılandırmaların kuruluşunuzun güvenlik gereksinimlerini karşıladığından emin olmak için gözden geçirilmesi gereken JavaScript Nesne Gösterimi (JSON) içinde şablonu dışarı aktarma özelliğine sahiptir.

Azure Güvenlik Merkezi önerilerini Azure kaynaklarınız için güvenli bir yapılandırma temeli olarak da kullanabilirsiniz.

Azure Machine Learning işi izlemek için Git depolarını tamamen destekler; depoları doğrudan paylaşılan çalışma alanı dosya sisteminize kopyalayabilir, yerel iş istasyonunuzda git 'i kullanabilir ve güvenli yapılandırmaların Machine Learning ortamınızın bir parçası olarak kod kaynaklarına uygulanmasını sağlayabilirsiniz.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias)

- [Öğretici: uyumluluğu zorlamak için ilke oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Güvenlik önerileri - başvuru kılavuzu](../security-center/recommendations-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: güvenli işletim sistemi yapılandırması oluşturma

**Rehberlik**: Işlem kaynağı Microsoft 'a aitse, microsoft, Azure Machine Learning hizmeti 'nin işletim sistemi güvenli yapılandırmalarından sorumludur.

Azure Machine Learning, farklı işlem kaynakları ve hatta kendi işlem kaynaklarınız arasında değişen desteğe sahiptir. Kuruluşunuza ait işlem kaynakları için Azure Güvenlik Merkezi önerilerini kullanarak tüm işlem kaynaklarında güvenlik yapılandırmalarının bakımını yapın.  Ayrıca, kuruluşunuz tarafından istenen işletim sisteminin güvenlik yapılandırmasını oluşturmak için özel işletim sistemi görüntülerini veya Azure Automation durum yapılandırması ' nı kullanabilirsiniz.

- [Azure Güvenlik Merkezi önerilerini izleme](../security-center/security-center-recommendations.md)

- [Güvenlik önerileri - başvuru kılavuzu](../security-center/recommendations-reference.md)

- [Azure Otomasyonu durum yapılandırmasına genel bakış](../automation/automation-dsc-overview.md)

- [Bir VHD 'YI karşıya yükleyin ve Azure 'da yeni Windows VM 'Leri oluşturmak için kullanın](../virtual-machines/windows/upload-generalized-managed.md)

- [Azure CLı ile özel diskten bir Linux VM oluşturma](../virtual-machines/linux/upload-vhd.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Kılavuz**: Azure kaynaklarınız genelinde güvenli ayarları zorlamak Için Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] kullanın. Ayrıca, kuruluşunuz tarafından gerekli olan Azure kaynaklarınızın güvenlik yapılandırmasını sürdürmek için Azure Resource Manager şablonlarını kullanabilirsiniz. 
 
- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)
 
- [Uyumluluğu zorunlu tutmak için ilkeleri oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)
 
- [ Azure Resource Manager şablonlarına genel bakış](../azure-resource-manager/templates/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: güvenli işletim sistemi yapılandırmalarının bakımını yapma

**Rehberlik**: Işlem kaynağı Microsoft 'a aitse, microsoft, Azure Machine Learning hizmeti 'nin işletim sistemi güvenli yapılandırmalarından sorumludur.

Azure Machine Learning, farklı işlem kaynakları ve hatta kendi işlem kaynaklarınız arasında değişen desteğe sahiptir. Kuruluşunuza ait işlem kaynakları için Azure işlem kaynaklarınız üzerinde güvenlik açığı değerlendirmelerinde Azure Güvenlik Merkezi 'nin önerilerini izleyin.  Ayrıca, kuruluşunuz tarafından istenen işletim sisteminin güvenlik yapılandırmasını sürdürmek için Azure Resource Manager şablonları, özel işletim sistemi görüntüleri veya Azure Otomasyonu durum yapılandırması ' nı kullanabilirsiniz.   Azure Otomasyonu durum yapılandırması ile birleştirilmiş Microsoft sanal makine şablonları, güvenlik gereksinimlerinin karşılanmasına ve bakımında yardımcı olabilir. 

Microsoft tarafından yayımlanan Azure Market sanal makine görüntülerinin Microsoft tarafından yönetildiğini ve bakımının yapılmadığını unutmayın. 

- [Azure Güvenlik Merkezi güvenlik açığı değerlendirmesi önerilerini uygulama](../security-center/deploy-vulnerability-assessment-vm.md)

- [ARM şablonundan Azure sanal makinesi oluşturma](../virtual-machines/windows/ps-template.md)

- [Azure Otomasyonu durum yapılandırmasına genel bakış](../automation/automation-dsc-overview.md)

- [Azure portal Windows sanal makinesi oluşturma](../virtual-machines/windows/quick-create-portal.md)

- [VM şablonunun nasıl indirileceği hakkında bilgi](/previous-versions/azure/virtual-machines/windows/download-template)

- [Bir VHD’yi Azure’a yüklemek ve yeni bir sanal makine oluşturmak için örnek betik](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Rehberlik**: Machine Learning veya ilgili kaynaklarınız Için özel Azure ilke tanımları kullanıyorsanız, kodunuzu güvenli bir şekilde depolamak ve yönetmek için Azure Repos kullanın.

Azure Machine Learning işi izlemek için Git depolarını tamamen destekler; depoları doğrudan paylaşılan çalışma alanı dosya sisteminize kopyalayabilir, yerel iş istasyonunuzda git 'i kullanabilir ve güvenli yapılandırmaların Machine Learning ortamınızın bir parçası olarak kod kaynaklarına uygulanmasını sağlayabilirsiniz.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow)

- [Azure Repos belgeleri](/azure/devops/repos/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: özel işletim sistemi görüntülerini güvenli bir şekilde depolayın

**Rehberlik**: Azure Machine Learning, farklı işlem kaynakları genelinde ve hatta kendi işlem kaynaklarınız üzerinde değişen desteğe sahiptir. Kuruluşunuza ait işlem kaynakları için, yalnızca yetkili kullanıcıların özel görüntülerinize erişebildiğinden emin olmak için Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanın. Azure Paylaşılan görüntü Galerisi 'ni kullanarak, görüntülerinizi kuruluşunuzdaki farklı kullanıcılar, hizmet sorumluları veya Azure Active Directory (Azure AD) grupları ile paylaşabilirsiniz. Kapsayıcı görüntülerini Azure Container Registry depolayın ve yalnızca yetkili kullanıcıların erişimi olduğundan emin olmak için Azure RBAC kullanın.

- [Azure RBAC 'yi anlama](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Container Registry için Azure RBAC 'nı anlama](../container-registry/container-registry-roles.md)

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Paylaşılan görüntü galerisine genel bakış](../virtual-machines/shared-image-galleries.md)

- [Kubernetes yetkilendirmesi için Azure RBAC kullanma](../aks/manage-azure-rbac.md)

**Sorumluluk**: geçerli değil

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: sistem yapılandırmalarına uyarı vermek, denetlemek ve zorlamak için özel ilkeler oluşturmak üzere "Microsoft. machinöğrenim" ad alanındaki Azure ilke diğer adlarını kullanın. Ayrıca, ilke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Diğer adları kullanma](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: işletim sistemleri için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: Bilgi Işlem kaynağı Microsoft 'a aitse, Microsoft Azure Machine Learning hizmetinin güvenli yapılandırma dağıtımından sorumludur.

Azure Machine Learning, farklı işlem kaynakları ve hatta kendi işlem kaynaklarınız arasında değişen desteğe sahiptir. Kuruluşunuza ait işlem kaynakları için, Istediğiniz bulut veya şirket içi veri merkezinde Istenen durum yapılandırması (DSC) düğümleri için Azure Otomasyonu durum yapılandırması ' nı kullanın. Makineleri kolayca ekleyebilir, bunlara bildirime dayalı yapılandırmalara atayabilir ve her makinenin, belirttiğiniz istenen durumla uyumluluğunu gösteren raporları görüntüleyebilirsiniz. 

- [Azure Otomasyonu durum yapılandırmasını etkinleştirme](../automation/automation-dsc-onboarding.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: Azure kaynaklarınız için temel taramalar gerçekleştirmek üzere Azure Güvenlik Merkezi 'ni kullanın. Ayrıca Azure Ilkesi 'ni kullanarak Azure Kaynak konfigürasyonları 'nı uyarır ve denetleyin.
 
 
 
- [ Azure Güvenlik Merkezi 'nde öneriler nasıl düzeltileceği](../security-center/security-center-remediate-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: işletim sistemleri için otomatik yapılandırma izlemeyi Uygula

**Rehberlik**: Bilgi Işlem kaynağı Microsoft 'a aitse, Microsoft Azure Machine Learning hizmeti 'nin otomatik güvenli yapılandırma izlemeinden sorumludur.

Azure Machine Learning, farklı işlem kaynakları ve hatta kendi işlem kaynaklarınız arasında değişen desteğe sahiptir. Kuruluşunuza ait işlem kaynakları için Azure Güvenlik Merkezi Işlem uygulamaları ' nı kullanın &amp; ve VM 'ler ve sunucular ve kapsayıcılar önerilerini izleyin.

- [Azure Güvenlik Merkezi'ndeki kapsayıcı önerilerini anlama](../security-center/container-security.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: bulut uygulamalarınız için gizli yönetimi basitleştirmek üzere Azure Key Vault ile birlikte yönetilen kimlikler kullanın.

Azure Machine Learning, müşteri tarafından yönetilen anahtarlarla veri deposu şifrelemesini destekler, anahtar döndürme ve her kuruluşun güvenlik ve uyumluluk gereksinimlerini iptal etme seçeneğini yönetmeniz gerekir. 

Gizli dizileri eğitim betiklerinizde düz metin yerine güvenli bir şekilde geçirmek için Azure Key Vault kullanın.

- [Azure Machine Learning müşteri tarafından yönetilen anahtarlar](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-blob-storage)

- [Azure Machine Learning eğitim çalışmalarından kimlik doğrulama kimlik bilgileri gizli dizilerini kullanın](how-to-use-secrets-in-runs.md)

- [Azure kaynakları için Yönetilen kimlikler kullanma](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault oluşturma](../key-vault/general/quick-create-portal.md)

- [Key Vault kimlik doğrulaması yapma](../key-vault/general/authentication.md)

- [Key Vault erişim ilkesi atama](../key-vault/general/assign-access-policy-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Rehberlik**: Azure Machine Learning hem yerleşik rolleri hem de özel roller oluşturma özelliğini destekler. Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik ile Azure hizmetleri sağlamak için Yönetilen kimlikler kullanın. Yönetilen kimlikler, kodunuzda kimlik bilgileri olmadan Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmanıza olanak sağlar.

- [Azure Machine Learning çalışma alanına erişimi yönetme](how-to-assign-roles.md)

- [Azure kaynakları için Yönetilen kimlikler nasıl yapılandırılır](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: kod içinde kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayın. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar. 

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: merkezi olarak yönetilen kötü amaçlı yazılımdan koruma yazılımı kullanma

**Rehberlik**: Microsoft 'un kötü amaçlı yazılımdan koruma özelliği, Azure hizmetlerini destekleyen temel ana bilgisayar üzerinde etkinleştirilmiştir (örneğin, Azure Machine Learning), ancak müşteri içeriği üzerinde çalışmaz.

Azure Machine Learning, farklı işlem kaynakları ve hatta kendi işlem kaynaklarınız arasında değişen desteğe sahiptir. Kuruluşunuza ait işlem kaynakları için, kaynaklarınızı sürekli olarak izlemek ve savunmak üzere Azure için Microsoft kötü amaçlı yazılımdan koruma kullanın. Linux için üçüncü taraf kötü amaçlı yazılımdan koruma çözümünü kullanın. Ayrıca, depolama hesaplarına yüklenen kötü amaçlı yazılımları algılamak için veri Hizmetleri için Azure Güvenlik Merkezi 'nin tehdit algılamasını kullanın.

- [Azure için Microsoft Antimalware 'i yapılandırma](../security/fundamentals/antimalware.md)

- [Azure Güvenlik Merkezi’nde tehdit koruması](../security-center/azure-defender.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: işlem dışı Azure kaynaklarına yüklenecek dosyaları önceden Tara

**Rehberlik**: Microsoft 'un kötü amaçlı yazılımdan koruma, Azure hizmetlerini destekleyen temel ana bilgisayarda (örneğin, Azure Machine Learning) etkinleştirilir, ancak müşteri içeriğinde çalışmaz.

İşlem dışı Azure kaynaklarına karşıya yüklenen tüm içerikleri önceden taramak sizin sorumluluğunuzdadır. Microsoft müşteri verilerine erişemez ve bu nedenle müşteri içeriğine ait kötü amaçlı yazılımdan koruma taramalarının sizin adınıza yapılamaz.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: kötü amaçlı yazılımdan koruma yazılımlarının ve imzaların güncelleştirildiğinden emin olun

**Rehberlik**: Microsoft 'un kötü amaçlı yazılımdan koruma özelliği, Azure hizmetlerini destekleyen temel ana bilgisayar için etkinleştirilir ve korunur (örneğin, Azure Machine Learning), ancak müşteri içeriği üzerinde çalışmaz.

Azure Machine Learning, farklı işlem kaynakları ve hatta kendi işlem kaynaklarınız arasında değişen desteğe sahiptir. Kuruluşunuzun sahip olduğu işlem kaynakları için, Azure Güvenlik Merkezi 'ndeki öneriler ' i, &amp; tüm uç noktaların en son imzalarla güncel olduğundan emin olmak Için işlem uygulamaları ' nı izleyin. Linux için üçüncü taraf kötü amaçlı yazılımdan koruma çözümünü kullanın.

- [Azure için Microsoft Antimalware nasıl dağıtılır](../security/fundamentals/antimalware.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli Otomatik yedeklemeli UPS sağlayın

**Rehberlik**: Machine Learning hizmetinde veri kurtarma yönetimi, bağlı veri depolarındaki veri yönetimi aracılığıyla yapılır. Müşteri kuruluş ilkelerine göre verileri yedeklemek için bağlı mağazaların veri kurtarma yönergelerini izlediğinizden emin olun.

- [Azure sanal makine yedeklemesinden dosyaları kurtarma](../backup/backup-azure-restore-files-from-vm.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: Machine Learning hizmetinde veri yedekleme, bağlı veri depolarındaki veri yönetimi aracılığıyla yapılır. VM 'Ler için Azure Backup etkinleştirin ve istenen sıklığı ve bekletme dönemlerini yapılandırın. Azure Key Vault 'de müşteri tarafından yönetilen anahtarları yedekleyin.

- [Azure sanal makine yedeklemesinden dosyaları kurtarma](../backup/backup-azure-restore-files-from-vm.md)

- [Azure 'da Key Vault anahtarlarını geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: Machine Learning hizmetinde veri yedekleme doğrulaması, bağlı veri depolarındaki veri yönetimi aracılığıyla yapılır. Azure Backup içeriğin veri geri yüklemesini düzenli olarak gerçekleştirin. Yedeklenen müşteri tarafından yönetilen anahtarları geri yüklemek için emin olun.

- [Azure sanal makine yedeğinden dosya kurtarma](../backup/backup-azure-restore-files-from-vm.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: şirket içi yedekleme için, bekleyen şifreleme, Azure 'a yedeklerken sağladığınız parola kullanılarak sağlanır. Yedeklemeleri ve müşteri tarafından yönetilen anahtarları korumak için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanın. 

Anahtarları yanlışlıkla veya kötü amaçlı silmeye karşı korumak için Key Vault ' de geçici silme ve Temizleme korumasını etkinleştirin. Yedeklemeleri depolamak için Azure depolama kullanılıyorsa, Bloblar veya blob anlık görüntüleri silindiğinde verilerinizi kaydetmek ve kurtarmak için geçici silme özelliğini etkinleştirin.

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

- [Key Vault'ta geçici silme ve temizleme koruması özelliklerini etkinleştirme](../storage/blobs/soft-delete-blob-overview.md)

- [Azure Blob depolama için geçici silme](../storage/blobs/soft-delete-blob-overview.md)

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

**Kılavuz**: Azure Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, uyarı veren etkinliğin arkasında kötü amaçlı bir amaç olduğunu ve uyarıyı vermek için ne kadar uygun güvenlik merkezi 'nin bulunması ya da analizin olduğunu temel alır.

Ayrıca, abonelikleri kullanarak abonelikleri işaretleyin ve Azure kaynaklarını tanımlamak ve kategorilere ayırmak için özellikle de hassas verileri işleyen bir adlandırma sistemi oluşturun. Olayın gerçekleştiği Azure kaynakları ve ortamının önem derecesine bağlı olarak, uyarıların düzeltilmesine öncelik vermek sizin sorumluluğunuzdadır.

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md)

- [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: Azure kaynaklarınızın korunmasına yardımcı olmak için, sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve boşlukları belirleyip yanıt planınızı gerektiği gibi düzeltin.

- [NıST yayını--BT planları ve becerileri için programları test etme, eğitim ve alıştırmaya yönelik kılavuz](https://csrc.nist.gov/publications/detail/sp/800-84/final)

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

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](/azure/security/benchmarks/overview)
- [Azure güvenlik temelleri](/azure/security/benchmarks/security-baselines-overview) hakkında daha fazla bilgi edinin
