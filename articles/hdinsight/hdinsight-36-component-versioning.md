---
title: Apache Hadoop bileşenleri ve sürümleri-Azure HDInsight 3,6
description: Azure HDInsight 3,6 ' de Apache Hadoop bileşenleri ve sürümleri hakkında bilgi edinin.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 32b287c3d7b1974db5a079d1ee84aaafad3faed7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727697"
---
# <a name="hdinsight-36-component-versions"></a>HDInsight 3,6 bileşen sürümleri

Bu makalede, Azure HDInsight 3,6 ' deki Apache Hadoop ortamı bileşenleri ve sürümleri hakkında bilgi edineceksiniz.

## <a name="support-for-hdinsight-36"></a>HDInsight 3,6 için destek

1 Temmuz 2021 ' den itibaren, Microsoft belirli HDI 3,6 küme türleri için Temel destek sunacaktır.
Aşağıdaki tabloda, HDInsight 3,6 küme türleri için destek zaman çerçevesi listelenmektedir.

| Küme Türü                    | Framework sürümü | Standart destek süre sonu       | Temel destek sona erme tarihi | Emeklilik tarihi |
|---------------------------------|-------------------|-----------------------------------|------------------------------|-----------------|
| HDInsight 3,6 Hadoop            | 2.7.3             | 30 Haziran 2021                     | 3 Nisan 2022                | 4 Nisan 2022 |
| HDInsight 3,6 Spark             | 2.3               | 30 Haziran 2021                     | 3 Nisan 2022                | 4 Nisan 2022 |
| HDInsight 3,6 Kafka             | 1.1               | 30 Haziran 2021                     | 3 Nisan 2022                | 4 Nisan 2022 |
| HDInsight 3,6 HBase             | 1.1               | 30 Haziran 2021                     | 3 Nisan 2022                | 4 Nisan 2022 |
| HDInsight 3,6 etkileşimli sorgu | 2.1               | 30 Haziran 2021                     | 3 Nisan 2022                | 4 Nisan 2022 |
| HDInsight 3,6 fırtınası             | 1.1               | 30 Haziran 2021                     | 3 Nisan 2022                | 4 Nisan 2022 |
| HDInsight 3,6 ML Hizmetleri      | 9.3               | -                                 | -                            | 31 Aralık 2020 |
| HDInsight 3,6 Spark             | 2,2               | -                                 | -                            | 30 Haziran 2020 |
| HDInsight 3,6 Spark             | 2.1               | -                                 | -                            | 30 Haziran 2020 |
| HDInsight 3,6 Kafka             | 1.0               | -                                 | -                            | 30 Haziran 2020 |

## <a name="apache-components-available-with-hdinsight-version-36"></a>HDInsight sürüm 3,6 ile kullanılabilen Apache bileşenleri

HDInsight 3,6 ile ilişkili OSS bileşen sürümleri aşağıdaki tabloda listelenmiştir.

| Bileşen              | HDInsight 3,6 (varsayılan)     |
|------------------------|-----------------------------|
| Apache Hadoop ve YARN | 2.7.3                       |
| Apache Tez             | 0.7.0                       |
| Apache Pig             | 0.16.0                      |
| Apache Hive            | (2.1.0 Interactive sorgusunda) |
| Apache Tez Hive2       | 0.8.4                       |
| Apache Ranger          | 0.7.0                       |
| Apache HBase           | 1.1.2                       |
| Apache Sqoop           | 1.4.6                       |
| Apache Oozie           | 4.2.0                       |
| Apache Zookeeper       | 3.4.6                       |
| Apache Storm           | 1.1.0                       |
| Apache Mahout          | 0.9.0 +                      |
| Apache Phoenix         | 4.7.0                       |
| Apache Spark           | 2.3.2.                      |
| Apache Livy            | 0,4.                        |
| Apache Kafka           | 1.1                         |
| Apache ambarı          | 2.6.0                       |
| Apache Zeppelin        | 0.7.3                       |
| Mono                   | 4.2.1                       |

## <a name="next-steps"></a>Sonraki adımlar

- [HDInsight üzerinde Apache Hadoop, Spark ve daha fazlası için küme kurulumu](hdinsight-hadoop-provision-linux-clusters.md)
- [Kurumsal Güvenlik Paketi](./enterprise-security-package.md)
- [Windows BILGISAYARDAN HDInsight üzerinde Apache Hadoop çalışma](hdinsight-hadoop-windows-tools.md)
