---
title: Azure HDInsight 'ta Apache Hive tablo ile izin reddedildi hatası
description: Azure HDInsight 'ta bir Apache Hive tablosu oluşturulmaya çalışılırken izin reddedildi hatası
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: 6d91ca747bb264b4c7262eec2bb0745b8a40b7a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930925"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta bir Apache Hive tablosu oluşturulmaya çalışılırken Izin reddedildi hatası

Bu makalede, Azure HDInsight kümelerinde etkileşimli sorgu bileşenlerini kullanırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Tablo oluşturmaya çalışırken şu hatayı görürsünüz:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Aşağıdaki, şu işlem depolama komutunu çalıştırırsanız benzer bir hata iletisi görürsünüz:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Nedeni

Apache Hive bir tablo oluşturma özelliği, kümenin depolama hesabına uygulanan izinler tarafından belirlenir. Küme depolama hesabı izinleri yanlış ise, tablo oluşturabileceksiniz. Bu, tablo oluşturma için doğru Ranger ilkelerine sahip olabileceği ve yine de "Izin reddedildi" hataları göremeyeceğiniz anlamına gelir.

## <a name="resolution"></a>Çözüm

Bu, kullanılan depolama kapsayıcısı üzerinde yeterli izinlerin bulunmaması nedeniyle oluşur. Hive tablosunu oluşturan kullanıcının kapsayıcıya karşı okuma, yazma ve yürütme izinleri olması gerekir. Daha fazla bilgi için bkz. [HDP 2,2 ' de Apache Ranger kullanarak Hive yetkilendirmesi Için En Iyi uygulamalar](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]