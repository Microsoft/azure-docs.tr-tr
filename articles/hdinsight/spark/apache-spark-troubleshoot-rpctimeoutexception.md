---
title: Apache Spark Thrift-Azure HDInsight için RpcTimeoutException
description: Apache Spark Thrift Server kullanarak büyük veri kümelerini işlerken 502 hata görürsünüz
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: c2975599272474fed9d61702fc1dbceb946c1190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98932708"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta Apache Spark Thrift sunucusu için RpcTimeoutException

Bu makalede, Azure HDInsight kümelerinde Apache Spark bileşenleri kullanılırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Spark uygulaması `org.apache.spark.rpc.RpcTimeoutException` `Futures timed out` Aşağıdaki örnekte olduğu gibi bir özel durumla ve iletiyle başarısız olur:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError``overhead limit exceeded`Ayrıca, `sparkthriftdriver.log` Aşağıdaki örnekte olduğu gibi hatalar da görüntülenebilir:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Nedeni

Bu hatalar, veri işleme sırasında bellek kaynaklarının bulunmaması nedeniyle oluşur. Java atık toplama işlemi başlatılırsa, Spark uygulamasının yanıt vermeyi durdurmasına neden olabilir. Sorgular zaman aşımına uğrar ve işlemeyi durduracaktır. `Futures timed out`Hata, ciddi bir stres altında bir kümeyi gösterir.

## <a name="resolution"></a>Çözüm

Daha fazla çalışan düğümü ekleyerek veya mevcut küme düğümlerinin bellek kapasitesini artırarak küme boyutunu artırın. Aynı zamanda işlenen veri miktarını azaltmak için veri ardışık düzenini de ayarlayabilirsiniz.

`spark.network.timeout`Tüm ağ bağlantıları için zaman aşımını denetler. Ağ zaman aşımını artırmak bazı kritik işlemlerin tamamlanabilmesi için daha fazla zaman alabilir, ancak bu durum sorunu tamamen çözmeyecektir.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]