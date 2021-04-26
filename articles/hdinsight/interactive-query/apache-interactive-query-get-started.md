---
title: Azure HDInsight 'ta etkileşimli sorgu nedir?
description: Azure HDInsight 'Ta Apache Hive LLAP olarak da bilinen etkileşimli sorguya giriş
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: ec5d63c18e8fb3a353de334ae571c730f9b0d3d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869812"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Azure HDInsight 'Ta etkileşimli sorgu nedir?

Etkileşimli sorgu (Apache Hive LLAP ya da [düşük gecikme süreli analitik işlem](https://cwiki.apache.org/confluence/display/Hive/LLAP)), bir Azure HDInsight [kümesi türüdür](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type). Etkileşimli sorgu, Apache Hive sorguları daha hızlı ve daha fazla etkileşimli hale getiren bellek içi önbelleğe almayı destekler. Müşteriler, Azure depolama & depolanan verileri daha hızlı şekilde Azure Data Lake Storage sorgulamak için etkileşimli sorgu kullanır. Etkileşimli sorgu, geliştiricilerin ve veri bilicilerin en sevdiğiniz BI araçlarını kullanarak büyük verilerle çalışmasını kolaylaştırır. HDInsight etkileşimli sorgusu, büyük verilere kolay bir şekilde erişmek için çeşitli araçları destekler.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Etkileşimli bir sorgu kümesi Apache Hadoop kümesinden farklıdır. Yalnızca Hive hizmetini içerir.

Etkileşimli sorgu kümesindeki Hive hizmetine yalnızca Apache ambarı Hive görünümü, Beeline ve Microsoft Hive açık veritabanı bağlantı sürücüsü (Hive ODBC) aracılığıyla erişebilirsiniz. Hive konsolu, Temptaton, Azure klasik CLı veya Azure PowerShell aracılığıyla erişemezsiniz.

## <a name="create-an-interactive-query-cluster"></a>Etkileşimli sorgu kümesi oluşturma

HDInsight kümesi oluşturma hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-provision-linux-clusters.md). Etkileşimli sorgu kümesi türünü seçin.

> [!IMPORTANT]
> Etkileşimli sorgu kümeleri için en düşük baş düğümüne boyutu Standard_D13_v2. Daha fazla bilgi için bkz. [Azure VM boyutlandırma grafiği](../../cloud-services/cloud-services-sizes-specs.md#dv2-series).

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Etkileşimli sorgudan Apache Hive sorguları yürütme

Hive sorgularını yürütmek için aşağıdaki seçeneklere sahipsiniz:

|Yöntem |Açıklama |
|---|---|
|Microsoft Power BI|Bkz. [Azure HDInsight 'ta Power BI etkileşimli sorgu Apache Hive verileri görselleştirme](./apache-hadoop-connect-hive-power-bi-directquery.md)ve [azure HDInsight 'ta Power BI büyük verileri görselleştirme](../hadoop/apache-hadoop-connect-hive-power-bi.md).|
|Visual Studio|Bkz. [Visual Studio için Data Lake araçlarını kullanarak Azure HDInsight 'A bağlanma ve Apache Hive sorguları çalıştırma](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).|
|Visual Studio Code|Bkz. [Apache Hive, LLAP veya pySpark için Visual Studio Code kullanma](../hdinsight-for-vscode.md).|
|Apache ambarı Hive görünümü|Bkz. [Azure HDInsight 'ta Apache Hadoop ile Apache Hive görünümünü kullanma](../hadoop/apache-hadoop-use-hive-ambari-view.md). HDInsight 4,0 için Hive görünümü kullanılamaz.|
|Apache Beeline|Bkz. [Apache Hive kullanarak HDInsight 'Ta Beeline sahip Apache Hadoop](../hadoop/apache-hadoop-use-hive-beeline.md). Baş düğümden ya da boş bir kenar düğümünden Beeline ' i kullanabilirsiniz. Boş bir kenar düğümünden Beeline kullanmanızı öneririz. Boş bir Edge düğümü kullanarak HDInsight kümesi oluşturma hakkında daha fazla bilgi için bkz. [HDInsight 'ta boş kenar düğümlerini kullanma](../hdinsight-apps-use-edge-node.md).|
|Hive ODBC|Bkz. [Microsoft HIVE ODBC sürücüsü ile Apache Hadoop Excel 'ı bağlama](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).|

Java veritabanı bağlantısı (JDBC) bağlantı dizesini bulmak için:

1. Bir Web tarayıcısından, `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` , `CLUSTERNAME` Kümenizin adı olan ' a gidin.
1. URL 'YI kopyalamak için Pano simgesini seçin:

   :::image type="content" source="./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png" alt-text="HDInsight Hadoop etkileşimli sorgu LLAP JDBC" border="true":::

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight 'Ta etkileşimli sorgu kümeleri oluşturmayı](../hdinsight-hadoop-provision-linux-clusters.md)öğrenin.
* [Azure HDInsight 'ta Power BI büyük verileri görselleştirmeyi](../hadoop/apache-hadoop-connect-hive-power-bi.md)öğrenin.
* [Apache Zeppelin kullanarak Azure HDInsight 'ta Apache Hive sorguları çalıştırma](../interactive-query/hdinsight-connect-hive-zeppelin.md)hakkında bilgi edinin.
