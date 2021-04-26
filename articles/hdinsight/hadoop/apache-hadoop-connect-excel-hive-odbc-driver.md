---
title: Açık veritabanı bağlantısı (ODBC) sürücüsü ile Excel & Apache Hadoop-Azure HDInsight
description: Microsoft Excel 'de HDInsight kümelerindeki verileri sorgulamak için Excel için Microsoft Hive ODBC sürücüsünü ayarlamayı ve kullanmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: c7125e25c9716a9934c97f9f8f85666d264d5f89
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866956"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC sürücüsüyle Excel'i Azure HDInsight'ta Apache Hadoop'a bağlama

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft 'un büyük veri çözümü, Microsoft Business Intelligence (BI) bileşenlerini HDInsight 'ta dağıtılan Apache Hadoop kümeleriyle tümleştirir. Excel 'i Hadoop kümesinin Hive veri ambarına bağlama özelliği bir örnektir. Microsoft Hive açık veritabanı bağlantısı (ODBC) sürücüsünü kullanarak bağlanın.

Excel 'deki bir HDInsight kümesiyle ilişkili verileri Excel için Microsoft Power Query eklentisi ile bağlayabilirsiniz. Daha fazla bilgi için bkz. [Power Query Ile Excel 'ı HDInsight 'A bağlama](./apache-hadoop-connect-excel-power-query.md).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleye başlamadan önce aşağıdaki öğelere sahip olmanız gerekir:

* Hadoop kümesi An HDInsight. Bir tane oluşturmak için bkz. [Azure HDInsight kullanmaya başlama](apache-hadoop-linux-tutorial-get-started.md).
* Office 2010 Professional Plus veya üzeri ya da Excel 2010 veya üzeri bir iş istasyonu.

## <a name="install-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC sürücüsünü yükler

[Microsoft Hive ODBC sürücüsü](https://www.microsoft.com/download/details.aspx?id=40886)indirin ve yükleyin. ODBC sürücüsünü kullanacağınız uygulamanın sürümüyle eşleşen sürümü seçin.  Bu makalede, sürücü Office Excel için kullanılır.

## <a name="create-apache-hive-odbc-data-source"></a>Apache Hive ODBC veri kaynağı oluşturma

Aşağıdaki adımlarda, Hive ODBC veri kaynağı oluşturma yöntemi gösterilmektedir.

1. Windows 'da, Windows **Yönetim araçları > ODBC veri kaynakları (32 bit)/(64 bit) > Başlat**' a gidin.  Bu eylem **ODBC veri kaynağı Yöneticisi** penceresini açar.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png" alt-text="OBDC veri kaynağı Yöneticisi" border="true":::

1. **Yeni veri kaynağı oluştur** penceresini açmak IÇIN **Kullanıcı DSN** sekmesinden **Ekle** ' yi seçin.

1. **Microsoft Hive ODBC sürücüsü**' yi seçin ve ardından **son** ' u seçerek **Microsoft Hive ODBC sürücüsü DSN kurulum** penceresini açın.

1. Aşağıdaki değerleri yazın veya seçin:

   | Özellik | Açıklama |
   | --- | --- |
   |  Data Source Name |Veri kaynağınız için bir ad verin |
   |  Ana bilgisayar (ler) |`HDInsightClusterName.azurehdinsight.net` yazın. Örneğin, `myHDICluster.azurehdinsight.net`. Note: `HDInsightClusterName-int.azurehdinsight.net` istemci VM 'sinin aynı sanal ağa eşlenmiş olması için desteklenir. |
   |  Bağlantı noktası |**443** yazın. (Önceden 563 olan bu bağlantı noktası 443 olarak değiştirilmiştir.) |
   |  Veritabanı |**Varsayılanı** kullanın. |
   |  Mechanism |**Windows Azure HDInsight hizmetini** seçin |
   |  User Name |HDInsight kümesi HTTP Kullanıcı Kullanıcı adı girin. Varsayılan kullanıcı adı **admin** şeklindedir. |
   |  Parola |HDInsight kümesi Kullanıcı parolasını girin. **Parolayı Kaydet (şifreli)** onay kutusunu seçin.|

1. İsteğe bağlı: **Gelişmiş seçenekleri seçin...**  

   | Parametre | Açıklama |
   | --- | --- |
   |  Yerel sorgu kullan |Seçildiğinde, ODBC sürücüsü TSQL HiveQL 'e dönüştürmeyi denemez. Yalnızca, saf HiveQL deyimlerini gönderdikten sonra %100 olduğundan bunu kullanacaksınız. SQL Server veya Azure SQL veritabanı 'na bağlanırken, işareti kaldırılmış olarak bırakmalısınız. |
   |  Blok başına getirilen satırlar |Çok sayıda kayıt getirilirken, en iyi performansı sağlamak için bu parametreyi ayarlama gerekebilir. |
   |  Varsayılan dize sütunu uzunluğu, Ikili sütun uzunluğu, ondalık sütun ölçeği |Veri türü uzunlukları ve ön ekleri verilerin nasıl döndürüldüğünü etkileyebilir. Duyarlık ve kesme kaybı nedeniyle yanlış bilgilerin döndürülmesine neden olur. |

    :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png" alt-text="Gelişmiş DSN yapılandırma seçenekleri" border="true":::

1. Veri kaynağını test etmek için **Test** ' i seçin. Veri kaynağı doğru şekilde yapılandırıldığında, test sonucu başarılı ' ı gösterir **!**

1. Test penceresini kapatmak için **Tamam ' ı** seçin.  

1. **MICROSOFT HIVE ODBC sürücüsü DSN kurulum** penceresini kapatmak için **Tamam ' ı** seçin.  

1. **ODBC veri kaynağı Yöneticisi** penceresini kapatmak için **Tamam ' ı** seçin.  

## <a name="import-data-into-excel-from-hdinsight"></a>HDInsight’tan Excel’e veri aktarma

Aşağıdaki adımlarda, bir Hive tablosundan bir Excel çalışma kitabına veri aktarma yolu, önceki bölümde oluşturduğunuz ODBC veri kaynağını kullanarak açıklanır.

1. Excel’de yeni veya mevcut bir çalışma kitabını açın.

2. ODBC 'den başlatmak için **veri** sekmesinden   >    >  **ODBC 'den**  diğer kaynaklardan veri al ' a gidin.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png" alt-text="Excel veri bağlantısı Sihirbazı 'nı açma" border="true":::

3. Aşağı açılan listeden, son bölümde oluşturduğunuz veri kaynağı adını seçin ve ardından **Tamam**' ı seçin.

4. İlk kullanım için bir **ODBC sürücüsü** iletişim kutusu açılır. Sol menüden **Windows** ' u seçin. Ardından, **Gezgin** penceresini açmak için **Bağlan** ' ı seçin.

5. **Gezgin**'den **HIVE**  >  **varsayılan**  >  **hivesampletable**' a gidin ve ardından **Yükle**' yi seçin. Verilerin Excel 'e aktarılması birkaç dakika sürer.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png" alt-text="HDInsight Excel Hive ODBC Gezgini" border="true":::

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Microsoft Hive ODBC sürücüsünü kullanarak HDInsight hizmetinden Excel 'e veri almayı öğrendiniz. Benzer şekilde, HDInsight hizmetinden SQL veritabanı 'na veri alabilirsiniz. Verileri bir HDInsight hizmetine yüklemek de mümkündür. Daha fazla bilgi edinmek için şu makalelere bakın:

* [Apache Hive verilerini Azure HDInsight 'Ta Microsoft Power BI Ile görselleştirin](apache-hadoop-connect-hive-power-bi.md).
* [Azure HDInsight 'ta Power BI etkileşimli sorgu Hive verilerini görselleştirin](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Power Query kullanarak Excel 'i Apache Hadoop bağlama](apache-hadoop-connect-excel-power-query.md).
* [Azure HDInsight 'A bağlanın ve Visual Studio Data Lake araçları 'nı kullanarak Apache Hive sorguları çalıştırın](apache-hadoop-visual-studio-tools-get-started.md).