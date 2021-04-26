---
title: Apache Hive görünümünde görünmeyen hata iletisi-Azure HDInsight
description: Sorgu Apache Hive görünümünde Azure HDInsight kümesi hakkında herhangi bir ayrıntı olmadan başarısız oluyor.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: c60e06e8f37e7aed0d0a0df661690a2b1f32dbd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931001"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta Apache Hive görünümünde sorgu hatası iletisi görüntülenmiyor

Bu makalede, Azure HDInsight kümelerinde etkileşimli sorgu bileşenlerini kullanırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache Hive görünümü sorgusu hata iletisi, daha fazla bilgi olmadan şuna benzer şekilde görünecektir:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Nedeni

Bazen bir sorgu hatasının hata iletisi Hive görünüm ana sayfasında görüntülenemeyecek kadar büyük olabilir.

## <a name="resolution"></a>Çözüm

Tam StackTrace ve hata Iletisini görmek için, Hive_view sağ üst köşesindeki Bildirimler sekmesini işaretleyin.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]