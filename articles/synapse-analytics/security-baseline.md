---
title: SYNAPSE Analytics için Azure Güvenlik temeli
description: SYNAPSE Analytics güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: synapse-analytics
ms.subservice: security
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1a426a86ddb25733b8425c5887d2c0522f915e2d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587723"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>SYNAPSE Analytics için Azure Güvenlik temeli

Bu güvenlik temeli, [Azure Güvenlik kıyaslama sürümü 1,0](../security/benchmarks/overview-v1.md) ' dan SYNAPSE Analytics 'e kılavuzluk uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar.
İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve SYNAPSE Analytics için geçerli olan ilgili kılavuza göre gruplandırılır. SYNAPSE Analytics için geçerli olmayan **denetimler** dışlandı.

 
SYNAPSE Analytics 'in Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam SYNAPSE Analytics güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: Azure SQL Server özel bağlantı aracılığıyla bir sanal ağ ile güvenli hale getirin. Azure özel bağlantısı, Azure PaaS hizmetlerine sanal ağınızdaki özel bir uç nokta üzerinden erişmenizi sağlar. Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağını de dolaşır.

Alternatif olarak, SYNAPSE SQL havuzunuza bağlanırken, bir ağ güvenlik grubu kullanarak SQL veritabanı 'na giden bağlantının kapsamını daraltın. Azure hizmetlerinin kapalı çalışmasına Izin ver ayarını yaparak, genel uç nokta aracılığıyla SQL veritabanı 'na yönelik tüm Azure hizmet trafiğini devre dışı bırakın. Güvenlik duvarı kurallarında genel IP adreslerine izin verilmediğinden emin olun.

- [Azure özel bağlantısını anlama](../private-link/private-link-overview.md)

- [Azure SYNAPSE SQL için özel bağlantıyı anlama](../azure-sql/database/private-endpoint-overview.md)

- [Sanal ağ oluşturma](../virtual-network/quick-create-portal.md)

- [Güvenlik Yapılandırması ile NSG oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: sanal ağların, alt ağların ve ağ arabirimlerinin yapılandırma ve trafiğini izleme ve günlüğe kaydetme

**Rehberlik**: adanmış SQL havuzunuza bağlanırken ve ağ güvenlik grubu (NSG) akış günlüklerini etkinleştirdiyseniz, Günlükler trafik denetimi Için bir Azure depolama hesabına gönderilir.

Ayrıca, NSG akış günlüklerini bir Log Analytics çalışma alanına gönderebilir ve Azure bulutunuzda trafik akışına Öngörüler sağlamak için Trafik Analizi kullanabilirsiniz. Trafik Analizi avantajlarından bazıları, ağ etkinliğini görselleştirme ve etkin noktaları belirlemek, güvenlik tehditlerini belirlemek, trafik akışı düzenlerini anlamak ve ağ yapılandırmalarını saptamak için kullanılır.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

- [Trafik Analizi etkinleştirme ve kullanma](../network-watcher/traffic-analytics.md)

- [Azure Güvenlik Merkezi tarafından sunulan ağ güvenliğini anlama](../security-center/security-center-network-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: bilinen kötü amaçlı IP adresleriyle iletişimleri reddetme

**Kılavuz**: Azure SYNAPSE SQL Için Gelişmiş tehdit KORUMASı (ATP) kullanın. ATP, veritabanlarına erişmek veya veritabanına yararlanmak için olağan dışı ve zararlı olabilecek girişimleri gösteren anormal etkinlikleri algılar ve "olası SQL ekleme," ve "olağan dışı konumdan erişim" gibi çeşitli uyarılar tetikleyebilirler. ATP, gelişmiş veri güvenliği (ADS) sunumunun bir parçasıdır ve merkezi SQL ADS portalı aracılığıyla erişilebilir ve yönetilebilir.

Dağıtılmış hizmet reddi saldırılarına karşı koruma için Azure SYNAPSE SQL ile ilişkili sanal ağlarda DDoS koruma standardını etkinleştirin. Bilinen kötü amaçlı veya kullanılmayan Internet IP adresleriyle iletişimleri reddetmek için Azure Güvenlik Merkezi tümleşik tehdit zekasını kullanın.

- [Azure SYNAPSE SQL için ATP 'yi anlama](../azure-sql/database/threat-detection-overview.md)

- [Azure SQL veritabanı için gelişmiş veri güvenliğini etkinleştirme](../azure-sql/database/azure-defender-for-sql.md)

- [ADS genel bakış](../azure-sql/database/azure-defender-for-sql.md)

- [DDoS korumasını yapılandırma](../ddos-protection/manage-ddos-protection.md)

- [Azure Güvenlik Merkezi tümleşik tehdit zekasını anlama](../security-center/azure-defender.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="15-record-network-packets"></a>1,5: ağ paketlerini kaydetme

**Rehberlik**: adanmış SQL havuzunuza bağlanırken ve ağ güvenlik grubu (NSG) akış günlüklerini etkinleştirdiyseniz, trafik denetimi Için günlükleri Azure depolama hesabına gönderin. Akış günlüklerini bir Log Analytics çalışma alanına da gönderebilir veya Event Hubs için akışla aktarabilirsiniz.  Anormal etkinlikleri araştırmak için gerekliyse, ağ Izleyicisi paket yakalamayı etkinleştirin.

- [NSG akış günlüklerini etkinleştirme](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Ağ İzleyicisini etkinleştirme](../network-watcher/network-watcher-create.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: ağ tabanlı yetkisiz giriş algılama/yetkisiz erişim önleme sistemleri (KIMLIKLER/IP 'ler) dağıtma

**Kılavuz**: Azure SYNAPSE SQL Için Gelişmiş tehdit KORUMASı (ATP) kullanın. ATP, veritabanlarına erişmek veya veritabanına yararlanmak için olağan dışı ve zararlı olabilecek girişimleri gösteren anormal etkinlikleri algılar ve "olası SQL ekleme," ve "olağan dışı konumdan erişim" gibi çeşitli uyarılar tetikleyebilirler. ATP, gelişmiş veri güvenliği (ADS) sunumunun bir parçasıdır ve merkezi SQL ADS portalı aracılığıyla erişilebilir ve yönetilebilir. ATP Ayrıca uyarıları Azure Güvenlik Merkezi ile tümleştirir.

- [Azure SYNAPSE SQL için ATP 'yi anlama](../azure-sql/database/threat-detection-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ağ güvenlik kurallarının karmaşıklığını ve yönetim yükünü en aza indirme

**Rehberlik**: ağ güvenlik gruplarında veya Azure Güvenlik duvarında ağ erişim denetimleri tanımlamak için sanal ağ hizmeti etiketlerini kullanın. Hizmet etiketlerini güvenlik kuralı oluştururken belirli IP adreslerinin yerine kullanabilirsiniz. Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adı (örn., Apimanaya) belirterek, ilgili hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

Adanmış SQL havuzunuz için bir hizmet uç noktası kullanırken, Azure SQL veritabanı genel IP adreslerine giden bağlantı gereklidir: bağlantıya izin vermek için ağ güvenlik grupları (NSG 'Ler) Azure SQL veritabanı IP 'lerine açılmalıdır. Bunu Azure SQL veritabanı için NSG hizmet etiketlerini kullanarak yapabilirsiniz.

- [Hizmet etiketlerini Azure SQL veritabanı için hizmet uç noktaları ile anlayın](https://docs.microsoft.com/azure/azure-sql/database/vnet-service-endpoint-rule-overview#limitations)

- [Hizmet etiketlerini anlama ve kullanma](../virtual-network/service-tags-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: ağ cihazları için standart güvenlik yapılandırmalarının bakımını yapma

**Kılavuz**: Azure ILKESIYLE adanmış SQL havuzunuza ilişkin kaynaklar için ağ güvenlik yapılandırması tanımlayın ve uygulayın. Özel ilke tanımlarını tanımlamak veya Azure SQL veritabanı/sunucu ağ koruması için tasarlanan yerleşik ilke tanımlarından herhangi birini kullanmak için "Microsoft. SQL" ad alanını kullanabilirsiniz. Azure SQL veritabanı sunucusu için geçerli bir yerleşik ağ güvenlik ilkesi örneği şöyle olabilir: "SQL Server sanal ağ hizmeti uç noktası kullanmalıdır".

Azure Kaynak Yönetimi şablonları, Azure rol tabanlı erişim denetimi (Azure RBAC) ve ilkeler gibi tek bir şema tanımında temel ortam yapıtları sunarak büyük ölçekli Azure dağıtımlarını basitleştirmek için Azure şemaları 'nı kullanın. Şema 'i yeni abonelikler ve ortamlara kolayca uygulayın ve sürüm oluşturma aracılığıyla denetimi ve yönetimi ayrıntılı olarak ayarlayın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint oluşturma](../governance/blueprints/create-blueprint-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="110-document-traffic-configuration-rules"></a>1,10: belge trafiği yapılandırma kuralları

**Rehberlik**: ağ güvenlik grupları (NSG) ve ağ güvenliği ve trafik akışıyla ilgili diğer kaynaklar için Etiketler kullanın. Bireysel NSG kuralları için "Açıklama" alanını kullanarak ağa giden/giden trafiğe izin veren kuralların iş gereksinimini ve/veya süresini (vs.) belirtin.

Tüm kaynakların etiketlerle oluşturulmasını ve mevcut etiketlenmemiş kaynakları bilgilendirmesini sağlamak için etiketlemeyle ilgili yerleşik Azure Ilke tanımlarından herhangi birini ("etiket ve onun değeri gerektir" gibi) kullanın.

Azure PowerShell veya Azure CLı kullanarak, etiketlerine göre kaynakları arayabilir veya bunlarla ilgili eylemler gerçekleştirebilirsiniz.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: ağ kaynağı yapılandırmasını izlemek ve değişiklikleri algılamak için otomatikleştirilmiş araçları kullanın

**Kılavuz**: Azure etkinlik günlüğü 'nü kullanarak ağ kaynak yapılandırmasını izleyin ve adanmış SQL havuzunuza ilişkin ağ kaynaklarına yönelik değişiklikleri tespit edin. Kritik ağ kaynaklarında yapılan değişiklikler yürürlüğe girdiğinde tetiklenecek Azure Izleyici içinde uyarılar oluşturun.

- [Azure etkinlik günlüğü olaylarını görüntüleme ve alma](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Izleyici 'de uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="logging-and-monitoring"></a>Günlüğe Kaydetme ve İzleme

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Merkezi güvenlik günlüğü yönetimini yapılandırma

**Rehberlik**: bir denetim ilkesi, belirli bir veritabanı Için veya Azure 'da varsayılan sunucu ilkesi olarak tanımlanabilir (Azure SYNAPSE ' i barındırır). Sunucu ilkesi, sunucuda var olan ve yeni oluşturulan tüm veritabanları için geçerlidir.

Sunucu denetimi etkinse, her zaman veritabanına uygulanır. Veritabanı denetim ayarlarından bağımsız olarak veritabanını denetlenecektir.

Denetimi etkinleştirdiğinizde, bunları Azure Depolama hesabınızdaki bir denetim günlüğüne, Log Analytics çalışma alanına veya Event Hubs yazabilirsiniz.

Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz.

- [Azure SQL kaynaklarınız için Denetim kurma](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Azure kaynakları için denetim günlüğünü etkinleştirme

**Rehberlik**: adanmış SQL havuzunuz IÇIN Azure SQL Server düzeyinde denetimi etkinleştirin ve denetim günlükleri için bir depolama konumu seçin (Azure depolama, Log Analytics veya Event Hubs).

Denetim hem veritabanı hem de sunucu düzeyinde etkinleştirilebilir ve belirli bir veritabanı için ayrı bir veri havuzu veya bekletme yapılandırması gerekmedikçe yalnızca sunucu düzeyinde etkin olmak üzere önerilir.

- [Azure SQL veritabanı için denetimi etkinleştirme](../azure-sql/database/auditing-overview.md)

- [Sunucunuz için denetimi etkinleştirme](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

- [Sunucu düzeyinde ve veritabanı düzeyinde denetim ilkelerine göre farklılıklar](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: güvenlik günlüğü depolama bekletmesini yapılandırma

**Rehberlik**: bir depolama hesabı, Log Analytics çalışma alanı veya Olay Hub 'ları IÇIN adanmış SQL havuzunuzla ilgili günlükleri depolarken, kuruluşunuzun uyumluluk düzenlemelerine göre günlük saklama süresini ayarlayın.

- [Azure Blob depolama yaşam döngüsünü yönetme](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Log Analytics çalışma alanında günlük tutma parametrelerini ayarlama](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Event Hubs akış olaylarını yakala](../event-hubs/event-hubs-capture-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2,6: günlükleri izleme ve gözden geçirme

**Rehberlik**: anormal davranışlar için günlükleri çözümleyin ve izleyin ve sonuçları düzenli olarak gözden geçirin. SQL veritabanınız ile ilgili olağandışı etkinliklerle ilgili uyarı almak için Azure Güvenlik Merkezi ile birlikte Azure SQL veritabanı için Gelişmiş tehdit koruması 'nı kullanın. Alternatif olarak, SQL veritabanı ile ilgili ölçüm değerlerine veya Azure etkinlik günlüğü girişlerine göre uyarıları yapılandırın.

Alternatif olarak, Azure Sentinel 'e veya bir üçüncü taraf SıEM 'ye veri etkinleştirebilir ve bu verileri ayarlayabilirsiniz.

- [Azure SQL veritabanı için Gelişmiş tehdit koruması ve uyarı vermeyi anlama](../azure-sql/database/threat-detection-overview.md)

- [Azure SQL veritabanı için gelişmiş veri güvenliğini etkinleştirme](../azure-sql/database/azure-defender-for-sql.md)

- [Azure SQL veritabanı için özel uyarıları yapılandırma](../azure-sql/database/alerts-insights-configure-portal.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: anormal etkinlikler için uyarıları etkinleştir

**Rehberlik**: anormal etkinlikleri izlemek ve uyarı almak Için Azure Güvenlik Merkezi Ile bırlıkte Azure SQL veritabanı Için Gelişmiş tehdit KORUMASı (ATP) kullanın. ATP, gelişmiş veri güvenliği (ADS) sunumunun bir parçasıdır ve portalda merkezi SQL REKLAMLARı aracılığıyla erişilebilir ve yönetilebilir. ADS, hassas verileri bulma ve sınıflandırma, olası veritabanı güvenlik açıklarını ortaya koymasının yanı sıra veritabanlarınızın bir tehdidi oluşturabilecek anormal etkinlikleri algılamayla ilgili işlevsellik içerir.

Alternatif olarak, Azure Sentinel 'de ve yerleşik verileri etkinleştirebilir.

- [Azure SQL veritabanı için Gelişmiş tehdit koruması ve uyarı vermeyi anlama](../azure-sql/database/threat-detection-overview.md)

- [Azure SQL veritabanı için gelişmiş veri güvenliğini etkinleştirme](../azure-sql/database/azure-defender-for-sql.md)

- [Azure Güvenlik Merkezi 'nde uyarıları yönetme](../security-center/security-center-managing-and-responding-alerts.md)

- [Azure Sentinel 'i ekleme](../sentinel/quickstart-onboard.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: yönetim hesaplarının envanterini tutma

**Rehberlik**: kullanıcıların kimliği Azure Active Directory (Azure AD) ya da SQL kimlik doğrulamasıyla doğrulanır.

Azure SQL 'i ilk kez dağıttığınızda, bu oturum açma için yönetici oturumu ve ilişkili bir parola belirtirsiniz. Bu yönetim hesabına Sunucu Yöneticisi adı verilir. Bir veritabanının yönetici hesaplarını, Azure portal açıp sunucunuzun veya yönetilen örneğinizin Özellikler sekmesine giderek belirleyebilirsiniz. Ayrıca, Azure AD kimlik doğrulamasını etkinleştirmek istiyorsanız, tam yönetici izinleriyle bir Azure AD yönetici hesabı da yapılandırabilirsiniz.

Yönetim işlemleri için, açıkça atanması gereken Azure yerleşik rollerini kullanın. Yönetim gruplarının üyesi olan hesapları bulmaya yönelik geçici sorgular gerçekleştirmek için Azure AD PowerShell modülünü kullanın.

- [SQL veritabanı için kimlik doğrulaması](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

- [Yönetici olmayan kullanıcılar için hesap oluşturma](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

- [Kimlik doğrulaması için bir Azure AD hesabı kullanın](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

- [Azure AD 'de PowerShell ile dizin rolü alma](/powershell/module/azuread/get-azureaddirectoryrole)

- [Azure AD 'de PowerShell ile bir dizin rolünün üyelerini alma](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure SQL 'de var olan oturum açma işlemlerini ve yönetici hesaplarını yönetme](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

- [Yerleşik Azure rolleri](../role-based-access-control/built-in-roles.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="32-change-default-passwords-where-applicable"></a>3,2: uygun yerlerde varsayılan parolaları değiştirme

**Rehberlik**: Azure Active Directory (Azure AD) varsayılan parola kavramına sahip değil. Adanmış bir SQL havuzu sağlanırken, kimlik doğrulamasını Azure AD ile tümleştirmeyi tercih etmeniz önerilir. Bu kimlik doğrulama yöntemiyle kullanıcı, bir kullanıcı hesabı adı gönderir ve hizmetin Azure AD 'de depolanan kimlik bilgileri bilgilerini kullanmasını ister.

- [Azure SQL ile Azure AD kimlik doğrulamasını yapılandırma ve yönetme](../azure-sql/database/authentication-aad-configure.md)

- [Azure SQL 'de kimlik doğrulamasını anlama](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: adanmış yönetim hesapları kullanın

**Rehberlik**: adanmış yönetim hesaplarının kullanımı etrafında ilke ve yordamlar oluşturun. Azure Active Directory (Azure AD) aracılığıyla oturum açma yönetim hesaplarının sayısını izlemek için Azure Güvenlik Merkezi kimlik ve erişim yönetimi 'ni kullanın.

Bir veritabanının yönetici hesaplarını belirlemek için, Azure portal açın ve sunucunuzun veya yönetilen örneğinizin Özellikler sekmesine gidin.

- [Azure Güvenlik Merkezi kimlik ve erişimini anlama](../security-center/security-center-identity-access.md)

- [Azure SQL 'de var olan oturum açma işlemlerini ve yönetici hesaplarını yönetme](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory çoklu oturum açma kullan (SSO)

**Rehberlik**: API çağrıları aracılığıyla denetim düzleminde (Azure Portal) veri Ambarınızla etkileşim kurmak için kullanılabilecek bir belirteç almak üzere bir Azure uygulama kaydı (hizmet sorumlusu) kullanın.

- [Azure REST API 'Lerini çağırma](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [İstemci uygulamanızı (hizmet sorumlusu) Azure Active Directory (Azure AD) ile kaydetme](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure SYNAPSE SQL REST API bilgileri](sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Active Directory (Azure AD) çok faktörlü kimlik doğrulamasını etkinleştirin ve Azure Güvenlik Merkezi kimlik ve erişim yönetimi önerilerini izleyin.

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme](../security-center/security-center-identity-access.md)

- [Azure SQL 'de çok faktörlü kimlik doğrulamasını anlama](../azure-sql/database/authentication-mfa-ssms-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: yönetim görevleri için güvenli, Azure tarafından yönetilen iş istasyonları kullanın

**Kılavuz**: Azure kaynaklarını açmak ve yapılandırmak için çok faktörlü kimlik doğrulaması yapılandırılmış bir ayrıcalıklı erişim iş istasyonu (Paw) kullanın.

- [Ayrıcalıklı erişim Iş Istasyonları hakkında bilgi edinin](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure 'da çok faktörlü kimlik doğrulamasını etkinleştirme](../active-directory/authentication/howto-mfa-getstarted.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: yönetim hesaplarından şüpheli etkinliklerle ilgili günlüğe kaydet ve uyar

**Rehberlik**: ortamda şüpheli veya güvenli olmayan bir etkinlik olduğunda Günlükler ve uyarılar oluşturmak için Azure Active Directory (Azure AD) güvenlik raporları kullanın.

Azure SQL veritabanı için Gelişmiş tehdit koruması 'nı Azure Güvenlik Merkezi ile birlikte kullanarak, veritabanları için olağan dışı ve olası zararlı girişimleri belirten anormal etkinlikleri algılayıp uyarma.

SQL Server denetim, sunucu seviyesi olayları için sunucu denetim belirtimleri ve veritabanı düzeyindeki olaylar için veritabanı denetim belirtimleri içerebilen sunucu denetimleri oluşturmanızı sağlar. Denetlenen olaylar olay günlüklerine yazılabilir veya dosyaları denetlemek için kullanılabilir.

- [Riskli etkinlik bayrağıyla işaretlenen Azure AD kullanıcılarını belirleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Güvenlik Merkezi 'nde Kullanıcı kimliğini ve erişim etkinliğini izleme](../security-center/security-center-identity-access.md)

- [Gelişmiş tehdit koruması ve olası uyarıları gözden geçirme](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

- [Azure SQL 'de oturum açma işlemlerini ve Kullanıcı hesaplarını anlama](../azure-sql/database/logins-create-manage.md)

- [SQL Server denetlemeyi anlayın](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure kaynaklarını yalnızca onaylanan konumlardan yönetme

**Rehberlik**: IP adresi aralıklarının veya ülkelerin/bölgelerin yalnızca belirli mantıksal gruplarından portala ve Azure kaynak yönetimine erişim sağlamak Için, koşullu erişim adlı konum kullanın.

- [Azure 'da adlandırılmış konumları yapılandırma](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory kullanın

**Rehberlik**: adanmış SQL havuzunuzdaki Azure SQL veritabanı sunucusu için bir Azure Active Directory (Azure AD) Yöneticisi oluşturun.

- [Azure SQL ile Azure AD kimlik doğrulamasını yapılandırma ve yönetme](../azure-sql/database/authentication-aad-configure.md)

- [Azure AD örneği oluşturma ve yapılandırma](../active-directory-domain-services/tutorial-create-instance.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: Kullanıcı erişimini düzenli olarak gözden geçirin ve karşılaştırın

**Rehberlik**: Azure Active Directory (Azure AD) eski hesapları keşfetmenize yardımcı olacak Günlükler sağlar. Ayrıca, grup üyeliklerini verimli bir şekilde yönetmek için Azure AD erişim gözden geçirmeleri ' nı kullanın, kurumsal uygulamalara ve rol atamalarına erişin. Kullanıcıların erişimi, yalnızca doğru kullanıcıların erişmeye devam ettiğinden emin olmak için düzenli aralıklarla gözden geçirilebilir.

SQL kimlik doğrulaması kullanırken, veritabanında kapsanan veritabanı kullanıcıları oluşturun. Bir veya daha fazla veritabanı kullanıcıyı bu kullanıcı grubuna uygun belirli izinlerle özel bir veritabanı rolüne yerleştirdiğinizden emin olun.

- [Erişim incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

- [Azure SQL 'de oturum açma işlemlerini ve Kullanıcı hesaplarını anlama](../azure-sql/database/logins-create-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: devre dışı bırakılmış kimlik bilgilerine erişme girişimlerini izleme

**Rehberlik**: Azure SQL ile Azure Active Directory (Azure AD) kimlik doğrulamasını yapılandırın ve Azure AD Kullanıcı hesapları için tanılama ayarlarını etkinleştirerek, Denetim günlüklerini ve oturum açma günlüklerini bir Log Analytics çalışma alanına gönderir. Log Analytics içindeki istenen uyarıları yapılandırın.

SQL kimlik doğrulaması kullanırken, veritabanında kapsanan veritabanı kullanıcıları oluşturun. Bir veya daha fazla veritabanı kullanıcıyı bu kullanıcı grubuna uygun belirli izinlerle özel bir veritabanı rolüne yerleştirdiğinizden emin olun.

- [Erişim incelemelerini kullanma](../active-directory/governance/access-reviews-overview.md)

- [Azure SQL veritabanı ile Azure AD kimlik doğrulamasını yapılandırma ve yönetme](../azure-sql/database/authentication-aad-configure.md)

- [Azure Etkinlik Günlüklerini Azure İzleyici ile tümleştirme](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure SQL 'de oturum açma işlemlerini ve Kullanıcı hesaplarını anlama](../azure-sql/database/logins-create-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: hesap oturum açma davranışı sapması üzerinde uyarı

**Rehberlik**: Kullanıcı kimlikleriyle ilgili şüpheli eylemleri algılanan otomatik yanıtları yapılandırmak için Azure Active Directory (Azure AD) kimlik koruması ve risk algılama özelliklerini kullanın. Ayrıca, daha fazla araştırma için yerleşik ve Azure Sentinel 'e veri alma.

SQL kimlik doğrulaması kullanırken, veritabanında kapsanan veritabanı kullanıcıları oluşturun. Bir veya daha fazla veritabanı kullanıcıyı bu kullanıcı grubuna uygun belirli izinlerle özel bir veritabanı rolüne yerleştirdiğinizden emin olun.

- [Azure AD risk oturumunu görüntüleme](../active-directory/identity-protection/overview-identity-protection.md)

- [Kimlik koruması risk ilkelerini yapılandırma ve etkinleştirme](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Yerleşik Azure Sentinel](../sentinel/connect-data-sources.md)

- [Azure SQL 'de oturum açma işlemlerini ve Kullanıcı hesaplarını anlama](../azure-sql/database/logins-create-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: destek senaryoları sırasında Microsoft 'un ilgili müşteri verilerine erişimini sağlama

**Kılavuz**: Microsoft 'un adanmış SQL havuzunuzdaki Azure SQL veritabanı ile ilgili verilere erişmesi gereken destek senaryolarında, Azure müşteri kasası veri erişim isteklerini gözden geçirmeniz ve onaylamanız veya reddetmeniz için bir arabirim sağlar.

- [Müşteri Kasası anlayın](../security/fundamentals/customer-lockbox-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-protection"></a>Veri Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Veri Koruma](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: hassas bilgilerin envanterini tutma

**Rehberlik**: hassas bilgileri depolayan veya işleyen Azure kaynaklarını izlemeye yardımcı olması için etiketleri kullanın.

Veri bulma &amp; sınıflandırması, Azure SYNAPSE SQL 'de yerleşik olarak bulunur. Veritabanınızdaki hassas verileri bulmak, sınıflandırmak, etiketlemek ve raporlamak için gelişmiş yetenekler sağlar.

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Veri bulma &amp; sınıflandırmasını anlama](../azure-sql/database/data-discovery-and-classification-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: hassas bilgileri depolayan veya işleyen sistemleri yalıtma

**Rehberlik**: geliştirme, test ve üretim için ayrı abonelikler ve/veya yönetim grupları uygulayın. Kaynaklar, sanal ağ/alt ağ ile ayrılmalıdır, uygun şekilde etiketlenebilir ve bir ağ güvenlik grubu ya da Azure Güvenlik duvarı içinde güvenliği sağlanmış olmalıdır. Hassas verileri depolayan veya işleyen kaynaklar yalıtılmalıdır. Özel bağlantı kullan; Azure SQL Server sanal bir ağ içinde dağıtın ve özel bağlantı kullanarak güvenli bir şekilde bağlanın.

- [Ek Azure abonelikleri oluşturma](../cost-management-billing/manage/create-subscription.md)

- [Yönetim Grupları oluşturma](../governance/management-groups/create-management-group-portal.md)

- [Etiketler oluşturma ve kullanma](../azure-resource-manager/management/tag-resources.md)

- [Azure SQL veritabanı için özel bağlantı ayarlama](../azure-sql/database/private-endpoint-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: hassas bilgilerin yetkisiz aktarımını izleme ve engelleme

**Rehberlik**: adanmış SQL havuzunuzdaki herhangi BIR Azure SQL veritabanı için gizli bilgileri depolayan veya işleyen, etiketleri kullanarak veritabanını ve ilgili kaynakları gizli olarak işaretleyin. Gizli bilgilerin ayıklanmasını engellemek için Azure SQL veritabanı örneklerindeki ağ güvenlik grubu (NSG) hizmet etiketleriyle birlikte özel bağlantıyı yapılandırın.

Ayrıca, Azure SQL veritabanı için Gelişmiş tehdit koruması, Azure SQL yönetilen örneği ve Azure SYNAPSE, veritabanları için olağandışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri algılar.

Microsoft tarafından yönetilen temel alınan platform için, Microsoft tüm müşteri içeriklerini gizli olarak değerlendirir ve müşteri veri kaybına ve açığa çıkmasına karşı koruma sağlamak için harika uzunluklara gider. Azure 'daki müşteri verilerinin güvende kalmasını sağlamak için Microsoft, bir dizi güçlü veri koruma denetimi ve özelliği uygulamıştır ve bakımını yapar.

- [Azure SQL veritabanı örneklerinizdeki verilerin ayıklanmasını engellemek için özel bağlantı ve NSG 'ler yapılandırma](../azure-sql/database/private-endpoint-overview.md)

- [Azure SQL veritabanı için Gelişmiş tehdit korumasını anlama](../azure-sql/database/threat-detection-overview.md)

- [Azure’da müşteri verilerinin korunmasını anlama](../security/fundamentals/protection-customer-data.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: yok

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: hassas verileri belirlemek için etkin bir keşif aracı kullanın

**Kılavuz**: Azure SYNAPSE SQL veri bulma &amp; sınıflandırması özelliğini kullanın. Veri bulma &amp; sınıflandırması, &amp; veritabanlarınızdaki hassas verileri korumak, sınıflandırmak ve etiketleme IÇIN Azure SQL veritabanı 'nda yerleşik olarak bulunan gelişmiş yetenekler sağlar.

Veri bulma &amp; sınıflandırması, GELIŞMIŞ SQL güvenlik özelliklerine yönelik Birleşik bir paket olan gelişmiş veri güvenliği sunumunun bir parçasıdır. Veri bulma &amp; sınıflandırmasına, MERKEZI SQL ads portalı aracılığıyla erişilebilir ve yönetilebilir.

Ayrıca, Azure portal bir dinamik veri maskeleme (DDM) ilkesi ayarlayabilirsiniz. DDM öneriler altyapısı, veritabanınızdaki belirli alanları, maskeleme için iyi aday olabilecek, potansiyel olarak hassas alanlar olarak işaretler.

- [Azure SQL Server veri bulma ve sınıflandırma kullanma](../azure-sql/database/data-discovery-and-classification-overview.md)

- [Azure SYNAPSE SQL için dinamik veri maskeleme 'yi anlama](../azure-sql/database/dynamic-data-masking-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-azure-rbac-access-control-to-control-access-to-resources"></a>4,6: kaynaklara erişimi denetlemek için Azure RBAC erişim denetimini kullanma 

**Rehberlik**: adanmış SQL HAVUZUNUZDAKI Azure SQL veritabanlarına erişimi yönetmek için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanın.

Yetkilendirme, Kullanıcı hesabınızın veritabanı rolü üyelikleri ve nesne düzeyi izinleri tarafından denetlenir. En iyi uygulama olarak, kullanıcılarınıza gerekli olan en düşük ayrıcalıkları tanımanız gerekir.

- [Azure SQL Server kimlik doğrulaması için Azure Active Directory (Azure AD) ile tümleştirme](../azure-sql/database/authentication-aad-overview.md)

- [Azure SQL Server erişimi denetleme](../azure-sql/database/logins-create-manage.md)

- [Azure SQL 'de yetkilendirmeyi ve kimlik doğrulamasını anlama](../azure-sql/database/logins-create-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: hassas bilgileri Rest 'te şifreleyin

**Kılavuz**: saydam veri şifrelemesi (tde), bekleyen verileri şifreleyerek kötü amaçlı çevrimdışı etkinlik tehditlerine karşı Azure SYNAPSE SQL 'i korumanıza yardımcı olur. Bu özellik bütün bir veritabanı, yedekleri ve işlem günlüğü dosyaları için gerçek zamanlı şifreleme ve şifre çözme işlemlerini gerçekleştirir ve uygulamada değişiklik yapmayı gerektirmez. Azure 'da, TDE için varsayılan ayar, DEK 'ın yerleşik bir sunucu sertifikasıyla korunmakta olması. Alternatif olarak, Ayrıca, TDE için Kendi Anahtarını Getir (BYOK) desteği olarak da adlandırılan müşteri tarafından yönetilen TDE kullanabilirsiniz. Bu senaryoda, 1 ' i şifreleyen TDE koruyucu, müşterinin sahip olduğu ve yönetilen bir Azure Key Vault (Azure 'un bulut tabanlı dış anahtar yönetim sistemi) depolanan ve anahtar kasasını hiçbir şekilde bırakmayan, müşteri tarafından yönetilen bir asimetrik anahtardır.

- [Hizmet tarafından yönetilen saydam veri şifrelemesini anlama](../azure-sql/database/transparent-data-encryption-tde-overview.md)

- [Müşterinin yönettiği saydam veri şifrelemesini anlama](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview#customer-managed-transparent-data-encryption---bring-your-own-key)

- [Kendi anahtarınızı kullanarak TDE nasıl devre dışı bırakılır](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: kritik Azure kaynaklarında yapılan değişikliklerle ilgili günlük ve uyarı

**Kılavuz**: SYNAPSE SQL havuzlarının üretim örneklerine ve diğer kritik veya ilgili kaynaklara yönelik değişikliklerin ne zaman gerçekleştiği hakkında uyarı oluşturmak Için Azure etkinlik günlüğü Ile Azure izleyici 'yi kullanın.

Ayrıca, Azure portal kullanarak SQL SYNAPSE havuzunuzdaki veritabanları için uyarılar ayarlayabilirsiniz. Uyarılar bir e-posta gönderebilir veya bazı bir ölçüm (örneğin, veritabanı boyutu veya CPU kullanımı) eşiğe ulaştığında bir Web kancası çağırabilir.

- [Azure etkinlik günlüğü olayları için uyarı oluşturma](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure SQL SYNAPSE için uyarılar oluşturma](../azure-sql/database/alerts-insights-configure-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: otomatikleştirilmiş güvenlik açığı tarama araçlarını çalıştırma

**Rehberlik**: Gelişmiş veri güvenliğini etkinleştirin ve Azure SQL veritabanlarında güvenlik açığı değerlendirmelerinin gerçekleştirilmesi Için Azure Güvenlik Merkezi 'ndeki önerileri izleyin.

- [Azure SQL veritabanlarında güvenlik açığı değerlendirmeleri nasıl çalıştırılır](../azure-sql/database/sql-vulnerability-assessment.md)

- [Gelişmiş veri güvenliğini etkinleştirme](../azure-sql/database/azure-defender-for-sql.md)

- [Azure Güvenlik Merkezi güvenlik açığı değerlendirmesi önerilerini uygulama](../security-center/deploy-vulnerability-assessment-vm.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: geri dönüş güvenlik açığı taramalarını karşılaştırın

**Rehberlik**: güvenlik açığı değerlendirmesi, Azure SYNAPSE SQL 'de yerleşik olarak bulunan bir tarama hizmetidir. Hizmet, güvenlik açıklarına işaret eden kuralların bilgi bankasını kullanır. Yanlış yapılandırma, aşırı izin ve korunmayan hassas veriler gibi en iyi uygulamalardan sapmaları vurgular.  Güvenlik açığı değerlendirmesi, merkezi SQL gelişmiş veri güvenliği (ADS) portalı aracılığıyla erişilebilir ve yönetilebilir.

- [SQL ADS portalındaki güvenlik açığı değerlendirme taramalarını yönetme ve dışarı aktarma](../azure-sql/database/sql-vulnerability-assessment.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek için risk derecelendirme işlemi kullanın

**Kılavuz**: Azure Güvenlik Merkezi tarafından sunulan varsayılan risk derecelendirmelerini (güvenli puan) kullanın.

Veri bulma &amp; sınıflandırması, Azure SYNAPSE SQL 'de yerleşik olarak bulunur. Veritabanınızdaki hassas verileri bulmak, sınıflandırmak, etiketlemek ve raporlamak için gelişmiş yetenekler sağlar.

- [Azure Güvenlik Merkezi güvenli Puanını anlama](../security-center/secure-score-security-controls.md)

- [Veri bulma &amp; sınıflandırmasını anlama](../azure-sql/database/data-discovery-and-classification-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: otomatik varlık bulma çözümünü kullanma

**Rehberlik**: abonelikleriniz DAHILINDE özel SQL havuzlarınızla ilgili tüm kaynakları sorgulamak ve saptamak Için Azure Kaynak grafiğini kullanın. Kiracınızda uygun (okuma) izinleriniz olduğundan ve aboneliklerinizdeki kaynakların yanı sıra tüm Azure aboneliklerinin listesini belirleyebildiğinizden emin olun.

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

**Rehberlik**: adanmış SQL havuzunuza Ilişkin onaylanan Azure kaynaklarının bir listesini tanımlayın.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: onaylanmamış Azure kaynakları için izleyici

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerinde oluşturulabilen kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:

- İzin verilmeyen kaynak türleri

- İzin verilen kaynak türleri

Aboneliklerinizde kaynakları sorgulamak/öğrenmek için Azure Kaynak Grafiği ' ni kullanın. Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Kaynak Graf ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="69-use-only-approved-azure-services"></a>6,9: yalnızca onaylanan Azure hizmetlerini kullanın

**Rehberlik**: aşağıdaki yerleşik ilke tanımlarını kullanarak müşteri aboneliklerine oluşturulabilecek kaynak türlerine kısıtlamalar koymak Için Azure ilkesini kullanın:
- İzin verilmeyen kaynak türleri
- İzin verilen kaynak türleri

Aboneliklerinizin içindeki kaynakları sorgulamak/öğrenmek için Azure Kaynak grafiğini kullanın. Ortamda bulunan tüm Azure kaynaklarının onaylandığından emin olun.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilkesiyle belirli bir kaynak türünü reddetme](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

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

**Rehberlik**: adanmış SQL havuzlarınızla ilgili kaynakların yapılandırılmasını denetlemek veya zorlamak üzere özel ilkeler oluşturmak için, "Microsoft. SQL" ad alanındaki Azure ilke diğer adlarını kullanın. Azure veritabanları/sunucu için yerleşik ilke tanımlarından da yararlanabilirsiniz, örneğin:

- SQL Server 'lar üzerinde tehdit algılamayı dağıtma
- SQL Server bir sanal ağ hizmeti uç noktası kullanmalıdır

- [Kullanılabilir Azure Ilkesi diğer adlarını görüntüleme](/powershell/module/az.resources/get-azpolicyalias)

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: güvenli Azure Kaynak yapılandırmalarının bakımını yapma

**Kılavuz**: Azure kaynaklarınız genelinde güvenli ayarları zorlamak Için Azure ilkesi [reddetme] ve [dağıtım yoksa dağıt] kullanın.

- [Azure İlkesi'ni yapılandırma ve yönetme](../governance/policy/tutorials/create-and-manage.md)

- [Azure Ilke efektlerini anlama](../governance/policy/concepts/effects.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Azure kaynaklarının yapılandırmasını güvenli bir şekilde depolayın

**Kılavuz**: özel Azure ilke tanımları kullanıyorsanız, kodunuzu güvenli bir şekilde depolamak ve yönetmek Için Azure devops veya Azure Repos kullanın.

- [Azure DevOps 'da kod depolama](/azure/devops/repos/git/gitworkflow)

- [Azure Repos belgeleri](/azure/devops/repos/)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Azure kaynakları için yapılandırma yönetimi araçları dağıtma

**Rehberlik**: uygulanamaz; Azure SYNAPSE SQL, yapılandırılabilir güvenlik ayarlarına sahip değil.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Azure kaynakları için otomatik yapılandırma izlemeyi uygulama

**Rehberlik**: adanmış SQL havuzunuz ile ilgili herhangi bir kaynak için temel taramalar gerçekleştirmek üzere Azure Güvenlik Merkezi 'nden yararlanın.

- [Azure Güvenlik Merkezi 'nde öneriler nasıl düzeltileceği](../security-center/security-center-remediate-recommendations.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure gizli dizilerini güvenli bir şekilde yönetin

**Rehberlik**: Azure Key Vault içindeki müşteri tarafından yönetilen anahtarlarla saydam veri şifrelemesi (tde), otomatik olarak oluşturulan veritabanı şifreleme anahtarı (dek) Ile TDE koruyucusu adlı, müşteri tarafından yönetilen bir asimetrik anahtarla şifrelemeye izin verir. Bu Ayrıca, Saydam Veri Şifrelemesi için genellikle Kendi Anahtarını Getir (BYOK) desteği olarak adlandırılır. BYOK senaryosunda, TDE koruyucusu müşterinin sahip olduğu ve yönetilen bir Azure Key Vault depolanır. Ayrıca, Azure Key Vault ' de geçici silme özelliğinin etkinleştirildiğinden emin olun.

- [Azure Key Vault 'tan müşteri tarafından yönetilen anahtarla TDE 'yi etkinleştirme](../azure-sql/database/transparent-data-encryption-byok-configure.md)

- [Azure Key Vault 'da geçici silme nasıl etkinleştirilir](../key-vault/general/key-vault-recovery.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: kimlikleri güvenli ve otomatik olarak yönetme

**Rehberlik**: Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik ile Azure hizmetleri sağlamak Için Yönetilen kimlikler kullanın. Yönetilen kimlikler, kodunuzda kimlik bilgileri olmadan Azure Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmanıza olanak sağlar.

- [Öğretici: Azure SQL hizmetine erişmek için Windows VM sistem tarafından atanan yönetilen kimlik kullanma](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Yönetilen kimlikleri yapılandırma](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: kodunuzda kimlik bilgilerini tanımlamak Için kimlik bilgisi tarayıcısı uygulayın. Kimlik Bilgisi Tarayıcısı ayrıca bulunan kimlik bilgilerinin Azure Key Vault gibi daha güvenlik konumlara aktarılmasını sağlar.

- [Kimlik bilgisi tarayıcısı kurulumu](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: işlem dışı Azure kaynaklarına yüklenecek dosyaları önceden Tara

**Rehberlik**: Microsoft 'un kötü amaçlı yazılımdan koruma özelliği, Azure hizmetleri 'ni destekleyen temel alınan konakta (örneğin, Azure SYNAPSE SQL) etkinleştirilir; Ancak, müşteri içeriği üzerinde çalışmaz.

App Service, Data Lake Storage, BLOB depolama, Azure SQL Server vb. gibi işlem dışı Azure kaynaklarına yüklenen tüm içerikleri önceden tarayın. Microsoft bu örneklerdeki verilerinize erişemez.

- [Azure Cloud Services ve sanal makineler için Microsoft Antimalware 'i anlayın](../security/fundamentals/antimalware.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="data-recovery"></a>Veri Kurtarma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: veri kurtarma](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: düzenli otomatik arka pencerelere emin olun

**Rehberlik**: adanmış SQL havuzunuzun anlık görüntüleri, yedi gün boyunca kullanılabilen geri yükleme noktaları oluşturma gününde otomatik olarak alınır. Bu saklama dönemi değiştirilemez. Adanmış SQL havuzu sekiz saatlik bir kurtarma noktası hedefini (RPO) destekler. Son yedi gün içinde alınan anlık görüntülerden herhangi birinden, birincil bölgedeki veri Ambarınızı geri yükleyebilirsiniz. Gerekirse, anlık görüntüleri el ile de tetikleyebileceğinizi unutmayın.

- [Adanmış SQL havuzunda yedekleme ve geri yükleme](sql-data-warehouse/backup-and-restore.md)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: tam sistem yedeklemeleri gerçekleştirin ve müşterinin yönettiği tüm anahtarları yedekleyin

**Rehberlik**: veri ambarınızın anlık görüntüleri, yedi gün boyunca kullanılabilen geri yükleme noktaları oluşturma gününde otomatik olarak alınır. Bu saklama dönemi değiştirilemez. Adanmış SQL havuzu sekiz saatlik bir kurtarma noktası hedefini (RPO) destekler. Son yedi gün içinde alınan anlık görüntülerden herhangi birinden, birincil bölgedeki veri Ambarınızı geri yükleyebilirsiniz. Gerekirse, anlık görüntüleri el ile de tetikleyebileceğinizi unutmayın.

Veritabanı şifreleme anahtarınızı şifrelemek için müşteri tarafından yönetilen bir anahtar kullanıyorsanız, anahtarınızın yedeklendiğinden emin olun.

- [Adanmış SQL havuzunda yedekleme ve geri yükleme](sql-data-warehouse/backup-and-restore.md)

- [Azure Key Vault anahtarlarını yedekleme](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Sorumluluk**: Paylaşılan

**Azure Güvenlik Merkezi izleme**: [Azure Güvenlik kıyaslaması](/azure/governance/policy/samples/azure-security-benchmark) , Güvenlik Merkezi için varsayılan Ilke girişimidir ve [Güvenlik Merkezi 'nin önerilerine](/azure/security-center/security-center-recommendations)yöneliktir. Bu denetimle ilgili Azure Ilke tanımları, Güvenlik Merkezi tarafından otomatik olarak etkinleştirilir. Bu denetimle ilgili uyarılar, ilgili hizmetler için bir [Azure Defender](/azure/security-center/azure-defender) planı gerektirebilir.

**Azure ilkesi yerleşik tanımları-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: müşteri tarafından yönetilen anahtarlar dahil tüm yedeklemeleri doğrulama

**Rehberlik**: anlık görüntülerinizin geçerli olduğundan emin olmak için geri yükleme noktalarınızı düzenli olarak test edin. Mevcut bir adanmış SQL havuzunu geri yükleme noktasından geri yüklemek için Azure portal ya da PowerShell kullanabilirsiniz. Yedeklenen müşteri tarafından yönetilen anahtarların test geri yüklemesi.

- [Azure Key Vault anahtarlarını geri yükleme](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Adanmış SQL havuzunda yedekleme ve geri yükleme](sql-data-warehouse/backup-and-restore.md)

- [Mevcut ayrılmış bir SQL havuzunu geri yükleme](sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: yedeklemelerin ve müşteri tarafından yönetilen anahtarların korunmasını sağlayın

**Kılavuz**: Azure SQL veritabanı 'nda, veritabanı yedeklerini 10 yıla kadar ayrı Azure Blob depolama kapsayıcılarında otomatik olarak saklamak için uzun süreli bir yedekleme bekletme ILKESIYLE (LTR) tek veya havuza alınmış bir veritabanı yapılandırabilirsiniz. Azure depolama 'daki veriler, 256 bit AES şifrelemesi kullanılarak şifrelenmiş ve şifresi çözülür, en güçlü blok şifrelemeleri kullanılabilir ve FIPS 140-2 uyumludur.

Varsayılan olarak, bir depolama hesabındaki veriler Microsoft tarafından yönetilen anahtarlarla şifrelenir. Verilerinizin şifrelenmesi için Microsoft tarafından yönetilen anahtarları kullanabilir veya kendi anahtarlarınız ile şifrelemeyi yönetebilirsiniz. Kendi anahtarlarınızı Key Vault ile yönetiyorsanız, geçici silme özelliğinin etkinleştirildiğinden emin olun.

- [Azure SQL veritabanı uzun süreli yedekleme bekletmesini yönetme](../azure-sql/database/long-term-backup-retention-configure.md)

- [Bekleyen veri için Azure Depolama şifrelemesi](../storage/common/storage-service-encryption.md)

- [Key Vault 'da geçici silme nasıl etkinleştirilir](../storage/blobs/soft-delete-blob-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: personel rollerinin yanı sıra olay işleme/yönetim aşamalarını tanımlayan yazılı olay yanıt planları olduğundan emin olun.

- [Azure Güvenlik Merkezi 'nde Iş akışı otomasyonlarını yapılandırma](../security-center/security-center-planning-and-operations-guide.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Rehberlik**: Güvenlik Merkezi, her uyarıya katılma sırasını önceliklendirmenize yardımcı olmak için uyarılara önem derecesi atar. böylece, bir kaynak tehlikeye girdiğinde, hemen bu işe başlayabilirsiniz. Önem derecesi, güvenlik merkezi 'nin, uyarıyı vermek için kullanılan bulma veya ölçümde ne kadar uygun olduğunu ve uyarıya yönelik etkinliğin arkasında kötü amaçlı bir amaç olduğunu bilmenin güven düzeyini temel alır.

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve açıkları belirleyip planı gerektiği şekilde gözden geçirin.

- [NıST yayınına başvurabilirsiniz: BT planları ve özellikleri için test, eğitim ve alıştırma programları](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

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

**Rehberlik**: Penettim testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak Için lütfen Microsoft katılım kurallarını izleyin: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [Microsoft 'un Microsoft tarafından yönetilen bulut altyapısına, hizmetlerine ve uygulamalarına göre kırmızı ekip oluşturma ve canlı site sızma sınamasını yürütme hakkında daha fazla bilgi edinebilirsiniz.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](/azure/security/benchmarks/overview)
- [Azure güvenlik temelleri](/azure/security/benchmarks/security-baselines-overview) hakkında daha fazla bilgi edinin
