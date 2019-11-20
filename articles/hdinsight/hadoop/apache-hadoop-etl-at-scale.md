---
title: Ölçekte ayıklama, dönüştürme ve yükleme (ETL)-Azure HDInsight
description: Apache Hadoop ile HDInsight 'ta ayıklama, dönüştürme ve yükleme işlemlerinin nasıl kullanıldığını öğrenin.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: ashishth
ms.openlocfilehash: d19640d19c3b7fa611f5bfe0e4fd0868924650c5
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066933"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Ölçeklendirerek ayıklama, dönüştürme ve yükleme (ETL)

Ayıklama, dönüştürme ve yükleme (ETL), verilerin çeşitli kaynaklardan alınma, standart bir konumda toplanan, temizlenen ve işlendiği ve sonunda sorgulanabilecek bir veri deposuna yüklendiği işlemdir. Eski ETL, verileri içeri aktarın, yerinde temizler ve sonra ilişkisel bir veri altyapısında depolar. HDInsight ile, çok çeşitli Apache Hadoop ekosistem bileşenleri ETL 'yi ölçekte gerçekleştirmeyi destekler.

ETL işleminde HDInsight 'ın kullanılması bu işlem hattı tarafından özetlenebilir:

![HDInsight ETL ölçeğe genel bakış](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

Aşağıdaki bölümler ETL aşamalarını ve bunlarla ilişkili bileşenleri keşfedebilir.

## <a name="orchestration"></a>Düzenleme

Düzenleme, ETL işlem hattının tüm aşamaları boyunca yayılır. HDInsight 'taki ETL işleri genellikle birbirleriyle birlikte çalışan birkaç farklı ürün içerir.  Verilerin bir bölümünü temizlemek için Hive kullanabilirsiniz, ancak Pig başka bir bölümü temizler.  Azure Data Lake Store Azure SQL veritabanı 'na veri yüklemek için Azure Data Factory kullanabilirsiniz.

Uygun zamanda ilgili işi çalıştırmak için düzenleme gereklidir.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie, Hadoop işlerini yöneten bir iş akışı koordinasyon sistemidir. Oozie bir HDInsight kümesi içinde çalışır ve Hadoop yığınına tümleştirilir. Oozie, Apache Hadoop MapReduce, Apache Pig, Apache Hive ve Apache Sqoop için Hadoop işlerini destekler. Oozie, Java programları veya kabuk betikleri gibi bir sisteme özgü işleri zamanlamak için de kullanılabilir.

Daha fazla bilgi için bkz. [HDInsight 'ta bir iş akışını tanımlamak ve çalıştırmak için Apache Hadoop Ile Apache Oozie 'Yi kullanarak bir](../hdinsight-use-oozie-linux-mac.md) uçtan uca işlem hattını nasıl kullanacağınızı gösteren bir ayrıntılı bilgi edinmek için bkz. [veri](../hdinsight-operationalize-data-pipeline.md)işlem hattını işleme.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory, bir hizmet olarak platform biçiminde düzenleme özellikleri sağlar. Veri taşıma ve veri dönüştürmeyi düzenlemek ve otomatikleştirmek için bulutta veri odaklı iş akışları oluşturmanıza olanak tanıyan, bulut tabanlı bir veri tümleştirme hizmetidir.

Azure Data Factory kullanarak şunları yapabilirsiniz:

1. Farklı veri depolarından veri alan veri odaklı iş akışları (işlem hatları olarak adlandırılır) oluşturun ve zamanlayın.
2. Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch ve Azure Machine Learning gibi işlem hizmetlerini kullanarak verileri işleyin ve dönüştürün.
3. Çıktı verilerini iş zekası (BI) uygulamalarının kullanması için Azure SQL Veri Ambarı gibi veri depolarında yayımlayabilirsiniz.

Azure Data Factory hakkında daha fazla bilgi için [belgelerine](../../data-factory/introduction.md)bakın.

## <a name="ingest-file-storage-and-result-storage"></a>Alma dosya depolama ve sonuç depolama

Kaynak veri dosyaları genellikle Azure Storage veya Azure Data Lake Storage bir konuma yüklenir. Dosyalar herhangi bir biçimde olabilir, ancak genellikle CSV 'ler gibi düz dosyalardır. 

### <a name="azure-storage"></a>Azure Storage 

[Azure depolama](https://azure.microsoft.com/services/storage/blobs/) 'nın [belirli ölçeklenebilirlik hedefleri](../../storage/common/storage-scalability-targets.md)vardır.  Birçok analitik düğüm için Azure depolama, çok daha küçük dosyalarla ilgilenirken en iyi şekilde ölçeklendirilir.  Azure depolama, kaç dosya olduğunu ya da dosyaların ne kadar büyük olduğunu (sınırlarınız dahilinde olduğu sürece) değil, aynı performansı garanti eder.  Diğer bir deyişle, verilerin bir alt kümesini ya da tüm verileri kullanıp kullanmayacağınızı, terabaytlarca veri depolayabilirsiniz ve yine de tutarlı performans sağlayabilirsiniz.

Azure depolama 'nın birçok farklı blob türü vardır.  *Ekleme blobu* , web günlüklerinin veya algılayıcı verilerinin depolanması için harika bir seçenektir.  

Birden çok blob, bunlara erişimi ölçeklendirmek için birçok sunucuya dağıtılabilir, ancak tek bir blob yalnızca tek bir sunucu tarafından sunulabilir. Blob 'lar blob kapsayıcılarında mantıksal olarak gruplandırılabildiği halde, bu gruplamanın hiçbir bölümleme etkileri yoktur.

Azure depolama 'nın Ayrıca blob depolaması için bir Webbir API katmanı vardır.  HDInsight 'taki tüm hizmetler, veri temizleme ve veri işleme için Azure Blob depolamada bulunan dosyalara benzer şekilde, bu hizmetlerin Hadoop Dağıtılmış dosya sistemi 'ni (IDA) nasıl kullanacaklardır.

Veriler genellikle PowerShell, Azure Storage SDK veya AZCopy kullanılarak Azure depolama 'ya alınır.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage (ADLS), bir analiz verileri için, "  ADLS,, bir tasarım paradigması ile benzer ve toplam kapasite ve tek dosyaların boyutu bakımından sınırsız ölçeklenebilirlik sunar. Büyük bir dosya birden çok düğümde depolanabildiği için, ADLS, büyük dosyalarla çalışırken çok iyidir.  ADLS içindeki bölümlendirme verileri arka planda yapılır.  Yüzlerce terabayt boyutunda veriyi verimli bir şekilde okuyan ve yazan eşzamanlı binlerce yürütücü sayesinde, analiz işlerini çok yüksek aktarım hızlarıyla çalıştırabilirsiniz.

Veriler genellikle Azure Data Factory, ADLS SDK 'Ları, AdlCopy hizmeti, Apache DistCp veya Apache Sqoop kullanılarak ADLS 'ye alınır.  Bu hizmetlerden hangisinin büyük ölçüde kullanılacağı, verilerin nerede olduğuna bağlıdır.  Veriler şu anda mevcut bir Hadoop kümenizle birlikte Apache DistCp, AdlCopy hizmeti veya Azure Data Factory kullanabilirsiniz.  Azure Blob depolamadaki Azure Data Lake Storage .NET SDK, Azure PowerShell veya Azure Data Factory kullanabilirsiniz.

ADLS, Azure Olay Hub 'ı veya Apache Storm kullanılarak olay alımı için de iyileştirilmiştir.

#### <a name="considerations-for-both-storage-options"></a>Depolama seçeneklerine ilişkin konular

Veri kümelerini terabayt aralığında karşıya yüklemek için, özellikle de veriler şirket içi bir konumdan geliyorsa, ağ gecikmesi önemli bir sorun olabilir.  Böyle durumlarda, aşağıdaki seçenekleri kullanabilirsiniz:

* Azure ExpressRoute:  Azure ExpressRoute, Azure veri merkezleri ile şirket içi altyapınız arasında özel bağlantılar oluşturmanızı sağlar. Bu bağlantılar, büyük miktarlarda veri aktarmaya yönelik güvenilir bir seçenek sağlar. Daha fazla bilgi için bkz. [Azure ExpressRoute belgeleri](../../expressroute/expressroute-introduction.md).

* Verilerin "çevrimdışı" yüklenmesi. Azure [içeri/dışarı aktarma hizmeti](../../storage/common/storage-import-export-service.md) 'ni kullanarak verilerinize bir Azure veri merkezine sabit disk sürücüleri gönderebilirsiniz. Verileriniz ilk olarak Azure Storage Bloblarına yüklenir. Daha sonra, Azure depolama Bloblarındaki verileri Data Lake Storage kopyalamak için [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) veya [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) aracını kullanabilirsiniz.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Veri Ambarı

Azure SQL DW, gelecekteki analizler için temizlenen ve hazırlanan sonuçların depolanması için harika bir seçimdir.  Azure HDInsight, Azure SQL DW için bu hizmetleri gerçekleştirmek üzere kullanılabilir.

Azure SQL veri ambarı (SQL DW), analitik iş yükleri için iyileştirilmiş bir ilişkisel veritabanı deposudur.  Azure SQL DW, bölümlenmiş tablolara göre ölçeklendirilir.  Tablolar birden çok düğüm arasında bölümlenebilir.  Azure SQL DW düğümleri oluşturma sırasında seçilir.  Olgu sonrasında ölçeklendirebilir, ancak bu, veri taşıma gerektirebilecek etkin bir işlemdir. Daha fazla bilgi için bkz. [SQL veri ambarı-Işlem yönetme](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md) .

### <a name="apache-hbase"></a>Apache HBase

Apache HBase, Azure HDInsight 'ta kullanılabilen bir anahtar-değer deposudur.  Apache HBase, Hadoop’ta oluşturulan ve Google BigTable’a göre modellenen açık kaynaklı bir NoSQL veritabanıdır. HBase, sütun ailelerine göre düzenlenmiş, büyük miktarlarda yapılandırılmamış ve semiste yazılmış veriler için yüksek düzeyde rastgele erişim ve güçlü tutarlılık sağlar.

Veriler bir tablonun satırlarında depolanır ve satır içindeki veriler sütun ailesi tarafından gruplandırılır. HBase, kullanılmadan önce sütunların ya da bunlarda depolanan veri türünün tanımlanmasına gerek duyulmayan, şemasız bir veritabanıdır. Açık kaynak kodu, binlerce düğümdeki petabaytlarca verileri işlemek için doğrusal olarak ölçeklendirir. HBase, Hadoop ekosistemindeki dağıtılmış uygulamalar tarafından sunulan veri yedekliliği, toplu işleme ve diğer özellikleri kullanabilir.   

HBase, gelecekteki analize yönelik algılayıcı ve günlük verileri için mükemmel bir hedefdir.

HBase ölçeklenebilirliği, HDInsight kümesindeki düğüm sayısına bağlıdır.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL veritabanı ve Azure veritabanı

Azure, hizmet olarak platform (PAAS) olarak üç farklı ilişkisel veritabanı sunmaktadır.

* [Azure SQL veritabanı](../../sql-database/sql-database-technical-overview.md) Microsoft SQL Server uygulamasıdır. Performans hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nda ayarlama performansı](../../sql-database/sql-database-performance-guidance.md).
* [MySQL Için Azure veritabanı](../../mysql/overview.md) , Oracle MySQL 'in bir uygulamasıdır.
* [PostgreSQL Için Azure veritabanı](../../postgresql/quickstart-create-server-database-portal.md) , PostgreSQL 'in bir uygulamasıdır.

Bu ürünlerin ölçeği, daha fazla CPU ve bellek ekleyerek ölçeklendiği anlamına gelir.  Daha iyi g/ç performansı için ürünlerle Premium diskler kullanmayı da tercih edebilirsiniz.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Azure Analysis Services (AAS), karar destek ve iş analizlerinin yanı sıra Power BI, Excel, Raporlama Hizmetleri raporları ve diğer veriler gibi iş raporları ve istemci uygulamaları için analitik verileri sağlayan bir analitik veri altyapısıdır görselleştirme araçları.

Analiz küpleri, her bir küpe ait katmanları değiştirerek ölçeklendirebilir.  Daha fazla bilgi için bkz. [Azure Analysis Services fiyatlandırması](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Ayıkla ve yükle

Veriler Azure 'da mevcut olduğunda, bunları ayıklamak ve diğer ürünlere yüklemek için birçok hizmet kullanabilirsiniz.  HDInsight, Sqoop ve flome 'yi destekler. 

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop, yapılandırılmış, yarı yapılandırılmış ve yapılandırılmamış veri kaynakları arasında verileri verimli bir şekilde aktarmak için tasarlanan bir araçtır. 

Sqoop, paralel işlem ve hataya dayanıklılık sağlamak üzere verileri içeri ve dışarı aktarmak için MapReduce kullanır.

### <a name="apache-flume"></a>Apache akıcı

Apache flome, büyük miktarlarda günlük verilerini verimli bir şekilde toplamak, toplamak ve taşımak için dağıtılmış, güvenilir ve kullanılabilir bir hizmettir. Akışkan akışı, akış veri akışlarını temel alan basit ve esnek bir mimariye sahiptir. Akıcı bir güvenilirlik mekanizmalarına ve birçok yük devretme ve kurtarma mekanizmalarına sahip sağlam ve hataya dayanıklı bir akışdır. Akışkan, çevrimiçi analitik uygulamaya izin veren basit bir Genişletilebilir veri modeli kullanır.

Apache akışkan, Azure HDInsight ile kullanılamaz.  Şirket içi Hadoop yüklemesi, verileri Azure depolama Bloblarına veya Azure Data Lake Storage göndermek için akıcı bir şekilde kullanabilir.  Daha fazla bilgi için bkz. [HDInsight Ile Apache Flobana kullanımı](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Dönüştürme

Seçilen konumda veriler varsa, bunu temizlemeniz, birleştirmeniz veya belirli bir kullanım deseninin hazırlanması gerekir.  Hive, Pig ve Spark SQL, bu tür bir çalışma için iyi seçimlerdir.  Bunlar HDInsight 'ta desteklenirler. 

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight üzerinde Apache Hadoop Apache Pig kullanma](hdinsight-use-pig.md)
* [ETL aracı olarak Apache Hive kullanma](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
* [Azure HDInsight kümeleriyle Azure Data Lake Storage 2. Nesil hizmetini kullanma](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
