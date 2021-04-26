---
title: Azure HDInsight 'ta Apache Mahout kullanarak öneriler oluşturma
description: HDInsight ile film önerileri oluşturmak için Apache Mahout Machine Learning kitaplığını nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/14/2020
ms.openlocfilehash: 4c3484c1e65eb70be484a4a2eebda21a41dbf1ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936878"
---
# <a name="generate-recommendations-using-apache-mahout-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Mahout kullanarak öneriler oluşturma

Azure HDInsight ile [Apache Mahout](https://mahout.apache.org) Machine Learning kitaplığı 'nı kullanarak film önerileri oluşturma hakkında bilgi edinin.

Mahout, Apache Hadoop için bir [makine öğrenme](https://en.wikipedia.org/wiki/Machine_learning) kitaplığıdır. Mahout, filtreleme, sınıflandırma ve kümeleme gibi verileri işlemeye yönelik algoritmalar içerir. Bu makalede, arkadaşlarınızın gördük filmlerini temel alan film önerileri oluşturmak için bir öneri altyapısı kullanırsınız.

Mahout, HDInsight 3,6 ' de kullanılabilir ve HDInsight 4,0 ' de kullanılamaz. HDInsight 'ta Mahout sürümü hakkında daha fazla bilgi için bkz. [hdınsight 3,6 bileşen sürümleri](../hdinsight-36-component-versioning.md).

## <a name="prerequisites"></a>Önkoşullar

HDInsight üzerinde bir Apache Hadoop kümesi. Bkz. [Linux 'Ta HDInsight kullanmaya başlama](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="understanding-recommendations"></a>Önerileri anlama

Mahout tarafından sunulan işlevlerden biri bir öneri altyapısıdır. Bu altyapı verileri `userID` , `itemId` , ve biçiminde `prefValue` (öğe için tercih) kabul eder. Mahout daha sonra şunları tespit etmek için ortak yineleme Analizi gerçekleştirebilir: *bir öğe için bir tercihi olan kullanıcılar aynı zamanda bu diğer öğeler için bir tercihe sahiptir*. Mahout daha sonra, tercih etmek için kullanılabilecek, LIKE öğesi tercihleri olan kullanıcıları belirler.

Aşağıdaki iş akışı, film verileri kullanan Basitleştirilmiş bir örnektir:

* **Ortak yinelenme**: ali, Gamze ve Bob tüm beğenilen *yıldız çatışmaları*, *Empire geri* *dönerek JEDI 'nın geri dönmesi*. Mahout, bu filmlerde herhangi birini beğenen, diğer iki de gibi kullanıcıları belirler.

* **Ortak olay**: Bob ve Gamze, *hayalet Menace*, *klonların saldırıları* ve *Revenge 'in* de beğenilmiş. Mahout, önceki üç film de bu üç film gibi beğenilen kullanıcıları belirler.

* **Benzerlik önerisi**: ali, ilk üç film Beğentiğinden, Mahout, benzer tercihleri beğenen, ancak ali 'nin (beğenilmiş/derecelendirildi) diğer filmlere bakar. Bu durumda Mahout, *hayali Menace*, *klonların saldırıları* ve *SITH Revenge* için önerilir.

### <a name="understanding-the-data"></a>Verileri anlama

Kolay bir şekilde [Grouplens araştırması](https://grouplens.org/datasets/movielens/) , filmler Için Mahout ile uyumlu bir biçimde derecelendirme verileri sağlar. Bu veriler, kümenizin varsayılan depolama alanı üzerinde bulunur `/HdiSamples/HdiSamples/MahoutMovieData` .

İki dosya vardır `moviedb.txt` `user-ratings.txt` . `user-ratings.txt`Dosya analiz sırasında kullanılır. , `moviedb.txt` Sonuçları görüntülerken Kullanıcı dostu metin bilgileri sağlamak için kullanılır.

İçinde bulunan veriler `user-ratings.txt` `userID` , `movieID` `userRating` `timestamp` her kullanıcının bir filmi derecelendirdiğini belirten,, ve yapısına sahiptir. Verilerin bir örneği aşağıda verilmiştir:

```output
    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596
```

## <a name="run-the-analysis"></a>Analizi Çalıştırma

1. Kümenize bağlanmak için [SSH komutunu](../hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. CLUSTERNAME öğesini kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Öneri işini çalıştırmak için aşağıdaki komutu kullanın:

    ```bash
    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
    ```

> [!NOTE]  
> İşin tamamlanması birkaç dakika sürebilir ve birden çok MapReduce işi çalıştırılabilir.

## <a name="view-the-output"></a>Çıktıyı görüntüleme

1. İş tamamlandıktan sonra, oluşturulan çıktıyı görüntülemek için aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    Çıktı aşağıdaki gibi görünür:

    ```output
    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
    ```

    İlk sütun `userID` . ' [' Ve '] ' içinde yer alan değerler `movieId` : `recommendationScore` .

2. Öneriler hakkında daha fazla bilgi sağlamak için çıktıyı moviedb.txt birlikte kullanabilirsiniz. İlk olarak, aşağıdaki komutları kullanarak dosyaları yerel olarak kopyalayın:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Bu komut, çıktı verilerini, film veri dosyalarıyla birlikte geçerli dizindeki **recommendations.txt** adlı bir dosyaya kopyalar.

3. Öneri çıktısındaki verilerin film adlarını gösteren bir Python betiği oluşturmak için aşağıdaki komutu kullanın:

    ```bash
    nano show_recommendations.py
    ```

    Düzenleyici açıldığında, dosyanın içeriği olarak aşağıdaki metni kullanın:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Verileri kaydetmek için **CTRL + X**, **Y** ve son olarak **ENTER** tuşlarına basın.

4. Python betiğini çalıştırın. Aşağıdaki komut, tüm dosyaların indirileceği dizinde olduğunuzu varsayar:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Bu komut, Kullanıcı KIMLIĞI 4 için oluşturulan önerilere bakar.

   * **user-ratings.txt** dosyası, derecelendirildi filmleri almak için kullanılır.

   * **moviedb.txt** dosyası, filmlerin adlarını almak için kullanılır.

   * **recommendations.txt** , bu kullanıcıya ait film önerilerini almak için kullanılır.

     Bu komutun çıktısı aşağıdaki metne benzer:

        ```output
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        ```

## <a name="delete-temporary-data"></a>Geçici verileri Sil

Mahout işleri, iş işlenirken oluşturulan geçici verileri kaldırmaz. Bu `--tempDir` parametre, geçici dosyaları kolay bir şekilde silinmek üzere belirli bir yola yalıtmak için örnek işte belirtilir. Geçici dosyaları kaldırmak için aşağıdaki komutu kullanın:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Komutu yeniden çalıştırmak istiyorsanız, çıkış dizinini de silmelisiniz. Bu dizini silmek için aşağıdakileri kullanın:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`

## <a name="next-steps"></a>Sonraki adımlar

Mahout 'u nasıl kullanacağınızı öğrendiğinize göre, HDInsight 'ta verilerle çalışmanın diğer yollarını buldığınıza göre:

* [HDInsight ile Apache Hive](hdinsight-use-hive.md)
* [HDInsight ile MapReduce](hdinsight-use-mapreduce.md)
