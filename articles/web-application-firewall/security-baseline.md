---
title: Azure Web uygulaması güvenlik duvarı için Azure Güvenlik temeli
description: Azure Web uygulaması güvenlik duvarı güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6aa3e2b84c4349e2134ddeb2a60fd193f56f84e8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875980"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Azure Web uygulaması güvenlik duvarı için Azure Güvenlik temeli

Bu güvenlik temeli [Azure Güvenlik kıyaslama sürümü 1,0](../security/benchmarks/overview-v1.md) ' dan Azure Web uygulaması güvenlik duvarı 'na kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure Web uygulaması güvenlik duvarı için geçerli olan ilgili kılavuza göre gruplandırılır. 

> [!NOTE]
> **Denetimler** Azure Web uygulaması güvenlik duvarı için geçerli değildir veya Microsoft 'un sorumluluğunu Microsoft 'un dışında tutulur. Azure Web uygulaması güvenlik duvarının Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, **[tam Azure Web uygulaması güvenlik duvarı güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/azure-web-application-firewall-security-baseline-v1.1.xlsx)** bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="13-protect-critical-web-applications"></a>1,3: kritik Web uygulamalarını koruma

**Rehberlik**: SQL ekleme ve siteler arası betik oluşturma gibi yaygın güvenlik açıklarından ve güvenlik açıklarından Web uygulamalarının merkezi koruması Için Microsoft Azure Web uygulaması güvenlik duvarını (WAF) kullanın. 

- Algılama modu: ağ trafiğini öğrenmek, anlaşılması ve hatalı pozitif sonuçları gözden geçirmek için bu modu kullanın. Tüm tehdit uyarılarını izler ve günlüğe kaydeder. Diagnostics ve WAF günlüğünün seçili ve açık olduğundan emin olun. WAF 'nin algılama modunda çalışırken gelen istekleri engellemediğine unutmayın.
- Önleme modu: kurallar tarafından algılanan saldırılar ve saldırıları engeller. Saldırgan bir "403 yetkisiz erişim" özel durumu alır ve bağlantı kapatılır. Önleme modu WAF günlüklerinde bu tür saldırıları kaydeder.

WAF algılama moduyla ağ trafiğinizi temel alarak. Trafik ihtiyaçlarınızı belirledikten sonra, istenmeyen trafiği Bock önlemek için WAF 'yi önleme modunda yapılandırın.

WAF tarafından korunmayan web özellikli herhangi bir kaynak için Güvenlik Merkezi 'nden yüksek öneme sahip önerilere uyun.  

- [Web uygulaması güvenlik duvarı, kural grupları ve kuralları](ag/application-gateway-crs-rulegroups-rules.md) 

- [Application Gateway WAF modları](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Ön kapıda WAF modları](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: trafiğe izin vermek ve trafiği engellemek Için Azure Web uygulaması güvenlik duvarı (WAF) ile özel kurallar kullanın. Örneğin, bir IP adresi aralığından gelen tüm trafik engellenebilir. Azure WAF ' i önleme modunda çalışacak şekilde yapılandırarak, kurallar tarafından algılanan yetkisiz saldırıları ve saldırıları engeller. Saldırgan bir "403 yetkisiz erişim" özel durumu alır ve bağlantı kapatılır. Önleme modu WAF günlüklerinde bu tür saldırıları kaydeder.

- [Application Gateway WAF modları](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Ön kapıda WAF modları](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Web uygulamalarına trafiği yönetme

**Rehberlik**: Azure Web uygulaması güvenlik duvarı (WAF), Azure 'un web uygulaması korumalarının temel bileşenidir. Azure WAF ' i kullanarak Web uygulamaları için önceden yapılandırılmış yönetilen kural kümesi ile genel sık kullanılan ve güvenlik açıklarından, OWASP Ilk 10 kategorisinden gelen bilinen saldırı imzaları için merkezi koruma sağlayın.

Kurallar ve kural gruplarıyla Azure WAF 'yi, Web uygulaması gereksinimlerine uyacak ve hatalı pozitif sonuçları ortadan kaldıracak şekilde özelleştirin. Bir WAF arkasındaki her site için bir Azure WAF Ilkesini siteye özgü yapılandırmaya izin verecek şekilde ilişkilendirin. Azure WAF, coğrafi filtreleme, hız sınırlaması, Azure tarafından yönetilen varsayılan kural kümesi kurallarını destekler. ve özel kurallar, bir uygulamanın ihtiyaçlarına uyacak şekilde oluşturulabilir. 

Belirlenen bir süre boyunca algılama modundaki ağ trafiğini taban çizgisi oluşturduktan sonra, Azure WAF 'yi önleme modunda çalışacak şekilde yapılandırın. Azure WAF, önleme modundaki kurallar tarafından algılanan yetkisiz saldırıları ve saldırıları engeller. Saldırgan bir "403 yetkisiz erişim" özel durumu alır ve bağlantı kapatılır. Önleme modu WAF günlüklerinde bu tür saldırıları kaydeder.

- [Application Gateway WAF modları](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Ön kapıda WAF modları](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

- [Web uygulaması güvenlik duvarı, kural grupları ve kuralları](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-crs-rulegroups-rules?tabs=owasp31)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Rehberlik**: ortak uygulama yapılandırmalarını (örneğin, Apache ve IIS) algılamak için Etiketler Ile bir Azure aboneliği üzerinde kaynakları oluşturun, ilişkilendirin ve mantıksal olarak düzenleyin. 

Uygulanan etiket meta verilerine dayalı olarak Azure Web uygulaması güvenlik duvarı (WAF) ilkelerine kurallar ve kural grupları uygulayın.

- [Application Gateway WAF ilkesi](/cli/azure/network/application-gateway/waf-policy)

- [Ön kapıda WAF ilkesi](/cli/azure/network/front-door/waf-policy)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Kılavuz**: Azure Application Gateway alt ağınızdaki Azure Web uygulaması güvenlik duvarı (WAF) ile ilişkili ağ güvenlik grupları ve ağ güvenliği ve trafik akışıyla ilgili diğer kaynaklar için Etiketler kullanın. Tek tek ağ güvenlik grubu kuralları için "Açıklama" alanını kullanarak ağa veya ağdan trafiğe izin veren tüm kurallar için iş ihtiyacı, süresi vb. belirtin.

Tüm kaynakların etiketlerle oluşturulmasını ve mevcut etiketlenmemiş kaynakları bilgilendirmesini sağlamak için etiketlemeyle ilgili yerleşik Azure Ilke tanımlarından herhangi birini ("etiket ve onun değeri gerektir" gibi) kullanın.

Azure PowerShell veya Azure CLı ' yi seçerek etiketlere göre kaynakları arayabilir veya bunlarla ilgili eylemleri gerçekleştirin.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Kılavuz**: Azure etkinlik günlüğü 'nü kullanarak ağ kaynak yapılandırmasını Izleyin ve Azure Web uygulaması güvenlik duvarı (WAF) dağıtımlarınızla ilgili ağ ayarları ve kaynakları için değişiklikleri tespit edin. Kritik ağ ayarlarında veya kaynaklarda değişiklik yapıldığında tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: onaylanan zaman eşitleme kaynaklarını kullanın

**Kılavuz**: Azure Web uygulaması güvenlik duvarı (WAF) IÇIN, UDP üzerinden bağlantı noktası 123 gibi uygun bağlantı noktası ve PROTOKOLLE bir NTP sunucusuna erişime izin vermek üzere bir ağ kuralı oluşturun.

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Kılavuz**: Azure Web uygulaması güvenlik duvarı (WAF) günlüklerini Azure Sentinel veya üçüncü taraf SIEM gibi bir merkezi güvenlik günlüğü yönetimi çözümüne gönderilmek üzere yapılandırın. Bu Günlükler Azure etkinlik, tanılama ve gerçek zamanlı WAF günlüklerini içerir, bu Günlükler daha sonra Azure Izleyici, Excel ve Power BI gibi farklı araçlarla görüntülenebilir. Azure Web uygulaması güvenlik duvarı günlükleri, Azure WAF 'nin değerlendirdiği, eşleştiği ve engellediği veriler hakkında fikir verir.

Azure Sentinel 'de, Azure WAF 'deki güvenlik olaylarına genel bakış sağlayan yerleşik bir Azure WAF çalışma kitabı vardır. Bu çalışma kitabı olayları, eşleşen ve engellenen kuralları ve güvenlik duvarı günlüklerinde günlüğe kaydedilen diğer her şeyi içerir. Bu telemetri, Azure Sentinel tarafından toplanan WAF olaylarına dayalı olarak bildirim almak veya düzeltme eylemleri gerçekleştirmek için PlayBook Otomasyonu 'nu açmak üzere kullanılabilir.

- [Etkinlik günlüklerini görüntüle](../azure-resource-manager/management/view-activity-logs.md)

- [Application Gateway için tanılama günlükleri](../application-gateway/application-gateway-diagnostics.md)

- [Microsoft Web uygulaması güvenlik duvarındaki verileri Azure Sentinel 'e bağlama](../sentinel/connect-azure-waf.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: denetim, güvenlik ve tanılama günlüklerine erişmek Için Azure Web uygulaması güvenlik duvarı (WAF) kaynaklarınızın günlüğünü etkinleştirin. Azure Web uygulaması güvenlik duvarı, algılanan her tehdit üzerinde, yapılandırılmış tanılama günlüklerinde kullanılabilir hale getirilen ayrıntılı raporlama sağlar. Otomatik olarak kullanılabilen etkinlik günlükleri Olay kaynağını, tarihi, kullanıcıyı, zaman damgasını, kaynak adreslerini, hedef adreslerini ve diğer yararlı öğeleri içerir.

- [Günlüğe kaydetmeye genel bakış](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#logging)

- [Azure Izleyici günlük sorgusuna genel bakış](../azure-monitor/logs/log-query-overview.md)

- [Azure platformu günlüklerine genel bakış](../azure-monitor/essentials/platform-logs-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure Web uygulaması güvenlik duvarı (WAF) günlüklerini özel bir depolama hesabına gönderin ve bekletme ilkesini tanımlayın. Kuruluşunuzun uyumluluk gereksinimlerine göre Log Analytics çalışma alanı saklama süresini ayarlamak için Azure Izleyici 'yi kullanın.
- [Depolama hesabı için izlemeyi yapılandırma](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Kılavuz**: Azure Web uygulaması güvenlik duvarı (WAF), algılanan her tehdit hakkında ayrıntılı raporlama sağlar. Günlük kaydı, denetim ve sorun giderme için önemli olan işlemler ve hatalar hakkında zengin bilgiler sağlayan Azure Tanılama günlükleriyle tümleşiktir. 

Azure WAF örnekleri, raporlama için uyarı ve sistem durumu bilgilerini göndermek üzere güvenlik merkezi ile tümleşiktir. Korumasız Web uygulamalarını algılamak ve bu güvenlik açığı olan kaynakları korumak için Güvenlik Merkezi 'nin önerilerini kullanın. 

Azure Sentinel, WAF 'deki güvenlik olaylarına genel bakış sağlayan yerleşik bir WAF-güvenlik duvarı olayları çalışma kitabına sahiptir. Bunlar, olayları, eşleşen ve engellenen kuralları ve güvenlik duvarı günlüklerinde günlüğe kaydedilen diğer her şeyi içerir.

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](/azure/azure-monitor/platform/activity-log)

- [Azure Application Gateway için tanılama ayarlarını etkinleştirme](../application-gateway/application-gateway-diagnostics.md)

- [Azure ön kapılarında ölçümleri ve günlükleri izleme](../frontdoor/front-door-diagnostics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Kılavuz**: Azure etkinlik günlüğü tanılama ayarlarını ve Azure WAF 'niz için tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına gönderin. Terimleri aramak, eğilimleri belirlemek, desenleri analiz etmek ve toplanan verilere göre birçok diğer öngörü sağlamak için Log Analytics sorguları gerçekleştirin. WAF ölçümleri temelinde anormal etkinlikler için uyarılar oluşturun. Örneğin, engellenen istek sayısı ' X ' ile aşıldıysanız, ' Y ' yapın.

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](/azure/azure-monitor/essentials/diagnostic-settings-legacy)

- [Azure 'da uyarı oluşturma](../azure-monitor/alerts/tutorial-response.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="28-centralize-anti-malware-logging"></a>2,8: kötü amaçlı yazılımdan koruma 'yı merkezileştirme

**Kılavuz**: Azure Web uygulaması güvenlik duvarını (WAF), gelen trafiğin ek incelemesi için kritik Web uygulamaları önünde dağıtın. 

Azure WAF, Web uygulamalarınızın yaygın güvenlik açıklarından ve güvenlik açıklarından merkezi bir şekilde korunmasını sağlar ve SQL kullanımı, siteler arası komut dosyası oluşturma, kötü amaçlı yazılım yüklemeleri ve DDoS saldırıları gibi saldırıları engellemek için gelen Web trafiğini inceleyerek uygulamalarınızın güvenliğini sağlar.

- [Azure WAF dağıtma](ag/create-waf-policy-ag.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: Azure Active Directory (Azure AD) yerleşik rollere sahiptir ve açıkça atanması gerekir. Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü kullanın.

- [Azure AD 'de PowerShell ile dizin rolü alma](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: WAF içinde kullanılabilir yerel yönetici ataması yok. Ancak, ortamda Azure WAF kaynakları üzerinde yönetim denetimi için izin verebilen Azure Active Directory (Azure AD) yönetici rolleri olabilir.
Azure Web uygulaması güvenlik duvarı (WAF) örneklerine erişimi olan adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturmak iyi bir uygulamadır. Yönetim hesaplarının sayısını izlemek için Güvenlik Merkezi 'nin kimlik ve erişim yönetimi özelliklerini kullanın.

- [Azure Güvenlik Merkezi kimlik ve erişimini anlama](../security-center/security-center-identity-access.md)

- [PostgreSQL için Azure veritabanı 'nda yönetici kullanıcılar oluşturmayı anlayın](https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account)

- [Azure Ilkesini kullanma](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin ve Güvenlik Merkezi 'nin kimlik ve erişim yönetimi önerilerini izleyin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: tüm yönetim görevleri için adanmış makineler (ayrıcalıklı erişim Iş Istasyonları) kullanın

**Kılavuz**: Azure Web uygulaması güvenlik duvarı (WAF) ve ilgili kaynakları oturum açmak ve yapılandırmak için yapılandırılmış çok faktörlü kimlik doğrulaması Ile ayrıcalıklı erişim iş istasyonu (Paw) kullanın.

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: ortamda şüpheli veya güvenli olmayan bir etkinlik olduğunda Günlükler ve uyarılar oluşturmak için Azure Active Directory (Azure AD) güvenlik raporları kullanın. Kimlik ve erişim etkinliğini izlemek için Güvenlik Merkezi 'ni kullanın.

- [Riskli etkinlik bayrağıyla işaretlenen Azure AD kullanıcılarını belirleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Güvenlik Merkezi’nde kullanıcıların kimliğini ve erişim etkinliğini izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: koşullu erişim ilkesinin konum koşulunu yapılandırın ve adlandırılmış konumlarınızı yönetin.

Adlandırılmış konumlara sahip IP adresi aralıklarının veya ülkelerin ve bölgelerin mantıksal gruplandırmaları oluşturun. Azure Key Vault gizli dizileri gibi hassas kaynaklarınıza erişimi, yapılandırılmış adlandırılmış konumlara göre kısıtlayın.

- [Azure Active Directory (Azure AD) koşullu erişim 'deki konum koşulu nedir?](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifreleme kullanarak verileri korur ve ayrıca, salları, karmaları ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.
- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlar. Grup üyeliklerini etkin bir şekilde yönetmek, kurumsal uygulamalara erişmek ve rol atamalarına yönelik Azure kimlik erişimi Incelemelerini kullanın. Yalnızca etkin kullanıcıların erişmeye devam ettiğinden emin olmak için Kullanıcı erişimini düzenli aralıklarla gözden geçirin.

- [Azure AD raporlamayı anlama](/azure/active-directory/reports-monitoring)

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: Azure Active Directory (Azure AD) oturum açma etkinliğini tümleştirin, Azure Sentinel gibi herhangi bir SIEM veya izleme aracıyla, olay günlüğü kaynaklarını denetleyin ve riskten yararlanın.

Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturup Log Analytics çalışma alanına denetim günlüklerini ve oturum açma günlüklerini göndererek bu işlemi kolaylaştırın. Log Analytics çalışma alanında istenen uyarıları yapılandırın.

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapması üzerinde uyarı

**Rehberlik**: Azure Active Directory (Azure AD) ' ın (Azure AD) risk ve kimlik koruma özelliklerini kullanarak otomatik yanıtları, Kullanıcı kimlikleriyle ilgili şüpheli eylemler tespit etmek üzere yapılandırın. Daha fazla araştırma için Azure Sentinel 'e veri alma.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Kılavuz**: Azure Web uygulaması güvenlik duvarı (WAF) ve hassas bilgileri depolayan veya işleyen ilgili kaynakları izlemeye yardımcı olması için Etiketler kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: ortam türü ve veri duyarlılığı düzeyi gibi bireysel güvenlik etki alanları için, geliştirme, test ve üretim ortamları gibi ayrı abonelikler ve yönetim grupları kullanarak yalıtım uygulayın. 

Azure Active Directory (Azure AD) rol tabanlı erişim denetimi (Azure RBAC) ile Azure kaynaklarına erişimi denetleme.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Rehberlik**: yoldaki tüm hassas bilgileri şifreleyin. Azure Web uygulaması güvenlik duvarı (WAF) örneklerine ve ilgili kaynaklara bağlanan tüm istemcilerin TLS 1,2 veya üzerini anlaşamadığından emin olun.

Uygun olduğunda, bekleyen ve şifreli şifreleme için Güvenlik Merkezi önerilerini izleyin.

- [Azure ile iletim sırasında şifrelemeyi anlama](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: kaynaklara erişimi yönetmek için Azure RBAC kullanma 

**Rehberlik**: Azure rol tabanlı erişim denetimi (Azure RBAC) Ile Web uygulaması güvenlik duvarı gibi Azure kaynaklarınıza erişimi denetleyin.

- [Azure 'da Azure RBAC yapılandırma](../role-based-access-control/role-assignments-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Kılavuz**: Azure Web uygulaması güvenlik duvarı (WAF) ve ilgili kaynakları dahil tüm Azure kaynakları için bekleyen şifreleme kullanın. Microsoft, Azure 'un şifreleme anahtarlarınızı yönetmesine izin vermesini önerir, ancak bazı örneklerde kendi anahtarlarınızı yönetmeniz için seçenek vardır.

- [Azure’da bekleyen veri şifrelemesini anlama](../security/fundamentals/encryption-atrest.md)

- [Müşteri tarafından yönetilen şifreleme anahtarlarını yapılandırma](https://docs.microsoft.com/azure/storage/common/customer-managed-keys-configure-key-vault?tabs=portal)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Kılavuz**: Azure Web uygulaması güvenlik duvarını (WAF), önceden belirlenmiş bir süre için algılama modundaki ağ trafiğini taban çizgisi oluşturulduktan sonra önleme modunda çalışacak şekilde yapılandırın. 

Önleme modundaki Azure WAF, kurallar tarafından algılanan yetkisiz saldırıları ve saldırıları engeller. Saldırgan bir "403 yetkisiz erişim" özel durumu alır ve bağlantı kapatılır. Önleme modu WAF günlüklerinde bu tür saldırıları kaydeder.

- [Application Gateway ile Azure Güvenlik Merkezi arasındaki tümleştirmeye genel bakış](https://docs.microsoft.com/azure/security-center/security-center-partner-integration#overview)

- [Application Gateway WAF modları](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Ön kapıda WAF modları](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: aboneliklerinizde, işlem, depolama, ağ, bağlantı noktaları ve protokoller gibi tüm kaynakları sorgulamak veya öğrenmek Için Azure Kaynak grafiğini kullanın.

Kiracınızda uygun (okuma) izinlere sahip olun ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerini numaralandırın. Klasik Azure kaynakları kaynak Graph aracılığıyla bulunabilse de, ileri doğru Azure Resource Manager kaynak oluşturmanız ve kullanılması kesinlikle önerilir.

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Kılavuz**: Azure Web uygulaması güvenlik duvarı (WAF) ilkelerinde Etiketler kullanın. Etiketler, kaynaklarla ilişkilendirilebilir ve bir müşteri aboneliğinde bu kaynaklara erişimi düzenlemek için mantıksal olarak uygulanabilir. 

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: Azure WAF ve ilgili kaynakları düzenlemek ve izlemek için uygun yerlerde etiketleme, yönetim grupları ve ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynaklarının envanterini tanımlayın ve saklayın

**Rehberlik**: Kurumsal ihtiyaçlarına göre yapılandırma dahil olmak üzere onaylanan kaynakların envanterini oluşturun.

Aboneliklerinizde oluşturulabilecek kaynak türlerine kısıtlamalar koymak için Azure Ilkesini kullanın. Abonelikler içindeki kaynakları sorgulamak ve bulmak için Azure Kaynak Grafı'nı kullanın. Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aboneliklerinizde oluşturulabilecek kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın.
Azure Kaynak Grafiği 'ni kullanarak aboneliklerindeki Azure Web uygulaması güvenlik duvarı (WAF) kaynaklarını sorgulama veya bulma. Ortamda bulunan tüm Azure WAF ve ilgili kaynakların onaylandığından emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Rehberlik**: Azure WAF dağıtımını veya belirli bir WAF türünü (örneğin, Azure WAF v1 vs v2) reddetmek Için Azure Ilkesiyle onaylanmamış Azure WAF kaynaklarını izleyin ve kaldırın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: ortamınızda sağlayabileceğiniz hizmetleri kısıtlamak Için Azure ilkesini kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](/azure/governance/policy/samples/built-in-policies#general)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak bir kullanıcının Azure Kaynak Yöneticisi ile etkileşime geçmesini sınırlamak Için Azure koşullu erişimi kullanın.

- [Azure Kaynak Yöneticisi 'ne erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: yüksek riskli uygulamaları fiziksel olarak veya mantıksal olarak ayırt edin

**Rehberlik**: Azure Web uygulaması güvenlik duvarı (WAF), yüksek riskli uygulamaları ayırmak için ağlar, kaynak grupları veya abonelikler aracılığıyla farklı ortamlarla ilişkilendirilebilir.

- [Azure sanal ağına genel bakış](../virtual-network/virtual-networks-overview.md)

- [Kaynaklarınızı Azure yönetim gruplarıyla düzenleme](../governance/management-groups/overview.md)

- [Abonelik kararı kılavuzu](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Kılavuz**: Azure Web uygulaması güvenlik duvarı (WAF) dağıtımlarınızla ilgili ağ ayarları için standart güvenlik yapılandırması tanımlayın ve uygulayın.

Azure uygulama ağ geçitleri, sanal ağlar, ağ güvenlik grupları ve yerleşik ilke tanımları kullanmak üzere özel ilkeler oluşturmak için "Microsoft. Network" ad alanındaki Azure Ilke diğer adlarını kullanın.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Kılavuz**: Azure Web uygulaması güvenlik duvarı (WAF) ve ilgili kaynaklarınız üzerinde güvenli ayarları zorlamak Için Azure ilkesi [reddetme] ve [dağıtım yok] etkileri kullanın. 

Azure WAF 'nizin güvenlik yapılandırmasını ve kuruluşunuzun gerektirdiği ilgili kaynakları korumak için Azure Resource Manager şablonlarını kullanın.

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

- [Uyumluluğu zorunlu tutmak için ilkeleri oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager şablonlarına genel bakış](../azure-resource-manager/templates/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilkeleri ve Azure Resource Manager şablonları gibi kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure DevOps kullanın.

Belirli kullanıcılar, yerleşik güvenlik grupları veya Azure Active Directory (Azure AD) ile tümleşik, Azure DevOps ile tümleşikse veya Team Foundation Server (TFS) ile tümleşikse Azure AD 'de tanımlanan gruplar için izin verin veya reddedin.

- [Azure DevOps 'da kod depolama](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure DevOps 'da izinler ve gruplar hakkında](/azure/devops/organizations/security/about-permissions)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: yerleşik Azure ilke tanımlarını ve sistem yapılandırmalarının uyarı vermesi, denetlemesi ve uygulanması için özel ilkeler oluşturmak üzere "Microsoft. Network" ad alanındaki Azure ilkesi diğer adları ' nı kullanın. İlke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure İlkesi belgeleri](/azure/governance/policy)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: yerleşik Azure ilke tanımlarını ve sistem yapılandırmalarının uyarı vermesi, denetlemesi ve uygulanması için özel ilkeler oluşturmak üzere "Microsoft. Network" ad alanındaki Azure ilkesi diğer adları ' nı kullanın. 

Azure kaynaklarınızın yapılandırmasını otomatik olarak zorlamak için [Denetim], [reddetme] ve [dağıtım yok] etkileri ' nı kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure İlkesi belgeleri](/azure/governance/policy)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: sertifikaları güvenli bir şekilde depolamak için Azure Key Vault kullanın. Azure Key Vault, gizli dizileri, anahtarları ve SSL sertifikalarını korumak için kullanabileceğiniz, platform tarafından yönetilen bir gizli depodır. 

Azure Application Gateway, HTTPS özellikli dinleyicilerine eklenen sunucu sertifikaları için Key Vault tümleştirmeyi destekler. 

- [Azure PowerShell kullanarak SSL sonlandırmasını Key Vault sertifikalarla yapılandırma](../application-gateway/configure-keyvault-ps.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: kod içinde kimlik bilgilerini belirlemek Için kimlik bilgisi tarayıcısını uygulayın ve bu da keşfedilen kimlik bilgilerini Azure Key Vault gibi daha güvenli konumlara taşımayı teşvik eder.
- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: Azure Key Vault için geçici silmenin etkinleştirildiğinden emin olun. Geçici silme, silinen anahtar kasalarının ve anahtar, gizli dizi ve sertifika gibi kasa nesnelerinin kurtarılmasına olanak tanır.

- [Azure Key Vault geçici silme kullanma](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-powershell&amp;preserve-view=true)

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

**Rehberlik**: Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her uyarıya önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin, uyarıyı vermek için kullanılan bulma veya ölçümde ne kadar uygun olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin güven düzeyini temel alır.

Abonelikleri açık bir şekilde işaretleyin (örneğin, üretim, üretim dışı) ve Azure kaynaklarını net bir şekilde tanımlamak ve kategorilere ayırmak için bir adlandırma sistemi oluşturun.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve açıkları belirleyip planı gerektiği şekilde gözden geçirin.
- [BT planları ve özellikleri için test, eğitim ve alıştırma programlarını denemek üzere NıST 'nin yayın kılavuzuna bakın](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: Microsoft Güvenlik Yanıt MERKEZI (MSRC), müşterinin verilerine izinsiz veya yetkisiz bir taraf tarafından erişildiğini belirlerse, Microsoft tarafından sizinle iletişim kurmak için güvenlik olayı iletişim bilgileri kullanılacaktır. Sorunların çözümlendiğinden emin olmak için gerçesonra olayları gözden geçirin.
- [Azure Güvenlik Merkezi güvenlik Ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: güvenlik uyarılarını olay yanıt sisteminizle birleştirme

**Rehberlik**: sürekli dışa aktarma özelliğini kullanarak güvenlik merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Sürekli dışa aktarma, uyarıları ve önerileri el ile veya devam eden sürekli bir biçimde dışa aktarmanız sağlar. Azure Güvenlik Merkezi veri bağlayıcısını, kuruluşunuzun gereksinimlerine göre uyarıları Azure Sentinel 'e akışa almak için kullanın.

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

**Rehberlik**: sızma testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak Için Microsoft katılım kurallarını izleyin. Microsoft 'un, Microsoft tarafından yönetilen bulut altyapısına, hizmetlerine ve uygulamalarına karşı kırmızı ekip oluşturma ve canlı site sızma testi stratejisini kullanın. 

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](/azure/security/benchmarks/overview)
- [Azure güvenlik temelleri](/azure/security/benchmarks/security-baselines-overview) hakkında daha fazla bilgi edinin
