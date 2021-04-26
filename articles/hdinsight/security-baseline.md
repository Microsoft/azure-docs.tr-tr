---
title: HDInsight için Azure Güvenlik temeli
description: HDInsight güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c5ffdecf768be0962950bb3691dbb11fb0e70120
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565019"
---
# <a name="azure-security-baseline-for-hdinsight"></a>HDInsight için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 1,0](../security/benchmarks/overview-v1.md) ' den HDInsight 'a kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar.
İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve HDInsight için geçerli olan ilgili kılavuza göre gruplandırılır. HDInsight için geçerli olmayan **denetimler** dışlandı.

 
HDInsight 'ın Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam HDInsight güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: Azure HDInsight 'ta çevre güvenliği sanal ağlar aracılığıyla elde edilir. Kurumsal Yönetici, sanal ağ içinde bir küme oluşturabilir ve sanal ağa erişimi kısıtlamak için bir ağ güvenlik grubu (NSG) kullanabilir. Yalnızca gelen ağ güvenlik grubu kurallarında izin verilen IP adresleri Azure HDInsight kümesiyle iletişim kurabiliyor. Bu yapılandırma, çevre güvenliği sağlar. Bir sanal ağda dağıtılan tüm kümelerin, küme ağ geçitlerine özel HTTP erişimi için sanal ağ içindeki özel bir IP adresine çözümlenen özel bir uç noktası da olur.

Veri kaybı riskini azaltmak için Azure Güvenlik Duvarı 'nı kullanarak Azure HDInsight kümeleri için giden ağ trafiğini kısıtlayın.

- [Azure HDInsight 'ı bir sanal ağ içinde dağıtma ve bir ağ güvenlik grubuyla güvenli hale getirme](hdinsight-create-virtual-network.md)

- [Azure Güvenlik Duvarı ile Azure HDInsight kümeleri için giden trafiği kısıtlama](hdinsight-restrict-outbound-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: sanal ağların, alt ağların ve ağ arabirimlerinin yapılandırma ve trafiğini izleme ve günlüğe kaydetme

**Kılavuz**: Azure Güvenlik Merkezi 'ni kullanın ve Azure HDInsight kümenizin güvenliğini sağlamak için kullanılan sanal ağ, alt ağ ve ağ güvenlik grubu için ağ koruma önerilerini düzeltin. Ağ güvenlik grubu (NSG) akış günlüklerini etkinleştirin ve günlükleri bir Azure depolama hesabına trafik denetimine gönderin. Ayrıca Azure Log Analytics çalışma alanına NSG akış günlükleri gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Azure Trafik Analizi kullanabilirsiniz. Azure Trafik Analizi 'nin bazı avantajları, ağ etkinliğini görselleştirme ve etkin noktaları tanımlayabilir, güvenlik tehditlerini belirleyebilir, trafik akışı düzenlerini anlayabilir ve ağ ile hatalı yapılandırmaların nasıl belirlenmesine olanak sağlar.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Kılavuz**: DDoS saldırılarına karşı korumalar Için Azure HDInsight 'ın dağıtıldığı sanal ağ üzerinde Azure DDoS standart korumasını etkinleştirin. Bilinen kötü amaçlı veya kullanılmayan Internet IP adresleriyle iletişimleri reddetmek için Azure Güvenlik Merkezi tümleşik tehdit zekasını kullanın.

- [DDoS korumasını yapılandırma](../ddos-protection/manage-ddos-protection.md)

- [Azure Güvenlik Merkezi tümleşik tehdit zekasını anlama](../security-center/azure-defender.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Kılavuz**: Azure HDInsight kümenizi korumak için kullanılan alt ağa eklenen NSG için ağ güvenlik grubu (NSG) akış günlüklerini etkinleştirin. Flow kayıtları oluşturmak için NSG akış günlüklerini bir Azure depolama hesabına kaydedin. Anormal etkinlikleri araştırmak için gerekliyse, Azure ağ Izleyicisi paket yakalamayı etkinleştirin.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Ağ İzleyicisini etkinleştirme](../network-watcher/network-watcher-create.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Rehberlik**: gereksinim, Azure GÜVENLIK denetim kimliği 1,1; karşılandı. Azure HDInsight kümesini bir sanal ağa dağıtın ve bir ağ güvenlik grubu (NSG) ile güvenli hale getirin.

Azure HDInsight için gelen trafik gerektiren birkaç bağımlılık vardır. Gelen yönetim trafiği bir güvenlik duvarı cihazından gönderilemez. Gerekli yönetim trafiğine yönelik kaynak adresleri bilinirler ve yayımlanır. Yalnızca güvenilen konumlardan gelen trafiğin güvenliğini sağlamak için bu bilgilerle ağ güvenlik grubu kuralları oluşturun.

Veri kaybı riskini azaltmak için Azure Güvenlik Duvarı 'nı kullanarak Azure HDInsight kümeleri için giden ağ trafiğini kısıtlayın.

- [Bir sanal ağ içinde HDInsight dağıtma ve bir ağ güvenlik grubuyla güvenli hale getirme](hdinsight-create-virtual-network.md)

- [HDInsight bağımlılıklarını ve Güvenlik Duvarı kullanımını anlama](hdinsight-restrict-outbound-traffic.md)

- [HDInsight yönetim IP adresleri](hdinsight-management-ip-addresses.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Kılavuz**: Azure HDInsight kümenizin dağıtıldığı alt ağa bağlı ağ güvenlik grupları (NSG) üzerinde ağ erişim denetimleri tanımlamak için sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adı (örn., Apimanaya) belirterek, ilgili hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

- [Azure HDInsight için hizmet etiketlerini anlama ve kullanma](../virtual-network/network-security-groups-overview.md#service-tags)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Kılavuz**: Azure HDInsight kümeniz ile ilgili ağ kaynakları için standart güvenlik yapılandırması tanımlayın ve uygulayın. Azure HDInsight kümenizin ağ yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için "Microsoft. HDInsight" ve "Microsoft. Network" ad alanlarında Azure Ilke diğer adlarını kullanın.

Ayrıca, tek bir şema tanımında Azure Resource Manager şablonları, Azure RBAC denetimleri ve ilkeleri gibi temel ortam yapıtlarını paketleyerek büyük ölçekli Azure dağıtımlarını basitleştirmek için Azure şemaları 'nı kullanabilirsiniz. Şema 'i yeni abonelikler ve ortamlara kolayca uygulayın ve sürüm oluşturma aracılığıyla denetimi ve yönetimi ayrıntılı olarak ayarlayın.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: ağ güvenlik grubu (NSG) ve Azure HDInsight kümeniz ile ilişkili ağ güvenliği ve trafik akışıyla ilgili diğer kaynaklar için Etiketler kullanın. Bireysel NSG kuralları için "Açıklama" alanını kullanarak ağa giden/giden trafiğe izin veren kuralların iş gereksinimini ve/veya süresini (vs.) belirtin.

Tüm kaynakların etiketlerle oluşturulmasını ve mevcut etiketlenmemiş kaynakları bilgilendirmesini sağlamak için etiketlemeyle ilgili yerleşik Azure Ilke tanımlarından herhangi birini ("etiket ve onun değeri gerektir" gibi) kullanın.

Azure PowerShell veya Azure komut satırı arabirimi 'ni (CLı) kullanarak, etiketlerine göre kaynakları arayabilir veya bunlarla ilgili eylemler gerçekleştirebilirsiniz.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Rehberlik**: Azure etkinlik günlüğü 'nü kullanarak ağ kaynak yapılandırmasını Izleyin ve Azure HDInsight dağıtımlarınızla ilgili ağ kaynaklarına yönelik değişiklikleri tespit edin. Kritik ağ kaynaklarında yapılan değişiklikler yürürlüğe girdiğinde tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: küme tarafından oluşturulan güvenlik verilerini toplamak Için Azure HDInsight kümenizi Azure izleyici 'ye ekleyebilirsiniz. Ortamdaki tehditleri algılamak ve yanıtlamak için özel sorgulardan yararlanın. 

- [Azure Izleyici 'ye Azure HDInsight kümesi ekleme](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Azure HDInsight kümesi için özel sorgular oluşturma](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: HDInsight kümesi Için Azure izleyicisini etkinleştirin ve bunu bir Log Analytics çalışma alanına yönlendirin. Bu, tüm Azure HDInsight küme düğümleri için ilgili küme bilgilerini ve işletim sistemi ölçümlerini günlüğe kaydeder.

- [HDInsight kümesi için günlüğe kaydetmeyi etkinleştirme](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [HDInsight günlüklerini sorgulama](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: işletim sistemlerinden güvenlik günlüklerini toplama

**Kılavuz**: Azure HDInsight kümesini Azure izleyici 'ye ekleme. Kullanılan Log Analytics çalışma alanının, kuruluşunuzun uyumluluk düzenlemelerine göre ayarlanmış günlük tutma süresine sahip olduğundan emin olun.

- [Azure Izleyici 'ye Azure HDInsight kümesi ekleme](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Log Analytics çalışma alanı saklama süresini yapılandırma](../azure-monitor/logs/manage-cost-storage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Kılavuz**: Azure HDInsight kümesini Azure izleyici 'ye ekleme. Kullanılan Azure Log Analytics çalışma alanının, kuruluşunuzun uyumluluk düzenlemelerine göre ayarlanmış günlük tutma süresine sahip olduğundan emin olun.

- [Azure Izleyici 'ye Azure HDInsight kümesi ekleme](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Log Analytics çalışma alanı saklama süresini yapılandırma](../azure-monitor/logs/manage-cost-storage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Kılavuz**: Azure HDInsight günlüklerini sorgulamak için Azure Log Analytics çalışma alanı sorgularını kullanın:

- [Azure HDInsight kümeleri için özel sorgular oluşturma](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Rehberlik**: Azure Log Analytics çalışma alanını, Güvenlik günlüklerine ve Azure HDInsight kümeniz ile ilgili olaylarda bulunan anormal etkinlikleri izlemek ve uyarmak için kullanın.

- [Azure Güvenlik Merkezi 'nde uyarıları yönetme](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics günlük verilerinde uyarı alma](../azure-monitor/alerts/tutorial-response.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="28-centralize-anti-malware-logging"></a>2,8: kötü amaçlı yazılımdan koruma 'yı merkezileştirme

**Kılavuz**: Azure HDInsight, küme düğümü görüntüleri için önceden yüklenmiş ve etkin olan Azure HDInsight ile birlikte gelir, ancak yazılımı yönetmeniz ve ClamScan 'ın ürettiği tüm günlükleri el ile toplamanız/izlemeniz gerekir.

- [ClamScan 'ı anlama](./hdinsight-faq.md#security-and-certificates)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="29-enable-dns-query-logging"></a>2,9: DNS sorgu günlüğünü etkinleştir

**Rehberlik**: DNS günlüğü için üçüncü taraf çözümü uygulayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="210-enable-command-line-audit-logging"></a>2,10: komut satırı denetim günlüğünü etkinleştir

**Rehberlik**: konsol günlüğünü her düğüm temelinde el ile yapılandırın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Kılavuz**: Azure HDInsight kümesinin küme sağlanması sırasında oluşturulan yerel yönetim hesabının kaydını ve oluşturduğunuz diğer hesapları saklayın. Ayrıca, Azure Active Directory (Azure AD) Tümleştirmesi kullanılırsa, Azure AD 'nin açıkça atanması ve bu nedenle sorgulanabilir olması gereken yerleşik rolleri vardır. Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü kullanın.

Ayrıca, Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini de kullanabilirsiniz.

- [Azure AD 'de PowerShell ile dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure Güvenlik Merkezi ile kimlik ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Rehberlik**: bir küme sağlanırken Azure, Web portalı ve Secure Shell (SSH) erişimi için yeni parolalar oluşturmanızı gerektirir. Değiştirilecek varsayılan parola yoktur, ancak SSH ve Web portalı erişimi için farklı parolalar belirtebilirsiniz.

- [Azure HDInsight kümesi sağlanırken parolaları ayarlama](hdinsight-hadoop-linux-use-ssh-unix.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: Azure Active Directory (Azure AD) Ile Azure HDInsight kümesi Için kimlik doğrulamasını tümleştirin. Adanmış yönetim hesaplarının kullanımı etrafında ilke ve yordamlar oluşturun.

Ayrıca, Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini de kullanabilirsiniz.

- [Azure HDInsight kimlik doğrulamasını Azure AD ile tümleştirme](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Azure Güvenlik Merkezi ile kimlik ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory çoklu oturum açma kullan (SSO)

**Kılavuz**: çok faktörlü kimlik doğrulaması kullanarak herhangi bir parola sağlamadan kurumsal GÜVENLIK PAKETI (ESP) kümelerinde oturum açmak Için Azure HDInsight kimlik Aracısı 'nı kullanın. Azure portal gibi diğer Azure hizmetlerinde zaten oturum açtıysanız, Azure HDInsight kümenizde çoklu oturum açma (SSO) deneyimiyle oturum açabilirsiniz.

- [Azure HDInsight KIMLIK Aracısı 'nı etkinleştirme](./domain-joined/identity-broker.md#enable-hdinsight-id-broker)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin ve Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini izleyin. Yapılandırılmış Kurumsal Güvenlik Paketi olan Azure HDInsight kümeleri, etki alanı kullanıcılarının, kümelerle kimlik doğrulamak ve büyük veri işleri çalıştırmak için kendi etki alanı kimlik bilgilerini kullanabilmesi için bir etki alanına bağlı olabilir. Çok faktörlü Authentication etkinken kimlik doğrulanırken, kullanıcılardan ikinci bir kimlik doğrulama faktörü sağlaması istenir.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: tüm yönetim görevleri için adanmış makineler (ayrıcalıklı erişim Iş Istasyonları) kullanın

**Kılavuz**: Azure HDInsight kümelerinizi ve ilgili kaynaklarınızı oturum açmak ve yapılandırmak için yapılandırılmış çok faktörlü kimlik doğrulaması Ile Paws (ayrıcalıklı erişim iş istasyonları) kullanın.

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: etki alanı kullanıcılarının kimlik doğrulaması için etki alanı kimlik bilgilerini kullanabilmesi için, yapılandırılmış kurumsal güvenlik paketi olan Azure HDInsight kümeleri bir etki alanına bağlanabilir. Azure AD ortamında şüpheli veya güvenli olmayan bir etkinlik gerçekleştiğinde günlüklerin ve uyarıların oluşturulması için Azure Active Directory (Azure AD) güvenlik raporları kullanabilirsiniz. Kimlik ve erişim etkinliğini izlemek için Azure Güvenlik Merkezi 'ni kullanın.

- [Riskli etkinlik bayrağıyla işaretlenen Azure AD kullanıcılarını belirleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Güvenlik Merkezi 'nde Kullanıcı kimliğini ve erişim etkinliğini izleme](../security-center/security-center-identity-access.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: etki alanı kullanıcılarının kimlik doğrulaması için etki alanı kimlik bilgilerini kullanabilmesi için, yapılandırılmış kurumsal güvenlik paketi olan Azure HDInsight kümeleri bir etki alanına bağlanabilir. IP adresi aralıklarının veya ülkelerin/bölgelerin yalnızca belirli mantıksal gruplarından erişime izin vermek için adlandırılmış konumlarda koşullu erişim kullanın.

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: merkezi kimlik doğrulama ve yetkilendirme sistemi olarak Azure Active Directory (Azure AD) kullanın. Azure AD, bekleyen ve aktarım sırasında veriler için güçlü şifrelemeyi kullanarak verileri korur. Azure AD Ayrıca, karma ve Kullanıcı kimlik bilgilerini güvenli bir şekilde depolar.

Kurumsal Güvenlik Paketi (ESP) ile yapılandırılmış Azure HDInsight kümeleri, etki alanı kullanıcılarının, kümelerle kimlik doğrulaması yapmak için etki alanı kimlik bilgilerini kullanabilmesi için bir etki alanına bağlanabilir.

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure HDInsight 'ta Azure AD Domain Services ile Kurumsal Güvenlik Paketi yapılandırma](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Kılavuz**: Azure hdınsight kümenizdeki Azure Active Directory (Azure AD) kimlik doğrulamasını kullanın. Azure AD, eski hesapların keşfedilmesine yardımcı olmak için Günlükler sağlar. Ayrıca, grup üyeliklerini etkin bir şekilde yönetmek, kurumsal uygulamalara erişmek ve rol atamaları için Azure kimlik erişimi Incelemelerini kullanın. Kullanıcının erişimi, yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için düzenli olarak incelenebilir.

- [Azure kimlik erişimi Incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: devre dışı bırakılmış hesaplara erişme girişimlerini izlemek için Azure Active Directory (Azure AD) oturum açma ve denetim günlüklerini kullanın; Bu Günlükler, herhangi bir üçüncü taraf SıEM/izleme aracıyla tümleştirilebilir.

Azure AD Kullanıcı hesapları için Tanılama ayarları oluşturarak bu işlemi kolaylaştırarak Azure Log Analytics çalışma alanına denetim günlüklerini ve oturum açma günlüklerini gönderebilirsiniz. Azure Log Analytics çalışma alanında istenen uyarıları yapılandırın.

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapması üzerinde uyarı

**Kılavuz**: Kurumsal GÜVENLIK PAKETI (ESP) ile yapılandırılmış Azure HDInsight kümeleri, etki alanı kullanıcılarının, kümelerle kimlik doğrulamak için etki alanı kimlik bilgilerini kullanabilmesi için bir etki alanına bağlanabilir. Otomatik yanıtları, Kullanıcı kimlikleriyle ilgili şüpheli eylemler için yapılandırmak üzere Azure Active Directory (Azure AD) risk algılamaları ve kimlik koruması özelliğini kullanın. Ayrıca, daha fazla araştırma için verileri Azure Sentinel 'e aktarabilirsiniz.

- [Azure AD riskli oturum açma işlemlerini görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: destek senaryoları sırasında Microsoft 'un ilgili müşteri verilerine erişimini sağlama

**Rehberlik**: kullanılamıyor; Müşteri Kasası henüz Azure HDInsight için desteklenmiyor.

- [Desteklenen Müşteri Kasası hizmetleri listesi](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: hassas bilgileri depolayan veya işleyen Azure kaynaklarını izlemeye yardımcı olması Için Azure HDInsight dağıtımlarınızla ilgili kaynaklarda yer alan etiketler kullanın.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: geliştirme, test ve üretim için ayrı abonelikler ve/veya yönetim grupları uygulayın. Azure HDInsight kümeleri ve ilişkili tüm depolama hesapları, sanal ağ/alt ağ ile ayrılmalıdır, uygun şekilde etiketlenebilir ve bir ağ güvenlik grubu (NSG) veya Azure Güvenlik duvarı içinde güvenliği sağlanmış olmalıdır. Küme verileri, güvenli bir Azure depolama hesabı veya Azure Data Lake Storage (Gen1 veya Gen2) içinde bulunmalıdır.

- [Azure HDInsight kümeniz için depolama seçeneklerini belirleyin](hdinsight-hadoop-compare-storage-options.md)

- [Azure Data Lake Storage nasıl güvence altına alın](../data-lake-store/data-lake-store-security-overview.md)

- [Azure depolama hesaplarını güvenli hale getirme](../storage/blobs/security-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: hassas bilgileri depolayan veya Işleyen Azure HDInsight kümeleri için, etiketleri kullanarak kümeyi ve ilgili kaynakları hassas olarak işaretleyin. Veri kaybı riskini azaltmak için Azure Güvenlik Duvarı 'nı kullanarak Azure HDInsight kümeleri için giden ağ trafiğini kısıtlayın.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure Güvenlik Duvarı ile Azure HDInsight kümeleri için giden trafiği kısıtlama](hdinsight-restrict-outbound-traffic.md)

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: yoldaki tüm hassas bilgileri şifreleyin

**Rehberlik**: yoldaki tüm hassas bilgileri şifreleyin. Azure HDInsight kümenize veya küme veri Depolarınıza (Azure Storage hesapları veya Azure Data Lake Storage 1./Gen2) bağlanan tüm istemcilerin TLS 1,2 veya üzerini anlaşamadığından emin olun. Microsoft Azure kaynaklar varsayılan olarak TLS 1,2 ' i uzlaşacaktır. 

- [İletimde Azure Data Lake Storage şifrelemeyi anlama](../data-lake-store/data-lake-store-security-overview.md)

- [Yoldaki Azure depolama hesabı şifrelemesini anlama](../storage/blobs/security-recommendations.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Rehberlik**: veri tanımlama, sınıflandırma ve kayıp önleme özellikleri, Azure depolama veya işlem kaynakları için henüz kullanılamıyor. Uyumluluk amacıyla gerekliyse üçüncü taraf çözümünü uygulayın.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: kaynaklara erişimi denetlemek için Azure RBAC kullanma 

**Kılavuz**: Azure HDInsight kurumsal GÜVENLIK PAKETI (ESP) ile, dosyalarda, klasörlerde, veritabanlarında, tablolarda ve satırlarda/sütunlarda depolanan verilerinize ilişkin ayrıntılı erişim denetimi ve veri gizleme ilkeleri oluşturmak ve yönetmek Için Apache Ranger 'ı kullanabilirsiniz. Hadoop Yöneticisi, Apache Ranger 'daki eklentileri kullanarak Apache Hive, HBase, Kafka ve Spark 'ı güvenli hale getirmek için rol tabanlı erişim denetimi 'ni (RBAC) yapılandırabilir.

RBAC ilkelerini Apache Ranger ile yapılandırmak, izinleri kuruluştaki bir rolle ilişkilendirmenize olanak sağlar. Bu soyutlama katmanı, kişilerin yalnızca iş sorumluluklarını gerçekleştirmek için gerekli izinlere sahip olmasını kolaylaştırır.

- [HDInsight 'ta Azure Active Directory (Azure AD) etki alanı Hizmetleri Kurumsal Güvenlik Paketi yapılandırma](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Azure HDInsight 'ta Kurumsal güvenliğe genel bakış](domain-joined/hdinsight-security-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: erişim denetimini zorlamak için ana bilgisayar tabanlı veri kaybı önleme kullanın

**Rehberlik**: hassas bilgileri depolayan veya Işleyen Azure HDInsight kümeleri için, etiketleri kullanarak kümeyi ve ilgili kaynakları hassas olarak işaretleyin. Veri tanımlama, sınıflandırma ve kayıp önleme özellikleri, Azure depolama veya işlem kaynakları için henüz kullanılamıyor. Uyumluluk amacıyla gerekliyse üçüncü taraf çözümünü uygulayın.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Rehberlik**: Apache Hive ve Apache Oozie meta verilerini depolamak IÇIN Azure SQL veritabanı KULLANıYORSANıZ, SQL verilerinin her zaman şifrelenmeye devam ettiğinden emin olun. Azure depolama hesapları ve Data Lake Storage (Gen1 veya Gen2) için, Microsoft 'un şifreleme anahtarlarınızı yönetmesine izin vermeniz önerilir, ancak kendi anahtarlarınızı yönetme seçeneğiniz vardır.

- [Azure depolama hesapları için şifreleme anahtarlarını yönetme](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Müşteri tarafından yönetilen şifreleme anahtarlarını kullanarak Azure Data Lake Storage oluşturma](../data-lake-store/data-lake-store-get-started-portal.md)

- [Azure SQL veritabanı şifrelemesini anlama](../azure-sql/database/sql-database-paas-overview.md#data-encryption)

- [Müşteri tarafından yönetilen anahtarları kullanarak SQL veritabanı için Saydam Veri Şifrelemesi yapılandırma](../azure-sql/database/transparent-data-encryption-tde-overview.md?tabs=azure-portal)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Rehberlik**: küme verilerine KARŞı tüm CRUD işlemlerini izlemek ve günlüğe kaydetmek Için Azure HDInsight kümeleriyle Ilişkili Azure depolama hesapları Için tanılama ayarlarını yapılandırın. Azure HDInsight kümesiyle ilişkili tüm depolama hesapları veya Data Lake depoları için denetimi etkinleştirin.

- [Azure depolama hesabı için ek günlüğe kaydetme/denetim özelliğini etkinleştirme](../storage/common/manage-storage-analytics-logs.md)

- [Azure Data Lake Storage için ek günlüğe kaydetme/denetim nasıl etkinleştirilir](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: otomatikleştirilmiş güvenlik açığı tarama araçlarını çalıştırma

**Rehberlik**: üçüncü taraf bir güvenlik açığı yönetim çözümü uygulayın.

İsteğe bağlı olarak, Rapid7, Qualys veya başka herhangi bir güvenlik açığı yönetim platformu aboneliğiniz varsa, Azure HDInsight küme düğümlerine güvenlik açığı değerlendirmesi aracılarını yüklemek ve ilgili Portal üzerinden düğümleri yönetmek için betik eylemleri ' ni kullanabilirsiniz.

- [Rapid7 aracısını el Ile yüklemek](https://insightvm.help.rapid7.com/docs/install)

- [Qualys aracısını el Ile nasıl yüklenir](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

- [Betik eylemlerini kullanma](hdinsight-hadoop-customize-cluster-linux.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: otomatik işletim sistemi düzeltme eki yönetimi çözümünü dağıtma

**Kılavuz**: otomatik sistem güncelleştirmeleri küme düğümü görüntüleri için etkinleştirildi, ancak güncelleştirmelerin uygulandığından emin olmak için düzenli aralıklarla küme düğümlerini yeniden başlatmanız gerekir.

Temel Azure HDInsight düğüm görüntülerini sürdürmek ve güncelleştirmek için Microsoft.

- [HDInsight kümeleri için işletim sistemi düzeltme eki uygulama zamanlamasını yapılandırma](hdinsight-os-patching.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: üçüncü taraf yazılım başlıkları için otomatik düzeltme eki yönetimi çözümünü dağıtma

**Kılavuz**: Azure HDInsight kümelerinizi yama için betik eylemlerini veya diğer mekanizmaları kullanın. Yeni oluşturulan kümeler en son güvenlik düzeltme ekleri dahil olmak üzere her zaman en son kullanılabilir güncelleştirmelere sahip olur.

- [Linux tabanlı Azure HDInsight kümeleri için işletim sistemi düzeltme eki uygulama zamanlamasını yapılandırma](hdinsight-os-patching.md)

- [Betik eylemlerini kullanma](hdinsight-hadoop-customize-cluster-linux.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: geri dönüş güvenlik açığı taramalarını karşılaştırın

**Rehberlik**: zaman içinde güvenlik açığı taramalarının karşılaştırılabilmesi için bir üçüncü taraf güvenlik açığı yönetimi çözümü uygulayın. Rapid7 veya Qualys aboneliğiniz varsa, geri dönüş güvenlik açıklarını görüntülemek ve karşılaştırmak için bu satıcının portalını kullanabilirsiniz.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek için risk derecelendirme işlemi kullanın

**Rehberlik**: ortak bir risk Puanlama programı (örneğin, ortak güvenlik açığı Puanlama sistemi) veya üçüncü taraf tarama aracınız tarafından sunulan varsayılan risk derecelendirmelerini kullanın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: Azure Kaynak Grafiği 'ni kullanarak aboneliklerinizde Azure HDInsight kümeleri dahil olmak üzere tüm kaynakları (işlem, depolama, ağ, bağlantı noktaları ve protokoller vb.) sorgulamak ve bulun. Kiracınızda uygun (okuma) izinleriniz olduğundan ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerinin listesini belirleyebildiğinizden emin olun.

Klasik Azure kaynakları Azure Kaynak Grafiği aracılığıyla bulunabilir, ancak ileri doğru Azure Resource Manager kaynak oluşturmanız ve kullanılması kesinlikle önerilir.

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

**Rehberlik**: varlıkları düzenlemek ve izlemek için uygun yerlerde etiketleme, yönetim grupları ve ayrı abonelikler kullanın. Envanterin düzenli olarak mutabakatını yapın ve yetkisiz kaynakların aboneliğin zamanında silindiğinden emin olun.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynaklarının envanterini tanımlayın ve saklayın

**Rehberlik**: işlem kaynaklarınız Için onaylanan Azure kaynakları ve onaylanan yazılımın listesini tanımlama

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:

- İzin verilmeyen kaynak türleri

- İzin verilen kaynak türleri

Aboneliklerinizde kaynakları sorgulamak ve saptamak için Azure Kaynak grafiğini kullanın. Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: işlem kaynakları içindeki onaylanmamış yazılım uygulamaları için izleyici

**Rehberlik**: onaylanmamış yazılım uygulamalarına yönelik küme düğümlerini izlemek için bir üçüncü taraf çözümü uygulayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Rehberlik**: Azure Kaynak Grafiği 'ni kullanarak aboneliklerinizde Azure HDInsight kümeleri dahil olmak üzere tüm kaynakları (işlem, depolama, ağ, bağlantı noktaları ve protokoller vb.) sorgulamak ve bulun.  Keşfettiğiniz onaylanmamış tüm Azure kaynaklarını kaldırın. Azure HDInsight küme düğümleri için, onaylanmamış yazılımları kaldırmak veya uyarmak üzere bir üçüncü taraf çözümü uygulayın.

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="68-use-only-approved-applications"></a>6,8: yalnızca onaylanan uygulamaları kullan

**Kılavuz**: Azure HDInsight küme düğümleri için, yetkisiz yazılımın yürütülmesini engelleyen bir üçüncü taraf çözümü uygulayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:
- İzin verilmeyen kaynak türleri

- İzin verilen kaynak türleri

Daha fazla bilgi için aşağıdaki başvuruları inceleyin:

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](../governance/policy/samples/built-in-policies.md#general)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: onaylanan yazılım başlıkları envanterini koruyun

**Kılavuz**: Azure HDInsight küme düğümleri için, yetkisiz dosya türlerinin yürütülmesini engelleyen bir üçüncü taraf çözümü uygulayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: kullanıcıların Azure Resource Manager etkileşime geçme yeteneğini sınırlayın

**Rehberlik**: "Microsoft Azure yönetimi" uygulaması için "erişimi engelle" özelliğini yapılandırarak kullanıcıların Azure Resource Manager etkileşime geçmesini sınırlamak Için Azure koşullu erişimi kullanın.

- [Azure Resource Manager erişimi engellemek için koşullu erişimi yapılandırma](../role-based-access-control/conditional-access-azure-management.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: tüm Azure kaynakları için güvenli yapılandırma oluşturma

**Rehberlik**: HDInsight kümenizin ağ yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak Için "Microsoft. HDInsight" ad alanındaki Azure ilke diğer adlarını kullanın.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: güvenli işletim sistemi yapılandırması oluşturma

**Rehberlik**: Microsoft tarafından yönetilen ve korunan Azure HDInsight Işletim sistemi görüntüleri. Küme düğümlerinizin işletim sistemine yönelik güvenli yapılandırmaların uygulamamasından sorumlu müşteri.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Kılavuz**: Azure HDInsight kümeleriniz ve ilgili kaynaklarınız için güvenli ayarları zorlamak üzere Azure ilkesi [reddetme] ve [dağıtım yok] kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: güvenli işletim sistemi yapılandırmalarının bakımını yapma

**Rehberlik**: Microsoft tarafından yönetilen ve korunan Azure HDInsight Işletim sistemi görüntüleri. İşletim sistemi düzeyinde durum yapılandırması uygulamaktan sorumlu müşteri.

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilke tanımları kullanıyorsanız, kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure devops veya Azure Repos kullanın.

- [Git öğreticisi Azure Repos](/azure/devops/repos/git/gitworkflow)

- [Azure Repos belgeleri](/azure/devops/repos/index)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: sistem yapılandırmalarına uyarı vermek, denetlemek ve zorlamak için özel ilkeler oluşturmak üzere "Microsoft. HDInsight" ad alanındaki Azure ilke diğer adlarını kullanın. Ayrıca, ilke özel durumlarını yönetmek için bir işlem ve işlem hattı geliştirin.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: işletim sistemleri için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: küme düğümü işletim sistemlerinizde istenen durumu korumak için bir üçüncü taraf çözümü uygulayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: HDInsight kümenizin yapılandırmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak Için "Microsoft. HDInsight" ad alanındaki Azure ilke diğer adlarını kullanın.

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: işletim sistemleri için otomatik yapılandırma izlemeyi Uygula

**Rehberlik**: küme düğümü işletim sistemlerinin durumunu izlemek için bir üçüncü taraf çözümü uygulayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Kılavuz**: Azure hdınsight, Apache Kafka için kendi anahtarını getir (bYok) desteği içerir. Bu özellik, bekleyen verileri şifrelemek için kullanılan anahtarları sahip olmanızı ve yönetmenizi sağlar.

Azure HDInsight 'taki tüm yönetilen diskler Azure Depolama Hizmeti Şifrelemesi (SSE) ile korunmaktadır. Varsayılan olarak, bu disklerdeki veriler Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir. BYOK ' u etkinleştirirseniz, Azure HDInsight 'ın Azure Key Vault kullanarak bunu kullanmasını ve yönetmesini sağlamak için şifreleme anahtarını sağlarsınız.

Key Vault Ayrıca, küme depolama (Azure depolama hesapları ve Azure Data Lake Storage) için anahtarları yönetmek üzere Azure HDInsight dağıtımlarıyla birlikte kullanılabilir.

- [Azure HDInsight 'ta Apache Kafka için kendi anahtarınızı getirme](./disk-encryption.md)

- [Azure depolama hesapları için şifreleme anahtarlarını yönetme](../storage/common/customer-managed-keys-configure-key-vault.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Rehberlik**: Yönetilen kimlikler, kümelerinizin Azure Active Directory (Azure AD) etki alanı hizmetlerine erişmesini, Azure Key Vault erişimini veya Azure Data Lake Storage 2. dosya erişimini sağlamak Için Azure HDInsight 'ta kullanılabilir.

- [Azure HDInsight ile yönetilen kimlikleri anlama](hdinsight-managed-identities.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Kılavuz**: Azure HDInsight dağıtımınızla ilgili herhangi bir kod kullanıyorsanız, kod içindeki kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayabilirsiniz. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar. 

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: merkezi olarak yönetilen kötü amaçlı yazılımdan koruma yazılımı kullanın

**Kılavuz**: Azure HDInsight, küme düğümü görüntüleri için önceden yüklenmiş ve etkin olan Azure HDInsight ile birlikte gelir, ancak yazılımı yönetmeniz ve ClamScan 'ın ürettiği tüm günlükleri el ile toplamanız/izlemeniz gerekir.

- [Azure HDInsight için ClamScan 'ı anlama](./hdinsight-faq.md#security-and-certificates)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: işlem dışı Azure kaynaklarına yüklenecek dosyaları önceden Tara

**Rehberlik**: Microsoft kötü amaçlı yazılımdan koruma, Azure hizmetlerini destekleyen temel alınan konakta etkinleştirilir, ancak müşteri içeriğinde çalışmaz.

Data Lake Storage, BLOB depolama vb. gibi Azure HDInsight küme dağıtımınızla ilişkili tüm Azure kaynaklarına yüklenen dosyaları önceden tarayın. Microsoft bu örneklerdeki müşteri verilerine erişemez.

- [Azure Cloud Services ve sanal makineler için Microsoft Antimalware 'i anlayın](../security/fundamentals/antimalware.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: kötü amaçlı yazılımdan koruma yazılımlarının ve imzaların güncelleştirildiğinden emin olun

**Rehberlik**: Azure HDInsight, küme düğümü görüntüleri için önceden yüklenmiş ve etkin bir şekilde birlikte gelir. ClamScan, altyapı ve tanım güncelleştirmelerini otomatik olarak gerçekleştirebilir, ancak günlüklerin toplama ve yönetiminin el ile gerçekleştirilmesi gerekir.

- [Azure Azure HDInsight için ClamScan 'ı anlama](./hdinsight-faq.md#security-and-certificates)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli otomatik arka pencerelere emin olun

**Rehberlik**: HDInsight kümesi veri deposu Için bir Azure depolama hesabı kullanırken uygun artıklık seçeneğini (LRS, ZRS, GRS, RA-GRS) seçin.  Azure HDInsight kümesi veri deposu için bir Azure SQL veritabanı kullanırken, etkin coğrafi çoğaltma 'yı yapılandırın.

- [Azure depolama hesapları için depolama yedekliği nasıl yapılandırılır](../storage/common/storage-redundancy.md)

- [Azure SQL veritabanı için yedeklilik yapılandırma](../azure-sql/database/active-geo-replication-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Kılavuz**: Azure HDInsight kümesi veri deposu Için bir Azure depolama hesabı kullanırken uygun artıklık seçeneğini (LRS, ZRS, GRS, RA-GRS) seçin. Azure HDInsight dağıtımınızın herhangi bir bölümü için Azure Key Vault kullanıyorsanız, anahtarlarınızın yedeklendiğinden emin olun.

- [Azure HDInsight kümeniz için depolama seçeneklerini belirleyin](hdinsight-hadoop-compare-storage-options.md)

- [Azure depolama hesapları için depolama yedekliği nasıl yapılandırılır](../storage/common/storage-redundancy.md)

- [Azure 'da Key Vault anahtarlarını yedekleme](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Kılavuz**: Azure hdınsight dağıtımınızda Azure Key Vault kullanılıyorsa, yedeklenen müşteri tarafından yönetilen anahtarların test geri yüklenmesi.

- [Azure HDInsight 'ta Apache Kafka için kendi anahtarınızı getirme](./disk-encryption.md)

- [Azure 'da Anahtar Kasası anahtarlarını geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Rehberlik**: Azure Key Vault Azure HDInsight dağıtımıyla birlikte kullanılıyorsa, anahtarları yanlışlıkla veya kötü amaçlı silmeye karşı korumak için Key Vault geçici silme özelliğini etkinleştirin.

- [Geçici silme Azure Key Vault etkinleştirme](../key-vault/general/soft-delete-overview.md)

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

**Rehberlik**: Güvenlik Merkezi, her uyarıya katılma sırasını önceliklendirmenize yardımcı olmak için uyarılara önem derecesi atar. böylece, bir kaynak tehlikeye girdiğinde, hemen bu işe başlayabilirsiniz. Önem derecesi, güvenlik merkezi 'nin uyarıyı vermek için kullanılan bulma veya analizte ne kadar önemli olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin yanı sıra güven düzeyini temel alır.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve açıkları belirleyip planı gerektiği şekilde gözden geçirin.

- [NıST 'nin yayını: BT planları ve özellikleri için test, eğitim ve alıştırma programlarını inceleyin](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: Microsoft Güvenlik Yanıt MERKEZI (MSRC), verilerinize izinsiz veya yetkisiz bir taraf tarafından erişildiğini belirlerse, Microsoft tarafından sizinle iletişim kurmak için güvenlik olayı iletişim bilgileri kullanılacaktır.

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

**Rehberlik**: güvenlik uyarılarında ve önerilerinde "Logic Apps" aracılığıyla yanıtları otomatik olarak tetiklemek Için Azure Güvenlik Merkezi 'Nde Iş akışı Otomasyonu özelliğini kullanın.

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
