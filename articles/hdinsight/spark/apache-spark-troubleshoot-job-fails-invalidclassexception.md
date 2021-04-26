---
title: Apache Spark-Azure HDInsight 'tan ınvalidclassexception hatası
description: Apache Spark iş, Azure HDInsight 'ta ınvalidclassexception, sınıf sürümü uyuşmazlığı ile başarısız oluyor
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 0b0889ac1e71ce33406e89ead62370a0c0168763
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929268"
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

Bu hata, yapılandırmaya ek bir jar eklenerek `spark.yarn.jars` , özellikle de paketin farklı bir sürümünü içeren `commons-lang3` ve bir sınıf uyumsuzluğu sunan bir gölgeli jar 'nin oluşmasına neden olabilir. Varsayılan olarak, Spark 2.1/2/3 sürüm 3,5 ' ü kullanır `commons-lang3` .

> [!TIP]
> Bir kitaplığı gölgelendirmek için, içeriğini kendi jar 'ınızla yerleştirmekte ve paketini değiştirmiş olmanız gerekir. Bu, kitaplığı paketlemeden ve yeniden paketlemeden kitaplığı kendi jar 'nize yerleştirmekten farklıdır.

## <a name="resolution"></a>Çözüm

Jar 'yi kaldırın veya özelleştirilmiş jar 'yi (AzureLogAppender) yeniden derleyin ve sınıfları yeniden konumlandırmak için [Maven-gölge-Plugin](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) kullanın.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]