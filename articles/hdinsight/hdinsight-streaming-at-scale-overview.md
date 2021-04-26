---
title: Azure HDInsight 'ta ölçekte akış
description: Azure HDInsight 'ta ölçeklenebilir Apache kümeleriyle veri akışını kullanma.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 3bf036ff413f720f54deef1a62b502668d1d8ac6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872005"
---
# <a name="streaming-at-scale-in-hdinsight"></a>HDInsight’ta ölçeğe göre akış

Gerçek zamanlı büyük veri çözümleri, hareket halindeki veriler üzerinde çalışır. Genellikle, bu veriler en fazla varış zamanında değerlidir. Gelen veri akışı bu anda işlenebileceğinden daha büyük olursa, kaynakları daraltmanız gerekebilir. Alternatif olarak, bir HDInsight kümesi, isteğe bağlı düğümler ekleyerek akış çözümünüzü karşılayacak şekilde ölçeklenebilir.

Bir akış uygulamasında, bir veya daha fazla veri kaynağı, yararlı bilgileri bırakmadan hızlı bir şekilde atılması gereken olayları (bazen milyonlarca saniyede milyonlarca) oluşturuyor. Gelen olaylar, [Apache Kafka](kafka/apache-kafka-introduction.md) veya [Event Hubs](https://azure.microsoft.com/services/event-hubs/)gibi bir hizmet tarafından *olay Kuyruklama* olarak da adlandırılan *akış ara belleği* ile işlenir. Olayları topladıktan sonra, [Apache Storm](storm/apache-storm-overview.md) veya [Apache Spark akışı](spark/apache-spark-streaming-overview.md)gibi *akış işleme* katmanında gerçek zamanlı bir analiz sistemi kullanarak verileri çözümleyebilirsiniz. İşlenen veriler, [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)gibi uzun süreli depolama sistemlerinde depolanabilir ve [Power BI](https://powerbi.microsoft.com), Tableau veya özel bir Web sayfası gibi bir iş zekası panosunda gerçek zamanlı olarak görüntülenebilir.

:::image type="content" source="./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png" alt-text="Azure HDInsight akış desenleri":::

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka, yüksek performanslı, düşük gecikmeli bir ileti sıraya alma hizmeti sağlar ve artık Apache 'nin açık kaynak yazılım (OSS) paketinin bir parçasıdır. Kafka, bir yayımlama ve abone olma mesajlaşma modeli kullanır ve bölümlenmiş verilerin akışlarını dağıtılmış, çoğaltılan bir kümede güvenli bir şekilde depolar. Kafka, üretilen iş arttıkça doğrusal olarak ölçeği ölçeklendirir.

Daha fazla bilgi için bkz. [HDInsight 'ta Apache Kafka tanıtımı](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm, Hadoop ile gerçek zamanlı olarak veri akışlarını işlemek için optimize edilmiş, dağıtılmış, hataya dayanıklı ve açık kaynaklı bir hesaplama sistemidir. Gelen bir olay için çekirdek veri birimi, sabit bir anahtar/değer çiftleri kümesi olan bir kayıt kümesidir. Bu tanımlama gruplarının sınırsız bir sırası, Spout 'tan gelen bir akış oluşturur. Spout bir akış veri kaynağını (örneğin, Kafka) sarmalanmış ve tanımlama gruplarını yayar. Bir fırtınası topolojisi, bu akışlarda dönüşümlerdeki bir dizidir.

Daha fazla bilgi için bkz. [Azure HDInsight 'ta Apache Storm nedir?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark Streaming

Spark akışı, toplu işleme için kullandığınız kodu yeniden kullanmanıza olanak tanıyan Spark uzantısıdır. Aynı uygulamadaki hem Batch hem de etkileşimli sorguları birleştirebilirsiniz. Bu durumda, Spark akışı, durum bilgisi olarak bir kez işleme semantiğini sağlar. [Kafka DIRECT API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html)'siyle birlikte kullanıldığında, tüm Kafka verilerinin tek bir kez Spark akışı tarafından alındığından emin olmanızı sağlayan, uçtan uca tam olarak bir kez garanti elde etmek mümkündür. Spark akışının güçlerinden biri hataya dayanıklı yeteneklerdir ve küme içinde birden çok düğüm kullanılırken, hatalı düğümleri hızlı bir şekilde kurtarıyor.

Daha fazla bilgi için bkz. [Apache Spark akışı nedir?](./spark/apache-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Küme ölçekleme

Oluşturma sırasında kümenizdeki düğüm sayısını belirtebilseniz de, iş yüküyle eşleşmesi için kümeyi büyütmek ya da küçültmek isteyebilirsiniz. Tüm HDInsight kümeleri [kümedeki düğümlerin sayısını değiştirmenize](hdinsight-administer-use-portal-linux.md#scale-clusters)izin verir. Tüm veriler Azure depolama 'da veya Data Lake Storage depolandığından, Spark kümeleri veri kaybı olmadan bırakılabilir.

Teknoloji kullanmanın avantajları vardır. Örneğin, Kafka bir olay arabelleğe alma teknolojisidir, bu nedenle çok fazla GÇ kullanımı ve çok fazla işlem gücü gerektirmez. Buna karşılık, Spark akışı gibi akış işlemcileri, daha güçlü VM 'Ler gerektiren işlem yoğunluklu bir işlemdir. Bu teknolojilerin farklı kümelere ayrılmasıyla, VM 'Leri en iyi şekilde kullanarak bunları bağımsız olarak ölçeklendirebilirsiniz.

### <a name="scale-the-stream-buffering-layer"></a>Akış arabelleğe alma katmanını ölçeklendirme

Akış arabelleğe alma teknolojileri Event Hubs ve Kafka her ikisi de bu bölümlerden okunan bölümleri ve müşterileri kullanır. Giriş aktarım hızını ölçeklendirmek için bölüm sayısının ölçeğini ölçekleme gerekir ve bölüm eklemek de artan paralellik sağlar. Event Hubs, bölüm sayısı dağıtımdan sonra değiştirilemez, bu nedenle hedef ölçeklendirmeye göz önünde bulundurularak başlamak önemlidir. Kafka ile, Kafka verileri işlerken bile [bölüm eklemek](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion)mümkündür. Kafka, bölümleri yeniden atamak için bir araç sağlar  `kafka-reassign-partitions.sh` . HDInsight, [bölüm çoğaltma yeniden dengeleme aracı](https://github.com/hdinsight/hdinsight-kafka-tools)sağlar  `rebalance_rackaware.py` . Bu yeniden dengeleme Aracı, `kafka-reassign-partitions.sh` her çoğaltmanın ayrı bir hata etki alanında ve güncelleştirme etki alanında olması, Kafka rafa göz önünde bulundurulması ve hata toleransı artırdığından bu şekilde aracı çağırır.

### <a name="scale-the-stream-processing-layer"></a>Akış işleme katmanını ölçeklendirme

Hem Apache Storm hem de Spark akışı, veriler işlenirken bile, kümelerine çalışan düğümlerin eklenmesini destekler.

Ölçeklendirme fırtınası aracılığıyla eklenen yeni düğümlerden yararlanmak için küme boyutu artırılmadan önce başlatılan tüm fırtınası topolojilerini yeniden dengelemeniz gerekir. Bu yeniden dengeleme, fırtınası Web Kullanıcı arabirimi veya CLı 'sı kullanılarak yapılabilir. Daha fazla bilgi için [Apache Storm belgelerine](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)bakın.

Apache Spark, uygulama gereksinimlerine bağlı olarak, ortamını yapılandırmak için üç temel parametre kullanır: `spark.executor.instances` , `spark.executor.cores` ve `spark.executor.memory` . *Yürütücü* , Spark uygulaması için başlatılan bir işlemdir. Bir yürütücü çalışan düğümünde çalışır ve uygulamanın görevlerinin çalıştırılmasından sorumludur. Her küme için varsayılan yürütme sayısı ve yürütücü boyutları, çalışan düğümlerinin sayısı ve çalışan düğüm boyutu temel alınarak hesaplanır. Bu numaralar, `spark-defaults.conf` her bir küme başlığı üzerindeki dosyada depolanır.

Bu üç parametre küme düzeyinde yapılandırılabilir, küme üzerinde çalışan tüm uygulamalar için de belirlenebilir ve her bir uygulama için de belirtilebilir. Daha fazla bilgi için bkz. [Apache Spark kümeleri için kaynakları yönetme](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure HDInsight 'ta Apache Storm topolojisi oluşturma ve izleme](storm/apache-storm-quickstart.md)
* [HDInsight üzerinde Apache Storm için örnek topolojiler](storm/apache-storm-example-topology.md)
* [HDInsight üzerinde Apache Spark giriş](spark/apache-spark-overview.md)
* [HDInsight üzerinde Apache Kafka'yı kullanmaya başlama](kafka/apache-kafka-get-started.md)