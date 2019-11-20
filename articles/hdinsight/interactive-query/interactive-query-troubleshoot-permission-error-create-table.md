---
title: Azure HDInsight 'ta Apache Hive tablo ile izin reddedildi hatası
description: Azure HDInsight 'ta bir Apache Hive tablosu oluşturulmaya çalışılırken izin reddedildi hatası
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: d9901132af992ea95a60773f404b1351386cfbcb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494205"
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

## <a name="resolution"></a>Çözünürlük

Bu, kullanılan depolama kapsayıcısı üzerinde yeterli izinlerin bulunmaması nedeniyle oluşur. Hive tablosunu oluşturan kullanıcının kapsayıcıya karşı okuma, yazma ve yürütme izinleri olması gerekir. Daha fazla bilgi için bkz. [HDP 2,2 ' de Apache Ranger kullanarak Hive yetkilendirmesi Için En Iyi uygulamalar](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport) ile bağlanma-Azure Community 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabı: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için lütfen [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) sayfasını inceleyin. Abonelik Yönetimi’ne ve faturalandırma desteğine erişim Microsoft Azure aboneliğinize dahildir, Teknik Destek ise herhangi bir [Azure Destek Planı](https://azure.microsoft.com/support/plans/) üzerinden sağlanır.
