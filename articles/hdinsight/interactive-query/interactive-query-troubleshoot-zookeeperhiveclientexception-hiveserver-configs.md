---
title: Apache Hive Zeppelin yorumlayıcı hatası-Azure HDInsight
description: Apache Zeppelin Hive JDBC yorumlayıcı, Azure HDInsight 'ta yanlış URL 'yi işaret ediyor
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 2d6e660459d9f350cc5c63fb8d312bcbcb300a64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930749"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Senaryo: Apache Hive Zeppelin yorumlayıcı, Azure HDInsight 'ta bir Zookeeper hatası veriyor

Bu makalede, Azure HDInsight kümelerinde etkileşimli sorgu bileşenlerini kullanırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache Hive LLAP kümesinde, Zeppelin yorumlayıcı bir sorguyu yürütmeye çalışırken aşağıdaki hata iletisini verir:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Nedeni

Zeppelin Hive JDBC yorumlayıcı yanlış URL 'yi işaret ediyor.

## <a name="resolution"></a>Çözüm

1. Hive bileşen Özeti ' ne gidin ve "Hive JDBC URL" öğesini panoya kopyalayın.

1. `https://clustername.azurehdinsight.net/zeppelin/#/interpreter` sayfasına gidin

1. JDBC ayarlarını düzenleyin: 1. adımda kopyalanmış olan Hive JDBC URL 'si için Hive. URL değerini güncelleştirin

1. Sorguyu kaydedin ve sonra yeniden deneyin

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]