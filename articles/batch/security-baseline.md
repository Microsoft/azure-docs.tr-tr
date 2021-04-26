---
title: Batch için Azure Güvenlik temeli
description: Batch güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: batch
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d8ef3efcda55868abec188c10ef904ae40a56722
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101728153"
---
# <a name="azure-security-baseline-for-batch"></a>Batch için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 1,0](../security/benchmarks/overview-v1.md) ' dan Batch 'e kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar.
İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve toplu iş için geçerli olan ilgili kılavuza göre gruplandırılır. Toplu Iş için geçerli olmayan **denetimler** dışlandı.

 
Toplu Işin Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Batch güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: bir sanal ağ içinde Azure Batch havuzu dağıtın. Havuz işlem düğümlerinin diğer sanal makinelerle veya şirket içi bir ağla güvenli bir şekilde iletişim kurmasına izin vermek için, havuzu bir Azure sanal ağının alt ağında sağlayabilirsiniz. Ayrıca, havuzunuzu bir sanal ağ içinde dağıtmak, tek tek düğümlerin ağ arabirimlerinin (NIC) ve alt ağın güvenliğini sağlamak için kullanılan ağ güvenlik grubu (NSG) üzerinde denetim sağlamanıza olanak tanır. NSG 'yi Internet 'teki yalnızca güvenilir IP (ler) e-konumlarından trafiğe izin verecek şekilde yapılandırın.

Uygun olduğunda, özel bir uç nokta aracılığıyla Azure Batch hesabına bağlanmak için Azure özel bağlantısı ' nı kullanarak genel ağ erişimini devre dışı bırakın. Azure özel bağlantı hizmeti güvenli hale getirilir ve yalnızca kimliği doğrulanmış ve yetkilendirilmiş özel uç noktalardan gelen bağlantıları kabul eder. Ayrıca, bir Batch havuzundaki işlem düğümleri için genel kullanıma açık RDP/SSH uç noktalarını devre dışı bırakarak bağlantıyı ve bulunabilirliği sınırlayabilirsiniz.

- [Sanal ağ içinde Azure Batch havuzu oluşturma](batch-virtual-network.md)

- [Ağ erişimi devre dışı olan bir Azure Batch hesabı oluşturma](private-connectivity.md)

- [Özel uç nokta oluşturma](../private-link/create-private-endpoint-portal.md)

- [RDP/SSH trafiğine erişimi reddetme](pool-endpoint-configuration.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: sanal ağların, alt ağların ve ağ arabirimlerinin yapılandırma ve trafiğini izleme ve günlüğe kaydetme

**Kılavuz**: Azure Güvenlik Merkezi 'ni kullanın ve Batch havuzunuzun ilişkili sanal ağ/ağ güvenlik grubu (NSG) ile ilgili ağ koruması önerilerini düzeltin. Batch havuzunu korumak için kullanılan NSG 'de akış günlüklerini etkinleştirin ve trafik denetimi için günlükleri Azure depolama hesabına gönderin. Ayrıca Azure Log Analytics çalışma alanına NSG akış günlükleri gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Azure Trafik Analizi kullanabilirsiniz. Azure Trafik Analizi 'nin bazı avantajları, ağ etkinliğini görselleştirme ve etkin noktaları tanımlayabilir, güvenlik tehditlerini belirleyebilir, trafik akışı düzenlerini anlayabilir ve ağ ile hatalı yapılandırmaların nasıl belirlenmesine olanak sağlar.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Rehberlik**: DDoS saldırılarına karşı koruma için Azure Batch havuzunuzu koruyan sanal ağ üzerinde Azure DDoS (dağıtılmış hizmet reddi) standart korumasını etkinleştirin. Bilinen kötü amaçlı veya kullanılmayan Internet IP adresleriyle iletişimleri reddetmek için Azure Güvenlik Merkezi tümleşik tehdit zekasını kullanın.

- [DDoS korumasını yapılandırma](../ddos-protection/manage-ddos-protection.md)

- [Azure Güvenlik Merkezi tümleşik tehdit zekasını anlama](../security-center/azure-defender.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Rehberlik**: Azure Batch havuzunuzu korumak için kullanılan ağ güvenlik grubu 'NDA (NSG) akış günlüklerini etkinleştirin ve trafik denetimi için günlükleri bir Azure depolama hesabına gönderin.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: uyumluluk amaçları için gerekliyse, Azure Marketi 'nden, yük İnceleme özelliklerine sahip yetkisiz giriş algılama sıstemlerı (kimlikler) ve yetkisiz erişim önleme sıstemlerı (IP) işlevselliğini destekleyen bir ağ sanal gereci seçin.

Yük incelemesini temel alan yetkisiz giriş algılama ve/veya önleme bir gereksinim değilse, tehdit zekasıyla Azure Güvenlik Duvarı kullanılabilir. Azure Güvenlik Duvarı tehdit zekası tabanlı filtreleme, bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen ve giden trafiği uyarabilir ve reddedebilir. IP adresleri ve etki alanları, Microsoft Tehdit Analizi akışından alınır.

Azure Güvenlik duvarını, Azure Batch havuz düğümleriyle aynı sanal ağdaki genel IP adresiyle dağıtın. Ağ adresi çevirisi (NAT) kurallarını Internet 'teki güvenilen konumlar ve ayrı havuz düğümlerinizin özel IP adresleri arasında yapılandırın. Azure Güvenlik duvarında, tehdit zekasından, bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen/giden trafiği uyarma ve engellemeye engel olmak için "uyarı ve reddetme" seçeneğini yapılandırın. IP adresleri ve etki alanları Microsoft Threat Intelligence akışından kaynaklıdır ve yalnızca en yüksek güvenirlik kayıtları dahildir. 

- [Sanal ağ içinde Azure Batch havuzu oluşturma](batch-virtual-network.md)

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Market](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Rehberlik**: ağ güvenlik grupları veya Azure Batch havuzlarınız Ile Ilişkili Azure Güvenlik duvarları üzerinde ağ erişim denetimleri tanımlamak için sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adı (örn., Apimanaya) belirterek, ilgili hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

- [Hizmet etiketlerini anlama ve kullanma](../virtual-network/service-tags-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Rehberlik**: Azure ilkesiyle Azure Batch havuzlarınızla ilişkili ağ kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın. Azure Batch havuzlarınızın ağ yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft.Batch" ve "Microsoft. Network" ad alanlarında Azure Ilke diğer adlarını kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: ağ hizmet grupları (NSG 'ler) ve Azure Batch havuzlarınızla ilişkili ağ güvenliği ve trafik akışıyla ilgili diğer kaynaklar için Etiketler kullanın. Bireysel NSG kuralları için "Açıklama" alanını kullanarak ağa giden/giden trafiğe izin veren kuralların iş gereksinimini ve/veya süresini (vs.) belirtin.

Tüm kaynakların etiketlerle oluşturulmasını ve mevcut etiketlenmemiş kaynakları bilgilendirmesini sağlamak için etiketlemeyle ilgili yerleşik Azure Ilke tanımlarından herhangi birini ("etiket ve onun değeri gerektir" gibi) kullanın.

Azure PowerShell veya Azure CLı kullanarak, etiketlerine göre kaynaklar üzerinde arama yapabilir veya eylemler gerçekleştirebilirsiniz.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Kılavuz**: Azure etkinlik günlüğü 'nü kullanarak ağ kaynak yapılandırmasını izleyin ve Azure Batch havuzlarınızla ilgili ağ kaynaklarına yönelik değişiklikleri tespit edin. Kritik ağ kaynaklarında yapılan değişiklikler yürürlüğe girdiğinde tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](../azure-monitor/essentials/activity-log.md#view-the-activity-log) 

- [Azure Izleyici 'de uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: küme cihazları tarafından oluşturulan güvenlik verilerini toplamak Için Azure izleyici 'ye Azure Batch hesabı ekleyin. Ortamdaki tehditleri algılamak ve yanıtlamak için özel sorgulardan yararlanın.  Kaynak düzeyinde izleme Azure Batch için Batch API 'Lerini kullanarak işler, görevler, düğümler ve havuzlar dahil kaynaklarınızın durumunu izleyin veya sorgulayın.

- [Azure Izleyici 'ye Azure Batch hesabı ekleme](batch-diagnostics.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Kılavuz**: Azure Batch hesap düzeyinde izleme Için, Azure izleyici 'nin özelliklerini kullanarak her Batch hesabını izleyin. Azure Izleyici, havuzlar, işler ve görevler gibi Batch hesabı düzeyinde kapsamlı kaynaklar için ölçümleri ve isteğe bağlı tanılama günlüklerini toplar. Batch hesabınızdaki etkinlikleri izlemek ve sorunları tanılamak için bu verileri el ile veya program aracılığıyla toplayıp kullanın.

Kaynak düzeyinde izleme Azure Batch için, işler, görevler, düğümler ve havuzlar dahil olmak üzere kaynaklarınızın durumunu izlemek veya sorgulamak için Azure Batch API 'Lerini kullanın.

- [Azure Batch hesap düzeyinde izleme ve günlüğe kaydetme 'yi yapılandırma](monitoring-overview.md)

- [Batch kaynak düzeyinde izlemeyi anlama](monitoring-overview.md#batch-resource-monitoring)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

#### <a name="azure-policy-built-in-definitions"></a>Azure Ilkesi yerleşik tanımları

[!INCLUDE [microsoft.batch-2-3](../../includes/policy/standards/asb/rp-controls/microsoft.batch-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: işletim sistemlerinden güvenlik günlüklerini toplama

**Kılavuz**: Azure izleyici, Azure Batch hesabınızdaki kaynaklar için ölçümleri ve tanılama günlüklerini toplar. Azure Batch hesabınızı izlemek ve sorunları tanılamak için kullanabileceğiniz çeşitli yollarla bu verileri toplayın ve kullanın. Ölçüm uyarılarını Ayrıca, bir ölçüm belirtilen bir değere ulaştığında bildirimler almanızı sağlayacak şekilde de yapılandırabilirsiniz.

Gerekirse, yerel işletim sistemi günlüklerine erişmek için güvenli kabuk (SSH) veya Uzak Masaüstü Protokolü (RDP) aracılığıyla ayrı havuz düğümlerine bağlanırsınız.

- [Azure Batch hesabınızdan tanılama günlüklerini toplama](batch-diagnostics.md#batch-diagnostics)

- [Azure Batch havuz düğümlerine uzaktan bağlanma](./batch-service-workflow-features.md#basic-workflow)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Rehberlik**: Azure Batch hesabını Azure izleyici 'ye ekleyin. Kullanılan Azure Log Analytics çalışma alanının, kuruluşunuzun uyumluluk düzenlemelerine göre ayarlanmış günlük tutma süresine sahip olduğundan emin olun

- [Azure Batch izlemeyi ve günlüğe kaydetmeyi yapılandırma](monitoring-overview.md)

- [Azure Log Analytics çalışma alanı bekletme dönemini yapılandırma](../azure-monitor/logs/manage-cost-storage.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Rehberlik**: belirtilen ölçüm değeri belirli bir eşiği aştığında tetiklenecek Azure Batch ölçüm uyarıları oluşturun.

- [Azure Batch ölçüm uyarılarını yapılandırma](batch-diagnostics.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Rehberlik**: belirtilen ölçüm değeri belirli bir eşiği aştığında tetiklenecek Azure Batch ölçüm uyarıları oluşturun.

- [Azure Batch ölçüm uyarılarını yapılandırma](batch-diagnostics.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="28-centralize-anti-malware-logging"></a>2,8: kötü amaçlı yazılımdan koruma 'yı merkezileştirme

**Rehberlik**: Windows işletim sistemleri durumunda ayrı toplu Iş düğümlerinde Windows Defender 'ı kullanın veya Linux kullanıyorsanız kendi kötü amaçlı yazılımdan koruma çözümünüzü sağlayın.

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="29-enable-dns-query-logging"></a>2,9: DNS sorgu günlüğünü etkinleştir

**Rehberlik**: DNS günlüğü için üçüncü taraf çözümü uygulayın.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="210-enable-command-line-audit-logging"></a>2,10: komut satırı denetim günlüğünü etkinleştir

**Rehberlik**: konsol günlüğünü ve PowerShell dökümünü, düğüm başına temelinde el ile yapılandırın.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: Azure Batch havuzunun sağlanması sırasında oluşturulan yerel yönetim hesabının kaydını ve oluşturduğunuz diğer hesapları saklayın. Ayrıca, Azure Active Directory tümleştirme kullanılırsa, Azure AD 'nin açıkça atanması ve bu nedenle sorgulanabilir olması gereken yerleşik rolleri vardır. Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü kullanın.

Ayrıca, Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini de kullanabilirsiniz.

- [Azure AD 'de PowerShell ile dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Azure Güvenlik Merkezi ile kimlik ve erişimi izleme](../security-center/security-center-identity-access.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Rehberlik**: bir Azure Batch havuzu sağlanırken, yerel makine hesapları oluşturma seçeneği verilir. Değiştirilecek varsayılan parola yoktur, ancak güvenli kabuk (SSH) ve Uzak Masaüstü Protokolü (RDP) erişimi için farklı parolalar belirtebilirsiniz. Azure Batch havuzu yapılandırıldıktan sonra, Azure portal veya Azure Resource Manager API aracılığıyla tek tek düğümler için rastgele bir kullanıcı oluşturabilirsiniz.

- [Belirli bir işlem düğümüne Kullanıcı ekleme](/rest/api/batchservice/computenode/adduser)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: Azure Active Directory Azure Batch uygulamalar Için kimlik doğrulamasını tümleştirin. Adanmış yönetim hesaplarının kullanımı etrafında ilke ve yordamlar oluşturun.

Ayrıca, Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini de kullanabilirsiniz.

- [Azure Active Directory ile Batch uygulamalarının kimliğini doğrulama](batch-aad-auth.md)

- [Azure Güvenlik Merkezi ile kimlik ve erişimi izleme](../security-center/security-center-identity-access.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory Azure Batch uygulamalar Için kimlik doğrulamasını tümleştirin. Azure AD çok faktörlü kimlik doğrulamasını etkinleştirin ve Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini izleyin.

 

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: yönetim görevleri için güvenli, Azure tarafından yönetilen iş istasyonları kullanın

**Rehberlik**: Azure Batch kaynaklarınızı oturum açmak ve yapılandırmak için yapılandırılmış çok faktörlü kimlik doğrulaması Ile Paws (ayrıcalıklı erişim iş istasyonları) kullanın.

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: Azure Active Directory sahip Azure Batch uygulamalar için tümleşik kimlik doğrulaması yaptıysanız, ortamda şüpheli veya güvenli olmayan bir etkinlik olduğunda günlüklerin ve uyarıların üretilmesi için Azure Active Directory güvenlik raporları kullanın. Kimlik ve erişim etkinliğini izlemek için Azure Güvenlik Merkezi 'ni kullanın.

- [Riskli etkinlik bayrağıyla işaretlenen Azure AD kullanıcılarını belirleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Güvenlik Merkezi 'nde Kullanıcı kimliğini ve erişim etkinliğini izleme](../security-center/security-center-identity-access.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: Azure Active Directory olan Azure Batch uygulamalar için tümleşik kimlik doğrulaması YAPTıYSANıZ, IP adresi aralıklarının veya ülkelerin/bölgelerin yalnızca belirli mantıksal gruplarından erişime izin vermek üzere koşullu erişim adlı konumları kullanabilirsiniz.

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: merkezi kimlik doğrulaması ve yetkilendirme sistemi olarak Azure Active Directory kullanın ve Azure AD Ile Azure Batch uygulamalar Için kimlik doğrulamasını tümleştirin. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifrelemeyi kullanarak verileri korur. Azure AD Ayrıca, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory-domain-services/tutorial-create-instance.md)

- [Azure AD ile Batch uygulamalarının kimliğini doğrulama](batch-aad-auth.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlar. Ayrıca, grup üyeliklerini verimli bir şekilde yönetmek için Azure kimlik erişimi Incelemelerini, kurumsal uygulamalara erişimi ve rol atamalarını da kullanabilirsiniz. Kullanıcıların erişimi, yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için düzenli aralıklarla gözden geçirilebilir.

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: Azure Active Directory Kullanıcı Hesapları Için Tanılama ayarları oluşturun, Denetim günlüklerini ve oturum açma günlüklerini bir Azure Log Analytics çalışma alanına gönderir. Azure Log Analytics çalışma alanında istenen uyarıları yapılandırın.

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapması üzerinde uyarı

**Rehberlik**: Kullanıcı kimlikleriyle ilgili şüpheli eylemleri algılanan otomatik yanıtları yapılandırmak Için Azure Active Directory risk algılamaları ve kimlik koruması özelliğini kullanın. Ayrıca, daha fazla araştırma için verileri Azure Sentinel 'e aktarabilirsiniz.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: destek senaryoları sırasında Microsoft 'un ilgili müşteri verilerine erişimini sağlama  

**Rehberlik**: kullanılamıyor; Müşteri Kasası Azure Batch için henüz desteklenmiyor.
 
- [Desteklenen Müşteri Kasası hizmetleri listesi](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: hassas bilgileri depolayan veya işleyen Azure kaynaklarını izlemeye yardımcı olması için etiketleri kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: geliştirme, test ve üretim için ayrı abonelikler ve/veya yönetim grupları uygulayın. Azure Batch havuzları, sanal ağ/alt ağ ile ayrılmalıdır, uygun şekilde etiketlenebilir ve bir ağ güvenlik grupları (NSG) ile korunmuş olmalıdır. Azure Batch verileri, güvenli bir Azure depolama hesabı içinde bulunmalıdır.

- [Sanal ağ içinde Azure Batch havuzu oluşturma](batch-virtual-network.md)

- [Azure depolama hesaplarını güvenli hale getirme](../storage/blobs/security-recommendations.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: hassas bilgiler Içeren Azure Batch havuzlarınızla Ilişkili Azure depolama hesapları Için, etiketleri kullanarak bunları gizli olarak Işaretleyin ve Azure en iyi uygulamaları ile güvenli hale getirin.

Veri tanımlama, sınıflandırma ve kayıp önleme özellikleri, Azure depolama veya işlem kaynakları için henüz kullanılamıyor. Uyumluluk amacıyla gerekliyse üçüncü taraf çözümünü uygulayın.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

- [Azure depolama hesaplarını güvenli hale getirme](../storage/blobs/security-recommendations.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Paylaşılan

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Rehberlik**: yoldaki tüm hassas bilgileri şifreleyin. Microsoft Azure kaynaklar varsayılan olarak TLS 1,2 ' i uzlaşacaktır. Azure Batch havuzlarınıza veya veri depolarına (Azure Storage hesapları) bağlanan tüm istemcilerin TLS 1,2 veya üzerini anlaşamadığından emin olun.

Azure Batch verilerinizi içeren depolama hesabına erişmek için HTTPS 'nin gerekli olduğundan emin olun.

- [Yoldaki Azure depolama hesabı şifrelemesini anlama](../storage/blobs/security-recommendations.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Paylaşılan

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: hassas bilgiler Içeren Azure Batch havuzlarınızla Ilişkili Azure depolama hesapları için, etiketleri kullanarak bunları gizli olarak Işaretleyin ve Azure en iyi uygulamaları ile güvenli hale getirin.

Veri tanımlama, sınıflandırma ve kayıp önleme özellikleri, Azure depolama veya işlem kaynakları için henüz kullanılamıyor. Uyumluluk amacıyla gerekliyse üçüncü taraf çözümünü uygulayın.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

- [Azure depolama hesaplarını güvenli hale getirme](../storage/blobs/security-recommendations.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Paylaşılan

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: kaynaklara erişimi denetlemek için rol tabanlı erişim denetimi kullanma

**Rehberlik**: Batch hesabı, Batch havuzları ve depolama hesapları dahil olmak üzere Azure kaynaklarının yönetim düzlemine erişimi denetlemek için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanın.

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: erişim denetimini zorlamak için ana bilgisayar tabanlı veri kaybı önleme kullanın

**Rehberlik**: veri tanımlama, sınıflandırma ve kayıp önleme özellikleri, Azure depolama veya işlem kaynakları için henüz kullanılamıyor. Uyumluluk amacıyla gerekliyse üçüncü taraf çözümünü uygulayın.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Paylaşılan

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Rehberlik**: Azure Batch hesabınızla ilişkili depolama hesapları Için, Microsoft 'un şifreleme anahtarlarını yönetmesine izin vermeniz önerilir, ancak gerekirse kendi anahtarlarınızı yönetme seçeneğiniz vardır.

Azure disk şifrelemesi, kurumsal güvenlik ve uyumluluk taahhütlerini karşılamak üzere verilerinizin korunmasına ve korunmasını sağlamaya yardımcı olmak için kullanılabilir. Mevcut disklere yazılan tüm yönetilen diskler, anlık görüntüler, görüntüler ve veriler, platform tarafından yönetilen anahtarlarla otomatik olarak şifrelenir.

- [Azure depolama hesapları için şifreleme anahtarlarını yönetme](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Müşteri tarafından yönetilen şifreleme anahtarlarını yapılandırma](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Disk şifrelemesi etkinken havuz oluşturma](disk-encryption.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Kılavuz**: Azure Batch hesaplarınız/havuzlarınızla ilişkili veya bunlarla Ilişkili kritik Azure kaynaklarında değişiklik gerçekleşirken uyarı oluşturmak Için Azure etkinlik günlüğü Ile Azure izleyici 'yi kullanın.

Havuz verilerine karşı tüm CRUD işlemlerini izlemek ve günlüğe kaydetmek için Azure Batch havuzuyla ilişkili depolama hesapları için tanılama ayarlarını yapılandırın.

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure depolama hesabı için ek günlüğe kaydetme/denetim özelliğini etkinleştirme](../storage/common/manage-storage-analytics-logs.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: otomatikleştirilmiş güvenlik açığı tarama araçlarını çalıştırma

**Rehberlik**: Azure Batch havuz düğümleri için, güvenlik açığı yönetimi çözümünü yönetmekten siz sorumlusunuz.

İsteğe bağlı olarak, Rapid7, Qualys veya başka bir güvenlik açığı yönetim platformu aboneliğiniz varsa, güvenlik açığı değerlendirmesi aracılarını Batch havuzu düğümlerine el ile yükleyebilirsiniz ve düğümleri ilgili portaldan yönetebilirsiniz.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: otomatik işletim sistemi düzeltme eki yönetimi çözümünü dağıtma

**Rehberlik**: temel Azure Batch havuzu düğüm görüntülerini sürdürmek ve güncelleştirmek için Microsoft. Azure Batch havuz düğümlerinin işletim sisteminin, otomatik güncelleştirmeleri etkinleştirmeyi, düğümleri izlemeyi veya düzenli olarak yeniden başlatmalar gerçekleştirmeyi gerektirebilecek küme ömrü boyunca düzeltme eki uygulandığından emin olun.

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Paylaşılan

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: üçüncü taraf yazılım başlıkları için otomatik düzeltme eki yönetimi çözümünü dağıtma

**Rehberlik**: Azure Batch havuz düğümlerinin üçüncü taraf uygulamalarının, otomatik güncelleştirmeleri etkinleştirmeyi, düğümleri izlemeyi veya düzenli olarak yeniden başlatmalar gerçekleştirmeyi gerektirebilecek küme ömrü boyunca düzeltme eki uygulandığından emin olun.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: geri dönüş güvenlik açığı taramalarını karşılaştırın

**Rehberlik**: bir Rapid7, Qualys veya başka bir güvenlik açığı yönetim platformu aboneliğiniz varsa, geri dönüş güvenlik açıklarını görüntülemek ve karşılaştırmak için bu satıcının portalını kullanabilirsiniz.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek için risk derecelendirme işlemi kullanın

**Rehberlik**: ortak bir risk Puanlama programı (örneğin, ortak güvenlik açığı Puanlama sistemi) veya üçüncü taraf tarama aracınız tarafından sunulan varsayılan risk derecelendirmelerini kullanın.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: abonelikleriniz dahilinde (işlem, depolama, ağ, vb.) tüm kaynakları sorgulamak/öğrenmek Için Azure Kaynak grafiğini kullanın. Kiracınızda uygun (okuma) izinlere sahip olduğunuzdan ve aboneliklerinizin içindeki kaynakların yanı sıra tüm Azure aboneliklerinin numaralandırılmasını sağlayabildiğinizden emin olun.

Klasik Azure kaynakları Azure Resource Graph Explorer aracılığıyla bulunabilir, ancak Azure Resource Manager kaynakların ileride oluşturulması ve kullanılması önerilir.

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure aboneliklerinizi görüntüleme](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Azure RBAC 'yi anlama](../role-based-access-control/overview.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="62-maintain-asset-metadata"></a>6,2: varlık meta verilerini koruma

**Kılavuz**: Azure kaynaklarına Etiketler uygulayarak bunları bir taksonomi halinde mantıksal olarak organize etmek için meta veriler verirsiniz.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: yetkisiz Azure kaynaklarını silme

**Rehberlik**: varlıkları düzenlemek ve izlemek için uygun yerlerde etiketleme, yönetim grupları ve ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynaklarının envanterini tanımlayın ve saklayın

**Rehberlik**: onaylanan Azure kaynakları listesini ve işlem kaynakları için onaylanan yazılımı tanımlayın

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerine oluşturulabilecek kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:

- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

Aboneliklerinizin içindeki kaynakları sorgulamak/öğrenmek için Azure Kaynak grafiğini kullanın. Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: işlem kaynakları içindeki onaylanmamış yazılım uygulamaları için izleyici

**Rehberlik**: Azure Batch havuz düğümleri için, onaylanmamış yazılım uygulamalarına yönelik küme düğümlerini izlemek üzere bir üçüncü taraf çözümü uygulayın.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Rehberlik**: Azure Batch havuz düğümleri için, onaylanmamış yazılım uygulamalarına yönelik küme düğümlerini izlemek üzere bir üçüncü taraf çözümü uygulayın.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="68-use-only-approved-applications"></a>6,8: yalnızca onaylanan uygulamaları kullan

**Rehberlik**: Azure Batch havuz düğümleri için, yetkisiz yazılımın yürütülmesini engelleyen bir üçüncü taraf çözümü uygulayın.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerine oluşturulabilecek kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın: 
- İzin verilmeyen kaynak türleri 
- İzin verilen kaynak türleri 

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](../governance/policy/samples/built-in-policies.md#general)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: onaylanan yazılım başlıkları envanterini koruyun

**Rehberlik**: Azure Batch havuz düğümleri için, yetkisiz dosya türlerinin yürütülmesini engelleyen bir üçüncü taraf çözümü uygulayın.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak Için Azure koşullu erişimi kullanın.

- [Azure Resource Manager erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Rehberlik**: Azure Batch hesaplarınız ve havuzlarınızın yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft.Batch" ad alanındaki Azure ilke diğer adlarını kullanın.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: güvenli işletim sistemi yapılandırması oluşturma

**Rehberlik**: Batch havuzu düğümleriniz için güvenli yapılandırma oluşturma.

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Rehberlik**: Batch hesabınızla ve havuzlarınızla ilgili Azure kaynakları için (sanal ağlar, alt ağlar, Azure Güvenlik duvarları, Azure depolama hesapları, vb.) güvenlik ayarlarını zorlamak üzere Azure ilkesi [reddetme] ve [dağıtım yok] kullanın. Özel ilkeler oluşturmak için aşağıdaki ad alanlarından Azure Ilke diğer adlarını kullanabilirsiniz:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: güvenli işletim sistemi yapılandırmalarının bakımını yapma

**Rehberlik**: Microsoft tarafından yönetilen ve korunan Azure Batch havuzu Işletim sistemi görüntüleri. İşletim sistemi düzeyinde durum yapılandırması uygulamaktan siz sorumlusunuz.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Paylaşılan

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Rehberlik**: Azure Batch hesaplarınız, havuzlarınız veya ilgili kaynaklarınız Için özel Azure ilke tanımları kullanıyorsanız, kodunuzu güvenli bir şekilde depolamak ve yönetmek için Azure Repos kullanın.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos belgeleri](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: özel işletim sistemi görüntülerini güvenli bir şekilde depolayın

**Kılavuz**: Azure Batch havuzlarınız için özel görüntüler kullanıyorsanız, yalnızca yetkili kullanıcıların görüntülere erişebildiğinden emin olmak için rol tabanlı erişim denetımı (RBAC) kullanın.

- [Azure 'da RBAC 'yi anlama](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Azure’da RBAC’yi yapılandırma](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: yerleşik Azure ilke tanımlarını kullanarak Azure Batch ilgili kaynak yapılandırmasını uyarır, denetleyebilir ve zorunlu kılabilirsiniz.  Azure Batch hesaplarınız ve havuzlarınız için özel ilkeler oluşturmak üzere "Microsoft.Batch" ad alanındaki Azure Ilke diğer adlarını kullanın. Ayrıca, ilke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: işletim sistemleri için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: Azure Batch havuz düğümlerinizin işletim sistemlerine yönelik istenen durumu korumak için bir üçüncü taraf çözümü uygulayın.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: Azure Batch örneğinizin yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft.Batch" ad alanındaki Azure ilke diğer adlarını kullanın. Ayrıca, Azure Batch için özel olarak oluşturulan yerleşik ilkeleri veya Azure Batch tarafından kullanılan kaynakları da kullanabilirsiniz; örneğin:
- Alt ağlar bir ağ güvenlik grubu ile ilişkilendirilmelidir-depolama hesapları bir sanal ağ hizmeti uç noktası kullanmalıdır
- Batch hesaplarındaki tanılama günlükleri etkinleştirilmelidir

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: işletim sistemleri için otomatik yapılandırma izlemeyi Uygula

**Rehberlik**: Azure Batch havuz düğümlerinizin işletim sistemlerinin durumunu izlemek için bir üçüncü taraf çözümü uygulayın.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: Azure Key Vault, Azure depolama hesapları içindeki havuz depolama için anahtarları yönetmek üzere Azure Batch dağıtımlarıyla birlikte kullanılabilir.

- [Azure yönetilen kimliklerle tümleştirme](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Azure Key Vault oluşturma](../key-vault/general/quick-create-portal.md)

- [Key Vault kimlik doğrulaması yapma](../key-vault/general/authentication.md)
- [Key Vault erişim ilkesi atama](../key-vault/general/assign-access-policy-portal.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: kod içinde kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayın. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar. 

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: merkezi olarak yönetilen kötü amaçlı yazılımdan koruma yazılımı kullanın

**Rehberlik**: Windows işletim sistemleri durumunda tek tek Azure Batch havuz düğümlerinde Windows Defender 'ı kullanın veya Linux kullanıyorsanız kendi kötü amaçlı yazılımdan koruma çözümünüzü sağlayın.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: işlem dışı Azure kaynaklarına yüklenecek dosyaları önceden Tara

**Rehberlik**: Microsoft kötü amaçlı yazılımdan koruma, Azure hizmetlerini destekleyen temel alınan konakta (örneğin, Azure Batch) etkinleştirilir, ancak müşteri içeriğinde çalışmaz.

App Service, Data Lake Storage, BLOB depolama vb. gibi işlem dışı Azure kaynaklarına yüklenen tüm dosyaları önceden tarayın. Microsoft bu örneklerdeki müşteri verilerine erişemez.

- [Azure Cloud Services ve sanal makineler için Microsoft Antimalware 'i anlayın](../security/fundamentals/antimalware.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: kötü amaçlı yazılımdan koruma yazılımlarının ve imzaların güncelleştirildiğinden emin olun

**Rehberlik**: Windows işletim sistemleri durumunda tek tek Azure Batch havuz düğümlerinde Windows Defender 'ı kullanın ve otomatik güncelleştirme özelliğinin etkinleştirildiğinden emin olun. Linux kullanıyorsanız kendi kötü amaçlı yazılımdan koruma çözümünüzü sağlayın.

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli otomatik arka pencerelere emin olun

**Rehberlik**: Azure Batch havuz veri deposu Için bir Azure depolama hesabı kullanırken, uygun artıklık seçeneğini (LRS, ZRS, GRS, RA-GRS) seçin. 

- [Azure depolama hesapları için depolama yedekliği nasıl yapılandırılır](../storage/common/storage-redundancy.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: Azure Batch havuz veri deposu Için bir Azure depolama hesabı kullanırken, uygun artıklık seçeneğini (LRS, ZRS, GRS, RA-GRS) seçin. Azure Batch dağıtımınızın herhangi bir bölümü için Azure Key Vault kullanıyorsanız, anahtarlarınızın yedeklendiğinden emin olun.

- [Azure depolama hesapları için depolama yedekliği nasıl yapılandırılır](../storage/common/storage-redundancy.md)

- [Azure 'da Anahtar Kasası anahtarlarını yedekleme](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Kılavuz**: Azure depolama hesapları için kendi anahtarlarınızı veya Azure Batch uygulamanız ile ilgili başka bir kaynak yönetiyorsanız, yedeklenen anahtarların düzenli olarak geri yüklenmesini test edin.

- [Azure 'da Anahtar Kasası anahtarlarını yedekleme](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

- [Müşteri tarafından yönetilen anahtarı PowerShell ile geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: Azure Key Vault Azure Batch havuz depolama hesaplarıyla ilgili herhangi bir anahtarı tutmak için kullanılıyorsa, anahtarları yanlışlıkla veya kötü amaçlı silme ile korumak için Azure Key Vault Soft-Delete etkinleştirin.

- [Azure Key Vault 'da geçici silme nasıl etkinleştirilir](../key-vault/general/key-vault-recovery.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: personel rollerinin yanı sıra olay işleme/yönetim aşamalarını tanımlayan yazılı olay yanıt planları olduğundan emin olun.

- [Azure Güvenlik Merkezi 'nde Iş akışı otomasyonlarını yapılandırma](../security-center/security-center-planning-and-operations-guide.md)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Rehberlik**: Güvenlik Merkezi, her uyarıya katılma sırasını önceliklendirmenize yardımcı olmak için uyarılara önem derecesi atar. böylece, bir kaynak tehlikeye girdiğinde, hemen bu işe başlayabilirsiniz. Önem derecesi, uyarı veren etkinliğin arkasında kötü amaçlı bir amaç olduğunu ve uyarıyı vermek için kullanılan analitik düzeyini, ne kadar güvenli bir güvenlik merkezinin olduğunu temel alır.

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve açıkları belirleyip planı gerektiği şekilde gözden geçirin.

- [NıST 'nin yayını: BT planları ve özellikleri için test, eğitim ve alıştırma programlarını inceleyin](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Müşteri

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: Microsoft Güvenlik Yanıt MERKEZI (MSRC), verilerinize izinsiz veya yetkisiz bir taraf tarafından erişildiğini belirlerse, Microsoft tarafından sizinle iletişim kurmak için güvenlik olayı iletişim bilgileri kullanılacaktır.

- [Azure Güvenlik Merkezi güvenlik Ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Azure Güvenlik Merkezi izlemesi**: Yes

**Sorumluluk**: Müşteri

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: güvenlik uyarılarını olay yanıt sisteminizle birleştirme

**Rehberlik**: sürekli dışa aktarma özelliğini kullanarak Azure Güvenlik Merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Sürekli dışa aktarma, uyarıları ve önerileri el ile veya devam eden sürekli bir biçimde dışa aktarmanız sağlar. Azure Güvenlik Merkezi veri bağlayıcısını kullanarak uyarıları Azure Sentinel 'e akışını sağlayabilirsiniz.

- [Sürekli dışarı aktarmayı yapılandırma](../security-center/continuous-export.md)

- [Uyarıların Azure Sentinel’e akışını yapma](../sentinel/connect-azure-security-center.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: güvenlik uyarılarına yanıtı otomatikleştirme

**Rehberlik**: güvenlik uyarılarında ve önerilerinde "Logic Apps" aracılığıyla yanıtları otomatik olarak tetiklemek Için Azure Güvenlik Merkezi 'Nde Iş akışı Otomasyonu özelliğini kullanın.

- [Iş akışı otomasyonu ve Logic Apps yapılandırma](../security-center/workflow-automation.md)

**Azure Güvenlik Merkezi izlemesi**: Şu anda kullanılamıyor

**Sorumluluk**: Müşteri

## <a name="penetration-tests-and-red-team-exercises"></a>Sızma Testleri ve Red Team Alıştırmaları

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: Penetme testleri ve Red ekibi alıştırmaları](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Azure kaynaklarınızın düzenli olarak sızma testini gerçekleştirin ve tüm kritik güvenlik bulgularını düzeltmeye dikkat edin

**Rehberlik**:- [Penettim testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak Için lütfen Microsoft katılım kurallarını izleyin](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

Microsoft 'un Microsoft tarafından yönetilen bulut altyapısına, hizmetlerine ve uygulamalarına göre kırmızı ekip oluşturma ve canlı site sızma testini yürütme hakkında daha fazla bilgi edinebilirsiniz: 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Güvenlik Merkezi ile izleme**: Uygulanamaz

**Sorumluluk**: Paylaşılan

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](../security/benchmarks/overview.md)
- [Azure güvenlik temelleri](../security/benchmarks/security-baselines-overview.md) hakkında daha fazla bilgi edinin