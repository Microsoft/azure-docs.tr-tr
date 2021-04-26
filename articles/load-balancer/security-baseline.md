---
title: Azure Load Balancer için Azure Güvenlik temeli
description: Azure Load Balancer güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 64e0a8cfcaf00c55038fbe5d1cdc7423b681b690
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589270"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Azure Load Balancer için Azure Güvenlik temeli

Bu güvenlik temeli, Microsoft Azure Load Balancer [Azure Güvenlik kıyaslama sürümü 1,0](../security/benchmarks/overview-v1.md) ' dan kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar.
İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure Load Balancer için geçerli olan ilgili kılavuza göre gruplandırılır. Azure Load Balancer için geçerli olmayan **denetimler** dışlandı.

 
Azure Load Balancer Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Azure Load Balancer güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: yalnızca belirli sanal ağlardan veya eşlenmiş sanal ağlardan internet 'e maruz kalmadan arka uç kaynaklarına giden trafiğe izin vermek Için Iç Azure Yük dengeleyicileri kullanın. Kaynak ağ ile dış Load Balancer uygulama

Doğrudan Internet 'in etkilenme koruması için arka uç kaynaklarının IP adreslerini geçici olarak çözmek için adres çevirisi (SNAT).

Azure, standart ve temel olmak üzere iki tür Load Balancer sunumu sunmaktadır. Tüm üretim iş yükleri için Standart Load Balancer kullanın. Ağ güvenlik gruplarını uygulayın ve yalnızca uygulamanızın güvenilen bağlantı noktalarına ve IP adresi aralıklarına erişime izin verin. Arka uç alt ağına veya arka uç sanal makinelerinin NIC 'sine atanmış bir ağ güvenlik grubu olmadığı durumlarda, trafiğe yük dengeleyiciden bu kaynaklarla izin verilmez. Standart yük dengeleyiciler ile, bir ağ güvenlik grubuyla giden NAT tanımlamak için giden kuralları sağlayın. Giden bağlantılarınızın davranışını ayarlamak için bu giden kuralları gözden geçirin.

Üretim iş yükleriniz için Standart Load Balancer kullanılması önerilir ve genellikle temel Load Balancer yalnızca test için kullanılır çünkü temel tür varsayılan olarak internet 'ten gelen bağlantılara açıktır ve işlem için ağ güvenlik grupları gerektirmez.

- [Azure’da giden bağlantılar](load-balancer-outbound-connections.md)

- [Azure genel Load Balancer yükseltme](upgrade-basic-standard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: sanal ağların, alt ağların ve NIC 'lerin yapılandırmasını ve trafiğini izleyin ve günlüğe kaydedin

**Rehberlik**: Load Balancer, arka uç kaynaklarına uygulanan ağ güvenlik grupları kurallarını ve internet erişimini denetlemek için yapılandırılmış giden kuralları temel aldığından bir geçiş hizmetidir.

Load Balancer giden kuralları dikey penceresinden Standart Load Balancer için yapılandırılan giden kuralları ve örtük giden kuralların etkin olduğu yük dengeleme kuralları dikey penceresini gözden geçirin.

Kaynaklarınızın internet 'e ne sıklıkta ulaşacağını izlemek için giden bağlantılarınızın sayısını izleyin. 

Güvenlik Merkezi 'ni kullanın ve Azure ağ kaynaklarınızın güvenliğini sağlamaya yardımcı olmak için ağ koruma önerilerini izleyin.

Arka uç kaynaklarınız için güvenlik önerilerini izleyin ve ağ güvenlik grubu akış günlüklerini etkinleştirin ve günlükleri denetim için bir Azure depolama hesabına gönderin.

Ayrıca, akış günlüklerini bir Log Analytics çalışma alanına gönderin ve ardından Azure bulutunuzda trafik desenlerine yönelik Öngörüler sağlamak için Trafik Analizi kullanın. Trafik Analizi avantajları arasında ağ etkinliğini görselleştirme, sık kullanılan noktaları ve güvenlik tehditlerini anlama, trafik akışı desenlerini anlama ve ağ yapılandırmalarını sağlama yeteneği dahildir.

- [Ağ güvenlik grubu akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

- [Giden bağlantı istatistiklerimi denetlemek Nasıl yaparım?](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1,3: kritik Web uygulamalarını koruma

**Rehberlik**: Web uygulamalarınızı korumak için Microsoft 'un tehdit bilgilerini kullanmak üzere Load Balancer ile giden kurallar ve ağ güvenlik grupları aracılığıyla internet bağlantısı ve geçerli kaynak IP 'leri açıkça tanımlayın.

- [Azure Güvenlik duvarını tümleştirme](../firewall/integrate-lb.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: DDoS saldırılarına karşı koruma sağlamak Için Azure sanal ağınızda Azure dağıtılmış hizmet reddi (DDoS) standart korumasını etkinleştirin. 

Tehdit zekası tabanlı filtreleme etkin ve kötü amaçlı ağ trafiği için "uyarı ve reddetme" olarak yapılandırılmış bir kuruluşun ağ sınırlarının her birinde Azure Güvenlik Duvarı 'Nı dağıtın.

 

Bilinen kötü amaçlı IP adresleriyle iletişimleri algılamak için Güvenlik Merkezi tehdit koruması 'nı kullanın. 

Standart Load Balancer, varsayılan olarak ve özel ve yalıtılmış bir sanal ağın bir parçası olarak güvenli olacak şekilde tasarlanmıştır. İzin verilen trafiğe açıkça izin vermek ve bilinen kötü amaçlı IP adreslerine izin vermemek için ağ güvenlik grupları tarafından açılmadığı müddetçe gelen akışlara kapalıdır. Sanal makine kaynağınızın bir alt ağda veya NIC 'inde bir ağ güvenlik grubu Load Balancer arkasında yoksa, trafiğin bu kaynağa erişmesine izin verilmez. 

Tehdit zekası tabanlı filtreleme etkin ve kötü amaçlı IP adreslerinden gelen saldırıları engellemek için kötü amaçlı ağ trafiği için "uyarı ve reddetme" olarak yapılandırılmış bir kuruluşun ağ sınırlarının her birinde Azure Güvenlik Duvarı 'Nı dağıtın. 

Load Balancer Azure Güvenlik duvarını tümleştirme kılavuzunu uygulayın.

Bilinen kötü amaçlı IP adresleriyle iletişimleri algılamak için Güvenlik Merkezi tehdit koruması özelliklerini kullanın. 

Güvenlik Merkezi (Standart katman) tam zamanında sanal makine erişimi sağlar ve izin verilen kaynak IP adreslerini yalnızca onaylanan IP adreslerinden/aralıklarından erişime izin verecek şekilde yapılandırır.
 

Bağlantı noktalarını ve kaynak IP 'Leri gerçek trafik ve tehdit zeka göre sınırlayan ağ güvenlik grubu yapılandırmalarının kullanılması için Güvenlik Merkezi 'nin Uyarlamalı ağ sağlamlaştırma özelliğini kullanın.
 

- [Azure portal kullanarak Azure DDoS koruması standardını yönetme](../ddos-protection/manage-ddos-protection.md)

- [Azure Güvenlik Duvarı tehdit zekası tabanlı filtreleme](../firewall/threat-intel.md)

- [Azure Güvenlik Merkezi’nde tehdit koruması](../security-center/azure-defender.md)

- [Tam zamanında erişim ile yönetim bağlantı noktalarınızın güvenliğini sağlama](../security-center/security-center-just-in-time.md)

- [Azure Güvenlik Merkezi 'nde Uyarlamalı ağ sağlamlaştırma](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Güvenlik duvarını Load Balancer tümleştirme](../firewall/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Rehberlik**: anormal etkinlikleri araştırmak Için Ağ İzleyicisi paket yakalamayı etkinleştirin.

- [Ağ Izleyicisi örneği oluşturma](../network-watcher/network-watcher-create.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Kılavuz**: Azure Marketi 'nden, yük İnceleme ÖZELLIKLERI olan kimlik/ıp 'leri Load Balancer ortamınıza yönelik bir teklif uygulayın. 

Yük incelemesi bir gereksinim değilse Azure Güvenlik Duvarı tehdit zekasını kullanın. Azure Güvenlik Duvarı tehdit zekası tabanlı filtreleme, bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen ve giden trafiği filtrelemek ve/veya engellemek için kullanılır. IP adresleri ve etki alanları, Microsoft Tehdit Analizi akışından alınır.

Kötü amaçlı trafiği algılamak ve/veya engellemek için kuruluşunuzun ağ sınırlarının her birinde tercih ettiğiniz güvenlik duvarı çözümünü dağıtın.

- [Azure Market](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Güvenlik Duvarı ile uyarıları yapılandırma](../firewall/threat-intel.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Web uygulamalarına trafiği yönetme

**Rehberlik**: Web uygulamalarınızı korumak için Microsoft 'un tehdit bilgileri özelliklerini kullanmak üzere Load Balancer ile giden kurallar ve ağ güvenlik grupları aracılığıyla internet bağlantısı ve geçerli kaynak IP 'leri açıkça tanımlayın.

- [Azure Güvenlik duvarını tümleştirme](../firewall/integrate-lb.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Rehberlik**: güvenlik kuralları oluştururken belirli IP adreslerinin yerine hizmet etiketleri kullanın. Karşılık gelen hizmet için trafiğe izin vermek veya reddetmek üzere bir kuralın kaynak veya hedef alanında hizmet etiketi adını belirtin. 

Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir. 

Varsayılan olarak, her ağ güvenlik grubu, durum araştırma trafiğine izin vermek için AzureLoadBalancer hizmet etiketini içerir. 

Ağ güvenlik grubu kurallarında kullanılabilecek tüm hizmet etiketleri için Azure belgelerine bakın.

- [Kullanılabilir hizmet etiketleri](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Kılavuz**: Azure ilkesiyle ağ kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın.

Tek bir şema tanımında Azure Resources Manager şablonları, Azure RBAC denetimleri ve ilkeleri gibi temel ortam yapıtlarını paketleyerek büyük ölçekli Azure dağıtımlarını basitleştirmek için Azure şemaları 'nı kullanın. 

Şema ' i yeni aboneliklere uygulayın ve sürüm oluşturma aracılığıyla denetim ve yönetime ince ayar yapın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Ağ için Azure Ilke örnekleri](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: ağ güvenlik grupları ve ağ güvenliği ve trafik akışıyla ilgili diğer kaynaklar için kaynak etiketleri kullanın. 

Tek tek ağ güvenlik grubu kuralları için bir ağa/ağdan giden trafiğe izin veren kuralları belgelemek için "Açıklama" alanını kullanın.

Etiketlemeyle ilgili yerleşik Azure Ilke tanımlarından herhangi birini ("etiket gerektir ve değerini ıste" gibi), tüm kaynakların etiketlerle oluşturulmasını ve var olan etiketlenmemiş kaynakları bilgilendirmesini sağlar.

Azure PowerShell veya Azure CLı kullanarak, etiketlerine göre kaynaklar üzerinde eylem arayabilir veya eylemleri gerçekleştirin.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Azure sanal ağı oluşturma](../virtual-network/quick-create-portal.md)

- [Ağ güvenlik grubu kuralları ile ağ trafiğini filtreleme](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Kılavuz**: Azure etkinlik günlüğü 'nü kullanarak kaynak yapılandırmalarının yanı sıra Azure kaynaklarınızda yapılan değişiklikleri tespit edin. 

Kritik kaynaklar değiştirildiğinde sizi bilgilendirmek için Azure Izleyici 'de uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: aboneliklerinizde etkinlik günlüğünü görüntüleyerek yük dengeleyicileri için giden kurallarınıza ve ağ güvenlik gruplarına yapılan değişiklikleri gözden geçirin. 

Arka uç kaynaklarınızın güvende olduğundan emin olmak için iç konak günlüklerini görüntüleyin.

Bu günlükleri Log Analytics veya başka bir depolama platformuna dışarı aktarın. Azure Izleyici 'de analiz sorgulamak ve gerçekleştirmek için Log Analytics çalışma alanlarını kullanın ve uzun süreli ve arşiv depolama için Azure depolama hesaplarını kullanın.

Kurumsal iş gereksinimlerinize bağlı olarak bu verileri Azure Sentinel 'e veya üçüncü taraf bir SıEM 'e etkinleştirin ve yerleşik olarak yapın.

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Izleyici ile Azure sanal makine iç konak günlüklerini toplama](../azure-monitor/vm/quick-collect-azurevm.md)

- [Azure Izleyici ve üçüncü taraf SıEM tümleştirmesi ile çalışmaya başlama](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Platform etkinlik günlükleri](../azure-monitor/essentials/activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Kılavuz**: denetim ve yönetim düzlemi günlük kaydını ve temel Load Balancer etkinlik günlükleriyle yakalanan denetim bilgilerini gözden geçirin. Bu yakalama ayarları varsayılan olarak etkindir. 

Tüm etkinlikleri ve bunların durumunu görüntülemek için kaynakların eylemlerini izlemek üzere etkinlik günlüklerini kullanın. 

Aboneliğinizdeki kaynaklar üzerinde etkinlik günlükleriyle hangi işlemlerin alındığını belirleme: 

- işlemi kim başlattı
- işlem oluştuğunda
- işlemin durumu

- işlemi araştırmanıza yardımcı olabilecek diğer özelliklerin değerleri

Azure PowerShell, Azure komut satırı arabirimi (CLı), Azure REST API veya Azure portal aracılığıyla etkinlik günlüğünden bilgi alın. 

Azure Izleyici ile Standart Load Balancer yapılandırma özellikleri için çok boyutlu tanılama uygulayın.  Bunlar, kaynak ağ adresi çevirisi (SNAT) bağlantıları, bağlantı noktaları hakkındaki bilgileri içeren güvenlik için kullanılabilir ölçümleri içerir. SYN (Synchronize) paketleri ve paket sayaçlarındaki ek ölçümler de mevcuttur. 

Çok boyutlu ölçümleri API 'Ler aracılığıyla programlı bir şekilde alın ve ' tüm ölçümler ' seçeneği aracılığıyla bunları bir depolama hesabına yazın.

Verilerinizi önceden yapılandırılmış panolar ile analiz etmek veya gereksinimlerinizi temel alarak görünümleri özelleştirmek için Microsoft Power BI ile Azure denetim günlükleri içerik paketini kullanın.

Günlükleri bir olay hub 'ına veya Log Analytics çalışma alanına akış. Bunlar ayrıca Azure Blob depolamadan ayıklanırlar ve Excel ve Power BI gibi farklı araçlarla görüntülenebilir. 

İş gereksinimlerinize bağlı olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'e verileri etkinleştirin.

- [Kaynak Yöneticisi ile denetim işlemlerinde ayrıntılı olarak açıklanan her yöntem için adım adım yönergeler ile bu makaleyi gözden geçirin.](../azure-resource-manager/management/view-activity-logs.md)

- [Genel Temel Yük Dengeleyici için Azure İzleyici günlükleri](load-balancer-monitor-log.md)

- [Kaynaklardaki eylemleri izlemek için etkinlik günlüklerini görüntüleme](../azure-resource-manager/management/view-activity-logs.md)

- [API 'Ler aracılığıyla çok boyutlu ölçümleri program aracılığıyla alma](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Azure Izleyici ve üçüncü taraf SıEM tümleştirmesi ile çalışmaya başlama](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Rehberlik**: etkinlik günlüğü varsayılan olarak etkindir ve Azure 'un olay günlükleri deposunda 90 gün boyunca korunur. Log Analytics çalışma alanı saklama dönemini, kuruluşunuzun Azure Izleyici 'deki uyumluluk düzenlemelerine göre ayarlayın. Uzun süreli ve arşiv depolama için Azure depolama hesaplarını kullanın.

- [Kaynaklardaki eylemleri izlemek için etkinlik günlüklerini görüntüleme makalesi](../azure-resource-manager/management/view-activity-logs.md)

- [Log Analytics veri saklama süresini değiştirme](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Azure depolama hesabı günlükleri için bekletme ilkesini yapılandırma](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Rehberlik**: Azure Portal ve Azure izleyici altındaki Kaynak Durumu sayfasındaki Load Balancer sayfasını kullanarak standart Load Balancer kaynakları izleyin, yönetin ve sorun giderin. Güvenlik için kullanılabilir ölçümler, kaynak ağ adresi çevirisi (SNAT) bağlantıları, bağlantı noktaları hakkında bilgi içerir. Ayrıca, SYN (Synchronize) paketleri ve paket sayaçlarındaki ölçümler de mevcuttur. 

Standart, dış ve dahili yük dengeleyiciler için çok boyutlu ölçümler ile Endpoint Health araştırma durumunu gözden geçirmek üzere Azure Izleyici 'yi kullanın. Bu ölçümleri API 'Ler aracılığıyla programlı bir şekilde toplayın ve ' tüm ölçümler ' seçeneği aracılığıyla bir depolama hesabına yazılır.

Azure Izleyici günlükleri, Iç temel yük dengeleyiciler için kullanılamaz. 

Arka uç havuzunuzdaki örnek sayısı, sistem durumu araştırma hatalarından dolayı Load Balancer istek almama gibi temel dış Load Balancer için arka uç havuzu başına özetlenen durum araştırma durumunu görmek üzere Azure Izleyici 'yi kullanın. 

Yük Dengeleyici Sistem durumu hakkında istatistikler sağlamak için Azure operasyonel içgörüler ile günlüğe kaydetme uygulayın. 

Etkinlik günlüklerini kullanarak uyarıları görüntüleyin ve kaynakları ve bunların durumlarını Azure aboneliklerinizde izler. Etkinlik günlükleri varsayılan olarak etkindir ve Azure portal görüntülenebilir. 

Azure denetim günlükleri içerik paketi ile Microsoft Power BI kullanın ve verilerinizi önceden yapılandırılmış panolar ile çözümleyin. Görünümleri iş gereksinimine göre gereksinimlerinize uyacak şekilde özelleştirin. 

Günlükleri bir olay hub 'ına veya Log Analytics çalışma alanına akış. Bunlar ayrıca Azure Blob depolamadan ayıklanırlar ve Excel ve Power BI gibi farklı araçlarla görüntülenebilir. Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz.

- [Load Balancer durum yoklamaları](load-balancer-custom-probe-overview.md)

- [Azure İzleyici REST API'si](/rest/api/monitor)

- [REST API aracılığıyla ölçümleri alma](/rest/api/monitor/metrics/list)

- [Ölçümler, uyarılar ve kaynak sistem durumu ile tanılamayı Standart Load Balancer](load-balancer-standard-diagnostics.md)

- [Genel Temel Yük Dengeleyici için Azure İzleyici günlükleri](load-balancer-monitor-log.md)

- [Yük dengeleyici ölçülerinizi Azure portal görüntüleyin](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Rehberlik**: Güvenlik Merkezi 'ni, güvenlik günlükleri ve olaylarında Load Balancer ilgili anormal etkinlikleri izlemek ve uyarmak için Log Analytics çalışma alanıyla birlikte kullanın.

Azure Sentinel veya bir üçüncü taraf SıEM aracında verileri etkinleştirin ve yerleşik olarak etkinleştirin.

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Güvenlik Merkezi 'nde uyarıları yönetme](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics günlük verilerinde uyarı alma](../azure-monitor/alerts/tutorial-response.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Kılavuz**: Azure rol tabanlı erişim denetimi (Azure RBAC), rol atamaları aracılığıyla Load Balancer gibi Azure kaynaklarına erişimi yönetmenizi sağlar. Bu rolleri kullanıcılara, gruplar hizmet sorumlularına ve yönetilen kimliklere atayın.

Azure CLı, Azure PowerShell veya Azure portal gibi araçlarla belirli kaynaklar için önceden tanımlı ve yerleşik roller.

- [PowerShell ile Azure Active Directory (Azure AD) içinde dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin ve Güvenlik Merkezi 'nin kimlik ve erişim yönetimi önerilerini izleyin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: tüm yönetim görevleri için adanmış makineler (ayrıcalıklı erişim Iş Istasyonları) kullanın

**Kılavuz**: Azure ağ kaynaklarını yönetmek ve erişmek için yapılandırılmış çok faktörlü kimlik doğrulaması Ile ayrıcalıklı erişim iş istasyonları (Paw) kullanın. 

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](/security/compass/privileged-access-devices)

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

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

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapmasından uyar

**Rehberlik**: Kullanıcı kimlikleriyle ilgili şüpheli eylemleri algılanan otomatik yanıtları yapılandırmak için Azure Active Directory (Azure AD) risk ve kimlik koruması özelliklerini kullanın. Daha fazla araştırma için Azure Sentinel 'e veri alma.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Kılavuz**: Azure kaynaklarınıza bağlanan tüm istemcilerin TLS 1,2 veya üzeri bir şekilde anlaşabilebileceğine emin olun.

Azure Güvenlik Merkezi önerilerini, varsa, bekleyen ve geçişte şifreleme için kullanın.

- [Azure ile iletim sırasında şifrelemeyi anlama](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: kaynaklara erişimi yönetmek için Azure RBAC kullanma

**Rehberlik**: Load Balancer kaynaklarınıza erişimi denetlemek IÇIN Azure RBAC kullanın.

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: erişim denetimini zorlamak için ana bilgisayar tabanlı veri kaybı önleme kullanın

**Rehberlik**: Load Balancer, müşteri verilerini depolamamayan bir geçiş hizmetidir. Bu, Microsoft tarafından yönetilen temel platformun bir parçasıdır. 

Microsoft, tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. 

Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar. 

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Rehberlik**: önemli üretim iş yükleri Için kullanılan yük dengeleyiciler gibi kritik Azure kaynaklarında değişiklik yapıldığında uyarı oluşturmak Için Azure etkinlik günlüğü Ile Azure izleyici 'yi kullanın.

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: aboneliklerinizde tüm kaynakları sorgulamak ve (işlem, depolama, ağ, bağlantı noktaları, protokoller vb.) sorgulamak Için Azure Kaynak grafiğini kullanın. Geçerli kaynakların oluşturulması ve kullanılması için Azure Resource Manager önerilir.

Kiracınızda uygun (okuma) izinlere sahip olun ve aboneliklerinizdeki tüm Azure aboneliklerini ve kaynaklarını numaralandırın.

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Rehberlik**: bir sınıflandırmaya göre mantıksal olarak düzenlemek için meta verilerle Azure kaynaklarına Etiketler uygulayın.

- [Etiketler oluşturma ve kullanma](/azure/azure-resource-manager/resource-group-using-tags)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: varlıkları düzenlemek ve izlemek için etiketleme, yönetim grupları ve uygun durumlarda ayrı abonelikler kullanın. 

Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliklerinizden zamanında silindiğinden emin olun.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynakları envanterini tanımlama ve sürdürme

**Rehberlik**: bir izin mekanizması olarak kullanabileceğiniz Kurumsal gereksinimlerinize göre onaylanan Azure kaynaklarının bir listesini oluşturun. Bu, kuruluşunuzun normal güvenlik değerlendirme süreçlerine göre incelenip onaylandıktan sonra yeni kullanılabilir Azure hizmetlerini eklemesine olanak tanır.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aboneliklerinizde oluşturulabilecek kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın.

Sahip olunan aboneliklerdeki Azure Kaynak Grafını kullanarak kaynakları sorgulayın ve bulun. 

Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:
- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Sanal ağ için Azure ilke örneği yerleşik bileşenleri](/azure/virtual-network/policy-samples)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak için Azure Active Directory (Azure AD) koşullu erişimi kullanın.

- [Azure Kaynak Yöneticisi 'ne erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: yüksek riskli uygulamaları fiziksel olarak veya mantıksal olarak ayırt edin

**Rehberlik**: iş işlemleri için gerekli olan, ancak kuruluşa daha fazla risk doğurabilecek yazılımlar, kendi sanal makinesi ve/veya sanal ağı içinde yalıtılmalı ve bir Azure Güvenlik duvarı ya da bir ağ güvenlik grubu ile yeterince güvenli hale gelmelidir.

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [Güvenlik Yapılandırması ile ağ güvenlik grubu oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Rehberlik**: Azure kaynaklarınızın diğer adlarını denetlemek veya Azure kaynaklarınızın yapılandırmasını zorlamak için özel ilkeler oluşturmak için kullanın. Yerleşik Azure Ilke tanımlarını kullanın.

Azure Resource Manager, yapılandırmaların kuruluşunuzun güvenlik gereksinimlerini karşıladığından emin olmak için gözden geçirilmesi gereken JavaScript Nesne Gösterimi (JSON) içinde şablonu dışarı aktarma özelliğine sahiptir.

Azure Resource Manager şablonlarını JavaScript Nesne Gösterimi (JSON) biçimlerine dışarı aktarın ve yapılandırmaların kurumsal güvenlik gereksinimlerinizi karşıladığından emin olmak için düzenli aralıklarla gözden geçirin.

Azure kaynaklarınız için güvenli bir yapılandırma temeli olarak güvenlik merkezi 'nden öneriler uygulayın.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias)

- [Öğretici: uyumluluğu zorlamak için ilke oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Güvenlik önerileri - başvuru kılavuzu](../security-center/recommendations-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Kılavuz**: Azure kaynaklarınız genelinde güvenli ayarları zorlamak Için Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] kullanın.  Ayrıca, kuruluşunuz tarafından gerekli olan Azure kaynaklarınızın güvenlik yapılandırmasını sürdürmek için Azure Resource Manager şablonlarını kullanabilirsiniz. 

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

- [Uyumluluğu zorunlu tutmak için ilkeleri oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager şablonlarına genel bakış](../azure-resource-manager/templates/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilke tanımları, Azure Resource Manager şablonları ve istenen durum yapılandırması betikleri gibi kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure DevOps kullanın.

Azure DevOps ile tümleşikse, yerleşik güvenlik grupları veya Azure Active Directory (Azure AD), TFS ile tümleştirildiğinde Azure AD 'de tanımlanan belirli kullanıcılara, yerleşik güvenlik gruplarına veya gruplara izin verin veya reddedin.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps 'da izinler ve gruplar hakkında](/azure/devops/organizations/security/about-permissions)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Kılavuz**: Azure Ilkesini kullanarak Azure kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın.  Azure kaynaklarınızın ağ yapılandırmasını denetlemek veya zorlamak için özel ilkeler oluşturmak üzere Azure Ilke diğer adlarını kullanın. Belirli Azure Load Balancer kaynaklarınızla ilgili yerleşik ilke tanımlarını uygulayın.  Ayrıca, yapılandırma değişikliklerini dağıtmak için Azure Otomasyonu 'nu kullanın. Azure Load Balancer kaynaklarınıza.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Diğer adları kullanma](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: Azure kaynaklarınız ve Azure ilkenize yönelik temel taramaların kaynak yapılandırmalarına uyarı ve denetim için temel taramalar gerçekleştirmesini sağlamak Için Güvenlik Merkezi 'ni kullanın.

- [Azure Güvenlik Merkezi 'nde öneriler nasıl düzeltileceği](../security-center/security-center-remediate-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: Kuruluşunuz için bir olay yanıt kılavuzu oluşturun. Tüm personelin rollerine ek olarak algılama aşamasından olay sonrası gözden geçirme aşamasına kadar tüm olay işleme/yönetim aşamalarını tanımlayan yazılı olay yanıt planları bulunduğundan emin olun. 

- [Azure Güvenlik Merkezi 'nde Iş akışı otomasyonlarını yapılandırma](../security-center/security-center-planning-and-operations-guide.md) 

- [Kendi güvenlik olay yanıtı işleminizi oluşturma kılavuzu](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Güvenlik Yanıt Merkezi 'nin bir olayın anatomisi](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Müşteri, kendi olay yanıt planının oluşturulmasına yardımcı olması için NıST 'nin bilgisayar güvenliği olay Işleme kılavuzunu da kullanabilir](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Rehberlik**: Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. 

Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizte ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır.

Abonelikleri kullanarak abonelikleri işaretleyin ve Azure kaynaklarını tanımlamak ve kategorilere ayırmak için özellikle de hassas verileri işleyen bir adlandırma sistemi oluşturun.  

Azure kaynaklarının önem düzeyine ve olayın oluştuğu ortama bağlı olarak uyarıların çözümünde önceliği belirlemek sizin sorumluluğunuzdadır.

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

**Kılavuz**: Azure kaynaklarına riskleri belirlemenize yardımcı olmak için sürekli dışarı aktarma özelliğini kullanarak güvenlik merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. 

Güvenlik Merkezi 'nde, uyarıları ve önerileri el ile veya devam eden sürekli bir biçimde dışa aktarmaya olanak sağlayan sürekli dışarı aktarma özelliğini kullanın. 

Uyarıları Azure Sentinel 'e akışa almak için Güvenlik Merkezi veri bağlayıcısını kullanın.

- [Sürekli dışarı aktarmayı yapılandırma](../security-center/continuous-export.md)

- [Uyarıların Azure Sentinel’e akışını yapma](../sentinel/connect-azure-security-center.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: güvenlik uyarılarına yanıtı otomatikleştirme

**Kılavuz**: Azure kaynaklarınızı korumaya yönelik güvenlik uyarılarına ve önerilerine yönelik yanıtları otomatik olarak tetiklemek Için Güvenlik Merkezi 'Ndeki Iş akışı Otomasyonu özelliğini kullanın.

- [Güvenlik ENTER 'ta iş akışı Otomasyonu nasıl yapılandırılır](../security-center/workflow-automation.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="penetration-tests-and-red-team-exercises"></a>Sızma Testleri ve Red Team Alıştırmaları

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: Penetme testleri ve Red ekibi alıştırmaları](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Azure kaynaklarınızın düzenli olarak sızma testini gerçekleştirin ve tüm kritik güvenlik bulgularını düzeltmeye dikkat edin

**Rehberlik**: Penettim testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak Için Microsoft bulut Penme test kurallarını izleyin. Microsoft tarafından yönetilen bulut altyapısına, hizmetlere ve uygulamalara yönelik kırmızı takım ve canlı site sızma testi gerçekleştirmek için Microsoft'un stratejisini ve yürütme sürecini kullanın. 

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](/azure/security/benchmarks/overview)
- [Azure güvenlik temelleri](/azure/security/benchmarks/security-baselines-overview) hakkında daha fazla bilgi edinin
