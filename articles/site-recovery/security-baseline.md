---
title: Site Recovery için Azure Güvenlik temeli
description: Site Recovery güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 429bb1ffdf40ed9906082e00d4ffd1156a4e5e0b
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967855"
---
# <a name="azure-security-baseline-for-site-recovery"></a>Site Recovery için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürüm 1,0](../security/benchmarks/overview-v1.md) ' den Sıterecovery 'a kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar. İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Sıterecovery için geçerli olan ilgili kılavuza göre gruplandırılır. **Denetimler** , SiteRecovery için geçerli değildir veya sorumluluğun Microsoft 'un ne olduğu, hariç tutulur.

Sıterecovery 'ın Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam sıterecoçok güvenlik taban çizgisi eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: Microsoft Azure Site Recovery Azure sanal ağına dağıtımı desteklemez. Ağınız üzerinde güvenli iletişim sağlamak için Azure özel uç noktasıyla Site Recovery hizmeti yapılandırın.

- [Azure Site Recovery özel bağlantı desteği](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Rehberlik**: Site Recovery hizmeti, müşterilerin yalnızca belirli hizmetlere ve bağlantı noktalarına trafik açmasına olanak tanıyan hizmet etiketlerini destekler. Müşteriler, Site Recovery hizmetine giden erişime izin vermek için güvenlik duvarı veya ağ güvenlik grubu üzerinde "Azuresterecovery" hizmet etiketine izin versin.

- [Hizmet etiketlerini kullanarak giden bağlantı](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-using-service-tags)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: ağ güvenlik grupları ve ağ güvenliği ve trafik akışıyla ilgili diğer kaynaklar için kaynak etiketleri kullanın. Tek tek ağ güvenlik grubu kuralları için "Açıklama" alanını kullanarak bir ağa giden ve ağdan gelen trafiğe izin veren kuralları belgeleyin. 

Tüm kaynakların etiketlerle oluşturulmasını ve mevcut etiketlenmemiş kaynakları bilgilendirmesini sağlamak için etiketlemeyle ilgili yerleşik Azure Ilke tanımlarından herhangi birini ("etiket ve onun değeri gerektir" gibi) ekleyin. 

Azure PowerShell veya Azure CLı kullanarak, etiketlerine göre kaynakları arayabilir veya bunlarla ilgili eylemler gerçekleştirebilirsiniz. 

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Azure sanal ağı oluşturma](../virtual-network/quick-create-portal.md) 

- [Ağ güvenlik grubu kuralları ile ağ trafiğini filtreleme](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Kılavuz**: Azure etkinlik günlüklerini kullanarak Site Recovery hizmetiyle ilgili ağ kaynak yapılandırmalarında yapılan tüm değişiklikleri izleyin. Kritik Site Recovery ağ kaynakları değiştirildiğinde sizi bilgilendirmek için Azure Izleyici 'de uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Izleyici 'yi kullanarak etkinlik günlüğü uyarıları oluşturma, görüntüleme ve yönetme](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: denetim günlüğü Için Azure etkinlik günlüğü tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına, Azure depolama hesabına veya arşivleme Için bir Azure Olay Hub 'ına gönderin.

Azure kaynaklarınız üzerinde herhangi bir yazma işlemi (PUT, POST, SILME) için "ne, kim ve ne zaman" i belirlemek için Azure etkinlik günlüğü verilerini kullanın.

Oluşturulan güvenlik verilerini toplamak için Azure Izleyici 'de günlüğe alma Site Recovery. Azure Izleyici 'de, Log Analytics çalışma alanlarını kullanarak analizleri sorgulama ve gerçekleştirme ve uzun süreli veya arşiv depolama için depolama hesapları kullanma. Ayrıca, Azure Sentinel 'e veya bir üçüncü taraf güvenlik olayı ve olay yönetimi (SıEM) çözümüne ve bu verileri etkinleştirebilir.

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](../azure-monitor/essentials/activity-log.md)

- [Azure İzleyici Günlükleriyle Site Recovery’yi izleme](monitor-log-analytics.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: denetim günlüğü Için Azure etkinlik günlüğü tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına, Azure depolama hesabına veya arşivleme Için bir Azure Olay Hub 'ına gönderin. 

Azure kaynaklarınız üzerinde herhangi bir yazma işlemi (PUT, POST, SILME) için "ne, kim ve ne zaman" i belirlemek için Azure etkinlik günlüğü verilerini kullanın.

Oluşturulan güvenlik verilerini toplamak için Azure Izleyici ile günlüğe alma Site Recovery. Azure Izleyici 'de, Log Analytics çalışma alanlarını kullanarak analiz sorgulama ve bunları gerçekleştirme ve uzun süreli/arşiv depolama için depolama hesapları kullanma. Azure Sentinel veya bir üçüncü taraf güvenlik olayı ve olay yönetimi (SıEM) çözümüne ve yerleşik verileri etkinleştirin.

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](../azure-monitor/essentials/activity-log.md)

- [Azure İzleyici Günlükleriyle Site Recovery’yi izleme](monitor-log-analytics.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Rehberlik**: kuruluşunuzun uyumluluk düzenlemelerine göre Azure izleyici 'Yi kullanarak Azure kurtarma hizmetleri kasalarıyla ilişkili Log Analytics çalışma alanları için günlük tutma süresini ayarlayın. 

- [Günlük tutma parametrelerini ayarlama](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Kılavuz**: Azure etkinlik günlüğü tanılama ayarlarını etkinleştirin ve günlükleri bir Log Analytics çalışma alanına gönderin. 

Şartlar aramak, eğilimleri belirlemek, desenleri belirlemek ve kurtarma hizmetleri kasalarından toplanan etkinlik günlüğü verileri hakkındaki öngörüleri belirlemek için Log Analytics sorguları gerçekleştirin.

- [Site Recovery’yi izleme](site-recovery-monitor-and-troubleshoot.md)

- [Azure etkinlik günlüğü için tanılama ayarlarını etkinleştirme](../azure-monitor/essentials/activity-log.md)

- [Azure Izleyici 'de Log Analytics çalışma alanında Azure etkinlik günlüklerini toplama ve çözümleme](../azure-monitor/essentials/activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Rehberlik**: Azure izleyici günlükleri ve Log Analytics kullanarak Azure Site Recovery tarafından çoğaltılan makineleri izleyin. Günlük sorgularını yazmak ve test etmek ve günlük verilerini etkileşimli olarak çözümlemek için Azure Izleyici içindeki Log Analytics kullanın. Azure Izleyici, diğer izleme verileriyle birlikte etkinlik ve kaynak günlüklerini toplar. 

Günlük sonuçlarını görselleştirin ve sorgulayın ve izlenen verilere göre eylemleri gerçekleştirmek için uyarıları yapılandırın. Bir Log Analytics çalışma alanındaki uyarıları Azure Sentinel 'e ayarlayarak bir güvenlik Orchestration otomatik yanıtı (SOAR) çözümü sağlar. Bu, PlayBook 'lar gibi güvenlik sorunlarını gidermek için otomatik çözümlerin oluşturulmasını ve kullanılmasını sağlar. Azure Izleyici 'yi kullanarak Log Analytics çalışma alanınızda özel günlük uyarıları oluşturun. 

- [Site Recovery’yi izleme](site-recovery-monitor-and-troubleshoot.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Izleyici 'yi kullanarak günlük uyarıları oluşturma, görüntüleme ve yönetme](../azure-monitor/alerts/alerts-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: varsayılan olarak hiçbir rol atanmaz. İş ihtiyaçları temelinde açıkça atanması gerekir. Tüm rol atamaları, yönetim gruplarının üyesi olan hesapları saptamak için PowerShell CLı veya Azure Active Directory (Azure AD) ile denetlenebilir.

- [Azure AD 'de PowerShell ile dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturun. Yönetim hesaplarının sayısını izlemek için Güvenlik Merkezi 'nin kimlik ve erişim yönetimi özelliklerini kullanın.

Ayrıca, özel yönetim hesaplarını izlemenize yardımcı olmak için, güvenlik merkezi 'nden veya yerleşik Azure ilkelerinden öneriler kullanın, örneğin: 

- Aboneliğinize birden fazla sahip atanmalıdır 
- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır 

- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır

Yönetim hesapları için kimlik ve erişim denetimini izlemek ve düzenli aralıklarla gözden geçirmek için bir işlem oluşturun.

- [Azure Güvenlik Merkezi 'ni kullanarak kimlik ve erişimi izleme](../security-center/security-center-identity-access.md)

- [Azure Ilkesini kullanma](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Azure Active Directory ile çoklu oturum açma (SSO) kullanın

**Rehberlik**: API çağrıları aracılığıyla kurtarma hizmetleri kasalarınızla etkileşim kurmak için kullanılacak bir belirteç almak üzere bir hizmet sorumlusu ile Azure uygulama kaydı 'nı kullanın.

- [Azure REST API 'Lerini çağırma](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [İstemci uygulamanızı (hizmet sorumlusu) Azure Active Directory (Azure AD) ile kaydetme](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Kurtarma Hizmetleri API bilgileri](/rest/api/recoveryservices)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory Multi-Factor Authentication etkinleştirin ve Güvenlik Merkezi 'nin kimlik ve erişim önerilerini izleyin.

- [Azure Active Directory Multi-Factor Authentication dağıtımını planlayın](../active-directory/authentication/howto-mfa-getstarted.md)

- [Kimlik ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: tüm yönetim görevleri için adanmış makineler (ayrıcalıklı erişim Iş Istasyonları) kullanın

**Rehberlik**: yönetim görevleri için Azure Active Directory Multi-Factor Authentication ve Site Recovery kaynaklarında ayrıcalıklı eylemler gerçekleştirmek için güvenli, Azure tarafından yönetilen bir iş istasyonu (bir ayrıcalıklı erişim iş istasyonu (Paw) olarak da bilinir) kullanın. 

- [Ayrıcalıklı Erişim İş İstasyonları](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Bulut tabanlı Azure Active Directory Multi-Factor Authentication dağıtımını planlama](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: ortamda şüpheli veya güvenli olmayan bir etkinlik oluştuğunda günlükler ve uyarılar oluşturmak için Azure Active Directory (Azure AD) PRIVILEGED IDENTITY Management (PIM) özelliğini kullanın.

Azure AD risk algılama özelliği sayesinde riskli Kullanıcı davranışında uyarıları ve raporları görüntüleyin.

- [Privileged Identity Management dağıtma (PıM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD risk algılamalarını anlama](../active-directory/identity-protection/overview-identity-protection.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: IP adresi aralıklarının, bölgelerin veya ülkelerin yalnızca belirli mantıksal gruplarından Azure Portal erişimine izin vermek Için, koşullu erişim adlı konum kullanın.
- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: Site Recovery için merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen veriler için güçlü şifreleme kullanarak aktarım ve ayrıca salts, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolayan verileri korur.

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: eski hesapların keşfedilmesine yardımcı olmak için Azure Active Directory (Azure AD) günlükleri kullanın.

Azure AD 'nin kimlik ve erişim gözden geçirmeleri ile grup üyeliklerini verimli bir şekilde yönetin, kurumsal uygulamalara ve rol atamalarına erişin.

Yalnızca tamamlanan erişim gözden geçirmeleri olan kullanıcıların erişmeye devam ettiğinden emin olmak için Kullanıcı erişimini düzenli olarak gözden geçirmek üzere bir işlem oluşturun.

- [Azure AD raporlamayı anlama](/azure/active-directory/reports-monitoring/)

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: Site Recovery kaynakları için merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifreleme kullanarak verileri korur ve ayrıca, salları, karmaları ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.

Azure AD oturum açma etkinliğine, denetim ve risk olay günlüğü kaynaklarına erişerek Azure Sentinel veya Azure Marketi 'nde bulunan herhangi bir SıEM veya izleme aracı ile tümleştirmenize olanak tanır.

Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturup Log Analytics çalışma alanına denetim ve oturum açma günlükleri göndererek bu süreci daha da kolaylaştırın. İstenen uyarıları bir Log Analytics çalışma alanında yapılandırabilirsiniz.

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Yerleşik Azure Sentinel](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapmasından uyar

**Rehberlik**: kurtarma hizmetleri kasaları için merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın.

Hesap oturum açma davranışı algılaması ve Kullanıcı kimlikleriyle ilgili olarak otomatik yanıtları algılanan şüpheli eylemlere göre yapılandırmak için Azure AD 'nin kimlik koruması özelliklerini kullanmayı sağlar. Ayrıca, daha fazla araştırma için Azure Sentinel 'e veri alma.

- [Azure AD riskli oturum açma nasıl görüntülenir](../active-directory/identity-protection/overview-identity-protection.md)

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

**Rehberlik**: geliştirme, test ve üretim kurtarma hizmetleri kasaları için ayrı abonelikler veya yönetim grupları uygulayın. Bir sanal ağ veya alt ağ ile, uygun şekilde etiketlenmiş ve bir ağ güvenlik grubu veya Azure Güvenlik Duvarı tarafından güvenli hale getirilmiş kaynakları ayırın. 

Kullanımda olmadığında hassas verileri depolayan veya işleyen sanal makineleri kapatın. Bu yinelenen bir işlem yapmak için ilke ve yordamları uygulayın. 

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Site Recovery genel bakış](site-recovery-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: Site Recovery etkinleştirilmiş sanal makinelerden veri taşalımı fırsatlarını azaltmak Için özel bağlantı veya özel uç nokta, ağ güvenlik grupları ve hizmet etiketleri kullanın.

Microsoft, Site Recovery tarafından kullanılan temel platformu yönetir ve tüm müşteri içeriklerini, müşteri veri kaybına ve pozlamaya karşı hassas ve koruma olarak değerlendirir. Microsoft, Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için bir dizi güçlü veri koruma denetimi ve özelliği kullanır. 

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

- [Azure özel uç noktaları ile sanal makineleri çoğaltma](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [Azure Site Recovery hizmeti etiketleriyle sanal makineleri çoğaltma](azure-to-azure-about-networking.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Rehberlik**: Site Recovery, Azure iş yükü sunucularından bir kurtarma hizmetleri Kasası arkasında barındırılan Site Recovery hizmetleri için GELIŞMIŞ ŞIFRELEME standardı (AES 256) kullanılarak şifrelenmiş güvenli bir HTTPS kanalı kullanır.

Site Recovery için desteklenen geçerli TLS sürümleri, 2019 sonuna kadar dinamik olarak bulunan bölgelerde TLS 1,0, TLS 1,1, TLS 1,2. TLS 1.2, tüm yeni bölgeler için desteklenen tek TLS sürümüdür.

- [Azure Site Recovery için geçiş sırasında şifrelemeyi anlama](physical-azure-set-up-source.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: veri tanımlama, sınıflandırma ve kayıp önleme özellikleri henüz Site Recovery için kullanılabilir değil. 

Uyumluluk amaçlarıyla, gerektiğinde bir üçüncü taraf çözümü uygulayın.

Microsoft, Site Recovery tarafından kullanılan temel platformu yönetir ve tüm müşteri içeriklerini müşteri veri kaybına ve pozlamaya karşı hassas ve korumalara göre değerlendirir. Bu, Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için bir dizi güçlü veri koruma denetimi ve özelliği kullanır. 

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: kaynaklara erişimi yönetmek için Azure RBAC kullanma

**Rehberlik**: Site Recovery kaynaklarla ilgili verilere ve kaynaklara erişimi yönetmek için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanın. 

İş görevlerini Azure RBAC ile ayırın ve bunlar için gereken uygun erişimi verin. Site Recovery yönetim işlemlerini denetlemek için yerleşik Site Recovery rollerini kullanın.

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md)

- [Azure Site Recovery yönetmek için Role-Based Access Control kullanın](site-recovery-role-based-linked-access-control.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Rehberlik**: hem platform hem de müşteri tarafından yönetilen anahtarlarla çift şifrelemeyi etkinleştirin. Bu özellik Site Recovery kullanılabilir. 

Site Recovery, verilerin geri kalanında şifrelemeyi destekler. Azure IaaS iş yükleri için veriler Depolama Hizmeti Şifrelemesi (SSE) kullanılarak şifrelenir. 

Müşteri tarafından yönetilen bir anahtarla şifrelenen bir kurtarma hizmetleri Kasası kullanılırken yalnızca müşterinin şifreleme anahtarına erişimi vardır. Microsoft hiçbir zaman bir kopya korumaz, anahtara erişemez ve birincil bilgisayardan olağanüstü durum kurtarma konumuna aktarılan verilerin şifresini çözer. 

- [Azure Site Recovery için müşteri tarafından yönetilen anahtarlar desteği](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Rehberlik**: kritik kaynaklarda değişiklik yapıldığında uyarı oluşturmak Için Azure izleyici Ile Azure etkinlik günlüklerini kullanın. Bu kaynaklara kurtarma hizmetleri kasalarının üretim örnekleri, Site Recovery hizmeti kaynakları ve ilgili kaynaklar dahil olabilir.
- [Azure etkinlik günlüğü olayları için uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: abonelikleriniz dahilinde kurtarma hizmetleri kasaları dahil tüm kaynakları sorgulamak veya öğrenmek Için Azure Kaynak grafiğini kullanın. Kiracınızda uygun okuma izinlerinin olduğundan emin olun ve aboneliklerinizde kaynakların yanı sıra tüm Azure aboneliklerini de numaralandırın.

Klasik Azure kaynakları kaynak Graph aracılığıyla bulunabilse de, ileri doğru Azure Resource Manager kaynak oluşturmanız ve kullanılması kesinlikle önerilir.

- [Azure Graph ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Rehberlik**: kurtarma hizmetleri kasalarına ve diğer ilgili kaynaklara Etiketler uygulayarak, meta verilerle Site Recovery tarafından kullanılan ve mantıksal olarak bunları bir taksonomi halinde organize edin.
- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: Site Recovery (Kurtarma Hizmetleri kasalarını) ve diğer ilgili kaynakları düzenlemek ve izlemek için uygun yerlerde etiketleme, yönetim grupları ve ayrı abonelikler kullanın. 

Ayrıca, aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak için Azure Ilkesini kullanın: 

- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynakları envanterini tanımlama ve sürdürme

**Rehberlik**: müşterinin kurumsal gereksinimlerine göre işlem kaynakları Için onaylanan Azure kaynakları ve onaylanan yazılım envanterini oluşturun.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın: 

- İzin verilmeyen kaynak türleri 
- İzin verilen kaynak türleri

Azure Kaynak Grafiği 'ni kullanarak aboneliklerdeki kaynakları sorgulama ve bulma.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:

- İzin verilmeyen kaynak türleri 
- İzin verilen kaynak türleri

Kurumsal standartlarınıza ve hizmet düzeyi sözleşmelerinize uyumluluğu korumak için Azure'da ilkelerin nasıl oluşturulduğunu ve yönetildiğini anlamak önemlidir.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](/azure/governance/policy/samples)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak Için Azure koşullu erişimi kullanın. Bu, yüksek bir güvenlik ortamındaki kaynaklarda oluşturma ve değişiklik yapılmasını önleyebilir.

- [Azure Resource Manager erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Kılavuz**: Azure Ilkesiyle kurtarma hizmetleri kasanızda standart güvenlik yapılandırması tanımlayın ve uygulayın. 

Denetim için özel ilkeler oluşturmak veya Site Recovery hizmetinin kurtarma hizmetleri Kasası kaynaklarını yapılandırmayı zorlamak için "Microsoft. RecoveryServices" ad alanındaki Azure Ilke diğer adlarını kullanın.
- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Rehberlik**: Azure kaynaklarınız genelinde güvenli ayarları zorlamak Için Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] etkileri kullanın.
- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Rehberlik**: kurtarma hizmetleri kasaları ve ilgili kaynaklarınız Için özel Azure ilke tanımları kullanıyorsanız kodunuzu güvenli bir şekilde depolamak ve yönetmek için Azure Repos seçin.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow)

- [Azure Repos belgeleri](/azure/devops/repos/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: yerleşik Azure ilke tanımlarını ve sistem yapılandırmalarının uyarı vermesi, denetlemesi ve uygulanması için özel ilkeler oluşturmak üzere "Microsoft. recoveryservices" ad alanındaki Azure ilkesi diğer adları 'nı kullanın. 

Ayrıca, ilke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: yerleşik Azure ilke tanımlarını ve sistem yapılandırmalarının uyarı vermesi, denetlemesi ve uygulanması için özel ilkeler oluşturmak üzere "Microsoft. recoveryservices" ad alanındaki Azure ilkesi diğer adları 'nı kullanın. 

Azure kaynaklarınızın yapılandırmasını otomatik olarak zorlamak için [Denetim], [reddetme] ve [dağıtım yok] etkileri ' nı kullanın.
- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: müşterinin Azure Anahtar Kasası ile tümleştirilmiş Site Recovery gizli dizileri yönetmesi gerekir ve Azure disk şifrelemesi etkinleştirilmiş sanal makineler Için olağanüstü durum kurtarmayı etkinleştirir. 

- [Anahtar Kasası oluşturma](../key-vault/general/quick-create-portal.md)

- [Anahtar kasasında kimlik doğrulaması yapma](../key-vault/general/authentication.md)

- [Anahtar Kasası erişim ilkesi atama](../key-vault/general/assign-access-policy-portal.md)

- [Site Recovery kullanarak Azure disk şifrelemesi etkinleştirilmiş sanal makineler için DR 'yi etkinleştirme](azure-to-azure-how-to-enable-replication-ade-vms.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Rehberlik**: Site Recovery, yalnızca bir müşterinin kurtarma hizmetleri kasasında sistem tarafından yönetilen kimliği etkinleştirebileceği sistem tarafından yönetilen kimliği destekler. Aynı metodoloji, erişim sınırını tanımlamak için olağanüstü durum kurtarma teklifinde kullanılan kaynaklar için geçerlidir.

Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik ile Azure hizmetleri sağlamak için Yönetilen kimlikler kullanın.

Yönetilen kimlikler, kodunuzda kimlik bilgileri olmadan Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmanıza olanak sağlar.

- [Azure yönetilen kimliklerle tümleştirme](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity?tabs=core2x)

- [Kurtarma Hizmetleri kasasında System-Managed kimliğini etkinleştirme](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-private-endpoints#enable-the-managed-identity-for-the-vault)

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

**Rehberlik**: Microsoft kötü amaçlı yazılımdan koruma, Azure hizmetlerini destekleyen temel alınan konakta (örneğin, Site Recovery) etkinleştirilir, ancak içeriğiniz üzerinde çalışmaz. App Service, Data Lake Storage ve BLOB depolama gibi işlem dışı Azure kaynaklarına yüklenen tüm dosyaları önceden tarayın.

Depolama hesaplarına yüklenen kötü amaçlı yazılımları algılamak için, veri Hizmetleri için Güvenlik Merkezi 'nin tehdit algılamasını kullanın.

- [Azure Cloud Services ve sanal makineler için Microsoft Antimalware 'i anlayın](../security/fundamentals/antimalware.md)

- [Azure Güvenlik Merkezi 'nin veri Hizmetleri tehdit algılamasını anlama](../security-center/azure-defender.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: Site Recovery, olağanüstü durum kurtarma çözümünün iş yüklerindeki müşteriler tarafından yapılandırıldığı şekilde durumunu korumak Için bir Azure depolama hesabı kullanır.

Site Recovery Services meta verileri tarafından şu tür yapılandırmasıyla kullanılan tüm depolama kaynakları: Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS). GRS üzerindeki türündeki depolama hesapları (kgrs, RAG-ZRS gibi), kesintiler sırasında müşterilere olağanüstü durum kurtarma işlemine devam etmek için verilerinizi ikincil bir bölgeye (kaynak verilerin birincil konumundan yüzlerce mil) çoğaltın.

Bu, müşteri kapsamında değildir ve Site Recovery ekibi tarafından dahili olarak ele alınır. Müşteri, Azure 'da Key Vault anahtarlarını yedekleyebilir.

- [Azure 'da Anahtar Kasası anahtarlarını yedekleme](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. RecoveryServices**:

[!INCLUDE [Resource Policy for Microsoft.RecoveryServices 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.recoveryservices-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: yedeklenen müşteri tarafından yönetilen anahtarların düzenli olarak test geri yüklemeleri.

- [Azure 'da Anahtar Kasası anahtarlarını geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: veriler, Azure 'un hizmet olarak altyapı (IaaS) tabanlı sanal makineler ile depolama HIZMETI ŞIFRELEMESI (SSE) kullanılarak şifrelenir. Anahtarları yanlışlıkla veya kötü amaçlı silmeye karşı korumak için Key Vault geçici silme özelliğini etkinleştirin.

- [Key Vault 'da geçici silme özelliğini etkinleştirme](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: Kuruluşunuz için bir olay yanıt kılavuzu oluşturun. 

Tüm personel rollerinin yanı sıra olay işleme veya yönetim aşamasından olay sonrası gözden geçirme aşamalarını tanımlayan, yazılı olay yanıt planları bulunduğundan emin olun.

- [Azure Güvenlik Merkezi 'nde Iş akışı otomasyonlarını yapılandırma](../security-center/security-center-planning-and-operations-guide.md)

- [Kendi güvenlik olay yanıtı işleminizi oluşturma kılavuzu](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft Güvenlik Yanıt Merkezi 'nin bir olayın anatomisi](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Müşteri, kendi olay yanıt planının oluşturulmasına yardımcı olması için NıST 'nin bilgisayar güvenliği olay Işleme kılavuzunu da kullanabilir](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Rehberlik**: Güvenlik Merkezi 'nin atanan uyarısı-önem derecesine göre hangi uyarıların araştırılması gerektiğini önceliklendirin. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizte ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır.

Abonelikleri net bir şekilde (örneğin, üretim, üretim dışı) işaretleyin ve Azure kaynaklarını net bir şekilde tanımlamak ve kategorilere ayırmak için bir adlandırma sistemi oluşturun.

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md) 

- [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve boşlukları belirler ve planı gerektiği şekilde gözden geçirin

- [BT planları ve özellikleri için bkz. NıST 'nin yayın Kılavuzu-test, eğitim ve alıştırma programları](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: Microsoft Güvenlik Yanıt MERKEZI (MSRC), müşterinin verilerine izinsiz veya yetkisiz bir taraf tarafından erişildiğini belirlerse, Microsoft tarafından sizinle iletişim kurmak için güvenlik olayı iletişim bilgileri kullanılacaktır. 

Sorunların çözümlendiğinden emin olmak için olayları gözden geçirmek için bir işlem oluşturun, oluşumu gönderin.

- [Azure Güvenlik Merkezi güvenlik Ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: güvenlik uyarılarını olay yanıt sisteminizle birleştirme

**Rehberlik**: sürekli dışa aktarma özelliğini kullanarak güvenlik merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Sürekli dışa aktarma, uyarıları ve önerileri el ile veya devam eden sürekli bir biçimde dışa aktarmanız sağlar. 

Gerektiğinde uyarıları Azure Sentinel 'e akışa almak için Güvenlik Merkezi veri bağlayıcısını kullanın.
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
