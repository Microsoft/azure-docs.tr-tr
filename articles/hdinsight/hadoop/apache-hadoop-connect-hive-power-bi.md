---
title: Power BI Azure HDInsight ile Apache Hive verileri görselleştirme
description: Azure HDInsight tarafından işlenen Hive verilerini görselleştirmek için Microsoft Power BI nasıl kullanacağınızı öğrenin.
keywords: HDInsight, Hadoop, Hive, etkileşimli sorgu, etkileşimli Hive, LLAP, ODBC
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 9b99e24d00441f0caab4b55a46e5ccc8be9cdd64
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529541"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Azure HDInsight 'ta ODBC kullanarak Microsoft Power BI ile Apache Hive verileri görselleştirme

Microsoft Power BI Desktop 'yi ODBC kullanarak Azure HDInsight 'a bağlamayı ve Apache Hive verileri görselleştirmeyi öğrenin.

> [!IMPORTANT]
> Power BI Desktop içinde Genel ODBC Bağlayıcısı aracılığıyla içeri aktarmak için Hive ODBC sürücüsünden yararlanabilirsiniz. Ancak, Hive sorgu altyapısının etkileşimli olmayan doğası olarak verilen bı iş yükleri için önerilmez. [HDInsight etkileşimli sorgu Bağlayıcısı](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) ve [HDInsight Spark Bağlayıcısı](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) , performansı için daha iyi seçimlerdir.

Bu makalede, verileri Power BI için `hivesampletable` Hive tablosundan yüklersiniz. Hive tablosu bazı cep telefonu kullanım verilerini içerir. Ardından, kullanım verilerini bir dünya eşlemesinde çizdirebilirsiniz:

![Eşleme raporu Power BI HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Bilgiler ayrıca yeni [etkileşimli sorgu](../interactive-query/apache-interactive-query-get-started.md) kümesi türü için de geçerlidir. Doğrudan sorgu kullanarak HDInsight etkileşimli sorgusuna bağlanma için bkz. [Azure HDInsight 'ta doğrudan sorgu kullanarak Microsoft Power BI etkileşimli sorgu Hive verilerini görselleştirme](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleye geçmeden önce aşağıdaki öğelere sahip olmanız gerekir:

* **HDInsight kümesi**. Küme, Hive içeren bir HDInsight kümesi veya yeni yayımlanmış bir etkileşimli sorgu kümesi olabilir. Küme oluşturmak için bkz. [küme oluşturma](apache-hadoop-linux-tutorial-get-started.md#create-cluster).

* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . Bir kopyasını [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=45331)' nden indirebilirsiniz.

## <a name="create-hive-odbc-data-source"></a>Hive ODBC veri kaynağı oluşturma

Bkz. [HIVE ODBC veri kaynağı oluşturma](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>HDInsight 'tan veri yükleme

**Hivesampletable** Hive tablosu tüm HDInsight kümeleriyle birlikte gelir.

1. Power BI Desktop başlatın.

1. Üstteki menüden **Home**  > **veri al**  > **daha fazla...** seçeneğine gidin.

    ![HDInsight Excel Power BI açık verileri](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. **Veri al** iletişim kutusundan soldan **diğer** ' i seçin, sağ taraftaki **ODBC** ' i seçin ve ardından alt kısımdaki **Bağlan** ' ı seçin.

1. **ODBC** iletişim kutusundan, açılan listeden son bölümde oluşturduğunuz veri kaynağı adını seçin. Sonra **Tamam**’ı seçin.

1. İlk kullanım için bir **ODBC sürücüsü** iletişim kutusu açılır. Sol menüden **varsayılan veya özel** ' i seçin. Ardından, **Gezgin**'i açmak için **Bağlan** ' ı seçin.

1. **Gezgin** Iletişim kutusunda **ODBC > HIVE >** ' ı genişletin, **hivesampletable**' ı seçin ve ardından **Yükle**' yi seçin.

## <a name="visualize-data"></a>Verileri görselleştirme

Son yordamdan devam edin.

1. Görsel Öğeler bölmesinde **eşle**' yi seçin, bir dünya simgesi.

    ![HDInsight Power BI raporu özelleştirir](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. **Alanlar** bölmesinde, **ülke** ve **devicemake**' ı seçin. Haritada çizili verileri görebilirsiniz.

1. Haritayı genişletin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Power BI kullanarak HDInsight 'tan verileri görselleştirmeyi öğrendiniz.  Daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure HDInsight 'ta Apache Hive sorguları çalıştırmak Için Apache Zeppelin kullanın](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Excel 'i Microsoft Hive ODBC sürücüsü Ile HDInsight 'A bağlayın](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Power Query kullanarak Excel 'i Apache Hadoop bağlama](apache-hadoop-connect-excel-power-query.md).
* [Azure HDInsight 'A bağlanın ve Visual Studio Data Lake araçları 'nı kullanarak Apache Hive sorguları çalıştırın](apache-hadoop-visual-studio-tools-get-started.md).
* [Visual Studio Code Için Azure HDInsight aracını kullanın](../hdinsight-for-vscode.md).
* [HDInsight 'A veri yükleme](./../hdinsight-upload-data.md).
