---
title: Azure HDInsight 'ta Apache Kafka verilerle Noclassdeffounbir Apache Spark
description: Bir Apache Kafka kümesinden verileri okuyan Apache Spark akış işi, Azure HDInsight 'ta Noclassdeffounbir hata ile başarısız oluyor
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 4d00cbcb0151da39feb0cb015660291af544d7f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946370"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>Apache Kafka verileri okuyan Apache Spark akış işi HDInsight 'ta Noclassdeffounbir hata ile başarısız oluyor

Bu makalede, Azure HDInsight kümelerinde Apache Spark bileşenleri kullanılırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache Spark kümesi, bir Apache Kafka kümesinden verileri okuyan bir Spark akış işi çalıştırır. Kafka akış sıkıştırması açıksa Spark akış işi başarısız olur. Bu durumda, Spark streaming Yarn uygulaması application_1525986016285_0193 hata nedeniyle başarısız oldu:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Nedeni

Bu hata, `spark-streaming-kafka` çalıştırmakta olduğunuz Kafka kümesinin sürümünden farklı bir jar dosyası sürümü belirtilerek oluşabilir.

Örneğin, bir Kafka Cluster sürüm 0.10.1 çalıştırıyorsanız, aşağıdaki komut bir hataya neden olur:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Çözüm

Spark-Gönder komutunu `–packages` seçeneğiyle kullanın ve Spark-streaming-Kafka jar dosyasının sürümünün çalıştırdığınız Kafka kümesinin sürümüyle aynı olduğundan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]