---
title: HDInsight 'ta Spark MLlib ile makine öğrenimi örneği-Azure
description: Lojistik gerileme aracılığıyla sınıflandırma kullanarak bir veri kümesini analiz eden bir makine öğrenimi uygulaması oluşturmak için Spark MLlib 'i nasıl kullanacağınızı öğrenin.
keywords: Spark Machine Learning, Spark Machine Learning örneği
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: c8ead7abc454df387db31b2ce65d2ba714b0067d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494082"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Machine Learning uygulaması derlemek ve bir veri kümesini çözümlemek için Apache Spark MLlib kullanın

Açık bir veri kümesi üzerinde basit bir tahmine dayalı analiz yapmak için, bir makine öğrenimi uygulaması oluşturmak üzere [Mllib](https://spark.apache.org/mllib/) Apache Spark nasıl kullanacağınızı öğrenin. Spark 'ın yerleşik makine öğrenimi kitaplıklarından Bu örnek Lojistik gerileme aracılığıyla *Sınıflandırmayı* kullanır. 

MLlib, aşağıdakiler için uygun olan yardımcı programlar dahil olmak üzere makine öğrenimi görevleri için yararlı birçok yardımcı program sağlayan bir temel Spark kitaplığıdır:

* Sınıflandırma
* Regresyon
* Lenmesi
* Konu modelleme
* Tekil değer ayrıştırma (SVD) ve asıl bileşen analizi (PCA)
* Örnek istatistiklerini test etme ve hesaplama

## <a name="understand-classification-and-logistic-regression"></a>Sınıflandırmayı ve lojistik regresyonunu anlayın
Popüler bir makine öğrenimi görevi *sınıflandırması*, giriş verilerini kategorilere sıralama işlemidir. Bu, sağladığınız giriş verilerine nasıl "etiket" atanacağını anlamak için bir sınıflandırma algoritmasının işleridir. Örneğin, stok bilgilerini girdi olarak kabul eden bir makine öğrenimi algoritması ve stoku iki kategoriye böler: Satım ve tutmanız gereken hisse senetleri.

Lojistik regresyon, sınıflandırma için kullandığınız algoritmadır. Spark 'un lojistik regresyon API 'SI, *ikili sınıflandırmada*veya giriş verilerinin iki gruptan birinde sınıflandırılmasına yardımcı olur. Lojistik gerilemeleri hakkında daha fazla bilgi için bkz. [Vikipedi](https://en.wikipedia.org/wiki/Logistic_regression).

Özet olarak, lojistik regresyon süreci, bir giriş vektörünün bir grupta veya diğeri ait olma olasılığını tahmin etmek için kullanılabilecek bir *lojistik işlevi* üretir.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Yiyecek İnceleme verilerinde tahmine dayalı analiz örneği
Bu örnekte, [Chicago veri portalının şehri](https://data.cityofchicago.org/)aracılığıyla elde edilen yiyecek İnceleme verileri (**Food_Inspections1. csv**) üzerinde bazı tahmine dayalı analiz gerçekleştirmek için Spark 'ı kullanırsınız. Bu veri kümesi, her bir oluşturma hakkında bilgiler, bulunan ihlaller (varsa) ve inceleme sonuçları dahil olmak üzere Chicago 'da yürütülen yiyecek oluşturma incelemeleri hakkında bilgiler içerir. CSV veri dosyası, **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**adresindeki kümeyle ilişkili depolama hesabında zaten kullanılabilir.

Aşağıdaki adımlarda, bir yiyecek incelemesini geçirmek veya başarısız kılmak için ne olduğunu görmek üzere bir model geliştirirsiniz.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Apache Spark MLlib makine öğrenimi uygulaması oluşturma

1. PySpark çekirdeği kullanarak bir Jupyter not defteri oluşturun. Yönergeler için bkz. [Jupyter not defteri oluşturma](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Bu uygulama için gereken türleri içeri aktarın. Aşağıdaki kodu kopyalayıp boş bir hücreye yapıştırın ve ardından **SHIFT + enter**tuşlarına basın.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    PySpark çekirdeği nedeniyle, açıkça herhangi bir bağlam oluşturmanız gerekmez. Birinci kod hücresini çalıştırdığınızda Spark ve Hive bağlamları sizin için otomatik olarak oluşturulur. 

## <a name="construct-the-input-dataframe"></a>Giriş veri çerçevesini oluşturun

Ham veriler CSV biçiminde olduğundan, dosyayı belleğe yapılandırılmamış metin olarak çekmek için Spark bağlamını kullanabilir, sonra da her bir veri satırını ayrıştırmak için Python 'un CSV kitaplığını kullanabilirsiniz.

1. Giriş verilerini içeri aktarıp ayrıştırarak esnek bir dağıtılmış veri kümesi (RDD) oluşturmak için aşağıdaki satırları çalıştırın.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value
    
    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. RDD 'den bir satır almak için aşağıdaki kodu çalıştırın, bu nedenle veri şemasına göz atabilirsiniz:

    ```PySpark
    inspections.take(1)
    ```

    Çıktı:

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    Çıktı, size giriş dosyasının şeması hakkında fikir verir. Her bir oluşturma adı, kurma türü, adres, İncelemeleri ve konum gibi diğer şeyler arasında yer alır. 

3. Bir dataframe (*df*) ve geçici bir tablo (*countresults*) oluşturmak için aşağıdaki kodu çalıştırarak, tahmine dayalı analiz için yararlı olan birkaç sütun oluşturun. `sqlContext`, yapılandırılmış verilerde dönüşümler gerçekleştirmek için kullanılır. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Veri çerçevesindeki ilgilendiğiniz dört sütun **kimlik**, **ad**, **sonuçlar**ve **ihlallerdir**.

4. Verilerin küçük bir örneğini almak için aşağıdaki kodu çalıştırın:

    ```PySpark
    df.show(5)
    ```

    Çıktı:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>Verileri anlayın

Veri kümesinin neleri içerdiğini bir fikir almaya başlayalım. 

1. **Sonuçlar** sütunundaki ayrı değerleri göstermek için aşağıdaki kodu çalıştırın:

    ```PySpark
    df.select('results').distinct().show()
    ```

    Çıktı:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Bu sonuçların dağıtımını görselleştirmek için aşağıdaki kodu çalıştırın:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    `%%sql` Magic tarafından izlenen `-o countResultsdf`, sorgunun çıkışının jupi sunucusunda (genellikle kümenin baş düğümüne) yerel olarak kalıcı olmasını sağlar. Çıktı, belirtilen **Countresultsdf**adlı bir [Pandas](https://pandas.pydata.org/) dataframe olarak kalıcıdır. `%%sql` Magic ve PySpark çekirdeği ile kullanılabilen diğer mıknatık hakkında daha fazla bilgi için bkz. [Apache Spark HDInsight kümeleri Ile Jupyter not defterlerinde kullanılabilen çekirdekler](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Çıktı:

    ![SQL sorgu çıktısı](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL sorgu çıktısı")


3. Ayrıca, bir çizim oluşturmak için veri görselleştirmesini oluşturmak için kullanılan bir kitaplık olan [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib)' ı da kullanabilirsiniz. Çizimin yerel olarak kalıcı **Countresultsdf** dataframe 'den oluşturulması gerektiğinden, kod parçacığının `%%local` Magic ile başlaması gerekir. Bu, kodun Jupyıter sunucusunda yerel olarak çalıştırılmasını sağlar.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Çıktı:

    ![Spark Machine Learning uygulama çıkışı-beş ayrı inceleme sonucu olan pasta grafik](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Spark Machine Learning sonuç çıkışı")

    Yiyecek denetimi sonucunu tahmin etmek için ihlallere göre bir model geliştirmeniz gerekir. Lojistik regresyon bir ikili sınıflandırma yöntemi olduğundan, sonuç verilerini iki kategoride gruplamak mantıklı olur: **başarısız** ve **Pass**:

   - Aktar
       - Aktar
       - W/koşullarını geçir
   - Neden
       - Neden
   - Mı
       - İş bulunamadı
       - Iş dışı

     Diğer sonuçlara sahip veriler ("Iş bulunamadı" veya "Iş dışı") yararlı değildir ve sonuç olarak sonuçların çok küçük bir yüzdesini yapar.

4. Varolan veri çerçevesini (`df`) her İnceleme bir etiket ihlalleri çifti olarak temsil edildiği yeni bir veri çerçevesine dönüştürmek için aşağıdaki kodu çalıştırın. Bu durumda, `0.0` bir etiketi bir hata temsil eder, bir `1.0` etiketi bir başarıyı temsil eder ve bir `-1.0` etiketi bu iki durumun yanı sıra bazı sonuçları temsil eder. 

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Etiketli verilerin bir satırını göstermek için aşağıdaki kodu çalıştırın:

    ```PySpark
    labeledData.take(1)
    ```

    Çıktı:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Giriş veri çerçevesinden bir lojistik regresyon modeli oluşturma

Son görev, etiketli verileri Lojistik gerileme tarafından çözümlenebilecek bir biçime dönüştürmelidir. Bir lojistik regresyon algoritmasına giriş, "özellik vektörü" ın giriş noktasını temsil eden sayıların vektörü olduğu bir *etiket özelliği vektör çiftleri*kümesi gerektirir. Bu nedenle, yarı yapılandırılmış olan ve serbest metinli çok sayıda yorum içeren "ihlalleri" sütununu, makinenin kolayca anlayabileceği gerçek sayı dizisine dönüştürmeniz gerekir.

Doğal dili işlemeye yönelik bir standart makine öğrenimi yaklaşımı, her bir ayrı sözcüğü bir "Dizin" olarak atamak ve ardından makine öğrenimi algoritmasına bir vektör iletmektir. bu nedenle her bir dizin değeri, metin dizesindeki sözcüğün göreli sıklığını içerir.

MLlib bu işlemi gerçekleştirmek için kolay bir yol sağlar. İlk olarak, her bir dizedeki tek kelimeleri almak için her bir ihlal dizesi olan "simgeize". Ardından, her bir belirteç kümesini bir özellik vektörüne dönüştürmek için bir `HashingTF` kullanın, böylece bir model oluşturmak için lojistik regresyon algoritmasına geçirilebilirler. Bu adımların tümünü bir "işlem hattı" kullanarak sırayla gerçekleştirebilirsiniz.

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Başka bir veri kümesi kullanarak modeli değerlendirme

Daha önce oluşturduğunuz modeli, gözlemlenecek ihlallere göre yeni İncelemeleri sonuçlarının ne olacağı *tahmin* etmek için kullanabilirsiniz. Bu modeli, **Food_Inspections1. csv**veri kümesi üzerinde eğitildi. Yeni verilerde bu modelin gücünü *değerlendirmek* için ikinci bir veri kümesi olan **Food_Inspections2. csv**' yi kullanabilirsiniz. Bu ikinci veri kümesi (**Food_Inspections2. csv**), kümeyle ilişkili varsayılan depolama kapsayıcısıdır.

1. Model tarafından oluşturulan tahmini içeren **predictionsDf** yeni bir veri çerçevesi oluşturmak için aşağıdaki kodu çalıştırın. Kod parçacığı Ayrıca veri çerçevesini temel alan **tahminler** adlı geçici bir tablo oluşturur.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Aşağıdaki gibi bir çıktı görmeniz gerekir:

    ```
    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Tahminlerden birine bakın. Bu kod parçacığını Çalıştır:

    ```PySpark
    predictionsDf.take(1)
    ```

   Test veri kümesindeki ilk giriş için bir tahmin vardır.
1. `model.transform()` yöntemi aynı dönüşümü aynı şemaya sahip tüm yeni verilere uygular ve verilerin sınıflandırılacağı bir tahmine ulaşır. Tahmine dayalı olarak ne kadar doğru olduğunu öğrenmek için bazı basit istatistikler yapabilirsiniz:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    Çıktı aşağıdaki gibi görünür:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Spark ile Lojistik gerileme kullanılması, Ingilizce 'deki ihlal açıklamaları arasındaki ilişkinin doğru bir modelini ve belirli bir işin bir yiyecek incelemesini geçmesi veya başarısız olup olmayacağını sağlar.

## <a name="create-a-visual-representation-of-the-prediction"></a>Tahmine ilişkin görsel bir temsili oluşturun
Artık bu testin sonuçları hakkında neden olacak bir son görselleştirme oluşturabilirsiniz.

1. Daha önce oluşturulan tahmine **dayalı geçici tablodaki farklı tahminleri ve** sonuçları çıkartarak başlayabilirsiniz. Aşağıdaki sorgular çıktıyı *true_positive*, *false_positive*, *true_negative*ve *false_negative*olarak ayırır. Aşağıdaki sorgularda, `-q` kullanarak görselleştirmeyi devre dışı bırakır ve ayrıca çıktıyı (`-o`kullanarak), daha sonra `%%local` Magic ile kullanılabilecek veri çerçeveleri olarak kaydeder.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Son olarak, **Matplotlib**kullanarak çizim oluşturmak için aşağıdaki kod parçacığını kullanın.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Aşağıdaki çıktıyı görmeniz gerekir:

    ![Spark Machine Learning uygulama çıkışı-başarısız yiyecek incelemeleri için pasta grafik yüzdeleri.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Spark Machine Learning sonuç çıkışı")

    Bu grafikte, "pozitif" bir sonuç başarısız yiyecek incelemesini ifade ederken, negatif bir sonuç de geçen bir inceleme anlamına gelir.

## <a name="shut-down-the-notebook"></a>Not defterini kapatma
Uygulamayı çalıştırmayı bitirdikten sonra, kaynakları serbest bırakmak için Not defterini kapatmanız gerekir. Bunu yapmak için not defterindeki **Dosya** menüsünde **Kapat ve Durdur**’u seçin. Bu, kapanır ve Not defterini kapatır.

## <a name="seealso"></a>Ayrıca bkz.
* [Genel Bakış: Azure HDInsight’ta Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>Senaryolar
* [BI ile Apache Spark: bı araçlarıyla HDInsight 'ta Spark kullanarak etkileşimli veri çözümlemesi gerçekleştirme](apache-spark-use-bi-tools.md)
* [Machine Learning ile Apache Spark: HVAC verilerini kullanarak oluşturma sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](apache-spark-ipython-notebook-machine-learning.md)
* [HDInsight 'ta Apache Spark kullanarak Web sitesi günlüğü Analizi](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Uygulamaları oluşturma ve çalıştırma
* [Scala kullanarak tek başına uygulama oluşturma](apache-spark-create-standalone-application.md)
* [Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Araçlar ve uzantılar
* [Spark Scala uygulamaları oluşturmak ve göndermek amacıyla IntelliJ IDEA için HDInsight Araçları Eklentisini kullanma](apache-spark-intellij-tool-plugin.md)
* [Apache Spark uygulamalarında uzaktan hata ayıklama için IntelliJ fıkır için HDInsight Araçları eklentisini kullanın](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesindeki Jupyter Not defteri için kullanılabilir kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Kaynakları yönetme
* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)
