---
title: Apache Hadoop, Apache Spark, Apache Kafka ve daha fazlasıyla HDInsight'ta küme oluşturma
description: HDInsight için Hadoop, Kafka, Spark, HBase, R Server veya fırtınası kümelerini tarayıcıdan, klasik Azure CLı, Azure PowerShell, REST veya SDK 'dan ayarlayın.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18, devx-track-azurecli
ms.date: 08/06/2020
ms.openlocfilehash: 3d1059ab46ff0e3722d1f6538bba61cdc4e8cb59
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482696"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Apache Hadoop, Apache Spark, Apache Kafka ve daha fazlasıyla HDInsight'ta küme oluşturma

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

HDInsight 'ta Apache Hadoop, Apache Spark, Apache Kafka, etkileşimli sorgu, Apache HBase, ML Hizmetleri veya Apache Storm ayarlamayı ve yapılandırmayı öğrenin. Ayrıca, kümeleri özelleştirmeyi ve bir etki alanına katılarak güvenlik eklemeyi öğrenin.

Hadoop kümesi, görevlerin dağıtılmış işlemesi için kullanılan birkaç sanal makineden (düğümden) oluşur. Azure HDInsight, tek tek düğümlerin yüklenmesi ve yapılandırılmasına ilişkin uygulama ayrıntılarını işler, bu nedenle yalnızca genel yapılandırma bilgilerini sağlamanız gerekir.

> [!IMPORTANT]  
> HDInsight kümesi faturalandırması küme oluşturulduğunda başlar ve küme silindiğinde sona erer. Fatura dakikalara eşit olarak dağıtıldığından, kullanılmayan kümelerinizi mutlaka silmelisiniz. [Bir kümeyi silmeyi öğrenin.](hdinsight-delete-cluster.md)

Birden çok kümeyi birlikte kullanıyorsanız, bir sanal ağ oluşturmak isteyeceksiniz ve bir Spark kümesi kullanıyorsanız, Hive ambarı bağlayıcısını de kullanmak isteyeceksiniz. Daha fazla bilgi için bkz. [Azure HDInsight için bir sanal ağ planlayın](./hdinsight-plan-virtual-network-deployment.md) ve [Hive ambarı Bağlayıcısı ile Apache Spark ve Apache Hive tümleştirin](interactive-query/apache-hive-warehouse-connector.md).

## <a name="cluster-setup-methods"></a>Küme kurulum yöntemleri

Aşağıdaki tabloda, bir HDInsight kümesi kurmak için kullanabileceğiniz farklı yöntemler gösterilmektedir.

| İle oluşturulan kümeler | Web tarayıcısı | Komut satırı | REST API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure portalı](hdinsight-hadoop-create-linux-clusters-portal.md) |âœ" |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |âœ" |âœ" |âœ" |âœ" |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |âœ" |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |âœ" |&nbsp; |&nbsp; |
| [kıvr](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |âœ" |âœ" |&nbsp; |
| [Azure Resource Manager şablonları](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |âœ" |&nbsp; |&nbsp; |

Bu makalede, bir HDInsight kümesi oluşturabileceğiniz [Azure Portal](https://portal.azure.com)kurulum işlemi adım adım açıklanmaktadır.

## <a name="basics"></a>Temel Bilgiler

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png" alt-text="HDInsight oluşturma seçenekleri özel hızlı":::

### <a name="project-details"></a>Proje ayrıntıları

[Azure Resource Manager](../azure-resource-manager/management/overview.md) , Azure [kaynak grubu](../azure-resource-manager/management/overview.md#resource-groups)olarak adlandırılan, uygulamanızdaki kaynaklarla bir grup olarak çalışmanıza yardımcı olur. Uygulamanıza yönelik tüm kaynakları tek bir eşgüdümlü işlemle dağıtabilir, güncelleştirebilir, izleyebilir veya silebilirsiniz.

### <a name="cluster-details"></a>Küme ayrıntıları

#### <a name="cluster-name"></a>Küme adı

HDInsight kümesi adları aşağıdaki kısıtlamalara sahiptir:

* İzin verilen karakterler: a-z, 0-9, A-Z
* En fazla uzunluk: 59
* Ayrılmış adlar: uygulamalar
* Tüm abonelikler genelinde tüm Azure için küme adlandırma kapsamı. Bu nedenle, küme adı Dünya genelinde benzersiz olmalıdır.
* İlk altı karakter bir sanal ağ içinde benzersiz olmalıdır

#### <a name="region"></a>Region

Küme konumunu açık bir şekilde belirtmeniz gerekmez: küme, varsayılan depolamayla aynı konumda. Desteklenen bölgelerin listesi için [HDInsight fiyatlandırması](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)üzerindeki **bölge** açılan listesini seçin.

#### <a name="cluster-type"></a>Küme türü

Azure HDInsight Şu anda, her biri belirli işlevleri sağlamak üzere bir bileşen kümesi olan aşağıdaki küme türlerini sağlamaktadır.

> [!IMPORTANT]  
> HDInsight kümeleri, her biri tek bir iş yükü veya teknoloji için çeşitli türlerde kullanılabilir. Bir kümede fırtınası ve HBase gibi birden çok türü birleştiren bir küme oluşturmak için desteklenen bir yöntem yoktur. Çözümünüz birden çok HDInsight kümesi türüne yayılan teknolojiler gerektiriyorsa, bir [Azure sanal ağı](../virtual-network/index.yml) gerekli küme türlerini bağlanabilir.

| Küme türü | İşlev |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Depolanan verilerin toplu sorgu ve Analizi |
| [HBase](hbase/apache-hbase-overview.md) |Büyük miktarlarda çok sayıda şemaya daha, NoSQL verisi işleme |
| [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) |Etkileşimli ve daha hızlı Hive sorguları için bellek içi önbelleğe alma |
| [Kafka](kafka/apache-kafka-introduction.md) | Gerçek zamanlı akış veri işlem hatları ve uygulamaları oluşturmak için kullanılabilen bir dağıtılmış akış platformu |
| [ML Services](r-server/r-server-overview.md) |Çeşitli büyük veri istatistikleri, tahmine dayalı modelleme ve makine öğrenimi özellikleri |
| [Spark](spark/apache-spark-overview.md) |Bellek içi işleme, etkileşimli sorgular, mikro-Batch akış işleme |
| [Storm](storm/apache-storm-overview.md) |Gerçek zamanlı olay işleme |

#### <a name="version"></a>Sürüm

Bu küme için HDInsight sürümünü seçin. Daha fazla bilgi için bkz. [desteklenen HDInsight sürümleri](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-credentials"></a>Küme kimlik bilgileri

HDInsight kümeleri ile, küme oluşturma sırasında iki kullanıcı hesabını yapılandırabilirsiniz:

* Küme oturum açma Kullanıcı adı: Varsayılan Kullanıcı adı *admin*. Azure portal temel yapılandırmayı kullanır. Bazen "Cluster user" veya "HTTP user" olarak adlandırılır.
* Secure Shell (SSH) Kullanıcı adı: kümeye SSH aracılığıyla bağlanmak için kullanılır. Daha fazla bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

HTTP Kullanıcı adı aşağıdaki kısıtlamalara sahiptir:

* İzin verilen özel karakterler: `_` ve `@`
* Karakterlere izin verilmiyor: #;. "', \/ : '! *? $ () {} [] <>|&--= +% ~ ^ boşluk
* En fazla uzunluk: 20

SSH Kullanıcı adı aşağıdaki kısıtlamalara sahiptir:

* İzin verilen özel karakterler: `_` ve `@`
* Karakterlere izin verilmiyor: #;. "', \/ : '! *? $ () {} [] <>|&--= +% ~ ^ boşluk
* En fazla uzunluk: 64
* Ayrılmış adlar: Hadoop, kullanıcılar, Oozie, Hive, mapred, ambarı-qa, Zookeeper, tez, bir, Sqoop, Yarn, hcat, AMS, HBase, fırtınası, yönetici, yönetici, Kullanıcı, Kullanıcı1, test, kullanıcı2, test1, User3, admin1, 1, 123, a, ACTUser, adm, Admin2, ASPNET, Backup, Console, David, Guest, John, Owner, root, Server, SQL, support, support_388945a0, sys, test2, test3, User4, user5, Spark

## <a name="storage"></a>Depolama

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png" alt-text="Küme depolama ayarları:,, uyumlu uç noktalar":::

Hadoop 'un Şirket içi yüklemesi kümedeki depolama için Hadoop Dağıtılmış Dosya Sistemi (bir) kullanıyor olsa da bulutta, kümeye bağlı depolama uç noktaları kullanılır. Bulut depolama kullanmak, verileri korurken hesaplama için kullanılan HDInsight kümelerini güvenle silebilmeniz anlamına gelir.

HDInsight kümeleri aşağıdaki depolama seçeneklerini kullanabilir:

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure Depolama Genel Amaçlı v2
* Azure Depolama Genel Amaçlı v1
* Azure Storage Blok Blobu (**yalnızca ikincil depolama olarak desteklenir**)

HDInsight ile depolama seçenekleri hakkında daha fazla bilgi için bkz. [Azure HDInsight kümeleri ile kullanım için depolama seçeneklerini karşılaştırma](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> HDInsight kümesinden farklı bir konumda ek depolama hesabı kullanılması desteklenmez.

Yapılandırma sırasında, varsayılan depolama uç noktası için bir Azure depolama hesabının veya Data Lake Storage blob kapsayıcısını belirtirsiniz. Varsayılan depolama, uygulama ve sistem günlükleri içerir. İsteğe bağlı olarak, ek bağlı Azure depolama hesapları ve kümenin erişebileceği Data Lake Storage hesapları belirtebilirsiniz. HDInsight kümesi ve bağımlı depolama hesapları aynı Azure konumunda olmalıdır.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> Bir küme oluşturduktan sonra güvenli depolama aktarımını etkinleştirmek, depolama hesabınızı kullanırken hatalara neden olabilir ve önerilmez. Güvenli aktarım özelliği zaten etkin olan bir depolama hesabı kullanılarak yeni bir küme oluşturulması daha iyidir.

> [!Note]  
> Azure HDInsight, Azure depolama 'da depolanan verilerinizi bir bölgeden diğerine otomatik olarak aktarılmaz, taşımaz veya kopyalamaz.

### <a name="metastore-settings"></a>Meta veri deposu ayarları

İsteğe bağlı Hive veya Apache Oozie metastores oluşturabilirsiniz. Ancak, tüm küme türleri metastores 'yi desteklemez ve Azure SYNAPSE Analytics, metastores ile uyumlu değildir.

Daha fazla bilgi için bkz. [Azure HDInsight 'ta dış meta veri depoları kullanma](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Özel bir meta veri deposu oluşturduğunuzda, veritabanı adında tireler, tireler veya boşluk kullanmayın. Bu, küme oluşturma işleminin başarısız olmasına neden olabilir.

#### <a name="sql-database-for-hive"></a>Hive için SQL veritabanı

Bir HDInsight kümesini sildikten sonra Hive tablolarınızı sürdürmek isterseniz, özel bir meta veri deposu kullanın. Daha sonra, meta veri deposunu başka bir HDInsight kümesine ekleyebilirsiniz.

Bir HDInsight küme sürümü için oluşturulan An HDInsight meta veri, farklı HDInsight küme sürümleri arasında paylaşılamaz. HDInsight sürümlerinin listesi için bkz. [desteklenen HDInsight sürümleri](hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Varsayılan meta veri deposu, **temel katman 5 DTU sınırı (yükseltilebilir)** Ile BIR Azure SQL veritabanı sağlar! Temel test amaçları için uygundur. Büyük veya üretim iş yükleri için bir dış meta veri yüklemeye geçiş yapmanızı öneririz.

#### <a name="sql-database-for-oozie"></a>Oozie için SQL veritabanı

Oozie kullanırken performansı artırmak için özel bir meta veri deposu kullanın. Bir meta veri deposu, kümenizi sildikten sonra Oozie iş verilerine erişim de sağlayabilir.

#### <a name="sql-database-for-ambari"></a>Ambarı için SQL veritabanı

Ambarı, HDInsight kümelerini izlemek, yapılandırma değişiklikleri yapmak ve küme yönetimi bilgilerinin yanı sıra iş geçmişi depolamak için kullanılır. Özel ambarı DB özelliği, yönettiğiniz bir dış veritabanında yeni bir küme dağıtmanıza ve ambarı ayarlamanıza olanak sağlar. Daha fazla bilgi için bkz. [özel AMBARı DB](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Özel bir Oozie meta veri deposunu yeniden kullanamazsınız. Özel bir Oozie meta veri deposu kullanmak için, HDInsight kümesini oluştururken boş bir Azure SQL veritabanı sağlamanız gerekir.

## <a name="security--networking"></a>Güvenlik + ağ

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png" alt-text="HDInsight oluşturma seçenekleri kurumsal güvenlik paketini seçin":::

### <a name="enterprise-security-package"></a>Kurumsal güvenlik paketi

Hadoop, Spark, HBase, Kafka ve etkileşimli sorgu kümesi türleri için **Kurumsal güvenlik paketi** etkinleştirmeyi seçebilirsiniz. Bu paket, Apache Ranger kullanarak ve Azure Active Directory tümleştirerek daha güvenli bir küme kurulumuna sahip olmak için seçenek sağlar. Daha fazla bilgi için bkz. [Azure HDInsight 'ta Kurumsal güvenliğe genel bakış](./domain-joined/hdinsight-security-overview.md).

Kurumsal güvenlik paketi, HDInsight 'ı Active Directory ve Apache Ranger ile tümleştirmenize olanak tanır. Kurumsal güvenlik paketi kullanılarak birden çok Kullanıcı oluşturulabilir.

Etki alanına katılmış HDInsight kümesi oluşturma hakkında daha fazla bilgi için bkz. [etki alanına katılmış HDInsight Sandbox ortamı oluşturma](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

### <a name="tls"></a>TLS

Daha fazla bilgi için bkz. [Aktarım Katmanı Güvenliği](./transport-layer-security.md)

### <a name="virtual-network"></a>Sanal ağ

Çözümünüz birden çok HDInsight kümesi türüne yayılan teknolojiler gerektiriyorsa, bir [Azure sanal ağı](../virtual-network/index.yml) gerekli küme türlerini bağlanabilir. Bu yapılandırma, kümelerin ve bunlara dağıttığınız tüm kodların birbirleriyle doğrudan iletişim kurmasına olanak tanır.

HDInsight ile Azure sanal ağını kullanma hakkında daha fazla bilgi için bkz. [HDInsight için bir sanal ağ planlayın](hdinsight-plan-virtual-network-deployment.md).

Bir Azure sanal ağı içinde iki küme türü kullanmanın bir örneği için, bkz. [Apache Kafka Apache Spark yapısal akış kullanma](hdinsight-apache-kafka-spark-structured-streaming.md). Sanal ağ için belirli yapılandırma gereksinimleri dahil olmak üzere bir sanal ağla HDInsight kullanma hakkında daha fazla bilgi için bkz. [HDInsight için bir sanal ağ planlayın](hdinsight-plan-virtual-network-deployment.md).

### <a name="disk-encryption-setting"></a>Disk şifreleme ayarı

Daha fazla bilgi için bkz. [müşteri tarafından yönetilen anahtar disk şifrelemesi](./disk-encryption.md).

### <a name="kafka-rest-proxy"></a>Kafka REST ara sunucusu

Bu ayar yalnızca Kafka küme türü için kullanılabilir. Daha fazla bilgi için bkz. [rest proxy kullanma](./kafka/rest-proxy.md).

### <a name="identity"></a>Kimlik

Daha fazla bilgi için bkz. [Azure HDInsight 'Ta Yönetilen kimlikler](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Yapılandırma + fiyatlandırma

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png" alt-text="HDInsight düğüm boyutunuzu seçin":::

Küme mevcut olduğu sürece düğüm kullanımı için faturalandırılırsınız. Faturalandırma, küme oluşturulduğunda başlar ve küme silindiğinde duraklar. Kümeler serbest olarak ayrılamaz veya beklemeye alınamaz.

### <a name="node-configuration"></a>Düğüm yapılandırması

Her küme türünün kendi düğüm sayısı, düğüm terminolojisi ve varsayılan VM boyutu vardır. Aşağıdaki tabloda, her düğüm türü için düğümlerin sayısı parantez içinde bulunur.

| Tür | Düğümler | Diyagram |
| --- | --- | --- |
| Hadoop |Baş düğüm (2), çalışan düğümü (1 +) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png" alt-text="HDInsight Hadoop küme düğümleri" border="false"::: |
| HBase |Baş sunucu (2), bölge sunucusu (1 +), ana/ZooKeeper düğüm (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png" alt-text="HDInsight HBase küme türü kurulumu" border="false"::: |
| Storm |Nimbus node (2), gözetmen sunucusu (1 +), ZooKeeper node (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png" alt-text="HDInsight fırtınası küme türü kurulumu" border="false"::: |
| Spark |Baş düğüm (2), çalışan düğümü (1 +), ZooKeeper node (3) (a1 ZooKeeper VM boyutu için ücretsiz) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png" alt-text="HDInsight Spark küme türü kurulumu" border="false"::: |

Daha fazla bilgi için bkz. "HDInsight 'ta Hadoop bileşenleri ve sürümleri nelerdir?" içindeki [kümeler Için varsayılan düğüm yapılandırması ve sanal makine boyutları](hdinsight-supported-node-configuration.md) .

HDInsight kümelerinin maliyeti düğüm sayısına ve düğümlerin sanal makine boyutlarına göre belirlenir.

Farklı küme türlerinde farklı düğüm türleri, düğüm sayıları ve düğüm boyutları vardır:
* Hadoop kümesi türü varsayılanı:
    * İki *baş düğüm*
    * Dört *çalışan düğümü*
* Fırtınası kümesi türü varsayılanı:
    * İki *Nimbus düğümü*
    * Üç *ZooKeeper düğümü*
    * Dört *Gözetmen düğümü*

HDInsight 'ı kullanmaya yeni başladıysanız bir çalışan düğümü kullanmanızı öneririz. HDInsight fiyatlandırması hakkında daha fazla bilgi için bkz. [HDInsight fiyatlandırması](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> Küme boyutu sınırı, Azure abonelikleri arasında farklılık gösterir. Limiti artırmak için [Azure Faturalandırma desteği](../azure-portal/supportability/how-to-create-azure-support-request.md) 'ne başvurun.

Kümeyi yapılandırmak için Azure portal kullandığınızda, düğüm boyutu **yapılandırma + fiyatlandırma** sekmesinden kullanılabilir. Portalda, farklı düğüm boyutlarıyla ilişkili maliyeti de görebilirsiniz.

### <a name="virtual-machine-sizes"></a>Sanal makine boyutları

Kümeleri dağıtırken, dağıtmayı planladığınız çözüme göre işlem kaynakları ' nı seçin. HDInsight kümeleri için aşağıdaki VM 'Ler kullanılır:

* A ve D1-4 Serisi VM 'Ler: [genel amaçlı LINUX VM boyutları](../virtual-machines/sizes-general.md)
* D11-14 serisi VM: [bellek için Iyileştirilmiş LINUX VM boyutları](../virtual-machines/sizes-memory.md)

Farklı SDK 'Ları kullanarak bir küme oluştururken veya Azure PowerShell kullanırken VM boyutunu belirtmek için hangi değeri kullanmanız gerektiğini öğrenmek için bkz. [HDInsight kümeleri için kullanılacak VM boyutları](../cloud-services/cloud-services-sizes-specs.md#size-tables). Bu bağlantılı makalede, tabloların **Boyut** sütunundaki değeri kullanın.

> [!IMPORTANT]  
> Bir kümede 32 ' den fazla çalışan düğümüne ihtiyacınız varsa, en az 8 çekirdek ve 14 GB RAM içeren bir baş düğüm boyutu seçmeniz gerekir.

Daha fazla bilgi için bkz. [sanal makineler Için boyutlar](../virtual-machines/sizes.md). Çeşitli boyutlardaki fiyatlar hakkında daha fazla bilgi için bkz. [HDInsight fiyatlandırması](https://azure.microsoft.com/pricing/details/hdinsight).

### <a name="add-application"></a>Uygulama ekleme

HDInsight uygulaması kullanıcıların Linux tabanlı HDInsight kümesine yükleyebileceği bir uygulamadır. Microsoft, üçüncü taraflar tarafından sunulan veya kendi kendinize Geliştirdiğiniz uygulamaları kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure HDInsight 'ta üçüncü taraf Apache Hadoop uygulamaları yüklemeyi](hdinsight-apps-install-applications.md).

HDInsight uygulamalarının çoğu boş bir kenar düğümüne yüklenir.  Boş bir Edge düğümü, aynı istemci araçları yüklü ve baş düğümde olarak yapılandırılmış bir Linux sanal makinedir. Küme erişimi, istemci uygulamalarınızı test etmek ve istemci uygulamalarınızı barındırmak için kenar düğümünü kullanabilirsiniz. Daha fazla bilgi için bkz. [HDInsight 'ta boş kenar düğümlerini kullanma](hdinsight-apps-use-edge-node.md).

### <a name="script-actions"></a>Betik eylemleri

Oluşturma sırasında betikleri kullanarak ek bileşenler yükleyebilir veya küme yapılandırmasını özelleştirebilirsiniz. Bu tür betikler, Azure portal, HDInsight Windows PowerShell cmdlet 'leri veya HDInsight .NET SDK 'dan kullanılabilen bir yapılandırma seçeneği olan **betik eylemi** aracılığıyla çağrılır. Daha fazla bilgi için bkz. [betik eylemini kullanarak HDInsight kümesini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md).

Apache Mahout ve basamaklı gibi bazı yerel Java bileşenleri, küme üzerinde Java Arşivi (JAR) dosyaları olarak çalıştırılabilir. Bu JAR dosyaları Azure depolama 'ya dağıtılabilir ve Hadoop iş gönderim mekanizmalarıyla HDInsight kümelerine gönderilebilir. Daha fazla bilgi için bkz. [Apache Hadoop işleri programlı olarak gönderme](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> JAR dosyalarını HDInsight kümelerine dağıtmaya veya HDInsight kümelerinde JAR dosyalarını çağırmaya sorunlarınız varsa [Microsoft desteği](https://azure.microsoft.com/support/options/)başvurun.
>
> HDInsight, HDInsight tarafından desteklenmez ve Microsoft Desteği için uygun değildir. Desteklenen bileşenlerin listesi için bkz. [HDInsight tarafından sunulan küme sürümlerindeki](hdinsight-component-versioning.md)yenilikler.

Bazen, oluşturma işlemi sırasında aşağıdaki yapılandırma dosyalarını yapılandırmak isteyebilirsiniz:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Daha fazla bilgi için bkz. [önyükleme kullanarak HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure HDInsight ile küme oluşturma hatalarıyla ilgili sorunları giderme](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [HDInsight nedir, Apache Hadoop ekosistemi ve Hadoop kümeleri nedir?](hadoop/apache-hadoop-introduction.md)
* [HDInsight 'ta Apache Hadoop kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Windows BILGISAYARDAN HDInsight üzerinde Apache Hadoop çalışma](hdinsight-hadoop-windows-tools.md)
