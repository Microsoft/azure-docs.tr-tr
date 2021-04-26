---
title: Apache Spark nedir?
description: Bu makalede, Azure SYNAPSE Analytics 'teki Apache Spark ve Spark kullanabileceğiniz farklı senaryolar hakkında bir giriş sunulmaktadır.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b31fe5daaa0882dc0927c1340902b20df56eb6b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96450430"
---
# <a name="apache-spark-in-azure-synapse-analytics"></a>Azure Synapse Analytics’te Apache Spark

Apache Spark, büyük veri analizi uygulamalarının performansını artırmak üzere bellek içi işlemeyi destekleyen paralel bir işleme çerçevesidir. Azure SYNAPSE Analytics 'te Apache Spark, Microsoft 'un buluttaki Apache Spark uygulamalarından biridir. Azure SYNAPSE, Azure 'da sunucusuz Apache Spark havuzu oluşturmayı ve yapılandırmayı kolaylaştırır. Azure SYNAPSE ' deki Spark havuzları, Azure depolama ve Azure Data Lake 2. nesil depolama ile uyumludur. Böylece, Azure 'da depolanan verilerinizi işlemek için Spark havuzlarını kullanabilirsiniz.

![Spark: birleşik çerçeve](./media/apache-spark-overview/spark-overview.png)

## <a name="what-is-apache-spark"></a>Apache Spark nedir?

Apache Spark, bellek içi küme bilgi işlem için temel öğeler sağlar. Bir Spark işi, verileri belleğe yükleyip önbelleğe alarak tekrar tekrar sorgulayabilir. Bellek içi işlemler, disk tabanlı uygulamalardan çok daha hızlıdır. Spark ayrıca, yerel Koleksiyonlar gibi dağıtılmış veri kümelerini değiştirmenize olanak sağlamak için birden fazla programlama dili ile tümleşir. Her şeyi harita olarak yapılandırmaya gerek olmadığı için işlem sayısı azalmış olur.

![Geleneksel MapReduce ile Spark](./media/apache-spark-overview/map-reduce-vs-spark.png)

Azure 'daki Spark havuzları, tam olarak yönetilen bir Spark hizmeti sunar. Azure SYNAPSE Analytics 'te Spark havuzu oluşturmanın avantajları burada listelenmiştir.

| Özellik | Açıklama |
| --- | --- |
| Hız ve verimlilik |Spark örnekleri 60 ' den az düğüm için yaklaşık 2 dakika ve 60 ' den fazla düğüm için yaklaşık 5 dakika içinde başlar. Örnek, varsayılan olarak, son işi bir not defteri bağlantısı tarafından etkin tutulmadığı takdirde 5 dakikada bir kapatır. |
| Oluşturma kolaylığı |Azure portal, Azure PowerShell veya SYNAPSE Analytics .NET SDK kullanarak dakikalar içinde Azure SYNAPSE 'te yeni bir Spark havuzu oluşturabilirsiniz. Bkz. [Azure SYNAPSE Analytics 'Te Spark havuzlarıyla çalışmaya başlama](../quickstart-create-apache-spark-pool-studio.md). |
| Kullanım kolaylığı |SYNAPSE Analytics, [Nteryasası](https://nteract.io/)'ten türetilmiş özel bir not defteri içerir. Etkileşimli veri işleme ve görselleştirme için bu not defterlerini kullanabilirsiniz.|
| REST API'leri |Azure SYNAPSE Analytics 'te Spark, işleri uzaktan göndermek ve izlemek için REST API tabanlı bir Spark iş sunucusu olan [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)içerir. |
| Azure Data Lake Storage oluşturma için destek 2| Azure SYNAPSE ' deki Spark havuzları, BLOB depolamayı Azure Data Lake Storage oluşturma 2 ' de kullanabilir. Data Lake Storage hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage genel bakış](../../data-lake-store/data-lake-store-overview.md). |
| Üçüncü taraf IDE’lerle tümleştirme | Azure SYNAPSE, bir Spark havuzunda uygulama oluşturmak ve göndermek için yararlı olan [JetBrains](https://www.jetbrains.com/idea/) IÇIN bir IDE eklentisi sağlar. |
| Önceden yüklenmiş Anaconda kitaplıkları |Azure 'daki Spark havuzları, önceden yüklenmiş Anaconda kitaplıkları ile gelir. [Anaconda](https://docs.continuum.io/anaconda/) machine learning, veri analizi, görselleştirme vb. için 200’e yakın kitaplık sağlar. |
| Ölçeklenebilirlik | Azure SYNAPSE havuzlarındaki Apache Spark, havuzların gerektikçe düğüm ekleyerek veya kaldırarak ölçeklenmesi için otomatik ölçeklendirmeyi etkin olabilir. Ayrıca tüm veriler Azure Depolama veya Data Lake Storage üzerinde depolandığından Spark havuzları veri kaybı yaşanmadan kapatılabilir. |

Azure SYNAPSE 'te Spark havuzları, havuzlarda varsayılan olarak bulunan aşağıdaki bileşenleri içerir.

- [Spark Core](https://spark.apache.org/docs/2.4.5/). Spark Core, Spark SQL, GraphX ve MLlib’i içerir.
- [Anaconda](https://docs.continuum.io/anaconda/)
- [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Nteryasası Not defteri](https://nteract.io/)

## <a name="spark-pool-architecture"></a>Spark havuzu mimarisi

Spark 'ın Azure SYNAPSE Analytics 'te nasıl çalıştığını anlayarak Spark bileşenlerini anlamak kolaydır.

Spark uygulamaları, ana programınızdaki (sürücü programı olarak adlandırılır) mini bağlam nesnesi tarafından koordine edilen bir havuzda bağımsız işlem kümesi olarak çalışır.

Mini bağlam, uygulamalar arasında kaynakları ayıran küme yöneticisine bağlanabilir. Küme Yöneticisi [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Bağlandıktan sonra Spark, havuzlarda, hesaplamalar çalıştıran ve uygulamanıza veri depolayan süreçler olan düğümlerde yürütmeleri elde ediyor. Ardından, uygulama kodunuzu (SparkContext’e geçirilen JAR veya Python dosyaları ile tanımlanır) yürütücülerinize gönderir. Son olarak SparkContext, yürütücülere çalıştırılacak görevleri gönderir.

Mini bağlam, kullanıcının ana işlevini çalıştırır ve düğümlerde çeşitli paralel işlemleri yürütür. Daha sonra SparkContext, işlemlerin sonuçlarını toplar. Düğümler dosya sistemine ve arasında verileri okur ve yazar. Düğümler Ayrıca, dönüştürülmüş verileri dayanıklı Dağıtılmış veri kümeleri (RDD) olarak bellek içinde önbelleğe al.

Mini bağlam, Spark havuzuna bağlanır ve bir uygulamayı yönlendirilmiş bir Çevrimsiz grafiğine (DAG) dönüştürmekten sorumludur. Grafik, düğümlerde bir yürütücü işlemi içinde yürütülen bireysel görevlerden oluşur. Her uygulama, uygulama süresince devam eden ve görevleri birden fazla iş parçacığında çalıştıran kendi yürütücü işlemine sahiptir.

## <a name="apache-spark-in-azure-synapse-analytics-use-cases"></a>Azure SYNAPSE Analytics kullanım durumlarında Apache Spark

Azure SYNAPSE Analytics 'te Spark havuzları aşağıdaki önemli senaryoları etkinleştirir:

### <a name="data-engineeringdata-preparation"></a>Veri Mühendisliği/veri hazırlığı

Apache Spark, büyük hacimlerin hazırlanmasını ve işlenmesini desteklemek için çok sayıda dil özelliği içerir, böylece daha değerli hale getirilebilir ve daha sonra Azure SYNAPSE Analytics içindeki diğer hizmetler tarafından tüketilebilir. Bu, birden çok dil (C#, Scala, PySpark, Spark SQL) ve işlem ve bağlantı için sağlanan kitaplıklar aracılığıyla etkinleştirilir.

### <a name="machine-learning"></a>Machine Learning

Apache Spark, Azure SYNAPSE Analytics 'teki bir Spark havuzundan kullanabileceğiniz Spark üzerinde oluşturulmuş bir makine öğrenimi kitaplığı olan [Mllib](https://spark.apache.org/mllib/)ile birlikte gelir. Azure SYNAPSE Analytics 'teki Spark havuzları, Machine Learning dahil veri bilimi için çeşitli paketlere sahip bir Python dağıtımı olan Anaconda 'yı da içerir. Yerleşik not defteri desteğiyle birlikte makine öğrenmesi uygulaması oluşturmak için gerekli ortamı sunar.

## <a name="where-do-i-start"></a>Nereden başlayabilirim?

Azure SYNAPSE Analytics 'te Apache Spark hakkında daha fazla bilgi edinmek için aşağıdaki makaleleri kullanın:

- [Hızlı başlangıç: Azure 'da Spark havuzu oluşturma SYNAPSE](../quickstart-create-apache-spark-pool-portal.md)
- [Hızlı başlangıç: Apache Spark Not defteri oluşturma](../quickstart-apache-spark-notebook.md)
- [Öğretici: Apache Spark kullanarak makine öğrenimi](./apache-spark-machine-learning-mllib-notebook.md)
- [Resmi belgeleri Apache Spark](https://spark.apache.org/docs/2.4.5/)

> [!NOTE]
> Bazı resmi Apache Spark belgeleri Spark konsolunu kullanmaya dayanır, bu Azure SYNAPSE Spark üzerinde kullanılamaz, bunun yerine not defteri veya IntelliJ deneyimlerini kullanın

## <a name="next-steps"></a>Sonraki adımlar

Bu genel bakışta, Azure SYNAPSE Analytics 'teki Apache Spark temel bir şekilde öğrenirsiniz. Azure SYNAPSE Analytics 'te Spark havuzu oluşturmayı öğrenmek için bir sonraki makaleye ilerleyin:

- [Azure 'da Spark havuzu oluşturma SYNAPSE](../quickstart-create-apache-spark-pool-portal.md)
