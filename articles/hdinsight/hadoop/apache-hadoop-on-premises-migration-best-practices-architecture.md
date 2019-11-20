---
title: "Mimari: şirket içi Apache Hadoop Azure HDInsight 'a"
description: Şirket içi Hadoop kümelerini Azure HDInsight 'a geçirmeye yönelik mimari en iyi yöntemlerini öğrenin.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: hrasheed
ms.openlocfilehash: 4ef3cded9aba7bd95ecc48e1feadf6c55acd7bdc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499259"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Şirket içi Apache Hadoop kümelerini Azure HDInsight 'a geçirme-mimari en iyi uygulamaları

Bu makale, Azure HDInsight sistemlerinin mimarisine yönelik öneriler sağlar. Şirket içi Apache Hadoop sistemlerini Azure HDInsight 'a geçirmeye yardımcı olmak için en iyi uygulamaları sağlayan bir serinin bir parçasıdır.

## <a name="use-multiple-workload-optimized-clusters"></a>Birden çok iş yükü için iyileştirilmiş küme kullanma

Birçok şirket içi Apache Hadoop dağıtımı, birçok iş yükünü destekleyen tek bir büyük kümeden oluşur. Bu tek küme karmaşık olabilir ve her şeyin birlikte çalışmasını sağlamak için tek tek hizmetlere yönelik olarak gerek duyar. Şirket içi Hadoop kümelerinin Azure HDInsight 'a geçirilmesi, yaklaşımda değişiklik yapılmasını gerektirir.

Azure HDInsight kümeleri belirli bir işlem kullanımı türü için tasarlanmıştır. Depolama alanı birden çok küme genelinde paylaşılabildiğinden, farklı işlerin ihtiyaçlarını karşılamak için birden çok iş yükü için iyileştirilmiş işlem kümesi oluşturmak mümkündür. Her küme türü bu belirli iş yükü için en uygun yapılandırmaya sahiptir. Aşağıdaki tabloda, HDInsight 'ta ve ilgili iş yüklerinde desteklenen küme türleri listelenmektedir.

|**İş yükü**|**HDInsight küme türü**|
|---|---|
|Toplu işleme (ETL/ELT)|Hadoop, Spark|
|Veri ambarlama|Hadoop, Spark, etkileşimli sorgu|
|IoT/akış|Kafka, fırtınası, Spark|
|NoSQL Işlem işleme|HBase|
|Bellek içi önbelleğe alma ile etkileşimli ve daha hızlı sorgular|Interactive Query|
|Veri Bilimi|ML Hizmetleri, Spark|

Aşağıdaki tabloda, HDInsight kümesi oluşturmak için kullanılabilecek farklı yöntemler gösterilmektedir.

|**Araç**|**Tarayıcı tabanlı**|**Komut satırı**|**REST API**|**SDK**|
|---|---|---|---|---|
|[Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLı (ver 1,0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)||||X|
|[Python SDK'sı](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Azure Resource Manager şablonları](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Daha fazla bilgi için bkz. [HDInsight 'Taki küme türleri](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Geçici istek üzerine kümeler kullanın

HDInsight kümeleri uzun süreler için kullanılmıyor olabilir. HDInsight, kaynak maliyetlerinde tasarruf etmenize yardımcı olmak için isteğe bağlı geçici kümeleri destekler. Bu, iş yükü başarıyla tamamlandıktan sonra silinebilirler.

Bir kümeyi sildiğinizde, ilişkili depolama hesabı ve dış meta veriler kaldırılmaz. Küme daha sonra aynı depolama hesapları ve meta depolar kullanılarak yeniden oluşturulabilir.

Azure Data Factory, isteğe bağlı HDInsight kümelerinin oluşturulmasını zamanlamak için kullanılabilir. Daha fazla bilgi için [Azure Data Factory kullanarak HDInsight 'ta isteğe bağlı Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-adf.md)makalesine bakın.

## <a name="decouple-storage-from-compute"></a>Depolamayı, işlem 'dan ayrıştı

Genellikle şirket içi Hadoop dağıtımları, veri depolama ve veri işleme için aynı makine kümesini kullanır. Birlikte bulundurma, işlem ve depolamanın birlikte ölçeklendirilmesi gerekir.

HDInsight kümelerinde depolamanın, işlem ile birlikte bulundurma ve Azure depolama, Azure Data Lake Storage ya da her ikisi de olabilir. Depolamanın işlem sırasında ayrılması aşağıdaki avantajlara sahiptir:

- Kümeler arasında veri paylaşımı.
- Veriler kümeye bağımlı olmadığından geçici kümelerin kullanımı.
- Azaltılan depolama maliyeti.
- Depolama ve işlem işlemlerini ayrı olarak ölçeklendirin.
- Bölgeler arasında veri çoğaltma.

Bilgi işlem ve depolama alanının performans maliyetini azaltmak için bir Azure bölgesindeki depolama hesabı kaynaklarına yakın şekilde işlem kümeleri oluşturulur. Yüksek hızlı ağlarda, işlem düğümlerinin Azure Storage içindeki verilere erişmesini verimli hale gelir.

## <a name="use-external-metadata-stores"></a>Dış meta veri depolarını kullanma


HDInsight kümeleriyle çalışan iki ana meta veri vardır: [Apache Hive](https://hive.apache.org/) ve [Apache Oozie](https://oozie.apache.org/). Hive meta veri deposu, Hadoop, Spark, LLAP, Presto ve Apache Pig gibi veri işleme motorları tarafından kullanılabilen merkezi şema deposudur. Oozie meta veri deposu, zamanlama ve ilerleme durumu ile tamamlanan Hadoop işlerinin durumuyla ilgili ayrıntıları depolar.


HDInsight, Hive için Azure SQL veritabanı ve Oozie meta tastores kullanır. HDInsight kümelerinde bir meta veri deposu kurmanın iki yolu vardır:

1. Varsayılan meta veri deposu

    - Ek maliyet yok.
    - Küme silindiğinde meta veri deposu silinir.
    - Meta veri deposu farklı kümeler arasında paylaştırılamaz.
    - Beş DTU sınırına sahip olan temel Azure SQL DB 'yi kullanır.

1. Özel dış meta veri deposu

    - Meta veri deposu olarak bir dış Azure SQL veritabanı belirtin.
    - Kümeler Hive şeması Oozie iş ayrıntıları dahil, meta veriler kaybedilmeden oluşturulup silinebilir.
    - Tek bir meta veri deposu veritabanı farklı tür kümeleriyle paylaşılabilir.
    - Meta veri deposu gerektiğinde ölçeklendirilebilir.
    - Daha fazla bilgi için bkz. [Azure HDInsight 'ta dış meta veri depoları kullanma](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Hive meta veri deposu için en iyi uygulamalar

Bazı HDInsight Hive meta veri deposu en iyi yöntemler şunlardır:

- İşlem kaynaklarını ve meta verileri ayırmak için özel bir dış meta veri deposu kullanın.
- 50 DTU ve 250 GB depolama sağlayan bir S2 katmanı Azure SQL örneği ile başlayın. Bir performans sorunu görürseniz, veritabanını ölçeklendirdirebilirsiniz.
- Farklı bir sürüm kümeleriyle tek bir HDInsight küme sürümü için oluşturulan meta veri deposunu paylaşmayın. Farklı Hive sürümleri farklı şemalar kullanır. Örneğin, bir meta veri deposu hem Hive 1,2 hem de Hive 2,1 kümeleriyle paylaştırılamaz.
- Özel meta veri deposunu düzenli olarak yedekleyin.
- Meta veri ve HDInsight kümesini aynı bölgede saklayın.
- Azure portal veya Azure Izleyici günlükleri gibi Azure SQL veritabanı Izleme araçlarını kullanarak performans ve kullanılabilirlik için meta veri deposunu izleyin.
- Tablo ve sütun istatistikleri oluşturmak için gereken şekilde **Tabloyu Çözümle** komutunu yürütün. Örneğin, `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Farklı iş yükleri için en iyi uygulamalar

- İyileştirilmiş yanıt süresi [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) Ile etkileşimli Hive sorguları için LLAP kümesi kullanmayı düşünün , sorguların bellek içi önbelleğe alınmasına Izin veren Hive 2,0 ' de yeni bir özelliktir. LLAP, bazı durumlarda Hive sorgularını çok daha hızlı ve [Hive 1. x 'ten daha hızlı](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)hale getirir.
- Hive işlerinin yerine Spark işleri kullanmayı göz önünde bulundurun.
- Impala tabanlı sorguları LLAP sorgularıyla değiştirmeyi göz önünde bulundurun.
- MapReduce işlerini Spark işleri ile değiştirmeyi düşünün.
- Spark yapılandırılmış akış işlerini kullanarak düşük gecikmeli Spark Batch işlerini değiştirmeyi göz önünde bulundurun.
- Veri düzenleme için Azure Data Factory (ADF) 2,0 kullanmayı düşünün.
- Küme yönetimi için ambarı göz önünde bulundurun.
- Şirket içi bir sunucudan, betikleri işlemek için veya ADLS veya ADFS 'e veri depolamayı değiştirin.
- Hive tablolarında ve denetiminde Ranger RBAC kullanmayı düşünün.
- MongoDB veya Cassandra yerine CosmosDB kullanmayı düşünün.

## <a name="next-steps"></a>Sonraki adımlar

Bu serideki bir sonraki makaleyi okuyun:

- [Azure HDInsight Hadoop geçiş için şirket içi altyapı en iyi uygulamaları](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
