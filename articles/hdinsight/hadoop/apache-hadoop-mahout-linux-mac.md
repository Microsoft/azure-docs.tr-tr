---
title: Azure HDInsight 'ta Apache Mahout kullanarak öneriler oluşturma
description: HDInsight (Hadoop) ile film önerileri oluşturmak için Apache Mahout Machine Learning kitaplığını nasıl kullanacağınızı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 3923abd10fc3a64773d561b1f375f9e2f00a7e56
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044558"
---
# <a name="generate-movie-recommendations-using-apache-mahout-with-apache-hadoop-in-hdinsight-ssh"></a>HDInsight 'ta Apache Hadoop ile Apache Mahout kullanarak film önerileri oluşturma (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Azure HDInsight ile [Apache Mahout](https://mahout.apache.org) Machine Learning kitaplığı 'nı kullanarak film önerileri oluşturma hakkında bilgi edinin.

Mahout, Apache Hadoop için bir [makine öğrenme](https://en.wikipedia.org/wiki/Machine_learning) kitaplığıdır. Mahout, filtreleme, sınıflandırma ve kümeleme gibi verileri işlemeye yönelik algoritmalar içerir. Bu makalede, arkadaşlarınızın gördük filmlerini temel alan film önerileri oluşturmak için bir öneri altyapısı kullanırsınız.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Hadoop kümesi. Bkz. [Linux 'Ta HDInsight kullanmaya başlama](./apache-hadoop-linux-tutorial-get-started.md).

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight 'A bağlanma (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="apache-mahout-versioning"></a>Apache Mahout sürümü oluşturma

HDInsight 'ta Mahout sürümü hakkında daha fazla bilgi için bkz. [HDInsight sürümleri ve Apache Hadoop bileşenleri](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Önerileri anlama

Mahout tarafından sunulan işlevlerden biri bir öneri altyapısıdır. Bu altyapı, `userID`, `itemId`ve `prefValue` (öğe için tercih) biçimindeki verileri kabul eder. Mahout daha sonra şunları tespit etmek için ortak yineleme Analizi gerçekleştirebilir: *bir öğe için bir tercihi olan kullanıcılar aynı zamanda bu diğer öğeler için bir tercihe sahiptir*. Mahout daha sonra, tercih etmek için kullanılabilecek, LIKE öğesi tercihleri olan kullanıcıları belirler.

Aşağıdaki iş akışı, film verileri kullanan Basitleştirilmiş bir örnektir:

* **Ortak yinelenme**: ali, Gamze ve Bob tüm beğenilen *yıldız çatışmaları*, *Empire geri* *dönerek JEDI 'nın geri dönmesi*. Mahout, bu filmlerde herhangi birini beğenen, diğer iki de gibi kullanıcıları belirler.

* **Ortak olay**: Bob ve Gamze, *hayalet Menace*, *klonların saldırıları*ve *Revenge 'in*de beğenilmiş. Mahout, önceki üç film de bu üç film gibi beğenilen kullanıcıları belirler.

* **Benzerlik önerisi**: ali, ilk üç film Beğentiğinden, Mahout, benzer tercihleri beğenen, ancak ali 'nin (beğenilmiş/derecelendirmediği) diğer filmlere bakar. Bu durumda Mahout, *hayali Menace*, *klonların saldırıları*ve *SITH Revenge*için önerilir.

### <a name="understanding-the-data"></a>Verileri anlama

Kolay bir şekilde [Grouplens araştırması](https://grouplens.org/datasets/movielens/) , filmler Için Mahout ile uyumlu bir biçimde derecelendirme verileri sağlar. Bu veriler, kümenizin varsayılan depolama alanı `/HdiSamples/HdiSamples/MahoutMovieData`' de bulunur.

`moviedb.txt` ve `user-ratings.txt`iki dosya vardır. `user-ratings.txt` dosyası analiz sırasında kullanılır. `moviedb.txt`, sonuçları görüntülerken Kullanıcı dostu metin bilgilerini sağlamak için kullanılır.

User-Ratings. txt ' de yer alan veriler `userID`, `movieID`, `userRating`ve `timestamp`bir yapısına sahiptir ve her kullanıcının bir filmi derecelendirdiğini gösterir. Verilerin bir örneği aşağıda verilmiştir:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Analizi Çalıştırma

Kümeye bir SSH bağlantısından, öneri işini çalıştırmak için aşağıdaki komutu kullanın:

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

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    İlk sütun `userID`. ' [' Ve '] ' içinde yer alan değerler `movieId`:`recommendationScore`.

2. Öneriler hakkında daha fazla bilgi sağlamak için, çıktıyı moviedb. txt ile birlikte kullanabilirsiniz. İlk olarak, aşağıdaki komutları kullanarak dosyaları yerel olarak kopyalayın:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Bu komut, çıktı verilerini geçerli dizinde, film veri dosyalarıyla birlikte **öneriler. txt** adlı bir dosyaya kopyalar.

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

    Verileri kaydetmek için **CTRL + X**, **Y**ve son olarak **ENTER** tuşlarına basın.

4. Python betiğini çalıştırın. Aşağıdaki komut, tüm dosyaların indirileceği dizinde olduğunuzu varsayar:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Bu komut, Kullanıcı KIMLIĞI 4 için oluşturulan önerilere bakar.

   * **User-Ratings. txt** dosyası, derecelendirildi filmleri almak için kullanılır.

   * **Moviedb. txt** dosyası, filmlerin adlarını almak için kullanılır.

   * **Öneriler. txt** , bu kullanıcıya ait film önerilerini almak için kullanılır.

     Bu komutun çıktısı aşağıdaki metne benzer:

       Tibet içinde yedi yıl (1997), Score = 5.0 Indiana Jones ve son Kırade (1989), Score = 5.0 JAWS (1975), Score = 5.0 Sense ve Senbilimi (1995), Score = 5.0 bağımsızlık günü (ıD4) (1996), Score = 5.0 En Iyi arkadaşınızın evlilik (1997), Score = 5.0 Jraz Maguire (1996), Score = 5.0 Skre2 (1997), Score = 5.0 sonlandırma süresi, A (1996), Score = 5.0

## <a name="delete-temporary-data"></a>Geçici verileri Sil

Mahout işleri, iş işlenirken oluşturulan geçici verileri kaldırmaz. `--tempDir` parametresi, geçici dosyaları kolay bir şekilde silinmek üzere belirli bir yola yalıtmak için örnek işte belirtilir. Geçici dosyaları kaldırmak için aşağıdaki komutu kullanın:

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
* [HDInsight ile Apache Pig](hdinsight-use-pig.md)
* [HDInsight ile MapReduce](hdinsight-use-mapreduce.md)