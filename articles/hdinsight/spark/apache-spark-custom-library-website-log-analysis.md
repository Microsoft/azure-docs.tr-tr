---
title: Spark-Azure 'da Python kitaplıklarıyla Web sitesi günlüklerini çözümleme
description: Bu not defteri, Azure HDInsight 'ta Spark ile özel bir kitaplık kullanarak günlük verilerinin nasıl çözümlendiğini gösterir.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 6d23e8cfa8d20169d2b63723138b60dafb1069de
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146990"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>HDInsight üzerinde Apache Spark kümesiyle özel bir Python kitaplığı kullanarak Web sitesi günlüklerini çözümleme

Bu not defteri, HDInsight üzerinde Apache Spark olan özel bir kitaplık kullanarak günlük verilerinin nasıl çözümlendiğini gösterir. Kullandığımız özel kitaplık, **iislogparser.py**adlı bir Python kitaplığıdır.

> [!TIP]  
> Bu makale, HDInsight 'ta oluşturduğunuz Spark (Linux) kümesinde Jupyter Not defteri olarak da kullanılabilir. Not defteri deneyimi, Python kod parçacıklarını Not defterinden çalıştırmanızı sağlar. Makaleyi bir not defteri içinden gerçekleştirmek için, bir Spark kümesi oluşturun, bir Jupyter Not defteri (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) başlatın ve sonra, **pyspark** klasörü altında **özel bir kitaplık. ipynb kullanarak Spark ile günlükleri analiz** ederek çalıştırın.

**Ön koşullar:**

Aşağıdakilere sahip olmanız gerekir:

* Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* HDInsight üzerinde bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Ham verileri RDD olarak kaydet
Bu bölümde, ham örnek verilerinizi işleyen işleri çalıştırmak ve Hive tablosu olarak kaydetmek için HDInsight 'ta bir Apache Spark kümesiyle ilişkili [Jupyter](https://jupyter.org) Not defterini kullanırız. Örnek veriler, varsayılan olarak tüm kümelerdeki kullanılabilir bir. CSV dosyasıdır (HVAC. csv).

Verileriniz bir Apache Hive tablo olarak kaydedildikten sonra, sonraki bölümde Power BI ve Tableau gibi bı araçlarını kullanarak Hive tablosuna bağlanacağız.

1. [Azure portalındaki](https://portal.azure.com/) başlangıç panosunda Spark kümenizin kutucuğuna tıklayın (başlangıç panosuna sabitlediyseniz). Ayrıca **Browse All (Tümüne Gözat)**  > **HDInsight Clusters (HDInsight Kümeleri)** altından kümenize gidebilirsiniz.

2. Spark kümesi dikey penceresinden **Küme Panosu**’na ve ardından **Jupyter Notebook**’a tıklayın. İstenirse, küme için yönetici kimlik bilgilerini girin.

   > [!NOTE]
   > Aşağıdaki URL’yi tarayıcınızda açarak da Jupyter Notebook’a ulaşabilirsiniz. **CLUSTERNAME** değerini kümenizin adıyla değiştirin:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Yeni bir not defteri oluşturun. **Yeni** ve ardından **PySpark** seçeneğine tıklayın.

    ![Yeni bir Apache Jupyter Not defteri oluşturma](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Yeni Jupyter Not defteri oluşturma")

4. Yeni bir not defteri oluşturulur ve Untitled.pynb adı ile açılır. Üstteki not defteri adına tıklayın ve kolay bir ad girin.

    ![Not defteri adını belirtme](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Not defteri adını belirtme")
5. PySpark çekirdeği kullanarak bir not defteri oluşturduğunuz için açıkça bir bağlam oluşturmanız gerekmez. Birinci kod hücresini çalıştırdığınızda Spark ve Hive bağlamları sizin için otomatik olarak oluşturulur. Bu senaryo için gerekli olan türleri içeri aktararak başlayabilirsiniz. Aşağıdaki kod parçacığını boş bir hücreye yapıştırın ve sonra **SHIFT + ENTER** tuşlarına basın.

        from pyspark.sql import Row
        from pyspark.sql.types import *

6. Kümede zaten bulunan örnek günlük verilerini kullanarak bir RDD oluşturun. **\Hdisamptas\hdisamppas\websitelogsampledata\samplelog\909f2b.log**konumundaki kümeyle ilişkili varsayılan depolama hesabındaki verilere erişebilirsiniz.

        logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')

7. Önceki adımın başarıyla tamamlandığını doğrulamak için bir örnek günlük kümesi alın.

        logs.take(5)

    Aşağıdakine benzer bir çıktı görmeniz gerekir:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Özel bir Python kitaplığı kullanarak günlük verilerini çözümleme

1. Yukarıdaki çıktıda, ilk iki satır üst bilgi bilgilerini ve kalan her satır bu üst bilgide açıklanan şemayla eşleşir. Bu tür günlüklerin çözümlenmesi karmaşık olabilir. Bu nedenle, bu tür günlükleri ayrıştırmayı çok daha kolay hale getiren özel bir Python kitaplığı (**iislogparser.py**) kullanıyoruz. Varsayılan olarak, bu kitaplık **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**adresinde HDInsight üzerinde Spark kümenize dahildir.

    Ancak, bu kitaplık içinde `PYTHONPATH` olmadığından, gibi `import iislogparser`bir içeri aktarma ifadesini kullanarak kullanmıyoruz. Bu kitaplığı kullanmak için tüm çalışan düğümlerine dağıtmanız gerekir. Aşağıdaki kod parçacığını çalıştırın.

        sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')

1. `iislogparser`bir günlük satırı `parse_log_line` bir başlık `None` satırı ise ve bir günlük satırı ile karşılaştığında `LogLine` sınıfın bir örneğini döndürürse döndüren bir işlev sağlar. Yalnızca RDD 'deki günlük satırlarını ayıklamak için sınıfınıkullanın:`LogLine`

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()

1. Adımın başarıyla tamamlandığını doğrulamak için ayıklanan birkaç günlük satırı alın.

       logLines.take(2)

   Çıktının aşağıdakine benzer olması gerekir:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]

1. Sınıfının sırasıyla bir günlük girdisinde hata kodu olup olmadığını döndüren bazı yararlı `is_error()`yöntemler vardır. `LogLine` Ayıklanan günlük satırlarındaki hata sayısını hesaplamak için bunu kullanın ve ardından tüm hataları farklı bir dosyaya kaydedin.

       errors = logLines.filter(lambda p: p.is_error())
       numLines = logLines.count()
       numErrors = errors.count()
       print 'There are', numErrors, 'errors and', numLines, 'log entries'
       errors.map(lambda p: str(p)).saveAsTextFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

   Aşağıdaki gibi bir çıktı görmeniz gerekir:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       There are 30 errors and 646 log entries
1. Ayrıca, verilerin bir görselliğini oluşturmak için **Matplotlib** de kullanabilirsiniz. Örneğin, uzun bir süre çalışan isteklerin nedenini yalıtmak istiyorsanız, ortalama olarak en fazla alan dosyaları bulmak isteyebilirsiniz.
   Aşağıdaki kod parçacığı, bir istek için en çok geçen en fazla 25 kaynağı alır.

       def avgTimeTakenByKey(rdd):
           return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                   lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                   lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                     .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

       avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

   Aşağıdaki gibi bir çıktı görmeniz gerekir:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [(u'/blogposts/mvc4/step13.png', 197.5),
        (u'/blogposts/mvc2/step10.jpg', 179.5),
        (u'/blogposts/extractusercontrol/step5.png', 170.0),
        (u'/blogposts/mvc4/step8.png', 159.0),
        (u'/blogposts/mvcrouting/step22.jpg', 155.0),
        (u'/blogposts/mvcrouting/step3.jpg', 152.0),
        (u'/blogposts/linqsproc1/step16.jpg', 138.75),
        (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
        (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
        (u'/blogposts/nested/step2.jpg', 126.0),
        (u'/blogposts/adminpack/step1.png', 124.0),
        (u'/BlogPosts/datalistpaging/step2.png', 118.0),
        (u'/blogposts/mvc4/step35.png', 117.0),
        (u'/blogposts/mvcrouting/step2.jpg', 116.5),
        (u'/blogposts/aboutme/basketball.jpg', 109.0),
        (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
        (u'/blogposts/mvc4/step12.png', 106.0),
        (u'/blogposts/linq8/step0.jpg', 105.5),
        (u'/blogposts/mvc2/step18.jpg', 104.0),
        (u'/blogposts/mvc2/step11.jpg', 104.0),
        (u'/blogposts/mvcrouting/step1.jpg', 104.0),
        (u'/blogposts/extractusercontrol/step1.png', 103.0),
        (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
        (u'/blogposts/mvcrouting/step21.jpg', 101.0),
        (u'/blogposts/mvc4/step1.png', 98.0)]

1. Bu bilgileri, çizim biçiminde de sunabilirsiniz. Bir çizim oluşturmak için ilk adım olarak, ilk olarak bir geçici tablo **Ortalamasaati**oluşturalım. Tablo, belirli bir zamanda olağan dışı gecikme süreleri olup olmadığını görmek için günlükleri zamana göre gruplandırır.

       avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
       schema = StructType([StructField('Minutes', IntegerType(), True),
                            StructField('Time', FloatType(), True)])

       avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
       avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

1. Ardından, **Averagetime** tablosundaki tüm kayıtları almak IÇIN aşağıdaki SQL sorgusunu çalıştırabilirsiniz.

       %%sql -o averagetime
       SELECT * FROM AverageTime

   Sonra da Magic, `%%sql` sorgunun çıkışının jupi sunucusunda (genellikle kümenin baş düğümüne) kalıcı olarak kalıcı olmasını sağlar.`-o averagetime` Çıktı, belirtilen **Ortalama**ada sahip bir [Pandas](https://pandas.pydata.org/) dataframe olarak kalıcıdır.

   Aşağıdaki gibi bir çıktı görmeniz gerekir:

   ![HDInsight jupkiter SQL qeriy çıkışı](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "SQL sorgu çıktısı")

   `%%sql` Magic hakkında daha fazla bilgi için bkz. [%% SQL Magic ile desteklenen parametreler](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

1. Artık bir çizim oluşturmak için veri görselleştirmesini oluşturmak için kullanılan bir kitaplık olan Matplotlib 'i kullanabilirsiniz. Çizimin yerel olarak kalıcı **averagetime** dataframe 'ten oluşturulması gerektiğinden, kod parçacığının `%%local` Magic ile başlaması gerekir. Bu, kodun Jupyıter sunucusunda yerel olarak çalıştırılmasını sağlar.

       %%local
       %matplotlib inline
       import matplotlib.pyplot as plt

       plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
       plt.xlabel('Time (min)')
       plt.ylabel('Average time taken for request (ms)')

   Aşağıdaki gibi bir çıktı görmeniz gerekir:

   ![Apache Spark Web günlüğü analiz çizimi](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib çıkışı")

1. Uygulamayı çalıştırmayı tamamladıktan sonra kaynakları serbest bırakmak için not defterini kapatmanız gerekir. Bunu yapmak için not defterindeki **Dosya** menüsünde **Kapat ve Durdur**’a tıklayın. Bunun yapılması not defterini kapatır.

## <a name="seealso"></a>Ayrıca bkz.
* [Bakýþ Azure HDInsight üzerinde Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>Senaryolar
* [BI ile Apache Spark: Bı araçlarıyla HDInsight 'ta Spark kullanarak etkileşimli veri çözümlemesi gerçekleştirme](apache-spark-use-bi-tools.md)
* [Machine Learning Apache Spark: HVAC verilerini kullanarak oluşturma sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning Apache Spark: Yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](apache-spark-machine-learning-mllib-ipython.md)

### <a name="create-and-run-applications"></a>Uygulamaları oluşturma ve çalıştırma
* [Scala kullanarak tek başına uygulama oluşturma](apache-spark-create-standalone-application.md)
* [Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Araçlar ve uzantılar
* [Apache Spark Scala uygulamaları oluşturmak ve göndermek için IntelliJ fıkır için HDInsight Araçları eklentisini kullanın](apache-spark-intellij-tool-plugin.md)
* [Apache Spark uygulamalarında uzaktan hata ayıklama için IntelliJ fıkır için HDInsight Araçları eklentisini kullanın](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesindeki Jupyter Not defteri için kullanılabilir kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Kaynakları yönet
* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)
