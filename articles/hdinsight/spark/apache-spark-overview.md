---
title: Apache Spark nedir-Azure HDInsight
description: Bu makalede, HDInsight’ta Spark ile ilgili bir tanıtım ve HDInsight’ta Spark kümesini kullanabileceğiniz farklı senaryolar sunulmaktadır.
ms.service: hdinsight
ms.custom: contperf-fy21q1
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: deaab53feed4d5402ff0a5dcf9b2bd208e85bb18
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062701"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Azure HDInsight’ta Apache Spark nedir?

Apache Spark, büyük veri analizi uygulamalarının performansını artırmak üzere bellek içi işlemeyi destekleyen paralel bir işleme çerçevesidir. Azure HDInsight’ta Apache Spark, Microsoft'un buluttaki Apache Spark uygulamasıdır. HDInsight, Azure’da bir Spark kümesi oluşturup yapılandırmayı kolaylaştırır. HDInsight 'ta Spark kümeleri, [Azure Blob depolama](../../storage/common/storage-introduction.md), [Azure Data Lake Storage 1.](../../data-lake-store/data-lake-store-overview.md)veya [Azure Data Lake Storage 2.](../../storage/blobs/data-lake-storage-introduction.md)uyumludur. Bu nedenle, HDInsight Spark kümelerini Azure’da depolanmış verilerinizi işlemek için kullanabilirsiniz. Bileşenler ve sürüm bilgileri için bkz. [Azure HDInsight 'ta bileşenler ve sürümler Apache Hadoop](../hdinsight-component-versioning.md).

:::image type="content" source="./media/apache-spark-overview/hdinsight-spark-overview.png" alt-text="Spark: birleşik çerçeve" border="false":::

## <a name="what-is-apache-spark"></a>Apache Spark nedir?

Spark, bellek içi küme hesaplama için temel bileşenleri sunar. Bir Spark işi, verileri belleğe yükleyip önbelleğe alarak tekrar tekrar sorgulayabilir. Bellek içi bilgi işlem, Hadoop gibi disk tabanlı uygulamalardan daha hızlıdır, bu da Hadoop Dağıtılmış dosya sistemi (II) aracılığıyla verileri paylaşır. Spark ayrıca Scala programlama diliyle tümleştirilerek yerel koleksiyonlar gibi dağıtılmış veri kümelerini işlemenizi sağlar. Her şeyi harita olarak yapılandırmaya gerek olmadığı için işlem sayısı azalmış olur.

:::image type="content" source="./media/apache-spark-overview/map-reduce-vs-spark1.png" alt-text="Geleneksel MapReduce ile Spark" border="false":::

HDInsight’ta Spark kümeleri, tam olarak yönetilen bir Spark hizmeti sunar. HDInsight'ta bir Spark kümesi oluşturmanın avantajları burada listelenmiştir.

| Özellik | Açıklama |
| --- | --- |
| Kolay oluşturma |Azure portalı, Azure PowerShell veya HDInsight .NET SDK kullanarak dakikalar içinde HDInsight’ta yeni bir Spark kümesi oluşturabilirsiniz. Bkz. [HDInsight 'ta Apache Spark kümesiyle çalışmaya başlama](apache-spark-jupyter-spark-sql-use-portal.md). |
| Kullanım kolaylığı |HDInsight 'ta Spark kümesi, Jupyter not defterlerini ve Apache Zeppelin not defterlerini içerir. Etkileşimli veri işleme ve görselleştirme için bu not defterlerini kullanabilirsiniz. Bkz. [Apache Spark Ile Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md) ve [verileri yükleme ve sorguları bir Apache Spark kümesinde çalıştırma](apache-spark-load-data-run-query.md).|
| REST API'leri |HDInsight 'ta Spark kümeleri, işleri uzaktan göndermek ve izlemek için REST API tabanlı bir Spark iş sunucusu olan [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)' i içerir. Bkz. [bir HDInsight Spark kümesine uzak işleri göndermek için Apache Spark REST API kullanma](apache-spark-livy-rest-interface.md).|
| Azure depolama desteği | HDInsight 'ta Spark kümeleri, birincil depolama alanı veya ek depolama alanı olarak Azure Data Lake Storage 1./Gen2 kullanabilir. Data Lake Storage 1. hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage 1.](../../data-lake-store/data-lake-store-overview.md). Data Lake Storage 2. hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage 2.](../../storage/blobs/data-lake-storage-introduction.md).|
| Azure hizmetleriyle tümleştirme |HDInsight’ta Spark kümesi, Azure Event Hubs için bir bağlayıcı ile birlikte sunulur. Event Hubs kullanarak akış uygulamaları oluşturabilirsiniz. Spark 'ın bir parçası olarak zaten bulunan Apache Kafka dahil. |
| ML Server desteği | HDInsight'ta ML Server desteği **ML Services** küme türü olarak sağlanır. Bir Spark kümesiyle taahhüt edilen hızlarda dağıtılmış R hesaplamaları çalıştırmak için ML Services kümesi ayarlayabilirsiniz. Daha fazla bilgi için bkz. [Azure HDInsight 'TA ml Hizmetleri nedir](../r-server/r-server-overview.md). |
| Üçüncü taraf IDE’lerle tümleştirme | HDInsight, bir HDInsight Spark kümesinde uygulama oluşturup göndermek için faydalı olacak birkaç IDE eklentisi sağlar. Daha fazla bilgi için bkz. [Azure TOOLKIT for INTELLIJ fikir kullanımı](apache-spark-intellij-tool-plugin.md), [vscode Için Spark & Hive araçları kullanma](../hdinsight-for-vscode.md)ve [Azure Toolkit for Eclipse kullanma](apache-spark-eclipse-tool-plugin.md).|
| Eş zamanlı sorgular |HDInsight’ta Spark kümeleri, eş zamanlı sorguları destekler. Bu özellik, bir kullanıcıdan veya çeşitli kullanıcılar ve uygulamalardan gelen birden çok sorgunun aynı küme kaynaklarında paylaşılmasını sağlar. |
| SSD’de önbelleğe alma |Bellekte veya küme düğümlerine ekli SSD’lerde verileri önbelleğe almayı için seçebilirsiniz. Bellekte önbelleğe almak en iyi sorgu performansını sağlar ancak pahalı olabilir. SSD’lerde önbelleğe alma, veri kümesinin tamamının belleğe sığması için gerekli olan boyutta bir küme oluşturmak zorunda kalmadan sorgu performansını artırmak için harika bir seçenek sağlar. Bkz. [Azure HDıNSIGHT GÇ önbelleğini kullanarak Apache Spark iş yüklerinin performansını geliştirme](apache-spark-improve-performance-iocache.md). |
| BI araçları ile tümleştirme |HDInsight’ta Spark kümeleri, veri analizlerine yönelik olarak BI araçları için Power BI gibi bağlayıcılar sağlar. |
| Önceden yüklenmiş Anaconda kitaplıkları |HDInsight’ta Spark kümeleri önceden yüklenmiş Anaconda kitaplıkları ile gelir. [Anaconda](https://docs.continuum.io/anaconda/) makine öğrenimi, veri analizi, görselleştirme vb. için 200 'e yakın bir kitaplık sağlar. |
| Uyumluluk | HDInsight, otomatik ölçeklendirme özelliğiyle küme düğümü sayısını dinamik olarak değiştirmenize olanak sağlar. Bkz. [Azure HDInsight kümelerini otomatik olarak ölçeklendirme](../hdinsight-autoscale-clusters.md). Ayrıca, tüm veriler Azure Blob depolama, [Azure Data Lake Storage 1.](../../data-lake-store/data-lake-store-overview.md) veya [Azure Data Lake Storage 2.](../../storage/blobs/data-lake-storage-introduction.md)depolandığından, Spark kümeleri veri kaybı olmadan bırakılabilir. |
| SLA |HDInsight’ta Spark kümeleri, 7 gün 24 saat destek ve % 99,9 çalışma süreli SLA ile birlikte sunulur. |

HDInsight 'ta Apache Spark kümeleri varsayılan olarak kümelerde kullanılabilen aşağıdaki bileşenleri içerir.

* [Spark Core](https://spark.apache.org/docs/latest/). Spark Core, Spark SQL, Spark akış API’leri, GraphX ve MLlib’i içerir.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Apache Zeppelin Not defteri](http://zeppelin-project.org/)

HDInsight Spark kümeleri, Microsoft Power BI gibi bı araçlarından bağlantı için bir [ODBC sürücüsü](/sql/connect/odbc/download-odbc-driver-for-sql-server) .

## <a name="spark-cluster-architecture"></a>Spark kümesi mimarisi

:::image type="content" source="./media/apache-spark-overview/hdi-spark-architecture.png" alt-text="HDInsight Spark mimarisi" border="false":::

Spark 'ın HDInsight kümelerinde nasıl çalıştığını anlayarak Spark bileşenlerini anlamak kolaydır.

Spark uygulamaları, bir kümede bağımsız işlem kümesi olarak çalışır. Ana programınızdaki (sürücü programı olarak adlandırılır) parlak bağlam nesnesi tarafından koordine edilir.

Mini bağlam, uygulamalar arasında kaynak veren çeşitli Küme Yöneticisi türlerine bağlanabilir. Bu küme yöneticileri Apache Mesos, Apache Hadoop YARN veya Spark kümesi Yöneticisi 'ni içerir. Spark, HDInsight'ta YARN küme yöneticisini kullanarak çalışır. Bağlantı kurulduktan sonra Spark, kümedeki çalışan düğümleri üzerinde yürütücüler devralır. Bunlar, uygulamalarınız için hesaplamalar yapan ve verileri depolayan işlemlerdir. Ardından, uygulama kodunuzu (SparkContext’e geçirilen JAR veya Python dosyaları ile tanımlanır) yürütücülerinize gönderir. Son olarak SparkContext, yürütücülere çalıştırılacak görevleri gönderir.

SparkContext, kullanıcının ana işlevini çalıştırır ve çalışan düğümlerinde çeşitli paralel işlemleri yürütür. Daha sonra SparkContext, işlemlerin sonuçlarını toplar. Çalışan düğümleri Hadoop Dağıtılmış dosya sistemine ve öğesinden verileri okur ve yazar. Çalışan düğümleri aynı zamanda dönüştürülmüş verileri Dayanıklı Dağıtılmış Veri Kümesi (RDD) olarak bellek içinde önbelleğe alır.

Mini bağlam, Spark yöneticisine bağlanır ve bir uygulamayı tek tek görevlerin yönlendirilmiş grafiğine (DAG) dönüştürmekten sorumludur. Çalışan düğümlerinde bir yürütücü işlemi içinde yürütülen görevler. Her uygulama kendi yürütücü süreçlerini alır. Bu, tüm uygulamanın süresine ve görevleri birden çok iş parçacığında çalıştırmaya devam ediyor.

## <a name="spark-in-hdinsight-use-cases"></a>HDInsight'ta Spark kullanım örnekleri

HDInsight'ta Spark kümeleri, aşağıdaki temel senaryolara olanak tanır:

### <a name="interactive-data-analysis-and-bi"></a>Etkileşimli veri analizi ve BI

HDInsight 'ta Apache Spark, verileri Azure Blob depolama, Azure Data Lake Gen1 veya Azure Data Lake Storage 2. depolar. İş uzmanları ve temel karar mekanizmaları, bu veriler üzerinde rapor çözümleyebilir ve raporlar oluşturabilir. Ve analiz edilen verilerden etkileşimli raporlar oluşturmak için Microsoft Power BI kullanın. Analistler küme depolama alanındaki yapılandırılmamış/yarı yapılandırılmış verilerden başlayabilir, not defterlerini kullanarak veriler için bir şema tanımlayabilir ve ardından Microsoft Power BI kullanarak veri modelleri oluşturabilir. HDInsight 'ta Spark kümeleri, bazı üçüncü taraf bı araçlarını da destekler. Tableau gibi, veri analistleri, iş uzmanları ve temel karar mekanizmalarının daha kolay olmasını sağlar.

* [Öğretici: Power BI kullanarak Spark verilerini görselleştirme](apache-spark-use-bi-tools.md)

### <a name="spark-machine-learning"></a>Spark Machine Learning

Apache Spark [Mllib](https://spark.apache.org/mllib/)ile birlikte gelir. MLlib, HDInsight 'ta bir Spark kümesinden kullanabileceğiniz bir Spark üzerinde oluşturulmuş bir makine öğrenme kitaplığıdır. HDInsight 'ta Spark kümesi, Machine Learning için farklı türlerde paketlere sahip bir Python dağıtımı olan Anaconda 'yı da içerir. Ve Jupyter ve Zeppelin Not defterleri için yerleşik destek sayesinde, makine öğrenimi uygulamaları oluşturmak için bir ortamınız vardır.

* [Öğretici: HVAC verilerini kullanarak oluşturma sıcaklıkları tahmin etme](apache-spark-ipython-notebook-machine-learning.md)  
* [Öğretici: yiyecek İnceleme sonuçlarını tahmin etme](apache-spark-machine-learning-mllib-ipython.md)

### <a name="spark-streaming-and-real-time-data-analysis"></a>Spark akış ve gerçek zamanlı veri çözümleme

HDInsight’ta Spark kümeleri, gerçek zamanlı analiz çözümleri oluşturmak için zengin destek sunar. Spark 'ta Kafka, flome, Twitter, ZeroMQ veya TCP yuvaları gibi birçok kaynaktan veri almak için bağlayıcılar zaten var. HDInsight 'ta Spark, Azure Event Hubs verileri almak için birinci sınıf destek ekler. Event Hubs Azure'da en yaygın şekilde kullanılan sıraya alma hizmetidir. Event Hubs için tam desteğe sahip olmak, HDInsight 'ta Spark kümelerinin gerçek zamanlı analiz işlem hattı oluşturmak için ideal bir platform olmasını sağlar.

* [Apache Spark akışa genel bakış](apache-spark-streaming-overview.md)
* [Apache Spark yapılandırılmış akışa genel bakış](apache-spark-structured-streaming-overview.md)

## <a name="next-steps"></a>Sonraki Adımlar

Bu genel bakışta, Azure HDInsight 'ta Apache Spark temel bir anlamış olursunuz.  HDInsight 'ta Apache Spark hakkında daha fazla bilgi edinmek için aşağıdaki makaleleri kullanabilir ve bir HDInsight Spark kümesi oluşturup, örnek Spark sorgularını daha fazla çalıştırabilirsiniz:

* [Hızlı başlangıç: HDInsight 'ta Apache Spark kümesi oluşturma ve Jupyter kullanarak etkileşimli sorgu çalıştırma](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Öğretici: Jupyter kullanarak Apache Spark işinde verileri yükleme ve sorguları çalıştırma](./apache-spark-load-data-run-query.md)
* [Öğretici: Power BI kullanarak Spark verilerini görselleştirme](apache-spark-use-bi-tools.md)
* [Öğretici: HVAC verilerini kullanarak oluşturma sıcaklıkları tahmin etme](apache-spark-ipython-notebook-machine-learning.md)
* [Spark işlerini performans için iyileştirin](apache-spark-perf.md)
