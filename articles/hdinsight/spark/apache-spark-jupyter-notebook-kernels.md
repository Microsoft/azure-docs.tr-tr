---
title: Azure HDInsight 'ta Spark kümelerinde Jupyter Not defteri için kernels
description: Azure HDInsight 'ta Spark kümeleriyle kullanılabilen Jupyter Not defteri için pyspark, PySpark3 ve Spark çekirdekler hakkında bilgi edinin.
keywords: Spark 'ta Jupyter Not defteri, Jupyter Spark
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: 44089ea4b997e06cb7654fc6665a1a9a59ae2658
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494117"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Spark kümelerinde Jupyter Not defteri için kernels

HDInsight Spark kümeleri, uygulamalarınızı test etmek için [Apache Spark](https://spark.apache.org/) üzerinde Jupyter Not defteri ile kullanabileceğiniz çekirdekler 'leri sağlar. Çekirdek, kodunuzu çalıştıran ve yorumlayan bir programdır. Üç keresi şunlardır:

- **Pyspark** -Python2 dilinde yazılmış uygulamalar için.
- **PySpark3** -Python3 dilinde yazılmış uygulamalar için.
- **Spark** -Scala 'da yazılmış uygulamalar için.

Bu makalede, bu çekirdekleri kullanmayı ve bunların nasıl kullanıldığını öğrenirsiniz.

## <a name="prerequisites"></a>Önkoşullar

HDInsight 'ta bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Spark HDInsight 'ta Jupyter Not defteri oluşturma

1. [Azure Portal](https://portal.azure.com/)Spark kümenizi seçin.  Yönergeler için bkz. [liste ve kümeleri gösterme](../hdinsight-administer-use-portal-linux.md#showClusters) . **Genel bakış** görünümü açılır.

2. **Genel bakış** görünümünden **küme panoları** kutusunda **Jupyter Not defteri**' ni seçin. İstenirse, küme için yönetici kimlik bilgilerini girin.

    ![Apache Spark Jupyter Not defteri](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Spark üzerinde Jupyter Not defteri")
  
   > [!NOTE]  
   > Ayrıca, tarayıcınızda aşağıdaki URL 'YI açarak Spark kümesinde Jupyter Not defteri 'ne ulaşabilirsiniz. **CLUSTERNAME** değerini kümenizin adıyla değiştirin:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. **Yeni**' yi seçin ve ardından bir not defteri oluşturmak Için **pyspark**, **PySpark3**veya **Spark** ' ı seçin. Scala uygulamaları için Spark çekirdeğini, Python2 uygulamaları için PySpark çekirdeği ve Python3 uygulamaları için PySpark3 çekirdeğini kullanın.

    ![Spark 'ta Jupyter Not defteri için kernels](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Spark 'ta Jupyter Not defteri için kernels")

4. Seçtiğiniz çekirdekle bir not defteri açılır.

## <a name="benefits-of-using-the-kernels"></a>Çekirdekler kullanmanın avantajları

İşte, Spark HDInsight kümelerinde Jupyter Not defteri ile yeni çekirdekler 'ları kullanmanın bazı avantajları aşağıda verilmiştir.

- **Önceden ayarlanmış bağlamlar**. **Pyspark**, **PySpark3**veya **Spark** kernels Ile, uygulamalarınızla çalışmaya başlamadan önce Spark veya Hive bağlamlarını açıkça ayarlamanız gerekmez. Bunlar varsayılan olarak kullanılabilir. Bu bağlamlar şunlardır:

  - **sc** -Spark bağlamı için
  - **SqlContext** -Hive bağlamı için

    Bu nedenle, bağlamları ayarlamak için aşağıdaki gibi deyimler çalıştırmanız gerekmez:

         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)

    Bunun yerine, uygulamanızda önceden ayarlanmış bağlamlarını doğrudan kullanabilirsiniz.

- **Hücre Mıknatıresikleri**. PySpark çekirdeği, `%%` (örneğin, `%%MAGIC` `<args>`) çağırabilmeniz için özel komutlar olan önceden tanımlanmış bazı "mıknatıcs" sağlar. MAGIC komutu bir kod hücresindeki ilk sözcük olmalıdır ve birden çok içerik satırı için izin verir. Sihirli sözcük, hücredeki ilk sözcük olmalıdır. Sihirli, hatta yorumlarla önce herhangi bir şey eklemek hataya neden olur.     Mıknatıcs hakkında daha fazla bilgi için [buraya](https://ipython.readthedocs.org/en/stable/interactive/magics.html)bakın.

    Aşağıdaki tabloda kernels aracılığıyla kullanılabilen farklı mıknatıler listelenmiştir.

   | Magic | Örnek | Açıklama |
   | --- | --- | --- |
   | Yardım |`%%help` |Örnek ve açıklama ile tüm kullanılabilir mıknatıc 'nin bir tablosunu oluşturur |
   | Bilgisine |`%%info` |Geçerli Livy uç noktası için oturum bilgilerini çıkışlar |
   | Yapılandırma |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Oturum oluşturmak için parametreleri yapılandırır. Oturumun bırakılmış ve yeniden oluşturulmasını sağlayan bir oturum zaten oluşturulduysa zorla bayrağı (-f) zorunludur. Geçerli parametrelerin listesi için [Livy 'ın/Sessions Istek gövdesine](https://github.com/cloudera/livy#request-body) bakın. Parametrelerin, örnek sütununda gösterildiği gibi, bir JSON dizesi olarak geçirilmesi ve bir sonraki satırda olması gerekir. |
   | SQL |`%%sql -o <variable name>`<br> `SHOW TABLES` |SqlContext 'e karşı bir Hive sorgusu yürütür. `-o` parametresi geçirilirse, sorgunun sonucu%% yerel Python bağlamında [Pandas](https://pandas.pydata.org/) dataframe olarak kalıcı hale getirilir. |
   | Yerel |`%%local`<br>`a=1` |Sonraki satırlardaki tüm kodlar yerel olarak yürütülür. Kod, kullanmakta olduğunuz çekirdekten bağımsız olarak geçerli Python2 Code olmalıdır. Bu nedenle, Not defterini oluştururken **PySpark3** veya **Spark** çekirdekler ' ı seçmiş olsanız bile, bir hücrede `%%local` Magic kullanırsanız, bu hücrede yalnızca geçerli Python2 kodu olmalıdır. |
   | günlükler |`%%logs` |Geçerli Livy oturumunun günlüklerini çıkış. |
   | delete |`%%delete -f -s <session number>` |Geçerli Livy uç noktasının belirli bir oturumunu siler. Çekirdek için başlatılan oturumu silemezsiniz. |
   | temizle |`%%cleanup -f` |Bu not defteri 'nin oturumu dahil olmak üzere, geçerli Livy uç noktası için tüm oturumları siler. Zorla bayrağı-f zorunludur. |

   > [!NOTE]  
   > PySpark çekirdeği tarafından eklenen mıknatıb 'ye ek olarak, `%%sh`dahil [yerleşik IPython mıknatıı](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics)'leri de kullanabilirsiniz. Küme yayın düğümündeki betikleri ve kod bloğunu çalıştırmak için `%%sh` Magic ' i kullanabilirsiniz.

- **Otomatik görselleştirme**. Pyspark çekirdeği, Hive ve SQL sorgularının çıkışını otomatik olarak görselleştirir. Tablo, pasta, çizgi, alan, çubuk gibi birçok farklı görselleştirme türü arasından seçim yapabilirsiniz.

## <a name="parameters-supported-with-the-sql-magic"></a>%% SQL Magic ile desteklenen parametreler

`%%sql` Magic, sorguları çalıştırdığınızda aldığınız çıkışın türünü denetlemek için kullanabileceğiniz farklı parametreleri destekler. Aşağıdaki tabloda çıkış listelenmiştir.

| Parametre | Örnek | Açıklama |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Sorgunun sonucunu%% yerel Python bağlamında [Pandas](https://pandas.pydata.org/) dataframe olarak kalıcı hale getirmek için bu parametreyi kullanın. Dataframe değişkeninin adı belirttiğiniz değişken adıdır. |
| -q |`-q` |Hücrenin görselleştirmeleri devre dışı bırakmak için bunu kullanın. Bir hücrenin içeriğini görselleştirmeyi ve yalnızca bir veri çerçevesi olarak yakalamak istemiyorsanız, `-q -o <VARIABLE>`kullanın. Sonuçları yakalamadan görselleştirmeleri devre dışı bırakmak istiyorsanız (örneğin, bir SQL sorgusunu bir `CREATE TABLE` bildiri gibi), bir `-o` bağımsız değişkeni belirtmeden `-q` kullanın. |
| -a |`-m <METHOD>` |Burada **yöntemi** **Al** veya **Sample** (varsayılan olarak **Al**). Yöntemi **Al**ise, çekırdek, MaxRows tarafından belirtilen sonuç veri kümesinin üst kısmından (Bu tabloda daha sonra açıklanmıştır) öğeleri seçer. Yöntem **örnek**ise, çekirdek, bu tabloda daha sonra açıklanan `-r` parametresine göre veri kümesi öğelerini rasgele örnekler. |
| -r |`-r <FRACTION>` |Burada **kesir** , 0,0 ile 1,0 arasında bir kayan noktalı sayıdır. SQL sorgusunun örnek yöntemi `sample`ise, çekirdek, sonuç kümesi öğelerinin belirtilen kesirini sizin için rasgele şekilde örnekleyebilirsiniz. Örneğin, `-m sample -r 0.01`bağımsız değişkenlerle bir SQL sorgusu çalıştırırsanız, sonuç satırlarının %1 ' i rastgele örneklenir. |
| -n |`-n <MAXROWS>` |**MaxRows** bir tamsayı değeridir. Çekirdek, çıkış satırı sayısını **MaxRows**olarak sınırlandırır. **MaxRows** , **-1**gibi negatif bir sayı ise, sonuç kümesindeki satır sayısı sınırlı değildir. |

**Örnek:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

Yukarıdaki ifade şunları yapar:

- **Hivesampletable**içindeki tüm kayıtları seçer.
- -Q ' ı kullandığımızda, oto görselleştirmeyi devre dışı bırakır.
- `-m sample -r 0.1 -n 500` kullandığımızda, hivesampletable içindeki satırların %10 ' un rastgele örneklerinin yanı sıra sonuç kümesinin boyutunu 500 satır olarak sınırlandırır.
- Son olarak `-o query2` kullandığımızda, çıktıyı **query2**adlı bir veri çerçevesine de kaydeder.

## <a name="considerations-while-using-the-new-kernels"></a>Yeni çekirdekler kullanırken dikkat edilecek noktalar

Hangi çekirdeğin kullanıldığı, çalışan not defterlerini çalıştıran küme kaynakları tüketir.  Bu çekirdekler sayesinde, bağlamlar önceden ayarlanmış olduğundan, not defterlerinden çıkmak bağlamı sonlandırmaz ve bu nedenle küme kaynakları kullanımda olmaya devam eder. Not defteri 'ni kullanmayı bitirdiğinizde Not defteri 'nin **Dosya** menüsünden **Kapat ve Durdur** seçeneğini kullanmak iyi bir uygulamadır. Bu, bağlamı sonlandırır ve sonra Not defterinden çıkar.

## <a name="where-are-the-notebooks-stored"></a>Not defterleri nerede depolanır?

Kümeniz varsayılan depolama hesabı olarak Azure Storage kullanıyorsa, jupi Not defterleri **/HdiNotebooks** klasörünün altında depolama hesabına kaydedilir.  Jupi içinden oluşturduğunuz Not defterleri, metin dosyaları ve klasörlere depolama hesabından erişilebilir.  Örneğin, Jupyter kullanarak **bir klasör klasörü ve** **myFolder/mynot defteri. ipynb**oluşturmak için bu not defterine depolama hesabı içinde `/HdiNotebooks/myfolder/mynotebook.ipynb` erişebilirsiniz.  Tersi de geçerlidir; diğer bir deyişle, `/HdiNotebooks/mynotebook1.ipynb`bir not defterini doğrudan depolama hesabınıza yüklerseniz, Not defteri Jupyter 'dan da görünür.  Not defterleri, küme silindikten sonra bile depolama hesabında kalır.

> [!NOTE]  
> Varsayılan depolama alanı olarak Azure Data Lake Storage HDInsight kümeleri, not defterlerini ilişkili depolamada depolamaz.

Not defterlerini depolama hesabına kaydetme yöntemi [Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)Le uyumludur. Bu nedenle, kümeye SSH yaparsanız, aşağıdaki kod parçacığında gösterildiği gibi dosya yönetimi komutlarını kullanabilirsiniz:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Kümenin Azure Storage ' ı veya varsayılan depolama hesabı olarak Azure Data Lake Storage kullanıp kullanmadığını bağımsız olarak, Not defterleri de `/var/lib/jupyter`küme yayın düğümüne kaydedilir.

## <a name="supported-browser"></a>Desteklenen tarayıcı

Spark HDInsight kümelerinde jupyıter Not defterleri yalnızca Google Chrome 'da desteklenir.

## <a name="feedback"></a>Geri Bildirim

Yeni kerler gelişen aşamada ve zaman içinde yer alacak. Bu da API 'Lerin bu çekirdekler gibi değiştirebileceği anlamına da gelebilir. Bu yeni çekirdekleri kullanırken sahip olduğunuz tüm geri bildirimleri umuruz. Bu kernels 'lerin son sürümünü şekillendirmeye yardımcı olur. Açıklamalarınızı/geri bildiriminizi bu makalenin altındaki **geri bildirim** bölümünde bırakabilirsiniz.

## <a name="seealso"></a>Ayrıca bkz.

- [Genel Bakış: Azure HDInsight’ta Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>Senaryolar

- [BI ile Apache Spark: bı araçlarıyla HDInsight 'ta Spark kullanarak etkileşimli veri çözümlemesi gerçekleştirme](apache-spark-use-bi-tools.md)
- [Machine Learning ile Apache Spark: HVAC verilerini kullanarak oluşturma sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](apache-spark-ipython-notebook-machine-learning.md)
- [Machine Learning Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](apache-spark-machine-learning-mllib-ipython.md)
- [HDInsight 'ta Apache Spark kullanarak Web sitesi günlüğü Analizi](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Uygulamaları oluşturma ve çalıştırma

- [Scala kullanarak tek başına uygulama oluşturma](apache-spark-create-standalone-application.md)
- [Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Araçlar ve uzantılar

- [Spark Scala uygulamaları oluşturmak ve göndermek amacıyla IntelliJ IDEA için HDInsight Araçları Eklentisini kullanma](apache-spark-intellij-tool-plugin.md)
- [Apache Spark uygulamalarında uzaktan hata ayıklama için IntelliJ fıkır için HDInsight Araçları eklentisini kullanın](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
- [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
- [Jupyter not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
- [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Kaynakları yönetme

- [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
- [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)
