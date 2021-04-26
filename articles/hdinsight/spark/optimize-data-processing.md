---
title: Apache Spark için veri işlemeyi iyileştirme-Azure HDInsight
description: Azure HDInsight ile Apache Spark verilerinizi işlemek için en verimli işlemleri nasıl seçebileceğinizi öğrenin.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 98457456bc1c8f9fdb08c32fdcd319a3aa9ff14a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944717"
---
# <a name="data-processing-optimization-for-apache-spark"></a>Apache Spark için veri işleme iyileştirmesi

Bu makalede, Azure HDInsight 'ta en iyi performans için Apache Spark kümenizin yapılandırmasının nasıl iyileştirileceği açıklanır.

## <a name="overview"></a>Genel Bakış

Bir JOIN veya karıştırma üzerinde işleriniz yavaşsa, nedeni büyük olasılıkla *veri eğriliği* olur. Veri eğriltme iş verilerinizde asymmetry. Örneğin, bir harita işi 20 saniye sürebilir. Ancak verilerin katıldığı veya karıştırılmış bir işi çalıştırmak saat alır. Veri eğriliğini onarmak için, anahtarın tamamını tam olarak yapmanız gerekir ya da anahtarların yalnızca bir alt kümesi için *yalıtılmış bir anahtar* kullanırsınız. Yalıtılmış bir güvenlik kullanıyorsanız, eşleme birleştirmelerde sallanan anahtarların alt kümesini yalıtmak için daha fazla filtre uygulamanız gerekir. Diğer bir seçenek de demet sütunu tanıtmak ve öncelikle demetlerde ön toplama işlemi yapmak.

Yavaş birleştirmelere neden olan başka bir faktör birleştirme türü olabilir. Spark, varsayılan olarak, `SortMerge` JOIN türünü kullanır. Bu tür bir JOIN, büyük veri kümeleri için idealdir. Ancak, öncelikle verilerin sol ve sağ taraflarını birleştirmeden önce sıralaması gerektiğinden, bu da hesaplama açısından pahalı değildir.

Bir `Broadcast` JOIN, daha küçük veri kümeleri için en uygun seçenektir veya birleştirmenin bir tarafı diğer taraftan çok daha küçüktür. Bu tür bir JOIN, tüm yürüticilerine bir taraf yayınlar ve bu nedenle genel olarak yayınlar için daha fazla bellek gerektirir.

Ayarları yaparak yapılandırmanızda JOIN türünü değiştirebilir `spark.sql.autoBroadcastJoinThreshold` veya DataFrame API 'lerini () kullanarak bir JOIN ipucu ayarlayabilirsiniz `dataframe.join(broadcast(df2))` .

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Buckelenmiş tablolar kullanıyorsanız, birleşimi üçüncü bir birleşme türüne sahip olursunuz `Merge` . Doğru bir önceden bölümlenmiş ve önceden sıralanmış veri kümesi, bir birleşimden pahalı sıralama aşamasını atlar `SortMerge` .

Birleştirme sırası, özellikle daha karmaşık sorgularda önemlidir. En seçmeli birleştirmelere başlayın. Ayrıca, mümkün olduğunda toplamaların ardından satır sayısını artıran birleştirmeleri taşıyın.

Kartezyen birleştirmeler için paralellik 'i yönetmek üzere iç içe yapılar, Pencereleme ve belki Spark işinizdeki bir veya daha fazla adımı atlayabilirsiniz.

## <a name="optimize-job-execution"></a>İş yürütmeyi iyileştirme

* Gerekirse, örneğin, verileri iki kez kullanırsanız, önbelleğe alma.
* Değişkenleri tüm yürüticilerine yayınla. Değişkenler yalnızca bir kez serileştirilir ve daha hızlı aramalar elde edilir.
* Sürücü üzerindeki iş parçacığı havuzunu kullanın, bu da birçok görev için daha hızlı işlem sağlar.

Performans sorunları için çalışan işlerinizi düzenli olarak izleyin. Belirli sorunlar hakkında daha fazla bilgi için, aşağıdaki performans profili oluşturma araçlarından birini göz önünde bulundurun:

* [INTEL PAL aracı](https://github.com/intel-hadoop/PAT) CPU, depolama ve ağ bant genişliği kullanımını izler.
* [Oracle Java 8 görev denetimi](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profilleri Spark ve yürütücü kodu.

Spark 2. x sorgu performansına yönelik anahtar, tam aşamalı kod oluşturmaya bağlı olan tungsten altyapısıdır. Bazı durumlarda, tam aşama kod üretimi devre dışı bırakılabilir. Örneğin, toplama ifadesinde kesilebilir olmayan bir tür ( `string` ) kullanırsanız, `SortAggregate` yerine görünür `HashAggregate` . Örneğin, daha iyi performans için aşağıdakileri deneyin ve kod oluşturmayı yeniden etkinleştirin:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Spark için veri depolamayı iyileştirin](optimize-data-storage.md)
* [Apache Spark için bellek kullanımını iyileştirin](optimize-memory-usage.md)
* [Apache Spark için küme yapılandırmasını iyileştirme](optimize-cluster-configuration.md)
