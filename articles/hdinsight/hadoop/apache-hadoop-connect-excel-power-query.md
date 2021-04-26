---
title: Apache Hadoop Power Query-Azure HDInsight ile Excel 'e bağlanma
description: İş zekası bileşenlerinden nasıl yararlanacağınızı öğrenin ve HDInsight 'ta Hadoop 'ta depolanan verilere erişmek için Excel için Power Query kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 13862e642c6a91fe6f3c635df2efde91672ecbad
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866820"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Power Query kullanarak Excel 'i Apache Hadoop bağlama

Microsoft büyük veri çözümünün bir anahtar özelliği, Microsoft Business Intelligence (BI) bileşenlerinin Azure HDInsight 'ta Apache Hadoop kümeleriyle Tümleştirmesidir. Birincil örnek, Excel için Microsoft Power Query eklentisini kullanarak, Excel 'i Hadoop kümeniz ile ilişkili verileri içeren Azure depolama hesabına bağlama olanağıdır. Bu makalede, HDInsight ile yönetilen bir Hadoop kümesiyle ilişkili verileri sorgulamak için Power Query ayarlama ve kullanma adımları gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Hadoop kümesi. Bkz. [Linux 'Ta HDInsight kullanmaya başlama](./apache-hadoop-linux-tutorial-get-started.md).
* Windows 10, 7, Windows Server 2008 R2 veya daha sonraki bir işletim sistemini çalıştıran bir iş istasyonu.
* Enterprise, Office 2016, Office 2013 Professional Plus, Excel 2013 tek başına veya Office 2010 Professional Plus için Microsoft 365 uygulamalar.

## <a name="install-microsoft-power-query"></a>Microsoft Power Query 'yi yükler

Power Query, çıktı olan veya HDInsight kümesi üzerinde çalışan bir Hadoop işi tarafından oluşturulmuş olan verileri içeri aktarabilir.

Excel 2016 ' de, Power Query & dönüşümü Al bölümünde veri şeridinde tümleştirilmiştir. Eski Excel sürümleri için [Microsoft Indirme merkezi](https://go.microsoft.com/fwlink/?LinkID=286689) ' nden Excel için Microsoft Power Query indirin ve yükleyin.

## <a name="import-hdinsight-data-into-excel"></a>HDInsight verilerini Excel 'e aktar

Excel için Power Query eklentisi, HDInsight kümenizdeki verileri Excel 'e aktarmayı kolaylaştırır; burada PowerPivot ve Power Map gibi BI araçları verileri incelemek, analiz etmek ve sunmak için kullanılabilir.

1. Excel 'i başlatın.

1. Yeni boş bir çalışma kitabı oluşturun.

1. Excel sürümüne göre aşağıdaki adımları gerçekleştirin:

   * Excel 2016

     * Azure   >    >    >  **HDInsight 'tan Azure HDInsight 'tan** veri almak > verileri seçin.

       :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png" alt-text="HDI. PowerQuery. SelectHdiSource. 2016" border="true":::

   * Excel 2013/2010

     * Microsoft Azure HDInsight   >  **Azure 'dan** Power Query seçin  >  .

       :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png" alt-text="HDI. PowerQuery. SelectHdiSource" border="true":::

       **Note:** **Power Query** menüsünü görmüyorsanız, **Dosya**  >  **seçenekleri**  >  **eklentileri**' ne gidin ve sayfanın alt kısmındaki aşağı açılan **Yönet** kutusundan **com eklentileri** ' ni seçin. **Git...** düğmesini seçin ve Excel eklentisi için Power Query kutusunun işaretli olduğunu doğrulayın.

       **Note:** Power Query Ayrıca **diğer kaynaklardan** seçerek de verileri bir sunucudan içeri aktarmanıza olanak tanır.

1. **Azure HDInsight (bir)** iletişim kutusunda, **Hesap adı veya URL** metin kutusuna kümenizle ilişkili Azure Blob depolama hesabının adını girin. Ardından **Tamam**’ı seçin. Bu hesap, varsayılan depolama hesabı veya bağlı bir depolama hesabı olabilir.  Biçim `https://StorageAccountName.blob.core.windows.net/` .

1. **Hesap anahtarı** Için, BLOB depolama hesabının anahtarını girin ve sonra **Bağlan**' ı seçin. (Bu mağazaya yalnızca ilk kez eriştiğinizde hesap bilgilerini girmeniz gerekir.)

1. Sorgu Düzenleyicisi 'nin solundaki **Gezgin** bölmesinde, kümeinizle ilişkili BLOB depolama kapsayıcısı adına çift tıklayın. Varsayılan olarak, kapsayıcı adı küme adı ile aynı addır.

1. **Ad** sütununda **HiveSampleData.txt** bulun (klasör yolu **... /Hive/Warehouse/hivesampletable/**) ve ardından HiveSampleData.txt sol tarafında **ikili** ' ı seçin. HiveSampleData.txt tüm kümeyle birlikte gelir. İsteğe bağlı olarak, kendi dosyanızı kullanabilirsiniz.

    :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png" alt-text="HDI Excel Power Query verileri içeri aktarma" border="true":::

1. İsterseniz, sütun adlarını yeniden adlandırabilirsiniz. Hazırsanız **& yükle**' yi seçin.  Veriler çalışma kitabınıza yüklendi:

    :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png" alt-text="HDI Excel Power Query içeri aktarılan tablosu" border="true":::

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, HDInsight 'tan Excel 'e veri almak için Power Query kullanmayı öğrendiniz. Benzer şekilde, HDInsight 'tan Azure SQL veritabanı 'na veri alabilirsiniz. HDInsight 'a veri yüklemek de mümkündür. Daha fazla bilgi için aşağıdaki makalelere bakın:

* [Apache Hive verilerini Azure HDInsight 'Ta Microsoft Power BI Ile görselleştirin](apache-hadoop-connect-hive-power-bi.md).
* [Azure HDInsight 'ta Power BI etkileşimli sorgu Hive verilerini görselleştirin](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Azure HDInsight 'ta Apache Hive sorguları çalıştırmak Için Apache Zeppelin kullanın](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Excel 'i Microsoft Hive ODBC sürücüsü Ile HDInsight 'A bağlayın](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Azure HDInsight 'A bağlanın ve Visual Studio Data Lake araçları 'nı kullanarak Apache Hive sorguları çalıştırın](apache-hadoop-visual-studio-tools-get-started.md).
* [Visual Studio Code Için Azure HDInsight aracını kullanın](../hdinsight-for-vscode.md).
* [HDInsight 'a veri yükleme](./../hdinsight-upload-data.md).
