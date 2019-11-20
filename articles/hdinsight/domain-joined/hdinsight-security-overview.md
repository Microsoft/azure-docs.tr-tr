---
title: Azure HDInsight 'ta Kurumsal güvenliğe genel bakış
description: Azure HDInsight 'ta kurumsal güvenlik sağlamaya yönelik çeşitli yöntemleri öğrenin.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: overview
ms.date: 09/23/2019
ms.openlocfilehash: 7fadb17476c2a071de767573994bb1120b476cdf
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053731"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Azure HDInsight 'ta Kurumsal güvenliğe genel bakış

Azure HDInsight, kurumsal güvenlik ihtiyaçlarınızı karşılamak için çeşitli yöntemler sunar. Bu çözümlerin çoğu varsayılan olarak etkinleştirilmez. Bu esneklik, sizin için en önemli olan güvenlik özelliklerini seçmenizi sağlar ve istemediğiniz özellikler için ödeme yapmaktan kaçınmanıza yardımcı olur. Bu aynı zamanda, kurulum ve ortamınız için doğru çözümlerin etkinleştirildiğinden emin olmak sizin sorumluluğunuzdadır.

Bu makale, güvenlik çözümlerini dört geleneksel güvenlik, kimlik doğrulaması, yetkilendirme ve şifreleme satırları üzerinde bölerek güvenlik çözümlerine bakar.

Bu makalede ayrıca HDInsight kümeleri için Active Directory tabanlı kimlik doğrulaması, çoklu Kullanıcı desteği ve rol tabanlı erişim denetimi sağlayan **Azure hdınsight kurumsal güvenlik paketi (ESP)** açıklanır.

## <a name="enterprise-security-pillars"></a>Kurumsal Güvenlik pilsi

Kurumsal Güvenlik 'e bakmaya yönelik bir yöntem, güvenlik çözümlerini denetim türüne göre dört ana gruba böler. Bu gruplar ayrıca güvenlik ile aynı şekilde adlandırılır ve aşağıdakiler şunlardır: çevre güvenliği, kimlik doğrulama, yetkilendirme ve şifreleme.

### <a name="perimeter-security"></a>Çevre güvenliği

HDInsight 'ta çevre güvenliği [sanal ağlar](../hdinsight-plan-virtual-network-deployment.md)aracılığıyla elde edilir. Kurumsal Yönetici, sanal ağ (VNET) içinde bir küme oluşturabilir ve sanal ağa erişimi kısıtlamak için ağ güvenlik grupları (NSG) kullanabilir. Yalnızca gelen NSG kurallarında izin verilen IP adresleri HDInsight kümesiyle iletişim kurabiliyor. Bu yapılandırma, çevre güvenliği sağlar.

VNET 'te dağıtılan tüm kümelerin, küme ağ geçitlerine özel HTTP erişimi için sanal ağın içindeki özel bir IP 'ye çözümlenen özel bir uç noktası da olur.

### <a name="authentication"></a>Kimlik Doğrulaması

HDInsight 'tan [Kurumsal güvenlik paketi](apache-domain-joined-architecture.md) , Active Directory tabanlı kimlik doğrulaması, çoklu Kullanıcı desteği ve rol tabanlı erişim denetimi sağlar. Active Directory tümleştirme [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md)kullanılarak elde edilir. Bu yetenekler sayesinde, yönetilen bir Active Directory etki alanına katılmış bir HDInsight kümesi oluşturabilirsiniz. Bundan sonra, kümeden kimlik doğrulaması yapan ve üzerinde oturum açabilen çalışanların bir listesini yapılandırabilirsiniz.

Bu kurulumla, kurumsal çalışanlar, kendi etki alanı kimlik bilgilerini kullanarak küme düğümlerinde oturum açabilir. Ayrıca, küme ile etkileşimde bulunmak için Apache ambarı görünümleri, ODBC, JDBC, PowerShell ve REST API 'Ler gibi diğer onaylanan uç noktalarla kimlik doğrulaması yapmak için etki alanı kimlik bilgilerini de kullanabilirler. 

### <a name="authorization"></a>Yetkilendirme

Çoğu kuruluşun takip eden en iyi uygulama, her çalışanın kurumsal kaynaklara erişiminin olmadığından emin olmanızı sağlar. Benzer şekilde, yönetici, küme kaynakları için rol tabanlı erişim denetimi ilkeleri tanımlayabilir. Bu yalnızca ESP kümelerinde kullanılabilir.

Hadoop Yöneticisi, Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md) ve [Kafka](apache-domain-joined-run-kafka.md) için Apache Ranger içindeki eklentileri kullanarak rol tabanlı erişim denetimi 'ni (RBAC) güvenli bir şekilde yapılandırabilir. RBAC ilkelerini yapılandırmak, izinleri kuruluştaki bir rolle ilişkilendirmenize olanak tanır. Bu soyutlama katmanı, kişilerin yalnızca iş sorumluluklarını gerçekleştirmek için gerekli izinlere sahip olmasını kolaylaştırır. Ranger Ayrıca çalışanların veri erişimini ve erişim denetimi ilkelerine yapılan değişiklikleri denetlemenize olanak tanır.

Örneğin yönetici [Apache Ranger](https://ranger.apache.org/)’ı Hive için erişim denetim ilkeleri belirleyecek şekilde yapılandırabilir. Bu işlevsellik, satır düzeyinde ve sütun düzeyinde filtreleme (veri maskeleme) ve duyarlı verileri yetkisiz kullanıcılardan filtrelemesini sağlar.

### <a name="auditing"></a>Denetim

Küme kaynaklarına erişimi denetleme ve veriler, kaynakların yetkisiz veya istenmeden erişimini izlemek için gereklidir. HDInsight küme kaynaklarını yetkisiz kullanıcılardan korumak ve verilerin güvenliğini sağlamak önemlidir.

Yönetici, HDInsight kümesi kaynaklarına ve verilerine yönelik tüm erişimi görüntüleyebilir ve rapor edebilir. Yönetici ayrıca Apache Ranger desteklenen uç noktalarında oluşturulan erişim denetim ilkelerine yapılan tüm değişiklikleri görüntüleyebilir ve rapor edebilir. 

Apache Ranger ve ambarı denetim günlüklerine ve SSH erişim günlüklerine erişmek için [Azure izleyicisini etkinleştirin](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) ve denetim kayıtları sağlayan tabloları görüntüleyin.

### <a name="encryption"></a>Şifreleme

Verilerin korunması, kurumsal güvenlik ve uyumluluk gereksinimlerini karşılamak için önemlidir. Yetkisiz çalışanların verilerine erişimi sınırlandırma ile birlikte şifrelemeniz gerekir.

HDInsight kümeleri için veri depoları, Azure Blob depolama ve Azure Data Lake Storage 1./Gen2, bekleyen verilerin şeffaf sunucu tarafı [şifrelemesini](../../storage/common/storage-service-encryption.md) destekler. Güvenli HDInsight kümeleri, bekleyen verilerin sunucu tarafı şifrelemesi özelliği ile sorunsuz bir şekilde çalışır.

### <a name="compliance"></a>Uyumluluk

Azure uyumluluk teklifleri, bağımsız üçüncü taraf denetim firmaları tarafından oluşturulan resmi sertifikalar, belirlediğimizi karşıladığımızı, doğrulamalar, yetkilendirmeler ve değerlendirmelerin yanı sıra, sözleşmeli değişiklik DTU 'ları gibi çeşitli türlerde güvenlerin temel alınarak yapılır. Microsoft tarafından üretilen kendi kendine değerlendirmeler ve müşteri Kılavuzu belgeleri. HDInsight uyumluluk bilgileri için bkz. [Microsoft Güven Merkezi](https://www.microsoft.com/trust-center) ve [Microsoft Azure uyumluluğuna genel bakış](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Paylaşılan sorumluluk modeli

Aşağıdaki görüntüde ana sistem güvenlik alanı ve her birinde kullanabileceğiniz güvenlik çözümleri özetlenmektedir. Ayrıca, hangi güvenlik alanlarının bir müşteri olarak sorumluluğunda olduğunu ve hangi alanların hizmet sağlayıcısı olarak HDInsight 'ın sorumluluğu olduğunu vurgular.

![HDInsight paylaşılan sorumlulukları diyagramı](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

Aşağıdaki tabloda her güvenlik çözümü türü için kaynakların bağlantıları verilmiştir.

| Güvenlik alanı | Kullanılabilir çözümler | Sorumlu parti |
|---|---|---|
| Veri erişim güvenliği | Azure Data Lake Storage 1. ve Gen2 için [erişim denetim listelerinin ACL 'lerini](../../storage/blobs/data-lake-storage-access-control.md) yapılandırma  | Müşteri |
|  | Depolama hesaplarında ["güvenli aktarım gerekli"](../../storage/common/storage-require-secure-transfer.md) özelliğini etkinleştirin. | Müşteri |
|  | [Azure depolama güvenlik duvarlarını](../../storage/common/storage-network-security.md) ve sanal ağları yapılandırma | Müşteri |
|  | Cosmos DB ve [Azure SQL veritabanı](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) için [Azure sanal ağ hizmet uç noktalarını](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) yapılandırma | Müşteri |
|  | Yoldaki veriler için [TLS şifrelemenin](../../storage/common/storage-security-tls.md) etkinleştirildiğinden emin olun. | Müşteri |
|  | Azure depolama şifrelemesi için [müşteri tarafından yönetilen anahtarları](../../storage/common/storage-encryption-keys-portal.md) yapılandırma | Müşteri |
| Uygulama ve ara yazılım güvenliği | AAD-DS ile tümleştirme ve [kimlik doğrulamasını yapılandırma](apache-domain-joined-configure-using-azure-adds.md) | Müşteri |
|  | [Apache Ranger yetkilendirme](apache-domain-joined-run-hive.md) ilkelerini yapılandırma | Müşteri |
|  | [Azure izleyici günlüklerini](../hdinsight-hadoop-oms-log-analytics-tutorial.md) kullanma | Müşteri |
| İşletim sistemi güvenliği | En son güvenli temel görüntüyle kümeler oluşturun | Müşteri |
|  | Düzenli aralıklarla [Işletim sistemi düzeltme eki uygulama](../hdinsight-os-patching.md) | Müşteri |
| Ağ güvenliği | [Sanal ağ](../hdinsight-plan-virtual-network-deployment.md) yapılandırma |
|  | [Gelen ağ güvenlik grubu (NSG) kurallarını](../hdinsight-plan-virtual-network-deployment.md#networktraffic) yapılandırma | Müşteri |
|  | Güvenlik duvarıyla [giden trafik kısıtlamasını](../hdinsight-restrict-outbound-traffic.md) yapılandırma (Önizleme) | Müşteri |
| Sanallaştırılmış altyapı | Yok | HDInsight (bulut sağlayıcısı) |
| Fiziksel altyapı güvenliği | Yok | HDInsight (bulut sağlayıcısı) |

## <a name="next-steps"></a>Sonraki adımlar

* [ESP ile HDInsight kümelerini planlayın](apache-domain-joined-architecture.md)
* [HDInsight kümelerini ESP ile yapılandırma](apache-domain-joined-configure.md)
* [HDInsight kümelerini ESP ile yönetme](apache-domain-joined-manage.md)
