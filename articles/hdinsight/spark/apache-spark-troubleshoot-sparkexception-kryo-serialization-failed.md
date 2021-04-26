---
title: JDBC/ODBC & Apache Thrift çerçevesi ile ilgili sorunlar-Azure HDInsight
description: Azure HDInsight 'ta JDBC/ODBC ve Apache Thrift yazılım çerçevesi kullanılarak büyük veri kümeleri indirilemedi
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/14/2020
ms.openlocfilehash: 0b96bc9f6a78d07e091e791b769056859183da0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945117"
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

Bu özel durum, serileştirme işleminin izin verilenden daha fazla arabellek alanı kullanmaya çalışılması nedeniyle oluşur. Spark 2.0.0 içinde, `org.apache.spark.serializer.KryoSerializer` veri Apache Thrift yazılım çerçevesi aracılığıyla erişildiğinde nesneleri serileştirmek için sınıfı kullanılır. Ağ üzerinden gönderilecek veya seri hale getirilmiş biçimde önbelleğe alınacak veriler için farklı bir sınıf kullanılır.

## <a name="resolution"></a>Çözüm

`Kryoserializer`Arabellek değerini artırın. Adlı bir anahtar ekleyin `spark.kryoserializer.buffer.max` ve `2047` altında spark2 config içine ayarlayın `Custom spark2-thrift-sparkconf` . Etkilenen tüm bileşenleri yeniden başlatın.

> [!IMPORTANT]
> Değeri `spark.kryoserializer.buffer.max` 2048 'den az olmalıdır. Kesirli değerler desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Azure Community 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabı ile bağlanın: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.