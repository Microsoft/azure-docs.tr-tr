---
title: Azure HDInsight 'ta Apache Spark yönergeleri
description: Azure HDInsight 'ta Apache Spark kullanmaya yönelik yönergeleri öğrenin.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: f81435abbedec25cb8abe0b958065a493c15ce4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938818"
---
# <a name="apache-spark-guidelines"></a>Apache Spark yönergeleri

Bu makalede, Azure HDInsight üzerinde Apache Spark kullanmaya yönelik çeşitli yönergeler sunulmaktadır.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Nasıl yaparım? Spark işleri mi çalışıyor yoksa gönder?

| Seçenek | Belgeler |
|---|---|
| VSCode | [Visual Studio Code için Spark & Hive araçlarını kullanma](../hdinsight-for-vscode.md) |
| Jupyter Notebooks | [Öğretici: Azure HDInsight içindeki bir Apache Spark kümesinde veri yükleme ve sorgular çalıştırma](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Öğretici: HDInsight kümesi için Apache Spark uygulamalar oluşturmak üzere Azure Toolkit for IntelliJ kullanma](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Öğretici: IntelliJ kullanarak HDInsight 'ta Apache Spark için bir Scala Maven uygulaması oluşturma](./apache-spark-create-standalone-application.md) |
| Zeppelin not defterleri | [Azure HDInsight'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](./apache-spark-zeppelin-notebook.md) |
| Livy ile uzak iş gönderimi | [HDInsight Spark kümesine uzak işleri göndermek için Apache Spark REST API’sini kullanma](./apache-spark-livy-rest-interface.md) |
|[Apache Oozie](../hdinsight-use-oozie-linux-mac.md)|Oozie, Hadoop işlerini yöneten bir iş akışı ve düzenleme sistemidir.|
|[Apache Livy](./apache-spark-livy-rest-interface.md)|Etkileşimli Spark kabukları çalıştırmak veya Spark üzerinde çalıştırılacak toplu işleri göndermek için Livy 'ı kullanabilirsiniz.|
|[Apache Spark için Azure Data Factory](../../data-factory/transform-data-using-spark.md)|Bir Data Factory işlem hattındaki Spark etkinliği kendi kendinize veya [isteğe bağlı HDInsight kümesinde bir Spark programını yürütür.|
|[Apache Hive için Azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md)|Bir Data Factory işlem hattının HDInsight Hive etkinliği, kendi kendinize veya isteğe bağlı HDInsight kümenizde Hive sorguları yürütür.|

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Spark işlerini izleme ve hata ayıklama Nasıl yaparım??

| Seçenek | Belgeler |
|---|---|
| Azure Toolkit for IntelliJ | [Azure Toolkit for IntelliJ Spark iş hata ayıklaması başarısız oldu (Önizleme)](apache-spark-intellij-tool-failure-debug.md) |
| SSH üzerinden Azure Toolkit for IntelliJ | [SSH aracılığıyla Azure Toolkit for IntelliJ ile HDInsight kümesinde yerel olarak veya uzaktan Apache Spark uygulamalarının hatasını ayıklama](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| VPN üzerinden Azure Toolkit for IntelliJ | [VPN aracılığıyla HDInsight 'ta Apache Spark uygulamalarında uzaktan hata ayıklamak için Azure Toolkit for IntelliJ kullanma](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Apache Spark geçmiş sunucusunda iş grafiği | [Apache Spark uygulamalarının hatasını ayıklamak ve bu uygulamaları tanılamak için genişletilmiş Apache Spark Geçmiş Sunucusunu kullanma](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Spark işlerimin daha verimli bir şekilde çalışmasını Nasıl yaparım? misiniz?

| Seçenek | Belgeler |
|---|---|
| GÇ önbelleği | [Azure HDInsight GÇ önbelleği 'ni (Önizleme) kullanarak Apache Spark iş yüklerinin performansını iyileştirme](./apache-spark-improve-performance-iocache.md) |
| Yapılandırma seçenekleri | [Apache Spark işlerini iyileştirme](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Nasıl yaparım? diğer Azure hizmetlerine bağlansın mı?

| Seçenek | Belgeler |
|---|---|
| HDInsight üzerinde Apache Hive | [Hive ambarı Bağlayıcısı ile Apache Spark ve Apache Hive tümleştirme](../interactive-query/apache-hive-warehouse-connector.md) |
| HDInsight üzerinde Apache HBase | [Apache HBase verilerini okuyup yazmak için Apache Spark kullanma](../hdinsight-using-spark-query-hbase.md) |
| HDInsight üzerinde Apache Kafka | [Öğretici: HDInsight üzerinde Apache Spark Yapılandırılmış Akışını Apache Kafka ile kullanma](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB için Azure Synapse Link](../../cosmos-db/synapse-link.md) |

## <a name="what-are-my-storage-options"></a>Depolama seçeneklerim nelerdir?

| Seçenek | Belgeler |
|---|---|
| Azure Data Lake Storage Gen2 | [Azure HDInsight kümeleriyle Azure Data Lake Storage 2. Nesil hizmetini kullanma](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Azure Data Lake Storage Gen1 | [Azure HDInsight kümeleri ile Azure Data Lake Storage 1. kullanma](../hdinsight-hadoop-use-data-lake-storage-gen1.md) |
| Azure Blob Depolama | [Azure HDInsight kümeleri ile Azure Depolama'yı kullanma](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Spark ayarlarını yapılandırma](apache-spark-settings.md)
* [HDInsight 'ta Apache Spark işlerini iyileştirme](apache-spark-perf.md)
