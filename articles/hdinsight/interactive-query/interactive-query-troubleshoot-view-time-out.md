---
title: Sorgu sonucundan Apache Hive görüntüleme zaman aşımı-Azure HDInsight
description: Azure HDInsight 'ta bir sorgu sonucu getirilirken Apache Hive görüntüleme zaman aşımına uğruyor
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 474fa5e084acfa508a4391075b3c78d96b01aa46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930725"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta bir sorgu sonucu getirilirken Apache Hive görüntüleme zaman aşımına uğruyor

Bu makalede, Azure HDInsight kümelerinde etkileşimli sorgu bileşenlerini kullanırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache Hive görünümünden belirli sorguları çalıştırırken aşağıdaki hata ile karşılaşabilirsiniz:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Nedeni

Hive görünümü varsayılan zaman aşımı değeri, çalıştırdığınız sorgu için uygun olmayabilir. Belirtilen zaman aralığı, Hive görünümü için sorgu sonucunu getirmek üzere çok kısa.

## <a name="resolution"></a>Çözüm

İçinde aşağıdaki özellikleri ayarlayarak Apache ambarı Hive görünümü zaman aşımlarını arttırın `/etc/ambari-server/conf/ambari.properties` .

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

Değeri `HIVE_VIEW_INSTANCE_NAME` Hive görünüm URL 'sinin sonunda kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]