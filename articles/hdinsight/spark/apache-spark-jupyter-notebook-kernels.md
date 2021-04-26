---
title: Azure HDInsight 'ta Spark kümelerinde Jupyter Notebook kernels
description: Azure HDInsight 'ta Spark kümeleri ile kullanılabilen Jupyter Notebook pyspark, PySpark3 ve Spark çekirdekler hakkında bilgi edinin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: ef2bc5e00779200e5447c8829a437824657a2227
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865987"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Spark kümelerinde Jupyter Notebook kernels

HDInsight Spark kümeleri, uygulamalarınızı test etmek için [Apache Spark](./apache-spark-overview.md) Jupyter Notebook birlikte kullanabileceğiniz çekirdekler 'leri sağlar. Çekirdek, kodunuzu çalıştıran ve yorumlayan bir programdır. Üç keresi şunlardır:

- **Pyspark** -Python2 dilinde yazılmış uygulamalar için.
- **PySpark3** -Python3 dilinde yazılmış uygulamalar için.
- **Spark** -Scala 'da yazılmış uygulamalar için.

Bu makalede, bu çekirdekleri kullanmayı ve bunların nasıl kullanıldığını öğrenirsiniz.

## <a name="prerequisites"></a>Önkoşullar

HDInsight 'ta bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Spark HDInsight üzerinde Jupyter Notebook oluşturma

1. [Azure Portal](https://portal.azure.com/)Spark kümenizi seçin.  Yönergeler için bkz. [liste ve kümeleri gösterme](../hdinsight-administer-use-portal-linux.md#showClusters) . **Genel bakış** görünümü açılır.

2. **Genel bakış** görünümünden **küme panoları** kutusunda **Jupyter Notebook**' yi seçin. İstenirse, küme için yönetici kimlik bilgilerini girin.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png " alt-text="Apache Spark Jupyter Notebook" border="true":::
  
   > [!NOTE]  
   > Ayrıca, tarayıcınızda aşağıdaki URL 'YI açarak Spark kümesindeki Jupyter Notebook de ulaşabilirsiniz. **CLUSTERNAME** değerini kümenizin adıyla değiştirin:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. **Yeni**' yi seçin ve ardından bir not defteri oluşturmak Için **pyspark**, **PySpark3** veya **Spark** ' ı seçin. Scala uygulamaları için Spark çekirdeğini, Python2 uygulamaları için PySpark çekirdeği ve Python3 uygulamaları için PySpark3 çekirdeğini kullanın.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png " alt-text="Spark üzerinde Jupyter Notebook için kernels" border="true":::

4. Seçtiğiniz çekirdekle bir not defteri açılır.

## <a name="benefits-of-using-the-kernels"></a>Çekirdekler kullanmanın avantajları

Spark HDInsight kümelerinde Jupyter Notebook yeni çekirdekler kullanmanın bazı avantajları aşağıda verilmiştir.

- **Önceden ayarlanmış bağlamlar**. **Pyspark**, **PySpark3** veya **Spark** kernels Ile, uygulamalarınızla çalışmaya başlamadan önce Spark veya Hive bağlamlarını açıkça ayarlamanız gerekmez. Bu bağlamlar varsayılan olarak kullanılabilir. Bu bağlamlar şunlardır:

  - **sc** -Spark bağlamı için
  - **SqlContext** -Hive bağlamı için

    Bu nedenle, bağlamları ayarlamak için aşağıdaki gibi deyimler **çalıştırmanız gerekmez:**

    ```sql
    sc = SparkContext('yarn-client')
    sqlContext = HiveContext(sc)
    ```

    Bunun yerine, uygulamanızda önceden ayarlanmış bağlamlarını doğrudan kullanabilirsiniz.

- **Hücre Mıknatıresikleri**. PySpark çekirdeği, ile çağırabilmeniz için özel komutlar olan önceden tanımlanmış bazı "mıknatıcs" sağlar `%%` (örneğin, `%%MAGIC` `<args>` ). MAGIC komutu bir kod hücresindeki ilk sözcük olmalıdır ve birden çok içerik satırı için izin verir. Sihirli sözcük, hücredeki ilk sözcük olmalıdır. Sihirli, hatta yorumlarla önce herhangi bir şey eklemek hataya neden olur.     Mıknatıcs hakkında daha fazla bilgi için [buraya](https://ipython.readthedocs.org/en/stable/interactive/magics.html)bakın.

    Aşağıdaki tabloda kernels aracılığıyla kullanılabilen farklı mıknatıler listelenmiştir.

   | Magic | Örnek | Description |
   | --- | --- | --- |
   | Yardım |`%%help` |Örnek ve açıklama ile tüm kullanılabilir mıknatıc 'nin bir tablosunu oluşturur |
   | bilgiler |`%%info` |Geçerli Livy uç noktası için oturum bilgilerini çıkışlar |
   | yapılandırma |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Oturum oluşturmak için parametreleri yapılandırır. Oturumun `-f` bırakılmış ve yeniden oluşturulmasını sağlayan bir oturum zaten oluşturulmuşsa zorla bayrağı () zorunludur. Geçerli parametrelerin listesi için [Livy 'ın/Sessions Istek gövdesine](https://github.com/cloudera/livy#request-body) bakın. Parametrelerin, örnek sütununda gösterildiği gibi, bir JSON dizesi olarak geçirilmesi ve bir sonraki satırda olması gerekir. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |SqlContext 'e karşı bir Hive sorgusu yürütür. `-o`Parametresi geçirilirse, sorgunun sonucu%% yerel Python bağlamında [Pandas](https://pandas.pydata.org/) dataframe olarak kalıcı hale getirilir. |
   | yerel |`%%local`<br>`a=1` |Sonraki satırlardaki tüm kodlar yerel olarak yürütülür. Kod, hangi çekirdekten bağımsız olarak geçerli Python2 kodu olmalıdır. Bu nedenle, Not defterini oluştururken **PySpark3** veya **Spark** çekirdekler ' ı seçmiş olsanız bile, `%%local` Magic 'i bir hücrede kullanırsanız, o hücre yalnızca geçerli Python2 koduna sahip olmalıdır. |
   | günlükler |`%%logs` |Geçerli Livy oturumunun günlüklerini çıkış. |
   | delete |`%%delete -f -s <session number>` |Geçerli Livy uç noktasının belirli bir oturumunu siler. Çekirdek için başlatılan oturumu silemezsiniz. |
   | temizle |`%%cleanup -f` |Bu not defteri 'nin oturumu dahil olmak üzere, geçerli Livy uç noktası için tüm oturumları siler. Zorla bayrağı-f zorunludur. |

   > [!NOTE]  
   > PySpark çekirdeği tarafından eklenen mıknatısa 'ya ek olarak, dahil [yerleşik IPython mıknatıcs](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics)de kullanabilirsiniz `%%sh` . `%%sh`Küme yayın düğümündeki betikleri ve kod bloğunu çalıştırmak için Magic ' i kullanabilirsiniz.

- **Otomatik görselleştirme**. Pyspark çekirdeği, Hive ve SQL sorgularının çıkışını otomatik olarak görselleştirir. Tablo, pasta, çizgi, alan, çubuk gibi birçok farklı görselleştirme türü arasından seçim yapabilirsiniz.

## <a name="parameters-supported-with-the-sql-magic"></a>%% SQL Magic ile desteklenen parametreler

`%%sql`Magic, sorguları çalıştırdığınızda aldığınız çıkışın türünü denetlemek için kullanabileceğiniz farklı parametreleri destekler. Aşağıdaki tabloda çıkış listelenmiştir.

| Parametre | Örnek | Description |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Sorgunun sonucunu%% yerel Python bağlamında [Pandas](https://pandas.pydata.org/) dataframe olarak kalıcı hale getirmek için bu parametreyi kullanın. Dataframe değişkeninin adı belirttiğiniz değişken adıdır. |
| -q |`-q` |Hücrenin görselleştirmeleri devre dışı bırakmak için bu parametreyi kullanın. Bir hücrenin içeriğini görselleştirmeyi ve yalnızca bir veri çerçevesi olarak yakalamak istemiyorsanız, öğesini kullanın `-q -o <VARIABLE>` . Sonuçları yakalamadan görselleştirmeleri devre dışı bırakmak istiyorsanız (örneğin, bir SQL sorgusunu bir ifade gibi çalıştırmak için `CREATE TABLE` ), `-q` bir `-o` bağımsız değişken belirtmeden kullanın. |
| -a |`-m <METHOD>` |Burada **yöntemi** **Al** veya **Sample** (varsayılan olarak **Al**). Yöntemi ise **`take`** , çekirdek MAXROWS tarafından belirtilen sonuç veri kümesinin üst kısmından (Bu tabloda daha sonra açıklanmıştır) öğeleri seçer. Yöntem **örnek** ise, çekirdek, `-r` Bu tablonun yanında açıklanan parametreye göre veri kümesi öğelerini rasgele örnekler. |
| -r |`-r <FRACTION>` |Burada **kesir** , 0,0 ile 1,0 arasında bir kayan noktalı sayıdır. SQL sorgusu için örnek yöntem ise `sample` , çekirdek, sizin için sonuç kümesi öğelerinin belirtilen kesirini rastgele olarak örnekler. Örneğin, bağımsız değişkenlerle bir SQL sorgusu çalıştırırsanız `-m sample -r 0.01` , sonuç satırlarının %1 ' i rastgele örneklenir. |
| -n |`-n <MAXROWS>` |**MaxRows** bir tamsayı değeridir. Çekirdek, çıkış satırı sayısını **MaxRows** olarak sınırlandırır. **MaxRows** , **-1** gibi negatif bir sayı ise, sonuç kümesindeki satır sayısı sınırlı değildir. |

**Örnek:**

```sql
%%sql -q -m sample -r 0.1 -n 500 -o query2
SELECT * FROM hivesampletable
```

Yukarıdaki ifade aşağıdaki eylemleri yapar:

- **Hivesampletable** içindeki tüm kayıtları seçer.
- -Q ' ı kullandığımızda, oto görselleştirmeyi devre dışı bırakır.
- Kullandığımızda `-m sample -r 0.1 -n 500` , hivesampletable içindeki satırların %10 ' u rastgele örnekliyoruz ve sonuç kümesinin boyutunu 500 satır olarak sınırlandırır.
- Son olarak, bunu kullandığımızda `-o query2` çıktıyı **query2** adlı bir veri çerçevesine de kaydeder.

## <a name="considerations-while-using-the-new-kernels"></a>Yeni çekirdekler kullanırken dikkat edilecek noktalar

Hangi çekirdeğin kullanıldığı, çalışan not defterlerini çalıştıran küme kaynakları tüketir.  Bu çekirdekler sayesinde, bağlamlar önceden ayarlı olduğundan, not defterlerinden çıkmak bağlamı sonlandırmaz. Bu nedenle, küme kaynakları kullanımda olmaya devam eder. Not defterini kullanmayı bitirdiğinizde Not defteri 'nin **Dosya** menüsünden **Kapat ve Durdur** seçeneğini kullanmak iyi bir uygulamadır. Kapanış bağlamını sonlandırır ve sonra Not defterinden çıkar.

## <a name="where-are-the-notebooks-stored"></a>Not defterleri nerede depolanır?

Kümeniz varsayılan depolama hesabı olarak Azure Storage kullanıyorsa, jupi Not defterleri **/HdiNotebooks** klasörünün altında depolama hesabına kaydedilir.  Jupi içinden oluşturduğunuz Not defterleri, metin dosyaları ve klasörlere depolama hesabından erişilebilir.  Örneğin, Jupyter kullanarak bir klasör **`myfolder`** ve **myFolder/mynot defteri. ipynb** oluşturmak için bu not defterine `/HdiNotebooks/myfolder/mynotebook.ipynb` depolama hesabı dahilinde erişebilirsiniz.  Tersi de geçerlidir, yani bir not defterini doğrudan depolama hesabınıza yüklerseniz `/HdiNotebooks/mynotebook1.ipynb` , Not defteri Jupyter 'dan da görünür.  Not defterleri, küme silindikten sonra bile depolama hesabında kalır.

> [!NOTE]  
> Varsayılan depolama alanı olarak Azure Data Lake Storage HDInsight kümeleri, not defterlerini ilişkili depolamada depolamaz.

Not defterlerini depolama hesabına kaydetme yöntemi [Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)Le uyumludur. Kümeye SSH oluşturursanız dosya yönetimi komutlarını kullanabilirsiniz:

| Komut | Açıklama |
|---------|-------------|
| `hdfs dfs -ls /HdiNotebooks` | # Kök dizindeki her şeyi listeleyin: Bu dizindeki her şey giriş sayfasından jupi 'ye görünür |
| `hdfs dfs –copyToLocal /HdiNotebooks` | # HdiNotebooks klasörünün içeriğini indirin|
| `hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks` | # Bir not defteri örneği yükleyin. ipynb, Jupyter 'dan görünmesi için kök klasöre |

Kümenin Azure Storage 'ı veya Azure Data Lake Storage varsayılan depolama hesabı olarak kullanıp kullanmadığını, Not defterleri de küme baş düğümüne üzerinde de kaydedilir `/var/lib/jupyter` .

## <a name="supported-browser"></a>Desteklenen tarayıcı

Spark HDInsight kümelerinde jupyıter Not defterleri yalnızca Google Chrome 'da desteklenir.

## <a name="suggestions"></a>Öneriler

Yeni kerler gelişen aşamada ve zaman içinde yer alacak. Bu nedenle, API 'Ler bu çekirdekler gibi değişebilir. Bu yeni çekirdekleri kullanırken sahip olduğunuz tüm geri bildirimleri umuruz. Geri bildirim, bu kernels 'in son sürümünü şekillendirmeye yardımcı olur. Açıklamalarınızı/geri bildiriminizi bu makalenin altındaki **geri bildirim** bölümünde bırakabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Genel Bakış: Azure HDInsight’ta Apache Spark](apache-spark-overview.md)
- [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
- [Jupyıter Not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
- [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)
