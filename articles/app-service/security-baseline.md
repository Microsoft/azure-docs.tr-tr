---
title: App Service için Azure Güvenlik temeli
description: App Service güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: d3e9d7d8cd8e5ffad74b02179994e299a59c8894
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833949"
---
# <a name="azure-security-baseline-for-app-service"></a>App Service için Azure Güvenlik temeli

App Service için Azure Güvenlik temeli, dağıtımınızın güvenlik duruşunu artırmanıza yardımcı olacak öneriler içerir. Bu hizmetin taban çizgisi, Azure [güvenlik kıyaslama sürümü 1,0](../security/benchmarks/overview-v1.md)' dan çizilir ve bu, en iyi yöntemler kılavuzumuzdan Azure 'da bulut çözümlerinizi nasıl güvence altına almak için öneriler sağlar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve App Service için geçerli olan ilgili kılavuza göre gruplandırılır. App Service için geçerli olmayan **denetimler** dışlandı.

App Service Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam App Service güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: yalıtılmış fiyatlandırma katmanında App Service kullanırken, Azure sanal ağınız içindeki bir alt ağa doğrudan dağıtabileceğiniz bir App SERVICE ORTAMı (Ao) olarak da bilinir. Sanal ağınızdaki kaynaklara gelen ve giden trafiği engelleyerek veya bir App Service Ortamı uygulamalara erişimi kısıtlamak için ağ güvenlik Azure App Service Ortamı grupları 'nı kullanın.

Varsayılan olarak, ağ güvenlik grupları en düşük önceliğe sahip bir örtük reddetme kuralı içerir ve açık izin verme kuralları eklemenizi gerektirir. En az ayrıcalıklı ağ yaklaşımını temel alarak ağ güvenlik grubunuz için izin verme kuralları ekleyin. App Service Ortamı barındırmak için kullanılan temeldeki sanal makinelere, Microsoft tarafından yönetilen bir abonelikte olduklarından doğrudan erişilebilir.

Trafiği bir Web uygulaması güvenlik duvarı (WAF) etkin Azure Application Gateway aracılığıyla yönlendirerek App Service Ortamı koruyun. Uygulamanıza gelen yayımlama trafiğini güvenli hale getirmek için Application Gateway ile birlikte hizmet uç noktalarını kullanın.  

Çok kiracılı App Service (yalıtılmış katmanda olmayan bir uygulama), uygulamanızdan giden trafiği engellemek için ağ güvenlik gruplarını kullanın. Sanal ağ tümleştirme özelliği ile uygulamalarınızın bir sanal ağ içindeki veya üzerinden kaynaklara erişmesine olanak sağlar. Bu özellik ayrıca, uygulamadan gelen genel adreslere giden trafiği engellemek için de kullanılabilir. Sanal Ağ tümleştirmesi, bir uygulamaya gelen erişim sağlamak için kullanılamaz.  

İle uygulamanıza gelen trafiği güvenli hale getirin:
- Erişim kısıtlamaları-gelen erişimi denetleyen izin verme veya reddetme kurallarından oluşan bir dizi
- Hizmet uç noktaları-belirtilen sanal ağların veya alt ağların dışından gelen trafiği reddedebilirler
- Özel uç noktalar-uygulamanızı özel bir IP adresi ile sanal ağınıza sunun. Uygulamanızda etkin olan özel uç noktalar, artık internet erişimli değildir

Sanal ağ tümleştirme özelliğini aynı bölgedeki sanal ağlarla kullanırken, ağ güvenlik grupları ve rota tablolarını Kullanıcı tanımlı yollarla kullanın. Kullanıcı tanımlı yollar, giden trafiği istendiği gibi göndermek için tümleştirme alt ağına yerleştirilebilir.  

Aboneliklerinizde ve sanal ağlarınızda uygulama ve ağ bağlantısı ilkelerini merkezi olarak oluşturmak, zorlamak ve günlüğe kaydetmek için bir Azure Güvenlik Duvarı uygulamayı düşünün. Azure Güvenlik Duvarı, sanal ağ kaynakları için statik bir genel IP adresi kullanır ve bu, dış güvenlik duvarlarının sanal ağınızdan kaynaklanan trafiği belirlemesine izin verir. 

- [App Service Ortamı kilitleme](environment/firewall-integration.md)

- [Web uygulaması güvenlik projesi (OWASP) Ilk 10 güvenlik açığı korumasını açın](https://owasp.org/www-project-top-ten/)

- [Ağ güvenlik grupları](../virtual-network/network-security-groups-overview.md)

- [Uygulamanızı bir Azure sanal ağı ile tümleştirme](web-sites-integrate-with-vnet.md)

- [App Service Ortamında ağ konusunda dikkat edilmesi gerekenler](environment/network-info.md)

- [Dış Ao oluşturma](environment/create-external-ase.md)

- [Dahili bir AO oluşturma](environment/create-ilb-ase.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

**Azure ilkesi yerleşik tanımları-Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: sanal ağların, alt ağların ve ağ arabirimlerinin yapılandırma ve trafiğini izleme ve günlüğe kaydetme

**Rehberlik**: App Service Uygulamalarınız ve API 'lerinizle ilgili ağ kaynaklarının ve yapılandırmaların güvenliğini sağlamak Için Azure Güvenlik Merkezi 'nden ağ koruma önerilerini uygulayın.

Azure Güvenlik Duvarı 'nı kullanarak trafik gönderin ve abonelikler ve sanal ağlarda uygulama ve ağ bağlantısı ilkelerini merkezi olarak oluşturun, uygulayın ve günlüğe kaydedin. Azure Güvenlik Duvarı, sanal ağ kaynaklarınız için bir statik genel IP adresi kullanır ve bu, dış güvenlik duvarlarının sanal ağınızdan kaynaklanan trafiği belirlemesine izin verir. Azure Güvenlik Duvarı hizmeti Ayrıca günlüğe kaydetme ve analiz için Azure Izleyici ile tamamen tümleşiktir.

- [Azure Güvenlik Duvarı 'Na genel bakış](../firewall/overview.md)

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

- [App Service Izlemeyi ve korumayı etkinleştirme](../security-center/defender-for-app-service-introduction.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1,3: kritik Web uygulamalarını koruma

**Rehberlik**: internet 'ten erişilebilen bir uygulamayı bir App SERVICE ORTAMı (Ao) içinde güvenli hale getirme:
- İnternet 'e yönelik bir uygulamanın önünde Azure Application Gateway bir Web uygulaması güvenlik duvarı (WAF) dağıtma
- Application Gateway gelen trafiği güvenli hale getirmek için erişim kısıtlamalarını kullanın
- Kimlik doğrulamasını sağlamak için Azure Active Directory (Azure AD) ile uygulamayı güvenli hale getirin
- En düşük TLS sürümünü 1,2 olarak ayarlayın
- Uygulamayı yalnızca HTTPS olarak ayarla

Azure Güvenlik Duvarı cihazından giden tüm uygulama trafiğini yönlendirin ve günlükleri izleyin. 

Çok kiracılı App Service (örneğin, yalıtılmış katmanda değil), internet 'ten erişilebilen bir uygulamanın güvenliğini sağlamak için
- Uygulamanın önünde Web uygulaması güvenlik duvarı özellikli bir cihaz dağıtma
- Web uygulaması güvenlik duvarı (WAF) cihazına gelen trafiği güvenli hale getirmek için erişim kısıtlamalarını veya hizmet uç noktalarını kullanın
- Kimlik doğrulamasını sağlamak için Azure AD ile uygulamayı güvenli hale getirme
- En düşük TLS sürümünü 1,2 olarak ayarlayın
- Uygulamayı yalnızca HTTPS olarak ayarla
- Tüm giden trafiğin tümleştirme alt ağındaki ağ güvenlik gruplarına ve Kullanıcı tanımlı yollara tabi olmasını sağlamak için NETWORK_ROUTE_ALL WEBSITE_VIRTUAL sanal ağ tümleştirmeyi ve uygulama ayarını kullanın.

Uygulama hizmeti ortamı uygulamasına benzer şekilde, bir Azure Güvenlik Duvarı cihazından giden tüm uygulama trafiğini ve uygulamadaki günlükleri izleyin.

Ayrıca, App Service Ortamı bir belgeyi kilitleyen önerileri gözden geçirin ve izleyin.

- [App Service Ortamı kilitleme](environment/firewall-integration.md)

- [Azure Application Gateway Azure Web uygulaması güvenlik duvarı](../web-application-firewall/ag/ag-overview.md)

- [Azure App Service erişim kısıtlamaları](app-service-ip-restrictions.md)

- [Azure Izleyici ile WAF uyarılarını izleyin ve eğilimleri kolayca izleyin ](../azure-monitor/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: App Service ortamı App Service ortamı bir belge kilitleme bölümünde açıklandığı gibi güvenli hale getirin. Bilinen kötü amaçlı veya kullanılmayan genel IP adresleriyle iletişimleri reddetmek için Azure Güvenlik Merkezi 'nde tümleşik tehdit bilgileri işlevlerini uygulayın. Application Gateway gelen trafiği güvenli hale getirmek için erişim kısıtlamalarını kullanın. 

Genel internet 'e yönelik uç nokta ile çok kiracılı App Service (yalıtılmış bir katmanda olmayan bir uygulama) güvenli hale getirin. Yalnızca sanal ağınız içindeki belirli bir alt ağdan gelen trafiğe izin verir ve diğer her şeyi engeller. İzin verilen gelen trafiği kilitlemek için ağ Access Control listelerini (IP kısıtlamalarını) yapılandırmak üzere erişim kısıtlamalarını kullanın.

Uygulamanıza ağ erişimini yönetmek için sıralı izin verme veya reddetme listesi arasında öncelik tanımlayın. Bu liste, IP adreslerini veya sanal ağ alt ağlarını içerebilir. Bir veya daha fazla giriş içerdiğinde listenin sonunda örtük bir "Tümünü Reddet" kuralı bulunur. Bu özellik, Web Apps, API Apps, Linux uygulamaları, Linux kapsayıcı uygulamaları ve Işlevleri dahil olmak üzere tüm App Service barındırılan iş yükleri ile çalışır. 

Web uygulamanıza erişimi bir Azure sanal ağından kısıtlamak için hizmet uç noktalarını kullanın. Hizmet uç noktalarına sahip seçili alt ağlardan çok kiracılı App Service erişimi (yalıtılmış bir katmanda olmayan bir uygulama) sınırlayın. 

- [Azure App Service Statik IP Kısıtlamaları](app-service-ip-restrictions.md)

- [Azure Application Gateway Azure Web uygulaması güvenlik duvarı](../web-application-firewall/ag/ag-overview.md)

- [App Service Ortamı için bir Web uygulaması güvenlik duvarı (WAF) yapılandırma](environment/app-service-app-service-environment-web-application-firewall.md)

- [Bir App Service Ortamı kilitleme konusunda açıklandığı gibi Ao 'un güvenliğini sağlama](environment/firewall-integration.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Rehberlik**: güvenlik merkezi ile App Service uygulamalara gönderilen ve giden istekleri ve yanıtları izler. Web uygulamasına yönelik saldırılar Web uygulaması güvenlik duvarı olan gerçek zamanlı Application Gateway kullanılarak izlenebilir, Web uygulaması güvenlik duvarı uyarılarını izlemek ve eğilimleri kolayca izlemek için Azure Izleyici 'den tümleşik günlük ile etkinleştirilir.

- [Azure Application Gateway Azure Web uygulaması güvenlik duvarı](../web-application-firewall/ag/ag-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Web uygulamalarına trafiği yönetme

**Rehberlik**: App Service ortamı bir uygulama için trafiği yönetme:

- Bir App Service Ortamı kilitleme konusunda açıklandığı gibi App Service Ortamı güvenliğini sağlama
- İnternet 'e yönelik uygulamalarınızın önünde Azure Web uygulaması güvenlik duvarı olan bir Application Gateway dağıtın
- Uygulamayı yalnızca HTTPS üzerinden erişilebilir olacak şekilde ayarlama

Çok kiracılı App Service (yalıtılmış katmanda değil) internet 'ten erişilebilen bir uygulama için trafiği yönetme: 

- İnternet 'e yönelik uygulamalarınızın önünde Azure Web uygulaması güvenlik duvarı 'Nın etkin olduğu bir Application Gateway dağıtın
- Web uygulaması güvenlik duvarı 'na gelen trafiğin güvenliğini sağlamak için erişim kısıtlamalarını veya hizmet uç noktalarını kullanın. Erişim kısıtlamaları özelliği, Web Apps, API Apps, Linux uygulamaları, Linux kapsayıcı uygulamaları ve Işlevleri dahil olmak üzere tüm App Service barındırılan iş yükleri ile çalışır.

- Uygulamayı yalnızca HTTPS üzerinden erişilebilir olacak şekilde ayarlama
- Yalnızca bir uygulama ağ geçidinde VIP 'den gelen trafiği erişime sahip tek adres olarak alabilmesi için App Service uygulamanıza erişimi statik IP kısıtlamalarıyla sınırlayın.

Ek bilgi için başvurulan bağlantıları gözden geçirin.

- [Azure App Service Statik IP Kısıtlamaları](app-service-ip-restrictions.md)

- [Azure Application Gateway Azure Web uygulaması güvenlik duvarı](../web-application-firewall/ag/ag-overview.md)

- [Portal ile Application Gateway kullanarak uçtan uca TLS Yapılandırma](../application-gateway/end-to-end-ssl-portal.md)

- [Bir App Service kilitleme konusunda açıklandığı gibi Ao 'un güvenliğini sağlama](/azure/app-service/environment/firewall-integration)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Kılavuz**: App Service hizmeti yönetmek için kullanılan uç noktalar vardır. Bu uç nokta adresleri AppServiceManagement IP hizmeti etiketinde de bulunur. AppServiceManagement etiketi yalnızca bu tür trafiğe izin vermek için bir App Service Ortamı ile kullanılır. 

Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adını belirterek karşılık gelen hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. App Service gelen adresler AppService IP hizmeti etiketinde izlenir. App Service tarafından kullanılan giden adresleri içeren bir IP hizmeti etiketi yok.

Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

- [Sanal ağ hizmeti etiketleri](../virtual-network/service-tags-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Rehberlik**: App Service uygulamalarınızla ilgili ağ ayarları için standart güvenlik yapılandırması tanımlayın ve uygulayın. 

"Microsoft. Web" ve "Microsoft. Network" ad alanlarında Azure Ilke diğer adlarını kullanarak güvenlik yapılandırmalarının bakımını yapın. App Service uygulamalarınızın ağ yapılandırmasını denetlemek veya zorlamak için özel ilkeler oluşturun. 

App Service için yerleşik ilke tanımlarını kullanın, örneğin:
- Uygulamanın bir sanal ağ hizmeti uç noktası kullanması gerekir
- Uygulamaya yalnızca HTTPS üzerinden erişilebilir olması gerekir
- En düşük TLS sürümünü geçerli sürüme ayarla

Ek bilgi için başvurulan bağlantıları gözden geçirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Portal ile Application Gateway kullanarak uçtan uca TLS Yapılandırma](../application-gateway/end-to-end-ssl-portal.md)

- [Bir App Service kilitleme konusunda açıklandığı gibi Ao 'un güvenliğini sağlama](/azure/app-service/environment/firewall-integration)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: App Service trafik akışı dahil olmak üzere ağ güvenlik grupları ve diğer ilgili kaynaklar için Etiketler kullanın.

Tek tek ağ güvenlik grupları kuralları için bir ağa veya ağdan giden trafiğe izin veren herhangi bir kural için "Açıklama" alanıyla iş ihtiyacı, süresi vb. belirtin.

Tüm kaynakların etiketlerle oluşturulduğundan ve var olan etiketsiz kaynakları bilgilendirmek için "etiket ve onun değeri gerektir" gibi etiketleme efektleriyle ilgili yerleşik Azure Ilke tanımlarından herhangi birini uygulayın. Azure PowerShell veya Azure CLı kullanarak, etiketlerine göre kaynaklar üzerinde eylem arayabilir veya eylemleri gerçekleştirin.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Azure App Service erişim kısıtlamaları](/azure/app-service/app-service-ip-restrictions)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Rehberlik**: ağ kaynak yapılandırmasını izlemek ve ağ ayarlarında ve App Service ilgili tüm kaynaklarda yapılan değişiklikleri algılamak Için Azure etkinlik günlüğü 'nü kullanın. 

Sanal ağ uç noktası hizmeti kullanımı için uygulamaları denetleyen bir ilke gibi App Service için çeşitli Azure Ilke yerleşik tanımlarının birini uygulayın. Kritik ağ ayarlarında veya kaynaklarda değişiklik yapıldığında tetiklenecek Azure Izleyici içinde uyarılar oluşturun. 

Güvenlik Merkezi 'nde, portalda veya programlı araçlar aracılığıyla ayrıntılı güvenlik uyarılarını ve önerilerini gözden geçirin. Bu bilgileri dışarı aktarın veya ortamınızdaki diğer izleme araçlarına gönderin. Araçlar, uyarıları ve önerileri el ile veya devam eden ve sürekli bir biçimde dışarı aktarmak için kullanılabilir. Bu araçlarla şunları yapabilirsiniz:
 
- Log Analytics çalışma alanına sürekli olarak dışarı aktarma
- Azure Event Hubs sürekli olarak dışarı aktarma (üçüncü taraf Sıems ile Tümleştirmeler için)
- Bir CSV dosyasına (bir kez) aktar

Ağ kaynak yapılandırmalarının izlenmesi ve değişiklikleri hızlıca tespit etmek için otomatikleştirilmiş araçlarla bir işlem oluşturmanız önerilir.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

- [Güvenlik uyarılarını ve önerilerini dışarı aktarma](../security-center/continuous-export.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: Azure depolama, Azure Event Hubs veya Log Analytics günlükleri göndermek Için Azure izleyici ile App SERVICE ORTAMı (ASA) tümleştirin. Denetim düzlemi denetim günlüğü için Azure etkinlik günlüğü tanılama ayarlarını etkinleştirin. Güvenlik Merkezi 'ndeki güvenlik uyarıları Azure etkinlik günlüğünde yayımlanır. Azure App Service ve diğer Azure kaynakları için denetim düzlemi düzeyinde gerçekleştirilen herhangi bir yazma işlemi (PUT, POST, DELETE) için "ne, kim ve ne zaman" olduğunu belirlemenizi sağlayan Azure etkinlik günlüğü verilerini denetleyin. Sorgularınızı daha sonra kullanmak üzere kaydedin, sorgu sonuçlarını Azure panolarına sabitleyin ve günlük uyarıları oluşturun. Ayrıca, telemetrinize programlı bir şekilde erişmek için Application Insights veri erişimi REST API kullanın.

İşletmenizin gereksinimlerine bağlı olarak çeşitli veri kaynaklarına ve bağlayıcılara bağlanmak için kullanılabilen, ölçeklenebilir, bulut Yerel ve güvenlik bilgileri olay yönetimi (SıEM) Microsoft Azure Sentinel kullanın. Ayrıca, Azure Marketi 'nde Barırcuda gibi üçüncü taraf bir güvenlik bilgileri olay yönetimi (SıEM) sistemine de ve yerleşik verileri etkinleştirebilir.

- [ATıCı etkinliğini günlüğe kaydetme](./environment/using-an-ase.md#logging)

- [Azure App Service için tanılama ayarlarını etkinleştirme](troubleshoot-diagnostic-logs.md)

- [Application Insights etkinleştirme](../azure-monitor/app/app-insights-overview.md)

- [Application Insights’tan telemetriyi dışarı aktarma](../azure-monitor/app/export-telemetry.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: App Service denetim düzlemi denetim günlüğü Için Azure etkinlik günlüğü tanılama ayarlarını etkinleştirin. Günlükleri bir Log Analytics çalışma alanına, Azure Olay Hub 'ına veya bir Azure Storage hesabına gönderin.

Denetim düzlemi düzeyinde gerçekleştirilen herhangi bir yazma işlemi (PUT, POST, DELETE) için "ne, kim ve ne zaman", App Service ve diğer Azure kaynakları için Azure etkinlik günlüğü verilerini kullanarak belirlenebilir.

Ayrıca, Azure Key Vault erişim ilkeleri ve denetim geçmişi ile merkezi gizli bir yönetim sağlar. 

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](../azure-monitor/essentials/activity-log.md)

- [Azure App Service için tanılama ayarlarını etkinleştirme](troubleshoot-diagnostic-logs.md)

- [Kaynak Yöneticisi Işlemler](../role-based-access-control/resource-provider-operations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure izleyici 'de, kuruluşunuzun uyumluluk düzenlemelerine göre App Service kaynaklarınızla ilişkili Log Analytics çalışma alanları için günlük tutma süresini ayarlayın.
- [Günlük tutma parametrelerini ayarlama](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Rehberlik**: App Service kaynaklarınızdaki Azure etkinlik günlüğü tanılama ayarlarını, Log Analytics çalışma alanına gönderilen Günlükler ile inceleyin. Terimleri aramak, eğilimleri belirlemek, desenleri analiz etmek ve toplanan verilere göre birçok diğer öngörü sağlamak için Log Analytics sorguları gerçekleştirin.

App Service uygulamalarınız için Application Insights kullanın ve günlük, performans ve hata verilerini toplayın. Azure portal içinde Application Insights tarafından toplanan telemetri verilerini görüntüleyin.

Bir Web uygulaması güvenlik duvarı (WAF) dağıttıysanız, gerçek zamanlı bir Web uygulaması güvenlik duvarı günlüğü kullanarak App Service uygulamalarınıza karşı saldırıları izleyebilirsiniz. Günlük, Web uygulaması güvenlik duvarı uyarılarını izlemek ve eğilimleri kolayca izlemek için Azure Izleyici ile tümleşiktir.

Çeşitli veri kaynakları ve bağlayıcılar ile tümleştirme için Azure Sentinel, ölçeklenebilir ve bulutta yerel güvenlik bilgileri olay yönetimi (SıEM) kullanın. İsteğe bağlı olarak, Azure Marketi 'nde bulunan üçüncü taraf güvenlik bilgileri olay yönetimi çözümüne ve şirket içi verileri etkinleştirin.

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](../azure-monitor/essentials/activity-log.md)

- [Application Insights etkinleştirme](../azure-monitor/app/app-insights-overview.md)

- [App Service Ortamı Azure Application Gateway ile tümleştirme (WAF)](environment/integrate-with-application-gateway.md)

- [Yerleşik Azure Sentinel](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Kılavuz**: Azure aboneliğinizde Güvenlik Merkezi 'ni yapılandırın ve oluşturulan uyarıları gözden geçirin. Etkinlik günlüğü verilerinizi Azure Sentinel gibi bir güvenlik bilgileri olay yönetimi (SıEM) çözümüyle okuyabileceğiniz bir olay hub 'ına almak için Azure Izleyici 'yi kullanın. 

Dağıtılmış bir Azure Web uygulaması güvenlik duvarı (WAF) ile gerçek zamanlı bir Web uygulaması güvenlik duvarı günlüğü kullanarak App Service uygulamalarınıza karşı saldırıları izleyin. Günlük, Web uygulaması güvenlik duvarı (WAF) uyarılarını izlemek ve eğilimleri kolayca izlemek için Azure Izleyici ile tümleşiktir.

- [App Service Ortamı Azure Application Gateway ile tümleştirme (WAF)](environment/integrate-with-application-gateway.md)

- [Güvenlik uyarılarını ve önerilerini dışarı aktarma](../security-center/continuous-export.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: Azure Active Directory (Azure AD), açıkça atanması ve sorgu kurabilmesi gereken yerleşik rollere sahiptir. Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü kullanın.

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [App Service ve Azure Işlevleri için Yönetilen kimlikler kullanma](./overview-managed-identity.md?tabs=dotnet&context=azure%2factive-directory%2fmanaged-identities-azure-resources%2fcontext%2fmsi-context)

- [Azure portal kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Rehberlik**: Azure Active Directory (Azure AD) varsayılan parola kavramına sahip değil. App Service için denetim düzlemi erişimi sağlar.

Genellikle, kendi uygulamalarınızı oluştururken Kullanıcı erişimi için varsayılan parolalar uygulamaktan kaçının. Azure AD, Microsoft hesabı, Facebook, Google veya Twitter gibi App Service için varsayılan olarak kullanılabilen kimlik sağlayıcılarından birini kullanın.

Bunu desteklemeniz gerekmiyorsa anonim erişimi devre dışı bırakın. 

- [Kimlik sağlayıcıları Azure App Service içinde varsayılan olarak kullanılabilir](./overview-authentication-authorization.md#identity-providers)

- [Azure App Service ve Azure Işlevlerinde kimlik doğrulama ve yetkilendirme](overview-authentication-authorization.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturun. Yönetim hesaplarının sayısını izlemek ve izlemek için Güvenlik Merkezi 'ndeki kimlik ve erişim yönetimi özelliklerini kullanın. 

Güvenlik Merkezi 'nden veya yerleşik Azure ilkelerinden öneriler kullanın, örneğin:

- Aboneliğinize birden fazla sahip atanmalıdır. 
- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır

Ağ kaynak yapılandırmalarının izlenmesi ve yönetim hesaplarında yapılan değişiklikleri tespit etmek için bir işlem oluşturun.

- [Azure Güvenlik Merkezi 'ni kullanarak kimlik ve erişimi izleme](../security-center/security-center-identity-access.md)

- [Azure Ilkesini kullanma](../governance/policy/tutorials/create-and-manage.md)

- [Kullanıcılara uygulamalara erişim verme hakkında daha fazla bilgi edinin](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory çoklu oturum açma kullan (SSO)

**Rehberlik**: Azure Active Directory (Azure AD) aracılığıyla App Service kimlik doğrulaması yapın. Kimlik sağlayıcınız için bir OAuth 2,0 hizmeti sağlar ve mobil ve Web uygulamalarına yetkilendirilmiş erişimi sağlar. 

App Service uygulamalar, bir üçüncü taraf kimlik sağlayıcısının sizin için Kullanıcı kimliklerini ve kimlik doğrulama akışını yönettiği federal kimlik kullanır. Bu kimlik sağlayıcıları varsayılan olarak kullanılabilir:

- Azure AD
- Microsoft Hesabı

- Facebook

- Google

- Twitter

Kimlik doğrulama ve yetkilendirmeyi bu sağlayıcılardan biriyle etkinleştirdiğinizde, oturum açma uç noktası Kullanıcı kimlik doğrulaması ve sağlayıcıdan kimlik doğrulama belirteçleri doğrulaması için kullanılabilir.

- [Azure App Service kimlik doğrulaması ve yetkilendirmeyi anlayın](./overview-authentication-authorization.md#identity-providers)

- [Azure App Service kimlik doğrulaması ve yetkilendirme hakkında bilgi edinin](overview-authentication-authorization.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) içinde çok faktörlü kimlik doğrulaması özelliğini etkinleştirin ve Güvenlik Merkezi 'nde kimlik ve erişim yönetimi önerilerini izleyin.

Azure AD için çok faktörlü kimlik doğrulaması uygulayın. Yöneticiler, portaldaki abonelik hesaplarının korunduğundan emin olmalıdır. Abonelik, oluşturduğunuz kaynakları yönettiği için saldırılara açıktır. 

- [Azure Güvenlik çok faktörlü kimlik doğrulaması](/previous-versions/azure/security/develop/secure-aad-app)

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: yönetim görevleri için güvenli, Azure tarafından yönetilen iş istasyonları kullanın

**Kılavuz**: Azure kaynaklarını açmak ve yapılandırmak için yapılandırılmış çok faktörlü kimlik doğrulaması Ile ayrıcalıklı erişim iş istasyonları (Paw) kullanın. 

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: ortamda şüpheli veya güvenli olmayan etkinlikler gerçekleştiğinde günlük ve uyarı oluşturmak için Azure Active Directory (Azure AD) içinde PRIVILEGED IDENTITY Management (PIM) kullanın.

Ayrıca, riskli Kullanıcı davranışında uyarıları ve raporları görüntülemek için Azure AD risk algılamalarını kullanın.

Güvenlik Merkezi 'nde tehdit koruması, ortamınız için Windows makineler, Linux makineleri, App Service ve Azure kapsayıcıları gibi Azure işlem kaynakları için tehdit koruması içeren kapsamlı savunma işlemleri sunar.

- [Privileged Identity Management dağıtma (PıM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD risk algılamalarını anlama](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure işlem kaynakları için tehdit koruması](../security-center/azure-defender.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: IP adresi aralıklarının, ülkelerin veya bölgelerin yalnızca belirli mantıksal gruplarından Azure Portal erişimine izin vermek Için, koşullu erişim adlı konum kullanın.

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: App Service uygulamalarınız için merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifreleme kullanarak verileri korur ve ayrıca, salları, karmaları ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.

- [Azure App Service uygulamalarınızı Azure AD oturum açma bilgilerini kullanacak şekilde yapılandırma](configure-authentication-provider-aad.md)

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) tarafından belirtilen günlüklere sahip eski hesapları bulur. Grup üyeliklerini ve kurumsal uygulamalara erişimi ve rol atamalarını verimli bir şekilde yönetmek için Azure kimlik erişimi Incelemelerini kullanın. Yalnızca amaçlanan kullanıcıların erişmeye devam ettiğinden emin olmak için Kullanıcı erişimini düzenli aralıklarla gözden geçirin. 

- [Azure AD raporlamayı anlama](../active-directory/reports-monitoring/index.yml)

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: App Service uygulamalarınız için merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen veriler ve aktarım sırasında, salts 'ler, karmalar ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolayarak verileri korur.

Azure AD oturum açma etkinliğine, denetimine ve risk olay günlüğü kaynaklarına erişim, Azure Sentinel veya bir üçüncü taraf güvenlik bilgileri olay yönetimi (SıEM) çözümüyle tümleştirmenize imkan tanır. Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturup Log Analytics çalışma alanına denetim ve oturum açma günlüklerini göndererek işlemi kolaylaştırın. İstenen günlük uyarıları Log Analytics içinde yapılandırılabilir.

- [Azure App Service uygulamalarınızı Azure AD oturum açma bilgilerini kullanacak şekilde yapılandırma](configure-authentication-provider-aad.md)

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Yerleşik Azure Sentinel](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapması üzerinde uyarı

**Rehberlik**: App Service uygulamalarınız için merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. 

Azure portal ile denetim düzleminde hesap oturum açma davranışı sapması gibi Kullanıcı kimlikleriyle ilgili şüpheli eylemleri algılanan otomatik yanıtları yapılandırmak için Azure AD Kimlik Koruması kullanın. Ayrıca, daha fazla araştırma için verileri Azure Sentinel 'e aktarabilirsiniz. 

- [Azure App Service uygulamanızı Azure AD oturum açma bilgilerini kullanacak şekilde yapılandırma](configure-authentication-provider-aad.md)

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: destek senaryoları sırasında Microsoft 'un ilgili müşteri verilerine erişimini sağlama

**Rehberlik**: kullanılamıyor; Azure App Service için Müşteri Kasası desteklenmez.

- [Müşteri Kasası tarafından desteklenen hizmetler listesi](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: hassas bilgileri depolayan veya işleyen App Service kaynaklarını izlemeye yardımcı olması için Etiketler kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: bir App Service ortamı için, geliştirme, test ve üretim ortamları için ayrı abonelikler, yönetim grupları veya her ikisini de uygulayın. Aynı şekilde diğer uygulamalardan önemli bilgileri işleyen uygulamaları ayırabilirsiniz. App Service uygulamanızı sanal bir ağa dağıtın. Daha fazla uygulama yalıtımı için ağ güvenlik grupları ve alt ağları kullanın. 

App Service ortamı için iki dağıtım türü vardır (Ao). Her ikisi de iş gereksinimlerinize göre trafiği yalıtmanızı sağlar.

- Dış uygulama hizmeti ortamı-internet erişimli bir IP adresinde App Service Ortamı barındırılan uygulamaları gösterir.

-  İç yük dengeleyici (ıLB) uygulama hizmeti ortamı-sanal ağınızın içindeki bir IP adresi üzerinde App Service Ortamı barındırılan uygulamaları gösterir. İç uç nokta, bir iç yük dengeleyiciye (ıLB) ve bu yüzden ıLB Ao olarak adlandırılmaktadır. 

Çok kiracılı App Service (yalıtılmış katmanda olmayan bir uygulama) için, uygulamanızın sanal ağınızdaki kaynaklara erişimi için sanal ağ tümleştirmesini kullanın.  Bir uygulamayı yalnızca bir Azure sanal ağı içinden bir özel ağdan erişilebilir hale getirmek için özel site erişimini kullanın. Sanal Ağ tümleştirmesi yalnızca uygulamanızdan sanal ağınıza giden çağrılar yapmak için kullanılır. Sanal ağ tümleştirme özelliği, aynı bölgedeki bir sanal ağla ve diğer bölgelerdeki sanal ağlarla birlikte kullanıldığında farklı davranır. 
 
- [App Service Ortamında ağ konusunda dikkat edilmesi gerekenler](environment/network-info.md)

- [Dış Ao oluşturma](environment/create-external-ase.md)

- [Dahili bir AO oluşturma](environment/create-ilb-ase.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: veri tanımlama, sınıflandırma ve kayıp önleme özellikleri henüz App Service için kullanılamadığından, hedefin Internet veya Azure hizmetleri için bir ' Tag ' kullandığı tüm kuralları kaldırarak sanal ağdan alınan veri kaldırma riskini azaltabilirsiniz. 

Microsoft, App Service için temel altyapıyı yönetir ve verilerinizin kaybını veya açıklanmasını engellemek için katı denetimler uygulamıştır.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Kılavuz**: iletim içindeki tüm bilgileri ŞIFRELEMEK için TLS/SSL ayarlarında yapılandırılan varsayılan en düşük TLS 1,2 sürümünü kullanın. Ayrıca tüm HTTP bağlantı isteklerinin HTTPS 'ye yönlendirildiğinden emin olun.

- [Azure App Service Web uygulamaları için iletimde şifrelemeyi anlama](security-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: Şu anda kullanılamıyor. Veri tanımlama, sınıflandırma ve kayıp önleme özellikleri henüz App Service için kullanılamaz. 

Gizli bilgileri işlemekte olabilecek uygulamaları App Service etiketleyin. Uyumluluk amaçlarıyla gerekirse, üçüncü taraf çözümü uygulayın.

Microsoft temel platformu yönetir ve tüm müşteri verilerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: kaynaklara erişimi denetlemek için Azure RBAC kullanma 

**Rehberlik**: Azure Portal App Service denetim düzlemine erişimi denetlemek için Azure Active Directory (Azure AD) içinde Azure rol tabanlı erişim denetimi (Azure RBAC) kullanın.

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Rehberlik**: dosyalar gibi bir App Service uygulamasındaki Web sitesi içeriği, bekleyen içeriği otomatik olarak şifreleyen Azure Storage 'da depolanır. Key Vault içinde uygulama gizli dizilerini depolamayı ve çalışma zamanında almayı seçin.

Müşteri tarafından sağlanan gizlilikler, App Service yapılandırma veritabanlarında depolanırken Rest 'te şifrelenir.

Yerel olarak bağlı diskler geçici depolama (örneğin, D:\Local ve% TMP%) olarak Web siteleri tarafından kullanılabilir olduğunda, bu kullanıcılar bekleyen olarak şifrelenmez.

- [Azure App Service için veri koruma denetimlerini anlayın](./security-recommendations.md#data-protection)

- [Rest 'de Azure depolama şifrelemesini anlama](../storage/common/storage-service-encryption.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Rehberlik**: üretim App Service uygulamalarında ve diğer kritik veya ilgili kaynaklarda yapılan değişikliklerle ilgili uyarılar oluşturmak Için Azure izleyici Ile Azure etkinlik günlüğünü kullanın.

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: otomatikleştirilmiş güvenlik açığı tarama araçlarını çalıştırma

**Rehberlik**: App Service uygulamalarınızın güvende olduğundan ve yaşam döngüsünün süresi boyunca güvenli kalmasını sağlamak Için bir DevSecOps uygulaması benimseyin. DevSecOps, kuruluşunuzun güvenlik ekibini ve yeteneklerini, ekipteki herkesin sorumluluğunda güvenlik sağlamak için DevOps uygulamalarınıza ekler.

App Service uygulamalarınızın güvenliğini sağlamak için Güvenlik Merkezi 'ndeki önerileri gözden geçirin ve izleyin.

- [CI/CD ardışık düzenine sürekli güvenlik doğrulaması ekleme](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops&preserve-view=true)

- [Azure Güvenlik Merkezi güvenlik açığı değerlendirmesi önerilerini uygulama](../security-center/deploy-vulnerability-assessment-vm.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek için risk derecelendirme işlemi kullanın

**Rehberlik**: Microsoft, App Service destekleyen temel sistemler üzerinde güvenlik açığı yönetimi gerçekleştirir. Ancak, güvenlik merkezi 'ndeki önerilerin önem derecesini ve ortamınızdaki riski ölçmek için güvenli puanı kullanabilirsiniz. Güvenli puanınız, kaç Güvenlik Merkezi önerisi azaldığından temel alır. İlk çözümleme önerilerini önceliklendirmek için, her birinin önem derecesini göz önünde bulundurun.

- [Güvenlik önerileri başvuru kılavuzu](../security-center/recommendations-reference.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: abonelikleriniz dahilinde tüm kaynakları (işlem, depolama, ağ, bağlantı noktaları, protokoller vb.) sorgulamak veya öğrenmek Için Azure Kaynak grafiğini kullanın. Kiracınıza uygun izinlerin uygulandığından emin olun ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerini de listeleyebilirsiniz.

Klasik Azure kaynakları kaynak Graph aracılığıyla bulunabilse de, ileri doğru Azure Resource Manager kaynak oluşturmanız ve kullanılması kesinlikle önerilir.

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&preserve-view=true)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Rehberlik**: verileri bir taksonomi halinde mantıksal olarak düzenlemek için meta verileri kullanarak Azure kaynaklarına Etiketler uygulayın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: Azure kaynaklarını düzenlemek ve izlemek için etiketleme, yönetim grupları ve ayrı abonelikler kullanın. Envanteri düzenli aralıklarla uzlaştırır ve bu işlemin bir parçası olarak aboneliklerinizden yetkisiz kaynakların kaldırılmasını sağlayın.

Aşağıdaki yerleşik ilke tanımlarını kullanarak, müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak için Azure Ilkesi ' ni seçin:

- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

Ek bilgi için başvurulan bağlantıları gözden geçirin.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynaklarının envanterini tanımlayın ve saklayın

**Rehberlik**: Kurumsal gereksinimlerinize göre işlem kaynakları Için onaylanan Azure kaynakları ve onaylanan yazılım envanterini oluşturun.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aboneliklerinizde oluşturulabilecek kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın.

Azure Kaynak Grafiği 'ni kullanarak aboneliklerindeki kaynakları sorgulama veya bulma.  Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun. 

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: işlem kaynakları içindeki onaylanmamış yazılım uygulamaları için izleyici

**Kılavuz**: Azure Kaynak Grafiği 'ni kullanarak aboneliklerinizde kaynakları sorgulayın veya bulabilir ve bulunan Azure kaynaklarının kuruluş ilkelerinize göre onaylandığından emin olun.

İşlem kaynakları içinde dağıtılan onaylanmamış yazılım uygulamalarını Izlemek için App Service 'de WebJobs kullanın. Web uygulaması, API uygulaması veya mobil uygulama ile aynı örnekte bir programı veya betiği çalıştırmak için WebJobs kullanın. WebJob yapılandırma ve günlüklerle izleme tanımlayın. WebJob çalıştırma ayrıntıları sayfasında, günlük içeriğinin metnini görmek için çıktıyı geç ' i seçin. Web Işlerinin Linux üzerinde App Service için henüz desteklenmediğini unutmayın.

- [Azure App Service Web Işleri ile arka plan görevleri çalıştırma](webjobs-create.md)

- [Öğretici-uyumluluğu zorlamak için ilke oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Hızlı başlangıç-Azure Kaynak Grafiği gezginini kullanarak ilk kaynak grafik sorgunuzu çalıştırma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Rehberlik**: ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun. Aboneliklerinizde oluşturulabilecek kaynak türlerine kısıtlamalar koymak için Azure Ilkesini kullanın. Kuruluş ilkelerinize göre onaylanmamış tüm dağıtılan yazılım uygulamalarını kaldırın.

- [Azure App Service Web Işleri ile arka plan görevleri çalıştırma](webjobs-create.md)

- [Öğretici-uyumluluğu zorlamak için ilke oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Hızlı başlangıç-Azure Kaynak Grafiği gezginini kullanarak ilk kaynak grafik sorgunuzu çalıştırma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="68-use-only-approved-applications"></a>6,8: yalnızca onaylanan uygulamaları kullan

**Rehberlik**: ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun. Aboneliklerinizde oluşturulabilecek kaynak türlerine kısıtlamalar koymak için Azure Ilkesini kullanın. Kuruluş ilkelerinize göre onaylanmamış tüm dağıtılan yazılım uygulamalarını kaldırın. 

- [Azure App Service Web Işleri ile arka plan görevleri çalıştırma](webjobs-create.md)

- [Öğretici-uyumluluğu zorlamak için ilke oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Hızlı başlangıç-Azure Kaynak Grafiği gezginini kullanarak ilk kaynak grafik sorgunuzu çalıştırma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aboneliklerinizde yalnızca yetkili Azure hizmetlerinin kullanıldığından emin olmak Için yetkisiz Azure hizmetlerini düzenli aralıklarla gözden geçirmek üzere bir işlem oluşturun.

Bu işlem dahilinde Azure Kaynak Grafiği 'ni kullanarak aboneliklerindeki kaynakları sorgulayın veya bulun. Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

Aşağıdaki yerleşik ilke tanımlarını kullanarak aboneliklerinizde oluşturulabilen kaynak türlerine kısıtlamalar koymak için Azure Ilkesini yapılandırın:

- İzin verilmeyen kaynak türleri

- İzin verilen kaynak türleri

Bilgisayar kaynakları içinde dağıtılan onaylanmamış yazılım uygulamalarını izlemek için App Service 'de WebJobs kullanın. Web uygulaması, API uygulaması veya mobil uygulama ile aynı örnekte bir programı veya betiği çalıştırmak için WebJobs kullanın. WebJob yapılandırma ve günlüklerle izleme tanımlayın. WebJob çalıştırma ayrıntıları sayfasında, günlük içeriğinin metnini görmek için çıktıyı geç ' i seçin. Web Işlerinin Linux üzerinde App Service için henüz desteklenmediğini unutmayın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](../governance/policy/samples/built-in-policies.md#general)

- [Azure App Service Web Işleri ile arka plan görevleri çalıştırma](webjobs-create.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: onaylanan yazılım başlıkları envanterini koruyun

**Rehberlik**: aboneliklerinizde yalnızca yetkili Azure hizmetlerinin kullanıldığından emin olmak için aboneliklerinizde yazılım başlıklarını envantere ve gözden geçirmek üzere bir işlem uygulayın.

Aboneliklerinizde kaynakları sorgulamak ya da öğrenmek için bu işlem dahilinde Azure Kaynak Grafiği ' ni kullanın. Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

Aşağıdaki yerleşik ilke tanımlarını kullanarak, müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak için Azure Ilkesini yapılandırın:

- İzin verilmeyen kaynak türleri

- İzin verilen kaynak türleri

Benzer şekilde, bilgisayar kaynakları içinde dağıtılan onaylanmamış yazılım uygulamalarının envanterini almak için App Service 'de WebJobs kullanın. Yapılandırma ve günlükleri ile izlemeyi tanımlayın. WebJob çalıştırma ayrıntıları sayfasında, günlük içeriğinin metnini görmek için çıktıyı geç ' i seçin. Web Işlerinin Linux üzerinde App Service için henüz desteklenmediğini unutmayın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](../governance/policy/samples/built-in-policies.md#general)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" seçeneğini yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak üzere Azure koşullu erişimini yapılandırın.

- [Azure Resource Manager erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: kullanıcıların işlem kaynakları içinde betikleri yürütme yeteneğini sınırlayın

**Rehberlik**: ' App Service de Web işleri, müşterilerin bir Web UYGULAMASı, API uygulaması veya mobil uygulama ile aynı örnekte bir program veya betiği çalıştırmasına olanak tanır. Yapılandırma, kuruluşunuzun izin verilmeyen betikleri kısıtlamak veya sınırlamak üzere tanımlanmasından sorumludur. App Service, betik yürütmeyi yerel olarak sınırlamak için bir mekanizma sağlamaz. Web Işlerinin Linux üzerinde App Service için henüz desteklenmediğini unutmayın.

- [Azure App Service Web Işleri ile arka plan görevleri çalıştırma](webjobs-create.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: yüksek riskli uygulamaları fiziksel olarak veya mantıksal olarak ayırt edin

**Rehberlik**: yüksek riskli App Service uygulamalar için yalıtım sağlamak üzere ayrı abonelikler veya yönetim grupları uygulayın. App Service çevre güvenliği sanal ağların kullanımı üzerinden elde edildiğinden, kendi sanal ağına daha yüksek riskli bir uygulama dağıtın. App Service Ortamı, Azure sanal ağınızdaki bir alt ağa App Service bir dağıtımdır. 

İki tür uygulama hizmeti ortamı, dış uygulama hizmeti ortamı ve ıLB (dahili Load Balancer) uygulama hizmeti ortamı vardır. Gereksinimlerinize göre en iyi mimariyi seçin.

- [App Service Ortamında ağ konusunda dikkat edilmesi gerekenler](environment/network-info.md)

- [Dış App Service ortamı oluşturma](environment/create-external-ase.md)

- [Iç Load Balancer App Service Ortamı oluşturma ve kullanma](environment/create-ilb-ase.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Kılavuz**: Azure ilkesiyle App Service dağıtılan uygulamalarınız için standart güvenlik yapılandırması tanımlayın ve uygulayın.

App Service Web Apps yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft. Web" ad alanındaki Azure Ilke diğer adlarını kullanın.

Gibi yerleşik ilke tanımlarını Uygula:

- App Service bir sanal ağ hizmeti uç noktası kullanmalıdır

- Web uygulamalarına yalnızca HTTPS üzerinden erişilebilir olması gerekir

- Uygulamalarınızdaki en son TLS sürümünü kullanın

Standartlaştırılmış kullanım için yerleşik ilke tanımlarını uygulamak üzere işlemi belgeetmeniz önerilir.   

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&preserve-view=true)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Rehberlik**: Azure App Service uygulamalarınız genelinde güvenli ayarları zorlamak Için Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] etkileri kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilke tanımlarını kullanırken kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure devops veya Azure Repos seçin.

Bilinen bir güvenli yapılandırma dağıtmak için mevcut sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattınızı kullanın.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Azure Repos belgeleri](/azure/devops/repos/?view=azure-devops&preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: yerleşik Azure ilke tanımlarını ve "Microsoft. Web" ad alanındaki Azure ilkesi diğer adlarını kullanarak uyarı, denetim ve sistem yapılandırmalarının uygulanmasını sağlayacak özel ilkeler oluşturun. İlke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: yerleşik Azure ilke tanımlarını ve "Microsoft. Web" ad alanındaki Azure ilkesi diğer adlarını kullanarak uyarı, denetim ve sistem yapılandırmalarının uygulanmasını sağlayacak özel ilkeler oluşturun. 

Azure Ilkesi [Denetim], [reddetme] ve [dağıtım yoksa dağıt] uygulama, Azure kaynaklarınızın yapılandırmasını otomatik olarak zorlayabileceğiniz etkileri.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen kimlik App Service uygulamalarınızı sağlamak Için Yönetilen kimlikler kullanın. Yönetilen kimlikler, sizin kodunuzda kimlik bilgileri olmadan Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmasını sağlar. Azure Key Vault ' de geçici silmenin etkinleştirildiğinden emin olun.

- [Azure Key Vault 'da geçici silme nasıl etkinleştirilir](../key-vault/general/key-vault-recovery.md)

- [App Service için Yönetilen kimlikler kullanma](overview-managed-identity.md)

- [Yönetilen kimlik ile Key Vault kimlik doğrulaması sağlama](../key-vault/general/assign-access-policy-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Rehberlik**: Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlikle App Service dağıtılan uygulamalarınız sağlamak Için Yönetilen kimlikler kullanın. Yönetilen kimlikler, uygulamalarınızın kodunuzda kimlik bilgileri olmadan Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulamasına olanak sağlar.

- [App Service için Yönetilen kimlikler kullanma](overview-managed-identity.md)

- [Yönetilen kimlik ile Key Vault kimlik doğrulaması sağlama](../key-vault/general/assign-access-policy-portal.md)

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

**Rehberlik**: App Service yedekleme ve geri yükleme özelliği, uygulama yedeklemelerini el ile veya bir zamanlamaya göre kolayca oluşturmanızı sağlar. Yedeklemeleri sınırsız bir zaman miktarına kadar tutulacak şekilde yapılandırabilirsiniz. Mevcut uygulamanın üzerine yazarak veya başka bir uygulamaya geri yükleyerek uygulamayı önceki bir anlık görüntüye geri yükleyebilirsiniz.

App Service, uygulamanızı kullanmak üzere yapılandırdığınız bir Azure depolama hesabına ve kapsayıcısına aşağıdaki bilgileri yedekleyebilir:
- Uygulama yapılandırması
- Dosya içeriği
- Uygulamanıza bağlı veritabanı

Düzenli ve otomatik arka yedeklemeler, kuruluş ilkeleriniz tarafından tanımlanan bir sıklıkta yapıldığından emin olun.

- [Azure App Service yedekleme özelliğini anlayın](manage-backup.md)

- [Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarlar](../storage/common/customer-managed-keys-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: uygulamalarınızı yedeklemek için App Service yedekleme ve geri yükleme özelliğini kullanın. Yedekleme Özellikleri, uygulamanızın yedekleme bilgilerini depolamak için bir Azure depolama hesabı gerektirir.

- Azure depolama, bekleyen şifreleme sağlar. sistem tarafından sağlanan anahtarları veya kendi müşteri tarafından yönetilen anahtarlarınızı kullanın. Bu, uygulama verilerinizin Azure 'da bir Web uygulamasında çalışmadığı durumlarda depolandığı yerdir.
- Dağıtım paketinden çalıştırmak, App Service dağıtım özelliğidir. Bir Azure depolama hesabından, paylaşılan erişim Imzası (SAS) URL 'SI kullanarak site içeriğinizi dağıtmanıza olanak tanır.

- Key Vault başvurular App Service bir güvenlik özelliğidir. Çalışma zamanında gizli dizileri uygulama ayarları olarak içeri aktarmanıza olanak tanır. Azure depolama hesabınızın SAS URL 'sini şifrelemek için bunu kullanın.

Başvurulan bağlantılarda daha fazla bilgi bulunabilir.

- [Uygulamanızı Azure’a yedekleme](manage-backup.md)

- [Azure App Service çalıştıran bir uygulamayı geri yükleme](web-sites-restore.md)

- [Azure’da bekleyen veri şifrelemesini anlama](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Şifreleme modeli ve anahtar yönetimi tablosu](../security/fundamentals/encryption-atrest.md)

- [Müşteri tarafından yönetilen anahtarları kullanarak bekleyen şifreleme](configure-encrypt-at-rest-using-cmk.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: App Service uygulamalarınızın yedekleri için geri yükleme sürecini düzenli olarak test edin.

- [Uygulamanızı Azure’a yedekleme](manage-backup.md)

- [Azure App Service Web uygulamasını geri yükleme](web-sites-restore.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: App Service yedeklemeler bir Azure depolama hesabında depolanır. Azure depolama 'daki veriler, 256 bit AES şifrelemesi kullanılarak şifrelenmiş ve şifresi çözülür, en güçlü blok şifrelemeleri kullanılabilir ve FIPS 140-2 uyumludur. Azure depolama şifrelemesi, Windows 'da BitLocker şifrelemeye benzer.

Azure depolama şifrelemesi, hem Kaynak Yöneticisi hem de klasik depolama hesapları da dahil olmak üzere tüm depolama hesapları için etkinleştirilmiştir. Azure depolama şifrelemesi devre dışı bırakılamıyor. Verileriniz varsayılan olarak güvenli hale getirildiğinden, Azure depolama şifrelemesi 'nin avantajlarından yararlanmak için kodunuzu veya uygulamalarınızı değiştirmeniz gerekmez.

Varsayılan olarak, bir depolama hesabındaki veriler Microsoft tarafından yönetilen anahtarlarla şifrelenir. Verilerinizin şifrelenmesi için Microsoft tarafından yönetilen anahtarları kullanabilir veya kendi anahtarlarınız ile şifrelemeyi yönetebilirsiniz. Azure Key Vault ' de geçici silmenin etkinleştirildiğinden emin olun.

- [Bekleyen veriler için Azure depolama şifrelemesini anlayın](../storage/common/storage-service-encryption.md)

- [Azure Key Vault 'da geçici silme nasıl etkinleştirilir](../key-vault/general/key-vault-recovery.md)

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

**Rehberlik**: Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizte ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır.

Ayrıca, abonelikleri açıkça işaretleyin (örneğin, üretim, üretim dışı) ve Azure kaynaklarını net bir şekilde tanımlamak ve kategorilere ayırmak için bir adlandırma sistemi oluşturun.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: sisteminizin olay yanıtı yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve açıkları belirleyip planı gerektiği şekilde gözden geçirin.

- [BT planları ve özellikleri için bkz. NıST 'nin yayın Kılavuzu-test, eğitim ve alıştırma programları](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: Microsoft Güvenlik Yanıt MERKEZI (MSRC), müşterinin verilerine izinsiz veya yetkisiz bir taraf tarafından erişildiğini belirlerse, Microsoft tarafından sizinle iletişim kurmak için güvenlik olayı iletişim bilgileri kullanılacaktır.  Sorunların çözümlendiğinden emin olmak için gerçesonra olayları gözden geçirin.

- [Azure Güvenlik Merkezi güvenlik Ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: güvenlik uyarılarını olay yanıt sisteminizle birleştirme

**Rehberlik**: sürekli dışa aktarma özelliğini kullanarak güvenlik merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Sürekli dışa aktarma, uyarıları ve önerileri el ile veya devam eden sürekli bir biçimde dışa aktarmanız sağlar. Uyarıları iş gereksinimlerine göre Azure Sentinel 'e akışa almak için Güvenlik Merkezi veri bağlayıcısını kullanın.

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

**Rehberlik**: Penettim testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak Için Microsoft bulut Penme test kurallarını izleyin. Microsoft tarafından yönetilen bulut altyapısına, hizmetlere ve uygulamalara yönelik kırmızı takım ve canlı site sızma testi gerçekleştirmek için Microsoft'un stratejisini ve yürütme sürecini kullanın. 

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](../security/benchmarks/overview.md)
- [Azure güvenlik temelleri](../security/benchmarks/security-baselines-overview.md) hakkında daha fazla bilgi edinin
