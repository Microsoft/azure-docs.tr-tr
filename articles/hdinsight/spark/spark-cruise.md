---
title: Apache Spark sorguları hızlandırmak için Azure HDInsight 'ta parlak CRUISE kullanın
description: Apache Spark sorgularının verimliliğini artırmak için, parlak CRUISE iyileştirme platformunu nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 9b68af13aef694e1ae457fd1d0f07b8e9bffb61b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068175"
---
# <a name="sparkcruise-on-azure-hdinsight"></a>Azure HDInsight 'ta parlak CRUISE

Bu belgede, sorgu verimliliğini artırmak için Apache Spark hesaplamaları otomatik olarak yeniden kullanan, Azure HDInsight özelliği *parlak CRUISE* anlatılmaktadır.

## <a name="overview"></a>Genel Bakış

Apache Spark gibi bir analiz platformunda çalıştırdığınız sorgular, daha küçük alt sorgular içeren bir sorgu planına parçalanır. Bu alt sorgular, birden çok sorgu için sorgu planlarında tekrar tekrar gösterilebilir. Her gerçekleştiğinde, sonuçları döndürmek için yeniden yürütülür. Ancak aynı sorguyu yeniden yürütmek, verimsiz olabilir ve gereksiz hesaplama maliyetlerine neden olabilir.

*Parlak CRUISE* , genel hesaplamaları yeniden kullanan, genel sorgu yürütme süresi ve veri aktarımı maliyetlerini düşüren bir iş yükü iyileştirme platformudur. Platform, sonuçları önceden hesaplanmış biçimde depolanan bir sorgu olan *gerçekleştirilmiş bir görünüm* kavramını kullanır. Bu sonuçlar daha sonra, sonuçların tümünün tekrar yeniden oluşturulması yerine sorgunun kendisi daha sonra tekrar görüntülendiğinde yeniden kullanılabilir.

## <a name="setup-and-installation"></a>Kurulum ve yükleme

Daha parlak CRUISE, Spark 2,3 veya 2,4 ile tüm HDInsight 4,0 kümelerinde kullanılabilir. Parlak CRUISE kitaplık dosyaları `/opt/peregrine/` HDInsight kümenizdeki dizine yüklenir. Düzgün şekilde çalışmak için, *parlak CRUISE* , varsayılan olarak ayarlanan aşağıdaki yapılandırma özelliklerini gerektirir.

* `spark.sql.queryExecutionListeners``com.microsoft.peregrine.spark.listeners.PlanLogListener`,, sorgu planlarının günlüğe kaydedilmesini sağlayan olarak ayarlanır.
* `spark.sql.extensions``com.microsoft.peregrine.spark.extensions.SparkExtensionsHdi`, çevrimiçi gerçekleştirmesi ve yeniden kullanım için iyileştirici kuralları sağlayan olarak ayarlanır.

## <a name="computation-reuse-in-spark-sql"></a>Spark SQL 'de hesaplama yeniden kullanımı

Aşağıdaki örnek senaryo, Apache Spark sorgularını iyileştirmek için *parlak cruin* nasıl kullanılacağını göstermektedir. 

1. Spark kümenizin baş düğümüne SSH.
1. `spark-shell` yazın.
1. Kümedeki örnek verileri kullanarak birkaç temel sorgu çalıştıran aşağıdaki kod parçacığını çalıştırın.

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct market from hivesampletable where querytime like '11%'").show
    spark.sql("select distinct state, country from hivesampletable where querytime like '11%'").show
    :quit
    ```
1. Spark uygulama günlüklerinde saklanan önceki sorguların sorgu planlarını çözümlemek için, *parlak CRUISE* sorgu Analizi aracını kullanın. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh analyze views
    ```

1. Bir geri bildirim dosyası olan analiz işleminin çıkışını görüntüleyin. Bu geri bildirim dosyası, gelecekteki Spark SQL sorgularının açıklamalarını içerir. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

`analyze`Komut, sorgu planlarını ayrıştırır ve iş yükünün tablosal gösterimini oluşturur. Bu iş yükü tablosu, [HDInsight parlak CRUISE örnekleri](https://github.com/Azure-Samples/azure-sparkcruise-samples) deposuna eklenen *workloadınsights* Not defteri kullanılarak sorgulanabilir. Ardından, `views` komut ortak alt plan ifadelerini tanımlar ve gelecekteki gerçekleştirmesi ve yeniden kullanım için ilginç alt plan ifadelerini seçer. Çıktı, gelecekteki Spark SQL sorgularının ek açıklamalarını içeren bir geri bildirim dosyasıdır. 

`show`Komut aşağıdaki metin gibi bir çıktı görüntüler:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 4 items
-rw-r--r--   1 sshuser sshuser     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 sshuser sshuser     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 sshuser sshuser      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 sshuser sshuser        536 2020-07-24 16:46 /peregrine/views/views.stp
```

Geri bildirim dosyası aşağıdaki biçimdeki girdileri içerir: `subplan-identifier [Materialize|Reuse] input/path/to/action` . Bu örnekte, iki benzersiz imza vardır: biri, yinelenen ilk iki sorguyu temsil eden ikinci ve en son iki sorgu içindeki filtre koşulunu temsil eder. Bu geri bildirim dosyası ile, yeniden gönderildiğinde aşağıdaki sorgular artık ortak alt planları otomatik olarak planlıyor ve yeniden kullanacaktır. 

İyileştirmeleri test etmek için başka bir örnek sorgu kümesi yürütün.

1. `spark-shell` yazın.
1. Aşağıdaki kod parçacığını yürütün

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct state, country from hivesampletable where querytime like '12%'").show
    spark.sql("select distinct market from hivesampletable where querytime like '12%'").show
    :quit
    ```

1. Yeniden kullanılan sorguların imzalarını görmek için geri bildirim dosyasını tekrar görüntüleyin.

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

`show`Komut aşağıdaki metne benzer bir çıktı verir:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 8 items
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/11259615723090744908
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/1593761760087311271
drwxr-xr-x   - root root          0 2020-07-24 17:22 /peregrine/views/18446744073621796959
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/9409467400931056980
-rw-r--r--   1 root root     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 root root     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 root root      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 root root        536 2020-07-24 16:46 /peregrine/views/views.stp

```

Sorgudaki değişmez değer ' den ' a değiştiği halde `'11%'` `'12%'` , *mini CRUISE* , önceki sorguları, değişmez değer ve veri kümesi sürümlerinin evrimine benzer hafif Çeşitlemeler içeren yeni sorgularla eşleştirebilir. Bir sorguda önemli değişiklikler varsa, yeniden kullanılabilen yeni sorguları belirlemek için çözümleme aracını yeniden çalıştırabilirsiniz.

Arka planda, *parlak CRUISE* , seçilen alt planı içeren ilk sorgudan sorumlu için bir alt sorgu tetikler. Daha sonra sorgular, gerçekleştirilmiş alt planları yeniden hesaplama yerine doğrudan okuyabilir. Bu iş yükünde, alt planlar ilk ve üçüncü sorgular tarafından çevrimiçi olarak uygulanır. Ortak alt planlar gerçekleştirilmiş olduktan sonra sorguların plan değişikliğini görebiliriz.

Artık dört yeni gerçekleştirilmiş alt ifadenin daha sonraki sorgularda yeniden kullanılması gerektiğini görebilirsiniz.

## <a name="clean-up"></a>Temizleme

Geribildirim dosyaları, gerçekleştirilmiş alt planlar ve sorgu günlükleri Spark oturumlarında kalıcı hale getirilir. Bu dosyaları kaldırmak için aşağıdaki komutu çalıştırın:

```bash
sudo /opt/peregrine/analyze/peregrine.sh clean
```

## <a name="next-steps"></a>Sonraki adımlar

* [Mini bir CRUISE 'nin avantajlarını öğrenmek için Iş yükü öngörüleri Not defterini kullanın](https://github.com/Azure-Samples/azure-sparkcruise-samples/tree/main/SparkCruise)
* [Azure HDInsight GÇ önbelleği 'ni kullanarak Apache Spark iş yüklerinin performansını iyileştirme](apache-spark-improve-performance-iocache.md)
* [HDInsight 'ta Apache Spark işlerini iyileştirme](./apache-spark-perf.md)
* [Parlak CRUISE: Spark 'ta el ile hesaplama yeniden kullanımı](https://people.cs.umass.edu/~aroy/sparkcruise-vldb19.pdf)
* [Azure HDInsight 'ta Apache Spark yönergeleri](./spark-best-practices.md)
