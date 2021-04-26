---
title: Power BI Azure HDInsight ile Apache Hive verileri görselleştirme
description: Azure HDInsight tarafından işlenen Hive verilerini görselleştirmek için Microsoft Power BI nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: bb448a4befb15618485b2b5951222761180a1f22
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866752"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Azure HDInsight’ta ODBC kullanarak Microsoft Power BI ile Apache Hive verilerini görselleştirme

Microsoft Power BI Desktop 'yi ODBC kullanarak Azure HDInsight 'a bağlamayı ve Apache Hive verileri görselleştirmeyi öğrenin.

> [!IMPORTANT]
> Power BI Desktop içinde Genel ODBC Bağlayıcısı aracılığıyla içeri aktarmak için Hive ODBC sürücüsünden yararlanabilirsiniz. Ancak, Hive sorgu altyapısının etkileşimli olmayan doğası olarak verilen bı iş yükleri için önerilmez. [HDInsight etkileşimli sorgu Bağlayıcısı](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) ve [HDInsight Spark Bağlayıcısı](/power-bi/spark-on-hdinsight-with-direct-connect) , performansı için daha iyi seçimlerdir.

Bu makalede, verileri `hivesampletable` Power BI için bir Hive tablosundan yüklersiniz. Hive tablosu bazı cep telefonu kullanım verilerini içerir. Ardından, kullanım verilerini bir dünya eşlemesinde çizdirebilirsiniz:

:::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png" alt-text="Eşleme raporu Power BI HDInsight" border="true":::

Bilgiler ayrıca yeni [etkileşimli sorgu](../interactive-query/apache-interactive-query-get-started.md) kümesi türü için de geçerlidir. Doğrudan sorgu kullanarak HDInsight etkileşimli sorgusuna bağlanma için bkz. [Azure HDInsight 'ta doğrudan sorgu kullanarak Microsoft Power BI etkileşimli sorgu Hive verilerini görselleştirme](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleye geçmeden önce aşağıdaki öğelere sahip olmanız gerekir:

* HDInsight kümesi. Küme, Hive içeren bir HDInsight kümesi veya yeni yayımlanmış bir etkileşimli sorgu kümesi olabilir. Küme oluşturmak için bkz. [küme oluşturma](apache-hadoop-linux-tutorial-get-started.md).

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/). Bir kopyasını [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=45331)' nden indirebilirsiniz.

## <a name="create-hive-odbc-data-source"></a>Hive ODBC veri kaynağı oluşturma

Bkz. [HIVE ODBC veri kaynağı oluşturma](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>HDInsight 'tan veri yükleme

**Hivesampletable** Hive tablosu tüm HDInsight kümeleriyle birlikte gelir.

1. Power BI Desktop’ı başlatın.

1. Üstteki menüden, **ana**  >  **veri al**  >  **daha fazla...** seçeneğine gidin.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png" alt-text="HDInsight Excel Power BI açık verileri" border="true":::

1. **Veri al** iletişim kutusundan soldan **diğer** ' i seçin, sağ taraftaki **ODBC** ' i seçin ve ardından alt kısımdaki **Bağlan** ' ı seçin.

1. **ODBC** iletişim kutusundan, açılan listeden son bölümde oluşturduğunuz veri kaynağı adını seçin. Ardından **Tamam**’ı seçin.

1. İlk kullanım için bir **ODBC sürücüsü** iletişim kutusu açılır. Sol menüden **varsayılan veya özel** ' i seçin. Ardından, **Gezgin**'i açmak için **Bağlan** ' ı seçin.

1. **Gezgin** Iletişim kutusunda **ODBC > HIVE >**' ı genişletin, **hivesampletable**' ı seçin ve ardından **Yükle**' yi seçin.

## <a name="visualize-data"></a>Verileri görselleştirme

Son yordamdan devam edin.

1. Görsel Öğeler bölmesinde **eşle**' yi seçin, bir dünya simgesi.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png" alt-text="HDInsight Power BI raporu özelleştirir" border="true":::

1. **Alanlar** bölmesinde, **ülke** ve **devicemake**' ı seçin. Haritada çizili verileri görebilirsiniz.

1. Haritayı genişletin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Power BI kullanarak HDInsight 'tan verileri görselleştirmeyi öğrendiniz.  Daha fazla bilgi için aşağıdaki makalelere bakın:

* [Excel 'i Microsoft Hive ODBC sürücüsü Ile HDInsight 'A bağlayın](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Power Query kullanarak Excel 'i Apache Hadoop bağlama](apache-hadoop-connect-excel-power-query.md).
* [Doğrudan sorgu kullanarak Microsoft Power BI ile etkileşimli sorgu Apache Hive verilerini görselleştirin](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)