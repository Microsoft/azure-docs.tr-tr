---
title: Logic Apps için Azure Güvenlik temeli
description: Logic Apps güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: logic-apps
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0b74962a192de6a10c09b9855780ed0cd6244515
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967307"
---
# <a name="azure-security-baseline-for-logic-apps"></a>Logic Apps için Azure Güvenlik temeli

Bu güvenlik temeli, Logic Apps için [Azure Güvenlik kıyaslama sürüm 1,0](../security/benchmarks/overview-v1.md) ' den rehberlik uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Logic Apps için geçerli olan ilgili kılavuza göre gruplandırılır. Logic Apps için geçerli olmayan **denetimler** veya Microsoft 'un hangi sorumluluğun çıkarılmadığı.

Logic Apps Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Logic Apps güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: "genel," çok kiracılı Logic Apps hizmetinde çalışan bağlayıcılar Microsoft tarafından dağıtılır ve yönetilir. Bu bağlayıcılar; Office 365, Azure Blob depolama, SQL Server, Dynamics, Salesforce, SharePoint ve daha fazlası dahil olmak üzere bulut hizmetlerine, şirket içi sistemlere veya her ikisine de erişmek için Tetikleyiciler ve eylemler sağlar. Ağ güvenlik grubunuzdaki veya Azure Güvenlik duvarınızdaki kuralları belirtirken ilgili kaynaklara erişime izin vermek için AzureConnectors Service etiketini kullanabilirsiniz.

Bir Azure sanal ağındaki kaynaklara doğrudan erişmesi gereken Logic Apps için, mantıksal uygulamalarınızı özel kaynaklarda oluşturabileceğiniz, dağıtabileceğiniz ve çalıştırabileceğiniz bir tümleştirme hizmeti ortamı (ıSE) oluşturabilirsiniz. Azure depolama, Azure Cosmos DB, Azure SQL veritabanı, iş ortağı hizmetleri veya Azure 'da barındırılan müşteri hizmetleri gibi Azure PaaS hizmetlerine erişim sağlamak için bazı Azure sanal ağları özel uç noktalar (Azure özel bağlantı) kullanır. Mantıksal uygulamalarınızın özel uç noktaları kullanan sanal ağlara erişmesi gerekiyorsa, bu mantık uygulamalarını bir ıSE içinde oluşturmanız, dağıtmanız ve çalıştırmanız gerekir.

ISE 'yi oluşturduğunuzda, iç veya dış erişim uç noktaları kullanmayı tercih edebilirsiniz. Seçiminiz, işinizdeki Logic Apps 'teki istek veya Web kancasının, sanal ağınızın dışından çağrı alıp alamayacağını belirler. İç ve dış erişim uç noktaları Ayrıca, sanal ağınızın içinden veya dışından bir çalıştırmanın giriş ve çıkışları dahil olmak üzere mantıksal uygulamanızın çalıştırma geçmişini görüntüleyip görüntüleyemeyeceğinizi de etkiler.

ISE ile ilgili tüm sanal ağ alt ağ dağıtımlarının, uygulamanızın güvenilen bağlantı noktalarına ve kaynaklarına özel ağ erişim denetimleriyle uygulanmış bir ağ güvenlik grubu olduğundan emin olun. Logic Apps 'i bir ıSE dağıtırken, özel bağlantı kullanın. Azure özel bağlantısı, sanal ağınızdaki özel bir uç nokta üzerinden Azure PaaS hizmetlerine ve Azure 'da barındırılan müşteri/iş ortağı hizmetlerine erişmenizi sağlar. Alternatif olarak, belirli bir kullanım durumu varsa, Azure Güvenlik Duvarı 'Nı uygulayarak bu gereksinimi karşılayabilirsiniz. Güvenlik kurallarını ayarlarken karmaşıklığı azaltmaya yardımcı olmak için, belirli bir Azure hizmeti için IP adresi ön eklerinin gruplarını temsil eden hizmet etiketlerini kullanın.

- [Logic Apps için bağlayıcıları anlayın](../connectors/apis-list.md)

- [Azure 'da hizmet etiketlerini anlama](../virtual-network/service-tags-overview.md)

- [Tümleştirme hizmeti ortamlarını (sesleri) kullanarak Azure Logic Apps Azure sanal ağ kaynaklarına erişimi anlayın](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Sanal ağ hizmeti uç noktalarını anlama](../virtual-network/virtual-network-service-endpoints-overview.md)

- [Azure özel bağlantısını anlama](../private-link/private-link-overview.md)

- [ISE uç noktası erişimini anlama](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview#ise-endpoint-access)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Güvenlik duvarını dağıtma ve yapılandırma](../firewall/tutorial-firewall-deploy-portal.md)

- [ISE için erişimi etkinleştirme](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment#enable-access-for-ise)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: sanal ağların, alt ağların ve ağ arabirimlerinin yapılandırma ve trafiğini izleme ve günlüğe kaydetme

**Kılavuz**: bir dış erişim noktası kullanan bir tümleştirme hizmeti ortamında (ISE) Logic Apps çalıştırırsanız, veri sızdırma riskini azaltmak için bir ağ güvenlik grubu (NSG) kullanabilirsiniz. Trafik denetimi için NSG akış günlüklerini etkinleştirin ve günlükleri bir Azure depolama hesabına gönderin. Ayrıca, NSG akış günlüklerini bir Log Analytics çalışma alanına gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır.

- [ISE uç noktası erişimini anlama](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview#ise-endpoint-access)

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: mantıksal uygulamanız, Istek veya Web kancası tetikleyicisi gibi gelen çağrıları veya istekleri alan istek tabanlı bir tetikleyici kullanıyorsa, yalnızca yetkili istemcilerin mantıksal uygulamanızı çağırabilmesi için erişimi sınırlayabilirsiniz.

Bir tümleştirme hizmeti ortamında (ıSE) Logic Apps çalıştırıyorsanız, DDoS saldırılarına karşı koruma sağlamak için ıSE ile ilişkili sanal ağ üzerinde DDoS koruma standardını etkinleştirin. Bilinen kötü amaçlı veya kullanılmayan Internet IP adresleriyle iletişimleri reddetmek için Azure Güvenlik Merkezi tümleşik tehdit zekasını kullanın.

Tehdit zekası etkinleştirilmiş ve kötü amaçlı ağ trafiği için "uyarı ve reddetme" olarak yapılandırılmış bir kuruluşun ağ sınırlarının her birinde Azure Güvenlik Duvarı 'Nı dağıtın.

NSG 'leri, sınırlı bir süre için bitiş noktaları onaylı IP adresleriyle sınırlamak üzere Azure Güvenlik Merkezi 'ni tam zamanında ağ erişimi ile kullanın.

Bağlantı noktalarını ve kaynak IP 'Leri gerçek trafik ve tehdit zeka göre sınırlayan NSG yapılandırmalarının kullanılması için Azure Güvenlik Merkezi Uyarlamalı ağ sağlamlaştırma kullanın.

- [Logic Apps gelen çağrıları güvenli hale getirme](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-inbound-requests)

- [Gelen IP adreslerini kısıtlama](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#restrict-inbound-ip-addresses)

- [DDoS korumasını yapılandırma](../ddos-protection/manage-ddos-protection.md)

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Güvenlik Merkezi tümleşik tehdit zekasını anlama](../security-center/azure-defender.md)

- [Azure Güvenlik Merkezi Uyarlamalı ağ sağlamlaştırma 'yi anlama](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Güvenlik Merkezi 'Ni tam zamanında ağ Access Control anlama](../security-center/security-center-just-in-time.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Kılavuz**: bir dış erişim noktası kullanan bir tümleştirme hizmeti ortamında (ISE) Logic Apps çalıştırırsanız, veri sızdırma riskini azaltmak için bir ağ güvenlik grubu (NSG) kullanabilirsiniz. Trafik denetimi için NSG akış günlüklerini etkinleştirin ve günlükleri bir Azure depolama hesabına gönderin. Ayrıca, NSG akış günlüklerini bir Log Analytics çalışma alanına gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır.

Ağ trafiğiyle ilgili daha fazla koruma ve bilgi sağlamak için, yalnızca bunları her bir Application Gateway örneği üzerinde etkinleştirdiyseniz oluşturulan erişim günlüklerine başvurabilirsiniz. Bu günlüğü, Application Gateway erişim düzenlerini görüntülemek ve önemli bilgileri çözümlemek için kullanabilirsiniz. Bu, arayanın IP, istenen URL, yanıt gecikmesi, dönüş kodu ve gelen ve giden baytları içerir.

Aksi takdirde, bu gereksinimi karşılamak için Market 'ten bir üçüncü taraf çözümünü kullanabilirsiniz.

- [ISE uç noktası erişimini anlama](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview#ise-endpoint-access)

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

- [Application Gateway ile iç VNET 'te API Management tümleştirme](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [WAF erişim günlüklerini anlama](https://docs.microsoft.com/azure/web-application-firewall/ag/web-application-firewall-logs#access-log)

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

**Rehberlik**: bir tümleştirme hizmeti ortamında (ISE) Logic Apps çalıştırıyorsanız Azure Application Gateway dağıtın.

- [Application Gateway ile iç VNET 'te API Management tümleştirme](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Application Gateway HTTPS kullanacak şekilde yapılandırma](../application-gateway/create-ssl-portal.md) 

- [Azure Web uygulaması ağ geçitleri ile katman 7 yük dengelemesini anlama](../application-gateway/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Rehberlik**: Azure Logic Apps örneklerine erişmesi gereken kaynaklar için, ağ güvenlik gruplarında veya Azure Güvenlik duvarında ağ erişim denetimleri tanımlamak üzere sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adı (örneğin, LogicApps, LogicAppsManagement) belirterek, karşılık gelen hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

- [Hizmet etiketlerini kullanma hakkında daha fazla bilgi için](../virtual-network/service-tags-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Rehberlik**: Azure ilkesiyle Azure Logic Apps örneklarınızla ilgili ağ kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın. Azure Logic Apps örneklerinizin ağ yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için, "Microsoft. Logic" ve "Microsoft. Network" ad alanlarında Azure Ilke diğer adlarını kullanın. Ayrıca, gibi yerleşik ilke tanımlarından da yararlanabilirsiniz:

- Logic Apps tanılama günlükleri etkinleştirilmelidir

- DDoS koruma standardı etkinleştirilmelidir

Ayrıca, tek bir şema tanımında Azure Resource Manager şablonları, Azure rol tabanlı erişim denetimi (Azure RBAC) ve ilkeler gibi temel ortam yapıtlarını paketleyerek büyük ölçekli Azure dağıtımlarını basitleştirmek için Azure şemaları 'nı kullanabilirsiniz. Şema 'i yeni abonelikler ve ortamlara kolayca uygulayın ve sürüm oluşturma aracılığıyla denetimi ve yönetimi ayrıntılı olarak ayarlayın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: bireysel NSG kuralları Için "Açıklama" alanını kullanarak ağa giden/giden trafiğe izin veren kuralların iş gereksinimini ve/veya süresini (vs.) belirtin.

Tüm kaynakların etiketlerle oluşturulmasını ve mevcut etiketlenmemiş kaynakları bilgilendirmesini sağlamak için etiketlemeyle ilgili yerleşik Azure Ilke tanımlarından herhangi birini ("etiket ve onun değeri gerektir" gibi) kullanın.

Azure PowerShell veya Azure CLı kullanarak, etiketlerine göre kaynakları arayabilir veya bunlarla ilgili eylemler gerçekleştirebilirsiniz.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

- [Logic Apps için Azure Ilke tanımlarının listesi](policy-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Rehberlik**: ağ kaynak yapılandırmasını izlemek ve Azure Logic Apps örneklarınızla ilgili ağ kaynaklarına yönelik değişiklikleri algılamak Için Azure etkinlik günlüğünü kullanın. Kritik ağ kaynaklarında yapılan değişiklikler yürürlüğe girdiğinde tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](/azure/azure-monitor/platform/alerts-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Kılavuz**: çalışma zamanı sırasında mantıksal uygulamalarınız hakkında daha zengin hata ayıklama bilgileri almak Için Azure izleyici günlüklerini ayarlayıp kullanarak, olayları tetikleme, çalıştırma olayları ve Log Analytics çalışma alanında eylem olayları gibi çalışma zamanı verileri ve olayları hakkında bilgi kaydedebilirsiniz. Azure İzleyici, bulut ve şirket içi ortamlarınızı izleyerek bunların kullanılabilirliğini ve performansını daha kolay korumanıza yardımcı olur. Azure Izleyici günlüklerini kullanarak, bu bilgileri toplayıp gözden geçirmenize yardımcı olan günlük sorguları oluşturabilirsiniz. Bu tanılama verilerini Azure depolama ve Azure Event Hubs gibi diğer Azure hizmetleriyle de kullanabilirsiniz.

Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz. 

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](/azure/azure-monitor/platform/activity-log)

- [Azure Izleyici günlüklerini ayarlama ve Azure Logic Apps için tanılama verilerini toplama](monitor-logic-apps-log-analytics.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Kılavuz**: çalışma zamanı sırasında mantıksal uygulamalarınız hakkında daha zengin hata ayıklama bilgileri almak Için Azure izleyici günlüklerini ayarlayıp kullanarak, olayları tetikleme, çalıştırma olayları ve Log Analytics çalışma alanında eylem olayları gibi çalışma zamanı verileri ve olayları hakkında bilgi kaydedebilirsiniz. Azure İzleyici, bulut ve şirket içi ortamlarınızı izleyerek bunların kullanılabilirliğini ve performansını daha kolay korumanıza yardımcı olur. Azure Izleyici günlüklerini kullanarak, bu bilgileri toplayıp gözden geçirmenize yardımcı olan günlük sorguları oluşturabilirsiniz. Bu tanılama verilerini Azure depolama ve Azure Event Hubs gibi diğer Azure hizmetleriyle de kullanabilirsiniz.

Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz. 

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](/azure/azure-monitor/platform/activity-log)

- [Azure Izleyici günlüklerini ayarlama ve Azure Logic Apps için tanılama verilerini toplama](monitor-logic-apps-log-analytics.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Logic**:

[!INCLUDE [Resource Policy for Microsoft.Logic 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.logic-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Rehberlik**: bir mantıksal uygulama oluşturup çalıştırdıktan sonra, bu mantıksal uygulamanın çalıştırma durumunu, çalışma geçmişi, tetikleme geçmişini ve performansı kontrol edebilirsiniz. Gerçek zamanlı olay izleme ve daha zengin hata ayıklama için, Azure Izleyici günlüklerini kullanarak mantıksal uygulamanız için tanılama günlüğü ayarlayın. Bu Azure hizmeti, kullanılabilirliğini ve performansını daha kolay koruyabilmeniz için bulutunuzu ve şirket içi ortamlarınızı izlemenize yardımcı olur. Daha sonra olayları tetikleme, olayları çalıştırma ve eylem olayları gibi olayları bulabilir ve görüntüleyebilirsiniz. Bu bilgileri Azure Izleyici günlüklerinde depolayarak, bu bilgileri bulmanıza ve çözümlemenize yardımcı olan günlük sorguları oluşturabilirsiniz. Bu tanılama verilerini Azure depolama ve Azure Event Hubs gibi diğer Azure hizmetleriyle de kullanabilirsiniz.

Azure Izleyici 'de, kuruluşunuzun uyumluluk düzenlemelerine göre Azure Logic Apps örnekleriyle ilişkili Günlükler için günlük tutma süresini ayarlayın.

- [Çalışma durumunu izleme, tetikleyici geçmişini inceleme ve Azure Logic Apps için uyarıları ayarlama](monitor-logic-apps.md)

- [Günlük tutma parametrelerini ayarlama](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Kılavuz**: mantıksal uygulamanız için günlük kaydı ayarlamak için, mantıksal uygulamanızı oluştururken Log Analytics etkinleştirebilir veya mevcut Logic Apps için Log Analytics çalışma alanınıza Logic Apps yönetimi çözümünü yükleyebilirsiniz. Bu çözüm, mantıksal uygulama çalışmalarınız için toplu bilgiler sağlar ve durum, yürütme süresi, yeniden gönderme görevi durumu ve bağıntı kimlikleri gibi belirli ayrıntıları içerir. Daha sonra, bu bilgiler için günlüğe kaydetmeyi etkinleştirmek ve sorgular oluşturmak için Azure Izleyici günlüklerini ayarlayın.

Azure etkinlik günlüğü tanılama ayarlarını da etkinleştirebilir ve günlükleri bir Log Analytics çalışma alanına gönderebilirsiniz. Terimleri aramak, eğilimleri belirlemek, desenleri analiz etmek ve Azure Logic Apps için toplanabilecek etkinlik günlüğü verilerine göre birçok diğer öngörü sağlamak için Log Analytics sorguları gerçekleştirin.

Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz. 

- [Azure Izleyici günlüklerini ayarlama ve Azure Logic Apps için tanılama verilerini toplama](monitor-logic-apps-log-analytics.md)

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](/azure/azure-monitor/platform/activity-log)

- [Azure Izleyici 'de Log Analytics Azure etkinlik günlüklerini toplama ve çözümleme](/azure/azure-monitor/platform/activity-log-collect)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Rehberlik**: güvenlik günlükleri ve olayları 'nda bulunan anormal etkinlikleri izlemek ve uyarmak için Log Analytics Ile Azure Güvenlik Merkezi 'ni kullanın.

Alternatif olarak, Azure Sentinel 'de ve yerleşik verileri etkinleştirebilir.

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Güvenlik Merkezi 'nde uyarıları yönetme](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics günlük verilerinde uyarı alma](/azure/azure-monitor/learn/tutorial-response)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: Azure Active Directory (Azure AD), açıkça atanması ve sorgulanabilir olması gereken yerleşik roller içerir. Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü kullanın.

Azure AD tarafından korunan diğer kaynaklara kolayca erişmek ve oturum açmadan kimliğinizi doğrulamak için, mantıksal uygulamanız kimlik bilgileri veya gizli dizileri yerine yönetilen bir kimlik (eski adıyla Yönetilen Hizmet Kimliği veya MSI) kullanabilir. Azure bu kimliği sizin için yönetir ve gizli dizi sağlamak veya döndürmek zorunda olmadığınızdan kimlik bilgilerinizin güvenliğinin sağlanmasına yardımcı olur.

Bir mantıksal uygulamadaki her istek uç noktası, uç noktanın URL 'sinde bir paylaşılan erişim Imzasına (SAS) sahiptir. Diğer kişilerle istek tabanlı bir tetikleyici için uç nokta URL 'SI paylaşırsanız, belirli anahtarları kullanan ve sona erme tarihleri olan geri çağırma URL 'Leri oluşturabilirsiniz. Bu şekilde, belirli bir TimeSpan temelinde, mantıksal uygulamanızı tetikleyerek anahtarları sorunsuzca alabilir veya erişimi kısıtlayabilirsiniz.

- [Azure AD 'de PowerShell ile dizin rolü alma](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Azure Logic Apps içindeki yönetilen kimlikleri kullanarak Azure kaynaklarına erişimi kimlik doğrulaması](create-managed-service-identity.md)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [SAS kullanarak Azure Logic Apps erişimi ve verileri güvenli hale getirme](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#sas)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Rehberlik**: Azure Active Directory (Azure AD) ve Azure Logic Apps varsayılan parola kavramına sahip değildir.

Temel kimlik doğrulaması kullanılıyorsa, bir Kullanıcı adı ve parola belirtmeniz gerekir. Bu kimlik bilgilerini oluştururken kimlik doğrulaması için güçlü bir parola yapılandırmadiğinizden emin olun.

Kod olarak altyapı kullanıyorsanız, parolaları kodda depolamaktan kaçının ve bunun yerine kimlik bilgilerini depolamak ve almak için Azure Key Vault kullanın.

- [Logic Apps verileri güvenli hale getirme ve verilere erişme](logic-apps-securing-a-logic-app.md)

- [Azure Key Vault bir gizli dizi ayarlama ve alma](../key-vault/general/quick-create-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturun. Yönetim hesaplarının sayısını izlemek için Azure Güvenlik Merkezi kimlik ve erişim yönetimi 'ni kullanın.

Ayrıca, özel yönetim hesaplarını izlemenize yardımcı olmak için Azure Güvenlik Merkezi veya yerleşik Azure Ilkeleri önerilerini kullanabilirsiniz; örneğin:

- Aboneliğinize birden fazla sahip atanmalıdır
- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Kimlik ve erişim (Önizleme) izlemek için Azure Güvenlik Merkezi 'ni kullanma](../security-center/security-center-identity-access.md)

- [Azure Ilkesini kullanma](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory çoklu oturum açma kullan (SSO)

**Rehberlik**: API çağrıları aracılığıyla kurtarma hizmetleri kasalarınızla etkileşim kurmak için kullanılabilecek bir belirteç almak üzere bir Azure uygulama kaydı (hizmet sorumlusu) kullanın.

Birçok bağlayıcı, mantıksal uygulamanızda bir tetikleyici veya eylem kullanabilmeniz için öncelikle hedef hizmet veya sisteme bir bağlantı oluşturmanızı ve kimlik doğrulama kimlik bilgilerini ya da diğer yapılandırma ayrıntılarını sağlamanızı gerektirir. Örneğin, verilere erişim veya sizin adınıza gönderi için Twitter hesabına bir bağlantı yetkilendirmelisiniz.

Azure Active Directory (Azure AD) OAuth kullanan bağlayıcılar için bağlantı oluşturmak, erişim belirtecinizin şifreli ve güvenli bir şekilde bir Azure gizli deposunda depolandığı Office 365, Salesforce veya GitHub gibi hizmette oturum açma anlamına gelir. FTP ve SQL gibi diğer bağlayıcılar, sunucu adresi, Kullanıcı adı ve parola gibi yapılandırma ayrıntılarına sahip bir bağlantı gerektirir. Bu bağlantı yapılandırma ayrıntıları da şifrelenir ve güvenli bir şekilde depolanır.

- [Azure REST API 'Lerini çağırma](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [İstemci uygulamanızı Azure AD 'ye kaydetme](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [İş akışı API bilgilerini tetikler](/rest/api/logic/workflowtriggers)

- [Bağlayıcı yapılandırmasını anlama](../connectors/apis-list.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin ve Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini izleyin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: yönetim görevleri için güvenli, Azure tarafından yönetilen iş istasyonları kullanın

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

Ayrıca, bir mantıksal uygulamadaki her istek uç noktası, uç noktanın URL 'sinde bir paylaşılan erişim Imzasına (SAS) sahiptir. Mantıksal uygulamanızı yalnızca belirli IP adreslerinden gelen istekleri kabul edecek şekilde kısıtlayabilirsiniz.

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

- [Logic Apps gelen IP adreslerini nasıl kısıtlayacağınızı anlayın](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#restrict-inbound-ip-addresses)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: Azure Logic Apps örneklerinizin merkezi kimlik doğrulaması ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifrelemeyi kullanarak verileri korur. Azure AD Ayrıca, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.

Logic Apps desteklendiği yerlerde, Azure AD tarafından korunan diğer kaynaklara kolayca erişmek ve kimlik bilgileri veya gizli dizileri yerine oturum açmadan kimliğinizi doğrulamak için yönetilen bir kimlik kullanın. Azure bu kimliği sizin için yönetir ve gizli dizi sağlamak veya döndürmek zorunda olmadığınızdan kimlik bilgilerinizin güvenliğinin sağlanmasına yardımcı olur.

Azure Logic Apps, hem sistem tarafından atanan hem de kullanıcı tarafından atanan kimlikleri destekler. Mantıksal uygulamanız, sistem tarafından atanan kimliği veya tek bir kullanıcı tarafından atanan kimliği kullanabilir ve bunu aynı gruptaki mantıksal uygulamalar arasında paylaşabilirsiniz (ikisini birden paylaşmak mümkün değildir). Şu anda yalnızca belirli yerleşik Tetikleyiciler ve Eylemler, yönetilmeyen bağlayıcılar veya bağlantılar değil, yönetilen kimlikleri destekler:

- HTTP
- Azure İşlevleri
- Azure API Management
- Azure Uygulama Hizmetleri

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure Logic Apps içindeki yönetilen kimlikleri kullanarak Azure kaynaklarına erişimi kimlik doğrulaması](create-managed-service-identity.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) eski hesapları keşfetmenize yardımcı olacak Günlükler sağlar. Ayrıca, grup üyeliklerini etkin bir şekilde yönetmek, kurumsal uygulamalara erişmek ve rol atamaları için Azure kimlik erişimi Incelemelerini kullanın. Yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için, Kullanıcı erişimi düzenli olarak incelenebilir.

- [Azure AD raporlamayı anlama](/azure/active-directory/reports-monitoring/)

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: Azure Logic Apps örneklerinizin merkezi kimlik doğrulaması ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifrelemeyi kullanarak verileri korur. Azure AD Ayrıca, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.

Azure AD oturum açma etkinliğine, denetim ve risk olay günlüğü kaynaklarına erişerek Azure Sentinel veya bir üçüncü taraf SıEM ile tümleştirmenize olanak tanır.

Bu işlemi, Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturarak ve Log Analytics çalışma alanına denetim günlüklerini ve oturum açma günlüklerini göndererek kolaylaştırabilirsiniz. Log Analytics içinde, istenen günlük uyarılarını yapılandırabilirsiniz.

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Yerleşik Azure Sentinel](../sentinel/quickstart-onboard.md)

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

**Rehberlik**: hassas bilgileri depolayan veya işleyen Azure kaynaklarını izlemeye yardımcı olması için etiketleri kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: "genel," çok kiracılı Logic Apps hizmetinde çalışan bağlayıcılar Microsoft tarafından dağıtılır ve yönetilir. Bu bağlayıcılar; Office 365, Azure Blob depolama, SQL Server, Dynamics, Salesforce, SharePoint ve daha fazlası dahil olmak üzere bulut hizmetlerine, şirket içi sistemlere veya her ikisine de erişmek için Tetikleyiciler ve eylemler sağlar.

Bir Azure sanal ağındaki kaynaklara doğrudan erişmesi gereken Logic Apps için, mantıksal uygulamalarınızı özel kaynaklarda oluşturabileceğiniz, dağıtabileceğiniz ve çalıştırabileceğiniz bir tümleştirme hizmeti ortamı (ıSE) oluşturabilirsiniz. Azure depolama, Azure Cosmos DB veya Azure SQL veritabanı, iş ortağı hizmetleri veya Azure 'da barındırılan müşteri hizmetleri gibi Azure PaaS hizmetlerine erişim sağlamak için bazı Azure sanal ağları özel uç noktalar (Azure özel bağlantı) kullanır. Mantıksal uygulamalarınızın özel uç noktaları kullanan sanal ağlara erişmesi gerekiyorsa, bu mantık uygulamalarını bir ıSE içinde oluşturmanız, dağıtmanız ve çalıştırmanız gerekir.

ISE 'yi oluşturduğunuzda, iç veya dış erişim uç noktaları kullanmayı tercih edebilirsiniz. Seçiminiz, işinizdeki Logic Apps 'teki istek veya Web kancasının, sanal ağınızın dışından çağrı alıp alamayacağını belirler.

Ayrıca, ortam türü ve veri duyarlılığı düzeyi gibi bireysel güvenlik etki alanları için ayrı abonelikler ve yönetim grupları kullanarak yalıtım uygulayın. Uygulamalarınızın ve kurumsal ortamların talep ettiği Azure kaynaklarınıza erişim düzeyini kısıtlayabilirsiniz. Azure rol tabanlı erişim denetimi (Azure RBAC) aracılığıyla Azure kaynaklarına erişimi denetleyebilirsiniz.

- [Logic Apps için bağlayıcıları anlayın](../connectors/apis-list.md)

- [Tümleştirme hizmeti ortamlarını (sesleri) kullanarak Azure Logic Apps Azure sanal ağ kaynaklarına erişim](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](/azure/governance/management-groups/create)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: Şu anda kullanılamıyor; veri tanımlama, sınıflandırma ve kayıp önleme özellikleri henüz Azure Logic Apps için kullanılamaz.

Hassas bilgilerin yetkisiz aktarımını izleyen ve bilgi güvenliği uzmanlarına uyarı ederken bu tür aktarımları engelleyen bir üçüncü taraf çözümünü Azure Marketi 'nden yararlanın. 

Microsoft, Azure Logic Apps için temel altyapıyı yönetir ve müşteri verilerinin kaybını veya açıklanmasını engellemek için katı denetimler uygulamıştır.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Rehberlik**: yoldaki tüm hassas bilgileri şifreleyin. Azure Logic Apps, mantıksal uygulama çalıştırma sırasındaki tüm veriler Aktarım Katmanı Güvenliği (TLS) ve bekleyen ' i kullanarak geçiş sırasında şifrelenir. Mantıksal uygulamanızın çalıştırma geçmişini görüntülediğinizde Logic Apps erişiminizin kimliğini doğrular ve ardından her bir çalıştırmaya yönelik istekler ve yanıtlara yönelik giriş ve çıkışlara bağlantılar sağlar. Ancak, herhangi bir parolayı, parolayı, anahtarı veya diğer hassas bilgileri işleyen eylemler için başkalarının bu verilere bakmasını ve erişimini engellemek isteyebilirsiniz. Örneğin, mantıksal uygulamanız bir HTTP eyleminin kimlik doğrulaması sırasında kullanmak üzere Azure Key Vault bir gizli dizi alırsa, bu gizli anahtarı görünümden gizlemek istersiniz.

Istek tetikleyicisi, gelen istekler için yalnızca Aktarım Katmanı Güvenliği (TLS) 1,2 ' i destekler. Azure kaynaklarınıza bağlanan tüm istemcilerin TLS 1,2 veya üzerini anlaşamadığından emin olun. HTTP bağlayıcısını kullanan giden çağrılar Aktarım Katmanı Güvenliği (TLS) 1,0, 1,1 ve 1,2 ' i destekler. 

Azure Güvenlik Merkezi önerilerini, varsa, bekleyen ve geçişte şifreleme için kullanın.

- [Azure Logic Apps-istek tabanlı tetikleyicilere gelen çağrılar için güvenli erişim ve veriler](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-inbound-requests)

- [Azure Logic Apps-diğer hizmetlere ve sistemlere giden aramalar için güvenli erişim ve veri](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-outbound-requests)

- [Azure ile iletim sırasında şifrelemeyi anlama](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

- [Azure ile bekleyen veri şifrelemesini anlama](../security/fundamentals/encryption-atrest.md)

- [Azure Logic Apps içindeki tümleştirme hizmeti ortamları (sesleri) için bekleyen verileri şifrelemek üzere müşteri tarafından yönetilen anahtarlar ayarlayın](customer-managed-keys-integration-service-environment.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: Azure Logic Apps, birçok tetikleyici ve eylemin, girdileri, çıkışları veya her ikisini de bir mantıksal uygulamanın çalıştırma geçmişinden obscuring göre güvenli hale getirmek için etkinleştirebileceğiniz ayarlar vardır.

Microsoft, Azure Logic Apps için temel altyapıyı yönetir ve müşteri verilerinin kaybını veya açıklanmasını engellemek için katı denetimler uygulamıştır.

- [Çalışma geçmişi verilerine güvenli erişim](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-run-history-data)

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: kaynaklara erişimi denetlemek için Azure RBAC kullanma 

**Rehberlik**: mantıksal uygulamaları yönetme, düzenleme ve görüntüleme gibi belirli görevleri yalnızca belirli kullanıcıların veya grupların çalıştırmasına izin verebilirsiniz. İzinlerini denetlemek için, Azure aboneliğinizdeki üyelere özelleştirilmiş veya yerleşik roller atayabilmeniz için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanın:

- Mantıksal uygulama katılımcısı: mantıksal uygulamaları yönetmenizi sağlar, ancak bunlara erişimi değiştiremezsiniz.
- Logic App operatörü: Logic Apps 'i okumanızı, etkinleştirmenizi ve devre dışı bırakmanızı sağlar, ancak bunları düzenleyemez veya güncelleştiremezsiniz.

Başkalarının mantıksal uygulamanızı değiştirmesini veya silmesini engellemek için Azure Kaynak kilidi ' ni kullanabilirsiniz. Bu özellik başkalarının üretim kaynaklarını değiştirmesini veya silmesini engeller.

- [Azure Logic Apps işlemlerine güvenli erişim](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-logic-app-operations)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Rehberlik**: Azure Logic Apps bekleyen verileri depolamak ve otomatik olarak şifrelemek Için Azure Storage 'ı kullanır. Bu şifreleme, verilerinizi korur ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur. Azure depolama, verilerinizi şifrelemek için varsayılan olarak Microsoft tarafından yönetilen anahtarları kullanır.

Logic Apps 'i barındırmak için bir tümleştirme hizmeti ortamı (ıSE) oluşturduğunuzda ve Azure Storage tarafından kullanılan şifreleme anahtarları üzerinde daha fazla denetim istiyorsanız, Azure Key Vault kullanarak kendi anahtarınızı ayarlayabilir, kullanabilir ve yönetebilirsiniz. Bu özellik "Kendi Anahtarını Getir" (BYOK) olarak da bilinir ve anahtarınız "müşteri tarafından yönetilen anahtar" olarak adlandırılır.

- [Azure Logic Apps içindeki tümleştirme hizmeti ortamları için bekleyen verileri şifreleme](customer-managed-keys-integration-service-environment.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Rehberlik**: değişikliklerin Azure Logic Apps ve diğer önemli veya ilgili kaynakların yanı sıra değişiklikler gerçekleştiği hakkında uyarı oluşturmak Için Azure etkinlik günlüğü Ile Azure izleyici 'yi kullanın.

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](/azure/azure-monitor/platform/alerts-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: abonelikleriniz dahilinde (işlem, depolama, ağ, bağlantı noktaları ve protokoller vb.) tüm kaynakları sorgulamak/öğrenmek Için Azure Kaynak grafiğini kullanın. Kiracınızda uygun (okuma) izinlere sahip olun ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerini numaralandırın.

Klasik Azure kaynakları kaynak Graph aracılığıyla bulunabilse de, ileri doğru Azure Resource Manager kaynak oluşturmanız ve kullanmanız önemle tavsiye edilir.

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

**Rehberlik**: Azure kaynaklarını düzenlemek ve izlemek için uygun yerlerde etiketleme, yönetim grupları ve ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

Ayrıca, aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri abonelikleri içinde oluşturulabilecek kaynak türlerine kısıtlamalar koymak için Azure Ilkesi 'ni kullanın:

- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](/azure/governance/management-groups/create)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynaklarının envanterini tanımlayın ve saklayın

**Rehberlik**: Kurumsal gereksinimleriniz uyarınca, işlem kaynakları Için onaylanan Azure kaynaklarının (bağlayıcılar gibi) envanterini ve onaylanan yazılımları oluşturun.

Note: Google 'ın veri ve gizlilik ilkeleri nedeniyle Gmail bağlayıcısını yalnızca Google tarafından onaylanan hizmetlerle kullanabilirsiniz. Bu durum gelişmekte ve gelecekte diğer Google bağlayıcıları etkileyebilir.

- [Tüm Logic Apps bağlayıcıları listesi](/connectors/connector-reference/connector-reference-logicapps-connectors)

- [Gmail bağlayıcılarına yönelik sorunları ve sınırlamaları anlayın](/connectors/gmail/#known-issues-and-limitations)

- [Google 'ın Gizlilik ilkesi hakkında daha fazla bilgi](../connectors/connectors-google-data-security-privacy-policy.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: abonelikleriniz içinde oluşturulabilecek kaynak türlerine yönelik kısıtlamalar koymak Için Azure ilkesini kullanın. 

Azure Kaynak Grafiği 'ni kullanarak aboneliklerinde kaynakları sorgulama/bulma.  Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerine oluşturulabilecek kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:
- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: onaylanan yazılım başlıkları envanterini koruyun

**Rehberlik**: uygulanamaz; Bu öneri, işlem kaynaklarına yöneliktir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak üzere Azure koşullu erişimini yapılandırın.

- [Azure Resource Manager erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: kullanıcıların işlem kaynakları içinde betikleri yürütme yeteneğini sınırlayın

**Rehberlik**: uygulanamaz; Bu öneri, işlem kaynaklarına yöneliktir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: yüksek riskli uygulamaları fiziksel olarak veya mantıksal olarak ayırt edin

**Rehberlik**: iş işlemlerinde gerekli olan, ancak kuruluşa daha fazla risk doğurabilecek Logic Apps ilgili kaynaklar, kendi sanal makinesi ve/veya sanal ağı içinde yalıtılmalı ve bir Azure Güvenlik Duvarı veya ağ güvenlik grubuyla yeterince güvenli hale getirilir.

İş işlemleri için gerekli olan, ancak kuruluş için daha yüksek risk doğurabilecek Logic Apps, belirli izinlere ve Azure RBAC sınırlarına sahip ayrı kaynak grupları aracılığıyla mümkün olan her yerde yalıtılmalıdır.

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md) 

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

- [Yönetim Grupları oluşturma](/azure/governance/management-groups/create) 

- [Azure RBAC aracılığıyla Logic Apps erişimi güvenli hale getirme](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-logic-app-operations)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Kılavuz**: Azure ilkesiyle Azure Logic Apps örneklerinizin standart güvenlik yapılandırmasını tanımlayın ve uygulayın. Logic Apps örneklerinizin yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft. Logic" ad alanındaki Azure Ilke diğer adlarını kullanın. Örneğin, kullanıcıların erişimi kısıtlamak istediğiniz kaynaklara bağlantı oluşturmasını veya bu bağlantıları kullanmasını engelleyebilirsiniz.

Ayrıca, Azure Resource Manager, yapılandırmanın kuruluşunuzun güvenlik gereksinimlerini karşıladığından/aştığından emin olmak için gözden geçirilmesi gereken JavaScript Nesne Gösterimi (JSON) içinde şablonu dışarı aktarma özelliğine sahiptir.

Ayrıca hassas verileri ve gizli dizileri korumak için güvenli parametreleri kullanın.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Logic Apps içinde bağlayıcılar tarafından oluşturulan bağlantıları engelle](block-connections-connectors.md)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Logic Apps için Azure Resource Manager şablonlarını dağıtma](logic-apps-deploy-azure-resource-manager-templates.md)

- [Güvenli eylem parametrelerini anlama](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-action-parameters)

- [Parametreler için güvenlik önerileri](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-best-practices#security-recommendations-for-parameters)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: güvenli işletim sistemi yapılandırması oluşturma

**Rehberlik**: uygulanamaz; Bu kılavuz işlem kaynaklarına yöneliktir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Kılavuz**: Azure kaynaklarınız genelinde güvenli ayarları zorlamak Için Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] kullanın.

Azure Ilkesiyle Azure Logic Apps örneklerinizin standart güvenlik yapılandırmasını tanımlayın ve uygulayın. Logic Apps örneklerinizin yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft. Logic" ad alanındaki Azure Ilke diğer adlarını kullanın. Örneğin, kullanıcıların erişimi kısıtlamak istediğiniz kaynaklara bağlantı oluşturmasını veya bu bağlantıları kullanmasını engelleyebilirsiniz.

Ayrıca, Azure Resource Manager, yapılandırmanın kuruluşunuzun güvenlik gereksinimlerini karşıladığından/aştığından emin olmak için gözden geçirilmesi gereken JavaScript Nesne Gösterimi (JSON) içinde şablonu dışarı aktarma özelliğine sahiptir.

Ayrıca, gizleme kullanarak verileri çalıştırma geçmişinde güvende olduğunuzdan emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

- [Azure Logic Apps içinde bağlayıcılar tarafından oluşturulan bağlantıları engelle](block-connections-connectors.md)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Logic Apps için Azure Resource Manager şablonlarını dağıtma](logic-apps-deploy-azure-resource-manager-templates.md)

- [Çalışma geçmişi girişleri ve çıkışları için güvenli erişim](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#obfuscate)

- [Parametre girdilerine güvenli erişim](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-action-parameters)

- [Parametreler için güvenlik önerileri](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-best-practices#security-recommendations-for-parameters)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: güvenli işletim sistemi yapılandırmalarının bakımını yapma

**Rehberlik**: uygulanamaz; Bu kılavuz işlem kaynaklarına yöneliktir.

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilke tanımları kullanıyorsanız, kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure devops veya Azure Repos kullanın.

Ayrıca, Azure Resource Manager, yapılandırmanın kuruluşunuzun güvenlik gereksinimlerini karşıladığından/aştığından emin olmak için gözden geçirilmesi gereken JavaScript Nesne Gösterimi (JSON) içinde şablonu dışarı aktarma özelliğine sahiptir.

- [Azure DevOps 'da kod depolama](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure Repos belgeleri](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: özel işletim sistemi görüntülerini güvenli bir şekilde depolayın

**Rehberlik**: uygulanamaz; Bu kılavuz işlem kaynaklarına yöneliktir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: yerleşik Azure ilke tanımlarını ve "Microsoft. Logic" ad alanındaki Azure ilkesi diğer adlarını kullanarak uyarı, denetim ve sistem yapılandırmalarının uygulanmasını sağlayacak özel ilkeler oluşturun. Azure kaynaklarınızın ağ yapılandırmasını denetlemek veya zorlamak için özel ilkeler oluşturmak üzere Azure Ilke diğer adlarını kullanın. Ayrıca, ilke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: işletim sistemleri için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: uygulanamaz; Bu kılavuz işlem kaynaklarına yöneliktir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: yerleşik Azure ilke tanımlarını ve "Microsoft. Logic" ad alanındaki Azure ilkesi diğer adlarını kullanarak uyarı, denetim ve sistem yapılandırmalarının uygulanmasını sağlayacak özel ilkeler oluşturun. Azure kaynaklarınızın yapılandırmasını otomatik olarak zorlamak için [Denetim], [reddetme] ve [dağıtım yoksa dağıt] Azure Ilkesini kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: işletim sistemleri için otomatik yapılandırma izlemeyi Uygula

**Rehberlik**: uygulanamaz; Bu kılavuz işlem kaynaklarına yöneliktir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: gizleme kullanarak mantıksal uygulama çalıştırma geçmişindeki giriş ve çıkışları güvenli hale getirin. Farklı ortamlarda dağıtım yaparsanız, mantıksal uygulamanızın iş akışı tanımındaki bu ortamlara göre farklılık gösteren değerleri parametreleştirmeyi göz önünde bulundurun. Bu şekilde, mantıksal uygulamanızı dağıtmak, güvenli parametreleri tanımlayarak gizli verileri korumak ve bir parametre dosyası kullanarak bu verileri şablon parametreleri aracılığıyla ayrı girişler olarak geçirmek için Azure Resource Manager şablonu kullanarak sabit kodlanmış verilerden kaçınabilirsiniz. Hassas verileri depolamak ve bu değerleri dağıtımda Key Vault alan güvenli şablon parametrelerini kullanmak için Key Vault kullanabilirsiniz. Daha sonra, parametre dosyanızdaki anahtar kasasına ve gizli anahtarlara başvurabilirsiniz. 

Logic Apps 'i barındırmak için bir tümleştirme hizmeti ortamı (ıSE) oluşturduğunuzda ve Azure Storage tarafından kullanılan şifreleme anahtarları üzerinde daha fazla denetim istiyorsanız, Azure Key Vault kullanarak kendi anahtarınızı ayarlayabilir, kullanabilir ve yönetebilirsiniz. Bu özellik "Kendi Anahtarını Getir" (BYOK) olarak da bilinir ve anahtarınız "müşteri tarafından yönetilen anahtar" olarak adlandırılır.

- [Azure Logic Apps çalışma geçmişinde güvenli giriş ve çıkış çıkışları](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#obfuscate)

- [Parametreler için güvenlik önerileri](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-best-practices#security-recommendations-for-parameters)

- [Azure Logic Apps içindeki parametre girdilerine güvenli erişim](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-parameter-inputs)

- [Azure Key Vault kullanarak dağıtım sırasında güvenli parametre değerlerini geçirme](../azure-resource-manager/templates/key-vault-parameter.md)

- [Azure Logic Apps içindeki tümleştirme hizmeti ortamları (sesleri) için bekleyen verileri şifrelemek üzere müşteri tarafından yönetilen anahtarlar ayarlayın](customer-managed-keys-integration-service-environment.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Kılavuz**: Azure Active Directory (Azure AD) tarafından korunan diğer kaynaklara kolayca erişmek ve oturum açmadan kimliğinizi doğrulamak için, mantıksal uygulamanız kimlik bilgileri veya gizli dizileri yerine yönetilen bir kimlik (eski adıyla YÖNETILEN HIZMET KIMLIĞI veya MSI) kullanabilir. Azure bu kimliği sizin için yönetir ve gizli dizi sağlamak veya döndürmek zorunda olmadığınızdan kimlik bilgilerinizin güvenliğinin sağlanmasına yardımcı olur.

Şu anda yalnızca belirli yerleşik Tetikleyiciler ve Eylemler, yönetilmeyen bağlayıcılar veya bağlantılar değil, yönetilen kimlikleri destekler:

- HTTP
- Azure İşlevleri
- Azure API Management
- Azure Uygulama Hizmetleri

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Azure Logic Apps içindeki yönetilen kimlikleri kullanarak Azure kaynaklarına erişimin kimliğini doğrulama](create-managed-service-identity.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: gizleme kullanarak mantıksal uygulama çalıştırma geçmişindeki giriş ve çıkışları güvenli hale getirin. Farklı ortamlarda dağıtım yaparsanız, mantıksal uygulamanızın iş akışı tanımındaki bu ortamlara göre farklılık gösteren değerleri parametreleştirmeyi göz önünde bulundurun. Bu şekilde, mantıksal uygulamanızı dağıtmak, güvenli parametreleri tanımlayarak gizli verileri korumak ve bir parametre dosyası kullanarak bu verileri şablon parametreleri aracılığıyla ayrı girişler olarak geçirmek için Azure Resource Manager şablonu kullanarak sabit kodlanmış verilerden kaçınabilirsiniz. Hassas verileri depolamak ve bu değerleri dağıtımda Key Vault alan güvenli şablon parametrelerini kullanmak için Key Vault kullanabilirsiniz. Daha sonra, parametre dosyanızdaki anahtar kasasına ve gizli anahtarlara başvurabilirsiniz. 

Kod içinde kimlik bilgilerini tanımlamak için kimlik bilgisi tarayıcısı da uygulayabilirsiniz. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar. 

- [Azure Logic Apps çalışma geçmişinde güvenli giriş ve çıkış çıkışları](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#obfuscate)

- [Parametreler için güvenlik önerileri](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-best-practices#security-recommendations-for-parameters)

- [Azure Logic Apps içindeki parametre girdilerine güvenli erişim](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-parameter-inputs)

- [Azure Key Vault kullanarak dağıtım sırasında güvenli parametre değerlerini geçirme](../azure-resource-manager/templates/key-vault-parameter.md)

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: merkezi olarak yönetilen kötü amaçlı yazılımdan koruma yazılımı kullanın

**Rehberlik**: uygulanamaz; Bu öneri, işlem kaynaklarına yöneliktir. Microsoft kötü amaçlı yazılımdan koruma, Azure hizmetlerini destekleyen temel ana bilgisayarda (örneğin, Azure Logic Apps) etkinleştirilir, ancak müşteri içeriklerde çalıştırılmaz.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: işlem dışı Azure kaynaklarına yüklenecek dosyaları önceden Tara

**Rehberlik**: Microsoft 'un kötü amaçlı yazılımdan koruma, Azure hizmetlerini destekleyen temel ana bilgisayarda (örneğin, Azure Backup) etkinleştirilir, ancak içeriğiniz üzerinde çalışmaz. 

App Service, Data Lake Storage, BLOB depolama vb. gibi işlem dışı Azure kaynaklarına yüklenen tüm dosyaları önceden tarayın. 

Depolama hesaplarına yüklenen kötü amaçlı yazılımları algılamak için veri Hizmetleri için Azure Güvenlik Merkezi 'nin tehdit algılamasını kullanın. 

- [Azure Cloud Services ve sanal makineler için Microsoft kötü amaçlı yazılımdan koruma 'yı anlayın](../security/fundamentals/antimalware.md)

- [Azure Güvenlik Merkezi 'nin veri Hizmetleri tehdit algılamasını anlama](/azure/security-center/threat-protection)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: kötü amaçlı yazılımdan koruma yazılımlarının ve imzaların güncelleştirildiğinden emin olun

**Rehberlik**: uygulanamaz; Bu kılavuz işlem kaynaklarına yöneliktir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli otomatik arka pencerelere emin olun

**Rehberlik**: bir olağanüstü durum kurtarma (Dr) çözümü uygulayarak verileri koruyabilir, kritik iş işlevlerini destekleyen kaynakları hızlıca geri yükleyebilir ve iş SÜREKLILIĞI (BC) sürdürmek için işlemleri çalıştırmaya devam edebilirsiniz.

Bu olağanüstü durum kurtarma stratejisi, Azure Logic Apps aynı zamanda kullanılabilir olan alternatif bir konumda bir bekleme veya yedekleme mantıksal uygulamasına yük devretmek için birincil mantıksal uygulamanızı ayarlamaya odaklanır. Bu şekilde, birincil, kayıpları, kesintiler veya hatalara sahip olursa ikincil iş üzerinde işlem yapabilir. Bu strateji, ikincil mantıksal uygulamanızın ve bağımlı kaynaklarınızın zaten dağıtılması ve alternatif konuma hazırlanmasının gerekli olmasını gerektirir.

Ayrıca, mantıksal uygulamanızın temel alınan iş akışı tanımını bir Azure Resource Manager şablonuna genişletmeniz gerekir. Bu şablon, mantıksal uygulamanızı sağlamak ve dağıtmak için altyapıyı, kaynakları, parametreleri ve diğer bilgileri tanımlar.

- [Azure Logic Apps için iş sürekliliği ve olağanüstü durum kurtarma hakkında daha fazla bilgi edinin](business-continuity-disaster-recovery-guidance.md)

- [Azure Resource Manager şablonları kullanarak Azure Logic Apps dağıtımını otomatikleştirme](logic-apps-azure-resource-manager-templates-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: bir olağanüstü durum kurtarma (Dr) çözümü uygulayarak verileri koruyabilir, kritik iş işlevlerini destekleyen kaynakları hızlıca geri yükleyebilir ve iş SÜREKLILIĞI (BC) sürdürmek için işlemleri çalıştırmaya devam edebilirsiniz.

Bu olağanüstü durum kurtarma stratejisi, Azure Logic Apps aynı zamanda kullanılabilir olan alternatif bir konumda bir bekleme veya yedekleme mantıksal uygulamasına yük devretmek için birincil mantıksal uygulamanızı ayarlamaya odaklanır. Bu şekilde, birincil, kayıpları, kesintiler veya hatalara sahip olursa ikincil iş üzerinde işlem yapabilir. Bu strateji, ikincil mantıksal uygulamanızın ve bağımlı kaynaklarınızın zaten dağıtılması ve alternatif konuma hazırlanmasının gerekli olmasını gerektirir.

Ayrıca, mantıksal uygulamanızın temel alınan iş akışı tanımını bir Azure Resource Manager şablonuna genişletmeniz gerekir. Bu şablon, mantıksal uygulamanızı sağlamak ve dağıtmak için altyapıyı, kaynakları, parametreleri ve diğer bilgileri tanımlar.

Bir mantıksal uygulamadaki her istek uç noktası, uç noktanın URL 'sinde bir paylaşılan erişim Imzasına (SAS) sahiptir. Gizliliklerinizi depolamak için Azure Key Vault kullanıyorsanız, anahtarlarınızın ve URL 'nizin düzenli otomatik yedeklemelerini sağlayın.

- [Azure Logic Apps için iş sürekliliği ve olağanüstü durum kurtarma hakkında daha fazla bilgi edinin](business-continuity-disaster-recovery-guidance.md)

- [Azure Resource Manager şablonları kullanarak Azure Logic Apps dağıtımı otomatikleştirin](logic-apps-azure-resource-manager-templates-overview.md)

- [SAS kullanarak Azure Logic Apps erişimi ve verileri güvenli hale getirme](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#sas)

- [Key Vault anahtarlarını yedekleme](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: olağanüstü durum kurtarma stratejiniz, Azure Logic Apps de kullanılabilir olan alternatif bir konumda bir bekleme veya yedekleme mantıksal uygulamasına yük devretmek için birincil mantıksal uygulamanızı ayarlamaya odaklanmalıdır. Bu şekilde, birincil, kayıpları, kesintiler veya hatalara sahip olursa ikincil iş üzerinde işlem yapabilir. Bu strateji, ikincil mantıksal uygulamanızın ve bağımlı kaynaklarınızın zaten dağıtılması ve alternatif konuma hazırlanmasının gerekli olmasını gerektirir.

Yedeklenen müşteri tarafından yönetilen anahtarların test geri yüklemesi. Bu, yalnızca tümleştirme hizmeti ortamlarında (ıSE) çalışan Logic Apps için geçerli olduğunu unutmayın.

- [Azure Logic Apps için iş sürekliliği ve olağanüstü durum kurtarma hakkında daha fazla bilgi edinin](business-continuity-disaster-recovery-guidance.md)

- [Azure Logic Apps içindeki tümleştirme hizmeti ortamları (sesleri) için bekleyen verileri şifrelemek üzere müşteri tarafından yönetilen anahtarlar ayarlayın](customer-managed-keys-integration-service-environment.md)

- [Azure 'da Anahtar Kasası anahtarlarını geri yükleme](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: olağanüstü durum kurtarma stratejiniz, Azure Logic Apps de kullanılabilir olan alternatif bir konumda bir bekleme veya yedekleme mantıksal uygulamasına yük devretmek için birincil mantıksal uygulamanızı ayarlamaya odaklanmalıdır. Bu şekilde, birincil, kayıpları, kesintiler veya hatalara sahip olursa ikincil iş üzerinde işlem yapabilir. Bu strateji, ikincil mantıksal uygulamanızın ve bağımlı kaynaklarınızın zaten dağıtılması ve alternatif konuma hazırlanmasının gerekli olmasını gerektirir. 

Yedeklenen müşteri tarafından yönetilen anahtarları koruyun. Bu, yalnızca tümleştirme hizmeti ortamlarında (ıSE) çalışan Logic Apps için geçerli olduğunu unutmayın.

Anahtarları yanlışlıkla veya kötü amaçlı silmeye karşı korumak için Key Vault Soft-Delete ve Temizleme korumasını etkinleştirin.

- [Azure Logic Apps için iş sürekliliği ve olağanüstü durum kurtarma hakkında daha fazla bilgi edinin](business-continuity-disaster-recovery-guidance.md)

- [Azure Logic Apps içindeki tümleştirme hizmeti ortamları (sesleri) için bekleyen verileri şifrelemek üzere müşteri tarafından yönetilen anahtarlar ayarlayın](customer-managed-keys-integration-service-environment.md)

- [Key Vault 'de Soft-Delete ve Temizleme korumasını etkinleştirme](https://docs.microsoft.com/azure/storage/blobs/soft-delete-overview?tabs=azure-portal)

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
