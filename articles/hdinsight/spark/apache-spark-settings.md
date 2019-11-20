---
title: Spark ayarlarını yapılandırma-Azure HDInsight
description: Azure HDInsight kümesi için Apache Spark ayarlarını görüntüleme ve yapılandırma
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: 48f19e5da8c7703cc597518246c2f62ebce3ae17
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003135"
---
# <a name="configure-apache-spark-settings"></a>Apache Spark ayarlarını yapılandırma

An HDInsight Spark kümesi, [Apache Spark](https://spark.apache.org/) kitaplığının bir yüklemesini içerir.  Her HDInsight kümesi, Spark dahil olmak üzere tüm yüklü Hizmetleri için varsayılan yapılandırma parametrelerini içerir.  HDInsight Apache Hadoop kümesini yönetmenin önemli bir yönü, işlerin öngörülebilir bir şekilde çalıştığından emin olmak için Spark Işleri dahil iş yükünü izliyor. Spark işlerini en iyi şekilde çalıştırmak için, kümenin mantıksal yapılandırmasını en iyi hale getirmeyi belirlerken fiziksel küme yapılandırmasını göz önünde bulundurun.

Varsayılan HDInsight Apache Spark kümesi şu düğümleri içerir: üç [Apache ZooKeeper](https://zookeeper.apache.org/) düğüm, iki baş düğüm ve bir veya daha fazla çalışan düğümü:

![Spark HDInsight mimarisi](./media/apache-spark-settings/spark-hdinsight-arch.png)

HDInsight kümenizdeki düğümlerin sayısı ve sanal makine boyutları Spark yapılandırmanızı de etkileyebilir. Varsayılan olmayan HDInsight yapılandırma değerleri genellikle varsayılan olmayan Spark yapılandırma değerlerini gerektirir. Bir HDInsight Spark kümesi oluşturduğunuzda, bileşenlerden her biri için önerilen VM boyutları gösterilir. Şu anda Azure için [bellek için Iyileştirilmiş LINUX VM boyutları](../../virtual-machines/linux/sizes-memory.md) D12 v2 veya daha fazladır.

## <a name="apache-spark-versions"></a>Apache Spark sürümleri

Kümeniz için en iyi Spark sürümünü kullanın.  HDInsight hizmeti hem Spark hem de HDInsight 'ın kendine ait çeşitli sürümlerini içerir.  Spark 'ın her sürümü bir dizi varsayılan küme ayarlarını içerir.  

Yeni bir küme oluşturduğunuzda, aralarından seçim yapabileceğiniz birden çok Spark sürümü vardır. Tam listeyi görmek için [HDInsight bileşenlerini ve sürümlerini](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]  
> HDInsight hizmetindeki Apache Spark varsayılan sürümü hiçbir bildirimde bulunmaksızın değiştirilebilir. Bir sürüm bağımlılığı varsa, Microsoft, .NET SDK, Azure PowerShell ve Azure klasik CLı kullanarak kümeler oluştururken belirli sürümü belirtmenizi önerir.

Apache Spark üç sistem yapılandırma konumuna sahiptir:

* Spark özellikleri çoğu uygulama parametresini denetler ve bir `SparkConf` nesne ya da Java sistem özellikleri aracılığıyla ayarlanabilir.
* Ortam değişkenleri, her düğümdeki `conf/spark-env.sh` komut dosyası aracılığıyla, IP adresi gibi makine başına ayarları ayarlamak için kullanılabilir.
* Günlüğe kaydetme, aracılığıyla `log4j.properties`yapılandırılabilir.

Spark 'ın belirli bir sürümünü seçtiğinizde, kümeniz varsayılan yapılandırma ayarlarını içerir.  Varsayılan Spark yapılandırma değerlerini özel bir Spark yapılandırma dosyası kullanarak değiştirebilirsiniz.  Aşağıda bir örnek gösterilmiştir.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

Yukarıda gösterilen örnek, beş Spark yapılandırma parametresi için birkaç varsayılan değeri geçersiz kılar.  Bunlar sıkıştırma codec 'tir Apache Hadoop, MapReduce Split minimum boyut ve Parquet blok boyutları ve ayrıca Spar SQL bölümü ve açık dosya boyutları varsayılan değerleri.  Bu yapılandırma değişiklikleri, ilişkili veriler ve işler (Bu örnekte genomik verileri) belirli özelliklere sahip olduğundan ve bu özel yapılandırma ayarları kullanılarak daha iyi gerçekleştirilecek şekilde seçilir.

---

## <a name="view-cluster-configuration-settings"></a>Küme yapılandırma ayarlarını görüntüle

Kümede performans iyileştirmesi gerçekleştirmeden önce geçerli HDInsight küme yapılandırma ayarlarını doğrulayın. Spark kümesi bölmesindeki **Pano** bağlantısına tıklayarak hdınsight panosunu Azure Portal başlatın. Küme yöneticisinin Kullanıcı adı ve parolasıyla oturum açın.

Apache ambarı Web Kullanıcı arabirimi, anahtar kümesi kaynak kullanımı ölçümlerinin bir pano görünümüyle görüntülenir.  Ambarı panosu, Apache Spark yapılandırmayı ve yüklediğiniz diğer hizmetleri gösterir. Panoda, Spark dahil olmak üzere tüm yüklü hizmetlerin yapılandırma bilgilerini görüntüleyebileceğiniz bir **yapılandırma geçmişi** sekmesi bulunur.

Apache Spark yapılandırma değerlerini görmek için yapılandırma **geçmişi**' ni seçin ve ardından **Spark2**' ı seçin.  Yapılandırma sekmesini **seçin ve ardından** hizmet listesindeki `Spark` ( `Spark2`sürümünüze bağlı olarak) bağlantısını seçin.  Kümeniz için yapılandırma değerlerinin listesini görürsünüz:

![Spark yapılandırması](./media/apache-spark-settings/spark-configurations.png)

Tek tek Spark yapılandırma değerlerini görmek ve değiştirmek için bağlantı başlığında "Spark" kelimesiyle herhangi bir bağlantı seçin.  Spark yapılandırmaları, bu kategorilerdeki özel ve gelişmiş yapılandırma değerlerini içerir:

* Özel Spark2-varsayılanlar
* Özel Spark2-ölçümler-Özellikler
* Gelişmiş Spark2-varsayılanlar
* Gelişmiş Spark2-env
* Gelişmiş spark2-Hive-site-geçersiz kıl

Varsayılan olmayan bir yapılandırma değerleri kümesi oluşturursanız, yapılandırma güncelleştirmelerinizin geçmişini de görebilirsiniz.  Bu yapılandırma geçmişi, varsayılan olmayan yapılandırmanın en iyi performansa sahip olduğunu görmek için yararlı olabilir.

> [!NOTE]  
> Ortak Spark kümesi yapılandırma ayarlarını görmek, ancak değiştirmek için, üst düzey **Spark işi Kullanıcı arabirimi** arabirimindeki **ortam** sekmesini seçin.

## <a name="configuring-spark-executors"></a>Spark yürüticileri yapılandırma

Aşağıdaki diyagramda, anahtar Spark nesneleri: sürücü programı ve onunla ilişkili Spark bağlamı ve Küme Yöneticisi ve *n* çalışan düğümleri gösterilmektedir.  Her çalışan düğümü bir yürütücü, önbellek ve *n* görev örnekleri içerir.

![Küme nesneleri](./media/apache-spark-settings/hdi-spark-architecture.png)

Spark işleri çalışan kaynakları, özellikle bellek kullanır, bu yüzden çalışan düğüm Yürüticileri için Spark yapılandırma değerlerini ayarlamak yaygındır.

Uygulama gereksinimlerini geliştirmek üzere Spark yapılandırmalarının ayarlanması için genellikle ayarlanmış üç temel parametre, `spark.executor.instances` `spark.executor.cores`ve `spark.executor.memory`' dir. Yürütücü, Spark uygulaması için başlatılan bir işlemdir. Bir yürütücü çalışan düğümünde çalışır ve uygulamanın görevlerinden sorumludur. Her küme için, varsayılan yürütmelerin sayısı ve yürütücü boyutları, çalışan düğümlerinin sayısı ve çalışan düğüm boyutu temel alınarak hesaplanır. Bunlar, ' de `spark-defaults.conf` küme baş düğümlerinde depolanır.  Bu değerleri, çalışan bir kümede, ambarı Web Kullanıcı arabirimindeki **özel Spark-Defaults** bağlantısını seçerek düzenleyebilirsiniz.  Değişiklik yaptıktan sonra, Kullanıcı arabirimi tarafından etkilenen tüm hizmetleri **yeniden başlatmanız** istenir.

> [!NOTE]  
> Bu üç yapılandırma parametresi küme düzeyinde yapılandırılabilir (küme üzerinde çalışan tüm uygulamalar için) ve ayrıca her bir uygulama için de belirtilir.

Spark Yürütücüleri tarafından kullanılan kaynaklar hakkında bir diğer bilgi kaynağı Spark Uygulaması Kullanıcı Arabirimidir.  Spark Kullanıcı arabiriminde, yürüticileri tarafından tüketilen yapılandırma ve kaynakların Özet ve ayrıntı görünümlerini göstermek için **yürüticileri** sekmesini seçin.  Bu görünümler varsayılan değerleri kümenin tamamında Spark yürütücüleri için mi yoksa belirli bir iş yürütmeleri kümesi için mi değiştireceğinizi saptamanıza yardımcı olabilir.

![Spark Yürüticileri](./media/apache-spark-settings/apache-spark-executors.png)

Alternatif olarak, HDInsight ve Spark kümesi yapılandırma ayarlarını programlı bir şekilde doğrulamak için ambarı REST API kullanabilirsiniz.  [GitHub 'Daki Apache AMBARı API başvurusunda](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)daha fazla bilgi bulabilirsiniz.

Spark iş yükünüze bağlı olarak, varsayılan dışındaki bir Spark yapılandırmasının daha iyi Spark iş yürütmeleri sağladığına karar verebilirsiniz.  Varsayılan dışındaki küme yapılandırmalarının doğrulanması için örnek iş yükleriyle karşılaştırmalı testler yapmalısınız.  Ayarlamayı düşünebileceğiniz yaygın parametrelerden bazıları:

* `--num-executors`yürüticileri sayısını ayarlar.
* `--executor-cores`Her bir yürütücü için çekirdek sayısını ayarlar. Diğer süreçler da kullanılabilir belleğin bir kısmını tükettiği için orta ölçekli yürüticileri kullanmanızı öneririz.
* `--executor-memory`[Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)üzerindeki her bir yürütücü için bellek boyutunu (yığın boyutu) denetler ve yürütme ek yükü için bazı bellek bırakmanız gerekir.

Farklı yapılandırma değerlerine sahip iki çalışan düğümü örneği aşağıda verilmiştir:

![İki düğümlü yapılandırma](./media/apache-spark-settings/executor-configuration.png)

Aşağıdaki listede Key Spark yürütücü bellek parametreleri gösterilmektedir.

* `spark.executor.memory`bir yürütücü için kullanılabilen toplam bellek miktarını tanımlar.
* `spark.storage.memoryFraction`(varsayılan ~% 60) kalıcı RDD 'leri depolamak için kullanılabilir bellek miktarını tanımlar.
* `spark.shuffle.memoryFraction`(varsayılan ~% 20) karıştırma için ayrılan bellek miktarını tanımlar.
* `spark.storage.unrollFraction`ve `spark.storage.safetyFraction` (Toplam belleğin% 30 ' una toplam)-Bu değerler Spark tarafından dahili olarak kullanılır ve değiştirilmemelidir.

YARN, her Spark düğümündeki kapsayıcı tarafından kullanılan en fazla bellek toplamını denetler. Aşağıdaki diyagramda, YARN yapılandırma nesneleri ile Spark nesneleri arasındaki düğüm başına ilişkiler gösterilmektedir.

![YARN Spark bellek yönetimi](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter Not defteri 'nde çalışan bir uygulama için parametreleri değiştirme

HDInsight 'ta Spark kümeleri, varsayılan olarak bir dizi bileşen içerir. Bu bileşenlerin her biri, gerektiğinde geçersiz kılınabilen varsayılan yapılandırma değerlerini içerir.

* Spark Core-Spark Core, Spark SQL, Spark streaming API 'Leri, GraphX ve Apache Spark MLlib.
* Anaconda-Python Paket Yöneticisi.
* [Apache Livy](https://livy.incubator.apache.org/) -uzak Işleri bir HDInsight Spark kümesine göndermek için kullanılan Apache Spark REST API.
* [Jupyter](https://jupyter.org/) ve [Apache Zeppelin](https://zeppelin.apache.org/) Not defterleri-Spark kümeniz ile etkileşimde bulunmak için etkileşimli tarayıcı tabanlı kullanıcı arabirimi.
* ODBC sürücüsü-HDInsight 'ta Spark kümelerini Microsoft Power BI ve Tableau gibi iş zekası (BI) araçlarına bağlar.

Jupyter not defterinde çalışan uygulamalar için, `%%configure` komut defteri içinden yapılandırma değişikliği yapmak için komutunu kullanın. Bu yapılandırma değişiklikleri, Not defteri örneğinden çalıştırılan Spark işlerine uygulanır. İlk kod hücresini çalıştırmadan önce bu değişiklikleri uygulamanın başlangıcında yapmanız gerekir. Değiştirilen yapılandırma, oluşturulması sırasında, Livy oturumuna uygulanır.

> [!NOTE]  
> Uygulamanın sonraki bir aşamada yapılandırmayı değiştirmek için `-f` (zorlama) parametresini kullanın. Ancak, uygulamadaki tüm ilerleme durumu kaybedilir.

Aşağıdaki kod, bir Jupyter not defterinde çalışan bir uygulama için yapılandırmanın nasıl değiştirileceğini gösterir.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Sonuç

Spark işlerinizin öngörülebilir ve performanslı bir şekilde çalıştığından emin olmak için izlemeniz ve ayarlamanız gereken birçok temel yapılandırma ayarı vardır. Bu ayarlar, belirli iş yükleriniz için en iyi Spark kümesi yapılandırmasını belirlemesine yardımcı olur.  Ayrıca, uzun süreli ve/veya kaynak kullanan Spark iş yürütmelerinin yürütülmesini izlemeniz gerekecektir.  Doğru olmayan yapılandırmalara (özellikle hatalı boyutlardaki yürüticileri), uzun süreli işlemlere ve görevlere yönelik olarak, Kartezyen işlemlerde oluşan en yaygın güçlükler merkezi.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight ile kullanılabilen bileşenler ve sürümler Apache Hadoop?](../hdinsight-component-versioning.md)
* [HDInsight üzerinde Apache Spark kümesi için kaynakları yönetme](apache-spark-resource-manager.md)
* [Apache Hadoop, Apache Spark, Apache Kafka ve daha fazlasıyla HDInsight'ta küme oluşturma](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark yapılandırması](https://spark.apache.org/docs/latest/configuration.html)
* [Apache Spark Apache Hadoop YARN üzerinde çalışıyor](https://spark.apache.org/docs/latest/running-on-yarn.html)
