---
title: JDBC/ODBC & Apache Thrift çerçevesi ile ilgili sorunlar-Azure HDInsight
description: Azure HDInsight 'ta JDBC/ODBC ve Apache Thrift yazılım çerçevesi kullanılarak büyük veri kümeleri indirilemedi
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 7423687cbca069792a584277699c6bf8ce904d5d
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241746"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>HDInsight 'ta JDBC/ODBC ve Apache Thrift Software Framework kullanılarak büyük veri kümeleri indirilemedi

Bu makalede, Azure HDInsight kümelerinde Apache Spark bileşenleri kullanılırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Azure HDInsight 'ta JDBC/ODBC ve Apache Thrift yazılım çerçevesini kullanarak büyük veri kümelerini indirmeye çalışırken, aşağıdakine benzer bir hata iletisi alırsınız:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Nedeni

Bu özel durum, serileştirme işleminin izin verilenden daha fazla arabellek alanı kullanmaya çalışılması nedeniyle oluşur. Spark 2.0.0 ' de, veri Apache Thrift yazılım çerçevesi aracılığıyla erişildiğinde nesneleri serileştirmek için `org.apache.spark.serializer.KryoSerializer` sınıfı kullanılır. Ağ üzerinden gönderilecek veya seri hale getirilmiş biçimde önbelleğe alınacak veriler için farklı bir sınıf kullanılır.

## <a name="resolution"></a>Çözünürlük

`Kryoserializer` arabellek değerini artırın. `spark.kryoserializer.buffer.max` adlı bir anahtar ekleyin ve `Custom spark2-thrift-sparkconf`altındaki spark2 config içinde `2048` olarak ayarlayın.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport) ile bağlanma-Azure Community 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabı: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için lütfen [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
