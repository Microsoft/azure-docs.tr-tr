---
title: Apache Spark App 'ten Requestbodytoobüyük hatası-Azure HDInsight
description: NativeAzureFileSystem ... Azure HDInsight 'ta Apache Spark akış uygulaması günlüğünde RequestBodyTooLarge görünüyor
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 73ae646cb083841ee1d55b6c7ce6af7180cef08e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929432"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... HDInsight 'ta Apache Spark akış uygulama günlüğünde RequestBodyTooLarge "görüntülenir

Bu makalede, Azure HDInsight kümelerinde Apache Spark bileşenleri kullanılırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Hata: `NativeAzureFileSystem ... RequestBodyTooLarge` Apache Spark bir akış uygulamasının sürücü günlüğünde görüntülenir.

## <a name="cause"></a>Nedeni

Spark olay günlüğü dosyanız muhtemelen LıBB 'nin dosya uzunluğu sınırına ulaşıyordur.

Spark 2,3 ' de, her Spark uygulaması bir Spark olay günlüğü dosyası oluşturur. Spark akış uygulamasının Spark olay günlüğü dosyası, uygulama çalışırken büyümeye devam eder. Günümüzde IDB 'deki bir dosya 50000 blok sınırına sahiptir ve varsayılan blok boyutu 4 MB 'tır. Bu nedenle, varsayılan yapılandırmada en büyük dosya boyutu 195 GB 'tır. Ancak, Azure Storage en büyük blok boyutunu 100 MB olarak artırmıştır ve bu, tek dosya sınırını 4,75 TB 'ye etkin bir şekilde getirdi. Daha fazla bilgi için bkz. [BLOB depolama Için ölçeklenebilirlik ve performans hedefleri](../../storage/blobs/scalability-targets.md).

## <a name="resolution"></a>Çözüm

Bu hata için üç çözüm bulunmaktadır:

* Blok boyutunu 100 MB 'a kadar artırın. Ambarı Kullanıcı arabiriminde, suconfiguration özelliğini değiştirin `fs.azure.write.request.size` (veya `Custom core-site` bölümünde oluşturun). Özelliği daha büyük bir değere ayarlayın, örneğin: 33554432. Güncelleştirilmiş yapılandırmayı kaydedin ve etkilenen bileşenleri yeniden başlatın.

* Spark akış işini düzenli aralıklarla durdurup yeniden gönderin.

* Spark olay günlüklerini depolamak için, "kullanın. Depolama için, bellek kullanımı, küme ölçekleme veya Azure yükseltmeleri sırasında Spark olay verilerinin kaybedilmesine neden olabilir.

    1. `spark.eventlog.dir` `spark.history.fs.logDirectory` Ambarı Kullanıcı arabiriminde ve aracılığıyla değişiklik yapın:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Şu sunucuda dizin oluşturma:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Tüm etkilenen Hizmetleri, ambarı Kullanıcı arabirimi aracılığıyla yeniden başlatın.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]