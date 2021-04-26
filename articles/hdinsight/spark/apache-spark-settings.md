---
title: Spark ayarlarını yapılandırma-Azure HDInsight
description: Azure HDInsight kümesi için Apache Spark ayarlarını görüntüleme ve yapılandırma
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 25ff8611cc988c9777b52a313cfd74d4ee0a638c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870305"
---
# <a name="configure-apache-spark-settings"></a>Apache Spark ayarlarını yapılandırma

An HDInsight Spark kümesi, Apache Spark kitaplığının bir yüklemesini içerir.  Her HDInsight kümesi, Spark dahil olmak üzere tüm yüklü Hizmetleri için varsayılan yapılandırma parametrelerini içerir.  HDInsight Apache Hadoop kümesini yönetmenin önemli bir yönü, Spark Işleri de dahil olmak üzere iş yükünü izliyor. Spark işlerini en iyi şekilde çalıştırmak için, kümenin mantıksal yapılandırmasını belirlerken fiziksel küme yapılandırmasını göz önünde bulundurun.

Varsayılan HDInsight Apache Spark kümesi şu düğümleri içerir: üç Apache ZooKeeper düğüm, iki baş düğüm ve bir veya daha fazla çalışan düğümü:

:::image type="content" source="./media/apache-spark-settings/spark-hdinsight-arch.png" alt-text="Spark HDInsight mimarisi" border="false":::

HDInsight kümenizdeki düğümlerin sayısı ve sanal makine boyutları Spark yapılandırmanızı etkileyebilir. Varsayılan olmayan HDInsight yapılandırma değerleri genellikle varsayılan olmayan Spark yapılandırma değerlerini gerektirir. Bir HDInsight Spark kümesi oluşturduğunuzda, bileşenlerden her biri için önerilen VM boyutları gösterilir. Şu anda Azure için [bellek için Iyileştirilmiş LINUX VM boyutları](../../virtual-machines/sizes-memory.md) D12 v2 veya daha fazladır.

## <a name="apache-spark-versions"></a>Apache Spark sürümleri

Kümeniz için en iyi Spark sürümünü kullanın.  HDInsight hizmeti hem Spark hem de HDInsight 'ın kendine ait çeşitli sürümlerini içerir.  Spark 'ın her sürümü bir dizi varsayılan küme ayarlarını içerir.  

Yeni bir küme oluşturduğunuzda, aralarından seçim yapabileceğiniz birden çok Spark sürümü vardır. Tam liste,  [HDInsight bileşenleri ve sürümleri](../hdinsight-component-versioning.md)görmek için.

> [!NOTE]  
> HDInsight hizmetindeki Apache Spark varsayılan sürümü hiçbir bildirimde bulunmaksızın değiştirilebilir. Bir sürüm bağımlılığı varsa, Microsoft, .NET SDK, Azure PowerShell ve Azure klasik CLı kullanarak kümeler oluştururken belirli sürümü belirtmenizi önerir.

Apache Spark üç sistem yapılandırma konumuna sahiptir:

* Spark özellikleri çoğu uygulama parametresini denetler ve bir `SparkConf` nesne ya da Java sistem özellikleri aracılığıyla ayarlanabilir.
* Ortam değişkenleri, `conf/spark-env.sh` her düğümdeki komut dosyası aracılığıyla, IP adresi gibi makine başına ayarları ayarlamak için kullanılabilir.
* Günlüğe kaydetme, aracılığıyla yapılandırılabilir `log4j.properties` .

Spark 'ın belirli bir sürümünü seçtiğinizde, kümeniz varsayılan yapılandırma ayarlarını içerir.  Varsayılan Spark yapılandırma değerlerini özel bir Spark yapılandırma dosyası kullanarak değiştirebilirsiniz.  Aşağıda bir örnek gösterilmiştir.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

Yukarıda gösterilen örnek, beş Spark yapılandırma parametresi için birkaç varsayılan değeri geçersiz kılar.  Bu değerler, MapReduce Split minimum boyut ve Parquet blok boyutları Apache Hadoop sıkıştırma codec 'tir. Ayrıca, Spar SQL bölümü ve açık dosya boyutları varsayılan değerleri.  Bu yapılandırma değişiklikleri, ilişkili veriler ve işler (Bu örnekte genomik verileri) belirli özelliklere sahip olduğundan seçilir. Bu özellikler, bu özel yapılandırma ayarları kullanılarak daha iyi olacaktır.

---

## <a name="view-cluster-configuration-settings"></a>Küme yapılandırma ayarlarını görüntüle

Kümede performans iyileştirmesi yapmadan önce geçerli HDInsight kümesi yapılandırma ayarlarını doğrulayın. Spark kümesi bölmesindeki **Pano** bağlantısına tıklayarak hdınsight panosunu Azure Portal başlatın. Küme yöneticisinin Kullanıcı adı ve parolasıyla oturum açın.

Apache ambarı Web Kullanıcı arabirimi, anahtar kümesi kaynak kullanımı ölçümlerinin bir panosu ile görüntülenir.  Ambarı panosu, Apache Spark yapılandırmayı ve diğer yüklü Hizmetleri gösterir. Pano, Spark dahil olmak üzere yüklü hizmetler için bilgileri görüntülediğiniz bir **yapılandırma geçmişi** sekmesi içerir.

Apache Spark yapılandırma değerlerini görmek için yapılandırma **geçmişi**' ni seçin ve ardından **Spark2**' ı seçin.  Yapılandırma sekmesini **seçin ve ardından** `Spark` `Spark2` hizmet listesindeki (sürümünüze bağlı olarak) bağlantısını seçin.  Kümeniz için yapılandırma değerlerinin listesini görürsünüz:

:::image type="content" source="./media/apache-spark-settings/spark-configurations.png" alt-text="Spark yapılandırması" border="true":::

Tek tek Spark yapılandırma değerlerini görmek ve değiştirmek için, başlığında "Spark" ile herhangi bir bağlantı seçin.  Spark yapılandırmaları, bu kategorilerdeki özel ve gelişmiş yapılandırma değerlerini içerir:

* Özel Spark2-varsayılanlar
* Özel Spark2-ölçümler-Özellikler
* Gelişmiş Spark2-varsayılanlar
* Gelişmiş Spark2-env
* Gelişmiş spark2-Hive-site-geçersiz kıl

Varsayılan olmayan bir yapılandırma değerleri kümesi oluşturursanız, güncelleştirme geçmişiniz görünür olur.  Bu yapılandırma geçmişi, varsayılan olmayan yapılandırmanın en iyi performansa sahip olduğunu görmek için yararlı olabilir.

> [!NOTE]  
> Ortak Spark kümesi yapılandırma ayarlarını görmek, ancak değiştirmek için, üst düzey **Spark işi Kullanıcı arabirimi** arabirimindeki **ortam** sekmesini seçin.

## <a name="configuring-spark-executors"></a>Spark yürüticileri yapılandırma

Aşağıdaki diyagramda, anahtar Spark nesneleri: sürücü programı ve onunla ilişkili Spark bağlamı ve Küme Yöneticisi ve *n* çalışan düğümleri gösterilmektedir.  Her çalışan düğümü bir yürütücü, önbellek ve *n* görev örnekleri içerir.

:::image type="content" source="./media/apache-spark-settings/hdi-spark-architecture.png" alt-text="Küme nesneleri" border="false":::

Spark işleri çalışan kaynakları, özellikle bellek kullanır, bu yüzden çalışan düğüm Yürüticileri için Spark yapılandırma değerlerini ayarlamak yaygındır.

Uygulama gereksinimlerini geliştirmek üzere Spark yapılandırmalarının ayarlanması için genellikle ayarlanmış üç temel parametre `spark.executor.instances` , ve ' dir `spark.executor.cores` `spark.executor.memory` . Yürütücü, Spark uygulaması için başlatılan bir işlemdir. Bir yürütücü çalışan düğümünde çalışır ve uygulamanın görevlerinden sorumludur. Çalışan düğümlerinin sayısı ve çalışan düğümü boyutu, yürütmelerin sayısını ve yürütücü boyutlarını belirler. Bu değerler `spark-defaults.conf` , küme baş düğümlerinde ' de depolanır.  Bu değerleri, çalışan bir kümede, ambarı Web Kullanıcı arabiriminde **özel Spark-Defaults** ' i seçerek düzenleyebilirsiniz.  Değişiklik yaptıktan sonra, Kullanıcı arabirimi tarafından etkilenen tüm hizmetleri **yeniden başlatmanız** istenir.

> [!NOTE]  
> Bu üç yapılandırma parametresi küme düzeyinde yapılandırılabilir (küme üzerinde çalışan tüm uygulamalar için) ve ayrıca her bir uygulama için de belirtilir.

Spark Yürüticileri tarafından kullanılan kaynaklarla ilgili başka bir bilgi kaynağı Spark uygulama kullanıcı arabirimi ' dir.  Kullanıcı arabiriminde,  **Yürüticileri** yapılandırma ve tüketilen kaynakların Özet ve ayrıntı görünümlerini görüntüler.  Tüm küme için yürütmeler değerlerinin mi yoksa belirli bir iş yürütmesi kümesi için mi Değişmeyeceğinizi belirleyin.

:::image type="content" source="./media/apache-spark-settings/apache-spark-executors.png" alt-text="Spark Yürüticileri" border="true":::

Ya da, HDInsight ve Spark kümesi yapılandırma ayarlarını programlı bir şekilde doğrulamak için ambarı REST API kullanabilirsiniz.  [GitHub 'Daki Apache AMBARı API başvurusunda](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)daha fazla bilgi bulabilirsiniz.

Spark iş yükünüze bağlı olarak, varsayılan dışındaki bir Spark yapılandırmasının daha iyi Spark iş yürütmeleri sağladığına karar verebilirsiniz.  Varsayılan olmayan küme yapılandırmalarının doğrulanması için örnek iş yükleriyle kıyaslama testi yapın.  Ayarlamayı düşünebileceğiniz yaygın parametrelerden bazıları:

|Parametre |Açıklama|
|---|---|
|--NUM-yürüticileri|Yürüticileri sayısını ayarlar.|
|--Yürütücü-çekirdekler|Her bir yürütücü için çekirdek sayısını ayarlar. Diğer süreçler da kullanılabilir belleğin bir kısmını tükettiği için orta ölçekli yürüticileri kullanmanızı öneririz.|
|--Yürütücü-bellek|Apache Hadoop YARN üzerindeki her bir yürütücü için bellek boyutunu (yığın boyutu) denetler ve yürütme ek yükü için bazı bellek bırakmanız gerekir.|

Farklı yapılandırma değerlerine sahip iki çalışan düğümü örneği aşağıda verilmiştir:

:::image type="content" source="./media/apache-spark-settings/executor-configuration.png" alt-text="İki düğümlü yapılandırma" border="false":::

Aşağıdaki listede Key Spark yürütücü bellek parametreleri gösterilmektedir.

|Parametre |Açıklama|
|---|---|
|spark.executor. Memory|Bir yürütücü için kullanılabilen toplam bellek miktarını tanımlar.|
|spark. Storage. Memorykesri|(varsayılan ~ %60) kalıcı RDD 'leri depolamak için kullanılabilir bellek miktarını tanımlar.|
|spark. karıştırma. Memorykesir|(varsayılan ~ %20) karıştırma için ayrılan bellek miktarını tanımlar.|
|spark. Storage. Unrollkesri ve Spark. Storage. Safetykesir|(Toplam belleğin %30 ' ının toplamı)-Bu değerler Spark tarafından dahili olarak kullanılır ve değiştirilmemelidir.|

YARN, her Spark düğümündeki kapsayıcı tarafından kullanılan en fazla bellek toplamını denetler. Aşağıdaki diyagramda, YARN yapılandırma nesneleri ile Spark nesneleri arasındaki düğüm başına ilişkiler gösterilmektedir.

:::image type="content" source="./media/apache-spark-settings/hdi-yarn-spark-memory.png" alt-text="YARN Spark bellek yönetimi" border="false":::

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter Notebook çalıştıran bir uygulama için parametreleri değiştirme

HDInsight 'ta Spark kümeleri, varsayılan olarak bir dizi bileşen içerir. Bu bileşenlerin her biri, gerektiğinde geçersiz kılınabilen varsayılan yapılandırma değerlerini içerir.

|Bileşen |Açıklama|
|---|---|
|Spark çekirdeği|Spark Core, Spark SQL, Spark streaming API 'Leri, GraphX ve Apache Spark MLlib.|
|Anaconda|Python Paket Yöneticisi.|
|Apache Livy|Apache Spark REST API, uzak işleri bir HDInsight Spark kümesine göndermek için kullanılır.|
|Jupyter Not defterleri ve Apache Zeppelin Not defterleri|Spark kümeniz ile etkileşimde bulunmak için etkileşimli tarayıcı tabanlı kullanıcı arabirimi.|
|ODBC sürücüsü|HDInsight 'ta Spark kümelerini Microsoft Power BI ve Tableau gibi iş zekası (BI) araçlarına bağlar.|

Jupyter Notebook çalıştıran uygulamalar için, `%%configure` komut defteri içinden yapılandırma değişikliği yapmak için komutunu kullanın. Bu yapılandırma değişiklikleri, Not defteri örneğinden çalıştırılan Spark işlerine uygulanır. İlk kod hücresini çalıştırmadan önce, uygulamanın başlangıcında bu değişiklikleri yapın. Değiştirilen yapılandırma, oluşturulması sırasında, Livy oturumuna uygulanır.

> [!NOTE]  
> Uygulamanın sonraki bir aşamada yapılandırmayı değiştirmek için `-f` (zorlama) parametresini kullanın. Ancak, uygulamadaki tüm ilerleme durumu kaybedilir.

Aşağıdaki kod, Jupyter Notebook çalıştıran bir uygulama için yapılandırmanın nasıl değiştirileceğini gösterir.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Sonuç

Spark işlerinizin öngörülebilir ve performanslı bir şekilde çalışmasını sağlamak için çekirdek yapılandırma ayarlarını izleyin. Bu ayarlar, belirli iş yükleriniz için en iyi Spark kümesi yapılandırmasını belirlemesine yardımcı olur.  Ayrıca, uzun süre çalışan ve veya kaynak kullanan Spark iş yürütmelerinin yürütülmesini izlemeniz gerekecektir.  Hatalı boyutlardaki yürüticileri gibi hatalı yapılandırmalardan bellek baskısı etrafında en yaygın sorunlar merkezi. Ayrıca, uzun süre çalışan işlemler ve, Kartezyen işlemler ile sonuçlanır.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight ile kullanılabilen bileşenler ve sürümler Apache Hadoop?](../hdinsight-component-versioning.md)
* [HDInsight üzerinde Apache Spark kümesi için kaynakları yönetme](apache-spark-resource-manager.md)
* [Apache Spark yapılandırması](https://spark.apache.org/docs/latest/configuration.html)
* [Apache Spark Apache Hadoop YARN üzerinde çalışıyor](https://spark.apache.org/docs/latest/running-on-yarn.html)
