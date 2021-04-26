---
title: Azure Izleyici için Azure Güvenlik temeli
description: Azure Izleyici güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f002c7196b864d4a04beda1124d0519af612b716
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600242"
---
# <a name="azure-security-baseline-for-azure-monitor"></a>Azure Izleyici için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 1.0](../security/benchmarks/overview-v1.md) 'Dan Azure izleyici 'ye kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure izleyici için geçerli olan ilgili kılavuza göre gruplandırılır. **Denetimler** Azure izleyici için geçerli değildir veya Microsoft 'un sorumluluğunu Microsoft 'un dışında tutulur.

Azure Izleyici 'nin Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Azure izleyici güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: sanal ağınızdaki özel bir uç nokta üzerinden Azure SaaS hizmetlerine (örneğin, Azure izleyici) ve Azure 'da barındırılan müşteri/iş ortağı hizmetlerine erişime izin vermek Için Azure özel bağlantısını etkinleştirin. Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağı üzerinden geçer ve genel İnternet’ten etkilenme olasılığı ortadan kaldırılır.

Trafiğin Azure Izleyici 'ye ulaşmasını sağlamak için, ağ güvenlik grupları aracılığıyla gelen ve giden trafiğe izin vermek üzere "AzureMonitor" hizmet etiketlerini kullanın. Kullanılabilirlik Izleme sınama trafiğinin Azure Izleyici 'ye ulaşmasını sağlamak için, ağ güvenlik grupları aracılığıyla tüm gelen trafiğe "Applicationınsiizsavailability" hizmet etiketini kullanın. Uyarı bildirimlerinin müşteri uç noktalarına ulaşmasını sağlamak için, ağ güvenlik grupları aracılığıyla gelen trafiğe izin vermek üzere "ActionGroup" hizmet etiketini kullanın.

Sanal ağ kuralları, Azure Izleyicisini yalnızca bir sanal ağ içindeki seçili alt ağlardan gönderilen iletişimleri kabul edecek şekilde etkinleştirir.

Bilgisayarların internet 'e bağlanmasına gerek kalmadan internet 'e doğrudan bağlanamamış bilgisayarlar adına, Azure Izleyici 'deki bir Log Analytics çalışma alanına veri göndermek için Log Analytics ağ geçidini kullanın. 

- [Azure Izleyici için özel bağlantı ayarlama](/azure/azure-monitor/platform/private-link-security)

- [Azure Izleyici 'de Log Analytics ağ geçidini kullanarak internet erişimi olmadan bilgisayarları bağlama](/azure/azure-monitor/platform/gateway)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: sanal ağların, alt ağların ve ağ arabirimlerinin yapılandırma ve trafiğini izleme ve günlüğe kaydetme

**Kılavuz**: Azure izleyici bir çekirdek hizmettir ve doğrudan bir sanal ağa dağıtımı desteklemez, temel alınan altyapı Microsoft tarafından işlenir. Bununla birlikte, Azure Izleyici sunumuna ağ bağlantısı yapan kaynaklar için ağ güvenlik grubuyla ağınızı güvenli hale getirin. Trafik denetimi için ağ güvenlik grubu akış günlüklerini etkinleştirin ve günlükleri bir depolama hesabına gönderin. Ayrıca, akış günlüklerini bir Log Analytics çalışma alanına gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır.

Azure Izleyici 'yi özel bağlantıyla birlikte kullanırken, ' özel uç nokta tarafından işlenen veriler (ın/OUT) ' gibi ağ günlüğüne erişim sahibi olursunuz.

- [Azure Izleyici aracıları için ağ gereksinimleri](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Azure Izleyici 'de Log Analytics ağ geçidini kullanarak internet erişimi olmadan bilgisayarları bağlama](/azure/azure-monitor/platform/gateway)

- [Ağ güvenlik grubu akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Kılavuz**: trafiğin Azure izleyici 'ye ulaşmasını sağlamak Için, ağ güvenlik grupları aracılığıyla gelen ve giden trafiğe izin vermek üzere "AzureMonitor" hizmet etiketlerini kullanın. Kullanılabilirlik izleme sınama trafiğinin Azure Izleyici 'ye ulaşmasını sağlamak için, ağ güvenlik grupları aracılığıyla tüm gelen trafiğe "Applicationınsiizsavailability" hizmet etiketini kullanın. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

- [Hizmet etiketlerini anlama ve kullanma](../virtual-network/service-tags-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Kılavuz**: Azure Izleyici, Azure çekirdek hizmetleri 'nin bir parçasıdır ve ayrı ayrı hizmet olarak dağıtılamaz. Azure izleyici Aracısı ve Application Insights SDK dahil olmak üzere Azure Izleyici bileşenleri kaynaklarla birlikte dağıtılabilir ve bu, bu kaynakların güvenlik duruşunu etkileyebilir.

- [Azure Izleyici aracıları için ağ gereksinimleri](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Azure Izleyici 'de Log Analytics ağ geçidini kullanarak internet erişimi olmadan bilgisayarları bağlama](/azure/azure-monitor/platform/gateway) 

- [Bkz. Application Insights kullanmaya başlama](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#get-started)

- [Kullanılabilirlik Web testlerini ayarlama](app/monitor-web-app-availability.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Rehberlik**: kaynak yapılandırmasını Izlemek ve Azure izleyici ile ilgili ağ kaynaklarınızda yapılan değişiklikleri algılamak Için Azure etkinlik günlüğünü kullanın. Bu kritik ağ kaynaklarında yapılan değişiklikler gerçekleşirken tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](/azure/azure-monitor/platform/alerts-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Kılavuz**: Azure izleyici, kaynaklarında yapılan değişiklikleri günlüğe kaydetmek için etkinlik günlüklerini kullanır. Bu günlükleri Azure depolama, Olay Hub 'ı veya Log Analytics çalışma alanına dışarı aktarabilirsiniz. Endpoint cihazları, ağ kaynakları ve diğer güvenlik sistemleri tarafından oluşturulan güvenlik verilerini toplamak için Azure Izleyici aracılığıyla günlük alma. Azure Izleyici 'de, verilere karşı analiz sorgulayabilir ve bu verileri gerçekleştirebilir, Azure depolama hesaplarını, günlüklerin uzun süreli/arşiv depolaması için kullanabilirsiniz.

Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz.

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](/azure/azure-monitor/platform/diagnostic-settings)

- [Azure Izleyici ile Azure sanal makine iç konak günlüklerini toplama](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Izleyici ve üçüncü taraf SıEM tümleştirmesi ile çalışmaya başlama](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Kılavuz**: Azure Izleyici, etkinlik günlüklerini kullanır, etkinlik günlüğü otomatik olarak etkinleştirilir ve Azure izleyici kaynakları üzerinde gerçekleştirilen işlem (örneğin, işlem ne zaman, işlem oluştuğunda, işlem durumu ve diğer yararlı denetim bilgileri) kaydedilir. 

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](/azure/azure-monitor/platform/diagnostic-settings)

- [Azure 'da günlüğe kaydetme ve farklı günlük türlerini anlama](/azure/azure-monitor/platform/platform-logs-overview)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure izleyici 'de kuruluşunuzun uyumluluk düzenlemelerine göre Log Analytics çalışma alanı saklama süresini ayarlayın. Günlüklerinizin uzun süreli/arşiv depolaması için Azure depolama hesaplarını kullanın.

- [Log Analytics veri saklama süresini değiştirme](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Azure depolama hesabı günlükleri için bekletme ilkesini yapılandırma](/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Rehberlik**: anormal davranışlar için günlükleri çözümleyin ve izleyin ve sonuçları düzenli olarak gözden geçirin. Günlükleri gözden geçirmek ve günlük verilerinde sorgular gerçekleştirmek için Azure Izleyici 'yi ve bir Log Analytics çalışma alanını kullanın.

Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz.

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Log Analytics sorguları ile çalışmaya başlama](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Azure Izleyici 'de özel sorgular gerçekleştirme](/azure/azure-monitor/log-query/get-started-queries)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Rehberlik**: güvenlik günlükleri ve olayları 'nda bulunan anormal etkinlikleri izlemek ve uyarmak için Log Analytics çalışma alanıyla Azure Güvenlik Merkezi 'ni kullanın. Alternatif olarak, Azure Sentinel 'de ve yerleşik verileri etkinleştirebilir.

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Güvenlik Merkezi 'nde uyarıları yönetme](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics günlük verilerinde uyarı alma](/azure/azure-monitor/learn/tutorial-response)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Kılavuz**: Azure rol tabanlı erişim denetimi (Azure RBAC), Azure kaynaklarına erişimi rol atamaları aracılığıyla yönetmenizi sağlar. Bu rolleri kullanıcılara atayabilir, hizmet sorumlularını ve yönetilen kimlikleri gruplara atayabilirsiniz. Belirli kaynaklar için önceden tanımlanmış yerleşik roller vardır ve Azure CLI, Azure PowerShell veya Azure portal gibi araçlardan yararlanarak bu rollerin envanteri çıkarılabilir ve bunlar sorgulanabilir.

- [PowerShell ile Azure Active Directory (Azure AD) içinde dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturun. Yönetim hesaplarının sayısını izlemek için Azure Güvenlik Merkezi kimlik ve erişim yönetimi 'ni kullanın.

Ayrıca, Microsoft Hizmetleri için ayrıcalıklı rolleri Privileged Identity Management Azure Active Directory (Azure AD) kullanarak tam zamanında/tam erişimi etkinleştirebilirsiniz ve Azure Resource Manager.

- [Azure AD Privileged Identity Management genel bakış](../active-directory/privileged-identity-management/pim-configure.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory çoklu oturum açma kullan (SSO)

**Rehberlik**: mümkün olan yerlerde, tek başına bağımsız kimlik bilgilerini hizmet başına yapılandırmak yerine Azure Active Directory (Azure AD) SSO kullanın. Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini kullanın.

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

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: IP adresi aralıklarının veya ülkelerin/bölgelerin yalnızca belirli mantıksal gruplarından erişime izin vermek Için adlandırılmış konumlar kullanın.

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifrelemeyi kullanarak verileri korur. Azure AD Ayrıca, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlar. Ayrıca, grup üyeliklerini etkin bir şekilde yönetmek, kurumsal uygulamalara erişmek ve rol atamaları için Azure kimlik erişimi Incelemelerini kullanın. Yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için, Kullanıcı erişimi düzenli olarak incelenebilir.

- [Azure AD raporlamayı anlama](/azure/active-directory/reports-monitoring/)

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: herhangi bir SIEM/izleme aracıyla tümleştirmenize imkan tanıyan Azure Active Directory (Azure AD) oturum açma etkinliğine, denetim ve risk olay günlüğü kaynaklarına erişiminiz vardır. Bu işlemi, Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturarak ve Log Analytics çalışma alanına denetim günlüklerini ve oturum açma günlüklerini göndererek kolaylaştırabilirsiniz. İstenen uyarıları Log Analytics çalışma alanı içinde yapılandırabilirsiniz.

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

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

**Rehberlik**: Log Analytics çalışma alanları gibi hassas bilgileri depolayan veya Işleyen Azure izleyici kaynaklarını izlemeye yardımcı olması mümkün olduğunda Etiketler kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Azure İzleyici'deki günlük verilerine ve çalışma alanlarına erişimi yönetme](/azure/azure-monitor/platform/manage-access)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: ortam türü ve veri duyarlılığı düzeyi gibi bireysel güvenlik etki alanları için ayrı abonelikler ve yönetim grupları kullanarak yalıtım uygulayın. Azure Izleyicinize erişim düzeyini ve uygulamalarınızın ve kurumsal ortamlarınızın talep ettiği ilgili kaynakları kısıtlayabilirsiniz. Azure Izleyici 'ye erişimi, Azure rol tabanlı erişim denetimi (Azure RBAC) aracılığıyla denetleyebilirsiniz.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Kılavuz**: Azure izleyici varsayılan olarak TLS 1,2 ' i görüşür. Azure kaynaklarınıza bağlanan tüm istemcilerin TLS 1,2 veya üzerini anlaşamadığından emin olun. 

Application Insights ve Log Analytics her ikisi de TLS 1,1 ve TLS 1,0 verilerinin belirtilmesine izin verecek şekilde devam eder. İstemci tarafında yapılandırılarak, veriler TLS 1,2 ile kısıtlanabilir.

- [TLS 1,2 kullanarak verileri güvenli bir şekilde gönderme](/azure/azure-monitor/platform/data-security#sending-data-securely-using-tls-12)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: veri tanımlama, sınıflandırma ve kayıp önleme özellikleri henüz Azure izleyici için kullanılabilir değil. Uyumluluk amacıyla gerekliyse üçüncü taraf çözümünü uygulayın.
Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: kaynaklara erişimi denetlemek için rol tabanlı erişim denetimi kullanma

**Kılavuz**: Azure izleyici erişimini yönetmek için Azure rol tabanlı erişim denetimi 'NI (RBAC) kullanın.

- [Azure Izleyici 'de roller, izinler ve güvenlik](/azure/azure-monitor/platform/roles-permissions-security)

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Kılavuz**: Azure izleyici, tüm veri ve kaydedilmiş sorguların, Microsoft tarafından yönetilen anahtarlar (MMK) kullanılarak geri kalanında şifrelenmesini sağlar. Azure Izleyici Ayrıca, Azure Key Vault depolanan ve sistem tarafından atanan yönetilen kimlik doğrulaması kullanan depolama tarafından erişilen kendi anahtarınızı kullanarak şifreleme için bir seçenek sağlar. Bu müşteri tarafından yönetilen anahtar (CMK) yazılım ya da donanım HSM korumalı olabilir.

- [Azure Izleyici müşteri tarafından yönetilen anahtarlar](/azure/azure-monitor/platform/customer-managed-keys)

- [Log Analytics veri güvenliği](/azure/azure-monitor/platform/data-security)

- [Application Insights'da veri toplama, saklama ve depolama](app/data-retention-privacy.md)

- [Azure’da bekleyen veri şifrelemesini anlama](../security/fundamentals/encryption-atrest.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Kılavuz**: Azure izleyici ve ilgili kaynaklarda değişiklik gerçekleşirken uyarı oluşturmak Için Azure etkinlik günlüğü Ile Azure izleyici 'yi kullanın.

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](/azure/azure-monitor/platform/alerts-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Insights**:

[!INCLUDE [Resource Policy for microsoft.insights 4.9](../../includes/policy/standards/asb/rp-controls/microsoft.insights-4-9.md)]

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek için risk derecelendirme işlemi kullanın

**Rehberlik**: ortak bir risk Puanlama programını (örneğin, ortak güvenlik açığı Puanlama sistemi) veya üçüncü taraf tarama aracınız tarafından sunulan varsayılan risk derecelendirmelerini kullanın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Kılavuz**: Azure CLI kullanarak aboneliklerinizde Azure izleyici kaynaklarını sorgulama ve bulma. Kiracınızda uygun (okuma) izinlere sahip olun ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerini numaralandırın.

- [Azure Izleyici CLı](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest&amp;preserve-view=true)

- [Azure aboneliklerinizi görüntüleme](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

- [Azure Izleyici 'de roller, izinler ve güvenlik](/azure/azure-monitor/platform/roles-permissions-security)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Kılavuz**: Azure izleyici kaynaklarına Etiketler uygulayarak bunları bir taksonomi halinde mantıksal olarak organize etmek için meta veriler verirsiniz.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: Azure izleyici ile ilgili kaynakları düzenlemek ve izlemek için uygun yerlerde etiketleme, yönetim grupları ve ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynaklarının envanterini tanımlayın ve saklayın

**Rehberlik**: Kurumsal gereksinimlerinize göre işlem kaynakları Için onaylanan Azure kaynakları ve onaylanan yazılım envanterini oluşturun.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aboneliklerinizde oluşturulabilecek kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın.

Abonelikler içindeki kaynakları sorgulamak ve bulmak için Azure Kaynak Grafı'nı kullanın.  Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Rehberlik**: envanterin düzenli olarak mutabakatını yapın ve Azure izleyici ile ilgili kaynakların, aboneliğin zamanında silindiğinden emin olun.  

- [Azure Log Analytics çalışma alanını sil](/azure/azure-monitor/platform/delete-workspace)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: ortamınızda sağlayabileceğiniz Azure izleyici ile ilgili kaynakları kısıtlamak Için Azure ilkesini kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcıların Azure kaynakları yöneticisiyle etkileşime geçmesini sınırlamak Için Azure koşullu erişimi kullanın.

- [Azure Kaynak Yöneticisi 'ne erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Kılavuz**: Azure izleyici ile ilgili kaynaklarınızı denetlemek veya yapılandırmasını zorlamak Için özel Azure ilke tanımlarını kullanın. Yerleşik Azure Ilke tanımlarını kullanılabilir olarak da kullanabilirsiniz.

Ayrıca, Azure Resource Manager, yapılandırmanın kuruluşunuzun güvenlik gereksinimlerini karşıladığından/aştığından emin olmak için gözden geçirilmesi gereken JavaScript Nesne Gösterimi (JSON) içinde şablonu dışarı aktarma özelliğine sahiptir.

Azure Güvenlik Merkezi 'nin önerilerini Azure kaynaklarınız için güvenli bir yapılandırma temeli olarak da kullanabilirsiniz.

Canlı akış APM özellikleri kullanıyorsanız, izleme anahtarına ek olarak kanalı gizli bir API anahtarı ile güvenli hale getirin.

- [Güvenli APM Canlı Ölçüm Akışı](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Öğretici: uyumluluğu zorlamak için ilke oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Güvenlik önerileri - başvuru kılavuzu](../security-center/recommendations-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Kılavuz**: Azure izleyici ile ilgili kaynaklarınız genelinde güvenli ayarları zorlamak için Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] kullanın.  Ayrıca, kuruluşunuz tarafından gerekli olan Azure Izleyici ile ilgili kaynaklarınızın güvenlik yapılandırmasını sürdürmek için Azure Resource Manager şablonlarını kullanabilirsiniz.

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

- [Uyumluluğu zorunlu tutmak için ilkeleri oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager şablonlarına genel bakış](../azure-resource-manager/templates/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilkeleri ve Azure Resource Manager şablonları gibi kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure DevOps kullanın. Azure DevOps 'da yönettiğiniz kaynaklara erişmek için, Azure DevOps ile tümleşikse veya TFS ile tümleşikse Active Directory belirli kullanıcılara, yerleşik güvenlik gruplarına veya Azure Active Directory (Azure AD) tanımlanmış gruplara izin verebilir veya vermeyebilirsiniz.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps 'da izinler ve gruplar hakkında](/azure/devops/organizations/security/about-permissions)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: Azure ilkesi 'Ni kullanarak Azure izleyici ile ilgili kaynaklar için standart güvenlik yapılandırması tanımlama ve uygulama. Azure Izleyici ile ilgili kaynaklarınızın güvenlik yapılandırmasını denetlemek veya zorlamak için özel Azure Ilke tanımlarını kullanın. Ayrıca, belirli kaynaklarınızla ilgili yerleşik ilke tanımlarından da yararlanabilirsiniz.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesi diğer adları](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Kılavuz**: Azure izleyici ile ilgili kaynaklarınız için temel taramalar gerçekleştirmek üzere Azure Güvenlik Merkezi 'ni kullanın.  Ayrıca Azure Ilkesi 'ni kullanarak Azure Kaynak konfigürasyonları 'nı uyarır ve denetleyin.

- [Azure Güvenlik Merkezi 'nde öneriler nasıl düzeltileceği](../security-center/security-center-remediate-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: desteklenen Azure izleyici ile ilgili kaynaklar için gizli yönetimi basitleştirmek ve güvenli hale getirmek üzere Azure Key Vault birlikte yönetilen hizmet kimliği kullanın.

- [Azure yönetilen kimliklerle tümleştirme](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Azure kaynakları için yönetilen kimlikleri destekleyen hizmetler](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Key Vault oluşturma](../key-vault/secrets/quick-create-portal.md)

- [Yönetilen kimlik ile Key Vault kimlik doğrulaması sağlama](/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Rehberlik**: Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik ile Azure hizmetleri sağlamak için Yönetilen kimlikler kullanın. Yönetilen kimlikler, kodunuzda kimlik bilgileri olmadan Azure Izleyici kaynakları dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmanıza imkan tanır.

- [Azure kaynakları için Yönetilen kimlikler nasıl yapılandırılır](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: kod içinde kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayın. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar.

- [Kimlik bilgisi tarayıcısını ayarlama](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: işlem dışı Azure kaynaklarına yüklenecek dosyaları önceden Tara

**Rehberlik**: Microsoft 'un kötü amaçlı yazılımdan koruma özelliği, Azure hizmetlerini destekleyen temel ana bilgisayar üzerinde (örneğin, Azure izleyici ile ilgili kaynaklar) etkinleştirilir, ancak içeriğiniz üzerinde çalışmaz. 

Log Analytics çalışma alanı gibi uygun Azure Izleyici ilgili kaynaklarına yüklenen tüm dosyaları önceden tarayın.

Depolama hesaplarına yüklenen kötü amaçlı yazılımları algılamak için veri Hizmetleri için Azure Güvenlik Merkezi 'nin tehdit algılamasını kullanın. 

- [Azure Cloud Services ve sanal makineler için Microsoft kötü amaçlı yazılımdan koruma 'yı anlayın](../security/fundamentals/antimalware.md)

- [Azure Güvenlik Merkezi 'nin veri Hizmetleri tehdit algılamasını anlama](../security-center/azure-defender.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli otomatik arka pencerelere emin olun

**Kılavuz**: Azure izleyici ve ilgili kaynakları Azure izleyici ve ilgili yapılandırma için yedekleme olarak kullanılabilecek bir JAVASCRIPT nesne GÖSTERIMI (JSON) şablonunda dışarı aktarmak için Azure Resource Manager kullanın.  Yedekleme betiklerini otomatik olarak çalıştırmak için Azure Otomasyonu 'nu kullanın. 

- [Azure Resource Manager şablonları kullanarak Log Analytics çalışma alanını yönetme](/azure/azure-monitor/samples/resource-manager-workspace)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Otomasyonu hakkında](../automation/automation-intro.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Kılavuz**: Azure izleyici ve ilgili kaynakları Azure izleyici ve ilgili yapılandırma için yedekleme olarak kullanılabilecek bir JAVASCRIPT nesne GÖSTERIMI (JSON) şablonunda dışarı aktarmak için Azure Resource Manager kullanın. Azure Izleyici ile ilgili kaynaklar müşteri tarafından yönetilen anahtarlar kullanıyorsa, müşteri tarafından yönetilen anahtarları Azure Key Vault içinde yedekleyin.

- [Azure Resource Manager şablonları kullanarak Log Analytics çalışma alanını yönetme](/azure/azure-monitor/platform/template-workspace-configuration)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Azure 'da Anahtar Kasası anahtarlarını yedekleme](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: Azure Resource Manager desteklenen şablon dosyalarını kullanarak düzenli aralıklarla geri yükleme gerçekleştirme olanağı sağlayın. Yedeklenen müşteri tarafından yönetilen anahtarların test geri yüklemesi.

- [Azure Resource Manager şablonları kullanarak Log Analytics çalışma alanını yönetme](/azure/azure-monitor/samples/resource-manager-workspace)

- [Azure 'da Anahtar Kasası anahtarlarını geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Kılavuz**: özel Azure ilkeleri, Azure Resource Manager şablonları gibi kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure DevOps kullanın. Azure DevOps 'da yönettiğiniz kaynakları korumak için, belirli kullanıcılara, yerleşik güvenlik gruplarına veya Azure DevOps ile tümleşikse veya TFS ile tümleşikse Active Directory Azure Active Directory (Azure AD) içinde tanımlanan gruplara izin verebilir veya vermeyebilirsiniz.  Müşteri tarafından yönetilen anahtarları korumak için Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanın.

Ayrıca, anahtarları yanlışlıkla veya kötü amaçlı silmeye karşı korumak için Key Vault Soft-Delete ve Temizleme korumasını etkinleştirin. Azure Resource Manager şablon yedeklemelerini depolamak için Azure depolama kullanılıyorsa, Bloblar veya blob anlık görüntüleri silindiğinde verilerinizi kaydetmek ve kurtarmak için geçici silme özelliğini etkinleştirin.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps 'da izinler ve gruplar hakkında](/azure/devops/organizations/security/about-permissions)

- [Key Vault 'de Soft-Delete ve Temizleme korumasını etkinleştirme](../storage/blobs/soft-delete-blob-overview.md)

- [Azure Depolama blobları için geçici silme](../storage/blobs/soft-delete-blob-overview.md)

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

**Rehberlik**: Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizte ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır.

Ayrıca, abonelikleri açıkça işaretleyin (örn. üretim, üretim dışı) etiketleri kullanarak Azure kaynaklarını açıkça tanımlamak ve kategorilere ayırmak için özellikle de hassas verileri işleyen bir adlandırma sistemi oluşturun.  Azure kaynaklarının önem düzeyine ve olayın oluştuğu ortama bağlı olarak uyarıların çözümünde önceliği belirlemek sizin sorumluluğunuzdadır.

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

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
