---
title: Spark-Azure 'da Python kitaplıklarıyla Web sitesi günlüklerini çözümleme
description: Bu not defteri, Azure HDInsight 'ta Spark ile özel bir kitaplık kullanarak günlük verilerinin nasıl çözümlendiğini gösterir.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: d99bf914d3ef746fe328d2447426565fc15e52b4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866327"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>HDInsight üzerinde Apache Spark kümesiyle özel bir Python kitaplığı kullanarak Web sitesi günlüklerini çözümleme

Bu not defteri, HDInsight üzerinde Apache Spark olan özel bir kitaplık kullanarak günlük verilerinin nasıl çözümlendiğini gösterir. Kullandığımız özel kitaplık, **iislogparser.py** adlı bir Python kitaplığıdır.

## <a name="prerequisites"></a>Önkoşullar

HDInsight üzerinde bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Ham verileri RDD olarak kaydet

Bu bölümde, ham örnek verilerinizi işleyen işleri çalıştırmak ve Hive tablosu olarak kaydetmek için HDInsight 'ta bir Apache Spark kümesiyle ilişkili [Jupyter](https://jupyter.org) Not defterini kullanırız. Örnek veriler, varsayılan olarak tüm kümelerdeki kullanılabilir bir. CSV dosyasıdır (hvac.csv).

Verileriniz bir Apache Hive tablo olarak kaydedildikten sonra, sonraki bölümde Power BI ve Tableau gibi bı araçlarını kullanarak Hive tablosuna bağlanacağız.

1. Bir Web tarayıcısından, `https://CLUSTERNAME.azurehdinsight.net/jupyter` , `CLUSTERNAME` Kümenizin adı olan ' a gidin.

1. Yeni bir not defteri oluşturun. **Yeni** ve ardından **pyspark**' ı seçin.

    :::image type="content" source="./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png " alt-text="Yeni bir Apache Jupyter Notebook oluştur" border="true"::: Not defteri "Border =" true ":::

1. Yeni bir not defteri oluşturulur ve Untitled.pynb adı ile açılır. Üstteki Not defteri adını seçin ve kolay bir ad girin.

    :::image type="content" source="./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png " alt-text="Not defteri tebook için bir ad sağlayın" border="true":::"Border =" true ":::

1. PySpark çekirdeği kullanarak bir not defteri oluşturduğunuz için, açıkça herhangi bir bağlam oluşturmanız gerekmez. Birinci kod hücresini çalıştırdığınızda Spark ve Hive bağlamları sizin için otomatik olarak oluşturulur. Bu senaryo için gerekli olan türleri içeri aktararak başlayabilirsiniz. Aşağıdaki kod parçacığını boş bir hücreye yapıştırın ve ardından **SHIFT + enter** tuşlarına basın.

    ```pyspark
    from pyspark.sql import Row
    from pyspark.sql.types import *
    ```

1. Kümede zaten bulunan örnek günlük verilerini kullanarak bir RDD oluşturun. ' De kümeyle ilişkili varsayılan depolama hesabındaki verilere erişebilirsiniz `\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log` . Şu kodu yürütün:

    ```pyspark
    logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')
    ```

1. Önceki adımın başarıyla tamamlandığını doğrulamak için bir örnek günlük kümesi alın.

    ```pyspark
    logs.take(5)
    ```

    Aşağıdaki metne benzer bir çıktı görmeniz gerekir:

    ```output
    [u'#Software: Microsoft Internet Information Services 8.0',
    u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']
    ```

## <a name="analyze-log-data-using-a-custom-python-library"></a>Özel bir Python kitaplığı kullanarak günlük verilerini çözümleme

1. Yukarıdaki çıktıda, ilk iki satır üst bilgi bilgilerini ve kalan her satır bu üst bilgide açıklanan şemayla eşleşir. Bu tür günlüklerin çözümlenmesi karmaşık olabilir. Bu nedenle, bu tür günlükleri ayrıştırmayı çok daha kolay hale getiren özel bir Python kitaplığı (**iislogparser.py**) kullanıyoruz. Varsayılan olarak, bu kitaplık, HDInsight üzerinde HDInsight üzerinde Spark kümenize dahildir `/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py` .

    Ancak, bu kitaplık, `PYTHONPATH` gibi bir içeri aktarma ifadesini kullanarak kullanamıyoruz `import iislogparser` . Bu kitaplığı kullanmak için tüm çalışan düğümlerine dağıtmanız gerekir. Aşağıdaki kod parçacığını çalıştırın.

    ```pyspark
    sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')
    ```

1. `iislogparser` bir `parse_log_line` `None` günlük satırı bir başlık satırı ise ve `LogLine` bir günlük satırı ile karşılaştığında sınıfın bir örneğini döndürürse döndüren bir işlev sağlar. `LogLine`Yalnızca RDD 'deki günlük satırlarını ayıklamak için sınıfını kullanın:

    ```pyspark
    def parse_line(l):
        import iislogparser
        return iislogparser.parse_log_line(l)
    logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
    ```

1. Adımın başarıyla tamamlandığını doğrulamak için ayıklanan birkaç günlük satırı alın.

    ```pyspark
    logLines.take(2)
    ```

   Çıktının aşağıdaki metne benzer olması gerekir:

    ```output
    [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
    2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
    ```

1. `LogLine`Sınıfının sırasıyla bir `is_error()` günlük girdisinde hata kodu olup olmadığını döndüren bazı yararlı yöntemler vardır. Ayıklanan günlük satırlarındaki hata sayısını hesaplamak için bu sınıfı kullanın ve ardından tüm hataları farklı bir dosyaya kaydedin.

    ```pyspark
    errors = logLines.filter(lambda p: p.is_error())
    numLines = logLines.count()
    numErrors = errors.count()
    print 'There are', numErrors, 'errors and', numLines, 'log entries'
    errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')
    ```

    Çıktının durumu olmalıdır `There are 30 errors and 646 log entries` .

1. Ayrıca, verilerin bir görselliğini oluşturmak için **Matplotlib** de kullanabilirsiniz. Örneğin, uzun bir süre çalışan isteklerin nedenini yalıtmak istiyorsanız, ortalama olarak en fazla alan dosyaları bulmak isteyebilirsiniz. Aşağıdaki kod parçacığı, bir istek için en çok geçen en fazla 25 kaynağı alır.

    ```pyspark
    def avgTimeTakenByKey(rdd):
        return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                    .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

    avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])
    ```

   Aşağıdaki metin gibi bir çıktı görmeniz gerekir:

    ```output
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
    ```

1. Bu bilgileri, çizim biçiminde de sunabilirsiniz. Bir çizim oluşturmak için ilk adım olarak, ilk olarak bir geçici tablo **Ortalamasaati** oluşturalım. Tablo, belirli bir zamanda olağan dışı gecikme süreleri olup olmadığını görmek için günlükleri zamana göre gruplandırır.

    ```pyspark
    avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
    schema = StructType([StructField('Minutes', IntegerType(), True),
                        StructField('Time', FloatType(), True)])

    avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
    avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
    ```

1. Ardından, **Averagetime** tablosundaki tüm kayıtları almak IÇIN aşağıdaki SQL sorgusunu çalıştırabilirsiniz.

    ```pyspark
    %%sql -o averagetime
    SELECT * FROM AverageTime
    ```

   `%%sql`Sonra da Magic, `-o averagetime` sorgunun çıkışının jupi sunucusunda (genellikle kümenin baş düğümüne) kalıcı olarak kalıcı olmasını sağlar. Çıktı, belirtilen **Ortalama** ada sahip bir [Pandas](https://pandas.pydata.org/) dataframe olarak kalıcıdır.

   Aşağıdaki görüntüde olduğu gibi bir çıktı görmeniz gerekir:

   :::image type="content" source="./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png " alt-text="HDInsight Jupyter SQL sorgu çıkış" border="true":::yter SQL sorgu çıktısı "Border =" true ":::

   Magic hakkında daha fazla bilgi için `%%sql` bkz. [%% SQL Magic Ile desteklenen parametreler](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

1. Artık bir çizim oluşturmak için veri görselleştirmesini oluşturmak için kullanılan bir kitaplık olan Matplotlib 'i kullanabilirsiniz. Çizimin yerel olarak kalıcı **averagetime** dataframe 'ten oluşturulması gerektiğinden, kod parçacığının Magic ile başlaması gerekir `%%local` . Bu, kodun Jupyıter sunucusunda yerel olarak çalıştırılmasını sağlar.

    ```pyspark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
    plt.xlabel('Time (min)')
    plt.ylabel('Average time taken for request (ms)')
    ```

   Aşağıdaki görüntüde olduğu gibi bir çıktı görmeniz gerekir:

   :::image type="content" source="./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png " alt-text="Apache Spark Web günlüğü analiz çizimi" border="true":::EB günlüğü analiz çizimi "Border =" true ":::

1. Uygulamayı çalıştırmayı bitirdikten sonra, kaynakları serbest bırakmak için Not defterini kapatmanız gerekir. Bunu yapmak için not defterindeki **Dosya** menüsünde **Kapat ve Durdur**’u seçin. Bu eylem kapatılacak ve Not defterini kapatacak.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makaleleri keşfet:

* [Genel Bakış: Azure HDInsight’ta Apache Spark](apache-spark-overview.md)
* [Jupyıter Not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)
