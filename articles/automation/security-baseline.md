---
title: Otomasyon için Azure Güvenlik temeli
description: Automation güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: automation
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 7f87fcac0c1b07e108082c4b4f48bde046dc63c7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834507"
---
# <a name="azure-security-baseline-for-automation"></a>Otomasyon için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 1,0](../security/benchmarks/overview-v1.md) ' dan Azure Otomasyonu 'na kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar.
İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Azure Otomasyonu için geçerli olan ilgili kılavuza göre gruplandırılır. Azure Otomasyonu için geçerli olmayan **denetimler** dışlandı.

 
Azure Otomasyonu 'nun Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Azure Otomasyonu güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Kılavuz**: Azure Otomasyonu hesabı, hizmete erişimi özel uç noktalar aracılığıyla kısıtlamak Için Azure özel bağlantısını henüz desteklememektedir. Azure 'daki kaynaklara göre kimlik doğrulayan ve çalıştırılan ve çalıştıran runbook 'lar, bir Azure korumalı alanı üzerinde çalışır ve Microsoft 'un birbirini yalıtmaktan sorumlu olan paylaşılan arka uç kaynaklarıyla yararlanır; ağları kısıtlanmamış ve ortak kaynaklara erişebilir. Azure Otomasyonu, karma runbook çalışanları desteğinin ötesinde özel ağ için sanal ağ tümleştirmesine sahip değildir. Karma runbook çalışanları olmadan kullanıma hazır hizmet hizmetini kullanıyorsanız bu denetim geçerli değildir.

Runbook 'larınız için daha fazla yalıtım sağlamak üzere, Azure sanal makinelerinde çalışan karma runbook çalışanlarını kullanabilirsiniz. Bir Azure sanal makinesi oluşturduğunuzda bir sanal ağ (VNet) oluşturmanız veya var olan bir VNet kullanmanız ve VM 'lerinizi bir alt ağ ile yapılandırmanız gerekir. Dağıtılan tüm alt ağların, uygulamalarınıza güvenilen bağlantı noktalarına ve kaynaklarına özel ağ erişim denetimleriyle uygulanmış bir ağ güvenlik grubu olduğundan emin olun. Hizmete özgü gereksinimler için ilgili hizmetin güvenlik önerisi ' ne bakın. Alternatif olarak, belirli bir gereksiniminize sahipseniz, Azure Güvenlik duvarı da bu uygulamayı karşılamak için de kullanılabilir.

- [Azure 'da sanal ağlar ve sanal makineler](../virtual-machines/network-overview.md)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Güvenlik duvarını dağıtma ve yapılandırma](../firewall/tutorial-firewall-deploy-portal.md)

- [Runbook yürütme ortamı](./automation-runbook-execution.md#runbook-execution-environment)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: sanal ağların, alt ağların ve ağ arabirimlerinin yapılandırma ve trafiğini izleme ve günlüğe kaydetme

**Kılavuz**: Azure Otomasyonu Şu anda, karma runbook çalışanları desteğinin ötesinde özel ağ için sanal ağ tümleştirmesine sahip değildir. Karma runbook çalışanları olmadan kullanıma hazır hizmet hizmetini kullanıyorsanız bu denetim geçerli değildir.

Azure sanal makineler tarafından desteklenen karma runbook çalışanları kullanıyorsanız, bu çalışanları içeren alt ağın bir ağ güvenlik grubu (NSG) ile etkinleştirildiğinden emin olun ve akış günlüklerini, trafik denetimi için günlükleri bir depolama hesabına iletecek şekilde yapılandırın. Ayrıca, NSG akış günlüklerini bir Log Analytics çalışma alanına iletebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır.

NSG kuralları ve Kullanıcı tanımlı yollar özel uç nokta için uygulanmadığında, giden bağlantılar için NSG akış günlükleri ve izleme bilgileri hala desteklenmektedir ve kullanılabilir.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Kılavuz**: Azure Otomasyonu, karma runbook çalışanları desteğinin ötesinde özel ağ için sanal ağ tümleştirmesine sahip değildir. Karma runbook çalışanları olmadan kullanıma hazır hizmet hizmetini kullanıyorsanız bu denetim geçerli değildir.

Azure sanal makineler tarafından desteklenen karma runbook çalışanları kullanıyorsanız, DDoS saldırılarına karşı koruma sağlamak için karma runbook çalışanlarınızı barındıran sanal ağlarınızda dağıtılmış hizmet reddi (DDoS) standart korumasını etkinleştirin. Azure Güvenlik Merkezi tümleşik tehdit zekasını kullanarak, bilinen kötü amaçlı IP adresleriyle iletişimleri reddedebilirsiniz.  Tehdit zekasını etkinleştirilmiş olan sanal ağ kesimlerinin her birinde Azure Güvenlik duvarını yapılandırın ve kötü amaçlı ağ trafiği için **uyarı ve reddetme** seçeneğini yapılandırın.

Windows sanal makinelerinin sınırlı bir süre boyunca onaylanan IP adresleriyle açıklanabilmesi için Azure Güvenlik Merkezi 'nin tam zamanında ağ erişimini kullanabilirsiniz.  Ayrıca, bağlantı noktalarını ve kaynak IP 'Leri gerçek trafik ve tehdit zekası temelinde sınırlamak için NSG yapılandırmalarının Azure Güvenlik Merkezi Uyarlamalı ağ sağlamlaştırma önerilerini kullanın.

- [DDoS korumasını yapılandırma](../ddos-protection/manage-ddos-protection.md)

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Güvenlik Merkezi tümleşik tehdit zekasını anlama](../security-center/azure-defender.md)

- [Azure Güvenlik Merkezi Uyarlamalı ağ sağlamlaştırma 'yi anlama](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Güvenlik Merkezi 'Ni tam zamanında ağ Access Control anlama](../security-center/security-center-just-in-time.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Kılavuz**: Azure Otomasyonu, karma runbook çalışanları desteğinin ötesinde özel ağ için sanal ağ tümleştirmesine sahip değil, karma çalışanları olmadan kullanıma hazır hizmet hizmetini kullanıyorsanız bu denetim geçerli değildir.

Azure sanal makineler tarafından desteklenen karma runbook çalışanları kullanıyorsanız, bir depolama hesabına NSG akış günlüklerini kaydedebilir ve bu da runbook çalışanları olarak davranan Azure sanal makineleriniz için akış kayıtları oluşturabilirsiniz. Anormal etkinlikleri araştırırken, ağ trafiğinin olağan dışı ve beklenmeyen etkinlikler için incelenebilecek şekilde ağ Izleyicisi paket yakalamayı etkinleştirebilirsiniz.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Ağ İzleyicisini etkinleştirme](../network-watcher/network-watcher-create.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Kılavuz**: Azure Otomasyonu, karma runbook çalışanları desteğinin ötesinde özel ağ için sanal ağ tümleştirmesine sahip değildir. Karma runbook çalışanları olmadan kullanıma hazır hizmet hizmetini kullanıyorsanız bu denetim geçerli değildir.

Azure sanal makinelerinde barındırılan karma runbook çalışanları kullanıyorsanız, ağ Izleyicisi ve açık kaynak KIMLIKLERI araçları tarafından sunulan paket yakalamalarını birleştirerek bu çalışan makinelere yönelik çok çeşitli tehditler için ağ izinsiz giriş algılaması gerçekleştirebilirsiniz. Ayrıca, tehdit zekasını etkinleştirilmiş ve kötü amaçlı ağ trafiği için "uyarı ve reddetme" olarak yapılandırılmış Azure Güvenlik duvarını sanal ağ kesimlerine uygun şekilde dağıtabilirsiniz.

- [Ağ Izleyicisi ve açık kaynak araçları ile ağ üzerinden izinsiz bir algılama gerçekleştirme](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Azure Güvenlik duvarını dağıtma](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Güvenlik Duvarı ile uyarıları yapılandırma](../firewall/threat-intel.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Rehberlik**: ağ güvenlik gruplarında veya Azure 'Da yapılandırılan Azure Güvenlik duvarında, Otomasyon kaynaklarınıza erişmesi gereken ağ erişim denetimlerini tanımlamak Için sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adı (örneğin, GuestAndHybridManagement) belirterek, karşılık gelen hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

- [Hizmet etiketlerini anlama ve kullanma](../virtual-network/service-tags-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Rehberlik**: Azure Otomasyonu tarafından Azure ilkesi tarafından kullanılan ağ kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın.

Azure şemaları, Azure Kaynak Yöneticisi şablonları, Azure RBAC denetimleri ve ilkeleri gibi tek bir şema tanımında paket ortam yapıtları sunarak büyük ölçekli Azure dağıtımlarını basitleştirmek için de kullanabilirsiniz. Şema 'i yeni aboneliklere uygulayabilir ve sürüm oluşturma aracılığıyla denetim ve yönetimi ayarlayabilirsiniz.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Ağ için Azure Ilke örnekleri](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: NSG 'ler ve ağ güvenliği ve trafik akışıyla ilgili diğer kaynaklar için Etiketler kullanın. Bireysel NSG kuralları için "Açıklama" alanını kullanarak ağa giden/giden trafiğe izin veren kuralların iş gereksinimini ve/veya süresini (vs.) belirtin.

Tüm kaynakların etiketlerle oluşturulmasını ve mevcut etiketlenmemiş kaynakları bilgilendirmesini sağlamak için etiketlemeyle ilgili yerleşik Azure Ilke tanımlarından herhangi birini ("etiket ve onun değeri gerektir" gibi) kullanın.

Azure PowerShell veya Azure CLı kullanarak, etiketlerine göre kaynaklar üzerinde arama yapabilir veya eylemler gerçekleştirebilirsiniz.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Rehberlik**: kaynak yapılandırmasını izlemek ve ağ kaynaklarınızda yapılan değişiklikleri algılamak Için Azure etkinlik günlüğü 'nü kullanın. Kritik kaynaklardaki değişiklikler gerçekleşirken tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Kılavuz**: Azure Otomasyonu kaynakları tarafından oluşturulan güvenlik verilerini toplamak için günlük verilerini Azure izleyici günlüklerine iletin. Azure Izleyici 'de, arama yapmak ve analiz yapmak için günlük sorgularını kullanın ve uzun süreli/arşiv depolama için Azure depolama hesaplarını kullanın. Azure Otomasyonu, Log Analytics çalışma alanınıza runbook iş durumu, iş akışları, otomasyon durumu yapılandırma verileri, güncelleştirme yönetimi ve değişiklik izleme ya da envanter günlükleri gönderebilir. Bu bilgiler, basit araştırmalar gerçekleştirmenize olanak tanıyan Azure portal, Azure PowerShell ve Azure Izleyici günlükleri API 'sinden görülebilir.

Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz. 

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Izleyici ve üçüncü taraf SıEM tümleştirmesi ile çalışmaya başlama](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Azure Otomasyonu iş verilerini Azure İzleyici günlüklerine iletme](automation-manage-send-joblogs-log-analytics.md)

- [DSC 'yi Azure Izleyici günlükleriyle tümleştirme](automation-dsc-diagnostics.md)

- [Bağlı Log Analytics çalışma alanı için desteklenen bölgeler](how-to/region-mappings.md)

- [Güncelleştirme Yönetimi günlükleri sorgula](update-management/query-logs.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: olay kaynağı, tarih, Kullanıcı, zaman damgası, kaynak adresleri, hedef adresler ve diğer yararlı öğeleri içeren denetim ve etkinlik günlüklerinize erişim Için Azure izleyicisini etkinleştirin. 

- [Azure Izleyici ile platform günlükleri ve ölçümleri toplama](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure izleyici 'de, Log Analytics çalışma alanı saklama dönemini kuruluşunuzun uyumluluk düzenlemelerine göre ayarlayın. Uzun süreli/arşiv depolama için Azure depolama hesaplarını kullanın.

- [Log Analytics veri saklama süresini değiştirme](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Otomasyon hesapları için veri saklama ayrıntıları](./automation-managing-data.md#data-retention)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Rehberlik**: anormal davranış için günlükleri çözümleyin ve izleyin ve sonuçları düzenli olarak gözden geçirin. Günlükleri gözden geçirmek ve günlük verilerinde sorgular gerçekleştirmek için Azure Izleyici günlük sorgularını kullanın.

Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz. 

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Izleyici 'de günlük sorgularını anlama](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Izleyici 'de özel sorgular gerçekleştirme](../azure-monitor/logs/get-started-queries.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Rehberlik**: güvenlik günlükleri ve olayları 'nda bulunan anormal etkinliklerle ilgili izleme ve uyarı Için Azure Izleyici Ile Azure Güvenlik Merkezi 'ni kullanın.

Alternatif olarak, Azure Sentinel 'de ve yerleşik verileri etkinleştirebilir.

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

- [Azure Güvenlik Merkezi 'nde uyarıları yönetme](../security-center/security-center-managing-and-responding-alerts.md)

- [Azure Izleyici günlük verilerinde uyarı alma](../azure-monitor/alerts/tutorial-response.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="29-enable-dns-query-logging"></a>2,9: DNS sorgu günlüğünü etkinleştir

**Rehberlik**: kuruluşunuzun ihtiyaç duyduğu şekılde, DNS günlüğü çözümü Için Azure Marketi 'nden bir üçüncü taraf çözümü uygulayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: Azure Active Directory (Azure AD), açıkça atanabilen ve sorgulanabilen yerleşik yönetici rollerini kullanın. Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü kullanın. Runbook 'larınız için Otomasyon hesabı farklı çalıştır hesaplarını kullanırken, bu hizmet sorumlularının, genellikle daha fazla zaman yükseltilmiş izinlere sahip olduğundan envanterinizde izlenmesi gerekir. Sunulan saldırı yüzeyini en aza indirmek için kullanılmayan farklı çalıştır hesaplarını silin.

- [Azure AD 'de PowerShell ile dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Farklı Çalıştır veya Klasik Farklı Çalıştır hesabını silme](delete-run-as-account.md)

- [Azure Otomasyonu farklı çalıştır hesabını yönetme](manage-runas-account.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Kılavuz**: Azure Otomasyonu hesabının varsayılan parola kavramı yoktur.  Müşteriler, hizmette veya karma runbook çalışanları üzerinde çalışan varsayılan parolaları kullanan üçüncü taraf uygulamalardan ve Market hizmetlerinden sorumludur.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: adanmış yönetim hesaplarının kullanımı etrafında standart işletim yordamları oluşturun. Yönetim hesaplarının sayısını izlemek için Azure Güvenlik Merkezi kimlik ve erişim yönetimi 'ni kullanın. Runbook 'larınız için Otomasyon hesabı farklı çalıştır hesaplarını kullanırken, bu hizmet sorumlularının, genellikle daha fazla zaman yükseltilmiş izinlere sahip olduğundan envanterinizde izlenmesi gerekir. Bu kimliklerin, runbook 'larınızın otomatikleştirilmiş işlemlerini başarıyla gerçekleştirmesi için ihtiyaç duydukları en az ayrıcalıklı izinlerle kapsamını kapsam. Sunulan saldırı yüzeyini en aza indirmek için kullanılmayan farklı çalıştır hesaplarını silin.

Ayrıca, Microsoft Hizmetleri için ayrıcalıklı rolleri Privileged Identity Management Azure Active Directory (Azure AD) kullanarak tam zamanında/tam erişimi etkinleştirebilirsiniz ve Azure Resource Manager.

- [Privileged Identity Management hakkında daha fazla bilgi edinin](../active-directory/privileged-identity-management/index.yml)

- [Farklı Çalıştır veya Klasik Farklı Çalıştır hesabını silme](delete-run-as-account.md)

- [Azure Otomasyonu farklı çalıştır hesabını yönetme](manage-runas-account.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory çoklu oturum açma kullan (SSO)

**Rehberlik**: mümkün olan yerlerde, tek başına bağımsız kimlik bilgilerini hizmet başına yapılandırmak yerine Azure Active Directory (Azure AD) ile SSO kullanın. Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini kullanın.

- [Azure AD 'de uygulamalarda çoklu oturum açma](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

- [Azure’da kimlik doğrulaması yapmak için Azure AD’yi kullanma](automation-use-azure-ad.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin ve Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini izleyin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: tüm yönetim görevleri için adanmış makineler (ayrıcalıklı erişim Iş Istasyonları) kullanın

**Rehberlik**: üretim ortamlarında Azure Otomasyonu hesap kaynaklarını açmak ve yapılandırmak için çok faktörlü kimlik doğrulaması Ile Paw 'leri kullanın. 

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar 

**Rehberlik**: riskli Kullanıcı davranışında uyarıları ve raporları görüntülemek için Azure Active Directory (Azure AD) risk algılamalarını kullanın. İsteğe bağlı olarak, müşteri Azure Güvenlik Merkezi risk algılama uyarılarını Azure Izleyici 'ye iletebilir ve eylem gruplarını kullanarak özel uyarı/bildirim yapılandırabilir.

- [Azure Güvenlik Merkezi risk algılamalarını anlama (şüpheli etkinlik)](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Özel uyarı ve bildirim için eylem gruplarını yapılandırma](../azure-monitor/alerts/action-groups.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme 

**Rehberlik**: IP adresi aralıklarının veya ülkelerin/bölgelerin yalnızca belirli mantıksal gruplarından erişime izin vermek için adlandırılmış konumlar kullanılması önerilir. 

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifrelemeyi kullanarak verileri korur. Azure AD Ayrıca, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar. Karma runbook çalışanları kullanılıyorsa, daha sorunsuz güvenli izinler sağlamak için farklı çalıştır hesapları yerine yönetilen kimliklerden yararlanabilirsiniz.

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory-domain-services/tutorial-create-instance.md)

- [Yönetilen kimliklerle runbook kimlik doğrulamasını kullanma](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlar. Ayrıca, grup üyeliklerini etkin bir şekilde yönetmek, kurumsal uygulamalara erişmek ve rol atamaları için Azure kimlik erişimi incelemelerini kullanın. Yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için, Kullanıcı erişimi düzenli olarak incelenebilir. Runbook 'larınız için Otomasyon hesabı farklı çalıştır hesaplarını kullanırken, bu hizmet sorumluları, genellikle daha fazla zaman yükseltilmiş izinlere sahip olduğundan envanterinizde de izlenir. Sunulan saldırı yüzeyini en aza indirmek için kullanılmayan farklı çalıştır hesaplarını silin.

- [Azure AD raporlamayı anlama](../active-directory/reports-monitoring/index.yml)

- [Azure kimlik erişimi incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

- [Farklı Çalıştır veya Klasik Farklı Çalıştır hesabını silme](delete-run-as-account.md)

- [Azure Otomasyonu farklı çalıştır hesabını yönetme](manage-runas-account.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: herhangi bir SIEM/izleme aracıyla tümleştirmenize imkan tanıyan Azure Active Directory (Azure AD) oturum açma etkinliğine, denetim ve risk olay günlüğü kaynaklarına erişiminiz vardır.

Bu işlemi, Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturarak ve Log Analytics çalışma alanına denetim günlüklerini ve oturum açma günlüklerini göndererek kolaylaştırabilirsiniz. İstenen uyarıları Log Analytics çalışma alanı içinde yapılandırabilirsiniz.

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapması üzerinde uyarı

**Rehberlik**: ağ kaynağınızın Kullanıcı kimlikleriyle ilgili şüpheli eylemler için otomatik yanıtlar yapılandırmak üzere Azure Active Directory (Azure AD) riskini ve kimlik koruması özelliklerini kullanın. Ayrıca, daha fazla araştırma için verileri Azure Sentinel 'e aktarabilirsiniz.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: destek senaryoları sırasında Microsoft 'un ilgili müşteri verilerine erişimini sağlama

**Kılavuz**: Azure Otomasyonu hesaplarında, Microsoft desteği başka bir araç kullanılmadan açık bir destek talebi sırasında platform kaynak meta verilerine erişebilir.

Bununla birlikte, Azure sanal makineler tarafından desteklenen karma runbook çalışanları ve müşteri verilerine (örneğin, bir destek isteği sırasında) erişmek için bir üçüncü taraf ihtiyaçları kullanılırken, müşteri verileri erişim isteklerini gözden geçirmek ve onaylamak veya reddetmek için Azure sanal makineler için Müşteri Kasası (Önizleme) kullanın.

- [Müşteri Kasası anlama](../security/fundamentals/customer-lockbox-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: gizli bilgileri depolayan veya Işleyen Azure Otomasyonu kaynaklarını izlemeye yardımcı olması için etiketleri kullanın. 

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: geliştirme, test ve üretim için ayrı abonelikler ve/veya yönetim grupları uygulayın. Ortamları ayrı Otomasyon hesabı kaynakları kullanarak yalıtın. Karma runbook çalışanları gibi kaynaklar, sanal ağ/alt ağ ile ayrılmalıdır, uygun şekilde etiketlenebilir ve bir ağ güvenlik grubu (NSG) veya Azure Güvenlik duvarı içinde güvenliği sağlanmış olmalıdır. Hassas verileri depolayan veya işleyen sanal makineler için, kullanımda olmadığında devre dışı bırakmak üzere ilke ve yordam uygulayın.

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

**Rehberlik**: karma Runbook Worker özelliği kullanılırken, hassas bilgilerin yetkisiz aktarımını izleyen ve bilgi güvenliği uzmanlarına uyarı ederken bu tür aktarımları engelleyen bir üçüncü taraf çözümünü Azure Marketi 'nden yararlanın.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini, müşteri veri kaybına ve pozlamaya karşı hassas ve koruma olarak değerlendirir. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Rehberlik**: yoldaki tüm hassas bilgileri şifreleyin. Azure sanal ağlarında Azure kaynaklarınıza bağlanan tüm istemcilerin TLS 1,2 veya üzeri bir anlaşma sağlayabilmesini sağlayın. Azure Otomasyonu, tüm dış ıSıPS uç noktaları için (Web kancaları, DSC düğümleri, karma runbook çalışanı aracılığıyla) Aktarım Katmanı (TLS) 1,2 ve tüm istemci çağrılarını ve sonraki sürümlerini tam olarak destekler ve uygular.

Azure Güvenlik Merkezi önerilerini, varsa, bekleyen ve geçişte şifreleme için kullanın.

- [Azure ile iletim sırasında şifrelemeyi anlama](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Azure Otomasyonu TLS 1,2 zorlaması](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: bir üçüncü taraf etkin bulma aracı kullanarak, şirket içi veya uzak bir hizmet sağlayıcı gibi kuruluş teknoloji sistemleri tarafından depolanan, işlenen veya aktarılan tüm hassas bilgileri, kuruluşun hassas bilgi envanterini güncelleştirmek için kullanın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: kaynaklara erişimi denetlemek için rol tabanlı erişim denetimi kullanma

**Rehberlik**: yerleşik rol tanımlarını kullanarak Azure Otomasyonu kaynaklarına erişimi denetlemek için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanın, en az ayrıcalıklı veya ' tam ' erişim modelinden sonra Otomasyon kaynaklarınıza erişen kullanıcılar için erişim atayın. Karma runbook çalışanları kullanılırken, hizmet sorumlularını kullanmaktan kaçınmak için bu sanal makinelerin yönetilen kimliklerinden yararlanın, hem çok kiracılı hem de karma runbook çalışanları kullanılırken, runbook çalışanlarının kimliği üzerinde doğru kapsamlı Azure RBAC izinleri uyguladığınızdan emin olun.

- [Azure RBAC 'yi yapılandırma](../role-based-access-control/role-assignments-portal.md)

- [Karma Runbook Worker için Runbook izinleri](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

- [Rol izinlerini ve güvenliği yönetme](automation-role-based-access-control.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: erişim denetimini zorlamak için ana bilgisayar tabanlı veri kaybı önleme kullanın

**Kılavuz**: Azure Otomasyonu Şu anda temeldeki çok kiracılı Runbook Worker 'ın sanal makinelerini kullanıma sunmamaktadır ve bu platform tarafından işlenir. Karma runbook çalışanları olmadan kullanıma hazır hizmet hizmetini kullanıyorsanız bu denetim geçerli değildir.

Azure sanal makineler tarafından desteklenen karma runbook çalışanları kullanıyorsanız, barındırılan karma Runbook Worker sanal makinelerinize erişim denetimleri zorlamak için üçüncü taraf ana bilgisayar tabanlı bir veri kaybı önleme çözümü kullanmanız gerekir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Kılavuz**: Azure Otomasyonu ile müşteri tarafından yönetilen anahtarlar kullanın. Azure Otomasyonu, kimlik bilgileri, sertifikalar, bağlantılar ve şifreli değişkenler gibi kullanılan tüm ' güvenli varlıkları ' şifrelemek için müşteri tarafından yönetilen anahtarların kullanılmasını destekler. Tüm kalıcı değişken arama gereksinimleriniz için Runbook 'larınızla, istenmeyen pozlandırmayı engellemek için şifrelenen değişkenlerden yararlanın.

Karma runbook çalışanları kullanılırken, sanal makinelerdeki sanal diskler, sunucu tarafı şifreleme veya Azure disk şifrelemesi (ADE) kullanılarak geri kalanıyla şifrelenir. Azure disk şifrelemesi, yönetilen diskleri, Konuk VM içindeki müşteri tarafından yönetilen anahtarlarla şifrelemek için Windows 'un BitLocker özelliğinden yararlanır. Müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifreleme, depolama hizmetindeki verileri şifreleyerek VM 'niz için herhangi bir işletim sistemi türünü ve görüntüsünü kullanmanızı sağlayarak ADE 'yi geliştirir.

- [Azure yönetilen disklerinin sunucu tarafı şifrelemesi](../virtual-machines/disk-encryption.md)

- [Windows VM 'Leri için Azure disk şifrelemesi](../virtual-machines/windows/disk-encryption-overview.md)

- [Otomasyon hesabı için müşteri tarafından yönetilen anahtarların kullanımı](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Azure Otomasyonu 'nda yönetilen değişkenler](shared-resources/variables.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. Automation**:

[!INCLUDE [Resource Policy for Microsoft.Automation 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.automation-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Rehberlik**: ağ bileşenleri, Azure Otomasyonu hesapları ve Runbook 'lar gibi kritik Azure kaynaklarında değişiklik gerçekleşirken uyarı oluşturmak Için Azure izleyici Ile Azure etkinlik günlüğü 'nü kullanın. 

- [Ağ güvenlik grubu için tanılama günlüğü](../private-link/private-link-overview.md#logging-and-monitoring)

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: otomatikleştirilmiş güvenlik açığı tarama araçlarını çalıştırma

**Rehberlik**: Azure kaynaklarınız üzerinde güvenlik açığı değerlendirmesi gerçekleştirmeye yönelik Azure Güvenlik Merkezi önerilerini izleyin

- [Azure Güvenlik Merkezi'nde güvenlik önerileri](../security-center/security-center-recommendations.md)

- [Güvenlik Merkezi öneri başvurusu](../security-center/recommendations-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: geri dönüş güvenlik açığı taramalarını karşılaştırın

**Rehberlik**: tarama sonuçlarını tutarlı aralıklarla dışarı aktarın ve güvenlik açıklarının düzeltildiğini doğrulamak için sonuçları karşılaştırın. Azure Güvenlik Merkezi tarafından önerilen güvenlik açığı yönetimi önerisi kullanılırken, müşteri, geçmiş tarama verilerini görüntülemek için seçili çözümün portalına pivot olarak eklenebilir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek için risk derecelendirme işlemi kullanın

**Rehberlik**: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek Için Azure Güvenlik Merkezi tarafından sağlanan varsayılan risk derecelendirmelerini (güvenli puan) kullanın.

- [Azure Güvenlik Merkezi güvenli Puanını anlama](../security-center/secure-score-security-controls.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: aboneliklerinizde bulunan tüm Azure Otomasyonu kaynaklarını sorgulamak ve öğrenmek Için Azure Kaynak grafiğini kullanın. Kiracınızda uygun (okuma) izinleriniz olduğundan ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerinin listesini belirleyebildiğinizden emin olun.

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

**Rehberlik**: Azure Otomasyonu kaynaklarını düzenlemek ve izlemek için uygun yerlerde etiketleme, yönetim grupları ve ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun. Sunulan saldırı yüzeyini en aza indirmek için kullanılmayan farklı çalıştır hesaplarını silin.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Farklı Çalıştır veya Klasik Farklı Çalıştır hesabını silme](delete-run-as-account.md)

- [Azure Otomasyonu farklı çalıştır hesabını yönetme](manage-runas-account.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynaklarının envanterini tanımlayın ve saklayın

**Rehberlik**: Kurumsal gereksinimleriniz uyarınca, işlem kaynakları Için onaylanan Azure kaynakları ve onaylanan yazılım envanterini oluşturmanız gerekir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın: 

- İzin verilmeyen kaynak türleri 
- İzin verilen kaynak türleri 

Ayrıca, abonelikleri içindeki kaynakları sorgulamak/öğrenmek için Azure Kaynak grafiğini kullanın. Bu, depolama hesaplarıyla ilgili olanlar gibi yüksek güvenlik tabanlı ortamlarda yardımcı olabilir. 

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

- [Azure Otomasyonu için Azure Ilke örneği yerleşik bileşenleri](policy-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Rehberlik**: müşteri, Azure ilkesiyle müşterinin Şirket yönergelerine göre gerekli olduğu şekilde kaynak oluşturulmasını veya kullanımını engelleyebilir. Yetkisiz kaynakları kaldırmak için kendi işleminizi uygulayabilirsiniz. Azure Otomasyonu çerçevesinde, PowerShell 'i yüklemek, kaldırmak ve yönetmek ya da runbook 'ların portal veya cmdlet 'ler aracılığıyla erişebileceği Python modülleri olabilir. Onaylanmamış veya eski modülün runbook 'lar için kaldırılması veya güncellenmesi gerekir.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Otomasyonu 'nda modülü yönetme](shared-resources/modules.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:
- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](../governance/policy/samples/built-in-policies.md#general)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Kılavuz**: güvenli olmayan veya onaylanmamış konumlardan veya cihazlardan "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" seçeneğini yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak Için Azure koşullu erişim ilkelerini kullanın. 

- [Azure Resource Manager erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Kılavuz**: Azure Otomasyonu ve ilgili kaynaklarınızın yapılandırmasını denetlemek veya zorlamak için özel ilkeler oluşturmak üzere Azure ilkesi diğer adlarını kullanın. Yerleşik Azure Ilke tanımlarını da kullanabilirsiniz.

Ayrıca, Azure Resource Manager, yapılandırmanın kuruluşunuzun güvenlik gereksinimlerini karşıladığından/aştığından emin olmak için gözden geçirilmesi gereken JavaScript Nesne Gösterimi (JSON) içinde şablonu dışarı aktarma özelliğine sahiptir.

Azure Güvenlik Merkezi 'nin önerilerini Azure kaynaklarınız için güvenli bir yapılandırma temeli olarak da kullanabilirsiniz.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias)

- [Öğretici: uyumluluğu zorlamak için ilke oluşturma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Otomasyonu için Azure Ilke örneği yerleşik bileşenleri](policy-reference.md)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Güvenlik önerileri - başvuru kılavuzu](../security-center/recommendations-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Kılavuz**: Azure Otomasyonu Ile ilişkili Azure kaynaklarını güvenli şekilde yapılandırmak için Azure Resource Manager şablonlarını ve Azure ilkesini kullanın. Azure Resource Manager şablonlar, Azure kaynaklarını dağıtmak için kullanılan JSON tabanlı dosyalardır ve özel şablonların bir kod deposunda güvenli bir şekilde depolanması ve saklanması gerekir. Kaynak denetimi deponuzdaki betiklerle Otomasyon hesabınızda runbook 'larınızı güncel tutmak için kaynak denetimi tümleştirme özelliğini kullanın. Azure kaynaklarınızın tamamında güvenli ayarları zorlamak için Azure Ilkesi [reddetme] ve [dağıtım yoksa dağıt] kullanın.

- [Kaynak denetimi tümleştirmesini kullanma](source-control-integration.md)

- [Azure Resource Manager şablonları oluşturma hakkında bilgi](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) 

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

- [Azure Resource Manager şablonu kullanarak Otomasyon hesabı dağıtma](/azure/automation/quickstart-create-automation-account-template)

- [Azure Otomasyonu için Azure Ilke örneği yerleşik bileşenleri](policy-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilkeleri, Azure Resource Manager şablonları ve Istenen durum yapılandırması betikleri gibi kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure DevOps kullanın. Azure DevOps 'da yönettiğiniz kaynaklara erişmek için, Azure DevOps ile tümleşikse veya TFS ile tümleşikse Active Directory belirli kullanıcılara, yerleşik güvenlik gruplarına veya Azure Active Directory (Azure AD) tanımlanmış gruplara izin verebilir veya vermeyebilirsiniz. Kaynak denetimi deponuzdaki betiklerle Otomasyon hesabınızda runbook 'larınızı güncel tutmak için kaynak denetimi tümleştirme özelliğini kullanın.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps 'da izinler ve gruplar hakkında](/azure/devops/organizations/security/about-permissions)

- [Kaynak denetimi tümleştirmesini kullanma](source-control-integration.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Kılavuz**: Azure Ilkesini kullanarak Azure kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın. Azure kaynaklarınızın ağ yapılandırmasını denetlemek veya zorlamak için özel ilkeler oluşturmak üzere Azure Ilke diğer adlarını kullanın. Ayrıca, belirli kaynaklarınızla ilgili yerleşik ilke tanımlarından da yararlanabilirsiniz. 

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Diğer adları kullanma](../governance/policy/concepts/definition-structure.md#aliases)

- [Azure Otomasyonu için Azure Ilke örneği yerleşik bileşenleri](policy-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Kılavuz**: Azure ilkesi 'Ni kullanarak Azure Kaynak konfigürasyonları 'nı uyarma ve denetleme, ilke özel bir uç noktayla yapılandırılmayan belirli bir kaynağı algılamak için kullanılabilir.

Karma Runbook Worker özelliğini kullanırken Azure sanal makineleriniz için temel taramalar gerçekleştirmek üzere Azure Güvenlik Merkezi 'Nden yararlanın.  Otomatik yapılandırma için ek yöntemler şunlardır: Azure Otomasyonu durum yapılandırması.

- [Azure Güvenlik Merkezi 'nde öneriler nasıl düzeltileceği](../security-center/security-center-remediate-recommendations.md)

- [Azure Otomasyonu durum yapılandırması ile çalışmaya başlama](automation-dsc-getting-started.md)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Otomasyonu için Azure Ilke örneği yerleşik bileşenleri](policy-reference.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: kod içinde kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayın. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar. 

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli otomatik arka pencerelere emin olun

**Kılavuz**: Azure Otomasyonu hesaplarını ve ilgili kaynakları dağıtmak için Azure Resource Manager kullanın. Azure Resource Manager, Azure Otomasyonu hesaplarını ve ilgili kaynakları geri yüklemek için yedeklemeler olarak kullanılabilecek şablonları dışarı aktarma olanağı sağlar. Azure Resource Manager şablonu dışarı aktarma API 'sini düzenli aralıklarla çağırmak için Azure Otomasyonu 'nu kullanın.

Kaynak denetimi deponuzdaki betiklerle Otomasyon hesabınızda runbook 'larınızı güncel tutmak için kaynak denetimi tümleştirme özelliğini kullanın.

- [Azure Resource Manager'a genel bakış](../azure-resource-manager/management/overview.md)

- [Azure Otomasyonu kaynakları için Azure Resource Manager şablonu başvurusu](/azure/templates/microsoft.automation/allversions)

- [Azure Resource Manager şablonu kullanarak Otomasyon hesabı oluşturma](quickstart-create-automation-account-template.md)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Kaynak grupları-şablonu dışarı aktarma](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Otomasyonu 'na giriş](automation-intro.md)

- [Azure 'da Anahtar Kasası anahtarlarını yedekleme](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Otomasyon hesabı için müşteri tarafından yönetilen anahtarların kullanımı](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Kaynak denetimi tümleştirmesini kullanma](source-control-integration.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Kılavuz**: Azure Otomasyonu hesaplarını ve ilgili kaynakları dağıtmak için Azure Resource Manager kullanın. Azure Resource Manager, Azure Otomasyonu hesaplarını ve ilgili kaynakları geri yüklemek için yedeklemeler olarak kullanılabilecek şablonları dışarı aktarma olanağı sağlar. Azure Resource Manager şablonu dışarı aktarma API 'sini düzenli aralıklarla çağırmak için Azure Otomasyonu 'nu kullanın. Azure Key Vault içinde müşteri tarafından yönetilen anahtarları yedekleyin. Runbook 'larınızı, Azure portal veya PowerShell kullanarak betik dosyalarına aktarabilirsiniz.

- [Azure Resource Manager'a genel bakış](../azure-resource-manager/management/overview.md)

- [Azure Otomasyonu kaynakları için Azure Resource Manager şablonu başvurusu](/azure/templates/microsoft.automation/allversions)

- [Azure Resource Manager şablonu kullanarak Otomasyon hesabı oluşturma](quickstart-create-automation-account-template.md)

- [Azure portal bir şablona tek ve çoklu kaynak verme](../azure-resource-manager/templates/export-template-portal.md)

- [Kaynak grupları-şablonu dışarı aktarma](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Otomasyonu 'na giriş](automation-intro.md)

- [Azure 'da Anahtar Kasası anahtarlarını yedekleme](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Otomasyon hesabı için müşteri tarafından yönetilen anahtarların kullanımı](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Otomasyon hesapları için Azure veri yedeklemesi](./automation-managing-data.md#data-backup)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: gerektiğinde yalıtılmış bir aboneliğe düzenli olarak Azure Resource Manager şablonlarının dağıtımını düzenli olarak gerçekleştirdiğinizden emin olun. Yedeklenen müşteri tarafından yönetilen anahtarların test geri yüklemesi.

- [ARM şablonları ve Azure portal kaynak dağıtma](../azure-resource-manager/templates/deploy-portal.md)

- [Azure 'da Anahtar Kasası anahtarlarını geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Otomasyon hesabı için müşteri tarafından yönetilen anahtarların kullanımı](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: kodunuzu Azure Resource Manager şablonlar gibi güvenli bir şekilde depolamak ve yönetmek Için Azure DevOps kullanın. Azure DevOps 'da yönettiğiniz kaynakları korumak için, belirli kullanıcılara, yerleşik güvenlik gruplarına veya Azure DevOps ile tümleşikse veya TFS ile tümleşikse Active Directory Azure Active Directory (Azure AD) içinde tanımlanan gruplara izin verebilir veya vermeyebilirsiniz.

Kaynak denetimi deponuzdaki betiklerle Otomasyon hesabınızda runbook 'larınızı güncel tutmak için kaynak denetimi tümleştirme özelliğini kullanın.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps 'da izinler ve gruplar hakkında](/azure/devops/organizations/security/about-permissions)

- [Kaynak denetimi tümleştirmesini kullanma](source-control-integration.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: Kuruluşunuz için bir olay yanıt kılavuzu oluşturun. Tüm personelin rollerine ek olarak algılama aşamasından olay sonrası gözden geçirme aşamasına kadar tüm olay işleme/yönetim aşamalarını tanımlayan yazılı olay yanıt planları bulunduğundan emin olun.

- [Kendi güvenlik olay yanıtı işleminizi oluşturma kılavuzu](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Güvenlik Yanıt Merkezi 'nin bir olayın anatomisi](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Müşteri, kendi olay yanıt planının oluşturulmasına yardımcı olması için NıST 'nin bilgisayar güvenliği olay Işleme kılavuzunu da kullanabilir](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Rehberlik**: Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizte ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır. 

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

**Rehberlik**: sızma testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak Için Microsoft katılım kurallarını izleyin. Microsoft tarafından yönetilen bulut altyapısına, hizmetlere ve uygulamalara yönelik kırmızı takım ve canlı site sızma testi gerçekleştirmek için Microsoft'un stratejisini ve yürütme sürecini kullanın.

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](../security/benchmarks/overview.md)
- [Azure güvenlik temelleri](../security/benchmarks/security-baselines-overview.md) hakkında daha fazla bilgi edinin
