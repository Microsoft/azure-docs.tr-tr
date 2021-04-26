---
title: Apache Zookeeper 'a bağlantı Apache Hive-Azure HDInsight
description: Azure HDInsight 'ta Apache Zookeeper sorunları nedeniyle Apache Hive görünümüne erişilemiyor
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 42fb9f5389cbc31e772dc9cf36b6a975c5e18d3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939302"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>Senaryo: Apache Hive Azure HDInsight 'ta Apache Zookeeper bağlantısı kuramıyor

Bu makalede, Azure HDInsight kümelerinde etkileşimli sorgu bileşenlerini kullanırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Hive görünümüne erişilemez ve içindeki Günlükler `/var/log/hive` aşağıdakine benzer bir hata gösterir:

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>Nedeni

Hive 'nin, Hive görünümünün başlamasını önleyen Zookeeper bağlantısı kurmasının başarısız olması olasıdır.

## <a name="resolution"></a>Çözüm

1. Zookeeper hizmetinin sağlıklı olup olmadığını denetleyin.

1. Zookeeper hizmetinin, Sunucu2 için bir ZNode girişi olup olmadığını denetleyin. Değer eksik ya da yanlış olacaktır.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. Bağlantıyı yeniden kurmak için Zookeeper düğümlerini yeniden başlatın ve HiveServer2 ' yi yeniden başlatın.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]