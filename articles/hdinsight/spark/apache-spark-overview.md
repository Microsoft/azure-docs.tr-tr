---
title: Apache Spark nedir-Azure HDInsight
description: Bu makalede, HDInsight 'ta Spark 'a giriş ve HDInsight 'ta Spark kümesini kullanabileceğiniz farklı senaryolar sağlanmaktadır.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: overview
ms.date: 10/01/2019
ms.openlocfilehash: 923e5a961ee9b7bdea94cf7a3e6fc833f596fafd
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937615"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Spark nedir?

Apache Spark, büyük veri analizi uygulamalarının performansını artırmak üzere bellek içi işlemeyi destekleyen paralel bir işleme çerçevesidir. Azure HDInsight 'ta Apache Spark, bulutta Apache Spark Microsoft uygulamasıdır. HDInsight, Azure 'da Spark kümesi oluşturmayı ve yapılandırmayı kolaylaştırır. HDInsight 'ta Spark kümeleri, Azure depolama ve Azure Data Lake Storage uyumludur. Böylece, Azure 'da depolanan verilerinizi işlemek için HDInsight Spark kümelerini kullanabilirsiniz. Bileşenler ve sürüm bilgileri için bkz. [Azure HDInsight 'ta bileşenler ve sürümler Apache Hadoop](../hdinsight-component-versioning.md).

![Spark: Birleşik bir çerçeve](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>Apache Spark nedir?

Spark, bellek içi küme bilgi işlem için temel öğeler sağlar. Bir Spark işi, verileri belleğe yükleyebilir ve önbelleğe alabilir ve tekrar tekrar sorgulayabilir. Bellek içi bilgi işlem, Hadoop gibi disk tabanlı uygulamalardan daha hızlıdır, bu da Hadoop Dağıtılmış dosya sistemi (II) aracılığıyla verileri paylaşır. Spark ayrıca, yerel Koleksiyonlar gibi dağıtılmış veri kümelerini değiştirmenize olanak sağlamak için Scala programlama diliyle tümleştirilir. Her şeyi eşleme olarak yapılandırmak ve işlemleri azaltmak gerekmez.

![Geleneksel MapReduce ile Spark](./media/apache-spark-overview/map-reduce-vs-spark1.png)

HDInsight 'ta Spark kümeleri, tam olarak yönetilen bir Spark hizmeti sunar. HDInsight 'ta Spark kümesi oluşturmanın avantajları burada listelenmiştir.

| Özellik | Açıklama |
| --- | --- |
| Kolaylığı oluşturma |Azure portal, Azure PowerShell veya HDInsight .NET SDK kullanarak dakikalar içinde HDInsight 'ta yeni bir Spark kümesi oluşturabilirsiniz. Bkz. [HDInsight 'ta Apache Spark kümesiyle çalışmaya başlama](apache-spark-jupyter-spark-sql-use-portal.md). |
| Kullanım kolaylığı |HDInsight 'ta Spark kümesi, Jupyter ve Apache Zeppelin not defterlerini içerir. Etkileşimli veri işleme ve görselleştirme için bu not defterlerini kullanabilirsiniz.|
| REST API 'Leri |HDInsight 'ta Spark kümeleri, işleri uzaktan göndermek ve izlemek için REST API tabanlı bir Spark iş sunucusu olan [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)' i içerir. Bkz. [bir HDInsight Spark kümesine uzak işleri göndermek için Apache Spark REST API kullanma](apache-spark-livy-rest-interface.md).|
| Azure Data Lake Storage için destek | HDInsight 'ta Spark kümeleri, birincil depolama alanı veya ek depolama alanı olarak Azure Data Lake Storage kullanabilir. Data Lake Storage hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage genel bakış](../../data-lake-store/data-lake-store-overview.md). |
| Azure hizmetleriyle tümleştirme |HDInsight 'ta Spark kümesi, Azure Event Hubs bağlayıcısıyla birlikte gelir. Spark 'un bir parçası olarak zaten bulunan [Apache Kafka](https://kafka.apache.org/)ek olarak Event Hubs kullanarak akış uygulamaları oluşturabilirsiniz. |
| ML Server için destek | HDInsight 'ta ML Server için destek, **ml Hizmetleri** küme türü olarak sunulmaktadır. Bir ML hizmetleri kümesini, dağıtılmış R hesaplamaları 'nı bir Spark kümesiyle ilişkilendirilen hızlarla birlikte çalıştıracak şekilde ayarlayabilirsiniz. Daha fazla bilgi için bkz. [Azure HDInsight 'TA ml Hizmetleri nedir](../r-server/r-server-overview.md). |
| Üçüncü taraf IDEs ile tümleştirme | HDInsight, bir HDInsight Spark kümesine uygulama oluşturmak ve göndermek için yararlı olan birkaç IDE eklenti sağlar. Daha fazla bilgi için bkz. [Azure TOOLKIT for INTELLIJ fikir kullanımı](apache-spark-intellij-tool-plugin.md), [vscode Için Spark & Hive araçları kullanma](../hdinsight-for-vscode.md)ve [Azure Toolkit for Eclipse kullanma](apache-spark-eclipse-tool-plugin.md).|
| Eşzamanlı sorgular |HDInsight 'ta Spark kümeleri eşzamanlı sorguları destekler. Bu özellik, bir kullanıcıdan veya çeşitli Kullanıcı ve uygulamalardan birden çok sorgunun aynı küme kaynaklarını paylaşmasını sağlar. |
| SSD 'lerde önbelleğe alma |Verileri bellekte veya küme düğümlerine bağlı SSD 'lerde önbelleğe alma seçeneğini belirleyebilirsiniz. Bellekte önbelleğe alma, en iyi sorgu performansını sağlar ancak pahalı olabilir. SSD 'lerde önbelleğe alma, veri kümesinin tamamına bellek içinde sığması için gereken bir boyut kümesi oluşturmaya gerek kalmadan sorgu performansını iyileştirmeye yönelik harika bir seçenek sunar. |
| Bı araçlarıyla tümleştirme |HDInsight 'ta Spark kümeleri, veri analizi için [Power BI](https://www.powerbi.com/) gibi BI araçları için bağlayıcılar sağlar. |
| Önceden yüklenmiş Anaconda kitaplıkları |HDInsight 'ta Spark kümeleri, önceden yüklenmiş Anaconda kitaplıkları ile gelir. [Anaconda](https://docs.continuum.io/anaconda/) makine öğrenimi, veri analizi, görselleştirme vb. için 200 'e yakın bir kitaplık sağlar. |
| Ölçeklenebilirlik | HDInsight, küme düğümlerinin sayısını değiştirmenize izin verir. Ayrıca, tüm veriler Azure depolama 'da veya Data Lake Storage depolandığından, Spark kümeleri veri kaybı olmadan bırakılabilir. |
| SLA |HDInsight 'ta Spark kümeleri 24/7 desteği ve% 99,9 ' nin güncel bir SLA 'Sı ile gelir. |

HDInsight 'ta Apache Spark kümeleri varsayılan olarak kümelerde kullanılabilen aşağıdaki bileşenleri içerir.

* [Spark Core](https://spark.apache.org/docs/latest/). Spark Core, Spark SQL, Spark streaming API 'Leri, GraphX ve MLlib içerir.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Not defteri](https://jupyter.org)
* [Apache Zeppelin Not defteri](http://zeppelin-project.org/)

HDInsight 'ta Spark kümeleri, Microsoft Power BI gibi bı araçlarından HDInsight 'ta Spark kümelerine bağlantı için bir [ODBC sürücüsü](https://go.microsoft.com/fwlink/?LinkId=616229) de sağlar.

## <a name="spark-cluster-architecture"></a>Spark kümesi mimarisi

![HDInsight Spark mimarisi](./media/apache-spark-overview/hdi-spark-architecture.png)

Spark 'ın HDInsight kümelerinde nasıl çalıştığını anlayarak Spark bileşenlerini anlamak kolaydır.

Spark uygulamaları, ana programınızdaki (sürücü programı olarak adlandırılır) mini bağlam nesnesi tarafından koordine edilen bir kümede bağımsız işlem kümesi olarak çalışır.

Mini bağlam, uygulamalar arasında kaynak ayıran çeşitli Küme Yöneticisi türlerine bağlanabilir. Bu küme yöneticileri [Apache Mesos](https://mesos.apache.org/), [Apache Hadoop Yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)veya Spark kümesi Yöneticisi 'ni içerir. HDInsight 'ta Spark, YARN kümesi Yöneticisi 'ni kullanarak çalışır. Bağlandıktan sonra Spark,, hesaplamalar çalıştıran ve uygulamanız için veri depolayan süreçler olan kümedeki çalışanlar düğümlerinde yürütmeleri elde ediyor. Daha sonra, uygulama kodunuzu (Bu, Mini Içerik 'e geçirilen JAR veya Python dosyaları tarafından tanımlanan) yürüticilere gönderir. Son olarak, Mini bağlam, çalıştırılacak görevleri yürütmek üzere gönderir.

Mini bağlam, kullanıcının ana işlevini çalıştırır ve çalışan düğümlerinde çeşitli paralel işlemleri yürütür. Daha sonra, Mini bağlam işlemlerin sonuçlarını toplar. Çalışan düğümleri Hadoop Dağıtılmış dosya sistemine ve öğesinden verileri okur ve yazar. Çalışan düğümleri aynı zamanda, dönüştürülmüş verileri dayanıklı Dağıtılmış veri kümeleri (RDDs) olarak bellek içinde önbelleğe alma.

Mini bağlam, Spark yöneticisine bağlanır ve bir uygulamayı çalışan düğümlerinde bir yürütücü işlemi içinde yürütülen ayrı görevlerin yönlendirilmiş grafiğine (DAG) dönüştürmekten sorumludur. Her uygulama, uygulamanın tamamına ve birden çok iş parçacığında görev çalıştırmaya devam eden kendi yürütücü süreçlerini alır.

## <a name="spark-in-hdinsight-use-cases"></a>HDInsight 'ta Spark kullanım örnekleri

HDInsight 'ta Spark kümeleri aşağıdaki temel senaryoları etkinleştirir:

* Etkileşimli veri analizi ve bı

    HDInsight 'ta Apache Spark, verileri Azure depolama veya Azure Data Lake Storage depolar. İş uzmanları ve temel karar mekanizmaları, bu verilere ilişkin raporları çözümleyebilir ve raporlar oluşturabilir ve çözümlenen verilerden etkileşimli raporlar oluşturmak için Microsoft Power BI kullanabilir. Analistleri küme depolamada yapılandırılmamış/yarı yapılandırılmış verilerden başlayabilir, not defterlerini kullanarak veriler için bir şema tanımlayabilir ve ardından Microsoft Power BI kullanarak veri modelleri oluşturabilir. HDInsight 'ta Spark kümeleri, Tableau gibi çeşitli üçüncü taraf bı araçlarını da destekler. Bu, veri analistleri, iş uzmanları ve temel karar mekanizmalarının daha kolay olmasını sağlar.

    [Öğretici: Power BI kullanarak Spark verilerini görselleştirme](apache-spark-use-bi-tools.md)

* Spark Machine Learning

    Apache Spark, HDInsight 'ta bir Spark kümesinden kullanabileceğiniz Spark üzerinde oluşturulmuş bir Machine Learning kitaplığı olan [Mllib](https://spark.apache.org/mllib/)ile birlikte gelir. HDInsight 'ta Spark kümesi, Machine Learning için farklı türlerde paketlere sahip bir Python dağıtımı olan Anaconda 'yı da içerir. Bunun için, Jupyter ve Zeppelin Not defterleri için yerleşik bir destek ve makine öğrenimi uygulamaları oluşturmak için bir ortamınız vardır.

    [Öğretici: HVAC verilerini kullanarak oluşturma sıcaklıkları tahmin etme](apache-spark-ipython-notebook-machine-learning.md)  
    [Öğretici: yiyecek İnceleme sonuçlarını tahmin etme](apache-spark-machine-learning-mllib-ipython.md)

* Spark akış ve gerçek zamanlı veri analizi

    HDInsight 'ta Spark kümeleri, gerçek zamanlı analiz çözümleri oluşturmak için zengin destek sunar. Spark, Kafka, flome, Twitter, ZeroMQ veya TCP yuvaları gibi birçok kaynaktan veri almak için bağlayıcılar içerdiğinden, HDInsight 'ta Spark, Azure Event Hubs veri alımı için birinci sınıf destek ekler. Event Hubs, Azure 'da en yaygın olarak kullanılan sıraya alma hizmetidir. Event Hubs için hazır olmayan desteğe sahip olma, HDInsight 'ta Spark kümelerinin gerçek zamanlı analiz işlem hattı oluşturmak için ideal bir platform olmasını sağlar.

## <a name="where-do-i-start"></a>Nereden başlayabilirim?

HDInsight 'ta Apache Spark hakkında daha fazla bilgi edinmek için aşağıdaki makaleleri kullanabilirsiniz:

* [Hızlı başlangıç: HDInsight 'ta Apache Spark kümesi oluşturma ve Jupyter kullanarak etkileşimli sorgu çalıştırma](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Öğretici: Jupyter kullanarak Apache Spark işi çalıştırma](./apache-spark-load-data-run-query.md)
* [Öğretici: bı araçlarını kullanarak verileri analiz etme](./apache-spark-use-bi-tools.md)
* [Öğretici: Apache Spark kullanarak makine öğrenimi](./apache-spark-ipython-notebook-machine-learning.md)
* [Öğretici: IntelliJ kullanarak bir Scala Maven uygulaması oluşturma](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>Sonraki Adımlar

Bu genel bakışta, Azure HDInsight 'ta Apache Spark temel bilgi edinirsiniz. Bir HDInsight Spark kümesi oluşturmayı ve bazı Spark SQL sorgularını çalıştırmayı öğrenmek için sonraki makaleye ilerleyin:

* [HDInsight 'ta Apache Spark kümesi oluşturma](./apache-spark-jupyter-spark-sql-use-portal.md)
