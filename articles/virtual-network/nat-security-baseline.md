---
title: Sanal ağ NAT için Azure Güvenlik temeli
description: Sanal ağ NAT güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 501d5b17358501f17d17e8884ceec0bcbbb2ab1c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612414"
---
# <a name="azure-security-baseline-for-virtual-network-nat"></a>Sanal ağ NAT için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 1,0](../security/benchmarks/overview-v1.md) ' dan Microsoft sanal ağ NAT 'ye kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar.
İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve sanal ağ NAT için geçerli olan ilgili kılavuza göre gruplandırılır. Sanal ağ NAT için geçerli olmayan **denetimler** dışlandı.

 
Sanal ağ NAT 'nin Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam sanal ağ NAT güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: sanal ağların, alt ağların ve NIC 'lerin yapılandırmasını ve trafiğini izleyin ve günlüğe kaydedin

**Rehberlik**: sanal ağ NAT, ağ güvenlik grupları (NSG) ile giden trafiğin denetlenmesini desteklemez.  Gelen trafiğe yalnızca giden kaynaklı bir akışa yanıt olarak izin verilir.

Ancak, ağ güvenlik grubu (NSG) akış günlükleri, giden kaynaklı trafiği izlemek için NAT ağ geçidi kaynaklarıyla birlikte kullanılabilir.

Azure Güvenlik Merkezi 'ni kullanın ve Azure ağ kaynaklarınızın güvenliğini sağlamaya yardımcı olmak için ağ koruma önerilerini izleyin. Denetim için ağ güvenlik grubu akış günlüklerini etkinleştirin ve günlükleri bir Azure depolama hesabına gönderin. Akış günlüklerini bir Log Analytics çalışma alanına da gönderebilir ve sonra Azure bulutunuzda trafik desenlerine yönelik Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme, etkin noktaları ve güvenlik tehditlerini anlama, trafik akışı desenlerini anlama ve ağ yapılandırmalarını belirlemeyi bilme yeteneğidir. 

- [Sanal ağ NAT genel bakış](./nat-overview.md)

- [NAT ağ geçidi kaynağı](./nat-gateway-resource.md)

- [Ağ güvenlik grubu akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Rehberlik**: anormal etkinlikleri araştırmak Için Ağ İzleyicisi paket yakalamayı etkinleştirin. 

- [Ağ Izleyicisi örneği oluşturma](../network-watcher/network-watcher-create.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Rehberlik**: yerleşik veya özel Azure ilke tanımlarını ve ATAMALARıNı kullanarak NAT Gateway kaynaklarıyla yapılandırılmış alt ağlar için standart güvenlik yapılandırması tanımlayın ve uygulayın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Ağ için Azure Ilke örnekleri](../governance/policy/samples/built-in-policies.md#network)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Rehberlik**: kaynak yapılandırmasını Izlemek ve NAT ağ geçidi kaynaklarınızda ve sanal ağ kaynaklarınız üzerinde yapılan değişiklikleri algılamak Için Azure etkinlik günlüğü 'nü kullanın. Kritik kaynaklar değiştirildiğinde sizi bilgilendirmek için Azure Izleyici 'de uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: Endpoint cihazları, ağ kaynakları ve diğer güvenlik sistemleri tarafından oluşturulan güvenlik verilerini toplamak Için Azure Izleyici aracılığıyla sanal ağ NAT ile ilgili günlükleri alma. Azure Izleyici 'de analiz sorgulamak ve gerçekleştirmek için Log Analytics çalışma alanlarını kullanın ve uzun süreli ve arşiv depolama için Azure depolama hesaplarını kullanın.

Alternatif olarak, bu verileri Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye etkinleştirebilir ve bu verileri ayarlayabilirsiniz.

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md) 

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure Izleyici ve üçüncü taraf SıEM tümleştirmesi ile çalışmaya başlama](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Kılavuz**: otomatik olarak kullanılabilir olan etkinlik günlükleri, okuma IŞLEMLERI (Get) haricinde NAT ağ geçidi kaynaklarınız için tüm yazma IŞLEMLERINI (put, Post, Delete) içerir. Etkinlik günlükleri, sorun giderirken bir hata bulmak veya kuruluşunuzdaki bir kullanıcının bir kaynağı nasıl değiştirdiğinizi izlemek için kullanılabilir.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure 'da günlüğe kaydetme ve farklı günlük türlerini anlama](../azure-monitor/essentials/platform-logs-overview.md) 

Sanal ağ NAT Şu anda müşteriler tarafından yapılandırılabilen ek tanılama günlükleri üretmiyor.

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Rehberlik**: güvenlik günlükleri ve olayları 'nda bulunan anormal etkinlikleri izlemek ve uyarmak için Log Analytics çalışma alanıyla Azure Güvenlik Merkezi 'ni kullanın. Bunun yerine, verileri Azure Sentinel 'de etkinleştirebilir ve ayarlayabilirsiniz.

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md) 

- [Azure Güvenlik Merkezi 'nde uyarıları yönetme](../security-center/security-center-managing-and-responding-alerts.md) 

- [Log Analytics günlük verilerinde uyarı alma](../azure-monitor/alerts/tutorial-response.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: abonelikleriniz dahilinde tüm kaynakları (işlem, depolama, ağ, bağlantı noktaları ve protokoller gibi) sorgulamak/öğrenmek Için Azure Kaynak grafiğini kullanın. 

Kiracınızda uygun (okuma) izinlere sahip olun ve aboneliklerinizdeki tüm Azure aboneliklerini ve kaynaklarını numaralandırın.

Klasik Azure kaynakları kaynak Graph aracılığıyla keşfedilebilir, ancak Azure Resource Manager tabanlı kaynakların oluşturulması ve kullanılması önemle önerilir.

- [Azure Kaynak Grafiği sorguları](../governance/resource-graph/first-query-portal.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Rehberlik**: Azure kaynaklarınıza, kaynak gruplarına ve aboneliklerinize mantıksal olarak bunları bir taksonomi halinde düzenlemek için Etiketler uygulayın. Her etiket bir ad ve bir değer çiftinden oluşur. Örneğin, "Ortam" adını ve "Üretim" değerini üretimdeki tüm kaynaklara uygulayabilirsiniz.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: Azure kaynaklarının yönetimi için Etiketler uygulayın.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md) 

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md) 

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynakları envanterini tanımlama ve sürdürme

**Rehberlik**: Şu anda kullanılamıyor

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: abonelikleriniz içinde oluşturulabilecek kaynak türlerine yönelik kısıtlamalar koymak Için Azure ilkesini kullanın.
Azure Kaynak Grafiği 'ni kullanarak aboneliklerinde kaynakları sorgulama/bulma. Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md) 
- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: ortamınızda sağlayabileceğiniz hizmetleri kısıtlamak Için Azure ilkesini kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](../governance/policy/samples/built-in-policies.md#general)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Rehberlik**: uygulanamaz; Sanal ağ NAT 'nin güvenlik yapılandırması yok.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilke tanımları, Azure Resource Manager şablonları ve istenen durum yapılandırması betikleri gibi kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure DevOps kullanın. Azure DevOps 'da yönettiğiniz kaynaklara erişmek için, Azure DevOps ile tümleşikse veya TFS ile tümleşikse Active Directory belirli kullanıcılara, yerleşik güvenlik gruplarına veya Azure Active Directory (Azure AD) tanımlanmış gruplara izin verebilir veya vermeyebilirsiniz. 

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow) 

- [Azure DevOps 'da izinler ve gruplar hakkında](/azure/devops/organizations/security/about-permissions)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: uygulanamaz; Sanal ağ NAT 'nin güvenlik yapılandırması yok.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: bir olay yanıt yordamının, olayların çözümüne göre uygun öncelik düzeyini alması için uygun olay yanıt işlemlerinin gerçekleşebilmesini sağlamak üzere oluşturulması gerekir.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Rehberlik**: doğru risk veya tehdit puanlaması sağlamak için teklif için bir olay Puanlama ve öncelik düzeyi oluşturulması gerekir. böylece, olaylar çözümüne göre uygun öncelik düzeyini alır.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: bir olay Güvenliği Yanıt yordamının oluşturulması ve test edileceği için, olaylar tahmin edilen çözünürlüğe göre uygun önceliklendirmesinin doğru düzeyde azalmasını sağlamak için teklif oluşturulmalıdır ve test edilmelidir.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: bir olay Güvenliği Yanıt yordamının oluşturulması ve test edileceği için, olaylar tahmin edilen çözünürlüğe göre uygun önceliklendirmesinin doğru düzeyde azalmasını sağlamak için teklif oluşturulmalıdır ve test edilmelidir.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: güvenlik uyarılarını olay yanıt sisteminizle birleştirme

**Rehberlik**: bir olay Güvenliği Yanıt yordamının oluşturulması ve test edileceği için, olaylar tahmin edilen çözünürlüğe göre uygun önceliklendirmesinin doğru düzeyde azalmasını sağlamak için teklif oluşturulmalıdır ve test edilmelidir.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: güvenlik uyarılarına yanıtı otomatikleştirme

**Rehberlik**: bir olay Güvenliği Yanıt yordamının oluşturulması ve test edileceği için, olaylar tahmin edilen çözünürlüğe göre uygun önceliklendirmesinin doğru düzeyde azalmasını sağlamak için teklif oluşturulmalıdır ve test edilmelidir.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="penetration-tests-and-red-team-exercises"></a>Sızma Testleri ve Red Team Alıştırmaları

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: Penetme testleri ve Red ekibi alıştırmaları](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Azure kaynaklarınızın düzenli olarak sızma testini gerçekleştirin ve tüm kritik güvenlik bulgularını düzeltmeye dikkat edin

**Rehberlik**: Penettim testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak Için Microsoft bulut Penme test kurallarını izleyin. Microsoft tarafından yönetilen bulut altyapısına, hizmetlere ve uygulamalara yönelik kırmızı takım ve canlı site sızma testi gerçekleştirmek için Microsoft'un stratejisini ve yürütme sürecini kullanın. 

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](../security/benchmarks/overview.md)
- [Azure güvenlik temelleri](../security/benchmarks/security-baselines-overview.md) hakkında daha fazla bilgi edinin