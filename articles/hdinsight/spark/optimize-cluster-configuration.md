---
title: Apache Spark küme yapılandırmasını iyileştirme-Azure HDInsight
description: Azure HDInsight 'ta üretilen işi en üst düzeye çıkarmak için Apache Spark kümenizi nasıl yapılandıracağınızı öğrenin.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: f70e9f242b0f12abf58e72554e7cb6819ce8f7a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944728"
---
# <a name="cluster-configuration-optimization-for-apache-spark"></a>Apache Spark için küme yapılandırması iyileştirmesi

Bu makalede, Azure HDInsight 'ta en iyi performans için Apache Spark kümenizin yapılandırmasının nasıl iyileştirileceği açıklanır.

## <a name="overview"></a>Genel Bakış

Spark kümesi iş yükünüze bağlı olarak, varsayılan olmayan bir Spark yapılandırmasının daha iyileştirilmiş Spark iş yürütmeye neden olabileceğini belirleyebilirsiniz.  Varsayılan olmayan küme yapılandırmalarının doğrulanması için örnek iş yükleriyle kıyaslama testi yapın.

Ayarlayabileceğiniz bazı yaygın parametreler şunlardır:

|Parametre |Açıklama |
|---|---|
|--NUM-yürüticileri|Uygun sayıda yürütme sayısını ayarlar.|
|--Yürütücü-çekirdekler|Her bir yürütücü için çekirdek sayısını ayarlar. Genellikle, diğer süreçler kullanılabilir belleğin bir kısmını tükettiği için orta ölçekli yürüticiler olmalıdır.|
|--Yürütücü-bellek|YARN üzerindeki yığın boyutunu denetleyen her bir yürütücü için bellek boyutunu ayarlar. Yürütme ek yükü için bir miktar bellek bırakın.|

## <a name="select-the-correct-executor-size"></a>Doğru yürütücü boyutunu seçin

Yürütücü yapılandırmanıza karar verirken Java çöp toplama (GC) ek yükünü göz önünde bulundurun.

* Yürütücü boyutunu azaltmanın faktörleri:
    1. %10 < GC yükünü korumak için 32 GB altındaki yığın boyutunu küçültün.
    2. %10 < GC yükünü korumak için çekirdek sayısını azaltın.

* Yürütücü boyutunu artırma faktörleri:
    1. Yürüticileri arasındaki iletişim yükünü azaltın.
    2. Daha büyük kümelerdeki (>100 yürüticileri) yürüticileri (N2) arasındaki açık bağlantı sayısını azaltın.
    3. Bellek kullanımı yoğun görevlere uyum sağlamak için yığın boyutunu artırın.
    4. İsteğe bağlı: yürütücü başına bellek ek yükünü azaltın.
    5. İsteğe bağlı: aşırı abone olma CPU 'SU ile kullanımı ve eşzamanlılık artırma.

Genel bir kural olarak, yürütücü boyutunu seçerken:

1. Yürütücü başına 30 GB ile başlayın ve kullanılabilir makine çekirdeğini dağıtın.
2. Daha büyük kümeler (> 100 yürüticileri) için Yürütücü çekirdekleri sayısını artırın.
3. Hem deneme çalışmalarından hem de GC yükü gibi önceki faktörlere göre boyut boyutunu değiştirin.

Eşzamanlı sorgular çalıştırılırken şunları göz önünde bulundurun:

1. Yürütücü başına 30 GB ve tüm makine çekirdekleri ile başlayın.
2. Daha fazla sayıda paralel Spark uygulaması oluşturun (%30 ' dan fazla gecikme geliştirmesi).
3. Sorguları paralel uygulamalar arasında dağıtın.
4. Hem deneme çalışmalarından hem de GC yükü gibi önceki faktörlere göre boyut boyutunu değiştirin.

Yürüticileri yapılandırmak için ambarı kullanma hakkında daha fazla bilgi için bkz. [Apache Spark Settings-Spark yürüticileri](apache-spark-settings.md#configuring-spark-executors).

Zaman çizelgesi görünümüne bakarak, aykırı değerler veya diğer performans sorunları için sorgu performansını izleyin. Ayrıca SQL Graph, iş istatistikleri ve benzeri. YARN ve Spark geçmiş sunucusunu kullanarak Spark işlerinin hatalarını ayıklama hakkında bilgi için bkz. [Azure HDInsight üzerinde çalışan hata ayıklama Apache Spark işleri](apache-spark-job-debugging.md). YARN zaman çizelgesi sunucusu kullanma hakkında ipuçları için bkz. [erişim Apache HADOOP YARN uygulama günlükleri](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Bazen yürüticilerinin biri veya birkaçı diğerlerinden daha yavaştır ve görevlerin yürütülmesi çok daha uzun sürer. Bu yavaşlığın genellikle daha büyük kümeler (> 30 düğüm) üzerinde meydana gelir. Bu durumda, Scheduler 'ın yavaş görevleri dengeyapabilmesi için çalışmayı daha fazla sayıda göreve bölün. Örneğin, uygulamadaki yürütücü çekirdekleri sayısı kadar en az iki görev vardır. Ayrıca, ile görevlerin yansımalı yürütülmesini de etkinleştirebilirsiniz `conf: spark.speculation = true` .

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Spark için veri işlemeyi iyileştirin](optimize-cluster-configuration.md)
* [Apache Spark için veri depolamayı iyileştirin](optimize-data-storage.md)
* [Apache Spark için bellek kullanımını iyileştirin](optimize-memory-usage.md)
