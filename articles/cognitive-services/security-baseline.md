---
title: Bilişsel hizmetler için Azure Güvenlik temeli
description: Bilişsel hizmetler güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a7f9570a68102eb20c84942ddf9ac73de033f01c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076607"
---
# <a name="azure-security-baseline-for-cognitive-services"></a>Bilişsel hizmetler için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 1.0](../security/benchmarks/overview-v1.md) 'dan bilişsel hizmetler 'e kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve bilişsel hizmetler için geçerli olan ilgili kılavuza göre gruplandırılır. **Denetim** , bilişsel hizmetler için geçerli değildir veya sorumluluğun Microsoft 'un ne olduğu, hariç tutulur.

Bilişsel hizmetlerin Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, tam bilişsel [Hizmetler güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: Microsoft Azure bilişsel hizmetler, katmanlı bir güvenlik modeli sağlar. Bu model, Bilişsel Hizmetler hesaplarınızı belirli bir ağ alt kümesinde güvenlik altına almanızı sağlar. Ağ kuralları yapılandırıldığında yalnızca belirli bir ağ kümesi üzerinden veri isteğinde bulunan uygulamalar hesaba erişim sağlayabilir. Yalnızca belirtilen IP adreslerinden, IP aralıklarından veya Azure sanal ağlardaki alt ağlar listesinden kaynaklanan isteklere izin veren istek filtreleme ile kaynaklarınıza erişimi sınırlayabilirsiniz.

Bilişsel hizmetler için sanal ağ ve hizmet uç noktası desteği belirli bir bölge kümesiyle sınırlıdır.

- [Azure bilişsel hizmetler sanal ağlarını yapılandırma](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)

- [Azure sanal ağlarına genel bakış](../virtual-network/virtual-networks-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: sanal ağların, alt ağların ve ağ arabirimlerinin yapılandırma ve trafiğini izleme ve günlüğe kaydetme

**Rehberlik**: sanal makineler, bilişsel hizmetler Kapsayıcınız ile aynı sanal ağda dağıtıldığında, veri sızdırma riskini azaltmak için ağ güvenlik gruplarını kullanabilirsiniz. Trafik denetimi için ağ güvenlik grupları akış günlüklerini etkinleştirin ve günlükleri bir Azure depolama hesabına gönderin. Ayrıca, bir Log Analytics çalışma alanına ağ güvenlik grupları akış günlükleri gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="13-protect-critical-web-applications"></a>1,3: kritik Web uygulamalarını koruma

**Rehberlik**: bir kapsayıcı içinde bilişsel hizmetler kullanıyorsanız, kapsayıcı dağıtımınızı kötü amaçlı trafiğe filtre uygulayan ve uçtan uca TLS şifrelemesini destekleyen ve kapsayıcı uç noktasının özel ve güvenli kalmasını sağlayan öne bakan bir Web uygulaması güvenlik duvarı çözümü ile dağıtabilirsiniz. 

Bilişsel hizmetler kapsayıcılarının faturalandırma amaçlarıyla ölçüm bilgilerini göndermesi gerektiğini göz önünde bulundurun. Tek özel durum, farklı bir faturalandırma metodolojisini izledikleri için çevrimdışı kapsayıcılardır. İzin verme hatası, bilişsel hizmetler kapsayıcılarının bağlı olduğu çeşitli ağ kanallarının kapsayıcının çalışmasını engelleyecek. Ana bilgisayar, liste bağlantı noktası 443 ve aşağıdaki etki alanlarına izin verir:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Ayrıca, bilişsel hizmetler kapsayıcılarının Microsoft sunucuları için kullandığı güvenli kanallarda güvenlik duvarı çözümünüz için derin paket incelemesini devre dışı bırakmanız gerektiğini unutmayın. Bunun başarısız olması, kapsayıcının düzgün çalışmasını engeller.

- [Azure bilişsel hizmetler kapsayıcı güvenliğini anlama](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: sanal makineler, bilişsel hizmetler Kapsayıcınız ile aynı sanal ağda dağıtıldığında, Azure ilkesiyle ilgili ağ kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın. Redsıs örnekleri için Azure önbelleğinizin ağ yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için, "Microsoft. Biliveservices" ve "Microsoft. Network" ad alanlarında Azure Ilke diğer adlarını kullanın. Ayrıca, gibi yerleşik ilke tanımlarından da yararlanabilirsiniz:

- DDoS koruma standardı etkinleştirilmelidir

Tek bir şema tanımında Azure Resource Manager şablonları, Azure rol tabanlı erişim denetimi (Azure RBAC) ve ilkeler gibi temel ortam yapıtlarını paketleyerek büyük ölçekli Azure dağıtımlarını basitleştirmek için Azure şemaları 'nı kullanın. Şema 'i yeni abonelikler ve ortamlara kolayca uygulayın ve sürüm oluşturma aracılığıyla denetimi ve yönetimi ayrıntılı olarak ayarlayın.

Bir kapsayıcı içinde bilişsel hizmetler kullanıyorsanız, kapsayıcı dağıtımınızı kötü amaçlı trafiğe filtre uygulayan ve uçtan uca TLS şifrelemesini destekleyen ve kapsayıcı uç noktasının özel ve güvenli kalmasını sağlayan bir ön taraftaki Web uygulaması güvenlik duvarı çözümü ile dağıtabilirsiniz. 

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

- [Azure bilişsel hizmetler kapsayıcı güvenliğini anlama](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Rehberlik**: sanal makineler, bilişsel hizmetler Kapsayıcınız ile aynı sanal ağda dağıtıldığında, veri sızdırma riskini azaltmak için ağ güvenlik gruplarını kullanabilirsiniz. Trafik denetimi için ağ güvenlik grupları akış günlüklerini etkinleştirin ve günlükleri bir Azure depolama hesabına gönderin. Ayrıca, bir Log Analytics çalışma alanına ağ güvenlik grupları akış günlükleri gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Kılavuz**: bir kapsayıcı içinde bilişsel hizmetler kullanıyorsanız, kapsayıcı dağıtımınızı kötü amaçlı trafiğe filtre uygulayan ve uçtan uca TLS şifrelemesini destekleyen ve kapsayıcı uç noktasının özel ve güvenli kalmasını sağlayan bir ön uç Web uygulaması güvenlik duvarı çözümü ile dağıtabilirsiniz.  Azure Marketi 'nden, kimlik denetimini devre dışı bırakabilme özelliği ile kimlikler/IP işlevlerini destekleyen bir teklif seçebilirsiniz.

Bilişsel hizmetler kapsayıcılarının faturalandırma amaçlarıyla ölçüm bilgilerini göndermesi gerektiğini göz önünde bulundurun. Tek özel durum, farklı bir faturalandırma metodolojisini izledikleri için çevrimdışı kapsayıcılardır. İzin verme hatası, bilişsel hizmetler kapsayıcılarının bağlı olduğu çeşitli ağ kanallarının kapsayıcının çalışmasını engelleyecek. Ana bilgisayar, liste bağlantı noktası 443 ve aşağıdaki etki alanlarına izin verir:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Ayrıca, bilişsel hizmetler kapsayıcılarının Microsoft sunucuları için kullandığı güvenli kanallarda güvenlik duvarı çözümünüz için derin paket incelemesini devre dışı bırakmanız gerektiğini unutmayın. Bunun başarısız olması, kapsayıcının düzgün çalışmasını engeller.

- [Azure bilişsel hizmetler kapsayıcı güvenliğini anlama](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

- [Azure Market](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Web uygulamalarına trafiği yönetme

**Kılavuz**: bir kapsayıcı içinde bilişsel hizmetler kullanıyorsanız, kapsayıcı dağıtımınızı kötü amaçlı trafiğe filtre uygulayan ve uçtan uca TLS şifrelemesini destekleyen ve kapsayıcı uç noktasının özel ve güvenli kalmasını sağlayan bir ön uç Web uygulaması güvenlik duvarı çözümü ile dağıtabilirsiniz. 

Bilişsel hizmetler kapsayıcılarının faturalandırma amaçlarıyla ölçüm bilgilerini göndermesi gerektiğini göz önünde bulundurun. Tek özel durum, farklı bir faturalandırma metodolojisini izledikleri için çevrimdışı kapsayıcılardır. İzin verme hatası, bilişsel hizmetler kapsayıcılarının bağlı olduğu çeşitli ağ kanallarının kapsayıcının çalışmasını engelleyecek. Ana bilgisayar, liste bağlantı noktası 443 ve aşağıdaki etki alanlarına izin verir:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Ayrıca, bilişsel hizmetler kapsayıcılarının Microsoft sunucuları için kullandığı güvenli kanallarda güvenlik duvarı çözümünüz için derin paket incelemesini devre dışı bırakmanız gerektiğini unutmayın. Bunun başarısız olması, kapsayıcının düzgün çalışmasını engeller.

- [Azure bilişsel hizmetler kapsayıcı güvenliğini anlama](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Rehberlik**: ağ güvenlik gruplarında veya Azure Güvenlik duvarında ağ erişim denetimleri tanımlamak için sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. Bir kuralın uygun kaynak veya hedef alanındaki hizmet etiketi adını (örneğin, Apimanaya) belirterek, karşılık gelen hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

Ayrıca, karmaşık güvenlik yapılandırmasını basitleştirmeye yardımcı olması için uygulama güvenlik gruplarını kullanabilirsiniz. Uygulama güvenlik grupları ağ güvenliğini uygulamanın yapısının doğal bir uzantısı olarak yapılandırmanıza imkan vererek sanal makineleri gruplamanızı ve ağ güvenlik ilkelerini bu gruplara göre tanımlamanızı sağlar.

- [Sanal ağ hizmeti etiketleri](../virtual-network/service-tags-overview.md)

- [Uygulama güvenlik grupları](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview#application-security-groups)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Kılavuz**: Azure ilkesi ile bilişsel hizmetler kapsayıcınıza ilişkin ağ kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın. Redsıs örnekleri için Azure önbelleğinizin ağ yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için, "Microsoft. Biliveservices" ve "Microsoft. Network" ad alanlarında Azure Ilke diğer adlarını kullanın.

Ayrıca, tek bir şema tanımında Azure Resource Manager şablonları, rol tabanlı erişim denetimi (Azure RBAC) ve ilkeler gibi anahtar ortam yapıtlarını paketleyerek büyük ölçekli Azure dağıtımlarını basitleştirmek için Azure şemaları 'nı kullanabilirsiniz. Şema 'i yeni abonelikler ve ortamlara kolayca uygulayın ve sürüm oluşturma aracılığıyla denetimi ve yönetimi ayrıntılı olarak ayarlayın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: bir taksonomi halinde mantıksal olarak organize etmek için bilişsel hizmetler kapsayıcınızda ilişkili ağ kaynakları için Etiketler kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Rehberlik**: ağ kaynak yapılandırmasını izlemek ve bilişsel hizmetler kapsayıcınıza ilişkin ağ kaynaklarına yönelik değişiklikleri algılamak Için Azure etkinlik günlüğünü kullanın. Kritik ağ kaynaklarında yapılan değişiklikler yürürlüğe girdiğinde tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](/azure/azure-monitor/platform/alerts-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Kılavuz**: Azure etkinlik günlüğü tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına, Azure Olay Hub 'ına veya Arşiv için Azure depolama hesabına gönderin. Etkinlik günlükleri, denetim düzlemi düzeyinde bilişsel Hizmetler kapsayıcısında gerçekleştirilen işlemlere ilişkin öngörüler sağlar. Azure etkinlik günlüğü verilerini kullanarak, Redsıs örnekleri için Azure önbelleğiniz için denetim düzlemi düzeyinde gerçekleştirilen herhangi bir yazma işlemi (PUT, POST, DELETE) için "ne, kim ve ne zaman" ı belirleyebilirsiniz.

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](/azure/azure-monitor/platform/activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: denetim düzlemi denetim günlüğü Için Azure etkinlik günlüğü tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına, Azure Olay Hub 'ına veya Arşiv için Azure depolama hesabına gönderin. Azure etkinlik günlüğü verilerini kullanarak, Azure kaynaklarınızın denetim düzlemi düzeyinde gerçekleştirilen herhangi bir yazma işlemi (PUT, POST, SILME) için "ne, kim ve ne zaman" seçeneğini belirleyebilirsiniz.

Ayrıca bilişsel hizmetler, analiz, uyarı ve raporlama amacıyla toplanabilecek ve kullanılabilecek tanılama olayları gönderir. Bilişsel hizmetler kapsayıcısı için tanılama ayarlarını Azure portal aracılığıyla yapılandırabilirsiniz. Bir depolama hesabına, Olay Hub 'ına veya bir Log Analytics çalışma alanına bir veya daha fazla tanılama olayı gönderebilirsiniz.

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Azure bilişsel hizmetler için tanılama ayarlarını kullanma](diagnostic-logging.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure izleyici 'de, Log Analytics çalışma alanı saklama dönemini kuruluşunuzun uyumluluk düzenlemelerine göre ayarlayın. Uzun süreli/arşiv depolama için Azure depolama hesaplarını kullanın.

- [Log Analytics çalışma alanları için günlük saklama parametrelerini ayarlama](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Kılavuz**: Azure etkinlik günlüğü tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına gönderin. Bu Günlükler, sorun tanımlama ve hata ayıklama için kullanılan bir kaynağın çalışması hakkında zengin, sık veriler sağlar. Terimleri aramak, eğilimleri belirlemek, desenleri analiz etmek ve Azure bilişsel hizmetler için toplanabilecek etkinlik günlüğü verilerine göre birçok diğer öngörü sağlamak için Log Analytics sorguları gerçekleştirin.

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](/azure/azure-monitor/platform/activity-log)

- [Azure Izleyici 'de Log Analytics çalışma alanında Azure etkinlik günlüklerini toplama ve çözümleme](/azure/azure-monitor/platform/activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Kılavuz**: Azure Izleyici 'de uyarılar ve ölçümler bölümüne giderek bilişsel hizmetler 'de desteklenen ölçümler üzerinde uyarılar oluşturabilirsiniz.

Bilişsel hizmetler Kapsayıcınız için tanılama ayarlarını yapılandırın ve günlükleri bir Log Analytics çalışma alanına gönderin. Log Analytics çalışma alanınızda, önceden tanımlanmış bir koşullar kümesi gerçekleşirken yapılacak uyarıları yapılandırın. Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz.

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Izleyici 'yi kullanarak günlük uyarıları oluşturma, görüntüleme ve yönetme](/azure/azure-monitor/platform/alerts-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: Azure Active Directory (Azure AD), açıkça atanması ve sorgulanabilir olması gereken yerleşik roller içerir. Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü kullanın.

- [Azure AD 'de PowerShell ile dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Rehberlik**: bilişsel hizmetler 'e denetim düzlemi erişimi, Azure Active Directory (Azure AD) ile denetlenir. Azure AD varsayılan parola kavramına sahip değildir.

Bilişsel hizmetler 'e veri düzlemi erişimi erişim anahtarları aracılığıyla denetlenir. Bu anahtarlar, önbelleğinize bağlanan istemciler tarafından kullanılır ve herhangi bir zamanda yeniden oluşturulabilir.

Uygulamanızda varsayılan parolalar oluşturmanız önerilmez. Bunun yerine, parolalarınızı Azure Key Vault kaydedebilir ve ardından bunları almak için Azure AD 'yi kullanabilirsiniz.

- [Redsıs erişim tuşları için Azure önbelleğini yeniden üretme](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturun. Yönetim hesaplarının sayısını izlemek için güvenlik merkezi kimlik ve erişim yönetimi 'ni kullanın.

Ayrıca, özel yönetim hesaplarını izlemenize yardımcı olmak için, güvenlik merkezi veya yerleşik Azure Ilkeleri önerilerini kullanabilirsiniz; örneğin:

- Aboneliğinize birden fazla sahip atanmalıdır
- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır

- [Kimlik ve erişim (Önizleme) izlemek için Azure Güvenlik Merkezi 'ni kullanma](../security-center/security-center-identity-access.md)

- [Azure Ilkesini kullanma](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory çoklu oturum açma kullan (SSO)

**Rehberlik**: bilişsel hizmetler, kullanıcıların kimliğini doğrulamak için erişim anahtarlarını kullanır ve veri düzlemi düzeyinde çoklu oturum AÇMAYı (SSO) desteklemez. Bilişsel hizmetler için denetim düzlemine erişim, REST API aracılığıyla kullanılabilir ve SSO 'yu destekler. Kimlik doğrulaması yapmak için istekleriniz için yetkilendirme üst bilgisini Azure Active Directory (Azure AD) ' dan edindiğiniz bir JSON (JavaScript Nesne Gösterimi) Web belirtecine ayarlayın.

- [Azure bilişsel hizmetler REST API anlayın](/rest/api/cognitiveservices/)

- [Azure AD ile SSO 'yu anlama](../active-directory/manage-apps/what-is-single-sign-on.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin ve güvenlik merkezi kimlik ve erişim yönetimi önerilerini izleyin.

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

**Rehberlik**: IP adresi aralıklarının veya ülkelerin ve bölgelerin yalnızca belirli mantıksal gruplarından erişime izin vermek için Azure Active Directory (Azure AD) koşullu erişim ' de adlandırılmış konumları yapılandırın.

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifreleme kullanarak verileri korur ve ayrıca, salları, karmaları ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar. Kullanım örneği AD kimlik doğrulamasını destekliyorsa, bilişsel hizmetler API 'nize yönelik isteklerin kimliğini doğrulamak için Azure AD 'yi kullanın. 

Şu anda yalnızca Görüntü İşleme API'si, Yüz Tanıma API'si, Metin Analizi API'si, derinlikli okuyucu, form tanıyıcı, anomali algılayıcısı ve Azure AD 'yi kullanarak Bing Özel Arama kimlik doğrulaması desteği hariç tüm Bing hizmetleri.

- [Bilişsel hizmetler için isteklerin kimliğini doğrulama](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-azure-active-directory)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlar. Ayrıca, müşteri, grup üyeliklerini verimli bir şekilde yönetmek için Azure kimlik erişimi Incelemelerini kullanmaya, kurumsal uygulamalara erişime ve rol atamalarına de olanak sağlar. Kullanıcıların erişimi, yalnızca etkin kullanıcıların erişmeye devam ettiğinden emin olmak için düzenli olarak incelenebilir.

API Management Kullanıcı hesaplarının envanterini sürdürmek için müşteri, gerektiğinde erişimi uzlaştırır. API Management, geliştiriciler API Management kullanarak kullanıma sunabileceğiniz API 'lerin kullanıcılardır. Varsayılan olarak, yeni oluşturulan geliştirici hesapları etkindir ve geliştiriciler grubuyla ilişkilendirilir. Etkin durumda olan geliştirici hesapları, aboneliklerine sahip oldukları tüm API 'Lere erişmek için kullanılabilir.

- [Azure API Management'ta kullanıcı hesaplarını yönetme](../api-management/api-management-howto-create-or-invite-developers.md)

- [API Management kullanıcıların listesini alma](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Kılavuz**: Azure Sentinel veya bir üçüncü taraf SIEM ile tümleştirmenize olanak tanıyan Azure Active Directory (Azure AD) oturum açma etkinliğine, denetim ve risk olay günlüğü kaynaklarına erişiminiz vardır.

Bu işlemi, Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturarak ve Log Analytics çalışma alanına denetim günlüklerini ve oturum açma günlüklerini göndererek kolaylaştırabilirsiniz. Log Analytics içinde, istenen günlük uyarılarını yapılandırabilirsiniz.

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Yerleşik Azure Sentinel](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapması üzerinde uyarı

**Kılavuz**: denetim düzleminde hesap oturum açma davranışı sapması için, Kullanıcı kimlikleriyle ilgili şüpheli eylemler tespit etmek üzere otomatik yanıtları yapılandırmak üzere Azure Active Directory (Azure AD) kimlik koruması ve risk algılama özelliklerini kullanın. Ayrıca, daha fazla araştırma için verileri Azure Sentinel 'e aktarabilirsiniz.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: destek senaryoları sırasında Microsoft 'un ilgili müşteri verilerine erişimini sağlama

**Rehberlik**: bilişsel hizmetler için kullanılamaz. Bilişsel hizmetler için Müşteri Kasası henüz desteklenmiyor.

- [Müşteri Kasası tarafından desteklenen hizmetler listesi](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

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

**Rehberlik**: geliştirme, test ve üretim için ayrı abonelikler veya yönetim grupları uygulayın. Kaynaklar, sanal ağlar veya alt ağlar ile ayrılmalıdır, uygun şekilde etiketlenebilir ve bir ağ güvenlik grubu veya Azure Güvenlik Duvarı tarafından güvenli hale getirilir. Hassas verileri depolayan veya işleyen kaynaklar yeterince yalıtılmalıdır. Hassas verileri depolayan veya işleyen sanal makineler için, kullanımda olmadığında devre dışı bırakmak üzere ilke ve yordamlar uygulayın.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Güvenlik Duvarı ile uyarı veya uyarı yapılandırma ve reddetme](../firewall/threat-intel.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: henüz kullanılamıyor. Veri tanımlama, sınıflandırma ve kayıp önleme özellikleri bilişsel hizmetler için henüz kullanılamamaktadır.

Microsoft, bilişsel hizmetler için temel altyapıyı yönetir ve müşteri verilerinin kaybını veya açıklanmasını engellemek için katı denetimler uygulamıştır.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Rehberlik**: tüm bilişsel hizmetler uç noktaları http zorla TLS 1,2 üzerinden kullanıma sunulur. Zorunlu bir güvenlik protokolüyle, bilişsel hizmetler uç noktasını çağırmaya çalışan tüketiciler şu yönergelere uymalıdır:

- İstemci Işletim sistemi (OS), TLS 1,2 ' i desteklemelidir.
- HTTP çağrısının isteğin bir parçası olarak TLS 1,2 ' i belirtmesini sağlamak için kullanılan dil (ve platform). Dile ve platforma bağlı olarak, TLS belirtmek örtülü olarak veya açık olarak gerçekleştirilir.

- [Azure bilişsel hizmetler için Aktarım katmanı güvenliğini anlama](cognitive-services-security.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: bilişsel hizmetler için veri tanımlama, sınıflandırma ve kayıp önleme özellikleri henüz kullanılamamaktadır. Gizli bilgiler içeren örnekleri Etiketler ve uyumluluk amaçları için gerekliyse üçüncü taraf çözümü uygular.

Microsoft temel platformu yönetir ve tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: kaynaklara erişimi denetlemek için Azure RBAC kullanma 

**Rehberlik**: bilişsel hizmetler Denetim düzlemi (ör. Azure Portal) erişimini denetlemek için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanın. 

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Rehberlik**: bilişsel hizmetler için bekleyen şifreleme, kullanılan belirli bir hizmete bağımlıdır. Çoğu durumda, veriler şifrelenir ve FIPS 140-2 uyumlu 256 bit AES şifrelemesi kullanılarak çözülür. Şifreleme ve şifre çözme, müşteriler için Microsoft tarafından yönetilen bir şifreleme ve erişim. Müşteri verileri varsayılan olarak güvenlidir ve şifrelemenin avantajlarından yararlanmak için kod veya uygulamalarını değiştirmesi gerekmez.

Ayrıca, müşteri tarafından yönetilen anahtarlarınızın depolanması için Azure Key Vault de kullanabilirsiniz. Kendi anahtarlarınızı oluşturabilir ve bunları bir anahtar kasasında saklayabilir veya Azure Key Vault API 'Lerini kullanarak anahtarlar oluşturabilirsiniz.

- [Bekleyen bilgileri şifreleyen hizmetlerin listesi](/azure/cognitive-services/encryption/cognitive-services-encryption-keys-portal)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Rehberlik**: bilişsel hizmetler 'in üretim örneklerine ve diğer kritik veya ilgili kaynaklara yönelik değişikliklerin ne zaman gerçekleştiği hakkında uyarı oluşturmak Için Azure etkinlik günlüğü Ile Azure izleyici 'yi kullanın.

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](/azure/azure-monitor/platform/alerts-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: abonelikleriniz dahilinde tüm kaynakları (işlem, depolama, ağ, bağlantı noktaları, protokoller vb.) sorgulamak veya öğrenmek Için Azure Kaynak grafiğini kullanın. Kiracınızda uygun (okuma) izinlere sahip olun ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerini numaralandırın.

Klasik Azure kaynakları kaynak Graph aracılığıyla bulunabilse de, ileri doğru Azure Resource Manager kaynak oluşturmanız ve kullanılması kesinlikle önerilir.

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Kılavuz**: Azure kaynaklarına Etiketler uygulayarak bunları bir taksonomi halinde mantıksal olarak organize etmek için meta veriler verirsiniz.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: uygun yerlerde, redsıs örnekleri ve ilgili kaynaklar Için Azure önbelleğini düzenlemek ve izlemek için etiketleme, yönetim grupları ve ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

Ayrıca, aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak için Azure Ilkesini kullanın:

- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:

- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

Ayrıca, aboneliklerdeki kaynakları sorgulamak veya bulmayı sağlamak için Azure Kaynak grafiğini kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:
- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

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

**Rehberlik**: Azure ilkesiyle bilişsel hizmetler Kapsayıcınız için standart güvenlik yapılandırması tanımlayın ve uygulayın. Redsıs örnekleri için Azure önbelleğinizin yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft. Biliveservices" ad alanındaki Azure Ilke diğer adlarını kullanın.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Kılavuz**: Azure kaynaklarınız genelinde güvenli ayarları zorlamak Için Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] etkileri kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Rehberlik**: bilişsel hizmetler kapsayıcıları ve ilgili kaynaklarınız Için özel Azure ilke tanımları veya Azure Resource Manager şablonları kullanıyorsanız, kodunuzu güvenli bir şekilde depolamak ve yönetmek için Azure Repos kullanın.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow)

- [Azure Repos belgeleri](/azure/devops/repos/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: sistem yapılandırmalarına uyarı vermek, denetlemek ve zorlamak için özel ilkeler oluşturmak üzere "Microsoft. cache" ad alanındaki Azure ilke diğer adlarını kullanın. Ayrıca, ilke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: sistem yapılandırmalarına uyarı vermek, denetlemek ve zorlamak üzere özel Azure ilke tanımları oluşturmak Için "Microsoft. Biliveservices" ad alanındaki Azure ilke diğer adlarını kullanın. Redsıs örnekleri ve ilgili kaynaklar için Azure önbelleğiniz için otomatik olarak yapılandırma uygulamak üzere Azure Ilkesi [Denetim], [reddetme] ve [dağıtım yoksa dağıt] etkileri kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: BILIŞSEL hizmetler API 'nize erişmek için kullanılan Azure App Service çalışan Azure sanal makineleri veya Web uygulamaları için, bilişsel hizmetler anahtar yönetimini basitleştirmek ve güvenli hale getirmek üzere Azure Key Vault ile birlikte yönetilen hizmet kimliği kullanın. Key Vault geçici silmenin etkinleştirildiğinden emin olun.

- [Azure yönetilen kimliklerle tümleştirme](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault oluşturma](/azure/key-vault/quick-create-portal)

- [Key Vault kimlik doğrulaması yapma](../key-vault/general/authentication.md)

- [Key Vault erişim ilkesi atama](../key-vault/general/assign-access-policy-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Rehberlik**: BILIŞSEL hizmetler API 'nize erişmek için kullanılan Azure App Service çalışan Azure sanal makineleri veya Web uygulamaları için, bilişsel hizmetler anahtar yönetimini basitleştirmek ve güvenli hale getirmek üzere Azure Key Vault ile birlikte yönetilen hizmet kimliği kullanın. Key Vault geçici silmenin etkinleştirildiğinden emin olun.

Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik ile Azure hizmetleri sağlamak için Yönetilen kimlikler kullanın. Yönetilen kimlikler, kodunuzda kimlik bilgileri olmadan Azure Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmanıza olanak sağlar.

- [Yönetilen kimlikleri yapılandırma](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Azure yönetilen kimliklerle tümleştirme](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: kod içinde kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayın. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar.

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: işlem dışı Azure kaynaklarına yüklenecek dosyaları önceden Tara

**Rehberlik**: Microsoft 'un kötü amaçlı yazılımdan koruma özelliği, Azure hizmetlerini destekleyen temel ana bilgisayarda (örneğin, redin Için Azure önbelleği) etkinleştirilir, ancak müşteri içeriğinde çalışmaz.

App Service, Data Lake Storage, BLOB depolama, PostgreSQL için Azure veritabanı gibi işlem dışı Azure kaynaklarına karşıya yüklenen tüm içerikleri önceden tarayın ve bu şekilde devam edin. Microsoft bu örneklerdeki verilerinize erişemez.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli otomatik arka pencerelere emin olun

**Rehberlik**: Microsoft Azure Depolama hesabınızdaki veriler, dayanıklılık ve yüksek kullanılabilirlik sağlamak için her zaman otomatik olarak çoğaltılır. Azure depolama, verilerinizi geçici donanım arızaları, ağ veya güç kesintileri ve çok büyük doğal olağanüstü durumlar dahil olmak üzere planlı ve planlanmamış olaylardan korunabilecek şekilde kopyalar. Verilerinizi aynı veri merkezinde, aynı bölgedeki ZGen veri merkezlerinde veya coğrafi olarak ayrılmış bölgelerde çoğaltmayı tercih edebilirsiniz. 

Ayrıca, verileri arşiv katmanına yedeklemek için yaşam döngüsü yönetimi özelliğini de kullanabilirsiniz. Ayrıca, depolama hesabında depolanan yedeklemeleriniz için geçici silme özelliğini etkinleştirin.

- [Azure depolama yedekliliği ve Service-Level sözleşmelerini anlama](../storage/common/storage-redundancy.md)

- [Azure Blob depolama yaşam döngüsünü yönetme](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Azure Depolama blobları için geçici silme](../storage/blobs/soft-delete-blob-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: bilişsel hizmetler ve ilgili kaynakları dağıtmak için Azure Resource Manager kullanın. Azure Resource Manager, çözümünüzü geliştirme yaşam döngüsü boyunca yeniden dağıtmanıza ve kaynaklarınızın tutarlı bir durumda dağıtıldığından emin olmanızı sağlayan şablonları dışarı aktarma özelliğini sağlar. Azure Resource Manager şablonu dışarı aktarma API 'sini düzenli aralıklarla çağırmak için Azure Otomasyonu 'nu kullanın. Azure Key Vault içinde önceden paylaşılan anahtarları yedekleyin.

- [Azure Resource Manager'a genel bakış](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager şablonu kullanarak bilişsel hizmetler kaynağı oluşturma](https://docs.microsoft.com/azure/cognitive-services/resource-manager-template?tabs=portal)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Kaynak grupları-şablonu dışarı aktarma](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Otomasyonu 'na giriş](../automation/automation-intro.md)

- [Azure 'da Anahtar Kasası anahtarlarını yedekleme](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: gerektiğinde yalıtılmış bir aboneliğe düzenli olarak Azure Resource Manager şablonlarının dağıtımını düzenli olarak gerçekleştirdiğinizden emin olun. Yedeklenen önceden paylaşılan anahtarların test geri yüklemesi.

- [ARM şablonları ve Azure portal kaynak dağıtma](../azure-resource-manager/templates/deploy-portal.md)

- [Azure 'da Anahtar Kasası anahtarlarını geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: Azure Resource Manager şablonlarınızı güvenli bir şekilde depolamak ve yönetmek Için Azure DevOps kullanın. Azure DevOps 'da yönettiğiniz kaynakları korumak için, belirli kullanıcılar, yerleşik güvenlik grupları veya Azure Active Directory (Azure AD) içinde tanımlanan, Azure DevOps ile tümleşikse veya Team Foundation Server ile tümleşikse Active Directory izinler verebilir veya reddedebilirsiniz.

Müşteri tarafından yönetilen anahtarları korumak için Azure rol tabanlı erişim denetimi kullanın. Anahtarları yanlışlıkla veya kötü amaçlı silmeye karşı korumak için Key Vault Soft-Delete ve Temizleme korumasını etkinleştirin. 

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps 'da izinler ve gruplar hakkında](/azure/devops/organizations/security/about-permissions)

- [Azure Depolama blobları için geçici silme](../storage/blobs/soft-delete-blob-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: Kuruluşunuz için bir olay yanıt kılavuzu oluşturun. Tüm personelin rollerine ek olarak algılama aşamasından olay sonrası gözden geçirme aşamasına kadar tüm olay işleme/yönetim aşamalarını tanımlayan yazılı olay yanıt planları bulunduğundan emin olun.

- [Azure Güvenlik Merkezi 'nde Iş akışı otomasyonlarını yapılandırma](../security-center/security-center-planning-and-operations-guide.md)

- [Kendi güvenlik olay yanıtı işleminizi oluşturma kılavuzu](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Ayrıca, NıST 'nin bilgisayar güvenliği olay Işleme kılavuzumuzu, kendi olay yanıtı planınızın oluşturulmasına yardımcı olması için de kullanabilirsiniz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Rehberlik**: Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizte ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır. 

Ayrıca, abonelikleri açıkça işaretleyin (örn. üretim, üretim dışı) ve Azure kaynaklarını net bir şekilde tanımlamak ve kategorilere ayırmak için bir adlandırma sistemi oluşturun.

**Sorumluluk**: Müşteri

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

**Rehberlik**: sürekli dışa aktarma özelliğini kullanarak güvenlik merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Sürekli dışa aktarma, uyarıları ve önerileri el ile veya devam eden sürekli bir biçimde dışa aktarmanız sağlar. Uyarıları Azure Sentinel 'e akışa almak için Güvenlik Merkezi veri bağlayıcısını kullanabilirsiniz.

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

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](/azure/security/benchmarks/overview)
- [Azure güvenlik temelleri](/azure/security/benchmarks/security-baselines-overview) hakkında daha fazla bilgi edinin
