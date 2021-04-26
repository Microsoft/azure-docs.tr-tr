---
title: Azure Kubernetes hizmeti için Azure Güvenlik temeli
description: Azure Kubernetes hizmeti güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 0564f1f39ac9d492dfffdf0e7adacdde08db0874
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769598"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Azure Kubernetes hizmeti için Azure Güvenlik temeli

Bu güvenlik temeli [Azure Güvenlik kıyaslama sürümü 1.0'Dan](../security/benchmarks/overview-v1.md) Azure Kubernetes 'e kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure Kubernetes için geçerli olan ilgili kılavuza göre gruplandırılır. **Denetimler** , Azure Kubernetes için geçerli değildir veya sorumluluğun Microsoft tarafından çıkarılmadığı anlamına gelir.

Azure Kubernetes 'in Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Azure Kubernetes güvenlik taban çizgisi eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: varsayılan olarak, bir ağ güvenlik grubu ve yol tablosu, bir Microsoft Azure Kubernetes hizmeti (aks) kümesi oluşturulmasıyla otomatik olarak oluşturulur. AKS, yük dengeleyiciler, bağlantı noktası eşleştirmeleri veya giriş rotaları ile hizmetler oluşturulduğundan uygun trafik akışı için ağ güvenlik gruplarını otomatik olarak değiştirir. Ağ güvenlik grubu, müşteri düğümlerindeki sanal NIC 'ler ve yol tablosu sanal ağ üzerindeki alt ağ ile otomatik olarak ilişkilendirilir. 

Ad alanları ve etiket seçicileri seçimine bağlı olarak bir kümedeki Linux 'ler arasındaki giriş ve çıkış trafiği için kuralları tanımlayarak ağ trafiğini sınırlandırmak için AKS ağ ilkelerini kullanın. Ağ İlkesi kullanımı, tanımlı sanal ağ ve alt ağlar ile Azure CNı eklentisini gerektirir ve yalnızca küme oluşturma sırasında etkinleştirilebilir. Mevcut bir AKS kümesine dağıtılamaz.

AKS API sunucunuz ve düğüm havuzlarınız arasındaki ağ trafiğinin yalnızca özel ağ üzerinde kalmasını sağlamak için özel bir AKS kümesi uygulayabilirsiniz. Denetim düzlemi veya API sunucusu, AKS tarafından yönetilen bir Azure aboneliğinde bulunur ve müşterinin kümesi veya düğüm havuzu kendi aboneliklerinde olduğunda iç (RFC1918) IP adreslerini kullanır. Sunucu ve küme veya düğüm havuzu, API sunucusu sanal ağındaki Azure özel bağlantı hizmetini ve müşterinin AKS kümesinin alt ağında kullanıma sunulan özel bir uç noktasını kullanarak birbirleriyle iletişim kurar.  Alternatif olarak, AKS API sunucusu için genel bir uç nokta kullanın ancak erişimi, AKS API sunucusunun yetkilendirilmiş IP aralıkları özelliğiyle kısıtlayın. 

- [Azure Kubernetes Service’teki (AKS) uygulamalar ve kümeler için güvenlik kavramları](concepts-security.md)

- [Azure Kubernetes Service (aks) içindeki ağ ilkelerini kullanarak Pod arasındaki trafiği güvenli hale getirme](use-network-policies.md)

- [Özel bir Azure Kubernetes hizmet kümesi oluşturma](private-clusters.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: sanal ağların, alt ağların ve NIC 'lerin yapılandırmasını ve trafiğini izleyin ve günlüğe kaydedin

**Kılavuz**: Azure Kubernetes Service (aks) kümeleriniz tarafından kullanılan ağ kaynaklarının güvenliğini sağlamak Için Güvenlik Merkezi 'ni kullanın ve ağ koruma önerilerini izleyin. 

Denetim için ağ güvenlik grubu akış günlüklerini etkinleştirin ve günlükleri bir Azure depolama hesabına gönderin. Ayrıca, akış günlüklerini bir Log Analytics çalışma alanına gönderebilir ve sonra, ağ etkinliğini görselleştirmek, etkin noktaları ve güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için Azure bulutunuzda trafik desenlerine Öngörüler sağlamak üzere Trafik Analizi kullanabilirsiniz.

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

- [Ağ güvenlik akışı günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="13-protect-critical-web-applications"></a>1,3: kritik Web uygulamalarını koruma

**Rehberlik**: Web uygulamalarınıza gelen trafiği filtreleyerek ek bir güvenlik katmanı sağlamak için bir aks kümesinin önünde Azure Application Gateway özellikli bir Web uygulaması güvenlik duvarı (WAF) kullanın. Azure WAF, açık Web uygulaması güvenlik projesi (OWASP) tarafından, siteler arası betik oluşturma veya bu trafiğe karşı zaman damgası gibi saldırılar için sunulan bir kural kümesi kullanır. 

AKS ortamınızda kullanılan API 'Ler için kimlik doğrulama, yetkilendirme, azaltma, önbelleğe alma, dönüştürme ve izleme için bir API ağ geçidi kullanın. Bir API ağ geçidi mikro hizmetlere ön kapı görevi görür, istemcileri mikro hizmetlerinizden ayırır ve çapraz kesme sorunlarını işleme yükünü ortadan kaldırarak mikro hizmetlerinizin karmaşıklığını azaltır.

- [AKS 'de ağ bağlantısı ve güvenlik için en iyi yöntemleri anlama](operator-best-practices-network.md)

- [Application Gateway giriş denetleyicisi ](../application-gateway/ingress-controller-overview.md)

- [Azure Kubernetes hizmetinde dağıtılan mikro hizmetlerle Azure API Management kullanma](../api-management/api-management-kubernetes.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Kılavuz**: Azure Kubernetes hizmeti (aks) bileşenlerinin DDoS saldırılarına karşı korumalar için dağıtıldığı sanal ağlarda Microsoft 'un dağıtılmış hizmet reddi (DDoS) standart korumasını etkinleştirin.

Ağ İlkesi altyapısını yüklemek ve Kubernetes ağ ilkeleri oluşturmak için, varsayılan olarak tüm trafiğe bu düğüm 'ler arasında izin verilir. Ağ ilkesi yalnızca, AKS 'deki Linux tabanlı düğümler ve düğüm 'ler için kullanılmalıdır. Gelişmiş güvenlik için pod iletişimini sınırlayan kuralları tanımlayın. 

Atanmış Etiketler, ad alanı veya trafik bağlantı noktası gibi ayarlara göre trafiğe izin vermeyi veya reddetme seçeneğini belirleyin. Gerekli ağ ilkeleri, bir AKS kümesinde dinamik olarak oluşturulan FID 'ler için otomatik olarak uygulanabilir. 

- [Azure Kubernetes Service (aks) içindeki ağ ilkelerini kullanarak Pod arasındaki trafiği güvenli hale getirme](use-network-policies.md)

- [DDoS korumasını yapılandırma](../ddos-protection/manage-ddos-protection.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Rehberlik**: anormal etkinlikleri araştırmak Için gereken ağ izleyicisi paket yakalamayı kullanın. 

Aboneliğinizde bir sanal ağ oluşturduğunuzda veya güncelleştirdiğinizde sanal ağınızın bölgesinde ağ Izleyicisi otomatik olarak etkinleştirilir. PowerShell, Azure CLı, REST API veya Azure Resource Manager Istemci yöntemi kullanarak ağ Izleyicisi 'nin yeni örneklerini de oluşturabilirsiniz.

- [Ağ İzleyicisini etkinleştirme](../network-watcher/network-watcher-create.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: Azure Kubernetes Service (aks) kümenizin güvenliğini bir Web uygulaması güvenlik duvarı (WAF) ile etkinleştirilen bir Azure Application Gateway. 

Yük denetimi veya davranış analizine dayalı olarak yetkisiz giriş algılama ve/veya önleme bir gereksinim değilse, WAF içeren bir Azure Application Gateway, uyarıları ve tehditleri günlüğe kaydetmek için "algılama modunda" kullanılabilir ve yapılandırılabilir ve algılanan yetkisiz erişimi ve saldırıları etkin bir şekilde engellemek için "önleme modu" kullanabilirsiniz.

- [AKS kümenizi bir WAF ile güvenli hale getirmek için en iyi yöntemleri anlama](https://docs.microsoft.com/azure/aks/operator-best-practices-network#secure-traffic-with-a-web-application-firewall-waf)

- [Azure Application Gateway dağıtma (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Kılavuz**: Azure Kubernetes Service (aks) örnekleriyle ilişkili ağ güvenlik grupları üzerinde ağ erişim denetimleri tanımlamak için sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketleri, hizmet etiketi adı belirtilerek karşılık gelen hizmetin trafiğine izin vermek veya reddetmek üzere güvenlik kuralları oluştururken belirli IP adreslerinin yerine kullanılabilir. 

Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

AKS kümenizdeki düğüm havuzlarına bir Azure etiketi uygulayın. Bunlar, sanal ağ hizmeti etiketlerinden farklıdır ve düğüm havuzu içindeki her düğüme uygulanır ve yükseltmeler aracılığıyla kalır. 

- [Hizmet etiketlerini anlama ve kullanma](../virtual-network/service-tags-overview.md)

- [AKS için NSG 'leri anlayın](support-policies.md)

- [Azure Kubernetes Service (AKS) içindeki küme düğümleri için çıkış trafiğini denetleme](limit-egress-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Rehberlik**: Azure Kubernetes Service (aks) kümelerinizle ilişkili ağ kaynakları Için Azure ilkesiyle birlikte standart güvenlik yapılandırması tanımlayın ve uygulayın. 

AKS kümelerinizin ağ yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft. ContainerService" ve "Microsoft. Network" ad alanlarında Azure Ilke diğer adlarını kullanın. 

Ayrıca, AKS ile ilgili yerleşik ilke tanımlarını kullanın, örneğin:

- Yetkili IP aralıkları, Kubernetes hizmetlerinde tanımlanmalıdır

- Kubernetes kümesinde HTTPS girişini zorla

- Hizmetlerin yalnızca Kubernetes kümesinde izin verilen bağlantı noktalarında dinlemesine emin olun

Ek bilgiler başvurulan bağlantılarda bulunabilir.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Ağ için Azure Ilke örnekleri](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Kılavuz**: Azure Kubernetes Service (aks) kümelerine giden ve giden trafik akışı için ağ güvenlik grupları ve diğer kaynaklar için Etiketler kullanın. Bir ağ üzerinden/giden trafiğe izin veren tüm kurallar için, her bir ağ güvenlik grubu kuralı için "Açıklama" alanını, iş ihtiyacını ve/veya süresini belirtmek için kullanın.

Yerleşik Azure Ilkesi etiketlemeyle ilgili tanımlarından herhangi birini kullanın, örneğin, "etiket ve değerini ıste", tüm kaynakların etiketlerle oluşturulmasını ve var olan etiketlenmemiş kaynaklar için bildirimler almasını sağlar.

Ağ ilkeleriyle ad alanları ve etiket seçicileri temelinde küme içindeki belirli ağ yollarına izin vermeyi veya reddetme seçeneğini belirleyin. Bu ad alanlarını ve etiketleri trafik yapılandırma kuralları için tanımlayıcı olarak kullanın. Azure PowerShell veya Azure komut satırı arabirimi 'ni (CLı) kullanarak bu kaynakları etiketlere göre arayabilir veya bunlarla ilgili işlemler gerçekleştirin.

- [CLı ile Azure Ilkesi](/cli/azure/policy)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Kılavuz**: Azure etkinlik günlüğü 'nü kullanarak ağ kaynak yapılandırmasını Izleyin ve Azure Kubernetes hizmeti (aks) kümeleriyle ilgili ağ kaynaklarına yönelik değişiklikleri tespit edin. 

Kritik ağ kaynaklarında yapılan değişiklikler yürürlüğe girdiğinde tetiklenecek Azure Izleyici içinde uyarılar oluşturun. Etkinlik günlüklerindeki AzureContainerService Kullanıcı girdileri platform eylemleri olarak günlüğe kaydedilir. 

Azure Izleyici günlüklerini kullanarak, bir ana bileşen bileşenleri olan kuin-apiserver ve kuin-Controller-Manager arasındaki günlükleri etkinleştirebilir ve sorgulayın. Kapsayıcı çalışma zamanı ile kubelet çalıştıran düğümleri oluşturun ve yönetin, yönetilen Kubernetes API sunucusu aracılığıyla uygulamalarını dağıtın. 

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](/azure/azure-monitor/platform/alerts-activity-log)

- [Azure Kubernetes Service (AKS) içindeki Kubernetes ana düğüm günlüklerini etkinleştirme ve inceleme](/azure/aks/view-master-logs)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: onaylanan zaman eşitleme kaynaklarını kullanın

**Kılavuz**: Azure Kubernetes Service (aks) düğümleri, NTP.Ubuntu.com for UDP bağlantı noktası 123 ve ağ zaman Protokolü (NTP) ile birlikte zaman eşitlemesi için kullanır. 

Özel DNS sunucuları kullanılıyorsa, NTP sunucularının küme düğümleri tarafından erişilebilir olduğundan emin olun. 

- [AKS küme düğümleri için NTP etki alanını ve bağlantı noktası gereksinimlerini anlayın](limit-egress-traffic.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: yönetilen bir hizmet olarak sunulan Azure Kubernetes Services (aks) ana bileşenlerinden, kuas-apiserver ve kuas-Controller-Manager denetim günlüklerini etkinleştirin. 

- Kuto-auditaksService: denetim dosyası işlemi için denetim günlüğünde görünen ad (hcpService 'ten) 

- MasterClient: MasterClientCertificate için Denetim günlüğündeki görünen ad, az aks Get-Credentials ile alacağınız sertifika 

- nodeclient: aracı düğümleri tarafından kullanılan ClientCertificate için görünen ad

Kuto-Audit gibi diğer denetim günlüklerini de etkinleştirin. 

Bu günlükleri Log Analytics veya başka bir depolama platformuna dışarı aktarın. Azure Izleyici 'de analiz sorgulamak ve gerçekleştirmek için Log Analytics çalışma alanlarını kullanın ve uzun süreli ve arşiv depolama için Azure depolama hesaplarını kullanın.

Kurumsal iş gereksinimlerinize bağlı olarak bu verileri Azure Sentinel 'e veya üçüncü taraf bir SıEM 'e etkinleştirin ve yerleşik olarak yapın.

- [Günlük rollerini içeren günlük şemasını buraya gözden geçirin](/azure/aks/view-master-logs)

- [Kapsayıcılar için Azure Izleyicisini anlama](/azure/azure-monitor/insights/container-insights-overview)

- [Kapsayıcılar için Azure Izleyicisini etkinleştirme](/azure/azure-monitor/insights/container-insights-onboard)

- [Azure Kubernetes Service (AKS) içindeki Kubernetes ana düğüm günlüklerini etkinleştirme ve inceleme](/azure/aks/view-master-logs)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: tüm etkinlikleri ve bunların durumunu görüntülemek Için Azure Kubernetes Service (aks) kaynaklarındaki eylemleri Izlemek için etkinlik günlüklerini kullanın. Aboneliğinizdeki kaynaklar üzerinde etkinlik günlükleriyle hangi işlemlerin alındığını belirleme: 

- işlemi kim başlattı
- işlem oluştuğunda
- işlemin durumu
- işlemi araştırmanıza yardımcı olabilecek diğer özelliklerin değerleri

Azure PowerShell, Azure komut satırı arabirimi (CLı), Azure REST API veya Azure portal aracılığıyla etkinlik günlüğünden bilgi alın. 

AKS ana bileşenlerinde denetim günlüklerini etkinleştirin; örneğin: 

- Kuto-auditaksService: denetim dosyası işlemi için denetim günlüğünde görünen ad (hcpService 'ten) 

- MasterClient: MasterClientCertificate için Denetim günlüğündeki görünen ad, az aks Get-Credentials ile alacağınız sertifika 

- nodeclient: aracı düğümleri tarafından kullanılan ClientCertificate için görünen ad

Kuin-Audit gibi diğer denetim günlüklerini de açın. 

- [AKS 'de Kubernetes ana düğüm günlüklerini etkinleştirme ve gözden geçirme](/azure/aks/view-master-logs)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: işletim sistemlerinden güvenlik günlüklerini toplama

**Rehberlik**: aks küme düğümlerinden veri toplamak için Log Analytics aracılarının otomatik yüklenmesini etkinleştirin. Ayrıca, Azure Güvenlik Merkezi 'nden Azure Log Analytics Izleme aracısının otomatik olarak sağlanması, varsayılan olarak otomatik sağlama kapalıdır. Aracı el ile de yüklenebilir. Otomatik sağlama, Güvenlik Merkezi tarafından desteklenen tüm Azure VM 'Lere ve oluşturulan tüm yeni sanal makinelere Log Analytics aracısını dağıtır. Güvenlik Merkezi, güvenlik açıklarını ve tehditleri izlemek için Azure sanal makineleri (VM), sanal makine ölçek kümeleri ve Kubernetes küme düğümleri gibi IaaS kapsayıcılarından verileri toplar. Veriler, makineden güvenlikle ilgili çeşitli yapılandırma ve olay günlüklerini okuyan ve analiz için verileri çalışma alanınıza kopyalayan Azure Log Analytics Aracısı kullanılarak toplanır. 

Eksik güncelleştirmelere görünürlük sağlamak için veri toplama gerekir, yanlış yapılandırılmış işletim sistemi güvenlik ayarları, uç nokta koruma durumu ve sistem durumu ve tehdit algılamaları.

- [Log Analytics aracısının otomatik olarak sağlanması nasıl etkinleştirilir](../security-center/security-center-enable-data-collection.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure Kubernetes hizmeti (aks) örneklerinizi Azure izleyicisine ekleme ve ilgili Azure Log Analytics çalışma alanı bekletme dönemini kuruluşunuzun uyumluluk gereksinimlerine göre ayarlama. 

- [Log Analytics çalışma alanları için günlük saklama parametrelerini ayarlama](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Kılavuz**: Azure Kubernetes hizmeti (aks) örneklerinizi Azure izleyicisine ekleme ve kümeniz için tanılama ayarlarını yapılandırma. 

Günlükleri gözden geçirmek ve günlük verilerinde sorgular gerçekleştirmek için Azure Izleyici Log Analytics çalışma alanını kullanın. Azure Izleyici günlükleri Azure portal veya CLı aracılığıyla etkinleştirilir ve yönetilir ve hem Kubernetes rol tabanlı erişim denetimi (Kubernetes RBAC), Azure RBAC, hem de RBAC özellikli olmayan AKS kümeleriyle çalışır.

Uygulama ve hizmetlerinizin sorunlarını gidermek için AKS ana bileşenleri tarafından oluşturulan günlükleri görüntüleyin (kuııcuonver ve Kuto-controllermanager). Merkezi günlük yönetimi ve izleme için verileri Azure Sentinel 'e veya üçüncü taraf bir SıEM 'e etkinleştirin.

- [AKS 'de Kubernetes ana düğüm günlüklerini etkinleştirme ve gözden geçirme](/azure/aks/view-master-logs)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Izleyici 'de özel sorgular gerçekleştirme](/azure/azure-monitor/log-query/get-started-queries)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Rehberlik**: aks düğümlerine daha derin bir görünürlük elde etmek Için Azure Kubernetes Service 'ı (aks) Güvenlik Merkezi ile birlikte kullanın. 

Konuk ve küme düzeyinde algılanan tehditler ve kötü amaçlı etkinliklerde Güvenlik Merkezi uyarılarını gözden geçirin. Güvenlik Merkezi, ağ verileri, işlem oluşturma ve Kubernetes denetim günlüğü gibi bir AKS kümesinde gerçekleşen ham güvenlik olaylarının sürekli analizini uygular. Bu etkinliğin beklenen davranış olup olmadığını veya uygulamanın hatalı çalışıp çalışmadığını belirleme. Bulgularınızı eklemek için Azure Izleyici 'de ölçümleri ve günlükleri kullanın. 

- [Güvenlik Merkezi ile Azure Kubernetes Hizmetleri tümleştirmesini anlama](../security-center/defender-for-kubernetes-introduction.md)

- [Azure Güvenlik Merkezi Standart katmanını etkinleştirme](../security-center/security-center-get-started.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="28-centralize-anti-malware-logging"></a>2,8: kötü amaçlı yazılımdan koruma 'yı merkezileştirme

**Kılavuz**: Azure Için Microsoft 'un kötü amaçlı yazılımdan koruma yazılımını Azure Kubernetes hizmeti (aks) sanal makinelerine ve sanal makine ölçek kümesi düğümlerine yükleyip etkinleştirin. Düzeltme için Güvenlik Merkezi 'ndeki uyarıları gözden geçirin.

- [Azure Cloud Services ve sanal makineler için Microsoft kötü amaçlı yazılımdan koruma](../security/fundamentals/antimalware.md)

- [Güvenlik uyarıları başvuru kılavuzu](../security-center/alerts-reference.md)

- [Kapsayıcılar için uyarılar-Azure Kubernetes hizmet kümeleri](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="29-enable-dns-query-logging"></a>2,9: DNS sorgu günlüğünü etkinleştir

**Kılavuz**: Azure Kubernetes hizmeti (aks), küme DNS yönetimi ve çözümü Için coredns projesini kullanır.

Coredns-Custom ConfigMap 'inizdeki belgelenen yapılandırmayı uygulayarak DNS sorgu günlüğünü etkinleştirin. 

- [Azure Kubernetes Service ile CoreDNS hizmetini özelleştirme](coredns-custom.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="210-enable-command-line-audit-logging"></a>2,10: komut satırı denetim günlüğünü etkinleştir

**Rehberlik**: bir Kubernetes kümesini yönetmek ve sorun giderme amacıyla aks düğümünden günlüklerini almak Için Azure Kubernetes Service (aks) içindeki bir komut satırı istemcisi olan kubectl kullanın. Azure Cloud Shell kullanıyorsanız, kubectl zaten yüklüdür. Kubectl 'yi yerel olarak yüklemek için ' Install-AzAksKubectl ' cmdlet 'ini kullanın.

- [Hızlı başlangıç-PowerShell kullanarak bir Azure Kubernetes hizmet kümesi dağıtma](kubernetes-walkthrough-powershell.md)

- [Azure Kubernetes Service (AKS) kümesi düğümlerinden kubelet günlüklerini alma](kubelet-logs.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Kılavuz**: Azure Kubernetes Service (aks), normal kullanıcı hesaplarını ve parolaları depolayan bir kimlik yönetimi çözümü sağlamaz. Azure Active Directory (Azure AD) Tümleştirmesi sayesinde, kullanıcılara veya gruplara bir ad alanı veya küme genelinde Kubernetes kaynaklarına erişim izni verebilirsiniz.

Azure AD PowerShell modülü ile AKS yönetim gruplarının üyeleri olan hesapları bulmaya yönelik geçici sorgular gerçekleştirme

Erişimi düzenli olarak mutabık kılma konusunda yardımcı olmak için, ' yönetilen bir Kubernetes kümesi için erişim kimlik bilgileri al ' gibi işlemler için Azure CLı kullanın. AKS 'de başka bir birincil kullanıcı türü olan hizmet hesaplarının güncelleştirilmiş envanterini tutmak için bu işlemi uygulayın. Güvenlik Merkezi 'nin kimlik ve erişim yönetimi önerilerini uygulayın.

- [AKS 'leri Azure AD ile tümleştirme](azure-ad-integration-cli.md)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure Güvenlik Merkezi ile kimlik ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Kılavuz**: Azure Kubernetes hizmeti (aks) ortak varsayılan parola kavramına sahip değildir ve normal kullanıcı hesaplarının ve parolaların depolanabileceği bir kimlik yönetimi çözümü sağlamaz. Azure Active Directory (Azure AD) Tümleştirmesi sayesinde, bir ad alanı veya küme genelinde AKS kaynaklarına rol tabanlı erişim izni verebilirsiniz. 

Azure AD PowerShell modülü ile AKS yönetim gruplarının üyeleri olan hesapları bulmaya yönelik geçici sorgular gerçekleştirme

- [AKS için erişimi ve kimlik seçeneklerini anlama](concepts-identity.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: Azure Kubernetes Service (aks) kümeleriniz için Kullanıcı kimlik doğrulamasını Azure Active Directory (Azure AD) ile tümleştirin. Bir Azure AD kimlik doğrulama belirteci kullanarak bir AKS kümesinde oturum açın. Kubernetes yapılandırma (kubeconfig) bilgileri ve izinleri, ad alanları ve küme kaynaklarına yönetim erişimi için Kubernetes rol tabanlı erişim denetimini (Kubernetes RBAC) yapılandırın. 

Adanmış yönetim hesaplarının kullanımı etrafında ilke ve yordamlar oluşturun. Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini uygulayın.

- [Azure Güvenlik Merkezi ile kimlik ve erişimi izleme](../security-center/security-center-identity-access.md)

- [Küme kaynaklarına erişimi denetleme](azure-ad-rbac.md)

- [Azure rol tabanlı erişim denetimlerini kullanma](control-kubeconfig-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Azure Active Directory ile çoklu oturum açma (SSO) kullanın

**Rehberlik**: BIR aks kümesi için Azure Active Directory (Azure AD) tümleşik kimlik doğrulaması Ile Azure Kubernetes hizmeti (aks) için çoklu oturum açma kullanın.

- [Kubernetes günlüklerini, olayları ve pod ölçümlerini gerçek zamanlı olarak görüntüleme](/azure/azure-monitor/insights/container-insights-livedata-overview)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Kubernetes hizmeti (aks) Için kimlik doğrulamasını Azure Active Directory (Azure AD) ile tümleştirin. 

Azure AD çok faktörlü kimlik doğrulamasını etkinleştirin ve Güvenlik Merkezi 'nin kimlik ve erişim yönetimi önerilerini izleyin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: tüm yönetim görevleri için adanmış makineler (ayrıcalıklı erişim Iş Istasyonları) kullanın

**Rehberlik**: belirtilen Azure Kubernetes Service (aks) kümelerinde ve ilgili kaynaklarda oturum açmak üzere yapılandırılmış, MULTI-Factor AUTHENTICATION (MFA) Ile bir ayrıcalıklı erişim iş istasyonu (Paw) kullanın.

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure 'da Multi-Factor Authentication (MFA) nasıl etkinleştirilir](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Kılavuz**: Azure Kubernetes Service (aks) IÇIN Azure AD ile tümleşik kimlik doğrulamasıyla Azure Active Directory (Azure AD) güvenlik raporları kullanın. Ortamda şüpheli veya güvenli olmayan bir etkinlik gerçekleştiğinde uyarılar oluşturulabilir. Kimlik ve erişim etkinliğini izlemek için Güvenlik Merkezi 'ni kullanın.

- [Riskli etkinlik bayrağıyla işaretlenen Azure AD kullanıcılarını belirleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Güvenlik Merkezi 'nde Kullanıcı kimliğini ve erişim etkinliğini izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Kılavuz**: Azure Kubernetes hizmeti (aks) KÜMELERINE yalnızca IP adresi aralıklarının veya ülkelerin/bölgelerin belirli mantıksal gruplarından erişime izin vermek Için, koşullu erişim adlı konum kullanın. Bu, Azure Active Directory (Azure AD) ile AKS için tümleşik kimlik doğrulaması gerektirir.

AKS API sunucusu erişimini sınırlı bir IP adresi kümesinden sınırlayın. Bu, kaynak oluşturmak veya düğüm sayısını ölçeklendirmek üzere kümede eylem gerçekleştirme istekleri aldığından. 

- [Azure Kubernetes Service (AKS) içindeki yetkili IP adresi aralıklarını kullanarak API sunucusuna güvenli erişim](api-server-authorized-ip-ranges.md)

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Kılavuz**: Azure Kubernetes Service (aks) için merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen veriler ve aktarım ve sallar, karma değerleri ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolayan verileri kullanarak verileri korur.

Kubernetes kümenize ilke atama işlemleri için Azure rol tabanlı erişim denetimi (Azure RBAC)-kaynak Ilkesi katılımcısı ve sahibi ile birlikte bulunan yerleşik rolleri kullanın

- [Azure İlkesi'ne Genel Bakış](../governance/policy/overview.md)

- [Azure AD 'yi AKS ile tümleştirme](azure-ad-integration-cli.md)

- [AKS tarafından yönetilen Azure AD 'yi tümleştirme](managed-aad.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Kılavuz**: Azure Kubernetes Service (aks) IÇIN Azure AD ile tümleşik kimlik doğrulamasıyla Azure Active Directory (Azure AD) güvenlik raporları kullanın. Eski hesapların keşfedilmesine yardımcı olması için Azure AD günlüklerinde arama yapın. 

Grup üyeliklerini verimli bir şekilde yönetmek, kurumsal uygulamalara erişmek ve rol atamalarına yönelik Azure kimlik erişimi Incelemeleri gerçekleştirin. Güvenlik Merkezi 'nden kimlik ve erişim önerilerini düzeltin.

Destek veya sorun giderme amacıyla kullanılan rollerden haberdar olun. Örneğin, Microsoft desteği tarafından alınan (Kullanıcı onayı ile) herhangi bir küme eylemi, aks-support-rolebinding adlı yerleşik bir Kubernetes "düzenleme" rolü altında yapılır. Bu rolle, küme yapılandırma ve kaynaklarını düzenlemek için bu rolle birlikte AKS desteği etkinleştirilir ve küme sorunlarını tanılayın. Ancak, bu rol izinleri değiştiremez ve rol ya da rol bağlamaları oluşturamaz. Bu rol erişimi yalnızca anında etkin destek biletleri altında yalnızca zamanında (JıT) erişim ile etkinleştirilir.
 
- [Azure Kubernetes Service (AKS) için erişim ve kimlik seçenekleri](concepts-identity.md)

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

- [Azure Güvenlik Merkezi 'nde kullanıcının kimlik ve erişim etkinliğini izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: Azure Kubernetes hizmeti (aks) için Kullanıcı kimlik doğrulamasını Azure Active Directory (Azure AD) ile tümleştirin. Denetim ve oturum açma günlüklerini Azure Log Analytics çalışma alanına göndererek Azure AD için Tanılama ayarları oluşturun. Azure Log Analytics çalışma alanında istenen uyarıları (devre dışı bırakılmış bir hesabın oturum açmaya çalıştığı gibi) yapılandırın.
- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure Izleyici kullanarak günlük uyarıları oluşturma, görüntüleme ve yönetme](/azure/azure-monitor/platform/alerts-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapmasından uyar

**Rehberlik**: Azure Kubernetes hizmeti (aks) için Kullanıcı kimlik doğrulamasını Azure Active Directory (Azure AD) ile tümleştirin. Azure AD 'nin risk algılamaları ve kimlik koruması özelliğini kullanarak otomatik yanıtları, Kullanıcı kimlikleriyle ilgili şüpheli eylemleri tespit etmek üzere yapılandırın. İş gereksinimlerine göre daha fazla araştırma için Azure Sentinel 'e veri alma.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: hassas bilgileri depolayan veya işleyen Azure kaynaklarını izlemeye yardımcı olmak Için Azure Kubernetes Service (aks) dağıtımlarıyla ilgili kaynaklarda Etiketler kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Yönetilen kümeler için etiketleri güncelleştir](/rest/api/aks/managedclusters/updatetags)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: her takım için gereken kaynaklara göre en az sayıda ayrıcalık sağlamak üzere Azure Kubernetes Service (aks) ile aynı kümedeki takımları ve iş yüklerini mantıksal olarak yalıtın. 

Bir mantıksal yalıtım sınırı oluşturmak için Kubernetes içindeki ad alanını kullanın. Yalıtım ve çok kiracılı, zamanlama, ağ, kimlik doğrulama/yetkilendirme ve kapsayıcılar gibi ek Kubernetes özellikleri uygulamayı düşünün.

Geliştirme, test ve üretim ortamları için ayrı abonelikler ve/veya yönetim grupları uygulayın. AKS kümelerini, uygun şekilde etiketlendirilmiş ayrı sanal ağlara dağıtarak ağ ile ayırın.

- [AKS 'de küme yalıtımına yönelik en iyi yöntemler hakkında bilgi edinin](operator-best-practices-cluster-isolation.md)

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [AKS 'de ağ bağlantısı ve güvenlik için en iyi yöntemleri anlama](operator-best-practices-network.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: Azure Marketi 'nden, önemli bilgilerin yetkisiz aktarımını izleyen ve bilgi güvenliği uzmanlarına uyarı ederken bu tür aktarımları engelleyen bir üçüncü taraf çözümü kullanın.

Microsoft temel platformu yönetir ve tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [AKS işlevselliği için gerekli bağlantı noktaları, adresler ve etki alanı adları listesi](limit-egress-traffic.md)

- [Azure Güvenlik Duvarı için tanılama ayarlarını yapılandırma](../firewall/firewall-diagnostics.md)

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Kılavuz**: Azure Kubernetes Service (aks) dağıtımlarınız IÇIN bir https giriş denetleyicisi oluşturun ve kendi TLS sertifikalarınızı (veya isteğe bağlı olarak, şifreleyelim) kullanın. 

Kubernetes çıkış trafiği varsayılan olarak HTTPS/TLS üzerinden şifrelenir. Ek izleme için AKS örnekinizdeki tüm olası şifreli çıkış trafiğini gözden geçirin. Bu, bazı durumlarda güncelleştirmeleri almak için NTP trafiği, DNS trafiği, HTTP trafiği içerebilir. 

- [AKS üzerinde bir HTTPS giriş denetleyicisi oluşturma ve kendi TLS sertifikalarınızı kullanma](ingress-own-tls.md)

- [AKS 'in Şifreleyebilmesine sahip bir HTTPS giriş denetleyicisi oluşturma](ingress-tls.md)

- [AKS tarafından kullanılan olası giden bağlantı noktaları ve protokoller listesi](limit-egress-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: veri tanımlama, sınıflandırma ve kayıp önleme özellikleri, Azure depolama veya işlem kaynakları için henüz kullanılamıyor. Uyumluluk amacıyla gerekliyse üçüncü taraf çözümünü uygulayın.
Microsoft temel platformu yönetir ve tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. 

Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: kaynaklara erişimi yönetmek için Azure RBAC kullanma

**Kılavuz**: Azure kaynakları üzerinde ayrıntılı erişim yönetimi sağlamak için Azure Resource Manager yerleşik Azure rol tabanlı erişim denetimi (Azure RBAC) yetkilendirme sistemini kullanın.

Azure Kubernetes hizmetini (AKS) Kullanıcı kimlik doğrulaması için Azure Active Directory (Azure AD) kullanacak şekilde yapılandırın. Bu yapılandırmayı kullanarak bir AKS kümesinde Azure AD kimlik doğrulama belirtecini kullanarak oturum açın. 

AKS kümenize ilke atama işlemleri için Azure RBAC-kaynak Ilkesi katılımcısı ve sahibi ile AKS yerleşik rollerini kullanın

- [AKS 'de Azure RBAC ve Azure AD kimliklerini kullanarak küme kaynaklarına erişimi denetleme](azure-ad-rbac.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 4.6](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-4-6.md)]

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: erişim denetimini zorlamak için ana bilgisayar tabanlı veri kaybı önleme kullanın

**Rehberlik**: veri tanımlama, sınıflandırma ve kayıp önleme özellikleri, Azure depolama veya işlem kaynakları için henüz kullanılamıyor. Uyumluluk amacıyla gerekliyse üçüncü taraf çözümünü uygulayın.
Microsoft temel platformu yönetir ve tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Kılavuz**: Azure Kubernetes Service (aks) içindeki birimler için belirtilen iki birincil depolama türü, Azure diskleri veya Azure dosyaları tarafından desteklenir. Her iki depolama türü de, güvenliği artırmak için bekleyen verileri şifreleyen Azure Depolama Hizmeti Şifrelemesi (SSE) kullanır. Varsayılan olarak, veriler Microsoft tarafından yönetilen anahtarlarla şifrelenir.

Müşteri tarafından yönetilen anahtarlar kullanılarak bekleyen şifreleme, şifreleme anahtarları üzerinde ek denetim için AKS kümelerindeki hem işletim sistemi hem de veri disklerini şifrelemek için kullanılabilir. Müşteriler, anahtar yedekleme ve döndürme gibi önemli yönetim etkinlikleri için sorumluluğa sahiptir. Diskler Şu anda AKS düğüm düzeyinde Azure disk şifrelemesi kullanılarak şifrelenemez.

- [Azure Kubernetes Service (AKS) içinde depolama ve yedeklemeler için en iyi uygulamalar](operator-best-practices-storage.md)

- [Azure Kubernetes Service (AKS) içindeki Azure diskleriyle kendi anahtarlarınızı (BYOK) getirin](azure-disk-customer-managed-keys.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Kılavuz**: Azure Kubernetes Service (aks) üzerinde barındırılan yönetilen Kubernetes kümelerine dağıtılan kapsayıcı iş yüklerinin performansını izlemek için kapsayıcılar Için Azure izleyicisini kullanın. 

Düğümlerde veya kapsayıcılardaki CPU ve bellek kullanımı, tanımlanan eşikleri aştığında veya altyapı ya da düğümler sistem durumu toplamasında kümede bir sistem durumu değişikliği gerçekleştiğinde öngörülü bildirim veya günlük oluşturma uyarılarını yapılandırın. 

AKS kümelerinizi ve ilgili kaynaklarınızı yüksek düzeyde izlemek için Azure etkinlik günlüğü 'nü kullanın. Özel uyarılar, panolar oluşturmak ve ayrıntılı analiz gerçekleştirmek için sorguları kullanarak düğümlerden ve Kubernetes tarafından toplanan uygulama ve iş yükü ölçümlerini görüntülemek için Prometheus ile tümleştirin.

- [Kapsayıcılar için Azure Izleyicisini anlama](/azure/azure-monitor/insights/container-insights-overview)

- [Kapsayıcılar için Azure Izleyicisini etkinleştirme](/azure/azure-monitor/insights/container-insights-onboard)

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: otomatikleştirilmiş güvenlik açığı tarama araçlarını çalıştırma

**Rehberlik**: Güvenlik Merkezi 'Ni kullanarak güvenlik açıkları Için Azure Kubernetes hizmeti (aks) örnekleri dahil Azure Container Registry izleyin. Güvenlik Merkezi 'nin kayıt defterine gönderilen görüntüleri taramaya hazırlanmaya çalıştığından emin olmak için Güvenlik Merkezi 'nde kapsayıcı kayıt defterleri paketini etkinleştirin.

Güvenlik Merkezi, görüntüyü Qualys kullanarak taradıktan sonra sorunlar bulunduğunda Güvenlik Merkezi panosunda bildirim alın. Kapsayıcı kayıt defterleri paket özelliği, Azure Resource Manager tabanlı kayıt defterlerinde kullanılan görüntülerin güvenlik açıklarına daha derin görünürlük sağlar. 

Her güvenlik açığı için uygulanabilir öneriler için Güvenlik Merkezi 'ni kullanın. Bu öneriler, düzeltme için bir önem sınıflandırması ve kılavuz içerir. 

- [Azure Kubernetes Service (AKS) içindeki kapsayıcı görüntüsü yönetimi ve güvenlik için en iyi uygulamalar](../security-center/defender-for-container-registries-introduction.md)

- [AKS 'de kapsayıcı görüntüsü yönetimi ve güvenlik için en iyi yöntemleri anlama](operator-best-practices-container-image-management.md)

- [Azure Güvenlik Merkezi ile kapsayıcı kayıt defteri tümleştirmesini anlama](../security-center/defender-for-container-registries-introduction.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: otomatik işletim sistemi düzeltme eki yönetimi çözümünü dağıtma

**Rehberlik**: müşterilerin Azure Kubernetes hizmeti (aks) kümelerini korumak için güvenlik güncelleştirmeleri Linux düğümlerine otomatik olarak uygulanır. Bu güncelleştirmeler, işletim sistemi güvenlik düzeltmelerini veya çekirdek güncelleştirmelerini içerir. 

Windows Server düğümleri günlük güncelleştirmeler almamasına gerek kalmadığını Windows Server düğümlerini güncel tutma işleminin Linux çalıştıran düğümlerden farklı olduğunu unutmayın. Bunun yerine, müşteriler, Azure Denetim Masası 'nı veya Azure CLı 'yı kullanarak en son temel pencere sunucu görüntüsü ve düzeltme ekleri ile yeni düğümler dağıtan AKS kümelerindeki Windows Server düğüm havuzlarında bir yükseltme gerçekleştirmeleri gerekir. Bu güncelleştirmeler AKS 'e yönelik güvenlik veya işlevsellik geliştirmelerini içerir.

- [Güncelleştirmelerin Linux çalıştıran AKS küme düğümlerine nasıl uygulandığını anlayın](node-updates-kured.md)

- [Windows Server düğümleri kullanan AKS kümeleri için AKS düğüm havuzunu yükseltme](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#upgrade-a-node-pool)

- [Azure Kubernetes hizmeti (AKS) düğümü görüntü yükseltmeleri](node-image-upgrade.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: üçüncü taraf yazılım başlıkları için otomatik düzeltme eki yönetimi çözümü dağıtma

**Kılavuz**: Azure Kubernetes Service (aks) küme düğümünün üçüncü taraf uygulamalarının, küme ömrü boyunca düzeltme eki olarak kalmasını sağlamak için el ile işlem uygulayın. Bu, otomatik güncelleştirmeleri etkinleştirmek, düğümleri izlemek veya düzenli olarak yeniden başlatmalar gerçekleştirmek için gerekebilir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 5.3](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-5-3.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: geri dönüş güvenlik açığı taramalarını karşılaştırın

**Rehberlik**: Güvenlik Merkezi tarama sonuçlarını tutarlı aralıklarla dışarı aktarın ve bu güvenlik açıklarının düzeltildiğini doğrulamak için sonuçları karşılaştırın.

Güvenlik Merkezi 'nin güvenlik sonrası ve düzeltme güvenlik açığı taraması gerçekleştirmesini sağlamak için uygulamanızı önerdiği güvenlik görevlerinin alınmasını otomatikleştirmek için "Get-AzSecurityTask" PowerShell cmdlet 'ini kullanın.

- [Azure Güvenlik Merkezi tarafından keşfedilen güvenlik açıklarını görüntülemek için PowerShell 'i kullanma](/powershell/module/az.security/get-azsecuritytask)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek için risk derecelendirme işlemi kullanın

**Rehberlik**: Güvenlik Merkezi tarafından sunulan önem derecesini kullanarak güvenlik açıklarının düzeltilme önceliğini belirleyin. 

Yerleşik bir güvenlik açığı değerlendirme aracı (örneğin, Azure tarafından sunulan Qualys veya Rapid7) kullanıyorsanız ortak güvenlik açığı Puanlama sistemi (CVSS) (veya tarama aracınız tarafından sağlanan başka bir puanlama sistemi) kullanın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: abonelikleriniz dahilinde tüm kaynakları (işlem, depolama, ağ vb.) sorgulamak/öğrenmek Için Azure Kaynak grafiğini kullanın. Kiracınızda uygun (okuma) izinlere sahip olduğunuzdan ve aboneliklerinizin içindeki kaynakların yanı sıra tüm Azure aboneliklerinin numaralandırılmasını sağlayabildiğinizden emin olun.

Klasik Azure kaynakları kaynak Graph aracılığıyla bulunabilir, ancak ileriye doğru Azure Resource Manager tabanlı kaynakların oluşturulması ve kullanılması kesinlikle önerilir.

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Rehberlik**: verileri bir taksonomi halinde mantıksal olarak düzenlemek için meta verilerle Azure kaynaklarına Etiketler uygulayın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: varlıkları düzenlemek ve izlemek için uygun yerlerde etiketleme, yönetim grupları ve ayrı abonelikler kullanın. 

Bir Azure Kubernetes hizmeti (AKS) düğüm havuzu oluştururken tatları, etiketleri veya etiketleri uygulayın. Bu düğüm havuzundaki tüm düğümler de o Taint, etiket veya etiketi de alır.

Litre, Etiketler veya Etiketler envanterin düzenli olarak mutabakatını sağlamak ve yetkisiz kaynakların aboneliklerden zamanında silinmesini sağlamak için kullanılabilir.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Ve Kullanıcı etiketleri oluşturma](../azure-resource-manager/management/tag-resources.md)

- [Yönetilen kümeler-güncelleştirme etiketleri](/rest/api/aks/managedclusters/updatetags)

- [Düğüm havuzu için bir taınt, etiket veya etiket belirtme](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#specify-a-taint-label-or-tag-for-a-node-pool)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynakları envanterini tanımlama ve sürdürme

**Rehberlik**: kurumsal iş gereksinimlerine göre işlem kaynakları Için onaylanan Azure kaynakları ve onaylanan yazılım listesini tanımlayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:
-   İzin verilmeyen kaynak türleri 

-   İzin verilen kaynak türleri

Aboneliklerinizde kaynakları sorgulamak/öğrenmek için Azure Kaynak Grafiği ' ni kullanın. Ortamda bulunan tüm Azure kaynaklarının kurumsal iş gereksinimlerine göre onaylandığından emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: işlem kaynakları içindeki onaylanmamış yazılım uygulamaları için izleyici

**Rehberlik**: ortamınızda yüklü olan yazılımları öğrenmek Için Azure Otomasyonu değişiklik izleme ve envanter özelliklerini kullanın. 

Bilgisayarlarınızda yazılım, dosya, Linux Daemon 'ları, Windows Hizmetleri ve Windows kayıt defteri anahtarlarının envanterini toplayın ve görüntüleyin ve onaylanmayan yazılım uygulamalarını izleyin. 

Ortamınızdaki işlem sorunlarını ortaya çıkan ve makinelerinizin durumunu daha iyi anlamak için makinelerinizin yapılandırmasını izleyin.

- [Azure sanal makine envanterini etkinleştirme](../automation/automation-tutorial-installed-software.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Rehberlik**: ortamınızda yüklü olan yazılımları öğrenmek Için Azure Otomasyonu değişiklik izleme ve envanter özelliklerini kullanın. 

Bilgisayarlarınızda yazılım, dosya, Linux Daemon 'ları, Windows Hizmetleri ve Windows kayıt defteri anahtarlarının envanterini toplayın ve görüntüleyin ve onaylanmayan yazılım uygulamalarını izleyin. 

Ortamınızdaki işlem sorunlarını ortaya çıkan ve makinelerinizin durumunu daha iyi anlamak için makinelerinizin yapılandırmasını izleyin.

- [Azure sanal makine envanterini etkinleştirme](../automation/automation-tutorial-installed-software.md)

- [Dosya bütünlüğü Izlemeyi kullanma](../security-center/security-center-file-integrity-monitoring.md)

- [Azure Değişiklik İzleme anlama](../automation/change-tracking/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="68-use-only-approved-applications"></a>6,8: yalnızca onaylanan uygulamaları kullan

**Rehberlik**: ortamınızda yüklü olan yazılımları öğrenmek Için Azure Otomasyonu değişiklik izleme ve envanter özelliklerini kullanın. 

Bilgisayarlarınızda yazılım, dosya, Linux Daemon 'ları, Windows Hizmetleri ve Windows kayıt defteri anahtarlarının envanterini toplayın ve görüntüleyin ve onaylanmayan yazılım uygulamalarını izleyin. 

Ortamınızdaki işlem sorunlarını ortaya çıkan ve makinelerinizin durumunu daha iyi anlamak için makinelerinizin yapılandırmasını izleyin.

Ortamınızdaki mevcut uygulamalar için Güvenlik Merkezi 'nde Uyarlamalı uygulama analizini etkinleştirin.

- [Azure sanal makine envanterini etkinleştirme](../automation/automation-tutorial-installed-software.md)

- [Azure Güvenlik Merkezi Uyarlamalı uygulama denetimlerini kullanma](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:

- İzin verilmeyen kaynak türleri

- İzin verilen kaynak türleri

Aboneliklerinizde kaynakları sorgulamak/öğrenmek için Azure Kaynak Grafiği ' ni kullanın. Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: onaylanan yazılım başlıkları envanterini koruyun

**Rehberlik**: yerleşik ilke tanımları kullanılarak aboneliklerinizde oluşturulabilecek kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak Için Azure koşullu erişimi kullanın.
- [Azure Resource Manager erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: kullanıcıların işlem kaynaklarında betikleri yürütme yeteneğini sınırlayın

**Rehberlik**: Azure Kubernetes Service (aks), normal kullanıcı hesaplarının ve parolaların depolandığı bir kimlik yönetimi çözümü sağlamıyor. Bunun yerine, AKS kümeleriniz için tümleşik kimlik çözümü olarak Azure Active Directory (Azure AD) kullanın.

Kullanıcı veya gruplara, bir ad alanı içindeki Kubernetes kaynaklarına veya Azure AD tümleştirmesini kullanarak küme genelinde erişim izni verin.

AKS yönetim gruplarınızı üye olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü kullanın ve erişimi düzenli olarak mutabık kılmak için kullanın. Yönetilen bir Kubernetes kümesi için ' erişim kimlik bilgileri al gibi işlemler için Azure CLı kullanın. Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini uygulayın.

- [Azure CLı ile AKS 'leri yönetme](/cli/azure/aks)

- [AKS ve Azure AD tümleştirmesini anlama](concepts-identity.md)

- [AKS 'leri Azure AD ile tümleştirme](azure-ad-integration-cli.md)

- [Azure AD 'de PowerShell ile dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure Güvenlik Merkezi ile kimlik ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: yüksek riskli uygulamaları fiziksel olarak veya mantıksal olarak ayırt edin

**Rehberlik**: Azure Kubernetes hizmeti (aks) özelliklerini kullanarak, her bir ekip için gereken kaynaklara göre, en az sayıda ayrıcalık için aynı kümede bulunan takımları ve iş yüklerini mantıksal olarak yalıtın. 

Bir mantıksal yalıtım sınırı oluşturmak için Kubernetes 'te ad alanı uygulayın. Azure Kubernetes Service (AKS) örneklerinizin yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft. ContainerService" ad alanındaki Azure Ilke diğer adlarını kullanın. 

Aşağıdakiler dahil olmak üzere yalıtım ve çoklu kiracı için ek Kubernetes özelliklerini ve konularını gözden geçirin ve uygulayın: zamanlama, ağ, kimlik doğrulama/yetkilendirme ve kapsayıcılar. Ayrıca, geliştirme, test ve üretim için ayrı abonelikler ve yönetim grupları kullanın. AKS kümelerini sanal ağlarla, uygun şekilde etiketlenmiş alt ağlarla ve bir Web uygulaması güvenlik duvarı (WAF) ile güvenliği sağlanmış olarak ayırın.

- [AKS 'de küme yalıtımına yönelik en iyi yöntemler hakkında bilgi edinin](operator-best-practices-cluster-isolation.md)

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [AKS 'de ağ bağlantısı ve güvenlik için en iyi yöntemleri anlama](operator-best-practices-network.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Kılavuz**: Azure Kubernetes Service (aks) örneklerinizin yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak Için "Microsoft. Containerservice" ad alanındaki Azure ilke diğer adlarını kullanın. Yerleşik Azure Ilke tanımlarını kullanın.

AKS için yerleşik ilke tanımlarının örnekleri şunlardır:

- Kubernetes kümesinde HTTPS girişini zorla

- Yetkili IP aralıkları, Kubernetes hizmetlerinde tanımlanmalıdır

- Rol tabanlı Access Control (RBAC), Kubernetes hizmetlerinde kullanılmalıdır

- Kubernetes kümesinde yalnızca izin verilen kapsayıcı görüntülerinin bulunmasını sağla

AKS yapılandırmanızın bir şablonunu Azure Resource Manager JavaScript Nesne Gösterimi (JSON) ' de dışarı aktarın. Bu yapılandırmaların kuruluşunuzun güvenlik gereksinimlerini karşıladığından emin olmak için düzenli aralıklarla gözden geçirin. Azure Güvenlik Merkezi 'nin önerilerini Azure kaynaklarınız için güvenli bir yapılandırma temeli olarak kullanın. 

- [AKS Pod güvenlik ilkelerini yapılandırma ve yönetme](use-pod-security-policies.md)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: güvenli işletim sistemi yapılandırması oluşturma

**Kılavuz**: Azure Kubernetes kümeleri (aks) kümeleri, güvenlik için IYILEŞTIRILMIŞ bir işletim sistemiyle konak sanal makinelere dağıtılır. Ana bilgisayar işletim sisteminde, saldırının yüzey alanını azaltmak ve kapsayıcıların güvenli bir biçimde dağıtımına izin vermek için ek güvenlik sağlamlaştırma adımları vardır. 

Azure, bir yeniden başlatma gerektiren bazı düzeltme eklerine sahip sanal makine konaklarına günlük düzeltme ekleri (güvenlik düzeltme ekleri dahil) uygular. Müşteriler, iş başına sanal makine konağı yeniden başlatmaları gerektiği şekilde zamanlamaktan sorumludur. 

- [AKS aracı düğümü konak işletim sistemi için güvenlik sağlamlaştırma](security-hardened-vm-host-image.md)

- [AKS sanal makine konaklarında güvenlik sağlamlaştırma anlayın](security-hardened-vm-host-image.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Rehberlik**: Pod güvenlik Ilkelerini kullanarak Azure Kubernetes hizmeti (aks) kümenizi güvenli hale getirin.  Kümenizin güvenliğini artırmak için hangi yığınların zamanlanabileceği ile sınırlı olduğunu sınırlayın. 

İzin verilmeyen istek kaynakları AKS kümesinde çalıştırılamaz. 

Ayrıca AKS dağıtımlarınızla ilgili Azure kaynakları (sanal ağlar, alt ağlar, Azure Güvenlik duvarları, depolama hesapları vb.) için güvenli ayarları zorlamak üzere Azure Ilkesi [reddetme] ve [dağıtım yok] etkileri kullanın. 

Aşağıdaki ad alanlarından diğer adları kullanarak özel Azure Ilke tanımları oluşturun: 

- Microsoft. ContainerService

- Microsoft.Network

Ek bilgiler başvurulan bağlantılarda bulunabilir.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: güvenli işletim sistemi yapılandırmalarının bakımını yapma

**Kılavuz**: Azure Kubernetes hizmeti (aks) kümeleri, güvenlik için IYILEŞTIRILMIŞ bir işletim sistemiyle konak sanal makinelere dağıtılır. Ana bilgisayar işletim sisteminde, saldırının yüzey alanını azaltmak ve kapsayıcıların güvenli bir biçimde dağıtımına izin vermek için ek güvenlik sağlamlaştırma adımları vardır. 

Konak işletim sisteminde yerleşik olarak bulunan Internet Security (CIS) denetimlerinin merkezi listesine bakın.  

- [AKS aracı düğümü konak işletim sistemi için güvenlik sağlamlaştırma](security-hardened-vm-host-image.md)

- [AKS kümelerinin durum yapılandırmasını anlama](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

- [AKS sanal makine konaklarında güvenlik sağlamlaştırma anlayın](security-hardened-vm-host-image.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilke tanımları kullanıyorsanız, yapılandırmalardan güvenli bir şekilde depolanması ve yönetilmesi için Azure Repos kullanın. Azure Kubernetes hizmeti (AKS) yapılandırmanızın bir şablonunu Azure Resource Manager JavaScript Nesne Gösterimi (JSON) ' de dışarı aktarın. Yapılandırmaların kuruluşunuzun güvenlik gereksinimlerini karşıladığından emin olmak için düzenli aralıklarla gözden geçirin.

Kubernetes kümesinin kaynaklarını bildiren bir yapılandırma dosyası oluşturmak için Terkform gibi üçüncü taraf çözümleri uygulayın. En iyi güvenlik uygulamalarını uygulayarak ve yapılandırmanızı, güvenli bir konumda kod olarak depolayabilmeniz için AKS dağıtımınızı HaRdA kullanabilirsiniz.

- [Kubernetes kümesi tanımlama](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

- [AKS aracı düğümü konak işletim sistemi için güvenlik sağlamlaştırma](security-hardened-vm-host-image.md)

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: özel işletim sistemi görüntülerini güvenli bir şekilde depolayın

**Kılavuz**: Azure Kubernetes Service (aks) için geçerli değildir. AKS, varsayılan olarak güvenlik için iyileştirilmiş bir ana bilgisayar Işletim sistemi (OS) sağlar. Alternatif veya özel bir işletim sistemi seçmek için geçerli bir seçenek yoktur.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: yerleşik ilke tanımları ve "Microsoft. Containerservice" ad alanındaki Azure ilkesi diğer adları kullanılarak aboneliklerde oluşturulabilecek kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın. 

Sistem yapılandırmasını denetlemek ve zorlamak için özel ilkeler oluşturun. İlke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Diğer adları kullanma](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: işletim sistemleri için yapılandırma yönetimi araçları dağıtma

**Kılavuz**: Azure Kubernetes hizmeti (aks) kümeleri, güvenlik için IYILEŞTIRILMIŞ bir işletim sistemiyle konak sanal makinelere dağıtılır. Ana bilgisayar işletim sisteminde, saldırının yüzey alanını azaltmak ve kapsayıcıların güvenli bir biçimde dağıtımına izin vermek için ek güvenlik sağlamlaştırma adımları vardır. 

AKS konaklarında yerleşik olarak bulunan Internet güvenliği (CIS) denetimlerinin merkezi listesine bakın.  

- [AKS aracı düğümü konak işletim sistemi için güvenlik sağlamlaştırma](security-hardened-vm-host-image.md)

- [AKS sanal makine konaklarında güvenlik sağlamlaştırma anlayın](security-hardened-vm-host-image.md)

- [AKS kümelerinin durum yapılandırmasını anlama](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Kılavuz**: Azure Kubernetes Service (aks) dağıtımlarınızla ilgili kaynaklar için temel taramalar gerçekleştirmek üzere güvenlik merkezini kullanın. Örnek kaynaklar, AKS kümesinin kendisiyle sınırlı değildir, aks kümesinin dağıtıldığı sanal ağ, Terrayform durumunu izlemek için kullanılan Azure depolama hesabı veya AKS kümenizin işletim sistemi ve veri diskleri için şifreleme anahtarları için kullanılan Azure Key Vault örneklerini içerir.

- [Azure Güvenlik Merkezi 'nde öneriler nasıl düzeltileceği](../security-center/security-center-remediate-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: işletim sistemleri için otomatik yapılandırma izlemeyi Uygula

**Kılavuz**: &amp; Azure Kubernetes SERVICE (aks) kümeleriniz için temel taramalar gerçekleştirmek üzere "işlem uygulamaları" bölümünde güvenlik merkezi kapsayıcı önerilerini kullanın. 

Yapılandırma sorunları veya güvenlik açıkları bulunduğunda Güvenlik Merkezi panosunda bildirim alın. Bu, güvenlik merkezi 'nin görüntüyü taramasını sağlayan isteğe bağlı kapsayıcı kayıt defterleri paketinin etkinleştirilmesini gerektirir.  

- [Azure Güvenlik Merkezi'ndeki kapsayıcı önerilerini anlama](../security-center/container-security.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: flexvolume drive kullanarak Azure Key Vault Azure Kubernetes Service (aks) kümesiyle tümleştirin. Kimlik bilgileri, depolama hesabı anahtarları veya sertifikalar gibi gizli dizileri depolamak ve düzenli olarak döndürmek için Azure Key Vault kullanın. FlexVolume sürücüsü, AKS kümesinin Key Vault 'ten yerel olarak kimlik bilgilerini almasına ve bunları yalnızca istenen pod için güvenli bir şekilde sağlamasına imkan tanır. Key Vault erişim istemek ve FlexVolume sürücüsü aracılığıyla gerekli kimlik bilgilerini almak için pod tarafından yönetilen bir kimlik kullanın. Key Vault geçici silmenin etkinleştirildiğinden emin olun. 

Uygulama kodunuzda kimlik bilgileri tanımlamadan kimlik bilgisi pozlamasını sınırlayın. 

Sabit veya paylaşılan kimlik bilgilerinin kullanılmasını önleyin. 

- [Azure Kubernetes Service’teki (AKS) uygulamalar ve kümeler için güvenlik kavramları](concepts-security.md)

- [AKS kümeniz ile Key Vault kullanma](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Kılavuz**: güvenlik açısından en iyi yöntem olarak uygulama kodunuzda kimlik bilgilerini tanımlamayın. Azure Key Vault de dahil olmak üzere, Azure 'daki herhangi bir hizmette bir pod 'ın kimliğini doğrulamak için Azure kaynakları için Yönetilen kimlikler kullanın. Pod 'a Azure Active Directory (Azure AD) kimlik doğrulaması için bir Azure kimliği atanır ve pod 'ın hizmete erişme yetkisi olup olmadığını kontrol eden ve gerekli eylemleri gerçekleştiren diğer Azure hizmetlerine sunulabilen bir dijital belirteç alır.

Pod tarafından yönetilen kimliklerin yalnızca Linux Pod ve kapsayıcı görüntüleri ile kullanılması amaçlandığını unutmayın. Dijital anahtarları ve kimlik bilgilerini depolamak ve almak için Azure Key Vault sağlayın. İşletim sistemi disklerini şifrelemek için kullanılanlar gibi anahtarlar, AKS kümesi verileri Azure Key Vault içinde depolanabilir.

Hizmet sorumluları, AKS kümelerinde da kullanılabilir. Ancak, hizmet sorumlularını kullanan kümeler sonunda kümenin çalışmasını sağlamak için hizmet sorumlusunun yenilenmesi gereken bir duruma ulaşabilir. Hizmet sorumlularını yönetmek karmaşıklık ekler ve bunun yerine yönetilen kimliklerin kullanılması daha kolay olur. Aynı izin gereksinimleri hem hizmet sorumluları hem de yönetilen kimlikler için geçerlidir.

- [Azure Kubernetes Service (AKS) ile yönetilen kimlikleri ve Key Vault anlayın](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

- [Azure AD Pod kimliği](https://github.com/Azure/aad-pod-identity)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: kod içinde kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayın. Kimlik bilgisi tarayıcısı Ayrıca bulunan kimlik bilgilerinin önerilerle Azure Key Vault gibi daha güvenli konumlara taşınmasını da önerir.

Uygulama kodunuzda kimlik bilgileri tanımlamadan kimlik bilgisi pozlamasını sınırlayın. ve paylaşılan kimlik bilgilerinin kullanılmasını önleyin. Azure Key Vault, dijital anahtarları ve kimlik bilgilerini depolamak ve almak için kullanılmalıdır. Pod 'nizin diğer kaynaklara erişmesine izin vermek için Azure kaynakları için Yönetilen kimlikler kullanın. 

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Pod güvenliği için geliştirici en iyi uygulamaları](developer-best-practices-pod-security.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: merkezi olarak yönetilen kötü amaçlı yazılımdan koruma yazılımı kullanma

**Rehberlik**: aks, aracı düğümlerinin yaşam döngüsünü ve işlemlerini sizin adınıza yönetmektedir. aracı düğümleriyle Ilişkili IaaS kaynaklarını değiştirme desteklenmez. Ancak, Linux düğümleri için, kötü amaçlı yazılımdan koruma çözümü gibi özel yazılımları yüklemek için Daemon kümelerini kullanabilirsiniz.

- [Güvenlik uyarıları başvuru kılavuzu](../security-center/alerts-reference.md)

- [Kapsayıcılar için uyarılar-Azure Kubernetes hizmet kümeleri](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [AKS paylaşılan sorumluluk ve Daemon kümeleri](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: işlem dışı Azure kaynaklarına yüklenecek dosyaları önceden Tara

**Rehberlik**: aks kaynaklarınıza yüklenen dosyaları önceden tarayın. Veri deposu olarak bir Azure depolama hesabı kullanıyorsanız veya AKS kümeniz için Teresform durumunu izlemek üzere, depolama hesaplarına yüklenen kötü amaçlı yazılımları algılamak için, güvenlik merkezi 'nin veri Hizmetleri tehdit algılamasını kullanın. 

- [Azure Güvenlik Merkezi 'nin veri Hizmetleri tehdit algılamasını anlama](../security-center/azure-defender.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: kötü amaçlı yazılımdan koruma yazılımlarının ve imzaların güncelleştirildiğinden emin olun

**Rehberlik**: aks, aracı düğümlerinin yaşam döngüsünü ve işlemlerini sizin adınıza yönetmektedir. aracı düğümleriyle Ilişkili IaaS kaynaklarını değiştirme desteklenmez. Ancak, Linux düğümleri için, kötü amaçlı yazılımdan koruma çözümü gibi özel yazılımları yüklemek için Daemon kümelerini kullanabilirsiniz.

- [Güvenlik uyarıları başvuru kılavuzu](../security-center/alerts-reference.md)

- [Kapsayıcılar için uyarılar-Azure Kubernetes hizmet kümeleri](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [AKS paylaşılan sorumluluk ve Daemon kümeleri](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli Otomatik yedeklemeli UPS sağlayın

**Kılavuz**: Velero gibi depolama türü için uygun bir aracı kullanarak verilerinizi yedekleyin. Bu, ek küme kaynakları ve yapılandırmalarının yanı sıra kalıcı birimleri yedeklebilirler. Düzenli aralıklarla, bu yedeklemelerin bütünlüğünü ve güvenliğini doğrulayın. 

Yedeklemeden önce uygulamalarınızdan durumu kaldırın. Bunun yapılabileceği durumlarda, verileri kalıcı birimlerden yedekleyin ve veri bütünlüğünü ve gerekli işlemleri doğrulamak için geri yükleme işlemlerini düzenli olarak test edin.

- [AKS 'de depolama ve yedeklemeler için en iyi uygulamalar](operator-best-practices-storage.md)

- [AKS 'de iş sürekliliği ve olağanüstü durum kurtarma için en iyi uygulamalar](operator-best-practices-multi-region.md)

- [Azure Site Recovery anlayın](../site-recovery/site-recovery-overview.md)

- [Azure 'da Velero kurulumu](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Kılavuz**: Velero gibi depolama türü için uygun bir aracı kullanarak verilerinizi yedekleyin. Bu, ek küme kaynakları ve yapılandırmalarının yanı sıra kalıcı birimleri yedeklebilirler. 

PowerShell komutlarıyla Key Vault sertifikaların, anahtarların, yönetilen depolama hesaplarının ve parolaların düzenli otomatik yedeklemelerini gerçekleştirin. 

- [Key Vault sertifikalarını yedekleme](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Key Vault anahtarlarını yedekleme](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Key Vault yönetilen depolama hesaplarını yedekleme](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Key Vault gizli dizileri yedekleme](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Azure Backup etkinleştirme](/azure/backup/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: Velero Backup içindeki içeriğin düzenli aralıklarla veri geri yüklemesini gerçekleştirir. Gerekirse, yalıtılmış bir sanal ağa geri yükleme testi yapın.

PowerShell komutlarıyla Key Vault sertifikaların, anahtarların, yönetilen depolama hesaplarının ve parolaların düzenli aralıklarla veri geri yüklemesini gerçekleştirin.

- [Key Vault sertifikalarını geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Key Vault anahtarlarını geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Key Vault yönetilen depolama hesaplarını geri yükleme](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault parolaları geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [Azure sanal makine yedeklemesinden dosyaları kurtarma](/azure/backup/backup-azure-restore-files-from-vm)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Kılavuz**: Velero gibi depolama türü için uygun bir aracı kullanarak verilerinizi yedekleyin. Bu, ek küme kaynakları ve yapılandırmalarının yanı sıra kalıcı birimleri yedeklebilirler. 

Azure Kubernetes Service (AKS) dağıtımları için ile birlikte kullanılıyorsa, anahtarları yanlışlıkla veya kötü amaçlı Azure Key Vault silmeye karşı korumak için Key Vault Soft-Delete etkinleştirin.

- [Azure Depolama Hizmeti Şifrelemesi anlama](../storage/common/storage-service-encryption.md)

- [Key Vault Soft-Delete etkinleştirme](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

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

**Rehberlik**: Güvenlik Merkezi 'nin uyarılara önem derecesine sahip olan ilk olarak araştırılması gereken uyarıları önceliklendirin. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizte ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır.
Abonelikleri açık bir şekilde işaretleyin (örneğin, üretim, üretim dışı) ve Azure kaynaklarını net bir şekilde tanımlamak ve kategorilere ayırmak için bir adlandırma sistemi oluşturun.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve boşlukları belirleyip olay yanıt planlarını gerektiği şekilde düzeltin.

- [BT planları ve özellikleri için test, eğitim ve alıştırma programları Kılavuzu](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: Microsoft Güvenlik Yanıt MERKEZI (MSRC), müşterinin verilerine izinsiz veya yetkisiz bir taraf tarafından erişildiğini belirlerse, Microsoft tarafından sizinle iletişim kurmak için güvenlik olayı iletişim bilgileri kullanılacaktır. 

Sorunların çözümlendiğinden emin olmak için, olgusunun ardından olayları gözden geçirin.

- [Azure Güvenlik Merkezi güvenlik Ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: güvenlik uyarılarını olay yanıt sisteminizle birleştirme

**Rehberlik**: Güvenlik Merkezi uyarılarını ve önerilerini, sürekli dışa aktarma özelliğini kullanarak dışarı aktarın. Sürekli dışa aktarma, uyarıları ve önerileri el ile veya devam eden sürekli bir biçimde dışa aktarmanız sağlar. 

Uyarıları Azure Sentinel 'e, gerek başına ve kurumsal iş gereksinimlerine bağlı olarak akışa almak için Güvenlik Merkezi veri bağlayıcısını seçin.

- [Sürekli dışarı aktarmayı yapılandırma](../security-center/continuous-export.md)

- [Uyarıların Azure Sentinel’e akışını yapma](../sentinel/connect-azure-security-center.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: güvenlik uyarılarına yanıtı otomatikleştirme

**Yönergeler**: güvenlik uyarıları ve önerilerinde "Logic Apps" aracılığıyla yanıtları otomatik olarak tetiklemek Için Güvenlik Merkezi 'Ndeki Iş akışı Otomasyonu özelliğini kullanın.

- [Iş akışı otomasyonu ve Logic Apps yapılandırma](../security-center/workflow-automation.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="penetration-tests-and-red-team-exercises"></a>Sızma Testleri ve Red Team Alıştırmaları

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: Penetme testleri ve Red ekibi alıştırmaları](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Azure kaynaklarınızın düzenli olarak sızma testini gerçekleştirin ve tüm kritik güvenlik bulgularını düzeltmeye dikkat edin

**Rehberlik**: sızma testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak Için Microsoft katılım kurallarını izleyin. Microsoft 'un, başvurulan bağlantılarda Microsoft tarafından yönetilen bulut altyapısına, hizmetlerine ve uygulamalarına yönelik kırmızı ekip oluşturma ve canlı site sızma testi stratejisi ve yürütmesi hakkında ek bilgiler.

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](/azure/security/benchmarks/overview)
- [Azure güvenlik temelleri](/azure/security/benchmarks/security-baselines-overview) hakkında daha fazla bilgi edinin
