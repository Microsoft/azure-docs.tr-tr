---
title: Azure HDInsight 'ta Kurumsal güvenliğe genel bakış
description: Azure HDInsight 'ta kurumsal güvenlik sağlamaya yönelik çeşitli yöntemleri öğrenin.
ms.service: hdinsight
ms.topic: overview
ms.custom: seoapr2020
ms.date: 08/24/2020
ms.openlocfilehash: 713559a747b58e65d04c8310755baedc93db929e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863165"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Azure HDInsight 'ta Kurumsal güvenliğe genel bakış

Azure HDInsight, kurumsal güvenlik ihtiyaçlarınızı karşılamak için çeşitli yöntemler sunar. Bu çözümlerin çoğu varsayılan olarak etkinleştirilmez. Bu esneklik, sizin için en önemli olan güvenlik özelliklerini seçmenizi sağlar ve istemediğiniz özellikler için ödeme yapmaktan kaçınmanıza yardımcı olur. Bu esneklik Ayrıca, kurulum ve ortamınız için doğru çözümlerin etkinleştirildiğinden emin olmak için sizin sorumluluğunuzdadır.

Bu makale, güvenlik çözümlerini dört geleneksel güvenlik, kimlik doğrulaması, yetkilendirme ve şifreleme ile ayırarak güvenlik çözümlerine bakar.

Bu makalede ayrıca HDInsight kümeleri için Active Directory tabanlı kimlik doğrulaması, çoklu Kullanıcı desteği ve rol tabanlı erişim denetimi sağlayan **Azure hdınsight kurumsal güvenlik paketi (ESP)** açıklanır.

## <a name="enterprise-security-pillars"></a>Kurumsal Güvenlik pilsi

Kurumsal Güvenlik 'e bakmaya yönelik bir yöntem, güvenlik çözümlerini denetim türüne göre dört ana gruba böler. Bu gruplar ayrıca güvenlik ile aynı şekilde adlandırılır ve aşağıdaki türlerdir: çevre güvenliği, kimlik doğrulama, yetkilendirme ve şifreleme.

### <a name="perimeter-security"></a>Çevre güvenliği

HDInsight 'ta çevre güvenliği [sanal ağlar](../hdinsight-plan-virtual-network-deployment.md)aracılığıyla elde edilir. Kurumsal Yönetici, sanal ağ (VNET) içinde bir küme oluşturabilir ve sanal ağa erişimi kısıtlamak için ağ güvenlik grupları (NSG) kullanabilir. Yalnızca gelen NSG kurallarında izin verilen IP adresleri HDInsight kümesiyle iletişim kurabilir. Bu yapılandırma, çevre güvenliği sağlar.

VNET 'te dağıtılan tüm kümelerin özel bir uç noktası da olur. Uç noktası, küme ağ geçitlerine özel HTTP erişimi için VNET 'in içindeki özel bir IP 'ye çözümlenir.

### <a name="authentication"></a>Kimlik Doğrulaması

HDInsight 'tan [Kurumsal güvenlik paketi](apache-domain-joined-architecture.md) , Active Directory tabanlı kimlik doğrulaması, çoklu Kullanıcı desteği ve rol tabanlı erişim denetimi sağlar. Active Directory tümleştirme [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md)kullanılarak elde edilir. Bu özelliklerde, bir Active Directory etki alanına katılmış bir HDInsight kümesi oluşturabilirsiniz. Daha sonra, kuruluş içinden kümeye kimlik doğrulayabilecek çalışanların bir listesini yapılandırın.

Bu kurulumla, kurumsal çalışanlar, kendi etki alanı kimlik bilgilerini kullanarak küme düğümlerinde oturum açabilir. Ayrıca, diğer onaylanan uç noktalarla kimlik doğrulaması yapmak için etki alanı kimlik bilgilerini de kullanabilirler. Küme ile etkileşimde bulunmak için Apache ambarı görünümleri, ODBC, JDBC, PowerShell ve REST API 'Leri gibi.

### <a name="authorization"></a>Yetkilendirme

Çoğu kuruluşun en iyi uygulaması, her çalışanın tüm kurumsal kaynaklara tam erişimi olmadığından emin olmanızı sağlar. Benzer şekilde, yönetici, küme kaynakları için rol tabanlı erişim denetimi ilkeleri tanımlayabilir. Bu eylem yalnızca ESP kümelerinde kullanılabilir.

Hadoop Yöneticisi rol tabanlı erişim denetimi 'ni (RBAC) yapılandırabilir. Yapılandırmalarda Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md)ve [Kafka](apache-domain-joined-run-kafka.md) ile Apache Ranger eklentileri vardır. RBAC ilkelerini yapılandırmak, izinleri kuruluştaki bir rolle ilişkilendirmenize olanak tanır. Bu soyutlama katmanı, kişilerin yalnızca iş sorumluluklarını yapmak için gerekli izinlere sahip olmasını kolaylaştırır. Ranger Ayrıca çalışanların veri erişimini ve erişim denetimi ilkelerine yapılan değişiklikleri denetlemenize olanak tanır.

Örneğin yönetici [Apache Ranger](https://ranger.apache.org/)’ı Hive için erişim denetim ilkeleri belirleyecek şekilde yapılandırabilir. Bu işlevsellik, satır düzeyinde ve sütun düzeyinde filtrelemeyi (veri maskeleme) sağlar. Ve duyarlı verileri yetkisiz kullanıcılardan filtreler.

### <a name="auditing"></a>Denetim

Denetim kümesi kaynak erişimi, kaynakların yetkisiz veya istenmeden erişimini izlemek için gereklidir. Küme kaynaklarının yetkisiz erişimden korunması önemlidir.

Yönetici, HDInsight kümesi kaynaklarına ve verilerine yönelik tüm erişimi görüntüleyebilir ve rapor edebilir. Yönetici, erişim denetimi ilkelerine değişiklikleri görüntüleyip rapor edebilir.

Apache Ranger ve ambarı denetim günlüklerine ve SSH erişim günlüklerine erişmek için [Azure izleyicisini etkinleştirin](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) ve denetim kayıtları sağlayan tabloları görüntüleyin.

### <a name="encryption"></a>Şifreleme

Verilerin korunması, kurumsal güvenlik ve uyumluluk gereksinimlerini karşılamak için önemlidir. Yetkisiz çalışanların verilerine erişimi sınırlandırma ile birlikte şifrelemeniz gerekir.

HDInsight, hem platform tarafından yönetilen hem de [müşteri tarafından yönetilen anahtarlarla](../disk-encryption.md)bekleyen veri şifrelemesini destekler. İletimde verilerin şifrelenmesi hem TLS hem de IPSec ile işlenir. Daha fazla bilgi için bkz. [Azure HDInsight için geçişte şifreleme](encryption-in-transit.md) .

### <a name="compliance"></a>Uyumluluk

Azure uyumluluk teklifleri, resmi sertifikalar da dahil olmak üzere çeşitli türlerde türler temel alır. Ayrıca, belirlediğimizi karşıladığımızı, doğrulamalar ve yetkilendirmeler. Bağımsız üçüncü taraf denetim firmaları tarafından oluşturulan değerlendirmeler. Microsoft tarafından üretilen sözleşme, kendi kendine değerlendirmeler ve müşteri Kılavuzu belgeleri. HDInsight uyumluluk bilgileri için bkz. [Microsoft Güven Merkezi](https://www.microsoft.com/trust-center) ve [Microsoft Azure uyumluluğuna genel bakış](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Paylaşılan sorumluluk modeli

Aşağıdaki görüntüde ana sistem güvenlik alanı ve her birinde kullanabileceğiniz güvenlik çözümleri özetlenmektedir. Ayrıca, müşteri olarak hangi güvenlik alanlarının sorumluluğunda olduğunu vurgular. Ve hangi alanlarda, HDInsight 'ın hizmet sağlayıcısı olarak sorumluluğu vardır.

:::image type="content" source="./media/hdinsight-security-overview/hdinsight-shared-responsibility.png" alt-text="HDInsight paylaşılan sorumlulukları diyagramı" border="false":::

Aşağıdaki tabloda her güvenlik çözümü türü için kaynakların bağlantıları verilmiştir.

| Güvenlik alanı | Kullanılabilir çözümler | Sorumlu parti |
|---|---|---|
| Veri erişim güvenliği | Azure Data Lake Storage 1. ve Gen2 için [erişim denetim listelerinin ACL 'lerini](../../storage/blobs/data-lake-storage-access-control.md) yapılandırma  | Müşteri |
|  | Depolama hesaplarında ["güvenli aktarım gerekli"](../../storage/common/storage-require-secure-transfer.md) özelliğini etkinleştirin. | Müşteri |
|  | [Azure depolama güvenlik duvarlarını](../../storage/common/storage-network-security.md) ve sanal ağları yapılandırma | Müşteri |
|  | Cosmos DB ve [Azure SQL veritabanı](../../azure-sql/database/vnet-service-endpoint-rule-overview.md) için [Azure sanal ağ hizmet uç noktalarını](../../virtual-network/virtual-network-service-endpoints-overview.md) yapılandırma | Müşteri |
|  | [Yoldaki şifreleme](./encryption-in-transit.md) özelliğinin, küme içi ILETIŞIM için TLS ve IPSec kullanmak üzere etkinleştirildiğinden emin olun. | Müşteri |
|  | Azure depolama şifrelemesi için [müşteri tarafından yönetilen anahtarları](../../storage/common/customer-managed-keys-configure-key-vault.md) yapılandırma | Müşteri |
|  | [Müşteri kasayı](../../security/fundamentals/customer-lockbox-overview.md) kullanarak Azure desteği ile verilerinize erişimi denetleme | Müşteri |
| Uygulama ve ara yazılım güvenliği | AAD-DS ile tümleştirme ve [ESP yapılandırma](apache-domain-joined-configure-using-azure-adds.md) veya [OAuth kimlik doğrulaması için Hib](identity-broker.md) kullanma| Müşteri |
|  | [Apache Ranger yetkilendirme](apache-domain-joined-run-hive.md) ilkelerini yapılandırma | Müşteri |
|  | [Azure izleyici günlüklerini](../hdinsight-hadoop-oms-log-analytics-tutorial.md) kullanma | Müşteri |
| İşletim sistemi güvenliği | En son güvenli temel görüntüyle kümeler oluşturun | Müşteri |
|  | Düzenli aralıklarla [Işletim sistemi düzeltme eki uygulama](../hdinsight-os-patching.md) | Müşteri |
|  | [VM 'ler Için CMK disk şifrelemesi](../disk-encryption.md) sağlayın | Müşteri |
| Ağ güvenliği | [Sanal ağ](../hdinsight-plan-virtual-network-deployment.md) yapılandırma |
|  | [Gelen ağ güvenlik grubu (NSG) kurallarını](../control-network-traffic.md) veya [özel bağlantıyı](../hdinsight-private-link.md) yapılandırma | Müşteri |
|  | Güvenlik duvarıyla [giden trafik kısıtlamasını](../hdinsight-restrict-outbound-traffic.md) yapılandırma | Müşteri |
|  | Küme düğümleri arasındaki [geçişte IPSec şifrelemesini](encryption-in-transit.md) yapılandırma | Müşteri |
| Sanallaştırılmış altyapı | Yok | HDInsight (bulut sağlayıcısı) |
| Fiziksel altyapı güvenliği | Yok | HDInsight (bulut sağlayıcısı) |

## <a name="next-steps"></a>Sonraki adımlar

* [ESP ile HDInsight kümelerini planlayın](apache-domain-joined-architecture.md)
* [HDInsight kümelerini ESP ile yapılandırma](./apache-domain-joined-configure-using-azure-adds.md)
* [HDInsight kümelerini ESP ile yönetme](apache-domain-joined-manage.md)
