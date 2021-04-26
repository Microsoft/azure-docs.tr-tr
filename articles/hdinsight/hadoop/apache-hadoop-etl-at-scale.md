---
title: Ölçekte ayıklama, dönüştürme ve yükleme (ETL)-Azure HDInsight
description: Apache Hadoop ile HDInsight 'ta ayıklama, dönüştürme ve yükleme işlemlerinin nasıl kullanıldığını öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: f11b31ba698c193029834caada2e5c4cf3ef2902
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866650"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Ölçeklendirerek ayıklama, dönüştürme ve yükleme (ETL)

Ayıklama, dönüştürme ve yükleme (ETL), verilerin çeşitli kaynaklardan alındığı işlemdir. Veriler standart bir konumda toplanır, temizlenir ve işlenir. Sonuç olarak, veriler sorgulanabilecek bir veri deposuna yüklenir. Eski ETL, verileri içeri aktarın, yerinde temizler ve sonra ilişkisel bir veri altyapısında depolar. Azure HDInsight ile çok çeşitli Apache Hadoop ortam bileşenleri ETL 'yi ölçeklendirerek destekler.

ETL işleminde HDInsight 'ın kullanılması bu işlem hattı tarafından özetlenir:

:::image type="content" source="./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png" alt-text="HDInsight ETL ölçeğe genel bakış" border="false":::

Aşağıdaki bölümler ETL aşamalarını ve bunlarla ilişkili bileşenleri keşfedebilir.

## <a name="orchestration"></a>Düzenleme

Düzenleme, ETL işlem hattının tüm aşamaları boyunca yayılır. HDInsight 'taki ETL işleri genellikle birbirleriyle birlikte çalışan birkaç farklı ürün içerir. Örnek:

- Verilerin bir bölümünü temizlemek için Apache Hive kullanabilirsiniz ve Apache Pig başka bir bölümü temizleyebilir.
- Azure Data Lake Store Azure SQL veritabanı 'na veri yüklemek için Azure Data Factory kullanabilirsiniz.

Uygun zamanda ilgili işi çalıştırmak için düzenleme gereklidir.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie, Hadoop işlerini yöneten bir iş akışı koordinasyon sistemidir. Oozie bir HDInsight kümesi içinde çalışır ve Hadoop yığınına tümleştirilir. Oozie, Apache Hadoop MapReduce, Pig, Hive ve Sqoop için Hadoop işlerini destekler. Java programları veya kabuk betikleri gibi bir sisteme özgü işleri zamanlamak için Oozie kullanabilirsiniz.

Daha fazla bilgi için bkz. [HDInsight 'ta bir iş akışını tanımlamak ve çalıştırmak için Apache Hadoop Ile Apache Oozie kullanma](../hdinsight-use-oozie-linux-mac.md). Ayrıca bkz. [veri](../hdinsight-operationalize-data-pipeline.md)işlem hattını işleme.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory, bir hizmet olarak platform (PaaS) biçiminde düzenleme özellikleri sağlar. Azure Data Factory, bulut tabanlı bir veri tümleştirme hizmetidir. Veri taşıma ve veri dönüştürmeyi düzenlemek ve otomatikleştirmek için veri odaklı iş akışları oluşturmanıza olanak tanır.

Azure Data Factory için kullanın:

1. Veri odaklı iş akışları oluşturun ve zamanlayın. Bu işlem hatları, farklı veri depolarından veri içerir.
1. HDInsight veya Hadoop gibi işlem hizmetlerini kullanarak verileri işleyin ve dönüştürün. Bu adım için Spark, Azure Data Lake Analytics, Azure Batch veya Azure Machine Learning de kullanabilirsiniz.
1. Bı uygulamalarının kullanması için çıktı verilerini Azure SYNAPSE Analytics gibi veri depolarında yayımlayın.

Azure Data Factory hakkında daha fazla bilgi için [belgelerine](../../data-factory/introduction.md)bakın.

## <a name="ingest-file-storage-and-result-storage"></a>Alma dosya depolama ve sonuç depolama

Kaynak veri dosyaları genellikle Azure Storage veya Azure Data Lake Storage bir konuma yüklenir. Dosyalar genellikle CSV gibi düz bir biçimde olur. Ancak, herhangi bir biçimde olabilir.

### <a name="azure-storage"></a>Azure Depolama

Azure depolama 'nın belirli uyarlanabilir hedefleri vardır. Daha fazla bilgi için bkz. [BLOB depolama Için ölçeklenebilirlik ve performans hedefleri](../../storage/blobs/scalability-targets.md) . Birçok analitik düğüm için Azure depolama, çok daha küçük dosyalarla ilgilenirken en iyi şekilde ölçeklendirilir. Hesap sınırlarınız dahilinde olduğunuz sürece Azure Storage, dosyaların ne kadar büyük olduğuna bakılmaksızın aynı performansı garanti eder. Terabaytlarca veri saklayabilir ve yine de tutarlı performans sağlayabilirsiniz. Bu ifade, bir alt küme veya tüm verileri kullanıp kullanmayacağınızı de doğrudur.

Azure depolama 'nın çeşitli blob türleri vardır. *Ekleme blobu* , web günlüklerinin veya algılayıcı verilerinin depolanması için harika bir seçenektir.

Birden çok blob, bunlara erişimi ölçeklendirmek için birçok sunucu arasında dağıtılabilir. Ancak tek bir blob yalnızca tek bir sunucu tarafından sunulur. Bloblar blob kapsayıcılarında mantıksal olarak gruplandırılabilse de, bu Gruplandırmadan bölümleme etkileri yoktur.

Azure depolama 'nın blob depolaması için bir Webbir API katmanı vardır. Tüm HDInsight Hizmetleri, veri temizleme ve veri işleme için Azure Blob Storage 'daki dosyalara erişebilir. Bu, bu hizmetlerin Hadoop Dağıtılmış Dosya Sistemi (bir) kullanmasına benzer.

Veriler genellikle PowerShell, Azure Storage SDK veya AZCopy aracılığıyla Azure depolama 'ya alınır.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage, analiz verileri için yönetilen, hiper ölçekli bir depodur. İle uyumludur ve, bir tasarım paradigması ile aynı şekilde kullanılır. Data Lake Storage, toplam kapasite ve tek dosyaların boyutu için sınırsız uyumluluk sunar. Bu, büyük dosyalarla çalışırken iyi bir seçimdir çünkü birden çok düğüm arasında depolanabilir. Data Lake Storage bölümleme verileri, arka planda yapılır. Yüzlerce terabaytlarca veriyi verimli bir şekilde okuyan ve yazan binlerce eşzamanlı yürütme sayesinde analitik işleri çalıştırmak için büyük ölçüde üretilen iş kazanın.

Veriler genellikle Azure Data Factory üzerinden Data Lake Storage alınır. Data Lake Storage SDK 'Ları, AdlCopy hizmeti, Apache DistCp veya Apache Sqoop 'yi de kullanabilirsiniz. Seçtiğiniz hizmet verilerin bulunduğu yere bağlıdır. Mevcut bir Hadoop kümenizle birlikte Apache DistCp, AdlCopy hizmeti veya Azure Data Factory kullanabilirsiniz. Azure Blob depolama 'daki veriler için Azure Data Lake Storage .NET SDK, Azure PowerShell veya Azure Data Factory kullanabilirsiniz.

Data Lake Storage, Azure Event Hubs veya Apache Storm üzerinden olay alımı için iyileştirilmiştir.

### <a name="considerations-for-both-storage-options"></a>Depolama seçeneklerine ilişkin konular

Veri kümelerini terabayt aralığında karşıya yüklemek için ağ gecikmesi önemli bir sorun olabilir. Bu, veriler şirket içi bir konumdan geliyorsa özellikle doğrudur. Bu gibi durumlarda, şu seçenekleri kullanabilirsiniz:

- **Azure ExpressRoute:** Azure veri merkezleri ve şirket içi altyapınız arasında özel bağlantılar oluşturun. Bu bağlantılar, büyük miktarlarda veri aktarmaya yönelik güvenilir bir seçenek sağlar. Daha fazla bilgi için bkz. [Azure ExpressRoute belgeleri](../../expressroute/expressroute-introduction.md).

- **Sabit disk sürücülerinden karşıya veri yükleme:** Azure [içeri/dışarı aktarma hizmetini](../../import-export/storage-import-export-service.md) kullanarak verilerinize bir Azure veri merkezine sabit disk sürücüleri gönderebilirsiniz. Verileriniz ilk olarak Azure Blob depolama alanına yüklenir. Daha sonra, Azure Blob depolamadan Data Lake Storage verileri kopyalamak için Azure Data Factory veya AdlCopy aracını kullanabilirsiniz.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Azure SYNAPSE Analytics, hazırlanan sonuçları depolamak için uygun bir seçimdir. Azure SYNAPSE Analytics için bu hizmetleri gerçekleştirmek üzere Azure HDInsight 'ı kullanabilirsiniz.

Azure SYNAPSE Analytics, analitik iş yükleri için iyileştirilmiş bir ilişkisel veritabanı deposudur. Bölümlenmiş tablolara göre ölçeklendirilir. Tablolar birden çok düğüm arasında bölümlenebilir. Düğümler, oluşturma sırasında seçilir. Olgu sonrasında ölçeklendirebilir, ancak bu, veri taşıma gerektirebilecek etkin bir işlemdir. Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics 'te Işlem yönetme](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Apache HBase, Azure HDInsight 'ta kullanılabilen bir anahtar/değer deposudur. Bu, Hadoop üzerinde oluşturulmuş ve Google BigTable 'dan sonra Modellenen bir açık kaynaklı, NoSQL veritabanıdır. HBase, büyük miktarlarda yapılandırılmamış ve yarı yapılandırılmış veriler için performansı rastgele erişim ve güçlü tutarlılık sağlar.

HBase, şemaya daha az bir veritabanı olduğundan, bunları kullanmadan önce sütunları ve veri türlerini tanımlamanız gerekmez. Veriler bir tablonun satırlarında depolanır ve sütun ailesine göre gruplandırılır.

Açık kaynak kodu, binlerce düğümdeki petabaytlarca verileri işlemek için doğrusal olarak ölçeklendirir. HBase, Hadoop ortamında dağıtılmış uygulamalar tarafından sunulan veri yedekliliği, toplu işleme ve diğer özellikleri kullanır.

HBase, gelecekteki analize yönelik algılayıcı ve günlük verileri için iyi bir hedeftir.

HBase uyumluluk, HDInsight kümesindeki düğüm sayısına bağlıdır.

### <a name="azure-sql-databases"></a>Azure SQL veritabanları

Azure üç PaaS ilişkisel veritabanı sunar:

* [Azure SQL veritabanı](../../azure-sql/database/sql-database-paas-overview.md) Microsoft SQL Server uygulamasıdır. Performans hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nda ayarlama performansı](../../azure-sql/database/performance-guidance.md).
* [MySQL Için Azure veritabanı](../../mysql/overview.md)  , Oracle MySQL 'in bir uygulamasıdır.
* [PostgreSQL Için Azure veritabanı](../../postgresql/quickstart-create-server-database-portal.md) , PostgreSQL 'in bir uygulamasıdır.

Bu ürünlerin ölçeğini ölçeklendirmek için daha fazla CPU ve bellek ekleyin.  Daha iyi g/ç performansı için ürünlerle Premium diskler kullanmayı da tercih edebilirsiniz.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services, karar destek ve iş analizte kullanılan bir analitik veri altyapısıdır. İş raporları ve Power BI gibi istemci uygulamalarına yönelik analitik verileri sağlar. Analitik veriler ayrıca Excel, SQL Server Reporting Services raporları ve diğer veri görselleştirme araçlarıyla da kullanılabilir.

Her bir küpe ait katmanları değiştirerek analiz küplerini ölçeklendirin. Daha fazla bilgi için bkz. [Azure Analysis Services fiyatlandırması](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Ayıkla ve yükle

Veriler Azure 'da mevcut olduktan sonra, diğer ürünlere ayıklamak ve yüklemek için birçok hizmet kullanabilirsiniz. HDInsight, Sqoop ve flome 'yi destekler.

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop, yapılandırılmış, yarı yapılandırılmış ve yapılandırılmamış veri kaynakları arasında verileri verimli bir şekilde aktarmak için tasarlanan bir araçtır.

Sqoop, paralel işlem ve hataya dayanıklılık sağlamak üzere verileri içeri ve dışarı aktarmak için MapReduce kullanır.

### <a name="apache-flume"></a>Apache akıcı

Apache flome, büyük miktarlarda günlük verilerini verimli bir şekilde toplamak, toplamak ve taşımak için dağıtılmış, güvenilir ve kullanılabilir bir hizmettir. Esnek mimarisi, akış veri akışlarını temel alır. Akıcı güvenilirlik mekanizmalarıyla sağlam ve hataya dayanıklı bir akışdır. Birçok yük devretme ve kurtarma mekanizması vardır. Akışkan, çevrimiçi, analitik uygulama için izin veren basit bir Genişletilebilir veri modeli kullanır.

Apache akışkan, Azure HDInsight ile kullanılamaz. Ancak şirket içi Hadoop yüklemesi, verileri Azure Blob depolama alanına veya Azure Data Lake Storage göndermek için akıcı bir şekilde kullanabilir. Daha fazla bilgi için bkz. [HDInsight Ile Apache Flobana kullanımı](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Dönüşüm

Seçilen konumda veriler varsa, bunu temizlemeniz, birleştirmeniz veya belirli bir kullanım deseninin hazırlanması gerekir. Hive, Pig ve Spark SQL, bu tür bir çalışma için iyi seçimlerdir. Bunlar HDInsight 'ta desteklenirler.

## <a name="next-steps"></a>Sonraki adımlar

- [ETL aracı olarak Apache Hive kullanma](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
- [Azure HDInsight kümeleriyle Azure Data Lake Storage 2. Nesil hizmetini kullanma](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
- [Verileri Azure SQL veritabanından bir Apache Hive tablosuna taşıma](./apache-hadoop-use-sqoop-mac-linux.md)
