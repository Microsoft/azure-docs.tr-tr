---
title: Sanal Makine Ölçek Kümeleri için Azure Güvenlik temeli
description: Sanal Makine Ölçek Kümeleri güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 60b1de12f031a55388960a6e3c4e7df00c43e3c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867960"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Sanal Makine Ölçek Kümeleri için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 1.0'Dan](../security/benchmarks/overview-v1.md) sanal makine ölçek kümelerine kılavuzluk uygular. Azure Güvenlik kıyaslaması, bulut Çözümlerinizi Azure 'da nasıl güvence altına sunabilmenize ilişkin öneriler sağlar. içerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve sanal makine ölçek kümeleri için geçerli olan ilgili kılavuza göre gruplandırılır.

> [!NOTE]
> **Denetimler** , sanal makine ölçek kümeleri için geçerli değildir veya sorumluluğun Microsoft tarafından çıkarılmadığı anlamına gelir. Sanal makine ölçek kümelerinin Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, **[tam sanal makine ölçek kümeleri güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/virtual-machine-scale-sets-security-baseline-v1.1.xlsx)** bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: bir Azure sanal MAKINESI (VM) oluşturduğunuzda bir sanal ağ oluşturmanız veya var olan bir sanal ağı kullanmanız ve VM 'yi bir alt ağ ile yapılandırmanız gerekir. Dağıtılan tüm alt ağların, uygulamalarınıza güvenilen bağlantı noktalarına ve kaynaklarına özel ağ erişim denetimleriyle uygulanmış bir ağ güvenlik grubu olduğundan emin olun.

Alternatif olarak, Merkezi güvenlik duvarı için belirli bir kullanım durumu varsa, bu gereksinimleri karşılamak için Azure Güvenlik duvarı da kullanılabilir.

- [Azure sanal makine ölçek kümeleri için ağ hizmeti](virtual-machine-scale-sets-networking.md)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Güvenlik duvarını dağıtma ve yapılandırma](../firewall/tutorial-firewall-deploy-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: sanal ağların, alt ağların ve ağ arabirimlerinin yapılandırma ve trafiğini izleme ve günlüğe kaydetme

**Kılavuz**: Azure 'Da Azure sanal makıne (VM) kaynaklarınızın güvenliğini sağlamaya yardımcı olmak üzere ağ koruma önerilerini tanımlamak ve Izlemek Için Azure Güvenlik Merkezi 'ni kullanın. NSG akış günlüklerini etkinleştirin ve olağandışı etkinliğin VM 'Leri için trafik denetimi için bir depolama hesabına Günlükler gönderin.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="13-protect-critical-web-applications"></a>1,3: kritik Web uygulamalarını koruma

**Rehberlik**: sanal makine ölçek kümesini Web uygulamalarını barındırmak üzere kullanıyorsanız, sanal makine ölçek kümesi alt ağında ağ güvenlik grubu (NSG) kullanarak iletişim kurmasına izin verilen ağ trafiğini, bağlantı noktalarını ve protokollerin sayısını sınırlandırın. NSG 'lerinizi yalnızca uygulamanıza gereken trafiğe izin verecek şekilde yapılandırırken en az ayrıcalıklı bir ağ yaklaşımını izleyin.

Azure Web uygulaması güvenlik duvarını (WAF), gelen trafiğin ek incelemesi için kritik Web uygulamaları önünde da dağıtabilirsiniz. WAF ve alma günlükleri için tanılama ayarını bir depolama hesabı, Olay Hub 'ı veya Log Analytics çalışma alanında etkinleştirin.

- [Azure sanal makine ölçek kümeleri için ağ hizmeti](virtual-machine-scale-sets-networking.md)

- [Azure portal kullanarak Web uygulaması güvenlik duvarı ile uygulama ağ geçidi oluşturma](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: DDoS saldırılarına karşı koruma sağlamak Için sanal ağlarda dağıtılmış hizmet reddi (DDoS) standart korumasını etkinleştirin. Azure Güvenlik Merkezi tümleşik tehdit zekasından yararlanarak, bilinen kötü amaçlı IP adresleriyle iletişimleri izleyebilirsiniz.  Tehdit zekası etkinleştirilmiş ve kötü amaçlı ağ trafiği için "uyarı ve reddetme" olarak yapılandırılmış sanal ağ segmentlerinizin her birinde Azure Güvenlik duvarını yapılandırın.

Azure Güvenlik Merkezi 'nin, sınırlı bir süre için onaylanan IP adresleriyle Windows Sanal Makineleri görünürlüğünü sınırlamak üzere tam zamanında ağ erişimi de kullanabilirsiniz.  Ayrıca, bağlantı noktalarını ve kaynak IP 'Leri gerçek trafik ve tehdit zekası temelinde sınırlayan NSG yapılandırmalarının kullanılması için Azure Güvenlik Merkezi Uyarlamalı ağ sağlamlaştırma kullanın.

- [DDoS korumasını yapılandırma](../ddos-protection/manage-ddos-protection.md)

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Güvenlik Merkezi tümleşik tehdit zekasını anlama](../security-center/azure-defender.md)

- [Azure Güvenlik Merkezi Uyarlamalı ağ sağlamlaştırma 'yi anlama](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Güvenlik Merkezi 'Ni tam zamanında ağ Access Control anlama](../security-center/security-center-just-in-time.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Kılavuz**: Azure sanal makineleriniz için akış kayıtları oluşturmak üzere NSG akış günlüklerini bir depolama hesabına kaydedebilirsiniz. Anormal etkinlikleri araştırırken, ağ trafiğinin olağan dışı ve beklenmeyen etkinlikler için incelenebilecek şekilde ağ Izleyicisi paket yakalamayı etkinleştirebilirsiniz.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Ağ İzleyicisini etkinleştirme](../network-watcher/network-watcher-create.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: Ağ İzleyicisi tarafından sunulan paket yakalamalarını ve açık kaynaklı kimlikler aracını birleştirerek, çok çeşitli tehditler için ağ üzerinden izinsiz giriş algılama gerçekleştirebilirsiniz. Ayrıca, tehdit zekasını etkinleştirilmiş ve kötü amaçlı ağ trafiği için "uyarı ve reddetme" olarak yapılandırılmış Azure Güvenlik duvarını sanal ağ kesimlerine uygun şekilde dağıtabilirsiniz.

- [Ağ Izleyicisi ve açık kaynak araçlarla ağ üzerinden izinsiz bir algılama gerçekleştirme](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Güvenlik Duvarı ile uyarıları yapılandırma](../firewall/threat-intel.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Web uygulamalarına trafiği yönetme

**Kılavuz**: Web uygulamalarını barındırmak Için sanal makine ölçek kümesi kullanıyorsanız, güvenilen SERTIFIKALAR için HTTPS/SSL özellikli Web uygulamaları için Azure Application Gateway dağıtabilirsiniz. Azure Application Gateway ile, bağlantı noktalarına dinleyici atayarak, kurallar oluşturarak ve sanal makine ölçek kümesi gibi bir arka uç havuzuna kaynak ekleyerek uygulama Web trafiğinizi belirli kaynaklara yönlendirdirebilirsiniz.

- [Application Gateway dağıtma](../application-gateway/quick-create-portal.md)

- [Application Gateway HTTPS kullanacak şekilde yapılandırma](../application-gateway/create-ssl-portal.md)

- [Bir Application Gateway’e başvuran bir ölçek kümesi oluşturma](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#create-a-scale-set-that-references-an-application-gateway)

- [Azure Web uygulaması ağ geçitleri ile katman 7 yük dengelemesini anlama](../application-gateway/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Rehberlik**: ağ güvenlik gruplarında veya Azure sanal makineleriniz Için yapılandırılmış Azure Güvenlik duvarında ağ erişim denetimleri tanımlamak Için sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adı (örn., Apimanaya) belirterek, ilgili hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

- [Hizmet etiketlerini anlama ve kullanma](../virtual-network/service-tags-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Kılavuz**: Azure ilkesi 'Ni kullanarak Azure sanal makine ölçek kümeleri için standart güvenlik yapılandırması tanımlayın ve uygulayın. Ayrıca, tek bir şema tanımında Azure Resource Manager şablonları, rol atamaları ve Azure ilke atamaları gibi temel ortam yapıtlarını paketleyerek büyük ölçekli Azure VM dağıtımlarını basitleştirmek için Azure şemaları 'nı kullanabilirsiniz. Şema 'i aboneliklere uygulayabilir ve şema sürümü oluşturma aracılığıyla kaynak yönetimini etkinleştirebilirsiniz.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Sanal Makine Ölçek Kümesi şablonları hakkında bilgi edinin](virtual-machine-scale-sets-mvss-start.md) 

- [Ağ için Azure Ilke örnekleri](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: ağ güvenlik grupları (NSG) ve Windows sanal makineleriniz için yapılandırılmış ağ güvenliği ve trafik akışı ile ilgili diğer kaynaklar için Etiketler kullanabilirsiniz. Bireysel NSG kuralları için "Açıklama" alanını kullanarak ağa giden/giden trafiğe izin veren kuralların iş gereksinimini ve/veya süresini belirtin.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Rehberlik**: Azure sanal makine ölçek kümesiyle ilgili ağ kaynak yapılandırmalarında yapılan değişiklikleri Izlemek Için Azure etkinlik günlüğünü kullanın. Kritik ağ ayarlarında veya kaynaklarda değişiklik yapıldığında tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

Sanal makine ölçek kümesiyle ilgili ağ kaynağına yönelik yapılandırmaların doğrulanması (ve/veya düzeltilmesi) için Azure Ilkesi ' ni kullanın.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Ağ için Azure Ilke örnekleri](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 1.11](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-11.md)]

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: onaylanan zaman eşitleme kaynaklarını kullanın

**Rehberlik**: Microsoft, Azure kaynakları için zaman kaynaklarını korur, ancak sanal makineleriniz için zaman eşitleme ayarlarını yönetme seçeneğiniz vardır.

- [Azure Windows işlem kaynakları için Saat eşitlemesini yapılandırma](../virtual-machines/windows/time-sync.md)

- [Azure Linux işlem kaynakları için Saat eşitlemesini yapılandırma](../virtual-machines/linux/time-sync.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: etkinlik günlükleri, sanal makine ölçek kümesi kaynakları üzerinde gerçekleştirilen işlemleri ve eylemleri denetlemek için kullanılabilir. Etkinlik günlüğü, kaynaklarınız için okuma işlemleri (GET) dışında tüm yazma işlemlerini (PUT, POST, DELETE) içerir. Etkinlik günlükleri, sorun giderirken bir hata bulmak veya kuruluşunuzdaki bir kullanıcının bir kaynağı nasıl değiştirdiğinizi izlemek için kullanılabilir.

Azure etkinlik günlüklerinden veya sanal makine kaynaklarından oluşturulan Pano günlüğü verilerini Azure Sentinel 'e veya Merkezi güvenlik günlüğü yönetimi için bir üçüncü taraf SıEM 'ye etkinleştirebilir.

Azure sanal makineleri için güvenlik olay günlüğü izlemesi sağlamak üzere Azure Güvenlik Merkezi 'ni kullanın. Güvenlik olay günlüğünün oluşturduğu veri hacmi verildiğinde, varsayılan olarak depolanmaz. 

Kuruluşunuz, güvenlik olay günlüğü verilerini sanal makineden sürdürmek istiyorsanız, Azure Güvenlik Merkezi 'nde yapılandırılmış istenen veri toplama katmanında bir Log Analytics çalışma alanı içinde depolanabilir.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama ](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Izleyici ve üçüncü taraf SıEM tümleştirmesi ile çalışmaya başlama](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

- [Azure Güvenlik Merkezinde veri toplama](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) 

- [Azure 'da sanal makineleri izleme](../azure-monitor/vm/monitor-vm-azure.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: etkinlik günlükleri, sanal makine ölçek kümesi kaynakları üzerinde gerçekleştirilen işlemleri ve eylemleri denetlemek için kullanılabilir. Etkinlik günlüğü, kaynaklarınız için okuma işlemleri (GET) dışında tüm yazma işlemlerini (PUT, POST, DELETE) içerir. Etkinlik günlükleri, sorun giderirken bir hata bulmak veya kuruluşunuzdaki bir kullanıcının bir kaynağı nasıl değiştirdiğinizi izlemek için kullanılabilir.

Tanılama uzantısını sanal makinelerinize (VM) dağıtarak, Konuk işletim sistemi tanılama verilerinin toplanmasını etkinleştirin. Tanılama uzantısını kullanarak, bir Azure sanal makinesinden uygulama günlükleri veya performans sayaçları gibi tanılama verilerini toplayabilirsiniz.

Azure sanal makine ölçek kümesi tarafından desteklenen uygulama ve hizmetlerin gelişmiş görünebilirliği için hem VM'ler için Azure İzleyici hem de Application Insights 'ı etkinleştirebilirsiniz. Application Insights, uygulamanızı izleyebilir ve HTTP istekleri, özel durumlar vb. gibi Telemetriyi yakalayabilir. böylece, VM 'Ler ve uygulamanız arasındaki sorunları ilişkilendirebilmenizi sağlayabilirsiniz.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure 'da sanal makineleri izleme](../azure-monitor/vm/monitor-vm-azure.md)

- [Application Insights'a genel bakış](../azure-monitor/app/app-insights-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: işletim sistemlerinden güvenlik günlüklerini toplama

**Rehberlik**: Azure sanal makineleri Için güvenlik olay günlüğü izlemesi sağlamak üzere Azure Güvenlik Merkezi 'ni kullanın. Güvenlik olay günlüğünün oluşturduğu veri hacmi verildiğinde, varsayılan olarak depolanmaz. 

Kuruluşunuz, güvenlik olay günlüğü verilerini sanal makineden sürdürmek istiyorsanız, Azure Güvenlik Merkezi 'nde yapılandırılmış istenen veri toplama katmanında bir Log Analytics çalışma alanı içinde depolanabilir.

- [Azure Güvenlik Merkezinde veri toplama](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) 

- [Azure 'da sanal makineleri izleme](../azure-monitor/vm/monitor-vm-azure.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-4.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Rehberlik**: sanal makine günlüklerini depolamak için kullanılan tüm depolama hesaplarının veya Log Analytics çalışma alanlarının, kuruluşunuzun uyumluluk düzenlemelerine göre ayarlanmış günlük tutma süresine sahip olduğundan emin olun.

- [Azure 'da sanal makineleri izleme](../azure-monitor/vm/monitor-vm-azure.md)

- [Log Analytics çalışma alanı saklama süresini yapılandırma](../azure-monitor/logs/manage-cost-storage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Rehberlik**: anormal davranış için günlükleri çözümleyin ve izleyin ve sonuçları düzenli olarak gözden geçirin. Günlükleri gözden geçirmek ve günlük verilerinde sorgular gerçekleştirmek için Azure Izleyici 'yi kullanın.

Alternatif olarak, günlüklerinizi izlemek ve gözden geçirmek için Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri kullanabilirsiniz.

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Log Analytics çalışma alanını anlayın](/azure/azure-monitor/logs/get-started-queries)

- [Azure Izleyici 'de özel sorgular gerçekleştirme](../azure-monitor/logs/log-analytics-tutorial.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Kılavuz**: Azure Güvenlik Merkezi 'ni kullanarak, Azure sanal makinelerinize yönelik güvenlik günlüklerinde ve olaylarda bulunan anormal etkinliklerle ilgili izleme ve uyarı oluşturma için bir Log Analytics çalışma alanı ile yapılandırılır.  

Alternatif olarak, anormal etkinliklere yönelik uyarıları ayarlamak için Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri etkinleştirebilirsiniz.

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Güvenlik Merkezi 'nde uyarıları yönetme](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics günlük verilerinde uyarı alma](../azure-monitor/alerts/tutorial-response.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="28-centralize-anti-malware-logging"></a>2,8: kötü amaçlı yazılımdan koruma 'yı merkezileştirme

**Kılavuz**: Azure Cloud Services ve sanal makineler için Microsoft kötü amaçlı yazılımdan koruma kullanabilir ve Windows sanal makinelerinizi olayları günlüğe kaydetmek Için bir Azure depolama hesabına yapılandırabilirsiniz. Depolama hesaplarından olayları almak ve uygun yerlerde uyarı oluşturmak için bir Log Analytics çalışma alanı yapılandırın. Azure Güvenlik Merkezi 'nde önerileri takip edin: "Işlem &amp; Uygulamaları".  Linux sanal makineleri için, kötü amaçlı yazılımdan koruma güvenlik açığı algılama için üçüncü taraf bir araca ihtiyacınız olacaktır. 

- [Cloud Services ve sanal makineler için Microsoft kötü amaçlı yazılımdan koruma yapılandırma](../security/fundamentals/antimalware.md)

- [Sanal makineler için konuk düzeyinde izlemeyi etkinleştirme](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

- [Linux sunucularını Azure Güvenlik Merkezi 'ne ekleme yönergeleri](../security-center/quickstart-onboard-machines.md) 

- [Aşağıdaki bağlantı, Microsoft tarafından önerilen güvenlik yönergeleri sağlar ve bu, seçilen güvenlik açığı yazılımının ölçüt listesi olarak işlev görebilir](../virtual-machines/security-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-8.md)]

### <a name="29-enable-dns-query-logging"></a>2,9: DNS sorgu günlüğünü etkinleştir

**Rehberlik**: kuruluşunuzun ihtiyaç duyduğu şekılde, DNS günlüğü çözümü Için Azure Marketi 'nden bir üçüncü taraf çözümü uygulayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="210-enable-command-line-audit-logging"></a>2,10: komut satırı denetim günlüğünü etkinleştir

**Kılavuz**: Azure Güvenlik Merkezi, Azure sanal MAKINELERI (VM) Için güvenlik olay günlüğü izleme sağlar. Güvenlik Merkezi, desteklenen tüm Azure VM 'lerde ve otomatik sağlama etkinse oluşturulan tüm yeni Microsoft Monitoring Agent sağlar veya aracıyı el ile yükleyebilirsiniz.  Aracı, işlem oluşturma olayı 4688 ve olay 4688 ' nin içindeki CommandLine alanı ' na izin vermez. VM üzerinde oluşturulan yeni süreçler, olay günlüğü tarafından kaydedilir ve Güvenlik Merkezi 'nin algılama hizmetleri tarafından izlenir.

Linux sanal makineleri için, konsol günlüğünü düğüm başına temelinde el ile yapılandırabilir ve verileri depolamak için Syslog 'lar kullanabilirsiniz.  Ayrıca, Azure Izleyici 'nin Log Analytics çalışma alanını kullanarak günlükleri gözden geçirin ve Azure sanal makinelerinden Syslog verilerinde sorgular gerçekleştirin.

- [Azure Güvenlik Merkezinde veri toplama](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

- [Azure Izleyici 'de özel sorgular gerçekleştirme](../azure-monitor/logs/get-started-queries.md)

- [Azure İzleyici'de Syslog veri kaynakları](../azure-monitor/agents/data-sources-syslog.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: Azure Active Directory (Azure AD) Kullanıcı erişimini yönetmek için önerilen yöntemdir, Azure sanal makineleri yerel hesaplara sahip olabilir. Hem yerel hem de etki alanı hesaplarının genellikle en düşük bir ayak izi ile incelenmesi ve yönetilmesi gerekir. Ayrıca, sanal makine kaynaklarına erişmek için kullanılan yönetim hesapları için Azure Privileged Identity Management 'ten yararlanın.

- [Yerel hesaplar için bilgiler](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

- [Ayrıcalıklı Identity Manager hakkında bilgi](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Kılavuz**: Azure sanal makine ölçek kümesi ve Azure Active Directory (Azure AD) varsayılan parola kavramına sahip değildir. Varsayılan parolaları kullanan üçüncü taraf uygulamalardan ve Market hizmetlerinden sorumlu müşteri.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: sanal makinelerinize erişimi olan adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturun. Yönetim hesaplarının sayısını izlemek için Azure Güvenlik Merkezi kimlik ve erişim yönetimi 'ni kullanın. Azure sanal makine kaynaklarına erişmek için kullanılan tüm yönetici hesapları da Azure Privileged Identity Management (PıM) tarafından yönetilebilir. Azure Privileged Identity Management, yalnızca belirli zaman çerçevelerinde ve onaylayan olması için izinleri sağlamak üzere, rol olmadan önce çok faktörlü kimlik doğrulaması gerektiren, bir rolün ve temsilciliğini gerektiren çeşitli seçenekler sunar.

- [Azure Güvenlik Merkezi kimlik ve erişimini anlama](../security-center/security-center-identity-access.md)

- [Ayrıcalıklı Identity Manager hakkında bilgi](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 3.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-3-3.md)]

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory çoklu oturum açma kullan (SSO)

**Rehberlik**: mümkün olan yerlerde, tek başına bağımsız kimlik bilgilerini hizmet başına yapılandırmak yerine Azure Active Directory (Azure AD) ile SSO kullanın. Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini kullanın.

- [Azure AD 'de uygulamalarda çoklu oturum açma](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin ve Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini izleyin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: yönetim görevleri için güvenli, Azure tarafından yönetilen iş istasyonları kullanın

**Kılavuz**: Azure kaynaklarını açmak ve yapılandırmak için yapılandırılmış çok faktörlü kimlik doğrulaması Ile Paws (ayrıcalıklı erişim iş istasyonları) kullanın.

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: ortamda şüpheli veya güvenli olmayan bir etkinlik oluştuğunda günlükler ve uyarılar oluşturmak için Azure Active Directory (Azure AD) PRIVILEGED IDENTITY Management (PIM) kullanın. Riskli Kullanıcı davranışında uyarıları ve raporları görüntülemek için Azure AD risk algılamalarını kullanın. İsteğe bağlı olarak, müşteri Azure Güvenlik Merkezi risk algılama uyarılarını Azure Izleyici 'ye alabilir ve eylem gruplarını kullanarak özel uyarı/bildirim yapılandırabilir.

- [Privileged Identity Management dağıtma (PıM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure Güvenlik Merkezi risk algılamalarını anlama (şüpheli etkinlik)](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Özel uyarı ve bildirim için eylem gruplarını yapılandırma](../azure-monitor/alerts/action-groups.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: IP adresi aralıklarının veya ülkelerin/bölgelerin yalnızca belirli mantıksal gruplarından erişime izin vermek için Azure Active Directory (Azure AD) koşullu erişim ilkeleri ve adlandırılmış konumlar kullanın.

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifrelemeyi kullanarak verileri korur. Azure AD Ayrıca, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.  Kodunuzda kimlik bilgileri olmadan Key Vault de dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için Yönetilen kimlikler ' i kullanabilirsiniz. Bir sanal makinede çalışan kodunuz, Azure AD kimlik doğrulamasını destekleyen hizmetlere erişim belirteçleri istemek için yönetilen kimliğini kullanabilir.

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory-domain-services/tutorial-create-instance.md)

- [Azure kaynaklarına genel bakış için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlar. Ayrıca, grup üyeliklerini verimli bir şekilde yönetmek için Azure AD kimlik erişimi incelemelerini kullanın, kurumsal uygulamalara ve rol atamalarına erişin. Kullanıcının erişimi, yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için düzenli olarak incelenebilir. Azure sanal makineleri 'ni kullanırken, sistemin güvenliğini tehlikeye atabilecek beklenmeyen hesaplar olmadığından emin olmak için yerel güvenlik gruplarını ve kullanıcıları gözden geçirmeniz gerekir.

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: denetim günlüklerini ve oturum açma günlüklerini bir Log Analytics çalışma alanına göndermek için Azure Active Directory (Azure AD) için tanılama ayarlarını yapılandırın. Ayrıca Azure Izleyici 'yi kullanarak günlükleri gözden geçirin ve Azure sanal makinelerindeki günlük verilerinde sorgular gerçekleştirin.

- [Log Analytics çalışma alanını anlayın](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure Izleyici 'de özel sorgular gerçekleştirme](../azure-monitor/logs/get-started-queries.md)

- [Azure 'da sanal makineleri izleme](/azure/azure-monitor/vm/monitor-vm-azure)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapması üzerinde uyarı

**Rehberlik**: depolama hesabı kaynaklarınızla ilgili olarak algılanan şüpheli eylemler için otomatik yanıtları yapılandırmak üzere Azure Active Directory (Azure AD) risk ve kimlik koruması özelliklerini kullanın. Kuruluşunuzun güvenlik yanıtlarını uygulamak için Azure Sentinel aracılığıyla otomatikleştirilmiş yanıtları etkinleştirmeniz gerekir.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: destek senaryoları sırasında Microsoft 'un ilgili müşteri verilerine erişimini sağlama

**Rehberlik**: Microsoft 'un müşteri verilerine erişmesi gereken (örneğin, bir destek isteği sırasında), Azure sanal makineler için müşteri kasası kullanarak müşteri verileri erişim isteklerini gözden geçirip onaylayabilir veya reddedebilir.

- [Müşteri Kasası anlama](../security/fundamentals/customer-lockbox-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: gizli bilgileri depolayan veya işleyen Azure sanal makinelerini izlemeye yardımcı olması için etiketleri kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: geliştirme, test ve üretim için ayrı abonelikler ve/veya yönetim grupları uygulayın. Kaynaklar, sanal ağ/alt ağ ile ayrılmalıdır, uygun şekilde etiketlenebilir ve bir ağ güvenlik grubu (NSG) içinde veya bir Azure Güvenlik Duvarı tarafından güvenli hale getirilir. Hassas verileri depolayan veya işleyen sanal makineler için, kullanımda olmadığında devre dışı bırakmak üzere ilke ve yordam uygulayın.

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

**Rehberlik**: önemli bilgilerin izinsiz aktarımını izleyen ve bilgi güvenliği uzmanlarına uyarı ederken bu tür aktarımları engelleyen üçüncü taraf çözümü uygulama.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini, müşteri veri kaybına ve pozlamaya karşı koruma altına alır. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Rehberlik**: bir RDP veya SSH OTURUMUNDA bir VM 'ye bağlanırken olduğu gibi, Windows çalıştıran sanal MAKINELER (VM) ile geçiş yapılan veriler, bağlantı yapısına bağlı olarak bir dizi şekilde şifrelenir.

Microsoft, bulut hizmetleri ve müşteriler arasında seyahat edildiğinde verileri korumak için Aktarım Katmanı Güvenliği (TLS) protokolünü kullanır.

- [VM 'lerde geçiş içi şifreleme](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#in-transit-encryption-in-vms)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: bir üçüncü taraf etkin bulma aracı kullanarak, şirket içi veya uzak bir hizmet sağlayıcı gibi kuruluş teknoloji sistemleri tarafından depolanan, işlenen veya aktarılan tüm hassas bilgileri, kuruluşun hassas bilgi envanterini güncelleştirmek için kullanın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: kaynaklara erişimi denetlemek için Azure RBAC kullanma 

**Kılavuz**: Azure rol tabanlı erişim denetimi 'Ni (Azure RBAC) kullanarak, takımınızda görevleri ayırabilirsiniz ve yalnızca sanal MAKINENIZE (VM) işlerini gerçekleştirmek için ihtiyaç duydukları kullanıcılara erişim miktarını verebilirsiniz. SANAL makinede herkes için sınırsız izin vermek yerine yalnızca belirli eylemlere izin verebilirsiniz. Azure CLı veya Azure PowerShell kullanarak Azure portal VM için erişim denetimi yapılandırabilirsiniz.

- [Azure RBAC](../role-based-access-control/overview.md)

- [Yerleşik Azure rolleri](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: erişim denetimini zorlamak için ana bilgisayar tabanlı veri kaybı önleme kullanın

**Rehberlik**: veri ihlallerinin riskini azaltmak için erişim denetimlerine zorlamak üzere otomatik ana bilgisayar tabanlı veri kaybı önleme çözümü gibi üçüncü taraf bir araç uygulayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Rehberlik**: sanal MAKINELERDE (VM) sanal diskler, sunucu tarafı şifreleme veya Azure disk ŞIFRELEMESI (ade) kullanılarak Rest 'te şifrelenir. Azure disk şifrelemesi, Konuk VM içindeki müşteri tarafından yönetilen anahtarlarla yönetilen diskleri şifrelemek için Linux 'un DM-Crypt özelliğinden yararlanır. Müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifreleme, depolama hizmetindeki verileri şifreleyerek VM 'niz için herhangi bir işletim sistemi türünü ve görüntüsünü kullanmanızı sağlayarak ADE 'yi geliştirir.

- [Sanal Makine Ölçek Kümeleri için Azure disk şifrelemesi](disk-encryption-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Kılavuz**: Azure Izleyici 'Yi Azure etkinlik günlüğü ile birlikte kullanarak, değişikliklerin sanal makinelerde ölçek kümelerine ve ilgili kaynaklara ne zaman gerçekleştiği hakkında uyarılar oluşturun.  

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure Depolama analizini günlüğe kaydetme](../storage/common/storage-analytics-logging.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: otomatikleştirilmiş güvenlik açığı tarama araçlarını çalıştırma

**Rehberlik**: Azure sanal makinelerinizde güvenlik açığı değerlendirmeleri gerçekleştirmeye yönelik Azure Güvenlik Merkezi önerilerini izleyin.  Sanal makinelerinize yönelik güvenlik açığı değerlendirmelerini gerçekleştirmek için, Azure Güvenlik önerilir veya üçüncü taraf çözümü kullanın.

- [Azure Güvenlik Merkezi güvenlik açığı değerlendirmesi önerilerini uygulama](../security-center/deploy-vulnerability-assessment-vm.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-1.md)]

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: otomatik işletim sistemi düzeltme eki yönetimi çözümünü dağıtma

**Rehberlik**: desteklenen işletim sistemi sürümleri Için veya paylaşılan görüntü galerisinde depolanan özel görüntüler IÇIN otomatik Işletim sistemi yükseltmelerini etkinleştirin.

- [Azure 'da sanal makine ölçek kümeleri için otomatik işletim sistemi yükseltmeleri](virtual-machine-scale-sets-automatic-upgrade.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 5.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-2.md)]

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: üçüncü taraf yazılım başlıkları için otomatik düzeltme eki yönetimi çözümünü dağıtma

**Kılavuz**: Azure sanal makine ölçek kümeleri, otomatik işletim sistemi görüntüsü yükseltmelerini kullanabilir. Sanal makine ölçek kümesindeki temeldeki sanal makineler için Azure Istenen durum yapılandırması (DSC) uzantısını kullanabilirsiniz. DSC, sanal makineleri, istenen yazılımınızı çalıştırmaya devam etmek için çevrimiçi olduklarında yapılandırmak üzere kullanılır.

- [Azure DSC Uzantısı ile sanal makine ölçek kümelerini kullanma](virtual-machine-scale-sets-dsc.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: geri dönüş güvenlik açığı taramalarını karşılaştırın

**Rehberlik**: tarama sonuçlarını tutarlı aralıklarla dışarı aktarın ve güvenlik açıklarının düzeltildiğini doğrulamak için sonuçları karşılaştırın. Azure Güvenlik Merkezi tarafından önerilen güvenlik açığı yönetimi önerisi kullanılırken, müşteri, geçmiş tarama verilerini görüntülemek için seçili çözümün portalına pivot olarak eklenebilir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek için risk derecelendirme işlemi kullanın

**Kılavuz**: Azure Güvenlik Merkezi tarafından sunulan varsayılan risk derecelendirmelerini (güvenli puan) kullanın.

- [Azure Güvenlik Merkezi güvenli Puanını anlama](../security-center/secure-score-security-controls.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-5.md)]

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: aboneliklerinizde bulunan tüm kaynakları (sanal makineler dahil) sorgulamak ve öğrenmek Için Azure Kaynak grafiğini kullanın. Kiracınızda uygun (okuma) izinleriniz olduğundan ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerinin listesini belirleyebildiğinizden emin olun.

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Kılavuz**: Azure kaynaklarına Etiketler uygulayarak bunları bir sınıflandırmaya göre mantıksal olarak organize etmek için meta veriler sağlar.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: sanal makinelerin ölçek kümelerini ve ilgili kaynakları düzenlemek ve izlemek için uygun yerlerde etiketleme, yönetim grupları ve ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynaklarının envanterini tanımlayın ve saklayın

**Rehberlik**: Kurumsal gereksinimleriniz uyarınca, işlem kaynakları Için onaylanan Azure kaynakları ve onaylanan yazılım envanterini oluşturun.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerine oluşturulabilecek kaynak türlerine kısıtlamalar koymak için Azure ilkesini kullanın:

- İzin verilmeyen kaynak türleri

- İzin verilen kaynak türleri

Ayrıca, Azure Kaynak grafiğini kullanarak abonelikler içindeki kaynakları sorgular/bulur. Bu, depolama hesaplarıyla ilgili olanlar gibi yüksek güvenlik tabanlı ortamlarda yardımcı olabilir.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: işlem kaynakları içindeki onaylanmamış yazılım uygulamaları için izleyici

**Kılavuz**: Azure Otomasyonu, dağıtım, işlemler ve iş yüklerinin ve kaynakların yetkisini alma sırasında tamamen denetim sağlar.  Sanal makinelerdeki tüm yazılımlarla ilgili bilgi toplamayı otomatikleştirmek için Azure sanal makine envanterinden yararlanın. Note: yazılım adı, sürüm, yayımcı ve yenileme süresi Azure portal kullanılabilir. Yüklemenin tarih ve diğer bilgilerine erişim sağlamak için, müşteri, Konuk düzeyinde tanılamayı etkinleştirmek ve Windows olay günlüklerini bir Log Analytics çalışma alanına getirmelerini sağlamak için gereklidir.

Şu anda Uyarlamalı uygulama denetimleri, sanal makine ölçek kümeleri için kullanılamaz.

- [Azure Otomasyonu’na giriş](../automation/automation-intro.md)

- [Azure VM envanterini etkinleştirme](../automation/automation-tutorial-installed-software.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Kılavuz**: Azure Otomasyonu, dağıtım, işlemler ve iş yüklerinin ve kaynakların yetkisini alma sırasında tamamen denetim sağlar.  Sanal makinelerde yüklü olan tüm yazılımları tanımlamak için Değişiklik İzleme kullanabilirsiniz. Kendi işleminizi uygulayabilir veya yetkisiz yazılımı kaldırmak için Azure Otomasyonu durum yapılandırması ' nı kullanabilirsiniz.

- [Azure Otomasyonu’na giriş](../automation/automation-intro.md)

- [Değişiklik İzleme çözümü ile ortamınızdaki değişiklikleri izleyin](../automation/change-tracking/overview.md)

- [Azure Otomasyonu durum yapılandırmasına genel bakış](../automation/automation-dsc-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="68-use-only-approved-applications"></a>6,8: yalnızca onaylanan uygulamaları kullan

**Rehberlik**: Şu anda Uyarlamalı uygulama denetimleri, sanal makine ölçek kümeleri için kullanılamaz. Yalnızca onaylanan uygulamalara yönelik kullanımı denetlemek için 3. taraf yazılımını kullanın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 6.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-8.md)]

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerine oluşturulabilecek kaynak türlerine kısıtlamalar koymak için Azure ilkesini kullanın:
- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 6.9](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-9.md)]

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: onaylanan yazılım başlıkları envanterini koruyun

**Rehberlik**: Şu anda Uyarlamalı uygulama denetimleri, sanal makine ölçek kümeleri için kullanılamaz. Kuruluşunuzun gereksinimini karşılamazsa üçüncü taraf çözümünü uygulayın.

- [Azure Güvenlik Merkezi Uyarlamalı uygulama denetimlerini kullanma](../security-center/security-center-adaptive-application.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 6.10](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-10.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak Için Azure koşullu erişimi kullanın.

- [Azure Resource Manager erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: kullanıcıların işlem kaynakları içinde betikleri yürütme yeteneğini sınırlayın

**Kılavuz**: betiklerin türüne bağlı olarak, kullanıcıların Azure işlem kaynakları içinde betikleri yürütme yeteneğini sınırlandırmak için işletim sistemine özgü yapılandırma veya üçüncü taraf kaynakları kullanabilirsiniz.

- [Windows ortamlarında PowerShell betiği yürütmeyi denetleme](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: yüksek riskli uygulamaları fiziksel olarak veya mantıksal olarak ayırt edin

**Kılavuz**: Azure ortamınızda dağıtılan yüksek riskli uygulamalar, sanal ağ, alt ağ, abonelikler, yönetim grupları vb. ve bir Azure Güvenlik Duvarı, Web uygulaması güvenlik duvarı (WAF) veya ağ güvenlik grubu (NSG) kullanılarak yeterince güvenli hale getirilmiş olabilir. 

- [Azure 'da sanal ağlar ve sanal makineler](/azure/virtual-machines/windows/network-overview)

- [Azure Güvenlik Duvarı 'na genel bakış](../firewall/overview.md)

- [Web uygulaması güvenlik duvarına genel bakış](../web-application-firewall/overview.md)

- [Ağ güvenliğine genel bakış](../virtual-network/network-security-groups-overview.md)

- [Azure sanal ağına genel bakış](../virtual-network/virtual-networks-overview.md)

- [Kaynaklarınızı Azure yönetim gruplarıyla düzenleme](../governance/management-groups/overview.md)

- [Abonelik kararı kılavuzu](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Kılavuz**: Azure Ilkesi veya Azure Güvenlik Merkezi 'ni kullanarak tüm Azure kaynakları için güvenlik yapılandırmalarının bakımını yapın. Ayrıca, Azure Resource Manager, yapılandırmanın şirketinizin güvenlik gereksinimlerini karşıladığından/aştığından emin olmak için gözden geçirilmesi gereken JavaScript Nesne Gösterimi (JSON) içinde şablonu dışarı aktarma özelliğine sahiptir.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [VM şablonunun nasıl indirileceği hakkında bilgi](/previous-versions/azure/virtual-machines/windows/download-template)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: güvenli işletim sistemi yapılandırması oluşturma

**Rehberlik**: tüm bilgi işlem kaynaklarında güvenlik yapılandırmalarının bakımını sağlamak Için sanal makinelerinizdeki güvenlik yapılandırmalarında güvenlik açıklarını düzeltme güvenlik açıklarını düzeltin.

- [Azure Güvenlik Merkezi önerilerini izleme](../security-center/security-center-recommendations.md)

- [Azure Güvenlik Merkezi önerilerini Düzeltme](../security-center/security-center-remediate-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Rehberlik**: sanal makine ölçek kümeleriyle ilişkili Azure kaynaklarını güvenli bir şekilde yapılandırmak için Azure Resource Manager şablonları ve Azure ilkeleri kullanın.  Azure Resource Manager şablonlar, Azure kaynaklarıyla birlikte sanal makine dağıtmak için kullanılan JSON tabanlı dosyalardır ve özel şablonla korunmaları gerekir.  Microsoft, temel şablonlarda Bakımı gerçekleştirir.  Azure kaynaklarınızın tamamında güvenli ayarları zorlamak için Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] kullanın.

- [Azure Resource Manager şablonları oluşturma hakkında bilgi](../virtual-machines/windows/ps-template.md)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: güvenli işletim sistemi yapılandırmalarının bakımını yapma

**Rehberlik**: dağıtım Için Azure sanal MAKINELERI (VM) için güvenli bir yapılandırma sürdürmek üzere çeşitli seçenekler mevcuttur:

1. Azure Resource Manager şablonlar: Bunlar, Azure portal bir VM dağıtmak için kullanılan JSON tabanlı dosyalardır ve özel şablonun saklanması gerekir. Microsoft, temel şablonlarda Bakımı gerçekleştirir.

2. Özel sanal sabit disk (VHD): bazı durumlarda, diğer yollarla yönetilebilecek karmaşık ortamlarla ilgilenirken gibi özel VHD dosyalarının kullanılması gerekebilir.

3. Azure Otomasyonu durum yapılandırması: temel işletim sistemi dağıtıldıktan sonra, ayarların daha ayrıntılı bir şekilde denetlenmesi ve otomasyon çerçevesi aracılığıyla uygulanması için bu kullanılabilir.

Çoğu senaryoda, Azure Otomasyonu Istenen durum yapılandırması ile birleştirilmiş Microsoft temel VM şablonları, güvenlik gereksinimlerinin karşılanmasına ve bakımında yardımcı olabilir.

- [VM şablonunun nasıl indirileceği hakkında bilgi](/previous-versions/azure/virtual-machines/windows/download-template)

- [ARM şablonları oluşturma hakkında bilgi](../virtual-machines/windows/ps-template.md)

- [Özel bir VM VHD 'sini Azure 'a yükleme](../virtual-machines/windows/upload-generalized-managed.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 7.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-7-4.md)]

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilkeleri, Azure Resource Manager şablonları, Istenen durum yapılandırma betikleri vb. gibi kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure DevOps kullanın.  Azure DevOps 'da, kodunuzun, derlemelerinizin ve iş izlemenin gibi yönettiğiniz kaynaklara erişmek için bu belirli kaynaklar için izinlerinizin olması gerekir. İzinler ve erişim bölümünde açıklandığı gibi, çoğu izin yerleşik güvenlik grupları aracılığıyla verilir. Azure DevOps ile tümleşikse veya TFS ile tümleşikse Active Directory belirli kullanıcılara, yerleşik güvenlik gruplarına veya Azure Active Directory (Azure AD) tanımlanmış gruplara izin verebilir veya vermeyebilirsiniz.

- [Azure DevOps 'da kod depolama](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure DevOps 'da izinler ve gruplar hakkında](/azure/devops/organizations/security/about-permissions)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: özel işletim sistemi görüntülerini güvenli bir şekilde depolayın

**Kılavuz**: özel görüntüler (örn. sanal sabit disk) kullanıyorsanız, yalnızca yetkili kullanıcıların görüntülere erişebildiğinden emin olmak için Azure rol tabanlı erişim denetimlerini kullanın.  

- [Azure 'da RBAC 'yi anlama](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Azure’da RBAC’yi yapılandırma](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: sanal makineleriniz için sistem yapılandırmasını uyarmak, denetlemek ve zorlamak üzere Azure ilkesinden yararlanın. Ayrıca, ilke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: işletim sistemleri için yapılandırma yönetimi araçları dağıtma

**Kılavuz**: Azure Otomasyonu durum yapılandırması, herhangi bir bulutta veya şirket içi veri merkezinde Istenen durum YAPıLANDıRMASı (DSC) düğümleri için bir yapılandırma yönetim hizmetidir. Merkezi bir güvenli konumdan binlerce makine genelinde hızla ve kolayca ölçeklenebilirlik sunar. Makineleri kolayca ekleyebilir, bunlara bildirime dayalı yapılandırmalara atayabilir ve her makinenin, belirttiğiniz istenen durumla uyumluluğunu gösteren raporları görüntüleyebilirsiniz. 

- [Azure Otomasyonu durum yapılandırmasına göre yönetim için makine ekleme](../automation/automation-dsc-onboarding.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Kılavuz**: Azure sanal makineleriniz için temel taramalar gerçekleştirmek üzere Azure Güvenlik Merkezi 'nden yararlanın.  Otomatik yapılandırma için ek yöntemler, Azure Otomasyonu durum yapılandırması 'nı içerir.

- [Azure Güvenlik Merkezi 'nde öneriler nasıl düzeltileceği](../security-center/security-center-remediate-recommendations.md)

- [Azure Otomasyonu durum yapılandırması ile çalışmaya başlama](../automation/automation-dsc-getting-started.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: işletim sistemleri için otomatik yapılandırma izlemeyi Uygula

**Kılavuz**: Azure Otomasyonu durum yapılandırması, herhangi bir bulutta veya şirket içi veri merkezinde Istenen durum YAPıLANDıRMASı (DSC) düğümleri için bir yapılandırma yönetim hizmetidir. Merkezi bir güvenli konumdan binlerce makine genelinde hızla ve kolayca ölçeklenebilirlik sunar. Makineleri kolayca ekleyebilir, bunlara bildirime dayalı yapılandırmalara atayabilir ve her makinenin, belirttiğiniz istenen durumla uyumluluğunu gösteren raporları görüntüleyebilirsiniz.

- [Azure Otomasyonu durum yapılandırmasına göre yönetim için makine ekleme](../automation/automation-dsc-onboarding.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 7.10](../../includes/policy/standards/asb/rp-controls/microsoft.compute-7-10.md)]

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: bulut uygulamalarınız için gizli yönetimi basitleştirmek ve güvenli hale getirmek için Azure Key Vault ile birlikte yönetilen hizmet kimliği kullanın.

- [Azure yönetilen kimliklerle tümleştirme](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault oluşturma](../key-vault/general/quick-create-portal.md)

- [Key Vault kimlik doğrulaması yapma](../key-vault/general/authentication.md)

- [Key Vault erişim ilkesi atama](../key-vault/general/assign-access-policy-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Rehberlik**: Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik ile Azure hizmetleri sağlamak Için Yönetilen kimlikler kullanın. Yönetilen kimlikler, kodunuzda kimlik bilgileri olmadan Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmanıza olanak sağlar.

- [Yönetilen kimlikleri yapılandırma](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

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

**Rehberlik**: kaynaklarınızı sürekli olarak izlemek ve savunmak Için Azure Windows sanal makineleri Için Microsoft kötü amaçlı yazılımdan koruma kullanın.  Azure Linux sanal makinesinde kötü amaçlı yazılımdan koruma için bir üçüncü taraf araca ihtiyacınız olacak. 

- [Cloud Services ve sanal makineler için Microsoft Antimalware 'i yapılandırma](../security/fundamentals/antimalware.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 8.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-8-1.md)]

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: kötü amaçlı yazılımdan koruma yazılımlarının ve imzaların güncelleştirildiğinden emin olun

**Rehberlik**: Windows sanal makineleri için dağıtıldığında, Azure Için Microsoft kötü amaçlı yazılımdan koruma, varsayılan olarak en son imza, platform ve altyapı güncelleştirmelerini otomatik olarak yükler. Azure Güvenlik Merkezi 'ndeki önerileri takip edin: "Işlem &amp; Uygulamaları", tüm uç noktaların en son imzalarla güncel olduğundan emin olmak için. Windows işletim sistemi, Azure Güvenlik Merkezi ile tümleşen Microsoft Defender Gelişmiş tehdit koruması hizmeti ile virüs veya kötü amaçlı yazılım tabanlı saldırılar riskini sınırlamak üzere ek güvenlik ile daha da korunabilir.

Azure Linux sanal makinesinde kötü amaçlı yazılımdan koruma için bir üçüncü taraf araca ihtiyacınız olacak. 

- [Azure Cloud Services ve sanal makineler için Microsoft Antimalware nasıl dağıtılır](../security/fundamentals/antimalware.md)

- [Microsoft Defender Gelişmiş Tehdit Koruması](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure) 

- [Cloud Services ve sanal makineler için Microsoft Antimalware 'i yapılandırma](../virtual-machines/security-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 8.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-8-3.md)]

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli otomatik arka pencerelere emin olun

**Rehberlik**: POWERSHELL veya REST API 'Leri kullanarak Azure sanal makine ölçek kümesi örneği veya örneğe iliştirilmiş yönetilen disk anlık görüntüsünü oluşturun.  Ayrıca, düzenli aralıklarla yedekleme betiklerini yürütmek için Azure Otomasyonu 'nu kullanabilirsiniz.

- [Sanal makine ölçek kümesi örneği ve yönetilen diskin anlık görüntüsünü alma](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance) 

- [Azure Otomasyonu 'na giriş](../automation/automation-intro.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: POWERSHELL veya REST API 'Lerini kullanarak Azure sanal makinelerinizin veya bu örneklere eklenen yönetilen disklerin anlık görüntülerini oluşturun. Azure Key Vault içindeki müşteri tarafından yönetilen anahtarları yedekleyin.

Azure Backup ve hedef Azure sanal makinelerini (VM), ayrıca istediğiniz sıklığı ve bekletme dönemlerini etkinleştirin. Bu, sistem durumu yedeklemesini tamamen içerir. Azure Disk Şifrelemesi kullanıyorsanız, Azure VM yedeklemesi, müşteri tarafından yönetilen anahtarların yedeklemesini otomatik olarak işler.

- [Şifreleme kullanan Azure VM 'lerinde yedekleme](../backup/backup-azure-vms-encryption.md)

- [Azure VM yedeklemesine genel bakış](../backup/backup-azure-vms-introduction.md)

- [Sanal makine ölçek kümesi örneği ve yönetilen diskin anlık görüntüsünü alma](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

- [Azure 'da Anahtar Kasası anahtarlarını yedekleme](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. COMPUTE**:

[!INCLUDE [Resource Policy for Microsoft.Compute 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: Azure Backup içinde yönetilen diskin düzenli aralıklarla veri geri yüklemesini gerçekleştirme olanağı sağlar. Gerekirse, içeriği yalıtılmış bir sanal ağa veya aboneliğe geri yükleme sınamasını yapın. Yedeklenen müşteri tarafından yönetilen anahtarların geri yüklemesini test etmek için müşteri.

Azure Disk Şifrelemesi kullanıyorsanız, sanal makine ölçek kümelerinizi disk şifreleme anahtarlarıyla geri yükleyebilirsiniz. Disk şifrelemeyi kullanırken, Azure VM 'yi disk şifreleme anahtarlarıyla geri yükleyebilirsiniz.

- [Şifreleme kullanan Azure VM 'lerinde yedekleme](../backup/backup-azure-vms-encryption.md)

- [Azure’da bir diski geri yükleme ve kurtarılan bir VM oluşturma](../backup/tutorial-restore-disk.md)

- [Azure 'da Anahtar Kasası anahtarlarını geri yükleme](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Azure sanal makine ölçek kümeleri için disk şifrelemeyi etkinleştirme](disk-encryption-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: kilitleri kullanarak yönetilen disk için korumayı silme özelliğini etkinleştirin. Anahtarları yanlışlıkla veya kötü amaçlı silmeye karşı korumak için Key Vault Soft-Delete ve Temizleme korumasını etkinleştirin.  

- [Beklenmeyen değişiklikleri önlemek için kaynakları kilitleme](../azure-resource-manager/management/lock-resources.md)

- [Azure Key Vault geçici-silme ve Temizleme korumasına genel bakış](../key-vault/general/soft-delete-overview.md)

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

**Rehberlik**: Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin, uyarıyı vermek için kullanılan bulma veya ölçümde ne kadar uygun olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin güven düzeyini temel alır. 

Ayrıca, abonelikleri açıkça işaretleyin (örn. üretim, üretim dışı) etiketleri kullanarak Azure kaynaklarını açıkça tanımlamak ve kategorilere ayırmak için özellikle de hassas verileri işleyen bir adlandırma sistemi oluşturun.  Azure kaynaklarının önem düzeyine ve olayın oluştuğu ortama bağlı olarak uyarıların çözümünde önceliği belirlemek sizin sorumluluğunuzdadır.

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md)

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

- [Azure Güvenlik Merkezi güvenlik Ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: güvenlik uyarılarını olay yanıt sisteminizle birleştirme

**Kılavuz**: Azure kaynaklarına yönelik riskleri belirlemenize yardımcı olmak Için sürekli dışarı aktarma özelliğini kullanarak Azure Güvenlik Merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Sürekli dışa aktarma, uyarıları ve önerileri el ile veya devam eden sürekli bir biçimde dışa aktarmanız sağlar. Azure Güvenlik Merkezi veri bağlayıcısını kullanarak uyarıları Azure Sentinel 'e akışını sağlayabilirsiniz.

- [Sürekli dışarı aktarmayı yapılandırma](../security-center/continuous-export.md)

- [Uyarıların Azure Sentinel’e akışını yapma](../sentinel/connect-azure-security-center.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: güvenlik uyarılarına yanıtı otomatikleştirme

**Kılavuz**: Azure Güvenlik Merkezi 'Nde Iş akışı Otomasyonu özelliğini kullanarak, güvenlik uyarılarındaki "Logic Apps" aracılığıyla yanıtları otomatik olarak tetikleyin ve Azure kaynaklarınızı korumaya yönelik öneriler alın. 

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
