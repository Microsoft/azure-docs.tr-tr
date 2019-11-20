---
title: Azure HDInsight 'ta en iyi uygulamaları Apache Spark
description: Azure HDInsight 'ta Apache Spark kullanmaya yönelik en iyi yöntemleri öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106131"
---
# <a name="apache-spark-best-practices"></a>En iyi Apache Spark uygulamalar

Bu makalede, Azure HDInsight üzerinde Apache Spark kullanmaya yönelik çeşitli en iyi yöntemler sunulmaktadır.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Nasıl yaparım? Spark işleri mi çalışıyor yoksa gönder?

| Seçenek | Belgeler |
|---|---|
| VSCode | [Visual Studio Code için Spark & Hive araçlarını kullanma](../hdinsight-for-vscode.md) |
| Jupyter Notebooks | [Öğretici: Azure HDInsight 'ta Apache Spark kümesinde verileri yükleme ve sorguları çalıştırma](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Öğretici: HDInsight kümesi için Apache Spark uygulamalar oluşturmak üzere Azure Toolkit for IntelliJ kullanma](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Öğretici: IntelliJ kullanarak HDInsight 'ta Apache Spark için bir Scala Maven uygulaması oluşturma](./apache-spark-create-standalone-application.md) |
| Zeppelin Not defterleri | [Azure HDInsight'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](./apache-spark-zeppelin-notebook.md) |
| Livy ile uzak iş gönderimi | [HDInsight Spark kümesine uzak işleri göndermek için Apache Spark REST API’sini kullanma](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Spark işlerini izleme ve hata ayıklama Nasıl yaparım??

| Seçenek | Belgeler |
|---|---|
| IntelliJ için Azure Araç Takımı | [Azure Toolkit for IntelliJ Spark iş hata ayıklaması başarısız oldu (Önizleme)](apache-spark-intellij-tool-failure-debug.md) |
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
| HDInsight üzerinde Apache HBase | [Apache HBase verilerini okumak ve yazmak için Apache Spark kullanma](../hdinsight-using-spark-query-hbase.md) |
| HDInsight üzerinde Apache Kafka | [Öğretici: HDInsight üzerinde Apache Kafka ile yapılandırılmış Apache Spark akışını kullanma](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Azure platformunda bir lambda mimarisi uygulama](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Depolama seçeneklerim nelerdir?

| Seçenek | Belgeler |
|---|---|
| Data Lake Storage Gen2 | [Azure HDInsight kümeleriyle Azure Data Lake Storage 2. Nesil hizmetini kullanma](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [Azure HDInsight kümeleri ile Data Lake Storage 1. kullanma](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob Depolama | [Azure HDInsight kümeleri ile Azure Depolama'yı kullanma](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Spark ayarlarını yapılandırma](apache-spark-settings.md)
* [HDInsight 'ta Apache Spark işlerini iyileştirme](apache-spark-perf.md)
