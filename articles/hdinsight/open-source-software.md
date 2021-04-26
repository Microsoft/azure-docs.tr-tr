---
title: Azure HDInsight 'ta açık kaynaklı yazılım desteği
description: Microsoft Azure, açık kaynaklı teknolojiler için genel bir destek düzeyi sağlar.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: fec4cff974031982c782c9265a7d3186d6bb0233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942563"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Azure HDInsight 'ta açık kaynaklı yazılım desteği

Microsoft Azure HDInsight hizmeti Apache Hadoop etrafında oluşturulan açık kaynaklı teknolojilerin bir ortamını kullanır. Microsoft Azure, açık kaynaklı teknolojiler için genel bir destek düzeyi sağlar. Daha fazla bilgi için [Azure desteği SSS](https://azure.microsoft.com/support/faq/)konusunun **Destek kapsamı** bölümüne bakın. HDInsight hizmeti, yerleşik bileşenler için ek bir destek düzeyi sağlar.

## <a name="components"></a>Bileşenler

HDInsight hizmetinde iki tür açık kaynaklı bileşen mevcuttur:

### <a name="built-in-components"></a>Yerleşik bileşenler

Bu bileşenler HDInsight kümelerine önceden yüklenir ve kümenin temel işlevlerini sağlar. Aşağıdaki bileşenler bu kategoriye aittir:

* [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Kaynak Yöneticisi.
* Hive sorgu dili [Hiveql](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
* [Apache Mahout](https://mahout.apache.org/).

[HDInsight ile kullanılabilen Apache Hadoop bileşenleri ve sürümleri olan](hdinsight-component-versioning.md) küme bileşenlerinin tam listesi mevcuttur

### <a name="custom-components"></a>Özel bileşenler

Kümenin bir kullanıcısı olarak, kuruluşunuzda bulunan veya sizin tarafınızdan oluşturulan herhangi bir bileşeni iş yükünüze yükleyebilir veya kullanabilirsiniz.

> [!WARNING]  
> HDInsight kümesiyle birlikte sunulan bileşenler tam olarak desteklenmektedir. Microsoft Desteği, bu bileşenlerle ilgili sorunları yalıtmaya ve çözmeye yardımcı olur.
>
> Özel bileşenler, sorunu gidermeye yardımcı olmak için ticari açıdan makul destek alır. Microsoft Desteği sorunu çözebiliyor olabilir. Ya da bu teknoloji için derin uzmanlığın bulunduğu açık kaynaklı teknolojiler için kullanılabilir kanalları da sunmanız istenebilir. Birçok topluluk sitesi kullanılabilir. HDInsight ve [Stack Overflow](https://stackoverflow.com) [için Microsoft Q&soru sayfası](/answers/topics/azure-hdinsight.html) örnekleri verilmiştir.
>
> Apache projelerinde Ayrıca, [Apache Web sitesinde](https://apache.org)proje siteleri de vardır. [Hadoop](https://hadoop.apache.org/)bir örnektir.

## <a name="component-usage"></a>Bileşen kullanımı

HDInsight hizmeti özel bileşenleri kullanmak için çeşitli yollar sağlar. Aynı destek düzeyi, bir bileşenin küme üzerinde nasıl kullanıldığını veya yüklendiğine bakılmaksızın geçerlidir. Aşağıdaki tabloda, HDInsight kümelerinde özel bileşenlerin kullanıldığı en yaygın yollar açıklanmaktadır:

|Kullanım |Description |
|---|---|
|İş gönderme|Hadoop veya özel bileşenleri çalıştıran veya kullanan diğer iş türleri de kümeye gönderilebilir.|
|Küme özelleştirmesi|Küme oluşturma sırasında, küme düğümlerinde yüklü ek ayarları ve özel bileşenleri belirtebilirsiniz.|
|Örnekler|Popüler özel bileşenlerde, Microsoft ve diğerleri bu bileşenlerin HDInsight kümelerinde nasıl kullanılabileceği hakkında örnekler sağlayabilir. Bu örnekler destek olmadan sağlanır.|

## <a name="next-steps"></a>Sonraki adımlar

* [Betik eylemlerini kullanarak Azure HDInsight kümelerini özelleştirme](./hdinsight-hadoop-customize-cluster-linux.md)
* [HDInsight için betik eylem betikleri geliştirme](hdinsight-hadoop-script-actions-linux.md)
* [Betik Eylemi kullanarak Azure HDInsight üzerinde Python ortamını güvenli bir şekilde yönetin](./spark/apache-spark-python-package-installation.md)