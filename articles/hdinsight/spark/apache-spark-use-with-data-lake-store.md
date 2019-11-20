---
title: HDInsight ile Azure Data Lake Storage 1. analiz Apache Spark
description: Azure Data Lake Storage 1. depolanan verileri analiz etmek için Apache Spark işleri çalıştırın
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: f7a6ab954aff1bcc2e3dae3fc035db4b136ccbbe
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818175"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>Data Lake Storage 1. verileri çözümlemek için HDInsight Spark kümesini kullanma

Bu makalede, bir Data Lake Storage hesabındaki verileri okuyan bir işi çalıştırmak için HDInsight Spark kümeleri ile kullanılabilir [Jupyter Notebook](https://jupyter.org/) kullanırsınız.

## <a name="prerequisites"></a>Ön koşullar

* Azure Data Lake Storage 1. hesabı. [Azure Portal kullanarak Azure Data Lake Storage 1. kullanmaya başlama](../../data-lake-store/data-lake-store-get-started-portal.md)yönergelerini izleyin.

* Depolama alanı olarak Data Lake Storage 1. Azure HDInsight Spark küme. [Hızlı başlangıç: HDInsight 'ta kümeleri ayarlama bölümündeki](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)yönergeleri izleyin.

## <a name="prepare-the-data"></a>Verileri hazırlama

> [!NOTE]  
> HDInsight kümesini varsayılan depolama alanı olarak Data Lake Storage oluşturduysanız bu adımı gerçekleştirmeniz gerekmez. Küme oluşturma işlemi, kümeyi oluştururken belirttiğiniz Data Lake Storage hesaba örnek veri ekler. Data Lake Storage ile HDInsight Spark kümesini kullanma bölümüne atlayın.

Ek depolama alanı olarak Data Lake Storage ve varsayılan depolama alanı olarak Azure Depolama Blobu bir HDInsight kümesi oluşturduysanız, önce bazı örnek verileri Data Lake Storage hesabına kopyalamanız gerekir. HDInsight kümesiyle ilişkili Azure Depolama Blobu örnek verileri kullanabilirsiniz. Bunu yapmak için [AdlCopy aracını](https://www.microsoft.com/download/details.aspx?id=50358) kullanabilirsiniz. Aracı bağlantıdan indirin ve yükleyin.

1. Bir komut istemi açın ve AdlCopy 'in yüklü olduğu dizine gidin, genellikle `%HOMEPATH%\Documents\adlcopy`.

2. Belirli bir blobu kaynak kapsayıcısından Data Lake Storage kopyalamak için aşağıdaki komutu çalıştırın:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    **/Hdisamples/hdisamples/sensorsampledata/hboş/** konumundaki **HVAC. csv** örnek veri dosyasını Azure Data Lake Storage hesabına kopyalayın. Kod parçacığı şöyle görünmelidir:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Dosya ve yol adlarının uygun büyük/küçük harf kullanımının doğru olduğundan emin olun.

3. Data Lake Storage hesabınıza sahip olduğunuz Azure aboneliğinin kimlik bilgilerini girmeniz istenir. Aşağıdaki kod parçacığına benzer bir çıkış görürsünüz:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Veri dosyası (**HVAC. csv**) Data Lake Storage hesabında **/HVAC** klasörü altına kopyalanacak.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Data Lake Storage 1. ile HDInsight Spark kümesi kullanma

1. [Azure Portal](https://portal.azure.com/), başlangıç panosundan, Apache Spark kümenizin kutucuğuna tıklayın (başlangıç panosuna sabitlediğiniz takdirde). Ayrıca **Tüm** > **HDInsight Kümelerine Gözat** altından kümenize gidebilirsiniz.

2. Spark kümesi dikey penceresinden **Hızlı Bağlantılar**’a ve sonra **Küme Panosu** dikey penceresinden **Jupyter Not Defteri**’ne tıklayın. İstenirse, küme için yönetici kimlik bilgilerini girin.

   > [!NOTE]  
   > Aşağıdaki URL’yi tarayıcınızda açarak da Jupyter Notebook’a ulaşabilirsiniz. **CLUSTERNAME** değerini kümenizin adıyla değiştirin:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Yeni bir not defteri oluşturun. **Yeni** ve ardından **PySpark** seçeneğine tıklayın.

    ![Yeni Jupyter Not defteri oluşturma](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Yeni bir Jupyter not defteri oluşturma")

4. PySpark çekirdeği kullanarak bir not defteri oluşturduğunuz için açıkça bir bağlam oluşturmanız gerekmez. Birinci kod hücresini çalıştırdığınızda Spark ve Hive bağlamları sizin için otomatik olarak oluşturulur. Bu senaryo için gereken türleri içeri aktararak işleme başlayabilirsiniz. Bunu yapmak için aşağıdaki kod parçacığını bir hücreye yapıştırın ve **SHIFT + ENTER** tuşuna basın.

        from pyspark.sql.types import *

    Jupyter’de bir işi her çalıştırdığınızda web tarayıcınızın pencere başlığında not defteri başlığı ile birlikte **(Meşgul)** durumu gösterilir. Ayrıca sağ üst köşedeki **PySpark** metninin yanında içi dolu bir daire görürsünüz. İş tamamlandıktan sonra bu simge boş bir daireye dönüşür.

     ![Jupyter Not defteri işinin durumu](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Jupyter not defteri işinin durumu")

5. Örnek verileri, Data Lake Storage 1. hesabına kopyaladığınız **HVAC. csv** dosyasını kullanarak geçici bir tabloya yükleyin. Data Lake Storage hesabındaki verilere aşağıdaki URL modelini kullanarak erişebilirsiniz.

   * Varsayılan depolama alanı olarak Data Lake Storage 1. varsa, HVAC. csv aşağıdaki URL 'ye benzer yolda olacaktır:

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       Ayrıca, aşağıdaki gibi kısaltılmış bir biçim de kullanabilirsiniz:

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * Ek depolama alanı Data Lake Storage sahipseniz, HVAC. csv dosyayı kopyaladığınız konumda olacaktır, örneğin:

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Boş bir hücrede, aşağıdaki kod örneğini yapıştırın, **MYDATALAKESTORE** değerini Data Lake Storage hesap adınızla değiştirin ve **SHIFT + enter**tuşlarına basın. Bu kod örneği, verileri **hvac** adlı geçici bir tabloya kaydeder.

           # Load the data. The path below assumes Data Lake Storage is default storage for the Spark cluster
           hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

           # Create the schema
           hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

           # Parse the data in hvacText
           hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

           # Create a data frame
           hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

           # Register the data fram as a table to run queries against
           hvacdf.registerTempTable("hvac")

6. Bir PySpark çekirdeği kullandığınız için **sihrini kullanarak yeni oluşturduğunuz**hvac`%%sql` geçici tablosunda bundan böyle bir SQL sorgusunu doğrudan çalıştırabilirsiniz. `%%sql` Magic hakkında daha fazla bilgi edinmek ve PySpark çekirdeğiyle kullanılabilen diğer mıknatıler hakkında daha fazla bilgi için, bkz. [Apache Spark HDInsight kümeleri Ile Jupyter not defterlerinde kullanılabilen çekirdekler](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. İş başarıyla tamamlandıktan sonra varsayılan olarak aşağıdaki tablo çıktısı görüntülenir.

      ![Sorgu sonucunun tablo çıktısı](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Sorgu sonucunun tablo çıktısı")

     Sonuçları diğer görselleştirmelerde de görebilirsiniz. Örneğin, aynı çıktı için bir alan grafiği aşağıdaki gibi görünür.

     ![Sorgu sonucunun alan grafiği](./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png "Sorgu sonucunun alan grafiği")

8. Uygulamayı çalıştırmayı tamamladıktan sonra kaynakları serbest bırakmak için not defterini kapatmanız gerekir. Bunu yapmak için not defterindeki **Dosya** menüsünde **Kapat ve Durdur**’a tıklayın. Bunun yapılması not defterini kapatır.


## <a name="next-steps"></a>Sonraki adımlar

* [Apache Spark kümesinde çalıştırılacak tek başına bir Scala uygulaması oluşturma](apache-spark-create-standalone-application.md)
* [HDInsight Spark Linux kümesi için Apache Spark uygulamalar oluşturmak üzere Azure Toolkit for IntelliJ 'de HDInsight araçlarını kullanma](apache-spark-intellij-tool-plugin.md)
* [HDInsight Spark Linux kümesi için Apache Spark uygulamalar oluşturmak üzere Azure Toolkit for Eclipse 'de HDInsight araçlarını kullanma](apache-spark-eclipse-tool-plugin.md)
* [Azure HDInsight kümeleriyle Azure Data Lake Storage 2. Nesil hizmetini kullanma](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
