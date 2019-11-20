---
title: Dış meta veri depoları kullanma-Azure HDInsight
description: Azure HDInsight kümeleri ve en iyi uygulamalar ile dış meta veri depoları kullanın.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 18e03b6828922b3444d9461bef394b5c6682c238
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930344"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Azure HDInsight 'ta dış meta veri depoları kullanma

HDInsight, anahtar meta veri çözümlerini ve yönetim veritabanlarını dış veri depolarına dağıtarak veri ve meta verileri denetlemenize olanak tanır. Bu özellik şu anda [Apache Hive meta veri deposu](#custom-metastore), [Apache Oozie meta veri deposu](#apache-oozie-metastore) ve [Apache ambarı veritabanı](#custom-ambari-db)için kullanılabilir.

HDInsight 'ta Apache Hive meta veri, Apache Hadoop mimarisinin önemli bir parçasıdır. Meta veri deposu, Apache Spark, etkileşimli sorgu (LLAP), Presto veya Apache Pig gibi diğer büyük veri erişim araçları tarafından kullanılabilen merkezi şema deposudur. HDInsight, Hive meta veri deposu olarak bir Azure SQL veritabanı kullanır.

![HDInsight Hive meta veri deposu mimarisi](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

HDInsight kümeleriniz için bir meta veri deposu kurmak için kullanabileceğiniz iki yol vardır:

* [Varsayılan meta veri deposu](#default-metastore)
* [Özel meta veri deposu](#custom-metastore)

## <a name="default-metastore"></a>Varsayılan meta veri deposu

Varsayılan olarak HDInsight, her küme türüyle bir meta veri deposu oluşturur. Bunun yerine, özel bir meta veri deposu belirtebilirsiniz. Varsayılan meta veri deposu aşağıdaki konuları içerir:

* Ek maliyet yok. HDInsight herhangi bir ek ücret ödemeden her küme türüyle bir meta veri deposu oluşturur.

* Her varsayılan meta veri deposu, küme yaşam döngüsünün bir parçasıdır. Bir kümeyi sildiğinizde, karşılık gelen meta veri deposu ve meta veriler de silinir.

* Varsayılan meta veri deposunu diğer kümelerle paylaşamazsınız.

* Varsayılan meta veri deposu, beş DTU (veritabanı işlem birimi) sınırına sahip olan temel Azure SQL DB 'yi kullanır.
Bu varsayılan meta veri deposu genellikle birden çok küme gerektirmeyen ve kümenin yaşam döngüsünün ötesinde meta verilerin korunması gerekmeyen görece basit iş yükleri için kullanılır.

## <a name="custom-metastore"></a>Özel meta veri deposu

HDInsight, üretim kümeleri için önerilen özel meta tasmres 'i de destekler:

* Kendi Azure SQL veritabanınızı, meta veri deposu olarak belirtirsiniz.

* Meta veri deposu yaşam döngüsü bir küme yaşam döngüsüne bağlı değildir, bu nedenle meta verileri kaybetmeden kümeler oluşturabilir ve silebilirsiniz. HDInsight kümesini silip yeniden oluşturduktan sonra bile Hive şemalarınız gibi meta veriler de devam edecektir.

* Özel bir meta veri deposu, bu meta veri deposu için birden çok küme ve küme türü eklemenize olanak tanır. Örneğin, tek bir meta veri deposu HDInsight 'ta etkileşimli sorgu, Hive ve Spark kümeleri arasında paylaşılabilir.

* Seçtiğiniz performans düzeyine göre bir metasrasyonun (Azure SQL DB) maliyeti için ödeme yaparsınız.

* Gerektiğinde meta veri deposunu ölçeklendirebilirsiniz.

![HDInsight Hive meta veri deposu kullanım örneği](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Özel meta veri deposu için Azure SQL veritabanı oluşturma ve yapılandırma

HDInsight kümesi için özel bir Hive meta veri deposu ayarlamadan önce mevcut bir Azure SQL veritabanı oluşturmanız veya sahip olmanız gerekir.  Daha fazla bilgi için bkz. [hızlı başlangıç: Azure SQL DB 'de tek bir veritabanı oluşturma](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

HDInsight kümenizin bağlı Azure SQL veritabanına erişebildiğinizden emin olmak için Azure SQL veritabanı güvenlik duvarı kurallarını Azure hizmetleri ve kaynaklarının sunucuya erişmesine izin verecek şekilde yapılandırın.

**Sunucu güvenlik duvarını ayarla**' ya tıklayarak Azure Portal bu seçeneği etkinleştirebilir ve Azure SQL veritabanı sunucusu veya veritabanı için **Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine izin ver ' e** tıklayabilirsiniz. Daha fazla bilgi için bkz. [IP güvenlik duvarı kuralları oluşturma ve yönetme](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![Sunucu güvenlik duvarı 'nı ayarla düğmesi](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![Azure hizmetlerine erişime izin ver](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Küme oluşturma sırasında özel bir meta veri deposu seçin

Küme oluşturma sırasında kümenizi önceden oluşturulmuş bir Azure SQL veritabanına işaret edebilir veya küme oluşturulduktan sonra SQL veritabanını yapılandırabilirsiniz. Bu seçenek, Azure portal yeni bir Hadoop, Spark veya etkileşimli Hive kümesi oluştururken **depolama > meta veri ayarları** ile belirtilir.

![HDInsight Hive meta veri deposu Azure portal](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

Ayrıca, Azure portal veya ambarı yapılandırmalarından özel bir meta veri deposu için ek kümeler ekleyebilirsiniz (Hive > Gelişmiş)

![HDInsight Hive meta veri deposu ambarı](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>En iyi Hive meta veri deposu uygulamalar

Aşağıda, bazı genel HDInsight Hive meta veri deposu en iyi yöntemler verilmiştir:

* İşlem kaynaklarını (çalışan kümeniz) ve meta verileri (meta veri deposu 'nda depolanır) ayırmaya yardımcı olmak için mümkün olduğunda özel bir meta veri deposu kullanın.

* 50 DTU ve 250 GB depolama sağlayan bir S2 katmanı ile başlayın. Bir performans sorunu görürseniz, veritabanını ölçeklendirdirebilirsiniz.

* Ayrı verilere erişmek için birden çok HDInsight kümesi düşünüyorsanız, her kümede meta veri deposu için ayrı bir veritabanı kullanın. Birden çok HDInsight kümesi arasında bir meta veri deposu paylaşırsanız, kümelerin aynı meta verileri ve temel alınan kullanıcı veri dosyalarını kullanması anlamına gelir.

* Düzenli aralıklarla özel meta veri deposunu yedekleyin. Azure SQL veritabanı yedeklemeleri otomatik olarak oluşturur, ancak yedekleme bekletme zaman dilimi değişir. Daha fazla bilgi için bkz. [OTOMATIK SQL veritabanı yedeklemeleri hakkında bilgi edinin](../sql-database/sql-database-automated-backups.md).

* En yüksek performans ve en düşük ağ çıkış ücretleri için, metasgeli ve HDInsight kümenizi aynı bölgede bulun.

* Azure portal veya Azure Izleyici günlükleri gibi Azure SQL veritabanı Izleme araçlarını kullanarak performans ve kullanılabilirlik için meta deponuz izleyin.

* Mevcut bir özel meta veri deposu veritabanına yönelik yeni, daha yüksek bir Azure HDInsight sürümü oluşturulduğunda, sistem, veritabanını yedekten geri yüklemeden geri alınamaz olan meta veri deposu şemasını yükseltir.

* Birden çok küme arasında bir meta veri deposu paylaşırsanız, tüm kümelerin aynı HDInsight sürümü olduğundan emin olun. Farklı Hive sürümleri farklı meta veri deposu veritabanı şemaları kullanır. Örneğin, Hive 2,1 ve Hive 3,1 sürümlü kümeler arasında bir meta veri deposu paylaşamazsınız.

* HDInsight 4,0 ' de Spark ve Hive, Mini SQL veya Hive tablolarına erişmek için bağımsız kataloglar kullanır. Spark tarafından oluşturulan bir tablo Spark kataloğunda bulunur. Hive tarafından oluşturulan bir tablo Hive kataloğunda bulunur. Bu, Hive ve Spark paylaşılan ortak kataloğunun HDInsight 3,6 sürümünden farklıdır. HDInsight 4,0 ' de Hive ve Spark tümleştirmesi Hive ambar Bağlayıcısı 'nı (HWC) kullanır. HWC, Spark ve Hive arasında bir köprü olarak çalışmaktadır. [Hive ambar Bağlayıcısı hakkında bilgi edinin](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Apache Oozie meta veri deposu

Apache Oozie, Hadoop işlerini yöneten bir iş akışı koordinasyon sistemidir.  Oozie, Apache MapReduce, Pig, Hive ve diğerleri için Hadoop işlerini destekler.  Oozie, geçerli ve tamamlanan iş akışlarıyla ilgili ayrıntıları depolamak için bir meta veri deposu kullanır. Oozie kullanırken performansı artırmak için Azure SQL veritabanını özel bir meta veri deposu olarak kullanabilirsiniz. Meta veri deposu, kümenizi sildikten sonra Oozie iş verilerine erişim de verebilir.

Azure SQL veritabanı ile bir Oozie meta oluşturma hakkında yönergeler için bkz. [iş akışları Için Apache Oozie kullanma](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Özel Ambari DB

HDInsight 'ta Apache ambarı ile kendi dış veritabanınızı kullanmak için bkz. [özel Apache ambarı veritabanı](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Hadoop, Apache Spark, Apache Kafka ve daha fazlasıyla HDInsight'ta küme oluşturma](./hdinsight-hadoop-provision-linux-clusters.md)
