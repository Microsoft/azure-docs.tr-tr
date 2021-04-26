---
title: Azure Işlevleri için Azure Güvenlik temeli
description: Azure Işlevleri güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 90ae774a4e243b854758c1c7a6cc10a882cd584f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831087"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Azure Işlevleri için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 1,0](../security/benchmarks/overview-v1.md) ' den Azure işlevlerine kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar.
İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure işlevleri için geçerli olan ilgili kılavuza göre gruplandırılır. Azure Işlevleri için geçerli olmayan **denetimler** dışlandı.

 
Azure Işlevlerinin Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Azure işlevleri güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: Azure işlevleri uygulamalarınızı bir Azure sanal ağı ile tümleştirin. Premium planda çalışan işlev uygulamaları, "VNet tümleştirme" özelliği de dahil olmak üzere Azure App Service Web uygulamalarıyla aynı barındırma özelliklerine sahiptir.  Azure sanal ağları, Azure Işlevleri gibi Azure kaynaklarınızın çoğunu internet 'e yönlendirilemeyen bir ağa yerleştirmeniz sağlar.

- [Işlevleri bir Azure sanal ağı ile tümleştirme](functions-create-vnet.md)

- [Azure Işlevleri ve Azure App Service için VNET tümleştirmesini anlayın](../app-service/web-sites-integrate-with-vnet.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: sanal ağların, alt ağların ve ağ arabirimlerinin yapılandırma ve trafiğini izleme ve günlüğe kaydetme

**Kılavuz**: Azure Güvenlik Merkezi 'ni kullanın ve Azure işlevleri uygulamalarınızla ilgili ağ kaynaklarının ve ağ yapılandırmalarının güvenliğini sağlamaya yardımcı olmak için ağ koruma önerilerini izleyin.

Azure Işlevleri uygulamasıyla ağ güvenlik grupları kullanıyorsanız, ağ güvenlik grupları akış günlüklerini etkinleştirin ve trafik denetimleri için günlükleri Azure depolama hesabına gönderin. Ayrıca, bir Log Analytics çalışma alanına ağ güvenlik grupları akış günlükleri gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır.

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="13-protect-critical-web-applications"></a>1,3: kritik Web uygulamalarını koruma

**Kılavuz**: Azure işlevleri uç noktalarınızın üretimde tam olarak güvenliğini sağlamak için aşağıdaki işlev uygulama düzeyi güvenlik seçeneklerinden birini uygulamayı göz önünde bulundurmanız gerekir:

- İşlev uygulamanız için App Service kimlik doğrulamasını veya yetkilendirmeyi açın

- İsteklerin kimliğini doğrulamak için Azure API Management (APıM) kullanma

- İşlev uygulamanızı bir Azure App Service Ortamı dağıtın.

Ayrıca, uzaktan hata ayıklamanın üretim Azure işlevleriniz için devre dışı bırakıldığından emin olun. Ayrıca, çıkış noktaları arası kaynak paylaşımı (CORS), tüm etki alanlarının Azure 'daki işlev uygulamanıza erişmesine izin vermemelidir. Yalnızca gerekli etki alanlarının işlev uygulamanızla etkileşime girmesine izin verin.

Gelen trafiğin ek incelemesi için ağ yapılandırmasının bir parçası olarak Azure Web uygulaması güvenlik duvarı (WAF) dağıtımı yapmayı göz önünde bulundurun. WAF ve alma günlükleri için tanılama ayarını bir depolama hesabı, Olay Hub 'ı veya Log Analytics çalışma alanında etkinleştirin. 

- [Üretimde Azure Işlevleri uç noktalarının güvenliğini sağlama](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Azure WAF dağıtma](../web-application-firewall/ag/create-waf-policy-ag.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: DDoS saldırılarına karşı koruma sağlamak için işlev uygulamalarınızla Ilişkili sanal ağlarda DDoS koruma standardını etkinleştirin. Bilinen kötü amaçlı veya kullanılmayan genel IP adresleriyle iletişimleri reddetmek için Azure Güvenlik Merkezi 'ndeki tehdit bilgileri özelliklerini kullanın.

Ayrıca, tüm gelen isteklerin kimliğini doğrulamak ve kötü amaçlı trafiği filtrelemek için Azure Web uygulaması güvenlik duvarı gibi bir ön uç ağ geçidi yapılandırın. Azure Web uygulaması güvenlik duvarı, SQL 'leri, siteler arası betikleri, kötü amaçlı yazılım yüklemelerini ve DDoS saldırılarını engellemek için gelen Web trafiğini inceleyerek işlev uygulamanızın güvenliğini sağlamaya yardımcı olabilir. Web uygulaması güvenlik duvarının tanıtımı için App Service Ortamı veya özel uç noktaların kullanılması gerekir. Premium ve App Service planlarında barındırılan işlevleriniz için özel uç noktaları kullanabilirsiniz.

- [Azure İşlevleri ağ seçenekleri](functions-networking-options.md)

- [DDoS korumasını yapılandırma](../ddos-protection/manage-ddos-protection.md)

- [Azure Güvenlik Merkezi tümleşik tehdit zekasını anlama](../security-center/azure-defender.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Kılavuz**: Azure işlevleri uygulamanız ile ağ güvenlik grubu kullanıyorsanız, ağ güvenlik grubu akış günlüklerini etkinleştirin ve trafik denetimi için günlükleri bir depolama hesabına gönderin. Ayrıca, akış günlüklerini bir Log Analytics çalışma alanına gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır.

- [Ağ güvenlik grubu akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

- [Ağ İzleyicisini etkinleştirme](../network-watcher/network-watcher-create.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: tüm gelen isteklerin kimliğini doğrulamak ve kötü amaçlı trafiği filtrelemek Için Azure Web uygulaması güvenlik duvarı gibi bir ön uç ağ geçidi yapılandırın. Azure Web uygulaması güvenlik duvarı, SQL 'leri, siteler arası betikleri, kötü amaçlı yazılım yüklemelerini ve DDoS saldırılarını engellemek için gelen Web trafiğini inceleyerek, işlev uygulamalarınızın güvenliğini sağlamaya yardımcı olabilir. Web uygulaması güvenlik duvarının tanıtımı için App Service Ortamı veya özel uç noktaların kullanılması gerekir. Premium ve App Service planlarında barındırılan işlevleriniz için özel uç noktaları kullanabilirsiniz.

Alternatif olarak, Azure için giriş algılama veya önleme özelliklerini içeren Azure Marketi 'nde bulunan Barırcuda Web uygulaması güvenlik duvarı gibi birden çok Market seçeneği vardır.

- [Azure İşlevleri ağ seçenekleri](functions-networking-options.md)

- [Azure Işlevleri Premium planı](functions-premium-plan.md)

- [App Service Ortamlarına giriş](../app-service/environment/intro.md)

- [App Service Ortamında ağ konusunda dikkat edilmesi gerekenler](../app-service/environment/network-info.md) 

- [Azure Web uygulaması güvenlik duvarını anlama](../web-application-firewall/overview.md)

- [Azure Işlevleri için özel uç noktaları kullanma](../app-service/networking/private-endpoint.md)

- [Barbcuda WAF bulut hizmetini anlama](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Web uygulamalarına trafiği yönetme

**Rehberlik**: ağınız için uçtan uca TLS şifrelemesi Ile Azure Web uygulaması güvenlik duvarı gibi bir ön uç ağ geçidi yapılandırın. Web uygulaması güvenlik duvarının tanıtımı için App Service Ortamı veya özel uç noktaların kullanılması gerekir. Premium ve App Service planlarında barındırılan işlevleriniz için özel uç noktaları kullanabilirsiniz.

- [Azure İşlevleri ağ seçenekleri](functions-networking-options.md)

- [Azure Işlevleri Premium planı](functions-premium-plan.md)

- [App Service Ortamlarına giriş](../app-service/environment/intro.md)

- [App Service Ortamında ağ konusunda dikkat edilmesi gerekenler](../app-service/environment/network-info.md) 

- [Azure Web uygulaması güvenlik duvarını anlama](../web-application-firewall/overview.md)

- [Portal ile Application Gateway kullanarak uçtan uca TLS Yapılandırma](../application-gateway/end-to-end-ssl-portal.md)

- [Azure Işlevleri için özel uç noktaları kullanma](../app-service/networking/private-endpoint.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Rehberlik**: ağ güvenlik grubundaki veya Azure Güvenlik duvarında ağ erişim denetimlerini tanımlamak Için sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adı (örneğin, Azureuygulamahizmeti) belirterek, karşılık gelen hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

- [Hizmet etiketlerini kullanma hakkında daha fazla bilgi için](../virtual-network/service-tags-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Kılavuz**: Azure işlevleriniz ile ilgili ağ ayarları için standart güvenlik yapılandırması tanımlayın ve uygulayın. Azure işlevlerinizin ağ yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft. Web" ve "Microsoft. Network" ad alanlarında Azure Ilke diğer adlarını kullanın. Azure Işlevleri için yerleşik ilke tanımlarından da yararlanabilirsiniz, örneğin:

- CORS, her kaynağın işlev uygulamalarınıza erişmesine izin vermemelidir

- İşlev uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır

- İşlev uygulamanızda en son TLS sürümü kullanılmalıdır

Ayrıca, Azure Resource Manager şablonları, Azure rol tabanlı erişim denetimi (Azure RBAC) ve tek bir şema tanımında ilkeler gibi anahtar ortam yapıtlarını paketleyerek büyük ölçekli Azure dağıtımlarını basitleştirmek için Azure şemaları 'nı kullanabilirsiniz. Yeni aboneliklere, ortamlara kolayca şema uygulayabilir ve sürüm oluşturma aracılığıyla denetim ve yönetime yönetim sağlayabilirsiniz.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Kılavuz**: Azure işlevleri uygulamanız ile ağ güvenlik grubu kullanıyorsanız, NSG 'ler ve ağ güvenliği ve trafik akışı ile ilgili diğer kaynaklar için Etiketler kullanın. Tek tek ağ güvenlik grubu kuralları için "Açıklama" alanını kullanarak ağa//veya ağ üzerinden trafiğe izin veren tüm kurallar için iş ihtiyacını ve/veya süresini ve bu süreyi belirtin.

Tüm kaynakların etiketlerle oluşturulmasını ve mevcut etiketlenmemiş kaynakları bilgilendirmesini sağlamak için etiketlemeyle ilgili yerleşik Azure ilke tanımlarından herhangi birini ("etiket ve onun değeri gerektir" gibi) kullanın.

Azure PowerShell veya Azure CLı kullanarak, etiketlerine göre kaynaklar üzerinde arama yapabilir veya eylemler gerçekleştirebilirsiniz.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Kılavuz**: Azure etkinlik günlüğü 'nü kullanarak ağ kaynak yapılandırmasını Izleyin ve Azure işlevleri dağıtımlarınızla ilgili ağ ayarları ve kaynakları için değişiklikleri tespit edin. Kritik ağ ayarlarında veya kaynaklarda yapılan değişiklikler gerçekleştiğinde tetiklenecek Azure Izleyici içinde uyarılar oluşturun. 

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: denetim düzlemi denetim günlüğü Için Azure etkinlik günlüğü tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına, Azure Olay Hub 'ına veya Arşiv için Azure depolama hesabına gönderin. Azure etkinlik günlüğü verilerini kullanarak, Azure kaynaklarınızın denetim düzlemi düzeyinde gerçekleştirilen herhangi bir yazma işlemi (PUT, POST, SILME) için "ne, kim ve ne zaman" seçeneğini belirleyebilirsiniz.

Azure Işlevleri, işlevleri izlemek için Azure Application Insights ile yerleşik tümleştirme de sağlar. Application Insights günlük, performans ve hata verilerini toplar. Performans sorunlarını otomatik olarak algılar ve sorunları tanılamanıza ve işlevlerinizin nasıl kullanıldığını anlamanıza yardımcı olacak güçlü analiz araçları içerir.

İşlev uygulamanızda yerleşik özel güvenlik/denetim günlükleriniz varsa, "FunctionAppLogs" Tanılama ayarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına, Azure Olay Hub 'ına veya Arşiv için Azure depolama hesabına gönderin. 

İsteğe bağlı olarak, Azure Sentinel veya bir üçüncü taraf sistem bilgileri ve olay yönetimi çözümünde veri ve şirket içi verileri etkinleştirebilir. 

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](../azure-monitor/essentials/activity-log.md)

- [Azure Işlevleri 'ni Azure Application Insights ayarlama](functions-monitoring.md)

- [Azure Işlevleri için tanılama ayarlarını etkinleştirme (Kullanıcı tarafından oluşturulan Günlükler)](functions-monitor-log-analytics.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: denetim düzlemi denetim günlüğü Için Azure etkinlik günlüğü tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına, Azure Olay Hub 'ına veya Arşiv için Azure depolama hesabına gönderin. Azure etkinlik günlüğü verilerini kullanarak, Azure kaynaklarınızın denetim düzlemi düzeyinde gerçekleştirilen herhangi bir yazma işlemi (PUT, POST, SILME) için "ne, kim ve ne zaman" seçeneğini belirleyebilirsiniz.

İşlev uygulamanızda yerleşik özel güvenlik/denetim günlükleriniz varsa, "FunctionAppLogs" Tanılama ayarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına, Azure Olay Hub 'ına veya Arşiv için Azure depolama hesabına gönderin. 

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](../azure-monitor/essentials/activity-log.md)

- [Azure Işlevleri için tanılama ayarlarını etkinleştirme (Kullanıcı tarafından oluşturulan Günlükler)](functions-monitor-log-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: işletim sistemlerinden güvenlik günlüklerini toplama

**Rehberlik**: uygulanamaz; Bu kılavuz IaaS işlem kaynaklarına yöneliktir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure izleyici 'de, kuruluşunuzun uyumluluk düzenlemelerine göre işlev uygulamalarınızla ilişkili Log Analytics çalışma alanları için günlük tutma süresini ayarlayın.

- [Günlük tutma parametrelerini ayarlama](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Kılavuz**: Azure etkinlik günlüğü tanılama ayarlarını ve işlev uygulamanız için tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına gönderin. Terimleri aramak, eğilimleri belirlemek, desenleri analiz etmek ve toplanan verilere göre birçok diğer öngörü sağlamak için Log Analytics sorguları gerçekleştirin.

Günlük, performans ve hata verilerini toplamak için işlev uygulamalarınızın Application Insights etkinleştirin. Application Insights tarafından toplanan telemetri verilerini Azure portal içinde görüntüleyebilirsiniz.

İşlev uygulamanızda yerleşik özel güvenlik/denetim günlükleriniz varsa, "FunctionAppLogs" Tanılama ayarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına, Azure Olay Hub 'ına veya Arşiv için Azure depolama hesabına gönderin. 

İsteğe bağlı olarak, Azure Sentinel veya bir üçüncü taraf sistem bilgileri ve olay yönetimi çözümünde veri ve şirket içi verileri etkinleştirebilir.

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](../azure-monitor/essentials/activity-log.md)

- [Azure Işlevleri için tanılama ayarlarını etkinleştirme](functions-monitor-log-analytics.md)

- [Azure Işlevleri 'ni Azure Application Insights ayarlama ve telemetri verilerini görüntüleme](functions-monitoring.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Kılavuz**: Azure etkinlik günlüğü tanılama ayarlarını ve işlev uygulamanız için tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına gönderin. Terimleri aramak, eğilimleri belirlemek, desenleri analiz etmek ve toplanan verilere göre birçok diğer öngörü sağlamak için Log Analytics sorguları gerçekleştirin. Log Analytics çalışma alanı sorgularınızı temel alan uyarılar oluşturabilirsiniz.

Günlük, performans ve hata verilerini toplamak için işlev uygulamalarınızın Application Insights etkinleştirin. Application Insights tarafından toplanan telemetri verilerini görüntüleyebilir ve Azure portal içinde uyarı oluşturabilirsiniz.

İsteğe bağlı olarak, Azure Sentinel veya bir üçüncü taraf sistem bilgileri ve olay yönetimi çözümünde veri ve şirket içi verileri etkinleştirebilir.

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](../azure-monitor/essentials/activity-log.md)

- [Azure Işlevleri için tanılama ayarlarını etkinleştirme](functions-monitor-log-analytics.md)

- [Azure Işlevleri için Application Insights etkinleştirme](./configure-monitoring.md#enable-application-insights-integration)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: Azure Active Directory (Azure AD), açıkça atanması ve sorgulanabilir olması gereken yerleşik roller içerir. Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü kullanın.

- [Azure AD 'de PowerShell ile dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Rehberlik**: Azure işlevlerine yönelik denetim düzlemi erişimi, Azure Active Directory (Azure AD) ile denetlenir. Azure AD varsayılan parola kavramına sahip değildir.

Veri düzlemi erişimi, yetkilendirme anahtarları, ağ kısıtlamaları ve bir Azure AD kimliğinin doğrulanması dahil olmak üzere çeşitli yollarla denetlenebilir. Yetkilendirme anahtarları, Azure Işlevleri HTTP uç noktalarınıza bağlanan istemciler tarafından kullanılır ve herhangi bir zamanda yeniden oluşturulabilir. Bu anahtarlar varsayılan olarak yeni HTTP uç noktaları için oluşturulur.

Birden çok dağıtım yöntemi, bir dizi oluşturulan kimlik bilgilerinden faydalanabilir işlev uygulamaları için kullanılabilir. Uygulamanız için kullanılacak dağıtım yöntemlerini gözden geçirin.

- [HTTP uç noktası güvenliğini sağlama](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Yetkilendirme anahtarlarını edinme ve yeniden oluşturma](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Azure Işlevlerinde dağıtım teknolojileri](functions-deployment-technologies.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturun. Yönetim hesaplarının sayısını izlemek için Azure Güvenlik Merkezi kimlik ve erişim yönetimi 'ni kullanın.

Ayrıca, özel yönetim hesaplarını izlemenize yardımcı olmak için Azure Güvenlik Merkezi veya yerleşik Azure Ilkeleri önerilerini kullanabilirsiniz; örneğin:

- Aboneliğinize birden fazla sahip atanmalıdır

- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır

- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır

Ek bilgiler başvurulan bağlantılarda bulunabilir.

- [Azure Güvenlik Merkezi 'ni kullanarak kimlik ve erişimi izleme ](../security-center/security-center-identity-access.md)

- [Azure Ilkesini kullanma](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory çoklu oturum açma kullan (SSO)

**Rehberlik**: mümkün olan yerlerde, işlev uygulamanıza veri erişimi için tek başına bağımsız kimlik bilgilerini yapılandırmak yerine Azure Active Directory (Azure AD) SSO kullanın. Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini kullanın. App Service kimlik doğrulaması/yetkilendirme özelliğini kullanarak işlevleriniz için çoklu oturum açma uygulayın.

- [Azure Işlevlerinde kimlik doğrulama ve yetkilendirmeyi anlama](../app-service/overview-authentication-authorization.md#identity-providers)

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

**Kılavuz**: Azure kaynaklarını açmak ve yapılandırmak için yapılandırılmış çok faktörlü kimlik doğrulaması ile ayrıcalıklı erişim iş istasyonları (Paw) kullanın. 

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: ortamda şüpheli veya güvenli olmayan bir etkinlik oluştuğunda günlükler ve uyarılar oluşturmak için Azure Active Directory (Azure AD) PRIVILEGED IDENTITY Management (PIM) kullanın.

Ayrıca, riskli Kullanıcı davranışında uyarıları ve raporları görüntülemek için Azure AD risk algılamalarını kullanın.

- [Privileged Identity Management dağıtma (PıM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD risk algılamalarını anlama](../active-directory/identity-protection/overview-identity-protection.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: IP adresi aralıklarının veya ülkelerin/bölgelerin yalnızca belirli mantıksal gruplarından Azure Portal erişimine izin vermek Için, koşullu erişim adlı konum kullanın.

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: işlev uygulamalarınız için merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifrelemeyi kullanarak verileri korur. Azure AD Ayrıca, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.

- [İşlev uygulamanızı Azure AD oturum açma bilgilerini kullanacak şekilde yapılandırma](../app-service/configure-authentication-provider-aad.md)

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) eski hesapları keşfetmenize yardımcı olacak Günlükler sağlar. Ayrıca, grup üyeliklerini etkin bir şekilde yönetmek, kurumsal uygulamalara erişmek ve rol atamaları için Azure kimlik erişimi Incelemelerini kullanın. Yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için, Kullanıcı erişimi düzenli olarak incelenebilir.

- [Azure AD raporlamayı anlama](../active-directory/reports-monitoring/index.yml)

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: işlev uygulamalarınız için merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifrelemeyi kullanarak verileri korur. Azure AD Ayrıca, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.

Azure AD oturum açma etkinliğine, denetim ve risk olay günlüğü kaynaklarına erişerek Azure Sentinel veya bir üçüncü taraf SıEM ile tümleştirmenize olanak tanır.

Bu işlemi, Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturarak ve Log Analytics çalışma alanına denetim günlüklerini ve oturum açma günlüklerini göndererek kolaylaştırabilirsiniz. Log Analytics içinde, istenen günlük uyarılarını yapılandırabilirsiniz.

- [İşlev uygulamanızı Azure AD oturum açma bilgilerini kullanacak şekilde yapılandırma](../app-service/configure-authentication-provider-aad.md)

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Yerleşik Azure Sentinel](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapması üzerinde uyarı

**Rehberlik**: işlev uygulamalarınız için merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Denetim düzleminde hesap oturum açma davranışı sapması (Azure portal) için, otomatik yanıtları Kullanıcı kimlikleriyle ilgili şüpheli eylemlere yönelik olarak yapılandırmak için Azure AD Kimlik Koruması ve risk algılama özelliklerini kullanın. Ayrıca, daha fazla araştırma için verileri Azure Sentinel 'e aktarabilirsiniz.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: hassas bilgileri depolayan veya işleyen Azure kaynaklarını izlemeye yardımcı olması için etiketleri kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: geliştirme, test ve üretim için ayrı abonelikler ve/veya yönetim grupları uygulayın. İşlev uygulamalarının sanal ağ (VNet)/subnet ile ayrılması ve uygun şekilde etiketlenmesi gerekir.

Ayrıca, Özel uç noktaları ağ yalıtımı gerçekleştirmek için de kullanabilirsiniz. Azure özel uç noktası, Azure özel bağlantısı tarafından desteklenen bir hizmete özel ve güvenli bir şekilde (örneğin: işlev uygulaması HTTPs uç noktası) bağlanan bir ağ arabirimidir. Özel Uç Nokta, sanal ağınızdaki bir özel IP adresini kullanır ve bu sayede hizmeti sanal ağınıza getirir. Premium ve App Service planlarında barındırılan işlevleriniz için özel uç noktaları kullanabilirsiniz.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Azure İşlevleri ağ seçenekleri](functions-networking-options.md)

- [Azure Işlevleri Premium planı](functions-premium-plan.md)

- [Özel uç noktayı anlama](../private-link/private-endpoint-overview.md)

- [Azure Işlevleri için özel uç noktaları kullanma](../app-service/networking/private-endpoint.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Rehberlik**: işlev uygulamalarınızın Azure Portal, "platform özellikleri: Ağ: SSL" altında "yalnızca https" ayarını etkinleştirin ve en düşük TLS sürümünü 1,2 olarak ayarlayın.

- [İşlev uygulamalarında HTTPS gerektir](./security-concepts.md#require-https)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: Şu anda kullanılamıyor; veri tanımlama, sınıflandırma ve kayıp önleme özellikleri şu anda Azure Işlevleri için kullanılamaz. Bu gibi hassas bilgileri işleyen ve uyumluluk amaçları için gerekliyse üçüncü taraf çözümü uygulayan Işlev uygulamalarını etiketleyin.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: kaynaklara erişimi denetlemek için rol tabanlı erişim denetimi kullanma

**Rehberlik**: işlev uygulaması denetim düzlemi (Azure Portal) için erişimi yönetmek üzere Azure rol tabanlı erişim denetimi 'Ni (Azure RBAC) kullanın.

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: erişim denetimini zorlamak için ana bilgisayar tabanlı veri kaybı önleme kullanın

**Rehberlik**: uygulanamaz; Bu öneri IaaS işlem kaynaklarına yöneliktir.

Microsoft, Azure Işlevleri için temel altyapıyı yönetir ve müşteri verilerinin kaybını veya açıklanmasını engellemek için katı denetimler uygulamıştır.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Rehberlik**: bir işlev uygulaması oluştururken blob, kuyruk ve tablo depolamayı destekleyen genel amaçlı bir Azure depolama hesabı oluşturmanız veya bağlamanız gerekir. Bunun nedeni, Işlevlerin Tetikleyicileri yönetme ve işlev yürütmelerinin günlüğe kaydedilmesini gibi işlemler için Azure Storage 'ı temel aldığından oluşur. Azure depolama, bekleyen bir depolama hesabındaki tüm verileri şifreler. Varsayılan olarak, veriler Microsoft tarafından yönetilen anahtarlarla şifrelenir. Şifreleme anahtarları üzerinde ek denetim için, blob ve dosya verilerinin şifrelenmesi için müşteri tarafından yönetilen anahtarlar sağlayabilirsiniz. Bu anahtarların, depolama hesabına erişebilmesi için işlev uygulamasının Azure Key Vault olması gerekir.

- [Azure Işlevleri için depolama konularını anlayın](storage-considerations.md)

- [Bekleyen veriler için Azure depolama şifrelemesini anlayın](../storage/common/storage-service-encryption.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Rehberlik**: Azure Izleyici 'Yi Azure etkinlik günlüğü ile birlikte kullanarak, üretim işlevi uygulamalarına ve diğer kritik veya ilgili kaynaklara yönelik değişikliklerin ne zaman gerçekleştiği hakkında uyarılar oluşturun.

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: otomatikleştirilmiş güvenlik açığı tarama araçlarını çalıştırma

**Rehberlik**: işlev uygulamalarınızın güvende olmasını sağlamak Için bir DevSecOps uygulaması benimseyin ve bunların yaşam döngüsü süresince mümkün olduğunca güvenli kalmasını sağlayın. DevSecOps, kuruluşunuzun güvenlik ekibini ve yeteneklerini, ekipteki herkesin sorumluluğunda güvenlik sağlamak için DevOps uygulamalarınıza ekler.

Ayrıca, işlev uygulamalarınızın güvenliğini sağlamaya yardımcı olmak için Azure Güvenlik Merkezi 'ndeki önerileri izleyin.

- [CI/CD ardışık düzenine sürekli güvenlik doğrulaması ekleme](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Azure Güvenlik Merkezi güvenlik açığı değerlendirmesi önerilerini uygulama](../security-center/deploy-vulnerability-assessment-vm.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek için risk derecelendirme işlemi kullanın

**Rehberlik**: Microsoft, Azure işlevlerini destekleyen temel sistemler üzerinde güvenlik açığı yönetimi gerçekleştirir, ancak Azure Güvenlik Merkezi 'ndeki önerilerin önem derecesini ve ortamınızdaki riski ölçmek Için güvenli puanı kullanabilirsiniz. Güvenli puanınız, kaç Güvenlik Merkezi önerisi azaldığından temel alır. İlk çözümleme önerilerini önceliklendirmek için, her birinin önem derecesini göz önünde bulundurun.

- [Güvenlik önerileri başvuru kılavuzu](../security-center/recommendations-reference.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: abonelikleriniz dahilinde (işlem, depolama, ağ, bağlantı noktaları ve protokoller vb.) tüm kaynakları sorgulamak/öğrenmek Için Azure Kaynak grafiğini kullanın. Kiracınızda uygun (okuma) izinlere sahip olun ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerini numaralandırın.

Klasik Azure kaynakları kaynak Graph aracılığıyla bulunabilse de, ileri doğru Azure Resource Manager kaynak oluşturmanız ve kullanılması kesinlikle önerilir.

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Kılavuz**: Azure kaynaklarına Etiketler uygulayarak bunları bir taksonomi halinde mantıksal olarak organize etmek için meta veriler verirsiniz.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: Azure kaynaklarını düzenlemek ve izlemek için uygun yerlerde etiketleme, yönetim grupları ve ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

Ayrıca, aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri abonelikleri içinde oluşturulabilecek kaynak türlerine kısıtlamalar koymak için Azure ilkesi 'ni kullanın:

- İzin verilmeyen kaynak türleri

- İzin verilen kaynak türleri

Ek bilgiler başvurulan bağlantılarda bulunabilir.

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

**Rehberlik**: abonelikleriniz içinde oluşturulabilecek kaynak türlerine yönelik kısıtlamalar koymak Için Azure ilkesini kullanın. 

Azure Kaynak Grafiği 'ni kullanarak aboneliklerinde kaynakları sorgulama/bulma.  Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun. 

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerine oluşturulabilecek kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:

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

**Kılavuz**: Azure ilkesiyle işlev uygulamanız için standart güvenlik yapılandırması tanımlayın ve uygulayın. İşlevler uygulamalarınızın yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft. Web" ad alanındaki Azure Ilke diğer adlarını kullanın. Ayrıca, gibi yerleşik ilke tanımlarından da yararlanabilirsiniz:

- Yönetilen kimlik, işlev uygulamanızda kullanılmalıdır

- İşlev uygulamaları için uzaktan hata ayıklama kapatılmalıdır

- işlev uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır

Ek bilgiler başvurulan bağlantılarda bulunabilir.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Kılavuz**: Azure kaynaklarınız genelinde güvenli ayarları zorlamak için Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Rehberlik**: kaynak denetiminde ARM şablonlarını ve özel Azure ilke tanımlarını güvenli bir şekilde depolayın ve yönetin.

- [Kod olarak altyapı nedir?](/azure/devops/learn/what-is-infrastructure-as-code)

- [Kod iş akışları olarak tasarım ilkesi](../governance/policy/concepts/policy-as-code.md)

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos belgeleri](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: yerleşik Azure ilke tanımlarını ve "Microsoft. Web" ad alanındaki Azure ilkesi diğer adlarını kullanarak uyarı, denetim ve sistem yapılandırmalarının uygulanmasını sağlayacak özel ilkeler oluşturun. Ayrıca, ilke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: yerleşik Azure ilke tanımlarını ve "Microsoft. Web" ad alanındaki Azure ilkesi diğer adlarını kullanarak uyarı, denetim ve sistem yapılandırmalarının uygulanmasını sağlayacak özel ilkeler oluşturun. Azure kaynaklarınızın yapılandırmasını otomatik olarak zorlamak için [Denetim], [reddetme] ve [dağıtım yoksa dağıt] Azure ilkesini kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: bulut uygulamalarınız için gizli yönetimi basitleştirmek ve güvenli hale getirmek üzere Azure Key Vault Ile birlikte yönetilen kimlikler kullanın. Yönetilen kimlikler, kodunuzda kimlik bilgileri olmadan Key Vault dahil olmak üzere Azure Active Directory (Azure AD) kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmasını sağlar.

- [Key Vault oluşturma](../key-vault/secrets/quick-create-portal.md)

- [App Service ve Azure Işlevleri için Yönetilen kimlikler kullanma](../app-service/overview-managed-identity.md)

- [Key Vault kimlik doğrulaması yapma](../key-vault/general/authentication.md)

- [Key Vault erişim ilkesi atama](../key-vault/general/assign-access-policy-portal.md)

- [App Service ve Azure Işlevleri için Key Vault başvurularını kullanma](../app-service/app-service-key-vault-references.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Rehberlik**: Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlikle işlev uygulamanızı sağlamak Için Yönetilen kimlikler kullanın. Yönetilen kimlikler, kodunuzda kimlik bilgileri olmadan Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmanıza olanak sağlar.

- [App Service ve Azure Işlevleri için Yönetilen kimlikler kullanma](../app-service/overview-managed-identity.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: kod içinde kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayın. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar. 

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli otomatik arka pencerelere emin olun

**Rehberlik**: uygulamanızın düzenli yedeklemelerini zamanlamak için yedekleme ve geri yükleme özelliğini kullanın. Premium planda çalışan işlev uygulamalarının, "yedekleme ve geri yükleme" özelliğini içeren Azure App Service Web uygulamalarıyla aynı barındırma özelliklerine sahip olması gerekir.

Ayrıca, kodunuzu güvenli bir şekilde depolamak ve yönetmek için Azure Repos ve Azure DevOps gibi bir kaynak denetimi çözümünü de kullanın. Azure DevOps Services donanım hatası, hizmet kesintisi veya bölge çapında olağanüstü durum gibi durumlarda verilerin kullanılabilir olmasını sağlamak için Azure depolama alanlarına özgü birçok farklı özellikten faydalanır. Azure DevOps ekibi ayrıca verileri yanlışlıkla veya kötü niyetli kişiler tarafından silinmeye karşı korumak için gerekli yordamları izler.

- [Uygulamanızı Azure’a yedekleme](../app-service/manage-backup.md)

- [Azure DevOps 'da veri kullanılabilirliğini anlama](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos belgeleri](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: uygulamanızın düzenli yedeklemelerini zamanlamak için yedekleme ve geri yükleme özelliğini kullanın. Premium planda çalışan işlev uygulamalarının, "yedekleme ve geri yükleme" özelliğini içeren Azure App Service Web uygulamalarıyla aynı barındırma özelliklerine sahip olması gerekir. Azure Key Vault içinde müşteri tarafından yönetilen anahtarları yedekleyin.

Ayrıca, kodunuzu güvenli bir şekilde depolamak ve yönetmek için Azure Repos ve Azure DevOps gibi bir kaynak denetimi çözümünü de kullanın. Azure DevOps Services donanım hatası, hizmet kesintisi veya bölge çapında olağanüstü durum gibi durumlarda verilerin kullanılabilir olmasını sağlamak için Azure depolama alanlarına özgü birçok farklı özellikten faydalanır. Azure DevOps ekibi ayrıca verileri yanlışlıkla veya kötü niyetli kişiler tarafından silinmeye karşı korumak için gerekli yordamları izler.

- [Uygulamanızı Azure’a yedekleme](../app-service/manage-backup.md)

- [Azure 'da Anahtar Kasası anahtarlarını yedekleme](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Azure DevOps 'da veri kullanılabilirliğini anlama](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos belgeleri](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: yedekleme ve geri yükleme özelliğinden düzenli aralıklarla geri yükleme gerçekleştirme olanağı sağlayın. Kodunuzu yedeklemek için başka bir çevrimdışı konum kullanılıyorsa, düzenli aralıklarla tüm geri yüklemeler gerçekleştirme yeteneği sağlar. Yedeklenen müşteri tarafından yönetilen anahtarların test geri yüklemesi.

- [Azure 'da bir uygulamayı yedekten geri yükleme](../app-service/web-sites-restore.md)

- [Azure 'da bir uygulamayı bir anlık görüntüden geri yükleme](../app-service/app-service-web-restore-snapshots.md)

- [Azure 'da Anahtar Kasası anahtarlarını geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: yedekleme ve geri yükleme özelliğinden yedeklemeler, aboneliğinizde bir Azure depolama hesabı kullanır. Azure depolama, bekleyen bir depolama hesabındaki tüm verileri şifreler. Varsayılan olarak, veriler Microsoft tarafından yönetilen anahtarlarla şifrelenir. Şifreleme anahtarları üzerinde ek denetim için, depolama verilerinin şifrelenmesi için müşteri tarafından yönetilen anahtarlar sağlayabilirsiniz.

Müşteri tarafından yönetilen anahtarlar kullanıyorsanız, Key Vault içindeki Soft-Delete yanlışlıkla veya kötü amaçlı silmeye karşı anahtarları korumak için etkinleştirildiğinden emin olun.

- [Azure Depolama bekleyen verileri şifreleme](../storage/common/storage-service-encryption.md)

- [Key Vault Soft-Delete etkinleştirme](../storage/blobs/soft-delete-blob-overview.md)

**Sorumluluk**: Paylaşılan

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

**Rehberlik**: Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, uyarı veren etkinliğin arkasında kötü amaçlı bir amaç olduğunu ve uyarıyı vermek için kullanılan analitik düzeyini, ne kadar güvenli bir güvenlik merkezinin olduğunu temel alır.

Ayrıca, abonelikleri açıkça işaretleyin (örn. üretim, üretim dışı) ve Azure kaynaklarını net bir şekilde tanımlamak ve kategorilere ayırmak için bir adlandırma sistemi oluşturun.

**Sorumluluk**: Paylaşılan

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

**Kılavuz**: Azure Güvenlik Merkezi 'Nde Iş akışı Otomasyonu özelliğini kullanarak güvenlik uyarılarına yönelik yanıtları ve Logic Apps önerileri otomatik olarak tetikleyin.

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

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](../security/benchmarks/overview.md)
- [Azure güvenlik temelleri](../security/benchmarks/security-baselines-overview.md) hakkında daha fazla bilgi edinin
