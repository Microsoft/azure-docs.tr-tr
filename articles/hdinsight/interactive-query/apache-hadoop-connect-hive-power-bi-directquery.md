---
title: Azure HDInsight 'ta Power BI etkileşimli sorgu Hive verilerine bakın
description: Azure HDInsight 'tan etkileşimli sorgu Hive verilerini görselleştirmek için Microsoft Power BI kullanma
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: ea63b0784c0f8680e544179dfcd927f59336b3ec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467143"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>HDInsight 'ta doğrudan sorgu kullanarak Microsoft Power BI ile etkileşimli sorgu Apache Hive verilerini görselleştirme

Bu makalede, Microsoft Power BI 'yi Azure HDInsight etkileşimli sorgu kümelerine bağlama ve doğrudan sorgu kullanarak Apache Hive verileri görselleştirme açıklanır. Belirtilen örnek, verileri bir `hivesampletable` Hive tablosundan Power BI için yükler. `hivesampletable` Hive tablosu, bazı cep telefonu kullanım verilerini içerir. Ardından, kullanım verilerini bir dünya eşlemesinde çizdirebilirsiniz:

![Eşleme raporu Power BI HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Power BI Desktop Genel ODBC Bağlayıcısı aracılığıyla içeri aktarmak için [Apache HIVE ODBC sürücüsünden](../hadoop/apache-hadoop-connect-hive-power-bi.md) yararlanabilirsiniz. Ancak, Hive sorgu altyapısının etkileşimli olmayan doğası olarak verilen bı iş yükleri için önerilmez. [HDInsight etkileşimli sorgu Bağlayıcısı](./apache-hadoop-connect-hive-power-bi-directquery.md) ve [HDInsight Apache Spark Bağlayıcısı](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) , performansı için daha iyi seçimlerdir.

## <a name="prerequisites"></a>Önkoşullar
Bu makaleye geçmeden önce aşağıdaki öğelere sahip olmanız gerekir:

* **HDInsight kümesi**. Küme, Apache Hive veya yeni bir etkileşimli sorgu kümesi olan bir HDInsight kümesi olabilir. Küme oluşturmak için bkz. [küme oluşturma](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . Bir kopyasını [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=45331)' nden indirebilirsiniz.

## <a name="load-data-from-hdinsight"></a>HDInsight 'tan veri yükleme

`hivesampletable` Hive tablosu tüm HDInsight kümeleriyle birlikte gelir.

1. Power BI Desktop başlatın.

2. Menü çubuğundan **Home** > **veri al** > **daha fazla...** seçeneğine gidin.

    ![HDInsight Power BI verileri daha fazla al](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. **Veri al** penceresinde, arama kutusuna **HDInsight** girin.  

4. Arama sonuçlarından **HDInsight etkileşimli sorgu**' yı seçin ve ardından **Bağlan**' ı seçin.  **HDInsight etkileşimli sorgusunu**görmüyorsanız Power BI Desktop en son sürüme güncelleştirmeniz gerekir.

5. **Üçüncü taraf bir hizmet** Iletişim kutusuna bağlanmayı kapatmak için **devam** ' ı seçin.

6. **HDInsight etkileşimli sorgu** penceresinde aşağıdaki bilgileri girip **Tamam**' ı seçin:

    |Özellik | Değer |
    |---|---|
    |Sunucu |Küme adını girin, örneğin *myiqcluster.azurehdinsight.net*.|
    |Database |Bu makale için **varsayılan** girin.|
    |Veri bağlantısı modu |Bu makale için **DirectQuery** 'yi seçin.|

    ![HDInsight etkileşimli sorgu Power BI DirectQuery Connect](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. HTTP kimlik bilgilerini girin ve sonra **Bağlan**' ı seçin. Varsayılan Kullanıcı adı **admin**' dir.

8. Sol bölmedeki **Gezgin** penceresinden **hivesampletale**' yı seçin.

9. Ana pencereden **Yükle** ' yi seçin.

    ![HDInsight etkileşimli sorgu Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Haritada verileri görselleştirme

Son yordamdan devam edin.

1. Görsel öğeler **bölmesinden, dünya simgesini seçin.** Daha sonra ana pencerede bir genel harita görüntülenir.

    ![HDInsight Power BI raporu özelleştirir](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. Alanlar bölmesinde, **ülke** ve **devicemake**' ı seçin. Veri noktalarıyla bir dünya haritası, birkaç dakika sonra ana pencerede görüntülenir.

3. Haritayı genişletin.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Microsoft Power BI kullanarak HDInsight 'tan verileri görselleştirmeyi öğrendiniz.  Veri görselleştirme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure HDInsight 'TA ODBC kullanarak Microsoft Power BI ile Apache Hive verilerini görselleştirin](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Azure HDInsight 'ta Apache Hive sorguları çalıştırmak Için Apache Zeppelin kullanın](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Excel 'i Microsoft Hive ODBC sürücüsü Ile HDInsight 'A bağlayın](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Power Query kullanarak Excel 'i Apache Hadoop bağlama](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Azure HDInsight 'A bağlanın ve Visual Studio Data Lake araçları 'nı kullanarak Apache Hive sorguları çalıştırın](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Visual Studio Code Için Azure HDInsight aracını kullanın](../hdinsight-for-vscode.md).
* [HDInsight 'A veri yükleme](./../hdinsight-upload-data.md).
