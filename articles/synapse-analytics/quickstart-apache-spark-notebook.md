---
title: 'Hızlı başlangıç: Web araçlarını kullanarak sunucusuz Apache Spark havuzu oluşturma'
description: Bu hızlı başlangıçta, Azure SYNAPSE Analytics 'te sunucusuz Apache Spark havuzu oluşturmak için web araçlarının nasıl kullanılacağı ve Spark SQL sorgusunun nasıl çalıştırılacağı gösterilmektedir.
services: synapse-analytics
author: euangMS
ms.author: euang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: quickstart
ms.date: 10/16/2020
ms.openlocfilehash: 060c78621b82f4698d4596383cd155d85d483d8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669490"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-in-azure-synapse-analytics-using-web-tools"></a>Hızlı başlangıç: Web araçlarını kullanarak Azure SYNAPSE Analytics 'te sunucusuz Apache Spark havuzu oluşturma

Bu hızlı başlangıçta, Web araçlarını kullanarak Azure SYNAPSE 'da sunucusuz Apache Spark havuzu oluşturmayı öğreneceksiniz. Daha sonra Apache Spark havuzuna bağlanmayı ve dosyalar ve tablolar için Spark SQL sorguları çalıştırmayı öğreneceksiniz. Apache Spark, bellek içi işleme kullanarak hızlı veri analizi ve küme hesaplama sağlar. Azure SYNAPSE Spark hakkında daha fazla bilgi için bkz. [genel bakış: Azure SYNAPSE üzerinde Apache Spark](./spark/apache-spark-overview.md).

> [!IMPORTANT]
> Spark örnekleri için faturalandırma, bunları kullanıp kullanmayacağınızı de dakika başına eşit olarak dağıtılır. Kullanmayı bitirdikten sonra Spark örneğinizi kapattığınızdan emin olun veya kısa bir zaman aşımı ayarlayın. Daha fazla bilgi için bu makalenin **Kaynakları temizleme** bölümüne bakın.

Azure aboneliğiniz yoksa [başlamadan önce ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- [SYNAPSE Analytics çalışma alanı](quickstart-create-workspace.md)
- [Sunucusuz Apache Spark havuzu](quickstart-create-apache-spark-pool-studio.md)

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="create-a-notebook"></a>Not defteri oluşturma

Not defteri, çeşitli programlama dillerini destekleyen etkileşimli bir ortamdır. Not defteri verilerinizle etkileşim kurmanıza, kodu Marku, metinle birleştirmeye ve basit görselleştirmeler gerçekleştirmenize olanak tanır.

1. Kullanmak istediğiniz Azure SYNAPSE çalışma alanının Azure portal görünümünde, **SYNAPSE Studio 'Yu Başlat**' ı seçin.
2. SYNAPSE Studio başlatıldıktan sonra **Geliştir**' i seçin. Ardından, **+** Yeni bir kaynak eklemek için "" simgesini seçin.
3. Buradan, **Not defteri**' ni seçin. Otomatik olarak oluşturulan bir adla yeni bir not defteri oluşturulur ve açılır.
 
     ![Yeni Not defteri](./media/quickstart-apache-spark-notebook/spark-get-started-new-notebook.png "Yeni Not defteri")

4. **Özellikler** penceresinde, Not defteri için bir ad sağlayın.
5. Araç çubuğunda **Yayımla**' ya tıklayın.
6. Çalışma alanınızda yalnızca bir Apache Spark havuzu varsa, varsayılan olarak seçilidir. Hiçbiri seçili değilse, doğru Apache Spark havuzunu seçmek için açılan eklentiyi kullanın.
7. **Kod Ekle**' ye tıklayın. Varsayılan dil `Pyspark` . Pyspark ve Spark SQL karışımını kullanacaksınız, bu nedenle varsayılan seçenek iyi bir seçimdir. Desteklenen diğer diller Spark için Scala ve .NET ' dir.
8. Daha sonra, işlemek için basit bir Spark DataFrame nesnesi oluşturursunuz. Bu durumda, kodu koddan oluşturursunuz. Üç satır ve üç sütun vardır:

   ```python
   new_rows = [('CA',22, 45000),("WA",35,65000) ,("WA",50,85000)]
   demo_df = spark.createDataFrame(new_rows, ['state', 'age', 'salary'])
   demo_df.show()
   ```

9. Şimdi aşağıdaki yöntemlerden birini kullanarak hücreyi çalıştırın:

   - **SHIFT + enter** tuşlarına basın.
   - Hücrenin solundaki mavi oynat simgesini seçin.
   - Araç çubuğundaki **Tümünü Çalıştır** düğmesini seçin.

       ![Veri çerçevesi nesnesi oluştur](./media/quickstart-apache-spark-notebook/spark-get-started-create-data-frame-object.png)

10. Apache Spark havuz örneği zaten çalışmıyorsa, otomatik olarak başlatılır. Apache Spark havuz örneği durumunu çalıştırdığınız hücrenin altında ve ayrıca not defterinin altındaki durum panelinde görebilirsiniz. Havuzun boyutuna bağlı olarak, başlangıç 2-5 dakika sürer. Kod çalışmayı bitirdikten sonra hücrenin altındaki bilgiler, ne kadar süre çalıştırılacağını ve yürütülmesi gerektiğini gösterir. Çıkış hücresinde çıktıyı görürsünüz.

    ![Bir hücreyi yürütmeden çıkış](./media/quickstart-apache-spark-notebook/run-cell-with-output.png)

11. Veriler artık verileri birçok farklı şekilde kullanabileceğiniz bir veri çerçevesinde bulunur. Bu hızlı başlangıçtaki geri kalanı için farklı biçimlerde gerekecektir.
12. Aşağıdaki kodu başka bir hücrede girin ve çalıştırın, bu, verilerin kopyaları ile birlikte bir Spark tablosu, CSV ve bir Parquet dosyası oluşturur:

    ```python
     demo_df.createOrReplaceTempView('demo_df')
     demo_df.write.csv('demo_df', mode='overwrite')
     demo_df.write.parquet('abfss://<<TheNameOfAStorageAccountFileSystem>>@<<TheNameOfAStorageAccount>>.dfs.core.windows.net/demodata/demo_df', mode='overwrite')
    ```

    Depolama Gezgini 'ni kullanıyorsanız, yukarıda kullanılan bir dosyayı yazmanın iki farklı yolu hakkında etkisini görmek mümkündür. Hiçbir dosya sistemi belirtilmediğinde, bu durumda varsayılan olarak kullanılır `default>user>trusted-service-user>demo_df` . Veriler, belirtilen dosya sisteminin konumuna kaydedilir.

    Hem "CSV&quot; hem de &quot;Parquet&quot; biçimlerinde, yazma işlemlerinde bir dizin oluşturan çok sayıda bölümlenmiş dosya ile dikkat edin.

    ![Çıktının Depolama Gezgini görünümü](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage.png &quot;Çıktının Depolama Gezgini görünümü")

    ![Varsayılan > demodata > demo_df yolunu vurgulayan ekran görüntüsü.](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage2.png "Çıktının Depolama Gezgini görünümü")

## <a name="run-spark-sql-statements"></a>Spark SQL deyimleri çalıştırma

Yapılandırılmış Sorgu Dili (SQL), verileri sorgulamak ve tanımlamak için en yaygın ve yaygın olarak kullanılan dildir. Bilinen SQL söz dizimini kullanan Spark SQL, yapısal verileri işleyen bir Apache Spark uzantısı olarak çalışır.

1. Aşağıdaki kodu boş bir hücreye yapıştırın ve kodu çalıştırın. Komut, havuzdaki tabloları listeler.

   ```sql
   %%sql
   SHOW TABLES
   ```

   Azure SYNAPSE Apache Spark havuzunuzdaki bir not defteri kullanırken, `sqlContext` Spark SQL kullanarak sorguları çalıştırmak için kullanabileceğiniz bir önayar alırsınız. `%%sql` Not defterine sorguyu çalıştırmak için önceden belirlenmiş ayarı kullanmasını söyler `sqlContext` . Sorgu, varsayılan olarak tüm Azure SYNAPSE Apache Spark havuzlarıyla birlikte gelen bir sistem tablosundan ilk 10 satırı alır.

2. `demo_df` komutundaki verileri görmek için başka bir sorgu çalıştırın.

    ```sql
    %%sql
    SELECT * FROM demo_df
    ```

    Kod, diğer bir deyişle, iş görünümünü gösteren veri sonuçlarını içeren iki çıkış hücresi üretir.

    Varsayılan olarak, sonuçlar görünümü bir kılavuz gösterir. Ancak, görünümün altında ızgara ve grafik görünümleri arasında geçiş yapmasına olanak tanıyan bir görünüm değiştirici vardır.

    ![Azure SYNAPSE Spark 'da sorgu çıkışı](./media/quickstart-apache-spark-notebook/spark-get-started-query.png "Azure SYNAPSE Spark 'da sorgu çıkışı")

3. **Görünüm** değiştiricisinde **grafik**' i seçin.
4. En sağdaki taraftaki **Görünüm seçenekleri** simgesini seçin.
5. **Grafik türü** alanında "çubuk grafik" i seçin.
6. X ekseni sütun alanında "durum" ı seçin.
7. Y ekseni sütun alanında "ücret" i seçin.
8. **Toplama** alanında "Ort" seçeneğini belirleyin.
9. **Uygula**’yı seçin.

   ![Azure SYNAPSE Spark 'da grafik çıkışı](./media/quickstart-apache-spark-notebook/spark-get-started-query-chart-output.png "Azure SYNAPSE Spark 'da grafik çıkışı")

10. Aynı SQL çalışan, ancak dilleri değiştirmek zorunda kalmadan aynı deneyimi almak mümkündür. Bu işlemi, yukarıdaki SQL hücresini bu PySpark hücresiyle değiştirerek yapabilirsiniz, çünkü **görüntüleme** komutu kullanıldığı için çıkış deneyimi aynıdır:

    ```python
    display(spark.sql('SELECT * FROM demo_df'))
    ```

11. Daha önce yürütülen hücrelerden her birinin **Geçmiş sunucusuna** ve **izlemeye** gitme seçeneği vardı. Bağlantılara tıkladığınızda kullanıcı deneyiminin farklı bölümlerine gidersiniz.

> [!NOTE]
> [Apache Spark resmi belgelerden](https://spark.apache.org/docs/latest/) bazıları, SYNAPSE Spark 'Da kullanılamayan Spark konsolunun kullanılmasına bağımlıdır. Bunun yerine [Not defteri](quickstart-apache-spark-notebook.md) veya [IntelliJ](./spark/intellij-tool-synapse.md) deneyimlerini kullanın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure SYNAPSE, verilerinizi Azure Data Lake Storage kaydeder. Bir Spark örneğinin kullanımda olmadığı bir şekilde kapatılmasını güvenle sağlayabilirsiniz. Bir sunucusuz Apache Spark havuzu, kullanımda olmadığında bile çalıştığı sürece ücretlendirilirsiniz. 

Havuzun ücretleri depolama ücretinden çok daha fazla olduğundan, Spark örneklerinin kullanımda olmadıkları sırada kapatılmasını sağlamak için ekonomik bir fikir verir.

Spark örneğinin kapatıldığından emin olmak için, bağlı olan tüm oturumları (Not defterleri) sonlandırın. Apache Spark havuzunda belirtilen **boşta kalma süresine** ulaşıldığında havuz kapanır. Not defteri 'nin altındaki durum çubuğundan **oturumu bitir** ' i de seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, sunucusuz Apache Spark havuzu oluşturmayı ve temel Spark SQL sorgusunu çalıştırmayı öğrendiniz.

- [Azure Synapse Analytics](overview-what-is.md)
- [Apache Spark belgeleri için .NET](/dotnet/spark)



