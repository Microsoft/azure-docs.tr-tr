---
title: Apache Hadoop Power Query-Azure HDInsight ile Excel 'e bağlanma
description: İş zekası bileşenlerinden nasıl yararlanacağınızı öğrenin ve HDInsight 'ta Hadoop 'ta depolanan verilere erişmek için Excel için Power Query kullanın.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: e64905cdfeac8d507df1c3dd92c245cb910a79b2
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033566"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Power Query kullanarak Excel 'i Apache Hadoop bağlama
Microsoft büyük veri çözümünün bir anahtar özelliği, Microsoft Business Intelligence (BI) bileşenlerinin Azure HDInsight 'ta Apache Hadoop kümeleriyle Tümleştirmesidir. Birincil örnek, Excel için Microsoft Power Query eklentisini kullanarak, Excel 'i Hadoop kümeniz ile ilişkili verileri içeren Azure depolama hesabına bağlama olanağıdır. Bu makalede, HDInsight ile yönetilen bir Hadoop kümesiyle ilişkili verileri sorgulamak için Power Query ayarlama ve kullanma adımları gösterilmektedir.

### <a name="prerequisites"></a>Önkoşullar
Bu makaleye başlamadan önce aşağıdaki öğelere sahip olmanız gerekir:

* **An HDInsight küme**. Bir tane yapılandırmak için bkz. [Azure HDInsight kullanmaya başlama](./apache-hadoop-linux-tutorial-get-started.md).
* Windows 7, Windows Server 2008 R2 veya sonraki bir işletim sistemini çalıştıran **bir iş istasyonu** .
* **Office 2016, office 2013 Professional Plus, office 365 ProPlus, Excel 2013 tek başına veya office 2010 Professional Plus**.

## <a name="install-power-query"></a>Power Query yüklensin
Power Query, çıktı olan veya HDInsight kümesi üzerinde çalışan bir Hadoop işi tarafından oluşturulmuş olan verileri içeri aktarabilir.

Excel 2016 ' de, Power Query & dönüşümü Al bölümünde veri şeridinde tümleştirilmiştir. Eski Excel sürümleri için [Microsoft Indirme merkezi](https://go.microsoft.com/fwlink/?LinkID=286689) ' nden Excel için Microsoft Power Query indirin ve yükleyin.

## <a name="import-hdinsight-data-into-excel"></a>HDInsight verilerini Excel 'e aktar
Excel için Power Query eklentisi, HDInsight kümenizdeki verileri Excel 'e aktarmayı kolaylaştırır; burada PowerPivot ve Power Map gibi BI araçları verileri incelemek, analiz etmek ve sunmak için kullanılabilir.

**HDInsight kümesinden verileri içeri aktarmak için**

1. Excel 'i açın.
2. Yeni boş bir çalışma kitabı oluşturun.
3. Excel sürümüne göre aşağıdaki adımları gerçekleştirin:

   - Excel 2016

     - **Veri** menüsünü tıklatın, veri al **& Dönüştür veri** şeridinde **verileri al** ' a tıklayın, **Azure 'dan**ve ardından **Azure HDInsight (bir)** ' e tıklayın.

       ![HDI. PowerQuery. SelectHdiSource. 2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   - Excel 2013/2010

     - **Power Query** menüsüne tıklayın, **Azure 'dan**ve ardından **Microsoft Azure HDInsight**' e tıklayın.
   
       ![HDI. PowerQuery. SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)
       
       **Not:** **Power Query** menüsünü görmüyorsanız, **Dosya** > **seçenekleri** > **eklentileri**' ne gidin ve sayfanın alt kısmındaki aşağı açılan **Yönet** kutusundan **com eklentileri** ' ni seçin. **Git...** düğmesini seçin ve Excel eklentisi için Power Query kutusunun işaretli olduğunu doğrulayın.
       
       **Not:** Power Query Ayrıca, **diğer kaynaklardan**tıklatarak, bu sunucudan verileri içeri aktarmanıza izin verir.
4. **Hesap adı**için, kümenizle Ilişkili Azure Blob depolama hesabının adını girin ve ardından **Tamam**' a tıklayın. Bu hesap, varsayılan depolama hesabı veya bağlı bir depolama hesabı olabilir.  Biçim *https://&lt;storageAccountName >. blob. Core. Windows. net/* olur.
5. **Hesap anahtarı**Için, BLOB depolama hesabının anahtarını girin ve **Kaydet**' e tıklayın. (Bu mağazaya yalnızca ilk kez eriştiğinizde hesap bilgilerini girmeniz gerekir.)
6. Sorgu Düzenleyicisi 'nin solundaki **Gezgin** bölmesinde, BLOB depolama kapsayıcısı adına çift tıklayın. Varsayılan olarak, kapsayıcı adı küme adı ile aynı addır.
7. Bulun **HiveSampleData.txt** içinde **adı** sütun (klasör yolu **../hive/warehouse/hivesampletable/ambar**) ve ardından **ikili** sol tarafındaki HiveSampleData.txt. HiveSampleData. txt tüm kümeyle birlikte gelir. İsteğe bağlı olarak, kendi dosyanızı kullanabilirsiniz.

    ![HDI Excel Power Query verileri içeri aktarma](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

8. İsterseniz, sütun adlarını yeniden adlandırabilirsiniz. Hazırsanız **& yükle**' ye tıklayın.  Veriler çalışma kitabınıza yüklendi:

    ![HDI Excel Power Query içeri aktarılan tablosu](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, HDInsight 'tan Excel 'e veri almak için Power Query kullanmayı öğrendiniz. Benzer şekilde, HDInsight 'tan Azure SQL veritabanı 'na veri alabilirsiniz. HDInsight 'a veri yüklemek de mümkündür. Daha fazla bilgi için aşağıdaki makalelere bakın:

* [Apache Hive verilerini Azure HDInsight 'Ta Microsoft Power BI Ile görselleştirin](apache-hadoop-connect-hive-power-bi.md).
* [Azure HDInsight 'ta Power BI etkileşimli sorgu Hive verilerini görselleştirin](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Azure HDInsight 'ta Apache Hive sorguları çalıştırmak Için Apache Zeppelin kullanın](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Excel 'i Microsoft Hive ODBC sürücüsü Ile HDInsight 'A bağlayın](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Azure HDInsight 'A bağlanın ve Visual Studio Data Lake araçları 'nı kullanarak Apache Hive sorguları çalıştırın](apache-hadoop-visual-studio-tools-get-started.md).
* [Visual Studio Code Için Azure HDInsight aracını kullanın](../hdinsight-for-vscode.md).
* [HDInsight 'a veri yükleme](./../hdinsight-upload-data.md).
