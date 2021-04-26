---
title: Dış meta veri depoları kullanma-Azure HDInsight
description: Azure HDInsight kümeleri ile dış meta veri depoları kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 08/06/2020
ms.openlocfilehash: a3bfcfbe59ccc15278b30470c6a060a9c1dd609c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871753"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Azure HDInsight’ta dış meta veri depolarını kullanma

HDInsight, dış veri depolarıyla verilerinizin ve meta verilerinizin denetimini almanıza olanak sağlar. Bu özellik, [Apache Hive meta veri deposu](#custom-metastore), [Apache Oozie meta veri deposu](#apache-oozie-metastore)ve [Apache ambarı veritabanı](#custom-ambari-db)için kullanılabilir.

HDInsight 'ta Apache Hive meta veri, Apache Hadoop mimarisinin önemli bir parçasıdır. Bir meta veri deposu merkezi şema deposudur. Meta veri deposu, Apache Spark, etkileşimli sorgu (LLAP), Presto veya Apache Pig gibi diğer büyük veri erişim araçları tarafından kullanılır. HDInsight, Hive meta veri deposu olarak bir Azure SQL veritabanı kullanır.

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png" alt-text="HDInsight Hive meta veri deposu mimarisi" border="false":::

HDInsight kümeleriniz için bir meta veri deposu kurmak için kullanabileceğiniz iki yol vardır:

* [Varsayılan meta veri deposu](#default-metastore)
* [Özel meta veri deposu](#custom-metastore)

## <a name="default-metastore"></a>Varsayılan meta veri deposu

Varsayılan olarak HDInsight, her küme türüyle bir meta veri deposu oluşturur. Bunun yerine, özel bir meta veri deposu belirtebilirsiniz. Varsayılan meta veri deposu aşağıdaki konuları içerir:

* Ek maliyet yok. HDInsight herhangi bir ek ücret ödemeden her küme türüyle bir meta veri deposu oluşturur.

* Her varsayılan meta veri deposu, küme yaşam döngüsünün bir parçasıdır. Bir kümeyi sildiğinizde, karşılık gelen meta veri deposu ve meta veriler de silinir.

* Varsayılan meta veri deposunu diğer kümelerle paylaşamazsınız.

* Varsayılan meta veri deposu yalnızca basit iş yükleri için önerilir. Birden çok küme gerektirmeyen ve kümenin yaşam döngüsünün ötesinde meta verilerin korunması gerekmeyen iş yükleri.

> [!IMPORTANT]
> Varsayılan meta veri deposu, **temel katman 5 DTU sınırı (yükseltilebilir)** Ile BIR Azure SQL veritabanı sağlar! Temel test amaçları için uygundur. Büyük veya üretim iş yükleri için bir dış meta veri yüklemeye geçiş yapmanızı öneririz.

## <a name="custom-metastore"></a>Özel meta veri deposu

HDInsight, üretim kümeleri için önerilen özel meta tasmres 'i de destekler:

* Kendi Azure SQL veritabanınızı, meta veri deposu olarak belirtirsiniz.

* Meta veri deposu yaşam döngüsü bir küme yaşam döngüsüne bağlı değildir, bu nedenle meta verileri kaybetmeden kümeler oluşturabilir ve silebilirsiniz. HDInsight kümesini silip yeniden oluşturduktan sonra bile Hive şemalarınız gibi meta veriler de devam edecektir.

* Özel bir meta veri deposu, bu meta veri deposu için birden çok küme ve küme türü eklemenize olanak tanır. Örneğin, tek bir meta veri deposu HDInsight 'ta etkileşimli sorgu, Hive ve Spark kümeleri arasında paylaşılabilir.

* Seçtiğiniz performans düzeyine göre bir metasrasyonun (Azure SQL veritabanı) maliyeti için ödeme yaparsınız.

* Gerektiğinde meta veri deposunu ölçeklendirebilirsiniz.

* Küme ve dış meta veri deposu aynı bölgede barındırılmalıdır.

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png" alt-text="HDInsight Hive meta veri deposu kullanım örneği" border="false":::

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Özel meta veri deposu için Azure SQL veritabanı oluşturma ve yapılandırma

HDInsight kümesi için özel bir Hive meta veri deposu ayarlamadan önce mevcut bir Azure SQL veritabanı oluşturun veya var olan bir Azure SQL veritabanı oluşturun.  Daha fazla bilgi için bkz. [hızlı başlangıç: Azure SQL veritabanı 'nda tek bir veritabanı oluşturma](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

Küme oluştururken, HDInsight hizmetinin dış meta veri deposu 'na bağlanması ve kimlik bilgilerinizi doğrulaması gerekir. Azure hizmet ve kaynaklarının sunucuya erişmesine izin vermek için Azure SQL veritabanı güvenlik duvarı kurallarını yapılandırın. **Sunucu güvenlik duvarını ayarla**' yı seçerek Azure Portal bu seçeneği etkinleştirin. Ardından, **genel ağ erişimini reddetme** altında **Hayır** **' ı ve Azure** Hizmetleri ve kaynaklarının Azure SQL veritabanı için **Bu sunucuya erişmesine izin ver** ' i seçin. Daha fazla bilgi için bkz. [IP güvenlik duvarı kuralları oluşturma ve yönetme](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

SQL depoları için özel uç noktalar yalnızca resourceproviderconnection ile oluşturulan kümelerde desteklenir `outbound` . Daha fazla bilgi edinmek için bkz. bu [belgetaa](./hdinsight-private-link.md).

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png" alt-text="Sunucu güvenlik duvarı 'nı ayarla düğmesi":::

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png" alt-text="Azure hizmetlerine erişime izin ver":::

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Küme oluşturma sırasında özel bir meta veri deposu seçin

Kümenizi dilediğiniz zaman daha önce oluşturulmuş bir Azure SQL veritabanına işaret edebilirsiniz. Portal üzerinden küme oluşturma için, bu seçenek **depolama > meta veri deposu ayarlarından** belirtilir.

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png" alt-text="HDInsight Hive meta veri deposu Azure portal":::

## <a name="hive-metastore-guidelines"></a>Hive meta veri deposu yönergeleri

> [!NOTE]
> İşlem kaynaklarını (çalışan kümeniz) ve meta verileri (meta veri deposu 'nda depolanır) ayırmaya yardımcı olmak için mümkün olduğunda özel bir meta veri deposu kullanın. 50 DTU ve 250 GB depolama sağlayan S2 katmanını başlatın. Bir performans sorunu görürseniz, veritabanını ölçeklendirdirebilirsiniz.

* Ayrı verilere erişmek için birden çok HDInsight kümesi düşünüyorsanız, her kümede meta veri deposu için ayrı bir veritabanı kullanın. Birden çok HDInsight kümesi arasında bir meta veri deposu paylaşırsanız, kümelerin aynı meta verileri ve temel alınan kullanıcı veri dosyalarını kullanması anlamına gelir.

* Düzenli aralıklarla özel meta veri deposunu yedekleyin. Azure SQL veritabanı yedeklemeleri otomatik olarak oluşturur, ancak yedekleme bekletme zaman dilimi değişir. Daha fazla bilgi için bkz. [OTOMATIK SQL veritabanı yedeklemeleri hakkında bilgi edinin](../azure-sql/database/automated-backups-overview.md).

* Metasgeli ve HDInsight kümenizi aynı bölgede bulun. Bu yapılandırma en yüksek performans ve en düşük ağ çıkış ücretlerini sağlar.

* Azure SQL veritabanı Izleme Araçları 'nı veya Azure Izleyici günlüklerini kullanarak performans ve kullanılabilirlik için meta deponuz izleyin.

* Mevcut bir özel meta veri deposu veritabanına yönelik yeni, daha yüksek bir Azure HDInsight sürümü oluşturulduğunda, sistem meta veri deposunu yükseltir. Yükseltme, veritabanını yedekten geri yüklemeden geri alınamaz.

* Birden çok küme arasında bir meta veri deposu paylaşırsanız, tüm kümelerin aynı HDInsight sürümü olduğundan emin olun. Farklı Hive sürümleri farklı meta veri deposu veritabanı şemaları kullanır. Örneğin, Hive 2,1 ve Hive 3,1 sürümlü kümeler arasında bir meta veri deposu paylaşamazsınız.

* HDInsight 4,0 ' de Spark ve Hive, Mini SQL veya Hive tablolarına erişmek için bağımsız kataloglar kullanır. Spark kataloğunda Spark tarafından oluşturulan bir tablo. Hive tarafından oluşturulan bir tablo Hive kataloğunda yaşar. Bu davranış, Hive ve Spark paylaşılan ortak kataloğunun HDInsight 3,6 ' inden farklıdır. HDInsight 4,0 ' de Hive ve Spark tümleştirmesi Hive ambar Bağlayıcısı 'nı (HWC) kullanır. HWC, Spark ve Hive arasında bir köprü olarak çalışmaktadır. [Hive ambar Bağlayıcısı hakkında bilgi edinin](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

* 4,0 HDInsight 'ta, Hive ve Spark arasında meta veri deposunu paylaştırmak istiyorsanız, metassıst. Catalog. Default özelliğini Spark kümenizdeki Hive olarak değiştirerek bunu yapabilirsiniz. Bu özelliği, ambarı gelişmiş spark2-Hive-site-override ' da bulabilirsiniz. Yalnızca dış Hive tablolarında çalışan, iç/yönetilen Hive tablolarına veya ACID tablolarına sahipseniz, bu, meta veri paylaşımının paylaşımını anlamak önemlidir.  

## <a name="apache-oozie-metastore"></a>Apache Oozie meta veri deposu

Apache Oozie, Hadoop işlerini yöneten bir iş akışı koordinasyon sistemidir. Oozie, Apache MapReduce, Pig, Hive ve diğerleri için Hadoop işlerini destekler.  Oozie iş akışlarıyla ilgili ayrıntıları depolamak için bir meta veri deposu kullanır. Oozie kullanırken performansı artırmak için Azure SQL veritabanını özel bir meta veri deposu olarak kullanabilirsiniz. Meta veri deposu, kümenizi sildikten sonra Oozie iş verilerine erişim sağlar.

Azure SQL veritabanı ile bir Oozie meta oluşturma hakkında yönergeler için bkz. [iş akışları Için Apache Oozie kullanma](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Özel Ambari DB

HDInsight 'ta Apache ambarı ile kendi dış veritabanınızı kullanmak için bkz. [özel Apache ambarı veritabanı](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Hadoop, Apache Spark, Apache Kafka ve daha fazlasıyla HDInsight'ta küme oluşturma](./hdinsight-hadoop-provision-linux-clusters.md)