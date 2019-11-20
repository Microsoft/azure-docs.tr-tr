---
title: Apache Spark-Azure HDInsight 'tan ınvalidclassexception hatası
description: Apache Spark iş, Azure HDInsight 'ta ınvalidclassexception, sınıf sürümü uyuşmazlığı ile başarısız oluyor
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 124d5586180258589c5db17454b8fbf1e465fc24
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106493"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Apache Spark iş, Azure HDInsight 'ta ınvalidclassexception, sınıf sürümü uyuşmazlığı ile başarısız oluyor

Bu makalede, Azure HDInsight kümelerinde Apache Spark bileşenleri kullanılırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Spark 2. x kümesinde bir Apache Spark işi oluşturmaya çalışırsınız. Aşağıdakine benzer bir hata ile başarısız olur:

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>Nedeni

Bu hata, `spark.yarn.jars` config 'e ek bir jar eklenerek, özellikle de `commons-lang3` paketinin farklı bir sürümünü içeren ve bir sınıf uyumsuzluğu sunan bir gölgeli jar 'nin oluşmasına neden olabilir. Varsayılan olarak, Spark 2.1/2/3 `commons-lang3`sürüm 3,5 ' ü kullanır.

> [!TIP]
> Bir kitaplığı gölgelendirmek için, içeriğini kendi jar 'ınızla yerleştirmekte ve paketini değiştirmiş olmanız gerekir. Bu, kitaplığı paketlemeden ve yeniden paketlemeden kitaplığı kendi jar 'nize yerleştirmekten farklıdır.

## <a name="resolution"></a>Çözüm

Jar 'yi kaldırın veya özelleştirilmiş jar 'yi (AzureLogAppender) yeniden derleyin ve sınıfları yeniden konumlandırmak için [Maven-gölge-Plugin](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport) ile bağlanma-Azure Community 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabı: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için lütfen [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) sayfasını inceleyin. Abonelik Yönetimi’ne ve faturalandırma desteğine erişim Microsoft Azure aboneliğinize dahildir, Teknik Destek ise herhangi bir [Azure Destek Planı](https://azure.microsoft.com/support/plans/) üzerinden sağlanır.
